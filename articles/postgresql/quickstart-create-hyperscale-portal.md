---
title: Azure Database for PostgreSQL – 하이퍼스케일(Citus)(미리 보기) 빠른 시작
description: Azure Database for PostgreSQL 하이퍼스케일(Citus)(미리 보기)에서 분산된 테이블을 만들고 쿼리하는 빠른 시작입니다.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.topic: quickstart
ms.date: 05/06/2019
ms.openlocfilehash: 30de4da43569abf4d7bd668fd0fa481ecac23f4d
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65080031"
---
# <a name="quickstart-create-an-azure-database-for-postgresql---hyperscale-citus-preview-in-the-azure-portal"></a>빠른 시작: Azure Portal에서 Azure Database for PostgreSQL - 하이퍼스케일(Citus)(미리 보기)

PostgreSQL용 Azure Database는 클라우드에서 항상 사용 가능한 PostgreSQL 데이터베이스를 실행, 관리 및 크기 조정하는 데 사용하는 관리 서비스입니다. 이 빠른 시작에서는 Azure Portal을 사용하여 Azure Database for PostgreSQL - 하이퍼스케일(Citus)(미리 보기) 서버 그룹을 만드는 방법을 보여줍니다. 분산된 데이터를 살펴보겠습니다. 노드 간에 테이블을 분할하고, 샘플 데이터를 수집하고, 여러 노드에서 실행되는 쿼리를 실행할 것입니다.

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
   - 서버 그룹 이름: 새 서버 그룹의 고유한 이름을 입력합니다. 이 이름은 서버 하위 도메인에도 사용됩니다.
   - 관리 사용자 이름: 고유한 사용자 이름을 입력합니다. 이 이름은 나중에 데이터베이스에 연결하는 데 사용됩니다.
   - 암호: 8자 이상이어야 하며, 영어 대문자, 영어 소문자, 숫자(0-9) 및 영숫자가 아닌 문자(!, $, #, % 등) 중 세 가지 범주의 문자를 포함해야 합니다.
   - 위치: 데이터에 가장 빠르게 액세스할 수 있도록 사용자와 가장 가까운 위치를 사용합니다.

   > [!IMPORTANT]
   > 여기에 지정한 서버 관리자 로그인 및 암호는 이 빠른 시작의 뒷부분에 나오는 서버 및 데이터베이스에 로그인해야 합니다. 나중에 사용하기 위해 이 정보를 기억하거나 기록합니다.

5. **서버 그룹 구성**을 클릭합니다. 이 섹션의 설정을 변경하지 않고 **저장**을 클릭합니다.
6. **검토 + 만들기**를 클릭한 다음, **만들기**를 클릭하여 서버를 프로비저닝합니다. 프로비전하는 데 몇 분이 걸립니다.
7. 배포를 모니터링하도록 페이지가 리디렉션됩니다. 실시간 상태가 **배포 진행 중**에서 **배포가 완료됨**으로 바뀌면 페이지 왼쪽에서 **출력** 메뉴 항목을 클릭합니다.
8. 출력 페이지에는 코디네이터 호스트 이름이 포함되어 있고, 그 옆에는 이 값을 클립보드에 복사하는 단추가 있습니다. 나중에 사용할 수 있도록 이 정보를 기록해 둡니다.

## <a name="configure-a-server-level-firewall-rule"></a>서버 수준 방화벽 규칙 구성

Azure Database for PostgreSQL - 하이퍼스케일(Citus)(미리 보기) 서비스는 서버 수준 방화벽을 사용합니다. 기본적으로 방화벽은 모든 외부 애플리케이션 및 도구가 코디네이터 노드와 그 내부의 데이터베이스에 연결하지 못하게 차단합니다. 특정 IP 주소 범위에 대해서는 방화벽을 여는 규칙을 추가해야 합니다.

1. 이전에 코디네이터 노드 호스트 이름을 복사한 **출력** 섹션에서 **개요** 메뉴 항목을 다시 클릭합니다.

2. 배포의 크기 조정 그룹 이름에는 "sg-"라는 접두사가 붙습니다. 리소스 목록에서 이 이름을 찾아 클릭합니다.

3. 왼쪽 메뉴의 **보안** 아래에서 **방화벽**을 클릭합니다.

4. **+ 현재 클라이언트 IP 주소에 대한 방화벽 규칙 추가** 링크를 클릭합니다. 마지막으로, **저장** 단추를 클릭합니다.

5. **저장**을 클릭합니다.

   > [!NOTE]
   > Azure PostgreSQL 서버는 5432 포트를 통해 통신합니다. 회사 네트워크 내에서 연결하려는 경우 5432 포트를 통한 아웃바운드 트래픽이 네트워크 방화벽에서 허용되지 않을 수 있습니다. 이 경우 IT 부서에서 포트 5432를 열지 않으면 Azure SQL Database 서버에 연결할 수 없습니다.
   >

## <a name="connect-to-the-database-using-psql-in-cloud-shell"></a>Cloud Shell에서 psql을 사용하여 데이터베이스에 연결

이제 [psql](https://www.postgresql.org/docs/current/app-psql.html) 명령줄 유틸리티를 사용하여 Azure Database for PostgreSQL 서버에 연결해 보겠습니다.
1. 위쪽 탐색 창의 터미널 아이콘을 통해 Azure Cloud Shell을 시작합니다.

   ![PostgreSQL용 Azure Database - Azure Cloud Shell 터미널 아이콘](./media/quickstart-create-hyperscale-portal/psql-cloud-shell.png)

2. 브라우저에서 bash 명령을 입력할 수 있는 Azure Cloud Shell이 열립니다.

   ![PostgreSQL용 Azure Database - Azure Shell Bash 프롬프트](./media/quickstart-create-hyperscale-portal/psql-bash.png)

3. Cloud Shell 프롬프트에서 psql 명령을 사용하여 PostgreSQL용 Azure Database 서버에 연결합니다. 다음 형식은 [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html) 유틸리티를 사용하여 Azure Database for PostgreSQL 서버에 연결하는 데 사용됩니다.
   ```bash
   psql --host=<myserver> --username=myadmin --dbname=citus
   ```

   예를 들어 다음 명령은 액세스 자격 증명을 사용하여 **mydemoserver.postgres.database.azure.com** PostgreSQL 서버의 **citus**라는 기본 데이터베이스에 연결합니다. 메시지가 표시되면 서버 관리자 암호를 입력합니다.

   ```bash
   psql --host=mydemoserver.postgres.database.azure.com --username=myadmin --dbname=citus
   ```

## <a name="create-and-distribute-tables"></a>테이블 만들기 및 테이블 배포

psql을 사용하여 하이퍼스케일 코디네이터 노드에 연결되면 기본 작업을 완료할 수 있습니다.

하이퍼스케일 서버 내에는 다음과 같은 세 가지 유형의 테이블이 있습니다.

- 분산 또는 분할된 테이블(성능 및 병렬 처리를 향상하기 위해 분산)
- 참조 테이블(여러 복사본 유지)
- 로컬 테이블(종종 내부 관리 테이블에 사용)

이 빠른 시작에서는 분산 테이블을 집중적으로 살펴보고 익히도록 하겠습니다.

우리가 작업하려는 데이터 모델은 간단합니다. GitHub의 사용자 및 이벤트 데이터입니다. 이벤트로는 분기 만들기, 조직과 관련된 git 커밋 등이 있습니다.

psql을 통해 연결했으면 테이블을 만들어 보겠습니다. psql 콘솔에서 다음을 실행합니다.

```sql
CREATE TABLE github_events
(
    event_id bigint,
    event_type text,
    event_public boolean,
    repo_id bigint,
    payload jsonb,
    repo jsonb,
    user_id bigint,
    org jsonb,
    created_at timestamp
);

CREATE TABLE github_users
(
    user_id bigint,
    url text,
    login text,
    avatar_url text,
    gravatar_id text,
    display_login text
);
```

`github_events`의 `payload` 필드는 JSONB 데이터 형식을 갖습니다. JSONB는 Postgres의 이진 형태인 JSON 데이터 형식입니다. 따라서 보다 유연한 스키마를 단일 열에 쉽게 저장할 수 있습니다.

Postgres는 모든 키와 키 내부의 값을 인덱싱하는 이 유형의 `GIN` 인섹스를 만들 수 있습니다. 인덱스가 있으면 다양한 조건을 사용하여 쉽고 빠르게 페이로드를 쿼리할 수 있습니다. 데이터를 로드하기 전에 몇 가지 인덱스를 만들어 보겠습니다. psql에서 다음을 수행합니다.

```sql
CREATE INDEX event_type_index ON github_events (event_type);
CREATE INDEX payload_index ON github_events USING GIN (payload jsonb_path_ops);
```

다음으로, 코디네이터 노드의 이 Postgres 테이블을 가져오고, 이 테이블을 여러 작업 노드에 분할하라고 하이퍼스케일에 지시하겠습니다. 이렇게 하기 위해 각 테이블에 대해 테이블을 분할할 키를 지정하는 쿼리를 실행하겠습니다. 현재 예제에서는 이벤트 및 사용자 테이블을 `user_id`에 분할합니다.

```sql
SELECT create_distributed_table('github_events', 'user_id');
SELECT create_distributed_table('github_users', 'user_id');
```

데이터를 로드할 준비가 되었습니다. 두 예제 파일 [users.csv](https://examples.citusdata.com/users.csv) 및 [events.csv](https://examples.citusdata.com/events.csv)를 다운로드합니다. 파일을 다운로드한 후에는 psql을 사용하여 데이터베이스에 연결합니다. 다운로드한 파일이 들어 있는 디렉터리에서 psql을 실행해야 합니다. 다음과 같이 `\copy` 명령으로 데이터를 로드합니다.

```sql
\copy github_events from 'events.csv' WITH CSV
\copy github_users from 'users.csv' WITH CSV
```

## <a name="run-queries"></a>쿼리 실행

이제부터 재미있는 부분이 시작됩니다. 몇 가지 쿼리를 실제로 실행할 것입니다. 간단한 `count (*)`부터 시작하여 우리가 얼마나 많은 데이터를 로드했는지 알아봅시다.

```sql
SELECT count(*) from github_events;
```

문제 없이 작동합니다. 집계에 관련된 내용은 나중에 살펴보기로 하고, 지금은 몇 가지 다른 쿼리를 살펴보겠습니다. JSONB `payload` 열 내에는 좋은 데이터가 있지만, 이벤트 유형에 따라 데이터가 달라집니다. `PushEvent` 이벤트에는 푸시에 대한 고유의 커밋 수를 포함하는 크기가 포함되어 있습니다. 다음과 같이 이 크기를 사용하여 시간당 총 커밋 수를 알아낼 수 있습니다.

```sql
SELECT date_trunc('hour', created_at) AS hour,
       sum((payload->>'distinct_size')::int) AS num_commits
FROM github_events
WHERE event_type = 'PushEvent'
GROUP BY hour
ORDER BY hour;
```

지금까지 살펴본 쿼리는 오직 github\_events만 사용했지만, 이 정보를 github\_users와 결합할 수 있습니다. 사용자와 이벤트를 동일한 식별자(`user_id`)에 분할했기 때문에 사용자 ID가 일치하는 두 테이블의 행은 동일한 데이터베이스 노드에 [공동 배치](http://docs.citusdata.com/en/stable/sharding/data_modeling.html#colocation)되며, 따라서 쉽게 조인할 수 있습니다.

`user_id`에 조인하는 경우 하이퍼스케일은 조인 실행을 분할된 데이터베이스로 보내 여러 작업자 노드에서 병렬로 실행할 수 있습니다. 예를 들어 리포지토리를 가장 많이 만든 사용자를 찾아보겠습니다.

```sql
SELECT login, count(*)
FROM github_events ge
JOIN github_users gu
ON ge.user_id = gu.user_id
WHERE event_type = 'CreateEvent' AND
      payload @> '{"ref_type": "repository"}'
GROUP BY login
ORDER BY count(*) DESC;
```

## <a name="clean-up-resources"></a>리소스 정리

이전 단계에서 리소스 그룹에 Azure 리소스를 만들었습니다. 이러한 리소스가 더 이상 필요 없으면 서버 그룹을 삭제합니다. 서버 그룹의 **개요** 페이지에서 **삭제** 단추를 누릅니다. 팝업 페이지에 메시지가 표시되면 서버 그룹의 이름을 확인하고 최종 **삭제** 단추를 클릭합니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 하이퍼스케일(Citus) 서버 그룹을 프로비저닝하는 방법을 배웠습니다. 그 후 psql을 사용하여 이 서버 그룹에 연결하고, 스키마를 만들고, 데이터를 분산했습니다.

다음으로, 자습서에 따라 확장성 있는 다중 테넌트 애플리케이션을 빌드합니다.
> [!div class="nextstepaction"]
> [다중 테넌트 데이터베이스 설계](https://aka.ms/hyperscale-tutorial-multi-tenant)
