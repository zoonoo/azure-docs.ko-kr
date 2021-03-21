---
title: Azure HDInsight 3.6 Hive 워크로드를 HDInsight 4.0으로 마이그레이션
description: HDInsight 3.6에서 Apache Hive 작업을 HDInsight 4.0로 마이그레이션하는 방법에 대해 알아봅니다.
author: kevxmsft
ms.author: kevx
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.date: 11/4/2020
ms.openlocfilehash: b13e8e088eff95071247a53ad1a4a18879f94053
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101742197"
---
# <a name="migrate-azure-hdinsight-36-hive-workloads-to-hdinsight-40"></a>Azure HDInsight 3.6 Hive 워크로드를 HDInsight 4.0으로 마이그레이션

Hdinsight 4.0에는 HDInsight 3.6에 비해 몇 가지 이점이 있습니다. 다음은 [HDInsight 4.0의 새로운 기능에 대 한 개요](../hdinsight-version-release.md)입니다.

이 문서에서는 다음을 포함 하 여 HDInsight 3.6에서 4.0로 Hive 워크 로드를 마이그레이션하는 단계를 설명 합니다.

* Hive metastore 복사 및 스키마 업그레이드
* ACID 호환성을 위한 안전한 마이그레이션
* Hive 보안 정책 유지

새 HDInsight 클러스터와 이전 HDInsight 클러스터에는 동일한 저장소 계정에 대 한 액세스 권한이 있어야 합니다.

Hive 테이블을 새 저장소 계정으로 마이그레이션하는 작업은 별도의 단계로 수행 해야 합니다. [저장소 계정 간 Hive 마이그레이션](./hive-migration-across-storage-accounts.md)을 참조 하세요.

## <a name="steps-to-upgrade"></a>업그레이드 단계

### <a name="1-prepare-the-data"></a>1. 데이터 준비

* 기본적으로 HDInsight 3.6은 ACID 테이블을 지원 하지 않습니다. 그러나 ACID 테이블이 있는 경우에는 ' MAJOR ' 압축을 실행 합니다. 압축에 대 한 자세한 내용은 [Hive 언어 설명서](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-AlterTable/Partition/Compact) 를 참조 하세요.

* [Azure Data Lake Storage Gen1](../overview-data-lake-storage-gen1.md)사용 하는 경우 Hive 테이블 위치는 클러스터의 HDFS 구성에 따라 달라질 수 있습니다. 다음 스크립트 작업을 실행 하 여 이러한 위치를 다른 클러스터로 이식할 수 있게 합니다. [실행 중인 클러스터에 대 한 동작 스크립팅을](../hdinsight-hadoop-customize-cluster-linux.md#script-action-to-a-running-cluster)참조 하세요.

    |속성 | 값 |
    |---|---|
    |Bash 스크립트 URI|`https://hdiconfigactions.blob.core.windows.net/linuxhivemigrationv01/hive-adl-expand-location-v01.sh`|
    |노드 유형|Head|
    |매개 변수||

### <a name="2-copy-the-sql-database"></a>2. SQL 데이터베이스 복사

* 클러스터가 기본 Hive metastore를 사용 하는 경우이 [가이드](./hive-default-metastore-export-import.md) 에 따라 외부 metastore 메타 데이터를 내보냅니다. 그런 다음 업그레이드할 외부 metastore 복사본을 만듭니다.

* 클러스터가 외부 Hive metastore를 사용 하는 경우 복사본을 만듭니다. 옵션에는 [내보내기/가져오기](../../azure-sql/database/database-export.md) 및 지정 [시간 복원이](../../azure-sql/database/recovery-using-backups.md#point-in-time-restore)있습니다.

### <a name="3-upgrade-the-metastore-schema"></a>3. metastore 스키마를 업그레이드 합니다.

이 단계에서는 [`Hive Schema Tool`](https://cwiki.apache.org/confluence/display/Hive/Hive+Schema+Tool) HDInsight 4.0의를 사용 하 여 metastore 스키마를 업그레이드 합니다.

> [!Warning]
> 이 단계는 되돌릴 수 없습니다. Metastore의 복사본 에서만이를 실행 합니다.

1. 4.0 Hive에 액세스할 임시 HDInsight 4.0 클러스터를 만듭니다 `schematool` . 이 단계에는 [기본 Hive metastore](../hdinsight-use-external-metadata-stores.md#default-metastore) 를 사용할 수 있습니다.

1. HDInsight 4.0 클러스터에서를 실행 `schematool` 하 여 대상 hdinsight 3.6 metastore을 업그레이드 합니다.

    ```sh
    SERVER='servername.database.windows.net'  # replace with your SQL Server
    DATABASE='database'  # replace with your 3.6 metastore SQL Database
    USERNAME='username'  # replace with your 3.6 metastore username
    PASSWORD='password'  # replace with your 3.6 metastore password
    STACK_VERSION=$(hdp-select status hive-server2 | awk '{ print $3; }')
    /usr/hdp/$STACK_VERSION/hive/bin/schematool -upgradeSchema -url "jdbc:sqlserver://$SERVER;databaseName=$DATABASE;trustServerCertificate=false;encrypt=true;hostNameInCertificate=*.database.windows.net;" -userName "$USERNAME" -passWord "$PASSWORD" -dbType "mssql" --verbose
    ```

    > [!NOTE]
    > 이 유틸리티는 클라이언트를 사용 하 여 `beeline` 에서 SQL 스크립트를 실행 `/usr/hdp/$STACK_VERSION/hive/scripts/metastore/upgrade/mssql/upgrade-*.mssql.sql` 합니다.
    >
    > 이러한 스크립트의 SQL 구문은 다른 클라이언트 도구와 반드시 호환 되는 것은 아닙니다. 예를 들어 [](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) [Azure Portal의 SSMS 및 쿼리 편집기](../../azure-sql/database/connect-query-portal.md) 에는 `GO` 각 명령 다음에 키워드가 필요 합니다.
    >
    > 리소스 용량 또는 트랜잭션 시간 제한으로 인해 스크립트가 실패 하는 경우 SQL Database 확장 합니다.

1. 쿼리를 사용 하 여 최종 버전을 확인 `select schema_version from dbo.version` 합니다.

    출력은 HDInsight 4.0 클러스터의 다음 bash 명령 중 하 나와 일치 해야 합니다.

    ```bash
    grep . /usr/hdp/$(hdp-select --version)/hive/scripts/metastore/upgrade/mssql/upgrade.order.mssql | tail -n1 | rev | cut -d'-' -f1 | rev
    ```

1. 임시 HDInsight 4.0 클러스터를 삭제 합니다.

### <a name="4-deploy-a-new-hdinsight-40-cluster"></a>4. 새 HDInsight 4.0 클러스터 배포

[업그레이드 된 Hive metastore](../hdinsight-use-external-metadata-stores.md#select-a-custom-metastore-during-cluster-creation) 및 동일한 저장소 계정을 선택 하 여 새 HDInsight 4.0 클러스터를 만듭니다.

* 새 클러스터에는 동일한 기본 파일 시스템이 필요 하지 않습니다.

* Metastore가 여러 저장소 계정에 있는 테이블을 포함 하는 경우 해당 테이블에 액세스 하려면 해당 저장소 계정을 새 클러스터에 추가 해야 합니다. [HDInsight에 추가 저장소 계정 추가를](../hdinsight-hadoop-add-storage.md)참조 하세요.

* 저장소 액세스할 수 없게 인해 Hive 작업이 실패 하는 경우 테이블 위치가 클러스터에 추가 된 저장소 계정에 있는지 확인 합니다.

    다음 Hive 명령을 사용 하 여 테이블 위치를 식별 합니다.

    ```sql
    SHOW CREATE TABLE ([db_name.]table_name|view_name);
    ```

### <a name="5-convert-tables-for-acid-compliance"></a>5. ACID 준수를 위해 테이블 변환

관리 되는 테이블은 HDInsight 4.0에서 ACID 규격 이어야 합니다. `strictmanagedmigration`HDInsight 4.0에서를 실행 하 여 ACID로 관리 되지 않는 모든 테이블을 속성을 사용 하는 외부 테이블로 변환 `'external.table.purge'='true'` 합니다. 헤드 노드에서 실행 합니다.

```bash
sudo su - hive
STACK_VERSION=$(hdp-select status hive-server2 | awk '{ print $3; }')
/usr/hdp/$STACK_VERSION/hive/bin/hive --config /etc/hive/conf --service strictmanagedmigration --hiveconf hive.strict.managed.tables=true -m automatic --modifyManagedTables
```

## <a name="secure-hive-across-hdinsight-versions"></a>HDInsight 버전 간 Hive 보안

필요에 따라 hdinsight는 Enterprise Security Package (ESP)를 사용 하 여 Azure Active Directory와 통합 됩니다. ESP는 Kerberos 및 Apache 레인저를 사용 하 여 클러스터 내의 특정 리소스에 대 한 권한을 관리 합니다. 다음 단계를 수행 하 여 HDInsight 3.6의 Hive에 대해 배포 된 레인저 정책을 HDInsight 4.0로 마이그레이션할 수 있습니다.

1. HDInsight 3.6 클러스터의 레인저 Service Manager 패널로 이동 합니다.
2. **HIVE** 라는 정책으로 이동 하 여 정책을 json 파일로 내보냅니다.
3. 내보낸 정책 json에서 참조 하는 모든 사용자가 새 클러스터에 존재 하는지 확인 합니다. 사용자가 정책 json에서 참조 되지만 새 클러스터에 존재 하지 않는 경우 새 클러스터에 사용자를 추가 하거나 정책에서 참조를 제거 합니다.
4. HDInsight 4.0 클러스터의 **레인저 Service Manager** 패널로 이동 합니다.
5. **HIVE** 라는 정책으로 이동 하 고 2 단계에서 레인저 정책 json을 가져옵니다.

## <a name="hive-changes-in-hdinsight-40-that-may-require-application-changes"></a>응용 프로그램 변경이 필요할 수 있는 HDInsight 4.0의 Hive 변경 내용

* ACID 테이블에 대해 Spark와 Hive 간에 metastore을 공유 하려면 [Hive 웨어하우스 커넥터를 사용 하 여 추가 구성](./apache-hive-warehouse-connector.md) 을 참조 하세요.

* HDInsight 4.0는 [저장소 기반 권한 부여](https://cwiki.apache.org/confluence/display/Hive/Storage+Based+Authorization+in+the+Metastore+Server)를 사용 합니다. 파일 사용 권한을 수정 하거나 Hive와 다른 사용자로 폴더를 만드는 경우 저장소 사용 권한에 따라 Hive 오류가 발생할 수 있습니다. 수정 하려면 `rw-` 사용자에 게 액세스 권한을 부여 합니다. [HDFS 사용 권한 가이드](https://hadoop.apache.org/docs/r2.7.1/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html)를 참조 하세요.

* `HiveCLI` 는로 대체 됩니다 `Beeline` .

추가 변경 내용은 [HDInsight 4.0 알림](../hdinsight-version-release.md) 을 참조 하세요.

## <a name="further-reading"></a>추가 정보

* [HDInsight 4.0 공지](../hdinsight-version-release.md)
* [HDInsight 4.0 심층 이해](https://azure.microsoft.com/blog/deep-dive-into-azure-hdinsight-4-0/)
* [Hive 3 ACID 테이블](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.1.0/using-hiveql/content/hive_3_internals.html)
