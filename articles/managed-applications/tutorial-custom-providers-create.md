---
title: 사용자 지정 공급자 만들기 및 사용
description: 이 자습서에서는 Azure Custom Provider를 만들고 사용하는 방법을 보여줍니다. 사용자 지정 공급자를 사용하여 Azure에서 워크플로를 변경합니다.
author: jjbfour
ms.service: managed-applications
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: 6217e9007f20cb365aff0837f58f1a6074a3e6ce
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72330052"
---
# <a name="create-and-use-a-custom-provider"></a>사용자 지정 공급자 만들기 및 사용

사용자 지정 공급자는 Azure와 엔드포인트 사이의 계약입니다. 사용자 지정 공급자를 사용하면 Azure에서 워크플로를 변경할 수 있습니다. 이 자습서에서는 사용자 지정 공급자를 만드는 프로세스를 살펴봅니다. Azure 사용자 지정 공급자가 생소한 경우에는 [Azure 사용자 지정 리소스 공급자에 대한 개요](./custom-providers-overview.md)를 참조하세요.

## <a name="create-a-custom-provider"></a>사용자 지정 공급자 만들기

> [!NOTE]
> 이 자습서에서는 엔드포인트를 작성하는 방법을 보여주지 않습니다. RESTFUL 엔드포인트가 없으면 현재 자습서의 기반이 되는 [RESTful 엔드포인트 작성에 대한 자습서](./tutorial-custom-providers-function-authoring.md)를 참조하세요.

엔드포인트를 만든 후에는 공급자와 엔드포인트 간의 계약을 생성하는 사용자 지정 공급자를 만들 수 있습니다. 사용자 지정 공급자를 통해 엔드포인트 정의 목록을 지정할 수 있습니다.

```JSON
{
  "name": "myEndpointDefinition",
  "routingType": "Proxy",
  "endpoint": "https://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>"
}
```

자산 | 필수 | 설명
---|---|---
**name** | 예 | 엔드포인트 정의의 이름입니다. Azure는 이 이름을 해당 API를 통해 /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders<br>/resourceProviders/{resourceProviderName}/{endpointDefinitionName} 아래에 노출합니다.
**routingType** | 아니요 | 엔드포인트 계약 형식입니다. 값을 지정하지 않으면 기본값은 “Proxy”입니다.
**endpoint** | 예 | 요청을 라우팅하는 엔드포인트입니다. 이 엔드포인트는 응답 및 요청의 부작용을 처리합니다.

**엔드포인트**의 값은 Azure 함수 앱의 트리거 URL입니다. `<yourapp>`, `<funcname>` 및 `<functionkey>` 자리 표시자는 생성한 함수 앱의 값으로 바꾸어야 합니다.

## <a name="define-custom-actions-and-resources"></a>사용자 지정 작업 및 리소스 정의

사용자 지정 공급자는 **actions** 및 **resourceTypes** 속성에서 모델링된 엔드포인트 정의 목록을 포함합니다. **actions** 속성은 사용자 지정 공급자가 노출하는 사용자 지정 작업에 매핑되고 **resourceTypes** 속성은 사용자 지정 리소스입니다. 이 자습서에서 사용자 지정 공급자에는 `myCustomAction`이라는 **actions** 속성과 `myCustomResources`라는 **resourceTypes** 속성이 있습니다.

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

## <a name="deploy-the-custom-provider"></a>사용자 지정 공급자 배포

> [!NOTE]
> **엔드포인트** 값을 이전 자습서에서 만든 함수 앱의 트리거 URL로 바꾸어야 합니다.

이전 사용자 지정 공급자는 Azure Resource Manager 템플릿을 사용하여 배포할 수 있습니다.

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

## <a name="use-custom-actions-and-resources"></a>사용자 지정 작업 및 리소스 사용

사용자 지정 공급자를 만든 후에는 새 Azure API를 사용할 수 있습니다. 다음 탭에서는 사용자 지정 공급자를 호출하고 사용하는 방법을 설명합니다.

### <a name="custom-actions"></a>사용자 지정 작업

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

> [!NOTE]
> `{subscriptionId}` 및 `{resourceGroupName}` 자리 표시자를 사용자 지정 공급자를 배포한 구독 및 리소스 그룹으로 바꾸어야 합니다.

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
*action* | 예 | 사용자 지정 공급자에 정의된 작업의 이름입니다.
*ids* | 예 | 사용자 지정 공급자의 리소스 ID입니다.
*request-body* | 아니요 | 엔드포인트로 전송될 요청 본문입니다.

# <a name="templatetabtemplate"></a>[템플릿](#tab/template)

없음.

---

### <a name="custom-resources"></a>사용자 지정 리소스

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

> [!NOTE]
> `{subscriptionId}` 및 `{resourceGroupName}` 자리 표시자를 사용자 지정 공급자를 배포한 구독 및 리소스 그룹으로 바꾸어야 합니다.

#### <a name="create-a-custom-resource"></a>사용자 지정 리소스 만들기

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
*is-full-object* | 예 | 속성 개체에 위치, 태그, SKU 또는 계획과 같은 다른 옵션이 포함되는지 여부를 나타냅니다.
*id* | 예 | 사용자 지정 리소스의 리소스 ID입니다. 이 ID는 사용자 지정 공급자 리소스 ID의 확장입니다.
*properties* | 예 | 엔드포인트로 전송될 요청 본문입니다.

#### <a name="delete-a-custom-resource"></a>사용자 지정 리소스 삭제

```azurecli-interactive
az resource delete --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/myCustomProvider/myCustomResources/myTestResourceName1
```

매개 변수 | 필수 | 설명
---|---|---
*id* | 예 | 사용자 지정 리소스의 리소스 ID입니다. 이 ID는 사용자 지정 공급자 리소스 ID의 확장입니다.

#### <a name="retrieve-a-custom-resource"></a>사용자 지정 리소스 검색

```azurecli-interactive
az resource show --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/myCustomProvider/myCustomResources/myTestResourceName1
```

매개 변수 | 필수 | 설명
---|---|---
*id* | 예 | 사용자 지정 리소스의 리소스 ID입니다. 이 ID는 사용자 지정 공급자 리소스 ID의 확장입니다.

# <a name="templatetabtemplate"></a>[템플릿](#tab/template)

샘플 Resource Manager 템플릿:

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
*resourceTypeName* | 예 | 사용자 지정 공급자에 정의된 **resourceTypes** 속성의 `name` 값입니다.
*resourceProviderName* | 예 | 사용자 지정 공급 기업 인스턴스 이름입니다.
*customResourceName* | 예 | 사용자 지정 리소스 이름입니다.

---

> [!NOTE]
> 사용자 지정 공급자의 배포 및 사용을 마쳤으면 Azure 함수 앱을 비롯한 생성된 모든 리소스를 정리해야 합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 사용자 지정 공급 기업에 대해 알아보았습니다. 자세한 내용은 다음을 참조하세요.

- [방법: Azure REST API에 사용자 지정 작업 추가](./custom-providers-action-endpoint-how-to.md)
- [방법: Azure REST API에 사용자 지정 리소스 추가](./custom-providers-resources-endpoint-how-to.md)
