---
title: 외부 메타데이터 저장소 사용 - Azure HDInsight
description: Use external metadata stores with Azure HDInsight clusters, and best practices.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/29/2019
ms.openlocfilehash: 14b36a391778649e96694f1cb1d3a1b4e7ee89ba
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74327357"
---
# <a name="use-external-metadata-stores-in-azure-hdinsight"></a>Azure HDInsight에서 외부 메타데이터 저장소 사용

HDInsight allows you to take control of your data and metadata by deploying key metadata solutions and management databases to external data stores. This feature is currently available for [Apache Hive metastore](#custom-metastore), [Apache Oozie metastore](#apache-oozie-metastore) and [Apache Ambari database](#custom-ambari-db).

HDInsight의 Apache Hive 메타스토어는 Apache Hadoop 아키텍처의 핵심 부분입니다. metastore는 Apache Spark, Interactive Query(LLAP), Presto 또는 Apache Pig와 같은 다른 빅 데이터 액세스 도구에서 사용할 수 있는 중앙 스키마 리포지토리입니다. HDInsight는 Azure SQL Database를 Hive 메타스토어로 사용합니다.

![HDInsight Hive 메타데이터 저장소 아키텍처](./media/hdinsight-use-external-metadata-stores/metadata-store-architecture.png)

두 가지 방법을 이용하여 HDInsight 클러스터에 대해 metastore를 설정할 수 있습니다.

* [기본 metastore](#default-metastore)
* [사용자 지정 metastore](#custom-metastore)

## <a name="default-metastore"></a>기본 metastore

기본적으로 HDInsight는 모든 클러스터 유형과 함께 metastore를 만듭니다. 사용자 지정 metastore를 지정할 수 있습니다. 기본 metastore에 대한 고려 사항은 다음과 같습니다.

* 추가 비용이 없습니다. HDInsight는 모든 클러스터 유형과 함께 metastore를 만들며, 추가 비용은 없습니다.

* 각 기본 metastore는 클러스터 수명 주기의 일부입니다. 클러스터를 삭제하면 해당하는 metastore와 메타데이터도 삭제됩니다.

* You can't share the default metastore with other clusters.

* 기본 metastore는 DTU(데이터베이스 트랜잭션 단위)가 5개로 제한되는 기본 Azure SQL DB를 사용합니다.
이 기본 metastore는 일반적으로 여러 클러스터가 필요하지 않은 상대적으로 단순하고 클러스터의 수명 주기 이상 메타데이터를 보존할 필요가 없는 워크로드에 사용됩니다.

## <a name="custom-metastore"></a>사용자 지정 metastore

HDInsight는 프로덕션 클러스터에 권장되는 사용자 지정 metastore도 지원합니다.

* 사용자 고유의 Azure SQL Database를 metastore로 지정합니다.

* The lifecycle of the metastore isn't tied to a clusters lifecycle, so you can create and delete clusters without losing metadata. HDInsight 클러스터를 삭제하고 다시 만든 후에도 Hive 스키마와 같은 메타데이터가 유지됩니다.

* 사용자 지정 metastore를 사용하면 해당 metastore에 여러 클러스터 및 클러스터 유형을 첨부할 수 있습니다. 예를 들어 HDInsight의 여러 대화형 쿼리, Hive 및 Spark 클러스터에서 단일 metastore를 공유할 수 있습니다.

* 선택한 성능 수준에 따라 metastore(Azure SQL DB) 비용을 지불합니다.

* 필요에 따라 metastore를 확장할 수 있습니다.

![HDInsight Hive 메타데이터 저장소 사용 사례](./media/hdinsight-use-external-metadata-stores/metadata-store-use-case.png)

### <a name="create-and-config-azure-sql-database-for-the-custom-metastore"></a>Create and config Azure SQL Database for the custom metastore

You need to create or have an existing Azure SQL Database before setting up a custom Hive metastore for a HDInsight cluster.  For more information, see [Quickstart: Create a single database in Azure SQL DB](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-get-started?tabs=azure-portal).

To make sure that your HDInsight cluster can access the connected Azure SQL Database, configure Azure SQL Database firewall rules to allow Azure services and resources to access the server.

You can enable this option in the Azure portal by clicking **Set server firewall**, and clicking **ON** underneath **Allow Azure services and resources to access this server** for the Azure SQL Database server or database. For more information, see [Create and manage IP firewall rules](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure#use-the-azure-portal-to-manage-server-level-ip-firewall-rules)

![set server firewall button](./media/hdinsight-use-external-metadata-stores/configure-azure-sql-database-firewall1.png)

![allow azure services access](./media/hdinsight-use-external-metadata-stores/configure-azure-sql-database-firewall2.png)

### <a name="select-a-custom-metastore-during-cluster-creation"></a>클러스터를 만드는 동안 사용자 지정 metastore 선택

클러스터를 만드는 동안 클러스터로 이전에 만든 Azure SQL Database를 가리키거나 클러스터가 만들어진 후 SQL Database를 구성할 수 있습니다. This option is specified with the **Storage > Metastore settings** while creating a new Hadoop, Spark, or interactive Hive cluster from Azure portal.

![HDInsight Hive 메타데이터 저장소 Azure Portal](./media/hdinsight-use-external-metadata-stores/azure-portal-cluster-storage-metastore.png)

## <a name="hive-metastore-best-practices"></a>Hive 메타스토어 모범 사례

다음은 몇 가지 일반적인 HDInsight Hive 메타스토어 모범 사례입니다.

* 컴퓨팅 리소스(실행 중인 클러스터)와 metastore에 저장된 메타데이터를 구분할 수 있도록 가능하면 항상 사용자 지정 metastore를 사용합니다.

* 50 DTU 및 250GB의 스토리지를 제공하는 S2 계층으로 시작합니다. 병목 상태가 발생하는 경우 데이터베이스를 확장할 수 있습니다.

* 여러 HDInsight 클러스터가 별개의 데이터에 액세스하도록 하려는 경우에는 각 클러스터의 metastore용으로 별도의 데이터베이스를 사용합니다. 여러 HDInsight 클러스터에서 metastore 하나를 공유하는 경우 클러스터는 같은 메타데이터 및 기본 사용자 데이터 파일을 사용합니다.

* 사용자 지정 metastore를 정기적으로 백업합니다. Azure SQL Database는 백업을 자동으로 생성하지만 백업 보존 기간은 각기 다릅니다. 자세한 내용은 [자동 SQL 데이터베이스 백업에 대해 알아보기](../sql-database/sql-database-automated-backups.md)를 참조하세요.

* 성능은 최대화하고 네트워크 송신 비용은 최소화하려면 metastore와 HDInsight 클러스터를 같은 지역에 배치합니다.

* Monitor your metastore for performance and availability using Azure SQL Database Monitoring tools, such as the Azure portal or Azure Monitor logs.

* 기존 사용자 지정 metastore 데이터베이스용으로 최신 버전 Azure HDInsight를 새로 만들면 시스템은 metastore의 스키마를 업그레이드합니다. 이 경우 백업에서 데이터베이스를 복원하지 않으면 업그레이드를 취소할 수 없습니다.

* 여러 클러스터 간에 metastore 하나를 공유하는 경우 모든 클러스터의 HDInsight 버전이 같은지 확인해야 합니다. 각 Hive 버전이 사용하는 metastore 데이터베이스 스키마는 서로 다릅니다. For example, you can't share a metastore across Hive 2.1 and Hive 3.1 versioned clusters.

* In HDInsight 4.0, Spark and Hive use independent catalogs for accessing SparkSQL or Hive tables. A table created by Spark resides in the Spark catalog. A table created by Hive resides in the Hive catalog. This is different than HDInsight 3.6 where Hive and Spark shared common catalog. Hive and Spark Integration in HDInsight 4.0 relies on Hive Warehouse Connector (HWC). HWC works as a bridge between Spark and Hive. [Learn about Hive Warehouse Connector](../hdinsight/interactive-query/apache-hive-warehouse-connector.md).

## <a name="apache-oozie-metastore"></a>Apache Oozie metastore

Apache Oozie는 Hadoop 작업을 관리하는 워크플로 조정 시스템입니다.  Oozie는 Apache MapReduce, Pig, Hive 등에 대한 Hadoop 작업을 지원합니다.  Oozie는 metastore를 사용하여 현재 및 완료된 워크플로에 대한 세부 정보를 저장합니다. Oozie 사용 시 성능을 높이기 위해 Azure SQL Database를 사용자 지정 metastore로 사용할 수 있습니다. 클러스터를 삭제한 후 metastore가 Oozie 작업 데이터에 대한 액세스를 제공할 수도 있습니다.

Azure SQL Database를 사용하여 Oozie metastore를 만드는 방법에 대한 지침은 [워크플로에 대해 Apache Oozie 사용](hdinsight-use-oozie-linux-mac.md)을 참조하세요.

## <a name="custom-ambari-db"></a>사용자 지정 Ambari DB

To use your own external database with Apache Ambari on HDInsight, see [Custom Apache Ambari database](hdinsight-custom-ambari-db.md).

## <a name="next-steps"></a>다음 단계

* [Apache Hadoop, Apache Spark, Apache Kafka 등을 사용하여 HDInsight에서 클러스터 설정](./hdinsight-hadoop-provision-linux-clusters.md)
