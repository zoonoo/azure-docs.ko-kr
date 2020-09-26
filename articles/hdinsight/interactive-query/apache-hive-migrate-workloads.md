---
title: Azure HDInsight 3.6 Hive 워크로드를 HDInsight 4.0으로 마이그레이션
description: HDInsight 3.6에서 Apache Hive 작업을 HDInsight 4.0로 마이그레이션하는 방법에 대해 알아봅니다.
author: msft-tacox
ms.author: tacox
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.date: 11/13/2019
ms.openlocfilehash: 26dfe8d134f9f38d8272895583ba2eff614d78e4
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91308387"
---
# <a name="migrate-azure-hdinsight-36-hive-workloads-to-hdinsight-40"></a>Azure HDInsight 3.6 Hive 워크로드를 HDInsight 4.0으로 마이그레이션

이 문서에서는 HDInsight 3.6에서 Apache Hive 및 LLAP 워크 로드를 HDInsight 4.0로 마이그레이션하는 방법을 보여 줍니다. HDInsight 4.0은 구체화 된 뷰 및 쿼리 결과 캐싱과 같은 새로운 Hive 및 LLAP 기능을 제공 합니다. 작업을 HDInsight 4.0로 마이그레이션하는 경우 HDInsight 3.6에서 사용할 수 없는 Hive 3의 최신 기능을 많이 사용할 수 있습니다.

이 문서에서는 다음 주제를 다룹니다.

* HDInsight 4.0로 Hive 메타 데이터 마이그레이션
* ACID 및 비 ACID 테이블의 안전 마이그레이션
* HDInsight 버전 간 Hive 보안 정책 유지
* HDInsight 3.6에서 HDInsight 4.0로 쿼리 실행 및 디버깅

Hive의 이점 중 하나는 외부 데이터베이스 (Hive Metastore 이라고 함)로 메타 데이터를 내보내는 기능입니다. **Hive Metastore** 는 테이블 저장소 위치, 열 이름 및 테이블 인덱스 정보를 포함 하 여 테이블 통계를 저장 하는 일을 담당 합니다. HDInsight 3.6 및 HDInsight 4.0에는 다른 metastore 스키마가 필요 하며 단일 metastore를 공유할 수 없습니다. Hive metastore를 안전 하 게 업그레이드 하는 권장 방법은 현재 프로덕션 환경에서 원본 대신 복사본을 업그레이드 하는 것입니다. 이 문서에는 동일한 저장소 계정에 대 한 액세스 권한이 있는 원본 및 새 클러스터가 필요 합니다. 따라서 다른 지역으로 데이터를 마이그레이션하는 것은 다루지 않습니다.

## <a name="migrate-from-external-metastore"></a>외부 metastore에서 마이그레이션

### <a name="1-run-major-compaction-on-acid-tables-in-hdinsight-36"></a>1. HDInsight 3.6의 ACID 테이블에서 주요 압축 실행

HDInsight 3.6 및 HDInsight 4.0 ACID 테이블은 ACID 델타를 다르게 인식 합니다. 마이그레이션을 수행 하기 전에 유일 하 게 필요한 작업은 3.6 클러스터의 각 ACID 테이블에 대해 ' MAJOR ' 압축을 실행 하는 것입니다. 압축에 대 한 자세한 내용은 [Hive 언어 설명서](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-AlterTable/Partition/Compact) 를 참조 하세요.

### <a name="2-copy-sql-database"></a>2. SQL 데이터베이스 복사
외부 metastore의 새 복사본을 만듭니다. 외부 metastore를 사용 하는 경우 metastore의 복사본을 만드는 안전 하 고 쉬운 방법 중 하나는 함수를 사용 하 여 다른 이름으로 [데이터베이스를 복원](../../azure-sql/database/recovery-using-backups.md#point-in-time-restore) 하는 것입니다 `RESTORE` .  외부 metastore를 HDInsight 클러스터에 연결 하는 방법에 대 한 자세한 내용은 [Azure hdinsight에서 외부 메타 데이터 저장소 사용](../hdinsight-use-external-metadata-stores.md) 을 참조 하세요.

### <a name="3-upgrade-metastore-schema"></a>3. metastore 스키마 업그레이드
Metastore **복사가** 완료 되 면 기존 HDInsight 3.6 클러스터의 [스크립트 작업](../hdinsight-hadoop-customize-cluster-linux.md) 에서 스키마 업그레이드 스크립트를 실행 하 여 새 metastore을 Hive 3 스키마로 업그레이드 합니다. 이 단계에서는 새 metastore를 클러스터에 연결 하지 않아도 됩니다. 이를 통해 데이터베이스를 HDInsight 4.0 metastore에 연결할 수 있습니다.

아래 표에 있는 값을 사용 합니다. `SQLSERVERNAME DATABASENAME USERNAME PASSWORD`공백으로 구분 된 Hive metastore **복사본**에 대 한 적절 한 값으로 대체 합니다. SQL server 이름을 지정할 때 ". database.windows.net"를 포함 하지 마십시오.

|속성 | 값 |
|---|---|
|스크립트 유형|- 사용자 지정|
|속성|Hive 업그레이드|
|Bash 스크립트 URI|`https://hdiconfigactions.blob.core.windows.net/hivemetastoreschemaupgrade/launch-schema-upgrade.sh`|
|노드 유형|Head|
|매개 변수|SQLSERVERNAME DATABASENAME 사용자 이름 암호|

> [!Warning]  
> HDInsight 3.6 메타 데이터 스키마를 HDInsight 4.0 스키마로 변환 하는 업그레이드는 되돌릴 수 없습니다.

데이터베이스에 대해 다음 sql 쿼리를 실행 하 여 업그레이드를 확인할 수 있습니다.

```sql
select * from dbo.version
```

### <a name="4-deploy-a-new-hdinsight-40-cluster"></a>4. 새 HDInsight 4.0 클러스터 배포

1. 업그레이드 된 metastore를 새 클러스터의 Hive metastore 지정 합니다.

1. 그러나 클러스터에 필요한 저장소 계정에 대 한 액세스 권한이 있는 경우에는 테이블의 실제 데이터에 액세스할 수 없습니다.
HDInsight 3.6 클러스터의 Hive 테이블 저장소 계정이 새 HDInsight 4.0 클러스터의 기본 또는 보조 저장소 계정으로 지정 되어 있는지 확인 합니다.
HDInsight 클러스터에 저장소 계정을 추가 하는 방법에 대 한 자세한 내용은 [hdinsight에 추가 저장소 계정 추가](../hdinsight-hadoop-add-storage.md)를 참조 하세요.

### <a name="5-complete-migration-with-a-post-upgrade-tool-in-hdinsight-40"></a>5. HDInsight 4.0에서 업그레이드 후 도구를 사용 하 여 마이그레이션 완료

관리 되는 테이블은 기본적으로 HDInsight 4.0에서 ACID 규격 이어야 합니다. Metastore 마이그레이션을 완료 한 후에는 업그레이드 후 도구를 실행 하 여 이전에 ACID로 관리 되지 않는 관리 되는 테이블이 HDInsight 4.0 클러스터와 호환 되도록 합니다. 이 도구는 다음 변환을 적용 합니다.

|3.6 |4.0 |
|---|---|
|외부 테이블|외부 테이블|
|비 ACID 관리 테이블|속성이 ' external. n e t. n e t ' = ' t r u e ' 인 외부 테이블|
|ACID 관리 테이블|ACID 관리 테이블|

SSH 셸을 사용 하 여 HDInsight 4.0 클러스터에서 Hive 업그레이드 후 도구를 실행 합니다.

1. SSH를 사용 하 여 클러스터 헤드 노드에 연결 합니다. 지침은 [SSH를 사용 하 여 HDInsight에 연결](../hdinsight-hadoop-linux-use-ssh-unix.md) 을 참조 하세요.
1. 을 실행 하 여 Hive 사용자로 로그인 셸을 엽니다. `sudo su - hive`
1. 셸에서 다음 명령을 실행 합니다.

    ```bash
    STACK_VERSION=$(hdp-select status hive-server2 | awk '{ print $3; }')
    /usr/hdp/$STACK_VERSION/hive/bin/hive --config /etc/hive/conf --service  strictmanagedmigration --hiveconf hive.strict.managed.tables=true -m automatic --modifyManagedTables
    ```

도구가 완료 되 면 Hive 웨어하우스가 HDInsight 4.0에 대해 준비 됩니다.

## <a name="migrate-from-internal-metastore"></a>내부 metastore에서 마이그레이션

HDInsight 3.6 클러스터가 내부 Hive metastore를 사용 하는 경우 아래 단계에 따라 metastore에서 개체 정의를 내보내는 Hive 쿼리를 생성 하는 스크립트를 실행 합니다.

HDInsight 3.6 및 4.0 클러스터는 동일한 저장소 계정을 사용 해야 합니다.

> [!NOTE]
>
> * ACID 테이블의 경우 테이블 아래에 있는 데이터의 새 복사본이 생성 됩니다.
>
> * 이 스크립트는 Hive 데이터베이스, 테이블 및 파티션의 마이그레이션을 지원 합니다. 뷰, Udf 및 테이블 제약 조건과 같은 다른 메타 데이터 개체는 수동으로 복사 해야 합니다.
>
> * 이 스크립트가 완료 되 면 이전 클러스터가 스크립트에서 참조 되는 테이블 또는 데이터베이스에 액세스 하는 데 더 이상 사용 되지 않는 것으로 간주 됩니다.
>
> * 모든 관리 되는 테이블은 HDInsight 4.0에서 트랜잭션이 됩니다. 필요에 따라 데이터를 ' external. purge ' = ' t r u e ' 인 외부 테이블로 내보내서 트랜잭션 되지 않은 테이블을 유지 합니다. 예를 들면 다음과 같습니다.
>
>    ```SQL
>    create table tablename_backup like tablename;
>    insert overwrite table tablename_backup select * from tablename;
>    create external table tablename_tmp like tablename;
>    insert overwrite table tablename_tmp select * from tablename;
>    alter table tablename_tmp set tblproperties('external.table.purge'='true');
>    drop table tablename;
>    alter table tablename_tmp rename to tablename;
>    ```

1. [Secure Shell (SSH) 클라이언트](../hdinsight-hadoop-linux-use-ssh-unix.md)를 사용 하 여 HDInsight 3.6 클러스터에 연결 합니다.

1. Open SSH 세션에서 다음 스크립트 파일을 다운로드 하 여 **alltables.sql. hql**라는 파일을 생성 합니다.

    ```bash
    wget https://hdiconfigactions.blob.core.windows.net/hivemetastoreschemaupgrade/exporthive_hdi_3_6.sh
    chmod 755 exporthive_hdi_3_6.sh
    ```

    * ESP를 사용 하지 않는 정기적인 HDInsight 클러스터의 경우를 실행 하기만 하면 `exporthive_hdi_3_6.sh` 됩니다.

    * ESP를 사용 하는 클러스터의 경우 kinit 및 beeline에 대 한 인수를 수정 합니다. 다음을 실행 합니다. 전체 Hive 권한이 있는 Azure AD 사용자의 사용자 및 도메인을 정의 합니다.

        ```bash
        USER="USER"  # replace USER
        DOMAIN="DOMAIN"  # replace DOMAIN
        DOMAIN_UPPER=$(printf "%s" "$DOMAIN" | awk '{ print toupper($0) }')
        kinit "$USER@$DOMAIN_UPPER"
        ```

        ```bash
        hn0=$(grep hn0- /etc/hosts | xargs | cut -d' ' -f4)
        BEE_CMD="beeline -u 'jdbc:hive2://$hn0:10001/default;principal=hive/_HOST@$DOMAIN_UPPER;auth-kerberos;transportMode=http' -n "$USER@$DOMAIN" --showHeader=false --silent=true --outputformat=tsv2 -e"
        ./exporthive_hdi_3_6.sh "$BEE_CMD"
        ```

1. SSH 세션을 종료합니다. 그런 다음 scp 명령을 입력 하 여 **alltables.sql. hql** 를 로컬로 다운로드 합니다.

    ```bash
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:alltables.hql c:/hdi
    ```

1. *새* HDInsight 클러스터에 alltables.sql를 업로드 **합니다** .

    ```bash
    scp c:/hdi/alltables.hql sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/home/sshuser/
    ```

1. 그런 다음 SSH를 사용 하 여 *새* HDInsight 4.0 클러스터에 연결 합니다. 이 클러스터에 대 한 SSH 세션에서 다음 코드를 실행 합니다.

    ESP 제외:

    ```bash
    beeline -u "jdbc:hive2://localhost:10001/;transportMode=http" -f alltables.hql
    ```

    ESP 사용:

    ```bash
    USER="USER"  # replace USER
    DOMAIN="DOMAIN"  # replace DOMAIN
    DOMAIN_UPPER=$(printf "%s" "$DOMAIN" | awk '{ print toupper($0) }')
    kinit "$USER@$DOMAIN_UPPER"
    ```

    ```bash
    hn0=$(grep hn0- /etc/hosts | xargs | cut -d' ' -f4)
    beeline -u "jdbc:hive2://$hn0:10001/default;principal=hive/_HOST@$DOMAIN_UPPER;auth-kerberos;transportMode=http" -n "$USER@$DOMAIN" -f alltables.hql
    ```

외부 metastore 마이그레이션에 대 한 업그레이드 후 도구는 여기에 적용 되지 않습니다. HDInsight 3.6의 ACID에서 관리 되지 않는 테이블이 HDInsight 4.0의 ACID 관리 되는 테이블로 변환 되기 때문입니다.

> [!Important]  
> Hdinsight 3.6 클러스터를 비롯 한 다른 서비스 또는 응용 프로그램에서는 HDInsight 4.0 (3.6에서 마이그레이션된 테이블 포함)의 관리 되는 테이블에 액세스 하지 않아야 합니다.

## <a name="secure-hive-across-hdinsight-versions"></a>HDInsight 버전 간 Hive 보안

Hdinsight 3.6부터 hdinsight는 Enterprise Security Package (ESP)를 사용 하 여 Azure Active Directory와 통합 됩니다. ESP는 Kerberos 및 Apache 레인저를 사용 하 여 클러스터 내의 특정 리소스에 대 한 권한을 관리 합니다. 다음 단계를 수행 하 여 HDInsight 3.6의 Hive에 대해 배포 된 레인저 정책을 HDInsight 4.0로 마이그레이션할 수 있습니다.

1. HDInsight 3.6 클러스터의 레인저 Service Manager 패널로 이동 합니다.
2. **HIVE** 라는 정책으로 이동 하 여 정책을 json 파일로 내보냅니다.
3. 내보낸 정책 json에서 참조 하는 모든 사용자가 새 클러스터에 존재 하는지 확인 합니다. 사용자가 정책 json에서 참조 되지만 새 클러스터에 존재 하지 않는 경우 새 클러스터에 사용자를 추가 하거나 정책에서 참조를 제거 합니다.
4. HDInsight 4.0 클러스터의 **레인저 Service Manager** 패널로 이동 합니다.
5. **HIVE** 라는 정책으로 이동 하 고 2 단계에서 레인저 정책 json을 가져옵니다.

## <a name="check-compatibility-and-modify-codes-as-needed-in-test-app"></a>테스트 앱에서 필요에 따라 호환성 및 수정 코드 확인

기존 프로그램 및 쿼리와 같은 작업을 마이그레이션하는 경우 릴리스 정보 및 설명서에서 변경 내용을 확인 하 고 필요에 따라 변경 내용을 적용 하세요. HDInsight 3.6 클러스터가 공유 Spark 및 Hive metastore를 사용 하는 경우 [Hive 웨어하우스 커넥터를 사용 하 여 추가 구성이](./apache-hive-warehouse-connector.md) 필요 합니다.

## <a name="deploy-new-app-for-production"></a>프로덕션을 위해 새 앱 배포

새 클러스터로 전환 하려는 경우, 예를 들어 새 클라이언트 응용 프로그램을 설치 하 여 새 프로덕션 환경으로 사용 하거나 기존 클라이언트 응용 프로그램을 업그레이드 하 고 HDInsight 4.0로 전환할 수 있습니다.

## <a name="switch-hdinsight-40-to-the-production"></a>HDInsight 4.0을 프로덕션 환경으로 전환

테스트 하는 동안 metastore에서 차이점이 생성 된 경우 전환 하기 전에 변경 내용을 업데이트 해야 합니다. 이 경우 metastore를 내보낸 & 가져온 다음 다시 업그레이드할 수 있습니다.

## <a name="remove-the-old-production"></a>이전 프로덕션 제거

릴리스가 완료 되 고 완벽 하 게 작동 하는지 확인 한 후 버전 3.6 및 이전 metastore를 제거할 수 있습니다. 환경을 삭제 하기 전에 모든 항목이 마이그레이션 되었는지 확인 하세요.

## <a name="query-execution-across-hdinsight-versions"></a>HDInsight 버전 간 쿼리 실행

HDInsight 3.6 클러스터 내에서 Hive/LLAP 쿼리를 실행 하 고 디버그 하는 방법에는 두 가지가 있습니다. HiveCLI는 명령줄 환경을 제공 하며 [Tez 뷰/Hive 보기](https://docs.microsoft.com/azure/hdinsight/hadoop/apache-hadoop-use-hive-ambari-view) 는 GUI 기반 워크플로를 제공 합니다.

HDInsight 4.0에서 HiveCLI는 Beeline로 대체 되었습니다. Tez 뷰/Hive 보기는 GUI 기반 워크플로를 제공 합니다. HiveCLI은 Hiveserver 1의 thrift 클라이언트 이며, Beeline는 Hiveserver 2에 대 한 액세스를 제공 하는 JDBC 클라이언트입니다. Beeline를 사용 하 여 다른 JDBC 호환 데이터베이스 끝점에 연결할 수도 있습니다. Beeline는 설치 하지 않아도 HDInsight 4.0에서 기본 제공 됩니다.

## <a name="next-steps"></a>다음 단계

* [HDInsight 4.0 공지](../hdinsight-version-release.md)
* [HDInsight 4.0 심층 이해](https://azure.microsoft.com/blog/deep-dive-into-azure-hdinsight-4-0/)
* [Hive 3 ACID 테이블](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.1.0/using-hiveql/content/hive_3_internals.html)
