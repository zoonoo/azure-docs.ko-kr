<properties 
	pageTitle="Microsoft Azure의 갤러리에서 Umbraco 웹 사이트 만들기" 
	description="필수" 
	services="web-sites" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/21/2014" 
	ms.author="tomfitz"/>

#Microsoft Azure의 갤러리에서 Umbraco 웹 사이트 만들기#

Umbraco CMS는 소규모 응용 프로그램에서 복잡한 응용 프로그램에 이르기까지 다양한 응용 프로그램을 만드는 데 사용할 수 있는 전기능 오픈 소스 콘텐츠 관리 시스템입니다. Azure 웹 사이트 응용 프로그램 갤러리는 Microsoft, 타사 및 오픈 소스 소프트웨어에서 개발된 다양한 인기 웹 응용 프로그램을 사용할 수 있습니다. 이 갤러리에서 시작 키트를 적용하거나 사용자 자신의 디자인을 통합하여 단 몇 분 만에 Umbraco CMS 사이트를 만들 수 있습니다. 

이 문서에서는 리소스 관리를 획기적으로 간소화하는 새로운 Azure 미리 보기 포털에 대해 설명합니다. 이 새로운 Azure 포털은 단일 작업 영역에서 Microsoft 및 해당 파트너의 플랫폼 간 도구, 기술 및 서비스를 배치하여 소프트웨어 전달 프로세스를 가속화하도록 디자인되었습니다. Azure 웹 사이트, Visual Studio 프로젝트 또는 데이터베이스와 같은 독립 실행형 리소스를 사용하는 대신, 전체 응용 프로그램을 단일 리소스 그룹으로 만들고 관리하고 분석할 수 있습니다. 

이 자습서에서는 다음에 대해 알아봅니다.

- 새 Azure 미리 보기 포털을 사용하여 갤러리를 통해 새 사이트를 만드는 방법
- Umbraco CMS를 사용하여 블로그 웹 사이트를 빌드하는 방법 

##Azure 포털의 갤러리에서 웹 사이트 만들기

1. [Microsoft Azure 관리 포털](https://portal.azure.com/)에 로그인합니다.

2. **Azure 갤러리** 아이콘을 선택합니다.
	
	![Choose Web Gallery][01Startboard]
	
3. **갤러리**에서 **웹** 탭을 선택하고 **Umbraco CMS**를 선택합니다.
	
	![Select Umbraco in the Web Gallery][02WebGallery]
	
4. 새 Umbraco CMS 웹 사이트를 만들려면 **만들기**를 클릭합니다.
	
	![Click Create][03UmbracoCMS]
	
5. 다음 단계는 Umbraco CMS와 연결된 모든 리소스를 구성하는 것입니다. 이 경우 리소스는 웹 사이트와 SQL Server 데이터베이스입니다. 먼저 **웹 사이트**를 선택하여 **URL**, **웹 호스팅 계획**, **웹앱 설정** 및 **위치**와 같은 웹 사이트 설정을 구성합니다. 
	
	![Configure resources][04AppSettings]
	
6. 이제 데이터베이스를 구성합니다. **데이터베이스**를 선택하고 **새 데이터베이스 만들기**를 선택합니다. 이 예에서는 Azure의 데이터베이스에 대한 SQL Server를 만듭니다.
	
	![Create a SQL Server on Azure][05NewServer]
	
7. 웹 사이트와 데이터베이스를 구성했으므로 이전 이미지의 첫 번째 **Umbraco CMS** 블레이드 아래쪽에 있는 **만들기**를 클릭하여 응용 프로그램 배포를 시작할 수 있습니다.
	
	![Click Create][06UmbracoCMSGroup]
	
배포가 완료되면 포털의 시작 보드에 Umbraco CMS용 리소스 그룹(이 경우는 **UmbracoCMSgroup**)이 만들어졌다는 정보가 표시됩니다. **요약** 섹션에서 웹 사이트 이름(이 경우는 **umbracocmsgroup**)을 클릭하여 웹 사이트의 속성을 확인합니다. 또한 **요약** 섹션에서 데이터베이스 리소스를 선택하여 연결된 데이터베이스의 속성을 확인할 수 있습니다.
	
![][07UmbracoCMSGroupBlade]

## Umbraco CMS 웹 사이트 시작 및 구성 ##

1. 웹 사이트의 세부 정보 블레이드에서 **찾아보기**를 클릭하여 사이트(이 경우는 umbracocmsgroup.azurewebsites.net)를 찾아봅니다.
	
	![Browse to your site][08UmbracoCMSGroupRunning]
	
2. 웹 사이트를 찾으면 Umbraco CMS의 설치 관리자 마법사를 시작합니다. 요청된 정보를 입력한 후 **사용자 지정**을 클릭합니다.
	
	![Install Umbraco wizard][09InstallUmbraco7]
	
3. Umbraco에서 사용될 데이터베이스에 대한 연결 및 인증 세부 정보를 입력합니다. 데이터베이스 유형에 대한 **Microsoft SQL Azure**를 선택합니다.  웹 사이트의 **사이트 설정** 섹션에서 데이터베이스에 대한 연결 문자열을 얻을 수 있습니다.
	
	![Configure your database][10ConfigureYourDatabase] 
	
4. Umbraco CMS를 처음 사용하는 경우에는 시작 웹 사이트 키트를 선택할 수 있습니다. 그렇지 않은 경우 *No thanks, I do not want to install a starter website**를 클릭합니다.
	
	![Install a starter website][11InstallAStarterWebsite]
	
5. Umbraco 설치 관리자는 응용 프로그램에 대한 설치를 완료합니다. 응용 프로그램이 구성되면 Umbraco CMS 관리 대시보드로 리디렉션됩니다.
	
	![Umbraco CMS dashboard][14FriendlyCMS]
	
6. 이제 게시할 샘플 텍스트 페이지가 만듭니다. **콘텐츠**, **오버플로**, **TextPage**를 차례로 선택합니다.
	
	![Create a text page][15CreateItemUnderOverflow]
	
7. 아래 표시된 것처럼 텍스트 페이지의 제목과 일부 콘텐츠를 입력합니다. 완료되면 **저장 및 게시**를 클릭합니다.
	
	![Enter a title and some content][16EnterAName]
	
8. 페이지가 게시되는 동안 잠시 기다리세요. 게시가 끝나면 화면 오른쪽 아래에 작은 경고가 표시됩니다. 이제 웹 사이트에서 새 콘텐츠를 찾아볼 수 있습니다. 
	
	![Published web site page][17MyPage]
	

끝났습니다. 단 몇 분 만에 Umbraco CMS를 사용하여 블로그 웹 사이트를 만드는 데 성공했습니다. 

##추가 리소스

[Umbraco 설명서(영문)](http://our.umbraco.org/documentation)

[Umbraco 비디오 자습서(영문)](https://umbraco.com/help-and-support/video-tutorials.aspx)

[Microsoft Azure 미리 보기 포털 개요](http://azure.microsoft.com/overview/preview-portal/)

[Microsoft Azure 미리 보기 포털 설명서](http://azure.microsoft.com/documentation/preview-portal/)

[Azure 미리 보기 포털(채널 9)](http://channel9.msdn.com/Blogs/Windows-Azure/Azure-Preview-portal)(영문) 

[Microsoft Azure 웹 사이트 설명서](http://azure.microsoft.com/documentation/services/web-sites/)


<!-- IMAGES -->
[01Startboard]: ./media/web-sites-gallery-umbraco/01Startboard.PNG
[02WebGallery]: ./media/web-sites-gallery-umbraco/02WebGallery.PNG
[03UmbracoCMS]: ./media/web-sites-gallery-umbraco/03UmbracoCMS.PNG
[04AppSettings]: ./media/web-sites-gallery-umbraco/04AppSettings.PNG
[05NewServer]: ./media/web-sites-gallery-umbraco/05NewServer.PNG
[06UmbracoCMSGroup]: ./media/web-sites-gallery-umbraco/06UmbracoCMSGroup.PNG
[07UmbracoCMSGroupBlade]: ./media/web-sites-gallery-umbraco/07UmbracoCMSGroupBlade.PNG
[08UmbracoCMSGroupRunning]: ./media/web-sites-gallery-umbraco/08UmbracoCMSGroupRunning.PNG
[09InstallUmbraco7]: ./media/web-sites-gallery-umbraco/09InstallUmbraco7.png
[10ConfigureYourDatabase]: ./media/web-sites-gallery-umbraco/10ConfigureYourDatabase.png
[11InstallAStarterWebsite]: ./media/web-sites-gallery-umbraco/11InstallAStarterWebsite.png
[12ConfigureYourDatabase]: ./media/web-sites-gallery-umbraco/12ConfigureYourDatabase.png
[14FriendlyCMS]: ./media/web-sites-gallery-umbraco/14FriendlyCMS.PNG
[15CreateItemUnderOverflow]: ./media/web-sites-gallery-umbraco/15CreateItemUnderOverflow.PNG
[16EnterAName]: ./media/web-sites-gallery-umbraco/16EnterAName.PNG
[17MyPage]: ./media/web-sites-gallery-umbraco/17MyPage.PNG

<!--HONumber=46--> 
