---
title: 시간 간격과 채우기 누락 값 채우기-Azure SQL Edge
description: Azure SQL Edge에서 시간 간격을 채우고 누락 된 값을 채우기 하는 방법을 알아봅니다.
keywords: SQL Edge, 시계열
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: eb1b3534481c55723bd5d13760259b18e0b5cce2
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93392047"
---
# <a name="filling-time-gaps-and-imputing-missing-values"></a>시간 간격과 채우기 누락 값 채우기 

시계열 데이터를 처리할 때 시계열 데이터에 특성에 대 한 누락 값이 있는 경우가 종종 있습니다. 데이터의 특성으로 인해 또는 데이터 수집 중단으로 인해 데이터 집합에 시간 *간격이* 있기 때문일 수도 있습니다.

예를 들어 스마트 장치에 대 한 에너지 사용 통계를 수집 하는 경우 장치가 작동 하지 않을 때마다 사용 통계에 간격이 나타납니다. 마찬가지로, 컴퓨터 원격 분석 데이터 수집 시나리오에서는 서로 다른 빈도로 데이터를 내보내도록 여러 센서가 구성 되어 센서에 대해 누락 된 값이 생성 될 수 있습니다. 예를 들어 각각 100 Hz와 10-Hz 빈도로 구성 된 두 개의 센서, 전압 및 압력이 있는 경우 전압 센서는 1-1/100 초 마다 데이터를 내보내고 압력 센서는 1 초 간격으로 데이터를 내보냅니다.

다음 표에서는 1 초 간격으로 수집 된 컴퓨터 원격 분석 데이터 집합을 설명 합니다. 

```
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

이전 데이터 집합의 두 가지 중요 한 특성이 있습니다. 

- 데이터 집합에는,,, 등의 몇 가지 타임 스탬프와 관련 된 데이터 요소가 없습니다 `2020-09-07 06:14:47.000` `2020-09-07 06:14:48.000` `2020-09-07 06:14:50.000` `2020-09-07 06:14:53.000` `2020-09-07 06:14:55.000` . 이러한 타임 스탬프는 데이터 집합의 *간격* 입니다.  
- `null`전압 및 압력 판독값에 대해로 표시 되는 누락 값이 있습니다. 

## <a name="gap-filling"></a>간격 채우기 

격차 채우기는 시계열 데이터의 분석을 쉽게 수행할 수 있도록 연속적인 순서로 타임 스탬프 집합을 만드는 데 도움이 되는 기술입니다. Azure SQL Edge에서 시계열 데이터 집합의 간격을 채우는 가장 쉬운 방법은 원하는 시간 배포를 사용 하 여 임시 테이블을 정의한 다음 `LEFT OUTER JOIN` `RIGHT OUTER JOIN` 데이터 집합 테이블에서 또는 작업을 수행 하는 것입니다. 

`MachineTelemetry`위에 표시 된 데이터를 예로 들면 다음 쿼리를 사용 하 여 분석을 위해 연속적인 정렬 된 타임 스탬프 집합을 생성할 수 있습니다. 

> [!NOTE]
> 다음 쿼리는 `null` 특성에 대 한 타임 스탬프 값과 값을 사용 하 여 누락 된 행을 생성 합니다. 

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
위의 쿼리는 지정 된 범위의 모든 *1 초* 타임 스탬프를 포함 하는 다음과 같은 출력을 생성 합니다.

결과 집합은 다음과 같습니다.

```

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

이전 쿼리에서는 데이터 분석을 위해 누락 된 타임 스탬프를 생성 했지만 및 판독값에 대해 누락 된 값 (null로 표시 됨)을 바꾸지 않았습니다 `voltage` `pressure` . Azure SQL Edge에서 T-sql 및 함수에 새 구문이 추가 되었습니다 .이는 `LAST_VALUE()` `FIRST_VALUE()` 데이터 집합의 앞의 값 또는 다음 값을 기준으로 누락 된 값을 돌립니다 하는 메커니즘을 제공 합니다. 

새 구문은 및 `IGNORE NULLS` `RESPECT NULLS` 함수에 및 절을 추가 합니다 `LAST_VALUE()` `FIRST_VALUE()` . 데이터 집합에 대 한 다음 쿼리는 `MachineTelemetry` last_value 함수를 사용 하 여 누락 값을 계산 합니다. 여기서 누락 값은 데이터 집합에서 마지막으로 관찰 된 값으로 대체 됩니다.

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

```

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

다음 쿼리는 및 함수를 모두 사용 하 여 누락 된 값을 imputes 합니다 `LAST_VALUE()` `FIRST_VALUE` . 의 경우 출력 열에는 누락 된 값 `ImputedVoltage` 이 마지막으로 관찰 된 값으로 바뀌고 출력 열의 경우 `ImputedPressure` 누락 값이 데이터 집합에서 관찰 된 다음 값으로 대체 됩니다. 

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

```

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
> 위의 쿼리는 함수를 사용 하 여 `FIRST_VALUE()` 누락 된 값을 관찰 된 다음 값으로 바꿉니다. 절에 함수를 사용 하 여 동일한 결과를 얻을 수 있습니다 `LAST_VALUE()` `ORDER BY <ordering_column> DESC` .

## <a name="next-steps"></a>다음 단계 

- [FIRST_VALUE(Transact-SQL)](/sql/t-sql/functions/first-value-transact-sql?toc=%252fazure%252fazure-sql-edge%252ftoc.json)
- [LAST_VALUE(Transact-SQL)](/sql/t-sql/functions/last-value-transact-sql?toc=%252fazure%252fazure-sql-edge%252ftoc.json)
- [DATE_BUCKET(Transact-SQL)](date-bucket-tsql.md)
- [집계 함수(Transact-SQL)](/sql/t-sql/functions/aggregate-functions-transact-sql)