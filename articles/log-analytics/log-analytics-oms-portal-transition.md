---
title: Azure로 이동하는 OMS 포털 | Microsoft Docs
description: OMS 포털은 Azure Portal로 이동하면서 모든 기능이 종료되고 있습니다. 이 문서는 이 전환에 대해 자세한 정보를 제공합니다.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/27/2018
ms.author: bwren
ms.component: na
ms.openlocfilehash: b9fb32f4f014f8e0fb67b558a2806d74edaac56c
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/06/2018
ms.locfileid: "39576018"
---
# <a name="oms-portal-moving-to-azure"></a>Azure로 이동하는 OMS 포털

> [!NOTE]
> 이 문서는 별도로 명시된 곳을 제외하고 Azure 공용 클라우드 및 정부 클라우드 모두에 적용됩니다.

Log Analytics 고객에게 반복적으로 듣는 한 피드백은 단일 사용자 환경이 온-프레미스와 Azure 워크로드를 모니터링 및 관리해야 한다는 것입니다. Azure Portal은 모든 Azure 서비스의 허브이며, 리소스 고정을 위한 대시보드, 리소스를 찾기 위한 지능형 검색, 리소스 관리를 위한 태그 지정 같은 기능을 통해 다양한 관리 환경을 제공합니다. 모니터링 및 관리 워크플로를 간소화하고 통합하기 위해 Azure Portal에 OMS 포털 기능을 추가하기 시작했습니다. OMS 포털 기능 대부분이 이제 Azure Portal에 포함되었음을 알리게 되어 기쁩니다. 사실, 트래픽 분석 같은 새 기능 중 일부는 Azure Portal에서만 사용할 수 있습니다. 아직 Azure Portal로 이동하는 과정에 있는 몇몇 솔루션을 포함해 남은 격차가 별로 없습니다. 이러한 기능을 사용하지 않는 경우 Azure Portal 등을 사용하여 OMS 포털에서 하던 모든 것을 수행할 수 있습니다. 아직 그렇게 하지 않은 경우 지금 Azure Portal의 사용을 시작하는 것이 좋습니다! 

2018년 8월까지 두 포털 간에 나머지 격차를 줄일 예정입니다. 고객의 의견에 따라 OMS 포털 종료를 위한 타임라인을 전달하겠습니다. Azure Portal로 이동하게 돼 기쁘며 이 전환이 쉬울 것이라 예상합니다. 그러나 변경은 어려우며 중단될 수 있음을 이해합니다. 모든 질문, 피드백 또는 **LAUpgradeFeedback@microsoft.com**에 대한 우려를 보냅니다. 이 문서의 나머지 부분에서는 주요 시나리오, 현재 격차 및 이 전환에 대한 로드맵을 살펴봅니다. 

## <a name="progress"></a>진행
다음은 이 문서의 이전 버전 이후 완료된 업데이트입니다.

### <a name="july-27"></a>7월 27일

- [DNS 분석](log-analytics-dns.md)은 이제 Azure Portal에서 완벽하게 작동합니다.
- [업데이트 관리](../automation/automation-update-management.md)는 Azure Portal에서 완벽하게 작동되도록 업데이트되었습니다. 자세한 내용은 [OMS 업데이트 배포를 Azure로 마이그레이션](../automation/migrate-oms-update-deployments.md)을 참조하세요.
- [경고](#changes-to-alerts)는 이제 Azure Portal로 완전히 확장되었습니다.
- [사용자 지정 로그 미리 보기 기능](log-analytics-data-sources-custom-logs.md)은 이제 모든 작업 영역에 대해 자동으로 사용할 수 있습니다.

## <a name="what-will-change"></a>무엇이 변경되나요? 
다음 변경 사항은 OMS 포털의 사용 중단으로 공지되었습니다. 각 변경에 대한 자세한 설명은 아래 섹션에 자세히 나와 있습니다.

- Azure Portal에서만 새 작업 영역을 만들 수 있습니다.
- 새 경고 관리 환경이 경고 관리 솔루션을 대체하게 됩니다.
- 사용자 액세스 관리는 Azure 역할 기반 액세스 제어를 사용하여 Azure Portal에서 수행됩니다.
- Application Insights 커넥터는 동일한 기능을 작업 영역 간 쿼리를 통해 사용할 수 있으므로 더 이상 필요치 않습니다.
- OMS 모바일 앱은 사용되지 않습니다. 
- NSG 솔루션은 트래픽 분석 솔루션을 통해 사용 가능한 향상된 기능으로 대체되고 있습니다.
- System Center Operations Manager에서 Log Analytics로의 새로운 연결에는 업데이트된 관리 팩이 필요합니다.


## <a name="current-known-gaps"></a>현재 알려진 격차 
현재 OMS 포털을 계속 사용할 것을 요구하는 일부 기능 격차가 있습니다. 이러한 격차는 좁혀지고 있으며 이 문서는 적절히 업데이트됩니다. 또한 확장 및 변경에 대한 지속적인 알림을 위해서는 [Azure 업데이트](https://azure.microsoft.com/updates/?product=log-analytics)를 참조해야 합니다.

- 다음 솔루션은 아직 Azure Portal에서 완벽하게 작동하지 않습니다. 업데이트될 때까지는 이러한 솔루션을 클래식 포털에서 계속 사용해야 합니다.

    - Windows Analytics 솔루션([업그레이드 준비](https://technet.microsoft.com/itpro/windows/deploy/upgrade-readiness-get-started), [장치 상태](https://docs.microsoft.com/windows/deployment/update/device-health-monitor) 및 [업데이트 준수](https://technet.microsoft.com/itpro/windows/manage/update-compliance-get-started))
    - [Surface Hub](log-analytics-surface-hubs.md)

-  Azure에서 Log Analytics 리소스에 액세스하려면 사용자에게 [Azure 역할 기반 액세스](#user-access-and-role-migration)를 통해 액세스 권한이 부여돼야 합니다.


## <a name="what-should-i-do-now"></a>이제 어떻게 해야 하나요?  
Azure Portal로 전환 방법에 대한 자세한 내용은 [Log Analytics 사용자를 위한 OMS 포털에서 Azure Portal로 전환에 대한 일반적인 질문](../log-analytics/log-analytics-oms-portal-faq.md)을 참조하세요. [위에서 설명한 격차](#current-known-gaps)가 사용자 환경에 적용되지 않는 경우 기본 환경으로 Azure Portal을 사용하는 것을 고려해야 합니다. 모든 피드백, 질문 또는 **LAUpgradeFeedback@microsoft.com**에 대한 우려를 보냅니다.

대부분의 기능은 마이그레이션을 수행하지 않고 계속해서 작동합니다. 제한 사항은 아래에 나열되어 있습니다.

- 업데이트 관리 솔루션 전환에 대한 세부 정보는 [OMS 업데이트 배포를 Azure로 마이그레이션](../automation/migrate-oms-update-deployments.md)을 참조하세요. 

## <a name="new-workspaces"></a>새 작업 영역
7월 29일부터 OMS 포털을 사용하여 더 이상 새 작업 영역을 만들 수 없습니다. Azure Portal에서 새 작업 영역을 만들려면 [Azure Portal에서 Log Analytics 작업 영역 만들기](log-analytics-quick-create-workspace.md)의 지침을 따릅니다.

## <a name="changes-to-alerts"></a>경고에 대한 변경 

### <a name="alert-extension"></a>경고 확장  

> [!NOTE]
> 경고는 이제 공용 클라우드에 대한 Azure Portal로 완전히 확장되었습니다. OMS 포털에서 기존 경고 규칙을 볼 수 있지만 Azure Portal에서는 관리만 할 수 있습니다. Azure Portal로 경고의 확장은 Azure 정부 클라우드에 대해 2018년 10월에 시작합니다.

경고는 [Azure Portal로 확장](../monitoring-and-diagnostics/monitoring-alerts-extend.md)되는 과정에 있습니다. 완료되면 경고에 대한 관리 작업은 Azure Portal에서만 사용할 수 있습니다. 기존 경고는 계속 OMS 포털에 나열됩니다. Log Analytics 경고 REST API 또는 Log Analytics 경고 리소스 템플릿을 사용하여 프로그래밍 방식으로 경고에 액세스하는 경우 API 호출, Azure Resource Manager 템플릿 및 PowerShell 명령에서 작업 대신 작업 그룹을 사용해야 합니다.

### <a name="alert-management-solution"></a>경고 관리 솔루션
[경고 관리 솔루션](log-analytics-solution-alert-management.md) 대신 [Azure Monitor의 통합 경고 인터페이스](../monitoring-and-diagnostics/monitoring-overview-unified-alerts.md)를 사용하여 경고를 시각화하고 관리할 수 있습니다. 이 새로운 환경은 Log Analytics에서 로그 경고를 포함하여 Azure 내의 여러 원본에서 경고를 집계합니다. 경고의 배포를 확인하고, 스마트 그룹을 통해 관련 경고의 자동 그룹화를 활용하고, 풍부하게 필터를 적용하는 동안 여러 구독에서 경고를 볼 수 있습니다. 이러한 모든 기능은 2018년 6월 4일부터 미리 보기에서 사용할 수 있습니다. 경고 관리 솔루션은 Azure Portal에서 사용할 수 없습니다. 

경고 관리 솔루션(경고 유형별 레코드)에 의해 수집된 데이터는 솔루션이 작업 영역에 대해 설치되는 한 Log Analytics에 계속 포함됩니다. 2018년 8월부터 통합 경고에서 작업 영역으로 경고의 스트리밍은 이 기능을 대체해 사용하도록 설정됩니다. 일부 스키마 변경 사항이 예상되며 나중에 발표될 것입니다.

## <a name="user-access-and-role-migration"></a>사용자 액세스 및 역할 마이그레이션
Azure Portal 액세스 관리는 OMS 포털에서 액세스 관리보다 더욱 풍부하고 더욱 강력하지만 일부 변환이 필요합니다. Log Analytics에서 액세스 관리에 대한 자세한 내용은 [작업 영역 관리](log-analytics-manage-access.md#manage-accounts-and-users)를 참조하세요.

7월 30일부터 OMS 포털에서 Azure Portal 사용 권한으로의 액세스 제어 사용 권한의 자동 변환이 시작됩니다. 변환이 완료되면 OMS 포털 사용자 관리 섹션은 사용자를 Azure에서 액세스 제어(IAM)로 안내합니다. 

변환 시 시스템은 OMS 포털에서 사용 권한이 있는 각 사용자 또는 보안 그룹을 확인하고 Azure에서 동일한 수준 또는 사용 권한이 있는지를 결정합니다. 사용 권한이 누락된 경우 관련 작업 영역 및 솔루션에 대해 다음 역할을 할당합니다.

| OMS 포털 사용 권한 | Azure 역할 |
|:---|:---|
| ReadOnly | Log Analytics 읽기 권한자 |
| 참가자 | Log Analytics 참가자 |
| 관리자 | 소유자 |

과도한 사용 권한이 사용자에게 할당되지 않았는지 확인하려면 시스템은 리소스 그룹 수준에 이러한 사용 권한을 자동으로 할당하지 않습니다. 결과적으로, 작업 영역 관리자가 리소스 그룹에서 수동으로 직접 _소유자_ 또는 _기여자_ 역할을 할당하여 다음 작업을 수행해야 합니다.

- 솔루션 추가 또는 제거
- 새 사용자 지정 보기 정의
- 경고 관리 

일부 경우에 자동 변환은 사용 권한에 적용할 수 없으며 관리자에게 수동으로 사용 권한을 할당하라는 메시지가 표시됩니다.

## <a name="oms-mobile-app"></a>OMS 모바일 앱
OMS 모바일 앱은 OMS 포털과 함께 종료됩니다. OMS 모바일 앱 대신에 IT 인프라, 대시보드 및 저장된 쿼리에 대한 정보에 액세스하려면 모바일 장치의 브라우저에서 직접 Azure Portal에 액세스할 수 있습니다. 경고를 받으려면 [Azure 동작 그룹](../monitoring-and-diagnostics/monitoring-action-groups.md)을 구성하여 음성 통화 또는 SMS 형식으로 알림을 받아야 합니다.

## <a name="application-insights-connector-and-solution"></a>Application Insights 커넥터 및 솔루션
[Application Insights 커넥터](log-analytics-app-insights-connector.md)는 Application Insights 데이터를 Log Analytics 작업 영역으로 가져올 수 있는 방법을 제공합니다. 이 데이터 중복은 인프라 및 응용 프로그램 데이터에서 시각화를 사용하는 데 필요했습니다.

[리소스 간 쿼리](log-analytics-cross-workspace-search.md)의 지원으로 더 이상 데이터를 복제할 필요가 없습니다. 따라서 기존 Application Insights 솔루션은 지원 중단될 예정입니다. 7월부터 새 Application Insights 리소스를 Log Analytics 작업 영역에 연결할 수 없게 됩니다. 기존 링크 및 대시보드는 2018년 11월까지 서비스될 예정입니다.


## <a name="azure-network-security-group-analytics"></a>Azure 네트워크 보안 그룹 분석
[Azure 네트워크 보안 그룹 분석 솔루션](log-analytics-azure-networking-analytics.md#azure-network-security-group-analytics-solution-in-log-analytics)은 최근에 시작된 [트래픽 분석](https://azure.microsoft.com/en-in/blog/traffic-analytics-in-preview/)으로 대체되어 클라우드 네트워크에서 사용자 및 응용 프로그램 작업에 대한 가시성을 제공합니다. 트래픽 분석은 조직의 네트워크 활동을 감사하고, 응용 프로그램 및 데이터를 보호하고, 워크로드 성능을 최적화하고, 준수 상태를 유지하는 데 도움이 됩니다. 

이 솔루션은 NSG 흐름 로그를 분석하고 다음에 대한 인사이트를 제공합니다.

- 트래픽은 Azure와 인터넷, 공용 클라우드 지역, VNET 및 서브넷 사이의 네트워크를 통한 흐릅니다.
- 스니퍼나 전용 흐름 수집 어플라이언스가 필요 없는 네트워크상의 응용 프로그램 및 프로토콜입니다.
- 상위 토커, 대화량이 많은 응용 프로그램, 클라우드의 VM 대화, 트래픽 핫스폿.
- VNET, 중요한 비즈니스 서비스와 응용 프로그램 간의 상호 관계 트래픽의 원본 및 대상입니다.
- 악성 트래픽, 인터넷에 열려 있는 포트, 인터넷 액세스를 시도하는 응용 프로그램 또는 VM을 포함한 보안입니다.
- 과도한 프로비전 또는 미달 사용이라는 문제를 제거하는 데 도움이 되는 용량 사용률입니다.

Log Analytics에 NSG 로그를 보내려면 진단 설정에 계속 의존할 수 있으므로 기존 저장된 검색, 경고 및 대시보드는 계속 작동하게 됩니다. 이미 솔루션을 설치한 고객은 추가 공지가 있을 때까지 솔루션을 계속 사용할 수 있습니다. 8월 15일부터 네트워크 보안 그룹 분석 솔루션은 마켓플레이스에서 제거되고 커뮤니티를 통해 [Azure QuickStart 템플릿](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Operationalinsights)으로 사용할 수 있게 됩니다.

## <a name="system-center-operations-manager"></a>System Center Operations Manager
[Log Analytics에 Operations Manager 관리 그룹을 연결](log-analytics-om-agents.md)한 경우 변경 없이 계속 작동합니다. 그러나 새 연결의 경우에는 [Operations Management Suite를 구성하기 위한 Microsoft System Center Operations Manager 관리 팩](https://blogs.technet.microsoft.com/momteam/2018/07/25/microsoft-system-center-operations-manager-management-pack-to-configure-operations-management-suite/)의 지침을 따릅니다.

## <a name="next-steps"></a>다음 단계
- OMS 포털에서 Azure Portal로 이동에 대한 지침은 [Log Analytics 사용자를 위한 OMS 포털에서 Azure Portal로 전환에 대한 일반적인 질문](log-analytics-oms-portal-faq.md)을 참조하세요.