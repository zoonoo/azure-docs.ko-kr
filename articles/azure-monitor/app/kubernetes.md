---
title: Application Insights를 사용 하 여 AKS (Azure Kubernetes Service) 또는 기타 Kubernetes 호스팅된 응용 프로그램 모니터링-Azure Monitor | Microsoft Docs
description: Azure Monitor는 Kubernetes 클러스터에서 service 메시 기술 인 Istio를 사용 하 여 Kubernetes 호스팅된 응용 프로그램에 대 한 응용 프로그램 모니터링을 제공 합니다. 이렇게 하면 클러스터에서 실행 중인 pod에서 들어오고 나가는 요청과 관련 된 원격 분석을 Application Insights 수집할 수 있습니다.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: tokaplan
ms.author: alkaplan
ms.date: 04/25/2019
ms.openlocfilehash: 3056b6c56be32cf5c054c4526a88157650a3e30b
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72820770"
---
# <a name="zero-instrumentation-application-monitoring-for-kubernetes-hosted-applications"></a>Kubernetes 호스팅된 응용 프로그램에 대 한 제로 계측 응용 프로그램 모니터링

> [!IMPORTANT]
> 이 기능은 현재 공개 미리 보기 상태입니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

Azure Monitor은 이제 Kubernetes 호스팅된 앱에 대 한 기본 응용 프로그램 모니터링을 제공 하기 위해 Kubernetes 클러스터에서 service 메시 기술을 활용 합니다. [응용 프로그램 맵과](../../azure-monitor/app/app-map.md) 같은 기본 응용 프로그램 정보 기능으로 종속성 [라이브 메트릭 스트림](../../azure-monitor/app/live-stream.md) 을 모델링 하 고, 실시간 모니터링, [기본 대시보드](../../azure-monitor/app/overview-dashboard.md)를 사용 하 여 강력한 시각화, [메트릭 탐색기](../../azure-monitor/platform/metrics-getting-started.md)및 [ 통합 문서](../../azure-monitor/app/usage-workbooks.md). 이 기능을 통해 사용자는 선택한 Kubernetes 네임 스페이스 내의 모든 Kubernetes 워크 로드에서 성능 병목 및 오류 핫스팟을 발견할 수 있습니다. Istio와 같은 기술을 사용 하 여 기존 서비스 메시 투자에 대 한 대문자화를 통해 응용 프로그램 코드를 수정 하지 않고도 자동으로 계측 된 앱 모니터링을 사용할 수 Azure Monitor.

> [!NOTE]
> 이는 Kubernetes에서 응용 프로그램 모니터링을 수행 하는 다양 한 방법 중 하나입니다. 또한 서비스 메시 없이 [APPLICATION INSIGHTS SDK](../../azure-monitor/azure-monitor-app-hub.md) 를 사용 하 여 Kubernetes에서 호스트 되는 모든 앱을 계측할 수 있습니다. SDK를 사용 하 여 응용 프로그램을 계측 하지 않고 Kubernetes를 모니터링 하려면 아래 방법을 사용할 수 있습니다.

## <a name="prerequisites"></a>전제 조건

- [Kubernetes 클러스터](https://docs.microsoft.com/azure/aks/concepts-clusters-workloads)입니다.
- *Kubectl*를 실행 하는 클러스터에 대 한 콘솔 액세스
- [응용 프로그램 정보 리소스](create-new-resource.md)
- 서비스 메시가 있어야 합니다. 클러스터에 Istio가 배포 되지 않은 경우 [Azure Kubernetes Service에서 Istio를 설치 하 고 사용](https://docs.microsoft.com/azure/aks/istio-install)하는 방법을 배울 수 있습니다.

## <a name="capabilities"></a>기능

Kubernetes 호스트 된 앱에 대해 제로 계측 응용 프로그램 모니터링을 사용 하면 다음을 사용할 수 있습니다.

- [애플리케이션 맵](../../azure-monitor/app/app-map.md)
- [라이브 스트림 메트릭](../../azure-monitor/app/live-stream.md)
- [대시보드](../../azure-monitor/app/overview-dashboard.md)
- [메트릭 탐색기](../../azure-monitor/platform/metrics-getting-started.md)
- [분산-추적](../../azure-monitor/app/distributed-tracing.md)
- [종단 간 트랜잭션 모니터링](../../azure-monitor/learn/tutorial-performance.md#identify-slow-server-operations)

## <a name="installation-steps"></a>설치 단계

이 솔루션을 사용 하도록 설정 하려면 다음 단계를 수행 합니다.
- 응용 프로그램을 배포 합니다 (아직 배포 하지 않은 경우).
- 응용 프로그램이 서비스 메시의 일부 인지 확인 합니다.
- 수집 된 원격 분석을 관찰 합니다.

### <a name="configure-your-app-to-work-with-a-service-mesh"></a>서비스 메시를 사용 하도록 앱 구성

Istio [는 pod를 계측 하](https://istio.io/docs/setup/kubernetes/additional-setup/sidecar-injection/)는 두 가지 방법을 지원 합니다.
대부분의 경우 응용 프로그램을 포함 하는 Kubernetes 네임 스페이스를 *istio 삽입* 레이블로 표시 하는 것이 가장 쉽습니다.

```console
kubectl label namespace <my-app-namespace> istio-injection=enabled
```

> [!NOTE]
> 서비스 메시는 네트워크에서 데이터를 리프트 하므로 암호화 된 트래픽을 가로챌 수 없습니다. 클러스터를 떠나지 않는 트래픽의 경우 암호화 되지 않은 프로토콜 (예: HTTP)을 사용 합니다. 암호화 해야 하는 외부 트래픽의 경우 수신 컨트롤러에서 [SSL 종료를 설정](https://kubernetes.io/docs/concepts/services-networking/ingress/#tls) 하는 것이 좋습니다.

서비스 메시 외부에서 실행 되는 응용 프로그램은 영향을 받지 않습니다.

### <a name="deploy-your-application"></a>애플리케이션 배포

- 응용 프로그램을 *내 앱 네임 스페이스* 네임 스페이스에 배포 합니다. 응용 프로그램이 이미 배포 되어 있고 위에서 설명한 자동 사이드카 주입 메서드를 수행한 경우 Istio가 사이드카를 삽입 하도록 pod를 다시 만들어야 합니다. 롤링 업데이트를 시작 하거나 개별 pod을 삭제 하 고 다시 생성 될 때까지 기다립니다.
- 응용 프로그램이 [Istio 요구 사항을](https://istio.io/docs/setup/kubernetes/prepare/requirements/)준수 하는지 확인 합니다.

### <a name="deploy-zero-instrumentation-application-monitoring-for-kubernetes-hosted-apps"></a>Kubernetes 호스트 된 앱에 대해 제로 계측 응용 프로그램 모니터링 배포

1. [ *Application Insights 어댑터* 릴리스](https://github.com/Microsoft/Application-Insights-Istio-Adapter/releases/)를 다운로드 하 고 압축을 풉니다.
2. 릴리스 폴더 안에 있는 */src/kubernetes/* 로 이동 합니다.
3. *응용 프로그램 편집-정보-istio-믹서-배포. yaml*
    - *ISTIO_MIXER_PLUGIN_AI_INSTRUMENTATIONKEY* 환경 변수의 값을 편집 하 여 원격 분석을 포함 하는 Azure Portal Application Insights 리소스의 계측 키를 포함 합니다.
    - 필요한 경우 *ISTIO_MIXER_PLUGIN_WATCHLIST_NAMESPACES* 환경 변수 값을 편집 하 여 모니터링을 사용 하도록 설정할 쉼표로 구분 된 네임 스페이스 목록을 포함 합니다. 모든 네임 스페이스를 모니터링 하려면 비워 둡니다.
4. 다음을 실행 하 여 *src/kubernetes/* 아래에 있는 *모든* yaml 파일을 적용 합니다 (여전히 */src/kubernetes/* 안에 있어야 함).

   ```console
   kubectl apply -f .
   ```

### <a name="verify-deployment"></a>배포 확인

- Application Insights 어댑터가 배포 되었는지 확인 합니다.

  ```console
  kubectl get pods -n istio-system -l "app=application-insights-istio-mixer-adapter"
  ```
> [!NOTE]
> 경우에 따라 미세 조정 조정이 필요 합니다. 수집 되는 개별 pod에 대 한 원격 분석을 포함 하거나 제외 하려면 해당 pod에서 *appinsights/모니터링* 사용 레이블을 사용 합니다. 이는 모든 네임 스페이스 기반 구성 보다 우선 합니다. *Appinsights/모니터링을 설정 합니다.* pod를 포함 하려면 *true* 로 설정 하 고, 제외 하려면 *false* 로 설정 합니다.

### <a name="view-application-insights-telemetry"></a>Application Insights 원격 분석 보기

- 응용 프로그램에 대 한 샘플 요청을 생성 하 여 모니터링이 제대로 작동 하는지 확인 합니다.
- 3-5 분 이내에 Azure Portal 원격 분석이 표시 되기 시작 해야 합니다. 포털에서 Application Insights 리소스의 *응용 프로그램 맵* 섹션을 확인 하세요.

## <a name="troubleshooting"></a>문제 해결

다음은 원격 분석이 예상 대로 Azure Portal에 표시 되지 않는 경우 사용할 문제 해결 흐름입니다.

1. 응용 프로그램이 부하 상태이 고 일반 HTTP에서 요청을 보내고 받고 있는지 확인 합니다. 원격 분석이 네트워크에서 리프트 되므로 암호화 된 트래픽은 지원 되지 않습니다. 들어오는 요청이 나 나가는 요청이 없는 경우에는 원격 분석을 사용할 수 없습니다.
2. 올바른 계측 키가 *ISTIO_MIXER_PLUGIN_AI_INSTRUMENTATIONKEY* 환경 변수에서 제공 되는지 확인 합니다 ( *응용 프로그램-정보-istio-* ). 계측 키는 Azure Portal Application Insights 리소스의 *개요* 탭에 있습니다.
3. 올바른 Kubernetes 네임 스페이스가 *ISTIO_MIXER_PLUGIN_WATCHLIST_NAMESPACES* 환경 변수 ( *application insights-istio-* )에 제공 되는지 확인 합니다. 모든 네임 스페이스를 모니터링 하려면 비워 둡니다.
4. 응용 프로그램의 pod를 Istio에서 사이드카으로 삽입 했는지 확인 합니다. 각 pod에 Istio의 사이드카 있는지 확인 합니다.

   ```console
   kubectl describe pod -n <my-app-namespace> <my-app-pod-name>
   ```
   Pod에서 실행 중인 *istio-proxy* 라는 컨테이너가 있는지 확인 합니다.

5. Application Insights 어댑터의 추적을 봅니다.

   ```console
   kubectl get pods -n istio-system -l "app=application-insights-istio-mixer-adapter"
   kubectl logs -n istio-system application-insights-istio-mixer-adapter-<fill in from previous command output>
   ```

   받은 원격 분석 항목의 수는 1 분에 한 번 업데이트 됩니다. 분 단위로 증가 하지 않는 경우-원격 분석을 Istio를 통해 어댑터로 보내지 않습니다.
   로그에서 오류를 찾습니다.
6. *Kubernetes 어댑터에 대 한 Application* insights가 원격 분석을 수행 하 고 있지 않은 것으로 설정 된 경우 istio의 믹서 로그를 확인 하 여 어댑터에 데이터를 전송 하지 않는 이유를 확인 합니다.

   ```console
   kubectl get pods -n istio-system -l "istio=mixer,app=telemetry"
   kubectl logs -n istio-system istio-telemetry-<fill in from previous command output> -c mixer
   ```
   특히 *applicationinsightsadapter* 어댑터와의 통신과 관련 된 오류를 찾습니다.

## <a name="faq"></a>FAQ

이 프로젝트의 진행 상황에 대 한 최신 정보는 [Istio 믹서 프로젝트의 GitHub에 대 한 Application Insights 어댑터](https://github.com/Microsoft/Application-Insights-Istio-Adapter/blob/master/SETUP.md#faq)를 참조 하세요.

## <a name="uninstall"></a>제거

제품을 제거 하려면 *src/kubernetes/* run 아래에 있는 *모든* yaml 파일에 대해 다음을 수행 합니다.

```console
kubectl delete -f <filename.yaml>
```


## <a name="next-steps"></a>다음 단계

Azure Monitor 및 컨테이너가 함께 작동 하는 방법에 대 한 자세한 내용은 [컨테이너 개요 Azure Monitor](../../azure-monitor/insights/container-insights-overview.md) 를 참조 하세요.