<properties linkid="develop-dotnet-website-from-gallery" urlDisplayName="Website from Gallery" pageTitle="Create an Orchard CMS website from the gallery in Azure" metaKeywords="Azure build website, manage website Azure" description="A tutorial that teaches you how to create a new website in Azure. Also learn how to launch and manage your site using the Management Portal." metaCanonical="" services="web-sites" documentationCenter=".NET" title="Create an Orchard CMS website from the gallery in Azure" authors="cephalin" solutions="" manager="wpickett" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="cephalin" />

# Azure의 갤러리에서 Orchard CMS 웹 사이트 만들기

갤러리는 Microsoft, 타사 및 오픈 소스 소프트웨어에서 개발된 다양한 인기 웹 응용 프로그램을 사용할 수 있습니다. 갤러리에서 만든 웹 응용 프로그램은 [Azure 관리 포털][Azure 관리 포털]에 연결하는 데 사용되는 브라우저 외에 다른 소프트웨어를 설치할 필요가 없습니다. 갤러리의 웹 응용 프로그램에 대한 자세한 내용은 [Windows 웹 앱 갤러리][Windows 웹 앱 갤러리]를 참조하십시오.

이 자습서에서는 다음에 대해 알아봅니다.

-   갤러리에서 새 사이트를 만드는 방법

-   관리 포털에서 사이트를 시작하고 관리하는 방법

이 자습서에서는 기본 템플릿을 사용하는 Orchard CMS 사이트를 빌드해 봅니다. [Orchard][Orchard]는 사용자 지정된 콘텐츠 중심의 웹 사이트를 만들 수 있는 오픈 소스의 무료 .NET 기반 CMS 응용 프로그램입니다. Orchard CMS에는 [추가 모듈 및 테마를 다운로드하여][추가 모듈 및 테마를 다운로드하여] 사이트를 사용자 지정할 수 있는 확장성 프레임워크가 포함되어 있습니다. 다음 그림은 앞으로 만들 Orchard CMS 사이트를 보여 줍니다.

![Orchard 블로그][Orchard 블로그]

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

## 갤러리에서 Orchard 웹 사이트 만들기

1.  [Azure 관리 포털][Azure 관리 포털]에 로그인합니다.

2.  포털의 왼쪽 아래에서 **새로 만들기** 아이콘을 클릭합니다.

    ![새로 만들기][새로 만들기]

3.  **웹 사이트** 아이콘을 클릭하고 **갤러리에서**를 클릭합니다.

    ![갤러리에서 만들기][갤러리에서 만들기]

4.  목록에서 **Orchard CMS** 아이콘을 찾아서 클릭한 후 화살표를 클릭하여 다음 단계를 진행합니다.

    ![목록의 Orchard][목록의 Orchard]

5.  **앱 구성** 페이지에서 모든 필드에 대해 값을 입력하거나 선택합니다.

-   선택한 URL 이름을 입력합니다.
-   사용자에게 가장 가까운 지역을 선택합니다. 그렇게 해야 최상의 성능이 보장됩니다.

    ![앱 구성][앱 구성]

1.  상자 아래의 오른쪽에 있는 확인 표시를 클릭하여 새로운 Orchard CMS 웹 사이트 배포를 시작합니다.

Azure에서 빌드 및 배포 작업을 시작합니다. 웹 사이트를 만들고 배포하는 동안 작업 상태가 웹 사이트 관리 포털의 아래쪽에 표시됩니다. 모든 작업이 수행된 후 웹 사이트가 만들어졌음을 나타내는 메시지가 표시됩니다.

## Orchard 사이트 시작 및 관리

1.  **웹 사이트** 페이지에서 새 사이트 이름을 클릭한 후 포털 아래쪽에서 **찾아보기**를 클릭하여 웹 사이트의 시작 페이지를 엽니다.

    ![대시보드 시작][대시보드 시작]

    ![찾아보기 단추][찾아보기 단추]

2.  Orchard에 필요한 구성 정보를 입력한 후 **설정 완료**를 클릭하여 구성을 완료하고 웹 사이트의 홈페이지를 엽니다.

    ![Orchard에 로그인][Orchard에 로그인]

    새 Orchard 사이트는 아래 스크린샷과 유사합니다.

    ![Orchard 사이트][Orchard 블로그]

3.  [Orchard 문서][Orchard 문서](영문)의 세부 정보를 따라서 Orchard에 대해 자세히 알아보고 새 사이트를 구성합니다.

## <span class="short-header">다음 단계</span>다음 단계

-   [Microsoft WebMatrix로 웹 사이트 개발 및 배포][Microsoft WebMatrix로 웹 사이트 개발 및 배포] - WebMatrix에서 Azure 웹 사이트를 편집하는 방법을 알아봅니다.
-   [Azure 웹 사이트에 멤버 자격, OAuth 및 SQL 데이터베이스가 포함된 보안 ASP.NET MVC 앱 배포][Azure 웹 사이트에 멤버 자격, OAuth 및 SQL 데이터베이스가 포함된 보안 ASP.NET MVC 앱 배포] - Visual Studio에서 새 웹 사이트를 만드는 방법을 알아봅니다.

  [Azure 관리 포털]: http://manage.windowsazure.com
  [Windows 웹 앱 갤러리]: http://www.microsoft.com/web/gallery/categories.aspx
  [Orchard]: http://www.orchardproject.net/
  [추가 모듈 및 테마를 다운로드하여]: http://gallery.orchardproject.net/
  [Orchard 블로그]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-08.png
  [새로 만들기]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-01.png
  [갤러리에서 만들기]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-02.png
  [목록의 Orchard]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-03.png
  [앱 구성]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-04.png
  [대시보드 시작]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-05.png
  [찾아보기 단추]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-12.png
  [Orchard에 로그인]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-07.png
  [Orchard 문서]: http://docs.orchardproject.net/
  [Microsoft WebMatrix로 웹 사이트 개발 및 배포]: /ko-KR/develop/net/tutorials/website-with-webmatrix/
  [Azure 웹 사이트에 멤버 자격, OAuth 및 SQL 데이터베이스가 포함된 보안 ASP.NET MVC 앱 배포]: /ko-KR/develop/net/tutorials/web-site-with-sql-database/
