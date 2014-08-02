<properties linkid="" urlDisplayName="" pageTitle="" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="Get started with Azure and ASP.NET" authors="tdykstra" solutions="" manager="wpickett" editor="mollybos" />

Azure 및 ASP.NET 시작
=====================

[Visual Studio 2013](/en-us/develop/net/tutorials/get-started/ "Visual Studio 2013")[Visual Studio 2012](/en-us/develop/net/tutorials/get-started-vs2012/ "Visual Studio 2012")

**참고**

[이 자습서의 더 최신 버전](/en-us/develop/net/tutorials/get-started/)을 사용할 수 있습니다. Visual Studio 2012를 사용하려면 이 버전의 내용을 계속 따를 수 있지만 본 자습서에는 최신 Azure SDK 기능이 일부 표시되지 않습니다.

이 자습서에서는 Visual Studio 2012 또는 Visual Studio 2012 Express for Web의 웹 게시 마법사를 사용하여 ASP.NET 웹 응용 프로그램을 Azure 웹 사이트에 배포하는 방법을 보여 줍니다. 원하는 경우 Visual Studio 2010 또는 Visual Web Developer Express 2010을 사용하여 자습서 단계를 따를 수 있습니다.

Azure 계정은 무료로 개설할 수 있으며, Visual Studio 2012이 아직 없는 경우 SDK에서 Web Express용 Visual Studio 2012를 자동으로 설치합니다. 따라서 Azure용 개발을 무료로 시작할 수 있습니다.

이 자습서에서는 이전에 Azure를 사용한 경험이 없다고 가정합니다. 이 자습서를 완료하면 클라우드에서 간단한 웹 응용 프로그램을 실행할 수 있습니다.

다음 내용을 배웁니다.

-   Azure SDK를 설치하여 사용자 컴퓨터에서 Azure를 개발할 수 있도록 하는 방법
-   Visual Studio ASP.NET MVC 4 프로젝트를 만들고 Azure 웹 사이트에 게시하는 방법

다음 그림에서는 완료된 응용 프로그램을 보여 줍니다.

![웹 사이트 예](./media/web-sites-dotnet-get-started-vs2012/DeployedWebSite.png)

**참고** 이 자습서를 완료하려면 Azure 계정이 필요합니다. 계정이 없는 경우 [MSDN 구독자 혜택을 활성화](/en-us/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)하거나 [무료 평가판을 등록](/en-us/pricing/free-trial/?WT.mc_id=A261C142F)할 수 있습니다.

### 자습서 세그먼트

1.  [개발 환경 설정](#setupdevenv)
2.  [Azure에서 웹 사이트 만들기](#setupwindowsazure)
3.  [ASP.NET MVC 4 응용 프로그램 만들기](#createmvc4app)
4.  [Azure에 응용 프로그램 배포](#deploytowindowsazure)
5.  [다음 단계](#nextsteps)

[WACOM.INCLUDE [install-sdk-2012-only](../includes/install-sdk-2012-only.md)]

사이트 만들기웹 사이트 만들기
-----------------------------

다음은 Azure 웹 사이트를 만드는 단계입니다.

1.  [Azure 관리 포털](http://manage.windowsazure.com)에서 **웹 사이트**를 클릭한 다음 **새로 만들기**를 클릭합니다.

	![새 웹 사이트](./media/web-sites-dotnet-get-started-vs2012/WebSiteNew.png)

1.  **빨리 만들기**를 클릭합니다.

    ![빨리 만들기](./media/web-sites-dotnet-get-started-vs2012/ClickQuickCreate.png)

2.  마법사의 **웹 사이트 만들기** 단계에서 응용 프로그램의 고유 URL로 사용할 문자열을 **URL** 상자에 입력합니다. 전체 URL은 여기에 입력한 문자열과 입력란 옆에 표시되는 접미사로 구성됩니다. 그림에는 **example1**이 표시되어 있지만 다른 사용자가 URL에 이 문자열을 이미 사용하는 경우 다른 값을 입력해야 합니다.

3.  **지역** 드롭다운 목록에서 가장 가까운 지역을 선택합니다. 이 설정은 웹 사이트가 실행되는 데이터 센터를 지정합니다.

4.  **웹 사이트 만들기** 화살표를 클릭합니다.

    ![새 웹 사이트 만들기](./media/web-sites-dotnet-get-started-vs2012/CreateWebsite.png)

    관리 포털이 웹 사이트 페이지로 돌아가고 **상태** 열에 사이트를 만드는 중이라고 표시됩니다. 잠시(일반적으로 1분 미만) 후에 **상태** 열에 사이트를 만들었다고 표시됩니다. 왼쪽의 탐색 모음에서 계정에 보유한 사이트 수가 **웹 사이트** 아이콘 옆에 나타납니다.

    ![관리 포털의 웹 사이트 페이지, 만들어진 웹 사이트](./media/web-sites-dotnet-get-started-vs2012/WebSiteStatusRunning.png)

앱 만들기ASP.NET MVC 4 응용 프로그램 만들기
-------------------------------------------

Azure 웹 사이트를 만들었지만 아직 콘텐츠가 없습니다. 다음 단계에서는 Azure에 게시할 Visual Studio 웹 응용 프로그램 프로젝트를 만듭니다.

### 프로젝트 만들기

1.  Visual Studio 2012 또는 Visual Studio 2012 for Web Express를 시작합니다.

2.  **파일** 메뉴에서 **새로 만들기**를 클릭한 다음 **프로젝트**를 클릭합니다.

	![파일 메뉴의 새 프로젝트](./media/web-sites-dotnet-get-started-vs2012/NewVSProject.png)

1.  **새 프로젝트** 대화 상자에서 **C\#**을 확장하고 **설치된 템플릿** 아래의 **웹**을 선택한 다음 **ASP.NET MVC 4 웹 응용 프로그램**을 선택합니다.

2.  **.NET Framework 4.5**가 대상 프레임워크로 선택되었는지 확인합니다.

3.  응용 프로그램 이름을 **MyExample**로 지정하고 **확인**을 클릭합니다.

	![새 프로젝트 대화 상자](./media/web-sites-dotnet-get-started-vs2012/NewMVC4WebApp.png)

1.  **새 ASP.NET MVC 4 프로젝트** 대화 상자에서 **인터넷 응용 프로그램** 템플릿을 선택하고 **확인**을 클릭합니다.

	![새 ASP.NET MVC 4 프로젝트 대화 상자](./media/web-sites-dotnet-get-started-vs2012/InternetAppTemplate.png)

### 로컬에서 응용 프로그램 실행

1.  **Ctrl**+**F5**를 눌러 응용 프로그램을 실행합니다. 응용 프로그램 홈페이지가 기본 브라우저에 나타납니다.

	![로컬에서 실행 중인 웹 사이트](./media/web-sites-dotnet-get-started-vs2012/AppRunningLocally.png)

이렇게 하면 Azure에 배포할 수 있는 간단한 응용 프로그램을 만들 수 있습니다.

앱 배포Azure에 응용 프로그램 배포
---------------------------------

1.  Visual Studio의 **솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 상황에 맞는 메뉴에서 **게시**를 선택합니다.

    ![프로젝트 상황에 맞는 메뉴의 게시](./media/web-sites-dotnet-get-started-vs2012/PublishVSSolution.png)

**웹 게시** 마법사가 열립니다.

1.  **웹 게시** 마법사의 **프로필** 탭에서 **가져오기**를 클릭합니다.

    ![게시 설정 가져오기](./media/web-sites-dotnet-get-started-vs2012/ImportPublishSettings.png)

**게시 프로필 가져오기** 대화 상자가 나타납니다.

1.  이전에 Visual Studio에 Azure 구독을 추가하지 않은 경우 다음 단계를 수행하십시오. 이들 단계에서 구독을 추가하여 **Azure 웹 사이트에서 가져오기** 아래의 드롭다운 목록에 웹 사이트를 포함합니다.

    -   **게시 프로필 가져오기** 대화 상자에서 **Azure 웹 사이트에서 가져오기**를 클릭한 후 **Add Azure subscription**를 클릭합니다.

    ![Azure 구독 추가](./media/web-sites-dotnet-get-started-vs2012/rzAddWAsub.png)

    -   **Import Azure Subscriptions** 대화 상자에서 **구독 파일 다운로드**를 클릭합니다.

    ![구독 파일 다운로드](./media/web-sites-dotnet-get-started-vs2012/rzDownLoadDownload.png)

    -   브라우저 창에서 *.publishsettings* 파일을 저장합니다.

    ![.publishsettings 파일 다운로드](./media/web-sites-dotnet-get-started-vs2012/rzDown2.png)

    -   **Import Azure Subscriptions** 대화 상자에서 **찾아보기**를 클릭하고 *.publishsettings* 파일로 이동합니다.

    ![구독 다운로드](./media/web-sites-dotnet-get-started-vs2012/rzDownLoadBrowse.png)

    -   **가져오기**를 클릭합니다.

    ![가져오기](./media/web-sites-dotnet-get-started-vs2012/rzImp.png)

    > [WACOM.NOTE] 
    > .publishsettings 파일에는 Azure 구독 및 서비스를 관리하는 데 사용되는 자격 증명(인코딩 해제)이 포함되어 있습니다. 이 파일의 보안을 유지하는 가장 좋은 방법은 소스 디렉터리 밖(예: 라이브러리\\문서 폴더)에 임시로 파일을 저장한 다음 일단 가져오기가 완료되면 삭제하는 것입니다. 악의적인 사용자가 .publishsettings 파일에 액세스할 경우 Azure 서비스를 편집, 생성 및 삭제할 수 있습니다.

2.  **게시 프로필 가져오기** 대화 상자에서 **Azure 웹 사이트에서 가져오기**를 선택하고 드롭다운 목록에서 웹 사이트를 선택한 후 **확인**을 클릭합니다.

    ![게시 프로필 가져오기](./media/web-sites-dotnet-get-started-vs2012/ImportPublishProfile.png)

3.  **연결** 탭에서 **연결 유효성 검사**를 클릭하여 설정이 올바른지 확인합니다.

    ![연결 유효성 검사](./media/web-sites-dotnet-get-started-vs2012/ValidateConnection.png)

4.  연결 유효성이 검사되면 **연결 유효성 검사** 단추 옆에 녹색 확인 표시가 나타납니다. **다음**을 클릭합니다.

    ![유효성이 검사된 연결](./media/web-sites-dotnet-get-started-vs2012/ValidateConnectionSuccess.png)

5.  이 응용 프로그램에 데이터베이스를 사용하지 않으므로 **설정** 탭에서 **Use this connection string at runtime** 옵션의 확인란을 선택 취소합니다. 이 페이지에서 남은 항목에 대해서는 기본 설정을 적용할 수 있습니다. 릴리스 빌드 구성을 배포 중이며 대상 서버의 파일을 삭제할 필요가 없는 경우 응용 프로그램을 미리 컴파일하거나 App\_Data 폴더의 파일을 제외합니다. **다음**을 클릭합니다.

    ![설정 탭](./media/web-sites-dotnet-get-started-vs2012/PublishWebSettingsTab.png)

6.  **미리 보기** 탭에서 **미리 보기 시작**을 클릭합니다.

    ![미리 보기 탭의 미리 보기 시작 단추](./media/web-sites-dotnet-get-started-vs2012/PublishWebStartPreview.png)

    이 탭에는 서버로 복사될 파일 목록이 표시됩니다. 미리 보기 표시는 응용 프로그램을 게시하는 데 필요하지 않지만 알아 두면 유용한 기능입니다. 따라서 표시된 파일 목록에 어떤 작업도 수행할 필요가 없습니다.

    ![StartPreview 파일 출력](./media/web-sites-dotnet-get-started-vs2012/PublishWebStartPreviewOutput.png)

7.  **게시**를 클릭합니다. Visual Studio에서 Azure 서버로 파일을 복사하는 프로세스를 시작합니다.

8.  **출력** 창에 수행된 배포 작업이 표시되고 성공적인 배포 완료가 보고됩니다.

    ![성공적인 배포를 보고하는 출력 창](./media/web-sites-dotnet-get-started-vs2012/PublishOutput.png)

9.  배포에 성공하면 기본 브라우저에서 배포된 웹 사이트의 URL이 자동으로 열립니다. 만든 응용 프로그램이 이제 클라우드에서 실행되고 있습니다.

    ![Azure에서 실행되는 웹 사이트](./media/web-sites-dotnet-get-started-vs2012/DeployedWebSite.png)

다음 단계다음 단계
------------------

이 자습서에서는 Azure 웹 사이트에 간단한 웹 응용 프로그램을 배포하는 방법을 확인했습니다. 사이트를 관리, 크기 조정 및 문제 해결하는 방법, 데이터베이스, 인증 및 권한 부여 기능을 추가하는 방법, 응용 프로그램을 Azure 웹 사이트 대신 Azure 클라우드 서비스에서 실행해야 할지 결정하는 방법 등에 대한 기타 리소스가 제공됩니다.

### 웹 사이트를 관리하는 방법

사이트 관련 작업을 완료한 경우 사이트를 삭제할 수 있으며, 경우에 따라 일시적으로 오프라인으로 전환하거나 사이트 설정을 변경하려 할 수 있습니다. Visual Studio의 **서버 탐색기**에서 이들 기능의 일부를 수행할 수 있습니다.

![서버 탐색기의 Azure 웹 사이트](./media/web-sites-dotnet-get-started-vs2012/ServerExplorerWSSettings.png)

![Visual Studio의 웹 사이트 구성](./media/web-sites-dotnet-get-started-vs2012/WSConfigurationInVS.png)

웹 사이트를 삭제하려면 Azure 관리 포털을 사용하면 됩니다. 다음 스크린샷은 관리 포털의 **대시보드** 탭에 있는 **중지**, **다시 시작** 및 **삭제** 단추를 보여 줍니다.

![관리 포털 대시보드 탭](./media/web-sites-dotnet-get-started-vs2012/MPStopStartDelete.png)

**구성** 탭에서 사이트 설정을 변경할 수 있습니다. 자세한 내용은 [웹 사이트를 관리하는 방법](/en-us/manage/services/web-sites/how-to-manage-websites/)을 참조하십시오.

### 웹 사이트 크기를 조정하는 방법

사이트가 공용이고 트래픽이 증가하기 시작하면 응답 시간이 느려질 수 있습니다. 이 문제를 해결하기 위해 관리 포털의 **크기 조정** 탭에서 서버 리소스를 쉽게 추가할 수 있습니다.

![관리 포털 크기 조정 탭](./media/web-sites-dotnet-get-started-vs2012/MPScale.png)

자세한 내용은 [웹 사이트 크기를 조정하는 방법](/en-us/manage/services/web-sites/how-to-scale-websites/)을 참조하십시오. 웹 사이트 크기를 조정하기 위해 서버 리소스를 추가하는 것은 무료가 아닙니다.

### 웹 사이트 문제를 해결하는 방법

문제 해결을 위해 추적 또는 로그 출력을 확인할 수도 있습니다. Visual Studio는 Azure 로그가 생성될 때 실시간으로 보기 쉽도록 기본 제공 도구를 제공합니다.

![Visual Studio의 로그](./media/web-sites-dotnet-get-started-vs2012/LogsInVS.png)

자세한 내용은 [Visual Studio에서 Azure 웹 사이트 문제 해결](/en-us/develop/net/tutorials/troubleshoot-web-sites-in-visual-studio/)을 참조하십시오.

### 데이터베이스 및 권한 부여 기능을 추가하는 방법

대부분의 프로덕션 웹 사이트는 데이터베이스를 사용하고 일부 사이트 기능을 권한 있는 특정 사용자로 제한합니다. 데이터베이스 액세스, 인증 및 권한 부여를 시작하는 방법을 보여 주는 자습서는 [Azure 웹 사이트에 멤버 자격, OAuth 및 SQL 데이터베이스가 포함된 보안 ASP.NET MVC 앱 배포](/en-us/develop/net/tutorials/web-site-with-sql-database/)를 참조하십시오.

### 응용 프로그램이 클라우드 서비스에서 실행되어야 하는지 여부를 결정하는 방법

Azure 웹 사이트 대신 Azure 클라우드 서비스에서 응용 프로그램을 실행하려는 경우도 있습니다. 자세한 내용은 [Azure 실행 모델](/en-us/develop/net/fundamentals/compute/)(영문) 및 [Azure 웹 사이트, 클라우드 서비스 및 VM: 각 항목을 사용해야 하는 경우](http://msdn.microsoft.com/en-us/library/windowsazure/jj218759.aspx)(영문)를 참조하십시오. 다중 계층 ASP.NET 웹 응용 프로그램을 만들고 클라우드 서비스에 배포하는 방법을 보여 주는 자습서 시리즈는 [저장소 테이블, 큐 및 Blob을 사용한 .NET 다중 계층 응용 프로그램](/en-us/develop/net/tutorials/multi-tier-web-site/1-overview/)을 참조하십시오.

