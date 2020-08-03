---
title: '자습서: SQL 풀을 사용하여 데이터 분석 시작'
description: 이 자습서에서는 NYC Taxi 샘플 데이터를 사용하여 SQL 풀의 분석 기능을 탐색합니다.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 07/20/2020
ms.openlocfilehash: e2e1d0479b8edacaae8816d74db061eeedb805a7
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87325222"
---
# <a name="analyze-data-with-sql-pools"></a>SQL 풀을 사용하여 데이터 분석

Azure Synapse Analytics는 SQL 풀을 사용하여 데이터를 분석하는 기능을 제공합니다. 이 자습서에서는 NYC Taxi 샘플 데이터를 사용하여 SQL 풀의 분석 기능을 탐색합니다.

## <a name="load-the-nyc-taxi-sample-data-into-the-sqldb1-database"></a>SQLDB1 데이터베이스에 NYC 택시 샘플 데이터 로드

1. Synapse Studio의 맨 위쪽의 파란색 메뉴에서 물음표( **?** ) 아이콘을 선택합니다.
1. **시작** > **허브 시작**을 차례로 선택합니다.
1. **쿼리 샘플 데이터**라는 레이블이 지정된 카드에서 **SQLDB1**이라는 SQL 풀을 선택합니다.
1. **데이터 쿼리**를 선택합니다. "샘플 데이터 로드 중"이라는 알림이 잠시 표시됩니다. Synapse Studio 위쪽 근처의 연한 파란색 상태 표시줄에서 데이터가 SQLDB1에 로드되고 있음을 나타냅니다.
1. 상태 표시줄이 녹색으로 바뀌면 해제합니다.

## <a name="explore-the-nyc-taxi-data-in-the-sql-pool"></a>SQL 풀에서 NYC 택시 데이터 검색

1. Synapse Studio에서 **데이터** 허브로 이동합니다.
1. **SQLDB1** > **테이블**로 차례로 이동합니다. 로드된 몇 가지 테이블이 표시됩니다.
1. 마우스 오른쪽 단추로 **dbo.Trip** 테이블을 클릭하고, **새 SQL 스크립트** > **상위 100개 행 선택**을 차례로 선택합니다.
1. 새 SQL 스크립트가 만들어져 실행될 때까지 기다립니다.
1. SQL 스크립트의 위쪽에서 **연결 대상**이 자동으로 **SQLDB1**이라는 SQL 풀로 설정됩니다.
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
1. SQL 스크립트 결과 창에서 결과를 꺾은선형 차트로 시각화하려면 **보기**를 **차트**로 변경합니다.



## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Spark를 사용하여 분석](get-started-analyze-spark.md)

