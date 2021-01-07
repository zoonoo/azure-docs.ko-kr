---
title: Azure HDInsight 클러스터에서 Apache Hive 복제를 사용 하는 방법
description: HDInsight 클러스터에서 Hive 복제를 사용 하 여 Hive metastore 및 Azure Data Lake Storage Gen 2 Data Lake를 복제 하는 방법에 대해 알아봅니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/08/2020
ms.openlocfilehash: af74392b3368a25e5d238f774292c80de5f91c65
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91857744"
---
# <a name="how-to-use-apache-hive-replication-in-azure-hdinsight-clusters"></a>Azure HDInsight 클러스터에서 Apache Hive 복제를 사용 하는 방법

데이터베이스 및 웨어하우스의 컨텍스트에서 복제는 한 웨어하우스에서 다른 웨어하우스에서 엔터티를 복제 하는 프로세스입니다. 중복은 전체 데이터베이스 또는 테이블이 나 파티션과 같은 더 작은 수준에 적용 될 수 있습니다. 목표는 기본 엔터티가 변경 될 때마다 복제본을 변경 하는 것입니다. Apache Hive의 복제는 재해 복구를 중심으로 하 고 단방향 기본 복사 복제를 제공 합니다. HDInsight 클러스터에서 Hive 복제를 사용 하 여 Hive metastore 및 Azure Data Lake Storage Gen2에 연결 된 기본 data lake를 unidirectionally 복제할 수 있습니다.  

Hive 복제는 더 나은 기능을 제공 하 고 더 빠르고 리소스를 적게 사용 하는 최신 버전을 사용 하 여 몇 년 동안 진화 했습니다. 이 문서에서는 HDInsight 3.6 및 HDInsight 4.0 클러스터 유형 둘 다에서 지원 되는 Hive 복제 (Replv2)에 대해 설명 합니다.

## <a name="advantages-of-replv2"></a>Replv2의 이점

Hive [ReplicationV2](https://cwiki.apache.org/confluence/display/Hive/HiveReplicationv2Development) 또는 (Replv2) hive [가져오기-내보내기를](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ImportExport)사용 하는 첫 번째 버전의 hive 복제에 비해 다음과 같은 이점이 있습니다.

- 이벤트 기반 증분 복제
- 특정 시점 복제  
- 대역폭 요구 사항 감소  
- 중간 복사본 수 감소  
- 복제 상태가 유지 됨
- 제한 된 복제  
- 허브 및 스포크 모델에 대 한 지원  
- ACID 테이블 지원 (HDInsight 4.0)

## <a name="replication-phases"></a>복제 단계

Hive 이벤트 기반 복제는 기본 클러스터와 보조 클러스터 간에 구성 됩니다. 이 복제는 부트스트래핑 및 증분 실행의 두 단계로 구성 됩니다.

### <a name="bootstrapping"></a>부트스트래핑

부트스트래핑은 한 번 실행 하 여 데이터베이스의 기본 상태를 기본에서 보조로 복제 합니다. 필요에 따라 복제를 사용 하도록 설정 해야 하는 대상 데이터베이스에 테이블의 하위 집합을 포함 하도록 부트스트래핑을 구성할 수 있습니다.

### <a name="incremental-runs"></a>증분 실행

부트스트랩 후에는 증분 실행이 주 클러스터에서 자동으로 실행 되 고 이러한 증분 실행 중에 생성 된 이벤트는 보조 클러스터에서 다시 재생 됩니다. 보조 클러스터가 기본 클러스터를 사용 하 여 작업 하는 경우 보조 복제본은 주 이벤트와 일치 하 게 됩니다.

## <a name="replication-commands"></a>복제 명령

Hive는,, 등의 REPL 명령 집합을 제공 `DUMP` `LOAD` 하 여 `STATUS` 이벤트 흐름을 오케스트레이션 합니다. `DUMP`명령은 기본 클러스터에 있는 모든 DDL/DML 이벤트의 로컬 로그를 생성 합니다. `LOAD`명령은 추출 된 복제 덤프 출력에 기록 된 데이터 및 데이터를 지연 복사 하 고 대상 클러스터에서 실행 하는 방법입니다. `STATUS`명령은 대상 클러스터에서 실행 되어 가장 최근의 복제 로드가 성공적으로 복제 된 최신 이벤트 ID를 제공 합니다.

### <a name="set-replication-source"></a>복제 원본 설정

복제를 시작 하기 전에 복제할 데이터베이스가 복제 원본으로 설정 되어 있는지 확인 합니다. 명령을 사용 하 여 `DESC DATABASE EXTENDED <db_name>` 매개 변수가 `repl.source.for` 정책 이름으로 설정 되었는지 확인할 수 있습니다.

정책이 예약 되 고 `repl.source.for` 매개 변수가 설정 되지 않은 경우 먼저를 사용 하 여이 매개 변수를 설정 해야 `ALTER DATABASE <db_name> SET DBPROPERTIES ('repl.source.for'='<policy_name>')` 합니다.

```sql
ALTER DATABASE tpcds_orc SET DBPROPERTIES ('repl.source.for'='replpolicy1') 
```

### <a name="dump-metadata-to-the-data-lake"></a>Data lake에 메타 데이터 덤프

`REPL DUMP [database name]. => location / event_id`명령은 부트스트랩 단계에서 관련 메타 데이터를 Azure Data Lake Storage Gen2에 덤프 하는 데 사용 됩니다. 는 `event_id` 관련 메타 데이터를 Azure Data Lake Storage Gen2에 배치 하는 최소 이벤트를 지정 합니다. 
 
```sql
repl dump tpcds_orc; 
```
예제 출력:

| dump_dir|last_repl_id
|-|-|
|/tmp/hive/repl/38896729-67d5-41b2-90dc-46eeed4c5dd0|2925|

### <a name="load-data-to-the-target-cluster"></a>대상 클러스터에 데이터 로드

`REPL LOAD [database name] FROM [ location ] { WITH ( ‘key1’=‘value1’{, ‘key2’=‘value2’} ) }`명령은 부트스트랩 및 복제의 증분 단계 모두에 대해 대상 클러스터에 데이터를 로드 하는 데 사용 됩니다. 는 `[database name]` 대상 클러스터의 소스 또는 다른 이름과 동일할 수 있습니다. 는 `[location]` 이전 명령의 출력에서 위치를 나타냅니다 `REPL DUMP` . 이는 대상 클러스터가 원본 클러스터와 통신할 수 있어야 함을 의미 합니다. `WITH`이 절은 주로 복제를 허용 하는 대상 클러스터의 다시 시작을 방지 하기 위해 추가 되었습니다.

```sql
repl load tpcds_orc from '/tmp/hive/repl/38896729-67d5-41b2-90dc-46eeed4c5dd0'; 
```

### <a name="output-the-last-replicated-event-id"></a>마지막 복제 이벤트 ID를 출력 합니다.

`REPL STATUS [database name]`명령은 대상 클러스터에서 실행 되며 마지막으로 복제 된를 출력 합니다 `event_id` . 또한 사용자는이 명령을 사용 하 여 대상 클러스터가 복제 된 상태를 확인할 수 있습니다. 이 명령의 출력을 사용 하 여 `REPL DUMP` 증분 복제에 대 한 다음 명령을 생성할 수 있습니다.

```sql
repl status tpcds_orc;
```

예제 출력:

|last_repl_id|
|-|
|2925|

### <a name="dump-relevant-data-and-metadata-to-the-data-lake"></a>Data lake에 관련 데이터 및 메타 데이터 덤프

`REPL DUMP [database name] FROM [event-id] { TO [event-id] } { LIMIT [number of events] }`명령은 Azure Data Lake Storage에 관련 메타 데이터 및 데이터를 덤프 하는 데 사용 됩니다. 이 명령은 증분 단계에서 사용 되며 소스 웨어하우스에서 실행 됩니다. 는 `FROM [event-id]` 증분 단계에 필요 하며,의 값은 `event-id` 대상 웨어하우스에서 명령을 실행 하 여 파생 될 수 있습니다 `REPL STATUS [database name]` .

```sql
repl dump tpcds_orc from 2925;
```

예제 출력:

|dump_dir|last_repl_id|
|-|-|
| /tmp/hive/repl/38896729-67d5-41b2-90dc-466466agadd0 | 2960|

## <a name="hive-replication-process"></a>Hive 복제 프로세스

다음 단계는 Hive 복제 프로세스 중에 발생 하는 순차적 이벤트입니다.

1. 복제할 테이블이 특정 정책에 대 한 복제 원본으로 설정 되어 있는지 확인 합니다.

1. `REPL_DUMP`이 명령은 데이터베이스 이름, 이벤트 ID 범위 및 Azure Data Lake Storage Gen2 저장소 URL과 같은 관련 제약 조건을 사용 하 여 주 클러스터에 실행 됩니다.

1. 시스템은 metastore에서 추적 된 모든 이벤트의 덤프를 최신으로 직렬화 합니다. 이 덤프는에 지정 된 URL에서 주 클러스터의 Azure Data Lake Storage Gen2 저장소 계정에 저장 됩니다 `REPL_DUMP` .  

1. 주 클러스터는 기본 클러스터의 Azure Data Lake Storage Gen2 저장소에 복제 메타 데이터를 유지 합니다. 이 경로는 Ambari의 Hive 구성 UI에서 구성할 수 있습니다. 이 프로세스에서는 메타 데이터가 저장 된 경로와 최신 추적 된 DML/DDL 이벤트의 ID를 제공 합니다.

1. `REPL_LOAD`명령은 보조 클러스터에서 실행 됩니다. 명령은 3 단계에서 구성 된 경로를 가리킵니다.

1. 보조 클러스터는 3 단계에서 만든 추적 된 이벤트를 사용 하 여 메타 데이터 파일을 읽습니다. 보조 클러스터에서 추적 된 이벤트가 저장 되는 기본 클러스터의 Azure Data Lake Storage Gen2 저장소에 대 한 네트워크 연결이 있는지 확인 `REPL_DUMP` 합니다.  

1. 보조 클러스터는 distributed copy () 계산을 생성 합니다 `DistCP` .

1. 보조 클러스터는 기본 클러스터 저장소에서 데이터를 복사 합니다.  

1. 보조 클러스터의 metastore 업데이트 됩니다.  

1. 마지막 추적 된 이벤트 ID는 기본 metastore에 저장 됩니다.

증분 복제는 동일한 프로세스를 따르고 마지막으로 복제 된 이벤트 ID를 입력으로 사용 해야 합니다. 마지막 복제 이벤트 이후 증분 복사본으로 이어집니다. 증분 복제는 일반적으로 필수 RPO (복구 지점 목표)를 달성 하기 위해 미리 결정 된 빈도로 자동으로 자동화 됩니다.

:::image type="content" source="media/apache-hive-replication/hive-replication-diagram.png" alt-text="Hive 복제 다이어그램":::

## <a name="replication-patterns"></a>복제 패턴  

복제는 일반적으로 주 데이터베이스와 보조 데이터베이스 간에 단방향 방식으로 구성 됩니다. 여기서 기본 맞춘는 읽기 및 쓰기 요청입니다. 보조 클러스터는 읽기 요청만 맞춘. 재해가 발생 하는 경우 보조 복제본에 대 한 쓰기를 허용 하지만 역방향 복제를 주 복제본으로 다시 구성 해야 합니다.

:::image type="content" source="media/apache-hive-replication/replication-pattern.png" alt-text="Hive 복제 다이어그램":::

주 – 보조, 허브 및 스포크, Relay를 비롯 한 Hive 복제에 적합 한 여러 가지 패턴이 있습니다.

HDInsight 활성 주 – 대기 보조는 일반적인 BCDR (비즈니스 연속성 및 재해 복구) 패턴이 며 HiveReplicationV2는 VNet 피어 링을 사용 하 여 지역적으로 구분 된 HDInsight Hadoop 클러스터와 함께이 패턴을 사용할 수 있습니다. 두 클러스터에 대 한 공통 가상 컴퓨터 피어 링를 사용 하 여 복제 자동화 스크립트를 호스트할 수 있습니다. 가능한 HDInsight BCDR 패턴에 대 한 자세한 내용은 [hdinsight 비즈니스 연속성 설명서](../hdinsight-business-continuity.md)를 참조 하세요.  

### <a name="hive-replication-with-enterprise-security-package"></a>Enterprise Security Package를 사용한 Hive 복제  

Enterprise Security Package를 사용 하 여 HDInsight Hadoop 클러스터에서 Hive 복제가 계획 된 경우에는 AD DS (레인저 metastore and Azure Active Directory Domain Services)에 대 한 복제 메커니즘을 고려해 야 합니다.  

Azure AD DS 복제본 집합 기능을 사용 하 여 여러 지역에서 Azure AD 테 넌 트 당 Azure AD DS 복제본 집합을 두 개 이상 만들 수 있습니다. 각 개별 복제본 집합은 해당 지역에서 HDInsight Vnet로 피어 링 되어야 합니다. 이 구성에서는 Azure AD DS 복제를 사용 하 여 구성, 사용자 id 및 자격 증명, 그룹, 그룹 정책 개체, 컴퓨터 개체 및 기타 변경 내용을 비롯 하 여 Azure AD DS에 대 한 변경 내용이 관리 되는 도메인의 모든 복제본 집합에 적용 됩니다.
  
레인저 Import-Export 기능을 사용 하 여 주 데이터베이스에서 보조 데이터베이스로 정기적으로 백업 하 고 복제할 수 있습니다. 보조 클러스터에서 구현 하려는 권한 부여 수준에 따라 모든 또는 모든 레인저 정책의 하위 집합을 복제 하도록 선택할 수 있습니다.  

## <a name="sample-code"></a>예제 코드  

다음 코드 시퀀스는 이라는 예제 테이블에서 부트스트래핑 및 증분 복제를 구현 하는 방법을 보여 줍니다 `tpcds_orc` .  

1. 테이블을 복제 정책의 원본으로 설정 합니다.

   ```sql
   ALTER DATABASE tpcds_orc SET DBPROPERTIES ('repl.source.   for'='replpolicy1');
   ```

1. 기본 클러스터의 부트스트랩 덤프입니다.

   ```sql
   repl dump tpcds_orc with ('hive.repl.rootdir'='/tmpag/hiveag/replag'); 
   ```
   
   예제 출력:
   
   |dump_dir|last_repl_id|
   |-|-|
   |/tmpag/hiveag/replag/675d1bea-2361-4cad-bcbf-8680d305a27a|2925|
 
1. 보조 클러스터에서 부트스트랩 로드 

   ```sql
   repl load tpcds_orc from '/tmpag/hiveag/replag 675d1bea-2361-4cad-bcbf-8680d305a27a'; 
   ```

1. `REPL`보조 클러스터의 상태를 확인 합니다.

   ```sql
   repl status tpcds_orc; 
   ```
 
   |last_repl_id|
   |-|
   |2925|

1. 주 클러스터의 증분 덤프.

   ```sql
   repl dump tpcds_orc from 2925 with ('hive.repl.rootdir'='/tmpag/hiveag/ replag');
   ```

   예제 출력:
   
   |dump_dir | last_repl_id|
   |-|-|
   |/tmpag/hiveag/replag/31177ff7-a40f-4f67-a613-3b64ebe3bb31|2960|

1. 보조 클러스터의 증분 로드  

   ```sql
   repl load tpcds_orc from '/tmpag/hiveag/replag/31177ff7-a40f-4f67-a613-3b64ebe3bb31';
   ```

1. `REPL`보조 클러스터의 상태를 확인 합니다.

   ```sql
   repl status tpcds_orc;
   ```

   |last_repl_id|
   |-|
   |2960|

## <a name="next-steps"></a>다음 단계

이 문서에서 설명 하는 항목에 대 한 자세한 내용은 다음을 참조 하세요.

- [Azure HDInsight 비즈니스 연속성](../hdinsight-business-continuity.md)
- [Azure HDInsight 비즈니스 연속성 아키텍처](../hdinsight-business-continuity-architecture.md)
- [Azure HDInsight 항상 사용 가능한 솔루션 아키텍처 사례 연구](../hdinsight-high-availability-case-study.md)
- [Azure HDInsight의 Apache Hive 및 HiveQL 무엇 인가요?](../hadoop/hdinsight-use-hive.md)