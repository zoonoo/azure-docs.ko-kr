---
title: 컨테이너에 대 한 Azure Monitor를 사용 하 여 Kubernetes 클러스터 상태 모니터링 | Microsoft Docs
description: 이 문서는 컨테이너에 대 한 Azure Monitor를 사용 하 여 AKS 및 AKS 클러스터의 상태를 보고 분석 하는 방법을 설명 합니다.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 11/14/2019
ms.author: magoedte
ms.openlocfilehash: e369067a3ff61ffefe1758f6fa8b4acdce4bb2e2
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2019
ms.locfileid: "74134438"
---
# <a name="understand-kubernetes-cluster-health-with-azure-monitor-for-containers"></a>컨테이너에 대 한 Azure Monitor를 사용 하 여 Kubernetes 클러스터 상태 이해

컨테이너에 대 한 Azure Monitor를 사용 하 여 관리 인프라 구성 요소 및 컨테이너에 대 한 Azure Monitor에서 지 원하는 모든 Kubernetes 클러스터에서 실행 되는 모든 노드의 상태를 모니터링 하 고 보고 합니다. 이 환경은 클러스터 상태에 대해 계산 되 고 보고 된 클러스터 상태 [를 넘어](container-insights-analyze.md#multi-cluster-view-from-azure-monitor)확장 됩니다. 이제 클러스터에 있는 하나 이상의 노드가 리소스 제한 인지 또는 큐 레이트 메트릭을 기반으로 하는 클러스터에서 실행 중인 응용 프로그램에 영향을 줄 수 있는 노드 또는 pod를 사용할 수 없는지 파악할 수 있습니다. 

컨테이너에 대해 Azure Monitor를 사용 하도록 설정 하는 방법에 대 한 자세한 내용은 [컨테이너의 Azure Monitor](container-insights-onboard.md)등록을 참조 하세요.

## <a name="overview"></a>개요

컨테이너에 대 한 Azure Monitor 상태 기능은 문제를 식별 하 고 진단 하는 데 도움이 되는 Kubernetes 클러스터의 사전 대응 상태 모니터링을 제공 합니다. 검색 된 중요 한 문제를 볼 수 있는 기능을 제공 합니다. 클러스터의 컨테이너 화 된 에이전트에서 실행 되는 클러스터의 상태를 평가 하는 모니터와 상태 데이터는 Log Analytics 작업 영역의 **KubeHealth** 테이블에 기록 됩니다. 

Kubernetes 클러스터 상태는 다음과 같은 Kubernetes 개체 및 추상화로 구성 된 다양 한 모니터링 시나리오를 기반으로 합니다.

- Kubernetes 인프라-CPU 및 메모리 사용률과 Pod 가용성을 평가 하 여 클러스터에 배포 된 노드에서 실행 되는 Kubernetes API 서버, ReplicaSets 및 DaemonSets의 롤업을 제공 합니다.

    ![Kubernetes 인프라 상태 롤업 보기](./media/container-insights-health/health-view-kube-infra-01.png)

- 노드-CPU 및 메모리 사용률을 평가 하 고 Kubernetes에서 보고 하는 노드의 상태를 평가 하 여 각 풀의 노드 풀 및 상태에 대 한 롤업을 제공 합니다.

    ![노드 상태 롤업 보기](./media/container-insights-health/health-view-nodes-01.png)

현재는 가상 kubelet의 상태만 지원 됩니다. 가상 kublet 노드의 CPU 및 메모리 사용률 상태는 신호를 수신 하지 않으므로 **알 수 없음**으로 보고 됩니다.

모든 모니터는 상태 계층 창에서 계층 레이아웃으로 표시 됩니다. 여기에서 Kubernetes 개체 또는 추상화 (즉, Kubernetes 인프라 또는 노드)를 나타내는 집계 모니터는 모두의 결합 된 상태를 반영 하는 최상위 모니터입니다. 종속 된 자식 모니터 상태를 파생 시키는 데 사용 되는 주요 모니터링 시나리오는 다음과 같습니다.

* 노드 및 컨테이너에서 CPU 사용률을 평가 합니다.
* 노드 및 컨테이너에서 메모리 사용률을 평가 합니다.
* Kubernetes에서 보고 하는 준비 상태 계산을 기반으로 하는 Pod 및 노드의 상태입니다.

상태를 나타내는 데 사용 되는 아이콘은 다음과 같습니다.

|아이콘|의미|  
|--------|-----------|  
|![정상 상태임을 나타내는 녹색 확인 아이콘](./media/container-insights-health/healthyicon.png)|성공, 상태 양호 (녹색)|  
|![노란색 삼각형 및 느낌표는 경고입니다.](./media/container-insights-health/warningicon.png)|경고 (노란색)|  
|![흰색 X가 있는 빨간색 단추는 위험 상태를 나타냅니다.](./media/container-insights-health/criticalicon.png)|위험 (빨간색)|  
|![회색으로 표시 됨 아이콘](./media/container-insights-health/grayicon.png)|알 수 없음 (회색)|  

## <a name="monitor-configuration"></a>모니터 구성

컨테이너 상태 기능에 대 한 Azure Monitor를 지 원하는 각 모니터의 동작 및 구성을 이해 하려면 [상태 모니터 구성 가이드](container-insights-health-monitors-config.md)를 참조 하세요.

## <a name="sign-in-to-the-azure-portal"></a>Azure 포털에 로그인합니다.

[Azure Portal](https://portal.azure.com)에 로그인합니다. 

## <a name="view-health-of-an-aks-or-non-aks-cluster"></a>AKS 또는 비 AKS 클러스터의 상태 보기

컨테이너 상태에 대 한 Azure Monitor에 대 한 액세스는 Azure Portal의 왼쪽 창에서 **Insights** 를 선택 하 여 AKS 클러스터에서 직접 사용할 수 있습니다. **인사이트** 섹션에서 **컨테이너**를 선택합니다. 

AKS 않는 클러스터에서 온-프레미스 또는 Azure Stack에서 호스트 되는 AKS 엔진 클러스터의 상태를 보려면 Azure Portal의 왼쪽 창에서 **Azure Monitor** 를 선택 합니다. **인사이트** 섹션에서 **컨테이너**를 선택합니다.  다중 클러스터 페이지의 목록에서 비 AKS 클러스터를 선택 합니다.

컨테이너의 Azure Monitor **클러스터** 페이지에서 **상태**를 선택 합니다.

![클러스터 상태 대시보드 예](./media/container-insights-health/container-insights-health-page.png)

## <a name="review-cluster-health"></a>클러스터 상태 검토

상태 페이지가 열리면 기본적으로 **Kubernetes Infrastructure** 가 **상태 측면** 표에서 선택 됩니다.  표는 Kubernetes 인프라 및 클러스터 노드의 현재 상태 롤업 상태를 요약 합니다. 상태를 선택 하면 상태 계층 창 (즉, 가운데 창)에서 결과가 업데이트 되 고 모든 자식 모니터가 계층 레이아웃으로 표시 되어 현재 상태가 표시 됩니다. 종속 모니터에 대 한 자세한 정보를 보려면 하나를 선택 하 고 페이지의 오른쪽에 속성 창이 자동으로 표시 되도록 합니다. 

![클러스터 상태 속성 창](./media/container-insights-health/health-view-property-pane.png)

속성 창에서 다음에 대해 알아봅니다.

- **개요** 탭에서 선택한 모니터의 현재 상태, 모니터가 마지막으로 계산 된 시간 및 마지막 상태가 변경 된 시간을 표시 합니다. 계층에서 선택한 모니터 유형에 따라 추가 정보가 표시 됩니다.

    상태 계층 구조 창에서 집계 모니터를 선택 하는 경우 속성 창의 **개요** 탭에는 계층의 총 자식 모니터 수와 위험, 경고 및 정상 상태의 집계 모니터 수가 표시 됩니다. 

    ![집계 모니터에 대 한 상태 속성 창 개요 탭](./media/container-insights-health/health-overview-aggregate-monitor.png)

    상태 계층 구조 창에서 유닛 모니터를 선택 하는 경우 마지막 4 시간 이내에 컨테이너 화 된 에이전트가 계산 하 고 보고 한 **마지막 상태 변경** 아래에도 표시 됩니다. 이는 여러 연속 값을 비교 하 여 상태를 결정 하기 위한 유닛 모니터 계산을 기반으로 합니다. 예를 들어 *Pod 준비 상태* 유닛 모니터를 선택한 경우 *ConsecutiveSamplesForStateTransition*매개 변수에서 제어 하는 마지막 두 샘플이 표시 됩니다. 자세한 내용은 [유닛 모니터](container-insights-health-monitors-config.md#unit-monitors)에 대 한 자세한 설명을 참조 하세요.
    
    ![상태 속성 창 개요 탭](./media/container-insights-health/health-overview-unit-monitor.png)

    **마지막 상태 변경** 으로 보고 된 시간이 일 이상이 면 모니터의 상태가 변경 되지 않은 결과입니다. 그러나 유닛 모니터에 대해 받은 마지막 샘플이 4 시간을 초과 하는 경우에는 컨테이너 화 된 에이전트가 데이터를 전송 하지 않았음을 나타내는 것일 수 있습니다. 에이전트가 특정 리소스 (예: 노드)가 있지만 노드의 CPU 또는 메모리 사용률 모니터에서 데이터를 받지 못한 경우 (예를 들어) 모니터의 상태가 **알 수 없음**으로 설정 됩니다.  

- **구성** 탭에는 기본 구성 매개 변수 설정 (유닛 모니터의 경우에만, 집계 모니터는 그렇지 않음) 및 해당 값이 표시 됩니다.
- **기술 자료** 탭에는 모니터의 동작 및 비정상 상황을 평가 하는 방법을 설명 하는 정보가 포함 되어 있습니다.

이 페이지의 모니터링 데이터는 자동으로 새로 고쳐지지 않으며, 페이지 맨 위에 있는 **새로 고침** 을 선택 하 여 클러스터에서 받은 최신 상태를 확인 해야 합니다.

## <a name="next-steps"></a>다음 단계

[로그 쿼리 예](container-insights-log-search.md#search-logs-to-analyze-data) 를 확인 하 여 미리 정의 된 쿼리 및 예제를 확인 하거나 사용자 지정 하 여 클러스터를 경고, 시각화 또는 분석할 수 있습니다.