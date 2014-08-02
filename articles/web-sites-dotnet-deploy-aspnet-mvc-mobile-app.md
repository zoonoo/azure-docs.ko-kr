<properties linkid="develop-dotnet-aspnet-mvc-4-mobile-website" urlDisplayName="ASP.NET MVC 4 mobile website" pageTitle=".NET ASP.NET MVC 4 mobile web site - Azure tutorials" metaKeywords="Azure tutorial, Azure web app tutorial, Azure mobile app, Azure ASP.NET MVC 4,,ASP.NET MVC" description="A tutorial that teaches you how to deploy a web application to an Azure web site using mobile features in ASP.NET MVC 4 web application." metaCanonical="" services="web-sites" documentationCenter=".NET" title="Deploy an ASP.NET MVC Mobile Web Application on Azure Web Sites" authors="tdykstra" solutions="" manager="" editor="" />

Azure 웹 사이트에 ASP.NET MVC 모바일 웹 응용 프로그램 배포
==========================================================

***저자 [Rick Anderson](https://twitter.com/RickAndMSFT), 업데이트 날짜: 2013년 6월 26일***

이 자습서에서는 Azure 웹 사이트에 웹 응용 프로그램을 배포하는 기본적인 방법을 설명합니다. 이 자습서의 목적을 위해 ASP.NET MVC 4 웹 응용 프로그램에서 모바일 기능을 사용할 것입니다. 이 자습서의 단계를 수행하기 위해 Microsoft Visual Studio 2012를 사용할 수 있습니다. Microsoft Visual Studio의 무료 버전인 [Visual Studio Express 2012](http://www.microsoft.com/visualstudio/eng/products/visual-studio-express-products)도 사용할 수 있습니다.

다음 내용을 배웁니다.
---------------------

-   모바일 장치의 화면을 향상시키기 위해 ASP.NET MVC 4 템플릿에서 HTML5 뷰포트 특성 및 적응 렌더링을 사용하는 방법
-   모바일 전용 뷰를 만드는 방법
-   응용 프로그램의 모바일 뷰와 데스크톱 뷰를 전환할 수 있는 뷰 전환기를 만드는 방법
-   웹 응용 프로그램을 Azure에 배포하는 방법

이 자습서에서는 시작 프로젝트에 제공된 간단한 회의 목록 응용 프로그램에 모바일 기능을 추가합니다. 다음 스크린샷은 Windows 7 Phone Emulator에 표시된 것처럼, 완성된 응용 프로그램의 기본 페이지를 보여 줍니다.

![MVC4 회의 응용 프로그램 기본 페이지](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/FinishedAPPMainScreen.png)

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

개발 환경 설정
--------------

Azure SDK for the .NET Framework를 설치하여 개발 환경을 설정합니다.

1.  Azure SDK for .NET을 설치하려면 아래 링크를 클릭합니다. Visual Studio 2012가 아직 설치되어 있지 않은 경우 링크를 통해 설치됩니다. 이 자습서는 Visual Studio 2012가 필요합니다. [Azure SDK for Visual Studio 2012](http://go.microsoft.com/fwlink/?LinkId=254364)
2.  실행 가능한 설치 프로그램을 실행할지 또는 저장할지 묻는 메시지가 표시되면 **실행**을 클릭합니다.
3.  웹 플랫폼 설치 관리자 창에서 **설치**를 클릭하여 설치를 계속합니다.

![웹 플랫폼 설치 관리자 - Azure SDK for .NET](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/WebPIAzureSdk20NetVS12.png)

모바일 브라우저 에뮬레이터도 필요합니다. 다음을 사용할 수 있습니다.

-   [Windows 7 Phone Emulator](http://msdn.microsoft.com/en-us/library/ff402530(VS.92).aspx) (이 자습서에서 대부분의 스크린샷에 사용된 에뮬레이터입니다.)
-   iPhone을 에뮬레이트하려면 사용자 에이전트 문자열을 변경하십시오. How-To Geek의 [이 블로그 항목](http://www.howtogeek.com/113439/how-to-change-your-browsers-user-agent-without-installing-any-extensions/)(영문)을 참조하십시오.
-   [Opera Mobile Emulator](http://www.opera.com/developer/tools/mobile/)
-   [Apple Safari](http://www.apple.com/safari/download/)(사용자 에이전트를 iPhone으로 설정). Safari에서 사용자 에이전트를 "iPhone"으로 설정하는 방법에 대한 지침은 David Alison의 블로그에서 [Safari를 IE로 사용하는 방법](http://www.davidalison.com/2008/05/how-to-let-safari-pretend-its-ie.html)(영문)을 참조하십시오.
-   [FireFox User Agent Switcher](https://addons.mozilla.org/en-US/firefox/addon/user-agent-switcher/)와 함께 [FireFox](http://www.bing.com/search?q=firefox+download)

이 자습서는 C\#의 코드를 보여 줍니다. 그러나 시작 프로젝트 및 완성된 프로젝트는 Visual Basic에서 사용할 수 있습니다. Visual Basic 및 C\# 소스 코드를 사용하는 Visual Studio 프로젝트를 이 항목과 함께 사용할 수 있습니다.

-   [시작 프로젝트 다운로드(영문)](http://go.microsoft.com/fwlink/?LinkId=228307)
-   [완성된 프로젝트 다운로드(영문)](http://go.microsoft.com/fwlink/?LinkId=228306)

이 자습서의 단계
----------------

-   [Azure 웹 사이트 만들기](#bkmk_CreateWebSite)
-   [시작 프로젝트 설정](#bkmk_setupstarterproject)
-   [뷰, 레이아웃 및 부분 뷰 재정의](#bkmk_overrideviews)
-   [jQuery Mobile을 사용하여 모바일 브라우저 인터페이스 정의](#bkmk_usejquerymobile)
-   [발표자 목록 개선](#bkmk_Improvespeakerslis)
-   [모바일 발표자 뷰 만들기](#bkmk_mobilespeakersview)
-   [태그 목록 개선](#bkmk_improvetags)
-   [날짜 목록 개선](#bkmk_improvedates)
-   [SessionsTable 뷰 개선](#bkmk_improvesessionstable)
-   [SessionByCode 뷰 개선](#bkmk_improvesessionbycode)
-   [Azure 웹 사이트에 응용 프로그램 배포](#bkmk_deployapplciation)

### Azure에서 웹 사이트 만들기

Azure 웹 사이트는 공유 호스팅 환경에서 실행되므로 다른 Azure 클라이언트와 공유되는 VM(가상 컴퓨터)에서 실행됩니다. 공유 호스팅 환경은 클라우드를 시작하는 저비용 방법입니다. 나중에 웹 트래픽이 증가하면 응용 프로그램이 전용 VM에서 실행되어 요구에 맞게 확장될 수 있습니다. 더 복잡한 아키텍처가 필요한 경우 Azure 클라우드 서비스로 마이그레이션할 수 있습니다. 클라우드 서비스는 요구에 따라 구성할 수 있는 전용 VM에서 실행됩니다.

1.  [Azure 관리 포털](https://manage.windowsazure.com)에 로그온합니다. 관리 포털에서 **새로 만들기**를 클릭합니다.

    ![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/depoly_mobile_new_website_1.png)

2.  **웹 사이트**를 클릭한 후 **빠른 생성**을 클릭합니다.

    ![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/depoly_mobile_new_website_2.png)

3.  **새 웹 사이트 만들기**에서 응용 프로그램의 고유 URL로 사용할 문자열을 **URL** 상자에 입력합니다.

    ![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/depoly_mobile_new_website_3.png)

    전체 URL은 여기에 입력한 문자열과 텍스트 상자 아래에 표시되는 접미사로 구성됩니다. 그림에는 "MyMobileMVC4WebSite"가 표시되지만 누군가 이 URL을 이미 사용하고 있는 경우 다른 URL을 선택해야 합니다. 현재 위치하고 있는 **지역**을 선택합니다.

4.  상자 아래쪽에 있는 확인 표시를 클릭하여 마쳤음을 표시합니다.

관리 포털이 웹 사이트 페이지로 돌아가고 상태 열에 사이트를 만드는 중이라고 표시됩니다. 잠시(일반적으로 1분 미만) 후에 상태 열에 사이트를 만들었다고 표시됩니다. 왼쪽의 탐색 모음에서 계정에 보유한 사이트 수가 웹 사이트 아이콘에 표시되고 데이터베이스 수가 SQL 데이터베이스 아이콘에 표시됩니다.

![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/depoly_mobile_new_website_4.png)

### 시작 프로젝트 설정

1.  [회의 목록 응용 프로그램 시작 프로젝트](http://go.microsoft.com/fwlink/?LinkId=228307)(영문)를 다운로드합니다.

2.  그런 다음 Windows 탐색기에서 MvcMobileStarterBeta.zip 파일을 마우스 오른쪽 단추로 클릭하고 *속성*을 선택합니다.

3.  MvcMobileRTMStarter.zip 속성 대화 상자에서 차단 해제 단추를 선택합니다. (차단 해제는 웹에서 다운로드한 .zip 파일을 사용하려고 할 때 발생하는 보안 경고를 막습니다.)

    ![속성 대화 상자](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/propertiespopup.png)

4.  MvcMobile.zip 파일을 마우스 오른쪽 단추로 클릭하고 압축 풀기를 선택하여 파일의 압축을 풉니다.

5.  Visual Studio에서 MvcMobile.sln 파일을 엽니다.

### 시작 프로젝트 실행

1.  CTRL+F5를 눌러 응용 프로그램을 실행하면 데스크톱 브라우저에 표시됩니다.
2.  모바일 브라우저 에뮬레이터를 시작하고 회의 응용 프로그램의 URL을 에뮬레이터에 복사한 다음 Browse by tag 링크를 클릭합니다.

    -   Windows Phone Emulator를 사용하는 경우 URL 표시줄을 클릭하고 일시 중지 키를 눌러 키보드 액세스 권한을 얻습니다. 아래 이미지는 AllTags 뷰(Browse by tag에서 선택)를 보여 줍니다.

    ![Browse by tag 페이지](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/BrowseByTagWithCallout.png)

이 화면은 모바일 장치에서 가독성이 뛰어납니다. ASP.NET 링크를 선택합니다.

![ASP.NET으로 태그가 지정된 세션 탐색](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/ASPNetPage.png)

ASP.NET 태그 뷰는 매우 어수선합니다. 예를 들어 Date 열은 읽기가 매우 힘듭니다. 이 자습서의 뒷부분에서 모바일 브라우저 전용으로 화면 가독성을 뛰어나게 만드는 AllTags 뷰 버전을 만들 것입니다.

뷰, 레이아웃 및 부분 뷰 재정의
------------------------------

이 섹션에서는 모바일 전용 레이아웃 파일을 만듭니다.

ASP.NET MVC 4의 중요한 새 기능은 일반적인 모바일 브라우저, 개별 모바일 브라우저 또는 특정 브라우저용 뷰(레이아웃 및 부분 뷰 포함)를 재정의할 수 있는 간단한 메커니즘입니다. 모바일 전용 뷰를 제공하기 위해 뷰 파일을 복사하여 파일 이름에 .Mobile을 추가할 수 있습니다. 예를 들어 모바일 인덱스 뷰를 만들려면 *Views\\Home\\Index.cshtml*을 *Views\\Home\\Index.Mobile.cshtml*로 복사합니다.

시작하려면 *Views\\Shared\\\_Layout.cshtml*을 *Views\\Shared\\\_Layout.Mobile.cshtml*로 복사합니다. *\_Layout.Mobile.cshtml*을 열고 제목을 **MVC4 Conference**에서 **Conference (Mobile)**로 변경합니다.

각 **Html.ActionLink** 호출에서 각 링크 ActionLink에서 "Browse by"를 제거합니다. 다음 코드는 모바일 레이아웃 파일의 완성된 본문 섹션을 보여 줍니다.

     <body>
        <div class="page">
            <div id="header">
                <div id="logindisplay"></div>
                <div id="title">
                    <h1> Conference (Mobile)</h1>
                </div>
                <div id="menucontainer">
                    <ul id="menu">
                        <li>@Html.ActionLink("Home", "Index", "Home")</li>
                        <li>@Html.ActionLink("Date", "AllDates", "Home")</li>
                        <li>@Html.ActionLink("Speaker", "AllSpeakers", "Home")</li>
                        <li>@Html.ActionLink("Tag", "AllTags", "Home")</li>
                    </ul>
                </div>
            </div>
            <div id="main">
                @RenderBody()
            </div>
            <div id="footer">
            </div>
        </div>
    </body>

*Views\\Home\\AllTags.cshtml* 파일을 *Views\\Home\\AllTags.Mobile.cshtml*로 복사합니다. 새 파일을 열고 &lt;h2\> 요소를 "Tags"에서 "Tags (M)"으로 변경합니다.

     <h2>Tags (M)</h2>

데스크톱 브라우저와 모바일 브라우저 에뮬레이터를 사용하여 태그 페이지로 이동합니다. 모바일 브라우저 에뮬레이터에 두 가지 변경 내용이 표시됩니다.

![태그 페이지 변경 내용 표시](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_p2m_layouttags_mobile_thumb.png)

반대로 데스크톱 화면은 변경되지 않았습니다.

![데스크톱 태그 뷰 표시](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/Windows-Live-Writer_ASP_NET-MVC-4-Mobile-Features_D2FF_p2_layoutTagsDesktop_thumb.png)

jQuery Mobile을 사용하여 모바일 브라우저 인터페이스 정의
--------------------------------------------------------

이 섹션에서는 jQuery Mobile 및 뷰 전환기 위젯을 설치하는 jQuery.Mobile.MVC NuGet 패키지를 설치합니다.

[jQuery Mobile](http://jquerymobile.com/demos/1.0b3/#/demos/1.0b3/docs/about/intro.html) 라이브러리는 모든 주요 모바일 브라우저에서 작동하는 사용자 인터페이스 프레임워크를 제공합니다. jQuery Mobile은 CSS 및 JavaScript를 지원하는 모바일 브라우저에 혁신적인 향상을 제공합니다. 혁신적인 향상을 통해 모든 브라우저는 웹 페이지의 기본 콘텐츠를 표시할 수 있으며, 더욱 강력한 브라우저 및 장치는 더욱 다채로운 화면을 표시할 수 있습니다. jQuery Mobile에 포함되는 JavaScript 및 CSS 파일은 태그를 변경하지 않고 모바일 브라우저에 맞는 여러 요소를 디자인합니다.

1.  이전에 만든 *Shared\\\_Layout.Mobile.cshtml* 파일을 삭제합니다.

2.  *Views\\Home\\AllTags.Mobile.cshtml*의 이름을 *Views\\Home\\AllTags.Mobile.cshtml.hide*로 변경합니다(이 파일을 나중에 다시 사용할 것임). 파일에 .cshtml 확장명이 더는 없기 때문에, *AllTags* 뷰를 렌더링하기 ASP.NET MVC 런타임에서 사용하지 않습니다.

3.  다음과 같이 하여 jQuery.Mobile.MVC NuGet 패키지를 설치합니다.

    a. **도구** 메뉴에서 **패키지 관리자** 콘솔을 선택한 다음 **라이브러리 패키지 관리자**를 선택합니다.

  ![Library package manager][jquery1]

    b. **패키지 관리자 콘솔**에서 *Install-Package jQuery.Mobile.MVC -version 1.0.0*을 입력합니다.

  ![Package manager console][jquery2]

jQuery.Mobile.MVC NuGet 패키지는 다음을 설치합니다.

-   *App\_Start\\BundleMobileConfig.cs* 파일, 추가한 jQuery JavaScript 및 CSS 파일을 참조할 때 필요합니다. 아래 지침을 따르고 이 파일에 정의된 모바일 번들을 참조해야 합니다.
-   jQuery Mobile CSS 파일
-   뷰 전환기 콘트롤러 위젯(*Controllers\\ViewSwitcherController.cs)*
-   jQuery Mobile JavaScript 파일
-   jQuery Mobile에서 디자인한 레이아웃 파일(*Views\\Shared\_Layout.Mobile.cshtml*)
-   데스크톱 뷰와 모바일 뷰 간을 전환하기 위해 각 페이지의 맨 위에 링크를 제공하는 뷰 전환기 부분 뷰(*MvcMobile\\Views\\Shared\_ViewSwitcher.cshtml*)
-   Content\\images 폴더의 여러 .png 및 .gif 이미지 파일

*Global.asax* 파일을 열고 Application\_Start 메서드의 마지막 줄로 다음 코드를 추가합니다.

 	BundleMobileConfig.RegisterBundles(BundleTable.Bundles);

다음 코드는 전체 Global.asax 파일을 보여 줍니다.

    using System; 
    using System.Web.Http; 
    using System.Web.Mvc; 
    using System.Web.Optimization; 
    using System.Web.Routing; 
    using System.Web.WebPages; 
     
    namespace MvcMobile 
    { 
     
        public class MvcApplication : System.Web.HttpApplication 
        { 
            protected void Application_Start() 
            { 
                DisplayModeProvider.Instance.Modes.Insert(0, new DefaultDisplayMode("iPhone") 
                { 
                    ContextCondition = (context => context.GetOverriddenUserAgent().IndexOf 
                        ("iPhone", StringComparison.OrdinalIgnoreCase) >= 0) 
                }); 
                AreaRegistration.RegisterAllAreas(); 
     
                WebApiConfig.Register(GlobalConfiguration.Configuration); 
                FilterConfig.RegisterGlobalFilters(GlobalFilters.Filters); 
                RouteConfig.RegisterRoutes(RouteTable.Routes); 
                BundleConfig.RegisterBundles(BundleTable.Bundles); 
                BundleMobileConfig.RegisterBundles(BundleTable.Bundles); 
            } 
        } 
    }

*MvcMobile\\Views\\Shared\\\_Layout.Mobile.cshtml* 파일을 열고 *Html.Partial* 호출 바로 뒤에 다음 태그를 추가합니다.

    <div data-role="header" align="center">
        @Html.ActionLink("Home", "Index", "Home")
        @Html.ActionLink("Date", "AllDates")
        @Html.ActionLink("Speaker", "AllSpeakers")
        @Html.ActionLink("Tag", "AllTags")
    </div>

전체 본문 섹션은 다음과 같습니다.

    <body>
        <div data-role="page" data-theme="a">
            @Html.Partial("_ViewSwitcher")
            <div data-role="header" align="center">
                @Html.ActionLink("Home", "Index", "Home")
                @Html.ActionLink("Date", "AllDates")
                @Html.ActionLink("Speaker", "AllSpeakers")
                @Html.ActionLink("Tag", "AllTags")
            </div>
            <div data-role="header">
                <h1>@ViewBag.Title</h1>
            </div>
            <div data-role="content">
                @RenderSection("featured", false)
                @RenderBody()
            </div>
        </div>
    </body>

응용 프로그램을 빌드하고 모바일 브라우저 에뮬레이터에서 AllTags 뷰를 탐색합니다. 다음이 표시됩니다.

![nuget을 통해 jquery 설치 후](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_p3_afternuget_thumb.png)

**참고**

IE 또는 Chrome에 대해 사용자 에이전트 문자열을 iPhone으로 설정한 다음 F-12 개발자 도구를 사용하여 모바일 전용 코드를 디버그할 수 있습니다. 모바일 브라우저에 **Home**, **Speaker**, **Tag** 및 **Date** 링크가 단추로 표시되지 않는 경우 jQuery Mobile 스크립트 및 CSS 파일에 대한 참조가 올바르지 않을 수 있습니다.

스타일 변경과 함께, **Displaying mobile view** 및 모바일 뷰에서 데스크톱 뷰로 전환할 수 있는 링크가 표시됩니다. **Desktop view** 링크를 선택하면 데스크톱 뷰가 표시됩니다.

데스크톱 뷰에는 모바일 뷰로 다시 돌아가는 방법이 없습니다. 지금 이 문제를 수정하겠습니다. *Views\\Shared\\\_Layout.cshtml* 파일을 엽니다. &lt;body\> 요소 바로 아래에 뷰 전환기 위젯을 렌더링하는 다음 코드를 추가합니다.

    @Html.Partial("_ViewSwitcher")

완성된 코드는 다음과 같습니다.

    <body>
        @Html.Partial("_ViewSwitcher")

        <div id="title">
            <h1> MVC4 Conference </h1>
        </div>

        @*Items removed for clarity.*@
    </body>

모바일 브라우저에서 **AllTags** 뷰를 새로 고칩니다. 이제 데스크톱 뷰와 모바일 뷰 간을 전환할 수 있습니다.

![모바일 뷰로 이동](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_p3_desktopviewwithmobilelink_thumb.png)

**참고**

사용자 에이전트 문자열이 모바일 장치로 설정된 브라우저를 사용할 때 다음 코드를 Views\\Shared\\\_ViewSwitcher.cshtml의 끝에 추가하여 뷰를 디버그할 수 있습니다.

``` {}
    else 
    { 
         @:Not Mobile/Get 
    } 
```

그리고 다음 제목을 Views\\Shared\\\_Layout.cshtml 파일에 추가합니다.

``` {}
    <h1>Non Mobile Layout MVC4 Conference</h1>
```

데스크톱 브라우저에서 AllTags 페이지로 이동합니다. 뷰 전환기 위젯은 모바일 레이아웃 페이지에만 추가되었기 때문에 데스크톱 브라우저에 표시되지 않습니다. 이 자습서의 뒷부분에 데스크톱 뷰에 뷰 전환기 위젯을 추가하는 방법이 나와 있습니다.

![데스크톱 환경 보기](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/Windows-Live-Writer_ASP_NET-MVC-4-Mobile-Features_D2FF_p3_desktopBrowser_thumb.png)

발표자 목록 개선
----------------

모바일 브라우저에서 **Speakers** 링크를 선택합니다. 모바일 뷰(*AllSpeakers.Mobile.cshtml*)가 없기 때문에 모바일 레이아웃 뷰(*\_Layout.Mobile.cshtml*)를 사용하여 기본 발표자 화면(*AllSpeakers.cshtml*)이 렌더링됩니다.

![모바일 발표자 목록 표시](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_p3_speakersdesktop_thumb.png)

다음과 같이 *Views\_ViewStart.cshtml* 파일에서 RequireConsistentDisplayMode를 true로 설정하여 모바일 레이아웃 내에서 기본(모바일 아님) 뷰가 렌더링되지 않도록 전체적으로 비활성화할 수 있습니다.

![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_p3_speakersconsistent_thumb.png)

    @{
        Layout = "~/Views/Shared/_Layout.cshtml";
        DisplayModes.RequireConsistentDisplayMode = true;
    }

*RequireConsistentDisplayMode*가 true로 설정되면 모바일 레이아웃(*\_Layout.Mobile.cshtml*)은 모바일 뷰에서만 사용됩니다. (즉 뷰 파일은 ViewName.Mobile.cshtml 형식입니다.) 모바일 레이아웃이 비모바일 뷰와 잘 작동하지 않는 경우 *RequireConsistentDisplayMode*를 true로 설정할 수 있습니다. 아래 스크린샷은 *RequireConsistentDisplayMode*가 true로 설정되었을 때 발표자 페이지가 어떻게 렌더링되는지 보여 줍니다.

![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_ps_data_filter_thumb.png)

뷰 파일에서 *RequireConsistentDisplayMode*를 false로 설정하여 뷰의 일관된 디스플레이 모드를 비활성화할 수 있습니다. *Views\\Home\\AllSpeakers.cshtml* 파일의 다음 태그는 *RequireConsistentDisplayMode*를 false로 설정합니다.

    @model IEnumerable<string>
    @{
        ViewBag.Title = "All speakers";
        DisplayModes.RequireConsistentDisplayMode = false;
    }

모바일 발표자 뷰 만들기
-----------------------

방금 본 것처럼 발표자 뷰는 가독성은 있지만 링크가 작고 모바일 장치에서 누르기 어렵습니다. 이 섹션에서는 현대식 모바일 응용 프로그램처럼 보이는 모바일 전용 발표자 뷰를 만듭니다. 크고 누르기 쉬운 링크를 표시하고 발표자를 빨리 찾을 수 있는 검색 상자가 포함됩니다.

1.  *AllSpeakers.cshtml*을 *AllSpeakers.Mobile.cshtml*로 복사합니다. *AllSpeakers.Mobile.cshtml* 파일을 열고 &lt;h2\>제목 요소를 제거합니다.
2.  **&lt;ul\>** 태그에서 data-role 특성을 추가하고 값을 *listview*로 설정합니다. *data-** attributes, *data-role="listview"*는 큰 목록 항목을 쉽게 누를 수 있도록 만듭니다. 완성된 태그는 다음과 같습니다.

         @model IEnumerable<string>
         @{
             ViewBag.Title = "All speakers";
         }
         <ul data-role="listview">
             @foreach(var speaker in Model) {
                 <li>@Html.ActionLink(speaker, "SessionsBySpeaker", new { speaker })</li>
             }
         </ul>

3.  모바일 브라우저를 새로 고칩니다. 업데이트된 뷰는 다음과 같이 표시됩니다.

    ![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_p3_updatedspeakerview1_thumb.png)

4.  **&lt;ul\>** 태그에서 data-filter 특성을 추가하고 true로 설정합니다. 아래 코드는 ul 태그를 보여 줍니다.

        <ul data-role="listview" data-filter="true">

다음 이미지는 data-filter 특성의 결과로 생긴 페이지 상단의 검색 필터를 보여 줍니다.

![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_ps_data_filter_thumb.png)

검색 상자에 각 문자를 입력하면 jQuery Mobile이 아래 이미지에 표시된 것처럼 표시된 목록을 필터링합니다.

![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_ps_data_filter_sc_thumb.png)

태그 목록 개선
--------------

기본 발표자 뷰처럼 태그 뷰도 가독성은 있지만 링크가 작고 모바일 장치에서 누르기 어렵습니다. 이 섹션에서는 발표자 뷰를 수정한 것과 같은 방법으로 태그 뷰를 수정합니다.

1.  *Views\\Home\\AllTags.Mobile.cshtml.hide* 파일의 이름을 *Views\\Home\\AllTags.Mobile.cshtml*로 변경합니다. 이름을 변경한 파일을 열고 **&lt;h2\>** 요소를 제거합니다.

2.  다음과 같이 data-role 및 data-filter 특성을 **&lt;ul\>** 태그에 추가합니다.

         <ul data-role="listview" data-filter="true">

    아래 이미지는 J 문자의 태그 페이지 필터링을 보여 줍니다.

![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_p3_tags_j_thumb.png)

날짜 목록 개선
--------------

**Speakers** 및 **Tags** 뷰를 개선한 것처럼 모바일 장치에서 사용하기 편하도록 Dates 뷰도 개선할 수 있습니다.

1.  *Views\\Home\\AllDates.Mobile.cshtml* 파일을 *Views\\Home\\AllDates.Mobile.cshtml*로 복사합니다.
2.  새 파일을 열고 **&lt;h2\>** 요소를 제거합니다.
3.  다음과 같이 *data-role="listview"*를 &lt;ul\> 태그에 추가합니다.

         <ul data-role="listview">

아래 이미지는 data-role 특성을 추가한 **Date** 페이지의 모습입니다.

![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_p3_dates1_thumb.png)

*Views\\Home\\AllDates.Mobile.cshtml* 파일의 내용을 다음 코드를 사용하여 바꿉니다.

    @model IEnumerable<DateTime>
    @{
        ViewBag.Title = "All dates";
        DateTime lastDay = default(DateTime);
    }
    <ul data-role="listview">
        @foreach(var date in Model) {
            if (date.Date != lastDay) {
                lastDay = date.Date;
                <li data-role="list-divider">@date.Date.ToString("ddd, MMM dd")</li>
            }
            <li>@Html.ActionLink(date.ToString("h:mm tt"), "SessionsByDate", new { date })</li>
        }
    </ul>

이 코드는 모든 세션을 날짜별로 그룹화합니다. 이 코드는 각 날짜에 대해 목록 구분선을 만들고 구분선 아래에 각 날짜에 해당하는 세션을 모두 나열합니다. 이 코드를 실행하면 다음과 같이 표시됩니다.

![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_p3_dates2_thumb.png)

SessionsTable 뷰 개선
---------------------

이 섹션에서는 모바일 전용 세션 뷰를 만듭니다. 다른 뷰보다 변경 사항이 더 많습니다.

모바일 브라우저에서 **Speaker** 단추를 누르고 검색 상자에 Sc를 입력합니다.

![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_ps_data_filter_sc_thumb_1.png)

**Scott Hanselman** 링크를 누릅니다.

![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_p3_scottha_thumb.png)

화면이 모바일 브라우저에서 읽기 어렵습니다. 날짜 열은 읽기 힘들며 태그 열은 뷰 범위를 벗어났습니다. 이를 수정하기 위해 Views\*Home\\SessionsTable.cshtml\*을 *Views\\Home\\SessionsTable.Mobile.cshtml*로 복사한 후 다음 코드를 사용하여 파일의 내용을 바꿉니다.

    @using MvcMobile.Models
    @model IEnumerable<Session>

    <ul data-role="listview">
        @foreach(var session in Model) {
            <li>
                <a href="@Url.Action("SessionByCode", new { session.Code })">
                    <h3>@session.Title</h3>
                    <p><strong>@string.Join(", ", session.Speakers)</strong></p>
                    <p>@session.DateText</p>
                </a>
            </li>
        }
    </ul>

이 코드는 회의실 및 태그 열을 제거하고 제목, 발표자, 날짜 형식을 세로로 만들어 모바일 브라우저에서 이 모든 정보를 읽을 수 있도록 만듭니다. 아래 이미지는 코드 변경 내용이 반영된 화면입니다.

![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_ps_sessionsbyscottha_thumb.png)

SessionByCode 뷰 개선
---------------------

마지막으로 **SessionByCode** 뷰의 모바일 전용 뷰를 만듭니다. 모바일 브라우저에서 **Speaker** 단추를 누르고 검색 상자에 Sc를 입력합니다.

![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_ps_data_filter_sc_thumb_2.png)

**Scott Hanselman** 링크를 누릅니다. Scott Hanselman의 세션이 표시됩니다.

![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_p3_scottha_thumb.png)

**An Overview of the MS Web Stack of Love** 링크를 선택합니다.

![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_ps_love_thumb.png)

기본 데스크톱 뷰도 좋지만 이 뷰를 향상할 수 있습니다.

*Views\\Home\\SessionByCode.cshtml*을 *Views\\Home\\SessionByCode.Mobile.cshtml*로 복사하고 다음 태그를 사용하여 *Views\\Home\\SessionByCode.Mobile.cshtml* 파일의 내용을 바꿉니다.

    @model MvcMobile.Models.Session

    @{
        ViewBag.Title = "Session details";
    }
    <h2>@Model.Title</h2>
    <p>
        <strong>@Model.DateText</strong> in <strong>@Model.Room</strong>
    </p>

    <ul data-role="listview" data-inset="true">
        <li data-role="list-divider">Speakers</li>
        @foreach (var speaker in Model.Speakers) {
            <li>@Html.ActionLink(speaker, "SessionsBySpeaker", new { speaker })</li>
        }
    </ul>

    <p>@Model.Description</p>
    <h4>Code: @Model.Code</h4>

    <ul data-role="listview" data-inset="true">
        <li data-role="list-divider">Tags</li>
        @foreach (var tag in Model.Tags) {
            <li>@Html.ActionLink(tag, "SessionsByTag", new { tag })</li>
        }
    </ul>

새 태그는 **data-role** 특성을 사용하여 뷰의 레이아웃을 향상합니다.

모바일 브라우저를 새로 고칩니다. 다음 이미지는 방금 변경한 코드가 반영된 화면입니다.

![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_p3_love2_thumb.png)

Azure 웹 사이트에 응용 프로그램 배포
------------------------------------

1.  Visual Studio의 **솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 상황에 맞는 메뉴에서 **게시**를 선택합니다.

    ![프로젝트 상황에 맞는 메뉴의 게시](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/PublishVSSolution.png)

    **웹 게시** 마법사가 열립니다.

2.  **웹 게시** 마법사의 **프로필** 탭에서 **가져오기**를 클릭합니다.

    ![게시 설정 가져오기](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/ImportPublishSettings.png)

    **게시 프로필 가져오기** 대화 상자가 나타납니다.

3.  이전에 Visual Studio에 Azure 구독을 추가하지 않은 경우 다음 단계를 수행하십시오. 이들 단계에서 구독을 추가하여 **Azure 웹 사이트에서 가져오기** 아래의 드롭다운 목록에 웹 사이트를 포함합니다.

    1.  **Import Publish Profile** 대화 상자에서 **Add Azure subscription**을 클릭합니다.

    ![win az 구독 추가](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/rzAddWAsub.png)

    1.  **Import Azure Subscriptions** 대화 상자에서 **구독 파일 다운로드**를 클릭합니다.

    ![구독 다운로드](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/rzDownLoad.png)

    1.  브라우저 창에서 *.publishsettings* 파일을 저장합니다.

    ![게시 파일 다운로드](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/rzDown2.png)

	[WACOM.INCLUDE [publishsettingsfilewarningchunk](../includes/publishsettingsfilewarningchunk.md)]
</br>

    1.  **Import Azure Subscriptions** 대화 상자에서 **찾아보기**를 클릭하고 *.publishsettings* 파일로 이동합니다.

    ![구독 다운로드](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/rzDownLoad.png)

    1.  **가져오기**를 클릭합니다.

    ![가져오기](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/rzImp.png)

    1.  **게시 프로필 가져오기** 대화 상자에서 **Azure 웹 사이트에서 가져오기**를 선택하고 드롭다운 목록에서 웹 사이트를 선택한 후 **확인**을 클릭합니다.

    ![게시 프로필 가져오기](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/ImportPublishProfile.png)

    1.  **연결** 탭에서 **연결 유효성 검사**를 클릭하여 설정이 올바른지 확인합니다.

    ![연결 유효성 검사](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/ValidateConnection.png)

    1.  연결 유효성이 검사되면 **연결 유효성 검사** 단추 옆에 녹색 확인 표시가 나타납니다. 
    ![연결 성공 아이콘 및 연결 탭의 다음 단추](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/dntutmobile-deploy1-publish-005.png)

    2.  이 페이지의 기본 설정을 모두 적용할 수 있습니다. 릴리스 빌드 구성을 배포 중이며 대상 서버의 파일을 삭제할 필요가 없습니다. **데이터베이스** 아래의 **UsersContext (DefaultConnection)** 항목은 DefaultConnection 문자열을 사용하는 *UsersContext:DbContext* 클래스에서 나옵니다. **다음**을 클릭합니다.

    ![연결 탭의 연결 성공 아이콘 및 다음 단추](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/rxPWS.png)

    1.  **미리 보기** 탭에서 **미리 보기 시작**을 클릭합니다. 이 탭에는 서버로 복사될 파일 목록이 표시됩니다. 미리 보기 표시는 응용 프로그램을 게시하는 데 필요하지 않지만 알아 두면 유용한 기능입니다. 따라서 표시된 파일 목록에 어떤 작업도 수행할 필요가 없습니다. 다음번에 게시할 때 변경된 파일만 미리 보기 목록에 나타납니다.

    ![미리 보기 탭의 미리 보기 시작 단추](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/dntutmobile-deploy1-publish-007.png)

    1.  **게시**를 클릭합니다.

    Visual Studio에서 Azure 서버로 파일을 복사하는 프로세스를 시작합니다. **출력** 창에 수행된 배포 작업이 표시되고 성공적인 배포 완료가 보고됩니다.

    1.  배포된 사이트의 URL이 기본 브라우저에서 자동으로 열립니다. 만든 응용 프로그램이 이제 클라우드에서 실행되고 있습니다.

    ![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/depoly_mobile_new_website_15.png)

모바일 브라우저에서 사이트 URL을 탐색하여 Phone Emulator를 사용하여 라이브 웹 사이트를 테스트할 수 있습니다.

<!-- Images -->
[jquery1]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-mobile-open-packagmanager.png
[jquery2]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-mobile-open-install-jquey.png
[jquery3]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_p3_afternuget_thumb.png
[jquery4]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_p3_desktopviewwithmobilelink_thumb.png
[jquery5]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_p3_desktopviewwithmobilelink_thumb.png
[jquery6]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/Windows-Live-Writer_ASP_NET-MVC-4-Mobile-Features_D2FF_p3_desktopBrowser_thumb.png