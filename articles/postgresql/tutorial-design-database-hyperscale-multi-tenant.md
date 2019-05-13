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
ms.date: 05/06/2019
ms.openlocfilehash: b135baf73e21cd524b6e8fad35452362f36cf0c0
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65079548"
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

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

[Azure Portal](https://portal.azure.com)에 로그인합니다.

## <a name="create-an-azure-database-for-postgresql"></a>PostgreSQL용 Azure Database 만들기

다음 단계에 따라 Azure Database for PostgreSQL 서버를 만듭니다.
1. Azure Portal의 왼쪽 위 모서리에서 **리소스 만들기**를 클릭합니다.
2. **새로 만들기** 페이지에서 **데이터베이스**를 선택하고, **데이터베이스** 페이지에서 **PostgreSQL용 Azure Database**를 선택합니다.
3. 배포 옵션을 보려면 **하이퍼스케일(Citus) 서버 그룹 - 미리 보기** 아래에서 **만들기** 단추를 클릭합니다.
4. 새 서버 세부 정보 양식을 다음 정보로 작성합니다.
   - 리소스 그룹: 이 필드의 텍스트 상자 아래에 있는 **새로 만들기** 링크를 클릭합니다. **myresourcegroup**과 같은 이름을 입력합니다.
   - 서버 그룹 이름: 새 서버 그룹에 대한 고유한 이름을 입력합니다. 이 이름은 서버 하위 도메인에도 사용됩니다.
   - 관리 사용자 이름: 고유한 사용자 이름을 입력합니다. 이 이름은 나중에 데이터베이스에 연결하는 데 사용됩니다.
   - 암호: 8자 이상이어야 하며, 영어 대문자, 영어 소문자, 숫자(0-9) 및 영숫자가 아닌 문자(!, $, #, % 등) 중 세 가지 범주의 문자를 포함해야 합니다.
   - 위치: 데이터에 가장 빠르게 액세스할 수 있도록 사용자와 가장 가까운 위치를 사용합니다.

   > [!IMPORTANT]
   > 여기에 지정하는 서버 관리자 로그인 및 암호는 이 자습서의 뒷부분에서 서버 및 해당 데이터베이스에 로그인하는 데 필요합니다. 나중에 사용하기 위해 이 정보를 기억하거나 기록합니다.

5. **서버 그룹 구성**을 클릭합니다. 해당 섹션의 설정을 변경하지 않고 **저장**을 클릭합니다.
6. **검토 + 만들기**를 클릭한 다음, **만들기**를 클릭하여 서버를 프로비저닝합니다. 프로비전하는 데 몇 분이 걸립니다.
7. 배포를 모니터링하기 위해 페이지가 리디렉션됩니다. 라이브 상태가 **배포 진행 중**에서 **배포가 완료됨**으로 변경되면 페이지의 왼쪽에 있는 **출력** 메뉴 항목을 클릭합니다.
8. 출력 페이지에는 값을 클립보드로 복사하기 위한 단추 옆에 코디네이터 호스트 이름이 있습니다. 나중에 사용할 수 있도록 이 정보를 적어 두세요.

## <a name="configure-a-server-level-firewall-rule"></a>서버 수준 방화벽 규칙 구성

Azure Database for PostgreSQL 서비스는 서버 수준 방화벽을 사용합니다. 기본적으로 방화벽은 서버와 서버의 데이터베이스에 모든 외부 애플리케이션 및 도구가 연결되지 않도록 합니다. 특정 IP 주소 범위에 대해 방화벽을 여는 규칙을 추가해야 합니다.

1. 앞에서 코디네이터 노드 호스트 이름을 복사한 **출력** 섹션에서 **개요** 메뉴 항목을 다시 클릭합니다.

2. 리소스 목록에서 배포에 대한 크기 조정 그룹을 찾아 클릭합니다. (이름 앞에 "sg-" 접두사가 있습니다.)

3. 왼쪽 메뉴의 **보안** 아래에서 **방화벽**을 클릭합니다.

4. **+ 현재 클라이언트 IP 주소에 대한 방화벽 규칙 추가** 링크를 클릭합니다. 마지막으로 **저장** 단추를 클릭합니다.

5. **저장**을 클릭합니다.

   > [!NOTE]
   > Azure PostgreSQL 서버는 5432 포트를 통해 통신합니다. 회사 네트워크 내에서 연결하려는 경우 5432 포트를 통한 아웃바운드 트래픽이 네트워크 방화벽에서 허용되지 않을 수 있습니다. 이 경우 IT 부서에서 포트 5432를 열지 않으면 Azure SQL Database 서버에 연결할 수 없습니다.
   >

## <a name="connect-to-the-database-using-psql-in-cloud-shell"></a>Cloud Shell에서 psql을 사용하여 데이터베이스에 연결

이제 [psql](https://www.postgresql.org/docs/current/app-psql.html) 명령줄 유틸리티를 사용하여 Azure Database for PostgreSQL 서버에 연결해 보겠습니다.
1. 위쪽 탐색 창의 터미널 아이콘을 통해 Azure Cloud Shell을 시작합니다.

   ![PostgreSQL용 Azure Database - Azure Cloud Shell 터미널 아이콘](./media/tutorial-design-database-hyperscale-multi-tenant/psql-cloud-shell.png)

2. 브라우저에서 bash 명령을 입력할 수 있는 Azure Cloud Shell이 열립니다.

   ![PostgreSQL용 Azure Database - Azure Shell Bash 프롬프트](./media/tutorial-design-database-hyperscale-multi-tenant/psql-bash.png)

3. Cloud Shell 프롬프트에서 psql 명령을 사용하여 PostgreSQL용 Azure Database 서버에 연결합니다. 다음 형식은 [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html) 유틸리티를 사용하여 Azure Database for PostgreSQL 서버에 연결하는 데 사용됩니다.
   ```bash
   psql --host=<myserver> --username=myadmin --dbname=citus
   ```

   예를 들어 다음 명령은 액세스 자격 증명을 사용하여 PostgreSQL 서버(**mydemoserver.postgres.database.azure.com**)의 **citus**라는 기본 데이터베이스에 연결합니다. 메시지가 표시되면 서버 관리자 암호를 입력합니다.

   ```bash
   psql --host=mydemoserver.postgres.database.azure.com --username=myadmin --dbname=citus
   ```

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

공유 지리적 정보를 보관할 테이블을 만듭니다. psql에서 다음을 실행합니다.

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

예제 데이터가 있는 테이블을 로드합니다. psql에서 데이터 세트를 다운로드한 디렉터리 내에서 다음을 실행해야 합니다.

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

이전 단계에서는 Azure 리소스를 서버 그룹에 만들었습니다. 나중에 이러한 리소스가 필요하지 않으면 서버 그룹을 삭제합니다. 서버 그룹에 대한 *개요*페이지에서 *삭제* 단추를 누릅니다. 팝업 페이지에서 메시지가 표시되면 서버 그룹 이름을 확인하고 마지막 *삭제* 단추를 클릭합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 하이퍼스케일(Citus) 서버 그룹을 프로비저닝하는 방법을 배웠습니다. psql을 사용하여 연결하고, 스키마를 만들고, 데이터를 배포했습니다. 테넌트 내 또는 테넌트 간에 데이터를 쿼리하고, 스키마를 테넌트별로 사용자 지정하는 방법을 알아보았습니다.

다음으로 하이퍼스케일의 개념에 대해 알아봅니다.
> [!div class="nextstepaction"]
> [하이퍼스케일 노드 유형](https://aka.ms/hyperscale-concepts)
