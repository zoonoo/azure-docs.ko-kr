---
title: Azure Event Grid - 구독 유효성 검사 문제 해결
description: 이 문서에서는 구독 유효성 검사 문제를 해결하는 방법을 보여줍니다.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 857760182675d5673a3b09495c2faaf7372a4164
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/13/2020
ms.locfileid: "94592943"
---
# <a name="troubleshoot-azure-event-grid-subscription-validations"></a>Azure Event Grid 구독 유효성 검사 문제 해결
이벤트 구독을 만드는 동안와 같은 오류 메시지가 표시 되는 경우 `The attempt to validate the provided endpoint https://your-endpoint-here failed. For more details, visit https://aka.ms/esvalidation` 유효성 검사 핸드셰이크에 오류가 있음을 나타냅니다. 이 오류를 해결하려면 다음과 같은 측면을 확인합니다.

- Postman 또는 말아와 유사한 도구를 사용 하 여 [샘플 SubscriptionValidationEvent](webhook-event-delivery.md#validation-details) 요청 본문으로 웹 후크 url에 대 한 HTTP POST를 수행 합니다.
- Webhook에서 동기 유효성 검사 핸드셰이크 메커니즘을 구현 하는 경우 ValidationCode가 응답의 일부로 반환 되는지 확인 합니다.
- 웹 webhook가 비동기 유효성 검사 핸드셰이크 메커니즘을 구현 하는 경우 HTTP POST가 200 OK를 반환 하는지 확인 합니다.
- 웹 후크에 응답을 반환 하는 경우 `403 (Forbidden)` webhook가 Azure 애플리케이션 게이트웨이 또는 웹 응용 프로그램 방화벽 뒤에 있는지 확인 합니다. 그렇다면 이러한 방화벽 규칙을 사용 하지 않도록 설정 하 고 HTTP POST를 다시 수행 해야 합니다.
    - 920300 (수락 헤더가 누락 된 요청)
    - 942430 (제한 된 SQL 문자 변칙 검색 (args): 특수 문자의 # 개 초과 (12))
    - 920230 (여러 URL 인코딩이 검색 됨)
    - 942130 (SQL 삽입 공격: SQL tautology이 검색 되었습니다.)
    - 931130 (가능한 원격 파일 포함 (RFI) 공격 = 오프-도메인 참조/링크)

> [!IMPORTANT]
> 웹후크의 엔드포인트 유효성 검사에 대한 자세한 내용은 [웹후크 이벤트 전달](webhook-event-delivery.md)을 참조하세요.

다음 섹션에서는 Postman 및 말아를 사용 하 여 이벤트 구독의 유효성을 검사 하는 방법을 보여 줍니다.  

## <a name="validate-event-grid-event-subscription-using-postman"></a>Postman을 사용하여 Event Grid 이벤트 구독 유효성 검사
다음은 Event Grid 이벤트의 웹후크 구독 유효성을 검사하기 위해 Postman을 사용하는 예입니다. 

![Postman을 사용하여 이벤트 그리드 이벤트 구독 유효성 검사](./media/troubleshoot-subscription-validation/event-subscription-validation-postman.png)

다음은 샘플 **SubscriptionValidationEvent** JSON입니다.

```json
[
  {
    "id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66",
    "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "subject": "",
    "data": {
      "validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6",
    },
    "eventType": "Microsoft.EventGrid.SubscriptionValidationEvent",
    "eventTime": "2018-01-25T22:12:19.4556811Z",
    "metadataVersion": "1",
    "dataVersion": "1"
  }
]
```

성공적인 응답 예제는 다음과 같습니다.

```json
{
  "validationResponse": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
}
```

웹후크의 Event Grid 이벤트 유효성 검사에 대한 자세한 내용은 [Event Grid 이벤트를 사용하여 엔드포인트 유효성 검사](webhook-event-delivery.md#endpoint-validation-with-event-grid-events)를 참조하세요.


## <a name="validate-event-grid-event-subscription-using-curl"></a>Curl을 사용하여 Event Grid 이벤트 구독 유효성 검사 
다음은 Event Grid 이벤트의 웹후크 구독 유효성을 검사하기 위한 샘플 Curl 명령입니다. 

```bash
curl -X POST -d '[{"id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66","topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx","subject": "","data": {"validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"},"eventType": "Microsoft.EventGrid.SubscriptionValidationEvent","eventTime": "2018-01-25T22:12:19.4556811Z", "metadataVersion": "1","dataVersion": "1"}]' -H 'Content-Type: application/json' https://{your-webhook-url.com}
```

## <a name="validate-cloud-event-subscription-using-postman"></a>Postman을 사용하여 클라우드 이벤트 구독 유효성 검사
다음은 클라우드 이벤트의 웹후크 구독 유효성을 검사하기 위해 Postman을 사용하는 예입니다. 

![Postman을 사용하여 클라우드 이벤트 구독 유효성 검사](./media/troubleshoot-subscription-validation/cloud-event-subscription-validation-postman.png)

클라우드 이벤트를 통한 유효성 검사에 **HTTP OPTIONS** 메서드를 사용합니다. 웹후크의 클라우드 이벤트 유효성 검사에 대한 자세한 내용은 [클라우드 이벤트를 사용하여 엔드포인트 유효성 검사](webhook-event-delivery.md#endpoint-validation-with-event-grid-events)를 참조하세요.

## <a name="troubleshoot-event-subscription-validation"></a>이벤트 구독 유효성 검사 문제 해결

## <a name="next-steps"></a>다음 단계
도움이 더 필요한 경우 [Stack Overflow 포럼](https://stackoverflow.com/questions/tagged/azure-eventgrid)에 문제를 게시하거나 [지원 티켓](https://azure.microsoft.com/support/options/)을 여세요. 
