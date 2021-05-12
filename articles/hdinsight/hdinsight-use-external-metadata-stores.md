---
title: 외부 메타데이터 저장소 사용 - Azure HDInsight
description: Azure HDInsight 클러스터로 외부 메타데이터 저장소를 사용합니다.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 08/06/2020
ms.openlocfilehash: a3bfcfbe59ccc15278b30470c6a060a9c1dd609c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104871747"
---
# <a name="use-external-metadata-stores-in-azure-hdinsight"></a>Azure HDInsight에서 외부 메타데이터 저장소 사용

HDInsight는 외부 저장소를 사용하여 데이터 및 메타데이터를 제어할 수 있게 해 줍니다. 이 기능은 [Apache Hive 메타스토어](#custom-metastore), [Apache Oozie 메타스토어](#apache-oozie-metastore), [Apache Ambari 데이터베이스](#custom-ambari-db)에 사용할 수 있습니다.

HDInsight의 Apache Hive 메타스토어는 Apache Hadoop 아키텍처의 핵심 부분입니다. 메타스토어는 중앙 스키마 리포지토리입니다. 메타스토어는 Apache Spark, Interactive Query(LLAP), Presto 또는 Apache Pig와 같은 다른 빅 데이터 액세스 도구에서 사용됩니다. HDInsight는 Azure SQL Database를 Hive 메타스토어로 사용합니다.

:::image type="content" source="./media/hdinsight-use-external-metadata-stores/metadata-store-architecture.png" alt-text="HDInsight Hive 메타데이터 저장소 아키텍처" border="false":::

두 가지 방법을 이용하여 HDInsight 클러스터에 대해 metastore를 설정할 수 있습니다.

* [기본 metastore](#default-metastore)
* [사용자 지정 metastore](#custom-metastore)

## <a name="default-metastore"></a>기본 metastore

기본적으로 HDInsight는 모든 클러스터 유형과 함께 metastore를 만듭니다. 사용자 지정 metastore를 지정할 수 있습니다. 기본 metastore에 대한 고려 사항은 다음과 같습니다.

* 추가 비용이 없습니다. HDInsight는 모든 클러스터 유형과 함께 metastore를 만들며, 추가 비용은 없습니다.

* 각 기본 metastore는 클러스터 수명 주기의 일부입니다. 클러스터를 삭제하면 해당하는 metastore와 메타데이터도 삭제됩니다.

* 기본 메타스토어는 다른 클러스터와 공유할 수 없습니다.

* 기본 메타스토어는 간단한 워크로드 즉, 여러 클러스터가 필요하지 않고 클러스터의 수명 주기 이상 메타데이터를 보존할 필요가 없는 워크로드에만 사용하는 것이 좋습니다.

> [!IMPORTANT]
> 기본 메타스토어는 **기본 계층 5 DTU 제한(업그레이드할 수 없음)** 이 걸린 Azure SQL Database를 제공합니다! 기본 테스트 목적으로 적합합니다. 대량 또는 프로덕션 워크로드의 경우 외부 메타스토어로 마이그레이션하는 것이 좋습니다.

## <a name="custom-metastore"></a>사용자 지정 metastore

HDInsight는 프로덕션 클러스터에 권장되는 사용자 지정 metastore도 지원합니다.

* 사용자 고유의 Azure SQL Database를 metastore로 지정합니다.

* metastore의 수명 주기가 클러스터의 수명 주기에 연결되어 있지 않으므로 메타데이터를 손실하지 않고 클러스터를 만들고 삭제할 수 있습니다. HDInsight 클러스터를 삭제하고 다시 만든 후에도 Hive 스키마와 같은 메타데이터가 유지됩니다.

* 사용자 지정 metastore를 사용하면 해당 metastore에 여러 클러스터 및 클러스터 유형을 첨부할 수 있습니다. 예를 들어 HDInsight의 여러 대화형 쿼리, Hive 및 Spark 클러스터에서 단일 metastore를 공유할 수 있습니다.

* 선택한 성능 수준에 따라 메타스토어(Azure SQL Database) 비용을 지불합니다.

* 필요에 따라 metastore를 확장할 수 있습니다.

* 클러스터와 외부 메타스토어는 동일한 지역에서 호스트되어야 합니다.

:::image type="content" source="./media/hdinsight-use-external-metadata-stores/metadata-store-use-case.png" alt-text="HDInsight Hive 메타데이터 저장소 사용 사례" border="false":::

### <a name="create-and-config-azure-sql-database-for-the-custom-metastore"></a>사용자 지정 메타스토어에 대해 Azure SQL Database를 만들고 구성하기

HDInsight 클러스터에 대해 사용자 지정 Hive 메타스토어를 설정하기 전에 Azure SQL Database를 만들거나 기존 Azure SQL Database를 사용합니다.  자세한 내용은 [빠른 시작: Azure SQL Database에서 단일 데이터베이스 만들기](../azure-sql/database/single-database-create-quickstart.md?tabs=azure-portal)를 참조하세요.

클러스터를 만드는 동안 HDInsight 서비스가 외부 메타스토어에 연결되어 내 자격 증명을 확인해야 합니다. Azure 서비스 및 리소스가 서버에 액세스할 수 있도록 Azure SQL Database 방화벽 규칙을 구성합니다. **서버 방화벽 설정** 을 선택하여 Azure Portal에서 이 옵션을 사용하도록 설정합니다. 그런 다음 Azure SQL Database에 대해 **공용 네트워크 액세스 거부** 아래에서 **아니요** 를 선택하고 **Azure 서비스 및 리소스가 이 서버에 액세스할 수 있도록 허용** 아래에서 **예** 를 선택합니다. 자세한 내용은 [IP 방화벽 규칙 만들기 및 관리](../azure-sql/database/firewall-configure.md#use-the-azure-portal-to-manage-server-level-ip-firewall-rules)를 참조하세요.

SQL 저장소에 대한 프라이빗 엔드포인트는 `outbound` ResourceProviderConnection을 사용하여 만든 클러스터에서만 지원됩니다. 자세히 알아보려면 이 [설명서](./hdinsight-private-link.md)를 참조하세요.

:::image type="content" source="./media/hdinsight-use-external-metadata-stores/configure-azure-sql-database-firewall1.png" alt-text="서버 방화벽 설정 단추":::

:::image type="content" source="./media/hdinsight-use-external-metadata-stores/configure-azure-sql-database-firewall2.png" alt-text="azure 서비스 액세스 허용":::

### <a name="select-a-custom-metastore-during-cluster-creation"></a>클러스터를 만드는 동안 사용자 지정 metastore 선택

언제든지 클러스터에서 이전에 만든 Azure SQL Database를 가리킬 수 있습니다. 포털을 통해 클러스터를 만들 때 옵션은 **스토리지 > 메타스토어 설정** 에서 지정합니다.

:::image type="content" source="./media/hdinsight-use-external-metadata-stores/azure-portal-cluster-storage-metastore.png" alt-text="HDInsight Hive 메타데이터 저장소 Azure Portal":::

## <a name="hive-metastore-guidelines"></a>Hive 메타스토어 지침

> [!NOTE]
> 컴퓨팅 리소스(실행 중인 클러스터)와 metastore에 저장된 메타데이터를 구분할 수 있도록 가능하면 항상 사용자 지정 metastore를 사용합니다. 50 DTU 및 250GB의 스토리지를 제공하는 S2 계층으로 시작합니다. 병목 상태가 발생하는 경우 데이터베이스를 확장할 수 있습니다.

* 여러 HDInsight 클러스터가 별개의 데이터에 액세스하도록 하려는 경우에는 각 클러스터의 metastore용으로 별도의 데이터베이스를 사용합니다. 여러 HDInsight 클러스터에서 metastore 하나를 공유하는 경우 클러스터는 같은 메타데이터 및 기본 사용자 데이터 파일을 사용합니다.

* 사용자 지정 metastore를 정기적으로 백업합니다. Azure SQL Database는 백업을 자동으로 생성하지만 백업 보존 기간은 각기 다릅니다. 자세한 내용은 [자동 SQL 데이터베이스 백업에 대해 알아보기](../azure-sql/database/automated-backups-overview.md)를 참조하세요.

* 메타스토어와 HDInsight 클러스터를 동일한 지역에 유지합니다. 이렇게 구성할 때 성능이 극대화되고 네트워크 송신 요금이 가장 적게 발생합니다.

* Azure SQL Database 모니터링 도구 또는 Azure Monitor 로그를 사용하여 성능 및 가용성 확인을 위해 메타스토어를 모니터링합니다.

* 기존 사용자 지정 메타스토어 데이터베이스용으로 더 높은 버전의 새 Azure HDInsight가 만들어지면 시스템에서 메타스토어의 스키마를 업그레이드합니다. 데이터베이스를 백업에서 복원하지 않고서는 업그레이드를 취소할 수 없습니다.

* 여러 클러스터 간에 metastore 하나를 공유하는 경우 모든 클러스터의 HDInsight 버전이 같은지 확인해야 합니다. 각 Hive 버전이 사용하는 metastore 데이터베이스 스키마는 서로 다릅니다. 예를 들어 Hive 2.1과 Hive 3.1 버전 클러스터에서 메타스토어를 공유할 수는 없습니다.

* HDInsight 4.0에서 Spark 및 Hive는 SparkSQL 또는 Hive 테이블에 액세스하기 위해 독립적인 카탈로그를 사용합니다. Spark에서 만든 테이블은 Spark 카탈로그에 있습니다. Hive에서 만든 테이블은 Hive 카탈로그에 있습니다. 이 동작은 Hive 및 Spark 공유 공통 카탈로그가 있는 HDInsight 3.6과는 다릅니다. HDInsight 4.0의 Hive 및 Spark 통합은 HWC(Hive Warehouse Connector)에 의존합니다. HWC는 Spark와 Hive 간에 브리지 역할을 합니다. [Hive Warehouse Connector에 대해 알아봅니다](../hdinsight/interactive-query/apache-hive-warehouse-connector.md).

* HDInsight 4.0에서 Hive와 Spark 간에 metastore을 공유하려는 경우 Spark 클러스터에서 metastore.catalog.default 속성을 hive로 변경하면 됩니다. Ambari 고급 spark2-hive-site-override에서 이 속성을 찾을 수 있습니다. metastore의 공유는 외부 hive 테이블에서만 작동합니다. 내부/관리형 hive 테이블 또는 ACID 테이블이 있는 경우에는 이 공유가 작동하지 않습니다.  

## <a name="apache-oozie-metastore"></a>Apache Oozie 메타스토어

Apache Oozie는 Hadoop 작업을 관리하는 워크플로 조정 시스템입니다. Oozie는 Apache MapReduce, Pig, Hive 등에 대한 Hadoop 작업을 지원합니다.  Oozie는 메타스토어를 사용하여 워크플로에 대한 세부 정보를 저장합니다. Oozie 사용 시 성능을 높이기 위해 Azure SQL Database를 사용자 지정 metastore로 사용할 수 있습니다. 클러스터를 삭제한 후에는 메타스토어가 Oozie 작업 데이터에 대한 액세스를 제공합니다.

Azure SQL Database를 사용하여 Oozie metastore를 만드는 방법에 대한 지침은 [워크플로에 대해 Apache Oozie 사용](hdinsight-use-oozie-linux-mac.md)을 참조하세요.

## <a name="custom-ambari-db"></a>사용자 지정 Ambari DB

HDInsight에서 Apache Ambari로 자신만의 외부 데이터베이스를 사용하려면 [사용자 지정 Apache Ambari 데이터베이스](hdinsight-custom-ambari-db.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* [Apache Hadoop, Apache Spark, Apache Kafka 등을 사용하여 HDInsight에서 클러스터 설정](./hdinsight-hadoop-provision-linux-clusters.md)