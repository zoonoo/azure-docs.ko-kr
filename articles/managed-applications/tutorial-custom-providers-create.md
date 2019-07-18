---
title: Azure 사용자 지정 공급 기업 만들기 및 활용
description: 이 자습서에서는 사용자 지정 공급 기업을 만들고 활용하는 방법을 살펴봅니다.
author: jjbfour
ms.service: managed-applications
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: 65a8e60d8216e1da16af987c9e699e24ecaec3ec
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67799132"
---
# <a name="authoring-a-restful-endpoint-for-custom-providers"></a>사용자 지정 공급 기업을 위한 RESTful 엔드포인트 작성

사용자 지정 공급 기업을 사용하면 Azure에서 워크플로를 사용자 지정할 수 있습니다. 사용자 지정 공급 기업은 Azure와 `endpoint` 사이의 계약입니다. 이 자습서에서는 사용자 지정 공급 기업을 만드는 프로세스를 살펴봅니다. Azure 사용자 지정 공급 기업이 생소한 경우에는 [사용자 지정 리소스 공급자에 대한 개요](./custom-providers-overview.md)를 참조하세요.

이 자습서는 다음과 같은 단계로 나뉩니다.

- 사용자 지정 공급 기업이란?
- 사용자 지정 작업 및 리소스 정의
- 사용자 지정 공급 기업 배포

이 자습서는 다음 자습서를 기반으로 합니다.

- [사용자 지정 공급 기업을 위한 RESTful 엔드포인트 작성](./tutorial-custom-providers-function-authoring.md)

## <a name="creating-a-custom-provider"></a>사용자 지정 공급 기업 만들기

> [!NOTE]
> 이 자습서에서는 엔드포인트 작성에 대한 내용은 다루지 않습니다. RESTful 엔드포인트가 없으면 [RESTful 엔드포인트 작성에 대한 자습서](./tutorial-custom-providers-function-authoring.md)를 참조하세요.

`endpoint`가 만들어지면 사용자 지정 공급 기업을 생성하고 이것과 `endpoint` 간의 계약을 생성할 수 있습니다. 사용자 지정 공급 기업을 통해 엔드포인트 정의 목록을 지정할 수 있습니다.

```JSON
{
  "name": "myEndpointDefinition",
  "routingType": "Proxy",
  "endpoint": "https://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>"
}
```

자산 | 필수 | 설명
---|---|---
이름 | *예* | 엔드포인트 정의의 이름입니다. Azure는 이 이름을 API를 통해 '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/<br>resourceProviders/{resourceProviderName}/{endpointDefinitionName}' 아래에 노출합니다.
routingType | *아니요* | `endpoint`와의 계약 유형을 결정합니다. 지정하지 않는 경우 기본값은 "Proxy"입니다.
endpoint | *예* | 요청을 라우팅하는 엔드포인트입니다. 응답은 물론 요청의 부작용도 처리합니다.

이 경우 `endpoint`는 Azure Function의 트리거 URL입니다. `<yourapp>`, `<funcname>` 및 `<functionkey>`는 생성한 함수의 값으로 대체해야 합니다.

## <a name="defining-custom-actions-and-resources"></a>사용자 지정 작업 및 리소스 정의

사용자 지정 공급 기업은 `actions` 및 `resourceTypes`에서 모델링된 엔드포인트 정의 목록을 포함합니다. `actions`는 사용자 지정 공급 기업에서 노출되는 사용자 지정 작업에 매핑되는 반면, `resourceTypes`은 사용자 지정 리소스입니다. 이 자습서의 경우, `myCustomAction`라는 `action`과 `myCustomResources`라는 `resourceType`을 사용하여 사용자 지정 공급 기업을 정의합니다.

```JSON
{
  "properties": {
    "actions": [
      {
        "name": "myCustomAction",
        "routingType": "Proxy",
        "endpoint": "https://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>"
      }
    ],
    "resourceTypes": [
      {
        "name": "myCustomResources",
        "routingType": "Proxy",
        "endpoint": "https://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>"
      }
    ]
  },
  "location": "eastus"
}
```

`endpoint`는 이전 자습서에서 앞서 만든 함수의 트리거 URL로 대체합니다.

## <a name="deploying-the-custom-provider"></a>사용자 지정 공급 기업 배포

> [!NOTE]
> `endpoint`는 함수 URL로 대체해야 합니다.

위의 사용자 지정 공급 기업은 Azure Resource Manager 템플릿을 사용하여 배포할 수 있습니다.

```JSON
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.CustomProviders/resourceProviders",
            "name": "myCustomProvider",
            "apiVersion": "2018-09-01-preview",
            "location": "eastus",
            "properties": {
                "actions": [
                    {
                        "name": "myCustomAction",
                        "routingType": "Proxy",
                        "endpoint": "https://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>"
                    }
                ],
                "resourceTypes": [
                    {
                        "name": "myCustomResources",
                        "routingType": "Proxy",
                        "endpoint": "https://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>"
                    }
                ]
            }
        }
    ]
}
```

## <a name="using-custom-actions-and-resources"></a>사용자 지정 작업 및 리소스 사용

사용자 지정 공급 기업을 만든 후에는 새로운 Azure API를 활용할 수 있습니다. 다음 섹션에서는 사용자 지정 공급 기업을 호출하고 활용하는 방법을 설명합니다.

### <a name="custom-actions"></a>사용자 지정 작업

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

> [!NOTE]
> `{subscriptionId}`와 `{resourceGroupName}`은 사용자 지정 공급 기업이 배포된 구독과 리소스 그룹으로 바꿔야 합니다.

```azurecli-interactive
az resource invoke-action --action myCustomAction \
                          --ids /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/myCustomProvider \
                          --request-body
                            '{
                                "hello": "world"
                            }'
```

매개 변수 | 필수 | 설명
---|---|---
action | *예* | 만든 사용자 지정 공급 기업에 정의된 작업의 이름입니다.
ids | *예* | 만든 사용자 지정 공급 기업의 리소스 ID입니다.
request-body | *아니요* | `endpoint`로 전송될 요청 본문입니다.

# <a name="templatetabtemplate"></a>[템플릿](#tab/template)

없음.

---

### <a name="custom-resources"></a>사용자 지정 리소스

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

> [!NOTE]
> `{subscriptionId}`와 `{resourceGroupName}`은 사용자 지정 공급 기업이 배포된 구독과 리소스 그룹으로 바꿔야 합니다.

사용자 지정 리소스 만들기:

```azurecli-interactive
az resource create --is-full-object \
                   --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/myCustomProvider/myCustomResources/myTestResourceName1 \
                   --properties
                    '{
                        "location": "eastus",
                        "properties": {
                            "hello" : "world"
                        }
                    }'
```

매개 변수 | 필수 | 설명
---|---|---
is-full-object | *예* | 속성 개체에 위치, 태그, SKU 및/또는 계획과 같은 다른 옵션이 포함된다는 것을 나타냅니다.
id | *예* | 사용자 지정 리소스의 리소스 ID입니다. 만든 사용자 지정 공급 기업에 있어야 합니다.
properties | *예* | `endpoint`로 전송될 요청 본문입니다.

Azure 사용자 지정 리소스 삭제:

```azurecli-interactive
az resource delete --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/myCustomProvider/myCustomResources/myTestResourceName1
```

매개 변수 | 필수 | 설명
---|---|---
id | *예* | 사용자 지정 리소스의 리소스 ID입니다. 만든 사용자 지정 공급 기업에 있어야 합니다.

Azure 사용자 지정 리소스 검색:

```azurecli-interactive
az resource show --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/myCustomProvider/myCustomResources/myTestResourceName1
```

매개 변수 | 필수 | 설명
---|---|---
id | *예* | 사용자 지정 리소스의 리소스 ID입니다. 만든 사용자 지정 공급 기업에 있어야 합니다.

# <a name="templatetabtemplate"></a>[템플릿](#tab/template)

샘플 Azure Resource Manager 템플릿:

```JSON
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.CustomProviders/resourceProviders/myCustomResources",
            "name": "myCustomProvider/myTestResourceName1",
            "apiVersion": "2018-09-01-preview",
            "location": "eastus",
            "properties": {
                "hello": "world"
            }
        }
    ]
}
```

매개 변수 | 필수 | 설명
---|---|---
resourceTypeName | *예* | 사용자 지정 공급 기업에 정의된 *resourceType*의 `name`입니다.
resourceProviderName | *예* | 사용자 지정 공급 기업 인스턴스 이름입니다.
customResourceName | *예* | 사용자 지정 리소스 이름입니다.

---

> [!NOTE]
> 사용자 지정 공급 기업 배포 및 사용을 마쳤으면 Azure Function을 비롯한 생성된 모든 리소스를 정리해야 합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 사용자 지정 공급 기업에 대해 알아보았습니다. 다음 문서로 이동하면 사용자 지정 공급 기업을 만들 수 있습니다.

- [방법: Azure REST API에 사용자 지정 작업 추가](./custom-providers-action-endpoint-how-to.md)
- [방법: Azure REST API에 사용자 지정 리소스 추가](./custom-providers-resources-endpoint-how-to.md)
