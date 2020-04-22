---
title: 데이터 분석가 자습서 - 주문형 SQL(미리 보기)을 사용하여 Azure Synapse Studio(미리 보기)에서 Azure Open Datasets 분석
description: 이 자습서에서는 주문형 SQL(미리 보기)을 사용하여 여러 Azure Open Datasets를 결합하여 예비 데이터 분석을 쉽게 수행하고 Azure Synapse Studio에서 결과를 시각화하는 방법을 알아봅니다.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 1080001cb222f91503080914d7fb253e5ee82626
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81426282"
---
# <a name="use-sql-on-demand-preview-to-analyze-azure-open-datasets-and-visualize-the-results-in-azure-synapse-studio-preview"></a>주문형 SQL(미리 보기)을 사용하여 Azure Open Datasets를 분석하고 그 결과를 Azure Synapse Studio(미리 보기)에서 시각화

이 자습서에서는 주문형 SQL을 사용하여 여러 Azure Open Datasets를 결합하여 예비 데이터 분석을 수행한 다음, 그 결과를 Azure Synapse Studio에서 시각화하는 방법을 알아봅니다.

특히, 승차 및 하차 날짜/시간, 승차 및 하차 위치, 이동 거리, 항목별 요금, 요금 유형, 지불 유형 및 운전자가 보고한 승객 수가 포함된 [NYC(New York City) 택시 데이터 세트](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/)를 분석합니다.

분석의 초점은 시간 경과에 따른 택시 승차 횟수의 변화 추세를 파악하는 것입니다. 두 가지 다른 Azure Open Datasets([공휴일](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/) 및 [날씨 데이터](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/))를 분석하여 택시 승차 횟수의 이상값을 파악합니다.

## <a name="create-credentials"></a>자격 증명 만들기

```sql
-- There is no secret. We are using public storage account which doesn't need a secret.
CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/nyctlc]
WITH IDENTITY='SHARED ACCESS SIGNATURE',
SECRET = ''
GO

CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/holidaydatacontainer]
WITH IDENTITY='SHARED ACCESS SIGNATURE',
SECRET = ''
GO

CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/isdweatherdatacontainer]
WITH IDENTITY='SHARED ACCESS SIGNATURE',
SECRET = ''
GO
```

## <a name="automatic-schema-inference"></a>자동 스키마 유추

데이터가 Parquet 파일 형식으로 저장되어 자동 스키마 유추가 가능하기 때문에, 파일에 있는 모든 열의 데이터 유형을 나열하지 않고도 데이터를 쉽게 쿼리할 수 있습니다. 또한, 가상 열 메커니즘과 filepath 함수를 활용하여 파일의 특정 하위 집합을 필터링할 수 있습니다.

먼저 다음 쿼리를 실행하여 NYC 택시 데이터에 익숙해 지도록 하겠습니다.

```sql
SELECT TOP 100 * FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [nyc]
```

다음은 NYC 택시 데이터의 결과 조각을 보여줍니다.

![결과 조각](./media/tutorial-data-analyst/1.png)

마찬가지로 다음 쿼리를 사용하여 공휴일 데이터 세트를 쿼리할 수 있습니다.

```sql
SELECT TOP 100 * FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/holidaydatacontainer/Processed/*.parquet',
        FORMAT='PARQUET'
    ) AS [holidays]
```

다음은 공휴일 데이터 세트의 결과 조각을 보여줍니다.

![결과 조각 2](./media/tutorial-data-analyst/2.png)

마지막으로 다음 쿼리를 사용하여 날씨 데이터 세트를 쿼리할 수도 있습니다.

```sql
SELECT
    TOP 100 *
FROM  
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/isdweatherdatacontainer/ISDWeather/year=*/month=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [weather]
```

다음은 날씨 데이터 세트의 결과 조각을 보여줍니다.

![결과 조각 3](./media/tutorial-data-analyst/3.png)

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

다음은 연간 택시 승차 횟수에 대한 결과 조각을 보여줍니다.

![결과 조각 4](./media/tutorial-data-analyst/4.png)

Synapse Studio에서 테이블 보기에서 차트 보기로 전환하면 데이터를 시각화할 수 있습니다. 여러 차트 유형(영역, 막대형, 세로 막대형, 꺾은선형, 원형 및 분산형) 중에 선택할 수 있습니다. 이 경우 Category 열을 "current_year"로 설정하여 세로 막대형 차트를 그리겠습니다.

![결과 시각화 5](./media/tutorial-data-analyst/5.png)

시각화를 보면, 수년 동안 승차 횟수가 줄어드는 추세가 명확하게 보이는데, 아마도 승차 공유 회사의 인기가 최근에 높아졌기 때문인 것 같습니다.

> [!NOTE]
> 이 자습서를 작성할 당시, 2019년 데이터가 불완전했기 때문에 해당 연도의 승차 횟수가 크게 하락했습니다.

다음으로, 단일 연도(예: 2016년)에 대한 분석에 중점을 두겠습니다. 다음 쿼리는 해당 연도 중 일일 승차 횟수를 반환합니다.

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

![결과 조각 6](./media/tutorial-data-analyst/6.png)

다시, Category 열을 "current_day"로 설정하고 Legend(series) 열을 "rides_per_day"로 설정하여 세로 막대형 차트를 그려서 데이터를 쉽게 시각화할 수 있습니다.

![결과 시각화 7](./media/tutorial-data-analyst/7.png)

차트를 보면, 토요일이 피크인 주 단위 패턴이 있는 것을 볼 수 있습니다. 여름 몇 개월 동안은 휴가 기간으로 인에 택시 승차가 줄어듭니다. 하지만, 택시 승차 횟수가 언제 그리고 왜 발생하는지에 대한 명확한 패턴이 없이 크게 감소한 경우도 있습니다.

다음에는, NYC 택시 승차 횟수와 공휴일 데이터 세트를 결합하여 이러한 하락이 잠재적으로 공휴일과 상관 관계가 있는지 살펴보겠습니다.

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

![결과 시각화 8](./media/tutorial-data-analyst/8.png)

이번에는 공휴일 중 택시 승차 횟수를 강조하려고 합니다. 이를 위해 Category 열에는 "none"을 선택하고 Legend(series) 열에는 "rides_per_day"와 "holiday"를 선택하겠습니다.

![결과 시각화 9](./media/tutorial-data-analyst/9.png)

차트를 보면 공휴일 중 택시 승차 횟수가 적은 것이 명확히 보입니다. 하지만 1월 23일의 큰 하락은 여전히 설명할 수 없습니다. 날씨 데이터 세트를 쿼리하여 해당일의 NYC 날씨를 확인해보겠습니다.

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

![결과 시각화 10](./media/tutorial-data-analyst/10.png)

쿼리 결과에 따르면 택시 승차 횟수 하락의 원인은 다음과 같습니다.

- 그 날 NYC에 폭설(~30cm)로 인한 눈보라가 있었습니다.
- 추웠습니다(섭씨 영하의 온도).
- 바람이 강했습니다(~10m/s).

이 자습서에서는 데이터 분석가가 예비 데이터 분석을 신속하게 수행하고, 주문형 SQL을 사용하여 여러 데이터 세트를 쉽게 결합하고 Azure Synapse Studio를 사용하여 결과를 시각화하는 방법을 살펴봤습니다.

## <a name="next-steps"></a>다음 단계

[주문형 SQL을 Power BI Desktop에 연결하고 보고서 만들기](tutorial-connect-power-bi-desktop.md) 문서를 검토하여 주문형 SQL을 Power BI Desktop에 연결하고 보고서를 만드는 방법을 알아보세요.
 
