---
title: 목록에는 Azure PowerShell을 사용 하 여 Azure 리소스에 대 한 할당 거부 | Microsoft Docs
description: 사용자, 그룹, 서비스 주체 및 Azure PowerShell을 사용 하 여 특정 범위에서 특정 Azure 리소스 작업에 대 한 액세스가 거부 된 관리 되는 id를 나열 하는 방법에 알아봅니다.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/12/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: c1ea26fdb4d60262f89ea6ab0f87220a08c01e68
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67110486"
---
# <a name="list-deny-assignments-for-azure-resources-using-azure-powershell"></a>목록에는 Azure PowerShell을 사용 하 여 Azure 리소스에 대 한 할당 거부

[거부 할당](deny-assignments.md)은 역할 할당이 사용자에게 액세스 권한을 부여하더라도 특정 Azure 리소스 작업을 사용자가 수행할 수 없도록 차단합니다. 이 문서에서는 나열 하는 방법을 설명 Azure PowerShell을 사용 하 여 할당을 거부 합니다.

> [!NOTE]
> 직접 만들 수 고유한 할당을 거부 합니다. 할당이 만들어진 거부 하는 방법에 대 한 정보를 참조 하세요 [할당이 거부](deny-assignments.md)합니다.

## <a name="prerequisites"></a>필수 조건

Deny 할당에 대 한 정보를 가져오려면 다음이 있어야 합니다.

- `Microsoft.Authorization/denyAssignments/read` 대부분에서 포함 되어 있는 사용 권한을 [Azure 리소스에 대 한 기본 제공 역할](built-in-roles.md)
- [Azure Cloud Shell의 PowerShell](/azure/cloud-shell/overview) 또는 [Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="list-deny-assignments"></a>목록 할당 거부

### <a name="list-all-deny-assignments"></a>모든 목록 할당 거부

모든 거부 사용 하 여 현재 구독에 대 한 할당을 나열 하려면 [Get AzDenyAssignment](/powershell/module/az.resources/get-azdenyassignment)합니다.

```azurepowershell
Get-AzDenyAssignment
```

```Example
PS C:\> Get-AzDenyAssignment

Id                      : 22222222-2222-2222-2222-222222222222
DenyAssignmentName      : Deny assignment '22222222-2222-2222-2222-222222222222' created by Blueprint Assignment
                          '/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Blueprint/blueprintAssignments/assignment-locked-storageaccount-TestingBPLocks'.
Description             : Created by Blueprint Assignment '/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Blueprint/blueprintAssignments/assignment-locked-storageaccount-TestingBPLocks'.
Actions                 : {*}
NotActions              : {*/read}
DataActions             : {}
NotDataActions          : {}
Scope                   : /subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/TestingBPLocks
DoNotApplyToChildScopes : True
Principals              : {
                          DisplayName:  All Principals
                          ObjectType:   SystemDefined
                          ObjectId:     00000000-0000-0000-0000-000000000000
                          }
ExcludePrincipals       : {
                          ObjectType:   ServicePrincipal
                          }
IsSystemProtected       : True

Id                      : 33333333-3333-3333-3333-333333333333
DenyAssignmentName      : Deny assignment '33333333-3333-3333-3333-333333333333' created by Blueprint Assignment
                          '/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Blueprint/blueprintAssignments/assignment-locked-storageaccount-TestingBPLocks'.
Description             : Created by Blueprint Assignment '/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Blueprint/blueprintAssignments/assignment-locked-storageaccount-TestingBPLocks'.
Actions                 : {*}
NotActions              : {*/read}
DataActions             : {}
NotDataActions          : {}
Scope                   : /subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/TestingBPLocks/providers/Microsoft.Storage/storageAccounts/storep6vkuxmu4m4pq
DoNotApplyToChildScopes : True
Principals              : {
                          DisplayName:  All Principals
                          ObjectType:   SystemDefined
                          ObjectId:     00000000-0000-0000-0000-000000000000
                          }
ExcludePrincipals       : {
                          DisplayName:  assignment-locked-storageaccount-TestingBPLocks
                          ObjectType:   ServicePrincipal
                          ObjectId:     2311a0b7-657a-4ca2-af6f-d1c33f6d2fff
                          }
IsSystemProtected       : True
```

### <a name="list-deny-assignments-at-a-resource-group-scope"></a>목록에 리소스 그룹 범위에서 할당 거부

모든 거부 사용 하 여 리소스 그룹 범위에서 할당을 나열 하려면 [Get AzDenyAssignment](/powershell/module/az.resources/get-azdenyassignment)합니다.

```azurepowershell
Get-AzDenyAssignment -ResourceGroupName <resource_group_name>
```

```Example
PS C:\> Get-AzDenyAssignment -ResourceGroupName TestingBPLocks | FL DenyAssignmentName, Scope

DenyAssignmentName : Deny assignment '22222222-2222-2222-2222-222222222222' created by Blueprint Assignment
                     '/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Blueprint/blueprintAssignments/assignment-locked-storageaccount-TestingBPLocks'.
Scope              : /subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/TestingBPLocks
Principals         : {
                     DisplayName:       All Principals
                     ObjectType:        SystemDefined
                     ObjectId:  00000000-0000-0000-0000-000000000000
                     }
```

### <a name="list-deny-assignments-at-a-subscription-scope"></a>목록에 구독 범위에서 할당 거부

모든 거부 사용 하 여 구독 범위의 할당을 나열 하려면 [Get AzDenyAssignment](/powershell/module/az.resources/get-azdenyassignment)합니다. 구독 ID를 가져오려면에서 찾을 수 있습니다 하는 **구독** 블레이드에서 Azure portal에서 사용할 수 있습니다 [Get AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription)합니다.

```azurepowershell
Get-AzDenyAssignment -Scope /subscriptions/<subscription_id>
```

```Example
PS C:\> Get-AzDenyAssignment -Scope /subscriptions/11111111-1111-1111-1111-111111111111
```

## <a name="next-steps"></a>다음 단계

- [Azure 리소스에 대한 거부 할당 이해](deny-assignments.md)
- [목록에는 Azure portal을 사용 하 여 Azure 리소스에 대 한 할당 거부](deny-assignments-portal.md)
- [REST API를 사용하여 Azure 리소스에 대한 거부 할당 나열](deny-assignments-rest.md)