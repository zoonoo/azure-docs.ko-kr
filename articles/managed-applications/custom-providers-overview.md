---
title: Azure 사용자 지정 리소스 공급자 개요
description: Azure 사용자 지정 리소스 공급자 및 Azure API 평면에 맞게 워크플로 확장 하는 방법에 알아봅니다.
author: jjbfour
ms.service: managed-applications
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: d25ef00adc307bae57da2c04d4472874f8d67bcd
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67796077"
---
# <a name="azure-custom-resource-providers-overview"></a>Azure 사용자 지정 리소스 공급자 개요

Azure 사용자 지정 리소스 공급자는 Azure에는 확장성 플랫폼입니다. Azure 환경을 보강 기본값을 사용할 수 있는 사용자 지정 Api를 정의할 수 있습니다. 이 문서에 설명합니다.

- 빌드 및 Azure 사용자 지정 리소스 공급자를 배포 하는 방법입니다.
- 기존 워크플로 확장 하려면 Azure 사용자 지정 리소스 공급자를 활용 하는 방법.
- 시작 하기 지침 및 코드 샘플을 찾을 위치입니다.

![사용자 지정 공급자 개요](./media/custom-providers-overview/overview.png)

> [!IMPORTANT]
> 사용자 지정 공급자는 현재 공개 미리 보기로 제공 됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="what-can-custom-resource-providers-do"></a>사용자 지정 리소스 공급자의 기능은 있을까요?

Azure 사용자 지정 리소스 공급자를 사용 하 여 얻을 수 있는 것의 몇 가지 예는 다음과 같습니다.

- 내부 및 외부 서비스를 포함 하도록 Azure Resource Manager REST API를 확장 합니다.
- 기존 Azure 워크플로 맨 위에 사용자 지정 시나리오를 사용 하도록 설정 합니다.
- Azure Resource Manager 템플릿 컨트롤 및 적용을 사용자 지정 합니다.

## <a name="what-is-a-custom-resource-provider"></a>사용자 지정 리소스 공급자는 무엇입니까

Azure 사용자 지정 리소스 공급자는 Azure와 끝점 간의 계약을 만들어 수행 됩니다. 이 계약은 새 리소스 및 새 리소스를 통해 작업의 목록을 정의 **Microsoft.CustomProviders/resourceProviders**합니다. 사용자 지정 리소스 공급자는 Azure에서 이러한 새 Api를 노출 합니다. Azure 사용자 지정 리소스 공급자는 세 부분으로 구성 됩니다: 사용자 지정 리소스 공급자 **끝점**, 및 사용자 지정 리소스입니다.

## <a name="how-to-build-custom-resource-providers"></a>사용자 지정 리소스 공급자를 빌드하는 방법

사용자 지정 리소스 공급자는 Azure와 끝점 간의 계약의 목록입니다. 이 계약은 Azure 끝점과 상호 작용 해야 하는 방법을 설명 합니다. 리소스 공급자 역할을 프록시 등의 요청 및 응답을 지정 된 전달할 **끝점**합니다. 리소스 공급자는 두 가지 유형의 계약을 지정할 수 있습니다: [ **Resourcetype** ](./custom-providers-resources-endpoint-how-to.md) 하 고 [ **동작**](./custom-providers-action-endpoint-how-to.md)합니다. 이러한 끝점 정의 통해 설정 됩니다. 끝점 정의 세 가지 필드로 구성 됩니다. **이름을**를 **routingType**, 및 **끝점**합니다.

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
name | *예* | 끝점 정의의 이름입니다. Azure에서 API 통해이 이름을 노출 하는 ' /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/<br>resourceProviders/{resourceProviderName}/{endpointDefinitionName}'
routingType | *no* | 계약 유형을 사용 하 여 결정 합니다 **끝점**합니다. 지정 하지 않으면 "프록시" 초기화 됩니다.
엔드포인트(endpoint) | *예* | 요청을 라우팅하는 끝점입니다. 이 요청의 부작용 뿐만 아니라 응답 처리할 수 있습니다.

### <a name="building-custom-resources"></a>사용자 지정 리소스 작성

**Resourcetype** Azure에 추가 된 새 사용자 지정 리소스에 설명 합니다. 이러한 기본 RESTful CRUD 메서드를 노출합니다. 참조 [사용자 지정 리소스를 만드는 대 한 자세한 정보](./custom-providers-resources-endpoint-how-to.md)

사용자 지정 리소스 공급자를 사용 하 여 샘플 **Resourcetype**:

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

위의 샘플에 대 한 Azure에 추가 되는 Api:

HttpMethod | URI 예제 | Description
---|---|---
PUT | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources/{customResourceName}?api-version=2018-09-01-preview | 새 리소스를 만드는 Azure REST API 호출입니다.
DELETE | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources/{customResourceName}?api-version=2018-09-01-preview | 기존 리소스를 삭제 하려면 Azure REST API 호출입니다.
가져오기 | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources/{customResourceName}?api-version=2018-09-01-preview | 기존 리소스를 검색할 Azure REST API 호출입니다.
가져오기 | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources?api-version=2018-09-01-preview | 기존 리소스의 목록을 검색 하려면 Azure REST API 호출입니다.

### <a name="building-custom-actions"></a>빌드 사용자 지정 작업

**작업** Azure에 추가 된 새 작업을 설명 합니다. 이러한 리소스 공급자를 기반으로 노출 하거나 수 아래에 중첩 된 **resourceType**합니다. 참조 [사용자 지정 작업 만들기에 대 한 자세한](./custom-providers-action-endpoint-how-to.md)

사용자 지정 리소스 공급자를 사용 하 여 샘플 **작업**:

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

위의 샘플에 대 한 Azure에 추가 되는 Api:

HttpMethod | URI 예제 | 설명
---|---|---
올리기 | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomAction?api-version=2018-09-01-preview | 액션을 활성화할 Azure REST API 호출입니다.

## <a name="looking-for-help"></a>도움말

Azure 사용자 지정 리소스 공급자 개발에 대 한 질문이 있으면 질문 [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-custom-providers)합니다. 유사한 질문 이미 요청 및 응답을 게시 하기 전에 먼저 따라서 확인 있을 수 있습니다. 태그 추가 ```azure-custom-providers``` 빠른 응답을 받을!

## <a name="next-steps"></a>다음 단계

이 문서에서는 사용자 지정 공급자에 대해 알아보았습니다. 사용자 지정 공급자를 만들려면 다음 문서를 이동 합니다.

- [빠른 시작: Azure 사용자 지정 리소스 공급자를 만들고 사용자 지정 리소스를 배포 합니다.](./create-custom-provider.md)
- [자습서: Azure에서 사용자 지정 작업 및 리소스 만들기](./tutorial-custom-providers-101.md)
- [방법: Azure REST API에 사용자 지정 작업 추가](./custom-providers-action-endpoint-how-to.md)
- [방법: Azure REST API에 사용자 지정 리소스 추가](./custom-providers-resources-endpoint-how-to.md)
