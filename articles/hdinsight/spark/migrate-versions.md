---
title: Apache Spark 2.1 또는 2.2 워크로드에서 2.3 또는 2.4로 마이그레이션 - Azure HDInsight
description: Apache Spark 2.1 및 2.2에서 2.3 또는 2.4로 마이그레이션하는 방법을 알아봅니다.
author: ashishthaps1
ms.author: ashishth
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/20/2020
ms.openlocfilehash: 1452a22303af169e9501c85336785f7627ac7e88
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83727421"
---
# <a name="migrate-apache-spark-21-and-22-workloads-to-23-and-24"></a>Apache Spark 2.1 및 2.2 워크로드를 2.3 및 2.4로 마이그레이션

이 문서에서는 Spark 2.1 및 2.2에서 Apache Spark 워크로드를 2.3 또는 2.4로 마이그레이션하는 방법을 설명합니다.

2020년 7월 1일부터 [릴리스 정보](../hdinsight-release-notes.md#upcoming-changes)에 설명된 대로 다음 클러스터 구성은 지원되지 않으며, 이러한 구성으로 새 클러스터를 만들 수 없게 됩니다.
 - HDInsight 3.6 Spark 클러스터의 Spark 2.1 및 2.2
 - HDInsight 4.0 Spark 클러스터의 Spark 2.3

이러한 구성의 기존 클러스터는 Microsoft의 지원 없이 있는 그대로 실행됩니다. HDInsight 3.6에서 Spark 2.1 또는 2.2를 사용하는 경우 잠재적인 시스템/지원 중단을 방지하기 위해 2020년 6월 30일까지 HDInsight 3.6의 Spark 2.3으로 전환합니다. HDInsight 4.0 클러스터에서 Spark 2.3을 사용하는 경우 잠재적인 시스템/지원 중단을 방지하기 위해 2020년 6월 30일까지 HDInsight 4.0의 Spark 2.4로 전환합니다.

HDInsight 클러스터를 3.6에서 4.0으로 마이그레이션하는 방법에 대한 일반적인 정보는 [HDInsight 클러스터를 최신 버전으로 마이그레이션](../hdinsight-upgrade-cluster.md)을 참조하세요. 최신 버전의 Apache Spark로 마이그레이션하는 방법에 대한 일반적인 정보는 [Apache Spark: 버전 관리 정책](https://spark.apache.org/versioning-policy.html)을 참조하세요.

## <a name="guidance-on-spark-version-upgrades-on-hdinsight"></a>HDInsight의 Spark 버전 업그레이드에 대한 지침

| 업그레이드 시나리오 | 메커니즘 | 고려할 사항 | Spark Hive 통합 |
|------------------|-----------|--------------------|------------------------|
|HDInsight 3.6 Spark 2.1에서 HDInsight 3.6 Spark 2.3으로| HDInsight Spark 2.3을 사용하여 클러스터 다시 만들기 | 다음 문서를 검토합니다. <br> [Apache Spark: Spark SQL 2.2에서 2.3으로 업그레이드](https://spark.apache.org/docs/latest/sql-migration-guide-upgrade.html#upgrading-from-spark-sql-22-to-23) <br><br> [Apache Spark: Spark SQL 2.1에서 2.2로 업그레이드](https://spark.apache.org/docs/latest/sql-migration-guide-upgrade.html#upgrading-from-spark-sql-21-to-22) | 변경 내용 없음 |
|HDInsight 3.6 Spark 2.2에서 HDInsight 3.6 Spark 2.3으로 | HDInsight Spark 2.3을 사용하여 클러스터 다시 만들기 | 다음 문서를 검토합니다. <br> [Apache Spark: Spark SQL 2.2에서 2.3으로 업그레이드](https://spark.apache.org/docs/latest/sql-migration-guide-upgrade.html#upgrading-from-spark-sql-22-to-23) | 변경 내용 없음 |
| HDInsight 3.6 Spark 2.1에서 HDInsight 4.0 Spark 2.4로 | HDInsight 4.0 Spark 2.4를 사용하여 클러스터 다시 만들기 | 다음 문서를 검토합니다. <br> [Apache Spark: Spark SQL 2.3에서 2.4로 업그레이드](https://spark.apache.org/docs/latest/sql-migration-guide-upgrade.html#upgrading-from-spark-sql-23-to-24) <br><br> [Apache Spark: Spark SQL 2.2에서 2.3으로 업그레이드](https://spark.apache.org/docs/latest/sql-migration-guide-upgrade.html#upgrading-from-spark-sql-22-to-23) <br><br> [Apache Spark: Spark SQL 2.1에서 2.2로 업그레이드](https://spark.apache.org/docs/latest/sql-migration-guide-upgrade.html#upgrading-from-spark-sql-21-to-22) | HDInsight 4.0에서 Spark Hive 통합이 변경되었습니다. <br><br> HDInsight 4.0에서 Spark 및 Hive는 SparkSQL 또는 Hive 테이블에 액세스하기 위해 독립적인 카탈로그를 사용합니다. Spark에서 만든 테이블은 Spark 카탈로그에 있습니다. Hive에서 만든 테이블은 Hive 카탈로그에 있습니다. 이 동작은 Hive 및 Spark 공유 공통 카탈로그가 있는 HDInsight 3.6과는 다릅니다. HDInsight 4.0의 Hive 및 Spark 통합은 HWC(Hive Warehouse Connector)에 의존합니다. HWC는 Spark와 Hive 간에 브리지 역할을 합니다. Hive Warehouse Connector에 대해 알아봅니다. <br> HDInsight 4.0에서 Hive와 Spark 간에 metastore을 공유하려는 경우 Spark 클러스터에서 metastore.catalog.default 속성을 hive로 변경하면 됩니다. Ambari 고급 spark2-hive-site-override에서 이 속성을 찾을 수 있습니다. metastore의 공유는 외부 hive 테이블에서만 작동합니다. 내부/관리형 hive 테이블 또는 ACID 테이블이 있는 경우에는 이 공유가 작동하지 않습니다. <br><br>자세한 내용은 [Azure HDInsight 3.6 Hive 워크로드를 HDInsight 4.0으로 마이그레이션](../interactive-query/apache-hive-migrate-workloads.md)을 참조하세요.<br><br> |
| HDInsight 3.6 Spark 2.2에서 HDInsight 4.0 Spark 2.4로 | HDInsight 4.0 Spark 2.4를 사용하여 클러스터 다시 만들기 | 다음 문서를 검토합니다. <br> [Apache Spark: Spark SQL 2.3에서 2.4로 업그레이드](https://spark.apache.org/docs/latest/sql-migration-guide-upgrade.html#upgrading-from-spark-sql-23-to-24) <br><br> [Apache Spark: Spark SQL 2.2에서 2.3으로 업그레이드](https://spark.apache.org/docs/latest/sql-migration-guide-upgrade.html#upgrading-from-spark-sql-22-to-23) | HDInsight 4.0에서 Spark Hive 통합이 변경되었습니다. <br><br> HDInsight 4.0에서 Spark 및 Hive는 SparkSQL 또는 Hive 테이블에 액세스하기 위해 독립적인 카탈로그를 사용합니다. Spark에서 만든 테이블은 Spark 카탈로그에 있습니다. Hive에서 만든 테이블은 Hive 카탈로그에 있습니다. 이 동작은 Hive 및 Spark 공유 공통 카탈로그가 있는 HDInsight 3.6과는 다릅니다. HDInsight 4.0의 Hive 및 Spark 통합은 HWC(Hive Warehouse Connector)에 의존합니다. HWC는 Spark와 Hive 간에 브리지 역할을 합니다. Hive Warehouse Connector에 대해 알아봅니다. <br> HDInsight 4.0에서 Hive와 Spark 간에 metastore을 공유하려는 경우 Spark 클러스터에서 metastore.catalog.default 속성을 hive로 변경하면 됩니다. Ambari 고급 spark2-hive-site-override에서 이 속성을 찾을 수 있습니다. metastore의 공유는 외부 hive 테이블에서만 작동합니다. 내부/관리형 hive 테이블 또는 ACID 테이블이 있는 경우에는 이 공유가 작동하지 않습니다. <br><br>자세한 내용은 [Azure HDInsight 3.6 Hive 워크로드를 HDInsight 4.0으로 마이그레이션](../interactive-query/apache-hive-migrate-workloads.md)을 참조하세요.|

## <a name="next-steps"></a>다음 단계

* [HDInsight 클러스터를 최신 버전으로 마이그레이션](../hdinsight-upgrade-cluster.md)
* [Azure HDInsight 3.6 Hive 워크로드를 HDInsight 4.0으로 마이그레이션](../interactive-query/apache-hive-migrate-workloads.md)
