---
title: Kubernetes의 Azure Event Grid - 이벤트 전달 및 재시도
description: 이 문서에서는 Azure Arc를 사용하는 Kubernetes의 Event Grid 이벤트를 전달하는 방법과 전달되지 않은 메시지를 처리하는 방법을 설명합니다.
author: jfggdl
ms.subservice: kubernetes
ms.author: jafernan
ms.date: 05/25/2021
ms.topic: conceptual
ms.openlocfilehash: 6a37945461a61167e2fee7d7d3ef6a8fbccf3372
ms.sourcegitcommit: e7d500f8cef40ab3409736acd0893cad02e24fc0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122529607"
---
# <a name="event-grid-on-kubernetes---event-delivery-and-retry"></a>Kubernetes의 Event Grid - 이벤트 전달 및 재시도
Azure Arc를 사용하는 Kubernetes의 Event Grid는 일치하는 각 구독에 대해 각 메시지를 한 번 이상 즉시 전달하려고 합니다. 구독자로부터 성공적인 HTTP 200 응답을 받지 못하거나 오류가 있는 경우 Kubernetes의 Event Grid는 고정된 재시도 일정 및 재시도 정책에 따라 전송을 다시 시도합니다. 

기본적으로 Kubernetes의 Event Grid는 구독자에게 한 번에 하나의 이벤트를 전달합니다. 그러나 전달 요청의 페이로드는 단일 이벤트가 있는 배열입니다. 출력 일괄 처리 기능을 활성화하면 한 번에 둘 이상의 이벤트를 전달할 수 있습니다. 이 기능에 대한 자세한 내용은 [Batch 이벤트 전달](batch-event-delivery.md)을 참조하세요.

[!INCLUDE [event-grid-preview-feature-note.md](../includes/event-grid-preview-feature-note.md)]

> [!NOTE]
> 미리 보기 중에 Kubernetes의 Event Grid 기능은 API 버전 [2020-10-15-Preview](/rest/api/eventgrid/version2021-06-01-preview/event-subscriptions/create-or-update)를 통해 지원됩니다. 


## <a name="retry-schedule"></a>다시 시도 일정
Kubernetes의 Event Grid는 이벤트를 전달한 후 최대 60초 동안 응답을 대기합니다. 구독자의 엔드포인트가 성공 응답(HTTP 200 등)을 보내지 않는 경우 이벤트 보내기를 다시 시도합니다. 작동 방식은 다음과 같습니다. 

1. 메시지가 Kubernetes의 Event Grid에 도달합니다. 이 메시지를 즉시 전송하려고 시도합니다.
1. 전송이 실패하면 메시지를 1분 큐에 넣고 1분 후에 전송을 다시 시도합니다.
1. 전송이 계속 실패하면 메시지를 10분 큐에 넣고 10분마다 전송을 다시 시도합니다.
1. 전송이 성공하거나 재시도 정책 한도에 도달할 때까지 전송을 시도합니다.
 
## <a name="retry-policy"></a>재시도 정책
재시도 정책을 결정하는 구성은 두 가지가 있습니다. 아래에 이 계정과 키의 예제가 나와 있습니다.

- 최대 시도 횟수
- 이벤트 TTL(Time to Live)

재시도 정책 한도 중 하나에 도달하면 이벤트가 삭제됩니다. 이러한 제한의 구성은 구독 기준으로 수행됩니다. 다음 섹션에서 각 항목에 대해 자세히 설명합니다.

### <a name="configuring-defaults-per-subscriber"></a>구독자별 기본값 구성
개별 구독에 대해 재시도 정책 한도를 지정할 수도 있습니다. 구독자별 기본값 구성 방법에 대한 정보는 [API 설명서](/rest/api/eventgrid/version2021-06-01-preview/event-subscriptions/create-or-update)를 참조하세요. 구독 수준 기본값은 Kubernetes 수준 구성에서 Event Grid 모듈을 재정의합니다.

다음 예제에서는 `maxNumberOfAttempts`는 3분, `eventTimeToLiveInMinutes`는 30분을 사용하여 웹후크 구독을 설정합니다.

```json
{
 "properties": {
  "destination": {
   "endpointType": "WebHook",
   "properties": {
    "endpointUrl": "<your_webhook_url>",
    "eventDeliverySchema": " CloudEventSchemaV1_0"
   }
  },
  "retryPolicy": {
   "eventTimeToLiveInMinutes": 30,
   "maxDeliveryAttempts": 3
  }
 }
```

## <a name="next-steps"></a>다음 단계
Kubernetes용 Azure Arc의 Event Grid에서 지원하는 대상 및 처리기에 대해 알아보려면 [Kubernetes의 Event Grid - 이벤트 처리기](event-handlers.md)를 참조하세요.
