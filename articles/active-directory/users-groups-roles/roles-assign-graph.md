---
title: Microsoft Graph API를 사용하여 Azure AD 관리자 역할 할당 | Microsoft Docs
description: Azure Active Directory에서 Graph API를 사용하여 Azure AD 관리자 역할 할당 및 제거
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 04/29/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7ac6f06a936893f26c634951e50a3579401c15d5
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2020
ms.locfileid: "83869613"
---
# <a name="assign-custom-admin-roles-using-the-microsoft-graph-api-in-azure-active-directory"></a>Azure Active Directory에서 Microsoft Graph API를 사용하여 사용자 지정 관리자 역할 할당 

Microsoft Graph API를 사용하여 사용자 계정에 역할을 할당하는 방법을 자동화할 수 있습니다. 이 문서에서는 roleAssignments에 대한 POST, GET 및 DELETE 작업에 대해 설명합니다.

## <a name="required-permissions"></a>필요한 사용 권한

전역 관리자 계정 또는 권한 있는 ID 관리자를 사용하여 Azure AD 조직에 연결하여 역할을 할당하거나 제거합니다.

## <a name="post-operations-on-roleassignment"></a>RoleAssignment에 대한 POST 작업

사용자와 역할 정의 간에 역할 할당을 만들기 위한 HTTP 요청입니다.

POST

``` HTTP
POST https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
Content-type: application/json
```

본문

``` HTTP
{
    "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
    "roleDefinitionId":"194ae4cb-b126-40b2-bd5b-6091b380977d",
    "resourceScopes":"/"
}
```

응답

``` HTTP
HTTP/1.1 201 Created
```

보안 주체 또는 역할 정의가 없는 역할 할당을 만들기 위한 HTTP 요청

POST

``` HTTP
https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
```

본문

``` HTTP
{
    "principalId":" 2142743c-a5b3-4983-8486-4532ccba12869",
    "roleDefinitionId":"194ae4cb-b126-40b2-bd5b-6091b380977d",
    "resourceScopes":"/"
}
```

응답

``` HTTP
HTTP/1.1 404 Not Found
```

기본 제공 역할 정의에 대한 단일 리소스 범위 역할 할당을 만들기 위한 HTTP 요청입니다.

> [!NOTE] 
> 현재 기본 제공 역할에는 “/” 조직 전체 범위 또는 “/AU/*” 범위로만 범위를 지정할 수 있는 제한 사항이 있습니다. 단일 리소스 범위 지정은 기본 제공 역할에서는 작동하지 않지만 사용자 지정 역할에서는 작동합니다.

POST

``` HTTP
https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
```

본문

``` HTTP
{
    "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
    "roleDefinitionId":"194ae4cb-b126-40b2-bd5b-6091b380977d",
    "resourceScopes":"/ab2e1023-bddc-4038-9ac1-ad4843e7e539"
}
```

응답

``` HTTP
HTTP/1.1 400 Bad Request
{
    "odata.error":
    {
        "code":"Request_BadRequest",
        "message":
        {
            "lang":"en",
            "value":"Provided authorization scope is not supported for built-in role definitions."},
            "values":
            [
                {
                    "item":"scope",
                    "value":"/ab2e1023-bddc-4038-9ac1-ad4843e7e539"
                }
            ]
        }
    }
}
```

## <a name="get-operations-on-roleassignment"></a>RoleAssignment에 대한 GET 작업

지정된 보안 주체에 대한 역할 할당을 가져오기 위한 HTTP 요청

GET

``` HTTP
https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments&$filter=principalId eq ‘<object-id-of-principal>’
```

응답

``` HTTP
HTTP/1.1 200 OK
{ 
    "id":"mhxJMipY4UanIzy2yE-r7JIiSDKQoTVJrLE9etXyrY0-1"
    "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
    "roleDefinitionId":"10dae51f-b6af-4016-8d66-8c2a99b929b3",
    "resourceScopes":"/"
} ,
{
    "id":"CtRxNqwabEKgwaOCHr2CGJIiSDKQoTVJrLE9etXyrY0-1"
    "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
    "roleDefinitionId":"3671d40a-1aac-426c-a0c1-a3821ebd8218",
    "resourceScopes":"/"
}
```

지정된 역할 정의에 대한 역할 할당을 가져오기 위한 HTTP 요청입니다.

GET

``` HTTP
https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments&$filter=roleDefinitionId eq ‘<object-id-or-template-id-of-role-definition>’
```

응답

``` HTTP
HTTP/1.1 200 OK
{
    "id":"CtRxNqwabEKgwaOCHr2CGJIiSDKQoTVJrLE9etXyrY0-1"
    "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
    "roleDefinitionId":"3671d40a-1aac-426c-a0c1-a3821ebd8218",
    "resourceScopes":"/"
}
```

ID별 역할 할당을 가져오기 위한 HTTP 요청입니다.

GET

``` HTTP
GET https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments/lAPpYvVpN0KRkAEhdxReEJC2sEqbR_9Hr48lds9SGHI-1
```

응답

``` HTTP
HTTP/1.1 200 OK
{ 
    "id":"mhxJMipY4UanIzy2yE-r7JIiSDKQoTVJrLE9etXyrY0-1",
    "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
    "roleDefinitionId":"10dae51f-b6af-4016-8d66-8c2a99b929b3",
    "resourceScopes":"/"
}
```

## <a name="delete-operations-on-roleassignment"></a>RoleAssignment에 대한 DELETE 작업

사용자와 역할 정의 간에 역할 할당을 삭제하기 위한 HTTP 요청입니다.

Delete

``` HTTP
GET https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments/lAPpYvVpN0KRkAEhdxReEJC2sEqbR_9Hr48lds9SGHI-1
```

응답
``` HTTP
HTTP/1.1 204 No Content
```

더 이상 존재하지 않는 역할 할당을 삭제하기 위한 HTTP 요청

Delete

``` HTTP
GET https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments/lAPpYvVpN0KRkAEhdxReEJC2sEqbR_9Hr48lds9SGHI-1
```

응답

``` HTTP
HTTP/1.1 404 Not Found
```

자체 역할 정의와 기본 제공 역할 정의 간에 역할 할당을 삭제하기 위한 HTTP 요청

Delete

``` HTTP
GET https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments/lAPpYvVpN0KRkAEhdxReEJC2sEqbR_9Hr48lds9SGHI-1
```

응답

``` HTTP
HTTP/1.1 400 Bad Request
{
    "odata.error":
    {
        "code":"Request_BadRequest",
        "message":
        {
            "lang":"en",
            "value":"Cannot remove self from built-in role definitions."},
            "values":null
        }
    }
}
```

## <a name="next-steps"></a>다음 단계

* [Azure AD 관리 역할 포럼](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)에서 경험을 자유롭게 공유하세요.
* 역할 및 관리자 역할 할당에 대한 자세한 내용은 [관리자 역할 할당](directory-assign-admin-roles.md)을 참조하세요.
* 기본 사용자 권한의 경우 [기본 게스트 및 멤버 사용자 권한 비교](../fundamentals/users-default-permissions.md)를 참조하세요.
