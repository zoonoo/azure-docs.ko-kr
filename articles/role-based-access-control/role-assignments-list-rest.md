---
title: Azure RBAC 및 REST API를 사용하여 역할 할당 목록
description: 사용자, 그룹, 서비스 주체 또는 관리되는 ID가 AZURE 역할 기반 액세스 제어(RBAC) 및 REST API를 사용하여 액세스할 수 있는 리소스를 확인하는 방법을 알아봅니다.
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
ms.date: 03/19/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: a494e7fd4c9fb79faa6a1d8cb2c3c871796ccdc5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062150"
---
# <a name="list-role-assignments-using-azure-rbac-and-the-rest-api"></a>Azure RBAC 및 REST API를 사용하여 역할 할당 목록

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)]이 문서에서는 REST API를 사용하여 역할 할당을 나열하는 방법을 설명합니다.

> [!NOTE]
> 조직에서 [Azure 위임리소스 관리를](../lighthouse/concepts/azure-delegated-resource-management.md)사용하는 서비스 공급자에게 관리 기능을 아웃소싱한 경우 해당 서비스 공급자가 승인한 역할 할당은 여기에 표시되지 않습니다.

## <a name="list-role-assignments"></a>역할 할당 나열

RBAC에서 역할 할당을 나열하면 액세스 권한이 나열됩니다. 역할 할당을 나열하려면 [역할 할당 - 나열](/rest/api/authorization/roleassignments/list) REST API 중 하나를 사용합니다. 결과를 구체화하려면 범위와 선택적 필터를 지정합니다.

1. 다음 요청으로 시작합니다.

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter={filter}
    ```

1. URI 내에서 *{scope}* 를 역할 할당을 나열하려는 범위로 바꿉니다.

    > [!div class="mx-tableFixed"]
    > | Scope | Type |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | 관리 그룹 |
    > | `subscriptions/{subscriptionId1}` | Subscription |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Resource group |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1` | 리소스 |

    이전 예제에서 microsoft.web은 앱 서비스 인스턴스를 참조하는 리소스 공급자입니다. 마찬가지로 다른 리소스 공급자를 사용하고 범위를 지정할 수 있습니다. 자세한 내용은 [Azure 리소스 공급자 및 유형 및](../azure-resource-manager/management/resource-providers-and-types.md) 지원되는 Azure 리소스 관리자 리소스 공급자 [작업을](resource-provider-operations.md)참조하십시오.  
     
1. *{filter}* 를 역할 할당 목록을 필터링하기 위해 적용하려는 조건으로 바꿉니다.

    > [!div class="mx-tableFixed"]
    > | Assert | 설명 |
    > | --- | --- |
    > | `$filter=atScope()` | 하위 범위의 역할 할당을 포함하지 않고 지정된 범위에 대해서만 역할 할당을 나열합니다. |
    > | `$filter=assignedTo('{objectId}')` | 지정된 사용자 또는 서비스 주체에 대한 역할 할당을 나열합니다.<br/>사용자가 역할 할당이 있는 그룹의 구성원인 경우 해당 역할 할당도 나열됩니다. 이 필터는 사용자가 그룹의 구성원이고 해당 그룹이 역할 할당이 있는 다른 그룹의 구성원인 경우 해당 역할 할당도 나열되는 그룹에 대해 전이적입니다.<br/>이 필터는 사용자 또는 서비스 주체에 대한 개체 ID만 허용합니다. 그룹에 대한 개체 ID를 전달할 수 없습니다. |
    > | `$filter=atScope()+and+assignedTo('{objectId}')` | 지정된 사용자 또는 서비스 주체및 지정된 범위에 대한 역할 할당을 나열합니다. |
    > | `$filter=principalId+eq+'{objectId}'` | 지정된 사용자, 그룹 또는 서비스 주체에 대한 역할 할당을 나열합니다. |

## <a name="next-steps"></a>다음 단계

- [Azure RBAC 및 REST API를 사용하여 역할 할당 추가 또는 제거](role-assignments-rest.md)
- [Azure REST API 참조](/rest/api/azure/)
