---
title: Jupyter를 로컬로 설치하고 Azure HDInsight에서 Spark에 연결
description: 컴퓨터에서 로컬로 Jupyter 노트북을 설치하고 Apache Spark 클러스터에 연결하는 방법을 알아봅니다.
ms.service: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/06/2019
ms.author: hrasheed
ms.openlocfilehash: 489685485af4e3c8868f7e0281d2f81464a166f6
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67066187"
---
# <a name="install-jupyter-notebook-on-your-computer-and-connect-to-apache-spark-on-hdinsight"></a>컴퓨터에 Jupyter 노트북을 설치하고 HDInsight에서 Apache Spark에 연결

이 문서에서는 Spark Magic을 사용하여 사용자 지정 PySpark(Python용) 및 Apache Spark(Scala용) 커널로 Jupyter Notebook을 설치한 후 해당 노트북을 HDInsight 클러스터에 연결하는 방법을 알아봅니다. 로컬 컴퓨터에 Jupyter를 설치하는 여러 가지 이유와 몇 가지 어려운 문제가 있을 수 있습니다. 이에 대한 자세한 내용은 이 문서의 끝에 있는 [내 컴퓨터에 Jupyter를 설치해야 하는 이유](#why-should-i-install-jupyter-on-my-computer) 섹션을 참조하세요.

Jupyter를 설치 하 고 HDInsight에서 Apache Spark에 연결 하는 데 참여할는 네 가지 주요 단계가 있습니다.

* Spark 클러스터를 구성 합니다.
* Jupyter 노트를 설치합니다.
* Spark Magic과 함께 PySpark 및 Spark 커널을 설치합니다.
* HDInsight에서 Spark 클러스터에 액세스하도록 Spark Magic을 구성합니다.

HDInsight 클러스터의 Jupyter Notebook에 사용할 수 있는 사용자 지정 커널 및 Spark Magic에 대한 자세한 내용은 [HDInsight의 Apache Spark Linux 클러스터에서 Jupyter Notebook에 사용할 수 있는 커널](apache-spark-jupyter-notebook-kernels.md)을 참조하세요.

## <a name="prerequisites"></a>필수 조건

여기에 나열된 필수 구성 요소는 Jupyter를 설치하기 위한 것이 아니며 Jupyter 노트북이 설치되면 HDInsight 클러스터를 노트북을 연결하기 위한 것입니다.

* HDInsight의 Apache Spark 클러스터입니다. 자세한 내용은 [Azure HDInsight에서 Apache Spark 클러스터 만들기](apache-spark-jupyter-spark-sql.md)를 참조하세요.

## <a name="install-jupyter-notebook-on-your-computer"></a>컴퓨터에 Jupyter 노트북 설치

Jupyter 노트북을 설치하려면 먼저 Python을 설치해야 합니다. 합니다 [Anaconda 배포](https://www.anaconda.com/download/) Python 및 Jupyter Notebook을 모두 설치 됩니다.

사용하는 플랫폼용 [Anaconda 설치 관리자](https://www.anaconda.com/download/) 를 다운로드하고 설치 프로그램을 실행합니다. 설치 마법사를 실행하는 동안 PATH 변수에 Anaconda를 추가하는 옵션을 선택해야 합니다.  도 참조 하세요 [Anaconda를 사용 하 여 Jupyter 설치](https://jupyter.readthedocs.io/en/latest/install.html)합니다.

## <a name="install-spark-magic"></a>Spark magic 설치

1. Spark magic 설치를 아래 명령 중 하나를 입력 합니다. 도 참조 하세요 [sparkmagic 설명서](https://github.com/jupyter-incubator/sparkmagic#installation)합니다.

    |클러스터 버전 | 명령을 설치 합니다. |
    |---|---|
    |v3.5 및 v3.6의 경우 |`pip install sparkmagic==0.12.7`|
    |v3.4|`pip install sparkmagic==0.2.3`|

1. 확인 `ipywidgets` 다음 명령을 실행 하 여 제대로 설치 되어 있습니다.

    ```cmd
    jupyter nbextension enable --py --sys-prefix widgetsnbextension
    ```

## <a name="install-pyspark-and-spark-kernels"></a>PySpark 및 Spark 커널 설치

1. 위치를 식별 `sparkmagic` 다음 명령을 입력 하 여 설치 됩니다.

    ```cmd
    pip show sparkmagic
    ```

    다음 위의 명령을 사용 하 여 식별 된 위치에 작업 디렉터리를 변경 합니다.

1. 새 작업 디렉터리에서 하나 이상의 원하는 kernel(s)를 설치 하려면 아래 명령을 입력 합니다.

    |커널 | 명령 |
    |---|---|
    |Spark|`jupyter-kernelspec install sparkmagic/kernels/sparkkernel`|
    |SparkR|`jupyter-kernelspec install sparkmagic/kernels/sparkrkernel`|
    |PySpark|`jupyter-kernelspec install sparkmagic/kernels/pysparkkernel`|
    |PySpark3|`jupyter-kernelspec install sparkmagic/kernels/pyspark3kernel`|

1. 선택 사항입니다. 서버 확장을 사용 하도록 설정 하려면 아래 명령을 입력 합니다.

    ```cmd
    jupyter serverextension enable --py sparkmagic
    ```

## <a name="configure-spark-magic-to-connect-to-hdinsight-spark-cluster"></a>HDInsight Spark 클러스터에 연결하도록 Spark Magic 구성

이 섹션에서는 Apache Spark 클러스터에 연결 하려면 이전에 설치한 Spark magic를 구성 합니다.

1. 다음 명령을 사용 하 여 Python 셸을 시작 합니다.

    ```cmd
    python
    ```

2. Jupyter 구성 정보는 일반적으로 사용자 홈 디렉터리에 저장됩니다. 홈 디렉터리를 식별 하려면 다음 명령을 입력 하 고 라는 폴더를 만듭니다 **.sparkmagic**합니다.  전체 경로 출력 합니다.

    ```python
    import os
    path = os.path.expanduser('~') + "\\.sparkmagic"
    os.makedirs(path)
    print(path)
    exit()
    ```

3. 폴더 내의 `.sparkmagic`, 라는 파일을 만듭니다 **config.json** 및 그 안에 다음 JSON 코드 조각을 추가 합니다.  

    ```json
    {
      "kernel_python_credentials" : {
        "username": "{USERNAME}",
        "base64_password": "{BASE64ENCODEDPASSWORD}",
        "url": "https://{CLUSTERDNSNAME}.azurehdinsight.net/livy"
      },

      "kernel_scala_credentials" : {
        "username": "{USERNAME}",
        "base64_password": "{BASE64ENCODEDPASSWORD}",
        "url": "https://{CLUSTERDNSNAME}.azurehdinsight.net/livy"
      },

      "heartbeat_refresh_seconds": 5,
      "livy_server_heartbeat_timeout_seconds": 60,
      "heartbeat_retry_seconds": 1
    }
    ```

4. 파일에 다음 편집을 확인 합니다.

    |템플릿 값 | 새 값 |
    |---|---|
    |{USERNAME}|클러스터 로그인 기본값은 `admin`합니다.|
    |{CLUSTERDNSNAME}|클러스터 이름|
    |{BASE64ENCODEDPASSWORD}|Base64 인코딩된 실제 암호에 대 한 암호입니다.  base64 암호를 생성할 수 있습니다 [ https://www.url-encode-decode.com/base64-encode-decode/ ](https://www.url-encode-decode.com/base64-encode-decode/)합니다.|
    |`"livy_server_heartbeat_timeout_seconds": 60`|사용 하는 경우 유지 `sparkmagic 0.12.7` (v3.5 및 v3.6 클러스터).  사용 하는 경우 `sparkmagic 0.2.3` (v3.4 클러스터)를 사용 하 여 대체 `"should_heartbeat": true`합니다.|

    전체 예제 파일을 볼 수 있습니다 [샘플 config.json](https://github.com/jupyter-incubator/sparkmagic/blob/master/sparkmagic/example_config.json)합니다.

   > [!TIP]  
   > 세션이 유출되지 않도록 하트 비트가 전송됩니다. 컴퓨터가 절전 모드로 전환되거나 종료되면, 하트비트가 전송되지 않으므로 세션이 삭제됩니다. 클러스터 v3.4의 경우, 이 동작을 사용하지 않도록 설정하려면 Ambari UI에서 Livy 구성 `livy.server.interactive.heartbeat.timeout`를 `0`로 설정할 수 있습니다. 클러스터 v 3.5의 경우, 위의 3.5 구성을 설정하지 않으면 세션이 삭제되지 않습니다.

5. Jupyter를 시작합니다. 명령 프롬프트에서 다음 명령을 사용합니다.

    ```cmd
    jupyter notebook
    ```

6. 와 커널에서 사용 가능한 Spark magic을 사용할 수 있는 것을 확인 합니다. 다음 단계를 수행합니다.

    a. 새 Notebook을 만듭니다. 오른쪽 모서리에서 선택 **새로 만들기**합니다. 기본 커널 나타납니다 **Python 2** 또는 **Python 3** 및 설치한 커널을 합니다. 실제 값은 선택한 설치에 따라 달라질 수 있습니다.  선택 **PySpark**합니다.

    ![Jupyter 노트북의 커널](./media/apache-spark-jupyter-notebook-install-locally/jupyter-kernels.png "Jupyter 노트북의 커널")

    > [!IMPORTANT]  
    > 선택한 후 **새로 만들기** 셸을 오류를 검토 합니다.  오류를 표시 하는 경우 `TypeError: __init__() got an unexpected keyword argument 'io_loop'` 토네이도의 특정 버전의 알려진된 문제가 발생할 수 있습니다.  그렇다면 커널을 중지 하 고 다음 명령 사용 하 여 토네이도 설치 다운 그레이드: `pip install tornado==4.5.3`합니다.

    b. 다음 코드 조각을 실행합니다.

    ```sql
    %%sql
    SELECT * FROM hivesampletable LIMIT 5
    ```  

    출력을 검색할 수 있으면 HDInsight 클러스터에 대한 연결이 테스트됩니다.

    다른 클러스터에 연결 하도록 노트북 구성을 업데이트 하려는 경우는 위의 3 단계에서에서 값의 새 집합으로 config.json을를 업데이트 합니다.

## <a name="why-should-i-install-jupyter-on-my-computer"></a>내 컴퓨터에 Jupyter를 설치해야 해야 이유는 무엇인가요?

컴퓨터에 Jupyter를 설치한 다음, HDInsight의 Apache Spark 클러스터에 연결하는 이유는 여러 가지입니다.

* Jupyter 노트북을 Azure HDInsight의 Spark 클러스터에 이미 사용할 수 있더라도 컴퓨터에 Jupyter를 설치하면 로컬에서 노트북을 만들고, 실행 중인 클러스터에 대해 애플리케이션을 테스트한 다음 클러스터에 노트북을 업로드하는 옵션이 제공됩니다. 노트북에 클러스터를 업로드하려면 클러스터에서 실행되는 Jupyter 노트북을 사용하여 업로드하거나 클러스터와 연결된 저장소 계정의 /HdiNotebooks 폴더에 저장할 수 있습니다. 클러스터에 Notebook을 저장하는 방법에 대한 자세한 내용은 [Jupyter Notebook이 저장되는 위치](apache-spark-jupyter-notebook-kernels.md#where-are-the-notebooks-stored)를 참조하세요.
* 로컬에서 사용할 수 있는 Notebook을 사용하여 애플리케이션 요구 사항에 따라 다른 Spark 클러스터에 연결할 수 있습니다.
* GitHub를 사용하여 원본 제어 시스템을 구현하고 Notebook에 대한 버전을 제어할 수 있습니다. 여러 사용자가 동일한 Notebook으로 작업할 수 있는 공동 작업 환경이 있을 수도 있습니다.
* 클러스터 없이 로컬로 Notebook을 사용할 수 있습니다. Notebook 또는 개발 환경을 수동으로 관리하기 위해서가 아니라 Notebook을 테스트하기 위해 클러스터가 필요합니다.
* 클러스터에서 Jupyter 설치를 구성하는 것보다 자체 로컬 개발 환경을 구성하는 것이 더 쉬울 수 있습니다.  하나 이상의 원격 클러스터를 구성하지 않고 로컬로 설치한 모든 소프트웨어를 모두 활용할 수 있습니다.

> [!WARNING]  
> 로컬 컴퓨터에 설치된 Jupyter를 사용하면 여러 사용자가 동일한 Spark 클러스터에서 동시에 동일한 Notebook을 실행할 수 있습니다. 이러한 상황에서 여러 Livy 세션이 생성됩니다. 문제가 발생하여 디버깅하려는 경우 어떤 사용자에게 어떤 Livy 세션이 속하는지를 추적하는 복잡한 작업이 됩니다.  

## <a name="next-steps"></a>다음 단계

* [개요: Azure HDInsight의 Apache Spark](apache-spark-overview.md)
* [BI와 Apache Spark: BI 도구와 함께 HDInsight의 Spark를 사용하여 대화형 데이터 분석 수행](apache-spark-use-bi-tools.md)
* [Machine Learning과 Apache Spark: HDInsight의 Spark를 사용하여 HVAC 데이터로 건물 온도 분석](apache-spark-ipython-notebook-machine-learning.md)