---
title: Log Analytics FAQ | Microsoft Docs
description: Azure Log Analytics 서비스에 대해 자주 묻는 질문에 대한 답변입니다.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ad536ff7-2c60-4850-a46d-230bc9e1ab45
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: magoedte
ms.openlocfilehash: 08e915354df4f4aa1d9a183e78cbad47460b8d37
ms.sourcegitcommit: 8e76be591034b618f5c11f4e66668f48c090ddfd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2019
ms.locfileid: "66356220"
---
# <a name="log-analytics-faq"></a>Log Analytics FAQ

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Microsoft FAQ는 Microsoft Azure의 Log Analytics에 대해 자주 묻는 질문의 목록입니다. Log Analytics에 대한 추가 질문이 있으면 [토론 포럼](https://social.msdn.microsoft.com/Forums/azure/home?forum=opinsights)으로 이동하여 질문을 게시하세요. 자주 묻는 질문일 경우 빠르고 쉽게 찾을 수 있도록 이 문서에 추가하겠습니다.


## <a name="new-logs-experience"></a>새로운 로그 환경

### <a name="q-whats-the-difference-between-the-new-logs-experience-and-log-analytics"></a>Q: 새로운 로그 환경과 Log Analytics의 차이점이 무엇인가요?

A: A: 동일합니다. [Log Analytics는 Azure Monitor의 기능으로 통합되어](../../azure-monitor/azure-monitor-rebrand.md) 보다 통합된 모니터링 환경을 제공합니다. Azure Monitor의 새로운 로그 환경은 많은 고객이 이미 사용하고 있는 Log Analytics 쿼리와 정확히 같습니다.

### <a name="q-can-i-still-use-log-search"></a>Q: 로그 검색을 계속 사용할 수 있나요? 

A: A: 로그 검색은 현재 OMS 포털과 Azure Portal에서 **로그(클래식)** 라는 이름으로 계속 제공됩니다. OMS 포털은 2019년 1월 15일에 공식적으로 사용이 중지됩니다. Azure Portal의 클래식 로그 환경은 점차적으로 사용이 중지되고 새로운 로그 환경으로 대체될 예정입니다. 

### <a name="q-can-i-still-use-advanced-analytics-portal"></a>Q. 고급 분석 포털을 계속 사용할 수 있나요? 
Azure Portal의 새로운 로그 환경은 고급 분석 포털을 기반으로 하지만 Azure Portal 외부에서 여전히 액세스할 수 있습니다. 이 외부 포털의 사용 중지에 대한 로드맵은 곧 발표될 예정입니다.

### <a name="q-why-cant-i-see-query-explorer-and-save-buttons-in-the-new-logs-experience"></a>Q. 새 로그 환경에 쿼리 탐색기와 저장 단추가 표시되지 않는 이유는 무엇인가요?

특정 리소스의 컨텍스트에서 로그를 탐색할 때는 **쿼리 탐색기**, **저장** 및 **경고 설정** 단추를 사용할 수 없습니다. 경고를 생성하거나, 쿼리를 저장하거나 로드하려면 로그의 범위를 작업 영역으로 지정해야 합니다. 작업 영역 컨텍스트에서 로그를 열려면 **모든 서비스** > **모니터** > **로그**를 선택합니다. 마지막으로 사용한 작업 영역이 선택되지만 다른 작업 영역을 선택할 수 있습니다. 자세한 내용은 [Log Analytics에서 데이터 확인 및 분석](../log-query/portals.md)을 참조하세요.

### <a name="q-how-do-i-extract-custom-fields-in-the-new-logs-experience"></a>Q. 새 로그 환경에서 사용자 지정 필드를 추출하려면 어떻게 하나요? 

A: 사용자 지정 필드 추출은 현재 클래식 로그 환경에서 지원됩니다. 

### <a name="q-where-do-i-find-list-view-in-the-new-logs"></a>Q. 새 로그의 목록 보기는 어디에 있나요? 

A: 새 로그에서는 목록 보기를 사용할 수 없습니다. 결과 테이블의 각 레코드 왼쪽에 화살표가 있습니다. 특정 레코드에 대한 세부 정보를 열려면 이 화살표를 클릭합니다. 

### <a name="q-after-running-a-query-a-list-of-suggested-filters-are-available-how-can-i-see-filters"></a>Q. 쿼리를 실행하면 추천 필터 목록이 제공됩니다. 필터를 확인하려면 어떻게 해야 하나요? 

A: 왼쪽 창에서 ‘필터’를 클릭하면 새 필터 구현의 미리 보기가 표시됩니다. 이 기능은 현재 UI의 레코드 한도가 10,000개로 제한되는 대신 전체 결과 집합을 기반으로 합니다. 현재 가장 많이 사용되는 필터 목록과 각 필터의 가장 일반적인 10가지 값입니다. 

### <a name="q-why-am-i-getting-the-error-register-resource-provider-microsoftinsights-for-this-subscription-to-enable-this-query-in-logs-after-drilling-in-from-vm"></a>Q. VM에서 드릴다운한 후 로그에  VM에서 드릴다운한 후에 로그에 "이 구독에 대한 리소스 공급 기업 'Microsoft.Insights'를 등록하여 이 쿼리를 사용하도록 설정" 

A: 기본적으로 대부분 리소스 공급자는 자동으로 등록되지만 일부 리소스 공급자는 수동으로 등록해야 합니다. 이렇게 하면 구독이 리소스 공급자에서 작동하도록 구성됩니다. 등록 범위는 항상 해당 구독입니다. 자세한 내용은 [리소스 공급자 및 형식](../../azure-resource-manager/resource-manager-supported-services.md#azure-portal)을 참조하세요.

### <a name="q-why-am-i-am-getting-no-access-error-message-when-accessing-logs-from-a-vm-page"></a>Q. VM 페이지에서 로그에 액세스할 때 액세스 오류 메시지가 표시되지 않는 이유는 무엇인가요? 

A: VM 로그를 보려면 VM 로그를 저장하는 작업 영역에 대한 읽기 권한을 부여 받아야 합니다. 이런 경우 관리자가 Azure에서 내게 권한을 부여해 줘야 합니다.

### <a name="q-why-can-i-can-access-my-workspace-in-oms-portal-but-i-get-the-error-you-have-no-access-in-the-azure-portal"></a>Q. OMS 포털에서 내 작업 영역에 액세스할 수 있는데 Azure Portal에서 “액세스할 수 없습니다” 오류가 표시되는 이유가 무언인가요?  

A: Azure에서 작업 영역에 액세스하려면 Azure 권한을 할당 받아야 합니다. 적절한 액세스 권한이 없는 경우가 몇 가지 있습니다. 이런 경우에는 관리자가 Azure에서 내게 권한을 부여해야 합니다. 자세한 내용은 [Azure로 이동하는 OMS 포털](oms-portal-transition.md)을 참조하세요.

### <a name="q-why-cant-i-cant-see-view-designer-entry-in-logs"></a>Q. 로그에서 보기 디자이너 항목을 볼 수 없는 이유는 무엇인가요? 
A: 보기 디자이너는 로그에서 기여자 권한 이상이 할당된 사용자만 사용할 수 있습니다.

### <a name="q-can-i-still-use-the-analytics-portal-outside-of-azure"></a>Q. Azure 외부의 Analytics 포털을 계속 사용할 수 있나요?
a. 예, Azure의 로그 페이지와 고급 분석 포털은 동일한 코드를 기반으로 합니다. Log Analytics는 Azure Monitor의 기능으로 통합되어 보다 통합된 모니터링 환경을 제공합니다. URL을 사용 하 여 Analytics 포털에 액세스할 수 있습니다: https:\/\/portal.loganalytics.io/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/workspaces/{workspaceName}.



## <a name="general"></a>일반

### <a name="q-how-can-i-see-my-views-and-solutions-in-azure-portal"></a>Q. Azure Portal에서 내 보기와 솔루션은 어떻게 볼 수 있나요? 

A: 보기 및 설치된 솔루션 목록은 Azure Portal에서 사용할 수 있습니다. **모든 서비스**를 클릭합니다. 리소스 목록에서 **모니터**를 선택한 다음, **...추가**를 클릭합니다. 마지막으로 사용한 작업 영역이 선택되지만 다른 작업 영역을 선택할 수 있습니다. 

### <a name="q-why-i-cant-create-workspaces-in-west-central-us-region"></a>Q. 미국 중서부 지역에 작업 영역을 만들 수 없는 이유가 무엇인가요? 

A: 이 지역은 임시 용량 한도에 도달했습니다. 이 한도 2019 년 9 월의 끝에서 처리할 예정 되어 있습니다.


### <a name="q-does-log-analytics-use-the-same-agent-as-azure-security-center"></a>Q. Log Analytics는 Azure Security Center와 같은 에이전트를 사용합니까?

A: 2017년 6월 초에 Azure Security Center는 Microsoft Monitoring Agent를 사용하여 데이터를 수집하고 저장합니다. 자세한 내용은 [Azure Security Center 플랫폼 마이그레이션 FAQ](../../security-center/security-center-enable-data-collection.md)를 참조하세요.

### <a name="q-what-checks-are-performed-by-the-ad-and-sql-assessment-solutions"></a>Q. AD 및 SQL 평가 솔루션에서 수행하는 검사는 무엇입니까?

A: 다음 쿼리는 현재 수행하는 모든 검사에 대한 설명을 보여 줍니다.

```
(Type=SQLAssessmentRecommendation OR Type=ADAssessmentRecommendation) | dedup RecommendationId | select FocusArea, ActionArea, Recommendation, Description | sort Type, FocusArea,ActionArea, Recommendation
```

추가 검토를 위해 결과를 Excel로 내보낼 수 있습니다.

### <a name="q-why-do-i-see-something-different-than-oms-in-the-system-center-operations-manager-console"></a>Q. System Center Operations Manager 콘솔에 OMS와 다른 내용이 표시되는 이유는 무엇인가요?

A: 사용 중인 Operations Manager의 업데이트 롤업에 따라 *System Center Advisor*, *Operational Insights* 또는 *Log Analytics*에 대한 노드가 표시될 수 있습니다.

*OMS* 에 대한 텍스트 문자열 업데이트가 수동으로 가져와야 하는 관리 팩에 포함됩니다. 현재 텍스트 및 기능을 보려면 최신 System Center Operations Manager 업데이트 롤업 기술 자료 문서의 지침을 따르고 콘솔을 새로 고칩니다.

### <a name="q-is-there-an-on-premises-version-of-log-analytics"></a>Q: Log Analytics의 온-프레미스 버전이 있나요?

A:  아니요. Log Analytics는 대량의 데이터를 처리 및 저장하는 확장성 있는 클라우드 서비스입니다. 

### <a name="q-how-do-i-troubleshoot-if-log-analytics-is-no-longer-collecting-data"></a>Q. Log Analytics에서 더 이상 데이터를 수집하지 않는 경우 어떻게 해결하나요?

A: 2018년 4월 2일 이전에 생성된 구독 및 작업 영역이 *무료* 가격 책정 계층에 있는 경우 하루에 500MB를 초과하는 데이터가 전송되면 그 날 나머지 시간 동안은 데이터 수집을 중지합니다. 일일 한도에 도달하는 것은 Log Analytics가 데이터 수집을 중지하고 데이터가 사라진 것처럼 표시되는 일반적인 이유입니다.  

Log Analytics는 *Heartbeat* 형식의 이벤트를 만들며, 데이터 수집이 중지되었는지 여부를 확인하는 데 사용할 수 있습니다. 

일일 한도에 도달하고 데이터 누락이 있는지 확인하려면 검색에서 다음 쿼리를 실행합니다. `Heartbeat | summarize max(TimeGenerated)`

특정 컴퓨터를 확인하려면 다음 쿼리를 실행합니다. `Heartbeat | where Computer=="contosovm" | summarize max(TimeGenerated)`

선택한 시간 범위에 따라 데이터 수집이 중지되면 반환되는 모든 레코드가 표시되지 않습니다.   

다음 표에서 데이터 수집을 중지하는 이유 및 데이터 수집을 다시 시작하는 권장되는 작업을 설명합니다.

| 데이터 수집 중지 이유                       | 데이터 수집을 다시 시작하려면 |
| -------------------------------------------------- | ----------------  |
| 무료 데이터의 한도 도달<sup>1</sup>       | 수집이 다음 달에 자동으로 다시 시작될 때까지 대기 또는<br> 유료 가격 책정 계층으로 변경 |
| Azure 구독이 다음으로 인해 일시 중단된 상태: <br> 평가판 종료 <br> Azure 암호 만료 <br> 월별 지출 한도 도달(예: MSDN 또는 Visual Studio 구독에서)                          | 유료 구독으로 전환 <br> 유료 구독으로 전환 <br> 한도 제거 또는 한도가 재설정될 때까지 대기 |

<sup>1</sup> 작업 영역이 *무료* 가격 책정 계층에 있는 경우 일당 500MB의 데이터를 서비스에 전송하도록 제한됩니다. 일일 한도에 도달하면 데이터 수집이 다음 날까지 중지됩니다. 데이터 수집이 중지되는 동안 전송된 데이터가 인덱싱되지 않으며 검색에 사용할 수 없습니다. 데이터 수집이 다시 시작되면 전송된 새 데이터에 대해서만 프로세스가 발생합니다. 

Log Analytics는 UTC 시간을 사용하고 매일 UTC 자정에 시작됩니다. 작업 영역이 일일 한도에 도달하면 다음 UTC 날의 처음 한 시간 동안 프로세스가 다시 시작됩니다.

### <a name="q-how-can-i-be-notified-when-data-collection-stops"></a>Q. 데이터 수집이 중지될 때 알림을 받을 수 있는 방법은 무엇인가요?

A: [새 로그 경고 만들기](../../azure-monitor/platform/alerts-metric.md)에서 설명한 단계를 사용하여 데이터 수집이 중지될 때 알림을 받을 수 있습니다.

데이터 수집이 중단되는 경우에 대한 경고를 만드는 시기는 다음을 설정합니다.

- **경고 조건 정의**는 리소스 대상으로 Log Analytics 작업 영역을 지정합니다.
- **경고 조건**은 다음을 지정합니다.
   - **신호 이름**은 **사용자 지정 로그 검색**을 선택합니다.
   - **쿼리 검색**을 `Heartbeat | summarize LastCall = max(TimeGenerated) by Computer | where LastCall < ago(15m)`으로
   - **경고 논리**는 *결과 수*에 **기반**하고 **조건**은 *0*의 **임계값**을 *초과*합니다.
   - *30*분의 **시간 범위** 및 매 *10*분의 **경고 주기**
- **경고 세부 정보 정의**는 다음을 지정합니다.
   - **이름**을 *데이터 수집 중지됨*으로
   - **심각도**를 *경고*로

로그 경고가 조건과 일치하도록 기존 항목을 지정하거나 새 [작업 그룹](../../azure-monitor/platform/action-groups.md)을 만들면 15분 초과 하트비트가 누락된 경우 사용자에게 알려줍니다.

## <a name="configuration"></a>구성
### <a name="q-can-i-change-the-name-of-the-tableblob-container-used-to-read-from-azure-diagnostics-wad"></a>Q. Azure Diagnostics(WAD)에서 읽어오는 데 사용되는 테이블/Blob 컨테이너의 이름을 변경할 수 있나요?

a. 아니요, 현재 Azure 저장소의 임의 테이블 또는 컨테이너에서 읽을 수 없습니다.

### <a name="q-what-ip-addresses-does-the-log-analytics-service-use-how-do-i-ensure-that-my-firewall-only-allows-traffic-to-the-log-analytics-service"></a>Q. Log Analytics 서비스에서 사용하는 IP 주소는 무엇인가요? 내 방화벽에서 Log Analytics 서비스에 대한 트래픽만 허용하도록 하려면 어떻게 하나요?

a. Log Analytics 서비스는 Azure를 기반으로 빌드됩니다. Log Analytics IP 주소는 [Microsoft Azure 데이터 센터 IP 범위](https://www.microsoft.com/download/details.aspx?id=41653)에 있습니다.

서비스 배포가 수행되면서 Log Analytics 서비스의 실제 IP 주소가 변경됩니다. 방화벽을 통과하도록 허용하는 DNS 이름은 [네트워크 요구 사항](../../azure-monitor/platform/log-analytics-agent.md#network-firewall-requirements)에 문서화됩니다.

### <a name="q-i-use-expressroute-for-connecting-to-azure-does-my-log-analytics-traffic-use-my-expressroute-connection"></a>Q. ExpressRoute를 사용하여 Azure에 연결합니다. Log Analytics 트래픽이 내 ExpressRoute 연결을 사용하나요?

a. 여러 유형의 ExpressRoute 트래픽이 [ExpressRoute 설명서](../../expressroute/expressroute-faqs.md#supported-services)에 나와 있습니다.

Log Analytics에 대한 트래픽은 공용 피어링 ExpressRoute 회로를 사용합니다.

### <a name="q-is-there-a-simple-and-easy-way-to-move-an-existing-log-analytics-workspace-to-another-log-analytics-workspaceazure-subscription"></a>Q. 기존 Log Analytics 작업 영역을 다른 Log Analytics 작업 영역/Azure 구독으로 이동할 간단하고 쉬운 방법이 있나요?

a. `Move-AzResource` cmdlet을 사용하면 Log Analytics 작업 영역을 이동할 수 있으며 한 Azure 구독에서 다른 구독으로 Automation 계정도 이동할 수 있습니다. 자세한 내용은 [이동 AzResource](https://msdn.microsoft.com/library/mt652516.aspx)합니다.

이러한 변경은 Azure 포털에서 이루어집니다.

데이터를 Log Analytics 작업 영역 간에 이동하거나 Log Analytics 데이터가 저장된 지역을 변경할 수 없습니다.

### <a name="q-how-do-i-add-log-analytics-to-system-center-operations-manager"></a>Q: Log Analytics를 System Center Operations Manager에 추가하려면 어떻게 할까요?

A:  최신 업데이트 롤업으로 업데이트 및 관리 팩 가져오기를 통해 Operations Manager를 Log Analytics에 연결할 수 있습니다.

>[!NOTE]
>Log Analytics에 Operations Manager 연결은 System Center Operations Manager 2012 SP1 이상에서만 사용할 수 있습니다.

### <a name="q-how-can-i-confirm-that-an-agent-is-able-to-communicate-with-log-analytics"></a>Q: 에이전트가 Log Analytics와 통신할 수 있는지 어떻게 확인하나요?

A: 에이전트가 OMS 서비스와 통신할 수 있는지 확인하려면 제어판, 보안 및 설정, **Microsoft Monitoring Agent**로 이동합니다.

**Azure Log Analytics(OMS)** 탭 아래에서 녹색 확인 표시를 찾습니다. 녹색 확인 표시 아이콘은 에이전트가 Azure 서비스와 통신할 수 있음을 확인합니다.

노란색 경고 아이콘은 에이전트가 Log Analytics와 통신하는 데 문제가 있음을 의미합니다. 한 가지 일반적인 이유는 Microsoft Monitoring Agent 서비스가 중지되었기 때문입니다. 서비스 제어 관리자를 사용하여 서비스를 다시 시작합니다.

### <a name="q-how-do-i-stop-an-agent-from-communicating-with-log-analytics"></a>Q: 에이전트가 Log Analytics와 통신을 중지하도록 하려면 어떻게 하나요?

A: System Center Operations Manager의 OMS 관리 컴퓨터 목록에서 컴퓨터를 제거합니다. Operations Manager는 Log Analytics에 더 이상 보고하지 않도록 에이전트의 구성을 업데이트합니다. Log Analytics에 직접 연결된 에이전트인 경우 제어판, 보안 및 설정, **Microsoft Monitoring Agent**로 이동합니다.
**Azure Log Analytics(OMS)** 아래에서 나열된 모든 작업 영역을 제거합니다.

### <a name="q-why-am-i-getting-an-error-when-i-try-to-move-my-workspace-from-one-azure-subscription-to-another"></a>Q: 내 작업 영역을 특정 Azure 구독에서 다른 구독으로 이동하려고 하는 경우 오류가 발생하는 이유는 무엇인가요?

A: 작업 영역을 다른 구독 또는 리소스 그룹으로 이동하려면 먼저 작업 영역에서 Automation 계정을 연결 해제해야 합니다. 작업 영역에 솔루션이 설치되어 있는 경우 Automation 계정을 연결 해제하려면 이 솔루션을 제거해야 합니다. 업데이트 관리, 변경 내용 추적 또는 작업 시간 외 VM 시작/중지가 제거됩니다. 이러한 솔루션을 제거한 후, Automation 계정 리소스의 왼쪽 창에서 **연결된 작업 영역**을 선택하고 리본에서 **작업 영역 연결 해제**를 클릭하여 Automation 계정을 연결 해제합니다.
 > 이동 후에는 제거된 솔루션을 작업 영역에 다시 설치하고 작업 영역에 Automation을 다시 연결해야 합니다.

두 Azure 구독에서 권한을 갖는지 확인합니다.

### <a name="q-why-am-i-getting-an-error-when-i-try-to-update-a-savedsearch"></a>Q: SavedSearch를 업데이트하려고 하면 오류가 발생하는 이유는 무엇인가요?

A: API 본문에 ‘etag’ 또는 Azure Resource Manager 템플릿 속성을 추가해야 합니다.
```
"properties": {
   "etag": "*",
   "query": "SecurityEvent | where TimeGenerated > ago(1h) | where EventID == 4625 | count",
   "displayName": "An account failed to log on",
   "category": "Security"
}
```

## <a name="agent-data"></a>에이전트 데이터
### <a name="q-how-much-data-can-i-send-through-the-agent-to-log-analytics-is-there-a-maximum-amount-of-data-per-customer"></a>Q. 에이전트를 통해 Log Analytics로 얼마나 많은 데이터를 보낼 수 있나요? 고객 한 명당 최대 데이터 용량이 있나요?
a. 무료 요금제에서는 작업 영역당 일일 용량을 500MB로 설정합니다. 표준 및 프리미엄 요금제에는 업로드되는 데이터 양에 제한이 없습니다. 클라우드 서비스로 Log Analytics는 고객의 데이터 볼륨(일일 테라바이트 단위까지)을 처리하도록 자동 강화되도록 설계되었습니다.

Log Analytics 에이전트는 작은 공간을 갖도록 설계되었습니다. 데이터 볼륨은 사용하도록 설정한 솔루션에 따라 다릅니다. [사용량](../../azure-monitor/platform/data-usage.md) 페이지에서 데이터 볼륨에 대한 자세한 정보를 확인하고 솔루션별로 정리된 내용을 볼 수 있습니다.

자세한 내용은 OMS 에이전트의 리소스 사용률(공간)을 평가한 후 해당 결과를 보여주는 [고객 블로그](https://thoughtsonopsmgr.blogspot.com/2015/09/one-small-footprint-for-server-one.html)를 참조할 수 있습니다.

### <a name="q-how-much-network-bandwidth-is-used-by-the-microsoft-management-agent-mma-when-sending-data-to-log-analytics"></a>Q. 데이터를 Log Analytics로 전송할 때 Microsoft Management Agent(MMA)에 사용된 네트워크 대역폭은 얼마나 되나요?

a. 대역폭은 전송된 데이터 양에 대한 기능입니다. 네트워크를 통해 데이터가 전송되는 동안 데이터가 압축됩니다.

### <a name="q-how-much-data-is-sent-per-agent"></a>Q. 에이전트당 얼마나 많은 데이터가 전송되나요?

a. 에이전트당 전송되는 데이터의 양에 따라 달라집니다.

* 사용하도록 설정한 솔루션
* 수집되는 로그 및 성능 카운터 수
* 로그에 있는 데이터 볼륨

무료 가격 책정 계층은 여러 서버에 등록하고 일반적인 데이터 볼륨을 측정하는 좋은 방법입니다. 전체 사용 현황은 [사용량](../../azure-monitor/platform/data-usage.md) 페이지에 표시됩니다.

WireData 에이전트를 실행할 수 있는 컴퓨터의 경우 다음 쿼리를 사용하여 전송 중인 데이터의 양을 확인합니다.

```
Type=WireData (ProcessName="C:\\Program Files\\Microsoft Monitoring Agent\\Agent\\MonitoringHost.exe") (Direction=Outbound) | measure Sum(TotalBytes) by Computer
```

## <a name="next-steps"></a>다음 단계
* [Log Analytics 시작](../../azure-monitor/overview.md) 에서 Log Analytics에 대한 정보와 Log Analytics를 몇 분 만에 시작 및 실행하는 방법에 대해 알아보세요.
