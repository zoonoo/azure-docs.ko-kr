---
title: 보안 센터 기능의 은퇴 기능 (7 월 2019) | 마이크로 소프트 문서
description: 이 문서에서는 2019년 7월 31일에 사용 중지된 보안 센터의 기능에 대해 설명합니다.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: article
ms.date: 09/10/2019
ms.author: memildin
ms.openlocfilehash: fda49ae31d7598497a3128a846a3f4e12e84ffe1
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80435901"
---
# <a name="retirement-of-security-center-features-july-2019"></a>보안 센터 기능의 은퇴 (7 월 2019)

> [!NOTE]
> 이 문서에서는 2019년 7월 31일에 Azure 보안 센터에서 사용 중지된 기능 목록을 자세히 설명합니다.
>
>

2019년 7월까지 6개월 동안 Azure 보안 센터를 몇 가지 [개선했습니다.](https://azure.microsoft.com/updates/?product=security-center)
이러한 향상된 기능을 통해 2019년 7월 31일에 보안 센터에서 일부 중복 기능 및 관련 API를 제거했습니다.

이러한 사용 중지된 기능의 대부분은 Azure 보안 센터 또는 Azure Log Analytics의 다른 기능으로 대체할 수 있습니다. 다른 기능은 Azure [Sentinel(미리 보기)을](https://azure.microsoft.com/services/azure-sentinel/)사용하여 구현할 수 있습니다.

은퇴한 보안 센터의 기능은 다음과 같습니다.

- [이벤트 대시보드](#menu_events)
- [검색 메뉴 항목](#menu_search)
- [ID 및 액세스에 대한 클래식 ID & 액세스 링크 보기(미리 보기)](#menu_classicidentity)
- [보안 이벤트 맵 버튼 의 보안 경고 맵(미리 보기)](#menu_securityeventsmap)
- [사용자 지정 경고 규칙(미리 보기)](#menu_customalerts)
- [위협 보호 보안 경고의 단추 조사](#menu_investigate)
- [보안 솔루션의 하위 집합](#menu_solutions)
- [보안 정책에 대한 보안 구성 편집](#menu_securityconfigurations)
- [로그 분석 작업 영역에 대한 보안 및 감사 대시보드(원래 OMS 포털에서 사용됨)](#menu_securityomsdashboard)

이 문서에서는 사용 중지된 각 기능에 대한 자세한 정보와 대체 기능을 구현하는 데 수행할 수 있는 단계를 제공합니다.

## <a name="events-dashboard"></a>이벤트 대시보드<a name="menu_events"></a>

보안 센터는 Log Analytics 에이전트를 사용하여 컴퓨터에서 다양한 보안 관련 구성 및 이벤트를 수집합니다. 이러한 이벤트는 작업 영역에 저장합니다. [이벤트 대시보드를](https://docs.microsoft.com/azure/security-center/security-center-events-dashboard) 사용하면 이 데이터를 볼 수 있으며 Log Analytics에 대한 진입점을 제공합니다.

작업 영역을 선택할 때 나타난 이벤트 대시보드는 모두 폐기되었습니다.

![이벤트 대시보드][2]

### <a name="events-dashboard---the-new-experience"></a>이벤트 대시보드 - 새로운 환경

Azure Log Analytics의 기본 기능을 사용하여 작업 공간에서 주목할 만한 이벤트를 보는 것이 좋습니다.

보안 센터에서 사용자 지정 주목할 만한 이벤트를 만든 경우 액세스할 수 있습니다. 로그 분석에서 저장된 검색 **영역선택으로** > **이동합니다.** 데이터는 손실되거나 수정되지 않습니다. 네이티브 주목할만한 이벤트는 로그 분석에서 동일한 화면에서 사용할 수 있습니다.

![작업 영역 저장된 검색][3]

## <a name="search-menu-entry"></a>검색 메뉴 항목<a name="menu_search"></a>

Azure Security Center는 현재 Azure Monitor 로그 검색을 사용하여 보안 데이터를 검색하고 분석합니다. 이 화면은 Log Analytics 검색 페이지의 창 역할을 하며 사용자가 선택한 작업 영역에서 검색 쿼리를 실행할 수 있도록 합니다. 자세한 내용은 [Azure 보안 센터 검색을](https://docs.microsoft.com/azure/security-center/security-center-search)참조하십시오. 이 검색 창은 사용 중지되었습니다.

![검색 페이지][4]

### <a name="search-menu-entry---the-new-experience"></a>검색 메뉴 항목 - 새로운 경험

Azure Log Analytics 네이티브 기능을 사용하여 작업 공간에서 검색 쿼리를 수행하는 것이 좋습니다. Azure 로그 분석으로 이동하여 **로그를 선택합니다.**

![로그 분석 로그 페이지][5]

## <a name="classic-identity--access-preview"></a>클래식 ID & 액세스(미리 보기)<a name="menu_classicidentity"></a>

보안 센터의 클래식 ID & 액세스 환경은 현재 로그 분석에서 ID 및 액세스 정보의 대시보드를 표시합니다. 이 대시보드를 보려면 다음을 수행하십시오.

1. **클래식 ID & 액세스 보기를**선택합니다.

   ![아이덴티티 페이지][6]

1. ID **& 액세스 대시보드를**봅니다.

    ![ID 페이지 - 작업 영역 선택][7]

1. 로그 애널리틱스에서 **ID & 액세스** 대시보드를 열려면 작업 영역을 선택하여 ID를 보고 작업 영역에서 정보에 액세스합니다.

   ![ID 페이지 - 대시보드][8]

이전 단계에 표시된 세 개의 화면을 모두 폐기했습니다. 데이터는 Log Analytics 보안 솔루션에서 사용할 수 있으며 수정되거나 제거되지 않았습니다.

### <a name="classic-identity--access-preview---the-new-experience"></a>클래식 id & 액세스(미리 보기) - 새로운 환경

로그 분석 대시보드는 단일 작업 영역에 대한 통찰력을 보여 주었습니다. 그러나 기본 보안 센터 기능은 모든 구독 및 연결된 모든 작업 영역에 대한 가시성을 제공합니다. 보안 점수에 따라 순위가 매겨진 권장 사항에 집중할 수 있는 사용하기 쉬운 보기에 액세스할 수 있습니다.

로그 분석의 **ID & 액세스** 대시보드의 모든 기능에 는 보안 센터 내에서 ID & **액세스(미리 보기)를** 선택하여 액세스할 수 있습니다.

![아이덴티티 페이지 - 클래식 경험 은퇴][9]

## <a name="security-events-map"></a>보안 이벤트 맵<a name="menu_securityeventsmap"></a>

보안 센터는 보안 위협을 식별하는 데 도움이 되는 [보안 경고 맵을](https://docs.microsoft.com/azure/security-center/security-center-threat-intel) 제공합니다. 해당 맵의 **보안 이벤트 맵으로 이동** 버튼은 선택한 작업 영역에서 원시 보안 이벤트를 볼 수 있는 대시보드를 엽니다.

**보안 이벤트로 이동 단추** 및 작업 영역별 대시보드를 제거했습니다.

![보안 알림 지도 - 버튼][10]

**보안 이벤트로 이동 단추를** 선택하면 (현재 사용 중지된) 위협 인텔리전스 대시보드가 열립니다.

![위협 인텔리전스 대시보드][11]

작업 영역을 선택하여 위협 인텔리전스 대시보드를 볼 때 Log Analytics에서 (현재 사용 중지된) 보안 경고 맵(미리 보기) 화면을 열었습니다.

![로그 분석에서 보안 경고 맵][12]

기존 데이터는 Log Analytics 보안 솔루션에서 사용할 수 있으며 수정되거나 제거되지 않았습니다.

### <a name="security-events-map---the-new-experience"></a>보안 이벤트 맵 - 새로운 경험

보안 센터: **보안 경고 맵(미리 보기)에**내장된 경고 맵 기능을 사용하는 것이 좋습니다. 이 기능은 최적화된 환경을 제공하며 모든 구독 및 관련 작업 영역에서 작동합니다. 사용자 환경 전반에 걸쳐 높은 수준의 뷰를 제공하며 단일 작업 영역에 초점을 맞추지 않습니다.

## <a name="custom-alert-rules-preview"></a>사용자 지정 경고 규칙(미리 보기)<a name="menu_customalerts"></a>

기본 인프라가 사용 [중지되었기](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) 때문에 2019년 6월 30일에 사용자 지정 경고 환경을 사용 중지했습니다. 사용 중지 날짜 이후에는 사용자 지정 보안 경고가 더 이상 생성되지 않습니다.
[Azure Sentinel을](https://azure.microsoft.com/services/azure-sentinel/) 사용하도록 설정하고 사용자 지정 경고를 다시 만드는 것이 좋습니다. 또는 Azure Monitor 로그 경고를 사용하여 경고를 만들 수 있습니다.

Azure Sentinel을 사용하여 사용자 지정 경고를 만들려면 다음을 수행합니다.

1. [Azure Sentinel을 열고](https://portal.azure.com/#create/Microsoft.ASI/preview) 사용자 지정 경고가 저장되는 작업 영역을 선택합니다.
1. 메뉴에서 **분석** 선택
1. Azure Sentinel에서 사용자 지정 경고를 만드는 방법에 대한 다음 [자습서의](https://docs.microsoft.com/azure/sentinel/tutorial-detect-threats) 지침을 따르십시오.

Azure Sentinel 사용에 관심이 없는 경우 Azure Monitor 로그 경고를 사용하여 경고를 만들 수 있습니다. 지침에 대 한 [참조 는 Azure 모니터 및 로그 경고를 사용 하 여 로그 경고 만들기, 보기 및 관리](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log) Azure [모니터에서 로그 경고를](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log)참조 합니다.

![사용자 지정 경고][13]

사용자 지정 경고 사용 중지에 대한 자세한 내용은 [Azure 보안 센터(미리 보기)의 사용자 지정 경고 규칙을](https://docs.microsoft.com/azure/security-center/security-center-custom-alert)참조하십시오.

## <a name="security-alerts-investigation"></a>보안 경고 조사<a name="menu_investigate"></a>

보안 [센터의 조사 기능을](https://docs.microsoft.com/azure/security-center/security-center-investigation) 사용하면 잠재적인 보안 인시던트를 분류할 수 있습니다. 이 기능을 사용하면 인시던트 범위를 이해하고 근본 원인을 추적할 수 있습니다. [Azure Sentinel에서](https://azure.microsoft.com/services/azure-sentinel/)향상된 환경으로 대체되었기 때문에 보안 센터에서 이 기능을 제거했습니다.

![보안 인시던트][14]

**보안 인시던트** 화면에서 **조사** 단추를 선택하면 로그 분석에서 조사 대시보드(미리 보기)를 엽니다. 조사 대시보드를 폐기했습니다.

기존 데이터는 Log Analytics 보안 솔루션에서 사용할 수 있으며 수정되거나 제거되지 않았습니다.

![로그 분석의 조사 대시보드][15]

### <a name="investigation---the-new-experience"></a>조사 - 새로운 경험

풍부한 조사 환경을 위해 [Azure Sentinel로](https://azure.microsoft.com/services/azure-sentinel/) 전환하는 것이 좋습니다. Azure Sentinel은 조직의 데이터 원본 전체에서 보안 위협을 찾아볼 수 있는 강력한 검색 및 쿼리 도구를 제공합니다.

## <a name="subset-of-security-solutions"></a>보안 솔루션의 하위 집합<a name="menu_solutions"></a>

보안 센터는 [Azure 에서 통합 보안 솔루션을](https://docs.microsoft.com/azure/security-center/security-center-partner-integration)활성화할 수 있습니다. 보안 센터에서 다음 파트너 솔루션을 폐기했습니다. 이러한 솔루션은 여러 추가 데이터 원본과 함께 [Azure Sentinel에서](https://azure.microsoft.com/services/azure-sentinel/) 활성화됩니다.

- [차세대 방화벽 및 웹 애플리케이션 방화벽 솔루션](https://docs.microsoft.com/azure/sentinel/connect-data-sources)
- [CEF(공통 이벤트 형식)를 지원하는 보안 솔루션 통합](https://docs.microsoft.com/azure/sentinel/connect-common-event-format)
- [Microsoft Advanced Threat Analytics](https://docs.microsoft.com/azure/sentinel/connect-azure-atp)
- [Azure AD ID 보호](https://docs.microsoft.com/azure/sentinel/connect-azure-ad-identity-protection)

사용 중지 후에는 UI 또는 API에서 이전 목록에 언급된 솔루션 형식을 추가하거나 수정할 수 없습니다. Azure 보안 센터는 더 이상 이러한 파트너 솔루션의 새 인스턴스를 검색하지 않습니다.

기존 에 연결된 솔루션이 있는 경우 Azure Sentinel로 이동하는 것이 좋습니다.

![보안 센터 솔루션][16]

## <a name="edit-security-configurations-for-security-policies"></a>보안 정책에 대한 보안 구성 편집<a name="menu_securityconfigurations"></a>

Azure Security Center는 OS를 강화하기 위해 [150개 이상의 추천 규칙](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335) 세트를 적용하여 보안 구성을 모니터링합니다. 이러한 규칙은 방화벽, 감사, 암호 정책 등과 관련이 있습니다. 컴퓨터에 취약한 구성이 있는 것으로 확인되면 Security Center에서 보안 권장 사항이 생성됩니다. [보안 구성 편집 화면에서는](https://docs.microsoft.com/azure/security-center/security-center-customize-os-security-config) 고객이 보안 센터에서 기본 OS 보안 구성을 사용자 지정할 수 있습니다.

이 미리 보기 기능을 사용 중지했습니다. 사용 중지 날짜 이후에 보안 구성을 기본값으로 다시 설정하려면 [다음 지침을](https://github.com/Azure/Azure-Security-Center/tree/master/Powershell%20samples/Reset%20security%20configurations%20customization)사용하여 API 또는 Powershell을 통해 다시 설정합니다.

![보안 구성 편집][17]

### <a name="edit-security-configurations---the-new-experience"></a>보안 구성 편집 - 새로운 환경

보안 센터에서 [게스트 구성 에이전트를](https://docs.microsoft.com/azure/governance/policy/concepts/guest-configuration)지원할 수 있도록 하려고 합니다. 이러한 업데이트를 통해 더 많은 운영 체제에 대한 지원 및 게스트 구성에 대한 Azure 인 게스트 정책 통합을 포함하여 훨씬 더 풍부한 기능 집합을 사용할 수 있습니다. 이러한 변경 사항을 사용하도록 설정하면 대규모로 구성을 제어하고 새 리소스에 자동으로 적용할 수도 있습니다.

## <a name="security-and-audit-dashboard-for-log-analytics-workspaces"></a>로그 분석 작업 영역에 대한 보안 및 감사 대시보드<a name="menu_securityomsdashboard"></a>

보안 및 감사 대시보드는 원래 OMS 포털에서 사용되었습니다. Log Analytics에서 대시보드는 주목할 만한 보안 이벤트 및 위협에 대한 작업 공간별 개요, 위협 인텔리전스 맵 및 작업 영역에 저장된 보안 이벤트의 ID 및 액세스 평가를 제공합니다. 대시보드를 제거했습니다. 대시보드 UI에서 이미 권장하는 것처럼 Azure 보안 센터로 전환하는 것이 좋습니다.

![로그 분석 보안 대시보드][18]

### <a name="security-and-audit-dashboard---the-new-experience"></a>보안 및 감사 대시보드 - 새로운 환경

Azure 보안 센터로 전환하는 것이 좋습니다. 여러 구독 및 이와 연관된 작업 영역과 동일한 보안 개요와 풍부한 기능 집합을 제공합니다.

보안 센터를 위한 [GitHub 리포지토리에서](https://github.com/Azure/Azure-Security-Center/tree/master/Legacy%20Log%20Analytics%20dashboards) 보안 및 감사 대시보드를 채우는 원래 Log Analytics 쿼리를 얻을 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure 보안 센터에](https://docs.microsoft.com/azure/security-center/)대해 자세히 알아봅니다.
- [Azure 센티넬](https://docs.microsoft.com/azure/sentinel)에 대해 자세히 알아보십시오.

<!--Image references - events-->
[1]: ./media/security-center-features-retirement-july2019/asc_events_dashboard.png
[2]: ./media/security-center-features-retirement-july2019/asc_events_dashboard_inner.png
[3]: ./media/security-center-features-retirement-july2019/workspace_saved_searches.png
<!--Image references - search-->
[4]: ./media/security-center-features-retirement-july2019/asc_search.png
[5]: ./media/security-center-features-retirement-july2019/workspace_logs.png
<!--Image references - classic identity and access-->
[6]: ./media/security-center-features-retirement-july2019/asc_identity.png
[7]: ./media/security-center-features-retirement-july2019/asc_identity_workspace_selection.png
[8]: ./media/security-center-features-retirement-july2019/loganalytics_dashboard_identity.png
[9]: ./media/security-center-features-retirement-july2019/asc_identity_nobuttonhighlight.png
<!--Image references - alerts map-->
[10]: ./media/security-center-features-retirement-july2019/asc_security_alerts_map.png
[11]: ./media/security-center-features-retirement-july2019/asc_threat_intellignece_dashboard.png
[12]: ./media/security-center-features-retirement-july2019/loganalytics_security_alerts_map.png
<!--Image references - custom alerts-->
[13]: ./media/security-center-features-retirement-july2019/asc_custom_alerts.png
<!--Image references - Investigation-->
[14]: ./media/security-center-features-retirement-july2019/asc-security-incident.png
[15]: ./media/security-center-features-retirement-july2019/loganalytics_investigation_dashboard.png
<!--Image references - Solutions-->
[16]: ./media/security-center-features-retirement-july2019/asc_security_solutions.png
<!--Image references - Edit security configurations-->
[17]: ./media/security-center-features-retirement-july2019/asc_edit_security_configurations.png
<!--Image references - Security dashboard in log analytics-->
[18]: ./media/security-center-features-retirement-july2019/loganalytics_security_dashboard.png
