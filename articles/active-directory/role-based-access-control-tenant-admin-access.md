---
title: "테넌트 관리자 권한 상승 액세스 - Azure AD | Microsoft Docs"
description: "이 항목에서는 역할 기반 액세스 제어(RBAC)에 대한 기본 제공 역할에 대해 설명합니다."
services: active-directory
documentationcenter: 
author: rolyon
manager: mtillman
editor: rqureshi
ms.assetid: b547c5a5-2da2-4372-9938-481cb962d2d6
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/30/2017
ms.author: rolyon
ms.openlocfilehash: dff3a26201507f974d52de3fe6dcb23945cd900f
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/14/2018
---
# <a name="elevate-access-as-a-tenant-admin-with-role-based-access-control"></a>역할 기반 액세스 제어를 사용하여 테넌트 관리자로 액세스 권한 상승

역할 기반 Access Control을 통해 테넌트 관리자가 액세스에서 임시 권한이 상승되어 평소보다 높은 수준의 사용 권한을 부여받을 수 있습니다. 테넌트 관리자는 필요할 때 사용자 액세스 관리자 역할로 스스로의 권한을 높일 수 있습니다. 해당 역할은 테넌트 관리자 사용 권한을 주어 "/" 범위에서 자신 또는 다른 사용자에게 역할을 부여합니다.

이 기능은 테넌트 관리자가 조직에 있는 모든 구독을 확인할 수 있도록 하기 때문에 중요합니다. 또한 송장 발행 및 감사와 같은 자동화 앱에서 모든 구독에 액세스하여 조직의 대금 청구 또는 자산 관리 상태를 정확하게 볼 수 있게 합니다.  

## <a name="use-elevateaccess-for-tenant-access-with-azure-ad-admin-center"></a>Azure AD 관리 센터를 사용하여 테넌트 액세스 권한을 위해 elevateAccess 사용

1. [Azure Active Directory 관리 센터](https://aad.portal.azure.com)로 이동한 다음 자격 증명을 사용하여 로그인합니다.

2. Azure AD 왼쪽 메뉴에서 **속성**을 선택합니다.

3. **전역 관리자는 Azure 구독을 관리할 수 있습니다.**를 찾고 **예**를 선택한 다음 **저장**을 선택합니다.
    > [!IMPORTANT] 
    > **예**를 선택하면 루트 "/"(루트 범위)에서 현재 포털에 로그인한 사용자에 대해 **사용자 액세스 관리자** 역할을 할당합니다. **이렇게 하면 사용자가 다른 모든 Azure 구독을 볼 수 있습니다.**
    
    > [!NOTE] 
    > **아니요**를 선택하면 루트 "/"(루트 범위)에서 현재 포털에 로그인한 사용자에 대해 **사용자 액세스 관리자** 역할을 제거합니다.

> [!TIP] 
> 이 기능이 Azure Active Directory의 전역 속성인 것처럼 보이지만 현재 로그온한 사용자에 대해 사용자 기준으로 작동합니다. Azure Active Directory에서 전역 관리자 권한이 있는 경우 Azure Active Directory 관리 센터를 현재 로그인한 사용자에게 elevateAccess 기능을 호출할 수 있습니다.

![Azure AD 관리 센터 - 속성 - Globaladmin은 Azure 구독을 관리할 수 있습니다. - 스크린샷](./media/role-based-access-control-tenant-admin-access/aad-azure-portal-global-admin-can-manage-azure-subscriptions.png)

## <a name="view-role-assignments-at-the--scope-using-powershell"></a>PowerShell을 사용하여 "/" 범위에서 역할 할당 보기
**/** 범위에서 **사용자 액세스 관리자** 할당을 보려면 `Get-AzureRmRoleAssignment` PowerShell cmdlet를 사용합니다.
    
```powershell
Get-AzureRmRoleAssignment* | where {$_.RoleDefinitionName -eq "User Access Administrator" -and $_SignInName -eq "<username@somedomain.com>" -and $_.Scope -eq "/"}
```

**예제 출력**:
```
RoleAssignmentId   : /providers/Microsoft.Authorization/roleAssignments/098d572e-c1e5-43ee-84ce-8dc459c7e1f0    
Scope              : /    
DisplayName        : username    
SignInName         : username@somedomain.com    
RoleDefinitionName : User Access Administrator    
RoleDefinitionId   : 18d7d88d-d35e-4fb5-a5c3-7773c20a72d9    
ObjectId           : d65fd0e9-c185-472c-8f26-1dafa01f72cc    
ObjectType         : User    
```

## <a name="delete-the-role-assignment-at--scope-using-powershell"></a>PowerShell을 사용하여 "/" 범위에서 역할 할당 삭제:
다음 PowerShell cmdlet를 사용하여 할당을 삭제할 수 있습니다.

```powershell
Remove-AzureRmRoleAssignment -SignInName <username@somedomain.com> -RoleDefinitionName "User Access Administrator" -Scope "/" 
```

## <a name="use-elevateaccess-to-give-tenant-access-with-the-rest-api"></a>REST API를 사용하여 테넌트 액세스 권한을 부여하기 위해 elevateAccess 사용

기본 프로세스는 다음 단계로 작동합니다.

1. REST를 사용하여 *elevateAccess*를 호출하면 "/" 범위에서 사용자 액세스 관리자 역할 권한을 부여합니다.

    ```
    POST https://management.azure.com/providers/Microsoft.Authorization/elevateAccess?api-version=2016-07-01
    ```

2. 모든 범위의 모든 역할을 할당하는 [역할 할당](/rest/api/authorization/roleassignments)을 만듭니다. 다음 예제에서는 "/" 범위에서 읽기 권한자 역할을 할당하는 속성을 보여 줍니다.

    ```json
    { 
      "properties": {
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


## <a name="how-to-undo-the-elevateaccess-action-with-the-rest-api"></a>REST API로 elevateAccess 작업을 취소하는 방법

*elevateAccess*를 호출하는 경우 해당 권한을 취소하려면 할당을 삭제해야 하므로 스스로 역할 할당을 만듭니다.

1.  roleName = 사용자 액세스 관리자인 GET roleDefinitions를 호출하여 사용자 액세스 관리자 역할의 GUID 이름을 확인합니다.
    ```
    GET https://management.azure.com/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter=roleName+eq+'User+Access+Administrator
    ```

    ```json
    {
      "value": [
        {
          "properties": {
        "roleName": "User Access Administrator",
        "type": "BuiltInRole",
        "description": "Lets you manage user access to Azure resources.",
        "assignableScopes": [
          "/"
        ],
        "permissions": [
          {
            "actions": [
              "*/read",
              "Microsoft.Authorization/*",
              "Microsoft.Support/*"
            ],
            "notActions": []
          }
        ],
        "createdOn": "0001-01-01T08:00:00.0000000Z",
        "updatedOn": "2016-05-31T23:14:04.6964687Z",
        "createdBy": null,
        "updatedBy": null
          },
          "id": "/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
          "type": "Microsoft.Authorization/roleDefinitions",
          "name": "18d7d88d-d35e-4fb5-a5c3-7773c20a72d9"
        }
      ],
      "nextLink": null
    }
    ```

    이 경우에 **18d7d88d-d35e-4fb5-a5c3-7773c20a72d9**인 *name* 매개 변수에서 GUID를 저장합니다.

2. 또한 테넌트 범위에서 테넌트 관리자의 역할 할당을 나열해야 합니다. 액세스 권한 상승 호출을 수행한 TenantAdmin의 PrincipalId에 대한 테넌트 범위의 모든 할당을 나열합니다. 그러면 ObjectID에 대한 테넌트의 모든 할당이 나열됩니다.

    ```
    GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=principalId+eq+'{objectid}'
    ```
    
    >[!NOTE] 
    >테넌트 관리자는 할당이 많지 않아야 합니다. 이전 쿼리에서 너무 많은 할당을 반환하는 경우, 테넌트 범위 수준의 모든 할당을 쿼리한 다음 결과를 필터링할 수도 있습니다. `GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=atScope()`
    
        
    2. 이전 호출은 역할 할당 목록을 반환합니다. 범위가 "/"이고 RoleDefinitionId가 1단계에서 찾은 역할 이름 GUID로 끝나고 PrincipalId가 테넌트 관리자의 ObjectId와 일치하는 역할 할당을 찾습니다. 
    
    샘플 역할 할당:

        ```json
        {
          "value": [
            {
              "properties": {
                "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
                "principalId": "{objectID}",
                "scope": "/",
                "createdOn": "2016-08-17T19:21:16.3422480Z",
                "updatedOn": "2016-08-17T19:21:16.3422480Z",
                "createdBy": "93ce6722-3638-4222-b582-78b75c5c6d65",
                "updatedBy": "93ce6722-3638-4222-b582-78b75c5c6d65"
              },
              "id": "/providers/Microsoft.Authorization/roleAssignments/e7dd75bc-06f6-4e71-9014-ee96a929d099",
              "type": "Microsoft.Authorization/roleAssignments",
              "name": "e7dd75bc-06f6-4e71-9014-ee96a929d099"
            }
          ],
          "nextLink": null
        }
        ```
        
    이 경우에 **e7dd75bc-06f6-4e71-9014-ee96a929d099**인 *name* 매개 변수에서 GUID를 다시 저장합니다.

    3. 마지막으로 강조 표시된 **RoleAssignment ID**를 사용하여 액세스 권한 상승에 의해 추가된 할당을 삭제합니다.

    ```
    DELETE https://management.azure.com/providers/Microsoft.Authorization/roleAssignments/e7dd75bc-06f6-4e71-9014-ee96a929d099?api-version=2015-07-01
    ```

## <a name="next-steps"></a>다음 단계

- [REST를 사용하여 역할 기반 Access Control을 관리](role-based-access-control-manage-access-rest.md)하는 방법에 대해 알아보기

- Azure Portal에서 [액세스 할당 관리](role-based-access-control-manage-assignments.md)
