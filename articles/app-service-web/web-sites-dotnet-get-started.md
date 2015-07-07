<properties
	pageTitle="Azure 앱 서비스에서 ASP.NET 웹 응용 프로그램 만들기"
	description="이 자습서에서는 Visual Studio 2013에서 ASP.NET 웹 프로젝트를 만들어 Azure 앱 서비스의 앱 응용 프로그램에 배포하는 방법에 대해 알아봅니다. 15분 이내에 클라우드에서 앱을 작동 및 실행할 수 있습니다."
	services="app-service\web"
	documentationCenter=".net"
	authors="tdykstra"
	manager="wpickett"
	editor="mollybos"/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="get-started-article" 
	ms.date="03/24/2015"
	ms.author="tdykstra"/>

# Azure 앱 서비스에서 ASP.NET 웹 응용 프로그램 만들기

## 개요

이 자습서에서는 Visual Studio 2013 또는 Web Express용 Visual Studio 2013을 사용하여 ASP.NET 웹 응용 프로그램을 만들고 [앱 서비스 웹앱](http://go.microsoft.com/fwlink/?LinkId=529714)에 배포하는 방법을 보여 줍니다. 이 자습서에서는 이전에 Azure 또는 ASP.NET을 사용한 경험이 없다고 가정합니다. 이 자습서를 완료하면 클라우드에서 간단한 웹 응용 프로그램을 실행할 수 있습니다.

다음 내용을 배웁니다.

* Azure SDK를 설치하여 사용자 컴퓨터에서 Azure를 개발할 수 있도록 하는 방법
* Visual Studio ASP.NET 웹 프로젝트를 만들고 Azure 웹 응용 프로그램에 배포하는 방법
* 웹 프로젝트를 변경하고 응용 프로그램을 다시 배포하는 방법
* [Azure 포털](http://go.microsoft.com/fwlink/?LinkId=529715)을 사용하여 웹앱을 모니터링 및 관리하는 방법

이 자습서를 완료하려면 Azure 계정이 있어야 합니다.

* [Azure 계정을 무료로 개설](/pricing/free-trial/?WT.mc_id=A261C142F)할 수 있음 - 유료 Azure 서비스를 사용해볼 수 있는 크레딧을 받게 되며 크레딧을 모두 사용한 후에도 계정을 유지하고 무료 Azure 서비스(예: 웹 서비스 웹앱)를 사용할 수 있습니다.
* [MSDN 구독자 혜택을 활성화](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)할 수 있음 - MSDN 구독은 유료 Azure 서비스에 사용할 수 있는 크레딧을 매달 제공합니다.</li>

다음 그림에서는 완료된 응용 프로그램을 보여 줍니다.

![웹앱 홈 페이지](./media/web-sites-dotnet-get-started/deployedandazure.png)

##<a name="video"></a>Microsoft Azure 등록(비디오)

이 비디오에서 Scott Hanselman은 Microsoft Azure의 무료 평가판 등록이 얼마나 쉬운지 보여 줍니다. (소요 시간: 1분 58초)

> [AZURE.VIDEO sign-up-for-microsoft-azure]

[AZURE.INCLUDE [install-sdk-2013-only](../../includes/install-sdk-2013-only.md)]

## ASP.NET 웹 응용 프로그램 만들기

첫 번째 단계에서는 웹 응용 프로그램 프로젝트를 만듭니다. Visual Studio에서 나중에 프로젝트를 배포할 Azure 웹 응용 프로그램을 자동으로 만듭니다.

1. Visual Studio 2013 또는 Visual Studio 2013 Express for Web을 엽니다.

2. **파일** 메뉴에서 **새 프로젝트**를 클릭합니다.

3. **새 프로젝트** 대화 상자에서 **C#** > **웹** > **ASP.NET 웹 응용 프로그램**을 클릭합니다. 원하는 경우 **Visual Basic**을 선택할 수 있습니다.

3. **.NET Framework 4.5**가 대상 프레임워크로 선택되었는지 확인합니다.

4. **프로젝트에 Application Insights 추가** 확인란의 선택을 취소합니다.

4. 응용 프로그램 이름을 **MyExample**로 지정하고 **확인**을 클릭합니다.

	![새 프로젝트 대화 상자](./media/web-sites-dotnet-get-started/GS13newprojdb.png)

5. **새 ASP.NET 프로젝트** 대화 상자에서 **MVC** 템플릿을 선택합니다. ASP.NET Web Forms에서 작업하려는 경우 **Web Forms** 템플릿을 선택할 수 있습니다.

	[MVC 및 Web Forms](http://www.asp.net/get-started/websites)는 웹앱을 개발하기 위한 ASP.NET 프레임워크입니다. 이 자습서에서는 둘 중 아무것이든 선택할 수 있지만 Web Forms를 선택하는 경우 나중에 자습서에서 *Index.cshtml*을 편집하라는 내용이 나올 때 *Default.aspx*를 편집해야 합니다.

7. **인증 변경**을 클릭합니다.

	![새 ASP.NET 프로젝트 대화 상자](./media/web-sites-dotnet-get-started/GS13changeauth.png)

6. **인증 변경** 대화 상자에서 **인증 없음**, **확인**을 차례로 클릭합니다.

	![인증 없음](./media/web-sites-dotnet-get-started/GS13noauth.png)

	여기서 만드는 샘플 응용 프로그램에는 사용자가 로그인할 수 없습니다. [다음 단계](#next-steps) 섹션은 인증 및 권한 부여를 구현하는 자습서로 연결됩니다.

5. **새 ASP.NET 프로젝트** 대화 상자에서 **MicrosoftAzure** 아래의 설정을 그대로 둔 다음 **확인**을 클릭합니다.

	![새 ASP.NET 프로젝트 대화 상자](./media/web-sites-dotnet-get-started/GS13newaspnetprojdb.png)

	기본 설정은 Visual Studio에서 웹 프로젝트용 Azure 웹 응용 프로그램을 만들도록 지정합니다. 자습서의 다음 섹션에서는 새로 만든 웹 응용 프로그램에 웹 프로젝트를 배포합니다.

5. Azure에 아직 로그인하지 않은 경우 로그인하라는 메시지가 Visual Studio에 표시됩니다. Azure 구독을 관리하는 데 사용하는 계정의 ID 및 암호로 로그인합니다.

	로그인하면 만들려는 리소스에 대해 묻는 **Microsoft Azure 웹앱 설정 구성** 대화 상자가 표시됩니다.

	![Azure에 로그인함](./media/web-sites-dotnet-get-started/configuresitesettings.png)

3. **Microsoft Azure 웹앱 설정 구성** 대화 상자에서 기본값을 그대로 둡니다.

	필요한 경우 다른 **웹앱 이름**을 입력할 수 있지만 이름은 *azurewebsites.net* 도메인에서 고유해야 합니다. Visual Studio에서 제공하는 기본 이름을 고유합니다.

	Azure에서는 응용 프로그램의 URL에 대한 접두사로 이 이름을 사용합니다. 전체 URL은 이 이름과 *.azurewebsites.net*(**웹앱 이름** 텍스트 상자 옆에 표시)으로 구성됩니다. 예를 들어 이름이 `MyExample6442`이면 URL은 `MyExample6442.azurewebsites.net`이 됩니다. URL은 고유해야 합니다. 입력한 URL을 다른 사용자가 이미 사용한 경우 녹색 확인 표시 대신 오른쪽에 빨간색 느낌표가 표시되며, 다른 이름을 입력해야 합니다.

4. **앱 서비스 계획** 드롭다운에서 **새 앱 서비스 계획 만들기**를 선택합니다.

	자습서의 마지막 부분에 있는 [다음 단계](#next-steps) 섹션에는 앱 서비스 계획에 대한 정보의 링크가 있습니다.

5. 계획 이름에 *MyExamplePlan* 또는 원하는 다른 이름을 입력합니다.

6. **리소스 그룹** 드롭다운에서 **새 리소스 그룹 만들기**를 선택합니다.

	[다음 단계](#next-steps) 섹션에 리소스 그룹에 대한 정보의 링크가 있습니다.

5. 리소스 그룹 이름에 *MyExampleResourceGroup* 또는 원하는 다른 이름을 입력합니다.

5. **지역** 드롭다운 목록에서 가장 가까운 위치를 선택합니다.

	이 설정은 웹앱이 실행되는 Azure 데이터 센터를 지정합니다. 이 자습서에서는 어떤 지역이든 선택할 수 있으며 지역에 따른 뚜렷한 차이는 없지만 프로덕션 웹 응용 프로그램의 경우 [대기 시간](http://www.bing.com/search?q=web%20latency%20introduction&qs=n&form=QBRE&pq=web%20latency%20introduction&sc=1-24&sp=-1&sk=&cvid=eefff99dfc864d25a75a83740f1e0090)을 최소화하기 위해 사용자 사이트에 액세스하는 브라우저와 최대한 가깝게 웹 서버를 배치할 수 있습니다.

5. 데이터베이스 필드를 변경하지 않고 그대로 둡니다.

	이 자습서에서는 데이터베이스를 사용하지 않습니다. 자습서 끝에 있는 [다음 단계](#next-steps) 섹션에 데이터베이스 사용 방법을 보여 주는 자습서 링크가 있습니다.

6. **확인**을 클릭합니다.

	![Azure에 로그인함](./media/web-sites-dotnet-get-started/configuresitesettings2.png)

	몇 초 후 Visual Studio에서 지정된 폴더에 웹 프로젝트를 만들고 지정된 Azure 지역에 웹 응용 프로그램을 만듭니다.

	**솔루션 탐색기** 창에 새 프로젝트의 파일 및 폴더가 표시됩니다.

	![솔루션 탐색기](./media/web-sites-dotnet-get-started/solutionexplorer.png)

	**Azure 앱 서비스 작업** 창에 웹앱이 만들어졌다고 표시됩니다.

	![웹앱이 생성됨](./media/web-sites-dotnet-get-started/GS13sitecreated1.png)

	이제 서버 탐색기에서 웹 응용 프로그램을 볼 수 있습니다.

	![웹앱이 생성됨](./media/web-sites-dotnet-get-started/siteinse.png)

## Azure에 응용 프로그램 배포

7. **Azure 앱 서비스 작업** 창에서 **지금 이 웹앱에 MyExample 게시**를 클릭합니다.

	![웹앱이 생성됨](./media/web-sites-dotnet-get-started/GS13sitecreated.png)

	몇 초 후에 **웹 게시** 마법사가 나타납니다.

	Azure에 프로젝트를 배포하는 데 필요한 Visual Studio의 설정이 *게시 프로필*에 저장되었습니다. 이 마법사에서 해당 설정을 확인하고 변경할 수 있습니다.

8. **웹 게시** 마법사의 **연결** 탭에서 **연결 유효성 검사**를 클릭하여 Visual Studio에서 웹 프로젝트를 배포하기 위해 Azure에 연결할 수 있는지 확인합니다.

	![연결 유효성 검사](./media/web-sites-dotnet-get-started/GS13ValidateConnection.png)

	연결 유효성이 검사되면 **연결 유효성 검사** 단추 옆에 녹색 확인 표시가 나타납니다.

9. **다음**을 클릭합니다.

	![유효성이 검사된 연결](./media/web-sites-dotnet-get-started/GS13ValidateConnectionSuccess.png)

10. **설정** 탭에서 **다음**을 클릭합니다.

	![설정 탭](./media/web-sites-dotnet-get-started/GS13SettingsTab.png)

	**구성** 및 **파일 게시 옵션**의 기본값을 그대로 사용합니다.

	**구성** 드롭다운에서 원격 디버깅을 위한 디버그 빌드를 배포할 수 있습니다. [다음 단계](#next-steps) 섹션은 Visual Studio를 원격에서 디버그 모드로 실행하는 방법을 보여 주는 자습서로 연결됩니다.

	**파일 게시 옵션**을 확장하면 이 자습서에는 적용되지 않는 다음과 같은 상황을 처리할 수 있는 몇 가지 설정이 표시됩니다.

	* 대상에 있는 추가적인 파일을 제거합니다.

		서버에서 프로젝트에 포함되지 않은 파일을 모두 삭제합니다. 앞서 다른 프로젝트를 배포한 웹 응용 프로그램에 프로젝트를 배포하는 경우 이 작업을 수행해야 할 수 있습니다.

	* 게시 중 미리 컴파일합니다.

		대형 응용 프로그램에 대한 최초 요청 준비 시간을 줄일 수 있습니다.

	* App_Data 폴더에서 파일을 실행합니다.

		테스팅을 위해 프로덕션에 배포하지 않을 SQL Server 데이터베이스 파일을 App_Data에 포함하는 경우가 가끔 있습니다.

11. **미리 보기** 탭에서 **미리 보기 시작**을 클릭합니다.

	![미리 보기 탭의 미리 보기 시작 단추](./media/web-sites-dotnet-get-started/GS13Preview.png)

	이 탭에는 서버로 복사될 파일 목록이 표시됩니다. 미리 보기 표시는 응용 프로그램을 게시하는 데 필요하지 않지만 알아 두면 유용한 기능입니다.

12. **게시**를 클릭합니다.

	![StartPreview 파일 출력](./media/web-sites-dotnet-get-started/GS13previewoutput.png)

	Visual Studio에서 Azure 서버로 파일을 복사하는 프로세스를 시작합니다.

	**출력** 및 **Web Publish Activity** 창에 수행된 배포 작업이 표시되고 성공적인 배포 완료가 보고됩니다.

	![성공적인 배포를 보고하는 출력 창](./media/web-sites-dotnet-get-started/PublishOutput.png)

	배포에 성공하면 기본 브라우저에서 배포된 웹 응용 프로그램의 URL이 자동으로 열리며, 만든 응용 프로그램이 이제 클라우드에서 실행되고 있습니다. 브라우저 주소 표시줄의 URL은 웹 응용 프로그램이 인터넷에서 로드되고 있음을 보여 줍니다.

	![Azure에서 실행되는 웹앱](./media/web-sites-dotnet-get-started/GS13deployedsite.png)

13. 브라우저를 닫습니다.

## 변경 및 다시 배포

자습서의 이 섹션에서는 홈페이지의 **h1** 제목을 변경하고 개발 컴퓨터에서 로컬로 프로젝트를 실행하여 변경 내용을 확인한 다음 Azure에 변경 내용을 배포합니다.

2. *솔루션 탐색기*에서 *Views/Home/Index.cshtml* 또는 **.vbhtml** 파일을 열고 **h1** 제목을 "ASP.NET"에서 "ASP.NET and Azure"로 변경한 다음 파일을 저장합니다.

	![MVC index.cshtml](./media/web-sites-dotnet-get-started/index.png)

	![MVC h1 변경](./media/web-sites-dotnet-get-started/mvcandazure.png)

1. Ctrl+F5 키를 눌러서 로컬 컴퓨터에서 웹 응용 프로그램을 실행하여 업데이트된 제목을 표시합니다.

	![로컬 실행되는 웹앱](./media/web-sites-dotnet-get-started/localandazure.png)

	`http://localhost` URL은 해당 사이트가 로컬 컴퓨터에서 실행되고 있음을 보여 줍니다. 기본적으로 웹 응용 프로그램 개발 중 사용하도록 설계된 IIS 경량 버전인 IIS Express에서 실행됩니다.

1. 브라우저를 닫습니다.

1. **솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시**를 선택합니다.

	![게시 선택](./media/web-sites-dotnet-get-started/choosepublish.png)

	**웹 게시** 마법사의 미리 보기 탭이 나타납니다. 게시 설정을 변경해야 하는 경우 다른 탭을 선택할 수 있지만 이제 같은 설정으로 다시 배포하면 됩니다.

2. **웹 게시** 마법사에서 **게시**를 클릭합니다.

	![게시 클릭](./media/web-sites-dotnet-get-started/clickpublish.png)

	Visual Studio에서 Azure에 프로젝트를 배포하고 기본 브라우저에서 웹 응용 프로그램을 엽니다.

	![변경된 웹앱 배포](./media/web-sites-dotnet-get-started/deployedandazure.png)

**팁:** 보다 빠른 배포를 위해 **한 번 클릭으로 웹 게시** 도구 모음을 사용할 수 있습니다. **보기** > **도구 모음**을 클릭한 다음 **한 번 클릭으로 웹 게시**를 선택합니다. 이 도구 모음을 사용하여 프로필을 선택하거나, 단추를 클릭하여 게시하거나, 단추를 클릭하여 **웹 게시** 마법사를 열 수 있습니다.

![한 번 클릭으로 웹 게시 도구 모음](./media/web-sites-dotnet-get-started/weboneclickpublish.png)

## 관리 포털에서 웹 응용 프로그램 모니터링 및 관리

[Azure 관리 포털](/services/management-portal/)은 Azure 서비스(예: 방금 만든 웹 응용 프로그램)를 관리하고 모니터링하는 데 사용할 수 있는 웹 인터페이스입니다. 자습서의 이 섹션에서는 포털에서 수행할 수 있는 작업 중 일부를 살펴봅니다.

1. 브라우저에서 [https://portal.azure.com]()으로 이동하여 Azure 자격 증명으로 로그인합니다.

2. **찾아보기 > 웹앱**을 클릭한 다음 웹 응용 프로그램의 이름을 클릭합니다.

	웹앱에 대한 **웹앱** 블레이드에 자주 사용되는 웹앱 관리 기능에 대한 링크 및 사용량 통계가 표시됩니다.

	![웹앱 블레이드](./media/web-sites-dotnet-get-started/portaldashboard.png)-->

	지금은 웹 응용 프로그램의 트래픽이 그다지 많지 않아서 그래픽에 아무것도 표시되지 않을 수 있습니다. 응용 프로그램을 탐색하고, 페이지를 몇 번 새로 고친 다음 포털 페이지를 새로 고치면 몇 가지 통계가 표시됩니다.

3. 더 많은 웹앱 구성 옵션을 보려면 **모든 설정**을 클릭합니다.

	설정 유형 목록이 표시됩니다.

	![](./media/web-sites-dotnet-get-started/portalconfigure1.png)-->

4. **응용 프로그램 설정**을 클릭하여 포털에서 구성할 수 설정 유형의 예를 볼 수 있습니다.

	예를 들어 웹앱에서 사용되는 .NET 버전을 제어하고, [WebSockets](/blog/2013/11/14/introduction-to-websockets-on-windows-azure-web-sites/)와 같은 기능을 활성화하고, [연결 문자열 값](/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/)을 설정하는 등의 작업을 수행할 수 있습니다.

	![포털 웹앱 구성 탭](./media/web-sites-dotnet-get-started/portalconfigure2.png)

이러한 기능은 관리 포털의 일부에 지나지 않습니다. 또한 새 웹 응용 프로그램을 만들고, 기존 응용 프로그램을 삭제하고, 웹 응용 프로그램을 중지 및 다시 시작하고, 다른 Azure 서비스(예: 데이터베이스 및 가상 컴퓨터)를 관리할 수도 있습니다.

>[AZURE.NOTE]Azure 계정을 등록하기 전에 Azure 앱 서비스를 시작하려면 [앱 서비스 평가](http://go.microsoft.com/fwlink/?LinkId=523751)로 이동합니다. 앱 서비스에서 단기 스타터 웹앱을 즉시 만들 수 있습니다. 신용 카드는 필요하지 않으며 약정도 필요하지 않습니다.

## 다음 단계

이 자습서에서는 간단한 웹 응용 프로그램을 만들고 Azure 웹 응용 프로그램에 배포하는 방법을 확인했습니다. 다음은 자세한 내용을 확인할 수 있는 몇 가지 관련 항목 및 리소스입니다.

* 웹 프로젝트를 배포하는 다른 방법

	이 자습서에서는 단일 작업으로 웹 응용 프로그램을 만들고 배포하는 가장 빠른 방법을 확인했습니다. Visual Studio를 사용하거나 [소스 제어 시스템](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control)에서 [배포를 자동화](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/continuous-integration-and-continuous-delivery)하여 배포하는 다른 방법에 대한 개요는 [Azure 웹앱을 배포하는 방법](web-sites-deploy.md)을 참조하세요.

	Visual Studio에서 배포를 자동화할 수 있는 Windows PowerShell 스크립트를 생성할 수도 있습니다. 자세한 내용은 [자동화(Azure에서 실제 클라우드 앱 빌드)](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything)(영문)를 참조하십시오.

* Visual Studio에서 웹 응용 프로그램을 관리하는 방법

	**서버 탐색기**에서 수행할 수 있는 웹앱 관리 기능에 대한 자세한 내용은 [Visual Studio에서 Azure 웹앱 문제 해결](web-sites-dotnet-troubleshoot-visual-studio.md)을 참조하세요.

* 웹 응용 프로그램 문제를 해결하는 방법

	Visual Studio는 Azure 로그가 생성될 때 실시간으로 보기 쉽도록 만드는 기능을 제공합니다. 원격으로 Azure에서 디버그 모드로 실행할 수도 있습니다. 자세한 내용은 [Visual Studio에서 Azure 웹앱 문제 해결](web-sites-dotnet-troubleshoot-visual-studio.md)을 참조하세요.

* 데이터베이스 및 권한 부여 기능을 추가하는 방법

	데이터베이스에 액세스하는 방법과 일부 응용 프로그램 기능을 인증된 사용자만 사용할 수 있도록 제한하는 방법을 보여 주는 자습서는 [Azure 웹앱에 멤버 자격, OAuth 및 SQL 데이터베이스가 포함된 보안 ASP.NET MVC 앱 배포](/develop/net/tutorials/web-site-with-sql-database/)를 참조하세요.

* 사용자 지정 도메인 이름 및 SSL을 추가하는 방법

	SSL 및 사용자만의 도메인(예: contoso.azurewebsites.net가 아닌 www.contoso.com 사용)을 사용하는 방법에 대한 자세한 내용은 다음 리소스를 참조하세요.

	* [Azure 웹 사이트에 대한 사용자 지정 도메인 이름 구성](web-sites-custom-domain-name.md)
	* [Azure 웹 사이트에 HTTPS 사용](web-sites-configure-ssl-certificate.md)

* 유휴 시간 제한 후 절전 모드 해제 대기 시간을 방지하는 방법

	기본적으로 웹 응용 프로그램은 일정 기간 동안 유휴 상태인 경우 언로드됩니다. 언로드된 후 첫 번째 요청은 웹 응용 프로그램이 다시 로드될 때까지 대기해야 합니다. 대기 시간을 방지하기 위해 AlwaysOn 기능을 사용할 수 있습니다. 자세한 내용은 [웹앱을 구성하는 방법](web-sites-configure.md)의 구성 옵션을 참조하세요.

* 채팅과 같은 실시간 기능을 추가하는 방법

	웹앱에 실시간 기능(예: 채팅 서비스, 게임, 주식 기호 등)을 포함하는 경우 [WebSocket](/blog/2013/11/14/introduction-to-websockets-on-windows-azure-web-sites/) 전송 방식으로 [ASP.NET SignalR](http://www.asp.net/signalr)을 사용하여 최상의 성과를 얻을 수 있습니다. 자세한 내용은 [Azure 웹앱에서 SignalR 사용](http://www.asp.net/signalr/overview/signalr-20/getting-started-with-signalr-20/using-signalr-with-windows-azure-web-sites)을 참조하세요.

* 웹 응용 프로그램용 앱 서비스, 클라우드 서비스 및 VM을 선택하는 방법

	Azure에서는 앱 서비스 웹 응용 프로그램을 웹 사이트(이 자습서에 설명됨), 클라우드 서비스 또는 가상 컴퓨터에서 실행할 수 있습니다. 자세한 내용은 [Azure 실행 모델](/develop/net/fundamentals/compute/) 및 [Azure 웹앱, 클라우드 서비스 및 VM을 사용하는 경우](/manage/services/web-sites/choose-web-app-service/)를 참조하세요.

* [앱 서비스 계획을 선택하거나 만드는 방법](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)

* [리소스 그룹을 선택하거나 만드는 방법](../azure-preview-portal-using-resource-groups.md)



## 변경된 내용
* 웹 사이트에서 앱 서비스로의 변경에 대한 지침은 [Azure 앱 서비스와 이 서비스가 기존 Azure 서비스에 미치는 영향](http://go.microsoft.com/fwlink/?LinkId=529714)을 참조하세요.
* 이전 포털에서 새 포털로의 변경에 대한 지침은 [미리 보기 포털 탐색에 대한 참조](http://go.microsoft.com/fwlink/?LinkId=529715)를 참조하세요.
 

<!---HONumber=62-->