<properties title="Active Directory Authentication 시작" pageTitle="" metaKeywords="Azure, Getting Started, Active Directory" description="" services="active-directory" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="active-directory" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/8/2014" ms.author="ghogen, kempb"></tags>

> [AZURE.SELECTOR]
>
> -   [시작하기][시작하기]
> -   [변경된 내용][변경된 내용]

### <span id="whathappened">내 프로젝트에서 무엇이 변경되었습니까?</span>

참조가 추가되었습니다.

##### NuGet 패키지 참조

-   `Microsoft.Owin`
-   `Microsoft.Owin.Host.SystemWeb`
-   `Microsoft.Owin.Security`
-   `Microsoft.Owin.Security.ActiveDirectory`
-   `Microsoft.Owin.Security.Jwt`
-   `Microsoft.Owin.Security.OAuth`
-   `Newtonsoft.Json`
-   `Owin`
-   `System.IdentityModel.Tokens.Jwt`

##### .NET 참조

-   `Microsoft.Owin`
-   `Microsoft.Owin.Host.SystemWeb`
-   `Microsoft.Owin.Security`
-   `Microsoft.Owin.Security.ActiveDirectory`
-   `Microsoft.Owin.Security.Jwt`
-   `Microsoft.Owin.Security.OAuth`
-   `Newtonsoft.Json`
-   `Owin`
-   `System.IdentityModel.Tokens.Jwt`

##### 프로젝트에 코드 파일이 추가됨

Azure AD 인증에 대한 시작 논리가 포함된 인증 시작 클래스 **App\_Start/Startup.Auth.cs**가 프로젝트에 추가되었습니다.

##### 프로젝트에 시작 코드가 추가됨

프로젝트에 시작 클래스가 이미 있는 경우 **Configuration** 메서드가 업데이트되어 `ConfigureAuth(app)`에 대한 호출이 해당 메서드에 추가되었습니다. 그렇지 않으면 시작 클래스가 프로젝트에 추가되었습니다.

##### app.config 또는 web.config 파일에 새 구성 값이 추가됨

다음 구성 항목이 추가되었습니다.

     <appSettings>              <add key="ida:ClientId" value="ClientId from the new Azure AD App" />              <add key="ida:Tenant" value="Your selected Azure AD Tenant" />              <add key="ida:Audience" value="The App ID Uri from the wizard" />      </appSettings> 

</p>
### Azure AD 앱이 만들어짐

마법사에서 선택한 디렉터리에 Azure AD 응용 프로그램이 만들어졌습니다.

[Azure Active Directory에 대한 자세한 정보][Azure Active Directory에 대한 자세한 정보]

  [시작하기]: /documentation/articles/vs-active-directory-webapi-getting-started/
  [변경된 내용]: /documentation/articles/vs-active-directory-webapi-what-happened/
  [Azure Active Directory에 대한 자세한 정보]: http://azure.microsoft.com/services/active-directory/
