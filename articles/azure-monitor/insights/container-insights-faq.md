---
title: 컨테이너용 Azure Monitor 질문과 대답 | Microsoft Docs
description: 컨테이너용 Azure Monitor는 Azure에서 AKS 클러스터 및 Container Instances의 상태를 모니터링하는 솔루션입니다. 이 문서에서는 일반적인 질문에 답변합니다.
ms.topic: conceptual
ms.date: 10/15/2019
ms.openlocfilehash: 0984de51221c506bb1824e4dcfd93eef56453a4d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75405078"
---
# <a name="azure-monitor-for-containers-frequently-asked-questions"></a>컨테이너용 Azure Monitor 질문과 대답

Microsoft FAQ는 컨테이너용 Azure Monitor에 대한 질문과 대답 목록입니다. 솔루션에 대한 추가 질문이 있으면 [토론 포럼](https://feedback.azure.com/forums/34192--general-feedback)으로 이동하여 질문을 게시하세요. 자주 묻는 질문일 경우 빠르고 쉽게 찾을 수 있도록 이 문서에 추가하겠습니다.

## <a name="i-dont-see-image-and-name-property-values-populated-when-i-query-the-containerlog-table"></a>ContainerLog 테이블을 쿼리할 때 채운 이미지 및 이름 속성 값이 표시 되지 않습니다.

에이전트 버전 ciprod12042019 이상에서는 수집 된 로그 데이터에 대해 발생 하는 비용을 최소화 하기 위해 모든 로그 줄에 대해 기본적으로 이러한 두 속성이 채워지지 않습니다. 이러한 속성을 포함 하는 테이블을 해당 값으로 쿼리 하는 두 가지 옵션이 있습니다.

### <a name="option-1"></a>옵션 1 

다른 테이블을 조인 하 여 이러한 속성 값을 결과에 포함 합니다.

ContainerID 속성에 조인 하 여 ```ContainerInventory``` 테이블에서 이미지 및 ImageTag 속성을 포함 하도록 쿼리를 수정 합니다. KubepodInventory 테이블의 ContaineName 필드에서 ContainerID 속성에 조인 하 여 이름 속성 (이전에는 ```ContainerLog``` 테이블에 표시 됨)을 포함할 수 있습니다. 이 옵션은 권장 되는 옵션입니다.

다음 예제는 조인을 사용 하 여 이러한 필드 값을 가져오는 방법을 설명 하는 샘플 상세 쿼리입니다.

```
//lets say we are querying an hour worth of logs
let startTime = ago(1h);
let endTime = now();
//below gets the latest Image & ImageTag for every containerID, during the time window
let ContainerInv = ContainerInventory | where TimeGenerated >= startTime and TimeGenerated < endTime | summarize arg_max(TimeGenerated, *)  by ContainerID, Image, ImageTag | project-away TimeGenerated | project ContainerID1=ContainerID, Image1=Image ,ImageTag1=ImageTag;
//below gets the latest Name for every containerID, during the time window
let KubePodInv  = KubePodInventory | where ContainerID != "" | where TimeGenerated >= startTime | where TimeGenerated < endTime | summarize arg_max(TimeGenerated, *)  by ContainerID2 = ContainerID, Name1=ContainerName | project ContainerID2 , Name1;
//now join the above 2 to get a 'jointed table' that has name, image & imagetag. Outer left is safer in-case there are no kubepod records are if they are latent
let ContainerData = ContainerInv | join kind=leftouter (KubePodInv) on $left.ContainerID1 == $right.ContainerID2;
//now join ContainerLog table with the 'jointed table' above and project-away redundant fields/columns and rename columns that were re-written
//Outer left is safer so you dont lose logs even if we cannot find container metadata for loglines (due to latency, time skew between data types etc...)
ContainerLog
| where TimeGenerated >= startTime and TimeGenerated < endTime 
| join kind= leftouter (
   ContainerData
) on $left.ContainerID == $right.ContainerID2 | project-away ContainerID1, ContainerID2, Name, Image, ImageTag | project-rename Name = Name1, Image=Image1, ImageTag=ImageTag1 

```

### <a name="option-2"></a>옵션 2

모든 컨테이너 로그 줄에 대해 이러한 속성에 대해 컬렉션을 다시 사용 하도록 설정 합니다.

쿼리 변경 내용으로 인해 첫 번째 옵션이 편리 하지 않을 경우 [데이터 컬렉션 구성 설정](./container-insights-agent-config.md)에 설명 된 대로 에이전트 구성 맵에서 ```log_collection_settings.enrich_container_logs``` 설정을 사용 하도록 설정 하 여 이러한 필드 수집을 다시 사용 하도록 설정할 수 있습니다.

> [!NOTE]
> 두 번째 옵션은 클러스터의 모든 노드에서 > API 서버 호출을 생성 하 여이 보강를 수행 하므로 노드가 50 이상인 대량 클러스터에는 권장 되지 않습니다. 이 옵션은 수집 된 모든 로그 줄에 대 한 데이터 크기도 늘립니다.

## <a name="can-i-view-metrics-collected-in-grafana"></a>Grafana에서 수집 된 메트릭을 볼 수 있나요?

컨테이너 Azure Monitor는 Grafana 대시보드의 Log Analytics 작업 영역에 저장 된 메트릭 보기를 지원 합니다. 사용자 지정 Grafana 대시보드를 시각화 하기 위해 모니터링 되는 클러스터의 추가 데이터를 쿼리 하는 방법을 배우는 데 도움이 되는 Grafana의 [대시보드 리포지토리에서](https://grafana.com/grafana/dashboards?dataSource=grafana-azure-monitor-datasource&category=docker) 다운로드할 수 있는 템플릿을 제공 했습니다. 

## <a name="can-i-monitor-my-aks-engine-cluster-with-azure-monitor-for-containers"></a>컨테이너에 대 한 Azure Monitor를 사용 하 여 AKS-engine 클러스터를 모니터링할 수 있나요?

컨테이너 Azure Monitor는 Azure에서 호스트 되는 AKS (이전의 ACS 엔진) 클러스터에 배포 된 컨테이너 작업을 모니터링 하도록 지원 합니다. 이 시나리오에 대 한 모니터링을 사용 하도록 설정 하는 데 필요한 단계에 대 한 자세한 내용과 개요는 [AKS의 컨테이너에 Azure Monitor 사용](https://github.com/microsoft/OMS-docker/tree/aks-engine)을 참조 하세요.

## <a name="why-dont-i-see-data-in-my-log-analytics-workspace"></a>내 Log Analytics 작업 영역에 데이터가 표시 되지 않는 이유는 무엇 인가요?

매일 특정 시간에 Log Analytics 작업 영역에서 데이터를 볼 수 없는 경우 매일 수집할 데이터의 양을 제어 하기 위해 지정 된 기본 500 MB 제한 또는 일일 상한에 도달 했을 수 있습니다. 해당 일에 대 한 제한이 충족 되 면 데이터 수집은 다음 날에만 중지 하 고 다시 시작 합니다. 예상 사용 패턴을 기준으로 데이터 사용량을 검토 하 고 다른 가격 책정 계층으로 업데이트 하려면 [로그 데이터 사용량 및 비용](../platform/manage-cost-storage.md)을 참조 하세요. 

## <a name="what-are-the-container-states-specified-in-the-containerinventory-table"></a>ContainerInventory 테이블에 지정 된 컨테이너 상태는 무엇 인가요?

ContainerInventory 테이블에는 중지된 컨테이너와 실행 중인 컨테이너 둘 다에 대한 정보가 포함되어 있습니다. 이 테이블은 Docker에서 모든 컨테이너(실행 중 및 중지)를 쿼리하고 해당 데이터를 Log Analytics 작업 영역에 전달하는 에이전트 내 워크플로로 채워집니다.
 
## <a name="how-do-i-resolve-missing-subscription-registration-error"></a>누락 된 *구독 등록* 오류를 해결 어떻게 할까요? 있습니까?

**Microsoft.operationsmanagement에 대 한 구독 등록 누락**오류가 표시 되 면 작업 영역이 정의 된 구독에서 리소스 공급자 **microsoft.operationsmanagement** 를 등록 하 여 해결할 수 있습니다. 이 작업을 수행하는 방법에 대한 설명서는 [여기](../../azure-resource-manager/resource-manager-register-provider-errors.md)에서 찾을 수 있습니다.

## <a name="is-there-support-for-rbac-enabled-aks-clusters"></a>RBAC 사용 AKS 클러스터에 대 한 지원이 있나요?

컨테이너 모니터링 솔루션은 RBAC를 지원 하지 않지만 컨테이너에 대 한 Azure Monitor에서 지원 됩니다. 솔루션 세부 정보 페이지에 이러한 클러스터에 대한 데이터를 표시하는 블레이드에 올바른 정보가 표시되지 않을 수 있습니다.

## <a name="how-do-i-enable-log-collection-for-containers-in-the-kube-system-namespace-through-helm"></a>Helm을 통해 kube-system 네임스페이스의 컨테이너에 대해 로그 수집을 사용하도록 설정하려면 어떻게 하나요?

kube-system 네임스페이스에 포함된 컨테이너의 로그 수집은 기본적으로 사용하지 않도록 설정됩니다. omsagent에 환경 변수를 설정하여 로그 수집을 사용하도록 설정할 수 있습니다. 자세한 내용은 [컨테이너 GitHub에 대 한 Azure Monitor](https://github.com/helm/charts/tree/master/incubator/azuremonitor-containers) 페이지를 참조 하세요. 

## <a name="how-do-i-update-the-omsagent-to-the-latest-released-version"></a>omsagent를 최신 릴리스 버전으로 업데이트하려면 어떻게 하나요?

에이전트를 업그레이드하는 방법을 알아보려면 [에이전트 관리](container-insights-manage-agent.md)를 참조하세요.

## <a name="how-do-i-enable-multi-line-logging"></a>여러 줄 로깅을 사용하도록 설정하려면 어떻게 하나요?

현재 컨테이너용 Azure Monitor는 여러 줄 로깅을 지원하지 않지만 해결 방법이 있습니다. JSON 형식으로 쓰도록 모든 서비스를 구성하면 Docker/Moby가 해당 데이터를 한 줄에 씁니다.

예를 들어, 샘플 node.js 애플리케이션에 대한 아래 예제와 같이 로그를 JSON 개체로 래핑할 수 있습니다.

```
console.log(json.stringify({ 
      "Hello": "This example has multiple lines:",
      "Docker/Moby": "will not break this into multiple lines",
      "and you will receive":"all of them in log analytics",
      "as one": "log entry"
      }));
```

이 데이터는 쿼리를 수행 하는 경우 로그에 대 한 Azure Monitor에서 다음 예제와 같이 표시 됩니다.

```
LogEntry : ({“Hello": "This example has multiple lines:","Docker/Moby": "will not break this into multiple lines", "and you will receive":"all of them in log analytics", "as one": "log entry"}

```

문제에 대 한 자세한 내용을 보려면 다음 [GitHub 링크](https://github.com/moby/moby/issues/22920)를 검토 하세요.

## <a name="how-do-i-resolve-azure-ad-errors-when-i-enable-live-logs"></a>라이브 로그를 사용 하도록 설정할 때 Azure AD 오류를 해결할 어떻게 할까요? 있나요? 

다음 오류가 표시 될 수 있습니다. **요청에 지정 된 회신 url이 응용 프로그램에 대해 구성 된 회신 url (' < 응용 프로그램 ID\>')과 일치 하지**않습니다. 이 문제를 해결 하는 [방법은 컨테이너에 대 한 Azure Monitor를 사용 하 여 실시간으로 컨테이너 데이터를 보는 방법](container-insights-livedata-setup.md#configure-ad-integrated-authentication)문서에서 찾을 수 있습니다. 

## <a name="why-cant-i-upgrade-cluster-after-onboarding"></a>온 보 딩 후 클러스터를 업그레이드할 수 없는 이유는 무엇입니까?

AKS 클러스터의 컨테이너에 대 한 Azure Monitor를 사용 하도록 설정한 후 클러스터를 업그레이드 하려고 할 때 클러스터가 데이터를 전송 하는 Log Analytics 작업 영역을 삭제 하면 오류가 발생 합니다. 이 문제를 해결 하려면 모니터링을 사용 하지 않도록 설정한 다음 구독에서 다른 유효한 작업 영역을 참조 하 여 다시 사용 하도록 설정 해야 합니다. 클러스터 업그레이드를 다시 수행 하려고 하면 성공적으로 처리 되 고 완료 됩니다.  

## <a name="which-ports-and-domains-do-i-need-to-openwhitelist-for-the-agent"></a>에이전트에 대해 열기/허용 목록 해야 하는 포트 및 도메인은 무엇 인가요?

Azure, Azure 미국 정부 및 Azure 중국 클라우드를 사용 하 여 컨테이너 화 된 에이전트에 필요한 프록시 및 방화벽 구성 정보는 [네트워크 방화벽 요구 사항](container-insights-onboard.md#network-firewall-requirements) 을 참조 하세요.

## <a name="next-steps"></a>다음 단계

AKS 클러스터 모니터링을 시작하려면 [컨테이너용 Azure Monitor를 등록하는 방법](container-insights-onboard.md)을 검토하여 모니터링 사용을 위한 요구 사항과 사용 가능한 방법을 파악하세요. 
