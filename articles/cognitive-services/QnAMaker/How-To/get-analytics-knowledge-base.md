---
title: 기술 자료에 대한 분석
titleSuffix: Azure Cognitive Services
description: QnA Maker Service를 생성하는 동안 App Insights를 사용하도록 설정한 경우 QnA Maker는 모든 채팅 로그 및 기타 원격 분석을 저장합니다. App Insights에서 채팅 로그를 가져오려면 샘플 쿼리를 실행합니다.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: tulasim88
ms.openlocfilehash: dc363a3ba0d809a3307a6803993bdf500da45f1b
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47035422"
---
# <a name="get-analytics-on-your-knowledge-base"></a>기술 자료에 대한 분석 가져오기

[QnA Maker Service 생성](./set-up-qnamaker-service-azure.md) 동안 App Insights를 사용하도록 설정한 경우 QnA Maker는 모든 채팅 로그 및 기타 원격 분석을 저장합니다. App Insights에서 채팅 로그를 가져오려면 샘플 쿼리를 실행합니다.

1. App Insights 리소스로 이동합니다.

    ![Application Insights 리소스 선택](../media/qnamaker-how-to-analytics-kb/resources-created.png)

2. **Analytics** 선택 QnA Maker 원격 분석을 쿼리할 수 있는 새 창이 열립니다.

    ![Analytics 선택](../media/qnamaker-how-to-analytics-kb/analytics.png)

3. 다음 쿼리에 붙여넣고 실행합니다.

    ```query
        requests
        | where url endswith "generateAnswer"
        | project timestamp, id, name, resultCode, duration
        | parse name with *"/knowledgebases/"KbId"/generateAnswer"
        | join kind= inner (
        traces | extend id = operation_ParentId
        ) on id
        | extend question = tostring(customDimensions['Question'])
        | extend answer = tostring(customDimensions['Answer'])
        | project KbId, timestamp, resultCode, duration, question, answer
    ```

    **실행**을 선택하여 쿼리를 실행합니다.

    ![쿼리 실행](../media/qnamaker-how-to-analytics-kb/run-query.png)

## <a name="run-queries-for-other-analytics-on-your-qna-maker-knowledge-base"></a>QnA Maker 기술 자료에 대한 다른 분석에 대해 쿼리 실행

### <a name="total-90-day-traffic"></a>총 90일 트래픽

```query
    //Total Traffic
    requests
    | where url endswith "generateAnswer" and name startswith "POST"
    | parse name with *"/knowledgebases/"KbId"/generateAnswer" 
    | summarize ChatCount=count() by bin(timestamp, 1d), KbId
``` 

### <a name="total-question-traffic-in-a-given-time-period"></a>지정된 기간 동안 총 질문 트래픽

```query
    //Total Question Traffic in a given time period
    let startDate = todatetime('2018-02-18');
    let endDate = todatetime('2018-03-12');
    requests
    | where timestamp <= endDate and timestamp >=startDate
    | where url endswith "generateAnswer" and name startswith "POST" 
    | parse name with *"/knowledgebases/"KbId"/generateAnswer" 
    | summarize ChatCount=count() by KbId
```

### <a name="user-traffic"></a>사용자 트래픽

```query
    //User Traffic
    requests
    | where url endswith "generateAnswer"
    | project timestamp, id, name, resultCode, duration
    | parse name with *"/knowledgebases/"KbId"/generateAnswer"
    | join kind= inner (
    traces | extend id = operation_ParentId 
    ) on id
    | extend UserId = tostring(customDimensions['UserId'])
    | summarize ChatCount=count() by bin(timestamp, 1d), UserId, KbId
```

### <a name="latency-distribution-of-questions"></a>질문 배포 대기 시간

```query
    //Latency distribution of questions
    requests
    | where url endswith "generateAnswer" and name startswith "POST"
    | parse name with *"/knowledgebases/"KbId"/generateAnswer" 
    | project timestamp, id, name, resultCode, performanceBucket, KbId
    | summarize count() by performanceBucket, KbId
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [키 관리](./key-management.md)
