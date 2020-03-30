---
title: 지속성 함수의 인간 상호 작용 및 시간 제한 - Azure
description: Azure Functions의 지속성 함수 확장에서 인간 상호 작용 및 시간 제한을 처리하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 4e0f71369bc02fdce5625d9c74e1d52264ed86be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335756"
---
# <a name="human-interaction-in-durable-functions---phone-verification-sample"></a>지속성 함수의 인간 상호 작용 - 전화 확인 샘플

이 샘플에서는 인간 상호 작용이 포함된 [지속성 함수](durable-functions-overview.md) 오케스트레이션을 작성하는 방법을 보여 줍니다. 실제 사람이 자동화된 프로세스에 참여할 때마다 프로세스에서 당사자에게 알림을 보내고 응답을 비동기적으로 받을 수 있어야 합니다. 사람이 사용할 수 없는 가능성도 허용해야 합니다. (이 마지막 부분은 시간 제한이 중요한 부분입니다.)

이 샘플에서는 SMS 기반 전화 확인 시스템을 구현합니다. 이러한 유형의 흐름은 고객의 전화 번호를 확인할 때 또는 MFA(다단계 인증)를 위해 자주 사용됩니다. 전체 구현은 몇 가지 작은 함수를 사용하여 수행되기 때문에 강력한 예입니다. 데이터베이스와 같은 외부 데이터 저장소는 필요하지 않습니다.

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="scenario-overview"></a>시나리오 개요

전화 확인은 애플리케이션의 최종 사용자가 스패머가 아니고 자신이 언급되는 당사자인지 확인하는 데 사용됩니다. 다단계 인증은 해커로부터 사용자 계정을 보호하기 위한 일반적인 사용 사례입니다. 사용자 고유의 전화 확인을 구현하는 챌린지에는 사람과의 **상태 저장 상호 작용**이 필요하다는 것입니다. 일반적으로 최종 사용자에게는 일부 코드(예: 4자리 숫자)가 제공되며 **적절한 시간 내에** 응답해야 합니다.

일반적인 Azure Functions는 다른 플랫폼의 다른 많은 클라우드 엔드포인트와 마찬가지로 상태 비저장이므로, 이러한 유형의 상호 작용에는 데이터베이스 또는 일부 다른 영구 저장소에서 외부적으로 상태를 관리하는 작업이 명시적으로 포함됩니다. 또한 상호 작용은 함께 조정될 수 있는 여러 함수로 분할되어야 합니다. 예를 들어 코드를 결정하고, 어딘가에 유지하고, 사용자의 전화로 보내는 함수가 하나 이상 필요합니다. 또한 사용자로부터 응답을 받기 위해 다른 함수가 하나 이상 필요하며, 코드 유효성 검사를 수행하기 위해 어떤 방식으로든 원래 함수 호출에 다시 매핑해야 합니다. 시간 제한은 보안을 유지하는 중요한 요소이기도 하지만, 그것은 매우 복잡 신속 하 게 얻을 수 있습니다.

지속성 함수를 사용하면 이 시나리오의 복잡성이 크게 줄어듭니다. 이 샘플에서 볼 수 있듯이 오케스트레이터 함수는 외부 데이터 저장소를 포함하지 않고 상태 저장 상호 작용을 쉽게 관리할 수 있습니다. 오케스트레이터 함수는 *지속적*이므로 이러한 대화형 흐름도 매우 안정적입니다.

## <a name="configuring-twilio-integration"></a>Twilio 통합 구성

[!INCLUDE [functions-twilio-integration](../../../includes/functions-twilio-integration.md)]

## <a name="the-functions"></a>함수

이 문서에서는 샘플 앱의 다음 함수에 대해 설명합니다.

* `E4_SmsPhoneVerification`: 시간 시간 및 재시도 관리를 포함하여 전화 확인 프로세스를 수행하는 [오케스트레이터 기능입니다.](durable-functions-bindings.md#orchestration-trigger)
* `E4_SendSmsChallenge`: 문자 메시지를 통해 코드를 전송하는 [활동 기능입니다.](durable-functions-bindings.md#activity-trigger)

### <a name="e4_smsphoneverification-orchestrator-function"></a>E4_SmsPhoneVerification 오케스트레이터 기능

# <a name="c"></a>[C #](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/PhoneVerification.cs?range=17-70)]

> [!NOTE]
> 처음에는 명확하지 않을 수도 있지만 이 오케스트레이터 함수는 완전히 결정적입니다. `CurrentUtcDateTime` 속성은 타이머 만료 시간을 계산 하는 데 사용 됩니다 및 오케스트레이터 코드의이 시점에서 모든 재생에 동일한 값을 반환 하기 때문에 결정적이 다. 이 동작은 `winner` `Task.WhenAny`에 대한 모든 반복 호출에서 동일한 결과가 있는지 확인하는 데 중요합니다.

# <a name="javascript"></a>[자바 스크립트](#tab/javascript)

**E4_SmsPhoneVerification** 함수는 오케스트레이터 함수에 표준 *function.json*을 사용합니다.

[!code-json[Main](~/samples-durable-functions/samples/javascript/E4_SmsPhoneVerification/function.json)]

다음은 이 함수를 구현하는 코드입니다.

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E4_SmsPhoneVerification/index.js)]

> [!NOTE]
> 처음에는 명확하지 않을 수도 있지만 이 오케스트레이터 함수는 완전히 결정적입니다. `currentUtcDateTime` 속성은 타이머 만료 시간을 계산 하는 데 사용 됩니다 및 오케스트레이터 코드의이 시점에서 모든 재생에 동일한 값을 반환 하기 때문에 결정적이 다. 이 동작은 `winner` `context.df.Task.any`에 대한 모든 반복 호출에서 동일한 결과가 있는지 확인하는 데 중요합니다.

---

시작되면 이 오케스트레이터 함수에서 다음을 수행합니다.

1. SMS 알림을 *보낼* 전화 번호를 가져옵니다.
2. **E4_SendSmsChallenge**를 호출하여 사용자에게 SMS 메시지를 보내고 예상되는 4자리 챌린지 코드를 다시 반환합니다.
3. 현재 시간에서 90초를 트리거하는 지속성 타이머를 만듭니다.
4. 타이머와 병행하여 사용자로부터 **SmsChallengeResponse** 이벤트를 기다립니다.

사용자는 4자리 코드가 있는 SMS 메시지를 받습니다. 동일한 4자리 코드를 오케스트레이터 함수 인스턴스로 다시 보내 확인 프로세스를 완료하는 데 90초가 있습니다. 잘못된 코드를 제출하면 동일한 90초 간격으로 3번의 시도를 추가로 얻을 수 있습니다.

> [!WARNING]
> 챌린지 응답이 수락되고 타이머가 더 이상 만료될 필요가 없으면 위의 예제와 같이 [타이머를 취소](durable-functions-timers.md)하는 것이 중요합니다.

## <a name="e4_sendsmschallenge-activity-function"></a>E4_SendSmsChallenge 활동 기능

**E4_SendSmsChallenge** 함수는 Twilio 바인딩을 사용하여 4자리 코드가 있는 SMS 메시지를 최종 사용자에게 보냅니다.

# <a name="c"></a>[C #](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/PhoneVerification.cs?range=72-89)]

> [!NOTE]
> 샘플 코드를 실행하려면 `Microsoft.Azure.WebJobs.Extensions.Twilio` Nuget 패키지를 설치해야 합니다.

# <a name="javascript"></a>[자바 스크립트](#tab/javascript)

*function.json*은 다음과 같이 정의됩니다.

[!code-json[Main](~/samples-durable-functions/samples/javascript/E4_SendSmsChallenge/function.json)]

다음은 4자리 챌린지 코드를 생성하고 SMS 메시지를 보내는 코드입니다.

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E4_SendSmsChallenge/index.js)]

---

## <a name="run-the-sample"></a>샘플 실행

샘플에 포함된 HTTP 트리거 함수를 사용하여 다음 HTTP POST 요청을 전송함으로써 오케스트레이션을 시작할 수 있습니다.

```
POST http://{host}/orchestrators/E4_SmsPhoneVerification
Content-Length: 14
Content-Type: application/json

"+1425XXXXXXX"
```

```
HTTP/1.1 202 Accepted
Content-Length: 695
Content-Type: application/json; charset=utf-8
Location: http://{host}/runtime/webhooks/durabletask/instances/741c65651d4c40cea29acdd5bb47baf1?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

{"id":"741c65651d4c40cea29acdd5bb47baf1","statusQueryGetUri":"http://{host}/runtime/webhooks/durabletask/instances/741c65651d4c40cea29acdd5bb47baf1?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}","sendEventPostUri":"http://{host}/runtime/webhooks/durabletask/instances/741c65651d4c40cea29acdd5bb47baf1/raiseEvent/{eventName}?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}","terminatePostUri":"http://{host}/runtime/webhooks/durabletask/instances/741c65651d4c40cea29acdd5bb47baf1/terminate?reason={text}&taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}"}
```

오케스트레이터 함수는 제공된 전화 번호를 받는 즉시 임의로 생성된 4자리 확인 코드&mdash;(예: *2168*)가 있는 SMS 메시지를 보냅니다. 그런 다음 이 함수는 90초 동안 응답을 기다립니다.

코드로 회신하려면 다른 함수 내에서 [ `RaiseEventAsync` `raiseEvent` (.NET) 또는 (JavaScript)를](durable-functions-instance-management.md) 사용하거나 위의 202 응답에서 참조 된 **sendEventUrl** HTTP POST 웹 `SmsChallengeResponse`후크를 호출하여 이벤트 이름으로 대체 `{eventName}` 할 수 있습니다.

```
POST http://{host}/runtime/webhooks/durabletask/instances/741c65651d4c40cea29acdd5bb47baf1/raiseEvent/SmsChallengeResponse?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
Content-Length: 4
Content-Type: application/json

2168
```

타이머가 만료되기 전에 보내면 오케스트레이션이 완료되고 `output` 필드가 `true`로 설정되어 성공적인 확인을 나타냅니다.

```
GET http://{host}/runtime/webhooks/durabletask/instances/741c65651d4c40cea29acdd5bb47baf1?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
```

```
HTTP/1.1 200 OK
Content-Length: 144
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":"+1425XXXXXXX","output":true,"createdTime":"2017-06-29T19:10:49Z","lastUpdatedTime":"2017-06-29T19:12:23Z"}
```

타이머가 만료되도록 하거나 잘못된 코드를 4번 입력하면 상태를 쿼리하고 전화 확인에 실패했음을 나타내는 `false` 오케스트레이션 함수 출력을 확인할 수 있습니다.

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
Content-Length: 145

{"runtimeStatus":"Completed","input":"+1425XXXXXXX","output":false,"createdTime":"2017-06-29T19:20:49Z","lastUpdatedTime":"2017-06-29T19:22:23Z"}
```

## <a name="next-steps"></a>다음 단계

이 샘플에서는 내구성 있는 기능, 특히 `WaitForExternalEvent` `CreateTimer` API의 고급 기능 중 일부를 보여 주어 있습니다. 이러한 기능을 `Task.WaitAny`와 결합하여 신뢰할 수 있는 시간 제한 시스템을 구현하는 방법을 살펴보았습니다. 이 시스템은 종종 실제 사람들과 상호 작용하는 데 유용합니다. 특정 항목을 자세히 다루는 일련의 문서를 읽고 지속성 함수를 사용하는 방법에 대해 자세히 배울 수 있습니다.

> [!div class="nextstepaction"]
> [시리즈의 첫 번째 문서로 이동](durable-functions-bindings.md)
