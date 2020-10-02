---
title: Kubernetes 클러스터 노드에서 PostgreSQL Hyperscale 서버 그룹 배치
description: PostgreSQL Hyperscale 서버 그룹을 형성 하는 PostgreSQL 인스턴스가 Kubernetes 클러스터 노드에 배치 되는 방법을 설명 합니다.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 2b69eb076c727a4383b7459ef914ac79dca31c84
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2020
ms.locfileid: "91628420"
---
# <a name="azure-arc-enabled-postgresql-hyperscale-server-group-placement"></a>Azure Arc 사용 PostgreSQL Hyperscale 서버 그룹 배치

이 문서에서는 Azure Arc enabled PostgreSQL Hyperscale server 그룹의 PostgreSQL 인스턴스를 호스트 하는 Kubernetes 클러스터의 실제 노드에 배치 하는 방법을 보여 주는 예제를 제공 합니다. 

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="configuration"></a>Configuration

이 예제에서는 네 개의 실제 노드가 있는 AKS (Azure Kubernetes Service) 클러스터를 사용 합니다. 

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/1_cluster_portal.png" alt-text="Azure Portal의 4 노드 AKS 클러스터":::

다음 명령을 실행 하 여 Kubernetes 클러스터의 실제 노드를 나열 합니다.

```console
kubectl get nodes
```

Kubernetes 클러스터 내에서 4 개의 실제 노드가 표시 됩니다.

```output
NAME                                STATUS   ROLES   AGE   VERSION
aks-agentpool-42715708-vmss000000   Ready    agent   11h   v1.17.9
aks-agentpool-42715708-vmss000001   Ready    agent   11h   v1.17.9
aks-agentpool-42715708-vmss000002   Ready    agent   11h   v1.17.9
aks-agentpool-42715708-vmss000003   Ready    agent   11h   v1.17.9
```

아키텍처는 다음과 같이 나타낼 수 있습니다.

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/2_logical_cluster.png" alt-text="Azure Portal의 4 노드 AKS 클러스터":::

Kubernetes 클러스터는 하나의 Azure Arc 데이터 컨트롤러와 하나의 Azure Arc enabled PostgreSQL Hyperscale 서버 그룹을 호스팅합니다. 이 서버 그룹은 하나의 코디네이터와 두 작업자 라는 세 개의 PostgreSQL 인스턴스를 구성 된 합니다.

명령을 사용 하 여 pod를 나열 합니다.

```console
kubectl get pods -n arc3
```
다음과 같은 출력을 생성합니다.

```output
NAME                 READY   STATUS    RESTARTS   AGE
…
postgres01-0         3/3     Running   0          9h
postgres01-1         3/3     Running   0          9h
postgres01-2         3/3     Running   0          9h
```
이러한 각 pod는 PostgreSQL 인스턴스를 호스팅합니다. 이들은 함께 Azure Arc enabled PostgreSQL Hyperscale 서버 그룹을 형성 합니다.

```output
Pod name    Role in the server group
postgres01-0            Coordinator
postgres01-1    Worker
postgres01-2    Worker
```

## <a name="placement"></a>배치
Kubernetes가 서버 그룹의 pod을 배치 하는 방법을 살펴보겠습니다. 각 pod를 설명 하 고 해당 pod가 배치 된 Kubernetes 클러스터의 실제 노드를 식별 합니다. 예를 들어 코디네이터의 경우 다음 명령을 실행 합니다.

```console
kubectl describe pod postgres01-0 -n arc3
```

다음과 같은 출력을 생성합니다.

```output
Name:         postgres01-0
Namespace:    arc3
Priority:     0
Node:         aks-agentpool-42715708-vmss000000
Start Time:   Thu, 17 Sep 2020 00:40:33 -0700
…
```

각 pod에 대해이 명령을 실행 하면 현재 배치가 다음과 같이 요약 됩니다.

| 서버 그룹 역할|서버 그룹 pod|Pod를 호스트 하는 Kubernetes 실제 노드 |
|--|--|--|
| 작업자|postgres01-1|aks-agentpool-42715708-vmss000002 |
| 작업자|postgres01-2|aks-agentpool-42715708-vmss000003 |

또한 pod에 대 한 설명에서 각 pod가 호스트 하는 컨테이너의 이름을 확인 합니다. 예를 들어 두 번째 작업자의 경우 다음 명령을 실행 합니다.

```console
kubectl describe pod postgres01-2 -n arc3
```

다음과 같은 출력을 생성합니다.

```output
…
Node:         aks-agentpool-42715708-vmss000003/10.240.0.7
..
Containers:
  Fluentbit:
…
  Postgres:
…
  Telegraf:
…
```

Azure Arc enabled PostgreSQL Hyperscale 서버 그룹의 일부인 각 pod는 다음 세 가지 컨테이너를 호스팅합니다.

|컨테이너|설명
|----|----|
|`Fluentbit` |데이터 * 로그 수집기: https://fluentbit.io/
|`Postgres`|Azure Arc enabled PosgreSQL Hyperscale 서버 그룹의 PostgreSQL 인스턴스 부분
|`Telegraf` |메트릭 수집기: https://www.influxdata.com/time-series-platform/telegraf/

아키텍처는 다음과 같습니다.

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/3_pod_placement.png" alt-text="Azure Portal의 4 노드 AKS 클러스터":::

즉,이 시점에서 Azure Arc enabled PostgreSQL Hyperscale 서버 그룹 백업을 위해 각 PostgreSQL 인스턴스는 Kubernetes 컨테이너 내의 특정 실제 호스트에서 호스팅됩니다. 각 역할 (코디네이터 및 작업자)이 각 실제 노드의 리소스를 사용 하기 때문에 Azure Arc 사용 PostgreSQL Hyperscale 서버 그룹에서 가장 높은 성능을 얻는 데 도움이 되는 최상의 구성입니다. 이러한 리소스는 여러 PostgreSQL 역할 간에 공유 되지 않습니다.

## <a name="scale-out-azure-arc-enabled-postgresql-hyperscale"></a>Azure Arc 사용 PostgreSQL Hyperscale 확장

이제 서버 그룹에 세 번째 작업자 노드를 추가 하 고 어떻게 되는지 관찰 해 보겠습니다. 네 번째 pod에서 호스트 되는 네 번째 PostgreSQL 인스턴스를 만듭니다.
규모를 확장 하려면 다음 명령을 실행 합니다.

```console
azdata arc postgres server edit --name postgres01 --workers 3
```

그러면 다음과 같은 출력이 생성 됩니다.

```output
Updating postgres01 in namespace `arc3`
postgres01 is Ready
```

Azure Arc 데이터 컨트롤러에 배포 된 서버 그룹을 나열 하 고 이제 서버 그룹이 세 개의 작업자와 함께 실행 되는지 확인 합니다. 명령 실행:

```console
azdata arc postgres server list
```

그리고 두 작업자에서 3 개의 작업자로 규모를 확장 했는지 확인 합니다.

```output
Name        State    Workers
----------  -------  ---------
postgres01  Ready    3
```

앞에서 설명한 것 처럼 서버 그룹은 이제 총 4 개의 pod를 사용 합니다.

```console
kubectl get pods -n arc3
```

```output
NAME                 READY   STATUS    RESTARTS   AGE
…
postgres01-0         3/3     Running   0          11h
postgres01-1         3/3     Running   0          11h
postgres01-2         3/3     Running   0          11h
postgres01-3         3/3     Running   0          5m2s
```

및는 새 pod를 설명 하 여 호스트 되는 Kubernetes 클러스터의 실제 노드를 식별 합니다.
명령 실행:

```console
kubectl describe pod postgres01-3 -n arc3
```

호스팅 노드의 이름을 식별 하려면 다음을 수행 합니다.

```output
Name:         postgres01-3
Namespace:    arc3
Priority:     0
Node:         aks-agentpool-42715708-vmss000000
```

클러스터의 실제 노드에 PostgreSQL 인스턴스를 배치 하는 것은 이제 다음과 같습니다.

|서버 그룹 역할|서버 그룹 pod|Pod를 호스트 하는 Kubernetes 실제 노드
|-----|-----|-----
|코디네이터가|postgres01-0|aks-agentpool-42715708-vmss000000
|작업자|postgres01-1|aks-agentpool-42715708-vmss000002
|작업자|postgres01-2|aks-agentpool-42715708-vmss000003
|작업자|postgres01-3|aks-agentpool-42715708-vmss000000

새 작업자 (postgres01-3)의 pod가 코디네이터와 동일한 노드에 배치 되었는지 확인 합니다. 

아키텍처는 다음과 같습니다.

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/4_pod_placement_.png" alt-text="Azure Portal의 4 노드 AKS 클러스터" 서비스입니다.|aks-agentpool-42715708-vmss000000
|logsdb-0|모든 Arc data services pod에서 수집 된 모든 로그를 저장 하는 데 사용 되는 탄력적 검색 인스턴스입니다. Elasticsearch, `Fluentbit` 각 pod의 컨테이너에서 데이터를 수신 합니다.|aks-agentpool-42715708-vmss000003
|logsui-5fzv5|이 인스턴스는 log analytics GUI를 제공 하기 위해 탄력적 검색 데이터베이스 위에 있는 Kibana 인스턴스입니다.|aks-agentpool-42715708-vmss000003
|metricsdb-0|모든 Arc data services pod에서 수집 된 모든 메트릭을 저장 하는 데 사용 되는 InfluxDB 인스턴스입니다. InfluxDB는 `Telegraf` 각 pod의 컨테이너에서 데이터를 수신 합니다.|aks-agentpool-42715708-vmss000000
|metricsdc-47d47|이는 노드에 대 한 노드 수준 메트릭을 수집 하기 위해 클러스터의 모든 Kubernetes 노드에 배포 된 daemonset입니다.|aks-agentpool-42715708-vmss000002
|metricsdc-864kj|이는 노드에 대 한 노드 수준 메트릭을 수집 하기 위해 클러스터의 모든 Kubernetes 노드에 배포 된 daemonset입니다.|aks-agentpool-42715708-vmss000001
|metricsdc-l8jkf|이는 노드에 대 한 노드 수준 메트릭을 수집 하기 위해 클러스터의 모든 Kubernetes 노드에 배포 된 daemonset입니다.|aks-agentpool-42715708-vmss000003
|metricsdc-nxm4l|이는 노드에 대 한 노드 수준 메트릭을 수집 하기 위해 클러스터의 모든 Kubernetes 노드에 배포 된 daemonset입니다.|aks-agentpool-42715708-vmss000000
|metricsui-4fb7l|이 인스턴스는 모니터링 대시보드 GUI를 제공 하기 위해 InfluxDB 데이터베이스 위에 있는 Grafana 인스턴스입니다.|aks-agentpool-42715708-vmss000003
|mgmtproxy-4qppp|Grafana 및 Kibana 인스턴스 앞에 있는 웹 응용 프로그램 프록시 계층입니다.|aks-agentpool-42715708-vmss000002

> \* Pod 이름의 접미사는 다른 배포에 따라 달라 집니다. 또한 Azure Arc 데이터 컨트롤러의 Kubernetes 네임 스페이스 내에서 호스트 되는 pod 여기에 나열 됩니다.

아키텍처는 다음과 같습니다.

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/5_full_list_of_pods.png" alt-text="Azure Portal의 4 노드 AKS 클러스터":::

즉, Azure Arc enabled Postgres Hyperscale server 그룹의 코디네이터 노드 (Pod 1)는 서버 그룹의 세 번째 작업자 노드 (Pod 4)와 동일한 물리적 리소스를 공유 합니다. 코디네이터 노드가 일반적으로 작업자 노드에 사용 되는 것과 비교 하 여 매우 적은 리소스를 사용 하기 때문에 허용 됩니다. 이를 통해 신중 하 게 선택 해야 한다는 것을 유추할 수 있습니다.
- Kubernetes 클러스터의 크기 및 각 실제 노드의 특성 (메모리, vCore)
- Kubernetes 클러스터 내의 실제 노드 수
- Kubernetes 클러스터에서 호스트 하는 응용 프로그램 또는 작업입니다.

Kubernetes 클러스터에서 너무 많은 워크 로드를 호스트 하는 것은 Azure Arc enabled PostgreSQL Hyperscale 서버 그룹에 대해 제한이 발생할 수 있습니다. 이런 경우에는 해당 기능을 수평으로 확장 하는 것이 더 효율적이 지 않습니다. 시스템에서 얻을 수 있는 성능은 실제 노드나 저장소 시스템의 배치 나 물리적 특성에 대 한 것이 아닙니다. 또한 Kubernetes 클러스터 내에서 실행 되는 각 리소스를 구성 하는 방법 (예를 들어, 메모리 및 vCore에 대해 설정 하는 요청 및 제한)에 대 한 정보를 얻을 수 있습니다. 지정 된 Kubernetes 클러스터에서 호스트할 수 있는 작업 양은 Kubernetes 클러스터의 특성, 워크 로드의 특성, 사용자 수, Kubernetes 클러스터의 작업을 수행 하는 방법 등을 기준으로 합니다.

## <a name="scale-out-aks"></a>규모 확장 AKS

AKS 클러스터와 Azure Arc enabled PostgreSQL Hyperscale 서버 모두 수평 확장을 통해 Azure Arc 사용 PostgreSQL Hyperscale의 높은 성능을 활용 하는 방법을 살펴보겠습니다.
AKS 클러스터에 다섯 번째 노드를 추가 해 보겠습니다.

:::row:::
    :::column:::
        이전
    :::column-end:::
    :::column:::
        이러한
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        :::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/6_layout_before.png" alt-text="Azure Portal의 4 노드 AKS 클러스터":::
    :::column-end:::
    :::column:::
        :::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/7_layout_after.png" alt-text="Azure Portal의 4 노드 AKS 클러스터":::
    :::column-end:::
:::row-end:::

아키텍처는 다음과 같습니다.

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/8_logical_layout_after.png" alt-text="Azure Portal의 4 노드 AKS 클러스터":::

명령을 실행 하 여 new AKS 실제 노드에 호스트 되는 Arc Data Controller 네임 스페이스의 pod을 살펴보겠습니다.

```console
kubectl describe node aks-agentpool-42715708-vmss000004
```

시스템 아키텍처의 표현을 업데이트 해 보겠습니다.

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/9_updated_list_of_pods.png" alt-text="Azure Portal의 4 노드 AKS 클러스터":::

Kubernetes 클러스터의 새 실제 노드가 Azure Arc data services에 필요한 메트릭 pod만 호스트 하 고 있음을 확인할 수 있습니다. 이 예제에서는 Arc 데이터 컨트롤러의 네임 스페이스에만 중점을 두고 있으며, 다른 pod를 나타내지 않습니다.

## <a name="scale-out-azure-arc-enabled-postgresql-hyperscale-again"></a>Azure Arc 사용 PostgreSQL Hyperscale을 다시 확장

다섯 번째 실제 노드가 아직 작업을 호스팅하지 않습니다. Azure Arc enabled PostgreSQL Hyperscale을 확장 하면 Kubernetes는 새 PostgreSQL pod의 배치를 최적화 하 고 더 많은 작업을 이미 호스팅하고 있는 실제 노드에서이를 배치 해서는 안 됩니다. 다음 명령을 실행 하 여 Azure Arc enabled PostgreSQL Hyperscale을 3에서 4 작업자로 조정 합니다. 작업이 끝날 때 서버 그룹은 하나의 코디네이터와 4 개의 작업자로 구성 된 되 고 5 개의 PostgreSQL 인스턴스 간에 분산 됩니다.

```console
azdata arc postgres server edit --name postgres01 --workers 4
```

그러면 다음과 같은 출력이 생성 됩니다.

```output
Updating postgres01 in namespace `arc3`
postgres01 is Ready
```

데이터 컨트롤러에 배포 된 서버 그룹을 나열 하 고 이제 4 개의 작업자를 사용 하 여 서버 그룹을 실행 하는지 확인 합니다.

```console
azdata arc postgres server list
```

그리고 3 개에서 4 개의 작업자로 규모를 확장 했는지 관찰 합니다. 

```console
Name        State    Workers
----------  -------  ---------
postgres01  Ready    4
```

앞에서 설명한 것 처럼 이제 서버 그룹에서 4 개의 pod를 사용 합니다.

```output
kubectl get pods -n arc3

NAME                 READY   STATUS    RESTARTS   AGE
…
postgres01-0         3/3     Running   0          13h
postgres01-1         3/3     Running   0          13h
postgres01-2         3/3     Running   0          13h
postgres01-3         3/3     Running   0          179m
postgres01-4         3/3     Running   0          3m13s
```

이제 서버 그룹의 모양은 다음과 같습니다.

|서버 그룹 역할|서버 그룹 pod
|----|-----
|코디네이터가|postgres01-0
|작업자|postgres01-1
|작업자|postgres01-2
|작업자|postgres01-3
|작업자|postgres01-4

호스트 되는 실제 노드를 식별 하는 postgres01 pod에 대해 설명 하겠습니다.

```console
kubectl describe pod postgres01-4 -n arc3
```

Pod이 실행 되는 항목을 확인 합니다.

|서버 그룹 역할|서버 그룹 pod| Pod
|----|-----|------
|코디네이터가|postgres01-0|aks-agentpool-42715708-vmss000000
|작업자|postgres01-1|aks-agentpool-42715708-vmss000002
|작업자|postgres01-2|aks-agentpool-42715708-vmss000003
|작업자|postgres01-3|aks-agentpool-42715708-vmss000000
|작업자|postgres01-4|aks-agentpool-42715708-vmss000004

아키텍처는 다음과 같습니다.

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/10_kubernetes_schedules_newest_pod.png" alt-text="Azure Portal의 4 노드 AKS 클러스터":::

Kubernetes는 Kubernetes 클러스터의 최소 로드 된 실제 노드에서 새 PostgreSQL pod를 예약 했습니다.

## <a name="summary"></a>요약

Azure Arc 사용 서버 그룹을 수평으로 확장 하는 확장성과 성능을 최대한 활용 하려면 Kubernetes 클러스터 내에서 리소스 경합이 발생 하지 않도록 해야 합니다.
- Azure Arc enabled PostgreSQL Hyperscale 서버 그룹 및 동일한 Kubernetes 클러스터에 호스트 되는 다른 워크 로드
- Azure Arc enabled PostgreSQL Hyperscale 서버 그룹을 구성 하는 모든 PostgreSQL 인스턴스 간

여러 가지 방법으로이 작업을 수행할 수 있습니다.
- Kubernetes 및 Azure Arc 사용 설정 Postgres Hyperscale 모두 확장: Azure Arc enabled PostgreSQL Hyperscale 서버 그룹의 크기를 조정 하는 것과 동일한 방식으로 Kubernetes 클러스터를 수평으로 확장 하는 것을 고려 합니다. 서버 그룹에 추가 하는 각 작업자에 대 한 실제 노드를 클러스터에 추가 합니다.
- Kubernetes에서 호스트 되는 워크 로드에 대 한 적절 한 리소스 제약 조건 (메모리 및 vCore에 대 한 요청 및 제한)을 설정 하 여 Kubernetes 확장 없이 Azure Arc 사용 가능 Postgres Hyperscale 사용: Kubernetes에서 워크 로드의 공동 배치를 사용 하도록 설정 하 고 리소스 경합 위험을 줄입니다. Kubernetes 클러스터의 실제 노드에 대 한 물리적 특성이 사용자가 정의한 리소스 제약 조건을 준수 하는지 확인 해야 합니다. 또한 시간이 지남에 따라 워크 로드가 진화 하거나 더 많은 작업이 Kubernetes 클러스터에 추가 될 때 평형 상태를 유지 하도록 해야 합니다.
- Kubernetes 메커니즘 (pod 선택기, 선호도, 선호도 방지)을 사용 하 여 pod 배치에 영향을 줍니다.

## <a name="next-steps"></a>다음 단계

[작업자 노드를 더 추가 하 여 Azure Arc 사용 PostgreSQL Hyperscale 서버 그룹 확장](scale-out-postgresql-hyperscale-server-group.md)
