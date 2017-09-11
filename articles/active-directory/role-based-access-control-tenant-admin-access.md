---
title: "테넌트 관리자 권한 상승 액세스 - Azure AD | Microsoft Docs"
description: "이 항목에서는 역할 기반 액세스 제어(RBAC)에 대한 기본 제공 역할에 대해 설명합니다."
services: active-directory
documentationcenter: 
author: andredm7
manager: femila
editor: rqureshi
ms.assetid: b547c5a5-2da2-4372-9938-481cb962d2d6
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2017
ms.author: andredm
ms.translationtype: Human Translation
ms.sourcegitcommit: c308183ffe6a01f4d4bf6f5817945629cbcedc92
ms.openlocfilehash: 0fa44799a0bd49d3d96a1916f32e6452405abce8
ms.contentlocale: ko-kr
ms.lasthandoff: 05/17/2017

---
# <a name="elevate-access-as-a-tenant-admin-with-role-based-access-control"></a>역할 기반 액세스 제어를 사용하여 테넌트 관리자로 액세스 권한 상승

역할 기반 액세스 제어를 통해 테넌트 관리자가 액세스에서 임시 권한이 상승되어 평소보다 높은 수준의 사용 권한을 부여받을 수 있습니다. 테넌트 관리자는 필요할 때 사용자 액세스 관리자 역할로 스스로의 권한을 높일 수 있습니다. 해당 역할은 테넌트 관리자 사용 권한을 주어 "/" 범위에서 자신 또는 다른 사용자에게 역할을 부여합니다.

이 기능은 테넌트 관리자가 조직에 있는 모든 구독을 확인할 수 있도록 하기 때문에 중요합니다. 또한 자동화 앱(예: 송장 발행 및 감사)에서 모든 구독에 액세스하여 조직의 대금 청구 또는 자산 관리 상태를 정확하게 볼 수 있게 합니다.  

## <a name="how-to-use-elevateaccess-to-give-tenant-access"></a>elevateAccess를 사용하여 테넌트 액세스 권한을 부여하는 방법

기본 프로세스는 다음 단계로 작동합니다.

1. REST를 사용하여 *elevateAccess*를 호출하면 "/" 범위에서 사용자 액세스 관리자 역할 권한을 부여합니다.

    ```
    POST https://management.azure.com/providers/Microsoft.Authorization/elevateAccess?api-version=2016-07-01
    ```

2. 모든 범위의 모든 역할을 할당하는 [역할 할당](/rest/api/authorization/roleassignments)을 만듭니다. 다음 예제에서는 "/" 범위에서 읽기 권한자 역할을 할당하는 속성을 보여 줍니다.

    ```
    { "properties":{
    "roleDefinitionId": "providers/Microsoft.Authorization/roleDefinitions/acdd72a7338548efbd42f606fba81ae7",
    "principalId": "cbc5e050-d7cd-4310-813b-4870be8ef5bb",
    "scope": "/"
    },
    "id": "providers/Microsoft.Authorization/roleAssignments/64736CA0-56D7-4A94-A551-973C2FE7888B",
    "type": "Microsoft.Authorization/roleAssignments",
    "name": "64736CA0-56D7-4A94-A551-973C2FE7888B"
    }
    ```

3. 사용자 액세스 관리자인 경우 "/" 범위에서 역할 할당을 삭제할 수 있습니다.

4. 다시 필요할 때까지 사용자 액세스 관리자 권한을 취소합니다.


## <a name="how-to-undo-the-elevateaccess-action"></a>elevateAccess 작업을 취소하는 방법

*elevateAccess*를 호출하는 경우 해당 권한을 취소하려면 할당을 삭제해야 하므로 스스로 역할 할당을 만듭니다.

1.  roleName = 사용자 액세스 관리자인 [GET roleDefinitions](/rest/api/authorization/roledefinitions#RoleDefinitions_Get)을 호출하여 사용자 액세스 관리자 역할의 GUID 이름을 확인합니다. 응답은 다음과 같아야 합니다.

    ```
    {"value":[{"properties":{
    "roleName":"User Access Administrator",
    "type":"BuiltInRole",
    "description":"Lets you manage user access to Azure resources.",
    "assignableScopes":["/"],
    "permissions":[{"actions":["*/read","Microsoft.Authorization/*","Microsoft.Support/*"],"notActions":[]}],
    "createdOn":"0001-01-01T08:00:00.0000000Z",
    "updatedOn":"2016-05-31T23:14:04.6964687Z",
    "createdBy":null,
    "updatedBy":null},
    "id":"/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
    "type":"Microsoft.Authorization/roleDefinitions",
    "name":"18d7d88d-d35e-4fb5-a5c3-7773c20a72d9"}],
    "nextLink":null}
    ```

    이 경우에 **18d7d88d-d35e-4fb5-a5c3-7773c20a72d9**인 *name* 매개 변수에서 GUID를 저장합니다.

2. principalId = 고유한 ObjectId인 [GET roleAssignments](/rest/api/authorization/roleassignments#RoleAssignments_Get)를 호출합니다. 그러면 테넌트의 모든 할당을 나열합니다. 범위가 "/"이고 RoleDefinitionId가 1단계에서 찾은 GUID 역할 이름으로 끝나는 할당을 찾아봅니다. 역할 할당은 다음과 같야아 합니다.

    ```
    {"value":[{"properties":{
    "roleDefinitionId":"/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
    "principalId":"{objectID}",
    "scope":"/",
    "createdOn":"2016-08-17T19:21:16.3422480Z",
    "updatedOn":"2016-08-17T19:21:16.3422480Z",
    "createdBy":"93ce6722-3638-4222-b582-78b75c5c6d65",
    "updatedBy":"93ce6722-3638-4222-b582-78b75c5c6d65"},
    "id":"/providers/Microsoft.Authorization/roleAssignments/e7dd75bc-06f6-4e71-9014-ee96a929d099",
    "type":"Microsoft.Authorization/roleAssignments",
    "name":"e7dd75bc-06f6-4e71-9014-ee96a929d099"}],
    "nextLink":null}
    ```

    이 경우에 **e7dd75bc-06f6-4e71-9014-ee96a929d099**인 *name* 매개 변수에서 GUID를 다시 저장합니다.

3. 마지막으로 roleAssignmentId = 2단계에서 찾은 GUID 이름인 [DELETE roleAssignments](/rest/api/authorization/roleassignments#RoleAssignments_DeleteById)를 호출합니다.

## <a name="next-steps"></a>다음 단계

- [REST를 사용하여 역할 기반 액세스 제어를 관리](role-based-access-control-manage-access-rest.md)하는 방법에 대해 알아보기

- Azure Portal에서 [액세스 할당 관리](role-based-access-control-manage-assignments.md)

