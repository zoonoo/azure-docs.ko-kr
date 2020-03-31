---
title: Azure 데이터 레이크 스토리지 Gen1 - 성능 조정
description: 성능에 대 한 Azure 데이터 호수 저장소 Gen1을 조정 하는 방법에 대해 설명 합니다.
author: stewu
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 06/30/2017
ms.author: stewu
ms.openlocfilehash: 2521700e0f07691541ee6cbbf085a8be72f08129
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73904619"
---
# <a name="tune-azure-data-lake-storage-gen1-for-performance"></a>성능을 위해 Azure 데이터 레이크 스토리지 Gen1 조정

데이터 레이크 스토리지 Gen1은 I/O 집약적인 분석 및 데이터 이동을 위한 높은 처리량을 지원합니다. Data Lake Storage Gen1에서는 사용 가능한 모든 처리량(초당 읽거나 쓸 수 있는 데이터 양)을 이용하여 최상의 성능을 얻는 것이 중요합니다. 이를 위해 최대한 많은 읽기와 쓰기를 병렬로 수행합니다.

![Data Lake Storage Gen1 성능](./media/data-lake-store-performance-tuning-guidance/throughput.png)

Data Lake Storage Gen1은 모든 분석 시나리오에 필요한 처리량을 제공하도록 크기가 조정될 수 있습니다. 기본적으로 Data Lake Storage Gen1 계정은 다양한 사용 사례 범주의 요구를 충족하기에 충분한 처리량을 자동으로 제공합니다. 고객이 기본 제한에 걸리는 경우 Microsoft 지원에 문의하여 더 많은 처리량을 제공하도록 Data Lake Storage Gen1 계정을 구성할 수 있습니다.

## <a name="data-ingestion"></a>데이터 수집

원본 시스템에서 데이터 레이크 스토리지 Gen1로 데이터를 수집할 때 원본 하드웨어, 소스 네트워크 하드웨어 및 Data Lake Storage Gen1에 대한 네트워크 연결이 병목 현상이 될 수 있음을 고려해야 합니다.

![Data Lake Storage Gen1 성능](./media/data-lake-store-performance-tuning-guidance/bottleneck.png)

데이터 이동이 이러한 요인의 영향을 받지 않도록 하는 것이 중요합니다.

### <a name="source-hardware"></a>원본 하드웨어

Azure에서 온-프레미스 컴퓨터 또는 VM을 사용하든 적절한 하드웨어를 신중하게 선택해야 합니다. 원본 디스크 하드웨어의 경우 HDD보다 SSD를 사용하고, 스핀들이 더 빠른 디스크 하드웨어를 선택합니다. 원본 네트워크 하드웨어의 경우 가능한 가장 빠른 NIC를 사용합니다. Azure에서는 적절히 강력한 디스크 및 네트워킹 하드웨어가 있는 Azure D14 VM을 권장합니다.

### <a name="network-connectivity-to-data-lake-storage-gen1"></a>데이터 레이크 스토리지 Gen1에 대한 네트워크 연결

원본 데이터와 Data Lake Storage Gen1 간의 네트워크 연결에서 병목 상태가 발생하는 경우도 있습니다. 원본 데이터가 온-프레미스인 경우 [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/)와 함께 전용 링크를 사용하는 것이 좋습니다. 원본 데이터가 Azure에 있는 경우 데이터가 Data Lake Storage Gen1 계정과 동일한 Azure 지역에 있을 때 성능이 가장 좋습니다.

### <a name="configure-data-ingestion-tools-for-maximum-parallelization"></a>최대 병렬 처리를 위한 데이터 수집 도구 구성

원본 하드웨어 및 네트워크 연결 병목 현상을 해결한 후에는 인베이션 도구를 구성할 준비가 된 것입니다. 다음 표에는 몇 가지 일반적인 수집 도구에 대한 주요 설정이 요약되어 있으며, 각 도구에 대한 심층 분석 성능 튜닝 문서가 제공됩니다. 시나리오에 사용할 도구에 대한 자세한 내용은 이 [문서](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-data-scenarios)를 참조하세요.

| 도구          | 설정 | 자세한 정보                                                                 |
|--------------------|------------------------------------------------------|------------------------------|
| PowerShell       | PerFileThreadCount, ConcurrentFileCount | [링크](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-powershell) |
| AdlCopy    | Azure Data Lake Analytics 단위 | [링크](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob#performance-considerations-for-using-adlcopy)         |
| DistCp            | -m(mapper) | [링크](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-wasb-distcp#performance-considerations-while-using-distcp)                             |
| Azure 데이터 팩터리| parallelCopies | [링크](../data-factory/copy-activity-performance.md)                          |
| Sqoop           | fs.azure.block.size, -m(mapper) | [링크](https://blogs.msdn.microsoft.com/bigdatasupport/2015/02/17/sqoop-job-performance-tuning-in-hdinsight-hadoop/)        |

## <a name="structure-your-data-set"></a>데이터 집합 구성

데이터가 Data Lake Storage Gen1에 저장되면 파일 크기, 파일 수 및 폴더 구조가 성능에 영향을 미칩니다. 다음 섹션에서는 이러한 영역의 모범 사례에 대해 설명합니다.

### <a name="file-size"></a>파일 크기

일반적으로 HDInsight, Azure Data Lake Analytics 등의 분석 엔진에서는 파일당 오버헤드가 발생합니다. 데이터를 많은 작은 파일로 저장하는 경우 성능이 저하될 수 있습니다.

일반적으로 성능을 향상하려면 데이터를 더 큰 파일로 구성합니다. 일반적으로 256MB 이상 파일로 데이터 집합을 구성합니다. 이미지, 이진 데이터 등과 같이 병렬로 처리할 수 없는 경우도 있습니다. 이러한 경우 개별 파일을 2GB 미만으로 유지하는 것이 좋습니다.

경우에 따라 데이터 파이프라인에는 작은 파일이 많은 원시 데이터에 대한 제어가 제한되는 경우가 있습니다. 다운스트림 애플리케이션에 사용할 더 큰 파일을 생성하는 "처리" 프로세스를 포함하는 것이 좋습니다.

### <a name="organize-time-series-data-in-folders"></a>폴더에서 시간계 데이터 구성

Hive 및 ADLA 워크로드의 경우 시간계 데이터의 파티션 전정은 일부 쿼리가 데이터의 하위 집합만 읽는 데 도움이 되어 성능을 향상시킬 수 있습니다.

시간계 데이터를 수집하는 파이프라인은 종종 파일 및 폴더에 대한 구조화 된 이름으로 파일을 배치합니다. 다음은 날짜별로 구조화된 데이터에 대해 표시되는 일반적인 예입니다.

    \DataSet\YYYY\MM\DD\datafile_YYYY_MM_DD.tsv

datetime 정보가 폴더 및 파일 이름 둘 다에 나타납니다.

날짜 및 시간의 일반적인 패턴은 다음과 같습니다.

    \DataSet\YYYY\MM\DD\HH\mm\datafile_YYYY_MM_DD_HH_mm.tsv

앞에서 설명했듯이, 더 큰 파일 크기와 각 폴더에 포함된 적절한 파일 수의 요건에 맞는 폴더 및 파일 구성을 선택해야 합니다.

## <a name="optimize-io-intensive-jobs-on-hadoop-and-spark-workloads-on-hdinsight"></a>HDInsight에서 하두롭 및 스파크 워크로드에서 I/O 집약적 작업 최적화

작업은 다음 세 가지 범주 중 하나에 속합니다.

* **CPU 집약적.** 이러한 작업은 최소한의 I/O 시간으로 계산 시간이 깁니다. 기계 학습 및 자연어 처리 작업을 예로 들 수 있습니다.
* **메모리 집약적.** 이러한 작업은 메모리를 많이 사용합니다. PageRank 및 실시간 분석 작업을 예로 들 수 있습니다.
* **I/O 집약적.** 이러한 작업은 I/O를 수행하는 데 대부분의 시간을 사용합니다. 일반적인 예로는 읽기 및 쓰기 작업만 수행하는 복사 작업이 있습니다. 다른 예로는 수많은 데이터를 읽고 일부 데이터 변환을 수행한 다음 데이터를 저장소에 다시 기록하는 데이터 준비 작업이 있습니다.

다음 지침은 I/O 집약적 작업에만 적용됩니다.

### <a name="general-considerations-for-an-hdinsight-cluster"></a>HDInsight 클러스터에 대한 일반적인 고려 사항

* **HDInsight 버전.** 최상의 성능을 얻으려면 HDInsight의 최신 릴리스를 사용합니다.
* **지역.** HDInsight 클러스터와 동일한 지역에 Data Lake Storage Gen1 계정을 배치합니다.

HDInsight 클러스터는 헤드 노드 두 개와 일부 작업자 노드로 구성됩니다. 각 작업자 노드는 VM 유형에 의해 결정되는 특정 개수의 코어와 메모리를 제공합니다. 작업을 실행할 때 YARN은 사용 가능한 메모리와 코어를 할당하여 컨테이너를 만드는 리소스 협상자입니다. 각 컨테이너는 작업을 완료하는 데 필요한 태스크를 실행합니다. 태스크를 신속하게 처리하기 위해 컨테이너가 병렬로 실행됩니다. 따라서 최대한 많은 병렬 컨테이너를 실행하여 성능을 향상합니다.

HDInsight 클러스터 내에 있는 3개의 계층을 튜닝하여 컨테이너 수를 늘리고 사용 가능한 모든 처리량을 이용할 수 있습니다.

* **물리적 계층**
* **YARN 계층**
* **워크로드 계층**

### <a name="physical-layer"></a>물리적 계층

**더 많은 노드 및/또는 더 큰 VM으로 클러스터를 실행합니다.** 더 큰 클러스터를 사용하면 아래 그림과 같이 더 많은 YARN 컨테이너를 실행할 수 있습니다.

![Data Lake Storage Gen1 성능](./media/data-lake-store-performance-tuning-guidance/VM.png)

**더 많은 네트워크 대역폭을 가진 VM을 사용합니다.** 네트워크 대역폭이 Data Lake Storage Gen1 처리량보다 작을 경우 네트워크 대역폭 크기로 인해 병목 상태가 발생할 수 있습니다. VM마다 각기 다른 네트워크 대역폭 크기를 갖게 됩니다. 가능한 가장 큰 네트워크 대역폭을 가진 VM 유형을 선택합니다.

### <a name="yarn-layer"></a>YARN 계층

**더 작은 YARN 컨테이너를 사용합니다.** 각 YARN 컨테이너의 크기를 줄여 동일한 리소스 양으로 더 많은 컨테이너를 만듭니다.

![Data Lake Storage Gen1 성능](./media/data-lake-store-performance-tuning-guidance/small-containers.png)

워크로드에 따라 항상 필요한 최소 YARN 컨테이너 크기가 있습니다. 너무 작은 컨테이너를 선택하면 작업에서 메모리 부족 문제가 발생합니다. 일반적으로 YARN 용기는 1GB 보다 작아야 합니다. 3GB YARN 용기를 보는 것이 일반적입니다. 일부 워크로드의 경우 더 큰 YARN 컨테이너가 필요할 수도 있습니다.

**YARN 컨테이너당 코어 수를 늘립니다.** 각 컨테이너에 할당된 코어 수를 늘려 각 컨테이너에서 실행되는 병렬 태스크 수를 늘립니다. 이는 컨테이너당 여러 작업을 실행하는 Spark와 같은 응용 프로그램에 적합합니다. 각 컨테이너에서 단일 스레드를 실행하는 Hive와 같은 응용 프로그램의 경우 컨테이너당 더 많은 코어보다 더 많은 컨테이너를 두는 것이 좋습니다.

### <a name="workload-layer"></a>워크로드 계층

**사용 가능한 모든 컨테이너를 이용합니다.** 모든 리소스가 사용되도록 작업 수를 사용 가능한 컨테이너 수보다 같거나 크게 설정합니다.

![Data Lake Storage Gen1 성능](./media/data-lake-store-performance-tuning-guidance/use-containers.png)

**실패한 태스크는 비용이 많이 듭니다.** 각 태스크에서 많은 양의 데이터를 처리하는 경우 태스크 실패 시 다시 시도하는 데 많은 비용이 듭니다. 따라서 각각 적은 양의 데이터를 처리하는 더 많은 작업을 만드는 것이 좋습니다.

위의 일반적인 지침 외에도 각 애플리케이션마다 특정 애플리케이션에 대해 튜닝할 수 있는 다른 매개 변수가 있습니다. 아래 표에는 각 애플리케이션에 대한 성능 튜닝을 시작하기 위한 몇 가지 매개 변수 및 링크가 나와 있습니다.

| 워크로드               | 작업을 설정하는 매개 변수                                                         |
|--------------------|-------------------------------------------------------------------------------------|
| [HDInsight의 Spark](data-lake-store-performance-tuning-spark.md)  | <ul><li>Num-executors</li><li>Executor-memory</li><li>Executor-cores</li></ul> |
| [HDInsight의 Hive](data-lake-store-performance-tuning-hive.md)    | <ul><li>hive.tez.container.size</li></ul>         |
| [HDInsight의 MapReduce](data-lake-store-performance-tuning-mapreduce.md)            | <ul><li>Mapreduce.map.memory</li><li>Mapreduce.job.maps</li><li>Mapreduce.reduce.memory</li><li>Mapreduce.job.reduces</li></ul> |
| [HDInsight의 Storm](data-lake-store-performance-tuning-storm.md)| <ul><li>작업자 프로세스 수</li><li>Spout 실행자 인스턴스 수</li><li>Bolt 실행자 인스턴스 수 </li><li>Spout 작업 수</li><li>Bolt 작업 수</li></ul>|

## <a name="see-also"></a>참조

* [Azure Data Lake Storage Gen1 개요](data-lake-store-overview.md)
* [Azure 데이터 레이크 분석 시작](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
