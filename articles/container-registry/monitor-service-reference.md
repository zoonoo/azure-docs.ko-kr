---
title: Azure Container Registry 데이터 참조 모니터링
description: Azure Container Registry를 모니터링할 때 필요한 중요한 참조 자료입니다. 메트릭, 리소스 로그 및 로그 스키마에 대한 세부 정보를 제공합니다.
author: dlepow
ms.author: danlep
ms.topic: reference
ms.custom: subject-monitoring
ms.service: container-registry
ms.date: 03/19/2021
ms.openlocfilehash: e22f349074ebd232903e8119ee5d90660fa0f13b
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110474965"
---
# <a name="monitoring-azure-container-registry-data-reference"></a>Azure Container Registry 데이터 참조 모니터링

Azure Container Registry에 대한 모니터링 데이터 수집 및 분석에 대한 자세한 내용은 [Azure Container Registry 모니터링](monitor-service.md)을 참조하세요.

## <a name="metrics"></a>메트릭

### <a name="container-registry-metrics"></a>컨테이너 레지스트리 메트릭

리소스 공급자 및 유형: [Microsoft.ContainerRegistry/registries](/azure/azure-monitor/platform/metrics-supported#microsoftcontainerregistryregistries)

| 메트릭 | 진단 설정을 통해 내보내기 가능? | 단위 | 집계 형식 | Description | 차원  |  
|:-------|:-----|:-----|:------------|:------------------|:----- |
|     AgentPoolCPUTime   | 예 |   초   | 합계 |   전용 [에이전트 풀](tasks-agent-pools.md)에서 실행되는 [ACR 작업](container-registry-tasks-overview.md)에 사용된 CPU 시간             | 없음 | 
|     RunDuration   | 예 |  밀리초   |  합계 |  [ACR 작업](container-registry-tasks-overview.md) 실행 기간       | 없음 | 
|     StorageUsed   |  아니요 | 바이트   |   평균 | 컨테이너 레지스트리에서 사용되는 스토리지<br/><br/>모든 스토리지의 고유 및 공유 레이어, 매니페스트 파일 및 복제본 <sup>1</sup>에 대한 스토리지 합계            | 지리적 위치 | 
|     SuccessfulPullCount | 예  |   개수   | 합계 | 레지스트리에서 컨테이너 이미지와 기타 아티팩트를 성공적으로 풀              | 없음 | 
|     SuccessfulPushCount   | 예 |   개수   | 합계 | 컨테이너 이미지 및 기타 아티팩트를 레지스트리에 성공적으로 푸시          | 없음 | 
|     TotalPullCount   |   예 | 개수   |     합계 |  레지스트리에서 컨테이너 이미지 및 기타 아티팩트의 전체 풀         | 없음 | 
|     TotalPushCount   | 예 |  개수   |   합계 |  레지스트리에 대한 컨테이너 이미지 및 기타 아티팩트의 전체 푸시           | 없음 | 

<sup>1</sup> 레이어 공유로 인해 사용되는 레지스트리 스토리지는 개별 리포지토리의 스토리지 합계보다 적을 수 있습니다. 리포지토리 또는 태그를 [삭제](container-registry-delete.md)하면 매니페스트 파일과 참조된 고유 레이어에서 사용하는 스토리지만 복구됩니다.

보다 자세한 정보는 [Azure Monitor에서 지원되는 모든 플랫폼 메트릭](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported)을 참조하세요.

## <a name="metric-dimensions"></a>메트릭 차원

메트릭 차원에 대한 자세한 내용은 [다차원 메트릭](/azure/azure-monitor/platform/data-platform-metrics#multi-dimensional-metrics)을 참조하세요.

Azure Container Registry에는 해당 메트릭과 관련된 다음과 같은 차원이 있습니다.

| 차원 이름 | Description |
| ------------------- | ----------------- |
| **지리적 위치** | 레지스트리 또는 [geo-replica](container-registry-geo-replication.md)의 Azure 지역입니다. |


## <a name="resource-logs"></a>리소스 로그

이 섹션에는 Azure Container Registry에 대해 수집할 수 있는 리소스 로그 유형이 나열되어 있습니다. 

[Azure Monitor에서 지원되는 모든 리소스 로그 범주 유형](/azure/azure-monitor/platform/resource-logs-schema) 목록을 참조하세요.

### <a name="container-registries"></a>컨테이너 레지스트리

리소스 공급자 및 유형: [Microsoft.ContainerRegistry/registries](/azure/azure-monitor/essentials/resource-logs-categories#microsoftcontainerregistryregistries)

| 범주 | 표시 이름 | 세부 정보  |
|:---------|:-------------|------------------|
| ContainerRegistryLoginEvents  | 로그인 이벤트 | 들어오는 ID와 IP 주소를 포함하는 레지스트리 인증 이벤트 및 상태 |
| ContainerRegistryRepositoryEvents | 리포지토리 이벤트           | 레지스트리 리포지토리의 이미지 및 기타 아티팩트 작업<br/><br/> 푸시, 풀, 태그 해제, 삭제(리포지토리 삭제 포함), 태그 제거 및 매니페스트 제거 <sup>1</sup> 작업이 기록됩니다. |

<sup>1</sup> 삭제 이벤트는 레지스트리 [보존 정책](container-registry-retention-policy.md)이 구성된 경우에만 기록됩니다.

## <a name="azure-monitor-logs-tables"></a>Azure Monitor Logs 테이블

이 섹션에서는 Azure Container Registry와 관련된 모든 Azure Monitor 로그 Kusto 테이블을 참조하고 로그 분석 쿼리를 사용할 수 있습니다. 

### <a name="container-registry"></a>Container Registry

| 테이블 |  설명 | 
|:---------|:-------------|
| [AzureActivity](/azure/azure-monitor/reference/tables/azureactivity)   | Azure에서 발생한 구독 수준 또는 관리 그룹 수준 이벤트에 대한 인사이트를 제공하는 Azure 활동 로그의 항목입니다. | 
| [AzureMetrics](/azure/azure-monitor/reference/tables/azuremetrics) | 상태 및 성능을 측정하는 Azure 서비스에서 내보내는 메트릭 데이터입니다.    |  
|  [ContainerRegistryLoginEvents](/azure/azure-monitor/reference/tables/containerregistryloginevents)               | Azure Container Registry 로그인 감사 로그                             |                                                     
|  [ContainerRegistryRepositoryEvents](/azure/azure-monitor/reference/tables/containerregistryrepositoryevents)               | Azure Container Registry 리포지토리 감사 로그                         |                                                    

모든 Azure Monitor Logs/Log Analytics 테이블에 대한 참조는 [Azure Monitor 로그 테이블 참조](/azure/azure-monitor/reference/tables/tables-resourcetype)를 확인하세요.

## <a name="activity-log"></a>활동 로그

다음 표에는 [활동 로그](/azure/azure-monitor/platform/activity-log)에 만들 수 있는 Azure Container Registry와 관련된 작업이 나열되어 있습니다. 이 목록은 전체 목록이 아닙니다.

| 작업(Operation) | Description |
|:---|:---|
| 컨테이너 레지스트리 만들기 또는 업데이트 | 컨테이너 레지스트리 만들기 또는 레지스트리 속성 업데이트 |
| 컨테이너 레지스트리 삭제 | 컨테이너 레지스트리 삭제 |
| 컨테이너 레지스트리 로그인 자격 증명 나열 | 레지스트리의 관리자 계정에 대한 자격 증명 표시 |
| 이미지 가져오기 | 레지스트리에 이미지 또는 기타 아티팩트 가져오기 |
| 역할 할당 만들기 | 리소스에 액세스하기 위한 RBAC 역할을 ID에 할당  |


## <a name="schemas"></a>스키마

Azure Container Registry 리소스 로그에서 사용 중인 스키마는 다음과 같습니다.

| 스키마 | Description |
|:--- |:---|
| [ContainerRegistryLoginEvents](/azure/azure-monitor/reference/tables/ContainerRegistryLoginEvents)  | 들어오는 ID와 IP 주소를 포함하여 레지스트리 인증 이벤트와 상태에 대한 스키마 |
| [ContainerRegistryRepositoryEvents](/azure/azure-monitor/reference/tables/ContainerRegistryRepositoryEvents) | 레지스트리 리포지토리의 이미지 및 기타 아티팩트 작업에 대한 스키마 |
## <a name="next-steps"></a>다음 단계

- Azure Container Registry 모니터링에 대한 설명은 [Azure Container Registry 모니터링](monitor-service.md)을 참조하세요.
- Azure 리소스 모니터링에 대한 자세한 내용은 [Azure Monitor를 사용하여 Azure 리소스 모니터링](/azure/azure-monitor/insights/monitor-azure-resources)을 참조하세요.
