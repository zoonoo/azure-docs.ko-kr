<properties 
	pageTitle="Azure 마켓플레이스에서 Orchard CMS 웹 앱 만들기" 
	description="Azure에서 새 웹 앱을 만드는 방법에 대해 설명하는 자습서입니다. Azure 포털을 사용하여 웹 앱을 시작하고 관리하는 방법도 알아봅니다." 
	tags="azure-portal"
	services="app-service\web" 
	documentationCenter=".net" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="04/29/2015" 
	ms.author="tomfitz"/>

# Azure 마켓플레이스에서 Orchard CMS 웹 앱 만들기

## 개요

마켓플레이스는 Microsoft, 타사 및 오픈 소스 소프트웨어에서 개발된 다양한 인기 웹 응용 프로그램을 사용할 수 있습니다. 마켓플레이스에서 만든 웹 응용 프로그램은 [Azure Preview 포털](http://go.microsoft.com/fwlink/?LinkId=529715)에 연결하는 데 사용되는 브라우저 외에 다른 소프트웨어를 설치하지 않아도 됩니다. 마켓플레이스의 웹 응용 프로그램에 대한 자세한 내용은 [Azure 마켓플레이스](/marketplace/)를 참조하세요.

이 자습서에서는 다음에 대해 알아봅니다.

- 마켓플레이스를 통해 새 웹 앱을 만드는 방법

- Azure 포털에서 웹 앱을 시작하고 관리하는 방법
 
기본 템플릿을 사용하는 Orchard CMS 웹 앱을 빌드해 봅니다. [Orchard](http://www.orchardproject.net/)는 사용자 지정된 콘텐츠 중심의 웹 앱 및 웹 사이트를 만들 수 있도록 하는 오픈 소스의 무료 .Net 기반 CMS 응용 프로그램입니다. Orchard CMS에는 [추가 모듈 및 테마를 다운로드](http://gallery.orchardproject.net/)하여 웹 앱을 사용자 지정할 수 있는 확장성 프레임워크가 포함되어 있습니다. 다음 그림은 앞으로 만들 [Azure 앱 서비스](http://go.microsoft.com/fwlink/?LinkId=529714)의 Orchard CMS 웹 앱을 보여 줍니다.

![Orchard 블로그][13]

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

>[AZURE.NOTE]Azure 계정을 등록하기 전에 Azure 앱 서비스를 시작하려면 [앱 서비스 평가](http://go.microsoft.com/fwlink/?LinkId=523751)로 이동합니다. 앱 서비스에서 단기 스타터 웹 앱을 즉시 만들 수 있습니다. 신용 카드는 필요하지 않으며 약정도 필요하지 않습니다.

## 마켓플레이스에서 Orchard 웹 앱 만들기

1. [Azure Preview 포털](http://portal.azure.com)에 로그인합니다.

2. **새로 만들기** > **웹 + 모바일** > **Azure 마켓플레이스**를 클릭합니다.
	
	![새로 만들기][1]

3. **웹 앱** > **Orchard CMS**를 클릭합니다. 다음 블레이드에서 **Create**를 클릭합니다.
	
	![마켓플레이스에서 만들기][2]

4. 또한 웹 앱의 URL, 앱 서비스 계획, 리소스 그룹 및 지역을 구성합니다. 완료하면 **만들기**를 클릭합니다.
	
	![앱 구성][3]

	웹 앱을 만든 후 **알림** 단추는 녹색의 "성공"을 표시하며 웹 앱의 블레이드가 표시 됩니다.

## Orchard 웹 앱 시작 및 관리

1. 웹 앱의 블레이드에서 **찾아보기**를 클릭하여 웹 앱의 시작 페이지를 엽니다.

	![찾아보기 단추][12]

2. Orchard에 필요한 구성 정보를 입력한 후 **Finish Setup**을 클릭하여 구성을 완료하고 웹 앱의 홈페이지를 엽니다.

	![Orchard에 로그인][7]

	새 Orchard 웹 앱은 아래 스크린샷과 유사합니다.

	![사용자의 Orchard 웹 앱][13]

3. [Orchard 문서](http://docs.orchardproject.net/)(영문)의 세부 정보를 따라서 Orchard에 대해 자세히 알아보고 새 웹 앱을 구성합니다.

## 다음 단계

* [Microsoft WebMatrix로 웹 앱 개발 및 배포](web-sites-dotnet-using-webmatrix.md) -- WebMatrix에서 앱 서비스 웹 앱을 편집하는 방법을 알아봅니다. 
* [인증 및 SQL DB을 사용하여 ASP.NET MVC 앱을 만들고 Azure 앱 서비스 배포](web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md)-- Visual Studio에서 Azure 앱 서비스에 새 웹 앱을 만드는 방법을 설명합니다.

## 변경된 내용
* 웹 사이트에서 앱 서비스로의 변경에 대한 지침은 [Azure 앱 서비스와 이 서비스가 기존 Azure 서비스에 미치는 영향](http://go.microsoft.com/fwlink/?LinkId=529714)을 참조하세요.
* 포털에서 미리 보기 포털로의 변경에 대한 지침은 [미리 보기 포털 탐색에 대한 참조](http://go.microsoft.com/fwlink/?LinkId=529715)를 참조하세요.

[1]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-01.png
[2]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-02.png
[3]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-03.png
[4]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-04.png
[5]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-05.png
[7]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-07.png
[12]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-12.png
[13]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-08.png



<!--HONumber=54--> 