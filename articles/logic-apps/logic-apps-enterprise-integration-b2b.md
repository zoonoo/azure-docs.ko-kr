---
title: B2B 엔터프라이즈 통합을 위한 메시지 교환 시나리오
description: 엔터프라이즈 통합 팩을 사용하여 Azure Logic Apps에서 거래 파트너 간에 B2B 메시지 받기 및 보내기
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 10/02/2020
ms.openlocfilehash: e16cc8934407a5c54c84fd045c99e28116e656c9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93310499"
---
# <a name="receive-and-confirm--b2b-as2-messages-by-using-azure-logic-apps-and-enterprise-integration-pack"></a>Azure Logic Apps 및 엔터프라이즈 통합 팩을 사용하여 B2B AS2 메시지 받기 및 확인

거래 파트너 및 규약을 정의하는 통합 계정이 있는 경우 [엔터프라이즈 통합 팩](../logic-apps/logic-apps-enterprise-integration-overview.md)과 [Azure Logic Apps](../logic-apps/logic-apps-overview.md)를 사용하여 거래 파트너 간에 메시지를 교환하는 자동화된 B2B(비즈니스 간) 워크플로를 만들 수 있습니다. Azure Logic Apps는 AS2, X12, EDIFACT 및 RosettaNet 산업 표준 프로토콜을 지원하는 커넥터에서 작동합니다. 이러한 커넥터를 [Logic Apps에서 사용할 수 있는 다른 커넥터](../connectors/apis-list.md)(예: Salesforce 및 Office 365 Outlook)와 결합할 수도 있습니다.

이 문서에서는 요청 트리거를 사용하여 HTTP 요청을 수신하고, AS2 및 X12 작업을 사용하여 메시지 콘텐츠를 디코딩하고, 응답 작업을 사용하여 응답을 반환하는 논리 앱을 만드는 방법을 보여 줍니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 아직 구독이 없는 경우 [Azure 체험 계정에 가입](https://azure.microsoft.com/free/)합니다.

* [요청](../connectors/connectors-native-reqres.md) 트리거와 이어져 진행되는 다음 작업을 사용하여 B2B 워크플로를 만들 수 있는 빈 논리 앱

  * [AS2 디코딩](../logic-apps/logic-apps-enterprise-integration-as2.md)

  * [조건](../logic-apps/logic-apps-control-flow-conditional-statement.md) - AS2 디코딩 작업이 성공 또는 실패 여부에 따라 [응답](../connectors/connectors-native-reqres.md)을 보냅니다.

  * [X12 메시지 디코딩](../logic-apps/logic-apps-enterprise-integration-x12.md) 

  논리 앱을 처음 사용하는 경우 [Azure Logic Apps란?](../logic-apps/logic-apps-overview.md) 및 [빠른 시작: 첫 번째 논리 앱 만들기](../logic-apps/quickstart-create-first-logic-app-workflow.md)를 검토합니다.

* Azure 구독에 연결되고 논리 앱에 연결된 [통합 계정](./logic-apps-enterprise-integration-create-integration-account.md) 논리 앱과 통합 계정이 동일한 위치 또는 Azure 지역에 있어야 합니다.

* 해당 파트너에 대한 [AS2 및 X12 규약](logic-apps-enterprise-integration-agreements.md)과 함께 통합 계정에 이미 정의된 둘 이상의 [거래 파트너](../logic-apps/logic-apps-enterprise-integration-partners.md)

## <a name="add-the-request-trigger"></a>요청 트리거 추가

이 예제에서는 Azure Portal에서 논리 앱 디자이너를 사용하지만 Visual Studio의 논리 앱 디자이너에 대해 비슷한 단계를 따를 수 있습니다.

1. [Azure Portal](https://portal.azure.com)의 Logic Apps 디자이너에서 빈 논리 앱을 엽니다.

1. 검색 상자에 `when a http request`를 입력하고 **HTTP 요청을 수신할 때** 를 선택하여 트리거로 사용합니다.

   ![요청 트리거를 선택하여 논리 앱 워크플로 시작](./media/logic-apps-enterprise-integration-b2b/select-http-request-trigger.png)

1. X12 메시지가 플랫 파일이므로 **요청 본문 JSON 스키마** 상자를 비워 둡니다.

   !["요청 본문 JSON 스키마"를 비워 두기](./media/logic-apps-enterprise-integration-b2b/receive-trigger-message-body-json-schema.png)

1. 완료되면 디자이너 도구 모음에서 **저장** 을 선택합니다.

   이 단계에서는 논리 앱을 트리거하는 요청을 보내는 데 사용할 **HTTP POST URL** 을 생성합니다. 이 URL을 복사하려면 URL 옆에 있는 복사 아이콘을 선택합니다.

   ![호출을 받기 위해 요청 트리거에 대해 생성된 URL](./media/logic-apps-enterprise-integration-b2b/generated-url-request-trigger.png)

## <a name="add-the-as2-decode-action"></a>AS2 디코딩 작업 추가

이제 사용하려는 B2B 작업을 추가합니다. 이 예제에서는 AS2 및 X12 작업을 사용합니다.

1. 트리거 아래에서 **새 단계** 를 선택합니다. 트리거 세부 정보를 숨기려면 트리거의 제목 표시줄을 클릭합니다.

   ![논리 앱 워크플로에 단계 더 추가](./media/logic-apps-enterprise-integration-b2b/add-new-action-under-trigger.png)

1. **작업 선택** 아래의 검색 상자에 `as2 decode`를 입력하고 **AS2 디코딩(v2)** 을 선택합니다.

   !["AS2 디코딩(v2)"를 찾아 선택합니다.](./media/logic-apps-enterprise-integration-b2b/add-as2-decode-action.png)

1. **디코딩할 메시지** 속성에 대해 AS2 작업이 디코딩할 입력(HTTP 요청 트리거에서 받은 `body` 콘텐츠)을 입력합니다. 다음과 같은 여러 방법으로 이 콘텐츠를 동적 콘텐츠 목록에서 입력으로 지정하거나 식으로 지정할 수 있습니다.

   * 사용 가능한 트리거 출력을 표시하는 목록에서 선택하려면 **디코딩할 메시지** 상자 내부를 클릭합니다. 동적 콘텐츠 목록이 나타나면 **HTTP 요청을 받은 경우** 아래에서 **본문** 속성 값을 선택합니다. 예를 들면 다음과 같습니다.

     ![트리거에서 "본문" 값 선택](./media/logic-apps-enterprise-integration-b2b/select-body-content-from-trigger.png)

   * 트리거의 `body` 출력을 참조하는 식을 입력하려면 **디코딩할 메시지** 상자 내부를 클릭합니다. 동적 콘텐츠 목록이 표시되면 **식** 을 선택합니다. 식 편집기에서 여기에 나오는 식을 입력하고 **확인** 을 선택합니다.

     `triggerOutputs()['body']`

     또는 **디코딩할 메시지** 상자에 다음 식을 직접 입력합니다.

     `@triggerBody()`

     식은 **Body** 토큰으로 확인됩니다.

     ![트리거에서 확인된 본문 출력](./media/logic-apps-enterprise-integration-b2b/resolved-trigger-outputs-body-expression.png)

1. **메시지 헤더** 속성에 대해 AS2 작업에 필요한 헤더를 입력합니다. 이 헤더는 HTTP 요청 트리거에서 받은 `headers` 콘텐츠에 설명되어 있습니다.

   1. 트리거의 `headers` 출력을 참조하는 식을 입력하려면 **메시지 헤더를 텍스트 모드로 전환** 을 선택합니다.

      ![선택된 "메시지 헤더를 텍스트 모드로 전환"을 보여 주는 스크린샷](./media/logic-apps-enterprise-integration-b2b/as2-decode-switch-text-mode.png)

   1. **메시지 헤더** 상자 내부를 클릭합니다. 동적 콘텐츠 목록이 표시되면 **식** 을 선택합니다. 식 편집기에서 여기에 나오는 식을 입력하고 **확인** 을 선택합니다.

      `triggerOutputs()['Headers']`

      AS2 디코딩 작업에서 식은 이제 토큰으로 표시됩니다.

      !["메시지 헤더" 상자의 "@triggerOutputs()['Headers']" 토큰을 보여 주는 스크린샷](./media/logic-apps-enterprise-integration-b2b/as2-decode-message-header-expression.png)

   1. **헤더** 토큰으로 확인할 식 토큰을 가져오려면 디자이너와 코드 보기 간을 전환합니다. 이 단계를 수행하면 AS2 디코딩 작업이 다음 예제와 같이 표시됩니다.

      ![트리거에서 확인된 헤더 출력](./media/logic-apps-enterprise-integration-b2b/resolved-trigger-outputs-headers-expression.png)

## <a name="add-response-action-for-message-receipt-notification"></a>메시지 수신 알림에 대한 응답 작업 추가

메시지가 수신되었음을 거래 파트너에 알리려면 **응답** 작업을 사용하여 AS2 MDN(메시지 처리 알림)이 포함된 응답을 반환할 수 있습니다. **AS2 디코딩** 작업 직후에 이 작업을 추가하면 해당 작업이 실패할 경우 논리 앱은 처리를 계속하지 않습니다.

1. **AS2 디코딩** 작업 아래에서 **새 단계** 를 선택합니다.

1. **작업 선택** 및 검색 상자에서 **기본 제공** 을 선택합니다. 검색 상자에 `condition`를 입력합니다. **작업** 목록에서 **조건** 을 선택합니다.

   !["조건" 작업 추가](./media/logic-apps-enterprise-integration-b2b/add-condition-action.png)

   이제 경로를 포함하여 조건이 충족되었는지 여부에 대한 조건 셰이프가 나타납니다.

   ![빈 경로를 포함하는 조건 셰이프를 보여 주는 스크린샷](./media/logic-apps-enterprise-integration-b2b/added-condition-action.png)

1. 이제 평가할 조건을 지정합니다. **값 선택** 상자에 다음 식을 입력합니다.

   `@body('AS2_Decode')?['AS2Message']?['MdnExpected']`

   가운데 상자에서 비교 작업이 `is equal to`로 설정되었는지 확인합니다. 오른쪽 상자에 값 `Expected`를 입력합니다. 이 토큰으로 확인할 식을 가져오려면 디자이너와 코드 보기 간을 전환합니다.

   ![조건이 추가된 조건 셰이프를 보여 주는 스크린샷](./media/logic-apps-enterprise-integration-b2b/expression-for-evaluating-condition.png)

1. 이제 **AS2 디코딩** 작업 성공 여부를 반환하는 응답을 지정합니다.

   1. **AS2 디코딩** 작업이 성공하는 경우 **If true** 셰이프에서 **작업 추가** 를 선택합니다. **작업 선택** 아래의 검색 상자에 `response`를 입력하고 **응답** 을 선택합니다.

      !["응답" 작업 찾기 및 선택](./media/logic-apps-enterprise-integration-b2b/select-http-response-action.png)

   1. **AS2 디코딩** 작업의 출력에서 AS2 MDN에 액세스하려면 다음과 같은 식을 지정합니다.

      * **응답** 작업의 **헤더** 속성에 다음 식을 입력합니다.

        `@body('AS2_Decode')?['OutgoingMdn']?['OutboundHeaders']`

      * **응답** 작업의 **본문** 속성에 다음 식을 입력합니다.

        `@body('AS2_Decode')?['OutgoingMdn']?['Content']`

   1. 토큰으로 확인할 식을 가져오려면 디자이너와 코드 보기 간을 전환합니다.

      ![AS2 MDN에 액세스하기 위한 확인된 식](./media/logic-apps-enterprise-integration-b2b/response-action-success-resolved-expression.png)

   1. **AS2 디코딩** 작업이 실패하는 경우 **If false** 셰이프에서 **작업 추가** 를 선택합니다. **작업 선택** 아래의 검색 상자에 `response`를 입력하고 **응답** 을 선택합니다. 원하는 상태와 오류를 반환하도록 **응답** 작업을 설정합니다.

1. 논리 앱을 저장합니다.

## <a name="add-decode-x12-message-action"></a>X12 메시지 디코딩 작업 추가

1. 이제 **X12 메시지 디코딩** 작업을 추가합니다. **응답** 작업에서 **작업 추가** 를 선택합니다.

1. **작업 선택** 아래의 검색 상자에 `x12 decode`를 입력하고 **X12 메시지 디코딩** 를 선택합니다.

   !["X12 메시지 디코딩" 작업 찾기 및 선택](./media/logic-apps-enterprise-integration-b2b/add-x12-decode-action.png)

1. X12 작업 중에 연결 정보를 묻는 메시지가 표시되면 연결 이름을 입력하고 사용할 통합 계정을 선택한 다음, **만들기** 를 선택합니다.

   ![통합 계정에 대한 X12 연결 만들기](./media/logic-apps-enterprise-integration-b2b/create-x12-integration-account-connection.png)

1. 이제 X12 작업에 대한 입력을 지정합니다. 이 예제에서는 메시지 콘텐츠에 해당하는 AS2 작업의 출력을 사용하지만 이 콘텐츠는 JSON 개체 형식이며 base64로 인코딩됩니다. 따라서 이 콘텐츠를 문자열로 변환해야 합니다.

   **디코딩할 X12 플랫 파일 메시지** 상자에 다음 식을 입력하여 AS2 출력을 변환합니다.

   `@base64ToString(body('AS2_Decode')?['AS2Message']?['Content'])`

    이 토큰으로 확인할 식을 가져오려면 디자이너와 코드 보기 간을 전환합니다.

    ![base64로 인코딩된 콘텐츠를 문자열로 변환](./media/logic-apps-enterprise-integration-b2b/x12-decode-message-content.png)

1. 논리 앱을 저장합니다.

   예를 들어 이 논리 앱에 대한 추가 단계가 필요한 경우 메시지 콘텐츠를 디코딩하고 JSON 개체 형식으로 해당 콘텐츠를 출력하기 위해 논리 앱을 계속 빌드합니다.

이제 B2B 논리 앱 설정이 완료되었습니다. 실제 앱에서는 디코딩된 X12 데이터를 LOB(기간 업무) 앱 또는 데이터 스토리지에 저장하려고 할 수도 있습니다. 예제를 보려면 다음 문서를 참조하세요.

* [Azure Logic Apps에서 SAP 시스템에 연결](../logic-apps/logic-apps-using-sap-connector.md)
* [SSH 및 Azure Logic Apps를 사용하여 SFTP 파일 모니터링, 만들기 및 관리](../connectors/connectors-sftp-ssh.md)

자신의 LOB 앱을 연결하고 논리 앱에서 이러한 API를 사용하려면 추가 작업을 추가하거나 [사용자 지정 API를 작성](../logic-apps/logic-apps-create-api-app.md)하면 됩니다.

## <a name="next-steps"></a>다음 단계

* [들어오는 HTTPS 호출 수신 및 응답](../connectors/connectors-native-reqres.md)
* [B2B 엔터프라이즈 통합을 위한 AS2 메시지 교환](../logic-apps/logic-apps-enterprise-integration-as2.md)
* [B2B 엔터프라이즈 통합을 위한 X12 메시지 교환](../logic-apps/logic-apps-enterprise-integration-x12.md)
* [엔터프라이즈 통합 팩](../logic-apps/logic-apps-enterprise-integration-overview.md)에 대해 자세히 알아보기
