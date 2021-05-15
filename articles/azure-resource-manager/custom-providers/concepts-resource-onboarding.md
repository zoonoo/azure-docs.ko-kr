---
title: 리소스 온보딩
description: Azure 사용자 지정 공급자를 사용하여 다른 Azure 리소스 유형에 관리 또는 구성을 적용하여 리소스 온보딩을 수행하는 방법을 알아봅니다.
author: jjbfour
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: jobreen
ms.openlocfilehash: 1846b036f12fe7e691021ec0248782cad946d9b7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "75650410"
---
# <a name="azure-custom-providers-resource-onboarding-overview"></a>Azure 사용자 지정 공급자 리소스 온보딩 개요

Azure 사용자 지정 공급자 리소스 온보딩은 Azure 리소스 유형에 대한 확장성 모델입니다. 이를 통해 대규모의 기존 Azure 리소스에서 작업 또는 관리를 적용할 수 있습니다. 자세한 내용은 [Azure 사용자 지정 공급자가 Azure를 확장하는 방법](overview.md)을 참조하세요. 이 문서에서는 다음을 설명합니다.

- 리소스 온보딩이 할 수 있는 작업.
- 리소스 온보딩 기본 사항 및 사용 방법.
- 시작하기 위한 가이드 및 코드 샘플을 찾을 수 있는 위치.

> [!IMPORTANT]
> 사용자 지정 공급자는 현재 퍼블릭 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며, 프로덕션 워크로드에는 권장되지 않습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="what-can-resource-onboarding-do"></a>리소스 온보딩은 무엇을 할 수 있나요?

[Azure 사용자 지정 공급자 사용자 지정 리소스](./custom-providers-resources-endpoint-how-to.md)와 마찬가지로 리소스 온보딩은 엔드포인트에 대한 “온보딩” 요청에 프록시를 사용하는 계약을 정의합니다. 사용자 지정 리소스와 달리 리소스 온보딩은 새 리소스 유형을 만들지 않습니다. 대신 기존 리소스 유형의 확장을 허용합니다. 그리고 리소스 온보딩은 Azure Policy를 준수하므로 리소스의 관리 및 구성이 대규모로 수행될 수 있습니다. 리소스 온보딩 워크플로의 몇 가지 예:

- 가상 머신 확장을 설치하고 관리합니다.
- Azure 스토리지 계정에 대한 기본값을 업로드하고 구성합니다.
- 대규모로 기준 진단 설정을 사용하도록 설정합니다.

## <a name="resource-onboarding-basics"></a>리소스 온보딩 기본 사항

Microsoft.CustomProviders/resourceProviders 및 Microsoft.CustomProviders/associations 리소스 유형을 사용하여 Azure 사용자 지정 공급자를 통해 리소스 온보딩을 구성합니다. 사용자 지정 공급자에 대한 리소스 온보딩을 사용하도록 설정하려면 구성 프로세스 중에 “Extension”을 포함하는 **routingType** 을 사용하여 “associations”이라는 **resourceType** 을 만듭니다. Microsoft.CustomProviders/associations 및 Microsoft.CustomProviders/resourceProviders는 동일한 리소스 그룹에 속할 필요가 없습니다.

샘플 Azure 사용자 지정 공급자는 다음과 같습니다.

```JSON
{
  "properties": {
    "resourceTypes": [
      {
        "name": "associations",
        "routingType": "Proxy,Cache,Extension",
        "endpoint": "https://microsoft.com/"
      }
    ]
  },
  "location": "eastus"
}
```

속성 | 필수 여부 | 설명
---|---|---
name | 예 | 엔드포인트 정의의 이름입니다. 리소스 온보딩의 경우 이름은 “associations”이어야 합니다.
routingType | 예 | 엔드포인트를 사용하여 계약 형식을 결정합니다. 리소스 온보딩의 경우 유효한 **routingTypes** 는 “Proxy, Cache, Extension” 및 “Webhook, Cache, Extension”입니다.
엔드포인트(endpoint) | 예 | 요청을 라우팅하는 엔드포인트입니다. 이는 응답과 요청의 모든 부작용을 처리합니다.

연결 리소스 유형을 사용하여 사용자 지정 공급자를 만든 후에는 Microsoft.CustomProviders/associations를 사용하여 대상을 지정할 수 있습니다. Microsoft.CustomProviders/associations는 모든 다른 Azure 리소스를 확장할 수 있는 확장 리소스입니다. Microsoft.CustomProviders/associations의 인스턴스가 만들어지면 **targetResourceId** 속성을 사용하며 이는 유효한 Microsoft.CustomProviders/resourceProviders 또는 Microsoft.Solutions/applications 리소스 ID여야 합니다. 이러한 경우 사용자가 만든 Microsoft.CustomProviders/resourceProviders 인스턴스의 연결 리소스 유형으로 요청이 전달됩니다.

> [!NOTE]
> **targetResourceId** 로 Microsoft.Solutions/applications 리소스 ID가 제공된 경우 “public”이라는 이름으로 관리형 리소스 그룹에 배포된 Microsoft.CustomProviders/resourceProviders가 있어야 합니다.

샘플 Azure 사용자 지정 공급자 연결:

```JSON
{
  "properties": {
    "targetResourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}",
    ...
  }
}
```

속성 | 필수 여부 | 설명
---|---|---
targetResourceId | 예 | Microsoft.CustomProviders/resourceProviders 또는 Microsoft.Solutions/applications의 리소스 ID.

## <a name="how-to-use-resource-onboarding"></a>리소스 온보딩 사용 방법

리소스 온보딩은 Microsoft.CustomProviders/associations 확장 리소스를 사용하여 다른 리소스를 확장하는 방식으로 작동합니다. 다음 샘플에서는 가상 머신에 대한 요청이 만들어지지만 모든 리소스를 확장할 수 있습니다.

먼저 연결 리소스 유형을 사용하여 사용자 지정 공급자 리소스를 만들어야 합니다. 이렇게 하면 사용자 지정 공급자를 대상으로 하는 해당 Microsoft.CustomProviders/associations를 만들 때 사용되는 콜백 URL이 선언됩니다.

샘플 Microsoft.CustomProviders/resourceProviders에서 요청을 만듭니다.

``` HTTP
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json

{
  "properties": {
    "resourceTypes": [
      {
        "name": "associations",
        "routingType": "Proxy,Cache,Extension",
        "endpoint": "https://{myCustomEndpoint}/"
      }
    ]
  },
  "location": "{location}"
}
```

사용자 지정 공급자를 만든 다음 다른 리소스를 대상으로 지정하고 사용자 지정 공급자의 부작용을 적용할 수 있습니다.

샘플 Microsoft.CustomProviders/associations 만들기 요청:

``` HTTP
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{virtualMachineName}/providers/Microsoft.CustomProviders/associations/{associationName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json

{
  "properties": {
    "targetResourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}",
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
  }
}
```

해당 요청은 사용자가 만든 사용자 지정 공급자에 지정된 엔드포인트로 전달되며 이는 이러한 형식의 **targetResourceId** 에 의해 참조됩니다.

``` HTTP
PUT https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/associations/{associationName}
X-MS-CustomProviders-ExtensionPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{virtualMachineName}/providers/Microsoft.CustomProviders/associations/{associationName}
X-MS-CustomProviders-ExtendedResource: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{virtualMachineName}

{
  "properties": {
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
  }
}
```

엔드포인트는 `Content-Type` 및 유효한 JSON 응답 본문으로 응답해야 합니다. JSON의 **properties** 개체 아래에서 반환되는 필드는 연결 반환 응답에 추가됩니다.

## <a name="getting-help"></a>도움말 보기

Azure 사용자 지정 공급자 개발에 대한 질문이 있는 경우 [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-custom-providers)에 문의하세요. 이미 비슷한 질문과 그에 대한 답변이 게시되었을 수도 있으므로, 먼저 확인한 후 게시하세요. 빠른 응답을 얻으려면 ```azure-custom-providers``` 태그를 추가하세요.

## <a name="next-steps"></a>다음 단계

이 문서에서는 사용자 지정 공급 기업에 대해 알아보았습니다. 자세한 내용은 다음 문서를 참조하세요.

- [자습서: 사용자 지정 공급자를 사용하여 리소스 온보딩](./tutorial-resource-onboarding.md)
- [자습서: Azure에서 사용자 지정 작업 및 리소스 만들기](./tutorial-get-started-with-custom-providers.md)
- [빠른 시작: 사용자 지정 리소스 공급자 만들기 및 사용자 지정 리소스 배포](./create-custom-provider.md)
- [방법: Azure REST API에 사용자 지정 작업 추가](./custom-providers-action-endpoint-how-to.md)
- [방법: Azure REST API에 사용자 지정 리소스 추가](./custom-providers-resources-endpoint-how-to.md)
