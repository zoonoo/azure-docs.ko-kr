---
title: 순차 호위 (convoy)를 사용 하 여 순서 대로 상관 관계가 지정 된 메시지 보내기
description: Azure Logic Apps에서 순차 호위 (convoy) 패턴을 사용 하 여 관련 메시지를 순서 대로 보냅니다 Azure Service Bus
services: logic-apps
ms.suite: integration
ms.reviewer: apseth, divswa, logicappspm
ms.topic: conceptual
ms.date: 05/29/2020
ms.openlocfilehash: 8c00d2e4f622bcfad7b2468013336f0d936e318c
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87048663"
---
# <a name="send-related-messages-in-order-by-using-a-sequential-convoy-in-azure-logic-apps-with-azure-service-bus"></a>Azure Service Bus에서 Azure Logic Apps 순차 호위 (convoy)를 사용 하 여 관련 메시지 보내기

특정 순서로 상관 관계가 지정 된 메시지를 보내야 하는 경우 [Azure Service Bus 커넥터](../connectors/connectors-create-api-servicebus.md)를 사용 하 여 [Azure Logic Apps](../logic-apps/logic-apps-overview.md) 를 사용할 때 [ *순차 호위 (convoy* ) 패턴](/azure/architecture/patterns/sequential-convoy) 을 따를 수 있습니다. 상관 관계가 지정 된 메시지에는 Service Bus [세션](../service-bus-messaging/message-sessions.md) 의 ID와 같은 해당 메시지 간의 관계를 정의 하는 속성이 있습니다.

예를 들어 "Session 1" 이라는 세션에 대해 10 개의 메시지가 있고 모두 동일한 [Service Bus 큐](../service-bus-messaging/service-bus-queues-topics-subscriptions.md)에 전송 되는 "session 2" 라는 세션에 대해 5 개의 메시지가 있다고 가정 합니다. 큐에서 메시지를 처리 하는 논리 앱을 만들어 "세션 1"의 모든 메시지가 단일 트리거 실행에 의해 처리 되 고 "Session 2"의 모든 메시지가 다음 트리거 실행에 의해 처리 되도록 할 수 있습니다.

![일반 순차 호위 (convoy) 패턴](./media/send-related-messages-sequential-convoy/sequential-convoy-pattern-general.png)

이 문서에서는 **service bus 세션을 사용 하 여 상호 관련 된 순차적 전달을** 사용 하 여이 패턴을 구현 하는 논리 앱을 만드는 방법을 보여 줍니다. 이 템플릿은 [Service Bus 큐](../service-bus-messaging/service-bus-queues-topics-subscriptions.md)에서 메시지를 수신 하는 **큐 (peek 잠금) 트리거에서 메시지를 받을 때** Service Bus 커넥터에서 시작 하는 논리 앱 워크플로를 정의 합니다. 이 논리 앱이 수행 하는 대략적인 단계는 다음과 같습니다.

* 트리거에서 Service Bus 큐에서 읽는 메시지에 따라 세션을 초기화 합니다.

* 현재 워크플로를 실행 하는 동안 큐에 있는 동일한 세션의 모든 메시지를 읽고 처리 합니다.

이 템플릿의 JSON 파일을 검토 하려면 [GitHub: service-bus-sessions.json](https://github.com/Azure/logicapps/blob/master/templates/service-bus-sessions.json)을 참조 하세요.

자세한 내용은 [순차 호위 (convoy) 패턴-Azure 아키텍처 클라우드 디자인 패턴](/azure/architecture/patterns/sequential-convoy)을 참조 하세요.

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독 구독이 없는 경우 [Azure 체험 계정에 등록](https://azure.microsoft.com/free/)합니다.

* 논리 앱에서 사용할 메시징 엔터티인 Service Bus 네임 스페이스 및 [Service Bus 큐](../service-bus-messaging/service-bus-queues-topics-subscriptions.md)입니다. 이러한 항목과 논리 앱은 동일한 Azure 구독을 사용 해야 합니다. 큐를 만들 때 **세션 사용** 을 선택 했는지 확인 합니다. 이러한 항목이 없는 경우 [Service Bus 네임 스페이스와 큐를 만드는 방법](../service-bus-messaging/service-bus-create-namespace-portal.md)에 대해 알아봅니다.

  [!INCLUDE [Warning about creating infinite loops](../../includes/connectors-infinite-loops.md)]

* 논리 앱을 만드는 방법에 대 한 기본 정보입니다. Azure Logic Apps를 처음 접하는 경우 빠른 시작을 시도 하 고 [첫 번째 자동화 된 워크플로를 만듭니다](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="permissions-connection-string"></a>

## <a name="check-access-to-service-bus-namespace"></a>Service Bus 네임 스페이스에 대 한 액세스 확인

논리 앱에 Service Bus 네임 스페이스에 액세스할 수 있는 권한이 있는지 확실 하지 않은 경우 해당 권한을 확인 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. Service Bus *네임 스페이스*를 찾아 선택 합니다.

1. 네임 스페이스 메뉴의 **설정**에서 **공유 액세스 정책**을 선택 합니다. **클레임**에서 해당 네임 스페이스에 대 한 **관리** 권한이 있는지 확인 합니다.

   ![Service Bus 네임 스페이스에 대 한 사용 권한 관리](./media/send-related-messages-sequential-convoy/check-service-bus-permissions.png)

1. 이제 Service Bus 네임 스페이스에 대 한 연결 문자열을 가져옵니다. 논리 앱에서 네임 스페이스에 대 한 연결을 만들 때 나중에이 문자열을 사용할 수 있습니다.

   1. **공유 액세스 정책** 창의 **정책**아래에서 **RootManageSharedAccessKey**을 선택 합니다.
   
   1. 기본 연결 문자열 옆에 있는 복사 단추를 선택 합니다. 나중에 사용할 연결 문자열을 저장합니다.

      ![Service Bus 네임스페이스 연결 문자열 복사](./media/send-related-messages-sequential-convoy/copy-service-bus-connection-string.png)

   > [!TIP]
   > 연결 문자열이 Service Bus 네임스페이스 또는 메시징 엔터티와 연결되어 있는지 확인하려면 연결 문자열을 검색하여 `EntityPath`  매개 변수를 찾습니다. 이 매개 변수를 찾은 경우 연결 문자열은 특정 엔터티에 대한 것이고 논리 앱에 사용할 올바른 문자열이 아닙니다.

## <a name="create-logic-app"></a>논리 앱 만들기

이 섹션에서는이 워크플로 패턴을 구현 하기 위한 트리거 및 동작을 포함 하는 **service bus 세션을 사용 하 여 상관 관계가 지정 된 순서 대로 배달을** 사용 하 여 논리 앱을 만듭니다. 또한 Service Bus 네임 스페이스에 대 한 연결을 만들고 사용 하려는 Service Bus 큐의 이름을 지정 합니다.

1. [Azure Portal](https://portal.azure.com)에서 빈 논리 앱을 만듭니다. Azure 홈 페이지에서 **리소스 만들기**  >  **통합**  >  **논리 앱**을 선택 합니다.

1. 템플릿 갤러리가 표시 되 면 비디오 및 일반 트리거 섹션을 지나서 스크롤합니다. **템플릿** 섹션에서 **service bus 세션을 사용 하 여 상관 관계가**지정 된 상관 관계를 제공 하는 템플릿을 선택 합니다.

   !["Service bus 세션을 사용 하 여 상호 관련 된 순서 전달" 템플릿 선택](./media/send-related-messages-sequential-convoy/select-correlated-in-order-delivery-template.png)

1. 확인 상자가 표시 되 면 **이 템플릿 사용**을 선택 합니다.

1. 논리 앱 디자이너의 **Service Bus** 셰이프에서 **계속**을 선택 하 고 **+** 셰이프에 표시 되는 더하기 기호 ()를 선택 합니다.

   !["계속"을 선택 하 여 Azure Service Bus에 연결](./media/send-related-messages-sequential-convoy/connect-to-service-bus.png)

1. 이제 다음 옵션 중 하나를 선택 하 여 Service Bus 연결을 만듭니다.

   * 이전에 Service Bus 네임 스페이스에서 복사한 연결 문자열을 사용 하려면 다음 단계를 수행 합니다.

     1. **수동으로 연결 정보 입력**을 선택 합니다.

     1. **연결 이름**에 연결 이름을 입력 합니다. **연결 문자열**에 대해 네임 스페이스 연결 문자열을 붙여넣고 **만들기**를 선택 합니다. 예를 들면 다음과 같습니다.

        ![연결 이름 입력 및 연결 문자열 Service Bus](./media/send-related-messages-sequential-convoy/provide-service-bus-connection-string.png)

        > [!TIP]
        > 이 연결 문자열이 없는 경우 [Service Bus 네임 스페이스 연결 문자열을 찾고 복사](#permissions-connection-string)하는 방법에 대해 알아봅니다.

   * 현재 Azure 구독에서 Service Bus 네임 스페이스를 선택 하려면 다음 단계를 수행 합니다.

     1. **연결 이름**에 연결 이름을 입력 합니다. **네임 스페이스 Service Bus**Service Bus 네임 스페이스를 선택 합니다. 예를 들면 다음과 같습니다.

        ![연결 이름을 입력 하 고 네임 스페이스 Service Bus 선택](./media/send-related-messages-sequential-convoy/create-service-bus-connection.png)

     1. 다음 창이 표시 되 면 Service Bus 정책을 선택 하 고 **만들기**를 선택 합니다.

        ![Service Bus 정책을 선택 하 고 "만들기"를 선택 합니다.](./media/send-related-messages-sequential-convoy/create-service-bus-connection-2.png)

1. 완료 되 면 **계속**을 선택 합니다.

   이제 논리 앱 디자이너에는 **서비스 버스 세션 템플릿을 사용 하 여 상호 관련 된 순차적 전달** 이 표시 됩니다. 여기에는 패턴을 따르는 오류 처리를 구현 하는 두 개의 범위를 비롯 하 여 트리거와 작업을 포함 하는 미리 채워진 워크플로가 포함 되어 있습니다 `Try-Catch` .

이제 템플릿의 트리거 및 작업에 대 한 자세한 정보를 확인 하거나 [논리 앱 템플릿에 대 한 값을 제공](#complete-template)하기 위해 계속 이동할 수 있습니다.

<a name="template-summary"></a>

## <a name="template-summary"></a>템플릿 요약

세부 정보가 축소 된 경우 **service bus 세션 템플릿을 사용 하 여 상관 관계가 지정 된 순차적 전달** 의 최상위 워크플로는 다음과 같습니다.

![템플릿의 최상위 워크플로](./media/send-related-messages-sequential-convoy/template-top-level-flow.png)

| 이름 | 설명 |
|------|-------------|
| **`When a message is received in a queue (peek-lock)`** | 지정 된 되풀이에 따라이 Service Bus 트리거는 지정 된 Service Bus 큐에서 메시지를 확인 합니다. 메시지가 큐에 있으면 트리거가 발생 하 여 워크플로 인스턴스를 만들고 실행 합니다. <p><p>용어 *피킹 (peeking) 잠금은* 트리거를 통해 큐에서 메시지를 검색 하는 요청을 보냅니다. 메시지가 있는 경우 트리거는 잠금 기간이 만료 될 때까지 해당 메시지에서 다른 처리가 발생 하지 않도록 메시지를 검색 하 고 잠급니다. 자세한 내용을 보려면 [세션을 초기화](#initialize-session)하십시오. |
| **`Init isDone`** | 이 [ **변수 초기화** 작업](../logic-apps/logic-apps-create-variables-store-values.md#initialize-variable) 은로 설정 된 부울 변수를 만들고 `false` 다음 조건이 true 인 경우를 나타냅니다. <p><p>-세션에서 읽을 수 있는 메시지가 더 이상 없습니다. <br>-현재 워크플로 인스턴스를 완료할 수 있도록 세션 잠금을 더 이상 갱신할 필요가 없습니다. <p><p>자세한 내용은 [세션 초기화](#initialize-session)를 참조 하세요. |
| **`Try`** | 이 [ **범위** 동작은](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md) 메시지를 처리 하기 위해 실행 되는 작업을 포함 합니다. 범위에서 문제가 발생 하는 경우 `Try` 후속 `Catch` **범위** 작업에서 해당 문제를 처리 합니다. 자세한 내용은 ["Try" 범위](#try-scope)를 참조 하세요. |
| **`Catch`**| 이 [ **범위** 동작은](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md) 이전 범위에서 문제가 발생 하는 경우 실행 되는 작업을 포함 합니다 `Try` . 자세한 내용은 ["Catch" 범위](#catch-scope)를 참조 하세요. |
|||

<a name="try-scope"></a>

### <a name="try-scope"></a>"시도" 범위

`Try`세부 정보가 축소 된 경우 [범위 작업](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md) 의 최상위 흐름은 다음과 같습니다.

!["시도" 범위 동작 워크플로](./media/send-related-messages-sequential-convoy/try-scope-action.png)

| 이름 | 설명 |
|------|-------------|
| **`Send initial message to topic`** | 이 작업을 큐의 세션에서 첫 번째 메시지를 처리 하려는 작업으로 바꿀 수 있습니다. 세션 ID는 세션을 지정 합니다. <p><p>이 템플릿의 경우 Service Bus 작업은 첫 번째 메시지를 Service Bus 토픽으로 보냅니다. 자세한 내용은 [초기 메시지 처리](#handle-initial-message)를 참조 하세요. |
| (병렬 분기) | 이 [병렬 분기 작업](../logic-apps/logic-apps-control-flow-branches.md) 은 두 개의 경로를 만듭니다. <p><p>-Branch #1: 메시지를 계속 처리 합니다. 자세한 내용은 [분기 #1: 큐에서 초기 메시지 완료](#complete-initial-message)를 참조 하세요. <p><p>-Branch #2: 오류가 발생 한 경우 메시지를 중단 하 고 다른 트리거에서 픽업을 위한 릴리스를 실행 합니다. 자세한 내용은 [분기 #2: 큐에서 초기 메시지 중단](#abandon-initial-message)을 참조 하세요. <p><p>두 경로는 나중에 **큐의 닫기 세션에서 조인 하 고 성공** 작업은 다음 행에 설명 되어 있습니다. |
| **`Close a session in a queue and succeed`** | 이 Service Bus 작업은 앞에서 설명한 분기를 조인 하 고 다음 이벤트 중 하나가 발생 한 후 큐의 세션을 닫습니다. <p><p>-워크플로에서 큐의 사용 가능한 메시지 처리를 완료 합니다. <br>-잘못 된 오류가 발생 하 여 워크플로가 초기 메시지를 중단 합니다. <p><p>자세한 내용은 [큐의 세션 닫기 및 성공](#close-session-succeed)을 참조 하세요. |
|||

<a name="complete-initial-message"></a>

#### <a name="branch-1-complete-initial-message-in-queue"></a>분기 #1: 큐의 초기 메시지 완료

| 이름 | 설명 |
|------|-------------|
| `Complete initial message in queue` | 이 Service Bus 작업은 성공적으로 검색 된 메시지를 완료로 표시 하 고 다시 처리 하지 않도록 메시지를 큐에서 제거 합니다. 자세한 내용은 [초기 메시지 처리](#handle-initial-message)를 참조 하세요. |
| `While there are more messages for the session in the queue` | 이 [ **until** 루프](../logic-apps/logic-apps-control-flow-loops.md#until-loop) 는 메시지가 존재 하거나 1 시간이 지날 때까지 메시지를 계속 받습니다. 이 루프의 동작에 대 한 자세한 내용은 [큐의 세션에 대 한 추가 메시지가](#while-more-messages-for-session)있는 경우를 참조 하세요. |
| **`Set isDone = true`** | 메시지가 더 이상 없으면이 [ **변수 설정** 동작이](../logic-apps/logic-apps-create-variables-store-values.md#set-variable) `isDone` 로 설정 `true` 됩니다. |
| **`Renew session lock until cancelled`** | 이 [ **until** 루프](../logic-apps/logic-apps-control-flow-loops.md#until-loop) 는 메시지가 존재 하거나 1 시간이 지날 때까지이 논리 앱에서 세션 잠금을 보유 하 고 있는지를 확인할 수 있습니다. 이 루프의 동작에 대 한 자세한 내용은 [취소 될 때까지 세션 잠금 갱신](#renew-session-while-messages-exist)을 참조 하세요. |
|||

<a name="abandon-initial-message"></a>

#### <a name="branch-2-abandon-initial-message-from-the-queue"></a>분기 #2: 큐에서 초기 메시지를 중단 합니다.

첫 번째 메시지를 처리 하는 작업이 실패 하는 경우 Service Bus 작업은 **큐에서 초기 메시지를 중단**하 고 다른 워크플로 인스턴스가 실행 되 고 처리 하기 위해 메시지를 해제 합니다. 자세한 내용은 [초기 메시지 처리](#handle-initial-message)를 참조 하세요.

<a name="catch-scope"></a>

### <a name="catch-scope"></a>"Catch" 범위

범위 내의 작업이 `Try` 실패 하는 경우 논리 앱은 여전히 세션을 닫아야 합니다. 범위 `Catch` [동작은](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md) 범위 작업으로 인해,, 또는 상태가 될 때 실행 됩니다 `Try` `Failed` `Skipped` `TimedOut` . 범위는 문제가 발생 한 세션 ID를 포함 하는 오류 메시지를 반환 하 고 논리 앱을 종료 합니다.

`Catch`세부 정보가 축소 된 경우 범위 작업의 최상위 흐름은 다음과 같습니다.

!["Catch" 범위 동작 워크플로](./media/send-related-messages-sequential-convoy/catch-scope-action.png)

| 이름 | 설명 |
|------|-------------|
| **`Close a session in a queue and fail`** | 이 Service Bus 작업은 세션 잠금이 열린 상태를 유지 하지 않도록 큐의 세션을 닫습니다. 자세한 내용은 [큐의 세션 닫기 및 실패](#close-session-fail)를 참조 하세요. |
| **`Find failure msg from 'Try' block`** | 이 [ **필터 배열** 작업](../logic-apps/logic-apps-perform-data-operations.md#filter-array-action) 은 `Try` 지정 된 조건에 따라 범위 내의 모든 작업에서 입력 및 출력을 사용 하 여 배열을 만듭니다. 이 경우이 작업은 상태를 발생 시킨 작업에서 출력을 반환 합니다 `Failed` . 자세한 내용은 [' Try ' 블록에서 실패 메시지 찾기](#find-failure-message)를 참조 하십시오. |
| **`Select error details`** | 이 [ **Select** 작업](../logic-apps/logic-apps-perform-data-operations.md#select-action) 은 지정 된 조건에 따라 JSON 개체를 포함 하는 배열을 만듭니다. 이러한 JSON 개체는 이전 작업에서 만든 배열의 값으로 빌드됩니다 `Find failure msg from 'Try' block` . 이 경우이 작업은 이전 작업에서 반환 된 오류 정보에서 만든 JSON 개체를 포함 하는 배열을 반환 합니다. 자세한 내용은 [오류 세부 정보 선택](#select-error-details)을 참조 하세요. |
| **`Terminate`** | 이 [ **종료** 작업](../logic-apps/logic-apps-workflow-actions-triggers.md#terminate-action) 은 워크플로 실행을 중지 하 고, 진행 중인 모든 작업을 취소 하 고, 나머지 작업을 건너뛰고, 지정 된 상태, 세션 ID 및 작업의 오류 결과를 반환 합니다 `Select error details` . 자세한 내용은 [논리 앱 종료](#terminate-logic-app)를 참조 하세요. |
|||

<a name="complete-template"></a>

## <a name="complete-the-template"></a>템플릿 완료

**Service bus 세션을 사용 하 여 상관 관계가 지정 된 순서 대로 배달** 에서 트리거 및 작업에 대 한 값을 제공 하려면 다음 단계를 수행 합니다. 논리 앱을 저장 하려면 먼저 별표 ()로 표시 되는 모든 필수 값을 제공 해야 **\*** 합니다.

<a name="initialize-session"></a>

### <a name="initialize-the-session"></a>세션 초기화

* **큐에서 메시지를 받을 때 (peek 잠금)** 트리거에서 다음과 같이 **세션 id** 속성을 사용 하 여 템플릿이 세션을 초기화할 수 있도록이 정보를 제공 합니다.

  !["큐에서 메시지를 수신 하는 경우 (보기-잠금)"에 대 한 Service Bus 트리거 세부 정보](./media/send-related-messages-sequential-convoy/service-bus-check-message-peek-lock-trigger.png)

  > [!NOTE]
  > 처음에는 논리 앱이 예상 보다 더 자주 실행 되지 않고 예상치 못한 청구 요금이 발생 하도록 폴링 간격이 3 분으로 설정 됩니다. 메시지 도착 시 논리 앱이 즉시 트리거되는 간격 및 빈도를 30 초로 설정 하는 것이 가장 좋습니다.

  | 속성 | 이 시나리오에 필요 합니다. | Value | 설명 |
  |----------|----------------------------|-------|-------------|
  | **큐 이름** | 예 | <*큐 이름*> | 이전에 만든 Service Bus 큐의 이름입니다. 이 예에서는 "Fabrikam-Service-Bus-큐"를 사용 합니다. |
  | **큐 유형** | 예 | **기본** | 기본 Service Bus 큐 |
  | **세션 id** | 예 | **다음 사용 가능** | 이 옵션은 Service Bus 큐에 있는 메시지의 세션 ID를 기반으로 하 여 각 트리거 실행에 대 한 세션을 가져옵니다. 다른 논리 앱 이나 다른 클라이언트에서이 세션과 관련 된 메시지를 처리할 수 없도록 세션도 잠깁니다. 워크플로의 후속 작업은이 문서의 뒷부분에 설명 된 대로 해당 세션과 연결 된 모든 메시지를 처리 합니다. <p><p>다른 **세션 id** 옵션에 대 한 자세한 내용은 다음과 같습니다. <p>- **None**: 기본 옵션으로, 세션을 생성 하지 않으며 순차 호위 (convoy) 패턴을 구현 하는 데 사용할 수 없습니다. <p>- **사용자 지정 값 입력**: 사용할 세션 id를 알고 있는 경우이 옵션을 사용 합니다 .이 옵션은 항상 해당 세션 id에 대해 트리거를 실행 하려고 합니다. <p>**참고**: Service Bus 커넥터는 Azure Service Bus에서 커넥터 캐시로 제한 된 수의 고유 세션을 저장할 수 있습니다. 세션 수가이 한도를 초과 하면 이전 세션이 캐시에서 제거 됩니다. 자세한 내용은 [Azure Logic Apps 및 Azure Service Bus를 사용 하 여 클라우드의 교환 메시지](../connectors/connectors-create-api-servicebus.md#connector-reference)를 참조 하세요. |
  | **간격** | 예 | <*간격 수*> | 메시지를 확인 하기 전 되풀이 사이의 시간 단위 수입니다. |
  | **빈도** | 예 | **초**, **분**, **시간**, **일**, **주** 또는 **월** | 메시지를 확인할 때 되풀이가 사용 되는 시간 단위입니다. <p>**팁**: **표준 시간대** 또는 **시작 시간**을 추가 하려면 **새 매개 변수 추가** 목록에서 이러한 속성을 선택 합니다. |
  |||||

  자세한 트리거 정보는 [Service Bus-큐에 메시지가 수신 되는 경우 (보기-잠금)](/connectors/servicebus/#when-a-message-is-received-in-a-queue-(peek-lock))를 참조 하세요. 트리거는 [Servicebusmessage](/connectors/servicebus/#servicebusmessage)를 출력 합니다.

세션을 초기화 한 후 워크플로는 **변수 초기화** 작업을 사용 하 여 처음에로 설정 된 부울 변수를 만들고 `false` 다음 조건이 true 인 경우를 나타냅니다. 

* 세션에서 읽을 수 있는 메시지가 더 이상 없습니다.

* 현재 워크플로 인스턴스를 완료할 수 있도록 세션 잠금을 더 이상 갱신할 필요가 없습니다.

!["Init isDone"에 대 한 "변수 초기화" 작업 세부 정보](./media/send-related-messages-sequential-convoy/init-is-done-variable.png)

그런 다음 **Try** 블록에서 워크플로는 읽은 첫 번째 메시지에 대해 작업을 수행 합니다.

<a name="handle-initial-message"></a>

### <a name="handle-the-initial-message"></a>초기 메시지 처리

첫 번째 작업은 자리 표시자 Service Bus 작업 인 **항목에 초기 메시지 보내기 항목**입니다 .이 작업은 큐에 있는 세션의 첫 번째 메시지를 처리 하려는 다른 작업으로 바꿀 수 있습니다. 세션 ID는 메시지가 시작 된 세션을 지정 합니다.

자리 표시자 Service Bus 작업은 **세션 Id** 속성으로 지정 된 Service Bus 토픽에 첫 번째 메시지를 보냅니다. 이렇게 하면 특정 세션과 연결 된 모든 메시지가 같은 항목으로 이동 합니다. 이 템플릿의 후속 동작에 대 한 모든 **세션 id** 속성은 동일한 세션 id 값을 사용 합니다.

!["항목에 초기 메시지 보내기"에 대 한 작업 세부 정보를 Service Bus.](./media/send-related-messages-sequential-convoy/send-initial-message-to-topic-action.png)

1. Service Bus 작업에서 **큐의 초기 메시지를 완료**하 고 Service Bus 큐의 이름을 입력 한 다음 작업의 다른 모든 기본 속성 값을 유지 합니다.

   !["큐의 초기 메시지 완료"에 대 한 작업 세부 정보를 Service Bus.](./media/send-related-messages-sequential-convoy/complete-initial-message-queue.png)

1. Service Bus 작업에서 큐의 **초기 메시지를 중단**하 고 Service Bus 큐의 이름을 입력 한 다음 작업의 다른 모든 기본 속성 값을 유지 합니다.

   !["큐에서 초기 메시지 중단"에 대 한 작업 세부 정보를 Service Bus.](./media/send-related-messages-sequential-convoy/abandon-initial-message-from-queue.png)

다음으로, **큐의 초기 메시지 완료** 작업을 따르는 작업에 필요한 정보를 제공 합니다. **큐 루프의 세션에 대 한 메시지가 더** 있지만에서 작업을 시작 합니다.

<a name="while-more-messages-for-session"></a>

### <a name="while-there-are-more-messages-for-the-session-in-the-queue"></a>큐의 세션에 대 한 메시지가 더 있습니다.

이 [ **until** 루프](../logic-apps/logic-apps-control-flow-loops.md#until-loop) 는 메시지가 큐에 있거나 1 시간이 지날 때까지 이러한 작업을 실행 합니다. 루프의 시간 제한을 변경 하려면 루프의 **Timeout** 속성 값을 편집 합니다.

* 메시지가 있는 동안 큐에서 추가 메시지를 가져옵니다.

* 남아 있는 메시지 수를 확인 합니다. 메시지가 여전히 있는 경우 계속 해 서 메시지를 처리 합니다. 메시지가 더 이상 없으면 워크플로는 변수를로 설정 하 `isDone` `true` 고 루프를 종료 합니다.

![Until 루프-큐에 있는 동안 메시지를 처리 합니다.](./media/send-related-messages-sequential-convoy/while-more-messages-for-session-in-queue.png)

1. Service Bus 작업에서 **세션에서 추가 메시지를 가져옵니다**Service Bus 큐의 이름을 제공 합니다. 그렇지 않으면 다른 모든 기본 속성 값을 작업에 유지 합니다.

   > [!NOTE]
   > 기본적으로 최대 메시지 수는로 설정 `175` 되지만이 제한은 Service Bus의 메시지 크기와 최대 메시지 크기 속성의 영향을 받습니다. 자세한 내용은 [큐의 메시지 크기](../service-bus-messaging/service-bus-quotas.md)를 참조 하십시오.

   ![Service Bus 작업-"세션에서 추가 메시지 가져오기"](./media/send-related-messages-sequential-convoy/get-additional-messages-from-session.png)

   다음으로 워크플로는 이러한 병렬 분기로 분할 됩니다.

   * 추가 메시지를 확인 하는 동안 오류 또는 오류가 발생 하는 경우 `isDone` 변수를로 설정 `true` 합니다.

   * 조건을 **받은 경우 메시지 처리** 는 남은 메시지 수가 0 인지 여부를 확인 합니다. False와 더 많은 메시지가 있는 경우 처리를 계속 합니다. True이 고 더 이상 메시지가 없으면 워크플로는 `isDone` 변수를로 설정 `true` 합니다.

   ![조건-메시지를 처리 합니다.](./media/send-related-messages-sequential-convoy/process-messages-if-any.png)

   **If false** 섹션에서 **for EACH** 루프는 FIFO (선입 선출)로 각 메시지를 처리 합니다. 루프의 **설정**에서 **동시성 제어** 설정은로 설정 `1` 되므로 한 번에 하나의 메시지만 처리 됩니다.

   !["For each" 루프-각 메시지를 한 번에 하나씩 처리 합니다.](./media/send-related-messages-sequential-convoy/for-each-additional-message.png)

1. Service Bus 작업의 경우 큐 **에 있는 메시지를 완료** 하 고 **큐에서 메시지를 중단**하 고 Service Bus 큐의 이름을 입력 합니다.

   ![작업 Service Bus-"큐에서 메시지 완료" 및 "큐에서 메시지 중단"](./media/send-related-messages-sequential-convoy/abandon-or-complete-message-in-queue.png)

   큐의 **세션에 대 한 메시지가 더** 있는 경우에는 워크플로는 `isDone` 변수를로 설정 `true` 합니다.

다음으로는 **취소 될 때까지 세션 잠금 갱신** 의 작업에 필요한 정보를 제공 합니다.

<a name="renew-session-while-messages-exist"></a>

### <a name="renew-session-lock-until-cancelled"></a>취소 될 때까지 세션 잠금 갱신

이 [ **until** 루프](../logic-apps/logic-apps-control-flow-loops.md#until-loop) 는 메시지를 큐에 저장 하는 동안 또는 이러한 작업을 실행 하 여 1 시간이 경과할 때까지이 논리 앱에서 세션 잠금을 보유 하 고 있는지를 확인할 수 있습니다. 루프의 시간 제한을 변경 하려면 루프의 **Timeout** 속성 값을 편집 합니다.

* 처리 중인 큐의 잠금 제한 시간 보다 25 초의 지연 시간 또는 시간 간격입니다. 가장 작은 잠금 기간은 30 초 이므로 기본값은 충분 합니다. 그러나 적절 하 게 조정 하 여 루프가 실행 되는 횟수를 최적화할 수 있습니다.

* `isDone`변수가로 설정 되었는지 여부를 확인 `true` 합니다.

  * `isDone`가로 설정 되지 않은 경우 `true` 워크플로는 여전히 메시지를 처리 하므로 워크플로는 큐의 세션에 대 한 잠금을 갱신 하 고 루프 상태를 다시 확인 합니다.

    Service Bus 작업에서 Service Bus 큐의 이름을 제공 하 고 [**큐의 세션에 대 한 잠금을 갱신**](#renew-lock-on-session)해야 합니다.

  * `isDone`가로 설정 된 경우 `true` 워크플로는 큐의 세션에 대 한 잠금을 갱신 하지 않고 루프를 종료 합니다.

  ![Until 루프-"취소 될 때까지 세션 잠금 갱신"](./media/send-related-messages-sequential-convoy/renew-lock-until-session-cancelled.png)

<a name="renew-lock-on-session"></a>

#### <a name="renew-lock-on-the-session-in-a-queue"></a>큐의 세션에 대 한 잠금 갱신

이 Service Bus 작업은 워크플로에서 메시지를 계속 처리 하는 동안 큐의 세션에 대 한 잠금을 갱신 합니다.

* Service Bus 작업에서 **큐의 세션에 대 한 잠금을 갱신**하 고 Service Bus 큐의 이름을 입력 합니다.

  ![Service Bus 작업-"큐의 세션에서 잠금 갱신"](./media/send-related-messages-sequential-convoy/renew-lock-on-session-in-queue.png)

다음으로 Service Bus 작업에 필요한 정보를 제공 하 **고 큐에서 세션을 닫은 후 성공**합니다.

<a name="close-session-succeed"></a>

### <a name="close-a-session-in-a-queue-and-succeed"></a>큐의 세션을 닫고 성공

이 Service Bus 작업을 수행 하면 워크플로에서 큐의 사용 가능한 모든 메시지 처리를 완료 하거나 워크플로가 초기 메시지를 중단 한 후 큐의 세션이 닫힙니다.

* Service Bus 작업에서 **큐의 세션을 닫고 성공 하 고**Service Bus 큐의 이름을 입력 합니다.

  ![Service Bus 작업-"큐에서 세션을 닫고 성공 합니다."](./media/send-related-messages-sequential-convoy/close-session-in-queue-succeed.png)

다음 섹션에서는 `Catch` 워크플로에서 발생 하는 오류 및 예외를 처리 하는 섹션의 작업에 대해 설명 합니다.

<a name="close-session-fail"></a>

### <a name="close-a-session-in-a-queue-and-fail"></a>큐의 세션을 닫고 실패 합니다.

이 Service Bus 작업은 항상 범위의 첫 번째 작업으로 실행 되 `Catch` 고 큐에서 세션을 닫습니다.

* Service Bus 작업에서 **큐의 세션을 닫고 실패**한 Service Bus 큐의 이름을 입력 합니다.

  ![Service Bus 작업-"큐의 세션을 닫고 실패 합니다."](./media/send-related-messages-sequential-convoy/close-session-in-queue-fail.png)

그런 다음, 워크플로는 `Try` 논리 앱이 발생 한 오류 또는 실패에 대 한 정보에 액세스할 수 있도록 범위 내 모든 작업의 입력 및 출력을 포함 하는 배열을 만듭니다.

<a name="find-failure-message"></a>

### <a name="find-failure-msg-from-try-block"></a>' Try ' 블록에서 실패 메시지 찾기

이 [ **필터 배열** 작업](../logic-apps/logic-apps-perform-data-operations.md#filter-array-action) 은 `Try` [ `result()` 함수](../logic-apps/workflow-definition-language-functions-reference.md#result)를 사용 하 여 지정 된 조건에 따라 범위 내 모든 작업의 입력 및 출력을 포함 하는 배열을 만듭니다. 이 경우이 작업은 `Failed` [ `equals()` 함수](../logic-apps/workflow-definition-language-functions-reference.md#equals) 및 [ `item()` 함수](../logic-apps/workflow-definition-language-functions-reference.md#item)를 사용 하 여 상태를 포함 하는 작업의 출력을 반환 합니다.

![필터 배열 작업-"Try ' 블록에서 실패 메시지 찾기"](./media/send-related-messages-sequential-convoy/find-failure-message.png)

이 작업에 대 한 JSON 정의는 다음과 같습니다.

```json
"Find_failure_msg_from_'Try'_block": {
   "inputs": {
      "from": "@Result('Try')",
      "where": "@equals(item()['status'], 'Failed')"
   },
   "runAfter": {
      "Close_the_session_in_the_queue_and_fail": [
         "Succeeded"
      ]
   },
   "type": "Query"
},
```

그런 다음 워크플로는 동작에서 반환 된 배열의 오류 정보를 포함 하는 JSON 개체를 사용 하 여 배열을 만듭니다 `Find failure msg from 'Try' block` .

<a name="select-error-details"></a>

### <a name="select-error-details"></a>오류 세부 정보 선택

이 [ **Select** 작업](../logic-apps/logic-apps-perform-data-operations.md#select-action) 은 이전 작업의 출력 인 입력 배열을 기반으로 하는 JSON 개체를 포함 하는 배열을 만듭니다 `Find failure msg from 'Try' block` . 특히이 작업은 배열의 각 개체에 대해 지정 된 속성만 포함 하는 배열을 반환 합니다. 이 경우 배열에 작업 이름 및 오류 결과 속성이 포함 됩니다.

![작업 선택-"오류 세부 정보 선택"](./media/send-related-messages-sequential-convoy/select-error-details.png)

이 작업에 대 한 JSON 정의는 다음과 같습니다.

```json
"Select_error_details": {
   "inputs": {
      "from": "@body('Find_failure_msg_from_''Try''_block')[0]['outputs']",
      "select": {
         "action": "@item()['name']",
         "errorResult": "@item()"
      }
   },
   "runAfter": {
      "Find_failure_msg_from_'Try'_block": [
         "Succeeded"
      ]
   },
   "type": "Select"
},
```

그런 다음 워크플로는 논리 앱 실행을 중지 하 고 발생 한 오류 또는 실패에 대 한 추가 정보와 함께 실행 상태를 반환 합니다.

<a name="terminate-logic-app"></a>

### <a name="terminate-logic-app-run"></a>논리 앱 실행 종료

이 [ **종료** 작업](../logic-apps/logic-apps-workflow-actions-triggers.md#terminate-action) 은 논리 앱 실행을 중지 하 고 `Failed` 작업의 세션 ID 및 오류 결과와 함께 논리 앱 실행의 상태로 반환 합니다 `Select error details` .

![작업을 종료 하 여 논리 앱 실행을 중지 합니다.](./media/send-related-messages-sequential-convoy/terminate-logic-app-run.png)

이 작업에 대 한 JSON 정의는 다음과 같습니다.

```json
"Terminate": {
   "description": "This Failure Termination only runs if the Close Session upon Failure action runs - otherwise the LA will be terminated as Success",
   "inputs": {
      "runError": {
         "code": "",
         "message": "There was an error processing messages for Session ID @{triggerBody()?['SessionId']}. The following error(s) occurred: @{body('Select_error_details')['errorResult']}"
         },
         "runStatus": "Failed"
      },
      "runAfter": {
         "Select_error_details": [
            "Succeeded"
         ]
      },
      "type": "Terminate"
   }
},
```

## <a name="save-and-run-logic-app"></a>논리 앱 저장 및 실행

템플릿을 완료 한 후 이제 논리 앱을 저장할 수 있습니다. 디자이너 도구 모음에서 **저장**을 선택합니다.

논리 앱을 테스트 하려면 Service Bus 큐로 메시지를 보냅니다. 

## <a name="next-steps"></a>다음 단계

* [Service Bus 커넥터의 트리거 및 작업](/connectors/servicebus/) 에 대 한 자세한 정보
