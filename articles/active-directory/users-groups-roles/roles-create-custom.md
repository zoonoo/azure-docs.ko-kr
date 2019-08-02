---
title: Azure AD 역할 기반 액세스 제어에서 사용자 지정 역할 정의 만들기-Azure Active Directory | Microsoft Docs
description: Azure Active Directory 리소스에 대 한 리소스 범위를 사용 하 여 사용자 지정 Azure AD 역할을 만듭니다.
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 07/31/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: ae773bc1a4e1831dbe462149bb827c26b7e74b96
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68722260"
---
# <a name="create-a-custom-role-and-assign-a-resource-scope-in-azure-active-directory"></a>Azure Active Directory에서 사용자 지정 역할을 만들고 리소스 범위를 할당 합니다.

이 문서에서는 Azure Active Directory (Azure AD)에서 새 사용자 지정 역할을 만드는 방법을 설명 합니다. 사용자 지정 역할은 Azure AD 개요 페이지나 [응용 프로그램 등록 페이지](https://portal.azure.com/?Microsoft_AAD_IAM_enableCustomRoleManagement=true&Microsoft_AAD_IAM_enableCustomRoleAssignment=true&feature.rbacv2roles=true&feature.rbacv2=true&Microsoft_AAD_RegisteredApps=demo#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview)의 **역할 및 관리자** 탭에서 만들 수 있습니다. 역할은 디렉터리 수준 범위에서 할당 되거나 앱 등록으로 범위가 한정 될 수 있습니다.

자세한 내용은 사용자 지정 역할에 대 한 기본 사항에 대 한 [사용자 지정 역할 개요](roles-custom-overview.md) 를 참조 하세요.

## <a name="using-the-azure-ad-portal"></a>Azure AD 포털 사용

### <a name="create-a-new-custom-role-to-grant-access-to-manage-app-registrations"></a>앱 등록 관리에 대 한 액세스 권한을 부여 하는 새 사용자 지정 역할 만들기

1. Azure ad 조직에서 권한 있는 역할 관리자 또는 전역 관리자 권한으로 [azure ad 관리 센터](https://aad.portal.azure.com) 에 로그인 합니다.
1.  **Azure Active Directory** > **역할 및 관리자** > **새 사용자 지정 역할**을 선택 합니다.

   ![역할 및 관리자 페이지에서 역할 만들기 또는 편집](./media/roles-create-custom/new-custom-role.png)

1. **기본 사항** 탭에서 역할에 대 한 이름 및 설명을 제공 합니다.

   ![기본 사항 탭에서 사용자 지정 역할에 대 한 이름 및 설명 입력](./media/roles-create-custom/basics-tab.png)

1. 앱 등록 자격 증명을 관리 하는 데 필요한 권한과 이름 등의 기본 속성을 선택 하려면 다음을 수행 합니다.
   1. 검색 창에 "자격 증명"을 입력 하 고 `microsoft.directory/applications/credentials/update` 사용 권한을 선택 합니다.

      ![사용 권한 탭에서 사용자 지정 역할에 대 한 사용 권한 선택](./media/roles-create-custom/permissions-tab.png)

   1. 검색 창에 "기본"을 입력 하 고 `microsoft.directory/applications/basic/update` 사용 권한을 선택한 후 **다음**을 클릭 합니다.
1. **검토 + 만들기** 탭에서 사용 권한을 검토 하 고 **만들기**를 선택 합니다.

사용자 지정 역할이 할당할 수 있는 역할 목록에 표시 됩니다.

## <a name="assign-a-role-scoped-to-a-resource"></a>리소스에 범위가 지정 된 역할 할당

기본 제공 역할과 마찬가지로 사용자 지정 역할을 기본 조직 범위에서 할당 하 여 모든 앱 등록에 대 한 액세스 권한을 부여할 수 있습니다. 그러나 사용자 지정 역할은 개체 범위에서 할당 될 수도 있습니다. 이를 통해 두 번째 사용자 지정 역할을 만들 필요 없이 단일 앱의 기본 속성 및 자격 증명을 업데이트할 수 있는 권한을 담당자에 게 제공할 수 있습니다.

1. Azure ad 조직에서 응용 프로그램 개발자 권한으로 [AZURE ad 관리 센터](https://aad.portal.azure.com) 에 로그인 합니다.
1. **앱 등록**을 선택합니다.
1. 관리 권한을 부여 하는 앱 등록을 선택 합니다. Azure AD 조직에서 앱 등록의 전체 목록을 보려면 **모든 응용 프로그램** 을 선택 해야 할 수도 있습니다.

    ![역할 할당에 대 한 리소스 범위로 앱 등록을 선택 합니다.](./media/roles-create-custom/appreg-all-apps.png)

1. 앱 등록에서 **역할 및 관리자**를 선택 합니다. 아직 만들지 않은 경우 지침은 [이전 절차](#create-a-new-custom-role-to-grant-access-to-manage-app-registrations)에 나와 있습니다.

    열려 있는 앱 등록의 컨텍스트에서이 역할을 할당 하는 경우 해당 담당자는 해당 단일 앱 등록에 대 한 사용 권한을 가집니다. 할당 하는 역할은 모든 앱 등록에 대 한 목록에 표시 됩니다. 역할에 따라 특정 Azure AD 리소스에 대 한 사용 권한을 소유자에 게 할당할 수 있는이 액세스 모델은 azure 리소스 액세스 제어를 위해 [AZURE RBAC](../../role-based-access-control/overview.md) 에서 사용 되는 모델과 유사 합니다.

1. 역할을 선택 하 여 **할당** 페이지를 엽니다.
1. **할당 추가** 를 선택 하 여 사용자를 추가 합니다. 사용자에 게 선택한 앱 이외의 앱 등록에 대 한 사용 권한이 부여 되지 않습니다.

## <a name="create-a-custom-role-using-azure-ad-powershell"></a>Azure AD PowerShell을 사용 하 여 사용자 지정 역할 만들기

### <a name="prepare-powershell"></a>PowerShell 준비

먼저 [Azure AD PowerShell 모듈을 다운로드](https://www.powershellgallery.com/packages/AzureAD/)해야 합니다.

Azure AD PowerShell 모듈을 설치하려면 다음 명령을 사용합니다.

``` PowerShell
install-module azureadpreview
import-module azureadpreview
```

모듈을 사용할 수 있는지 확인하려면 다음 명령을 사용합니다.

``` PowerShell
get-module azuread
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.0.115    azuread                      {Add-AzureADAdministrati...}
```

### <a name="create-the-custom-role"></a>사용자 지정 역할 만들기

다음 PowerShell 스크립트를 사용 하 여 새 역할을 만듭니다.

``` PowerShell
# Basic role information
$description = "Application Support Administrator"
$displayName = "Can manage basic aspects of application registrations."
$templateId = (New-Guid).Guid

# Set of permissions to grant
$allowedResourceAction =
@(
    "microsoft.directory/applications/allProperties/read",
    "microsoft.directory/applications/basic/update",
    "microsoft.directory/applications/credentials/update"
)
$resourceActions = @{'allowedResourceActions'= $allowedResourceAction}
$rolePermission = @{'resourceActions' = $resourceActions}
$rolePermissions = $rolePermission

# Create new custom admin role
$customAdmin = New-AzureAdRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

## <a name="create-a-custom-role-using-microsoft-graph-api"></a>Microsoft Graph API를 사용 하 여 사용자 지정 역할 만들기

1. 역할 정의를 만듭니다.

    사용자 지정 역할 정의를 만들기 위한 HTTP 요청입니다.

    올리기

    ``` HTTP
    https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitions
    ```

    본문

    ``` HTTP
    {
    "description":"Can manage basic aspects of application registrations.",
    "displayName":"Application Support Administrator",
    "isEnabled":true,
    "rolePermissions":
    [
        {
            "allowedResourceActions": 
            [
                "microsoft.directory/applications/basic/read",
                "microsoft.directory/applications/credentials/update"
            ]
        }
    ]
    }
    ```

1. 역할 할당을 만듭니다.

    사용자 지정 역할 정의를 만들기 위한 HTTP 요청입니다.

    올리기

    ``` HTTP
    https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitions/roleAssignments
    ```

    본문

    ``` HTTP
    {
    "principalId":"<GUID OF USER>",
    "roleDefinitionId":"<GUID OF ROLE DEFINITION>",
    "resourceScope":["/<GUID OF APPLICATION REGISTRATION>"]
    }
    ```

## <a name="next-steps"></a>다음 단계

- [Azure AD 관리 역할 포럼](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)에서 경험을 자유롭게 공유하세요.
- 역할 및 관리자 역할 할당에 대한 자세한 내용은 [관리자 역할 할당](directory-assign-admin-roles.md)을 참조하세요.
- 기본 사용자 권한의 경우 [기본 게스트 및 멤버 사용자 권한 비교](../fundamentals/users-default-permissions.md)를 참조하세요.
