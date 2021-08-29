---
title: 'Azure HDInsight: Java 샘플'
description: Java용 HDInsight SDK를 사용하는 일반적인 작업을 위해 GitHub에서 Java 예제를 찾습니다.
ms.custom: devx-track-java
ms.service: hdinsight
ms.topic: sample
ms.date: 11/29/2019
ms.openlocfilehash: 80b30f295254e9d83ad97542381ec7b47e0f094a
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/17/2021
ms.locfileid: "122643552"
---
# <a name="azure-hdinsight-java-samples"></a>Azure HDInsight: Java 샘플

> [!div class="op_single_selector"]
> * [Java 예제](hdinsight-sdk-java-samples.md)
> * [.NET 예제](hdinsight-sdk-dotnet-samples.md)
> * [Python 예제](hdinsight-sdk-python-samples.md)
<!-- * [Go Examples](hdinsight-sdk-dotnet-samples.md)-->

이 문서에서는 다음을 제공합니다.

* 클러스터 만들기 작업용 샘플에 연결합니다.
* 다른 관리 작업을 위한 참조 콘텐츠에 연결합니다.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>필수 구성 요소

[Java용 Azure HDInsight SDK](/java/api/overview/azure/hdinsight#sdk-installation)

## <a name="cluster-management---creation"></a>클러스터 관리 - 만들기

* [Kafka 클러스터 만들기](https://github.com/Azure-Samples/hdinsight-java-sdk-samples/blob/master/management/src/main/java/com/microsoft/azure/hdinsight/samples/CreateKafkaClusterSample.java)
* [Spark 클러스터 만들기](https://github.com/Azure-Samples/hdinsight-java-sdk-samples/blob/master/management/src/main/java/com/microsoft/azure/hdinsight/samples/CreateSparkClusterSample.java)
* [Azure Data Lake Storage Gen2로 Spark 클러스터 만들기](https://github.com/Azure-Samples/hdinsight-java-sdk-samples/blob/master/management/src/main/java/com/microsoft/azure/hdinsight/samples/CreateHadoopClusterWithAdlsGen2Sample.java)
* [ESP(Enterprise Security Package)를 사용하여 Spark 클러스터 만들기](https://github.com/Azure-Samples/hdinsight-java-sdk-samples/blob/master/management/src/main/java/com/microsoft/azure/hdinsight/samples/CreateEspClusterSample.java)

[hdinsight-java-sdk-samples](https://github.com/Azure-Samples/hdinsight-java-sdk-samples) GitHub 리포지토리를 복제하여 이러한 Java용 샘플을 가져올 수 있습니다.

[!INCLUDE [hdinsight-sdk-additional-functionality](includes/hdinsight-sdk-additional-functionality.md)]

이 추가 SDK 기능에 대한 코드 조각은 [Java용 HDInsight SDK 참조 설명서](/java/api/overview/azure/hdinsight)에서 찾을 수 있습니다.