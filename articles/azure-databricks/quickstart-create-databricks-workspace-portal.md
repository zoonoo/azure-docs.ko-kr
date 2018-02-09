---
title: "빠른 시작: Azure Portal을 사용하여 Azure Databricks에서 Spark 작업 실행 | Microsoft Docs"
description: "빠른 시작은 Azure Portal을 사용하여 Azure Databricks 작업 영역, Apache Spark 클러스터를 만들고 Spark 작업을 실행하는 방법을 보여줍니다."
services: azure-databricks
documentationcenter: 
author: nitinme
manager: cgronlun
editor: cgronlun
ms.service: azure-databricks
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 01/22/2018
ms.author: nitinme
ms.custom: mvc
ms.openlocfilehash: 8a91e92e0e535b047ddfcfeff53de72d769f05c7
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/01/2018
---
# <a name="quickstart-run-a-spark-job-on-azure-databricks-using-the-azure-portal"></a>빠른 시작: Azure Portal을 사용하여 Azure Databricks에서 Spark 작업 실행

빠른 시작은 Azure Databricks 작업 영역과 해당 작업 영역 내에 Apache Spark 클러스터를 만드는 방법을 보여줍니다. 마지막으로, Databricks 클러스터에서 Spark 작업을 실행하는 방법을 알아봅니다. Azure Databricks에 대한 자세한 내용은 [Azure Databricks란?](what-is-azure-databricks.md)을 참조하세요.

## <a name="log-in-to-the-azure-portal"></a>Azure Portal에 로그인

[Azure Portal](https://portal.azure.com)에 로그인합니다.

## <a name="create-a-databricks-workspace"></a>Databricks 작업 영역 만들기

이 섹션에서는 Azure Portal을 사용하여 Azure Databricks 작업 영역을 만듭니다. 

1. Azure Portal에서 **+**를 클릭하고, **데이터 + 분석**을 클릭한 다음, **Azure Databricks(미리 보기)**를 클릭합니다. 

    ![Azure Portal의 Databricks](./media/quickstart-create-databricks-workspace-portal/azure-databricks-on-portal.png "Azure Portal의 Databricks")

2. **Azure Databricks(미리 보기)** 아래에 있는 **만들기**를 클릭합니다.

3. **Azure Databricks 서비스**아래에 다음 값을 제공합니다.

    ![Azure Databricks 작업 영역 만들기](./media/quickstart-create-databricks-workspace-portal/create-databricks-workspace.png "Azure Databricks 작업 영역 만들기")

    * **작업 영역 이름**에는 Databricks 작업 영역의 이름을 제공합니다.
    * **구독**의 경우, 드롭다운에서 Azure 구독을 선택합니다.
    * **리소스 그룹**에서 새 리소스 그룹을 만들지 아니면 기존 집합을 사용할지 여부를 지정합니다. 리소스 그룹은 Azure 솔루션에 관련된 리소스를 보유하는 컨테이너입니다. 자세한 내용은 [Azure Resource Manager 개요](../azure-resource-manager/resource-group-overview.md)를 참조하세요.
    * **위치**에서는 **미국 동부 2**를 선택합니다. 사용 가능한 다른 영역은 [지역별 사용 가능한 Azure 서비스](https://azure.microsoft.com/regions/services/)를 참조하세요.

4. **만들기**를 클릭합니다.

## <a name="create-a-spark-cluster-in-databricks"></a>Databricks에서 Spark 클러스터 만들기

1. Azure Portal에서 사용자가 만든 Databricks 작업 영역으로 이동한 다음 **작업 영역 초기화**를 클릭합니다.

2. Azure Databricks 포털로 리디렉션됩니다. 포털에서 **클러스터**를 클릭합니다.

    ![Azure의 Databricks](./media/quickstart-create-databricks-workspace-portal/databricks-on-azure.png "Azure의 Databricks")

3. **새 클러스터** 페이지에서 값을 제공하여 클러스터를 만듭니다.

    ![Azure에서 Databricks Spark 클러스터 만들기](./media/quickstart-create-databricks-workspace-portal/create-databricks-spark-cluster.png "Azure에서 Databricks Spark 클러스터 만들기")

    * 클러스터의 이름을 입력합니다.
    * **___ 분 후 작업 종료** 확인란을 선택했는지 확인합니다. 클러스터를 사용하지 않는 경우 클러스터를 종료하는 기간(분)을 제공합니다.
    * 다른 모든 기본값을 적용합니다. 
    * **클러스터 만들기**를 클릭합니다. 클러스터가 실행되면 노트북을 클러스터에 첨부하고 Spark 작업을 실행할 수 있습니다.

클러스터를 만드는 방법에 대한 자세한 내용은 [Azure Databricks에서 Spark 클러스터 만들기](https://docs.azuredatabricks.net/user-guide/clusters/create.html)를 참조하세요.

## <a name="run-a-spark-sql-job"></a>Spark SQL 작업 실행

이 섹션을 시작하기 전에 다음을 완료해야 합니다.

* [Azure Storage 계정 만들기](../storage/common/storage-create-storage-account.md#create-a-storage-account) 
* [Github에서](https://github.com/Azure/usql/blob/master/Examples/Samples/Data/json/radiowebsite/small_radio_json.json) 샘플 JSON 파일을 다운로드합니다. 
* 사용자가 만든 Azure 저장소 계정에 샘플 JSON 파일을 업로드합니다. [Microsoft Azure Storage 탐색기](../vs-azure-tools-storage-manage-with-storage-explorer.md)를 사용하여 파일을 업로드할 수 있습니다.

다음 단계를 수행하여 Databricks에서 노트북을 만들고, Azure Blob Storage 계정에서 데이터를 읽는 노트북을 구성한 다음 데이터에 대해 Spark SQL 작업을 실행합니다.

1. 왼쪽 창에서 **작업 영역**을 클릭합니다. **작업 영역** 드롭 다운에서 **만들기**를 클릭한 다음, **Notebook**을 클릭합니다.

    ![Databricks에서 노트북 만들기](./media/quickstart-create-databricks-workspace-portal/databricks-create-notebook.png "Databricks에서 노트북 만들기")

2. **Notebook 만들기** 대화 상자에서 이름을 입력하고, 언어로 **Scala**를 선택한 다음, 이전에 만든 Spark 클러스터를 선택합니다.

    ![Databricks에서 노트북 만들기](./media/quickstart-create-databricks-workspace-portal/databricks-notebook-details.png "Databricks에서 노트북 만들기")

    **만들기**를 클릭합니다.

3. 다음 코드 조각에서 `{YOUR STORAGE ACCOUNT NAME}`를 사용자가 만든 Azure 저장소 계정 이름으로, `{YOUR STORAGE ACCOUNT ACCESS KEY}`를 저장소 계정 액세스 키로 대체합니다. 노트북의 빈 셀에 코드 조각을 붙여넣은 다음 SHIFT + ENTER를 눌러 코드 셀을 실행합니다. 이 코드 조각은 Azure Blob Storage에서 데이터를 읽는 노트북을 구성합니다.

       spark.conf.set("fs.azure.account.key.{YOUR STORAGE ACCOUNT NAME}.blob.core.windows.net", "{YOUR STORAGE ACCOUNT ACCESS KEY}")
    
    저장소 계정 액세스 키를 검색하는 방법에 대한 자세한 내용은 [저장소 액세스 키 관리](../storage/common/storage-create-storage-account.md#manage-your-storage-account)를 참조하세요.

    > [!NOTE]
    > 또한 Azure Databricks에서 Azure Data Lake Store를 Spark 클러스터와 함께 사용할 수 있습니다. 자세한 내용은 [Azure Databricks에서 Data Lake Store 사용](https://go.microsoft.com/fwlink/?linkid=864084)을 참조하세요.

4. SQL 문을 실행하여 샘플 JSON 데이터 파일인 **small_radio_json.json**의 데이터를 사용하여 임시 테이블을 만듭니다. 다음 코드 조각에서 자리 표시자 값을 컨테이너 이름 및 저장소 계정 이름으로 대체합니다. 노트북의 코드 셀에 코드 조각을 붙여넣은 다음 SHIFT + ENTER를 누릅니다. 코드 조각에서 `path`는 Azure 저장소 계정에 업로드한 샘플 JSON 파일의 위치를 나타냅니다.

    ```sql
    %sql 
    CREATE TEMPORARY TABLE radio_sample_data
    USING json
    OPTIONS (
     path "wasbs://{YOUR CONTAINER NAME}@{YOUR STORAGE ACCOUNT NAME}.blob.core.windows.net/small_radio_json.json"
    )
    ```

    명령이 성공적으로 완료되면 JSON 파일의 모든 데이터가 Databricks 클러스터에 테이블로 저장됩니다.

    `%sql` 언어 magic 명령을 사용하면 노트북이 다른 유형인 경우에도 노트북에서 SQL 코드를 실행할 수 있습니다. 자세한 내용은 [노트북에서 언어 혼합](https://docs.azuredatabricks.net/user-guide/notebooks/index.html#mixing-languages-in-a-notebook)을 참조하세요.

5. 우리가 실행하는 쿼리를 더 잘 이해할 수 있도록 샘플 JSON 데이터의 스냅샷을 살펴보겠습니다. 코드 셀에 다음 코드 조각을 붙여넣은 다음 **SHIFT + ENTER**를 누릅니다.

    ```sql
    %sql 
    SELECT * from radio_sample_data
    ```

6. 다음 스크린샷과 같이 테이블 형식으로 출력됩니다(일부 열만 표시됨).

    ![샘플 JSON 데이터](./media/quickstart-create-databricks-workspace-portal/databricks-sample-csv-data.png "샘플 JSON 데이터")

    기타 세부 사항 중 샘플 데이터는 라디오 채널(열 이름, **성별**)의 청중의 성별과 구독이 무료 또는 유료(열 이름, **수준**)인지 여부를 캡처합니다.

7. 이제 이 데이터를 시각적으로 표현하여 각 성별, 무료 계정을 가진 사용자 수 및 유료 구독자 수를 보여줍니다. 테이블 형식 출력 하단에서 **가로 막대형 차트** 아이콘을 클릭한 다음 **플롯 옵션**을 클릭합니다.

    ![가로 막대형 차트 만들기](./media/quickstart-create-databricks-workspace-portal/create-plots-databricks-notebook.png "가로 막대형 차트 만들기")

8. **사용자 지정 플롯**에서 스크린샷에 표시된 것과 같이 값을 끌어서 놓습니다.

    ![가로 막대형 차트 사용자 지정](./media/quickstart-create-databricks-workspace-portal/databricks-notebook-customize-plot.png "가로 막대형 차트 사용자 지정")

    * **키**를 **성별**로 설정합니다.
    * **시계열 그룹화**를 **수준**으로 설정합니다.
    * **값**을 **수준**으로 설정합니다.
    * **집계**를 **COUNT**로 설정합니다.

    **Apply**를 클릭합니다.

9. 출력은 다음 스크린샷에 표시된 것처럼 시각적인 표시를 보여줍니다.

     ![가로 막대형 차트 사용자 지정](./media/quickstart-create-databricks-workspace-portal/databricks-sql-query-output-bar-chart.png "가로 막대형 차트 사용자 지정")

## <a name="clean-up-resources"></a>리소스 정리

Spark 클러스터를 생성하는 동안  **__분 후 작업 종료 확인란**을 선택한 경우 클러스터가 지정된 시간 동안 비활성 상태인 경우 클러스터가 자동으로 종료됩니다.

이 확인란을 선택하지 않으면 수동으로 클러스터를 종료해야 합니다. 이렇게 하려면 왼쪽 창의 Azure Databricks 작업 영역에서 **클러스터**를 클릭합니다. 종료하려는 클러스터에서 커서를 **작업** 열의 줄임표 위로 이동한 다음 **종료** 아이콘을 클릭합니다.

![Databricks 클러스터 종료](./media/quickstart-create-databricks-workspace-portal/terminate-databricks-cluster.png "Databricks 클러스터 종료")

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure Databricks에서 Spark 클러스터를 만들고 Azure 저장소의 데이터를 사용하여 Spark 작업을 실행했습니다. 또한 [Spark 데이터 소스](https://docs.azuredatabricks.net/spark/latest/data-sources/index.html)를 보고 다른 데이터 소스의 데이터를 Azure Databricks로 가져오는 방법을 배울 수 있습니다. Azure Data Lake Store를 Azure Databricks와 함께 사용하는 방법을 배우려면 다음 문서로 넘어가세요.

> [!div class="nextstepaction"]
>[Azure Databricks에 Data Lake Store 사용](https://go.microsoft.com/fwlink/?linkid=864084)
