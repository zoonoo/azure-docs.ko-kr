---
title: Azure Database for PostgreSQL - 하이퍼스케일(Citus)(미리 보기)을 사용하여 실시간 대시보드 설계 자습서
description: 이 자습서에서는 Azure Database for PostgreSQL 하이퍼스케일(Citus)(미리 보기)에서 분산된 테이블을 만들고, 채우고, 쿼리하는 방법을 보여줍니다.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.topic: tutorial
ms.date: 05/06/2019
ms.openlocfilehash: 7324ab1d7aa6e42100c9c6760c17b0ea6445f21d
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65079458"
---
# <a name="tutorial-design-a-real-time-analytics-dashboard-by-using-azure-database-for-postgresql--hyperscale-citus-preview"></a>자습서: Azure Database for PostgreSQL - 하이퍼스케일(Citus)(미리 보기)을 사용하여 실시간 분석 대시보드 설계

이 자습서에서는 Azure Database for PostgreSQL – 하이퍼스케일(Citus)(미리 보기)을 사용하여 다음 작업을 수행하는 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 하이퍼스케일(Citus) 서버 그룹 만들기
> * psql 유틸리티를 사용하여 스키마 만들기
> * 노드 간 테이블 분할
> * 샘플 데이터 생성
> * 롤업 수행
> * 원시 및 집계 데이터 쿼리
> * 데이터 만료

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
   - 서버 그룹 이름: **mydemoserver**. DNS 이름에 매핑되는 서버 이름이며 전역적으로 고유해야 합니다.
   - 관리 사용자 이름: **myadmin**. 나중에 데이터베이스에 연결할 때 사용됩니다.
   - 암호: 8자 이상이어야 하며, 영어 대문자, 영어 소문자, 숫자(0-9) 및 영숫자가 아닌 문자(!, $, #, % 등) 중 세 가지 범주의 문자를 포함해야 합니다.
   - 위치: 데이터에 가장 빠르게 액세스할 수 있도록 사용자와 가장 가까운 위치를 사용합니다.

   > [!IMPORTANT]
   > 여기에 지정하는 서버 관리자 로그인 및 암호는 이 자습서의 뒷부분에서 서버 및 해당 데이터베이스에 로그인하는 데 필요합니다. 나중에 사용하기 위해 이 정보를 기억하거나 기록합니다.

5. **서버 그룹 구성**을 클릭합니다. 이 섹션의 설정을 변경하지 않고 **저장**을 클릭합니다.
6. **검토 + 만들기**를 클릭한 다음, **만들기**를 클릭하여 서버를 프로비저닝합니다. 프로비전하는 데 몇 분이 걸립니다.
7. 배포를 모니터링하도록 페이지가 리디렉션됩니다. 실시간 상태가 **배포 진행 중**에서 **배포가 완료됨**으로 바뀌면 페이지 왼쪽에서 **출력** 메뉴 항목을 클릭합니다.
8. 출력 페이지에는 코디네이터 호스트 이름이 포함되어 있고, 그 옆에는 이 값을 클립보드에 복사하는 단추가 있습니다. 나중에 사용할 수 있도록 이 정보를 기록해 둡니다.

## <a name="configure-a-server-level-firewall-rule"></a>서버 수준 방화벽 규칙 구성

Azure Database for PostgreSQL 서비스는 서버 수준 방화벽을 사용합니다. 기본적으로 방화벽은 모든 외부 애플리케이션 및 도구가 서버 및 서버의 데이터베이스에 연결하지 못하게 차단합니다. 특정 IP 주소 범위에 대해서는 방화벽을 여는 규칙을 추가해야 합니다.

1. 이전에 코디네이터 노드 호스트 이름을 복사한 **출력** 섹션에서 **개요** 메뉴 항목을 다시 클릭합니다.

2. 리소스 목록에서 배포의 크기 조정 그룹을 찾아 클릭합니다. 이름에 접두사 "sg-"가 붙어 있습니다.

3. 왼쪽 메뉴의 **보안** 아래에서 **방화벽**을 클릭합니다.

4. **+ 현재 클라이언트 IP 주소에 대한 방화벽 규칙 추가** 링크를 클릭합니다. 마지막으로, **저장** 단추를 클릭합니다.

5. **저장**을 클릭합니다.

   > [!NOTE]
   > Azure PostgreSQL 서버는 5432 포트를 통해 통신합니다. 회사 네트워크 내에서 연결하려는 경우 5432 포트를 통한 아웃바운드 트래픽이 네트워크 방화벽에서 허용되지 않을 수 있습니다. 이 경우 IT 부서에서 포트 5432를 열지 않으면 Azure SQL Database 서버에 연결할 수 없습니다.
   >

## <a name="connect-to-the-database-using-psql-in-cloud-shell"></a>Cloud Shell에서 psql을 사용하여 데이터베이스에 연결

이제 [psql](https://www.postgresql.org/docs/current/app-psql.html) 명령줄 유틸리티를 사용하여 Azure Database for PostgreSQL 서버에 연결해 보겠습니다.
1. 위쪽 탐색 창의 터미널 아이콘을 통해 Azure Cloud Shell을 시작합니다.

   ![PostgreSQL용 Azure Database - Azure Cloud Shell 터미널 아이콘](./media/tutorial-design-database-hyperscale-realtime/psql-cloud-shell.png)

2. 브라우저에서 bash 명령을 입력할 수 있는 Azure Cloud Shell이 열립니다.

   ![PostgreSQL용 Azure Database - Azure Shell Bash 프롬프트](./media/tutorial-design-database-hyperscale-realtime/psql-bash.png)

3. Cloud Shell 프롬프트에서 psql 명령을 사용하여 PostgreSQL용 Azure Database 서버에 연결합니다. 다음 형식은 [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html) 유틸리티를 사용하여 Azure Database for PostgreSQL 서버에 연결하는 데 사용됩니다.
   ```bash
   psql --host=<myserver> --username=myadmin --dbname=citus
   ```

   예를 들어 다음 명령은 액세스 자격 증명을 사용하여 **mydemoserver.postgres.database.azure.com** PostgreSQL 서버의 **citus**라는 기본 데이터베이스에 연결합니다. 메시지가 표시되면 서버 관리자 암호를 입력합니다.

   ```bash
   psql --host=mydemoserver.postgres.database.azure.com --username=myadmin --dbname=citus
   ```

## <a name="use-psql-utility-to-create-a-schema"></a>psql 유틸리티를 사용하여 스키마 만들기

psql을 사용하여 Azure Database for PostgreSQL - 하이퍼스케일(Citus)(미리 보기)에 연결되면 몇 가지 기본 작업을 완료할 수 있습니다. 이 자습서에서는 웹 분석의 트래픽 데이터를 수집한 다음, 데이터를 롤업하여 해당 데이터를 기반으로 실시간 대시보드를 제공하는 방법을 설명합니다.

모든 원시 웹 트래픽 데이터를 사용할 테이블을 만들겠습니다. psql 터미널에서 다음 명령을 실행합니다.

```sql
CREATE TABLE http_request (
  site_id INT,
  ingest_time TIMESTAMPTZ DEFAULT now(),

  url TEXT,
  request_country TEXT,
  ip_address TEXT,

  status_code INT,
  response_time_msec INT
);
```

또한 분 단위 집계를 보관할 테이블과 마지막 롤업의 위치를 유지하는 테이블을 만들겠습니다. psql에서 다음 명령도 실행합니다.

```sql
CREATE TABLE http_request_1min (
  site_id INT,
  ingest_time TIMESTAMPTZ, -- which minute this row represents

  error_count INT,
  success_count INT,
  request_count INT,
  average_response_time_msec INT,
  CHECK (request_count = error_count + success_count),
  CHECK (ingest_time = date_trunc('minute', ingest_time))
);

CREATE INDEX http_request_1min_idx ON http_request_1min (site_id, ingest_time);

CREATE TABLE latest_rollup (
  minute timestamptz PRIMARY KEY,

  CHECK (minute = date_trunc('minute', minute))
);
```

이제 이 psql 명령을 사용하면 테이블 목록에서 새로 만든 테이블을 볼 수 있습니다.

```postgres
\dt
```

## <a name="shard-tables-across-nodes"></a>노드 간 테이블 분할

하이퍼스케일 배포는 사용자가 지정한 열 값을 기반으로 다른 노드에 테이블 행을 저장합니다. 이 "배포 열"은 노드 간에 데이터를 분할하는 방법을 표시합니다.

배포 열을 분할 키인 site\_id로 설정해 보겠습니다. psql에서 다음 함수를 실행합니다.

  ```sql
SELECT create_distributed_table('http_request',      'site_id');
SELECT create_distributed_table('http_request_1min', 'site_id');
```

## <a name="generate-sample-data"></a>샘플 데이터 생성

이제 서버 그룹이 데이터를 수집할 준비가 완료되었습니다. `psql` 연결에서 로컬로 다음 명령을 실행하여 지속적으로 데이터를 삽입할 수 있습니다.

```sql
DO $$
  BEGIN LOOP
    INSERT INTO http_request (
      site_id, ingest_time, url, request_country,
      ip_address, status_code, response_time_msec
    ) VALUES (
      trunc(random()*32), clock_timestamp(),
      concat('http://example.com/', md5(random()::text)),
      ('{China,India,USA,Indonesia}'::text[])[ceil(random()*4)],
      concat(
        trunc(random()*250 + 2), '.',
        trunc(random()*250 + 2), '.',
        trunc(random()*250 + 2), '.',
        trunc(random()*250 + 2)
      )::inet,
      ('{200,404}'::int[])[ceil(random()*2)],
      5+trunc(random()*150)
    );
    PERFORM pg_sleep(random() * 0.25);
  END LOOP;
END $$;
```

이 쿼리는 약 0.25초마다 행을 추가합니다. 행은 배포 열 `site_id`의 지시에 따라 다른 작업자 노드에 저장됩니다.

   > [!NOTE]
   > 데이터 생성 쿼리가 계속 실행되도록 그대로 두고, 이 자습서의 나머지 명령에 대한 두 번째 psql 연결을 엽니다.
   >

## <a name="query"></a>쿼리

하이퍼스케일 호스팅 옵션을 사용하면 여러 노드가 쿼리를 병렬로 처리하여 속도를 높일 수 있습니다. 예를 들어 데이터베이스는 작업자 노드에서 SUM 및 COUNT 같은 집계를 계산하고, 그 결과를 최종 대답에 결합합니다.

다음은 몇 가지 통계와 함께 분당 웹 요청 수를 집계하는 쿼리입니다.
psql에서 이 쿼리를 실행하고 결과를 관찰해보세요.

```sql
SELECT
  site_id,
  date_trunc('minute', ingest_time) as minute,
  COUNT(1) AS request_count,
  SUM(CASE WHEN (status_code between 200 and 299) THEN 1 ELSE 0 END) as success_count,
  SUM(CASE WHEN (status_code between 200 and 299) THEN 0 ELSE 1 END) as error_count,
  SUM(response_time_msec) / COUNT(1) AS average_response_time_msec
FROM http_request
WHERE date_trunc('minute', ingest_time) > now() - '5 minutes'::interval
GROUP BY site_id, minute
ORDER BY minute ASC;
```

## <a name="performing-rollups"></a>롤업 수행

위의 쿼리는 초기 단계에서는 제대로 작동하지만, 데이터가 커지면 성능이 저하됩니다. 분산 처리를 사용하더라도 이 데이터를 반복적으로 다시 계산하는 것보다는 미리 컴퓨팅하는 것이 더 빠릅니다.

원시 데이터를 주기적으로 집계 테이블에 롤링하여 대시보드 속도를 높게 유지할 수 있습니다. 이 예에서는 1분 집계 테이블을 롤업하겠지만 5분, 15분, 1시간 등으로 집계할 수도 있습니다.

이 롤업을 지속적으로 실행할 것이므로 롤업을 수행하는 함수를 만들겠습니다. psql에서 다음 명령을 실행하여 `rollup_http_request` 함수를 만듭니다.

```sql
-- initialize to a time long ago
INSERT INTO latest_rollup VALUES ('10-10-1901');

-- function to do the rollup
CREATE OR REPLACE FUNCTION rollup_http_request() RETURNS void AS $$
DECLARE
  curr_rollup_time timestamptz := date_trunc('minute', now());
  last_rollup_time timestamptz := minute from latest_rollup;
BEGIN
  INSERT INTO http_request_1min (
    site_id, ingest_time, request_count,
    success_count, error_count, average_response_time_msec
  ) SELECT
    site_id,
    date_trunc('minute', ingest_time),
    COUNT(1) as request_count,
    SUM(CASE WHEN (status_code between 200 and 299) THEN 1 ELSE 0 END) as success_count,
    SUM(CASE WHEN (status_code between 200 and 299) THEN 0 ELSE 1 END) as error_count,
    SUM(response_time_msec) / COUNT(1) AS average_response_time_msec
  FROM http_request
  -- roll up only data new since last_rollup_time
  WHERE date_trunc('minute', ingest_time) <@
          tstzrange(last_rollup_time, curr_rollup_time, '(]')
  GROUP BY 1, 2;

  -- update the value in latest_rollup so that next time we run the
  -- rollup it will operate on data newer than curr_rollup_time
  UPDATE latest_rollup SET minute = curr_rollup_time;
END;
$$ LANGUAGE plpgsql;
```

함수가 준비되면 다음과 같이 함수를 실행하여 데이터를 롤업합니다.

```sql
SELECT rollup_http_request();
```

데이터가 미리 집계된 형태로 되면 롤업 테이블을 쿼리하여 이전과 동일한 보고서를 얻을 수 있습니다. 다음을 실행합니다.

```sql
SELECT site_id, ingest_time as minute, request_count,
       success_count, error_count, average_response_time_msec
  FROM http_request_1min
 WHERE ingest_time > date_trunc('minute', now()) - '5 minutes'::interval;
 ```

## <a name="expiring-old-data"></a>오래된 데이터 만료

롤업으로 쿼리 속도가 좀 더 빨라지겠지만, 불필요한 스토리지 비용이 발생하지 않도록 오래된 데이터를 만료해야 합니다. 간단하게 각 세분성에서 데이터를 유지할 기간을 결정하고, 표준 쿼리를 사용하여 만료된 데이터를 삭제하면 됩니다. 다음 예제에서는 원시 데이터를 하루 동안 유지하고, 분 단위 집계를 한 달 동안 유지하기로 결정했습니다.

```sql
DELETE FROM http_request WHERE ingest_time < now() - interval '1 day';
DELETE FROM http_request_1min WHERE ingest_time < now() - interval '1 month';
```

프로덕션 환경에서는 이러한 쿼리를 함수에 래핑하고 cron 작업에서 1분마다 호출할 수 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

이전 단계에서 리소스 그룹에 Azure 리소스를 만들었습니다. 이러한 리소스가 더 이상 필요 없으면 서버 그룹을 삭제합니다. 서버 그룹의 *개요* 페이지에서 *삭제* 단추를 누릅니다. 팝업 페이지에 메시지가 표시되면 서버 그룹의 이름을 확인하고 최종 *삭제* 단추를 클릭합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 하이퍼스케일(Citus) 서버 그룹을 프로비저닝하는 방법을 배웠습니다. 그 후 psql을 사용하여 이 서버 그룹에 연결하고, 스키마를 만들고, 데이터를 분산했습니다. 데이터를 원시 형식으로 쿼리하는 방법과 데이터를 주기적으로 집계하고, 집계된 테이블을 쿼리하고, 오래된 데이터를 만료하는 방법을 알아보았습니다.

다음으로, 하이퍼스케일의 개념을 알아보세요.
> [!div class="nextstepaction"]
> [하이퍼스케일 노드 형식](https://aka.ms/hyperscale-concepts)