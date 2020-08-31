---
title: 'Azure HDInsight: Python 샘플'
description: Python용 HDInsight SDK를 사용하는 일반적인 작업을 위해 GitHub에서 Python 예제를 찾습니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: sample
ms.date: 11/08/2019
ms.custom: devx-track-python
ms.openlocfilehash: 8aa2644f86f5c26fa861aba8c928e79b7bc58a6c
ms.sourcegitcommit: dea88d5e28bd4bbd55f5303d7d58785fad5a341d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/06/2020
ms.locfileid: "87873197"
---
# <a name="azure-hdinsight-python-samples"></a>Azure HDInsight: Python 샘플

> [!div class="op_single_selector"]
> * [Python 예제](hdinsight-sdk-python-samples.md)
> * [.NET 예제](hdinsight-sdk-dotnet-samples.md)
> * [Java 예제](hdinsight-sdk-java-samples.md)
<!-- * [Go Examples](hdinsight-sdk-go-samples.md)-->

> [!Important]
> Python 2.7은 2020년 1월 1일부터 사용되지 않습니다. 여전히 Python 2.7을 사용하는 경우 HDInsight Python SDK를 사용하려면 3.7로 업그레이드하세요.  

이 문서는 다음을 제공합니다.

* 클러스터 만들기 작업용 샘플에 연결합니다.
* 다른 관리 작업을 위한 참조 콘텐츠에 연결합니다.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>사전 요구 사항

[Python용 Azure HDInsight SDK](https://docs.microsoft.com/python/api/overview/azure/hdinsight#sdk-installation)

## <a name="cluster-management---creation"></a>클러스터 관리 - 만들기

* [Apache Kafka 클러스터 만들기](https://github.com/Azure-Samples/hdinsight-python-sdk-samples/blob/master/samples/create_kafka_cluster_sample.py)
* [Apache Spark 클러스터 만들기](https://github.com/Azure-Samples/hdinsight-python-sdk-samples/blob/master/samples/create_spark_cluster_sample.py)
* [Azure Data Lake Storage Gen2로 Apache Spark 클러스터 만들기](https://github.com/Azure-Samples/hdinsight-python-sdk-samples/blob/master/samples/create_hadoop_cluster_with_adls_gen2_sample.py)
* [ESP(Enterprise Security Package)를 사용하여 Apache Spark 클러스터 만들기](https://github.com/Azure-Samples/hdinsight-python-sdk-samples/blob/master/samples/create_esp_cluster_sample.py)

[hdinsight-python-sdk-samples](https://github.com/Azure-Samples/hdinsight-python-sdk-samples) GitHub 리포지토리를 복제를 통해 Python용 샘플을 가져올 수 있습니다.

[!INCLUDE [hdinsight-sdk-additional-functionality](../../includes/hdinsight-sdk-additional-functionality.md)]

이 추가 SDK 기능에 대한 코드 조각은 [Python용 HDInsight SDK 참조 설명서](https://docs.microsoft.com/python/api/overview/azure/hdinsight?view=azure-python)에서 찾을 수 있습니다.
