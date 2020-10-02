---
title: PostgreSQL 확장 사용
description: PostgreSQL 확장 사용
titleSuffix: Azure Arc enabled data services
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 58386786266c48c6e721094f9f2837709bb684e5
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2020
ms.locfileid: "91631769"
---
# <a name="use-postgresql-extensions-in-your-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Azure Arc enabled PostgreSQL Hyperscale 서버 그룹에서 PostgreSQL 확장 사용

PostgreSQL는 확장과 함께 사용할 때 가장 효율적입니다. 실제로 자체적인 하이퍼 크기 조정 기능의 주요 요소는 `citus` 기본적으로 설치 되는 Microsoft 제공 확장 이며,이 확장을 통해 Postgres가 여러 노드에 걸쳐 데이터를 투명 하 게 분할할 수 있습니다.


[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="list-of-extensions"></a>확장 목록
의 확장 외에도 [`contrib`](https://www.postgresql.org/docs/12/contrib.html) Azure Arc Enabled PostgreSQL Hyperscale 서버 그룹의 컨테이너에 있는 확장 목록은 다음과 같습니다.
- `citus`, v: 9.4
- `pg_cron`, v: 1.2
- `plpgsql`, v: 1.0
- `postgis`, v: 3.0.2
- `plv8`, v: 2.3.14

이 목록에는 초과 시간이 진화 하 고 업데이트가이 문서에 게시 됩니다. 아직 위에 나열 된 확장 이외의 확장을 추가할 수는 없습니다.

이 가이드에서는 다음 두 가지 확장을 사용 하는 시나리오를 살펴보겠습니다.
- [PostGIS](https://postgis.net/)
- [`pg_cron`](https://github.com/citusdata/pg_cron)


## <a name="manage-extensions"></a>확장 관리

### <a name="enable-extensions"></a>확장 사용
의 일부인 확장에는이 단계가 필요 하지 않습니다 `contrib` .
확장을 사용 하도록 설정 하는 명령의 일반적인 형식은 다음과 같습니다.

#### <a name="enable-an-extension-at-the-creation-time-of-a-server-group"></a>서버 그룹을 만들 때 확장을 사용 하도록 설정 합니다.
```console
azdata arc postgres server create -n <name of your postgresql server group> --extensions <extension names>
```
#### <a name="enable-an-extension-on-an-instance-that-already-exists"></a>이미 존재 하는 인스턴스에서 확장을 사용 하도록 설정 합니다.
```console
azdata arc postgres server edit -n <name of your postgresql server group> --extensions <extension names>
```

#### <a name="get-the-list-of-extensions-enabled"></a>사용 하도록 설정 된 확장 목록을 가져옵니다.
다음 명령 중 하나를 실행 합니다.

##### <a name="with-azdata"></a>Azdata 사용
```console
azdata arc postgres server show -n <server group name>
```
출력에서 스크롤하고 서버 그룹의 사양에 engine\extensions 섹션이 있는지 확인 합니다. 예를 들면 다음과 같습니다.
```console
"engine": {
      "extensions": [
        {
          "name": "citus"
        },
        {
          "name": "pg_cron"
        }
      ]
    },
```
##### <a name="with-kubectl"></a>Kubectl 사용
```console
kubectl describe postgresql-12s/postgres02
```
출력에서 스크롤하고 서버 그룹의 사양에 engine\extensions 섹션이 있는지 확인 합니다. 예를 들면 다음과 같습니다.
```console
Engine:
    Extensions:
      Name:  citus
      Name:  pg_cron
```


### <a name="create-extensions"></a>확장 만들기:
선택한 클라이언트 도구를 사용 하 여 서버 그룹에 연결 하 고 표준 PostgreSQL 쿼리를 실행 합니다.
```console
CREATE EXTENSION <extension name>;
```

### <a name="get-the-list-of-extension-created-in-your-server-group"></a>서버 그룹에 생성 된 확장 프로그램 목록을 가져옵니다.
선택한 클라이언트 도구를 사용 하 여 서버 그룹에 연결 하 고 표준 PostgreSQL 쿼리를 실행 합니다.
```console
select * from pg_extension;
```

### <a name="drop-an-extension-from-your-server-group"></a>서버 그룹에서 확장을 삭제 합니다.
선택한 클라이언트 도구를 사용 하 여 서버 그룹에 연결 하 고 표준 PostgreSQL 쿼리를 실행 합니다.
```console
drop extension <extension name>;
```

## <a name="use-the-postgis-and-the-pg_cron-extensions"></a>PostGIS 및 Pg_cron 확장 사용

### <a name="the-postgis-extension"></a>PostGIS 확장

기존 서버 그룹에서 PostGIS 확장을 사용 하도록 설정 하거나 이미 사용 하도록 설정 된 확장을 사용 하 여 새 항목을 만들 수 있습니다.

**서버 그룹을 만들 때 확장을 사용 하도록 설정:**
```console
azdata arc postgres server create -n <name of your postgresql server group> --extensions <extension names>

#Example:
azdata arc postgres server create -n pg2 -w 2 --extensions postgis
```

**이미 존재 하는 인스턴스에서 확장을 사용 하도록 설정:**
```console
azdata arc postgres server edit -n <name of your postgresql server group> --extensions <extension names>

#Example:
azdata arc postgres server edit --extensions postgis -n pg2
```

설치 된 확장을 확인 하려면 Azure Data Studio와 같이 즐겨 사용 하는 PostgreSQL 클라이언트 도구를 사용 하 여 인스턴스에 연결한 후 아래 표준 PostgreSQL 명령을 사용 합니다.
```console
select * from pg_extension;
```

PostGIS 예의 경우 먼저 MIT의 도시 연구 & 계획에서 [샘플 데이터](http://duspviz.mit.edu/tutorials/intro-postgis/) 를 가져옵니다. `apt-get install unzip`테스트를 위해 VM을 사용할 때 압축 풀기를 설치 하려면를 실행 해야 할 수 있습니다.

```console
wget http://duspviz.mit.edu/_assets/data/intro-postgis-datasets.zip
unzip intro-postgis-datasets.zip
```

데이터베이스에 연결 하 고 PostGIS 확장을 만듭니다.

```console
CREATE EXTENSION postgis;
```

> [!NOTE]
> 패키지의 확장 중 하나를 사용 하려면 `postgis` (예:, `postgis_raster` `postgis_topology` `postgis_sfcgal` , `fuzzystrmatch` ...) 먼저 postgis 확장을 만든 다음 다른 확장을 만들어야 합니다. 예: `CREATE EXTENSION postgis` ; `CREATE EXTENSION postgis_raster` ;

스키마를 만듭니다.

```sql
CREATE TABLE coffee_shops (
  id serial NOT NULL,
  name character varying(50),
  address character varying(50),
  city character varying(50),
  state character varying(50),
  zip character varying(10),
  lat numeric,
  lon numeric,
  geom geometry(POINT,4326)
);
CREATE INDEX coffee_shops_gist ON coffee_shops USING gist (geom);
```

이제 coffee_shops 테이블을 분산 하 여 PostGIS를 scale out 기능과 결합할 수 있습니다.

```sql
SELECT create_distributed_table('coffee_shops', 'id');
```

일부 데이터를 로드 해 보겠습니다.

```console
\copy coffee_shops(id,name,address,city,state,zip,lat,lon) from cambridge_coffee_shops.csv CSV HEADER;
```

그런 다음 `geom` PostGIS 데이터 형식의 올바르게 인코딩된 위도 및 경도로 필드를 채웁니다 `geometry` .

```sql
UPDATE coffee_shops SET geom = ST_SetSRID(ST_MakePoint(lon,lat),4326);
```

이제 MIT (77 Massachusetts Ave at 42.359055,-71.093500)에 가장 가까운 커피숍을 나열할 수 있습니다.

```sql
SELECT name, address FROM coffee_shops ORDER BY geom <-> ST_SetSRID(ST_MakePoint(-71.093500,42.359055),4326);
```


### <a name="the-pg_cron-extension"></a>Pg_cron 확장

`pg_cron`PostgreSQL 서버 그룹 뿐만 아니라 PostGIS도 사용 하도록 하겠습니다.

```console
azdata postgres server update -n pg2 -ns arc --extensions postgis,pg_cron
```

이는 노드를 다시 시작 하 고 추가 확장을 설치 합니다 .이는 2-3 분 정도 걸릴 수 있습니다.

이제 다시 연결 하 여 확장을 만들 수 있습니다 `pg_cron` .

```sql
CREATE EXTENSION pg_cron;
```

테스트를 위해를 사용 하면 앞의 `the_best_coffee_shop` 테이블에서 임의의 이름을 사용 하는 테이블을 만들고 `coffee_shops` 테이블 내용을 설정 합니다.

```sql
CREATE TABLE the_best_coffee_shop(name text);
```

`cron.schedule`뿐만 아니라 몇 가지 SQL 문을 사용 하 여 임의의 테이블 이름을 가져오고 (분산 쿼리 결과를 저장 하는 임시 테이블을 사용 하는 것을 볼 수 있음)에 저장할 수 있습니다 `the_best_coffee_shop` .

```sql
SELECT cron.schedule('* * * * *', $$
  TRUNCATE the_best_coffee_shop;
  CREATE TEMPORARY TABLE tmp AS SELECT name FROM coffee_shops ORDER BY random() LIMIT 1;
  INSERT INTO the_best_coffee_shop SELECT * FROM tmp;
  DROP TABLE tmp;
$$);
```

이제 1 분에 한 번은 다른 이름을 가져옵니다.

```sql
SELECT * FROM the_best_coffee_shop;
```

```console
      name
-----------------
 B & B Snack Bar
(1 row)
```

구문에 대 한 자세한 내용은 [PG_CRON 추가](https://github.com/citusdata/pg_cron) 정보를 참조 하세요.

>[!NOTE]
>확장을 삭제 하는 것은 지원 되지 않습니다 `citus` . 확장은 하이퍼 `citus` 규모 환경을 제공 하기 위해 필요 합니다.

## <a name="next-steps"></a>다음 단계:
- [Plv8](https://plv8.github.io/) 설명서 읽기
- [Postgis](https://postgis.net/) 에서 설명서 읽기
- 설명서 읽기 [`pg_cron`](https://github.com/citusdata/pg_cron)
