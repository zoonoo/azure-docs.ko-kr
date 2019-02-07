---
title: Azure Database for PostgreSQL의 읽기 복제본
description: 이 문서에서는 Azure Database for PostgreSQL의 읽기 복제본에 대해 설명합니다.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/23/2019
ms.openlocfilehash: 9270c3290bd7be0bbb79d30aff8becc04dcfc603
ms.sourcegitcommit: 644de9305293600faf9c7dad951bfeee334f0ba3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/25/2019
ms.locfileid: "54904015"
---
# <a name="read-replicas-in-azure-database-for-postgresql"></a>Azure Database for PostgreSQL의 읽기 복제본

> [!IMPORTANT]
> 읽기 복제본 기능은 공개 미리 보기로 있습니다.

읽기 복제본 기능을 사용하면 동일한 Azure 지역 내에서 Azure Database for PostgreSQL 서버(마스터)의 데이터를 최대 5개의 읽기 전용 서버(읽기 복제본)로 복제할 수 있습니다. 읽기 복제본은 PostgreSQL 엔진의 기본 복제 기술을 사용하여 비동기적으로 업데이트됩니다.

복제본은 일반적인 독립형 Azure Database for PostgreSQL 서버와 비슷한 방식으로 관리할 수 있는 새 서버입니다. 각 읽기 복제본의 경우 vCore에서 프로비전된 컴퓨팅 및 프로비전된 스토리지에 대한 요금이 GB/월 단위로 청구됩니다.

## <a name="when-to-use-read-replicas"></a>읽기 복제본을 사용하면 적절한 경우
읽기 복제본 기능은 읽기 집약적 워크로드의 성능 및 규모를 향상시키는 데 도움을 주기 위한 것입니다. 예를 들어, 읽기 워크로드는 복제본으로 분리될 수 있으며 쓰기 워크로드를 마스터로 보내질 수 있습니다.

일반적인 시나리오는 BI와 분석 워크로드가 보고를 위한 데이터 원본으로 읽기 복제본을 사용하도록 합니다.

복제본은 읽기 전용이므로 마스터의 쓰기 용량 부담을 직접적으로 완화시키지 않으며, 이 기능은 쓰기 집약적 워크로드를 대상으로 하지 않습니다.

읽기 복제본 기능은 PostgreSQL의 비동기 복제를 사용하므로, 동기 복제 시나리오에 적용되지 않습니다. 마스터와 복제본 간에는 측정 가능한 지연이 발생합니다. 복제본의 데이터는 결과적으로 마스터의 데이터와 일치하게 됩니다. 이러한 지연 시간을 수용할 수 있는 워크로드에 이 기능을 사용합니다.

## <a name="creating-a-replica"></a>복제본 만들기
마스터 서버에서 **azure.replication_support**를 REPLICA로 설정해야 합니다. 이 매개 변수 변경 내용을 적용하려면 서버를 다시 시작해야 합니다. **Azure.replication_support** 매개 변수는 범용 및 메모리 최적화 계층에만 적용됩니다.

복제본 만들기 워크플로를 시작하면 빈 Azure Database for PostgreSQL 서버가 만들어집니다. 새 서버는 마스터 서버에 있는 데이터로 채워집니다. 새 복제본을 만드는 데 걸리는 시간은 마스터의 데이터 양과 마지막 주별 전체 백업 이후의 시간에 따라 달라집니다. 이 시간은 몇 분에서 몇 시간까지 될 수 있습니다.

읽기 복제본 기능은 PostgreSQL의 물리적 복제(논리 복제 아님)를 사용합니다. 복제 슬롯을 사용하는 복제 스트리밍이 기본 작업 모드입니다. 필요한 경우 후속 작업에 로그 전달이 사용됩니다.

> [!NOTE]
> 권장된 대로 서버에서 스토리지 경고를 아직 설정하지 않은 경우 서버가 스토리지 한도에 가까워질 경우 복제에 영향을 미치므로 알림이 표시됩니다.

[Azure Portal에서 읽기 복제본을 만드는 방법을 알아봅니다](howto-read-replicas-portal.md).

## <a name="connecting-to-a-replica"></a>복제본에 연결
복제본을 만들면 마스터 서버의 방화벽 규칙 또는 VNet 서비스 엔드포인트를 상속하지 않습니다. 이러한 규칙은 복제본에 대해 별도로 설정해야 합니다.

복제본은 마스터 서버에서 해당 관리자 계정을 상속합니다. 마스터 서버의 모든 사용자 계정은 읽기 복제본으로 복제됩니다. 마스터 서버에서 사용 가능한 사용자 계정을 사용하여 읽기 복제본에만 연결할 수 있습니다.

일반 Azure Database for PostgreSQL 서버에서와 같이 해당 호스트 이름 및 유효한 사용자 계정을 사용하여 복제본에 연결할 수 있습니다. 예를 들어, 서버 이름이 myreplica이고 관리 사용자 이름이 myadmin인 경우 다음과 같이 psql에서 연결할 수 있습니다.

```
psql -h myreplica.postgres.database.azure.com -U myadmin@myreplica -d postgres
```
메시지가 표시되면 사용자 계정의 암호를 입력합니다.

## <a name="monitoring-replication"></a>복제 모니터링
Azure Monitor에서 사용할 수 있는 **복제본 간 최대 지연 시간** 메트릭이 있습니다. 이 메트릭은 마스터 서버에서만 사용할 수 있습니다. 이 메트릭은 마스터와 가장 느리게 지연 복제본 간의 지연 시간을 보여 줍니다.다. 

Azure Monitor에서 **복제본 지연 시간** 메트릭도 제공합니다. 이 메트릭은 복제본에만 사용할 수 있습니다. 

이 메트릭은 pg_stat_wal_receiver 뷰에서 계산됩니다.

```SQL
EXTRACT (EPOCH FROM now() - pg_last_xact_replay_timestamp())
```
복제본 지연 시간 메트릭은 마지막으로 재생된 트랜잭션 이후의 시간을 보여 줍니다. 마스터에서 트랜잭션이 발생하지 않으면 이 메트릭은 이 지연 시간을 반영합니다.

복제본 지연 시간이 워크로드에 적합하지 않은 값에 도달할 때 사용자에게 알려 주는 경고를 설정하는 것이 좋습니다. 

추가 인사이트를 얻으려면 마스터 서버를 직접 쿼리하여 모든 복제본의 복제 지연 시간(바이트)을 가져옵니다.
PG 10:
```SQL
select pg_wal_lsn_diff(pg_current_wal_lsn(), stat.replay_lsn) 
AS total_log_delay_in_bytes from pg_stat_replication;
```

PG 9.6 및 이전:
```SQL
select pg_xlog_location_diff(pg_current_xlog_location(), stat.replay_location) 
AS total_log_delay_in_bytes from pg_stat_replication;
```

> [!NOTE]
> 마스터 또는 복제본 서버가 다시 시작되면 다시 시작한 후 따라잡는 데 소요되는 시간이 복제본 지연 시간 메트릭에 반영됩니다.

## <a name="stopping-replication-to-a-replica"></a>복제본으로의 복제 중지
마스터 및 복제본 간의 복제를 중지하도록 선택할 수 있습니다. 이렇게 하면 복제본이 다시 시작되고 해당 복제 설정이 제거됩니다. 마스터 서버와 복제본 서버 간의 복제가 중지되면 복제본 서버는 독립형 서버가 됩니다. 독립형 서버의 데이터는 복제 중지 명령이 시작될 때 복제본에서 사용할 수 있던 데이터입니다. 독립형 서버는 마스터 서버를 따르지 않습니다.

이 서버를 다시 복제본으로 설정할 수는 없습니다.

복제를 중지하기 전에 복제본에 필요한 모든 데이터가 있는지 확인합니다.

[방법 문서에서 복제본을 중지하는 방법을 알아볼 수 있습니다](howto-read-replicas-portal.md).


## <a name="considerations"></a>고려 사항

### <a name="preparing-for-replica"></a>복제본 준비
복제본을 만들려면 먼저 마스터 서버에서 **azure.replication_support**를 REPLICA로 설정해야 합니다. 이 매개 변수 변경 내용을 적용하려면 서버를 다시 시작해야 합니다. 이 매개 변수는 범용 및 메모리 최적화 계층에만 적용됩니다.

### <a name="stopped-replicas"></a>중지된 복제본
마스터 및 복제본 간의 복제를 중지하도록 선택한 경우 이러한 변경 내용을 적용하기 위해 복제본이 다시 시작됩니다. 그 이후에는 다시 복제본으로 설정할 수 없습니다.

### <a name="replicas-are-new-servers"></a>복제본은 새로운 서버임
복제본은 새로운 Azure Database for PostgreSQL 서버로 생성됩니다. 기존 서버는 복제본으로 만들 수 없습니다.

### <a name="replica-server-configuration"></a>복제본 서버 구성
복제본 서버는 다음 구성을 포함하여 마스터와 동일한 서버 구성으로 생성됩니다.
- 가격 책정 계층 
- 계산 세대
- vCore 수
- Storage
- Backup 보존 기간
- 백업 중복 옵션
- PostgreSQL 엔진 버전

복제본을 만든 후에는 마스터 서버와는 별도로 가격 책정 계층(기본 가격 책정 계층에서 이 계층으로 또는 그 반대로 변경하는 경우는 제외), 계산 생성, vCores, 저장소 및 백업 보존 기간을 변경할 수 있습니다.

> [!IMPORTANT]
> 마스터 서버 구성을 새 값으로 업데이트하기 전에 복제본의 구성을 같거나 더 큰 값으로 업데이트해야 합니다. 위와 같이 업데이트하면 마스터 변경 내용을 복제본에도 유지할 수 있습니다.

특히, Postgres에서는 매개 변수 max_connections의 복제본 서버 값이 마스터의 값보다 크거나 같아야 합니다. 그렇지 않으면 복제본이 시작되지 않습니다. Azure Database for PostgreSQL에서 max_connections 값은 SKU에 따라 설정됩니다. 자세한 내용은 [한도 문서](concepts-limits.md)를 참조하세요. 

이 원칙을 위반하는 업데이트를 수행하려고 하면 오류가 발생합니다.


### <a name="deleting-the-master"></a>마스터 삭제
마스터 서버를 삭제하면 모든 읽기 복제본이 독립 실행형 서버가 됩니다. 이 변경 내용을 적용하기 위해 복제본이 다시 시작됩니다.

### <a name="other"></a>기타
- 읽기 복제본은 마스터와 동일한 Azure 지역에서만 만들 수 있습니다.
- 복제본의 복제본 만들기는 지원되지 않습니다.

## <a name="next-steps"></a>다음 단계
- [Azure Portal에서 읽기 복제본을 만들고 관리하는 방법](howto-read-replicas-portal.md).
