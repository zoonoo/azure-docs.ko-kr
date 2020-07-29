---
title: REST API를 사용 하 여 Azure deny 할당 나열-Azure RBAC
description: REST API 및 azure RBAC (역할 기반 액세스 제어)를 사용 하 여 사용자, 그룹 및 응용 프로그램에 대 한 Azure 거부 할당을 나열 하는 방법을 알아봅니다.
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
ms.topic: how-to
ms.date: 03/19/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 2f835c270930734bf9963a7c7c3168b873eddaf6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84791914"
---
# <a name="list-azure-deny-assignments-using-the-rest-api"></a>REST API를 사용 하 여 Azure deny 할당 나열

[Azure 거부 할당](deny-assignments.md) 은 사용자가 역할 할당을 통해 액세스 권한을 부여 하는 경우에도 특정 Azure 리소스 작업을 수행 하지 못하도록 차단 합니다. 이 문서에서는 REST API를 사용 하 여 거부 할당을 나열 하는 방법을 설명 합니다.

> [!NOTE]
> 사용자 고유의 거부 할당을 직접 만들 수는 없습니다. 거부 할당을 만드는 방법에 대 한 자세한 내용은 [Azure 거부 할당](deny-assignments.md)을 참조 하세요.

## <a name="prerequisites"></a>사전 요구 사항

거부 할당에 대 한 정보를 가져오려면 다음이 있어야 합니다.

- `Microsoft.Authorization/denyAssignments/read`사용 권한은 대부분의 [Azure 기본 제공 역할](built-in-roles.md)에 포함 되어 있습니다.

## <a name="list-a-single-deny-assignment"></a>단일 거부 할당 나열

1. 다음 요청으로 시작합니다.

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/denyAssignments/{deny-assignment-id}?api-version=2018-07-01-preview
    ```

1. URI 내에서 *{scope}* 를 거부 할당을 나열하려는 범위로 바꿉니다.

    > [!div class="mx-tableFixed"]
    > | Scope | 형식 |
    > | --- | --- |
    > | `subscriptions/{subscriptionId}` | Subscription |
    > | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Resource group |
    > | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1` | 리소스 |

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

    > [!div class="mx-tableFixed"]
    > | Scope | 형식 |
    > | --- | --- |
    > | `subscriptions/{subscriptionId}` | Subscription |
    > | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Resource group |
    > | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1` | 리소스 |

1. *{filter}* 를 거부 할당 목록을 필터링하기 위해 적용하려는 조건으로 바꿉니다.

    > [!div class="mx-tableFixed"]
    > | Assert | 설명 |
    > | --- | --- |
    > | (필터링 안 함) | 지정 된 범위, 위와 아래의 모든 거부 할당을 나열 합니다. |
    > | `$filter=atScope()` | 지정 된 범위 이상에 대해서만 거부 할당을 나열 합니다. 하위 범위에 있는 거부 할당을 포함하지 않습니다. |
    > | `$filter=assignedTo('{objectId}')` | 지정 된 사용자 또는 서비스 주체에 대 한 거부 할당을 나열 합니다.<br/>사용자가 거부 할당을 포함 하는 그룹의 구성원 인 경우 해당 거부 할당도 나열 됩니다. 이 필터는 그룹에 대해 전이적입니다. 즉, 사용자가 그룹의 구성원이 고 해당 그룹이 거부 할당이 있는 다른 그룹의 멤버인 경우에도 해당 거부 할당이 나열 됩니다.<br/>이 필터는 사용자 또는 서비스 사용자의 개체 ID만 허용 합니다. 그룹의 개체 ID를 전달할 수 없습니다. |
    > | `$filter=atScope()+and+assignedTo('{objectId}')` | 지정 된 사용자 또는 서비스 주체와 지정 된 범위에서 거부 할당을 나열 합니다. |
    > | `$filter=denyAssignmentName+eq+'{deny-assignment-name}'` | 지정 된 이름의 거부 할당을 나열 합니다. |
    > | `$filter=principalId+eq+'{objectId}'` | 지정 된 사용자, 그룹 또는 서비스 사용자에 대 한 거부 할당을 나열 합니다. |

## <a name="list-deny-assignments-at-the-root-scope-"></a>루트 범위(/)에 있는 거부 할당을 나열합니다.

1. [모든 Azure 구독 및 관리 그룹을 관리 하기 위해 액세스 권한 상승](elevate-access-global-admin.md)에 설명 된 대로 액세스 권한을 상승 시킵니다.

1. 다음 요청을 사용합니다.

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview&$filter={filter}
    ```

1. *{filter}* 를 거부 할당 목록을 필터링하기 위해 적용하려는 조건으로 바꿉니다. 필터가 필요합니다.

    > [!div class="mx-tableFixed"]
    > | Assert | 설명 |
    > | --- | --- |
    > | `$filter=atScope()` | 루트 범위에 대한 거부 할당만 나열합니다. 하위 범위에 있는 거부 할당을 포함하지 않습니다. |
    > | `$filter=denyAssignmentName+eq+'{deny-assignment-name}'` | 지정된 이름의 거부 할당을 나열합니다. |

1. 상승된 액세스 권한을 제거합니다.

## <a name="next-steps"></a>다음 단계

- [Azure 거부 할당 이해](deny-assignments.md)
- [모든 Azure 구독 및 관리 그룹을 관리 하기 위해 액세스 권한 상승](elevate-access-global-admin.md)
- [Azure REST API 참조](/rest/api/azure/)
