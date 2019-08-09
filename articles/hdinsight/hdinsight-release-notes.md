---
title: Azure HDInsight 릴리스 정보
description: Azure HDInsight에 대한 최신 릴리스 정보입니다. Hadoop, Spark, R Server, Hive 등에 대 한 개발 팁 및 세부 정보를 확인 하세요.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/08/2019
ms.openlocfilehash: b6e9a340a1fdcbe3ee24b8c81d171ade04c63139
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/09/2019
ms.locfileid: "68880028"
---
# <a name="release-notes-for-azure-hdinsight"></a>Azure HDInsight 릴리스 정보

이 문서에서는 **가장 최근** Azure HDInsight 릴리스 업데이트에 대한 정보를 제공합니다. 이전 릴리스에 대한 자세한 내용은 [HDInsight 릴리스 정보 보관](hdinsight-release-notes-archive.md)을 참조하세요.

> [!IMPORTANT]  
> Linux는 HDInsight 버전 3.4 이상에서 사용되는 유일한 운영 체제입니다. 자세한 내용은 [HDInsight 버전 관리 문서](hdinsight-component-versioning.md)를 참조하세요.

## <a name="summary"></a>요약

Azure HDInsight는 Azure에서 오픈 소스 Apache Hadoop 및 Apache Spark 분석을 위해 기업 고객들 사이에서 가장 인기 있는 서비스 중 하나입니다.

## <a name="new-features"></a>새 기능

HDInsight 4.0의 중요 한 변경 내용에 대 한 자세한 내용은 [HDI 4.0의 새로운 기능](../hdinsight/hdinsight-version-release.md)을 참조 하세요.

## <a name="component-versions"></a>구성 요소 버전

모든 HDInsight 4.0 구성 요소의 공식 Apache 버전은 아래에서 제공 됩니다. 나열 된 구성 요소는 사용 가능한 최신 안정적인 버전의 릴리스입니다.

- Apache Ambari 2.7.1
- Apache Hadoop 3.1.1
- Apache HBase 2.0.0
- Apache Hive 3.1.0
- Apache Kafka 1.1.1, 2.1.0
- Apache Mahout 0.9.0 이상
- Apache Oozie 4.2.0
- Apache Phoenix 4.7.0
- Apache Pig 0.16.0
- Apache Ranger 0.7.0
- Apache Slider 0.92.0
- Apache Spark 2.3.1, 2.4.0
- Apache Sqoop 1.4.7
- Apache TEZ 0.9.1
- Apache Zeppelin 0.8.0부터
- Apache ZooKeeper 3.4.6

이후 버전의 Apache 구성 요소는 위에 나열 된 버전 외에도 HDP 배포에 번들로 제공 됩니다. 이 경우 이러한 이후 버전은 기술 미리 보기 표에 나열되어 있으며, 프로덕션 환경에서 위 목록의 Apache 구성 요소 버전을 대체하지 않아야 합니다.

## <a name="apache-patch-information"></a>Apache 패치 정보

HDInsight 4.0에서 사용할 수 있는 패치에 대 한 자세한 내용은 아래 표에 나와 있는 각 제품의 패치 목록을 참조 하십시오.

| 제품 이름 | 패치 정보 |
|---|---|
| Ambari | [Ambari 패치 정보](https://docs.hortonworks.com/HDPDocuments/Ambari-2.7.1.0/bk_ambari-release-notes/content/ambari_relnotes-2.7.1.0-patch-information.html) |
| Hadoop | [Hadoop 패치 정보](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_hadoop.html) |
| Hbase | [HBase 패치 정보](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_hbase.html) |
| Hive  | 이 릴리스는 추가 Apache 패치 없이 Hive 3.1.0 제공 합니다.  |
| Kafka | 이 릴리스는 추가 Apache 패치 없이 Kafka 1.1.1을 제공 합니다. |
| Oozie | [Oozie 패치 정보](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_oozie.html) |
| Phoenix | [Phoenix 패치 정보](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_phoenix.html) |
| Pig | [Pig 패치 정보](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_pig.html) |
| Ranger | [레인저 패치 정보](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_ranger.html) |
| Spark | [Spark 패치 정보](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_spark.html) |
| Sqoop | 이 릴리스에서는 추가 Apache 패치 없이 Sqoop 1.4.7을 제공 합니다. |
| Tez | 이 릴리스는 추가 Apache 패치 없이 Tez 0.9.1를 제공 합니다. |
| Zeppelin | 이 릴리스는 추가 Apache 패치 없이 Zeppelin 0.8.0부터을 제공 합니다. |
| Zookeeper | [사육 사 패치 정보](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_zookeeper.html) |

## <a name="fixed-common-vulnerabilities-and-exposures"></a>수정된 일반 취약성 및 노출

이 릴리스에서 해결 된 보안 문제에 대 한 자세한 내용은 Hortonworks [의 수정 된 일반적인 취약점 및 HDP 3.0.1에 대 한 노출](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/cve.html)을 참조 하세요.

## <a name="known-issues"></a>알려진 문제

### <a name="replication-is-broken-for-secure-hbase-with-default-installation"></a>기본 설치를 사용 하는 Secure HBase에 대해 복제가 중단 됨

HDInsight 4.0의 경우 다음 단계를 수행 합니다.

1. 클러스터 간 통신을 사용 하도록 설정 합니다.
1. 활성 헤드 노드에 로그인 합니다.
1. 다음 명령을 사용 하 여 복제를 사용 하도록 설정 하는 스크립트를 다운로드 합니다.

    ```
    sudo wget https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_enable_replication.sh
    ```
1. 명령을 `sudo kinit <domainuser>`입력 합니다.
1. 다음 명령을 입력 하 여 스크립트를 실행 합니다.

    ```
    sudo bash hdi_enable_replication.sh -m <hn0> -s <srclusterdns> -d <dstclusterdns> -sp <srcclusterpasswd> -dp <dstclusterpasswd> -copydata
    ```
HDInsight 3.6의 경우 다음을 수행 합니다.

1. 활성 HMaster ZK에 로그인 합니다.
1. 다음 명령을 사용 하 여 복제를 사용 하도록 설정 하는 스크립트를 다운로드 합니다.
    ```
    sudo wget https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_enable_replication.sh
    ```
1. 명령을 `sudo kinit -k -t /etc/security/keytabs/hbase.service.keytab hbase/<FQDN>@<DOMAIN>`입력 합니다.
1. 다음 명령을 입력합니다.

    ```bash
    sudo bash hdi_enable_replication.sh -s <srclusterdns> -d <dstclusterdns> -sp <srcclusterpasswd> -dp <dstclusterpasswd> -copydata
    ```

### <a name="phoenix-sqlline-stops-working-after-migrating-hbase-cluster-to-hdinsight-40"></a>HBase 클러스터를 HDInsight 4.0로 마이그레이션한 후 Phoenix Sqlline 작동이 중지 됨

다음 단계를 수행합니다.

1. 다음 Phoenix 테이블을 삭제 합니다.
    1. `SYSTEM.FUNCTION`
    1. `SYSTEM.SEQUENCE`
    1. `SYSTEM.STATS`
    1. `SYSTEM.MUTEX`
    1. `SYSTEM.CATALOG`
1. 테이블을 삭제할 수 없는 경우 HBase를 다시 시작 하 여 테이블에 대 한 모든 연결을 지우십시오.
1. `sqlline.py`을 다시 실행합니다. Phoenix는 1 단계에서 삭제 된 모든 테이블을 다시 만듭니다.
1. HBase 데이터에 대해 Phoenix 테이블 및 뷰를 다시 생성 합니다.

### <a name="phoenix-sqlline-stops-working-after-replicating-hbase-phoenix-metadata-from-hdinsight-36-to-40"></a>HDInsight 3.6에서 4.0로 HBase Phoenix 메타 데이터를 복제 한 후 Phoenix Sqlline 작동이 중지 됨

다음 단계를 수행합니다.

1. 복제를 수행 하기 전에 대상 4.0 클러스터로 이동 하 여를 `sqlline.py`실행 합니다. 이 명령은 4.0에만 있는와 `SYSTEM.MUTEX` `SYSTEM.LOG` 같은 Phoenix 테이블을 생성 합니다.
1. 다음 테이블을 삭제 합니다.
    1. `SYSTEM.FUNCTION`
    1. `SYSTEM.SEQUENCE`
    1. `SYSTEM.STATS`
    1. `SYSTEM.CATALOG`
1. HBase 복제 시작

## <a name="deprecation"></a>사용 중단

Apache Storm 및 ML 서비스는 HDInsight 4.0에서 사용할 수 없습니다.
