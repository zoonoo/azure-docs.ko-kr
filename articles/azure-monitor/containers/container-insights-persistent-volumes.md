---
title: Container insights를 사용 하 여 PV 모니터링 구성 | Microsoft Docs
description: 이 문서에서는 컨테이너 insights를 사용 하 여 영구 볼륨으로 모니터링 Kubernetes 클러스터를 구성 하는 방법을 설명 합니다.
ms.topic: conceptual
ms.date: 03/03/2021
ms.openlocfilehash: 16c87177a8911a7b88284606f54f7bf6e0e0daa3
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105026107"
---
# <a name="configure-pv-monitoring-with-container-insights"></a>컨테이너 insights를 사용 하 여 PV 모니터링 구성

에이전트 버전 *ciprod10052020* 부터 컨테이너에 대 한 Azure Monitor 통합 에이전트가 이제 PV (영구적 볼륨) 사용량 모니터링을 지원 합니다. 에이전트 버전 *ciprod01112021* 을 사용 하는 경우 에이전트는 상태, 저장소 클래스, 유형, 액세스 모드 및 기타 세부 정보를 포함 하 여 PV inventory 모니터링을 지원 합니다.
## <a name="pv-metrics"></a>PV 메트릭

컨테이너 insights는 60-초 간격으로 다음 메트릭을 수집 하 고 **InsightMetrics** 테이블에 저장 하 여 PV 사용 모니터링을 자동으로 시작 합니다.

| 메트릭 이름 | 메트릭 차원 (태그) | 메트릭 설명 | | `pvUsedBytes`| poduid, podName, pvcName, Pvcname, capacityBytes, clusterId, clusterName | 특정 pod에서 사용 하는 클레임을 가진 특정 영구적 볼륨에 대해 사용 된 공간 (바이트)입니다. `capacityBytes` 는 데이터 수집 비용을 줄이고 쿼리를 간소화 하기 위해 태그 필드에 차원으로 중첩 됩니다. |

[여기](./container-insights-agent-config.md)에서 수집 된 PV 메트릭을 구성 하는 방법에 대해 자세히 알아보세요.

## <a name="pv-inventory"></a>PV 인벤토리

컨테이너 Azure Monitor는 60-초 간격으로 다음 정보를 수집 하 고 **KubePVInventory** 테이블에 저장 하 여 pvs 모니터링을 자동으로 시작 합니다.

|데이터 |데이터 원본| 데이터 형식| 필드|
|-----|-----------|----------|-------|
|Kubernetes 클러스터의 영구적 볼륨 인벤토리 |Kube API |`KubePVInventory` | PVName, PVCapacityBytes, PVCName, Pvcname, PVStatus, PVAccessModes, PVType, PVTypeInfo, PVStorageClassName, PVCreationTimestamp, TimeGenerated, ClusterId, ClusterName, _ResourceId |

## <a name="monitor-persistent-volumes"></a>영구 볼륨 모니터링

컨테이너에 대 한 Azure Monitor에는이 사용 메트릭에 대 한 미리 구성 된 차트 및 모든 클러스터에 대 한 통합 문서 템플릿의 인벤토리 정보가 포함 됩니다. 또한 PV 사용에 대해 권장 되는 경고를 사용 하도록 설정 하 고 Log Analytics에서 이러한 메트릭을 쿼리할 수 있습니다.  

### <a name="workload-details-workbook"></a>워크 로드 정보 통합 문서

왼쪽 창에서 통합 문서를 선택 하 고, 정보 창의 **통합 문서 보기** 드롭다운 목록에서 또는 Insights 창의 **보고서 (미리 보기) 탭** 에서 AKS 클러스터에서 직접 **특정 작업에** 대 한 사용 현황 차트를 찾을 수 있습니다.


:::image type="content" source="./media/container-insights-persistent-volumes/pv-workload-example.PNG" alt-text="Azure Monitor PV 워크 로드 통합 문서 예제":::

### <a name="persistent-volume-details-workbook"></a>영구적 볼륨 정보 통합 문서

왼쪽 창에서 통합 문서를 선택 하 고, Insights 창의 **통합 문서 보기** 드롭다운 목록에서 또는 Insights 창의 **보고서 (미리 보기)** 탭에서 AKS 클러스터에서 **직접 영구적 볼륨** 인벤토리에 대 한 개요를 찾을 수 있습니다.


:::image type="content" source="./media/container-insights-persistent-volumes/pv-details-workbook-example.PNG" alt-text="Azure Monitor PV details 통합 문서 예제":::

### <a name="persistent-volume-usage-recommended-alert"></a>영구적 볼륨 사용 권장 경고
Pod의 평균 PV 사용량이 80% 이상인 경우 경고를 표시 하는 권장 되는 경고를 사용 하도록 설정할 수 있습니다. [여기에서 경고에](./container-insights-metric-alerts.md) 대 한 자세한 내용과 [여기](./container-insights-metric-alerts.md#configure-alertable-metrics-in-configmaps)에서 기본 임계값을 재정의 하는 방법을 알아보세요.
## <a name="next-steps"></a>다음 단계

- 수집 된 PV 메트릭에 대 한 자세한 내용은 [여기](./container-insights-agent-config.md)를 참조 하세요.