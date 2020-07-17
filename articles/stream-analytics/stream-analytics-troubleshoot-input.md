---
title: Azure Stream Analytics의 입력 문제 해결
description: 이 문서에서는 Azure Stream Analytics 작업에서 입력 연결의 문제를 해결하는 기술에 대해 설명합니다.
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: troubleshooting
ms.date: 05/01/2020
ms.custom: seodec18
ms.openlocfilehash: 3cac20e33ff865058ce41799ae8841a05716edc9
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/07/2020
ms.locfileid: "86045079"
---
# <a name="troubleshoot-input-connections"></a>입력 연결 문제 해결

이 문서에서는 Azure Stream Analytics 입력 연결과 관련된 일반적인 문제, 입력 문제를 해결하는 방법 및 해당 문제를 해결하는 방법을 설명합니다. 대부분의 문제 해결 단계에서는 Stream Analytics 작업에 대해 리소스 로그를 사용하도록 설정해야 합니다. 리소스 로그를 사용하도록 설정하지 않은 경우 [리소스 로그를 사용하여 Azure Stream Analytics 문제 해결](stream-analytics-job-diagnostic-logs.md)을 참조하세요.

## <a name="input-events-not-received-by-job"></a>작업에서 수신되지 않은 입력 이벤트 

1.  입력 및 출력 연결을 테스트합니다. 각 입력 및 출력에 대해 **테스트 연결** 단추를 사용하여 입력 및 출력에 대한 연결을 확인합니다.

2.  입력 데이터를 검사합니다.

    1. 각 입력에 대해 [**샘플 데이터**](stream-analytics-sample-data-input.md) 단추를 사용합니다. 입력 샘플 데이터를 다운로드합니다.
        
    1. 샘플 데이터를 검사하여 스키마 및 [데이터 형식](https://docs.microsoft.com/stream-analytics-query/data-types-azure-stream-analytics)을 파악합니다.
    
    1. [Event Hub 메트릭](../event-hubs/event-hubs-metrics-azure-monitor.md)을 확인하여 이벤트가 전송되고 있는지 확인합니다. Event Hubs가 메시지를 수신하는 경우 메시지 메트릭은 0보다 커야 합니다.

3.  입력 미리 보기에서 시간 범위를 선택했는지 확인합니다. **시간 범위 선택**을 선택한 다음, 쿼리를 테스트하기 전에 샘플 기간을 입력합니다.

## <a name="malformed-input-events-causes-deserialization-errors"></a>잘못된 형식의 입력 이벤트로 인해 역직렬화 오류가 발생할 수 있습니다. 

Stream Analytics 작업의 입력 스트림에 잘못된 형식의 메시지가 포함되어 있으면 역직렬화 문제가 발생합니다. 예를 들어 잘못된 형식의 메시지는 JSON 개체에서 괄호나 중괄호가 누락되거나 시간 필드의 타임스탬프 형식이 잘못된 경우 발생할 수 있습니다. 
 
Stream Analytics 작업이 입력으로부터 잘못된 형식의 메시지를 수신하면 해당 메시지를 삭제하고 경고로 알립니다. 경고 기호는 Stream Analytics 작업의 **입력** 타일에 표시됩니다. 작업이 실행 중인 경우 다음 경고 기호가 존재합니다.

![Azure Stream Analytics 입력 타일](media/stream-analytics-malformed-events/stream-analytics-inputs-tile.png)

리소스 로그를 사용하여 오류의 세부 정보 및 오류를 발생시킨 메시지(페이로드)를 확인합니다. 역직렬화 오류가 발생할 수 있는 여러 가지 이유가 있습니다. 특정 역직렬화 오류에 대한 자세한 내용은 [입력 데이터 오류](data-errors.md#input-data-errors)를 참조하세요. 리소스 로그를 사용하지 않는 경우 Azure Portal에서 간단한 알림을 사용할 수 있습니다.

![입력 세부 정보 경고 알림](media/stream-analytics-malformed-events/warning-message-with-offset.png)

메시지 페이로드가 32KB보다 크거나 이진 형식인 경우 [GitHub 샘플 리포지토리](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/CheckMalformedEventsEH)에서 사용할 수 있는 CheckMalformedEvents.cs 코드를 실행합니다. 이 코드는 파티션 ID, 오프셋을 읽고 오프셋에 배치된 데이터를 출력합니다. 

## <a name="job-exceeds-maximum-event-hub-receivers"></a>작업이 최대 Event Hub 수신기를 초과함

Event Hubs를 사용하는 모범 사례는 작업 확장성을 위해 여러 소비자 그룹을 사용하는 것입니다. 특정 입력에 대한 Stream Analytics 작업의 판독기 수가 단일 소비자 그룹의 판독기 수에 영향을 줍니다. 수신자의 정확한 수는 확장 토폴로지 논리에 대한 내부 구현 세부 정보를 기반으로 하고 외부에 노출되지 않습니다. 판독기 수는 작업이 시작하거나 작업을 업그레이드하는 동안 변경될 수 있습니다.

수신기 수가 최댓값을 초과하는 경우 다음 오류 메시지가 표시됩니다. 오류 메시지에는 소비자 그룹 아래의 Event Hub에 대한 기존 연결 목록이 포함됩니다. `AzureStreamAnalytics` 태그는 연결이 Azure Streaming Service에서 온 것임을 나타냅니다.

```
The streaming job failed: Stream Analytics job has validation errors: Job will exceed the maximum amount of Event Hub Receivers.

The following information may be helpful in identifying the connected receivers: Exceeded the maximum number of allowed receivers per partition in a consumer group which is 5. List of connected receivers – 
AzureStreamAnalytics_c4b65e4a-f572-4cfc-b4e2-cf237f43c6f0_1, 
AzureStreamAnalytics_c4b65e4a-f572-4cfc-b4e2-cf237f43c6f0_1, 
AzureStreamAnalytics_c4b65e4a-f572-4cfc-b4e2-cf237f43c6f0_1, 
AzureStreamAnalytics_c4b65e4a-f572-4cfc-b4e2-cf237f43c6f0_1, 
AzureStreamAnalytics_c4b65e4a-f572-4cfc-b4e2-cf237f43c6f0_1.
```

> [!NOTE]
> 작업을 업그레이드하는 동안 판독기 수가 변경되면 일시적인 경고가 감사 로그에 기록됩니다. Stream Analytics 작업은 자동으로 이러한 일시적인 문제를 복구합니다.

### <a name="add-a-consumer-group-in-event-hubs"></a>Event Hubs에 소비자 그룹 추가

Event Hubs 인스턴스에 새 소비자 그룹을 추가하려면 다음 단계를 수행합니다.

1. Azure Portal에 로그인합니다.

2. Event Hub를 찾습니다.

3. **엔터티** 제목 아래에서 **Event Hubs**를 선택합니다.

4. 이름으로 이벤트 허브를 선택합니다.

5. **Event Hubs 인스턴스** 페이지의 **엔터티** 제목 아래에서 **소비자 그룹**을 선택합니다. 이름이 **$Default**인 소비자 그룹이 표시됩니다.

6. **+소비자 그룹**을 선택하여 새 소비자 그룹을 추가합니다. 

   ![Event Hubs에 소비자 그룹 추가](media/stream-analytics-event-hub-consumer-groups/new-eh-consumer-group.png)

7. Stream Analytics 작업에서 이벤트 허브를 가리키는 입력을 만들 때 소비자 그룹을 지정했습니다. 아무 것도 지정하지 않으면 **$Default**가 사용됩니다. 새 소비자 그룹을 만든 후에는 Stream Analytics 작업에서 이벤트 허브 입력을 편집하고 새 소비자 그룹의 이름을 지정합니다.

## <a name="readers-per-partition-exceeds-event-hubs-limit"></a>파티션당 판독기가 Event Hubs 제한을 초과함

스트리밍 쿼리 구문이 동일한 입력 이벤트 허브 리소스를 여러 번 참조하는 경우 작업 엔진이 해당 소비자 그룹의 쿼리에 여러 판독기를 사용할 수 있습니다. 동일한 소비자 그룹에 대한 참조가 너무 많으면 작업이 한도 5를 초과하여 오류가 throw 될 수 있습니다. 이 경우 다음 섹션에 설명된 솔루션으로 여러 소비자 그룹의 여러 입력을 사용하여 추가로 분할하면 됩니다. 

파티션당 읽기 권한자 수가 5개 Event Hubs 제한을 초과하는 시나리오에는 다음이 포함됩니다.

* 여러 SELECT 문: **동일한** 이벤트 허브 입력을 참조하는 여러 SELECT 문을 사용하는 경우 각 SELECT 문으로 인해 새 수신기가 생성됩니다.

* UNION: UNION을 사용하면 **동일한** 이벤트 허브 및 소비자 그룹을 참조하는 여러 입력이 발생할 수 있습니다.

* SELF JOIN: SELF JOIN 작업을 사용하면 **동일한** 이벤트 허브를 여러 번 참조할 수 있습니다.

다음 모범 사례는 파티션당 읽기 권한자 수가 5개 Event Hubs 제한을 초과하는 시나리오를 완화하는 데 도움이 될 수 있습니다.

### <a name="split-your-query-into-multiple-steps-by-using-a-with-clause"></a>WITH 절을 사용하여 쿼리를 여러 단계로 분할

WITH 절은 쿼리의 FROM 절에서 참조할 수 있는 명명된 임시 결과 집합을 지정합니다. 단일 SELECT 문의 실행 범위에서 WITH 절을 정의합니다.

예를 들어 아래 쿼리 대신,

```SQL
SELECT foo 
INTO output1
FROM inputEventHub

SELECT bar
INTO output2
FROM inputEventHub 
…
```

다음 쿼리를 사용합니다.

```SQL
WITH data AS (
   SELECT * FROM inputEventHub
)

SELECT foo
INTO output1
FROM data

SELECT bar
INTO output2
FROM data
…
```

### <a name="ensure-that-inputs-bind-to-different-consumer-groups"></a>입력이 다른 소비자 그룹으로 바인딩되었는지 확인

세 개 이상의 입력이 동일한 Event Hubs 소비자 그룹에 연결된 쿼리의 경우 개별 소비자 그룹을 만듭니다. 이를 위해서는 추가 Stream Analytics 입력을 만들어야 합니다.

### <a name="create-separate-inputs-with-different-consumer-groups"></a>다른 소비자 그룹을 사용 하 여 별도의 입력 만들기

동일한 이벤트 허브에 대해 서로 다른 소비자 그룹을 사용 하 여 별도의 입력을 만들 수 있습니다. 다음 UNION 쿼리는 *Inputone* 및 *inputone* 가 동일한 이벤트 허브 원본을 참조 하는 경우의 예입니다. 모든 쿼리에는 서로 다른 소비자 그룹을 사용 하는 별도의 입력이 있을 수 있습니다. UNION 쿼리는 한 가지 예입니다.

```sql
WITH 
DataOne AS 
(
SELECT * FROM InputOne 
),

DataTwo AS 
(
SELECT * FROM InputTwo 
),

SELECT foo FROM DataOne
UNION 
SELECT foo FROM DataTwo

```

## <a name="get-help"></a>도움말 보기

추가 지원이 필요한 경우 [Azure Stream Analytics용 Microsoft Q&A 질문 페이지](https://docs.microsoft.com/answers/topics/azure-stream-analytics.html)를 사용해보세요.

## <a name="next-steps"></a>다음 단계

* [Azure Stream Analytics 소개](stream-analytics-introduction.md)
* [Azure Stream Analytics 사용 시작](stream-analytics-real-time-fraud-detection.md)
* [Azure  Stream Analytics 작업 규모 지정](stream-analytics-scale-jobs.md)
* [Azure  Stream Analytics 쿼리 언어 참조](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Stream Analytics 관리 REST API 참조](https://msdn.microsoft.com/library/azure/dn835031.aspx)
