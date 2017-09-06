---
title: "Azure HDInsight의 Spark 소개 | Microsoft Docs"
description: "이 문서에서는 HDInsight의 Spark를 소개하고 HDInsight에서 Spark 클러스터를 사용할 수 있는 다양한 시나리오를 제공합니다."
keywords: "Apache Spark란?, Spark 클러스터, Spark 소개, HDInsight의 Spark"
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 82334b9e-4629-4005-8147-19f875c8774e
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/28/2017
ms.author: nitinme
ms.translationtype: HT
ms.sourcegitcommit: a0b98d400db31e9bb85611b3029616cc7b2b4b3f
ms.openlocfilehash: b8955acc83b0fbb0612e7042d62170ae8078b9ad
ms.contentlocale: ko-kr
ms.lasthandoff: 08/29/2017

---
# <a name="introduction-to-spark-on-hdinsight"></a>HDInsight의 Spark 소개

이 문서에서는 HDInsight의 Spark를 소개합니다. <a href="http://spark.apache.org/" target="_blank">Apache Spark</a>는 메모리 내 처리를 지원하여 빅데이터 분석 응용 프로그램의 성능을 향상하는 오픈 소스 병렬 처리 프레임워크입니다. HDInsight의 Spark 클러스터는 Azure Data Lake Store뿐만 아니라 Azure Storage(WASB)와도 호환됩니다. 따라서 Azure에 저장된 기존 데이터는 Spark 클러스터를 통해 쉽게 처리할 수 있습니다.

HDInsight의 Spark 클러스터를 만들면 Spark가 설치되고 구성된 Azure 계산 리소스를 만듭니다. HDInsight에서 Spark 클러스터를 약 10분만에 만들 수 있습니다. 처리되는 데이터는 Azure Storage 또는 Azure Data Lake Store에 저장됩니다. [HDInsight에서 Azure Storage 사용](hdinsight-hadoop-use-blob-storage.md)을 참조하세요.

**HDInsight의 Spark 클러스터를 만들려면** [빠른 시작: HDInsight에서 Spark 클러스터 만들기 및 Jupyter를 사용하여 대화형 쿼리 실행](hdinsight-apache-spark-jupyter-spark-sql.md)을 참조하세요.


## <a name="what-is-apache-spark-on-azure-hdinsight"></a>Azure HDInsight의 Apache Spark란?
HDInsight의 Spark 클러스터는 완벽하게 관리되는 Spark 서비스를 제공합니다. HDInsight의 Spark 클러스터를 만드는 이점은 다음과 같습니다.

| 기능 | 설명 |
| --- | --- |
| 손쉬운 Spark 클러스터 만들기 |Azure Portal, Azure PowerShell 또는 HDInsight .NET SDK를 사용하여 몇 분 만에 HDInsight에 새 Spark 클러스터를 만들 수 있습니다. [HDInsight에서 Spark 클러스터 시작](hdinsight-apache-spark-jupyter-spark-sql.md) |
| 사용 편의성 |HDInsight의 Spark 클러스터에는 Jupyter 및 Zeppelin 노트북이 포함되어 있습니다. 이러한 노트북을 대화형 데이터 처리 및 시각화에 사용할 수 있습니다.|
| REST API |HDInsight의 Spark 클러스터에는 원격으로 작업을 제출하고 모니터링하는 REST API 기반 Spark 작업 서버인 [Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server)가 포함되어 있습니다. |
| Azure Data Lake 저장소에 대한 지원 | HDInsight의 Spark 클러스터는 Azure Data Lake Store를 기본 저장소(HDInsight 3.5 클러스터에서만)뿐만 아니라 추가 저장소로 사용하도록 구성할 수 있습니다. Data Lake 저장소에 대한 자세한 내용은 [Azure Data Lake 저장소 개요](../data-lake-store/data-lake-store-overview.md)를 참조하세요. |
| Azure 서비스와의 통합 |HDInsight의 Spark 클러스터는 Azure Event Hubs에 대한 커넥터와 함께 제공됩니다. 고객은 [Kafka](http://kafka.apache.org/)외에, 이미 Spark에서 제공하는 이벤트 허브를 사용하여 스트리밍 응용 프로그램을 빌드할 수 있습니다. |
| R 서버에 대한 지원 | Spark 클러스터와 약속된 속도로 분산된 R 계산을 실행하도록 HDInsight Spark 클러스터에서 R 서버를 설정할 수 있습니다. 자세한 내용은 [HDInsight에서 R 서버 시작](hdinsight-hadoop-r-server-get-started.md)을 참조하세요. |
| 타사 IDE와의 통합 | HDInsight는 IntelliJ IDEA 및 Eclipse와 같은 IDE용 플러그 인을 제공하여 HDInsight Spark 클러스터에 응용 프로그램을 만들고 제출할 수 있습니다. 자세한 내용은 [IntelliJ IDEA용 Azure 도구 키트 사용](hdinsight-apache-spark-intellij-tool-plugin.md) 및 [Eclipse용 Azure 도구 키트 사용](hdinsight-apache-spark-eclipse-tool-plugin.md)을 참조하세요.|
| 동시 쿼리 |HDInsight의 Spark 클러스터는 동시 쿼리를 지원합니다. 이를 통해 한 사용자의 여러 개 쿼리 또는 여러 사용자 및 응용 프로그램의 여러 개 쿼리에서 동일한 클러스터 리소스를 공유할 수 있습니다. |
| SSD에서 캐시 |클러스터 노드에 연결된 메모리 또는 SSD에서 데이터를 캐시하도록 선택할 수 있습니다. 메모리에서 캐시하면 최고의 쿼리 성능을 제공하지만 리소스가 많이 들 수 있습니다. SSD에서 캐시하면 메모리에서 전체 데이터 집합에 맞게 필요한 크기의 클러스터를 만들 필요 없이 쿼리 성능 향상을 위한 훌륭한 옵션을 제공합니다. |
| BI 도구와의 통합 |HDInsight의 Spark 클러스터는 데이터 분석을 위해 [Power BI](http://www.powerbi.com/) 및 [Tableau](http://www.tableau.com/products/desktop)와 같은 BI 도구용 커넥터를 제공합니다. |
| 미리 로드된 Anaconda 라이브러리 |HDInsight에서 Spark 클러스터는 미리 설치된 Anaconda 라이브러리와 함께 제공됩니다. [Anaconda](http://docs.continuum.io/anaconda/) 는 기계 학습, 데이터 분석, 시각화 등을 위해 약 200개의 라이브러리를 제공합니다. |
| 확장성 |만드는 동안 클러스터의 노드 수를 지정할 수 있지만 작업과 일치하도록 클러스터를 확장하거나 축소할 수도 있습니다. 모든 HDInsight 클러스터를 통해 클러스터의 노드 수를 변경할 수 있습니다. 또한 모든 데이터가 Azure Storage 또는 Data Lake Store에 저장되므로 데이터 손실 없이 Spark 클러스터를 삭제할 수 있습니다. |
| 24/7 지원 |HDInsight의 Spark 클러스터는 24/7 엔터프라이즈 수준 지원 및 99.9% 가동 시간 SLA와 함께 제공됩니다. |

## <a name="what-are-the-use-cases-for-spark-on-hdinsight"></a>HDInsight에서 Spark에 대한 사용 사례
HDInsight의 Spark 클러스터는 다음과 같은 주요 시나리오를 사용합니다.

### <a name="interactive-data-analysis-and-bi"></a>대화형 데이터 분석 및 BI
[자습서 살펴보기](hdinsight-apache-spark-use-bi-tools.md)

HDInsight의 Apache Spark는 Azure Storage 또는 Azure Data Lake Store에 데이터를 저장합니다. 비즈니스 전문가 및 주요 의사 결정권자는 해당 데이터에 대한 보고서를 분석 및 작성하고 Microsoft Power BI를 사용하여 분석된 데이터에서 대화형 보고서를 작성합니다. 분석자는 클러스터 저장소의 비구조적/반구조적 데이터부터 시작하여 노트북으로 데이터에 대한 스키마를 정의한 다음 Microsoft Power BI를 사용하여 데이터 모델을 작성할 수 있습니다. 또한 HDInsight의 Spark 클러스터는 데이터 분석자, 비즈니스 전문가 및 주요 의사 결정권자를 위한 이상적인 플랫폼을 만드는 Tableau와 같은 여러 타사 BI 도구를 지원합니다.

### <a name="spark-machine-learning"></a>Spark 기계 학습
[자습서 살펴보기: HVAC 데이터를 사용하여 건물 온도 예측](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

[자습서 살펴보기: 음식 검사 결과 예측](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

Apache Spark는 [MLlib](http://spark.apache.org/mllib/), 즉 Spark를 기반으로 하여 빌드되어 HDInsight의 Spark 클러스터에서 사용할 수 있는 기계 학습 라이브러리와 함께 제공됩니다. HDInsight의 Spark 클러스터에는 기계 학습을 위한 다양한 패키지가 포함된 Python 배포인 Anaconda도 있습니다. Jupyter 및 Zeppelin 노트북이 기본 제공되는 지원 기능과 결합하고, 기계 학습 응용 프로그램을 만들기 위한 최고의 환경을 제공합니다.

### <a name="spark-streaming-and-real-time-data-analysis"></a>Spark 스트리밍 및 실시간 데이터 분석
[자습서 살펴보기](hdinsight-apache-spark-eventhub-streaming.md)

HDInsight의 Spark 클러스터는 실시간 분석 솔루션을 빌드하기 위한 풍부한 지원을 제공합니다. Spark가 이미 커넥터를 가지고 Kafka, Flume, Twitter, ZeroMQ 또는 TCP 소켓 같은 여러 소스에서 데이터를 수집하는 동안 HDInsight에서 Spark는 Azure 이벤트 허브에서 데이터 수집을 위한 최상의 지원을 추가합니다. 이벤트 허브는 Azure에서 가장 널리 사용되는 큐 서비스입니다. Event Hubs에 대한 즉각적인 지원을 통해 HDInsight의 Spark 클러스터는 실시간 분석 파이프라인을 빌드하기 위한 이상적인 플랫폼이 됩니다.

## <a name="next-steps"></a>Spark 클러스터의 일부로 포함된 구성 요소
HDInsight의 Spark 클러스터에는 기본적으로 클러스터에서 사용할 수 있는 다음 구성 요소가 포함되어 있습니다.

* [Spark Core](https://spark.apache.org/docs/1.5.1/). Spark Core, Spark SQL, Spark 스트리밍 API, GraphX 및 MLlib가 포함됩니다.
* [Anaconda](http://docs.continuum.io/anaconda/)
* [Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server)
* [Jupyter Notebook](https://jupyter.org)
* [Zeppelin Notebook](http://zeppelin-project.org/)

또한 HDInsight의 Spark 클러스터는 Microsoft Power BI 및 Tableau와 같은 BI 도구에서 HDInsight의 Spark 클러스터에 연결하기 위한 [ODBC 드라이버](http://go.microsoft.com/fwlink/?LinkId=616229)도 제공합니다.

## <a name="where-do-i-start"></a>시작 단계
HDInsight의 Spark 클러스터를 만드는 것으로 시작합니다. [빠른 시작: HDInsight Linux에서 Spark 클러스터 만들기 및 Jupyter를 사용하여 대화형 쿼리 실행](hdinsight-apache-spark-jupyter-spark-sql.md).을 참조하세요. 

## <a name="next-steps"></a>다음 단계
### <a name="scenarios"></a>시나리오
* [BI와 Spark: BI 도구와 함께 HDInsight에서 Spark를 사용하여 대화형 데이터 분석 수행](hdinsight-apache-spark-use-bi-tools.md)
* [기계 학습과 Spark: HVAC 데이터를 사용하여 건물 온도를 분석하는 데 HDInsight의 Spark 사용](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [기계 학습과 Spark: 음식 검사 결과를 예측하는 데 HDInsight의 Spark 사용](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Spark 스트리밍: HDInsight에서 Spark를 사용하여 실시간 스트리밍 응용 프로그램 빌드](hdinsight-apache-spark-eventhub-streaming.md)
* [HDInsight의 Spark를 사용하여 웹 사이트 로그 분석](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>응용 프로그램 만들기 및 실행
* [Scala를 사용하여 독립 실행형 응용 프로그램 만들기](hdinsight-apache-spark-create-standalone-application.md)
* [Livy를 사용하여 Spark 클러스터에서 원격으로 작업 실행](hdinsight-apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>도구 및 확장
* [IntelliJ IDEA용 HDInsight 도구 플러그 인을 사용하여 Spark Scala 응용 프로그램 만들기 및 제출](hdinsight-apache-spark-intellij-tool-plugin.md)
* [IntelliJ IDEA용 HDInsight 도구 플러그 인을 사용하여 Spark 응용 프로그램을 원격으로 디버그](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [HDInsight에서 Spark 클러스터와 함께 Zeppelin Notebook 사용](hdinsight-apache-spark-zeppelin-notebook.md)
* [HDInsight의 Spark 클러스터에서 Jupyter Notebook에 사용할 수 있는 커널](hdinsight-apache-spark-jupyter-notebook-kernels.md)
* [Jupyter 노트북에서 외부 패키지 사용](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)
* [컴퓨터에 Jupyter를 설치하고 HDInsight Spark 클러스터에 연결](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>리소스 관리
* [Azure HDInsight에서 Apache Spark 클러스터에 대한 리소스 관리](hdinsight-apache-spark-resource-manager.md)
* [HDInsight의 Apache Spark 클러스터에서 실행되는 작업 추적 및 디버그](hdinsight-apache-spark-job-debugging.md)
* [Azure HDInsight의 Apache Spark에 대해 알려진 문제](hdinsight-apache-spark-known-issues.md)

