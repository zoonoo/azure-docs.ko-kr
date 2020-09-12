---
title: 컨테이너에 대 한 Azure Monitor의 메트릭 경고 | Microsoft Docs
description: 이 문서에서는 공개 미리 보기로 제공 되는 컨테이너에 대 한 Azure Monitor에서 사용할 수 있는 권장 메트릭 경고를 검토 합니다.
ms.topic: conceptual
ms.date: 08/04/2020
ms.openlocfilehash: aace260ff22d63211424f2ce4a7319bf577436f4
ms.sourcegitcommit: 43558caf1f3917f0c535ae0bf7ce7fe4723391f9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/11/2020
ms.locfileid: "90019889"
---
# <a name="recommended-metric-alerts-preview-from-azure-monitor-for-containers"></a>컨테이너에 대 한 Azure Monitor의 권장 메트릭 경고 (미리 보기)

최대 수요를 발생 시킬 때 시스템 리소스 문제에 대해 경고 하 고, 컨테이너에 대 한 Azure Monitor를 사용 하 여 Azure Monitor 로그에 저장 된 성능 데이터를 기반으로 로그 경고를 만들 수 있습니다. 컨테이너에 대 한 Azure Monitor에는 이제 공개 미리 보기로 제공 되는 AKS 클러스터에 대 한 미리 구성 된 메트릭 경고 규칙이 포함 되어 있습니다.

이 문서에서는 이러한 경고 규칙을 구성 하 고 관리 하는 방법에 대 한 지침을 제공 하며 환경을 검토 합니다.

Azure Monitor 경고에 익숙하지 않은 경우 시작 하기 전에 [Microsoft Azure의 경고 개요](../platform/alerts-overview.md) 를 참조 하세요. 메트릭 경고에 대해 자세히 알아보려면 [Azure Monitor에서 메트릭 경고](../platform/alerts-metric-overview.md)를 참조 하세요.

## <a name="prerequisites"></a>전제 조건

시작 하기 전에 다음을 확인 합니다.

* 사용자 지정 메트릭은 Azure 지역의 하위 집합 에서만 사용할 수 있습니다. 지원 되는 지역 목록은 [여기](../platform/metrics-custom-overview.md#supported-regions)에 설명 되어 있습니다.

* 메트릭 경고 및 추가 메트릭의 도입을 지원 하기 위해 필요한 최소 에이전트 버전은 **microsoft/oms: ciprod05262020**입니다.

    클러스터가 최신 버전의 에이전트를 실행 하 고 있는지 확인 하려면 다음 중 하나를 수행 합니다.

    * 명령을 실행 `kubectl describe <omsagent-pod-name> --namespace=kube-system` 합니다. 반환 된 상태에서 출력의 *컨테이너* 섹션에서 Omsagent의 **이미지** 아래에 있는 값을 확인 합니다. 
    * **노드** 탭에서 클러스터 노드를 선택 하 고 오른쪽의 **속성** 창에서 **에이전트 이미지 태그**아래의 값을 확인 합니다.

    표시 된 값은 **ciprod05262020**보다 이후 버전 이어야 합니다. 클러스터에 이전 버전이 있는 경우 [AKS 클러스터의 업그레이드 에이전트](container-insights-manage-agent.md#upgrade-agent-on-aks-cluster) 단계를 수행 하 여 최신 버전을 가져옵니다.
    
    에이전트 릴리스와 관련 된 자세한 내용은 [에이전트 릴리스 기록](https://github.com/microsoft/docker-provider/tree/ci_feature_prod)을 참조 하세요. 메트릭이 수집 되 고 있는지 확인 하려면 메트릭 탐색기 Azure Monitor 사용 하 여 **정보** 를 표시 하는 **메트릭 네임 스페이스** 에서 확인할 수 있습니다. 이 경우 계속 진행 하 여 경고 설정을 시작할 수 있습니다. 수집 된 메트릭이 표시 되지 않으면 클러스터 서비스 주체 또는 MSI에 필요한 사용 권한이 없는 것입니다. SPN 또는 MSI가 **모니터링 메트릭 게시자** 역할의 구성원 인지 확인 하려면 [Azure CLI를 사용 하 여 클러스터당 업그레이드](container-insights-update-metrics.md#upgrade-per-cluster-using-azure-cli) 섹션에 설명 된 단계를 수행 하 여 역할 할당을 확인 하 고 설정 합니다.

## <a name="alert-rules-overview"></a>경고 규칙 개요

중요 한 사항에 대해 경고 하기 위해 컨테이너에 대 한 Azure Monitor는 AKS 클러스터에 대해 다음과 같은 메트릭 경고를 포함 합니다.

|Name| 설명 |기본 임계값 |
|----|-------------|------------------|
|평균 컨테이너 CPU (%) |컨테이너 당 사용 되는 평균 CPU를 계산 합니다.|컨테이너 당 평균 CPU 사용량이 95% 보다 큰 경우| 
|평균 컨테이너 작업 집합 메모리% |컨테이너 당 사용 되는 평균 작업 집합 메모리를 계산 합니다.|컨테이너 당 평균 작업 집합 메모리 사용량이 95% 보다 큰 경우 |
|평균 CPU % |노드당 사용 되는 평균 CPU를 계산 합니다. |평균 노드 CPU 사용률이 80%를 초과 하는 경우 |
|평균 디스크 사용량 (%) |노드의 평균 디스크 사용량을 계산 합니다.|노드의 디스크 사용량이 80% 보다 큰 경우 |
|평균 작업 집합 메모리 (%) |노드의 평균 작업 집합 메모리를 계산 합니다. |노드의 평균 작업 집합 메모리가 80% 보다 큰 경우 |
|컨테이너 수 다시 시작 |컨테이너 다시 시작 수를 계산 합니다. | 컨테이너 다시 시작이 0 보다 큰 경우 |
|실패 한 Pod 개수 |실패 상태의 pod가 있는지 여부를 계산 합니다.|실패 상태에 있는 pod 수가 0 보다 큰 경우 |
|노드 NotReady 상태 |노드가 NotReady 상태 인지 여부를 계산 합니다.|NotReady 상태의 노드 수가 0 보다 큰 경우 |
|OOM 종료 컨테이너 |OOM 중지 된 컨테이너 수를 계산 합니다. |OOM이 많은 컨테이너 수가 0 보다 큰 경우 |
|Pod ready% |Pod의 평균 준비 상태를 계산 합니다. |Pod 준비 상태가 80% 미만인 경우|
|완료 된 작업 수 |6 시간 전에 완료 된 작업 수를 계산 합니다. |6 시간 보다 오래 된 부실 작업 수가 0 보다 큰 경우|

이러한 모든 경고 규칙에는 다음과 같은 공통 속성이 있습니다.

* 모든 경고 규칙은 메트릭 기반입니다.

* 모든 경고 규칙은 기본적으로 사용 하지 않도록 설정 됩니다.

* 모든 경고 규칙은 분당 한 번 평가 되며 지난 5 분 동안의 데이터를 다시 찾습니다.

* 경고 규칙에는 기본적으로 할당 된 작업 그룹이 없습니다. 경고 규칙을 편집 하는 동안 기존 작업 그룹을 선택 하거나 새 작업 그룹을 만들어 경고에 [작업 그룹](../platform/action-groups.md) 을 추가할 수 있습니다.

* 경고 규칙에 대 한 임계값은 직접 편집 하 여 수정할 수 있습니다. 그러나 임계값을 수정 하기 전에 각 경고 규칙에 제공 된 지침을 참조 하세요.

다음 경고 기반 메트릭은 다른 메트릭과 비교 하 여 고유한 동작 특성을 가집니다.

* *completedJobsCount* 메트릭은 6 시간 전에 완료 된 작업이 있는 경우에만 전송 됩니다.

* *containerRestartCount* 메트릭은 컨테이너가 다시 시작 될 때만 전송 됩니다.

* *oomKilledContainerCount* 메트릭은 OOM이 종료 된 컨테이너에 있는 경우에만 전송 됩니다.

* *cpuExceededPercentage*, *memoryRssExceededPercentage*및 *MemoryWorkingSetExceededPercentage* 메트릭은 CPU, 메모리 Rss 및 메모리 작업 집합 값이 구성 된 임계값을 초과할 때 전송 됩니다 (기본 임계값은 95%). 이러한 임계값은 해당 경고 규칙에 대해 지정 된 경고 조건 임계값을 제외 합니다. 즉, 이러한 메트릭을 수집 하 고 [메트릭 탐색기](../platform/metrics-getting-started.md)에서 분석 하려는 경우 경고 임계값 보다 낮은 값으로 임계값을 구성 하는 것이 좋습니다. 컨테이너 리소스 사용률 임계값에 대 한 컬렉션 설정과 관련 된 구성은 ConfigMaps 파일의 섹션 아래에서 재정의할 수 있습니다 `[alertable_metrics_configuration_settings.container_resource_utilization_thresholds]` . Configmaps 구성 파일 구성과 관련 된 자세한 내용은 [했어야 메트릭 구성 ConfigMaps](#configure-alertable-metrics-in-configmaps) 섹션을 참조 하세요.

## <a name="metrics-collected"></a>수집 된 메트릭

이 기능의 일부로 달리 지정 되지 않은 경우 다음과 같은 메트릭이 활성화 되 고 수집 됩니다.

|메트릭 네임스페이스 |메트릭 |설명 |
|---------|----|------------|
|정보. 컨테이너/노드 |cpuUsageMillicores |Millicores에서 호스트에의 한 CPU 사용률입니다.|
|정보. 컨테이너/노드 |cpuUsagePercentage |노드당 CPU 사용량 백분율입니다.|
|정보. 컨테이너/노드 |memoryRssBytes |호스트의 메모리 RSS 사용률 (바이트)입니다.|
|정보. 컨테이너/노드 |memoryRssPercentage |호스트의 메모리 RSS 사용 백분율입니다.|
|정보. 컨테이너/노드 |memoryWorkingSetBytes |호스트의 메모리 작업 집합 사용률 (바이트)입니다.|
|정보. 컨테이너/노드 |memoryWorkingSetPercentage |호스트의 메모리 작업 집합 사용 백분율입니다.|
|정보. 컨테이너/노드 |nodesCount |상태별 노드 수입니다.|
|정보. 컨테이너/노드 |diskUsedPercentage |장치에서 노드에 사용 된 디스크의 백분율입니다.|
|Pod/ |podCount |컨트롤러, 네임 스페이스, 노드 및 단계의 pod 수입니다.|
|Pod/ |completedJobsCount |완료 된 작업은 컨트롤러, Kubernetes 네임 스페이스에서 이전 사용자 구성 가능 임계값 (기본값은 6 시간)을 계산 합니다. |
|Pod/ |restartingContainerCount |컨트롤러, Kubernetes 네임 스페이스 별로 컨테이너를 다시 시작 하는 횟수입니다.|
|Pod/ |oomKilledContainerCount |Controller, Kubernetes 네임 스페이스 별로 중지 된 컨테이너 수입니다.|
|Pod/ |podReadyPercentage |Controller, Kubernetes 네임 스페이스에서 준비 상태에 있는 pod의 백분율입니다.|
|정보. 컨테이너/컨테이너 |cpuExceededPercentage |컨테이너 이름, 컨트롤러 이름, Kubernetes 네임 스페이스, pod 이름에 의해 사용자 구성 가능 임계값 (기본값은 95.0)을 초과 하는 컨테이너에 대 한 CPU 사용률입니다.<br> 시  |
|정보. 컨테이너/컨테이너 |memoryRssExceededPercentage |컨테이너 이름, 컨트롤러 이름, Kubernetes 네임 스페이스, pod 이름에 의해 사용자 구성 가능 임계값을 초과 하는 컨테이너에 대 한 메모리 RSS 비율 (기본값은 95.0)입니다.|
|정보. 컨테이너/컨테이너 |memoryWorkingSetExceededPercentage |컨테이너 이름, 컨트롤러 이름, Kubernetes 네임 스페이스, pod 이름에 의해 사용자 구성 가능 임계값 (기본값은 95.0)을 초과 하는 컨테이너에 대 한 메모리 작업 집합 비율입니다.|

## <a name="enable-alert-rules"></a>경고 규칙 사용

Azure Portal에서 Azure Monitor 메트릭 경고를 사용 하도록 설정 하려면 다음 단계를 수행 합니다. 리소스 관리자 템플릿을 사용 하도록 설정 하려면 [리소스 관리자 템플릿을](#enable-with-a-resource-manager-template)사용 하 여 사용을 참조 하세요.

### <a name="from-the-azure-portal"></a>Azure Portal에서

이 섹션에서는 Azure Portal에서 컨테이너 메트릭 경고 (미리 보기)에 대 한 Azure Monitor를 사용 하도록 설정 하는 과정을 안내 합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

2. 컨테이너 메트릭 경고 (미리 보기) 기능에 대 한 Azure Monitor에 대 한 액세스는 Azure Portal의 왼쪽 창에서 **정보** 를 선택 하 여 AKS 클러스터에서 직접 사용할 수 있습니다.

3. 명령 모음에서 **권장 되는 경고**를 선택 합니다.

    ![컨테이너에 대 한 Azure Monitor의 권장 경고 옵션](./media/container-insights-metric-alerts/command-bar-recommended-alerts.png)

4. **권장 되는 경고** 속성 창이 페이지의 오른쪽에 자동으로 표시 됩니다. 기본적으로 목록에 있는 모든 경고 규칙은 사용 하지 않도록 설정 됩니다. **사용**을 선택 하면 경고 규칙이 만들어지고 규칙 이름이로 업데이트 되어 경고 리소스에 대 한 링크가 포함 됩니다.

    ![권장 되는 경고 속성 창](./media/container-insights-metric-alerts/recommended-alerts-pane.png)

    **사용/사용 안 함** 을 선택 하 여 경고를 설정 하면 경고 규칙이 만들어지고 규칙 이름이 업데이트 되어 실제 경고 리소스에 대 한 링크가 포함 됩니다.

    ![경고 규칙 사용](./media/container-insights-metric-alerts/recommended-alerts-pane-enable.png)

5. 경고 규칙은 사용자에 게 경고가 트리거 되었음을 알리는 [작업 그룹과](../platform/action-groups.md) 연결 되지 않습니다. **작업 그룹 할당 안 함** 을 선택 하 고 **작업 그룹** 페이지에서 기존를 지정 하거나 **추가** 또는 **만들기**를 선택 하 여 작업 그룹을 만듭니다.

    ![작업 그룹 선택](./media/container-insights-metric-alerts/select-action-group.png)

### <a name="enable-with-a-resource-manager-template"></a>리소스 관리자 템플릿으로 사용

Azure Resource Manager 템플릿 및 매개 변수 파일을 사용 하 여 Azure Monitor에 포함 된 메트릭 경고를 만들 수 있습니다.

기본적인 단계는 다음과 같습니다.

1. [GitHub](https://github.com/microsoft/Docker-Provider/tree/ci_dev/alerts/recommended_alerts_ARM)에서 경고를 만드는 방법을 설명 하는 사용 가능한 템플릿 중 하나 또는 모두를 다운로드 합니다.

2. [매개 변수 파일](../../azure-resource-manager/templates/parameter-files.md) 을 만들고 JSON으로 사용 하 여 경고 규칙을 만드는 데 필요한 값을 설정 합니다.

3. Azure Portal, PowerShell 또는 Azure CLI에서 템플릿을 배포 합니다.

#### <a name="deploy-through-azure-portal"></a>Azure Portal를 통해 배포

1. 다음 명령을 사용 하 여 경고 규칙을 만들려면 Azure Resource Manager 템플릿 및 매개 변수 파일을 다운로드 하 여 로컬 폴더에 저장 합니다.

2. 포털을 통해 사용자 지정 된 템플릿을 배포 하려면 [Azure Portal](https://portal.azure.com)에서 **리소스 만들기** 를 선택 합니다.

3. **템플릿을**검색 한 다음 **템플릿 배포**를 선택 합니다.

4. **만들기**를 선택합니다.

5. 템플릿을 만들기 위한 몇 가지 옵션이 표시 됩니다. **편집기에서 고유한 템플릿 빌드**를 선택 합니다.

6. **템플릿 편집 페이지**에서 **파일 로드** 를 선택 하 고 템플릿 파일을 선택 합니다.

7. **템플릿 편집** 페이지에서 **저장**을 선택 합니다.

8. **사용자 지정 배포** 페이지에서 다음을 지정 하 고 완료 되 면 **구매** 를 선택 하 여 템플릿을 배포 하 고 경고 규칙을 만듭니다.

    * Resource group
    * 위치
    * 경고 이름
    * 클러스터 리소스 ID

#### <a name="deploy-with-azure-powershell-or-cli"></a>Azure PowerShell 또는 CLI를 사용 하 여 배포

1. 다음 명령을 사용 하 여 경고 규칙을 만들려면 Azure Resource Manager 템플릿 및 매개 변수 파일을 다운로드 하 여 로컬 폴더에 저장 합니다.

2. PowerShell 또는 Azure CLI를 사용한 템플릿 및 매개 변수 파일을 사용하여 메트릭 경고를 만들 수 있습니다.

    Azure PowerShell 사용

    ```powershell
    Connect-AzAccount

    Select-AzSubscription -SubscriptionName <yourSubscriptionName>
    New-AzResourceGroupDeployment -Name CIMetricAlertDeployment -ResourceGroupName ResourceGroupofTargetResource `
    -TemplateFile templateFilename.json -TemplateParameterFile templateParameterFilename.parameters.json
    ```

    Azure CLI 사용

    ```azurecli
    az login

    az group deployment create \
    --name AlertDeployment \
    --resource-group ResourceGroupofTargetResource \
    --template-file templateFileName.json \
    --parameters @templateParameterFilename.parameters.json
    ```

    >[!NOTE]
    >메트릭 경고는 다른 리소스 그룹에서 대상 리소스로 만들 수 있지만, 대상 리소스와 동일한 리소스 그룹을 사용하는 것이 좋습니다.

## <a name="edit-alert-rules"></a>경고 규칙 편집

컨테이너 경고 규칙에 대 한 Azure Monitor를 보고 관리 하 여 해당 임계값을 편집 하거나 AKS 클러스터에 대 한 [작업 그룹](../platform/action-groups.md) 을 구성할 수 있습니다. Azure Portal 및 Azure CLI에서 이러한 작업을 수행할 수 있지만, 컨테이너에 대 한 Azure Monitor의 AKS 클러스터에서 직접 수행할 수도 있습니다.

1. 명령 모음에서 **권장 되는 경고**를 선택 합니다.

2. 임계값을 수정 하려면 **권장 되는 경고** 창에서 사용 경고를 선택 합니다. **편집 규칙**에서 편집 하려는 **경고 조건을** 선택 합니다.

    * 경고 규칙 임계값을 수정 하려면 **조건을**선택 합니다.
    * 기존를 지정 하거나 작업 그룹을 만들려면 **작업 그룹** 에서 **추가** 또는 **만들기** 를 선택 합니다.

사용 하도록 설정 된 규칙에 대해 생성 된 경고를 보려면 **권장 되는 경고** 창에서 **경고 보기**를 선택 합니다. 클러스터에 대해 현재 생성 된 모든 경고를 볼 수 있는 AKS 클러스터에 대 한 경고 메뉴로 리디렉션됩니다.

## <a name="configure-alertable-metrics-in-configmaps"></a>ConfigMaps에서 했어야 메트릭 구성

기본 컨테이너 리소스 사용률 임계값을 재정의 하도록 ConfigMap 구성 파일을 구성 하려면 다음 단계를 수행 합니다. 이러한 단계는 다음 했어야 메트릭에만 적용 됩니다.

* *cpuExceededPercentage*
* *memoryRssExceededPercentage*
* *memoryWorkingSetExceededPercentage*

1. 섹션 아래의 ConfigMap yaml 파일을 편집 합니다 `[alertable_metrics_configuration_settings.container_resource_utilization_thresholds]` .

2. *CpuExceededPercentage* 임계값을 90%로 수정 하 고 해당 임계값에 도달 하 여이 메트릭의 시작을 수집 하려면 다음 예제를 사용 하 여 configmap 파일을 구성 합니다.

    ```
    container_cpu_threshold_percentage = 90.0
    # Threshold for container memoryRss, metric will be sent only when memory rss exceeds or becomes equal to the following percentage
    container_memory_rss_threshold_percentage = 95.0
    # Threshold for container memoryWorkingSet, metric will be sent only when memory working set exceeds or becomes equal to the following percentage
    container_memory_working_set_threshold_percentage = 95.0
    ```

3. 다음 kubectl 명령을 실행 `kubectl apply -f <configmap_yaml_file.yaml>` 합니다.

    예: `kubectl apply -f container-azm-ms-agentconfig.yaml`.

구성 변경 내용을 적용 하기 전에 완료 하는 데 몇 분 정도 걸릴 수 있으며, 클러스터의 모든 omsagent pod가 다시 시작 됩니다. 다시 시작은 모든 omsagent pod에 대 한 롤링 다시 시작 이지만 동시에 다시 시작 되지 않습니다. 다시 시작이 완료 되 면 다음과 유사한 메시지가 표시 되 고 결과가 포함 됩니다 `configmap "container-azm-ms-agentconfig" created` .

## <a name="next-steps"></a>다음 단계

- [로그 쿼리 예](container-insights-log-search.md#search-logs-to-analyze-data) 를 확인 하 여 미리 정의 된 쿼리 및 예제를 확인 하거나 사용자 지정 하 여 클러스터에 대 한 경고, 시각화 또는 분석을 평가 하거나 사용자 지정 합니다.

- Azure Monitor 및 Kubernetes 클러스터의 다른 측면을 모니터링 하는 방법에 대 한 자세한 내용은 [Kubernetes 클러스터 성능 보기](container-insights-analyze.md)를 참조 하세요.
