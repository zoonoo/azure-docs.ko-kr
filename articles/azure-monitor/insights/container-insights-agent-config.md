---
title: 컨테이너 에이전트 데이터 컬렉션에 대 한 Azure Monitor 구성 | Microsoft Docs
description: 이 문서에서는 stdout/stderr 및 환경 변수 로그 수집을 제어 하기 위해 컨테이너 에이전트에 대 한 Azure Monitor를 구성 하는 방법을 설명 합니다.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/14/2019
ms.author: magoedte
ms.openlocfilehash: 7cd915c47fa0661a9da66d7ca3315480ce7d6b98
ms.sourcegitcommit: d4c9821b31f5a12ab4cc60036fde00e7d8dc4421
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2019
ms.locfileid: "71709435"
---
# <a name="configure-agent-data-collection-for-azure-monitor-for-containers"></a>컨테이너의 Azure Monitor에 대 한 에이전트 데이터 수집 구성

컨테이너에 대 한 Azure Monitor는 컨테이너 화 된 에이전트에서 AKS (Azure Kubernetes Service)에 호스트 된 관리 되는 Kubernetes 클러스터에 배포 된 컨테이너 워크 로드에서 stdout, stderr 및 환경 변수를 수집 합니다. 또한이 에이전트는 컨테이너 화 된 에이전트를 사용 하 여 프로메테우스에서, 프로메테우스 서버 및 데이터베이스를 설정 하 고 관리 하지 않고도 메트릭 (메트릭이 라고도 함)의 시계열 데이터를 수집할 수 있습니다. 사용자 지정 Kubernetes ConfigMaps를 만들어이 환경을 제어 하 여 에이전트 데이터 수집 설정을 구성할 수 있습니다. 

이 문서에서는 사용자의 요구 사항에 따라 ConfigMap을 만들고 데이터 컬렉션을 구성 하는 방법을 보여 줍니다.

>[!NOTE]
>프로메테우스에 대 한 지원은 현재 공개 미리 보기의 기능입니다.
>

## <a name="configmap-file-settings-overview"></a>ConfigMap 파일 설정 개요

사용자 지정 항목을 처음부터 새로 만들지 않고도 쉽게 편집할 수 있도록 하는 템플릿 ConfigMap 파일이 제공 됩니다. 시작 하기 전에 configmaps [에 대 한](https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/) Kubernetes 설명서를 검토 하 고 configmaps을 생성, 구성 및 배포 하는 방법을 숙지 해야 합니다. 이렇게 하면 네임 스페이스 당 또는 전체 클러스터에서 stderr 및 stdout을 필터링 할 수 있으며 클러스터의 모든 pod/노드에서 실행 중인 모든 컨테이너에 대해 환경 변수를 필터링 할 수 있습니다.

>[!IMPORTANT]
>컨테이너 작업에서 stdout, stderr 및 환경 변수를 수집 하는 데 지원 되는 최소 에이전트 버전은 ciprod06142019 이상입니다. 스크랩 프로메테우스 메트릭에 대해 지원 되는 최소 에이전트 버전은 ciprod07092019 이상입니다. 에이전트 버전을 확인 하려면 **노드** 탭에서 노드를 선택 하 고 속성 창에서 **에이전트 이미지 태그** 속성의 값을 확인 합니다.  

### <a name="data-collection-settings"></a>데이터 컬렉션 설정

다음은 데이터 수집을 제어 하도록 구성할 수 있는 설정입니다.

|Key |데이터 형식 |값 |설명 |
|----|----------|------|------------|
|`schema-version` |문자열 (대/소문자 구분) |v1 |이 ConfigMap을 구문 분석할 때 에이전트에서 사용 하는 스키마 버전입니다. 현재 지원 되는 스키마 버전은 v1입니다. 이 값 수정은 지원 되지 않으며 ConfigMap이 평가 될 때 거부 됩니다.|
|`config-version` |문자열 | | 소스 제어 시스템/리포지토리에서이 구성 파일의 버전을 추적 하는 기능을 지원 합니다. 허용 되는 최대 문자 수는 10이 고 다른 모든 문자는 잘립니다. |
|`[log_collection_settings.stdout] enabled =` |Boolean | true 또는 false | Stdout 컨테이너 로그 수집이 설정 되었는지 여부를 제어 합니다. 로 `true` 설정 되 고 stdout 로그 컬렉션에 대 한 네임 스페이스가 제외`log_collection_settings.stdout.exclude_namespaces` 되지 않은 경우 (아래 설정) 클러스터의 모든 pod/노드에 있는 모든 컨테이너에서 stdout 로그가 수집 됩니다. ConfigMaps에 지정 되지 않은 경우 기본값 `enabled = true`은입니다. |
|`[log_collection_settings.stdout] exclude_namespaces =`|문자열 | 쉼표로 구분 된 배열 |Stdout 로그가 수집 되지 않을 Kubernetes 네임 스페이스의 배열입니다. 이 설정은가로 설정 된 `log_collection_settings.stdout.enabled` 경우에 `true`만 적용 됩니다. ConfigMap에 지정 되지 않은 경우 기본값 `exclude_namespaces = ["kube-system"]`은입니다.|
|`[log_collection_settings.stderr] enabled =` |Boolean | true 또는 false |Stderr 컨테이너 로그 수집이 사용 되는지 여부를 제어 합니다. 로 `true` 설정 된 경우 stdout 로그 수집 (`log_collection_settings.stderr.exclude_namespaces` 설정)에 대 한 네임 스페이스가 제외 되지 않으면 클러스터의 모든 pod/노드의 모든 컨테이너에서 stderr 로그가 수집 됩니다. ConfigMaps에 지정 되지 않은 경우 기본값 `enabled = true`은입니다. |
|`[log_collection_settings.stderr] exclude_namespaces =` |문자열 |쉼표로 구분 된 배열 |Stderr 로그가 수집 되지 않을 Kubernetes 네임 스페이스의 배열입니다. 이 설정은가로 설정 된 `log_collection_settings.stdout.enabled` 경우에 `true`만 적용 됩니다. ConfigMap에 지정 되지 않은 경우 기본값 `exclude_namespaces = ["kube-system"]`은입니다. |
| `[log_collection_settings.env_var] enabled =` |Boolean | true 또는 false | 환경 변수 컬렉션을 사용할 수 있는지 여부를 제어 합니다. 로 `false`설정 되 면 클러스터의 모든 pod/노드에서 실행 중인 모든 컨테이너에 대해 환경 변수가 수집 되지 않습니다. ConfigMap에 지정 되지 않은 경우 기본값 `enabled = true`은입니다. |

### <a name="prometheus-scraping-settings"></a>프로메테우스 스크랩 설정

![프로메테우스 용 컨테이너 모니터링 아키텍처](./media/container-insights-agent-config/monitoring-kubernetes-architecture.png)

컨테이너에 대 한 Azure Monitor는 다음 표와 같이 다음 메커니즘을 통해 여러 스크랩에서 프로메테우스 메트릭을 수집할 수 있는 원활한 환경을 제공 합니다. 메트릭은 컨테이너 작업에서 stdout, stderr 및 환경 변수 컬렉션을 구성 하는 데 사용 되는 것과 동일한 파일인 단일 ConfigMap 파일에 지정 된 설정 집합을 통해 수집 됩니다. 

프로메테우스의 활성 메트릭 스크랩는 다음 두 가지 관점 중 하나에서 수행 됩니다.

* 클러스터 전반의 HTTP URL로, 서비스의 나열 된 끝점에서 대상을 검색 하 고, kube와 같은 k8s 서비스를 검색 하 고, 응용 프로그램과 관련 된 pod 주석을 검색 합니다. 이 컨텍스트에서 수집 된 메트릭은 ConfigMap 섹션 *[프로메테우스 data_collection_settings]* 에 정의 됩니다.
* 노드 전반의 HTTP URL로, 서비스의 나열 된 끝점에서 대상을 검색 합니다. 이 컨텍스트에서 수집 된 메트릭은 ConfigMap 섹션 *[Prometheus_data_collection_settings]* 에 정의 됩니다.

| 엔드포인트 | Scope | 예제 |
|----------|-------|---------|
| Pod 주석 | 클러스터 전체 | 달 <br>`prometheus.io/scrape: "true"` <br>`prometheus.io/path: "/mymetrics"` <br>`prometheus.io/port: "8000" <br>prometheus.io/scheme: "http"` |
| Kubernetes Service | 클러스터 전체 | `http://my-service-dns.my-namespace:9100/metrics` <br>`https://metrics-server.kube-system.svc.cluster.local/metrics` |
| url/끝점 | 노드당 및/또는 클러스터 전체 | `http://myurl:9101/metrics` |

URL을 지정 하면 Azure Monitor 컨테이너의 경우에만 끝점을 스크랩 합니다. Kubernetes service를 지정 하면 서비스 이름이 클러스터 DNS 서버를 통해 확인 되어 IP 주소를 가져온 다음 확인 된 서비스가 스크랩 됩니다.

|Scope | Key | 데이터 형식 | 값 | 설명 |
|------|-----|-----------|-------|-------------|
| 클러스터 전체 | | | | 다음 세 가지 방법 중 하나를 지정 하 여 메트릭에 대 한 끝점을 스크랩. |
| | `urls` | 문자열 | 쉼표로 구분 된 배열 | HTTP 끝점 (IP 주소 또는 올바른 URL 경로 중 하나). 예를 들어 `urls=[$NODE_IP/metrics]`을 참조하십시오. $NODE _IP는 컨테이너 매개 변수에 대 한 특정 Azure Monitor 이며 노드 IP 주소 대신 사용할 수 있습니다. 모두 대문자 여야 합니다. |
| | `kubernetes_services` | 문자열 | 쉼표로 구분 된 배열 | Kube에서 메트릭을 스크랩 하는 Kubernetes services의 배열입니다. 예를`kubernetes_services = ["https://metrics-server.kube-system.svc.cluster.local/metrics", http://my-service-dns.my-namespace:9100/metrics]`들면입니다.|
| | `monitor_kubernetes_pods` | Boolean | true 또는 false | 클러스터 전체 설정 `true` 에서로 설정 된 경우 컨테이너 에이전트의 Azure Monitor는 다음의 프로메테우스 주석을 위해 전체 클러스터에서 Kubernetes pod를 스크랩 합니다.<br> `prometheus.io/scrape:`<br> `prometheus.io/scheme:`<br> `prometheus.io/path:`<br> `prometheus.io/port:` |
| | `prometheus.io/scrape` | Boolean | true 또는 false | Pod의 스크랩를 사용 하도록 설정 합니다. `monitor_kubernetes_pods`은 `true`로 설정해야 합니다. |
| | `prometheus.io/scheme` | 문자열 | http 또는 https | 기본값은 HTTP over scrapping입니다. 필요한 경우 `https`으로 설정 합니다. | 
| | `prometheus.io/path` | 문자열 | 쉼표로 구분 된 배열 | 메트릭을 페치할 HTTP 리소스 경로입니다. 메트릭 경로가이 아닌 `/metrics`경우에는이 주석을 사용 하 여 정의 합니다. |
| | `prometheus.io/port` | 문자열 | 9102 | 수신 대기할 포트를 지정 합니다. 포트가 설정 되지 않은 경우 기본값은 9102입니다. |
| 노드 차원 | `urls` | 문자열 | 쉼표로 구분 된 배열 | HTTP 끝점 (IP 주소 또는 올바른 URL 경로 중 하나). 예를 들어 `urls=[$NODE_IP/metrics]`을 참조하십시오. $NODE _IP는 컨테이너 매개 변수에 대 한 특정 Azure Monitor 이며 노드 IP 주소 대신 사용할 수 있습니다. 모두 대문자 여야 합니다. |
| 노드 전체 또는 클러스터 전체 | `interval` | 문자열 | 60 초 | 컬렉션 간격 기본값은 1 분 (60 초)입니다. *[Prometheus_data_collection_settings]* 및/또는 *[prometheus_data_collection_settings]* 에 대 한 컬렉션을 시간 단위 (예: ns, us (또는 Âμs), ms, s, m, h)로 수정할 수 있습니다. |
| 노드 전체 또는 클러스터 전체 | `fieldpass`<br> `fielddrop`| 문자열 | 쉼표로 구분 된 배열 | 허용 (`fieldpass`) 및 허용 안 함 (`fielddrop`) 목록을 설정 하 여 끝점에서 수집할 특정 메트릭을 지정할 수 있습니다. 먼저 허용 목록을 설정 해야 합니다. |

ConfigMap은 전역 목록이 며 에이전트에 하나의 ConfigMap만 적용 될 수 있습니다. 컬렉션에서 다른 ConfigMap을 과도 하 게 사용할 수 없습니다.

## <a name="configure-and-deploy-configmaps"></a>ConfigMaps 구성 및 배포

ConfigMap 구성 파일을 구성 하 고 클러스터에 배포 하려면 다음 단계를 수행 합니다.

1. 템플릿 ConfigMap yaml 파일을 [다운로드](https://github.com/microsoft/OMS-docker/blob/ci_feature_prod/Kubernetes/container-azm-ms-agentconfig.yaml) 하 고 azm로 저장 합니다.  
1. 사용자 지정 항목으로 ConfigMap yaml 파일을 편집 합니다.

    - Stdout 로그 컬렉션에 대 한 특정 네임 스페이스를 제외 하려면 다음 예제 `[log_collection_settings.stdout] enabled = true exclude_namespaces = ["my-namespace-1", "my-namespace-2"]`를 사용 하 여 키/값을 구성 합니다.
    
    - 특정 컨테이너에 대해 환경 변수 컬렉션을 사용 하지 않도록 설정 하려면 키/ `[log_collection_settings.env_var] enabled = true` 값을 설정 하 여 전역으로 변수 컬렉션을 사용 하도록 설정 하 고 [여기](container-insights-manage-agent.md#how-to-disable-environment-variable-collection-on-a-container) 의 단계에 따라 특정 컨테이너에 대 한 구성을 완료 합니다.
    
    - Stderr 로그 수집 클러스터 전체를 사용 하지 않도록 설정 하려면 다음 예제 `[log_collection_settings.stderr] enabled = false`를 사용 하 여 키/값을 구성 합니다.
    
    - 다음 예에서는 클러스터 전체에서, 에이전트의 DameonSet 노드 전체에서, pod 주석을 지정 하 여 ConfigMap 파일 메트릭을 구성 하는 방법을 보여 줍니다.

        - 클러스터 전체에서 특정 URL의 프로메테우스 메트릭을 스크랩 합니다.

        ```
         prometheus-data-collection-settings: |- 
         # Custom Prometheus metrics data collection settings
         [prometheus_data_collection_settings.cluster] 
         interval = "1m"  ## Valid time units are ns, us (or µs), ms, s, m, h.
         fieldpass = ["metric_to_pass1", "metric_to_pass12"] ## specify metrics to pass through 
         fielddrop = ["metric_to_drop"] ## specify metrics to drop from collecting
         urls = ["http://myurl:9101/metrics"] ## An array of urls to scrape metrics from
        ```

        - 클러스터의 모든 노드에서 실행 되는 에이전트의 DaemonSet에서 프로메테우스 메트릭을 스크랩 합니다.

        ```
         prometheus-data-collection-settings: |- 
         # Custom Prometheus metrics data collection settings 
         [prometheus_data_collection_settings.node] 
         interval = "1m"  ## Valid time units are ns, us (or µs), ms, s, m, h. 
         # Node level scrape endpoint(s). These metrics will be scraped from agent's DaemonSet running in every node in the cluster 
         urls = ["http://$NODE_IP:9103/metrics"] 
         fieldpass = ["metric_to_pass1", "metric_to_pass2"] 
         fielddrop = ["metric_to_drop"] 
        ```

        - Pod 주석을 지정 하 여 프로메테우스 메트릭을 스크랩 합니다.

        ```
         prometheus-data-collection-settings: |- 
         # Custom Prometheus metrics data collection settings
         [prometheus_data_collection_settings.cluster] 
         interval = "1m"  ## Valid time units are ns, us (or µs), ms, s, m, h
         monitor_kubernetes_pods = true #replicaset will scrape Kubernetes pods for the following prometheus annotations: 
          - prometheus.io/scrape:"true" #Enable scraping for this pod 
          - prometheus.io/scheme:"http:" #If the metrics endpoint is secured then you will need to set this to `https`, if not default ‘http’
          - prometheus.io/path:"/mymetrics" #If the metrics path is not /metrics, define it with this annotation. 
          - prometheus.io/port:"8000" #If port is not 9102 use this annotation
        ```

1. 다음 kubectl 명령을 `kubectl apply -f <configmap_yaml_file.yaml>`실행 하 여 configmap을 만듭니다.
    
    예: `kubectl apply -f container-azm-ms-agentconfig.yaml`. 
    
    구성 변경 내용을 적용 하기 전에 완료 하는 데 몇 분 정도 걸릴 수 있으며, 클러스터의 모든 omsagent pod가 다시 시작 됩니다. 다시 시작은 모든 omsagent pod에 대 한 롤링 다시 시작 이지만 동시에 다시 시작 되지 않습니다. 다시 시작이 완료 되 면 다음과 유사한 메시지가 표시 되 고 결과가 `configmap "container-azm-ms-agentconfig" created`포함 됩니다.

구성이 성공적으로 적용 되었는지 확인 하려면 다음 명령을 사용 하 여 에이전트 pod `kubectl logs omsagent-fdf58 -n=kube-system`에서 로그를 검토 합니다. Omsagent pod의 구성 오류가 있으면 출력에 다음과 유사한 오류가 표시 됩니다.

``` 
***************Start Config Processing******************** 
config::unsupported/missing config schema version - 'v21' , using defaults
```

또한 프로메테우스의 구성 변경 적용과 관련 된 오류를 검토할 수 있습니다.  동일한 `kubectl logs` 명령 또는 라이브 로그를 사용 하 여 에이전트 pod의 로그를 사용 합니다. 라이브 로그에는 다음과 비슷한 오류가 표시 됩니다.

```
2019-07-08T18:55:00Z E! [inputs.prometheus]: Error in plugin: error making HTTP request to http://invalidurl:1010/metrics: Get http://invalidurl:1010/metrics: dial tcp: lookup invalidurl on 10.0.0.10:53: no such host
```

오류가 발생 하면 omsagent에서 파일을 구문 분석 하 여 다시 시작 되 고 기본 구성을 사용 하 게 됩니다. ConfigMap에서 오류를 수정한 후에는 yaml 파일을 저장 하 고 명령을 `kubectl apply -f <configmap_yaml_file.yaml`실행 하 여 업데이트 된 configmap을 적용 합니다.

## <a name="applying-updated-configmap"></a>업데이트 된 ConfigMap 적용

클러스터에 ConfigMap을 이미 배포 했 고 최신 구성으로 업데이트 하려는 경우 이전에 사용 했던 ConfigMap 파일을 편집한 다음 이전과 동일한 명령을 사용 하 여 적용할 수 있습니다. `kubectl apply -f <configmap_yaml_file.yaml`.

구성 변경 내용을 적용 하기 전에 완료 하는 데 몇 분 정도 걸릴 수 있으며, 클러스터의 모든 omsagent pod가 다시 시작 됩니다. 다시 시작은 모든 omsagent pod에 대 한 롤링 다시 시작 이지만 동시에 다시 시작 되지 않습니다. 다시 시작이 완료 되 면 다음과 유사한 메시지가 표시 되 고 결과가 `configmap "container-azm-ms-agentconfig" updated`포함 됩니다.

## <a name="verifying-schema-version"></a>스키마 버전 확인

지원 되는 구성 스키마 버전은 omsagent pod에서 pod 주석 (스키마 버전)으로 사용할 수 있습니다. 다음 kubectl 명령을 사용 하 여 볼 수 있습니다.`kubectl describe pod omsagent-fdf58 -n=kube-system`

출력은 주석 스키마 버전을 사용 하는 다음과 유사 하 게 표시 됩니다.

```
    Name:           omsagent-fdf58
    Namespace:      kube-system
    Node:           aks-agentpool-95673144-0/10.240.0.4
    Start Time:     Mon, 10 Jun 2019 15:01:03 -0700
    Labels:         controller-revision-hash=589cc7785d
                    dsName=omsagent-ds
                    pod-template-generation=1
    Annotations:    agentVersion=1.10.0.1
                  dockerProviderVersion=5.0.0-0
                    schema-versions=v1 
```

## <a name="review-prometheus-data-usage"></a>프로메테우스 데이터 사용 검토

Azure Monitor으로 스크랩 된 프로메테우스 메트릭을 보려면 "프로메테우스"를 네임 스페이스로 지정 합니다. @No__t-0 kubernetes 네임 스페이스에서 프로메테우스 메트릭을 보는 샘플 쿼리는 다음과 같습니다.

```
InsightsMetrics 
| where Namespace contains "prometheus"
| extend tags=parse_json(Tags)
| where tostring(tags.namespace) == "default" 
```

또한 이름으로 프로메테우스 데이터를 직접 쿼리할 수 있습니다.

```
InsightsMetrics 
| where Name contains "some_prometheus_metric"
```

각 메트릭의 수집 볼륨 크기를 매일 GB 단위로 식별 하 여 높은 경우에는 다음 쿼리를 제공 합니다.

```
InsightsMetrics 
| where Namespace contains "prometheus"
| where TimeGenerated > ago(24h)
| summarize VolumeInGB = (sum(_BilledSize) / (1024 * 1024 * 1024)) by Name
| order by VolumeInGB desc
| render barchart
```
출력은 다음과 유사한 결과를 표시 합니다.

![데이터 수집 볼륨의 쿼리 결과를 기록 합니다.](./media/container-insights-agent-config/log-query-example-usage-03.png)

작업 영역에서 수신 되는 데이터 수집의 양이 높은 경우에는 한 달에 각 메트릭 크기 (GB)가 무엇 인지 파악 하기 위해 다음 쿼리가 제공 됩니다.

```
InsightsMetrics 
| where Namespace contains "prometheus"
| where TimeGenerated > ago(24h)
| summarize EstimatedGBPer30dayMonth = (sum(_BilledSize) / (1024 * 1024 * 1024)) * 30 by Name
| order by EstimatedGBPer30dayMonth desc
| render barchart
```

출력은 다음과 유사한 결과를 표시 합니다.

![데이터 수집 볼륨의 쿼리 결과를 기록 합니다.](./media/container-insights-agent-config/log-query-example-usage-02.png)

데이터 사용량을 모니터링 하 고 비용을 분석 하는 방법에 대 한 자세한 내용은 [Azure Monitor 로그를 사용 하 여 사용량 및 비용 관리](../platform/manage-cost-storage.md)에서 확인할 수 있습니다.

## <a name="next-steps"></a>다음 단계

컨테이너의 Azure Monitor에는 미리 정의 된 경고 집합이 포함 되지 않습니다. [컨테이너에 대 한 Azure Monitor를 사용 하 여 성능 경고 만들기](container-insights-alerts.md) 를 검토 하 여 devops 또는 운영 프로세스 및 절차를 지원 하기 위해 높은 CPU 및 메모리 사용률에 대해 권장 되는 경고를 만드는 방법을 알아봅니다.

- Azure Monitor를 사용하고 AKS 클러스터의 다른 측면을 모니터링하는 방법을 계속 알아보려면 [Azure Kubernetes 서비스 상태](container-insights-analyze.md)를 참조하세요.

- [로그 쿼리 예](container-insights-log-search.md#search-logs-to-analyze-data) 를 확인 하 여 미리 정의 된 쿼리 및 예제를 확인 하거나 사용자 지정 하 여 클러스터에 대 한 경고, 시각화 또는 분석을 평가 하거나 사용자 지정 합니다.
