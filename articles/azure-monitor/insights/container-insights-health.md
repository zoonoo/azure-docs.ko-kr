---
title: 컨테이너에 대한 Azure 모니터를 통해 Kubernetes 클러스터 상태 모니터링 | 마이크로 소프트 문서
description: 이 문서에서는 컨테이너에 대한 Azure Monitor를 통해 AKS 및 AKS가 아닌 클러스터의 상태를 보고 분석하는 방법에 대해 설명합니다.
ms.topic: conceptual
ms.date: 12/01/2019
ms.openlocfilehash: f50ef13efca78bbb5285b99759b8111dc1915ad0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76843993"
---
# <a name="understand-kubernetes-cluster-health-with-azure-monitor-for-containers"></a>컨테이너에 대한 Azure 모니터를 통해 Kubernetes 클러스터 상태 이해

컨테이너용 Azure Monitor를 사용하면 관리되는 인프라 구성 요소및 컨테이너에 대해 Azure Monitor에서 지원하는 모든 Kubernetes 클러스터에서 실행되는 모든 노드의 상태 상태를 모니터링하고 보고합니다. 이 환경은 [다중 클러스터 뷰에서](container-insights-analyze.md#multi-cluster-view-from-azure-monitor)계산및 보고된 클러스터 상태 이상으로 확장되며, 여기서 클러스터의 하나 이상의 노드가 리소스제한되거나 선별된 메트릭을 기반으로 클러스터에서 실행 중인 응용 프로그램에 영향을 줄 수 있는 노드 또는 포드를 사용할 수 없는지 확인할 수 있습니다.

>[!NOTE]
>상태 기능은 현재 공개 미리 보기입니다.
>

컨테이너에 대해 Azure 모니터를 사용하도록 설정하는 방법에 대한 자세한 내용은 [컨테이너에 대한 온보드 Azure 모니터를](container-insights-onboard.md)참조하십시오.

>[!NOTE]
>AKS 엔진 클러스터를 지원하려면 다음을 충족하는지 확인합니다.
>- 최신 버전의 [HELM 클라이언트를](https://helm.sh/docs/using_helm/)사용하고 있습니다.
>- 컨테이너화된 에이전트 버전은 *microsoft/oms:ciprod11012019*. 에이전트를 업그레이드하려면 [Kubernetes 클러스터의 업그레이드 에이전트를](container-insights-manage-agent.md#upgrade-agent-on-monitored-kubernetes-cluster)참조하십시오.
>

## <a name="overview"></a>개요

컨테이너용 Azure 모니터에서 상태(미리 보기) 기능은 Kubernetes 클러스터의 사전 상태 모니터링을 제공하여 문제를 식별하고 진단하는 데 도움을 줍니다. 그것은 당신에게 감지 된 중요한 문제를 볼 수있는 기능을 제공합니다. 클러스터의 컨테이너화된 에이전트에서 실행되는 클러스터의 상태를 평가하는 모니터링및 상태 데이터는 Log Analytics 작업 영역의 **KubeHealth** 테이블에 기록됩니다. 

Kubernetes 클러스터 상태는 다음과 같은 Kubernetes 개체 및 추상화에 의해 구성 된 모니터링 시나리오의 수를 기반으로 합니다.

- Kubernetes 인프라 - CPU 및 메모리 사용률을 평가하여 클러스터에 배포된 노드에서 실행되는 Kubernetes API 서버, ReplicaSet 및 데몬세트의 롤업과 Pods 가용성을 제공합니다.

    ![Kubernetes 인프라 상태 롤업 보기](./media/container-insights-health/health-view-kube-infra-01.png)

- 노드 - CPU 및 메모리 사용률을 평가하여 각 풀의 노드 풀 및 개별 노드 상태와 Kubernetes에서 보고한 노드의 상태를 롤업합니다.

    ![노드 상태 롤업 보기](./media/container-insights-health/health-view-nodes-01.png)

현재는 가상 쿠벨렛의 상태만 지원됩니다. CPU의 상태와 가상 kublet 노드의 메모리 사용률은 신호가 수신되지 않으므로 **알 수 없음으로**보고됩니다.

모든 모니터는 상태 계층 구조 창의 계층 적 레이아웃에 표시되며, 여기서 Kubernetes 개체 또는 추상화(즉, Kubernetes 인프라 또는 노드)를 나타내는 집계 모니터는 모두의 결합된 상태를 반영하는 최상위 모니터입니다. 종속 자식 모니터. 상태를 파생하는 데 사용되는 주요 모니터링 시나리오는 다음과 같습니다.

* 노드 및 컨테이너에서 CPU 사용률을 평가합니다.
* 노드 및 컨테이너에서 메모리 사용률을 평가합니다.
* Kubernetes가 보고한 준비 상태 계산을 기반으로 하는 포드 및 노드의 상태입니다.

상태를 표시하는 데 사용되는 아이콘은 다음과 같습니다.

|아이콘|의미|  
|--------|-----------|  
|![정상 상태를 나타내는 녹색 확인 아이콘](./media/container-insights-health/healthyicon.png)|성공, 상태 양호(녹색)|  
|![노란색 삼각형과 느낌표(경고)](./media/container-insights-health/warningicon.png)|경고(노란색)|  
|![흰색 X가 포함된 빨간색 단추(위험 상태를 나타냄)](./media/container-insights-health/criticalicon.png)|위험(빨간색)|  
|![회색 으로 표시된 아이콘](./media/container-insights-health/grayicon.png)|알 수 없음(회색)|  

## <a name="monitor-configuration"></a>모니터 구성

컨테이너 상태 기능에 대한 Azure 모니터를 지원하는 각 모니터의 동작 및 구성을 이해하려면 [상태 모니터 구성 가이드를](container-insights-health-monitors-config.md)참조하십시오.

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

[Azure 포털에](https://portal.azure.com)로그인합니다. 

## <a name="view-health-of-an-aks-or-non-aks-cluster"></a>AKS 또는 비 AKS 클러스터의 상태 보기

컨테이너 상태(미리 보기) 기능에 대한 Azure 모니터에 대한 액세스는 Azure 포털의 왼쪽 창에서 **인사이트를** 선택하여 AKS 클러스터에서 직접 사용할 수 있습니다. **인사이트** 섹션에서 **컨테이너**를 선택합니다. 

온-프레미스 또는 Azure 스택에서 호스팅되는 AKS 엔진 클러스터인 비 AKS 클러스터에서 상태를 보려면 Azure Portal의 왼쪽 창에서 **Azure Monitor를** 선택합니다. **인사이트** 섹션에서 **컨테이너**를 선택합니다.  다중 클러스터 페이지에서 목록에서 AKS가 아닌 클러스터를 선택합니다.

**컨테이너에** 대한 Azure 모니터에서 클러스터 페이지에서 **상태를 선택합니다.**

![클러스터 상태 대시보드 예제](./media/container-insights-health/container-insights-health-page.png)

## <a name="review-cluster-health"></a>클러스터 상태 검토

상태 페이지가 열리면 기본적으로 **Kubernetes 인프라가** **상태 측면** 그리드에서 선택됩니다.  그리드는 Kubernetes 인프라 및 클러스터 노드의 현재 상태 롤업 상태를 요약합니다. 상태 측면을 선택하면 상태 계층 창(즉, 중간 창)의 결과가 업데이트되고 모든 자식 모니터가 계층 적 레이아웃에 표시되어 현재 상태 상태가 표시됩니다. 종속 모니터에 대한 자세한 정보를 보려면 하나를 선택하고 속성 창이 페이지 오른쪽에 자동으로 표시됩니다. 

![클러스터 상태 속성 창](./media/container-insights-health/health-view-property-pane.png)

속성 창에서 다음을 배웁니다.

- **개요** 탭에는 선택한 모니터의 현재 상태, 모니터가 마지막으로 계산된 시간 및 마지막 상태 변경이 발생한 시기가 표시됩니다. 계층 구조에서 선택한 모니터 유형에 따라 추가 정보가 표시됩니다.

    상태 계층 창에서 집계 모니터를 선택하면 속성 창의 **개요** 탭 아래에 계층 구조의 총 하위 모니터 수와 중요, 경고 및 정상 상태에 있는 집계 모니터 수의 롤업이 표시됩니다. 

    ![집계 모니터에 대한 상태 속성 창 개요 탭](./media/container-insights-health/health-overview-aggregate-monitor.png)

    상태 계층 창에서 단위 모니터를 선택하면 **마지막 상태 변경** 에서 마지막 상태 변경에서 지난 4시간 이내에 컨테이너화된 에이전트가 계산하고 보고한 이전 샘플도 표시됩니다. 이는 여러 연속 값을 비교하여 상태를 결정하기 위한 단위 모니터 계산을 기반으로 합니다. 예를 들어 *Pod 준비 상태* 단위 모니터를 선택한 경우 매개 변수 *연속SamplesForStateTransition에*의해 제어되는 마지막 두 샘플을 표시합니다. 자세한 내용은 [장치 모니터에](container-insights-health-monitors-config.md#unit-monitors)대한 자세한 설명을 참조하십시오.
    
    ![상태 창 개요 탭](./media/container-insights-health/health-overview-unit-monitor.png)

    **Last 상태 변경에** 의해 보고된 시간이 하루 이상인 경우 모니터의 상태 변경이 없는 결과입니다. 그러나 단위 모니터에 대해 받은 마지막 샘플이 4시간 이상 지난 경우 컨테이너화된 에이전트가 데이터를 보내지 않은 것일 수 있습니다. 에이전트가 노드와 같은 특정 리소스가 존재한다는 것을 알고 있지만 노드의 CPU 또는 메모리 사용률 모니터에서 데이터를 받지 못했다면 모니터의 상태는 **알 수 없음으로**설정됩니다.  

- **구성** 탭에는 기본 구성 매개 변수 설정(집계 모니터가 아닌 단위 모니터에 대해서만 해당 값)이 표시됩니다.
- **기술** 탭에는 모니터의 동작과 비정상 조건에 대해 평가하는 방법을 설명하는 정보가 포함되어 있습니다.

이 페이지의 모니터링 데이터는 자동으로 새로 고쳐지지 않으며 페이지 상단에서 **새로 고침을** 선택하여 클러스터에서 받은 최신 상태를 확인해야 합니다.

## <a name="next-steps"></a>다음 단계

[로그 쿼리 예제를](container-insights-log-search.md#search-logs-to-analyze-data) 보고 미리 정의된 쿼리 및 예제를 보고 클러스터를 경고, 시각화 또는 분석하기 위해 평가하거나 사용자 지정합니다.
