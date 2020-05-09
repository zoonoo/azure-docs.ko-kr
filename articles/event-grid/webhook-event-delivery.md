---
title: WebHook 이벤트 전달
description: 이 문서에서는 웹 후크를 사용할 때 WebHook 이벤트 전달 및 끝점 유효성 검사에 대해 설명 합니다.
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 03/06/2020
ms.author: babanisa
ms.openlocfilehash: 7ae8a21d4ea9216bea13d47ad5ae41f3bc1c2089
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82630175"
---
# <a name="webhook-event-delivery"></a>웹 후크 이벤트 전달
웹후크는 Azure Event Grid에서 이벤트를 수신하는 여러 가지 방법 중 하나입니다. 새 이벤트가 준비되면 Event Grid 서비스는 요청 본문에 이벤트가 포함되어 구성된 엔드포인트로 HTTP 요청을 게시합니다.

웹후크를 지원하는 여러 다른 서비스와 마찬가지로, Event Grid를 사용하려면 해당 엔드포인트로 이벤트 제공을 시작하기 전에 웹후크 엔드포인트에 대한 소유권을 증명해야 합니다. 이 요구 사항으로 인해 악의적인 사용자가 엔드포인트에서 과도한 이벤트를 발생하는 일이 방지됩니다. 아래 나열된 세 가지 Azure 서비스를 사용하는 경우 Azure 인프라는 자동으로 이 유효성 검사를 처리합니다.

- [Event Grid 커넥터](https://docs.microsoft.com/connectors/azureeventgrid/)를 포함하는 Azure Logic Apps
- [웹후크](../event-grid/ensure-tags-exists-on-new-virtual-machines.md)를 통한 Azure Automation
- [Event Grid Trigger](../azure-functions/functions-bindings-event-grid.md)를 포함하는 Azure Functions

## <a name="endpoint-validation-with-event-grid-events"></a>Event Grid 이벤트를 사용 하 여 끝점 유효성 검사
HTTP 트리거 기반 Azure 함수와 같은 엔드포인트의 다른 형식을 사용하는 경우, 엔드포인트 코드가 Event Grid를 통해 핸드셰이크 유효성 검사에 참여해야 합니다. Event Grid는 두 가지 방법의 구독 유효성 검사를 지원합니다.

1. **동기 핸드셰이크**: 이벤트 구독을 만들 때 Event Grid 구독 유효성 검사 이벤트를 끝점으로 보냅니다. 이 이벤트의 스키마는 다른 Event Grid 이벤트와 비슷합니다. 이 이벤트의 데이터 부분에는 `validationCode` 속성이 포함됩니다. 응용 프로그램은 유효성 검사 요청이 예상 이벤트 구독에 대 한 것 인지 확인 하 고 응답의 유효성 검사 코드를 동기적으로 반환 합니다. 이 핸드셰이크 메커니즘은 모든 Event Grid 버전에서 지원됩니다.

2. **비동기 핸드셰이크**: 특정 한 경우에는 응답에서 validationcode를 동기적으로 반환할 수 없습니다. 예를 들어 타사 서비스 (예: [`Zapier`](https://zapier.com) 또는 [IFTTT](https://ifttt.com/))를 사용 하는 경우 유효성 검사 코드를 프로그래밍 방식으로 응답할 수 없습니다.

   버전 2018-05-01-미리 보기부터 Event Grid는 수동 유효성 검사 핸드셰이크를 지원합니다. API 버전 2018-05-01-미리 보기 이상을 사용하는 SDK 또는 도구에서 이벤트 구독을 만드는 경우 Event Grid는 구독 유효성 검사 이벤트의 데이터 부분에 `validationUrl` 속성을 전송합니다. 핸드셰이크를 완료 하려면 이벤트 데이터에서 해당 URL을 찾아 GET 요청을 수행 합니다. REST 클라이언트 또는 웹 브라우저를 사용할 수 있습니다.

   제공 된 URL은 **5 분**동안 유효 합니다. 이 시간 동안 이벤트 구독의 프로비전 상태가 `AwaitingManualAction`입니다. 5 분 내에 수동 유효성 검사를 완료 하지 않으면 프로 비전 상태가로 `Failed`설정 됩니다. 수동 유효성 검사를 시작하기 전에 이벤트 구독을 다시 작성해야 합니다.

   또한이 인증 메커니즘을 사용 하려면 웹 후크 끝점에서 HTTP 상태 코드 200을 반환 하 여 유효성 검사 이벤트의 게시물이 수동 유효성 검사 모드에 배치 되기 전에 승인 되었음을 알 수 있습니다. 즉, 끝점이 200을 반환 하지만 유효성 검사 응답을 동기적으로 반환 하지 않는 경우 모드가 수동 유효성 검사 모드로 전환 됩니다. 5 분 이내에 유효성 검사 URL에 대 한 GET이 있으면 유효성 검사 핸드셰이크가 성공한 것으로 간주 됩니다.

> [!NOTE]
> 유효성 검사에 자체 서명 된 인증서를 사용 하는 것은 지원 되지 않습니다. 대신 CA (인증 기관)에서 서명 된 인증서를 사용 합니다.

### <a name="validation-details"></a>유효성 검사 세부 정보

- 이벤트 구독 생성/업데이트 시 Event Grid는 대상 엔드포인트에 구독 유효성 검사 이벤트를 게시합니다.
- 이벤트에는 “aeg-event-type: SubscriptionValidation” 헤더 값이 포함됩니다.
- 이벤트 본문에는 다른 Event Grid 이벤트와 동일한 스키마가 있습니다.
- 이벤트의 eventType 속성은 `Microsoft.EventGrid.SubscriptionValidationEvent`입니다.
- 이벤트의 데이터 속성에는 임의로 생성된 문자열을 포함한 `validationCode` 속성이 있습니다. 예를 들어 “validationCode: acb13...”과 같습니다.
- 이벤트 데이터는 구독에 대해 수동으로 유효성 검사를 수행하기 위해 URL에 `validationUrl` 속성도 포함되어 있습니다.
- 배열에는 유효성 검사 이벤트만 포함됩니다. 다른 이벤트는 유효성 검사 코드를 에코 백한 후 별도의 요청으로 전송됩니다.
- EventGrid DataPlane SDK에는 구독 유효성 검사 이벤트 데이터 및 구독 유효성 검사 응답에 해당하는 클래스가 있습니다.

SubscriptionValidationEvent 예가 다음 예제에 나와 있습니다.

```json
[
  {
    "id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66",
    "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "subject": "",
    "data": {
      "validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6",
      "validationUrl": "https://rp-eastus2.eventgrid.azure.net:553/eventsubscriptions/estest/validate?id=512d38b6-c7b8-40c8-89fe-f46f9e9622b6&t=2018-04-26T20:30:54.4538837Z&apiVersion=2018-05-01-preview&token=1A1A1A1A"
    },
    "eventType": "Microsoft.EventGrid.SubscriptionValidationEvent",
    "eventTime": "2018-01-25T22:12:19.4556811Z",
    "metadataVersion": "1",
    "dataVersion": "1"
  }
]
```

엔드포인트 소유권을 증명하기 위해 다음 예제와 같이 validationResponse 속성의 유효성 검사 코드를 에코 백합니다.

```json
{
  "validationResponse": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
}
```

HTTP 200 정상 응답 상태 코드를 반환해야 합니다. HTTP 202 수락은 유효한 Event Grid 구독 유효성 검사 응답으로 인식되지 않습니다. Http 요청은 30 초 내에 완료 되어야 합니다. 작업이 30 초 내에 완료 되지 않으면 작업이 취소 되 고 5 초 후에 다시 시도 수 있습니다. 모든 시도가 실패 하면 유효성 검사 핸드셰이크 오류로 처리 됩니다.

또는 유효성 검사 URL에 GET 요청을 수동으로 전송하여 구독이 유효한지 수동으로 검사할 수 있습니다. 이벤트 구독은 유효성을 검사할 때까지 보류 상태로 유지됩니다. 유효성 검사 Url은 포트 553를 사용 합니다. 방화벽 규칙이 포트 553를 차단할 경우 성공적인 수동 핸드셰이크를 위해 규칙을 업데이트 해야 할 수 있습니다.

구독 유효성 검사 핸드셰이크 처리 예제를 보려면 [C# 샘플](https://github.com/Azure-Samples/event-grid-dotnet-publish-consume-events/blob/master/EventGridConsumer/EventGridConsumer/Function1.cs)을 참조하세요.

## <a name="endpoint-validation-with-cloudevents-v10"></a>CloudEvents v 1.0을 사용 하는 끝점 유효성 검사
Event Grid에 대해 잘 알고 있는 경우 남용 방지를 위한 Event Grid의 끝점 유효성 검사 핸드셰이크를 알고 있을 수 있습니다. CloudEvents v 1.0은 HTTP OPTIONS 메서드를 사용 하 여 자체 [불건전 보호 의미 체계](webhook-event-delivery.md) 를 구현 합니다. [여기](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#4-abuse-protection)에서 자세한 내용을 확인할 수 있습니다. 출력에 CloudEvents 스키마를 사용 하는 경우 Event Grid는 Event Grid 유효성 검사 이벤트 메커니즘 대신 CloudEvents v1.0 남용 보호와 함께를 사용 합니다.

## <a name="next-steps"></a>다음 단계
이벤트 구독 유효성 검사 문제를 해결 하는 방법에 대해 알아보려면 다음 문서를 참조 하세요. 

[이벤트 구독 유효성 검사 문제 해결](troubleshoot-subscription-validation.md)
