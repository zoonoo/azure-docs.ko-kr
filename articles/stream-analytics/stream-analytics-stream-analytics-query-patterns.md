---
title: Azure Stream Analytics의 일반적인 쿼리 패턴
description: 이 문서에서는 Azure Stream Analytics 작업에 유용한 몇 가지 일반적인 쿼리 패턴 및 디자인을 설명합니다.
services: stream-analytics
author: rodrigoaatmicrosoft
ms.author: rodrigoa
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/18/2019
ms.openlocfilehash: aa8bd6e89dd47c4e972a860691d1bc3779ba5bc7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75982309"
---
# <a name="common-query-patterns-in-azure-stream-analytics"></a>Azure Stream Analytics의 일반적인 쿼리 패턴

Azure Stream Analytics에서 쿼리는 SQL 방식 쿼리 언어로 표현됩니다. 언어 구문은 [Stream Analytics 쿼리 언어 참조](/stream-analytics-query/stream-analytics-query-language-reference) 가이드에 설명되어 있습니다. 

쿼리 디자인은 하나의 입력 스트림에서 출력 데이터 저장소로 이벤트 데이터를 이동하는 간단한 통과 논리를 표현하거나 스트림 분석 가이드를 [사용하여 IoT 솔루션 빌드에서와](stream-analytics-build-an-iot-solution-using-stream-analytics.md) 같이 다양한 시간 창에서 집계를 계산하기 위해 풍부한 패턴 일치 및 시간 분석을 수행할 수 있습니다. 여러 입력에서 데이터를 조인하여 스트리밍 이벤트를 결합할 수 있으며 정적 참조 데이터에 대한 조회를 수행하여 이벤트 값을 보강할 수 있습니다. 여러 출력에 데이터를 쓸 수도 있습니다.

이 문서에서는 실제 시나리오를 기반으로 하는 몇 가지 일반적인 쿼리 패턴에 대한 솔루션을 간략하게 설명합니다.

## <a name="supported-data-formats"></a>지원되는 데이터 형식

Azure Stream Analytics는 CSV, JSON 및 Avro 데이터 형식의 이벤트 처리를 지원합니다.

JSON 및 Avro는 둘 다 중첩된 개체(레코드) 또는 배열과 같은 복합 형식을 포함할 수 있습니다. 이러한 복잡한 데이터 형식 작업에 대한 자세한 내용은 [구문 분석 JSON 및 AVRO 데이터](stream-analytics-parsing-json.md) 문서를 참조하십시오.

## <a name="simple-pass-through-query"></a>간단한 통과 쿼리

간단한 통과 쿼리를 사용하여 입력 스트림 데이터를 출력에 복사할 수 있습니다. 예를 들어 문자 분석을 위해 실시간 차량 정보가 포함된 데이터 스트림을 SQL 데이터베이스에 저장해야 하는 경우 간단한 통과 쿼리가 작업을 수행합니다.

**입력**:

| 계정을 | Time | 무게 |
| --- | --- | --- |
| 메이크1 |2015-01-01T00:오전 12:01.0000000Z |"1000" |
| 메이크1 |2015-01-01T00:오전 12:02.0000000Z |"2000" |

**출력**:

| 계정을 | Time | 무게 |
| --- | --- | --- |
| 메이크1 |2015-01-01T00:오전 12:01.0000000Z |"1000" |
| 메이크1 |2015-01-01T00:오전 12:02.0000000Z |"2000" |

**쿼리**:

```SQL
SELECT
    *
INTO Output
FROM Input
```

**SELECT** * 쿼리는 들어오는 이벤트의 모든 필드를 투영하고 출력으로 보냅니다. 마찬가지로 **SELECT를** 사용하여 입력에서 필요한 필드만 투영할 수도 있습니다. 이 예제에서 차량 *만들기* 및 *시간이* 저장할 수 있는 유일한 필수 필드인 경우 **SELECT** 문에 해당 필드를 지정할 수 있습니다.

**입력**:

| 계정을 | Time | 무게 |
| --- | --- | --- |
| 메이크1 |2015-01-01T00:오전 12:01.0000000Z |1000 |
| 메이크1 |2015-01-01T00:오전 12:02.0000000Z |2000 |
| 메이크 2 |2015-01-01T00:오전 12:04.0000000Z |1500 |

**출력**:

| 계정을 | Time |
| --- | --- |
| 메이크1 |2015-01-01T00:오전 12:01.0000000Z |
| 메이크1 |2015-01-01T00:오전 12:02.0000000Z |
| 메이크 2 |2015-01-01T00:오전 12:04.0000000Z |

**쿼리**:

```SQL
SELECT
    Make, Time
INTO Output
FROM Input
```
## <a name="data-aggregation-over-time"></a>시간에 따라 데이터 집계

시간 기간 동안 정보를 계산하기 위해 데이터를 함께 집계할 수 있습니다. 이 예제에서는 모든 특정 자동차 만들기에 대해 마지막 10분 동안 카운트가 계산됩니다.

**입력**:

| 계정을 | Time | 무게 |
| --- | --- | --- |
| 메이크1 |2015-01-01T00:오전 12:01.0000000Z |1000 |
| 메이크1 |2015-01-01T00:오전 12:02.0000000Z |2000 |
| 메이크 2 |2015-01-01T00:오전 12:04.0000000Z |1500 |

**출력**:

| 계정을 | 개수 |
| --- | --- |
| 메이크1 | 2 |
| 메이크 2 | 1 |

**쿼리**:

```SQL
SELECT
    Make,
    COUNT(*) AS Count
FROM
    Input TIMESTAMP BY Time
GROUP BY
    Make,
    TumblingWindow(second, 10)
```

이 집계는 *자동차를 Make별로* 그룹화하고 10초마다 계산합니다. 출력에는 통행료를 통과한 자동차의 *메이크* 앤 *카운트가* 있습니다.

텀블링윈도우는 이벤트를 함께 그룹화하는 데 사용되는 창 기능입니다. 집계는 그룹화된 모든 이벤트에 적용할 수 있습니다. 자세한 내용은 [창 기능.](stream-analytics-window-functions.md)

집계에 대한 자세한 내용은 [집계 함수를](/stream-analytics-query/aggregate-functions-azure-stream-analytics)참조하십시오.

## <a name="data-conversion"></a>데이터 변환

**CAST** 방법을 사용하여 데이터를 실시간으로 캐스팅할 수 있습니다. 예를 들어 자동차 중량은 **nvarchar(max)** 유형에서 **bigint** 를 입력하고 숫자 계산에 사용할 수 있습니다.

**입력**:

| 계정을 | Time | 무게 |
| --- | --- | --- |
| 메이크1 |2015-01-01T00:오전 12:01.0000000Z |"1000" |
| 메이크1 |2015-01-01T00:오전 12:02.0000000Z |"2000" |

**출력**:

| 계정을 | 무게 |
| --- | --- |
| 메이크1 |3000 |

**쿼리**:

```SQL
SELECT
    Make,
    SUM(CAST(Weight AS BIGINT)) AS Weight
FROM
    Input TIMESTAMP BY Time
GROUP BY
    Make,
    TumblingWindow(second, 10)
```

**CAST** 문을 사용하여 데이터 형식을 지정합니다. [데이터 형식(Azure Stream Analytics)에서](/stream-analytics-query/data-types-azure-stream-analytics)지원되는 데이터 유형 목록을 참조하세요.

데이터 변환 [기능에](/stream-analytics-query/conversion-functions-azure-stream-analytics)대한 자세한 내용은 .

## <a name="string-matching-with-like-and-not-like"></a>문자열 은 좋아하고 좋아하지 않는 문자열 일치

**LIKE** 및 **NOT LIKE는** 필드가 특정 패턴과 일치하는지 확인하는 데 사용할 수 있습니다. 예를 들어 필터를 만들어 문자 'A'로 시작하여 숫자 9로 끝나는 번호판만 반환할 수 있습니다.

**입력**:

| 계정을 | License_plate | Time |
| --- | --- | --- |
| 메이크1 |ABC-123 |2015-01-01T00:오전 12:01.0000000Z |
| 메이크 2 |AAA-999 |2015-01-01T00:오전 12:02.0000000Z |
| 메이크 3 |ABC-369 |2015-01-01T00:오전 12:03.0000000Z |

**출력**:

| 계정을 | License_plate | Time |
| --- | --- | --- |
| 메이크 2 |AAA-999 |2015-01-01T00:오전 12:02.0000000Z |
| 메이크 3 |ABC-369 |2015-01-01T00:오전 12:03.0000000Z |

**쿼리**:

```SQL
SELECT
    *
FROM
    Input TIMESTAMP BY Time
WHERE
    License_plate LIKE 'A%9'
```

**LIKE** 문을 사용하여 **License_plate** 필드 값을 확인합니다. 문자 'A'로 시작한 다음 0자 이상의 문자열이 있고 숫자 9로 끝나야 합니다.

## <a name="specify-logic-for-different-casesvalues-case-statements"></a>다른 사례/값(CASE 문)에 대한 논리를 지정합니다.

**CASE** 문은 특정 기준에 따라 서로 다른 필드에 대해 서로 다른 계산을 제공할 수 있습니다. 예를 *들어, Make1의* 자동차에 차선 'A'를 할당하고 다른 메이크에 'B'를 차선 'B'로 지정합니다.

**입력**:

| 계정을 | Time |
| --- | --- |
| 메이크1 |2015-01-01T00:오전 12:01.0000000Z |
| 메이크 2 |2015-01-01T00:오전 12:02.0000000Z |
| 메이크 2 |2015-01-01T00:오전 12:03.0000000Z |

**출력**:

| 계정을 |Dispatch_to_lane | Time |
| --- | --- | --- |
| 메이크1 |"A" |2015-01-01T00:오전 12:01.0000000Z |
| 메이크 2 |"B" |2015-01-01T00:오전 12:02.0000000Z |

**해결 방법**:

```SQL
SELECT
    Make
    CASE
        WHEN Make = "Make1" THEN "A"
        ELSE "B"
    END AS Dispatch_to_lane,
    System.TimeStamp() AS Time
FROM
    Input TIMESTAMP BY Time
```

**CASE** 식은 식을 간단한 식 집합과 비교하여 결과를 결정합니다. 이 예에서는 *Make1의* 차량이 차선 'A'로 파견되고 다른 메이크의 차량은 차선 'B'가 할당됩니다.

자세한 내용은 [대/소문자 식을](/stream-analytics-query/case-azure-stream-analytics)참조하십시오.

## <a name="send-data-to-multiple-outputs"></a>데이터를 여러 출력으로 전송

여러 **SELECT** 문을 사용하여 다른 출력 싱크에 데이터를 출력할 수 있습니다. 예를 들어 하나의 SELECT는 임계값 기반 경고를 출력할 수 있고 다른 **SELECT는** 이벤트를 Blob 저장소에 출력할 수 있습니다.

**입력**:

| 계정을 | Time |
| --- | --- |
| 메이크1 |2015-01-01T00:오전 12:01.0000000Z |
| 메이크1 |2015-01-01T00:오전 12:02.0000000Z |
| 메이크 2 |2015-01-01T00:오전 12:01.0000000Z |
| 메이크 2 |2015-01-01T00:오전 12:02.0000000Z |
| 메이크 2 |2015-01-01T00:오전 12:03.0000000Z |

**출력 아카이브출력**:

| 계정을 | Time |
| --- | --- |
| 메이크1 |2015-01-01T00:오전 12:01.0000000Z |
| 메이크1 |2015-01-01T00:오전 12:02.0000000Z |
| 메이크 2 |2015-01-01T00:오전 12:01.0000000Z |
| 메이크 2 |2015-01-01T00:오전 12:02.0000000Z |
| 메이크 2 |2015-01-01T00:오전 12:03.0000000Z |

**출력 경고출력**:

| 계정을 | Time | 개수 |
| --- | --- | --- |
| 메이크 2 |2015-01-01T00:오전 12:10.0000000Z |3 |

**쿼리**:

```SQL
SELECT
    *
INTO
    ArchiveOutput
FROM
    Input TIMESTAMP BY Time

SELECT
    Make,
    System.TimeStamp() AS Time,
    COUNT(*) AS [Count]
INTO
    AlertOutput
FROM
    Input TIMESTAMP BY Time
GROUP BY
    Make,
    TumblingWindow(second, 10)
HAVING
    [Count] >= 3
```

**INTO** 절은 스트림 애널리틱스에 데이터를 쓸 출력을 알려줍니다. 첫 번째 **SELECT는** 입력에서 데이터를 수신하고 **ArchiveOutput이라는**출력으로 전송하는 통과 쿼리를 정의합니다. 두 번째 쿼리는 **AlertOutput**이라는 다운스트림 경고 시스템 출력에 결과 보내기 전에 몇 가지 간단한 집계 및 필터링을 수행 합니다.

**WITH** 절을 사용하여 여러 하위 쿼리 블록을 정의할 수 있습니다. 이 옵션은 입력 소스에 더 적은 판독기를 여는 이점이 있습니다.

**쿼리**:

```SQL
WITH ReaderQuery AS (
    SELECT
        *
    FROM
        Input TIMESTAMP BY Time
)

SELECT * INTO ArchiveOutput FROM ReaderQuery

SELECT 
    Make,
    System.TimeStamp() AS Time,
    COUNT(*) AS [Count] 
INTO AlertOutput 
FROM ReaderQuery
GROUP BY
    Make,
    TumblingWindow(second, 10)
HAVING [Count] >= 3
```

자세한 내용은 [ **WITH** 절을](/stream-analytics-query/with-azure-stream-analytics)참조하십시오.

## <a name="count-unique-values"></a>고유 값 카운트

**COUNT** 및 **DISTINCT를** 사용하여 시간 기간 내에 스트림에 나타나는 고유 필드 값의 수를 계산할 수 있습니다. 쿼리를 만들어 2초 창에서 유료 부스를 통과한 자동차의 고유 *하게 만드는* 횟수를 계산할 수 있습니다.

**입력**:

| 계정을 | Time |
| --- | --- |
| 메이크1 |2015-01-01T00:오전 12:01.0000000Z |
| 메이크1 |2015-01-01T00:오전 12:02.0000000Z |
| 메이크 2 |2015-01-01T00:오전 12:01.0000000Z |
| 메이크 2 |2015-01-01T00:오전 12:02.0000000Z |
| 메이크 2 |2015-01-01T00:오전 12:03.0000000Z |

**출력:**

| Count_make | Time |
| --- | --- |
| 2 |2015-01-01T00:오전 12:02.000Z |
| 1 |2015-01-01T00:오전 12:04.000Z |

**쿼리:**

```SQL
SELECT
     COUNT(DISTINCT Make) AS Count_make,
     System.TIMESTAMP() AS Time
FROM Input TIMESTAMP BY TIME
GROUP BY 
     TumblingWindow(second, 2)
```

**COUNT(고유 확인)는** 시간 창 내에서 **Make** 열에서 고유 값의 개수를 반환합니다.
자세한 내용은 [ **COUNT** 집계 함수를](/stream-analytics-query/count-azure-stream-analytics)참조하십시오.

## <a name="calculation-over-past-events"></a>과거 이벤트에 대한 계산

**LAG** 함수는 시간 기간 내의 과거 이벤트를 보고 현재 이벤트와 비교하는 데 사용할 수 있습니다. 예를 들어, 통행료를 통과한 마지막 차량과 다른 경우 현재 차량 메이크를 출력할 수 있습니다.

**입력**:

| 계정을 | Time |
| --- | --- |
| 메이크1 |2015-01-01T00:오전 12:01.0000000Z |
| 메이크 2 |2015-01-01T00:오전 12:02.0000000Z |

**출력**:

| 계정을 | Time |
| --- | --- |
| 메이크 2 |2015-01-01T00:오전 12:02.0000000Z |

**쿼리**:

```SQL
SELECT
    Make,
    Time
FROM
    Input TIMESTAMP BY Time
WHERE
    LAG(Make, 1) OVER (LIMIT DURATION(minute, 1)) <> Make
```

**LAG를** 사용하여 입력 스트림을 한 번 다시 들여다 보면서 *Make* 값을 검색하고 이를 현재 이벤트의 *Make* 값과 비교하여 이벤트를 출력합니다.

자세한 내용은 [**LAG**](/stream-analytics-query/lag-azure-stream-analytics)를 참조하십시오.

## <a name="retrieve-the-first-event-in-a-window"></a>창에서 첫 번째 이벤트 검색

**IsFirst는** 시간 창에서 첫 번째 이벤트를 검색하는 데 사용할 수 있습니다. 예를 들어 10분 간격으로 첫 번째 자동차 정보를 아웃퍼팅합니다.

**입력**:

| License_plate | 계정을 | Time |
| --- | --- | --- |
| DXE 5291 |메이크1 |2015-07-27T00:오전 12:05.0000000Z |
| YZK 5704 |메이크 3 |2015-07-27T00:오전 2:17.0000000Z |
| RMV 8282 |메이크1 |2015-07-27T00:오전 5:01.0000000Z |
| YHN 6970 |메이크 2 |2015-07-27T00:오전 6:00.0000000Z |
| VFE 1616 |메이크 2 |2015-07-27T00:오전 9:31.0000000Z |
| QYF 9358 |메이크1 |2015-07-27T00:오후 12:02.0000000Z |
| MDR 6128 |메이크4 |2015-07-27T00:오후 1:45.0000000Z |

**출력**:

| License_plate | 계정을 | Time |
| --- | --- | --- |
| DXE 5291 |메이크1 |2015-07-27T00:오전 12:05.0000000Z |
| QYF 9358 |메이크1 |2015-07-27T00:오후 12:02.0000000Z |

**쿼리**:

```SQL
SELECT 
    License_plate,
    Make,
    Time
FROM 
    Input TIMESTAMP BY Time
WHERE 
    IsFirst(minute, 10) = 1
```

**IsFirst는** 또한 데이터를 분할하고 각 특정 자동차에 첫 번째 이벤트를 계산할 수 있습니다 매 10 분 간격으로 발견 *확인합니다.*

**출력**:

| License_plate | 계정을 | Time |
| --- | --- | --- |
| DXE 5291 |메이크1 |2015-07-27T00:오전 12:05.0000000Z |
| YZK 5704 |메이크 3 |2015-07-27T00:오전 2:17.0000000Z |
| YHN 6970 |메이크 2 |2015-07-27T00:오전 6:00.0000000Z |
| QYF 9358 |메이크1 |2015-07-27T00:오후 12:02.0000000Z |
| MDR 6128 |메이크4 |2015-07-27T00:오후 1:45.0000000Z |

**쿼리**:

```SQL
SELECT 
    License_plate,
    Make,
    Time
FROM 
    Input TIMESTAMP BY Time
WHERE 
    IsFirst(minute, 10) OVER (PARTITION BY Make) = 1
```

자세한 내용은 [**IsFirst**](/stream-analytics-query/isfirst-azure-stream-analytics)을 참조하십시오.

## <a name="return-the-last-event-in-a-window"></a>창에서 마지막 이벤트 반환

이벤트가 시스템에서 실시간으로 사용되기 때문에 이벤트가 해당 시간 동안 마지막으로 도착할 지 여부를 확인할 수 있는 함수는 없습니다. 이를 위해 입력 스트림은 이벤트 시간이 해당 창의 모든 이벤트에 대한 최대 시간인 다른 스트림과 결합되어야 합니다.

**입력**:

| License_plate | 계정을 | Time |
| --- | --- | --- |
| DXE 5291 |메이크1 |2015-07-27T00:오전 12:05.0000000Z |
| YZK 5704 |메이크 3 |2015-07-27T00:오전 2:17.0000000Z |
| RMV 8282 |메이크1 |2015-07-27T00:오전 5:01.0000000Z |
| YHN 6970 |메이크 2 |2015-07-27T00:오전 6:00.0000000Z |
| VFE 1616 |메이크 2 |2015-07-27T00:오전 9:31.0000000Z |
| QYF 9358 |메이크1 |2015-07-27T00:오후 12:02.0000000Z |
| MDR 6128 |메이크4 |2015-07-27T00:오후 1:45.0000000Z |

**출력**:

| License_plate | 계정을 | Time |
| --- | --- | --- |
| VFE 1616 |메이크 2 |2015-07-27T00:오전 9:31.0000000Z |
| MDR 6128 |메이크4 |2015-07-27T00:오후 1:45.0000000Z |

**쿼리**:

```SQL
WITH LastInWindow AS
(
    SELECT 
        MAX(Time) AS LastEventTime
    FROM 
        Input TIMESTAMP BY Time
    GROUP BY 
        TumblingWindow(minute, 10)
)

SELECT 
    Input.License_plate,
    Input.Make,
    Input.Time
FROM
    Input TIMESTAMP BY Time 
    INNER JOIN LastInWindow
    ON DATEDIFF(minute, Input, LastInWindow) BETWEEN 0 AND 10
    AND Input.Time = LastInWindow.LastEventTime
```

쿼리의 첫 번째 단계는 10분 창의 최대 타임스탬프를 찾습니다. 두 번째 단계는 첫 번째 쿼리의 결과를 원래 스트림과 조인하여 각 창의 마지막 타임스탬프와 일치하는 이벤트를 찾습니다. 

**DATEDIFF는** 두 DateTime 필드 간의 시간 차이를 비교하고 반환하는 날짜별 함수로, 자세한 내용은 [날짜 함수를](https://docs.microsoft.com/stream-analytics-query/date-and-time-functions-azure-stream-analytics)참조하십시오.

스트림 조인에 대한 자세한 내용은 [**JOIN**](/stream-analytics-query/join-azure-stream-analytics)을 참조하십시오.


## <a name="correlate-events-in-a-stream"></a>스트림의 이벤트 상관 관계

**LAG** 함수를 사용하여 과거 이벤트를 살펴보면 동일한 스트림에서 이벤트의 상관 관계를 수행할 수 있습니다. 예를 들어, 동일한 *Make에서* 연속된 두 대의 자동차가 지난 90초 동안 통행료를 통과할 때마다 출력을 생성할 수 있습니다.

**입력**:

| 계정을 | License_plate | Time |
| --- | --- | --- |
| 메이크1 |ABC-123 |2015-01-01T00:오전 12:01.0000000Z |
| 메이크1 |AAA-999 |2015-01-01T00:오전 12:02.0000000Z |
| 메이크 2 |DEF-987 |2015-01-01T00:오전 12:03.0000000Z |
| 메이크1 |GHI-345 |2015-01-01T00:오전 12:04.0000000Z |

**출력**:

| 계정을 | Time | Current_car_license_plate | First_car_license_plate | First_car_time |
| --- | --- | --- | --- | --- |
| 메이크1 |2015-01-01T00:오전 12:02.0000000Z |AAA-999 |ABC-123 |2015-01-01T00:오전 12:01.0000000Z |

**쿼리**:

```SQL
SELECT
    Make,
    Time,
    License_plate AS Current_car_license_plate,
    LAG(License_plate, 1) OVER (LIMIT DURATION(second, 90)) AS First_car_license_plate,
    LAG(Time, 1) OVER (LIMIT DURATION(second, 90)) AS First_car_time
FROM
    Input TIMESTAMP BY Time
WHERE
    LAG(Make, 1) OVER (LIMIT DURATION(second, 90)) = Make
```

**LAG** 함수는 입력 스트림을 한 번 의 이벤트로 다시 살펴보고 *Make* 값을 검색하여 현재 이벤트의 *Make* 값과 비교할 수 있습니다.  조건이 충족되면 **SELECT** 문에서 **LAG를** 사용하여 이전 이벤트의 데이터를 투영할 수 있습니다.

자세한 내용은 [LAG](/stream-analytics-query/lag-azure-stream-analytics)를 참조하십시오.

## <a name="detect-the-duration-between-events"></a>이벤트 간 기간 검색

종료 이벤트가 수신되면 마지막 시작 이벤트를 보고 이벤트 기간을 계산할 수 있습니다. 이 쿼리는 사용자가 페이지 나 기능에 소요되는 시간을 결정하는 데 유용 할 수 있습니다.

**입력**:  

| 사용자 | 기능 | 행사 | Time |
| --- | --- | --- | --- |
| user@location.com |RightMenu |시작 |2015-01-01T00:오전 12:01.0000000Z |
| user@location.com |RightMenu |끝 |2015-01-01T00:오전 12:08.0000000Z |

**출력**:  

| 사용자 | 기능 | Duration |
| --- | --- | --- |
| user@location.com |RightMenu |7 |

**쿼리**:

```SQL
SELECT
    [user],
    feature,
    DATEDIFF(
        second,
        LAST(Time) OVER (PARTITION BY [user], feature LIMIT DURATION(hour, 1) WHEN Event = 'start'),
        Time) as duration
FROM input TIMESTAMP BY Time
WHERE
    Event = 'end'
```

**LAST** 함수를 사용하여 특정 조건 내에서 마지막 이벤트를 검색할 수 있습니다. 이 예제에서 조건은 **[시작]** 유형의 이벤트로, 파티션 별 사용자 및 기능별로 검색을 분할합니다. 이렇게 하면 Start 이벤트를 검색할 때 모든 사용자와 기능이 독립적으로 처리됩니다. **기간 제한** 은 종료 및 시작 이벤트 사이의 1 시간으로 시간을 다시 검색을 제한합니다.

## <a name="detect-the-duration-of-a-condition"></a>조건의 기간 감지

여러 이벤트를 통해 범위 조건의 경우 **LAG** 함수를 사용하여 해당 조건의 기간을 식별할 수 있습니다. 예를 들어 버그로 인해 모든 자동차의 중량이 잘못되었고(20.000파운드 이상) 버그의 지속 시간을 계산해야 한다고 가정합니다.

**입력**:

| 계정을 | Time | 무게 |
| --- | --- | --- |
| 메이크1 |2015-01-01T00:오전 12:01.0000000Z |2000 |
| 메이크 2 |2015-01-01T00:오전 12:02.0000000Z |25000 |
| 메이크1 |2015-01-01T00:오전 12:03.0000000Z |26000 |
| 메이크 2 |2015-01-01T00:오전 12:04.0000000Z |25000 |
| 메이크1 |2015-01-01T00:오전 12:05.0000000Z |26000 |
| 메이크 2 |2015-01-01T00:오전 12:06.0000000Z |25000 |
| 메이크1 |2015-01-01T00:오전 12:07.0000000Z |26000 |
| 메이크 2 |2015-01-01T00:오전 12:08.0000000Z |2000 |

**출력**:

| Start_fault | End_fault |
| --- | --- |
| 2015-01-01T00:오전 12:02.000Z |2015-01-01T00:오전 12:07.000Z |

**쿼리**:

```SQL
WITH SelectPreviousEvent AS
(
SELECT
    *,
    LAG([time]) OVER (LIMIT DURATION(hour, 24)) as previous_time,
    LAG([weight]) OVER (LIMIT DURATION(hour, 24)) as previous_weight
FROM input TIMESTAMP BY [time]
)

SELECT 
    LAG(time) OVER (LIMIT DURATION(hour, 24) WHEN previous_weight < 20000 ) [Start_fault],
    previous_time [End_fault]
FROM SelectPreviousEvent
WHERE
    [weight] < 20000
    AND previous_weight > 20000
```
첫 번째 **SELECT** 문은 현재 중량 측정과 이전 측정의 상관 관계를 지정하여 현재 측정과 함께 투영합니다. 두 번째 **SELECT는** *previous_weight* 20000미만이고 현재 가중치가 20000보다 작고 현재 이벤트의 *previous_weight* 20000보다 큰 마지막 이벤트를 다시 살펴봅니다.

End_fault 이전 이벤트에 결함이 있는 현재 오류 없는 이벤트이며 Start_fault 그 이전의 마지막 결함이 없는 이벤트입니다.

## <a name="periodically-output-values"></a>주기적으로 출력값

불규칙하거나 누락된 이벤트의 경우 보다 희소한 데이터 입력에서 일반 간격 출력을 생성할 수 있습니다. 예를 들어, 가장 최근 표시된 데이터 지점을 보고하도록 5초마다 이벤트를 생성합니다.

**입력**:

| Time | 값 |
| --- | --- |
| "2014-01-01T06:01:00" |1 |
| "2014-01-01T06:오전 1:05" |2 |
| "2014-01-01T06:오전 1:10" |3 |
| "2014-01-01T06:오전 1:15" |4 |
| "2014-01-01T06:오전 1:30" |5 |
| "2014-01-01T06:오전 1:35" |6 |

**출력(처음 10개 행)**:

| Window_end | Last_event. 시간 | Last_event. 값 |
| --- | --- | --- |
| 2014-01-01T14:오전 1:00.000Z |2014-01-01T14:오전 1:00.000Z |1 |
| 2014-01-01T14:오전 1:05.000Z |2014-01-01T14:오전 1:05.000Z |2 |
| 2014-01-01T14:오전 1:10.000Z |2014-01-01T14:오전 1:10.000Z |3 |
| 2014-01-01T14:오전 1:15.000Z |2014-01-01T14:오전 1:15.000Z |4 |
| 2014-01-01T14:오전 1:20.000Z |2014-01-01T14:오전 1:15.000Z |4 |
| 2014-01-01T14:오전 1:25.000Z |2014-01-01T14:오전 1:15.000Z |4 |
| 2014-01-01T14:오전 1:30.000Z |2014-01-01T14:오전 1:30.000Z |5 |
| 2014-01-01T14:오전 1:35.000Z |2014-01-01T14:오전 1:35.000Z |6 |
| 2014-01-01T14:오전 1:40.000Z |2014-01-01T14:오전 1:35.000Z |6 |
| 2014-01-01T14:오전 1:45.000Z |2014-01-01T14:오전 1:35.000Z |6 |

**쿼리**:

```SQL
SELECT
    System.Timestamp() AS Window_end,
    TopOne() OVER (ORDER BY Time DESC) AS Last_event
FROM
    Input TIMESTAMP BY Time
GROUP BY
    HOPPINGWINDOW(second, 300, 5)
```

이 쿼리는 5초마다 이벤트를 생성하고 이전에 수신했던 마지막 이벤트를 출력합니다. **호핑창** 기간에는 쿼리가 최신 이벤트를 찾기 위해 얼마나 뒤로 보이는지 결정합니다.

자세한 내용은 [호핑 창을](/stream-analytics-query/hopping-window-azure-stream-analytics)참조하십시오.

## <a name="process-events-with-independent-time-substreams"></a>독립적인 시간으로 이벤트 처리(하위 스트림)

이벤트 생성자 간의 클록 스큐, 파티션 간 클럭 스큐 또는 네트워크 대기 시간으로 인해 이벤트가 늦게 도착하거나 순서대로 도착하지 못할 수 있습니다.
예를 들어 *TollID* 2의 장치 시계는 *TollID* 1보다 5초 뒤이고 *TollID* 3의 장치 시계는 *TollID* 1보다 10초 뒤에 있습니다. 계산은 자체 클럭 데이터만 타임스탬프로 고려하여 각 유료 요금에 대해 독립적으로 발생할 수 있습니다.

**입력**:

| LicensePlate | 계정을 | Time | TollId |
| --- | --- | --- | --- |
| DXE 5291 |메이크1 |2015-07-27T00:00:01.0000000Z | 1 |
| YHN 6970 |메이크 2 |2015-07-27T00:오전 12:05.0000000Z | 1 |
| QYF 9358 |메이크1 |2015-07-27T00:00:01.0000000Z | 2 |
| GXF 9462 |메이크 3 |2015-07-27T00:00:04.0000000Z | 2 |
| VFE 1616 |메이크 2 |2015-07-27T00:00:10.0000000Z | 1 |
| RMV 8282 |메이크1 |2015-07-27T00:00:03.0000000Z | 3 |
| MDR 6128 |메이크 3 |2015-07-27T00:00:11.0000000Z | 2 |
| YZK 5704 |메이크4 |2015-07-27T00:00:07.0000000Z | 3 |

**출력**:

| TollId | 개수 |
| --- | --- |
| 1 | 2 |
| 2 | 2 |
| 1 | 1 |
| 3 | 1 |
| 2 | 1 |
| 3 | 1 |

**쿼리**:

```SQL
SELECT
      TollId,
      COUNT(*) AS Count
FROM input
      TIMESTAMP BY Time OVER TollId
GROUP BY TUMBLINGWINDOW(second, 5), TollId
```

**TIMESTAMP OVER BY** 절은 하위 스트림을 사용하여 각 장치 타임라인을 독립적으로 살펴봅니다. 각 *TollID에* 대한 출력 이벤트는 계산될 때 생성되므로 모든 장치가 동일한 시계에 있는 것처럼 순서가 지정되지 않고 각 *TollID와* 관련하여 이벤트가 순서대로 정렬됩니다.

자세한 내용은 [타임스탬프 BY OVER](/stream-analytics-query/timestamp-by-azure-stream-analytics#over-clause-interacts-with-event-ordering)를 참조하십시오.

## <a name="remove-duplicate-events-in-a-window"></a>창에서 중복된 이벤트 제거

지정된 시간 범위에서 이벤트에 대한 평균 계산과 같은 작업을 수행하는 경우 중복 이벤트를 필터링해야 합니다. 다음 예제에서 두 번째 이벤트는 첫 번째 이벤트의 복제본입니다.

**입력**:  

| deviceId | Time | 특성 | 값 |
| --- | --- | --- | --- |
| 1 |2018-07-27T00:00:01.0000000Z |온도 |50 |
| 1 |2018-07-27T00:00:01.0000000Z |온도 |50 |
| 2 |2018-07-27T00:00:01.0000000Z |온도 |40 |
| 1 |2018-07-27T00:00:05.0000000Z |온도 |60 |
| 2 |2018-07-27T00:00:05.0000000Z |온도 |50 |
| 1 |2018-07-27T00:00:10.0000000Z |온도 |100 |

**출력**:  

| AverageValue | deviceId |
| --- | --- |
| 70 | 1 |
|45 | 2 |

**쿼리**:

```SQL
With Temp AS (
SELECT
    COUNT(DISTINCT Time) AS CountTime,
    Value,
    DeviceId
FROM
    Input TIMESTAMP BY Time
GROUP BY
    Value,
    DeviceId,
    SYSTEM.TIMESTAMP()
)

SELECT
    AVG(Value) AS AverageValue, DeviceId
INTO Output
FROM Temp
GROUP BY DeviceId,TumblingWindow(minute, 5)
```

**COUNT(DISTINCT Time)** 는 시간 범위 내에서 Time 열의 고유한 값 수를 반환합니다. 그런 다음 첫 번째 단계의 출력을 사용하여 중복을 삭제하여 장치당 평균을 계산할 수 있습니다.

자세한 내용은 [COUNT(고유 시간)를](/stream-analytics-query/count-azure-stream-analytics)참조하십시오.

## <a name="session-windows"></a>세션 윈도우

세션 창은 이벤트가 발생할 때 계속 확장되고 특정 시간 이후에 이벤트가 수신되지 않거나 창이 최대 기간에 도달하면 계산을 위해 닫히는 창입니다.
이 창은 사용자 상호 작용 데이터를 계산할 때 특히 유용합니다. 사용자가 시스템과 상호 작용을 시작하면 창이 시작되고 더 이상 이벤트가 관찰되지 않는 경우 닫힙닫히므로 사용자가 상호 작용을 중지했습니다.
예를 들어 사용자가 클릭 수가 기록되는 웹 페이지와 상호 작용하는 경우 세션 창을 사용하여 사용자가 사이트와 상호 작용한 기간을 확인할 수 있습니다.

**입력**:

| User_id | Time | URL |
| --- | --- | --- |
| 0 | 2017-01-26T00:00:00.0000000Z | "www.example.com/a.html" |
| 0 | 2017-01-26T00:00:20.0000000Z | "www.example.com/b.html" |
| 1 | 2017-01-26T00:00:55.00000000Z | "www.example.com/c.html" |
| 0 | 2017-01-26T00:01:10.00000000Z | "www.example.com/d.html" |
| 1 | 2017-01-26T00:01:15.00000000Z | "www.example.com/e.html" |

**출력**:

| User_id | StartTime | EndTime | Duration_in_seconds |
| --- | --- | --- | --- |
| 0 | 2017-01-26T00:00:00.0000000Z | 2017-01-26T00:01:10.00000000Z | 70 |
| 1 | 2017-01-26T00:00:55.00000000Z | 2017-01-26T00:01:15.00000000Z | 20 |

**쿼리**:

``` SQL
SELECT
    user_id,
    MIN(time) as StartTime,
    MAX(time) as EndTime,
    DATEDIFF(second, MIN(time), MAX(time)) AS duration_in_seconds
FROM input TIMESTAMP BY time
GROUP BY
    user_id,
    SessionWindow(minute, 1, 60) OVER (PARTITION BY user_id)
```

**SELECT는** 상호 작용 기간과 함께 사용자 상호 작용과 관련된 데이터를 투영합니다. 최대 창 크기가 60분인 1분 이내에 상호 작용이 발생하지 않으면 닫히는 사용자 및 **SessionWindow별로** 데이터를 그룹화합니다.

**세션창에**대한 자세한 내용은 [세션 창을](/stream-analytics-query/session-window-azure-stream-analytics) 참조하십시오.

## <a name="language-extensibility-with-user-defined-function-in-javascript-and-c"></a>자바 스크립트 및 C에서 사용자 정의 기능을 가진 언어 확장성 #

Azure Stream Analytics 쿼리 언어는 JavaScript 또는 C# 언어로 작성된 사용자 지정 함수로 확장할 수 있습니다. UDF(사용자 정의 함수)는 **SQL** 언어를 사용하여 쉽게 표현할 수 없는 사용자 지정/복잡한 계산입니다. 이러한 UdF는 한 번 정의할 수 있으며 쿼리 내에서 여러 번 사용할 수 있습니다. 예를 들어 UDF를 사용하여 헥사데피mal *nvarchar(max)* 값을 *큰* 값으로 변환할 수 있습니다.

**입력**:

| Device_id | 헥스 밸류에이치 |
| --- | --- |
| 1 | "B4" |
| 2 | "11B" |
| 3 | "121" |

**출력**:

| Device_id | Decimal |
| --- | --- |
| 1 | 180 |
| 2 | 283 |
| 3 | 289 |

```JavaScript
function hex2Int(hexValue){
    return parseInt(hexValue, 16);
}
```

```C#
public static class MyUdfClass {
    public static long Hex2Int(string hexValue){
        return int.Parse(hexValue, System.Globalization.NumberStyles.HexNumber);
    }
}
```

```SQL
SELECT
    Device_id,
    udf.Hex2Int(HexValue) AS Decimal
From
    Input
```

사용자 정의 함수는 소비된 모든 이벤트에 대해 HexValue의 *bigint* 값을 계산합니다.

자세한 내용은 [자바스크립트](/azure/stream-analytics/stream-analytics-javascript-user-defined-functions) 및 [C#을](/azure/stream-analytics/stream-analytics-edge-csharp-udf)참조하십시오.

## <a name="advanced-pattern-matching-with-match_recognize"></a>MATCH_RECOGNIZE 고급 패턴 매칭

**MATCH_RECOGNIZE** 잘 정의된 정규식 패턴에 일련의 이벤트를 일치시키는 데 사용할 수 있는 고급 패턴 일치 메커니즘입니다.
예를 들어 ATM을 작동 하는 동안 관리자에게 통보해야 하는 두 개의 연속경고 메시지가 있는 경우 ATM이 실시간으로 모니터링됩니다.

**입력**:

| ATM_id | Operation_id | Return_Code | Time |
| --- | --- | --- | --- |
| 1 | "핀 입력" | "Success" | 2017-01-26T00:10:00.00000000Z |
| 2 | "오픈 머니 슬롯" | "Success" | 2017-01-26T00:10:07.00000000Z |
| 2 | "클로징 머니 슬롯" | "Success" | 2017-01-26T00:10:11.00000000Z |
| 1 | "인출 수량 입력" | "Success" | 2017-01-26T00:10:08.00000000Z |
| 1 | "오픈 머니 슬롯" | "경고" | 2017-01-26T00:10:14.00000000Z |
| 1 | "은행 잔액 인쇄" | "경고" | 2017-01-26T00:10:19.00000000Z |

**출력**:

| ATM_id | First_Warning_Operation_id | Warning_Time |
| --- | --- | --- |
| 1 | "오픈 머니 슬롯" | 2017-01-26T00:10:14.00000000Z |

```SQL
SELECT *
FROM intput TIMESTAMP BY time OVER ATM_id
MATCH_RECOGNIZE (
    PARTITON BY ATM_id
    LIMIT DURATION(minute, 1)
    MEASURES
        First(Warning.ATM_id) AS ATM_id,
        First(Warning.Operation_Id) AS First_Warning_Operation_id,
        First(Warning.Time) AS Warning_Time
    AFTER MATCH SKIP TO NEXT ROW
    PATTERN (Success* Warning{2,})
    DEFINE
        Success AS Succes.Return_Code = 'Success',
        Failure AS Warning.Return_Code <> 'Success'
) AS patternMatch
```

이 쿼리는 두 개 이상의 연속실패 이벤트를 일치시키고 조건이 충족되면 경보를 생성합니다.
**PATTERN은** 일치에 사용할 정규식을 정의합니다(이 경우 성공한 작업의 수에 관계없이 두 번 이상의 연속 실패가 뒤따릅니다.)
성공과 실패는 Return_Code 값을 사용하여 정의되며 조건이 충족되면 **측정값은** *ATM_id,* 첫 번째 경고 작업 및 첫 번째 경고 시간으로 투영됩니다.

자세한 내용은 [MATCH_RECOGNIZE](/stream-analytics-query/match-recognize-stream-analytics)을 참조하십시오.

## <a name="geofencing-and-geospatial-queries"></a>지오펜싱 및 지리 공간 쿼리
Azure Stream Analytics는 차량 관리, 차량 공유, 커넥티드 카 및 자산 추적과 같은 시나리오를 구현하는 데 사용할 수 있는 기본 제공 지리 공간 함수를 제공합니다.
지리 공간 데이터는 이벤트 스트림 또는 참조 데이터의 일부로 GeoJSON 또는 WKT 형식으로 수집할 수 있습니다.
예를 들어, 여권 인쇄를 위한 제조 기계 전문 회사인 회사는 기계를 정부 및 영사관에 임대합니다. 이러한 기계의 위치는 여권 위조에 대한 잘못된 배치 및 가능한 사용을 피하기 위해 엄격하게 제어됩니다. 각 컴퓨터에는 GPS 추적기가 장착되어 있으며 해당 정보는 Azure Stream Analytics 작업으로 다시 릴레이됩니다.
제조 는 그 기계의 위치를 추적하고 그들 중 하나가 허가 된 영역을 떠날 경우 경고하고 싶습니다, 이 방법은 원격으로 비활성화 할 수 있습니다, 당국에 경고하고 장비를 검색 할 수 있습니다.

**입력**:

| Equipment_id | Equipment_current_location | Time |
| --- | --- | --- |
| 1 | "POINT(-122.13288777982818 47.64082002051315)" | 2017-01-26T00:10:00.00000000Z |
| 1 | "POINT(-122.13307252987875 47.64081350934929)" | 2017-01-26T00:11:00.00000000Z |
| 1 | "POINT(-122.13308862313283 47.6406508603241)" | 2017-01-26T00:12:00.00000000Z |
| 1 | "POINT(-122.13341048821462 47.64043760861279)" | 2017-01-26T00:13:00.00000000Z |

**참조 데이터 입력**:

| Equipment_id | Equipment_lease_location |
| --- | --- |
| 1 | "POLYGON(-122.1326028450979 47.6409838666794,-122.13266666794,-122.386794,-122.132665544 1 47.64061471602751,-122.13326028450979 47.6406146020228440994)" |

**출력**:

| Equipment_id | Equipment_alert_location | Time |
| --- | --- | --- |
| 1 | "POINT(-122.13341048821462 47.64043760861279)" | 2017-01-26T00:13:00.00000000Z |

```SQL
SELECT
    input.Equipment_id AS Equipment_id,
    input.Equipment_current_location AS Equipment_current_location,
    input.Time AS Time
FROM input TIMESTAMP BY time
JOIN
    referenceInput 
    ON input.Equipment_id = referenceInput.Equipment_id
    WHERE 
        ST_WITHIN(input.Equipment_currenct_location, referenceInput.Equipment_lease_location) = 1
```

쿼리를 통해 제조업체는 기계 위치를 자동으로 모니터링하여 컴퓨터가 허용된 지오펜스를 떠날 때 경고를 받을 수 있습니다. 내장 된 지리 공간 기능을 사용하면 타사 라이브러리없이 쿼리 내에서 GPS 데이터를 사용할 수 있습니다.

자세한 내용은 Azure Stream Analytics 문서를 사용하여 [지오펜싱 및 지리 공간 집계 시나리오를](geospatial-scenarios.md) 참조하십시오.

## <a name="get-help"></a>도움말 보기

추가 지원은 [Azure 스트림 분석 포럼을](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)참조하십시오.

## <a name="next-steps"></a>다음 단계
* [Azure Stream Analytics 소개](stream-analytics-introduction.md)
* [Azure 스트림 분석 사용 시작](stream-analytics-real-time-fraud-detection.md)
* [Azure  Stream Analytics 작업 규모 지정](stream-analytics-scale-jobs.md)
* [Azure 스트림 분석 쿼리 언어 참조](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Stream Analytics 관리 REST API 참조](https://msdn.microsoft.com/library/azure/dn835031.aspx)
