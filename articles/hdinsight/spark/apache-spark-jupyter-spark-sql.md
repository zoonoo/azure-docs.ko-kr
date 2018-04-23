---
title: Azure HDInsight에서 Apache Spark 클러스터 만들기 | Microsoft Docs
description: HDInsight에서 Apache Spark 클러스터를 만드는 방법에 대한 HDInsight Spark 빠른 시작입니다.
keywords: spark 빠른 시작, 대화형 spark, 대화형 쿼리, hdinsight spark, azure spark
services: hdinsight
documentationcenter: ''
author: mumian
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.assetid: 91f41e6a-d463-4eb4-83ef-7bbb1f4556cc
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: conceptual
ms.date: 03/01/2018
ms.author: jgao
ms.openlocfilehash: 1fdae7d6e47ff70e83e8153cdc22c0b2881d2743
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2018
---
# <a name="create-an-apache-spark-cluster-in-azure-hdinsight"></a>Azure HDInsight에서 Apache Spark 클러스터 만들기

Azure HDInsight에서 Apache Spark 클러스터를 만드는 방법과 Hive 테이블에 대해 Spark SQL 쿼리를 실행하는 방법을 알아봅니다. HDInsight의 Spark에 대한 자세한 내용은 [개요: Azure HDInsight에서 Apache Spark](apache-spark-overview.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

* **Azure 구독** - 이 자습서를 시작하기 전에 Azure 구독이 있어야 합니다. [무료 Azure 계정 만들기](https://azure.microsoft.com/free)를 참조하세요.

## <a name="create-hdinsight-spark-cluster"></a>HDInsight Spark 클러스터 만들기

[Azure Resource Manager 템플릿](../hdinsight-hadoop-create-linux-clusters-arm-templates.md)을 사용하여 HDInsight Spark 클러스터를 만듭니다. 이 템플릿은 [GitHub](https://azure.microsoft.com/resources/templates/101-hdinsight-spark-linux/)에서 찾을 수 있습니다. 클러스터를 만드는 다른 방법은 [HDInsight 클러스터 만들기](../hdinsight-hadoop-provision-linux-clusters.md)를 참조하세요.

1. Azure 포털에서 템플릿을 열려면 다음 이미지를 클릭합니다.         

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-spark-linux%2Fazuredeploy.json" target="_blank"><img src="./media/apache-spark-jupyter-spark-sql/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. 다음 값을 입력합니다.

    ![Azure Resource Manager 템플릿을 사용하여 HDInsight Spark 클러스터 만들기](./media/apache-spark-jupyter-spark-sql/create-spark-cluster-in-hdinsight-using-azure-resource-manager-template.png "Azure Resource Manager 템플릿을 사용하여 HDInsight에서 Spark 클러스터 만들기")

    * **구독**: 이 클러스터를 만드는 데 사용되는 Azure 구독을 선택합니다.
    * **리소스 그룹**: 리소스 그룹을 만들거나 기존 리소스 그룹을 선택합니다. 리소스 그룹은 프로젝트에 대한 Azure 리소스를 관리하는 데 사용됩니다.
    * **위치**: 리소스 그룹의 위치를 선택합니다. 템플릿에서는 기본 클러스터 저장소뿐만 아니라 클러스터를 만드는 데 이 위치를 사용합니다.
    * **ClusterName**: 만들려는 HDInsight 클러스터의 이름을 입력합니다.
    * **클러스터 로그인 이름 및 암호**: 기본 로그인 이름은 admin입니다. 클러스터 로그인에 대한 암호를 선택합니다.
    * **SSH 사용자 이름 및 암호**. SSH 사용자에 대한 암호를 선택합니다.

3. **위에 명시된 사용 약관에 동의함**을 선택하고 **대시보드에 고정**을 선택한 다음 **구매**를 클릭합니다. **템플릿 배포 중**이라는 제목의 새 타일을 볼 수 있습니다. 클러스터를 만드는 데 약 20분이 걸립니다.

HDInsight 클러스터를 만드는 데 문제가 발생하는 경우 이를 수행하기 위한 적절한 사용 권한이 없을 수 있습니다. 자세한 내용은 [액세스 제어 요구 사항](../hdinsight-administer-use-portal-linux.md#create-clusters)을 참조하세요.

> [!NOTE]
> 이 문서에서는 [클러스터 저장소로 Azure Storage Blob](../hdinsight-hadoop-use-blob-storage.md)을 사용하는 Spark 클러스터를 만듭니다. 기본 저장소로 [Azure Data Lake Store](../hdinsight-hadoop-use-data-lake-store.md)를 사용하는 Spark 클러스터를 만들 수도 있습니다. 자세한 내용은 [Data Lake 저장소가 있는 HDInsight 클러스터 만들기](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md)를 참조하세요.
>
>

## <a name="create-a-jupyter-notebook"></a>Jupyter Notebook 만들기

[Jupyter Notebook](http://jupyter.org)은 데이터와 상호 작용하고 코드를 markdown 텍스트와 결합하고 간단한 시각화를 수행할 수 있는 다양한 프로그래밍 언어를 지원하는 대화형 노트북 환경입니다. HDInsight의 Spark에는 [Zeppelin Notebook](apache-spark-zeppelin-notebook.md)도 포함되어 있습니다. 이 자습서에서는 Jupyter Notebook이 사용됩니다.

**Jupyter Notebook을 만들려면**

1. [Azure 포털](https://portal.azure.com/)을 엽니다.

2. 만든 Spark 클러스터를 엽니다. 지침은 [클러스터 나열 및 표시](../hdinsight-administer-use-portal-linux.md#list-and-show-clusters)를 참조하세요.

3. 포털에서 **클러스터 대시보드**를 클릭한 다음, **Jupyter Notebook**을 클릭합니다. 메시지가 표시되면 클러스터에 대한 관리자 자격 증명을 입력합니다.

   ![Jupyter Notebook을 열어서 대화형 Spark SQL 쿼리 실행](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-open-jupyter-interactive-spark-sql-query.png "Jupyter Notebook을 열어서 대화형 Spark SQL 쿼리 실행")

   > [!NOTE]
   > 또한 브라우저에서 다음 URL을 열어 클러스터에 대한 Jupyter Notebook에 액세스할 수 있습니다. **CLUSTERNAME** 을 클러스터의 이름으로 바꿉니다.
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`
   >
   >
3. **새로 만들기**를 클릭한 다음 **PySpark**를 클릭하여 노트북을 만듭니다. HDInsight 클러스터의 Jupyter 노트북은 **PySpark**, **PySpark3** 및 **Spark**라는 3가지 커널을 지원합니다. 이 자습서에서는 **PySpark** 커널이 사용됩니다. 커널 및 **PySpark** 사용의 이점에 대한 자세한 내용은 [HDInsight에서 Apache Spark 클러스터와 함께 Jupyter Notebook 커널 사용](apache-spark-jupyter-notebook-kernels.md)을 참조하세요.

   ![Jupyter Notebook을 만들어서 대화형 Spark SQL 쿼리 실행](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-create-jupyter-interactive-spark-sql-query.png "Jupyter Notebook을 만들어서 대화형 Spark SQL 쿼리 실행")

   새 노트북이 만들어지고 Untitled(Untitled.pynb) 이름으로 열립니다.

4. 맨 위에서 노트북 이름을 클릭하고 원하는 경우 식별하기 쉬운 이름을 입력합니다.

    ![Jupyter Notebook의 이름을 제공하여 대화형 Spark 쿼리 실행](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-jupyter-notebook-name.png "Jupyter Notebook의 이름을 제공하여 대화형 Spark 쿼리 실행")

## <a name="run-spark-sql-statements-on-a-hive-table"></a>Hive 테이블에서 Spark SQL 문 실행

SQL(구조적 쿼리 언어)은 데이터 쿼리 및 정의에 가장 일반적이며 널리 사용되는 언어입니다. Spark SQL은 익숙한 SQL 구문을 사용하여 구조화된 데이터를 처리하기 위한 Apache Spark에 대한 확장으로 작동합니다.

Spark SQL에서는 SQL 및 HiveQL을 모두 쿼리 언어로 지원합니다. 해당 기능에는 Python, Scala 및 Java의 바인딩이 포함됩니다. 이를 통해 외부 데이터베이스, 구조화된 데이터 파일(예: JSON) 및 Hive 테이블과 같은 여러 위치에 저장된 데이터를 쿼리할 수 있습니다.

Hive 테이블 대신 csv 파일에서 데이터를 읽는 예제는 [HDInsight의 Spark 클러스터에서 대화형 쿼리 실행](apache-spark-load-data-run-query.md)을 참조하세요.

**Spark SQL을 실행하려면**

1. 노트북을 처음으로 시작하면 커널이 백그라운드에서 몇 가지 작업을 수행합니다. 커널이 준비될 때까지 기다립니다. 노트북의 커널 이름 옆에 속이 빈 원이 보이면 커널이 준비된 것입니다. 속이 찬 원은 커널이 사용 중이라는 뜻입니다.

    ![HDInsight Spark의 Hive 쿼리](./media/apache-spark-jupyter-spark-sql/jupyter-spark-kernel-status.png "HDInsight Spark의 Hive 쿼리")

2. 커널이 준비되면 빈 셀에 다음 코드를 붙여넣은 다음, **SHIFT + ENTER**를 눌러 코드를 실행합니다. 이 명령은 클러스터의 Hive 테이블을 나열합니다.

    ```PySpark
    %%sql
    SHOW TABLES
    ```
    Jupyter Notebook을 HDInsight Spark 클러스터와 함께 사용하는 경우 Spark SQL을 사용하여 Hive 쿼리를 실행하는 데 사용할 수 있는 미리 설정된 `sqlContext`를 얻게 됩니다. `%%sql`은 Jupyter Notebook에 미리 설정된 `sqlContext`를 사용하여 Hive 쿼리를 실행하도록 지시합니다. 쿼리는 기본적으로 모든 HDInsight 클러스터와 함께 제공되는 Hive 테이블(**hivesampletable**)에서 상위 10개의 행을 검색합니다. 결과를 얻는데 약 30초가 걸립니다. 출력은 다음과 같이 표시됩니다. 

    ![HDInsight Spark의 Hive 쿼리](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-get-started-hive-query.png "HDInsight Spark의 Hive 쿼리")

    `%%sql` 매직 및 미리 설정 컨텍스트에 대한 자세한 내용은 [HDInsight 클러스터에 사용할 수 있는 Jupyter 커널](apache-spark-jupyter-notebook-kernels.md)을 참조하세요.

    Jupyter에서 쿼리를 실행할 때마다, 웹 브라우저 창 제목에 Notebook 제목과 함께 **(사용 중)** 상태가 표시됩니다. 또한 오른쪽 위 모서리에 있는 **PySpark** 텍스트 옆에 단색 원이 표시됩니다.
    
2. 또 다른 쿼리를 실행하여 `hivesampletable`의 데이터를 봅니다.

    ```PySpark
    %%sql
    SELECT * FROM hivesampletable LIMIT 10
    ```
    
    쿼리 출력이 표시되도록 화면이 새로 고쳐집니다.

    ![HDInsight Spark의 Hive 쿼리 출력](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-get-started-hive-query-output.png "HDInsight Spark의 Hive 쿼리 출력")

2. 노트북의 **파일** 메뉴에서 **닫기 및 중지**를 클릭합니다. 노트북을 종료하면 클러스터 리소스가 릴리스됩니다.

3. 나중에 다음 단계를 완료하려는 경우 이 문서에서 만든 HDInsight 클러스터를 삭제해야 합니다. 

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>다음 단계 

이 문서에서는 HDInsight Spark 클러스터를 만들고 기본 Spark SQL 쿼리를 실행하는 방법을 알아보았습니다. 다음 문서를 진행하여 샘플 데이터에서 대화형 쿼리를 실행하는 데 HDInsight Spark 클러스터를 사용하는 방법에 대해 알아보세요.

> [!div class="nextstepaction"]
>[HDInsight Spark 클러스터에서 대화형 쿼리 실행](apache-spark-load-data-run-query.md)



