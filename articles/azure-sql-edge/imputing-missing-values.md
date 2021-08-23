---
title: 시간 간격을 채우고 누락된 값 귀속 - Azure SQL Edge
description: Azure SQL Edge에서 시간 간격을 채우고 누락된 값을 귀속하는 방법 알아보기
keywords: SQL Edge, 시계열
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: f5ae44637d17a8de145e8bcd42bd076423b0a3a0
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122536092"
---
# <a name="filling-time-gaps-and-imputing-missing-values"></a>시간 간격을 채우고 누락된 값 귀속

시계열 데이터를 처리할 때 시계열 데이터에 누락된 특성 값이 있을 수 있습니다. 또한 데이터의 특성이나 데이터 수집에서 중단이 발생하여 데이터 세트에 시간 *간격* 이 있을 수 있습니다.

예를 들어 스마트 디바이스에 대한 에너지 사용 통계를 수집하는 경우 디바이스가 작동하지 않을 때마다 사용 통계에 간격이 나타납니다. 마찬가지로, 컴퓨터 원격 분석 데이터 수집 시나리오에서 여러 센서가 데이터를 다른 빈도로 내보내도록 구성된 경우 센서에 누락된 값이 생성될 수 있습니다. 예를 들어 전압과 압력이 각각 100Hz와 10Hz 빈도로 구성된 두 개의 센서가 있는 경우 전압 센서는 1/100초 마다 데이터를 내보내고 압력 센서는 1/10초 간격으로 데이터를 내보냅니다.

다음 표는 1초 간격으로 수집된 컴퓨터 원격 분석 데이터 세트를 나타냅니다.

```txt
timestamp               VoltageReading  PressureReading
----------------------- --------------- ----------------
2020-09-07 06:14:41.000 164.990400      97.223600
2020-09-07 06:14:42.000 162.241300      93.992800
2020-09-07 06:14:43.000 163.271200      NULL
2020-09-07 06:14:44.000 161.368100      93.403700
2020-09-07 06:14:45.000 NULL            NULL
2020-09-07 06:14:46.000 NULL            98.364800
2020-09-07 06:14:49.000 NULL            94.098300
2020-09-07 06:14:51.000 157.695700      103.359100
2020-09-07 06:14:52.000 157.019200      NULL
2020-09-07 06:14:54.000 NULL            95.352000
2020-09-07 06:14:56.000 159.183500      100.748200
```

앞의 데이터 세트에는 두 개의 중요한 특징이 있습니다.

- 데이터 세트에는 몇 개의 타임스탬프 `2020-09-07 06:14:47.000`, `2020-09-07 06:14:48.000`, `2020-09-07 06:14:50.000`, `2020-09-07 06:14:53.000`, `2020-09-07 06:14:55.000`에 대한 데이터 요소가 없습니다. 이러한 타임스탬프는 데이터 세트의 *간격* 입니다.
- 전압과 압력 값에 `null`로 나타나는 누락된 값이 있습니다.

## <a name="gap-filling"></a>간격 채우기

간격 채우기란 시계열 데이터의 분석을 쉽게 수행할 수 있도록 연속적으로 정렬된 타임 스탬프 집합을 만드는 데 도움이 되는 기술입니다. Azure SQL Edge에서 시계열 데이터 세트의 간격을 채우는 가장 쉬운 방법은 원하는 시간 배포를 사용하여 임시 테이블을 정의한 다음 데이터 세트 테이블에서 `LEFT OUTER JOIN` 또는 `RIGHT OUTER JOIN` 작업을 수행하는 것입니다.

위에 표시된 `MachineTelemetry` 데이터를 예로 들면 다음 쿼리를 사용하여 분석을 위해 연속적으로 정렬된 타임 스탬프 집합을 생성할 수 있습니다.

> [!NOTE]
> 아래의 쿼리를 사용하면 특성에 대한 타임스탬프 값과 `null` 값으로 누락된 행을 생성합니다.

```sql
Create Table #SeriesGenerate(dt datetime Primary key Clustered)
GO

Declare @startdate datetime = '2020-09-07 06:14:41.000', @endtime datetime = '2020-09-07 06:14:56.000'
While (@startdate <= @endtime)
BEGIN
Insert into #SeriesGenerate values (@startdate)
set @startdate = DATEADD(SECOND, 1, @startdate)
END

Select a.dt as timestamp, b.VoltageReading, b.PressureReading
From
#SeriesGenerate a LEFT OUTER JOIN MachineTelemetry b
    on a.dt = b.[timestamp]
```
위의 쿼리를 사용하면 지정된 범위의 모든 *1초* 타임스탬프를 포함하는 다음과 같은 출력을 생성합니다.

결과 집합은 다음과 같습니다.

```txt
timestamp               VoltageReading    PressureReading
----------------------- ----------------- ----------------
2020-09-07 06:14:41.000 164.990400        97.223600
2020-09-07 06:14:42.000 162.241300        93.992800
2020-09-07 06:14:43.000 163.271200        NULL
2020-09-07 06:14:44.000 161.368100        93.403700
2020-09-07 06:14:45.000 NULL              NULL
2020-09-07 06:14:46.000 NULL              98.364800
2020-09-07 06:14:47.000 NULL              NULL
2020-09-07 06:14:48.000 NULL              NULL
2020-09-07 06:14:49.000 NULL              94.098300
2020-09-07 06:14:50.000 NULL              NULL
2020-09-07 06:14:51.000 157.695700        103.359100
2020-09-07 06:14:52.000 157.019200        NULL
2020-09-07 06:14:53.000 NULL              NULL
2020-09-07 06:14:54.000 NULL              95.352000
2020-09-07 06:14:55.000 NULL              NULL
2020-09-07 06:14:56.000 159.183500        100.748200
```

## <a name="imputing-missing-values"></a>누락 값 입력

앞의 쿼리를 사용하면 데이터 분석을 위한 누락된 타임스탬프가 생성되지만 `voltage` 및 `pressure` 값의 누락된 값(null로 표시됨)을 대체하지는 못합니다. Azure SQL Edge에서 T-SQL `LAST_VALUE()` 및 `FIRST_VALUE()` 기능에 대해 새로운 구문이 추가되었습니다. 이 구문은 데이터 세트 앞의 값이나 다음 값을 기준으로 누락된 값을 대체하는 메커니즘을 제공합니다.

새로운 구문은 `LAST_VALUE()` 및 `FIRST_VALUE()` 함수에 `IGNORE NULLS` 및 `RESPECT NULLS` 절을 추가합니다. `MachineTelemetry` 데이터 세트의 다음 쿼리는 last_value 기능을 사용하여 누락된 값을 계산합니다. 이 기능을 사용하면 데이터 세트에서 마지막으로 관찰된 값을 바꿉니다.

```sql
Select
    timestamp,
    VoltageReading As OriginalVoltageValues,
    LAST_VALUE(VoltageReading) IGNORE NULLS OVER (ORDER BY timestamp) As ImputedUsingLastValue,
    PressureReading As OriginalPressureValues,
    LAST_VALUE(PressureReading) IGNORE NULLS OVER (ORDER BY timestamp) As ImputedUsingLastValue
From
MachineTelemetry
order by timestamp
```
결과 집합은 다음과 같습니다.

```txt
timestamp               OrigVoltageVals  ImputedVoltage OrigPressureVals  ImputedPressure
----------------------- ---------------- -------------- ----------------- ----------------
2020-09-07 06:14:41.000 164.990400       164.990400     97.223600         97.223600
2020-09-07 06:14:42.000 162.241300       162.241300     93.992800         93.992800
2020-09-07 06:14:43.000 163.271200       163.271200     NULL              93.992800
2020-09-07 06:14:44.000 161.368100       161.368100     93.403700         93.403700
2020-09-07 06:14:45.000 NULL             161.368100     NULL              93.403700
2020-09-07 06:14:46.000 NULL             161.368100     98.364800         98.364800
2020-09-07 06:14:49.000 NULL             161.368100     94.098300         94.098300
2020-09-07 06:14:51.000 157.695700       157.695700     103.359100        103.359100
2020-09-07 06:14:52.000 157.019200       157.019200     NULL              103.359100
2020-09-07 06:14:54.000 NULL             157.019200     95.352000         95.352000
2020-09-07 06:14:56.000 159.183500       159.183500     100.748200        100.748200
```

다음 쿼리를 사용하면 `LAST_VALUE()` 및 `FIRST_VALUE` 함수를 모두 사용하여 누락된 값을 대체합니다. 의 경우 출력 열 `ImputedVoltage`에서 누락된 값은 마지막으로 관찰된 값으로 바뀌고 출력 열 `ImputedPressure`의 경우 누락된 값은 데이터 세트에서 관찰된 다음 값으로 대체됩니다.

```sql
Select
    dt as timestamp,
    VoltageReading As OrigVoltageVals,
    LAST_VALUE(VoltageReading) IGNORE NULLS OVER (ORDER BY dt) As ImputedVoltage,
    PressureReading As OrigPressureVals,
    First_VALUE(PressureReading) IGNORE NULLS OVER (ORDER BY dt ROWS
                    BETWEEN CURRENT ROW AND UNBOUNDED FOLLOWING) As ImputedPressure
From
(Select a.dt, b.VoltageReading,b.PressureReading  from
    #SeriesGenerate a
        LEFT OUTER JOIN
    MachineTelemetry b
        on a.dt = b.[timestamp]) A
order by timestamp
```
결과 집합은 다음과 같습니다.

```txt
timestamp               OrigVoltageVals  ImputedVoltage  OrigPressureVals  ImputedPressure
----------------------- ---------------- --------------- ----------------- ---------------
2020-09-07 06:14:41.000 164.990400       164.990400      97.223600         97.223600
2020-09-07 06:14:42.000 162.241300       162.241300      93.992800         93.992800
2020-09-07 06:14:43.000 163.271200       163.271200      NULL              93.403700
2020-09-07 06:14:44.000 161.368100       161.368100      93.403700         93.403700
2020-09-07 06:14:45.000 NULL             161.368100      NULL              98.364800
2020-09-07 06:14:46.000 NULL             161.368100      98.364800         98.364800
2020-09-07 06:14:47.000 NULL             161.368100      NULL              94.098300
2020-09-07 06:14:48.000 NULL             161.368100      NULL              94.098300
2020-09-07 06:14:49.000 NULL             161.368100      94.098300         94.098300
2020-09-07 06:14:50.000 NULL             161.368100      NULL              103.359100
2020-09-07 06:14:51.000 157.695700       157.695700      103.359100        103.359100
2020-09-07 06:14:52.000 157.019200       157.019200      NULL              95.352000
2020-09-07 06:14:53.000 NULL             157.019200      NULL              95.352000
2020-09-07 06:14:54.000 NULL             157.019200      95.352000         95.352000
2020-09-07 06:14:55.000 NULL             157.019200      NULL              100.748200
2020-09-07 06:14:56.000 159.183500       159.183500      100.748200        100.748200
```

> [!NOTE]
> 위의 쿼리는 `FIRST_VALUE()` 함수를 사용하여 누락된 값을 다음 관찰된 값으로 바꿉니다. `ORDER BY <ordering_column> DESC` 절을 사용하는 `LAST_VALUE()` 기능을 사용해도 동일한 결과를 얻을 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [FIRST_VALUE(Transact-SQL)](/sql/t-sql/functions/first-value-transact-sql?toc=%2fazure%2fazure-sql-edge%2ftoc.json)
- [LAST_VALUE(Transact-SQL)](/sql/t-sql/functions/last-value-transact-sql?toc=%2fazure%2fazure-sql-edge%2ftoc.json)
- [DATE_BUCKET(Transact-SQL)](date-bucket-tsql.md)
- [집계 함수(Transact-SQL)](/sql/t-sql/functions/aggregate-functions-transact-sql)