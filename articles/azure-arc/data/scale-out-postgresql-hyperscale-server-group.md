---
title: 규모 확장 서버 그룹 Azure Database for PostgreSQL
description: 규모 확장 서버 그룹 Azure Database for PostgreSQL
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: df0620308fab2e813fe3802dc7effb9dc1ce226c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91285386"
---
# <a name="scale-out-your-azure-arc-enabled-postgresql-hyperscale-server-group-by-adding-more-worker-nodes"></a>작업자 노드를 더 추가 하 여 Azure Arc 사용 PostgreSQL Hyperscale 서버 그룹 확장
이 문서에서는 Azure Arc 사용 PostgreSQL Hyperscale 서버 그룹을 확장 하는 방법을 설명 합니다. 시나리오를 통해이를 수행 합니다. **시나리오를 실행 하지 않고 규모를 확장 하는 방법에 대 한 자세한 내용을 보려면 단락 [규모 확장](#scale-out)으로 이동**하세요.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="get-started"></a>시작
Azure Arc의 크기 조정 모델에 이미 익숙한 경우 PostgreSQL Hyperscale 또는 Citus (Azure Database for PostgreSQL Hyperscale)를 사용 하는 경우이 단락을 건너뛸 수 있습니다. 그렇지 않은 경우에는 Citus (Azure Database for PostgreSQL Hyperscale)의 문서 페이지에서이 크기 조정 모델에 대해 읽어 시작 하는 것이 좋습니다. Citus (Azure Database for PostgreSQL Hyperscale)는 Azure Arc 사용 Data Services의 일부로 제공 되는 대신 Azure에서 서비스로 호스트 되는 동일한 기술 (PAAS 라고도 하는 서비스 라고도 함)입니다.
- [노드 및 테이블](../../postgresql/concepts-hyperscale-nodes.md)
- [애플리케이션 유형 확인](../../postgresql/concepts-hyperscale-app-type.md)
- [배포 열 선택](../../postgresql/concepts-hyperscale-choose-distribution-column.md)
- [테이블 공동 배치](../../postgresql/concepts-hyperscale-colocation.md)
- [테이블 분산 및 수정](../../postgresql/howto-hyperscale-modify-distributed-tables.md)
- [다중 테 넌 트 데이터베이스 디자인](../../postgresql/tutorial-design-database-hyperscale-multi-tenant.md)*
- [실시간 분석 대시보드 디자인](../../postgresql/tutorial-design-database-hyperscale-realtime.md)*

> \* 위의 문서에서 **Azure Portal에 로그인**섹션을 건너뛰고 **Azure Database for PostgreSQL-Hyperscale (Citus) & 만듭니다**. Azure Arc 배포의 나머지 단계를 구현 합니다. 이러한 섹션은 Azure 클라우드에서 PaaS 서비스로 제공 되는 Citus (Azure Database for PostgreSQL Hyperscale)에 고유 하지만, 문서의 다른 부분은 Azure Arc enabled PostgreSQL Hyperscale에 직접 적용할 수 있습니다.

## <a name="scenario"></a>시나리오
이 시나리오는 [Azure Arc Enabled PostgreSQL Hyperscale 서버 그룹 만들기](create-postgresql-hyperscale-server-group.md) 설명서에서 예제로 생성 된 PostgreSQL hyperscale 서버 그룹을 참조 합니다.

### <a name="load-test-data"></a>테스트 데이터 로드
이 시나리오에서는 [Citus data 웹 사이트](https://www.citusdata.com/) (Citus Data is Microsoft의 일부)에서 사용할 수 있는 공개 된 GitHub 데이터의 샘플을 사용 합니다.

#### <a name="connect-to-your-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Azure Arc 사용 PostgreSQL Hyperscale 서버 그룹에 연결

##### <a name="list-the-connection-information"></a>연결 정보 나열
먼저 연결 정보를 가져와 Azure Arc enabled PostgreSQL Hyperscale 서버 그룹에 연결 합니다 .이 명령의 일반적인 형식은 다음과 같습니다.
```console
azdata arc postgres endpoint list -n <server name>
```
예를 들면 다음과 같습니다.
```console
azdata arc postgres endpoint list -n postgres01
```

예제 출력:

```console
[
  {
    "Description": "PostgreSQL Instance",
    "Endpoint": "postgresql://postgres:<replace with password>@12.345.123.456:1234"
  },
  {
    "Description": "Log Search Dashboard",
    "Endpoint": "https://12.345.123.456:12345/kibana/app/kibana#/discover?_a=(query:(language:kuery,query:'custom_resource_name:\"postgres01\"'))"
  },
  {
    "Description": "Metrics Dashboard",
    "Endpoint": "https://12.345.123.456:12345/grafana/d/postgres-metrics?var-Namespace=arc3&var-Name=postgres01"
  }
]
```

##### <a name="connect-with-the-client-tool-of-your-choice"></a>선택한 클라이언트 도구를 사용 하 여 연결 합니다.

다음 쿼리를 실행 하 여 현재 Kubernetes pod에 해당 하는 두 개 (또는 더 많은 Hyperscale worker 노드)가 있는지 확인 합니다.

```sql
SELECT * FROM pg_dist_node;
```

```console
 nodeid | groupid |                       nodename                        | nodeport | noderack | hasmetadata | isactive | noderole | nodecluster | metadatasynced | shouldhaveshards
--------+---------+-------------------------------------------------------+----------+----------+-------------+----------+----------+-------------+----------------+------------------
      1 |       1 | pg1-1.pg1-svc.default.svc.cluster.local |     5432 | default  | f           | t        | primary  | default     | f              | t
      2 |       2 | pg1-2.pg1-svc.default.svc.cluster.local |     5432 | default  | f           | t        | primary  | default     | f              | t
(2 rows)
```

#### <a name="create-a-sample-schema"></a>샘플 스키마 만들기
다음 쿼리를 실행 하 여 두 개의 테이블을 만듭니다.

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

JSONB는 PostgreSQL에서 이진 형식의 JSON 데이터 형식입니다. PostgreSQL를 사용 하 여 단일 열에 유연한 스키마를 저장 합니다. 이 스키마에는 모든 키와 값을 인덱싱할 수 있는 GIN 인덱스가 포함 됩니다. GIN 인덱스를 사용 하면 해당 페이로드에 직접 다양 한 조건을 사용 하 여 빠르고 쉽게 쿼리할 수 있습니다. 따라서 데이터를 로드 하기 전에 몇 가지 인덱스를 만들어 보겠습니다.

```sql
CREATE INDEX event_type_index ON github_events (event_type);
CREATE INDEX payload_index ON github_events USING GIN (payload jsonb_path_ops);
```

분할 된 표준 테이블에 대해 각 테이블에 대해 쿼리를 실행 합니다. 분할 하려는 테이블 및 분할 하려는 키를 지정 합니다. User_id에서 이벤트와 사용자 테이블을 분할 합니다.

```sql
SELECT create_distributed_table('github_events', 'user_id');
SELECT create_distributed_table('github_users', 'user_id');
```

#### <a name="load-sample-data"></a>샘플 데이터 로드
복사본을 사용 하 여 데이터 로드 ... 프로그램에서:

```sql
COPY github_users FROM PROGRAM 'curl "https://examples.citusdata.com/users.csv"' WITH ( FORMAT CSV );
COPY github_events FROM PROGRAM 'curl "https://examples.citusdata.com/events.csv"' WITH ( FORMAT CSV );
```

#### <a name="query-the-data"></a>데이터 쿼리
그리고 이제 다음 두 노드를 사용 하 여 간단한 쿼리를 수행 하는 기간을 측정 합니다.

```sql
SELECT COUNT(*) FROM github_events;
```
쿼리 실행 시간을 기록 합니다.


## <a name="scale-out"></a>확장
수평 확장 명령의 일반적인 형식은 다음과 같습니다.
```console
azdata arc postgres server edit -n <server group name> -w <target number of worker nodes>
```

> [!CAUTION]
> 미리 보기 릴리스에서는 크기 조정을 지원하지 않습니다. 예를 들어 아직 작업자 노드 수를 줄일 수는 없습니다. 이 작업을 수행해야 하는 경우 데이터를 추출/백업하고, 서버 그룹을 삭제하고, 작업자 노드가 더 적은 새 서버 그룹을 만든 다음 데이터를 가져와야 합니다.

이 예제에서는 다음 명령을 실행 하 여 작업자 노드 수를 2에서 4로 늘립니다.

```console
azdata arc postgres server edit -n postgres01 -w 4
```

노드를 추가할 때 서버 그룹에 대 한 보류 중 상태가 표시 됩니다. 예를 들면 다음과 같습니다.
```console
azdata arc postgres server list
```

```console
Name        State          Workers
----------  -------------  ---------
postgres01  Pending 4/5    4
```

노드를 사용할 수 있게 되 면 Hyperscale 분할이 자동으로 실행 되 고 데이터를 새 노드에 다시 분산 합니다. 수평 확장 작업은 온라인 작업입니다. 노드가 추가 되 고 데이터가 노드 간에 재배포 되는 동안 데이터는 쿼리에 사용할 수 있는 상태로 유지 됩니다.

### <a name="verify-the-new-shape-of-the-server-group-optional"></a>서버 그룹의 새 셰이프를 확인 합니다 (선택 사항).
아래 방법 중 하나를 사용 하 여 서버 그룹이 추가 된 추가 작업자 노드를 현재 사용 하 고 있는지 확인 합니다.

#### <a name="with-azdata"></a>Azdata 사용:
명령 실행:
```console
azdata arc postgres server list
```

네임 스페이스에 생성 된 서버 그룹의 목록을 반환 하 고 작업자 노드 수를 나타냅니다. 예를 들면 다음과 같습니다.
```console
Name        State    Workers
----------  -------  ---------
postgres01  Ready    4
```

#### <a name="with-kubectl"></a>Kubectl 사용:
명령 실행:
```console
kubectl get postgresql-12
```

네임 스페이스에 생성 된 서버 그룹의 목록을 반환 하 고 작업자 노드 수를 나타냅니다. 예를 들면 다음과 같습니다.
```console
NAME         STATE   READY-PODS   EXTERNAL-ENDPOINT   AGE
postgres01   Ready   4/4          10.0.0.4:31066      4d20h
```
> [!NOTE]
> 12 대신 버전 11 PostgreSQL의 서버 그룹을 만든 경우 다음 명령을 대신 실행 합니다. _kubectl get PostgreSQL-11_

#### <a name="with-a-sql-query"></a>SQL 쿼리 사용:
선택한 클라이언트 도구를 사용 하 여 서버 그룹에 연결 하 고 다음 쿼리를 실행 합니다.

```sql
SELECT * FROM pg_dist_node;
```

```console
 nodeid | groupid |                       nodename                        | nodeport | noderack | hasmetadata | isactive | noderole | nodecluster | metadatasynced | shouldhaveshards
--------+---------+-------------------------------------------------------+----------+----------+-------------+----------+----------+-------------+----------------+------------------
      1 |       1 | pg1-1.pg1-svc.default.svc.cluster.local |     5432 | default  | f           | t        | primary  | default     | f              | t
      2 |       2 | pg1-2.pg1-svc.default.svc.cluster.local |     5432 | default  | f           | t        | primary  | default     | f              | t
      3 |       3 | pg1-3.pg1-svc.default.svc.cluster.local |     5432 | default  | f           | t        | primary  | default     | f              | t
      4 |       4 | pg1-4.pg1-svc.default.svc.cluster.local |     5432 | default  | f           | t        | primary  | default     | f              | t
(4 rows)
```

## <a name="return-to-the-scenario"></a>시나리오로 돌아가기

Select count 쿼리의 실행 시간을 예제 데이터 집합과 비교 하려면 동일한 count 쿼리를 사용 합니다. SQL 문을 변경 하지 않고 4 개의 작업자 노드 전체에서 사용할 수 있습니다.

```sql
SELECT COUNT(*) FROM github_events;
```
실행 시간을 확인 합니다.


> [!NOTE]
> 사용자 환경에 따라 (예: 단일 노드 VM에서를 사용 하 여 테스트 서버 그룹을 배포한 경우 `kubeadm` ) 실행 시간이 크게 향상 될 수 있습니다. Azure Arc enabled PostgreSQL Hyperscale으로 연결할 수 있는 성능 향상의 유형을 보다 잘 이해 하려면 다음 짧은 비디오를 시청 하세요.
>* [Citus (Azure PostgreSQL Hyperscale)를 사용 하는 고성능 HTAP](https://www.youtube.com/watch?v=W_3e07nGFxY)
>* [Python을 사용 하 여 HTAP 응용 프로그램 빌드 & Azure PostgreSQL Hyperscale (Citus)](https://www.youtube.com/watch?v=YDT8_riLLs0)


## <a name="next-steps"></a>다음 단계

- [Azure Arc 사용 PostgreSQL hyperscale 서버 그룹을 확장 및 축소 (메모리, vcores)](scale-up-down-postgresql-hyperscale-server-group-using-cli.md) 하는 방법에 대해 알아봅니다.
- Azure Arc 사용 PostgreSQL Hyperscale 서버 그룹에서 서버 매개 변수를 설정 하는 방법에 대해 알아봅니다.
- Azure Database for PostgreSQL Hyperscale의 개념 및 방법 가이드를 읽고 여러 PostgreSQL Hyperscale 노드에 데이터를 분산 하 고 Postgres Hyperscale 용 Azure Database의 모든 기능을 활용 하세요. :
    * [노드 및 테이블](../../postgresql/concepts-hyperscale-nodes.md)
    * [애플리케이션 유형 확인](../../postgresql/concepts-hyperscale-app-type.md)
    * [배포 열 선택](../../postgresql/concepts-hyperscale-choose-distribution-column.md)
    * [테이블 공동 배치](../../postgresql/concepts-hyperscale-colocation.md)
    * [테이블 분산 및 수정](../../postgresql/howto-hyperscale-modify-distributed-tables.md)
    * [다중 테 넌 트 데이터베이스 디자인](../../postgresql/tutorial-design-database-hyperscale-multi-tenant.md)*
    * [실시간 분석 대시보드 디자인](../../postgresql/tutorial-design-database-hyperscale-realtime.md)*

 > \* 위의 문서에서 **Azure Portal에 로그인**섹션을 건너뛰고 **Azure Database for PostgreSQL-Hyperscale (Citus) & 만듭니다**. Azure Arc 배포의 나머지 단계를 구현 합니다. 이러한 섹션은 Azure 클라우드에서 PaaS 서비스로 제공 되는 Citus (Azure Database for PostgreSQL Hyperscale)에 고유 하지만, 문서의 다른 부분은 Azure Arc enabled PostgreSQL Hyperscale에 직접 적용할 수 있습니다.

- [저장소 구성 및 Kubernetes 저장소 개념](storage-configuration.md)
- [영구적 볼륨 클레임 확장](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#expanding-persistent-volumes-claims)
- [Kubernetes 리소스 모델](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)
