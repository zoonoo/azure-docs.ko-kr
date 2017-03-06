---
title: "Azure Data Lake Store 성능 조정 지침 | Microsoft Docs"
description: "Azure Data Lake Store 성능 조정 지침"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: ebde7b9f-2e51-4d43-b7ab-566417221335
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/02/2016
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: af11866fc812cd8a375557b7bf9df5cdc9bba610
ms.openlocfilehash: f0d0c05c08ce198e2702c76ad35b348107c664c7
ms.lasthandoff: 01/18/2017


---
# <a name="performance-tuning-guidance-for-azure-data-lake-store"></a>Azure Data Lake Store에 대한 성능 조정 지침

이 문서에서는 Azure Data Lake Store 성능에서 데이터를 읽거나 쓰는 동안 최적의 성능을 얻는 방법에 대한 지침을 제공합니다. 이 문서는사용자가 데이터 업로드/다운로드 도구 및 데이터 분석 워크로드에 대해 일반적으로 구성될 수 있는 매개 변수를 이해하도록 도와줍니다. 이 가이드의 튜닝은 특히 Data Lake Store를 읽거나 쓰는 대량의 데이터가 있는 리소스 사용량이 많은 워크로드를 대상으로 합니다.

## <a name="prerequisites"></a>필수 조건

* **Azure 구독**. [Azure 무료 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.
* **Azure 데이터 레이크 저장소 계정**. 만드는 방법에 대한 지침은 [Azure 데이터 레이크 저장소 시작](data-lake-store-get-started-portal.md)
* **Azure HDInsight 클러스터** 입니다. [Data Lake Store가 있는 HDInsight 클러스터 만들기](data-lake-store-hdinsight-hadoop-use-portal.md)를 참조하세요. 클러스터에 대한 원격 데스크톱을 사용하도록 설정해야 합니다.


## <a name="guidelines-for-data-ingestion-tools"></a>데이터 수집 도구에 대한 지침

이 섹션에서는 Data Lake Store로 데이터를 복사하거나 이동할 때 성능 향상을 위한 일반적인 지침을 제공합니다. 이 섹션에서는 성능을 제한하는 요인 및 해당 한계를 극복하는 방법을 설명합니다. 다음은 기억해야 할 몇 가지 고려 사항입니다.

* **원본 데이터** - 원본 데이터가 제공되는 위치에서 발생할 수 있는 많은 제약 조건이 있습니다. 원본 데이터가 처리량이 낮은 느린 스핀들 또는 원격 저장소에 있는 경우 처리량은 병목 상태가 될 수 있습니다. SSD(로컬 디스크 권장)는 디스크 처리량이 높기 때문에 최상의 성능을 제공합니다.

* **네트워크** - VM에 원본 데이터가 있는 경우 VM 및 Data Lake Store 사이의 네트워크 연결이 중요합니다. 가장 많이 사용할 수 있는 NIC를 포함한 VM을 사용하여 더 많은 네트워크 대역폭을 가져올 수 있습니다.

* **지역 간 복사** - 예를 들어 미국 중부에 있는 Data Lake Store 계정에 데이터를 쓰기 위해 미국 동부 2의 VM에서 데이터 수집 도구를 실행하는 경우, 지역 간 데이터 I/O에 내재된 대규모 네트워크 비용이 발생합니다. 지역에 걸쳐 데이터를 복사하는 경우에 성능이 저하될 수 있습니다. 대상 Data Lake Store 계정과 동일한 지역의 VM에서 데이터 수집 작업을 사용하여 네트워크 처리량을 최대화하는 것이 좋습니다.                                                                                                                                        

* **클러스터** - HDInsight 클러스터(예: DistCp)를 통해 데이터 수집 작업을 실행하는 경우 클러스터에 더 많은 메모리를 포함하는 D 시리즈 VM을 사용하는 것이 좋습니다. 코어 수가 많으면 처리량을 증가시킬 수 있습니다.                                                                                                                                                                                                                                                                                                            

* **스레드의 동시성** - HDInsight 클러스터를 사용하여 저장소 컨테이너에서 데이터를 복사하는 경우 클러스터 크기, 컨테이너 크기 및 스레드 설정에 따라 사용할 수 있는 병렬 스레드 수는 제한됩니다. Data Lake Store의 성능을 증가시킬 수 있는 중요한 방법 중 하나는 동시성을 늘리는 것입니다. 처리량을 높이기 위해 동시성에 대한 최대량을 얻도록 설정을 조정해야 합니다. 다음 테이블은 더 많은 동시성을 달성하기 위해 구성할 수 있는 각 수집 방법에 대한 설정입니다. 테이블에 있는 링크를 따라 도구를 사용하여 Data Lake Store에 데이터를 수집하는 방법 및 최대 처리량에 대해 도구의 성능을 조정하는 방법에 대해 다루는 문서로 이동합니다.

    | 도구               | 동시성 설정                                                                |
    |--------------------|------------------------------------------------------------------------------------|
    | [PowerShell](data-lake-store-get-started-powershell.md)       | PerFileThreadCount, ConcurrentFileCount |
    | [AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)    | Azure Data Lake Analytics 단위         |
    | [DistCp](data-lake-store-copy-data-wasb-distcp.md)            | -m(mapper)                             |
    | [Azure 데이터 팩터리](../data-factory/data-factory-azure-datalake-connector.md)| parallelCopies                          |
    | [Sqoop](data-lake-store-data-transfer-sql-sqoop.md)           | fs.azure.block.size, -m(mapper)        |


## <a name="guidelines-while-working-with-hdinsight-workloads"></a>HDInsight 워크로드 사용 지침

Data Lake Store의 데이터로 작업하는 분석 워크로드를 실행하는 동안 Data Lake Store의 성능을 극대화하려면 HDInsight 3.5 클러스터 버전을 사용하는 것이 좋습니다. 작업이 I/O 집약적인 경우 성능상의 이유로 특정 매개 변수를 구성할 수 있습니다. 예를 들어 작업이 주로 읽기 또는 쓰기를 구성하는 경우 Azure Data Lake Store 간의 I/O에 대한 동시성이 증가하면 성능도 향상될 수 있습니다.

Azure Data Lake Store는 동시성이 더 큰 경우 성능에 가장 최적화됩니다. I/O 집약적인 작업에 대한 동시성을 높이는 몇 가지 일반적인 방법이 있습니다.

1. **소수의 거대한 YARN 컨테이너보다 여러 개의 계산 YARN 컨테이너 실행** – 컨테이너가 많으면 입력 및 출력 작업의 동시성이 증가하므로 Data Lake Store의 기능을 활용합니다.

    예를 들어 HDInsight 클러스터에 2개의 D3v2 노드가 있다고 가정하겠습니다. 각 D3v2 노드에는 12GB YARN 메모리가 있으므로 2개의 D3v2 컴퓨터에는 24GB의 YARN 메모리가 있습니다. 또한 YARN 컨테이너 크기를 6GB로 설정했다고 가정하겠습니다. 즉, 각각 6GB인 4개의 컨테이너를 사용할 수 있습니다. 따라서 4개의 동시 작업을 병렬로 실행할 수 있습니다. 동시성을 증가시키려면 컨테이너의 크기를 3GB로 줄여서 각각 3GB인 8개의 컨테이너를 사용할 수 있습니다. 그러면 8개의 동시 작업을 병렬로 실행할 수 있습니다. 다음은 그림입니다.

    ![Data Lake Store 성능](./media/data-lake-store-performance-tuning-guidance/image-1.png)

    동시성을 더욱 높이기 위해 24개의 컨테이너를 사용할 수 있도록 컨테이너 크기를 1GB의 메모리로 감소시키지 않는지를 일반적으로 질문합니다. 작업에 3GB의 메모리가 필요한지 또는 1GB이면 충분한지 여부에 따라 다릅니다.  1GB의 메모리만 필요로 하는 컨테이너에서 간단한 작업을 수행하거나 3GB의 메모리를 사용하는 컨테이너에서 복잡한 작업을 수행할 수 있습니다.  컨테이너의 크기를 너무 많이 줄일 경우 메모리 부족 예외가 발생할 수 있습니다.  이 경우에 컨테이너의 크기를 늘려야 합니다.  메모리 외에도 가상 코어 수가 병렬 처리에 영향을 줄 수 있습니다.

    ![Data Lake Store 성능](./media/data-lake-store-performance-tuning-guidance/image-2.png)

2. **클러스터에서 메모리를 증가시켜 동시성 향상** - 클러스터의 크기를 늘리거나 메모리가 더 많은 VM 유형을 선택하여 클러스터의 메모리를 늘릴 수 있습니다. 그러면 더 많은 컨테이너를 만들 수 있도록 사용 가능한 YARN 메모리 양을 증가시켜서 동시성을 향상시킵니다.  

    예를 들어 12GB의 YARN 메모리와 3GB의 컨테이너가 있는 HDInsight 클러스터에 단일 D3v2 노드가 있다고 가정하겠습니다.  2개의 D3v2로 클러스터를 확장하면 YARN 메모리를 24GB로 향상시킵니다.  동시성을 4개에서 8개로 증가시킵니다.

    ![Data Lake Store 성능](./media/data-lake-store-performance-tuning-guidance/image-3.png)

3. **작업 수를 보유한 동시성의 수로 설정하여 시작** – 지금까지 최대 크기의 동시성을 사용하기 위해 컨테이너 크기를 적절하게 설정했습니다. 이제 모든 해당 컨테이너를 사용하도록 작업의 수를 설정해야 합니다. 각 워크로드에서 작업의 이름이 다릅니다.

    작업의 크기를 고려할 수도 있습니다. 작업의 크기가 큰 경우 각 작업에는 처리할 데이터가 많을 수 있습니다. 하나의 작업에서 너무 많은 데이터 처리하지 않도록 더 많은 작업을 사용할 수 있습니다.

    예를 들어 6개의 컨테이너가 있다고 가정하겠습니다. 시작 지점으로 작업을 6으로 설정해야 합니다. 성능을 개선시키는지 확인하기 위해 작업 수를 높게 하여 실험해 볼 수 있습니다. 작업 수를 높게 설정하더라도 동시성이 증가하지 않습니다. 작업을 6보다 높게 설정하면 다음 웨이브까지 작업이 실행되지 않습니다. 작업을 6보다 낮게 설정하는 경우 동시성은 완벽하게 사용되지 않습니다.

    각 워크로드에는 작업의 수를 설정할 수 있는 다른 매개 변수가 있습니다. 아래 테이블은 그 중 일부를 나열합니다.

    | 워크로드               | 작업을 설정하는 매개 변수                                                         |
    |--------------------|------------------------------------------------------------------------------------|
    | [HDInisight의 Spark](data-lake-store-performance-tuning-spark.md)       | <ul><li>Num-executors</li><li>Executor-memory</li><li>Executor-cores</li></ul> |
    | [HDInsight의 Hive](data-lake-store-performance-tuning-hive.md)    | hive.tez.container.size         |
    | [HDInsight의 MapReduce](data-lake-store-performance-tuning-mapreduce.md)            | <ul><li>Mapreduce.map.memory</li><li>Mapreduce.job.maps</li><li>Mapreduce.reduce.memory</li><li>Mapreduce.job.reduces</li></ul> |
    | [HDInsight의 Storm](data-lake-store-performance-tuning-storm.md)| <ul><li>작업자 프로세스 수</li><li>Spout 실행자 인스턴스 수</li><li>Bolt 실행자 인스턴스 수 </li><li>Spout 작업 수</li><li>Bolt 작업 수</li></ul>|

## <a name="see-also"></a>참고 항목
* [Azure 데이터 레이크 저장소 개요](data-lake-store-overview.md)
* [Azure 데이터 레이크 분석 시작](../data-lake-analytics/data-lake-analytics-get-started-portal.md)

