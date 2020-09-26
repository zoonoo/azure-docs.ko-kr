---
title: 확장-Azure Database for PostgreSQL-유연한 서버
description: Azure Database for PostgreSQL 유연한 서버에서 사용할 수 있는 Postgres 확장에 대해 알아봅니다.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: f38006e83be47142a6d7a6db25eefb3daccd0d92
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91307588"
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql---flexible-server"></a>Azure Database for PostgreSQL 유연한 서버의 PostgreSQL 확장

> [!IMPORTANT]
> Azure Database for PostgreSQL - 유연한 서버는 미리 보기로 제공됨

PostgreSQL은 확장을 사용하여 데이터베이스의 기능을 확장하는 방법을 제공합니다. 확장은 여러 관련 SQL 개체를 단일 패키지에 함께 묶어 데이터베이스에서 로드 하거나 제거할 수 있습니다. 데이터베이스에 로드한 확장은 기본 제공 기능처럼 작동합니다.

## <a name="how-to-use-postgresql-extensions"></a>PostgreSQL 확장을 사용하는 방법
PostgreSQL 확장을 사용하려면 먼저 데이터베이스에 설치해야 합니다. 특정 확장을 설치 하려면 [CREATE extension](https://www.postgresql.org/docs/current/sql-createextension.html) 명령을 실행 합니다. 이 명령은 패키지 된 개체를 데이터베이스에 로드 합니다.

Azure Database for PostgreSQL은 아래에 나열 된 대로 키 확장의 하위 집합을 지원 합니다. 이 정보를 `SHOW azure.extensions;`를 실행하여 얻을 수도 있습니다. 이 문서에 나열 되지 않은 확장은 Azure Database for PostgreSQL 유연한 서버에서 지원 되지 않습니다. Azure Database for PostgreSQL에서 사용자 고유의 확장을 만들거나 로드할 수 없습니다.


## <a name="postgres-12-extensions"></a>Postgres 12 확장

다음 확장은 Postgres 버전 12가 있는 Azure Database for PostgreSQL 유연한 서버에서 사용할 수 있습니다. 

> [!div class="mx-tableFixed"]
> | **내선 번호**| **확장 버전** | **설명** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 3.0.0           | 주소를 구성 요소로 구문 분석하는 데 사용됩니다. |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 3.0.0           | Address Standardizer US 데이터 집합 예제|
> |[amcheck](https://www.postgresql.org/docs/12/amcheck.html)                    | 1.2             | 관계 무결성을 확인 하기 위한 함수|
> |[블 룸](https://www.postgresql.org/docs/12/bloom.html)                    | 1.0             | 블 룸 access 메서드-서명 파일 기반 인덱스|
> |[btree_gin](https://www.postgresql.org/docs/12/btree-gin.html)                    | 1.3             | GIN의 공통 데이터 형식 인덱싱 지원|
> |[btree_gist](https://www.postgresql.org/docs/12/btree-gist.html)                   | 1.5             | 요점의 공통 데이터 형식 인덱싱 지원|
> |[citext](https://www.postgresql.org/docs/12/citext.html)                       | 1.6             | 대/소문자를 구분 하지 않는 문자열의 데이터 형식|
> |[큐브](https://www.postgresql.org/docs/12/cube.html)                         | 1.4             | 다차원 큐브에 대 한 데이터 형식|
> |[dblink](https://www.postgresql.org/docs/12/dblink.html)                       | 1.2             | 데이터베이스 내에서 다른 PostgreSQL 데이터베이스에 연결|
> |[dict_int](https://www.postgresql.org/docs/12/dict-int.html)                     | 1.0             | 정수에 대 한 텍스트 검색 사전 템플릿|
> |[dict_xsyn](https://www.postgresql.org/docs/12/dict-xsyn.html)                     | 1.0             | 확장 된 동의어 처리를 위한 텍스트 검색 사전 템플릿|
> |[earthdistance](https://www.postgresql.org/docs/12/earthdistance.html)                | 1.1             | 지구 표면에서 큰 원 거리를 계산 합니다.|
> |[fuzzystrmatch](https://www.postgresql.org/docs/12/fuzzystrmatch.html)                | 1.1             | 문자열 간의 유사성 및 거리 결정|
> |[hstore](https://www.postgresql.org/docs/12/hstore.html)                       | 1.6             | (키, 값) 쌍 집합을 저장 하기 위한 데이터 형식|
> |[intagg](https://www.postgresql.org/docs/12/intagg.html)                     | 1.1             | 정수 집계 및 열거자. 않게|
> |[intarray](https://www.postgresql.org/docs/12/intarray.html)                     | 1.2             | 정수의 1 차원 배열에 대 한 함수, 연산자 및 인덱스 지원|
> |[isn](https://www.postgresql.org/docs/12/isn.html)                          | 1.2             | 국제 제품 번호 매기기 표준에 대 한 데이터 형식|
> |[ltree](https://www.postgresql.org/docs/12/ltree.html)                        | 1.1             | 계층적 트리 구조에 대 한 데이터 형식|
> |[pageinspect](https://www.postgresql.org/docs/12/pageinspect.html)                        | 1.7             | 낮은 수준에서 데이터베이스 페이지의 내용 검사|
> |[pg_buffercache](https://www.postgresql.org/docs/12/pgbuffercache.html)               | 1.3             | 공유 버퍼 캐시를 검사 합니다.|
> |[pg_freespacemap](https://www.postgresql.org/docs/12/pgfreespacemap.html)               | 1.2             | 사용 가능한 공간 맵 (FSM) 검사|
> |[pg_prewarm](https://www.postgresql.org/docs/12/pgprewarm.html)                   | 1.2             | 사전 웜 관계 데이터|
> |[pg_stat_statements](https://www.postgresql.org/docs/12/pgstatstatements.html)           | 1.7             | 실행 된 모든 SQL 문의 실행 통계 추적|
> |[pg_trgm](https://www.postgresql.org/docs/12/pgtrgm.html)                      | 1.4             | trigrams을 기반으로 하는 텍스트 유사성 측정 및 인덱스 검색|
> |[pg_visibility](https://www.postgresql.org/docs/12/pgvisibility.html)                      | 1.2             | 표시 유형 맵 (VM) 및 페이지 수준 표시 유형 정보를 검사 합니다.|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.4             | 감사 기능 제공|
> |[pgcrypto](https://www.postgresql.org/docs/12/pgcrypto.html)                     | 1.3             | 암호화 함수|
> |[pgrowlocks](https://www.postgresql.org/docs/12/pgrowlocks.html)                   | 1.2             | 행 수준 잠금 정보 표시|
> |[pgstattuple](https://www.postgresql.org/docs/12/pgstattuple.html)                  | 1.5             | 튜플 수준 통계 표시|
> |[plpgsql](https://www.postgresql.org/docs/12/plpgsql.html)                      | 1.0             | PL/pgSQL 절차적 언어|
> |[postgis](https://www.postgis.net/)                      | 3.0.0           | PostGIS geometry, geography |
> |[postgis_raster](https://www.postgis.net/)               | 3.0.0           | PostGIS 래스터 형식 및 함수| 
> |[postgis_sfcgal](https://www.postgis.net/)               | 3.0.0           | PostGIS SFCGAL 함수|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 3.0.0           | PostGIS tiger geocoder 및 reverse geocoder|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 3.0.0           | PostGIS 토폴로지 공간 형식 및 함수|
> |[postgres_fdw](https://www.postgresql.org/docs/12/postgres-fdw.html)                 | 1.0             | remote PostgreSQL servers에 대 한 외부 데이터 래퍼|
> |[sslin](https://www.postgresql.org/docs/12/sslinfo.html)                    | 1.2             | SSL 인증서에 대 한 정보|
> |[tsm_system_rows](https://www.postgresql.org/docs/12/tsm-system-rows.html)                    | 1.0             |  행 수를 제한으로 허용 하는 TABLESAMPLE 메서드|
> |[tsm_system_time](https://www.postgresql.org/docs/12/tsm-system-time.html)                    | 1.0             |  제한 시간 (밀리초)을 허용 하는 TABLESAMPLE 메서드|
> |[unaccent](https://www.postgresql.org/docs/12/unaccent.html)                     | 1.1             | 강조를 제거 하는 텍스트 검색 사전|
> |[uuid-ossp](https://www.postgresql.org/docs/12/uuid-ossp.html)                    | 1.1             | Uuid (범용 고유 식별자)를 생성 합니다.|

## <a name="postgres-11-extensions"></a>Postgres 11 확장

다음 확장은 Postgres 버전 11이 있는 Azure Database for PostgreSQL 유연한 서버에서 사용할 수 있습니다. 

> [!div class="mx-tableFixed"]
> | **내선 번호**| **확장 버전** | **설명** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.5.1           | 주소를 구성 요소로 구문 분석하는 데 사용됩니다. |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.5.1           | Address Standardizer US 데이터 집합 예제|
> |[amcheck](https://www.postgresql.org/docs/11/amcheck.html)                    | 1.1             | 관계 무결성을 확인 하기 위한 함수|
> |[블 룸](https://www.postgresql.org/docs/11/bloom.html)                    | 1.0             | 블 룸 access 메서드-서명 파일 기반 인덱스|
> |[btree_gin](https://www.postgresql.org/docs/11/btree-gin.html)                    | 1.3             | GIN의 공통 데이터 형식 인덱싱 지원|
> |[btree_gist](https://www.postgresql.org/docs/11/btree-gist.html)                   | 1.5             | 요점의 공통 데이터 형식 인덱싱 지원|
> |[citext](https://www.postgresql.org/docs/11/citext.html)                       | 1.5             | 대/소문자를 구분 하지 않는 문자열의 데이터 형식|
> |[큐브](https://www.postgresql.org/docs/11/cube.html)                         | 1.4             | 다차원 큐브에 대 한 데이터 형식|
> |[dblink](https://www.postgresql.org/docs/11/dblink.html)                       | 1.2             | 데이터베이스 내에서 다른 PostgreSQL 데이터베이스에 연결|
> |[dict_int](https://www.postgresql.org/docs/11/dict-int.html)                     | 1.0             | 정수에 대 한 텍스트 검색 사전 템플릿|
> |[dict_xsyn](https://www.postgresql.org/docs/11/dict-xsyn.html)                     | 1.0             | 확장 된 동의어 처리를 위한 텍스트 검색 사전 템플릿|
> |[earthdistance](https://www.postgresql.org/docs/11/earthdistance.html)                | 1.1             | 지구 표면에서 큰 원 거리를 계산 합니다.|
> |[fuzzystrmatch](https://www.postgresql.org/docs/11/fuzzystrmatch.html)                | 1.1             | 문자열 간의 유사성 및 거리 결정|
> |[hstore](https://www.postgresql.org/docs/11/hstore.html)                       | 1.5             | (키, 값) 쌍 집합을 저장 하기 위한 데이터 형식|
> |[intagg](https://www.postgresql.org/docs/11/intagg.html)                     | 1.1             | 정수 집계 및 열거자. 않게|
> |[intarray](https://www.postgresql.org/docs/11/intarray.html)                     | 1.2             | 정수의 1 차원 배열에 대 한 함수, 연산자 및 인덱스 지원|
> |[isn](https://www.postgresql.org/docs/11/isn.html)                          | 1.2             | 국제 제품 번호 매기기 표준에 대 한 데이터 형식|
> |[ltree](https://www.postgresql.org/docs/11/ltree.html)                        | 1.1             | 계층적 트리 구조에 대 한 데이터 형식|
> |[pageinspect](https://www.postgresql.org/docs/11/pageinspect.html)                        | 1.7             | 낮은 수준에서 데이터베이스 페이지의 내용 검사|
> |[pg_buffercache](https://www.postgresql.org/docs/11/pgbuffercache.html)               | 1.3             | 공유 버퍼 캐시를 검사 합니다.|
> |[pg_freespacemap](https://www.postgresql.org/docs/11/pgfreespacemap.html)               | 1.2             | 사용 가능한 공간 맵 (FSM) 검사|
> |[pg_prewarm](https://www.postgresql.org/docs/11/pgprewarm.html)                   | 1.2             | 사전 웜 관계 데이터|
> |[pg_stat_statements](https://www.postgresql.org/docs/11/pgstatstatements.html)           | 1.6             | 실행 된 모든 SQL 문의 실행 통계 추적|
> |[pg_trgm](https://www.postgresql.org/docs/11/pgtrgm.html)                      | 1.4             | trigrams을 기반으로 하는 텍스트 유사성 측정 및 인덱스 검색|
> |[pg_visibility](https://www.postgresql.org/docs/11/pgvisibility.html)                      | 1.2             | 표시 유형 맵 (VM) 및 페이지 수준 표시 유형 정보를 검사 합니다.|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.3.1             | 감사 기능 제공|
> |[pgcrypto](https://www.postgresql.org/docs/11/pgcrypto.html)                     | 1.3             | 암호화 함수|
> |[pgrowlocks](https://www.postgresql.org/docs/11/pgrowlocks.html)                   | 1.2             | 행 수준 잠금 정보 표시|
> |[pgstattuple](https://www.postgresql.org/docs/11/pgstattuple.html)                  | 1.5             | 튜플 수준 통계 표시|
> |[plpgsql](https://www.postgresql.org/docs/11/plpgsql.html)                      | 1.0             | PL/pgSQL 절차적 언어|
> |[postgis](https://www.postgis.net/)                      | 2.5.1           | PostGIS geometry, geography 및 래스터 공간 형식 및 함수|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.5.1           | PostGIS SFCGAL 함수|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.5.1           | PostGIS tiger geocoder 및 reverse geocoder|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.5.1           | PostGIS 토폴로지 공간 형식 및 함수|
> |[postgres_fdw](https://www.postgresql.org/docs/11/postgres-fdw.html)                 | 1.0             | remote PostgreSQL servers에 대 한 외부 데이터 래퍼|
> |[sslin](https://www.postgresql.org/docs/11/sslinfo.html)                    | 1.2             | SSL 인증서에 대 한 정보|
> |[tablefunc](https://www.postgresql.org/docs/11/tablefunc.html)                    | 1.0             | 크로스탭을 포함 하 여 전체 테이블을 조작 하는 함수|
> |[tsm_system_rows](https://www.postgresql.org/docs/11/tsm-system-rows.html)                    | 1.0             |  행 수를 제한으로 허용 하는 TABLESAMPLE 메서드|
> |[tsm_system_time](https://www.postgresql.org/docs/11/tsm-system-time.html)                    | 1.0             |  제한 시간 (밀리초)을 허용 하는 TABLESAMPLE 메서드|
> |[unaccent](https://www.postgresql.org/docs/11/unaccent.html)                     | 1.1             | 강조를 제거 하는 텍스트 검색 사전|
> |[uuid-ossp](https://www.postgresql.org/docs/11/uuid-ossp.html)                    | 1.1             | Uuid (범용 고유 식별자)를 생성 합니다.|


## <a name="dblink-and-postgres_fdw"></a>ablink 및 postgres_fdw
[dblink](https://www.postgresql.org/docs/current/contrib-dblink-function.html) PostgreSQL 서버에서 다른 서버에 연결 하거나 동일한 서버의 다른 데이터베이스에 연결할 수 있습니다. [postgres_fdw](https://www.postgresql.org/docs/current/postgres-fdw.html) 보내는 서버는 수신 서버에 대 한 아웃 바운드 연결을 허용 해야 합니다. 수신 서버는 보내는 서버에서의 연결을 허용 해야 합니다.

이러한 두 확장을 사용할 계획인 경우 [VNet 통합](concepts-networking.md) 을 사용 하 여 서버를 배포 하는 것이 좋습니다. 기본적으로 VNet 통합은 VNET의 서버 간 연결을 허용 합니다. [VNet 네트워크 보안 그룹](../../virtual-network/manage-network-security-group.md) 을 사용 하 여 액세스를 사용자 지정 하도록 선택할 수도 있습니다.


## <a name="pg_prewarm"></a>pg_prewarm

Pg_prewarm 확장은 관계형 데이터를 캐시로 로드 합니다. 캐시를 미리 준비 하는 것은 다시 시작 후 쿼리를 처음 실행 하는 경우 응답 시간이 더 나은 것을 의미 합니다. 자동 prewarm 기능은 Azure Database for PostgreSQL 유연한 서버에서 현재 사용할 수 없습니다.

## <a name="pg_stat_statements"></a>pg_stat_statements
[Pg_stat_statements 확장](https://www.postgresql.org/docs/current/pgstatstatements.html) 은 SQL 문의 실행 통계를 추적 하는 수단을 제공 하기 위해 모든 Azure Database for PostgreSQL 유연한 서버에 미리 로드 됩니다.
확장을 통해 어떤 명령문을 계산할지 제어하는 `pg_stat_statements.track` 설정의 기본값은 `top`이며 이것은 클라이언트가 직접 실행하는 모든 명령문이 추적된다는 의미입니다. 다른 두 가지 추적 수준은 `none`과 `all`입니다. 이 설정은 서버 매개 변수로 구성할 수 있습니다.

pg_stat_statements를 통해 제공되는 쿼리 실행 정보와 각 SQL 문을 기록할 때 서버 성능에 미치는 영향 사이에는 상충 관계가 있습니다. pg_stat_statements 확장을 적극적으로 사용하지 않는 경우에는 `pg_stat_statements.track`을 `none`으로 설정하는 것이 좋습니다. 일부 타사 모니터링 서비스가 pg_stat_statements에 기반하여 쿼리 성능 정보를 제공할 수 있으므로 여기에 해당하는 경우인지 여부를 확인하십시오.


## <a name="next-steps"></a>다음 단계

사용하려는 확장이 표시되지 않으면 알려주세요. [피드백 포럼](https://feedback.azure.com/forums/597976-azure-database-for-postgresql)에서 기존 요청에 투표 하거나 새 피드백 요청을 만듭니다.