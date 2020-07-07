---
title: Azure HDInsight에 대 한 Apache Spark 지침
description: Azure HDInsight에서 Apache Spark 사용에 대 한 지침을 알아봅니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/28/2020
ms.openlocfilehash: 01cdc121abded954c2443599c5d69689acd69b62
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "82562341"
---
# <a name="apache-spark-guidelines"></a>Apache Spark 지침

이 문서에서는 Azure HDInsight에서 Apache Spark를 사용 하기 위한 다양 한 지침을 제공 합니다.

## <a name="how-do-i-run-or-submit-spark-jobs"></a>Spark 작업을 실행 하거나 제출할 어떻게 할까요? 있나요?

| 옵션 | 문서 |
|---|---|
| VSCode | [Visual Studio Code용 Spark & Hive Tools 사용](../hdinsight-for-vscode.md) |
| Jupyter Notebook | [자습서: Azure HDInsight의 Apache Spark 클러스터에서 데이터 로드 및 쿼리 실행](./apache-spark-load-data-run-query.md) |
| IntelliJ | [자습서: Azure Toolkit for IntelliJ을 사용 하 여 HDInsight 클러스터용 Apache Spark 응용 프로그램 만들기](./apache-spark-intellij-tool-plugin.md) |
| IntelliJ | [자습서: IntelliJ를 사용하여 HDInsight의 Apache Spark용 Scala Maven 애플리케이션 만들기](./apache-spark-create-standalone-application.md) |
| Zeppelin Notebooks | [Azure HDInsight에서 Apache Spark 클러스터와 함께 Apache Zeppelin Notebook 사용](./apache-spark-zeppelin-notebook.md) |
| Livy를 사용 하 여 원격 작업 제출 | [Apache Spark REST API를 사용하여 HDInsight Spark 클러스터에 원격 작업 제출](./apache-spark-livy-rest-interface.md) |
|[Apache Oozie](../hdinsight-use-oozie-linux-mac.md)|Oozie는 Hadoop 작업을 관리하는 워크플로 및 코디네이션 시스템입니다.|
|[Apache Livy](./apache-spark-livy-rest-interface.md)|Livy를 사용하여 대화형 Spark 셸을 실행하거나 Spark에서 실행되도록 배치 작업을 제출할 수 있습니다.|
|[Apache Spark에 대 한 Azure Data Factory](../../data-factory/transform-data-using-spark.md)|Data Factory 파이프라인의 Spark 작업은 사용자 고유의 또는 [주문형 HDInsight 클러스터에서 Spark 프로그램을 실행 합니다.|
|[Apache Hive에 대 한 Azure Data Factory](../../data-factory/transform-data-using-hadoop-hive.md)|Data Factory 파이프라인의 HDInsight Hive 작업은 사용자 고유 또는 주문형 HDInsight 클러스터에서 Hive 쿼리를 실행 합니다.|

## <a name="how-do-i-monitor-and-debug-spark-jobs"></a>Spark 작업을 모니터링 하 고 디버그할 어떻게 할까요? 있나요?

| 옵션 | 문서 |
|---|---|
| IntelliJ용 Azure 도구 키트 | [Azure Toolkit for IntelliJ를 사용 하 여 spark 작업 디버깅 실패 (미리 보기)](apache-spark-intellij-tool-failure-debug.md) |
| SSH를 통해 Azure Toolkit for IntelliJ | [Azure Toolkit for IntelliJ를 사용하여 SSH를 통해 HDInsight 클러스터에서 로컬 또는 원격으로 Apache Spark 애플리케이션 디버그](apache-spark-intellij-tool-debug-remotely-through-ssh.md) |
| VPN을 통한 Azure Toolkit for IntelliJ | [Azure Toolkit for IntelliJ를 사용하여 VPN을 통해 HDInsight에서 원격으로 Apache Spark 애플리케이션 디버그](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md) |
| Apache Spark 기록 서버의 작업 그래프 | [확장된 Apache Spark 기록 서버를 사용하여 Apache Spark 애플리케이션 디버그 및 진단](./apache-azure-spark-history-server.md) |

## <a name="how-do-i-make-my-spark-jobs-run-more-efficiently"></a>Spark 작업을 보다 효율적으로 실행 하는 어떻게 할까요?

| 옵션 | 문서 |
|---|---|
| IO 캐시 | [Azure HDInsight IO 캐시를 사용하여 Apache Spark 워크로드의 성능 개선(미리 보기)](./apache-spark-improve-performance-iocache.md) |
| 구성 옵션 | [Apache Spark 작업 최적화](./apache-spark-perf.md) |

## <a name="how-do-i-connect-to-other-azure-services"></a>다른 Azure 서비스에 연결 어떻게 할까요??

| 옵션 | 문서 |
|---|---|
| HDInsight의 Apache Hive | [Hive Warehouse 커넥터를 사용하여 Apache Spark 및 Apache Hive 통합](../interactive-query/apache-hive-warehouse-connector.md) |
| HDInsight의 Apache HBase | [Apache Spark를 사용하여 Apache HBase 데이터 읽기 및 쓰기](../hdinsight-using-spark-query-hbase.md) |
| HDInsight의 Apache Kafka | [자습서: HDInsight에서 Apache Kafka의 Apache Spark 정형 스트림 사용](../hdinsight-apache-kafka-spark-structured-streaming.md) |
| Azure Cosmos DB | [Azure Cosmos DB: Azure 플랫폼에 람다 아키텍처 구현](../../cosmos-db/lambda-architecture.md) |

## <a name="what-are-my-storage-options"></a>내 저장소 옵션은 무엇 인가요?

| 옵션 | 문서 |
|---|---|
| Data Lake Storage Gen2 | [Azure HDInsight 클러스터에 Azure Data Lake Storage Gen2 사용](../hdinsight-hadoop-use-data-lake-storage-gen2.md) |
| Data Lake Storage Gen1 | [Azure HDInsight 클러스터에 Data Lake Storage Gen1 사용](../hdinsight-hadoop-use-data-lake-store.md) |
| Azure Blob Storage | [Azure HDInsight 클러스터에서 Azure Storage 사용](../hdinsight-hadoop-use-blob-storage.md) |

## <a name="next-steps"></a>다음 단계

* [Apache Spark 설정 구성](apache-spark-settings.md)
* [HDInsight에서 Apache Spark 작업 최적화](apache-spark-perf.md)
