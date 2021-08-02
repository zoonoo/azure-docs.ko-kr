---
title: Azure PowerShell을 사용하여 Azure 거부 할당 나열 - Azure RBAC
description: Azure PowerShell 및 Azure RBAC(역할 기반 액세스 제어)를 사용하여 특정 범위에서 특정 Azure 리소스 작업에 대한 액세스가 거부된 사용자, 그룹, 서비스 주체, 관리 ID를 나열하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/12/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: ab180dcbf12b43a3bd60a88767169877be5e7e2f
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2021
ms.locfileid: "110696560"
---
# <a name="list-azure-deny-assignments-using-azure-powershell"></a>Azure PowerShell을 사용한 Azure 거부 할당 나열

[Azure 거부 할당](deny-assignments.md)은 역할 할당이 사용자에게 액세스 권한을 부여하더라도 사용자가 특정 Azure 리소스 작업을 수행할 수 없도록 차단합니다. 이 문서에서는 Azure PowerShell을 사용하여 거부 할당을 나열하는 방법에 관해 설명합니다.

> [!NOTE]
> 사용자 고유의 거부 할당을 직접 만들 수는 없습니다. 거부 할당이 만들어지는 방법에 대한 자세한 내용은 [Azure 거부 할당](deny-assignments.md)을 참조하세요.

## <a name="prerequisites"></a>필수 조건

거부 할당에 대한 정보를 가져오려면 다음이 있어야 합니다.

- 대부분의 [Azure 기본 제공 역할](built-in-roles.md)에 포함되어 있는 `Microsoft.Authorization/denyAssignments/read` 권한
- [Azure Cloud Shell의 PowerShell](../cloud-shell/overview.md) 또는 [Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="list-deny-assignments"></a>거부 할당 목록

### <a name="list-all-deny-assignments"></a>모든 거부 할당 나열

현재 구독에 대한 모든 거부 할당을 나열하려면 [Get-AzDenyAssignment](/powershell/module/az.resources/get-azdenyassignment)를 사용합니다.

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

### <a name="list-deny-assignments-at-a-resource-group-scope"></a>리소스 그룹 범위에서 거부 할당 나열

리소스 그룹 범위에서 모든 거부 할당을 나열하려면 [Get-AzDenyAssignment](/powershell/module/az.resources/get-azdenyassignment)를 사용합니다.

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

### <a name="list-deny-assignments-at-a-subscription-scope"></a>구독 범위에서 거부 할당 나열

구독 범위에서 모든 거부 할당을 나열하려면 [Get-AzDenyAssignment](/powershell/module/az.resources/get-azdenyassignment)를 사용합니다. 구독 ID를 가져오려면 Azure Portal의 **구독** 블레이드에서 확인하거나 [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription)을 사용할 수 있습니다.

```azurepowershell
Get-AzDenyAssignment -Scope /subscriptions/<subscription_id>
```

```Example
PS C:\> Get-AzDenyAssignment -Scope /subscriptions/11111111-1111-1111-1111-111111111111
```

## <a name="next-steps"></a>다음 단계

- [Azure 거부 할당 이해](deny-assignments.md)
- [Azure Portal을 사용한 Azure 거부 할당 나열](deny-assignments-portal.md)
- [REST API를 사용하여 Azure 거부 할당 나열](deny-assignments-rest.md)
