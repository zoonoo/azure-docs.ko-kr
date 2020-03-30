---
title: GA(VM용 Azure 모니터)에서 자주 묻는 질문 | 마이크로 소프트 문서
description: VM용 Azure Monitor는 Azure VM 운영 체제의 상태 및 성능 모니터링뿐만 아니라 애플리케이션 구성 요소 및 종속성의 자동 검색도 다른 리소스와 결합하고, 이러한 항목 간의 통신을 매핑하는 Azure의 솔루션입니다. 이 문서에서는 GA 릴리스에 대한 일반적인 질문에 대한 답변을 답변합니다.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/31/2020
ms.openlocfilehash: 3d250ef1aba979be04a44acaf31a3d685f162e37
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80283891"
---
# <a name="azure-monitor-for-vms-generally-available-ga-frequently-asked-questions"></a>일반적으로 사용 가능한 VM용 Azure 모니터(GA) 자주 묻는 질문
이 일반 사용 가능 FAQ는 GA에 대비한 2019년 4분기 및 2020년 1분기에 변경된 사항을 다룹니다.

## <a name="updates-for-azure-monitor-for-vms"></a>VM용 Azure 모니터에 대한 업데이트
GA 발표에 앞서 2020년 1월에 VM용 Azure 모니터의 새 버전을 릴리스했습니다. 이제 VM용 Azure 모니터를 사용하도록 설정하는 고객은 GA 버전을 받게 되지만 2019년 4분기 및 이전 버전의 VM용 Azure 모니터 버전을 사용하는 기존 고객은 업그레이드하라는 메시지가 표시됩니다. 이 FAQ는 여러 작업 영역에 대규모 배포가 있는 경우 대규모로 업그레이드를 수행할 수 있는 지침을 제공합니다.


이 업그레이드를 통해 VM 성능 데이터에 대한 Azure Monitor는 [컨테이너용 Azure Monitor와](container-insights-overview.md)동일한 *InsightsMetrics* 테이블에 저장되어 두 데이터 집합을 더 쉽게 쿼리할 수 있습니다. 또한 이전에 사용한 테이블에 저장할 수 없었던 보다 다양한 데이터 집합을 저장할 수 있습니다. 

이제 실적 뷰는 *InsightsMetrics* 테이블에 저장하는 데이터를 사용하고 있습니다.  작업 영역에서 최신 VMInsights 솔루션을 사용하도록 아직 업그레이드하지 않은 경우 차트에 더 이상 정보가 표시되지 않습니다.  아래 설명된 대로 **시작 하기** 페이지에서 업그레이드할 수 있습니다.


## <a name="what-is-changing"></a>변경되는 내용
로그 애널리틱스 작업 영역에 이 데이터를 저장하기 위한 새 위치와 함께 데이터 수집을 위한 추가 기능을 포함하는 VMInsights라는 새 솔루션을 출시했습니다. 

과거에는 Log Analytics 작업 영역에서 작업 영역 및 설정 성능 카운터에서 ServiceMap 솔루션을 사용하여 *데이터를 Perf* 테이블로 전송했습니다. 이 새 솔루션은 컨테이너에 Azure Monitor에서 사용하는 *InsightsMetrics라는* 테이블로 데이터를 보냅니다. 이 테이블 스키마를 사용하면 *Perf* 테이블 형식과 호환되지 않는 추가 메트릭 및 서비스 데이터 집합을 저장할 수 있습니다.

InsightsMetrics 테이블에 저장한 데이터를 사용하도록 성능 *차트를 업데이트했습니다.* 아래에 설명된 대로 **시작 하기** 페이지에서 *InsightsMetrics* 테이블을 사용 하 여 업그레이드할 수 있습니다.


## <a name="how-do-i-upgrade"></a>업그레이드는 어떻게 하나요?
Log Analytics 작업 영역이 Azure 모니터의 최신 버전으로 VM으로 업그레이드되면 해당 작업 영역에 연결된 각 VM에 대한 종속성 에이전트가 업그레이드됩니다. 업그레이드가 필요한 각 VM은 Azure 포털의 VM에 대한 Azure 모니터의 **시작** 받기 탭에서 식별됩니다. VM을 업그레이드하도록 선택하면 해당 작업 영역에 연결된 다른 VM과 함께 해당 VM의 작업 영역이 업그레이드됩니다. 단일 VM 또는 여러 VM, 리소스 그룹 또는 구독을 선택할 수 있습니다. 

다음 명령을 사용하여 PowerShell을 사용하여 작업 영역을 업그레이드합니다.

```PowerShell
Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName <resource-group-name> -WorkspaceName <workspace-name> -IntelligencePackName "VMInsights" -Enabled $True
```

## <a name="what-should-i-do-about-the-performance-counters-in-my-workspace-if-i-install-the-vminsights-solution"></a>VMInsights 솔루션을 설치하는 경우 작업 공간의 성능 카운터에 대해 어떻게 해야 합니까?

VM에 대한 Azure 모니터를 사용하도록 설정하는 이전 방법은 작업 영역에서 성능 카운터를 사용했습니다. 현재 버전은 이 데이터를 라는 `InsightsMetrics`테이블에 저장합니다. 더 이상 사용할 필요가 없는 경우 작업 공간에서 이러한 성능 카운터를 사용하지 않도록 선택할 수 있습니다. 

>[!NOTE]
>`Perf` 테이블에서 이러한 카운터를 참조하는 경고 규칙이 있는 경우 `InsightsMetrics` 테이블에 저장된 새 데이터를 참조하도록 해당 카운터를 업데이트해야 합니다. 이 표를 참조하는 데 사용할 수 있는 로그 쿼리와 같은 설명서를 참조하십시오.
>

성능 카운터를 사용하도록 설정하려면 [Log Analytics 가격 책정](를 기준으로 `Perf` 테이블에 수집되고 저장된 데이터에 대한https://azure.microsoft.com/pricing/details/monitor/)요금이 청구됩니다.)

## <a name="how-will-this-change-affect-my-alert-rules"></a>이 변경 사항은 내 경고 규칙에 어떤 영향을 미칩니까?

작업 영역에서 [Log alerts](../platform/alerts-unified-log.md) 활성화된 `Perf` 테이블 타겟팅 성능 카운터를 쿼리하는 Log 경고를 만든 경우 대신 `InsightsMetrics` 테이블을 참조하도록 이러한 규칙을 업데이트해야 합니다. 이 가이드는 해당 데이터 집합이 `ServiceMapProcess_CL`테이블로 `VMComputer` 이동하기 `VMProcess` 때문에 를 사용하는 `ServiceMapComputer_CL` 모든 로그 검색 규칙에도 적용됩니다.

이 FAQ 및 설명서를 업데이트하여 수집한 데이터 집합에 대한 예제 로그 검색 경고 규칙을 포함합니다.

## <a name="how-will-this-affect-my-bill"></a>이것은 내 청구서에 어떤 영향을 미칩니까?

청구는 여전히 Log Analytics 작업 영역에서 수집되고 유지되는 데이터를 기반으로 합니다.

우리가 수집하는 컴퓨터 수준 성능 데이터는 동일하며 `Perf` 테이블에 저장된 데이터와 비슷한 크기이며 거의 동일한 금액의 비용이 듭니다.

## <a name="what-if-i-only-want-to-use-service-map"></a>서비스 맵만 사용하려면 어떻게 해야 하나요?

그건 괜찮아요. 다가오는 업데이트에 대한 VM용 Azure 모니터를 볼 때 Azure 포털에 프롬프트가 표시됩니다. 릴리스되면 새 버전으로 업데이트하라는 메시지가 표시됩니다. [지도](vminsights-maps.md) 기능만 사용하려는 경우 VM용 Azure 모니터의 맵 기능및 작업 영역 또는 대시보드 타일에서 액세스한 서비스 맵 솔루션의 지도 기능을 업그레이드하지 않도록 선택할 수 있습니다.

작업 영역에서 성능 카운터를 수동으로 사용하도록 선택한 경우 Azure Monitor에서 본 일부 성능 차트의 데이터를 볼 수 있습니다. 새 솔루션이 릴리스되면 `InsightsMetrics` 성능 차트를 업데이트하여 테이블에 저장된 데이터를 쿼리합니다. 이러한 차트에서 해당 테이블의 데이터를 보려면 VM용 Azure Monitor의 새 버전으로 업그레이드해야 합니다.

데이터를 `ServiceMapComputer_CL` `ServiceMapProcess_CL` 이동하는 변경 사항은 VM에 대한 서비스 맵및 Azure 모니터모두에 영향을 미치므로 이 업데이트를 계획해야 합니다.

**VMInsights** 솔루션으로 업그레이드하지 않기로 선택한 경우 `Perf` 테이블의 데이터를 참조하는 성능 통합 문서의 레거시 버전을 계속 제공합니다.  

## <a name="will-the-service-map-data-sets-also-be-stored-in-insightsmetrics"></a>서비스 맵 데이터 세트도 InsightsMetrics에 저장되나요?

두 솔루션을 모두 사용하는 경우 데이터 집합이 중복되지 않습니다. 두 오퍼링 모두 저장될 데이터 `VMComputer` 집합(이전의 `VMProcess` ServiceMapComputer_CL) `VMConnection`및 `VMBoundPort` 당사가 수집하는 맵 데이터 집합을 저장하는 테이블(이전의 ServiceMapProcess_CL)을 공유합니다.  

이 `InsightsMetrics` 표는 수집한 VM, 프로세스 및 서비스 데이터 집합을 저장하며 VM 및 VM Insights 솔루션에 대한 Azure Monitor를 사용하는 경우에만 채워집니다. 서비스 맵 솔루션은 `InsightsMetrics` 테이블에 데이터를 수집하거나 저장하지 않습니다.

## <a name="will-i-be-double-charged-if-i-have-the-service-map-and-vminsights-solutions-in-my-workspace"></a>작업 영역에 서비스 맵 및 VMInsights 솔루션이 있는 경우 요금이 두 배로 청구되나요?

아니요, 두 솔루션은 (이전의 ServiceMapComputer_CL), `VMComputer` (이전의 `VMProcess` ServiceMapProcess_CL) `VMConnection`및 `VMBoundPort`에 저장하는 맵 데이터 집합을 공유합니다. 작업 영역에 두 솔루션이 모두 있는 경우 이 데이터에 대해 요금이 두 번 청구되지 않습니다.

## <a name="if-i-remove-either-the-service-map-or-vminsights-solution-will-it-remove-my-data"></a>서비스 맵 또는 VMInsights 솔루션을 제거하면 데이터가 제거되나요?

아니요, 두 솔루션은 (이전의 ServiceMapComputer_CL), `VMComputer` (이전의 `VMProcess` ServiceMapProcess_CL) `VMConnection`및 `VMBoundPort`에 저장하는 맵 데이터 집합을 공유합니다. 솔루션 중 하나를 제거하면 이러한 데이터 세트는 데이터를 사용하는 솔루션이 여전히 존재하며 Log Analytics 작업 영역에 남아 있음을 알 수 있습니다. 데이터를 제거하려면 작업 영역에서 두 솔루션을 모두 제거해야 합니다.

## <a name="health-feature-is-in-limited-public-preview"></a>상태 기능은 제한된 공개 미리 보기에 있습니다.

우리는 우리의 VM 건강 기능 세트에 대한 고객의 좋은 피드백을 많이 받았습니다. 이 기능에대한 많은 관심과 모니터링 워크플로우 지원 가능성에 대한 기대감이 있습니다. 기능을 추가하고 받은 피드백을 해결하기 위해 일련의 변경 사항을 계획하고 있습니다. 

이러한 변경 사항이 신규 고객에게 미치는 영향을 최소화하기 위해 이 기능을 **제한된 공개 미리 보기로**옮겼습니다. 이 업데이트는 2019년 10월에 일어났습니다.

VM용 Azure 모니터가 GA에 있는 후 2020년에 이 상태 기능을 다시 시작할 계획입니다.

## <a name="how-do-existing-customers-access-the-health-feature"></a>기존 고객은 Health 기능에 어떻게 액세스합니까?

Health 기능을 사용하는 기존 고객은 계속 액세스할 수 있지만 새 고객에게는 제공되지 않습니다.  

기능에 액세스하려면 Azure 포털 URL에 `feature.vmhealth=true` [https://portal.azure.com](https://portal.azure.com)다음 기능 플래그를 추가할 수 있습니다. 예제 `https://portal.azure.com/?feature.vmhealth=true`.

피처 플래그를 자동으로 설정하는 이 짧은 URL을 [https://aka.ms/vmhealthpreview](https://aka.ms/vmhealthpreview)사용할 수도 있습니다.

기존 고객은 상태 기능이 있는 기존 작업 영역 설정에 연결된 VM에서 상태 기능을 계속 사용할 수 있습니다.  

## <a name="i-use-vm-health-now-with-one-environment-and-would-like-to-deploy-it-to-a-new-one"></a>이제 하나의 환경에서 VM Health를 사용하고 새 환경에 배포하고 싶습니다.

Health 기능을 사용하고 있고 새 롤아웃에 사용하려는 기존 고객인 경우 문의하여 vminsights@microsoft.com 지침을 요청하십시오.

## <a name="next-steps"></a>다음 단계

가상 머신을 모니터링하는 데 도움이 되는 요구 사항과 메서드를 이해하려면 [VM용 Azure Monitor 배포](vminsights-enable-overview.md)를 검토하세요.
