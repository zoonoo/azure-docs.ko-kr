---
title: 사용자 지정 공급자 개요
description: Azure 사용자 지정 리소스 공급자및 워크플로에 맞게 Azure API 평면을 확장하는 방법에 대해 알아봅니다.
author: jjbfour
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: fd21117219ea3db6946e7a1b889d92702af65b58
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75650488"
---
# <a name="azure-custom-resource-providers-overview"></a>Azure 사용자 지정 리소스 공급자 개요

Azure 사용자 지정 리소스 공급자는 Azure에 대한 확장성 플랫폼입니다. 기본 Azure 환경을 보강하는 데 사용할 수 있는 사용자 지정 API를 정의할 수 있습니다. 이 설명서에는 다음이 설명됩니다.

- Azure 사용자 지정 리소스 공급자를 빌드하고 배포하는 방법
- Azure 사용자 지정 리소스 공급자를 사용하여 기존 워크플로를 확장하는 방법
- 시작할 가이드 및 코드 샘플을 찾을 수 있는 위치입니다.

![사용자 지정 공급 기업 개요](./media/overview/overview.png)

> [!IMPORTANT]
> 사용자 지정 공급자는 현재 공개 미리 보기 상태입니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="what-can-custom-resource-providers-do"></a>사용자 지정 리소스 공급자가 수행할 수 있는 일

다음은 Azure 사용자 지정 리소스 공급자를 통해 달성할 수 있는 몇 가지 예입니다.

- Azure 리소스 관리자 REST API를 확장하여 내부 및 외부 서비스를 포함합니다.
- 기존 Azure 워크플로 위에 사용자 지정 시나리오를 사용하도록 설정합니다.
- Azure 리소스 관리자 템플릿 제어 및 효과를 사용자 지정합니다.

## <a name="what-is-a-custom-resource-provider"></a>사용자 지정 리소스 공급자란 무엇입니까?

Azure 사용자 지정 리소스 공급자는 Azure와 끝점 간에 계약을 만들어 만들어집니다. 이 계약은 새 리소스인 **Microsoft.CustomProviders/resourceProviders를**통해 새 리소스 및 작업 목록을 정의합니다. 그러면 사용자 지정 리소스 공급자가 Azure에서 이러한 새 API를 노출합니다. Azure 사용자 지정 리소스 공급자는 사용자 지정 리소스 공급자, **끝점**및 사용자 지정 리소스의 세 부분으로 구성됩니다.

## <a name="how-to-build-custom-resource-providers"></a>사용자 지정 리소스 공급자를 빌드하는 방법

사용자 지정 리소스 공급자는 Azure와 끝점 간의 계약 목록입니다. 이 계약은 Azure가 끝점과 상호 작용하는 방법을 설명합니다. 리소스 공급자는 프록시처럼 작동하며 지정된 **끝점에서**요청 및 응답을 전달합니다. 리소스 공급자는 [**리소스유형**](./custom-providers-resources-endpoint-how-to.md) 및 [**작업이라는**](./custom-providers-action-endpoint-how-to.md)두 가지 유형의 계약을 지정할 수 있습니다. 이러한 정의는 끝점 정의를 통해 활성화됩니다. 끝점 정의는 **이름,** **라우팅유형**및 **끝점의**세 가지 필드로 구성됩니다.

샘플 끝점:

```JSON
{
  "name": "{endpointDefinitionName}",
  "routingType": "Proxy",
  "endpoint": "https://{endpointURL}/"
}
```

속성 | 필수 | 설명
---|---|---
name | *예* | 엔드포인트 정의의 이름입니다. Azure는 이 이름을 API를 통해 '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/<br>resourceProviders/{resourceProviderName}/{endpointDefinitionName}' 아래에 노출합니다.
routingType | *아니요* | **끝점을**사용하여 계약 유형을 결정합니다. 지정하지 않는 경우 기본값은 "Proxy"입니다.
엔드포인트(endpoint) | *예* | 요청을 라우팅하는 엔드포인트입니다. 응답은 물론 요청의 부작용도 처리합니다.

### <a name="building-custom-resources"></a>사용자 지정 리소스 빌드

**ResourceType** Azure에 추가 된 새 사용자 지정 리소스를 설명 합니다. 이들은 기본적인 RESTful CRUD 메서드를 노출합니다. [사용자 지정 리소스 만들기에 대해 자세히](./custom-providers-resources-endpoint-how-to.md) 알아보기

**리소스 유형이**있는 사용자 지정 리소스 공급자 샘플:

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
  "location": "eastus"
}
```

위의 샘플에 대해 Azure에 추가된 API:

HttpMethod | 샘플 URI | 설명
---|---|---
PUT | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources/{사용자 지정 리소스 이름}?api 버전=2018-09-01-미리 보기 | 새 리소스를 만들기 위해 Azure REST API 호출을 클릭합니다.
Delete | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources/{사용자 지정 리소스 이름}?api 버전=2018-09-01-미리 보기 | Azure REST API호출을 통해 기존 리소스를 삭제합니다.
GET | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources/{사용자 지정 리소스 이름}?api 버전=2018-09-01-미리 보기 | Azure REST API 호출을 통해 기존 리소스를 검색합니다.
GET | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources?api 버전=2018-09-01-미리 보기 | Azure REST API 호출을 사용하여 기존 리소스 목록을 검색합니다.

### <a name="building-custom-actions"></a>사용자 지정 작업 빌드

**작업은** Azure에 추가된 새 작업을 설명합니다. 리소스 공급자 위에 노출되거나 resourceType 에 중첩될 수 **있습니다.** [사용자 지정 작업 만들기에 대해 자세히](./custom-providers-action-endpoint-how-to.md) 알아보기

**작업을**사용하여 사용자 지정 리소스 공급자샘플:

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
  "location": "eastus"
}
```

위의 샘플에 대해 Azure에 추가된 API:

HttpMethod | 샘플 URI | 설명
---|---|---
POST | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomAction?api 버전=2018-09-01-미리 보기 | 작업을 활성화하기 위한 Azure REST API 호출입니다.

## <a name="looking-for-help"></a>도움말 찾기

Azure 사용자 지정 리소스 공급자 개발에 대 한 질문이 있는 경우 [스택 오버플로에](https://stackoverflow.com/questions/tagged/azure-custom-providers)문의 해 보십시오. 이미 비슷한 질문을 받고 답변했을 수 있으므로 게시하기 전에 먼저 확인하세요. 빠른 응답을 얻으려면 ```azure-custom-providers``` 태그를 추가하세요.

## <a name="next-steps"></a>다음 단계

이 문서에서는 사용자 지정 공급 기업에 대해 알아보았습니다. 다음 문서로 이동하면 사용자 지정 공급 기업을 만들 수 있습니다.

- [빠른 시작: Azure 사용자 지정 리소스 공급자 를 만들고 사용자 지정 리소스 배포](./create-custom-provider.md)
- [자습서: Azure에서 사용자 지정 작업 및 리소스 만들기](./tutorial-get-started-with-custom-providers.md)
- [방법: Azure REST API에 사용자 지정 작업 추가](./custom-providers-action-endpoint-how-to.md)
- [방법: Azure REST API에 사용자 지정 리소스 추가](./custom-providers-resources-endpoint-how-to.md)
