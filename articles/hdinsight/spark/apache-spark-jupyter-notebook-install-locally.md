---
title: Jupyter를 로컬로 설치하고 Azure HDInsight에서 Spark에 연결
description: 컴퓨터에서 로컬로 Jupyter 노트북을 설치하고 Apache Spark 클러스터에 연결하는 방법을 알아봅니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/23/2020
ms.openlocfilehash: b2394c580b871105fee84d63c478c3c490b56a0b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82191926"
---
# <a name="install-jupyter-notebook-on-your-computer-and-connect-to-apache-spark-on-hdinsight"></a>컴퓨터에 Jupyter 노트북을 설치하고 HDInsight에서 Apache Spark에 연결

이 문서에서는 Spark magic이 있는 사용자 지정 PySpark (Python 용) 및 Apache Spark (Scala) 커널을 사용 하 여 Jupyter 노트북을 설치 하는 방법에 대해 알아봅니다. 그런 다음, 노트북을 HDInsight 클러스터에 연결 합니다.

Jupyter를 설치 하 고 HDInsight에서 Apache Spark에 연결 하는 데는 4 가지 주요 단계가 있습니다.

* Spark 클러스터를 구성 합니다.
* Jupyter 노트를 설치합니다.
* Spark Magic과 함께 PySpark 및 Spark 커널을 설치합니다.
* HDInsight에서 Spark 클러스터에 액세스하도록 Spark Magic을 구성합니다.

사용자 지정 커널 및 Spark magic에 대 한 자세한 내용은 [HDInsight의 Apache Spark Linux 클러스터에서 Jupyter 노트북에 사용할 수 있는 커널](apache-spark-jupyter-notebook-kernels.md)을 참조 하세요.

## <a name="prerequisites"></a>사전 요구 사항

* HDInsight의 Apache Spark. 자세한 내용은 [Azure HDInsight에서 Apache Spark 클러스터 만들기](apache-spark-jupyter-spark-sql.md)를 참조하세요. 로컬 노트북은 HDInsight 클러스터에 연결 합니다.

* HDInsight의 Spark에서 Jupyter Notebook을 사용하는 방법 이해.

## <a name="install-jupyter-notebook-on-your-computer"></a>컴퓨터에 Jupyter 노트북 설치

Jupyter 노트북을 설치 하기 전에 Python을 설치 합니다. [Anaconda 배포](https://www.anaconda.com/download/) 는 Python 및 Jupyter Notebook를 모두 설치 합니다.

사용하는 플랫폼용 [Anaconda 설치 관리자](https://www.anaconda.com/download/) 를 다운로드하고 설치 프로그램을 실행합니다. 설치 마법사를 실행하는 동안 PATH 변수에 Anaconda를 추가하는 옵션을 선택해야 합니다.  또한 Anaconda를 [사용 하 여 Jupyter 설치](https://jupyter.readthedocs.io/en/latest/install.html)를 참조 하세요.

## <a name="install-spark-magic"></a>Spark magic 설치

1. 아래 명령 중 하나를 입력 하 여 Spark magic을 설치 합니다. 또한 [sparkmagic 설명서](https://github.com/jupyter-incubator/sparkmagic#installation)를 참조 하세요.

    |클러스터 버전 | Install 명령 |
    |---|---|
    |v 3.6 및 v 3.5 |`pip install sparkmagic==0.13.1`|
    |v. 3.4|`pip install sparkmagic==0.2.3`|

1. 다음 `ipywidgets` 명령을 실행 하 여를 제대로 설치 했는지 확인 합니다.

    ```cmd
    jupyter nbextension enable --py --sys-prefix widgetsnbextension
    ```

## <a name="install-pyspark-and-spark-kernels"></a>PySpark 및 Spark 커널 설치

1. 다음 명령을 `sparkmagic` 입력 하 여가 설치 되는 위치를 식별 합니다.

    ```cmd
    pip show sparkmagic
    ```

    그런 다음 작업 디렉터리를 위 명령으로 식별 된 **위치로** 변경 합니다.

1. 새 작업 디렉터리에서 아래 명령을 하나 이상 입력 하 여 원하는 커널을 설치 합니다.

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

이 섹션에서는 이전에 설치한 Spark magic을 구성 하 여 Apache Spark 클러스터에 연결 합니다.

1. 다음 명령을 사용 하 여 Python 셸을 시작 합니다.

    ```cmd
    python
    ```

2. Jupyter 구성 정보는 일반적으로 사용자 홈 디렉터리에 저장됩니다. 다음 명령을 입력 하 여 홈 디렉터리를 식별 하 고 ** \.sparkmagic**라는 폴더를 만듭니다.  전체 경로가 출력 됩니다.

    ```python
    import os
    path = os.path.expanduser('~') + "\\.sparkmagic"
    os.makedirs(path)
    print(path)
    exit()
    ```

3. 폴더 `.sparkmagic`내에서 **app.config** 라는 파일을 만들고 그 안에 다음 json 코드 조각을 추가 합니다.  

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

      "custom_headers" : {
        "X-Requested-By": "livy"
      },

      "heartbeat_refresh_seconds": 5,
      "livy_server_heartbeat_timeout_seconds": 60,
      "heartbeat_retry_seconds": 1
    }
    ```

4. 파일을 다음과 같이 편집 합니다.

    |템플릿 값 | 새 값 |
    |---|---|
    |이름|클러스터 로그인 이며 기본값은 `admin`입니다.|
    |CLUSTERDNSNAME|클러스터 이름|
    |{BASE64ENCODEDPASSWORD}|실제 암호에 대 한 base64 인코딩 암호입니다.  에서 [https://www.url-encode-decode.com/base64-encode-decode/](https://www.url-encode-decode.com/base64-encode-decode/)base64 암호를 생성할 수 있습니다.|
    |`"livy_server_heartbeat_timeout_seconds": 60`|을 사용 하 `sparkmagic 0.12.7` 는 경우 유지 합니다 (클러스터 v1.0 및 v 3.6).  (클러스터 `sparkmagic 0.2.3` v 3.4)를 사용 하는 경우 `"should_heartbeat": true`를로 바꿉니다.|

    [샘플 app.config](https://github.com/jupyter-incubator/sparkmagic/blob/master/sparkmagic/example_config.json)에서 전체 예제 파일을 볼 수 있습니다.

   > [!TIP]  
   > 세션이 유출되지 않도록 하트 비트가 전송됩니다. 컴퓨터가 절전 모드로 전환되거나 종료되면, 하트비트가 전송되지 않으므로 세션이 삭제됩니다. 클러스터 v3.4의 경우, 이 동작을 사용하지 않도록 설정하려면 Ambari UI에서 Livy 구성 `livy.server.interactive.heartbeat.timeout`를 `0`로 설정할 수 있습니다. 클러스터 v 3.5의 경우, 위의 3.5 구성을 설정하지 않으면 세션이 삭제되지 않습니다.

5. Jupyter를 시작합니다. 명령 프롬프트에서 다음 명령을 사용합니다.

    ```cmd
    jupyter notebook
    ```

6. 커널을 사용할 수 있는 Spark magic을 사용할 수 있는지 확인 합니다. 다음 단계를 완료합니다.

    a. 새 Notebook을 만듭니다. 오른쪽 모서리에서 **새로 만들기**를 선택 합니다. 기본 커널 **python 2** 또는 **python 3** 및 사용자가 설치한 커널을 표시 되어야 합니다. 실제 값은 설치 선택 사항에 따라 달라질 수 있습니다.  **PySpark**를 선택 합니다.

    ![Jupyter 노트북에서 사용 가능한 커널](./media/apache-spark-jupyter-notebook-install-locally/jupyter-kernels-notebook.png "Jupyter 노트북의 커널")

    > [!IMPORTANT]  
    > **새로 만들기** 를 선택한 후 오류에 대 한 셸을 검토 합니다.  오류가 `TypeError: __init__() got an unexpected keyword argument 'io_loop'` 표시 되는 경우 특정 버전의 토네이도에서 알려진 문제가 발생할 수 있습니다.  그렇다면 커널을 중지 한 후 다음 명령을 사용 하 여 토네이도 설치를 다운 그레이드 `pip install tornado==4.5.3`합니다.

    b. 다음 코드 조각을 실행합니다.

    ```sql
    %%sql
    SELECT * FROM hivesampletable LIMIT 5
    ```  

    출력을 검색할 수 있으면 HDInsight 클러스터에 대한 연결이 테스트됩니다.

    다른 클러스터에 연결 하도록 노트북 구성을 업데이트 하려는 경우 위의 3 단계와 같이 새 값 집합을 사용 하 여 app.config를 업데이트 합니다.

## <a name="why-should-i-install-jupyter-on-my-computer"></a>내 컴퓨터에 Jupyter를 설치해야 해야 이유는 무엇인가요?

컴퓨터에 Jupyter를 설치 하 고 HDInsight의 Apache Spark 클러스터에 연결 하는 이유는 다음과 같습니다.

* 는 전자 필기장을 로컬로 만들고, 실행 중인 클러스터에 대해 응용 프로그램을 테스트 한 다음, 노트북을 클러스터에 업로드 하는 옵션을 제공 합니다. 클러스터에 노트북을 업로드 하려면 또는 클러스터를 실행 하는 Jupyter 노트북을 사용 하 여 업로드 하거나 클러스터와 연결 된 저장소 계정의 `/HdiNotebooks` 폴더에 저장 합니다. 클러스터에 Notebook을 저장하는 방법에 대한 자세한 내용은 [Jupyter Notebook이 저장되는 위치](apache-spark-jupyter-notebook-kernels.md#where-are-the-notebooks-stored)를 참조하세요.
* 로컬에서 사용할 수 있는 Notebook을 사용하여 애플리케이션 요구 사항에 따라 다른 Spark 클러스터에 연결할 수 있습니다.
* GitHub를 사용하여 원본 제어 시스템을 구현하고 Notebook에 대한 버전을 제어할 수 있습니다. 여러 사용자가 동일한 Notebook으로 작업할 수 있는 공동 작업 환경이 있을 수도 있습니다.
* 클러스터 없이 로컬로 Notebook을 사용할 수 있습니다. Notebook 또는 개발 환경을 수동으로 관리하기 위해서가 아니라 Notebook을 테스트하기 위해 클러스터가 필요합니다.
* 클러스터에 Jupyter 설치를 구성 하는 것 보다 자체 로컬 개발 환경을 구성 하는 것이 더 쉬울 수 있습니다.  하나 이상의 원격 클러스터를 구성 하지 않고 로컬로 설치한 모든 소프트웨어를 활용할 수 있습니다.

> [!WARNING]  
> 로컬 컴퓨터에 설치된 Jupyter를 사용하면 여러 사용자가 동일한 Spark 클러스터에서 동시에 동일한 Notebook을 실행할 수 있습니다. 이러한 상황에서 여러 Livy 세션이 생성됩니다. 문제가 발생하여 디버깅하려는 경우 어떤 사용자에게 어떤 Livy 세션이 속하는지를 추적하는 복잡한 작업이 됩니다.  

## <a name="next-steps"></a>다음 단계

* [개요: Azure HDInsight에서 Apache Spark](apache-spark-overview.md)
* [Apache Spark에서 Jupyter 노트북에 대 한 커널](apache-spark-jupyter-notebook-kernels.md)
* [Apache Spark에서 Jupyter 노트북과 함께 외부 패키지 사용](apache-spark-jupyter-notebook-use-external-packages.md)
