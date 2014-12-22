<properties urlDisplayName="Website with SQL Database" pageTitle="Azure 웹 사이트에 멤버 자격, OAuth 및 SQL 데이터베이스를 포함한 ASP.NET MVC 앱 배포" metaKeywords="Azure hello world 자습서, Azure 시작 자습서, SQL 데이터베이스 자습서, Azure .NET hello world 자습서, Azure C# hello world 자습서, SQL Azure C# 자습서" description="Learn how to develop an ASP.NET MVC 5 website with a SQL Database back-end deploy it to Azure." metaCanonical="" services="web-sites,sql-database" documentationCenter=".NET" title="Deploy a Secure ASP.NET MVC 5 app with Membership, OAuth, and SQL Database to an Azure Website" authors="riande"  solutions="" writer="riande" manager="wpickett" editor="mollybos"  />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="10/14/2014" ms.author="riande" /> 



# Azure 웹 사이트에 멤버 자격, OAuth 및 SQL 데이터베이스를 포함한 ASP.NET MVC 5 앱 배포

***업데이트: 2014년 10월 12일.***

이 자습서는 사용자가 Facebook 또는 Google 자격 증명을 사용하여 로그인할 수 있는 보안 ASP.NET MVC 5 웹 앱을 빌드하는 방법을 보여 줍니다. 또한 Azure에 응용 프로그램을 배포합니다.

Azure 계정은 무료로 개설할 수 있으며, Visual Studio 2013이 아직 없는 경우 SDK에서 Web Express용 Visual Studio 2013를 자동으로 설치합니다. Azure용 개발을 무료로 시작할 수 있습니다.

이 자습서에서는 이전에 Azure를 사용한 경험이 없다고 가정합니다. 이 자습서를 완료하면 클라우드에서 클라우드 데이터베이스를 사용하는 보안 데이터 기반 웹 응용 프로그램을 실행할 수 있습니다.

다음 내용을 배웁니다.

* 보안 ASP.NET MVC 5 프로젝트를 만들고 Azure 웹 사이트에 게시하는 방법
* [OAuth](http://oauth.net/ "http://oauth.net/") 및 ASP.NET 멤버 자격 데이터베이스를 사용하여 응용 프로그램 보안을 유지하는 방법
* SQL 데이터베이스를 사용하여 Azure에 데이터를 저장하는 방법

ASP.NET MVC 5에서 빌드되고 데이터베이스 액세스에 ADO.NET Entity Framework를 사용하는 간단한 연락처 목록 웹 앱을 빌드합니다. 다음 그림은 완성된 응용 프로그램에 대한 로그인 페이지를 보여 줍니다.

![login page][rxb]

>[WACOM.NOTE] 이 자습서를 완료하려면 Microsoft Azure 계정이 필요합니다. 계정이 없는 경우 <a href="/ko-kr/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F" target="_blank">MSDN 구독자 혜택을 활성화</a>하거나 <a href="/ko-kr/pricing/free-trial/?WT.mc_id=A261C142F" target="_blank">무료 체험을 등록</a>할 수 있습니다. 


이 자습서에서는 다음을 수행합니다.

- [개발 환경 설정](#setupdevenv)
- [ASP.NET MVC 5 응용 프로그램 만들기][createapplication]
- [Azure에 응용 프로그램 배포][deployapp1]
- [응용 프로그램에 데이터베이스 추가][adddb]
- [OAuth 공급자 추가][]
- [Azure에 앱 배포][deployapp11]
- [다음 단계][]


[WACOM.INCLUDE [install-sdk-2013-only](../includes/install-sdk-2013-only.md)]

localhost에 새 SSL 인증서를 사용하려면 [Visual Studio 2013 Update 3 RC](http://go.microsoft.com/fwlink/?LinkId=390521) 이상을 설치해야 합니다.

<h2><a name="bkmk_createmvc4app"></a>ASP.NET MVC 5 응용 프로그램 만들기</h2>

### 프로젝트 만들기

1. **파일** 메뉴에서 **새 프로젝트**를 클릭합니다.

	![New Project in File menu](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/gs13newproj.png)

1. **새 프로젝트** 대화 상자에서 **C#**을 확장하고 **설치된 템플릿** 아래의 **웹**을 선택한 다음 **ASP.NET 웹 응용 프로그램**을 선택합니다.

1. 응용 프로그램 이름을 **ContactManager**로 지정하고 **확인**을 클릭합니다.

	![New Project dialog box](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/GS13newprojdb.png)
 
	**참고:** "ContactManager"를 입력해야 합니다. 나중에 복사할 코드 블록에서는 프로젝트 이름을 ContactManager로 가정합니다. 

1. **새 ASP.NET 프로젝트** 대화 상자에서 **MVC 템플릿**을 선택합니다. **인증**이 **개별 사용자 계정*으로 설정되고, **Host in the cloud**가 선택되고 **웹 사이트**가 선택되었는지 확인합니다.

	![New ASP.NET Project dialog box](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss1.PNG)

1. 구성 마법사에서 *ContactManager*를 기준으로 고유한 이름을 제안합니다(아래 이미지 참조). 근처에 있는 지역을 선택합니다. [azurespeed.com](http://www.azurespeed.com/ "AzureSpeed.com")을 사용하여 대기 시간이 가장 짧은 데이터 센터를 찾을 수 있습니다. 
2. 기존에 만든 데이터베이스 서버가 없으면 **새 서버 만들기**를 선택하고 데이터베이스 사용자 이름과 암호를 입력합니다.

	![Configure Azure Website](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/configAz.PNG)

데이터베이스 서버가 있으면 이를 사용하여 새 데이터베이스를 만듭니다. 데이터베이스 서버는 중요한 리소스이며, 일반적으로 데이터베이스별로 데이터베이스 서버를 만들기보다는 테스트 및 개발을 위해 같은 서버에서 여러 데이터베이스 만들려고 합니다. 웹 사이트와 데이터베이스가 같은 지역에 있는지 확인하세요.

![Configure Azure Website](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/configWithDB.PNG)

### 페이지 머리글 및 바닥글 설정


1. **솔루션 탐색기**의 *Views\Shared* 폴더에서 *Layout.cshtml* 파일을 엽니다.

	![_Layout.cshtml in Solution Explorer][newapp004]

1. *Layout.cshtml* 파일의 태그를 다음 코드로 바꿉니다. 변경 내용은 아래에 강조 표시되어 있습니다.

<pre>
			&lt;!DOCTYPE html&gt;
			&lt;html&gt;
			&lt;head&gt;
			    &lt;meta charset="utf-8" /&gt;
			    &lt;meta name="viewport" content="width=device-width, initial-scale=1.0"&gt;
			    &lt;title&gt;@ViewBag.Title - <mark>Contact Manager</mark>&lt;/title&gt;
			    @Styles.Render("~/Content/css")
			    @Scripts.Render("~/bundles/modernizr")
			
			&lt;/head&gt;
			&lt;body&gt;
			    &lt;div class="navbar navbar-inverse navbar-fixed-top"&gt;
			        &lt;div class="container"&gt;
			            &lt;div class="navbar-header"&gt;
			                &lt;button type="button" class="navbar-toggle" data-toggle="collapse" data-target=".navbar-collapse"&gt;
			                    &lt;span class="icon-bar"&gt;&lt;/span&gt;
			                    &lt;span class="icon-bar"&gt;&lt;/span&gt;
			                    &lt;span class="icon-bar"&gt;&lt;/span&gt;
			                &lt;/button&gt;
			                @Html.ActionLink("<mark>CM Demo</mark>", "Index", "<mark>Cm</mark>", new { area = "" }, new { @class = "navbar-brand" })
			            &lt;/div&gt;
			            &lt;div class="navbar-collapse collapse"&gt;
			                &lt;ul class="nav navbar-nav"&gt;
			                    &lt;li&gt;@Html.ActionLink("Home", "Index", "Home")&lt;/li&gt;
			                    &lt;li&gt;@Html.ActionLink("About", "About", "Home")&lt;/li&gt;
			                    &lt;li&gt;@Html.ActionLink("Contact", "Contact", "Home")&lt;/li&gt;
			                &lt;/ul&gt;
			                @Html.Partial("_LoginPartial")
			            &lt;/div&gt;
			        &lt;/div&gt;
			    &lt;/div&gt;
			    &lt;div class="container body-content"&gt;
			        @RenderBody()
			        &lt;hr /&gt;
			        &lt;footer&gt;
			            &lt;p&gt;&amp;copy; @DateTime.Now.Year - <mark>Contact Manager</mark>&lt;/p&gt;
			        &lt;/footer&gt;
			    &lt;/div&gt;
			
			    @Scripts.Render("~/bundles/jquery")
			    @Scripts.Render("~/bundles/bootstrap")
			    @RenderSection("scripts", required: false)
			&lt;/body&gt;
			&lt;/html&gt;
</pre>


![code changes](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rs3.png)


### 로컬에서 응용 프로그램 실행

1. Ctrl+F5를 눌러 앱을 실행합니다.

	응용 프로그램 홈페이지가 기본 브라우저에 나타납니다.

	![Web site running locally](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr2.png)

Azure에 배포할 응용 프로그램을 만들기 위해 지금 수행해야 하는 작업은 이것뿐입니다. 

## 프로젝트에 SSL 사용 ##

1. SSL을 사용하도록 설정합니다. 솔루션 탐색기에서 **ContactManager** 프로젝트를 클릭한 다음 F4 키를 클릭하여 속성 대화 상자를 표시합니다. **SSL 사용**을 true로 변경합니다. **SSL URL**을 복사합니다. 이전에 SSL 웹 사이트를 만들지 않은 경우 SSL URL은 https://localhost:44300/입니다.

	![enable SSL][rxSSL]
 
1. 솔루션 탐색기에서 **Contact Manager** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **속성**을 클릭합니다.
1. 왼쪽 탭에서 **웹**을 클릭합니다.
1. **프로젝트 URL**을 변경하여 **SSL URL**을 사용하고 페이지를 저장합니다(Ctrl S).

	![enable SSL](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr1.png)
 
1. 다음 이미지와 같이 Internet Explorer가 Visual Studio가 시작되는 브라우저인지 확인합니다.

	![default browser](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss12.PNG)

	브라우저 선택기에서 Visual Studio가 시작되는 브라우저를 지정할 수 있습니다.

 	![browser selector](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss13.png)

여러 개의 브라우저를 선택할 수 있으며, 변경 사항이 있을 경우 Visual Studio에서 각 브라우저를 업데이트하도록 할 수 있습니다. 자세한 내용은 [Visual Studio 2013에서 브라우저 링크 사용](http://www.asp.net/visual-studio/overview/2013/using-browser-link)(영문)을 참조하세요.


1. Ctrl+F5를 눌러 응용 프로그램을 실행합니다. 지침에 따라 IIS Express에서 생성한 자체 서명된 인증서를 신뢰합니다.

	 ![instructions to trust the self-signed certificate that IIS Express has generated](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss26.PNG)

1. **보안 경고** 대화 상자를 읽고 **localhost**를 나타내는 인증서를 설치하려면 **예**를 클릭합니다.

 	![localhost IIS Express certificate warning ](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss27.PNG)

1. IE에 *홈* 페이지가 표시되며 SSL 경고가 없습니다.

	 ![IE with localhost SSL and no warnings](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss28.PNG)

	Google Chrome도 인증서를 수락하고 경고 없이 HTTPS 콘텐츠를 표시합니다. Firefox는 자체 인증서 저장소를 사용하므로 경고가 표시됩니다.

	 ![FireFox Cert Warning](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss30.PNG)

<h2><a name="bkmk_deploytowindowsazure1"></a>Azure에 응용 프로그램 배포</h2>

1. Visual Studio의 **솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 상황에 맞는 메뉴에서 **게시**를 선택합니다.

	![Publish in project context menu](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/GS13publish.png)
	
   **웹 게시** 마법사가 열립니다.

1. **웹 게시** 대화 상자에서 **게시**를 클릭합니다.

	![Publish](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr3.png)

	만든 응용 프로그램이 이제 클라우드에서 실행되고 있습니다. 다음에 응용 프로그램을 배포할 때는 변경된(또는 새) 파일만 배포됩니다.

	![Running in Cloud](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss4.PNG)

<h2><a name="bkmk_addadatabase"></a>응용 프로그램에 데이터베이스 추가</h2>

이제 앱을 업데이트하여 연락처를 표시 및 업데이트하고 데이터베이스에 데이터를 저장하는 기능을 추가하겠습니다. 앱에서는 EF(Entity Framework)를 사용하여 데이터베이스를 만들고 데이터를 읽고 업데이트합니다.

### 연락처에 대한 데이터 모델 클래스 추가

먼저 코드로 간단한 데이터 모델을 만듭니다.

1. **솔루션 탐색기**에서 모델 폴더를 마우스 오른쪽 단추로 클릭하고 **추가**를 클릭한 후 **클래스**를 클릭합니다.

	![Add Class in Models folder context menu](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr5.png)

2. **새 항목 추가** 대화 상자에서 새 클래스 파일의 이름을 *Contact.cs*로 지정하고 **추가**를 클릭합니다.

	![Add New Item dialog box][adddb002]

3. Contacts.cs 파일 내용을 다음 코드로 바꿉니다.

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
**Contacts** 클래스는 각 연락처에 대해 저장할 데이터와 데이터베이스에 필요한 기본 키 *ContactID*를 정의합니다.

### 앱 사용자가 연락처 작업을 수행할 수 있는 웹 페이지 만들기

ASP.NET MVC 스캐폴딩 기능은 CRUD(만들기, 읽기, 업데이트 및 삭제) 작업을 수행하는 코드를 자동으로 생성할 수 있습니다.

<h2><a name="bkmk_addcontroller"></a>데이터에 대한 컨트롤러 및 뷰 추가</h2>

1. 프로젝트를 빌드합니다**(Ctrl+Shift+B)**. 스캐폴딩 메커니즘을 사용하기 전에 프로젝트를 빌드해야 합니다. 
1. **솔루션 탐색기**에서 컨트롤러 폴더를 마우스 오른쪽 단추로 클릭하고 **추가**, **컨트롤러**를 차례로 클릭합니다.

	![Add Controller in Controllers folder context menu][addcode001]

5. **스캐폴드 추가** 대화 상자에서 **MVC 5 컨트롤러(뷰 포함), EF 사용**을 선택하고 **추가**를 클릭합니다.
	
	![Add Scaffold dlg](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr6.png)


1. **모델 클래스** 드롭다운 상자에서 **Contact(ContactManager.Models)**를 선택합니다. 아래 이미지를 참조하세요.
1. **데이터 컨텍스트 클래스**에서 **ApplicationDbContext(ContactManager.Models)**를 선택합니다. **ApplicationDbContext**는 멤버 자격 DB 및 연락처 데이터 둘 다에 사용됩니다.
1. **컨트롤러 이름** 입력란에 컨트롤러 이름으로 "CmController"를 입력합니다. 

	![New data ctx dlg](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss5.PNG)

1. **추가**를 클릭합니다.

Visual Studio에서 **Contact** 개체에 대한 CRUD 데이터베이스 작업의 컨트롤러 메서드와 뷰를 만듭니다.

## 마이그레이션 사용, 데이터베이스 만들기, 샘플 데이터 및 데이터 이니셜라이저 추가 ##

다음 작업은 만든 데이터 모델에 따라 데이터베이스를 만들기 위해 [Code First 마이그레이션](http://msdn.microsoft.com/library/hh770484.aspx)(영문) 기능을 사용하도록 설정하는 것입니다.

1. **도구** 메뉴에서 **NuGet 패키지 관리자**, **패키지 관리자 콘솔**을 차례로 선택합니다.
	![Package Manager Console in Tools menu](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/SS6.png)

2. **패키지 관리자 콘솔** 창에서 다음 명령을 입력합니다.

		 enable-migrations
**enable-migrations** 명령은 *Migrations* 폴더를 만들고 해당 폴더에 *Configuration.cs* 파일을 넣습니다. 이 파일을 편집하여 데이터베이스를 시드하고 마이그레이션을 구성할 수 있습니다. 

2. **패키지 관리자 콘솔** 창에서 다음 명령을 입력합니다.

		 add-migration Initial


**add-migration Initial** 명령은 데이터베이스를 만든 *Migrations* 폴더에서 **&lt;date_stamp&gt;Initial** 파일을 생성합니다. 첫 번째 매개 변수(**Initial**)는 임의이며 파일 이름을 만드는 데 사용됩니다. **솔루션 탐색기**에서 새 클래스 파일을 볼 수 있습니다.
**Initial** 클래스의** Up** 메서드는 Contacts 테이블을 만들고 이전 상태로 돌아가려는 경우 사용되는 **Down** 메서드는 테이블을 삭제합니다.
3. *Migrations\Configuration.cs* 파일을 엽니다. 
4. 다음 네임스페이스를 추가합니다. 

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

이 코드는 연락처 정보를 사용하여 데이터베이스를 초기화(시드)합니다. 데이터베이스 시드에 대한 자세한 내용은 [EF(Entity Framework) DB 시드 및 디버그](http://blogs.msdn.com/b/rickandy/archive/2013/02/12/seeding-and-debugging-entity-framework-ef-dbs.aspx)(영문)를 참조하세요.


6. **패키지 관리자 콘솔**에서 다음 명령을 입력합니다.

		update-database

	![Package Manager Console commands][addcode009]

**update-database**는 데이터베이스를 만드는 첫 번째 마이그레이션을 실행합니다. 기본적으로 데이터베이스는 SQL Server Express LocalDB 데이터베이스로 생성됩니다. 

7. Ctrl+F5를 눌러 응용 프로그램을 실행한 다음 **CM Demo** 링크를 클릭하거나 http://localhost:(port#)/Cm으로 이동합니다.

응용 프로그램에서 시드 데이터를 표시하고 편집, 세부 정보 및 삭제 링크를 제공합니다. 데이터를 만들고, 편집, 삭제 및 표시할 수 있습니다.

	![MVC view of data][rx2]



<h2><a name="addOauth"></a>OAuth2 공급자 추가</h2>

[OAuth](http://oauth.net/ "http://oauth.net/")는 웹, 모바일 및 데스크톱 응용 프로그램에서 간단한 표준 메서드로 보안 권한 부여를 허용하는 개방형 프로토콜입니다. ASP.NET MVC 인터넷 템플릿은 OAuth를 사용하여 Facebook, Twitter, Google 및 Microsoft를 인증 공급자로 표시합니다. 이 자습서에서는 인증 공급자로 Google만 사용하지만 이러한 공급자를 사용하도록 코드를 쉽게 수정할 수 있습니다. 다른 공급자를 구현하는 단계도 이 자습서에 표시되는 단계와 매우 비슷합니다. Facebook을 인증 공급자로 사용하려면 내 자습서 [Facebook, Twitter, LinkedIn 및 Google OAuth2 Sign-on을 사용한 MVC 5 앱](http://www.asp.net/mvc/tutorials/mvc-5/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on)(영문)을 참조하세요.

자습서에서는 인증 외에도 역할을 사용하여 권한 부여를 구현합니다. *canEdit* 역할에 추가한 사용자만 데이터를 변경할 수 있습니다(즉, 연락처 만들기, 편집 또는 삭제).

내 자습서 [Facebook, Twitter, LinkedIn 및 Google OAuth2 Sign-on을 사용한 MVC 5 앱](http://www.asp.net/mvc/tutorials/mvc-5/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on#goog)(영문)에서 **OAuth 2용 Google 앱을 만들어 OAuth2용 Google 앱 설정**의 지침을 따릅니다. 앱을 실행하고 테스트하여 Google 인증으로 로그온할 수 있는지 확인합니다.

<h2><a name="mbrDB"></a>멤버 자격 API 사용</h2>
이 섹션에서는 멤버 자격 데이터베이스에 로컬 사용자와 *canEdit* 역할을 추가합니다. *canEdit* 역할의 사용자만 데이터를 편집할 수 있습니다. 수행하는 작업별로 역할의 이름을 지정하는 것이 좋으므로 *admin* 역할보다 *canEdit*이 우선합니다. 응용 프로그램이 발전함에 따라 설명이 부족한 *superAdmin*보다 *canDeleteMembers* 등의 새 역할을 추가할 수 있습니다.

1. *migrations\configuration.cs* 파일을 열고 다음 `using` 문을 추가합니다.

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
	<pre>
        protected override void Seed(ContactManager.Models.ApplicationDbContext context)
        {
            <mark>AddUserAndRole(context);</mark>
            context.Contacts.AddOrUpdate(p => p.Name,
                // 간단한 표시를 위해 코드가 제거됨
        }
	</pre>  
<span></span>
	The following images shows the changes to *Seed* method:

	![code image](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss24.PNG)

   This code creates a new role called *canEdit*, creates a new local user *user1@contoso.com*, and adds *user1@contoso.com* to the *canEdit* role. For more information, see the [ASP.NET Identity resource page](http://curah.microsoft.com/55636/aspnet-identity).

## Use Temporary Code to Add New Social Login Users to the canEdit Role  ##
In this section you will temporarily modify the **ExternalLoginConfirmation** method in the Account controller to add new users registering with an OAuth provider to the *canEdit* role. We will temporarily modify the **ExternalLoginConfirmation** method to automatically add new users to an administrative role. Until we provide a tool to add and manage roles, we'll use the temporary automatic registration code below. We hope to provide a tool similar to [WSAT](http://msdn.microsoft.com/ko-kr/library/ms228053.aspx) in the future which allow you to create and edit user accounts and roles. Later in the tutorial I'll show how you can use **Server Explorer** to add users to roles.  

1. Open the **Controllers\AccountController.cs** file and navigate to the **ExternalLoginConfirmation** method.
1. Add the following call to **AddToRoleAsync** just before the **SignInAsync** call.

                await UserManager.AddToRoleAsync(user.Id, "canEdit");

   The code above adds the newly registered user to the "canEdit" role, which gives them access to action methods that change (edit) data. An image of the code change is shown below:

   ![code](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss9.PNG)

Later in the tutorial you will deploy the application to Azure, where you will log-on with Google or another third party authentication provider. This will add your newly registered account to the *canEdit* role. Anyone who finds your site's URL and has a Google ID can then register and update your database. To prevent other people from doing that, you can stop the site. You'll be able to verify who is in the *canEdit* role by examining the database.

In the **Package Manager Console** hit the up arrow key to bring up the following command:

		Update-Database

Run the  **Update-Database** command which will run the **Seed** method, and that will run the **AddUserAndRole** you just added. The **AddUserAndRole** will create the user *user1@contoso.com* and add her to the *canEdit* role.

## Protect the Application with SSL and the Authorize Attribute ##

In this section you will apply the [Authorize](http://msdn.microsoft.com/ko-kr/library/system.web.mvc.authorizeattribute.aspx) attribute to restrict access to the action methods. Anonymous users will be able to view the **Index** action method of the home controller only. Registered users will be able to see contact data (The **Index** and **Details** pages of the Cm controller), the About, and the Contact pages. Only users in the *canEdit* role will be able to access action methods that change data.

1. Add the [Authorize](http://msdn.microsoft.com/ko-kr/library/system.web.mvc.authorizeattribute.aspx) filter and the [RequireHttps](http://msdn.microsoft.com/ko-kr/library/system.web.mvc.requirehttpsattribute.aspx) filter to the application. An alternative approach is to add the [Authorize](http://msdn.microsoft.com/ko-kr/library/system.web.mvc.authorizeattribute.aspx) attribute and the [RequireHttps](http://msdn.microsoft.com/ko-kr/library/system.web.mvc.requirehttpsattribute.aspx) attribute to each controller, but it's considered a security best practice to apply them to the entire application. By adding them globally, every new controller and action method you add will automatically be protected, you won't need to remember to apply them. For more information see [Securing your ASP.NET MVC  App and the new AllowAnonymous Attribute](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx). Open the *App_Start\FilterConfig.cs* file and replace the *RegisterGlobalFilters* method with the following (which adds the two filters):
		<pre>
        public static void
        RegisterGlobalFilters(GlobalFilterCollection filters)
        {
            filters.Add(new HandleErrorAttribute());
            <mark>filters.Add(new System.Web.Mvc.AuthorizeAttribute());
            filters.Add(new RequireHttpsAttribute());</mark>
        }
		</pre>


	The following image shows the changed code:


	The [Authorize](http://msdn.microsoft.com/ko-kr/library/system.web.mvc.authorizeattribute.aspx) filter applied in the code above will prevent anonymous users from accessing any methods in the application. You will use the [AllowAnonymous](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx) attribute to opt out of the authorization requirement in a couple methods, so anonymous users can log in and can view the home page. The  [RequireHttps](http://msdn.microsoft.com/ko-kr/library/system.web.mvc.requirehttpsattribute.aspx) will require all access to the web app be through HTTPS.

1. Add the [AllowAnonymous](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx) attribute to the **Index** method of the Home controller. The [AllowAnonymous](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx) attribute enables you to white-list the methods you want to opt out of authorization. An image of a portion of the HomeController is shown below:	

  	![code](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss11.PNG)

2. Do a global search for *AllowAnonymous*, you can see it is used in the log in and registration methods of the Account controller.
1. In *CmController.cs*, add `[Authorize(Roles = "canEdit")]` to the HttpGet and HttpPost methods that change data (Create, Edit, Delete, every action method except Index and Details) in the *Cm* controller. A portion of the completed code is shown below: 

   	![img of code](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr11.png)



1. If you are still logged in from a previous session, hit the **Log out** link.
1. Click on the **About** or **Contact** links. You will be redirected to the log in page because anonymous users cannot view those pages. 
1. Click the **Register as a new user** link and add a local user with email *joe@contoso.com*. Verify *Joe* can view the Home, About and Contact pages. 

	![login](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss14.PNG)

1. Click the *CM Demo* link and verify you see the data. 
1. Click an edit link on the page, you will be redirected to the log in page (because a new local user is not added to the *canEdit* role).
1. Log in as *user1@contoso.com* with password of "P_assw0rd1" (the "0" in "word" is a zero). You will be redirected to the edit page you previously selected. <br/>
   If you can't log in with that account and password, try copying the password from the source code and pasting it. If you still can't log in, check the **UserName** column of the **AspNetUsers** table to verify *user1@contoso.com* was added. 

1. Verify you can make data changes.

<h2><a name="bkmk_deploytowindowsazure11"></a>Azure에 앱 배포</h2>

1. Visual Studio의 **솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 상황에 맞는 메뉴에서 **게시**를 선택합니다.

	![Publish in project context menu][firsdeploy003]

**웹 게시** 마법사가 열립니다.

1. **웹 게시** 대화 상자 왼쪽에 있는 **설정** 탭을 클릭합니다. **v** 아이콘을 클릭하여 **ApplicationDbContext**에 대한 **원격 연결 문자열**을 선택한 다음 **ContactManagerNN_db**를 선택합니다.

   
	![settings](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrc2.png)

1. **ContactManagerContext**에서 **Execute Code First 마이그레이션**을 선택합니다.

	![settings](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrc3.png)

1. **Publish**를 클릭합니다.
1. *user1@contoso.com*(암호: "P_assw0rd1")으로 로그인하고 데이터를 편집할 수 있는지 확인합니다.
1. 로그아웃합니다.
1. [Google Developers Console](https://console.developers.google.com/)로 이동하고 **자격 증명** 탭에서 Azure URL을 사용하도록 리디렉션 URIS 및 JavaScript Orgins를 업데이트합니다.
1. Google 또는 Facebook을 사용하여 로그인합니다. 이렇게 하면 Google 또는 Facebook 계정이 **canEdit** 역할에 추가됩니다. 다음 메시지와 함께 HTTP 400 오류가 표시될 경우 *The redirect URI in the request: https://contactmanager{my version}.azurewebsites.net/signin-google did not match a registered redirect URI.*, you'll have to wait until the changes you made are propagated. If you get this error after more than a minute, verify the URIs are correct.

### 다른 사용자가 등록할 수 없도록 웹 사이트 중지  

1. **서버 탐색기**에서 **웹 사이트**로 이동합니다.
4. 각 웹 사이트 인스턴스를 마우스 오른쪽 단추로 클릭하고 **웹 사이트 중지**를 선택합니다. 

	![stop web site](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr2.png) 

또는 Azure 관리 포털에서 웹 사이트를 선택하고 페이지 맨 아래에 있는 **중지** 아이콘을 클릭합니다.

	![stop web site](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr3.png)

### AddToRoleAsync 제거, 게시 및 테스트

1. Account 컨트롤러의 **ExternalLoginConfirmation** 메서드에서 다음 코드를 주석으로 처리하거나 제거합니다. 
                `await UserManager.AddToRoleAsync(user.Id, "canEdit");`
1. 프로젝트를 빌드하여 파일 변경 내용을 저장하고 컴파일 오류가 없는지 확인합니다.
5. **솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **Publish**를 선택합니다.

	   ![Publish in project context menu](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/GS13publish.png)
	
4. **Start Preview** 단추를 클릭합니다. 업데이트해야 하는 파일만 배포됩니다.
5. Visual Studio 또는 포털에서 웹 사이트를 시작합니다. **웹 사이트가 중지된 동안에는 게시할 수 없습니다**.

	![start web site](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss15.png)

5. Visual Studio로 돌아가서 **게시**를 클릭합니다.
3. Azure 앱이 기본 브라우저에서 열립니다. 로그인되어 있으면 익명 사용자로 홈 페이지를 볼 수 있도록 로그아웃합니다.  
4. **정보** 링크를 클릭합니다. 로그인 페이지로 리디렉션됩니다.
5. 로그인 페이지에서 **등록** 링크를 클릭하고 로컬 계정을 만듭니다. 이 로컬 계정을 사용하여 읽기 전용 페이지에는 액세스할 수 있지만 *canEdit* 역할로 보호된 데이터 변경 페이지에는 액세스할 수 없는 것을 확인할 것입니다. 자습서의 뒷부분에서 로컬 계정 액세스를 제거하겠습니다. 

	![Register](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss16.PNG)

1. *About* 및 *Contact* 페이지로 이동할 수 있는지 확인합니다.

	![Log off](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss17.PNG)

1. **CM Demo** 링크를 클릭하여 **Cm** 컨트롤러로 이동합니다. 또는 URL에 *Cm*을 추가할 수 있습니다. 

	![CM page](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr4.png)
 
1. 편집 링크를 클릭합니다. 로그인 페이지로 리디렉션됩니다. **다른 서비스를 사용하여 로그인**에서 Google 또는 Facebook을 클릭하고 이전에 등록한 계정으로 로그인합니다. 신속하게 작업하는 동안 세션 쿠키가 시간 초과되지 않으면 이전에 사용한 Google 또는 Facebook 계정을 사용하여 자동으로 로그인됩니다.
2. 해당 계정에 로그인되어 있는 동안 데이터를 편집할 수 있는지 확인합니다.
 	**참고:** 이 앱을 통해 Google에서 로그아웃한 다음 동일한 브라우저에서 다른 Google 계정으로 로그인할 수는 없습니다. 브라우저 한 개를 사용하는 경우 Google로 이동해서 로그아웃해야 합니다. 다른 브라우저에서는 Google과 같은 동일한 타사 인증자의 다른 계정으로 로그온할 수 있습니다.

Google 계정 정보의 이름과 성을 채우지 않은 경우 NullReferenceException이 발생합니다.


## SQL Azure DB 검사 ##

1. **서버 탐색기**에서 **ContactDB**로 이동합니다.
2. **ContactDB**를 마우스 오른쪽 단추로 클릭하고 **SQL Server 개체 탐색기에서 열기**를 선택합니다.
 
	![open in SSOX](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr12.png)
 
**참고:****SQL 데이터베이스**를 확장할 수 없고 Visual Studio에서 **ContactDB**를 *볼 수 없는* 경우 아래 지침에 따라 방화벽 포트나 포트 범위를 열어야 합니다. **Azure 방화벽 규칙 설정**의 지침을 따릅니다. 방화벽 규칙을 추가한 후 데이터베이스에 액세스하기 위해 몇 분 정도 기다려야 할 수도 있습니다.
 
1. **AspNetUsers** 테이블을 마우스 오른쪽 단추로 클릭하고 **데이터 보기**를 선택합니다.

	![CM page](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr8.png)
 
1. **canEdit** 역할에 추가되도록 등록한 Google 계정의 ID와 *user1@contoso.com*의 ID를 확인합니다. **canEdit** 역할에 포함된 유일한 사용자여야 합니다. 다음 단계에서 확인하겠습니다.

	![CM page](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr9.png)
 
2. **SQL Server 개체 탐색기**에서 **AspNetUserRoles**를 마우스 오른쪽 단추로 클릭하고 **데이터 보기**를 선택합니다.

	![CM page](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rs1.png)
 
**UserId**가 *user1@contoso.com* 및 등록한 Google 계정의 ID인지 확인합니다. 


## Azure 방화벽 규칙 설정 ##

Visual Studio에서 SQL Azure에 연결할 수 없거나 "서버를 열 수 없음"이라는 오류 대화 상자가 표시되면 이 섹션의 단계를 수행합니다.

![firewall error](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx5.png)

허용된 IP에 IP 주소를 추가해야 합니다.

1. Azure 포털의 왼쪽 탭에서 **SQL Databases**를 선택합니다.

	![Select SQL][rx6]

1. **ContactDB**를 클릭합니다.

1. **이 IP 주소에 대한 Azure 방화벽 규칙 설정** 링크를 클릭합니다.

	![firewall rules][rx7]

1. "현재 IP 주소 xxx.xxx.xxx.xxx이(가) 기존 방화벽 규칙에 포함되어 있지 않습니다. 방화벽 규칙을 업데이트하시겠습니까?"라는 메시지가 표시되면 **Yes**를 클릭합니다. 일부 회사 방화벽 뒤에 이 주소를 추가하는 것으로 충분하지 않은 경우가 많습니다. IP 주소 범위를 추가해야 합니다.

다음 단계는 허용된 IP 주소 범위를 추가하는 것입니다.

1. Azure 포털에서 **SQL 데이터베이스**를 클릭합니다.
1. **서버** 탭을 선택한 다음 구성하려는 서버를 클릭합니다.

	![Servers tab in Azure ](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss25.PNG)

1. **구성** 탭을 클릭합니다.

1. 규칙 이름, 시작 및 끝 IP 주소를 추가합니다.

	![ip range][rx9]

1. 페이지 맨 아래에 있는 **저장**을 클릭합니다.
1. 의견을 남기고, 연결할 IP 주소 범위를 추가해야 하는 경우 알려주세요.

이제 SSOX(SQL Server 개체 탐색기)에서 SQL 데이터베이스 인스턴스에 연결할 수 있습니다.

1. 보기 메뉴에서 **SQL Server 개체 탐색기**를 클릭합니다.
1. **SQL Server**를 마우스 오른쪽 단추로 클릭하고 **Add SQL Server**를 선택합니다.
1. **Connect to Server** 대화 상자에서 **Authentication**을 **SQL Server Authentication**으로 설정합니다. Azure 포털에서 **Server name** 및 **Login**을 가져옵니다.
1. 브라우저에서 포털로 이동한 다음 **SQL 데이터베이스**를 선택합니다.
1. **ContactDB**를 선택하고 **SQL 데이터베이스 연결 문자열 보기**를 클릭합니다.
1. **연결 문자열** 페이지에서 **서버** 및 **사용자 ID**를 복사합니다.
 
	![con string](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss21.PNG)
1. **서버** 및 **사용자 ID** 값을 Visual Studio의 **서버에 연결** 대화 상자에 전달합니다. **사용자 ID** 값이 **로그인** 항목으로 이동합니다. SQL DB를 만드는 데 사용한 암호를 입력합니다.

	![Connect to Server DLG](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rss1.png)

이제 앞에 제공된 지침에 따라 연락처 DB로 이동할 수 있습니다.


## 데이터베이스 테이블을 편집하여 canEdit 역할에 사용자를 추가하려면

자습서 앞부분에서는 코드를 사용하여 canEdit 역할에 사용자를 추가했습니다. 대체 방법은 멤버 자격 테이블의 데이터를 직접 조작하는 것입니다. 다음 단계에서는 이 대체 방법을 사용하여 역할에 사용자를 추가하는 방법을 보여 줍니다.

2. **SQL Server 개체 탐색기**에서 **AspNetUserRoles**를 마우스 오른쪽 단추로 클릭하고 **데이터 보기**를 선택합니다.

	![CM page](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rs1.png)

1. *RoleId*를 복사하여 빈(새) 행에 붙여 넣습니다.
	
	![CM page](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rs2.png)
	
2. **AspNetUsers** 테이블에서 역할에 추가할 사용자를 찾아 사용자 *Id*를 복사한 다음 **AspNetUserRoles** 테이블의 **UserId** 열에 붙여 넣습니다.

사용자 및 역할 관리를 도와줄 도구를 개발 중입니다.


<h2><a name="nextsteps"></a>다음 단계</h2>

이 샘플을 기반으로 빌드된 내 자습서를 따르세요.

1.	[로그인, 메일 확인 및 암호 재설정이 포함된 보안 ASP.NET MVC 5 웹 앱 만들기(영문)](http://www.asp.net/mvc/overview/getting-started/create-an-aspnet-mvc-5-web-app-with-email-confirmation-and-password-reset)
2.	[SMS 및 메일 2단계 인증을 사용하는 ASP.NET MVC 5 앱(영문)](http://www.asp.net/mvc/overview/getting-started/aspnet-mvc-5-app-with-sms-and-email-two-factor-authentication)
3.	[암호 및 기타 중요한 데이터를 ASP.NET 및 Azure에 배포하기 위한 모범 사례](http://www.asp.net/identity/overview/features-api/best-practices-for-deploying-passwords-and-other-sensitive-data-to-aspnet-and-azure) (영문)
4.	[Facebook과 Google OAuth2를 사용하여 ASP.NET MVC 5 앱 만들기](http://www.asp.net/mvc/tutorials/mvc-5/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on )(영문) 여기에는 사용자 등록 DB에 프로필 데이터를 추가하는 방법 및 Facebook을 인증 공급자로 사용하는 방법에 대한 지침이 포함됩니다.
5.	[ASP.NET MVC 5 시작](http://www.asp.net/mvc/tutorials/mvc-5/introduction/getting-started)

이 자습서의 맨 위에 표시된 소셜 로그인 단추를 사용하도록 설정하려면 [ASP.NET MVC 5의 소셜 로그인 단추](http://www.beabigrockstar.com/pretty-social-login-buttons-for-asp-net-mvc-5/)(영문)를 참조하세요.

Tom Dykstra의 뛰어난 [EF 및 MVC 시작](http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application)에서는 고급 MVC 및 EF 프로그래밍을 보여 줍니다.

이 자습서 및 응용 프로그램 예제는 [Rick Anderson](http://blogs.msdn.com/b/rickandy/)(Twitter [@RickAndMSFT](https://twitter.com/RickAndMSFT))에 의해 작성되었으며, Tom Dykstra 및 Barry Dorrans(Twitter [@blowdart](https://twitter.com/blowdart))의 도움을 받았습니다. 

**자습서 자체뿐 아니라 설명된 제품과 관련해서 좋아한 사항이나 바라는 개선 사항에 대한 *의견을 남겨주세요***. 사용자 의견은 개선 사항의 우선 순위를 지정하는 데 도움이 됩니다. [코드 사용 방법 보기](http://aspnet.uservoice.com/forums/228522-show-me-how-with-code)에서 새 항목을 요청하고 투표할 수도 있습니다.

<!-- bookmarks -->
[OAuth 공급자 추가]: #addOauth
[멤버 자격 API 사용]:#mbrDB
[데이터 배포 스크립트 작성]:#ppd
[멤버 자격 데이터베이스 업데이트]:#ppd2

[setupwindowsazureenv]: #bkmk_setupwindowsazure
[createapplication]: #bkmk_createmvc4app
[deployapp1]: #bkmk_deploytowindowsazure1
[deployapp11]: #bkmk_deploytowindowsazure11
[adddb]: #bkmk_addadatabase


<!-- images-->
[rx2]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx2.png

[rx5]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx5.png
[rx6]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx6.png
[rx7]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx7.png
[rx8]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx8.png
[rx9]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx9.png

[rxb]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxb.png


[rxSSL]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxSSL.png

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

[newapp004]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-createapp-004.png

[firsdeploy003]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-deploy1-publish-001.png

[adddb002]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-adddatabase-002.png
[addcode001]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-controller-add-context-menu.png

[addcode008]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-migrations-package-manager-menu.png
[addcode009]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-migrations-package-manager-console.png


[Azure 웹 사이트의 ASP.NET에 대한 중요한 정보]: #aspnetwindowsazureinfo
[다음 단계]: #nextsteps

[ImportPublishSettings]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ImportPublishSettings.png
























