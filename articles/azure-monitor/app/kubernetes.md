---
title: 응용 프로그램 인사이트를 사용하여 Azure Kubernetes 서비스(AKS) 또는 기타 Kubernetes 호스팅 응용 프로그램 모니터링 - Azure 모니터 | 마이크로 소프트 문서
description: Azure Monitor는 Kubernetes 클러스터에서 서비스 메시 기술인 Istio를 사용하여 Kubernetes 호스팅 응용 프로그램에 대한 응용 프로그램 모니터링을 제공합니다. 이를 통해 클러스터에서 실행되는 포드와 들어오고 나가는 요청과 관련된 Application Insights 원격 분석을 수집할 수 있습니다.
ms.topic: conceptual
author: tokaplan
ms.author: alkaplan
ms.date: 04/25/2019
ms.openlocfilehash: 56a0cb66f5b54c817067970ab369d7ca471a1696
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80132354"
---
# <a name="zero-instrumentation-application-monitoring-for-kubernetes-hosted-applications"></a>Kubernetes 호스팅 애플리케이션을 위한 계측 애플리케이션 모니터링 제로

> [!IMPORTANT]
> 이 기능은 현재 공개 미리 보기 상태입니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

이제 Azure Monitor는 Kubernetes 클러스터의 서비스 메시 기술을 활용하여 Kubernetes 호스팅 앱에 대한 즉시 응용 프로그램 모니터링을 제공합니다. 종속성을 모델링하는 [응용 프로그램 맵,](../../azure-monitor/app/app-map.md) 실시간 모니터링을 위한 [실시간 메트릭 스트림,](../../azure-monitor/app/live-stream.md) 기본 대시보드, [메트릭 탐색기](../../azure-monitor/platform/metrics-getting-started.md)및 [통합 문서와](../../azure-monitor/app/usage-workbooks.md)같은 기본 응용 프로그램 인사이트 기능을 사용하면 됩니다. [default dashboard](../../azure-monitor/app/overview-dashboard.md) 이 기능을 사용하면 선택한 Kubernetes 네임스페이스 내의 모든 Kubernetes 워크로드에서 성능 병목 현상 및 실패 핫스팟을 파악할 수 있습니다. Azure Monitor는 Istio와 같은 기술로 기존 서비스 메시 투자를 활용하여 응용 프로그램 코드를 수정하지 않고도 자동 계측 앱 모니터링을 지원합니다.

> [!NOTE]
> 이는 Kubernetes에서 응용 프로그램 모니터링을 수행하는 여러 가지 방법 중 하나입니다.또한 서비스 메시없이 [응용 프로그램 인사이트 SDK를](../../azure-monitor/azure-monitor-app-hub.yml) 사용하여 Kubernetes에서 호스팅되는 모든 앱을 계측할 수 있습니다. SDK로 응용 프로그램을 계측하지 않고 Kubernetes를 모니터링하려면 아래 방법을 사용할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

- [쿠베르네테스 클러스터](https://docs.microsoft.com/azure/aks/concepts-clusters-workloads).
- 콘솔 액세스는 *kubectl을*실행하기 위해 클러스터에 대한 액세스.
- [애플리케이션 인사이트 리소스](create-new-resource.md)
- 서비스 메시가 있습니다. 클러스터에 Istio가 배포되어 있지 않은 경우 [Azure Kubernetes 서비스에서 Istio를 설치하고 사용하는](https://docs.microsoft.com/azure/aks/istio-install)방법을 알아볼 수 있습니다.

## <a name="capabilities"></a>기능

Kubernetes 호스팅 앱에 대해 계측 응용 프로그램 모니터링을 0개 사용하면 다음을 사용할 수 있습니다.

- [애플리케이션 맵](../../azure-monitor/app/app-map.md)
- [실시간 스트림 지표](../../azure-monitor/app/live-stream.md)
- [대시보드](../../azure-monitor/app/overview-dashboard.md)
- [메트릭 탐색기](../../azure-monitor/platform/metrics-getting-started.md)
- [분산 추적](../../azure-monitor/app/distributed-tracing.md)
- [엔드 투 엔드 트랜잭션 모니터링](../../azure-monitor/learn/tutorial-performance.md#identify-slow-server-operations)

## <a name="installation-steps"></a>설치 단계

솔루션을 활성화하려면 다음 단계를 수행합니다.
- 응용 프로그램을 배포합니다(아직 배포되지 않은 경우).
- 응용 프로그램이 서비스 메시의 일부인지 확인합니다.
- 수집된 원격 분석을 관찰합니다.

### <a name="configure-your-app-to-work-with-a-service-mesh"></a>서비스 메시로 작동하도록 앱 구성

Istio는 [포드를 계측하는](https://istio.io/docs/setup/kubernetes/additional-setup/sidecar-injection/)두 가지 방법을 지원합니다.
대부분의 경우 응용 프로그램이 포함된 Kubernetes 네임스페이스를 *istio 주입* 레이블로 표시하는 것이 가장 쉽습니다.

```console
kubectl label namespace <my-app-namespace> istio-injection=enabled
```

> [!NOTE]
> 서비스 메시가 와이어에서 데이터를 들어 올리기 때문에 암호화된 트래픽을 가로챌 수 없습니다. 클러스터를 벗어나지 않는 트래픽의 경우 암호화되지 않은 프로토콜(예: HTTP)을 사용합니다. 암호화해야 하는 외부 트래픽의 경우 수신 컨트롤러에서 [TLS 종료를 설정하는](https://kubernetes.io/docs/concepts/services-networking/ingress/#tls) 것이 좋습니다.

서비스 메시 외부에서 실행되는 응용 프로그램은 영향을 받지 않습니다.

### <a name="deploy-your-application"></a>애플리케이션 배포

- *내 앱-네임스페이스* 네임스페이스에 응용 프로그램을 배포합니다. 응용 프로그램이 이미 배포되어 있고 위에서 설명한 자동 사이드카 주입 방법을 따랐으면 Istio가 사이드카를 주입할 수 있도록 포드를 다시 만들어야 합니다. 롤링 업데이트를 시작하거나 개별 포드를 삭제하고 다시 만들 때까지 기다립니다.
- 응용 프로그램이 [Istio 요구 사항을](https://istio.io/docs/setup/kubernetes/prepare/requirements/)준수하는지 확인합니다.

### <a name="deploy-zero-instrumentation-application-monitoring-for-kubernetes-hosted-apps"></a>Kubernetes 호스팅 앱에 대해 계측 응용 프로그램 모니터링 제로 배포

1. 응용 프로그램 [ *인사이트 어댑터* 릴리스를](https://github.com/Microsoft/Application-Insights-Istio-Adapter/releases/)다운로드하고 추출합니다.
2. 릴리스 폴더 내부 */src/kubernetes/로* 이동합니다.
3. 응용 *프로그램 인사이트 편집-istio-믹서-어댑터-배포.yaml*
    - *ISTIO_MIXER_PLUGIN_AI_INSTRUMENTATIONKEY* 환경 변수의 값을 편집하여 원격 분석을 포함하도록 Azure Portal의 Application Insights 리소스의 계측 키를 포함합니다.
    - 필요한 경우 *ISTIO_MIXER_PLUGIN_WATCHLIST_NAMESPACES* 환경 변수 값을 편집하여 모니터링을 사용하도록 설정하려는 쉼표로 구분된 네임스페이스 목록을 포함합니다. 모든 네임스페이스를 모니터링하려면 비워 둡니다.
4. *SRC/kubernetes에서* 발견된 *모든* YAML 파일을 다음을 실행하여 적용합니다(여전히 */src/kubernetes*내부에 있어야 합니다.

   ```console
   kubectl apply -f .
   ```

### <a name="verify-deployment"></a>배포 확인

- 애플리케이션 인사이트 어댑터가 배포되었는지 확인합니다.

  ```console
  kubectl get pods -n istio-system -l "app=application-insights-istio-mixer-adapter"
  ```
> [!NOTE]
> 경우에 따라 미세 조정이 필요합니다. 개별 포드에 대한 원격 분석을 수집하지 못하도록 포함하거나 제외하려면 해당 포드에서 *appinsights/monitoring.enabled* 레이블을 사용합니다. 모든 네임스페이스 기반 구성보다 우선 순위가 높습니다. *appinsights/monitoring.enabled를* *true로* 설정하여 포드를 포함하고 *false를* 제외합니다.

### <a name="view-application-insights-telemetry"></a>애플리케이션 인사이트 원격 분석 보기

- 응용 프로그램에 대한 샘플 요청을 생성하여 모니터링이 제대로 작동하는지 확인합니다.
- 3~5분 이내에 Azure 포털에 원격 분석이 표시되는 것을 시작해야 합니다. 포털에서 응용 프로그램 인사이트 리소스의 *응용 프로그램 맵* 섹션을 확인하십시오.

## <a name="troubleshooting"></a>문제 해결

다음은 원격 분석이 Azure 포털에 예상대로 나타나지 않을 때 사용할 문제 해결 흐름입니다.

1. 응용 프로그램이 로드 중이고 일반 HTTP로 요청을 보내고 받는지 확인합니다. 원격 분석이 와이어에서 해제되므로 암호화된 트래픽은 지원되지 않습니다. 수신 또는 나가는 요청이 없는 경우 원격 분석도 없습니다.
2. *응용 프로그램 인사이트-istio-mixer-adapter-deployment.yaml에서* *ISTIO_MIXER_PLUGIN_AI_INSTRUMENTATIONKEY* 환경 변수에 올바른 계측 키가 제공되었는지 확인합니다. 계측 키는 Azure 포털의 응용 프로그램 인사이트 리소스의 *개요* 탭에서 찾을 수 있습니다.
3. *응용 프로그램 인사이트-istio-mixer-adapter-deployment.yaml에서* *ISTIO_MIXER_PLUGIN_WATCHLIST_NAMESPACES* 환경 변수에 올바른 Kubernetes 네임스페이스가 제공되도록 합니다. 모든 네임스페이스를 모니터링하려면 비워 둡니다.
4. 응용 프로그램의 포드가 Istio에 의해 사이드카 주입되었는지 확인합니다. 각 포드에 Istio의 사이드카가 있는지 확인합니다.

   ```console
   kubectl describe pod -n <my-app-namespace> <my-app-pod-name>
   ```
   포드에서 *istio-proxy라는* 컨테이너가 실행되고 있는지 확인합니다.

5. 애플리케이션 인사이트 어댑터의 추적을 봅니다.

   ```console
   kubectl get pods -n istio-system -l "app=application-insights-istio-mixer-adapter"
   kubectl logs -n istio-system application-insights-istio-mixer-adapter-<fill in from previous command output>
   ```

   수신된 원격 분석 항목의 수는 1분에 한 번 업데이트됩니다. 분 단위가 증가하지 않으면 Istio에서 어댑터로 원격 분석이 전송되지 않습니다.
   로그의 오류를 찾습니다.
6. *Kubernetes 어댑터에 대한 응용 프로그램 인사이트가* 원격 분석을 제공하지 않는 경우 Istio의 믹서 로그를 확인하여 어댑터로 데이터를 보내지 않는 이유를 알아냅니다.

   ```console
   kubectl get pods -n istio-system -l "istio=mixer,app=telemetry"
   kubectl logs -n istio-system istio-telemetry-<fill in from previous command output> -c mixer
   ```
   특히 *애플리케이션인사이트어댑터* 어댑터와의 통신에 관련된 오류를 찾아보십시오.

## <a name="faq"></a>FAQ

이 프로젝트의 진행 상황에 대한 최신 정보는 [Istio Mixer 프로젝트의 GitHub에 대한 응용 프로그램 인사이트 어댑터를](https://github.com/Microsoft/Application-Insights-Istio-Adapter/blob/master/SETUP.md#faq)참조하십시오.

## <a name="uninstall"></a>제거

제품을 제거하려면 *src/kubernetes/실행에서* 발견된 *모든* YAML 파일에 대해:

```console
kubectl delete -f <filename.yaml>
```


## <a name="next-steps"></a>다음 단계

Azure Monitor 및 컨테이너가 함께 작동하는 방법에 대해 자세히 알아보려면 [Azure Monitor를 방문하여 컨테이너 개요를](../../azure-monitor/insights/container-insights-overview.md) 확인하십시오.
