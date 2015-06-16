<properties 
	pageTitle="Microsoft Azure의 마켓플레이스에서 Umbraco 웹 앱 만들기" 
	description="Umbraco 콘텐츠 관리 시스템을 만들고 Azure 앱 서비스 웹 앱에 배포합니다." 
	tags="azure-portal"
	services="app-service\web" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/21/2015" 
	ms.author="tomfitz"/>

#Microsoft Azure의 마켓플레이스에서 Umbraco 웹 앱 만들기#

Umbraco CMS는 소규모 응용 프로그램에서 복잡한 응용 프로그램에 이르기까지 다양한 응용 프로그램을 만드는 데 사용할 수 있는 전기능 오픈 소스 콘텐츠 관리 시스템입니다. Azure 마켓플레이스는 Microsoft, 타사 및 오픈 소스 소프트웨어에서 개발된 다양한 인기 웹 응용 프로그램을 제공합니다. 이 갤러리에서 시작 키트를 적용하거나 사용자 자신의 디자인을 통합하여 단 몇 분 만에 Umbraco CMS 앱을 만들 수 있습니다.

이 문서에서는 리소스 관리를 획기적으로 간소화하는 Azure 미리 보기 포털에 대해 설명합니다. 이 Azure 미리 보기 포털은 단일 작업 영역에서 Microsoft 및 해당 파트너의 플랫폼 간 도구, 기술 및 서비스를 배치하여 소프트웨어 전달 프로세스를 가속화하도록 디자인되었습니다. [Azure 앱 서비스](http://go.microsoft.com/fwlink/?LinkId=529714) 웹 앱, Visual Studio 프로젝트 또는 데이터베이스와 같은 독립 실행형 리소스를 사용하는 대신, 전체 응용 프로그램을 단일 리소스 그룹으로 만들고 관리하고 분석할 수 있습니다.

이 자습서에서는 다음에 대해 알아봅니다.

- Azure 미리 보기 포털을 사용하여 마켓플레이스를 통해 새 웹 앱을 만드는 방법
- Umbraco CMS를 사용하여 블로그 웹 사이트를 빌드하는 방법 

##Azure 미리 보기 포털을 사용하여 마켓플레이스에서 웹 앱 만들기

1. [Azure Preview 포털](https://portal.azure.com/)에 로그인합니다.

2. **마켓플레이스** 아이콘을 선택합니다.
	
	![웹 갤러리 선택][01Startboard]
	
3. **마켓플레이스**에서 **웹 앱** 탭을 선택한 다음 **Umbraco CMS**를 선택합니다.
	
	![웹 갤러리에서 Umbraco 선택][02WebGallery]
	
4. 새 Umbraco CMS 웹 앱을 만들려면 **만들기**를 클릭합니다.
	
	![만들기 클릭][03UmbracoCMS]
	
5. 다음 단계는 Umbraco CMS와 연결된 모든 리소스를 구성하는 것입니다. 이 경우 리소스는 웹 앱과 SQL Server 데이터베이스입니다. 먼저 **웹 앱**을 선택하여 **URL**, **앱 서비스 계획**, **웹 앱 설정** 및 **위치**와 같은 웹 앱 설정을 구성합니다.
	
	![리소스 구성][04AppSettings]
	
6. 이제 데이터베이스를 구성합니다. **데이터베이스**를 선택한 다음 **서버**를 선택합니다. 이 예에서는 Azure의 데이터베이스에 대한 SQL Server를 만듭니다.
	
	![Azure에서 SQL Server 만들기][05NewServer]
	
7. 이제 웹 앱과 데이터베이스를 구성했으므로 이전 이미지의 첫 번째 **Umbraco CMS** 블레이드 아래쪽에 있는 **만들기**를 클릭하여 응용 프로그램 배포를 시작할 수 있습니다.
	
	![만들기 클릭][06UmbracoCMSGroup]
	
배포가 완료된 후 Umbraco CMS 웹 앱의 리소스 그룹에 대한 블레이드는 포털에 표시됩니다. **요약** 섹션에서 웹 앱의 속성을 보려면 웹 앱 이름을 클릭합니다. 또한 **요약** 섹션에서 데이터베이스 리소스를 선택하여 연결된 데이터베이스의 속성을 확인할 수 있습니다.
	
![][07UmbracoCMSGroupBlade]

## Umbraco CMS 웹 앱 시작 및 구성 ##

1. 웹 앱에 대한 세부 정보 블레이드에서 **찾아보기**를 클릭하여 웹 앱을 찾아볼 수 있습니다.
	
	![사이트 찾아보기][08UmbracoCMSGroupRunning]
	
2. 웹 앱을 찾으면 Umbraco CMS의 설치 관리자 마법사를 시작합니다. 요청된 정보를 입력한 후 **사용자 지정**을 클릭합니다.
	
	![Umbraco 설치 마법사][09InstallUmbraco7]
	
3. Umbraco에서 사용될 데이터베이스에 대한 연결 및 인증 세부 정보를 입력합니다. 데이터베이스 유형에 대한 **Microsoft SQL Azure**를 선택합니다. 웹 앱의 **사이트 설정** 섹션에서 데이터베이스에 대한 연결 문자열을 얻을 수 있습니다.
	
	![데이터베이스 구성][10ConfigureYourDatabase]
	
4. Umbraco CMS를 처음 사용하는 경우에는 시작 웹 사이트 키트를 선택할 수 있습니다. 그렇지 않은 경우 **No thanks, I do not want to install a starter website**를 클릭합니다.
	
	![시작 웹 사이트 시작][11InstallAStarterWebsite]
	
5. Umbraco 설치 관리자는 응용 프로그램에 대한 설치를 완료합니다. 응용 프로그램이 구성되면 Umbraco CMS 관리 대시보드로 리디렉션됩니다.
	
	![Umbraco CMS 대시보드][14FriendlyCMS]
	
6. 이제 게시할 샘플 텍스트 페이지가 만들어집니다. **Content**, **Overflow** 및 **TextPage**를 차례로 선택합니다.
	
	![텍스트 페이지 만들기][15CreateItemUnderOverflow]
	
7. 아래 표시된 것처럼 텍스트 페이지의 제목과 일부 콘텐츠를 입력합니다. 완료되면 **저장 및 게시**를 클릭합니다.
	
	![제목 및 일부 콘텐츠 입력][16EnterAName]
	
8. 페이지가 게시되는 동안 잠시 기다리세요. 게시가 끝나면 화면 오른쪽 아래에 작은 경고가 표시됩니다. 이제 웹 앱에서 새 콘텐츠를 찾아볼 수 있습니다.
	
	![게시된 웹 사이트 페이지][17MyPage]
	

끝났습니다. 단 몇 분 만에 Umbraco CMS를 사용하여 블로그 웹 앱을 만드는 데 성공했습니다.

##추가 리소스

[Umbraco 설명서(영문)](http://our.umbraco.org/documentation)

[Umbraco 비디오 자습서](https://umbraco.com/help-and-support/video-tutorials.aspx)

[Azure 포털 문서](../preview-portal.md)

[Azure 포털(채널 9)](http://channel9.msdn.com/Blogs/Windows-Azure/Azure-Preview-portal)

[Azure 앱 서비스 웹 앱 설명서](/documentation/services/websites/)

## 변경된 내용
* 웹 사이트에서 앱 서비스로의 변경에 대한 지침은 [Azure 앱 서비스와 이 서비스가 기존 Azure 서비스에 미치는 영향](http://go.microsoft.com/fwlink/?LinkId=529714)을 참조하세요.
* 포털에서 미리 보기 포털로의 변경에 대한 지침은 [미리 보기 포털 탐색에 대한 참조](http://go.microsoft.com/fwlink/?LinkId=529715)를 참조하세요.

>[AZURE.NOTE]Azure 계정을 등록하기 전에 Azure 앱 서비스를 시작하려면 [앱 서비스 평가](http://go.microsoft.com/fwlink/?LinkId=523751)로 이동합니다. 앱 서비스에서 단기 스타터 웹 앱을 즉시 만들 수 있습니다. 신용 카드는 필요하지 않으며 약정도 필요하지 않습니다.


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

<!--HONumber=54--> 