---
title: Azure AD에 연결할 때 WebAPI 프로젝트에 대 한 변경 내용
description: Visual Studio를 사용 하 여 Azure AD에 연결할 때 WebAPI 프로젝트의 상황을 설명 합니다.
author: ghogen
manager: jillfra
ms.assetid: 57630aee-26a2-4326-9dbb-ea2a66daa8b0
ms.workload: azure-vs
ms.prod: visual-studio-windows
ms.technology: vs-azure
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev, vs-azure
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3a0cf375902281817ecc002f8fb76be32eed149d
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74966319"
---
# <a name="what-happened-to-my-webapi-project-visual-studio-azure-active-directory-connected-service"></a>내 WebAPI 프로젝트 (Visual Studio Azure Active Directory 연결 된 서비스)의 변경 내용

> [!div class="op_single_selector"]
> - [Első lépések](vs-active-directory-webapi-getting-started.md)
> - [무슨 일이 있었나요](vs-active-directory-webapi-what-happened.md)

이 문서에서는 [Visual Studio를 사용 하 여 Azure Active Directory 연결 된 서비스](vs-active-directory-add-connected-service.md)를 추가할 때 ASP.NET WebAPI, ASP.NET 단일 페이지 응용 프로그램 및 ASP.NET Azure API 프로젝트에 대 한 정확한 변경 내용을 확인 합니다. Visual Studio 2015의 ASP.NET Azure 모바일 서비스 프로젝트에도 적용 됩니다.

연결 된 서비스 작업에 대 한 자세한 내용은 [시작](vs-active-directory-webapi-getting-started.md)을 참조 하세요.

## <a name="added-references"></a>추가 된 참조

프로젝트 파일 * .NET 참조) 및 `packages.config` (NuGet 참조)에 영향을 줍니다.

| Type (Típus) | Leírások |
| --- | --- |
| 선택망 NuGet | Owin |
| 선택망 NuGet | Microsoft.Owin.Host.SystemWeb |
| 선택망 NuGet | Owin |
| 선택망 NuGet | Owin. ActiveDirectory |
| 선택망 NuGet | Owin. Jwt |
| 선택망 NuGet | Microsoft.Owin.Security.OAuth |
| 선택망 NuGet | Owin |
| 선택망 NuGet | System.identitymodel. Jwt |

**디렉터리 데이터 읽기** 옵션을 선택한 경우 추가 참조:

| Type (Típus) | Leírások |
| --- | --- |
| 선택망 NuGet | EntityFramework |
| .NET        | EntityFramework. SqlServer (Visual Studio 2015에만 해당) |
| 선택망 NuGet | ActiveDirectory 클라이언트입니다. |
| 선택망 NuGet | Microsoft.Data.Edm |
| 선택망 NuGet | Microsoft.Data.OData |
| 선택망 NuGet | Microsoft. Data. Service. 클라이언트 |
| 선택망 NuGet | System.identitymodel. ActiveDirectory |
| .NET        | System.identitymodel. ActiveDirectory.<br>(Visual Studio 2015에만 해당) |
| 선택망 NuGet | System.Spatial |

다음 참조가 제거 됩니다 (Visual Studio 2015에서와 같이 ASP.NET 4 프로젝트에만 해당).

| Type (Típus) | Leírások |
| --- | --- |
| 선택망 NuGet | Microsoft. 항등. |
| 선택망 NuGet | Microsoft.AspNet.Identity.EntityFramework |
| 선택망 NuGet | Microsoft.AspNet.Identity.Owin |

## <a name="project-file-changes"></a>프로젝트 파일 변경

- 속성 `IISExpressSSLPort`를 고유한 숫자로 설정 합니다.
- 속성 `WebProject_DirectoryAccessLevelKey`를 0으로 설정 하거나, **디렉터리 데이터 읽기** 옵션을 선택한 경우 1을 설정 합니다.
- 속성 `IISUrl`를 `<port>` `IISExpressSSLPort` 값과 일치 하는 `https://localhost:<port>/`로 설정 합니다.

## <a name="webconfig-or-appconfig-changes"></a>web.config 또는 app.config 변경

- 다음 구성 항목을 추가 했습니다.

    ```xml
    <appSettings>
        <add key="ida:ClientId" value="<ClientId from the new Azure AD app>" />
        <add key="ida:Tenant" value="<your selected Azure domain>" />
        <add key="ida:Audience" value="<your selected domain + / + project name>" />
    </appSettings>
    ```

- Visual Studio 2017만 해당: `<appSettings>`"아래에 다음 항목이 추가 되었습니다.

    ```xml
    <add key="ida:MetadataAddress" value="<domain URL + /federationmetadata/2007-06/federationmetadata.xml>" />
    ```

- `System.IdentityModel.Tokens.Jwt`에 대 한 `<runtime><assemblyBinding>` 노드 아래에 `<dependentAssembly>` 요소를 추가 했습니다.

- **디렉터리 데이터 읽기** 옵션을 선택한 경우 `<appSettings>`아래에 다음 구성 항목을 추가 합니다.

    ```xml
    <add key="ida:Password" value="<Your Azure AD app's new password>" />
    ```

## <a name="code-changes-and-additions"></a>코드 변경 및 추가

- `Controllers/ValueController.cs` 및 기타 기존 컨트롤러에 `[Authorize]` 특성을 추가 했습니다.

- Azure AD 인증을 위한 시작 논리를 포함 하는 `App_Start/Startup.Auth.cs`인증 시작 클래스를 추가 하거나 적절 하 게 수정 했습니다. **디렉터리 데이터 읽기** 옵션을 선택한 경우이 파일에는 OAuth 코드를 받고 액세스 토큰에 대해 교환 하는 코드도 포함 됩니다.

- (Visual Studio 2015 ASP.NET 4 앱에만 해당) `App_Start/IdentityConfig.cs` 제거 되 고 `Controllers/AccountController.cs`, `Models/IdentityModel.cs`및 `Providers/ApplicationAuthProvider.cs`추가 됩니다.

- Visual Studio에서 연결 된 서비스의 추가를 추적 하는 데 사용 하는 정보가 포함 된 `Connected Services/AzureAD/ConnectedService.json` (Visual Studio 2017) 또는 `Service References/Azure AD/ConnectedService.json` (Visual Studio 2015)가 추가 되었습니다.

### <a name="file-backup-visual-studio-2015"></a>파일 백업 (Visual Studio 2015)

연결 된 서비스를 추가할 때 Visual Studio 2015은 변경 및 제거 된 파일을 백업 합니다. 영향을 받는 모든 파일이 `Backup/AzureAD`폴더에 저장 됩니다. Visual Studio 2017은 백업을 만들지 않습니다.

- `Startup.cs`
- `App_Start\IdentityConfig.cs`
- `App_Start\Startup.Auth.cs`
- `Controllers\AccountController.cs`
- `Controllers\ManageController.cs`
- `Models\IdentityModels.cs`
- `Models\ApplicationOAuthProvider.cs`

## <a name="changes-on-azure"></a>Azure의 변경 내용

- 연결 된 서비스를 추가할 때 선택한 도메인에서 Azure AD 응용 프로그램을 만들었습니다.
- 해당 옵션이 선택 된 경우 **디렉터리 데이터 읽기** 권한을 포함 하도록 앱을 업데이트 했습니다.

[Azure Active Directory에 대해 자세히 알아보세요](https://azure.microsoft.com/services/active-directory/).

## <a name="next-steps"></a>Következő lépések

- [Azure Active Directory에 대 한 인증 시나리오](authentication-scenarios.md)
- [ASP.NET 웹 앱에 Microsoft에 로그인 추가](quickstart-v1-aspnet-webapp.md)
