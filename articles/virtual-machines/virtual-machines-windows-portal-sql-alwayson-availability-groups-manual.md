---
title: "수동으로 Azure VM의 Always On 가용성 그룹 구성 - Microsoft Azure"
description: "Azure 가상 컴퓨터로 Always On 가용성 그룹을 만듭니다. 이 자습서에서는 스크립트보다는 사용자 인터페이스 및 도구를 주로 사용합니다."
services: virtual-machines
documentationcenter: na
author: MikeRayMSFT
manager: timlt
editor: monicar
tags: azure-service-management
ms.assetid: 986cbc2e-553d-4eba-8acb-c34ad7fd1d8b
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: infrastructure-services
ms.date: 10/21/2016
ms.author: MikeRayMSFT
translationtype: Human Translation
ms.sourcegitcommit: ee34a7ebd48879448e126c1c9c46c751e477c406
ms.openlocfilehash: 6b102884c7d5aafc85acdeb52a92b630e6e99b6a


---
# <a name="configure-always-on-availability-group-in-azure-vm-manually---resource-manager"></a>수동으로 Azure VM의 Always On 가용성 그룹 구성 - 리소스 관리자
> [!div class="op_single_selector"]
> * [Resource Manager: 템플릿](virtual-machines-windows-portal-sql-alwayson-availability-groups.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
> * [리소스 관리자: 수동](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
> * [클래식: UI](virtual-machines-windows-classic-portal-sql-alwayson-availability-groups.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
> * [클래식: PowerShell](virtual-machines-windows-classic-ps-sql-alwayson-availability-groups.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
> 
> 

<br/>

이 종단 간 자습서에서는 Azure Resource Manager 가상 컴퓨터에 SQL Server 가용성 그룹을 구현하는 방법을 보여줍니다. 

이 자습서에서는 다음 요소를 만듭니다.

* 프런트 엔드 및 백 엔드 서브넷을 비롯한 두 서브넷을 포함하는 가상 네트워크
* AD(Active Directory) 도메인을 포함한 가용성 집합에 있는 두 개의 도메인 컨트롤러 
* 백 엔드 서브넷에 배포되고 AD 도메인에 가입된 가용성 집합의 SQL Server VM 2개
* 노드 과반수 쿼럼 모델을 포함하는 3-노드 WSFC 클러스터
* 하나 이상의 가용성 그룹 수신기를 지원하는 IP 주소가 하나 이상 있는 내부 부하 분산 장치
* 가용성 데이터베이스의 두 개의 동기 커밋 복제본이 포함된 가용성 그룹

아래 그림은 솔루션을 그래픽으로 표현한 것입니다.

![Azure Resource Manager에서 AG를 위한 아키텍처](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/00-EndstateSampleNoELB.png)

이것은 가능한 구성 중 하나입니다. 요구 사항에 맞게 구성을 수정할 수 있습니다. 예를 들어 도메인 컨트롤러를 쿼럼 파일 공유 감시로 사용하여 가상 컴퓨터 수를 줄일 수 있습니다. 이렇게 하면 2복제본 가용성 그룹에 대한 VM 수를 줄일 수 있습니다. 이 방법을 사용하면 이 솔루션에서 VM 수가 하나씩 줄어듭니다.

이 자습서에서는 수신기용 IP 주소가 하나 있는 단일 가용성 그룹을 만듭니다. IP 주소와 수신기가 각각 따로 있는 가용성 그룹을 여러 개 만들 수도 있습니다. 각 IP 주소는 동일한 부하 분산 장치를 사용합니다. 부하 분산 장치에 여러 IP 주소를 구성하려면 PowerShell을 사용합니다. 자세한 내용은 [하나 이상의 Always On 가용성 그룹 수신기 구성 - Resource Manager](virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 참조하세요.

이 자습서에서는 여러 Azure 가상 컴퓨터를 구축하고 구성해야 하므로 완료하는 데 몇 시간이 걸립니다. 또한 이 전체 솔루션을 자동으로 빌드할 수 있습니다. Azure 포털에는 수신기와 함께 AlwaysOn 가용성 그룹을 위한 갤러리 설치가 있습니다. 이 갤러리 설정은 가용성 그룹에 필요한 모든 항목을 자동으로 구성합니다. 자세한 내용은 [포털 - 리소스 관리자](virtual-machines-windows-portal-sql-alwayson-availability-groups.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 참조하세요. 

[!INCLUDE [availability-group-template](../../includes/virtual-machines-windows-portal-sql-alwayson-ag-template.md)]

이 자습서에서는 다음을 가정합니다.

* Azure 계정이 있습니다.
* GUI를 사용하여 가상 컴퓨터 갤러리에서 SQL Server VM을 프로비전하는 방법을 이미 알고 있습니다. 자세한 내용은 [Azure에서 SQL Server 가상 컴퓨터 프로비전](virtual-machines-windows-portal-sql-server-provision.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* 가용성 그룹을 확실하게 이해하고 있습니다. 자세한 내용은 [Always On 가용성 그룹(SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx)을 참조하세요.

> [!NOTE]
> SharePoint와 가용성 그룹을 사용하는 것에 관심이 있는 경우 [SharePoint 2013에 대해 SQL Server 2012 Always On 가용성 그룹 구성](https://technet.microsoft.com/library/jj715261.aspx)을 참조하세요.
> 
> 

## <a name="create-resource-group"></a>리소스 그룹 만들기
1. [Azure 포털](http://portal.azure.com)에 로그인합니다. 
2. **+ 새로 만들기**를 클릭하고 **마켓플레이스** 검색 창에 **리소스 그룹**을 입력합니다.
   
    ![리소스 그룹](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/01-resourcegroupsymbol.png)
3.  **리소스 그룹** 
   
    ![새 리소스 그룹](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/01-newresourcegroup.png)
4. **만들기**를 클릭합니다. 
5. **리소스 그룹** 블레이드의 **리소스 그룹 이름**에 **SQL-HA-RG**를 입력합니다.
6. 여러 Azure 구독이 있는 경우 해당 구독이 가용성 그룹을 만들려는 Azure 구독인지 확인합니다. 
7. 위치를 선택합니다. 위치는 가용성 그룹을 만들려는 Azure 지역입니다. 이 자습서에서는 모든 리소스를 한 Azure 위치에 빌드합니다. 
8. **대시보드에 고정** 이 선택되어 있는지 확인합니다. 이 선택적 설정은 Azure 포털 대시보드에 리소스 그룹에 대한 바로 가기를 배치합니다. 
9. **만들기** 를 클릭하여 리소스 그룹을 만듭니다.

리소스 그룹이 만들어지고 리소스 그룹에 대한 바로 가기가 포털에 고정됩니다.

## <a name="create-network-and-subnets"></a>네트워크 및 서브넷 만들기
다음 단계는 Azure 리소스 그룹에 네트워크 및 서브넷을 만드는 것입니다.

이 솔루션은 하나의 가상 네트워크를 두 개의 서브넷과 함께 사용합니다. [가상 네트워크 개요](../virtual-network/virtual-networks-overview.md) 에서는 Azure의 네트워크에 대한 자세한 정보를 제공합니다.

가상 네트워크를 만들려면

1. Azure Portal에서 새 리소스 그룹을 클릭하고 **+** 를 클릭하여 리소스 그룹에 새 항목을 추가합니다. **모두** 블레이드가 열립니다. 
   
   ![새 항목](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/02-newiteminrg.png)
2. **가상 네트워크**를 검색합니다.
   
     ![가상 네트워크 검색](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/04-findvirtualnetwork.png)
3. **가상 네트워크**를 클릭합니다.
4. **가상 네트워크** 블레이드에서 **Resource Manager** 배포 모델을 클릭하고 **만들기**를 클릭합니다.
   
     ![가상 네트워크 만들기](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/05-createvirtualnetwork.png)
5. **가상 네트워크 만들기** 블레이드에서 가상 네트워크를 구성합니다. 
   
   다음 표에서는 가상 네트워크에 대한 설정을 보여 줍니다.
   
   | **필드** | 값 |
   | --- | --- |
   | **Name** |autoHAVNET |
   | **주소 공간** |10.0.0.0/16 |
   | **서브넷 이름** |Subnet-1 |
   | **서브넷 주소 범위** |10.0.0.0/24 |
   | **구독** |사용하려는 구독을 지정합니다. 하나의 구독만 있는 경우 이 옵션이 비어 있을 수 있습니다. |
   | **위치** |Azure 위치를 지정합니다. |
   
    주소 공간 및 서브넷 주소 범위는 표와 다를 수 있습니다. 구독에 따라 사용 가능한 주소 공간 및 해당 서브넷 주소 범위가 지정됩니다. 사용할 수 있는 주소 공간이 충분하지 않으면 다른 구독을 사용하세요. 
6.  **만들기**
   
   ![가상 네트워크 구성](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/06-configurevirtualnetwork.png)

포털 대시보드가 반환되고 새 네트워크가 만들어지는 시점을 사용자에게 알립니다.

### <a name="create-the-second-subnet"></a>두 번째 서브넷 만들기
이제 가상 네트워크에는 Subnet-1이라는 하나의 서브넷이 있습니다. 도메인 컨트롤러는 이 서브넷을 사용합니다. SQL Server는 **Subnet-2**라는 두 번째 서브넷을 사용합니다. Subnet-2를 구성하려면

1. 대시보드에서 사용자가 만든 리소스 그룹인 **SQL-HA-RG**를 클릭합니다. 리소스 그룹의 **리소스**에서 네트워크를 찾습니다.
   
    **SQL-HA-RG**가 표시되지 않으면 **리소스 그룹**을 클릭하고 리소스 그룹 이름으로 필터링하여 찾습니다.
2. 리소스 목록에서 **autoHAVNET** 을 클릭합니다. 네트워크 구성 블레이드가 열립니다.
3. **autoHAVNET** 가상 네트워크에서 **모든 설정**을 클릭합니다.
4. **설정** 블레이드에서 **서브넷**을 클릭합니다**.
   
    이미 만들어진 서브넷을 확인합니다. 
   
    ![가상 네트워크 구성](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/07-addsubnet.png)
5. 두 번째 서브넷을 만듭니다. **+ 서브넷**을 클릭합니다. 
6. **서브넷 추가** 블레이드에서 **이름** 아래에 **subnet-2**를 입력하여 서브넷을 구성합니다. 유효한 **주소 범위**가 자동으로 지정됩니다. 이 주소 범위에 최소 10개의 주소가 있는지 확인합니다. 프로덕션 환경에서는 더 많은 주소가 필요할 수 있습니다. 
7. **확인**을 클릭합니다.
   
    ![가상 네트워크 구성](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/08-configuresubnet.png)

다음은 가상 네트워크 및 두 서브넷에 대한 구성 설정의 요약입니다.

| **필드** | 값 |
| --- | --- |
| **Name** |**autoHAVNET** |
| **주소 공간** |구독에서 사용 가능한 주소 공간에 따라 달라집니다. 일반적인 값은 10.0.0.0/16입니다. |
| **서브넷 이름** |**Subnet-1** |
| **서브넷 주소 범위** |구독에서 사용 가능한 주소 범위에 따라 달라집니다. 일반적인 값은 10.0.0.0/24입니다. |
| **서브넷 이름** |**Subnet-2** |
| **서브넷 주소 범위** |구독에서 사용 가능한 주소 범위에 따라 달라집니다. 일반적인 값은 10.0.1.0/24입니다. |
| **구독** |사용하려는 구독을 지정합니다. |
| **리소스 그룹** |**SQL-HA-RG** |
| **위치** |리소스 그룹에 대해 선택한 위치와 같은 위치를 지정합니다. |

## <a name="create-availability-sets"></a>가용성 집합 만들기
가상 컴퓨터를 만들기 전에 가용성 집합을 만들어야 합니다. 가용성 집합은 계획되거나 계획되지 않은 유지 관리 이벤트에 대한 가동 중지 시간을 줄입니다. Azure 가용성 집합은 Azure에서 물리적 장애 도메인 및 업데이트 도메인에 배치하는 리소스의 논리적 그룹입니다. 장애 도메인을 사용하면 가용성 집합의 구성원이 개별 전원 및 네트워크 리소스를 사용할 수 있습니다. 업데이트 도메인을 사용하면 가용성 집합의 구성원이 유지 관리를 위해 동시에 중단되지 않습니다. [가상 컴퓨터의 가용성을 관리합니다](virtual-machines-windows-manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

두 개의 가용성 집합이 필요합니다. 하나는 도메인 컨트롤러용이고 두 번째 SQL Server용입니다.

가용성 집합을 만들려면 리소스 그룹으로 이동하고 **추가**를 클릭합니다. **가용성 집합**을 입력하여 결과를 필터링합니다. 결과에서 **가용성 집합** 을 클릭합니다. **만들기**를 클릭합니다.

다음 표의 매개 변수에 따라 두 개의 가용성 집합을 구성합니다.

| **필드** | 도메인 컨트롤러 가용성 집합 | SQL Server 가용성 집합 |
| --- | --- | --- |
| **Name** |adavailabilitySet |sqlAvailabilitySet |
| **리소스 그룹** |SQL-HA-RG |SQL-HA-RG |
| **장애 도메인** |3 |3 |
| **업데이트 도메인** |5 |3 |

가용성 집합을 만든 후 Azure 포털의 리소스 그룹으로 돌아옵니다.

## <a name="create-domain-controllers"></a>도메인 컨트롤러 만들기
네트워크, 서브넷, 가용성 집합을 만든 후에 인터넷 연결 부하 분산 장치를 만들었습니다. 도메인 컨트롤러에 대한 가상 컴퓨터를 만들 준비가 되었습니다.

### <a name="create-the-virtual-machines-for-the-domain-controllers"></a>도메인 컨트롤러에 대한 가상 컴퓨터 만들기
도메인 컨트롤러를 만들고 구성하려면 **SQL-HA-RG** 리소스 그룹으로 돌아갑니다.

1. 추가를 클릭합니다. **모두** 블레이드가 열립니다.
2. **Windows Server 2012 R2 Datacenter**를 입력합니다. 
3. **Windows Server 2012 R2 Datacenter**를 클릭합니다. **Windows Server 2012 R2 Datacenter** 블레이드에서 배포 모델이 **Resource Manager**인지 확인하고 **만들기**를 클릭합니다. **가상 컴퓨터 만들기** 블레이드가 열립니다. 

이전 단계를 두 번 반복하여 두 개의 가상 컴퓨터를 만듭니다. 두 개의 가상 컴퓨터 이름을 지정합니다.

* ad-primary-dc
* ad-secondary-dc
  
  > [!NOTE]
  > **ad-secondary-dc** 는 Active Directory 도메인 서비스에 높은 가용성을 제공하는 선택적 구성 요소입니다. 
  > 
  > 

다음 표에서는 이러한 두 컴퓨터에 대한 설정을 보여 줍니다.

| **필드** | 값 |
| --- | --- |
| **사용자 이름** |DomainAdmin |
| **암호** |Contoso!0000 |
| **구독** |*구독* |
| **리소스 그룹** |SQL-HA-RG |
| **위치** |*사용자의 위치* |
| **크기** |D1_V2(표준) |
| **저장소 유형** |표준 |
| **저장소 계정** |*자동으로 생성됨* |
| **가상 네트워크** |autoHAVNET |
| **서브넷** |Subnet-1 |
| **공용 IP 주소** |*VM과 같은 이름* |
| **네트워크 보안 그룹** |*VM과 같은 이름* |
| **진단** |사용 |
| **진단 저장소 계정** |*자동으로 생성됨* |
| **가용성 집합** |adavailabilitySet |

> [!NOTE]
> 만든 후에는 VM의 가용성 집합을 변경할 수 없습니다.
> 
> 

가상 컴퓨터가 만들어집니다.

가상 컴퓨터를 만든 후에 도메인 컨트롤러를 구성합니다.

### <a name="configure-the-domain-controller"></a>도메인 컨트롤러 구성
다음 단계에서는 corp.contoso.com에 대한 도메인 컨트롤러로 **ad-primary-dc** 컴퓨터를 구성합니다.

1. 포털에서 **SQL-HA-RG** 리소스 그룹을 선택하고 **ad-primary-dc** 컴퓨터를 선택합니다. **ad-primary-dc** 블레이드에서 **연결**을 클릭하여 원격 데스크톱 액세스를 위한 RDP 파일을 엽니다.
   
    ![가상 컴퓨터에 연결](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/20-connectrdp.png)
2. 구성된 관리자 계정(**\DomainAdmin**) 및 암호(**Contoso!0000**)로 로그인합니다.
3. 기본적으로 **서버 관리자** 대시보드가 표시됩니다.
4. 대시보드에서 **역할 및 기능 추가** 링크를 클릭합니다.
   
    ![서버 탐색기 역할 추가](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC784623.png)
5. **서버 역할** 섹션으로 이동할 때까지 **다음**을 선택합니다.
6. **Active Directory 도메인 서비스** 및 **DNS 서버** 역할을 선택합니다. 메시지가 표시되면 이러한 역할에 필요한 기능을 더 추가합니다.
   
   > [!NOTE]
   > 정적 IP 주소가 없다는 경고가 표시됩니다. 구성을 테스트하는 경우 계속을 클릭합니다. 프로덕션 시나리오의 경우 Azure Portal에서 IP 주소를 고정으로 설정하거나 [PowerShell을 사용하여 도메인 컨트롤러 컴퓨터의 고정 IP 주소를 설정](../virtual-network/virtual-networks-reserved-private-ip.md)합니다.
   > 
   > 
   
    ![역할 추가 대화 상자](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC784624.png)
7. **확인** 섹션에 도달할 때까지 **다음**을 클릭합니다. **필요한 경우 자동으로 대상 서버 다시 시작** 확인란을 선택합니다.
8. **Install**을 클릭합니다.
9. 기능 설치를 완료한 후에는 **서버 관리자** 대시보드로 돌아갑니다.
10. 왼쪽 창에서 새 **AD DS** 옵션을 선택합니다.
11. 노란색 경고 표시줄에서 **자세히** 링크를 클릭합니다.
    
    ![DNS 서버 VM에서 AD DS 대화 상자](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC784625.png)
12. **모든 서버 작업 세부 정보** 대화 상자의 **작업** 열에서 **이 서버를 도메인 컨트롤러로 승격**을 클릭합니다.
13. **Active Directory 도메인 서비스 구성 마법사**에서 다음 값을 사용합니다.
    
    | **Page** | 설정 |
    | --- | --- |
    | **배포 구성** |**새 포리스트 추가** = 선택됨<br/>**루트 도메인 이름** = corp.contoso.com |
    | **도메인 컨트롤러 옵션** |**암호** = Contoso!0000<br/>**암호 확인** = Contoso!0000 |
14. **다음**을 클릭하여 마법사의 다른 페이지를 진행합니다. **필수 구성 요소 확인** 페이지에서 다음 메시지가 표시되는지 확인합니다. **모든 필수 구성 요소 검사를 마쳤습니다**. 해당하는 모든 경고 메시지를 검토해야 하지만 설치는 계속할 수 있습니다.
15. **Install**을 클릭합니다. **ad-primary-dc** 가상 컴퓨터가 자동으로 다시 부팅됩니다.

### <a name="configure-the-second-domain-controller"></a>두 번째 도메인 컨트롤러 구성
주 도메인 컨트롤러를 다시 부팅한 후에 두 번째 도메인 컨트롤러를 구성할 수 있습니다. 이 선택적 단계는 가용성을 높이기 위한 것입니다. 이 단계를 완료하려면 도메인 컨트롤러에 대한 개인 IP 주소를 알아야 합니다. Azure 포털에서 이 주소를 가져올 수 있습니다. 두 번째 도메인 컨트롤러를 구성하려면 다음 단계를 수행합니다.

1. **ad-primary-dc** 컴퓨터에 로그인합니다. 
2. 원격 데스크톱을 열고 IP 주소를 통해 보조 도메인 컨트롤러에 연결합니다. 두 번째 도메인 컨트롤러의 IP 주소를 모르는 경우 Azure 포털로 이동하여 두 번째 도메인 컨트롤러의 네트워크 인터페이스에 할당된 주소를 확인합니다. 
3. 기본 설정된 DNS 서버 주소를 도메인 컨트롤러의 주소로 변경합니다. 
4. 주 도메인 컨트롤러(**ad-primary-dc**)에 대해 RDP 파일을 시작하고 구성된 관리자 계정(**BUILTIN\DomainAdmin**) 및 암호(**Contoso!0000**)를 사용하여 VM에 로그인합니다.
5. 주 도메인 컨트롤러에서 IP 주소를 사용하여 **ad-secondary-dc** 에 대해 원격 데스크톱을 시작합니다. 동일한 계정 및 암호를 사용합니다.
6. 로그인하면 **서버 관리자** 대시보드가 표시됩니다. 왼쪽 창에서 **로컬 서버** 를 클릭합니다.
7. **DHCP에 의해 할당된 IPv4 주소, IPv6 사용 가능** 링크를 선택합니다.
8. **네트워크 연결** 창에서 네트워크 아이콘을 선택합니다.
   
    ![VM 기본 설정 DNS 서버 변경](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC784629.png)
9. 명령 모음에서 **이 연결의 설정 변경** (창의 크기에 따라 명령을 표시하기 위해 이중 오른쪽 화살표를 클릭해야 할 수 있음)을 클릭합니다.
10. **인터넷 프로토콜 버전 4(TCP/IPv4)** 를 선택하고 속성을 클릭합니다.
11. 다음 DNS 서버 주소 사용을 선택하고 **기본 설정 DNS 서버**에서 주 도메인 컨트롤러 주소를 지정합니다.
12. 주소는 Azure 가상 네트워크에서 subnet-1 서브넷의 VM에 할당된 주소이며 해당 VM은 **ad-primary-dc**입니다. **ad-primary-dc**의 IP 주소를 확인하려면 아래와 같이 명령 프롬프트에서 **nslookup ad-primary-dc**를 사용합니다.
    
    ![NSLOOKUP을 사용하여 DC에 대한 IP 주소 찾기](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC664954.png)
    
    > [!NOTE]
    > DNS를 설정한 후에는 구성원 서버에 대한 RDP 세션이 닫힐 수 있습니다. 이렇게 하면 Azure 포털에서 VM이 다시 부팅됩니다.
    > 
    > 
13. **확인**을 클릭한 후 **닫기**를 클릭하여 변경 내용을 커밋합니다. 이제 VM을 **corp.contoso.com**에 연결할 수 있습니다.
14. 첫 번째 도메인 컨트롤러를 만들 때 수행한 단계를 반복합니다. 단, **Active Directory 도메인 서비스 구성 마법사**에서 다음 값을 사용합니다.

| Page | 설정 |
| --- | --- |
| **배포 구성** |**기존 도메인에 도메인 컨트롤러 추가** = 선택됨<br/>**루트** = corp.contoso.com |
| **도메인 컨트롤러 옵션** |**암호** = Contoso!0000<br/>**암호 확인** = Contoso!0000 |

### <a name="configure-domain-accounts"></a>도메인 계정 구성
다음 단계에서는 나중에 사용하기 위해 AD(Active Directory) 계정을 구성합니다.

1. **ad-primary-dc** 컴퓨터에 다시 로그인합니다.
2. **서버 관리자**에서 **도구**를 선택한 후 **Active Directory 관리 센터**를 클릭합니다.
   
    ![Active Directory 관리 센터](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC784626.png)
3.  **Active Directory 관리 센터** select **corp(로컬)** 을 선택합니다.
4. 오른쪽 **작업** 창에서 **새로 만들기**를 선택한 후 **사용자**를 클릭합니다. 다음 설정을 사용합니다.
   
   | 설정 | 값 |
   | --- | --- |
   |  **이름** |Install |
   |  **사용자 SamAccountName** |Install |
   |  **암호** |Contoso!0000 |
   |  **암호 확인** |Contoso!0000 |
   |  **기타 암호 옵션** |선택 |
   |  **암호 사용 기간 제한 없음** |선택 |
5. **확인**을 클릭하여 **Install** 사용자를 만듭니다. 이 계정을 사용하여 장애 조치(Failover) 클러스터 및 가용성 그룹을 구성합니다.
6. 동일한 단계로 **CORP\SQLSvc1** 및 **CORP\SQLSvc2**의 추가 사용자 2개를 만듭니다. SQL Server 서비스는 이러한 계정을 사용합니다. 다음으로 WSFC(Windows 서비스 장애 조치(failover) 클러스터링)를 구성하는 데 필요한 권한을 **CORP\Install**에 부여합니다.
7. **Active Directory 관리 센터**의 왼쪽 창에서 **corp(로컬)**을 선택합니다. 다음으로 오른쪽 **작업** 창에서 **속성**을 클릭합니다.
   
    ![CORP 사용자 속성](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC784627.png)
8. **확장**을 선택한 후 **보안** 탭에서 **고급** 단추를 클릭합니다.
9. **corp 고급 보안 설정** 대화 상자에서 **추가**를 클릭합니다.
10. **보안 주체 선택**을 클릭합니다. 그런 다음 **CORP\Install**을 검색합니다. **확인**을 클릭합니다.
11. **모든 속성 읽기** 및 **컴퓨터 개체 만들기** 권한을 선택합니다.
    
     ![Corp 사용자 권한](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC784628.png)
12. **확인**을 클릭한 후 **확인**을 한 번 더 클릭합니다. corp 속성 창을 닫습니다.

Active Directory 및 사용자 개체 구성을 완료했으므로 2개의 SQL Server VM과 1개의 감시 서버 VM을 만듭니다. 그런 다음 세 개의 VM을 도메인에 연결합니다.

## <a name="create-sql-servers"></a>SQL Server를 만듭니다.
### <a name="create-and-configure-the-sql-server-vms"></a>SQL Server VM 만들기 및 구성
다음으로 WSFC 클러스터 노드 1개와 SQL Server VM 2개를 포함하는 VM을 3개 만듭니다. 각 VM을 만들려면 **SQL-HA-RG** 리소스 그룹으로 돌아가서 **추가**를 클릭하고 적합한 갤러리 항목, **가상 컴퓨터** 및 **갤러리에서**를 차례로 검색합니다. 다음으로 아래 표의 템플릿을 사용하면 VM을 만드는 데 도움이 됩니다.

| Page | VM1 | VM2 | VM3 |
| --- | --- | --- | --- |
| 적절한 갤러리 항목 선택 |**Windows Server 2012 R2 Datacenter** |**Windows Server 2012 R2의 SQL Server 2014 SP1 Enterprise** |**Windows Server 2012 R2의 SQL Server 2014 SP1 Enterprise** |
| 가상 컴퓨터 구성 **기본 사항** |**이름** = cluster-fsw<br/>**사용자 이름** = DomainAdmin<br/>**암호** = Contoso!0000<br/>**구독** = 사용자 구독<br/>**리소스 그룹** = SQL-HA-RG<br/>**위치** = 해당 Azure 위치 |**이름** = sqlserver-0<br/>**사용자 이름** = DomainAdmin<br/>**암호** = Contoso!0000<br/>**구독** = 사용자 구독<br/>**리소스 그룹** = SQL-HA-RG<br/>**위치** = 해당 Azure 위치 |**이름** = sqlserver-1<br/>**사용자 이름** = DomainAdmin<br/>**암호** = Contoso!0000<br/>**구독** = 사용자 구독<br/>**리소스 그룹** = SQL-HA-RG<br/>**위치** = 해당 Azure 위치 |
| 가상 컴퓨터 구성 **크기** |DS1(1 코어, 3.5GB 메모리) |**크기** = DS 2(2개 코어, 7GB 메모리) |**크기** = DS 2(2개 코어, 7GB 메모리) |
| 가상 컴퓨터 구성 **설정** |**Storage** = Premium(SSD)<br/>**네트워크 서브넷** = autoHAVNET<br/>** 계정** = 자동으로 생성된 Storage 계정 사용<br/>**서브넷** = subnet-2(10.1.1.0/24)<br/>**공용 IP 주소** = 없음<br/>**네트워크 보안 그룹** = 없음<br/>**진단 모니터링** = 사용<br/>**진단 Storage 계정** = 자동으로 생성된 Storage계정 사용<br/>**가용성 집합** = sqlAvailabilitySet<br/> |**Storage** = Premium(SSD)<br/>**네트워크 서브넷** = autoHAVNET<br/>** 계정** = 자동으로 생성된 Storage 계정 사용<br/>**서브넷** = subnet-2(10.1.1.0/24)<br/>**공용 IP 주소** = 없음<br/>**네트워크 보안 그룹** = 없음<br/>**진단 모니터링** = 사용<br/>**진단 Storage 계정** = 자동으로 생성된 Storage계정 사용<br/>**가용성 집합** = sqlAvailabilitySet<br/> |**Storage** = Premium(SSD)<br/>**네트워크 서브넷** = autoHAVNET<br/>** 계정** = 자동으로 생성된 Storage 계정 사용<br/>**서브넷** = subnet-2(10.1.1.0/24)<br/>**공용 IP 주소** = 없음<br/>**네트워크 보안 그룹** = 없음<br/>**진단 모니터링** = 사용<br/>**진단 Storage 계정** = 자동으로 생성된 Storage계정 사용<br/>**가용성 집합** = sqlAvailabilitySet<br/> |
| 가상 컴퓨터 구성 **SQL Server 설정** |해당 없음 |**SQL 연결** = 개인(가상 네트워크 내)<br/>**포트** = 1433<br/>**SQL 인증** = 사용 안 함<br/>**Storage 구성** = 일반<br/>**자동화된 패치** = 일요일 2시<br/>**자동화된 백업** = 사용 안 함</br>**Azure 주요 자격 증명 모음 통합** = 사용 안 함 |**SQL 연결** = 개인(가상 네트워크 내)<br/>**포트** = 1433<br/>**SQL 인증** = 사용 안 함<br/>**Storage 구성** = 일반<br/>**자동화된 패치** = 일요일 2시<br/>**자동화된 백업** = 사용 안 함</br>**Azure 주요 자격 증명 모음 통합** = 사용 안 함 |

<br/>

> [!NOTE]
> 기본 계층 컴퓨터는 나중에 가용성 그룹 수신기에 필요한 부하가 분산된 끝점을 지원하지 않기 때문에 이전 구성은 표준 계층 가상 컴퓨터를 제안합니다. 여기에서 제안하는 컴퓨터 크기는 Azure VM에서 가용성 그룹 테스트를 위해 계획된 것입니다. 프로덕션 작업에서 최상의 성능을 얻으려면 [Azure 가상 컴퓨터의 SQL Server에 대한 성능 모범 사례](virtual-machines-windows-sql-performance.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)에서 SQL Server 컴퓨터 크기 및 구성에 대한 권장 사항을 참조하세요.
> 
> 

3개의 VM이 완전히 프로비전되면 VM을 **corp.contoso.com** 도메인에 연결하고 컴퓨터에 CORP\Install 관리 권한을 부여해야 합니다.

쉽게 진행할 수 있도록 각 VM에 대한 Azure 가상 IP 주소를 적어둡니다. 각 서버에 대한 IP 주소를 가져옵니다. Azure SQL-HA-RG 리소스 그룹에서 **autohaVNET** 리소스를 클릭합니다. **autohaVNET** 블레이드는 네트워크의 각 컴퓨터에 대한 IP 주소를 표시합니다.

다음 장치에 대한 IP 주소를 기록합니다. 

| VM 역할 | 장치 | IP 주소 |
| --- | --- | --- |
| 주 도메인 컨트롤러 |ad-primary-dc | |
| 두 번째 도메인 컨트롤러 |ad-secondary-dc | |
| 클러스터 파일 공유 감시 |cluster-fsw | |
| SQL Server |sqlserver-0 | |
| SQL Server |sqlserver-1 | |

이러한 주소를 사용하여 각 VM에 대한 DNS 서비스를 구성합니다. 3개의 VM 각각에 대해 다음 단계를 사용합니다.

1. 먼저, 각 구성원 서버에 대해 기본 설정된 DNS 서버 주소를 변경합니다. 
2. 주 도메인 컨트롤러(**ad-primary-dc**)에 대해 RDP 파일을 시작하고 구성된 관리자 계정(**BUILTIN\DomainAdmin**) 및 암호(**Contoso!0000**)를 사용하여 VM에 로그인합니다.
3. 주 도메인 컨트롤러에서 IP 주소를 사용하여 **sqlserver-0** 에 대해 원격 데스크톱을 시작합니다. 동일한 계정 및 암호를 사용합니다.
4. 로그인하면 **서버 관리자** 대시보드가 표시됩니다. 왼쪽 창에서 **로컬 서버** 를 클릭합니다.
5. **DHCP에 의해 할당된 IPv4 주소, IPv6 사용 가능** 링크를 선택합니다.
6. **네트워크 연결** 창에서 네트워크 아이콘을 선택합니다.
   
    ![VM 기본 설정 DNS 서버 변경](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC784629.png)
7. 명령 모음에서 **이 연결의 설정 변경** (창의 크기에 따라 명령을 표시하기 위해 이중 오른쪽 화살표를 클릭해야 할 수 있음)을 클릭합니다.
8. **인터넷 프로토콜 버전 4(TCP/IPv4)** 를 선택하고 속성을 클릭합니다.
9. 다음 DNS 서버 주소 사용을 선택하고 **기본 설정 DNS 서버**에서 주 도메인 컨트롤러 주소를 지정합니다.
10. 주소는 Azure 가상 네트워크에서 subnet-1 서브넷의 VM에 할당된 주소이며 해당 VM은 **ad-primary-dc**입니다. **ad-primary-dc**의 IP 주소를 확인하려면 아래와 같이 명령 프롬프트에서 **nslookup ad-primary-dc**를 사용합니다.
    
    ![NSLOOKUP을 사용하여 DC에 대한 IP 주소 찾기](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC664954.png)
    
    > [!NOTE]
    > DNS를 설정한 후에는 구성원 서버에 대한 RDP 세션이 닫힐 수 있습니다. 이렇게 하면 Azure 포털에서 VM이 다시 부팅됩니다.
    > 
    > 
11. **확인**을 클릭한 후 **닫기**를 클릭하여 변경 내용을 커밋합니다. 이제 VM을 **corp.contoso.com**에 연결할 수 있습니다.
12. **로컬 서버** 창으로 돌아가 **WORKGROUP** 링크를 클릭합니다.
13. **컴퓨터 이름** 섹션에서 **변경**을 클릭합니다.
14. **도메인** 확인란을 선택하고 텍스트 상자에 **corp.contoso.com**을 입력합니다. **확인**을 클릭합니다.
15. **Windows 보안** 팝업 대화 상자에서 기본 도메인 관리자 계정에 대한 자격 증명(**CORP\DomainAdmin**) 및 암호(**Contoso!0000**)를 지정합니다.
16. "corp.contoso.com 도메인 시작" 메시지가 표시되면 **확인**을 클릭합니다.
17. **닫기**를 클릭한 후 팝업 대화 상자에서 **지금 다시 시작**을 클릭합니다.
18. 파일 공유 미러링 모니터 서버와 각 SQL Server에 대해 이 단계를 반복합니다. 

### <a name="add-the-corpinstall-user-as-an-administrator-on-each-cluster-vm"></a>각 클러스터 VM에서 Corp\Install 사용자를 관리자로 추가합니다.
1. VM이 다시 시작될 때까지 기다린 후 주 도메인 컨트롤러에서 RDP 파일을 다시 시작하여 **BUILTIN\DomainAdmin** 계정을 사용하여 **sqlserver-0**에 로그인합니다.
2. **서버 관리자**에서 **도구**를 선택하고 **컴퓨터 관리**를 클릭합니다.
   
    ![컴퓨터 관리](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC784630.png)
3. **컴퓨터 관리** 창에서 **로컬 사용자 및 그룹**을 확장한 후 **그룹**을 선택합니다.
4. **Administrators** 그룹을 두 번 클릭합니다.
5. **Administrators 속성** 대화 상자에서 **추가** 단추를 클릭합니다.
6. **CORP\Install** 사용자를 입력한 후 **확인**을 클릭합니다. 자격 증명에 대한 메시지가 표시되면 **DomainAdmin** 계정과 **Contoso!0000** 암호를 사용합니다.
7. **확인**을 클릭하여 **Administrator 속성** 대화 상자를 닫습니다.
8. **sqlserver-1** 및 **cluster-fsw**에서 위의 단계를 반복합니다.

## <a name="create-the-cluster"></a>클러스터 만들기
### <a name="add-the-failover-clustering-feature-to-each-cluster-vm"></a>**장애 조치(failover) 클러스터링** 기능을 각 클러스터 VM에 추가합니다.
1. **sqlserver-0**으로 RDP합니다.
2. **서버 관리자** 대시보드에서 **역할 및 기능 추가**를 클릭합니다.
3. **할 및 기능 추가 마법사**에서 **기능** 페이지가 표시될 때까지 **다음**을 클릭합니다.
4. **장애 조치 클러스터링**을 선택합니다. 메시지가 표시되면 다른 종속 기능을 추가합니다.
   
    ![장애 조치(Failover) 클러스터링 기능을 VM에 추가](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC784631.png)
5. **다음**을 클릭한 후 **확인** 페이지에서 **설치**를 클릭합니다.
6. **장애 조치 클러스터링** 기능 설치가 완료되면 **닫기**를 클릭합니다.
7. VM에서 로그아웃합니다.
8. **sqlserver-1** 및 **cluster-fsw**에서 이 섹션의 단계를 반복합니다.

SQL Server VM이 프로비전되어 실행 중이지만 기본 옵션으로 SQL Server에 설치되었습니다.

### <a name="create-the-wsfc-cluster"></a>WSFC 클러스터 만들기
이 섹션에서는 가용성 그룹을 호스트하기 위한 WSFC 클러스터를 만듭니다. 이제 WSFC 클러스터에 있는 3개의 VM 각각에 대해 다음을 완료해야 합니다.

* Azure에서 완전히 프로비전
* VM이 도메인에 연결됨
* 로컬 관리자 그룹에 **CORP\Install** 추가됨
* 장애 조치 클러스터링 기능 추가됨

각 VM에서 이러한 모든 필수 구성 요소가 준비되어야 WSFC 클러스터에 연결할 수 있습니다.

또한 Azure 가상 네트워크는 온-프레미스 네트워크와 다르게 작동합니다. 다음 순서로 클러스터를 만들어야 합니다.

1. 하나의 노드에 단일 노드 클러스터를 만듭니다(**sqlserver-0**).
2. 클러스터 IP 주소를 **sqlsubnet**의 사용하지 않는 IP 주소로 수정합니다.
3. 클러스터 이름을 온라인 상태로 전환합니다.
4. 다른 노드(**sqlserver-1** 및 **cluster-fsw**)를 추가합니다.

다음 단계에 따라 클러스터를 구성합니다.

1. **sqlserver-0**에 대한 RDP 파일을 시작하고 도메인 계정 **CORP\Install**을 사용하여 로그인합니다.
2. **서버 관리자** 대시보드에서 **도구**를 선택한 후 **장애 조치(Failover) 클러스터 관리자**를 클릭합니다.
3. 왼쪽 창에서 **장애 조치(Failover) 클러스터 관리자**를 마우스 오른쪽 단추로 클릭하고 아래와 같이 **클러스터 만들기**를 클릭합니다.
   
    ![클러스터 만들기](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC784632.png)
4. 클러스터 만들기 마법사에서 아래 설정으로 페이지를 단계별로 진행하여 1노드 클러스터를 만듭니다.
   
   | Page | 설정 |
   | --- | --- |
   | 시작하기 전에 |기본값 사용 |
   | 서버 선택 |**서버 이름 입력**에 **sqlserver-0**을 입력하고 **추가** 클릭 |
   | 유효성 검사 경고 |**아니요. 이 클러스터에 대한 Microsoft의 지원이 필요 없으므로 유효성 검사 테스트를 실행하지 않습니다. [다음]을 클릭하면 클러스터 만들기를 계속합니다.**를 선택합니다. |
   | 클러스터 관리를 위한 액세스 지점 |**클러스터 이름**에 **Cluster1**을 입력합니다. |
   | 다음 |저장소 공간을 사용하지 않는 경우 기본값을 사용합니다. 이 표 다음의 참고 사항을 참조하세요. |

    >[AZURE.NOTE] 여러 디스크를 Storage 풀로 그룹화하는 [Storage 공간](https://technet.microsoft.com/library/hh831739)을 사용 중인 경우 **확인** 페이지에서 **클러스터에 사용할 수 있는 모든 Storage를 추가하세요.** 확인란을 선택 취소해야 합니다. 이 옵션을 선택 취소하지 않으면 클러스터링 프로세스 중에 가상 디스크가 분리됩니다. 그 결과, 저장소 공간이 클러스터에서 제거되고 PowerShell을 사용하여 다시 연결할 때까지 디스크 관리자 또는 탐색기에 표시되지 않습니다.

    이제 클러스터가 만들어졌으므로 구성을 확인하고 나머지 노드를 추가합니다. 

1. 가운데 창에서 **클러스터 코어 리소스** 섹션으로 아래로 스크롤하고 **이름: Clutser1** 세부 정보를 확장합니다. **이름** 및 **IP 주소** 리소스가 **실패** 상태에 모두 표시됩니다. 클러스터에 컴퓨터 자체와 같은 IP 주소가 할당되어 주소가 중복되므로 IP 주소 리소스는 온라인 상태로 전환할 수 없습니다.
2. 오류가 발생한 **IP 주소** 리소스를 마우스 오른쪽 단추로 클릭하고 **속성**을 클릭합니다.
   
    ![클러스터 속성](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC784633.png)
3. **고정 IP 주소** 를 선택하고 주소 텍스트 상자에 subnet-2에서 사용 가능한 주소를 지정합니다. 그런 다음 **확인**을 클릭합니다.
4. **클러스터 코어 리소스** 섹션에서 **이름: Cluster1**을 마우스 오른쪽 단추로 클릭하고 **온라인 상태로 전환**을 클릭합니다. 그런 다음 두 리소스가 모두 온라인 상태로 전환될 때까지 기다립니다. 클러스터 이름 리소스가 온라인 상태가 되면 새 AD 컴퓨터 계정으로 DC 서버를 업데이트합니다. 이 AD 계정을 사용하여 나중에 가용성 그룹 클러스터형 서비스를 실행합니다.
5. 마지막으로 클러스터에 나머지 노드를 추가합니다. 아래와 같이 브라우저 트리에서 **Cluster1.corp.contoso.com**을 마우스 오른쪽 단추로 클릭하고 **노드 추가**를 클릭합니다.
   
    ![클러스터에 노드 추가](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC784634.png)
6. **노드 추가 마법사**에서 **다음**을 클릭합니다. **서버 선택** 페이지에서 **서버 이름 입력**에 서버 이름을 입력한 후 **추가**를 클릭하여 **sqlserver-1** 및 **cluster-fsw**를 목록에 추가합니다. 완료하면 **다음**을 클릭합니다.
7. **유효성 검사 경고** 페이지에서 **아니요**(유효성 검사 테스트를 수행할 프로덕션 시나리오에서)를 클릭합니다. 그런 후 **다음**을 클릭합니다.
8. **확인** 페이지에서 **다음**을 클릭하여 노드를 추가합니다.
   
   > [!WARNING]
   > 여러 디스크를 저장소 풀로 그룹화하는 [저장소 공간](https://technet.microsoft.com/library/hh831739)을 사용 중인 경우 **클러스터에 사용할 수 있는 모든 저장소를 추가하세요** 확인란을 선택 취소해야 합니다. 이 옵션을 선택 취소하지 않으면 가상 디스크가 클러스터 프로세스 중에 분리됩니다. 그 결과, 저장소 공간이 클러스터에서 제거되고 PowerShell을 사용하여 다시 연결할 때까지 디스크 관리자 또는 탐색기에 표시되지 않습니다.
   > 
   > 
9. 클러스터에 노드가 추가되면 **마침**을 클릭합니다. 이제 장애 조치(Failover) 클러스터 관리자에 3개의 노드가 포함된 클러스터가 표시되고 **노드** 컨테이너에 목록으로 표시됩니다.
10. 원격 데스크톱 세션에서 로그아웃합니다.

## <a name="configure-availability-groups"></a>가용성 그룹 구성
이 섹션에서는 **sqlserver-0** 및 **sqlserver-1** 모두에 대해 다음을 수행합니다.

* 기본 SQL Server 인스턴스에 sysadmin 역할로 **CORP\Install** 추가
* SQL Server, 데이터베이스 미러링 끝점 및 Azure 부하 분산 장치 프로브 포트에 대한 SQL Server 원격 액세스를 위해 방화벽 열기 
* 가용성 그룹 기능 사용
* SQL Server 서비스 계정을 **CORP\SQLSvc1** 및 **CORP\SQLSvc2**로 각각 변경

이 작업은 순서와 관계없이 수행할 수 있습니다. 하지만 아래 단계는 순서대로 진행합니다. **sqlserver-0** 및 **sqlserver-1** 모두에 대해 단계를 수행합니다.

### <a name="add-installation-account-as-sysadmin-fixed-server-role-on-each-sql-server"></a>각 SQL Server에서 sysadmin 고정 서버 역할로 설치 계정 추가
1. VM에 대한 원격 데스크톱 세션에서 로그아웃하지 않은 경우 지금 로그아웃합니다.
2. **sqlserver-0** 및 **sqlserver-1**에 대해 RDP 파일을 시작하고 **BUILTIN\DomainAdmin**으로 로그인합니다.
3. **SQL Server Management Studio**를 시작하고 기본 SQL Server 인스턴스에 **sysadmin** 역할로 **CORP\Install**을 추가합니다. **개체 탐색기**에서 **로그인**을 마우스 오른쪽 단추로 클릭하고 **새 로그인**을 클릭합니다.
4. **로그인 이름**에 **CORP\Install**을 입력합니다.
5. **서버 역할** 페이지에서 **sysadmin**을 선택합니다. 그런 다음 **확인**을 클릭합니다. 로그인이 생성되면 **로그인** in **로그인**을 참조하세요.

### <a name="open-the-firewall-for-remote-access-to-sql-server-and-the-probe-port-on-each-sql-server"></a>SQL Server와 각 SQL Server의 프로브 포트에 대한 원격 액세스를 위해 방화벽을 엽니다.
이 솔루션은 각 SQL Server에 대해 하나씩 두 개의 방화벽 규칙이 필요합니다. 첫 번째 규칙은 SQL Server에 대한 인바운드 액세스를 제공하고 두 번째 규칙은 부하 분산 장치 및 수신기에 대한 인바운드 액세스를 제공합니다. 

1. 다음으로 SQL Server에 대한 새 방화벽 규칙을 만듭니다. **시작** 화면에서 **고급 보안이 포함된 Windows 방화벽**을 시작합니다.
2. 왼쪽 창에서 **인바운드 규칙**을 선택합니다. 오른쪽 창에서 **새 규칙**을 클릭합니다.
3. **규칙 종류** 페이지에서 **프로그램**을 선택한 후 **다음**을 클릭합니다.
4. **프로그램** 페이지에서 **다음 프로그램 경로**를 선택하고 텍스트 상자에 **%ProgramFiles%\Microsoft SQL Server\MSSQL12.MSSQLSERVER\MSSQL\Binn\sqlservr.exe**를 입력합니다(이 지침을 따르지만 SQL Server 2012를 사용하는 경우 SQL Server 디렉터리는 **MSSQL11.MSSQLSERVER**임). 그런 후 **Next**를 클릭합니다.
5. **작업** 페이지에서 **연결 허용**을 선택한 상태로 유지하고 **다음**을 클릭합니다.
6. **프로필** 페이지에서 기본 설정을 그대로 적용하고 **다음**을 클릭합니다.
7. **이름** 페이지에서 **이름** 텍스트 상자에 **SQL Server(프로그램 규칙)**와 같은 규칙 이름을 지정하고 **마침**을 클릭합니다.
8. 프로브 포트에 대한 추가 인바운드 방화벽 규칙을 만듭니다. 이 규칙은 이 자습서의 목적을 위해 만들어진 TCP 포트 59999에 대한 인바운드 규칙입니다. 규칙 이름을 **SQL Server 수신기**로 지정합니다.

두 SQL Server에서 모든 단계를 완료합니다.

### <a name="enable-availability-groups-feature-on-each-sql-server"></a>각 SQL Server에서 가용성 그룹 기능을 사용하도록 설정
두 SQL Server에서 다음 단계를 수행합니다. 

1. 다음으로, **AlwaysOn 가용성 그룹** 기능을 사용하도록 설정합니다. **시작** 화면에서 **SQL Server 구성 관리자**를 시작합니다.
2. 브라우저 트리에서 **SQL Server 서비스**를 클릭하고 **SQL Server (MSSQLSERVER)** 서비스를 마우스 오른쪽 단추로 클릭한 다음 **속성**을 클릭합니다.
3. **AlwaysOn 고가용성** 탭을 클릭한 후 **AlwaysOn 가용성 그룹 사용**을 선택한 후에 아래와 같이 **적용**을 클릭합니다. 팝업 대화 상자에서 **확인** 을 클릭하고 아직 속성 창을 닫지 마세요. 서비스 계정을 변경한 후에 SQL Server 서비스를 다시 시작합니다.
   
    ![AlwaysOn 가용성 그룹 사용](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC665520.gif)

### <a name="set-the-sql-server-service-account-on-each-sql-server"></a>각 SQL Server에서 SQL Server 서비스 계정 설정
두 SQL Server에서 다음 단계를 수행합니다.

1. 다음으로, SQL Server 서비스 계정을 변경합니다. **로그온** 탭을 클릭한 후 **계정 이름**에 **CORP\SQLSvc1**(**sqlserver-0**인 경우) 또는 **CORP\SQLSvc2**(**sqlserver-1**인 경우)를 입력한 후 암호를 입력 및 확인한 후 **확인**을 클릭합니다.
2. 팝업 창에서 **예** 를 클릭하여 SQL Server 서비스를 다시 시작합니다. SQL Server 서비스가 다시 시작되면 속성 창에서 변경한 내용이 적용됩니다.
3. VM에서 로그아웃합니다.

### <a name="create-the-availability-group"></a>가용성 그룹 만들기
이제 가용성 그룹을 구성할 준비가 되었습니다. 다음은 수행할 사항을 간략히 설명합니다.

* **sqlserver-0**에 새 데이터베이스(**MyDB1**) 만들기
* 데이터베이스의 전체 백업 및 트랜잭션 로그 백업 수행
* **NORECOVERY** 옵션으로 전체 및 로그 백업을 **sqlserver-1**로 복원
* 동기 커밋, 자동 장애 조치(failover) 및 읽을 수 있는 보조 복제본으로 가용성 그룹 만들기(**AG1**)

### <a name="create-the-mydb1-database-on-sqlserver-0"></a>sqlserver-0에 MyDB1 데이터베이스 만들기:
1. **sqlserver-0** 및 **sqlserver-1**에 대한 원격 데스크톱 세션에서 아직 로그아웃하지 않은 경우 지금 로그아웃합니다.
2. **sqlserver-0**에 대한 RDP 파일을 시작하고 **CORP\Install**로 로그인합니다.
3. **파일 탐색기**에서 ***C:\** 아래에 **backup**이라는 디렉터리를 만듭니다. 이 디렉터리는 데이터베이스를 백업 및 복원하는 데 사용합니다.
4. 새 디렉터리를 마우스 오른쪽 단추로 클릭하고 **공유 대상**을 가리킨 후 아래와 같이 **특정 사용자**를 클릭합니다.
   
    ![백업 폴더 만들기](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC665521.gif)
5. **CORP\SQLSvc1**을 추가하고 **읽기/쓰기** 권한을 부여한 후 **CORP\SQLSvc2**를 추가하고 **읽기/쓰기** 권한을 부여한 후 아래와 같이 **공유**를 클릭합니다. 파일 공유 프로세스가 완료되면 **완료**를 클릭합니다.
   
    ![백업 폴더에 대한 권한 부여](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC665522.gif)
6. 다음으로 데이터베이스를 만듭니다.. **시작** 메뉴에서 **SQL Server Management Studio**를 시작한 후 **연결**을 클릭하여 기본 SQL Server 인스턴스에 연결합니다.
7. **개체 탐색기**에서 **데이터베이스**를 마우스 오른쪽 단추로 클릭하고 **새 데이터베이스**를 클릭합니다.
8. **데이터베이스 이름**에 **MyDB1**을 입력하고 **확인**을 클릭합니다.

### <a name="take-a-full-backup-of-mydb1-and-restore-it-on-sqlserver-1"></a>MyDB1의 전체 백업 수행 및 sqlserver-1에 복원:
1. 다음으로 데이터베이스의 전체 백업을 수행합니다. **개체 탐색기**에서 **데이터베이스**를 확장한 후 **MyDB1**을 마우스 오른쪽 단추로 클릭한 다음 **작업**을 가리키고 **백업**을 클릭합니다.
2. **원본** 섹션에서 **백업 유형**을 **전체**로 설정된 상태로 유지합니다. **대상** 섹션에서 **제거**를 클릭하여 백업 파일의 기본 파일 경로를 제거합니다.
3. **대상** 섹션에서 **추가**를 클릭합니다.
4. **파일 이름** 텍스트 상자에 **\\\\sqlserver-0\backup\MyDB1.bak**를 입력합니다. 그런 다음 **확인**을 클릭한 후 **확인**을 한 번 더 클릭하여 데이터베이스를 백업합니다. 백업 작업이 완료되면 **확인** 을 한 번 더 클릭하여 대화 상자를 닫습니다.
5. 다음으로 데이터베이스의 트랜잭션 로그 백업을 수행합니다. **개체 탐색기**에서 **데이터베이스**를 확장한 후 **MyDB1**을 마우스 오른쪽 단추로 클릭한 다음 **작업**을 가리키고 **백업**을 클릭합니다.
6. **백업** 유형에서 **트랜잭션 로그**를 선택합니다. **대상** 파일 경로를 앞에서 지정한 경로로 설정된 상태를 유지하고 **확인**을 클릭합니다. 백업 작업이 완료되면 **확인** 을 한 번 더 클릭합니다.
7. 다음으로 **sqlserver-1**에서 전체 및 트랜잭션 로그 백업을 복원합니다. **sqlserver-1**에 대한 RDP 파일을 시작하고 **CORP\Install**로 로그인합니다. **sqlserver-0** 에 대한 원격 데스크톱 세션을 열린 상태로 둡니다.
8. **시작** 메뉴에서 **SQL Server Management Studio**를 시작한 후 **연결**을 클릭하여 기본 SQL Server 인스턴스에 연결합니다.
9. **개체 탐색기**에서 **데이터베이스**를 마우스 오른쪽 단추로 클릭하고 **데이터베이스 복원**을 클릭합니다.
10. **원본** 섹션에서 **장치**를 선택하고 **…**  단추를 선택합니다.
11. **백업 장치 선택**에서 **추가**를 클릭합니다.
12. 백업 파일 위치에서 **\\\\sqlserver-0\backup**을 입력한 후 새로 고침을 클릭한 다음 MyDB1.bak를 선택하고 확인을 클릭한 후 확인을 한 번 더 클릭합니다. 그러면 복원할 백업 세트 창에 전체 백업 및 로그 백업이 표시됩니다.
13. 옵션 페이지로 이동한 후 복구 상태에서 RESTORE WITH NORECOVERY를 선택하고 확인을 클릭하여 데이터베이스를 복원합니다. 복원 작업이 완료되면 확인을 클릭합니다.

### <a name="create-the-availability-group"></a>가용성 그룹 만들기:
1. **sqlserver-0**에 대한 원격 데스크톱 세션으로 돌아갑니다. SSMS의 **개체 탐색기** i에서 **AlwaysOn 고가용성**을 마우스 오른쪽 단추로 클릭하고 아래와 같이 **새 가용성 그룹 마법사**를 클릭합니다.
   
    ![새 가용성 그룹 마법사 시작](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC665523.gif)
2. **소개** 페이지에서 **다음**을 클릭합니다. **가용성 그룹 이름 지정** 페이지에서, **가용성 그룹 이름**에 **AG1**을 입력한 후에 **다음**을 한 번 더 클릭합니다.
   
    ![새 AG 마법사, AG 이름 지정](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC665524.gif)
3. **데이터베이스 선택** 페이지에서 **MyDB1**을 선택하고 **다음**을 클릭합니다. 원하는 주 복제본에서 전체 백업을 하나 이상 생성했으므로, 이러한 데이터베이스는 가용성 그룹의 필수 구성 요소를 충족합니다.
   
    ![새 AG 마법사, 데이터베이스 선택](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC665525.gif)
4. **복제본 지정** 페이지에서 **복제본 추가**를 클릭합니다.
   
    ![새 AG 마법사, 복제본 지정](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC665526.png)
5. **서버에 연결** 대화 상자가 나타납니다. **서버 이름**에 **sqlserver-1**을 입력한 후 **연결**을 클릭합니다.
   
    ![새 AG 마법사, 서버에 연결](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC665527.png)
6. **복제본 지정** 페이지로 돌아가면 이제 **가용성 복제본**에 **sqlserver-1**이 표시됩니다. 아래와 같이 복제본을 구성합니다. 
   
    ![새 AG 마법사, 복제본 지정(전체)](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC665528.png)
7. **끝점** 을 클릭하여 이 가용성 그룹에 사용될 데이터베이스 미러링 끝점을 봅니다. SQL Server의 각 인스턴스에는 하나의 데이터베이스 미러링 끝점이 있어야 합니다. 이 끝점에 대해 마법사가 지정하는 TCP 포트를 적어둡니다. 이 TCP 포트에 대해 각 서버에서 인바운드 방화벽 규칙을 만듭니다.
   
    작업을 마쳤으면 **다음**을 클릭합니다.
8. **초기 데이터 동기화 선택** 페이지에서 **조인만**을 선택하고 **다음**을 클릭합니다. **sqlserver-0**에서 전체 및 트랜잭션 백업을 수행하고 이를 **sqlserver-1**에서 복원할 때 이미 데이터 동기화를 수동으로 수행했습니다. 대신, 데이터베이스에서 백업 및 복원 작업을 수행하지 않고 **전체** 를 선택하여 새 가용성 그룹 마법사가 데이터 동기화를 자동으로 수행하도록 할 수 있습니다. 그러나 일부 기업에서 사용하는 대형 데이터베이스의 경우에는 이 방법을 수행하지 않는 것이 좋습니다.
   
    ![새 AG 마법사, 초기 데이터 동기화 선택](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC665529.png)
9. **유효성 검사** 페이지에서 **다음**을 클릭합니다. 이 페이지는 다음과 유사하게 표시되어야 합니다. 가용성 그룹 수신기가 구성되어 있지 않으므로 수신기 구성에 대한 경고가 표시됩니다. 이 자습서에서는 수신기를 구성하지 않으므로 이 경고를 무시해도 됩니다. 이 자습서에서는 수신기를 나중에 만듭니다.
   
    ![새 AG 마법사, 유효성 검사](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC665530.gif)
10. **요약** 페이지에서 **마침**을 클릭한 후 마법사에서 새 가용성 그룹을 구성하는 동안 기다립니다. **진행률** 페이지에서 **자세한 내용**을 클릭하여 자세한 진행 상태를 확인할 수 있습니다. 마법사가 완료되면 아래와 같이 **결과** 페이지를 검토하여 가용성 그룹이 올바르게 만들어졌는지 확인한 후 **닫기**를 클릭하여 마법사를 종료합니다.
    
     ![새 AG 마법사, 결과](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC665531.png)
11. **개체 탐색기**에서 **AlwaysOn 고가용성**을 확장하고 **가용성 그룹**을 확장합니다. 이 컨테이너에 새 가용성 그룹이 표시됩니다. **AG1(기본)**을 마우스 오른쪽 단추로 클릭하고 **대시보드 표시**를 클릭합니다.
    
     ![AG 대시보드 표시](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC665532.png)
12. **AlwaysOn 대시보드** 는 다음과 유사하게 표시되어야 합니다. 복제본, 각 복제본의 장애 조치(failover) 모드 및 동기화 상태를 볼 수 있습니다.
    
     ![AG 대시보드](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC665533.png)
13. **서버 관리자**로 돌아가서 **도구**를 선택한 후 **장애 조치(Failover) 클러스터 관리자**를 시작합니다.
14. **Cluster1.corp.contoso.com**과 **서비스 및 응용 프로그램**을 차례로 확장합니다. **역할**을 선택하고 **AG1** 가용성 그룹 역할이 만들어진 것을 확인합니다. AG1에는 수신기를 구성하지 않았으므로 데이터베이스 클라이언트가 가용성 그룹에 연결할 수 있는 IP 주소가 없습니다. 읽기/쓰기 작업을 위해 주 노드에 직접 연결하고 읽기 전용 쿼리를 위해 보조 노드에 직접 연결할 수 있습니다.
    
    ![장애 조치(Failover) 클러스터 관리자에서 AG](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC665534.png)
    
    > [!WARNING]
    > 장애 조치(Failover) 클러스터 관리자에서 가용성 그룹으로 장애 조치를 시도하지 마세요. 모든 장애 조치(Failover) 작업은 SSMS의 **AlwaysOn 대시보드** 에서 수행해야 합니다. 자세한 내용은 [가용성 그룹에서 WSFC 장애 조치(Failover) 클러스터 관리자 사용에 대한 제한 사항](https://msdn.microsoft.com/library/ff929171.aspx)을 참조하세요.
    > 
    > 

## <a name="configure-internal-load-balancer"></a>내부 부하 분산 장치 구성
가용성 그룹에 직접 연결하려면 부하 분산 장치를 구성해야 합니다. 부하 분산 장치는 수신기 IP 주소 및 프로브 포트에 바인딩된 VM으로 클라이언트 트래픽을 보냅니다. 이 자습서는 내부 부하 분산 장치 또는 ILB를 사용합니다. ILB는 동일한 가상 네트워크 내의 트래픽이 SQL Server에 연결할 수 있도록 합니다. 인터넷을 통해 SQL Server에 연결해야 하는 응용 프로그램에는 인터넷 연결 또는 외부 부하 분산 장치가 필요합니다. 자세한 내용은 [Azure 부하 분산 장치 개요](../load-balancer/load-balancer-overview.md)를 참조하세요.

> [!NOTE]
> 이 자습서에서는 ILB IP 주소 하나로 단일 수신기를 만드는 방법을 보여줍니다. 하나 이상의 eIP 주소를 사용하여 하나 이상의 수신기를 만드는 방법은 [가용성 그룹 수신기 및 부하 분산 장치 만들기 | Azure](virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 참조하세요. 
> 
> 

### <a name="create-the-load-balancer-in-azure"></a>Azure에서 부하 분산 장치 만들기
1. Azure Portal에서 **SQL-HA-RG**로 이동하여 **+ 추가**를 클릭합니다.
2. **부하 분산 장치**를 검색합니다. Microsoft에서 게시한 부하 분산 장치를 선택하고 **만들기**를 클릭합니다.
3. 부하 분산 장치에 대한 다음 매개 변수를 구성합니다.

| 설정 | 필드 |
| --- | --- |
| **Name** |sqlLB |
| **구성표** |내부 |
| **가상 네트워크** |autoHAVNET |
| **서브넷** |subnet-2. 클러스터 리소스에서 수신기에 대해 설정할 IP 주소입니다. |
| **IP 주소 할당** |정적 |
| **IP 주소** |subnet-2에서 사용 가능한 주소를 사용합니다. |
| **구독** |이 솔루션의 다른 모든 리소스와 동일한 구독을 사용합니다. |
| **위치** |이 솔루션의 다른 모든 리소스와 동일한 위치를 사용합니다. |

**만들기**를 클릭합니다.

부하 분산 장치에서 다음 설정을 확인합니다.

| 설정 | 필드 |
| --- | --- |
| **백 엔드 풀** 이름 |sqlLBBE |
| **SQLLBBE 가용성 집합** |sqlAvailabilitySet |
| **SQLLBBE 가상 컴퓨터** |sqlserver-0, sqlserver-1 |
| **SQLLBBE 다음에서 사용** |SQLAlwaysOnEndPointListener |
| **프로브** 이름 |SQLAlwaysOnEndPointProbe |
| **프로브 프로토콜** |TCP |
| **프로브 포트** |59999 - 사용되지 않는 모든 포트를 사용할 수 있습니다. |
| **프로브 간격** |5 |
| **프로브 비정상 임계값** |2 |
| **프로브 다음에서 사용** |SQLAlwaysOnEndPointListener |
| **부하 분산 규칙** 이름 |SQLAlwaysOnEndPointListener |
| **부하 분산 규칙 프로토콜** |TCP |
| **부하 분산 규칙 포트** |1433 * |
| **부하 분산 규칙 백 엔드 포트** |1433 |
| **부하 분산 규칙 프로브** |SQLAlwaysOnEndPointProbe |
| **부하 분산 규칙 세션 지속성** |없음 |
| **부하 분산 규칙 유휴 제한 시간** |4 |
| **부하 분산 규칙 부동 IP(Direct Server Return)** |사용 |

> * 1433은 기본 SQL Server 포트입니다. 기본 인스턴스의 프런트 엔드 포트에 사용합니다. 여러 가용성 그룹이 필요한 경우 각 가용성 그룹에 대해 추가 IP 주소를 만들어야 합니다. 각 가용성 그룹에는 자체 프런트 엔드 포트가 필요합니다. [가용성 그룹 수신기 및 부하 분산 장치 만들기 | Azure](virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 참조하세요.
> 
> [!NOTE]
> 부하 분산 규칙을 만들 때 DSR(Direct Server Return)을 사용하도록 설정해야 합니다.
> 
> 

부하 분산 장치를 구성한 후 장애 조치(failover) 클러스터에서 수신기를 구성합니다. 

## <a name="configure-the-listener"></a>수신기 구성
다음에 수행할 작업은 장애 조치(failover) 클러스터에서 가용성 그룹 수신기를 구성하는 것입니다. 

1. ad-primary-dc에서 sqlserver-0으로 SQL Server에 RDP합니다.
2. 장애 조치(failover) 클러스터 관리자에 표시된 클러스터 네트워크의 이름을 적어둡니다. **장애 조치(failover) 클러스터 관리자**에서 클러스터 네트워크 이름을 확인하려면 왼쪽 창에서 **네트워크**를 클릭합니다. 이 이름은 PowerShell 스크립트에서 `$ClusterNetworkName` 변수에 사용됩니다.
3. 장애 조치(failover) 클러스터 관리자에서 클러스터 이름을 확장하고 **역할**을 클릭합니다.
4. **역할**에서 가용성 그룹 이름을 마우스 오른쪽 단추로 클릭한 다음 **리소스 추가** > **클라이언트 액세스 지점**을 선택합니다. 
5. **이름**에 **aglistener**를 입력합니다. **다음**을 두 번 클릭한 후 **마침**을 클릭합니다. 현재 온라인 상태에서 수신기 또는 리소스를 가져오지 마세요.
6. **리소스** 탭을 클릭한 다음 방금 만든 클라이언트 액세스 지점을 확장합니다. IP 리소스를 마우스 오른쪽 단추로 클릭하고 속성을 클릭합니다. IP 주소의 이름을 적어둡니다. 이 이름을 PowerShell 스크립트에서 `$IPResourceName` 변수에 사용합니다.
7. **IP 주소**에서 **고정 IP 주소**를 클릭하고 Azure Portal에서 **sqlLB** 부하 분산 장치에 사용된 주소와 동일한 주소로 고정 IP 주소를 설정합니다. 이 동일한 IP 주소를 PowerShell 스크립트에서 `$ILBIP` 변수에 사용합니다. 
8. 이 주소에 대해 NetBIOS를 사용하지 않도록 설정하고 **확인**을 클릭합니다. 
9. 현재 주 복제본을 호스트하는 클러스터 노드에서 관리자 권한으로 PowerShell ISE를 열고 새 스크립트에 다음 명령을 붙여 넣습니다.
   
    ```
    $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
    $IPResourceName = "<IPResourceName>" # the IP Address resource name
    $ILBIP = "<X.X.X.X>" # the IP Address of the Internal Load Balancer (ILB). This is the static IP address for the load balancer you configured in the Azure portal.
   [int]$ProbePort = <nnnnn> # In this sample we've using 59999 for the probe port. 
   
    Import-Module FailoverClusters
   
    Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
    ```
10. 변수를 업데이트하고 PowerShell 스크립트를 실행하여 새 수신기에 대한 IP 주소와 포트를 구성합니다.
11. **장애 조치(failover) 클러스터 관리자**에서 가용성 그룹 리소스를 마우스 오른쪽 단추로 클릭하고 **속성**을 클릭합니다. **종속성** 탭에서 리소스 그룹을 수신기 네트워크 이름의 종속 항목으로 설정합니다. 
12. 수신기 포트 속성을 1433으로 설정합니다. 이렇게 하려면 SQL Server Management Studio를 열고 가용성 그룹 수신기를 마우스 오른쪽 단추로 클릭한 다음 속성을 선택합니다. **포트** 를 1433으로 설정합니다. SQL Server에 대해 구성한 것과 동일한 포트 번호를 사용해야 합니다. 
13. 이제 수신기를 온라인 상태로 전환할 수 있습니다. 수신기 이름을 마우스 오른쪽 단추로 클릭하고 **온라인 상태로 전환**을 클릭합니다.

### <a name="test-the-connection-to-the-listener"></a>수신기에 대한 연결 테스트
연결을 테스트하려면

1. 복제본을 소유하지 않은 SQL Server로 RDP합니다.
2. sqlcmd 유틸리티를 사용하여 연결을 테스트합니다. 예를 들어 다음 스크립트는 Windows 인증을 사용하는 수신기를 통해 주 복제본에 대한 sqlcmd 연결을 설정합니다.
   
    ```
    sqlcmd -S "<listenerName>" -E
    ```
   
   수신기가 1433 이외의 포트를 사용하는 경우 테스트에 포트 번호를 지정해야 합니다. 예를 들어 다음 쿼리는 포트 1435를 사용하여 수신기 이름에 대한 연결을 테스트합니다.
   
    ```
    sqlcmd -S "<listenerName>",1435 -E
    ```

## <a name="next-steps"></a>다음 단계
Azure에서 SQL Server를 사용하는 방법에 대한 기타 정보는 [Azure 가상 컴퓨터의 SQL Server](virtual-machines-windows-sql-server-iaas-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 참조하세요.




<!--HONumber=Nov16_HO3-->


