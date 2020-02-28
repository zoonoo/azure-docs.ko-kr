---
title: GA (VM용 Azure Monitor) 질문과 대답 | Microsoft Docs
description: VM용 Azure Monitor는 Azure VM 운영 체제의 상태 및 성능 모니터링뿐만 아니라 애플리케이션 구성 요소 및 종속성의 자동 검색도 다른 리소스와 결합하고, 이러한 항목 간의 통신을 매핑하는 Azure의 솔루션입니다. 이 문서에서는 GA 릴리스에 대 한 일반적인 질문에 답변 합니다.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/31/2020
ms.openlocfilehash: 0af5e4b92b52b4ecfc4e0e302b5d2a7701297908
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/27/2020
ms.locfileid: "77656197"
---
# <a name="azure-monitor-for-vms-generally-available-ga-frequently-asked-questions"></a>GA (VM용 Azure Monitor 일반 공급) 질문과 대답

이 일반적인 가용성 FAQ는 GA 릴리스를 준비할 때 VM용 Azure Monitor에서 발생 하는 변경 내용을 다룹니다. 

## <a name="updates-for-azure-monitor-for-vms"></a>VM용 Azure Monitor에 대 한 업데이트

새 버전의 VM용 Azure Monitor를 출시 했습니다. Vm에 대 한 Azure 모니터를 사용 하도록 설정 하는 고객은 이제 새 버전을 받게 되지만 이미 VM용 Azure Monitor를 사용 하는 기존 고객은 업그레이드 하 라는 메시지가 표시 됩니다. 이 질문과 설명서에서는 여러 작업 영역에 대규모 배포가 있는 경우 대규모 업그레이드를 수행 하는 지침을 제공 합니다.

이 업그레이드를 통해 VM용 Azure Monitor 성능 데이터는 [컨테이너에 대 한 Azure Monitor](container-insights-overview.md)와 동일한 *InsightsMetrics* 테이블에 저장 되므로 두 데이터 집합을 보다 쉽게 쿼리할 수 있습니다. 또한 이전에 사용한 테이블에 저장할 수 없는 다양 한 데이터 집합을 저장할 수 있습니다. 

이제 성능 보기에서 *InsightsMetrics* 테이블에 저장 하는 데이터를 사용 하 고 있습니다.  작업 영역에서 최신 VMInsights 솔루션을 사용 하도록 아직 업그레이드 하지 않은 경우 차트가 더 이상 정보를 표시 하지 않습니다.  아래에 설명 된 것 처럼 **시작** 페이지에서 업그레이드할 수 있습니다.

기존 고객에 게 업그레이드를 요청 하면 워크플로의 중단이 발생 합니다. 따라서 GA 이후에는 공개 미리 보기에서이 작업을 수행 하도록 선택 했습니다.


## <a name="what-is-changing"></a>변경되는 내용

Log Analytics 작업 영역에이 데이터를 저장 하는 새 위치와 함께 데이터 수집에 대 한 추가 기능을 포함 하는 VMInsights 라는 새 솔루션이 출시 되었습니다. 

이전에는 작업 영역에서 ServiceMap 솔루션을 사용 하도록 설정 하 고 Log Analytics 작업 영역에서 성능 카운터를 설정 하 여 데이터를 *Perf* 테이블로 보냅니다. 이 새 솔루션은 컨테이너의 Azure Monitor에도 사용 되는 *InsightsMetrics* 라는 테이블로 데이터를 보냅니다. 이 테이블 스키마를 사용 하면 *성능* 테이블 형식과 호환 되지 않는 추가 메트릭과 서비스 데이터 집합을 저장할 수 있습니다.

*InsightsMetrics* 테이블에 저장 하는 데이터를 사용 하도록 성능 차트를 업데이트 했습니다. 아래에 설명 된 것 처럼 **시작** 페이지의 *InsightsMetrics* 테이블을 사용 하도록를 업그레이드할 수 있습니다.


## <a name="how-do-i-upgrade"></a>업그레이드 어떻게 할까요??
Log Analytics 작업 영역이 최신 버전의 Vm에 대 한 Azure Monitor 최신 버전으로 업그레이드 되 면 해당 작업 영역에 연결 된 각 Vm에서 종속성 에이전트가 업그레이드 됩니다. 업그레이드가 필요한 각 VM은 Azure Portal VM용 Azure Monitor의 **시작** 탭에서 확인할 수 있습니다. VM을 업그레이드 하도록 선택 하면 해당 vm에 대 한 작업 영역을 해당 작업 영역에 연결 된 다른 Vm과 함께 업그레이드 합니다. 단일 VM 또는 여러 Vm, 리소스 그룹 또는 구독을 선택할 수 있습니다. 

다음 명령을 사용 하 여 PowerShell을 사용 하 여 작업 영역을 업그레이드 합니다.

```PowerShell
Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName <resource-group-name> -WorkspaceName <workspace-name> -IntelligencePackName "VMInsights" -Enabled $True
```

## <a name="what-should-i-do-about-the-performance-counters-in-my-workspace-if-i-install-the-vminsights-solution"></a>VMInsights 솔루션을 설치 하는 경우 내 작업 영역에서 성능 카운터는 어떻게 해야 하나요?

VM용 Azure Monitor 사용 하도록 설정 하는 현재 방법은 작업 영역에서 성능 카운터를 사용 합니다. 새 메서드는 `InsightsMetrics`라는 새 테이블에이 데이터를 저장 합니다.

`InsightsMetrics` 테이블의 데이터를 사용 하도록 사용자 인터페이스를 업데이트 한 후에는 설명서를 업데이트 하 고 Azure Portal에 배너를 표시 하는 것을 포함 하 여 여러 채널을 통해이 공지를 전달 합니다. 이 시점에서 더 이상 사용 하지 않아도 되는 작업 영역에서 이러한 [성능 카운터](vminsights-enable-overview.md#performance-counters-enabled) 를 사용 하지 않도록 선택할 수 있습니다. 

>[!NOTE]
>`Perf` 테이블에서 이러한 카운터를 참조 하는 경고 규칙이 있는 경우 `InsightsMetrics` 테이블에 저장 된 새 데이터를 참조 하도록 업데이트 해야 합니다. 이 테이블을 참조 하는 데 사용할 수 있는 예제 로그 쿼리는 설명서를 참조 하세요.
>

성능 카운터를 사용 하도록 설정 하는 경우 데이터 수집에 대 한 요금이 청구 되 고 [Log Analytics 가격 책정 [(https://azure.microsoft.com/pricing/details/monitor/)에 따라 `Perf` 테이블에 저장 됩니다.

## <a name="how-will-this-change-affect-my-alert-rules"></a>이 변경 내용은 내 경고 규칙에 어떤 영향을 미칩니까?

작업 영역에서 사용할 수 있는 성능 카운터를 대상으로 하는 `Perf` 테이블을 쿼리 하는 [로그 경고](../platform/alerts-unified-log.md) 를 만든 경우에는 대신 `InsightsMetrics` 테이블을 참조 하도록 이러한 규칙을 업데이트 해야 합니다. 이 지침은 `ServiceMapComputer_CL` 및 `ServiceMapProcess_CL`를 사용 하는 모든 로그 검색 규칙에도 적용 됩니다. 이러한 데이터 집합은 `VMComputer` 테이블과 `VMProcess` 테이블로 이동 하기 때문입니다.

수집 하는 데이터 집합에 대 한 예제 로그 검색 경고 규칙을 포함 하도록이 질문과 설명서를 업데이트할 예정입니다.

## <a name="how-will-this-affect-my-bill"></a>청구서에 어떻게 영향을 미칩니까?

청구는 여전히 데이터 수집을 기반으로 하며 Log Analytics 작업 영역에 보존 됩니다.

수집 하는 컴퓨터 수준 성능 데이터는 동일 하 고 `Perf` 테이블에 저장 된 데이터와 비슷한 크기 이며 거의 같은 비용으로 비용이 청구 됩니다.

## <a name="what-if-i-only-want-to-use-service-map"></a>서비스 맵만 사용 하려면 어떻게 해야 하나요?

괜찮습니다. 예정 된 업데이트에 대 한 VM용 Azure Monitor를 볼 때 Azure Portal에 프롬프트가 표시 됩니다. 릴리스된 후 새 버전으로 업데이트 하 라는 메시지가 표시 됩니다. [지도](vminsights-maps.md) 기능만 사용 하려는 경우 업그레이드 하지 않고 작업 영역 또는 대시보드 타일에서 액세스 하는 서비스 맵 솔루션 및 VM용 Azure Monitor의 맵 기능을 계속 사용 하도록 선택할 수 있습니다.

작업 영역에서 성능 카운터를 수동으로 사용 하도록 선택한 경우 Azure Monitor에서 볼 수 있는 일부 성능 차트에서 데이터를 볼 수 있습니다. 새 솔루션이 릴리스되면 성능 차트를 업데이트 하 여 `InsightsMetrics` 테이블에 저장 된 데이터를 쿼리 합니다. 이러한 차트에서 해당 테이블의 데이터를 보려면 VM용 Azure Monitor 새 버전으로 업그레이드 해야 합니다.

`ServiceMapComputer_CL` 및 `ServiceMapProcess_CL`에서 데이터를 이동 하는 변경 내용은 서비스 맵와 VM용 Azure Monitor 모두에 영향을 주므로이 업데이트에 대 한 계획을 세워야 합니다.

**VMInsights** 솔루션으로 업그레이드 하지 않기로 선택한 경우에는 `Perf` 테이블의 데이터를 참조 하는 레거시 버전의 성능 통합 문서가 계속 제공 됩니다.  

## <a name="will-the-service-map-data-sets-also-be-stored-in-insightsmetrics"></a>서비스 맵 데이터 집합이 InsightsMetrics에도 저장 됩니까?

두 솔루션을 모두 사용 하는 경우 데이터 집합이 중복 되지 않습니다. 두 제품은 모두 `VMComputer` (이전의 ServiceMapComputer_CL), `VMProcess` (이전의 ServiceMapProcess_CL), `VMConnection`테이블에 저장 되는 데이터 집합을 공유 하 여 수집 하는 맵 데이터 집합을 저장 합니다.`VMBoundPort`  

`InsightsMetrics` 테이블은 수집 하는 VM, 프로세스 및 서비스 데이터 집합을 저장 하 고 VM용 Azure Monitor 및 VM Insights 솔루션을 사용 하는 경우에만 채워집니다. 서비스 맵 솔루션은 `InsightsMetrics` 테이블에서 데이터를 수집 하거나 저장 하지 않습니다.

## <a name="will-i-be-double-charged-if-i-have-the-service-map-and-vminsights-solutions-in-my-workspace"></a>내 작업 영역에 서비스 맵 및 VMInsights 솔루션이 있는 경우 두 배가 청구 되나요?

아니요, 두 솔루션은 `VMComputer` (이전의 ServiceMapComputer_CL), `VMProcess` (이전의 ServiceMapProcess_CL), `VMConnection`, `VMBoundPort`에 저장 하는 맵 데이터 집합을 공유 합니다. 작업 영역에 두 솔루션이 모두 있는 경우에는이 데이터에 대 한 요금이 청구 되지 않습니다.

## <a name="if-i-remove-either-the-service-map-or-vminsights-solution-will-it-remove-my-data"></a>서비스 맵 또는 VMInsights 솔루션을 제거 하면 데이터가 제거 되나요?

아니요, 두 솔루션은 `VMComputer` (이전의 ServiceMapComputer_CL), `VMProcess` (이전의 ServiceMapProcess_CL), `VMConnection`, `VMBoundPort`에 저장 하는 맵 데이터 집합을 공유 합니다. 솔루션 중 하나를 제거 하는 경우 이러한 데이터 집합은 데이터를 사용 하는 솔루션이 여전히 있으며 Log Analytics 작업 영역에 남아 있음을 확인 합니다. 데이터를 제거 하려면 작업 영역에서 두 솔루션을 모두 제거 해야 합니다.

## <a name="when-will-this-update-be-released"></a>언제이 업데이트를 릴리스할 예정 인가요?

2020 년 1 월 초에 VM용 Azure Monitor 업데이트를 릴리스할 예정입니다. 1 월에 릴리스 날짜에 가까울수록 Azure Monitor를 열 때 여기에 업데이트를 게시 하 고 Azure Portal 알림을 제공 합니다.

## <a name="health-feature-is-in-limited-public-preview"></a>상태 기능이 제한 된 공개 미리 보기 상태입니다.

Microsoft는 VM 상태 기능 집합에 대 한 고객의 많은 유용한 피드백을 받았습니다. 이 기능에 대 한 많은 관심을 가지 며 모니터링 워크플로를 지원할 가능성이 있습니다. 기능을 추가 하 고 받은 피드백을 해결 하기 위해 일련의 변경을 수행할 계획입니다. 

이러한 변경 사항이 새 고객에 미치는 영향을 최소화 하기 위해이 기능을 제한 된 **공개 미리 보기로**옮겼습니다. 이 업데이트는 10 월 2019 일에 발생 합니다.

VM용 Azure Monitor GA에 있는 후 2020에서이 상태 기능을 다시 시작할 예정입니다.

## <a name="how-do-existing-customers-access-the-health-feature"></a>기존 고객이 상태 기능에 어떻게 액세스 하나요?

상태 기능을 사용 하는 기존 고객은 계속 액세스할 수 있지만 새 고객에 게는 제공 되지 않습니다.  

이 기능에 액세스 하려면 Azure Portal URL [https://portal.azure.com](https://portal.azure.com)에 `feature.vmhealth=true` 다음 기능 플래그를 추가할 수 있습니다. 예 `https://portal.azure.com/?feature.vmhealth=true`.

[https://aka.ms/vmhealthpreview](https://aka.ms/vmhealthpreview)기능 플래그를 자동으로 설정 하는이 짧은 url을 사용할 수도 있습니다.

기존 고객으로 서 상태 기능을 사용 하 여 기존 작업 영역 설정에 연결 된 Vm에서 상태 기능을 계속 사용할 수 있습니다.  

## <a name="i-use-vm-health-now-with-one-environment-and-would-like-to-deploy-it-to-a-new-one"></a>이제 하나의 환경에서 VM 상태를 사용 하 고 새 VM에 배포 하려는 경우

상태 기능을 사용 하 고 있는 기존 고객이 새 롤아웃에이를 사용 하려는 경우 vminsights@microsoft.com에 문의 하 여 지침을 요청 하세요.

## <a name="next-steps"></a>다음 단계

가상 머신을 모니터링하는 데 도움이 되는 요구 사항과 메서드를 이해하려면 [VM용 Azure Monitor 배포](vminsights-enable-overview.md)를 검토하세요.
