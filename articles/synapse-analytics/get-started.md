---
title: '자습서: Azure Synapse Analytics 시작'
description: 이 자습서에서는 Azure Synapse Analytics를 설정하고 사용하는 기본 단계에 대해 알아봅니다.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 05/19/2020
ms.openlocfilehash: daa8b594b06203c7de9a9b462be469dd71ed2e49
ms.sourcegitcommit: 6571e34e609785e82751f0b34f6237686470c1f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/15/2020
ms.locfileid: "84791863"
---
# <a name="get-started-with-azure-synapse-analytics"></a>Azure Synapse Analytics 시작

이 자습서에서는 Azure Synapse Analytics를 설정하고 사용하는 기본 단계를 안내합니다.

## <a name="prepare-a-storage-account"></a>스토리지 계정 준비

1. [Azure Portal](https://portal.azure.com)을 엽니다.
1. 다음 설정이 있는 새 스토리지 계정을 만듭니다.

    |탭|설정 | 제안 값 | Description |
    |---|---|---|---|
    |기본 사항|**스토리지 계정 이름**| 임의의 이름을 지정할 수 있습니다.|이 문서에서는 **contosolake**라고 합니다.|
    |기본 사항|**계정 종류**|**StorageV2**로 설정해야 합니다.||
    |기본 사항|**위치**|임의의 위치를 선택합니다.| Azure Synapse Analytics 작업 영역과 Azure Data Lake Storage Gen2 계정이 동일한 지역에 있는 것이 좋습니다.|
    |고급|**Data Lake Storage Gen2**|**Enabled**| Azure Synapse는 이 설정을 사용하도록 설정된 스토리지 계정에서만 작동합니다.|
    |||||

1. 스토리지 계정이 만들어지면 왼쪽 창에서 **액세스 제어(IAM)** 를 선택합니다. 그런 다음, 다음 역할을 할당하거나 이미 할당되어 있는지 확인합니다.
    1. 사용자를 **소유자** 역할에 할당합니다.
    1. 사용자를 **Storage Blob 데이터 소유자** 역할에 할당합니다.
1. 왼쪽 창에서 **컨테이너**를 선택하여 컨테이너를 만듭니다.
1. 임의의 컨테이너 이름을 지정할 수 있습니다. 이 문서에서는 컨테이너 이름을 **users**로 지정합니다.
1. **공용 액세스 수준** 기본 설정을 적용한 다음, **만들기**를 선택합니다.

다음 단계에서는 이 스토리지 계정을 "기본" 스토리지 계정으로 사용하고, 작업 영역 데이터를 저장하는 컨테이너를 사용하도록 Azure Synapse 작업 영역을 구성합니다. 작업 영역은 데이터를 Apache Spark 테이블에 저장합니다. Spark 애플리케이션 로그를 **/synapse/workspacename**이라는 폴더에 저장합니다.

## <a name="create-a-synapse-workspace"></a>Synapse 작업 영역 만들기

1. [Azure Portal](https://portal.azure.com)을 열고, 위쪽에서 **Synapse**를 검색합니다.
1. **서비스** 아래의 검색 결과에서 **Azure Synapse Analytics(작업 영역 미리 보기)** 를 선택합니다.
1. **추가**를 선택하여 다음 설정을 사용하는 작업 영역을 만듭니다.

    |탭|설정 | 제안 값 | Description |
    |---|---|---|---|
    |기본 사항|**작업 영역 이름**|모든 항목을 호출할 수 있습니다.| 이 문서에서는 **myworkspace**를 사용합니다.|
    |기본 사항|**지역**|스토리지 계정의 지역과 일치합니다.|

1. **Data Lake Storage Gen 2 선택** 아래에서 이전에 만든 계정과 컨테이너를 선택합니다.
1. **검토 + 만들기** > **만들기**를 차례로 선택합니다. 몇 분 안에 작업 영역이 준비됩니다.

## <a name="verify-access-to-the-storage-account"></a>스토리지 계정에 대한 액세스 확인

Azure Synapse 작업 영역의 관리 ID는 이미 스토리지 계정에 액세스할 수 있습니다. 다음 단계에 따라 확인합니다.

1. [Azure Portal](https://portal.azure.com) 및 작업 영역에 대해 선택한 기본 스토리지 계정을 엽니다.
1. 왼쪽 창에서 **액세스 제어(IAM)** 를 선택합니다.
1. 다음 역할을 할당하거나 이미 할당되어 있는지 확인합니다. 작업 영역 ID와 작업 영역 이름에 동일한 이름을 사용합니다.
    1. 스토리지 계정에 대한 **Storage Blob 데이터 기여자** 역할의 경우 **myworkspace**를 작업 영역 ID로 할당합니다.
    1. **myworkspace**를 작업 영역 이름으로 할당합니다.

1. **저장**을 선택합니다.

## <a name="open-synapse-studio"></a>Synapse Studio 열기

Azure Synapse 작업 영역이 만들어지면 다음 두 가지 방법으로 Synapse Studio를 열 수 있습니다.

* [Azure Portal](https://portal.azure.com)에서 Synapse 작업 영역을 엽니다. **개요** 섹션의 위쪽에서 **Synapse Studio 시작**을 선택합니다.
* https://web.azuresynapse.net 으로 이동하여 작업 영역에 로그인합니다.

## <a name="create-a-sql-pool"></a>SQL 풀 만들기

1. Synapse Studio의 왼쪽 창에서 **관리** > **SQL 풀**을 차례로 선택합니다.
1. **새로 만들기**를 선택하고 다음 설정을 입력합니다.

    |설정 | 제안 값 | 
    |---|---|---|
    |**SQL 풀 이름**| **SQLDB1**|
    |**성능 수준**|**DW100C**|
    |||

1. **검토 + 만들기** > **만들기**를 차례로 선택합니다. 몇 분 후에 SQL 풀이 준비됩니다. SQL 풀이 **SQLDB1**이라고도 하는 SQL 풀 데이터베이스와 연결됩니다.

SQL 풀은 활성 상태인 동안 청구 가능한 리소스를 사용합니다. 비용을 줄이기 위해 풀을 일시 중지할 수 있습니다.

## <a name="create-an-apache-spark-pool"></a>Apache Spark 풀 만들기

1. Synapse Studio의 왼쪽 창에서 **관리** > **Apache Spark 풀**을 차례로 선택합니다.
1. **새로 만들기**를 선택하고 다음 설정을 입력합니다.

    |설정 | 제안 값 | 
    |---|---|---|
    |**Apache Spark 풀 이름**|**Spark1**
    |**노드 크기**| **소형**|
    |**노드 수**| 최솟값 및 최댓값을 각각 3으로 설정합니다.|

1. **검토 + 만들기** > **만들기**를 차례로 선택합니다. 몇 초 후에 Apache Spark 풀이 준비됩니다.

> [!NOTE]
> 이름과 관계없이 Apache Spark 풀은 SQL 풀과 다릅니다. 이는 Spark와 상호 작용하는 방법을 Synapse 작업 영역에 알리는 데 사용하는 몇 가지 기본 메타데이터일 뿐입니다.

메타데이터이므로 Spark 풀을 시작하거나 중지할 수 없습니다.

Azure Synapse에서 Spark 작업을 수행할 때 사용할 Spark 풀을 지정합니다. 이 풀은 사용할 Spark 리소스의 수를 Azure Synapse에 알려줍니다. 사용하는 리소스에 대해서만 비용을 지불합니다. 풀 사용을 적극적으로 중지하면 리소스가 자동으로 시간 제한되고 재활용됩니다.

> [!NOTE]
> Spark 데이터베이스는 Spark 풀에서 독립적으로 만들어집니다. 작업 영역에는 항상 **default**라는 Spark 데이터베이스가 있습니다. Spark 데이터베이스는 추가로 만들 수 있습니다.

## <a name="the-sql-on-demand-pool"></a>SQL 주문형 풀

모든 작업 영역에는 **SQL 주문형**이라는 미리 빌드된 풀이 제공됩니다. 이 풀은 삭제할 수 없습니다. SQL 주문형 풀을 사용하면 Azure Synapse에서 SQL 풀을 만들거나 이를 관리하도록 고려하지 않고도 SQL을 사용할 수 있습니다.

다른 종류의 풀과 달리 SQL 주문형에 대한 요금 청구는 쿼리를 실행하는 데 사용된 리소스의 수가 아니라 쿼리를 실행하는 데 검사된 데이터의 양을 기준으로 합니다.

* SQL 주문형에는 SQL 주문형 풀과 별도로 존재하는 자체 SQL 주문형 데이터베이스가 있습니다.
* 작업 영역에는 항상 **SQL 주문형**이라는 하나의 SQL 주문형 풀만 있습니다.

## <a name="load-the-nyc-taxi-sample-data-into-the-sqldb1-database"></a>SQLDB1 데이터베이스에 NYC 택시 샘플 데이터 로드

1. Synapse Studio 맨 위쪽의 파란색 메뉴에서 **?** 를 선택합니다. 아이콘.
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

## <a name="analyze-nyc-taxi-data-in-spark-databases-using-sql-on-demand"></a>SQL 주문형을 사용하여 Spark 데이터베이스에서 NYC 택시 데이터 분석

Spark 데이터베이스의 테이블은 자동으로 표시되며, SQL 주문형에서 이를 쿼리할 수 있습니다.

1. Synapse Studio에서 **개발** 허브로 이동하여 새 SQL 스크립트를 만듭니다.
1. **연결 대상**을 **SQL 주문형**으로 설정합니다.
1. 다음 텍스트를 스크립트에 붙여넣고, 해당 스크립트를 실행합니다.

    ```sql
    SELECT *
    FROM nyctaxi.dbo.passengercountstats
    ```

    > [!NOTE]
    > SQL 주문형을 사용하는 쿼리를 처음 실행하는 경우 SQL 주문형에서 쿼리를 실행하는 데 필요한 SQL 리소스를 수집하는 데 약 10초 정도 걸립니다. 이후 쿼리는 훨씬 더 빠릅니다.
  
## <a name="orchestrate-activities-with-pipelines"></a>파이프라인을 사용하여 작업 오케스트레이션

Azure Synapse에서는 다양한 작업을 오케스트레이션할 수 있습니다.

1. Synapse Studio에서 **오케스트레이션** 허브로 이동합니다.
1. **+**  > **파이프라인**을 선택하여 새 파이프라인을 만듭니다.
1. **개발** 허브로 이동하여 이전에 만든 Notebook을 찾습니다.
1. 해당 Notebook을 파이프라인으로 끕니다.
1. 파이프라인에서 **트리거 추가** > **새로 만들기/편집**을 차례로 선택합니다.
1. **트리거 선택**에서 **새로 만들기**를 선택한 다음, **되풀이**에서 트리거가 1시간마다 실행되도록 설정합니다.
1. **확인**을 선택합니다.
1. **모두 게시**를 선택합니다. 파이프라인이 1시간마다 실행됩니다.
1. 다음 시간을 기다리지 않고 지금 파이프라인을 실행하려면 **트리거 추가** > **새로 만들기/편집**을 차례로 선택합니다.

## <a name="work-with-data-in-a-storage-account"></a>스토리지 계정에서 데이터 작업

지금까지 데이터가 작업 영역의 데이터베이스에 있는 시나리오에 대해 살펴보았습니다. 이제 스토리지 계정에서 파일을 사용하는 방법을 살펴보겠습니다. 이 시나리오에서는 작업 영역을 만들 때 지정한 작업 영역 및 컨테이너의 기본 스토리지 계정을 사용합니다.

* 스토리지 계정의 이름: **contosolake**
* 스토리지 계정에 있는 컨테이너의 이름: **users**

### <a name="create-csv-and-parquet-files-in-your-storage-account"></a>스토리지 계정에 CSV 및 Parquet 파일 만들기

Notebook에서 다음 코드를 실행합니다. CSV 파일 및 parquet 파일을 스토리지 계정에 만듭니다.

```py
%%pyspark
df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df = df.repartition(1) # This ensure we'll get a single file during write()
df.write.mode("overwrite").csv("/NYCTaxi/PassengerCountStats.csv")
df.write.mode("overwrite").parquet("/NYCTaxi/PassengerCountStats.parquet")
```

### <a name="analyze-data-in-a-storage-account"></a>스토리지 계정에서 데이터 분석

1. Synapse Studio에서 **데이터** 허브로 이동한 다음, **연결됨**을 선택합니다.
1. **스토리지 계정** > **myworkspace(기본 - contosolake)** 로 차례로 이동합니다.
1. **users(기본)** 를 선택합니다. **NYCTaxi** 폴더가 표시됩니다. 내부에는 **PassengerCountStats.csv** 및 **PassengerCountStats.parquet**라는 두 개의 폴더가 표시됩니다.
1. **PassengerCountStats. parquet** 폴더를 엽니다. 내부에는 *part-00000-2638e00c-0790-496b-a523-578da9a15019-c000.snappy.parquet*와 같은 이름의 parquet 파일이 표시됩니다.
1. 마우스 오른쪽 단추로 **.parquet**를 클릭한 다음, **새 Notebook**을 선택합니다. 다음과 같은 셀이 있는 Notebook을 만듭니다.

    ```py
    %%pyspark
    data_path = spark.read.load('abfss://users@contosolake.dfs.core.windows.net/NYCTaxi/PassengerCountStats.parquet/part-00000-1f251a58-d8ac-4972-9215-8d528d490690-c000.snappy.parquet', format='parquet')
    data_path.show(100)
    ```

1. 셀을 실행합니다.
1. 마우스 오른쪽 단추로 내부의 parquet 파일을 클릭한 다음, **새 SQL 스크립트** > **상위 100개 행 선택**을 차례로 선택합니다. 다음과 같은 SQL 스크립트를 만듭니다.

    ```sql
    SELECT TOP 100 *
    FROM OPENROWSET(
        BULK 'https://contosolake.dfs.core.windows.net/users/NYCTaxi/PassengerCountStats.parquet/part-00000-1f251a58-d8ac-4972-9215-8d528d490690-c000.snappy.parquet',
        FORMAT='PARQUET'
    ) AS [r];
    ```

     스크립트에서 **연결 대상** 필드가 **SQL 주문형**으로 설정됩니다.

1. 스크립트를 실행합니다.

## <a name="visualize-data-with-power-bi"></a>Power BI를 사용하여 데이터 시각화

NYC 택시 데이터에서 집계된 데이터 세트를 다음 두 테이블에 만들었습니다.
- **nyctaxi.passengercountstats**
- **SQLDB1.dbo.PassengerCountStats**

Power BI 작업 영역을 Azure Synapse 작업 영역에 연결할 수 있습니다. 이렇게 하면 데이터를 Power BI 작업 영역에 쉽게 가져올 수 있습니다. Azure Synapse 작업 영역에서 Power BI 보고서를 직접 편집할 수 있습니다.

### <a name="create-a-power-bi-workspace"></a>Power BI 작업 영역 만들기

1. [powerbi.microsoft.com](https://powerbi.microsoft.com/)에 로그인합니다.
1. **NYCTaxiWorkspace1**이라는 새 Power BI 작업 영역을 만듭니다.

### <a name="link-your-azure-synapse-workspace-to-your-new-power-bi-workspace"></a>새 Power BI 작업 영역에 Azure Synapse 작업 영역 연결

1. Synapse Studio에서 **관리** > **연결된 서비스**로 차례로 이동합니다.
1. **새로 만들기** > **Power BI에 연결**을 차례로 선택하고, 다음 필드를 설정합니다.

    |설정 | 제안 값 | 
    |---|---|
    |**이름**|**NYCTaxiWorkspace1**|
    |**작업 영역 이름**|**NYCTaxiWorkspace1**|

1. **만들기**를 선택합니다.

### <a name="create-a-power-bi-dataset-that-uses-data-in-your-azure-synapse-workspace"></a>Azure Synapse 작업 영역의 데이터를 사용하는 Power BI 데이터 세트 만들기

1. Synapse Studio에서 **개발** > **Power BI**로 차례로 이동합니다.
1. **NYCTaxiWorkspace1** > **Power BI 데이터 세트**로 차례로 이동하여 **새 Power BI 데이터 세트**를 선택합니다.
1. 마우스로 **SQLDB1** 데이터베이스 위를 가리키고, **.pbids 파일 다운로드**를 선택합니다.
1. 다운로드한 **.pbids** 파일을 엽니다. Power BI 데스크톱이 열리고, Azure Synapse 작업 영역의 **SQLDB1**에 자동으로 연결됩니다.
1. **SQL 서버 데이터베이스**라는 대화 상자가 표시되는 경우 다음을 수행합니다.
    1. **Microsoft 계정**을 선택합니다.
    1. **로그인**을 선택하고 계정에 로그인합니다.
    1. **연결**을 선택합니다.
1. **탐색기** 대화 상자가 열리면 **PassengerCountStats** 테이블을 확인하고 **로드**를 선택합니다.
1. **연결 설정** 대화 상자가 표시되면 **DirectQuery** > **확인**을 차례로 선택합니다.
1. 왼쪽에서 **보고서** 단추를 선택합니다.
1. **꺾은선형 차트**를 보고서에 추가합니다.
    1. **PassengerCount** 열을 **시각화** > **축**으로 끕니다.
    1. **SumTripDistance** 및 **AvgTripDistance** 열을 **시각화** > **값**으로 끕니다.
1. **홈** 탭에서 **게시**를 선택합니다.
1. **저장**을 선택하여 변경 내용을 저장합니다.
1. **PassengerAnalysis.pbix**라는 파일 이름을 선택한 다음, **저장**을 선택합니다.
1. **대상 선택**에서 **NYCTaxiWorkspace1**을 선택한 다음, **선택**을 클릭합니다.
1. 게시가 완료될 때까지 기다립니다.

### <a name="configure-authentication-for-your-dataset"></a>데이터 세트에 대한 인증 구성

1. [powerbi.microsoft.com](https://powerbi.microsoft.com/)을 열고 **로그인**합니다.
1. 왼쪽의 **작업 영역** 아래에서 **NYCTaxiWorkspace1** 작업 영역을 선택합니다.
1. 해당 작업 영역 내에서 **승객 분석**이라는 데이터 세트 및 **승객 분석**이라는 보고서를 찾습니다.
1. 마우스로 **PassengerAnalysis** 데이터 세트 위를 가리키고, 줄임표(...) 단추를 선택한 다음, **설정**을 선택합니다.
1. **데이터 원본 자격 증명**에서 **인증 방법**을 **OAuth2**로 설정한 다음, **로그인**을 선택합니다.

### <a name="edit-a-report-in-synapse-studio"></a>Synapse Studio에서 보고서 편집

1. Synapse Studio로 돌아가서 **닫기 및 새로 고침**을 선택합니다.
1. **개발** 허브로 이동합니다.
1. 마우스로 **Power BI** 위를 가리키고, **Power BI 보고서** 노드 새로 고침을 선택합니다.
1. **Power BI** 아래에 다음과 같이 표시됩니다.
    1. **NYCTaxiWorkspace1** > **Power BI 데이터 세트** 아래에 **PassengerAnalysis**라는 새 데이터 세트
    1. **NYCTaxiWorkspace1** > **Power BI 보고서** 아래에 **PassengerAnalysis**라는 새 보고서
1. **PassengerAnalysis** 보고서를 선택합니다. 보고서가 열리고, Synapse Studio 내에서 직접 편집할 수 있습니다.

## <a name="monitor-activities"></a>모니터링 활동

1. Synapse Studio에서 **모니터** 허브로 이동합니다.
1. 이 위치에서는 작업 영역에서 수행되는 모든 활동의 기록과 현재 활성 상태인 활동을 확인할 수 있습니다.
1. **파이프라인 실행**, **Apache Spark 애플리케이션** 및 **SQL 요청**을 검색하여 작업 영역에서 이미 수행한 작업을 확인합니다.

## <a name="next-steps"></a>다음 단계

[Azure Synapse Analytics(작업 영역 미리 보기)](overview-what-is.md)에 대해 자세히 알아봅니다.

