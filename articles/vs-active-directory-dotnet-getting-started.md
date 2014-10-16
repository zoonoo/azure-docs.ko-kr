<properties title="Getting Started with Active Directory Authentication" pageTitle="" metaKeywords="Azure, Getting Started, Active Directory" description="" services="active-directory" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="active-directory" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/8/2014" ms.author="ghogen, kempb"></tags>

### 변경된 내용

프로젝트에 참조가 추가됨

###### NuGet 패키지 참조

Microsoft.IdentityModel.Protocol.Extensions, Microsoft.Owin, Microsoft.Owin.Host.SystemWeb, Microsoft.Owin.Security, Microsoft.Owin.Security.Cookies, Microsoft.Owin.Security.OpenIdConnect, Owin, System.IdentityModel.Tokens.Jwt

###### .NET 참조

Microsoft.IdentityModel.Protocol.Extensions, Microsoft.Owin, Microsoft.Owin.Host.SystemWeb, Microsoft.Owin.Security, Microsoft.Owin.Security.Cookies, Microsoft.Owin.Security.OpenIdConnect, Owin, System, System.Data, System.Drawing, System.IdentityModel, System.IdentityModel.Tokens.Jwt, System.Runtime.Serialization

###### 프로젝트에 코드 파일이 추가됨

Azure AD 인증에 대한 시작 논리가 포함된 인증 시작 클래스 App\_Start/Startup.Auth.cs가 프로젝트에 추가되었습니다. 또한 SignIn() 및 SignOut() 메서드를 포함하는 컨트롤러 클래스 Controllers/AccountController.cs가 추가되었습니다. 마지막으로 SignIn/SignOut에 대한 작업 링크를 포함하는 부분 뷰 Views/Shared/\_LoginPartial.cshtml이 추가되었습니다.

###### 프로젝트에 시작 코드가 추가됨

프로젝트에 시작 클래스가 이미 있는 경우 Configuration() 메서드가 업데이트되어 ConfigureAuth(app)에 대한 호출이 해당 메서드에 추가되었습니다. 그렇지 않으면 시작 클래스가 프로젝트에 추가되었습니다.

###### app.config 또는 web.config에 새 구성 값이 추가됨

다음 구성 항목이 추가되었습니다.
	<pre>
	`<appSettings>
	    <add key="ida:ClientId" value="ClientId from the new Azure AD App" /> 
	    <add key="ida:Tenant" value="Your selected Azure AD Tenant" /> 
	    <add key="ida:AADInstance" value="https://login.windows.net/{0}" /> 
	    <add key="Ida:PostLogoutRedirectURI" value="Your project start page" /> 
	</appSettings>` </pre>
###### Azure AD(Active Directory) 앱이 만들어짐

마법사에서 선택한 디렉터리에 Azure AD 응용 프로그램이 만들어졌습니다.

## Azure AD(Active Directory) 시작

추가된 코드로 할 수 있는 작업은 다음과 같습니다.

###### 컨트롤러에 액세스하려면 인증 필요

프로젝트의 모든 컨트롤러는 [Authorize] 특성으로 표시되었습니다. 이 특성으로 인해 사용자가 인증해야만 이러한 컨트롤러에 액세스할 수 있습니다. 컨트롤러에 익명으로 액세스할 수 있도록 하려면 컨트롤러에서 이 특성을 제거하세요.

###### SignIn/SignOut 컨트롤 추가

뷰에 SignIn/SignOut 컨트롤을 추가하려면 \_LoginPartial.cshtml 부분 뷰를 사용하여 뷰 중 하나에 기능을 추가할 수 있습니다. 다음은 표준 \_Layout.cshtml 뷰에 추가한 기능의 예입니다.
	<pre> 
	`<!DOCTYPE html> 
	<html> 
	<head> 
	    <meta charset="utf-8" /> 
	    <meta name="viewport" content="width=device-width, initial-scale=1.0"> 
	    <title>@ViewBag.Title - My ASP.NET Application</title> 
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
	                @Html.ActionLink("Application name", "Index", "Home", new { area = "" }, new { @class = "navbar-brand" }) 
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
	            <p>&copy; @DateTime.Now.Year - My ASP.NET Application</p> 
	        </footer> 
	    </div> 
	    @Scripts.Render("~/bundles/jquery") 
	    @Scripts.Render("~/bundles/bootstrap") 
	    @RenderSection("scripts", required: false) 
	</body> 
	</html>` </pre>

