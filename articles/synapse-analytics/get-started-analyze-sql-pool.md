---
title: '자습서: 전용 SQL 풀을 사용하여 데이터 분석 시작'
description: 이 자습서에서는 NYC Taxi 샘플 데이터를 사용하여 SQL 풀의 분석 기능을 탐색합니다.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: tutorial
ms.date: 07/20/2020
ms.openlocfilehash: c46adf9e9f5c1b2e74c1098ebf137c4556bfc58d
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/17/2020
ms.locfileid: "92147554"
---
# <a name="analyze-data-with-dedicated-sql-pools"></a>전용 SQL 풀을 사용하여 데이터 분석

Azure Synapse Analytics는 전용 SQL 풀을 사용하여 데이터를 분석하는 기능을 제공합니다. 이 자습서에서는 NYC Taxi 데이터를 사용하여 전용 SQL 풀의 기능을 탐색합니다.

## <a name="load-the-nyc-taxi-data-into-sqldb1"></a>NYC Taxi 데이터를 SQLDB1에 로드

1. Synapse Studio에서 **개발** 허브로 이동한 다음, 새 SQL 스크립트를 만듭니다.
1. 스크립트의 '연결 대상' 섹션에서 'SQLDB1' 풀(이 자습서의 [1단계](https://docs.microsoft.com/azure/synapse-analytics/get-started-create-workspace#create-a-sql-pool)에서 만든 풀)을 선택합니다.
1. 다음 코드를 입력합니다.
    ```
    CREATE TABLE [dbo].[Trip]
    (
        [DateID] int NOT NULL,
        [MedallionID] int NOT NULL,
        [HackneyLicenseID] int NOT NULL,
        [PickupTimeID] int NOT NULL,
        [DropoffTimeID] int NOT NULL,
        [PickupGeographyID] int NULL,
        [DropoffGeographyID] int NULL,
        [PickupLatitude] float NULL,
        [PickupLongitude] float NULL,
        [PickupLatLong] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DropoffLatitude] float NULL,
        [DropoffLongitude] float NULL,
        [DropoffLatLong] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [PassengerCount] int NULL,
        [TripDurationSeconds] int NULL,
        [TripDistanceMiles] float NULL,
        [PaymentType] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [FareAmount] money NULL,
        [SurchargeAmount] money NULL,
        [TaxAmount] money NULL,
        [TipAmount] money NULL,
        [TollsAmount] money NULL,
        [TotalAmount] money NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    );

    COPY INTO [dbo].[Trip]
    FROM 'https://nytaxiblob.blob.core.windows.net/2013/Trip2013/QID6392_20171107_05910_0.txt.gz'
    WITH
    (
        FILE_TYPE = 'CSV',
        FIELDTERMINATOR = '|',
        FIELDQUOTE = '',
        ROWTERMINATOR='0X0A',
        COMPRESSION = 'GZIP'
    )
    OPTION (LABEL = 'COPY : Load [dbo].[Trip] - Taxi dataset');
    ```
1. 이 스크립트를 실행하는 데 약 1분이 걸립니다. 2백만 행의 NYC Taxi 데이터를 **dbo.Trip** 이라는 테이블에 로드합니다.

## <a name="explore-the-nyc-taxi-data-in-the-dedicated-sql-pool"></a>전용 SQL 풀에서 NYC Taxi 데이터 검색

1. Synapse Studio에서 **데이터** 허브로 이동합니다.
1. **SQLDB1** > **테이블** 로 차례로 이동합니다. 로드된 몇 가지 테이블이 표시됩니다.
1. 마우스 오른쪽 단추로 **dbo.Trip** 테이블을 클릭하고, **새 SQL 스크립트** > **상위 100개 행 선택** 을 차례로 선택합니다.
1. 새 SQL 스크립트가 만들어져 실행될 때까지 기다립니다.
1. SQL 스크립트의 위쪽에서 **연결 대상** 이 자동으로 **SQLDB1** 이라는 SQL 풀로 설정됩니다.
1. SQL 스크립트의 텍스트를 다음 코드로 바꾸고 실행합니다.

    ```sql
    SELECT PassengerCount,
          SUM(TripDistanceMiles) as SumTripDistance,
          AVG(TripDistanceMiles) as AvgTripDistance
    FROM  dbo.Trip
    WHERE TripDistanceMiles > 0 AND PassengerCount > 0
    GROUP BY PassengerCount
    ORDER BY PassengerCount
    ```

    이 쿼리에서는 총 주행 거리 및 평균 주행 거리와 승객 수 간의 관계를 보여 줍니다.
1. SQL 스크립트 결과 창에서 결과를 꺾은선형 차트로 시각화하려면 **보기** 를 **차트** 로 변경합니다.



## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Spark를 사용하여 분석](get-started-analyze-spark.md)

