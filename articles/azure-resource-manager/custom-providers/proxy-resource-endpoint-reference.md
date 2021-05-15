---
title: 사용자 지정 리소스 프록시 참조
description: Azure 사용자 지정 리소스 공급자에 대한 사용자 지정 리소스 프록시 참조입니다. 이 문서에서는 프록시 사용자 지정 리소스를 구현하는 엔드포인트에 대한 요구 사항을 살펴봅니다.
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: 46b38686b39836f3d4bfb80686d514f932a79bf3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "75650462"
---
# <a name="custom-resource-proxy-reference"></a>사용자 지정 리소스 프록시 참조

이 문서에서는 프록시 사용자 지정 리소스를 구현하는 엔드포인트에 대한 요구 사항을 살펴봅니다. Azure 사용자 지정 리소스 공급자에 익숙하지 않은 경우 [사용자 지정 리소스 공급자에 대한 개요](overview.md)를 참조하세요.

## <a name="how-to-define-a-proxy-resource-endpoint"></a>프록시 리소스 엔드포인트를 정의하는 방법

프록시 리소스는 **routingType** 을 “Proxy”로 지정하여 만들 수 있습니다.

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

## <a name="building-proxy-resource-endpoint"></a>프록시 리소스 엔드포인트 빌드

“Proxy” 리소스 **엔드포인트** 를 구현하는 **엔드포인트** 는 Azure의 새 API에 대한 요청 및 응답을 처리해야 합니다. 이러한 경우 **resourceType** 은 `PUT`, `GET`, 단일 리소스에서 CRUD를 수행하기 위한 `DELETE`뿐만 아니라 모든 기존 리소스를 검색하기 위한`GET`에 대해 새 Azure 리소스 API를 생성합니다.

> [!NOTE]
> `id`, `name`, `type` 필드는 필요하지 않지만 사용자 지정 리소스를 기존 Azure 에코시스템에 통합하는 데는 필요합니다.

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

속성 | 샘플 | Description
---|---|---
name | '{myCustomResourceName}' | 사용자 지정 리소스의 이름입니다.
type | 'Microsoft.CustomProviders/resourceProviders/{resourceTypeName}' | 리소스 종류 네임스페이스입니다.
id | '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources/{myCustomResourceName}' | 리소스 ID입니다.

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

해당 요청은 다음 형식으로 **엔드포인트** 로 전달됩니다.

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

마찬가지로 **엔드포인트** 의 응답은 고객에게 다시 전달됩니다. 엔드포인트의 응답은 다음을 반환해야 합니다.

- 유효한 JSON 개체 문서. 모든 배열 및 문자열은 상위 개체 아래에 중첩되어야 합니다.
- `Content-Type` 헤더를 “application/json; charset=utf-8”으로 설정해야 합니다.

**엔드포인트** 응답:

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

### <a name="remove-a-custom-resource"></a>사용자 지정 리소스 제거

Azure API 들어오는 요청:

``` HTTP
Delete https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json
```

해당 요청은 다음 형식으로 **엔드포인트** 로 전달됩니다.

``` HTTP
Delete https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}
```

마찬가지로 **엔드포인트** 의 응답은 고객에게 다시 전달됩니다. 엔드포인트의 응답은 다음을 반환해야 합니다.

- 유효한 JSON 개체 문서. 모든 배열 및 문자열은 상위 개체 아래에 중첩되어야 합니다.
- `Content-Type` 헤더를 “application/json; charset=utf-8”으로 설정해야 합니다.

**엔드포인트** 응답:

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

해당 요청은 다음 형식으로 **엔드포인트** 로 전달됩니다.

``` HTTP
GET https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}
```

마찬가지로 **엔드포인트** 의 응답은 고객에게 다시 전달됩니다. 엔드포인트의 응답은 다음을 반환해야 합니다.

- 유효한 JSON 개체 문서. 모든 배열 및 문자열은 상위 개체 아래에 중첩되어야 합니다.
- `Content-Type` 헤더를 “application/json; charset=utf-8”으로 설정해야 합니다.

**엔드포인트** 응답:

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

### <a name="enumerate-all-custom-resources"></a>모든 사용자 지정 리소스 열거

Azure API 들어오는 요청:

``` HTTP
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json
```

해당 요청은 다음 형식으로 **엔드포인트** 로 전달됩니다.

``` HTTP
GET https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources
```

마찬가지로 **엔드포인트** 의 응답은 고객에게 다시 전달됩니다. 엔드포인트의 응답은 다음을 반환해야 합니다.

- 유효한 JSON 개체 문서. 모든 배열 및 문자열은 상위 개체 아래에 중첩되어야 합니다.
- `Content-Type` 헤더를 “application/json; charset=utf-8”으로 설정해야 합니다.
- 리소스 목록은 상위 수준 `value` 속성 아래에 배치해야 합니다.

**엔드포인트** 응답:

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

- [Azure 사용자 지정 리소스 공급자에 대한 개요](overview.md)
- [빠른 시작: Azure 사용자 지정 리소스 공급자 만들기 및 사용자 지정 리소스 배포](./create-custom-provider.md)
- [자습서: Azure에서 사용자 지정 작업 및 리소스 만들기](./tutorial-get-started-with-custom-providers.md)
- [방법: Azure REST API에 사용자 지정 작업 추가](./custom-providers-action-endpoint-how-to.md)
- [참조: 사용자 지정 리소스 캐시 참조](proxy-cache-resource-endpoint-reference.md)
