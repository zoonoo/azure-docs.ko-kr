---
title: "Azure AD에 연결할 때의 WebApi 프로젝트 변경 내용 | Microsoft Docs"
description: "Visual Studio를 사용하여 Azure AD에 연결할 때 프로젝트 WebApi에서 변경되는 사항에 대해 설명합니다."
services: active-directory
documentationcenter: 
author: kraigb
manager: ghogen
editor: 
ms.assetid: 57630aee-26a2-4326-9dbb-ea2a66daa8b0
ms.service: active-directory
ms.workload: web
ms.tgt_pltfrm: vs-what-happened
ms.devlang: na
ms.topic: article
ms.date: 03/01/2017
ms.author: kraigb
ms.custom: aaddev
ms.translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 8ca3234e54d8736aa187195640a103ac6eb145e7
ms.contentlocale: ko-kr
ms.lasthandoff: 03/21/2017

---
# <a name="what-happened-to-my-webapi-project-visual-studio-azure-active-directory-connected-service"></a>내 WebApi 프로젝트(Visual Studio Azure Active Directory 연결 서비스)의 변경 내용
> [!div class="op_single_selector"]
> * [시작](vs-active-directory-webapi-getting-started.md)
> * [변경된 내용](vs-active-directory-webapi-what-happened.md)
> 
> 

## <a name="references-have-been-added"></a>참조가 추가됨
### <a name="nuget-package-references"></a>NuGet 패키지 참조
* `Microsoft.Owin`
* `Microsoft.Owin.Host.SystemWeb`
* `Microsoft.Owin.Security`
* `Microsoft.Owin.Security.ActiveDirectory`
* `Microsoft.Owin.Security.Jwt`
* `Microsoft.Owin.Security.OAuth`
* `Owin`
* `System.IdentityModel.Tokens.Jwt`

### <a name="net-references"></a>.NET 참조
* `Microsoft.Owin`
* `Microsoft.Owin.Host.SystemWeb`
* `Microsoft.Owin.Security`
* `Microsoft.Owin.Security.ActiveDirectory`
* `Microsoft.Owin.Security.Jwt`
* `Microsoft.Owin.Security.OAuth`
* `Owin`
* `System.IdentityModel.Tokens.Jwt`

## <a name="code-changes"></a>코드 변경 내용
### <a name="code-files-were-added-to-your-project"></a>프로젝트에 코드 파일이 추가됨
Azure AD 인증에 대한 시작 논리가 포함된 인증 시작 클래스 **App_Start/Startup.Auth.cs**가 프로젝트에 추가되었습니다.

### <a name="startup-code-was-added-to-your-project"></a>프로젝트에 시작 코드가 추가됨
프로젝트에 시작 클래스가 이미 있는 경우 **Configuration** 메서드가 업데이트되어 `ConfigureAuth(app)`에 대한 호출이 해당 메서드에 업데이트되었습니다. 그렇지 않으면 시작 클래스가 프로젝트에 추가되었습니다.

### <a name="your-appconfig-or-webconfig-file-has-new-configuration-values"></a>app.config 또는 web.config 파일에 새 구성 값이 추가됨
다음 구성 항목이 추가되었습니다.

```
    <appSettings>
            <add key="ida:ClientId" value="ClientId from the new Azure AD App" />
            <add key="ida:Tenant" value="Your selected Azure AD Tenant" />
            <add key="ida:Audience" value="The App ID Uri from the wizard" />
    </appSettings>`
```

### <a name="an-azure-ad-app-was-created"></a>Azure AD 앱이 만들어짐
마법사에서 선택한 디렉터리에 Azure AD 응용 프로그램이 만들어졌습니다.

[Azure Active Directory에 대한 자세한 정보](https://azure.microsoft.com/services/active-directory/)

## <a name="if-i-checked-disable-individual-user-accounts-authentication-what-additional-changes-were-made-to-my-project"></a>*개별 사용자 계정 인증을 사용하지 않도록 설정*한 경우 내 프로젝트에 추가된 변경 내용은 무엇인가요?
NuGet 패키지 참조가 제거되고 파일이 제거 및 백업되었습니다. 프로젝트의 상태에 따라 수동으로 추가 참조 또는 파일을 제거하거나 적절하게 코드를 수정해야 할 수도 있습니다.

### <a name="nuget-package-references-removed-for-those-present"></a>NuGet 패키지 참조가 제거됨(해당되는 경우)
* `Microsoft.AspNet.Identity.Core`
* `Microsoft.AspNet.Identity.EntityFramework`
* `Microsoft.AspNet.Identity.Owin`

### <a name="code-files-backed-up-and-removed-for-those-present"></a>코드 파일이 백업 및 제거됨(해당되는 경우)
다음 파일이 각각 프로젝트에서 백업 및 제거되었습니다. 백업 파일은 프로젝트 디렉터리의 루트에 있는 'Backup' 폴더에 있습니다.

* `App_Start\IdentityConfig.cs`
* `Controllers\AccountController.cs`
* `Controllers\ManageController.cs`
* `Models\IdentityModels.cs`
* `Providers\ApplicationOAuthProvider.cs`

### <a name="code-files-backed-up-for-those-present"></a>코드 파일이 백업됨(해당되는 경우)
교체 전에 다음 파일이 각각 백업되었습니다. 백업 파일은 프로젝트 디렉터리의 루트에 있는 'Backup' 폴더에 있습니다.

* `Startup.cs`
* `App_Start\Startup.Auth.cs`

## <a name="if-i-checked-read-directory-data-what-additional-changes-were-made-to-my-project"></a>*디렉터리 데이터 읽기*를 선택한 경우 내 프로젝트에 추가된 변경 내용은 무엇인가요?
### <a name="additional-changes-were-made-to-your-appconfig-or-webconfig"></a>app.config 또는 web.config에 변경 내용 추가됨
다음 추가 구성 항목이 추가되었습니다.

```
    <appSettings>
        <add key="ida:Password" value="Your Azure AD App's new password" />
    </appSettings>`
```

### <a name="your-azure-active-directory-app-was-updated"></a>Azure Active Directory 앱이 업데이트됨
Azure Active Directory 앱이 *디렉터리 데이터 읽기* 권한을 포함하도록 업데이트되었으며, 추가 키가 생성되어 `web.config` 파일에서 *ida:Password*로 사용되었습니다.

## <a name="next-steps"></a>다음 단계
- [Azure Active Directory에 대한 자세한 정보](https://azure.microsoft.com/services/active-directory/)


