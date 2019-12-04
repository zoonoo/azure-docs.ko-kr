---
title: Azure Monitor를 사용 하 여 논리 앱 모니터링
description: Azure Monitor 로그를 사용 하 여 문제를 해결 하 고 논리 앱 실행을 진단 하기 위한 정보 및 디버깅 데이터 가져오기
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 08/29/2019
ms.openlocfilehash: 305b50c86a468354f049fcc57fcb79b537e8dfed
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74791905"
---
# <a name="get-insights-and-debugging-data-for-logic-apps-by-using-azure-monitor-logs"></a>Azure Monitor 로그를 사용 하 여 논리 앱에 대 한 통찰력 및 디버깅 데이터 가져오기

논리 앱에 대 한 보다 다양 한 디버깅 정보를 모니터링 하 고 얻으려면 논리 앱을 만들 때 [Azure Monitor 로그](../log-analytics/log-analytics-overview.md) 를 켜 세요. Azure Monitor 로그는 Azure Portal에 Logic Apps 관리 솔루션을 설치할 때 논리 앱에 대 한 진단 로깅 및 모니터링을 제공 합니다. 이 솔루션은 상태, 실행 시간, 다시 제출 상태 및 상관 관계 ID와 같은 특정 세부 정보를 사용하여 논리 앱 실행에 대한 집계 정보를 제공합니다. 이 문서에서는 논리 앱 실행에 대 한 런타임 이벤트 및 데이터를 볼 수 있도록 Azure Monitor 로그를 설정 하는 방법을 보여 줍니다.

이 항목에서는 논리 앱을 만들 때 Azure Monitor 로그를 설정 하는 방법을 보여 줍니다. 기존 논리 앱에 대 한 Azure Monitor 로그를 켜려면 다음 단계를 수행 하 여 [진단 로깅을 설정 하 고 논리 앱 런타임 데이터를 Azure Monitor 로그에 보냅니다](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics).

> [!NOTE]
> 이전에 설명 된이 페이지에서는 Microsoft Operations Management Suite (OMS)를 사용 하 여 이러한 작업을 수행 하는 방법에 대 한 단계를 설명 Log Analytics 했습니다 .이 작업은 [1 월 2019 일에](../azure-monitor/platform/oms-portal-transition.md)사용이 중지 되 고 해당 단계를 [Azure Monitor 로그](../azure-monitor/platform/data-platform-logs.md)로 대체 합니다. 로그 데이터는 여전히 Log Analytics 작업 영역에 저장되며 동일한 Log Analytics 서비스에 의해 계속 수집 및 분석됩니다. 자세한 내용은 [Azure Monitor 용어 변경 내용](../azure-monitor/terminology.md)을 참조 하세요.

## <a name="prerequisites"></a>전제 조건

시작하기 전에 Log Analytics 작업 영역이 필요합니다. [Log Analytics 작업 영역을 만드는 방법](../azure-monitor/learn/quick-create-workspace.md)을 알아봅니다.

## <a name="turn-on-logging-for-new-logic-apps"></a>새 논리 앱에 대 한 로깅 설정

1. [Azure Portal](https://portal.azure.com)에서 논리 앱을 만듭니다. Azure 주 메뉴에서 **리소스 만들기** > **통합** > **논리 앱**을 차례로 선택합니다.

   ![새 논리 앱 만들기](media/logic-apps-monitor-your-logic-apps-oms/create-new-logic-app.png)

1. **논리 앱**아래에서 다음 단계를 수행 합니다.

   1. 논리 앱의 이름을 입력하고 Azure 구독을 선택합니다.

   1. Azure 리소스 그룹을 만들거나 선택합니다. 논리 앱의 위치를 선택 합니다.

   1. **Log Analytics**에서 **켜기**를 선택 합니다.

   1. **Log Analytics 작업 영역** 목록에서 논리 앱 실행의 데이터를 전송 하려는 작업 영역을 선택 합니다.

      ![논리 앱 정보 제공](./media/logic-apps-monitor-your-logic-apps-oms/create-logic-app-details.png)

      이 단계를 마치면 Azure에서 Log Analytics 작업 영역에 연결된 논리 앱이 만들어집니다. 또한이 단계에서는 Logic Apps 관리 솔루션을 작업 영역에 자동으로 설치 합니다.

   1. 완료되면 **만들기**를 선택합니다.

1. 논리 앱 실행을 보려면 [다음 단계로 계속 진행합니다](#view-logic-app-runs-oms).

## <a name="install-logic-apps-management-solution"></a>Logic Apps 관리 솔루션 설치

논리 앱을 만들 때 Azure Monitor 로그를 이미 설정한 경우이 단계를 건너뜁니다. Logic Apps 관리 솔루션이 이미 설치되어 있습니다.

1. [Azure Portal](https://portal.azure.com)에서 **모든 서비스**를 선택합니다. 검색 상자에서 "log analytics 작업 영역"을 찾고 **Log Analytics 작업 영역**을 선택 합니다.

   !["Log Analytics 작업 영역"을 선택 합니다.](./media/logic-apps-monitor-your-logic-apps-oms/find-log-analytics.png)

1. **Log Analytics 작업 영역**에서 작업 영역을 선택 합니다.

   ![Log Analytics 작업 영역 선택](./media/logic-apps-monitor-your-logic-apps-oms/select-log-analytics-workspace.png)

1. 개요 창의 **Log Analytics 시작** > **모니터링 솔루션 구성**에서 **솔루션 보기**를 선택 합니다.

   !["솔루션 보기"를 선택 합니다.](media/logic-apps-monitor-your-logic-apps-oms/log-analytics-workspace.png)

1. **개요**에서 **추가**를 선택 합니다.

   !["추가" 선택](./media/logic-apps-monitor-your-logic-apps-oms/add-logic-apps-management-solution.png)

1. **Marketplace** 가 열리면 검색 상자에 "논리 앱 관리"를 입력 하 고 **Logic Apps 관리**를 선택 합니다.

   ![“Logic Apps 관리” 선택](./media/logic-apps-monitor-your-logic-apps-oms/select-logic-apps-management.png)

1. 솔루션 설명 창에서 **만들기**를 선택 합니다.

   !["Logic Apps 관리"에 대해 "만들기"를 선택 합니다.](./media/logic-apps-monitor-your-logic-apps-oms/create-logic-apps-management-solution.png)

1. 솔루션을 설치 하려는 Log Analytics 작업 영역을 검토 하 고 확인 한 후 **만들기** 를 다시 선택 합니다.

   !["Logic Apps 관리"에 대해 "만들기"를 선택 합니다.](./media/logic-apps-monitor-your-logic-apps-oms/confirm-log-analytics-workspace.png)

   Azure가 Log Analytics 작업 영역을 포함 하는 Azure 리소스 그룹에 솔루션을 배포한 후 솔루션은 작업 영역의 요약 창에 표시 됩니다.

   ![작업 영역 요약 창](./media/logic-apps-monitor-your-logic-apps-oms/workspace-summary-pane-logic-apps-management.png)

<a name="view-logic-app-runs-oms"></a>

## <a name="view-logic-app-run-information"></a>논리 앱 실행 정보 보기

논리 앱 실행 후 **Logic Apps 관리** 타일에서 해당 논리 앱 실행의 상태 및 수를 확인할 수 있습니다.

1. Log Analytics 작업 영역으로 이동 하 고 **작업 영역 요약** > **Logic Apps 관리**를 선택 합니다.

   ![논리 앱 실행 상태 및 수](media/logic-apps-monitor-your-logic-apps-oms/logic-app-runs-summary.png)

   여기에서 논리 앱 실행은 이름이나 실행 상태로 그룹화됩니다. 이 페이지는 논리 앱 실행에 대한 작업 또는 트리거의 오류에 대한 세부 정보도 표시합니다.

   ![논리 앱 실행에 대한 상태 요약](media/logic-apps-monitor-your-logic-apps-oms/logic-app-runs-summary-details.png)

1. 특정 논리 앱 또는 상태에 대한 모든 실행을 보려면 논리 앱 또는 상태에 대한 행을 선택합니다.

   특정 논리 앱에 대한 모든 실행을 보여 주는 예제는 다음과 같습니다.

   ![논리 앱 실행 및 상태 보기](media/logic-apps-monitor-your-logic-apps-oms/logic-app-run-details.png)

   이 페이지에는 다음과 같은 고급 옵션이 있습니다. 

   * **추적 된 속성** 열: 작업 별로 그룹화 된 추적 속성을 설정 하는 논리 앱의 경우이 열에서 해당 속성을 볼 수 있습니다. 이러한 추적 된 속성을 보려면 **보기**를 선택 합니다. 추적된 속성을 검색하려면 열 필터를 사용합니다.

      ![논리 앱에 대해 추적된 속성 보기](media/logic-apps-monitor-your-logic-apps-oms/logic-app-tracked-properties.png)

      추적된 속성이 새로 추가되어 처음으로 표시되는 데 10-15분 정도 걸릴 수 있습니다. [논리 앱에 추적된 속성을 추가하는 방법](logic-apps-monitor-your-logic-apps.md#azure-diagnostics-event-settings-and-details)을 알아보세요.

   * 다시 **전송**: 실패 했거나 성공 했거나 아직 실행 중인 논리 앱 실행을 하나 이상 다시 제출할 수 있습니다. 다시 전송 하려는 실행의 확인란을 선택 하 고 다시 **전송**을 선택 합니다.

     ![논리 앱 실행 다시 제출](media/logic-apps-monitor-your-logic-apps-oms/logic-app-resubmit.png)

1. 결과를 필터링 하기 위해 클라이언트 쪽 및 서버 쪽 필터링을 모두 수행할 수 있습니다.

   * **클라이언트 쪽 필터**: 각 열에 대해 원하는 필터를 선택 합니다. 예를 들면 다음과 같습니다.

     ![예제 열 필터](media/logic-apps-monitor-your-logic-apps-oms/filters.png)

   * **서버 쪽 필터**: 특정 시간 창을 선택 하거나 표시 되는 실행 수를 제한 하려면 페이지 맨 위에 있는 범위 컨트롤을 사용 합니다. 기본적으로 1,000개 레코드가 한 번에 나타납니다.

     ![시간 변경 창](media/logic-apps-monitor-your-logic-apps-oms/change-interval.png)

1. 특정 실행에 대 한 모든 작업과 해당 세부 정보를 보려면 논리 앱 실행에 대 한 행을 선택 합니다.

   특정 논리 앱 실행에 대 한 모든 작업 및 트리거를 보여 주는 예제는 다음과 같습니다.

   ![논리 앱 실행에 대한 작업 보기](media/logic-apps-monitor-your-logic-apps-oms/logic-app-action-details.png)

1. 결과 페이지에서 결과 뒤의 쿼리를 보거나 모든 결과를 표시 하려면 **모두 표시**를 선택 하 여 **로그** 페이지를 엽니다.

   ![모든 결과 보기](media/logic-apps-monitor-your-logic-apps-oms/logic-app-see-all.png)

   **로그** 페이지에서 다음 옵션을 선택할 수 있습니다.

   * 테이블에서 쿼리 결과를 보려면 **테이블**을 선택 합니다.

   * 쿼리에서 [Kusto 쿼리 언어](https://aka.ms/LogAnalyticsLanguageReference)를 사용 하 여 다른 결과를 보려는 경우 편집할 수 있습니다. 쿼리를 변경 하려면 쿼리 문자열을 업데이트 하 고 **실행** 을 선택 하 여 테이블의 결과를 확인 합니다. 

     ![Log Analytics 쿼리 뷰](media/logic-apps-monitor-your-logic-apps-oms/query.png)

## <a name="next-steps"></a>다음 단계

* [B2B 메시지 모니터링](../logic-apps/logic-apps-monitor-b2b-message.md)