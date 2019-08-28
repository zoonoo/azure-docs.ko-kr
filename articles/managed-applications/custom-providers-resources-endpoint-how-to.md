---
title: Azure REST API에 사용자 지정 리소스 추가
description: Azure REST API를 사용자 지정 리소스를 추가 하는 방법에 알아봅니다. 이 문서에서는 사용자 지정 리소스를 구현 하려는 끝점에 대 한 모범 사례와 요구 사항을 안내 합니다.
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: a3cd1fe69a0d99f9faf3a451f76a3a420d713711
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67795211"
---
# <a name="adding-custom-resources-to-azure-rest-api"></a>Azure REST API에 사용자 지정 리소스 추가

이 문서에서는 사용자 지정 리소스를 구현 하는 Azure 사용자 지정 리소스 공급자 끝점 만들기에 대 한 모범 사례와 요구 사항을 살펴봅니다. Azure 사용자 지정 리소스 공급자를 사용 하 여 잘 모르는 경우 [사용자 지정 리소스 공급자에 대 한 개요](./custom-providers-overview.md)합니다.

## <a name="how-to-define-a-resource-endpoint"></a>리소스 끝점을 정의 하는 방법

**끝점** 와 Azure 간의 기본 계약을 구현 하는 서비스를 가리키는 URL입니다. 끝점은 사용자 지정 리소스 공급자에 정의 되어 있으며 공개적으로 액세스 가능한 URL이 될 수 있습니다. 아래 샘플에는 **resourceType** 호출 `myCustomResource` 구현한 `endpointURL`합니다.

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

## <a name="building-a-resource-endpoint"></a>리소스 끝점 빌드

**끝점** 구현 하는 **resourceType** Azure에서 새 API에 대 한 요청 및 응답을 처리 해야 합니다. 때 사용 하 여 사용자 지정 리소스 공급자는 **resourceType** 가 만들어지면 생성 Api 집합을 새 Azure에서. 이 경우는 **resourceType** 에 대 한 새 Azure 리소스 API 생성 됩니다 `PUT`, `GET`, 및 `DELETE` 단일 리소스에서 CRUD를 수행 하려면 뿐만 `GET` 모든 기존 리소스를 검색 하려면:

단일 리소스를 조작 (`PUT`하십시오 `GET`, 및 `DELETE`):

``` JSON
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResource/{myCustomResourceName}
```

모든 리소스를 검색 (`GET`):

``` JSON
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResource
```

사용자 지정 리소스에 대 한 사용자 지정 리소스 공급자는 두 가지 유형의 제공할 **routingTypes**: "`Proxy`"및"`Proxy, Cache`"입니다.

### <a name="proxy-routing-type"></a>프록시 라우팅 유형

"`Proxy`" **routingType** 모든 메서드를 요청 하는 프록시를 **끝점** 사용자 지정 리소스 공급자에서 지정 합니다. 사용 하는 경우 "`Proxy`":

- 응답에 대 한 모든 권한 필요 합니다.
- 기존 리소스를 사용 하 여 시스템을 통합 합니다.

에 대해 자세히 알아보려면 "`Proxy`" 리소스를 참조 하세요. [사용자 지정 리소스 프록시 참조](./custom-providers-proxy-resource-endpoint-reference.md)

### <a name="proxy-cache-routing-type"></a>프록시 캐시 라우팅 유형

"`Proxy, Cache`" **routingType** 프록시만 `PUT` 및 `DELETE` 메서드를 요청 합니다 **끝점** 사용자 지정 리소스 공급자에 지정 된 합니다. 사용자 지정 리소스 공급자가 자동으로 반환 하는 `GET` 요청 캐시에 저장 된 항목에 기반 합니다. 사용자 지정 리소스를 캐시에 표시 되는 경우 사용자 지정 리소스 공급자 추가/Api Azure 준수 확인에 대 한 응답에서 필드를 덮어쓸 수도 됩니다. 사용 하는 경우 "`Proxy, Cache`":

- 새 시스템을 만드는 없는 기존 같은 리소스가 있습니다.
- 기존 Azure 에코 시스템을 사용 하 여 작동 합니다.

에 대해 자세히 알아보려면 "`Proxy, Cache`" 리소스를 참조 하세요. [사용자 지정 리소스 캐시 참조](./custom-providers-proxy-cache-resource-endpoint-reference.md)

## <a name="creating-a-custom-resource"></a>사용자 지정 리소스 만들기

두 가지 기본 사용자 지정 리소스 공급자에서 사용자 지정 리소스를 만드는 중:

- Azure CLI
- Azure 리소스 관리자 템플릿

### <a name="azure-cli"></a>Azure CLI

사용자 지정 리소스를 만듭니다.

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

매개 변수 | 필수 | Description
---|---|---
is-full-object | *예* | 속성 개체 위치, 태그, sku 및/또는 계획 등의 다른 옵션을 포함 함을 나타냅니다.
id | *예* | 사용자 지정 리소스의 리소스 ID입니다. 으로 존재 해야이 **ResourceProvider**
properties | *예* | 요청 본문에 전송 합니다 **끝점**합니다.

Azure 사용자 지정 리소스를 삭제 합니다.

```azurecli-interactive
az resource delete --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{resourceTypeName}/{customResourceName}
```

매개 변수 | 필수 | Description
---|---|---
id | *예* | 사용자 지정 리소스의 리소스 ID입니다. 이것으로 존재 해야 합니다 **ResourceProvider**합니다.

Azure 사용자 지정 리소스를 검색 합니다.

```azurecli-interactive
az resource show --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{resourceTypeName}/{customResourceName}
```

매개 변수 | 필수 | 설명
---|---|---
id | *예* | 사용자 지정 리소스의 리소스 ID입니다. 으로 존재 해야이 **ResourceProvider**

### <a name="azure-resource-manager-template"></a>Azure Resource Manager 템플릿

> [!NOTE]
> 응답 적절 한 포함 리소스 필요 `id`, `name`, 및 `type` 에서 합니다 **끝점**합니다.

Azure Resource Manager 템플릿은 요구 `id`, `name`, 및 `type` 다운스트림 끝점에서 올바르게 반환 합니다. 반환 되는 리소스 응답 형식 이어야 합니다.

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

Azure Resource Manager 템플릿 샘플:

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
resourceTypeName | *예* | **이름을** 의 합니다 **resourceType** 사용자 지정 공급자에 정의 합니다.
resourceProviderName | *예* | 사용자 지정 리소스 공급자 인스턴스 이름입니다.
customResourceName | *예* | 사용자 지정 리소스 이름입니다.

## <a name="next-steps"></a>다음 단계

- [Azure 사용자 지정 리소스 공급자에 대 한 개요](./custom-providers-overview.md)
- [빠른 시작: Azure 사용자 지정 리소스 공급자를 만들고 사용자 지정 리소스를 배포 합니다.](./create-custom-provider.md)
- [자습서: Azure에서 사용자 지정 작업 및 리소스 만들기](./tutorial-custom-providers-101.md)
- [방법: Azure REST API에 사용자 지정 작업 추가](./custom-providers-action-endpoint-how-to.md)
- [참조: 사용자 지정 리소스 프록시 참조](./custom-providers-proxy-resource-endpoint-reference.md)
- [참조: 사용자 지정 리소스 캐시 참조](./custom-providers-proxy-cache-resource-endpoint-reference.md)
