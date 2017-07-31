---
title: "Azure HDInsight에서 Apache Spark 클러스터 만들기 | Microsoft Docs"
description: "HDInsight에서 Apache Spark 클러스터를 만드는 방법에 대한 HDInsight Spark 빠른 시작입니다."
keywords: "spark 빠른 시작, 대화형 spark, 대화형 쿼리, hdinsight spark, azure spark"
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 91f41e6a-d463-4eb4-83ef-7bbb1f4556cc
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/20/2017
ms.author: nitinme
ms.translationtype: HT
ms.sourcegitcommit: 2812039649f7d2fb0705220854e4d8d0a031d31e
ms.openlocfilehash: 0625984bf10588fe50a2632285f565eb79b66ab7
ms.contentlocale: ko-kr
ms.lasthandoff: 07/22/2017

---
# <a name="create-an-apache-spark-cluster-in-azure-hdinsight"></a>Azure HDInsight에서 Apache Spark 클러스터 만들기

이 문서에서는 Azure HDInsight에서 Apache Spark 클러스터를 만드는 방법에 대해 설명합니다.

   ![Azure HDInsight에서 Apache Spark 클러스터를 만드는 단계를 설명하는 빠른 시작 다이어그램](./media/hdinsight-apache-spark-jupyter-spark-sql/hdinsight-spark-quickstart-interactive-spark-query-flow.png "HDInsight에서 Apache Spark를 사용하여 Spark 빠른 시작. 다음을 보여 주는 단계: 클러스터 만들기, Spark 대화형 쿼리 실행")

## <a name="prerequisites"></a>필수 조건

* **Azure 구독** - 이 자습서를 시작하기 전에 Azure 구독이 있어야 합니다. [지금 무료 Azure 계정 만들기](https://azure.microsoft.com/free)를 참조하세요.

## <a name="create-hdinsight-spark-cluster"></a>HDInsight Spark 클러스터 만들기

이 섹션에서는 [Azure Resource Manager 템플릿](https://azure.microsoft.com/resources/templates/101-hdinsight-spark-linux/)을 사용하여 HDInsight Spark 클러스터를 만듭니다. 클러스터를 만드는 다른 방법은 [HDInsight 클러스터 만들기](hdinsight-hadoop-provision-linux-clusters.md)를 참조하세요.

1. Azure 포털에서 템플릿을 열려면 다음 이미지를 클릭합니다.         

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-spark-linux%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apache-spark-jupyter-spark-sql/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. 다음 값을 입력합니다.

    ![Azure Resource Manager 템플릿을 사용하여 HDInsight Spark 클러스터 만들기](./media/hdinsight-apache-spark-jupyter-spark-sql/create-spark-cluster-in-hdinsight-using-azure-resource-manager-template.png "Azure Resource Manager 템플릿을 사용하여 HDInsight에서 Spark 클러스터 만들기")

    * **구독**: 이 클러스터에 대해 Azure 구독을 선택합니다.
    * **리소스 그룹**: 리소스 그룹을 만들거나 기존 리소스 그룹을 선택합니다. 리소스 그룹은 프로젝트에 대한 Azure 리소스를 관리하는 데 사용됩니다.
    * **위치**: 리소스 그룹의 위치를 선택합니다. 템플릿에서는 기본 클러스터 저장소뿐만 아니라 클러스터를 만드는 데 이 위치를 사용합니다.
    * **ClusterName**: 만들려는 HDInsight 클러스터의 이름을 입력합니다.
    * **Spark 버전**: 클러스터에 설치할 버전을 **2.0**으로 선택합니다.
    * **클러스터 로그인 이름 및 암호**: 기본 로그인 이름은 admin입니다.
    * **SSH 사용자 이름 및 암호**.

   이러한 값을 기록해 둡니다.  이 정보는 자습서의 뒷부분에서 필요합니다.

3. **위에 명시된 사용 약관에 동의함**을 선택하고 **대시보드에 고정**을 선택한 다음 **구매**를 클릭합니다. 템플릿 배포에 배포 제출 중이라는 제목의 새 타일이 표시됩니다. 클러스터를 만드는 데 약 20분이 걸립니다.

HDInsight 클러스터를 만드는 데 문제가 발생하는 경우 이를 수행하기 위한 적절한 사용 권한이 없을 수 있습니다. 자세한 내용은 [액세스 제어 요구 사항](hdinsight-administer-use-portal-linux.md#create-clusters)을 참조하세요.

> [!NOTE]
> 이 문서에서는 [클러스터 저장소로 Azure 저장소 Blob](hdinsight-hadoop-use-blob-storage.md)을 사용하는 Spark 클러스터를 만듭니다. 기본 저장소로 [Azure Data Lake Store](hdinsight-hadoop-use-data-lake-store.md)를 사용하는 Spark 클러스터를 만들 수도 있습니다. 자세한 내용은 [Data Lake 저장소가 있는 HDInsight 클러스터 만들기](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md)를 참조하세요.
>
>

## <a name="run-a-hive-query-using-spark-sql"></a>Spark SQL을 사용하여 Hive 쿼리를 실행합니다.

HDInsight Spark 클러스터에 구성된 Jupyter Notebook을 사용하는 경우 Spark SQL을 사용하여 Hive 쿼리를 실행하는 데 사용할 수 있는 `sqlContext`를 미리 설정합니다. 이 섹션에서는 Jupyter Notebook을 시작한 다음 기본 Hive 쿼리를 실행하는 방법을 설명합니다.

1. [Azure 포털](https://portal.azure.com/)을 엽니다.

2. 클러스터를 대시보드에 고정하도록 선택한 경우 대시보드에서 클러스터 타일을 클릭하여 클러스터 블레이드를 시작합니다.

    클러스터를 대시보드에 고정하지 않은 경우 왼쪽 창에서 **HDInsight 클러스터**를 클릭한 후 만든 클러스터를 클릭합니다.

3. **빠른 링크**에서 **클러스터 대시보드**를 클릭한 다음 **Jupyter Notebook**을 클릭합니다. 메시지가 표시되면 클러스터에 대한 관리자 자격 증명을 입력합니다.

   ![Jupyter 노트북을 열어 대화형 Spark SQL 쿼리 실행](./media/hdinsight-apache-spark-jupyter-spark-sql/hdinsight-spark-open-jupyter-interactive-spark-sql-query.png "Jupyter 노트북을 열어 대화형 Spark SQL 쿼리 실행")

   > [!NOTE]
   > 또한 브라우저에서 다음 URL을 열어 클러스터에 대한 Jupyter 노트북에 액세스할 수 있습니다. **CLUSTERNAME** 을 클러스터의 이름으로 바꿉니다.
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`
   >
   >
3. Notebook을 만듭니다. **새로 만들기**를 클릭한 다음 **PySpark**를 클릭합니다.

   ![Jupyter 노트북을 만들어 대화형 Spark SQL 쿼리 실행](./media/hdinsight-apache-spark-jupyter-spark-sql/hdinsight-spark-create-jupyter-interactive-Spark-SQL-query.png "Jupyter 노트북을 만들어 대화형 Spark SQL 쿼리 실행")

   새 노트북이 만들어지고 Untitled(Untitled.pynb) 이름으로 열립니다.

4. 맨 위에서 노트북 이름을 클릭하고 원하는 경우 식별하기 쉬운 이름을 입력합니다.

    ![Jupter 노트북에 대한 이름을 제공하여 대화형 Spark 쿼리 실행](./media/hdinsight-apache-spark-jupyter-spark-sql/hdinsight-spark-jupyter-notebook-name.png "Jupter 노트북에 대한 이름을 제공하여 대화형 Spark 쿼리 실행")

5.  빈 셀에 다음 코드를 붙여 넣은 다음 **SHIFT + ENTER**를 눌러 코드를 실행합니다. 아래 코드에서 `%%sql`(SQL 매직이라고 함)은 Jupyter Notebook에서 `sqlContext` Hive 쿼리를 실행하는 사전 설정을 사용하도록 합니다. Hive 테이블(**hivesampletable**)에서 상위 10개의 행을 검색하는 쿼리는 모든 HDInsight 클러스터에서 기본적으로 사용할 수 있습니다.

        %%sql
        SELECT * FROM hivesampletable LIMIT 10

    ![HDInsight Spark의 Hive 쿼리](./media/hdinsight-apache-spark-jupyter-spark-sql/hdinsight-spark-get-started-hive-query.png "HDInsight Spark의 Hive 쿼리")

    `%%sql` 매직 및 미리 설정 컨텍스트에 대한 자세한 내용은 [HDInsight 클러스터에 사용할 수 있는 Jupyter 커널](hdinsight-apache-spark-jupyter-notebook-kernels.md)을 참조하세요.

    > [!NOTE]
    > Jupyter에서 쿼리를 실행할 때마다, 웹 브라우저 창 제목에 Notebook 제목과 함께 **(사용 중)** 상태가 표시됩니다. 또한 오른쪽 위 모서리에 있는 **PySpark** 텍스트 옆에 단색 원이 표시됩니다. 작업이 완료되면 속이 빈 원으로 변경됩니다.
    >
    >
    
6. 쿼리 출력을 표시하려면 화면을 새로 고쳐야 합니다.

    ![HDInsight Spark의 Hive 쿼리 출력](./media/hdinsight-apache-spark-jupyter-spark-sql/hdinsight-spark-get-started-hive-query-output.png "HDInsight Spark의 Hive 쿼리 출력")

7. 응용 프로그램 실행을 완료한 후 클러스터 리소스를 해제하도록 Notebook을 종료합니다. 이렇게 하기 위해 Notebook의 **파일** 메뉴에서 **닫기 및 중지**를 클릭합니다.

## <a name="next-step"></a>다음 단계

이 문서에서는 HDInsight Spark 클러스터를 만들고 기본 Spark SQL 쿼리를 실행하는 방법을 알아보았습니다. 이제 [HDInsight Spark에 데이터를 로드하고 대화형 쿼리를 실행](hdinsight-apache-spark-load-data-run-query.md)하는 방법에 대해 알아봅니다.

나중에 다음 단계를 완료하려는 경우 HDInsight 클러스터를 삭제해야 합니다. 

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]
