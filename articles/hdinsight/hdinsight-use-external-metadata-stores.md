---
title: 외부 메타데이터 저장소 사용 - Azure HDInsight
description: Azure HDInsight 클러스터에서 외부 메타데이터 저장소를 사용합니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/03/2020
ms.openlocfilehash: 0cadf3930008868fe223e6e1024a2d14d17d8131
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80657114"
---
# <a name="use-external-metadata-stores-in-azure-hdinsight"></a>Azure HDInsight에서 외부 메타데이터 저장소 사용

HDInsight를 사용하면 외부 데이터 저장소를 통해 데이터와 메타데이터를 제어할 수 있습니다. 이 기능은 [아파치 하이브 메타스토어,](#custom-metastore) [아파치 오지 메타스토어](#apache-oozie-metastore)및 [아파치 암바리 데이터베이스에서](#custom-ambari-db)사용할 수 있습니다.

HDInsight의 Apache Hive 메타스토어는 Apache Hadoop 아키텍처의 핵심 부분입니다. 메타스토어는 중앙 스키마 리포지토리입니다. 메타스토어는 아파치 스파크, 인터랙티브 쿼리(LLAP), 프레스토 또는 아파치 피그와 같은 다른 빅 데이터 액세스 도구에서 사용됩니다. HDInsight는 Azure SQL Database를 Hive 메타스토어로 사용합니다.

![HDInsight Hive 메타데이터 저장소 아키텍처](./media/hdinsight-use-external-metadata-stores/metadata-store-architecture.png)

두 가지 방법을 이용하여 HDInsight 클러스터에 대해 metastore를 설정할 수 있습니다.

* [기본 metastore](#default-metastore)
* [사용자 지정 metastore](#custom-metastore)

## <a name="default-metastore"></a>기본 metastore

기본적으로 HDInsight는 모든 클러스터 유형과 함께 metastore를 만듭니다. 사용자 지정 metastore를 지정할 수 있습니다. 기본 metastore에 대한 고려 사항은 다음과 같습니다.

* 추가 비용이 없습니다. HDInsight는 모든 클러스터 유형과 함께 metastore를 만들며, 추가 비용은 없습니다.

* 각 기본 metastore는 클러스터 수명 주기의 일부입니다. 클러스터를 삭제하면 해당하는 metastore와 메타데이터도 삭제됩니다.

* 기본 메타스토어를 다른 클러스터와 공유할 수 없습니다.

* 기본 metastore는 DTU(데이터베이스 트랜잭션 단위)가 5개로 제한되는 기본 Azure SQL DB를 사용합니다.
이 기본 메타스토어는 일반적으로 비교적 간단한 워크로드에 사용됩니다. 여러 클러스터가 필요하지 않고 클러스터의 수명 주기 동안 보존된 메타데이터가 필요하지 않은 워크로드입니다.

## <a name="custom-metastore"></a>사용자 지정 metastore

HDInsight는 프로덕션 클러스터에 권장되는 사용자 지정 metastore도 지원합니다.

* 사용자 고유의 Azure SQL Database를 metastore로 지정합니다.

* 메타스토어의 수명 주기는 클러스터 수명 주기와 연결되지 않으므로 메타데이터를 잃지 않고 클러스터를 만들고 삭제할 수 있습니다. HDInsight 클러스터를 삭제하고 다시 만든 후에도 Hive 스키마와 같은 메타데이터가 유지됩니다.

* 사용자 지정 metastore를 사용하면 해당 metastore에 여러 클러스터 및 클러스터 유형을 첨부할 수 있습니다. 예를 들어 HDInsight의 여러 대화형 쿼리, Hive 및 Spark 클러스터에서 단일 metastore를 공유할 수 있습니다.

* 선택한 성능 수준에 따라 metastore(Azure SQL DB) 비용을 지불합니다.

* 필요에 따라 metastore를 확장할 수 있습니다.

* 클러스터와 외부 메타스토어는 동일한 리전에서 호스팅되어야 합니다.

![HDInsight Hive 메타데이터 저장소 사용 사례](./media/hdinsight-use-external-metadata-stores/metadata-store-use-case.png)

### <a name="create-and-config-azure-sql-database-for-the-custom-metastore"></a>사용자 지정 메타스토어에 대한 Azure SQL 데이터베이스 만들기 및 구성

HDInsight 클러스터에 대한 사용자 지정 Hive 메타스토어를 설정하기 전에 기존 Azure SQL 데이터베이스를 만들거나 설정합니다.  자세한 내용은 [빠른 시작: Azure SQL DB에서 단일 데이터베이스 만들기를](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-get-started?tabs=azure-portal)참조하십시오.

Azure 서비스 및 리소스가 서버에 액세스할 수 있도록 Azure SQL Database 방화벽 규칙을 구성합니다. **서버 방화벽 설정을**선택하여 Azure 포털에서 이 옵션을 활성화합니다. 그런 다음 **Azure 서비스 및 리소스가** Azure SQL Database 서버 또는 데이터베이스에 대해 이 서버에 액세스할 수 있도록 아래에서 **ON을** 선택합니다. 자세한 내용은 [IP 방화벽 규칙 만들기 및 관리를](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure#use-the-azure-portal-to-manage-server-level-ip-firewall-rules) 참조하십시오.

![서버 방화벽 버튼 설정](./media/hdinsight-use-external-metadata-stores/configure-azure-sql-database-firewall1.png)

![Azure 서비스 액세스 허용](./media/hdinsight-use-external-metadata-stores/configure-azure-sql-database-firewall2.png)

### <a name="select-a-custom-metastore-during-cluster-creation"></a>클러스터를 만드는 동안 사용자 지정 metastore 선택

클러스터를 언제든지 이전에 만든 Azure SQL Database로 가리킬 수 있습니다. 포털을 통한 클러스터 생성의 경우 **저장소 > 메타스토어 설정에서**옵션이 지정됩니다.

![HDInsight Hive 메타데이터 저장소 Azure Portal](./media/hdinsight-use-external-metadata-stores/azure-portal-cluster-storage-metastore.png)

## <a name="hive-metastore-guidelines"></a>하이브 메타스토어 가이드라인

* 컴퓨팅 리소스(실행 중인 클러스터)와 metastore에 저장된 메타데이터를 구분할 수 있도록 가능하면 항상 사용자 지정 metastore를 사용합니다.

* 50 DTU 및 250GB의 스토리지를 제공하는 S2 계층으로 시작합니다. 병목 상태가 발생하는 경우 데이터베이스를 확장할 수 있습니다.

* 여러 HDInsight 클러스터가 별개의 데이터에 액세스하도록 하려는 경우에는 각 클러스터의 metastore용으로 별도의 데이터베이스를 사용합니다. 여러 HDInsight 클러스터에서 metastore 하나를 공유하는 경우 클러스터는 같은 메타데이터 및 기본 사용자 데이터 파일을 사용합니다.

* 사용자 지정 metastore를 정기적으로 백업합니다. Azure SQL Database는 백업을 자동으로 생성하지만 백업 보존 기간은 각기 다릅니다. 자세한 내용은 [자동 SQL 데이터베이스 백업에 대해 알아보기](../sql-database/sql-database-automated-backups.md)를 참조하세요.

* 동일한 리전에서 메타스토어 및 HDInsight 클러스터를 찾습니다. 이 구성은 가장 높은 성능과 가장 낮은 네트워크 송신 요금을 제공합니다.

* Azure SQL 데이터베이스 모니터링 도구 또는 Azure 모니터 로그를 사용하여 메타스토어에서 성능 및 가용성을 모니터링합니다.

* 기존 사용자 지정 메타스토어 데이터베이스에 대해 새 상위 버전의 Azure HDInsight가 만들어지면 시스템은 메타스토어의 스키마를 업그레이드합니다. 백업에서 데이터베이스를 복원하지 않으면 업그레이드를 되돌릴 수 없습니다.

* 여러 클러스터 간에 metastore 하나를 공유하는 경우 모든 클러스터의 HDInsight 버전이 같은지 확인해야 합니다. 각 Hive 버전이 사용하는 metastore 데이터베이스 스키마는 서로 다릅니다. 예를 들어 Hive 2.1 및 Hive 3.1 버전 클러스터에서 메타스토어를 공유할 수 없습니다.

* HDInsight 4.0에서 스파크와 하이브는 SparkSQL 또는 하이브 테이블에 액세스하기 위해 독립적인 카탈로그를 사용합니다. 스파크가 만든 테이블은 스파크 카탈로그에 있습니다. 하이브가 만든 테이블은 하이브 카탈로그에 있습니다. 이 동작은 Hive와 Spark가 공통 카탈로그를 공유한 HDInsight 3.6과 다릅니다. HDInsight 4.0의 하이브 및 스파크 통합은 하이브 웨어하우스 커넥터(HWC)를 사용합니다. HWC는 스파크와 하이브 사이의 다리 역할을 합니다. [하이브 창고 커넥터에 대해 자세히 알아보기.](../hdinsight/interactive-query/apache-hive-warehouse-connector.md)

## <a name="apache-oozie-metastore"></a>아파치 오지 메타스토어

Apache Oozie는 Hadoop 작업을 관리하는 워크플로 조정 시스템입니다. Oozie는 Apache MapReduce, Pig, Hive 등에 대한 Hadoop 작업을 지원합니다.  Oozie는 메타스토어를 사용하여 워크플로에 대한 세부 정보를 저장합니다. Oozie 사용 시 성능을 높이기 위해 Azure SQL Database를 사용자 지정 metastore로 사용할 수 있습니다. 메타스토어는 클러스터를 삭제한 후 Oozie 작업 데이터에 대한 액세스를 제공합니다.

Azure SQL Database를 사용하여 Oozie metastore를 만드는 방법에 대한 지침은 [워크플로에 대해 Apache Oozie 사용](hdinsight-use-oozie-linux-mac.md)을 참조하세요.

## <a name="custom-ambari-db"></a>사용자 지정 Ambari DB

HDInsight에 아파치 암바리와 자신의 외부 데이터베이스를 사용하려면, [사용자 정의 아파치 암바리 데이터베이스를](hdinsight-custom-ambari-db.md)참조하십시오.

## <a name="next-steps"></a>다음 단계

* [Apache Hadoop, Apache Spark, Apache Kafka 등을 사용하여 HDInsight에서 클러스터 설정](./hdinsight-hadoop-provision-linux-clusters.md)
