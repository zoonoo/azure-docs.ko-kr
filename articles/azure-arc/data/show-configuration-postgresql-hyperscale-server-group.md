---
title: 원호의 사용 PostgreSQL Hyperscale 서버 그룹 구성 표시
titleSuffix: Azure Arc enabled data services
description: 원호의 사용 PostgreSQL Hyperscale 서버 그룹 구성 표시
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: a268cd6b2fa3da6846554e3d1b170298abec7f18
ms.sourcegitcommit: 58f12c358a1358aa363ec1792f97dae4ac96cc4b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93279404"
---
# <a name="show-the-configuration-of-an-arc-enabled-postgresql-hyperscale-server-group"></a>원호의 사용 PostgreSQL Hyperscale 서버 그룹 구성 표시

이 문서에서는 서버 그룹의 구성을 표시 하는 방법을 설명 합니다. 사용자에 게 요청할 수 있는 몇 가지 질문을 예측 하 여이를 수행 합니다. 몇 번의 유효한 답변이 있을 수 있습니다. 이 문서에서는 가장 일반적 이거나 유용한 항목을 pitches 합니다. 테마에 따라 이러한 질문을 그룹화 합니다.

- Kubernetes 관점에서
- Azure Arc 사용 데이터 서비스 관점에서

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="from-a-kubernetes-point-of-view"></a>Kubernetes 관점에서

### <a name="how-many-pods-are-used-by-azure-arc-enabled-postgresql-hyperscale"></a>Azure Arc에서 사용 되는 pod의 수는 PostgreSQL Hyperscale 입니까?

Postgres 형식의 Kubernetes 리소스를 나열 합니다. 명령 실행:

```console
kubectl get postgresqls [-n <namespace name>]
```

이 명령의 출력은 생성 된 서버 그룹 목록을 보여 줍니다. 각에 대해 pod의 수를 나타냅니다. 다음은 그 예입니다.

```output
NAME                                             STATE   READY-PODS   EXTERNAL-ENDPOINT   AGE
postgresql-12.arcdata.microsoft.com/postgres01   Ready   3/3          10.0.0.4:30499      6h34m
postgresql-12.arcdata.microsoft.com/postgres02   Ready   3/3          10.0.0.4:31066      6d7h
```

이 예에서는 2 개의 서버 그룹이 만들어지고 각각 3 pod (1 개의 코디네이터 + 2 작업자)에서 실행 되는 것을 보여 줍니다. 즉,이 Azure Arc 데이터 컨트롤러에서 만든 서버 그룹은 6 pod를 사용 합니다.

### <a name="what-pods-are-used-by-azure-arc-enabled-postgresql-hyperscale-server-groups"></a>Azure Arc에서 사용 되는 pod PostgreSQL Hyperscale 서버 그룹은 무엇 인가요?

다음을 실행합니다.

```console
kubectl get pods [-n <namespace name>]
```

그러면 pod 목록이 반환 됩니다. 서버 그룹에 지정한 이름에 따라 서버 그룹에서 사용 하는 pod 표시 됩니다. 다음은 그 예입니다.

```console 
NAME                 READY   STATUS    RESTARTS   AGE
bootstrapper-vdltm   1/1     Running   0          6d8h
control-h6kc9        2/2     Running   0          6d8h
controldb-0          2/2     Running   0          6d8h
controlwd-96sbn      1/1     Running   0          6d8h
logsdb-0             1/1     Running   0          6d8h
logsui-7wkg2         1/1     Running   0          6d8h
metricsdb-0          1/1     Running   0          6d8h
metricsdc-28ffl      1/1     Running   0          6d8h
metricsui-k7qsh      1/1     Running   0          6d8h
mgmtproxy-gd84z      2/2     Running   0          6d8h
postgres01-0         3/3     Running   0          6h50m
postgres01-1         3/3     Running   0          6h50m
postgres01-2         3/3     Running   0          6h50m
postgres02-0         3/3     Running   0          22h
postgres02-1         3/3     Running   0          22h
postgres02-2         3/3     Running   0          22h
```

이 예에서는 생성 된 두 서버 그룹을 호스트 하는 6 개의 pod는 다음과 같습니다.
- `postgres01-0`
- `postgres01-1`
- `postgres01-2`
- `postgres02-0`
- `postgres02-1`
- `postgres02-2`  

### <a name="what-server-group-pod-is-used-for-what-role-the-server-group"></a>서버 그룹의 역할에 사용 되는 서버 그룹 pod는 무엇 인가요?

로 시작 하는 모든 pod 이름은 `-0` 코디네이터 노드를 나타냅니다. 가 1 이상인 노드 이름에는 `-x` 작업자 노드가 있습니다. 위의 예제에서는 다음과 같습니다.
- 코디네이터는 다음과 같습니다. `postgres01-0` , `postgres02-0`
- 작업자는 `postgres01-2` , `postgres01-2` , `postgres02-1` ,입니다. `postgres02-2`

### <a name="what-is-the-status-of-the-pods"></a>Pod의 상태는 무엇 인가요?

`kubectl get pods`을 실행 하 고 열을 확인 합니다.`STATUS`

### <a name="what-persistent-volume-claims-pvcs-are-being-used"></a>사용 되는 영구적 볼륨 클레임 (Pvc)은 무엇 인가요? 

데이터, 로그 및 백업에 사용 되는 Pvc 뿐만 아니라 사용 되는 Pvc를 이해 하려면 다음을 실행 합니다. 

```console
kubectl get pvc [-n <namespace name>]
```

기본적으로 PVC의 이름 접두사는 사용을 나타냅니다.

- `backups-`...: 백업에 사용 되는 PVC
- `data-`...: 데이터 파일에 사용 되는 PVC
- `logs-`...: 트랜잭션 로그/WAL 파일에 사용 되는 PVC

다음은 그 예입니다.

```output
NAME                                            STATUS   VOLUME              CAPACITY   ACCESS MODES   STORAGECLASS    AGE
backups-few7hh0k4npx9phsiobdc3hq-postgres01-0   Bound    local-pv-485e37db   1938Gi     RWO            local-storage   6d6h
backups-few7hh0k4npx9phsiobdc3hq-postgres01-1   Bound    local-pv-9d3d4a15   1938Gi     RWO            local-storage   6d6h
backups-few7hh0k4npx9phsiobdc3hq-postgres01-2   Bound    local-pv-7b8dd819   1938Gi     RWO            local-storage   6d6h
...
data-few7hh0k4npx9phsiobdc3hq-postgres01-0      Bound    local-pv-3c1a8cc5   1938Gi     RWO            local-storage   6d6h
data-few7hh0k4npx9phsiobdc3hq-postgres01-1      Bound    local-pv-8303ab19   1938Gi     RWO            local-storage   6d6h
data-few7hh0k4npx9phsiobdc3hq-postgres01-2      Bound    local-pv-55572fe6   1938Gi     RWO            local-storage   6d6h
...
logs-few7hh0k4npx9phsiobdc3hq-postgres01-0      Bound    local-pv-5e852b76   1938Gi     RWO            local-storage   6d6h
logs-few7hh0k4npx9phsiobdc3hq-postgres01-1      Bound    local-pv-55d309a7   1938Gi     RWO            local-storage   6d6h
logs-few7hh0k4npx9phsiobdc3hq-postgres01-2      Bound    local-pv-5ccd02e6   1938Gi     RWO            local-storage   6d6h
...
```


## <a name="from-an-azure-arc-enabled-data-services-point-of-view"></a>Azure Arc 사용 데이터 서비스 관점에서 다음을 수행 합니다.

* Arc 데이터 컨트롤러에 생성 되는 서버 그룹은 몇 개입니까?
* 이름은 무엇 인가요?
* 사용 되는 작업자 노드 수는 몇 개입니까?
* 실행 되는 Postgres의 버전은 무엇 인가요?

다음 명령 중 하나를 사용 합니다.
- **Kubectl 사용:**

   ```console
   kubectl get postgresqls [-n <namespace name>]
   ``` 

   예를 들어, 각 줄이 인 아래 출력을 생성 합니다 `servergroup` . 아래 표시에서 이름 구조는 다음과 같이 구성 됩니다.

   `<Name-Of-Custom-Resource-Definition>`/`<Server-Group-Name>`

   ```output
   NAME                                             STATE   READY-PODS   EXTERNAL-ENDPOINT   AGE
   postgresql-12.arcdata.microsoft.com/postgres01   Ready   3/3          10.0.0.4:30499      7h15m
   postgresql-12.arcdata.microsoft.com/postgres02   Ready   3/3          10.0.0.4:31066      6d7h
   ```

   위의 출력은 Postgres 버전 12의 서버 그룹 2 개를 보여 줍니다. 버전이 Postgres 11 인 경우에는 CRD 이름이 대신 postgresql-11.arcdata.microsoft.com 됩니다.

   각 항목은 3 개의 노드/pod: 1 코디네이터와 2 작업자에서 실행 됩니다.

- **Azdata 사용:**

다음 명령을 실행합니다. 출력은 kubectl 표시 되는 것과 비슷한 정보를 표시 합니다.

   ```console
   azdata arc postgres server list

   `output
   Name        State    Workers
   ----------  -------  ---------
   postgres01  Ready    2
   postgres02  Ready    2
   ```


### <a name="how-much-memory-and-vcores-are-being-used-and-what-extensions-were-created-for-a-group"></a>사용 중인 메모리 및 vCores의 양과 그룹에 대해 생성 된 확장은 무엇 인가요?

다음 명령 중 하나를 실행 합니다.

**Kubectl 사용:**

Kubectl를 사용 하 여 Postgres 리소스를 설명 합니다. 이렇게 하려면 해당 종류 (위와 같이 해당 Postgres 버전의 경우 CRD (Kubernetes resource) 이름)와 서버 그룹의 이름을 필요 합니다.
이 명령의 일반적인 형식은 다음과 같습니다.

```console
kubectl describe <CRD name>/<server group name> [-n <namespace name>]
```

다음은 그 예입니다.

```console
kubectl describe postgresql-12/postgres02
```

이 명령은 서버 그룹의 구성을 보여 줍니다.

```output
Name:         postgres02
Namespace:    arc
Labels:       <none>
Annotations:  <none>
API Version:  arcdata.microsoft.com/v1alpha1
Kind:         postgresql-12
Metadata:
  Creation Timestamp:  2020-08-31T21:01:07Z
  Generation:          10
  Resource Version:    569516
  Self Link:           /apis/arcdata.microsoft.com/v1alpha1/namespaces/arc/postgresql-12s/postgres02
  UID:                 8a9cd118-361b-4a2e-8a9d-5f9257bf6abb
Spec:
  Engine:
    Extensions:
      Name:  citus
      Name:  pg_stat_statements
  Scale:
    Workers:  2
  Scheduling:
    Default:
      Resources:
        Limits:
          Cpu:     4
          Memory:  1024Mi
        Requests:
          Cpu:     1
          Memory:  512Mi
  Service:
    Type:  NodePort
  Storage:
    Data:
      Class Name:  local-storage
      Size:        5Gi
    Logs:
      Class Name:  local-storage
      Size:        5Gi
Status:
  External Endpoint:  10.0.0.4:31066
  Ready Pods:         3/3
  State:              Ready
Events:               <none>
```

>[!NOTE]
>이전 예제에서는 2020 년 10 월 이전에 `Workers` 가 출시 되었습니다 `Shards` . 자세한 내용은 [릴리스 정보-Azure Arc 사용 가능 데이터 서비스 (미리 보기)](release-notes.md) 를 참조 하세요.

위에 표시 된에 대 한 설명에서 특정 관심 사항을 확인해 보겠습니다 `servergroup` . 이 서버 그룹에 대해 알려주세요.

- 이 버전은 Postgres의 버전 12입니다. 
   > ```json
   > Kind:         `postgresql-12`
   > ```
- 8 월 2020 일에 생성 되었습니다.
   > ```json
   > Creation Timestamp:  `2020-08-31T21:01:07Z`
   > ```
- 이 서버 그룹에서 두 개의 Postgres 확장을 만들었습니다. `citus``pg_stat_statements`
   > ```json
   > Engine:
   >    Extensions:
   >      Name:  `citus`
   >      Name:  `pg_stat_statements`
   > ```
- 두 작업자 노드를 사용 합니다.
   > ```json
   > Scale:
   >    Workers:  `2`
   > ```
- 노드당 하나의 cpu/vCore 및 512MB Ram을 사용 하는 것이 보장 됩니다. 5 개 이상의 cpu/vCores와 1024MB의 메모리를 사용 합니다.
   > ```json
   > Scheduling:
   >    Default: 
   >      Resources:
   >        Limits:
   >          Cpu:     4
   >          Memory:  1024Mi
   >        Requests:
   >          Cpu:     1
   >          Memory:  512Mi
   > ```
 - 쿼리에 사용할 수 있으며 문제가 발생 하지 않습니다. 모든 노드가 실행 되 고 있습니다.
   > ```json
   > Status:
   >  ...
   >  Ready Pods:         3/3
   >  State:              Ready
   > ```

**Azdata 사용:**

명령의 일반적인 형식은 다음과 같습니다.

```console
azdata arc postgres server show -n <server group name>
```

다음은 그 예입니다.

```console
azdata arc postgres server show -n postgres02
```

Kubectl에서 반환 하는 것과 매우 유사한 형식 및 내용으로 아래 출력을 반환 합니다.

```console
{
  "apiVersion": "arcdata.microsoft.com/v1alpha1",
  "kind": "postgresql-12",
  "metadata": {
    "creationTimestamp": "2020-08-31T21:01:07Z",
    "generation": 10,
    "name": "postgres02",
    "namespace": "arc",
    "resourceVersion": "569516",
    "selfLink": "/apis/arcdata.microsoft.com/v1alpha1/namespaces/arc/postgresql-12s/postgres02",
    "uid": "8a9cd118-361b-4a2e-8a9d-5f9257bf6abb"
  },
  "spec": {
    "engine": {
      "extensions": [
        {
          "name": "citus"
        },
        {
          "name": "pg_stat_statements"
        }
      ]
    },
    "scale": {
      "workers": 2
    },
    "scheduling": {
      "default": {
        "resources": {
          "limits": {
            "cpu": "4",
            "memory": "1024Mi"
          },
          "requests": {
            "cpu": "1",
            "memory": "512Mi"
          }
        }
      }
    },
    "service": {
      "type": "NodePort"
    },
    "storage": {
      "data": {
        "className": "local-storage",
        "size": "5Gi"
      },
      "logs": {
        "className": "local-storage",
        "size": "5Gi"
      }
    }
  },
  "status": {
    "externalEndpoint": "10.0.0.4:31066",
    "readyPods": "3/3",
    "state": "Ready"
  }
}
```

## <a name="next-steps"></a>다음 단계
- [Azure Arc 사용 PostgreSQL Hyperscale의 개념에 대해 읽기](concepts-distributed-postgres-hyperscale.md)
- [서버 그룹을 확장 (작업자 노드 추가) 하는 방법에 대해 알아봅니다.](scale-out-postgresql-hyperscale-server-group.md)
- [서버 그룹을 확장/축소 (메모리 및/또는 vCores 증가 또는 감소) 하는 방법에 대해 알아봅니다.](scale-up-down-postgresql-hyperscale-server-group-using-cli.md)
- [저장소 구성에 대 한 읽기 권한](storage-configuration.md)
- [데이터베이스 인스턴스를 모니터링 하는 방법 읽기](monitor-grafana-kibana.md)
- [Azure Arc enabled PostgreSQL Hyperscale 서버 그룹에서 PostgreSQL 확장 사용](using-extensions-in-postgresql-hyperscale-server-group.md)
- [Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹에 대한 보안 구성](configure-security-postgres-hyperscale.md)
