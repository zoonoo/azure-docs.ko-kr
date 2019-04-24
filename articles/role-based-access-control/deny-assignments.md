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
ms.date: 03/13/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: ''
ms.openlocfilehash: 497571a65510f806d7d7994c9dc37f9a00b65a5f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60197139"
---
# <a name="understand-deny-assignments-for-azure-resources"></a>Azure 리소스에 대한 거부 할당 이해

‘거부 할당’은 역할 할당과 마찬가지로 액세스를 거부하기 위해 특정 범위에서 사용자, 그룹 또는 서비스 주체에게 거부 작업 세트를 연결합니다. 거부 할당은 역할 할당이 사용자에게 액세스 권한을 부여하더라도 특정 Azure 리소스 작업을 사용자가 수행할 수 없도록 차단합니다. 이제 Azure의 일부 리소스 공급자에는 거부 할당이 포함되어 있습니다.

일부 측면에서 거부 할당은 역할 할당과 다릅니다. 거부 할당은 보안 주체를 제외하고 자식 범위로의 상속을 차단할 수 있습니다. 거부 할당은 [클래식 구독 관리자](rbac-and-directory-admin-roles.md) 할당에도 적용됩니다.

이 문서에서는 거부 할당을 정의하는 방법을 설명합니다.

> [!NOTE]
> 현재 자체 거부 할당을 추가할 수 있는 유일한 방법은 Azure Blueprints를 사용하는 것입니다. 자세한 내용은 [Azure Blueprints 리소스 잠금으로 새 리소스 보호](../governance/blueprints/tutorials/protect-new-resources.md)를 참조하세요.

## <a name="deny-assignment-properties"></a>거부 할당 속성

 거부 할당에 포함된 속성은 다음과 같습니다.

> [!div class="mx-tableFixed"]
> | 자산 | 필수 | 형식 | 설명 |
> | --- | --- | --- | --- |
> | `DenyAssignmentName` | 예. | String | 거부 할당의 표시 이름입니다. 이름은 지정된 범위에 대해 고유해야 합니다. |
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

## <a name="system-defined-principal"></a>시스템 정의 보안 주체

거부 할당을 지원하기 위해 **시스템 정의 보안 주체**가 도입되었습니다. 이 보안 주체는 Azure AD 디렉터리의 모든 사용자, 그룹, 서비스 주체 및 관리 ID를 나타냅니다. 보안 주체 ID가 0만 포함하는 GUID `00000000-0000-0000-0000-000000000000`이고 보안 주체 유형이 `SystemDefined`이면 보안 주체는 모든 보안 주체를 나타냅니다. `SystemDefined`와 `ExcludePrincipals`를 결합하면 일부 사용자를 제외한 모든 보안 주체를 거부할 수 있습니다. `SystemDefined`에는 다음 제약 조건이 있습니다.

- `Principals`에서만 사용할 수 있으며 `ExcludePrincipals`에서는 사용할 수 없습니다.
- `Principals[i].Type`은 `SystemDefined`로 설정해야 합니다.

## <a name="next-steps"></a>다음 단계

* [보기에는 Azure portal을 사용 하 여 Azure 리소스에 대 한 할당 거부](deny-assignments-portal.md)
* [Azure 리소스에 대한 역할 정의 이해](role-definitions.md)
