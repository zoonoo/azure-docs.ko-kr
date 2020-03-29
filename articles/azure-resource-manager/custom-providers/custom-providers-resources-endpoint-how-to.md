---
title: Azure REST API에 사용자 지정 리소스 추가
description: Azure REST API에 사용자 지정 리소스를 추가하는 방법을 알아봅니다. 이 문서에서는 사용자 지정 리소스를 구현하려는 끝점에 대한 요구 사항 및 모범 사례를 안내합니다.
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: b6c5f5b8e437ad2dc2e8a3be3f3f2ed03a613b44
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75650527"
---
# <a name="adding-custom-resources-to-azure-rest-api"></a>Azure REST API에 사용자 지정 리소스 추가

이 문서에서는 사용자 지정 리소스를 구현하는 Azure 사용자 지정 리소스 공급자 끝점을 만들기 위한 요구 사항 및 모범 사례를 살펴보습니다. Azure 사용자 지정 리소스 공급자에 익숙하지 않은 경우 [사용자 지정 리소스 공급자에 대한 개요를 참조하세요.](overview.md)

## <a name="how-to-define-a-resource-endpoint"></a>리소스 끝점을 정의하는 방법

**끝점은** 서비스를 가리키는 URL로, 해당 URL과 Azure 간의 기본 계약을 구현합니다. 끝점은 사용자 지정 리소스 공급자에 정의되며 공개적으로 액세스할 수 있는 URL일 수 있습니다. 아래 샘플에는 에서 구현한 `endpointURL` **resourceType이** `myCustomResource` 있습니다.

샘플 **리소스 공급자**:

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

## <a name="building-a-resource-endpoint"></a>리소스 끝점 작성

**resourceType을** 구현하는 **끝점은** Azure의 새 API에 대한 요청 및 응답을 처리해야 합니다. **resourceType이** 있는 사용자 지정 리소스 공급자가 만들어지면 Azure에서 새 API 집합이 생성됩니다. 이 경우 **resourceType은** `PUT`에 `GET`대한 새 Azure 리소스 `DELETE` API를 생성하고 단일 리소스에서 `GET` CRUD를 수행하고 모든 기존 리소스를 검색합니다.

단일 리소스 조작`PUT` `GET`(, `DELETE`및)

``` JSON
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResource/{myCustomResourceName}
```

모든 리소스`GET`검색 ():

``` JSON
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResource
```

사용자 지정 리소스의 경우 사용자 지정 리소스 공급자는 두`Proxy, Cache`가지 유형의 라우팅 **유형을**제공합니다.`Proxy`

### <a name="proxy-routing-type"></a>프록시 라우팅 유형

"`Proxy` **"라우팅 유형은** 사용자 지정 리소스 공급자에 지정된 **끝점에** 대한 모든 요청 메서드를 프록시합니다. 사용 시기`Proxy`" :

- 응답을 완전히 제어해야 합니다.
- 시스템을 기존 리소스와 통합합니다.

" 리소스에`Proxy`대해 자세히 알아보려면 [사용자 지정 리소스 프록시 참조를](proxy-resource-endpoint-reference.md) 참조하십시오.

### <a name="proxy-cache-routing-type"></a>프록시 캐시 라우팅 유형

`Proxy, Cache`" **"라우팅 유형은** 사용자 `PUT` 지정 `DELETE` 리소스 공급자에 지정된 **끝점에** 메서드만 프록시를 요청합니다. 사용자 지정 리소스 공급자는 `GET` 캐시에 저장된 내용에 따라 요청을 자동으로 반환합니다. 사용자 지정 리소스가 캐시로 표시되면 사용자 지정 리소스 공급자는 응답에 필드를 추가/덮어쓰어 API Azure를 준수하도록 합니다. 사용 시기`Proxy, Cache`" :

- 기존 리소스가 없는 새 시스템 만들기
- 기존 Azure 에코시스템과 함께 작업합니다.

" 리소스에`Proxy, Cache`대해 자세히 알아보려면 [사용자 지정 리소스 캐시 참조를](proxy-cache-resource-endpoint-reference.md) 참조하세요.

## <a name="creating-a-custom-resource"></a>사용자 지정 리소스 만들기

사용자 지정 리소스 공급자에서 사용자 지정 리소스를 만드는 방법에는 두 가지가 있습니다.

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
id | *예* | 사용자 지정 리소스의 리소스 ID입니다. **리소스 공급자에서** 이 권한이 있어야 합니다.
properties | *예* | **끝점으로**전송될 요청 본문입니다.

Azure 사용자 지정 리소스 삭제:

```azurecli-interactive
az resource delete --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{resourceTypeName}/{customResourceName}
```

매개 변수 | 필수 | 설명
---|---|---
id | *예* | 사용자 지정 리소스의 리소스 ID입니다. **리소스 공급자**.

Azure 사용자 지정 리소스 검색:

```azurecli-interactive
az resource show --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{resourceTypeName}/{customResourceName}
```

매개 변수 | 필수 | 설명
---|---|---
id | *예* | 사용자 지정 리소스의 리소스 ID입니다. **리소스 공급자에서** 이 권한이 있어야 합니다.

### <a name="azure-resource-manager-template"></a>Azure Resource Manager 템플릿

> [!NOTE]
> 리소스에는 `id`응답에 적절한 . `name`및 `type` **끝점의**이가 포함되어야 합니다.

Azure 리소스 관리자 템플릿에는 `name`이 `type` `id`가 필요하며 다운스트림 끝점에서 올바르게 반환됩니다. 반환된 리소스 응답은 다음 형식이어야 합니다.

샘플 **끝점** 응답:

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
resourceTypeName | *예* | 사용자 지정 공급자에 정의된 **resourceType의** **이름입니다.**
resourceProviderName | *예* | 사용자 지정 리소스 공급자 인스턴스 이름입니다.
customResourceName | *예* | 사용자 지정 리소스 이름입니다.

## <a name="next-steps"></a>다음 단계

- [Azure 사용자 지정 리소스 공급자에 대한 개요](overview.md)
- [빠른 시작: Azure 사용자 지정 리소스 공급자 를 만들고 사용자 지정 리소스 배포](./create-custom-provider.md)
- [자습서: Azure에서 사용자 지정 작업 및 리소스 만들기](./tutorial-get-started-with-custom-providers.md)
- [방법: Azure REST API에 사용자 지정 작업 추가](./custom-providers-action-endpoint-how-to.md)
- [참조: 사용자 지정 리소스 프록시 참조](proxy-resource-endpoint-reference.md)
- [참조: 사용자 지정 리소스 캐시 참조](proxy-cache-resource-endpoint-reference.md)
