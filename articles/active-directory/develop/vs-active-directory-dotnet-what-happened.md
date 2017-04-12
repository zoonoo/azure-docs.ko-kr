---
title: "Azure AD에 연결할 때의 MVC 프로젝트 변경 내용 | Microsoft Docs"
description: "Visual Studio 연결된 서비스를 사용하여 Azure AD에 연결할 때 MVC 프로젝트의 변경 내용을 설명합니다."
services: active-directory
documentationcenter: na
author: TomArcher
manager: douge
editor: 
ms.assetid: 8b24adde-547e-4ffe-824a-2029ba210216
ms.service: active-directory
ms.workload: web
ms.tgt_pltfrm: vs-what-happened
ms.devlang: na
ms.topic: article
ms.date: 03/01/2017
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 72cd94ba16cb4fe234c898b093c7de6a08f71239
ms.lasthandoff: 03/21/2017


---
# <a name="what-happened-to-my-mvc-project-visual-studio-azure-active-directory-connected-service"></a>내 MVC 프로젝트(Visual Studio Azure Active Directory 연결된 서비스)의 변경 내용
> [!div class="op_single_selector"]
> * [시작](vs-active-directory-dotnet-getting-started.md)
> * [변경된 내용](vs-active-directory-dotnet-what-happened.md)
> 
> 

## <a name="references-have-been-added"></a>참조가 추가됨
### <a name="nuget-package-references"></a>NuGet 패키지 참조
* **Microsoft.IdentityModel.Protocol.Extensions**
* **Microsoft.Owin**
* **Microsoft.Owin.Host.SystemWeb**
* **Microsoft.Owin.Security**
* **Microsoft.Owin.Security.Cookies**
* **Microsoft.Owin.Security.OpenIdConnect**
* **Owin**
* **System.IdentityModel.Tokens.Jwt**

### <a name="net-references"></a>.NET 참조
* **Microsoft.IdentityModel.Protocol.Extensions**
* **Microsoft.Owin**
* **Microsoft.Owin.Host.SystemWeb**
* **Microsoft.Owin.Security**
* **Microsoft.Owin.Security.Cookies**
* **Microsoft.Owin.Security.OpenIdConnect**
* **Owin**
* **System.IdentityModel**
* **System.IdentityModel.Tokens.Jwt**
* **System.Runtime.Serialization**

## <a name="code-has-been-added"></a>코드가 추가됨
### <a name="code-files-were-added-to-your-project"></a>프로젝트에 코드 파일이 추가됨
Azure AD 인증에 대한 시작 논리가 포함된 인증 시작 클래스 **App_Start/Startup.Auth.cs**가 프로젝트에 추가되었습니다. 또한 **SignIn()** 및 **SignOut()** 메서드를 포함하는 컨트롤러 클래스 Controllers/AccountController.cs가 추가되었습니다. 마지막으로 SignIn/SignOut에 대한 작업 링크를 포함하는 부분 뷰 **Views/Shared/_LoginPartial.cshtml**이 추가되었습니다.

### <a name="startup-code-was-added-to-your-project"></a>프로젝트에 시작 코드가 추가됨
프로젝트에 시작 클래스가 이미 있는 경우 **Configuration** 메서드가 업데이트되어 **ConfigureAuth(app)**에 대한 호출이 해당 메서드에 업데이트되었습니다. 그렇지 않으면 시작 클래스가 프로젝트에 추가되었습니다.

### <a name="your-appconfig-or-webconfig-has-new-configuration-values"></a>app.config 또는 web.config에 새 구성 값이 추가됨
다음 구성 항목이 추가되었습니다.

    <appSettings>
        <add key="ida:ClientId" value="ClientId from the new Azure AD App" />
        <add key="ida:AADInstance" value="https://login.microsoftonline.com/" />
        <add key="ida:Domain" value="The selected Azure AD Domain" />
        <add key="ida:TenantId" value="The Id of your selected Azure AD Tenant" />
        <add key="ida:PostLogoutRedirectUri" value="Your project start page" />
    </appSettings>

### <a name="an-azure-active-directory-ad-app-was-created"></a>Azure AD(Active Directory) 앱이 만들어짐
마법사에서 선택한 디렉터리에 Azure AD 응용 프로그램이 만들어졌습니다.

## <a name="if-i-checked-disable-individual-user-accounts-authentication-what-additional-changes-were-made-to-my-project"></a>*개별 사용자 계정 인증을 사용하지 않도록 설정*한 경우 내 프로젝트에 추가된 변경 내용은 무엇인가요?
NuGet 패키지 참조가 제거되고 파일이 제거 및 백업되었습니다. 프로젝트의 상태에 따라 수동으로 추가 참조 또는 파일을 제거하거나 적절하게 코드를 수정해야 할 수도 있습니다.

### <a name="nuget-package-references-removed-for-those-present"></a>NuGet 패키지 참조가 제거됨(해당되는 경우)
* **Microsoft.AspNet.Identity.Core**
* **Microsoft.AspNet.Identity.EntityFramework**
* **Microsoft.AspNet.Identity.Owin**

### <a name="code-files-backed-up-and-removed-for-those-present"></a>코드 파일이 백업 및 제거됨(해당되는 경우)
다음 파일이 각각 프로젝트에서 백업 및 제거되었습니다. 백업 파일은 프로젝트 디렉터리의 루트에 있는 'Backup' 폴더에 있습니다.

* **App_Start\IdentityConfig.cs**
* **Controllers\ManageController.cs**
* **Models\IdentityModels.cs**
* **Models\ManageViewModels.cs**

### <a name="code-files-backed-up-for-those-present"></a>코드 파일이 백업됨(해당되는 경우)
교체 전에 다음 파일이 각각 백업되었습니다. 백업 파일은 프로젝트 디렉터리의 루트에 있는 'Backup' 폴더에 있습니다.

* **Startup.cs**
* **App_Start\Startup.Auth.cs**
* **Controllers\AccountController.cs**
* **Views\Shared\_LoginPartial.cshtml**

## <a name="if-i-checked-read-directory-data-what-additional-changes-were-made-to-my-project"></a>*디렉터리 데이터 읽기*를 선택한 경우 내 프로젝트에 추가된 변경 내용은 무엇인가요?
추가 참조가 추가되었습니다.

### <a name="additional-nuget-package-references"></a>추가 NuGet 패키지 참조
* **EntityFramework**
* **Microsoft.Azure.ActiveDirectory.GraphClient**
* **Microsoft.Data.Edm**
* **Microsoft.Data.OData**
* **Microsoft.Data.Services.Client**
* **Microsoft.IdentityModel.Clients.ActiveDirectory**
* **System.Spatial**

### <a name="additional-net-references"></a>추가 .NET 참조
* **EntityFramework**
* **EntityFramework.SqlServer**
* **Microsoft.Azure.ActiveDirectory.GraphClient**
* **Microsoft.Data.Edm**
* **Microsoft.Data.OData**
* **Microsoft.Data.Services.Client**
* **Microsoft.IdentityModel.Clients.ActiveDirectory**
* **Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms**
* **System.Spatial**

### <a name="additional-code-files-were-added-to-your-project"></a>프로젝트에 추가 코드 파일이 추가됨
토큰 캐싱을 지원하기 위해 두 파일 **Models\ADALTokenCache.cs** 및 **Models\ApplicationDbContext.cs**가 추가되었습니다.  추가 컨트롤러와 뷰는 Azure graph API를 사용하여 액세스 사용자 프로필 정보를 설명하기 위해 추가되었습니다.  해당 파일은 **Controllers\UserProfileController.cs** 및 **Views\UserProfile\Index.cshtml**입니다.

### <a name="additional-startup-code-was-added-to-your-project"></a>프로젝트에 추가 시작 코드가 추가됨
**startup.auth.cs** 파일에서 **OpenIdConnectAuthenticationOptions**의 **Notifications** 멤버에 **OpenIdConnectAuthenticationNotifications** 개체가 추가되었습니다.  이를 통해 OAuth 코드를 받고 액세스 토큰에 대해 교환할 수 있습니다.

### <a name="additional-changes-were-made-to-your-appconfig-or-webconfig"></a>app.config 또는 web.config에 변경 내용 추가됨
다음 추가 구성 항목이 추가되었습니다.

    <appSettings>
        <add key="ida:ClientSecret" value="Your Azure AD App's new client secret" />
    </appSettings>

다음 구성 섹션 및 연결 문자열이 추가되었습니다.

    <configSections>
        <!-- For more information on Entity Framework configuration, visit http://go.microsoft.com/fwlink/?LinkID=237468 -->
        <section name="entityFramework" type="System.Data.Entity.Internal.ConfigFile.EntityFrameworkSection, EntityFramework, Version=6.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" requirePermission="false" />
    </configSections>
    <connectionStrings>
        <add name="DefaultConnection" connectionString="Data Source=(localdb)\MSSQLLocalDB;AttachDbFilename=|DataDirectory|\aspnet-[AppName + Generated Id].mdf;Initial Catalog=aspnet-[AppName + Generated Id];Integrated Security=True" providerName="System.Data.SqlClient" />
    </connectionStrings>
    <entityFramework>
        <defaultConnectionFactory type="System.Data.Entity.Infrastructure.LocalDbConnectionFactory, EntityFramework">
          <parameters>
            <parameter value="mssqllocaldb" />
          </parameters>
        </defaultConnectionFactory>
        <providers>
          <provider invariantName="System.Data.SqlClient" type="System.Data.Entity.SqlServer.SqlProviderServices, EntityFramework.SqlServer" />
        </providers>
    </entityFramework>


### <a name="your-azure-active-directory-app-was-updated"></a>Azure Active Directory 앱이 업데이트됨
Azure Active Directory 앱이 *디렉터리 데이터 읽기* 권한을 포함하도록 업데이트되었으며, 추가 키가 생성되어 **web.config** 파일에서 *ida:ClientSecret*으로 사용되었습니다.

## <a name="next-steps"></a>다음 단계
- [Azure Active Directory에 대한 자세한 정보](https://azure.microsoft.com/services/active-directory/)


