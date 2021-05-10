---
title: 컨테이너 인사이트로 PV 모니터링 구성 | Microsoft Docs
description: 이 문서에서는 컨테이너 인사이트를 사용하여 영구 볼륨으로 모니터링 Kubernetes 클러스터를 구성하는 방법을 설명합니다.
ms.topic: conceptual
ms.date: 03/03/2021
ms.openlocfilehash: 7c6ddd62bf06d313987289e444962378cea43fc8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105627900"
---
# <a name="configure-pv-monitoring-with-container-insights"></a>컨테이너 인사이트로 PV 모니터링 구성

에이전트 버전 *ciprod10052020* 부터 이제 컨테이너 통합 에이전트용 Azure Monitor에서 PV(영구적 볼륨) 사용량 모니터링을 지원합니다. 에이전트 버전 *ciprod01112021* 을 사용하는 경우 에이전트에서 상태, 스토리지 클래스, 유형, 액세스 모드 및 기타 세부 정보에 대한 정보 등 PV 인벤토리 모니터링을 지원합니다.
## <a name="pv-metrics"></a>PV 메트릭

컨테이너 인사이트는 60초 간격으로 다음 메트릭을 수집하고 **InsightMetrics** 테이블에 저장하여 PV 사용량 모니터링을 자동으로 시작합니다.

| 메트릭 이름 | 메트릭 차원(태그) | 메트릭 설명 |
|-----|-----------|----------|
| `pvUsedBytes`| podUID, podName, pvcName, pvcNamespace, capacityBytes, clusterId, clusterName| 특정 Pod에서 사용하는 클레임이 있는 특정 영구 볼륨에 사용된 공간(바이트)입니다. `capacityBytes`은(는) 데이터 수집 비용을 줄이고 쿼리를 간소화하기 위해 태그 필드에 차원으로 포함됩니다.|

[여기](./container-insights-agent-config.md)에서 수집된 PV 메트릭을 구성하는 방법에 대해 자세히 알아보세요.

## <a name="pv-inventory"></a>PV 인벤토리

컨테이너용 Azure Monitor는 60초 간격으로 다음 정보를 수집하고 **KubePVInventory** 테이블에 저장하여 PV 모니터링을 자동으로 시작합니다.

|데이터 |데이터 원본| 데이터 형식| 필드|
|-----|-----------|----------|-------|
|Kubernetes 클러스터의 영구 볼륨 인벤토리 |Kube API |`KubePVInventory` |    PVName, PVCapacityBytes, PVCName, PVCNamespace, PVStatus, PVAccessModes, PVType, PVTypeInfo, PVStorageClassName, PVCreationTimestamp, TimeGenerated, ClusterId, ClusterName, _ResourceId |

## <a name="monitor-persistent-volumes"></a>영구 볼륨 모니터링

컨테이너용 Azure Monitor에는 이 사용 현황 메트릭용으로 미리 구성된 차트와 모든 클러스터에 대한 통합 문서 템플릿의 인벤토리 정보가 포함됩니다. 또한 PV 사용량에 권장되는 경고를 사용하도록 설정하고 Log Analytics에서 이러한 메트릭을 쿼리할 수 있습니다.  

### <a name="workload-details-workbook"></a>워크로드 세부 정보 통합 문서

왼쪽 창에서 또는 인사이트 창의 **통합 문서 보기** 드롭다운 목록에서 또는 인사이트 창의 **보고서(미리 보기) 탭** 에서 통합 문서를 선택하여 AKS에서 직접 **워크로드 세부 정보** 통합 문서의 영구 볼륨 탭에 있는 특정 워크로드의 사용량 차트를 찾을 수 있습니다.


:::image type="content" source="./media/container-insights-persistent-volumes/pv-workload-example.PNG" alt-text="Azure Monitor PV 워크로드 통합 문서 예제":::

### <a name="persistent-volume-details-workbook"></a>영구 볼륨 세부 정보 통합 문서

왼쪽 창에서 또는 인사이트 창의 **통합 문서 보기** 드롭다운 목록에서 또는 인사이트 창의 **보고서(미리 보기)** 탭에서 통합 문서를 선택하여 AKS 클러스터에서 직접 **영구 볼륨 세부 정보** 통합 문서에 있는 영구 볼륨 인벤토리의 개요를 찾을 수 있습니다.


:::image type="content" source="./media/container-insights-persistent-volumes/pv-details-workbook-example.PNG" alt-text="Azure Monitor PV 세부 정보 통합 문서 예제":::

### <a name="persistent-volume-usage-recommended-alert"></a>영구 볼륨 사용량 권장 경고
Pod의 평균 PV 사용량이 80%를 초과할 때 경고하도록 권장 경고를 사용하도록 설정할 수 있습니다. 경고에 대한 자세한 내용은 [여기](./container-insights-metric-alerts.md)를, 기본 임계값을 재정의하는 방법에 대한 자세한 내용은 [여기](./container-insights-metric-alerts.md#configure-alertable-metrics-in-configmaps)를 참조하세요.
## <a name="next-steps"></a>다음 단계

- 수집된 PV 메트릭에 대한 자세한 내용은 [여기](./container-insights-agent-config.md)를 참조하세요.