---
title: Azure 리소스에 대한 거부 할당 이해 | Microsoft Docs
description: Azure 리소스에 대한 RBAC(역할 기반 액세스 제어)의 거부 할당을 알아봅니다.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: ''
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/25/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: ''
ms.openlocfilehash: 2c663b587d2e9ee278fc774c2841899b060ccbcf
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/25/2019
ms.locfileid: "74479363"
---
# <a name="understand-deny-assignments-for-azure-resources"></a>Azure 리소스에 대한 거부 할당 이해

‘거부 할당’은 역할 할당과 마찬가지로 액세스를 거부하기 위해 특정 범위에서 사용자, 그룹 또는 서비스 주체에게 거부 작업 세트를 연결합니다. 거부 할당은 역할 할당이 사용자에게 액세스 권한을 부여하더라도 특정 Azure 리소스 작업을 사용자가 수행할 수 없도록 차단합니다.

이 문서에서는 거부 할당을 정의하는 방법을 설명합니다.

## <a name="how-deny-assignments-are-created"></a>How deny assignments are created

Deny assignments are created and managed by Azure to protect resources. Azure Blueprints and Azure managed apps use deny assignments to protect system-managed resources. Azure Blueprints and Azure managed apps are the only way that deny assignments can be created. You can't directly create your own deny assignments.  자세한 내용은 [Azure Blueprints 리소스 잠금으로 새 리소스 보호](../governance/blueprints/tutorials/protect-new-resources.md)를 참조하세요.

> [!NOTE]
> You can't directly create your own deny assignments.

## <a name="compare-role-assignments-and-deny-assignments"></a>Compare role assignments and deny assignments

Deny assignments follow a similar pattern as role assignments, but also have some differences.

| 기능 | 역할 할당 | Deny assignment |
| --- | --- | --- |
| 액세스 권한 부여 | :heavy_check_mark: |  |
| 액세스 거부 |  | :heavy_check_mark: |
| Can be directly created | :heavy_check_mark: |  |
| Apply at a scope | :heavy_check_mark: | :heavy_check_mark: |
| Exclude principals |  | :heavy_check_mark: |
| Prevent inheritance to child scopes |  | :heavy_check_mark: |
| Apply to [classic subscription administrator](rbac-and-directory-admin-roles.md) assignments |  | :heavy_check_mark: |

## <a name="deny-assignment-properties"></a>거부 할당 속성

 거부 할당에 포함된 속성은 다음과 같습니다.

> [!div class="mx-tableFixed"]
> | 자산 | 필수 | Type | 설명 |
> | --- | --- | --- | --- |
> | `DenyAssignmentName` | yes | string | 거부 할당의 표시 이름입니다. 이름은 지정된 범위에 대해 고유해야 합니다. |
> | `Description` | 아닙니다. | string | 거부 할당의 설명입니다. |
> | `Permissions.Actions` | Actions 또는 DataActions 하나 이상 | 문자열[] | 거부 할당이 액세스를 차단하는 관리 작업을 지정하는 문자열 배열입니다. |
> | `Permissions.NotActions` | 아닙니다. | 문자열[] | 거부 할당에서 제외할 관리 작업을 지정하는 문자열 배열입니다. |
> | `Permissions.DataActions` | Actions 또는 DataActions 하나 이상 | 문자열[] | 거부 할당이 액세스를 차단하는 데이터 작업을 지정하는 문자열 배열입니다. |
> | `Permissions.NotDataActions` | 아닙니다. | 문자열[] | 거부 할당에서 제외할 데이터 작업을 지정하는 문자열 배열입니다. |
> | `Scope` | 아닙니다. | string | 거부 할당이 적용되는 범위를 지정하는 범위입니다. |
> | `DoNotApplyToChildScopes` | 아닙니다. | Boolean | 거부 할당이 하위 범위에 적용되는지 여부를 지정합니다. 기본값은 False입니다. |
> | `Principals[i].Id` | yes | 문자열[] | 거부 할당이 적용되는 Azure AD 보안 주체 개체 ID(사용자, 그룹, 서비스 주체 또는 관리 ID)의 배열입니다. 모든 보안 주체를 나타내려면 빈 GUID `00000000-0000-0000-0000-000000000000`으로 설정합니다. |
> | `Principals[i].Type` | 아닙니다. | 문자열[] | An array of object types represented by Principals[i].Id. Set to `SystemDefined` to represent all principals. |
> | `ExcludePrincipals[i].Id` | 아닙니다. | 문자열[] | 거부 할당이 적용되지 않는 Azure AD 보안 주체 개체 ID(사용자, 그룹, 서비스 주체 또는 관리 ID)의 배열입니다. |
> | `ExcludePrincipals[i].Type` | 아닙니다. | 문자열[] | ExcludePrincipals[i].Id로 표시되는 개체 유형의 배열입니다. |
> | `IsSystemProtected` | 아닙니다. | Boolean | 이 거부 할당이 Azure에서 생성되었으며 편집하거나 삭제할 수 없는지 여부를 지정합니다. 현재 모든 거부 할당은 시스템에서 보호됩니다. |

## <a name="the-all-principals-principal"></a>The All Principals principal

To support deny assignments, a system-defined principal named *All Principals* has been introduced. 이 보안 주체는 Azure AD 디렉터리의 모든 사용자, 그룹, 서비스 주체 및 관리 ID를 나타냅니다. 보안 주체 ID가 0만 포함하는 GUID `00000000-0000-0000-0000-000000000000`이고 보안 주체 유형이 `SystemDefined`이면 보안 주체는 모든 보안 주체를 나타냅니다. In Azure PowerShell output, All Principals looks like the following:

```azurepowershell
Principals              : {
                          DisplayName:  All Principals
                          ObjectType:   SystemDefined
                          ObjectId:     00000000-0000-0000-0000-000000000000
                          }
```

All Principals can be combined with `ExcludePrincipals` to deny all principals except some users. All Principals has the following constraints:

- `Principals`에서만 사용할 수 있으며 `ExcludePrincipals`에서는 사용할 수 없습니다.
- `Principals[i].Type`은 `SystemDefined`로 설정해야 합니다.

## <a name="next-steps"></a>다음 단계

* [List deny assignments for Azure resources using the Azure portal](deny-assignments-portal.md)
* [Azure 리소스에 대한 역할 정의 이해](role-definitions.md)
