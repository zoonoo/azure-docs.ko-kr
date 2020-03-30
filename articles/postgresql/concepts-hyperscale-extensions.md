---
title: 확장 - 하이퍼 스케일 (시터스) - PostgreSQL에 대한 Azure 데이터베이스
description: PostgreSQL - 하이퍼스케일(Citus)에 대한 Azure 데이터베이스의 확장을 사용하여 데이터베이스의 기능을 확장하는 기능에 대해 설명합니다.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 02/18/2020
ms.openlocfilehash: 84b28096904db49f98c16601c5927928ad38743b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77485406"
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql--hyperscale-citus"></a>PostgreSQL에 대 한 Azure 데이터베이스에서 PostgreSQL 확장 – 하이퍼 스케일 (시터스)

PostgreSQL은 확장을 사용하여 데이터베이스의 기능을 확장할 수 있는 기능을 제공합니다. 확장을 통해 관련된 여러 SQL 개체를 단일 패키지에 번들로 묶을 수 있으며 단일 명령을 사용해서 데이터베이스에서 로드하거나 제거할 수 있습니다. 데이터베이스에 로드된 후 확장은 기본 제공 기능처럼 작동할 수 있습니다. PostgreSQL 확장에 대한 자세한 내용은 [관련 개체를 확장으로 패키지](https://www.postgresql.org/docs/current/static/extend-extensions.html)를 참조하십시오.

## <a name="use-postgresql-extensions"></a>포스트그레SQL 확장 사용

PostgreSQL 확장을 사용하려면 먼저 데이터베이스에 설치해야 합니다. 특정 확장을 설치하려면 psql 도구에서 [CREATE 확장](https://www.postgresql.org/docs/current/static/sql-createextension.html) 명령을 실행하여 패키지된 개체를 데이터베이스에 로드합니다.

PostgreSQL용 Azure 데이터베이스 - 하이퍼스케일(Citus)은 현재 여기에 나열된 키 확장의 하위 집합을 지원합니다. 나열된 확장 이외의 확장은 지원되지 않습니다. PostgreSQL용 Azure 데이터베이스로 는 사용자 고유의 확장을 만들 수 없습니다.

## <a name="extensions-supported-by-azure-database-for-postgresql"></a>PostgreSQL용 Azure 데이터베이스에서 지원하는 확장

다음 표에는 PostgreSQL용 Azure 데이터베이스에서 현재 지원하는 표준 PostgreSQL 확장이 나와 있습니다. 이 정보를 `SELECT * FROM pg_available_extensions;`를 실행하여 얻을 수도 있습니다.

### <a name="data-types-extensions"></a>데이터 형식 확장

> [!div class="mx-tableFixed"]
> | **확장** | **설명** |
> |---|---|
> | [citext](https://www.postgresql.org/docs/current/static/citext.html) | 대/소문자 구분 문자 문자열 형식을 제공합니다. |
> | [큐브](https://www.postgresql.org/docs/current/static/cube.html) | 다차원 큐브의 데이터 형식을 제공합니다. |
> | [hstore](https://www.postgresql.org/docs/current/static/hstore.html) | 키-값 쌍 집합을 저장 하기 위한 데이터 형식을 제공 합니다. |
> | [hll](https://github.com/citusdata/postgresql-hll) | 하이퍼로그 데이터 구조를 제공합니다. |
> | [isn](https://www.postgresql.org/docs/current/static/isn.html) | 국제 제품 번호 매기기 표준에 대한 데이터 형식을 제공합니다. |
> | [Lo](https://www.postgresql.org/docs/current/lo.html) | 큰 개체 유지 관리. |
> | [ltree](https://www.postgresql.org/docs/current/static/ltree.html) | 계층적 트리 구조에 대한 데이터 형식을 제공합니다. |
> | [Seg](https://www.postgresql.org/docs/current/seg.html) | 선 세그먼트 또는 부동 점 간격을 나타내는 데이터 형식입니다. |
> | [토른 (것)](https://github.com/citusdata/postgresql-topn/) | 맨 위 JSONB에 대 한 입력합니다. |

### <a name="full-text-search-extensions"></a>전체 텍스트 검색 확장

> [!div class="mx-tableFixed"]
> | **확장** | **설명** |
> |---|---|
> | [dict\_int](https://www.postgresql.org/docs/current/static/dict-int.html) | 정수에 대한 텍스트 검색 사전 템플릿을 제공합니다. |
> | [딕트\_xsyn](https://www.postgresql.org/docs/current/dict-xsyn.html) | 확장 된 동의어 처리를위한 텍스트 검색 사전 템플릿. |
> | [unaccent](https://www.postgresql.org/docs/current/static/unaccent.html) | Lexemes에서 악센트(분음 기호)를 제거하는 텍스트 검색 사전입니다. |

### <a name="functions-extensions"></a>함수 확장

> [!div class="mx-tableFixed"]
> | **확장** | **설명** |
> |---|---|
> | [오토인크](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.7) | 자동 증분 필드에 대한 함수입니다. |
> | [earthdistance](https://www.postgresql.org/docs/current/static/earthdistance.html) | 지구 표면의 대원 거리를 계산하는 방법을 제공합니다. |
> | [fuzzystrmatch](https://www.postgresql.org/docs/current/static/fuzzystrmatch.html) | 문자열 간 유사성 및 거리를 확인하기 위한 몇 가지 함수를 제공합니다. |
> | [사용자\_이름 삽입](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.8) | 테이블을 변경한 사람을 추적하기 위한 함수입니다. |
> | [인태그](https://www.postgresql.org/docs/current/intagg.html) | 정수 집계 및 열거자(사용되지 않음). |
> | [intarray](https://www.postgresql.org/docs/current/static/intarray.html) | null 없는 정수 배열을 조작하기 위한 함수 및 연산자를 제공합니다. |
> | [모드 날짜 시간](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.9) | 마지막 수정 시간을 추적하기 위한 기능입니다. |
> | [pgcrypto](https://www.postgresql.org/docs/current/static/pgcrypto.html) | 암호화 함수를 제공합니다. |
> | [pg\_partman](https://pgxn.org/dist/pg_partman/doc/pg_partman.html) | 시간 또는 ID로 분할된 테이블을 관리합니다. |
> | [pg\_trgm](https://www.postgresql.org/docs/current/static/pgtrgm.html) | trigram 일치를 기준으로 영숫자 텍스트의 유사성을 확인하기 위한 함수 및 연산자를 제공합니다. |
> | [정제](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.5) | 참조 무결성을 구현하기 위한 함수(더 이상 사용되지 않음). |
> | 세션\_분석 | hstore 배열을 쿼리하기 위한 함수입니다. |
> | [tablefunc](https://www.postgresql.org/docs/current/static/tablefunc.html) | 크로스탭을 비롯하여 전체 테이블을 조작하는 함수를 제공합니다. |
> | [tcn](https://www.postgresql.org/docs/current/tcn.html) | 트리거된 변경 알림입니다. |
> | [시간 여행](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.6) | 시간 여행을 구현하기위한 기능. |
> | [uuid-ossp](https://www.postgresql.org/docs/current/static/uuid-ossp.html) | UUID(범용 고유 식별자)를 생성합니다. |

### <a name="hyperscale-extensions"></a>하이퍼스케일 확장

> [!div class="mx-tableFixed"]
> | **확장** | **설명** |
> |---|---|
> | [시터스 (있는)](https://github.com/citusdata/citus) | 시터스 분산 데이터베이스. |
> | 샤드\_리밸저 | 노드 추가 또는 제거시 서버 그룹의 데이터를 안전하게 재조정합니다. |

### <a name="index-types-extensions"></a>인덱스 유형 확장

> [!div class="mx-tableFixed"]
> | **확장** | **설명** |
> |---|---|
> | [꽃](https://www.postgresql.org/docs/current/bloom.html) | Bloom 액세스 방법 - 서명 파일 기반 인덱스입니다. |
> | [btree\_gin](https://www.postgresql.org/docs/current/static/btree-gin.html) | 특정 데이터 형식에 대해 B-트리와 같은 동작을 구현하는 샘플 GIN 연산자 클래스를 제공합니다. |
> | [btree\_gist](https://www.postgresql.org/docs/current/static/btree-gist.html) | B-트리를 구현하는 GiST 인덱스 연산자 클래스를 제공합니다. |

### <a name="language-extensions"></a>언어 확장

> [!div class="mx-tableFixed"]
> | **확장** | **설명** |
> |---|---|
> | [plpgsql](https://www.postgresql.org/docs/current/static/plpgsql.html) | PL/pgSQL 로드 가능 절차 언어. |

### <a name="miscellaneous-extensions"></a>기타 확장

> [!div class="mx-tableFixed"]
> | **확장** | **설명** |
> |---|---|
> | [관리 팩](https://www.postgresql.org/docs/current/adminpack.html) | PostgreSQL에 대한 관리 기능. |
> | [amcheck](https://www.postgresql.org/docs/current/amcheck.html) | 관계 무결성을 확인하는 기능입니다. |
> | [파일\_FDW](https://www.postgresql.org/docs/current/file-fdw.html) | 플랫 파일 액세스를 위한 외부 데이터 래퍼입니다. |
> | [페이지 검사](https://www.postgresql.org/docs/current/pageinspect.html) | 낮은 수준에서 데이터베이스 페이지의 내용을 검사합니다. |
> | [pg\_buffercache](https://www.postgresql.org/docs/current/static/pgbuffercache.html) | 공유 버퍼 캐시에서 일어나는 작업을 실시간으로 검사하기 위한 수단을 제공합니다. |
> | [PG\_크론](https://github.com/citusdata/pg_cron) | 포스트그레SQL의 작업 스케줄러. |
> | [pg\_프리스페이스맵](https://www.postgresql.org/docs/current/pgfreespacemap.html) | 여유 공간 맵(FSM)을 검사합니다. |
> | [pg\_prewarm](https://www.postgresql.org/docs/current/static/pgprewarm.html) | 관계 데이터를 버퍼 캐시에 로드하는 방법을 제공합니다. |
> | [pg\_stat\_statements](https://www.postgresql.org/docs/current/static/pgstatstatements.html) | 서버에서 실행되는 모든 SQL 문의 실행 통계를 추적하는 수단을 제공합니다. 이 확장에 대한 자세한 내용은 "pg_stat_statements" 섹션을 참조하세요. |
> | [pg\_가시성](https://www.postgresql.org/docs/current/pgvisibility.html) | VM(가시성 맵) 및 페이지 수준 가시성 정보를 검사합니다. |
> | [pgrowlocks](https://www.postgresql.org/docs/current/static/pgrowlocks.html) | 행 수준 잠금 정보를 표시하는 방법을 제공합니다. |
> | [pgstattuple](https://www.postgresql.org/docs/current/static/pgstattuple.html) | 튜플 수준 통계를 표시하는 방법을 제공합니다. |
> | [postgres\_fdw](https://www.postgresql.org/docs/current/static/postgres-fdw.html) | 외부 PostgreSQL 서버에 저장된 데이터에 액세스하는 데 사용되는 외부 데이터 래퍼입니다. 이 확장에 대한 자세한 내용은 "dblink 및 postgres_fdw" 섹션을 참조하십시오.|
> | [sslinfo](https://www.postgresql.org/docs/current/sslinfo.html) | SSL 인증서에 대한 정보입니다. |
> | [tsm\_\_시스템 행](https://www.postgresql.org/docs/current/tsm-system-rows.html) | 행 수를 제한으로 허용하는 TABLESAMPLE 메서드입니다. |
> | [tsm\_\_시스템 시간](https://www.postgresql.org/docs/current/tsm-system-time.html) | 시간(밀리초)을 제한으로 허용하는 TABLESAMPLE 메서드입니다. |
> | [hypopg](https://hypopg.readthedocs.io/en/latest/) | CPU나 디스크 비용이 들지 않는 가상의 인덱스를 만드는 방법을 제공합니다. |
> | [dblink](https://www.postgresql.org/docs/current/dblink.html) | 데이터베이스 세션 내에서 다른 PostgreSQL 데이터베이스로의 연결을 지원하는 모듈입니다. 이 확장에 대한 자세한 내용은 "dblink 및 postgres_fdw" 섹션을 참조하십시오. |
> | [xml2](https://www.postgresql.org/docs/current/xml2.html) | XPath 쿼리 및 XSLT. |


### <a name="postgis-extensions"></a>PostGIS 확장

> [!div class="mx-tableFixed"]
> | **확장** | **설명** |
> |---|---|
> | [PostGIS](https://www.postgis.net/), postgis\_topology, postgis\_tiger\_geocoder, postgis\_sfcgal | PostgreSQL에 대한 공간 및 지리적 개체입니다. |
> | address\_standardizer, address\_standardizer\_data\_us | 주소를 구성 요소로 구문 분석하는 데 사용됩니다. 지오코딩 주소 정규화 단계를 지원하는 데 사용됩니다. |
> | 포스트기스\_스프갈 | 포스트기스 SFCGAL 기능. |
> | 포스트기스\_\_타이거 지오코더 | PostGIS 호랑이 지오 코더 및 역 지오 코더. |
> | 포스트기스\_토폴로지 | PostGIS 토폴로지 공간 유형 및 기능. |


## <a name="pg_stat_statements"></a>pg_stat_statements
[pg\_stat\_문 확장은](https://www.postgresql.org/docs/current/pgstatstatements.html) PostgreSQL 서버의 모든 Azure 데이터베이스에 미리 로드되어 SQL 문의 실행 통계를 추적하는 방법을 제공합니다.

설정은 `pg_stat_statements.track` 확장에 의해 계산되는 문을 제어합니다. 기본적으로 `top`는 클라이언트에서 직접 발급한 모든 문이 추적된다는 것을 의미합니다. 다른 두 가지 추적 수준은 `none`과 `all`입니다. 이 설정은 [Azure Portal](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-portal)이나 [Azure CLI](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-cli)를 통해 서버 매개 변수로 구성할 수 있습니다.

pg_stat_statements 제공하는 쿼리 실행 정보와 각 SQL 문을 기록할 때 서버 성능에 미치는 영향 사이에는 장단점이 있습니다. pg_stat_statements 확장을 적극적으로 사용하지 않는 경우 `pg_stat_statements.track` `none`로 설정하는 것이 좋습니다. 일부 타사 모니터링 서비스는 쿼리 성능 통찰력을 제공하기 위해 pg_stat_statements 의존할 수 있으므로 이러한 서비스인지 여부를 확인합니다.

## <a name="dblink-and-postgres_fdw"></a>드블링 및 postgres_fdw
dblink 및 postgres_fdw 사용하여 PostgreSQL 서버 한 대에서 다른 서버또는 동일한 서버의 다른 데이터베이스에 연결할 수 있습니다. 받는 서버는 해당 방화벽을 통한 보내는 서버의 연결을 허용해야 합니다. 이러한 확장을 사용하여 PostgreSQL 서버에 대한 Azure 데이터베이스 간에 연결하려면 Azure 서비스에 대한 **액세스 허용을** ON으로 설정합니다. 또한 확장을 사용하여 동일한 서버로 다시 루프하려면 이 설정을 ON으로 설정해야 합니다. **Azure 서비스 설정에 대한 액세스 허용은** **연결 보안**에서 Postgres 서버의 Azure 포털 페이지에서 찾을 수 있습니다. 켜기 **켜기 Azure 서비스에 대한 액세스 허용** ON은 모든 Azure IP를 허용합니다.

현재 PostgreSQL용 Azure 데이터베이스의 아웃바운드 연결은 PostgreSQL 서버에 대한 다른 Azure 데이터베이스에 대한 연결을 제외하고 지원되지 않습니다.
