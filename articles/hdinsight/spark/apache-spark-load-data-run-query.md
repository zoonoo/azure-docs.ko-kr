---
title: "Azure HDInsight Spark 클러스터에서 대화형 쿼리 실행 | Microsoft Docs"
description: "HDInsight에서 Apache Spark 클러스터를 만드는 방법에 대한 HDInsight Spark 빠른 시작입니다."
keywords: "spark 빠른 시작, 대화형 spark, 대화형 쿼리, hdinsight spark, azure spark"
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2017
ms.author: nitinme
ms.openlocfilehash: 0d93e261121f11d2a1082b9672e6d979955d3bee
ms.sourcegitcommit: 6a6e14fdd9388333d3ededc02b1fb2fb3f8d56e5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/07/2017
---
# <a name="run-interactive-queries-on-an-hdinsight-spark-cluster"></a>HDInsight Spark 클러스터에서 대화형 쿼리 실행

이 문서에서는 Jupyter 노트북을 사용하여 Spark 클러스터에 대해 대화형 Spark SQL 쿼리를 실행합니다. Jupyter 노트북은 콘솔 기반 대화형 환경을 웹으로 확장하는 브라우저 기반 응용 프로그램입니다. 자세한 내용은 [The Jupyter notebook](http://jupyter-notebook.readthedocs.io/en/latest/notebook.html)(Jupyter 노트북)을 참조하세요.

이 자습서에서는 Jupyter 노트북에서 **PySpark** 커널을 사용하여 대화형 Spark SQL 쿼리를 실행합니다. HDInsight 클러스터의 Jupyter 노트북은 **PySpark3** 및 **Spark**의 두 가지 다른 커널도 지원합니다. 커널 및 **PySpark** 사용의 이점에 대한 자세한 내용은 [HDInsight에서 Apache Spark 클러스터와 함께 Jupyter Notebook 커널 사용](apache-spark-jupyter-notebook-kernels.md)을 참조하세요.

## <a name="prerequisites"></a>필수 조건

* **Azure HDInsight Spark 클러스터**. 자세한 내용은 [Azure HDInsight에서 Apache Spark 클러스터 만들기](apache-spark-jupyter-spark-sql.md)를 참조하세요.

## <a name="create-a-jupyter-notebook-to-run-interactive-queries"></a>대화형 쿼리를 실행할 Jupyter 노트북 만들기

쿼리를 실행하기 위해 여기서는 클러스터와 연결된 저장소에서 기본적으로 제공되는 예제 데이터를 사용합니다. 그러나 먼저 데이터를 Spark에 데이터 프레임으로 로드해야 합니다. 데이터 프레임이 있으면 Jupyter 노트북을 사용하여 이 프레임에 대한 쿼리를 실행할 수 있습니다. 이 문서에서는 다음 방법을 살펴봅니다.

* 예제 데이터 집합을 Spark 데이터 프레임으로 등록
* 데이터 프레임에 대한 쿼리 실행

이제 시작하겠습니다.

1. [Azure 포털](https://portal.azure.com/)을 엽니다. 클러스터를 대시보드에 고정하도록 선택한 경우 대시보드에서 클러스터 타일을 클릭하여 클러스터 블레이드를 시작합니다.

    클러스터를 대시보드에 고정하지 않은 경우 왼쪽 창에서 **HDInsight 클러스터**를 클릭한 후 만든 클러스터를 클릭합니다.

3. **빠른 링크**에서 **클러스터 대시보드**를 클릭한 다음 **Jupyter Notebook**을 클릭합니다. 메시지가 표시되면 클러스터에 대한 관리자 자격 증명을 입력합니다.

   ![Jupyter 노트북을 열어 대화형 Spark SQL 쿼리 실행](./media/apache-spark-load-data-run-query/hdinsight-spark-start-jupyter-interactive-spark-sql-query.png "Jupyter 노트북을 열어 대화형 Spark SQL 쿼리 실행")

   > [!NOTE]
   > 또한 브라우저에서 다음 URL을 열어 클러스터에 대한 Jupyter 노트북에 액세스할 수 있습니다. **CLUSTERNAME** 을 클러스터의 이름으로 바꿉니다.
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`
   >
   >
3. Notebook을 만듭니다. **새로 만들기**를 클릭한 다음 **PySpark**를 클릭합니다.

   ![Jupyter 노트북을 만들어 대화형 Spark SQL 쿼리 실행](./media/apache-spark-load-data-run-query/hdinsight-spark-create-jupyter-interactive-Spark-SQL-query.png "Jupyter 노트북을 만들어 대화형 Spark SQL 쿼리 실행")

   새 노트북이 만들어지고 Untitled(Untitled.pynb) 이름으로 열립니다.

4. 맨 위에서 노트북 이름을 클릭하고 원하는 경우 식별하기 쉬운 이름을 입력합니다.

    ![Jupter 노트북에 대한 이름을 제공하여 대화형 Spark 쿼리 실행](./media/apache-spark-load-data-run-query/hdinsight-spark-jupyter-notebook-name.png "Jupter 노트북에 대한 이름을 제공하여 대화형 Spark 쿼리 실행")

5. 빈 셀에 다음 코드를 붙여 넣은 다음 **SHIFT + ENTER**를 눌러 코드를 실행합니다. 코드는 이 시나리오에 필요한 형식을 가져옵니다.

        from pyspark.sql import *
        from pyspark.sql.types import *

    PySpark 커널을 사용하여 노트북을 만들었기 때문에 컨텍스트를 명시적으로 만들 필요가 없습니다. 첫 번째 코드 셀을 실행하면 Spark 및 Hive 컨텍스트가 자동으로 만들어집니다.

    ![대화형 Spark SQL 쿼리 상태](./media/apache-spark-load-data-run-query/hdinsight-spark-interactive-spark-query-status.png "대화형 Spark SQL 쿼리 상태")

    Jupyter에서 대화형 쿼리를 실행할 때마다, 웹 브라우저 창 제목에 노트북 제목과 함께 **(사용 중)** 상태가 표시됩니다. 또한 오른쪽 위 모서리에 있는 **PySpark** 텍스트 옆에 단색 원이 표시됩니다. 작업이 완료되면 속이 빈 원으로 변경됩니다.

6. Spark 클러스터로 데이터를 로드하기 전에 데이터의 스냅숏을 살펴보겠습니다. 이 자습서에서 사용하는 예제 데이터는 모든 HDInsight Spark 클러스터에서 **\HdiSamples\HdiSamples\SensorSampleData\hvac\hvac.csv**에 CSV 파일로 제공됩니다. 이 데이터는 건물의 온도 변화를 캡처합니다. 다음은 데이터의 첫 행입니다.

    ![대화형 Spark SQL 쿼리용 데이터의 스냅숏](./media/apache-spark-load-data-run-query/hdinsight-spark-sample-data-interactive-spark-sql-query.png "대화형 Spark SQL 쿼리용 데이터의 스냅숏")

6. 다음 코드를 실행하여 데이터 프레임 및 임시 테이블(**hvac**)을 만듭니다. 이 자습서의 경우 CSV 파일에서 사용할 수 있는 모든 열을 만들지 않습니다. 

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

7. 테이블을 만들었으면 다음 코드를 사용하여 데이터에 대한 대화형 쿼리를 실행합니다.

        %%sql
        SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"

   PySpark 커널을 사용하기 때문에 이제 `%%sql` 매직을 사용하여 만든 임시 테이블 **hvac**에서 대화형 SQL 쿼리를 직접 실행할 수 있습니다. `%%sql` 매직 및 기타 PySpark 커널에서 사용 가능한 매직에 대한 자세한 내용은 [Spark HDInsight 클러스터와 함께 Jupyter Notebook에서 사용 가능한 커널](apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic)을 참조하세요.

   다음과 같은 테이블 형식 출력이 기본적으로 표시됩니다.

     ![대화형 Spark 쿼리 결과의 테이블 출력](./media/apache-spark-load-data-run-query/hdinsight-interactive-spark-query-result.png "대화형 Spark 쿼리 결과의 테이블 출력")

9. 다른 시각화로 결과를 볼 수도 있습니다. 동일한 출력에 대한 영역형 그래프를 보려면 **영역**을 선택한 다음 표시된 것처럼 다른 값을 설정합니다.

    ![대화형 Spark 쿼리 결과의 영역 그래프](./media/apache-spark-load-data-run-query/hdinsight-interactive-spark-query-result-area-chart.png "대화형 Spark 쿼리 결과의 영역 그래프")

10. Notebook의 **파일** 메뉴에서 **닫기 및 검사점**을 클릭합니다. 

11. 이제 [다음 자습서](apache-spark-use-bi-tools.md)를 시작하는 경우 Notebook을 열어 둡니다. 그렇지 않은 경우 Notebook을 종료하여 Notebook의 **파일** 메뉴에서 클러스터 리소스를 해제하고 **닫기 및 중지**를 클릭합니다.

## <a name="next-step"></a>다음 단계

이 문서에서는 Jupyter 노트북을 사용하여 Spark에서 대화형 쿼리를 실행하는 방법을 알아보았습니다. 다음 문서로 진행하여 Spark에 등록된 데이터를 Power BI 및 Tableau와 같은 BI 분석 도구로 가져오는 방법을 확인하세요. 

> [!div class="nextstepaction"]
>[Azure HDInsight와 함께 데이터 시각화 도구를 사용하는 Spark BI](apache-spark-use-bi-tools.md)




