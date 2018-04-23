---
title: Azure HDInsight Spark 클러스터에서 대화형 쿼리 실행 | Microsoft Docs
description: HDInsight에서 Apache Spark 클러스터를 만드는 방법에 대한 HDInsight Spark 빠른 시작입니다.
keywords: spark 빠른 시작, 대화형 spark, 대화형 쿼리, hdinsight spark, azure spark
services: hdinsight
documentationcenter: ''
author: mumian
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: conceptual
ms.date: 11/29/2017
ms.author: jgao
ms.openlocfilehash: 177fb47c72e9abbafcda69416643fbd3848373bd
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2018
---
# <a name="run-interactive-queries-on-spark-clusters-in-hdinsight"></a>HDInsight의 Spark 클러스터에서 대화형 쿼리 실행

Jupyter 노트북을 사용하여 Spark 클러스터에 대해 대화형 Spark SQL 쿼리를 실행하는 방법에 대해 알아봅니다. 

[Jupyter 노트북](http://jupyter-notebook.readthedocs.io/en/latest/notebook.html)은 콘솔 기반 대화형 환경을 웹으로 확장하는 브라우저 기반 응용 프로그램입니다. HDInsight의 Spark에는 [Zeppelin Notebook](apache-spark-zeppelin-notebook.md)도 포함되어 있습니다. 이 자습서에서는 Jupyter Notebook이 사용됩니다.

HDInsight 클러스터의 Jupyter 노트북에는 **PySpark**, **PySpark3** 및 **Spark**의 3개 커널이 지원됩니다. 이 자습서에서는 **PySpark** 커널이 사용됩니다. 커널 및 **PySpark** 사용의 이점에 대한 자세한 내용은 [HDInsight에서 Apache Spark 클러스터와 함께 Jupyter Notebook 커널 사용](apache-spark-jupyter-notebook-kernels.md)을 참조하세요. Zeppelin Notebook을 사용하려면 [Azure HDInsight의 Apache Spark 클러스터에서 Zeppelin Notebook 사용](./apache-spark-zeppelin-notebook.md)을 참조하세요.

이 자습서에서는 csv 파일의 데이터를 쿼리합니다. 먼저 데이터를 데이터 프레임으로 Spark에 로드해야 합니다. 그런 다음 Jupyter Notebook을 사용하여 데이터 프레임에서 쿼리를 실행할 수 있습니다. 

## <a name="prerequisites"></a>필수 조건

* **Azure HDInsight Spark 클러스터**. 자세한 지침은 [Azure HDInsight에서 Apache Spark 클러스터 만들기](apache-spark-jupyter-spark-sql.md)를 참조하세요.
* **PySpark를 사용하는 Jupyter 노트북** - 자세한 지침은 [Jupyter Notebook 만들기](./apache-spark-jupyter-spark-sql.md#create-a-jupyter-notebook)를 참조하세요.

## <a name="create-a-dataframe-from-a-csv-file"></a>csv 파일에서 데이터 프레임 만들기

SQLContext를 사용하면 응용 프로그램은 기존 RDD, Hive 테이블 또는 데이터 원본에서 데이터 프레임을 만들 수 있습니다. 

**csv 파일에서 데이터 프레임을 만들려면**

1. 없는 경우 PySpark를 사용하여 Jupyter 노트북을 만듭니다. 자세한 지침은 [Jupyter Notebook 만들기](./apache-spark-jupyter-spark-sql.md#create-a-jupyter-notebook)를 참조하세요.

2. 노트북의 빈 셀에 다음 코드를 붙여넣은 다음 **Shift+Enter**를 눌러 해당 코드를 실행합니다. 코드는 이 시나리오에 필요한 형식을 가져옵니다.

    ```PySpark
    from pyspark.sql import *
    from pyspark.sql.types import *
    ```
    PySpark 커널을 사용하여 노트북을 만들면 첫 번째 코드 셀을 실행할 때 Spark 및 Hive 컨텍스트가 자동으로 만들어집니다. 컨텍스트를 명시적으로 만들 필요가 없습니다.

    Jupyter에서 대화형 쿼리를 실행하면 웹 브라우저 창 또는 탭 캡션에 노트북 제목과 함께 **(사용 중)** 상태가 표시됩니다. 또한 오른쪽 위 모서리에 있는 **PySpark** 텍스트 옆에 단색 원이 표시됩니다. 작업이 완료되면 속이 빈 원으로 변경됩니다.

    ![대화형 Spark SQL 쿼리 상태](./media/apache-spark-load-data-run-query/hdinsight-spark-interactive-spark-query-status.png "대화형 Spark SQL 쿼리 상태")

3. 다음 코드를 실행하여 데이터 프레임과 임시 테이블(**hvac**)을 만듭니다. 이 코드는 CSV 파일에서 사용 가능한 모든 열을 추출하지 않습니다. 

    ```PySpark
    # Create an RDD from sample data
    hvacText = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
    
    # Create a schema for our data
    Entry = Row('Date', 'Time', 'TargetTemp', 'ActualTemp', 'BuildingID')
    
    # Parse the data and create a schema
    hvacParts = hvacText.map(lambda s: s.split(',')).filter(lambda s: s[0] != 'Date')
    hvac = hvacParts.map(lambda p: Entry(str(p[0]), str(p[1]), int(p[2]), int(p[3]), int(p[6])))
    
    # Infer the schema and create a table       
    hvacTable = sqlContext.createDataFrame(hvac)
    hvacTable.registerTempTable('hvactemptable')
    dfw = DataFrameWriter(hvacTable)
    dfw.saveAsTable('hvac')
    ```
    다음 스크린샷에서는 HVAC.csv 파일의 스냅숏을 보여 줍니다. csv 파일에는 모든 HDInsigt Spark 클러스터가 함께 제공됩니다. 이 데이터는 건물의 온도 변화를 캡처합니다.

    ![대화형 Spark SQL 쿼리용 데이터의 스냅숏](./media/apache-spark-load-data-run-query/hdinsight-spark-sample-data-interactive-spark-sql-query.png "대화형 Spark SQL 쿼리용 데이터의 스냅숏")

## <a name="run-queries-on-the-dataframe"></a>데이터 프레임에서 쿼리 실행

테이블이 만들어지면 데이터에 대한 대화형 쿼리를 실행할 수 있습니다.

**쿼리를 실행하려면**

1. 노트북의 빈 셀에서 다음 코드를 실행합니다.

    ```PySpark
    %%sql
    SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"
    ```

   이제 노트북에서 PySpark 커널이 사용되므로 `%%sql` 매직을 사용하여 만든 **hvac** 임시 테이블에서 대화형 SQL 쿼리를 직접 실행할 수 있습니다. `%%sql` 매직 및 기타 PySpark 커널에서 사용 가능한 매직에 대한 자세한 내용은 [Spark HDInsight 클러스터와 함께 Jupyter Notebook에서 사용 가능한 커널](apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic)을 참조하세요.

   다음과 같은 테이블 형식 출력이 기본적으로 표시됩니다.

     ![대화형 Spark 쿼리 결과의 테이블 출력](./media/apache-spark-load-data-run-query/hdinsight-interactive-spark-query-result.png "대화형 Spark 쿼리 결과의 테이블 출력")

3. 다른 시각화로 결과를 볼 수도 있습니다. 동일한 출력에 대한 영역형 그래프를 보려면 **영역**을 선택한 다음 표시된 것처럼 다른 값을 설정합니다.

    ![대화형 Spark 쿼리 결과의 영역 그래프](./media/apache-spark-load-data-run-query/hdinsight-interactive-spark-query-result-area-chart.png "대화형 Spark 쿼리 결과의 영역 그래프")

10. Notebook의 **파일** 메뉴에서 **닫기 및 검사점**을 클릭합니다. 

11. 이제 [다음 자습서](apache-spark-use-bi-tools.md)를 시작하는 경우 Notebook을 열어 둡니다. 그렇지 않은 경우 Notebook을 종료하여 Notebook의 **파일** 메뉴에서 클러스터 리소스를 해제하고 **닫기 및 중지**를 클릭합니다.

## <a name="next-step"></a>다음 단계

이 문서에서는 Jupyter 노트북을 사용하여 Spark에서 대화형 쿼리를 실행하는 방법을 알아보았습니다. 다음 문서로 진행하여 Spark에 등록된 데이터를 Power BI 및 Tableau와 같은 BI 분석 도구로 가져오는 방법을 확인하세요. 

> [!div class="nextstepaction"]
>[Azure HDInsight와 함께 데이터 시각화 도구를 사용하는 Spark BI](apache-spark-use-bi-tools.md)




