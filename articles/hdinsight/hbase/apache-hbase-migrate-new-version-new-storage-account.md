---
title: HBase 클러스터를 새 버전 및 스토리지 계정으로 마이그레이션 - Azure HDInsight
description: 다른 Azure Storage 계정을 사용하여 Azure HDInsight의 Apache HBase 클러스터를 최신 버전으로 마이그레이션하는 방법을 알아봅니다.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 05/06/2021
ms.openlocfilehash: 4ed4de8c134575bba1b961f918216eb3cca9b6b1
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111963988"
---
# <a name="migrate-apache-hbase-to-a-new-version-and-storage-account"></a>새 버전 및 스토리지 계정으로 Apache HBase 마이그레이션

이 문서에서는 Azure HDInsight의 Apache HBase 클러스터를 다른 Azure Storage 계정을 사용하여 최신 버전으로 업데이트하는 방법을 설명합니다.

이 문서는 원본 및 대상 클러스터에 대해 다른 스토리지 계정을 사용해야 하는 경우에만 적용됩니다. 원본 및 대상 클러스터에 대해 동일한 스토리지 계정을 사용하여 버전을 업그레이드하려면 [Apache HBase를 새 버전으로 마이그레이션](apache-hbase-migrate-new-version.md)을 참조하세요.

업그레이드하는 동안 가동 중지 시간은 몇 분 정도만 지속되어야 합니다. 이 가동 중지 시간은 모든 메모리 내 데이터를 플러시하는 단계로 인해 발생하며, 그런 후에 새 클러스터에서 서비스를 구성하고 다시 시작합니다. 결과는 노드 수, 데이터 양 및 기타 변수에 따라 달라집니다.

## <a name="review-apache-hbase-compatibility"></a>Apache HBase 호환성 검토

Apache HBase를 업그레이드하기 전에 원본 및 대상 클러스터의 HBase 버전이 호환되는지 확인합니다. [Hbase 참조 가이드](https://hbase.apache.org/book.html#upgrading)의 HBase 버전 호환성 매트릭스와 릴리스 정보를 검토하여 애플리케이션이 새 버전과 호환되는지 확인합니다.

예제 호환성 매트릭스는 다음과 같습니다. Y는 호환성을 나타내고 N은 잠재적인 비호환성을 나타냅니다.

| 호환성 유형 | 주 버전| 부 버전 | 패치 |
| --- | --- | --- | --- |
| 클라이언트-서버 통신 호환성 | N | Y | Y |
| 서버 간 호환성 | N | Y | Y |
| 파일 형식 호환성 | N | Y | Y |
| 클라이언트 API 호환성 | N | Y | Y |
| 클라이언트 이진 호환성 | N | N | Y |
| **서버 쪽 제한 API 호환성** |  |  |  |
| Stable | N | Y | Y |
| 진화 | N | N | Y |
| 불안정 | N | N | N |
| 종속성 호환성 | N | Y | Y |
| 운영 호환성 | N | N | Y |

HBase 버전 릴리스 정보는 주요 비호환성을 설명합니다. HDInsight 및 HBase의 대상 버전을 실행하는 클러스터에서 애플리케이션을 테스트합니다.

HDInsight 버전 및 호환성에 대한 자세한 내용은 [Azure HDInsight 버전](../hdinsight-component-versioning.md)을 참조하세요.

## <a name="apache-hbase-cluster-migration-overview"></a>Apache HBase 클러스터 마이그레이션 개요

Azure HDInsight의 Apache HBase 클러스터를 업그레이드하고 새 스토리지 계정으로 마이그레이션하려면 다음과 같은 기본 단계를 완료합니다. 자세한 지침은 자세한 단계 및 명령을 참조하세요.

원본 클러스터 준비:
1. 데이터 수집을 중지합니다.
1. memstore 데이터를 플러시합니다.
1. Ambari에서 HBase를 중지합니다.
1. 가속화된 쓰기를 포함하는 클러스터의 경우 WAL(미리 쓰기 로그) 디렉터리를 백업합니다.

대상 서버 준비:
1. 대상 클러스터를 만듭니다.
1. Ambari에서 HBase를 중지합니다.
1. Zookeeper 데이터를 정리합니다.
1. 사용자를 HBase로 전환합니다.

마이그레이션 완료:
1. 대상 파일 시스템을 정리하고, 데이터를 마이그레이션하고, `/hbase/hbase.id`를 제거합니다.
1. WAL을 정리하고 마이그레이션합니다.
1. Ambari 대상 클러스터에서 모든 서비스를 시작합니다.
1. HBase를 확인합니다.
1. 원본 클러스터를 삭제합니다.

## <a name="detailed-migration-steps-and-commands"></a>자세한 마이그레이션 단계 및 명령

이러한 자세한 단계 및 명령을 사용하여 새 스토리지 계정으로 Apache HBase 클러스터를 마이그레이션합니다.

### <a name="prepare-the-source-cluster"></a>원본 클러스터 준비

1. 원본 HBase 클러스터에 대한 수집을 중지합니다.
   
1. 업그레이드하는 원본 HBase 클러스터를 플러시합니다.
   
   HBase는 들어오는 데이터를 *memstore* 라는 메모리 내 저장소에 씁니다. memstore가 특정 크기에 도달한 후 HBase는 장기 저장을 위해 클러스터의 스토리지 계정에 있는 디스크로 플러시됩니다. 업그레이드 후 원본 클러스터를 삭제하면 memstore의 데이터도 모두 삭제됩니다. 데이터를 유지하려면 업그레이드하기 전에 각 테이블의 memstore를 디스크에 수동으로 플러시합니다.
   
   [hbase-utils GitHub 리포지토리](https://github.com/Azure/hbase-utils/)에서 [flush_all_tables.sh](https://github.com/Azure/hbase-utils/blob/master/scripts/flush_all_tables.sh) 스크립트를 실행하여 memstore 데이터를 플러시할 수 있습니다.
   
   HDInsight 클러스터 내에서 다음 HBase 셸 명령을 실행하여 memstore 데이터를 플러시할 수도 있습니다.
   
   ```bash
   hbase shell
   flush "<table-name>"
   ```
   
1. `https://<OLDCLUSTERNAME>.azurehdinsight.net`을 사용하여 원본 클러스터에서 [Apache Ambari](https://ambari.apache.org/)에 로그인하고 HBase 서비스를 중지합니다.
   
1. 확인 메시지에서 HBase에 대한 유지 관리 모드를 설정하는 상자를 선택합니다.
   
   Ambari 연결 및 사용에 대한 자세한 내용은 [Ambari 웹 UI를 사용하여 HDInsight 클러스터 관리](../hdinsight-hadoop-manage-ambari.md)를 참조하세요.
   
1. 원본 HBase 클러스터에 [가속화된 쓰기](apache-hbase-accelerated-writes.md) 기능이 없는 경우 이 단계를 건너뜁니다. 가속화된 쓰기를 사용하는 원본 HBase 클러스터의 경우 모든 원본 Zookeeper 노드 또는 작업자 노드의 SSH 세션에서 다음 명령을 실행하여 HDFS 아래의 WAL 디렉터리를 백업합니다.
   
   ```bash
   hdfs dfs -mkdir /hbase-wal-backup
   hdfs dfs -cp hdfs://mycluster/hbasewal /hbase-wal-backup
   ```

### <a name="prepare-the-destination-cluster"></a>대상 서버 준비

1. Azure Portal에서 원본 클러스터와 다른 스토리지 계정을 사용하는 [새 대상 HDInsight 클러스터를 설정](../hdinsight-hadoop-provision-linux-clusters.md)합니다.
   
1. `https://<NEWCLUSTERNAME>.azurehdinsight.net`의 새 클러스터에서 [Apache Ambari](https://ambari.apache.org/)에 로그인하고 HBase 서비스를 중지합니다.
   
1. Zookeeper 노드 또는 작업자 노드에서 다음 명령을 실행하여 대상 클러스터의 Zookeeper 데이터를 정리합니다.
   
   ```bash
   hbase zkcli
   rmr /hbase-unsecure
   quit
   ```
   
1. `sudo su hbase`를 실행하여 사용자를 HBase로 전환합니다.

### <a name="clean-and-migrate-the-file-system-and-wal"></a>파일 시스템/WAL 정리 및 마이그레이션

원본 HDI 버전과 원본 및 대상 클러스터에 가속화된 쓰기가 있는지 여부에 따라 다음 명령을 실행합니다. HDI 3.6은 기본 지원에 속하며 새 클러스터에는 권장되지 않기 때문에 대상 클러스터는 항상 HDI 버전 4.0입니다.

- [원본 클러스터는 가속화된 쓰기가 있는 HDI 3.6이며 대상 클러스터에는 가속화된 쓰기가 있습니다](#the-source-cluster-is-hdi-36-or-hdi-40-with-accelerated-writes-and-the-destination-cluster-has-accelerated-writes).
- [원본 클러스터는 가속화된 쓰기가 없는 HDI 3.6이며 대상 클러스터에는 가속화된 쓰기가 있습니다](#the-source-cluster-is-hdi-36-without-accelerated-writes-and-the-destination-cluster-has-accelerated-writes).
- [원본 클러스터는 가속화된 쓰기가 없는 HDI 3.6이며 대상 클러스터에는 가속화된 쓰기가 없습니다](#the-source-cluster-is-hdi-36-without-accelerated-writes-and-the-destination-cluster-doesnt-have-accelerated-writes).
- [원본 클러스터는 가속화된 쓰기가 있는 HDI 4.0이며 대상 클러스터에는 가속화된 쓰기가 있습니다](#the-source-cluster-is-hdi-36-or-hdi-40-with-accelerated-writes-and-the-destination-cluster-has-accelerated-writes).
- [원본 클러스터는 가속화된 쓰기가 없는 HDI 4.0이며 대상 클러스터에는 가속화된 쓰기가 있습니다](#the-source-cluster-is-hdi-40-without-accelerated-writes-and-the-destination-cluster-has-accelerated-writes).
- [원본 클러스터는 가속화된 쓰기가 없는 HDI 4.0이며 대상 클러스터에는 가속화된 쓰기가 없습니다](#the-source-cluster-is-hdi-40-without-accelerated-writes-and-the-destination-cluster-doesnt-have-accelerated-writes).

스토리지 계정의 `<container-endpoint-url>`는 `https://<storageaccount>.blob.core.windows.net/<container-name>`입니다. URL의 맨 끝에 스토리지 계정에 대한 SAS 토큰을 지정합니다.

- 스토리지 유형 WASB에 대한 `<container-fullpath>`는 `wasbs://<container-name>@<storageaccount>.blob.core.windows.net`입니다.
- 스토리지 유형 Azure Data Lake Storage Gen2에 대한 `<container-fullpath>`는 `abfs://<container-name>@<storageaccount>.dfs.core.windows.net`입니다.

#### <a name="copy-commands"></a>복사 명령

HDFS 복사 명령은 `hdfs dfs <copy properties starting with -D> -cp`입니다.
 
복사할 파일이 페이지 Blob에 없는 경우 더 나은 성능을 위해 `hadoop distcp`를 사용합니다. `hadoop distcp <copy properties starting with -D>`
 
스토리지 계정의 키를 전달하려면 다음을 사용합니다.
- `-Dfs.azure.account.key.<storageaccount>.blob.core.windows.net='<storage account key>'`
- `-Dfs.azure.account.keyprovider.<storageaccount>.blob.core.windows.net=org.apache.hadoop.fs.azure.SimpleKeyProvider`

HBase 데이터 파일을 복사할 때 더 나은 성능을 위해 [AzCopy](../../storage/common/storage-ref-azcopy.md)를 사용할 수도 있습니다.
   
1. AzCopy 명령 실행:
   
   ```bash
   azcopy cp "<source-container-endpoint-url>/hbase" "<target-container-endpoint-url>" --recursive
   ```

1. 대상 스토리지 계정이 Azure Blob Storage인 경우 복사 후에 이 단계를 수행합니다. 대상 스토리지 계정이 Data Lake Storage Gen2인 경우 이 단계를 건너뜁니다.
   
   Hadoop WASB 드라이버는 모든 디렉터리에 해당하는 특수한 0 크기 Blob을 사용합니다. AzCopy는 복사를 수행할 때 이러한 파일을 건너뜁니다. 일부 WASB 작업은 이러한 Blob을 사용하므로 대상 클러스터에서 만들어야 합니다. Blob을 만들려면 대상 클러스터의 임의 노드에서 다음 Hadoop 명령을 실행합니다.
   
   ```bash
   sudo -u hbase hadoop fs -chmod -R 0755 /hbase
   ```

[AzCopy 시작](../../storage/common/storage-use-azcopy-v10.md)에서 AzCopy를 다운로드할 수 있습니다. AzCopy 사용에 대한 자세한 내용은 [azcopy copy](../../storage/common/storage-ref-azcopy-copy.md)를 참조하세요.

#### <a name="the-source-cluster-is-hdi-36-or-hdi-40-with-accelerated-writes-and-the-destination-cluster-has-accelerated-writes"></a>원본 클러스터는 가속화된 쓰기가 있는 HDI 3.6 또는 HDI 4.0이며 대상 클러스터에는 가속화된 쓰기가 있습니다.

1. 파일 시스템을 정리하고 데이터를 마이그레이션하려면 다음 명령을 실행합니다.
   
   ```bash
   hdfs dfs -rm -r /hbase 
   hadoop distcp <source-container-fullpath>/hbase /
   ```
   
1. `hdfs dfs -rm /hbase/hbase.id`를 실행하여 `hbase.id` 제거
   
1. WAL을 정리하고 마이그레이션하려면 다음 명령을 실행합니다.
   
   ```bash
   hdfs dfs -rm -r hdfs://<destination-cluster>/hbasewal
   hdfs dfs -cp <source-container-fullpath>/hbase-wal-backup/hbasewal hdfs://<destination-cluster>/hbasewal
   ```

#### <a name="the-source-cluster-is-hdi-36-without-accelerated-writes-and-the-destination-cluster-has-accelerated-writes"></a>원본 클러스터는 가속화된 쓰기가 없는 HDI 3.6이며 대상 클러스터에는 가속화된 쓰기가 있습니다.

1. 파일 시스템을 정리하고 데이터를 마이그레이션하려면 다음 명령을 실행합니다.
   
   ```bash
   hdfs dfs -rm -r /hbase 
   hdfs dfs -Dfs.azure.page.blob.dir="/hbase/WALs,/hbase/MasterProcWALs,/hbase/oldWALs,/hbase-wals" -cp <source-container-fullpath>/hbase /
   hdfs dfs -rm -r /hbase/*WALs
   ```
   
1. `hdfs dfs -rm /hbase/hbase.id`를 실행하여 `hbase.id` 제거
   
1. WAL을 정리하고 마이그레이션하려면 다음 명령을 실행합니다.
   
   ```bash
   hdfs dfs -rm -r hdfs://<destination-cluster>/hbasewal/*
   hdfs dfs -Dfs.azure.page.blob.dir="/hbase/WALs,/hbase/MasterProcWALs,/hbase/oldWALs,/hbase-wals" -cp <source-container-fullpath>/hbase/*WALs hdfs://<destination-cluster>/hbasewal
   ```

#### <a name="the-source-cluster-is-hdi-36-without-accelerated-writes-and-the-destination-cluster-doesnt-have-accelerated-writes"></a>원본 클러스터는 가속화된 쓰기가 없는 HDI 3.6이며 대상 클러스터에는 가속화된 쓰기가 없습니다.

1. 파일 시스템을 정리하고 데이터를 마이그레이션하려면 다음 명령을 실행합니다.
   
   ```bash
   hdfs dfs -rm -r /hbase 
   hdfs dfs -Dfs.azure.page.blob.dir="/hbase/WALs,/hbase/MasterProcWALs,/hbase/oldWALs,/hbase-wals" -cp <source-container-fullpath>/hbase /
   hdfs dfs -rm -r /hbase/*WALs
   ```
   
1. `hdfs dfs -rm /hbase/hbase.id`를 실행하여 `hbase.id` 제거
   
1. WAL을 정리하고 마이그레이션하려면 다음 명령을 실행합니다.
   
   ```bash
   hdfs dfs -rm -r /hbase-wals/*
   hdfs dfs -Dfs.azure.page.blob.dir="/hbase/WALs,/hbase/MasterProcWALs,/hbase/oldWALs,/hbase-wals" -cp <source-container-fullpath>/hbase/*WALs /hbase-wals
   ```

#### <a name="the-source-cluster-is-hdi-40-without-accelerated-writes-and-the-destination-cluster-has-accelerated-writes"></a>원본 클러스터는 가속화된 쓰기가 없는 HDI 4.0이며 대상 클러스터에는 가속화된 쓰기가 있습니다.

1. 파일 시스템을 정리하고 데이터를 마이그레이션하려면 다음 명령을 실행합니다.
   
   ```bash
   hdfs dfs -rm -r /hbase 
   hadoop distcp <source-container-fullpath>/hbase /
   ```
   
1. `hdfs dfs -rm /hbase/hbase.id`를 실행하여 `hbase.id` 제거
   
1. WAL을 정리하고 마이그레이션하려면 다음 명령을 실행합니다.
   
   ```bash
   hdfs dfs -rm -r hdfs://<destination-cluster>/hbasewal
   hdfs dfs -Dfs.azure.page.blob.dir="/hbase-wals" -cp <source-container-fullpath>/hbase-wals hdfs://<destination-cluster>/hbasewal
   ```

#### <a name="the-source-cluster-is-hdi-40-without-accelerated-writes-and-the-destination-cluster-doesnt-have-accelerated-writes"></a>원본 클러스터는 가속화된 쓰기가 없는 HDI 4.0이며 대상 클러스터에는 가속화된 쓰기가 없습니다.

1. 파일 시스템을 정리하고 데이터를 마이그레이션하려면 다음 명령을 실행합니다.
   
   ```bash
   hdfs dfs -rm -r /hbase 
   hadoop distcp <source-container-fullpath>/hbase /
   ```
   
1. `hdfs dfs -rm /hbase/hbase.id`를 실행하여 `hbase.id` 제거
   
1. WAL을 정리하고 마이그레이션하려면 다음 명령을 실행합니다.
   
   ```bash
   hdfs dfs -rm -r /hbase-wals/*
   hdfs dfs -Dfs.azure.page.blob.dir="/hbase-wals" -cp <source-container-fullpath>/hbase-wals /
   ```

### <a name="complete-the-migration"></a>마이그레이션 완료

1. 대상 클러스터에서 변경 내용을 저장하고 Ambari에서 지시하는 대로 필요한 모든 서비스를 다시 시작합니다.
   
1. 애플리케이션이 대상 클러스터를 가리키도록 합니다.
   
   > [!NOTE]  
   > 업그레이드할 때 애플리케이션에 대한 고정 DNS 이름이 변경됩니다. 이 DNS 이름을 하드 코딩하는 대신, 도메인 이름의 DNS 설정에서 클러스터 이름을 가리키는 CNAME을 구성할 수 있습니다. 또 다른 옵션은 다시 배포하지 않고 업데이트할 수 있는 애플리케이션에 대한 구성 파일을 사용하는 것입니다.
   
1. 수집을 시작합니다.
   
1. HBase 일관성과 간단한 DDL(데이터 정의 언어) 및 DML(데이터 조작 언어) 작업을 확인합니다.
   
1. 대상 클러스터가 만족스러운 경우 원본 클러스터를 삭제합니다.

## <a name="next-steps"></a>다음 단계

[Apache HBase](https://hbase.apache.org/) 및 HDInsight 클러스터 업그레이드에 대한 자세한 내용은 다음 문서를 참조하세요.

- [HDInsight 클러스터를 최신 버전으로 업그레이드](../hdinsight-upgrade-cluster.md)
- [Apache Ambari Web UI를 사용하여 Azure HDInsight 모니터링 및 관리](../hdinsight-hadoop-manage-ambari.md)
- [Azure HDInsight 버전](../hdinsight-component-versioning.md)
- [Apache HBase 최적화](../optimize-hbase-ambari.md)