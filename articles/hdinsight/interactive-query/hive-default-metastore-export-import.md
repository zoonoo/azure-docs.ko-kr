---
title: Azure HDInsight에서 기본 Hive 메타스토어를 외부 메타스토어로 마이그레이션
description: Azure HDInsight에서 기본 Hive 메타스토어를 외부 메타스토어로 마이그레이션
author: kevxmsft
ms.author: kevx
ms.reviewer: ''
ms.service: hdinsight
ms.topic: how-to
ms.date: 11/4/2020
ms.openlocfilehash: 825204fe40125a65e8e6f27c6973417813700a9e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101745349"
---
# <a name="migrate-default-hive-metastore-db-to-external-metastore-db"></a>기본 Hive metastore DB를 외부 metastore DB로 마이그레이션

이 문서에서는 Hive 용 [기본 METASTORE DB](../hdinsight-use-external-metadata-stores.md#default-metastore) 에서 HDInsight의 외부 SQL Database로 메타 데이터를 마이그레이션하는 방법을 보여 줍니다. 

## <a name="why-migrate-to-external-metastore-db"></a>외부 metastore DB로 마이그레이션하는 이유

* 기본 metastore DB는 기본 SKU로 제한 되며 프로덕션 규모의 워크 로드를 처리할 수 없습니다.

* 외부 metastore DB를 통해 고객은 동일한 metastore DB를 공유 하는 새 HDInsight 클러스터를 추가 하 여 Hive 계산 리소스를 수평적으로 확장할 수 있습니다.

* HDInsight 3.6에서 4.0로 마이그레이션하려면 Hive 스키마 버전을 업그레이드 하기 전에 메타 데이터를 외부 metastore DB로 마이그레이션해야 합니다. [Hdinsight 3.6에서 hdinsight 4.0로 워크 로드 마이그레이션을](./apache-hive-migrate-workloads.md)참조 하세요.

기본 metastore DB에는 제한 된 계산 용량이 있으므로 메타 데이터를 마이그레이션하는 동안 클러스터의 다른 작업에서 사용률을 낮게 설정 하는 것이 좋습니다.

원본 및 대상 Db는 동일한 HDInsight 버전과 동일한 저장소 계정을 사용 해야 합니다. HDInsight 버전을 3.6에서 4.0로 업그레이드 하는 경우이 문서의 단계를 먼저 수행 합니다. 그런 다음 [여기](./apache-hive-migrate-workloads.md)에서 공식 업그레이드 단계를 따르세요.

## <a name="prerequisites"></a>필수 구성 요소

[Azure Data Lake Storage Gen1](../overview-data-lake-storage-gen1.md)사용 하는 경우 Hive 테이블 위치는 Azure Data Lake Storage Gen1에 대 한 클러스터의 HDFS 구성에 따라 달라질 수 있습니다. 다음 스크립트 작업을 실행 하 여 이러한 위치를 다른 클러스터로 이식할 수 있게 합니다. [실행 중인 클러스터에 대 한 동작 스크립팅을](../hdinsight-hadoop-customize-cluster-linux.md#script-action-to-a-running-cluster)참조 하세요.

작업은 symlink를 전체 경로로 바꾸는 것과 비슷합니다.

|속성 | 값 |
|---|---|
|Bash 스크립트 URI|`https://hdiconfigactions.blob.core.windows.net/linuxhivemigrationv01/hive-adl-expand-location-v01.sh`|
|노드 유형|Head|
|매개 변수|""|

## <a name="migrate-with-exportimport-using-sqlpackage"></a>Sqlpackage를 사용 하 여 내보내기/가져오기를 사용 하 여 마이그레이션

2020-10-15 이후에만 생성 된 An HDInsight 클러스터는를 사용 하 여 Hive 기본 metastore DB에 대 한 SQL 내보내기/가져오기를 지원 `sqlpackage` 합니다.

1. 클러스터에 [sqlpackage](https://docs.microsoft.com/sql/tools/sqlpackage-download#get-sqlpackage-net-core-for-linux) 를 설치 합니다.

2. 다음 명령을 실행 하 여 기본 metastore DB를 BACPAC 파일로 내보냅니다.

    ```bash
    wget "https://hdiconfigactions.blob.core.windows.net/linuxhivemigrationv01/hive_metastore_tool.py"
    SQLPACKAGE_FILE='/home/sshuser/sqlpackage/sqlpackage'  # replace with sqlpackage location
    TARGET_FILE='hive.bacpac'
    sudo python hive_metastore_tool.py --sqlpackagefile $SQLPACKAGE_FILE --targetfile $TARGET_FILE
    ```

3. BACPAC 파일을 저장 합니다. 다음은 옵션입니다.

    ```bash
    hdfs dfs -mkdir -p /bacpacs
    hdfs dfs -put $TARGET_FILE /bacpacs/
    ```

4. [여기](../../azure-sql/database/database-import.md)에 나열 된 단계를 사용 하 여 BACPAC 파일을 새 데이터베이스로 가져옵니다.

5. 새 데이터베이스를 [새 HDInsight 클러스터에서 외부 METASTORE DB로 구성할](../hdinsight-use-external-metadata-stores.md#select-a-custom-metastore-during-cluster-creation)준비가 되었습니다.

## <a name="migrate-using-hive-script"></a>Hive 스크립트를 사용 하 여 마이그레이션

2020-10-15 이전에 만든 클러스터는 기본 metastore DB의 내보내기/가져오기를 지원 하지 않습니다.

이러한 클러스터의 경우 [외부 HIVE METASTORE DB](../hdinsight-use-external-metadata-stores.md#select-a-custom-metastore-during-cluster-creation)를 사용 하는 두 번째 클러스터를 사용 하 여 [저장소 계정 간에 Hive 테이블 복사](./hive-migration-across-storage-accounts.md)가이드를 따르세요. 두 번째 클러스터는 동일한 저장소 계정을 사용할 수 있지만 새 기본 파일 시스템을 사용 해야 합니다.

### <a name="option-to-shallow-copy"></a>"얕은" 복사 옵션
위의 가이드를 사용 하 여 테이블이 "deep"로 복사 되 면 저장소 사용이 두 배가 됩니다. 원본 저장소 컨테이너에서 데이터를 수동으로 정리 해야 합니다.
대신, 비트랜잭션 인 경우 테이블을 "얕은" 복사할 수 있습니다. HDInsight 3.6의 모든 Hive 테이블은 기본적으로 비트랜잭션 이지만 외부 테이블만 HDInsight 4.0에서 비트랜잭션입니다. 트랜잭션 테이블은 전체 복사 해야 합니다. 비트랜잭션 테이블을 단순 복사 하려면 다음 단계를 수행 합니다.

1. 원본 클러스터의 기본 헤드 노드에서 스크립트 [hive-ddls.sh](https://hdiconfigactions.blob.core.windows.net/linuxhivemigrationv01/hive-ddls.sh) 을 실행 하 여 모든 hive 테이블에 대 한 DDL을 생성 합니다.
2. DDL은 라는 로컬 Hive 스크립트에 기록 됩니다 `/tmp/hdi_hive_ddls.hql` . 외부 Hive metastore DB를 사용 하는 대상 클러스터에서이를 실행 합니다.

## <a name="verify-that-all-hive-tables-are-imported"></a>모든 Hive 테이블을 가져왔는지 확인 합니다.

다음 명령은 metastore DB에 대 한 SQL 쿼리를 사용 하 여 모든 Hive 테이블 및 해당 데이터 위치를 인쇄 합니다. 새 metastore DB에 테이블이 없는지 확인 하려면 새 클러스터와 이전 클러스터의 출력을 비교 합니다.

```bash
SCRIPT_FNAME='hive_metastore_tool.py'
SCRIPT="/tmp/$SCRIPT_FNAME"
wget -O "$SCRIPT" "https://hdiconfigactions.blob.core.windows.net/linuxhivemigrationv01/$SCRIPT_FNAME"
OUTPUT_FILE='/tmp/hivetables.csv'
QUERY="SELECT DBS.NAME, TBLS.TBL_NAME, SDS.LOCATION FROM SDS, TBLS, DBS WHERE TBLS.SD_ID = SDS.SD_ID AND TBLS.DB_ID = DBS.DB_ID ORDER BY DBS.NAME, TBLS.TBL_NAME ASC;"
sudo python "$SCRIPT" --query "$QUERY" > $OUTPUT_FILE
```

## <a name="further-reading"></a>추가 정보

* [HDInsight 3.6에서 4.0로 워크 로드 마이그레이션](./apache-hive-migrate-workloads.md)
* [저장소 계정 간 Hive 워크 로드 마이그레이션](./hive-migration-across-storage-accounts.md)
* [HDInsight에서 Beeline에 연결](../hadoop/connect-install-beeline.md)
* [권한 오류 문제 해결 테이블 만들기](./interactive-query-troubleshoot-permission-error-create-table.md)
