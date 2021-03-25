---
title: Azure Synapse Studio Notebook
description: 이 자습서에서는 Apache Spark 및 서버리스 SQL 풀을 사용하여 Power BI 대시보드를 만드는 방법을 간략히 설명합니다.
services: synapse-analytics
author: midesa
ms.author: midesa
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: spark
ms.topic: tutorial
ms.date: 11/16/2020
ms.openlocfilehash: 53fb256d3a0fba1cc10cafb239ff7f7bb21ac4eb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98121076"
---
# <a name="tutorial-create-a-power-bi-report-using-apache-spark-and-azure-synapse-analytics"></a>자습서: Apache Spark 및 Azure Synapse Analytics를 사용하여 Power BI 보고서 만들기

조직에서 주요 비즈니스 관련자에게 서비스를 제공하기 전에 대량의 데이터를 처리해야 하는 경우가 많습니다. 이 자습서에서는 Azure Synapse Analytics의 통합 환경을 활용하여 Apache Spark를 통해 데이터를 처리하고, 나중에 Power BI 및 서버리스 SQL을 통해 최종 사용자에게 데이터를 제공하는 방법에 대해 알아봅니다.

## <a name="before-you-begin"></a>시작하기 전에
- 기본 스토리지로 구성된 ADLS Gen2 스토리지 계정이 있는 [Azure Synapse Analytics 작업 영역](../quickstart-create-workspace.md) 
- 데이터를 시각화하는 Power BI 작업 영역 및 Power BI Desktop. 자세한 내용은 [Power BI 작업 영역 만들기](/power-bi/service-create-the-new-workspaces) 및 [Power BI Desktop 설치](https://powerbi.microsoft.com/downloads/)를 참조하세요.
- Azure Synapse Analytics 및 Power BI 작업 영역을 연결하는 연결된 서비스. 자세한 내용은 [Power BI 작업 영역에 연결](../quickstart-power-bi.md)을 참조하세요.
- Synapse Analytics 작업 영역의 서버리스 Apache Spark 풀. 자세한 내용은 [서버리스 Apache Spark 풀 만들기](../quickstart-create-apache-spark-pool-studio.md)를 참조하세요.
  
## <a name="download-and-prepare-the-data"></a>데이터 다운로드 및 준비
다음 예제에서는 Apache Spark를 사용하여 뉴욕의 택시 주행 팁 데이터에 대한 일부 분석을 수행합니다. 데이터는 [Azure 공개 데이터 세트](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/)를 통해 사용할 수 있습니다. 이 데이터 세트의 하위 집합에는 각 이동에 대한 정보, 시작 및 종료 시간과 위치, 비용 및 기타 흥미로운 특성을 포함한 노랑 택시 이동에 대한 정보가 포함되어 있습니다.

1. 다음 줄을 실행하여 새 셀에 코드를 붙여넣어 Spark 데이터 프레임을 만듭니다. Open Datasets API를 통해 데이터를 검색합니다. 이 데이터를 모두 풀하면 약 15억 행이 생성됩니다. 다음 코드 예제에서는 start_date 및 end_date를 사용하여 한 달 분량의 데이터를 반환하는 필터를 적용합니다.
   
   ```python
    from azureml.opendatasets import NycTlcYellow
    from dateutil import parser
    from datetime import datetime

    end_date = parser.parse('2018-06-06')
    start_date = parser.parse('2018-05-01')
    nyc_tlc = NycTlcYellow(start_date=start_date, end_date=end_date)
    filtered_df = nyc_tlc.to_spark_dataframe()
   ```
2. Apache Spark SQL을 사용하여 NycTlcTutorial이라는 데이터베이스를 만듭니다. 이 데이터베이스를 사용하여 데이터 처리 결과를 저장합니다.
   ```python
   %%pyspark
    spark.sql("CREATE DATABASE IF NOT EXISTS NycTlcTutorial")
   ```
3. 다음으로, Spark 데이터 프레임 작업을 사용하여 데이터를 처리합니다. 다음 코드에서는 다음과 같은 변환을 수행합니다.
   1. 필요하지 않은 열 제거
   2. 필터링을 통해 이상값/잘못된 값을 제거
   3. 추가 분석을 위한 새 기능(예: ```tripTimeSecs``` 및 ```tipped```) 만들기
    ```python
    from pyspark.sql.functions import unix_timestamp, date_format, col, when

    taxi_df = filtered_df.select('totalAmount', 'fareAmount', 'tipAmount', 'paymentType', 'rateCodeId', 'passengerCount'\
                                    , 'tripDistance', 'tpepPickupDateTime', 'tpepDropoffDateTime'\
                                    , date_format('tpepPickupDateTime', 'hh').alias('pickupHour')\
                                    , date_format('tpepPickupDateTime', 'EEEE').alias('weekdayString')\
                                    , (unix_timestamp(col('tpepDropoffDateTime')) - unix_timestamp(col('tpepPickupDateTime'))).alias('tripTimeSecs')\
                                    , (when(col('tipAmount') > 0, 1).otherwise(0)).alias('tipped')
                                    )\
                            .filter((filtered_df.passengerCount > 0) & (filtered_df.passengerCount < 8)\
                                    & (filtered_df.tipAmount >= 0) & (filtered_df.tipAmount <= 25)\
                                    & (filtered_df.fareAmount >= 1) & (filtered_df.fareAmount <= 250)\
                                    & (filtered_df.tipAmount < filtered_df.fareAmount)\
                                    & (filtered_df.tripDistance > 0) & (filtered_df.tripDistance <= 100)\
                                    & (filtered_df.rateCodeId <= 5)
                                    & (filtered_df.paymentType.isin({"1", "2"})))
    ```
4. 마지막으로, Apache Spark ```saveAsTable``` 메서드를 사용하여 데이터 프레임을 저장합니다. 그러면 나중에 서버리스 SQL 풀을 사용하여 동일한 테이블을 쿼리하고 연결할 수 있습니다.
  ```python
     taxi_df.write.mode("overwrite").saveAsTable("NycTlcTutorial.nyctaxi")
  ```
   
## <a name="query-data-using-serverless-sql-pools"></a>서버리스 SQL 풀을 사용하여 데이터 쿼리
Azure Synapse Analytics를 사용하면 여러 작업 영역 컴퓨팅 엔진이 서버리스 Spark Apache 풀과 서버리스 SQL 풀 간에 데이터베이스와 테이블을 공유할 수 있습니다. 이는 Synapse [공유 메타데이터 관리](../metadata/overview.md) 기능을 통해 구동됩니다. 따라서 Spark에서 만든 데이터베이스 및 해당 Parquet 지원 테이블이 작업 영역 서버리스 SQL 풀에 표시됩니다.

서버리스 SQL 풀을 사용하여 Apache Spark 테이블을 쿼리하려면 다음을 수행합니다.
   1. Apache Spark 테이블이 저장되면 **데이터** 탭으로 전환합니다.
   
   2. **작업 영역** 아래에서 방금 만든 Apache Spark 테이블을 찾아서 **새 SQL 스크립트** 를 선택한 다음, **상위 100개 행 선택** 을 선택합니다. 
      
      :::image type="content" source="../spark/media/apache-spark-power-bi/query-spark-table-with-sql.png" alt-text="SQL 쿼리" border="true":::

   3. 계속하여 SQL 차트 옵션을 통해 쿼리를 구체화하거나 결과를 시각화할 수도 있습니다.

## <a name="connect-to-power-bi"></a>Power BI에 연결
다음으로, 서버리스 SQL 풀을 Power BI 작업 영역에 연결합니다. 작업 영역이 연결되면 Azure Synapse Analytics 및 Power BI 데스크톱 모두에서 Power BI 보고서를 직접 만들 수 있습니다.

>[!Note]
> 시작하기 전에 [Power BI 작업 영역](../quickstart-power-bi.md)에 연결된 서비스를 설정하고 [Power BI 데스크톱](/power-bi/service-create-the-new-workspaces)을 다운로드해야 합니다.  

서버리스 SQL 풀을 Power BI 작업 영역에 연결하려면 다음을 수행합니다.

1.  **Power BI 데이터 세트** 탭으로 이동하여 **+ 새 데이터 세트** 옵션을 선택합니다. 프롬프트에서 데이터 원본으로 사용하려는 SQL Analytics 데이터베이스에서 .pbids 파일을 다운로드합니다. 
   :::image type="content" source="../spark/media/apache-spark-power-bi/power-bi-datasets.png" alt-text="Power BI 데이터 세트" border="true":::

2.  Power BI Desktop에서 파일을 열어 데이터 세트를 만듭니다. 파일이 열리면 **Microsoft 계정** 및 **가져오기** 옵션을 사용하여 SQL 서버 데이터베이스에 연결합니다. 
   

## <a name="create-a-power-bi-report"></a>Power BI 보고서 만들기
1. 이제 Power BI 데스크톱에서 **주요 인플루언서** 차트를 보고서에 추가할 수 있습니다.
   
   1. 평균 *tipAmount* 열을 **분석** 축으로 끕니다.
   
   2. **weekdayString**, 평균 **tripDistance** 및 평균 **tripTimeSecs** 열을 **설명 기준** 축으로 끕니다. 
   
   :::image type="content" source="../spark/media/apache-spark-power-bi/power-bi-desktop.png" alt-text="Power BI Desktop" border="true":::

2. Power BI 데스크톱 [홈] 탭에서 변경 내용 **게시** 및 **저장** 을 선택합니다. 파일 이름을 입력하고, 이 보고서를 *NycTaxiTutorial 작업 영역* 에 저장합니다.
   
3. 또한 Azure Synapse Analytics 작업 영역 내에서 Power BI 시각화를 만들 수도 있습니다. 이렇게 하려면 Azure Synapse 작업 영역에서 **개발** 탭으로 이동하여 Power BI 탭을 엽니다. 여기서 보고서를 선택하고, 추가 시각화를 계속 빌드할 수 있습니다. 
   
   :::image type="content" source="../spark/media/apache-spark-power-bi/power-bi-synapse.png" alt-text="Azure Synapse Analytics 작업 영역" border="true":::

서버리스 SQL을 통해 데이터 세트를 만들고 Power BI에 연결하는 방법에 대한 자세한 내용은 이 [Power BI 데스크톱에 연결](../../synapse-analytics/sql/tutorial-connect-power-bi-desktop.md) 자습서를 참조하세요.

## <a name="next-steps"></a>다음 단계
다음 문서 및 자습서를 참조하여 Azure Synapse Analytics의 데이터 시각화 기능에 대해 자세히 알아볼 수 있습니다.
   - [서버리스 Apache Spark 풀을 사용하여 데이터 시각화](../spark/apache-spark-data-visualization-tutorial.md)
   - [Apache Spark 풀을 사용한 데이터 시각화 개요](../spark/apache-spark-data-visualization.md)