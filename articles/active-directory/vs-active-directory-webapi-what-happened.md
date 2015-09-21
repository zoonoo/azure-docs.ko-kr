<properties pageTitle="What happened to my WebApi project (Visual Studio Azure Active Directory connected service) |Microsoft Azure " description="Describes what happens to your MVC project WebApi you connect to Azure AD by using Visual Studio services="active-directory" services="active-directory" documentationCenter="" authors="patshea123" manager="douge" editor="tglee"/>

<tags
	ms.service="active-directory"
	ms.workload="web"
	ms.tgt_pltfrm="vs-what-happened"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/03/2015"
	ms.author="patshea"/>

# 내 WebApi 프로젝트(Visual Studio Azure Active Directory 연결 서비스)의 변경 내용

> [AZURE.SELECTOR]
> - [Getting Started](vs-active-directory-webapi-getting-started.md)
> - [What Happened](vs-active-directory-webapi-what-happened.md)

##참조가 추가됨

###NuGet 패키지 참조

- `Microsoft.Owin`
- `Microsoft.Owin.Host.SystemWeb`
- `Microsoft.Owin.Security`
- `Microsoft.Owin.Security.ActiveDirectory`
- `Microsoft.Owin.Security.Jwt`
- `Microsoft.Owin.Security.OAuth`
- `Owin`
- `System.IdentityModel.Tokens.Jwt`

###.NET 참조

- `Microsoft.Owin`
- `Microsoft.Owin.Host.SystemWeb`
- `Microsoft.Owin.Security`
- `Microsoft.Owin.Security.ActiveDirectory`
- `Microsoft.Owin.Security.Jwt`
- `Microsoft.Owin.Security.OAuth`
- `Owin`
- `System.IdentityModel.Tokens.Jwt`

##코드 변경 내용

###프로젝트에 코드 파일이 추가됨

Azure AD 인증에 대한 시작 논리가 포함된 인증 시작 클래스 **App\_Start/Startup.Auth.cs**가 프로젝트에 추가되었습니다.

###프로젝트에 시작 코드가 추가됨

프로젝트에 시작 클래스가 이미 있는 경우 **Configuration** 메서드가 업데이트되어 `ConfigureAuth(app)`에 대한 호출이 해당 메서드에 업데이트되었습니다. 그렇지 않으면 시작 클래스가 프로젝트에 추가되었습니다.


###app.config 또는 web.config 파일에 새 구성 값이 추가됨

다음 구성 항목이 추가되었습니다. ```
	`<appSettings>
    		<add key="ida:ClientId" value="ClientId from the new Azure AD App" />
    		<add key="ida:Tenant" value="Your selected Azure AD Tenant" />
    		<add key="ida:Audience" value="The App ID Uri from the wizard" />
	</appSettings>`
```

###Azure AD 앱이 만들어짐

마법사에서 선택한 디렉터리에 Azure AD 응용 프로그램이 만들어졌습니다.

[Azure Active Directory에 대한 자세한 정보](http://azure.microsoft.com/services/active-directory/)

##*개별 사용자 계정 인증을 사용하지 않도록 설정*한 경우 내 프로젝트에 추가된 변경 내용은 무엇인가요?
NuGet 패키지 참조가 제거되고 파일이 제거 및 백업되었습니다. 프로젝트의 상태에 따라 수동으로 추가 참조 또는 파일을 제거하거나 적절하게 코드를 수정해야 할 수도 있습니다.

###NuGet 패키지 참조가 제거됨(해당되는 경우)

- `Microsoft.AspNet.Identity.Core`
- `Microsoft.AspNet.Identity.EntityFramework`
- `Microsoft.AspNet.Identity.Owin`

###코드 파일이 백업 및 제거됨(해당되는 경우)

다음 파일이 각각 프로젝트에서 백업 및 제거되었습니다. 백업 파일은 프로젝트 디렉터리의 루트에 있는 'Backup' 폴더에 있습니다.

- `App_Start\IdentityConfig.cs`
- `Controllers\AccountController.cs`
- `Controllers\ManageController.cs`
- `Models\IdentityModels.cs`
- `Providers\ApplicationOAuthProvider.cs`

###코드 파일이 백업됨(해당되는 경우)

교체 전에 다음 파일이 각각 백업되었습니다. 백업 파일은 프로젝트 디렉터리의 루트에 있는 'Backup' 폴더에 있습니다.

- `Startup.cs`
- `App_Start\Startup.Auth.cs`

##*디렉터리 데이터 읽기*를 선택한 경우 내 프로젝트에 추가된 변경 내용은 무엇인가요?

###app.config 또는 web.config에 변경 내용 추가됨

다음 추가 구성 항목이 추가되었습니다.

```
	`<appSettings>
	    <add key="ida:Password" value="Your Azure AD App's new password" />
	</appSettings>`
```

###Azure Active Directory 앱이 업데이트됨
Azure Active Directory 앱이 *디렉터리 데이터 읽기* 권한을 포함하도록 업데이트되었으며, 추가 키가 생성되어 `web.config` 파일에서 *ida:Password*로 사용되었습니다.

[Azure Active Directory에 대한 자세한 정보](http://azure.microsoft.com/services/active-directory/)

<!---HONumber=Sept15_HO2-->