<properties title="SharePoint Server Farm" pageTitle="SharePoint Server Farm" description="Describes the new SharePoint Server Farm feature available in the Azure Preview Portal" metaKeywords="" services="virtual-machines" solutions="" documentationCenter="" authors="josephd" videoId="" scriptId="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-sharepoint" ms.devlang="na" ms.topic="article" ms.date="08/06/2014" ms.author="josephd"></tags>

# SharePoint Server 팜

SharePoint Server 팜을 사용하여 Microsoft Azure 미리 보기 포털은 미리 구성된 SharePoint Server 2013 팜을 자동으로 생성합니다. 그러면 개발 및 테스팅 환경을 위해 기본 또는 고가용성 SharePoint 팜이 필요하거나 SharePoint Server 2013 을 조직의 협업 솔루션으로 평가하는 경우 상당한 시간이 줄어들 수 있습니다.

기본 SharePoint 팜은 다음 구성의 3가지 가상 컴퓨터로 구성됩니다.

![sharepointfarm][]

이 팜 구성을 간소화된 SharePoint 앱 개발 설정이나 SharePoint 2013의 최초 평가에 사용할 수 있습니다.

고가용성 SharePoint 팜은 다음과 같은 구성으로 9개의 가상 컴퓨터로 구성됩니다.

![sharepointfarm][1]

이 팜 구성을 사용하여 SharePoint 팜에 대해 보다 과도한 클라이언트 부하, 외부 SharePoint 사이트의 고가용성 및 SQL Server AlwaysOn을 테스트할 수 있습니다. 또한 고가용성 환경에서 SharePoint app 개발에 이 구성을 사용할 수 있습니다.

이러한 두 가지 팜에 대한 구성 세부 사항에 대해서는 [SharePoint 서버 팜 구성 세부 정보][]를 참조하세요.

이러한 팜은 인터넷 연결 클라이언트 컴퓨터를 통해 SharePoint 웹 서버에 대한 인증된 웹 트래픽(TCP 포트 80)을 허용하도록 미리 구성된 끝점을 포함합니다. 이 끝점은 미리 구성된 팀 사이트에 대한 끝점입니다. 이 팀 사이트에 액세스하려면 먼저 SharePoint 팜 웹 서버의 도메인 이름을 가져와야 합니다.

1.  Azure 미리 보기 포털에서 **찾아보기**와 **가상 컴퓨터**를 차례로 클릭합니다.
2.  가상 컴퓨터의 목록에서 웹 서버 이름을 클릭합니다(SP, WEB1 또는 WEB2로 끝남).
3.  웹 서버 가상 컴퓨터의 창에서 **속성**을 클릭합니다.
4.  **도메인 이름**의 FQDN을 기록합니다.
5.  인터넷 브라우저에서 URL **[http://[FQDN]][]**에 액세스합니다. 예를 들어, 도메인 이름이 spfarm1-web1-contoso.cloudpapp.net인 경우 URL은 **<http://spfarm1-web1-contoso.cloudpapp.net>**입니다.
6.  메시지가 표시되면 팜을 만들 때 지정한 사용자 계정 자격 증명을 입력합니다.

중앙 관리 SharePoint 사이트에서 내 사이트, SharePoint 응용 프로그램 및 기타 기능을 구성할 수 있습니다. 자세한 내용은 [SharePoint 2013 구성][]을 참조하세요. 중앙 관리 SharePoint 사이트에 액세스하려면 먼저 외부 포트 번호를 가져와야 합니다.

1.  Azure 미리 보기 포털에서 **찾아보기**와 **가상 컴퓨터**를 차례로 클릭합니다.
2.  가상 컴퓨터 목록에서 기본 구성의 SharePoint 서버 이름(SP로 끝남) 또는 고가용성 구성의 응용 프로그램 서버 이름(APP1 또는 APP2로 끝남)을 클릭합니다.
3.  가상 컴퓨터의 창에서 **속성**을 클릭합니다.
4.  **도메인 이름**의 FQDN을 기록합니다.
5.  **속성** 창을 닫습니다.
6.  가상 컴퓨터의 창에서 **끝점** 목록 아래로 스크롤합니다.
7.  SPCentralAdmin이라는 끝점의 포트 번호를 기록합니다.
8.  인터넷 브라우저에서 URL **[http://[FQDN]][]:[포트 번호]**에 액세스합니다. 예를 들어, 도메인 이름이 spfarm1-app1-contoso.cloudpapp.net이고 외부 포트 번호가 54398인 경우 URL은 **<http://spfarm1-app1-contoso.cloudpapp.net:54398>**입니다.
9.  메시지가 표시되면 팜을 만들 때 지정한 사용자 계정 자격 증명을 입력합니다.

참고:

-   Azure 미리 보기 포털은 구독 내에서 이러한 가상 컴퓨터를 만듭니다.
-   Azure 미리 보기 포털은 인터넷 연결 웹 서비스를 사용하는 클라우드 전용 가상 네트워크에서 이러한 팜을 모두 만듭니다. 조직 네트워크에 대한 사이트 간 VPN 연결은 없습니다.
-   원격 데스크톱 연결을 통해 이러한 서버를 관리할 수 있습니다.

## 연결 단계별 실행

SharePoint 팜으로 SharePoint 팜을 만들려면 다음을 수행합니다.

1.  [Microsoft Azure 미리 보기 포털][]에서 **가상 컴퓨터** \> **SharePoint Server 팜**을 클릭합니다.
2.  **SharePoint 팜 만들기** 창에서 리소스 그룹의 이름을 입력합니다.
3.  팜의 각 가상 컴퓨터에서 로컬 관리자 계정의 사용자 이름 및 암호를 입력합니다. 추측하기 어려운 이름 및 암호를 선택하세요.
4.  고가용성 팜을 원하는 경우 **고가용성 사용**을 클릭합니다.
5.  도메인 컨트롤러를 구성하려면 화살표를 클릭합니다. 호스트 이름 접두사(기본값은 리소스 그룹 이름), 포리스트 루트 도메인 이름(기본값은 contoso.com) 및 도메인 컨트롤러의 크기(기본값은 A1) 을 지정할 수 있습니다.
6.  SQL Server를 구성하려면 화살표를 클릭합니다. 호스트 이름 접두사(기본값은 리소스 그룹 이름), SQL Server의 크기(기본값은 A5), 데이터베이스 액세스 계정 이름 및 암호(기본값은 관리자 계정 사용), SQL Server 서비스 계정 이름(기본값은 sqlservice) 및 암호(기본값은 관리자 계정과 동일한 암호 사용)를 지정할 수 있습니다.
7.  SharePoint 서버를 구성하려면 화살표를 클릭합니다. 호스트 이름 접두사(기본값은 리소스 그룹 이름), SharePoint 서버의 크기(기본값은 A23), SharePoint 사용자 계정(기본값은 sp\_setup) 및 암호, SharePoint 팜 계정 이름(기본값은 sp\_farm) 및 암호, SharePoint 팜 암호를 지정할 수 있습니다. 기본값은 SharePoint 사용자 계정, 팜 계정 및 암호에 대해 관리자 암호를 사용하는 것입니다.
8.  선택적 구성(가상 네트워크 또는 저장소 계정, 진단)을 구성하려면 화살표를 클릭합니다.
9.  구독을 지정하려면 화살표를 클릭합니다.
10. 위치(지역)를 지정하려면 화살표를 클릭합니다.
11. 완료하면 **만들기**를 클릭합니다.

## Azure 리소스 관리자

SharePoint 서버 팜은 Azure 리소스 관리자 및 스크립트를 사용하여 SharePoint 팜의 인프라 및 서버 구성을 자동으로 만듭니다. 자세한 내용은 [리소스 관리자에서 Windows PowerShell 사용][]을 참조하세요.

  [sharepointfarm]: ./media/virtual-machines-sharepoint-farm-azure-preview/SPFarm_Basic.png
  [1]: ./media/virtual-machines-sharepoint-farm-azure-preview/SPFarm_HighAvail.png
  [SharePoint 서버 팜 구성 세부 정보]: ../virtual-machines-sharepoint-farm-config-azure-preview/
  [http://[FQDN]]: http://[FQDN]
  [SharePoint 2013 구성]: http://technet.microsoft.com/library/ee836142.aspx
  [Microsoft Azure 미리 보기 포털]: https://portal.azure.com/
  [리소스 관리자에서 Windows PowerShell 사용]: http://azure.microsoft.com/ko-kr/documentation/articles/powershell-azure-resource-manager/
