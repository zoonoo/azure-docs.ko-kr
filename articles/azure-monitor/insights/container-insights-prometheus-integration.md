---
title: 컨테이너 프로메테우스 통합을 위한 Azure 모니터 구성 | 마이크로 소프트 문서
description: 이 문서에서는 Kubernetes 클러스터를 사용하여 Prometheus의 메트릭을 스크레이핑하도록 컨테이너 에이전트에 대한 Azure 모니터를 구성하는 방법에 대해 설명합니다.
ms.topic: conceptual
ms.date: 04/16/2020
ms.openlocfilehash: 7fcf52cceb69834f68f8e4ce7a2674972a6430fd
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537375"
---
# <a name="configure-scraping-of-prometheus-metrics-with-azure-monitor-for-containers"></a>컨테이너에 대한 Azure 모니터를 사용 하 고 프로메테우스 메트릭의 스크래핑 구성

[프로메테우스는](https://prometheus.io/) 인기있는 오픈 소스 메트릭 모니터링 솔루션이며 [클라우드 네이티브 컴퓨팅 재단의](https://www.cncf.io/)일부입니다. 컨테이너용 Azure Monitor는 Prometheus 메트릭을 수집하는 원활한 온보딩 환경을 제공합니다. 일반적으로 프로메테우스를 사용하려면 스토어에서 프로메테우스 서버를 설정하고 관리해야 합니다. Azure 모니터와 통합하면 프로메테우스 서버가 필요하지 않습니다. 내보내기 또는 포드(응용 프로그램)를 통해 Prometheus 메트릭 끝점을 노출하기만 하면 되며 컨테이너용 Azure Monitor의 컨테이너화된 에이전트는 메트릭을 스크레이핑할 수 있습니다. 

![프로메테우스용 컨테이너 모니터링 아키텍처](./media/container-insights-prometheus-integration/monitoring-kubernetes-architecture.png)

>[!NOTE]
>Prometheus 메트릭을 스크래핑하기 위해 지원되는 최소 에이전트 버전은 ciprod07092019 이상이며 `KubeMonAgentEvents` 테이블의 구성 및 에이전트 오류를 작성하는 데 지원되는 에이전트 버전은 ciprod10112019입니다. 에이전트 버전 및 각 릴리스에 포함된 내용에 대한 자세한 내용은 [에이전트 릴리스 정보를](https://github.com/microsoft/Docker-Provider/tree/ci_feature_prod)참조하십시오. 에이전트 버전을 확인하려면 **노드** 탭에서 노드를 선택하고 속성 창에서 에이전트 **이미지 태그** 속성의 메모 값을 선택합니다.

Prometheus 메트릭의 스크래핑은 다음에서 호스팅되는 Kubernetes 클러스터를 통해 지원됩니다.

- AKS(Azure Kubernetes Service)
- Azure 스택 또는 온-프레미스
- Azure Red Hat OpenShift

>[!NOTE]
>Azure Red Hat OpenShift의 경우 템플릿 ConfigMap 파일이 *오픈시프트-azure 로깅* 네임스페이스에 만들어집니다. 에이전트에서 메트릭 또는 데이터 수집을 적극적으로 스크레이핑하도록 구성되지 않았습니다.
>

## <a name="azure-red-hat-openshift-prerequisites"></a>Azure 레드햇 오픈시프트 전제 조건

시작하기 전에 Azure Red Hat OpenShift 클러스터의 고객 클러스터 관리자 역할의 구성원으로 컨테이너화된 에이전트 및 Prometheus 스크레이핑 설정을 구성하는지 확인합니다. *osa-고객-관리자* 그룹의 구성원인지 확인하려면 다음 명령을 실행합니다.

``` bash
  oc get groups
```

출력은 다음과 유사합니다.

``` bash
NAME                  USERS
osa-customer-admins   <your-user-account>@<your-tenant-name>.onmicrosoft.com
```

*osa-고객-관리자* 그룹의 구성원인 경우 다음 명령을 사용하여 `container-azm-ms-agentconfig` ConfigMap을 나열할 수 있습니다.

``` bash
oc get configmaps container-azm-ms-agentconfig -n openshift-azure-logging
```

출력은 다음과 유사합니다.

``` bash
NAME                           DATA      AGE
container-azm-ms-agentconfig   4         56m
```

### <a name="prometheus-scraping-settings"></a>프로메테우스 스크레이핑 설정

Prometheus의 메트릭의 활성 스크래핑은 다음 두 가지 관점 중 하나에서 수행됩니다.

* 클러스터 전체 - HTTP URL 및 서비스의 나열된 끝점에서 대상을 검색합니다. 예를 들어 kube-dns 및 kube-state-metrics와 같은 k8s 서비스 및 응용 프로그램에 특정한 포드 주석입니다. 이 컨텍스트에서 수집된 메트릭은 ConfigMap 섹션 *[프로메테우스 data_collection_settings.cluster]* 에서 정의됩니다.
* 노드 전체 - HTTP URL 및 서비스의 나열된 끝점에서 대상을 검색합니다. 이 컨텍스트에서 수집된 메트릭은 ConfigMap 섹션 *[Prometheus_data_collection_settings.node]에*정의됩니다.

| 엔드포인트 | 범위 | 예제 |
|----------|-------|---------|
| 포드 추가 | 클러스터 전체 | 주석: <br>`prometheus.io/scrape: "true"` <br>`prometheus.io/path: "/mymetrics"` <br>`prometheus.io/port: "8000"` <br>`prometheus.io/scheme: "http"` |
| Kubernetes 서비스 | 클러스터 전체 | `http://my-service-dns.my-namespace:9100/metrics` <br>`https://metrics-server.kube-system.svc.cluster.local/metrics` |
| URL/끝점 | 노드별 및/또는 클러스터 전체 | `http://myurl:9101/metrics` |

URL을 지정하면 컨테이너에 대한 Azure Monitor는 끝점만 긁어냅니다. Kubernetes 서비스를 지정하면 서비스 이름이 클러스터 DNS 서버로 확인되어 IP 주소를 얻은 다음 해결된 서비스가 스크랩됩니다.

|범위 | 키 | 데이터 형식 | 값 | Description |
|------|-----|-----------|-------|-------------|
| 클러스터 전체 | | | | 메트릭에 대한 끝점을 스크레이핑할 다음 세 가지 방법 중 하나를 지정합니다. |
| | `urls` | String | 쉼표 로 구분된 배열 | HTTP 끝점(IP 주소 또는 유효한 URL 경로 지정). 예: `urls=[$NODE_IP/metrics]` ($NODE_IP는 컨테이너 매개 변수에 대 한 특정 Azure 모니터 이며 노드 IP 주소 대신 사용할 수 있습니다. 모든 대문자여야 합니다.) |
| | `kubernetes_services` | String | 쉼표 로 구분된 배열 | kube 상태 메트릭에서 메트릭을 스크레이핑하는 Kubernetes 서비스의 배열입니다. 예를 들면 `kubernetes_services = ["https://metrics-server.kube-system.svc.cluster.local/metrics",http://my-service-dns.my-namespace:9100/metrics]`입니다.|
| | `monitor_kubernetes_pods` | 부울 | true 또는 false | 클러스터 전체 `true` 설정에서 설정하면 컨테이너 에이전트용 Azure Monitor는 다음 Prometheus 주석에 대해 전체 클러스터에서 Kubernetes 포드를 긁어냅니다.<br> `prometheus.io/scrape:`<br> `prometheus.io/scheme:`<br> `prometheus.io/path:`<br> `prometheus.io/port:` |
| | `prometheus.io/scrape` | 부울 | true 또는 false | 포드를 긁을 수 있습니다. `monitor_kubernetes_pods`은 `true`로 설정해야 합니다. |
| | `prometheus.io/scheme` | String | HTTP 또는 HTTPS | 기본값은 HTTP를 통해 스크랩됩니다. 필요한 경우 로 `https`설정합니다. | 
| | `prometheus.io/path` | String | 쉼표 로 구분된 배열 | 메트릭을 가져올 HTTP 리소스 경로입니다. 메트릭 경로가 아닌 `/metrics`경우 이 추가로 정의합니다. |
| | `prometheus.io/port` | String | 9102 | 스크레이핑할 포트를 지정합니다. 포트가 설정되지 않은 경우 기본값은 9102로 설정됩니다. |
| | `monitor_kubernetes_pods_namespaces` | String | 쉼표 로 구분된 배열 | Kubernetes 포드에서 메트릭을 스크레이핑할 수 있는 네임스페이스 목록입니다.<br> 예를 들어 `monitor_kubernetes_pods_namespaces = ["default1", "default2", "default3"]` |
| 노드 전체 | `urls` | String | 쉼표 로 구분된 배열 | HTTP 끝점(IP 주소 또는 유효한 URL 경로 지정). 예: `urls=[$NODE_IP/metrics]` ($NODE_IP는 컨테이너 매개 변수에 대 한 특정 Azure 모니터 이며 노드 IP 주소 대신 사용할 수 있습니다. 모든 대문자여야 합니다.) |
| 노드 전체 또는 클러스터 전체 | `interval` | String | 60대 | 컬렉션 간격 기본값은 1분(60초)입니다. *[prometheus_data_collection_settings.node]* 및/또는 *[prometheus_data_collection_settings.cluster]에* 대한 컬렉션을 s, m, h와 같은 시간 단위로 수정할 수 있습니다. |
| 노드 전체 또는 클러스터 전체 | `fieldpass`<br> `fielddrop`| String | 쉼표 로 구분된 배열 | 허용()`fieldpass`및 허용 허용()`fielddrop`목록을 설정하여 끝점에서 수집할 특정 메트릭을 지정할 수 있습니다. 허용 목록을 먼저 설정해야 합니다. |

ConfigMaps는 전역 목록이며 에이전트에 하나의 ConfigMap만 적용할 수 있습니다. 컬렉션을 덮는 다른 ConfigMaps를 가질 수 없습니다.

## <a name="configure-and-deploy-configmaps"></a>구성 맵 구성 및 배포

다음 단계를 수행하여 Kubernetes 클러스터에 대한 ConfigMap 구성 파일을 구성합니다.

1. 템플릿 ConfigMap yaml 파일을 [다운로드하고](https://github.com/microsoft/OMS-docker/blob/ci_feature_prod/Kubernetes/container-azm-ms-agentconfig.yaml) 컨테이너 azm-ms-agentconfig.yaml로 저장합니다.

   >[!NOTE]
   >ConfigMap 템플릿이 클러스터에 이미 있으므로 Azure Red Hat OpenShift로 작업할 때이 단계는 필요하지 않습니다.

2. Prometheus 메트릭을 스크레이핑하려면 사용자 지정으로 ConfigMap yaml 파일을 편집합니다. Azure Red Hat OpenShift에 대한 ConfigMap yaml 파일을 편집하는 경우 먼저 명령을 `oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging` 실행하여 텍스트 편집기에서 파일을 엽니다.

    >[!NOTE]
    >조정을 방지하기 `openshift.io/reconcile-protect: "true"` 위해 *컨테이너-azm-ms-agentconfig* ConfigMap의 메타데이터 아래에 다음 추가를 추가해야 합니다. 
    >```
    >metadata:
    >   annotations:
    >       openshift.io/reconcile-protect: "true"
    >```

    - Kubernetes 서비스 클러스터 전체에서 수집하려면 다음 예제를 사용하여 ConfigMap 파일을 구성합니다.

        ```
        prometheus-data-collection-settings: |- 
        # Custom Prometheus metrics data collection settings
        [prometheus_data_collection_settings.cluster] 
        interval = "1m"  ## Valid time units are s, m, h.
        fieldpass = ["metric_to_pass1", "metric_to_pass12"] ## specify metrics to pass through 
        fielddrop = ["metric_to_drop"] ## specify metrics to drop from collecting
        kubernetes_services = ["http://my-service-dns.my-namespace:9102/metrics"]
        ```

    - 클러스터 전체의 특정 URL에서 Prometheus 메트릭을 스크래핑하도록 구성하려면 다음 예제를 사용하여 ConfigMap 파일을 구성합니다.

        ```
        prometheus-data-collection-settings: |- 
        # Custom Prometheus metrics data collection settings
        [prometheus_data_collection_settings.cluster] 
        interval = "1m"  ## Valid time units are s, m, h.
        fieldpass = ["metric_to_pass1", "metric_to_pass12"] ## specify metrics to pass through 
        fielddrop = ["metric_to_drop"] ## specify metrics to drop from collecting
        urls = ["http://myurl:9101/metrics"] ## An array of urls to scrape metrics from
        ```

    - 클러스터의 모든 개별 노드에 대해 에이전트의 DaemonSet에서 Prometheus 메트릭을 스크래핑하도록 구성하려면 ConfigMap에서 다음을 구성합니다.
    
        ```
        prometheus-data-collection-settings: |- 
        # Custom Prometheus metrics data collection settings 
        [prometheus_data_collection_settings.node] 
        interval = "1m"  ## Valid time units are s, m, h. 
        urls = ["http://$NODE_IP:9103/metrics"] 
        fieldpass = ["metric_to_pass1", "metric_to_pass2"] 
        fielddrop = ["metric_to_drop"] 
        ```

        >[!NOTE]
        >$NODE_IP는 컨테이너 매개 변수에 대한 특정 Azure 모니터이며 노드 IP 주소 대신 사용할 수 있습니다. 모든 대문자여야 합니다. 

    - 포드 별칭을 지정하여 Prometheus 메트릭의 스크래핑을 구성하려면 다음 단계를 수행합니다.

       1. 구성 맵에서 다음을 지정합니다.

            ```
            prometheus-data-collection-settings: |- 
            # Custom Prometheus metrics data collection settings
            [prometheus_data_collection_settings.cluster] 
            interval = "1m"  ## Valid time units are s, m, h
            monitor_kubernetes_pods = true 
            ```

       2. 포드 주석에 대해 다음 구성을 지정합니다.

           ```
           - prometheus.io/scrape:"true" #Enable scraping for this pod 
           - prometheus.io/scheme:"http:" #If the metrics endpoint is secured then you will need to set this to `https`, if not default ‘http’
           - prometheus.io/path:"/mymetrics" #If the metrics path is not /metrics, define it with this annotation. 
           - prometheus.io/port:"8000" #If port is not 9102 use this annotation
           ```
    
          주석이 있는 포드의 특정 네임스페이스로 모니터링을 제한하려는 경우(예: 프로덕션 워크로드 전용 포드만 포함) ConfigMap에서 를 `monitor_kubernetes_pod` `true` `monitor_kubernetes_pods_namespaces` 설정하고 스크레이핑할 네임스페이스를 지정하는 네임스페이스 필터를 추가합니다. 예를 들어 `monitor_kubernetes_pods_namespaces = ["default1", "default2", "default3"]`

3. Azure Red Hat OpenShift 이외의 클러스터의 경우 다음 kubectl 명령을 `kubectl apply -f <configmap_yaml_file.yaml>`실행합니다.
    
    예: `kubectl apply -f container-azm-ms-agentconfig.yaml`. 

    Azure Red Hat OpenShift의 경우 변경 내용을 편집기에서 저장합니다.

구성 변경이 적용되기 전에 완료하는 데 몇 분 정도 걸릴 수 있으며 클러스터의 모든 omsagent 포드가 다시 시작됩니다. 다시 시작은 모든 omsagent 포드에 대한 롤링 다시 시작이며 동시에 다시 시작되지는 않습니다. 다시 시작이 완료되면 다음과 유사한 메시지가 표시되며 결과가 포함됩니다. `configmap "container-azm-ms-agentconfig" created`

명령을 실행하여 Azure Red Hat OpenShift에 대한 업데이트된 `oc describe configmaps container-azm-ms-agentconfig -n openshift-azure-logging`ConfigMap을 볼 수 있습니다. 

## <a name="applying-updated-configmap"></a>업데이트된 구성 맵 적용

클러스터에 ConfigMap을 이미 배포한 적이 있고 최신 구성으로 업데이트하려는 경우 이전에 사용했던 ConfigMap 파일을 편집한 다음 이전과 동일한 명령을 사용하여 적용할 수 있습니다.

Azure Red Hat OpenShift 이외의 Kubernetes 클러스터의 `kubectl apply -f <configmap_yaml_file.yaml`경우 명령을 실행합니다. 

Azure Red Hat OpenShift 클러스터의 `oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging` 경우 명령을 실행하여 기본 편집기에서 파일을 열어 수정한 다음 저장합니다.

구성 변경이 적용되기 전에 완료하는 데 몇 분 정도 걸릴 수 있으며 클러스터의 모든 omsagent 포드가 다시 시작됩니다. 다시 시작은 모든 omsagent 포드에 대한 롤링 다시 시작이며 동시에 다시 시작되지는 않습니다. 다시 시작이 완료되면 다음과 유사한 메시지가 표시되며 결과가 포함됩니다. `configmap "container-azm-ms-agentconfig" updated`

## <a name="verify-configuration"></a>구성 확인

구성이 클러스터에 성공적으로 적용되었는지 확인하려면 다음 명령을 사용하여 에이전트 포드에서 `kubectl logs omsagent-fdf58 -n=kube-system`로그를 검토합니다. 

>[!NOTE]
>이 명령은 Azure Red Hat OpenShift 클러스터에 적용되지 않습니다.
> 

omsagent 포드의 구성 오류가 있는 경우 출력에 다음과 유사한 오류가 표시됩니다.

``` 
***************Start Config Processing******************** 
config::unsupported/missing config schema version - 'v21' , using defaults
```

구성 변경 적용과 관련된 오류도 검토할 수 있습니다. 다음 옵션은 구성 변경 및 Prometheus 메트릭스크래핑에 대한 추가 문제 해결을 수행하는 데 사용할 수 있습니다.

- 동일한 `kubectl logs` 명령을 사용하는 에이전트 포드 로그에서 
    >[!NOTE]
    >이 명령은 Azure Red Hat OpenShift 클러스터에 적용되지 않습니다.
    > 

- 라이브 데이터에서 (미리보기). 라이브 데이터(미리 보기) 로그에는 다음과 유사한 오류가 표시됩니다.

    ```
    2019-07-08T18:55:00Z E! [inputs.prometheus]: Error in plugin: error making HTTP request to http://invalidurl:1010/metrics: Get http://invalidurl:1010/metrics: dial tcp: lookup invalidurl on 10.0.0.10:53: no such host
    ```

- 로그 분석 작업 영역의 **KubeMonAgentEvents** 테이블에서. 데이터는 스크레이핑 오류에 대한 *경고* 심각도 및 구성 오류에 대한 *오류* 심각도와 함께 매시간 전송됩니다. 오류가 없는 경우 테이블의 항목에는 오류 없음을 보고하는 심각도 *Info가*있는 데이터가 있습니다. **Tags** 속성에는 오류가 발생한 포드 및 컨테이너 ID에 대한 자세한 정보와 마지막 시간의 첫 번째 발생, 마지막 발생 및 개수에 대한 자세한 정보가 포함되어 있습니다.

- Azure Red Hat OpenShift의 경우 **ContainerLog** 테이블을 검색하여 옴에이전트 로그를 확인하여 openshift-azure-로깅의 로그 수집이 활성화되어 있는지 확인합니다.

오류로 인해 omsagent가 파일을 구문 분석하지 못하게 되어 파일을 다시 시작하고 기본 구성을 사용합니다. Azure Red Hat OpenShift가 아닌 클러스터의 ConfigMap에서 오류를 수정한 후 yaml 파일을 저장하고 명령을 실행하여 `kubectl apply -f <configmap_yaml_file.yaml`업데이트된 ConfigMaps를 적용합니다. 

Azure Red Hat OpenShift의 경우 명령을 실행하여 업데이트된 ConfigMaps를 편집하고 `oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging`저장합니다.

## <a name="query-prometheus-metrics-data"></a>프로메테우스 메트릭 데이터 쿼리

Azure Monitor에서 스크랩한 프로메테우스 메트릭과 에이전트가 보고한 구성/스크래핑 오류를 보려면 [쿼리 Prometheus 메트릭 데이터](container-insights-log-search.md#query-prometheus-metrics-data) 및 쿼리 구성 또는 [스크래핑 오류를](container-insights-log-search.md#query-config-or-scraping-errors)검토합니다.

## <a name="view-prometheus-metrics-in-grafana"></a>그라파나에서 프로메테우스 측정항목 보기

컨테이너용 Azure Monitor는 Grafana 대시보드의 로그 분석 작업 영역에 저장된 메트릭 보기를 지원합니다. 그라파나 [대시보드 리포지토리에서](https://grafana.com/grafana/dashboards?dataSource=grafana-azure-monitor-datasource&category=docker) 다운로드할 수 있는 템플릿을 제공하여 사용자 지정 Grafana 대시보드에서 시각화하기 위해 모니터링되는 클러스터에서 추가 데이터를 쿼리하는 방법을 알아보는 데 도움이 되는 참조를 제공합니다. 

## <a name="review-prometheus-data-usage"></a>프로메테우스 데이터 사용량 검토

각 메트릭 크기의 섭취 량을 하루에 GB로 식별하여 높은지 확인하려면 다음 쿼리가 제공됩니다.

```
InsightsMetrics 
| where Namespace == "prometheus"
| where TimeGenerated > ago(24h)
| summarize VolumeInGB = (sum(_BilledSize) / (1024 * 1024 * 1024)) by Name
| order by VolumeInGB desc
| render barchart
```
출력에는 다음과 유사한 결과가 표시됩니다.

![데이터 수집 볼륨의 쿼리 결과 로그](./media/container-insights-prometheus-integration/log-query-example-usage-03.png)

작업 영역에서 수신된 데이터의 양이 높은지 확인하기 위해 GB의 각 메트릭 크기를 한 달 동안 예측하려면 다음 쿼리가 제공됩니다.

```
InsightsMetrics 
| where Namespace contains "prometheus"
| where TimeGenerated > ago(24h)
| summarize EstimatedGBPer30dayMonth = (sum(_BilledSize) / (1024 * 1024 * 1024)) * 30 by Name
| order by EstimatedGBPer30dayMonth desc
| render barchart
```

출력에는 다음과 유사한 결과가 표시됩니다.

![데이터 수집 볼륨의 쿼리 결과 로그](./media/container-insights-prometheus-integration/log-query-example-usage-02.png)

데이터 사용량을 모니터링하고 비용을 분석하는 방법에 대한 자세한 내용은 [Azure Monitor Logs를 사용하여 사용량 및 비용 관리에서](../platform/manage-cost-storage.md)확인할 수 있습니다.

## <a name="next-steps"></a>다음 단계

여기에서 컨테이너 워크로드에서 stdout, stderr 및 환경 변수에 대한 에이전트 [here](container-insights-agent-config.md)컬렉션 설정을 구성하는 방법에 대해 자세히 알아보십시오. 
