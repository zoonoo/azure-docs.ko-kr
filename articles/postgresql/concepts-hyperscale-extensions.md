---
title: Azure Database for PostgreSQL-Hyperscale (Citus)의 PostgreSQL 확장
description: Azure Database for PostgreSQL 확장을 사용 하 여 데이터베이스의 기능을 확장 하는 기능에 대해 설명 합니다.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: aabcb0b0d01d821c529803927dacec448c923745
ms.sourcegitcommit: 4b8a69b920ade815d095236c16175124a6a34996
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2019
ms.locfileid: "69998026"
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql--hyperscale-citus"></a>Azure Database for PostgreSQL-Hyperscale (Citus)의 PostgreSQL 확장

PostgreSQL는 확장을 사용 하 여 데이터베이스의 기능을 확장 하는 기능을 제공 합니다. 확장을 통해 관련된 여러 SQL 개체를 단일 패키지에 번들로 묶을 수 있으며 단일 명령을 사용해서 데이터베이스에서 로드하거나 제거할 수 있습니다. 데이터베이스에 로드 된 확장은 기본 제공 기능 처럼 작동할 수 있습니다. PostgreSQL 확장에 대 한 자세한 내용은 [패키지 관련 개체를 확장](https://www.postgresql.org/docs/9.6/static/extend-extensions.html)합니다 .를 참조 하세요.

## <a name="use-postgresql-extensions"></a>PostgreSQL 확장 사용

PostgreSQL 확장을 사용하려면 먼저 데이터베이스에 설치해야 합니다. 특정 확장을 설치 하려면 psql 도구에서 [CREATE extension](https://www.postgresql.org/docs/9.6/static/sql-createextension.html) 명령을 실행 하 여 패키지 된 개체를 데이터베이스에 로드 합니다.

Citus (Azure Database for PostgreSQL-Hyperscale) 미리 보기는 현재 여기에 나열 된 대로 키 확장의 하위 집합을 지원 합니다. 나열 된 확장 이외의 확장은 지원 되지 않습니다. Azure Database for PostgreSQL를 사용 하 여 사용자 고유의 확장을 만들 수 없습니다.

## <a name="extensions-supported-by-azure-database-for-postgresql"></a>PostgreSQL용 Azure 데이터베이스에서 지원하는 확장

다음 표에는 PostgreSQL용 Azure 데이터베이스에서 현재 지원하는 표준 PostgreSQL 확장이 나와 있습니다. 이 정보를 `SELECT * FROM pg_available_extensions;`를 실행하여 얻을 수도 있습니다.

### <a name="data-types-extensions"></a>데이터 형식 확장

> [!div class="mx-tableFixed"]
> | **확장명** | **설명** |
> |---|---|
> | [citext](https://www.postgresql.org/docs/9.6/static/citext.html) | 대/소문자 구분 문자 문자열 형식을 제공합니다. |
> | [cube](https://www.postgresql.org/docs/9.6/static/cube.html) | 다차원 큐브의 데이터 형식을 제공합니다. |
> | [hstore](https://www.postgresql.org/docs/9.6/static/hstore.html) | 키-값 쌍 집합을 저장 하기 위한 데이터 형식을 제공 합니다. |
> | [isn](https://www.postgresql.org/docs/9.6/static/isn.html) | 국제 제품 번호 매기기 표준에 대한 데이터 형식을 제공합니다. |
> | [lo](https://www.postgresql.org/docs/current/lo.html) | 대량 개체 유지 관리. |
> | [ltree](https://www.postgresql.org/docs/9.6/static/ltree.html) | 계층적 트리 구조에 대한 데이터 형식을 제공합니다. |
> | [seg](https://www.postgresql.org/docs/current/seg.html) | 선 세그먼트나 부동 소수점 간격을 나타내는 데이터 형식입니다. |
> | [topn](https://github.com/citusdata/postgresql-topn/) | Top-n JSONB에 대 한 형식입니다. |

### <a name="full-text-search-extensions"></a>전체 텍스트 검색 확장

> [!div class="mx-tableFixed"]
> | **확장명** | **설명** |
> |---|---|
> | [dict\_int](https://www.postgresql.org/docs/9.6/static/dict-int.html) | 정수에 대한 텍스트 검색 사전 템플릿을 제공합니다. |
> | [dict\_xsyn](https://www.postgresql.org/docs/current/dict-xsyn.html) | 확장 된 동의어 처리를 위한 텍스트 검색 사전 템플릿입니다. |
> | [unaccent](https://www.postgresql.org/docs/9.6/static/unaccent.html) | Lexemes에서 악센트(분음 기호)를 제거하는 텍스트 검색 사전입니다. |

### <a name="functions-extensions"></a>함수 확장

> [!div class="mx-tableFixed"]
> | **확장** | **설명** |
> |---|---|
> | [autoinc](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.7) | 자동 증가 필드를 위한 함수입니다. |
> | [earthdistance](https://www.postgresql.org/docs/9.6/static/earthdistance.html) | 지구 표면의 대원 거리를 계산하는 방법을 제공합니다. |
> | [fuzzystrmatch](https://www.postgresql.org/docs/9.6/static/fuzzystrmatch.html) | 문자열 간 유사성 및 거리를 확인하기 위한 몇 가지 함수를 제공합니다. |
> | [insert\_username](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.8) | 테이블을 변경한 사람을 추적 하는 함수입니다. |
> | [intagg](https://www.postgresql.org/docs/current/intagg.html) | 정수 집계 및 열거자 (사용 되지 않음). |
> | [intarray](https://www.postgresql.org/docs/9.6/static/intarray.html) | null 없는 정수 배열을 조작하기 위한 함수 및 연산자를 제공합니다. |
> | [moddatetime](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.9) | 마지막 수정 시간을 추적 하는 함수입니다. |
> | [pgcrypto](https://www.postgresql.org/docs/9.6/static/pgcrypto.html) | 암호화 함수를 제공합니다. |
> | [pg\_partman](https://pgxn.org/dist/pg_partman/doc/pg_partman.html) | 시간 또는 ID로 분할된 테이블을 관리합니다. |
> | [pg\_trgm](https://www.postgresql.org/docs/9.6/static/pgtrgm.html) | trigram 일치를 기준으로 영숫자 텍스트의 유사성을 확인하기 위한 함수 및 연산자를 제공합니다. |
> | [refint](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.5) | 참조 무결성을 구현 하는 함수입니다 (사용 되지 않음). |
> | session\_analytics | Hstore 배열을 쿼리 하는 함수입니다. |
> | [tablefunc](https://www.postgresql.org/docs/9.6/static/tablefunc.html) | 크로스탭을 비롯하여 전체 테이블을 조작하는 함수를 제공합니다. |
> | [tcn](https://www.postgresql.org/docs/current/tcn.html) | 변경 알림이 트리거 되었습니다. |
> | [timetravel](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.6) | 시간 이동 구현을 위한 함수입니다. |
> | [uuid-ossp](https://www.postgresql.org/docs/9.6/static/uuid-ossp.html) | UUID(범용 고유 식별자)를 생성합니다. |

### <a name="hyperscale-extensions"></a>Hyperscale 확장

> [!div class="mx-tableFixed"]
> | **확장명** | **설명** |
> |---|---|
> | [citus](https://github.com/citusdata/citus) | Citus 분산 데이터베이스입니다. |
> | shard\_rebalancer | 노드 추가 또는 제거의 경우 서버 그룹의 데이터를 안전 하 게 리 밸런스 합니다. |

### <a name="index-types-extensions"></a>인덱스 형식 확장

> [!div class="mx-tableFixed"]
> | **확장명** | **설명** |
> |---|---|
> | [bloom](https://www.postgresql.org/docs/current/bloom.html) | 블 룸 access 메서드 서명 파일 기반 인덱스입니다. |
> | [btree\_gin](https://www.postgresql.org/docs/9.6/static/btree-gin.html) | 특정 데이터 형식에 대 한 B-트리 형식의 동작을 구현 하는 샘플 GIN 연산자 클래스를 제공 합니다. |
> | [btree\_gist](https://www.postgresql.org/docs/9.6/static/btree-gist.html) | B-트리를 구현하는 GiST 인덱스 연산자 클래스를 제공합니다. |

### <a name="language-extensions"></a>언어 확장

> [!div class="mx-tableFixed"]
> | **확장명** | **설명** |
> |---|---|
> | [plpgsql](https://www.postgresql.org/docs/9.6/static/plpgsql.html) | PL/pgSQL 로드 가능 절차 언어. |

### <a name="miscellaneous-extensions"></a>기타 확장

> [!div class="mx-tableFixed"]
> | **확장명** | **설명** |
> |---|---|
> | [adminpack](https://www.postgresql.org/docs/current/adminpack.html) | PostgreSQL에 대 한 관리 기능. |
> | [amcheck](https://www.postgresql.org/docs/current/amcheck.html) | 관계 무결성을 확인 하는 함수입니다. |
> | [file\_fdw](https://www.postgresql.org/docs/current/file-fdw.html) | 플랫 파일 액세스용 외래 데이터 래퍼입니다. |
> | [pageinspect](https://www.postgresql.org/docs/current/pageinspect.html) | 낮은 수준에서 데이터베이스 페이지의 내용을 검사 합니다. |
> | [pg\_buffercache](https://www.postgresql.org/docs/9.6/static/pgbuffercache.html) | 공유 버퍼 캐시에서 일어나는 작업을 실시간으로 검사하기 위한 수단을 제공합니다. |
> | [pg\_cron](https://github.com/citusdata/pg_cron) | PostgreSQL에 대 한 작업 스케줄러입니다. |
> | [pg\_freespacemap](https://www.postgresql.org/docs/current/pgfreespacemap.html) | 사용 가능한 공간 맵 (FSM)을 검사 합니다. |
> | [pg\_prewarm](https://www.postgresql.org/docs/9.6/static/pgprewarm.html) | 관계 데이터를 버퍼 캐시에 로드하는 방법을 제공합니다. |
> | [pg\_stat\_statements](https://www.postgresql.org/docs/9.6/static/pgstatstatements.html) | 서버에서 실행되는 모든 SQL 문의 실행 통계를 추적하는 수단을 제공합니다. 이 확장에 대 한 자세한 내용은 "pg_stat_statements" 섹션을 참조 하세요. |
> | [pg\_visibility](https://www.postgresql.org/docs/current/pgvisibility.html) | 표시 유형 맵 (VM) 및 페이지 수준 표시 유형 정보를 검사 합니다. |
> | [pgrowlocks](https://www.postgresql.org/docs/9.6/static/pgrowlocks.html) | 행 수준 잠금 정보를 표시하는 방법을 제공합니다. |
> | [pgstattuple](https://www.postgresql.org/docs/9.6/static/pgstattuple.html) | 튜플 수준 통계를 표시하는 방법을 제공합니다. |
> | [postgres\_fdw](https://www.postgresql.org/docs/9.6/static/postgres-fdw.html) | 외부 PostgreSQL 서버에 저장된 데이터에 액세스하는 데 사용되는 외부 데이터 래퍼입니다. 이 확장에 대 한 자세한 내용은 "eblink and postgres_fdw" 섹션을 참조 하세요.|
> | [sslinfo](https://www.postgresql.org/docs/current/sslinfo.html) | SSL 인증서에 대 한 정보입니다. |
> | [tsm\_system\_rows](https://www.postgresql.org/docs/current/tsm-system-rows.html) | 행 수를 한도로 허용 하는 TABLESAMPLE 메서드. |
> | [tsm\_system\_time](https://www.postgresql.org/docs/current/tsm-system-time.html) | 제한 시간을 밀리초 단위로 허용 하는 TABLESAMPLE 메서드. |
> | [hypopg](https://hypopg.readthedocs.io/en/latest/) | CPU나 디스크 비용이 들지 않는 가상의 인덱스를 만드는 방법을 제공합니다. |
> | [dblink](https://www.postgresql.org/docs/current/dblink.html) | 데이터베이스 세션 내에서 다른 PostgreSQL 데이터베이스로의 연결을 지원하는 모듈입니다. 이 확장에 대 한 자세한 내용은 "eblink and postgres_fdw" 섹션을 참조 하세요. |
> | [xml2](https://www.postgresql.org/docs/current/xml2.html) | XPath 쿼리 및 XSLT. |


### <a name="postgis-extensions"></a>PostGIS 확장

> [!div class="mx-tableFixed"]
> | **확장명** | **설명** |
> |---|---|
> | [PostGIS](https://www.postgis.net/), postgis\_topology, postgis\_tiger\_geocoder, postgis\_sfcgal | PostgreSQL에 대한 공간 및 지리적 개체입니다. |
> | address\_standardizer, address\_standardizer\_data\_us | 주소를 구성 요소로 구문 분석하는 데 사용됩니다. 지오코딩 주소 정규화 단계를 지원하는 데 사용됩니다. |
> | postgis\_sfcgal | PostGIS SFCGAL 함수입니다. |
> | postgis\_tiger\_geocoder | PostGIS tiger geocoder 및 reverse geocoder입니다. |
> | postgis\_topology | PostGIS는 토폴로지 공간 유형 및 함수입니다. |


## <a name="pg_stat_statements"></a>pg_stat_statements
[Pg\_stat\_문 확장](https://www.postgresql.org/docs/current/pgstatstatements.html) 은 SQL 문의 실행 통계를 추적 하는 방법을 제공 하기 위해 모든 Azure Database for PostgreSQL 서버에 미리 로드 됩니다.

설정은 `pg_stat_statements.track` 확장에서 계산 되는 문을 제어 합니다. 기본적으로로 `top`설정 됩니다. 즉, 클라이언트에서 직접 실행 한 모든 문이 추적 됩니다. 다른 두 가지 추적 수준은 `none`과 `all`입니다. 이 설정은 [Azure Portal](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-portal)이나 [Azure CLI](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-cli)를 통해 서버 매개 변수로 구성할 수 있습니다.

Pg_stat_statements에서 제공 하는 쿼리 실행 정보와 각 SQL 문을 로깅할 때 서버 성능에 미치는 영향을 서로 절충 합니다. Pg_stat_statements 확장을 사용 하지 않는 경우를로 `pg_stat_statements.track` `none`설정 하는 것이 좋습니다. 일부 타사 모니터링 서비스는 pg_stat_statements를 사용 하 여 쿼리 성능 정보를 제공할 수 있으므로 이것이 사용자의 사례 인지 여부를 확인 합니다.

## <a name="dblink-and-postgres_fdw"></a>ablink 및 postgres_fdw
Eblink 및 postgres_fdw를 사용 하 여 한 PostgreSQL 서버에서 다른 서버에 연결 하거나 동일한 서버의 다른 데이터베이스에 연결할 수 있습니다. 받는 서버는 해당 방화벽을 통한 보내는 서버의 연결을 허용해야 합니다. 이러한 확장을 사용 하 여 Azure Database for PostgreSQL 서버 간에 연결 하려면 **Azure 서비스에 대 한 액세스 허용** 을 켜기로 설정 합니다. 또한 확장을 사용 하 여 동일한 서버에 다시 루프를 사용 하려는 경우이 설정을 켜야 합니다. **Azure 서비스에 대 한 액세스 허용** 설정은 **연결 보안**아래에 있는 postgres server에 대 한 Azure Portal 페이지에서 찾을 수 있습니다. 허용 목록 모든 Azure Ip에서 **azure 서비스에 대 한 액세스를 허용** 합니다.

현재는 다른 Azure Database for PostgreSQL 서버에 대 한 연결을 제외 하 고 Azure Database for PostgreSQL의 아웃 바운드 연결이 지원 되지 않습니다.
