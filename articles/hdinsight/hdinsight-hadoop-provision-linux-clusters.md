---
title: "Azure HDInsight에서 Hadoop, HBase, Kafka, Storm 또는 Spark 클러스터 만들기 | Microsoft Docs"
description: "브라우저, Azure CLI, Azure PowerShell, REST 또는 SDK를 사용하여 Linux 기반 HDInsight에서 Hadoop, HBase, Storm 또는 Spark 클러스터를 만드는 방법에 대해 알아봅니다."
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 23a01938-3fe5-4e2e-8e8b-3368e1bbe2ca
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/01/2017
ms.author: jgao
ms.translationtype: Human Translation
ms.sourcegitcommit: f6006d5e83ad74f386ca23fe52879bfbc9394c0f
ms.openlocfilehash: 9fc96db2b832f1e57813bebd2d46e4b78ed04677
ms.contentlocale: ko-kr
ms.lasthandoff: 05/03/2017


---
# <a name="create-hadoop-clusters-in-hdinsight"></a>HDInsight에서 Hadoop 클러스터 만들기
[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Hadoop 클러스터는 클러스터에 있는 작업의 분산 처리에 사용되는 여러 가상 컴퓨터(노드)로 구성됩니다. Azure HDInsight는 개별 노드의 설치 및 구현에 대한 세부 구현을 추상화하므로 일반적인 구성 정보만 제공해야 합니다. 이 문서에서는 이러한 구성 설정에 대해 알아봅니다.

## <a name="basic-configurations"></a>기본 구성

[Azure Portal](https://portal.azure.com)에서 *빠른 생성* 또는 *사용자 지정*을 사용하여 HDInsight 클러스터를 만들 수 있습니다. 이 섹션에서는 빠른 생성 옵션에 사용되는 기본 구성 설정을 설명합니다. 사용자 지정 옵션에는 다음과 같은 추가 구성이 포함됩니다.

- [응용 프로그램](#hdinsight-applications)
- [클러스터 크기](#cluster-size):
- 고급 설정

  - [스크립트 동작](#customize-clusters-using-script-action)
  - [가상 네트워크](#use-virtual-network)

### <a name="subscription"></a>구독 
각 HDInsight 클러스터는 하나의 Azure 구독에 연결됩니다.

### <a name="resource-group-name"></a>리소스 그룹 이름
[Azure Resource Manager](../azure-resource-manager/resource-group-overview.md)를 사용하면 Azure 리소스 그룹이라는 그룹으로서 응용 프로그램에서 리소스로 작업할 수 있습니다. 응용 프로그램에 대한 모든 리소스의 배포, 업데이트, 모니터링 또는 삭제를 조정된 단일 작업으로 수행할 수 있습니다.

### <a name="cluster-name"></a>클러스터 이름
클러스터 이름은 클러스터를 식별하는 데 사용됩니다. 클러스터 이름은 전역적으로 고유해야 하고 다음 명명 지침을 준수해야 합니다.

* 이 필드는 3~63자 사이의 문자열이어야 합니다.
* 이 필드에는 문자, 숫자 및 하이픈만 포함할 수 있습니다.

### <a name="cluster-types"></a> 클러스터 유형
Azure HDInsight는 현재 각각이 특정 기능을 제공하는 구성 요소 모음을 포함하는 다음과 같은 클러스터 유형을 제공합니다.

| 클러스터 유형 | 기능 |
| --- | --- |
| [Hadoop](hdinsight-hadoop-introduction.md) |쿼리 및 분석(배치 작업) |
| [HBase](hdinsight-hbase-overview.md) |NoSQL 데이터 저장소 |
| [Storm](hdinsight-storm-overview.md) |실시간 이벤트 처리 |
| [Spark](hdinsight-apache-spark-overview.md) |메모리 내 처리, 대화형 쿼리, 마이크로 배치 스트림 처리 |
| [Kafka (미리 보기)](hdinsight-apache-kafka-introduction.md) | 실시간 스트리밍 데이터 파이프라인과 응용 프로그램을 만드는 데 사용할 수 있는 분산형 스트리밍 플랫폼 |
| [R Server](hdinsight-hadoop-r-server-overview.md) |다양한 빅 데이터 통계, 예측 모델링 및 기계 학습 기능 |
| [대화형 Hive(미리 보기)](hdinsight-hadoop-use-interactive-hive.md) |대화형 및 더 빠른 Hive 쿼리에 대한 메모리 내 캐싱 |

각 클러스터 유형에는 클러스터 내 자체 노드 수, 클러스터 내 노드 용어 및 각 노드 유형에 대한 기본 VM 크기가 포함됩니다. 다음 표에서는 각 노드 유형에 대한 노드 수는 괄호로 묶어서 표시됩니다.

| 형식 | 노드 | 다이어그램 |
| --- | --- | --- |
| Hadoop은 |헤드 노드(2), 데이터 노드(1+) |![HDInsight Hadoop 클러스터 노드](./media/hdinsight-hadoop-provision-linux-clusters/HDInsight-Hadoop-roles.png) |
| HBase |헤드 서버(2), 지역 서버(1+), 마스터/ZooKeeper 노드(3) |![HDInsight HBase 클러스터 노드](./media/hdinsight-hadoop-provision-linux-clusters/HDInsight-HBase-roles.png) |
| Storm |Nimbus 노드(2), 감독자 서버(1+), ZooKeeper 노드(3) |![HDInsight Storm 클러스터 노드](./media/hdinsight-hadoop-provision-linux-clusters/HDInsight-Storm-roles.png) |
| Spark |헤드 노드(2), 작업자 노드(1+), ZooKeeper 노드(3)(A1 ZooKeeper VM 크기의 경우 무료) |![HDInsight Spark 클러스터 노드](./media/hdinsight-hadoop-provision-linux-clusters/HDInsight-Spark-roles.png) |

다음 테이블에서는 HDInsight에 대한 기본 VM 크기를 나열합니다.

* 브라질 남부 및 일본 서부를 제외하고 지원되는 모든 지역:

  | 클러스터 유형 | Hadoop은 | HBase | Storm | Spark | R 서버 |
  | --- | --- | --- | --- | --- | --- |
  | 헤드: 기본 VM 크기 |D3 v2 |D3 v2 |A3 |D12 v2 |D12 v2 |
  | 헤드: 권장되는 VM 크기 |D3 v2, D4 v2, D12 v2 |D3 v2, D4 v2, D12 v2 |A3, A4, A5 |D12 v2, D13 v2, D14 v2 |D12 v2, D13 v2, D14 v2 |
  | 작업자: 기본 VM 크기 |D3 v2 |D3 v2 |D3 v2 |Windows: D12 v2; Linux: D4 v2 |Windows: D12 v2; Linux: D4 v2 |
  | 작업자: 권장되는 VM 크기 |D3 v2, D4 v2, D12 v2 |D3 v2, D4 v2, D12 v2 |D3 v2, D4 v2, D12 v2 |Windows: D12 v2, D13 v2, D14 v2; Linux: D4 v2, D12 v2, D13 v2, D14 v2 |Windows: D12 v2, D13 v2, D14 v2; Linux: D4 v2, D12 v2, D13 v2, D14 v2 |
  | Zookeeper: 기본 VM 크기 | |A3 |A2 | | |
  | Zookeeper: 권장되는 VM 크기 | |A3, A4, A5 |A2, A3, A4 | | |
  | Edge: 기본 VM 크기 | | | | |Windows: D12 v2; Linux: D4 v2 |
  | Edge: 권장되는 VM 크기 | | | | |Windows: D12 v2, D13 v2, D14 v2; Linux: D4 v2, D12 v2, D13 v2, D14 v2 |
* 브라질 남부 및 일본 서부만 (v2 크기는 여기 없음):

  | 클러스터 유형 | Hadoop은 | HBase | Storm | Spark | R 서버 |
  | --- | --- | --- | --- | --- | --- |
  | 헤드: 기본 VM 크기 |D3 |D3 |A3 |D12 |D12 |
  | 헤드: 권장되는 VM 크기 |D3, D4, D12 |D3, D4, D12 |A3, A4, A5 |D12, D13, D14 |D12, D13, D14 |
  | 작업자: 기본 VM 크기 |D3 |D3 |D3 |Windows: D12; Linux: D4 |Windows: D12; Linux: D4 |
  | 작업자: 권장되는 VM 크기 |D3, D4, D12 |D3, D4, D12 |D3, D4, D12 |Windows: D12, D13, D14; Linux: D4, D12, D13, D14 |Windows: D12, D13, D14; Linux: D4, D12, D13, D14 |
  | Zookeeper: 기본 VM 크기 | |A2 |A2 | | |
  | Zookeeper: 권장되는 VM 크기 | |A2, A3, A4 |A2, A3, A4 | | |
  | Edge: 기본 VM 크기 | | | | |Windows: D12; Linux: D4 |
  | Edge: 권장되는 VM 크기 | | | | |Windows: D12, D13, D14; Linux: D4, D12, D13, D14 |

> [!NOTE]
> 헤드는 Storm 클러스터 유형에 대한 *Nimbus*로 알려져 있습니다. 작업자는 HBase 클러스터 유형에 대한 *영역* 및 Storm 클러스터 유형에 대한 *감독자*로 알려져 있습니다.

> [!IMPORTANT]
> 클러스터를 생성할 때 또는 클러스터를 만든 후 확장할 때 32개 이상의 작업자 노드를 사용하려는 경우 최소한 8개의 코어와 14GB RAM으로 헤드 노드의 크기를 선택해야 합니다.
>
>

[스크립트 작업](#customize-clusters-using-script-action)을 사용하여 이러한 기본 유형에 Hue 등의 같은 다른 구성 요소를 추가할 수 있습니다.

> [!IMPORTANT]
> HDInsight 클러스터는 작업 부하 또는 클러스터에 대한 튜닝 기술에 해당하는 다양한 형식을 제공합니다. 하나의 클러스터에서 Storm 및 HBase 등의 여러 유형을 결합하는 클러스터를 만들기 위해 지원되는 메서드가 없습니다.
>
>

사용자 솔루션에 여러 유형의 HDInsight 클러스터에 분산되어 있는 기술이 필요한 경우, Azure Virtual Network를 만들고 가상 네트워크 내에서 필요한 클러스터 유형을 만들어야 합니다. 이 구성은 클러스터를 허용하며, 배포하는 임의의 코드가 서로 직접 통신하도록 허용합니다.

HDInsight와 함께 Azure Virtual Network를 사용하는 방법에 대한 자세한 내용은 [Azure Virtual Network를 사용하여 HDInsight 확장](hdinsight-extend-hadoop-virtual-network.md)을 참조하세요.

Azure Virtual Network 내에서 두 개의 클러스터 유형을 사용하는 예제는 [Storm 및 HBase의 센서 데이터 분석](hdinsight-storm-sensor-data-analysis.md)을 참조하세요.

### <a name="operating-system"></a>운영 체제
Windows 또는 Linux에서 HDInsight 클러스터를 만들 수 있습니다.  OS 버전에 대한 자세한 내용은 [지원되는 HDInsight 버전](hdinsight-component-versioning.md#supported-hdinsight-versions)을 참조하세요. HDInsight 3.4 이상에서는 클러스터에 대한 기본 OS로 Ubuntu Linux 배포를 사용합니다. 

### <a name="version"></a>버전
이 옵션은 이 클러스터에 필요한 HDInsight 버전을 결정하는 데 사용됩니다. 자세한 내용은 [지원되는 HDInsight 버전](hdinsight-component-versioning.md#supported-hdinsight-versions)을 참조하세요.

### <a name="cluster-tiers"></a>클러스터 계층

Azure HDInsight는 빅 데이터 클라우드 제품을 Standard 및 Premium의 두 범주로 제공합니다.  자세한 내용은 [HDInsight Standard 및 HDInsight Premium]](hdinsight-component-versioning.md#hdinsight-standard-and-hdinsight-premium)을 참조하세요.

다음 스크린 샷에서 클러스터 유형을 선택하기 위한 Azure 포털 정보를 보여줍니다.

![HDInsight 프리미엄 구성](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-cluster-type-configuration.png)


### <a name="credentials"></a>자격 증명
HDInsight 클러스터를 사용하면 클러스터 생성 중에 다음과 같은 두 개의 사용자 계정을 구성할 수 있습니다.

* HTTP 사용자. 기본 사용자 이름은 *admin*입니다. Azure Portal에서 기본 구성을 사용합니다. 경우에 따라 "클러스터 사용자"라고도 합니다.
* SSH 사용자(Linux 클러스터). SSH를 사용하여 클러스터에 연결하는 데 사용됩니다. 자세한 내용은 [HDInsight와 함께 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

### <a name="location"></a>위치
클러스터 위치를 명시적으로 지정할 필요는 없습니다. 클러스터는 기본 저장소의 동일한 위치를 공유합니다. 지원되는 지역 목록은 **HDInsight 가격** 에서 [지역](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409)드롭다운 목록을 클릭하세요.


### <a name="storage"></a>저장소

기존의 Hadoop 분산 파일 시스템(HDFS)은 클러스터에 있는 많은 로컬 디스크를 사용합니다. HDInsight는 [Azure Storage](hdinsight-hadoop-provision-linux-clusters.md#azure-storage) 또는 [Azure Data Lake Store](hdinsight-hadoop-provision-linux-clusters.md#azure-data-lake-store)의 Blob을 사용합니다. HDInsight용 Data Lake Store를 사용할 경우 몇 가지 특정 요구 사항이 적용됩니다. 자세한 내용은 [Azure 포털을 사용하여 Data Lake Store로 HDInsight 클러스터 만들기](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md)의 소개 섹션을 참조하세요.

![HDInsight 저장소](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-cluster-creation-storage.png)


#### <a name="azure-storage"></a>Azure Storage
Azure Storage는 HDInsight와 완벽하게 통합되는 강력한 범용 저장소 솔루션입니다. HDInsight의 모든 구성 요소 집합은 HDFS 인터페이스를 통해 Blob에 저장된 구조적 또는 비구조적 데이터에서 직접 작동할 수 있습니다. Azure Storage에 데이터를 저장하면 사용자 데이터 손실 없이 계산에 사용되는 HDInsight 클러스터를 안전하게 삭제할 수 있습니다. HDInsight는 __범용__ Azure Storage 계정만 지원합니다. 현재 __Blob 저장소__ 계정 유형은 지원하지 않습니다.

구성 중에 Azure Storage 계정과 해당 Azure Storage 계정의 Blob 컨테이너를 지정합니다. Blob 컨테이너는 클러스터에서 기본 저장소 위치로 사용됩니다. 필요에 따라 클러스터에서 액세스할 수 있는 추가 Azure Storage 계정(연결된 저장소)을 지정할 수 있습니다. 또한 클러스터는 전체 공용 읽기 액세스 또는 Blob 전용 공용 읽기 액세스로 구성된 모든 Blob 컨테이너에 액세스할 수도 있습니다.

기본 Blob 컨테이너는 비즈니스 데이터를 저장하는 데 사용하지 않는 것이 좋습니다. 저장소 비용을 줄이기 위해 사용한 후에는 매번 기본 Blob 컨테이너를 삭제하는 것이 좋습니다. 기본 컨테이너에는 응용 프로그램 및 시스템 로그가 포함되어 있습니다. 컨테이너를 삭제하기 전에 이러한 로그를 검색해야 합니다.

여러 클러스터에 대해 하나의 Blob 컨테이너를 공유하는 것은 지원되지 않습니다.

Azure Storage 계정 사용에 대한 자세한 내용은 [HDInsight에서 Azure Storage 사용](hdinsight-hadoop-use-blob-storage.md)을 참조하세요.

#### <a name="azure-data-lake-store"></a>Azure Data Lake Store
Azure Storage 외에도 [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md)를 HDInsight의 HBase 클러스터에 대한 기본 저장소 계정으로 사용하고, 네 개의 모든 HDInsight 클러스터 유형에 대한 연결된 저장소로 사용할 수 있습니다. 자세한 내용은 [Azure 포털을 사용하여 Data Lake Store로 HDInsight 클러스터 만들기](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md)를 참조하세요.

#### <a name="use-additional-storage"></a>추가 저장소

일부 경우에는 클러스터에 추가 저장소를 추가할 수도 있습니다. 예를 들어 서로 다른 지리적 지역 또는 서비스에 대해 여러 Azure Storage 계정을 가지고 있지만 HDInsight로 모두 분석하려는 경우가 있습니다.

HDInsight 클러스터를 만들 때 또는 클러스터를 만든 후에 저장소 계정을 추가할 수 있습니다.  [스크립트 작업을 사용하여 Linux 기반 HDInsight 클러스터 사용자 지정](hdinsight-hadoop-customize-cluster-linux.md)을 참조하세요.

보조 Azure Storage에 대한 자세한 내용은 [HDInsight에서 Azure Storage 사용](hdinsight-hadoop-use-blob-storage.md)을 참조하세요. 보조 Data Lake Storage에 대한 자세한 내용은 [Azure Portal을 사용하는 Data Lake Store로 HDInsight 클러스터 만들기](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md)를 참조하세요.


#### <a name="use-hiveoozie-metastore"></a>Hive/Oozie metastore
HDInsight 클러스터를 삭제한 후 Hive 테이블을 유지하려는 경우 사용자 지정 metastore를 사용하는 것이 좋습니다. 해당 metastore를 다른 HDInsight 클러스터에 연결할 수 있습니다.

> [!IMPORTANT]
> 특정 HDInsight 클러스터 버전에 대해 만든 HDInsight metastore는 여러 다른 HDInsight 클러스터 버전 간에 공유할 수 없습니다. HDInsight 버전 목록은 [지원되는 HDInsight 버전](hdinsight-component-versioning.md#supported-hdinsight-versions)을 참조하세요.
>
>

Metastore는 Hive 테이블, 파티션, 스키마, 열 등 Hive 및 Oozie 메타데이터에 대한 정보를 포함합니다. metastore를 통해 Hive 및 Oozie 메타데이터를 보존할 수 있으므로 새 클러스터를 만들 때 Hive 테이블 또는 Oozie 작업을 다시 만들 필요가 없습니다. 기본적으로 Hive는 포함된 Azure SQL 데이터베이스를 사용하여 이 정보를 저장합니다. 포함된 데이터베이스에서는 클러스터가 삭제된 경우 메타데이터를 유지할 수 없습니다. Hive metastore를 구성하여 HDInsight 클러스터에서 Hive 테이블을 만든 경우 동일한 Hive metastore를 사용하여 클러스터를 다시 만들면 해당 테이블이 유지됩니다.

HBase 클러스터 유형에는 Metastore 구성을 사용할 수 없습니다.

> [!IMPORTANT]
> 사용자 지정 metastore를 만들 때 대시 또는 하이픈을 포함하는 데이터베이스 이름은 사용하지 마세요. 이렇게 하면 클러스터 만들기 프로세스가 실패할 수 있습니다.
>
>

## <a name="install-hdinsight-applications"></a>HDInsight 응용 프로그램 설치

HDInsight 응용 프로그램은 Linux 기반 HDInsight 클러스터에 사용자가 설치할 수 있는 응용 프로그램입니다. Microsoft, ISV(독립 소프트웨어 공급 업체) 또는 사용자가 직접 이러한 응용 프로그램을 개발할 수 있습니다. 자세한 내용은 [Azure HDInsight에 타사 Hadoop 응용 프로그램 설치](hdinsight-apps-install-applications.md)를 참조하세요.

HDInsight 응용 프로그램 대부분은 빈 Edge 노드에 설치됩니다.  빈 에지 노드는 헤드 노드에 설치되고 구성된 것과 동일한 클라이언트 도구를 사용하는 Linux 가상 컴퓨터입니다. 클러스터에 액세스하고, 클라이언트 응용 프로그램을 테스트하며 클라이언트 응용 프로그램을 호스트하는 데 에지 노드를 사용할 수 있습니다. 자세한 내용은 [HDInsight에서 빈 에지 노드 사용](hdinsight-apps-use-edge-node.md)을 참조하세요.

## <a name="configure-cluster-size"></a>클러스터 크기 구성

고객은 클러스터의 수명 기간 동안 해당 노드의 사용량에 대한 대금이 청구됩니다. 클러스터가 만들어지면 청구가 시작되고 클러스터가 삭제되면 청구가 중지됩니다. 클러스터의 경우 할당을 취소하거나 보류할 수 없습니다.

클러스터 유형마다 서로 다른 노드 유형, 노드 수 및 노드 크기를 포함합니다. 예를 들어, Hadoop 클러스터 유형은 *헤드 노드* 2개, 기본 *데이터 노드* 4개를 포함하는 반면, Storm 클러스터 유형은 *Nimbus 노드* 2개, *ZooKeeper 노드* 3개 및 기본 *감독자 노드* 4개를 포함합니다. HDInsight 클러스터의 비용은 노드 수와 노드에 대한 가상 컴퓨터 크기에 따라 결정됩니다. 예를 들어 많은 메모리가 필요한 작업을 수행할 것으로 예상되는 경우 더 많은 메모리를 포함하는 계산 리소스를 선택할 수 있습니다. 학습 목적인 경우 하나의 데이터 노드를 사용하는 것이 좋습니다. HDInsight 가격에 대한 자세한 내용은 [HDInsight 가격](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409)을 참조하세요.

> [!NOTE]
> 클러스터 크기 제한은 Azure 구독에 따라 다릅니다. 제한을 늘리려면 청구 지원 팀에 문의하세요.
>
> 노드에 대해 사용되는 가상 컴퓨터 이미지는 HDInsight 서비스의 구현 세부 정보이므로 클러스터에서 사용되는 노드는 가상 컴퓨터로 계산되지 않습니다. 노드에서 사용되는 계산 코어는 구독에 사용할 수 있는 계산 코어의 총 수에 계산되지 않습니다. HDInsight 클러스터를 만들 때 **클러스터 크기** 블레이드의 요약 섹션에서 클러스터에서 사용할 코어 수와 사용 가능한 코어 수를 확인할 수 있습니다.
>
>

클러스터를 구성하기 위해 Azure Portal을 사용하는 경우 노드 크기는 **노드 가격 책정 계층** 블레이드를 통해 사용할 수 있습니다. 또한 다양한 노드 크기와 관련된 비용도 볼 수 있습니다. 다음 스크린샷에서는 Linux 기반 Hadoop 클러스터에 대한 선택 항목을 보여 줍니다.

![HDInsight VM 노드 크기](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-node-sizes.png)

다음 표에는 HDInsight 클러스터에서 지원하는 크기와 제공하는 용량이 나와 있습니다.

### <a name="standard-tier-a-series"></a>표준 계층: A 시리즈
클래식 배포 모델에서는 PowerShell과 명령줄 인터페이스(CLI) 간에 일부 VM 크기가 약간 다릅니다.

* Standard_A3은 Large
* Standard_A4는 ExtraLarge

| 크기 | CPU 코어 | 메모리 | NIC(최대) | 최대 디스크 크기 | 최대 데이터 디스크(각 1023GB) | 최대 IOPS(디스크당 500) |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A3\Large |4 |7 GB |2 |임시 = 285GB |8 |8x500 |
| Standard_A4\ExtraLarge |8 |14 GB |4 |임시 = 605GB |16 |16x500 |
| Standard_A6 |4 |28GB |2 |임시 = 285GB |8 |8x500 |
| Standard_A7 |8 |56GB |4 |임시 = 605GB |16 |16x500 |

### <a name="standard-tier-d-series"></a>표준 계층: D 시리즈
| 크기 | CPU 코어 | 메모리 | NIC(최대) | 최대 디스크 크기 | 최대 데이터 디스크(각 1023GB) | 최대 IOPS(디스크당 500) |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_D3 |4 |14 GB |4 |임시(SSD) = 200GB |8 |8x500 |
| Standard_D4 |8 |28GB |8 |임시(SSD) = 400GB |16 |16x500 |
| Standard_D12 |4 |28GB |4 |임시(SSD) = 200GB |8 |8x500 |
| Standard_D13 |8 |56GB |8 |임시(SSD) = 400GB |16 |16x500 |
| Standard_D14 |16 |112GB |8 |임시(SSD) = 800GB |32 |32x500 |

### <a name="standard-tier-dv2-series"></a>표준 계층: Dv2 시리즈
| 크기 | CPU 코어 | 메모리 | NIC(최대) | 최대 디스크 크기 | 최대 데이터 디스크(각 1023GB) | 최대 IOPS(디스크당 500) |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_D3_v2 |4 |14 GB |4 |임시(SSD) = 200GB |8 |8x500 |
| Standard_D4_v2 |8 |28GB |8 |임시(SSD) = 400GB |16 |16x500 |
| Standard_D12_v2 |4 |28GB |4 |임시(SSD) = 200GB |8 |8x500 |
| Standard_D13_v2 |8 |56GB |8 |임시(SSD) = 400GB |16 |16x500 |
| Standard_D14_v2 |16 |112GB |8 |임시(SSD) = 800GB |32 |32x500 |

이러한 리소스의 사용 계획을 세울 때 알아야 할 배포 고려 사항은 [가상 컴퓨터 크기](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 참조하세요. 다양한 크기의 가격 책정에 대한 자세한 내용은 [HDInsight 가격 책정](https://azure.microsoft.com/pricing/details/hdinsight)을 참조하세요.   

> [!IMPORTANT]
> 클러스터를 생성할 때 또는 클러스터를 만든 후 확장할 때 32개 이상의 작업자 노드를 사용하려는 경우 최소한 8개의 코어와 14GB RAM으로 헤드 노드의 크기를 선택해야 합니다.
>
>

클러스터가 만들어지면 청구가 시작되고 클러스터가 삭제되면 청구가 중지됩니다. 가격 책정에 대한 자세한 내용은 [HDInsight 가격 정보](https://azure.microsoft.com/pricing/details/hdinsight/)를 참조하세요.


> [!WARNING]
> HDInsight 클러스터와 다른 위치에서는 추가 저장소 계정을 사용할 수 없습니다.



## <a name="use-virtual-network"></a>가상 네트워크 사용
[Azure Virtual Network](https://azure.microsoft.com/documentation/services/virtual-network/)를 통해 솔루션에 필요한 리소스를 포함하는 안전한 영구 네트워크를 만들 수 있습니다. 가상 네트워크에 대한 특정 구성 요구 사항을 비롯한 가상 네트워크로 HDInsight를 사용하는 것에 대한 자세한 내용은 [Azure Virtual Network를 사용하여 HDInsight 기능 확장](hdinsight-extend-hadoop-virtual-network.md)을 참조하세요.


## <a name="customize-clusters-using-script-action"></a>스크립트 작업을 사용하여 클러스터 사용자 지정

만드는 동안 스크립트를 사용하여 추가 구성 요소를 설치하거나 클러스터 구성을 사용자 지정할 수 있습니다. 해당 스크립트는 **스크립트 작업**을 통해 호출됩니다. 스크립트 작업은 Azure 포털, HDInsight Windows PowerShell cmdlet 또는 HDInsight .NET SDK에서 사용할 수 있는 구성 옵션입니다. 자세한 내용은 [스크립트 작업을 사용하여 HDInsight 클러스터 사용자 지정](hdinsight-hadoop-customize-cluster-linux.md)(영문)을 참조하세요.

Mahout, Cascading 등의 일부 네이티브 Java 구성 요소는 클러스터에서 JAR(Java Archive) 파일로 실행할 수 있습니다. 이러한 JAR 파일은 Azure Storage에 배포되고 Hadoop 작업 제출 메커니즘을 통해 HDInsight 클러스터에 제출될 수 있습니다. 자세한 내용은 [프로그래밍 방식으로 Hadoop 작업 제출](hdinsight-submit-hadoop-jobs-programmatically.md)을 참조하세요.

> [!NOTE]
> HDInsight 클러스터에 JAR 파일을 배포하거나 HDInsight 클러스터에서 JAR 파일을 호출하는 데 문제가 있는 경우 [Microsoft 지원](https://azure.microsoft.com/support/options/)으로 문의하세요.
>
> Cascading은 HDInsight에서 지원되지 않으며 Microsoft 지원 대상이 아닙니다. 지원되는 구성 요소 목록은 [HDInsight에서 제공하는 클러스터 버전의 새로운 기능](hdinsight-component-versioning.md)을 참조하세요.
>
>

경우에 따라 생성 프로세스 동안 다음과 같은 구성 파일을 구성해야 할 수 있습니다.

* clusterIdentity.xml
* core-site.xml
* gateway.xml
* hbase-env.xml
* hbase-site.xml
* hdfs-site.xml
* hive-env.xml
* hive-site.xml
* mapred-site
* oozie-site.xml
* oozie-env.xml
* storm-site.xml
* tez-site.xml
* webhcat-site.xml
* yarn-site.xml

자세한 내용은 [부트스트랩을 사용하여 HDInsight 클러스터 사용자 지정](hdinsight-hadoop-customize-cluster-bootstrap.md)을 참조하세요.

## <a name="cluster-creation-methods"></a>클러스터 생성 방법
이 문서에서는 Linux 기반 HDInsight 클러스터 만들기에 대한 기본 정보에 대해 알아봤습니다. 사용자 요구에 적합한 방법을 사용하여 클러스터를 만드는 방법에 대한 구체적인 정보를 확인하기 위해 다음 테이블을 사용합니다.

| 다음을 사용하여 만든 클러스터 | 웹 브라우저 사용 | 명령 줄 | REST API | SDK) | Linux, Mac OS X 또는 Unix | Windows |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| [Azure 포털](hdinsight-hadoop-create-linux-clusters-portal.md) |✔ |&nbsp; |&nbsp; |&nbsp; |✔ |✔ |
| [Azure 데이터 팩터리](hdinsight-hadoop-create-linux-clusters-adf.md) |✔ |✔ |✔ |✔ |✔ |✔ |
| [Azure CLI](hdinsight-hadoop-create-linux-clusters-azure-cli.md) |&nbsp; |✔ |&nbsp; |&nbsp; |✔ |✔ |
| [Azure PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md) |&nbsp; |✔ |&nbsp; |&nbsp; |✔ |✔ |
| [cURL](hdinsight-hadoop-create-linux-clusters-curl-rest.md) |&nbsp; |✔ |✔ |&nbsp; |✔ |✔ |
| [.NET SDK](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md) |&nbsp; |&nbsp; |&nbsp; |✔ |✔ |✔ |
| [Azure 리소스 관리자 템플릿](hdinsight-hadoop-create-linux-clusters-arm-templates.md) |&nbsp; |✔ |&nbsp; |&nbsp; |✔ |✔ |

## <a name="troubleshoot"></a>문제 해결

HDInsight 클러스터를 만드는 동안 문제가 발생할 경우 [액세스 제어 요구 사항](hdinsight-administer-use-portal-linux.md#create-clusters)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- [HDInsight 이란?](hdinsight-hadoop-introduction.md)
- [Hadoop 자습서: HDInsight에서 Hadoop 사용 시작](hdinsight-hadoop-linux-tutorial-get-started.md).
