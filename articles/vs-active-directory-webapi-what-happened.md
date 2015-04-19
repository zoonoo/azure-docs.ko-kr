<properties 
	pageTitle="" 
	description="Azure Active Directory 마법사를 실행 한 후 Visual Studio 프로젝트에서 무엇이 변경되었는지에 대해 설명합니다." 
	services="active-directory" 
	documentationCenter="" 
	authors="kempb" 
	manager="douge" 
	editor="tglee"/>
 
<tags 
	ms.service="active-directory" 
	ms.workload="web" 
	ms.tgt_pltfrm="vs-what-happened" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/02/2015" 
	ms.author="kempb"/>

> [AZURE.SELECTOR]
> - [시작하기](vs-active-directory-webapi-getting-started.md)
> - [변경된 내용](vs-active-directory-webapi-what-happened.md)

###<span id="whathappened">내 프로젝트에서 무엇이 변경되었습니까?</span>

참조가 추가되었습니다. 

#####NuGet 패키지 참조 

- `Microsoft.Owin`
- `Microsoft.Owin.Host.SystemWeb`
- `Microsoft.Owin.Security`
- `Microsoft.Owin.Security.ActiveDirectory`
- `Microsoft.Owin.Security.Jwt`
- `Microsoft.Owin.Security.OAuth`
- `Newtonsoft.Json`
- `Owin`
- `System.IdentityModel.Tokens.Jwt`

#####.NET 참조 

- `Microsoft.Owin`
- `Microsoft.Owin.Host.SystemWeb`
- `Microsoft.Owin.Security`
- `Microsoft.Owin.Security.ActiveDirectory`
- `Microsoft.Owin.Security.Jwt`
- `Microsoft.Owin.Security.OAuth`
- `Newtonsoft.Json`
- `Owin`
- `System.IdentityModel.Tokens.Jwt` 

#####프로젝트에 코드 파일이 추가됨 

Azure AD 인증에 대한 시작 논리가 포함된 인증 시작 클래스 **App_Start/Startup.Auth.cs**가 프로젝트에 추가되었습니다. 

#####프로젝트에 시작 코드가 추가됨 

프로젝트에 시작 클래스가 이미 있는 경우 **Configuration** 메서드가 업데이트되어 `ConfigureAuth(app)`에 대한 호출이 해당 메서드에 추가되었습니다. 그렇지 않으면 시작 클래스가 프로젝트에 추가되었습니다. 


#####app.config 또는 web.config 파일에 새 구성 값이 추가됨

다음 구성 항목이 추가되었습니다. 
	<pre>
	`<appSettings> 
    		<add key="ida:ClientId" value="ClientId from the new Azure AD App" /> 
    		<add key="ida:Tenant" value="Your selected Azure AD Tenant" /> 
    		<add key="ida:Audience" value="The App ID Uri from the wizard" /> 
	</appSettings>` </pre>

###Azure AD 앱이 만들어짐 

마법사에서 선택한 디렉터리에 Azure AD 응용 프로그램이 만들어졌습니다.


[Azure Active Directory에 대한 자세한 정보](http://azure.microsoft.com/services/active-directory/)

<!--HONumber=46--> 
