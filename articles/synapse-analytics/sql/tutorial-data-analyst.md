---
title: 주문형 SQL(미리 보기)을 사용하여 Azure Synapse Studio(미리 보기)에서 Azure Open Datasets 분석
description: 이 자습서에서는 주문형 SQL(미리 보기)을 사용하여 여러 Azure Open Datasets를 결합하여 예비 데이터 분석을 쉽게 수행하고 Azure Synapse Studio에서 결과를 시각화하는 방법을 알아봅니다.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: sql
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 2fc98e927fcf9686f0f39dae600f944b485c5a06
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87089144"
---
# <a name="use-sql-on-demand-to-analyze-azure-open-datasets-and-visualize-the-results-in-azure-synapse-studio"></a>SQL 주문형을 사용하여 Azure Open Datasets를 분석하고 그 결과를 Azure Synapse Studio에서 시각화

이 자습서에서는 주문형 SQL을 사용하여 여러 Azure Open Datasets를 결합하여 예비 데이터 분석을 수행한 다음, 그 결과를 Azure Synapse Studio에서 시각화하는 방법을 알아봅니다.

특히 다음을 포함하는 [뉴욕시(NYC) 택시 데이터 세트](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/)를 분석합니다.

- 승하차 날짜와 시간
- 승하차 위치. 
- 이동 거리
- 항목별 요금
- 요율 유형
- 결제 유형 
- 운전사가 보고한 승객 수

## <a name="automatic-schema-inference"></a>자동 스키마 유추

데이터는 Parquet 파일 형식으로 저장되므로 자동 스키마 유추를 사용할 수 있습니다. 파일에 있는 모든 열의 데이터 형식을 나열할 필요 없이 데이터를 쉽게 쿼리할 수 있습니다. 또한 가상 열 메커니즘과 filepath 함수를 활용하여 파일의 특정 하위 집합을 필터링할 수 있습니다.

먼저 다음 쿼리를 실행하여 NYC 택시 데이터를 살펴보도록 하겠습니다.

```sql
SELECT TOP 100 * FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [nyc]
```

다음은 NYC 택시 데이터의 결과 조각을 보여 줍니다.

![NYC 택시 데이터 결과 조각](./media/tutorial-data-analyst/1.png)

마찬가지로, 다음 쿼리를 사용하여 공휴일 데이터 세트를 쿼리할 수 있습니다.

```sql
SELECT TOP 100 * FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/holidaydatacontainer/Processed/*.parquet',
        FORMAT='PARQUET'
    ) AS [holidays]
```

다음은 공휴일 데이터 세트의 결과 조각을 보여 줍니다.

![공휴일 데이터 세트 결과 조각](./media/tutorial-data-analyst/2.png)

마지막으로, 다음 쿼리를 사용하여 날씨 데이터 세트를 쿼리할 수도 있습니다.

```sql
SELECT
    TOP 100 *
FROM  
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/isdweatherdatacontainer/ISDWeather/year=*/month=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [weather]
```

다음은 날씨 데이터 세트의 결과 조각을 보여 줍니다.

![날씨 데이터 세트 결과 조각](./media/tutorial-data-analyst/3.png)

개별 열의 의미는 [NYC 택시](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/), [공휴일](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/) 및 [날씨 데이터](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/) 데이터 세트에 대한 설명에서 자세히 알아볼 수 있습니다.

## <a name="time-series-seasonality-and-outlier-analysis"></a>시계열, 계절성 및 이상값 분석

다음 쿼리를 사용하여 연간 택시 승차 횟수를 쉽게 요약할 수 있습니다.

```sql
SELECT
    YEAR(tpepPickupDateTime) AS current_year,
    COUNT(*) AS rides_per_year
FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [nyc]
WHERE nyc.filepath(1) >= '2009' AND nyc.filepath(1) <= '2019'
GROUP BY YEAR(tpepPickupDateTime)
ORDER BY 1 ASC
```

다음은 연간 택시 승차 횟수에 대한 결과 조각을 보여 줍니다.

![연간 택시 승차 횟수 결과 조각](./media/tutorial-data-analyst/4.png)

Synapse Studio에서 **테이블** 보기를 **차트** 보기로 전환하면 데이터를 시각화할 수 있습니다. 여러 차트 유형(예: **영역**, **막대형**, **세로 막대형**, **꺾은선형**, **원형** 및 **분산형**) 중에 선택할 수 있습니다. 이 경우 **Category** 열을 **current_year**로 설정하여 **세로 막대형** 차트를 그리겠습니다.

![연간 승차 횟수를 보여 주는 세로 막대형 차트](./media/tutorial-data-analyst/5.png)

이 시각화에서 연간 승차 횟수의 감소 추세를 명확하게 볼 수 있습니다. 아마도 최근 승차 공유 회사의 인기가 상승하면서 승차 인원이 감소했기 때문일 수 있습니다.

> [!NOTE]
> 이 자습서를 작성할 당시에는 2019년도 데이터가 완전하지 않습니다. 따라서 해당 연도의 탑승 횟수는 크게 감소한 것처럼 보입니다.

다음으로, 단일 연도(예: 2016년)에 대한 분석을 중점적으로 살펴보겠습니다. 다음 쿼리는 해당 연도 중 일일 승차 횟수를 반환합니다.

```sql
SELECT
    CAST([tpepPickupDateTime] AS DATE) AS [current_day],
    COUNT(*) as rides_per_day
FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [nyc]
WHERE nyc.filepath(1) = '2016'
GROUP BY CAST([tpepPickupDateTime] AS DATE)
ORDER BY 1 ASC
```

다음은 이 쿼리에 대한 결과 조각을 보여줍니다.

![2016년도 일일 탑승 횟수 결과 조각](./media/tutorial-data-analyst/6.png)

다시, **Category** 열을 **current_day**로 설정하고 **Legend(series)** 열을 **rides_per_day**로 설정하여 **세로 막대형** 차트를 그려서 데이터를 쉽게 시각화할 수 있습니다.

![2016년도 일일 탑승 횟수를 보여 주는 세로 막대형 차트](./media/tutorial-data-analyst/7.png)

이 그림 차트에서 주 단위 패턴을 볼 수 있으며, 토요일이 가장 탑승 횟수가 많은 요일임을 알 수 있습니다. 여름 몇 개월 동안은 휴가로 인해 택시 승차 인원이 줄어듭니다. 택시 승차 횟수가 언제 그리고 왜 발생하는지에 대한 명확한 패턴이 없이 크게 감소한 경우도 있습니다.

다음에는, NYC 택시 승차 횟수 데이터 세트와 공휴일 데이터 세트를 결합하여 이러한 하락이 공휴일과 상관 관계가 있는지 살펴보겠습니다.

```sql
WITH taxi_rides AS
(
    SELECT
        CAST([tpepPickupDateTime] AS DATE) AS [current_day],
        COUNT(*) as rides_per_day
    FROM  
        OPENROWSET(
            BULK 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
            FORMAT='PARQUET'
        ) AS [nyc]
    WHERE nyc.filepath(1) = '2016'
    GROUP BY CAST([tpepPickupDateTime] AS DATE)
),
public_holidays AS
(
    SELECT
        holidayname as holiday,
        date
    FROM
        OPENROWSET(
            BULK 'https://azureopendatastorage.blob.core.windows.net/holidaydatacontainer/Processed/*.parquet',
            FORMAT='PARQUET'
        ) AS [holidays]
    WHERE countryorregion = 'United States' AND YEAR(date) = 2016
)
SELECT
*
FROM taxi_rides t
LEFT OUTER JOIN public_holidays p on t.current_day = p.date
ORDER BY current_day ASC
```

![NYC 택시 탑승 횟수 및 공휴일 데이터 세트 결과 시각화](./media/tutorial-data-analyst/8.png)

이번에는 공휴일 중 택시 승차 횟수를 강조하려고 합니다. 이를 위해 **Category** 열에는 **none**을 선택하고 **Legend(series)** 열에는 **rides_per_day**와 **holiday**를 선택하겠습니다.

![공휴일 택시 탑승 횟수 그림 차트](./media/tutorial-data-analyst/9.png)

그림 차트를 보면 공휴일 중 택시 승차 횟수가 적은 것을 알 수 있습니다. 1월 23일의 큰 하락은 여전히 설명할 수 없습니다. 날씨 데이터 세트를 쿼리하여 해당일의 NYC 날씨를 확인해보겠습니다.

```sql
SELECT
    AVG(windspeed) AS avg_windspeed,
    MIN(windspeed) AS min_windspeed,
    MAX(windspeed) AS max_windspeed,
    AVG(temperature) AS avg_temperature,
    MIN(temperature) AS min_temperature,
    MAX(temperature) AS max_temperature,
    AVG(sealvlpressure) AS avg_sealvlpressure,
    MIN(sealvlpressure) AS min_sealvlpressure,
    MAX(sealvlpressure) AS max_sealvlpressure,
    AVG(precipdepth) AS avg_precipdepth,
    MIN(precipdepth) AS min_precipdepth,
    MAX(precipdepth) AS max_precipdepth,
    AVG(snowdepth) AS avg_snowdepth,
    MIN(snowdepth) AS min_snowdepth,
    MAX(snowdepth) AS max_snowdepth
FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/isdweatherdatacontainer/ISDWeather/year=*/month=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [weather]
WHERE countryorregion = 'US' AND CAST([datetime] AS DATE) = '2016-01-23' AND stationname = 'JOHN F KENNEDY INTERNATIONAL AIRPORT'
```

![날씨 데이터 세트 결과 시각화](./media/tutorial-data-analyst/10.png)

쿼리 결과에 따르면 택시 승차 횟수 하락의 원인은 다음과 같습니다.

- 그날 NYC에 폭설(~30cm)로 인한 눈보라가 있었습니다.
- 추웠습니다(섭씨 영하의 온도).
- 바람이 불었습니다(초속 10m 이하).

이 자습서에서는 데이터 분석가가 예비 데이터 분석을 신속하게 수행하고, 주문형 SQL을 사용하여 여러 데이터 세트를 쉽게 결합하고 Azure Synapse Studio를 사용하여 결과를 시각화하는 방법을 살펴보았습니다.

## <a name="next-steps"></a>다음 단계

주문형 SQL을 Power BI Desktop에 연결하고 보고서를 만드는 방법을 알아보려면 [주문형 SQL을 Power BI Desktop에 연결하고 보고서 만들기](tutorial-connect-power-bi-desktop.md)를 참조하세요.
 
