---
title: Azure PowerShell을 사용하면 Azure 리소스에 대한 할당 거부 목록
description: Azure PowerShell을 사용하여 특정 범위에서 특정 Azure 리소스 작업에 대한 액세스가 거부된 사용자, 그룹, 서비스 주체 및 관리되는 ID를 나열하는 방법을 알아봅니다.
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
ms.openlocfilehash: 5ba18b89bd37dbd55350321c503e37ab0590ab87
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77137390"
---
# <a name="list-deny-assignments-for-azure-resources-using-azure-powershell"></a>Azure PowerShell을 사용하여 Azure 리소스에 대한 할당 거부 목록

[할당 거부는](deny-assignments.md) 역할 할당이 액세스 권한을 부여하는 경우에도 사용자가 특정 Azure 리소스 작업을 수행하지 못하도록 차단합니다. 이 문서에서는 Azure PowerShell을 사용하여 거부 할당을 나열하는 방법에 대해 설명합니다.

> [!NOTE]
> 거부 할당을 직접 만들 수는 없습니다. 할당 거부를 생성하는 방법에 대한 자세한 내용은 [할당 거부를](deny-assignments.md)참조하십시오.

## <a name="prerequisites"></a>사전 요구 사항

거부 할당에 대한 정보를 얻으려면 다음이 있어야 합니다.

- `Microsoft.Authorization/denyAssignments/read`권한, [Azure 리소스에 대 한](built-in-roles.md) 대부분의 기본 제공 역할에 포함 된
- Azure 클라우드 셸 또는 [Azure PowerShell의](/powershell/azure/install-az-ps) [PowerShell](/azure/cloud-shell/overview)

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

### <a name="list-deny-assignments-at-a-resource-group-scope"></a>리소스 그룹 범위에서 거부 할당 목록

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

### <a name="list-deny-assignments-at-a-subscription-scope"></a>구독 범위에서 할당 거부 목록

구독 범위에서 모든 거부 할당을 나열하려면 [Get-AzDenyAssignment](/powershell/module/az.resources/get-azdenyassignment)를 사용합니다. 구독 ID를 얻으려면 Azure 포털의 **구독** 블레이드에서 찾거나 [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription)을 사용할 수 있습니다.

```azurepowershell
Get-AzDenyAssignment -Scope /subscriptions/<subscription_id>
```

```Example
PS C:\> Get-AzDenyAssignment -Scope /subscriptions/11111111-1111-1111-1111-111111111111
```

## <a name="next-steps"></a>다음 단계

- [Azure 리소스에 대한 거부 할당 이해](deny-assignments.md)
- [Azure 포털을 사용하여 Azure 리소스에 대한 할당 거부 목록](deny-assignments-portal.md)
- [REST API를 사용하여 Azure 리소스에 대한 거부 할당 나열](deny-assignments-rest.md)