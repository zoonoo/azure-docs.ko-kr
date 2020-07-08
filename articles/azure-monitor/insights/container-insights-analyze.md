---
title: 컨테이너에 대 한 Azure Monitor를 사용 하 여 Kubernetes 모니터링 | Microsoft Docs
description: 이 문서는 컨테이너에 대 한 Azure Monitor를 사용 하 여 Kubernetes 클러스터의 성능을 확인 하 고 분석 하는 방법을 설명 합니다.
ms.topic: conceptual
ms.date: 03/26/2020
ms.openlocfilehash: 17efb89e36429f9658759a0ed90d4e7b785fe4b7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85340907"
---
# <a name="monitor-your-kubernetes-cluster-performance-with-azure-monitor-for-containers"></a>컨테이너에 대 한 Azure Monitor를 사용 하 여 Kubernetes 클러스터 성능 모니터링

컨테이너에 대 한 Azure Monitor를 사용 하 여 성능 차트와 상태를 사용 하 여 AKS (Azure Kubernetes Service), Azure Stack 또는 다른 환경에서 호스트 되는 Kubernetes 클러스터의 작업을 모니터링할 수 있습니다. 클러스터에서 직접 모니터링 하거나 Azure Monitor에서 구독의 모든 클러스터를 볼 수 있습니다. 특정 AKS 클러스터를 모니터링 하는 경우에도 Azure Container Instances을 볼 수 있습니다.

이 문서에서는 두 가지 관점을 이해 하 고 검색 된 문제를 신속 하 게 평가, 조사 및 해결 하는 데 도움이 되 Azure Monitor 방법을 설명 합니다.

컨테이너용 Azure Monitor를 사용하도록 설정하는 방법은 [컨테이너용 Azure Monitor 등록](container-insights-onboard.md)을 참조하세요.

Azure Monitor는 구독에서 리소스 그룹에 배포 된 Linux 및 Windows Server 2019를 실행 하는 모든 모니터링 되는 Kubernetes 클러스터의 상태를 보여 주는 다중 클러스터 뷰를 제공 합니다. 솔루션에서 모니터링 되지 않는 모든 환경에서 검색 된 클러스터를 표시 합니다. 클러스터 상태를 즉시 이해할 수 있으며, 여기에서 노드 및 컨트롤러 성능 페이지로 드릴 다운 하거나 탐색 하 여 클러스터에 대 한 성능 차트를 볼 수 있습니다. 검색 되 고 모니터링 되지 않는 것으로 식별 된 AKS 클러스터의 경우 언제 든 지 모니터링을 사용 하도록 설정할 수 있습니다.

Linux 클러스터와 비교 하 여 컨테이너에 대 한 Azure Monitor 포함 된 Windows Server 클러스터 모니터링의 주요 차이점은 개요 문서 [에서 설명 합니다](container-insights-overview.md#what-does-azure-monitor-for-containers-provide) .

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

[Azure Portal](https://portal.azure.com)에 로그인합니다.

## <a name="multi-cluster-view-from-azure-monitor"></a>Azure Monitor에서 다중 클러스터 보기

배포 된 모든 Kubernetes 클러스터의 상태를 보려면 Azure Portal의 왼쪽 창에서 **모니터** 를 선택 합니다. **인사이트** 섹션에서 **컨테이너**를 선택합니다.

![Azure Monitor 다중 클러스터 대시보드 예제](./media/container-insights-analyze/azmon-containers-multiview.png)

표에 표시 된 결과의 범위를 표시 하 여 다음과 같은 클러스터를 표시할 수 있습니다.

* Azure Kubernetes Service에서 호스트 되는 **azure** AKS 및 AKS 클러스터
* **Azure Stack (미리 보기)** -Azure Stack에 호스트 된 AKS-엔진 클러스터
* **비 Azure (미리 보기)** -온-프레미스에서 호스트 되는 Kubernetes 클러스터
* **모두** -Azure, Azure Stack 및 컨테이너에 대해 Azure Monitor 등록 된 온-프레미스 환경에서 호스트 되는 모든 Kubernetes 클러스터를 확인 합니다.

특정 환경에서 클러스터를 보려면 페이지의 왼쪽 위 모서리에 있는 **환경** 약에서 클러스터를 선택 합니다.

![Environment 약 선택기 예제](./media/container-insights-analyze/clusters-multiview-environment-pill.png)

모니터링 되는 **클러스터** 탭에서 다음에 대해 알아봅니다.

- 위험 또는 비정상 상태에 있는 클러스터의 수는 정상 또는 보고 되지 않은 상태 (알 수 없는 상태)와 비교 합니다.
- 모든 [Azure Kubernetes 엔진 (AKS)](https://github.com/Azure/aks-engine) 배포가 정상 상태 인지 여부입니다.
- 클러스터 당 배포 되는 노드와 사용자 및 시스템 pod 수입니다.
- 사용할 수 있는 디스크 공간의 양과 용량 문제가 있는지 여부입니다.

다음과 같은 상태가 있습니다.

* **정상**: VM에 대 한 문제가 검색 되지 않으며, 필요에 따라 작동 합니다.
* **위험**: 정상적인 작동 상태를 예상 대로 복원 하기 위해 해결 해야 하는 하나 이상의 중요 한 문제가 발견 되었습니다.
* **경고**: 해결 해야 하는 하나 이상의 문제가 검색 되었거나 상태 조건이 중요 한 것일 수 있습니다.
* **알 수 없음**: 서비스에서 노드나 pod에 연결할 수 없는 경우 상태가 알 수 없음 상태로 변경 됩니다.
* **찾을 수 없음**:이 솔루션에 대 한 작업 영역을 포함 하는 작업 영역, 리소스 그룹 또는 구독이 삭제 되었습니다.
* **권한 없음**: 사용자에 게 작업 영역의 데이터를 읽는 데 필요한 권한이 없습니다.
* **오류**: 작업 영역에서 데이터를 읽는 동안 오류가 발생 했습니다.
* **잘못**구성 됨: 컨테이너의 Azure Monitor 지정 된 작업 영역에서 올바르게 구성 되지 않았습니다.
* **데이터 없음**: 지난 30 분 동안 데이터가 작업 영역에 보고 되지 않았습니다.

상태는 전체 클러스터 상태를 한 가지 예외를 제외 하 고 세 가지 상태의 *최악의* 것으로 계산 합니다. 세 가지 상태 중 하나를 알 수 없는 경우 전체 클러스터 상태에서 **알 수 없음**이 표시 됩니다.

다음 표에서는 다중 클러스터 뷰에서 모니터링 되는 클러스터의 상태를 제어 하는 계산에 대 한 분석을 제공 합니다.

| |상태 |가용성 |
|-------|-------|-----------------|
|**사용자 pod**| | |
| |Healthy |100% |
| |경고 |90-99% |
| |위험 |<90% |
| |알 수 없음 |지난 30분 동안 보고하지 않은 경우 |
|**시스템 pod**| | |
| |Healthy |100% |
| |경고 |해당 없음 |
| |위험 |<100% |
| |알 수 없음 |지난 30분 동안 보고하지 않은 경우 |
|**노드** | | |
| |Healthy |>85% |
| |경고 |60 - 84% |
| |위험 |<60% |
| |알 수 없음 |지난 30분 동안 보고하지 않은 경우 |

클러스터의 목록에서 클러스터의 이름을 선택 하 여 **클러스터** 페이지로 드릴 다운할 수 있습니다. 그런 다음 노드 성능 페이지로 이동 하 여 해당 특정 클러스터에 대 한 **노드** 열에서 노드 롤업을 **선택 합니다.** 또는 **사용자 pod** 또는 **System pod** 열에 대 한 롤업을 선택 하 여 **컨트롤러** 성능 페이지로 드릴 다운할 수 있습니다.

## <a name="view-performance-directly-from-a-cluster"></a>클러스터에서 직접 성능 보기

컨테이너의 Azure Monitor에 대 한 액세스는 **Insights**  >  왼쪽 창에서 Insights**클러스터** 를 선택 하거나 다중 클러스터 뷰에서 클러스터를 선택 하 여 AKS 클러스터에서 직접 사용할 수 있습니다. 클러스터에 대 한 정보는 다음 네 가지 관점으로 구성 됩니다.

- 클러스터
- 노드
- Controllers
- 컨테이너

>[!NOTE]
>이 문서의 나머지 부분에서 설명 하는 환경은 다중 클러스터 보기에서 선택할 경우 Azure Stack 또는 다른 환경에서 호스트 되는 Kubernetes 클러스터의 성능 및 상태를 보는 데에도 적용 됩니다.

기본 페이지가 열리고 클러스터의 주요 성능 메트릭을 보여 주는 4 개의 꺾은선형 성능 차트가 표시 됩니다.

![클러스터 탭의 예제 성능 차트](./media/container-insights-analyze/containers-cluster-perfview.png)

성능 차트는 4 가지 성능 메트릭을 표시 합니다.

- **노드 cpu 사용률 &nbsp; % **: 전체 클러스터에 대 한 cpu 사용률의 집계 된 관점입니다. 시간 범위에 대 한 결과를 필터링 하려면 차트 위의 백분위 수 선택기에서 **Avg**, **Min**, **50**, **90**, **95**또는 **Max** 를 선택 합니다. 필터는 개별적으로 사용 하거나 조합 하 여 사용할 수 있습니다.
- **노드 메모리 사용률 &nbsp; % **: 전체 클러스터의 메모리 사용률에 대 한 집계 된 관점입니다. 시간 범위에 대 한 결과를 필터링 하려면 차트 위의 백분위 수 선택기에서 **Avg**, **Min**, **50**, **90**, **95**또는 **Max** 를 선택 합니다. 필터는 개별적으로 사용 하거나 조합 하 여 사용할 수 있습니다.
- **노드 수**: Kubernetes의 노드 수 및 상태입니다. 표시 되는 클러스터 노드의 상태는 총, 준비 됨, 준비 되지 않음입니다. 이러한 필터는 개별적으로 필터링 하거나 차트 위의 선택기에서 결합할 수 있습니다.
- **활성 pod 수**: Kubernetes의 pod 개수 및 상태입니다. 표시 되는 pod의 상태는 Total, Pending, Running, Unknown, Succeeded 또는 Failed입니다. 이러한 필터는 개별적으로 필터링 하거나 차트 위의 선택기에서 결합할 수 있습니다.

왼쪽 및 오른쪽 화살표 키를 사용 하 여 차트의 각 데이터 요소를 순환 합니다. 위쪽 및 아래쪽 화살표 키를 사용 하 여 백분위 수 선 사이를 순환 합니다. 차트 중 하나의 오른쪽 위 모퉁이에 있는 고정 아이콘을 선택 하 여 선택한 차트를 표시 된 마지막 Azure 대시보드에 고정 합니다. 대시보드에서 차트의 크기를 조정하고 위치를 변경할 수 있습니다. 대시보드에서 차트를 선택 하면 컨테이너에 대 한 Azure Monitor으로 리디렉션되고 올바른 범위와 보기가 로드 됩니다.

컨테이너에 대 한 Azure Monitor는 사용자 고유의 플롯 차트를 만들고, 추세를 상호 연결 하 고, 추세를 조사 하 고, 대시보드에 고정할 수 있는 Azure Monitor [메트릭 탐색기](../platform/metrics-getting-started.md)도 지원 합니다. 메트릭 탐색기에서 메트릭 [기반 경고 규칙](../platform/alerts-metric.md)의 기반으로 메트릭을 시각화 하기 위해 설정 하는 조건을 사용할 수도 있습니다.

## <a name="view-container-metrics-in-metrics-explorer"></a>메트릭 탐색기에서 컨테이너 메트릭 보기

메트릭 탐색기에서 컨테이너에 대 한 Azure Monitor 집계 된 노드 및 pod 사용률 메트릭을 볼 수 있습니다. 다음 표에서는 메트릭 차트를 사용 하 여 컨테이너 메트릭을 시각화 하는 방법을 이해 하는 데 도움이 되는 세부 정보를 요약 하 여 보여 줍니다.

|네임스페이스 | 메트릭 | Description |
|----------|--------|-------------|
| 정보. 컨테이너/노드 | |
| | cpuUsageMillicores | 클러스터 전반의 CPU 사용률 집계 측정. 1000 단위로 분할 된 CPU 코어 (밀리초 = 1000)입니다. 많은 응용 프로그램에서 코어 하나를 사용할 수 있는 컨테이너의 코어 사용량을 확인 하는 데 사용 됩니다.|
| | cpuUsagePercentage | 집계 된 평균 CPU 사용률은 클러스터 전체에서 백분율로 측정 됩니다.|
| | memoryRssBytes | 사용 되는 컨테이너 RSS 메모리 (바이트)입니다.|
| | memoryRssPercentage | %에 사용 되는 컨테이너 RSS 메모리입니다.|
| | memoryWorkingSetBytes | 사용 되는 컨테이너 작업 집합 메모리입니다.|
| | memoryWorkingSetPercentage | %에 사용 되는 컨테이너 작업 집합 메모리입니다. |
| | nodesCount | Kubernetes의 노드 수입니다.|
| pod/ | |
| | PodCount | Kubernetes의 pod 개수입니다.|

메트릭을 [분할](../platform/metrics-charts.md#apply-splitting-to-a-chart) 하 여 차원 별로 표시 하 고 서로 다른 세그먼트의 서로 비교 하는 방법을 시각화할 수 있습니다. 노드의 경우 *호스트* 차원에 따라 차트를 분할할 수 있습니다. Pod에서 다음 차원으로 분할할 수 있습니다.

* Controller
* Kubernetes 네임 스페이스
* 노드
* 단계

## <a name="analyze-nodes-controllers-and-container-health"></a>노드, 컨트롤러 및 컨테이너 상태 분석

**노드**, **컨트롤러**및 **컨테이너** 탭으로 전환 하면 속성 창이 페이지의 오른쪽에 자동으로 표시 됩니다. 선택한 항목의 속성을 보여 줍니다. 여기에는 Kubernetes 개체를 구성 하기 위해 정의한 레이블이 포함 됩니다. Linux 노드를 선택 하면 **로컬 디스크 용량** 섹션에는 사용 가능한 디스크 공간과 노드에 표시 된 각 디스크에 사용 되는 비율도 표시 됩니다. 창 **>>** 에서 링크를 선택 하 여 창을 보거나 숨깁니다.

계층 구조에서 개체를 펼치면 선택한 개체에 따라 속성 창이 업데이트됩니다. 창 맨 위에 있는 **라이브 데이터 보기 (미리 보기)** 링크를 선택 하 여 창에서 Kubernetes 컨테이너 로그 (stdout/stderror), 이벤트 및 pod 메트릭을 볼 수도 있습니다. 이 데이터를 볼 수 있는 액세스 권한을 부여 하 고 제어 하는 데 필요한 구성에 대 한 자세한 내용은 [라이브 데이터 설정 (미리 보기)](container-insights-livedata-setup.md)을 참조 하세요. 클러스터 리소스를 검토 하는 동안 컨테이너에서 실시간으로이 데이터를 볼 수 있습니다. 이 기능에 대 한 자세한 내용은 [실시간으로 Kubernetes 로그, 이벤트 및 pod 메트릭을 보는 방법](container-insights-livedata-overview.md)을 참조 하세요. 미리 정의 된 로그 검색을 기반으로 작업 영역에 저장 된 Kubernetes 로그 데이터를 보려면 **분석의 보기** 드롭다운 목록에서 **컨테이너 로그 보기** 를 선택 합니다. 이 항목에 대 한 자세한 내용은 [데이터를 분석 하기 위해 로그 검색](container-insights-log-search.md#search-logs-to-analyze-data)을 참조 하세요.

페이지 맨 위에 있는 **+ 필터 추가** 옵션을 사용 하 여 **서비스**, **노드**, **네임 스페이스**또는 **노드 풀**별로 뷰의 결과를 필터링 할 수 있습니다. 필터 범위를 선택한 후 **값 선택** 필드에 표시 되는 값 중 하나를 선택 합니다. 필터를 구성한 후에는 AKS 클러스터의 큐브 뷰를 보는 동안 전역적으로 적용 됩니다. 수식은 등호만 지원됩니다. 첫 번째 필터에 필터를 추가하여 결과를 더 좁힐 수 있습니다. 예를 들어 **노드**필터링을 지정 하는 경우 두 번째 필터에 대 한 **서비스** 또는 **네임 스페이스** 를 선택할 수 있습니다.

한 탭에서 필터를 지정 하는 것은 다른 탭을 선택 하는 경우에도 계속 적용 됩니다. 지정 된 필터 옆에 있는 **x** 기호를 선택 하면 삭제 됩니다.

**노드** 탭으로 전환 하면 행 계층 구조는 클러스터의 노드로 시작 하는 Kubernetes 개체 모델을 따릅니다. 노드에서 실행 중인 하나 이상의 pod를 보려면 노드를 확장 합니다. 하나 이상의 컨테이너가 pod로 그룹화 되 면 계층 구조에서 마지막 행으로 표시 됩니다. 호스트에 프로세서 또는 메모리가 중이 있는 경우 호스트에서 실행 중인 비 pod 관련 워크 로드 수를 확인할 수도 있습니다.

![성능 보기의 Kubernetes Node 계층 예](./media/container-insights-analyze/containers-nodes-view.png)

Windows Server 2019 OS를 실행 하는 windows Server 컨테이너는 목록의 모든 Linux 기반 노드 다음에 표시 됩니다. Windows Server 노드를 확장 하면 노드에서 실행 되는 하나 이상의 pod 및 컨테이너를 볼 수 있습니다. 노드를 선택 하면 속성 창에 버전 정보가 표시 됩니다.

![나열 된 Windows Server 노드가 포함 된 예제 노드 계층 구조](./media/container-insights-analyze/nodes-view-windows.png)

Linux OS를 실행 하는 Azure Container Instances 가상 노드는 목록의 마지막 AKS 클러스터 노드 다음에 표시 됩니다. Container Instances 가상 노드를 확장 하면 노드에서 실행 되는 하나 이상의 Container Instances pod 및 컨테이너를 볼 수 있습니다. 메트릭은 pod에 대해서만 수집 되 고 노드에 대해 보고 되지 않습니다.

![Container Instances가 나열된 노드 계층 구조 예제](./media/container-insights-analyze/nodes-view-aci.png)

확장 된 노드에서 컨트롤러에 대 한 노드에서 실행 되는 pod 또는 컨테이너를 드릴 다운 하 여 해당 컨트롤러에 대해 필터링 된 성능 데이터를 볼 수 있습니다. 특정 노드에 대 한 **컨트롤러** 열 아래에서 값을 선택 합니다.

![성능 보기에서 보여 주는 노드에서 컨트롤러로의 드릴다운 예제](./media/container-insights-analyze/drill-down-node-controller.png)

페이지 맨 위에 있는 컨트롤러 또는 컨테이너를 선택 하 여 해당 개체의 상태 및 리소스 사용률을 검토 합니다. 메모리 사용률을 검토 하려면 **메트릭** 드롭다운 목록에서 **메모리 RSS** 또는 **메모리 작업 집합**을 선택 합니다. **메모리 RSS**는 Kubernetes 버전 1.8 이상에서만 지원됩니다. 그렇지 않으면 undefined 또는 unrepresentable 값을 나타내는 숫자 데이터 형식 값인 *NaN &nbsp; % * **값을 볼 &nbsp; % ** 수 있습니다.

![컨테이너 노드 성능 보기](./media/container-insights-analyze/containers-node-metric-dropdown.png)

**메모리 작업 집합** 에는 포함 된 메모리 및 가상 메모리 (캐시)가 모두 표시 되 고 응용 프로그램에서 사용 하는 총 메모리와 총 메모리가 표시 됩니다. **메모리 RSS** 는 주 메모리만 표시 합니다. 즉,이 경우에는 상주 하는 메모리는 없습니다. 이 메트릭은 사용 가능한 메모리의 실제 용량을 보여 줍니다. 상주 메모리와 가상 메모리의 차이점은 무엇 인가요?

- 상주 하는 메모리 또는 주 메모리는 클러스터의 노드에 사용할 수 있는 실제 컴퓨터 메모리 양입니다.

- 가상 메모리는 메모리 부족 시 운영 체제에서 메모리의 데이터를 디스크로 교환 하는 데 사용 되는 예약 된 하드 디스크 공간 (캐시) 이며 필요한 경우 메모리에 다시 인출 됩니다.

기본적으로 성능 데이터는 지난 6 시간을 기반으로 하지만 왼쪽 위에서 **TimeRange** 옵션을 사용 하 여 창을 변경할 수 있습니다. 백분위 수 선택기에서 **Min**, **Avg**, **50**, **90**, **95**, **Max** 를 선택 하 여 시간 범위 내에서 결과를 필터링 할 수도 있습니다.

![데이터 필터링에 대한 백분위 선택 영역](./media/container-insights-analyze/containers-metric-percentile-filter.png)

**추세** 열 아래의 막대 그래프를 마우스로 가리키면 각 막대에는 선택한 메트릭에 따라 CPU 또는 메모리 사용이 15 분 이내에 표시 됩니다. 키보드를 통해 추세 차트를 선택한 후에는 Alt + Page up 키 또는 Alt + Page down 키를 사용 하 여 각 막대를 개별적으로 순환할 수 있습니다. 막대를 마우스로 가리키면 동일한 세부 정보를 얻을 수 있습니다.

![추세 가로 막대형 차트 가리키기 예](./media/container-insights-analyze/containers-metric-trend-bar-01.png)

다음 예제에서 목록의 첫 번째 노드인 *aks-nodepool1-* **컨테이너** 의 값은 9입니다. 이 값은 배포 된 컨테이너의 총 수에 대 한 롤업입니다.

![컨테이너 당 컨테이너의 롤업 예제](./media/container-insights-analyze/containers-nodes-containerstotal.png)

이 정보는 클러스터의 노드 간에 컨테이너의 적절 한 균형을 유지 하는지 여부를 신속 하 게 식별 하는 데 도움이 됩니다.

**노드** 탭을 볼 때 표시 되는 정보는 다음 표에 설명 되어 있습니다.

| Column | 설명 |
|--------|-------------|
| 이름 | 호스트의 이름입니다. |
| 상태 | 노드 상태의 Kubernetes 보기입니다. |
| 최소 &nbsp; %, 평균 &nbsp; %, 50 &nbsp; %, 90 &nbsp; %, 95 &nbsp; %, 최대&nbsp;%  | 선택한 기간 동안 백분위에 기반한 평균 노드 백분율입니다. |
| 최소값, 평균, 50, 90, 95, 최대값 | 선택한 기간 동안 백분위 수를 기준으로 하는 평균 노드 값입니다. Average 값은 노드에 대해 설정 된 CPU/메모리 제한에서 측정 됩니다. Pod 및 컨테이너의 경우 호스트에서 보고 하는 평균 값입니다. |
| 컨테이너 | 컨테이너의 수입니다. |
| 작동 시간 | 노드가 시작되었거나 다시 부팅된 이후 경과된 시간을 나타냅니다. |
| Controller | 컨테이너 및 Pod용입니다. 컨트롤러가 있는 컨트롤러를 표시 합니다. 모든 Pod가 컨트롤러에 있는 것은 아니므로 **N/A**가 표시될 수 있습니다. |
| 추세 Min &nbsp; %, Avg &nbsp; %, 50 &nbsp; %, 90 &nbsp; %, 95 &nbsp; %, 최대값&nbsp;% | 컨트롤러의 평균 백분위 메트릭 비율을 나타내는 막대 그래프 추세입니다. |

**다른 프로세스**라는 노드를 확장 한 후에 작업을 확인할 수 있습니다. 이는 노드에서 실행 되 고 다음을 포함 하는 비 컨테이너 화 된 프로세스를 나타냅니다.

* 자체 관리형 또는 Kubernetes 관리형 컨테이너화되지 않은 프로세스

* 컨테이너 런타임 프로세스

* kubelet

* 노드에서 실행되는 시스템 프로세스

* 노드 하드웨어 또는 VM에서 실행되는 기타 비 Kubernetes 워크로드

*Total usage from CAdvisor*  -  *컨테이너 화 된 프로세스의*cadvisor 사용에 대 한 총 사용량을 기준으로 계산 됩니다.

선택기에서 **컨트롤러**를 선택합니다.

![컨트롤러 뷰 선택](./media/container-insights-analyze/containers-controllers-tab.png)

여기서 컨트롤러의 성능 상태를 확인 하 고 컨트롤러에 연결 되지 않은 가상 노드 pod 가상 노드를 Container Instances 수 있습니다.

![\<이름> 컨트롤러 성능 보기](./media/container-insights-analyze/containers-controllers-view.png)

행 계층은 컨트롤러에서 시작 합니다. 컨트롤러를 확장할 때 하나 이상의 pod를 볼 수 있습니다. Pod를 확장하면 마지막 행에 Pod로 그룹화된 컨테이너가 표시됩니다. 확장 된 컨트롤러에서 실행 중인 노드로 드릴 다운 하 여 해당 노드에 대해 필터링 된 성능 데이터를 볼 수 있습니다. 컨트롤러에 연결 되지 않은 Container Instances pod는 목록에서 마지막에 나열 됩니다.

![Container Instances Pod가 나열된 컨트롤러 계층 구조 예제](./media/container-insights-analyze/controllers-view-aci.png)

특정 컨트롤러에 대 한 **노드** 열 아래에서 값을 선택 합니다.

![성능 보기에서 보여 주는 노드에서 컨트롤러로의 드릴다운 예제](./media/container-insights-analyze/drill-down-controller-node.png)

컨트롤러를 볼 때 표시 되는 정보는 다음 표에 설명 되어 있습니다.

| Column | 설명 |
|--------|-------------|
| 이름 | 컨트롤러의 이름입니다.|
| 상태 | 컨테이너의 실행이 완료 된 후 *확인*, *종료*, *실패*, *중지*됨 또는 *일시 중지*됨과 같은 상태를 사용 하 여 컨테이너의 롤업 상태입니다. 컨테이너가 실행 되 고 있지만 에이전트가 제대로 표시 되지 않았거나 에이전트가 선택 하지 않았고 30 분 넘게 응답 하지 않은 경우 상태를 *알 수 없음*으로 표시 합니다. 상태 아이콘의 추가 세부 정보는 다음 표에 나와 있습니다.|
| 최소 &nbsp; %, 평균 &nbsp; %, 50 &nbsp; %, 90 &nbsp; %, 95 &nbsp; %, 최대&nbsp;%| 선택한 메트릭 및 백분위에 대한 각 엔터티 평균 백분율의 평균 롤업입니다. |
| 최소값, 평균, 50, 90, 95, 최대값  | 선택된 백분위에 대한 컨테이너의 평균 CPU 밀리코어 또는 메모리 성능의 롤업입니다. 평균 값은 Pod에 대해 설정된 CPU/메모리 제한에서 측정됩니다. |
| 컨테이너 | 컨트롤러 또는 Pod에 대한 컨테이너의 총 수입니다. |
| Restarts | 컨테이너에서 다시 시작한 횟수의 롤업입니다. |
| 작동 시간 | 컨테이너가 시작된 이후 경과된 시간을 나타냅니다. |
| 노드 | 컨테이너 및 Pod용입니다. 컨트롤러가 있는 컨트롤러를 표시 합니다. |
| 추세 Min &nbsp; %, Avg &nbsp; %, 50 &nbsp; %, 90 &nbsp; %, 95 &nbsp; %, 최대값&nbsp;% | 컨트롤러의 평균 백분위 메트릭을 나타내는 막대 그래프 추세입니다. |

상태 필드의 아이콘은 컨테이너의 온라인 상태를 표시 합니다.

| 아이콘 | 상태 |
|--------|-------------|
| ![실행이 준비됨 상태 아이콘](./media/container-insights-analyze/containers-ready-icon.png) | 실행 중(준비됨)|
| ![대기 또는 일시 중지 상태 아이콘](./media/container-insights-analyze/containers-waiting-icon.png) | 대기 중 또는 일시 중지됨|
| ![마지막으로 보고된 실행 중 상태 아이콘](./media/container-insights-analyze/containers-grey-icon.png) | 마지막으로 보고 되었지만 30 분 넘게 응답 하지 않았습니다.|
| ![성공 상태 아이콘](./media/container-insights-analyze/containers-green-icon.png) | 성공적으로 중지됨 또는 중지하지 못함|

상태 아이콘은 Pod에서 제공하는 것에 따라 개수를 보여줍니다. 두 가지 나쁜 상태를 보여주고 있으며, 상태를 마우스로 가리키면 컨테이너에 있는 모든 Pod의 롤업 상태가 표시됩니다. 준비 상태가 아니면 상태 값은 **(0)** 을 표시합니다.

선택기에서 **컨테이너**를 선택합니다.

![컨테이너 보기 선택](./media/container-insights-analyze/containers-containers-tab.png)

여기서 Azure Kubernetes 및 Azure Container Instances 컨테이너의 성능 상태를 볼 수 있습니다.

![\<이름> 컨테이너 성능 보기](./media/container-insights-analyze/containers-containers-view.png)

컨테이너에서 Pod 또는 노드로 드릴다운하여 해당 개체에 대해 필터링된 성능 데이터를 볼 수 있습니다. 특정 컨테이너의 **Pod** 또는 **Node** 열 아래에서 값을 선택 합니다.

![성능 보기의 노드를 컨테이너에서 드릴 다운 하는 예](./media/container-insights-analyze/drill-down-controller-node.png)

컨테이너를 볼 때 표시 되는 정보는 다음 표에 설명 되어 있습니다.

| Column | 설명 |
|--------|-------------|
| 이름 | 컨트롤러의 이름입니다.|
| 상태 | 컨테이너의 상태입니다(있는 경우). 상태 아이콘에 대한 자세한 내용은 아래 표에 나와 있습니다.|
| 최소 &nbsp; %, 평균 &nbsp; %, 50 &nbsp; %, 90 &nbsp; %, 95 &nbsp; %, 최대&nbsp;% | 선택한 메트릭 및 백분위에 대한 각 엔터티 평균 백분율의 롤업입니다. |
| 최소값, 평균, 50, 90, 95, 최대값 | 선택된 백분위에 대한 컨테이너의 평균 CPU 밀리코어 또는 메모리 성능의 롤업입니다. 평균 값은 Pod에 대해 설정된 CPU/메모리 제한에서 측정됩니다. |
| Pod | Pod가 위치한 컨테이너입니다.|
| 노드 |  컨테이너가 있는 노드입니다. |
| Restarts | 컨테이너가 시작된 이후 경과된 시간을 나타냅니다. |
| 작동 시간 | 컨테이너가 시작 또는 다시 부팅된 이후 경과된 시간을 나타냅니다. |
| 추세 Min &nbsp; %, Avg &nbsp; %, 50 &nbsp; %, 90 &nbsp; %, 95 &nbsp; %, 최대값&nbsp;% | 컨테이너의 평균 백분위 메트릭 비율을 나타내는 막대 그래프 추세입니다. |

상태 필드의 아이콘은 다음 표에 설명 된 대로 pod의 온라인 상태를 표시 합니다.

| 아이콘 | 상태 |
|--------|-------------|
| ![실행이 준비됨 상태 아이콘](./media/container-insights-analyze/containers-ready-icon.png) | 실행 중(준비됨)|
| ![대기 또는 일시 중지 상태 아이콘](./media/container-insights-analyze/containers-waiting-icon.png) | 대기 중 또는 일시 중지됨|
| ![마지막으로 보고된 실행 중 상태 아이콘](./media/container-insights-analyze/containers-grey-icon.png) | 하지만 마지막으로 보고된 실행 중 상태는 30분 넘게 응답되지 않았습니다.|
| ![종료됨 상태 아이콘](./media/container-insights-analyze/containers-terminated-icon.png) | 성공적으로 중지됨 또는 중지하지 못함|
| ![실패 상태 아이콘](./media/container-insights-analyze/containers-failed-icon.png) | 실패 상태 |

## <a name="workbooks"></a>통합 문서

통합 문서는 텍스트, [로그 쿼리](../log-query/query-language.md), [메트릭](../platform/data-platform-metrics.md)및 매개 변수를 풍부한 대화형 보고서로 결합 합니다. 통합 문서는 동일한 Azure 리소스에 대한 액세스 권한이 있는 다른 팀 멤버에 의해 편집될 수 있습니다.

컨테이너에 대 한 Azure Monitor에는 시작 하기 위한 4 개의 통합 문서가 포함 되어 있습니다.

- **디스크 용량**: 다음과 같은 관점에서 컨테이너 내의 노드에 표시 되는 각 디스크에 대 한 대화형 디스크 사용 차트를 표시 합니다.

    - 모든 디스크의 디스크 사용 백분율입니다.
    - 모든 디스크의 사용 가능한 디스크 공간
    - 각 노드의 디스크, 사용 된 공간의 백분율, 사용 가능한 디스크 공간 (GiB) 및 사용 가능한 디스크 공간 (GiB)의 추세를 보여 주는 표 테이블에서 행을 선택 하면 사용 된 공간 및 사용 가능한 디스크 공간 (GiB)의 백분율이 행 아래에 표시 됩니다.

- **디스크 IO**: 다음 관점에서 컨테이너 내의 노드에 표시 되는 각 디스크에 대 한 대화형 디스크 사용률 차트를 표시 합니다.

    - 모든 디스크에서 read bytes/sec, write bytes/sec, read 및 write bytes/sec 추세를 기준으로 전체 디스크 i/o를 요약 합니다.
    - 8 개의 성능 차트는 디스크 i/o 병목 상태를 측정 하 고 식별 하는 데 도움이 되는 핵심 성과 지표를 표시 합니다.

- **Kubelet**: 키 노드 운영 통계를 표시 하는 두 개의 그리드를 포함 합니다.

    - 노드당 개요 그리드는 각 노드에 대 한 백분율 및 추세 별로 총 작업, 총 오류 및 성공한 작업을 요약 합니다.
    - 작업 유형별 개요 유형별 합계 작업, 총 오류, 성공 작업 (백분율 및 추세)을 요약 합니다.

- **네트워크**: 각 노드의 네트워크 어댑터에 대 한 대화형 네트워크 사용률 차트를 표시 하 고 그리드는 네트워크 어댑터의 성능을 측정 하는 데 도움이 되는 핵심 성과 지표를 제공 합니다.

**통합 문서 보기** 드롭다운 목록에서 각 통합 문서를 선택 하 여 액세스 합니다.

![통합 문서 보기 드롭다운 목록](./media/container-insights-analyze/view-workbooks-dropdown-list.png)

## <a name="next-steps"></a>다음 단계

- [컨테이너에 대 한 Azure Monitor를 사용 하 여 성능 경고 만들기](container-insights-alerts.md) 를 검토 하 여 devops 또는 운영 프로세스 및 절차를 지원 하기 위해 높은 CPU 및 메모리 사용률에 대 한 경고를 만드는 방법을 알아보세요.

- 클러스터를 경고, 시각화 또는 분석하기 위해 평가하거나 사용자 지정하는 미리 정의된 쿼리 및 예제를 보려면 [로그 쿼리 예제](container-insights-log-search.md#search-logs-to-analyze-data)를 확인하세요.

- [모니터 클러스터 상태](container-insights-health.md) 보기 Kubernetes 클러스터의 상태를 보는 방법에 대해 알아봅니다.
