<properties title="Getting Started with Active Directory Authentication - What Happened" pageTitle="Active Directory Authentication 시작 - 변경된 내용" metaKeywords="Azure, Getting Started, Active Directory" description="" services="active-directory" documentationCenter="" authors="ghogen, kempb" />
  
<tags ms.service="active-directory" ms.workload="web" ms.tgt_pltfrm="vs-what-happened" ms.devlang="na" ms.topic="article" ms.date="10/8/2014" ms.author="ghogen, kempb" />

> [AZURE.SELECTOR]
> - [시작하기](/documentation/articles/vs-active-directory-dotnet-getting-started/)
> - [변경된 내용](/documentation/articles/vs-active-directory-dotnet-what-happened/)

###<span id="whathappened">내 프로젝트에서 무엇이 변경되었습니까?</span>
 
참조가 추가되었습니다.

#####NuGet 패키지 참조

- `Microsoft.IdentityModel.Protocol.Extensions`
- `Microsoft.Owin`
- `Microsoft.Owin.Host.SystemWeb`
- `Microsoft.Owin.Security`
- `Microsoft.Owin.Security.Cookies`
- `Microsoft.Owin.Security.OpenIdConnect`
- `Owin`
- `System.IdentityModel.Tokens.Jwt`

#####.NET 참조

- `Microsoft.IdentityModel.Protocol.Extensions`
- `Microsoft.Owin`
- `Microsoft.Owin.Host.SystemWeb`
- `Microsoft.Owin.Security`
- `Microsoft.Owin.Security.Cookies`
- `Microsoft.Owin.Security.OpenIdConnect`
- `Owin`
- `System`
- `System.Data`
- `System.Drawing`
- `System.IdentityModel`
- `System.IdentityModel.Tokens.Jwt`
- `System.Runtime.Serialization`

#####프로젝트에 코드 파일이 추가됨 

Azure AD 인증에 대한 시작 논리가 포함된 인증 시작 클래스 `App_Start/Startup.Auth.cs`가 프로젝트에 추가되었습니다. 또한 `SignIn()` 및 `SignOut()` 메서드를 포함하는 컨트롤러 클래스 Controllers/AccountController.cs가 추가되었습니다. 마지막으로 SignIn/SignOut에 대한 작업 링크를 포함하는 부분 뷰 `Views/Shared/_LoginPartial.cshtml`이 추가되었습니다. 

#####프로젝트에 시작 코드가 추가됨
 
프로젝트에 시작 클래스가 이미 있는 경우 **Configuration** 메서드가 업데이트되어 `ConfigureAuth(app)`에 대한 호출이 해당 메서드에 추가되었습니다. 그렇지 않으면 시작 클래스가 프로젝트에 추가되었습니다. 

#####app.config 또는 web.config에 새 구성 값이 추가됨 

다음 구성 항목이 추가되었습니다. 
	<pre>
	`<appSettings>
	    <add key="ida:ClientId" value="ClientId from the new Azure AD App" /> 
	    <add key="ida:Tenant" value="Your selected Azure AD Tenant" /> 
	    <add key="ida:AADInstance" value="https://login.windows.net/{0}" /> 
	    <add key="Ida:PostLogoutRedirectURI" value="Your project start page" /> 
	</appSettings>` </pre>

#####Azure AD(Active Directory) 앱이 만들어짐 
마법사에서 선택한 디렉터리에 Azure AD 응용 프로그램이 만들어졌습니다. 

[Azure Active Directory에 대한 자세한 정보](http://azure.microsoft.com/services/active-directory/)
