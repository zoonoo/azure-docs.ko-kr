---
title: Azure Log Analytics를 사용한 컨테이너 인스턴스 로깅
description: Azure Log Analytics로 컨테이너 출력(STDOUT 및 STDERR)을 전송하는 방법을 알아봅니다.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: overview
ms.date: 07/17/2018
ms.author: danlep
ms.openlocfilehash: 62457fe5b0336cb6ae6ac4fc63f4884ed14cf9d3
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/08/2018
ms.locfileid: "48856040"
---
# <a name="container-instance-logging-with-azure-log-analytics"></a>Azure Log Analytics를 사용한 컨테이너 인스턴스 로깅

Log Analytics 작업 영역은 Azure 리소스뿐만 아니라 온-프레미스 리소스 및 다른 클라우드의 리소스에서도 로그 데이터를 저장 및 쿼리할 수 있는 중앙 집중식 위치를 제공합니다. Azure Container Instances는 Log Analytics로 데이터를 전송할 수 있는 기능을 기본 제공합니다.

Log Analytics로 컨테이너 인스턴스 데이터를 전송하려면 Azure CLI(또는 Cloud Shell) 및 YAML 파일을 사용하여 컨테이너 그룹을 만들어야 합니다. 다음 섹션에서는 로깅을 사용할 수 있는 컨테이너 그룹과 쿼리 로그를 만드는 방법을 설명합니다.

## <a name="prerequisites"></a>필수 조건

컨테이너 인스턴스에 로그인을 사용하도록 설정하려면 다음이 필요합니다.

* [Log Analytics 작업 영역](../log-analytics/log-analytics-quick-create-workspace.md)
* [Azure CLI](/cli/azure/install-azure-cli)(또는 [Cloud Shell](/azure/cloud-shell/overview))

## <a name="get-log-analytics-credentials"></a>Log Analytics 자격 증명 가져오기

Azure Container Instances에 Log Analytics 작업 영역에 데이터를 전송할 권한이 있어야 합니다. 이 권한을 부여하고 로깅을 사용하도록 설정하려면 컨테이너 그룹을 만들 때 Log Analytics 작업 영역 ID와 키(기본 키 또는 보조 키 중 하나)를 제공해야 합니다.

Log Analytics 작업 영역 ID 및 기본 키를 가져오려면:

1. Azure Portal에서 Log Analytics 작업 영역으로 이동
1. **설정** 아래에서 **진단 설정** 선택
1. **연결된 원본** > **Windows 서버**(또는 **Linux 서버** - 둘 다 ID와 키는 동일함) 선택
1. 다음을 기록해 둡니다.
   * **작업 영역 ID**
   * **기본 키**

## <a name="create-container-group"></a>컨테이너 그룹 만들기

Log Analytics 작업 영역 ID와 기본 키를 알고 있으므로 로깅을 사용할 수 있는 컨테이너 그룹을 만들 수 있습니다.

다음 예제에서는 단일 [fluentd][ fluentd] 컨테이너를 사용하여 컨테이너 그룹을 만드는 두 가지 방법인 Azure CLI 및 YAML 템플릿의 Azure CLI를 보여줍니다. Fluentd 컨테이너는 기본 구성에서 여러 줄의 출력을 생성합니다. 이 출력은 Log Analytics 작업 영역으로 전송되므로 로그를 보고 쿼리하는 방법을 보여주기에 효과적입니다.

### <a name="deploy-with-azure-cli"></a>Azure CLI를 사용하여 배포

Azure CLI를 사용하여 배포하려면 [az container create][az-container-create] 명령에서 `--log-analytics-workspace` 및 `--log-analytics-workspace-key` 매개 변수를 지정합니다. 다음 명령을 실행하기 전에 이전 단계에서 얻은 값으로 두 작업 영역 값을 바꾸고 리소스 그룹 이름을 업데이트합니다.

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainergroup001 \
    --image fluent/fluentd \
    --log-analytics-workspace <WORKSPACE_ID> \
    --log-analytics-workspace-key <WORKSPACE_KEY>
```

### <a name="deploy-with-yaml"></a>YAML을 사용하여 배포

YAML을 사용하여 컨테이너 그룹을 배포하려는 경우 이 메서드를 사용합니다. 다음 YAML은 단일 컨테이너로 컨테이너 그룹을 만듭니다. 새 파일에 YAML을 복사한 다음, `LOG_ANALYTICS_WORKSPACE_ID` 및 `LOG_ANALYTICS_WORKSPACE_KEY`를 이전 단계에서 구한 값으로 바꿉니다. 파일을 **deploy-aci.yaml**로 저장합니다.

```yaml
apiVersion: 2018-06-01
location: eastus
name: mycontainergroup001
properties:
  containers:
  - name: mycontainer001
    properties:
      environmentVariables: []
      image: fluent/fluentd
      ports: []
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
  osType: Linux
  restartPolicy: Always
  diagnostics:
    logAnalytics:
      workspaceId: LOG_ANALYTICS_WORKSPACE_ID
      workspaceKey: LOG_ANALYTICS_WORKSPACE_KEY
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

그리고 다음 명령을 실행하여 컨테이너 그룹을 배포합니다. `myResourceGroup`은 구독의 리소스 그룹으로 바꿉니다(또는 먼저 "myResourceGroup"이라는 리소스 그룹 만들기).

```azurecli-interactive
az container create --resource-group myResourceGroup --name mycontainergroup001 --file deploy-aci.yaml
```

명령 실행 즉시 Azure에서 배포 세부 정보가 포함된 응답이 수신되어야 합니다.

## <a name="view-logs-in-log-analytics"></a>Log Analytics에서 로그 보기

컨테이너 그룹을 배포한 후 첫 번째 로그 항목이 Azure Portal에 표시되기 까지 몇 분(최대 10분)이 걸릴 수 있습니다. 컨테이너 그룹 로그를 보려면 Log Analytics 작업 영역을 연 후 다음을 수행합니다.

1. **OMS 작업 영역** 개요에서 **로그 검색**을 선택합니다.
1. **시도해 볼 쿼리가 몇 가지 더 있습니다**에서 **수집된 모든 데이터** 링크를 선택합니다.

`search *` 쿼리에서 몇 가지 결과를 표시해야 합니다. 처음에 아무 결과도 표시되지 않으면 몇 분 정도 기다린 다음, **실행** 단추를 선택하여 쿼리를 다시 실행합니다. 기본적으로 로그 항목은 "목록" 보기에 표시됩니다. 로그 항목을 간략한 형식으로 보려면 **표**를 선택합니다. 그런 다음, 행을 확장하여 개별 로그 항목의 내용을 볼 수 있습니다.

![Azure Portal의 로그 검색 결과][log-search-01]

## <a name="query-container-logs"></a>쿼리 컨테이너 로그

Log Analytics에는 약 수천 줄의 로그 출력에서 정보를 가져오기 위한 광범위한 [쿼리 언어][query_lang]가 포함되어 있습니다.

Azure Container Instances 로깅 에이전트는 Log Analytics 작업 영역의 `ContainerInstanceLog_CL` 테이블로 항목을 전송합니다. 쿼리의 기본 구조는 원본 테이블(`ContainerInstanceLog_CL`)이며 그 뒤에 파이프 문자(`|`)로 구분된 일련의 연산자가 있습니다. 여러 연산자를 묶어 결과를 구체화하고 고급 기능을 수행할 수 있습니다.

예제 쿼리 결과를 보려면 다음 쿼리를 쿼리 텍스트 상자("레거시 언어 변환기 표시" 아래)에 붙여넣고 **실행** 단추를 선택하여 쿼리를 실행합니다. 이 쿼리는 "메시지" 필드에 "warn"이라는 단어가 포함된 모든 로그 항목을 표시합니다.

```query
ContainerInstanceLog_CL
| where Message contains("warn")
```

더 복잡한 쿼리도 지원됩니다. 예를 들어 이 쿼리는 지난 1시간 이내에 생성된 "mycontainergroup001" 컨테이너 그룹의 로그 항목만 표시합니다.

```query
ContainerInstanceLog_CL
| where (ContainerGroup_s == "mycontainergroup001")
| where (TimeGenerated > ago(1h))
```

## <a name="next-steps"></a>다음 단계

### <a name="log-analytics"></a>Log Analytics

Azure Log Analytics에서 로그를 쿼리하고 경고를 구성하는 방법에 대한 자세한 내용은 다음을 참조하세요.

* [Log Analytics의 로그 검색 이해](../log-analytics/log-analytics-log-search.md)
* [Azure Monitor의 통합 경고](../monitoring-and-diagnostics/monitoring-overview-unified-alerts.md)

### <a name="monitor-container-cpu-and-memory"></a>컨테이너 CPU 및 메모리 모니터링

컨테이너 인스턴스 CPU 및 메모리 리소스 모니터링에 대한 자세한 내용은 다음을 참조하세요.

* [Azure Container Instances에서 컨테이너 리소스 모니터링](container-instances-monitor.md)

<!-- IMAGES -->
[log-search-01]: ./media/container-instances-log-analytics/portal-query-01.png

<!-- LINKS - External -->
[fluentd]: https://hub.docker.com/r/fluent/fluentd/
[query_lang]: https://aka.ms/LogAnalyticsLanguage

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create