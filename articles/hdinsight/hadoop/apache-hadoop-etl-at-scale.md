---
title: 대규모로 수행하는 ETL(추출, 변환 및 로드) - Azure HDInsight
description: HDInsight에서 Apache Hadoop과 함께 추출, 변환 및 로드를 사용하는 방법을 알아봅니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/28/2020
ms.openlocfilehash: bb978f30104b1e38bd8930ccb058c4057d394e51
ms.sourcegitcommit: 693df7d78dfd5393a28bf1508e3e7487e2132293
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/28/2020
ms.locfileid: "92899664"
---
# <a name="extract-transform-and-load-etl-at-scale"></a>규모에 맞게 ETL(추출, 변환 및 로드)

ETL(추출, 변환 및 로드)은 다양한 원본에서 데이터를 가져오고, 표준 위치에서 수집하고 정리 및 처리한 다음, 최종적으로 쿼리할 수 있는 데이터 저장소에 로드하는 프로세스입니다. 레거시 ETL 프로세스는 데이터를 가져오고, 제 위치에서 정리한 다음, 관계형 데이터 엔진에 저장합니다. Azure HDInsight를 사용하면 다양한 Apache Hadoop 환경 구성 요소에서 대규모로 ETL을 수행할 수 있습니다.

ETL 프로세스에서 HDInsight를 사용하는 방법은 다음과 같은 파이프라인으로 요약할 수 있습니다.

![대규모로 수행하는 HDInsight ETL 개요](./media/apache-hadoop-etl-at-scale/hdinsight-etl-at-scale-overview.png)

다음 섹션에서는 ETL 단계 및 관련 구성 요소 각각에 대해 살펴봅니다.

## <a name="orchestration"></a>오케스트레이션

오케스트레이션은 ETL 파이프라인의 모든 단계에 걸쳐 있습니다. HDInsight의 ETL 작업은 몇 가지 다른 제품과 함께 상호 작동하는 경우가 많습니다. 예를 들면 다음과 같습니다.

- Apache Pig에서 다른 부분을 정리하는 동안 Apache Hive를 사용하여 데이터의 일부분을 정리할 수 있습니다.
- Azure Data Factory를 사용하여 Azure Data Lake Store에서 Azure SQL Database로 데이터를 로드할 수 있습니다.

적절한 시간에 적절한 작업을 실행하려면 오케스트레이션이 필요합니다.

### <a name="apache-oozie"></a>Apache Oozie

Apache Oozie는 Hadoop 작업을 관리하는 워크플로 조정 시스템입니다. Oozie는 HDInsight 클러스터 내에서 실행되며 Hadoop 스택과 통합됩니다. Oozie는 Apache Hadoop, MapReduce, Pig, Hive 및 Sqoop에 대한 Hadoop 작업을 지원합니다. Oozie를 사용하여 Java 프로그램이나 셸 스크립트와 같이 각 시스템에 적용되는 작업을 예약할 수 있습니다.

자세한 내용은 [Apache Hadoop과 함께 Apache Oozie를 사용하여 Linux 기반 Azure HDInsight에서 워크플로 정의 및 실행](../hdinsight-use-oozie-linux-mac.md)을 참조하세요. [데이터 파이프라인 운용](../hdinsight-operationalize-data-pipeline.md)도 참조하세요.

### <a name="azure-data-factory"></a>Azure 데이터 팩터리

Azure Data Factory는 오케스트레이션 기능을 PaaS(Platform as a Service) 형태로 제공합니다. Azure Data Factory는 클라우드 기반 데이터 통합 서비스입니다. Azure Data Factory를 사용하면 데이터 이동 및 데이터 변환을 오케스트레이션하고 자동화하기 위한 데이터 기반 워크플로를 만들 수 있습니다.

Azure Data Factory를 사용하여 다음과 같은 작업을 수행할 수 있습니다.

1. 데이터 기반 워크플로를 만들고 예약. 파이프라인은 서로 다른 데이터 저장소에서 데이터를 수집합니다.
1. HDInsight, Hadoop과 같은 컴퓨팅 서비스를 사용하여 데이터 처리 및 변환. 이 단계에서는 Spark, Azure Data Lake Analytics, Azure Batch 또는 Azure Machine Learning을 사용할 수도 있습니다.
1. Azure Synapse Analytics와 같은 데이터 저장소에 출력 데이터를 게시 하 여 BI 응용 프로그램에서 사용할 수 있습니다.

Azure Data Factory에 대한 자세한 내용은 [이 설명서](../../data-factory/introduction.md)를 참조하세요.

## <a name="ingest-file-storage-and-result-storage"></a>파일 스토리지 및 결과 스토리지 수집

원본 데이터 파일은 일반적으로 Azure Storage 또는 Azure Data Lake Storage의 위치에 로드됩니다. 파일은 보통 CSV와 같은 플랫 형식이지만 어떠한 형식도 지원됩니다.

### <a name="azure-storage"></a>Azure Storage

Azure Storage에는 특정한 확장성 목표가 있습니다. 자세한 내용은 [Blob Storage의 확장성 및 성능 목표](../../storage/blobs/scalability-targets.md)를 참조하세요. 대부분의 분석 노드의 경우 Azure Storage에서 더 작은 파일을 많이 처리할 때 그 크기가 가장 효율적으로 조정됩니다. Azure Storage는 계정 한도 내에서 파일 크기에 관계없이 동일한 성능을 보장합니다. 즉, 일부 데이터만 사용하든 모든 데이터를 사용하든, 테라바이트 단위의 데이터를 저장한 경우에도 일관된 성능을 유지할 수 있습니다.

Azure Storage에는 여러 가지 유형의 Blob이 있습니다. *추가 Blob* 은 웹 로그 또는 센서 데이터를 저장하는 데 유용한 옵션입니다.

여러 서버로 다수의 Blob을 분산하여 Blob에 대한 액세스 권한을 스케일 아웃할 수 있습니다. 단, 서버 한 대당 Blob 하나만을 처리합니다. Blob은 Blob 컨테이너로 논리적으로 그룹화할 수 있지만, 이 그룹화에서는 분할이 이뤄지지 않습니다.

Azure Storage에는 Blob Storage를 위한 WebHDFS API 계층이 있습니다. 모든 HDInsight 서비스는 HDFS(Hadoop 분산 파일 시스템)를 사용하는 방식과 비슷하게 데이터 정리 및 데이터 처리를 위해 Azure Blob Storage의 파일에 액세스할 수 있습니다.

데이터는 일반적으로 PowerShell, Azure Storage SDK 또는 AZCopy를 사용하여 Azure Storage로 수집됩니다.

### <a name="azure-data-lake-storage"></a>Azure Data Lake Storage

Azure Data Lake Storage는 분석 데이터를 위한 관리형 하이퍼스케일 리포지토리입니다. 이는 HDFS와 호환되며 HDFS와 비슷한 디자인 패러다임을 사용합니다. Data Lake Storage는 개별 파일의 전체 용량과 크기에 대해 제한 없는 적응성을 제공합니다. 큰 파일을 여러 노드에 저장할 수 있으므로 큰 파일을 다룰 때 사용하기에 좋습니다. Data Lake Storage에서 데이터를 분할하는 작업은 백그라운드에서 수행됩니다. 수백 테라바이트의 데이터를 효율적으로 읽고 쓰는 수천 개의 동시 실행기를 사용하여 분석 작업을 실행하는 대규모 처리량을 얻을 수 있습니다.

데이터는 일반적으로 Azure Data Factory를 통해 Data Lake Storage에 수집됩니다. Data Lake Storage SDK, AdlCopy 서비스, Apache DistCp 또는 Apache Sqoop을 사용할 수도 있습니다. 어떤 서비스를 선택할지는 데이터가 어디에 있는지에 따라 달라집니다. 데이터가 기존 Hadoop 클러스터에 있는 경우 Apache DistCp, AdlCopy Service 또는 Azure Data Factory를 사용할 수 있습니다. 데이터가 Azure Blob Storage에 있는 경우 Azure Data Lake Storage .NET SDK, Azure PowerShell 또는 Azure Data Factory를 사용할 수 있습니다.

Data Lake Storage는 Azure Event Hubs 또는 Apache Storm을 통한 이벤트 수집에 최적화되어 있습니다.

### <a name="considerations-for-both-storage-options"></a>두 스토리지 옵션 모두에 대한 고려 사항

테라바이트 범위의 데이터 세트를 업로드하는 경우 네트워크 대기 시간이 중요한 문제가 될 수 있습니다. 데이터가 온-프레미스 위치에서 제공되는 경우 특히 그렇습니다. 이러한 경우 다음과 같은 옵션을 사용할 수 있습니다.

- **Azure ExpressRoute:** Azure 데이터 센터와 온-프레미스 인프라 사이의 프라이빗 연결을 만듭니다. 이러한 연결은 대량의 데이터를 전송할 때 신뢰할 수 있는 옵션을 제공합니다. 자세한 내용은 [Azure ExpressRoute 설명서](../../expressroute/expressroute-introduction.md)를 참조하세요.

- **하드 디스크 드라이브에서 데이터 업로드:** [Azure Import/Export 서비스](../../storage/common/storage-import-export-service.md)를 사용하여 데이터가 포함된 하드 디스크를 Azure 데이터 센터로 보낼 수 있습니다. 데이터는 먼저 Azure Blob Storage에 업로드됩니다. 그런 다음, Azure Data Factory 또는 AdlCopy 도구를 사용하여 Azure Blob storage에서 Data Lake Storage로 데이터를 복사할 수 있습니다.

### <a name="azure-synapse-analytics"></a>Azure Synapse Analytics

Azure Synapse Analytics (이전의 SQL DW)는 준비 된 결과를 저장 하는 데 적절 한 선택입니다. Azure HDInsight를 사용 하 여 Azure Synapse Analytics에 대해 이러한 서비스를 수행할 수 있습니다.

Azure Synapse Analytics는 분석 워크 로드에 최적화 된 관계형 데이터베이스 저장소입니다. Azure SQL Data Warehouse의 크기는 분할된 테이블에 따라 조정됩니다. 테이블은 여러 노드에 걸쳐 분할될 수 있습니다. 노드는 테이블을 만들 때 선택됩니다. 사후에 크기를 조정할 수 있지만, 이 경우 데이터 이동이 필요할 수 있는 활성 프로세스입니다. 자세한 내용은 [Azure Synapse Analytics에서 계산 관리](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md)를 참조 하세요.

### <a name="apache-hbase"></a>Apache HBase

Apache HBase는 Azure HDInsight에서 사용할 수 있는 키/값 저장소입니다. Apache HBase는 Hadoop을 기반으로 하고 Google BigTable 이후에 모델링된 오픈 소스 NoSQL 데이터베이스입니다. HBase는 대량의 비정형/반정형 데이터에 대해 고성능 임의 액세스 및 강력한 일관성을 제공합니다.

HBase는 스키마가 없는 데이터베이스이므로 열과 데이터 형식을 사용하기 전에 정의할 필요가 없습니다. 데이터는 테이블의 행에 저장되고 열 패밀리로 그룹화됩니다.

오픈 소스 코드는 수천 대의 노드에 있는 페타바이트 크기의 데이터를 처리할 수 있을 정도로 선형으로 확장됩니다. HBase는 Hadoop 환경의 분산 애플리케이션이 제공하는 데이터 중복, 일괄 처리 및 기타 기능을 사용합니다.

HBase는 향후 분석을 위한 센서 및 로그 데이터를 위한 좋은 대상입니다.

HBase 적응성은 HDInsight 클러스터의 노드 수에 따라 달라집니다.

### <a name="azure-sql-databases"></a>Azure SQL 데이터베이스

Azure는 다음과 같은 세 가지 PaaS 관계형 데이터베이스를 제공합니다.

* [Azure SQL Database](../../azure-sql/database/sql-database-paas-overview.md)는 Microsoft SQL Server를 구현한 데이터베이스입니다. 성능에 대한 자세한 내용은 [Azure SQL Database에서 성능 튜닝](../../azure-sql/database/performance-guidance.md)을 참조하세요.
* [Azure Database for MySQL](../../mysql/overview.md)은 Oracle MySQL을 구현한 데이터베이스입니다.
* [Azure Database for PostgreSQL](../../postgresql/quickstart-create-server-database-portal.md)은 PostgreSQL을 구현한 데이터베이스입니다.

이들 제품을 스케일 업하려면 CPU와 메모리를 추가하세요.  또한 I/O 성능을 향상시키기 위해 제품과 함께 프리미엄 디스크를 사용하도록 선택할 수도 있습니다.

## <a name="azure-analysis-services"></a>Azure Analysis Services

Azure Analysis Services는 의사 결정 지원 및 비즈니스 분석에서 사용되는 분석 데이터입니다. Azure Analysis Services는 Power BI와 같은 클라이언트 애플리케이션과 비즈니스 보고서를 위한 분석 데이터를 제공합니다. 분석 데이터는 Excel, SQL Server Reporting Services 보고서 및 기타 데이터 시각화 도구에서도 사용할 수 있습니다.

분석 큐브의 크기는 개별 큐브의 계층을 변경하여 조정할 수 있습니다. 자세한 내용은 [Azure Analysis Services 가격](https://azure.microsoft.com/pricing/details/analysis-services/)을 참조하세요.

## <a name="extract-and-load"></a>추출 및 로드

Azure에 데이터가 있으면 여러 서비스를 사용하여 데이터를 추출하여 다른 제품에 로드할 수 있습니다. HDInsight는 Sqoop 및 Flume을 지원합니다.

### <a name="apache-sqoop"></a>Apache Sqoop

Apache Sqoop은 구조화, 반구조화 및 비구조화된 데이터 원본 간에 데이터를 효율적으로 전송하도록 설계된 도구입니다.

Sqoop은 MapReduce를 사용하여 데이터를 가져오고 내보내고, 병렬 작업과 내결함성을 제공합니다.

### <a name="apache-flume"></a>Apache Flume

Apache Flume은 대량의 로그 데이터를 효율적으로 수집, 집계 및 이동하기 위해 안정적으로 사용할 수 있는 분산 서비스입니다. Flume은 스트리밍 데이터 흐름을 기반으로 한 유연한 아키텍처입니다. Flume은 튜닝할 수 있는 안정성 메커니즘을 통해 내결함성이 있는 강력한 도구입니다. Flume에는 여러 장애 조치(failover) 및 복구 메커니즘이 있습니다. Flume은 온라인 분석 애플리케이션을 사용할 수 있도록 지원하는 확장 가능한 간단한 데이터 모델을 사용합니다.

Apache Flume은 Azure HDInsight와 함께 사용할 수 없습니다. 하지만 온-프레미스 Hadoop 설치에서는 Flume을 사용하여 Azure Blob Storage 또는 Azure Data Lake Storage로 데이터를 보낼 수 있습니다. 자세한 내용은 [HDInsight와 함께 Apache Flume 사용](https://web.archive.org/web/20190217104751/https://blogs.msdn.microsoft.com/bigdatasupport/2014/03/18/using-apache-flume-with-hdinsight/)을 참조하세요.

## <a name="transform"></a>변환

선택한 위치에 데이터가 있으면 특정 사용 패턴에 맞게 데이터를 정리, 결합 또는 준비해야 합니다. Hive, Pig 및 Spark SQL은 모두 이러한 종류의 작업에 적합합니다. HDInsight에서는 이러한 도구를 모두 지원합니다.

## <a name="next-steps"></a>다음 단계

- [Apache Hive를 ETL 도구로 사용](apache-hadoop-using-apache-hive-as-an-etl-tool.md)
- [Azure HDInsight 클러스터에 Azure Data Lake Storage Gen2 사용](../hdinsight-hadoop-use-data-lake-storage-gen2.md)
- [Azure SQL Database에서 Apache Hive 테이블로 데이터 이동](./apache-hadoop-use-sqoop-mac-linux.md)
