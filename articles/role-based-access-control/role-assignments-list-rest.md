---
title: Azure RBAC 및 REST API를 사용 하 여 역할 할당 나열
description: Azure RBAC (역할 기반 액세스 제어) 및 REST API를 사용 하 여 사용자, 그룹, 서비스 주체 또는 관리 id가 액세스할 수 있는 리소스를 확인 하는 방법에 대해 알아봅니다.
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
ms.date: 11/25/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: e20edcb5e2406c216711a2e0f696ef06e19fe21e
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74710400"
---
# <a name="list-role-assignments-using-azure-rbac-and-the-rest-api"></a>Azure RBAC 및 REST API를 사용 하 여 역할 할당 나열

이 문서 [!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)] REST API를 사용 하 여 역할 할당을 나열 하는 방법을 설명 합니다.

## <a name="list-role-assignments"></a>역할 할당 나열

RBAC에서 역할 할당을 나열하면 액세스 권한이 나열됩니다. 역할 할당을 나열하려면 [역할 할당 - 나열](/rest/api/authorization/roleassignments/list) REST API 중 하나를 사용합니다. 결과를 구체화하려면 범위와 선택적 필터를 지정합니다.

1. 다음 요청으로 시작합니다.

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter={filter}
    ```

1. URI 내에서 *{scope}* 를 역할 할당을 나열하려는 범위로 바꿉니다.

    | 범위 | Type |
    | --- | --- |
    | `providers/Microsoft.Management/managementGroups/{groupId1}` | 관리 그룹 |
    | `subscriptions/{subscriptionId1}` | Subscription |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Resource group |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | 리소스 |

    이전 예제에서 microsoft. web은 App Service 인스턴스를 참조 하는 리소스 공급자입니다. 마찬가지로 다른 리소스 공급자를 사용 하 여 범위를 지정할 수 있습니다. 자세한 내용은 [Azure 리소스 공급자 및 형식](../azure-resource-manager/resource-manager-supported-services.md) 및 지원 되는 [Azure Resource Manager 리소스 공급자 작업](resource-provider-operations.md)을 참조 하세요.  
     
1. *{filter}* 를 역할 할당 목록을 필터링하기 위해 적용하려는 조건으로 바꿉니다.

    | 필터링 | 설명 |
    | --- | --- |
    | `$filter=atScope()` | 하위 범위에 역할 할당을 포함 하지 않고 지정 된 범위에 대 한 역할 할당을 나열 합니다. |
    | `$filter=principalId%20eq%20'{objectId}'` | 지정 된 사용자, 그룹 또는 서비스 사용자에 대 한 역할 할당을 나열 합니다. |
    | `$filter=assignedTo('{objectId}')` | 지정 된 사용자 또는 서비스 사용자에 대 한 역할 할당을 나열 합니다. 사용자가 역할 할당을 포함 하는 그룹의 구성원 인 경우 해당 역할 할당도 나열 됩니다. 이 필터는 그룹에 대해 전이적입니다. 즉, 사용자가 그룹의 구성원이 고 해당 그룹이 역할 할당을 포함 하는 다른 그룹의 멤버인 경우 해당 역할 할당도 나열 됩니다. 이 필터는 사용자 또는 서비스 사용자의 개체 ID만 허용 합니다. 그룹의 개체 ID를 전달할 수 없습니다. |

## <a name="next-steps"></a>다음 단계

- [Azure RBAC 및 REST API를 사용 하 여 역할 할당 추가 또는 제거](role-assignments-rest.md)
- [Azure REST API 참조](/rest/api/azure/)
