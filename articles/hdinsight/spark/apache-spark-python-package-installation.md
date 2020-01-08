---
title: Azure HDInsight에서 Jupyter를 사용 하는 Python 패키지에 대 한 스크립트 작업
description: 스크립트 작업을 사용하여 HDInsight Spark 클러스터와 함께 제공되는 Jupyter 노트북에서 외부 python 패키지를 사용하도록 구성하는 방법에 대한 단계별 지침입니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/19/2019
ms.openlocfilehash: 109ac20d8a3d3dc87b4a83165c0e6c24808c1340
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/28/2019
ms.locfileid: "75529646"
---
# <a name="safely-manage-python-environment-on-azure-hdinsight-using-script-action"></a>스크립트 작업을 사용하여 Azure HDInsight에서 Python 환경을 안전하게 관리

> [!div class="op_single_selector"]
> * [셀 매직 사용](apache-spark-jupyter-notebook-use-external-packages.md)
> * [스크립트 작업 사용](apache-spark-python-package-installation.md)

HDInsight에는 Spark 클러스터에 두 가지 기본 제공 Python 설치 Anaconda Python 2.7 및 Python 3.5이 있습니다. 경우에 따라 고객은 외부 Python 패키지 또는 다른 Python 버전을 설치 하는 등 Python 환경을 사용자 지정 해야 합니다. 이 문서에서는 HDInsight에서 [Apache Spark](https://spark.apache.org/) 클러스터에 대 한 Python 환경을 안전 하 게 관리 하는 모범 사례를 보여 줍니다.

## <a name="prerequisites"></a>필수 조건

* Azure 구독 [Azure 평가판](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)을 참조하세요.

* HDInsight의 Apache Spark. 자세한 내용은 [Azure HDInsight에서 Apache Spark 클러스터 만들기](apache-spark-jupyter-spark-sql.md)를 참조하세요.

   > [!NOTE]  
   > Hdinsight Linux에 Spark 클러스터가 아직 없는 경우 클러스터를 만드는 동안 스크립트 작업을 실행할 수 있습니다. [사용자 지정 스크립트 작업을 사용하는 방법](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux)에 대한 설명서를 참조하세요.

## <a name="support-for-open-source-software-used-on-hdinsight-clusters"></a>HDInsight 클러스터에서 사용하는 오픈 소스 소프트웨어 지원

Microsoft Azure HDInsight 서비스는 Apache Hadoop으로 형성된 오픈 소스 기술의 에코시스템을 사용합니다. Microsoft Azure는 오픈 소스 기술에 대한 일반 수준의 지원을 제공합니다. 자세한 내용은 [Azure 지원 FAQ 웹 사이트](https://azure.microsoft.com/support/faq/)를 참조 하세요. HDInsight 서비스는 기본 제공 구성 요소에 대해 추가 수준의 지원을 제공합니다.

HDInsight 서비스에서 사용할 수 있는 오픈 소스 구성 요소에는 두 가지 유형이 있습니다.

* **기본 제공 구성 요소** - 이러한 구성 요소는 HDInsight 클러스터에 미리 설치 되어 있으며 클러스터의 핵심 기능을 제공합니다. 예를 들어 Apache Hadoop YARN 리소스 관리자, HiveQL (Apache Hive query language) 및 Mahout 라이브러리는이 범주에 속합니다. 클러스터 구성 요소의 전체 목록은 [HDInsight에서 제공하는 Apache Hadoop 클러스터 버전의 새로운 기능](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning)에 있습니다.
* **사용자 지정 구성 요소** - 클러스터의 사용자로서 사용자는 커뮤니티에서 사용 가능한 모든 구성 요소 또는 사용자가 만든 구성 요소를 작업에 설치하거나 사용할 수 있습니다.

> [!IMPORTANT]
> HDInsight 클러스터에 제공되는 구성 요소는 완벽히 지원됩니다. Microsoft 지원은 이러한 구성 요소와 관련된 문제를 격리하고 해결하도록 도와줍니다.
>
> 사용자 지정 구성 요소는 문제 해결에 도움이 되는 합리적인 지원을 받습니다. Microsoft 지원을 통해 문제를 해결할 수 있습니다. 또는 해당 기술에 대한 전문 지식이 있는 오픈 소스 기술에 대해 사용 가능한 채널에 참여하도록 요청할 수 있습니다. 예를 들어 [HDInsight에 대한 MSDN 포럼](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight), [https://stackoverflow.com](https://stackoverflow.com)과 같은 여러 커뮤니티 사이트를 사용할 수 있습니다. 또한 Apache 프로젝트에는 [https://apache.org](https://apache.org)에 대 한 프로젝트 사이트가 있습니다 (예: [Hadoop](https://hadoop.apache.org/)).

## <a name="understand-default-python-installation"></a>기본 Python 설치 이해

Anaconda 설치를 사용 하 여 HDInsight Spark 클러스터를 만듭니다. 클러스터에는 python 2.7 및 Python 3.5의 두 가지 Python 설치가 Anaconda 있습니다. 아래 표에서는 Spark, Livy 및 Jupyter에 대 한 기본 Python 설정을 보여 줍니다.

| |Python 2.7|Python 3.5|
|----|----|----|
|경로|/usr/bin/anaconda/bin|/usr/bin/anaconda/envs/py35/bin|
|Spark|2\.7로 기본 설정|N/A|
|Livy|2\.7로 기본 설정|N/A|
|Jupyter|PySpark 커널|PySpark3 커널|

## <a name="safely-install-external-python-packages"></a>외부 Python 패키지를 안전 하 게 설치

HDInsight 클러스터는 Python 2.7 및 Python 3.5의 기본 제공 Python 환경에 따라 달라 집니다. 이러한 기본 제공 환경에 사용자 지정 패키지를 직접 설치 하면 예기치 않은 라이브러리 버전이 변경 될 수 있으며 클러스터를 추가로 중단할 수 있습니다. Spark 응용 프로그램에 대 한 사용자 지정 외부 Python 패키지를 안전 하 게 설치 하려면 아래 단계를 따르세요.

1. Conda를 사용 하 여 Python 가상 환경을 만듭니다. 가상 환경은 다른 사용자를 분리 하지 않고 프로젝트에 대해 격리 된 공간을 제공 합니다. Python 가상 환경을 만들 때 사용할 python 버전을 지정할 수 있습니다. Python 2.7 및 3.5를 사용 하려는 경우에도 가상 환경을 만들어야 합니다. 이는 클러스터의 기본 환경이 중단 되지 않도록 하기 위한 것입니다. 아래 스크립트를 사용 하 여 모든 노드에 대해 클러스터에서 스크립트 작업을 실행 하 여 Python 가상 환경을 만듭니다. 

    -   `--prefix` conda 가상 환경이 있는 경로를 지정 합니다. 여기에 지정 된 경로에 따라 추가로 변경 해야 하는 몇 가지 configs 있습니다. 이 예제에서는 클러스터에 py35 이라는 기존 가상 환경이 이미 있으므로 py35new를 사용 합니다.
    -   `python=` 가상 환경에 대 한 Python 버전을 지정 합니다. 이 예제에서는 버전 3.5을 사용 합니다 .이 버전은 기본 제공 되는 클러스터와 동일한 버전입니다. 다른 Python 버전을 사용 하 여 가상 환경을 만들 수도 있습니다.
    -   `anaconda`은 가상 환경에 Anaconda 패키지를 설치 하는 anaconda로 package_spec 지정 합니다.
    
    ```bash
    sudo /usr/bin/anaconda/bin/conda create --prefix /usr/bin/anaconda/envs/py35new python=3.5 anaconda --yes 
    ```

2. 필요한 경우 생성 된 가상 환경에 외부 Python 패키지를 설치 합니다. 아래 스크립트를 사용 하 여 모든 노드에 대해 클러스터에서 스크립트 작업을 실행 하 여 외부 Python 패키지를 설치 합니다. 가상 환경 폴더에 파일을 쓰려면 여기에서 sudo 권한이 있어야 합니다.

    사용할 수 있는 패키지의 전체 목록은 [패키지 인덱스](https://pypi.python.org/pypi)를 검색할 수 있습니다. 다른 소스에서 사용 가능한 패키지 목록을 가져올 수도 있습니다. 예를 들어 [conda-forge](https://conda-forge.org/feedstocks/)를 통해 제공되는 패키지를 설치할 수 있습니다.

    -   `seaborn`는 설치 하려는 패키지 이름입니다.
    -   방금 만든 가상 환경 이름을 지정 `-n py35new` 합니다. 가상 환경 만들기에 따라 이름을 변경 해야 합니다.

    ```bash
    sudo /usr/bin/anaconda/bin/conda install seaborn -n py35new --yes
    ```

    가상 환경 이름을 모르는 경우 클러스터의 헤드 노드로 SSH를 실행 하 고 `/usr/bin/anaconda/bin/conda info -e`를 실행 하 여 모든 가상 환경을 표시할 수 있습니다.

3. Spark 및 Livy configs를 변경 하 고 만든 가상 환경을 가리킵니다.

    1. Ambari UI를 열고 Spark2 페이지, Configs 탭으로 이동 합니다.
    
        ![Ambari을 통해 Spark 및 Livy 구성 변경](./media/apache-spark-python-package-installation/ambari-spark-and-livy-config.png)
 
    2. Advanced livy2를 확장 하 고 아래에 아래 문을 추가 합니다. 다른 접두사를 사용 하 여 가상 환경을 설치한 경우 경로를 변경 합니다.

        ```
        export PYSPARK_PYTHON=/usr/bin/anaconda/envs/py35new/bin/python
        export PYSPARK_DRIVER_PYTHON=/usr/bin/anaconda/envs/py35new/bin/python
        ```

        ![Ambari를 통해 Livy 구성 변경](./media/apache-spark-python-package-installation/ambari-livy-config.png)

    3. Advanced spark2를 확장 하 고 기존 export PYSPARK_PYTHON 문을 맨 아래로 바꿉니다. 다른 접두사를 사용 하 여 가상 환경을 설치한 경우 경로를 변경 합니다.

        ```
        export PYSPARK_PYTHON=${PYSPARK_PYTHON:-/usr/bin/anaconda/envs/py35new/bin/python}
        ```

        ![Ambari를 통해 Spark 구성 변경](./media/apache-spark-python-package-installation/ambari-spark-config.png)

    4. 변경 내용을 저장 하 고 영향을 받는 서비스를 다시 시작 합니다. 이러한 변경에는 Spark2 서비스를 다시 시작 해야 합니다. Ambari UI에 필수 다시 시작 미리 알림이 표시 되 면 다시 시작을 클릭 하 여 영향을 받는 모든 서비스를 다시 시작 합니다.

        ![Ambari를 통해 Spark 구성 변경](./media/apache-spark-python-package-installation/ambari-restart-services.png)
 
4.  Jupyter에서 새로 만든 가상 환경을 사용 하려는 경우. Jupyter configs를 변경 하 고 Jupyter를 다시 시작 해야 합니다. 다음 문을 사용 하 여 모든 헤더 노드에서 스크립트 작업을 실행 하 여 새 생성 된 가상 환경에 대 한 Jupyter를 가리키도록 합니다. 가상 환경에 대해 지정한 접두사의 경로를 수정 해야 합니다. 이 스크립트 작업을 실행 한 후 Ambari UI를 통해 Jupyter 서비스를 다시 시작 하 여이 변경 내용을 사용할 수 있도록 합니다.

    ```
    sudo sed -i '/python3_executable_path/c\ \"python3_executable_path\" : \"/usr/bin/anaconda/envs/py35new/bin/python3\"' /home/spark/.sparkmagic/config.json
    ```

    아래 코드를 실행 하 여 Jupyter Notebook에서 Python 환경을 두 번 확인할 수 있습니다.

    ![Jupyter Notebook에서 Python 버전 확인](./media/apache-spark-python-package-installation/check-python-version-in-jupyter.png)

## <a name="known-issue"></a>알려진 문제

Anaconda version 4.7.11 및 4.7.12에 대 한 알려진 버그가 있습니다. 스크립트 작업이 `"Collecting package metadata (repodata.json): ...working..."`에서 중단 되 고 `"Python script has been killed due to timeout after waiting 3600 secs"`실패 하는 것을 볼 수 있습니다. [이 스크립트](https://gregorysfixes.blob.core.windows.net/public/fix-conda.sh) 를 다운로드 하 여 모든 노드에서 스크립트 작업으로 실행 하 여 문제를 해결할 수 있습니다.

Anaconda 버전을 확인 하려면 클러스터 헤더 노드로 SSH를 실행 하 고 `/usr/bin/anaconda/bin/conda --v`를 실행 합니다.

## <a name="seealso"></a>참고 항목

* [개요: Azure HDInsight에서 Apache Spark](apache-spark-overview.md)

### <a name="scenarios"></a>시나리오

* [BI와 Apache Spark: BI 도구와 함께 HDInsight의 Spark를 사용하여 대화형 데이터 분석 수행](apache-spark-use-bi-tools.md)
* [Machine Learning과 Apache Spark: HVAC 데이터를 사용하여 건물 온도를 분석하는 데 HDInsight의 Spark 사용](apache-spark-ipython-notebook-machine-learning.md)
* [Machine Learning과 Apache Spark: HDInsight의 Spark를 사용하여 식품 검사 결과 예측](apache-spark-machine-learning-mllib-ipython.md)
* [HDInsight의 Apache Spark를 사용한 웹 사이트 로그 분석](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>애플리케이션 만들기 및 실행

* [Scala를 사용하여 독립 실행형 애플리케이션 만들기](apache-spark-create-standalone-application.md)
* [Apache Livy를 사용하여 Apache Spark 클러스터에서 원격으로 작업 실행](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>도구 및 확장

* [HDInsight의 Apache Spark 클러스터에서 Jupyter 노트북과 함께 외부 패키지 사용](apache-spark-jupyter-notebook-use-external-packages.md)
* [IntelliJ IDEA용 HDInsight 도구 플러그 인을 사용하여 Spark Scala 애플리케이션 만들기 및 제출](apache-spark-intellij-tool-plugin.md)
* [IntelliJ IDEA용 HDInsight 도구 플러그 인을 사용하여 Apache Spark 애플리케이션을 원격으로 디버그](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [HDInsight에서 Apache Spark 클러스터와 함께 Apache Zeppelin Notebook 사용](apache-spark-zeppelin-notebook.md)
* [HDInsight의 Apache Spark 클러스터에서 Jupyter Notebook에 사용할 수 있는 커널](apache-spark-jupyter-notebook-kernels.md)
* [컴퓨터에 Jupyter를 설치하고 HDInsight Spark 클러스터에 연결](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>리소스 관리

* [Azure HDInsight에서 Apache Spark 클러스터에 대한 리소스 관리](apache-spark-resource-manager.md)
* [HDInsight의 Apache Spark 클러스터에서 실행되는 작업 추적 및 디버그](apache-spark-job-debugging.md)
