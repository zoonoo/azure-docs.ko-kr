---
title: "SQL Server 가용성 그룹 수신기 만들기 - Azure Virtual Machines | Microsoft Docs"
description: "Azure Virtual Machines의 SQL Server에 대한 Always On 가용성 그룹용 수신기를 만드는 단계별 지침"
services: virtual-machines
documentationcenter: na
author: MikeRayMSFT
manager: jhubbard
editor: monicar
ms.assetid: d1f291e9-9af2-41ba-9d29-9541e3adcfcf
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 12/28/2016
ms.author: mikeray
translationtype: Human Translation
ms.sourcegitcommit: 407b189af12116d633ed505facf4bcfde9be5822
ms.openlocfilehash: 6a37e9e786a4e399c49cb77758a23793790888c9


---
# <a name="configure-an-internal-load-balancer-for-an-always-on-availability-group-in-azure"></a>Azure에서 Always On 가용성 그룹에 대한 내부 부하 분산 장치 구성
이 항목에서는 리소스 관리자 모델에서 실행 중인 Azure 가상 컴퓨터에서 SQL Server Always On 가용성 그룹에 대한 내부 부하 분산 장치를 만드는 방법을 설명합니다. SQL Server 인스턴스가 Azure 가상 컴퓨터에 있는 경우 가용성 그룹을 사용하려면 부하 분산 장치가 필요합니다. 부하 분산 장치는 가용성 그룹 수신기의 IP 주소를 저장합니다. 가용성 그룹이 여러 지역에 분산된 경우 각 지역에 부하 분산 장치가 있어야 합니다.

이 작업을 완료하려면 Resource Manager 모델의 Azure 가상 컴퓨터에 SQL Server 가용성 그룹이 배포되어야 합니다. 두 SQL Server 가상 컴퓨터는 동일한 가용성 집합에 속해야 합니다. [Microsoft 템플릿](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) 을 사용하여 Azure Resource Manager에서 가용성 그룹을 자동으로 만들 수 있습니다. 이 템플릿은 내부 부하 분산 장치를 자동으로 만듭니다. 

원하는 경우 [수동으로 가용성 그룹을 구성](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md)할 수 있습니다.

이 항목을 수행하려면 가용성 그룹이 이미 구성되어 있어야 합니다.  

관련 항목은 다음과 같습니다.

* [Azure VM의 Always On 가용성 그룹 구성(GUI)](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md)   
* [Azure 리소스 관리자 및 PowerShell을 사용하여 VNet-VNet 연결 구성](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

이 문서를 통해 Azure Portal에서 부하 분산 장치를 만들고 구성합니다. 완료 후에는 가용성 그룹 수신기에 대한 부하 분산 장치에서 IP 주소를 사용하도록 클러스터를 구성합니다.

## <a name="create-and-configure-the-load-balancer-in-the-azure-portal"></a>Azure 포털에서 부하 분산 장치 만들기 및 구성
작업의 이 부분에서는 Azure Portal에서 다음 단계를 수행합니다.

1. 부하 분산 장치 만들기 및 IP 주소 구성
2. 백 엔드 풀 구성
3. 프로브 만들기 
4. 부하 분산 규칙 설정

> [!NOTE]
> SQL Server가 다른 리소스 그룹 및 지역에 있는 경우 이 모든 단계를 각 리소스 그룹마다 한 번씩 두 번 수행합니다.
> 
> 

### <a name="1-create-the-load-balancer-and-configure-the-ip-address"></a>1. 부하 분산 장치 만들기 및 IP 주소 구성
첫 번째 단계는 부하 분산 장치를 만드는 것입니다. Azure 포털에서 SQL Server 가상 컴퓨터를 포함하는 리소스 그룹을 엽니다. 리소스 그룹에서 **추가**를 클릭합니다.

1. **부하 분산 장치**를 검색합니다. 검색 결과에서 **Microsoft**에서 게시하는 **부하 분산 장치**를 선택합니다.
1. **부하 분산 장치** 블레이드에서 **만들기**를 클릭합니다.
1. **부하 분산 장치 만들기**에서 다음과 같이 부하 분산 장치를 구성합니다.

   | 설정 | 값 |
   | --- | --- |
   | **Name** |부하 분산 장치를 나타내는 텍스트 이름입니다. 예를 들어 **sqlLB**입니다. |
   | **형식** |**내부** |
   | **가상 네트워크** |SQL Server가 있는 가상 네트워크를 선택합니다. |
   | **서브넷** |SQL Server가 있는 서브넷을 선택합니다. |
   | **IP 주소 할당** |**정적** |
   | **개인 IP 주소** |서브넷에서 사용 가능한 IP 주소를 지정합니다. 클러스터에서 수신기를 만들 때 이 IP 주소를 사용합니다. 이 주소는 이 문서 뒷부분의 PowerShell 스크립트에서 `$ILBIP` 변수에 대해 사용됩니다. |
   | **구독** |구독이 여러 개인 경우 이 필드가 나타날 수 있습니다. 이 리소스와 연결할 구독을 선택합니다. 일반적으로 가용성 그룹에 대한 모든 리소스와 동일한 구독입니다. |
   | **리소스 그룹** |SQL Server가 있는 리소스 그룹을 선택합니다. |
   | **위치** |SQL Server가 있는 Azure 위치를 선택합니다. |

1. **만들기**를 클릭합니다. 

Azure에서 부하 분산 장치를 만듭니다. 부하 분산 장치는 특정 네트워크, 서브넷, 리소스 그룹 및 위치에 속합니다. 완료되면 Azure에서 부하 분산 장치 설정을 확인합니다. 

### <a name="2-configure-the-backend-pool"></a>2. 백 엔드 풀 구성
다음 단계는 백 엔드 주소 풀을 만드는 것입니다. 백 엔드 주소 풀 *백 엔드 풀*이 호출됩니다. 이 경우 백 엔드 풀은 가용성 그룹에 있는 두 SQL Server의 주소입니다. 

1. 리소스 그룹에서, 만든 부하 분산 장치를 클릭합니다. 
1. **설정**에서 **백 엔드 풀**을 클릭합니다.
1. **백 엔드 풀**에서 **추가**를 클릭하여 백 엔드 주소 풀을 만듭니다. 
1. **백 엔드 풀 추가** under **Name**에 백 엔드 풀의 이름을 입력합니다.
1. **가상 컴퓨터**에서 **+ 가상 컴퓨터 추가**를 클릭합니다. 
1. **가상 컴퓨터 선택**에서 **가용성 집합 선택**을 클릭하고 SQL Server 가상 컴퓨터가 속한 가용성 집합을 지정합니다.
1. 가용성 집합을 선택한 후 **가상 컴퓨터 선택**을 클릭합니다. 가용성 그룹에서 SQL Server 인스턴스를 호스트하는 두 가상 컴퓨터를 클릭합니다. **선택**을 클릭합니다. 
1. **확인**을 클릭하여 **가상 컴퓨터 선택** 및 **백 엔드 풀 추가**에 대한 블레이드를 닫습니다. 

백 엔드 주소 풀에 대한 설정이 업데이트됩니다. 이제 가용성 집합에는 두 개의 SQL Server 풀이 있습니다.

### <a name="3-create-a-probe"></a>3. 프로브 만들기
다음 단계는 프로브를 만드는 것입니다. 프로브는 Azure에서 현재 가용성 그룹 수신기를 소유하는 SQL Server를 확인하는 방법을 정의합니다. Azure는 프로브를 만들 때 정의한 포트의 IP 주소를 기반으로 서비스를 프로브합니다.

1. 부하 분산 장치 **설정** 블레이드에서 **상태 프로브**를 클릭합니다. 
1. **상태 프로브** 블레이드에서 **추가**를 클릭합니다.
1. **프로브 추가** 블레이드에서 프로브를 구성합니다. 다음 값을 사용하여 프로브를 구성합니다.

   | 설정 | 값 |
   | --- | --- |
   | **Name** |프로브를 나타내는 텍스트 이름입니다. 예를 들어 **SQLAlwaysOnEndPointProbe**입니다. |
   | **프로토콜** |**TCP** |
   | **포트** |사용 가능한 모든 포트를 사용할 수 있습니다. 예를 들어 *59999*입니다. |
   | **간격** |*5* |
   | **비정상 임계값** |*2* |

1.  **확인**을 클릭합니다. 

> [!NOTE]
> 지정한 포트가 두 SQL Server의 방화벽에서 열려 있는지 확인합니다. 두 서버 모두 사용하는 TCP 포트에 대한 인바운드 규칙이 필요합니다. 자세한 내용은 [방화벽 규칙 추가 또는 편집](http://technet.microsoft.com/library/cc753558.aspx)을 참조하세요. 
> 
> 

프로브가 만들어집니다. Azure는 가용성 그룹에 대한 수신기가 있는 SQL Server를 테스트하는 데 프로브를 사용합니다.

### <a name="4-set-the-load-balancing-rules"></a>4. 부하 분산 규칙 설정
부하 분산 규칙을 설정합니다. 부하 분산 규칙은 부하 분산 장치가 트래픽을 SQL Server로 라우트하는 방법을 구성합니다. 이 부하 분산 장치의 경우 한 번에 두 SQL Server 중 하나만 가용성 그룹 수신기 리소스를 소유하므로 DSR(Direct Server Return)이 사용됩니다.

1. 부하 분산 장치 **설정** 블레이드에서 **부하 분산 규칙**을 클릭합니다. 
1. **부하 분산 규칙** 블레이드에서 **추가**를 클릭합니다.
1. **부하 분산 규칙 추가** 블레이드를 사용하여 부하 분산 규칙을 구성합니다. 다음 설정을 사용합니다. 

   | 설정 | 값 |
   | --- | --- |
   | **Name** |부하 분산 규칙을 나타내는 텍스트 이름입니다. 예를 들어 **SQLAlwaysOnEndPointListener**입니다. |
   | **프로토콜** |**TCP** |
   | **포트** |*1433* |
   | **백 엔드 포트** |*1433*. 이 규칙은 **부동 IP(Direct Server Return)**를 사용하므로 이 값은 무시됩니다. |
   | **프로브** |이 부하 분산 장치에 대해 만든 프로브의 이름을 사용합니다. |
   | **세션 지속성** |**없음** |
   | **유휴 제한 시간(분)** |*4* |
   | **부동 IP(Direct Server Return)** |**사용** |

   > [!NOTE]
   > 블레이드에서 모든 설정을 보려면 아래로 스크롤해야 할 수도 있습니다.
   > 

1. **확인**을 클릭합니다. 
1. 부하 분산 규칙이 구성됩니다. 이제 가용성 그룹에 대한 수신기를 호스트하는 SQL Server로 트래픽을 라우트하도록 부하 분산 장치가 구성되었습니다. 

현재 리소스 그룹에는 두 SQL Server 컴퓨터에 모두 연결하는 하나의 부하 분산 장치가 있습니다. 부하 분산 장치에는 SQL Server AlwaysOn 가용성 그룹 수신기에 대한 IP 주소도 있으므로 두 컴퓨터 중 하나가 가용성 그룹에 대한 요청에 응답할 수 있습니다.

> [!NOTE]
> SQL Server가 두 개의 별도 지역에 있는 경우 다른 지역에서 단계를 반복합니다. 각 지역마다 하나의 부하 분산 장치가 필요합니다. 
> 
> 

## <a name="configure-the-cluster-to-use-the-load-balancer-ip-address"></a>부하 분산 장치 IP 주소를 사용하도록 클러스터 구성
다음 단계는 클러스터에서 수신기를 구성하고 수신기를 온라인 상태로 전환하는 것입니다. 다음 단계를 수행합니다. 

1. 장애 조치(failover) 클러스터에서 가용성 그룹 수신기 만들기 
2. 수신기를 온라인 상태로 만들기

### <a name="5-create-the-availability-group-listener-on-the-failover-cluster"></a>5. 장애 조치(failover) 클러스터에서 가용성 그룹 수신기 만들기
이 단계에서는 수동으로 장애 조치(Failover) 클러스터 관리자 및 SSMS(SQL Server Management Studio)에서 가용성 그룹 수신기를 만듭니다.

[!INCLUDE [ag-listener-configure](../../../../includes/virtual-machines-ag-listener-configure.md)]

<!---------------------------
* Use RDP to connect to the Azure virtual machine that hosts the primary replica. 
* Open Failover Cluster Manager.
* Select the **Networks** node, and note the cluster network name. This name will be used in the `$ClusterNetworkName` variable in the PowerShell script.
* Expand the cluster name, and then click **Roles**.
* In the **Roles** pane, right-click the availability group name and then select **Add Resource** > **Client Access Point**.
* In the **Name** box, create a name for this new listener, then click **Next** twice, and then click **Finish**. Do not bring the listener or resource online at this point.
  
  > [!NOTE]
  > The name for the new listener is the network name that applications will use to connect to databases in the SQL Server availability group.
  > 
  > 
* Click the **Resources** tab, then expand the Client Access Point you just created. Right-click the IP resource and click properties. Note the name of the IP address. You will use this name in the `$IPResourceName` variable in the PowerShell script.
* Under **IP Address** click **Static IP Address** and set the static IP address to the same address that you used when you set the load balancer IP address on the Azure portal. Enable NetBIOS for this address and click **OK**. Repeat this step for each IP resource if your solution spans multiple Azure VNets. 
* On the cluster node that currently hosts the primary replica, open an elevated PowerShell ISE and paste the following commands into a new script.
  
        $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP Address resource name
        $ILBIP = “<X.X.X.X>” # the IP Address of the Internal Load Balancer (ILB). This is the static IP address for the load balancer you configured in the Azure portal.
  
        Import-Module FailoverClusters
  
        Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
* Update the variables and run the PowerShell script to configure the IP address and port for the new listener.
  
  > [!NOTE]
  > If your SQL Servers are in separate regions, you need to run the PowerShell script twice. The first time use the cluster network name, cluster IP resource name, and load balancer IP address from the first resource group. The second time use the cluster network name, cluster IP resource name, and load balancer IP address from the second resource group.
  > 
  > 

Now the cluster has an availability group listener resource.

### 2. Bring the listener online
With the availability group listener resource configured, you can bring the listener online so that applications can connect to databases in the availability group with the listener.

* Navigate back to Failover Cluster Manager. Expand **Roles** and then highlight your Availability Group. On the **Resources** tab, right-click the listener name and click **Properties**.
* Click the **Dependencies** tab. If there are multiple resources listed, verify that the IP addresses have OR, not AND, dependencies. Click **OK**.
* Right-click the listener name and click **Bring Online**.
* Once the listener is online, from the **Resources** tab, right-click the availability group and click **Properties**.
* Create a dependency on the listener name resource (not the IP address resources name). Click **OK**.
* Launch SQL Server Management Studio and connect to the primary replica.
* Navigate to **AlwaysOn High Availability** | **Availability Groups** | **Availability Group Listeners**. 
* You should now see the listener name that you created in Failover Cluster Manager. Right-click the listener name and click **Properties**.
* In the **Port** box, specify the port number for the availability group listener by using the $EndpointPort you used earlier (1433 was the default), then click **OK**.

------------------------------->

### <a name="verify-the-configuration-of-the-listener"></a>수신기의 구성 확인

클러스터 리소스 및 종속성이 제대로 구성된 경우 SQL Server 관리 스튜디오에서 수신기를 볼 수 있습니다. 다음 단계를 수행하여 수신기 포트를 설정합니다.

1. SQL Server Management Studio를 시작하고 주 복제본에 연결합니다.
2. **AlwaysOn 고가용성** | **가용성 그룹** | **가용성 그룹 수신기**로 이동합니다. 
1. 이제 장애 조치(Failover) 클러스터 관리자에서 만든 수신기 이름이 표시됩니다. 수신기 이름을 마우스 오른쪽 단추로 클릭하고 **속성**을 클릭합니다.
1. **포트** 상자에서 이전에 사용한 $EndpointPort(1433이 기본값임)를 사용하여 가용성 그룹 수신기에 대한 포트 번호를 지정한 다음 **확인**을 클릭합니다.

이제 리소스 관리자 모드에서 실행 중인 Azure 가상 컴퓨터의 SQL Server AlwaysOn 가용성 그룹이 만들어졌습니다. 

## <a name="test-the-connection-to-the-listener"></a>수신기에 대한 연결 테스트
연결을 테스트하려면

1. 동일한 가상 네트워크에 있지만 복제본을 소유하지 않는 SQL Server로 RDP합니다. 클러스터의 다른 SQL Server가 될 수 있습니다.
2. **sqlcmd** 유틸리티를 사용하여 연결을 테스트합니다. 예를 들어 다음 스크립트는 Windows 인증을 사용하는 수신기를 통해 주 복제본에 대한 **sqlcmd** 연결을 설정합니다.
   
        sqlcmd -S <listenerName> -E

SQLCMD 연결은 주 복제본을 호스트하는 SQL Server 인스턴스에 자동으로 연결합니다. 

## <a name="guidelines-and-limitations"></a>지침 및 제한 사항
내부 부하 분산 장치를 사용하는 Azure에서는 가용성 그룹 수신기에 다음과 같은 지침이 적용됩니다.

* 수신기가 부하 분산 장치에 구성되고 내부 부하 분산 장치 하나만 있기 때문에 클라우드 서비스당 내부 가용성 그룹 수신기 하나만 지원됩니다. 그러나 외부 수신기는 여러 개를 만들 수 있습니다. 
* 내부 부하 분산 장치를 사용할 경우 동일한 가상 네트워크 내에서만 수신기에 액세스합니다.


<!--HONumber=Jan17_HO2-->


