---
title: 상태 모니터링, 기록 보기 및 경고 설정
description: 실행 상태를 확인하고, 트리거 기록을 검토하고, Azure Logic Apps에서 경고를 사용하도록 설정하여 논리 앱 문제 해결
services: logic-apps
ms.suite: integration
ms.reviewer: divswa, logicappspm
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: 495877f1c839de2cf3583a37180054c91bd9f139
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76907774"
---
# <a name="monitor-run-status-review-trigger-history-and-set-up-alerts-for-azure-logic-apps"></a>실행 상태를 모니터링하고, 트리거 기록을 검토하고, Azure Logic Apps에 대한 경고를 설정합니다.

논리 [앱을 만들고 실행한](../logic-apps/quickstart-create-first-logic-app-workflow.md)후 논리 앱의 실행 상태, [실행 기록,](#review-runs-history) [트리거 기록](#review-trigger-history)및 성능을 확인할 수 있습니다. 오류 또는 가능한 다른 문제에 대한 알림을 받으려면 [경고](#add-azure-alerts)를 설정합니다. 예를 들어 "한 시간에 5개 이상의 실행이 실패하는 경우"를 검색하는 경고를 만들 수 있습니다.

실시간 이벤트 모니터링 및 풍부한 디버깅을 위해 Azure Monitor 로그를 사용하여 논리 앱에 대한 진단 [로깅을 설정합니다.](../azure-monitor/overview.md) 이 Azure 서비스를 사용하면 클라우드 및 온-프레미스 환경을 모니터링하여 가용성과 성능을 보다 쉽게 유지할 수 있습니다. 그런 다음 트리거 이벤트, 실행 이벤트 및 작업 이벤트와 같은 이벤트를 찾아 볼 수 있습니다. [Azure Monitor 로그에](../azure-monitor/platform/data-platform-logs.md)이 정보를 저장하면 이 정보를 찾고 분석하는 데 도움이 되는 [로그 쿼리를](../azure-monitor/log-query/log-query-overview.md) 만들 수 있습니다. 이 진단 데이터를 Azure 저장소 및 Azure 이벤트 허브와 같은 다른 Azure 서비스와 함께 사용할 수도 있습니다. 자세한 내용은 Azure Monitor 를 [사용하여 논리 앱 모니터링을](../logic-apps/monitor-logic-apps-log-analytics.md)참조하세요.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

<a name="review-runs-history"></a>

## <a name="review-runs-history"></a>실행 기록 검토

트리거가 항목 또는 이벤트에 대해 실행될 때마다 Logic Apps 엔진은 각 항목 또는 이벤트에 대해 별도의 워크플로 인스턴스를 만들고 실행합니다. 기본적으로 각 워크플로 인스턴스는 병렬로 실행되므로 실행을 시작하기 전에 워크플로를 기다릴 필요가 없습니다. 워크플로의 각 단계의 상태와 각 단계의 입력 및 출력을 포함하여 해당 실행 중에 발생한 상황을 검토할 수 있습니다.

1. Azure [포털에서](https://portal.azure.com)논리 앱 디자이너에서 논리 앱을 찾아 엽니다.

   논리 앱을 찾으려면 기본 Azure 검색 상자에서 을 입력한 `logic apps`다음 논리 앱을 **선택합니다.**

   !["로직 앱" 서비스 찾기 및 선택](./media/monitor-logic-apps/find-your-logic-app.png)

   Azure 포털에는 Azure 구독과 연결된 모든 논리 앱이 표시됩니다. 이름, 구독, 리소스 그룹, 위치 등을 기반으로 이 목록을 필터링할 수 있습니다.

   ![구독과 연결된 논리 앱 보기](./media/monitor-logic-apps/logic-apps-list-in-subscription.png)

1. 논리 앱을 선택한 다음 **개요를**선택합니다.

   개요 창, **실행 기록**아래, 모든 과거, 현재 및 논리 앱에 대 한 대기 실행 이 나타납니다. 목록에 많은 실행이 표시되고 원하는 항목을 찾을 수 없는 경우 목록을 필터링해 보십시오. 예상한 데이터를 찾을 수 없는 경우 도구 모음에서 **새로 고침**을 선택해보세요.

   ![개요, 기록 실행 및 기타 논리 앱 정보](./media/monitor-logic-apps/overview-pane-logic-app-details-run-history.png)

   논리 앱 실행에 대한 가능한 상태는 다음과 같습니다.

   | 상태 | 설명 |
   |--------|-------------|
   | **Cancelled** | 워크플로가 실행 중이었지만 취소 요청을 받았습니다. |
   | **실패** | 하나 이상의 작업이 실패했으며 워크플로의 이후 작업이 실패를 처리하도록 설정되지 않았습니다. |
   | **실행 중** | 워크플로가 현재 실행 중입니다. <p>이 상태는 제한된 워크플로 또는 현재 가격 책정 계획으로 인해 나타날 수도 있습니다. 자세한 내용은 [가격 책정 페이지에서 작업 제한](https://azure.microsoft.com/pricing/details/logic-apps/)을 참조하세요. [진단 로깅을](../logic-apps/monitor-logic-apps.md)설정하면 발생하는 모든 스로틀 이벤트에 대한 정보를 얻을 수 있습니다. |
   | **성공** | 모든 작업에 성공했습니다. <p>**참고:** 특정 작업에서 오류가 발생한 경우 워크플로의 이후 작업에서 해당 오류를 처리했습니다. |
   | **대기** | 예를 들어 아직 실행 중인 이전 워크플로로 인해 워크플로가 시작되지 않았거나 일시 중지되었습니다. |
   |||

1. 특정 실행에 대한 단계 및 기타 정보를 검토하려면 **실행 기록에서**해당 실행을 선택합니다.

   ![검토할 특정 실행 선택](./media/monitor-logic-apps/select-specific-logic-app-run.png)

   **논리 앱 실행** 창에는 선택한 실행의 각 단계, 각 단계의 실행 상태 및 각 단계가 실행되는 데 걸린 시간이 표시됩니다.

   ![특정 실행의 각 작업](./media/monitor-logic-apps/logic-app-run-pane.png)

   목록 양식에서 이 정보를 보려면 **논리 앱 실행** 도구 모음에서 세부 정보 **실행을**선택합니다.

   ![도구 모음에서 "세부 정보 실행"을 선택합니다.](./media/monitor-logic-apps/select-run-details-on-toolbar.png)

   세부 정보 실행 보기에는 각 단계, 해당 상태 및 기타 정보가 표시됩니다.

   ![실행의 각 단계에 대한 세부 정보 검토](./media/monitor-logic-apps/review-logic-app-run-details.png)

   예를 들어 [로직 앱에 대한 REST API를](https://docs.microsoft.com/rest/api/logic)사용할 때 필요할 수 있는 실행의 **상관 ID** 속성을 얻을 수 있습니다.

1. 특정 단계에 대한 자세한 정보를 얻으려면 다음 중 하나를 선택합니다.

   * 논리 **앱 실행** 창에서 셰이프가 확장되도록 단계를 선택합니다. 이제 입력, 출력 및 해당 단계에서 발생한 오류와 같은 정보를 볼 수 있습니다.

     ![논리 앱 실행 창에서 뷰 실패 단계](./media/monitor-logic-apps/specific-step-inputs-outputs-errors.png)

   * 논리 **앱 실행 세부 정보** 창에서 원하는 단계를 선택합니다.

     ![실행 세부 정보 창에서 뷰 실패 단계](./media/monitor-logic-apps/select-failed-step-in-failed-run.png)

     이제 다음과 같이 해당 단계의 입력 및 출력과 같은 정보를 볼 수 있습니다.

   > [!NOTE]
   > 모든 런타임 세부 정보 및 이벤트는 Logic Apps 서비스 내에서 암호화됩니다. 사용자가 해당 데이터를 보기 위해 요청하는 경우에만 해독됩니다. 실행 [기록에서 입력 및 출력을 숨기거나](../logic-apps/logic-apps-securing-a-logic-app.md#obfuscate) [AZURE 역할 기반 액세스 제어(RBAC)를](../role-based-access-control/overview.md)사용하여 이 정보에 대한 사용자 액세스를 제어할 수 있습니다.

<a name="review-trigger-history"></a>

## <a name="review-trigger-history"></a>트리거 기록 검토

각 논리 앱 실행은 트리거로 시작합니다. 트리거 기록에는 논리 앱이 만든 모든 트리거 시도와 각 트리거 시도에 대한 입력 및 출력에 대한 정보가 나열됩니다.

1. Azure [포털에서](https://portal.azure.com)논리 앱 디자이너에서 논리 앱을 찾아 엽니다.

   논리 앱을 찾으려면 기본 Azure 검색 상자에서 을 입력한 `logic apps`다음 논리 앱을 **선택합니다.**

   !["로직 앱" 서비스 찾기 및 선택](./media/monitor-logic-apps/find-your-logic-app.png)

   Azure 포털에는 Azure 구독과 연결된 모든 논리 앱이 표시됩니다. 이름, 구독, 리소스 그룹, 위치 등을 기반으로 이 목록을 필터링할 수 있습니다.

   ![구독과 연결된 논리 앱 보기](./media/monitor-logic-apps/logic-apps-list-in-subscription.png)

1. 논리 앱을 선택한 다음 **개요를**선택합니다.

1. 논리 앱의 메뉴에서 **개요를**선택합니다. **요약** 섹션에서 **평가**에서 트리거 **기록 참조를**선택합니다.

   ![논리 앱의 트리거 기록 보기](./media/monitor-logic-apps/overview-pane-logic-app-details-trigger-history.png)

   트리거 기록 창에는 논리 앱이 시도한 모든 트리거 시도가 표시됩니다. 트리거가 항목 또는 이벤트에 대해 실행될 때마다 Logic Apps 엔진은 워크플로를 실행하는 별도의 논리 앱 인스턴스를 만듭니다. 기본적으로 실행을 시작하기 전에 대기하는 워크플로가 없도록 각 인스턴스는 병렬로 실행합니다. 따라서 논리 앱이 동시에 여러 항목에 대해 트리거되는 경우 각 항목에 대해 동일한 날짜와 시간이 있는 트리거 항목이 나타납니다.

   ![다른 항목에 대한 여러 트리거 시도](./media/monitor-logic-apps/logic-app-trigger-history.png)

   트리거 시도에서 가능한 상태는 다음과 같습니다.

   | 상태 | 설명 |
   |--------|-------------|
   | **실패** | 오류가 발생했습니다. 실패한 트리거에 생성된 오류 메시지를 검토하려면 해당 트리거 시도를 선택하고 **출력**을 선택합니다. 예를 들어 유효하지 않은 입력을 찾을 수 있습니다. |
   | **건너뜁니다** | 트리거는 엔드포인트를 확인했지만 데이터가 없습니다. |
   | **성공** | 트리거는 엔드포인트를 확인하고 사용할 수 있는 데이터를 찾았습니다. 일반적으로 "실행됨" 상태도 이 상태와 함께 나타납니다. 그렇지 않은 트리거 정의는 `SplitOn` 또는 조건을 충족하지 않는 명령을 포함합니다. <p>이 상태는 수동 트리거, 되풀이 트리거 또는 폴링 트리거에 적용될 수 있습니다. 작업이 처리되지 않은 오류를 생성할 때 트리거가 성공적으로 실행될 수는 있지만 실행 자체는 실패할 수 있습니다. |
   |||

   > [!TIP]
   > 다음 되풀이를 기다리지 않고 트리거를 다시 확인할 수 있습니다. 개요 도구 모음에서 **트리거 실행을**선택하고 검사를 강제하는 트리거를 선택합니다. 또는 Logic Apps 디자이너 도구 모음에서 **실행**을 선택합니다.

1. 트리거 창에서 특정 트리거 시도에 대한 정보를 보려면 해당 트리거 이벤트를 선택합니다. 목록에 많은 트리거 시도가 표시되고 원하는 항목을 찾을 수 없는 경우 목록을 필터링해 보십시오. 예상한 데이터를 찾을 수 없는 경우 도구 모음에서 **새로 고침**을 선택해보세요.

   ![특정 트리거 시도 보기](./media/monitor-logic-apps/select-trigger-event-for-review.png)

   이제 선택한 트리거 이벤트에 대한 정보를 검토할 수 있습니다.

   ![특정 트리거 정보 보기](./media/monitor-logic-apps/view-specific-trigger-details.png)

<a name="add-azure-alerts"></a>

## <a name="set-up-monitoring-alerts"></a>모니터링 경고 설정

논리 앱의 특정 메트릭 또는 초과 임계값을 기반으로 경고를 받으려면 [Azure Monitor에서 경고를 설정합니다.](../azure-monitor/platform/alerts-overview.md) [Azure의 매트릭](../monitoring-and-diagnostics/monitoring-overview-metrics.md)에 대해 알아봅니다. [Azure Monitor를](../log-analytics/log-analytics-overview.md)사용하지 않고 경고를 설정하려면 다음 단계를 따르십시오.

1. 논리 앱 메뉴에서 **모니터링**에서**경고 새 경고 규칙을** **선택합니다.** > 

   ![논리 앱에 대한 경고 추가](./media/monitor-logic-apps/add-new-alert-rule.png)

1. **리소스**에서 **규칙 만들기** 창에서 아직 선택하지 않은 경우 논리 앱을 선택합니다. **조건에서**경고를 트리거하는 조건을 정의할 수 있도록 **추가를** 선택합니다.

   ![규칙에 대한 조건 추가](./media/monitor-logic-apps/add-condition-for-rule.png)

1. 신호 **논리 구성** 창에서 경고를 받을 신호를 찾아 선택합니다. 검색 상자를 사용하거나 신호를 사전순으로 정렬하려면 신호 **이름** 열 헤더를 선택할 수 있습니다.

   예를 들어 트리거가 실패할 때 경고를 보내려면 다음 단계를 따르십시오.

   1. 신호 **이름** 열에서 트리거 실패 신호를 찾아 **선택합니다.**

      ![경고 를 만들기 위한 신호 선택](./media/monitor-logic-apps/find-and-select-signal.png)

   1. 선택한 신호에 대해 열리는 정보 창에서 **Alert 논리에서**다음과 같은 조건을 설정합니다.

   1. **연산자의**경우 **보다 크거나 같음을**선택합니다.

   1. **집계 유형의**경우 **수를**선택합니다.

   1. **임계값의**경우 `1`을 입력합니다.

   1. **조건 미리 보기에서**상태가 올바른지 확인합니다.

   1. 을 **기반으로 평가된**값에서 경고 규칙을 실행하기 위한 간격 및 빈도를 설정합니다. **집계 세분성(기간)의**경우 데이터를 그룹화하는 기간을 선택합니다. **평가 빈도의**경우 조건을 확인할 빈도를 선택합니다.

   1. 준비가 되면 **완료를**선택합니다.

   완성된 상태는 다음과 같습니다.

   ![경고에 대한 조건 설정](./media/monitor-logic-apps/set-up-condition-for-alert.png)

   이제 **규칙 만들기** 페이지에 만든 조건과 해당 경고를 실행하는 비용이 표시됩니다.

   !["규칙 만들기" 페이지의 새 경고](./media/monitor-logic-apps/finished-alert-condition-cost.png)

1. 경고에 대한 이름, 선택적 설명 및 심각도 수준을 지정합니다. **만들기 설정이** 켜져 있을 때 Enable 규칙을 그대로 두거나 규칙을 활성화할 준비가 될 때까지 끕니다.

1. 작업이 완료되면 **경고 규칙 만들기를**선택합니다.

> [!TIP]
> 경고에서 논리 앱을 실행하기 위해 워크플로에서 다음 예제와 같은 작업을 수행할 수 있도록 하는 [요청 트리거](../connectors/connectors-native-reqres.md)를 포함할 수 있습니다.
> 
> * [Slack에 게시](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app)
> * [텍스트 보내기](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app)
> * [큐에 메시지 추가](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app)

## <a name="next-steps"></a>다음 단계

* [Azure 모니터를 사용하여 논리 앱 모니터링](../logic-apps/monitor-logic-apps-log-analytics.md)