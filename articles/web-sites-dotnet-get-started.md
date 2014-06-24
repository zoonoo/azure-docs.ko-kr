<properties  linkid="develop-net-tutorials-get-started-vs2013" urlDisplayName="Get started with Azure for ASP.NET" pageTitle="Get started with Azure for ASP.NET" metaKeywords="" description="This tutorial shows you how to create an ASP.NET web project in Visual Studio 2013 and deploy it to an Azure Web Site. In less than 15 minutes you'll have an app up and running in the cloud." metaCanonical="" services="web-sites" documentationCenter=".NET" title="Get started with Azure and ASP.NET" authors="tdykstra" solutions="" manager="wpickett" editor="mollybos" />
# Azure 및 ASP.NET 시작

 
<div  class="dev-center-tutorial-selector sublanding"><a  href="/en-us/develop/net/tutorials/get-started/" title="Visual Studio 2013" class="current">Visual Studio 2013</a><a  href="/en-us/develop/net/tutorials/get-started-vs2012/" title="Visual Studio 2012">Visual Studio 2012</a></div>

 이 자습서에서는 Visual Studio 2013 또는 Web Express용 Visual Studio 2013을 사용하여
ASP.NET 웹 응용 프로그램을 만들고 Azure 웹 사이트에 배포하는 방법을 보여 줍니다. 이 자습서에서는 이전에 Azure
또는 ASP.NET을 사용한 경험이 없다고 가정합니다. 이 자습서를 완료하면 클라우드에서 간단한 웹 응용 프로그램을 실행할 수
있습니다.

Azure 계정은 무료로 개설할 수 있으며, Visual Studio 2013이 아직 없는 경우 SDK에서 Web Express용
Visual Studio 2013을 자동으로 설치합니다. 따라서 Azure용 개발을 무료로 시작할 수 있습니다.

다음 내용을 배웁니다.

* Azure SDK를 설치하여 사용자 컴퓨터에서 Azure를 개발할 수 있도록 하는 방법
* Visual Studio ASP.NET 웹 프로젝트를 만들고 Azure 웹 사이트에 배포하는 방법
* 프로젝트를 변경하고 다시 배포하는 방법

다음 그림에서는 완료된 응용 프로그램을 보여 줍니다.

![웹 사이트 홈
페이지](./media/web-sites-dotnet-get-started-vs2013/deployedandazure.png)

> [WACOM.NOTE] 이 자습서를 완료하려면 Azure 계정이 필요합니다. 계정이 없는 경우 [MSDN 구독자 혜택을
> 활성화](/en-us/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)하거나 [무료 체험을
> 등록](/en-us/pricing/free-trial/?WT.mc_id=A261C142F)할 수 있습니다.
### 자습서 세그먼트

* [개발 환경 설정](#set-up-the-development-environment)
* [Visual Studio에서 ASP.NET 웹 프로젝트 만들기](#create-an-asp.net-web-project)
* [Azure에 응용 프로그램 배포](#deploy-the-application-to-azure)
* [변경 및 다시 배포](#make-a-change-and-redeploy)
* [다음 단계](#next-steps)

[WACOM.INCLUDE [install-sdk-2013-only](../includes/install-sdk-2013-only.md)]
## ASP.NET 웹 응용 프로그램 만들기

첫 번째 단계에서는 웹 응용 프로그램 프로젝트를 만듭니다. Visual Studio에서 나중에 프로젝트를 배포할 Azure 웹
사이트를 자동으로 만듭니다.

1.  Visual Studio 2013 또는 Visual Studio 2013 Express for Web을 엽니다.

2.  **파일** 메뉴에서 **새 프로젝트**를 클릭합니다.
    
    ![파일 메뉴의 새
    프로젝트](./media/web-sites-dotnet-get-started-vs2013/gs13newproj.png)

3.  **새 프로젝트** 대화 상자에서 **C#** 또는 **Visual Basic**을 확장하고 **설치된 템플릿** 아래의
    **웹**을 선택한 다음 **ASP.NET 웹 응용 프로그램**을 선택합니다.

4.  **.NET Framework 4.5**가 대상 프레임워크로 선택되었는지 확인합니다.

5.  응용 프로그램 이름을 **MyExample**로 지정하고 **확인**을 클릭합니다.
    
    ![새 프로젝트 대화
    상자](./media/web-sites-dotnet-get-started-vs2013/GS13newprojdb.png)

6.  **새 ASP.NET 프로젝트** 대화 상자에서 **MVC** 또는 **Web Forms** 템플릿을 선택하고 **인증
    변경**을 클릭합니다.
    
    [MVC 및 Web Forms][1]는 웹 사이트를 만들기 위한 ASP.NET 프레임워크입니다. 기본 설정이 없고 다른
    Azure 자습서를 수행하려는 경우 MVC를 사용하는 Azure 자습서가 더 많으므로 MVC를 선택하는 것이 좋습니다.
    
    ![새 ASP.NET 프로젝트 대화
    상자](./media/web-sites-dotnet-get-started-vs2013/GS13changeauth.png)

7.  **인증 변경** 대화 상자에서 **인증 없음**, **확인**을 차례로 클릭합니다.
    
    ![인증 없음](./media/web-sites-dotnet-get-started-vs2013/GS13noauth.png)
    
    만드는 샘플 응용 프로그램에는 사용자 로그인을 요구하는 기능이 없습니다. 이 자습서 끝에 있는 [다음
    단계](#next-steps) 섹션에 인증 및 권한 부여를 구현하는 자습서에 대한 링크가 있습니다.

8.  대화 상자의 **Azure**에서 확인란은 선택되고 드롭다운 상자는 **웹 사이트**로 설정된 상태로 유지합니다.
    
    확인란 캡션은 **Host in the cloud** 또는 **Create remote resources**일 수
    있습니다. 어느 경우든지 효과는 같습니다.
    
    이러한 설정은 Visual Studio에서 웹 프로젝트용 Azure 웹 사이트를 만들도록 합니다. 새로 만든 웹 사이트에
    웹 프로젝트를 배포합니다. 대신 Visual Studio에서 IIS가 실행되는 Azure 가상 컴퓨터를 만들도록 드롭다운
    상자 선택을 변경할 수 있지만 이 자습서에서는 해당 옵션에 대한 단계를 자세히 설명하지 않습니다.

9.  **새 ASP.NET 프로젝트** 대화 상자에서 **확인**을 클릭합니다.
    
    ![새 ASP.NET 프로젝트 대화
    상자](./media/web-sites-dotnet-get-started-vs2013/GS13newaspnetprojdb.png)
    
    이 스크린샷에서는 MVC 템플릿이 선택된 것을 보여 줍니다. Web Forms를 선택한 경우 **Web Forms**가
    선택되어 있습니다.

10. Azure에 아직 로그인하지 않은 경우 로그인하라는 메시지가 Visual Studio에 표시됩니다. **로그인**을
    클릭합니다.
    
    ![Azure에
    로그인](./media/web-sites-dotnet-get-started-vs2013/signin.png)

11. Azure 구독을 관리하는 데 사용하는 계정의 ID 및 암호를 입력합니다.
    
    ![Azure에
    로그인](./media/web-sites-dotnet-get-started-vs2013/signindb.png)
    
    로그인하면 만들려는 리소스에 대해 묻는 **Configure Azure Site Settings** 대화 상자가
    표시됩니다.
    
    ![Azure에
    로그인함](./media/web-sites-dotnet-get-started-vs2013/configuresitesettings.png)

12. Visual Studio에서 기본 **사이트 이름**을 제공하며, Azure에서 응용 프로그램 URL의 접두사로
    사용합니다. 원하는 경우 다른 사이트 이름을 입력합니다.
    
    전체 URL은 여기에 입력한 문자열과 텍스트 상자 옆에 표시되는 도메인으로 구성됩니다. 예를 들어 사이트 이름이
    `MyExample6442`이면 URL은 `MyExample6442.azurewebsites.net`이 됩니다. 입력한
    URL을 다른 사용자가 이미 사용한 경우 녹색 확인 표시 대신 오른쪽에 빨간색 느낌표가 표시되며, 다른 값을 입력해야
    합니다.

13. **지역** 드롭다운 목록에서 가장 가까운 위치를 선택합니다.
    
    이 설정은 웹 사이트가 실행되는 Azure 데이터 센터를 지정합니다.

14. 데이터베이스 필드를 변경하지 않고 그대로 둡니다.
    
    이 자습서에서는 데이터베이스를 사용하지 않습니다. 자습서 끝에 있는 [다음 단계](#next-steps) 섹션에
    데이터베이스 사용 방법을 보여 주는 자습서 링크가 있습니다.

15. **확인**을 클릭합니다.
    
    몇 초 후 Visual Studio에서 지정된 폴더에 웹 프로젝트를 만들고 지정된 Azure 지역에 웹 사이트를 만듭니다.
    
    **솔루션 탐색기** 창에 새 프로젝트의 파일 및 폴더가 표시됩니다. 스크린샷은 Web Forms 프로젝트용이며 MVC
    프로젝트는 폴더와 파일이 다릅니다.
    
    ![솔루션
    탐색기](./media/web-sites-dotnet-get-started-vs2013/solutionexplorer.png)
    
    **Web Publish Activity** 창에 사이트가 만들어졌다고 표시됩니다.
    
    ![웹 사이트가
    만들어짐](./media/web-sites-dotnet-get-started-vs2013/GS13sitecreated1.png)
## Azure에 응용 프로그램 배포

1.  **Web Publish Activity** 창에서 **Publish MyExample to this site now**를
    클릭합니다.
    
    ![웹 사이트가
    만들어짐](./media/web-sites-dotnet-get-started-vs2013/GS13sitecreated.png)
    
    몇 초 후에 **웹 게시** 마법사가 나타납니다. 이 마법사는 Azure에 프로젝트를 배포하기 위해 Visual
    Studio에 필요한 웹 사이트 URL과 같은 설정을 포함하는 새 *게시 프로필*을 만듭니다. 이 프로필은 자동으로
    저장되므로 나중에 프로젝트를 변경할 때 같은 사이트에 프로젝트를 쉽게 다시 배포할 수 있습니다.

2.  **웹 게시** 마법사의 **연결** 탭에서 **연결 유효성 검사**를 클릭하여 Visual Studio에서 웹 프로젝트를
    배포하기 위해 Azure에 연결할 수 있는지 확인합니다.
    
    ![연결 유효성
    검사](./media/web-sites-dotnet-get-started-vs2013/GS13ValidateConnection.png)
    
    연결 유효성이 검사되면 **연결 유효성 검사** 단추 옆에 녹색 확인 표시가 나타납니다.

3.  **다음**을 클릭합니다.
    
    ![유효성이 검사된
    연결](./media/web-sites-dotnet-get-started-vs2013/GS13ValidateConnectionSuccess.png)

4.  **설정** 탭에서 **다음**을 클릭합니다.
    
    ![설정
    탭](./media/web-sites-dotnet-get-started-vs2013/GS13SettingsTab.png)
    
    이 탭에서 기본 설정을 적용할 수 있습니다. 릴리스 빌드를 배포 중이며 대상 서버의 파일을 삭제할 필요가 없는 경우 응용
    프로그램을 미리 컴파일하거나 App\_Data 폴더의 파일을 제외합니다. 이 자습서 끝에 있는 [다음
    단계](#next-steps) 섹션에 디버그 빌드를 배포하고 디버그 모드에서 원격으로 Visual Studio를 실행하는
    방법을 보여 주는 자습서에 대한 링크가 있습니다.

5.  **미리 보기** 탭에서 **미리 보기 시작**을 클릭합니다.
    
    ![미리 보기 탭의 미리 보기 시작
    단추](./media/web-sites-dotnet-get-started-vs2013/GS13Preview.png)
    
    이 탭에는 서버로 복사될 파일 목록이 표시됩니다. 미리 보기 표시는 응용 프로그램을 게시하는 데 필요하지 않지만 알아 두면
    유용한 기능입니다.

6.  **게시**를 클릭합니다.
    
    ![StartPreview 파일
    출력](./media/web-sites-dotnet-get-started-vs2013/GS13previewoutput.png)
    
    Visual Studio에서 Azure 서버로 파일을 복사하는 프로세스를 시작합니다.
    
    **출력** 및 **Web Publish Activity** 창에 수행된 배포 작업이 표시되고 성공적인 배포 완료가
    보고됩니다.
    
    ![성공적인 배포를 보고하는 출력
    창](./media/web-sites-dotnet-get-started-vs2013/PublishOutput.png)
    
    배포에 성공하면 기본 브라우저에서 배포된 웹 사이트의 URL이 자동으로 열리며, 만든 응용 프로그램이 이제 클라우드에서
    실행되고 있습니다. 브라우저 주소 표시줄의 URL은 사이트가 인터넷에서 로드되고 있음을 보여 줍니다.
    
    ![Azure에서 실행되는 웹
    사이트](./media/web-sites-dotnet-get-started-vs2013/GS13deployedsite.png)

7.  브라우저를 닫습니다.
## 변경 및 다시 배포

자습서의 이 섹션(옵션)에서는 웹 프로젝트를 변경하고 개발 컴퓨터에서 로컬로 프로젝트를 실행하여 변경 내용을 확인한 다음
Azure에 변경 내용을 배포합니다.

1.  MVC 프로젝트를 만든 경우 **솔루션 탐색기**에서 *Views/Home/Index.cshtml* 또는 *.vbhtml*
    파일을 열고 **h1** 제목을 "ASP.NET"에서 "ASP.NET and Azure"로 변경한 다음 파일을 저장합니다.
    
    ![MVC
    index.cshtml](./media/web-sites-dotnet-get-started-vs2013/index.png)
    
    ![MVC h1
    변경](./media/web-sites-dotnet-get-started-vs2013/mvcandazure.png)

2.  Web Forms 프로젝트를 만든 경우 **솔루션 탐색기**에서 *Default.aspx* 파일을 열고 **h1** 제목을
    "ASP.NET"에서 "ASP.NET and Azure"로 변경한 다음 파일을 저장합니다.
    
    ![Web Forms
    default.aspx](./media/web-sites-dotnet-get-started-vs2013/default.png)
    
    ![Web Forms h1
    변경](./media/web-sites-dotnet-get-started-vs2013/wfandazure.png)

3.  CTRL+F5를 눌러 로컬 컴퓨터에서 사이트를 실행함으로써 변경 내용을 테스트합니다.
    
    ![로컬에서 실행 중인 웹
    사이트](./media/web-sites-dotnet-get-started-vs2013/localandazure.png)
    
    `http://localhost` URL은 로컬 컴퓨터에서 실행되고 있음을 보여 줍니다. 기본적으로 웹 응용 프로그램 개발
    중 사용하도록 설계된 IIS 경량 버전인 IIS Express에서 실행됩니다.

4.  브라우저를 닫습니다.

5.  **솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시**를 선택합니다.
    
    ![게시
    선택](./media/web-sites-dotnet-get-started-vs2013/choosepublish.png)
    
    **웹 게시** 마법사의 미리 보기 탭이 나타납니다. 게시 설정을 변경해야 하는 경우 다른 탭을 선택할 수 있지만 이제
    같은 설정으로 다시 배포하면 됩니다.

6.  **웹 게시** 마법사에서 **게시**를 클릭합니다.
    
    ![게시
    클릭](./media/web-sites-dotnet-get-started-vs2013/clickpublish.png)
    
    Visual Studio에서 Azure에 프로젝트를 배포하고 기본 브라우저에서 사이트를 엽니다.
    
    ![변경되어 배포된
    사이트](./media/web-sites-dotnet-get-started-vs2013/deployedandazure.png)

게시 설정을 변경할 필요가 없는 경우 **한 번 클릭으로 웹 게시** 도구 모음을 사용하면 훨씬 더 빠르게 다시 배포할 수
있습니다.

![한 번 클릭으로 웹 게시 도구
모음](./media/web-sites-dotnet-get-started-vs2013/weboneclickpublish.png)

이 도구 모음은 기본적으로 사용하도록 설정되어 있지 않습니다. **보기 - 도구 모음** 메뉴에서 사용하도록 설정합니다. 이 도구
모음을 사용하여 프로필을 선택하거나, 단추를 클릭하여 게시하거나, 단추를 클릭하여 **웹 게시** 마법사를 열 수 있습니다.
## 다음 단계

이 자습서에서는 간단한 웹 응용 프로그램을 만들고 Azure 웹 사이트에 배포하는 방법을 확인했습니다. 다음은 자세한 내용을
확인할 수 있는 몇 가지 관련 항목 및 리소스입니다.

* 웹 프로젝트를 배포하는 다른 방법
* 사이트의 관리, 크기 조정 및 문제 해결을 수행하는 방법
* 데이터베이스 및 권한 부여 기능을 추가하는 방법
* 웹 응용 프로그램용 웹 사이트, 클라우드 서비스 및 VM을 선택하는 방법
### 웹 프로젝트를 배포하는 다른 방법

이 자습서에서는 단일 작업으로 사이트를 만들고 배포하는 가장 빠른 방법을 확인했습니다. Visual Studio를 사용하거나
[소스 제어 시스템][2]에서 [배포를 자동화][3]하여 배포하는 다른 방법에 대한 개요는 [Azure 웹 사이트를 배포하는
방법](/en-us/documentation/articles/web-sites-deploy/")을 참조하십시오.

배포를 자동화하는 한 가지 방법으로 Windows PowerShell 스크립트를 사용합니다. Visual Studio 및
Azure는 Visual Studio에서 수행할 수 있는 것과 동일한 배포 작업을 수행하는 데 사용할 수 있는 PowerShell
스크립트를 생성하여 해당 작업을 간소화합니다. 자세한 내용은 [자동화(Azure에서 실제 클라우드 앱 빌드)][4](영문)를
참조하십시오.
### 웹 사이트를 관리하는 방법

[Azure 관리 포털](en-us/services/management-portal/)은 웹 사이트, 클라우드 서비스, 가상
컴퓨터, 데이터베이스 등의 Azure 서비스를 관리하고 모니터링하는 데 사용할 수 있는 웹 인터페이스입니다. 포털에서 수행할 수
있는 작업을 확인하려면 [https://manage.windowsazure.com]()으로 이동하여 Azure 구독에 대한 관리
권한이 있는 계정의 사용자 이름 및 암호로 로그인하십시오. 자세한 내용은 [웹 사이트를 관리하는
방법](/en-us/manage/services/web-sites/how-to-manage-websites/)을 참조하십시오.

Visual Studio의 **서버 탐색기**에서 바로 일부 웹 사이트 관리 기능을 수행할 수도 있습니다. **서버 탐색기**에서
수행할 수 있는 작업에 대한 자세한 내용은 [Visual Studio에서 Azure 웹 사이트 문제
해결](/en-us/develop/net/tutorials/troubleshoot-web-sites-in-visual-studio/)을
참조하십시오.
### 웹 사이트 크기를 조정하는 방법

사이트가 공용이고 트래픽이 증가하기 시작하면 응답 시간이 느려질 수 있습니다. 이 문제를 해결하기 위해 관리 포털의
**Scale** 탭에서 서버 리소스를 쉽게 추가할 수 있습니다. 자세한 내용은 [웹 사이트 크기를 조정하는
방법](/en-us/manage/services/web-sites/how-to-scale-websites/)을 참조하십시오. 웹
사이트 크기를 조정하기 위해 서버 리소스를 추가하는 것은 무료가 아닙니다.
### 웹 사이트 문제를 해결하는 방법

문제 해결을 위해 추적 또는 로그 출력을 확인할 수도 있습니다. Visual Studio는 Azure 로그가 생성될 때 실시간으로
보기 쉽도록 기본 제공 도구를 제공합니다. 원격으로 Azure에서 디버그 모드로 실행할 수도 있습니다. 자세한 내용은
[Visual Studio에서 Azure 웹 사이트 문제
해결](/en-us/develop/net/tutorials/troubleshoot-web-sites-in-visual-studio/)을
참조하십시오.
### 데이터베이스 및 권한 부여 기능을 추가하는 방법

대부분의 프로덕션 웹 사이트는 데이터베이스를 사용하고 일부 사이트 기능을 권한 있는 특정 사용자로 제한합니다. 데이터베이스
액세스, 인증 및 권한 부여를 시작하는 방법을 보여 주는 자습서는 [Azure 웹 사이트에 멤버 자격, OAuth 및 SQL
데이터베이스가 포함된 보안 ASP.NET MVC 앱
배포](/en-us/develop/net/tutorials/web-site-with-sql-database/)를 참조하십시오.
### 응용 프로그램이 클라우드 서비스에서 실행되어야 하는지 여부를 결정하는 방법

Azure에서는 웹 응용 프로그램을 웹 사이트(이 자습서에 설명됨), 클라우드 서비스 또는 가상 컴퓨터에서 실행할 수 있습니다.
자세한 내용은 [Azure 실행 모델](/en-us/develop/net/fundamentals/compute/)(영문) 및
[Azure 웹 사이트, 클라우드 서비스 및 VM: 각 항목을 사용해야 하는
경우](/en-us/manage/services/web-sites/choose-web-app-service/)(영문)를
참조하십시오.



[1]: http://www.asp.net/get-started/websites
[2]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control
[3]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/continuous-integration-and-continuous-delivery
[4]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything
