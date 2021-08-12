---
title: 상태 모니터링, 기록 보기 및 경고 설정
description: 실행 상태를 확인하고, 트리거 기록을 검토하고, Azure Logic Apps에서 경고를 사용하도록 설정하여 논리 앱 문제 해결
services: logic-apps
ms.suite: integration
ms.reviewer: divswa, logicappspm
ms.topic: article
ms.date: 05/04/2020
ms.openlocfilehash: 39971500d89ba8cbc1bbaf4938884bf2eba20813
ms.sourcegitcommit: 9ad20581c9fe2c35339acc34d74d0d9cb38eb9aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2021
ms.locfileid: "110536625"
---
# <a name="monitor-run-status-review-trigger-history-and-set-up-alerts-for-azure-logic-apps"></a>실행 상태를 모니터링하고, 트리거 기록을 검토하고, Azure Logic Apps에 대한 경고를 설정합니다.

[논리 앱을 만들고 실행](../logic-apps/quickstart-create-first-logic-app-workflow.md)한 후에는 해당 논리 앱의 실행 상태, [실행 기록](#review-runs-history), [트리거 기록](#review-trigger-history) 및 성능을 확인할 수 있습니다. 오류 또는 가능한 다른 문제에 대한 알림을 받으려면 [경고](#add-azure-alerts)를 설정합니다. 예를 들어 "한 시간에 5개 이상의 실행이 실패하는 경우"를 검색하는 경고를 만들 수 있습니다.

실시간 이벤트 모니터링 및 보다 풍부한 디버깅은 [Azure Monitor 로그](../azure-monitor/overview.md)를 사용하여 논리 앱에 대한 진단 로깅을 설정합니다. 이 Azure 서비스를 사용하면 클라우드 및 온-프레미스 환경을 모니터링하여 가용성과 성능을 보다 쉽게 유지 관리할 수 있습니다. 그러면 트리거 이벤트, 실행 이벤트, 작업 이벤트와 같은 이벤트를 찾고 볼 수 있습니다. [Azure Monitor 로그](../azure-monitor/logs/data-platform-logs.md)에 이 정보를 저장하여 이 정보를 찾고 분석하는 데 도움이 되는 [로그 쿼리](../azure-monitor/logs/log-query-overview.md)를 만들 수 있습니다. 또한 Azure Storage 및 Azure Event Hubs와 같은 다른 Azure 서비스에서 이 진단 데이터를 사용할 수도 있습니다. 자세한 내용은 [Azure Monitor를 사용하여 논리 앱 모니터링](../logic-apps/monitor-logic-apps-log-analytics.md)을 참조하세요.

> [!NOTE]
> 논리 앱이 [내부 액세스 엔드포인트](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access)를 사용하도록 만들어진 [ISE(통합 서비스 환경)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)에서 실행되는 경우 *가상 네트워크 내부에서만 논리 앱의 실행 기록에서 입력 및 출력을 보고 액세스할 수 있습니다*. 실행 기록에 액세스하려는 컴퓨터와 프라이빗 엔드포인트 간에 네트워크 연결이 있어야 합니다. 예를 들어 클라이언트 컴퓨터는 ISE의 가상 네트워크 내부 또는 피어링이나 VPN(가상 사설망)을 통해 ISE의 가상 네트워크에 연결된 가상 네트워크 내부에 있을 수 있습니다. 자세한 내용은 [ISE 엔드포인트 액세스](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access)를 참조하세요.

<a name="review-runs-history"></a>

## <a name="review-runs-history"></a>실행 기록 검토

항목이나 이벤트에 대해 트리거가 발생될 때마다 Logic Apps 엔진은 각 항목 또는 이벤트에 대해 별도의 워크플로 인스턴스를 만들고 실행합니다. 기본적으로 실행을 시작하기 전에 대기하는 워크플로가 없도록 각 워크플로 인스턴스는 병렬로 실행됩니다. 워크플로 내 각 단계의 상태 및 각 단계의 입력과 출력을 비롯하여 해당 실행 중에 발생한 결과를 검토할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)의 Logic App Designer에서 논리 앱을 찾아서 엽니다.

   논리 앱을 찾으려면 기본 Azure 검색 상자에 `logic apps`를 입력한 다음, **Logic Apps** 를 선택합니다.

   ![‘Logic Apps’ 찾기 및 선택](./media/monitor-logic-apps/find-your-logic-app.png)

   Azure Portal에 Azure 구독과 연결된 모든 논리 앱이 표시됩니다. 이름, 구독, 리소스 그룹, 위치 등을 기준으로 이 목록을 필터링할 수 있습니다.

   ![구독과 연결된 논리 앱 보기](./media/monitor-logic-apps/logic-apps-list-in-subscription.png)

1. 논리 앱을 선택한 후 **개요** 를 선택합니다.

   개요 창의 **실행 기록** 에서 논리 앱의 모든 과거, 현재 및 대기 중인 실행이 표시됩니다. 목록에 표시된 실행이 많아서 원하는 항목을 찾을 수 없는 경우 목록을 필터링해 보세요.

   > [!TIP]
   > 실행 상태가 표시되지 않으면 **새로 고침** 을 선택하여 개요 페이지를 새로 고쳐 보세요.
   > 조건이 충족되지 않거나 데이터를 찾을 수 없어 트리거를 건너뛴 경우에는 실행이 발생하지 않습니다.

   ![개요, 실행 기록 및 기타 논리 앱 정보](./media/monitor-logic-apps/overview-pane-logic-app-details-run-history.png)

   다음은 가능한 실행 상태입니다.

   | 실행 상태 | Description |
   |------------|-------------|
   | **중단됨** | 시스템이 중단되거나 Azure 구독이 만료되는 등의 외부 문제로 인해 실행이 중지되었거나 완료되지 않았습니다. |
   | **취소** | 실행이 트리거되고 시작되었지만 취소 요청이 수신되었습니다. |
   | **실패** | 실행에서 하나 이상의 작업이 실패했습니다. 실패를 처리하는 워크플로의 후속 작업이 설정되지 않았습니다. |
   | **실행 중** | 실행이 트리거되어 현재 진행 중이지만, [작업 제한](logic-apps-limits-and-config.md) 또는 [현재 가격 책정 계획](https://azure.microsoft.com/pricing/details/logic-apps/)으로 인해 제한된 실행에도 이 상태가 표시될 수 있습니다. <p><p>**팁**: [진단 로깅](monitor-logic-apps-log-analytics.md)을 설정한 경우 발생하는 모든 제한된 이벤트에 대한 정보를 얻을 수 있습니다. |
   | **성공함** | 실행이 성공했습니다. 작업이 실패하면 워크플로의 후속 작업에서 실패를 처리합니다. |
   | **시간 초과됨** | 현재 기간이 [**실행 기록 보존 기간(일)** 설정](logic-apps-limits-and-config.md#run-duration-retention-limits)을 통해 제어되는 실행 기간 제한을 초과하여 실행 시간이 초과되었습니다. 실행의 기간은 실행의 시작 시간과 해당 시작 시간의 실행 기간 제한을 사용하여 계산됩니다. <p><p>**참고**: 실행 기간이 [**실행 기록 보존 기간(일)** 설정](logic-apps-limits-and-config.md#run-duration-retention-limits)을 통해 제어되는 현재 *실행 기록 보존 제한* 을 초과하는 경우에도 일간 정리 작업을 통해 실행 기록에서 실행이 지워집니다. 실행 시간이 초과되건 실행이 완료되건 보존 기간은 항상 실행의 시작 시간과 *현재* 보존 제한을 사용하여 계산됩니다. 따라서 진행 중인 실행의 기간 제한을 줄이면 실행 시간이 초과됩니다. 그러나 실행이 유지되는지 아니면 기록에서 실행이 지워지는지 여부는 실행 기간이 보존 한도를 초과했는지 여부에 따라 결정됩니다. |
   | **대기 중** | 예를 들어 아직 실행 중인 이전 워크플로 인스턴스 때문에 실행이 시작되지 않았거나 일시 중지되었습니다. |
   |||

1. 특정 실행의 단계 및 기타 정보를 검토하려면 **실행 기록** 에서 해당 실행을 선택합니다.

   ![검토할 특정 실행 선택](./media/monitor-logic-apps/select-specific-logic-app-run.png)

   **논리 앱 실행** 창에는 선택한 실행의 각 단계, 각 단계의 실행 상태 및 각 단계를 실행하는 데 걸린 시간이 표시됩니다. 예를 들면 다음과 같습니다.

   ![특정 실행의 각 작업](./media/monitor-logic-apps/logic-app-run-pane.png)

   목록 양식에서 이 정보를 보려면 **논리 앱 실행** 도구 모음에서 **실행 세부 정보** 를 선택합니다.

   ![도구 모음에서 ‘실행 세부 정보’ 선택](./media/monitor-logic-apps/select-run-details-on-toolbar.png)

   실행 세부 정보 보기에는 각 단계, 해당 상태 및 기타 정보가 표시됩니다.

   ![실행의 각 단계에 대한 세부 정보 검토](./media/monitor-logic-apps/review-logic-app-run-details.png)

   예를 들어 [Logic Apps용 REST API](/rest/api/logic)를 사용할 때 필요할 수 있는 실행의 **상관 관계 ID** 를 가져올 수 있습니다.

1. 특정 단계에 대한 추가 정보를 가져오려면 다음 옵션 중 하나를 선택합니다.

   * **논리 앱 실행** 창에서 셰이프가 확장되도록 단계를 선택합니다. 이제 입력 및 출력과 같은 정보와 해당 단계에서 발생한 오류를 볼 수 있습니다. 예를 들면 다음과 같습니다.

     ![논리 앱 실행 창에서 실패한 단계 보기](./media/monitor-logic-apps/specific-step-inputs-outputs-errors.png)

   * **논리 앱 실행 세부 정보** 창에서 원하는 단계를 선택합니다.

     ![실행 세부 정보 창에서 실패한 단계 보기](./media/monitor-logic-apps/select-failed-step-in-failed-run.png)

     이제 해당 단계의 입력 및 출력과 같은 정보를 볼 수 있습니다. 예를 들면 다음과 같습니다.

   > [!NOTE]
   > 모든 런타임 세부 정보 및 이벤트는 Logic Apps 서비스 내에서 암호화됩니다.
   > 사용자가 해당 데이터를 보기 위해 요청하는 경우에만 해독됩니다.
   > [Azure RBAC(역할 기반 액세스 제어)](../role-based-access-control/overview.md)를 사용하여 [실행 기록에서 입력 및 출력을 숨기거나](../logic-apps/logic-apps-securing-a-logic-app.md#obfuscate) 이 정보에 대한 사용자 액세스를 제어할 수 있습니다.

<a name="review-trigger-history"></a>

## <a name="review-trigger-history"></a>트리거 기록 검토

각 논리 앱 실행은 트리거로 시작합니다. 트리거 기록은 논리 앱이 수행한 모든 트리거 시도 및 각 트리거 시도의 입력과 출력에 대한 정보를 나열합니다.

1. [Azure Portal](https://portal.azure.com)의 Logic App Designer에서 논리 앱을 찾아서 엽니다.

   논리 앱을 찾으려면 기본 Azure 검색 상자에 `logic apps`를 입력한 다음 **Logic Apps** 를 선택합니다.

   ![‘Logic Apps’ 찾기 및 선택](./media/monitor-logic-apps/find-your-logic-app.png)

   Azure Portal에 Azure 구독과 연결된 모든 논리 앱이 표시됩니다. 이름, 구독, 리소스 그룹, 위치 등을 기준으로 이 목록을 필터링할 수 있습니다.

   ![구독과 연결된 논리 앱 보기](./media/monitor-logic-apps/logic-apps-list-in-subscription.png)

1. 논리 앱을 선택한 후 **개요** 를 선택합니다.

1. 논리 앱의 메뉴에서 **개요** 를 선택합니다. **요약** 섹션의 **평가** 에서 **트리거 기록 보기** 를 선택합니다.

   ![논리 앱의 트리거 기록 보기](./media/monitor-logic-apps/overview-pane-logic-app-details-trigger-history.png)

   트리거 기록 창에는 논리 앱이 수행한 모든 트리거 시도가 표시됩니다. 항목 또는 이벤트에 대해 트리거가 발생할 때마다 Logic Apps 엔진은 워크플로를 실행하는 별도의 논리 앱 인스턴스를 만듭니다. 기본적으로 실행을 시작하기 전에 대기하는 워크플로가 없도록 각 인스턴스는 병렬로 실행합니다. 따라서 논리 앱이 동시에 여러 항목에서 트리거하는 경우 각 항목의 날짜 및 시간이 동일한 트리거 항목이 표시됩니다.

   ![여러 항목에 대한 여러 트리거 시도](./media/monitor-logic-apps/logic-app-trigger-history.png)

   가능한 트리거 시도 상태는 다음과 같습니다.

   | 트리거 상태 | Description |
   |----------------|-------------|
   | **실패** | 오류가 발생했습니다. 실패한 트리거에 생성된 오류 메시지를 검토하려면 해당 트리거 시도를 선택하고 **출력** 을 선택합니다. 예를 들어 유효하지 않은 입력을 찾을 수 있습니다. |
   | **생략** | 트리거가 엔드포인트를 확인했지만 지정된 기준을 충족하는 데이터를 찾지 못했습니다. |
   | **성공함** | 트리거는 엔드포인트를 확인하고 사용할 수 있는 데이터를 찾았습니다. 일반적으로 **실행됨** 상태도 이 상태와 함께 나타납니다. 그렇지 않은 트리거 정의는 `SplitOn` 또는 조건을 충족하지 않는 명령을 포함합니다. <p><p>이 상태는 수동 트리거, 되풀이 트리거 또는 폴링 트리거에 적용될 수 있습니다. 작업이 처리되지 않은 오류를 생성할 때 트리거가 성공적으로 실행될 수는 있지만 실행 자체는 실패할 수 있습니다. |
   |||

   > [!TIP]
   > 다음 되풀이를 기다리지 않고 트리거를 다시 확인할 수 있습니다. 개요 도구 모음에서 **트리거 실행** 을 선택하고 트리거를 선택합니다. 그러면 확인을 강제로 수행합니다. 또는 Logic Apps 디자이너 도구 모음에서 **실행** 을 선택합니다.

1. 특정 트리거 시도에 대한 정보를 보려면 트리거 창에서 해당 트리거 이벤트를 선택합니다. 목록에 표시된 트리거 시도가 많아서 원하는 항목을 찾을 수 없는 경우 목록을 필터링해 보세요. 예상한 데이터를 찾을 수 없는 경우 도구 모음에서 **새로 고침** 을 선택해보세요.

   ![특정 트리거 시도 보기](./media/monitor-logic-apps/select-trigger-event-for-review.png)

   이제 선택한 트리거 이벤트에 대한 정보를 검토할 수 있습니다. 예를 들면 다음과 같습니다.

   ![특정 트리거 정보 보기](./media/monitor-logic-apps/view-specific-trigger-details.png)

<a name="add-azure-alerts"></a>

## <a name="set-up-monitoring-alerts"></a>모니터링 경고 설정

논리 앱의 특정 메트릭 또는 초과된 임계값에 대한 경고를 받으려면 [Azure Monitor에서 경고](../azure-monitor/alerts/alerts-overview.md)를 설정합니다. [Azure의 매트릭](../azure-monitor/data-platform.md)에 대해 알아봅니다. [Azure Monitor](../azure-monitor/logs/log-query-overview.md)를 사용하지 않고 경고를 설정하려면 다음 단계를 수행합니다.

1. 논리 앱 메뉴의 **모니터링** 에서 **경고** > **새 경고 규칙** 을 선택합니다.

   ![논리 앱에 대한 경고 추가](./media/monitor-logic-apps/add-new-alert-rule.png)

1. **규칙 만들기** 창의 **리소스** 에서 아직 선택하지 않았다면 논리 앱을 선택합니다. **조건** 에서 **추가** 를 선택하여 경고를 트리거하는 조건을 정의할 수 있습니다.

   ![규칙의 조건 추가](./media/monitor-logic-apps/add-condition-for-rule.png)

1. **신호 논리 구성** 창에서 경고를 받을 신호를 찾아 선택합니다. 검색 상자를 사용하거나 신호를 사전순으로 정렬하려면 **신호 이름** 열 헤더를 선택합니다.

   예를 들어 트리거가 실패할 때 경고를 보내려면 다음 단계를 수행합니다.

   1. **신호 이름** 열에서 **트리거 실패** 신호를 찾아 선택합니다.

      ![경고를 만들기 위한 신호 선택](./media/monitor-logic-apps/find-and-select-signal.png)

   1. 선택한 신호에 대해 열리는 정보 창의 **경고 논리** 에서 다음과 같이 조건을 설정합니다.

   1. **연산자** 에서 **크거나 같음** 을 선택합니다.

   1. **집계 유형** 에서 **개수** 를 선택합니다.

   1. **임계값** 에서 `1`을 입력합니다.

   1. **조건 미리 보기** 에서 조건이 올바르게 표시되는지 확인합니다.

   1. **평가 기준** 에서 경고 규칙을 실행할 간격 및 빈도를 설정합니다. **집계 세분성(기간)** 에서 데이터를 그룹화하는 기간을 선택합니다. **평가 빈도** 에서 조건을 확인할 빈도를 선택합니다.

   1. 준비가 되면 **완료** 를 선택합니다.

   완료된 조건은 다음과 같습니다.

   ![경고 조건 설정](./media/monitor-logic-apps/set-up-condition-for-alert.png)

   이제 **규칙 만들기** 페이지에 만든 조건 및 해당 경고를 실행하는 데 드는 비용이 표시됩니다.

   ![‘규칙 만들기’ 페이지의 새 경고](./media/monitor-logic-apps/finished-alert-condition-cost.png)

1. 경고의 이름, 선택적 설명 및 심각도 수준을 지정합니다. **규칙을 만들면 바로 사용** 설정을 켜진 상태 그대로 두거나 규칙을 사용할 준비가 될 때까지 끕니다.

1. 완료되면 **경고 규칙 만들기** 를 선택합니다.

> [!TIP]
> 경고에서 논리 앱을 실행하기 위해 워크플로에서 다음 예제와 같은 작업을 수행할 수 있도록 하는 [요청 트리거](../connectors/connectors-native-reqres.md)를 포함할 수 있습니다.
>
> * [Slack에 게시](https://github.com/Azure/azure-quickstart-templates/tree/master/demos/alert-to-slack-with-logic-app)
> * [텍스트 보내기](https://github.com/Azure/azure-quickstart-templates/tree/master/demos/alert-to-text-message-with-logic-app)
> * [큐에 메시지 추가](https://github.com/Azure/azure-quickstart-templates/tree/master/demos/alert-to-queue-with-logic-app)

## <a name="next-steps"></a>다음 단계

* [Azure Monitor를 사용하여 논리 앱 모니터링](../logic-apps/monitor-logic-apps-log-analytics.md)
