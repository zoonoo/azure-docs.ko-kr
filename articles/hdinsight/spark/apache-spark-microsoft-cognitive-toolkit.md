---
title: 아파치 스파크와 마이크로 소프트 인지 도구 키트 - Azure HDInsight
description: Azure HDInsight Spark 클러스터에서 Spark Python API를 사용하여 데이터 세트에 학습된 Microsoft Cognitive 도구 키트 심층 학습 모델을 적용하는 방법에 대해 알아봅니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/14/2020
ms.openlocfilehash: 1933db624dfef2ffa747ecb043be6730b6b884b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78206557"
---
# <a name="use-microsoft-cognitive-toolkit-deep-learning-model-with-azure-hdinsight-spark-cluster"></a>Azure HDInsight Spark 클러스터에서 Microsoft Cognitive 도구 키트 심층 학습 모델 사용

이 문서에서는 다음 단계를 수행합니다.

1. 사용자 지정 스크립트를 실행하여 Azure HDInsight Spark 클러스터에 [Microsoft Cognitive Toolkit](https://docs.microsoft.com/cognitive-toolkit/)를 설치합니다.

2. [Apache Spark](https://spark.apache.org/) 클러스터에 [Jupyter Notebook](https://jupyter.org/)을 업로드하여 [Spark Python API(PySpark)](https://spark.apache.org/docs/latest/api/python/index.html)를 통해 Azure Blob Storage 계정의 파일에 학습된 Microsoft Cognitive Toolkit 딥러닝 모델을 적용하는 방법을 확인합니다.

## <a name="prerequisites"></a>사전 요구 사항

* HDInsight의 Apache Spark. [Apache Spark 클러스터 만들기](./apache-spark-jupyter-spark-sql-use-portal.md)를 참조하세요.

* HDInsight의 Spark에서 Jupyter Notebook을 사용하는 방법 이해. 자세한 내용은 [HDInsight의 Apache Spark로 데이터 로드 및 쿼리 실행](./apache-spark-load-data-run-query.md)을 참조하세요.

## <a name="how-does-this-solution-flow"></a>이 솔루션을 전달하는 방법

이 솔루션은 이 문서의 일부로 업로드한 Jupyter 노트북과 이 문서로 나뉩니다. 이 문서에서는 다음 단계를 수행합니다.

* HDInsight Spark 클러스터에서 스크립트 동작을 실행하여 Microsoft Cognitive 도구 키트 및 Python 패키지를 설치합니다.
* 솔루션을 실행하는 Jupyter 노트북을 HDInsight Spark 클러스터에 업로드합니다.

다음의 나머지 단계에서는 Jupyter 노트북에서 설명합니다.

* 샘플 이미지를 스파크 복원력 분산 데이터 집합 또는 RDD로 로드합니다.
  * 모듈 로드 및 사전 설정 정의
  * Spark 클러스터에서 로컬로 데이터 세트 다운로드
  * 데이터 세트를 RDD로 변환
* 학습된 Cognitive 도구 키트 모델을 사용하여 이미지 점수 매기기
  * Spark 클러스터에 학습된 Cognitive 도구 키트 모델 다운로드
  * 작업자 노드에서 사용할 함수 정의
  * 작업자 노드에서 이미지 점수 매기기
  * 모델 정확도 평가

## <a name="install-microsoft-cognitive-toolkit"></a>Microsoft Cognitive 도구 키트 설치

스크립트 동작을 사용하여 Spark 클러스터에 Microsoft Cognitive 도구 키트를 설치할 수 있습니다. 스크립트 작업은 사용자 지정 스크립트를 사용하여 기본적으로 사용할 수 없는 구성 요소를 클러스터에 설치합니다. HDInsight .NET SDK를 사용하거나 Azure PowerShell을 사용하여 Azure 포털에서 사용자 지정 스크립트를 사용할 수 있습니다. 또한 스크립트를 사용하여 클러스터 만들기의 일부로 또는 클러스터가 작동하여 실행된 후에 도구 키트를 설치할 수 있습니다.

이 문서에서는 클러스터를 만든 후에 포털을 사용하여 도구 키트를 설치합니다. 사용자 지정 스크립트를 실행하는 다른 방법은 [스크립트 동작을 사용하여 HDInsight 클러스터 사용자 지정](../hdinsight-hadoop-customize-cluster-linux.md)을 참조하세요.

### <a name="using-the-azure-portal"></a>Azure Portal 사용

Azure 포털을 사용하여 스크립트 작업을 실행하는 방법에 대한 지침은 [스크립트 작업을 사용하여 HDInsight 클러스터 사용자 지정을](../hdinsight-hadoop-customize-cluster-linux.md#script-action-during-cluster-creation)참조하세요. Microsoft Cognitive 도구 키트를 설치하려면 다음 입력을 제공해야 합니다. 스크립트 작업에 다음 값을 사용합니다.

|속성 |값 |
|---|---|
|스크립트 유형|- 사용자 지정|
|이름| MCT 설치|
|Bash 스크립트 URI|`https://raw.githubusercontent.com/Azure-Samples/hdinsight-pyspark-cntk-integration/master/cntk-install.sh`|
|노드 유형:|머리, 노동자|
|매개 변수|None|

## <a name="upload-the-jupyter-notebook-to-azure-hdinsight-spark-cluster"></a>Azure HDInsight Spark 클러스터에 Jupyter 노트북 업로드

Azure HDInsight Spark 클러스터에서 Microsoft Cognitive 도구 키트를 사용하려면 Azure HDInsight Spark 클러스터에 **CNTK_model_scoring_on_Spark_walkthrough.ipynb** Jupyter 노트북을 로드해야 합니다. 이 전자 필기장에서 [https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration](https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration)GitHub에서 사용할 수 있습니다.

1. 다운로드 및 [https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration](https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration)압축을 해제합니다.

1. 웹 브라우저에서 클러스터 `https://CLUSTERNAME.azurehdinsight.net/jupyter`의 `CLUSTERNAME` 이름으로 이동합니다.

1. Jupyter 노트북에서 오른쪽 상단 모서리에 **업로드를** 선택한 다음 다운로드로 `CNTK_model_scoring_on_Spark_walkthrough.ipynb`이동하여 파일을 선택합니다.

    ![Azure HDInsight 스파크 클러스터에 주피터 노트북 업로드](./media/apache-spark-microsoft-cognitive-toolkit/hdinsight-microsoft-cognitive-toolkit-load-jupyter-notebook.png "Azure HDInsight 스파크 클러스터에 주피터 노트북 업로드")

1. 다시 **업로드를** 선택합니다.

1. 전자 필기장을 업로드한 후 전자 필기장 이름을 클릭한 다음 데이터 집합을 로드하고 문서를 수행하는 방법에 대한 전자 필기장의 지침을 따릅니다.

## <a name="see-also"></a>참조

* [개요: Azure HDInsight에서 Apache Spark](apache-spark-overview.md)

### <a name="scenarios"></a>시나리오

* [BI와 Apache Spark: BI 도구와 함께 HDInsight의 Spark를 사용하여 대화형 데이터 분석 수행](apache-spark-use-bi-tools.md)
* [Machine Learning과 Apache Spark: HVAC 데이터를 사용하여 건물 온도를 분석하는 데 HDInsight의 Spark 사용](apache-spark-ipython-notebook-machine-learning.md)
* [Machine Learning과 Apache Spark: HDInsight의 Spark를 사용하여 식품 검사 결과 예측](apache-spark-machine-learning-mllib-ipython.md)
* [HDInsight의 Apache Spark를 사용한 웹 사이트 로그 분석](apache-spark-custom-library-website-log-analysis.md)
* [HDInsight의 Apache Spark를 사용한 Application Insight 원격 분석 데이터 분석](apache-spark-analyze-application-insight-logs.md)

### <a name="create-and-run-applications"></a>애플리케이션 만들기 및 실행

* [Scala를 사용하여 독립 실행형 애플리케이션 만들기](apache-spark-create-standalone-application.md)
* [Apache Livy를 사용하여 Apache Spark 클러스터에서 원격으로 작업 실행](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>도구 및 확장

* [IntelliJ IDEA용 HDInsight 도구 플러그 인을 사용하여 Spark Scala 애플리케이션 만들기 및 제출](apache-spark-intellij-tool-plugin.md)
* [IntelliJ IDEA용 HDInsight 도구 플러그 인을 사용하여 Apache Spark 애플리케이션을 원격으로 디버그](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [HDInsight에서 Apache Spark 클러스터와 함께 Apache Zeppelin Notebook 사용](apache-spark-zeppelin-notebook.md)
* [HDInsight의 Apache Spark 클러스터에서 Jupyter Notebook에 사용할 수 있는 커널](apache-spark-jupyter-notebook-kernels.md)
* [Jupyter 노트북에서 외부 패키지 사용](apache-spark-jupyter-notebook-use-external-packages.md)
* [컴퓨터에 Jupyter를 설치하고 HDInsight Spark 클러스터에 연결](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>리소스 관리

* [Azure HDInsight에서 Apache Spark 클러스터에 대한 리소스 관리](apache-spark-resource-manager.md)
* [HDInsight의 Apache Spark 클러스터에서 실행되는 작업 추적 및 디버그](apache-spark-job-debugging.md)
