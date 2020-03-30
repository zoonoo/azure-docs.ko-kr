---
title: Azure HDInsight 3.6 하이브 워크로드를 HDInsight 4.0으로 마이그레이션
description: HDInsight 3.6에서 아파치 하이브 워크로드를 HDInsight 4.0으로 마이그레이션하는 방법을 알아봅니다.
author: msft-tacox
ms.author: tacox
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/13/2019
ms.openlocfilehash: ec96189185a06c1fcbd95eed6216ade47f3089c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79214652"
---
# <a name="migrate-azure-hdinsight-36-hive-workloads-to-hdinsight-40"></a>Azure HDInsight 3.6 하이브 워크로드를 HDInsight 4.0으로 마이그레이션

이 문서에서는 HDInsight 3.6에서 아파치 하이브 및 LLAP 워크로드를 HDInsight 4.0으로 마이그레이션하는 방법을 보여 주며 있습니다. HDInsight 4.0은 구체화된 뷰 및 쿼리 결과 캐싱과 같은 최신 Hive 및 LLAP 기능을 제공합니다. 워크로드를 HDInsight 4.0으로 마이그레이션하면 HDInsight 3.6에서는 사용할 수 없는 Hive 3의 많은 최신 기능을 사용할 수 있습니다.

이 문서에서는 다음 주제를 다룹니다.

* 하이브 메타데이터를 HDInsight 4.0으로 마이그레이션
* ACID 및 비ACID 테이블의 안전한 마이그레이션
* HDInsight 버전 에서 Hive 보안 정책 보존
* HDInsight 3.6에서 HDInsight 4.0으로 쿼리 실행 및 디버깅

Hive의 한 가지 장점은 외부 데이터베이스(Hive Metastore라고 함)로 메타데이터를 내보내는 기능입니다. **Hive Metastore는** 테이블 저장소 위치, 열 이름 및 테이블 인덱스 정보를 포함한 테이블 통계를 저장합니다. 메타스토어 데이터베이스 스키마는 Hive 버전간에 다릅니다. Hive 메타스토어를 안전하게 업그레이드하는 데 권장되는 방법은 현재 프로덕션 환경 대신 복사본을 만들고 업그레이드하는 것입니다.

## <a name="copy-metastore"></a>메타스토어 복사

HDInsight 3.6 및 HDInsight 4.0은 다른 메타스토어 스키마를 필요로 하며 단일 메타스토어를 공유할 수 없습니다.

### <a name="external-metastore"></a>외부 메타스토어

외부 메타스토어의 새 복사본을 만듭니다. 외부 메타스토어를 사용하는 경우 메타스토어의 복사본을 만드는 안전하고 쉬운 방법 중 하나는 SQL Database 복원 기능을 사용하여 다른 이름으로 [데이터베이스를 복원하는](../../sql-database/sql-database-recovery-using-backups.md#point-in-time-restore) 것입니다.  HDInsight 클러스터에 외부 메타스토어를 연결하는 방법에 대해 자세히 알아보려면 [Azure HDInsight의 외부 메타데이터 저장소 사용을](../hdinsight-use-external-metadata-stores.md) 참조하십시오.

### <a name="internal-metastore"></a>내부 메타스토어

내부 메타스토어를 사용하는 경우 쿼리를 사용하여 Hive 메타스토어에서 개체 정의를 내보내고 새 데이터베이스로 가져올 수 있습니다.

이 스크립트가 완료되면 스크립트에서 참조된 테이블이나 데이터베이스에 액세스하는 데 이전 클러스터가 더 이상 사용되지 않는다고 가정합니다.

> [!NOTE]
> ACID 테이블의 경우 테이블 아래에 있는 데이터의 새 복사본이 만들어집니다.

1. [보안 셸(SSH) 클라이언트를](../hdinsight-hadoop-linux-use-ssh-unix.md)사용하여 HDInsight 클러스터에 연결합니다.

1. 다음 명령을 입력하여 열려 있는 SSH 세션에서 [Beeline 클라이언트와](../hadoop/apache-hadoop-use-hive-beeline.md) HiveServer2에 연결합니다.

    ```hiveql
    for d in `beeline -u "jdbc:hive2://localhost:10001/;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "show databases;"`; 
    do
        echo "Scanning Database: $d"
        echo "create database if not exists $d; use $d;" >> alltables.hql; 
        for t in `beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "show tables;"`;
        do
            echo "Copying Table: $t"
            ddl=`beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "show create table $t;"`;

            echo "$ddl;" >> alltables.hql;
            lowerddl=$(echo $ddl | awk '{print tolower($0)}')
            if [[ $lowerddl == *"'transactional'='true'"* ]]; then
                if [[ $lowerddl == *"partitioned by"* ]]; then
                    # partitioned
                    raw_cols=$(beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "show create table $t;" | tr '\n' ' ' | grep -io "CREATE TABLE .*" | cut -d"(" -f2- | cut -f1 -d")" | sed 's/`//g');
                    ptn_cols=$(beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "show create table $t;" | tr '\n' ' ' | grep -io "PARTITIONED BY .*" | cut -f1 -d")" | cut -d"(" -f2- | sed 's/`//g');
                    final_cols=$(echo "(" $raw_cols "," $ptn_cols ")")

                    beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "create external table ext_$t $final_cols TBLPROPERTIES ('transactional'='false');";
                    beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "insert into ext_$t select * from $t;";
                    staging_ddl=`beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "show create table ext_$t;"`;
                    dir=$(echo $staging_ddl | grep -io " LOCATION .*" | grep -m1 -o "'.*" | sed "s/'[^-]*//2g" | cut -c2-);

                    parsed_ptn_cols=$(echo $ptn_cols| sed 's/ [a-z]*,/,/g' | sed '$s/\w*$//g');
                    echo "create table flattened_$t $final_cols;" >> alltables.hql;
                    echo "load data inpath '$dir' into table flattened_$t;" >> alltables.hql;
                    echo "insert into $t partition($parsed_ptn_cols) select * from flattened_$t;" >> alltables.hql;
                    echo "drop table flattened_$t;" >> alltables.hql;
                    beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "drop table ext_$t";
                else
                    # not partitioned
                    beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "create external table ext_$t like $t TBLPROPERTIES ('transactional'='false');";
                    staging_ddl=`beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "show create table ext_$t;"`;
                    dir=$(echo $staging_ddl | grep -io " LOCATION .*" | grep -m1 -o "'.*" | sed "s/'[^-]*//2g" | cut -c2-);

                    beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "insert into ext_$t select * from $t;";
                    echo "load data inpath '$dir' into table $t;" >> alltables.hql;
                    beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "drop table ext_$t";
                fi
            fi
            echo "$ddl" | grep -q "PARTITIONED\s*BY" && echo "MSCK REPAIR TABLE $t;" >> alltables.hql;
        done;
    done
    ```

    이 명령은 **alltables.hql**라는 파일을 생성 합니다.

1. SSH 세션을 종료합니다. 그런 다음 scp 명령을 입력하여 **alltables.hql을** 로컬로 다운로드합니다.

    ```bash
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:alltables.hql c:/hdi
    ```

1. **alltables.hql을** *새로운* HDInsight 클러스터에 업로드합니다.

    ```bash
    scp c:/hdi/alltables.hql sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/home/sshuser/
    ```

1. 그런 다음 SSH를 사용하여 *새* HDInsight 클러스터에 연결합니다. SSH 세션에서 다음 코드를 실행합니다.

    ```bash
    beeline -u "jdbc:hive2://localhost:10001/;transportMode=http" -i alltables.hql
    ```


## <a name="upgrade-metastore"></a>메타스토어 업그레이드

메타스토어 **복사본이** 완료되면 기존 HDInsight 3.6 클러스터의 [스크립트 작업에서](../hdinsight-hadoop-customize-cluster-linux.md) 스키마 업그레이드 스크립트를 실행하여 새 메타스토어를 Hive 3 스키마로 업그레이드합니다. 이렇게 하면 데이터베이스를 HDInsight 4.0 메타스토어로 연결할 수 있습니다.

아래 표의 값을 더 사용하십시오. `SQLSERVERNAME DATABASENAME USERNAME PASSWORD` **공백으로 구분된 복사된** Hive 메타스토어에 대한 적절한 값으로 바꿉니다. SQL 서버 이름을 지정할 때 ".database.windows.net"을 포함하지 마십시오.

|속성 | 값 |
|---|---|
|스크립트 유형|- 사용자 지정|
|이름|하이브 업그레이드|
|Bash 스크립트 URI|`https://hdiconfigactions.blob.core.windows.net/hivemetastoreschemaupgrade/launch-schema-upgrade.sh`|
|노드 유형|Head|
|매개 변수|SQLSERVER NAME 데이터베이스 이름 사용자 이름 암호|

> [!Warning]  
> HDInsight 3.6 메타데이터 스키마를 HDInsight 4.0 스키마로 변환하는 업그레이드는 되돌릴 수 없습니다.

데이터베이스에 대해 다음 sql 쿼리를 실행하여 업그레이드를 확인할 수 있습니다.

```sql
select * from dbo.version
```

## <a name="migrate-hive-tables-to-hdinsight-40"></a>하이브 테이블을 HDInsight 4.0으로 마이그레이션

Hive Metastore를 HDInsight 4.0으로 마이그레이션하기 위한 이전 단계를 완료한 후 메타스토어에 기록된 테이블과 데이터베이스는 HDInsight 4.0 클러스터 `show tables` `show databases` 내에서 실행하거나 클러스터 내에서 볼 수 있습니다. HDInsight 4.0 클러스터의 쿼리 실행에 대한 자세한 내용은 [HDInsight 버전에서](#query-execution-across-hdinsight-versions) 쿼리 실행을 참조하십시오.

그러나 테이블의 실제 데이터는 클러스터가 필요한 저장소 계정에 액세스할 수 있도록 할 때까지 액세스할 수 없습니다. HDInsight 4.0 클러스터가 이전 HDInsight 3.6 클러스터와 동일한 데이터에 액세스할 수 있도록 하려면 다음 단계를 완료하십시오.

1. 테이블 또는 데이터베이스의 Azure 저장소 계정을 확인합니다.

1. HDInsight 4.0 클러스터가 이미 실행 중인 경우 Ambari를 통해 Azure 저장소 계정을 클러스터에 연결합니다. HDInsight 4.0 클러스터를 아직 만들지 않은 경우 Azure 저장소 계정이 기본 또는 보조 클러스터 저장소 계정으로 지정되어 있는지 확인합니다. HDInsight 클러스터에 스토리지 계정을 추가하는 자세한 내용은 [HDInsight에 추가 저장소 계정 추가](../hdinsight-hadoop-add-storage.md)를 참조하십시오.

## <a name="deploy-new-hdinsight-40-and-connect-to-the-new-metastore"></a>새로운 HDInsight 4.0 을 배포하고 새로운 메타스토어에 연결

스키마 업그레이드가 완료되면 새 HDInsight 4.0 클러스터를 배포하고 업그레이드된 메타스토어를 연결합니다. 이미 4.0을 배포한 경우 Ambari에서 메타스토어에 연결할 수 있도록 설정합니다.

## <a name="run-schema-migration-script-from-hdinsight-40"></a>HDInsight 4.0에서 스키마 마이그레이션 스크립트 실행

표는 HDInsight 3.6 및 HDInsight 4.0에서 다르게 처리됩니다. 따라서 다른 버전의 클러스터에 대해 동일한 테이블을 공유할 수 없습니다. HDInsight 4.0과 동시에 HDInsight 3.6을 사용하려면 각 버전에 대해 별도의 데이터 복사본이 있어야 합니다.

Hive 워크로드에는 ACID 테이블과 비ACID 테이블이 혼합되어 있을 수 있습니다. HDInsight 3.6(하이브 2)의 하이브와 HDInsight 4.0(하이브 3)의 하이브의 주요 차이점은 테이블에 대한 ACID 준수입니다. HDInsight 3.6에서 Hive ACID 컴플라이언스를 사용하도록 설정하려면 추가 구성이 필요하지만 HDInsight 4.0 테이블에서는 기본적으로 ACID를 준수합니다. 마이그레이션 전에 필요한 유일한 작업은 3.6 클러스터의 ACID 테이블에 대해 주요 압축을 실행하는 것입니다. Hive 보기 또는 Beeline에서 다음 쿼리를 실행합니다.

```sql
alter table myacidtable compact 'major';
```

HDInsight 3.6 및 HDInsight 4.0 ACID 테이블은 ACID 델타를 다르게 이해하기 때문에 이러한 압축이 필요합니다. 다짐은 일관성을 보장하는 깨끗한 슬레이트를 적용합니다. [Hive 마이그레이션 설명서의](https://docs.hortonworks.com/HDPDocuments/Ambari-2.7.3.0/bk_ambari-upgrade-major/content/prepare_hive_for_upgrade.html) 섹션 4에는 HDInsight 3.6 ACID 테이블의 대량 압축에 대한 지침이 포함되어 있습니다.

메타스토어 마이그레이션 및 압축 단계를 완료한 후에는 실제 웨어하우스를 마이그레이션할 수 있습니다. Hive 웨어하우스 마이그레이션을 완료하면 HDInsight 4.0 웨어하우스에 다음과 같은 속성이 있습니다.

|3.6 |4.0 |
|---|---|
|외부 테이블|외부 테이블|
|비트랜잭션 관리 테이블|외부 테이블|
|트랜잭션 관리 테이블|관리되는 테이블|

마이그레이션을 실행하기 전에 웨어하우스의 속성을 조정해야 할 수 있습니다. 예를 들어, 일부 테이블이 타사(예: HDInsight 3.6 클러스터)에서 액세스할 것으로 예상되는 경우 마이그레이션이 완료되면 해당 테이블이 외부여야 합니다. HDInsight 4.0에서는 모든 관리되는 테이블이 트랜잭션입니다. 따라서 HDInsight 4.0의 관리되는 테이블은 HDInsight 4.0 클러스터에서만 액세스해야 합니다.

테이블 속성이 올바르게 설정되면 SSH 셸을 사용하여 클러스터 헤드노드 중 하나에서 Hive 웨어하우스 마이그레이션 도구를 실행합니다.

1. SSH를 사용하여 클러스터 헤드노드에 연결합니다. 지침은 [SSH를 사용하여 HDInsight에 연결 참조하세요.](../hdinsight-hadoop-linux-use-ssh-unix.md)
1. 실행하여 Hive 사용자로 로그인 셸을 엽니다.`sudo su - hive`
1. 을 실행하여 데이터 플랫폼 스택 `ls /usr/hdp`버전을 결정합니다. 그러면 다음 명령에서 사용해야 하는 버전 문자열이 표시됩니다.
1. 셸에서 다음 명령을 실행합니다. 이전 `STACK_VERSION` 단계의 버전 문자열로 바꾸기:

```bash
/usr/hdp/STACK_VERSION/hive/bin/hive --config /etc/hive/conf --service  strictmanagedmigration --hiveconf hive.strict.managed.tables=true -m automatic --modifyManagedTables
```

마이그레이션 도구가 완료되면 Hive 웨어하우스가 HDInsight 4.0에 사용할 수 있습니다.

> [!Important]  
> HDInsight 4.0(3.6에서 마이그레이션된 테이블 포함)의 관리되는 테이블은 HDInsight 3.6 클러스터를 포함한 다른 서비스 또는 응용 프로그램에서 액세스해서는 안 됩니다.

## <a name="secure-hive-across-hdinsight-versions"></a>HDInsight 버전에서 하이브 보안

HDInsight 3.6 이후, HDInsight는 HDInsight 엔터프라이즈 보안 패키지 (ESP)를 사용하여 Azure 활성 디렉토리와 통합합니다. ESP는 Kerberos와 아파치 레인저를 사용하여 클러스터 내의 특정 리소스의 권한을 관리합니다. HDInsight 3.6에서 Hive에 대해 배포된 레인저 정책은 다음 단계를 통해 HDInsight 4.0으로 마이그레이션할 수 있습니다.

1. HDInsight 3.6 클러스터의 레인저 서비스 관리자 패널로 이동합니다.
2. **HIVE라는** 정책으로 이동하여 정책을 json 파일로 내보냅니다.
3. 내보낸 정책 json에 참조된 모든 사용자가 새 클러스터에 있는지 확인합니다. 사용자가 정책 json에서 참조되지만 새 클러스터에 없는 경우 사용자를 새 클러스터에 추가하거나 정책에서 참조를 제거합니다.
4. HDInsight 4.0 클러스터의 **레인저 서비스 관리자** 패널로 이동합니다.
5. **HIVE라는** 정책으로 이동하여 2단계에서 레인저 정책 json을 가져옵니다.

## <a name="check-compatibility-and-modify-codes-as-needed-in-test-app"></a>테스트 앱에서 필요에 따라 호환성을 확인하고 코드를 수정합니다.

기존 프로그램 및 쿼리와 같은 워크로드를 마이그레이션할 때 릴리스 정보 및 설명서에서 변경 사항을 확인하고 필요에 따라 변경 내용을 적용하십시오. HDInsight 3.6 클러스터가 공유 스파크 및 Hive 메타스토어를 사용하는 경우 [Hive 웨어하우스 커넥터를 사용한 추가 구성이](./apache-hive-warehouse-connector.md) 필요합니다.

## <a name="deploy-new-app-for-production"></a>프로덕션을 위한 새 앱 배포

새 클러스터로 전환하려면 예를 들어 새 클라이언트 응용 프로그램을 설치하고 새 프로덕션 환경으로 사용하거나 기존 클라이언트 응용 프로그램을 업그레이드하고 HDInsight 4.0으로 전환할 수 있습니다.

## <a name="switch-hdinsight-40-to-the-production"></a>HDInsight 4.0을 프로덕션으로 전환

테스트 하는 동안 메타 스토어에서 차이점이 만들어진 경우 전환 하기 직전에 변경 내용을 업데이트 해야 합니다. 이 경우 메타스토어를 가져오고 다시 업그레이드하기 & 다시 업그레이드할 수 있습니다.

## <a name="remove-the-old-production"></a>이전 생산 제거

릴리스가 완전하고 완벽하게 작동중임을 확인한 후에는 버전 3.6 및 이전 메타스토어를 제거할 수 있습니다. 환경을 삭제하기 전에 모든 것이 마이그레이션되었는지 확인하십시오.

## <a name="query-execution-across-hdinsight-versions"></a>HDInsight 버전에서 쿼리 실행

HDInsight 3.6 클러스터 내에서 Hive/LLAP 쿼리를 실행하고 디버깅하는 방법에는 두 가지가 있습니다. HiveCLI는 명령줄 환경을 제공하며 Tez 보기/Hive 보기는 GUI 기반 워크플로우를 제공합니다.

HDInsight 4.0에서 HiveCLI가 Beeline으로 대체되었습니다. HiveCLI는 Hiveserver 1의 중고품 클라이언트이며 Beeline은 Hiveserver 2에 대한 액세스를 제공하는 JDBC 클라이언트입니다. Beeline은 다른 JDBC 호환 데이터베이스 끝점에 연결하는 데도 사용할 수 있습니다. Beeline은 HDInsight 4.0에서 설치가 필요 없이 즉시 사용할 수 있습니다.

HDInsight 3.6에서 하이브 서버와 상호 작용하는 GUI 클라이언트는 암바리 하이브 뷰입니다. HDInsight 4.0은 암바리 뷰와 함께 제공되지 않습니다. 우리는 고객이 핵심 HDInsight 서비스가 아닌 DAS(데이터 분석 스튜디오)를 사용할 수 있는 방법을 제공했습니다. DAS는 HDInsight 클러스터와 함께 즉시 제공되지 않으며 공식적으로 지원되는 패키지가 아닙니다. 그러나 DAS는 다음과 같이 스크립트 [작업을](../hdinsight-hadoop-customize-cluster-linux.md) 사용하여 클러스터에 설치할 수 있습니다.

|속성 | 값 |
|---|---|
|스크립트 유형|- 사용자 지정|
|이름|Das|
|Bash 스크립트 URI|`https://hdiconfigactions.blob.core.windows.net/dasinstaller/LaunchDASInstaller.sh`|
|노드 유형|Head|

10~15분 기다린 다음 이 URL을 사용하여 `https://CLUSTERNAME.azurehdinsight.net/das/`데이터 분석 스튜디오를 시작합니다.

DAS에 액세스하기 전에 Ambari UI를 새로 고치거나 모든 Ambari 구성 요소를 다시 시작해야 할 수 있습니다.

DAS가 설치되면 쿼리 뷰어에서 실행한 쿼리가 표시되지 않으면 다음 단계를 수행합니다.

1. [DAS 설치 문제 해결을 위해 이 가이드에](https://docs.hortonworks.com/HDPDocuments/DAS/DAS-1.2.0/troubleshooting/content/das_queries_not_appearing.html)설명된 대로 Hive, Tez 및 DAS에 대한 구성을 설정합니다.
2. 다음 Azure 저장소 디렉터리 구성이 페이지 Blob이고 아래에 `fs.azure.page.blob.dirs`나열되어 있는지 확인합니다.
    * `hive.hook.proto.base-directory`
    * `tez.history.logging.proto-base-dir`
3. 두 헤드노드에서 HDFS, 하이브, 테즈 및 DAS를 다시 시작합니다.

## <a name="next-steps"></a>다음 단계

* [HDInsight 4.0 발표](../hdinsight-version-release.md)
* [HDInsight 4.0 심층 다이빙](https://azure.microsoft.com/blog/deep-dive-into-azure-hdinsight-4-0/)
* [하이브 3 ACID 테이블](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.1.0/using-hiveql/content/hive_3_internals.html)
