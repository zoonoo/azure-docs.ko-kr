---
title: Azure Stream Analytics의 일반적인 쿼리 패턴
description: 이 아티클에서는 Azure Stream Analytics 작업에 유용한 다양한 일반 쿼리 패턴 및 디자인을 설명합니다.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/16/2019
ms.openlocfilehash: 61f9e128fa9299a743012e18882fe32591fdd3f0
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75369952"
---
# <a name="query-examples-for-common-stream-analytics-usage-patterns"></a>일반적인 Stream Analytics 사용 패턴에 대한 쿼리 예제

Azure Stream Analytics에서 쿼리는 SQL 방식 쿼리 언어로 표현됩니다. 언어 구문은 [Stream Analytics 쿼리 언어 참조](/stream-analytics-query/stream-analytics-query-language-reference) 가이드에 설명되어 있습니다. 

쿼리 디자인은 단일 입력 스트림에서 이벤트 데이터를 출력 데이터 저장소로 이동 하는 간단한 통과 논리를 표현할 수 있으며, 다양 한 패턴 일치 및 임시 분석을 수행 하 여 Stream Analytics 가이드를 [사용 하 여 IoT 솔루션 빌드](stream-analytics-build-an-iot-solution-using-stream-analytics.md) 에서와 같이 다양 한 기간에 대 한 집계를 계산할 수 있습니다. 여러 입력의 데이터를 조인 하 여 스트리밍 이벤트를 결합할 수 있으며, 정적 참조 데이터에 대해 조회를 수행 하 여 이벤트 값을 보강할 수 있습니다. 여러 출력에 데이터를 쓸 수도 있습니다.

이 문서에서는 실제 시나리오에 따른 몇 가지 일반적인 쿼리 패턴에 대 한 솔루션을 간략하게 설명 합니다.

## <a name="work-with-complex-data-types-in-json-and-avro"></a>JSON 및 AVRO에서 복잡한 데이터 형식 작업

Azure Stream Analytics는 CSV, JSON 및 Avro 데이터 형식의 이벤트 처리를 지원합니다.

JSON 및 Avro는 둘 다 중첩된 개체(레코드) 또는 배열과 같은 복합 형식을 포함할 수 있습니다. 이러한 복합 데이터 형식을 사용 하는 방법에 대 한 자세한 내용은 [JSON 및 AVRO 데이터 구문 분석](stream-analytics-parsing-json.md) 문서를 참조 하세요.

## <a name="query-example-convert-data-types"></a>쿼리 예제: 데이터 형식 변환

**설명**: 입력 스트림의 속성 형식을 정의합니다. 예를 들어 자동차 가중치는 입력 스트림에 문자열로 제공 되며 **합계**를 수행 하기 위해 **INT** 로 변환 해야 합니다.

**입력**:

| 만들기 | 시간 | 무게 |
| --- | --- | --- |
| Honda |2015-01-01T00:00:01.0000000 Z |"1000" |
| Honda |2015-01-01T00:00:02.0000000 Z |"2000" |

**출력**:

| 만들기 | 무게 |
| --- | --- |
| Honda |3000 |

**솔루션**:

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

**설명**: **무게** 필드에서 **CAST** 문을 사용하여 해당 데이터 형식을 지정합니다. [데이터 형식(Azure Stream Analytics)](/stream-analytics-query/data-types-azure-stream-analytics)에서 지원되는 데이터 형식의 목록을 참조하세요.

## <a name="query-example-use-likenot-like-to-do-pattern-matching"></a>쿼리 예제: LIKE/NOT LIKE를 사용 하 여 패턴 일치를 수행 합니다.

**설명**: 이벤트의 필드 값이 특정 패턴과 일치하는지 확인합니다.
예를 들어 결과가 A로 시작하고 9로 끝나는 번호판을 반환하는지 확인합니다.

**입력**:

| 만들기 | LicensePlate | 시간 |
| --- | --- | --- |
| Honda |ABC-123 |2015-01-01T00:00:01.0000000 Z |
| Toyota |AAA-999 |2015-01-01T00:00:02.0000000 Z |
| Nissan |ABC-369 |2015-01-01T00:00:03.0000000 Z |

**출력**:

| 만들기 | LicensePlate | 시간 |
| --- | --- | --- |
| Toyota |AAA-999 |2015-01-01T00:00:02.0000000 Z |
| Nissan |ABC-369 |2015-01-01T00:00:03.0000000 Z |

**솔루션**:

```SQL
    SELECT
        *
    FROM
        Input TIMESTAMP BY Time
    WHERE
        LicensePlate LIKE 'A%9'
```

**설명**: **LIKE** 문을 사용하여 **LicensePlate** 필드 값을 확인합니다. 문자 A로 시작한 다음 0 개 이상의 문자로 이루어진 문자열을 포함 하 고 숫자 9로 끝나야 합니다. 

## <a name="query-example-specify-logic-for-different-casesvalues-case-statements"></a>쿼리 예제: 다른 사례/값(CASE 문)에 대한 논리를 지정합니다.

**설명**: 특정 조건에 따라 필드에 대해 다른 계산을 제공합니다. 예를 들어 1이라는 특수 사례를 가진 동일한 브랜드의 차가 몇 대 통과했는지에 대한 문자열 설명을 제공합니다.

**입력**:

| 만들기 | 시간 |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000 Z |
| Toyota |2015-01-01T00:00:02.0000000 Z |
| Toyota |2015-01-01T00:00:03.0000000 Z |

**출력**:

| CarsPassed | 시간 |
| --- | --- |
| 1 Honda |2015-01-01T00:00:10.0000000 Z |
| 2 Toyotas |2015-01-01T00:00:10.0000000 Z |

**솔루션**:

```SQL
    SELECT
        CASE
            WHEN COUNT(*) = 1 THEN CONCAT('1 ', Make)
            ELSE CONCAT(CAST(COUNT(*) AS NVARCHAR(MAX)), ' ', Make, 's')
        END AS CarsPassed,
        System.TimeStamp() AS AsaTime
    FROM
        Input TIMESTAMP BY Time
    GROUP BY
        Make,
        TumblingWindow(second, 10)
```

**설명**: **CASE** 식은 하나의 식을 일련의 간단한 식과 비교하여 결과를 결정합니다. 이 예제에서는 차량 수가 1인 경우 차량 수가 1이 아닌 경우와 다른 문자열 설명을 반환합니다.

## <a name="query-example-send-data-to-multiple-outputs"></a>쿼리 예제: 데이터를 여러 출력으로 보내기

**설명**: 데이터를 단일 작업에서 여러 출력 대상으로 보냅니다. 예를 들어 임계점 기반 경고에 대한 데이터를 분석하고 Blob Storage에 모든 이벤트를 보관합니다.

**입력**:

| 만들기 | 시간 |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000 Z |
| Honda |2015-01-01T00:00:02.0000000 Z |
| Toyota |2015-01-01T00:00:01.0000000 Z |
| Toyota |2015-01-01T00:00:02.0000000 Z |
| Toyota |2015-01-01T00:00:03.0000000 Z |

**출력1**:

| 만들기 | 시간 |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000 Z |
| Honda |2015-01-01T00:00:02.0000000 Z |
| Toyota |2015-01-01T00:00:01.0000000 Z |
| Toyota |2015-01-01T00:00:02.0000000 Z |
| Toyota |2015-01-01T00:00:03.0000000 Z |

**출력2**:

| 만들기 | 시간 | 카운트 |
| --- | --- | --- |
| Toyota |2015-01-01T00:00:10.0000000 Z |3 |

**솔루션**:

```SQL
    SELECT
        *
    INTO
        ArchiveOutput
    FROM
        Input TIMESTAMP BY Time

    SELECT
        Make,
        System.TimeStamp() AS AsaTime,
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

**설명**: **INTO** 절은 이 문의 데이터를 어떤 출력에 쓸 것인지에 대한 Stream Analytics을 알려줍니다. 첫 번째 쿼리는 **ArchiveOutput**라는 출력으로 수신 되는 데이터의 통과입니다. 두 번째 쿼리는 일부 간단한 집계 및 필터링을 수행 하 고 결과를 결과를 다운스트림 경고 시스템 **Alertoutput**으로 보냅니다.

여러 출력 문에서 공통 테이블 식(CTE)(예: **WITH** 문)의 결과를 다시 사용할 수도 있습니다. 이 옵션에는 입력 원본에 대해 더 적은 독자를 여는 추가 이점이 있습니다.

예: 

```SQL
    WITH AllRedCars AS (
        SELECT
            *
        FROM
            Input TIMESTAMP BY Time
        WHERE
            Color = 'red'
    )
    SELECT * INTO HondaOutput FROM AllRedCars WHERE Make = 'Honda'
    SELECT * INTO ToyotaOutput FROM AllRedCars WHERE Make = 'Toyota'
```

## <a name="query-example-count-unique-values"></a>쿼리 예제: 고유 값 계산

**설명**: 시간 창 안의 스트림에 나타나는 고유 필드 값 개수를 계산합니다. 예를 들어 2초 안에 톨게이트 요금소를 통과한 자동차의 고유한 브랜드는 몇 개입니까?

**입력**:

| 만들기 | 시간 |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000 Z |
| Honda |2015-01-01T00:00:02.0000000 Z |
| Toyota |2015-01-01T00:00:01.0000000 Z |
| Toyota |2015-01-01T00:00:02.0000000 Z |
| Toyota |2015-01-01T00:00:03.0000000 Z |

**출력:**

| CountMake | 시간 |
| --- | --- |
| 2 |2015-01-01T00:00:02.000 Z |
| 1 |2015-01-01T00:00:04.000 Z |

**해결 방법:**

```SQL
SELECT
     COUNT(DISTINCT Make) AS CountMake,
     System.TIMESTAMP() AS AsaTIME
FROM Input TIMESTAMP BY TIME
GROUP BY 
     TumblingWindow(second, 2)
```


**설명:** 
**COUNT(DISTINCT Make)** 는 특정 기간 내에서 **Make** 열의 고유한 값 수를 반환합니다.

## <a name="query-example-determine-if-a-value-has-changed"></a>쿼리 예제: 값이 변경되었는지 여부를 확인합니다.

**설명**: 이전 값을 확인하여 현재 값과 다른지 여부를 확인합니다. 예를 들어 유료 도로의 이전 자동차는 현재 자동차와 동일한 브랜드인가요?

**입력**:

| 만들기 | 시간 |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000 Z |
| Toyota |2015-01-01T00:00:02.0000000 Z |

**출력**:

| 만들기 | 시간 |
| --- | --- |
| Toyota |2015-01-01T00:00:02.0000000 Z |

**솔루션**:

```SQL
    SELECT
        Make,
        Time
    FROM
        Input TIMESTAMP BY Time
    WHERE
        LAG(Make, 1) OVER (LIMIT DURATION(minute, 1)) <> Make
```

**설명**: **LAG**를 사용하여 지난 이벤트의 입력 스트림을 살펴보고, **Make** 값을 얻습니다. 그런 다음 현재 이벤트와 출력 이벤트의 **Make** 값을 비교합니다.

## <a name="query-example-find-the-first-event-in-a-window"></a>쿼리 예제: 창에서 첫 번째 이벤트 찾기

**설명**: 매 10분 간격으로 첫 번째 자동차를 찾습니다.

**입력**:

| LicensePlate | 만들기 | 시간 |
| --- | --- | --- |
| DXE 5291 |Honda |2015-07-27T00:00:05.0000000 Z |
| YZK 5704 |Ford |2015-07-27T00:02:17.0000000 Z |
| RMV 8282 |Honda |2015-07-27T00:05:01.0000000 Z |
| YHN 6970 |Toyota |2015-07-27T00:06:00.0000000 Z |
| VFE 1616 |Toyota |2015-07-27T00:09:31.0000000 Z |
| QYF 9358 |Honda |2015-07-27T00:12:02.0000000 Z |
| MDR 6128 |BMW |2015-07-27T00:13:45.0000000 Z |

**출력**:

| LicensePlate | 만들기 | 시간 |
| --- | --- | --- |
| DXE 5291 |Honda |2015-07-27T00:00:05.0000000 Z |
| QYF 9358 |Honda |2015-07-27T00:12:02.0000000 Z |

**솔루션**:

```SQL
    SELECT 
        LicensePlate,
        Make,
        Time
    FROM 
        Input TIMESTAMP BY Time
    WHERE 
        IsFirst(minute, 10) = 1
```

이제 문제를 변경 하 고 10 분 간격 마다 특정의 첫 번째 자동차를 찾습니다.

| LicensePlate | 만들기 | 시간 |
| --- | --- | --- |
| DXE 5291 |Honda |2015-07-27T00:00:05.0000000 Z |
| YZK 5704 |Ford |2015-07-27T00:02:17.0000000 Z |
| YHN 6970 |Toyota |2015-07-27T00:06:00.0000000 Z |
| QYF 9358 |Honda |2015-07-27T00:12:02.0000000 Z |
| MDR 6128 |BMW |2015-07-27T00:13:45.0000000 Z |

**솔루션**:

```SQL
    SELECT 
        LicensePlate,
        Make,
        Time
    FROM 
        Input TIMESTAMP BY Time
    WHERE 
        IsFirst(minute, 10) OVER (PARTITION BY Make) = 1
```

## <a name="query-example-find-the-last-event-in-a-window"></a>쿼리 예제: 창에서 마지막 이벤트 찾기

**설명**: 매 10분 간격마다 마지막 자동차를 찾습니다.

**입력**:

| LicensePlate | 만들기 | 시간 |
| --- | --- | --- |
| DXE 5291 |Honda |2015-07-27T00:00:05.0000000 Z |
| YZK 5704 |Ford |2015-07-27T00:02:17.0000000 Z |
| RMV 8282 |Honda |2015-07-27T00:05:01.0000000 Z |
| YHN 6970 |Toyota |2015-07-27T00:06:00.0000000 Z |
| VFE 1616 |Toyota |2015-07-27T00:09:31.0000000 Z |
| QYF 9358 |Honda |2015-07-27T00:12:02.0000000 Z |
| MDR 6128 |BMW |2015-07-27T00:13:45.0000000 Z |

**출력**:

| LicensePlate | 만들기 | 시간 |
| --- | --- | --- |
| VFE 1616 |Toyota |2015-07-27T00:09:31.0000000 Z |
| MDR 6128 |BMW |2015-07-27T00:13:45.0000000 Z |

**솔루션**:

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
        Input.LicensePlate,
        Input.Make,
        Input.Time
    FROM
        Input TIMESTAMP BY Time 
        INNER JOIN LastInWindow
        ON DATEDIFF(minute, Input, LastInWindow) BETWEEN 0 AND 10
        AND Input.Time = LastInWindow.LastEventTime
```

**설명**: 쿼리에서 두 단계가 있습니다. 첫 번째 단계는 10분 간격으로 최신 타임스탬프를 찾습니다. 두 번째 단계는 각 창의 최신 타임스탬프와 일치하는 이벤트를 찾기 위해 원본 스트림을 사용한 첫 번째 쿼리의 결과를 조인합니다. 

## <a name="query-example-locate-correlated-events-in-a-stream"></a>쿼리 예제: 스트림에서 상호 관련 된 이벤트 찾기

**설명**: 스트림에서 상호 관련 된 이벤트를 찾습니다. 예를 들어 최근 90초 이내에 동일한 브랜드의 차 2대가 연속해서 유료 도로로 진입했는지 확인합니다.

**입력**:

| 만들기 | LicensePlate | 시간 |
| --- | --- | --- |
| Honda |ABC-123 |2015-01-01T00:00:01.0000000 Z |
| Honda |AAA-999 |2015-01-01T00:00:02.0000000 Z |
| Toyota |DEF-987 |2015-01-01T00:00:03.0000000 Z |
| Honda |GHI-345 |2015-01-01T00:00:04.0000000 Z |

**출력**:

| 만들기 | 시간 | CurrentCarLicensePlate | FirstCarLicensePlate | FirstCarTime |
| --- | --- | --- | --- | --- |
| Honda |2015-01-01T00:00:02.0000000 Z |AAA-999 |ABC-123 |2015-01-01T00:00:01.0000000 Z |

**솔루션**:

```SQL
    SELECT
        Make,
        Time,
        LicensePlate AS CurrentCarLicensePlate,
        LAG(LicensePlate, 1) OVER (LIMIT DURATION(second, 90)) AS FirstCarLicensePlate,
        LAG(Time, 1) OVER (LIMIT DURATION(second, 90)) AS FirstCarTime
    FROM
        Input TIMESTAMP BY Time
    WHERE
        LAG(Make, 1) OVER (LIMIT DURATION(second, 90)) = Make
```

**설명**: **LAG**를 사용하여 지난 이벤트의 입력 스트림을 살펴보고, **Make** 값을 얻습니다. 현재 이벤트와 출력 이벤트의 **MAKE** 값이 동일한지 비교합니다. **LAG**를 사용하여 이전 자동차에 대한 데이터를 가져올 수도 있습니다.

## <a name="query-example-detect-the-duration-between-events"></a>쿼리 예제: 이벤트 간 기간 검색

**설명**: 제공된 이벤트의 기간을 찾습니다. 예를 들어 웹 클릭스트림이 지정된 경우 기능에서 소요된 시간을 확인합니다.

**입력**:  

| 사용자 | 기능 | 이벤트 | 시간 |
| --- | --- | --- | --- |
| user@location.com |RightMenu |시작 |2015-01-01T00:00:01.0000000 Z |
| user@location.com |RightMenu |종료 |2015-01-01T00:00:08.0000000 Z |

**출력**:  

| 사용자 | 기능 | 기간 |
| --- | --- | --- |
| user@location.com |RightMenu |7 |

**솔루션**:

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

**설명**: **LAST** 함수를 사용하여 이벤트 유형이 **Start**였을 때 마지막 **시간** 값을 검색합니다. **LAST** 함수는 **PARTITION BY [사용자]** 를 사용하여 고유한 사용자마다 결과가 계산된다는 것을 나타냅니다. 쿼리에 **Start**와 **Stop** 이벤트 간 시간 차이를 위해 최대 1시간의 임계값이 있지만 필요에 따라 구성 가능합니다 **(LIMIT DURATION(hour, 1)** .

## <a name="query-example-detect-the-duration-of-a-condition"></a>쿼리 예제: 조건의 기간 감지
**설명**: 조건이 발생한 기간을 알아봅니다.
예를 들어 버그로 인해 모든 자동차의 중량이 잘못되었고(20.000파운드 이상) 버그의 지속 시간을 계산해야 한다고 가정합니다.

**입력**:

| 만들기 | 시간 | 무게 |
| --- | --- | --- |
| Honda |2015-01-01T00:00:01.0000000 Z |2000 |
| Toyota |2015-01-01T00:00:02.0000000 Z |25000 |
| Honda |2015-01-01T00:00:03.0000000 Z |26000 |
| Toyota |2015-01-01T00:00:04.0000000 Z |25000 |
| Honda |2015-01-01T00:00:05.0000000 Z |26000 |
| Toyota |2015-01-01T00:00:06.0000000 Z |25000 |
| Honda |2015-01-01T00:00:07.0000000 Z |26000 |
| Toyota |2015-01-01T00:00:08.0000000 Z |2000 |

**출력**:

| StartFault | EndFault |
| --- | --- |
| 2015-01-01T00:00:02.000 Z |2015-01-01T00:00:07.000 Z |

**솔루션**:

```SQL
    WITH SelectPreviousEvent AS
    (
    SELECT
    *,
        LAG([time]) OVER (LIMIT DURATION(hour, 24)) as previousTime,
        LAG([weight]) OVER (LIMIT DURATION(hour, 24)) as previousWeight
    FROM input TIMESTAMP BY [time]
    )

    SELECT 
        LAG(time) OVER (LIMIT DURATION(hour, 24) WHEN previousWeight < 20000 ) [StartFault],
        previousTime [EndFault]
    FROM SelectPreviousEvent
    WHERE
        [weight] < 20000
        AND previousWeight > 20000
```

**설명**: **LAG**를 사용하여 24시간 동안 입력 스트림을 보고 **StartFault**와 **StopFault**가 가중치 < 20000인 인스턴스를 찾습니다.

## <a name="query-example-fill-missing-values"></a>쿼리 예제: 누락된 값 입력

**설명**: 누락된 값이 있는 이벤트 스트림의 경우 정기적으로 이벤트 스트림을 생성합니다. 예를 들어, 가장 최근 표시된 데이터 지점을 보고하도록 5초마다 이벤트를 생성합니다.

**입력**:

| t | 값 |
| --- | --- |
| "2014-01-01T06:01:00" |1 |
| "2014-01-01T06:오전 1:05" |2 |
| "2014-01-01T06:오전 1:10" |3 |
| "2014-01-01T06:오전 1:15" |4 |
| "2014-01-01T06:오전 1:30" |5 |
| "2014-01-01T06:오전 1:35" |6 |

**출력(처음 10개 행)** :

| windowend | lastevent.t | lastevent.value |
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

**솔루션**:

```SQL
    SELECT
        System.Timestamp() AS windowEnd,
        TopOne() OVER (ORDER BY t DESC) AS lastEvent
    FROM
        input TIMESTAMP BY t
    GROUP BY HOPPINGWINDOW(second, 300, 5)
```

**설명**: 이 쿼리는 5초마다 이벤트를 생성하고 이전에 수신했던 마지막 이벤트를 출력합니다. [도약 창](/stream-analytics-query/hopping-window-azure-stream-analytics) 지속 시간은 쿼리를 통해 최신 이벤트 (이 예제에서는 300 초)를 찾는 정도를 결정 합니다.


## <a name="query-example-correlate-two-event-types-within-the-same-stream"></a>쿼리 예: 동일한 스트림 내에서 두 가지 이벤트 유형에 상관 관계 지정

**설명**: 특정 시간대에 발생한 여러 이벤트 유형을 기반으로 알림을 생성해야 하는 경우가 있습니다. 예를 들어 가정용 오븐의 IoT 시나리오에서 팬 온도가 40도 미만이고 지난 3분 동안 최대 전력이 10 미만이면 경고가 생성되어야 합니다.

**입력**:

| time | deviceId | sensorName | 값 |
| --- | --- | --- | --- |
| "2018-01-01T16:01:00" | "Oven1" | "temp" |120 |
| "2018-01-01T16:01:00" | "Oven1" | "power" |15 |
| "2018-01-01T16:02:00" | "Oven1" | "temp" |100 |
| "2018-01-01T16:02:00" | "Oven1" | "power" |15 |
| "2018-01-01T16:03:00" | "Oven1" | "temp" |70 |
| "2018-01-01T16:03:00" | "Oven1" | "power" |15 |
| "2018-01-01T16:04:00" | "Oven1" | "temp" |50 |
| "2018-01-01T16:04:00" | "Oven1" | "power" |15 |
| "2018-01-01T16:05:00" | "Oven1" | "temp" |30 |
| "2018-01-01T16:05:00" | "Oven1" | "power" |8 |
| "2018-01-01T16:06:00" | "Oven1" | "temp" |20 |
| "2018-01-01T16:06:00" | "Oven1" | "power" |8 |
| "2018-01-01T16:07:00" | "Oven1" | "temp" |20 |
| "2018-01-01T16:07:00" | "Oven1" | "power" |8 |
| "2018-01-01T16:08:00" | "Oven1" | "temp" |20 |
| "2018-01-01T16:08:00" | "Oven1" | "power" |8 |

**출력**:

| eventTime | deviceId | temp | alertMessage | maxPowerDuringLast3mins |
| --- | --- | --- | --- | --- | 
| "2018-01-01T16:05:00" | "Oven1" |30 | "Short circuit heating elements" |15 |
| "2018-01-01T16:06:00" | "Oven1" |20 | "Short circuit heating elements" |15 |
| "2018-01-01T16:07:00" | "Oven1" |20 | "Short circuit heating elements" |15 |

**솔루션**:

```SQL
WITH max_power_during_last_3_mins AS (
    SELECT 
        System.TimeStamp() AS windowTime,
        deviceId,
        max(value) as maxPower
    FROM
        input TIMESTAMP BY t
    WHERE 
        sensorName = 'power' 
    GROUP BY 
        deviceId, 
        SlidingWindow(minute, 3) 
)

SELECT 
    t1.t AS eventTime,
    t1.deviceId, 
    t1.value AS temp,
    'Short circuit heating elements' as alertMessage,
    t2.maxPower AS maxPowerDuringLast3mins
    
INTO resultsr

FROM input t1 TIMESTAMP BY t
JOIN max_power_during_last_3_mins t2
    ON t1.deviceId = t2.deviceId 
    AND t1.t = t2.windowTime
    AND DATEDIFF(minute,t1,t2) between 0 and 3
    
WHERE
    t1.sensorName = 'temp'
    AND t1.value <= 40
    AND t2.maxPower > 10
```

**설명**: 첫 번째 쿼리 `max_power_during_last_3_mins`는 [슬라이딩 윈도우](/stream-analytics-query/sliding-window-azure-stream-analytics)를 사용하여 지난 3분 동안 모든 디바이스에 대한 전력 센서의 최댓값을 찾습니다. 두 번째 쿼리는 첫 번째 쿼리에 조인되어 현재 이벤트와 관련된 가장 최근 윈도우의 파워 값을 찾습니다. 그런 다음, 조건이 충족되면 디바이스에 대한 경고가 생성됩니다.

## <a name="query-example-process-events-independent-of-device-clock-skew-substreams"></a>쿼리 예제: 디바이스 클록 스큐(하위 스트림)와 무관한 이벤트 처리

**설명**: 이벤트 생성자 간의 클록 스큐, 파티션 간 클럭 스큐 또는 네트워크 대기 시간으로 인해 이벤트가 늦게 도착하거나 순서대로 도착하지 못할 수 있습니다. 다음 예제에서 TollID 2에 대 한 장치 클록은 TollID 1에서 5 초 뒤 이며 TollID 3의 장치 클록은 TollID 1 보다 10 초 뒤입니다. 

**입력**:

| LicensePlate | 만들기 | 시간 | TollId |
| --- | --- | --- | --- |
| DXE 5291 |Honda |2015-07-27T00:00:01.0000000 Z | 1 |
| YHN 6970 |Toyota |2015-07-27T00:00:05.0000000 Z | 1 |
| QYF 9358 |Honda |2015-07-27T00:00:01.0000000 Z | 2 |
| GXF 9462 |BMW |2015-07-27T00:00:04.0000000 Z | 2 |
| VFE 1616 |Toyota |2015-07-27T00:00:10.0000000 Z | 1 |
| RMV 8282 |Honda |2015-07-27T00:00:03.0000000 Z | 3 |
| MDR 6128 |BMW |2015-07-27T00:00:11.0000000 Z | 2 |
| YZK 5704 |Ford |2015-07-27T00:00:07.0000000 Z | 3 |

**출력**:

| TollId | 카운트 |
| --- | --- |
| 1 | 2 |
| 2 | 2 |
| 1 | 1 |
| 3 | 1 |
| 2 | 1 |
| 3 | 1 |

**솔루션**:

```SQL
SELECT
      TollId,
      COUNT(*) AS Count
FROM input
      TIMESTAMP BY Time OVER TollId
GROUP BY TUMBLINGWINDOW(second, 5), TollId
```

**설명**: [TIMESTAMP BY OVER](/stream-analytics-query/timestamp-by-azure-stream-analytics#over-clause-interacts-with-event-ordering) 절은 하위 스트림을 사용하여 각 디바이스 타임라인을 개별적으로 살펴봅니다. 각 TollID에 대한 출력 이벤트는 계산될 때 생성됩니다. 즉 모든 디바이스가 동일한 시계를 사용하는 것처럼 순서가 재배열되는 대신 이벤트가 각 TollID에 관해 순서대로 처리됩니다.

## <a name="query-example-remove-duplicate-events-in-a-window"></a>쿼리 예제: 창에서 중복 이벤트 제거

**설명**: 지정 된 기간에 이벤트에 대 한 평균을 계산 하는 등의 작업을 수행 하는 경우 중복 이벤트를 필터링 해야 합니다. 다음 예제에서 두 번째 이벤트는 첫 번째 이벤트와 중복 됩니다.

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

**솔루션**:

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

**설명**: [COUNT (distinct time)](/stream-analytics-query/count-azure-stream-analytics) 는 시간 열에서 시간 창의 고유 값 수를 반환 합니다. 그러면 중복 항목을 삭제하여 디바이스당 평균을 컴퓨팅하는 데 이 단계의 출력을 사용할 수 있습니다.

## <a name="geofencing-and-geospatial-queries"></a>지 오 펜싱 및 지리 공간적 쿼리
Azure Stream Analytics는 지역 관리, 공동 공유, 연결 된 자동차 및 자산 추적과 같은 시나리오를 구현 하는 데 사용할 수 있는 기본 제공 지리 공간적 함수를 제공 합니다. 지리 공간적 데이터는 이벤트 스트림 또는 참조 데이터의 일부로 GeoJSON 또는 WKT 형식으로 수집 수 있습니다. 자세한 내용은 [Azure Stream Analytics 문서 지 오 펜싱 및 지리 공간적 집계 시나리오](geospatial-scenarios.md) 를 참조 하세요.

## <a name="language-extensibility-through-javascript-and-c"></a>JavaScript를 통한 언어 확장성 및C#
Azure Stream Ananlytics query langugae는 JavaScript 또는 C# 언어로 작성 된 사용자 지정 함수로 확장할 수 있습니다. 자세한 내용은 foolowing 문서를 참조 하세요.
* [JavaScript 사용자 정의 함수 Azure Stream Analytics](stream-analytics-javascript-user-defined-functions.md)
* [JavaScript 사용자 정의 집계 Azure Stream Analytics](stream-analytics-javascript-user-defined-aggregates.md)
* [Edge 작업 Azure Stream Analytics에 대 한 .NET Standard 사용자 정의 함수 개발](stream-analytics-edge-csharp-udf-methods.md)

## <a name="get-help"></a>도움 받기

추가 지원이 필요한 경우 [Azure Stream Analytics 포럼](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)을 참조하세요.

## <a name="next-steps"></a>다음 단계
* [Azure Stream Analytics 소개](stream-analytics-introduction.md)
* [Azure Stream Analytics 사용 시작](stream-analytics-real-time-fraud-detection.md)
* [Azure  Stream Analytics 작업 규모 지정](stream-analytics-scale-jobs.md)
* [Azure  Stream Analytics 쿼리 언어 참조](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Stream Analytics 관리 REST API 참조](https://msdn.microsoft.com/library/azure/dn835031.aspx)

