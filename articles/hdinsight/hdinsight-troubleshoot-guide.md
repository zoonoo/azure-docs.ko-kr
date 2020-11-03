---
title: Azure HDInsight 문제 해결 가이드
description: Azure HDInsight 문제 해결. 단계별 설명서에서는 HDInsight를 사용하여 Apache Hive, Apache Spark, Apache YARN, Apache HBase, HDFS 및 Apache Storm의 일반적인 문제를 해결하는 방법을 보여 줍니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/14/2019
ms.openlocfilehash: 5ffd688a60f793ffa497cd0d23ed88437e56a07c
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93289037"
---
# <a name="troubleshoot-azure-hdinsight"></a>Azure HDInsight 문제 해결

| Apache 워크로드 | 가장 많이 하는 질문 |
|---|---|
|![hdinsight apache HBase 아이콘 아이콘](./media/hdinsight-troubleshoot-guide/hdinsight-apache-hbase.png)<br>[Apache HBase 문제 해결]()|<br>[할당 되지 않은 영역](hbase/hbase-troubleshoot-unassigned-regions.md#scenario-unassigned-regions)<br><br>[Azure HDInsight에서 'hbase hbck' 명령을 사용하여 시간 제한](hbase/hbase-troubleshoot-timeouts-hbase-hbck.md)<br><br>[Azure HDInsight의 Apache Phoenix 연결 문제](hbase/hbase-troubleshoot-phoenix-connectivity.md)<br><br>[마스터 서버가 시작 되지 않는 이유는 무엇 인가요?](hbase/hbase-troubleshoot-start-fails.md)<br><br>[BindException-이미 사용 중인 주소](hbase/hbase-troubleshoot-bindexception-address-use.md)|
|![hdinsight apache hdfs 아이콘 아이콘](./media/hdinsight-troubleshoot-guide/hdinsight-apache-hdfs.png)<br>[Apache Hadoop HDFS 문제 해결](hdinsight-troubleshoot-hdfs.md)|<br>[클러스터 내부에서 로컬 HDFS에 액세스하려면 어떻게 하나요?](hdinsight-troubleshoot-hdfs.md#how-do-i-access-local-hdfs-from-inside-a-cluster)<br><br>[Azure HDInsight 클러스터의 안전 모드에서 로컬 HDFS가 중단 됨](hadoop/hdinsight-hdfs-troubleshoot-safe-mode.md)|
|![hdinsight apache Hive 아이콘 아이콘](./media/hdinsight-troubleshoot-guide/hdinsight-apache-hive.png)<br>[문제 해결 Apache Hive](hdinsight-troubleshoot-hive.md)|<br>[Hive 메타스토어를 내보내고 다른 클러스터로 가져오려면 어떻게 하나요?](hdinsight-troubleshoot-hive.md#how-do-i-export-a-hive-metastore-and-import-it-on-another-cluster)<br><br>[클러스터에서 Apache Hive 로그를 찾으려면 어떻게 할까요?](hdinsight-troubleshoot-hive.md#how-do-i-locate-hive-logs-on-a-cluster)<br><br>[클러스터에서 특정 구성으로 Apache Hive 셸을 시작하려면 어떻게 하나요?](hdinsight-troubleshoot-hive.md#how-do-i-launch-the-hive-shell-with-specific-configurations-on-a-cluster)<br><br>[클러스터에 중요 한 경로에서 Apache Tez DAG 데이터를 분석 하는 어떻게 할까요?](hdinsight-troubleshoot-hive.md#how-do-i-analyze-tez-dag-data-on-a-cluster-critical-path)<br><br>[클러스터에서 Apache Tez DAG 데이터를 다운로드하려면 어떻게 하나요?](hdinsight-troubleshoot-hive.md#how-do-i-download-tez-dag-data-from-a-cluster)|
|![hdinsight apache Spark 아이콘 아이콘](./media/hdinsight-troubleshoot-guide/hdinsight-apache-spark.png)<br>[Apache Spark 문제 해결](./spark/apache-troubleshoot-spark.md)|<br>[클러스터에서 Apache Ambari를 사용하여 Apache Spark 애플리케이션을 구성하려면 어떻게 해야 하나요?](spark/apache-troubleshoot-spark.md#how-do-i-configure-an-apache-spark-application-by-using-apache-ambari-on-clusters)<br><br>[클러스터에서 Jupyter Notebook을 사용하여 Apache Spark 애플리케이션을 구성하려면 어떻게 해야 하나요?](spark/apache-troubleshoot-spark.md#how-do-i-configure-an-apache-spark-application-by-using-a-jupyter-notebook-on-clusters)<br><br>[클러스터에서 Apache Livy를 사용하여 Apache Spark 애플리케이션을 구성하려면 어떻게 해야 하나요?](spark/apache-troubleshoot-spark.md#how-do-i-configure-an-apache-spark-application-by-using-apache-livy-on-clusters)<br><br>[클러스터에서 spark-submit을 사용하여 Apache Spark 애플리케이션을 구성하려면 어떻게 해야 하나요?](spark/apache-troubleshoot-spark.md#how-do-i-configure-an-apache-spark-application-by-using-spark-submit-on-clusters)<br><br>[IntelliJ를 사용하여 Apache Spark 애플리케이션을 구성하려면 어떻게 하나요?](spark/apache-spark-intellij-tool-plugin.md)<br><br>[Eclipse를 사용하여 Apache Spark 애플리케이션을 구성하려면 어떻게 하나요?](spark/apache-spark-eclipse-tool-plugin.md)<br><br>[VSCode를 사용하여 Apache Spark 애플리케이션을 구성하려면 어떻게 하나요?](hdinsight-for-vscode.md)<br><br>[Apache Spark에 대 한 OutOfMemoryError 예외](spark/apache-spark-troubleshoot-outofmemory.md#scenario-outofmemoryerror-exception-for-apache-spark)|
|![hdinsight apache 스톰 아이콘 아이콘](./media/hdinsight-troubleshoot-guide/hdinsight-apache-storm.png)<br>[Apache Storm 문제 해결](./storm/apache-troubleshoot-storm.md)|<br>[클러스터에서 Apache Storm UI에 액세스하려면 어떻게 하나요?](storm/apache-troubleshoot-storm.md#how-do-i-access-the-storm-ui-on-a-cluster)<br><br>[한 토폴로지에서 다른 토폴로지로 Apache Storm 이벤트 허브 spout 검사점 정보를 전송하려면 어떻게 하나요?](storm/apache-troubleshoot-storm.md#how-do-i-transfer-storm-event-hub-spout-checkpoint-information-from-one-topology-to-another)<br><br>[클러스터에서 Storm 이진을 찾는 방법](storm/apache-troubleshoot-storm.md#how-do-i-locate-storm-binaries-on-a-cluster)<br><br>[Storm 클러스터의 배포 토폴로지를 확인하는 방법](storm/apache-troubleshoot-storm.md#how-do-i-determine-the-deployment-topology-of-a-storm-cluster)<br><br>[개발용 Apache Storm 이벤트 허브 spout 이진을 찾으려면 어떻게 하나요?](storm/apache-troubleshoot-storm.md#how-do-i-locate-storm-event-hub-spout-binaries-for-development)|
|![hdinsight apache YARN 아이콘 아이콘](./media/hdinsight-troubleshoot-guide/hdinsight-apache-yarn.png)<br>[Apache Hadoop YARN 문제 해결](hdinsight-troubleshoot-YARN.md)|<br>[클러스터에서 새 Apache Hadoop YARN 큐를 만들려면 어떻게 하나요?](hdinsight-troubleshoot-yarn.md#how-do-i-create-a-new-yarn-queue-on-a-cluster)<br><br>[클러스터에서 Apache Hadoop YARN 로그를 다운로드하려면 어떻게 할까요?](hdinsight-troubleshoot-yarn.md#how-do-i-download-yarn-logs-from-a-cluster)|

## <a name="hdinsight-troubleshooting-resources"></a>HDInsight 문제 해결 리소스

| 원하는 정보 | 아래 문서를 참조하세요. |
| --- | --- |
| Linux의 HDInsight 및 최적화 | - [Linux에서 HDInsight를 사용 하는 방법에 대 한 정보](hdinsight-hadoop-linux-information.md)<br>- [Apache Hadoop 메모리 및 성능 문제 해결](hdinsight-hadoop-stack-trace-error-messages.md)<br>- [Apache Hive 쿼리 성능](https://web.archive.org/web/20190217214250/https://blogs.msdn.microsoft.com/bigdatasupport/2015/08/13/troubleshooting-hive-query-performance-in-hdinsight-hadoop-cluster/) |
| 로그 및 덤프 | - [Linux에서 Apache Hadoop YARN 응용 프로그램 로그에 액세스](hdinsight-hadoop-access-yarn-app-logs-linux.md)<br>- [Linux에서 Apache Hadoop 서비스에 대한 힙 덤프 사용](hdinsight-hadoop-collect-debug-heap-dump-linux.md)|
| 오류 | - [WebHCat 오류 이해 및 해결](hdinsight-hadoop-templeton-webhcat-debug-errors.md)<br>- [OutofMemory 오류를 해결하기 위한 Apache Hive 설정](hdinsight-hadoop-hive-out-of-memory-error-oom.md) |
| 도구 | - [Apache Hive 쿼리 최적화](hdinsight-hadoop-optimize-hive-query.md)<br>- [HDInsight IntelliJ 도구](./spark/apache-spark-intellij-tool-plugin.md)<br>- [HDInsight Eclipse 도구](./spark/apache-spark-eclipse-tool-plugin.md)<br>- [HDInsight VSCode 도구](hdinsight-for-vscode.md)<br>- [HDInsight Visual Studio 도구](./hadoop/apache-hadoop-visual-studio-tools-get-started.md) |

## <a name="next-steps"></a>다음 단계

[!INCLUDE [troubleshooting next steps](../../includes/hdinsight-troubleshooting-next-steps.md)]
