---
title: Azure HDInsight에서 기본 Hive 메타스토어를 외부 메타스토어로 마이그레이션
description: Azure HDInsight에서 기본 Hive 메타스토어를 외부 메타스토어로 마이그레이션
author: kevxmsft
ms.author: kevx
ms.reviewer: ''
ms.service: hdinsight
ms.topic: how-to
ms.date: 11/4/2020
ms.openlocfilehash: 4a0258d5e448c59baa1cd63e98058fe7116a8485
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105566118"
---
# <a name="migrate-default-hive-metastore-db-to-external-metastore-db"></a>기본 Hive 메타스토어 DB를 외부 메타스토어 DB로 마이그레이션

이 문서에서는 Hive용 [기본 메타스토어 DB](../hdinsight-use-external-metadata-stores.md#default-metastore)의 메타데이터를 HDInsight의 외부 SQL Database로 마이그레이션하는 방법을 보여 줍니다. 

## <a name="why-migrate-to-external-metastore-db"></a>외부 메타스토어 DB로 마이그레이션하는 이유

* 기본 메타스토어 DB는 기본 SKU로 제한되며 프로덕션 규모의 워크로드를 처리할 수 없습니다.

* 외부 메타스토어 DB를 통해 고객은 동일한 메타스토어 DB를 공유하는 새 HDInsight 클러스터를 추가하여 Hive 컴퓨팅 리소스를 수평적으로 확장할 수 있습니다.

* HDInsight 3.6에서 4.0으로 마이그레이션하는 경우 Hive 스키마 버전을 업그레이드하기 전에 메타데이터를 외부 메타스토어 DB로 마이그레이션해야 합니다. [HDInsight 3.6에서 HDInsight 4.0으로 워크로드 마이그레이션](./apache-hive-migrate-workloads.md)을 참조하세요.

기본 메타스토어 DB는 컴퓨팅 용량이 제한되어 있으므로, 메타데이터를 마이그레이션하는 동안 클러스터의 다른 작업에서 사용률을 낮추는 것이 좋습니다.

원본/대상 DB는 동일한 HDInsight 버전과 동일한 스토리지 계정을 사용해야 합니다. HDInsight 버전을 3.6에서 4.0으로 업그레이드하는 경우, 먼저 이 문서의 단계를 완료합니다. 그런 다음 [여기](./apache-hive-migrate-workloads.md)에서 공식 업그레이드 단계를 따릅니다.

## <a name="prerequisites"></a>필수 구성 요소

[Azure Data Lake Storage Gen1](../overview-data-lake-storage-gen1.md)을 사용하는 경우 Hive 테이블 위치는 Azure Data Lake Storage Gen1에 대한 클러스터의 HDFS 구성에 따라 달라질 수 있습니다. 다음 스크립트 작업을 실행하여 이러한 위치를 다른 클러스터로 이식할 수 있도록 합니다. [실행 중인 클러스터에 대한 동작 스크립팅](../hdinsight-hadoop-customize-cluster-linux.md#script-action-to-a-running-cluster)을 참조하세요.

이 작업은 symlink를 전체 경로로 바꾸는 것과 유사합니다.

|속성 | 값 |
|---|---|
|Bash 스크립트 URI|`https://hdiconfigactions.blob.core.windows.net/linuxhivemigrationv01/hive-adl-expand-location-v01.sh`|
|노드 유형|Head|
|매개 변수|""|

## <a name="migrate-with-exportimport-using-sqlpackage"></a>sqlpackage를 사용하여 내보내기/가져오기로 마이그레이션

2020년 10월 15일 이후에 만들어진 HDInsight 클러스터는 `sqlpackage`을(를) 사용하여 Hive 기본 메타스토어 DB에 대한 SQL 내보내기/가져오기를 지원합니다.

1. 클러스터에 [sqlpackage](/sql/tools/sqlpackage-download#get-sqlpackage-net-core-for-linux)를 설치합니다.

2. 다음 명령을 실행하여 기본 메타스토어 DB를 BACPAC 파일로 내보냅니다.

    ```bash
    wget "https://hdiconfigactions.blob.core.windows.net/linuxhivemigrationv01/hive_metastore_tool.py"
    SQLPACKAGE_FILE='/home/sshuser/sqlpackage/sqlpackage'  # replace with sqlpackage location
    TARGET_FILE='hive.bacpac'
    sudo python hive_metastore_tool.py --sqlpackagefile $SQLPACKAGE_FILE --targetfile $TARGET_FILE
    ```

3. BACPAC 파일을 저장합니다. 다음은 옵션입니다.

    ```bash
    hdfs dfs -mkdir -p /bacpacs
    hdfs dfs -put $TARGET_FILE /bacpacs/
    ```

4. [여기](../../azure-sql/database/database-import.md)에 나열된 단계를 사용하여 BACPAC 파일을 새 데이터베이스로 가져옵니다.

5. 새 데이터베이스를 [새 HDInsight 클러스터에서 외부 메타스토어 DB로 구성](../hdinsight-use-external-metadata-stores.md#select-a-custom-metastore-during-cluster-creation)할 준비가 되었습니다.

## <a name="migrate-using-hive-script"></a>Hive 스크립트를 사용하여 마이그레이션

2020년 10월 15일 이전에 만든 클러스터는 기본 메타스토어 DB의 내보내기/가져오기를 지원하지 않습니다.

이러한 클러스터의 경우 [외부 Hive 메타스토어 DB](../hdinsight-use-external-metadata-stores.md#select-a-custom-metastore-during-cluster-creation)가 있는 두 번째 클러스터를 사용하여 [스토리지 계정 간에 Hive 테이블 복사](./hive-migration-across-storage-accounts.md) 가이드를 따르세요. 두 번째 클러스터에서는 동일한 스토리지 계정을 사용할 수 있지만, 새 기본 파일 시스템을 사용해야 합니다.

### <a name="option-to-shallow-copy"></a>"단순" 복사 옵션
위의 가이드를 사용하여 테이블을 "전체" 복사하면 저장소 사용량이 두 배가 됩니다. 원본 스토리지 컨테이너에서 데이터를 수동으로 정리해야 합니다.
대신, 비트랜잭션인 경우 테이블을 "단순" 복사할 수 있습니다. HDInsight 3.6에서는 모든 Hive 테이블이 기본적으로 비트랜잭션이지만, HDInsight 4.0에서는 외부 테이블만 비트랜잭션입니다. 트랜잭션 테이블은 전체 복사해야 합니다. 비트랜잭션 테이블을 단순 복사하려면 다음 단계를 따릅니다.

1. 원본 클러스터의 기본 헤드노드에서 스크립트 [hive-ddls.sh](https://hdiconfigactions.blob.core.windows.net/linuxhivemigrationv01/hive-ddls.sh)를 실행하여 모든 Hive 테이블용 DDL을 생성합니다.
2. DDL은 `/tmp/hdi_hive_ddls.hql`(이)라는 로컬 Hive 스크립트에 작성됩니다. 외부 Hive 메타스토어 DB를 사용하는 대상 클러스터에서 이를 실행합니다.

## <a name="verify-that-all-hive-tables-are-imported"></a>모든 Hive 테이블을 가져왔는지 확인합니다.

다음 명령은 메타스토어 DB에서 SQL 쿼리를 사용하여 모든 Hive 테이블 및 해당 데이터 위치를 인쇄합니다. 새 메타스토어 DB에 누락된 테이블이 없는지 확인하려면 새 클러스터와 이전 클러스터의 출력을 비교합니다.

```bash
SCRIPT_FNAME='hive_metastore_tool.py'
SCRIPT="/tmp/$SCRIPT_FNAME"
wget -O "$SCRIPT" "https://hdiconfigactions.blob.core.windows.net/linuxhivemigrationv01/$SCRIPT_FNAME"
OUTPUT_FILE='/tmp/hivetables.csv'
QUERY="SELECT DBS.NAME, TBLS.TBL_NAME, SDS.LOCATION FROM SDS, TBLS, DBS WHERE TBLS.SD_ID = SDS.SD_ID AND TBLS.DB_ID = DBS.DB_ID ORDER BY DBS.NAME, TBLS.TBL_NAME ASC;"
sudo python "$SCRIPT" --query "$QUERY" > $OUTPUT_FILE
```

## <a name="further-reading"></a>추가 참고 자료

* [HDInsight 3.6의 워크로드를 HDInsight 4.0으로 마이그레이션](./apache-hive-migrate-workloads.md)
* [스토리지 계정 간 Hive 워크로드 마이그레이션](./hive-migration-across-storage-accounts.md)
* [HDInsight에서 Beeline에 연결](../hadoop/connect-install-beeline.md)
* [권한 오류 문제 해결 테이블 만들기](./interactive-query-troubleshoot-permission-error-create-table.md)