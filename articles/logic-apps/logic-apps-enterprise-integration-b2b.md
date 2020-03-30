---
title: B2B 엔터프라이즈 통합 시나리오에 대한 메시지 교환
description: 엔터프라이즈 통합 팩을 사용하여 Azure Logic Apps의 거래 파트너 간에 B2B 메시지 수신 및 전송
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 02/10/2020
ms.openlocfilehash: 01b2bd464db51e255930fe83a3f4321687322275
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77151203"
---
# <a name="receive-and-send-b2b-messages-by-using-azure-logic-apps-and-enterprise-integration-pack"></a>Azure 논리 앱 및 엔터프라이즈 통합 팩을 사용하여 B2B 메시지 수신 및 전송

거래 파트너 및 계약을 정의하는 통합 계정이 있는 경우 [엔터프라이즈 통합 팩과](../logic-apps/logic-apps-enterprise-integration-overview.md)함께 [Azure Logic Apps를](../logic-apps/logic-apps-overview.md) 사용하여 거래 파트너 간에 메시지를 교환하는 B2B(비즈니스 간 자동화된 비즈니스 워크플로)를 만들 수 있습니다. Azure 논리 앱은 AS2, X12, EDIFACT 및 RosettaNet 업계 표준 프로토콜을 지원하는 커넥터와 함께 작동합니다. 이러한 커넥터를 로직 앱(예: Salesforce 및 Office 365 [Outlook)에서 사용할 수 있는](../connectors/apis-list.md)다른 커넥터와 결합할 수도 있습니다.

이 문서에서는 요청 트리거를 사용하여 HTTP 요청을 수신하는 논리 앱을 만들고, AS2 및 X12 작업을 사용하여 메시지 콘텐츠를 디코딩한 다음 응답 작업을 사용하여 응답을 반환하는 방법을 보여 준다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 아직 구독이 없는 경우 [무료 Azure 계정에 등록합니다.](https://azure.microsoft.com/free/)

* 다음 작업 다음에 수행되는 [요청](../connectors/connectors-native-reqres.md) 트리거를 사용하여 B2B 워크플로를 만들 수 있도록 하는 빈 논리 앱입니다.

  * [AS2 디코딩](../logic-apps/logic-apps-enterprise-integration-as2.md)

  * AS2 디코딩 작업이 성공또는 실패하는지 여부에 따라 [응답을](../connectors/connectors-native-reqres.md) 보내는 [조건](../logic-apps/logic-apps-control-flow-conditional-statement.md)

  * [X12 메시지 디코딩](../logic-apps/logic-apps-enterprise-integration-x12.md) 

  논리 앱을 처음 접하는 경우 [Azure 논리 앱이란 무엇입니까?](../logic-apps/logic-apps-overview.md) 및 빠른 시작: 첫 번째 논리 앱 만들기 를 [검토합니다.](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Azure 구독과 연결되고 논리 앱에 연결된 [통합 계정입니다.](../logic-apps/logic-apps-enterprise-integration-accounts.md) 논리 앱과 통합 계정은 모두 동일한 위치 또는 Azure 지역에 있어야 합니다.

* 해당 파트너에 대한 [AS2 및 X12 계약과](logic-apps-enterprise-integration-agreements.md) 함께 통합 계정에 이미 정의된 거래 [파트너가](../logic-apps/logic-apps-enterprise-integration-partners.md) 두 개 이상 있습니다.

## <a name="add-request-trigger"></a>요청 트리거 추가

이 예제에서는 Azure 포털에서 논리 앱 디자이너를 사용하지만 Visual Studio의 논리 앱 디자이너에 대해 유사한 단계를 수행할 수 있습니다.

1. Azure [포털에서](https://portal.azure.com)논리 앱 디자이너에서 빈 논리 앱을 엽니다.

1. 검색 상자에서 을 `when a http request`입력하고 HTTP **요청을 트리거로** 사용할 때를 선택합니다.

   ![로직 앱 워크플로를 시작하려면 요청 트리거를 선택합니다.](./media/logic-apps-enterprise-integration-b2b/select-http-request-trigger.png)

1. X12 메시지가 플랫 파일이므로 **요청 본문 JSON 스키마** 상자를 비워 둡니다.

   !["요청 본문 JSON 스키마"를 비워 둡니다.](./media/logic-apps-enterprise-integration-b2b/receive-trigger-message-body-json-schema.png)

1. 작업이 완료되면 디자이너 도구 모음에서 **저장을**선택합니다.

   이 단계에서는 논리 앱을 트리거하는 요청을 보내는 데 사용할 **HTTP POST URL을** 생성합니다. 이 URL을 복사하려면 URL 옆에 있는 복사 아이콘을 선택합니다.

   ![호출을 수신하기 위해 요청 트리거에 대해 생성된 URL](./media/logic-apps-enterprise-integration-b2b/generated-url-request-trigger.png)

## <a name="add-as2-decode-action"></a>AS2 디코딩 작업 추가

이제 사용하려는 B2B 작업을 추가합니다. 이 예제에서는 AS2 및 X12 작업을 사용합니다.

1. 트리거 아래에서 **새 단계**를 선택합니다. 트리거 세부 정보를 숨기려면 트리거의 제목 표시줄을 클릭합니다.

   ![논리 앱 워크플로에 또 다른 단계 추가](./media/logic-apps-enterprise-integration-b2b/add-new-action-under-trigger.png)

1. **작업 선택에서**검색 상자에 을 `as2 decode`입력하고 **AS2 디코딩(v2)을**선택합니다.

   !["AS2 디코딩(v2)" 찾기 및 선택](./media/logic-apps-enterprise-integration-b2b/add-as2-decode-action.png)

1. 속성을 **디코딩하는 Message의** 경우 AS2 작업을 디코딩할 입력을 입력합니다.이 작업은 HTTP 요청 트리거에서 수신한 `body` 콘텐츠입니다. 동적 콘텐츠 목록에서 또는 식으로 이 콘텐츠를 입력으로 지정하는 여러 가지 방법이 있습니다.

   * 사용 가능한 트리거 출력을 표시하는 목록에서 선택하려면 메시지 내부를 클릭하여 **디코딩합니다.** 동적 콘텐츠 목록이 나타나면 **HTTP 요청이 수신될 때**에서 **Body** 속성 값을 선택합니다.

     ![트리거에서 "본문" 값 선택](./media/logic-apps-enterprise-integration-b2b/select-body-content-from-trigger.png)

   * 트리거의 `body` 출력을 참조하는 식을 입력하려면 메시지 내부를 클릭하여 **디코딩합니다.** 동적 콘텐츠 목록이 나타나면 **표현식**을 선택합니다. 식 편집기에서 표현식을 여기에 입력하고 **확인을**선택합니다.

     `triggerOutputs()['body']`

     또는 **디코딩할 메시지** 상자에서 이 식을 직접 입력합니다.

     `@triggerBody()`

     식은 **Body** 토큰으로 확인됩니다.

     ![트리거에서 바디 출력 확인](./media/logic-apps-enterprise-integration-b2b/resolved-trigger-outputs-body-expression.png)

1. Message **헤더** 속성의 경우 HTTP 요청 트리거에서 수신한 `headers` 콘텐츠에 의해 설명되는 AS2 작업에 필요한 헤더를 입력합니다.

   트리거의 `headers` 출력을 참조하는 식을 입력하려면 메시지 헤더 상자 내부를 **클릭합니다.** 동적 콘텐츠 목록이 나타나면 **표현식**을 선택합니다. 식 편집기에서 표현식을 여기에 입력하고 **확인을**선택합니다.

   `triggerOutputs()['Headers']`

   이 식을 이 토큰으로 확인하려면 디자이너와 코드 뷰 간에 전환합니다.

   ![트리거에서 해결된 헤더 출력](./media/logic-apps-enterprise-integration-b2b/resolved-trigger-outputs-headers-expression.png)

## <a name="add-response-action-for-message-receipt-notification"></a>메시지 수신 알림에 대한 응답 작업 추가

거래 업체에게 메시지가 수신되었음을 알리려면 응답 작업을 사용하여 AS2 메시지 처리 알림(MDN)이 포함된 **응답을** 반환할 수 있습니다. **AS2 디코딩** 작업 직후에 이 작업을 추가하면 해당 작업이 실패하면 논리 앱이 처리를 계속하지 않습니다.

1. **AS2 디코딩** 작업에서 **새 단계를**선택합니다.

1. **작업 선택에서**검색 상자에서 **기본 제공**을 선택합니다. 검색 상자에 `condition`를 입력합니다. **작업** 목록에서 **조건을**선택합니다.

   !["조건" 작업 추가](./media/logic-apps-enterprise-integration-b2b/add-condition-action.png)

   이제 조건이 충족되는지 여부에 대한 경로를 포함하여 조건 모양이 나타납니다.

   ![의사 결정 경로가 있는 조건 모양](./media/logic-apps-enterprise-integration-b2b/added-condition-action.png)

1. 이제 평가할 조건을 지정합니다. 값 선택 상자에서 다음 **식을** 입력합니다.

   `@body('AS2_Decode')?['AS2Message']?['MdnExpected']`

   중간 상자에서 비교 작업이 `is equal to`로 설정되어 있는지 확인합니다. 오른쪽 상자에 값을 `Expected`입력합니다. 이 토큰으로 해결할 식을 얻으려면 디자이너와 코드 보기 간에 전환합니다.

   ![의사 결정 경로가 있는 조건 모양](./media/logic-apps-enterprise-integration-b2b/expression-for-evaluating-condition.png)

1. 이제 **AS2 디코딩** 작업이 성공했는지 여부를 반환할 응답을 지정합니다.

   1. **AS2 디코딩** 작업이 성공하는 경우 If **true** 모양에서 **작업 추가를**선택합니다. **작업 선택에서**검색 상자에 를 `response`입력하고 **응답을**선택합니다.

      !['응답' 작업 찾기 및 선택](./media/logic-apps-enterprise-integration-b2b/select-http-response-action.png)

   1. **AS2 디코딩** 작업의 출력에서 AS2 MDN에 액세스하려면 다음 식을 지정합니다.

      * **응답** 작업의 헤더 속성에서 다음 표현식을 **입력합니다.**

        `@body('AS2_Decode')?['OutgoingMdn']?['OutboundHeaders']`

      * **응답** 작업의 **Body** 속성에서 다음 식을 입력합니다.

        `@body('AS2_Decode')?['OutgoingMdn']?['Content']`

   1. 토큰으로 해결할 식을 얻으려면 디자이너와 코드 보기 간에 전환하십시오.

      ![AS2 MDN에 액세스하기 위해 해결된 표현식](./media/logic-apps-enterprise-integration-b2b/response-action-success-resolved-expression.png)

   1. **AS2 디코딩** 작업이 실패한 경우 **False** 셰이프에서 **작업 추가를**선택합니다. **작업 선택에서**검색 상자에 를 `response`입력하고 **응답을**선택합니다. 원하는 상태 및 오류를 반환하도록 **응답** 작업을 설정합니다.

1. 논리 앱을 저장합니다.

## <a name="add-decode-x12-message-action"></a>디코딩 X12 메시지 작업 추가

1. 이제 **디코딩 X12 메시지** 작업을 추가합니다. **응답** 작업에서 **작업 추가를**선택합니다.

1. **작업 선택에서**검색 상자에 를 `x12 decode`입력하고 **X12 메시지 디코딩을**선택합니다.

   ![찾기 및 "X12 메시지 디코딩" 작업 선택](./media/logic-apps-enterprise-integration-b2b/add-x12-decode-action.png)

1. X12 동작에서 연결 정보를 묻는 메시지가 표시되면 연결 이름을 제공하고 사용할 통합 계정을 선택한 다음 **만들기를**선택합니다.

   ![통합 계정에 X12 연결 만들기](./media/logic-apps-enterprise-integration-b2b/create-x12-integration-account-connection.png)

1. 이제 X12 동작에 대한 입력을 지정합니다. 이 예제에서는 메시지 콘텐츠인 AS2 작업의 출력을 사용하지만 이 콘텐츠는 JSON 개체 형식이며 base64 인코딩되어 있습니다. 따라서 이 내용을 문자열로 변환해야 합니다.

   해독 **할 X12 Flat 파일 메시지에서** AS2 출력을 변환하려면 이 식을 입력합니다.

   `@base64ToString(body('AS2_Decode')?['AS2Message']?['Content'])`

    이 토큰으로 해결할 식을 얻으려면 디자이너와 코드 보기 간에 전환합니다.

    ![base64 인코딩된 콘텐츠를 문자열로 변환](./media/logic-apps-enterprise-integration-b2b/x12-decode-message-content.png)

1. 논리 앱을 저장합니다.

   예를 들어 이 논리 앱에 대한 추가 단계가 필요한 경우 메시지 콘텐츠를 디코딩하고 JSON 개체 형식의 해당 콘텐츠를 출력하려면 논리 앱을 계속 빌드합니다.

이제 B2B 논리 앱 설정이 완료되었습니다. 실제 앱에서는 LOB(업무 용) 앱 또는 데이터 저장소에 디코딩된 X12 데이터를 저장할 수 있습니다. 예를 들어 다음 문서를 참조하십시오.

* [Azure Logic Apps에서 SAP 시스템에 연결](../logic-apps/logic-apps-using-sap-connector.md)
* [SSH 및 Azure Logic Apps를 사용하여 SFTP 파일 모니터링, 만들기 및 관리](../connectors/connectors-sftp-ssh.md)

사용자 고유의 LOB 앱을 연결하고 논리 앱에서 이러한 API를 사용하려면 작업을 더 추가하거나 [사용자 지정 API를 작성할](../logic-apps/logic-apps-create-api-app.md)수 있습니다.

## <a name="next-steps"></a>다음 단계

* [수신 HTTPS 호출 수신 및 응답](../connectors/connectors-native-reqres.md)
* [B2B 엔터프라이즈 통합을 위한 AS2 메시지 교환](../logic-apps/logic-apps-enterprise-integration-as2.md)
* [B2B 엔터프라이즈 통합을 위한 X12 메시지 교환](../logic-apps/logic-apps-enterprise-integration-x12.md)
* 엔터프라이즈 통합 [팩에](../logic-apps/logic-apps-enterprise-integration-overview.md) 대해 자세히 알아보기