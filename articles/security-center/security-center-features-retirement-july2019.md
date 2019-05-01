---
title: Security Center의 사용 중지 기능 (2019 년 7 월) | Microsoft Docs
description: 이 문서에서는 Security Center는 2019 년 7 월 31 일에 만료 됩니다에서 기능을 자세히 설명 합니다.
services: security-center
author: yoavfrancis
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.date: 4/16/2019
ms.author: yoafr
ms.openlocfilehash: 392782310d8bc3b38a3dd1349cb1760ca287acd1
ms.sourcegitcommit: 2c09af866f6cc3b2169e84100daea0aac9fc7fd0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/29/2019
ms.locfileid: "64875581"
---
# <a name="retirement-of-security-center-features-july-2019"></a>Security Center의 사용 중지 기능 (2019 년 7 월)

여러 만들었습니다 [향상 된 기능](https://azure.microsoft.com/updates/?product=security-center) 는 지난 6 달 동안 Azure Security Center에 있습니다.  
향상 된 기능을 사용 하 여 2019 년 7 월 31 일에 Security Center에서 다양 한 관련된 Api 뿐만 아니라 중복 기능을 제거 합니다.  

Azure Security Center 또는 Log Analytics;의 새로운 기능을 사용 하 여 대부분의 사용 중지 기능을 대체할 수 있습니다. 몇 가지 기능을 사용 하 여 구현할 수 있습니다 [Azure (미리 보기) Sentinel](https://azure.microsoft.com/services/azure-sentinel/), 최근에 발표 된 합니다.

Security Center에서 사용이 중지 되는 기능의 목록에는 다음이 포함 됩니다.

- [이벤트 대시보드](#menu_events)
- [검색 메뉴 항목](#menu_search)
- [클래식 Id 및 액세스 링크 보기에서 Id 및 액세스 (미리 보기)](#menu_classicidentity)
- [보안 이벤트에서 보안 경고 맵 (미리 보기) 단추를 매핑합니다.](#menu_securityeventsmap)
- [사용자 지정 경고 규칙 (미리 보기)](#menu_customalerts)
- [단추 위협 보호 보안 경고를 조사 합니다.](#menu_investigate)
- [보안 솔루션의 하위 집합](#menu_solutions)
- [보안 정책에 대 한 보안 구성 편집](#menu_securityconfigurations)
- [보안 및 감사 대시보드 (OMS 포털에서 사용 된 원래) Log Analytics 작업 영역에 대 한 합니다.](#menu_securityomsdashboard)

다음 사용 중지 된 각 기능 및 대체 기능을 사용 하 여 수행할 수 있는 단계에 대 한 자세한 정보를 제공 합니다.

## 이벤트 대시보드<a name="menu_events"></a>
Security Center에서는 Microsoft Monitoring Agent를 사용하여 컴퓨터에서 다양한 보안 관련 구성과 이벤트를 수집한 다음 작업 영역에 저장합니다. 합니다 [이벤트 대시보드](https://docs.microsoft.com/azure/security-center/security-center-events-dashboard) 이 데이터를 볼 수 있도록 하 고 기본적으로 Log Analytics에 다른 진입점을 제공 합니다.

이벤트 대시보드 앞 사용이 중지 됩니다.

![이벤트 작업 영역 선택 화면][1]

사용자가 작업 영역을 클릭 한 후 표시 되는 이벤트 대시보드도 사용 중지 됩니다.

![이벤트 대시보드][2]

### <a name="events-dashboard---new-experience"></a>이벤트 대시보드-새로운 환경

고객은 해당 작업 영역에서 주목할 만한 이벤트를 보려면 Log Analytics의 기본 기능을 사용 하는 것이 좋습니다.
Security Center에서 사용자 지정 주요 이벤트를 이미 만든 경우 다음을 통해 액세스할 수 Log analytics 선택-> 작업 영역에 저장 된 검색-> 합니다. 데이터는 손실 되거나 수정 되지 않습니다. 네이티브 주목할 만한 이벤트 동일한 화면에서 사용할 수 있습니다.

![작업 영역 저장 된 검색][3]

## 검색 메뉴 항목<a name="menu_search"></a>
Azure Security Center는 현재 검색 및 보안 데이터를 분석 하려면 Azure Monitor의 로그 검색을 사용 합니다. 이 화면은 기본적으로 Log Analytics에는 외관 역할 "[검색](https://docs.microsoft.com/azure/security-center/security-center-search)" 페이지-사용자가 선택한 작업 영역에서 검색 쿼리를 실행할 수 있습니다. 이 검색 창 앞 사용이 중지 됩니다.

![검색 페이지][4]

### <a name="search-menu-entry---new-experience"></a>검색 메뉴 항목-새로운 환경

고객은 사용 하는 것이 좋습니다 **Log Analytics** 자신의 작업 영역에서 검색 쿼리를 수행 하는 기본 기능입니다. 이렇게 하려면 Azure에서 Log analytics로 이동 하 고 선택 수 있습니다 이러한 합니다. "로그":

![Log Analytics 로그 페이지][5]

## 클래식 Id 및 액세스 (미리 보기)<a name="menu_classicidentity"></a>
Security Center에서 "클래식" id 및 액세스 환경을 고객이 자신의 Id 보기 및 log analytics의 관련된 정보에 액세스할 수 있는 방법을 제공 했습니다. 이 작업은 아래가 수행 하 여 수행 되었습니다.

"보기 클래식 Id 및 액세스" 클릭

![Id 페이지][6]

이 페이지를 열려면는 화면과 함께 "Id 및 액세스 대시보드":

![Id 페이지-작업 영역 선택][7]

작업 영역에서 클릭 "Id 및 액세스" 로그 분석 대시보드가 열립니다 고객 id 보려면 및 해당 작업 영역에 대 한 정보를 액세스 가능 합니다.

![Id 페이지-대시보드][8]

위의 모든 세 화면 앞 사용 되지 것입니다. 데이터 로그 분석 보안 솔루션에서 사용할 수 있는 남아 및 없습니다 수정 또는 제거할 수 있습니다.

### <a name="classic-identity--access-preview---new-experience"></a>클래식 Id 및 액세스 (미리 보기)-새로운 환경
로그 분석 대시보드는 지정 된 작업 영역에서 insights 제공한, 네이티브 Security Center 기능 모든 구독 및 동료를 쉽게 연결 하는 모든 작업 영역에 대 한 가시성 제공-수 있는 뷰를 사용 하도록의 중요 사용자 Id 및 액세스 권장 사항 제공에의 보안 점수에 따라 합니다.
Security Center에서 "Id 및 액세스 (미리 보기)"를 선택 하 여 모든 기능의 id 및 액세스 로그 분석 대시보드를 연결할 수 있습니다.

![Id 페이지-클래식 경험 사용 중지][9]

## 보안 이벤트 맵<a name="menu_securityeventsmap"></a>
Security Center를 제공 하는 [맵](https://docs.microsoft.com/azure/security-center/security-center-threat-intel) 수 있는 환경에 대 한 보안 위협을 식별 합니다. 이 맵에서 '보안 이벤트 맵으로 이동' 단추를 선택한 작업 영역에서 원시 보안 이벤트를 볼 수 있도록 대시보드에 연결 됩니다.
작업 영역 마다 대시보드와 함께 단추를 사용 중단 후 제거 됩니다.

![보안 경고 맵-단추][10]

현재 "보안 이벤트 맵으로 이동"을 클릭 하면 위협 인텔리전스 대시보드에 열려 있습니다. 위협 인텔리전스 대시보드는 사용 되지 것입니다.  

![위협 인텔리전스 대시보드][11]

보안 경고 map(Preview) 화면 위협 인텔리전스 대시보드를 볼 작업 영역을 선택 하면 *Log Analytics에서* 열립니다. 이 화면을 사용 되지 것입니다.

![Log Analytics에서 보안 경고 맵][12]

기존 데이터는 log analytics 보안 솔루션에서 사용할 수 있는 남아 및는 수정 하지 제거 합니다.

### <a name="security-events-map---new-experience"></a>보안 이벤트 맵-새로운 환경
Security Center에 기본 제공 경고 맵 기능을 사용 하려면 고객 것이 좋습니다-"보안 경고 (미리 보기)를 매핑" 합니다. 이 최적화 된 환경을 제공 및 매크로 허용, 모든 구독 및 연결 된 작업 영역에서 사용할 사용자 환경 전반의 보기 및 단일 작업 영역에 중점을 두 합니다.

## 사용자 지정 경고 규칙 (미리 보기)<a name="menu_customalerts"></a>
사용자 지정 경고 환경은 [사용 중지](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) 2019 년 6 월 30 일의 기본 인프라 사용 중지로 인해를 기반으로 합니다. 사용 중단 될 때까지 기간, 사용자가 기존 사용자 지정 경고 규칙을 편집할 수 있지만 새로 추가할 수 없습니다. 사용 하도록 설정 하려면 사용자는 것이 좋습니다 [Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/) 로그 경고를 자동으로 자신의 기존 경고 및 새 데이터베이스를 만들고 마이그레이션하거나 또는 Azure Monitor를 사용 하 여 해당 경고를 다시 만들 원클릭 등록 합니다.

에 기존 경고를 유지 하 고 Azure Sentinel 마이그레이션할 Azure Sentinel를 다시 시작 하십시오. 첫 번째 단계로, 사용자 지정 경고 저장 된 작업 영역을 선택 하 고 경고를 자동으로 마이그레이션할 '분석' 메뉴 항목을 선택 합니다.

![사용자 지정 경고][13]

Azure Sentinel에 온 보 딩 되지 관심이 있는 고객은 Azure Monitor의 로그 경고를 사용 하 여 해당 경고를 다시 만드는 것이 좋습니다. 지침을 참조 하세요. [만들기, 보기 및 관리 Azure Monitor를 사용 하 여 로그 경고](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log)합니다. 로그 경고를 만드는 방법에 대 한 자세한 내용은 다음을 참조 하세요. [Azure Monitor의 로그 경고](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log)합니다.

사용자 지정 경고 사용 중지에 대 한 자세한 내용은 참조 하십시오 합니다 [사용자 지정 하는 Security Center 경고 설명서](https://docs.microsoft.com/azure/security-center/security-center-custom-alert)합니다.

## 보안 경고 조사<a name="menu_investigate"></a>
[조사 기능](https://docs.microsoft.com/azure/security-center/security-center-investigation) Security Center에서 심사 범위를 이해 하 고 잠재적 보안 인시던트의 근본 원인을 추적할 수 있습니다. 향상된 된 환경을 사용 하 여 바뀌었습니다이 기능은 Security Center에서 제거 됩니다 [Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/)합니다.

![보안 인시던트][14]

위의 "조사" 단추를 클릭 하면 "조사 대시보드 (미리 보기)" Log Analytics에서 열립니다. 조사 대시보드는 사용 되지 것입니다.  
기존 데이터는 Log Analytics 보안 솔루션에서 사용할 수 있는 남아 및는 수정 하지 제거 합니다.

![Log Analytics를 조사 대시보드][15]

### <a name="investigation---new-experience"></a>조사-새로운 환경

고객 온 보 딩 것이 좋습니다 [Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/) 다양 한 조사 환경을 기능 렵 경고에서 제공 합니다. Azure Sentinel 조직의 데이터 원본에서 보안 위협에 대 한 사냥 하 강력한 찾거나 검색 및 쿼리 도구를 제공 합니다.  

## 보안 솔루션의 하위 집합<a name="menu_solutions"></a>

Security Center가 지 원하는 기능 [Azure의 보안 솔루션 통합](https://docs.microsoft.com/azure/security-center/security-center-partner-integration)합니다. 다음 파트너 솔루션은 제거 되 고 지원 [Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/), 더 많은 데이터 원본과 함께 합니다.

- 차세대 방화벽 및 웹 응용 프로그램 방화벽 솔루션 (Azure Sentinel [설명서](https://docs.microsoft.com/azure/sentinel/connect-data-sources))
- 일반적인 이벤트 형식 (CEF)를 지 원하는 보안 솔루션 통합 (Azure Sentinel [설명서](https://docs.microsoft.com/azure/sentinel/connect-common-event-format))
- Microsoft Advanced Threat Analytics (Azure Sentinel [설명서](https://docs.microsoft.com/azure/sentinel/connect-azure-atp))
- Azure AD Id 보호 (Azure Sentinel [설명서](https://docs.microsoft.com/azure/sentinel/connect-azure-ad-identity-protection))

사용 중단, 다음 사용자 됩니다 새로 추가 하거나 UI와 API를 둘 다에서 위에서 언급 한 형식의 연결 된 기존 솔루션을 수정할 수 있습니다.
기존 연결 된 솔루션 Azure Sentinel이 기간의 끝으로 이동 하는 것이 좋습니다.

![보안 솔루션 센터][16]

## 보안 정책에 대 한 보안 구성 편집<a name="menu_securityconfigurations"></a>
Azure Security Center는 방화벽, 감사, 암호 정책 등과 관련된 규칙을 포함하여 OS를 강화하기 위한 [150여 개의 권장 규칙](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335)을 적용하여 보안 구성을 모니터링합니다. 컴퓨터에 취약한 구성이 있는 것으로 확인되면 Security Center에서 보안 권장 사항이 생성됩니다. 합니다 [보안 구성 편집 화면](https://docs.microsoft.com/azure/security-center/security-center-customize-os-security-config) 고객은 Security Center에서 기본 OS 보안 구성을 사용자 지정할 수 있습니다.

이 기능은 미리 보기로 제공에서 되었고 사용이 중지 됩니다.

![보안 구성 편집][17]

### <a name="edit-security-configurations---new-experience"></a>새 환경 보안 구성 편집

Security Center는 지원 합니다 [게스트 내 에이전트](https://docs.microsoft.com/azure/governance/policy/concepts/guest-configuration) 가까운 미래에 보다 다양 한 기능 집합을-추가 운영 체제 및 게스트 구성 (게스트 내 Azure 정책 사용 하 여와 통합에 대 한 지원을 비롯 하 여 허용 정책)입니다. 이렇게 하면 규모에서 제어 하 고 새 리소스에 자동으로 적용 하는 기능을 제공 됩니다.

## Log Analytics 작업 영역에 대 한 보안 및 감사 대시보드 (원래 OMS 포털에서 사용)<a name="menu_securityomsdashboard"></a>

Log analytics의 보안 대시보드에 주목할 만한 보안 이벤트 및 위협, 위협 인텔리전스 맵 및 작업 영역에 저장 하는 보안 이벤트의 Id 및 액세스 평가 당 작업 영역 개요를 제공 합니다. 대시보드는 앞으로 제거 됩니다. 이미 대시보드 UI의에서 권장 했습니다 사용자에 게 시켜야 하는 Azure Security Center를 사용 하 여 앞입니다.

![로그 분석 보안 대시보드][18]

### <a name="security--audit-dashboard---new-experience"></a>보안 및 감사 대시보드-새로운 환경
여러 구독 및 그와 관련 된 다양 한 기능 집합을 함께 작업 영역에서 동일한 보안 개요를 제공 하는 Azure Security Center를 사용 하는 고객에 게 사용 하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계
- [Azure Security Center](https://docs.microsoft.com/azure/security-center/)에 대한 자세한 정보
- 자세한 내용은 [Azure Sentinel](https://docs.microsoft.com/azure/sentinel)

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
