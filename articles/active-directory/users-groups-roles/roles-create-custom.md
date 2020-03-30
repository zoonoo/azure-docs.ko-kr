---
title: Azure AD 역할 기반 액세스 제어에서 사용자 지정 역할 만들기 | 마이크로 소프트 문서
description: Azure Active Directory 리소스에서 리소스 범위를 사용하여 사용자 지정 Azure AD 역할을 만들고 할당합니다.
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
ms.openlocfilehash: c2cb19c82f8c19bf87eeef755adb5756b2452512
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74025280"
---
# <a name="create-and-assign-a-custom-role-in-azure-active-directory"></a>Azure Active Directory에서 사용자 지정 역할 만들기 및 할당

이 문서에서는 Azure Active Directory(Azure AD)에서 새 사용자 지정 역할을 만드는 방법에 대해 설명합니다. 사용자 지정 역할의 기본 내용은 [사용자 지정 역할 개요를 참조하세요.](roles-custom-overview.md) 역할은 디렉터리 수준 범위 또는 앱 등록 리소스 범위에서만 할당할 수 있습니다.

Azure AD 개요 페이지의 [역할 및 관리자](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators) 탭에서 사용자 지정 역할을 만들 수 있습니다.

## <a name="create-a-role-in-the-azure-portal"></a>Azure 포털에서 역할 만들기

### <a name="create-a-new-custom-role-to-grant-access-to-manage-app-registrations"></a>앱 등록 을 관리하기 위한 액세스 권한을 부여하는 새 사용자 지정 역할 만들기

1. Azure AD 조직의 권한 있는 역할 관리자 또는 전역 관리자 권한을 사용하여 Azure [AD 관리자 센터에](https://aad.portal.azure.com) 로그인합니다.
1. **Azure Active Directory** > **역할 및 관리자** > **새 사용자 지정 역할**입니다.

   ![역할 및 관리자 페이지에서 역할 만들기 또는 편집](./media/roles-create-custom/new-custom-role.png)

1. **기본** 탭에서 역할에 대한 이름과 설명을 제공한 다음 **다음**을 클릭합니다.

   ![기본 탭에서 사용자 지정 역할에 대한 이름과 설명을 제공합니다.](./media/roles-create-custom/basics-tab.png)

1. 사용 **권한** 탭에서 앱 등록의 기본 속성 및 자격 증명 속성을 관리하는 데 필요한 권한을 선택합니다. 각 권한에 대한 자세한 설명은 [Azure Active Directory](./roles-custom-available-permissions.md)의 응용 프로그램 등록 하위 유형 및 사용 권한을 참조하십시오.
   1. 먼저 검색 표시줄에 "자격 증명"을 `microsoft.directory/applications/credentials/update` 입력하고 권한을 선택합니다.

      ![사용 권한 탭에서 사용자 지정 역할에 대한 사용 권한 선택](./media/roles-create-custom/permissions-tab.png)

   1. 그런 다음 검색 표시줄에 "basic"을 입력하고 `microsoft.directory/applications/basic/update` 권한을 선택한 다음 **다음을 클릭합니다.**
1. 검토 **+ 만들기** 탭에서 사용 권한을 검토하고 **에서 만들기를**선택합니다.

사용자 지정 역할은 할당할 사용 가능한 역할 목록에 표시됩니다.

## <a name="create-a-role-using-powershell"></a>PowerShell을 사용하여 역할 만들기

### <a name="prepare-powershell"></a>파워쉘 준비

먼저 Azure [AD 미리 보기 PowerShell 모듈을 다운로드해야](https://www.powershellgallery.com/packages/AzureADPreview)합니다.

Azure AD PowerShell 모듈을 설치하려면 다음 명령을 사용합니다.

``` PowerShell
install-module azureadpreview
import-module azureadpreview
```

모듈을 사용할 수 있는지 확인하려면 다음 명령을 사용합니다.

``` PowerShell
get-module azureadpreview
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.2.31     azuread                      {Add-AzureADAdministrati...}
```

### <a name="create-the-custom-role"></a>사용자 지정 역할 만들기

다음 PowerShell 스크립트를 사용하여 새 역할을 만듭니다.

``` PowerShell
# Basic role information
$displayName = "Application Support Administrator"
$description = "Can manage basic aspects of application registrations."
$templateId = (New-Guid).Guid
 
# Set of permissions to grant
$allowedResourceAction =
@(
    "microsoft.directory/applications/basic/update",
    "microsoft.directory/applications/credentials/update"
)
$rolePermissions = @{'allowedResourceActions'= $allowedResourceAction}
 
# Create new custom admin role
$customAdmin = New-AzureADMSRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

### <a name="assign-the-custom-role-using-azure-ad-powershell"></a>Azure AD PowerShell을 사용하여 사용자 지정 역할 할당

아래 PowerShell 스크립트를 사용하여 역할을 할당합니다.

``` PowerShell
# Get the user and role definition you want to link
$user = Get-AzureADUser -Filter "userPrincipalName eq 'cburl@f128.info'"
$roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Application Support Administrator'"

# Get app registration and construct resource scope for assignment.
$appRegistration = Get-AzureADApplication -Filter "displayName eq 'f/128 Filter Photos'"
$resourceScope = '/' + $appRegistration.objectId

# Create a scoped role assignment
$roleAssignment = New-AzureADMSRoleAssignment -ResourceScope $resourceScope -RoleDefinitionId $roleDefinition.Id -PrincipalId $user.objectId
```

## <a name="create-a-role-with-graph-api"></a>그래프 API를 사용하여 역할 만들기

1. 역할 정의를 만듭니다.

    사용자 지정 역할 정의를 만들기 위한 HTTP 요청입니다.

    POST

    ``` HTTP
    https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitions
    ```

    본문

    ``` HTTP
   {
       "description": "Can manage basic aspects of application registrations.",
       "displayName": "Application Support Administrator",
       "isEnabled": true,
       "templateId": "<GUID>",
       "rolePermissions": [
           {
               "allowedResourceActions": [
                   "microsoft.directory/applications/basic/update",
                   "microsoft.directory/applications/credentials/update"
               ]
           }
       ]
   }
    ```

1. 역할 할당을 만듭니다.

    사용자 지정 역할 정의를 만들기 위한 HTTP 요청입니다.

    POST

    ``` HTTP
    https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
    ```

    본문

    ``` HTTP
   {
       "principalId":"<GUID OF USER>",
       "roleDefinitionId":"<GUID OF ROLE DEFINITION>",
       "resourceScope":"/<GUID OF APPLICATION REGISTRATION>"
   }
    ```

## <a name="assign-a-custom-role-scoped-to-a-resource"></a>리소스에 범위가 지정된 사용자 지정 역할 할당

기본 제공 역할과 마찬가지로 사용자 지정 역할은 기본적으로 기본 조직 전체 범위에서 할당되어 조직의 모든 앱 등록에 대한 액세스 권한을 부여합니다. 그러나 기본 제공 역할과 달리 사용자 지정 역할은 단일 Azure AD 리소스의 범위에서 할당할 수도 있습니다. 이렇게 하면 두 번째 사용자 지정 역할을 만들지 않고도 단일 앱의 자격 증명 및 기본 속성을 업데이트할 수 있는 권한을 사용자에게 부여할 수 있습니다.

1.  [Azure AD](https://aad.portal.azure.com) 조직의 응용 프로그램 개발자 권한을 사용하여 Azure AD 관리자 센터에 로그인합니다.
1. **앱 등록을 선택합니다.**
1. 관리할 수 있는 액세스 권한을 부여하는 앱 등록을 선택합니다. Azure AD 조직의 전체 앱 등록 목록을 보려면 **모든 응용 프로그램을** 선택해야 할 수 있습니다.

    ![역할 할당을 위한 리소스 범위로 앱 등록선택](./media/roles-create-custom/appreg-all-apps.png)

1. 앱 등록에서 **역할 및 관리자를**선택합니다. 아직 만들지 않은 경우 지침은 [이전 절차에](#create-a-new-custom-role-to-grant-access-to-manage-app-registrations)있습니다.

1. 과제 페이지를 열 역할을 **선택합니다.**
1. 사용자를 추가하려면 **할당 추가를** 선택합니다. 사용자에게는 선택한 앱 등록에 대한 모든 권한이 부여됩니다.

## <a name="next-steps"></a>다음 단계

- [Azure AD 관리 역할 포럼](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)에서 경험을 자유롭게 공유하세요.
- 역할 및 관리자 역할 할당에 대한 자세한 내용은 [관리자 역할 할당](directory-assign-admin-roles.md)을 참조하세요.
- 기본 사용자 권한의 경우 [기본 게스트 및 멤버 사용자 권한 비교](../fundamentals/users-default-permissions.md)를 참조하세요.
