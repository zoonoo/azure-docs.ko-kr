---
title: 앱 등록 만들기에 대 한 제한 제거-Azure AD | Microsoft Docs
description: Azure AD Active Directory에서 무제한 앱 등록을 허용 하는 사용자 지정 역할을 할당 합니다.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7acd76ff45f783f614b2a1d3f0d5c10d800a1ea9
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/22/2020
ms.locfileid: "77559048"
---
# <a name="quickstart-grant-permission-to-create-unlimited-app-registrations"></a>빠른 시작: 무제한 앱 등록을 만들 수 있는 권한 부여

이 빠른 시작에서는 무제한 개수의 앱 등록을 만들 수 있는 권한이 있는 사용자 지정 역할을 만든 다음 해당 역할을 사용자에 게 할당 합니다. 그러면 할당 된 사용자가 Azure AD portal, Azure AD PowerShell 또는 Microsoft Graph API를 사용 하 여 응용 프로그램 등록을 만들 수 있습니다. 기본 제공 응용 프로그램 개발자 역할과 달리이 사용자 지정 역할은 응용 프로그램 등록을 무제한으로 만들 수 있는 기능을 부여 합니다. 응용 프로그램 개발자 역할은 기능을 부여 하지만 [디렉터리 차원의 개체 할당량에](directory-service-limits-restrictions.md)도달 하지 않도록 생성 된 개체의 총 수는 250 개로 제한 됩니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisite"></a>필수 요소

Azure AD 사용자 지정 역할을 만들고 할당 하는 데 필요한 최소 권한 있는 역할은 권한 있는 역할 관리자입니다.

## <a name="create-a-new-custom-role-using-the-azure-ad-portal"></a>Azure AD 포털을 사용 하 여 새 사용자 지정 역할 만들기

1. Azure ad 조직에서 권한 있는 역할 관리자 또는 전역 관리자 권한으로  [AZURE ad 관리 센터](https://aad.portal.azure.com) 에 로그인 합니다.
1. **Azure Active Directory**를 선택 하 고 **역할 및 관리자**를 선택한 다음 **새 사용자 지정 역할**을 선택 합니다.

    ![역할 및 관리자 페이지에서 역할 만들기 또는 편집](./media/roles-create-custom/new-custom-role.png)

1. **기본** 탭에서 역할 이름에 "응용 프로그램 등록 작성자"를 제공 하 고 역할 설명에 "응용 프로그램 등록을 무제한으로 만들 수 있습니다."를 선택한 후 **다음**을 선택 합니다.

    ![기본 사항 탭에서 사용자 지정 역할에 대 한 이름 및 설명 입력](./media/roles-quickstart-app-registration-limits/basics-tab.png)

1. **사용 권한** 탭의 검색 상자에 "microsoft. 디렉터리/응용 프로그램/만들기"를 입력 한 다음 원하는 권한 옆의 확인란을 선택 하 고 **다음**을 선택 합니다.

    ![사용 권한 탭에서 사용자 지정 역할에 대 한 사용 권한 선택](./media/roles-quickstart-app-registration-limits/permissions-tab.png)

1. **검토 + 만들기** 탭에서 사용 권한을 검토 하 고 **만들기**를 선택 합니다.

### <a name="assign-the-role-to-a-user-using-the-azure-ad-portal"></a>Azure AD 포털을 사용 하 여 사용자에 게 역할 할당

1. Azure ad 조직에서 권한 있는 역할 관리자 또는 전역 관리자 권한으로  [AZURE ad 관리 센터](https://aad.portal.azure.com) 에 로그인 합니다.
1. **Azure Active Directory** 선택 하 고 **역할 및 관리자**를 선택 합니다.
1. 응용 프로그램 등록 작성자 역할을 선택 하 고 **할당 추가**를 선택 합니다.
1. 원하는 사용자를 선택 하 고 **선택** 을 클릭 하 여 사용자를 역할에 추가 합니다.

Done! 이 빠른 시작에서는 무제한 개수의 앱 등록을 만들 수 있는 권한이 있는 사용자 지정 역할을 성공적으로 만든 후 해당 역할을 사용자에 게 할당 합니다.

> [!TIP]
> Azure AD 포털을 사용 하 여 응용 프로그램에 역할을 할당 하려면 할당 페이지의 검색 상자에 응용 프로그램의 이름을 입력 합니다. 응용 프로그램은 기본적으로 목록에 표시 되지 않지만 검색 결과에 반환 됩니다.

### <a name="app-registration-permissions"></a>앱 등록 권한

응용 프로그램 등록을 만들 수 있는 기능을 부여 하는 데 사용할 수 있는 두 가지 권한은 각기 다른 동작입니다.

- microsoft. directory/applications/createAsOwner:이 권한을 할당 하면 작성자가 생성 된 앱 등록의 첫 번째 소유자로 추가 되며, 만들어진 앱 등록은 작성자의 250 생성 된 개체 할당량에 따라 계산 됩니다.
- microsoft. directory/applicationPolicies/만들기:이 권한을 할당 하면 만든 앱 등록의 첫 번째 소유자로 작성자가 추가 되지 않으며 만들어진 앱 등록이 작성자의 250 생성 된 개체 할당량에 대해 계산 되지 않습니다. 디렉터리 수준 할당량이 적중 될 때까지 담당자가 앱 등록을 만들 수 없도록 하는 것이 없으므로이 사용 권한을 신중 하 게 사용 합니다. 두 사용 권한이 모두 할당 된 경우이 권한이 우선적으로 적용 됩니다.

## <a name="create-a-custom-role-using-azure-ad-powershell"></a>Azure AD PowerShell을 사용 하 여 사용자 지정 역할 만들기

다음 PowerShell 스크립트를 사용 하 여 새 역할을 만듭니다.

``` PowerShell
# Basic role information
$description = "Application Registration Creator"
$displayName = "Can create an unlimited number of application registrations."
$templateId = (New-Guid).Guid

# Set of permissions to grant
$allowedResourceAction =
@(
    "microsoft.directory/applications/createAsOwner"
)
$resourceActions = @{'allowedResourceActions'= $allowedResourceAction}
$rolePermission = @{'resourceActions' = $resourceActions}
$rolePermissions = $rolePermission

# Create new custom admin role
$customRole = New-AzureAdRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

### <a name="assign-the-custom-role-using-azure-ad-powershell"></a>Azure AD PowerShell을 사용 하 여 사용자 지정 역할 할당

#### <a name="prepare-powershell"></a>PowerShell 준비

먼저 [PowerShell 갤러리](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.17)에서 Azure AD PowerShell 모듈을 설치 합니다. 그런 후 다음 명령을 사용 하 여 Azure AD PowerShell 미리 보기 모듈을 가져옵니다.

```powershell
import-module azureadpreview
```

모듈을 사용할 준비가 되었는지 확인 하려면 다음 명령에서 반환 된 버전을 여기에 나열 된 버전과 일치 시킵니다.

```powershell
get-module azureadpreview
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.0.115    azureadpreview               {Add-AzureADAdministrati...}
```

#### <a name="assign-the-custom-role"></a>사용자 지정 역할 할당

아래 PowerShell 스크립트를 사용 하 여 역할을 할당 합니다.

``` PowerShell
# Basic role information
$description = "Application Registration Creator"
$displayName = "Can create an unlimited number of application registrations."
$templateId = (New-Guid).Guid

# Set of permissions to grant
$allowedResourceAction =
@(
    "microsoft.directory/applications/create"
)
$resourceActions = @{'allowedResourceActions'= $allowedResourceAction}
$rolePermission = @{'resourceActions' = $resourceActions}
$rolePermissions = $rolePermission

# Create new custom admin role
$customRole = New-AzureAdRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

### <a name="create-a-custom-role-using-microsoft-graph-api"></a>Microsoft Graph API를 사용 하 여 사용자 지정 역할 만들기

사용자 지정 역할을 만들기 위한 HTTP 요청입니다.

POST

``` HTTP
https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitions
```

본문

```HTTP
{
    "description":"Can create an unlimited number of application registrations.",
    "displayName":"Application Registration Creator",
    "isEnabled":true,
    "rolePermissions":
    [
        {
            "resourceActions":
            {
                "allowedResourceActions":
                [
                    "microsoft.directory/applications/create"
                ]
            },
            "condition":null
        }
    ],
    "templateId":"<PROVIDE NEW GUID HERE>",
    "version":"1"
}
```

### <a name="assign-the-custom-role-using-microsoft-graph-api"></a>Microsoft Graph API를 사용 하 여 사용자 지정 역할 할당

역할 할당은 보안 주체 ID (사용자 또는 서비스 사용자 일 수 있음), 역할 정의 (역할) ID 및 Azure AD 리소스 범위를 결합 합니다.

사용자 지정 역할을 할당 하는 HTTP 요청입니다.

POST

``` HTTP
https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
```

본문

``` HTTP
{
    "principalId":"<PROVIDE OBJECTID OF USER TO ASSIGN HERE>",
    "roleDefinitionId":"<PROVIDE OBJECTID OF ROLE DEFINITION HERE>",
    "resourceScopes":["/"]
}
```

## <a name="next-steps"></a>다음 단계

- [Azure AD 관리 역할 포럼](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)에서 경험을 자유롭게 공유하세요.
- 역할 및 관리자 역할 할당에 대한 자세한 내용은 [관리자 역할 할당](directory-assign-admin-roles.md)을 참조하세요.
- 기본 사용자 권한의 경우 [기본 게스트 및 멤버 사용자 권한 비교](../fundamentals/users-default-permissions.md)를 참조하세요.
