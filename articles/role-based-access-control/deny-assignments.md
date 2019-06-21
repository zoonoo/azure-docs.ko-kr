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
ms.date: 06/13/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: ''
ms.openlocfilehash: f15d6fd81337aa4a859539e86f37a516848c9370
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/17/2019
ms.locfileid: "67165993"
---
# <a name="understand-deny-assignments-for-azure-resources"></a>Azure 리소스에 대한 거부 할당 이해

‘거부 할당’은 역할 할당과 마찬가지로 액세스를 거부하기 위해 특정 범위에서 사용자, 그룹 또는 서비스 주체에게 거부 작업 세트를 연결합니다.  거부 할당은 역할 할당이 사용자에게 액세스 권한을 부여하더라도 특정 Azure 리소스 작업을 사용자가 수행할 수 없도록 차단합니다.

이 문서에서는 거부 할당을 정의하는 방법을 설명합니다.

## <a name="how-deny-assignments-are-created"></a>할당이 만들어진를 거부 하는 방법

할당에서 생성 되 고 리소스를 보호 하기 위해 Azure에서 관리를 거부 합니다. 예를 들어 Azure 청사진 및 Azure 관리 되는 앱 사용 하 여 시스템 관리 되는 리소스를 보호 하기 위해 할당을 거부 합니다. 자세한 내용은 [Azure Blueprints 리소스 잠금으로 새 리소스 보호](../governance/blueprints/tutorials/protect-new-resources.md)를 참조하세요.

## <a name="compare-role-assignments-and-deny-assignments"></a>역할 할당을 비교 하 고 할당 거부

할당 역할 할당으로 비슷한 패턴을 따르지만 수도 다음 몇 가지 차이점을 거부 합니다.

| 기능 | 역할 할당 | 할당 거부 |
| --- | --- | --- |
| 액세스 권한 부여 | :heavy_check_mark: |  |
| 액세스 거부 |  | :heavy_check_mark: |
| 직접 만들 수 있습니다. | :heavy_check_mark: |  |
| 범위에서 적용 | :heavy_check_mark: | :heavy_check_mark: |
| 보안 주체를 제외 합니다. |  | :heavy_check_mark: |
| 하위 범위를 상속 하지 않도록 |  | :heavy_check_mark: |
| 에 적용할 [클래식 구독 관리자](rbac-and-directory-admin-roles.md) 할당 |  | :heavy_check_mark: |

## <a name="deny-assignment-properties"></a>거부 할당 속성

 거부 할당에 포함된 속성은 다음과 같습니다.

> [!div class="mx-tableFixed"]
> | 자산 | 필수 | 형식 | 설명 |
> | --- | --- | --- | --- |
> | `DenyAssignmentName` | 예 | String | 거부 할당의 표시 이름입니다. 이름은 지정된 범위에 대해 고유해야 합니다. |
> | `Description` | 아닙니다. | String | 거부 할당의 설명입니다. |
> | `Permissions.Actions` | Actions 또는 DataActions 하나 이상 | 문자열[] | 거부 할당이 액세스를 차단하는 관리 작업을 지정하는 문자열 배열입니다. |
> | `Permissions.NotActions` | 아닙니다. | 문자열[] | 거부 할당에서 제외할 관리 작업을 지정하는 문자열 배열입니다. |
> | `Permissions.DataActions` | Actions 또는 DataActions 하나 이상 | 문자열[] | 거부 할당이 액세스를 차단하는 데이터 작업을 지정하는 문자열 배열입니다. |
> | `Permissions.NotDataActions` | 아닙니다. | 문자열[] | 거부 할당에서 제외할 데이터 작업을 지정하는 문자열 배열입니다. |
> | `Scope` | 아닙니다. | String | 거부 할당이 적용되는 범위를 지정하는 범위입니다. |
> | `DoNotApplyToChildScopes` | 아닙니다. | Boolean | 거부 할당이 하위 범위에 적용되는지 여부를 지정합니다. 기본값은 False입니다. |
> | `Principals[i].Id` | 예 | 문자열[] | 거부 할당이 적용되는 Azure AD 보안 주체 개체 ID(사용자, 그룹, 서비스 주체 또는 관리 ID)의 배열입니다. 모든 보안 주체를 나타내려면 빈 GUID `00000000-0000-0000-0000-000000000000`으로 설정합니다. |
> | `Principals[i].Type` | 아닙니다. | 문자열[] | Principals[i].Id로 표시되는 개체 유형의 배열입니다. 모든 보안 주체를 나타내려면 `SystemDefined`로 설정합니다. |
> | `ExcludePrincipals[i].Id` | 아닙니다. | 문자열[] | 거부 할당이 적용되지 않는 Azure AD 보안 주체 개체 ID(사용자, 그룹, 서비스 주체 또는 관리 ID)의 배열입니다. |
> | `ExcludePrincipals[i].Type` | 아닙니다. | 문자열[] | ExcludePrincipals[i].Id로 표시되는 개체 유형의 배열입니다. |
> | `IsSystemProtected` | 아닙니다. | Boolean | 이 거부 할당이 Azure에서 생성되었으며 편집하거나 삭제할 수 없는지 여부를 지정합니다. 현재 모든 거부 할당은 시스템에서 보호됩니다. |

## <a name="the-all-principals-principal"></a>모든 보안 주체 보안 주체

에 지원 거부 할당, 명명 된 시스템에 정의 된 보안 주체 *모든 보안 주체* 도입 되었습니다. 이 보안 주체는 Azure AD 디렉터리의 모든 사용자, 그룹, 서비스 주체 및 관리 ID를 나타냅니다. 보안 주체 ID가 0만 포함하는 GUID `00000000-0000-0000-0000-000000000000`이고 보안 주체 유형이 `SystemDefined`이면 보안 주체는 모든 보안 주체를 나타냅니다. Azure PowerShell 출력에서는 모든 보안 주체는 다음과 같습니다.

```azurepowershell
Principals              : {
                          DisplayName:  All Principals
                          ObjectType:   SystemDefined
                          ObjectId:     00000000-0000-0000-0000-000000000000
                          }
```

모든 보안 주체와 결합할 수 있습니다 `ExcludePrincipals` 일부 사용자를 제외한 모든 보안 주체를 거부 합니다. 모든 보안 주체에 다음 제약 조건이 있습니다.

- `Principals`에서만 사용할 수 있으며 `ExcludePrincipals`에서는 사용할 수 없습니다.
- `Principals[i].Type`은 `SystemDefined`로 설정해야 합니다.

## <a name="next-steps"></a>다음 단계

* [목록에는 Azure portal을 사용 하 여 Azure 리소스에 대 한 할당 거부](deny-assignments-portal.md)
* [Azure 리소스에 대한 역할 정의 이해](role-definitions.md)
