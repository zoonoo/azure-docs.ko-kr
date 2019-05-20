---
title: Azure Kubernetes Service (AKS) 또는 다른 Kubernetes 모니터링에 Application Insights를 사용 하 여 호스트 응용 프로그램-Azure Monitor | Microsoft Docs
description: Azure 모니터는 호스트 된 Kubernetes 응용 프로그램에 대 한 응용 프로그램 모니터링을 제공 하도록 Kubernetes 클러스터에 서비스 메시 기술을 Istio를 사용 합니다. 이 옵션을 사용 하면 클러스터에서 실행 되는 pod에서 들어오고 나가는 요청에 관련 된 Application Insights 원격 분석을 수집할 수 있습니다.
services: application-insights
author: tokaplan
manager: carmonm
ms.service: application-insights
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: alkaplan
ms.openlocfilehash: c94d589875195207ec6f71c35ad077cac281fda5
ms.sourcegitcommit: 9e8dfa1169a55c3c8af93a6c5f4e0dace4de48b2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/13/2019
ms.locfileid: "65555829"
---
# <a name="zero-instrumentation-application-monitoring-for-kubernetes-hosted-applications"></a>호스트 응용 프로그램을 Kubernetes 0 계측 응용 프로그램 모니터링

> [!IMPORTANT]
> 이 기능은 현재 공개 미리 보기로 제공 됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

Azure Monitor는 이제 호스트 된 Kubernetes 앱에 대해 상자 응용 프로그램 모니터링을 제공 하도록 Kubernetes 클러스터에 서비스 메시 기술을 활용 합니다. Application Insight 기능 들이 기본값 [응용 프로그램 맵](../../azure-monitor/app/app-map.md) 종속성을 모델링 하 [라이브 메트릭 Stream](../../azure-monitor/app/live-stream.md) 실시간 모니터링을 사용 하 여 강력한 시각화 요소를 [기본 대시보드](../../azure-monitor/app/overview-dashboard.md)하십시오 [메트릭 탐색기](../../azure-monitor/platform/metrics-getting-started.md), 및 [통합 문서](../../azure-monitor/app/usage-workbooks.md)합니다. 이 기능은 선택한 Kubernetes 네임 스페이스 내에서 Kubernetes 워크 로드의 모든 사용자가 성능 병목 상태 및 실패 핫스폿을 찾는 도와줍니다. Istio와 같은 기술 사용 하 여 기존 서비스 메시 투자를 활용 하 여 Azure Monitor 자동 계측 앱 모니터링 응용 프로그램의 코드를 수정 하지 않아도 사용 하도록 설정 합니다.

> [!NOTE]
> Kubernetes에서 응용 프로그램 모니터링을 수행 하는 여러 가지 방법 중 하나입니다. 사용 하 여 Kubernetes에서 호스트 되는 모든 앱을 계측할 수 있습니다 합니다 [Application Insights SDK](../../azure-monitor/azure-monitor-app-hub.md) 서비스 메시에 대 한 필요 하지 않습니다. 사용할 수 있는 SDK 사용 하 여 응용 프로그램을 계측 하지 않고 Kubernetes를 모니터링 하는 메서드 아래.

## <a name="prerequisites"></a>필수 조건

- A [Kubernetes 클러스터](https://docs.microsoft.com/azure/aks/concepts-clusters-workloads)합니다.
- 실행 하는 클러스터에 대 한 액세스를 콘솔 *kubectl*합니다.
- [Application Insight 리소스](create-new-resource.md)
- 서비스 메시를 경우 클러스터 배포 Istio에 없는 경우 알아볼 수 있습니다 하는 방법 [를 설치 하 고 Istio를 사용 하 여 Azure Kubernetes Service에서](https://docs.microsoft.com/azure/aks/istio-install)합니다.

## <a name="capabilities"></a>기능

Kubernetes에 대 한 호스 티 드 앱 모니터링 0 계측 응용 프로그램을 사용 하 여 사용할 수 있습니다.

- [애플리케이션 맵](../../azure-monitor/app/app-map.md)
- [라이브 Stream 메트릭](../../azure-monitor/app/live-stream.md)
- [대시보드](../../azure-monitor/app/overview-dashboard.md)
- [메트릭 탐색기](../../azure-monitor/platform/metrics-getting-started.md)
- [Distributed-tracing](../../azure-monitor/app/distributed-tracing.md)
- [종단 간 트랜잭션 모니터링](../../azure-monitor/learn/tutorial-performance.md#identify-slow-server-operations)

## <a name="installation-steps"></a>설치 단계

솔루션을 사용 하려면 다음 단계를 수행 될 것 했습니다.
- (아직 배포 된 경우) 응용 프로그램을 배포 합니다.
- 서비스 메시의 일부인 응용 프로그램을 확인 합니다.
- 수집 된 원격 분석을 확인 합니다.

### <a name="configure-your-app-to-work-with-a-service-mesh"></a>서비스 메시를 사용 하도록 앱 구성

Istio의 두 가지 방법을 지원 [pod 계측](https://istio.io/docs/setup/kubernetes/additional-setup/sidecar-injection/)합니다.
대부분의 경우에서 응용 프로그램을 포함 하는 Kubernetes 네임 스페이스를 표시 하는 가장 쉬운 것은 *istio 주입* 레이블:

```console
kubectl label namespace <my-app-namespace> istio-injection=enabled
```

> [!NOTE]
> 서비스 메시 리프트 데이터 연결 해제 되 고 있으므로 암호화 된 트래픽을 가로챌 수 없습니다 했습니다. 클러스터를 남기지 않게 하는 트래픽을 암호화 되지 않은 프로토콜 (예: HTTP)을 사용 합니다. 외부 트래픽을 암호화 해야 하는 것이 좋습니다 [SSL 종료를 설정](https://kubernetes.io/docs/concepts/services-networking/ingress/#tls) 수신 컨트롤러에 있습니다.

서비스 메시는 외부에서 실행 중인 응용 프로그램을 받지 않습니다.

### <a name="deploy-your-application"></a>애플리케이션 배포

- 응용 프로그램을 배포 *my-앱-네임 스페이스* 네임 스페이스입니다. Istio 해당 사이드카; 삽입 되도록 pod를 다시 만들 필요가 응용 프로그램을 이미 배포한 경우 위에서 설명한 자동 사이드카 삽입 메서드를 수행한 롤링 업데이트를 시작 또는 개별 pod를 삭제 및 다시 생성 하기 위해 대기 합니다.
- 응용 프로그램 준수 확인 [Istio 요구 사항](https://istio.io/docs/setup/kubernetes/prepare/requirements/)합니다.

### <a name="deploy-zero-instrumentation-application-monitoring-for-kubernetes-hosted-apps"></a>Kubernetes에 대 한 호스 티 드 앱 모니터링 0 계측 응용 프로그램 배포

1. 다운로드 하 고 추출 된 [ *Application Insights 어댑터* 릴리스](https://github.com/Microsoft/Application-Insights-Istio-Adapter/releases/)합니다.
2. 이동할 */srckubernetes/* 릴리스 폴더 안에 있습니다.
3. Edit *application-insights-istio-mixer-adapter-deployment.yaml*
    - 값을 편집할 *ISTIO_MIXER_PLUGIN_AI_INSTRUMENTATIONKEY* 원격 분석을 포함 하는 Azure portal에서 Application Insights 리소스의 계측 키를 포함 하는 환경 변수입니다.
    - 필요한 경우 값을 편집할 *ISTIO_MIXER_PLUGIN_WATCHLIST_NAMESPACES* 환경 변수를 모니터링할 수 있도록 하려는 네임 스페이스의 쉼표로 구분 된 목록을 포함 합니다. 모든 네임 스페이스를 모니터링 하려면 비워 둡니다.
4. 적용 *마다* YAML 파일에서 찾을 *src/kubernetes/* 다음을 실행 하 여 (내 해야 */srckubernetes/*):

   ```console
   kubectl apply -f .
   ```

### <a name="verify-deployment"></a>배포 확인

- Application Insights 어댑터 배포 되었는지 확인 합니다.

  ```console
  kubectl get pods -n istio-system -l "app=application-insights-istio-mixer-adapter"
  ```
> [!NOTE]
> 경우에 따라 튜닝 미세 조정가 필요 합니다. 포함 하거나 제외할는 개별 pod에 대 한 원격 분석 수집을 사용 하 여 *appinsights/monitoring.enabled* 해당 pod의 레이블. 이 모든 네임 스페이스 기반 구성 보다 우선 순위를 갖습니다. 설정 *appinsights/monitoring.enabled* 하 *true* pod를 포함 하 고 *false* 제외 하기.

### <a name="view-application-insights-telemetry"></a>Application Insights 원격 분석 보기

- 모니터링가 올바르게 작동 하는지 확인 하려면 응용 프로그램에 대 한 샘플 요청을 생성 합니다.
- 3 ~ 5 분 내 Azure portal에 표시 하는 원격 분석 표시를 시작 해야 합니다. 체크 아웃 해야 합니다 *응용 프로그램 맵* 포털에서 Application Insights 리소스의 섹션입니다.

## <a name="troubleshooting"></a>문제 해결

아래 문제 해결 흐름 사용 원격 분석으로 Azure 포털에 나타나지 않는 경우에 필요 합니다.

1. 응용 프로그램 부하 및 일반 http에서 요청을 보내는 수신은 확인 합니다. 원격 분석 연결이 끊어져 리프트 된 하므로 암호화 된 트래픽을 지원 되지 않습니다. 들어오거나 나가는 요청 없음의 경우 있습니다 됩니다 없는 원격 분석 하거나.
2. 올바른 계측 키에서 제공 되는지 확인 합니다 *ISTIO_MIXER_PLUGIN_AI_INSTRUMENTATIONKEY* 환경 변수를 *application-insights-istio-mixer-adapter-deployment.yaml*합니다. 계측 키가 있는 합니다 *개요* Azure portal에서 Application Insights 리소스를 탭 합니다.
3. 올바른 Kubernetes 네임 스페이스에서 제공 되는지 확인 합니다 *ISTIO_MIXER_PLUGIN_WATCHLIST_NAMESPACES* 환경 변수를 *application-insights-istio-mixer-adapter-deployment.yaml*합니다. 모든 네임 스페이스를 모니터링 하려면 비워 둡니다.
4. 응용 프로그램의 pod Istio에 의해 사이드카 삽입 된를 확인 합니다. Istio의 사이드카 각 pod에 존재 하는지 확인 합니다.

   ```console
   kubectl describe pod -n <my-app-namespace> <my-app-pod-name>
   ```
   라는 컨테이너가 있는지 확인 하십시오 *istio 프록시* pod를 실행 합니다.

5. Application Insights 어댑터의 추적을 보고 합니다.

   ```console
   kubectl get pods -n istio-system -l "app=application-insights-istio-mixer-adapter"
   kubectl logs -n istio-system application-insights-istio-mixer-adapter-<fill in from previous command output>
   ```

   수신한 원격 분석 항목 수가 1 분 마다 업데이트 됩니다. 분별로-분 증가 하지 해당 하는 경우 원격 분석이 어댑터에 전송 하 여 Istio 여 되 됩니다.
   로그에 오류를 찾아보십시오.
6. 설정 된 경우는 *Kubernetes에 대 한 Application Insight* 어댑터는 원격 분석 공급 되지, Istio의 Mixer 로그 어댑터에 데이터를 보내는 것은 되는 이유를 확인 합니다.

   ```console
   kubectl get pods -n istio-system -l "istio=mixer,app=telemetry"
   kubectl logs -n istio-system istio-telemetry-<fill in from previous command output> -c mixer
   ```
   특히와 통신에 관련 된 모든 오류를 검색할 *applicationinsightsadapter* 어댑터.

## <a name="faq"></a>FAQ

이 프로젝트의 진행에 대 한 최신 정보를 참조 하세요. 합니다 [Istio Mixer 프로젝트의 GitHub에 대 한 Application Insights 어댑터](https://github.com/Microsoft/Application-Insights-Istio-Adapter/blob/master/SETUP.md#faq)합니다.

## <a name="uninstall"></a>제거

에 대 한 제품을 제거할 *마다* YAML 파일에서 찾을 *src/kubernetes/* 실행:

```console
kubectl delete -f <filename.yaml>
```


## <a name="next-steps"></a>다음 단계

Azure Monitor 및 컨테이너 방식으로 함께 방문에 자세히 알아보려면 [컨테이너 개요에 대 한 Azure Monitor](../../azure-monitor/insights/container-insights-overview.md)