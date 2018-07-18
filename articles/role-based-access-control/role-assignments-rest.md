---
title: RBAC 및 REST API를 사용하여 액세스 관리 | Microsoft Docs
description: RBAC(역할 기반 액세스 제어) 및 REST API를 사용하여 사용자, 그룹 및 응용 프로그램의 액세스 권한을 관리하는 방법을 알아봅니다. 여기에는 액세스 권한을 나열, 부여 및 제거하는 방법이 포함됩니다.
services: active-directory
documentationcenter: na
author: rolyon
manager: mtillman
editor: ''
ms.assetid: 1f90228a-7aac-4ea7-ad82-b57d222ab128
ms.service: role-based-access-control
ms.workload: multiple
ms.tgt_pltfrm: rest-api
ms.devlang: na
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 859a410a4ff9204e8e52fbd2cc3b38823f4bb830
ms.sourcegitcommit: e0834ad0bad38f4fb007053a472bde918d69f6cb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/03/2018
ms.locfileid: "37435221"
---
# <a name="manage-access-using-rbac-and-the-rest-api"></a>RBAC 및 REST API를 사용하여 액세스 관리

[RBAC(역할 기반 액세스 제어)](overview.md)는 Azure에서 리소스에 대한 액세스를 관리하는 방법입니다. 이 문서에서는 RBAC 및 REST API를 사용하여 사용자, 그룹 및 응용 프로그램의 액세스 권한을 관리하는 방법을 설명합니다.

## <a name="list-access"></a>액세스 권한 나열

RBAC에서 역할 할당을 나열하면 액세스 권한이 나열됩니다. 역할 할당을 나열하려면 [역할 할당 - 나열](/rest/api/authorization/roleassignments/list) REST API 중 하나를 사용합니다. 결과를 구체화하려면 범위와 선택적 필터를 지정합니다. API를 호출하려면 지정된 범위에서 `Microsoft.Authorization/roleAssignments/read` 작업에 액세스할 수 있어야 합니다. 여러 [기본 제공 역할](built-in-roles.md)에는 이 작업에 대한 액세스 권한이 부여되어 있습니다.

1. 다음 요청으로 시작합니다.

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter={filter}
    ```

1. URI 내에서 *{scope}* 를 역할 할당을 나열하려는 범위로 바꿉니다.

    | 범위 | type |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | 구독 |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | 리소스 그룹 |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | 리소스 |

1. *{filter}* 를 역할 할당 목록을 필터링하기 위해 적용하려는 조건으로 바꿉니다.

    | Filter | 설명 |
    | --- | --- |
    | `$filter=atScope()` | 하위 범위의 역할 할당은 제외하고 지정된 범위에 대한 역할 할당만 나열합니다. |
    | `$filter=principalId%20eq%20'{objectId}'` | 지정된 사용자, 그룹 또는 서비스 주체에 대한 역할 할당을 나열합니다. |
    | `$filter=assignedTo('{objectId}')` | 그룹에서 상속된 역할 할당을 포함하여 지정된 사용자에 대한 역할 할당을 나열합니다. |

## <a name="grant-access"></a>액세스 권한 부여

RBAC에서 액세스 권한을 부여하기 위해 역할 할당을 만듭니다. 역할 할당을 만들려면 [역할 할당 - 만들기](/rest/api/authorization/roleassignments/create) REST API를 사용하고 보안 주체, 역할 정의 및 범위를 지정합니다. 이 API를 호출하려면 `Microsoft.Authorization/roleAssignments/write` 작업에 액세스할 수 있어야 합니다. 기본 제공 역할의 경우 [소유자](built-in-roles.md#owner) 및 [사용자 액세스 관리자](built-in-roles.md#user-access-administrator)에게만 이러한 작업의 권한이 부여됩니다.

1. 할당하려는 역할 정의에 대한 식별자를 가져오려면 [역할 정의 - 나열](/rest/api/authorization/roledefinitions/list) REST API를 사용하거나 [기본 제공 역할](built-in-roles.md)을 참조하세요.

1. GUID 도구를 사용하여 역할 할당 식별자에 사용할 고유 식별자를 생성합니다. 식별자의 형식은 `00000000-0000-0000-0000-000000000000`입니다.

1. 다음 요청 및 본문으로 시작합니다.

    ```http
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentName}?api-version=2015-07-01
    ```

    ```json
    {
      "properties": {
        "roleDefinitionId": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}",
        "principalId": "{principalId}"
      }
    }
    ```
    
1. URI 내에서 *{scope}* 를 역할 할당에 대한 범위로 바꿉니다.

    | 범위 | type |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | 구독 |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | 리소스 그룹 |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | 리소스 |

1. *{roleAssignmentName}* 을 역할 할당의 GUID 식별자로 바꿉니다.

1. 요청 본문 내에서 *{subscriptionId}* 를 구독 식별자로 바꿉니다.

1. *{roleDefinitionId}* 를 역할 정의 식별자로 바꿉니다.

1. *{principalId}* 를 역할이 할당될 사용자, 그룹 또는 서비스 주체의 개체 식별자로 바꿉니다.

## <a name="remove-access"></a>액세스 권한 제거

RBAC에서 액세스 권한을 제거하려면 역할 할당을 제거해야 합니다. 역할 할당을 제거하려면 [역할 할당 - 삭제](/rest/api/authorization/roleassignments/delete) REST API를 사용합니다. 이 API를 호출하려면 `Microsoft.Authorization/roleAssignments/delete` 작업에 액세스할 수 있어야 합니다. 기본 제공 역할의 경우 [소유자](built-in-roles.md#owner) 및 [사용자 액세스 관리자](built-in-roles.md#user-access-administrator)에게만 이러한 작업의 권한이 부여됩니다.

1. 역할 할당 식별자(GUID)를 가져옵니다. 이 식별자는 역할 할당을 처음 만들 때 반환되거나 역할 할당을 나열하여 가져올 수 있습니다.

1. 다음 요청으로 시작합니다.

    ```http
    DELETE https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentName}?api-version=2015-07-01
    ```

1. URI 내에서 *{scope}* 를 제거할 역할 할당에 대한 범위로 바꿉니다.

    | 범위 | type |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | 구독 |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | 리소스 그룹 |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | 리소스 |

1. *{roleAssignmentName}* 을 역할 할당의 GUID 식별자로 바꿉니다.

## <a name="next-steps"></a>다음 단계

- [Resource Manager 템플릿과 Resource Manager REST API로 리소스 배포](../azure-resource-manager/resource-group-template-deploy-rest.md)
- [Azure REST API 참조](/rest/api/azure/)
- [REST API를 사용하여 사용자 지정 역할 만들기](custom-roles-rest.md)