---
title: Azure RBAC의 거부 할당 이해 | Microsoft Docs
description: Azure의 리소스에 대한 RBAC(역할 기반 액세스 제어)의 거부 할당에 대해 알아봅니다.
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
ms.date: 09/24/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: ''
ms.openlocfilehash: 8ef3a2ec44c5eff80d3a50a6c56805667e164ba8
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46980171"
---
# <a name="understand-deny-assignments"></a>거부 할당 이해

*거부 할당*은 역할 할당과 마찬가지로 액세스를 거부하기 위해 특정 범위에서 사용자, 그룹 또는 서비스 주체에게 거부 작업 집합을 바인딩합니다. 거부 할당은 보안 주체를 제외하고 하위 범위로의 상속을 차단할 수도 있습니다. 이러한 방식은 역할 할당과는 다릅니다. 현재 거부 할당은**읽기 전용**이며 Azure를 통해서만 설정할 수 있습니다. 이 문서에서는 거부 할당을 정의하는 방법을 설명합니다.

## <a name="deny-assignment-properties"></a>거부 할당 속성

 거부 할당에 포함된 속성은 다음과 같습니다.

> [!div class="mx-tableFixed"]
> | 자산 | 필수 | 형식 | 설명 |
> | --- | --- | --- | --- |
> | `DenyAssignmentName` | yes | 문자열 | 거부 할당의 표시 이름입니다. 이름은 지정된 범위에 대해 고유해야 합니다. |
> | `Description` | 아니요 | 문자열 | 거부 할당의 설명입니다. |
> | `Permissions.Actions` | Actions 또는 DataActions 하나 이상 | 문자열[] | 거부 할당이 액세스를 차단하는 관리 작업을 지정하는 문자열 배열입니다. |
> | `Permissions.NotActions` | 아니요 | 문자열[] | 거부 할당에서 제외할 관리 작업을 지정하는 문자열 배열입니다. |
> | `Permissions.DataActions` | Actions 또는 DataActions 하나 이상 | 문자열[] | 거부 할당이 액세스를 차단하는 데이터 작업을 지정하는 문자열 배열입니다. |
> | `Permissions.NotDataActions` | 아니요 | 문자열[] | 거부 할당에서 제외할 데이터 작업을 지정하는 문자열 배열입니다. |
> | `Scope` | 아니요 | 문자열 | 거부 할당이 적용되는 범위를 지정하는 범위입니다. |
> | `DoNotApplyToChildScopes` | 아니요 | BOOLEAN | 거부 할당이 하위 범위에 적용되는지 여부를 지정합니다. 기본값은 False입니다. |
> | `Principals[i].Id` | yes | 문자열[] | 거부 할당이 적용되는 Azure AD 보안 주체 개체 ID(사용자, 그룹 또는 서비스 주체)의 배열입니다. 모든 사용자를 나타내려면 빈 GUID `00000000-0000-0000-0000-000000000000`으로 설정합니다. |
> | `Principals[i].Type` | 아니요 | 문자열[] | Principals[i].Id로 표시되는 개체 유형의 배열입니다. 모든 사용자를 나타내려면 `Everyone`으로 설정합니다. |
> | `ExcludePrincipals[i].Id` | 아니요 | 문자열[] | 거부 할당이 적용되지 않는 Azure AD 보안 주체 개체 ID(사용자, 그룹 또는 서비스 주체)의 배열입니다. |
> | `ExcludePrincipals[i].Type` | 아니요 | 문자열[] | ExcludePrincipals[i].Id로 표시되는 개체 유형의 배열입니다. |
> | `IsSystemProtected` | 아니요 | BOOLEAN | 이 거부 할당이 Azure에서 생성되었으며 편집하거나 삭제할 수 없는지 여부를 지정합니다. 현재 모든 거부 할당은 시스템에서 보호됩니다. |

## <a name="everyone-principal"></a>모든 사용자 보안 주체

거부 할당을 지원하기 위해 모든 사용자 보안 주체가 도입되었습니다. 모든 사용자 보안 주체는 Azure AD 디렉터리의 모든 사용자, 그룹 및 서비스 주체를 나타냅니다. 보안 주체 ID가 0만 포함하는 GUID `00000000-0000-0000-0000-000000000000`이고 보안 주체 유형이 `Everyone`이면 보안 주체는 모든 사용자를 나타냅니다. 모든 사용자 보안 주체를 `ExcludePrincipals`와 결합하면 일부 사용자를 제외한 모든 사용자를 거부할 수 있습니다. 모든 사용자 보안 주체의 제약 조건은 다음과 같습니다.

- `Principals`에서만 사용할 수 있으며 `ExcludePrincipals`에서는 사용할 수 없습니다.
- `Principals[i].Type`은 `Everyone`로 설정해야 합니다.

## <a name="next-steps"></a>다음 단계

* [RBAC 및 REST API를 사용하여 거부 할당 나열](deny-assignments-rest.md)
* [역할 정의 이해](role-definitions.md)
