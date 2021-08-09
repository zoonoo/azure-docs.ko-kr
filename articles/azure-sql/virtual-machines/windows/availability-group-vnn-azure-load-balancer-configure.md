---
title: AG VNN 수신기에 대해 Load Balancer 구성
description: HADR(고가용성 및 재해 복구)을 위해 Azure VM에서 SQL Server를 사용하여 트래픽을 가용성 그룹의 VNN(가상 네트워크 이름) 수신기로 라우팅하도록 Azure Load Balancer를 구성하는 방법을 알아봅니다.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/02/2020
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 2d89759438cb625a0e220af10ab6b287096f6390
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97359883"
---
# <a name="configure-load-balancer-for-ag-vnn-listener"></a>AG VNN 수신기에 대해 Load Balancer 구성
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Azure Virtual Machines에서 클러스터는 한 번에 하나의 클러스터 노드에 있어야 하는 IP 주소를 저장하는 부하 분산 장치를 사용합니다. 이 솔루션에서 부하 분산 장치는 Always On AG(가용성 그룹)의 VNN(가상 네트워크 이름) 수신기에 대한 IP 주소를 보유합니다. 

이 문서에서는 Azure Load Balancer 서비스를 사용하여 부하 분산 장치를 구성하는 방법을 안내합니다. 부하 분산 장치는 HADR(고가용성 및 재해 복구)을 위해 Azure VM의 SQL Server를 사용하여 트래픽을 [AG(가용성 그룹) 수신기](availability-group-overview.md)로 라우팅합니다. 

SQL Server 2019 CU8 이상을 사용하는 고객을 위한 대체 연결 옵션의 경우 구성 간소화 및 장애 조치 개선을 위해 [DNN 수신기](availability-group-vnn-azure-load-balancer-configure.md)를 고려합니다.  



## <a name="prerequisites"></a>필수 구성 요소

이 문서의 단계를 완료하기 전에 다음이 준비되어 있어야 합니다.

- Azure Load Balancer가 [HADR 솔루션에 대한 연결 옵션](hadr-cluster-best-practices.md#connectivity)으로 적합하다고 결정했습니다.
- [가용성 그룹 수신기](availability-group-overview.md)를 구성했습니다.
- 최신 버전의 [PowerShell](/powershell/azure/install-az-ps)을 설치했습니다. 


## <a name="create-load-balancer"></a>부하 분산 장치 만들기

[Azure Portal](https://portal.azure.com)에서 부하 분산 장치를 만듭니다.

1. Azure Portal에서 가상 머신이 포함된 리소스 그룹으로 이동합니다.

1. **추가** 를 선택합니다. Azure Marketplace에서 **부하 분산 장치** 를 검색합니다. **부하 분산 장치** 를 선택합니다.

1. **만들기** 를 선택합니다.

1. 다음 값을 사용하여 부하 분산 장치를 설정합니다.

   - **구독**: Azure 구독.
   - **리소스 그룹**: 가상 머신이 포함된 리소스 그룹을 선택합니다.
   - **Name**: 부하 분산 장치를 식별하는 이름입니다.
   - **지역**: 가상 머신이 포함된 Azure 위치입니다.
   - **형식**: 공용 또는 비공개 중 하나입니다. 동일한 가상 네트워크 내에서 프라이빗 부하 분산 장치에 액세스할 수 있습니다. 대부분의 Azure 애플리케이션은 프라이빗 부하 분산 장치를 사용할 수 있습니다. 애플리케이션에 인터넷을 통해 직접 SQL Server에 대한 액세스가 필요한 경우 공개 부하 분산 장치를 사용합니다.
   - **SKU**: 표준입니다.
   - **가상 네트워크**: 가상 머신과 동일한 네트워크입니다.
   - **IP 주소 할당**: 정적입니다. 
   - **개인 IP 주소**: 클러스터형 네트워크 리소스에 할당한 IP 주소입니다.

   다음 이미지에서는 **부하 분산 장치 만들기** UI를 보여 줍니다.

   ![부하 분산 장치 설정](./media/failover-cluster-instance-premium-file-share-manually-configure/30-load-balancer-create.png)
   

## <a name="configure-backend-pool"></a>백 엔드 풀 구성

1. 가상 머신이 포함된 Azure 리소스 그룹으로 돌아가고 새 부하 분산 장치를 찾습니다. 리소스 그룹 보기를 새로 고쳐야 할 수도 있습니다. 부하 분산 장치를 선택합니다.

1. **백 엔드 풀** 을 선택한 다음 **추가** 를 선택합니다.

1. VM이 포함된 가용성 집합과 백 엔드 풀을 연결합니다.

1. **대상 네트워크 IP 구성** 에서 **가상 머신** 을 선택하고 클러스터 노드로 참여하는 가상 머신을 선택합니다. FCI 또는 가용성 그룹을 호스트하는 모든 가상 머신을 포함해야 합니다.

1. **확인** 을 선택하여 백엔드 풀을 만듭니다.

## <a name="configure-health-probe"></a>상태 프로브 구성

1. 부하 분산 장치 창에서 **상태 프로브** 를 선택합니다.

1. **추가** 를 선택합니다.

1. **상태 프로브 추가** 창에서 <span id="probe"> </span> 다음의 상태 프로브 매개 변수를 설정합니다.

   - **Name**: 상태 프로브의 이름입니다.
   - **프로토콜**: TCP입니다.
   - **포트**: [VM을 준비할 때](failover-cluster-instance-prepare-vm.md#uninstall-sql-server-1) 상태 프로브용으로 방화벽에서 만든 포트입니다. 이 문서에서는 TCP 포트 `59999`를 사용합니다.
   - **간격**: 5초입니다.
   - **비정상 임계값**: 2번 연속 실패입니다.

1. **확인** 을 선택합니다.

## <a name="set-load-balancing-rules"></a>부하 분산 규칙 설정

1. 부하 분산 장치 창에서 **부하 분산 규칙** 을 선택합니다.

1. **추가** 를 선택합니다.

1. 부하 분산 규칙 매개 변수를 설정합니다.

   - **이름**: 부하 분산 규칙의 이름입니다.
   - **프런트 엔드 IP 주소**: SQL Server FCI 또는 AG 수신기의 클러스터형 네트워크 리소스에 대한 IP 주소입니다.
   - **포트**: SQL Server TCP 포트입니다. 기본 인스턴스 포트는 1433입니다.
   - **백 엔드 포트**: **부동 IP(Direct Server Return)** 를 활성화할 때 **포트** 값과 동일한 포트를 사용합니다.
   - **백 엔드 풀**: 이전에 구성한 백 엔드 풀 이름입니다.
   - **상태 프로브**: 이전에 구성한 상태 프로브입니다.
   - **세션 지속성**: 없음
   - **유휴 제한 시간(분)** : 4.
   - **부동 IP(Direct Server Return)** : 사용

1. **확인** 을 선택합니다.

## <a name="configure-cluster-probe"></a>클러스터 프로브 구성

PowerShell에서 클러스터 프로브 포트 매개 변수를 설정합니다.

클러스터 프로브 포트 매개 변수를 설정하려면 다음 스크립트의 변수를 사용자 환경의 값으로 업데이트합니다. 스크립트에서 꺾쇠 괄호(`<` 및 `>`)를 제거합니다.

```powershell
$ClusterNetworkName = "<Cluster Network Name>"
$IPResourceName = "<SQL Server FCI / AG Listener IP Address Resource Name>" 
$ILBIP = "<n.n.n.n>" 
[int]$ProbePort = <nnnnn>

Import-Module FailoverClusters

Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
```

다음 표는 업데이트해야 하는 값을 보여 줍니다.


|**값**|**설명**|
|---------|---------|
|`Cluster Network Name`| 네트워크의 Windows Server 장애 조치(failover) 클러스터 이름입니다. **장애 조치(failover) 클러스터 관리자** > **네트워크** 에서 네트워크를 마우스 오른쪽 단추로 클릭하고 **속성** 을 선택합니다. 올바른 값은 **일반** 탭의 **이름** 아래에 있습니다.|
|`AG listener IP Address Resource Name`|SQL Server FCI 또는 AG 수신기의 IP 주소에 대한 리소스 이름입니다. **장애 조치(failover) 클러스터 관리자** > **역할** 의 SQL Server FCI 역할 아래에 있는 **서버 이름** 에서 IP 주소 리소스를 마우스 오른쪽 단추로 클릭하고 **속성** 을 선택합니다. 올바른 값은 **일반** 탭의 **이름** 아래에 있습니다.|
|`ILBIP`|ILB(내부 부하 분산 장치)의 IP 주소입니다. 이 주소는 Azure Portal에서 ILB의 프런트 엔드 주소로 구성됩니다. 또한 SQL Server FCI의 IP 주소이기도 합니다. 이 주소는 `<AG listener IP Address Resource Name>`이 있는 동일한 속성 페이지의 **장애 조치(Failover) 클러스터 관리자** 에서 확인할 수 있습니다.|
|`nnnnn`|부하 분산 장치의 상태 프로브에서 구성한 프로브 포트입니다. 사용하지 않는 모든 TCP 포트는 유효합니다.|
|"SubnetMask"| 클러스터 매개 변수의 서브넷 마스크입니다. TCP IP 브로드캐스트 주소 `255.255.255.255`여야 합니다.| 


클러스터 프로브를 설정한 후에는 PowerShell에서 모든 클러스터 매개 변수를 볼 수 있습니다. 다음 스크립트를 실행합니다.

```powershell
Get-ClusterResource $IPResourceName | Get-ClusterParameter
```


## <a name="test-failover"></a>테스트 장애 조치


클러스터 기능의 유효성을 검사하기 위해 클러스터형 리소스의 장애 조치(failover)를 테스트하세요. 


다음과 같은 단계를 수행합니다.

1. [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)를 열고 가용성 그룹 수신기에 연결합니다. 
1. **개체 탐색기** 에서 **Always On 가용성 그룹** 을 확장합니다. 
1. 마우스 오른쪽 단추로 가용성 그룹을 클릭하고, **장애 조치** 를 선택합니다. 
1. 마법사의 안내에 따라 가용성 그룹을 보조 복제본으로 장애 조치합니다. 

복제본이 역할을 전환하고 둘 다 동기화되면 장애 조치가 성공합니다. 


## <a name="test-connectivity"></a>연결 테스트

연결을 테스트하려면 동일한 가상 네트워크의 다른 가상 머신에 로그인합니다. **SQL Server Management Studio** 를 열고 가용성 그룹 수신기에 연결합니다.

>[!NOTE]
>필요한 경우 [SQL Server Management Studio를 다운로드](/sql/ssms/download-sql-server-management-studio-ssms)할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Azure의 SQL Server HADR 기능에 대한 자세한 내용은 [가용성 그룹](availability-group-overview.md) 및 [장애 조치(failover) 클러스터 인스턴스](failover-cluster-instance-overview.md)를 참조하세요. 또한 고가용성 및 재해 복구를 위한 환경 구성에 필요한 [모범 사례](hadr-cluster-best-practices.md)를 배울 수 있습니다. 



