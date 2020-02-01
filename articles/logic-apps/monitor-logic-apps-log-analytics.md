---
title: Azure Monitor 로그를 사용 하 여 논리 앱 모니터링
description: Azure Monitor 로그를 설정 하 고에 대 한 진단 데이터를 수집 하 여 논리 앱 문제 해결 Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: divswa, logicappspm
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: 1948151dfa07e565a929b4506273e3f88f5408c4
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/31/2020
ms.locfileid: "76907826"
---
# <a name="set-up-azure-monitor-logs-and-collect-diagnostics-data-for-azure-logic-apps"></a>Azure Monitor 로그 설정 및 Azure Logic Apps에 대 한 진단 데이터 수집

런타임 중에 논리 앱에 대 한 보다 다양 한 디버깅 정보를 얻기 위해 [Azure Monitor 로그](../azure-monitor/platform/data-platform-logs.md) 를 설정 하 고 사용 하 여 트리거 이벤트, 실행 이벤트 및 작업 이벤트와 같은 런타임 데이터 및 이벤트에 대 한 정보를 [Log Analytics 작업 영역](../azure-monitor/platform/resource-logs-collect-workspace.md)에 기록 하 고 저장할 수 있습니다. [Azure Monitor](../azure-monitor/overview.md) 를 사용 하면 클라우드 및 온-프레미스 환경을 모니터링 하 여 가용성과 성능을 더 쉽게 유지할 수 있습니다. Azure Monitor 로그를 사용 하 여이 정보를 수집 하 고 검토 하는 데 도움이 되는 [로그 쿼리](../azure-monitor/log-query/log-query-overview.md) 를 만들 수 있습니다. 또한 Azure Storage 및 Azure Event Hubs와 같은 [다른 azure 서비스에서이 진단 데이터를 사용할](#extend-data)수 있습니다.

논리 앱에 대 한 로깅을 설정 하려면 [논리 앱을 만들 때 Log Analytics를 사용 하도록](#logging-for-new-logic-apps)설정 하거나 기존 논리 앱에 대 한 Log Analytics 작업 영역에 [Logic Apps 관리 솔루션을 설치할](#install-management-solution) 수 있습니다. 이 솔루션은 논리 앱 실행에 대 한 집계 된 정보를 제공 하며 상태, 실행 시간, 다시 전송 상태 및 상관 관계 Id와 같은 특정 세부 정보를 포함 합니다. 그런 다음이 정보에 대 한 로깅을 사용 하도록 설정 하 고 쿼리를 만들려면 [Azure Monitor 로그를 설정](#set-up-resource-logs)합니다.

이 문서에서는 논리 앱을 만들 때 Log Analytics를 사용 하도록 설정 하는 방법, Logic Apps 관리 솔루션을 설치 및 설정 하는 방법 및 Azure Monitor 로그에 대 한 쿼리를 설정 하 고 만드는 방법을 보여 줍니다.

## <a name="prerequisites"></a>필수 조건

시작 하기 전에 [Log Analytics 작업 영역이](../azure-monitor/platform/resource-logs-collect-workspace.md)필요 합니다. 작업 영역이 없는 경우 [Log Analytics 작업 영역을 만드는 방법](../azure-monitor/learn/quick-create-workspace.md)에 대해 알아봅니다.

<a name="logging-for-new-logic-apps"></a>

## <a name="enable-log-analytics-for-new-logic-apps"></a>새 논리 앱에 대 한 Log Analytics 사용

논리 앱을 만들 때 Log Analytics를 켤 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에서 논리 앱을 만들기 위한 정보를 제공 하는 창에서 다음 단계를 수행 합니다.

   1. **Log Analytics**에서 **켜기**를 선택 합니다.

   1. **Log Analytics 작업 영역** 목록에서 논리 앱 실행의 데이터를 전송 하려는 작업 영역을 선택 합니다.

      ![논리 앱 정보 제공](./media/monitor-logic-apps-log-analytics/create-logic-app-details.png)

      이 단계를 마치면 Azure에서 Log Analytics 작업 영역에 연결된 논리 앱이 만들어집니다. 또한이 단계에서는 Logic Apps 관리 솔루션을 작업 영역에 자동으로 설치 합니다.

1. 완료되면 **만들기**를 선택합니다.

1. 논리 앱을 실행 한 후 논리 앱 실행을 보려면 [다음 단계를 계속 진행](#view-logic-app-runs)합니다.

<a name="install-management-solution"></a>

## <a name="install-logic-apps-management-solution"></a>Logic Apps 관리 솔루션 설치

논리 앱을 만들 때 Log Analytics를 설정한 경우이 단계를 건너뜁니다. Log Analytics 작업 영역에 Logic Apps 관리 솔루션이 이미 설치 되어 있습니다.

1. [Azure Portal](https://portal.azure.com)의 검색 상자에 `log analytics workspaces`를 입력 하 고 **Log Analytics 작업 영역**을 선택 합니다.

   !["Log Analytics 작업 영역"을 선택 합니다.](./media/monitor-logic-apps-log-analytics/find-select-log-analytics-workspaces.png)

1. **Log Analytics 작업 영역**에서 작업 영역을 선택 합니다.

   ![Log Analytics 작업 영역 선택](./media/monitor-logic-apps-log-analytics/select-log-analytics-workspace.png)

1. **개요** 창의 **Log Analytics 시작** > **모니터링 솔루션 구성**에서 **솔루션 보기**를 선택 합니다.

   ![개요 창에서 "솔루션 보기"를 선택 합니다.](./media/monitor-logic-apps-log-analytics/log-analytics-workspace.png)

1. **개요**에서 **추가**를 선택 합니다.

   ![개요 창에서 새 솔루션 추가](./media/monitor-logic-apps-log-analytics/add-logic-apps-management-solution.png)

1. **Marketplace** 가 열리면 검색 상자에 `logic apps management`를 입력 하 고 **Logic Apps 관리**를 선택 합니다.

   ![Marketplace에서 "Logic Apps 관리"를 선택 합니다.](./media/monitor-logic-apps-log-analytics/select-logic-apps-management.png)

1. 솔루션 설명 창에서 **만들기**를 선택 합니다.

   !["만들기"를 선택 하 여 "Logic Apps 관리" 솔루션을 추가 합니다.](./media/monitor-logic-apps-log-analytics/create-logic-apps-management-solution.png)

1. 솔루션을 설치 하려는 Log Analytics 작업 영역을 검토 하 고 확인 한 후 **만들기** 를 다시 선택 합니다.

   !["Logic Apps 관리"에 대해 "만들기"를 선택 합니다.](./media/monitor-logic-apps-log-analytics/confirm-log-analytics-workspace.png)

   Azure가 Log Analytics 작업 영역을 포함 하는 Azure 리소스 그룹에 솔루션을 배포한 후 솔루션은 작업 영역의 요약 창에 표시 됩니다.

   ![작업 영역 요약 창](./media/monitor-logic-apps-log-analytics/workspace-summary-pane-logic-apps-management.png)

<a name="set-up-resource-logs"></a>

## <a name="set-up-azure-monitor-logs"></a>Azure Monitor 로그 설정

런타임 이벤트 및 데이터에 대 한 정보를 [Azure Monitor 로그](../azure-monitor/platform/data-platform-logs.md)에 저장 하는 경우이 정보를 찾고 검토 하는 데 도움이 되는 [로그 쿼리](../azure-monitor/log-query/log-query-overview.md) 를 만들 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에서 논리 앱을 찾고 선택합니다.

1. 논리 앱 메뉴의 **모니터링**아래에서 진단 **설정** > **진단 설정 추가**를 선택 합니다.

   !["모니터링"에서 "진단 설정" > "진단 설정 추가"를 선택 합니다.](./media/monitor-logic-apps-log-analytics/logic-app-diagnostics.png)

1. 설정을 만들려면 다음 단계를 수행 합니다.

   1. 설정의 이름을 지정 합니다.

   1. **Log Analytics에 보내기**를 선택합니다.

   1. **구독**의 경우 Log Analytics 작업 영역에 연결 된 Azure 구독을 선택 합니다.

   1. **Log Analytics 작업 영역**에서 사용 하려는 작업 영역을 선택 합니다.

   1. **로그**에서 기록 하려는 이벤트 범주를 지정 하는 **WorkflowRuntime** 범주를 선택 합니다.

   1. 모든 메트릭을 선택 하려면 **메트릭**에서 **allmetrics**을 선택 합니다.

   1. 완료되면 **저장**을 선택합니다.

   예:

   ![로깅에 대한 Log Analytics 작업 영역 및 데이터 선택](./media/monitor-logic-apps-log-analytics/send-diagnostics-data-log-analytics-workspace.png)

<a name="view-logic-app-runs"></a>

## <a name="view-logic-app-runs-status"></a>논리 앱 실행 상태 보기

논리 앱을 실행 한 후 Log Analytics 작업 영역에서 해당 실행에 대 한 데이터를 볼 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에서 Log Analytics 작업 영역을 찾아 엽니다.

1. 작업 영역의 메뉴에서 **작업 영역 요약** > **Logic Apps 관리**를 선택 합니다.

   ![논리 앱 실행 상태 및 수](./media/monitor-logic-apps-log-analytics/logic-app-runs-summary.png)

   > [!NOTE]
   > Logic Apps 관리 타일이 실행 후에 즉시 결과를 표시 하지 않는 경우 **새로 고침** 을 선택 하거나 잠시 기다린 후 다시 시도 하세요.

   여기에서 논리 앱 실행은 이름이나 실행 상태로 그룹화됩니다. 이 페이지는 논리 앱 실행에 대한 작업 또는 트리거의 오류에 대한 세부 정보도 표시합니다.

   ![논리 앱 실행에 대한 상태 요약](./media/monitor-logic-apps-log-analytics/logic-app-runs-summary-details.png)

1. 특정 논리 앱 또는 상태에 대 한 모든 실행을 보려면 해당 논리 앱 또는 상태에 대 한 행을 선택 합니다.

   특정 논리 앱에 대한 모든 실행을 보여 주는 예제는 다음과 같습니다.

   ![논리 앱 실행 및 상태 보기](./media/monitor-logic-apps-log-analytics/logic-app-run-details.png)

   [추적 속성을 설정](#extend-data)하는 작업의 경우 **추적 된 속성** 열에서 **보기** 를 선택 하 여 해당 속성을 볼 수도 있습니다. 추적된 속성을 검색하려면 열 필터를 사용합니다.

   ![논리 앱에 대해 추적된 속성 보기](./media/monitor-logic-apps-log-analytics/logic-app-tracked-properties.png)

   > [!NOTE]
   > Log Analytics 작업 영역에 표시 되기 전에 추적 된 속성 또는 완료 된 이벤트에서 10-15 분의 지연이 발생할 수 있습니다.
   > 또한이 페이지의 다시 **전송** 기능을 현재 사용할 수 없습니다.

1. 결과를 필터링 하기 위해 클라이언트 쪽 및 서버 쪽 필터링을 모두 수행할 수 있습니다.

   * **클라이언트 쪽 필터**: 각 열에 대해 원하는 필터를 선택 합니다. 예를 들면 다음과 같습니다.

     ![예제 열 필터](./media/monitor-logic-apps-log-analytics/filters.png)

   * **서버 쪽 필터**: 특정 시간 창을 선택 하거나 표시 되는 실행 수를 제한 하려면 페이지 맨 위에 있는 범위 컨트롤을 사용 합니다. 기본적으로 1,000개 레코드가 한 번에 나타납니다.

     ![시간 변경 창](./media/monitor-logic-apps-log-analytics/change-interval.png)

1. 특정 실행에 대 한 모든 작업과 해당 세부 정보를 보려면 논리 앱 실행에 대 한 행을 선택 합니다.

   특정 논리 앱 실행에 대 한 모든 작업 및 트리거를 보여 주는 예제는 다음과 같습니다.

   ![논리 앱 실행에 대한 작업 보기](./media/monitor-logic-apps-log-analytics/logic-app-action-details.png)

<!-------------
   * **Resubmit**: You can resubmit one or more logic apps runs that failed, succeeded, or are still running. Select the check boxes for the runs that you want to resubmit, and then select **Resubmit**.

     ![Resubmit logic app runs](./media/monitor-logic-apps-log-analytics/logic-app-resubmit.png)
--------------->

<a name="extend-data"></a>

## <a name="send-diagnostic-data-to-azure-storage-and-azure-event-hubs"></a>Azure Storage 및 Azure Event Hubs에 진단 데이터 보내기

Azure Monitor 로그를 사용 하 여 논리 앱의 진단 데이터를 다른 Azure 서비스와 함께 사용 하는 방법을 확장할 수 있습니다. 예를 들면 다음과 같습니다.

* [저장소 계정에 Azure 리소스 로그 보관](../azure-monitor/platform/resource-logs-collect-storage.md)
* [Azure 플랫폼 로그를 Azure Event Hubs로 스트림](../azure-monitor/platform/resource-logs-stream-event-hubs.md)

그런 다음 [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) 및 [Power BI](../azure-monitor/platform/powerbi.md)와 같은 다른 서비스의 원격 분석 및 분석을 사용하여 실시간으로 모니터링할 수 있습니다. 예:

* [Event Hub에서 Stream Analytics로 데이터 스트림](../stream-analytics/stream-analytics-define-inputs.md)
* [Stream Analytics를 사용하여 스트리밍 데이터 분석 및 Power BI에서 실시간 분석 대시보드 만들기](../stream-analytics/stream-analytics-power-bi-dashboard.md)

진단 데이터를 전송 하려는 위치에 따라 먼저 [azure storage 계정을 만들거나](../storage/common/storage-create-storage-account.md) [azure 이벤트 허브를 만들어야](../event-hubs/event-hubs-create.md)합니다. 그런 다음 해당 데이터를 전송 하려는 대상을 선택할 수 있습니다. 보존 기간은 저장소 계정을 사용 하는 경우에만 적용 됩니다.

![Azure Storage 계정 또는 이벤트 허브로 데이터 보내기](./media/monitor-logic-apps-log-analytics/diagnostics-storage-event-hub-log-analytics.png)

<a name="diagnostic-event-properties"></a>

## <a name="azure-monitor-diagnostics-events"></a>Azure Monitor 진단 이벤트

각 진단 이벤트는 논리 앱 및 해당 이벤트(예: 상태, 시작 시간, 종료 시간 등)에 대한 세부 정보를 포함합니다. 모니터링, 추적 및 로깅을 프로그래밍 방식으로 설정 하려면 [Azure Logic Apps에 대 한 REST API](https://docs.microsoft.com/rest/api/logic) 및 [Azure Monitor에 대 한 REST API](../azure-monitor/platform/metrics-supported.md#microsoftlogicworkflows)이 정보를 사용할 수 있습니다. 또한에 표시 되는 `clientTrackingId` 및 `trackedProperties` 속성을 사용할 수 있습니다. 

* `clientTrackingId`: 제공되지 않은 경우 Azure는 자동으로 이 ID를 생성하고 논리 앱에서 호출되는 중첩된 모든 워크플로를 포함하여 논리 앱 실행 간의 이벤트를 상호 연결합니다. 트리거 요청에서 사용자 지정 ID 값으로 `x-ms-client-tracking-id` 헤더를 전달 하 여 트리거에서이 ID를 수동으로 지정할 수 있습니다. 요청 트리거, HTTP 트리거 또는 웹후크 트리거를 사용할 수 있습니다.

* `trackedProperties`: 진단 데이터에서 입력 또는 출력을 추적 하려면 논리 앱 디자이너를 사용 하거나 논리 앱의 JSON 정의에서 직접 `trackedProperties` 섹션을 작업에 추가할 수 있습니다. 추적된 속성으로 단일 작업의 입력 및 출력만 추적할 수 있지만 이벤트의 `correlation` 속성을 사용하여 실행에 있는 작업 간에 상호 연결할 수 있습니다. 하나 이상의 속성, 하나 이상의 속성을 추적 하려면 작업 정의에 `trackedProperties` 섹션과 속성을 추가 합니다.

  다음은 **변수 초기화** 작업 정의에 레코드가 아닌 배열이 아니라 작업 입력의 추적 된 속성이 포함 되는 방법을 보여 주는 예입니다.

  ``` json
  {
     "Initialize_variable": {
        "type": "InitializeVariable",
        "inputs": {
           "variables": [
              {
                 "name": "ConnectorName", 
                 "type": "String", 
                 "value": "SFTP-SSH" 
              }
           ]
        },
        "runAfter": {},
        "trackedProperties": { 
           "myTrackedPropertyName": "@action().inputs.variables[0].value"
        }
     }
  }
  ```

  이 예제에서는 추적 된 여러 속성을 보여 줍니다.

  ``` json
  "HTTP": {
     "type": "Http",
     "inputs": {
        "body": "@triggerBody()",
        "headers": {
           "Content-Type": "application/json"
        },
        "method": "POST",
        "uri": "http://store.fabrikam.com",
     },
     "runAfter": {},
     "trackedProperties": {
        "myActionHTTPStatusCode": "@action()['outputs']['statusCode']",
        "myActionHTTPValue": "@action()['outputs']['body']['<content>']",
        "transactionId": "@action()['inputs']['body']['<content>']"
     }
  }
  ```

이 예에서는 `ActionCompleted` 이벤트에 `clientTrackingId` 및 `trackedProperties` 특성이 포함 되는 방법을 보여 줍니다.

```json
{
   "time": "2016-07-09T17:09:54.4773148Z",
   "workflowId": "/subscriptions/XXXXXXXXXXXXXXX/resourceGroups/MyResourceGroup/providers/Microsoft.Logic/workflows/MyLogicApp",
   "resourceId": "/subscriptions/<subscription-ID>/resourceGroups/MyResourceGroup/providers/Microsoft.Logic/workflows/MyLogicApp/runs/<run-ID>/actions/Http",
   "category": "WorkflowRuntime",
   "level": "Information",
   "operationName": "Microsoft.Logic/workflows/workflowActionCompleted",
   "properties": {
      "$schema": "2016-06-01",
      "startTime": "2016-07-09T17:09:53.4336305Z",
      "endTime": "2016-07-09T17:09:53.5430281Z",
      "status": "Succeeded",
      "code": "OK",
      "resource": {
         "subscriptionId": "<subscription-ID>",
         "resourceGroupName": "MyResourceGroup",
         "workflowId": "<logic-app-workflow-ID>",
         "workflowName": "MyLogicApp",
         "runId": "08587361146922712057",
         "location": "westus",
         "actionName": "Http"
      },
      "correlation": {
         "actionTrackingId": "e1931543-906d-4d1d-baed-dee72ddf1047",
         "clientTrackingId": "<my-custom-tracking-ID>"
      },
      "trackedProperties": {
         "myTrackedPropertyName": "<value>"
      }
   }
}
```

## <a name="next-steps"></a>다음 단계

* [모니터링 및 추적 쿼리 만들기](../logic-apps/create-monitoring-tracking-queries.md)
* [Azure Monitor 로그를 사용 하 여 B2B 메시지 모니터링](../logic-apps/monitor-b2b-messages-log-analytics.md)