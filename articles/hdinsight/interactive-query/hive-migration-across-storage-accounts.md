---
title: Azure Storage의 새 계정으로 Hive 워크 로드 마이그레이션
description: Azure Storage의 새 계정으로 Hive 워크 로드 마이그레이션
author: kevxmsft
ms.author: kevx
ms.reviewer: ''
ms.service: hdinsight
ms.topic: how-to
ms.date: 12/11/2020
ms.openlocfilehash: 0d62bebddb7751c168ba2e487b2391a40bbc6e67
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101746101"
---
# <a name="hive-workload-migration-to-new-account-in-azure-storage"></a>Azure Storage의 새 계정으로 Hive 워크 로드 마이그레이션

스크립트 작업을 사용 하 여 HDInsight의 저장소 계정에서 Hive 테이블을 복사 하는 방법에 대해 알아봅니다. 이는로 마이그레이션하는 경우에 유용할 수 있습니다 [`Azure Data Lake Storage Gen2`](../hdinsight-hadoop-use-data-lake-storage-gen2.md) .

HDInsight 4.0에서 개별 Hive 테이블을 수동으로 복사 하려면 [Hive 내보내기/가져오기](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ImportExport)를 참조 하세요.

## <a name="prerequisites"></a>필수 구성 요소

* 다음 구성을 포함 하는 새 HDInsight 클러스터:
  * 기본 파일 시스템은 대상 저장소 계정에 있습니다. [Azure HDInsight 클러스터에서 azure Storage 사용](../hdinsight-hadoop-use-blob-storage.md)을 참조 하세요.
  * 해당 클러스터 버전은 원본 클러스터의와 일치 해야 합니다.
  * 새 외부 Hive metastore DB를 사용 합니다. [외부 메타 데이터 저장소 사용](../hdinsight-use-external-metadata-stores.md#select-a-custom-metastore-during-cluster-creation)을 참조 하세요.
* 원본 및 새 클러스터 둘 다에서 액세스할 수 있는 저장소 계정입니다. 허용 되는 보조 저장소 유형에 대 한 HDInsight 및 [저장소 유형 및 기능](../hdinsight-hadoop-compare-storage-options.md#storage-types-and-features) [에 추가 저장소 계정 추가](../hdinsight-hadoop-add-storage.md) 를 참조 하세요.

    다음은 몇 가지 옵션입니다.
  * 원본 클러스터에 대상 저장소 계정을 추가 합니다.
  * 원본 저장소 계정을 새 클러스터에 추가 합니다.
  * 중간 저장소 계정을 원래 클러스터와 새 클러스터에 추가 합니다.

## <a name="how-it-works"></a>작동 방법

원래 클러스터에서 지정 된 HDFS 디렉터리로 Hive 테이블을 내보내는 스크립트 작업을 실행 합니다. [실행 중인 클러스터에 대 한 동작 스크립팅을](../hdinsight-hadoop-customize-cluster-linux.md#script-action-to-a-running-cluster)참조 하세요.

그런 다음 새 클러스터에서 다른 스크립트 작업을 실행 하 여 HDFS 디렉터리에서 Hive 테이블을 가져옵니다.

이 스크립트는 새 클러스터의 기본 파일 시스템에 테이블을 다시 만듭니다. 또한 네이티브 테이블은 저장소에 데이터를 복사 합니다. 네이티브가 아닌 테이블은 정의에 의해서만 복사 됩니다. 네이티브가 아닌 테이블에 대 한 자세한 내용은 [Hive 저장소 처리기](https://cwiki.apache.org/confluence/display/Hive/StorageHandlers) 를 참조 하세요.

Hive 웨어하우스 디렉터리에 없는 외부 테이블의 경로는 유지 됩니다. 다른 테이블은 대상 클러스터의 기본 Hive 경로에 복사 됩니다. Hive 속성 `hive.metastore.warehouse.external.dir` 및을 참조 하십시오 `hive.metastore.warehouse.dir` .

스크립트는 대상 클러스터에서 사용자 지정 파일 사용 권한을 유지 하지 않습니다.

> [!NOTE]
>
> 이 가이드에서는 Hive 데이터베이스, 테이블 및 파티션과 관련 된 메타 데이터 개체를 복사 하는 방법을 설명 합니다. 다른 메타 데이터 개체는 수동으로 다시 만들어야 합니다.
>
> * 의 경우 `Views` hive는 `SHOW VIEWS` HDInsight 4.0에서 hive 2.2.0 명령을 지원 합니다. `SHOW CREATE TABLE`뷰 정의에 사용 합니다. 이전 버전의 Hive의 경우 metastore SQL DB를 쿼리하여 뷰를 표시 합니다.
> * 의 경우 `Materialized Views` 명령 `SHOW MATERIALIZED VIEWS` , 및를 사용 `DESCRIBE FORMATTED` `CREATE MATERIALIZED VIEW` 합니다. 자세한 내용은 [구체화 된 뷰](https://cwiki.apache.org/confluence/display/Hive/Materialized+views) 를 참조 하세요.
> * `Constraints`HDInsight 4.0의 Hive 2.1.0에서 지원 되는의 경우를 사용 `DESCRIBE EXTENDED` 하 여 테이블에 대 한 제약 조건을 나열 하 고를 사용 하 여 `ALTER TABLE` 제약 조건을 추가 합니다. 자세한 내용은 [Alter Table 제약 조건](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-AlterTableConstraints) 을 참조 하세요.

## <a name="copy-hive-tables"></a>Hive 테이블 복사

1. 원본 클러스터에서 다음 필드를 사용 하 여 "내보내기" 스크립트 동작을 적용 합니다.

    그러면 중간 Hive 스크립트가 생성 되 고 실행 됩니다. 지정 된에 저장 됩니다 `<hdfs-export-path>` .

    필요에 따라를 사용 `--run-script=false` 하 여 수동으로 실행 하기 전에 사용자 지정 합니다.

    |속성 | 값 |
    |---|---|
    |Bash 스크립트 URI|`https://hdiconfigactions.blob.core.windows.net/linuxhivemigrationv01/export-hive-data-v01.sh`|
    |노드 유형|Head|
    |매개 변수|`<hdfs-export-path>` `--run-script`|

    ```sh
    usage: generate Hive export and import scripts and export Hive data to specified HDFS path
           [--run-script={true,false}]
           hdfs-export-path

    positional arguments:

        hdfs-export-path      remote HDFS directory to write export data to

    optional arguments:
        --run-script={true,false}
                            whether to execute the generated Hive export script
                            (default: true)
    ```

2. 내보내기가 성공적으로 완료 되 면 다음 필드를 사용 하 여 새 클러스터에 "가져오기" 스크립트 작업을 적용 합니다.

    |속성 | 값 |
    |---|---|
    |Bash 스크립트 URI|`https://hdiconfigactions.blob.core.windows.net/linuxhivemigrationv01/import-hive-data-v01.sh`|
    |노드 유형|Head|
    |매개 변수|`<hdfs-export-path>`|

    ```sh
    usage: download Hive import script from specified HDFS path and execute it
           hdfs-export-path

    positional arguments:

      hdfs-export-path      remote HDFS directory to download Hive import script from

    ```

## <a name="verification"></a>확인

각 클러스터의 주 노드에서 루트 사용자로 스크립트를 다운로드 및 실행 [`hive_contents.sh`](https://hdiconfigactions.blob.core.windows.net/linuxhivemigrationv01/hive_contents.sh) 하 고 출력 파일의 내용을 비교 `/tmp/hive_contents.out` 합니다. [SSH를 사용 하 여 HDInsight에 연결 (Apache Hadoop)을](../hdinsight-hadoop-linux-use-ssh-unix.md)참조 하세요.

## <a name="cleanup-additional-storage-usage"></a>추가 저장소 사용 정리

저장소 마이그레이션을 완료 하 고 확인 한 후에는 지정 된 HDFS 내보내기 경로에서 데이터를 삭제할 수 있습니다.

HDFS 명령을 사용 하는 옵션입니다 `hdfs dfs -rm -R` .

## <a name="option-reduce-additional-storage-usage"></a>옵션: 추가 저장소 사용량을 줄입니다.

스크립트 내보내기 작업은 Hive로 인 한 저장소 사용량을 두 배로 증가 시킬 수 있습니다. 그러나 한 번에 하나의 데이터베이스 또는 테이블을 수동으로 마이그레이션하여 추가 저장소 사용량을 제한할 수 있습니다.

1. `--run-script=false`생성 된 Hive 스크립트의 실행을 건너뛰도록 지정 합니다. Hive 내보내기 및 가져오기 스크립트는 내보내기 경로에 계속 저장 됩니다.

2. Hive 내보내기 및 스크립트 데이터베이스 또는 테이블에 대 한 스크립트의 조각을 실행 하 여 마이그레이션된 각 데이터베이스 또는 테이블 뒤에 내보내기 경로를 수동으로 정리 합니다.

## <a name="next-steps"></a>다음 단계

* [Azure Data Lake Storage Gen2](../hdinsight-hadoop-use-data-lake-storage-gen2.md)
* [외부 메타데이터 저장소 사용](../hdinsight-use-external-metadata-stores.md#select-a-custom-metastore-during-cluster-creation)
* [저장소 유형 및 기능](../hdinsight-hadoop-compare-storage-options.md#storage-types-and-features)
* [실행 중인 클러스터에 대 한 작업 스크립팅](../hdinsight-hadoop-customize-cluster-linux.md#script-action-to-a-running-cluster)
