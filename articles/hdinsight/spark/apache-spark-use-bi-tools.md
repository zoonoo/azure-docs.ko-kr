---
title: "Azure HDInsight에서 데이터 시각화 도구를 사용하는 Spark BI | Microsoft Docs"
description: "HDInsight 클러스터에서 Apache Spark BI를 사용하는 분석에 대해 데이터 시각화 도구 사용"
keywords: "apache spark bi,spark bi, spark 데이터 시각화, spark 비즈니스 인텔리전스"
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 1448b536-9bc8-46bc-bbc6-d7001623642a
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2017
ms.author: nitinme
ms.openlocfilehash: 3886923639be8a7bd8167f10db503d7ebf8c1657
ms.sourcegitcommit: 6a6e14fdd9388333d3ededc02b1fb2fb3f8d56e5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/07/2017
---
# <a name="apache-spark-bi-using-data-visualization-tools-with-azure-hdinsight"></a>Azure HDInsight와 함께 데이터 시각화 도구를 사용하는 Apache Spark BI

Power BI 및 Tableau를 사용하여 Azure HDInsight의 Apache Spark 클러스터에서 데이터를 시각화하는 방법을 알아봅니다.

## <a name="prerequisites"></a>필수 조건

* HDInsight의 Apache Spark 클러스터입니다. 자세한 내용은 [Azure HDInsight에서 Apache Spark 클러스터 만들기](apache-spark-jupyter-spark-sql.md)를 참조하세요.
* 클러스터의 샘플 데이터입니다. 자세한 내용은 [HDInsight Spark 클러스터에서 대화형 쿼리 실행](apache-spark-load-data-run-query.md)을 참조하세요.
* Power BI: [Power BI Desktop](https://powerbi.microsoft.com/en-us/desktop/) 및 [Power BI 평가판 구독](https://app.powerbi.com/signupredirect?pbi_source=web)(선택 사항)
* Tableau: [Tableau Desktop](http://www.tableau.com/products/desktop) 및 [Microsoft Spark ODBC 드라이버](http://go.microsoft.com/fwlink/?LinkId=616229)


## <a name="hivetable"></a>샘플 데이터 검토

[이전 자습서](apache-spark-load-data-run-query.md)에서 만든 Jupyter 노트북은 `hvac` 테이블을 만드는 코드를 포함합니다. 이 테이블은 **\HdiSamples\HdiSamples\SensorSampleData\hvac\hvac.csv** 모든 HDInsight Spark 클러스터에서 사용 가능한 CSV 파일을 기반으로 합니다. 시각화를 만들기 전에 Spark 클러스터의 데이터를 검토해 보겠습니다.

1. 예상되는 테이블이 존재하는지 확인합니다. Notebook의 빈 셀에서 다음 코드 조각을 복사하고 **Shift + Enter**를 누릅니다.

        %%sql
        SHOW TABLES

    아래와 같은 출력이 표시됩니다.

    ![Spark에 테이블 표시](./media/apache-spark-use-bi-tools/show-tables.png)

    이 자습서를 시작하기 전에 Notebook을 닫은 경우 `hvactemptable`이 정리되므로 출력에 포함되지 않습니다.
    metastore에 저장된 하이브 테이블만(**isTemporary** 열 아래에서 **False**로 표시됨) BI 도구에서 액세스할 수 있습니다. 이 자습서에서는 만든 **hvac** 테이블에 연결합니다.

2. 테이블에 예상되는 데이터가 포함되는지 확인합니다. Notebook의 빈 셀에서 다음 코드 조각을 복사하고 **Shift + Enter**를 누릅니다.

        %%sql
        SELECT * FROM hvac LIMIT 10

    아래와 같은 출력이 표시됩니다.

    ![Spark의 hvac 테이블에서 행 표시](./media/apache-spark-use-bi-tools/select-limit.png)

3. Notebook을 종료하여 리소스를 해제합니다. 이렇게 하기 위해 Notebook의 **파일** 메뉴에서 **닫기 및 중지**를 클릭합니다.

## <a name="powerbi"></a>Spark 데이터 시각화에 대해 Power BI 사용

예상되는 데이터가 존재하는지 확인했으므로 Power BI를 사용하여 이 데이터에서 시각화, 보고서 및 대시보드를 만들 수 있습니다. 이 문서에서는 정적 데이터와 함께 간단한 예제를 보여 주지만 더 복잡한 스트리밍 예제를 보려는 경우 의견에서 알려주세요.

### <a name="create-a-report-in-power-bi-desktop"></a>Power BI Desktop에서 보고서 만들기
Spark를 사용하는 첫 번째 단계는 Power BI Desktop에서 클러스터에 연결하고, 클러스터에서 데이터를 로드하고, 해당 데이터를 기반으로 기본 시각화를 만드는 것입니다.

> [!NOTE]
> 이 문서에서 설명하는 커넥터는 현재 미리 보기 상태이지만 사용을 권장하며 [Power BI 커뮤니티](https://community.powerbi.com/) 사이트 또는 [Power BI Ideas](https://ideas.powerbi.com/forums/265200-power-bi-ideas)를 통해 피드백을 제공하기를 원합니다.

1. Power BI Desktop의 **홈** 탭에서 **데이터 가져오기**, **자세히**를 차례로 클릭합니다.

2. `Spark`를 검색하고, **Azure HDInsight Spark**를 선택한 다음 **연결**을 클릭합니다.

    ![Apache Spark BI에서 Power BI로 데이터 가져오기](./media/apache-spark-use-bi-tools/apache-spark-bi-import-data-power-bi.png "Apache Spark BI에서 Power BI로 데이터 가져오기")

3. 클러스터 URL을 입력하고(`mysparkcluster.azurehdinsight.net` 양식으로), **DirectQuery**를 선택한 다음 **확인**을 클릭합니다.

    ![Apache Spark BI에 연결](./media/apache-spark-use-bi-tools/connect-to-apache-spark-bi.png "Apache Spark BI에 연결")

    > [!NOTE]
    > Spark와 함께 연결 모드 중 하나를 사용할 수 있습니다. DirectQuery를 사용하는 경우 변경 내용은 전체 데이터 집합을 새로 고치지 않고 보고서에 반영됩니다. 데이터를 가져오는 경우 변경 내용을 보려면 데이터 집합을 새로 고쳐야 합니다. DirectQuery를 사용하는 방법 및 경우에 대한 자세한 내용은 [Power BI에서 DirectQuery 사용](https://powerbi.microsoft.com/documentation/powerbi-desktop-directquery-about/)을 참조하세요. 

4. **사용자 이름** 및 **암호**에 대한 HDInsight 로그인 계정 정보를 입력한 다음(기본 계정은 `admin`) **연결**을 클릭합니다.

    ![Spark 클러스터 사용자 이름 및 암호](./media/apache-spark-use-bi-tools/user-password.png "Spark 클러스터 사용자 이름 및 암호")

5. `hvac` 테이블을 선택하고 데이터의 미리 보기를 확인하기 위해 기다립니다. 그런 다음 **로드**를 클릭합니다.

    ![Spark 클러스터 사용자 이름 및 암호](./media/apache-spark-use-bi-tools/apache-spark-bi-select-table.png "Spark 클러스터 사용자 이름 및 암호")

    이제 Power BI Desktop에 Spark 클러스터에 연결하고 `hvac` 테이블에서 데이터를 로드하는 데 필요한 모든 정보가 있습니다. 테이블 및 해당 열이 **필드** 창에 표시됩니다.

    ![Apache Spark BI 대시보드에 테이블 나열](./media/apache-spark-use-bi-tools/apache-spark-bi-display-tables.png "Apache Spark BI 대시보드에 테이블 나열")

7. 각 건물에 대한 대상 온도와 실제 온도 간의 차이를 보여 주는 시각화를 빌드합니다. 

    1. **시각화** 창에서 **영역형 차트**를 선택합니다. **BuildingID** 필드를 **축**으로 끌어 놓고, **ActualTemp** 및 **TargetTemp** 필드를 **값**으로 끌어 놓습니다.

        ![Apache Spark BI를 사용하여 Spark 데이터 시각화 만들기](./media/apache-spark-use-bi-tools/apache-spark-bi-add-value-columns.png "Apache Spark BI를 사용하여 Spark 데이터 시각화 만들기")

    2. 기본적으로 시각화에서는 **ActualTemp** 및 **TargetTemp**의 합계를 보여 줍니다. 두 필드 모두에 대해 드롭다운 메뉴에서 **평균**을 선택하여 각 건물에 대한 실제 및 대상 온도의 평균을 얻습니다.

        ![Apache Spark BI를 사용하여 Spark 데이터 시각화 만들기](./media/apache-spark-use-bi-tools/apache-spark-bi-average-of-values.png "Apache Spark BI를 사용하여 Spark 데이터 시각화 만들기")

    3. 데이터 시각화는 스크린샷의 데이터 시각화와 비슷해야 합니다. 커서를 시각화 위로 이동하면 관련 데이터와 함께 도구 설명이 나타납니다.

        ![Apache Spark BI를 사용하여 Spark 데이터 시각화 만들기](./media/apache-spark-use-bi-tools/apache-spark-bi-area-graph.png "Apache Spark BI를 사용하여 Spark 데이터 시각화 만들기")

11. **파일**, **저장**을 차례로 클릭하고, 파일에 대한 이름 `spark.pbix`를 입력합니다. 

### <a name="publish-the-report-to-the-power-bi-service-optional"></a>Power BI 서비스에 보고서 게시(선택 사항)
이제 Power BI Desktop에 모든 기능을 갖춘 보고서가 있고 거기에서 중지할 수 있지만 많은 사람들은 조직 전체에서 보고서 및 대시보드를 쉽게 공유할 수 있도록 하는 Power BI 서비스를 활용하기를 원합니다. 

이 섹션에서는 만든 Power BI Desktop 파일에 포함된 데이터 집합 및 보고서를 게시합니다. 그런 다음 보고서에서 대시보드로 시각화를 고정합니다. 대시보드는 보고서에 있는 데이터 하위 집합에 집중하는 데 일반적으로 사용되며 보고서에 하나의 시각화만이 있지만 단계를 수행하는 데 여전히 유용합니다.

1. Power BI Desktop의 **홈** 탭에서 **게시**를 클릭합니다.

    ![Power BI Desktop에서 게시](./media/apache-spark-use-bi-tools/apache-spark-bi-publish.png "Power BI Desktop에서 게시")

2. 데이터 집합 및 보고서를 게시하려는 작업 영역을 선택한 다음 **선택**을 클릭합니다. 다음 이미지에서 기본 **내 작업 영역**이 선택됩니다.

    ![데이터 집합 및 보고서를 게시하려는 작업 영역 선택](./media/apache-spark-use-bi-tools/apache-spark-bi-select-workspace.png "데이터 집합 및 보고서를 게시하려는 작업 영역 선택") 

3. Power BI Desktop이 성공 메시지와 함께 반환된 후 **Power BI에서 'spark.pbix' 열기**를 클릭합니다.

    ![게시 성공, 클릭하여 자격 증명 입력](./media/apache-spark-use-bi-tools/apache-spark-bi-publish-success.png "게시 성공, 클릭하여 자격 증명 입력") 

4. Power BI 서비스에서 **자격 증명 입력**을 클릭합니다.

    ![Power BI 서비스에서 자격 증명 입력](./media/apache-spark-use-bi-tools/apache-spark-bi-enter-credentials.png "Power BI 서비스에서 자격 증명 입력")

5. **자격 증명 편집**을 클릭합니다.

    ![Power BI 서비스에서 자격 증명 편집](./media/apache-spark-use-bi-tools/apache-spark-bi-edit-credentials.png "Power BI 서비스에서 자격 증명 편집")

6. HDInsight 로그인 계정 정보를 입력한 다음(일반적으로 Power BI Desktop에서 사용한 기본 `admin` 계정) **로그인**을 클릭합니다.

    ![Spark 클러스터에 로그인](./media/apache-spark-use-bi-tools/apache-spark-bi-sign-in.png "Spark 클러스터에 로그인")

7. 왼쪽 창에서 **작업 영역** > **내 작업 영역** > **보고서**로 이동한 다음 **spark**를 클릭합니다.

    ![왼쪽 창의 보고서 아래에 나열된 보고서](./media/apache-spark-use-bi-tools/apache-spark-bi-service-left-pane.png "왼쪽 창의 보고서 아래에 나열된 보고서")

    왼쪽 창의 **데이터 집합** 아래에 나열된 **spark**도 표시됩니다.

8. 이제 Power BI Desktop에서 만든 시각적 개체를 서비스에서 사용할 수 있습니다. 이 시각적 개체를 대시보드에 고정하려면 시각적 개체를 마우스로 가리키고 고정 아이콘을 클릭합니다.

    ![Power BI 서비스의 보고서](./media/apache-spark-use-bi-tools/apache-spark-bi-service-report.png "Power BI 서비스의 보고서")

9. "새 대시보드"를 선택하고, 이름 `SparkDemo`를 입력한 다음 **고정**을 클릭합니다.

    ![새 대시보드에 고정](./media/apache-spark-use-bi-tools/apache-spark-bi-pin-dashboard.png "새 대시보드에 고정")

10. 보고서에서 **대시보드로 이동**을 클릭합니다. 

    ![대시보드로 이동](./media/apache-spark-use-bi-tools/apache-spark-bi-open-dashboard.png "대시보드로 이동")

시각적 개체가 대시보드에 고정됩니다. 다른 시각적 개체를 보고서에 추가하고 동일한 대시보드에 고정할 수 있습니다. 보고서 및 대시보드에 대한 자세한 내용은 [Power BI의 보고서](https://powerbi.microsoft.com/documentation/powerbi-service-reports/)및 [Power BI의 대시보드](https://powerbi.microsoft.com/documentation/powerbi-service-dashboards/)를 참조하세요.

## <a name="tableau"></a>Spark 데이터 시각화에 대해 Tableau Desktop 사용

> [!NOTE]
> 이 섹션은 Azure HDInsight에서 만든 Spark 1.5.2 클러스터에만 적용할 수 있습니다.
>
>

1. 이 Apache Spark BI 자습서를 실행 중인 컴퓨터에 [Tableau Desktop](http://www.tableau.com/products/desktop)을 설치합니다.

2. 또한 Microsoft Spark ODBC 드라이버가 컴퓨터에 설치되어 있는지 확인합니다. [여기](http://go.microsoft.com/fwlink/?LinkId=616229)에서 드라이버를 설치할 수 있습니다.

1. Tableau Desktop을 실행합니다. 왼쪽 창의 연결할 서버 목록에서 **Spark SQL**을 클릭합니다. 왼쪽 창에 Spark SQL이 기본적으로 표시되지 않으면 **추가 서버**를 클릭하여 찾을 수 있습니다.
2. Spark SQL 연결 대화 상자에서 스크린샷과 같은 값을 제공한 다음 **확인**을 클릭합니다.

    ![Apache Spark BI용 클러스터에 연결](./media/apache-spark-use-bi-tools/connect-to-tableau-apache-spark-bi.png "Apache Spark BI용 클러스터에 연결")

    컴퓨터에 **Microsoft Spark ODBC 드라이버** 를 설치한 경우에만 인증 드롭다운 목록에 [Microsoft Azure HDInsight Service](http://go.microsoft.com/fwlink/?LinkId=616229) 가 옵션으로 표시됩니다.
3. 다음 화면의 **스키마** 드롭다운에서 **찾기** 아이콘, **기본값**을 차례로 클릭합니다.

    ![Apache Spark BI용 스키마 찾기](./media/apache-spark-use-bi-tools/tableau-find-schema-apache-spark-bi.png "Apache Spark BI용 스키마 찾기")
4. **테이블** 필드에서 **찾기** 아이콘을 다시 클릭하면 클러스터에 사용 가능한 Hive 테이블이 모두 나열됩니다. 이전에 Notebook을 사용하여 만든 **hvac** 테이블이 표시됩니다.

    ![Apache Spark BI용 테이블 찾기](./media/apache-spark-use-bi-tools/tableau-find-table-apache-spark-bi.png "Apache Spark BI용 테이블 찾기")
5. 테이블을 오른쪽 상단에 있는 상자에 끌어다 놓습니다. Tableau에서 데이터를 가져오고 빨간색 상자로 강조하여 스키마를 표시합니다.

    ![Apache Spark BI용 Tableau에 테이블 추가](./media/apache-spark-use-bi-tools/tableau-add-table-apache-spark-bi.png "Apache Spark BI용 Tableau에 테이블 추가")
6. 왼쪽 아래에서 **Sheet1** 탭을 클릭합니다. 날짜별로 모든 건물에 대한 대상 및 실제 온도 평균을 보여 주는 시각화를 만듭니다. **날짜** 및 **건물 ID**를 **열**로, **실제 온도**/**대상 온도**를 **행**으로 끌어갑니다. **표시** 아래에서 **영역**을 선택하여 Spark 데이터 시각화에 대한 영역 맵을 사용합니다.

     ![Spark 데이터 시각화를 위한 필드 추가](./media/apache-spark-use-bi-tools/spark-data-visualization-add-fields.png "Spark 데이터 시각화를 위한 필드 추가")
7. 기본적으로 온도 필드가 집계로 표시됩니다. 대신, 평균 온도를 표시하려면 아래 표시된 것과 같이 드롭다운에서 이 작업을 하면 됩니다.

    ![Spark 데이터 시각화에 대한 온도 평균 구하기](./media/apache-spark-use-bi-tools/spark-data-visualization-average-temperature.png "Spark 데이터 시각화에 대한 온도 평균 구하기")

8. 또한 한 온도 맵을 다른 온도 맵 위에 겹쳐 놓아 대상 및 실제 온도 간의 차이를 보다 잘 파악할 수도 있습니다. 마우스를 하단 영역 맵의 모서리로 이동하면 빨간색 원으로 강조 표시된 핸들 모양이 나타납니다. 맵을 위쪽의 다른 맵으로 끌어온 후 빨간색 사각형으로 강조 표시된 모양이 표시되면 마우스를 놓습니다.

    ![Spark 데이터 시각화를 위한 맵 병합](./media/apache-spark-use-bi-tools/spark-data-visualization-merge-maps.png "Spark 데이터 시각화를 위한 맵 병합")

     스크린샷과 같이 데이터 시각화가 변경되어야 합니다.

    ![Spark 데이터 시각화에 대한 Tableau 출력](./media/apache-spark-use-bi-tools/spark-data-visualization-tableau-output.png "Spark 데이터 시각화에 대한 Tableau 출력")
9. **저장** 을 클릭하여 워크시트를 저장합니다. 대시보드를 만들고 시트를 하나 이상 추가할 수도 있습니다.

## <a name="next-steps"></a>다음 단계

지금까지 클러스터를 만들고 Spark 데이터 프레임을 만들어 데이터를 쿼리한 다음 BI 도구에서 해당 데이터에 액세스하는 방법을 배웠습니다. 이제 클러스터 리소스를 관리하고 HDInsight Spark 클러스터에서 실행 중인 작업을 디버그하는 방법에 대한 지침을 살펴볼 수 있습니다.

* [Azure HDInsight에서 Apache Spark 클러스터에 대한 리소스 관리](apache-spark-resource-manager.md)
* [HDInsight의 Apache Spark 클러스터에서 실행되는 작업 추적 및 디버그](apache-spark-job-debugging.md)

