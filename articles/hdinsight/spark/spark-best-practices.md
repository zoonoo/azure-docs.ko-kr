---
title: Azure HDInsight에 대한 아파치 스파크 모범 사례
description: Azure HDInsight에서 아파치 스파크를 사용하는 모범 사례를 알아봅니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 09/18/2019
ms.openlocfilehash: 20033e52e862f086d1491c06d38cdf4f2c57ba8d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "71106126"
---
# <a name="apache-spark-best-practices"></a>아파치 스파크 모범 사례

이 문서에서는 Azure HDInsight에서 아파치 스파크를 사용하기 위한 다양한 모범 사례를 제공합니다.

## <a name="how-do-i-run-or-submit-spark-jobs"></a>Spark 작업을 실행하거나 제출하려면 어떻게 해야 합니까?

| 옵션 | 문서 |
|---|---|
| VSCode | [비주얼 스튜디오 코드에 스파크 & 하이브 도구 사용](../hdinsight-for-vscode.md) |
| Jupyter Notebook | [자습서: Azure HDInsight의 Apache Spark 클러스터에서 데이터 로드 및 쿼리 실행](./apache-spark-load-data-run-query.md) |
| IntelliJ | [자습서: IntelliJ용 Azure 도구 키트를 사용하여 HDInsight 클러스터에 대한 아파치 스파크 응용 프로그램을 만듭니다.](./apache-spark-intellij-tool-plugin.md) |
| IntelliJ | [자습서: IntelliJ를 사용하여 HDInsight의 Apache Spark에 대한 Scala Maven 애플리케이션 만들기](./apache-spark-create-standalone-application.md) |
| Zeppelin Notebooks | [Azure HDInsight에서 Apache Spark 클러스터와 함께 Apache Zeppelin Notebook 사용](./apache-spark-zeppelin-notebook.md) |
| 리비와 원격 작업 제출 | [Apache Spark REST API를 사용하여 HDInsight Spark 클러스터에 원격 작업 제출](./apache-spark-livy-rest-interface.md) |

## <a name="how-do-i-monitor-and-debug-spark-jobs"></a>스파크 작업을 모니터링하고 디버깅하려면 어떻게 해야 합니까?

| 옵션 | 문서 |
|---|---|
| IntelliJ용 Azure 도구 키트 | [IntelliJ용 Azure 도구 키트를 사용하여 작업 디버깅을 스파크(미리 보기)](apache-spark-intellij-tool-failure-debug.md) |
| SSH를 통해 IntelliJ에 대한 Azure 도구 키트 | [Azure Toolkit for IntelliJ를 사용하여 SSH를 통해 HDInsight 클러스터에서 로컬 또는 원격으로 Apache Spark 애플리케이션 디버그](apache-spark-intellij-tool-debug-remotely-through-ssh.md) |
| VPN을 통해 IntelliJ에 대한 Azure 도구 키트 | [Azure Toolkit for IntelliJ를 사용하여 VPN을 통해 HDInsight에서 원격으로 Apache Spark 애플리케이션 디버그](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md) |
| 아파치 스파크 히스토리 서버의 작업 그래프 | [확장된 Apache Spark 기록 서버를 사용하여 Apache Spark 애플리케이션 디버그 및 진단](./apache-azure-spark-history-server.md) |

## <a name="how-do-i-make-my-spark-jobs-run-more-efficiently"></a>스파크 작업을 보다 효율적으로 실행하려면 어떻게 해야 합니까?

| 옵션 | 문서 |
|---|---|
| IO 캐시 | [Azure HDInsight IO 캐시를 사용하여 Apache Spark 워크로드의 성능 개선(미리 보기)](./apache-spark-improve-performance-iocache.md) |
| 구성 옵션 | [Apache Spark 작업 최적화](./apache-spark-perf.md) |

## <a name="how-do-i-connect-to-other-azure-services"></a>다른 Azure 서비스에 연결하려면 어떻게 해야 합니까?

| 옵션 | 문서 |
|---|---|
| HDInsight의 Apache Hive | [Hive Warehouse 커넥터를 사용하여 Apache Spark 및 Apache Hive 통합](../interactive-query/apache-hive-warehouse-connector.md) |
| HDInsight의 Apache HBase | [Apache Spark를 사용하여 Apache HBase 데이터 읽기 및 쓰기](../hdinsight-using-spark-query-hbase.md) |
| HDInsight의 Apache Kafka | [자습서: HDInsight에서 Apache Kafka의 Apache Spark 구조적 스트림 사용](../hdinsight-apache-kafka-spark-structured-streaming.md) |
| Azure Cosmos DB | [Azure Cosmos DB: Azure 플랫폼에 람다 아키텍처 구현](../../cosmos-db/lambda-architecture.md) |

## <a name="what-are-my-storage-options"></a>내 저장소 옵션은 무엇입니까?

| 옵션 | 문서 |
|---|---|
| Data Lake Storage Gen2 | [Azure HDInsight 클러스터에 Azure Data Lake Storage Gen2 사용](../hdinsight-hadoop-use-data-lake-storage-gen2.md) |
| Data Lake Storage Gen1 | [Azure HDInsight 클러스터에 Data Lake Storage Gen1 사용](../hdinsight-hadoop-use-data-lake-store.md) |
| Azure Blob Storage | [Azure HDInsight 클러스터에서 Azure Storage 사용](../hdinsight-hadoop-use-blob-storage.md) |

## <a name="next-steps"></a>다음 단계

* [Apache Spark 설정 구성](apache-spark-settings.md)
* [HDInsight에서 아파치 스파크 작업 최적화](apache-spark-perf.md)
