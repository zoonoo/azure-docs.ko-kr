---
title: 사용자 지정 리소스 캐시 참조
description: Azure 사용자 지정 리소스 공급자에 대 한 사용자 지정 리소스 캐시 참조입니다. 이 문서에서는 캐시 사용자 지정 리소스를 구현 하는 끝점에 대 한 요구 사항을 설명 합니다.
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: e1b8c44f020d18066423eed236018308fe88b607
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "75650384"
---
# <a name="custom-resource-cache-reference"></a>사용자 지정 리소스 캐시 참조

이 문서에서는 캐시 사용자 지정 리소스를 구현 하는 끝점에 대 한 요구 사항을 설명 합니다. Azure 사용자 지정 리소스 공급자에 대해 잘 모르는 경우 [사용자 지정 리소스 공급자에 대 한 개요](overview.md)를 참조 하세요.

## <a name="how-to-define-a-cache-resource-endpoint"></a>캐시 리소스 끝점을 정의 하는 방법

프록시 리소스는 **Routingtype** 을 "Proxy, Cache"로 지정 하 여 만들 수 있습니다.

샘플 사용자 지정 리소스 공급자:

```JSON
{
  "properties": {
    "resourceTypes": [
      {
        "name": "myCustomResources",
        "routingType": "Proxy, Cache",
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

## <a name="building-proxy-resource-endpoint"></a>프록시 리소스 끝점을 빌드하는 중

"프록시, 캐시" 리소스 **끝점** 을 구현 하는 **끝점** 은 Azure의 새 API에 대 한 요청 및 응답을 처리 해야 합니다. 이 경우 **resourceType** 은, `PUT` `GET`및에 대 한 새 Azure 리소스 API를 생성 하 고 `DELETE` 모든 기존 리소스를 검색 하는 것 `GET` 뿐만 아니라 단일 리소스에 대해 CRUD를 수행 합니다.

> [!NOTE]
> Azure API `PUT`는 요청 메서드인 `GET`및 `DELETE`를 생성 하지만 캐시 **끝점** 은 및 `PUT` `DELETE`만 처리 하면 됩니다.
> **끝점** 에서도 구현 `GET`하는 것이 좋습니다.

### <a name="create-a-custom-resource"></a>사용자 지정 리소스 만들기

Azure API 들어오는 요청:

``` HTTP
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}?api-version=2018-09-01-preview
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

이 요청은 다음 형식으로 **끝점** 에 전달 됩니다.

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

마찬가지로, **끝점** 의 응답은 고객에 게 다시 전달 됩니다. 끝점의 응답은 다음을 반환 해야 합니다.

- 유효한 JSON 개체 문서입니다. 모든 배열 및 문자열은 최상위 개체 아래에 중첩 되어야 합니다.
- `Content-Type` 헤더를 "application/json;으로 설정 해야 합니다. charset = utf-8 ".
- 사용자 지정 리소스 공급자는 요청에 `name`대 `type`한, `id` 및 필드를 덮어씁니다.
- 사용자 지정 리소스 공급자는 캐시 끝점에 대 한 `properties` 개체 아래 에서만 필드를 반환 합니다.

**끝점** 응답이

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

사용자 `name`지정 `id`리소스 공급자 `type` 는 사용자 지정 리소스에 대해, 및 필드가 자동으로 생성 됩니다.

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

### <a name="remove-a-custom-resource"></a>사용자 지정 리소스 제거

Azure API 들어오는 요청:

``` HTTP
Delete https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json
```

이 요청은 다음 형식으로 **끝점** 에 전달 됩니다.

``` HTTP
Delete https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}
```

마찬가지로, **끝점** 의 응답은 고객에 게 다시 전달 됩니다. 끝점의 응답은 다음을 반환 해야 합니다.

- 유효한 JSON 개체 문서입니다. 모든 배열 및 문자열은 최상위 개체 아래에 중첩 되어야 합니다.
- `Content-Type` 헤더를 "application/json;으로 설정 해야 합니다. charset = utf-8 ".
- 200 수준 응답이 반환 되 면 Azure 사용자 지정 리소스 공급자는 해당 캐시에서 항목만 제거 합니다. 리소스가 없는 경우에도 **끝점** 은 204을 반환 해야 합니다.

**끝점** 응답이

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
```

Azure 사용자 지정 리소스 공급자 응답:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
```

### <a name="retrieve-a-custom-resource"></a>사용자 지정 리소스 검색

Azure API 들어오는 요청:

``` HTTP
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json
```

요청은 **끝점**으로 전달 **되지** 않습니다.

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

### <a name="enumerate-all-custom-resources"></a>모든 사용자 지정 리소스 열거

Azure API 들어오는 요청:

``` HTTP
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json
```

이 요청은 **끝점**으로 전달 **되지** 않습니다.

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

- [Azure 사용자 지정 리소스 공급자에 대 한 개요](overview.md)
- [빠른 시작: Azure 사용자 지정 리소스 공급자 만들기 및 사용자 지정 리소스 배포](./create-custom-provider.md)
- [자습서: Azure에서 사용자 지정 작업 및 리소스 만들기](./tutorial-get-started-with-custom-providers.md)
- [방법: Azure REST API에 사용자 지정 작업 추가](./custom-providers-action-endpoint-how-to.md)
- [참조: 사용자 지정 리소스 프록시 참조](proxy-resource-endpoint-reference.md)
