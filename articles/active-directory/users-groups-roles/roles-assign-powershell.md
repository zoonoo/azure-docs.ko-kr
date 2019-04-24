---
title: 할당 및 Azure PowerShell-Azure Active Directory를 사용 하 여 관리자 역할 할당 제거 | Microsoft Docs
description: 사용자에 게 자주 역할 할당을 관리, 이제 Azure PowerShell을 사용 하 여 Azure AD 관리자 역할의 멤버를 관리할 수 있습니다.
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 04/15/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: f6877c3e547d625cf58129a546dae798b37a24ae
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60469097"
---
# <a name="assign-azure-active-directory-admin-roles-using-powershell"></a>PowerShell을 사용 하 여 Azure Active Directory 관리자 역할 할당

Azure PowerShell을 사용 하 여 사용자 계정에 역할을 할당 하는 방법을 자동화할 수 있습니다. 이 문서에서는 합니다 [Azure Active Directory PowerShell 버전 2](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#directory_roles) 모듈입니다.

## <a name="prepare-powershell"></a>PowerShell을 준비

먼저 [Azure AD PowerShell 모듈을 다운로드](https://www.powershellgallery.com/packages/AzureAD/)해야 합니다.

## <a name="install-the-azure-ad-powershell-module"></a>Azure AD PowerShell 모듈 설치

Azure AD PowerShell 모듈을 설치하려면 다음 명령을 사용합니다.

```powershell
install-module azuread
import-module azuread
```

모듈을 사용할 수 있는지 확인하려면 다음 명령을 사용합니다.

```powershell
get-module azuread
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.0.115    azuread                      {Add-AzureADAdministrati...}
```

이제 모듈에서 cmdlet 사용을 시작할 수 있습니다. Azure AD 모듈의 cmdlet에 대한 자세한 내용은 [Azure Active Directory PowerShell 버전 2](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#directory_roles)에 대한 온라인 참조 문서를 참조하세요.

## <a name="permissions-required"></a>필요한 사용 권한

역할 할당 또는 제거 하려면 전역 관리자 계정을 사용 하 여 Azure AD 테 넌 트에 연결 합니다.

## <a name="assign-a-single-role"></a>단일 역할 할당

역할에 할당 하려면 먼저 할당 하려는 역할의 이름과 표시 이름만 얻어야 합니다. 계정의 표시 이름 및 역할의 이름에 있는 경우 사용자에 게 역할을 할당 하려면 다음 cmdlet을 사용 합니다.

``` PowerShell
# Fetch user to assign to role
$roleMember = Get-AzureADUser -ObjectId "username@contoso.com"

# Fetch User Account Administrator role instance
$role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq 'User Account Administrator'}

# If role instance does not exist, instantiate it based on the role template
if ($role -eq $null) {
    # Instantiate an instance of the role template
    $roleTemplate = Get-AzureADDirectoryRoleTemplate | Where-Object {$_.displayName -eq 'User Account Administrator'}
    Enable-AzureADDirectoryRole -RoleTemplateId $roleTemplate.ObjectId

    # Fetch User Account Administrator role instance again
    $role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq 'User Account Administrator'}
}

# Add user to role
Add-AzureADDirectoryRoleMember -ObjectId $role.ObjectId -RefObjectId $roleMember.ObjectId

# Fetch role membership for role to confirm
Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId | Get-AzureADUser
```

## <a name="assign-a-role-to-a-service-principal"></a>서비스 주체에 역할 할당

역할에 서비스 주체를 할당 하는 예입니다.

```powershell
# Fetch a service principal to assign to role
$roleMember = Get-AzureADServicePrincipal -ObjectId "00221b6f-4387-4f3f-aa85-34316ad7f956"
 
#Fetch list of all directory roles with object ID
Get-AzureADDirectoryRole
 
# Fetch a directory role by ID
$role = Get-AzureADDirectoryRole -ObjectId "5b3fe201-fa8b-4144-b6f1-875829ff7543"
 
# Add user to role
Add-AzureADDirectoryRoleMember -ObjectId $role.ObjectId -RefObjectId $roleMember.ObjectId
 
# Fetch the assignment for the role to confirm
Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId | Get-AzureADServicePrincipal
```

## <a name="multiple-role-assignments"></a>여러 역할 할당

할당 하 고 한 번에 여러 역할을 제거 하는 예제입니다.

```powershell
#File name
$fileName="<file path>" 
 
$input_Excel = New-Object -ComObject Excel.Application
$input_Workbook = $input_Excel.Workbooks.Open($fileName)
$input_Worksheet = $input_Workbook.Sheets.Item(1)
 
        #Count number of users who have to be assigned to role
$count = $input_Worksheet.UsedRange.Rows.Count
 
#Loop through each line of the csv file starting from line 2 (assuming first line is title)
for ($i=2; $i -le $count; $i++)
{
       #Fetch user display name
       $displayName = $input_Worksheet.Cells.Item($i,1).Text
       
       #Fetch role name
       $roleName = $input_Worksheet.Cells.Item($i,2).Text
 
       #Assign role
       Add-AzureADDirectoryRoleMember -ObjectId (Get-AzureADDirectoryRole | Where-Object DisplayName -eq $roleName).ObjectId -RefObjectId (Get-AzureADUser | Where-Object DisplayName -eq $displayName).ObjectId
}
 
#Remove multiple role assignments
for ($i=2; $i -le $count; $i++)
{
       $displayName = $input_Worksheet.Cells.Item($i,1).Text
       $roleName = $input_Worksheet.Cells.Item($i,2).Text
 
       Remove-AzureADDirectoryRoleMember -ObjectId (Get-AzureADDirectoryRole | Where-Object DisplayName -eq $roleName).ObjectId -MemberId (Get-AzureADUser | Where-Object DisplayName -eq $displayName).ObjectId
}
```

## <a name="remove-a-role-assignment"></a>역할 할당 제거

이 예제에서는 지정된 된 사용자에 대 한 역할 할당을 제거합니다.

```powershell
# Fetch user to assign to role
$roleMember = Get-AzureADUser -ObjectId "username@contoso.com"

#Fetch list of all directory roles with object id
Get-AzureADDirectoryRole
 
# Fetch a directory role by id
$role = Get-AzureADDirectoryRole -ObjectId "5b3fe201-fa8b-4144-b6f1-875829ff7543"
 
# Remove user from role
Remove-AzureADDirectoryRoleMember -ObjectId $role.ObjectId -MemberId $roleMember.ObjectId 

# Fetch role membership for role to confirm
Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId | Get-AzureADUser
 
```

## <a name="next-steps"></a>다음 단계

* [Azure AD 관리 역할 포럼](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)에서 경험을 자유롭게 공유하세요.
* 역할 및 관리자 역할 할당에 대한 자세한 내용은 [관리자 역할 할당](directory-assign-admin-roles.md)을 참조하세요.
* 기본 사용자 권한의 경우 [기본 게스트 및 멤버 사용자 권한 비교](../fundamentals/users-default-permissions.md)를 참조하세요.
