---
title: Azure Stream Analytics의 일반적인 쿼리 패턴
description: 이 문서에서는 Azure Stream Analytics 작업에 유용한 몇 가지 일반적인 쿼리 패턴 및 디자인에 대해 설명 합니다.
services: stream-analytics
author: rodrigoaatmicrosoft
ms.author: rodrigoa
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/18/2019
ms.openlocfilehash: 54d1b640a4067cf65fc28501840b4926455ec259
ms.sourcegitcommit: 3eb0cc8091c8e4ae4d537051c3265b92427537fe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/11/2020
ms.locfileid: "75903451"
---
# <a name="common-query-patterns-in-azure-stream-analytics"></a>Azure Stream Analytics의 일반적인 쿼리 패턴

Azure Stream Analytics에서 쿼리는 SQL 방식 쿼리 언어로 표현됩니다. 언어 구문은 [Stream Analytics 쿼리 언어 참조](/stream-analytics-query/stream-analytics-query-language-reference) 가이드에 설명되어 있습니다. 

쿼리 디자인은 단일 입력 스트림에서 이벤트 데이터를 출력 데이터 저장소로 이동 하는 간단한 통과 논리를 표현할 수 있으며, 다양 한 패턴 일치 및 임시 분석을 수행 하 여 Stream Analytics 가이드를 [사용 하 여 IoT 솔루션 빌드](stream-analytics-build-an-iot-solution-using-stream-analytics.md) 에서와 같이 다양 한 기간에 대 한 집계를 계산할 수 있습니다. 여러 입력의 데이터를 조인 하 여 스트리밍 이벤트를 결합할 수 있으며, 정적 참조 데이터에 대해 조회를 수행 하 여 이벤트 값을 보강할 수 있습니다. 여러 출력에 데이터를 쓸 수도 있습니다.

이 문서에서는 실제 시나리오에 따른 몇 가지 일반적인 쿼리 패턴에 대 한 솔루션을 간략하게 설명 합니다.

## <a name="supported-data-formats"></a>지원 되는 데이터 형식

Azure Stream Analytics는 CSV, JSON 및 Avro 데이터 형식의 이벤트 처리를 지원합니다.

JSON 및 Avro는 둘 다 중첩된 개체(레코드) 또는 배열과 같은 복합 형식을 포함할 수 있습니다. 이러한 복합 데이터 형식을 사용 하는 방법에 대 한 자세한 내용은 [JSON 및 AVRO 데이터 구문 분석](stream-analytics-parsing-json.md) 문서를 참조 하세요.

## <a name="simple-pass-through-query"></a>단순 통과 쿼리

간단한 통과 쿼리를 사용 하 여 입력 스트림 데이터를 출력에 복사할 수 있습니다. 예를 들어 문자 분석을 위해 실시간 차량 정보를 포함 하는 데이터 스트림을 SQL 데이터베이스에 저장 해야 하는 경우 간단한 통과 쿼리를 통해 작업을 수행 합니다.

**입력**:

| 만들기 | 시간 | 무게 |
| --- | --- | --- |
| Make1 |2015-01-01T00:00:01.0000000 Z |"1000" |
| Make1 |2015-01-01T00:00:02.0000000 Z |"2000" |

**출력**:

| 만들기 | 시간 | 무게 |
| --- | --- | --- |
| Make1 |2015-01-01T00:00:01.0000000 Z |"1000" |
| Make1 |2015-01-01T00:00:02.0000000 Z |"2000" |

**쿼리**:

```SQL
SELECT
    *
INTO Output
FROM Input
```

**SELECT** * 쿼리는 들어오는 이벤트의 모든 필드를 프로젝션 하 고 출력으로 보냅니다. 마찬가지로 **SELECT** 를 사용 하 여 입력에서 필요한 필드를 프로젝션 할 수도 있습니다. 이 예 *에서는 차량 및* *시간이* 저장 해야 하는 유일한 필수 필드인 경우 해당 필드를 **SELECT** 문에 지정할 수 있습니다.

**입력**:

| 만들기 | 시간 | 무게 |
| --- | --- | --- |
| Make1 |2015-01-01T00:00:01.0000000 Z |1000 |
| Make1 |2015-01-01T00:00:02.0000000 Z |2000 |
| Make2 |2015-01-01T00:00:04.0000000 Z |1500 |

**출력**:

| 만들기 | 시간 |
| --- | --- |
| Make1 |2015-01-01T00:00:01.0000000 Z |
| Make1 |2015-01-01T00:00:02.0000000 Z |
| Make2 |2015-01-01T00:00:04.0000000 Z |

**쿼리**:

```SQL
SELECT
    Make, Time
INTO Output
FROM Input
```
## <a name="data-aggregation-over-time"></a>시간별 데이터 집계

시간 창에 대 한 정보를 계산 하기 위해 데이터를 함께 집계할 수 있습니다. 이 예에서는 특정 자동차를 만들 때마다 지난 10 분 동안 카운트를 계산 합니다.

**입력**:

| 만들기 | 시간 | 무게 |
| --- | --- | --- |
| Make1 |2015-01-01T00:00:01.0000000 Z |1000 |
| Make1 |2015-01-01T00:00:02.0000000 Z |2000 |
| Make2 |2015-01-01T00:00:04.0000000 Z |1500 |

**출력**:

| 만들기 | 카운트 |
| --- | --- |
| Make1 | 2 |
| Make2 | 1 |

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

이 집계는 자동차를 *만들어* 10 초 마다 계산 합니다. 출력에는 전화를 통해 *수행* 된 자동차의 *수와 수가* 있습니다.

TumblingWindow는 이벤트를 그룹화 하는 데 사용 되는 기간 이동 함수입니다. 그룹화 된 모든 이벤트에 대해 집계를 적용할 수 있습니다. 자세한 내용은 [창 고 함수](stream-analytics-window-functions.md)를 참조 하세요.

집계에 대 한 자세한 내용은 [집계 함수](/stream-analytics-query/aggregate-functions-azure-stream-analytics)를 참조 하세요.

## <a name="data-conversion"></a>데이터 변환

**Cast** 메서드를 사용 하 여 실시간으로 데이터를 캐스팅할 수 있습니다. 예를 들어 자동차 가중치는 **nvarchar (max)** 형식에서 **bigint** 형식으로 변환 되 고 숫자 계산에 사용 될 수 있습니다.

**입력**:

| 만들기 | 시간 | 무게 |
| --- | --- | --- |
| Make1 |2015-01-01T00:00:01.0000000 Z |"1000" |
| Make1 |2015-01-01T00:00:02.0000000 Z |"2000" |

**출력**:

| 만들기 | 무게 |
| --- | --- |
| Make1 |3000 |

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

**CAST** 문을 사용 하 여 해당 데이터 형식을 지정 합니다. 데이터 형식에서 지원 되는 데이터 형식 목록 [(Azure Stream Analytics)](/stream-analytics-query/data-types-azure-stream-analytics)을 참조 하세요.

[데이터 변환 함수](/stream-analytics-query/conversion-functions-azure-stream-analytics)에 대 한 자세한 내용은을 (를).

## <a name="string-matching-with-like-and-not-like"></a>LIKE와 NOT LIKE와 문자열 일치

**Like** 및 **NOT like** 는 필드가 특정 패턴과 일치 하는지 확인 하는 데 사용할 수 있습니다. 예를 들어 ' A ' 문자로 시작 하 고 숫자 9로 끝나는 라이선스 판을 반환 하도록 필터를 만들 수 있습니다.

**입력**:

| 만들기 | License_plate | 시간 |
| --- | --- | --- |
| Make1 |ABC-123 |2015-01-01T00:00:01.0000000 Z |
| Make2 |AAA-999 |2015-01-01T00:00:02.0000000 Z |
| Make3 |ABC-369 |2015-01-01T00:00:03.0000000 Z |

**출력**:

| 만들기 | License_plate | 시간 |
| --- | --- | --- |
| Make2 |AAA-999 |2015-01-01T00:00:02.0000000 Z |
| Make3 |ABC-369 |2015-01-01T00:00:03.0000000 Z |

**쿼리**:

```SQL
SELECT
    *
FROM
    Input TIMESTAMP BY Time
WHERE
    License_plate LIKE 'A%9'
```

**LIKE** 문을 사용 하 여 **License_plate** 필드 값을 확인 합니다. 문자 ' A '로 시작 하 고 0 개 이상의 문자로 이루어진 문자열을 포함 하 여 숫자 9로 끝나야 합니다.

## <a name="specify-logic-for-different-casesvalues-case-statements"></a>다른 사례/값(CASE 문)에 대한 논리를 지정합니다.

**CASE** 문은 특정 조건에 따라 여러 필드에 대해 다른 계산을 제공할 수 있습니다. 예를 들어 레인 ' A '를 *Make1* 및 레인 ' B '의 자동차에 할당 합니다.

**입력**:

| 만들기 | 시간 |
| --- | --- |
| Make1 |2015-01-01T00:00:01.0000000 Z |
| Make2 |2015-01-01T00:00:02.0000000 Z |
| Make2 |2015-01-01T00:00:03.0000000 Z |

**출력**:

| 만들기 |Dispatch_to_lane | 시간 |
| --- | --- | --- |
| Make1 |은 |2015-01-01T00:00:01.0000000 Z |
| Make2 |"B" |2015-01-01T00:00:02.0000000 Z |

**솔루션**:

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

**CASE** 식은 식을 일련의 단순 식과 비교 하 여 결과를 확인 합니다. 이 예제에서 *Make1* 의 차량은 레인 ' A '로 발송 되 고 다른 모든 자동차는 레인 ' B '로 할당 됩니다.

자세한 내용은 [case 식](/stream-analytics-query/case-azure-stream-analytics)을 참조 하세요.

## <a name="send-data-to-multiple-outputs"></a>데이터를 여러 출력으로 전송

여러 **SELECT** 문을 사용 하 여 데이터를 다른 출력 싱크로 출력할 수 있습니다. 예를 들어 한 **SELECT** 는 임계값 기반 경고를 출력 하 고 다른 하나는 이벤트를 blob 저장소에 출력할 수 있습니다.

**입력**:

| 만들기 | 시간 |
| --- | --- |
| Make1 |2015-01-01T00:00:01.0000000 Z |
| Make1 |2015-01-01T00:00:02.0000000 Z |
| Make2 |2015-01-01T00:00:01.0000000 Z |
| Make2 |2015-01-01T00:00:02.0000000 Z |
| Make2 |2015-01-01T00:00:03.0000000 Z |

**출력 ArchiveOutput**:

| 만들기 | 시간 |
| --- | --- |
| Make1 |2015-01-01T00:00:01.0000000 Z |
| Make1 |2015-01-01T00:00:02.0000000 Z |
| Make2 |2015-01-01T00:00:01.0000000 Z |
| Make2 |2015-01-01T00:00:02.0000000 Z |
| Make2 |2015-01-01T00:00:03.0000000 Z |

**출력 alertoutput**:

| 만들기 | 시간 | 카운트 |
| --- | --- | --- |
| Make2 |2015-01-01T00:00:10.0000000 Z |3 |

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

**INTO** 절은 데이터를 쓸 출력 Stream Analytics를 알려 줍니다. 첫 번째 **SELECT** 는 입력에서 데이터를 받아 **ArchiveOutput**이라는 출력으로 전송 하는 통과 쿼리를 정의 합니다. 두 번째 쿼리는 결과를 **alertoutput**이라는 다운스트림 경고 시스템 출력으로 보내기 전에 몇 가지 간단한 집계 및 필터링을 수행 합니다.

**WITH** 절을 사용 하 여 여러 하위 쿼리 블록을 정의할 수 있습니다. 이 옵션을 선택 하면 입력 소스에서 판독기 수를 줄일 수 있습니다.

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

자세한 내용은 [ **WITH** 절](/stream-analytics-query/with-azure-stream-analytics)을 참조 하세요.

## <a name="count-unique-values"></a>고유 값 카운트

**Count** 및 **DISTINCT** 는 시간 창 내에서 스트림에 나타나는 고유 필드 값의 수를 계산 하는 데 사용할 수 있습니다. 2 초 창에서 요금 창구를 통과 하는 *고유한 자동차* 의 수를 계산 하는 쿼리를 만들 수 있습니다.

**입력**:

| 만들기 | 시간 |
| --- | --- |
| Make1 |2015-01-01T00:00:01.0000000 Z |
| Make1 |2015-01-01T00:00:02.0000000 Z |
| Make2 |2015-01-01T00:00:01.0000000 Z |
| Make2 |2015-01-01T00:00:02.0000000 Z |
| Make2 |2015-01-01T00:00:03.0000000 Z |

**출력:**

| Count_make | 시간 |
| --- | --- |
| 2 |2015-01-01T00:00:02.000 Z |
| 1 |2015-01-01T00:00:04.000 Z |

**쿼리:**

```SQL
SELECT
     COUNT(DISTINCT Make) AS Count_make,
     System.TIMESTAMP() AS Time
FROM Input TIMESTAMP BY TIME
GROUP BY 
     TumblingWindow(second, 2)
```

**COUNT (DISTINCT)** 는 특정 시간 창에 있는 **만들기** 열의 고유 값 수를 반환 합니다.
자세한 내용은 [ **COUNT** 집계 함수](/stream-analytics-query/count-azure-stream-analytics)를 참조 하세요.

## <a name="calculation-over-past-events"></a>과거 이벤트에 대 한 계산

**LAG** 함수를 사용 하 여 시간 창 내에서 과거 이벤트를 확인 하 고 현재 이벤트와 비교할 수 있습니다. 예를 들어, 현재 자동차는 전화를 통해 마지막으로 수행 된 자동차와 다른 경우에는 출력 될 수 있습니다.

**입력**:

| 만들기 | 시간 |
| --- | --- |
| Make1 |2015-01-01T00:00:01.0000000 Z |
| Make2 |2015-01-01T00:00:02.0000000 Z |

**출력**:

| 만들기 | 시간 |
| --- | --- |
| Make2 |2015-01-01T00:00:02.0000000 Z |

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

**LAG** 를 사용 하 여 입력 스트림을 다시 피킹 하 고,이 값을 검색 한 후 현재 이벤트 *의 값과* 비교 *하 여 이벤트* 를 출력 합니다.

자세한 내용은 [**지연**](/stream-analytics-query/lag-azure-stream-analytics)을 참조 하세요.

## <a name="retrieve-the-first-event-in-a-window"></a>창에서 첫 번째 이벤트 검색

**Isfirst** 는 시간 창에서 첫 번째 이벤트를 검색 하는 데 사용할 수 있습니다. 예를 들어 10 분 간격으로 첫 번째 자동차 정보를 출력 합니다.

**입력**:

| License_plate | 만들기 | 시간 |
| --- | --- | --- |
| DXE 5291 |Make1 |2015-07-27T00:00:05.0000000 Z |
| YZK 5704 |Make3 |2015-07-27T00:02:17.0000000 Z |
| RMV 8282 |Make1 |2015-07-27T00:05:01.0000000 Z |
| YHN 6970 |Make2 |2015-07-27T00:06:00.0000000 Z |
| VFE 1616 |Make2 |2015-07-27T00:09:31.0000000 Z |
| QYF 9358 |Make1 |2015-07-27T00:12:02.0000000 Z |
| MDR 6128 |Make4 |2015-07-27T00:13:45.0000000 Z |

**출력**:

| License_plate | 만들기 | 시간 |
| --- | --- | --- |
| DXE 5291 |Make1 |2015-07-27T00:00:05.0000000 Z |
| QYF 9358 |Make1 |2015-07-27T00:12:02.0000000 Z |

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

**Isfirst** 는 데이터를 분할 하 고 각 *특정 자동차에* 대해 10 분 간격으로 첫 번째 이벤트를 계산할 수도 있습니다.

**출력**:

| License_plate | 만들기 | 시간 |
| --- | --- | --- |
| DXE 5291 |Make1 |2015-07-27T00:00:05.0000000 Z |
| YZK 5704 |Make3 |2015-07-27T00:02:17.0000000 Z |
| YHN 6970 |Make2 |2015-07-27T00:06:00.0000000 Z |
| QYF 9358 |Make1 |2015-07-27T00:12:02.0000000 Z |
| MDR 6128 |Make4 |2015-07-27T00:13:45.0000000 Z |

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

자세한 내용은 [**Isfirst**](/stream-analytics-query/isfirst-azure-stream-analytics)를 참조 하세요.

## <a name="return-the-last-event-in-a-window"></a>창에서 마지막 이벤트 반환

이벤트가 실시간으로 시스템에서 사용 되는 경우 해당 기간 동안 이벤트가 마지막으로 도착할 지 여부를 결정할 수 있는 함수가 없습니다. 이를 위해 입력 스트림은 이벤트 시간이 해당 창의 모든 이벤트에 대 한 최대 시간인 다른에 조인 해야 합니다.

**입력**:

| License_plate | 만들기 | 시간 |
| --- | --- | --- |
| DXE 5291 |Make1 |2015-07-27T00:00:05.0000000 Z |
| YZK 5704 |Make3 |2015-07-27T00:02:17.0000000 Z |
| RMV 8282 |Make1 |2015-07-27T00:05:01.0000000 Z |
| YHN 6970 |Make2 |2015-07-27T00:06:00.0000000 Z |
| VFE 1616 |Make2 |2015-07-27T00:09:31.0000000 Z |
| QYF 9358 |Make1 |2015-07-27T00:12:02.0000000 Z |
| MDR 6128 |Make4 |2015-07-27T00:13:45.0000000 Z |

**출력**:

| License_plate | 만들기 | 시간 |
| --- | --- | --- |
| VFE 1616 |Make2 |2015-07-27T00:09:31.0000000 Z |
| MDR 6128 |Make4 |2015-07-27T00:13:45.0000000 Z |

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

쿼리의 첫 번째 단계는 10 분 동안 최대 타임 스탬프를 찾습니다 .이 타임 스탬프는 해당 창에 대 한 마지막 이벤트의 타임 스탬프입니다. 두 번째 단계에서는 첫 번째 쿼리의 결과를 원래 스트림과 조인 하 여 각 창에서 마지막 타임 스탬프와 일치 하는 이벤트를 찾습니다. 

**DATEDIFF** 는 두 날짜/시간 필드 간의 시간 차이를 비교 하 고 반환 하는 날짜 관련 함수입니다. 자세한 내용은 [date 함수](https://docs.microsoft.com/stream-analytics-query/date-and-time-functions-azure-stream-analytics)를 참조 하세요.

스트림 조인에 대 한 자세한 내용은 [**조인**](/stream-analytics-query/join-azure-stream-analytics)을 참조 하세요.


## <a name="correlate-events-in-a-stream"></a>스트림의 이벤트 상관 관계

**LAG** 함수를 사용 하 여 과거 이벤트를 살펴보면 동일한 스트림의 이벤트 상관 관계를 지정할 수 있습니다. 예를 들어 동일한 *작업* 에서 두 개의 연속 된 자동차가 지난 90 초 동안의 요금을 통과할 때마다 출력이 생성 될 수 있습니다.

**입력**:

| 만들기 | License_plate | 시간 |
| --- | --- | --- |
| Make1 |ABC-123 |2015-01-01T00:00:01.0000000 Z |
| Make1 |AAA-999 |2015-01-01T00:00:02.0000000 Z |
| Make2 |DEF-987 |2015-01-01T00:00:03.0000000 Z |
| Make1 |GHI-345 |2015-01-01T00:00:04.0000000 Z |

**출력**:

| 만들기 | 시간 | Current_car_license_plate | First_car_license_plate | First_car_time |
| --- | --- | --- | --- | --- |
| Make1 |2015-01-01T00:00:02.0000000 Z |AAA-999 |ABC-123 |2015-01-01T00:00:01.0000000 Z |

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

**LAG** 함수는 이벤트의 입력 스트림을 다시 확인 하 고 *설정* 값을 검색 하 여 현재 이벤트의 *설정* 값과 비교할 수 있습니다.  조건을 충족 하면 **SELECT** 문의 **LAG** 를 사용 하 여 이전 이벤트의 데이터를 예측할 수 있습니다.

자세한 내용은 [지연](/stream-analytics-query/lag-azure-stream-analytics)을 참조 하세요.

## <a name="detect-the-duration-between-events"></a>이벤트 간 기간 검색

이벤트 기간은 끝 이벤트가 수신 되 면 마지막 시작 이벤트를 확인 하 여 계산할 수 있습니다. 이 쿼리는 사용자가 페이지 또는 기능에 대해 소비한 시간을 확인 하는 데 유용할 수 있습니다.

**입력**:  

| 사용자 | 기능 | 이벤트 | 시간 |
| --- | --- | --- | --- |
| user@location.com |RightMenu |시작 |2015-01-01T00:00:01.0000000 Z |
| user@location.com |RightMenu |종료 |2015-01-01T00:00:08.0000000 Z |

**출력**:  

| 사용자 | 기능 | 기간 |
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

**마지막** 함수를 사용 하 여 특정 조건 내에서 마지막 이벤트를 검색할 수 있습니다. 이 예제에서 조건은 Start 형식의 이벤트로, 사용자별 **로** 검색 분할 및 기능을 분할 합니다. 그러면 모든 사용자 및 기능이 시작 이벤트를 검색할 때 독립적으로 처리 됩니다. **제한 기간은** 종료 이벤트와 시작 이벤트 사이에서 검색을 다시 1 시간으로 제한 합니다.

## <a name="detect-the-duration-of-a-condition"></a>조건의 기간 감지

여러 이벤트를 사용 하는 조건의 경우 **LAG** 함수를 사용 하 여 해당 조건의 기간을 식별할 수 있습니다. 예를 들어 버그로 인해 모든 자동차의 중량이 잘못되었고(20.000파운드 이상) 버그의 지속 시간을 계산해야 한다고 가정합니다.

**입력**:

| 만들기 | 시간 | 무게 |
| --- | --- | --- |
| Make1 |2015-01-01T00:00:01.0000000 Z |2000 |
| Make2 |2015-01-01T00:00:02.0000000 Z |25000 |
| Make1 |2015-01-01T00:00:03.0000000 Z |26000 |
| Make2 |2015-01-01T00:00:04.0000000 Z |25000 |
| Make1 |2015-01-01T00:00:05.0000000 Z |26000 |
| Make2 |2015-01-01T00:00:06.0000000 Z |25000 |
| Make1 |2015-01-01T00:00:07.0000000 Z |26000 |
| Make2 |2015-01-01T00:00:08.0000000 Z |2000 |

**출력**:

| Start_fault | End_fault |
| --- | --- |
| 2015-01-01T00:00:02.000 Z |2015-01-01T00:00:07.000 Z |

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
첫 번째 **SELECT** 문은 현재 가중치와 이전 측정의 상관 관계를 계산 하 여 현재 측정과 함께 측정 합니다. 두 번째 **SELECT** 는 *previous_weight* 이 2만 보다 작은 마지막 이벤트를 다시 찾습니다. 여기서 현재 가중치는 2만 보다 작고 현재 이벤트의 *previous_weight* 2만 보다 큽니다.

End_fault은 이전 이벤트에 오류가 발생 한 현재 잘못 된 이벤트 이며 Start_fault 이전에 오류가 발생 하지 않은 마지막 이벤트입니다.

## <a name="periodically-output-values"></a>정기적으로 값 출력

비정상적인 이벤트 또는 누락 된 이벤트의 경우 보다 스파스 데이터 입력에서 정기적인 간격 출력이 생성 될 수 있습니다. 예를 들어, 가장 최근 표시된 데이터 지점을 보고하도록 5초마다 이벤트를 생성합니다.

**입력**:

| 시간 | 값 |
| --- | --- |
| "2014-01-01T06:01:00" |1 |
| "2014-01-01T06:오전 1:05" |2 |
| "2014-01-01T06:오전 1:10" |3 |
| "2014-01-01T06:오전 1:15" |4 |
| "2014-01-01T06:오전 1:30" |5 |
| "2014-01-01T06:오전 1:35" |6 |

**출력(처음 10개 행)** :

| Window_end | Last_event. 런타임 | Last_event. 기본값 |
| --- | --- | --- |
| 2014-01-01T14:01:00.000 Z |2014-01-01T14:01:00.000 Z |1 |
| 2014-01-01T14:01:05.000 Z |2014-01-01T14:01:05.000 Z |2 |
| 2014-01-01T14:01:10.000 Z |2014-01-01T14:01:10.000 Z |3 |
| 2014-01-01T14:01:15.000 Z |2014-01-01T14:01:15.000 Z |4 |
| 2014-01-01T14:01:20.000 Z |2014-01-01T14:01:15.000 Z |4 |
| 2014-01-01T14:01:25.000 Z |2014-01-01T14:01:15.000 Z |4 |
| 2014-01-01T14:01:30.000 Z |2014-01-01T14:01:30.000 Z |5 |
| 2014-01-01T14:01:35.000 Z |2014-01-01T14:01:35.000 Z |6 |
| 2014-01-01T14:01:40.000 Z |2014-01-01T14:01:35.000 Z |6 |
| 2014-01-01T14:01:45.000 Z |2014-01-01T14:01:35.000 Z |6 |

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

이 쿼리는 5초마다 이벤트를 생성하고 이전에 수신했던 마지막 이벤트를 출력합니다. **HOPPINGWINDOW** 기간은 쿼리를 통해 최신 이벤트를 찾는 정도를 결정 합니다.

자세한 내용은 [도약 창](/stream-analytics-query/hopping-window-azure-stream-analytics)을 참조 하세요.

## <a name="process-events-with-independent-time-substreams"></a>독립 된 시간을 사용 하 여 이벤트 처리 (Substreams)

이벤트 생성자 간의 클록 스큐, 파티션 간 클럭 스큐 또는 네트워크 대기 시간으로 인해 이벤트가 늦게 도착하거나 순서대로 도착하지 못할 수 있습니다.
예를 들어 *TollID* 2에 대 한 장치 클록은 *TollID* 1 보다 5 초 뒤 이며 *TollID* 3의 장치 클록은 *TollID* 1 보다 10 초입니다. 각 요금에 대해 계산을 독립적으로 수행 하 여 고유한 클록 데이터만 타임 스탬프로 간주 합니다.

**입력**:

| LicensePlate | 만들기 | 시간 | TollId |
| --- | --- | --- | --- |
| DXE 5291 |Make1 |2015-07-27T00:00:01.0000000 Z | 1 |
| YHN 6970 |Make2 |2015-07-27T00:00:05.0000000 Z | 1 |
| QYF 9358 |Make1 |2015-07-27T00:00:01.0000000 Z | 2 |
| GXF 9462 |Make3 |2015-07-27T00:00:04.0000000 Z | 2 |
| VFE 1616 |Make2 |2015-07-27T00:00:10.0000000 Z | 1 |
| RMV 8282 |Make1 |2015-07-27T00:00:03.0000000 Z | 3 |
| MDR 6128 |Make3 |2015-07-27T00:00:11.0000000 Z | 2 |
| YZK 5704 |Make4 |2015-07-27T00:00:07.0000000 Z | 3 |

**출력**:

| TollId | 카운트 |
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

**TIMESTAMP OVER** 절은 substreams를 사용 하 여 각 장치 타임 라인을 독립적으로 확인 합니다. 각 *TollID* 에 대 한 출력 이벤트는 계산 될 때 생성 됩니다. 즉, 모든 장치가 동일한 클록에 있는 것 처럼 순서가 다시 정렬 되는 대신 각 *TollID* 를 기준으로 이벤트가 정렬 됩니다.

자세한 내용은 [초과 타임 스탬프](/stream-analytics-query/timestamp-by-azure-stream-analytics#over-clause-interacts-with-event-ordering)를 참조 하세요.

## <a name="remove-duplicate-events-in-a-window"></a>창에서 중복된 이벤트 제거

지정된 시간 범위에서 이벤트에 대한 평균 계산과 같은 작업을 수행하는 경우 중복 이벤트를 필터링해야 합니다. 다음 예제에서 두 번째 이벤트는 첫 번째 이벤트와 중복 됩니다.

**입력**:  

| deviceId | 시간 | attribute | 값 |
| --- | --- | --- | --- |
| 1 |2018-07-27T00:00:01.0000000 Z |온도 |50 |
| 1 |2018-07-27T00:00:01.0000000 Z |온도 |50 |
| 2 |2018-07-27T00:00:01.0000000 Z |온도 |40 |
| 1 |2018-07-27T00:00:05.0000000 Z |온도 |60 |
| 2 |2018-07-27T00:00:05.0000000 Z |온도 |50 |
| 1 |2018-07-27T00:00:10.0000000 Z |온도 |100 |

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

**COUNT (Distinct time)** 는 시간 열의 시간 열에 있는 고유 값의 수를 반환 합니다. 그런 다음 첫 번째 단계의 출력은 중복 항목을 삭제 하 여 장치당 평균을 계산 하는 데 사용할 수 있습니다.

자세한 내용은 [개수 (고유 시간)](/stream-analytics-query/count-azure-stream-analytics)를 참조 하세요.

## <a name="session-windows"></a>세션 창

세션 창은 이벤트 발생 시 확장을 유지 하 고 특정 시간 이후 이벤트가 수신 되지 않거나 창이 최대 지속 시간에 도달한 경우 계산을 위해 닫히는 창입니다.
이 창은 사용자 상호 작용 데이터를 계산할 때 특히 유용 합니다. 사용자가 시스템과의 상호 작용을 시작 하면 창이 시작 되 고 더 이상 이벤트가 관찰 되지 않는 경우 (즉, 사용자의 상호 작용을 중지 한 경우) 창이 닫힙니다.
예를 들어 사용자가 클릭 수가 로깅되는 웹 페이지와 상호 작용 하는 경우 세션 창을 사용 하 여 사용자가 사이트와 상호 작용 하는 시간을 확인할 수 있습니다.

**입력**:

| User_id | 시간 | URL |
| --- | --- | --- |
| 0 | 2017-01-26T00:00:00.0000000 Z | "www.example.com/a.html" |
| 0 | 2017-01-26T00:00:20.0000000 Z | "www.example.com/b.html" |
| 1 | 2017-01-26T00:00:55.0000000 Z | "www.example.com/c.html" |
| 0 | 2017-01-26T00:01:10.0000000 Z | "www.example.com/d.html" |
| 1 | 2017-01-26T00:01:15.0000000 Z | "www.example.com/e.html" |

**출력**:

| User_id | StartTime | EndTime | Duration_in_seconds |
| --- | --- | --- | --- |
| 0 | 2017-01-26T00:00:00.0000000 Z | 2017-01-26T00:01:10.0000000 Z | 70 |
| 1 | 2017-01-26T00:00:55.0000000 Z | 2017-01-26T00:01:15.0000000 Z | 20 |

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

**SELECT** 는 상호 작용 기간과 함께 사용자 상호 작용과 관련 된 데이터를 프로젝션 합니다. 사용자를 기준으로 데이터를 그룹화 하 고 1 분 내에 상호 작용이 발생 하지 않을 경우에는 발생 하는 **Sessionwindow** 를 최대 창 크기로 60 분으로 그룹화 합니다.

**Sessionwindow**에 대 한 자세한 내용은 [세션 창](/stream-analytics-query/session-window-azure-stream-analytics) 을 참조 하세요.

## <a name="language-extensibility-with-user-defined-function-in-javascript-and-c"></a>JavaScript 및에서 사용자 정의 함수가 포함 된 언어 확장성C#

JavaScript 또는 C# 언어로 작성 된 사용자 지정 함수로 Azure Stream Analytics 쿼리 언어를 확장할 수 있습니다. UDF (사용자 정의 함수)는 **SQL** 언어를 사용 하 여 쉽게 표현할 수 없는 사용자 지정/복잡 한 계산입니다. 이러한 Udf를 한 번 정의 하 여 쿼리 내에서 여러 번 사용할 수 있습니다. 예를 들어 UDF를 사용 하 여 16 진수 *nvarchar (max)* 값을 *bigint* 값으로 변환할 수 있습니다.

**입력**:

| Device_id | HexValue |
| --- | --- |
| 1 | B4 |
| 2 | 11B |
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

사용자 정의 함수는 사용 되는 모든 이벤트에 대해 HexValue의 *bigint* 값을 계산 합니다.

자세한 내용은 [JavaScript](/stream-analytics/stream-analytics-javascript-user-defined-functions) 및 [C#](/stream-analytics/stream-analytics-edge-csharp-udf)을 참조 하십시오.

## <a name="advanced-pattern-matching-with-match_recognize"></a>MATCH_RECOGNIZE와 고급 패턴 일치

**MATCH_RECOGNIZE** 는 이벤트 시퀀스를 잘 정의 된 정규식 패턴과 일치 시키는 데 사용할 수 있는 고급 패턴 일치 메커니즘입니다.
예를 들어 atm은 오류에 대해 실시간으로 모니터링 되 고, 두 개의 연속 된 경고 메시지가 있는 경우 ATM 작업 중에 관리자에 게 알림이 필요 합니다.

**입력**:

| ATM_id | Operation_id | Return_Code | 시간 |
| --- | --- | --- | --- |
| 1 | "Pin 입력" | "Success" | 2017-01-26T00:10:00.0000000 Z |
| 2 | "통화 슬롯 열기" | "Success" | 2017-01-26T00:10:07.0000000 Z |
| 2 | "통화 슬롯 결산" | "Success" | 2017-01-26T00:10:11.0000000 Z |
| 1 | "회수 수량 입력" | "Success" | 2017-01-26T00:10:08.0000000 Z |
| 1 | "통화 슬롯 열기" | 내용의 | 2017-01-26T00:10:14.0000000 Z |
| 1 | "Bank 잔액 인쇄" | 내용의 | 2017-01-26T00:10:19.0000000 Z |

**출력**:

| ATM_id | First_Warning_Operation_id | Warning_Time |
| --- | --- | --- |
| 1 | "통화 슬롯 열기" | 2017-01-26T00:10:14.0000000 Z |

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

이 쿼리는 두 개 이상의 연속 된 실패 이벤트를 일치 시키고 조건이 충족 되 면 경보를 생성 합니다.
**패턴** 은 일치 하는 데 사용할 정규식을 정의 합니다 .이 경우에는 성공한 작업 수에 관계 없이 두 번 이상 연속 해 서 실패 합니다.
성공 및 실패는 Return_Code 값을 사용 하 여 정의 되며, 조건이 충족 되 면 **측정값이** *ATM_id*, 첫 번째 경고 작업 및 첫 번째 경고 시간으로 프로젝션 됩니다.

자세한 내용은 [MATCH_RECOGNIZE](/stream-analytics-query/match-recognize-stream-analytics)를 참조 하세요.

## <a name="geofencing-and-geospatial-queries"></a>지 오 펜싱 및 지리 공간적 쿼리
Azure Stream Analytics는 지역 관리, 공동 공유, 연결 된 자동차 및 자산 추적과 같은 시나리오를 구현 하는 데 사용할 수 있는 기본 제공 지리 공간적 함수를 제공 합니다.
지리 공간적 데이터는 이벤트 스트림 또는 참조 데이터의 일부로 GeoJSON 또는 WKT 형식으로 수집 수 있습니다.
예를 들어 여권 인쇄를 위해 제조 컴퓨터에 전문화 된 회사는 정부 및 consulates에 컴퓨터를 임대 합니다. 이러한 컴퓨터의 위치는 여권의 위조에 대 한 잘못 된 배치 및 사용 가능성을 방지 하기 위해 과도 하 게 제어 됩니다. 각 컴퓨터는 GPS 추적기에 맞게 조정 되 고, 해당 정보는 Azure Stream Analytics 작업에 다시 릴레이 됩니다.
제조업체는 이러한 컴퓨터의 위치를 추적 하 고, 그 중 하나가 권한이 부여 된 영역을 벗어나면 경고를 표시 하 여 원격으로 사용 하지 않도록 설정 하 고, 경고를 받고, 장비를 검색할 수 있습니다.

**입력**:

| Equipment_id | Equipment_current_location | 시간 |
| --- | --- | --- |
| 1 | "POINT (-122.13288797982818 47.64082002051315)" | 2017-01-26T00:10:00.0000000 Z |
| 1 | "POINT (-122.13307252987875 47.64081350934929)" | 2017-01-26T00:11:00.0000000 Z |
| 1 | "POINT (-122.13308862313283 47.6406508603241)" | 2017-01-26T00:12:00.0000000 Z |
| 1 | "POINT (-122.13341048821462 47.64043760861279)" | 2017-01-26T00:13:00.0000000 Z |

**참조 데이터 입력**:

| Equipment_id | Equipment_lease_location |
| --- | --- |
| 1 | "POLYGON ((-122.13326028450979 47.6409833866794,-122.13261655434621 47.6409833866794,-122.13261655434621 47.64061471602751,-122.13326028450979 47.64061471602751,-122.13326028450979 47.6409833866794))" |

**출력**:

| Equipment_id | Equipment_alert_location | 시간 |
| --- | --- | --- |
| 1 | "POINT (-122.13341048821462 47.64043760861279)" | 2017-01-26T00:13:00.0000000 Z |

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

이 쿼리를 사용 하면 제조업체에서 컴퓨터 위치를 자동으로 모니터링 하 여 컴퓨터에서 허용 되는 지 오를 떠날 때 경고를 받을 수 있습니다. 사용자는 기본 제공 지리 공간적 함수를 사용 하 여 타사 라이브러리 없이 쿼리 내에서 GPS 데이터를 사용할 수 있습니다.

자세한 내용은 [Azure Stream Analytics 문서 지 오 펜싱 및 지리 공간적 집계 시나리오](geospatial-scenarios.md) 를 참조 하세요.

## <a name="get-help"></a>도움 받기

추가 지원이 필요한 경우 [Azure Stream Analytics 포럼](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)을 참조하세요.

## <a name="next-steps"></a>다음 단계
* [Azure Stream Analytics 소개](stream-analytics-introduction.md)
* [Azure Stream Analytics 사용 시작](stream-analytics-real-time-fraud-detection.md)
* [Azure  Stream Analytics 작업 규모 지정](stream-analytics-scale-jobs.md)
* [Azure  Stream Analytics 쿼리 언어 참조](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Stream Analytics 관리 REST API 참조](https://msdn.microsoft.com/library/azure/dn835031.aspx)
