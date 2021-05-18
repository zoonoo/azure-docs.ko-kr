---
title: AKS(Azure Kubernetes Service) 컨트롤러 로그 보기
description: AKS(Azure Kubernetes Service)에서 Kubernetes 컨트롤 플레인에 대한 로그 활성화 및 확인 방법 알아보기
services: container-service
ms.topic: article
ms.date: 01/27/2020
ms.openlocfilehash: 395422ca97b43488a7d7ad1c7434b20ccc59f2b0
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767294"
---
# <a name="enable-and-review-kubernetes-control-plane-logs-in-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Service)에서 Kubernetes 컨트롤 플레인 로그 활성화 및 검토

AKS(Azure Kubernetes Service)에서 *kube-apiserver* 및 *kube-controller-manager* 같은 컨트롤 플레인 구성 요소는 관리되는 서비스로 제공됩니다. *kubelet* 및 컨테이너 런타임을 실행하는 노드를 생성 및 관리하고, 관리되는 Kubernetes API 서버를 통해 애플리케이션을 배포합니다. 애플리케이션 및 서비스의 문제를 해결하려면 이러한 컨트롤 플레인 구성 요소에서 생성한 로그를 확인해야 합니다. 이 문서에서는 Azure Monitor 로그를 사용하여 Kubernetes 컨트롤 플레인 구성 요소의 로그를 활성화하고 쿼리하는 방법을 보여줍니다.

## <a name="before-you-begin"></a>시작하기 전에

이 문서의 경우 Azure 계정에서 실행 중인 기존 AKS 클러스터가 필요합니다. AKS 클러스터가 아직 없는 경우 [Azure CLI][cli-quickstart] 또는 [Azure Portal][portal-quickstart]을 사용하여 만듭니다. Azure Monitor 로그는Kubernetes RBAC, Azure RBAC 및 RBAC 미지원 AKS 클러스터 둘 다에서 작동합니다.

## <a name="enable-resource-logs"></a>리소스 로그 사용

Azure Monitor 로그는 여러 원본의 데이터를 수집하고 검토하는 데 도움이 되도록 환경에 대한 인사이트를 제공하는 분석 엔진과 쿼리 언어를 제공합니다. 작업 영역은 데이터를 정렬하고 분석하는 데 사용되며, Application Insights 및 Security Center와 같은 다른 Azure 서비스와 통합할 수 있습니다. 다른 플랫폼을 사용하여 로그를 분석하려면 Azure Storage 계정 또는 이벤트 허브로 리소스 로그를 보내는 옵션을 대신 선택할 수 있습니다. 자세한 내용은 [Azure Monitor 로그란?][log-analytics-overview]을 참조하세요.

Azure Monitor 로그는 Azure Portal에서 사용하도록 설정되고 관리됩니다. AKS 클러스터의 Kubernetes 컨트롤 플레인 구성 요소에 대한 로그 수집을 사용하도록 설정하려면 웹 브라우저에서 Azure Portal을 열고 다음 단계를 완료하세요.

1. AKS 클러스터의 리소스 그룹(예: *myResourceGroup*)을 선택합니다. 개별 AKS 클러스터 리소스가 포함된 리소스 그룹(예: *MC_myResourceGroup_myAKSCluster_eastus*)은 선택하지 마세요.

2. 왼쪽에서 **진단 설정** 을 선택합니다.

3. AKS 클러스터(예: *myAKSCluster*)를 선택한 후 **진단 설정 추가** 를 선택합니다.
  :::image type="content" source="media\view-control-plane-logs\select-add-diagnostic-setting.PNG" alt-text="진단 설정을 표시하는 브라우저 창에서 '진단 설정 추가'가 선택되어야 함을 나타내는 Azure Portal의 스크린샷":::

4. *myAKSClusterLogs* 등의 이름을 입력하고 **Log Analytics 작업 영역에 보내기** 옵션을 선택합니다.

5. 기존 작업 영역 또는 새 작업 영역 만들기를 선택합니다. 작업 영역을 만들어야 하는 경우, 작업 영역 이름, 리소스 그룹 및 위치를 지정합니다.

6. 사용 가능한 로그 목록에서 사용하도록 설정하려는 로그를 선택합니다. 이 예제에서는 *kube-audit* 및 *kube-audit-admin* 로그를 사용하도록 설정합니다. 일반적인 로그는 *kube-apiserver*, *kube-controller-manager* 및 *kube-scheduler* 를 포함합니다. Log Analytics 작업 영역이 사용하도록 설정되면 수집된 로그를 반환하고 변경할 수 있습니다.

7. 준비되면 **저장** 을 선택하여 선택된 로그의 수집을 사용하도록 설정합니다.
  :::image type="content" source="media\view-control-plane-logs\settings-selected.PNG" alt-text="Azure Portal의 '진단 설정 추가' 화면의 스크린샷'. Log Analytics 작업 영역으로 보내기 ' 및 'kube-audit' 및 'kube-audit-admin' 로그의 대상이 선택됩니다.":::

## <a name="log-categories"></a>로그 범주

Kubernetes에서 작성한 항목 외에도 프로젝트의 감사 로그에는 AKS의 항목이 있습니다.

감사 로그는 *kube-audit*, *kube-audit-admin* 및 *guard* 의 세 가지 범주로 기록됩니다.

- *kube-audit* 범주에는 *get*, *list*, *create*, *update*, *delete*, *patch* 및 *post* 등 모든 감사 이벤트에 대한 모든 감사 로그 데이터가 포함되어 있습니다.
- *kube-audit-admin* 범주는 *kube-audit* 로그 범주의 하위 집합입니다. *kube-audit-admin* 은 로그의 *get* 및 *list* 감사 이벤트를 제외하여 로그 수를 크게 줄입니다.
- *guard* 범주는 관리되는 azure AD 및 Azure RBAC 감사입니다. 관리되는 Azure AD의 경우: 토큰 입력, 사용자 정보 출력. Azure RBAC의 경우: 액세스 검토 입력 및 출력.

## <a name="schedule-a-test-pod-on-the-aks-cluster"></a>AKS 클러스터의 테스트 Pod 예약

일부 로그를 생성하려면 AKS 클러스터에 새 Pod를 만듭니다. 다음 예제의 YAML 매니페스트는 기본 NGINX 인스턴스를 만드는 데 사용할 수 있습니다. 원하는 편집기에서 `nginx.yaml`이라는 파일을 만들고 다음 내용을 붙여넣습니다.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  nodeSelector:
    "beta.kubernetes.io/os": linux
  containers:
  - name: mypod
    image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
    ports:
    - containerPort: 80
```

다음 예에 표시된 대로 [kubectl create][kubectl-create] 명령을 사용하여 Pod를 만들고 YAML 파일을 지정합니다.

```
$ kubectl create -f nginx.yaml

pod/nginx created
```

## <a name="view-collected-logs"></a>수집된 로그 보기

진단 로그를 사용하도록 설정하고 표시되기까지 최대 10분 정도 걸릴 수 있습니다.

> [!NOTE]
> 규정 준수 또는 기타 목적을 위해 모든 감사 로그 데이터가 필요한 경우 Blob Storage와 같은 저렴한 스토리지에 수집하여 저장합니다. *kube-audit-admin* 로그 범주를 사용하여 모니터링 및 경고 목적으로 의미 있는 감사 로그 데이터 집합을 수집하고 저장합니다.

Azure Portal에서 AKS 클러스터로 이동하여 왼쪽에 있는 **로그** 를 선택합니다. *예제 쿼리* 창이 나타나면 닫습니다.

왼쪽에서 **로그** 를 선택합니다. *kube-auditr* 로그를 보려면 텍스트 상자에 다음 쿼리를 입력합니다.

```
AzureDiagnostics
| where Category == "kube-audit"
| project log_s
```

많은 로그가 반환됩니다. 쿼리 범위를 좁혀서 이전 단계에서 만든 NGINX Pod에 대한 로그만 보려면 다음 쿼리 예제에 표시된 것처럼 *nginx* 를 검색하는 *where* 문을 추가합니다.

```
AzureDiagnostics
| where Category == "kube-audit"
| where log_s contains "nginx"
| project log_s
```

*kube-audit-admin* 로그를 보려면 텍스트 상자에 다음 쿼리를 입력합니다.

```
AzureDiagnostics
| where Category == "kube-audit-admin"
| project log_s
```

이 예제에서 쿼리는 *kube-audit-admin* 의 모든 만들기 작업을 보여줍니다. 이전 단계에서 만든 NGINX Pod에 대한 로그를 볼 수 있도록 쿼리 범위를 축소하기 위해 반환되는 결과가 많을 수 있습니다. 다음 예제 쿼리와 같이 *nginx* 를 검색하는 *where* 문을 추가합니다.

```
AzureDiagnostics
| where Category == "kube-audit-admin"
| where log_s contains "nginx"
| project log_s
```


로그 데이터를 쿼리하고 필터링하는 방법에 대한 자세한 내용은 [로그 분석 로그 검색을 사용하여 수집한 데이터 보기 또는 분석][analyze-log-analytics]을 참조하세요.

## <a name="log-event-schema"></a>로그 이벤트 스키마

AKS는 다음 이벤트를 기록합니다.

* [AzureActivity][log-schema-azureactivity]
* [AzureDiagnostics][log-schema-azurediagnostics]
* [AzureMetrics][log-schema-azuremetrics]
* [ContainerImageInventory][log-schema-containerimageinventory]
* [ContainerInventory][log-schema-containerinventory]
* [ContainerLog][log-schema-containerlog]
* [ContainerNodeInventory][log-schema-containernodeinventory]
* [ContainerServiceLog][log-schema-containerservicelog]
* [하트비트][log-schema-heartbeat]
* [InsightsMetrics][log-schema-insightsmetrics]
* [KubeEvents][log-schema-kubeevents]
* [KubeHealth][log-schema-kubehealth]
* [KubeMonAgentEvents][log-schema-kubemonagentevents]
* [KubeNodeInventory][log-schema-kubenodeinventory]
* [KubePodInventory][log-schema-kubepodinventory]
* [KubeServices][log-schema-kubeservices]
* [Perf][log-schema-perf]

## <a name="log-roles"></a>로그 역할

| 역할                     | 설명 |
|--------------------------|-------------|
| *aksService*             | hcpService에서 가져온 컨트롤 플레인 작업에 대한 감사 로그의 표시 이름 |
| *masterclient*           | az aks get-credentials에서 가져온 인증서인 MasterClientCertificate에 대한 감사 로그의 표시 이름 |
| *nodeclient*             | 에이전트 노드에서 사용하는 ClientCertificate의 표시 이름 |

## <a name="next-steps"></a>다음 단계

이 문서에서는 AKS 클러스터의 Kubernetes 컨트롤 플레인 구성 요소에 대한 로그를 사용하도록 설정하고 검토하는 방법을 알아보았습니다. 추가로 모니터링하고 문제를 해결하려면 [Kubelet 로그를 확인][kubelet-logs]하고 [SSH 노드 액세스를 사용하도록 설정][aks-ssh]하면 됩니다.

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create

<!-- LINKS - internal -->
[cli-quickstart]: kubernetes-walkthrough.md
[portal-quickstart]: kubernetes-walkthrough-portal.md
[log-analytics-overview]: ../azure-monitor/logs/log-query-overview.md
[analyze-log-analytics]: ../azure-monitor/logs/log-analytics-tutorial.md
[kubelet-logs]: kubelet-logs.md
[aks-ssh]: ssh.md
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[log-schema-azureactivity]: /azure/azure-monitor/reference/tables/azureactivity
[log-schema-azurediagnostics]: /azure/azure-monitor/reference/tables/azurediagnostics
[log-schema-azuremetrics]: /azure/azure-monitor/reference/tables/azuremetrics
[log-schema-containerimageinventory]: /azure/azure-monitor/reference/tables/containerimageinventory
[log-schema-containerinventory]: /azure/azure-monitor/reference/tables/containerinventory
[log-schema-containerlog]: /azure/azure-monitor/reference/tables/containerlog
[log-schema-containernodeinventory]: /azure/azure-monitor/reference/tables/containernodeinventory
[log-schema-containerservicelog]: /azure/azure-monitor/reference/tables/containerservicelog
[log-schema-heartbeat]: /azure/azure-monitor/reference/tables/heartbeat
[log-schema-insightsmetrics]: /azure/azure-monitor/reference/tables/insightsmetrics
[log-schema-kubeevents]: /azure/azure-monitor/reference/tables/kubeevents
[log-schema-kubehealth]: /azure/azure-monitor/reference/tables/kubehealth
[log-schema-kubemonagentevents]: /azure/azure-monitor/reference/tables/kubemonagentevents
[log-schema-kubenodeinventory]: /azure/azure-monitor/reference/tables/kubenodeinventory
[log-schema-kubepodinventory]: /azure/azure-monitor/reference/tables/kubepodinventory
[log-schema-kubeservices]: /azure/azure-monitor/reference/tables/kubeservices
[log-schema-perf]: /azure/azure-monitor/reference/tables/perf