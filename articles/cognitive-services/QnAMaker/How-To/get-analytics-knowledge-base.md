---
title: 기술 자료 분석-QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker Service를 생성하는 동안 App Insights를 사용하도록 설정한 경우 QnA Maker는 모든 채팅 로그 및 기타 원격 분석을 저장합니다. App Insights에서 채팅 로그를 가져오려면 샘플 쿼리를 실행합니다.
services: cognitive-services
manager: nitinme
displayName: chat history, history, chat logs, logs
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.openlocfilehash: f58fe342d66c328bdadf41fc965c2952605aea8e
ms.sourcegitcommit: 051908e18ce42b3b5d09822f8cfcac094e1f93c2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/09/2020
ms.locfileid: "94376578"
---
# <a name="get-analytics-on-your-knowledge-base"></a>기술 자료에 대한 분석 가져오기

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (안정적인 릴리스)](#tab/v1)

[QnA Maker 서비스를 만드는](./set-up-qnamaker-service-azure.md)동안 Application Insights를 사용 하도록 설정한 경우 모든 채팅 로그 및 기타 원격 분석을 QnA Maker 저장 합니다. 샘플 쿼리를 실행 하 여 Application Insights에서 채팅 로그를 가져옵니다.

1. Application Insights 리소스로 이동 합니다.

    ![Application Insights 리소스 선택](../media/qnamaker-how-to-analytics-kb/resources-created.png)

2. **로그 (분석)** 를 선택 합니다. QnA Maker 원격 분석을 쿼리할 수 있는 새 창이 열립니다.

3. 다음 쿼리에 붙여넣고 실행합니다.

    ```kusto
    requests
    | where url endswith "generateAnswer"
    | project timestamp, id, url, resultCode, duration, performanceBucket
    | parse kind = regex url with *"(?i)knowledgebases/"KbId"/generateAnswer"
    | join kind= inner (
    traces | extend id = operation_ParentId
    ) on id
    | extend question = tostring(customDimensions['Question'])
    | extend answer = tostring(customDimensions['Answer'])
    | extend score = tostring(customDimensions['Score'])
    | project timestamp, resultCode, duration, id, question, answer, score, performanceBucket,KbId
    ```

    **실행** 을 선택하여 쿼리를 실행합니다.

    [![쿼리를 실행 하 여 사용자의 질문, 답변 및 점수를 확인 합니다.](../media/qnamaker-how-to-analytics-kb/run-query.png)](../media/qnamaker-how-to-analytics-kb/run-query.png#lightbox)

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker 관리 (미리 보기 릴리스)](#tab/v2)

QnA Maker 관리 (미리 보기)는 Azure 진단 로깅을 사용 하 여 원격 분석 데이터와 채팅 로그를 저장 합니다. 아래 단계에 따라 샘플 쿼리를 실행 하 여 QnA Maker 기술 자료의 사용에 대 한 분석을 받으세요.

1. QnA Maker 관리 (미리 보기) 서비스에 대 한 [진단 로깅을 사용 하도록 설정](https://docs.microsoft.com/azure/cognitive-services/diagnostic-logging) 합니다.

2. 이전 단계에서 로깅에 대 한 **감사, RequestResponse 및 AllMetrics** 외에 **추적** 을 선택 합니다.

    ![QnA Maker 관리 (미리 보기)에서 추적 로깅 사용](../media/qnamaker-how-to-analytics-kb/qnamaker-v2-enable-trace-logging.png)

---

## <a name="run-queries-for-other-analytics-on-your-qna-maker-knowledge-base"></a>QnA Maker 기술 자료에 대한 다른 분석에 대해 쿼리 실행

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (안정적인 릴리스)](#tab/v1)

### <a name="total-90-day-traffic"></a>총 90일 트래픽

```kusto
//Total Traffic
requests
| where url endswith "generateAnswer" and name startswith "POST"
| parse kind = regex url with *"(?i)knowledgebases/"KbId"/generateAnswer"
| summarize ChatCount=count() by bin(timestamp, 1d), KbId
```

### <a name="total-question-traffic-in-a-given-time-period"></a>지정된 기간 동안 총 질문 트래픽

```kusto
//Total Question Traffic in a given time period
let startDate = todatetime('2019-01-01');
let endDate = todatetime('2020-12-31');
requests
| where timestamp <= endDate and timestamp >=startDate
| where url endswith "generateAnswer" and name startswith "POST"
| parse kind = regex url with *"(?i)knowledgebases/"KbId"/generateAnswer"
| summarize ChatCount=count() by KbId
```

### <a name="user-traffic"></a>사용자 트래픽

```kusto
//User Traffic
requests
| where url endswith "generateAnswer"
| project timestamp, id, url, resultCode, duration
| parse kind = regex url with *"(?i)knowledgebases/"KbId"/generateAnswer"
| join kind= inner (
traces | extend id = operation_ParentId
) on id
| extend UserId = tostring(customDimensions['UserId'])
| summarize ChatCount=count() by bin(timestamp, 1d), UserId, KbId
```

### <a name="latency-distribution-of-questions"></a>질문 배포 대기 시간

```kusto
//Latency distribution of questions
requests
| where url endswith "generateAnswer" and name startswith "POST"
| parse kind = regex url with *"(?i)knowledgebases/"KbId"/generateAnswer"
| project timestamp, id, name, resultCode, performanceBucket, KbId
| summarize count() by performanceBucket, KbId
```

### <a name="unanswered-questions"></a>답변 되지 않은 질문

```kusto
// Unanswered questions
requests
| where url endswith "generateAnswer"
| project timestamp, id, url
| parse kind = regex url with *"(?i)knowledgebases/"KbId"/generateAnswer"
| join kind= inner (
traces | extend id = operation_ParentId
) on id
| extend question = tostring(customDimensions['Question'])
| extend answer = tostring(customDimensions['Answer'])
| extend score = tostring(customDimensions['Score'])
| where  score  == "0" and message == "QnAMaker GenerateAnswer"
| project timestamp, KbId, question, answer, score
| order  by timestamp  desc
```

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker 관리 (미리 보기 릴리스)](#tab/v2)

### <a name="all-qna-chat-log"></a>모든 QnA 채팅 로그

```kusto
// All QnA Traffic
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| where OperationName=="QnAMaker GenerateAnswer"
| extend answer_ = tostring(parse_json(properties_s).answer)
| extend question_ = tostring(parse_json(properties_s).question)
| extend score_ = tostring(parse_json(properties_s).score)
| extend kbId_ = tostring(parse_json(properties_s).kbId)
| project question_, answer_, score_, kbId_
```

### <a name="traffic-count-per-knowledge-base-and-user-in-a-time-period"></a>특정 기간 동안 기술 자료 및 사용자 당 트래픽 수

```kusto
// Traffic count per KB and user in a time period
let startDate = todatetime('2019-01-01');
let endDate = todatetime('2020-12-31');
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| where OperationName=="QnAMaker GenerateAnswer"
| where TimeGenerated <= endDate and TimeGenerated >=startDate
| extend kbId_ = tostring(parse_json(properties_s).kbId)
| extend userId_ = tostring(parse_json(properties_s).userId)
| summarize ChatCount=count() by bin(TimeGenerated, 1d), kbId_, userId_
```

### <a name="latency-of-generateanswer-api"></a>GenerateAnswer API의 대기 시간

```kusto
// Latency of GenerateAnswer
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| where OperationName=="Generate Answer"
| project TimeGenerated, DurationMs
| render timechart
```

### <a name="average-latency-of-all-operations"></a>모든 작업의 평균 대기 시간

```kusto
// Average Latency of all operations
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| project DurationMs, OperationName
| summarize count(), avg(DurationMs) by OperationName
| render barchart
```

### <a name="unanswered-questions"></a>답변 되지 않은 질문

```kusto
// All unanswered questions
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| where OperationName=="QnAMaker GenerateAnswer"
| extend answer_ = tostring(parse_json(properties_s).answer)
| extend question_ = tostring(parse_json(properties_s).question)
| extend score_ = tostring(parse_json(properties_s).score)
| extend kbId_ = tostring(parse_json(properties_s).kbId)
| where score_ == 0
| project question_, answer_, score_, kbId_
```

---

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Capactiy 선택](./improve-knowledge-base.md)