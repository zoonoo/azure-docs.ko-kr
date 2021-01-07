---
title: 엔터프라이즈 앱 액세스 할당에 대 한 사용자 지정 역할 권한-Azure Active Directory | Microsoft Docs
description: Azure Active Directory에서 엔터프라이즈 앱 액세스에 대 한 사용자 지정 Azure AD 역할 만들기 및 할당
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 11/04/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0932e4b6163264b0b514958e1e898b297e249870
ms.sourcegitcommit: 0d171fe7fc0893dcc5f6202e73038a91be58da03
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93378553"
---
# <a name="assign-custom-roles-to-manage-enterprise-apps-in-azure-active-directory"></a>Azure Active Directory에서 엔터프라이즈 앱을 관리 하는 사용자 지정 역할 할당

이 문서에서는 Azure Active Directory (Azure AD)에서 사용자 및 그룹에 대 한 엔터프라이즈 앱 할당을 관리할 수 있는 권한이 있는 사용자 지정 역할을 만드는 방법을 설명 합니다. 역할 할당의 요소와 하위 형식, 권한 및 속성 집합과 같은 용어의 의미에 대해서는 [사용자 지정 역할 개요](custom-overview.md)를 참조 하세요.

## <a name="enterprise-app-role-permissions"></a>엔터프라이즈 앱 역할 사용 권한

이 문서에서 설명 하는 두 가지 엔터프라이즈 앱 권한이 있습니다. 모든 예에서는 update 권한을 사용 합니다.

* 범위에서 사용자 및 그룹 할당을 읽으려면 사용 권한을 부여 합니다. `microsoft.directory/servicePrincipals/appRoleAssignedTo/read`
* 범위에서 사용자 및 그룹 할당을 관리 하려면 권한을 부여 합니다. `microsoft.directory/servicePrincipals/appRoleAssignedTo/update`

업데이트 권한을 부여 하면 담당자가 엔터프라이즈 앱에 대 한 사용자 및 그룹의 할당을 관리할 수 있습니다. 단일 응용 프로그램에 대해 사용자 및/또는 그룹 할당의 범위를 부여 하거나 모든 응용 프로그램에 대 한 권한을 부여할 수 있습니다. 조직 차원의 수준에서 부여 되는 경우 담당자는 모든 응용 프로그램에 대 한 할당을 관리할 수 있습니다. 응용 프로그램 수준에서 수행 하는 경우 담당자는 지정 된 응용 프로그램에 대 한 할당을 관리할 수 있습니다.

업데이트 권한을 부여 하는 작업은 다음 두 단계로 수행 됩니다.

1. 사용 권한이 있는 사용자 지정 역할 만들기 `microsoft.directory/servicePrincipals/appRoleAssignedTo/update`
1. 사용자 또는 그룹에 게 엔터프라이즈 앱에 대 한 사용자 및 그룹 할당을 관리할 수 있는 권한을 부여 합니다. 이는 범위를 조직 전체 수준 또는 단일 응용 프로그램으로 설정할 수 있는 경우입니다.

## <a name="use-the-azure-ad-admin-center"></a>Azure AD 관리 센터 사용

### <a name="create-a-new-custom-role"></a>새 사용자 지정 역할 만들기

>[!NOTE]
> 사용자 지정 역할은 조직 전체 수준에서 만들어지고 관리 되며 조직의 개요 페이지 에서만 사용할 수 있습니다.

1. 조직에서 권한 있는 역할 관리자 또는 전역 관리자 권한으로 [AZURE AD 관리 센터](https://aad.portal.azure.com) 에 로그인 합니다.
1. **Azure Active Directory** 를 선택하고 **역할 및 관리자** 를 선택한 다음, **새 사용자 지정 역할** 을 선택합니다.

    ![Azure AD의 역할 목록에서 새 사용자 지정 역할 추가](./media/custom-enterprise-apps/new-custom-role.png)

1. **기본 정보** 탭에서 역할의 이름에 "사용자 및 그룹 할당 관리"를 제공 하 고 역할 설명에 "사용자 및 그룹 할당을 관리 하는 권한 부여"를 선택한 후 **다음** 을 선택 합니다.

    ![사용자 지정 역할에 대 한 이름 및 설명 제공](./media/custom-enterprise-apps/role-name-and-description.png)

1. **사용 권한** 탭의 검색 상자에 "microsoft Directory/Serviceprincipals/appRoleAssignedTo/update"를 입력 하 고 원하는 권한 옆의 확인란을 선택한 후 **다음** 을 선택 합니다.

    ![사용자 지정 역할에 사용 권한 추가](./media/custom-enterprise-apps/role-custom-permissions.png)

1. **검토 + 만들기** 탭에서 권한을 검토하고 **만들기** 를 선택합니다.

    ![이제 사용자 지정 역할을 만들 수 있습니다.](./media/custom-enterprise-apps/role-custom-create.png)

### <a name="assign-the-role-to-a-user-using-the-azure-ad-portal"></a>Azure AD 포털을 사용하여 사용자에게 역할 할당

1. 권한 있는 역할 관리자 역할 권한으로 [AZURE AD 관리 센터](https://aad.portal.azure.com) 에 로그인 합니다.
1. **Azure Active Directory** 를 선택한 다음, **역할 및 관리자** 를 선택합니다.
1. **사용자 및 그룹 할당을 관리할 수 있는 권한 부여 역할을** 선택 합니다.

    ![역할 및 관리자를 열고 사용자 지정 역할 검색](./media/custom-enterprise-apps/select-custom-role.png)

1. **할당 추가** 를 선택 하 고 원하는 사용자를 선택한 다음 **선택** 을 클릭 하 여 사용자에 게 역할 할당을 추가 합니다.

    ![사용자에 게 사용자 지정 역할에 대 한 할당 추가](./media/custom-enterprise-apps/assign-user-to-role.png)

#### <a name="assignment-tips"></a>할당 팁

* 조직 전체에서 모든 엔터프라이즈 앱에 대 한 사용자 및 그룹 액세스를 관리 하기 위해 담당자에 게 권한을 부여 하려면 조직에 대 한 Azure AD **개요** 페이지의 조직 전체 **역할 및 관리자** 목록에서 시작 합니다.
* 특정 엔터프라이즈 앱에 대 한 사용자 및 그룹 액세스를 관리 하기 위해 담당자에 게 권한을 부여 하려면 Azure AD에서 해당 앱으로 이동 하 여 해당 앱에 대 한 **역할 및 관리자** 목록에서 엽니다. 새 사용자 지정 역할을 선택 하 고 사용자 또는 그룹 할당을 완료 합니다. 책임자는 특정 앱에 대 한 사용자 및 그룹 액세스만 관리할 수 있습니다.
* 사용자 지정 역할 할당을 테스트 하려면 담당자로 로그인 하 고 응용 프로그램의 **사용자 및 그룹** 페이지를 열어 **사용자 추가** 옵션이 설정 되어 있는지 확인 합니다.

    ![사용자 권한 확인](./media/custom-enterprise-apps/verify-user-permissions.png)

## <a name="use-azure-ad-powershell"></a>Azure AD PowerShell 사용

자세한 내용은 PowerShell을 사용 하 여 [사용자 지정 역할 만들기 및 할당](custom-create.md) 및 [리소스 범위를 사용 하 여 사용자 지정 역할 할당](custom-assign-powershell.md)을 참조 하세요.

먼저 [PowerShell 갤러리](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.17)에서 Azure AD PowerShell 모듈을 설치 합니다. 그런 다음, 다음 명령을 사용하여 Azure AD PowerShell 미리 보기 모듈을 가져옵니다.

```powershell
PowerShell
import-module azureadpreview
```

모듈을 사용할 준비가 되었는지 확인하려면 다음 명령에서 반환된 버전을 여기에 나열된 버전과 일치시킵니다.

```powershell
PowerShell
get-module azureadpreview
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.0.115    azureadpreview               {Add-AzureADAdministrati...}
```

### <a name="create-a-custom-role"></a>사용자 지정 역할 만들기

다음 PowerShell 스크립트를 사용하여 새 역할을 만듭니다.

```PowerShell
# Basic role information
$description = "Manage user and group assignments"
$displayName = "Can manage user and group assignments for Applications"
$templateId = (New-Guid).Guid

# Set of permissions to grant
$allowedResourceAction =@( "microsoft.directory/servicePrincipals/appRoleAssignedTo/update")
$resourceActions = @{'allowedResourceActions'= $allowedResourceAction}
$rolePermission = @{'resourceActions' = $resourceActions}
$rolePermissions = $rolePermission

# Create new custom admin role
$customRole = New-AzureADMSRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

### <a name="assign-the-custom-role"></a>사용자 지정 역할 할당

이 PowerShell 스크립트를 사용 하 여 역할을 할당 합니다.

```powershell
PowerShell
# Basic role information

$description = "Manage user and group assignments"
$displayName = "Can manage user and group assignments for Applications"
$templateId = (New-Guid).Guid

# Set of permissions to grant
$allowedResourceAction =
@(
    "microsoft.directory/servicePrincipals/appRoleAssignedTo/update"
)
$resourceActions = @{'allowedResourceActions'= $allowedResourceAction}
$rolePermission = @{'resourceActions' = $resourceActions}
$rolePermissions = $rolePermission

# Create new custom role
$customRole = New-AzureAdRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

## <a name="use-the-microsoft-graph-api"></a>Microsoft Graph API 사용

Microsoft Graph API에서 제공 된 예제를 사용 하 여 사용자 지정 역할을 만듭니다. 자세한 내용은 [사용자 지정 역할 만들기 및 할당](custom-create.md) 및 [Microsoft Graph API를 사용 하 여 사용자 지정 관리자 역할 할당](custom-assign-graph.md)을 참조 하세요.

사용자 지정 역할을 만들기 위한 HTTP 요청.

```HTTP
POST
https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitionsIsEnabled $true
{
    "description":"Can manage user and group assignments for Applications.",
    "displayName":" Manage user and group assignments",
    "isEnabled":true,
    "rolePermissions":
    [
        {
            "resourceActions":
            {
                "allowedResourceActions":
                [
                    "microsoft.directory/servicePrincipals/appRoleAssignedTo/update"
                ]
            },
            "condition":null
        }
    ],
    "templateId":"<PROVIDE NEW GUID HERE>",
    "version":"1"
}
```

### <a name="assign-the-custom-role-using-microsoft-graph-api"></a>Microsoft Graph API를 사용하여 사용자 지정 역할 할당

역할 할당은 보안 주체 ID (사용자 또는 서비스 사용자 일 수 있음), 역할 정의 ID 및 Azure AD 리소스 범위를 결합 합니다. 역할 할당의 요소에 대 한 자세한 내용은 [사용자 지정 역할 개요](custom-overview.md) 를 참조 하세요.

사용자 지정 역할을 할당하기 위한 HTTP 요청.

```HTTP
POST https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments

{
    "principalId":"<PROVIDE OBJECTID OF USER TO ASSIGN HERE>",
    "roleDefinitionId":"<PROVIDE OBJECTID OF ROLE DEFINITION HERE>",
    "resourceScopes":["/"]
}
```

## <a name="next-steps"></a>다음 단계

* [엔터프라이즈 앱에 사용할 수 있는 사용자 지정 역할 사용 권한 살펴보기](custom-enterprise-app-permissions.md)
