---
title: Durable Functions의 모니터 (Python)-Azure
description: Azure Functions (Python)의 Durable Functions 확장을 사용 하 여 상태 모니터를 구현 하는 방법에 대해 알아봅니다.
author: davidmrdavid
ms.topic: conceptual
ms.date: 12/02/2020
ms.author: azfuncdf
ms.openlocfilehash: 62b3c9bb1c6fd53d9f11227a9d7e774d56859d04
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/07/2021
ms.locfileid: "102434766"
---
# <a name="monitor-scenario-in-durable-functions---github-issue-monitoring-sample"></a>Durable Functions에서 시나리오 모니터링-GitHub 문제 모니터링 샘플

모니터링 패턴은 워크플로의 유연한 되풀이(예: 특정 조건이 충족될 때까지 폴링) 프로세스를 말합니다. 이 문서에서는 지속성 함수를 사용하여 모니터링을 구현하는 샘플에 대해 설명합니다.

## <a name="prerequisites"></a>필수 구성 요소

* [빠른 시작 문서 완료](quickstart-python-vscode.md)
* [GitHub에서 샘플 프로젝트 복제 또는 다운로드](https://github.com/Azure/azure-functions-durable-python/tree/main/samples/)


## <a name="scenario-overview"></a>시나리오 개요

이 샘플은 GitHub 리포지토리의 문제 수를 모니터링 하 고 3 개 이상의 미해결 문제가 있는 경우 사용자에 게 알립니다. 정기적인 타이머 트리거 함수를 사용 하 여 정기적인 간격으로 열린 문제 수를 요청할 수 있습니다. 단, 이러한 방식의 한 가지 문제점은 **수명 관리** 입니다. 경고가 하나만 전송 되어야 하는 경우 3 개 이상의 문제가 검색 된 후 모니터를 사용 하지 않도록 설정 해야 합니다. 모니터링 패턴은 다른 이점과 함께 자체 실행을 종료 할 수 있습니다.

* 모니터는 간격을 두고 실행되며 일정에 따라 실행되는 것이 아닙니다. 타이머 트리거는 1시간마다 실행(*run*)되고 모니터는 작업 사이에 한 시간을 대기합니다(*wait*). 모니터의 작업은 지정되지 않는 한 겹치지 않습니다. 이는 장기 실행되는 작업에 중요합니다.
* 모니터에 동적 간격을 설정할 수 있으며, 대기 시간은 조건에 따라 변경될 수 있습니다.
* 모니터는 조건이 충족되면 종료되거나 다른 프로세스에 의해 종료될 수 있습니다.
* 모니터에 매개 변수를 사용할 수 있습니다. 이 샘플에서는 요청 된 모든 공용 GitHub 리포지토리 및 전화 번호에 동일한 리포지토리 모니터링 프로세스를 적용 하는 방법을 보여 줍니다.
* 모니터는 확장성이 있습니다. 각 모니터는 오케스트레이션 인스턴스이기 때문에 새 함수를 만들거나 코드를 더 정의하지 않고도 다수의 모니터를 만들 수 있습니다.
* 모니터는 보다 큰 워크플로에 쉽게 통합됩니다. 모니터는 더 복잡한 오케스트레이션 함수 또는 [ 하위 오케스트레이션](durable-functions-sub-orchestrations.md)의 한 섹션이 될 수 있습니다.

## <a name="configuration"></a>구성

### <a name="configuring-twilio-integration"></a>Twilio 통합 구성

[!INCLUDE [functions-twilio-integration](../../../includes/functions-twilio-integration.md)]

## <a name="the-functions"></a>함수

이 문서에서는 샘플 앱의 다음 함수에 대해 설명합니다.

* `E3_Monitor`: 정기적으로를 호출 하는 오 케 스트레이 터 [함수](durable-functions-bindings.md#orchestration-trigger) 입니다 `E3_TooManyOpenIssues` . `E3_SendAlert`의 반환 값이 인 경우를 `E3_TooManyOpenIssues` 호출 `True` 합니다.
* `E3_TooManyOpenIssues`: 리포지토리에 열린 문제가 너무 많이 있는지 확인 하는 [작업 함수](durable-functions-bindings.md#activity-trigger) 입니다. Demoing의 경우 세 개 이상의 미해결 문제가 너무 많은 것으로 간주 하는 것이 좋습니다.
* `E3_SendAlert`: Twilio를 통해 SMS 메시지를 보내는 작업 함수입니다.

### <a name="e3_monitor-orchestrator-function"></a>E3_Monitor orchestrator 함수


**E3_Monitor** 함수는 오케스트레이터 함수에 표준 *function.json* 을 사용합니다.

[!code-json[Main](~/samples-durable-functions-python/samples/monitor/E3_Monitor/function.json)]

다음은 이 함수를 구현하는 코드입니다.

[!code-python[Main](~/samples-durable-functions-python/samples/monitor/E3_Monitor/\_\_init\_\_.py)]


이 오케스트레이터 함수는 다음 작업을 수행합니다.

1. 모니터링할 *리포지토리* 및 SMS 알림을 보낼 *전화 번호* 를 가져옵니다.
2. 모니터의 만료 시간을 결정합니다. 간결함을 위해 이 샘플에서는 하드 코드된 값을 사용합니다.
3. **E3_TooManyOpenIssues** 를 호출 하 여 요청 된 리포지토리에 미해결 문제가 너무 많이 있는지 여부를 확인 합니다.
4. 문제가 너무 많은 경우 **E3_SendAlert** 를 호출 하 여 SMS 알림을 요청한 전화 번호로 보냅니다.
5. 다음 폴링 간격에서 오케스트레이션을 다시 시작하도록 지속성 타이머를 만듭니다. 간결함을 위해 이 샘플에서는 하드 코드된 값을 사용합니다.
6. 현재 UTC 시간이 모니터의 만료 시간을 경과 하거나 SMS 경고가 전송 될 때까지 계속 실행 됩니다.

여러 orchestrator 인스턴스는 오 케 스트레이 터 함수를 여러 번 호출 하 여 동시에 실행할 수 있습니다. 모니터링할 리포지토리 및 SMS 경고를 보낼 전화 번호를 지정할 수 있습니다. 마지막으로 타이머를 대기 하는 동안 오 케 스트레이 터 함수가 실행 되 고 *있지* 않으므로 요금이 청구 되지 않습니다.


### <a name="e3_toomanyopenissues-activity-function"></a>E3_TooManyOpenIssues activity 함수

다른 샘플과 마찬가지로 도우미 작업 함수는 `activityTrigger` 트리거 바인딩을 사용하는 일반 함수입니다. **E3_TooManyOpenIssues** 함수는 리포지토리에 대 한 현재 미해결 문제 목록을 가져오고, 샘플에 따라 3 개 이상으로 "너무 많은"이 있는지 확인 합니다.


*function.json* 은 다음과 같이 정의됩니다.

[!code-json[Main](~/samples-durable-functions-python/samples/monitor/E3_TooManyOpenIssues/function.json)]

그리고 구현은 다음과 같습니다.

[!code-python[Main](~/samples-durable-functions-python/samples/monitor/E3_TooManyOpenIssues/\_\_init\_\_.py)]


### <a name="e3_sendalert-activity-function"></a>E3_SendAlert activity 함수

**E3_SendAlert** 함수는 Twilio binding을 사용 하 여 최종 사용자에 게 해결을 대기 하는 세 개 이상의 미해결 문제가 있음을 알리는 SMS 메시지를 보냅니다.


*function.json* 은 간단합니다.

[!code-json[Main](~/samples-durable-functions-python/samples/monitor/E3_TooManyOpenIssues/function.json)]

SMS 메시지를 보내는 코드는 다음과 같습니다.

[!code-python[Main](~/samples-durable-functions-python/samples/monitor/E3_SendAlert/\_\_init\_\_.py)]


## <a name="run-the-sample"></a>샘플 실행

[GitHub](https://github.com/) 계정이 필요 합니다. 이를 사용 하 여 문제를 열 수 있는 임시 공용 리포지토리를 만듭니다.

샘플에 포함된 HTTP 트리거 함수를 사용하여 다음 HTTP POST 요청을 전송함으로써 오케스트레이션을 시작할 수 있습니다.

```
POST https://{host}/orchestrators/E3_Monitor
Content-Length: 77
Content-Type: application/json

{ "repo": "<your github handle>/<a new github repo under your user>", "phone": "+1425XXXXXXX" }
```

예를 들어 GitHub 사용자 이름이이 `foo` 고 리포지토리가 인 경우 `bar` 값은 `"repo"` 이어야 `"foo/bar"` 합니다.

```
HTTP/1.1 202 Accepted
Content-Type: application/json; charset=utf-8
Location: https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635?taskHub=SampleHubVS&connection=Storage&code={SystemKey}
RetryAfter: 10

{"id": "f6893f25acf64df2ab53a35c09d52635", "statusQueryGetUri": "https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635?taskHub=SampleHubVS&connection=Storage&code={systemKey}", "sendEventPostUri": "https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635/raiseEvent/{eventName}?taskHub=SampleHubVS&connection=Storage&code={systemKey}", "terminatePostUri": "https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635/terminate?reason={text}&taskHub=SampleHubVS&connection=Storage&code={systemKey}"}
```

**E3_Monitor** 인스턴스는 제공 된 리포지토리에 열린 문제를 시작 하 고 쿼리 합니다. 세 개 이상의 미해결 문제가 발견 되 면 작업 함수를 호출 하 여 경고를 보냅니다. 그렇지 않으면 타이머를 설정 합니다. 타이머가 만료되면 오케스트레이션이 다시 시작됩니다.

Azure Functions 포털의 함수 로그를 검토하여 오케스트레이션의 작업을 볼 수 있습니다.

```
[2020-12-04T18:24:30.007Z] Executing 'Functions.HttpStart' (Reason='This function was programmatically 
called via the host APIs.', Id=93772f6b-f4f0-405a-9d7b-be9eb7a38aa6)
[2020-12-04T18:24:30.769Z] Executing 'Functions.E3_Monitor' (Reason='(null)', Id=058e656e-bcb1-418c-95b3-49afcd07bd08)
[2020-12-04T18:24:30.847Z] Started orchestration with ID = '788420bb31754c50acbbc46e12ef4f9c'.
[2020-12-04T18:24:30.932Z] Executed 'Functions.E3_Monitor' (Succeeded, Id=058e656e-bcb1-418c-95b3-49afcd07bd08, Duration=174ms)
[2020-12-04T18:24:30.955Z] Executed 'Functions.HttpStart' (Succeeded, Id=93772f6b-f4f0-405a-9d7b-be9eb7a38aa6, Duration=1028ms)
[2020-12-04T18:24:31.229Z] Executing 'Functions.E3_TooManyOpenIssues' (Reason='(null)', Id=6fd5be5e-7f26-4b0b-98df-c3ac39125da3)
[2020-12-04T18:24:31.772Z] Executed 'Functions.E3_TooManyOpenIssues' (Succeeded, Id=6fd5be5e-7f26-4b0b-98df-c3ac39125da3, Duration=555ms)
[2020-12-04T18:24:40.754Z] Executing 'Functions.E3_Monitor' (Reason='(null)', Id=23915e4c-ddbf-46f9-b3f0-53289ed66082)
[2020-12-04T18:24:40.789Z] Executed 'Functions.E3_Monitor' (Succeeded, Id=23915e4c-ddbf-46f9-b3f0-53289ed66082, Duration=38ms)
(...trimmed...)
```

시간 제한에 도달 하거나 3 개 이상의 미해결 문제가 감지 되 면 오케스트레이션이 완료 됩니다. `terminate`다른 함수 내에서 API를 사용 하거나 위의 202 응답에서 참조 되는 **TERMINATEPOSTURI** HTTP POST webhook를 호출할 수도 있습니다. 웹 후크를 사용 하려면를 `{text}` 초기 종료의 원인으로 바꿉니다. HTTP POST URL은 대략적으로 다음과 같이 표시 됩니다.

```
POST https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635/terminate?reason=Because&taskHub=SampleHubVS&connection=Storage&code={systemKey}
```

## <a name="next-steps"></a>다음 단계

이 샘플은 지속성 함수가 [지속성 타이머](durable-functions-timers.md)와 조건부 논리를 사용하여 외부 소스의 상태를 모니터링하는 방법을 보여줍니다. 다음 샘플에서는 외부 이벤트 및 [지속형 타이머](durable-functions-timers.md)를 사용하여 인간 상호 작용을 처리하는 방법을 보여 줍니다.

> [!div class="nextstepaction"]
> [인간 상호 작용 샘플 실행](durable-functions-phone-verification.md)
