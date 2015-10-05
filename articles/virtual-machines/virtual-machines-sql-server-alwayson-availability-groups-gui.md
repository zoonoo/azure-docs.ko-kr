<properties
	pageTitle="AlwaysOn 가용성 그룹 구성(GUI) | Microsoft Azure"
	description="Azure 가상 컴퓨터로 AlwaysOn 가용성 그룹을 만듭니다. 이 자습서에서는 스크립트보다는 사용자 인터페이스 및 도구를 주로 사용합니다."
	services="virtual-machines"
	documentationCenter="na"
	authors="rothja"
	manager="jeffreyg"
	editor="monicar"
	tags="azure-service-management" />
<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="08/12/2015"
	ms.author="jroth" />

# Azure VM의 AlwaysOn 가용성 그룹 구성(GUI)

> [AZURE.SELECTOR]
- [Portal](virtual-machines-sql-server-alwayson-availability-groups-gui.md)
- [PowerShell](virtual-machines-sql-server-alwayson-availability-groups-powershell.md)

<br/>

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]이 문서에서는 클래식 배포 모델을 사용하여 리소스를 만드는 방법을 설명합니다.

이 종단 간 자습서에서는 Azure 가상 컴퓨터에서 실행되는 SQL Server AlwaysOn을 사용하여 가용성 그룹을 구현하는 방법을 보여줍니다.

>[AZURE.NOTE]Azure 관리 포털에는 수신기와 함께 AlwaysOn 가용성 그룹을 위한 새로운 갤러리 설치가 있습니다. 설치 시 AlwaysOn 가용성 그룹에 필요한 모든 항목이 자동으로 구성됩니다. 자세한 내용은 [Microsoft Azure 포털 갤러리의 SQL Server AlwaysOn 제품](http://blogs.technet.com/b/dataplatforminsider/archive/2014/08/25/sql-server-alwayson-offering-in-microsoft-azure-portal-gallery.aspx)을 참조하세요. PowerShell을 사용하려면 [PowerShell을 사용하여 Azure에서 AlwaysOn 가용성 그룹 구성](virtual-machines-sql-server-alwayson-availability-groups-powershell.md)에서 동일한 시나리오의 자습서를 참조하세요.

자습서 마지막에서 Azure의 SQL Server AlwaysOn 솔루션은 다음 요소로 구성됩니다.

- 프런트 엔드 및 백 엔드 서브넷을 비롯한 여러 서브넷을 포함하는 가상 네트워크

- AD(Active Directory) 도메인을 포함한 도메인 컨트롤러

- 백 엔드 서브넷에 배포되고 AD 도메인에 가입된 SQL Server VM 2개

- 노드 과반수 쿼럼 모델을 포함하는 3-노드 WSFC 클러스터

- 가용성 데이터베이스의 두 개의 동기 커밋 복제본이 포함된 가용성 그룹

아래 그림은 솔루션을 그래픽으로 표현한 것입니다.

![Azure에서 AG에 대한 테스트 랩 아키텍처](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC791912.png)

이것은 가능한 구성 중 하나입니다. 예를 들어, Azure에서 계산 시간을 줄이기 위해 2노드 WSFC 클러스터에서 쿼럼 파일 공유 감시로 도메인 컨트롤러를 사용하여 두 개의 복제된 가용성 그룹에 대한 VM 수를 최소화할 수 있습니다. 이 방법을 사용하면 위의 구성에서 하나로 VM 수가 줄어듭니다.

이 자습서에서는 다음을 가정합니다.

- Azure 계정이 있습니다.

- GUI를 사용하여 가상 컴퓨터 갤러리에서 SQL Server VM을 프로비전하는 방법을 이미 알고 있습니다. 자세한 내용은 [Azure에서 SQL Server 가상 컴퓨터 프로비전](virtual-machines-provision-sql-server.md)을 참조하세요.

- AlwaysOn 가용성 그룹을 확실하게 이해하고 있습니다. 자세한 내용은 [AlwaysOn 가용성 그룹(SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx)을 참조하세요.

>[AZURE.NOTE]SharePoint와 AlwaysOn 가용성 그룹을 사용하는 것에 관심이 있는 경우 [SharePoint 2013에 대해 SQL Server 2012 AlwaysOn 가용성 그룹 구성](https://technet.microsoft.com/library/jj715261.aspx)을 참조하세요.

## 가상 네트워크 및 도메인 컨트롤러 서버 만들기

새로운 Azure 평가판 계정으로 시작합니다. 계정 설정을 마쳤으면 Azure 포털의 홈 화면이 표시됩니다.

1. 아래와 같이 페이지 왼쪽 하단에서 **새로 만들기** 단추를 클릭합니다.

	![포털에서 새로 만들기 클릭](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665511.gif)

1. **네트워크 서비스**를 클릭한 다음 **가상 네트워크**, **사용자 지정 만들기**를 차례로 클릭합니다.

	![가상 네트워크 만들기](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665512.gif)

1. **가상 네트워크 만들기** 대화 상자에서 아래 설정으로 페이지를 단계별로 진행하여 새 가상 네트워크를 만듭니다.

	|Page|설정|
|---|---|
|가상 네트워크 세부 정보|**이름 = ContosoNET**<br/>**지역 = 미국 서부**|
|DNS 서버 및 VPN 연결|없음|
|가상 네트워크 주소 공간|설정이 아래 스크린샷에 나와 있습니다. ![가상 네트워크 만들기](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC784620.png)|

1. 다음으로 도메인 컨트롤러(DC)로 사용할 VM을 만듭니다. 다시 **새로 만들기**를 클릭하고, 아래와 같이 **계산**, **가상 컴퓨터**, **갤러리에서**를 차례로 클릭합니다.

	![VM 만들기](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC784621.png)

1. **가상 컴퓨터 만들기** 대화 상자에서 아래 설정으로 페이지를 단계별로 진행하여 새 VM을 구성합니다.

	|Page|설정|
|---|---|
|가상 컴퓨터 운영 체제 선택|Windows Server 2012 R2 Datacenter|
|가상 컴퓨터 구성|**버전 릴리스 날짜** = (최신)<br/>**가상 컴퓨터 이름** = ContosoDC<br/>**계층** = 기본<br/>**크기** = A2(2코어)<br/>**새 사용자 이름** = AzureAdmin<br/>**새 암호** = Contoso!000<br/>**확인** = Contoso!000|
|가상 컴퓨터 구성|**클라우드 서비스** = 새 클라우드 서비스 만들기<br/>**클라우드 서비스 DNS 이름** = 고유한 클라우드 서비스 이름<br/>**DNS 이름** = 고유한 이름(예: ContosoDC123)<br/>**지역/선호도 그룹/가상 네트워크** = ContosoNET<br/>**가상 네트워크 서브넷** = Back(10.10.2.0/24)<br/>**저장소 계정** = 자동으로 생성된 저장소 계정 사용<br/>**가용성 집합** = (없음)|
|가상 컴퓨터 옵션|기본값 사용|

새 VM을 구성했으면 VM이 프로비전될 때까지 기다립니다. 이 과정은 완료하는 데 다소 시간이 소요되며 Azure 포털에서 **가상 컴퓨터** 탭을 클릭하면 ContosoDC 순환 상태가 **시작 중(프로비전)**에서 **중지됨**, **시작 중**, **실행 중(프로비전)**, 마지막으로 **실행 중**으로 표시될 수 있습니다.

이제 DC 서버가 성공적으로 프로비전되었습니다. 다음으로 이 DC 서버에 Active Directory 도메인을 구성합니다.

## 도메인 컨트롤러 구성

다음 단계에서는 corp.contoso.com에 대한 도메인 컨트롤러로 ContosoDC 컴퓨터를 구성합니다.

1. 포털에서 **ContosoDC** 컴퓨터를 선택합니다. **대시보드** 탭에서 **연결**을 클릭하여 원격 데스크톱 액세스를 위한 RDP 파일을 엽니다.

	![가상 컴퓨터에 연결](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC784622.png)

1. 구성된 관리자 계정(**\\AzureAdmin**) 및 암호(**Contoso!000**)로 로그인합니다.

1. 기본적으로 **서버 관리자** 대시보드가 표시됩니다.

1. 대시보드에서 **역할 및 기능 추가** 링크를 클릭합니다.

	![서버 탐색기 역할 추가](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC784623.png)

1. **서버 역할** 섹션으로 이동할 때까지 **다음**을 선택합니다.

1. **Active Directory 도메인 서비스** 및 **DNS 서버** 역할을 선택합니다. 메시지가 표시되면 이러한 역할에 필요한 기능을 더 추가합니다.

	>[AZURE.NOTE]고정 IP 주소가 없다는 유효성 검사 경고 페이지가 표시됩니다. 구성을 테스트하는 경우 계속을 클릭합니다. 프로덕션 시나리오의 경우 [PowerShell을 사용하여 도메인 컨트롤러 컴퓨터의 고정 IP 주소를 설정](./virtual-network/virtual-networks-reserved-private-ip.md)합니다.

	![역할 추가 대화 상자](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC784624.png)

1. **확인** 섹션에 도달할 때까지 **다음**을 클릭합니다. **필요한 경우 자동으로 대상 서버 다시 시작** 확인란을 선택합니다.

1. **Install**을 클릭합니다.

1. 기능 설치를 완료한 후에는 **서버 관리자** 대시보드로 돌아갑니다.

1. 왼쪽 창에서 새 **AD DS** 옵션을 선택합니다.

1. 노란색 경고 표시줄에서 **자세히** 링크를 클릭합니다.

	![DNS 서버 VM에서 AD DS 대화 상자](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC784625.png)

1. **모든 서버 작업 세부 정보** 대화 상자의 **작업** 열에서 **이 서버를 도메인 컨트롤러로 승격**을 클릭합니다.

1. **Active Directory 도메인 서비스 구성 마법사**에서 다음 값을 사용합니다.

	|Page|설정|
|---|---|
|배포 구성|**새 포리스트 추가** = 선택<br/>**루트 도메인 이름** = corp.contoso.com|
|도메인 컨트롤러 옵션|**암호** = Contoso!000<br/>**암호 확인** = Contoso!000|

1. **다음**을 클릭하여 마법사의 다른 페이지를 진행합니다. **필수 구성 요소 확인** 페이지에서 다음 메시지가 표시되는지 확인합니다. **모든 필수 구성 요소 검사를 마쳤습니다**. 해당하는 모든 경고 메시지를 검토해야 하지만 설치는 계속할 수 있습니다.

1. **Install**을 클릭합니다. **ContosoDC** 가상 컴퓨터가 자동으로 다시 부팅됩니다.

## 도메인 계정 구성

다음 단계에서는 나중에 사용하기 위해 AD(Active Directory) 계정을 구성합니다.

1. **ContosoDC** 컴퓨터에 다시 로그인합니다.

1. **서버 관리자**에서 **도구**를 선택한 후 **Active Directory 관리 센터**를 클릭합니다.

	![Active Directory 관리 센터](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC784626.png)

1. **Active Directory 관리 센터**의 왼쪽 창에서 **corp(로컬)**을 선택합니다.

1. 오른쪽 **작업** 창에서 **새로 만들기**를 선택한 후 **사용자**를 클릭합니다. 다음 설정을 사용합니다.

	|설정|값|
|---|---|
|**이름**|설치|
|**사용자 SamAccountName**|설치|
|**암호**|Contoso!000|
|**암호 확인**|Contoso!000|
|**기타 암호 옵션**|선택|
|**암호 사용 기간 제한 없음**|선택|

1. **확인**을 클릭하여 **Install** 사용자를 만듭니다. 이 계정이 장애 조치(Failover) 클러스터 및 가용성 그룹을 구성하는 데 사용됩니다.

1. 동일한 단계로 **CORP\\SQLSvc1** 및 **CORP\\SQLSvc2**의 추가 사용자 2개를 만듭니다. 이러한 계정은 SQL Server 인스턴스에 사용됩니다. 다음으로 WSFC(Windows Service Failover Clustering)를 구성하는 데 필요한 권한인 **CORP\\Install**을 부여해야 합니다.

1. **Active Directory 관리 센터**의 왼쪽 창에서 **corp(로컬)**을 선택합니다. 다음으로 오른쪽 **작업** 창에서 **속성**을 클릭합니다.

	![CORP 사용자 속성](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC784627.png)

1. **확장**을 선택한 후 **보안** 탭에서 **고급** 단추를 클릭합니다.

1. **corp 고급 보안 설정** 대화 상자에서 **추가**를 클릭합니다.

1. **보안 주체 선택**을 클릭합니다. 그런 다음 **CORP\\Install**을 검색합니다. **확인**을 클릭합니다.

1. **모든 속성 읽기** 및 **컴퓨터 개체 만들기** 권한을 선택합니다.

	![Corp 사용자 권한](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC784628.png)

1. **확인**을 클릭한 후 **확인**을 한 번 더 클릭합니다. corp 속성 창을 닫습니다.

Active Directory 및 사용자 개체 구성을 완료하면 3개의 SQL Server VM이 만들어져 이 도메인에 연결됩니다.

## SQL Server VM 만들기

다음으로 WSFC 클러스터 노드 1개와 SQL Server VM 2개를 포함하는 VM을 3개 만듭니다. 각 VM을 만들려면 Azure 포털로 돌아가 **새로 만들기**, **계산**, **가상 컴퓨터**, **갤러리에서**를 클릭합니다. 다음으로 아래 표의 템플릿을 사용하면 VM을 만드는 데 도움이 됩니다.

|Page|VM1|VM2|VM3|
|---|---|---|---|
|가상 컴퓨터 운영 체제 선택|**Windows Server 2012 R2 Datacenter**|**SQL Server 2014 RTM Enterprise**|**SQL Server 2014 RTM Enterprise**|
|가상 컴퓨터 구성|**버전 릴리스 날짜** = (최신)<br/>**가상 컴퓨터 이름** = ContosoWSFCNode<br/>**계층** = 기본<br/>**크기** = A2(2코어)<br/>**새 사용자 이름** = AzureAdmin<br/>**새 암호** = Contoso!000<br/>**확인** = Contoso!000|**버전 릴리스 날짜** = (최신)<br/>**가상 컴퓨터 이름** = ContosoSQL1<br/>**계층** = 기본<br/>**크기** = A3(4코어)<br/>**새 사용자 이름** = AzureAdmin<br/>**새 암호** = Contoso!000<br/>**확인** = Contoso!000|**버전 릴리스 날짜** = (최신)<br/>**가상 컴퓨터 이름** = ContosoSQL2<br/>**계층** = 기본<br/>**크기** = A3(4코어)<br/>**새 사용자 이름** = AzureAdmin<br/>**새 암호** = Contoso!000<br/>**확인** = Contoso!000|
|가상 컴퓨터 구성|**클라우드 서비스** = 이전에 만든 고유한 클라우드 서비스 DNS 이름(예: ContosoDC123)<br/>**지역/선호도 그룹/가상 네트워크** = ContosoNET<br/>**가상 네트워크 서브넷** = Back(10.10.2.0/24)<br/>**저장소 계정** = 자동으로 생성된 저장소 계정 사용<br/>**가용성 집합** = 가용성 집합 만들기<br/>**가용성 집합 이름** = SQLHADR|**클라우드 서비스** = 이전에 만든 고유한 클라우드 서비스 DNS 이름(예: ContosoDC123)<br/>**지역/선호도 그룹/가상 네트워크** = ContosoNET<br/>**가상 네트워크 서브넷** = Back(10.10.2.0/24)<br/>**저장소 계정** = 자동으로 생성된 저장소 계정 사용<br/>**가용성 집합** = SQLHADR(컴퓨터를 만든 후 가용성 집합을 구성할 수도 있습니다. SQLHADR 가용성 집합에 3개의 컴퓨터를 모두 할당합니다.)|**클라우드 서비스** = 이전에 만든 고유한 클라우드 서비스 DNS 이름(예: ContosoDC123)<br/>**지역/선호도 그룹/가상 네트워크** = ContosoNET<br/>**가상 네트워크 서브넷** = Back(10.10.2.0/24)<br/>**저장소 계정** = 자동으로 생성된 저장소 계정 사용<br/>**가용성 집합** = SQLHADR(컴퓨터를 만든 후 가용성 집합을 구성할 수도 있습니다. SQLHADR 가용성 집합에 3개의 컴퓨터를 모두 할당합니다.)|
|가상 컴퓨터 옵션|기본값 사용|기본값 사용|기본값 사용|

3개의 VM이 완전히 프로비전되면 VM을 **corp.contoso.com** 도메인에 연결하고 컴퓨터에 CORP\\Install 관리 권한을 부여해야 합니다. 이렇게 하려면 3개의 VM 각각에 대해 다음 단계를 사용합니다.

1. 먼저 기본 설정된 DNS 서버 주소를 변경합니다. 목록에서 VM을 선택하고 **연결** 단추를 클릭하여 각 VM의 원격 데스크톱(RDP) 파일을 로컬 디렉터리로 다운로드하는 것으로 시작합니다. VM을 선택하려면 아래와 같이 행의 첫 번째 셀을 제외하고 아무 곳이나 클릭합니다.

	![RDP 파일 다운로드](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC664953.jpg)

1. 다운로드한 RDP 파일을 시작하고 구성된 관리자 계정(**BUILTIN\\AzureAdmin**) 및 암호(**Contoso!000**)를 사용하여 VM에 로그인 합니다.

1. 로그인하면 **서버 관리자** 대시보드가 표시됩니다. 왼쪽 창에서 **로컬 서버**를 클릭합니다.

1. **DHCP에 의해 할당된 IPv4 주소, IPv6 사용 가능** 링크를 선택합니다.

1. **네트워크 연결** 창에서 네트워크 아이콘을 선택합니다.

	![VM 기본 설정 DNS 서버 변경](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC784629.png)

1. 명령 모음에서 **이 연결의 설정 변경**(창의 크기에 따라 명령을 표시하기 위해 이중 오른쪽 화살표를 클릭해야 할 수 있음)을 클릭합니다.

1. **인터넷 프로토콜 버전 4(TCP/IPv4)**를 선택하고 속성을 클릭합니다.

1. 다음 DNS 서버 주소 사용을 선택하고 **기본 설정 DNS 서버**에 **10.10.2.4**를 지정합니다.

1. 주소 **10.10.2.4**는 Azure 가상 네트워크에서 10.10.2.0/24 서브넷의 VM에 할당된 주소이며 해당 VM은 **ContosoDC**입니다. **ContosoDC**의 IP 주소를 확인하려면 아래와 같이 명령 프롬프트에서 **nslookup contosodc**를 사용합니다.

	![NSLOOKUP을 사용하여 DC에 대한 IP 주소 찾기](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC664954.jpg)

1. **확인**을 클릭한 후 **닫기**를 클릭하여 변경 내용을 커밋합니다. 이제 VM을 **corp.contoso.com**에 연결할 수 있습니다.

1. **로컬 서버** 창으로 돌아가 **WORKGROUP** 링크를 클릭합니다.

1. **컴퓨터 이름** 섹션에서 **변경**을 클릭합니다.

1. **도메인** 확인란을 선택하고 텍스트 상자에 **corp.contoso.com**을 입력합니다. **확인**을 클릭합니다.

1. **Windows 보안** 팝업 대화 상자에서 기본 도메인 관리자 계정에 대한 자격 증명(**CORP\\AzureAdmin**) 및 암호(**Contoso!000**)를 지정합니다.

1. "corp.contoso.com 도메인 시작" 메시지가 표시되면 **확인**을 클릭합니다.

1. **닫기**를 클릭한 후 팝업 대화 상자에서 **지금 다시 시작**을 클릭합니다.

### 각 VM에서 Corp\\Install 사용자를 관리자로 추가합니다.

1. VM이 다시 시작될 때까지 기다렸다가 RDP 파일을 다시 시작하여 **BUILTIN\\AzureAdmin** 계정으로 VM에 로그인합니다.

1. **서버 관리자**에서 **도구**를 선택하고 **컴퓨터 관리**를 클릭합니다.

	![컴퓨터 관리](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC784630.png)

1. **컴퓨터 관리** 창에서 **로컬 사용자 및 그룹**을 확장한 후 **그룹**을 선택합니다.

1. **Administrators** 그룹을 두 번 클릭합니다.

1. **Administrators 속성** 대화 상자에서 **추가** 단추를 클릭합니다.

1. **CORP\\Install** 사용자를 입력한 후 **확인**을 클릭합니다. 자격 증명에 대한 메시지가 표시되면 **AzureAdmin** 계정과 **Contoso!000** 암호를 사용합니다.

1. **확인**을 클릭하여 **Administrator 속성** 대화 상자를 닫습니다.

### **장애 조치 클러스터링** 기능을 각 VM에 추가합니다.

1. **서버 관리자** 대시보드에서 **역할 및 기능 추가**를 클릭합니다.

1. **역할 및 기능 추가 마법사**에서 **기능** 페이지가 표시될 때까지 **다음**을 클릭합니다.

1. **장애 조치 클러스터링**을 선택합니다. 메시지가 표시되면 다른 종속 기능을 추가합니다.

	![장애 조치(Failover) 클러스터링 기능을 VM에 추가](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC784631.png)

1. **다음**을 클릭한 후 **확인** 페이지에서 **설치**를 클릭합니다.

1. **장애 조치 클러스터링** 기능 설치가 완료되면 **닫기**를 클릭합니다.

1. VM에서 로그아웃합니다.

1. **ContosoWSFCNode**, **ContosoSQL1**, **ContosoSQL2**의 3가지 모든 서버에 대해 이 섹션의 단계를 반복합니다.

SQL Server VM이 프로비전되어 실행 중이지만 기본 옵션으로 SQL Server에 설치했습니다.

## WSFC 클러스터 만들기

이 섹션에서는 나중에 만들 가용성 그룹을 호스팅하는 WSFC 클러스터를 만듭니다. 이제 WSFC 클러스터에서 사용할 3개의 VM 각각에 대해 다음이 완료되어야 합니다.

- Azure에서 완전히 프로비전

- VM이 도메인에 연결됨

- 로컬 관리자 그룹에 **CORP\\Install** 추가됨

- 장애 조치 클러스터링 기능 추가됨

각 VM에서 이러한 모든 필수 구성 요소가 준비되어야 WSFC 클러스터에 연결할 수 있습니다.

또한 Azure 가상 네트워크는 온-프레미스 네트워크와 다르게 작동합니다. 다음 순서로 클러스터를 만들어야 합니다.

1. 하나의 노드에 단일 노드 클러스터를 만듭니다(**ContosoSQL1**).

1. 클러스터 IP 주소를 사용하지 않는 IP 주소로 수정합니다(**10.10.2.101**).

1. 클러스터 이름을 온라인 상태로 전환합니다.

1. 다른 노드를 추가합니다(**ContosoSQL2** 및 **ContosoWSFCNode**).

아래 단계에 따라 클러스터를 완전히 구성하는 작업을 수행합니다.

1. **ContosoSQL1**에 대한 RDP 파일을 시작하고 도메인 계정 **CORP\\Install**을 사용하여 로그인합니다.

1. **서버 관리자** 대시보드에서 **도구**를 선택한 후 **장애 조치(Failover) 클러스터 관리자**를 클릭합니다.

1. 왼쪽 창에서 **장애 조치(Failover) 클러스터 관리자**를 마우스 오른쪽 단추로 클릭하고 아래와 같이 **클러스터 만들기**를 클릭합니다.

	![클러스터 만들기](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC784632.png)

1. 클러스터 만들기 마법사에서 아래 설정으로 페이지를 단계별로 진행하여 1노드 클러스터를 만듭니다.

	|Page|설정|
|---|---|
|시작하기 전에|기본값 사용|
|서버 선택|**서버 이름 입력**에 **ContosoSQL1**을 입력하고 **추가**를 클릭합니다.|
|유효성 검사 경고|**아니요. 이 클러스터에 대한 Microsoft의 지원이 필요 없으므로 유효성 검사 테스트를 실행하지 않습니다. [다음]을 클릭하면 클러스터 만들기를 계속합니다.**를 선택합니다.|
|클러스터 관리를 위한 액세스 지점|**클러스터 이름**에 **Cluster1**을 입력합니다.|
|확인 페이지|저장소 공간을 사용하지 않는 경우 기본값을 사용합니다. 이 표 다음의 참고 사항을 참조하세요.|

	>[AZURE.WARNING]여러 디스크를 저장소 풀로 그룹화하는 [저장소 공간](https://technet.microsoft.com/library/hh831739)을 사용 중인 경우 **확인** 페이지에서 **클러스터에 사용할 수 있는 모든 저장소를 추가하세요** 확인란을 선택 취소해야 합니다. 이 옵션을 선택 취소하지 않으면 가상 디스크가 클러스터 프로세스 중에 분리됩니다. 그 결과, 저장소 공간이 클러스터에서 제거되고 PowerShell을 사용하여 다시 연결할 때까지 디스크 관리자 또는 탐색기에 표시되지 않습니다.

1. 왼쪽 창에서 **장애 조치(Failover) 클러스터 관리자**를 확장하고 **Cluster1.corp.contoso.com**을 클릭합니다.

1. 가운데 창에서 **클러스터 코어 리소스** 섹션으로 아래로 스크롤하고 **이름: Clutser1** 세부 정보를 확장합니다. **이름** 및 **IP 주소** 리소스가 **실패** 상태에 모두 표시됩니다. 클러스터에 컴퓨터 자체와 같은 IP 주소가 할당되어 주소가 중복되므로 IP 주소 리소스는 온라인 상태로 전환할 수 없습니다.

1. 오류가 발생한 **IP 주소** 리소스를 마우스 오른쪽 단추로 클릭하고 **속성**을 클릭합니다.

	![클러스터 속성](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC784633.png)

1. **고정 IP 주소**를 선택하고 주소 텍스트 상자에 **10.10.2.101**을 지정합니다. 그런 다음 **확인**을 클릭합니다.

1. **클러스터 코어 리소스** 섹션에서 **이름: Cluster1**을 마우스 오른쪽 단추로 클릭하고 **온라인 상태로 전환**을 클릭합니다. 그런 다음 두 리소스가 모두 온라인 상태로 전환될 때까지 기다립니다. 클러스터 이름 리소스가 온라인 상태가 되면 새 AD 컴퓨터 계정으로 DC 서버를 업데이트합니다. 이 AD 계정은 나중에 가용성 그룹 클러스터된 서비스를 실행하는 데 사용됩니다.

1. 마지막으로 클러스터에 나머지 노드를 추가합니다. 아래와 같이 브라우저 트리에서 **Cluster1.corp.contoso.com**을 마우스 오른쪽 단추로 클릭하고 **노드 추가**를 클릭합니다.

	![클러스터에 노드 추가](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC784634.png)

1. **노드 추가 마법사**에서 **다음**을 클릭합니다. **서버 선택** 페이지에서 **서버 이름 입력**에 서버 이름을 입력한 후 **추가**를 클릭하여 **ContosoSQL2** 및 **ContosoWSFCNode**를 목록에 추가합니다. 완료하면 **다음**을 클릭합니다.

1. **유효성 검사 경고** 페이지에서 **아니요**(유효성 검사 테스트를 수행할 프로덕션 시나리오에서)를 클릭합니다. 그런 후 **다음**을 클릭합니다.

1. **확인** 페이지에서 **다음**을 클릭하여 노드를 추가합니다.

	>[AZURE.WARNING]여러 디스크를 저장소 풀로 그룹화하는 [저장소 공간](https://technet.microsoft.com/library/hh831739)을 사용 중인 경우 **클러스터에 사용할 수 있는 모든 저장소를 추가하세요** 확인란을 선택 취소해야 합니다. 이 옵션을 선택 취소하지 않으면 가상 디스크가 클러스터 프로세스 중에 분리됩니다. 그 결과, 저장소 공간이 클러스터에서 제거되고 PowerShell을 사용하여 다시 연결할 때까지 디스크 관리자 또는 탐색기에 표시되지 않습니다.

1. 클러스터에 노드가 추가되면 **마침**을 클릭합니다. 이제 장애 조치(Failover) 클러스터 관리자에 3개의 노드가 포함된 클러스터가 표시되고 **노드** 컨테이너에 목록으로 표시됩니다.

1. 원격 데스크톱 세션에서 로그아웃합니다.

## 가용성 그룹에 대한 SQL Server 인스턴스 준비

이 섹션에서는 **ContosoSQL1** 및 **contosoSQL2** 모두에 대해 다음을 수행합니다.

- 필요한 권한이 기본 SQL Server 인스턴스로 설정된 **NT AUTHORITY\\System**에 대한 로그인 추가

- 기본 SQL Server 인스턴스에 sysadmin 역할로 **CORP\\Install** 추가

- SQL Server의 원격 액세스를 위한 방화벽 열기

- AlwaysOn 가용성 그룹 기능 사용

- SQL Server 서비스 계정을 **CORP\\SQLSvc1** 및 **CORP\\SQLSvc2**로 각각 변경

이 작업은 순서와 관계없이 수행할 수 있습니다. 하지만 아래 단계는 순서대로 진행합니다. **ContosoSQL1** 및 **ContosoSQL2** 모두에 대해 다음 단계를 따릅니다.

1. VM에 대한 원격 데스크톱 세션에서 로그아웃하지 않은 경우 지금 로그아웃합니다.

1. **ContosoSQL1** 및 **ContosoSQL2**에 대한 RDP 파일을 시작하고 **BUILTIN\\AzureAdmin**으로 로그인합니다.

1. 먼저, **NT AUTHORITY\\System**을 필요한 권한과 함께 SQL Server 로그인에 추가합니다. **SQL Server Management Studio**를 시작합니다.

1. **연결**을 클릭하여 기본 SQL Server 인스턴스에 연결합니다.

1. **개체 탐색기**에서 **보안**을 확장한 다음 **로그인**을 확장합니다.

1. **NT AUTHORITY\\System** 로그인을 마우스 오른쪽 단추로 클릭하고 **속성**을 클릭합니다.

1. 로컬 서버에 대한 **보안 개체** 페이지에서 다음 사용 권한에 대해 **권한 부여**를 선택하고 **확인**을 클릭합니다.

	- 가용성 그룹 변경

	- SQL 연결

	- 서버 상태 보기

1. 다음으로 기본 SQL Server 인스턴스에 **sysadmin** 역할로 **CORP\\Install**을 추가합니다. **개체 탐색기**에서 **로그인**을 마우스 오른쪽 단추로 클릭하고 **새 로그인**을 클릭합니다.

1. **로그인 이름**에 **CORP\\Install**을 입력합니다.

1. **서버 역할** 페이지에서 **sysadmin**을 선택합니다. 그런 다음 **확인**을 클릭합니다. 로그인이 생성되면 **개체 탐색기**에서 **로그인**을 확장하여 확인할 수 있습니다.

1. 다음으로 SQL Server에 대한 새 방화벽 규칙을 만듭니다. **시작** 화면에서 **고급 보안이 포함된 Windows 방화벽**을 시작합니다.

1. 왼쪽 창에서 **인바운드 규칙**을 선택합니다. 오른쪽 창에서 **새 규칙**을 클릭합니다.

1. **규칙 종류** 페이지에서 **프로그램**을 선택한 후 **다음**을 클릭합니다.

1. **프로그램** 페이지에서 **다음 프로그램 경로**를 선택하고 텍스트 상자에 **%ProgramFiles%\\Microsoft SQL Server\\MSSQL12.MSSQLSERVER\\MSSQL\\Binn\\sqlservr.exe**를 입력합니다(이 지침을 따르지만 SQL Server 2012를 사용하는 경우 SQL Server 디렉터리는 **MSSQL11.MSSQLSERVER**임). 그런 후 **Next**를 클릭합니다.

1. **작업** 페이지에서 **연결 허용**을 선택한 상태로 유지하고 **다음**을 클릭합니다.

1. **프로필** 페이지에서 기본 설정을 그대로 적용하고 **다음**을 클릭합니다.

1. **이름** 페이지에서 **이름** 텍스트 상자에 **SQL Server(프로그램 규칙)**와 같은 규칙 이름을 지정하고 **마침**을 클릭합니다.

1. 다음으로, **AlwaysOn 가용성 그룹** 기능을 사용하도록 설정합니다. **시작** 화면에서 **SQL Server 구성 관리자**를 시작합니다.

1. 브라우저 트리에서 **SQL Server 서비스**를 클릭하고 **SQL Server(MSSQLSERVER)** 서비스를 마우스 오른쪽 단추로 클릭한 다음 **속성**을 클릭합니다.

1. **AlwaysOn 고가용성** 탭을 클릭한 후 **AlwaysOn 가용성 그룹 사용**을 선택한 후에 아래와 같이 **적용**을 클릭합니다. 팝업 대화 상자에서 **확인**을 클릭하고 아직 속성 창을 닫지 마세요. 서비스 계정을 변경한 후에 SQL Server 서비스를 다시 시작합니다.

	![AlwaysOn 가용성 그룹 사용](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665520.gif)

1. 다음으로, SQL Server 서비스 계정을 변경합니다. **로그온** 탭을 클릭한 후 **계정 이름**에 **CORP\\SQLSvc1**(**ContosoSQL1**인 경우) 또는 **CORP\\SQLSvc2**(**ContosoSQL2**인 경우)를 입력한 후 암호를 입력 및 확인한 후 **확인**을 클릭합니다.

1. 팝업 창에서 **예**를 클릭하여 SQL Server 서비스를 다시 시작합니다. SQL Server 서비스가 다시 시작되면 속성 창에서 변경한 내용이 적용됩니다.

1. VM에서 로그아웃합니다.

## 가용성 그룹 만들기

이제 가용성 그룹을 구성할 준비가 되었습니다. 다음은 수행할 사항을 간략히 설명합니다.

- **ContosoSQL1**에 새 데이터베이스(**MyDB1**) 만들기

- 데이터베이스의 전체 백업 및 트랜잭션 로그 백업 수행

- **NORECOVERY** 옵션으로 전체 및 로그 백업을 **ContosoSQL2**로 복원

- 동기 커밋, 자동 장애 조치(failover) 및 읽을 수 있는 보조 복제본으로 가용성 그룹 만들기(**AG1**)

### ContosoSQL1에 MyDB1 데이터베이스 만들기:

1. **ContosoSQL1** 및 **ContosoSQL2**에 대한 원격 데스크톱 세션에서 아직 로그아웃하지 않은 경우 지금 로그아웃합니다.

1. **ContosoSQL1**에 대한 RDP 파일을 시작하고 **CORP\\Install**로 로그인합니다.

1. **파일 탐색기**에서 **C:** 아래에 **backup** 이라는 디렉터리를 만듭니다. 이 디렉터리는 데이터베이스를 백업 및 복원하는 데 사용합니다.

1. 새 디렉터리를 마우스 오른쪽 단추로 클릭하고 **공유 대상**을 가리킨 후 아래와 같이 **특정 사용자**를 클릭합니다.

	![백업 폴더 만들기](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665521.gif)

1. **CORP\\SQLSvc1**을 추가하고 **읽기/쓰기** 권한을 부여한 후 **CORP\\SQLSvc2**를 추가하고 **읽기** 권한을 부여한 후 아래와 같이 **공유**를 클릭합니다. 파일 공유 프로세스가 완료되면 **완료**를 클릭합니다.

	![백업 폴더에 대한 권한 부여](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665522.gif)

1. 다음으로 데이터베이스를 만듭니다.. **시작** 메뉴에서 **SQL Server Management Studio**를 시작한 후 **연결**을 클릭하여 기본 SQL Server 인스턴스에 연결합니다.

1. **개체 탐색기**에서 **데이터베이스**를 마우스 오른쪽 단추로 클릭하고 **새 데이터베이스**를 클릭합니다.

1. **데이터베이스 이름**에 **MyDB1**을 입력하고 **확인**을 클릭합니다.

### MyDB1의 전체 백업 수행 및 ContosoSQL2에 복원:

1. 다음으로 데이터베이스의 전체 백업을 수행합니다. **개체 탐색기**에서 **데이터베이스**를 확장한 후 **MyDB1**을 마우스 오른쪽 단추로 클릭한 다음 **작업**을 가리키고 **백업**을 클릭합니다.

1. **원본** 섹션에서 **백업 유형**을 **전체**로 설정된 상태로 유지합니다. **대상** 섹션에서 **제거**를 클릭하여 백업 파일의 기본 파일 경로를 제거합니다.

1. **대상** 섹션에서 **추가**를 클릭합니다.

1. **파일 이름** 텍스트 상자에서 **\\ContosoSQL1\\backup\\MyDB1.bak**를 입력합니다. 그런 다음 **확인**을 클릭한 후 **확인**을 한 번 더 클릭하여 데이터베이스를 백업합니다. 백업 작업이 완료되면 **확인**을 한 번 더 클릭하여 대화 상자를 닫습니다.

1. 다음으로 데이터베이스의 트랜잭션 로그 백업을 수행합니다. **개체 탐색기**에서 **데이터베이스**를 확장한 후 **MyDB1**을 마우스 오른쪽 단추로 클릭한 다음 **작업**을 가리키고 **백업**을 클릭합니다.

1. **백업 유형**에서 **트랜잭션 로그**를 선택합니다. **대상** 파일 경로를 앞에서 지정한 경로로 설정된 상태를 유지하고 **확인**을 클릭합니다. 백업 작업이 완료되면 **확인**을 한 번 더 클릭합니다.

1. 다음으로 **ContosoSQL2**에서 전체 및 트랜잭션 로그 백업을 복원합니다. **ContosoSQL2**에 대한 RDP 파일을 시작하고 **CORP\\Install**로 로그인합니다. **ContosoSQL1**에 대한 원격 데스크톱 세션은 열린 상태로 둡니다.

1. **시작** 메뉴에서 **SQL Server Management Studio**를 시작한 후 **연결**을 클릭하여 기본 SQL Server 인스턴스에 연결합니다.

1. **개체 탐색기**에서 **데이터베이스**를 마우스 오른쪽 단추로 클릭하고 **데이터베이스 복원**을 클릭합니다.

1. **원본** 섹션에서 **장치**를 선택하고 **…** 단추를 클릭합니다.

1. **백업 장치 선택**에서 **추가**를 클릭합니다.

1. 백업 파일 위치에서 \\ContosoSQL1\\backup을 입력한 후 새로 고침을 클릭한 다음 MyDB1.bak를 선택하고 확인을 클릭한 후 확인을 한 번 더 클릭합니다. 그러면 복원할 백업 세트 창에 전체 백업 및 로그 백업이 표시됩니다.

1. 옵션 페이지로 이동한 후 복구 상태에서 RESTORE WITH NORECOVERY를 선택하고 확인을 클릭하여 데이터베이스를 복원합니다. 복원 작업이 완료되면 확인을 클릭합니다.

### 가용성 그룹 만들기:

1. **ContosoSQL1**에 대한 원격 데스크톱 세션으로 돌아갑니다. SSMS의 **개체 탐색기**에서 **AlwaysOn 고가용성**을 마우스 오른쪽 단추로 클릭하고 아래와 같이 **새 가용성 그룹 마법사**를 클릭합니다.

	![새 가용성 그룹 마법사 시작](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665523.gif)

1. **소개** 페이지에서 **다음**을 클릭합니다. **가용성 그룹 이름 지정** 페이지에서, **가용성 그룹 이름**에 **AG1**을 입력한 후에 **다음**을 한 번 더 클릭합니다.

	![새 AG 마법사, AG 이름 지정](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665524.gif)

1. **데이터베이스 선택** 페이지에서 **MyDB1**을 선택하고 **다음**을 클릭합니다. 원하는 주 복제본에서 전체 백업을 하나 이상 생성했으므로, 이러한 데이터베이스는 가용성 그룹의 필수 구성 요소를 충족합니다.

	![새 AG 마법사, 데이터베이스 선택](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665525.gif)

1. **복제본 지정** 페이지에서 **복제본 추가**를 클릭합니다.

	![새 AG 마법사, 복제본 지정](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665526.gif)

1. **서버에 연결** 대화 상자가 나타납니다. **서버 이름**에 **ContosoSQL2**를 입력한 후 **연결**을 클릭합니다.

	![새 AG 마법사, 서버에 연결](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665527.gif)

1. **복제본 지정** 페이지로 돌아가면 이제 **사용 가능한 복제본**에 **ContosoSQL2**가 표시됩니다. 아래와 같이 복제본을 구성합니다. 작업을 마쳤으면 **다음**을 클릭합니다.

	![새 AG 마법사, 복제본 지정(전체)](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665528.gif)

1. **초기 데이터 동기화 선택** 페이지에서 **조인만**을 선택하고 **다음**을 클릭합니다. **ContosoSQL1**에서 전체 및 트랜잭션 백업을 수행하고 이를 **ContosoSQL2**에서 복원할 때 이미 데이터 동기화를 수동으로 수행했습니다. 대신, 데이터베이스에서 백업 및 복원 작업을 수행하지 않고 **전체**를 선택하여 새 가용성 그룹 마법사가 데이터 동기화를 자동으로 수행하도록 할 수 있습니다. 그러나 일부 기업에서 사용하는 대형 데이터베이스의 경우에는 이 방법을 수행하지 않는 것이 좋습니다.

	![새 AG 마법사, 초기 데이터 동기화 선택](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665529.gif)

1. **유효성 검사** 페이지에서 **다음**을 클릭합니다. 이 페이지는 다음과 유사하게 표시되어야 합니다. 가용성 그룹 수신기가 구성되어 있지 않으므로 수신기 구성에 대한 경고가 표시됩니다. 이 자습서에서는 수신기를 구성하지 않으므로 이 경고를 무시해도 됩니다. 이 자습서를 완료한 후 수신기를 구성하려면 [Azure에서 AlwaysOn 가용성 그룹을 위해 ILB 수신기 구성](virtual-machines-sql-server-configure-ilb-alwayson-availability-group-listener.md)을 참조하세요.

	![새 AG 마법사, 유효성 검사](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665530.gif)

1. **요약** 페이지에서 **마침**을 클릭한 후 마법사에서 새 가용성 그룹을 구성하는 동안 기다립니다. **진행률** 페이지에서 **자세한 내용**을 클릭하여 자세한 진행 상태를 확인할 수 있습니다. 마법사가 완료되면 아래와 같이 **결과** 페이지를 검토하여 가용성 그룹이 올바르게 만들어졌는지 확인한 후 **닫기**를 클릭하여 마법사를 종료합니다.

	![새 AG 마법사, 결과](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665531.gif)

1. **개체 탐색기**에서 **AlwaysOn 고가용성**을 확장하고 **가용성 그룹**을 확장합니다. 이 컨테이너에 새 가용성 그룹이 표시됩니다. **AG1(기본)**을 마우스 오른쪽 단추로 클릭하고 **대시보드 표시**를 클릭합니다.

	![AG 대시보드 표시](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665532.gif)

1. **AlwaysOn 대시보드**는 다음과 유사하게 표시되어야 합니다. 복제본, 각 복제본의 장애 조치(failover) 모드 및 동기화 상태를 볼 수 있습니다.

	![AG 대시보드](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665533.gif)

1. **서버 관리자**로 돌아가서 **도구**를 선택한 후 **장애 조치(Failover) 클러스터 관리자**를 시작합니다.

1. **Cluster1.corp.contoso.com**과 **서비스 및 응용 프로그램**을 차례로 확장합니다. **역할**을 선택하고 **AG1** 가용성 그룹 역할이 만들어진 것을 확인합니다. AG1에는수신기를 구성하지 않았으므로 데이터베이스 클라이언트가 가용성 그룹에 연결할 수 있는 IP 주소가 없습니다. 읽기/쓰기 작업을 위해 주 노드에 직접 연결하고 읽기 전용 쿼리를 위해 보조 노드에 직접 연결할 수 있습니다.

	![장애 조치(Failover) 클러스터 관리자에서 AG](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665534.gif)

>[AZURE.WARNING]장애 조치(Failover) 클러스터 관리자에서 가용성 그룹으로 장애 조치를 시도하지 마세요. 모든 장애 조치(Failover) 작업은 SSMS의 **AlwaysOn 대시보드**에서 수행해야 합니다. 자세한 내용은 [가용성 그룹에서 WSFC 장애 조치(Failover) 클러스터 관리자 사용에 대한 제한 사항](https://msdn.microsoft.com/library/ff929171.aspx)을 참조하세요.

## 다음 단계
이제 Azure에서 가용성 그룹을 만들어 SQL Server AlwaysOn을 성공적으로 구현했습니다. 이 가용성 그룹에 대한 수신기를 구성하려면 [Azure에서 AlwaysOn 가용성 그룹에 대한 ILB 수신기 구성](virtual-machines-sql-server-configure-ilb-alwayson-availability-group-listener.md)을 참조하세요.

Azure에서 SQL Server를 사용하는 방법에 대한 기타 정보는 [Azure 가상 컴퓨터의 SQL Server](../articles/virtual-machines/virtual-machines-sql-server-infrastructure-services.md)를 참조하세요.

<!---HONumber=Sept15_HO4-->