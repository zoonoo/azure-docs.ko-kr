<properties 
	pageTitle="Azure 앱 서비스에 ASP.NET MVC 5 모바일 웹 앱 배포" 
	description="ASP.NET MVC 5 웹 응용 프로그램에서 모바일 기능을 사용하여 Azure 앱 서비스에 웹 앱을 배포하는 방법에 대해 설명하는 자습서입니다." 
	services="app-service" 
	documentationCenter=".net" 
	authors="cephalin" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="09/16/2015" 
	ms.author="cephalin;riande"/>


# Azure 앱 서비스에 ASP.NET MVC 5 모바일 웹 앱 배포

이 자습서에서는 모바일 친화적인 ASP.NET MVC 5 웹 앱을 만들고 Azure 앱 서비스에 배포하는 방법에 대한 기초적인 내용을 알려 줍니다. 이 자습서를 사용하려면 [Visual Studio Express 2013 for Web][Visual Studio Express 2013] 또는 이미 보유하고 있는 Visual Studio의 Professional 버전이 필요합니다. [Visual Studio 2015]를 사용할 수 있지만 스크린샷이 달라지게 되고 ASP.NET 4.x 템플릿을 사용해야 합니다.

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

## 만들 내용

이 자습서에서는 [시작 프로젝트][StarterProject]에 제공된 간단한 회의 목록 응용 프로그램에 모바일 기능을 추가합니다. 다음 스크린샷은 완료된 응용 프로그램에서의 ASP.NET 세션을 보여 줍니다. Internet Explorer 11 F12 개발자 도구에 있는 브라우저 에뮬레이터에서 이러한 세션을 볼 수 있습니다.

![][FixedSessionsByTag]

Internet Explorer 11 F12 개발자 도구 및 [Fiddler 도구][Fiddler]를 사용하여 응용 프로그램을 디버그할 수 있습니다.

## 학습할 기술

다음 내용을 학습하게 됩니다.

-	Visual Studio 2013을 사용하여 웹 응용 프로그램을 Azure 앱 서비스의 웹 앱에 직접 게시하는 방법
-   모바일 장치의 디스플레이를 향상하기 위해 ASP.NET MVC 5 템플릿에서 CSS 부트스트랩 프레임워크를 사용하는 방법
-   iPhone 및 Android 등 특정 모바일 브라우저를 대상으로 모바일 전용 뷰를 만드는 방법
-   반응형 뷰(장치 간의 서로 다른 브라우저에 반응하는 뷰)를 만드는 방법

## 개발 환경 설정

Azure SDK for .NET 2.5.1 이상을 설치하여 개발 환경을 설정합니다.

1. Azure SDK for .NET을 설치하려면 아래 링크를 클릭합니다. Visual Studio 2013가 아직 설치되어 있지 않은 경우 링크를 통해 설치됩니다. 이 자습서를 완료하려면 Visual Studio 2013이 필요합니다. [Azure SDK for Visual Studio 2013][AzureSDKVs2013]
1. 웹 플랫폼 설치 관리자 창에서 **설치**를 클릭하여 설치를 계속합니다.

모바일 브라우저 에뮬레이터도 필요합니다. 다음을 사용할 수 있습니다.

-   [Internet Explorer 11 F12 개발자 도구][EmulatorIE11]의 브라우저 에뮬레이터(모바일 브라우저 스크린샷에 사용됨) Windows Phone 8, Windows Phone 7 및 Apple iPad를 위한 사용자 에이전트 문자열 기본 설정이 있습니다.
-	[Google Chrome DevTools][EmulatorChrome]의 브라우저 에뮬레이터. Apple iPhone, Apple iPad, Amazon Kindle Fire 등 여러 Android 장치에 대한 기본 설정이 포함되어 있습니다. 터치 이벤트도 에뮬레이트합니다.
-   [Opera Mobile Emulator][EmulatorOpera]

C# 소스 코드를 사용하는 Visual Studio 프로젝트를 다음 항목과 함께 사용할 수 있습니다.

-   [시작 프로젝트 다운로드(영문)][StarterProject]
-   [완성된 프로젝트 다운로드(영문)][CompletedProject]

##<a name="bkmk_DeployStarterProject"></a>Azure 웹 앱에 시작 프로젝트 배포

1.	회의 목록 응용 프로그램 [시작 프로젝트][StarterProject](영문)를 다운로드합니다.

2. 	그런 다음 Windows 탐색기에서 Mvc5Mobile.zip 파일을 마우스 오른쪽 단추로 클릭하고 *속성*을 선택합니다.

3. 	**Mvc5Mobile.zip 속성** 대화 상자에서 **차단 해제** 단추를 선택합니다. (차단 해제는 웹에서 다운로드한 *.zip* 파일을 사용하려고 할 때 발생하는 보안 경고를 막습니다.)

4.	*Mvc5Mobile.zip* 파일을 마우스 오른쪽 단추로 클릭하고 **모두 압축 풀기**를 선택하여 파일의 압축을 풉니다.

5. 	Visual Studio에서 *Mvc5Mobile.sln* 파일을 엽니다.

6.  솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시**를 클릭합니다.

	![][DeployClickPublish]

7.	웹 게시에서 **Microsoft Azure 웹 앱**을 클릭합니다.

	![][DeployClickWebSites]

8.	**로그인**을 클릭합니다.

	![][DeploySignIn]

9.	표시되는 메시지에 따라 Azure 계정에 로그인합니다.

11. 이제 기존 웹 앱 선택 대화 상자에서 로그인된 것으로 표시됩니다. **새로 만들기**를 클릭합니다.

	![][DeployNewWebsite]

12. **웹 앱 이름** 필드에서 고유한 앱 이름 접두사를 지정합니다. 정규화된 웹 앱 이름은 *&lt;prefix>*.azurewebsites.net입니다. 또한 **앱 서비스 계획**, **리소스 그룹** 및 **지역** 필드를 구성합니다. 그런 다음에 **만들기**를 클릭합니다.

	![][DeploySiteSettings]

13.	웹 게시 대화 상자에 새 웹 앱의 설정이 채워집니다. **게시**를 클릭합니다.

	![][DeployPublishSite]

	Visual Studio에서 시작 프로젝트를 Azure 웹 앱에 게시하는 것이 완료된 후에는 데스크톱 브라우저가 열려서 라이브 웹 앱을 표시합니다.

14.	모바일 브라우저 에뮬레이터를 시작하고 회의 응용 프로그램(*<prefix>*.azurewebsites.net)의 URL을 에뮬레이터에 복사한 다음 오른쪽 위에 있는 단추를 클릭하고 **태그로 찾아보기**를 선택합니다. Internet Explorer 11을 기본 브라우저로 사용 중인 경우에는 `F12` 및 `Ctrl+8`을 입력한 후에 브라우저 프로필을 **Windows Phone**으로 변경하면 됩니다. 아래 이미지는 *AllTags* 뷰를 세로 모드로 보여 줍니다(**태그로 찾아보기** 선택 시).

	![][AllTags]

>[AZURE.NOTE]Visual Studio 내에서 MVC 5 응용 프로그램을 디버깅할 수도 있고, 웹 앱을 Azure에 다시 게시하여 모바일 브라우저나 브라우저 에뮬레이터에서 라이브 웹 앱을 직접 확인할 수도 있습니다.

이 화면은 모바일 장치에서 가독성이 뛰어납니다. 이미 부트스트랩 CSS 프레임워크에 의해 적용된 시각 효과 중 일부를 알고 있을 수도 있습니다. **ASP.NET** 링크를 클릭합니다.

![][SessionsByTagASP.NET]

ASP.NET 태그 뷰는 화면에 맞게 크기가 조정되어 있습니다. 크기 조정은 Bootstrap에서 자동으로 수행합니다. 하지만 모바일 브라우저에 더 알맞게 이 뷰를 향상할 수 있습니다. 예를 들어 **Date** 열은 읽기 어렵습니다. 이 자습서 뒷부분에서 *AllTags* 뷰를 모바일 친화적으로 변경해 볼 것입니다.

##<a name="bkmk_bootstrap"></a> 부트스트랩 CSS 프레임워크

MVC 5 템플릿의 새로운 점은 기본 제공 Bootstrap 지원입니다. 이것이 응용 프로그램에서 여러 가지 뷰를 즉시 향상하는 것을 이미 확인한 바 있습니다. 예를 들어 맨 위에 있는 탐색 표시줄은 브라우저 너비가 좁아질 때 자동으로 축소될 수 있습니다. 데스크톱 브라우저에서 브라우저 창의 크기를 조정하여 탐색 표시줄의 모양과 느낌이 어떻게 변하는지 확인해 보세요. 이것이 Bootstrap에 기본 제공되는 반응형 웹 디자인입니다.

Bootstrap을 사용하지 않을 때 웹 앱이 어떻게 보이는지를 확인하려면 *App\_Start\\BundleConfig.cs*를 열고 *bootstrap.js* 및 *bootstrap.css*가 들어 있는 줄을 주석으로 처리하세요. 다음 코드는 변경한 후 `RegisterBundles` 메서드의 마지막 두 문을 보여 줍니다

     bundles.Add(new ScriptBundle("~/bundles/bootstrap").Include(
              //"~/Scripts/bootstrap.js",
              "~/Scripts/respond.js"));

    bundles.Add(new StyleBundle("~/Content/css").Include(
              //"~/Content/bootstrap.css",
              "~/Content/site.css"));

`Ctrl+F5`를 눌러 응용 프로그램을 실행합니다.

축소 가능한 탐색 표시줄이 이제 일반적인 순서 없는 목록일 뿐인 것을 볼 수 있습니다. **태그로 찾아보기**를 다시 클릭하고 **ASP.NET**을 클릭합니다. 모바일 에뮬레이터 뷰에서 이제 더 이상 화면에 맞게 크기가 조정되지 않는 것을 볼 수 있으며, 표의 오른쪽을 보려면 옆쪽으로 스크롤해야 합니다.

![][SessionsByTagASP.NETNoBootstrap]

변경 내용을 실행 취소하고 모바일 브라우저를 새로 고쳐, 모바일 친화적인 디스플레이가 복원되었는지 확인하세요.

Bootstrap은 ASP.NET MVC 5 전용이 아니므로 어떤 웹 응용 프로그램에서나 이 기능을 활용할 수 있습니다. 하지만 이제는 ASP.NET MVC 5 프로젝트 템플릿에 기본 제공되므로 MVC 5 웹 응용 프로그램은 Bootstrap을 기본적으로 활용할 수 있습니다.

부트스트랩에 대한 자세한 내용을 보려면 [부트스트랩][BootstrapSite] 사이트로 이동하세요.

다음 섹션에서는 모바일 브라우저 전용 뷰를 제공하는 방법을 설명합니다.

##<a name="bkmk_overrideviews"></a> 뷰, 레이아웃 및 부분 뷰 재정의

일반적인 모바일 브라우저, 개별 모바일 브라우저 또는 특정 브라우저용 뷰(레이아웃 및 부분 뷰 포함)를 재정의할 수 있습니다. 모바일 전용 뷰를 제공하기 위해 뷰 파일을 복사하여 파일 이름에 *.Mobile*을 추가할 수 있습니다. 예를 들어, 모바일 *인덱스* 뷰를 만들려면 *Views\\Home\\Index.cshtml*을 *Views\\Home\\Index.Mobile.cshtml*로 복사하면 됩니다.

이 섹션에서는 모바일 전용 레이아웃 파일을 만듭니다.

시작하려면 *Views\\Shared\\_Layout.cshtml*을 *Views\\Shared\\_Layout.Mobile.cshtml*로 복사합니다. *\_Layout.Mobile.cshtml*을 열고 제목을 **MVC5 Application**에서 **MVC5 Application (Mobile)**로 변경합니다.

탐색 모음에 대한 각 `Html.ActionLink` 호출에서 각 링크 *ActionLink*에서 "Browse by"를 제거합니다. 다음 코드는 모바일 레이아웃 파일의 완성된 `<ul class="nav navbar-nav">` 태그를 보여 줍니다.

    <ul class="nav navbar-nav">
        <li>@Html.ActionLink("Home", "Index", "Home")</li>
        <li>@Html.ActionLink("Date", "AllDates", "Home")</li>
        <li>@Html.ActionLink("Speaker", "AllSpeakers", "Home")</li>
        <li>@Html.ActionLink("Tag", "AllTags", "Home")</li>
    </ul>

*Views\\Home\\AllTags.cshtml* 파일을 *Views\\Home\\AllTags.Mobile.cshtml*로 복사합니다. 새 파일을 열고 `<h2>` 요소를 "Tags"에서 "Tags (M)"으로 변경합니다.

    <h2>Tags (M)</h2>

데스크톱 브라우저와 모바일 브라우저 에뮬레이터를 사용하여 태그 페이지로 이동합니다. 모바일 브라우저 에뮬레이터에 두 가지 변경 내용이 표시됩니다(*\_Layout.Mobile.cshtml*에서 변경된 제목 및 *AllTags.Mobile.cshtml*에서 변경된 제목).

![][AllTagsMobile_LayoutMobile]

반대로 데스크톱 화면은 변경되지 않았습니다(*\_Layout.cshtml* 및 *AllTags.cshtml*의 제목 그대로).

![][AllTagsMobile_LayoutMobileDesktop]

##<a name="bkmk_browserviews"></a> 브라우저 전용 뷰 만들기

모바일 전용 및 데스크톱 전용 뷰 외에도 개별 브라우저를 위한 뷰를 만들 수 있습니다. 예를 들어 iPhone 또는 Android 브라우저 전용의 뷰를 만들 수 있습니다. 이 섹션에서는 iPhone 브라우저를 위한 레이아웃과 iPhone 버전의 *AllTags* 뷰를 만들어 봅니다.

*Global.asax* 파일을 열고 `Application_Start` 메서드의 맨 아래에 다음 코드를 추가합니다.

    DisplayModeProvider.Instance.Modes.Insert(0, new DefaultDisplayMode("iPhone")
    {
        ContextCondition = (context => context.GetOverriddenUserAgent().IndexOf
            ("iPhone", StringComparison.OrdinalIgnoreCase) >= 0)
    });

이 코드는 각 수신 요청에 맞출 "iPhone"이라는 새로운 디스플레이 모드를 정의합니다. 수신 요청이 정의된 조건과 일치하는 경우(즉, 사용자 에이전트가 "iPhone" 문자열을 포함하는 경우) ASP.NET MVC는 이름에 "iPhone" 접미사가 들어 있는 뷰를 찾습니다.

>[AZURE.NOTE]iPhone 및 Android와 같은 모바일 브라우저 전용 디스플레이 모드를 추가할 때는 브라우저 전용 모드가 모바일 템플릿(*.Mobile.cshtml)보다 우선적으로 적용되도록 첫 번째 인수를 `0`으로 설정해야 합니다(목록 맨 위에 삽입). 반대로 모바일 템플릿이 목록의 맨 위에 있으면 의도한 디스플레이 모드가 아니라 해당 모바일 템플릿이 선택됩니다(첫 번째 일치 항목이 적용되는데 모바일 템플릿은 모든 모바일 브라우저와 일치함).

코드에서 `DefaultDisplayMode`를 마우스 오른쪽 단추로 클릭하고 **확인**을 선택한 다음 `using System.Web.WebPages;`를 선택합니다. 그러면 `System.Web.WebPages` 네임스페이스에 참조가 추가되고, 여기에서 `DisplayModeProvider` 및 `DefaultDisplayMode` 유형이 정의됩니다.

![][ResolveDefaultDisplayMode]

또는 파일의 `using` 섹션에 다음 줄을 직접 추가할 수 있습니다.

    using System.Web.WebPages;

변경 내용을 저장합니다. *Views\\Shared\\_Layout.Mobile.cshtml* 파일을 *Views\\Shared\\_Layout.iPhone.cshtml*에 복사합니다. 새 파일을 연 후에 제목을 `MVC5 Application (Mobile)`에서 `MVC5 Application (iPhone)`으로 변경합니다.

*Views\\Home\\AllTags.Mobile.cshtml* 파일을 *Views\\Home\\AllTags.iPhone.cshtml*에 복사합니다. 새 파일에서 `<h2>` 요소를 "Tags (M)"에서 "Tags (iPhone)"로 변경합니다.

응용 프로그램을 실행합니다. 모바일 브라우저 에뮬레이터를 실행하고, 사용자 에이전트가 "iPhone"으로 설정되었는지 확인하고, *AllTags* 뷰로 이동합니다. Internet Explorer 11 F12 개발자 도구에서 에뮬레이터를 사용 중인 경우 에뮬레이션을 다음과 같이 구성합니다.

-   브라우저 프로필 = **Windows Phone**
-   사용자 에이전트 문자열 = **Custom**
-   사용자 지정 문자열 = **Apple-iPhone5C1/1001.525**

다음 스크린샷은 Internet Explorer 11 F12 개발자 도구에서 에뮬레이터에 렌더링된 *AllTags* 뷰와 사용자 지정 사용자 에이전트 문자열(iPhone 5C 사용자 에이전트 문자열임)을 보여 줍니다.

![][AllTagsIPhone_LayoutIPhone]

모바일 브라우저에서 **Speakers** 링크를 선택합니다. 모바일 뷰(*AllSpeakers.Mobile.cshtml*)가 없으므로 기본 발표자 뷰(*AllSpeakers.cshtml*)는 모바일 레이아웃 뷰(*\_Layout.Mobile.cshtml*)를 사용하여 렌더링됩니다. 아래와 같이 **MVC5 Application (Mobile)**이라는 제목이 *\_Layout.Mobile.cshtml*에서 정의됩니다.

![][AllSpeakers_LayoutMobile]

다음과 같이 *Views\\_ViewStart.cshtml* 파일에서 `RequireConsistentDisplayMode`를 `true`로 설정하여 모바일 레이아웃 내에서 기본(모바일 아님) 뷰가 렌더링되지 않도록 전체적으로 비활성화할 수 있습니다.

    @{
        Layout = "~/Views/Shared/_Layout.cshtml";
        DisplayModeProvider.Instance.RequireConsistentDisplayMode = true;
    }

`RequireConsistentDisplayMode`이(가) `true`(으)로 설정되면 모바일 레이아웃(*\_Layout.Mobile.cshtml*)이 모바일 뷰에 대해서만 사용됩니다(예: 뷰 파일이 ****ViewName**.Mobile.cshtml* 형식일 때). 모바일 뷰가 아닌 뷰에서 모바일 레이아웃이 제대로 작동하지 않을 때 `RequireConsistentDisplayMode`를 `true`로 설정할 수도 있습니다. 아래의 스크린샷은 `RequireConsistentDisplayMode`가 `true`로 설정되어 있을 때 *발표자* 페이지가 어떻게 렌더링되는 지를 보여줍니다(맨 위의 탐색 표시줄에 "(Mobile)" 문자열이 없음).

![][AllSpeakers_LayoutMobileOverridden]

뷰 파일에서 `RequireConsistentDisplayMode`를 `false`로 설정하여 특정 뷰의 일관된 디스플레이 모드를 비활성화할 수 있습니다. *Views\\Home\\AllSpeakers.cshtml* 파일의 다음 태그는 `RequireConsistentDisplayMode`를 `false`로 설정합니다.

    @model IEnumerable<string>

    @{
        ViewBag.Title = "All speakers";
        DisplayModeProvider.Instance.RequireConsistentDisplayMode = false;
    }

이 섹션에서는 모바일 레이아웃 및 뷰를 만드는 방법과 iPhone과 같은 특정 장치용의 레이아웃 및 뷰를 만드는 방법을 살펴보았습니다. 그러나 부트스트랩 CSS 프레임워크의 주요 이점은 반응형 레이아웃입니다. 이 레이아웃을 통해 데스크톱, 전화 및 태블릿 브라우저에 걸쳐 단일 스타일 시트를 적용하여 일관된 모양과 느낌을 줄 수 있습니다. 다음 섹션에서는 Bootstrap을 활용하여 모바일 친화적인 뷰를 만드는 방법을 설명합니다.

##<a name="bkmk_Improvespeakerslist"></a> 발표자 목록 개선

방금 본 것처럼 *발표자* 뷰는 가독성은 있지만 링크가 작고 모바일 장치에서 누르기 어렵습니다. 이 섹션에서는 *AllSpeakers* 뷰를 모바일 친화적으로 만듭니다. 크고 누르기 쉬운 링크를 표시하고 발표자를 빨리 찾을 수 있는 검색 상자가 포함됩니다.

부트스트랩 [연결된 목록 그룹][] 스타일을 사용하여 *발표자* 뷰를 개선할 수 있습니다. *Views\\Home\\AllSpeakers.cshtml*에서 Razor 파일의 내용을 아래의 코드로 바꿉니다.

     @model IEnumerable<string>

    @{
        ViewBag.Title = "All Speakers";
    }

    <h2>Speakers</h2>

    <div class="list-group">
        @foreach (var speaker in Model)
        {
            @Html.ActionLink(speaker, "SessionsBySpeaker", new { speaker }, new { @class = "list-group-item" })
        }
    </div>

`<div>` 태그의 `class="list-group"` 속성은 Bootstrap 목록 항목 스타일을 적용하고 `class="input-group-item"` 속성은 각 링크에 Bootstrap 목록 스타일을 적용합니다.

모바일 브라우저를 새로 고칩니다. 업데이트된 뷰는 다음과 같이 표시됩니다.

![][AllSpeakersFixed]

부트스트랩 [연결된 목록 그룹][] 스타일은 각 링크의 전체 상자를 클릭 가능한 상태로 만들어서 사용자 환경을 더욱 개선합니다. 데스크톱 뷰로 전환해도 일관된 모양과 느낌이 유지됩니다.

![][AllSpeakersFixedDesktop]

모바일 브라우저 뷰가 개선되기는 했지만 긴 발표자 목록을 탐색하기가 힘듭니다. 부트스트랩은 바로 사용 가능한 검색 필터 기능을 제공하지 않지만, 코드 몇 줄을 사용하여 추가할 수 있습니다. 먼저 검색 상자를 뷰에 추가한 후에 JavaScript 코드를 연결하여 필터 기능을 만듭니다. *Views\\Home\\AllSpeakers.cshtml*에서 다음과 같이 <form> 태그를 <h2> 태그 바로 뒤에 추가합니다.

    @model IEnumerable<string>

    @{
        ViewBag.Title = "All Speakers";
    }

    <h2>Speakers</h2>

    <form class="input-group">
        <span class="input-group-addon"><span class="glyphicon glyphicon-search"></span></span>
        <input type="text" class="form-control" placeholder="Search speaker">
    </form>
    <br />
    <div class="list-group">
        @foreach (var speaker in Model)
        {
            @Html.ActionLink(speaker, 
                             "SessionsBySpeaker", 
                             new { speaker }, 
                             new { @class = "list-group-item" })
        }
    </div>

`<form>` 및 `<input>` 태그 모두에 Bootstrap 스타일이 적용되어 있습니다. `<span>` 요소는 Bootstrap [glyphicon][]을 검색 상자에 추가합니다.

*Scripts* 폴더에서 *filter.js*라는 JavaScript 파일을 추가합니다. 파일을 열고 다음 코드를 붙여 넣습니다.

    $(function () {

        // reset the search form when the page loads
        $("form").each(function () {
            this.reset();
        });

        // wire up the events to the <input> element for search/filter
        $("input").bind("keyup change", function () {
            var searchtxt = this.value.toLowerCase();
            var items = $(".list-group-item");

            // show all speakers that begin with the typed text and hide others
            for (var i = 0; i < items.length; i++) {
                var val = items[i].text.toLowerCase();
                val = val.substring(0, searchtxt.length);
                if (val == searchtxt) {
                    $(items[i]).show();
                }
                else {
                    $(items[i]).hide();
                }
            }
        });
    });

또한 등록된 번들에 filter.js를 포함해야 합니다. *App\_Start\\BundleConfig.cs*를 열고 첫 번째 번들을 변경합니다. 첫 번째 `bundles.Add` 문(**jquery** 번들에 해당)을 변경하여 다음과 같이 *Scripts\\filter.js*를 포함합니다.

     bundles.Add(new ScriptBundle("~/bundles/jquery").Include(
                "~/Scripts/jquery-{version}.js",
                "~/Scripts/filter.js"));

**jquery** 번들은 이미 기본 *\_Layout* 뷰에 렌더링되어 있습니다. 나중에 같은 JavaScript 코드를 활용하여 필터 기능을 다른 목록 뷰에 적용할 수 있습니다.

모바일 브라우저를 새로 고치고 *AllSpeakers* 뷰로 이동합니다. 검색 상자에 "sc"를 입력합니다. 이제 검색 문자열에 따라 발표자 목록을 필터링할 수 있습니다.

![][AllSpeakersFixedSearchBySC]

##<a name="bkmk_improvetags"></a> 태그 목록 개선

*발표자* 뷰처럼 *태그* 뷰도 가독성은 있지만 링크가 작고 모바일 장치에서 누르기 어렵습니다. *발표자* 뷰를 수정한 것과 같은 방법으로 *태그* 뷰를 수정할 수 있습니다. 앞에서 설명한 대로 코드를 변경하되 *Views\\Home\\AllTags.cshtml*에서 다음 `Html.ActionLink` 메서드 구문을 사용하면 됩니다.

    @Html.ActionLink(tag, 
                     "SessionsByTag", 
                     new { tag }, 
                     new { @class = "list-group-item" })

새로 고친 데스크톱 브라우저는 다음과 같이 표시됩니다.

![][AllTagsFixedDesktop]

그리고 새로 고친 모바일 브라우저는 다음과 같이 표시됩니다.

![][AllTagsFixed]

>[AZURE.NOTE]모바일 브라우저에서 원래의 목록 형식이 그대로 있고, 보기 좋았던 Bootstrap 스타일이 사라진 것이 눈에 띌 것입니다. 이것은 앞서 모바일 전용 뷰를 만들었기 때문입니다. 하지만 이제는 부트스트랩 CSS 프레임워크를 사용하여 반응형 웹 디자인을 만드는 것이기 때문에 모바일 전용 뷰와 모바일 전용 레이아웃 뷰를 제거하세요. 그러면 새로 고친 모바일 브라우저에 Bootstrap 스타일이 표시될 것입니다.

##<a name="bkmk_improvedates"></a> 날짜 목록 개선

*스피커* 및 *태그*에서 개선한 것과 같이 *날짜* 뷰를 개선할 수 있습니다. 앞에서 설명한 대로 코드를 변경하되 *Views\\Home\\AllDates.cshtml*에서 다음 `Html.ActionLink` 메서드 구문을 사용하면 됩니다.

    @Html.ActionLink(date.ToString("ddd, MMM dd, h:mm tt"), 
                     "SessionsByDate", 
                     new { date }, 
                     new { @class = "list-group-item" })

새로 고친 모바일 브라우저 뷰는 다음과 같이 표시됩니다.

![][AllDatesFixed]

날짜-시간 값을 날짜별로 정리함으로써 *날짜* 뷰를 더욱 개선할 수 있습니다. Bootstrap [패널][] 스타일을 사용하면 됩니다. *Views\\Home\\AllDates.cshtml* 파일의 내용을 다음 코드를 사용하여 바꿉니다.

    @model IEnumerable<DateTime>

    @{
        ViewBag.Title = "All Dates";
    }

    <h2>Dates</h2>

    @foreach (var dategroup in Model.GroupBy(x=>x.Date))
    {
        <div class="panel panel-primary">
            <div class="panel-heading">
                @dategroup.Key.ToString("ddd, MMM dd")
            </div>
            <div class="panel-body list-group">
                @foreach (var date in dategroup)
                {
                    @Html.ActionLink(date.ToString("h:mm tt"), 
                                     "SessionsByDate", 
                                     new { date }, 
                                     new { @class = "list-group-item" })
                }
            </div>
        </div>
    }

이 코드는 목록에 있는 각 개별 날짜에 대해 별도의 `<div class="panel panel-primary">` 태그를 만들고 앞서와 마찬가지로 해당 링크에 [연결된 목록 그룹][]을 사용합니다. 이 코드를 실행하면 모바일 브라우저가 다음과 같이 표시됩니다.

![][AllDatesFixed2]

데스크톱 브라우저로 전환합니다. 이번에도 일관된 모양을 확인할 수 있습니다.

![][AllDatesFixed2Desktop]

##<a name="bkmk_improvesessionstable"></a> SessionsTable 뷰 개선

이 섹션에서는 *SessionsTable* 뷰를 보다 모바일 친화적으로 만듭니다. 앞서의 경우보다 변화의 폭이 더 큽니다.

모바일 브라우저에서 **태그** 단추를 누르고 검색 상자에 `asp`를 입력합니다.

![][AllTagsFixedSearchByASP]

**ASP.NET** 링크를 누릅니다.

![][SessionsTableTagASP.NET]

보시다시피 표 형태로 표시됩니다. 현재는 데스크톱 브라우저에서 보도록 디자인된 것입니다. 하지만 모바일 브라우저에서는 읽기가 약간 어렵습니다. 이를 수정하기 위해 *Views\\Home\\SessionsTable.cshtml*을 열고 파일의 내용을 다음 코드로 바꿉니다.

    @model IEnumerable<Mvc5Mobile.Models.Session>

    <h2>@ViewBag.Title</h2>

    <div class="container">
        <div class="row">
            @foreach (var session in Model)
            {
                <div class="col-md-4">
                    <div class="list-group">
                        @Html.ActionLink(session.Title, 
                                         "SessionByCode", 
                                         new { session.Code }, 
                                         new { @class="list-group-item active" })
                        <div class="list-group-item">
                            <div class="list-group-item-text">
                                @Html.Partial("_SpeakersLinks", session)
                            </div>
                            <div class="list-group-item-info">
                                @session.DateText
                            </div>
                            <div class="list-group-item-info small hidden-xs">
                                @Html.Partial("_TagsLinks", session)
                            </div>
                        </div>
                    </div>
                </div>
            }
        </div>
    </div>

이 코드는 세 가지 작업을 수행합니다.

-   부트스트랩 [사용자 지정 연결된 목록 그룹][]을 사용하여 세션 정보를 세로 형태로 정리하여 이 모든 정보를 모바일 브라우저에서 읽을 수 있게 만듭니다(list-group-item-text 등의 클래스 사용).
-   [그리드 시스템][]을 레이아웃에 적용하여 세션 항목이 데스크톱 브라우저에서 가로 방향으로 흐르고 모바일 브라우저에서는 세로 방향으로 흐르도록 합니다(col-md-4 클래스 사용).
-   모바일 브라우저에서 볼 때 [응답성이 뛰어난 유틸리티][]를 사용하여 세션 태그를 숨깁니다(hidden-xs 클래스 사용).

제목 링크를 눌러 해당 세션으로 이동할 수도 있습니다. 아래 이미지는 코드 변경 내용이 반영된 화면입니다.

![][FixedSessionsByTag]

자동으로 적용한 Bootstrap 그리드 시스템은 모바일 브라우저에서 세션을 세로로 정렬합니다. 또한 태그가 표시되지 않습니다. 데스크톱 브라우저로 전환합니다.

![][SessionsTableFixedTagASP.NETDesktop]

데스크톱 브라우저에서는 태그가 표시됩니다. 또한 적용한 부트스트랩 그리드 시스템이 세션 항목을 두 개의 열로 정렬합니다. 브라우저를 확대하면 세 개의 열로 정렬되는 것을 볼 수 있습니다.

##<a name="bkmk_improvesessionbycode"></a> SessionByCode 뷰 개선

마지막으로, *SessionByCode* 뷰를 모바일 친화적으로 수정해 봅니다.

모바일 브라우저에서 **태그** 단추를 누르고 검색 상자에 `asp`를 입력합니다.

![][AllTagsFixedSearchByASP]

**ASP.NET** 링크를 누릅니다. ASP.NET 태그의 세션이 표시됩니다.

![][FixedSessionsByTag]

**ASP.NET 및 AngularJS로 단일 페이지 응용 프로그램 만들기** 링크를 선택합니다.

![][SessionByCode3-644]

기본 데스크톱 뷰도 좋지만 몇 가지 Bootstrap GUI 구성 요소를 사용하여 이 뷰를 쉽게 개선할 수 있습니다.

*Views\\Home\\SessionByCode.cshtml*을 열고 내용을 다음 태그로 바꿉니다.

    @model Mvc5Mobile.Models.Session

    @{
        ViewBag.Title = "Session details";
    }
    <h3>@Model.Title (@Model.Code)</h3>
    <p>
        <strong>@Model.DateText</strong> in <strong>@Model.Room</strong>
    </p>

    <div class="panel panel-primary">
        <div class="panel-heading">
            Speakers
        </div>
        @foreach (var speaker in Model.Speakers)
        {
            @Html.ActionLink(speaker, 
                             "SessionsBySpeaker", 
                             new { speaker }, 
                             new { @class="panel-body" })
        }
    </div>

    <p>@Model.Abstract</p>

    <div class="panel panel-primary">
        <div class="panel-heading">
            Tags
        </div>
        @foreach (var tag in Model.Tags)
        {
            @Html.ActionLink(tag, 
                             "SessionsByTag", 
                             new { tag }, 
                             new { @class = "panel-body" })
        }
    </div>

새 태그는 Bootstrap 패널 스타일을 사용하여 모바일 뷰를 개선합니다.

모바일 브라우저를 새로 고칩니다. 다음 이미지는 방금 변경한 코드가 반영된 화면입니다.

![][SessionByCodeFixed3-644]

## 요약 및 검토

이 자습서에서는 ASP.NET MVC 5를 사용하여 모바일 친화적인 웹 응용 프로그램을 개발하는 방법을 보여 주었습니다. 내용은 다음과 같습니다.

-	앱 서비스 웹 앱에 ASP.NET MVC 5 응용 프로그램 배포
-   MVC 5 응용 프로그램에서 Bootstrap을 사용하여 반응형 웹 레이아웃 만들기
-   모든 뷰에 대해 그리고 개별 뷰에 대해 레이아웃, 뷰 및 부분 뷰 재정의
-   `RequireConsistentDisplayMode` 속성을 사용하여 레이아웃 및 부분 재정의 작업 제어
-   iPhone 브라우저 등 특정 브라우저를 대상으로 하는 뷰 만들기
-   Razor 코드에서 Boostrap 스타일 적용

## 참고 항목

-   [반응형 웹 디자인의 9가지 기본 원칙](http://blog.froont.com/9-basic-principles-of-responsive-web-design/)
-   [Bootstrap][BootstrapSite]
-   [공식 부트스트랩 블로그][]
-   [Tutorial Republic의 Twitter Bootstrap 자습서][]
-   [Bootstrap 놀이터][]
-   [W3C에서 권장하는 모바일 웹 응용 프로그램 모범 사례][]
-   [미디어 쿼리에 대한 W3C 권장 사항][]

## 변경된 내용
* 웹 사이트에서 앱 서비스로의 변경에 대한 지침은 [Azure 앱 서비스와 이 서비스가 기존 Azure 서비스에 미치는 영향](http://go.microsoft.com/fwlink/?LinkId=529714)을 참조하세요.
* 이전 포털에서 새 포털로의 변경에 대한 지침은 [미리 보기 포털 탐색에 대한 참조](http://go.microsoft.com/fwlink/?LinkId=529715)를 참조하세요.

<!-- Internal Links -->
[Deploy the starter project to an Azure web app]: #bkmk_DeployStarterProject
[Bootstrap CSS Framework]: #bkmk_bootstrap
[Override the Views, Layouts, and Partial Views]: #bkmk_overrideviews
[Create Browser-Specific Views]: #bkmk_browserviews
[Improve the Speakers List]: #bkmk_Improvespeakerslist
[Improve the Tags List]: #bkmk_improvetags
[Improve the Dates List]: #bkmk_improvedates
[Improve the SessionsTable View]: #bkmk_improvesessionstable
[Improve the SessionByCode View]: #bkmk_improvesessionbycode

<!-- External Links -->
[Visual Studio Express 2013]: http://www.visualstudio.com/downloads/download-visual-studio-vs#d-express-web
[Visual Studio 2015]: https://www.visualstudio.com/downloads/download-visual-studio-vs
[AzureSDKVs2013]: http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409
[Fiddler]: http://www.fiddler2.com/fiddler2/
[EmulatorIE11]: http://msdn.microsoft.com/library/ie/dn255001.aspx
[EmulatorChrome]: https://developers.google.com/chrome-developer-tools/docs/mobile-emulation
[EmulatorOpera]: http://www.opera.com/developer/tools/mobile/
[StarterProject]: http://go.microsoft.com/fwlink/?LinkID=398780&clcid=0x409
[CompletedProject]: http://go.microsoft.com/fwlink/?LinkID=398781&clcid=0x409
[BootstrapSite]: http://getbootstrap.com/
[WebPIAzureSdk23NetVS13]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/WebPIAzureSdk23NetVS13.png
[연결된 목록 그룹]: http://getbootstrap.com/components/#list-group-linked
[glyphicon]: http://getbootstrap.com/components/#glyphicons
[패널]: http://getbootstrap.com/components/#panels
[사용자 지정 연결된 목록 그룹]: http://getbootstrap.com/components/#list-group-custom-content
[그리드 시스템]: http://getbootstrap.com/css/#grid
[응답성이 뛰어난 유틸리티]: http://getbootstrap.com/css/#responsive-utilities
[공식 부트스트랩 블로그]: http://blog.getbootstrap.com/
[Tutorial Republic의 Twitter Bootstrap 자습서]: http://www.tutorialrepublic.com/twitter-bootstrap-tutorial/
[Bootstrap 놀이터]: http://www.bootply.com/
[W3C에서 권장하는 모바일 웹 응용 프로그램 모범 사례]: http://www.w3.org/TR/mwabp/
[미디어 쿼리에 대한 W3C 권장 사항]: http://www.w3.org/TR/css3-mediaqueries/

<!-- Images -->
[DeployClickPublish]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-1.png
[DeployClickWebSites]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-2.png
[DeploySignIn]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-3.png
[DeployUsername]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-4.png
[DeployPassword]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-5.png
[DeployNewWebsite]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-6.png
[DeploySiteSettings]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-7.png
[DeployPublishSite]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-8.png
[MobileHomePage]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/mobile-home-page.png
[FixedSessionsByTag]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionsByTag-ASP.NET-Fixed.png
[AllTags]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTags.png
[SessionsByTagASP.NET]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionsByTag-ASP.NET.png
[SessionsByTagASP.NETNoBootstrap]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionsByTag-ASP.NET-NoBootstrap.png
[AllTagsMobile_LayoutMobile]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTagsMobile-_LayoutMobile.png
[AllTagsMobile_LayoutMobileDesktop]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTagsMobile-_LayoutMobile-Desktop.png
[ResolveDefaultDisplayMode]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/Resolve-DefaultDisplayMode.png
[AllTagsIPhone_LayoutIPhone]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTagsIPhone-_LayoutIPhone.png
[AllSpeakers_LayoutMobile]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllSpeakers-_LayoutMobile.png
[AllSpeakers_LayoutMobileOverridden]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllSpeakers-_LayoutMobile-Overridden.png
[AllSpeakersFixed]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllSpeakers-Fixed.png
[AllSpeakersFixedDesktop]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllSpeakers-Fixed-Desktop.png
[AllSpeakersFixedSearchBySC]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllSpeakers-Fixed-SearchBySC.png
[AllTagsFixedDesktop]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTags-Fixed-Desktop.png
[AllTagsFixed]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTags-Fixed.png
[AllDatesFixed]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllDates-Fixed.png
[AllDatesFixed2]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllDates-Fixed2.png
[AllDatesFixed2Desktop]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllDates-Fixed2-Desktop.png
[AllTagsFixedSearchByASP]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTags-Fixed-SearchByASP.png
[SessionsTableTagASP.NET]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionsTable-Tag-ASP.NET.png
[SessionsTableFixedTagASP.NETDesktop]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionsTable-Fixed-Tag-ASP.NET-Desktop.png
[SessionByCode3-644]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionByCode-3-644.png
[SessionByCodeFixed3-644]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionByCode-Fixed-3-644.png
 

<!---HONumber=Oct15_HO3-->