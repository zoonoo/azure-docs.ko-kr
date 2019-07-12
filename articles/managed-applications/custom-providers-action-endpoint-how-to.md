---
title: Azure REST API에 사용자 지정 작업 추가
description: Azure REST API를 사용자 지정 작업을 추가 하는 방법에 알아봅니다. 이 문서에서는 사용자 지정 동작을 구현 하려는 끝점에 대 한 모범 사례와 요구 사항을 안내 합니다.
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: 6fbd20c201e1b141b7276e3283599b00cdefd118
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67795294"
---
# <a name="adding-custom-actions-to-azure-rest-api"></a>Azure REST API에 사용자 지정 작업 추가

이 문서에서는 사용자 지정 작업을 구현 하는 Azure 사용자 지정 리소스 공급자 끝점 만들기에 대 한 모범 사례와 요구 사항을 살펴봅니다. Azure 사용자 지정 리소스 공급자를 사용 하 여 잘 모르는 경우 [사용자 지정 리소스 공급자에 대 한 개요](./custom-providers-overview.md)합니다.

## <a name="how-to-define-an-action-endpoint"></a>작업 끝점을 정의 하는 방법

**끝점** 와 Azure 간의 기본 계약을 구현 하는 서비스를 가리키는 URL입니다. 끝점은 사용자 지정 리소스 공급자에 정의 되어 있으며 공개적으로 액세스 가능한 URL이 될 수 있습니다. 아래 샘플에는 **동작** 호출 `myCustomAction` 구현한 `endpointURL`합니다.

샘플 **ResourceProvider**:

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

## <a name="building-an-action-endpoint"></a>작업 끝점 빌드

**끝점** 구현 하는 **동작** Azure에서 새 API에 대 한 요청 및 응답을 처리 해야 합니다. 때 사용 하 여 사용자 지정 리소스 공급자는 **동작** 가 만들어지면 생성 Api 집합을 새 Azure에서. 이 경우 작업을 새 Azure API에 대해 작업을 발생 `POST` 호출 합니다.

``` JSON
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomAction
```

Azure API 들어오는 요청:

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

이 요청에 전달할 수는 합니다 **끝점** 형식에서:

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

마찬가지로, 응답을 **끝점** 고객에 게 전달 됩니다. 끝점의 응답을 반환 해야 합니다.

- 유효한 JSON 개체 문서입니다. 상위 개체 아래에 있는 모든 배열 및 문자열이 중첩 되어야 합니다.
- `Content-Type` 헤더가 "application/json;으로 설정 해야 charset = utf-8 "입니다.

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

## <a name="calling-a-custom-action"></a>사용자 지정 작업을 호출합니다.

두 가지 기본 사용자 지정 리소스 공급자에서 사용자 지정 작업을 호출 합니다.

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

매개 변수 | 필수 | Description
---|---|---
action | *예* | 정의 된 동작의 이름을 합니다 **ResourceProvider**합니다.
id | *예* | 리소스 ID를 **ResourceProvider**합니다.
request-body | *no* | 요청 본문에 전송 합니다 **끝점**합니다.

### <a name="azure-resource-manager-template"></a>Azure Resource Manager 템플릿

> [!NOTE]
> 작업에서 Azure Resource Manager 템플릿 지원 제한 됩니다. 템플릿 내에서 호출할 작업에 대 한 순서 대로 포함 되어야 합니다 [ `list` ](../azure-resource-manager/resource-group-template-functions-resource.md#list) 이름에 접두사.

샘플 **ResourceProvider** 목록 작업을 사용 하 여:

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

Azure Resource Manager 템플릿 샘플:

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

매개 변수 | 필수 | Description
---|---|---
resourceIdentifier | *예* | 리소스 ID를 **ResourceProvider**합니다.
apiVersion | *예* | 리소스 런타임 API 버전입니다. 항상 "2018-09-01-미리 보기" 이어야 합니다.
functionValues | *no* | 요청 본문에 전송 합니다 **끝점**합니다.

## <a name="next-steps"></a>다음 단계

- [Azure 사용자 지정 리소스 공급자에 대 한 개요](./custom-providers-overview.md)
- [빠른 시작: Azure 사용자 지정 리소스 공급자를 만들고 사용자 지정 리소스를 배포 합니다.](./create-custom-provider.md)
- [자습서: Azure에서 사용자 지정 작업 및 리소스 만들기](./tutorial-custom-providers-101.md)
- [방법: Azure REST API에 사용자 지정 리소스 추가](./custom-providers-resources-endpoint-how-to.md)
