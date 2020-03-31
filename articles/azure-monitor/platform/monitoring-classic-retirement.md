---
title: Azure 모니터에서 클래식 경고 & 모니터링 업데이트
description: 경고(클래식) 아래Azure 포털에 표시된 클래식 모니터링 서비스 및 기능의 폐기에 대한 설명입니다.
author: yanivlavi
services: azure-monitor
ms.topic: conceptual
ms.date: 2/7/2019
ms.author: yalavi
ms.subservice: alerts
ms.openlocfilehash: dec4d6824644cabf8b1872da207b8554fee0b3d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77659478"
---
# <a name="unified-alerting--monitoring-in-azure-monitor-replaces-classic-alerting--monitoring"></a>클래식 경고 및 모니터링을 Azure Monitor 통합 경고 및 모니터링으로 대체

Azure Monitor는 이제 리소스 전체에서 '하나의 메트릭' 및 '하나의 경고'를 지원하는 통합된 전체 스택 모니터링 서비스가 되었습니다. 자세한 내용은 [새 Azure Monitor에 대한 블로그 게시물](https://azure.microsoft.com/blog/new-full-stack-monitoring-capabilities-in-azure-monitor/)을 참조하세요. 새 Azure 모니터링 및 경고 플랫폼은 성장하고 있는 클라우드 컴퓨팅에 보조를 맞추고 Microsoft 지능형 클라우드 원칙에 맞게 더 빠르고, 더 스마트하게, 확장 가능하도록 설계되었습니다. 

새로운 Azure 모니터링 및 경고 플랫폼을 사용하면 Azure 경고의 *보기 클래식 경고* 섹션 내에서 호스팅되는 "클래식" 모니터링 및 경고 플랫폼을 **폐기하고 Azure 공용 클라우드에서 2019년 8월까지 더 이상 사용되지 않습니다.** [Azure 정부 클라우드](../../azure-government/documentation-government-welcome.md) 및 [Azure China 21Vianet은](https://docs.azure.cn/) 영향을 받지 않습니다.

> [!NOTE]
> 마이그레이션 도구의 롤아웃 이 지연으로 인해 클래식 경고 마이그레이션의 사용 중지 날짜가 원래 발표된 [날짜인 2019년 6월 30일부터 2019년 8월 31일로 연장되었습니다.](https://azure.microsoft.com/updates/azure-monitor-classic-alerts-retirement-date-extended-to-august-31st-2019/)

 ![Azure Portal의 클래식 경고](media/monitoring-classic-retirement/monitor-alert-screen2.png) 

새 플랫폼에서 경고를 시작하고 다시 만드는 것이 좋습니다. 많은 수의 경고가 있는 고객의 경우 중단이나 추가 비용 없이 기존 클래식 경고를 새 경고 시스템으로 이동하는 [자발적 마이그레이션 도구인](alerts-using-migration-tool.md) [단계로 롤아웃하고](alerts-understand-migration.md#rollout-phases)있습니다.

> [!IMPORTANT]
> 활동 로그에서 생성된 클래식 경고 규칙은 더 이상 사용 또는 마이그레이션되지 않습니다. 활동 로그에서 생성된 모든 클래식 경고 규칙은 새로운 Azure Monitor - 경고에서 액세스하여 사용할 수 있습니다. 자세한 내용은 [Azure Monitor를 사용하여 활동 로그 경고 만들기, 보기 및 관리](../../azure-monitor/platform/alerts-activity-log.md)를 참조하세요. 마찬가지로, Service Health에 대한 경고는 새로운 Service Health 섹션에서 현재 상태로 액세스하여 사용할 수 있습니다. 자세한 내용은 [서비스 상태 알림에서 경고](../../azure-monitor/platform/alerts-activity-log-service-notifications.md)를 참조하세요.

## <a name="unified-metrics-and-alerts-in-application-insights"></a>Application Insights의 통합 메트릭 및 경고

Azure Monitor의 새 메트릭 플랫폼은 이제 Application Insights에서 제공하는 모니터링을 강화합니다. 이러한 이동은 Application Insights가 작업 그룹에 연결되어 이전의 이메일 및 웹후크 호출보다 훨씬 더 많은 옵션을 허용합니다. 경고는 이제 음성 통화, Azure Functions, Logic Apps, SMS 및 ServiceNow 및 ITSM 도구(예: Automation Runbook)를 트리거할 수 있습니다. 거의 실시간 모니터링 및 경고를 제공하는 새 플랫폼을 통해 Application Insights 사용자는 동일한 기술을 활용하여 다른 Azure 리소스 모니터링을 강화하고 Microsoft 제품 모니터링을 지원할 수 있습니다.

Application Insights에 대한 새로운 통합 모니터링 및 경고에 포함된 항목은 다음과 같습니다.

- **Application Insights 플랫폼 메트릭** – Application Insights 제품에서 인기 있는 미리 작성된 메트릭을 제공합니다. 자세한 내용은 [새 Azure Monitor에서 Application Insights에 대한 플랫폼 메트릭 사용](../../azure-monitor/app/pre-aggregated-metrics-log-metrics.md#pre-aggregated-metrics) 문서를 참조하세요.
- **Application Insights 가용성 및 웹 테스트** - 웹 애플리케이션 또는 서버의 응답성과 가용성을 평가할 수 있는 기능을 제공합니다. 자세한 내용은 [새 Azure Monitor에서 Application Insights에 대한 가용성 테스트 및 알림 사용](../../azure-monitor/app/monitor-web-app-availability.md) 문서를 참조하세요.
- **Application Insights 사용자 지정 메트릭** – 모니터링 및 경고에 대한 자체의 메트릭을 정의하고 내보낼 수 있습니다. 자세한 내용은 [새 Azure Monitor에서 Application Insights에 대한 사용자 지정 메트릭 사용](../../azure-monitor/app/pre-aggregated-metrics-log-metrics.md#custom-metrics-dimensions-and-pre-aggregation) 문서를 참조하세요.
- **Application Insights 오류 이상(스마트 검색의 일부)** – 웹 애플리케이션이 실패한 HTTP 요청 또는 종속성 호출의 속도가 비정상적으로 증가하는 경우 거의 실시간으로 자동으로 알려줍니다. 자세한 내용은 [스마트 검색 - 오류 이상](https://docs.microsoft.com/azure/azure-monitor/app/proactive-failure-diagnostics)사용에 대한 이 문서를 참조하십시오.

## <a name="unified-metrics-and-alerts-for-other-azure-resources"></a>다른 Azure 리소스에 대한 통합 메트릭 및 경고

2018년 3월 이후 Azure 리소스에 대한 차세대 경고 및 다차원 모니터링이 출시되었습니다. 이제 새 메트릭 플랫폼 및 경고는 거의 실시간에 가까운 기능을 통해 더욱 빨라졌습니다. 무엇보다도 최신 플랫폼에 특정 값 조합, 조건 또는 작업을 조각화 및 필터링할 수 있는 차원 옵션이 포함되어 있어 최신 메트릭 플랫폼 경고에서 더 자세한 세분성을 제공합니다. 새 Azure Monitor의 모든 경고와 마찬가지로, ActionGroups를 사용함으로써 최신 메트릭 경고가 확장되어 이메일 또는 웹후크를 넘어 SMS, 음성, Azure Function, Automation Runbook 등으로 알림을 확장할 수 있습니다. 자세한 내용은 [Azure Monitor를 사용하여 메트릭 경고 만들기, 보기 및 관리](../../azure-monitor/platform/alerts-metric.md)를 참조하세요.
Azure 리소스에 대해 사용할 수 있는 최신 메트릭은 다음과 같습니다.

- **Azure Monitor 표준 플랫폼 메트릭** – 다양한 Azure 서비스와 제품에서 인기 있는 미리 채워진 메트릭을 제공합니다. 자세한 내용은 [Azure Monitor에서 지원되는 메트릭](../../azure-monitor/platform/alerts-metric-near-real-time.md#metrics-and-dimensions-supported) 및 [Azure Monitor에서 메트릭 경고 지원](../../azure-monitor/platform/alerts-metric-overview.md#supported-resource-types-for-metric-alerts) 문서를 참조하세요.
- **Azure Monitor 사용자 지정 메트릭** – Azure Diagnostics 에이전트가 포함된 사용자 구동 원본의 메트릭을 제공합니다. 자세한 내용은 [Azure Monitor의 사용자 지정 메트릭](../../azure-monitor/platform/metrics-custom-overview.md) 문서를 참조하세요. 사용자 지정 메트릭을 사용하면 [Windows Azure Diagnostics 에이전트](../../azure-monitor/platform/collect-custom-metrics-guestos-resource-manager-vm.md) 및 [InfluxData Telegraf 에이전트](../../azure-monitor/platform/collect-custom-metrics-linux-telegraf.md)에서 수집한 메트릭을 게시할 수도 있습니다.

## <a name="retirement-of-classic-monitoring-and-alerting-platform"></a>클래식 모니터링 및 경고 플랫폼의 사용 중지

앞에서 설명한 대로, 현재 Azure Portal의 [경고(클래식) 섹션](../../azure-monitor/platform/alerts-classic.overview.md)에서 사용할 수 있는 클래식 모니터링 및 경고 플랫폼은 새 시스템으로 교체됨에 따라 앞으로 몇 개월 내에 사용 중지됩니다.
이전 클래식 모니터링 및 경고는 2019년 8월 31일에 사용 중지됩니다. 관련 API, Azure 포털 인터페이스 및 서비스의 폐쇄를 포함합니다. 사용 중지되는 기능은 구체적으로 다음과 같습니다.

- 현재 Azure Portal의 [경고(클래식) 섹션](../../azure-monitor/platform/alerts-classic.overview.md)을 통해 사용할 수 있는 Azure 리소스에 대한 이전(클래식) 메트릭 및 경고이며, [microsoft.insights/alertrules](https://docs.microsoft.com/rest/api/monitor/alertrules) 리소스로 액세스할 수 있습니다.
- 현재 Azure Portal의 [경고(클래식) 섹션](../../azure-monitor/platform/alerts-classic.overview.md)을 통해 사용할 수 있는 Application Insights에 대한 이전(클래식) 플랫폼과 사용자 지정 메트릭 및 관련 경고이며, [microsoft.insights/alertrules](https://docs.microsoft.com/rest/api/monitor/alertrules) 리소스로 액세스할 수 있습니다.
- 현재 Azure Porta에서 [Application Insights 내 스마트 검색](../../azure-monitor/app/proactive-diagnostics.md)으로 사용할 수 있는 이전(클래식) 오류 이상 경고이며, Azure Portal의 [경고(클래식) 섹션](../../azure-monitor/platform/alerts-classic.overview.md)에 표시된 경고 구성도 포함됩니다.

해당 [API,](https://msdn.microsoft.com/library/azure/dn931945.aspx) [PowerShell,](../../azure-monitor/platform/alerts-classic-portal.md) [CLI,](../../azure-monitor/platform/alerts-classic-portal.md)Azure 포털 [페이지](../../azure-monitor/platform/alerts-classic-portal.md)및 [리소스 템플릿을](../../azure-monitor/platform/alerts-enable-template.md) 포함한 모든 클래식 모니터링 및 경고 시스템은 2019년 8월 말까지 사용할 수 있습니다. 

2019년 8월 말, Azure 모니터에서:

- 클래식 모니터링 및 경고 서비스는 사용 중지되며 새 경고 규칙을 만들 때 더 이상 사용할 수 없습니다.
- 2019년 8월 이후에도 경고(클래식)에 계속 존재하는 모든 경고 규칙은 계속 실행되고 알림이 실행되지만 수정에는 사용할 수 없습니다.
- 2019년 9월부터 마이그레이션할 수 있는 클래식 모니터링 & 경고 규칙의 경고 규칙이 Microsoft에서 몇 주에 걸친 단계의 새 Azure 모니터 플랫폼에서 해당 플랫폼에 대해 자동으로 이동됩니다. 이 프로세스는 가동 중지 시간 없이 원활하게 진행되며 고객이 모니터링 범위를 손실하지 않도록 보장합니다.
- 새 경고 플랫폼으로 마이그레이션된 경고 규칙은 이전처럼 모니터링 범위를 제공하지만 새 페이로드를 사용하여 알림을 생성합니다. 클래식 경고 규칙과 연결된 모든 전자 메일 주소, 웹후크 끝점 또는 논리 앱 링크는 마이그레이션할 때 전달되지만 새 플랫폼에서 경고 페이로드가 다를 수 있기 때문에 올바르게 수행되지 않을 수 있습니다.
- [자동으로 마이그레이션할 수 없고](alerts-understand-migration.md#classic-alert-rules-that-will-not-be-migrated) 사용자의 수동 조치가 필요한 일부 클래식 경고 규칙은 2020년 6월까지 계속 실행됩니다.

> [!IMPORTANT]
> Microsoft Azure Monitor는 곧 새로운 플랫폼으로 클래식 경고 규칙을 [자발적으로 마이그레이션하기](alerts-using-migration-tool.md) 위해 단계 도구에 출시되었습니다. 또한 2019년 9월부터 여전히 존재하고 마이그레이션할 수 있는 모든 클래식 경고 규칙에 대해 강제로 실행합니다. 고객은 클래식 경고 규칙이 마이그레이션된 후에 [Application Insights의 통합 메트릭 및 경고](#unified-metrics-and-alerts-in-application-insights) 또는 [기타 Azure 리소스용 통합 메트릭 및 경고](#unified-metrics-and-alerts-for-other-azure-resources)에서 클래식 경고 규칙 페이로드를 사용하는 자동화 기능이 새 페이로드를 처리할 수 있도록 조정되었는지를 확인해야 합니다. 자세한 내용은 [클래식 경고 규칙 마이그레이션 준비를](alerts-prepare-migration.md) 참조하세요.

이 문서의 내용은 신규 Azure 모니터링 및 경고 기능 관련 링크/세부 정보와, 새 Azure Monitor 플랫폼 채택 과정에서 사용자를 지원하는 도구의 사용 가능 여부 정보가 추가되는 방식으로 계속 업데이트될 예정입니다.

## <a name="pricing-for-migrated-alert-rules"></a>마이그레이션된 경고 규칙에 대한 가격 책정

Azure Monitor [클래식 경고를](../../azure-monitor/platform/alerts-classic.overview.md) 새 경고 환경으로 마이그레이션하는 데 도움이 되는 마이그레이션 도구를 롤아웃합니다. 마이그레이션된 경고 규칙 및 해당 마이그레이션된 작업 그룹(이메일, 웹후크 또는 LogicApp)은 무료로 유지됩니다. 임계값, 집계 유형 및 집계 세분성을 편집하는 기능을 포함하여 클래식 경고와 함께 있던 기능은 마이그레이션된 경고 규칙을 사용하여 계속 무료로 사용할 수 있습니다. 그러나 새 경고 플랫폼 기능, 알림 또는 작업 유형을 사용하도록 마이그레이션된 경고 규칙을 편집하면 해당 요금이 부과됩니다. 경고 규칙 및 알림에 대한 가격 책정에 대한 자세한 내용은 [Azure 모니터 가격 책정](https://azure.microsoft.com/pricing/details/monitor/)을 참조하십시오.

다음은 경고 규칙에 대한 요금이 부과되는 사례의 예입니다.

- 새 Azure Monitor 플랫폼에 체험 단위를 초과하여 만든 모든 새(마이그레이션되지 않음) 경고 규칙
- Azure Monitor에 포함된 체험 단위를 초과하여 수집되고 유지된 모든 데이터
- Application Insights에서 실행된 모든 다중 테스트 웹 테스트
- Azure Monitor에 포함된 체험 단위를 초과하여 저장된 모든 사용자 지정 메트릭
- 빈도, 여러 리소스/차원, [동적 임계값,](alerts-dynamic-thresholds.md)리소스/신호 변경 등과 같은 최신 메트릭 경고 기능을 사용하도록 편집된 모든 마이그레이션된 경고 규칙입니다.
- 최신 알림을 사용하도록 편집된 마이그레이션된 작업 그룹 또는 SMS, 음성 통화 및/또는 ITSM 통합과 같은 작업 유형입니다.

## <a name="next-steps"></a>다음 단계

* [새로운 통합 Azure Monitor](../../azure-monitor/overview.md)에 대해 자세히 알아봅니다.
* [새로운 Azure 경고](../../azure-monitor/platform/alerts-overview.md)에 대해 자세히 알아봅니다.
