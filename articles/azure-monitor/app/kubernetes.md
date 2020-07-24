---
title: Application Insights를 사용하여 AKS(Azure Kubernetes Service) 또는 Kubernetes에서 호스트된 다른 애플리케이션 모니터링 - Azure Monitor | Microsoft Docs
description: Azure Monitor는 Kubernetes 클러스터에서 서비스 메시 기술인 Istio를 사용하여 Kubernetes에서 호스트된 애플리케이션에 대한 애플리케이션 모니터링을 제공합니다. 이를 통해 클러스터에서 실행 중인 Pod를 기준으로 들어오고 나가는 요청과 관련된 Application Insights 원격 분석을 수집할 수 있습니다.
ms.topic: conceptual
author: tokaplan
ms.author: alkaplan
ms.date: 04/25/2019
ms.openlocfilehash: 4bb1af6ca2126b7ae58a6c836624ec78a071a5a5
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87075283"
---
# <a name="zero-instrumentation-application-monitoring-for-kubernetes-hosted-applications-with-istio---deprecated"></a>Istio를 사용하여 Kubernetes에서 호스트된 애플리케이션에 대한 제로 계측 애플리케이션 모니터링 - 사용되지 않음

> [!IMPORTANT]
> 이 기능은 현재 사용되지 않으며 2020년 8월 1일부터 더 이상 지원되지 않습니다.
> 현재 코드리스 모니터링은 [독립 실행형 에이전트를 통해 Java](./java-in-process-agent.md)에 대해서만 사용하도록 설정할 수 있습니다. 다른 언어의 경우 SDK를 사용하여 AKS에서 [ASP.Net Core](./asp-net-core.md), [ASP.Net](./asp-net.md), [Node.js](./nodejs.md), [JavaScript](./javascript.md) 및 [Python](./opencensus-python.md) 등의 앱을 모니터링합니다.

Azure Monitor는 Kubernetes 클러스터에서 서비스 메시 기술을 사용하여 Kubernetes에서 호스트된 모든 앱에 대한 애플리케이션 모니터링을 즉시 제공합니다. [애플리케이션 맵](../../azure-monitor/app/app-map.md)과 같은 기본 Application Insight 기능을 사용하여 종속성, 실시간 모니터링을 위한 [라이브 메트릭 스트림](../../azure-monitor/app/live-stream.md), [기본 대시보드](../../azure-monitor/app/overview-dashboard.md)를 이용한 강력한 시각화, [메트릭 탐색기](../../azure-monitor/platform/metrics-getting-started.md), [통합 문서](../../azure-monitor/platform/workbooks-overview.md) 등을 모델링할 수 있습니다. 사용자는 이 기능을 사용하여 선택한 Kubernetes 네임스페이스 내의 모든 Kubernetes 워크로드에서 성능 병목 상태 및 오류 다발 지점을 찾을 수 있습니다. Azure Monitor는 Istio와 같은 기술을 통해 기존의 서비스 메시 투자를 활용하여 애플리케이션 코드를 수정하지 않고도 자동 계측된 앱 모니터링을 사용하도록 설정할 수 있습니다.

> [!NOTE]
> 위 방법은 Kubernetes에서 애플리케이션 모니터링을 수행하는 여러 가지 방법 중 하나입니다. [Application Insights SDK](../../azure-monitor/azure-monitor-app-hub.yml)를 사용하여 서비스 메시 없이도 Kubernetes에서 호스트되는 모든 앱을 계측할 수도 있습니다. SDK를 사용하여 애플리케이션을 계측하지 않고 Kubernetes를 모니터링하려면 아래 방법을 사용하면 됩니다.

## <a name="prerequisites"></a>사전 요구 사항

- [Kubernetes 클러스터](../../aks/concepts-clusters-workloads.md)
- *kubectl*을 실행하는 클러스터에 대한 콘솔 액세스
- [Application Insight 리소스](create-new-resource.md)
- 서비스 메시. 배포된 Istio가 클러스터에 없는 경우 [Azure Kubernetes Service에서 Istio를 설치하고 사용](../../aks/servicemesh-istio-install.md)하는 방법을 배울 수 있습니다.

## <a name="capabilities"></a>기능

Kubernetes에서 호스트된 앱에 대해 제로 계측 애플리케이션 모니터링을 사용하면 다음을 사용할 수 있습니다.

- [애플리케이션 맵](../../azure-monitor/app/app-map.md)
- [라이브 스트림 메트릭](../../azure-monitor/app/live-stream.md)
- [대시보드](../../azure-monitor/app/overview-dashboard.md)
- [메트릭 탐색기](../../azure-monitor/platform/metrics-getting-started.md)
- [분산 추적](../../azure-monitor/app/distributed-tracing.md)
- [엔드투엔드 트랜잭션 모니터링](../../azure-monitor/learn/tutorial-performance.md#identify-slow-server-operations)

## <a name="installation-steps"></a>설치 단계

이 솔루션을 사용하도록 설정하려면 다음 단계를 수행하세요.
- 애플리케이션을 배포합니다(아직 배포하지 않은 경우).
- 애플리케이션이 서비스 메시에 포함되어 있는지 확인합니다.
- 수집된 원격 분석을 관찰합니다.

### <a name="configure-your-app-to-work-with-a-service-mesh"></a>서비스 메시를 사용하도록 앱 구성

Istio는 [Pod를 계측](https://istio.io/docs/setup/kubernetes/additional-setup/sidecar-injection/)하는 두 가지 방법을 지원합니다.
대부분의 경우 *istio-injection* 레이블을 사용하여 애플리케이션이 포함된 Kubernetes 네임스페이스를 표시하는 것이 가장 쉽습니다.

```console
kubectl label namespace <my-app-namespace> istio-injection=enabled
```

> [!NOTE]
> 서비스 메시는 네트워크에서 데이터를 리프트하므로 암호화된 트래픽을 가로챌 수 없습니다. 클러스터를 벗어나지 않는 트래픽의 경우 암호화되지 않은 프로토콜(예: HTTP)을 사용합니다. 암호화해야 하는 외부 트래픽의 경우 수신 컨트롤러에서 [TLS 종료를 설정](https://kubernetes.io/docs/concepts/services-networking/ingress/#tls)하는 것이 좋습니다.

서비스 메시 외부에서 실행되는 애플리케이션은 영향을 받지 않습니다.

### <a name="deploy-your-application"></a>애플리케이션 배포

- 애플리케이션을 *my-app-namespace* 네임스페이스에 배포합니다. 애플리케이션이 이미 배포되어 있고 위에서 설명한 자동 사이드카 삽입 방법을 따른 경우 Istio가 사이드카를 삽입하도록 Pod를 다시 만들어야 합니다. 롤링 업데이트를 시작하거나 개별 Pod를 삭제하고 이와 같은 Pod가 다시 생성될 때까지 기다립니다.
- 애플리케이션이 [Istio 요구 사항](https://istio.io/docs/setup/kubernetes/prepare/requirements/)을 준수하는지 확인합니다.

### <a name="deploy-zero-instrumentation-application-monitoring-for-kubernetes-hosted-apps"></a>Kubernetes에서 호스트된 앱에 대한 제로 계측 애플리케이션 모니터링 배포

1. [‘Application Insights 어댑터’ 릴리스](https://github.com/Microsoft/Application-Insights-Istio-Adapter/releases/)를 다운로드하고 추출합니다.
2. 릴리스 폴더 내의 */src/kubernetes/* 로 이동합니다.
3. *application-insights-istio-mixer-adapter-deployment.yaml* 편집
    - Azure Portal의 Application Insights 리소스의 계측 키를 포함하도록 *ISTIO_MIXER_PLUGIN_AI_INSTRUMENTATIONKEY* 환경 변수의 값을 편집하여 원격 분석을 포함합니다.
    - 필요한 경우 모니터링을 사용하도록 설정할 쉼표로 구분된 네임스페이스 목록을 포함하도록 *ISTIO_MIXER_PLUGIN_WATCHLIST_NAMESPACES* 환경 변수의 값을 편집합니다. 모든 네임스페이스를 모니터링하려면 값을 비워 둡니다.
4. 다음을 실행하여 *src/kubernetes/* 에서 발견된 ‘모든’ YAML 파일을 적용합니다(계속 */src/kubernetes/* 안에 있어야 함).

   ```console
   kubectl apply -f .
   ```

### <a name="verify-deployment"></a>배포 확인

- Application Insights 어댑터가 배포되었는지 확인합니다.

  ```console
  kubectl get pods -n istio-system -l "app=application-insights-istio-mixer-adapter"
  ```
> [!NOTE]
> 경우에 따라 미세 조정이 필요합니다. 개별 Pod에 대한 원격 분석을 수집에 포함하거나 수집에서 제외하려면 해당 Pod에 *appinsights/monitoring.enabled* 레이블을 사용합니다. 이렇게 하면 모든 네임스페이스 기반 구성보다 높은 우선 순위를 가지게 됩니다. Pod를 포함하려면 *appinsights/monitoring.enabled*를 *true*로 설정하고, 제외하려면 *false*로 설정합니다.

### <a name="view-application-insights-telemetry"></a>Application Insights 원격 분석 보기

- 애플리케이션에 대한 샘플 요청을 생성하여 모니터링이 제대로 작동하는지 확인합니다.
- 3~5분 이내에 Azure Portal에 원격 분석이 표시되기 시작합니다. Portal에서 Application Insights 리소스의 ‘애플리케이션 맵’ 섹션을 확인하세요.

## <a name="troubleshooting"></a>문제 해결

아래는 원격 분석이 Azure Portal에 예상대로 표시되지 않는 경우 사용할 문제 해결 흐름입니다.

1. 애플리케이션이 부하가 높은 상태이고 일반 HTTP에서 요청을 보내고 받는 중인지 확인합니다. 원격 분석이 네트워크에서 리프트되므로 암호화된 트래픽은 지원되지 않습니다. 들어오거나 나가는 요청이 없는 경우에도 원격 분석이 없습니다.
2. *application-insights-istio-mixer-adapter-deployment.yaml*의 *ISTIO_MIXER_PLUGIN_AI_INSTRUMENTATIONKEY* 환경 변수에서 올바른 계측 키가 제공되는지 확인합니다. 계측 키는 Azure Portal에서 Application Insights 리소스의 ‘개요’ 탭에 있습니다.
3. *application-insights-istio-mixer-adapter-deployment.yaml*의 *ISTIO_MIXER_PLUGIN_WATCHLIST_NAMESPACES* 환경 변수에서 올바른 Kubernetes 네임스페이스가 제공되는지 확인합니다. 모든 네임스페이스를 모니터링하려면 값을 비워 둡니다.
4. 애플리케이션의 Pod가 Istio에서 사이드카로 삽입되었는지 확인합니다. 각 Pod에 Istio의 사이드카가 있는지 확인합니다.

   ```console
   kubectl describe pod -n <my-app-namespace> <my-app-pod-name>
   ```
   Pod에서 실행 중인 *istio-proxy*라는 컨테이너가 있는지 확인합니다.

5. Application Insights 어댑터의 추적을 봅니다.

   ```console
   kubectl get pods -n istio-system -l "app=application-insights-istio-mixer-adapter"
   kubectl logs -n istio-system application-insights-istio-mixer-adapter-<fill in from previous command output>
   ```

   받은 원격 분석 항목의 수는 1분마다 업데이트됩니다. 분 단위로 수가 증가하지 않는다면 원격 분석이 Istio를 통해 어댑터로 전송되지 않는 것입니다.
   로그에서 오류를 찾습니다.
6. *Application Insight for Kubernetes* 어댑터에 원격 분석이 제공되지 않도록 설정된 경우 Istio의 Mixer 로그를 확인하여 어댑터에 데이터를 전송하지 않는 이유를 확인합니다.

   ```console
   kubectl get pods -n istio-system -l "istio=mixer,app=telemetry"
   kubectl logs -n istio-system istio-telemetry-<fill in from previous command output> -c mixer
   ```
   특히 *applicationinsightsadapter* 어댑터와의 통신과 관련된 오류를 살펴봅니다.

## <a name="faq"></a>FAQ

이 프로젝트의 진행 상황에 대한 최신 정보는 [Istio Mixer 프로젝트의 GitHub에 대한 Application Insights 어댑터](https://github.com/Microsoft/Application-Insights-Istio-Adapter/blob/master/SETUP.md#faq)를 참조하세요.

## <a name="uninstall"></a>제거

제품을 제거하려면 *src/kubernetes/* 아래에 있는 ‘모든’ YAML 파일에 대해 다음을 실행합니다.

```console
kubectl delete -f <filename.yaml>
```


## <a name="next-steps"></a>다음 단계

Azure Monitor 및 컨테이너가 함께 작동하는 방법에 대한 자세한 내용은 [컨테이너용 Azure Monitor 개요](../../azure-monitor/insights/container-insights-overview.md)를 참조하세요.
