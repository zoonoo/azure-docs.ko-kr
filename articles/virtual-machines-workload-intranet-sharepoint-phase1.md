<properties 
	pageTitle="SharePoint 인트라넷 팜 작업 1단계: Azure 구성" 
	description="Azure 인프라 서비스의 SQL Server AlwaysOn 가용성 그룹을 사용하여 인트라넷 전용 SharePoint 2013 팜을 배포하는 이 첫 단계에서는 Azure 가상 네트워크 및 기타 Azure 인프라 요소를 만듭니다." 
	documentationCenter=""
	services="virtual-machines" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/05/2015" 
	ms.author="josephd"/>

# SharePoint 인트라넷 팜 작업 1단계: Azure 구성

Azure 인프라 서비스의 SQL Server AlwaysOn 가용성 그룹을 사용하여 인트라넷 전용 SharePoint 2013 팜을 배포하는 이 단계에서는 Azure 네트워킹 및 저장소 인프라를 구축합니다. [2단계](virtual-machines-workload-intranet-sharepoint-phase2.md)로 진행하기 전에 이 단계를 완료해야 합니다. 전체 단계를 보려면 [Azure에서 SQL Server AlwaysOn 가용성 그룹을 사용하여 SharePoint 배포](virtual-machines-workload-intranet-sharepoint-overview.md)를 참조하세요.

다음과 같은 기본 네트워크 구성 요소를 Azure에 프로비전해야 합니다.

- 서브넷 하나가 포함된 크로스-프레미스 가상 네트워크
- Azure 클라우드 서비스 3개
- VHD 디스크 이미지 및 추가 데이터 디스크를 저장할 Azure 저장소 계정 하나

## 시작하기 전에

Azure 구성 요소 구성을 시작하기 전에 다음 표에 필요한 정보를 입력합니다. Azure 구성 절차를 쉽게 진행하려면 이 섹션을 인쇄한 다음 필요한 정보를 기록하거나 이 섹션을 문서에 복사한 후에 정보를 입력합니다.

가상 네트워크(VNet)의 설정은 표 V에 입력합니다.

항목 | 구성 요소 | 설명 | 값 
--- | --- | --- | --- 
1. | VNet 이름 | Azure 가상 네트워크에 할당할 이름입니다(예: SPFarmNet). | __________________
2. | VNet 위치 | 가상 네트워크를 포함할 Azure 데이터 센터입니다. | __________________
3. | 로컬 네트워크 이름 | 조직 네트워크에 할당할 이름입니다. | __________________
4. | VPN 장치 IP 주소 | 인터넷에서 사용할 VPN 장치 인터페이스의 공용 IPv4 주소입니다. 이 주소는 IT 부서에서 확인할 수 있습니다. | __________________
5. | VNet 주소 공간 | 단일 개인 주소 접두사로 정의되는 가상 네트워크의 주소 공간입니다. 이 주소 공간은 IT 부서에서 확인할 수 있습니다. | __________________
6. | 첫 번째 최종 DNS 서버 | 가상 네트워크 서브넷의 주소 공간에 사용 가능한 네 번째 IP 주소입니다(표 S 참조). 이러한 주소는 IT 부서에서 확인할 수 있습니다. | __________________
7. | 두 번째 최종 DNS 서버 | 가상 네트워크 서브넷의 주소 공간에 사용 가능한 다섯 번째 IP 주소입니다(표 S 참조). 이러한 주소는 IT 부서에서 확인할 수 있습니다. | __________________

**표 V: 크로스-프레미스 가상 네트워크 구성**

이 솔루션의 서브넷 관련 정보는 표 S에 입력합니다. 서브넷에 대해 이름, 가상 네트워크 주소 공간을 기반으로 하는 단일 IP 주소 공간, 그리고 용도에 대한 설명을 지정합니다. 주소 공간은 네트워크 접두사 형식이라고도 하는 CIDR(Classless Interdomain Routing) 형식이어야 합니다. 예를 들면 10.24.64.0/20과 같습니다. 가상 네트워크 주소 공간의 이 주소 공간은 IT 부서에서 확인할 수 있습니다.

항목 | 서브넷 이름 | 서브넷 주소 공간 | 목적 
--- | --- | --- | --- 
1. | _______________ | _____________________________ | _________________________

**표 S: 가상 네트워크의 서브넷**

> [AZURE.NOTE]이 미리 정의된 아키텍처에서는 간편한 작업을 위해 단일 서브넷을 사용합니다. 서브넷 격리를 에뮬레이트하기 위해 트래픽 필터 집합을 오버레이하려는 경우에는 Azure [네트워크 보안 그룹](https://msdn.microsoft.com/library/azure/dn848316.aspx)을 사용할 수 있습니다.

가상 네트워크에서 도메인 컨트롤러를 처음 설정할 때 사용하려는 두 온-프레미스 DNS 서버의 정보를 표 D에 입력합니다. 각 DNS 서버에는 이름과 단일 IP 주소를 지정합니다. 이 이름은 DNS 서버의 호스트 이름 또는 컴퓨터 이름과 일치하지 않아도 됩니다. 아래에는 빈 칸이 두 개 있지만 항목을 더 추가할 수 있습니다. 추가 항목은 IT 부서에서 확인할 수 있습니다.

항목 | DNS 서버 이름 | DNS 서버 IP 주소 
--- | --- | ---
1. | ___________________________ | ___________________________
2. | ___________________________ | ___________________________ 

**표 D: 온-프레미스 DNS 서버**

사이트 간 VPN 연결을 통해 크로스-프레미스 네트워크에서 조직 네트워크로 패킷을 라우팅하려면 조직 온-프레미스 네트워크에서 연결 가능한 모든 위치에 대해 CIDR 표기법으로 작성된 주소 공간 목록을 포함하는 로컬 네트워크를 사용하여 가상 네트워크를 구성해야 합니다. 로컬 네트워크를 정의하는 주소 공간 목록은 다른 가상 네트워크나 다른 로컬 네트워크에 사용되는 주소 공간을 포함할 수 없으며 주소 공간이 겹쳐서는 안 됩니다. 즉, 구성된 가상 네트워크와 로컬 네트워크의 주소 공간은 고유해야 합니다.

로컬 네트워크 주소 공간 집합의 정보는 표 L에 입력합니다. 아래에는 빈 칸이 3개 있지만 일반적으로는 항목을 더 추가해야 합니다. 이 주소 공간 목록은 IT 부서에서 확인할 수 있습니다.

항목 | 로컬 네트워크 주소 공간 
--- | ---
1. | ___________________________________
2. | ___________________________________
3. | ___________________________________

**표 L: 로컬 네트워크의 주소 접두사**

표 V, S, D, L의 설정을 사용하여 가상 네트워크를 만들려면 [구성 표를 사용하여 크로스-프레미스 가상 네트워크 만들기](virtual-machines-workload-deploy-vnet-config-tables.md)의 지침을 사용합니다.

> [AZURE.NOTE]이 절차에서는 사이트 간 VPN 연결을 사용하는 가상 네트워크를 만드는 과정을 안내합니다. 사이트 간 연결에 대해 Express 경로를 사용하는 방법에 대한 자세한 내용은 [Express 경로 기술 개요](http://msdn.microsoft.com/library/dn606309.aspx)를 참조하세요.

Azure 가상 네트워크를 만들고 나면 Azure 관리 포털에서 다음 항목을 확인합니다.

- 가상 네트워크용 Azure VPN 게이트웨이의 공용 IPv4 주소
- 사이트 간 VPN 연결용 IPsec(인터넷 프로토콜 보안) 미리 공유한 키

가상 네트워크를 만든 후 Azure 관리 포털에서 이러한 정보를 확인하려면 **네트워크**, 가상 네트워크의 이름, **대시보드** 메뉴 옵션을 차례로 클릭합니다.

다음으로는 보안 사이트 간 VPN 연결을 만들기 위해 가상 네트워크 게이트웨이를 구성합니다. 관련 지침은 [관리 포털에서 가상 네트워크 게이트웨이 구성](http://msdn.microsoft.com/library/jj156210.aspx)을 참조하세요.

그런 다음 새 가상 네트워크와 온-프레미스 VPN 장치 간에 사이트 간 VPN 연결을 만듭니다. 자세한 지침은 [관리 포털에서 가상 네트워크 게이트웨이 구성](http://msdn.microsoft.com/library/jj156210.aspx)을 참조하세요.

그런 다음 온-프레미스 네트워크에서 가상 네트워크의 주소 공간에 연결할 수 있는지 확인합니다. 이를 위해 일반적으로는 가상 네트워크 주소 공간에 해당하는 경로를 VPN 장치에 추가한 다음 조직 네트워크의 나머지 라우팅 인프라에 해당 경로를 보급합니다. 이 작업을 수행하는 방법은 IT 부서에서 확인할 수 있습니다.

다음으로는 [Azure PowerShell을 설치 및 구성하는 방법](install-configure-powershell.md)의 지침을 사용하여 로컬 컴퓨터에 Azure PowerShell을 설치합니다. Azure PowerShell 명령 프롬프트를 엽니다.

먼저 다음 명령을 사용하여 올바른 Azure 구독을 선택합니다. < and > 문자를 포함하여 따옴표 안의 모든 항목을 올바른 이름으로 바꿉니다.

	$subscr="<Subscription name>"
	Select-AzureSubscription -SubscriptionName $subscr –Current

**Get-AzureSubscription** 명령 출력의 **SubscriptionName** 속성에서 구독 이름을 가져올 수 있습니다.

다음으로 이 SharePoint 팜에 필요한 클라우드 서비스 3개를 만듭니다. 표 C에 정보를 입력합니다.

항목 | 목적 | 클라우드 서비스 이름 
--- | --- | ---
1. | 도메인 컨트롤러 | ___________________________
2. | SQL Server | ___________________________
3. | SharePoint Server | ___________________________

**표 C: 클라우드 서비스 이름**

각 클라우드 서비스에 대해 고유한 이름을 선택해야 합니다. *클라우드 서비스 이름은 문자, 숫자 및 하이픈만 포함할 수 있습니다. 필드의 첫 번째 및 마지막 문자는 문자 또는 숫자여야 합니다.*

예를 들어 첫 번째 클라우드 서비스 이름을 DCs-*UniqueSequence*로 지정할 수 있습니다. 여기서 *UniqueSequence*는 조직의 약어입니다. 예를 들어 조직의 이름이 Tailspin Toys인 경우 클라우드 서비스 이름을 DCs-Tailspin으로 지정할 수 있습니다.

로컬 컴퓨터에서 다음 Azure PowerShell 명령을 사용하여 이름의 고유성을 테스트할 수 있습니다.

	Test-AzureName -Service <Proposed cloud service name>

이 명령에서 "False"가 반환되면 제안한 이름이 고유한 것입니다. 그런 후에 다음 명령을 사용하여 클라우드 서비스를 만듭니다.

	New-AzureService -Service <Unique cloud service name> -Location "<Table V – Item 2 – Value column>"

새로 만든 각 클라우드 서비스의 실제 이름을 표 C에 기록합니다.

다음으로 SharePoint 팜에 대한 저장소 계정을 만듭니다. *소문자와 숫자만 포함된 고유한 이름을 선택해야 합니다.* 다음 Azure PowerShell 명령을 사용하여 저장소 계정 이름의 고유성을 테스트할 수 있습니다.

	Test-AzureName -Storage <Proposed storage account name>

이 명령에서 "False"가 반환되면 제안한 이름이 고유한 것입니다. 그런 다음 저장소 계정을 만들고 다음 명령을 사용하여 구독에서 해당 계정을 사용하도록 설정합니다.

	$staccount="<Unique storage account name>"
	New-AzureStorageAccount -StorageAccountName $staccount -Location "<Table V – Item 2 – Value column>"
	Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount

다음으로 가용성 집합 4개의 이름을 정의합니다. 표 A에 해당 정보를 입력합니다.

항목 | 목적 | 가용성 집합 이름 
--- | --- | --- 
1. | 도메인 컨트롤러 | ___________________________
2. | SQL Server | ___________________________
3. | SharePoint 응용 프로그램 서버 | ___________________________
4. | SharePoint 프런트 엔드 웹 서버 | ___________________________

**표 A: 가용성 집합 이름**

2, 3, 4단계에서 가상 컴퓨터를 만들 때 이러한 이름이 필요합니다.

이 단계를 올바르게 완료하면 해당 구성이 생성됩니다.

![](./media/virtual-machines-workload-intranet-sharepoint-phase1/workload-spsqlao_01.png)

## 다음 단계

이 작업을 계속 구성하려면 [2단계: 도메인 컨트롤러 구성](virtual-machines-workload-intranet-sharepoint-phase2.md)으로 진행하세요.

## 추가 리소스

[Azure에서 SQL Server AlwaysOn 가용성 그룹을 사용하여 SharePoint 배포](virtual-machines-workload-intranet-sharepoint-overview.md)

[Azure 인프라 서비스에서 호스트되는 SharePoint 팜](virtual-machines-sharepoint-infrastructure-services.md)

[SQL Server AlwaysOn이 포함된 SharePoint 인포그래픽](http://go.microsoft.com/fwlink/?LinkId=394788)

[SharePoint 2013용 Microsoft Azure 아키텍처](https://technet.microsoft.com/library/dn635309.aspx)

<!--HONumber=54-->