---
title: REST API를 사용 하 여 Azure 역할 할당 나열-Azure RBAC
description: REST API 및 Azure RBAC (역할 기반 액세스 제어)를 사용 하 여 사용자, 그룹, 서비스 사용자 또는 관리 id가 액세스할 수 있는 리소스를 확인 하는 방법에 대해 알아봅니다.
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
ms.openlocfilehash: 043c91e5c1ecd86951ac3f9423558a3ab369d506
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/03/2020
ms.locfileid: "82735677"
---
# <a name="list-azure-role-assignments-using-the-rest-api"></a>REST API를 사용 하 여 Azure 역할 할당 나열

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)]이 문서에서는 REST API를 사용 하 여 역할 할당을 나열 하는 방법을 설명 합니다.

> [!NOTE]
> 조직에서 [Azure 위임 된 리소스 관리](../lighthouse/concepts/azure-delegated-resource-management.md)를 사용 하는 서비스 공급자에 대해 아웃소싱 된 관리 기능을 사용 하는 경우 해당 서비스 공급자가 승인한 역할 할당은 여기에 표시 되지 않습니다.

## <a name="list-role-assignments"></a>역할 할당 나열

Azure RBAC에서 액세스를 나열 하려면 역할 할당을 나열 합니다. 역할 할당을 나열하려면 [역할 할당 - 나열](/rest/api/authorization/roleassignments/list) REST API 중 하나를 사용합니다. 결과를 구체화하려면 범위와 선택적 필터를 지정합니다.

1. 다음 요청으로 시작합니다.

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter={filter}
    ```

1. URI 내에서 *{scope}* 를 역할 할당을 나열하려는 범위로 바꿉니다.

    > [!div class="mx-tableFixed"]
    > | 범위 | Type |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | 관리 그룹 |
    > | `subscriptions/{subscriptionId1}` | Subscription |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Resource group |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1` | 리소스 |

    이전 예제에서 microsoft. web은 App Service 인스턴스를 참조 하는 리소스 공급자입니다. 마찬가지로 다른 리소스 공급자를 사용 하 여 범위를 지정할 수 있습니다. 자세한 내용은 [Azure 리소스 공급자 및 형식](../azure-resource-manager/management/resource-providers-and-types.md) 및 지원 되는 [Azure Resource Manager 리소스 공급자 작업](resource-provider-operations.md)을 참조 하세요.  
     
1. *{filter}* 를 역할 할당 목록을 필터링하기 위해 적용하려는 조건으로 바꿉니다.

    > [!div class="mx-tableFixed"]
    > | Assert | 설명 |
    > | --- | --- |
    > | `$filter=atScope()` | 하위 범위에 역할 할당을 포함 하지 않고 지정 된 범위에 대 한 역할 할당을 나열 합니다. |
    > | `$filter=assignedTo('{objectId}')` | 지정 된 사용자 또는 서비스 사용자에 대 한 역할 할당을 나열 합니다.<br/>사용자가 역할 할당을 포함 하는 그룹의 구성원 인 경우 해당 역할 할당도 나열 됩니다. 이 필터는 그룹에 대해 전이적입니다. 즉, 사용자가 그룹의 구성원이 고 해당 그룹이 역할 할당을 포함 하는 다른 그룹의 멤버인 경우 해당 역할 할당도 나열 됩니다.<br/>이 필터는 사용자 또는 서비스 사용자의 개체 ID만 허용 합니다. 그룹의 개체 ID를 전달할 수 없습니다. |
    > | `$filter=atScope()+and+assignedTo('{objectId}')` | 지정 된 사용자 또는 서비스 사용자와 지정 된 범위에 대 한 역할 할당을 나열 합니다. |
    > | `$filter=principalId+eq+'{objectId}'` | 지정 된 사용자, 그룹 또는 서비스 사용자에 대 한 역할 할당을 나열 합니다. |

## <a name="next-steps"></a>다음 단계

- [REST API를 사용 하 여 Azure 역할 할당 추가 또는 제거](role-assignments-rest.md)
- [Azure REST API 참조](/rest/api/azure/)
