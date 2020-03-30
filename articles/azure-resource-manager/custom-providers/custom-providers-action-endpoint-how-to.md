---
title: Azure REST API에 사용자 지정 작업 추가
description: Azure REST API에 사용자 지정 작업을 추가하는 방법에 대해 알아봅니다. 이 문서에서는 사용자 지정 작업을 구현하려는 끝점에 대한 요구 사항 및 모범 사례를 안내합니다.
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: 6110a7952b7c29609d2b98e135b61032aec3fa52
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75650397"
---
# <a name="adding-custom-actions-to-azure-rest-api"></a>Azure REST API에 사용자 지정 작업 추가

이 문서에서는 사용자 지정 작업을 구현하는 Azure 사용자 지정 리소스 공급자 끝점을 만들기 위한 요구 사항 및 모범 사례를 살펴보습니다. Azure 사용자 지정 리소스 공급자에 익숙하지 않은 경우 [사용자 지정 리소스 공급자에 대한 개요를 참조하세요.](overview.md)

## <a name="how-to-define-an-action-endpoint"></a>작업 끝점을 정의하는 방법

**끝점은** 서비스를 가리키는 URL로, 해당 URL과 Azure 간의 기본 계약을 구현합니다. 끝점은 사용자 지정 리소스 공급자에 정의되며 공개적으로 액세스할 수 있는 URL일 수 있습니다. 아래 샘플에는 `endpointURL`에서 `myCustomAction` 구현한 **작업이** 있습니다.

샘플 **리소스 공급자**:

```JSON
{
  "properties": {
    "actions": [
      {
        "name": "myCustomAction",
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

## <a name="building-an-action-endpoint"></a>작업 끝점 작성

**작업을** 구현하는 **끝점은** Azure의 새 API에 대한 요청 및 응답을 처리해야 합니다. **작업이** 있는 사용자 지정 리소스 공급자가 만들어지면 Azure에서 새 API 집합이 생성됩니다. 이 경우 작업은 호출에 대한 새 Azure `POST` 작업 API를 생성합니다.

``` JSON
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomAction
```

Azure API 수신 요청:

``` HTTP
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomAction?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json

{
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
}
```

이 요청은 다음 형식의 **끝점으로** 전달됩니다.

``` HTTP
POST https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomAction

{
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
}
```

마찬가지로 **끝점의** 응답은 고객에게 다시 전달됩니다. 끝점의 응답은 반환되어야 합니다.

- 유효한 JSON 개체 문서입니다. 모든 배열과 문자열은 맨 위 개체 아래에 중첩되어야 합니다.
- 헤더는 `Content-Type` "응용 프로그램/json; charset=utf-8".

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
}
```

Azure 사용자 지정 리소스 공급자 응답:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
}
```

## <a name="calling-a-custom-action"></a>사용자 지정 작업 호출

사용자 지정 리소스 공급자에서 사용자 지정 작업을 호출하는 두 가지 주요 방법이 있습니다.

- Azure CLI
- Azure 리소스 관리자 템플릿

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az resource invoke-action --action {actionName} \
                          --ids /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName} \
                          --request-body \
                            '{
                                "myProperty1": "myPropertyValue1",
                                "myProperty2": {
                                    "myProperty3": "myPropertyValue3"
                                }
                            }'
```

매개 변수 | 필수 | 설명
---|---|---
action | *예* | **리소스 공급자**에 정의된 작업의 이름입니다.
ids | *예* | **리소스 공급자의**리소스 ID입니다.
request-body | *아니요* | **끝점으로**전송될 요청 본문입니다.

### <a name="azure-resource-manager-template"></a>Azure Resource Manager 템플릿

> [!NOTE]
> Azure 리소스 관리자 템플릿에서 작업에는 지원이 제한되어 있습니다. 작업을 템플릿 내에서 호출하려면 해당 이름에 [`list`](../templates/template-functions-resource.md#list) 접두사를 포함해야 합니다.

목록 작업을 사용하여 리소스 **공급자** 샘플:

```JSON
{
  "properties": {
    "actions": [
      {
        "name": "listMyCustomAction",
        "routingType": "Proxy",
        "endpoint": "https://{endpointURL}/"
      }
    ]
  },
  "location": "eastus"
}
```

샘플 Azure Resource Manager 템플릿:

``` JSON
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "variables": {
        "resourceIdentifier": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}",
        "apiVersion": "2018-09-01-preview",
        "functionValues": {
            "myProperty1": "myPropertyValue1",
            "myProperty2": {
                "myProperty3": "myPropertyValue3"
            }
        }
    },
    "resources": [],
    "outputs": {
        "myCustomActionOutput": {
            "type": "object",
            "value": "[listMyCustomAction(variables('resourceIdentifier'), variables('apiVersion'), variables('functionValues'))]"
        }
    }
}
```

매개 변수 | 필수 | 설명
---|---|---
리소스 식별자 | *예* | **리소스 공급자의**리소스 ID입니다.
apiVersion | *예* | 리소스 런타임의 API 버전입니다. 이것은 항상 "2018-09-01-미리보기"여야합니다.
functionValues | *아니요* | **끝점으로**전송될 요청 본문입니다.

## <a name="next-steps"></a>다음 단계

- [Azure 사용자 지정 리소스 공급자에 대한 개요](overview.md)
- [빠른 시작: Azure 사용자 지정 리소스 공급자 를 만들고 사용자 지정 리소스 배포](./create-custom-provider.md)
- [자습서: Azure에서 사용자 지정 작업 및 리소스 만들기](./tutorial-get-started-with-custom-providers.md)
- [방법: Azure REST API에 사용자 지정 리소스 추가](./custom-providers-resources-endpoint-how-to.md)
