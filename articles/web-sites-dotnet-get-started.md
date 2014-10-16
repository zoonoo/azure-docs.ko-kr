<properties linkid="develop-net-tutorials-get-started-vs2013" pageTitle="Get started with Azure Websites for ASP.NET" metaKeywords="" description="This tutorial shows you how to create an ASP.NET web project in Visual Studio 2013 and deploy it to an Azure Website. In less than 15 minutes you'll have an app up and running in the cloud." metaCanonical="" services="web-sites" documentationCenter=".NET" title="Get started with Azure Websites and ASP.NET" authors="tdykstra"  solutions="" manager="wpickett" editor="mollybos"  />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="hero-article" ms.date="01/01/1900" ms.author="tdykstra"></tags>

# Azure 웹 사이트 및 ASP.NET 시작

<div class="dev-center-tutorial-selector sublanding"><a href="/en-us/develop/net/tutorials/get-started/" title="Visual Studio 2013" class="current">Visual Studio 2013</a><a href="/en-us/develop/net/tutorials/get-started-vs2012/" title="Visual Studio 2012">Visual Studio 2012</a></div>

이 자습서에서는 Visual Studio 2013 또는 Web Express용 Visual Studio 2013을 사용하여 ASP.NET 웹 응용 프로그램을 만들고 Azure 웹 사이트에 배포하는 방법을 보여 줍니다. 이 자습서에서는 이전에 Azure 또는 ASP.NET을 사용한 경험이 없다고 가정합니다. 이 자습서를 완료하면 클라우드에서 간단한 웹 응용 프로그램을 실행할 수 있습니다.

Azure 계정은 무료로 개설할 수 있으며, Visual Studio 2013이 아직 없는 경우 SDK에서 Web Express용 Visual Studio 2013를 자동으로 설치합니다. 따라서 Azure용 개발을 무료로 시작할 수 있습니다.

다음 내용을 배웁니다.

-   Azure SDK를 설치하여 사용자 컴퓨터에서 Azure를 개발할 수 있도록 하는 방법
-   Visual Studio ASP.NET 웹 프로젝트를 만들고 Azure 웹 사이트에 배포하는 방법
-   웹 프로젝트를 변경하고 응용 프로그램을 다시 배포하는 방법
-   Azure 관리 포털을 사용하여 웹 사이트를 모니터 및 관리하는 방법

다음 그림에서는 완료된 응용 프로그램을 보여 줍니다.

![웹 사이트 홈페이지][]

<div class="wa-note">
  <span class="wa-icon-bulb"></span>
  <h5><a name="note"></a>이 자습서를 완료하려면 Azure 계정이 있어야 합니다.</h5>
  <ul>
    <li><a href="/en-us/pricing/free-trial/?WT.mc_id=A261C142F">Azure 계정을 무료로 개설</a>할 수 있음 - 유료 Azure 서비스를 사용해볼 수 있는 크레딧을 받게 되며 크레딧을 모두 사용한 후에도 계정을 유지하고 무료 Azure 서비스(예: 웹 서비스)를 사용할 수 있습니다.</li>
    <li><a href="/en-us/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F">MSDN 구독자 혜택을 활성화</a>할 수 있음 - MSDN 구독은 유료 Azure 서비스에 사용할 수 있는 크레딧을 매달 제공합니다.</li>
  <ul>
</div>

### 자습서 세그먼트

-   [개발 환경 설정][]
-   [Visual Studio에서 ASP.NET 웹 응용 프로그램 만들기][]
-   [Azure에 응용 프로그램 배포][]
-   [변경 및 다시 배포][]
-   [관리 포털에서 사이트 모니터 및 관리][]
-   [다음 단계][]

[WACOM.INCLUDE [install-sdk-2013-only][]]

## ASP.NET 웹 응용 프로그램 만들기

첫 번째 단계에서는 웹 응용 프로그램 프로젝트를 만듭니다. Visual Studio에서 나중에 프로젝트를 배포할 Azure 웹 사이트를 자동으로 만듭니다. 다음 다이어그램에서는 이와 같은 두 단계에서 수행되는 작업을 보여 줍니다.

![프로젝트 만들기 및 배포 단계를 보여 주는 다이어그램][]

1.  Visual Studio 2013 또는 Visual Studio 2013 Express for Web을 엽니다.

2.  **파일** 메뉴에서 **새 프로젝트**를 클릭합니다.

3.  **새 프로젝트** 대화 상자에서 **C\#** \> **웹** \> **ASP.NET 웹 응용 프로그램**을 클릭합니다. 원하는 경우 **Visual Basic**을 선택할 수 있습니다.

4.  **.NET Framework 4.5**가 대상 프레임워크로 선택되었는지 확인합니다.

5.  응용 프로그램 이름을 **MyExample**로 지정하고 **확인**을 클릭합니다.

    ![새 프로젝트 대화 상자][]

6.  **새 ASP.NET 프로젝트** 대화 상자에서 **MVC** 템플릿을 선택합니다. ASP.NET Web Forms에서 작업하려는 경우 **Web Forms** 템플릿을 선택할 수 있습니다.

    [MVC 및 Web Forms][]는 웹 사이트를 개발하기 위한 ASP.NET 프레임워크입니다. 이 자습서에서는 둘 중 아무것이든 선택할 수 있지만 Web Forms를 선택하는 경우 나중에 자습서에서 *Index.cshtml*을 편집하라는 내용이 나올 때 *Default.aspx*를 편집해야 합니다.

7.  **인증 변경**을 클릭합니다.

    ![새 ASP.NET 프로젝트 대화 상자][]

8.  **인증 변경** 대화 상자에서 **인증 없음**, **확인**을 차례로 클릭합니다.

    ![인증 없음][]

    작성 중인 샘플 응용 프로그램에서 사용자가 로그인할 수 없습니다. [다음 단계][] 섹션은 인증 및 권한 부여를 구현하는 자습서로 연결됩니다.

9.  **새 ASP.NET 프로젝트** 대화 상자에서 **Azure** 아래의 설정을 그대로 둔 다음 **확인**을 클릭합니다.

    ![새 ASP.NET 프로젝트 대화 상자][1]

    기본 설정은 Visual Studio에서 웹 프로젝트용 Azure 웹 사이트를 만들도록 지정합니다. 자습서의 다음 섹션에서는 새로 만든 웹 사이트에 웹 프로젝트를 배포합니다.

    확인란 캡션은 **Host in the cloud** 또는 **Create remote resources**일 수 있습니다. 어느 경우든지 효과는 같습니다.

10. Azure에 아직 로그인하지 않은 경우 로그인하라는 메시지가 Visual Studio에 표시됩니다. **로그인**을 클릭합니다.

    ![Azure에 로그인][]

11. **Azure에 로그인** 대화 상자에서 Azure 구독을 관리하는 데 사용하는 계정의 ID 및 암호를 입력합니다.

    로그인하면 만들려는 리소스에 대해 묻는 **Configure Azure Site Settings** 대화 상자가 표시됩니다.

    ![Azure에 로그인함][]

12. Visual Studio에서 기본 **사이트 이름**을 제공하며, Azure에서 응용 프로그램 URL의 접두사로 사용합니다. 원하는 경우 다른 사이트 이름을 입력합니다.

    완전한 URL은 여기에 입력한 항목과 *.azurewebsites.net*(**사이트 이름** 텍스트 상자 옆에 표시된 대로)으로 구성됩니다. 예를 들어 사이트 이름이 `MyExample6442`이면 URL은 `MyExample6442.azurewebsites.net`이 됩니다. URL은 고유해야 합니다. 입력한 URL을 다른 사용자가 이미 사용한 경우 녹색 확인 표시 대신 오른쪽에 빨간색 느낌표가 표시되며, 다른 사이트 이름을 입력해야 합니다.

13. **지역** 드롭다운 목록에서 가장 가까운 위치를 선택합니다.

    이 설정은 웹 사이트가 실행될 Azure 데이터 센터를 지정합니다. 이 자습서에서는 어떤 지역이든 선택할 수 있으며 지역에 따른 뚜렷한 차이는 없지만 프로덕션 사이트의 경우 [대기 시간][]을 최소화하기 위해 사용자 사이트에 액세스하는 브라우저와 최대한 가깝게 웹 서버를 배치할 수 있습니다.

14. 데이터베이스 필드를 변경하지 않고 그대로 둡니다.

    이 자습서에서는 데이터베이스를 사용하지 않습니다. 자습서 끝에 있는 [다음 단계][] 섹션에 데이터베이스 사용 방법을 보여 주는 자습서 링크가 있습니다.

15. **확인**을 클릭합니다.

    몇 초 후 Visual Studio에서 지정된 폴더에 웹 프로젝트를 만들고 지정된 Azure 지역에 웹 사이트를 만듭니다.

    **솔루션 탐색기** 창에 새 프로젝트의 파일 및 폴더가 표시됩니다.

    ![솔루션 탐색기][]

    **Web Publish Activity** 창에 사이트가 만들어졌다고 표시됩니다.

    ![웹 사이트가 만들어짐][]

    이제 서버 탐색기에서 사이트를 볼 수 있습니다.

    ![웹 사이트가 만들어짐][2]

## Azure에 응용 프로그램 배포

1.  **Web Publish Activity** 창에서 **Publish MyExample to this site now**를 클릭합니다.

    ![웹 사이트가 만들어짐][3]

    몇 초 후에 **웹 게시** 마법사가 나타납니다.

    Azure에 프로젝트를 배포하는 데 필요한 Visual Studio의 설정이 *게시 프로필*에 저장되었습니다. 이 마법사에서 해당 설정을 확인하고 변경할 수 있습니다.

2.  **웹 게시** 마법사의 **연결** 탭에서 **연결 유효성 검사**를 클릭하여 Visual Studio에서 웹 프로젝트를 배포하기 위해 Azure에 연결할 수 있는지 확인합니다.

    ![연결 유효성 검사][]

    연결 유효성이 검사되면 **연결 유효성 검사** 단추 옆에 녹색 확인 표시가 나타납니다.

3.  **다음**을 클릭합니다.

    ![유효성이 검사된 연결][]

4.  **설정** 탭에서 **다음**을 클릭합니다.

    ![설정 탭][]

    **구성** 및 **파일 게시 옵션**의 기본값을 그대로 사용합니다.

    **구성** 드롭다운에서 원격 디버깅을 위한 디버그 빌드를 배포할 수 있습니다. [다음 단계][] 섹션은 Visual Studio를 원격에서 디버그 모드로 실행하는 방법을 보여 주는 자습서로 연결됩니다.

    **파일 게시 옵션**을 확장하면 이 자습서에는 적용되지 않는 다음과 같은 상황을 처리할 수 있는 몇 가지 설정이 표시됩니다.

    -   대상에 있는 추가적인 파일을 제거합니다.

        서버에서 프로젝트에 포함되지 않은 파일을 모두 삭제합니다. 앞서 다른 프로젝트를 배포한 사이트에 프로젝트를 배포하는 경우 이 작업을 수행해야 할 수 있습니다.

    -   게시 중 미리 컴파일합니다.

        대형 사이트에 대한 최초 요청 준비 시간을 줄일 수 있습니다.

    -   App\_Data 폴더에서 파일을 실행합니다.

        테스팅을 위해 프로덕션에 배포하지 않을 SQL Server 데이터베이스 파일을 App\_Data에 포함하는 경우가 가끔 있습니다.

5.  **미리 보기** 탭에서 **미리 보기 시작**을 클릭합니다.

    ![미리 보기 탭의 미리 보기 시작 단추][]

    이 탭에는 서버로 복사될 파일 목록이 표시됩니다. 미리 보기 표시는 응용 프로그램을 게시하는 데 필요하지 않지만 알아 두면 유용한 기능입니다.

6.  **게시**를 클릭합니다.

    ![StartPreview 파일 출력][]

    Visual Studio에서 Azure 서버로 파일을 복사하는 프로세스를 시작합니다.

    **출력** 및 **Web Publish Activity** 창에 수행된 배포 작업이 표시되고 성공적인 배포 완료가 보고됩니다.

    ![성공적인 배포를 보고하는 출력 창][]

    배포에 성공하면 기본 브라우저에서 배포된 웹 사이트의 URL이 자동으로 열리며,
     만든 응용 프로그램이 이제 클라우드에서 실행되고 있습니다. 브라우저 주소 표시줄의 URL은 사이트가 인터넷에서 로드되고 있음을 보여 줍니다.

    ![Azure에서 실행되는 웹 사이트][]

7.  브라우저를 닫습니다.

## 변경 및 다시 배포

자습서의 이 섹션에서는 홈페이지의 **h1** 제목을 변경하고 개발 컴퓨터에서 로컬로 프로젝트를 실행하여 변경 내용을 확인한 다음 Azure에 변경 내용을 배포합니다.

1.  **솔루션 탐색기**에서 *Views/Home/Index.cshtml* 또는 *.vbhtml* 파일을 열고 **h1** 제목을 "ASP.NET"에서 "ASP.NET and Azure"로 변경한 다음 파일을 저장합니다.

    ![MVC index.cshtml][]

    ![MVC h1 변경][]

2.  CTRL+F5 키를 눌러서 로컬 컴퓨터에서 사이트를 실행하여 업데이트된 제목을 표시합니다.

    ![로컬에서 실행 중인 웹 사이트][]

    `http://localhost` URL은 해당 사이트가 로컬 컴퓨터에서 실행되고 있음을 보여 줍니다. 기본적으로 웹 응용 프로그램 개발 중 사용하도록 설계된 IIS 경량 버전인 IIS Express에서 실행됩니다.

3.  브라우저를 닫습니다.

4.  **솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시**를 선택합니다.

    ![게시 선택][]

    **웹 게시** 마법사의 미리 보기 탭이 나타납니다. 게시 설정을 변경해야 하는 경우 다른 탭을 선택할 수 있지만 이제 같은 설정으로 다시 배포하면 됩니다.

5.  **웹 게시** 마법사에서 **게시**를 클릭합니다.

    ![게시 클릭][]

    Visual Studio에서 Azure에 프로젝트를 배포하고 기본 브라우저에서 사이트를 엽니다.

    ![변경되어 배포된 사이트][웹 사이트 홈페이지]

**팁:** 보다 빠른 배포를 위해 **한 번 클릭으로 웹 게시** 도구 모음을 사용할 수 있습니다. **보기** \> **도구 모음**을 클릭한 다음 **한 번 클릭으로 웹 게시**를 선택합니다. 이 도구 모음을 사용하여 프로필을 선택하거나, 단추를 클릭하여 게시하거나, 단추를 클릭하여 **웹 게시** 마법사를 열 수 있습니다.

![한 번 클릭으로 웹 게시 도구 모음][]

## 관리 포털에서 사이트 모니터 및 관리

[Azure 관리 포털][]은 Azure 서비스(예: 방금 만든 웹 사이트)를 관리하고 모니터하는 데 사용할 수 있는 웹 인터페이스입니다. 자습서의 이 섹션에서는 포털에서 수행할 수 있는 작업 중 일부를 살펴봅니다.

1.  브라우저에서 http://manage.windowsazure.com 으로 이동하고 Azure 자격 증명으로 로그인합니다.

    포털에 Azure 서비스 목록이 표시됩니다.

2.  웹 사이트의 이름을 클릭합니다.

    ![새 웹 사이트가 호출된 포털 홈페이지][]

3.  **대시보드** 탭을 클릭합니다.

    **대시보드** 탭에는 자주 사용되는 다수의 사이트 관리 기능에 대한 링크 및 사용량 통계의 개요가 표시됩니다. **간략 상태**에서 응용 프로그램의 홈페이지에 대한 링크를 볼 수 있습니다.

    ![포털 웹 사이트 대시보드 탭][]

    지금은 사이트의 트래픽이 그다지 많지 않아서 그래픽에 아무것도 표시되지 않을 수 있습니다. 응용 프로그램을 탐색하고, 페이지를 몇 번 새로 고친 다음 포털 **대시보드** 페이지를 새로 고치면 몇 가지 통계가 표시됩니다. **모니터** 탭을 클릭하면 더 자세한 내용을 확인할 수 있습니다.

4.  **구성** 탭을 클릭합니다.

    [구성][] 탭에서 이 사이트에 사용되는 .NET 버전을 관리하고, [WebSockets][], [진단 로깅][] 등의 기능을 사용하도록 설정하고, [연결 문자열 값][]을 설정하는 등의 작업을 수행할 수 있습니다.

    ![포털 웹 사이트 구성 탭][]

5.  **크기 조정** 탭을 클릭합니다.

    웹 사이트 서비스의 유료 계층에 대해서는, [크기 조정][] 탭에서 다양한 트래픽을 처리하기 위해 웹 응용 프로그램을 제공하는 컴퓨터의 크기 및 대수를 관리할 수 있습니다.

    크기를 수동으로 조정하거나 자동 크기 조정에 대한 기준 또는 일정을 구성할 수 있습니다.

    ![포털 웹 사이트 크기 조정 탭][]

이러한 기능은 관리 포털의 일부에 지나지 않습니다. 또한 새 웹 사이트를 만들고, 기존 사이트를 삭제하고, 사이트를 중지 및 다시 시작하고, 다른 Azure 서비스(예: 데이터베이스 및 가상 컴퓨터)를 관리할 수도 있습니다.

**팁:** 미리 보기의 새로운 관리 포털이 현재 사용 중인 관리 포털을 대체할 예정입니다. 자세한 내용은 [Azure 미리 보기 포털][]을 참조하세요.

## 다음 단계

이 자습서에서는 간단한 웹 응용 프로그램을 만들고 Azure 웹 사이트에 배포하는 방법을 확인했습니다. 다음은 자세한 내용을 확인할 수 있는 몇 가지 관련 항목 및 리소스입니다.

-   웹 프로젝트를 배포하는 다른 방법

    이 자습서에서는 단일 작업으로 사이트를 만들고 배포하는 가장 빠른 방법을 확인했습니다. Visual Studio를 사용하거나 [소스 제어 시스템][]에서 [배포를 자동화][]하여 배포하는 다른 방법에 대한 개요는 [Azure 웹 사이트를 배포하는 방법][]을 참조하세요.

    Visual Studio에서 배포를 자동화할 수 있는 Windows PowerShell 스크립트를 생성할 수도 있습니다. 자세한 내용은 [자동화(Azure에서 실제 클라우드 앱 빌드)][](영문)를 참조하세요.

-   Visual Studio에서 웹 사이트를 관리하는 방법

    **서버 탐색기**에서 수행할 수 있는 사이트 관리 기능에 대한 자세한 내용은 [Visual Studio에서 Azure 웹 사이트 문제 해결][]을 참조하세요.

-   웹 사이트 문제를 해결하는 방법

    Visual Studio는 Azure 로그가 생성될 때 실시간으로 보기 쉽도록 만드는 기능을 제공합니다. 원격으로 Azure에서 디버그 모드로 실행할 수도 있습니다. 자세한 내용은 [Visual Studio에서 Azure 웹 사이트 문제 해결][]을 참조하세요.

-   데이터베이스 및 권한 부여 기능을 추가하는 방법

    데이터베이스에 액세스하는 방법과 일부 사이트 기능을 인증된 사용자만 사용할 수 있도록 제한하는 방법을 보여 주는 자습서는 [Azure 웹 사이트에 멤버 자격, OAuth 및 SQL 데이터베이스가 포함된 보안 ASP.NET MVC 앱 배포][]를 참조하세요.

-   사용자 지정 도메인 이름 및 SSL을 추가하는 방법

    SSL 및 사용자만의 도메인(예: contoso.azurewebsites.net가 아닌 www.contoso.com 사용)을 사용하는 방법에 대한 자세한 내용은 다음 리소스를 참조하세요.

    -   [Azure 웹 사이트에 대한 사용자 지정 도메인 이름 구성][]
    -   [Azure 웹 사이트에 HTTPS 사용][]
-   유휴 시간 제한 후 절전 모드 해제 대기 시간을 방지하는 방법

    기본적으로 웹 사이트가 일정 기간 동안 유휴 상태인 경우 언로드됩니다. 언로드된 후 첫 번째 요청은 사이트가 다시 로드될 때까지 대기해야 합니다. 대기 시간을 방지하기 위해 AlwaysOn 기능을 사용할 수 있습니다. 자세한 내용은 [웹 사이트를 구성하는 방법][](영문)의 구성 옵션을 참조하세요.

-   채팅과 같은 실시간 기능을 추가하는 방법

    웹 사이트에 실시간 기능(예: 채팅 서비스, 게임, 주식 기호 등)을 포함하는 경우 [WebSocket][WebSockets] 전송 방식으로 [ASP.NET SignalR][]을 사용하여 최상의 성과를 얻을 수 있습니다. 자세한 내용은 [Windows Azure 웹 사이트에서 SignalR 사용][]을 참조하세요.

-   웹 응용 프로그램용 Azure 웹 사이트, 클라우드 서비스 및 VM을 선택하는 방법

    Azure에서는 웹 응용 프로그램을 웹 사이트(이 자습서에 설명됨), 클라우드 서비스 또는 가상 컴퓨터에서 실행할 수 있습니다. 자세한 내용은 [Azure 실행 모델][](영문) 및 [Azure 웹 사이트, 클라우드 서비스 및 VM: 각 항목을 사용해야 하는 경우][](영문)를 참조하세요.

  [Visual Studio 2013]: /en-us/develop/net/tutorials/get-started/ "Visual Studio 2013"
  [Visual Studio 2012]: /en-us/develop/net/tutorials/get-started-vs2012/ "Visual Studio 2012"
  [웹 사이트 홈페이지]: ./media/web-sites-dotnet-get-started-vs2013/deployedandazure.png
  [Azure 계정을 무료로 개설]: /en-us/pricing/free-trial/?WT.mc_id=A261C142F
  [MSDN 구독자 혜택을 활성화]: /en-us/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F
  [개발 환경 설정]: #set-up-the-development-environment
  [Visual Studio에서 ASP.NET 웹 응용 프로그램 만들기]: #create-an-aspnet-web-application
  [Azure에 응용 프로그램 배포]: #deploy-the-application-to-azure
  [변경 및 다시 배포]: #make-a-change-and-redeploy
  [관리 포털에서 사이트 모니터 및 관리]: #monitor-and-manage-the-site-in-the-management-portal
  [다음 단계]: #next-steps
  [install-sdk-2013-only]: ../includes/install-sdk-2013-only.md
  [프로젝트 만들기 및 배포 단계를 보여 주는 다이어그램]: ./media/web-sites-dotnet-get-started-vs2013/createdeploydiagram.png
  [새 프로젝트 대화 상자]: ./media/web-sites-dotnet-get-started-vs2013/GS13newprojdb.png
  [MVC 및 Web Forms]: http://www.asp.net/get-started/websites
  [새 ASP.NET 프로젝트 대화 상자]: ./media/web-sites-dotnet-get-started-vs2013/GS13changeauth.png
  [인증 없음]: ./media/web-sites-dotnet-get-started-vs2013/GS13noauth.png
  [1]: ./media/web-sites-dotnet-get-started-vs2013/GS13newaspnetprojdb.png
  [Azure에 로그인]: ./media/web-sites-dotnet-get-started-vs2013/signin.png
  [Azure에 로그인함]: ./media/web-sites-dotnet-get-started-vs2013/configuresitesettings.png
  [대기 시간]: http://www.bing.com/search?q=web%20latency%20introduction&qs=n&form=QBRE&pq=web%20latency%20introduction&sc=1-24&sp=-1&sk=&cvid=eefff99dfc864d25a75a83740f1e0090
  [솔루션 탐색기]: ./media/web-sites-dotnet-get-started-vs2013/solutionexplorer.png
  [웹 사이트가 만들어짐]: ./media/web-sites-dotnet-get-started-vs2013/GS13sitecreated1.png
  [2]: ./media/web-sites-dotnet-get-started-vs2013/siteinse.png
  [3]: ./media/web-sites-dotnet-get-started-vs2013/GS13sitecreated.png
  [연결 유효성 검사]: ./media/web-sites-dotnet-get-started-vs2013/GS13ValidateConnection.png
  [유효성이 검사된 연결]: ./media/web-sites-dotnet-get-started-vs2013/GS13ValidateConnectionSuccess.png
  [설정 탭]: ./media/web-sites-dotnet-get-started-vs2013/GS13SettingsTab.png
  [미리 보기 탭의 미리 보기 시작 단추]: ./media/web-sites-dotnet-get-started-vs2013/GS13Preview.png
  [StartPreview 파일 출력]: ./media/web-sites-dotnet-get-started-vs2013/GS13previewoutput.png
  [성공적인 배포를 보고하는 출력 창]: ./media/web-sites-dotnet-get-started-vs2013/PublishOutput.png
  [Azure에서 실행되는 웹 사이트]: ./media/web-sites-dotnet-get-started-vs2013/GS13deployedsite.png
  [MVC index.cshtml]: ./media/web-sites-dotnet-get-started-vs2013/index.png
  [MVC h1 변경]: ./media/web-sites-dotnet-get-started-vs2013/mvcandazure.png
  [로컬에서 실행 중인 웹 사이트]: ./media/web-sites-dotnet-get-started-vs2013/localandazure.png
  [게시 선택]: ./media/web-sites-dotnet-get-started-vs2013/choosepublish.png
  [게시 클릭]: ./media/web-sites-dotnet-get-started-vs2013/clickpublish.png
  [한 번 클릭으로 웹 게시 도구 모음]: ./media/web-sites-dotnet-get-started-vs2013/weboneclickpublish.png
  [Azure 관리 포털]: /en-us/services/management-portal/
  [새 웹 사이트가 호출된 포털 홈페이지]: ./media/web-sites-dotnet-get-started-vs2013/portalhome.png
  [포털 웹 사이트 대시보드 탭]: ./media/web-sites-dotnet-get-started-vs2013/portaldashboard.png
  [구성]: /ko-kr/documentation/articles/web-sites-configure//
  [WebSockets]: /blog/2013/11/14/introduction-to-websockets-on-windows-azure-web-sites/
  [진단 로깅]: /ko-kr/documentation/articles/web-sites-enable-diagnostic-log/
  [연결 문자열 값]: /blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/
  [포털 웹 사이트 구성 탭]: ./media/web-sites-dotnet-get-started-vs2013/portalconfigure.png
  [크기 조정]: /ko-kr/documentation/articles/web-sites-scale/
  [포털 웹 사이트 크기 조정 탭]: ./media/web-sites-dotnet-get-started-vs2013/portalscale.png
  [Azure 미리 보기 포털]: /en-us/overview/preview-portal/
  [소스 제어 시스템]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control
  [배포를 자동화]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/continuous-integration-and-continuous-delivery
  [Azure 웹 사이트를 배포하는 방법]: /ko-kr/documentation/articles/web-sites-deploy/"
  [자동화(Azure에서 실제 클라우드 앱 빌드)]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything
  [Visual Studio에서 Azure 웹 사이트 문제 해결]: /en-us/develop/net/tutorials/troubleshoot-web-sites-in-visual-studio/
  [Azure 웹 사이트에 멤버 자격, OAuth 및 SQL 데이터베이스가 포함된 보안 ASP.NET MVC 앱 배포]: /en-us/develop/net/tutorials/web-site-with-sql-database/
  [Azure 웹 사이트에 대한 사용자 지정 도메인 이름 구성]: /ko-kr/documentation/articles/web-sites-custom-domain-name/
  [Azure 웹 사이트에 HTTPS 사용]: http://azure.microsoft.com/ko-kr/documentation/articles/web-sites-configure-ssl-certificate/
  [웹 사이트를 구성하는 방법]: http://azure.microsoft.com/ko-kr/documentation/articles/web-sites-configure/
  [ASP.NET SignalR]: http://www.asp.net/signalr
  [Windows Azure 웹 사이트에서 SignalR 사용]: http://www.asp.net/signalr/overview/signalr-20/getting-started-with-signalr-20/using-signalr-with-windows-azure-web-sites
  [Azure 실행 모델]: /en-us/develop/net/fundamentals/compute/
  [Azure 웹 사이트, 클라우드 서비스 및 VM: 각 항목을 사용해야 하는 경우]: /en-us/manage/services/web-sites/choose-web-app-service/
