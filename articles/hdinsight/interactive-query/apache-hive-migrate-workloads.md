---
title: Azure HDInsight 3.6 Hive 워크로드를 HDInsight 4.0으로 마이그레이션
description: HDInsight 3.6의 Apache Hive 워크로드를 HDInsight 4.0으로 마이그레이션하는 방법에 대해 알아봅니다.
author: kevxmsft
ms.author: kevx
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.date: 11/4/2020
ms.openlocfilehash: 8ae2d31bcb6cd17d020bf1602b5280f1db927f3f
ms.sourcegitcommit: bb9a6c6e9e07e6011bb6c386003573db5c1a4810
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110496201"
---
# <a name="migrate-azure-hdinsight-36-hive-workloads-to-hdinsight-40"></a>Azure HDInsight 3.6 Hive 워크로드를 HDInsight 4.0으로 마이그레이션

HDInsight 4.0은 HDInsight 3.6에 비해 몇 가지 이점이 있습니다. 다음은 [HDInsight 4.0의 새로운 기능에 대한 개요](../hdinsight-version-release.md)입니다.

이 문서에서는 다음을 포함하여 HDInsight 3.6의 Hive 워크로드를 4.0으로 마이그레이션하는 단계를 설명합니다.

* Hive 메타스토어 복사 및 스키마 업그레이드
* ACID 호환성을 위한 안전한 마이그레이션
* Hive 보안 정책 유지

새 HDInsight 클러스터와 이전 HDInsight 클러스터에는 동일한 스토리지 계정에 대한 액세스 권한이 있어야 합니다.

Hive 테이블을 새 스토리지 계정으로 마이그레이션하는 작업은 별도의 단계로 수행해야 합니다. [스토리지 계정 간 Hive 마이그레이션](./hive-migration-across-storage-accounts.md)을 참조하세요.

## <a name="steps-to-upgrade"></a>업그레이드 단계

### <a name="1-prepare-the-data"></a>1. 데이터 준비

* 기본적으로 HDInsight 3.6은 ACID 테이블을 지원하지 않습니다. 그러나 ACID 테이블이 있는 경우 'MAJOR' 압축을 실행합니다. 압축에 대한 자세한 내용은 [Hive 언어 설명서](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-AlterTable/Partition/Compact)를 참조하세요.

* [Azure Data Lake Storage Gen1](../overview-data-lake-storage-gen1.md)을 사용하는 경우 Hive 테이블 위치는 클러스터의 HDFS 구성에 따라 달라질 수 있습니다. 다음 스크립트 작업을 실행하여 이러한 위치를 다른 클러스터로 이식할 수 있도록 합니다. [실행 중인 클러스터에 대한 동작 스크립팅](../hdinsight-hadoop-customize-cluster-linux.md#script-action-to-a-running-cluster)을 참조하세요.

    |속성 | 값 |
    |---|---|
    |Bash 스크립트 URI|`https://hdiconfigactions.blob.core.windows.net/linuxhivemigrationv01/hive-adl-expand-location-v01.sh`|
    |노드 유형|Head|
    |매개 변수||

### <a name="2-copy-the-sql-database"></a>2. SQL 데이터베이스 복사

* 클러스터가 기본 Hive 메타스토어를 사용하는 경우 이 [가이드](./hive-default-metastore-export-import.md)에 따라 메타데이터를 외부 메타스토어로 내보냅니다. 그런 다음 업그레이드할 외부 메타스토어 복사본을 만듭니다.

* 클러스터가 외부 Hive 메타스토어를 사용하는 경우 해당 복사본을 만듭니다. 옵션에는 [내보내기/가져오기](../../azure-sql/database/database-export.md) 및 [특정 시점 복원](../../azure-sql/database/recovery-using-backups.md#point-in-time-restore)이 있습니다.

### <a name="3-upgrade-the-metastore-schema"></a>3. 메타스토어 스키마 업그레이드

이 단계에서는 HDInsight 4.0의 [`Hive Schema Tool`](https://cwiki.apache.org/confluence/display/Hive/Hive+Schema+Tool)을(를) 사용하여 메타스토어 스키마를 업그레이드합니다.

> [!Warning]
> 이 단계는 되돌릴 수 없습니다. 메타스토어의 복사본에서만 이 단계를 실행합니다.

1. 임시 HDInsight 4.0 클러스터를 만들어 4.0 Hive `schematool`에 액세스합니다. 이 단계에서는 [기본 Hive 메타스토어](../hdinsight-use-external-metadata-stores.md#default-metastore)를 사용할 수 있습니다.

1. HDInsight 4.0 클러스터에서 `schematool`을(를) 실행하여 대상 HDInsight 3.6 메타스토어를 업그레이드합니다.

    ```sh
    SERVER='servername.database.windows.net'  # replace with your SQL Server
    DATABASE='database'  # replace with your 3.6 metastore SQL Database
    USERNAME='username'  # replace with your 3.6 metastore username
    PASSWORD='password'  # replace with your 3.6 metastore password
    STACK_VERSION=$(hdp-select status hive-server2 | awk '{ print $3; }')
    /usr/hdp/$STACK_VERSION/hive/bin/schematool -upgradeSchema -url "jdbc:sqlserver://$SERVER;databaseName=$DATABASE;trustServerCertificate=false;encrypt=true;hostNameInCertificate=*.database.windows.net;" -userName "$USERNAME" -passWord "$PASSWORD" -dbType "mssql" --verbose
    ```

    > [!NOTE]
    > 이 유틸리티는 `beeline` 클라이언트를 사용하여 `/usr/hdp/$STACK_VERSION/hive/scripts/metastore/upgrade/mssql/upgrade-*.mssql.sql`에서 SQL 스크립트를 실행합니다.
    >
    > 이러한 스크립트의 SQL 구문이 다른 클라이언트 도구와 반드시 호환되는 것은 아닙니다. 예를 들어, [SSMS](/sql/ssms/download-sql-server-management-studio-ssms) 및 [Azure Portal 쿼리 편집기](../../azure-sql/database/connect-query-portal.md)에는 각 명령 뒤에 `GO` 키워드가 필요합니다.
    >
    > 리소스 용량 또는 트랜잭션 시간 제한으로 인해 스크립트가 실패하는 경우 SQL Database를 스케일 업합니다.

1. `select schema_version from dbo.version` 쿼리를 사용하여 최종 버전을 확인합니다.

    출력은 HDInsight 4.0 클러스터의 다음 bash 명령 중 하나와 일치해야 합니다.

    ```bash
    grep . /usr/hdp/$(hdp-select --version)/hive/scripts/metastore/upgrade/mssql/upgrade.order.mssql | tail -n1 | rev | cut -d'-' -f1 | rev
    ```

1. 임시 HDInsight 4.0 클러스터를 삭제합니다.

### <a name="4-deploy-a-new-hdinsight-40-cluster"></a>4. 새 HDInsight 4.0 클러스터 배포

[업그레이드된 Hive 메타스토어](../hdinsight-use-external-metadata-stores.md#select-a-custom-metastore-during-cluster-creation) 및 동일한 스토리지 계정을 선택하여 새 HDInsight 4.0 클러스터를 만듭니다.

* 새 클러스터에는 동일한 기본 파일 시스템이 필요하지 않습니다.

* 메타스토어가 여러 스토리지 계정에 있는 테이블을 포함하는 경우, 이러한 테이블에 액세스하려면 해당 스토리지 계정을 새 클러스터에 추가해야 합니다. [HDInsight에 추가 스토리지 계정 추가](../hdinsight-hadoop-add-storage.md)를 참조하세요.

* 스토리지에 액세스하지 못해 Hive 작업이 실패하는 경우 테이블 위치가 클러스터에 추가된 스토리지 계정에 있는지 확인합니다.

    다음 Hive 명령을 사용하여 테이블 위치를 식별합니다.

    ```sql
    SHOW CREATE TABLE ([db_name.]table_name|view_name);
    ```

### <a name="5-convert-tables-for-acid-compliance"></a>5. ACID를 준수하기 위한 테이블 변환

관리되는 테이블은 HDInsight 4.0에서 ACID를 준수해야 합니다. HDInsight 4.0에서 `strictmanagedmigration`을(를) 실행하여 ACID로 관리되지 않는 모든 테이블을 외부 테이블(`'external.table.purge'='true'` 속성 사용)로 변환합니다. 헤드 노드에서 다음을 실행합니다.

```bash
sudo su - hive
STACK_VERSION=$(hdp-select status hive-server2 | awk '{ print $3; }')
/usr/hdp/$STACK_VERSION/hive/bin/hive --config /etc/hive/conf --service strictmanagedmigration --hiveconf hive.strict.managed.tables=true -m automatic --modifyManagedTables
```

## <a name="secure-hive-across-hdinsight-versions"></a>HDInsight 버전 간 Hive 보안

HDInsight는 필요에 따라 ESP(Enterprise Security Package)를 사용하여 Azure Active Directory와 통합됩니다. ESP는 Kerberos 및 Apache Ranger를 사용하여 클러스터 내 특정 리소스에 대한 사용 권한을 관리합니다. HDInsight 3.6의 Hive에 대해 배포된 Ranger 정책은 다음 단계를 통해 HDInsight 4.0으로 마이그레이션할 수 있습니다.

1. HDInsight 3.6 클러스터의 Ranger Service Manager 패널로 이동합니다.
2. **HIVE** 라는 정책으로 이동하여 json 파일로 정책을 내보냅니다.
3. 내보낸 정책 json에서 참조되는 모든 사용자가 새 클러스터에 있는지 확인합니다. 사용자가 정책 json에서 참조되지만 새 클러스터에 없는 경우 새 클러스터에 사용자를 추가하거나 정책에서 참조를 제거합니다.
4. HDInsight 4.0 클러스터의 **Ranger Service Manager** 패널로 이동합니다.
5. **HIVE** 라는 정책으로 이동하여 2단계에서 Ranger 정책 json을 가져옵니다.

## <a name="hive-changes-in-hdinsight-40-that-may-require-application-changes"></a>애플리케이션을 변경해야 할 수도 있는 HDInsight 4.0의 Hive 변경 내용

* ACID 테이블용 Spark와 Hive 간에 메타스토어를 공유하려면 [Hive Warehouse Connector를 사용하여 추가 구성](./apache-hive-warehouse-connector.md)을 참조하세요.

* HDInsight 4.0은 [스토리지 기반 권한 부여](https://cwiki.apache.org/confluence/display/Hive/Storage+Based+Authorization+in+the+Metastore+Server)를 사용합니다. 파일 사용 권한을 수정하거나 Hive와 다른 사용자로 폴더를 만드는 경우 스토리지 사용 권한에 따라 Hive 오류가 발생할 수 있습니다. 수정하려면 사용자에게 `rw-` 액세스 권한을 부여합니다. [HDFS 사용 권한 가이드](https://hadoop.apache.org/docs/r2.7.1/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html)를 참조하세요.

* `HiveCLI`이(가) `Beeline`(으)로 바뀝니다.

추가 변경 내용은 [HDInsight 4.0 공지](../hdinsight-version-release.md)를 참조하세요.

## <a name="troubleshooting-guide"></a>문제 해결 가이드

[Hive 워크로드에 대한 HDInsight 3.6~4.0 문제 해결 가이드](./interactive-query-troubleshoot-migrate-36-to-40.md)는 Hive 워크로드를 HDInsight 3.6에서 HDInsight 4.0으로 마이그레이션할 때 발생하는 일반적인 문제에 대한 답변을 제공합니다.

## <a name="further-reading"></a>추가 참고 자료

* [HDInsight 4.0 공지](../hdinsight-version-release.md)
* [HDInsight 4.0 심층 분석](https://azure.microsoft.com/blog/deep-dive-into-azure-hdinsight-4-0/)
* [Hive 3 ACID 테이블](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.1.0/using-hiveql/content/hive_3_internals.html)