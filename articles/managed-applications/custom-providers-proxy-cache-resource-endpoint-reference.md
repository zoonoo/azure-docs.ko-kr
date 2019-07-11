---
title: 사용자 지정 리소스 캐시 참조
description: 사용자 지정 리소스 Azure 사용자 지정 리소스 공급자에 대 한 참조를 캐시 합니다. 이 문서에서는 리소스를 사용자 지정 하는 캐시를 구현 하는 끝점에 대 한 요구 사항을 살펴봅니다.
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: 6a3d570d9695516a293b601b3d34c2bcba6b058d
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/29/2019
ms.locfileid: "67478889"
---
# <a name="custom-resource-cache-reference"></a>사용자 지정 리소스 캐시 참조

이 문서에서는 리소스를 사용자 지정 하는 캐시를 구현 하는 끝점에 대 한 요구 사항을 살펴봅니다. Azure 사용자 지정 리소스 공급자를 사용 하 여 잘 모르는 경우 [사용자 지정 리소스 공급자에 대 한 개요](./custom-providers-overview.md)합니다.

## <a name="how-to-define-a-cache-resource-endpoint"></a>캐시 리소스 끝점을 정의 하는 방법

프록시 리소스를 지정 하 여 만들 수 있습니다 합니다 **routingType** "프록시에 캐시"입니다.

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

## <a name="building-proxy-resource-endpoint"></a>구성 프록시 리소스 끝점

**끝점** "프록시, 캐시" 리소스를 구현 하는 **끝점** Azure에서 새 API에 대 한 요청 및 응답을 처리 해야 합니다. 이 경우는 **resourceType** 에 대 한 새 Azure 리소스 API 생성 됩니다 `PUT`, `GET`, 및 `DELETE` 단일 리소스에서 CRUD를 수행 하려면 뿐만 `GET` 모든 기존 리소스를 검색 하려면:

> [!NOTE]
> Azure API 요청 메서드 생성 됩니다 `PUT`, `GET`, 및 `DELETE`, 하지만 캐시가 **끝점** 만 처리 해야 `PUT` 및 `DELETE`합니다.
> 하는 것이 좋습니다 합니다 **끝점** 구현 `GET`합니다.

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
- 사용자 지정 리소스 공급자를 덮어씁니다를 `name`, `type`, 및 `id` 요청에 대 한 필드입니다.
- 사용자 지정 리소스 공급자가 필드에서 반환 하 고만 `properties` 캐시 끝점에 대 한 개체입니다.

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

`name`, `id`, 및 `type` 필드는 사용자 지정 리소스 공급자에 의해 자동으로 사용자 지정 리소스에 대 한 생성 하는 수.

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

마찬가지로, 응답을 **끝점** 고객에 게 전달 됩니다. 끝점의 응답을 반환 해야 합니다.

- 유효한 JSON 개체 문서입니다. 상위 개체 아래에 있는 모든 배열 및 문자열이 중첩 되어야 합니다.
- `Content-Type` 헤더가 "application/json;으로 설정 해야 charset = utf-8 "입니다.
- 200 수준 응답을 반환 되 면 Azure 사용자 지정 리소스 공급자 캐시에서 항목을 제거만 됩니다. 리소스가 없는 경우에 합니다 **끝점** 204를 반환 해야 합니다.

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

요청은 **되지** 전달할 합니다 **끝점**합니다.

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

이 요청 됩니다 **되지** 전달할 합니다 **끝점**합니다.

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
- [자습서: Azure 사용자 지정 리소스 공급자를 만들고 사용자 지정 리소스를 배포 합니다.](./create-custom-provider.md)
- [방법: Azure REST API에 사용자 지정 작업 추가](./custom-providers-action-endpoint-how-to.md)
- [참조: 사용자 지정 리소스 프록시 참조](./custom-providers-proxy-resource-endpoint-reference.md)
