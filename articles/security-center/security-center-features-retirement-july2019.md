---
title: Security Center 기능 사용 중지 (7 월 2019) | Microsoft Docs
description: 이 문서에서는 2019 년 7 월 31 일에 사용이 중지 된 Security Center의 기능을 설명 합니다.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: article
ms.date: 09/10/2019
ms.author: memildin
ms.openlocfilehash: c4fe185f5d203b1b0eba306c80cded14ad82bc98
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72438638"
---
# <a name="retirement-of-security-center-features-july-2019"></a>Security Center 기능 사용 중지 (7 월 2019)

> [!NOTE]
> 이 문서에서는 2019 년 7 월 31 일에 Azure Security Center에서 사용이 중지 된 기능 목록에 대해 자세히 설명 합니다.
>
>

6 개월 동안 7 월 2019까지 몇 Azure Security Center [개선](https://azure.microsoft.com/updates/?product=security-center) 했습니다.
이러한 향상 된 기능을 통해 2019 년 7 월 31 일에 Security Center에서 일부 중복 기능 및 관련 Api를 제거 했습니다.

이러한 사용 중지 된 기능의 대부분은 Azure Security Center 또는 Azure Log Analytics의 다른 기능으로 바꿀 수 있습니다. 다른 기능은 [Azure 센티널 (미리 보기)](https://azure.microsoft.com/services/azure-sentinel/)을 사용 하 여 구현할 수 있습니다.

사용이 중지 된 Security Center 기능은 다음과 같습니다.

- [이벤트 대시보드](#menu_events)
- [검색 메뉴 항목](#menu_search)
- [Id 및 액세스의 클래식 Id & 액세스 링크 보기 (미리 보기)](#menu_classicidentity)
- [보안 경고 맵의 보안 이벤트 맵 단추 (미리 보기)](#menu_securityeventsmap)
- [사용자 지정 경고 규칙 (미리 보기)](#menu_customalerts)
- [위협 방지 보안 경고의 조사 단추](#menu_investigate)
- [보안 솔루션의 하위 집합](#menu_solutions)
- [보안 정책에 대 한 보안 구성 편집](#menu_securityconfigurations)
- [Log Analytics 작업 영역에 대 한 보안 및 감사 대시보드 (원래 OMS 포털에서 사용 됨)](#menu_securityomsdashboard)

이 문서에서는 사용 중지 된 각 기능에 대 한 자세한 정보와 대체 기능을 구현 하기 위해 수행할 수 있는 단계를 제공 합니다.

## 이벤트 대시보드<a name="menu_events"></a>

Security Center은 Microsoft Monitoring Agent를 사용 하 여 컴퓨터에서 다양 한 보안 관련 구성 및 이벤트를 수집 합니다. 이러한 이벤트는 작업 영역에 저장 됩니다. [이벤트 대시보드](https://docs.microsoft.com/azure/security-center/security-center-events-dashboard) 를 사용 하 여이 데이터를 확인 하 고 Log Analytics에 대 한 진입점을 제공 합니다.

작업 영역을 선택할 때 표시 되는 이벤트 대시보드를 사용 중지 합니다.

![이벤트 대시보드][2]

### <a name="events-dashboard---the-new-experience"></a>이벤트 대시보드-새로운 환경

Azure Log Analytics의 기본 기능을 사용 하 여 작업 영역에서 주목할 만한 이벤트를 확인 하는 것이 좋습니다.

Security Center에서 사용자 지정 주목할 만한 이벤트를 만든 경우 액세스할 수 있습니다. Log Analytics에서 **작업 영역 선택**@no__t-**저장 된 검색**1 개로 이동 합니다. 데이터가 손실 되거나 수정 되지 않습니다. 기본 주목할 만한 이벤트는 Log Analytics 동일한 화면에서 사용할 수도 있습니다.

![작업 영역 저장 된 검색][3]

## 검색 메뉴 항목<a name="menu_search"></a>

Azure Security Center 현재 Azure Monitor 로그 검색을 사용 하 여 보안 데이터를 검색 하 고 분석 합니다. 이 화면은 검색 페이지 Log Analytics 창 역할을 하며 사용자가 선택한 작업 영역에서 검색 쿼리를 실행할 수 있도록 합니다. 자세한 내용은 [Azure Security Center 검색](https://docs.microsoft.com/azure/security-center/security-center-search)을 참조 하세요. 이 검색 윈도를 사용 중지 했습니다.

![검색 페이지][4]

### <a name="search-menu-entry---the-new-experience"></a>검색 메뉴 항목-새 환경

Azure Log Analytics 기본 기능을 사용 하 여 작업 영역에서 검색 쿼리를 수행 하는 것이 좋습니다. Azure Log Analytics로 이동 하 여 **로그**를 선택 합니다.

![Log Analytics 로그 페이지][5]

## 클래식 Id & 액세스 (미리 보기)<a name="menu_classicidentity"></a>

현재 Security Center의 클래식 Id & 액세스 환경은 Log Analytics의 id 및 액세스 정보 대시보드를 표시 합니다. 이 대시보드를 보려면 다음을 수행 합니다.

1. **클래식 id & 액세스 보기**를 선택 합니다.

   ![Id 페이지][6]

1. **Id & 액세스 대시보드**를 확인 합니다.

    ![Id 페이지-작업 영역 선택][7]

1. 작업 영역을 선택 하 여 id를 확인 하 고 작업 영역에 대 한 정보에 액세스 하는 Log Analytics에서 **id & 액세스** 대시보드를 엽니다.

   ![Id 페이지-대시보드][8]

이전 단계에 표시 된 세 화면 모두 사용이 중지 되었습니다. 데이터는 Log Analytics 보안 솔루션에서 사용할 수 있는 상태로 유지 되며 수정 되거나 제거 되지 않습니다.

### <a name="classic-identity--access-preview---the-new-experience"></a>클래식 Id & 액세스 (미리 보기)-새 환경

Log Analytics 대시보드는 단일 작업 영역에 대 한 정보를 보여 줍니다. 그러나 네이티브 Security Center 기능을 사용 하면 모든 구독과 연결 된 모든 작업 영역을 볼 수 있습니다. 사용 하기 쉬운 보기에 액세스 하 여 보안 점수에 따라 적용 되는 권장 사항에 중요 한 사항에 집중할 수 있습니다.

Security Center 내에서 **id & 액세스 (미리 보기)** 를 선택 하 여 Log Analytics에서 **id & 액세스** 대시보드의 모든 기능에 연결할 수 있습니다.

![Id 페이지-클래식 환경 사용 중지][9]

## 보안 이벤트 맵<a name="menu_securityeventsmap"></a>

Security Center는 보안 위협을 식별 하는 데 도움이 되는 [보안 경고 맵을](https://docs.microsoft.com/azure/security-center/security-center-threat-intel) 제공 합니다. 해당 맵의 **보안 이벤트로 이동 맵** 단추를 클릭 하면 선택한 작업 영역에서 원시 보안 이벤트를 볼 수 있는 대시보드가 열립니다.

**보안 이벤트로 이동 맵** 단추와 작업 영역 당 대시보드를 제거 했습니다.

![보안 경고 맵-단추][10]

**보안 이벤트로 이동 맵** 단추를 선택 하면 이제 사용 중지 된 위협 인텔리전스 대시보드가 열립니다.

![위협 인텔리전스 대시보드][11]

작업 영역을 선택 하 여 해당 위협 인텔리전스 대시보드를 볼 때 Log Analytics에서 사용이 중지 된 보안 경고 맵 (미리 보기) 화면을 열었습니다.

![Log Analytics의 보안 경고 매핑][12]

기존 데이터는 Log Analytics 보안 솔루션에서 사용할 수 있는 상태로 유지 되며 수정 되거나 제거 되지 않습니다.

### <a name="security-events-map---the-new-experience"></a>보안 이벤트 맵-새 환경

Security Center: **보안 경고 맵 (미리 보기)** 에 기본 제공 되는 경고 매핑 기능을 사용 하는 것이 좋습니다. 이 기능은 최적화 된 환경을 제공 하며 모든 구독과 관련 작업 영역에서 작동 합니다. 환경 전체에서 개략적인 보기를 제공 하며 단일 작업 영역에 집중 하지 않습니다.

## 사용자 지정 경고 규칙 (미리 보기)<a name="menu_customalerts"></a>

2019 년 6 월 30 일에 기본 인프라가 사용 중지 되었으므로 [사용자 지정 경고 환경을 사용 중지](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) 했습니다. 사용 중지 날짜 이후에는 더 이상 사용자 지정 보안 경고가 생성 되지 않습니다.
[Azure 센티널](https://azure.microsoft.com/services/azure-sentinel/) 을 사용 하도록 설정 하 고 사용자 지정 경고를 다시 만드는 것이 좋습니다. 또는 Azure Monitor 로그 경고를 사용 하 여 경고를 만들 수 있습니다.

Azure 센티널을 사용 하 여 사용자 지정 경고를 만들려면:

1. [Azure 센티널을 열고](https://portal.azure.com/#create/Microsoft.ASI/preview) 사용자 지정 경고가 저장 된 작업 영역을 선택 합니다.
1. 메뉴에서 **분석** 선택
1. Azure 센티널에서 사용자 지정 경고를 만드는 방법에 대 한 다음 [자습서](https://docs.microsoft.com/azure/sentinel/tutorial-detect-threats) 의 지침을 따르세요.

Azure 센티널을 사용 하지 않으려는 경우 Azure Monitor 로그 경고를 사용 하 여 경고를 만들 수 있습니다. 자세한 내용은 Azure Monitor 및 [로그 경고](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log)를 [사용 하 여 로그 경고 만들기, 보기 및 관리](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log) Azure Monitor를 참조 하세요.

![사용자 지정 경고][13]

사용자 지정 경고 사용 중지에 대 한 자세한 내용은 [Azure Security Center (미리 보기)의 사용자 지정 경고 규칙](https://docs.microsoft.com/azure/security-center/security-center-custom-alert)을 참조 하세요.

## 보안 경고 조사<a name="menu_investigate"></a>

Security Center의 [조사 기능은](https://docs.microsoft.com/azure/security-center/security-center-investigation) 잠재적인 보안 인시던트를 심사 하는 데 도움이 됩니다. 기능을 사용 하면 인시던트의 범위를 이해 하 고 근본 원인을 추적할 수 있습니다. [Azure 센티널](https://azure.microsoft.com/services/azure-sentinel/)에서 개선 된 환경으로 대체 되었기 때문에 Security Center에서이 기능을 제거 했습니다.

![보안 인시던트][14]

**보안 인시던트** 화면에서 **조사** 단추를 선택 하는 경우 Log Analytics에서 조사 대시보드 (미리 보기)를 엽니다. 조사 대시보드가 사용 중지 되었습니다.

기존 데이터는 Log Analytics 보안 솔루션에서 사용할 수 있는 상태로 유지 되며 수정 되거나 제거 되지 않습니다.

![Log Analytics의 조사 대시보드][15]

### <a name="investigation---the-new-experience"></a>조사-새로운 환경

풍부한 조사 환경을 위해 [Azure 센티널](https://azure.microsoft.com/services/azure-sentinel/) 로 전환 하는 것이 좋습니다. Azure 센티널은 강력한 검색 및 쿼리 도구를 제공 하 여 조직의 데이터 원본에서 보안 위협을 검색 합니다.

## 보안 솔루션의 하위 집합<a name="menu_solutions"></a>

Security Center는 [Azure에서 통합 보안 솔루션](https://docs.microsoft.com/azure/security-center/security-center-partner-integration)을 사용 하도록 설정할 수 있습니다. Security Center에서 다음과 같은 파트너 솔루션을 사용 중지 했습니다. 이러한 솔루션은 여러 추가 데이터 원본과 함께 [Azure 센티널](https://azure.microsoft.com/services/azure-sentinel/) 에서 사용 하도록 설정 됩니다.

- [차세대 방화벽 및 웹 응용 프로그램 방화벽 솔루션](https://docs.microsoft.com/azure/sentinel/connect-data-sources)
- [CEF (일반 이벤트 형식)를 지 원하는 보안 솔루션 통합](https://docs.microsoft.com/azure/sentinel/connect-common-event-format)
- [Microsoft Advanced Threat Analytics](https://docs.microsoft.com/azure/sentinel/connect-azure-atp)
- [Azure AD ID 보호](https://docs.microsoft.com/azure/sentinel/connect-azure-ad-identity-protection)

사용이 중지 된 후에는 UI 또는 API에서 위의 목록에 언급 된 솔루션 유형을 추가 하거나 수정할 수 없습니다. Azure Security Center은 이러한 파트너 솔루션의 새 인스턴스를 더 이상 검색 하지 않습니다.

기존에 연결 된 솔루션이 있는 경우 Azure 센티널로 이동 하는 것이 좋습니다.

![보안 센터 솔루션][16]

## 보안 정책에 대 한 보안 구성 편집<a name="menu_securityconfigurations"></a>

Azure Security Center는 OS를 강화하기 위해 [150개 이상의 추천 규칙](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335) 세트를 적용하여 보안 구성을 모니터링합니다. 이러한 규칙은 방화벽, 감사, 암호 정책 등에 관련 됩니다. 컴퓨터에 취약한 구성이 있는 것으로 확인되면 Security Center에서 보안 권장 사항이 생성됩니다. [보안 구성 편집 화면](https://docs.microsoft.com/azure/security-center/security-center-customize-os-security-config) 에서 고객이 Security Center의 기본 OS 보안 구성을 사용자 지정할 수 있습니다.

이 미리 보기 기능을 사용 중지 했습니다. 사용 중지 날짜 후에 보안 구성을 기본값으로 다시 설정 하려면 [다음 지침](https://github.com/Azure/Azure-Security-Center/tree/master/Powershell%20samples/Reset%20security%20configurations%20customization) 을 사용 하 여 API 또는 Powershell을 통해이 작업을 수행 합니다.

![보안 구성 편집][17]

### <a name="edit-security-configurations---the-new-experience"></a>보안 구성 편집-새 환경

Security Center를 사용 하도록 설정 하 여 [게스트 구성 에이전트](https://docs.microsoft.com/azure/governance/policy/concepts/guest-configuration)를 지원 하려고 합니다. 이러한 업데이트는 게스트 구성에 대 한 Azure 게스트 내 정책의 통합 및 추가 운영 체제에 대 한 지원을 포함 하 여 훨씬 더 다양 한 기능 집합을 허용 합니다. 이러한 변경 내용을 활성화 한 후에는 규모에 맞게 구성을 제어 하 고 새 리소스에 자동으로 적용할 수 있습니다.

## Log Analytics 작업 영역에 대 한 보안 및 감사 대시보드<a name="menu_securityomsdashboard"></a>

보안 및 감사 대시보드는 원래 OMS 포털에서 사용 되었습니다. Log Analytics에서 대시보드는 작업 영역에 저장 된 보안 이벤트에 대 한 중요 한 보안 이벤트와 위협, 위협 인텔리전스 맵 및 id 및 액세스 평가의 작업 영역 당 개요를 제공 합니다. 대시보드가 제거 되었습니다. 대시보드 UI에 이미 권장 된 것 처럼 Azure Security Center로 전환 하는 것이 좋습니다.

![Log Analytics 보안 대시보드][18]

### <a name="security-and-audit-dashboard---the-new-experience"></a>보안 및 감사 대시보드-새로운 환경

Azure Security Center로 전환 하는 것이 좋습니다. 여러 구독 및 이와 연결 된 작업 영역에서 동일한 보안 개요를 제공 하 고 다양 한 기능 집합을 제공 합니다.

Security Center에 대 한 [GitHub 리포지토리에서](https://github.com/Azure/Azure-Security-Center/tree/master/Legacy%20Log%20Analytics%20dashboards) 보안 및 감사 대시보드를 채우는 원래 Log Analytics 쿼리를 가져올 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure Security Center](https://docs.microsoft.com/azure/security-center/)에 대해 자세히 알아봅니다.
- [Azure 센티널](https://docs.microsoft.com/azure/sentinel)에 대해 자세히 알아보세요.

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
