---
title: Azure Database for PostgreSQL – 하이퍼스케일(Citus)(미리 보기)을 사용하여 다중 테넌트 데이터베이스 설계 자습서
description: 이 자습서에서는 Azure Database for PostgreSQL 하이퍼스케일(Citus)(미리 보기)에서 분산된 테이블을 만들고, 채우고, 쿼리하는 방법을 보여줍니다.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 05/14/2019
ms.openlocfilehash: 73d7aebf3dbff59320e0ef92cbd54811503c71b4
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2019
ms.locfileid: "65792264"
---
# <a name="tutorial-design-a-multi-tenant-database-by-using-azure-database-for-postgresql--hyperscale-citus-preview"></a>자습서: Azure Database for PostgreSQL – 하이퍼스케일(Citus)(미리 보기)을 사용하여 다중 테넌트 데이터베이스 설계

이 자습서에서는 Azure Database for PostgreSQL – 하이퍼스케일(Citus)(미리 보기)을 사용하여 다음 작업을 수행하는 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 하이퍼스케일(Citus) 서버 그룹 만들기
> * psql 유틸리티를 사용하여 스키마 만들기
> * 노드 간 테이블 분할
> * 샘플 데이터 수집
> * 테넌트 데이터 쿼리
> * 테넌트 간 데이터 공유
> * 테넌트별 스키마 사용자 지정

## <a name="prerequisites"></a>필수 조건

[!INCLUDE [azure-postgresql-hyperscale-create-db](../../includes/azure-postgresql-hyperscale-create-db.md)]

## <a name="use-psql-utility-to-create-a-schema"></a>psql 유틸리티를 사용하여 스키마 만들기

psql을 사용하여 Azure Database for PostgreSQL - 하이퍼스케일(Citus)(미리 보기)에 연결되면 몇 가지 기본 작업을 완료할 수 있습니다. 이 자습서에서는 광고주가 자신의 캠페인을 추적할 수 있는 웹앱을 만드는 과정을 안내합니다.

여러 회사에서 이 앱을 사용할 수 있으므로 회사를 보관할 테이블과 자신의 캠페인을 보관할 다른 테이블을 만들어 보겠습니다. psql 콘솔에서 다음 명령을 실행합니다.

```sql
CREATE TABLE companies (
  id bigserial PRIMARY KEY,
  name text NOT NULL,
  image_url text,
  created_at timestamp without time zone NOT NULL,
  updated_at timestamp without time zone NOT NULL
);

CREATE TABLE campaigns (
  id bigserial,
  company_id bigint REFERENCES companies (id),
  name text NOT NULL,
  cost_model text NOT NULL,
  state text NOT NULL,
  monthly_budget bigint,
  blacklisted_site_urls text[],
  created_at timestamp without time zone NOT NULL,
  updated_at timestamp without time zone NOT NULL,

  PRIMARY KEY (company_id, id)
);
```

각 캠페인에는 광고를 실행하기 위한 비용이 지불됩니다. psql에서 위의 코드 뒤에 다음 코드를 실행하여 광고에 대한 테이블도 추가합니다.

```sql
CREATE TABLE ads (
  id bigserial,
  company_id bigint,
  campaign_id bigint,
  name text NOT NULL,
  image_url text,
  target_url text,
  impressions_count bigint DEFAULT 0,
  clicks_count bigint DEFAULT 0,
  created_at timestamp without time zone NOT NULL,
  updated_at timestamp without time zone NOT NULL,

  PRIMARY KEY (company_id, id),
  FOREIGN KEY (company_id, campaign_id)
    REFERENCES campaigns (company_id, id)
);
```

마지막으로, 각 광고에 대한 클릭 횟수와 인상에 대한 통계를 추적합니다.

```sql
CREATE TABLE clicks (
  id bigserial,
  company_id bigint,
  ad_id bigint,
  clicked_at timestamp without time zone NOT NULL,
  site_url text NOT NULL,
  cost_per_click_usd numeric(20,10),
  user_ip inet NOT NULL,
  user_data jsonb NOT NULL,

  PRIMARY KEY (company_id, id),
  FOREIGN KEY (company_id, ad_id)
    REFERENCES ads (company_id, id)
);

CREATE TABLE impressions (
  id bigserial,
  company_id bigint,
  ad_id bigint,
  seen_at timestamp without time zone NOT NULL,
  site_url text NOT NULL,
  cost_per_impression_usd numeric(20,10),
  user_ip inet NOT NULL,
  user_data jsonb NOT NULL,

  PRIMARY KEY (company_id, id),
  FOREIGN KEY (company_id, ad_id)
    REFERENCES ads (company_id, id)
);
```

다음을 실행하여 psql의 테이블 목록에서 새로 만든 테이블을 볼 수 있습니다.

```postgres
\dt
```

다중 테넌트 애플리케이션은 테넌트별로만 고유성을 적용할 수 있으므로 모든 기본 키와 외래 키에는 회사 ID가 포함됩니다.

## <a name="shard-tables-across-nodes"></a>노드 간 테이블 분할

하이퍼스케일 배포에서는 사용자 지정 열의 값에 따라 테이블 행이 다른 노드에 저장됩니다. 이 "배포 열"은 각 테넌트에서 소유하고 있는 열을 나타냅니다.

배포 열을 테넌트 식별자인 company\_id로 설정해 보겠습니다. psql에서 다음 함수를 실행합니다.

```sql
SELECT create_distributed_table('companies',   'id');
SELECT create_distributed_table('campaigns',   'company_id');
SELECT create_distributed_table('ads',         'company_id');
SELECT create_distributed_table('clicks',      'company_id');
SELECT create_distributed_table('impressions', 'company_id');
```

## <a name="ingest-sample-data"></a>샘플 데이터 수집

이제 psql이 아닌 일반 명령줄에서 샘플 데이터 세트를 다운로드합니다.

```bash
for dataset in companies campaigns ads clicks impressions geo_ips; do
  curl -O https://examples.citusdata.com/mt_ref_arch/${dataset}.csv
done
```

psql로 다시 돌아가서 데이터를 대량으로 로드합니다. psql은 데이터 파일을 다운로드한 것과 동일한 디렉터리에서 실행해야 합니다.

```sql
\copy companies from 'companies.csv' with csv
\copy campaigns from 'campaigns.csv' with csv
\copy ads from 'ads.csv' with csv
\copy clicks from 'clicks.csv' with csv
\copy impressions from 'impressions.csv' with csv
```

이제 이 데이터는 작업자 노드에 분산됩니다.

## <a name="query-tenant-data"></a>테넌트 데이터 쿼리

애플리케이션에서 단일 테넌트에 대한 데이터를 요청하면 데이터베이스는 단일 작업자 노드에서 쿼리를 실행할 수 있습니다. 단일 테넌트 쿼리는 단일 테넌트 ID를 기준으로 필터링합니다. 예를 들어 다음 쿼리에서는 광고 및 인상에 대해 `company_id = 5`를 필터링합니다. 결과를 보려면 psql에서 실행해 보세요.

```sql
SELECT a.campaign_id,
       RANK() OVER (
         PARTITION BY a.campaign_id
         ORDER BY a.campaign_id, count(*) desc
       ), count(*) as n_impressions, a.id
  FROM ads as a
  JOIN impressions as i
    ON i.company_id = a.company_id
   AND i.ad_id      = a.id
 WHERE a.company_id = 5
GROUP BY a.campaign_id, a.id
ORDER BY a.campaign_id, n_impressions desc;
```

## <a name="share-data-between-tenants"></a>테넌트 간 데이터 공유

지금까지 모든 테이블이 `company_id`에 기반하여 배포되었지만, 일부 데이터는 당연히 특정 테넌트에 "속하지 않으며" 공유할 수 있습니다. 예를 들어 광고 예제 플랫폼에 있는 모든 회사에서 대상 그룹에 대해 IP 주소 기반의 지리적 정보를 얻으려고 할 수도 있습니다.

공유 지리적 정보를 보관할 테이블을 만듭니다. psql에서 다음 명령을 실행합니다.

```sql
CREATE TABLE geo_ips (
  addrs cidr NOT NULL PRIMARY KEY,
  latlon point NOT NULL
    CHECK (-90  <= latlon[0] AND latlon[0] <= 90 AND
           -180 <= latlon[1] AND latlon[1] <= 180)
);
CREATE INDEX ON geo_ips USING gist (addrs inet_ops);
```

다음으로 `geo_ips`를 "참조 테이블"로 만들어 모든 작업자 노드에 테이블 복사본을 저장합니다.

```sql
SELECT create_reference_table('geo_ips');
```

예제 데이터가 있는 테이블을 로드합니다. psql에서 데이터 세트를 다운로드한 디렉터리 내에서 다음 명령을 실행해야 합니다.

```sql
\copy geo_ips from 'geo_ips.csv' with csv
```

clicks 테이블을 geo\_ips와 조인하면 모든 노드에서 효율적입니다.
다음은 광고를 클릭한 모든 사람의 위치를 찾기 위한 조인입니다.
290. psql에서 쿼리를 실행해 보세요.

```sql
SELECT c.id, clicked_at, latlon
  FROM geo_ips, clicks c
 WHERE addrs >> c.user_ip
   AND c.company_id = 5
   AND c.ad_id = 290;
```

## <a name="customize-the-schema-per-tenant"></a>테넌트별 스키마 사용자 지정

각 테넌트는 다른 테넌트에 필요하지 않은 특수 정보를 저장해야 할 수도 있습니다. 그러나 모든 테넌트에서 동일한 데이터베이스 스키마를 사용하여 공용 인프라를 공유하고 있습니다. 추가 데이터는 어디에 배치할 수 있을까요?

한 가지 방법은 PostgreSQL의 JSONB와 같이 확장 가능한 열 형식을 사용하는 것입니다.  스키마의 `clicks`에는 `user_data`라는 JSONB 필드가 있습니다.
회사(예: 회사 ID가 5임)에서 이 열을 사용하여 사용자가 모바일 디바이스에 있는지 여부를 추적할 수 있습니다.

다음은 모바일 또는 기존 방문자 중 누가 더 많이 클릭하는지 확인하는 쿼리입니다.

```sql
SELECT
  user_data->>'is_mobile' AS is_mobile,
  count(*) AS count
FROM clicks
WHERE company_id = 5
GROUP BY user_data->>'is_mobile'
ORDER BY count DESC;
```

[부분 인덱스](https://www.postgresql.org/docs/current/static/indexes-partial.html)를 만들어 이 쿼리를 단일 회사에 대해 최적화할 수 있습니다.

```sql
CREATE INDEX click_user_data_is_mobile
ON clicks ((user_data->>'is_mobile'))
WHERE company_id = 5;
```

일반적으로 열 내의 모든 키와 값에 대해 [GIN 인덱스](https://www.postgresql.org/docs/current/static/gin-intro.html)를 만들 수 있습니다.

```sql
CREATE INDEX click_user_data
ON clicks USING gin (user_data);

-- this speeds up queries like, "which clicks have
-- the is_mobile key present in user_data?"

SELECT id
  FROM clicks
 WHERE user_data ? 'is_mobile'
   AND company_id = 5;
```

## <a name="clean-up-resources"></a>리소스 정리

이전 단계에서 리소스 그룹에 Azure 리소스를 만들었습니다. 이러한 리소스가 더 이상 필요 없으면 서버 그룹을 삭제합니다. 서버 그룹의 *개요* 페이지에서 *삭제* 단추를 누릅니다. 팝업 페이지에 메시지가 표시되면 서버 그룹의 이름을 확인하고 최종 *삭제* 단추를 클릭합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 하이퍼스케일(Citus) 서버 그룹을 프로비저닝하는 방법을 배웠습니다. psql을 사용하여 연결하고, 스키마를 만들고, 데이터를 배포했습니다. 테넌트 내 또는 테넌트 간에 데이터를 쿼리하고, 스키마를 테넌트별로 사용자 지정하는 방법을 알아보았습니다.

다음으로 하이퍼스케일의 개념에 대해 알아봅니다.
> [!div class="nextstepaction"]
> [하이퍼스케일 노드 형식](https://aka.ms/hyperscale-concepts)
