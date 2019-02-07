---
title: Azure Database for PostgreSQL 서버에서 자동 진공 최적화
description: 이 문서에서는 Azure Database for PostgreSQL 서버에서 자동 진공을 최적화하는 방법을 설명합니다.
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/22/2018
ms.openlocfilehash: 21ac48ff473dcf494f96f87210bdfe09e4d82646
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/28/2019
ms.locfileid: "55103397"
---
# <a name="optimizing-autovacuum-on-azure-database-for-postgresql-server"></a>Azure Database for PostgreSQL 서버에서 자동 진공 최적화 
이 문서에서는 Azure Database for PostgreSQL에서 자동 진공을 효과적으로 최적화하는 방법에 대해 설명합니다.

## <a name="overview-of-autovacuum"></a>자동 진공 개요
PostgreSQL은 MVCC를 사용하여 데이터베이스 동시성을 높일 수 있도록 합니다. 모든 업데이트에서 삽입 및 삭제가 수행되고, 모든 삭제에서 행이 삭제되도록 소프트 표시됩니다. 소프트 표시 결과는 나중에 제거할 데드 튜플을 식별합니다. PostgreSQL은 진공 작업을 실행하여 이 모든 것을 달성합니다.

진공 작업은 수동 또는 자동으로 트리거할 수 있습니다. 데이터베이스에서 많은 업데이트 또는 삭제 작업이 발생하는 경우 데드 튜플이 더 많이 있으며, 유휴 상태인 경우에는 더 적습니다.  데이터베이스의 부하가 높은 경우 진공 실행 빈도를 더 높여야 하므로 **수동**으로 진공 작업을 실행하기에는 불편합니다.

자동 진공은 구성할 수 있으며 튜닝의 이점을 활용할 수 있습니다. PostgreSQL에서 함께 제공하는 기본값은 Raspberry Pi를 포함한 모든 종류의 디바이스에서 제품이 작동되는지 확인하려고 하며, 이상적인 구성 값은 다음과 같은 여러 가지 요소에 따라 달라집니다.
- 사용 가능한 총 리소스 크기 - SKU 및 스토리지 크기
- 리소스 사용량
- 개별 개체 특성

## <a name="autovacuum-benefits"></a>자동 진공의 이점
때때로 진공을 실행하지 않으면 누적된 데드 튜플로 인해 다음과 같은 결과가 발생할 수 있습니다.
- 데이터 블로트 - 더 큰 데이터베이스 및 테이블
- 부적절한 인덱스 증가
- I/O 증가

## <a name="monitoring-bloat-with-autovacuum-queries"></a>자동 진공 쿼리를 사용하여 블로트 모니터링
“XYZ”라는 테이블에 있는 데드 및 라이브 튜플의 수를 식별하도록 설계된 샘플 쿼리는 다음과 같습니다. 'SELECT relname, n_dead_tup, n_live_tup, (n_dead_tup/ n_live_tup) AS DeadTuplesRatio, last_vacuum, last_autovacuum FROM pg_catalog.pg_stat_all_tables WHERE relname = 'XYZ' order by n_dead_tup DESC;'

## <a name="autovacuum-configurations"></a>자동 진공 구성
자동 진공을 제어하는 구성 매개 변수는 다음 두 가지 주요 질문을 중심으로 이루어집니다.
- 언제 시작해야 하나요?
- 시작한 후에 얼마나 정리해야 하나요?

위의 질문에 따라 업데이트할 수 있는 자동 진공 구성 매개 변수 중 일부 및 몇 가지 지침은 다음과 같습니다.
매개 변수|설명|기본값
---|---|---
autovacuum_vacuum_threshold|한 테이블에서 VACUUM을 트리거하는 데 필요한 업데이트되거나 또는 삭제되는 트리플의 최소 수를 지정합니다. 기본값은 50개 튜플입니다. 이 매개 변수는 postgresql.conf 파일 또는 서버 명령줄에서만 설정할 수 있습니다. 이 설정은 테이블 스토리지 매개 변수를 변경하여 개별 테이블에 대해 재정의할 수 있습니다.|50
autovacuum_vacuum_scale_factor|VACUUM을 트리거할지 여부를 결정할 때 autovacuum_vacuum_threshold에 추가할 테이블 크기의 비율을 지정합니다. 기본값은 0.2(테이블 크기의 20%)입니다. 이 매개 변수는 postgresql.conf 파일 또는 서버 명령줄에서만 설정할 수 있습니다. 이 설정은 테이블 스토리지 매개 변수를 변경하여 개별 테이블에 대해 재정의할 수 있습니다.|5%
autovacuum_vacuum_cost_limit|자동 VACUUM 작업에 사용할 비용 제한 값을 지정합니다. -1(기본값)이 지정되면 일반적인 vacuum_cost_limit 값이 사용됩니다. 둘 이상의 작업자가 있는 경우 이 값은 실행 중인 자동 진공 작업자 간에 비례적으로 배분됩니다. 각 작업자에 대한 제한의 합계는 이 변수의 값을 초과하지 않습니다. 이 매개 변수는 postgresql.conf 파일 또는 서버 명령줄에서만 설정할 수 있습니다. 이 설정은 테이블 스토리지 매개 변수를 변경하여 개별 테이블에 대해 재정의할 수 있습니다.|-1
autovacuum_vacuum_cost_delay|자동 VACUUM 작업에 사용할 비용 지연 값을 지정합니다. -1이 지정되면 일반적인 vacuum_cost_delay 값이 사용됩니다. 기본값은 20밀리초입니다. 이 매개 변수는 postgresql.conf 파일 또는 서버 명령줄에서만 설정할 수 있습니다. 이 설정은 테이블 스토리지 매개 변수를 변경하여 개별 테이블에 대해 재정의할 수 있습니다.|20밀리초
autovacuum_nap_time|지정된 데이터베이스에서 자동 진공 실행 사이의 최소 지연 시간을 지정합니다. 각 라운드에서 디먼은 데이터베이스를 검사하고, 해당 데이터베이스의 테이블에 필요한 대로 VACUUM 및 ANALYZE 명령을 실행합니다. 지연 시간은 초 단위로 측정되며, 기본값은 1분(1min)입니다. 이 매개 변수는 postgresql.conf 파일 또는 서버 명령줄에서만 설정할 수 있습니다.|15초
autovacuum_max_workers|한 번에 실행할 수 있는 자동 진공 프로세스(자동 진공 시작 관리자 제외)의 최대 수를 지정합니다. 기본값은 3입니다. 이 매개 변수는 서버를 시작할 때만 설정할 수 있습니다.|3
위의 설정은 테이블 스토리지 매개 변수를 변경하여 개별 테이블에 대해 재정의할 수 있습니다.  

## <a name="autovacuum-cost"></a>자동 진공 비용
진공 작업을 실행하는 데 드는 "비용"은 다음과 같습니다.
- 진공이 실행되는 데이터 페이지에 잠금이 적용됩니다.
- 진공이 실행 중일 때 계산과 메모리가 사용됩니다.

즉, 진공이 너무 자주 또는 너무 드물게 실행되지 않아야 하며 워크로드에 적응해야 합니다. 자동 진공 매개 변수 상호 간의 절충으로 인해 모든 자동 진공 매개 변수의 변경을 테스트하는 것이 좋습니다.

## <a name="autovacuum-start-trigger"></a>자동 진공 시작 트리거
데드 튜플의 수가 autovacuum_vacuum_threshold + autovacuum_vacuum_scale_factor * reltuples를 초과할 때 자동 진공이 트리거됩니다. 여기서 reltuples는 상수입니다.

자동 진공에서 정리하려면 데이터베이스 로드를 따라가야 합니다. 그렇지 않으면 스토리지가 부족해지고 쿼리 속도가 전반적으로 느려질 수 있습니다. 시간이 지남에 따라 분할 상환되며, 진공 작업에서 데드 튜플을 정리하는 속도는 데드 튜플을 만드는 속도와 같아야 합니다.

많은 업데이트/삭제가 있는 데이터베이스에는 더 많은 데드 튜플이 있으며 더 많은 공간이 필요합니다. 일반적으로 데드 튜플이 장기간 누적되지 않도록 방지하기 위해 많은 업데이트/삭제가 있는 데이터베이스는 autovacuum_vacuum_scale_actor 및 autovacuum_threshold의 낮은 값을 통해 이점을 얻습니다. 진공에 대한 요구 사항이 비교적 긴급하지 않으므로 더 작은 데이터베이스에서 두 매개 변수에 대해 더 높은 값을 사용할 수 있습니다. 이처럼 빈번한 진공 작업으로 인해 계산과 메모리가 소모된다는 것에 유의하세요.

기본 배율인 20%는 낮은 데드 튜플 비율의 테이블에서 효율적으로 작동하지만, 높은 데드 튜플 비율의 테이블에서는 그렇지 않습니다. 예를 들어 20GB 테이블에서는 4GB의 데드 튜플로 변환되고, 1TB의 테이블에서는 200GB의 데드 튜플이 됩니다.

PostgreSQL을 사용하면 이러한 매개 변수를 테이블 수준 또는 인스턴스 수준에서 설정할 수 있습니다. 현재 이러한 매개 변수는 Azure Database for PostgreSQL에서만 테이블 수준에서 설정할 수 있습니다.

## <a name="estimating-the-cost-of-autovacuum"></a>자동 진공 비용 예측
자동 진공을 실행하는 경우 "비용이 많이 들기" 때문에 진공 작업의 런타임을 제어하는 매개 변수가 있습니다. 진공 실행 비용을 예측하는 데 도움이 되는 매개 변수는 다음과 같습니다.
- vacuum_cost_page_hit = 1
- vacuum_cost_page_miss = 10
- vacuum_cost_page_dirty = 20

진공 프로세스는 물리적 페이지를 읽고 데드 튜플을 확인합니다. shared_buffers의 모든 페이지는 1(vacuum_cost_page_hit)의 비용이 있는 것으로 간주되며, 다른 모든 페이지는 데드 튜플이 있는 경우20(vacuum_cost_page_dirty), 데드 튜플이 없는 경우 10 (vacuum_cost_page_miss)의 비용이 있는 것으로 간주됩니다. 프로세스에서 autovacuum_vacuum_cost_limit를 초과하면 진공 작업이 중지됩니다.  

제한에 도달하면 프로세스가 autovacuum_vacuum_cost_delay 매개 변수에 지정된 기간 동안 기다린 후에 다시 시작됩니다. 제한에 도달하지 않으면 자동 진공이 autovacuum_nap_time 매개 변수에 지정된 값 이후에 시작됩니다.

요약하면, autovacuum_vacuum_cost_delay 및 autovacinum_vacuum_cost_limit 매개 변수는 단위 시간당 허용되는 데이터 정리의 양을 제어합니다. 대부분의 가격 책정 계층에서는 기본값이 너무 낮습니다. 이러한 매개 변수에 대한 최적 값은 가격 책정 계층에 따라 달라지며 이에 따라 적절히 구성해야 합니다.

autovacuum_max_worker 매개 변수는 동시에 실행할 수 있는 자동 진공 프로세스의 최대 수를 결정합니다.

PostgreSQL을 사용하면 이러한 매개 변수를 테이블 수준 또는 인스턴스 수준에서 설정할 수 있습니다. 현재 이러한 매개 변수는 Azure Database for PostgreSQL에서만 테이블 수준에서 설정할 수 있습니다.

## <a name="optimizing-autovacuum-per-table"></a>테이블당 자동 진공 최적화
위의 모든 구성 매개 변수는 다음과 같이 테이블별로 구성할 수 있습니다.
```sql
ALTER TABLE t SET (autovacuum_vacuum_threshold = 1000);
ALTER TABLE t SET (autovacuum_vacuum_scale_factor = 0.1);
ALTER TABLE t SET (autovacuum_vacuum_cost_limit = 1000);
ALTER TABLE t SET (autovacuum_vacuum_cost_delay = 10);
```

자동 진공은 테이블당 동기 프로세스입니다. 한 테이블에 있는 데드 튜플의 비율이 높을수록 자동 진공에 대한 "비용"도 더 높아집니다.  업데이트/삭제 비율이 높은 테이블을 여러 테이블로 분할하면 자동 진공을 병렬 처리하고 한 테이블에서 자동 진공을 수행하는 데 드는 "비용"을 줄일 수 있습니다. 또한 작업자를 자유롭게 예약할 수 있도록 병렬 자동 진공 작업자의 수를 늘릴 수 있습니다.

## <a name="next-steps"></a>다음 단계
자동 진공을 사용하고 튜닝하는 방법을 자세히 알아보려면 다음 PostgreSQL 설명서를 검토하세요.
 - PostgreSQL 설명서 - [18장 서버 구성](https://www.postgresql.org/docs/9.5/static/runtime-config-autovacuum.html)
 - PostgreSQL 설명서 – [24장 일상적인 데이터베이스 유지 관리 작업](https://www.postgresql.org/docs/9.6/static/routine-vacuuming.html)
