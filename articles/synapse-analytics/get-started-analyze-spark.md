---
title: '자습서: Spark를 사용하여 분석 시작하기'
description: 이 자습서에서는 Azure Synapse Analytics를 설정하고 사용하는 기본 단계에 대해 알아봅니다.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 07/20/2020
ms.openlocfilehash: 5c6b35c1d9f00cae8fc688569e3a491679900995
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87093801"
---
# <a name="analyze-with-apache-spark"></a>Apache Spark를 사용하여 분석

이 자습서에서는 Azure Synapse용 Apache Spark를 사용하여 데이터를 로드하고 분석하는 기본 단계에 대해 알아봅니다.

## <a name="load-the-nyc-taxi-data-into-the-spark-nyctaxi-database"></a>Spark nyctaxi 데이터베이스에 NYC 택시 데이터 로드

**SQLDB1**의 테이블에는 사용할 수 있는 데이터가 있습니다. 이제 이를 **nyctaxi**라는 Spark 데이터베이스에 로드합니다.

1. Synapse Studio에서 **개발** 허브로 이동합니다.
1. **+**  > **Notebook**을 선택합니다.
1. Notebook의 위쪽에서 **연결 대상** 값을 **Spark1**로 설정합니다.
1. **코드 추가**를 선택하여 Notebook 코드 셀을 추가하고, 다음 텍스트를 붙여넣습니다.

    ```scala
    %%spark
    spark.sql("CREATE DATABASE IF NOT EXISTS nyctaxi")
    val df = spark.read.sqlanalytics("SQLDB1.dbo.Trip") 
    df.write.mode("overwrite").saveAsTable("nyctaxi.trip")
    ```

1. **데이터** 허브로 이동하여 마우스 오른쪽 단추로 **데이터베이스**를 클릭한 다음, **새로 고침**을 선택합니다. 다음 데이터베이스가 표시됩니다.

    - **SQLDB1**(SQL 풀)
    - **nyctaxi**(Spark)

## <a name="analyze-the-nyc-taxi-data-using-spark-and-notebooks"></a>Spark 및 Notebook을 사용하여 NYC 택시 데이터 분석

1. Notebook으로 돌아갑니다.
1. 새 코드 셀을 만들고, 다음 텍스트를 입력합니다. 그런 다음, 셀을 실행하여 **nyctaxi** Spark 데이터베이스에 로드한 NYC 택시 데이터를 표시합니다.

   ```py
   %%pyspark
   df = spark.sql("SELECT * FROM nyctaxi.trip") 
   display(df)
   ```

1. 다음 코드를 실행하여 이전에 **SQLDB1** SQL 풀에서 수행한 것과 동일한 분석을 수행합니다. 이 코드는 분석 결과를 **nyctaxi.passengercountstats**라는 테이블에 저장하고, 결과를 시각화합니다.

   ```py
   %%pyspark
   df = spark.sql("""
      SELECT PassengerCount,
          SUM(TripDistanceMiles) as SumTripDistance,
          AVG(TripDistanceMiles) as AvgTripDistance
      FROM nyctaxi.trip
      WHERE TripDistanceMiles > 0 AND PassengerCount > 0
      GROUP BY PassengerCount
      ORDER BY PassengerCount
   """) 
   display(df)
   df.write.saveAsTable("nyctaxi.passengercountstats")
   ```

1. 셀 결과에서 **차트**를 선택하여 시각화된 데이터를 표시합니다.

## <a name="customize-data-visualization-with-spark-and-notebooks"></a>Spark 및 Notebook을 사용하여 데이터 시각화 데이터 사용자 지정

Notebook을 사용하여 차트가 렌더링되는 방법을 제어할 수 있습니다. 다음 코드에서는 간단한 예를 보여 줍니다. 인기 있는 **matplotlib** 및 **seaborn** 라이브러리를 사용합니다. 이 코드에서는 이전에 실행한 SQL 쿼리와 동일한 종류의 꺾은선형 차트를 렌더링합니다.

```py
%%pyspark
import matplotlib.pyplot
import seaborn

seaborn.set(style = "whitegrid")
df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df = df.toPandas()
seaborn.lineplot(x="PassengerCount", y="SumTripDistance" , data = df)
seaborn.lineplot(x="PassengerCount", y="AvgTripDistance" , data = df)
matplotlib.pyplot.show()
```



## <a name="load-data-from-a-spark-table-into-a-sql-pool-table"></a>Spark 테이블에서 SQL 풀 테이블로 데이터 로드

이전에 **SQLDB1.dbo.Trip** SQL 풀 테이블의 데이터를 **nyctaxi.trip** Spark 테이블에 복사했습니다. 그런 다음, Spark를 사용하여 데이터를 **nyctaxi.passengercountstats** Spark 테이블에 집계했습니다. 이제 **nyctaxi.passengercountstats**의 데이터를 **SQLDB1.dbo.PassengerCountStats**라는 SQL 풀 테이블에 복사하겠습니다.

노트북에서 다음 셀을 실행합니다. 집계된 Spark 테이블을 SQL 풀 테이블에 다시 복사합니다.

```scala
%%spark
val df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df.write.sqlanalytics("SQLDB1.dbo.PassengerCountStats", Constants.INTERNAL )
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [스토리지 데이터 분석](get-started-analyze-storage.md)


