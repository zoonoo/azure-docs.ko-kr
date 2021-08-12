---
title: HBase 클러스터를 최신 버전으로 마이그레이션 - Azure HDInsight
description: Azure HDInsight의 Apache HBase 클러스터를 최신 버전으로 마이그레이션하는 방법을 알아봅니다.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 05/06/2021
ms.openlocfilehash: 1d6f2d66a00601334a9cab4695b4e78c77a67917
ms.sourcegitcommit: 3de22db010c5efa9e11cffd44a3715723c36696a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2021
ms.locfileid: "109654803"
---
# <a name="migrate-an-apache-hbase-cluster-to-a-new-version"></a>Apache HBase 클러스터를 최신 버전으로 마이그레이션

이 문서에서는 Azure HDInsight의 Apache HBase 클러스터를 최신 버전으로 업데이트하는 방법을 설명합니다.

이 문서는 원본 및 대상 클러스터에 동일한 Azure Storage 계정을 사용하는 경우에만 적용됩니다. 대상 클러스터에 대해 새 스토리지 계정이나 다른 스토리지 계정으로 업그레이드하려면 [새 스토리지 계정을 사용하여 Apache HBase를 새 버전으로 마이그레이션](apache-hbase-migrate-new-version-new-storage-account.md)을 참조하세요.

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

HDInsight 버전 및 호환성에 대한 자세한 내용은 [Azure HDInsight 버전](../hdinsight-component-versioning.md)을 참조하세요.

## <a name="apache-hbase-cluster-migration-overview"></a>Apache HBase 클러스터 마이그레이션 개요

Azure HDInsight에서 Apache HBase 클러스터를 업그레이드하려면 다음 기본 단계를 완료합니다. 자세한 지침은 자세한 단계 및 명령을 참조하세요.

원본 클러스터 준비:
1. 데이터 수집을 중지합니다.
1. memstore 데이터를 플러시합니다.
1. Ambari에서 HBase를 중지합니다.
1. 가속화된 쓰기를 포함하는 클러스터의 경우 WAL(미리 쓰기 로그) 디렉터리를 백업합니다.

대상 서버 준비:
1. 대상 클러스터를 만듭니다.
1. Ambari에서 HBase를 중지합니다.
1. 원래 원본 클러스터 컨테이너를 참조하도록 HDFS 서비스 구성에서 `fs.defaultFS`를 업데이트합니다.
1. 가속 쓰기를 사용하는 클러스터의 경우, 원래 원본 클러스터 컨테이너를 참조하도록 HBase 서비스 구성에서 `hbase.rootdir`을 업데이트합니다.
1. Zookeeper 데이터를 정리합니다.

마이그레이션 완료:
1. WAL을 정리하고 마이그레이션합니다.
1. 대상 클러스터의 기본 컨테이너에서 원래 원본 컨테이너로 앱을 복사합니다.
1. Ambari 대상 클러스터에서 모든 서비스를 시작합니다.
1. HBase를 확인합니다.
1. 원본 클러스터를 삭제합니다.

## <a name="detailed-migration-steps-and-commands"></a>자세한 마이그레이션 단계 및 명령

이러한 자세한 단계 및 명령을 사용하여 Apache HBase 클러스터를 마이그레이션합니다.

### <a name="prepare-the-source-cluster"></a>원본 클러스터 준비

1. 원본 HBase 클러스터에 대한 수집을 중지합니다.
   
1. 업그레이드하는 원본 HBase 클러스터를 플러시합니다.
   
   HBase는 들어오는 데이터를 *memstore* 라는 메모리 내 저장소에 씁니다. memstore가 특정 크기에 도달한 후 HBase는 장기 저장을 위해 클러스터의 스토리지 계정에 있는 디스크로 플러시됩니다. 업그레이드 후 원본 클러스터를 삭제하면 memstore의 데이터도 모두 삭제됩니다. 데이터를 유지하려면 업그레이드하기 전에 각 테이블의 memstore를 디스크에 수동으로 플러시합니다.
   
   [Azure hbase-utils GitHub 리포지토리](https://github.com/Azure/hbase-utils/)에서 [flush_all_tables.sh](https://github.com/Azure/hbase-utils/blob/master/scripts/flush_all_tables.sh) 스크립트를 실행하여 memstore 데이터를 플러시할 수 있습니다.
   
   HDInsight 클러스터에서 다음 HBase 셸 명령을 실행하여 memstore 데이터를 플러시할 수도 있습니다.
   
   ```bash
   hbase shell
   flush "<table-name>"
   ```
   
1. `https://<OLDCLUSTERNAME>.azurehdinsight.net`을 사용하여 원본 클러스터에서 [Apache Ambari](https://ambari.apache.org/)에 로그인하고 HBase 서비스를 중지합니다.
   
1. 확인 메시지에서 HBase에 대한 유지 관리 모드를 설정하는 상자를 선택합니다.
   
   Ambari 연결 및 사용에 대한 자세한 내용은 [Ambari 웹 UI를 사용하여 HDInsight 클러스터 관리](../hdinsight-hadoop-manage-ambari.md)를 참조하세요.
   
1. 원본 HBase 클러스터에 [가속화된 쓰기](apache-hbase-accelerated-writes.md) 기능이 없는 경우 이 단계를 건너뜁니다. 가속 쓰기를 사용하는 원본 HBase 클러스터의 경우 원본 클러스터의 작업자 노드 또는 Zookeeper 노드의 SSH 세션에서 다음 명령을 실행하여 HDFS 아래의 WAL 디렉토리를 백업합니다.
   
   ```bash
   hdfs dfs -mkdir /hbase-wal-backup
   hdfs dfs -cp hdfs://mycluster/hbasewal /hbase-wal-backup
   ```
   
### <a name="prepare-the-destination-cluster"></a>대상 서버 준비

1. Azure Portal에서 원본 클러스터와 동일한 스토리지 계정을 사용하지만 컨테이너 이름은 다른 [새 대상 HDInsight 클러스터를 설정](../hdinsight-hadoop-provision-linux-clusters.md)합니다.

   
1. `https://<NEWCLUSTERNAME>.azurehdinsight.net`의 새 클러스터에서 [Apache Ambari](https://ambari.apache.org/)에 로그인하고 HBase 서비스를 중지합니다.
   
1. **서비스** > **HDFS** > **구성** > **고급** > **고급 코어 사이트** 에서 원래 원본 클러스터 컨테이너 이름을 가리키도록 `fs.defaultFS` HDFS 설정을 변경합니다. 예를 들어 다음 스크린샷의 설정을 `wasbs://hbase-upgrade-old-2021-03-22`로 변경해야 합니다.
   
   :::image type="content" source="./media/apache-hbase-migrate-new-version/hdfs-advanced-settings.png" alt-text="Ambari에서 서비스 > HDFS > 구성> 고급 > 고급 코어를 선택하고 컨테이너 이름을 변경합니다." border="false":::
   
1. 대상 클러스터에 가속 쓰기 기능이 있는 경우 `hbase.rootdir` 경로를 원래 원본 클러스터 컨테이너 이름을 가리키도록 변경합니다. 예를 들어 다음 경로를 `hbase-upgrade-old-2021-03-22`로 변경해야 합니다. 클러스터에 가속 쓰기가 없는 경우 이 단계를 건너뜁니다.
   
   :::image type="content" source="./media/apache-hbase-migrate-new-version/change-container-name-for-hbase-rootdir.png" alt-text="Ambari에서 HBase rootdir의 컨테이너 이름을 변경합니다." border="true":::
   
1. Zookeeper 노드 또는 작업자 노드에서 다음 명령을 실행하여 대상 클러스터의 Zookeeper 데이터를 정리합니다.
   
   ```bash
   hbase zkcli
   rmr /hbase-unsecure
   quit
   ```
   
### <a name="clean-and-migrate-wal"></a>WAL 정리 및 마이그레이션

원본 HDI 버전과 원본 및 대상 클러스터에 가속화된 쓰기가 있는지 여부에 따라 다음 명령을 실행합니다.

- HDI 3.6은 기본 지원에 속하며 새 클러스터에는 권장되지 않기 때문에 대상 클러스터는 항상 HDI 버전 4.0입니다.
- HDFS 복사 명령은 `hdfs dfs <copy properties starting with -D> -cp <source> <destination> # Serial execution`입니다.

> [!NOTE]  
> - 스토리지 유형 WASB에 대한 `<source-container-fullpath>`는 `wasbs://<source-container-name>@<storageaccountname>.blob.core.windows.net`입니다.
> - 스토리지 유형 Azure Data Lake Storage Gen2에 대한 `<source-container-fullpath>`는 `abfs://<source-container-name>@<storageaccountname>.dfs.core.windows.net`입니다.

- [원본 클러스터는 가속화된 쓰기가 있는 HDI 3.6이며 대상 클러스터에는 가속화된 쓰기가 있습니다](#the-source-cluster-is-hdi-36-or-hdi-40-with-accelerated-writes-and-the-destination-cluster-has-accelerated-writes).
- [원본 클러스터는 가속화된 쓰기가 없는 HDI 3.6이며 대상 클러스터에는 가속화된 쓰기가 있습니다](#the-source-cluster-is-hdi-36-without-accelerated-writes-and-the-destination-cluster-has-accelerated-writes).
- [원본 클러스터는 가속화된 쓰기가 없는 HDI 3.6이며 대상 클러스터에는 가속화된 쓰기가 없습니다](#the-source-cluster-is-hdi-36-without-accelerated-writes-and-the-destination-cluster-doesnt-have-accelerated-writes).
- [원본 클러스터는 가속화된 쓰기가 있는 HDI 4.0이며 대상 클러스터에는 가속화된 쓰기가 있습니다](#the-source-cluster-is-hdi-36-or-hdi-40-with-accelerated-writes-and-the-destination-cluster-has-accelerated-writes).
- [원본 클러스터는 가속화된 쓰기가 없는 HDI 4.0이며 대상 클러스터에는 가속화된 쓰기가 있습니다](#the-source-cluster-is-hdi-40-without-accelerated-writes-and-the-destination-cluster-has-accelerated-writes).
- [원본 클러스터는 가속화된 쓰기가 없는 HDI 4.0이며 대상 클러스터에는 가속화된 쓰기가 없습니다](#the-source-cluster-is-hdi-40-without-accelerated-writes-and-the-destination-cluster-doesnt-have-accelerated-writes).

#### <a name="the-source-cluster-is-hdi-36-or-hdi-40-with-accelerated-writes-and-the-destination-cluster-has-accelerated-writes"></a>원본 클러스터는 가속화된 쓰기가 있는 HDI 3.6 또는 HDI 4.0이며 대상 클러스터에는 가속화된 쓰기가 있습니다.

대상 클러스터의 WAL FS 데이터를 정리하고 원본 클러스터의 WAL 디렉터리를 대상 클러스터의 HDFS로 복사합니다. 대상 클러스터의 작업자 노드 또는 Zookeeper 노드에서 다음 명령을 실행하여 디렉터리를 복사합니다.

```bash   
sudo -u hbase hdfs dfs -rm -r hdfs://mycluster/hbasewal
sudo -u hbase hdfs dfs -cp <source-container-fullpath>/hbase-wal-backup/hbasewal hdfs://mycluster/
```
#### <a name="the-source-cluster-is-hdi-36-without-accelerated-writes-and-the-destination-cluster-has-accelerated-writes"></a>원본 클러스터는 가속화된 쓰기가 없는 HDI 3.6이며 대상 클러스터에는 가속화된 쓰기가 있습니다.

대상 클러스터의 WAL FS 데이터를 정리하고 원본 클러스터의 WAL 디렉터리를 대상 클러스터의 HDFS로 복사합니다. 대상 클러스터의 작업자 노드 또는 Zookeeper 노드에서 다음 명령을 실행하여 디렉터리를 복사합니다.

```bash
sudo -u hbase hdfs dfs -rm -r hdfs://mycluster/hbasewal
sudo -u hbase hdfs dfs -Dfs.azure.page.blob.dir="/hbase/WALs,/hbase/MasterProcWALs,/hbase/oldWALs" -cp <source-container>/hbase/*WALs hdfs://mycluster/hbasewal
```

#### <a name="the-source-cluster-is-hdi-36-without-accelerated-writes-and-the-destination-cluster-doesnt-have-accelerated-writes"></a>원본 클러스터는 가속화된 쓰기가 없는 HDI 3.6이며 대상 클러스터에는 가속화된 쓰기가 없습니다.

대상 클러스터의 WAL FS 데이터를 정리하고 원본 클러스터 WAL 디렉터리를 대상 클러스터의 HDFS로 복사합니다. 디렉터리를 복사하려면 대상 클러스터의 작업자 노드 또는 Zookeeper 노드에서 다음 명령을 실행합니다.

```bash
sudo -u hbase hdfs dfs -rm -r /hbase-wals/*
sudo -u hbase hdfs dfs -Dfs.azure.page.blob.dir="/hbase/WALs,/hbase/MasterProcWALs,/hbase/oldWALs" -cp <source-container-fullpath>/hbase/*WALs /hbase-wals
```

#### <a name="the-source-cluster-is-hdi-40-without-accelerated-writes-and-the-destination-cluster-has-accelerated-writes"></a>원본 클러스터는 가속화된 쓰기가 없는 HDI 4.0이며 대상 클러스터에는 가속화된 쓰기가 있습니다.

대상 클러스터의 WAL FS 데이터를 정리하고 원본 클러스터의 WAL 디렉터리를 대상 클러스터의 HDFS로 복사합니다. 대상 클러스터의 작업자 노드 또는 Zookeeper 노드에서 다음 명령을 실행하여 디렉터리를 복사합니다.

```bash
sudo -u hbase hdfs dfs -rm -r hdfs://mycluster/hbasewal
sudo -u hbase hdfs dfs -cp <source-container-fullpath>/hbase-wals/* hdfs://mycluster/hbasewal
   ```

#### <a name="the-source-cluster-is-hdi-40-without-accelerated-writes-and-the-destination-cluster-doesnt-have-accelerated-writes"></a>원본 클러스터는 가속화된 쓰기가 없는 HDI 4.0이며 대상 클러스터에는 가속화된 쓰기가 없습니다.

대상 클러스터의 WAL FS 데이터를 정리하고 원본 클러스터 WAL 디렉터리를 대상 클러스터의 HDFS로 복사합니다. 디렉터리를 복사하려면 대상 클러스터의 작업자 노드 또는 Zookeeper 노드에서 다음 명령을 실행합니다.

```bash
sudo -u hbase hdfs dfs -rm -r /hbase-wals/*
sudo -u hbase hdfs dfs -Dfs.azure.page.blob.dir="/hbase-wals" -cp <source-container-fullpath>/hbase-wals /
```

### <a name="complete-the-migration"></a>마이그레이션 완료

1. `sudo -u hdfs` 사용자 컨텍스트를 사용하여 `/hdp/apps/<new-version-name>` 폴더와 해당 콘텐츠를 `<destination-container-fullpath>`에서 `<source-container-fullpath>` 아래 `/hdp/apps` 폴더에 복사합니다. 대상 클러스터에서 다음 명령을 실행하여 폴더를 복사할 수 있습니다.
   
   ```bash   
   sudo -u hdfs hdfs dfs -cp /hdp/apps/<hdi-version> <source-container-fullpath>/hdp/apps
   ```
   
   예를 들면 다음과 같습니다.
   ```bash
   sudo -u hdfs hdfs dfs -cp /hdp/apps/4.1.3.6 wasbs://hbase-upgrade-old-2021-03-22@hbaseupgrade.blob.core.windows.net/hdp/apps
   ```
   
1. 대상 클러스터에서 변경 내용을 저장하고 Ambari에 나타나는 대로 필요한 모든 서비스를 다시 시작합니다.
   
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
