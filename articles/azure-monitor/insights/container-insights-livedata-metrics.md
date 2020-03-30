---
title: 컨테이너에 대한 Azure 모니터를 통해 실시간으로 메트릭 보기 | 마이크로 소프트 문서
description: 이 문서에서는 컨테이너에 대해 Azure Monitor에서 kubectl을 사용하지 않고 메트릭의 실시간 보기를 설명합니다.
ms.topic: conceptual
ms.date: 10/15/2019
ms.openlocfilehash: 4604635c985057ec0b7f49a0d1cca7111dfc8eec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79216573"
---
# <a name="how-to-view-metrics-in-real-time"></a>실시간으로 메트릭을 보는 방법

컨테이너에 대한 Azure 모니터 라이브 데이터(미리 보기) 기능을 사용하면 클러스터의 노드 및 포드 상태에 대한 메트릭을 실시간으로 시각화할 수 있습니다. 이 Insight에 포함된 성능 `kubectl top nodes`차트에서 `kubectl get nodes` 데이터를 호출, 구문 분석 및 시각화하는 및 에대한 `kubectl get pods –all-namespaces`직접 액세스를 에뮬레이트합니다. 

이 문서에서는 자세한 개요를 제공 하며이 기능을 사용 하는 방법을 이해 하는 데 도움이 됩니다.  

>[!NOTE]
>개인 클러스터로 활성화된 AKS [클러스터는](https://azure.microsoft.com/updates/aks-private-cluster/) 이 기능에서 지원되지 않습니다. 이 기능은 브라우저에서 프록시 서버를 통해 Kubernetes API에 직접 액세스하는 데 의존합니다. 네트워킹 보안을 사용하여 이 프록시에서 Kubernetes API를 차단하면 이 트래픽이 차단됩니다. 

>[!NOTE]
>이 기능은 Azure China를 포함한 모든 Azure 지역에서 사용할 수 있습니다. 현재 Azure 미국 정부에서는 사용할 수 없습니다.

라이브 데이터(미리 보기) 기능 설정 또는 문제 해결에 대한 도움말을 보려면 [설정 가이드를](container-insights-livedata-setup.md)참조하십시오.

## <a name="how-it-works"></a>작동 방식 

라이브 데이터(미리 보기) 기능은 Kubernetes API에 직접 액세스하며 인증 모델에 대한 추가 정보는 [여기에서](https://kubernetes.io/docs/concepts/overview/kubernetes-api/)확인할 수 있습니다. 

이 기능은 기본적으로 5초마다 되는 메트릭 `/api/v1/nodes`끝점(포함 `/apis/metrics.k8s.io/v1beta1/nodes`및)에 `/api/v1/pods`대해 폴링 작업을 수행합니다. 이 데이터는 브라우저에 캐시되며 **라이브 이동(미리 보기)을**선택하여 **클러스터** 탭의 컨테이너에 대한 Azure Monitor에 포함된 네 가지 성능 차트에 차트로 표시됩니다. 이후의 각 설문 조사는 5분 짜리 시각화 창으로 차트화됩니다. 

![클러스터 보기에서 라이브 이동 옵션](./media/container-insights-livedata-metrics/cluster-view-go-live-example-01.png)

폴링 간격은 설정 **간격** 드롭다운에서 구성되므로 1초, 5초, 15초 및 30초마다 새 데이터에 대한 폴링을 설정할 수 있습니다. 

![라이브 드롭다운 폴링 간격 이동](./media/container-insights-livedata-metrics/cluster-view-polling-interval-dropdown.png)

>[!IMPORTANT]
>짧은 시간 동안 문제를 해결하는 동안 폴링 간격을 1초로 설정하는 것이 좋습니다. 이러한 요청은 클러스터에서 Kubernetes API의 가용성 및 제한에 영향을 미칠 수 있습니다. 그런 다음 더 긴 폴링 간격으로 다시 구성합니다. 

>[!IMPORTANT]
>이 기능을 작동하는 동안 데이터가 영구적으로 저장되지 않습니다. 이 세션 중에 캡처된 모든 정보는 브라우저를 닫거나 기능에서 벗어나면 즉시 삭제됩니다. 데이터는 5분 창 안에 시각화를 위해서만 존재합니다. 5분 이상 된 모든 메트릭도 영구적으로 삭제됩니다.

이러한 차트는 라이브 모드에서 본 마지막 Azure 대시보드에 고정할 수 없습니다.

## <a name="metrics-captured"></a>캡처된 메트릭

### <a name="node-cpu-utilization---node-memory-utilization-"></a>노드 CPU 사용률 % / 노드 메모리 사용률 % 

이 두 성능 차트는 **CPU%** `kubectl top nodes` 및 **MEMORY%** 열의 결과를 각 차트에 호출하고 캡처하는 것과 동등한 것으로 매핑됩니다. 

![Kubectl 상단 노드 예제 결과](./media/container-insights-livedata-metrics/kubectl-top-nodes-example.png)

![노드 CPU 사용률 백분율 차트](./media/container-insights-livedata-metrics/cluster-view-node-cpu-util.png)

![노드 메모리 사용률 백분율 차트](./media/container-insights-livedata-metrics/cluster-view-node-memory-util.png)

백분위수 계산은 클러스터의 이상값 노드를 식별하는 데 도움이 되는 더 큰 클러스터에서 작동합니다. 예를 들어 노드가 축소를 위해 활용도가 낮은지 이해합니다. **최소** 집계를 활용하면 클러스터에서 사용률이 낮은 노드를 확인할 수 있습니다. 추가 조사를 위해 노드 탭을 선택하고 CPU 또는 메모리 사용률별로 그리드를 **정렬합니다.**

또한 어떤 노드가 한계에 밀려있는지, 확장이 필요한지 파악할 수 있습니다. **Max** 및 **P95** 집계를 모두 활용하면 리소스 사용률이 높은 클러스터에 노드가 있는지 확인할 수 있습니다. 추가 조사를 위해 노드 탭으로 다시 **전환합니다.**

### <a name="node-count"></a>노드 수

이 성능 차트는 **STATUS** 열을 `kubectl get nodes` 상태 유형별로 그룹화된 차트에 호출하고 매핑하는 것과 동일한 것으로 매핑됩니다.

![Kubectl 노드 예제 결과](./media/container-insights-livedata-metrics/kubectl-get-nodes-example.png)

![노드 카운트 차트](./media/container-insights-livedata-metrics/cluster-view-node-count-01.png)

노드는 **준비** 또는 준비 **되지 않은** 상태로 보고 됩니다. 이러한 집계는 계산되고 총 개수가 생성되며 이 두 집계의 결과가 차트로 표시됩니다.
예를 들어 노드가 실패한 상태로 떨어지는지 확인합니다. 준비 **되지 않음** 집계를 활용 하면 신속 하 게 **현재 준비 되지 않은** 상태에 클러스터에서 노드의 수를 볼 수 있습니다.

### <a name="active-pod-count"></a>활성 포드 수

이 성능 차트는 호출과 동일한 `kubectl get pods –all-namespaces` 차트에 매핑하고 상태 유형별로 그룹화된 **차트의 STATUS** 열을 매핑합니다.

![Kubectl 얻을 포드 예제 결과](./media/container-insights-livedata-metrics/kubectl-get-pods-example.png)

![노드 포드 카운트 차트](./media/container-insights-livedata-metrics/cluster-view-node-pod-count.png)

>[!NOTE]
>해석된 `kubectl` 상태 이름은 차트에서 정확히 일치하지 않을 수 있습니다. 

## <a name="next-steps"></a>다음 단계

[로그 쿼리 예제를](container-insights-log-search.md#search-logs-to-analyze-data) 보고 미리 정의된 쿼리 및 예제를 확인하여 경고, 시각화를 만들거나 클러스터에 대한 추가 분석을 수행합니다.
