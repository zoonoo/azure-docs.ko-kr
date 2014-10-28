<properties title="Create an Umbraco website from the gallery in Microsoft Azure" pageTitle="Create an Umbraco website from the gallery in Microsoft Azure" description="required" metaKeywords="Azure, gallery, Umbraco, web site, website" services="web-sites" solutions="web" documentationCenter="" authors="timamm" manager="paulettm" editor="mollybos" videoId="" scriptId="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="timamm"></tags>

# Microsoft Azure의 갤러리에서 Umbraco 웹 사이트 만들기

Umbraco CMS는 소규모 응용 프로그램에서 복잡한 응용 프로그램에 이르기까지 다양한 응용 프로그램을 만드는 데 사용할 수 있는 전기능 오픈 소스 콘텐츠 관리 시스템입니다. Azure 웹 사이트 응용 프로그램 갤러리는 Microsoft, 타사 및 오픈 소스 소프트웨어에서 개발된 다양한 인기 웹 응용 프로그램을 사용할 수 있습니다. 이 갤러리에서 시작 키트를 적용하거나 사용자 자신의 디자인을 통합하여 단 몇 분 만에 Umbraco CMS 사이트를 만들 수 있습니다.

이 문서에서는 리소스 관리를 획기적으로 간소화하는 새로운 Azure 미리 보기 포털에 대해 설명합니다. 이 새로운 Azure 포털은 단일 작업 영역에서 Microsoft 및 해당 파트너의 플랫폼 간 도구, 기술 및 서비스를 배치하여 소프트웨어 전달 프로세스를 가속화하도록 디자인되었습니다. Azure 웹 사이트, Visual Studio 프로젝트 또는 데이터베이스와 같은 독립 실행형 리소스를 사용하는 대신, 전체 응용 프로그램을 단일 리소스 그룹으로 만들고 관리하고 분석할 수 있습니다.

이 자습서에서는 다음에 대해 알아봅니다.

-   새 Azure 미리 보기 포털을 사용하여 갤러리를 통해 새 사이트를 만드는 방법
-   Umbraco CMS를 사용하여 블로그 웹 사이트를 빌드하는 방법

## Azure 포털의 갤러리에서 웹 사이트 만들기

1.  [Microsoft Azure 관리 포털][Microsoft Azure 관리 포털]에 로그인합니다.

2.  **Azure 갤러리** 아이콘을 선택합니다.

    ![웹 갤러리 선택][웹 갤러리 선택]

3.  **갤러리**에서 **웹** 탭을 선택하고 **Umbraco CMS**를 선택합니다.

    ![웹 갤러리에서 Umbraco 선택][웹 갤러리에서 Umbraco 선택]

4.  새 Umbraco CMS 웹 사이트를 만들려면 **만들기**를 클릭합니다.

    ![만들기 클릭][만들기 클릭]

5.  다음 단계는 Umbraco CMS와 연결된 모든 리소스를 구성하는 것입니다. 이 경우 리소스는 웹 사이트와 SQL Server 데이터베이스입니다. 먼저 **웹 사이트**를 선택하여 **URL**, **웹 호스팅 계획**, **웹앱 설정** 및 **위치**와 같은 웹 사이트 설정을 구성합니다.

    ![리소스 구성][리소스 구성]

6.  이제 데이터베이스를 구성합니다. **데이터베이스**를 선택하고 **새 데이터베이스 만들기**를 선택합니다. 이 예에서는 Azure의 데이터베이스에 대한 SQL Server를 만듭니다.

    ![Azure에서 SQL Server 만들기][Azure에서 SQL Server 만들기]

7.  웹 사이트와 데이터베이스를 구성했으므로 이전 이미지의 첫 번째 **Umbraco CMS** 블레이드 아래쪽에 있는 **만들기**를 클릭하여 응용 프로그램 배포를 시작할 수 있습니다.

    ![만들기 클릭][1]

배포가 완료되면 포털의 시작 보드에 Umbraco CMS용 리소스 그룹(이 경우는 **UmbracoCMSgroup**)이 만들어졌다는 정보가 표시됩니다. **요약** 섹션에서 웹 사이트 이름(이 경우는 **umbracocmsgroup**)을 클릭하여 웹 사이트의 속성을 확인합니다. 또한 **요약** 섹션에서 데이터베이스 리소스를 선택하여 연결된 데이터베이스의 속성을 확인할 수 있습니다.

![][]

## Umbraco CMS 웹 사이트 시작 및 구성

1.  웹 사이트의 세부 정보 블레이드에서 **찾아보기**를 클릭하여 사이트(이 경우는 umbracocmsgroup.azurewebsites.net)를 찾아봅니다.

    ![사이트 찾아보기][사이트 찾아보기]

2.  웹 사이트를 찾으면 Umbraco CMS의 설치 관리자 마법사를 시작합니다. 요청된 정보를 입력한 후 **사용자 지정**을 클릭합니다.

    ![Umbraco 설치 마법사][Umbraco 설치 마법사]

3.  Umbraco에서 사용될 데이터베이스에 대한 연결 및 인증 세부 정보를 입력합니다. 데이터베이스 유형에 대한 **Microsoft SQL Azure**를 선택합니다. 웹 사이트의 **사이트 설정** 섹션에서 데이터베이스에 대한 연결 문자열을 얻을 수 있습니다.

    ![데이터베이스 구성][데이터베이스 구성]

4.  Umbraco CMS를 처음 사용하는 경우에는 시작 웹 사이트 키트를 선택할 수 있습니다. 그렇지 않은 경우 **No thanks, I do not want to install a starter website**를 클릭합니다.

    ![시작 웹 사이트 시작][시작 웹 사이트 시작]

5.  Umbraco 설치 관리자는 응용 프로그램에 대한 설치를 완료합니다. 응용 프로그램이 구성되면 Umbraco CMS 관리 대시보드로 리디렉션됩니다.

    ![Umbraco CMS 대시보드][Umbraco CMS 대시보드]

6.  이제 게시할 샘플 텍스트 페이지가 만들어집니다. **Content**, **Overflow** 및 **TextPage**를 차례로 선택합니다.

    ![텍스트 페이지 만들기][텍스트 페이지 만들기]

7.  아래 표시된 것처럼 텍스트 페이지의 제목과 일부 콘텐츠를 입력합니다. 완료되면 **저장 및 게시**를 클릭합니다.

    ![제목 및 일부 콘텐츠 입력][제목 및 일부 콘텐츠 입력]

8.  페이지가 게시되는 동안 잠시 기다리세요. 게시가 끝나면 화면 오른쪽 아래에 작은 경고가 표시됩니다. 이제 웹 사이트에서 새 콘텐츠를 찾아볼 수 있습니다.

    ![게시된 웹 사이트 페이지][게시된 웹 사이트 페이지]

끝났습니다. 단 몇 분 만에 Umbraco CMS를 사용하여 블로그 웹 사이트를 만드는 데 성공했습니다.

## 추가 리소스

[Umbraco 설명서(영문)][Umbraco 설명서(영문)]

[Umbraco 비디오 자습서(영문)][Umbraco 비디오 자습서(영문)]

[Microsoft Azure 미리 보기 포털 개요][Microsoft Azure 미리 보기 포털 개요]

[Microsoft Azure 미리 보기 포털 설명서][Microsoft Azure 미리 보기 포털 설명서]

[Azure 미리 보기 포털(채널 9)(영문)][Azure 미리 보기 포털(채널 9)(영문)]

[Microsoft Azure 웹 사이트 설명서][Microsoft Azure 웹 사이트 설명서]

<!-- IMAGES -->

  [Microsoft Azure 관리 포털]: https://portal.azure.com/
  [웹 갤러리 선택]: ./media/web-sites-gallery-umbraco/01Startboard.PNG
  [웹 갤러리에서 Umbraco 선택]: ./media/web-sites-gallery-umbraco/02WebGallery.PNG
  [만들기 클릭]: ./media/web-sites-gallery-umbraco/03UmbracoCMS.PNG
  [리소스 구성]: ./media/web-sites-gallery-umbraco/04AppSettings.PNG
  [Azure에서 SQL Server 만들기]: ./media/web-sites-gallery-umbraco/05NewServer.PNG
  [1]: ./media/web-sites-gallery-umbraco/06UmbracoCMSGroup.PNG
  []: ./media/web-sites-gallery-umbraco/07UmbracoCMSGroupBlade.PNG
  [사이트 찾아보기]: ./media/web-sites-gallery-umbraco/08UmbracoCMSGroupRunning.PNG
  [Umbraco 설치 마법사]: ./media/web-sites-gallery-umbraco/09InstallUmbraco7.png
  [데이터베이스 구성]: ./media/web-sites-gallery-umbraco/10ConfigureYourDatabase.png
  [시작 웹 사이트 시작]: ./media/web-sites-gallery-umbraco/11InstallAStarterWebsite.png
  [Umbraco CMS 대시보드]: ./media/web-sites-gallery-umbraco/14FriendlyCMS.PNG
  [텍스트 페이지 만들기]: ./media/web-sites-gallery-umbraco/15CreateItemUnderOverflow.PNG
  [제목 및 일부 콘텐츠 입력]: ./media/web-sites-gallery-umbraco/16EnterAName.PNG
  [게시된 웹 사이트 페이지]: ./media/web-sites-gallery-umbraco/17MyPage.PNG
  [Umbraco 설명서(영문)]: http://our.umbraco.org/documentation
  [Umbraco 비디오 자습서(영문)]: https://umbraco.com/help-and-support/video-tutorials.aspx
  [Microsoft Azure 미리 보기 포털 개요]: http://azure.microsoft.com/ko--kr/overview/preview-portal/
  [Microsoft Azure 미리 보기 포털 설명서]: http://azure.microsoft.com/ko--kr/documentation/preview-portal/
  [Azure 미리 보기 포털(채널 9)(영문)]: http://channel9.msdn.com/Blogs/Windows-Azure/Azure-Preview-portal
  [Microsoft Azure 웹 사이트 설명서]: http://azure.microsoft.com/ko--kr/documentation/services/web-sites/
