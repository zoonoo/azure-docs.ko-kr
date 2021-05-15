---
title: Azure REST API에 사용자 지정 리소스 추가
description: Azure REST API에 사용자 지정 리소스를 추가하는 방법을 알아봅니다. 이 문서에서는 사용자 지정 리소스를 구현하려는 엔드포인트에 대한 요구 사항 및 모범 사례를 살펴봅니다.
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: b6c5f5b8e437ad2dc2e8a3be3f3f2ed03a613b44
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "75650527"
---
# <a name="adding-custom-resources-to-azure-rest-api"></a>Azure REST API에 사용자 지정 리소스 추가

이 문서에서는 사용자 지정 리소스를 구현하는 Azure 사용자 지정 리소스 공급자 엔드포인트를 만들기 위한 요구 사항 및 모범 사례를 살펴봅니다. Azure 사용자 지정 리소스 공급자에 익숙하지 않은 경우 [사용자 지정 리소스 공급자에 대한 개요](overview.md)를 참조하세요.

## <a name="how-to-define-a-resource-endpoint"></a>리소스 엔드포인트를 정의하는 방법

**엔드포인트** 는 서비스를 가리키는 URL로 서비스와 Azure 간의 기본 계약을 구현합니다. 엔드포인트는 사용자 지정 리소스 공급자에서 정의되며 공개적으로 액세스할 수 있는 모든 URL이 될 수 있습니다. 아래 샘플에는 `endpointURL`에서 구현한 `myCustomResource`라고 불리는 **resourceType** 이 있습니다.

샘플 **ResourceProvider**:

```JSON
{
  "properties": {
    "resourceTypes": [
      {
        "name": "myCustomResource",
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

## <a name="building-a-resource-endpoint"></a>리소스 엔드포인트 빌드

**ResourceType** 을 구현하는 **엔드포인트** 는 Azure의 새 API에 대한 요청 및 응답을 처리해야 합니다. **ResourceType** 을 사용하여 사용자 지정 리소스 공급자를 만들면 Azure에서 새로운 API 세트를 생성합니다. 이러한 경우 **resourceType** 은 `PUT`, `GET`, 단일 리소스에서 CRUD를 수행하기 위한 `DELETE`뿐만 아니라 모든 기존 리소스를 검색하기 위한`GET`에 대해 새 Azure 리소스 API를 생성합니다.

단일 리소스(`PUT`, `GET`, `DELETE`) 조작:

``` JSON
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResource/{myCustomResourceName}
```

모든 리소스 검색(`GET`):

``` JSON
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResource
```

사용자 지정 리소스의 경우 사용자 지정 리소스 공급자는 “`Proxy`” 및 “`Proxy, Cache`”라는 두 가지 형식의 **routingTypes** 를 제공합니다.

### <a name="proxy-routing-type"></a>프록시 라우팅 유형

“`Proxy`” **routingType** 은 모든 요청 메서드를 사용자 지정 리소스 공급자에서 지정된 **엔드포인트** 로 프록시를 사용합니다. “`Proxy`” 사용 시기:

- 응답에 대한 모든 권한이 필요합니다.
- 기존 리소스와 시스템을 통합합니다.

“`Proxy`” 리소스에 대한 자세한 내용은 [사용자 지정 리소스 프록시 참조](proxy-resource-endpoint-reference.md)를 참조하세요.

### <a name="proxy-cache-routing-type"></a>프록시 캐시 라우팅 유형

“`Proxy, Cache`” **routingType** 은 사용자 지정 리소스 공급자에 지정된 **엔드포인트** 에 대해 `PUT` 및 `DELETE` 요청 메서드만 프록시로 사용합니다. 사용자 지정 리소스 공급자는 캐시에 저장된 내용에 따라 `GET` 요청을 자동으로 반환합니다. 사용자 지정 리소스를 캐시로 표시하는 경우 사용자 지정 리소스 공급자는 또한 API가 Azure를 준수하도록 하기 위해 응답의 필드를 추가하거나 덮어씁니다. “`Proxy, Cache`” 사용 시기:

- 기존 리소스가 없는 새 시스템을 만듭니다.
- 기존 Azure 에코시스템을 사용합니다.

“`Proxy, Cache`” 리소스에 대한 자세한 내용은 [사용자 지정 리소스 캐시 참조](proxy-cache-resource-endpoint-reference.md)를 참조하세요.

## <a name="creating-a-custom-resource"></a>사용자 지정 리소스 만들기

사용자 지정 리소스 공급자에서 사용자 지정 리소스를 만드는 두 가지 주요 방법이 있습니다.

- Azure CLI
- Azure 리소스 관리자 템플릿

### <a name="azure-cli"></a>Azure CLI

사용자 지정 리소스 만들기:

```azurecli-interactive
az resource create --is-full-object \
                   --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{resourceTypeName}/{customResourceName} \
                   --properties \
                    '{
                        "location": "eastus",
                        "properties": {
                            "myProperty1": "myPropertyValue1",
                            "myProperty2": {
                                "myProperty3": "myPropertyValue3"
                            }
                        }
                    }'
```

매개 변수 | 필수 | 설명
---|---|---
is-full-object | *예* | 속성 개체에 위치, 태그, SKU 및/또는 계획과 같은 다른 옵션이 포함된다는 것을 나타냅니다.
id | *예* | 사용자 지정 리소스의 리소스 ID입니다. 이는 **ResourceProvider** 에 있어야 합니다.
properties | *예* | **엔드포인트** 로 전송될 요청 본문입니다.

Azure 사용자 지정 리소스 삭제:

```azurecli-interactive
az resource delete --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{resourceTypeName}/{customResourceName}
```

매개 변수 | 필수 | Description
---|---|---
id | *예* | 사용자 지정 리소스의 리소스 ID입니다. 이는 **ResourceProvider** 에 있어야 합니다.

Azure 사용자 지정 리소스 검색:

```azurecli-interactive
az resource show --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{resourceTypeName}/{customResourceName}
```

매개 변수 | 필수 | Description
---|---|---
id | *예* | 사용자 지정 리소스의 리소스 ID입니다. 이는 **ResourceProvider** 에 있어야 합니다.

### <a name="azure-resource-manager-template"></a>Azure Resource Manager 템플릿

> [!NOTE]
> 리소스를 사용하려면 응답에 **엔드포인트** 의 적절한 `id`, `name`, `type`이 포함되어야 합니다.

Azure Resource Manager 템플릿을 사용하려면 `id` , `name`, `type`이 다운스트림 엔드포인트에서 올바르게 반환되어야 합니다. 반환된 리소스 응답은 다음과 같은 형식이어야 합니다.

샘플 **엔드포인트** 응답:

``` JSON
{
  "properties": {
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3": "myPropertyValue3"
    }
  },
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{customResourceName}",
  "name": "{customResourceName}",
  "type": "Microsoft.CustomProviders/resourceProviders/{resourceTypeName}"
}
```

샘플 Azure Resource Manager 템플릿:

```JSON
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.CustomProviders/resourceProviders/{resourceTypeName}",
            "name": "{resourceProviderName}/{customResourceName}",
            "apiVersion": "2018-09-01-preview",
            "location": "eastus",
            "properties": {
                "myProperty1": "myPropertyValue1",
                "myProperty2": {
                    "myProperty3": "myPropertyValue3"
                }
            }
        }
    ]
}
```

매개 변수 | 필수 | 설명
---|---|---
resourceTypeName | *예* | 사용자 지정 공급자에 정의된 **resourceType** 의 **이름** 입니다.
resourceProviderName | *예* | 사용자 지정 리소스 공급자 인스턴스의 이름입니다.
customResourceName | *예* | 사용자 지정 리소스 이름입니다.

## <a name="next-steps"></a>다음 단계

- [Azure 사용자 지정 리소스 공급자에 대한 개요](overview.md)
- [빠른 시작: Azure 사용자 지정 리소스 공급자 만들기 및 사용자 지정 리소스 배포](./create-custom-provider.md)
- [자습서: Azure에서 사용자 지정 작업 및 리소스 만들기](./tutorial-get-started-with-custom-providers.md)
- [방법: Azure REST API에 사용자 지정 작업 추가](./custom-providers-action-endpoint-how-to.md)
- [참조: 사용자 지정 리소스 프록시 참조](proxy-resource-endpoint-reference.md)
- [참조: 사용자 지정 리소스 캐시 참조](proxy-cache-resource-endpoint-reference.md)
