---
title: Azure 리소스에 대한 거부 할당 이해
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
ms.date: 03/26/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: ''
ms.openlocfilehash: db249ccde1026cd468a1c30942891119482697ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80372478"
---
# <a name="understand-deny-assignments-for-azure-resources"></a>Azure 리소스에 대한 거부 할당 이해

‘거부 할당’은 역할 할당과 마찬가지로 액세스를 거부하기 위해 특정 범위에서 사용자, 그룹 또는 서비스 주체에게 거부 작업 세트를 연결합니다.** 거부 할당은 역할 할당이 사용자에게 액세스 권한을 부여하더라도 특정 Azure 리소스 작업을 사용자가 수행할 수 없도록 차단합니다.

이 문서에서는 거부 할당을 정의하는 방법을 설명합니다.

## <a name="how-deny-assignments-are-created"></a>할당 거부 를 생성하는 방법

거부 할당은 Azure에서 만들고 관리하여 리소스를 보호합니다. Azure Blueprint및 Azure 관리형 앱은 거부 할당을 사용하여 시스템 관리 되는 리소스를 보호합니다. Azure Blueprint및 Azure 관리되는 앱은 할당을 거부할 수 있는 유일한 방법입니다. 거부 할당을 직접 만들 수는 없습니다. Blueprint가 거부 할당을 사용하여 리소스를 잠그는 방법에 대한 자세한 내용은 [Azure Blueprint에서 리소스 잠금 이해 를](../governance/blueprints/concepts/resource-locking.md)참조하십시오.

> [!NOTE]
> 거부 할당을 직접 만들 수는 없습니다.

## <a name="compare-role-assignments-and-deny-assignments"></a>역할 할당 및 할당 거부 비교

할당 거부는 역할 할당과 유사한 패턴을 따르지만 몇 가지 차이점도 있습니다.

| 기능 | 역할 할당 | 할당 거부 |
| --- | --- | --- |
| 액세스 권한 부여 | :heavy_check_mark: |  |
| 액세스 거부 |  | :heavy_check_mark: |
| 직접 만들 수 있습니다. | :heavy_check_mark: |  |
| 범위에서 적용 | :heavy_check_mark: | :heavy_check_mark: |
| 보안 주체 제외 |  | :heavy_check_mark: |
| 자식 범위에 대한 상속 방지 |  | :heavy_check_mark: |
| 클래식 [구독 관리자](rbac-and-directory-admin-roles.md) 할당에 적용 |  | :heavy_check_mark: |

## <a name="deny-assignment-properties"></a>거부 할당 속성

 거부 할당에 포함된 속성은 다음과 같습니다.

> [!div class="mx-tableFixed"]
> | 속성 | 필수 | Type | Description |
> | --- | --- | --- | --- |
> | `DenyAssignmentName` | yes | String | 거부 할당의 표시 이름입니다. 이름은 지정된 범위에 대해 고유해야 합니다. |
> | `Description` | 예 | String | 거부 할당의 설명입니다. |
> | `Permissions.Actions` | Actions 또는 DataActions 하나 이상 | String[] | 거부 할당이 액세스를 차단하는 관리 작업을 지정하는 문자열 배열입니다. |
> | `Permissions.NotActions` | 예 | String[] | 거부 할당에서 제외할 관리 작업을 지정하는 문자열 배열입니다. |
> | `Permissions.DataActions` | Actions 또는 DataActions 하나 이상 | String[] | 거부 할당이 액세스를 차단하는 데이터 작업을 지정하는 문자열 배열입니다. |
> | `Permissions.NotDataActions` | 예 | String[] | 거부 할당에서 제외할 데이터 작업을 지정하는 문자열 배열입니다. |
> | `Scope` | 예 | String | 거부 할당이 적용되는 범위를 지정하는 범위입니다. |
> | `DoNotApplyToChildScopes` | 예 | 부울 | 거부 할당이 하위 범위에 적용되는지 여부를 지정합니다. 기본값은 False입니다. |
> | `Principals[i].Id` | yes | String[] | 거부 할당이 적용되는 Azure AD 보안 주체 개체 ID(사용자, 그룹, 서비스 주체 또는 관리 ID)의 배열입니다. 모든 보안 주체를 나타내려면 빈 GUID `00000000-0000-0000-0000-000000000000`으로 설정합니다. |
> | `Principals[i].Type` | 예 | String[] | 보안 주체[i].Id.로 표시되는 개체 형식의 배열은 모든 보안 주체를 나타내도록 `SystemDefined` 설정합니다. |
> | `ExcludePrincipals[i].Id` | 예 | String[] | 거부 할당이 적용되지 않는 Azure AD 보안 주체 개체 ID(사용자, 그룹, 서비스 주체 또는 관리 ID)의 배열입니다. |
> | `ExcludePrincipals[i].Type` | 예 | String[] | ExcludePrincipals[i].Id로 표시되는 개체 유형의 배열입니다. |
> | `IsSystemProtected` | 예 | 부울 | 이 거부 할당이 Azure에서 생성되었으며 편집하거나 삭제할 수 없는지 여부를 지정합니다. 현재 모든 거부 할당은 시스템에서 보호됩니다. |

## <a name="the-all-principals-principal"></a>모든 보안 주체

거부 할당을 지원하기 위해 *모든 보안 주체라는* 시스템 정의 보안 주체가 도입되었습니다. 이 보안 주체는 Azure AD 디렉터리의 모든 사용자, 그룹, 서비스 주체 및 관리 ID를 나타냅니다. 보안 주체 ID가 0만 포함하는 GUID `00000000-0000-0000-0000-000000000000`이고 보안 주체 유형이 `SystemDefined`이면 보안 주체는 모든 보안 주체를 나타냅니다. Azure PowerShell 출력에서 모든 보안 주체는 다음과 같습니다.

```azurepowershell
Principals              : {
                          DisplayName:  All Principals
                          ObjectType:   SystemDefined
                          ObjectId:     00000000-0000-0000-0000-000000000000
                          }
```

일부 사용자를 제외한 모든 `ExcludePrincipals` 보안 주체를 함께 결합하여 보안 주체를 거부할 수 있습니다. 모든 보안 주체에는 다음과 같은 제약 조건이 있습니다.

- `Principals`에서만 사용할 수 있으며 `ExcludePrincipals`에서는 사용할 수 없습니다.
- `Principals[i].Type`은 `SystemDefined`로 설정해야 합니다.

## <a name="next-steps"></a>다음 단계

* [자습서: Azure Blueprints 리소스 잠금을 사용 하 고 새 리소스를 보호 합니다.](../governance/blueprints/tutorials/protect-new-resources.md)
* [Azure 포털을 사용하여 Azure 리소스에 대한 할당 거부 목록](deny-assignments-portal.md)
