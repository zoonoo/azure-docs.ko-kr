---
title: 컨테이너용 Azure Monitor를 사용하여 Kubernetes 클러스터 상태 모니터링 | Microsoft Docs
description: 이 문서에서는 컨테이너용 Azure Monitor를 사용하여 AKS 및 AKS 이외 클러스터의 상태를 보고 분석하는 방법을 설명합니다.
ms.topic: conceptual
ms.date: 05/18/2020
ms.openlocfilehash: 94fdb6388134eae326ed5a8e46fbd3d13f8da2d5
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83649582"
---
# <a name="understand-kubernetes-cluster-health-with-azure-monitor-for-containers"></a>컨테이너용 Azure Monitor를 사용하여 Kubernetes 클러스터 상태 이해

컨테이너용 Azure Monitor는 관리형 인프라 구성 요소 및 컨테이너용 Azure Monitor가 지원하는 모든 Kubernetes 클러스터에서 실행되는 모든 노드의 상태를 모니터링하고 보고합니다. 따라서 이제 클러스터 상태가 평가되고 [다중 클러스터 보기](container-insights-analyze.md#multi-cluster-view-from-azure-monitor)에 보고되는 것을 넘어, 큐레이팅된 메트릭을 바탕으로 클러스터에 있는 하나 이상의 노드에 리소스가 한정적이거나 노드 또는 Pod를 사용할 수 없어서 클러스터에서 실행 중인 애플리케이션에 영향을 줄 수 있는지 파악할 수 있습니다.

>[!NOTE]
>상태 기능은 2020년 6월말에 프라이빗 미리 보기로 전환될 예정입니다. 자세한 내용은 다음 [Azure 업데이트 공지](https://azure.microsoft.com/updates/ci-health-limited-preview/)를 참조하세요.
>

컨테이너용 Azure Monitor를 사용하도록 설정하는 방법은 [컨테이너용 Azure Monitor 등록](container-insights-onboard.md)을 참조하세요.

>[!NOTE]
>AKS Engine 클러스터를 지원하려면 다음을 충족하는지 확인합니다.
>- 최신 버전의 [HELM 클라이언트](https://helm.sh/docs/using_helm/)를 사용하고 있습니다.
>- 컨테이너화된 에이전트 버전은 *microsoft/oms:ciprod11012019*입니다. 에이전트를 업그레이드하려면 [Kubernetes 클러스터에서 에이전트 업그레이드](container-insights-manage-agent.md#how-to-upgrade-the-azure-monitor-for-containers-agent)를 참조하세요.
>

## <a name="overview"></a>개요

컨테이너용 Azure Monitor의 상태(미리 보기) 기능은 문제를 식별하고 진단하는 데 도움이 되는 Kubernetes 클러스터의 사전 상태 모니터링을 제공합니다. 검색된 중요한 문제를 볼 수 있는 기능을 제공합니다. 클러스터의 컨테이너화된 에이전트에서 실행되는 클러스터의 상태를 평가하는 모니터와 상태 데이터는 Log Analytics 작업 영역의 **KubeHealth** 테이블에 기록됩니다. 

Kubernetes 클러스터 상태는 다음과 같은 Kubernetes 개체 및 추상화로 구성된 다양한 모니터링 시나리오를 기반으로 합니다.

- Kubernetes 인프라 - CPU 및 메모리 사용률과 Pod 가용성을 평가하여 클러스터에 배포된 노드에서 실행 중인 Kubernetes API 서버, ReplicaSets 및 DaemonSets의 롤업을 제공합니다.

    ![Kubernetes 인프라 상태 롤업 보기](./media/container-insights-health/health-view-kube-infra-01.png)

- 노드 - CPU 및 메모리 사용률과 Kubernetes에서 보고한 노드의 상태를 평가하여 노드 풀 및 각 풀의 개별 노드 상태에 대한 롤업을 제공합니다.

    ![노드 상태 롤업 보기](./media/container-insights-health/health-view-nodes-01.png)

현재는 가상 kubelet의 상태만 지원됩니다. 가상 kublet 노드의 CPU 및 메모리 사용률 상태는 신호가 수신되지 않아 **알 수 없음**으로 보고됩니다.

모든 모니터는 상태 계층 구조 창에 계층 구조 레이아웃으로 표시됩니다. 여기서 Kubernetes 개체 또는 추상화(즉, Kubernetes 인프라 또는 노드)를 나타내는 집계 모니터는 모든 종속 자식 모니터의 결합된 상태를 반영하는 최상위 모니터입니다. 상태를 파생시키는 데 사용되는 주요 모니터링 시나리오는 다음과 같습니다.

* 노드 및 컨테이너에서 CPU 사용률을 평가합니다.
* 노드 및 컨테이너에서 메모리 사용률을 평가합니다.
* Kubernetes에서 보고한 준비 상태 평가를 기반으로 하는 Pod 및 노드의 상태.

상태를 나타내는 데 사용되는 아이콘은 다음과 같습니다.

|아이콘|의미|  
|--------|-----------|  
|![정상 상태를 나타내는 녹색 확인 아이콘](./media/container-insights-health/healthyicon.png)|성공, 상태 양호(녹색)|  
|![경고를 나타내는 노란색 삼각형과 느낌표](./media/container-insights-health/warningicon.png)|경고(노란색)|  
|![위험 상태를 나타내는 흰색 X가 포함된 빨간색 단추](./media/container-insights-health/criticalicon.png)|위험(빨간색)|  
|![회색 표시된 아이콘](./media/container-insights-health/grayicon.png)|알 수 없음(회색)|  

## <a name="monitor-configuration"></a>모니터 구성

컨테이너용 Azure Monitor의 상태 기능을 지원하는 각 모니터의 동작 및 구성을 이해하려면 [상태 모니터 구성 가이드](container-insights-health-monitors-config.md)를 참조하세요.

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

[Azure Portal](https://portal.azure.com)에 로그인합니다. 

## <a name="view-health-of-an-aks-or-non-aks-cluster"></a>AKS 또는 AKS 이외 클러스터의 상태 보기

Azure Portal의 왼쪽 창에서 **인사이트(미리 보기)** 를 선택하여 AKS 클러스터에서 직접 컨테이너용 Azure Monitor의 상태(미리 보기) 기능에 액세스할 수 있습니다. **인사이트** 섹션에서 **컨테이너**를 선택합니다. 

AKS 이외 클러스터, 즉 온-프레미스 또는 Azure Stack에서 호스트되는 AKS Engine 클러스터의 상태를 보려면 Azure Portal 왼쪽 창에서 **Azure Monitor**를 선택합니다. **인사이트** 섹션에서 **컨테이너**를 선택합니다.  다중 클러스터 페이지의 목록에서 AKS 이외 클러스터를 선택합니다.

컨테이너용 Azure Monitor의 **클러스터** 페이지에서 **상태**를 선택합니다.

![클러스터 상태 대시보드 예제](./media/container-insights-health/container-insights-health-page.png)

## <a name="review-cluster-health"></a>클러스터 상태 검토

상태 페이지가 열리면 **상태 양상** 표에서 기본적으로 **Kubernetes 인프라**가 선택됩니다.  이 표에는 Kubernetes 인프라 및 클러스터 노드의 현재 상태 롤업 상태가 간략히 표시됩니다. 상태를 선택하면 상태 계층 구조 창(즉, 가운데 창)에 결과가 업데이트되고 모든 자식 모니터가 계층 구조 레이아웃에 표시되며 현재 상태가 표시됩니다. 종속 모니터에 대한 자세한 정보를 보려면 해당 항목을 선택합니다. 그러면 페이지의 오른쪽에 속성 창이 자동으로 표시됩니다. 

![클러스터 상태 속성 창](./media/container-insights-health/health-view-property-pane.png)

속성 창에서 다음 내용을 알아봅니다.

- **개요** 탭에는 선택한 모니터의 현재 상태, 모니터가 마지막으로 평가된 시간 및 마지막으로 상태가 변경된 시간이 표시됩니다. 계층 구조에서 선택한 모니터 유형에 따라 추가 정보가 표시됩니다.

    상태 계층 구조 창에서 집계 모니터를 선택하면 속성 창의 **개요** 탭에 계층 구조의 총 자식 모니터 수에 대한 롤업과 위험, 경고 및 정상 상태의 집계 모니터 수가 표시됩니다. 

    ![상태 속성 창의 집계 모니터 개요 탭](./media/container-insights-health/health-overview-aggregate-monitor.png)

    상태 계층 구조 창에서 유닛 모니터를 선택하면 지난 4시간 동안 컨테이너화된 에이전트가 평가하고 보고한 이전 샘플이 **마지막 상태 변경** 아래에도 표시됩니다. 이는 유닛 모니터 평가를 기반으로 여러 연속 값을 비교하여 상태를 결정합니다. 예를 들어 *Pod 준비 상태* 유닛 모니터를 선택한 경우 *ConsecutiveSamplesForStateTransition* 매개 변수에 의해 제어되는 마지막 두 샘플이 표시됩니다. 자세한 내용은 [유닛 모니터](container-insights-health-monitors-config.md#unit-monitors)를 참조하세요.
    
    ![상태 속성 창 개요 탭](./media/container-insights-health/health-overview-unit-monitor.png)

    **마지막 상태 변경**에 의해 보고된 시간이 하루 이상이면 모니터 상태가 변경되지 않은 것입니다. 그러나 유닛 모니터에 대해 받은 마지막 샘플이 4시간을 경과한 경우에는 컨테이너화된 에이전트가 데이터를 전송하지 않은 것일 수 있습니다. 에이전트가 특정 리소스(예: 노드)가 있다는 것을 알지만 해당 노드의 CPU 또는 메모리 사용률 모니터에서 데이터를 받지 못한 경우 모니터의 상태가 **알 수 없음**으로 설정됩니다.  

- **구성** 탭에는 기본 구성 매개 변수 설정(유닛 모니터만 해당, 집계 모니터는 해당 없음) 및 해당 값이 표시됩니다.
- **정보** 탭에는 모니터의 동작과 비정상 상태를 평가하는 방법을 설명하는 정보가 포함되어 있습니다.

이 페이지의 모니터링 데이터는 자동으로 새로 고침되지 않으며, 페이지 맨 위에 있는 **새로 고침**을 선택하여 클러스터에서 받은 가장 최근의 상태를 확인해야 합니다.

## <a name="next-steps"></a>다음 단계

클러스터를 경고, 시각화 또는 분석하기 위해 평가하거나 사용자 지정하는 미리 정의된 쿼리 및 예제를 보려면 [로그 쿼리 예제](container-insights-log-search.md#search-logs-to-analyze-data)를 확인하세요.
