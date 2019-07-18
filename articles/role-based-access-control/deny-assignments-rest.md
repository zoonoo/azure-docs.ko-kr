---
title: REST API를 사용하여 Azure 리소스에 대한 거부 할당 나열 - Azure | Microsoft Docs
description: 나열 하는 방법은 사용자, 그룹 및 역할 기반 액세스 제어 (RBAC)를 사용 하 여 Azure 리소스 및 REST API에 대 한 응용 프로그램에 대 한 할당을 거부 합니다.
services: active-directory
documentationcenter: na
author: rolyon
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: role-based-access-control
ms.workload: multiple
ms.tgt_pltfrm: rest-api
ms.devlang: na
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 0bc49456f5965846a2de542b4a063bab2d1838bf
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67118297"
---
# <a name="list-deny-assignments-for-azure-resources-using-the-rest-api"></a>REST API를 사용하여 Azure 리소스에 대한 거부 할당 나열

[거부 할당](deny-assignments.md)은 역할 할당이 사용자에게 액세스 권한을 부여하더라도 특정 Azure 리소스 작업을 사용자가 수행할 수 없도록 차단합니다. 이 문서에서는 나열 하는 방법을 설명 REST API를 사용 하 여 할당을 거부 합니다.

> [!NOTE]
> 직접 만들 수 고유한 할당을 거부 합니다. 할당이 만들어진 거부 하는 방법에 대 한 정보를 참조 하세요 [할당이 거부](deny-assignments.md)합니다.

## <a name="prerequisites"></a>필수 조건

Deny 할당에 대 한 정보를 가져오려면 다음이 있어야 합니다.

- `Microsoft.Authorization/denyAssignments/read` 대부분의 포함 된 사용 권한 [Azure 리소스에 대 한 기본 제공 역할](built-in-roles.md)입니다.

## <a name="list-a-single-deny-assignment"></a>단일 거부 할당 나열

1. 다음 요청으로 시작합니다.

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/denyAssignments/{deny-assignment-id}?api-version=2018-07-01-preview
    ```

1. URI 내에서 *{scope}* 를 거부 할당을 나열하려는 범위로 바꿉니다.

    | Scope | Type |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | 구독 |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | 리소스 그룹 |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Resource |

1. *{deny-assignment-id}* 를 검색하려는 거부 할당 식별자로 바꿉니다.

## <a name="list-multiple-deny-assignments"></a>여러 거부 할당 나열

1. 다음 요청 중 하나로 시작합니다.

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview
    ```

    선택적 매개 변수 사용:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview&$filter={filter}
    ```

1. URI 내에서 *{scope}* 를 거부 할당을 나열하려는 범위로 바꿉니다.

    | Scope | Type |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | 구독 |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | 리소스 그룹 |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Resource |

1. *{filter}* 를 거부 할당 목록을 필터링하기 위해 적용하려는 조건으로 바꿉니다.

    | Filter | 설명 |
    | --- | --- |
    | (필터링 안 함) | 지정된 범위, 그 위 및 그 아래에 있는 모든 거부 할당을 나열합니다. |
    | `$filter=atScope()` | 지정된 범위 및 그 위에 있는 거부 할당만 나열합니다. 하위 범위에 있는 거부 할당을 포함하지 않습니다. |
    | `$filter=denyAssignmentName%20eq%20'{deny-assignment-name}'` | 지정된 이름의 거부 할당을 나열합니다. |

## <a name="list-deny-assignments-at-the-root-scope-"></a>루트 범위(/)에 있는 거부 할당을 나열합니다.

1. [Azure Active Directory의 전역 관리자에 대한 액세스 권한 상승](elevate-access-global-admin.md)에 설명된 대로 액세스 권한을 상승시킵니다.

1. 다음 요청을 사용합니다.

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview&$filter={filter}
    ```

1. *{filter}* 를 거부 할당 목록을 필터링하기 위해 적용하려는 조건으로 바꿉니다. 필터가 필요합니다.

    | Filter | 설명 |
    | --- | --- |
    | `$filter=atScope()` | 루트 범위에 대한 거부 할당만 나열합니다. 하위 범위에 있는 거부 할당을 포함하지 않습니다. |
    | `$filter=denyAssignmentName%20eq%20'{deny-assignment-name}'` | 지정된 이름의 거부 할당을 나열합니다. |

1. 상승된 액세스 권한을 제거합니다.

## <a name="next-steps"></a>다음 단계

- [Azure 리소스에 대한 거부 할당 이해](deny-assignments.md)
- [Azure Active Directory에서 전역 관리자에 대한 액세스 권한 상승](elevate-access-global-admin.md)
- [Azure REST API 참조](/rest/api/azure/)
