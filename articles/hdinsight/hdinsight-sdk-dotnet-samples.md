---
title: 'Azure HDInsight: .NET 샘플'
description: .NET용 HDInsight SDK를 사용하는 일반적인 작업을 위해 GitHub에서 C# .NET 예제를 찾습니다.
author: hrasheed-msft
ms.service: hdinsight
ms.topic: sample
ms.date: 04/15/2019
ms.author: hrasheed
ms.openlocfilehash: f589c5e23be105086d2908fb7db72e69c781e726
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/17/2019
ms.locfileid: "59678261"
---
# <a name="azure-hdinsight-net-samples"></a>Azure HDInsight: .NET 샘플

> [!div class="op_single_selector"]
> * [.NET 예제](hdinsight-sdk-dotnet-samples.md)
> * [Python 예제](hdinsight-sdk-python-samples.md)
> * [Java 예제](hdinsight-sdk-java-samples.md)
<!-- * [Go Examples](hdinsight-sdk-go-samples.md)-->

이 문서는 다음을 제공합니다.

* 클러스터 만들기 작업용 샘플에 연결합니다.
* 다른 관리 작업을 위한 참조 콘텐츠에 연결합니다.

[Visual Studio 구독자 혜택을 활성화](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)할 수 있습니다. Visual Studio 구독은 유료 Azure 서비스에 사용할 수 있는 크레딧을 매달 제공합니다.

## <a name="prerequisites"></a>필수 조건

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

- [.NET용 Azure HDInsight SDK](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight#sdk-installation)

## <a name="cluster-management---creation"></a>클러스터 관리 - 만들기

* [Kafka 클러스터 만들기](https://github.com/Azure-Samples/hdinsight-dotnet-sdk-samples/blob/master/Management/Microsoft.Azure.Management.HDInsight.Samples/Microsoft.Azure.Management.HDInsight.Samples/CreateKafkaClusterSample.cs)
* [Spark 클러스터 만들기](https://github.com/Azure-Samples/hdinsight-dotnet-sdk-samples/blob/master/Management/Microsoft.Azure.Management.HDInsight.Samples/Microsoft.Azure.Management.HDInsight.Samples/CreateSparkClusterSample.cs)
* [Azure Data Lake Storage Gen2로 Spark 클러스터 만들기](https://github.com/Azure-Samples/hdinsight-dotnet-sdk-samples/blob/master/Management/Microsoft.Azure.Management.HDInsight.Samples/Microsoft.Azure.Management.HDInsight.Samples/CreateHadoopClusterWithAdlsGen2Sample.cs)
* [ESP(Enterprise Security Package)를 사용하여 Spark 클러스터 만들기](https://github.com/Azure-Samples/hdinsight-dotnet-sdk-samples/blob/master/Management/Microsoft.Azure.Management.HDInsight.Samples/Microsoft.Azure.Management.HDInsight.Samples/CreateEspClusterSample.cs)

[hdinsight-dotnet-sdk-samples](https://github.com/Azure-Samples/hdinsight-dotnet-sdk-samples) GitHub 리포지토리를 복제하여 이러한 .NET용 샘플을 가져올 수 있습니다.

[!INCLUDE [hdinsight-sdk-additional-functionality](../../includes/hdinsight-sdk-additional-functionality.md)]

이 추가 SDK 기능에 대한 코드 조각은 [.NET용 HDInsight SDK 참조 설명서](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight?view=azure-dotnet)에서 찾을 수 있습니다.