---
title: Azure Application Insights에서 Search 사용 | Microsoft Docs
description: 웹앱에서 전송된 원시 원격 분석을 검색하고 필터링합니다.
ms.topic: conceptual
ms.date: 07/30/2019
ms.openlocfilehash: 8039a55784f63030f330d6c1e2061e99b8b63bbf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275985"
---
# <a name="using-search-in-application-insights"></a>Application Insights에서 Search 사용

Search는 페이지 보기, 예외 사항 또는 웹 요청과 같은 개별 원격 분석 항목을 찾고 검색하는 데 사용하는 [Application Insights](../../azure-monitor/app/app-insights-overview.md)의 기능입니다. 또한 코딩한 로그 추적 및 이벤트를 볼 수 있습니다.

(사용자 데이터에 비해 좀 더 복잡한 쿼리를 위해서는 [Analytics](../../azure-monitor/log-query/get-started-portal.md)를 사용합니다.)

## <a name="where-do-you-see-search"></a>Search 기능은 어디에 있나요?

### <a name="in-the-azure-portal"></a>Azure 포털에서 다음을 수행합니다.

응용 프로그램의 응용 프로그램 인사이트 개요 탭(상단 표시줄에 있음) 또는 왼쪽의 조사 아래에서 진단 검색을 열 수 있습니다.

![검색 탭](./media/diagnostic-search/view-custom-events.png)

이벤트 유형의 드롭다운 메뉴로 이동하여 원격 분석 항목-서버 요청, 페이지 보기, 코딩한 사용자 지정 이벤트 목록을 확인합니다. 결과 목록 의 맨 위에는 시간에 따른 이벤트 수를 보여주는 요약 차트가 있습니다.

드롭다운 메뉴 또는 새로 고침을 클릭하여 새 이벤트를 가져옵니다.

### <a name="in-visual-studio"></a>Visual Studio에서

Visual Studio에는 Application Insights Search 창도 있습니다. 이 창은 디버그 중인 애플리케이션에 의해 생성된 원격 분석 이벤트를 표시하는 데 가장 유용합니다. 하지만 게시된 앱에서 수집된 이벤트를 Azure Portal에 표시할 수도 있습니다.

Visual Studio에서 Search 창을 엽니다.

![Visual Studio에서 열린 Application Insights 검색](./media/diagnostic-search/32.png)

Search 창은 웹 포털과 비슷한 기능을 제공합니다.

![Visual Studio Application Insights 검색 창](./media/diagnostic-search/34.png)

추적 작업 탭은 요청 또는 페이지 보기를 여는 경우에 사용할 수 있습니다. '연산'은 단일 요청 또는 페이지 보기와 연결된 이벤트의 시퀀스입니다. 예를 들어 종속성 호출, 예외, 추적 로그 및 사용자 지정 이벤트는 단일 연산의 일부일 수 있습니다. 추적 연산 탭에는 요청 또는 페이지 보기와 관련해서 이러한 이벤트의 타이밍 및 기간을 그래픽으로 보여 줍니다.

## <a name="inspect-individual-items"></a>개별 항목 검사

키 필드 및 관련 항목을 보려면 원격 분석 항목을 선택합니다.

![개별 종속성 요청 스크린샷](./media/diagnostic-search/telemetry-item.png)

이렇게 하면 엔드투엔드 트랜잭션 세부 정보 보기가 시작됩니다.

## <a name="filter-event-types"></a>이벤트 유형 필터링

이벤트 유형의 드롭다운 메뉴를 열고 표시할 이벤트 유형을 선택합니다. 나중에 필터를 복원하려면 재설정을 클릭합니다.

이벤트 유형은 다음과 같습니다.

* **Trace** - TrackTrace, log4Net, NLog 및 System.Diagnostic.Trace 호출을 포함한[추적 진단 로그를](../../azure-monitor/app/asp-net-trace-logs.md) 추적합니다.
* **요청** - 페이지, 스크립트, 이미지, 스타일 파일 및 데이터를 포함하는 서버 애플리케이션이 수신하는 HTTP 요청입니다. 이러한 이벤트는 요청을 만들고 개요 차트에 응답하는 데 사용됩니다.
* **웹** - 클라이언트에서 보낸 페이지 보기[원격 분석,](../../azure-monitor/app/javascript.md)페이지 보기 보고서를 만드는 데 사용 됩니다.
* **사용자 지정 이벤트** - [사용량을 모니터링](../../azure-monitor/app/api-custom-events-metrics.md)하기 위해 TrackEvent()에 호출을 삽입한 경우 여기에서 검색할 수 있습니다.
* **예외** - [서버에서](../../azure-monitor/app/asp-net-exceptions.md)catch되지 않은 예외 및 TrackException()을 사용하여 기록한 예외입니다.
* **종속성** - REST API 또는 데이터베이스와 같은 다른 서비스에[서버 응용 프로그램에서](../../azure-monitor/app/asp-net-dependencies.md) 호출 및 AJAX 클라이언트 [코드에서](../../azure-monitor/app/javascript.md)호출 합니다.
* **가용성** - [가용성 테스트](../../azure-monitor/app/monitor-web-app-availability.md)의 결과입니다.

## <a name="filter-on-property-values"></a>속성 값에서 필터링

해당 속성 값에서 이벤트를 필터링할 수 있습니다. 사용 가능한 속성은 선택한 이벤트 유형에 따라 다릅니다. 필터 아이콘을 클릭합니다. ![필터 아이콘](./media/diagnostic-search/filter-icon.png) 을 사용하여 시작합니다.

특정 속성 값을 선택하지 않는 것은 모든 값을 선택하는 것과 동일한 효과가 있습니다. 해당 속성에서 필터링을 전한합니다.

필터 값의 오른쪽에 있는 수는 현재 필터링된 집합에서 발생한 수를 보여줍니다.

## <a name="find-events-with-the-same-property"></a>동일한 속성이 있는 이벤트 찾기

속성 값이 같은 모든 항목을 찾으려면 검색 모음에 입력하거나 필터 탭의 속성을 살펴볼 때 확인란을 클릭합니다.

![필터 탭에서 속성의 확인란을 클릭합니다.](./media/diagnostic-search/filter-property.png)

## <a name="search-the-data"></a>데이터 검색

> [!NOTE]
> 더 복잡한 쿼리를 작성하려면 검색 블레이드 상단에서 [**로그(분석)를**](../../azure-monitor/log-query/get-started-portal.md) 엽니다.
>

속성 값 중 하나에서 용어를 검색할 수 있습니다. 속성 값을 사용 하 여 [사용자 지정 이벤트를](../../azure-monitor/app/api-custom-events-metrics.md) 작성 하는 경우에 유용 합니다.

더 짧은 시간 범위로 검색하는 것이 더 빠른 것처럼 시간 범위를 설정하고자 할 수 있습니다.

![진단 검색 열기](./media/diagnostic-search/search-property.png)

하위 문자열이 아닌 전체 단어를 검색합니다. 인용 부호를 사용하여 특수 문자를 묶습니다.

| String | 찾을 수 *없습니다.* | 있음 |
| --- | --- | --- |
| HomeController.About |`home`<br/>`controller`<br/>`out` | `homecontroller`<br/>`about`<br/>`"homecontroller.about"`|
|미국|`Uni`<br/>`ted`|`united`<br/>`states`<br/>`united AND states`<br/>`"united states"`

다음은 사용할 수 있는 검색 식입니다.

| 샘플 쿼리 | 영향 |
| --- | --- |
| `apple` |지정된 시간 범위의 필드에 단어 "apple"이 포함된 모든 이벤트를 찾습니다. |
| `apple AND banana` <br/>`apple banana` |두 단어를 모두 포함하는 이벤트를 찾습니다. "and"가 아닌 대문자 "AND"를 사용하세요. <br/>약식입니다. |
| `apple OR banana` |둘 중 한 단어를 포함하는 이벤트를 찾습니다. "or"가 아닌 "OR"을 사용하세요. |
| `apple NOT banana` |한 단어는 포함하지만 다른 단어는 포함하지 않는 이벤트를 찾습니다. |

## <a name="sampling"></a>샘플링

앱에서 많은 양의 원격 분석을 생성하는 경우(ASP.NET SDK 버전 2.0.0-beta3 이상)을 사용하는 경우 적응 샘플링 모듈은 이벤트의 대표적인 부분만 전송하여 포털로 전송되는 볼륨을 자동으로 줄입니다. 그러나, 동일한 요청과 관련된 이벤트가 그룹으로 선택되거나 선택 취소되므로 관련 이벤트 간을 이동할 수 있습니다.

[샘플링에 대해 알아봅니다](../../azure-monitor/app/sampling.md).

## <a name="create-work-item"></a>작업 항목 만들기

원격 분석 항목의 세부 정보를 이용하여 GitHub 또는 Azure DevOps에서 버그를 만들 수 있습니다.

모든 원격 분석 항목을 클릭하여 종단 간 트랜잭션 세부 정보 보기로 이동한 다음 **작업 항목 만들기를**선택합니다.

![새 작업 항목을 클릭하고, 필드를 편집한 다음 확인을 클릭합니다.](./media/diagnostic-search/work-item.png)

처음 이 작업을 할 때는 Azure DevOps 조직과 프로젝트 링크를 구성하라는 메시지가 나타납니다.

(작업 항목 탭에서 링크를 구성할 수도 있습니다.)

## <a name="send-more-telemetry-to-application-insights"></a>Application Insights에 더 많은 원격 분석 전송

Application Insights SDK에서 보낸 기본 원격 분석 외에도 다음을 수행할 수 있습니다.

* [.NET](../../azure-monitor/app/asp-net-trace-logs.md) 또는 [Java](../../azure-monitor/app/java-trace-logs.md)의 즐겨찾는 로깅 프레임워크에서 로그 추적을 캡처합니다. 이는 로그 추적을 통해 검색하고 페이지 보기, 예외 사항 및 기타 이벤트와 상관 관계를 지정할 수 있음을 의미합니다.
* [코드를 작성](../../azure-monitor/app/api-custom-events-metrics.md)하여 사용자 지정 이벤트, 페이지 보기 및 예외 사항을 보냅니다.

[Application Insights에 로그 및 사용자 지정 원격 분석을 보내는 방법에 대해 알아봅니다](../../azure-monitor/app/asp-net-trace-logs.md).

## <a name="q--a"></a><a name="questions"></a>질문 및 답변

### <a name="how-much-data-is-retained"></a><a name="limits"></a>얼마나 많은 데이터가 보존되나요?

[제한 요약](../../azure-monitor/app/pricing.md#limits-summary)을 참조하세요.

### <a name="how-can-i-see-post-data-in-my-server-requests"></a>내 서버 요청에서 게시 데이터를 어떻게 볼 수 있나요?

게시 데이터를 자동으로 기록하지는 않지만 [TrackTrace 또는 로그 호출](../../azure-monitor/app/asp-net-trace-logs.md)을 사용할 수 있습니다. 메시지 매개 변수에 게시 데이터를 넣습니다. 속성을 필터링할 수 있는 동일한 방법으로 메시지에서 필터링할 수는 없지만 크기 제안은 더 깁니다.

## <a name="next-steps"></a><a name="add"></a>다음 단계

* [분석에서 복잡한 쿼리 작성](../../azure-monitor/log-query/get-started-portal.md)
* [Application Insights에 로그 및 사용자 지정 원격 분석 전송](../../azure-monitor/app/asp-net-trace-logs.md)
* [가용성 및 응답성 테스트 설정](../../azure-monitor/app/monitor-web-app-availability.md)
* [문제 해결](../../azure-monitor/app/troubleshoot-faq.md)
