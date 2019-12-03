---
title: GA (VM용 Azure Monitor) 질문과 대답 | Microsoft Docs
description: VM용 Azure Monitor는 Azure VM 운영 체제의 상태 및 성능 모니터링뿐만 아니라 애플리케이션 구성 요소 및 종속성의 자동 검색도 다른 리소스와 결합하고, 이러한 항목 간의 통신을 매핑하는 Azure의 솔루션입니다. 이 문서에서는 GA 릴리스에 대 한 일반적인 질문에 답변 합니다.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 10/07/2019
ms.openlocfilehash: 88634777897341f4bd4d8c12b5f9d3b6d9982758
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2019
ms.locfileid: "74671527"
---
# <a name="azure-monitor-for-vms-generally-available-ga-frequently-asked-questions"></a>GA (VM용 Azure Monitor 일반 공급) 질문과 대답

최근 [Azure 업데이트](https://azure.microsoft.com/blog/) 블로그를 발표 했지만 10 월 및 11 월 2019에 발생 하는 몇 가지 계획 된 변경 사항이 있습니다. 이러한 변경 내용에 대 한 자세한 내용은이 일반 공급 FAQ에서 다룹니다.

## <a name="updates-for-azure-monitor-for-vms"></a>VM용 Azure Monitor에 대 한 업데이트

11 월에 새 버전의 VM용 Azure Monitor를 출시 하 고 있습니다. 이 릴리스 후 Vm에 대 한 Azure 모니터를 사용 하도록 설정 하는 고객은 자동으로 새 버전을 수신 하지만 이미 VM용 Azure Monitor를 사용 하는 기존 고객은 업그레이드 하 라는 메시지가  이 FAQ 및 설명서에서는 여러 작업 영역에 대규모 배포를 수행 하는 경우 대량 업그레이드를 수행 하는 지침을 제공 합니다.

이 업그레이드를 사용 하면 VM용 Azure Monitor 성능 데이터 집합이 [컨테이너에 대해 Azure Monitor](container-insights-overview.md)같은 `InsightsMetrics` 테이블에 저장 되 고 두 데이터 집합을 보다 쉽게 쿼리할 수 있습니다. 또한 이전에 사용한 테이블에 저장할 수 없는 다양 한 데이터 집합을 저장할 수 있습니다.  이 새 테이블을 사용하도록 성능 보기도 업데이트됩니다.

연결 데이터 집합에 대 한 새 데이터 형식으로 이동 하 고 있습니다. 사용자 지정 로그 테이블을 사용 하는 `ServiceMapComputer_CL` 및 `ServiceMapProcess_CL`에 현재 저장 된 데이터는 `VMComputer` 및 `VMProcess`이라는 전용 데이터 형식으로 이동 됩니다.  전용 데이터 형식으로 이동 하면 데이터 수집을 위해 이러한 우선 순위를 지정할 수 있으며, 테이블 스키마는 모든 고객에 게 표준화 됩니다.

기존 고객에게 업그레이드하도록 요청하면 해당 워크플로에 중단이 발생하므로, GA된 후 나중에 하기보다는 현재 퍼블릭 미리 보기 상태일 때 업그레이드하도록 요청하기로 했습니다.

## <a name="what-will-change"></a>무엇이 변경되나요?

현재 VM용 Azure Monitor에 대 한 온 보 딩 프로세스를 완료 하면 모니터링 데이터를 저장 하기 위해 선택한 작업 영역에서 서비스 맵 솔루션을 사용 하도록 설정 하 고 Vm에서 수집한 데이터에 대 한 성능 카운터를 구성 합니다. 이후 주에는 Log Analytics에이 데이터를 저장 하기 위한 새 위치와 함께 데이터 수집에 대 한 추가 기능을 포함 하는 **VMInsights**라는 새 솔루션이 출시 될 예정입니다.

작업 영역에서 성능 카운터를 사용 하는 현재 프로세스는 Log Analytics의 Perf 테이블로 데이터를 보냅니다.  이 새 솔루션은 Azure Monitor 컨테이너에도 사용 되는 `InsightsMetrics` 라는 테이블로 데이터를 보냅니다. 이 테이블 스키마를 사용 하면 성능 테이블 형식과 호환 되지 않는 추가 메트릭과 서비스 데이터 집합을 저장할 수 있습니다.

## <a name="what-should-i-do-about-the-performance-counters-on-my-workspace-if-i-install-the-vminsights-solution"></a>VMInsights 솔루션을 설치 하는 경우 내 작업 영역에서 성능 카운터는 어떻게 해야 하나요?

VM용 Azure Monitor 사용 하도록 설정 하는 현재 방법은 작업 영역에서 성능 카운터를 사용 합니다. 새 메서드는 `InsightsMetrics`라는 새 테이블에이 데이터를 저장 합니다.

InsightsMetrics의 데이터를 사용 하도록 사용자 인터페이스를 업데이트 하면 설명서를 업데이트 하 고 Azure Portal에 배너를 표시 하는 것을 포함 하 여 여러 채널을 통해이 공지를 전달 합니다. 이 시점에서 더 이상 사용 하지 않으려는 경우 작업 영역에서 이러한 [성능 카운터](vminsights-enable-overview.md#performance-counters-enabled) 를 사용 하지 않도록 선택할 수 있습니다. 

>[!NOTE]
>성능 테이블에서 이러한 카운터를 참조 하는 경고 규칙이 있는 경우이를 업데이트 하 여 `InsightsMetrics` 테이블의 새 데이터를 참조 해야 합니다.  이 테이블을 참조 하는 데 사용할 수 있는 예제 로그 쿼리는 설명서를 참조 하세요.
>

성능 카운터를 사용 하도록 설정 하는 경우 데이터 수집에 대 한 요금이 청구 되 고 [Log Analytics 가격 책정 [(https://azure.microsoft.com/pricing/details/monitor/) 을 기준으로 성능 테이블에 보존 됩니다.

## <a name="how-will-this-change-affect-my-alert-rules"></a>이 변경 내용은 내 경고 규칙에 어떤 영향을 미칩니까?

작업 영역에서 사용 하도록 설정 된 성능 카운터를 대상으로 하는 `Perf` 테이블을 쿼리 하는 [로그 경고](../platform/alerts-unified-log.md) 를 만든 경우 대신 `InsightsMetrics` 테이블을 참조 하도록 이러한 규칙을 업데이트 해야 합니다. 이 지침은 `ServiceMapComputer_CL` 및 `ServiceMapProcess_CL`를 사용 하는 모든 로그 검색 규칙에도 적용 됩니다. 이러한 데이터 집합은 `VMComputer` 테이블과 `VMProcess` 테이블로 이동 하기 때문입니다.

수집 하는 데이터 집합에 대 한 예제 로그 검색 경고 규칙을 포함 하도록이 질문과 설명서를 업데이트할 예정입니다.

## <a name="will-there-be-any-changes-to-billing"></a>청구에 대 한 변경 내용이 있나요?

청구는 여전히 데이터 수집을 기반으로 하며 Log Analytics 작업 영역에 보존 됩니다.

수집 하는 컴퓨터 수준 성능 데이터는 동일 하 고, 성능 테이블에 저장 된 데이터의 크기와 비슷하며, 거의 같은 비용을 산출 합니다.

## <a name="what-if-i-only-want-to-use-service-map"></a>서비스 맵만 사용 하려면 어떻게 해야 하나요?

괜찮습니다.  예정 된 업데이트에 대 한 VM용 Azure Monitor를 볼 때 Azure Portal에 프롬프트가 표시 됩니다. 릴리스된 후 새 버전으로 업데이트 하 라는 메시지가 표시 됩니다. [지도](vminsights-maps.md) 기능을 사용 하려는 경우에만 업그레이드 하지 않고 작업 영역 또는 대시보드 타일에서 액세스할 수 있는 서비스 맵 솔루션 VM용 Azure Monitor 및 맵 기능을 계속 사용 하도록 선택할 수 있습니다.

작업 영역에서 성능 카운터를 수동으로 사용 하도록 선택한 경우 Azure Monitor에서 볼 수 있는 일부 성능 차트에서 데이터를 볼 수 있습니다. 새 솔루션이 릴리스되면 성능 차트를 업데이트 하 여 `InsightsMetrics` 테이블에 저장 된 데이터를 쿼리 합니다. 이러한 차트에서 해당 테이블의 데이터를 보려면 VM용 Azure Monitor 새 버전으로 업그레이드 해야 합니다.

`ServiceMapComputer_CL` 및 `ServiceMapProcess_CL`에서 데이터를 이동 하는 변경 내용은 서비스 맵와 VM용 Azure Monitor 모두에 영향을 주므로이 업데이트에 대 한 계획을 세워야 합니다.

**VMInsights** 솔루션으로 업그레이드 하지 않기로 선택한 경우에는 `Perf` 테이블의 데이터를 참조 하는 레거시 버전의 성능 통합 문서가 계속 제공 됩니다.  

## <a name="will-the-service-map-data-sets-also-be-stored-in-insightsmetrics"></a>서비스 맵 데이터 집합이 InsightsMetrics에도 저장 됩니까?

두 솔루션을 모두 사용 하는 경우 데이터 집합이 중복 되지 않습니다. 두 제품은 모두 `VMComputer` (이전의 ServiceMapComputer_CL), `VMProcess` (이전의 ServiceMapProcess_CL), `VMConnection`테이블에 저장 되는 데이터 집합을 공유 하 여 수집 하는 맵 데이터 집합을 저장 합니다.  

`InsightsMetrics` 테이블은 수집 하는 VM, 프로세스 및 서비스 데이터 집합을 저장 하는 데 사용 되며 VM용 Azure Monitor를 사용 하는 경우에만 채워집니다.

## <a name="will-i-be-double-charged-if-i-have-the-service-map-and-vminsights-solutions-on-my-workspace"></a>내 작업 영역에 서비스 맵 및 VMInsights 솔루션이 있는 경우 두 배가 청구 되나요?

아니요, 두 솔루션은 `VMComputer` (이전의 ServiceMapComputer_CL), `VMProcess` (이전의 ServiceMapProcess_CL), `VMConnection`, `VMBoundPort`에 저장 하는 맵 데이터 집합을 공유 합니다.  작업 영역에 두 솔루션이 모두 있는 경우에는이 데이터에 대 한 요금이 청구 되지 않습니다.

## <a name="if-i-remove-either-the-service-map-or-vminsights-solution-will-it-remove-my-data-in-log-analytics"></a>서비스 맵 또는 VMInsights 솔루션을 제거 하면 Log Analytics에서 데이터가 제거 됩니까?

아니요, 두 솔루션은 `VMComputer` (이전의 ServiceMapComputer_CL), `VMProcess` (이전의 ServiceMapProcess_CL), `VMConnection`, `VMBoundPort`에 저장 하는 맵 데이터 집합을 공유 합니다.  솔루션 중 하나를 제거 하는 경우 이러한 데이터 집합은 데이터를 사용 하는 솔루션을 계속 사용 하 고 Log Analytics에 남아 있음을 확인 합니다.  Log Analytics 작업 영역에서 데이터를 제거 하려면 작업 영역에서 두 솔루션을 모두 제거 해야 합니다.

## <a name="when-will-this-update-be-released"></a>언제이 업데이트를 릴리스할 예정 인가요?

11 월 중반에 VM용 Azure Monitor 업데이트를 릴리스할 예정입니다. 출시 날짜에 가까울수록 여기에 업데이트를 게시 하 고 Azure Monitor 이동할 때 Azure Portal에 알림을 제공 합니다.

## <a name="health-feature-to-enter-limited-public-preview"></a>제한 된 공개 미리 보기로 전환 하는 상태 기능

Microsoft는 VM 상태 기능 집합에 대 한 고객의 많은 유용한 피드백을 받았습니다.  이 기능에 관한 관심이 많았으며 이 기능의 모니터링 워크플로 지원 잠재성에 대한 기대도 컸습니다. Microsoft는 기능을 추가하고 받은 피드백을 처리하기 위해 일련의 변경을 계획하고 있습니다. 이러한 변경 사항이 새 고객에 미치는 영향을 최소화 하기 위해이 기능을 제한 된 공개 미리 보기로 전환 하 고 있습니다.

이 전환은 10 월 초에 시작 되며 해당 월의 끝에 완료 되어야 합니다.

중점적으로 살펴볼 영역은 다음과 같습니다.

- 상태 모델 및 임계값에 대 한 추가 제어
- Vm 범위에 적용 되는 확장 상태 모델 제작
- 상태 기반 경고 규칙을 관리 하기 위한 경고 플랫폼의 기본 사용
- 하나 이상의 구독과 같이 더 광범위 한 범위에서 상태를 확인 하는 기능
- 상태 전환 및 경고 알림에서 대기 시간이 감소 합니다.

## <a name="how-do-existing-customers-access-the-health-feature"></a>기존 고객이 상태 기능에 어떻게 액세스 하나요?

상태 기능을 사용 하는 기존 고객은 계속 액세스할 수 있지만 새 고객에 게는 제공 되지 않습니다.  

기능에 액세스 하려면 포털 URL [https://portal.azure.com](https://portal.azure.com)에 `feature.vmhealth=true` 다음 기능 플래그를 추가할 수 있습니다. 예 `https://portal.azure.com/?feature.vmhealth=true`.

[https://aka.ms/vmhealthpreview](https://aka.ms/vmhealthpreview)기능 플래그를 자동으로 설정 하는이 짧은 url을 사용할 수도 있습니다.

기존 고객으로 서 상태 기능을 사용 하 여 기존 작업 영역 설정에 연결 된 Vm에서 상태 기능을 계속 사용할 수 있습니다.  

## <a name="i-use-vm-health-now-with-one-environment-and-would-like-to-deploy-it-for-a-new-environment"></a>이제 하나의 환경에서 VM 상태를 사용 하 고 새 환경에 대해 배포 하 고 싶습니다.

상태 기능을 사용 하 고 있는 기존 고객이 새 롤아웃에이를 사용 하려는 경우 vminsights@microsoft.com에 문의 하 여 지침을 요청 하세요.

## <a name="next-steps"></a>다음 단계

가상 머신을 모니터링하는 데 도움이 되는 요구 사항과 메서드를 이해하려면 [VM용 Azure Monitor 배포](vminsights-enable-overview.md)를 검토하세요.
