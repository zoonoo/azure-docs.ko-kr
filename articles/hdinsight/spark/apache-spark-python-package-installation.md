---
title: 스크립트 작업 - Azure HDInsight에서 Jupyter를 사용해 Python 패키지 설치
description: 스크립트 작업을 사용하여 HDInsight Spark 클러스터와 함께 제공되는 Jupyter 노트북에서 외부 python 패키지를 사용하도록 구성하는 방법에 대한 단계별 지침입니다.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: hrasheed
ms.openlocfilehash: 5b90519120e64a905073eec6b73e4ced64fe4f64
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/28/2018
ms.locfileid: "52496052"
---
# <a name="use-script-action-to-install-external-python-packages-for-jupyter-notebooks-in-apache-spark-clusters-on-hdinsight"></a>HDInsight의 Apache Spark 클러스터에서 스크립트 작업을 사용하여 Jupyter Notebook용 외부 python 패키지 설치
> [!div class="op_single_selector"]
> * [셀 매직 사용](apache-spark-jupyter-notebook-use-external-packages.md)
> * [스크립트 작업 사용](apache-spark-python-package-installation.md)
>
>

클러스터에 기본적으로 포함되지 않는 외부의 커뮤니티 제공 **python** 패키지를 사용하도록 HDInsight(Linux)의 [Apache Spark](https://spark.apache.org/) 클러스터를 구성하는 스크립트 작업 사용 방법을 알아봅니다.

> [!NOTE]
> `%%configure` 매직을 사용하여 외부 패키지를 사용하도록 Jupyter Notebook을 구성할 수도 있습니다. 지침에 대해서는 [HDInsight의 Apache Spark 클러스터에서 Jupyter Notebook과 함께 외부 패키지 사용](apache-spark-jupyter-notebook-use-external-packages.md)을 참조하세요.
> 
> 

사용할 수 있는 패키지의 전체 목록은 [패키지 인덱스](https://pypi.python.org/pypi)를 검색할 수 있습니다. 다른 소스에서 사용 가능한 패키지 목록을 가져올 수도 있습니다. 예를 들어 [conda-forge](https://conda-forge.org/feedstocks/)를 통해 제공되는 패키지를 설치할 수 있습니다.

이 문서에서는 클러스터에서 스크립트 작업을 사용하여 [TensorFlow](https://www.tensorflow.org/) 패키지를 설치하고 Jupyter 노트북을 통해 예제로 사용하는 방법을 알아봅니다.

## <a name="prerequisites"></a>필수 조건
다음이 있어야 합니다.

* Azure 구독. [Azure 평가판](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)을 참조하세요.
* HDInsight의 Apache Spark 클러스터입니다. 자세한 내용은 [Azure HDInsight에서 Apache Spark 클러스터 만들기](apache-spark-jupyter-spark-sql.md)를 참조하세요.

   > [!NOTE]
   > Hdinsight Linux에 Spark 클러스터가 아직 없는 경우 클러스터를 만드는 동안 스크립트 작업을 실행할 수 있습니다. [사용자 지정 스크립트 작업을 사용하는 방법](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux)에 대한 설명서를 참조하세요.
   > 
   > 
   
## <a name="support-for-open-source-software-used-on-hdinsight-clusters"></a>HDInsight 클러스터에서 사용하는 오픈 소스 소프트웨어 지원

Microsoft Azure HDInsight 서비스는 Apache Hadoop으로 형성된 오픈 소스 기술의 에코시스템을 사용합니다. Microsoft Azure는 오픈 소스 기술에 대한 일반 수준의 지원을 제공합니다. 자세한 내용은 [Azure Support FAQ 웹 사이트](https://azure.microsoft.com/support/faq/)의 **지원 범위** 섹션을 참조하세요. HDInsight 서비스는 기본 제공 구성 요소에 대해 추가 수준의 지원을 제공합니다.

HDInsight 서비스에서 사용할 수 있는 오픈 소스 구성 요소에는 두 가지 유형이 있습니다.

* **기본 제공 구성 요소** - 이러한 구성 요소는 HDInsight 클러스터에 미리 설치 되어 있으며 클러스터의 핵심 기능을 제공합니다. 예를 들어 Apache Hadoop YARN ResourceManager, Apache Hive 쿼리 언어(HiveQL) 및 Mahout 라이브러리는 이 범주에 속합니다. 클러스터 구성 요소의 전체 목록은 [HDInsight에서 제공하는 Apache Hadoop 클러스터 버전의 새로운 기능](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning)에 있습니다.
* **사용자 지정 구성 요소** - 클러스터의 사용자로서 사용자는 커뮤니티에서 사용 가능한 모든 구성 요소 또는 사용자가 만든 구성 요소를 작업에 설치하거나 사용할 수 있습니다.

> [!WARNING]
> HDInsight 클러스터에 제공되는 구성 요소는 완벽히 지원됩니다. Microsoft 지원은 이러한 구성 요소와 관련된 문제를 격리하고 해결하도록 도와줍니다.
>
> 사용자 지정 구성 요소는 문제 해결에 도움이 되는 합리적인 지원을 받습니다. Microsoft 지원을 통해 문제를 해결할 수 있습니다. 또는 해당 기술에 대한 전문 지식이 있는 오픈 소스 기술에 대해 사용 가능한 채널에 참여하도록 요청할 수 있습니다. 예를 들어 [HDInsight에 대한 MSDN 포럼](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight), [http://stackoverflow.com](http://stackoverflow.com)과 같은 여러 커뮤니티 사이트를 사용할 수 있습니다. Apache 프로젝트는 [http://apache.org](http://apache.org)에 프로젝트 사이트가 있습니다(예: [Hadoop](http://hadoop.apache.org/)).


## <a name="use-external-packages-with-jupyter-notebooks"></a>Jupyter 노트북에서 외부 패키지 사용

1. [Azure Portal](https://portal.azure.com/)의 시작 보드에서 Spark 클러스터에 대한 타일을 클릭합니다(시작 보드에 고정한 경우). **모두 찾아보기** > **HDInsight 클러스터**에서 클러스터로 이동할 수도 있습니다.   

2. Spark 클러스터 블레이드의 왼쪽 창에서 **스크립트 작업**을 클릭합니다. 스크립트 유형으로 "사용자 지정"을 사용하고 스크립트 작업의 이름을 입력합니다. **헤드 및 작업자 노드**에서 스크립트를 실행하고 매개 변수 필드를 비워 둡니다. 기본 스크립트는 https://hdiconfigactions.blob.core.windows.net/linuxtensorflow/tensorflowinstall.sh[사용자 지정 스크립트 작업을 사용하는 방법](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux)의 설명서를 참조할 수 있습니다.

   > [!NOTE]
   > 클러스터에 두 개의 python 설치 프로그램이 있습니다. Spark는 `/usr/bin/anaconda/bin`에 있는 Anaconda python 설치 프로그램을 사용합니다. `/usr/bin/anaconda/bin/pip` 및 `/usr/bin/anaconda/bin/conda`를 통해 사용자 지정 작업에서 해당 설치 프로그램을 참조하세요.
   > 
   > 

3. PySpark Jupyter 노트북을 엽니다.

    ![새 Jupyter 노트북 만들기](./media/apache-spark-python-package-installation/hdinsight-spark-create-notebook.png "새 Jupyter 노트북 만들기")

4. 새 노트북이 만들어지고 Untitled.pynb 이름으로 열립니다. 맨 위에서 노트북 이름을 클릭하고 식별하기 쉬운 이름을 입력합니다.

    ![노트북 이름 제공](./media/apache-spark-python-package-installation/hdinsight-spark-name-notebook.png "노트북 이름 제공")

5. 이제 `import tensorflow` 및 hello world 예제를 실행합니다. 

    복사할 코드:

        import tensorflow as tf
        hello = tf.constant('Hello, TensorFlow!')
        sess = tf.Session()
        print(sess.run(hello))

    결과는 다음과 유사하게 표시됩니다.
    
    ![TensorFlow 코드 실행](./media/apache-spark-python-package-installation/execution.png "TensorFlow 코드 실행")

## <a name="seealso"></a>참고 항목
* [개요: Azure HDInsight에서 Apache Spark](apache-spark-overview.md)

### <a name="scenarios"></a>시나리오
* [BI와 Apache Spark: BI 도구와 함께 HDInsight의 Spark를 사용하여 대화형 데이터 분석 수행](apache-spark-use-bi-tools.md)
* [Machine Learning과 Apache Spark: HVAC 데이터를 사용하여 건물 온도를 분석하는 데 HDInsight의 Spark 사용](apache-spark-ipython-notebook-machine-learning.md)
* [Machine Learning과 Apache Spark: 음식 검사 결과를 예측하는 데 HDInsight의 Spark 사용](apache-spark-machine-learning-mllib-ipython.md)
* [HDInsight의 Apache Spark를 사용한 웹 사이트 로그 분석](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>응용 프로그램 만들기 및 실행
* [Scala를 사용하여 독립 실행형 응용 프로그램 만들기](apache-spark-create-standalone-application.md)
* [Livy를 사용하여 Apache Spark 클러스터에서 원격으로 작업 실행](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>도구 및 확장
* [HDInsight의 Apache Spark 클러스터에서 Jupyter 노트북과 함께 외부 패키지 사용](apache-spark-jupyter-notebook-use-external-packages.md)
* [IntelliJ IDEA용 HDInsight 도구 플러그 인을 사용하여 Spark Scala 응용 프로그램 만들기 및 제출](apache-spark-intellij-tool-plugin.md)
* [IntelliJ IDEA용 HDInsight 도구 플러그 인을 사용하여 Apache Spark 애플리케이션을 원격으로 디버그](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [HDInsight에서 Apache Spark 클러스터와 함께 Apache Zeppelin Notebook 사용](apache-spark-zeppelin-notebook.md)
* [HDInsight의 Apache Spark 클러스터에서 Jupyter Notebook에 사용할 수 있는 커널](apache-spark-jupyter-notebook-kernels.md)
* [컴퓨터에 Jupyter를 설치하고 HDInsight Spark 클러스터에 연결](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>리소스 관리
* [Azure HDInsight에서 Apache Spark 클러스터에 대한 리소스 관리](apache-spark-resource-manager.md)
* [HDInsight의 Apache Spark 클러스터에서 실행되는 작업 추적 및 디버그](apache-spark-job-debugging.md)
