---
title: Azure HDInsight에서 Jupyter를 사용한 Python 패키지용 스크립트 작업
description: 스크립트 작업을 사용하여 HDInsight Spark 클러스터와 함께 제공되는 Jupyter 노트북에서 외부 python 패키지를 사용하도록 구성하는 방법에 대한 단계별 지침입니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020, devx-track-python
ms.date: 04/29/2020
ms.openlocfilehash: 5a0f9f9f972ec42987d6152c16e4377e399cdba5
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/28/2020
ms.locfileid: "92896415"
---
# <a name="safely-manage-python-environment-on-azure-hdinsight-using-script-action"></a>스크립트 작업을 사용하여 Azure HDInsight에서 Python 환경을 안전하게 관리

HDInsight는 Spark 클러스터에서 두 개의 기본 제공 Python 설치(Anaconda Python 2.7 및 Python 3.5)가 있습니다. 고객은 외부 Python 패키지 설치와 같은 Python 환경을 사용자 지정 해야 할 수 있습니다. 여기서는 HDInsight에서 Apache Spark 클러스터에 대한 Python 환경을 안전하게 관리하는 모범 사례를 소개합니다.

## <a name="prerequisites"></a>사전 요구 사항

HDInsight의 Apache Spark. 자세한 내용은 [Azure HDInsight에서 Apache Spark 클러스터 만들기](apache-spark-jupyter-spark-sql.md)를 참조하세요. HDInsight에 Spark 클러스터가 아직 없는 경우 클러스터를 만드는 동안 스크립트 작업을 실행할 수 있습니다. [사용자 지정 스크립트 작업을 사용하는 방법](../hdinsight-hadoop-customize-cluster-linux.md)에 대한 설명서를 참조하세요.

## <a name="support-for-open-source-software-used-on-hdinsight-clusters"></a>HDInsight 클러스터에서 사용하는 오픈 소스 소프트웨어 지원

Microsoft Azure HDInsight 서비스는 Apache Hadoop으로 형성된 오픈 소스 기술의 환경을 사용합니다. Microsoft Azure는 오픈 소스 기술에 대한 일반 수준의 지원을 제공합니다. 자세한 내용은 [Azure 지원 FAQ 웹 사이트](https://azure.microsoft.com/support/faq/)를 참조하세요. HDInsight 서비스는 기본 제공 구성 요소에 대해 추가 수준의 지원을 제공합니다.

HDInsight 서비스에서 사용할 수 있는 오픈 소스 구성 요소에는 두 가지 유형이 있습니다.

|구성 요소 |Description |
|---|---|
|기본 제공|이러한 구성 요소는 HDInsight 클러스터에 미리 설치되어 있으며 클러스터의 핵심 기능을 제공합니다. 예를 들어 Apache Hadoop YARN Resource Manager, Apache HiveQL(Hive 쿼리 언어) 및 Mahout 라이브러리는 이 범주에 속합니다. 클러스터 구성 요소의 전체 목록은 [HDInsight에서 제공하는 Apache Hadoop 클러스터 버전의 새로운 기능](../hdinsight-component-versioning.md)에 있습니다.|
|사용자 지정|클러스터 사용자는 커뮤니티에서 사용할 수 있거나 직접 만든 구성 요소를 워크로드에 설치하거나 사용할 수 있습니다.|

> [!IMPORTANT]
> HDInsight 클러스터에 제공되는 구성 요소는 완벽히 지원됩니다. Microsoft 지원은 이러한 구성 요소와 관련된 문제를 격리하고 해결하도록 도와줍니다.
>
> 사용자 지정 구성 요소는 문제 해결에 도움이 되는 합리적인 지원을 받습니다. Microsoft 지원을 통해 문제를 해결할 수 있습니다. 또는 해당 기술에 대한 전문 지식이 있는 오픈 소스 기술에 대해 사용 가능한 채널에 참여하도록 요청할 수 있습니다. 예를 들어 [HDInsight에 대한 Microsoft Q&A 질문 페이지](/answers/topics/azure-hdinsight.html), `https://stackoverflow.com` 같은 여러 커뮤니티 사이트를 사용할 수 있습니다. 또한 Apache 프로젝트는 `https://apache.org`에 프로젝트 사이트가 있습니다.

## <a name="understand-default-python-installation"></a>기본 Python 설치 이해

Anaconda 설치를 사용하여 HDInsight Spark 클러스터를 만듭니다. 클러스터에는 두 개의 Python 설치(Anaconda Python 2.7 및 Python 3.5)가 있습니다. 아래 표에서는 Spark, Livy 및 Jupyter에 대한 기본 Python 설정을 보여 줍니다.

|설정 |Python 2.7|Python 3.5|
|----|----|----|
|경로|/usr/bin/anaconda/bin|/usr/bin/anaconda/envs/py35/bin|
|Spark 버전|2\.7로 기본 설정|Config를 3.5으로 변경할 수 있습니다.|
|Livy 버전|2\.7로 기본 설정|Config를 3.5으로 변경할 수 있습니다.|
|Jupyter|PySpark 커널|PySpark3 커널|

## <a name="safely-install-external-python-packages"></a>외부 Python 패키지를 안전하게 설치

HDInsight 클러스터는 Python 2.7 및 Python 3.5의 기본 제공 Python 환경에 따라 달라집니다. 이러한 기본 제공 환경에 사용자 지정 패키지를 직접 설치하면 예기치 않은 라이브러리 버전 변경이 발생할 수 있습니다. 그리고 클러스터를 추가로 중단합니다. Spark 애플리케이션에 대한 사용자 지정 외부 Python 패키지를 안전하게 설치하려면 아래 단계를 따르세요.

1. Conda를 사용하여 Python 가상 환경을 만듭니다. 가상 환경은 다른 프로젝트의 중단 없이 격리된 프로젝트 공간을 제공합니다. Python 가상 환경을 만들 때 사용할 Python 버전을 지정할 수 있습니다. Python 2.7 및 3.5를 사용하려는 경우에도 가상 환경을 만들어야 합니다. 이 요구 사항은 클러스터의 기본 환경이 중단되지 않도록 하기 위한 것입니다. 아래 스크립트를 사용하여 모든 노드를 대상으로 클러스터에서 스크립트 작업을 실행하여 Python 가상 환경을 만듭니다.

    -   `--prefix`는 Conda 가상 환경이 있는 경로를 지정합니다. 여기에 지정된 경로에 따라 추가로 변경해야 하는 몇 가지 구성이 있습니다. 이 예제에서는 클러스터에 py35라는 기존 가상 환경이 이미 있으므로 py35new를 사용합니다.
    -   `python=`은 가상 환경에 대한 Python 버전을 지정합니다. 이 예제에서는 기본 제공되는 클러스터와 동일한 3.5 버전을 사용합니다. 다른 Python 버전을 사용하여 가상 환경을 만들 수도 있습니다.
    -   `anaconda`는 Anaconda로 package_spec을 지정하여 가상 환경에 Anaconda 패키지를 설치합니다.
    
    ```bash
    sudo /usr/bin/anaconda/bin/conda create --prefix /usr/bin/anaconda/envs/py35new python=3.5 anaconda --yes
    ```

2. 필요한 경우 생성된 가상 환경에 외부 Python 패키지를 설치합니다. 아래 스크립트를 사용하여 모든 노드를 대상으로 클러스터에서 스크립트 작업을 실행하여 외부 Python 패키지를 설치합니다. 가상 환경 폴더에 파일을 쓰려면 sudo 권한이 있어야 합니다.

    사용할 수 있는 패키지의 전체 목록을 보려면 [패키지 인덱스](https://pypi.python.org/pypi)를 검색합니다. 다른 소스에서 사용 가능한 패키지 목록을 가져올 수도 있습니다. 예를 들어 [conda-forge](https://conda-forge.org/feedstocks/)를 통해 제공되는 패키지를 설치할 수 있습니다.

    최신 버전의 라이브러리를 설치하려면 아래 명령을 사용하세요.

    - Conda 채널 사용:

        -   `seaborn`은 설치하려는 패키지 이름입니다.
        -   `-n py35new`는 방금 만든 가상 환경 이름을 지정합니다. 가상 환경 생성에 따라 이름을 적절히 변경해야 합니다.

        ```bash
        sudo /usr/bin/anaconda/bin/conda install seaborn -n py35new --yes
        ```

    - 또는 PyPi 리포지토리를 사용하고 `seaborn` 및 `py35new`를 이에 맞춰 변경합니다.
        ```bash
        sudo /usr/bin/anaconda/envs/py35new/bin/pip install seaborn
        ```

    특정 버전의 라이브러리를 설치하려면 아래 명령을 사용하세요.

    - Conda 채널 사용:

        -   `numpy=1.16.1`은 설치하려는 패키지 이름 및 버전입니다.
        -   `-n py35new`는 방금 만든 가상 환경 이름을 지정합니다. 가상 환경 생성에 따라 이름을 적절히 변경해야 합니다.

        ```bash
        sudo /usr/bin/anaconda/bin/conda install numpy=1.16.1 -n py35new --yes
        ```

    - 또는 PyPi 리포지토리를 사용하고 `numpy==1.16.1` 및 `py35new`를 이에 맞춰 변경합니다.

        ```bash
        sudo /usr/bin/anaconda/envs/py35new/bin/pip install numpy==1.16.1
        ```

    가상 환경 이름을 모르는 경우 클러스터의 헤드 노드로 SSH를 실행하고 `/usr/bin/anaconda/bin/conda info -e`를 실행하여 모든 가상 환경을 표시할 수 있습니다.

3. Spark 및 Livy 구성을 변경하고 만든 가상 환경을 가리킵니다.

    1. Ambari UI를 열고 Spark2 페이지의 구성 탭으로 이동합니다.

        ![Ambari을 통해 Spark 및 Livy 구성 변경](./media/apache-spark-python-package-installation/ambari-spark-and-livy-config.png)

    2. 고급 livy2-env를 확장하고 맨 아래에 다음 문을 추가합니다. 다른 접두사를 사용하여 가상 환경을 설치한 경우 경로를 이에 맞춰 변경합니다.

        ```bash
        export PYSPARK_PYTHON=/usr/bin/anaconda/envs/py35new/bin/python
        export PYSPARK_DRIVER_PYTHON=/usr/bin/anaconda/envs/py35new/bin/python
        ```

        ![Ambari를 통해 Livy 구성 변경](./media/apache-spark-python-package-installation/ambari-livy-config.png)

    3. 고급 spark2-env를 확장하고 맨 아래의 기존 내보내기 PYSPARK_PYTHON 문을 바꿉니다. 다른 접두사를 사용하여 가상 환경을 설치한 경우 경로를 이에 맞춰 변경합니다.

        ```bash
        export PYSPARK_PYTHON=${PYSPARK_PYTHON:-/usr/bin/anaconda/envs/py35new/bin/python}
        ```

        ![Ambari를 통해 Spark 구성 변경](./media/apache-spark-python-package-installation/ambari-spark-config.png)

    4. 변경 내용을 저장하고 영향을 받은 서비스를 다시 시작합니다. 이러한 변경을 하면 Spark2 서비스를 다시 시작해야 합니다. Ambari UI에서 필수 다시 시작 알림이 표시되면 다시 시작을 클릭하여 영향을 받는 모든 서비스를 다시 시작합니다.

        ![서비스 다시 시작](./media/apache-spark-python-package-installation/ambari-restart-services.png)

    5. 작업이 업데이트 된 spark 구성 (및)을 가리키도록 하기 위해 Spark 세션에 두 속성을 설정 `spark.yarn.appMasterEnv.PYSPARK_PYTHON` 합니다 `spark.yarn.appMasterEnv.PYSPARK_DRIVER_PYTHON` . 

        터미널 또는 노트북을 사용 하 여 함수를 사용 `spark.conf.set` 합니다.

        ```spark
        spark.conf.set("spark.yarn.appMasterEnv.PYSPARK_PYTHON", "/usr/bin/anaconda/envs/py35/bin/python")
        spark.conf.set("spark.yarn.appMasterEnv.PYSPARK_DRIVER_PYTHON", "/usr/bin/anaconda/envs/py35/bin/python")
        ```

        Livy를 사용 하는 경우 요청 본문에 다음 속성을 추가 합니다.

        ```
        “conf” : {
        “spark.yarn.appMasterEnv.PYSPARK_PYTHON”:”/usr/bin/anaconda/envs/py35/bin/python”,
        “spark.yarn.appMasterEnv.PYSPARK_DRIVER_PYTHON”:”/usr/bin/anaconda/envs/py35/bin/python”
        }
        ```

4. Jupyter에서 새로 만든 가상 환경을 사용하려는 경우 다음과 같이 합니다. Jupyter 구성을 변경하고 Jupyter를 다시 시작합니다. 아래의 문을 사용하여 모든 헤더 노드에서 스크립트 작업을 실행하여 Jupyter가 새로 생성된 가상 환경을 가리키도록 합니다. 가상 환경을 대상으로 지정한 접두사의 경로를 수정해야 합니다. 이 스크립트 작업을 실행한 후 Ambari UI를 통해 Jupyter 서비스를 다시 시작하여 이 변경 내용을 적용합니다.

    ```bash
    sudo sed -i '/python3_executable_path/c\ \"python3_executable_path\" : \"/usr/bin/anaconda/envs/py35new/bin/python3\"' /home/spark/.sparkmagic/config.json
    ```

    아래 코드를 실행하여 Jupyter Notebook에서 Python 환경을 이중으로 확인할 수 있습니다.

    ![Jupyter Notebook에서 Python 버전 확인](./media/apache-spark-python-package-installation/check-python-version-in-jupyter.png)

## <a name="known-issue"></a>알려진 문제

Anaconda 버전 `4.7.11`, `4.7.12` 및 `4.8.0`에 대한 알려진 버그가 있습니다. 스크립트 동작이에서 응답을 중지 하 고를 사용 하 여 실패 하는 것을 볼 수 있습니다 `"Collecting package metadata (repodata.json): ...working..."` `"Python script has been killed due to timeout after waiting 3600 secs"` . [관련 스크립트](https://gregorysfixes.blob.core.windows.net/public/fix-conda.sh)를 다운로드하여 모든 노드에서 스크립트 작업으로 실행함으로써 문제를 해결할 수 있습니다.

Anaconda 버전을 확인하려면 클러스터 헤더 노드로 SSH를 실행하고 `/usr/bin/anaconda/bin/conda --v`를 실행합니다.

## <a name="next-steps"></a>다음 단계

* [개요: Azure HDInsight의 Apache Spark](apache-spark-overview.md)
* [Apache Spark에서 Jupyter Notebook을 사용하는 외부 패키지](apache-spark-jupyter-notebook-use-external-packages.md)
* [HDInsight의 Apache Spark 클러스터에서 실행되는 작업 추적 및 디버그](apache-spark-job-debugging.md)
