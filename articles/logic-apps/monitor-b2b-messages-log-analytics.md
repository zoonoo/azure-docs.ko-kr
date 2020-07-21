---
title: Azure Monitor를 사용 하 여 B2B 메시지 모니터링
description: Azure Monitor 로그를 설정 하 고에 대 한 진단 데이터를 수집 하 여 AS2, X12 및 EDIFACT 메시지 문제를 해결 Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: divswa, logicappspm
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: 5baa4d4d968adb25b5520ca91149970f5c5578e9
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86536272"
---
# <a name="set-up-azure-monitor-logs-and-collect-diagnostics-data-for-b2b-messages-in-azure-logic-apps"></a>Azure Logic Apps에서 B2B 메시지에 대한 Azure Monitor 로그 설정 및 진단 데이터 수집

통합 계정에서 거래 파트너 간에 B2B 통신을 설정한 후에는 해당 파트너가 AS2, X12 및 EDIFACT와 같은 프로토콜을 사용 하 여 메시지를 교환할 수 있습니다. 이 통신이 원하는 방식으로 작동 하는지 확인 하기 위해 통합 계정에 대 한 [Azure Monitor 로그](../azure-monitor/platform/data-platform-logs.md) 를 설정할 수 있습니다. [Azure Monitor](../azure-monitor/overview.md) 를 사용 하면 클라우드 및 온-프레미스 환경을 모니터링 하 여 가용성과 성능을 더 쉽게 유지할 수 있습니다. Azure Monitor 로그를 사용 하 여 [Log Analytics 작업 영역](../azure-monitor/platform/resource-logs.md#send-to-log-analytics-workspace)에서 트리거 이벤트, 실행 이벤트 및 작업 이벤트와 같은 런타임 데이터 및 이벤트에 대 한 데이터를 기록 하 고 저장할 수 있습니다. 메시지의 경우 로깅은 다음과 같은 정보를 수집 합니다.

* 메시지 수 및 상태
* 승인 상태
* 메시지와 승인 간의 상관 관계
* 실패에 대한 자세한 오류 설명

Azure Monitor를 사용 하 여이 정보를 찾고 검토 하는 데 도움이 되는 [로그 쿼리](../azure-monitor/log-query/log-query-overview.md) 를 만들 수 있습니다. 또한 Azure Storage 및 Azure Event Hubs와 같은 [다른 azure 서비스에서이 진단 데이터를 사용할](../logic-apps/monitor-logic-apps-log-analytics.md#extend-data)수 있습니다.

통합 계정에 대 한 로깅을 설정 하려면 Azure Portal에 [Logic Apps B2B 솔루션을 설치](#install-b2b-solution) 합니다. 이 솔루션은 B2B 메시지 이벤트에 대 한 집계 정보를 제공 합니다. 그런 다음이 정보에 대 한 로깅을 사용 하도록 설정 하 고 쿼리를 만들려면 [Azure Monitor 로그](#set-up-resource-logs)를 설정 합니다.

이 문서에서는 통합 계정에 대해 Azure Monitor 로깅을 사용 하도록 설정 하는 방법을 보여 줍니다.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>사전 요구 사항

* Log Analytics 작업 영역. Log Analytics 작업 영역이 없는 경우 [Log Analytics 작업 영역을 만드는 방법](../azure-monitor/learn/quick-create-workspace.md)을 알아봅니다.

* Azure Monitor 로깅을 설정 하 고 해당 정보를 Log Analytics 작업 영역으로 전송 하는 논리 앱입니다. [논리 앱에 대 한 Azure Monitor 로그를 설정 하는 방법을](../logic-apps/monitor-logic-apps.md)알아봅니다.

* 논리 앱에 연결된 통합 계정. [통합 계정을 논리 앱에 연결 하는 방법을](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)알아봅니다.

<a name="install-b2b-solution"></a>

## <a name="install-logic-apps-b2b-solution"></a>Logic Apps B2B 솔루션 설치

Azure Monitor 로그에서 논리 앱에 대 한 B2B 메시지를 추적 하려면 먼저 Log Analytics 작업 영역에 **Logic Apps B2B** 솔루션을 추가 합니다.

1. [Azure Portal](https://portal.azure.com)의 검색 상자에를 입력 하 `log analytics workspaces` 고 **Log Analytics 작업 영역**을 선택 합니다.

   !["Log Analytics 작업 영역"을 선택 합니다.](./media/monitor-b2b-messages-log-analytics/find-select-log-analytics-workspaces.png)

1. **Log Analytics 작업 영역**에서 작업 영역을 선택 합니다.

   ![Log Analytics 작업 영역 선택](./media/monitor-b2b-messages-log-analytics/select-log-analytics-workspace.png)

1. 개요 창의 **Get started with Log Analytics**  >  **모니터링 솔루션 구성**Log Analytics 아래에서 **솔루션 보기**를 선택 합니다.

   ![개요 창에서 "솔루션 보기"를 선택 합니다.](./media/monitor-b2b-messages-log-analytics/log-analytics-workspace.png)

1. 개요 창에서 **추가**를 선택 합니다.

   ![개요 창에서 새 솔루션 추가](./media/monitor-b2b-messages-log-analytics/add-logic-apps-management-solution.png)

1. **Marketplace** 가 열리면 검색 상자에를 입력 하 `logic apps b2b` 고 **Logic Apps B2B**를 선택 합니다.

   ![Marketplace에서 "Logic Apps 관리"를 선택 합니다.](./media/monitor-b2b-messages-log-analytics/select-logic-apps-b2b-solution.png)

1. 솔루션 설명 창에서 **만들기**를 선택 합니다.

   !["만들기"를 선택 하 여 "Logic Apps B2B" 솔루션 추가](./media/monitor-b2b-messages-log-analytics/create-logic-apps-b2b-solution.png)

1. 솔루션을 설치 하려는 Log Analytics 작업 영역을 검토 하 고 확인 한 후 **만들기** 를 다시 선택 합니다.

   !["Logic Apps B2B"에 대해 "만들기"를 선택 합니다.](./media/monitor-b2b-messages-log-analytics/confirm-log-analytics-workspace.png)

   Azure가 Log Analytics 작업 영역을 포함 하는 Azure 리소스 그룹에 솔루션을 배포한 후 솔루션은 작업 영역의 요약 창에 표시 됩니다. B2B 메시지를 처리 하는 경우이 창의 메시지 수가 업데이트 됩니다.

   ![작업 영역 요약 창](./media/monitor-b2b-messages-log-analytics/b2b-overview-messages-summary.png)

<a name="set-up-resource-logs"></a>

## <a name="set-up-azure-monitor-logs"></a>Azure Monitor 로그 설정

통합 계정에서 직접 Azure Monitor 로깅을 사용 하도록 설정할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에서 통합 계정을 찾고 선택합니다.

   ![통합 계정 찾기 및 선택](./media/monitor-b2b-messages-log-analytics/find-integration-account.png)

1. 통합 계정의 메뉴에서 **모니터링**아래에 있는 **진단 설정**을 선택 합니다. **진단 설정 추가**를 선택 합니다.

   !["모니터링"에서 "진단 설정"을 선택 합니다.](./media/monitor-b2b-messages-log-analytics/monitor-diagnostics-settings.png)

1. 설정을 만들려면 다음 단계를 수행 합니다.

   1. 설정의 이름을 지정 합니다.

   1. **Log Analytics에 보내기**를 선택합니다.

   1. **구독**의 경우 Log Analytics 작업 영역에 연결 된 Azure 구독을 선택 합니다.

   1. **Log Analytics 작업 영역**에서 사용 하려는 작업 영역을 선택 합니다.

   1. **로그**에서 기록 하려는 이벤트 범주를 지정 하는 **IntegrationAccountTrackingEvents** 범주를 선택 합니다.

   1. 완료되면 **저장**을 선택합니다.

   예를 들면 다음과 같습니다. 

   ![Azure Monitor 로그를 설정 하 여 진단 데이터 수집](./media/monitor-b2b-messages-log-analytics/send-diagnostics-data-log-analytics-workspace.png)

<a name="view-message-status"></a>

## <a name="view-message-status"></a>메시지 상태 보기

논리 앱을 실행 한 후 Log Analytics 작업 영역에서 이러한 메시지에 대 한 상태 및 데이터를 볼 수 있습니다.

1. [Azure Portal](https://portal.azure.com) 검색 상자에서 Log Analytics 작업 영역을 찾아 엽니다.

1. 작업 영역의 메뉴에서 **작업 영역 요약**  >  **Logic Apps B2B**를 선택 합니다.

   ![작업 영역 요약 창](./media/monitor-b2b-messages-log-analytics/b2b-overview-messages-summary.png)

   > [!NOTE]
   > Logic Apps B2B 타일이 실행 후에 즉시 결과를 표시 하지 않는 경우 **새로 고침** 을 선택 하거나 잠시 기다린 후 다시 시도 하세요.

   기본적으로 **Logic Apps B2B** 타일은 일별 데이터를 표시합니다. 데이터 범위를 다른 간격으로 변경 하려면 페이지 맨 위에 있는 범위 컨트롤을 선택 합니다.

   ![간격 변경](./media/monitor-b2b-messages-log-analytics/change-summary-interval.png)

1. 메시지 상태 대시보드가 나타난 후 일별 데이터를 표시하는 특정 메시지 유형에 대한 자세한 정보를 볼 수 있습니다. **AS2**, **X12**또는 **EDIFACT**에 대 한 타일을 선택 합니다.

   ![메시지 상태 보기](./media/monitor-b2b-messages-log-analytics/workspace-summary-b2b-messages.png)

   선택한 타일에 대한 메시지 목록이 표시됩니다. 예를 들어 AS2 메시지 목록은 다음과 같습니다.

   ![AS2 메시지의 상태 및 세부 정보](./media/monitor-b2b-messages-log-analytics/as2-message-results-list.png)

   각 메시지 유형에 대한 속성에 대해 자세히 알아보려면 이러한 메시지 속성 설명을 참조하세요.

   * [AS2 메시지 속성](#as2-message-properties)
   * [X12 메시지 속성](#x12-message-properties)
   * [EDIFACT 메시지 속성](#EDIFACT-message-properties)

<!--
1. To view or export the inputs and outputs for specific messages, select those messages, and select **Download**. When you're prompted, save the .zip file to your local computer, and then extract that file.

   The extracted folder includes a folder for each selected message. If you set up acknowledgements, the message folder also includes files with acknowledgement details. Each message folder has at least these files:
   
   * Human-readable files with the input payload and output payload details
   * Encoded files with the inputs and outputs

   For each message type, you can find the folder and file name formats here:

   * [AS2 folder and file name formats](#as2-folder-file-names)
   * [X12 folder and file name formats](#x12-folder-file-names)
   * [EDIFACT folder and file name formats](#edifact-folder-file-names)

   ![Download message files](./media/monitor-b2b-messages-log-analytics/download-messages.png)

1. To view all actions that have the same run ID, on the **Log Search** page, select a message from the message list.

   You can sort these actions by column, or search for specific results.

   * To search results with prebuilt queries, select **Favorites**.

   * Learn [how to build queries by adding filters](../logic-apps/create-monitoring-tracking-queries.md). Or learn more about [how to find data with log searches in Azure Monitor logs](../azure-monitor/log-query/log-query-overview.md).

   * To change query in the search box, update the query with the columns and values that you want to use as filters.
-->

<a name="message-list-property-descriptions"></a>

## <a name="property-descriptions-and-name-formats-for-as2-x12-and-edifact-messages"></a>AS2, X12 및 EDIFACT 메시지에 대한 속성 설명 및 이름 형식

각 메시지 유형의 경우 다운로드한 메시지 파일에 대한 속성 설명 및 이름 형식은 다음과 같습니다.

<a name="as2-message-properties"></a>

### <a name="as2-message-property-descriptions"></a>AS2 메시지 속성 설명

다음은 각 AS2 메시지에 대한 속성 설명입니다.

| 속성 | 설명 |
|----------|-------------|
| **으로부터** | **수신 설정**에 지정된 게스트 파트너 또는 AS2 규약의 **송신 설정**에 지정된 호스트 파트너 |
| **수신자** | **수신 설정**에 지정된 호스트 파트너 또는 AS2 규약의 **송신 설정**에 지정된 게스트 파트너 |
| **논리 앱** | AS2 작업이 설정된 논리 앱 |
| **상태** | AS2 메시지 상태 <br>성공 = 유효한 AS2 메시지를 받거나 보냈습니다. MDN이 설정되지 않습니다. <br>성공 = 유효한 AS2 메시지를 받거나 보냈습니다. MDN이 설정 및 수신됩니다. 또는 MDN이 전송됩니다. <br>실패 = 유효하지 않은 AS2 메시지를 받았습니다. MDN이 설정되지 않습니다. <br>보류 중 = 유효한 AS2 메시지를 받거나 보냈습니다. MDN이 설정되었고 MDN을 기다립니다. |
| **돌아가기** | MDN 메시지 상태 <br>수락됨 = 양의 MDN을 받거나 보냈습니다. <br>보류 중 = MDN을 받거나 보낼 때까지 대기 중입니다. <br>거부됨 = 음의 MDN을 받거나 보냈습니다. <br>필요하지 않음 = 규약에 MDN이 설정되지 않습니다. |
| **방향** | AS2 메시지 방향 |
| **추적 ID** | 논리 앱의 모든 트리거와 작업을 상호 연결하는 ID |
| **메시지 ID** | AS2 메시지 헤더의 AS2 메시지 ID |
| **Timestamp** | AS2 작업이 메시지를 처리한 시간 |
|||

<!--
<a name="as2-folder-file-names"></a>

### AS2 name formats for downloaded message files

Here are the name formats for each downloaded AS2 message folder and files.

| Folder or file | Name format |
|----------------|-------------|
| Message folder | [sender]\_[receiver]\_AS2\_[correlation-ID]\_[message-ID]\_[timestamp] |
| Input, output, and if set up, acknowledgement files | **Input payload**: [sender]\_[receiver]\_AS2\_[correlation-ID]\_input_payload.txt </p>**Output payload**: [sender]\_[receiver]\_AS2\_[correlation-ID]\_output\_payload.txt </p></p>**Inputs**: [sender]\_[receiver]\_AS2\_[correlation-ID]\_inputs.txt </p></p>**Outputs**: [sender]\_[receiver]\_AS2\_[correlation-ID]\_outputs.txt |
|||
-->

<a name="x12-message-properties"></a>

### <a name="x12-message-property-descriptions"></a>X12 메시지 속성 설명

다음은 각 X12 메시지에 대한 속성 설명입니다.

| 속성 | 설명 |
|----------|-------------|
| **으로부터** | **수신 설정**에 지정된 게스트 파트너 또는 X12 규약의 **송신 설정**에 지정된 호스트 파트너 |
| **수신자** | **수신 설정**에 지정된 호스트 파트너 또는 X12 규약의 **송신 설정**에 지정된 게스트 파트너 |
| **논리 앱** | X12 작업이 설정된 논리 앱 |
| **상태** | X12 메시지 상태 <br>성공 = 유효한 X12 메시지를 받거나 보냈습니다. 기능 Ack가 설정되지 않습니다. <br>성공 = 유효한 X12 메시지를 받거나 보냈습니다. 기능 Ack가 설정되고 수신되었거나 기능 Ack가 전송되었습니다. <br>실패 = 유효하지 않은 X12 메시지를 받거나 보냈습니다. <br>보류 중 = 유효한 X12 메시지를 받거나 보냈습니다. 기능 Ack가 설정되었고 기능 Ack를 기다립니다. |
| **돌아가기** | 기능 Ack(997) 상태 <br>수락됨 = 양의 기능 Ack를 받거나 보냈습니다. <br>거부됨 = 음의 기능 Ack를 받거나 보냈습니다. <br>보류 중 = 기능 Ack를 기다렸지만 받지 못했습니다. <br>보류 중 = 기능 Ack를 생성했지만 파트너에게 보낼 수 없습니다. <br>필요하지 않음 = 기능 Ack가 설정되지 않습니다. |
| **방향** | X12 메시지 방향 |
| **추적 ID** | 논리 앱의 모든 트리거와 작업을 상호 연결하는 ID |
| **메시지 유형** | EDI X12 메시지 유형 |
| **ICN** | X12 메시지의 교환 컨트롤 번호 |
| **TSCN** | X12 메시지의 트랜잭션 집합 컨트롤 번호 |
| **Timestamp** | X12 작업이 메시지를 처리한 시간 |
|||

<!--
<a name="x12-folder-file-names"></a>

### X12 name formats for downloaded message files

Here are the name formats for each downloaded X12 message folder and files.

| Folder or file | Name format |
|----------------|-------------|
| Message folder | [sender]\_[receiver]\_X12\_[interchange-control-number]\_[global-control-number]\_[transaction-set-control-number]\_[timestamp] |
| Input, output, and if set up, acknowledgement files | **Input payload**: [sender]\_[receiver]\_X12\_[interchange-control-number]\_input_payload.txt </p>**Output payload**: [sender]\_[receiver]\_X12\_[interchange-control-number]\_output\_payload.txt </p></p>**Inputs**: [sender]\_[receiver]\_X12\_[interchange-control-number]\_inputs.txt </p></p>**Outputs**: [sender]\_[receiver]\_X12\_[interchange-control-number]\_outputs.txt |
|||
-->

<a name="EDIFACT-message-properties"></a>

### <a name="edifact-message-property-descriptions"></a>EDIFACT 메시지 속성 설명

다음은 각 EDIFACT 메시지에 대한 속성 설명입니다.

| 속성 | 설명 |
|----------|-------------|
| **으로부터** | **수신 설정**에 지정된 게스트 파트너 또는 EDIFACT 규약의 **송신 설정**에 지정된 호스트 파트너 |
| **수신자** | **수신 설정**에 지정된 호스트 파트너 또는 EDIFACT 규약의 **송신 설정**에 지정된 게스트 파트너 |
| **논리 앱** | EDIFACT 작업이 설정된 논리 앱 |
| **상태** | EDIFACT 메시지 상태 <br>성공 = 유효한 EDIFACT 메시지를 받거나 보냈습니다. 기능 Ack가 설정되지 않습니다. <br>성공 = 유효한 EDIFACT 메시지를 받거나 보냈습니다. 기능 Ack가 설정되고 수신되었거나 기능 Ack가 전송되었습니다. <br>실패 = 유효하지 않은 EDIFACT 메시지를 받거나 보냈습니다. <br>보류 중 = 유효한 EDIFACT 메시지를 받거나 보냈습니다. 기능 Ack가 설정되었고 기능 Ack를 기다립니다. |
| **돌아가기** | 기능 Ack (CONTRL) 상태 <br>수락됨 = 양의 기능 Ack를 받거나 보냈습니다. <br>거부됨 = 음의 기능 Ack를 받거나 보냈습니다. <br>보류 중 = 기능 Ack를 기다렸지만 받지 못했습니다. <br>보류 중 = 기능 Ack를 생성했지만 파트너에게 보낼 수 없습니다. <br>필요하지 않음 = 기능 Ack가 설정되지 않습니다. |
| **방향** | EDIFACT 메시지 방향 |
| **추적 ID** | 논리 앱의 모든 트리거와 작업을 상호 연결하는 ID |
| **메시지 유형** | EDIFACT 메시지 형식 |
| **ICN** | EDIFACT 메시지의 교환 컨트롤 번호 |
| **TSCN** | EDIFACT 메시지의 트랜잭션 집합 컨트롤 번호 |
| **Timestamp** | EDIFACT 작업이 메시지를 처리한 시간 |
|||

<!--
<a name="edifact-folder-file-names"></a>

### EDIFACT name formats for downloaded message files

Here are the name formats for each downloaded EDIFACT message folder and files.

| Folder or file | Name format |
|----------------|-------------|
| Message folder | [sender]\_[receiver]\_EDIFACT\_[interchange-control-number]\_[global-control-number]\_[transaction-set-control-number]\_[timestamp] |
| Input, output, and if set up, acknowledgement files | **Input payload**: [sender]\_[receiver]\_EDIFACT\_[interchange-control-number]\_input_payload.txt </p>**Output payload**: [sender]\_[receiver]\_EDIFACT\_[interchange-control-number]\_output\_payload.txt </p></p>**Inputs**: [sender]\_[receiver]\_EDIFACT\_[interchange-control-number]\_inputs.txt </p></p>**Outputs**: [sender]\_[receiver]\_EDIFACT\_[interchange-control-number]\_outputs.txt |
|||
-->

## <a name="next-steps"></a>다음 단계

* [모니터링 및 추적 쿼리 만들기](../logic-apps/create-monitoring-tracking-queries.md)
