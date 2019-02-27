---
title: 컨테이너용 Azure Monitor 질문과 대답 | Microsoft Docs
description: 컨테이너용 Azure Monitor는 Azure에서 AKS 클러스터 및 Container Instances의 상태를 모니터링하는 솔루션입니다. 이 문서에서는 일반적인 질문에 답변합니다.
services: azure-monitor
author: mgoedtel
manager: carmonm
editor: tysonn
ms.service: azure-monitor
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/18/2019
ms.author: magoedte
ms.openlocfilehash: 27a191bb62ae59aa154167a22c99d3e699f3eb5a
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/19/2019
ms.locfileid: "56418478"
---
# <a name="azure-monitor-for-containers-frequently-asked-questions"></a>컨테이너용 Azure Monitor 질문과 대답
Microsoft FAQ는 컨테이너용 Azure Monitor에 대한 질문과 대답 목록입니다. 솔루션에 대한 추가 질문이 있으면 [토론 포럼](https://feedback.azure.com/forums/34192--general-feedback)으로 이동하여 질문을 게시하세요. 자주 묻는 질문일 경우 빠르고 쉽게 찾을 수 있도록 이 문서에 추가하겠습니다.

## <a name="why-am-i-not-seeing-data-if-log-analytics-workspace-is-configured-with-the-free-pricing-tier"></a>Log Analytics 작업 영역이 무료 가격 책정 계층으로 구성된 경우 데이터가 표시되지 않는 이유는 무엇인가요? 

기본값인 500MB 제한에 도달했거나 매일 수집하는 데이터 양을 제어하는 일일 상한을 지정한 것일 수 있습니다. 데이터 사용량을 확인하고 관리하려면 [로그 데이터 사용량 및 비용](../platform/manage-cost-storage.md)을 참조하세요. 

## <a name="what-are-the-states-of-containers-specified-in-the-containerinventory-table"></a>ContainerInventory 테이블에 지정된 컨테이너의 상태는 무엇인가요?
ContainerInventory 테이블에는 중지된 컨테이너와 실행 중인 컨테이너 둘 다에 대한 정보가 포함되어 있습니다. 이 테이블은 Docker에서 모든 컨테이너(실행 중 및 중지)를 쿼리하고 해당 데이터를 Log Analytics 작업 영역에 전달하는 에이전트 내 워크플로로 채워집니다.
 
## <a name="how-do-i-resolve-errors-related-to-missing-subscription-registration-for-microsoftoperationsmanagement"></a>**Microsoft.OperationsManagement의 구독 등록 누락**과 관련된 오류는 어떻게 해결하나요?
이 오류를 해결하려면 작업 영역이 정의되어 있는 구독에 리소스 공급자 **Microsoft.OperationsManagement**를 등록합니다. 이 작업을 수행하는 방법에 대한 설명서는 [여기](../../azure-resource-manager/resource-manager-register-provider-errors.md)에서 찾을 수 있습니다.

## <a name="does-azure-monitor-for-containers-include-support-for-rbac-enabled-aks-clusters"></a>컨테이너용 Azure Monitor가 RBAC 사용 AKS 클러스터를 지원하나요?
RBAC 사용 AKS 클러스터는 현재 이 솔루션에서 지원되지 않습니다. 솔루션 세부 정보 페이지에 이러한 클러스터에 대한 데이터를 표시하는 블레이드에 올바른 정보가 표시되지 않을 수 있습니다.

## <a name="how-do-i-enable-log-collection-for-containers-in-the-kube-system-namespace-through-helm"></a>Helm을 통해 kube-system 네임스페이스의 컨테이너에 대해 로그 수집을 사용하도록 설정하려면 어떻게 하나요?
kube-system 네임스페이스에 포함된 컨테이너의 로그 수집은 기본적으로 사용하지 않도록 설정됩니다. omsagent에 환경 변수를 설정하여 로그 수집을 사용하도록 설정할 수 있습니다. 자세한 내용은 [컨테이너용 Azure Monitor](https://github.com/helm/charts/tree/master/incubator/azuremonitor-containers) GitHub 페이지를 참조하세요. 

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

쿼리를 수행하면 이 데이터는 Azure Monitor의 로그에 대해 다음과 같이 표시됩니다.

```
LogEntry : ({“Hello": "This example has multiple lines:","Docker/Moby": "will not break this into multiple lines", "and you will receive":"all of them in log analytics", "as one": "log entry"}

```

이 문제를 자세히 살펴보려면 다음 [github 링크](https://github.com/moby/moby/issues/22920)를 검토하세요.

## <a name="how-do-i-resolve-azure-active-directory-errors-when-i-enable-live-logs"></a>실시간 로그를 사용하도록 설정하는 경우 Azure Active Directory 오류를 해결하려면 어떻게 하나요? 
다음 오류가 표시될 수 있습니다. **요청에 지정된 회신 URL이 애플리케이션에 대해 구성된 회신 URL( '60b4dec7-5a69-4165-a211-12c40b5c0435'**)과 일치하지 않습니다. 이 문제의 해결 방법은 [Azure Monitor에서 컨테이너 로그를 실시간으로 보는 방법](container-insights-live-logs.md#configure-aks-with-azure-active-directory) 문서에서 찾을 수 있습니다. 

## <a name="next-steps"></a>다음 단계
AKS 클러스터 모니터링을 시작하려면 [컨테이너용 Azure Monitor를 등록하는 방법](container-insights-onboard.md)을 검토하여 모니터링 사용을 위한 요구 사항과 사용 가능한 방법을 파악하세요. 