---
title: 리소스 온보딩
description: Azure 사용자 지정 공급자를 사용하여 관리 또는 구성을 다른 Azure 리소스 유형에 적용하여 리소스 온보딩을 수행하는 방법에 대해 알아봅니다.
author: jjbfour
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: jobreen
ms.openlocfilehash: 1846b036f12fe7e691021ec0248782cad946d9b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75650410"
---
# <a name="azure-custom-providers-resource-onboarding-overview"></a>Azure 사용자 지정 공급자 리소스 온보딩 개요

Azure 사용자 지정 공급자 리소스 온보딩AzureAzure Azure 리소스 형식에 대 한 확장성 모델입니다. 이를 통해 기존 Azure 리소스에 대규모로 작업 또는 관리를 적용할 수 있습니다. 자세한 내용은 [Azure 사용자 지정 공급자가 Azure](overview.md)를 확장할 수 있는 방법을 참조하세요. 이 문서에서는 다음을 설명합니다.

- 온보딩이 수행할 수 있는 리소스
- 리소스 온보딩 기본 및 사용 방법.
- 시작할 가이드 및 코드 샘플을 찾을 수 있는 위치입니다.

> [!IMPORTANT]
> 사용자 지정 공급자는 현재 공개 미리 보기 상태입니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 권장하지 않습니다. 일부 기능은 지원되지 않거나 제한된 기능이 있을 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="what-can-resource-onboarding-do"></a>리소스 온보딩은 무엇을 할 수 있습니까?

Azure [사용자 지정 공급자 사용자 지정 리소스와](./custom-providers-resources-endpoint-how-to.md)마찬가지로 리소스 온보딩은 요청을 끝점에 "온보딩" 하는 계약을 정의합니다. 사용자 지정 리소스와 달리 리소스 온보딩은 새 리소스 유형을 만들지 않습니다. 대신 기존 리소스 형식의 확장을 허용 합니다. 리소스 온보딩은 Azure 정책과 함께 작동하므로 리소스관리 및 구성을 대규모로 수행할 수 있습니다. 리소스 온보딩 워크플로의 몇 가지 예:

- 가상 시스템 확장에 설치 및 관리합니다.
- Azure 저장소 계정에 기본값을 업로드하고 구성합니다.
- 규모에서 기준 진단 설정을 활성화합니다.

## <a name="resource-onboarding-basics"></a>리소스 온보딩 기본 사항

Microsoft.CustomProviders/resourceProviders 및 Microsoft.CustomProviders/연결 리소스 형식을 사용 하 여 Azure 사용자 지정 공급자를 통해 온보딩 리소스를 구성 합니다. 사용자 지정 공급자에 대 한 리소스 온보딩을 사용 하려면 구성 프로세스 동안 "연결" 라는 **resourceType** "확장"을 포함 하는 **라우팅 Type을** 만듭니다. Microsoft.CustomProviders/연결 및 Microsoft.CustomProviders/리소스공급자는 동일한 리소스 그룹에 속할 필요가 없습니다.

다음은 Azure 사용자 지정 공급자 샘플입니다.

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
name | yes | 엔드포인트 정의의 이름입니다. 리소스 온보딩의 경우 이름은 "연결"이어야 합니다.
routingType | yes | 끝점을 사용하여 계약 유형을 결정합니다. 리소스 온보딩의 경우 유효한 **라우팅 유형은** "프록시, 캐시, 확장" 및 "웹후크, 캐시, 확장"입니다.
엔드포인트(endpoint) | yes | 요청을 라우팅하는 엔드포인트입니다. 이렇게 하면 요청의 응답 및 부작용이 처리됩니다.

연결 리소스 형식을 사용하여 사용자 지정 공급자를 만든 후 Microsoft.CustomProviders/연결을 사용하여 대상으로 지정할 수 있습니다. Microsoft.CustomProviders/연결은 다른 Azure 리소스를 확장할 수 있는 확장 리소스입니다. Microsoft.CustomProviders/연결의 인스턴스가 만들어지면 유효한 Microsoft.CustomProviders/resourceProviders 또는 Microsoft.Solutions/응용 프로그램 리소스 ID여야 하는 속성 **targetResourceId를**사용합니다. 이러한 경우 요청은 만든 Microsoft.CustomProviders/resourceProviders 인스턴스의 연결 리소스 유형으로 전달됩니다.

> [!NOTE]
> Microsoft.Solutions/응용 프로그램 리소스 ID가 **targetResourceId로**제공되는 경우 관리되는 리소스 그룹에 "public"이라는 이름으로 배포된 Microsoft.CustomProviders/resourceProviders가 있어야 합니다.

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
대상자원이있는 | yes | Microsoft.CustomProviders/리소스 공급자 또는 Microsoft.솔루션/응용 프로그램의 리소스 ID입니다.

## <a name="how-to-use-resource-onboarding"></a>리소스 온보딩 사용 방법

리소스 온보딩은 Microsoft.CustomProviders/연결 확장 리소스를 사용 하 여 다른 리소스를 확장 하 여 작동 합니다. 다음 샘플에서는 가상 시스템에 대한 요청이 이루어지지만 모든 리소스를 확장할 수 있습니다.

먼저 연결 리소스 유형이 있는 사용자 지정 공급자 리소스를 만들어야 합니다. 이렇게 하면 해당 Microsoft.CustomProviders/연결 리소스가 만들어사용자 공급자를 대상으로 하는 호출 연결 URL이 선언됩니다.

샘플 Microsoft.CustomProviders/리소스 공급자는 요청을 만듭니다.

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

사용자 지정 공급자를 만든 후 다른 리소스를 대상으로 지정하고 사용자 지정 공급자의 부작용을 적용할 수 있습니다.

샘플 Microsoft.CustomProviders/연결은 요청을 만듭니다.

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

이 요청은 만든 사용자 지정 공급자에 지정된 끝점으로 전달되며, 이 경우 이 양식의 **targetResourceId에서** 참조합니다.

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

끝점은 응용 프로그램/json `Content-Type` 및 유효한 JSON 응답 본문으로 응답해야 합니다. JSON의 **속성** 개체 아래에 반환되는 필드는 연결 반환 응답에 추가됩니다.

## <a name="getting-help"></a>도움말 보기

Azure 사용자 지정 리소스 공급자 개발에 대 한 질문이 있는 경우 [스택 오버플로에](https://stackoverflow.com/questions/tagged/azure-custom-providers)대 한 질문 을 시도 합니다. 이미 비슷한 질문과 그에 대한 답변이 게시되었을 수도 있으므로, 먼저 확인한 후 게시하세요. 빠른 응답을 얻으려면 ```azure-custom-providers``` 태그를 추가하세요.

## <a name="next-steps"></a>다음 단계

이 문서에서는 사용자 지정 공급 기업에 대해 알아보았습니다. 자세한 내용은 다음 문서를 참조하십시오.

- [자습서: 사용자 지정 공급자를 사용 하 고 리소스 온보딩](./tutorial-resource-onboarding.md)
- [자습서: Azure에서 사용자 지정 작업 및 리소스 만들기](./tutorial-get-started-with-custom-providers.md)
- [빠른 시작: 사용자 지정 리소스 공급자를 만들고 사용자 지정 리소스 배포](./create-custom-provider.md)
- [방법: Azure REST API에 사용자 지정 작업 추가](./custom-providers-action-endpoint-how-to.md)
- [방법: Azure REST API에 사용자 지정 리소스 추가](./custom-providers-resources-endpoint-how-to.md)
