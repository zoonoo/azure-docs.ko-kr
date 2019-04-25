---
title: 'Azure HDInsight: Java 샘플'
description: Java용 HDInsight SDK를 사용하는 일반적인 작업을 위해 GitHub에서 Java 예제를 찾습니다.
author: hrasheed-msft
ms.service: hdinsight
ms.topic: sample
ms.date: 04/15/2019
ms.author: hrasheed
ms.openlocfilehash: 971af370425f733649f0b8d0079baaf93cc72129
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/17/2019
ms.locfileid: "59681542"
---
# <a name="azure-hdinsight-java-samples"></a>Azure HDInsight: Java 샘플

> [!div class="op_single_selector"]
> * [Java 예제](hdinsight-sdk-java-samples.md)
> * [.NET 예제](hdinsight-sdk-dotnet-samples.md)
> * [Python 예제](hdinsight-sdk-python-samples.md)
<!-- * [Go Examples](hdinsight-sdk-dotnet-samples.md)-->

이 문서는 다음을 제공합니다.

* 클러스터 만들기 작업용 샘플에 연결합니다.
* 다른 관리 작업을 위한 참조 콘텐츠에 연결합니다.

## <a name="prerequisites"></a>필수 조건

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

- [Java용 Azure HDInsight SDK](https://docs.microsoft.com/java/api/overview/azure/hdinsight#sdk-installation)

## <a name="cluster-management---creation"></a>클러스터 관리 - 만들기

* [Kafka 클러스터 만들기](https://github.com/Azure-Samples/hdinsight-java-sdk-samples/blob/master/management/src/main/java/com/microsoft/azure/hdinsight/samples/CreateKafkaClusterSample.java)
* [Spark 클러스터 만들기](https://github.com/Azure-Samples/hdinsight-java-sdk-samples/blob/master/management/src/main/java/com/microsoft/azure/hdinsight/samples/CreateSparkClusterSample.java)
* [Azure Data Lake Storage Gen2로 Spark 클러스터 만들기](https://github.com/Azure-Samples/hdinsight-java-sdk-samples/blob/master/management/src/main/java/com/microsoft/azure/hdinsight/samples/CreateHadoopClusterWithAdlsGen2Sample.java)
* [ESP(Enterprise Security Package)를 사용하여 Spark 클러스터 만들기](https://github.com/Azure-Samples/hdinsight-java-sdk-samples/blob/master/management/src/main/java/com/microsoft/azure/hdinsight/samples/CreateEspClusterSample.java)

[hdinsight-java-sdk-samples](https://github.com/Azure-Samples/hdinsight-java-sdk-samples) GitHub 리포지토리를 복제하여 이러한 Java용 샘플을 가져올 수 있습니다.

[!INCLUDE [hdinsight-sdk-additional-functionality](../../includes/hdinsight-sdk-additional-functionality.md)]

이 추가 SDK 기능에 대한 코드 조각은 [Java용 HDInsight SDK 참조 설명서](https://docs.microsoft.com/java/api/overview/azure/hdinsight?view=azure-java-preview)에서 찾을 수 있습니다.