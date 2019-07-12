---
title: 사용자 지정 리소스 프록시 참조
description: Azure 사용자 지정 리소스 공급자에 대 한 사용자 지정 리소스 프록시 참조 합니다. 이 문서에서는 사용자 지정 리소스 프록시를 구현 하는 끝점에 대 한 요구 사항을 살펴봅니다.
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: fe470165d11b24ffb1df704ecaa9804663fc9e5c
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67796025"
---
# <a name="custom-resource-proxy-reference"></a>사용자 지정 리소스 프록시 참조

이 문서에서는 사용자 지정 리소스 프록시를 구현 하는 끝점에 대 한 요구 사항을 살펴봅니다. Azure 사용자 지정 리소스 공급자를 사용 하 여 잘 모르는 경우 [사용자 지정 리소스 공급자에 대 한 개요](./custom-providers-overview.md)합니다.

## <a name="how-to-define-a-proxy-resource-endpoint"></a>프록시 리소스 끝점을 정의 하는 방법

프록시 리소스를 지정 하 여 만들 수 있습니다 합니다 **routingType** "프록시"입니다.

샘플 사용자 지정 리소스 공급자:

```JSON
{
  "properties": {
    "resourceTypes": [
      {
        "name": "myCustomResources",
        "routingType": "Proxy",
        "endpoint": "https://{endpointURL}/"
      }
    ]
  },
  "location": "eastus",
  "type": "Microsoft.CustomProviders/resourceProviders",
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}",
  "name": "{resourceProviderName}"
}
```

## <a name="building-proxy-resource-endpoint"></a>구성 프록시 리소스 끝점

**끝점** "프록시" 리소스를 구현 하는 **끝점** Azure에서 새 API에 대 한 요청 및 응답을 처리 해야 합니다. 이 경우는 **resourceType** 에 대 한 새 Azure 리소스 API 생성 됩니다 `PUT`, `GET`, 및 `DELETE` 단일 리소스에서 CRUD를 수행할 수 뿐만 `GET` 모든 기존 리소스를 검색 합니다.

> [!NOTE]
> 합니다 `id`, `name`, 및 `type` 필드는 필요 하지 않지만 기존 Azure 에코 시스템을 사용 하 여 사용자 지정 리소스를 통합 하는 데 필요한 합니다.

샘플 리소스:

``` JSON
{
    "name": "{myCustomResourceName}",
    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}",
    "type": "Microsoft.CustomProviders/resourceProviders/myCustomResources",
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

매개 변수 참조:

속성 | 예제 | Description
---|---|---
name | '{myCustomResourceName}' | 사용자 지정 리소스의 이름입니다.
type | 'Microsoft.CustomProviders/resourceProviders/{resourceTypeName}' | 리소스 형식 네임 스페이스입니다.
id | '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources/{myCustomResourceName}' | 리소스 id입니다.

### <a name="create-a-custom-resource"></a>사용자 지정 리소스 만들기

Azure API 들어오는 요청:

``` HTTP
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resource-provider-name}/myCustomResources/{myCustomResourceName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json

{
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

이 요청에 전달할 수는 합니다 **끝점** 형식에서:

``` HTTP
PUT https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}

{
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

마찬가지로, 응답을 **끝점** 고객에 게 전달 됩니다. 끝점의 응답을 반환 해야 합니다.

- 유효한 JSON 개체 문서입니다. 상위 개체 아래에 있는 모든 배열 및 문자열이 중첩 되어야 합니다.
- `Content-Type` 헤더가 "application/json;으로 설정 해야 charset = utf-8 "입니다.

**끝점** 응답:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "name": "{myCustomResourceName}",
    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}",
    "type": "Microsoft.CustomProviders/resourceProviders/myCustomResources",
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

Azure 사용자 지정 리소스 공급자 응답:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "name": "{myCustomResourceName}",
    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}",
    "type": "Microsoft.CustomProviders/resourceProviders/myCustomResources",
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

### <a name="remove-a-custom-resource"></a>사용자 지정 리소스를 제거 합니다.

Azure API 들어오는 요청:

``` HTTP
Delete https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json
```

이 요청에 전달할 수는 합니다 **끝점** 형식에서:

``` HTTP
Delete https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}
```

마찬가지로 응답 합니다 **끝점** 고객에 게 전달 됩니다. 끝점의 응답을 반환 해야 합니다.

- 유효한 JSON 개체 문서입니다. 상위 개체 아래에 있는 모든 배열 및 문자열이 중첩 되어야 합니다.
- `Content-Type` 헤더가 "application/json;으로 설정 해야 charset = utf-8 "입니다.

**끝점** 응답:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
```

Azure 사용자 지정 리소스 공급자 응답:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
```

### <a name="retrieve-a-custom-resource"></a>사용자 지정 리소스를 검색 합니다.

Azure API 들어오는 요청:

``` HTTP
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json
```

이 요청에 전달할 수는 합니다 **끝점** 형식에서:

``` HTTP
GET https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}
```

마찬가지로, 응답을 **끝점** 고객에 게 전달 됩니다. 끝점의 응답을 반환 해야 합니다.

- 유효한 JSON 개체 문서입니다. 상위 개체 아래에 있는 모든 배열 및 문자열이 중첩 되어야 합니다.
- `Content-Type` 헤더가 "application/json;으로 설정 해야 charset = utf-8 "입니다.

**끝점** 응답:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "name": "{myCustomResourceName}",
    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}",
    "type": "Microsoft.CustomProviders/resourceProviders/myCustomResources",
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

Azure 사용자 지정 리소스 공급자 응답:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "name": "{myCustomResourceName}",
    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}",
    "type": "Microsoft.CustomProviders/resourceProviders/myCustomResources",
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

### <a name="enumerate-all-custom-resources"></a>모든 사용자 지정 리소스를 열거 합니다.

Azure API 들어오는 요청:

``` HTTP
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json
```

이 요청에 전달할 수는 합니다 **끝점** 형식에서:

``` HTTP
GET https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources
```

마찬가지로, 응답을 **끝점** 고객에 게 전달 됩니다. 끝점의 응답을 반환 해야 합니다.

- 유효한 JSON 개체 문서입니다. 상위 개체 아래에 있는 모든 배열 및 문자열이 중첩 되어야 합니다.
- `Content-Type` 헤더가 "application/json;으로 설정 해야 charset = utf-8 "입니다.
- 최상위 수준 리소스 목록을 배치할 `value` 속성입니다.

**끝점** 응답:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "value" : [
        {
            "name": "{myCustomResourceName}",
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}",
            "type": "Microsoft.CustomProviders/resourceProviders/myCustomResources",
            "properties": {
                "myProperty1": "myPropertyValue1",
                "myProperty2": {
                    "myProperty3" : "myPropertyValue3"
                }
            }
        }
    ]
}
```

Azure 사용자 지정 리소스 공급자 응답:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "value" : [
        {
            "name": "{myCustomResourceName}",
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}",
            "type": "Microsoft.CustomProviders/resourceProviders/myCustomResources",
            "properties": {
                "myProperty1": "myPropertyValue1",
                "myProperty2": {
                    "myProperty3" : "myPropertyValue3"
                }
            }
        }
    ]
}
```

## <a name="next-steps"></a>다음 단계

- [Azure 사용자 지정 리소스 공급자에 대 한 개요](./custom-providers-overview.md)
- [빠른 시작: Azure 사용자 지정 리소스 공급자를 만들고 사용자 지정 리소스를 배포 합니다.](./create-custom-provider.md)
- [자습서: Azure에서 사용자 지정 작업 및 리소스 만들기](./tutorial-custom-providers-101.md)
- [방법: Azure REST API에 사용자 지정 작업 추가](./custom-providers-action-endpoint-how-to.md)
- [참조: 사용자 지정 리소스 캐시 참조](./custom-providers-proxy-cache-resource-endpoint-reference.md)
