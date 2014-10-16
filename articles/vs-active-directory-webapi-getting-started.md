<properties title="Getting Started with Active Directory Authentication" pageTitle="" metaKeywords="Azure, Getting Started, Active Directory" description="" services="active-directory" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="active-directory" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/8/2014" ms.author="ghogen, kempb"></tags>

### 변경된 내용

프로젝트에 참조가 추가됨

###### NuGet 패키지 참조

Microsoft.Owin, Microsoft.Owin.Host.SystemWeb, Microsoft.Owin.Security, Microsoft.Owin.Security.ActiveDirectory, Microsoft.Owin.Security.Jwt, Microsoft.Owin.Security.OAuth, Newtonsoft.Json, Owin, System.IdentityModel.Tokens.Jwt

###### .NET 참조

Microsoft.Owin, Microsoft.Owin.Host.SystemWeb, Microsoft.Owin.Security, Microsoft.Owin.Security.ActiveDirectory, Microsoft.Owin.Security.Jwt, Microsoft.Owin.Security.OAuth, Newtonsoft.Json, Owin, System.IdentityModel.Tokens.Jwt

###### 프로젝트에 코드 파일이 추가됨

Azure AD 인증에 대한 시작 논리가 포함된 인증 시작 클래스 App\_Start/Startup.Auth.cs가 프로젝트에 추가되었습니다.

###### 프로젝트에 시작 코드가 추가됨

프로젝트에 시작 클래스가 이미 있는 경우 Configuration() 메서드가 업데이트되어 ConfigureAuth(app)에 대한 호출이 해당 메서드에 추가되었습니다. 그렇지 않으면 시작 클래스가 프로젝트에 추가되었습니다.

###### app.config 또는 web.config 파일에 새 구성 값이 추가됨

다음 구성 항목이 추가되었습니다.
	<pre>
	`<appSettings> 
    		<add key="ida:ClientId" value="ClientId from the new Azure AD App" /> 
    		<add key="ida:Tenant" value="Your selected Azure AD Tenant" /> 
    		<add key="ida:Audience" value="The App ID Uri from the wizard" /> 
	</appSettings>` </pre>
### Azure AD(Active Directory) 앱이 만들어짐

마법사에서 선택한 디렉터리에 Azure AD 응용 프로그램이 만들어졌습니다.

### Azure AD(Active Directory) 시작

추가된 코드로 할 수 있는 작업은 다음과 같습니다.

###### 컨트롤러에 액세스하려면 인증 필요

프로젝트의 모든 컨트롤러는 Authorize 특성으로 표시되었습니다. 이 특성으로 인해 사용자가 인증해야만 이러한 컨트롤러에서 정의한 API에 액세스할 수 있습니다. 컨트롤러에 익명으로 액세스할 수 있도록 하려면 컨트롤러에서 이 특성을 제거하세요. 더 세부적인 수준에서 권한을 설정하려면 특성을 컨트롤러 클래스에 적용하는 대신 인증이 필요한 각 메서드에 적용하세요.

