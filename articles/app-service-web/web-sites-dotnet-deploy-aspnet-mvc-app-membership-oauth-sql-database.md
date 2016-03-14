<properties 
	pageTitle="인증 및 SQL DB를 사용하여 ASP.NET MVC 앱을 만들고 Azure 앱 서비스에 배포" 
	description="SQL 데이터베이스 백엔드로 ASP.NET MVC 5 앱을 개발하고, 인증 및 권한 부여를 추가하고 Azure에 배포하는 방법에 알아봅니다." 
	services="app-service\web" 
	documentationCenter=".net" 
	authors="Rick-Anderson" 
	writer="Rick-Anderson" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="12/07/2015" 
	ms.author="riande"/>

# 인증 및 SQL DB를 사용하여 ASP.NET MVC 앱을 만들고 Azure 앱 서비스에 배포

이 자습서는 사용자가 Facebook 또는 Google 자격 증명을 사용하여 로그인할 수 있는 보안 ASP.NET MVC 5 웹앱을 빌드하는 방법을 보여 줍니다. 이 앱은 데이터베이스 액세스를 위해 ADO.NET Entity Framework를 사용하는 간단한 연락처 목록입니다. [Azure 앱 서비스](http://go.microsoft.com/fwlink/?LinkId=529714)에 앱을 배포합니다.

이 자습서를 완료하면 클라우드에서 클라우드 데이터베이스를 사용하는 보안 데이터 기반 웹 응용 프로그램을 실행할 수 있습니다. 다음 그림은 완성된 응용 프로그램에 대한 로그인 페이지를 보여 줍니다.

![로그인 페이지][rxb]

다음 내용을 배웁니다.

* Visual Studio에서 보안 ASP.NET MVC 5 웹 프로젝트를 만드는 방법
* Google 또는 Facebook 계정 자격 증명을 사용하여 로그온하는 사용자를 인증하고 권한을 부여([OAuth 2.0](http://oauth.net/2 "http://oauth.net/2")을 사용한 소셜 공급자 인증)하는 방법
* 응용 프로그램에서 관리되는 데이터베이스에 등록하는 사용자를 인증하고 권한을 부여([ASP.NET Identity](http://asp.net/identity/) 사용한 로컬 인증)하는 방법
* ADO.NET Entity Framework 6 Code First를 사용하여 SQL 데이터베이스에서 데이터를 읽고 쓰는 방법
* Entity Framework Code First 마이그레이션을 사용하여 데이터베이스를 배포하는 방법
* Azure SQL 데이터베이스를 사용하여 클라우드에서 관계형 데이터를 저장하는 방법
* Azure 앱 서비스에서 데이터베이스를 사용하는 웹 프로젝트를 [웹앱](http://go.microsoft.com/fwlink/?LinkId=529714)에 배포하는 방법

>[AZURE.NOTE] 긴 자습서입니다. Azure 앱 서비스 및 Visual Studio 웹 프로젝트에 대한 간략한 소개를 보려면 [Azure 앱 서비스에서 ASP.NET 웹앱 만들기](web-sites-dotnet-get-started.md)를 참조하세요. 문제 해결 정보는 [문제 해결](#troubleshooting) 섹션을 참조하세요.
>
>Azure 계정을 등록하기 전에 Azure 앱 서비스를 시작하려면 [앱 서비스 평가](http://go.microsoft.com/fwlink/?LinkId=523751)로 이동합니다. 앱 서비스에서 단기 스타터 웹앱을 즉시 만들 수 있습니다. 신용 카드는 필요하지 않으며 약정도 필요하지 않습니다.

## 필수 조건

이 자습서를 완료하려면 Microsoft Azure 계정이 필요합니다. 계정이 없는 경우 [Visual Studio 구독자 혜택을 활성화](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)하거나 [무료 평가판을 등록](/pricing/free-trial/?WT.mc_id=A261C142F)할 수 있습니다.

개발 환경 설정을 설정하려면 [Visual Studio 2013 업데이트 5](http://go.microsoft.com/fwlink/?LinkId=390521) 이상 및 최신 버전의 [.NET용 Azure SDK](http://go.microsoft.com/fwlink/?linkid=324322&clcid=0x409)를 설치해야 합니다. 이 문서는 Visual Studio 업데이트 4 및 SDK 2.8.1에 대해 작성되었습니다. Visual Studio 2015는 설치된 최신 [.NET용 Azure SDK](http://go.microsoft.com/fwlink/?linkid=518003&clcid=0x409)와 동일한 지침으로 작동하지만 일부 화면이 그림과 다르게 표시됩니다.

## ASP.NET MVC 5 응용 프로그램 만들기

### 프로젝트 만들기

1. **파일** 메뉴에서 **새 프로젝트**를 클릭합니다.

	![파일 메뉴의 새 프로젝트](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/gs13newproj.png)

1. **새 프로젝트** 대화 상자에서 **C#**을 확장하고 **설치된 템플릿** 아래의 **웹**을 선택한 다음 **ASP.NET 웹 응용 프로그램**을 선택합니다.

1. 응용 프로그램 이름을 **ContactManager**로 지정한 다음 **확인**을 클릭합니다.

	![새 프로젝트 대화 상자](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/GS13newprojdb.png)
 
	**참고:** "ContactManager"를 입력해야 합니다. 나중에 복사할 코드 블록에서는 프로젝트 이름을 ContactManager로 가정합니다.

1. **새 ASP.NET 프로젝트** 대화 상자에서 **MVC** 템플릿을 선택합니다. **인증**이 **개별 사용자 계정**으로 설정되고, **Host in the cloud(클라우드에서 호스트)**가 선택되고 **앱 서비스**가 선택되었는지 확인합니다.

	![새 ASP.NET 프로젝트 대화 상자](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/newproject.png)

1. **확인**을 클릭합니다.

3. **Microsoft Azure 웹앱 설정 구성** 대화 상자가 나타나면 Azure에 로그인했는지 확인합니다. 아직 로그인하지 않았다면 로그인하거나 로그인이 만료된 경우 자격 증명을 다시 입력합니다.

	![자격 증명 다시 입력](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/reentercredentials.png)

2. 웹앱에 대한 이름을 지정하려는 경우 **웹앱 이름** 상자의 값을 변경합니다.

	웹앱의 URL이 {name}.azurewebsites.net이 되기 때문에 이름은 azurewebsites.net 도메인에서 고유해야 합니다. 구성 마법사에서 프로젝트 이름 "ContactManager"에 숫자를 추가하여 고유 이름을 제안하고 이 자습서에 적합합니다.

5. **앱 서비스 계획** 드롭다운에서 **새 앱 서비스 계획 만들기**를 선택하고 그림에 표시된 것처럼 "StandardWeb"과 같은 이름을 입력합니다.

	원하는 경우 이미 가지고 있는 앱 서비스 계획을 선택할 수 있습니다. 앱 서비스 계획에 대한 자세한 내용은 [Azure 앱 서비스 계획의 포괄 개요](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)를 참조하세요.

5. **리소스 그룹** 드롭다운에서 **새 리소스 그룹 만들기**를 선택하고 그림에 표시된 것처럼 "ExampleMVC"와 같은 이름을 입력합니다.

	원하는 경우 이미 가지고 있는 리소스 그룹을 선택할 수 있습니다. 하지만 새 리소스 그룹을 만들고 이 자습서에만 사용하는 경우 사용하고 난 후 자습서에 대해 만든 모든 Azure 리소스를 삭제하는 것이 쉽습니다. 리소스 그룹에 대한 자세한 내용은 [Azure 리소스 관리자 개요](../resource-group-overview.md)를 참조하세요.

7. 근처에 있는 지역을 선택합니다.

	아직 **확인**을 클릭하지 마세요. 다음 단계에서는 데이터베이스 리소스를 구성합니다. 대화 상자는 이제 다음 그림과 같이 나타납니다.

	![새 계획 및 리소스 그룹](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/newplanandgroup.png)
 
2. **새 서버 만들기**를 선택하고 서버 이름, 사용자 이름 및 암호를 입력합니다.

	서버 이름은 고유해야 합니다. 소문자, 숫자 및 하이픈을 포함할 수 있습니다. 후행 하이픈을 포함할 수 없습니다. 사용자 이름 및 암호는 새 서버에 대해 만드는 새 자격 증명입니다.

	데이터베이스 서버가 이미 있는 경우 새로 만드는 대신 해당 서버를 선택할 수 있습니다. 데이터베이스 서버는 중요한 리소스이며, 일반적으로 데이터베이스별로 데이터베이스 서버를 만들기보다는 테스트 및 개발을 위해 같은 서버에서 여러 데이터베이스 만들려고 합니다. 그러나 이 자습서의 경우 서버는 임시적으로 필요하므로 웹 사이트와 동일한 리소스 그룹의 서버를 생성하여 자습서를 완료했을 때 리소스 그룹을 삭제하여 웹앱 및 데이터베이스 리소스를 삭제하기 쉽습니다.

	기존 데이터베이스 서버를 선택하는 경우 웹앱 및 데이터베이스가 동일한 지역에 있어야 합니다.

	![새 데이터베이스 사용](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/newdb.png)

4. **확인**을 클릭합니다.

	Visual Studio는 ContactManager 웹 프로젝트를 만들고 지정한 리소스 그룹 및 앱 서비스 계획을 만들고 지정한 이름을 가진 Azure 앱 서비스에서 웹앱을 만듭니다.

### 페이지 머리글 및 바닥글 설정

1. **Solution Explorer**에서 *Views\\Shared* 폴더의 *Layout.cshtml* 파일을 엽니다.

	![솔루션 탐색기의 \_Layout.cshtml][newapp004]

1. *Layout.cshtml* 파일 내용을 다음 코드로 바꿉니다.

		<!DOCTYPE html>
		<html>
		<head>
		    <meta charset="utf-8" />
		    <meta name="viewport" content="width=device-width, initial-scale=1.0">
		    <title>@ViewBag.Title - Contact Manager</title>
		    @Styles.Render("~/Content/css")
		    @Scripts.Render("~/bundles/modernizr")
		
		</head>
		<body>
		    <div class="navbar navbar-inverse navbar-fixed-top">
		        <div class="container">
		            <div class="navbar-header">
		                <button type="button" class="navbar-toggle" data-toggle="collapse" data-target=".navbar-collapse">
		                    <span class="icon-bar"></span>
		                    <span class="icon-bar"></span>
		                    <span class="icon-bar"></span>
		                </button>
		                @Html.ActionLink("CM Demo", "Index", "Cm", new { area = "" }, new { @class = "navbar-brand" })
		            </div>
		            <div class="navbar-collapse collapse">
		                <ul class="nav navbar-nav">
		                    <li>@Html.ActionLink("Home", "Index", "Home")</li>
		                    <li>@Html.ActionLink("About", "About", "Home")</li>
		                    <li>@Html.ActionLink("Contact", "Contact", "Home")</li>
		                </ul>
		                @Html.Partial("_LoginPartial")
		            </div>
		        </div>
		    </div>
		    <div class="container body-content">
		        @RenderBody()
		        <hr />
		        <footer>
		            <p>&copy; @DateTime.Now.Year - Contact Manager</p>
		        </footer>
		    </div>
		
		    @Scripts.Render("~/bundles/jquery")
		    @Scripts.Render("~/bundles/bootstrap")
		    @RenderSection("scripts", required: false)
		</body>
		</html>

	이 코드는 "My ASP.NET Application" 및 "Application name"에서 "Contact Manager" 및 "CM Demo"로 머리글 및 바닥글의 응용 프로그램 이름을 변경합니다.
 
### 로컬에서 응용 프로그램 실행

1. Ctrl+F5를 눌러 앱을 실행합니다.

	응용 프로그램 홈페이지가 기본 브라우저에 나타납니다.

	![로컬로 실행되는 웹앱](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rr2.png)

Azure에 배포할 응용 프로그램을 만들기 위해 지금 수행해야 하는 작업은 이것뿐입니다.

## Azure에 응용 프로그램 배포

1. Visual Studio의 **솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 상황에 맞는 메뉴에서 **게시**를 선택합니다.

	![프로젝트 상황에 맞는 메뉴의 게시](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/GS13publish.png)
	
	**웹 게시** 마법사가 열립니다.

1. **웹 게시** 대화 상자에서 **게시**를 클릭합니다.

	![게시](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rr3.png)

	만든 응용 프로그램이 이제 클라우드에서 실행되고 있습니다. 다음에 응용 프로그램을 배포할 때는 변경된(또는 새) 파일만 배포됩니다.

	![클라우드에서 실행 중](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss4.PNG)

## 프로젝트에 SSL 사용 ##

1. **솔루션 탐색기**에서 **ContactManager** 프로젝트를 클릭한 다음 F4 키를 클릭하여 **속성** 창을 엽니다.

3. **SSL 사용**을 **True**로 변경합니다.

4. **SSL URL**을 복사합니다.

	이전에 SSL 웹앱을 만들지 않은 경우 SSL URL은 https://localhost:44300/입니다.

	![SSL 사용][rxSSL]
 
1. **솔루션 탐색기**에서 **Contact Manager** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **속성**을 클릭합니다.

1. **웹** 탭을 클릭합니다.

1. **프로젝트 URL**을 변경하여 **SSL URL**을 사용하고 페이지를 저장합니다(Ctrl S).

	![SSL 사용](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rrr1.png)
 
1. 다음 이미지와 같이 Internet Explorer가 Visual Studio가 시작되는 브라우저인지 확인합니다.

	![기본 브라우저](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss12.PNG)

	브라우저 선택기에서 Visual Studio가 시작되는 브라우저를 지정할 수 있습니다. 여러 개의 브라우저를 선택할 수 있으며, 변경 사항이 있을 경우 Visual Studio에서 각 브라우저를 업데이트하도록 할 수 있습니다. 자세한 내용은 [Visual Studio 2013에서 브라우저 링크 사용](http://www.asp.net/visual-studio/overview/2013/using-browser-link)(영문)을 참조하세요.

 	![브라우저 선택기](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss13.png)

1. Ctrl+F5를 눌러 응용 프로그램을 실행합니다. **예**를 클릭하여 IIS Express에서 생성한 자체 서명된 인증서 신뢰 프로세스를 시작합니다.

	 ![IIS Express에서 생성한 자체 서명된 인증서를 신뢰하는 지침](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss26.PNG)

1. **보안 경고** 대화 상자를 읽고 **localhost**를 나타내는 인증서를 설치하려면 **예**를 클릭합니다.

 	![localhost IIS Express 인증서 경고](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss27.PNG)

1. IE에 *홈* 페이지가 표시되며 SSL 경고가 없습니다.

	 ![localhost SSL이 표시되고 경고가 없는 IE](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss28.PNG)

	 SSL은 경고 없이 인증서를 수락하고 HTTPS 콘텐츠를 표시하기 때문에 SSL을 사용하는 경우 Internet Explorer가 좋습니다. Microsoft Edge 및 Google Chrome도 인증서를 승인합니다. Firefox는 자체 인증서 저장소를 사용하므로 경고가 표시됩니다.

	 ![FireFox 인증서 경고](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss30.PNG)

## 응용 프로그램에 데이터베이스 추가

이제 앱을 업데이트하여 연락처를 표시 및 업데이트하고 데이터베이스에 데이터를 저장하는 기능을 추가하겠습니다. 앱은 EF(Entity Framework)를 사용하여 데이터베이스를 만들며 데이터를 읽고 업데이트합니다.

### 연락처에 대한 데이터 모델 클래스 추가

먼저 코드로 간단한 데이터 모델을 만듭니다.

1. **솔루션 탐색기**에서 모델 폴더를 마우스 오른쪽 단추로 클릭하고 **추가**를 클릭한 후 **클래스**를 클릭합니다.

	![모델 폴더 상황에 맞는 메뉴의 클래스 추가](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rr5.png)

2. **새 항목 추가** 대화 상자에서 새 클래스 파일의 이름을 *Contact.cs*로 지정하고 **추가**를 클릭합니다.

	![새 항목 추가 대화 상자][adddb002]

3. Contact.cs 파일 내용을 다음 코드로 바꿉니다.

        using System.ComponentModel.DataAnnotations;
        using System.Globalization;
        namespace ContactManager.Models
        {
            public class Contact
            {
                public int ContactId { get; set; }
                public string Name { get; set; }
                public string Address { get; set; }
                public string City { get; set; }
                public string State { get; set; }
                public string Zip { get; set; }
                [DataType(DataType.EmailAddress)]
                public string Email { get; set; }
            }
        }
**Contact** 클래스는 각 연락처에 대해 저장할 데이터와 데이터베이스에 필요한 기본 키 *ContactID*를 정의합니다.

### 앱 사용자가 연락처 작업을 수행할 수 있는 웹 페이지 만들기

ASP.NET MVC 스캐폴딩 기능은 CRUD(만들기, 읽기, 업데이트 및 삭제) 작업을 수행하는 코드를 자동으로 생성할 수 있습니다.

1. 프로젝트를 빌드합니다**(Ctrl+Shift+B)**. 스캐폴딩 메커니즘을 사용하기 전에 프로젝트를 빌드해야 합니다.
 
1. **솔루션 탐색기**에서 컨트롤러 폴더를 마우스 오른쪽 단추로 클릭하고 **추가**를 클릭한 후 **컨트롤러**를 클릭합니다.

	![컨트롤러 폴더 상황에 맞는 메뉴의 컨트롤러 추가][addcode001]

5. **스캐폴드 추가** 대화 상자에서 **MVC 5 컨트롤러(뷰 포함), EF 사용**을 선택하고 **추가**를 클릭합니다.
	
	![스캐폴드 추가 대화 상자](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rr6.png)

1. **모델 클래스** 드롭다운 상자에서 **Contact(ContactManager.Models)**를 선택합니다. 아래 이미지를 참조하세요.

1. **데이터 컨텍스트 클래스**에서 **ApplicationDbContext(ContactManager.Models)**를 선택합니다. **ApplicationDbContext**는 멤버 자격 DB 및 연락처 데이터 둘 다에 사용됩니다.

1. **컨트롤러 이름** 입력란에 컨트롤러 이름으로 "CmController"를 입력합니다.

	![새 데이터 CTX 대화 상자](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss5.PNG)

1. **추가**를 클릭합니다.

   Visual Studio에서 **Contact** 개체에 대한 CRUD 데이터베이스 작업의 컨트롤러 메서드와 뷰를 만듭니다.

## 마이그레이션 사용, 데이터베이스 만들기, 샘플 데이터 및 데이터 이니셜라이저 추가 ##

다음 작업은 만든 데이터 모델에 따라 데이터베이스 테이블을 만들기 위해 [Code First 마이그레이션](http://msdn.microsoft.com/library/hh770484.aspx)(영문) 기능을 사용하도록 설정하는 것입니다.

1. **도구** 메뉴에서 **NuGet 패키지 관리자**, **패키지 관리자 콘솔**을 차례로 선택합니다.

	![도구 메뉴의 패키지 관리자 콘솔](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/SS6.png)

2. **패키지 관리자 콘솔** 창에서 다음 명령을 입력합니다.

		enable-migrations

	**enable-migrations** 명령은 *Migrations* 폴더를 만들고 해당 폴더에 *Configuration.cs* 파일을 넣습니다. 이 파일을 편집하여 데이터베이스를 시드하고 마이그레이션을 구성할 수 있습니다.

2. **패키지 관리자 콘솔** 창에서 다음 명령을 입력합니다.

		add-migration Initial


	**add-migration Initial** 명령은 *Migrations* 폴더에서 **&lt;date\_stamp&gt;Initial** 파일을 생성합니다. 이 파일의 코드는 데이터베이스 테이블을 만듭니다. 첫 번째 매개변수(**Initial**)는 파일의 이름을 만드는 데 사용됩니다. **솔루션 탐색기**에서 새 클래스 파일을 볼 수 있습니다.

	**Initial** 클래스의 **Up** 메서드는 Contacts 테이블을 만들고 이전 상태로 돌아가려는 경우 사용되는 **Down** 메서드는 테이블을 삭제합니다.

3. *Migrations\\Configuration.cs* 파일을 엽니다.

4. 다음 `using` 문을 추가합니다.

    	 using ContactManager.Models;

5. *Seed* 메서드를 다음 코드로 바꿉니다.

        protected override void Seed(ContactManager.Models.ApplicationDbContext context)
        {
            context.Contacts.AddOrUpdate(p => p.Name,
               new Contact
               {
                   Name = "Debra Garcia",
                   Address = "1234 Main St",
                   City = "Redmond",
                   State = "WA",
                   Zip = "10999",
                   Email = "debra@example.com",
               },
                new Contact
                {
                    Name = "Thorsten Weinrich",
                    Address = "5678 1st Ave W",
                    City = "Redmond",
                    State = "WA",
                    Zip = "10999",
                    Email = "thorsten@example.com",
                },
                new Contact
                {
                    Name = "Yuhong Li",
                    Address = "9012 State st",
                    City = "Redmond",
                    State = "WA",
                    Zip = "10999",
                    Email = "yuhong@example.com",
                },
                new Contact
                {
                    Name = "Jon Orton",
                    Address = "3456 Maple St",
                    City = "Redmond",
                    State = "WA",
                    Zip = "10999",
                    Email = "jon@example.com",
                },
                new Contact
                {
                    Name = "Diliana Alexieva-Bosseva",
                    Address = "7890 2nd Ave E",
                    City = "Redmond",
                    State = "WA",
                    Zip = "10999",
                    Email = "diliana@example.com",
                }
                );
        }

	이 코드는 연락처 정보를 사용하여 데이터베이스를 초기화(시드)합니다. 데이터베이스 시드에 대한 자세한 내용은 [EF(Entity Framework) DB 시드 및 디버그](http://blogs.msdn.com/b/rickandy/archive/2013/02/12/seeding-and-debugging-entity-framework-ef-dbs.aspx)(영문)를 참조하십시오.

6. **패키지 관리자 콘솔**에서 다음 명령을 입력합니다.

		update-database

	![패키지 관리자 콘솔 명령][addcode009]

	**update-database**는 데이터베이스를 만드는 첫 번째 마이그레이션을 실행합니다. 기본적으로 데이터베이스는 SQL Server Express LocalDB 데이터베이스로 생성됩니다.

7. Ctrl+F5를 눌러 응용 프로그램을 실행한 다음 **CM Demo** 링크를 클릭하거나 https://localhost:(port#)/Cm으로 이동합니다.

	응용 프로그램에서 시드 데이터를 표시하고 편집, 세부 정보 및 삭제 링크를 제공합니다. 데이터를 만들고, 편집, 삭제 및 표시할 수 있습니다.

	![MVC 데이터 뷰][rx2]

## OAuth2 공급자 추가

>[AZURE.NOTE] Google 및 Facebook 개발자 포털 사이트 사용 방법에 대한 자세한 지침의 경우 이 자습서는 ASP.NET 사이트의 자습서에 연결되어 있습니다. 그러나 Google 및 Facebook은 자습서가 업데이트되는 것보다 사이트를 자주 변경하고 현재 최신 상태에서 벗어났습니다. 지침을 따르는데 문제가 있는 경우 변경 내용 목록에 대한 이 자습서의 후반부에 있는 주요 Disqus 설명을 참조하세요.

[OAuth](http://oauth.net/ "http://oauth.net/")는 웹, 모바일 및 데스크톱 응용 프로그램에서 간단한 표준 메서드로 보안 권한 부여를 허용하는 개방형 프로토콜입니다. ASP.NET MVC 인터넷 템플릿은 OAuth를 사용하여 Facebook, Twitter, Google 및 Microsoft를 인증 공급자로 표시합니다. 이 자습서에서는 인증 공급자로 Google만 사용하지만 이러한 공급자를 사용하도록 코드를 쉽게 수정할 수 있습니다. 다른 공급자를 구현하는 단계도 이 자습서에 표시되는 단계와 매우 비슷합니다. Facebook을 인증 공급자로 사용하려면 [Facebook, Twitter, LinkedIn 및 Google OAuth2 Sign-on을 사용한 MVC 5 앱](http://www.asp.net/mvc/tutorials/mvc-5/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on)(영문)을 참조하세요.

자습서에서는 인증 외에도 역할을 사용하여 권한 부여를 구현합니다. *canEdit* 역할에 추가한 사용자만 데이터를 변경할 수 있습니다(즉, 연락처 만들기, 편집 또는 삭제).

1. [Facebook, Twitter, LinkedIn 및 Google OAuth2 Sign-on을 사용한 MVC 5 앱](http://www.asp.net/mvc/tutorials/mvc-5/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on#goog)(영문)에서 **OAuth 2용 Google 앱을 만들어 OAuth2용 Google 앱 설정**의 지침을 따릅니다.

3. 앱을 실행 및 테스트하여 Google 인증으로 로그온할 수 있는지 확인합니다.

2. 공급자 특정 아이콘을 사용하여 소셜 로그인 단추를 만들려는 경우 [ASP.NET MVC 5의 소셜 로그인 단추](http://www.jerriepelser.com/blog/pretty-social-login-buttons-for-asp-net-mvc-5)(영문)를 참조하세요.

## 멤버 자격 API 사용

이 섹션에서는 멤버 자격 데이터베이스에 로컬 사용자와 *canEdit* 역할을 추가합니다. *canEdit* 역할의 사용자만 데이터를 편집할 수 있습니다. 수행할 수 있는 작업으로 역할의 이름을 지정하는 것이 모범 사례이므로 *admin* 역할보다 *canEdit*를 사용하는 것이 좋습니다. 응용 프로그램이 발전함에 따라 설명이 부족한 *superAdmin*보다 *canDeleteMembers* 등의 새 역할을 추가할 수 있습니다.

1. *migrations\\configuration.cs* 파일을 열고 다음 `using` 문을 추가합니다.

        using Microsoft.AspNet.Identity;
        using Microsoft.AspNet.Identity.EntityFramework;

1. 클래스에 다음과 같은 **AddUserAndRole** 메서드를 추가합니다.

		bool AddUserAndRole(ContactManager.Models.ApplicationDbContext context)
		{
		    IdentityResult ir;
		    var rm = new RoleManager<IdentityRole>
		        (new RoleStore<IdentityRole>(context));
		    ir = rm.Create(new IdentityRole("canEdit"));
		    var um = new UserManager<ApplicationUser>(
		        new UserStore<ApplicationUser>(context));
		    var user = new ApplicationUser()
		    {
		        UserName = "user1@contoso.com",
		    };
		    ir = um.Create(user, "P_assw0rd1");
		    if (ir.Succeeded == false)
		        return ir.Succeeded;
		    ir = um.AddToRole(user.Id, "canEdit");
		    return ir.Succeeded;
		}

1. **Seed** 메서드에서 새 메서드를 호출합니다.

		protected override void Seed(ContactManager.Models.ApplicationDbContext context)
		{
		    AddUserAndRole(context);
		    context.Contacts.AddOrUpdate(p => p.Name,
		        // Code removed for brevity
		}

	다음 이미지에서는 *Seed* 메서드 변경을 보여 줍니다.

	![코드 이미지](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss24.PNG)

	이 코드는 *canEdit* 라는 새 역할을 만들고, 새 로컬 사용자 *user1@contoso.com* 을 만든 다음 *canEdit* 역할에 *user1@contoso.com* 을 추가합니다. 자세한 내용은 ASP.NET 사이트의 [ASP.NET ID 자습서](http://www.asp.net/identity/overview/features-api)를 참조하세요.

## 임시 코드를 사용하여 canEdit 역할에 새 소셜 로그인 사용자 추가  ##

이 섹션에서는 Account 컨트롤러의 **ExternalLoginConfirmation** 메서드를 임시로 수정하여 OAuth 공급자로 등록하는 새 사용자를 *canEdit* 역할에 추가합니다. 앞으로 사용자 계정 및 역할을 만들고 편집할 수 있는 [WSAT](http://msdn.microsoft.com/library/ms228053.aspx)와 유사한 도구를 제공하려고 합니다. 그때까지는 임시 코드를 사용하여 동일한 기능을 수행할 수 있습니다.

1. **Controllers\\AccountController.cs** 파일을 열고 **ExternalLoginConfirmation** 메서드로 이동합니다.

1. **AddToRoleAsync**에서 **SignInAsync** 호출 바로 앞에 다음 호출을 추가합니다.

		await UserManager.AddToRoleAsync(user.Id, "canEdit");

   위의 코드는 새로 등록된 사용자를 "canEdit" 역할에 추가하여 데이터를 변경(편집)하는 작업 메서드에 액세스할 수 있도록 합니다. 다음 코드 조각은 컨텍스트에서 새 코드 줄을 보여 줍니다.

		  // POST: /Account/ExternalLoginConfirmation
		  [HttpPost]
		  [AllowAnonymous]
		  [ValidateAntiForgeryToken]
		  public async Task ExternalLoginConfirmation(ExternalLoginConfirmationViewModel model, string returnUrl)
		  {
		     if (User.Identity.IsAuthenticated)
		     {
		        return RedirectToAction("Index", "Manage");
		     }
		     if (ModelState.IsValid)
		     {
		        // Get the information about the user from the external login provider
		        var info = await AuthenticationManager.GetExternalLoginInfoAsync();
		        if (info == null)
		        {
		           return View("ExternalLoginFailure");
		        }
		        var user = new ApplicationUser { UserName = model.Email, Email = model.Email };
		        var result = await UserManager.CreateAsync(user);
		        if (result.Succeeded)
		        {
		           result = await UserManager.AddLoginAsync(user.Id, info.Login);
		           if (result.Succeeded)
		           {
		              await UserManager.AddToRoleAsync(user.Id, "canEdit");
		              await SignInManager.SignInAsync(user, isPersistent: false, rememberBrowser: false);
		              return RedirectToLocal(returnUrl);
		           }
		        }
		        AddErrors(result);
		     }
		     ViewBag.ReturnUrl = returnUrl;
		     return View(model);
		  }

자습서의 뒷부분에서 응용 프로그램을 Azure에 배포합니다. 여기서 Google 또는 다른 타사 인증 공급자를 사용하여 로그온합니다. 이렇게 하면 새로 등록된 계정이 *canEdit* 역할에 추가됩니다. Google ID가 있고 웹앱의 URL을 찾은 모든 사용자는 데이터베이스를 등록하고 업데이트할 수 있습니다. 다른 사용자가 등록 및 업데이트할 수 없게 하려면 사이트를 중지합니다. 데이터베이스를 검사하여 *canEdit* 역할에 있는 사용자를 확인할 수 있습니다.

**패키지 관리자 콘솔** 창에서 위쪽 화살표 키를 눌러 다음 명령을 표시합니다.

		Update-Database

**데이터베이스 업데이트** 명령은 **시드** 메서드를 실행하고 이전에 추가한 **AddUserAndRole** 메서드를 실행합니다. **AddUserAndRole** 메서드는 사용자 *user1@contoso.com* 을 만들고 *canEdit* 역할에 추가합니다.

## SSL 및 Authorize 특성을 사용하여 응용 프로그램 보호 ##

이 섹션에서는 [Authorize](http://msdn.microsoft.com/library/system.web.mvc.authorizeattribute.aspx) 특성을 적용하여 작업 메서드에 대한 액세스를 제한합니다. 익명 사용자는 home 컨트롤러의 **인덱스** 작업 메서드만 볼 수 있습니다. 등록된 사용자는 연락처 데이터(Cm 컨트롤러의 **인덱스** 및 **세부 정보** 페이지), 정보 페이지 및 연락처 페이지를 볼 수 있습니다. *canEdit* 역할의 사용자만 데이터를 변경하는 작업 메서드에 액세스할 수 있습니다.

1. *App\_Start\\FilterConfig.cs* 파일을 열고 *RegisterGlobalFilters* 메서드를 다음 내용(두 개의 필터 추가)으로 바꿉니다.

		public static void
		RegisterGlobalFilters(GlobalFilterCollection filters)
		{
		    filters.Add(new HandleErrorAttribute());
		    filters.Add(new System.Web.Mvc.AuthorizeAttribute());
		    filters.Add(new RequireHttpsAttribute());
		}
		
	이 코드는 응용 프로그램에 [Authorize](http://msdn.microsoft.com/library/system.web.mvc.authorizeattribute.aspx) 필터와 [RequireHttps](http://msdn.microsoft.com/library/system.web.mvc.requirehttpsattribute.aspx) 필터를 추가합니다. [Authorize](http://msdn.microsoft.com/library/system.web.mvc.authorizeattribute.aspx) 필터는 익명 사용자가 응용 프로그램의 메서드에 액세스할 수 없도록 합니다. [AllowAnonymous](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx) 특성을 사용하여 몇 개 메서드의 권한 부여 요구 사항을 옵트아웃(opt out)하므로 익명 사용자가 로그인하고 홈 페이지를 볼 수 있습니다. [RequireHttps](http://msdn.microsoft.com/library/system.web.mvc.requirehttpsattribute.aspx)는 웹앱에 대한 모든 액세스가 HTTPS를 통해 이루어져야 합니다.

	또 다른 방법은 각 컨트롤러에 [Authorize](http://msdn.microsoft.com/library/system.web.mvc.authorizeattribute.aspx) 특성과 [RequireHttps](http://msdn.microsoft.com/library/system.web.mvc.requirehttpsattribute.aspx) 특성을 추가하는 것이지만 전체 응용 프로그램에 적용하는 것이 보안상 더 좋은 모범 사례입니다. 전체적으로 추가하면 새로 추가된 모든 컨트롤러와 작업 메서드가 자동으로 보호되므로 따로 적용할 필요가 없습니다. 자세한 내용은 [ASP.NET MVC 앱 및 새 AllowAnonymous 특성 보안 유지](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx)(영문)를 참조하십시오.

1. Home 컨트롤러의 [Index](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx) 메서드에 **AllowAnonymous** 특성을 추가합니다. [AllowAnonymous](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx) 특성을 사용하여 권한 부여에서 옵트아웃(opt out)하려는 메서드를 허용 목록에 추가할 수 있습니다.

		public class HomeController : Controller
		{
		  [AllowAnonymous]
		  public ActionResult Index()
		  {
		     return View();
		  }

	*AllowAnonymous* 에 대한 전체 검색을 수행한 경우 Account 컨트롤러의 로그인 및 등록 메서드에서 사용되는 것을 확인하게 됩니다.

1. *CmController.cs* 에서 *Cm* 컨트롤러의 데이터를 변경하는 HttpGet 및 HttpPost 메서드(만들기, 편집, 삭제 그리고 인덱스 및 세부 사항을 제외한 모든 작업 메서드)에 `[Authorize(Roles = "canEdit")]`를 추가합니다. 아래에는 완성된 코드의 일부가 표시되어 있습니다. 

		// GET: Cm/Create
		[Authorize(Roles = "canEdit")]
		public ActionResult Create()
		{
		   return View(new Contact { Address = "123 N 456 W",
		    City="Great Falls", Email = "ab@cd.com", Name="Joe Smith", State="MT",
		   Zip = "59405"});
		}
		// POST: Cm/Create
		// To protect from overposting attacks, please enable the specific properties you want to bind to, for 
		// more details see http://go.microsoft.com/fwlink/?LinkId=317598.
		[HttpPost]
		[ValidateAntiForgeryToken]
		 [Authorize(Roles = "canEdit")]
		public ActionResult Create([Bind(Include = "ContactId,Name,Address,City,State,Zip,Email")] Contact contact)
		{
		    if (ModelState.IsValid)
		    {
		        db.Contacts.Add(contact);
		        db.SaveChanges();
		        return RedirectToAction("Index");
		    }
		    return View(contact);
		}
		// GET: Cm/Edit/5
		[Authorize(Roles = "canEdit")]
		public ActionResult Edit(int? id)
		{
		    if (id == null)
		    {
		        return new HttpStatusCodeResult(HttpStatusCode.BadRequest);
		    }
		    Contact contact = db.Contacts.Find(id);
		    if (contact == null)
		    {
		        return HttpNotFound();
		    }
		    return View(contact);
		}
		
1. Ctrl+F5를 눌러 응용 프로그램을 실행합니다.

1. 이전 세션에서 로그인되어 있는 경우 **로그아웃** 링크를 누릅니다.

1. **정보** 또는 **연락처** 링크를 클릭합니다. 익명 사용자는 해당 페이지를 볼 수 없으므로 로그인 페이지로 리디렉션됩니다. 

1. **새 사용자로 등록** 링크를 클릭하고 메일 *joe@contoso.com* 을 사용하여 로컬 사용자를 추가합니다. *Joe* 가 홈, 정보 및 연락처 페이지를 볼 수 있는지 확인합니다.

	![로그인](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss14.PNG)

1. *CM Demo* 링크를 클릭하고 데이터가 표시되는지 확인합니다.

1. 페이지의 편집 링크를 클릭하면 새 로컬 사용자가 *canEdit* 역할에 추가되지 않았으므로 로그인 페이지로 리디렉션됩니다.

1. 암호가 "P\_assw0rd1"("word"에서 "0"은 숫자 0임)인 **user1@contoso.com*으로 로그인합니다. 이전에 선택한 편집 페이지로 리디렉션됩니다.

	해당 계정과 암호를 사용하여 로그인할 수 없는 경우 소스 코드에서 암호를 복사하여 붙여 넣습니다. 그래도 로그인할 수 없으면 **AspNetUsers** 테이블의 **UserName** 열을 검사하여 *user1@contoso.com* 이 추가되었는지 확인합니다.

1. 데이터를 변경할 수 있는지 확인합니다.

## Azure에 앱 배포

1. Visual Studio의 **솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 상황에 맞는 메뉴에서 **게시**를 선택합니다.

	![프로젝트 상황에 맞는 메뉴의 게시][firsdeploy003]

	**웹 게시** 마법사가 열립니다.

1. **웹 게시** 대화 상자 왼쪽에 있는 **설정** 탭을 클릭합니다.

2. **v** 아이콘을 클릭하여 **ApplicationDbContext**에 대한 **원격 연결 문자열**을 선택하고 프로젝트를 만들 때 만든 데이터베이스를 선택합니다.
   
	![설정](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rrc2.png)

1. **ContactManagerContext**에서 **Code First 마이그레이션 실행**을 선택합니다.

	![설정](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rrc3.png)

1. **게시**를 클릭합니다.

1. *user1@contoso.com* 으로 로그인하고(암호 "P\_assw0rd1") 데이터를 편집할 수 있는지 확인합니다.

1. 로그아웃합니다.

1. [Google Developers Console](https://console.developers.google.com/)로 이동하고 **자격 증명** 탭에서 Azure URL을 사용하도록 리디렉션 URIS 및 JavaScript Orgins를 업데이트합니다.

1. Google 또는 Facebook을 사용하여 로그인합니다. 이렇게 하면 Google 또는 Facebook 계정이 **canEdit** 역할에 추가됩니다. *요청에서 리디렉션 URI:https://contactmanager{my version}.azurewebsites.net/signin-google은 등록된 리디렉션 URI와 일치하지 않습니다.*라는 메시지와 함께 HTTP 400 오류가 발생한 경우, 변경 내용을 전파할 때까지 대기해야 합니다. 몇 분 후에 이 오류가 발생하면 URI가 올바른지 확인하세요.

### 다른 사용자가 등록할 수 없도록 웹앱 중지  

1. **서버 탐색기**에서 **Azure > 앱 서비스 > {리소스 그룹} > {웹앱}**으로 이동합니다.

4. 웹앱을 마우스 오른쪽 단추로 클릭하고 **중지**를 선택합니다.

	또는 [Azure 포털](https://portal.azure.com/)에서 웹앱의 블레이드로 이동한 다음 블레이드 맨 위에서 **중지** 아이콘을 클릭할 수 있습니다.

	![웹앱 포털 중지](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/stopweb.png)

### AddToRoleAsync 제거, 게시 및 테스트

1. Account 컨트롤러의 **ExternalLoginConfirmation** 메서드에서 다음 코드를 주석으로 처리하거나 제거합니다.

		await UserManager.AddToRoleAsync(user.Id, "canEdit");

1. 프로젝트를 빌드하여 파일 변경 내용을 저장하고 컴파일 오류가 없는지 확인합니다.

5. **Solution Explorer**에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **Publish**를 선택합니다.

	   ![프로젝트 상황에 맞는 메뉴의 게시](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/GS13publish.png)
	
4. **Start Preview** 단추를 클릭합니다. 업데이트해야 하는 파일만 배포됩니다.

5. Visual Studio 또는 포털에서 웹앱을 시작합니다. **웹앱이 중지된 동안에는 게시할 수 없습니다.**

	![웹앱 시작](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss15.png)

5. Visual Studio로 돌아가서 **게시**를 클릭합니다.

3. Azure 앱이 기본 브라우저에서 열립니다. 로그인되어 있으면 익명 사용자로 홈 페이지를 볼 수 있도록 로그아웃합니다.

4. **정보** 링크를 클릭합니다. 로그인 페이지로 리디렉션됩니다.

5. 로그인 페이지에서 **등록** 링크를 클릭하고 로컬 계정을 만듭니다. 이 로컬 계정을 사용하여 읽기 전용 페이지에는 액세스할 수 있지만 *canEdit* 역할로 보호된 데이터 변경 페이지에는 액세스할 수 없는 것을 확인할 것입니다. 자습서의 뒷부분에서 로컬 계정 액세스를 제거하겠습니다.

	![등록](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss16.PNG)

1. *정보* 및 *연락처* 페이지로 이동할 수 있는지 확인합니다.

	![로그오프](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss17.PNG)

1. **CM Demo** 링크를 클릭하여 **Cm** 컨트롤러로 이동합니다. 또는 URL에 *Cm* 을 추가할 수 있습니다.

	![CM 페이지](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rrr4.png)
 
1. 편집 링크를 클릭합니다.

	로그인 페이지로 리디렉션됩니다.

2. **다른 서비스를 사용하여 로그인**에서 Google 또는 Facebook을 클릭하고 이전에 등록한 계정으로 로그인합니다. 신속하게 작업하는 동안 세션 쿠키가 시간 초과되지 않으면 이전에 사용한 Google 또는 Facebook 계정을 사용하여 자동으로 로그인됩니다.

2. 해당 계정에 로그인되어 있는 동안 데이터를 편집할 수 있는지 확인합니다.

	**참고:** 이 앱을 통해 Google에서 로그아웃한 다음 동일한 브라우저에서 다른 Google 계정으로 로그인할 수 없습니다. 브라우저 한 개를 사용하는 경우 Google로 이동해서 로그아웃해야 합니다. 다른 브라우저에서는 Google과 같은 동일한 타사 인증자의 다른 계정으로 로그온할 수 있습니다.

Google 계정 정보의 이름과 성을 채우지 않은 경우 NullReferenceException이 발생합니다.

## SQL Azure DB 검사 ##

1. **서버 탐색기**에서 **Azure > SQL 데이터베이스 > {데이터베이스}**로 이동

2. 데이터베이스를 마우스 오른쪽 단추로 클릭한 다음 **SQL Server 개체 탐색기에서 열기**를 선택합니다.
 
	![SSOX에서 열기](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rrr12.png)
 
3. 이전에 이 데이터베이스에 연결하지 않은 경우, 사용자의 현재 IP 주소에 대한 액세스가 가능하도록 방화벽 규칙을 추가 하라는 메시지가 표시될 수 있습니다. IP 주소가 미리 채워집니다. **방화벽 규칙 추가**를 클릭만 하면 액세스할 수 있도록 합니다.

	![방화벽 규칙 추가](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/addfirewallrule.png)

3. 데이터베이스 서버를 만들 때 지정한 사용자 이름 및 암호를 사용하여 데이터베이스에 로그인합니다.
 
1. **AspNetUsers** 테이블을 마우스 오른쪽 단추로 클릭하고 **데이터 보기**를 선택합니다.

	![CM 페이지](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rrr8.png)
 
1. 등록 시 사용한 Google 계정의 ID가 **canEdit** 역할에 있고 *user1@contoso.com* 의 ID입니다. **canEdit** 역할에 포함된 유일한 사용자여야 합니다. 다음 단계에서 확인하겠습니다.

	![CM 페이지](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/s2.png)
 
2. **SQL Server 객체 탐색기**에서 **AspNetUserRoles**를 마우스 오른쪽 단추로 클릭하고 **데이터 보기**를 선택합니다.

	![CM 페이지](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rs1.png)
 
3. **UserId**가 *user1@contoso.com* 에 속하고 등록한 Google 계정인지 확인합니다.

## 문제 해결

문제를 발생한 경우 해결 방법에 대한 몇 가지 제안은 다음과 같습니다.

* SQL 데이터베이스 프로비전 오류 - 현재 SDK가 설치되었는지 확인합니다. 2.8.1 이전 버전은 VS에서 데이터베이스 서버 또는 데이터베이스를 만들려고 할 때 일부 시나리오에서 오류가 발생하는 버그가 있습니다.
* Azure 리소스를 만들 때 오류 메시지 "구독 제공 형식에서 작업을 지원하지 않습니다" - 위와 동일합니다.
* 배포 시 오류 - [기본 ASP.NET 배포](web-sites-dotnet-get-started.md) 문서 진행을 고려합니다. 배포 시나리오는 더 간단하고 동일한 문제가 있는 경우 격리시키기 쉽습니다. 예를 들어 일부 엔터프라이즈 환경에서 회사 방화벽은 웹 배포가 Azure에 대한 필요한 종류의 연결을 만들지 못하도록 합니다.
* 배포 시 웹 게시 마법사에서 연결 문자열 선택 옵션 없음 - 다른 메서드를 사용하여 Azure 리소스를 만든 경우(예: 포털에서 만든 웹앱 및 SQL 데이터베이스에 배포하려는 경우) SQL 데이터베이스는 웹앱에 연결되지 않을 수 있습니다. VS를 사용하여 새 웹앱 및 데이터베이스를 만드는 가장 쉬운 솔루션은 자습서에 표시된 것과 같습니다. 자습서를 다시 시작하지 않아도 됩니다. -- 웹 게시 마법사에서 새 웹앱을 만들도록 선택할 수 있고 프로젝트를 만들 때 얻은 것과 동일한 Azure 리소스 만들기 대화 상자를 가져옵니다.
* Google 또는 Facebook 개발자 포털에 대한 지침은 만료되었습니다. - 이 자습서의 후반부에 있는 주요 Disqus 설명을 참조하세요.

## 다음 단계

사용자를 인증하는 기본 ASP.NET MVC 웹앱을 만들었습니다. 일반적인 인증 작업 및 중요한 데이터 보안을 유지하는 방법에 대한 자세한 내용은 다음 자습서를 참조하세요.

- [로그인, 메일 확인 및 암호 재설정이 포함된 보안 ASP.NET MVC 5 웹앱 만들기(영문)](http://www.asp.net/mvc/overview/getting-started/create-an-aspnet-mvc-5-web-app-with-email-confirmation-and-password-reset)
- [SMS 및 메일 2단계 인증을 사용하는 ASP.NET MVC 5 앱(영문)](http://www.asp.net/mvc/overview/getting-started/aspnet-mvc-5-app-with-sms-and-email-two-factor-authentication)
- [암호 및 기타 중요한 데이터를 ASP.NET 및 Azure에 배포하기 위한 모범 사례(영문)](http://www.asp.net/identity/overview/features-api/best-practices-for-deploying-passwords-and-other-sensitive-data-to-aspnet-and-azure) 
- [Facebook과 Google OAuth2를 사용하여 ASP.NET MVC 5 앱 만들기](http://www.asp.net/mvc/tutorials/mvc-5/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on)(영문) 여기에는 사용자 등록 DB에 프로필 데이터를 추가하는 방법 및 Facebook을 인증 공급자로 사용하는 방법에 대한 지침이 포함됩니다.
- [ASP.NET MVC 5 시작](http://www.asp.net/mvc/tutorials/mvc-5/introduction/getting-started)

Entity Framework를 사용하는 방법에 대한 고급 자습서는 [EF 및 MVC 시작](http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application)을 참조하세요.

이 자습서는 [Rick Anderson](http://blogs.msdn.com/b/rickandy/)(Twitter [@RickAndMSFT](https://twitter.com/RickAndMSFT))에 의해 작성되었으며, Tom Dykstra 및 Barry Dorrans(Twitter [@blowdart](https://twitter.com/blowdart))의 도움을 받았습니다.

자습서 자체뿐 아니라 설명된 제품과 관련해서 좋아한 사항이나 바라는 개선 사항에 대한 ***의견을 남겨주세요.*** 사용자 의견은 개선 사항의 우선 순위를 지정하는 데 도움이 됩니다. [코드 사용 방법 보기](http://aspnet.uservoice.com/forums/228522-show-me-how-with-code)에서 새 항목을 요청하고 투표할 수도 있습니다.

## 변경된 내용

* 웹 사이트에서 앱 서비스로의 변경에 대한 지침은 [Azure 앱 서비스와 이 서비스가 기존 Azure 서비스에 미치는 영향](http://go.microsoft.com/fwlink/?LinkId=529714)을 참조하세요.

<!-- bookmarks -->
[Add an OAuth Provider]: #addOauth
[Using the Membership API]: #mbrDB
[Create a Data Deployment Script]: #ppd
[Update the Membership Database]: #ppd2

[setupwindowsazureenv]: #bkmk_setupwindowsazure
[createapplication]: #bkmk_createmvc4app
[deployapp1]: #bkmk_deploytowindowsazure1
[deployapp11]: #bkmk_deploytowindowsazure11
[adddb]: #bkmk_addadatabase


<!-- images-->
[rx2]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rx2.png

[rx5]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx5.png
[rx6]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx6.png
[rx7]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx7.png
[rx8]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx8.png
[rx9]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx9.png

[rxb]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rxb.png


[rxSSL]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rxSSL.png

[rxNOT]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxNOT.png
[rxNOT2]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxNOT2.png

[rxNOT]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxNOT.png
[rxNOT]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxNOT.png
[rxNOT]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxNOT.png
[rr1]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr1.png

[rxPrevDB]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxPrevDB.png

[rxWSnew]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxWSnew2.png
[rxCreateWSwithDB]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxCreateWSwithDB.png

[setup007]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-setup-azure-site-004.png

[newapp004]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/dntutmobile-createapp-004.png

[firsdeploy003]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/dntutmobile-deploy1-publish-001.png

[adddb002]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/dntutmobile-adddatabase-002.png
[addcode001]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/dntutmobile-controller-add-context-menu.png

[addcode008]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-migrations-package-manager-menu.png
[addcode009]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/dntutmobile-migrations-package-manager-console.png


[Important information about ASP.NET in Azure web apps]: #aspnetwindowsazureinfo
[Next steps]: #nextsteps

[ImportPublishSettings]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ImportPublishSettings.png
 

<!---HONumber=AcomDC_0302_2016-->