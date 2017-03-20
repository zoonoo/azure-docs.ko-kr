---
title: "SQL Server 가용성 그룹 - Azure Virtual Machines - 필수 구성 요소 | Microsoft Docs"
description: "이 자습서에서는 Azure VM에서 SQL Server Always On 가용성 그룹을 만들기 위한 필수 구성 요소를 구성하는 방법을 보여 줍니다."
services: virtual-machines
documentationCenter: na
authors: MikeRayMSFT
manager: jhubbard
editor: monicar
tags: azure-service-management
ms.assetid: c492db4c-3faa-4645-849f-5a1a663be55a
ms.service: virtual-machines-sql
ms.devlang: na
ms.custom: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/10/2017
ms.author: mikeray
translationtype: Human Translation
ms.sourcegitcommit: 4326cc342088ff16a72b8c460245bda1f2cd17c9
ms.openlocfilehash: 3e0c58af3566ea443efaa012495e5b736fafb46d
ms.lasthandoff: 01/14/2017


---

# <a name="complete-prerequisites-for-creating-always-on-availability-groups-in-azure-virtual-machines"></a>Azure Virtual Machines에 Always On 가용성 그룹을 만들기 위한 필수 구성 요소 완료

이 자습서에서는 [Azure Virtual Machines에 SQL Server Always On 가용성 그룹](virtual-machines-windows-portal-sql-availability-group-tutorial.md)을 만들기 위해 필수 구성 요소를 완료하는 방법을 보여 줍니다. 필수 구성 요소를 완료하면 단일 리소스 그룹에 도메인 컨트롤러, 두 개의 SQL Server 및 감시 서버를 갖습니다.

**예상 시간**: 필수 구성 요소를 완료하는 데 시간이 걸릴 수 있습니다. 이 시간의 대부분은 가상 컴퓨터를 만드는 데 소요됩니다.

다이어그램은 자습서에서 빌드할 작업을 나타냅니다.

![가용성 그룹](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/00-EndstateSampleNoELB.png)

## <a name="review-availability-group-documentation"></a>가용성 그룹 설명서 검토

이 자습서는 사용자가 SQL Server Always On 가용성 그룹을 기본적으로 이해하고 있다고 가정합니다. 이 기술에 익숙하지 않은 경우 [Always On 가용성 그룹 개요(SQL Server)](http://msdn.microsoft.com/library/ff877884.aspx)를 참조하세요.


## <a name="create-an-azure-account"></a>Azure 계정 만들기
* Azure 계정이 필요합니다. [무료 Azure 계정을 열거나](/pricing/free-trial/?WT.mc_id=A261C142F) 또는 [Visual Studio 구독자 혜택을 활성화](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)할 수 있습니다. 

## <a name="create-resource-group"></a>리소스 그룹 만들기
1. [Azure 포털](http://portal.azure.com)에 로그인합니다. 
2. **+**를 클릭하여 포털에서 새 개체를 만듭니다.

   ![새로 만들기](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/01-portalplus.png)

1. **Marketplace** 검색 창에 **리소스 그룹**을 입력합니다.
   
   ![리소스 그룹](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/01-resourcegroupsymbol.png)
1. **리소스 그룹**을 클릭합니다.
4. **만들기**를 클릭합니다. 
5. **리소스 그룹** 블레이드의 **리소스 그룹 이름**에 리소스 그룹의 이름을 입력합니다. 예를 들어 **sql-ha-rg**를 입력합니다.
6. 여러 Azure 구독이 있는 경우 해당 구독이 가용성 그룹을 만들려는 Azure 구독인지 확인합니다. 
7. 위치를 선택합니다. 위치는 가용성 그룹을 만들려는 Azure 지역입니다. 이 자습서에서는 모든 리소스를 한 Azure 위치에 빌드합니다. 
8. **대시보드에 고정** 이 선택되어 있는지 확인합니다. 이 선택적 설정은 Azure 포털 대시보드에 리소스 그룹에 대한 바로 가기를 배치합니다.

   ![리소스 그룹](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/01-resourcegroup.png)

9. **만들기** 를 클릭하여 리소스 그룹을 만듭니다.

리소스 그룹이 만들어지고 리소스 그룹에 대한 바로 가기가 포털에 고정됩니다.

## <a name="create-network-and-subnets"></a>네트워크 및 서브넷 만들기
다음 단계는 Azure 리소스 그룹에 네트워크 및 서브넷을 만드는 것입니다.

이 솔루션은 하나의 가상 네트워크를 두 개의 서브넷과 함께 사용합니다. [가상 네트워크 개요](../../../virtual-network/virtual-networks-overview.md) 에서는 Azure의 네트워크에 대한 자세한 정보를 제공합니다.

가상 네트워크를 만들려면

1. 리소스 그룹의 Azure Portal에서 **+ 추가**를 클릭합니다. **모두** 블레이드가 열립니다. 
   
   ![새 항목](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/02-newiteminrg.png)
2. **가상 네트워크**를 검색합니다.
   
     ![가상 네트워크 검색](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/04-findvirtualnetwork.png)
3. **가상 네트워크**를 클릭합니다.
4. **가상 네트워크** 블레이드에서 **Resource Manager** 배포 모델을 클릭하고 **만들기**를 클릭합니다.
   
   다음 표에서는 가상 네트워크에 대한 설정을 보여 줍니다.
   
   | **필드** | 값 |
   | --- | --- |
   | **Name** |autoHAVNET |
   | **주소 공간** |10.33.0.0/24 |
   | **서브넷 이름** |관리자 |
   | **서브넷 주소 범위** |10.33.0.0/29 |
   | **구독** |사용하려는 구독을 지정합니다. 하나의 구독만 있는 경우 **구독**은 비어 있습니다. |
   | **위치**: |Azure 위치를 지정합니다. |
   
   주소 공간 및 서브넷 주소 범위는 표와 다를 수 있습니다. 구독에 따라 포털은 사용 가능한 주소 공간 및 해당 서브넷 주소 범위를 제안합니다. 사용할 수 있는 주소 공간이 충분하지 않으면 다른 구독을 사용하세요. 

   예제에서는 서브넷 이름 **Admin**을 사용합니다. 이 서브넷은 도메인 컨트롤러용입니다. 

6. **만들기**를 클릭합니다.
   
   ![가상 네트워크 구성](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/06-configurevirtualnetwork.png)

포털 대시보드가 반환되고 새 네트워크가 만들어지는 시점을 사용자에게 알립니다.

### <a name="create-a-second-subnet"></a>두 번째 서브넷 만들기
새 가상 네트워크에는 **Admin**이라는 하나의 서브넷이 있습니다. 도메인 컨트롤러는 이 서브넷을 사용합니다. SQL Server는 **SQL**이라는 두 번째 서브넷을 사용합니다. 이 서브넷을 구성하려면:

1. 대시보드에서 사용자가 만든 리소스 그룹인 **SQL-HA-RG**를 클릭합니다. 리소스 그룹의 **리소스**에서 네트워크를 찾습니다.
   
    **SQL-HA-RG**가 표시되지 않으면 **리소스 그룹**을 클릭하고 리소스 그룹 이름으로 필터링하여 찾습니다.
2. 리소스 목록에서 **autoHAVNET** 을 클릭합니다. 네트워크 구성 블레이드가 열립니다.
3. **autoHAVNET** 가상 네트워크에서 **모든 설정**을 클릭합니다.
4. **설정** 블레이드에서 **서브넷**을 클릭합니다.
   
    이미 만들어진 서브넷을 확인합니다. 
   
   ![가상 네트워크 구성](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/07-addsubnet.png)
5. 두 번째 서브넷을 만듭니다. **+ 서브넷**을 클릭합니다. 
6. **서브넷 추가** 블레이드에서 **이름** 아래에 **sqlsubnet**을 입력하여 서브넷을 구성합니다. 유효한 **주소 범위**가 자동으로 지정됩니다. 이 주소 범위에 최소 10개의 주소가 있는지 확인합니다. 프로덕션 환경에서는 더 많은 주소가 필요할 수 있습니다. 
7. **확인**을 클릭합니다.
   
    ![가상 네트워크 구성](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/08-configuresubnet.png)

다음 표에서는 네트워크 구성 설정을 요약합니다.

| **필드** | 값 |
| --- | --- |
| **Name** |**autoHAVNET** |
| **주소 공간** |구독에서 사용 가능한 주소 공간에 따라 달라집니다. 일반적인 값은 10.0.0.0/16입니다. |
| **서브넷 이름** |**admin** |
| **서브넷 주소 범위** |구독에서 사용 가능한 주소 범위에 따라 달라집니다. 일반적인 값은 10.0.0.0/24입니다. |
| **서브넷 이름** |**sqlsubnet** |
| **서브넷 주소 범위** |구독에서 사용 가능한 주소 범위에 따라 달라집니다. 일반적인 값은 10.0.1.0/24입니다. |
| **구독** |사용하려는 구독을 지정합니다. |
| **리소스 그룹** |**SQL-HA-RG** |
| **위치** |리소스 그룹에 대해 선택한 위치와 같은 위치를 지정합니다. |

## <a name="create-availability-sets"></a>가용성 집합 만들기

가상 컴퓨터를 만들기 전에 가용성 집합을 만들어야 합니다. 가용성 집합은 계획되거나 계획되지 않은 유지 관리 이벤트에 대한 가동 중지 시간을 줄입니다. Azure 가용성 집합은 Azure에서 물리적 장애 도메인 및 업데이트 도메인에 배치하는 리소스의 논리적 그룹입니다. 장애 도메인을 사용하면 가용성 집합의 구성원이 개별 전원 및 네트워크 리소스를 사용할 수 있습니다. 업데이트 도메인을 사용하면 가용성 집합의 구성원이 유지 관리를 위해 동시에 중단되지 않습니다. [가상 컴퓨터의 가용성을 관리합니다](../../virtual-machines-windows-manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

두 개의 가용성 집합이 필요합니다. 하나는 도메인 컨트롤러용이고 두 번째 SQL Server용입니다.

가용성 집합을 만들려면 리소스 그룹으로 이동하고 **추가**를 클릭합니다. **가용성 집합**을 입력하여 결과를 필터링합니다. 결과에서 **가용성 집합** 을 클릭합니다. **만들기**를 클릭합니다.

다음 표의 매개 변수에 따라 두 개의 가용성 집합을 구성합니다.

| **필드** | 도메인 컨트롤러 가용성 집합 | SQL Server 가용성 집합 |
| --- | --- | --- |
| **Name** |adavailabilityset |sqlavailabilityset |
| **리소스 그룹** |SQL-HA-RG |SQL-HA-RG |
| **장애 도메인** |3 |3 |
| **업데이트 도메인** |5 |3 |

가용성 집합을 만든 후 Azure 포털의 리소스 그룹으로 돌아옵니다.

## <a name="create-domain-controllers"></a>도메인 컨트롤러 만들기
네트워크, 서브넷, 가용성 집합을 만든 후에 인터넷 연결 부하 분산 장치를 만들었습니다. 도메인 컨트롤러에 대한 가상 컴퓨터를 만들 준비가 되었습니다.

### <a name="create-the-virtual-machines-for-the-domain-controllers"></a>도메인 컨트롤러에 대한 가상 컴퓨터 만들기
도메인 컨트롤러를 만들고 구성하려면 **SQL-HA-RG** 리소스 그룹으로 돌아갑니다.

1. 추가를 클릭합니다. **모두** 블레이드가 열립니다.
2. **Windows Server 2016 Datacenter**를 입력합니다. 
3. **Windows Server 2016 Datacenter**를 클릭합니다. **Windows Server 2016 Datacenter** 블레이드에서 배포 모델이 **Resource Manager**인지 확인하고 **만들기**를 클릭합니다. **가상 컴퓨터 만들기** 블레이드가 열립니다. 

두 개의 가상 컴퓨터를 만들려면 이전 단계를 반복합니다. 두 개의 가상 컴퓨터 이름을 지정합니다.

* ad-primary-dc
* ad-secondary-dc
  
  > [!NOTE]
  > **ad-secondary-dc** 는 Active Directory 도메인 서비스에 높은 가용성을 제공하는 선택적 구성 요소입니다. 
  > 
  > 

다음 표에서는 이러한 두 컴퓨터에 대한 설정을 보여 줍니다.

| **필드** | 값 |
| --- | --- |
| **VM 디스크 유형** |SSD |
| **사용자 이름** |DomainAdmin |
| **암호** |Contoso!0000 |
| **구독** |*구독* |
| **리소스 그룹** |SQL-HA-RG |
| **위치** |*사용자의 위치* |
| **크기** |DS1_V2 |
| **저장소 계정** |*자동으로 생성됨* |
| **가상 네트워크** |autoHAVNET |
| **서브넷** |관리자 |
| **공용 IP 주소** |*VM과 같은 이름* |
| **네트워크 보안 그룹** |*VM과 같은 이름* |
| **가용성 집합** |adavailabilityset |
| **진단** |사용 |
| **진단 저장소 계정** |*자동으로 생성됨* |


   >[!IMPORTANT]
   >VM을 만들 때 가용성 집합에 VM을 배치할 수 있습니다. VM을 만든 후에는 가용성 집합을 변경할 수 없습니다. [가상 컴퓨터의 가용성 관리](../../virtual-machines-windows-manage-availability.md)를 참조하세요.

가상 컴퓨터가 만들어집니다.

가상 컴퓨터를 만든 후에 도메인 컨트롤러를 구성합니다.

### <a name="configure-the-domain-controller"></a>도메인 컨트롤러 구성
다음 단계에서는 corp.contoso.com에 대한 도메인 컨트롤러로 **ad-primary-dc** 컴퓨터를 구성합니다.

1. 포털에서 **SQL-HA-RG** 리소스 그룹을 선택하고 **ad-primary-dc** 컴퓨터를 선택합니다. **ad-primary-dc** 블레이드에서 **연결**을 클릭하여 원격 데스크톱 액세스를 위한 RDP 파일을 엽니다.
   
    ![가상 컴퓨터에 연결](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/20-connectrdp.png)
2. 구성된 관리자 계정(**\DomainAdmin**) 및 암호(**Contoso!0000**)로 로그인합니다.
3. 기본적으로 **서버 관리자** 대시보드가 표시됩니다.
4. 대시보드에서 **역할 및 기능 추가** 링크를 클릭합니다.
   
    ![서버 탐색기 역할 추가](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/22-addfeatures.png)
5. **서버 역할** 섹션으로 이동할 때까지 **다음**을 선택합니다.
6. **Active Directory 도메인 서비스** 및 **DNS 서버** 역할을 선택합니다. 메시지가 표시되면 이러한 역할에 필요한 기능을 더 추가합니다.
   
   > [!NOTE]
   > 정적 IP 주소가 없다는 경고가 표시됩니다. 구성을 테스트하는 경우 계속을 클릭합니다. 프로덕션 시나리오의 경우 Azure Portal에서 IP 주소를 고정으로 설정하거나 [PowerShell을 사용하여 도메인 컨트롤러 컴퓨터의 고정 IP 주소를 설정](../../../virtual-network/virtual-networks-reserved-private-ip.md)합니다.
   > 
   > 
   
    ![역할 추가 대화 상자](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/23-addroles.png)
7. **확인** 섹션에 도달할 때까지 **다음**을 클릭합니다. **필요한 경우 자동으로 대상 서버 다시 시작** 확인란을 선택합니다.
8. **Install**을 클릭합니다.
9. 기능 설치를 완료한 후에는 **서버 관리자** 대시보드로 돌아갑니다.
10. 왼쪽 창에서 새 **AD DS** 옵션을 선택합니다.
11. 노란색 경고 표시줄에서 **자세히** 링크를 클릭합니다.
    
    ![DNS 서버 VM에서 AD DS 대화 상자](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/24-addsmore.png)
12. **모든 서버 작업 세부 정보** 대화 상자의 **작업** 열에서 **이 서버를 도메인 컨트롤러로 승격**을 클릭합니다.
13. **Active Directory 도메인 서비스 구성 마법사**에서 다음 값을 사용합니다.
    
    | **Page** | 설정 |
    | --- | --- |
    | **배포 구성** |**새 포리스트 추가**<br/> **루트 도메인 이름** = corp.contoso.com |
    | **도메인 컨트롤러 옵션** |**암호** = Contoso!0000<br/>**암호 확인** = Contoso!0000 |
14. **다음**을 클릭하여 마법사의 다른 페이지를 진행합니다. **필수 구성 요소 확인** 페이지에서 다음 메시지가 표시되는지 확인합니다. **모든 필수 구성 요소 검사를 마쳤습니다**. 해당하는 모든 경고 메시지를 검토해야 하지만 설치는 계속할 수 있습니다.
15. **Install**을 클릭합니다. **ad-primary-dc** 가상 컴퓨터가 자동으로 다시 부팅됩니다.

### <a name="note-ip-address-of-primary-domain-controller"></a>주 도메인 컨트롤러의 IP 주소 메모

DNS에 대한 주 도메인 컨트롤러를 사용합니다. 주 도메인 컨트롤러 IP 주소를 메모합니다.

주 도메인 컨트롤러 IP 주소를 가져오는 한 가지 방법은 Azure Portal을 통해 가져오는 것입니다. 

1. Azure Portal에서 리소스 그룹을 엽니다. 

1. 주 도메인 컨트롤러를 클릭합니다.

1. 주 도메인 컨트롤러 블레이드에서 **네트워크 인터페이스**를 클릭합니다.

![새 항목](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/25-primarydcip.png)

이 서버에 대한 개인 IP 주소를 메모합니다. 

### <a name="configure-the-second-domain-controller"></a>두 번째 도메인 컨트롤러 구성
주 도메인 컨트롤러를 다시 부팅한 후에 두 번째 도메인 컨트롤러를 구성할 수 있습니다. 이 선택적 단계는 가용성을 높이기 위한 것입니다. 두 번째 도메인 컨트롤러를 구성하려면 다음 단계를 수행합니다.

1. 포털에서 **SQL-HA-RG** 리소스 그룹을 열고 **ad-secondary-dc** 컴퓨터를 선택합니다. **ad-secondary-dc** 블레이드에서 **연결**을 클릭하여 원격 데스크톱 액세스를 위한 RDP 파일을 엽니다.
4. 구성된 관리자 계정(**BUILTIN\DomainAdmin**) 및 암호(**Contoso!0000**)를 사용하여 VM에 로그인합니다.
3. 기본 설정된 DNS 서버 주소를 도메인 컨트롤러의 주소로 변경합니다. 
1. **네트워크 및 공유 센터**에서 네트워크 인터페이스를 클릭합니다. 
   ![NetworkInterface](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/26-networkinterface.png)

5. **속성**을 클릭합니다.
10. **인터넷 프로토콜 버전 4(TCP/IPv4)** 를 선택하고 속성을 클릭합니다.
11. **다음 DNS 서버 주소 사용**을 선택하고 **기본 설정 DNS 서버**에서 주 도메인 컨트롤러 주소를 지정합니다.
1. **확인**을 클릭한 후 **닫기**를 클릭하여 변경 내용을 커밋합니다. 이제 VM을 **corp.contoso.com**에 연결할 수 있습니다.

   >[!IMPORTANT]
   >DNS 설정을 변경한 후에 원격 데스크톱에 대한 연결을 잃어버리는 경우 Azure Portal로 이동하고 가상 컴퓨터를 다시 시작합니다.

1. 원격 데스크톱에서 보조 도메인 컨트롤러로 **서버 관리자 대시보드**를 엽니다.
4. 대시보드에서 **역할 및 기능 추가** 링크를 클릭합니다.
   
    ![서버 탐색기 역할 추가](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/22-addfeatures.png)
5. **서버 역할** 섹션으로 이동할 때까지 **다음**을 선택합니다.
6. **Active Directory 도메인 서비스** 및 **DNS 서버** 역할을 선택합니다. 메시지가 표시되면 이러한 역할에 필요한 기능을 더 추가합니다.

9. 기능 설치를 완료한 후에는 **서버 관리자** 대시보드로 돌아갑니다.
10. 왼쪽 창에서 새 **AD DS** 옵션을 선택합니다.
11. 노란색 경고 표시줄에서 **자세히** 링크를 클릭합니다.
12. **모든 서버 작업 세부 정보** 대화 상자의 **작업** 열에서 **이 서버를 도메인 컨트롤러로 승격**을 클릭합니다.
1. **배포 구성** 아래에서 **기존 도메인에 도메인 컨트롤러 추가**를 선택합니다. 
   ![NetworkInterface](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/28-deploymentconfig.png)

1. **선택...**을 클릭합니다.
1. 관리자 계정(**CORP.CONTOSO.COM\domainadmin**) 및 암호(**Contoso!0000**)를 사용하여 연결합니다.
1. **포리스트에서 도메인 선택**에서 해당 도메인을 클릭하고 **확인**을 클릭합니다. 

1. **도메인 컨트롤러 옵션**에서 기본값을 사용하고 DSRM 암호를 설정합니다.

   >[!NOTE]
   >**DNS 옵션** 페이지에서 이 DNS 서버에 대한 위임을 만들 수 없다고 경고할 수 있습니다. 비-프로덕션 환경에서 이 경고를 무시할 수 있습니다. 
1. 대화 상자가 **필수 구성 요소** 확인에 도달할 때까지 **다음**을 클릭합니다. **설치**를 클릭합니다.

서버가 구성 변경 내용을 완료한 후 서버를 다시 시작합니다. 

### <a name=DomainAccounts></a> 도메인 계정 구성

다음 단계에서는 AD(Active Directory) 계정을 구성합니다. 다음 표에서 이러한 계정을 보여줍니다.

| |설치 계정<br/> |sqlserver-0 <br/>SQL Server 및 SQL 에이전트 서비스 계정 |sqlserver-1<br/>SQL Server 및 SQL 에이전트 서비스 계정
| --- | --- | --- | --- 
|**이름** |Install |SQLSvc1 | SQLSvc2
|**사용자 SamAccountName** |Install |SQLSvc1 | SQLSvc2

다음 단계를 사용하여 각 계정을 만듭니다. 

1. **ad-primary-dc** 컴퓨터에 다시 로그인합니다.
2. **서버 관리자**에서 **도구**를 선택한 후 **Active Directory 관리 센터**를 클릭합니다.   
3. 왼쪽 창에서 **corp(로컬)**을 선택합니다.
4. 오른쪽 **작업** 창에서 **새로 만들기**를 선택한 후 **사용자**를 클릭합니다. 
   ![Active Directory 관리 센터](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/29-addcnewuser.png)

   >[!TIP]
   >각 계정에 대해 복잡한 암호를 설정합니다.<br/> 비-프로덕션 환경의 경우 사용자 계정이 만료되지 않도록 설정합니다.

5. **확인**을 클릭하여 사용자를 만듭니다. 
6. 3개의 각 계정에 대해 위의 단계를 반복합니다. 

### <a name="grant-required-permissions-to-the-installation-account"></a>설치 계정에 필요한 권한 부여
1. **Active Directory 관리 센터**의 왼쪽 창에서 **corp(로컬)**을 선택합니다. 다음으로 오른쪽 **작업** 창에서 **속성**을 클릭합니다.
   
    ![CORP 사용자 속성](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/31-addcproperties.png)
8. **확장**을 선택한 후 **보안** 탭에서 **고급** 단추를 클릭합니다.
9. **corp 고급 보안 설정** 대화 상자에서 **추가**를 클릭합니다.
10. **보안 주체 선택**을 클릭합니다. 그런 다음 **CORP\Install**을 검색합니다. **확인**을 클릭합니다.
11. **모든 속성 읽기**를 선택합니다.

1. **컴퓨터 개체 만들기**를 선택합니다.
    
     ![Corp 사용자 권한](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/33-addpermissions.png)
12. **확인**을 클릭한 후 **확인**을 한 번 더 클릭합니다. corp 속성 창을 닫습니다.

Active Directory 및 사용자 개체 구성을 완료했으므로&2;개의 SQL Server VM과&1;개의 감시 서버 VM을 만듭니다. 그런 다음 세 개의 VM을 도메인에 연결합니다.

## <a name="create-sql-servers"></a>SQL Server를 만듭니다.
### <a name="create-and-configure-the-sql-server-vms"></a>SQL Server VM 만들기 및 구성
다음으로 WSFC 클러스터 노드&1;개와 SQL Server VM&2;개를 포함하는 VM을&3;개 만듭니다. 각 VM을 만들려면 **SQL-HA-RG** 리소스 그룹으로 돌아가서 **추가**를 클릭하고 적합한 갤러리 항목, **가상 컴퓨터** 및 **갤러리에서**를 차례로 검색합니다. 아래 표의 정보를 사용하면 VM을 만드는 데 도움이 됩니다.

| Page | VM1 | VM2 | VM3 |
| --- | --- | --- | --- |
| 적절한 갤러리 항목 선택 |**Windows Server 2016 Datacenter** |**Windows Server 2016의 SQL Server 2016 SP1 Enterprise** |**Windows Server 2016의 SQL Server 2016 SP1 Enterprise** |
| 가상 컴퓨터 구성 **기본 사항** |**이름** = cluster-fsw<br/>**사용자 이름** = DomainAdmin<br/>**암호** = Contoso!0000<br/>**구독** = 사용자 구독<br/>**리소스 그룹** = SQL-HA-RG<br/>**위치** = 해당 Azure 위치 |**이름** = sqlserver-0<br/>**사용자 이름** = DomainAdmin<br/>**암호** = Contoso!0000<br/>**구독** = 사용자 구독<br/>**리소스 그룹** = SQL-HA-RG<br/>**위치** = 해당 Azure 위치 |**이름** = sqlserver-1<br/>**사용자 이름** = DomainAdmin<br/>**암호** = Contoso!0000<br/>**구독** = 사용자 구독<br/>**리소스 그룹** = SQL-HA-RG<br/>**위치** = 해당 Azure 위치 |
| 가상 컴퓨터 구성 **크기** |DS1\_V2(1 코어, 3.5GB) |**크기** = DS2\_V2(2 코어, 7GB) |**크기** = DS2\_V2(2 코어, 7GB) |
| 가상 컴퓨터 구성 **설정** |**Storage** = Premium(SSD)<br/>**네트워크 서브넷** = autoHAVNET<br/>**계정** = 자동으로 생성된 Storage 계정 사용<br/>**서브넷** = sqlsubnet(10.1.1.0/24)<br/>**공용 IP 주소** = 없음<br/>**네트워크 보안 그룹** = 없음<br/>**진단 모니터링** = 사용<br/>**진단 Storage 계정** = 자동으로 생성된 Storage계정 사용<br/>**가용성 집합** = sqlAvailabilitySet<br/> |**Storage** = Premium(SSD)<br/>**네트워크 서브넷** = autoHAVNET<br/>**계정** = 자동으로 생성된 Storage 계정 사용<br/>**서브넷** = sqlsubnet(10.1.1.0/24)<br/>**공용 IP 주소** = 없음<br/>**네트워크 보안 그룹** = 없음<br/>**진단 모니터링** = 사용<br/>**진단 Storage 계정** = 자동으로 생성된 Storage계정 사용<br/>**가용성 집합** = sqlAvailabilitySet<br/> |**Storage** = Premium(SSD)<br/>**네트워크 서브넷** = autoHAVNET<br/>**계정** = 자동으로 생성된 Storage 계정 사용<br/>**서브넷** = sqlsubnet(10.1.1.0/24)<br/>**공용 IP 주소** = 없음<br/>**네트워크 보안 그룹** = 없음<br/>**진단 모니터링** = 사용<br/>**진단 Storage 계정** = 자동으로 생성된 Storage계정 사용<br/>**가용성 집합** = sqlAvailabilitySet<br/> |
| 가상 컴퓨터 구성 **SQL Server 설정** |해당 없음 |**SQL 연결** = 개인(가상 네트워크 내)<br/>**포트** = 1433<br/>**SQL 인증** = 사용 안 함<br/>**Storage 구성** = 일반<br/>**자동화된 패치** = 일요일 2시<br/>**자동화된 백업** = 사용 안 함</br>**Azure 주요 자격 증명 모음 통합** = 사용 안 함 |**SQL 연결** = 개인(가상 네트워크 내)<br/>**포트** = 1433<br/>**SQL 인증** = 사용 안 함<br/>**Storage 구성** = 일반<br/>**자동화된 패치** = 일요일 2시<br/>**자동화된 백업** = 사용 안 함</br>**Azure 주요 자격 증명 모음 통합** = 사용 안 함 |

<br/>

> [!NOTE]
> 여기에서 제안하는 컴퓨터 크기는 Azure VM에서 가용성 그룹 테스트를 위해 계획된 것입니다. 프로덕션 작업에서 최상의 성능을 얻으려면 [Azure 가상 컴퓨터의 SQL Server에 대한 성능 모범 사례](virtual-machines-windows-sql-performance.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)에서 SQL Server 컴퓨터 크기 및 구성에 대한 권장 사항을 참조하세요.
> 
> 

3개의 VM이 완전히 프로비전되면 VM을 **corp.contoso.com** 도메인에 연결하고 컴퓨터에 CORP\Install 관리 권한을 부여해야 합니다.

### <a name="set-dns-on-each-server"></a>각 서버에서 DNS 설정
먼저, 각 구성원 서버에 대해 기본 설정된 DNS 서버 주소를 변경합니다. 다음 단계를 수행하세요.

1. 포털에서 **SQL-HA-RG** 리소스 그룹을 열고 **sqlserver-0** 컴퓨터를 선택합니다. **sqlserver-0** 블레이드에서 **연결**을 클릭하여 원격 데스크톱 액세스를 위한 RDP 파일을 엽니다.
2. 구성된 관리자 계정(**\DomainAdmin**) 및 암호(**Contoso!0000**)로 로그인합니다.
3. 기본적으로 **서버 관리자** 대시보드가 표시됩니다. 왼쪽 창에서 **로컬 서버** 를 클릭합니다.
5. **DHCP에 의해 할당된 IPv4 주소, IPv6 사용 가능** 링크를 선택합니다.
6. **네트워크 연결** 창에서 네트워크 아이콘을 선택합니다.
7. 명령 모음에서 **이 연결의 설정 변경**을 클릭합니다. 이 옵션이 표시되지 않으면 이중 오른쪽 화살표를 클릭합니다.
8. **인터넷 프로토콜 버전 4(TCP/IPv4)** 를 선택하고 속성을 클릭합니다.
9. **다음 DNS 서버 주소 사용**을 선택하고 **기본 설정 DNS 서버**에서 주 도메인 컨트롤러 주소를 지정합니다.
   >[!TIP]
   >서버의 IP 주소를 가져오려면 `nslookup`을 사용합니다.<br/>
   >명령 프롬프트에서 `nslookup ad-primary-dc`를 입력합니다. 
11. **확인**을 클릭한 후 **닫기**를 클릭하여 변경 내용을 커밋합니다. 

   >[!IMPORTANT]
   >DNS 설정을 변경한 후에 원격 데스크톱에 대한 연결을 잃어버리는 경우 Azure Portal로 이동하고 가상 컴퓨터를 다시 시작합니다.

모든 서버에 이 단계를 반복합니다.

### <a name="joinDomain"></a>도메인에 서버를 조인합니다.

이제 VM을 **corp.contoso.com**에 연결할 수 있습니다. 두 SQL Server 및 파일 공유 감시 서버에 대해 다음을 수행합니다. 

1. **BUILTIN\DomainAdmin**을 사용하여 가상 컴퓨터에 원격으로 연결합니다. 
1. **서버 관리자**에서 **로컬 서버**를 클릭합니다.
1. **작업 그룹** 링크를 클릭합니다.
1. **컴퓨터 이름** 섹션에서 **변경**을 클릭합니다.
1. **도메인** 확인란을 선택하고 텍스트 상자에 **corp.contoso.com**을 입력합니다. **확인**을 클릭합니다.
1. **Windows 보안** 팝업 대화 상자에서 기본 도메인 관리자 계정에 대한 자격 증명(**CORP\DomainAdmin**) 및 암호(**Contoso!0000**)를 지정합니다.
1. "corp.contoso.com 도메인 시작" 메시지가 표시되면 **확인**을 클릭합니다.
1. **닫기**를 클릭한 후 팝업 대화 상자에서 **지금 다시 시작**을 클릭합니다.



### <a name="add-the-corpinstall-user-as-an-administrator-on-each-cluster-vm"></a>각 클러스터 VM에서 Corp\Install 사용자를 관리자로 추가합니다.

각 가상 컴퓨터가 도메인의 구성원으로 다시 시작한 후 **CORP\Install**을 로컬 관리자 그룹의 구성원으로 추가합니다. 

1. VM이 다시 시작될 때까지 기다린 후 주 도메인 컨트롤러에서 RDP 파일을 다시 시작하여 **CORP\DomainAdmin** 계정을 사용하여 **sqlserver-0**에 로그인합니다.
   >[!TIP]
   >도메인 관리자 계정을 사용하여 로그인해야 합니다. 이전 단계에서 BUILT IN 관리자 계정을 사용했습니다. 이제 서버가 도메인에 있으므로 도메인 계정을 사용합니다. RDP 세션에서 *도메인*\\*사용자 이름*을 지정합니다.

2. **서버 관리자**에서 **도구**를 선택하고 **컴퓨터 관리**를 클릭합니다.
3. **컴퓨터 관리** 창에서 **로컬 사용자 및 그룹**을 확장한 후 **그룹**을 선택합니다.
4. **Administrators** 그룹을 두 번 클릭합니다.
5. **Administrators 속성** 대화 상자에서 **추가** 단추를 클릭합니다.
6. **CORP\Install** 사용자를 입력한 후 **확인**을 클릭합니다. 
7. **확인**을 클릭하여 **Administrator 속성** 대화 상자를 닫습니다.
8. **sqlserver-1** 및 **cluster-fsw**에서 위의 단계를 반복합니다.

### <a name="setServiceAccount"></a>SQL Server 서비스 계정 설정

각 SQL Server에서 SQL Server 서비스 계정을 설정합니다. [도메인 계정을 구성](#DomainAccounts)할 때 만든 계정을 사용합니다.

1. **SQL Server 구성 관리자**를 엽니다.

1. SQL Server 서비스를 마우스 오른쪽 단추로 클릭하고 **속성**을 클릭합니다.

1. 계정 및 암호를 설정합니다. 

1. 다른 SQL Server에서도 이 단계를 반복합니다.  

SQL Server 가용성 그룹의 경우 각 SQL Server는 도메인 계정으로 실행해야 합니다. 

### <a name="create-login-on-each-sql-server-for-installation-account"></a>설치 계정에 대한 각 SQL Server에서 로그인 만들기

설치 계정을 사용하여 가용성 그룹을 구성합니다. 이 계정은 각 SQL Server에서 **sysadmin** 고정 서버 역할의 구성원이어야 합니다. 다음 단계에서는 설치 계정에 대한 로그인을 만듭니다.

1. *\<MachineName\>\DomainAdmin* 계정을 사용하여 서버로 RDP합니다.

1. SQL Server Management Studio를 열고 SQL Server의 로컬 인스턴스에 연결합니다. 

1. **개체 탐색기**에서 **보안**을 클릭합니다.

1. 마우스 오른쪽 단추로 **로그인**을 클릭합니다. **새 로그인...**을 클릭합니다.

1. **로그인 - 신규**에서 **검색...**을 클릭합니다.

1. **위치...**를 클릭합니다.

1. 도메인 관리자 네트워크 자격 증명을 입력합니다. 

1. 설치 계정을 사용합니다.

1. **sysadmin** 고정 서버 역할의 구성원이 될 로그인을 설정합니다. 

1. 확인을 클릭합니다. 

다른 SQL Server에서도 위의 단계를 반복합니다. 

## <a name="add-failover-cluster-features-to-both-sql-servers"></a>두 SQL Server에 장애 조치(Failover) 클러스터 기능 추가

장애 조치(Failover) 클러스터 기능을 추가하려면 두 SQL Server에서 다음 단계를 수행합니다.

1. 원격 데스크톱에서 보조 도메인 컨트롤러로 **서버 관리자 대시보드**를 엽니다.
4. 대시보드에서 **역할 및 기능 추가** 링크를 클릭합니다.
   
    ![서버 탐색기 역할 추가](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/22-addfeatures.png)
5. **서버 기능** 섹션으로 이동할 때까지 **다음**을 선택합니다.
1. **기능**에서 **장애 조치 클러스터링**을 선택합니다. 
1. 필요한 추가 기능을 추가합니다. 
1. 설치를 클릭하여 모든 기능을 추가합니다.

다른 SQL Server에서도 이 단계를 반복합니다. 


## <a name="a-nameendpoint-firewall-configure-the-firewall-on-each-sql-server"></a><a name="endpoint-firewall"> 각 SQL Server에서 방화벽 구성

솔루션은 방화벽에서 열리기 위해 다음 TCP 포트가 필요합니다.

- **SQL Server**<br/>
   SQL Server의 기본 인스턴스의 경우 포트 1433입니다. 
- **Azure Load Balancer 프로브**<br/>
   사용 가능한 모든 포트입니다. 예제는 59999를 자주 사용합니다.
- **데이터베이스 미러링 끝점** <br/>
   사용 가능한 모든 포트입니다. 예제는 5022를 자주 사용합니다. 

방화벽 포트는 두 SQL Server에서 열려야 합니다.

포트를 여는 방법은 사용하는 방화벽 솔루션에 따라 다릅니다. 다음 섹션에서는 Windows 방화벽에서 포트를 여는 방법을 설명합니다. 각 SQL Server의 필요한 포트를 엽니다. 

### <a name="open-a-tcp-port-on-a-firewall"></a>방화벽에서 TCP 포트 열기 

1. 첫 번째 SQL Server **시작** 화면에서 **고급 보안이 포함된 Windows 방화벽**을 시작합니다.

2. 왼쪽 창에서 **인바운드 규칙**을 선택합니다. 오른쪽 창에서 **새 규칙**을 클릭합니다.

3. **규칙 유형**에 **포트**를 선택합니다.

1. 포트에 대해 TCP를 지정하고 적절한 포트 번호를 입력합니다. 다음 예제를 참조하세요.

   ![SQLFirewall](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/35-tcpports.png)

1. **다음**을 클릭합니다. 

5. **작업** 페이지에서 **연결 허용**을 선택한 상태로 유지하고 **다음**을 클릭합니다.

6. **프로필** 페이지에서 기본 설정을 그대로 적용하고 **다음**을 클릭합니다.

7. **이름** 페이지에서 **이름** 텍스트 상자에 **Azure LB Probe**와 같은 규칙 이름을 지정하고 **마침**을 클릭합니다.

동일한 방법으로 두 번째 SQL Server에서 이러한 단계를 반복합니다.



## <a name="next-steps"></a>다음 단계

* [Azure Virtual Machines에서 SQL Server Always On 가용성 그룹 만들기](virtual-machines-windows-portal-sql-availability-group-tutorial.md)

