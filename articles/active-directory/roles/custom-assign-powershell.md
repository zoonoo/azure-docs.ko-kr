---
title: Azure AD PowerShell을 사용 하 여 사용자 지정 역할 할당-Azure AD | Microsoft Docs
description: Azure AD PowerShell을 사용 하 여 Azure AD 관리자 사용자 지정 역할의 멤버를 관리 합니다.
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
ms.openlocfilehash: 8b155ccd7f8f0d7f6d63d906d7d0baaa3243512b
ms.sourcegitcommit: 61d2b2211f3cc18f1be203c1bc12068fc678b584
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/18/2021
ms.locfileid: "98562780"
---
# <a name="assign-custom-roles-with-resource-scope-using-powershell-in-azure-active-directory"></a>Azure Active Directory에서 PowerShell을 사용 하 여 리소스 범위에 사용자 지정 역할 할당

이 문서에서는 Azure AD (Azure Active Directory)에서 조직 차원의 범위에 역할 할당을 만드는 방법을 설명 합니다. 조직 전체의 범위에서 역할을 할당 하면 Azure AD 조직에서 액세스 권한을 부여 합니다. 단일 Azure AD 리소스 범위를 사용 하 여 역할 할당을 만들려면 [사용자 지정 역할을 만들고 리소스 범위에서 할당 하는 방법](custom-create.md)을 참조 하세요. 이 문서에서는 [Azure Active Directory PowerShell 버전 2](/powershell/module/azuread/#directory_roles) 모듈을 사용 합니다.

Azure AD 관리자 역할에 대 한 자세한 내용은 [Azure Active Directory에서 관리자 역할 할당](permissions-reference.md)을 참조 하세요.

## <a name="required-permissions"></a>필요한 사용 권한

전역 관리자 계정을 사용 하 여 Azure AD 조직에 연결 하 여 역할을 할당 하거나 제거 합니다.

## <a name="prepare-powershell"></a>PowerShell 준비

[PowerShell 갤러리](https://www.powershellgallery.com/packages/AzureADPreview)에서 Azure AD PowerShell 모듈을 설치 합니다. 그런 다음, 다음 명령을 사용하여 Azure AD PowerShell 미리 보기 모듈을 가져옵니다.

``` PowerShell
Import-Module AzureADPreview
```

모듈을 사용할 준비가 되었는지 확인하려면 다음 명령에서 반환된 버전을 여기에 나열된 버전과 일치시킵니다.

``` PowerShell
Get-Module AzureADPreview
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.0.115    azureadpreview               {Add-AzureADMSAdministrati...}
```

이제 모듈에서 cmdlet 사용을 시작할 수 있습니다. Azure AD 모듈의 cmdlet에 대 한 자세한 설명은 [AZURE ad 미리 보기 모듈](https://www.powershellgallery.com/packages/AzureADPreview)의 온라인 참조 설명서를 참조 하세요.

## <a name="assign-a-directory-role-to-a-user-or-service-principal-with-resource-scope"></a>리소스 범위를 사용 하 여 사용자 또는 서비스 사용자에 게 디렉터리 역할 할당

1. Azure AD PowerShell (미리 보기) 모듈을 로드 합니다.
1. 명령을 실행 하 여 로그인 `Connect-AzureAD` 합니다.
1. 다음 PowerShell 스크립트를 사용 하 여 새 역할을 만듭니다.

``` PowerShell
## Assign a role to a user or service principal with resource scope
# Get the user and role definition you want to link
$user = Get-AzureADUser -Filter "userPrincipalName eq 'cburl@f128.info'"
$roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Application Support Administrator'"

# Get app registration and construct resource scope for assignment.
$appRegistration = Get-AzureADApplication -Filter "displayName eq 'f/128 Filter Photos'"
$resourceScope = '/' + $appRegistration.objectId

# Create a scoped role assignment
$roleAssignment = New-AzureADMSRoleAssignment -ResourceScope $resourceScope -RoleDefinitionId $roleDefinition.Id -PrincipalId $user.objectId
```

사용자 대신 서비스 주체에 역할을 할당 하려면 [AzureADMSServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) cmdlet을 사용 합니다.

## <a name="role-definitions"></a>역할 정의

역할 정의 개체에는 기본 제공 또는 사용자 지정 역할의 정의와 해당 역할 할당에 의해 부여 된 사용 권한이 포함 됩니다. 이 리소스는 사용자 지정 역할 정의 및 기본 제공 디렉터리 역할 (roleDefinition 등가 형식으로 표시 됨)을 모두 표시 합니다. 현재 Azure AD 조직에는 최대 30 개의 고유한 사용자 지정 역할 정의를 정의할 수 있습니다.

### <a name="create-a-role-definition"></a>역할 정의 만들기

``` PowerShell
# Basic information
$description = "Can manage credentials of application registrations"
$displayName = "Application Registration Credential Administrator"
$templateId = (New-Guid).Guid

# Set of actions to include
$rolePermissions = @{
    "allowedResourceActions" = @(
        "microsoft.directory/applications/standard/read",
        "microsoft.directory/applications/credentials/update"
    )
}

# Create new custom directory role
$customAdmin = New-AzureADMSRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

### <a name="read-and-list-role-definitions"></a>역할 정의 읽기 및 나열

``` PowerShell
# Get all role definitions
Get-AzureADMSRoleDefinitions

# Get single role definition by ID
Get-AzureADMSRoleDefinition -Id 86593cfc-114b-4a15-9954-97c3494ef49b

# Get single role definition by templateId
Get-AzureADMSRoleDefinition -Filter "templateId eq 'c4e39bd9-1100-46d3-8c65-fb160da0071f'"
```

### <a name="update-a-role-definition"></a>역할 정의 업데이트

``` PowerShell
# Update role definition
# This works for any writable property on role definition. You can replace display name with other
# valid properties.
Set-AzureADMSRoleDefinition -Id c4e39bd9-1100-46d3-8c65-fb160da0071f -DisplayName "Updated DisplayName"
```

### <a name="delete-a-role-definition"></a>역할 정의 삭제

``` PowerShell
# Delete role definition
Remove-AzureADMSRoleDefinitions -Id c4e39bd9-1100-46d3-8c65-fb160da0071f
```

## <a name="role-assignments"></a>역할 할당

역할 할당에는 지정 된 보안 주체 (사용자 또는 응용 프로그램 서비스 주체)를 역할 정의에 연결 하는 정보가 포함 됩니다. 필요한 경우 할당 된 사용 권한에 대해 단일 Azure AD 리소스의 범위를 추가할 수 있습니다.  역할 할당 범위 제한은 기본 제공 및 사용자 지정 역할에 대해 지원 됩니다.

### <a name="create-a-role-assignment"></a>역할 할당 만들기

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

### <a name="read-and-list-role-assignments"></a>역할 할당 읽기 및 나열

``` PowerShell
# Get role assignments for a given principal
Get-AzureADMSRoleAssignment -Filter "principalId eq '27c8ca78-ab1c-40ae-bd1b-eaeebd6f68ac'"

# Get role assignments for a given role definition 
Get-AzureADMSRoleAssignment -Filter "roleDefinitionId eq '355aed8a-864b-4e2b-b225-ea95482e7570'"
```

### <a name="delete-a-role-assignment"></a>역할 할당 삭제

``` PowerShell
# Delete role assignment
Remove-AzureADMSRoleAssignment -Id 'qiho4WOb9UKKgng_LbPV7tvKaKRCD61PkJeKMh7Y458-1'
```

## <a name="next-steps"></a>다음 단계

- [AZURE AD 관리 역할 포럼](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032) 에서 microsoft와 공유
- 역할 및 Azure AD 관리자 역할 할당에 대 한 자세한 내용은 [관리자 역할 할당](permissions-reference.md) 을 참조 하세요.
- 기본 사용자 권한의 경우 [기본 게스트 및 멤버 사용자 권한 비교](../fundamentals/users-default-permissions.md) 를 참조 하세요.
