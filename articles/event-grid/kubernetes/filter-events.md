---
title: Kubernetes의 Azure Event Grid - 이벤트 필터링
description: 이 문서에서는 Azure Event Grid 구독을 만들 때 이벤트를 필터링하는 방법을 설명합니다.
author: jfggdl
ms.subservice: kubernetes
ms.author: jafernan
ms.date: 05/25/2021
ms.topic: conceptual
ms.openlocfilehash: e4dbdbb2f46546391b6e749a94bfa655bb9de45e
ms.sourcegitcommit: 5163ebd8257281e7e724c072f169d4165441c326
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/21/2021
ms.locfileid: "112417346"
---
# <a name="event-grid-on-kubernetes---event-filtering-for-subscriptions"></a>Kubernetes의 Event Grid - 구독에 대한 이벤트 필터링
Kubernetes의 Event Grid를 사용하면 json 페이로드의 모든 속성에 필터를 지정할 수 있습니다. 이러한 필터는 AND 조건 세트로 모델링되며 각 외부 조건에는 선택적 내부 OR 조건이 있습니다. 각 AND 조건에 대해 다음 값을 지정합니다.

- OperatorType - 비교 형식입니다.
- 키 - 필터를 적용할 속성의 json 경로입니다.
- 값 - 필터가 실행되는 참조 값 (또는) 값- 필터가 실행되는 참조 값 세트입니다.

[!INCLUDE [event-grid-preview-feature-note.md](../includes/event-grid-preview-feature-note.md)]



## <a name="filter-by-event-type"></a>이벤트 유형별 필터링
기본적으로 이벤트 원본에 대한 모든 [이벤트 유형](event-schemas.md)(`type` 특성)은 엔드포인트로 전송됩니다. 특정 이벤트 유형만 엔드포인트에 보내도록 결정할 수 있습니다. 이벤트 유형별로 필터링에 대한 JSON 구문은 다음과 같습니다.

```json
"filter": {
  "includedEventTypes": [
    "orderCreated",
    "orderUpdated"
  ]
}
```

위의 예제에서는 `orderCreated` 및 `orderUpdated` 이벤트 유형의 이벤트만 구독자 엔드포인트로 전송됩니다. 

샘플 이벤트는 다음과 같습니다.

```json
[{
      "specVersion": "1.0",
      "type" : "orderCreated",
      "source": "myCompanyName/us/webCommerceChannel/myOnlineCommerceSiteBrandName",
      "id" : "eventId-n",
      "time" : "2020-12-25T20:54:07+00:00",
      "subject" : "account/acct-123224/order/o-123456",
      "dataSchema" : "1.0",
      "data" : {
         "orderId" : "123",
         "orderType" : "PO",
         "reference" : "https://www.myCompanyName.com/orders/123"
      }
}]
```

## <a name="filter-by-subject"></a>제목별 필터링
제목별 간단한 필터링을 위해 제목에 대한 시작 또는 끝 값을 지정합니다. 주체를 필터링하는 JSON 구문:

```json
"filter": {
  "subjectBeginsWith": "/account/acct-123224/"
}
``` 

예를 들어 위에서 구성한 필터는 계정 `acct-123224`와 연결된 모든 주문을 구독자 엔드포인트로 보냅니다. 

항목에 이벤트를 게시할 때 구독자가 이벤트에 관심이 있는지 쉽게 알 수 있도록 사용자 이벤트에 대한 제목을 만듭니다. 구독자는 제목 속성을 사용하여 이벤트를 필터링 및 라우팅합니다. 구독자가 해당 경로의 세그먼트를 기준으로 필터링할 수 있도록 이벤트가 발생하는 경로를 추가하는 것이 좋습니다. 구독자는 경로를 통해 이벤트를 제한적이거나 광범위하게 필터링할 수 있습니다. 제목에 /A/B/C와 같은 3개의 세그먼트 경로를 제공하는 경우 구독자는 첫 번째 세그먼트 /A를 기준으로 필터링하여 광범위한 이벤트 세트를 가져올 수 있습니다. 해당 구독자는 /A/B/C 또는 /A/D/E와 같은 제목이 있는 이벤트를 가져옵니다. 다른 구독자는 /A/B를 기준으로 필터링하여 제한된 이벤트 세트를 가져올 수 있습니다.

## <a name="filter-by-values-in-event-data"></a>이벤트 데이터의 값으로 필터링
고급 필터링에 대해 자세히 알아보려면 [Azure의 Event Grid 문서에 있는 고급 필터링 섹션](../event-filtering.md)을 참조하세요. 다음 기능 및 연산자는 Kubernetes의 Event Grid에서 지원되지 않습니다. 

- 들어오는 이벤트 키의 배열 데이터 필터링
- [CloudEvents 확장 컨텍스트 특성](https://github.com/cloudevents/spec/blob/v1.0/documented-extensions.md)에 대한 필터링을 허용합니다.
- 다음 연산자
    - StringNotContains
    - StringNotBeginsWith
    - StringNotEndsWith
    - NumberInRange
    - NumberNotInRange
    - IsNullOrUndefined
    - IsNotNull
    

## <a name="next-steps"></a>다음 단계
Kubernetes용 Azure Arc의 Event Grid에서 지원하는 대상 및 처리기에 대해 알아보려면 [Kubernetes의 Event Grid - 이벤트 처리기](event-handlers.md)를 참조하세요.