---
title: 사용자 지정 리소스 캐시 참조
description: Azure 사용자 지정 리소스 공급자에 대 한 사용자 지정 리소스 캐시 참조입니다. 이 문서에서는 캐시 사용자 지정 리소스를 구현하는 끝점에 대한 요구 사항을 검토합니다.
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: e1b8c44f020d18066423eed236018308fe88b607
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75650384"
---
# <a name="custom-resource-cache-reference"></a>사용자 지정 리소스 캐시 참조

이 문서에서는 캐시 사용자 지정 리소스를 구현하는 끝점에 대한 요구 사항을 검토합니다. Azure 사용자 지정 리소스 공급자에 익숙하지 않은 경우 [사용자 지정 리소스 공급자에 대한 개요를 참조하세요.](overview.md)

## <a name="how-to-define-a-cache-resource-endpoint"></a>캐시 리소스 끝점을 정의하는 방법

"프록시, 캐시"로 라우팅 **유형을** 지정하여 프록시 리소스를 만들 수 있습니다.

사용자 지정 리소스 공급자 샘플:

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

## <a name="building-proxy-resource-endpoint"></a>프록시 리소스 끝점 빌드

"프록시, 캐시" 리소스 **끝점을** 구현하는 **끝점은** Azure의 새 API에 대한 요청 및 응답을 처리해야 합니다. 이 경우 **resourceType은** `PUT`에 `GET`대한 새 Azure 리소스 `DELETE` API를 생성하고 단일 리소스에서 `GET` CRUD를 수행하고 모든 기존 리소스를 검색합니다.

> [!NOTE]
> Azure API는 요청 메서드 `PUT` `GET`및 `DELETE`및 을 생성하지만 캐시 **끝점은** 처리하기만 `PUT` 하면 됩니다. `DELETE`
> **끝점은** 또한 구현 하는 `GET`것이 좋습니다.

### <a name="create-a-custom-resource"></a>사용자 지정 리소스 만들기

Azure API 수신 요청:

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

이 요청은 다음 형식의 **끝점으로** 전달됩니다.

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

마찬가지로 **끝점의** 응답은 고객에게 다시 전달됩니다. 끝점의 응답은 반환되어야 합니다.

- 유효한 JSON 개체 문서입니다. 모든 배열과 문자열은 맨 위 개체 아래에 중첩되어야 합니다.
- 헤더는 `Content-Type` "응용 프로그램/json; charset=utf-8".
- 사용자 지정 리소스 공급자는 `name` `type`요청에 `id` 대한 및 의 필드를 덮어씁니다.
- 사용자 지정 리소스 공급자는 캐시 `properties` 끝점에 대한 개체 아래의 필드만 반환합니다.

**끝점** 응답:

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

`name`의 `id`및 `type` 필드는 사용자 지정 리소스 공급자가 사용자 지정 리소스에 대해 자동으로 생성됩니다.

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

Azure API 수신 요청:

``` HTTP
Delete https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json
```

이 요청은 다음 형식의 **끝점으로** 전달됩니다.

``` HTTP
Delete https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}
```

마찬가지로 **끝점의** 응답은 고객에게 다시 전달됩니다. 끝점의 응답은 반환되어야 합니다.

- 유효한 JSON 개체 문서입니다. 모든 배열과 문자열은 맨 위 개체 아래에 중첩되어야 합니다.
- 헤더는 `Content-Type` "응용 프로그램/json; charset=utf-8".
- Azure 사용자 지정 리소스 공급자는 200수준 응답이 반환되는 경우에만 해당 캐시에서 항목을 제거합니다. 리소스가 없는 경우에도 **끝점은** 204를 반환해야 합니다.

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

### <a name="retrieve-a-custom-resource"></a>사용자 지정 리소스 검색

Azure API 수신 요청:

``` HTTP
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json
```

요청은 **끝점으로** **전달되지 않습니다.**

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

### <a name="enumerate-all-custom-resources"></a>모든 사용자 지정 리소스 를 등록합니다.

Azure API 수신 요청:

``` HTTP
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json
```

이 요청은 **끝점으로** **전달되지 않습니다.**

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

- [Azure 사용자 지정 리소스 공급자에 대한 개요](overview.md)
- [빠른 시작: Azure 사용자 지정 리소스 공급자 를 만들고 사용자 지정 리소스 배포](./create-custom-provider.md)
- [자습서: Azure에서 사용자 지정 작업 및 리소스 만들기](./tutorial-get-started-with-custom-providers.md)
- [방법: Azure REST API에 사용자 지정 작업 추가](./custom-providers-action-endpoint-how-to.md)
- [참조: 사용자 지정 리소스 프록시 참조](proxy-resource-endpoint-reference.md)
