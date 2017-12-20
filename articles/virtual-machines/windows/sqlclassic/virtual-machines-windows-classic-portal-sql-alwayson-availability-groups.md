---
title: "Azure Virtual Machines의 Always On 가용성 그룹 구성(클래식) | Microsoft Docs"
description: "Azure Virtual Machines로 Always On 가용성 그룹을 만듭니다. 이 자습서에서는 스크립트보다는 사용자 인터페이스 및 도구를 주로 사용합니다."
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: 8d48a3d2-79f8-4ab0-9327-2f30ee0b2ff1
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/17/2017
ms.author: mikeray
ms.openlocfilehash: 275c0fdfecac558e4f10d36eee71d38528f34679
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/11/2017
---
# <a name="configure-always-on-availability-group-in-azure-virtual-machines-classic"></a>Azure Virtual Machines의 Always On 가용성 그룹 구성(클래식)
> [!div class="op_single_selector"]
> * [클래식: UI](../classic/portal-sql-alwayson-availability-groups.md)
> * [클래식: PowerShell](../classic/ps-sql-alwayson-availability-groups.md)
<br/>

시작하기 전에 이제 Azure Resource Manager 모델에서 이 작업을 완료할 수 있는지 확인하는 것이 좋습니다. 새 배포에는 Azure Resource Manager 모델을 사용하는 것이 좋습니다. [Azure Virtual Machines의 SQL Server Always On 가용성 그룹](../sql/virtual-machines-windows-portal-sql-availability-group-overview.md)을 참조하세요.

> [!IMPORTANT] 
> 새로운 배포는 대부분 리소스 관리자 모델을 사용하는 것이 좋습니다. Azure에는 리소스를 만들고 작업하기 위한 [리소스 관리자 및 클래식](../../../azure-resource-manager/resource-manager-deployment-model.md)이라는 두 가지 배포 모델이 있습니다. 이 문서에서는 클래식 배포 모델을 사용하는 방법을 설명합니다. 

Azure Resource Manager를 사용하여 이 작업을 완료하려면 [Azure Virtual Machines의 SQL Server AlwaysOn 가용성 그룹](../sql/virtual-machines-windows-portal-sql-availability-group-overview.md)을 참조하세요.

이 종단 간 자습서에서는 Azure Virtual Machines에서 실행되는 SQL Server Always On을 사용하여 가용성 그룹을 구현하는 방법을 보여 줍니다.

자습서 마지막에서 Azure의 SQL Server Always On 솔루션은 다음 요소로 구성됩니다.

* 여러 서브넷과 프런트 엔드 및 백 엔드 서브넷을 포함하는 가상 네트워크
* Active Directory(Azure AD) 도메인을 포함한 도메인 컨트롤러
* SQL Server를 실행하고 백 엔드 서브넷에 배포되고 Azure AD 도메인에 가입된 두 개의 가상 컴퓨터
* 노드 과반수 쿼럼 모델을 포함하는 3노드 장애 조치 클러스터
* 가용성 데이터베이스의 두 개의 동기 커밋 복제본이 포함된 가용성 그룹

다음 그림은 솔루션을 그래픽으로 표현한 것입니다.

![Azure에서 가용성 그룹에 대한 테스트 랩 아키텍처](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC791912.png)

이것은 가능한 구성 중 하나입니다. 예를 들어 2복제본 가용성 그룹에 대한 가상 컴퓨터 수를 최소화할 수 있습니다. 이 구성에서는 도메인 컨트롤러를 2노드 클러스터의 쿼럼 파일 공유 감시로 사용하여 Azure에서 계산 시간을 단축합니다. 이 방법을 사용하면 설명된 구성에서 가상 컴퓨터 수가 하나씩 감소합니다.

이 자습서에서는 다음을 가정합니다.

* Azure 계정이 있습니다.
* 가상 컴퓨터 갤러리에서 GUI를 사용하여 SQL Server를 실행하는 클래식 가상 컴퓨터를 프로비전하는 방법을 알고 있습니다.
* Always On 가용성 그룹을 확실하게 이해하고 있습니다. 자세한 내용은 [Always On 가용성 그룹(SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx)을 참조하세요.

> [!NOTE]
> SharePoint와 Always On 가용성 그룹을 사용하는 것에 관심이 있는 경우 [Configure SQL Server 2012 Always On Availability Groups for SharePoint 2013](https://technet.microsoft.com/library/jj715261.aspx)(SharePoint 2013에 대해 SQL Server 2012 Always On 가용성 그룹 구성)을 참조하세요.
> 
> 

## <a name="create-the-virtual-network-and-domain-controller-server"></a>가상 네트워크 및 도메인 컨트롤러 서버 만들기
새로운 Azure 평가판 계정으로 시작합니다. 계정을 설정한 후 Azure Portal의 홈 화면이 표시됩니다.

1. 다음 스크린샷과 같이 페이지의 왼쪽 아래 모퉁이에서 **새로 만들기** 단추를 클릭합니다.
   
    ![포털에서 새로 만들기 클릭](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665511.gif)
2. 다음 스크린샷과 같이 **Network Services** > **Virtual Network** > **사용자 지정 만들기**를 클릭합니다.
   
    ![Virtual Network 만들기](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665512.gif)
3. **가상 네트워크 만들기** 대화 상자에서 페이지를 단계별로 진행하고 다음 표의 설정을 사용하여 새 가상 네트워크를 만듭니다. 
   
   | Page | 설정 |
   | --- | --- |
   | Virtual Network 세부 정보 |**이름 = ContosoNET**<br/>**지역 = 미국 서부** |
   | DNS 서버 및 VPN 연결 |없음 |
   | Virtual Network 주소 공간 |다음 스크린샷에 설정이 나와 있습니다. ![Virtual Network 만들기](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784620.png) |
4. DC(도메인 컨트롤러)로 사용할 가상 컴퓨터를 만듭니다. 다음 스크린샷과 같이 **새로 만들기** > **계산** > **가상 컴퓨터** > **갤러리에서**를 클릭합니다.
   
    ![가상 컴퓨터 만들기](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784621.png)
5. **가상 컴퓨터 만들기** 대화 상자에서 페이지를 단계별로 진행하고 다음 표의 설정을 사용하여 새 가상 컴퓨터를 구성합니다. 
   
   | Page | 설정 |
   | --- | --- |
   | 가상 컴퓨터 운영 체제 선택 |Windows Server 2012 R2 Datacenter |
   | 가상 컴퓨터 구성 |**버전 릴리스 날짜** = (latest)<br/>**가상 컴퓨터 이름** = ContosoDC<br/>**계층** = 표준<br/>**크기** = A2(2코어)<br/>**새 사용자 이름** = AzureAdmin<br/>**새 암호** = Contoso!000<br/>**확인** = Contoso!000 |
   | 가상 컴퓨터 구성 |**클라우드 서비스** = 새 클라우드 서비스 만들기<br/>**클라우드 서비스 DNS 이름** = 고유한 클라우드 서비스 이름<br/>**DNS 이름** = 고유 이름(예: ContosoDC123)<br/>**지역/선호도 그룹/가상 네트워크** = ContosoNET<br/>**가상 네트워크 서브넷** = Back(10.10.2.0/24)<br/> **계정** = 자동으로 생성된 Storage 계정 사용<br/>**가용성 집합** = (없음) |
   | 가상 컴퓨터 옵션 |기본값 사용 |

새 가상 컴퓨터를 구성한 후 가상 컴퓨터가 프로비전될 때까지 기다립니다. 이 프로세스는 완료하는 데 다소 시간이 걸립니다. Azure Portal에서 **Virtual Machine** 탭을 클릭하면 ContosoDC 순환 상태가 **시작 중(프로비전)**에서 **중지됨**, **시작 중**, **실행 중(프로비전)**, 마지막으로 **실행 중**으로 표시될 수 있습니다.

이제 DC 서버가 성공적으로 프로비전되었습니다. 다음으로 이 DC 서버에 Active Directory 도메인을 구성합니다.

## <a name="configure-the-domain-controller"></a>도메인 컨트롤러 구성
다음 단계에서는 corp.contoso.com에 대한 도메인 컨트롤러로 ContosoDC 컴퓨터를 구성합니다.

1. 포털에서 **ContosoDC** 컴퓨터를 선택합니다. **대시보드** 탭에서 **연결**을 클릭하여 원격 데스크톱 액세스를 위한 RDP(원격 데스크톱) 파일을 엽니다.
   
    ![가상 컴퓨터에 연결](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784622.png)
2. 구성된 관리자 계정(**\AzureAdmin**) 및 암호(**Contoso!000**)로 로그인합니다.
3. 기본적으로 **서버 관리자** 대시보드가 표시됩니다.
4. 대시보드에서 **역할 및 기능 추가** 링크를 클릭합니다.
   
    ![서버 탐색기 역할 추가](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784623.png)
5. **서버 역할** 섹션이 표시될 때까지 **다음**을 클릭합니다.
6. **Active Directory Domain Services** 및 **DNS 서버** 역할을 선택합니다. 메시지가 표시되면 이러한 역할에 필요한 기능을 추가합니다.
   
   > [!NOTE]
   > 고정 IP 주소가 없다는 유효성 검사 경고 페이지가 표시됩니다. 구성을 테스트하는 경우 **계속**을 클릭합니다. 프로덕션 시나리오의 경우 [PowerShell을 사용하여 도메인 컨트롤러 컴퓨터의 고정 IP 주소를 설정](../../../virtual-network/virtual-networks-reserved-private-ip.md)합니다.
   > 
   > 
   
    ![역할 추가 대화 상자](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784624.png)
7. **확인** 섹션에 도달할 때까지 **다음**을 클릭합니다. **필요한 경우 자동으로 대상 서버 다시 시작** 확인란을 선택합니다.
8. **Install**을 클릭합니다.
9. 기능이 설치된 후 **서버 관리자** 대시보드로 돌아갑니다.
10. 왼쪽 창에서 새 **AD DS** 옵션을 선택합니다.
11. 노란색 경고 표시줄에서 **자세히** 링크를 클릭합니다.
    
     ![DNS 서버 가상 컴퓨터의 AD DS 대화 상자](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784625.png)
12. **모든 서버 작업 세부 정보** 대화 상자의 **작업** 열에서 **이 서버를 도메인 컨트롤러로 승격**을 클릭합니다.
13. **Active Directory Domain Services 구성 마법사**에서 다음 값을 사용합니다.
    
    | Page | 설정 |
    | --- | --- |
    | 배포 구성 |**새 포리스트 추가** = 선택됨<br/>**루트 도메인 이름** = corp.contoso.com |
    | 도메인 컨트롤러 옵션 |**암호** = Contoso!000<br/>**암호 확인** = Contoso!000 |
14. **다음**을 클릭하여 마법사의 다른 페이지를 진행합니다. **필수 구성 요소 확인** 페이지에서 다음 메시지가 표시되는지 확인합니다. **모든 필수 구성 요소 검사를 마쳤습니다**. 해당하는 모든 경고 메시지를 검토해야 하지만 설치는 계속할 수 있습니다.
15. **Install**을 클릭합니다. **ContosoDC** 가상 컴퓨터가 자동으로 다시 부팅됩니다.

## <a name="configure-domain-accounts"></a>도메인 계정 구성
다음 단계에서는 나중에 사용하기 위해 Active Directory 계정을 구성합니다.

1. **ContosoDC** 컴퓨터에 다시 로그인합니다.
2. **서버 관리자**에서 **도구** > **Active Directory 관리 센터**를 클릭합니다.
   
    ![Active Directory 관리 센터](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784626.png)
3. **Active Directory 관리 센터**의 왼쪽 창에서 **corp(로컬)**을 선택합니다.
4. 오른쪽 **작업** 창에서 **새로 만들기** > **사용자**를 클릭합니다. 다음 설정을 사용합니다.
   
   | 설정 | 값 |
   | --- | --- |
   | **이름** |Install |
   | **사용자 SamAccountName** |Install |
   | **암호** |Contoso!000 |
   | **암호 확인** |Contoso!000 |
   | **기타 암호 옵션** |선택 |
   | **암호 사용 기간 제한 없음** |선택 |
5. **확인**을 클릭하여 **Install** 사용자를 만듭니다. 이 계정이 장애 조치(Failover) 클러스터 및 가용성 그룹을 구성하는 데 사용됩니다.
6. 동일한 단계로 두 개의 추가 사용자 **CORP\SQLSvc1** 및 **CORP\SQLSvc2**를 만듭니다. 이러한 계정은 SQL Server 인스턴스에 사용됩니다. 그다음에 Windows 장애 조치(failover) 클러스터링을 구성하는 데 필요한 권한을 **CORP\Install**에 제공해야 합니다.
7. **Active Directory 관리 센터**의 왼쪽 창에서 **corp(로컬)**을 클릭합니다. **작업** 창에서 **속성**을 클릭합니다.
   
    ![CORP 사용자 속성](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784627.png)
8. **확장**을 선택한 후 **보안** 탭에서 **고급** 단추를 클릭합니다.
9. **corp 고급 보안 설정** 대화 상자에서 **추가**를 클릭합니다.
10. **보안 주체 선택**을 클릭하고, **CORP\Install**을 검색하고 나서, **확인**을 클릭합니다.
11. **모든 속성 읽기** 및 **컴퓨터 개체 만들기** 권한을 선택합니다.
    
     ![Corp 사용자 권한](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784628.png)
12. **확인**을 클릭한 후 **확인**을 한 번 더 클릭합니다. corp 속성 창을 닫습니다.

이제 Active Directory 및 사용자 개체를 구성했으므로 3개의 SQL Server 가상 컴퓨터를 만들고 이 도메인에 가입시킵니다.

## <a name="create-the-sql-server-virtual-machines"></a>SQL Server 가상 컴퓨터 만들기
3개의 가상 컴퓨터를 만듭니다. 하나는 클러스터 노드에 사용되고 두 개는 SQL Server에 사용됩니다. 각 가상 컴퓨터를 만들려면 Azure 클래식 포털로 돌아가 **새로 만들기**,  > **계산** > **가상 컴퓨터** > **갤러리에서**를 클릭합니다. 다음으로 아래 표의 템플릿을 사용하면 가상 컴퓨터를 만드는 데 도움이 됩니다.

| Page | VM1 | VM2 | VM3 |
| --- | --- | --- | --- |
| 가상 컴퓨터 운영 체제 선택 |**Windows Server 2012 R2 Datacenter** |**SQL Server 2014 RTM Enterprise** |**SQL Server 2014 RTM Enterprise** |
| 가상 컴퓨터 구성 |**버전 릴리스 날짜** = (latest)<br/>**가상 컴퓨터 이름** = ContosoWSFCNode<br/>**계층** = 표준<br/>**크기** = A2(2코어)<br/>**새 사용자 이름** = AzureAdmin<br/>**새 암호** = Contoso!000<br/>**확인** = Contoso!000 |**버전 릴리스 날짜** = (latest)<br/>**가상 컴퓨터 이름** = ContosoSQL1<br/>**계층** = 표준<br/>**크기** = A3(4코어)<br/>**새 사용자 이름** = AzureAdmin<br/>**새 암호** = Contoso!000<br/>**확인** = Contoso!000 |**버전 릴리스 날짜** = (latest)<br/>**가상 컴퓨터 이름** = ContosoSQL2<br/>**계층** = 표준<br/>**크기** = A3(4코어)<br/>**새 사용자 이름** = AzureAdmin<br/>**새 암호** = Contoso!000<br/>**확인** = Contoso!000 |
| 가상 컴퓨터 구성 |**클라우드 서비스** = 이전에 만든 고유한 클라우드 서비스 DNS 이름(예: ContosoDC123)<br/>**지역/선호도 그룹/가상 네트워크** = ContosoNET<br/>**가상 네트워크 서브넷** = Back(10.10.2.0/24)<br/> **계정** = 자동으로 생성된 Storage 계정 사용<br/>**가용성 집합** = 가용성 집합 만들기<br/>**가용성 집합 이름** = SQLHADR |**클라우드 서비스** = 이전에 만든 고유한 클라우드 서비스 DNS 이름(예: ContosoDC123)<br/>**지역/선호도 그룹/가상 네트워크** = ContosoNET<br/>**가상 네트워크 서브넷** = Back(10.10.2.0/24)<br/> **계정** = 자동으로 생성된 Storage 계정 사용<br/>**가용성 집합** = SQLHADR(컴퓨터를 만든 후 가용성 집합을 구성할 수도 있습니다. SQLHADR 가용성 집합에 3개의 컴퓨터를 모두 할당합니다.) |**클라우드 서비스** = 이전에 만든 고유한 클라우드 서비스 DNS 이름(예: ContosoDC123)<br/>**지역/선호도 그룹/가상 네트워크** = ContosoNET<br/>**가상 네트워크 서브넷** = Back(10.10.2.0/24)<br/> **계정** = 자동으로 생성된 Storage 계정 사용<br/>**가용성 집합** = SQLHADR(컴퓨터를 만든 후 가용성 집합을 구성할 수도 있습니다. SQLHADR 가용성 집합에 3개의 컴퓨터를 모두 할당합니다.) |
| 가상 컴퓨터 옵션 |기본값 사용 |기본값 사용 |기본값 사용 |

<br/>

> [!NOTE]
> 기본 계층 컴퓨터는 부하가 분산된 끝점을 지원하지 않기 때문에 이전 구성은 표준 계층 가상 컴퓨터를 제안합니다. 가용성 그룹 수신기를 만들려면 부하가 분산된 끝점이 필요합니다. 또한 여기에서 제안하는 컴퓨터 크기는 Azure Virtual Machines에서 가용성 그룹 테스트를 위해 계획된 것입니다. 프로덕션 작업에서 최상의 성능을 얻으려면 [Azure Virtual Machines의 SQL Server에 대한 성능 모범 사례](../sql/virtual-machines-windows-sql-performance.md)에서 SQL Server 컴퓨터 크기 및 구성에 대한 권장 사항을 참조하세요.
> 
> 

3개의 가상 컴퓨터가 완전히 프로비전된 후 가상 컴퓨터를 **corp.contoso.com** 도메인에 가입시키고 컴퓨터에 CORP\Install 관리 권한을 부여해야 합니다. 이렇게 하려면 3개의 가상 컴퓨터 각각에 대해 다음 단계를 사용합니다.

1. 먼저 기본 설정된 DNS 서버 주소를 변경합니다. 목록에서 가상 컴퓨터를 선택하고 **연결** 단추를 클릭하여 각 가상 컴퓨터의 RDP 파일을 로컬 디렉터리로 다운로드합니다. 가상 컴퓨터를 선택하려면 다음 스크린샷과 같이 행의 첫 번째 셀을 제외하고 아무 곳이나 클릭합니다.
   
    ![RDP 파일 다운로드](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC664953.jpg)
2. 다운로드한 RDP 파일을 열고 구성된 관리자 계정(**BUILTIN\AzureAdmin**) 및 암호(**Contoso!000**)로 가상 컴퓨터에 로그인합니다.
3. 로그인한 후 **서버 관리자** 대시보드가 표시됩니다. 왼쪽 창에서 **로컬 서버** 를 클릭합니다.
4. **DHCP에 의해 할당된 IPv4 주소, IPv6 사용 가능** 링크를 클릭합니다.
5. **네트워크 연결** 대화 상자에서 네트워크 아이콘을 클릭합니다.
   
    ![가상 컴퓨터 기본 설정 DNS 서버 변경](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784629.png)
6. 명령 모음에서 **이 연결의 설정 변경**을 클릭합니다. 창의 크기에 따라 이 명령을 표시하기 위해 이중 오른쪽 화살표를 클릭해야 할 수 있습니다.
7. **인터넷 프로토콜 버전 4(TCP/IPv4)**를 선택하고 **속성**을 클릭합니다.
8. **다음 DNS 서버 주소 사용**을 선택하고 **기본 설정 DNS 서버**에 **10.10.2.4**를 지정합니다.
9. **10.10.2.4** 주소는 Azure 가상 네트워크에서 10.10.2.0/24 서브넷의 가상 컴퓨터에 할당된 주소입니다. 가상 컴퓨터는 **ContosoDC**입니다. **ContosoDC**의 IP 주소를 확인하려면 다음 스크린샷과 같이 명령 프롬프트 창에서 **nslookup contosodc**를 사용합니다.
   
    ![NSLOOKUP을 사용하여 도메인 컨트롤러에 대한 IP 주소 찾기](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC664954.jpg)
10. **확인** > **닫기**를 클릭하여 변경 내용을 커밋합니다. 이제 가상 컴퓨터를 **corp.contoso.com**에 가입시킬 수 있습니다.
11. **로컬 서버** 창으로 돌아가 **WORKGROUP** 링크를 클릭합니다.
12. **컴퓨터 이름** 섹션에서 **변경**을 클릭합니다.
13. **도메인** 확인란을 선택하고, 텍스트 상자에 **corp.contoso.com**을 입력하고 나서, **확인**을 클릭합니다.
14. **Windows 보안** 대화 상자에서 기본 도메인 관리자 계정에 대한 자격 증명(**CORP\AzureAdmin**) 및 암호(**Contoso!000**)를 지정합니다.
15. "corp.contoso.com 도메인 시작" 메시지가 표시되면 **확인**을 클릭합니다.
16. 대화 상자에서 **닫기** > **지금 다시 시작**을 클릭합니다.

### <a name="add-the-corpinstall-user-as-an-administrator-on-each-virtual-machine"></a>각 가상 컴퓨터에서 Corp\Install 사용자를 관리자로 추가합니다.
1. 가상 컴퓨터가 다시 시작될 때까지 기다렸다가 RDP 파일을 다시 열어 **BUILTIN\AzureAdmin** 계정으로 가상 컴퓨터에 로그인합니다.
2. **서버 관리자**에서 **도구** > **컴퓨터 관리**를 클릭합니다.
   
    ![컴퓨터 관리](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784630.png)
3. **컴퓨터 관리** 대화 상자에서 **로컬 사용자 및 그룹**을 확장한 후 **그룹**을 클릭합니다.
4. **Administrators** 그룹을 두 번 클릭합니다.
5. **Administrators 속성** 대화 상자에서 **추가** 단추를 클릭합니다.
6. **CORP\Install** 사용자를 입력한 후 **확인**을 클릭합니다. 자격 증명에 대한 메시지가 표시되면 **AzureAdmin** 계정과 **Contoso!000** 암호를 사용합니다.
7. **확인**을 클릭하여 **Administrator 속성** 대화 상자를 닫습니다.

### <a name="add-the-failover-clustering-feature-to-each-virtual-machine"></a>장애 조치(Failover) 클러스터링 기능을 각 가상 컴퓨터에 추가
1. **서버 관리자** 대시보드에서 **역할 및 기능 추가**를 클릭합니다.
2. **할 및 기능 추가 마법사**에서 **기능** 페이지가 표시될 때까지 **다음**을 클릭합니다.
3. **장애 조치 클러스터링**을 선택합니다. 메시지가 표시되면 다른 종속 기능을 추가합니다.
   
    ![장애 조치(Failover) 클러스터링 기능을 가상 컴퓨터에 추가](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784631.png)
4. **다음**을 클릭한 후 **확인** 페이지에서 **설치**를 클릭합니다.
5. **장애 조치 클러스터링** 기능 설치가 완료되면 **닫기**를 클릭합니다.
6. 가상 컴퓨터에서 로그아웃합니다.
7. **ContosoWSFCNode**, **ContosoSQL1** 및 **ContosoSQL2**의 세 가지 서버에 대해 모두 이 섹션의 단계를 반복합니다.

이제 SQL Server 가상 컴퓨터가 프로비전되어 실행 중이지만 각 가상 컴퓨터에는 SQL Server에 대한 기본 옵션이 있습니다.

## <a name="create-the-failover-cluster"></a>장애 조치 클러스터 만들기
이 섹션에서는 나중에 만들 가용성 그룹을 호스트하는 장애 조치(failover) 클러스터를 만듭니다. 이제 장애 조치(failover) 클러스터에서 사용할 3개의 가상 컴퓨터 각각에 대해 다음이 완료되어야 합니다.

* Azure에서 가상 컴퓨터가 완전히 프로비전됨
* 가상 컴퓨터가 도메인에 가입됨
* 로컬 관리자 그룹에 **CORP\Install** 추가됨
* 장애 조치(failover) 클러스터링 기능이 추가됨

각 가상 컴퓨터에서 이러한 모든 필수 구성 요소가 준비되어야 장애 조치(failover) 클러스터에 가입할 수 있습니다.

또한 Azure 가상 네트워크는 온-프레미스 네트워크와 다르게 작동합니다. 다음 순서로 클러스터를 만들어야 합니다.

1. 하나의 노드에 단일 노드 클러스터를 만듭니다(**ContosoSQL1**).
2. 클러스터 IP 주소를 사용하지 않는 IP 주소로 수정합니다(**10.10.2.101**).
3. 클러스터 이름을 온라인 상태로 전환합니다.
4. 다른 노드(**ContosoSQL2** 및 **ContosoWSFCNode**)를 추가합니다.

다음 단계를 사용하여 클러스터를 완전히 구성하는 작업을 완료합니다.

1. **ContosoSQL1**에 대한 RDP 파일을 열고 도메인 계정 **CORP\Install**을 사용하여 로그인합니다.
2. **서버 관리자** 대시보드에서 **도구** > **장애 조치(Failover) 클러스터 관리자**를 클릭합니다.
3. 왼쪽 창에서 **장애 조치(Failover) 클러스터 관리자**를 마우스 오른쪽 단추로 클릭하고 다음 스크린샷과 같이 **클러스터 만들기**를 클릭합니다.
   
    ![클러스터 만들기](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784632.png)
4. 클러스터 만들기 마법사에서 페이지를 단계별로 진행하고 다음 표의 설정을 사용하여 1노드 클러스터를 만듭니다.
   
   | Page | 설정 |
   | --- | --- |
   | 시작하기 전에 |기본값 사용 |
   | 서버 선택 |**서버 이름 입력**에 **ContosoSQL1**을 입력하고 **추가**를 클릭합니다. |
   | 유효성 검사 경고 |**아니요. 이 클러스터에 대한 Microsoft의 지원이 필요 없으므로 유효성 검사 테스트를 실행하지 않습니다. [다음]을 클릭하면 클러스터 만들기를 계속합니다.**를 선택합니다. |
   | 클러스터 관리를 위한 액세스 지점 |**클러스터 이름**에 **Cluster1**을 입력합니다. |
   | 다음 |저장소 공간을 사용하지 않는 경우 기본값을 사용합니다. 이 표 다음에 있는 경고를 참조하세요. |
   
   > [!WARNING]
   > 여러 디스크를 저장소 풀로 그룹화하는 [저장소 공간](https://technet.microsoft.com/library/hh831739)을 사용 중인 경우 **확인** 페이지에서 **클러스터에 사용할 수 있는 모든 저장소를 추가하세요** 확인란을 선택 취소해야 합니다. 이 옵션을 선택 취소하지 않으면 가상 디스크가 클러스터 프로세스 중에 분리됩니다. 그 결과, 저장소 공간이 클러스터에서 제거되고 PowerShell을 사용하여 다시 연결할 때까지 디스크 관리자 또는 탐색기에 표시되지 않습니다.
   > 
   > 
5. 왼쪽 창에서 **장애 조치(Failover) 클러스터 관리자**를 확장하고 **Cluster1.corp.contoso.com**을 클릭합니다.
6. 가운데 창에서 **클러스터 코어 리소스** 섹션으로 아래로 스크롤하고 **이름: Clutser1** 세부 정보를 확장합니다. **이름** 및 **IP 주소** 리소스가 **실패** 상태에 모두 표시됩니다. 클러스터에 컴퓨터 자체와 같은 IP 주소가 할당되어 주소가 중복되므로 IP 주소 리소스는 온라인 상태로 전환할 수 없습니다.
7. 오류가 발생한 **IP 주소** 리소스를 마우스 오른쪽 단추로 클릭하고 **속성**을 클릭합니다.
   
    ![클러스터 속성](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784633.png)
8. **고정 IP 주소**를 선택하고, **주소** 텍스트 상자에 **10.10.2.101**을 지정하고 나서, **확인**을 클릭합니다.
9. **클러스터 코어 리소스** 섹션에서 **이름: Cluster1**을 마우스 오른쪽 단추로 클릭하고 **온라인 상태로 전환**을 클릭합니다. 두 리소스가 모두 온라인 상태로 전환될 때까지 기다립니다. 클러스터 이름 리소스가 온라인 상태가 되면 DC 서버는 새 Active Directory 컴퓨터 계정으로 업데이트됩니다. 이 Active Directory 계정은 나중에 가용성 그룹 클러스터된 서비스를 실행하는 데 사용됩니다.
10. 클러스터에 나머지 노드를 추가합니다. 브라우저 트리에서 **Cluster1.corp.contoso.com**을 마우스 오른쪽 단추로 클릭하고 다음 스크린샷과 같이 **노드 추가**를 클릭합니다.
    
     ![클러스터에 노드 추가](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784634.png)
11. **노드 추가 마법사**의 **서버 선택** 페이지에서 **다음**을 클릭하고, **서버 이름 입력**에 서버 이름을 입력하고 **추가**를 클릭하여 **ContosoSQL2** 및 **ContosoWSFCNode**를 목록에 추가합니다. 완료하면 **다음**을 클릭합니다.
12. **유효성 검사 경고** 페이지에서 **아니요**를 클릭합니다. 하지만 프로덕션 시나리오에서는 유효성 검사 테스트를 수행해야 합니다. 그런 후 **다음**을 클릭합니다.
13. **확인** 페이지에서 **다음**을 클릭하여 노드를 추가합니다.
    
    > [!WARNING]
    > 여러 디스크를 저장소 풀로 그룹화하는 [저장소 공간](https://technet.microsoft.com/library/hh831739)을 사용 중인 경우 **클러스터에 사용할 수 있는 모든 저장소를 추가하세요** 확인란을 선택 취소해야 합니다. 이 옵션을 선택 취소하지 않으면 가상 디스크가 클러스터 프로세스 중에 분리됩니다. 그 결과, 저장소 공간이 클러스터에서 제거되고 PowerShell을 사용하여 다시 연결할 때까지 디스크 관리자 또는 탐색기에 표시되지 않습니다.
    > 
    > 
14. 클러스터에 노드가 추가된 후 **마침**을 클릭합니다. 이제 장애 조치(Failover) 클러스터 관리자에 3개의 노드가 포함된 클러스터가 표시되고 **노드** 컨테이너에 목록으로 표시됩니다.
15. 원격 데스크톱 세션에서 로그아웃합니다.

## <a name="prepare-the-sql-server-instances-for-availability-groups"></a>가용성 그룹에 대한 SQL Server 인스턴스 준비
이 섹션에서는 **ContosoSQL1** 및 **contosoSQL2** 모두에 대해 다음을 수행합니다.

* 필요한 권한이 기본 SQL Server 인스턴스로 설정된 **NT AUTHORITY\System**에 대한 로그인을 추가합니다.
* 기본 SQL Server 인스턴스에 sysadmin 역할로 **CORP\Install**을 추가합니다.
* SQL Server의 원격 액세스를 위해 방화벽을 엽니다.
* Always On 가용성 그룹 기능을 사용하도록 설정합니다.
* SQL Server 서비스 계정을 각각 **CORP\SQLSvc1** and **CORP\SQLSvc2**로 변경합니다.

이 작업은 순서와 관계없이 수행할 수 있습니다. 하지만 다음 단계는 순서대로 진행합니다. **ContosoSQL1** 및 **ContosoSQL2** 모두에 대해 다음 단계를 따릅니다.

1. 가상 컴퓨터에 대한 원격 데스크톱 세션에서 로그아웃하지 않은 경우 지금 로그아웃합니다.
2. **ContosoSQL1** 및 **ContosoSQL2**에 대한 RDP 파일을 열고 **BUILTIN\AzureAdmin**으로 로그인합니다.
3. **NT AUTHORITY\System**을 필요한 권한과 함께 SQL Server 로그인에 추가합니다. **SQL Server Management Studio**를 엽니다.
4. **연결** 을 클릭하여 기본 SQL Server 인스턴스에 연결합니다.
5. **개체 탐색기**에서 **보안**을 확장한 다음 **로그인**을 확장합니다.
6. **NT AUTHORITY\System** 로그인을 마우스 오른쪽 단추로 클릭하고 **속성**을 클릭합니다.
7. 로컬 서버에 대한 **보안 개체** 페이지에서 다음 사용 권한에 대해 **권한 부여**를 선택하고 **확인**을 클릭합니다.
   
   * 가용성 그룹 변경
   * SQL 연결
   * 서버 상태 보기
8. 기본 SQL Server 인스턴스에 **sysadmin** 역할로 **CORP\Install**을 추가합니다. **개체 탐색기**에서 **로그인**을 마우스 오른쪽 단추로 클릭하고 **새 로그인**을 클릭합니다.
9. **로그인 이름**에 **CORP\Install**을 입력합니다.
10. **서버 역할** 페이지에서 **sysadmin**을 선택한 다음 **확인**을 클릭합니다. 로그인을 만든 후 **개체 탐색기**에서 **로그인**을 확장하여 로그인을 확인할 수 있습니다.
11. SQL Server에 대한 방화벽 규칙을 만들려면 **시작** 화면에서 **고급 보안이 포함된 Windows 방화벽**을 엽니다.
12. 왼쪽 창에서 **인바운드 규칙**을 선택합니다. 오른쪽 창에서 **새 규칙**을 클릭합니다.
13. **규칙 유형** 페이지에서 **프로그램** > **다음**을 클릭합니다.
14. **프로그램** 페이지에서 **다음 프로그램 경로**를 선택하고, 텍스트 상자에 **%ProgramFiles%\Microsoft SQL Server\MSSQL12.MSSQLSERVER\MSSQL\Binn\sqlservr.exe**를 입력하고 나서, **다음**을 클릭합니다. 이러한 지침을 따르지만 SQL Server 2012를 사용 중인 경우 SQL Server 디렉터리는 **MSSQL11.MSSQLSERVER**입니다.
15. **작업** 페이지에서 **연결 허용**을 선택한 상태로 유지하고 **다음**을 클릭합니다.
16. **프로필** 페이지에서 기본 설정을 그대로 적용하고 **다음**을 클릭합니다.
17. **이름** 페이지에서 **이름** 텍스트 상자에 **SQL Server(프로그램 규칙)**와 같은 규칙 이름을 지정하고 **마침**을 클릭합니다.
18. **Always On 가용성 그룹** 기능을 사용하도록 설정하려면 **시작** 화면에서 **SQL Server 구성 관리자**를 엽니다.
19. 브라우저 트리에서 **SQL Server 서비스**를 클릭하고 **SQL Server (MSSQLSERVER)** 서비스를 마우스 오른쪽 단추로 클릭한 다음 **속성**을 클릭합니다.
20. **Always On 고가용성** 탭을 클릭하고, 다음 스크린샷과 같이 **Always On 가용성 그룹 사용**을 선택하고 나서, **적용**을 클릭합니다. 대화 상자에서 **확인**을 클릭하고 아직 **속성** 대화 상자를 닫지 마세요. 서비스 계정을 변경한 후에 SQL Server 서비스를 다시 시작합니다.
    
     ![Always On 가용성 그룹 사용](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665520.gif)
21. SQL Server 서비스 계정을 변경하려면 **로그온** 탭을 클릭하고, **계정 이름**에 **CORP\SQLSvc1**(**ContosoSQL1**인 경우) 또는 **CORP\SQLSvc2**(**ContosoSQL2**인 경우)를 입력하고, 암호를 입력 및 확인한 후, **확인**을 클릭합니다.
22. 열리는 대화 상자에서 **예**를 클릭하여 SQL Server 서비스를 다시 시작합니다. SQL Server 서비스가 다시 시작된 후 **속성** 대화 상자에서 변경한 내용이 적용됩니다.
23. 가상 컴퓨터에서 로그아웃합니다.

## <a name="create-the-availability-group"></a>가용성 그룹 만들기
이제 가용성 그룹을 구성할 준비가 되었습니다. 다음은 수행할 사항을 간략히 설명합니다.

* **ContosoSQL1**에 새 데이터베이스(**MyDB1**)를 만듭니다.
* 데이터베이스의 전체 백업 및 트랜잭션 로그 백업을 수행합니다.
* **NORECOVERY** 옵션으로 전체 및 로그 백업을 **ContosoSQL2**로 복원합니다.
* 동기 커밋, 자동 장애 조치(failover) 및 읽을 수 있는 보조 복제본으로 가용성 그룹(**AG1**)을 만듭니다.

### <a name="create-the-mydb1-database-on-contososql1"></a>ContosoSQL1에 MyDB1 데이터베이스 만들기
1. **ContosoSQL1** 및 **ContosoSQL2**에 대한 원격 데스크톱 세션에서 아직 로그아웃하지 않은 경우 지금 로그아웃합니다.
2. **ContosoSQL1**에 대한 RDP 파일을 열고 **CORP\Install**로 로그인합니다.
3. **파일 탐색기**에서 **C:\\** 아래에 **backup**이라는 디렉터리를 만듭니다. 이 디렉터리는 데이터베이스를 백업 및 복원하는 데 사용합니다.
4. 새 디렉터리를 마우스 오른쪽 단추로 클릭하고 **공유 대상**을 가리킨 후 다음 스크린샷과 같이 **특정 사용자**를 클릭합니다.
   
    ![백업 폴더 만들기](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665521.gif)
5. **CORP\SQLSvc1**을 추가하고 **읽기/쓰기** 권한을 부여합니다. **CORP\SQLSvc2**를 추가하고, 다음 스크린샷과 같이 **읽기** 권한을 부여하고 나서, **공유**를 클릭합니다. 파일 공유 프로세스가 완료된 후 **완료**를 클릭합니다.
   
    ![백업 폴더에 대한 권한 부여](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665522.gif)
6. 데이터베이스를 만들려면 **시작** 메뉴에서 **SQL Server Management Studio**를 열고 **연결**을 클릭하여 기본 SQL Server 인스턴스에 연결합니다.
7. **개체 탐색기**에서 **데이터베이스**를 마우스 오른쪽 단추로 클릭한 다음 **새 데이터베이스**를 클릭합니다.
8. **데이터베이스 이름**에 **MyDB1**을 입력하고 **확인**을 클릭합니다.

### <a name="make-a-full-backup-of-mydb1-and-restore-it-on-contososql2"></a>MyDB1의 전체 백업 수행 및 ContosoSQL2에 복원
1. 데이터베이스의 전체 백업을 만들려면 **개체 탐색기**에서 **데이터베이스**를 확장하고, **MyDB1**을 마우스 오른쪽 단추로 클릭하고, **작업**을 가리키고 나서, **백업**을 클릭합니다.
2. **원본** 섹션에서 **Backup 유형**을 **전체**로 설정된 상태로 유지합니다. **대상** 섹션에서 **제거**를 클릭하여 백업 파일의 기본 파일 경로를 제거합니다.
3. **대상** 섹션에서 **추가**를 클릭합니다.
4. **파일 이름** 텍스트 상자에 **\\ContosoSQL1\backup\MyDB1.bak**를 입력하고, **확인**을 클릭하고 나서, **확인**을 다시 클릭하여 데이터베이스를 백업합니다. 백업 작업이 완료되면 **확인**을 다시 클릭하여 대화 상자를 닫습니다.
5. 데이터베이스의 트랜잭션 로그 백업을 만들려면 **개체 탐색기**에서 **데이터베이스**를 확장하고, **MyDB1**을 마우스 오른쪽 단추로 클릭하고, **작업**을 가리키고 나서, **백업**을 클릭합니다.
6. **Backup 유형**에서 **트랜잭션 로그**를 선택합니다. **대상** 파일 경로를 앞에서 지정한 경로로 설정된 상태를 유지하고 **확인**을 클릭합니다. 백업 작업이 완료된 후 **확인**을 다시 클릭합니다.
7. **ContosoSQL2**에서 전체 및 트랜잭션 로그 백업을 복원하려면 **ContosoSQL2**에 대한 RDP 파일을 열고 **CORP\Install**로 로그인합니다. **ContosoSQL1** 에 대한 원격 데스크톱 세션은 열린 상태로 둡니다.
8. **시작** 메뉴에서 **SQL Server Management Studio**를 열고 **연결**을 클릭하여 기본 SQL Server 인스턴스에 연결합니다.
9. **개체 탐색기**에서 **데이터베이스**를 마우스 오른쪽 단추로 클릭하고 **데이터베이스 복원**을 클릭합니다.
10. **원본** 섹션에서 **장치**를 선택하고 줄임표(**…**)를 클릭합니다. 단추를 선택합니다.
11. **백업 장치 선택**에서 **추가**를 클릭합니다.
12. **백업 파일 위치**에서 **\\ContosoSQL1\backup**을 입력하고, **새로 고침**을 클릭하고, **MyDB1.bak**를 선택하고, **확인**을 클릭하고 나서, **확인**을 다시 클릭합니다. 그러면 **복원할 Backup 세트** 창에 전체 백업 및 로그 백업이 표시됩니다.
13. **옵션** 페이지로 이동한 후 **복구 상태**에서 **RESTORE WITH NORECOVERY**를 선택하고 **확인**을 클릭하여 데이터베이스를 복원합니다. 복원 작업이 완료된 후 **확인**을 클릭합니다.

### <a name="create-the-availability-group"></a>가용성 그룹 만들기
1. **ContosoSQL1**에 대한 원격 데스크톱 세션으로 돌아갑니다. SQL Server Management Studio의 **개체 탐색기**에서 **Always On 고가용성**을 마우스 오른쪽 단추로 클릭하고 다음 스크린샷과 같이 **새 가용성 그룹 마법사**를 클릭합니다.
   
    ![새 가용성 그룹 마법사 시작](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665523.gif)
2. **소개** 페이지에서 **다음**을 클릭합니다. **가용성 그룹 이름 지정** 페이지에서 **가용성 그룹 이름**에 **AG1**을 입력하고 **다음**을 다시 클릭합니다.
   
    ![새 가용성 그룹 마법사, 가용성 그룹 이름 지정](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665524.gif)
3. **데이터베이스 선택** 페이지에서 **MyDB1**을 선택하고 **다음**을 클릭합니다. 원하는 주 복제본에서 전체 백업을 하나 이상 생성했으므로, 이러한 데이터베이스는 가용성 그룹의 필수 구성 요소를 충족합니다.
   
    ![새 가용성 그룹 마법사, 데이터베이스 선택](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665525.gif)
4. **복제본 지정** 페이지에서 **복제본 추가**를 클릭합니다.
   
    ![새 가용성 그룹 마법사, 복제본 지정](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665526.gif)
5. **서버에 연결** 대화 상자에서 **서버 이름**에 **ContosoSQL2**를 입력하고 **연결**을 클릭합니다.
   
    ![새 가용성 그룹 마법사, 서버에 연결](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665527.gif)
6. **복제본 지정** 페이지로 돌아가면 이제 **사용 가능한 복제본**에 **ContosoSQL2**가 표시됩니다. 다음 스크린샷과 같이 복제본을 구성합니다. 작업을 마쳤으면 **다음**을 클릭합니다.
   
    ![새 가용성 그룹 마법사, 복제본 지정(전체)](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665528.gif)
7. **초기 데이터 동기화 선택** 페이지에서 **조인만**을 선택하고 **다음**을 클릭합니다. **ContosoSQL1**에서 전체 및 트랜잭션 백업을 만들고 이를 **ContosoSQL2**에서 복원할 때 이미 데이터 동기화를 수동으로 수행했습니다. 데이터베이스에서 백업 및 복원 작업을 수행하지 않고 대신에 **전체** 를 선택하여 새 가용성 그룹 마법사가 데이터 동기화를 자동으로 수행하도록 할 수 있습니다. 그러나 일부 기업에서 사용하는 대형 데이터베이스의 경우에는 이 옵션을 사용하지 않는 것이 좋습니다.
   
    ![새 가용성 그룹 마법사, 초기 데이터 동기화 선택.](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665529.gif)
8. **유효성 검사** 페이지에서 **다음**을 클릭합니다. 이 페이지는 다음 스크린샷과 비슷하게 표시됩니다. 가용성 그룹 수신기가 구성되어 있지 않으므로 수신기 구성에 대한 경고가 표시됩니다. 이 자습서에서는 수신기를 구성하지 않으므로 이 경고를 무시해도 됩니다. 이 자습서를 완료한 후 수신기를 구성하려면 [Azure에서 Always On 가용성 그룹을 위해 ILB 수신기 구성](../classic/ps-sql-int-listener.md)을 참조하세요.
   
    ![새 가용성 그룹 마법사, 유효성 검사](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665530.gif)
9. **요약** 페이지에서 **마침**을 클릭한 후 마법사에서 새 가용성 그룹을 구성하는 동안 기다립니다. **진행률** 페이지에서 **자세한 내용**을 클릭하여 자세한 진행 상태를 확인할 수 있습니다. 마법사가 완료된 후 다음 스크린샷과 같이 **결과** 페이지를 검토하여 가용성 그룹이 올바르게 만들어졌는지 확인한 후 **닫기**를 클릭하여 마법사를 종료합니다.
   
    ![새 가용성 그룹 마법사, 결과](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665531.gif)
10. **개체 탐색기**에서 **Always On 고가용성**을 확장하고 **가용성 그룹**을 확장합니다. 이 컨테이너에 새 가용성 그룹이 표시됩니다. **AG1(기본)**을 마우스 오른쪽 단추로 클릭하고 **대시보드 표시**를 클릭합니다.
    
     ![가용성 그룹 대시보드 표시](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665532.gif)
11. **Always On 대시보드**는 다음 스크린샷과 비슷하게 표시됩니다. 복제본, 각 복제본의 장애 조치(failover) 모드 및 동기화 상태를 볼 수 있습니다.
    
     ![가용성 그룹 대시보드](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665533.gif)
12. **서버 관리자**로 돌아가서 **도구**를 클릭하고 **장애 조치(Failover) 클러스터 관리자**를 엽니다.
13. **Cluster1.corp.contoso.com**과 **서비스 및 응용 프로그램**을 차례로 확장합니다. **역할**을 선택하고 **AG1** 가용성 그룹 역할이 만들어진 것을 확인합니다. AG1에는 수신기를 구성하지 않았으므로 데이터베이스 클라이언트가 가용성 그룹에 연결할 수 있는 IP 주소가 없습니다. 읽기/쓰기 작업을 위해 주 노드에 직접 연결하고 읽기 전용 쿼리를 위해 보조 노드에 직접 연결할 수 있습니다.
    
     ![장애 조치(Failover) 클러스터 관리자에서 AG](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665534.gif)

> [!WARNING]
> 장애 조치(Failover) 클러스터 관리자에서 가용성 그룹으로 장애 조치를 시도하지 마세요. 모든 장애 조치(failover) 작업은 SQL Server Management Studio의 **Always On 대시보드**에서 수행해야 합니다. 자세한 내용은 [가용성 그룹에서 장애 조치(Failover) 클러스터 관리자 사용에 대한 제한 사항](https://msdn.microsoft.com/library/ff929171.aspx)을 참조하세요.
> 
> 

## <a name="next-steps"></a>다음 단계
이제 Azure에서 가용성 그룹을 만들어 SQL Server Always On을 성공적으로 구현했습니다. 이 가용성 그룹에 대한 수신기를 구성하려면 [Azure에서 Always On 가용성 그룹에 대한 ILB 수신기 구성](../classic/ps-sql-int-listener.md)을 참조하세요.

Azure에서 SQL Server를 사용하는 방법에 대한 기타 정보는 [Azure Virtual Machines의 SQL Server](../sql/virtual-machines-windows-sql-server-iaas-overview.md)를 참조하세요.

