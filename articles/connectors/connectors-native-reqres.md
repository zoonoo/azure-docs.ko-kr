---
title: HTTPS를 사용하여 통화 수신 및 응답
description: Azure 논리 앱을 사용하여 외부 서비스의 인바운드 HTTPS 요청 처리
services: logic-apps
ms.suite: integration
ms.reviewers: klam, logicappspm
ms.topic: conceptual
ms.date: 03/12/2020
tags: connectors
ms.openlocfilehash: 1885d7f8713b3801ce0c9846b7a8509b3864032a
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656296"
---
# <a name="receive-and-respond-to-inbound-https-requests-in-azure-logic-apps"></a>Azure 논리 앱에서 인바운드 HTTPS 요청 수신 및 응답

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) 및 기본 제공 요청 트리거 또는 응답 작업을 사용하여 들어오는 HTTPS 요청을 수신하고 응답하는 자동화된 작업 및 워크플로를 만들 수 있습니다. 예를 들어 논리 앱을 사용할 수 있습니다.

* 온-프레미스 데이터베이스의 데이터에 대한 HTTPS 요청을 수신하고 응답합니다.
* 외부 웹후크 이벤트가 발생하면 워크플로를 트리거합니다.
* 다른 논리 앱에서 HTTPS 호출을 수신하고 응답합니다.

> [!NOTE]
> 요청 트리거는 *only* 수신 호출에 대한 전송 계층 보안(TLS) 1.2만 지원합니다. 나가는 호출은 TLS 1.0, 1.1 및 1.2를 계속 지원합니다. 자세한 내용은 [TLS 1.0 문제 해결을](https://docs.microsoft.com/security/solving-tls1-problem)참조하십시오.
>
> TLS 핸드셰이크 오류가 표시되면 TLS 1.2를 사용해야 합니다. 수신 호출의 경우 지원되는 암호 제품군은 다음과 같습니다.
>
> * TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
> * TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
> * TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
> * TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
> * TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
> * TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
> * TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
> * TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 구독이 없는 경우 무료 Azure [계정에 등록할](https://azure.microsoft.com/free/)수 있습니다.

* [논리 앱에](../logic-apps/logic-apps-overview.md)대한 기본 지식 . 논리 앱을 처음 접하는 경우 [첫 번째 논리 앱을 만드는 방법을](../logic-apps/quickstart-create-first-logic-app-workflow.md)알아봅니다.

<a name="add-request"></a>

## <a name="add-request-trigger"></a>요청 트리거 추가

이 기본 제공 트리거는 들어오는 HTTPS *요청만* 받을 수 있는 수동으로 호출 가능한 HTTPS 끝점을 만듭니다. 이 이벤트가 발생하면 트리거가 로직 앱을 실행하고 실행합니다. 트리거의 기본 JSON 정의 및 이 트리거를 호출하는 방법에 대한 자세한 내용은 Azure Logic Apps에서 HTTP 끝점을 사용하여 [요청 트리거 유형](../logic-apps/logic-apps-workflow-actions-triggers.md#request-trigger) 및 [호출, 트리거 또는 중첩 워크플로를](../logic-apps/logic-apps-http-endpoint.md)참조하십시오.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 빈 논리 앱을 만듭니다.

1. 논리 앱 디자이너가 열리면 검색 상자에 필터로 "http 요청"을 입력합니다. 트리거 목록에서 로직 앱 워크플로의 첫 번째 단계인 **HTTP 요청이 수신된** 경우 트리거를 선택합니다.

   ![요청 트리거 선택](./media/connectors-native-reqres/select-request-trigger.png)

   요청 트리거에는 다음 속성이 표시됩니다.

   ![요청 트리거](./media/connectors-native-reqres/request-trigger.png)

   | 속성 이름 | JSON 속성 이름 | 필수 | 설명 |
   |---------------|--------------------|----------|-------------|
   | **HTTP POST URL** | {없음} | 예 | 논리 앱을 저장한 후 생성되고 논리 앱을 호출하는 데 사용되는 끝점 URL |
   | **요청 바디 JSON 스키마** | `schema` | 예 | 들어오는 요청 본문에서 속성 및 값을 설명하는 JSON 스키마 |
   |||||

1. 요청 **본문 JSON 스키마** 상자에는 선택적으로 들어오는 요청의 본문을 설명하는 JSON 스키마를 입력합니다.

   ![JSON 스키마 예제](./media/connectors-native-reqres/provide-json-schema.png)

   디자이너는 이 스키마를 사용하여 요청의 속성에 대한 토큰을 생성합니다. 이렇게 하면 논리 앱이 트리거를 통해 요청의 데이터를 구문 분석, 사용 및 전달하여 워크플로로 전달할 수 있습니다.

   샘플 스키마는 다음과 같습니다.

   ```json
   {
      "type": "object",
      "properties": {
         "account": {
            "type": "object",
            "properties": {
               "name": {
                  "type": "string"
               },
               "ID": {
                  "type": "string"
               },
               "address": {
                  "type": "object",
                  "properties": {
                     "number": {
                        "type": "string"
                     },
                     "street": {
                        "type": "string"
                     },
                     "city": {
                        "type": "string"
                     },
                     "state": {
                        "type": "string"
                     },
                     "country": {
                        "type": "string"
                     },
                     "postalCode": {
                        "type": "string"
                     }
                  }
               }
            }
         }
      }
   }
   ```

   JSON 스키마를 입력하면 디자이너는 요청에 헤더를 `Content-Type` 포함하고 해당 헤더 값을 `application/json`로 설정하라는 미리 알림을 표시합니다. 자세한 내용은 [콘텐츠 형식 처리를](../logic-apps/logic-apps-content-type.md)참조하십시오.

   !["콘텐츠 유형" 헤더를 포함하도록 미리 알림](./media/connectors-native-reqres/include-content-type.png)

   이 헤더는 JSON 형식으로 표시됩니다.

   ```json
   {
      "Content-Type": "application/json"
   }
   ```

   예상 페이로드(데이터)를 기반으로 하는 JSON 스키마를 생성하려면 [JSONSchema.net](https://jsonschema.net)같은 도구를 사용하거나 다음 단계를 따를 수 있습니다.

   1. 요청 트리거에서 **샘플 페이로드를 사용하여 스키마 생성**을 선택합니다.

      ![페이로드에서 스키마 생성](./media/connectors-native-reqres/generate-from-sample-payload.png)

   1. 샘플 페이로드를 입력하고 **완료를 선택합니다.**

      ![페이로드에서 스키마 생성](./media/connectors-native-reqres/enter-payload.png)

      샘플 페이로드는 다음과 같습니다.

      ```json
      {
         "account": {
            "name": "Contoso",
            "ID": "12345",
            "address": { 
               "number": "1234",
               "street": "Anywhere Street",
               "city": "AnyTown",
               "state": "AnyState",
               "country": "USA",
               "postalCode": "11111"
            }
         }
      }
      ```

1. 추가 속성을 지정하려면 **새 매개변수 추가** 목록을 열고 추가할 매개변수를 선택합니다.

   | 속성 이름 | JSON 속성 이름 | 필수 | 설명 |
   |---------------|--------------------|----------|-------------|
   | **메서드** | `method` | 예 | 들어오는 요청이 논리 앱을 호출하는 데 사용해야 하는 메서드 |
   | **상대 경로** | `relativePath` | 예 | 논리 앱의 끝점 URL이 허용할 수 있는 매개 변수에 대한 상대 경로 |
   |||||

   이 예제는 **Method** 속성을 추가합니다.

   ![메서드 매개 변수 추가](./media/connectors-native-reqres/add-parameters.png)

   메서드 **속성은** 목록에서 메서드를 선택할 수 있도록 트리거에 나타납니다.

   ![Select 메서드](./media/connectors-native-reqres/select-method.png)

1. 이제 워크플로의 다음 단계로 다른 작업을 추가합니다. 트리거 아래에서 추가하려는 작업을 찾을 수 있도록 **다음 단계를** 선택합니다.

   예를 들어 사용자 지정된 응답을 반환하는 데 사용할 수 있는 [응답 작업을 추가하여](#add-response)요청에 응답할 수 있으며 이 항목의 나중에 설명합니다.

   논리 앱은 들어오는 요청을 1분 동안만 열어 두는 것입니다. 논리 앱 워크플로에 응답 작업이 포함되어 있다고 가정하면 이 시간이 지나도 논리 앱이 `504 GATEWAY TIMEOUT` 응답을 반환하지 않는 경우 논리 앱은 호출자에게 a를 반환합니다. 그렇지 않으면 논리 앱에 응답 작업이 포함되지 않은 경우 논리 `202 ACCEPTED` 앱은 즉시 호출자에게 응답을 반환합니다.

1. 완료되면 논리 앱을 저장합니다. 디자이너 도구 모음에서 **저장**을 선택합니다. 

   이 단계는 논리 앱을 트리거하는 요청을 보내는 데 사용할 URL을 생성합니다. 이 URL을 복사하려면 URL 옆에 있는 복사 아이콘을 선택합니다.

   ![로직 앱 트리거링에 사용할 URL](./media/connectors-native-reqres/generated-url.png)

1. 논리 앱을 트리거하려면 생성된 URL로 HTTP POST를 보냅니다. 예를 들어 [Postman](https://www.getpostman.com/)과 같은 도구를 사용할 수 있습니다.

### <a name="trigger-outputs"></a>트리거 출력

요청 트리거의 출력에 대한 자세한 내용은 다음과 같습니다.

| JSON 속성 이름 | 데이터 형식 | 설명 |
|--------------------|-----------|-------------|
| `headers` | Object | 요청의 헤더를 설명하는 JSON 개체 |
| `body` | Object | 요청의 본문 내용을 설명하는 JSON 개체 |
||||

<a name="add-response"></a>

## <a name="add-a-response-action"></a>응답 작업 추가

응답 작업을 사용하여 들어오는 HTTPS 요청에 페이로드(데이터)로 응답할 수 있지만 HTTPS 요청에 의해 트리거되는 논리 앱에서만 응답할 수 있습니다. 워크플로의 어느 지점에서나 응답 작업을 추가할 수 있습니다. 이 트리거의 기본 JSON 정의에 대한 자세한 내용은 [응답 작업 유형을](../logic-apps/logic-apps-workflow-actions-triggers.md#response-action)참조하십시오.

논리 앱은 들어오는 요청을 1분 동안만 열어 두는 것입니다. 논리 앱 워크플로에 응답 작업이 포함되어 있다고 가정하면 이 시간이 지나도 논리 앱이 `504 GATEWAY TIMEOUT` 응답을 반환하지 않는 경우 논리 앱은 호출자에게 a를 반환합니다. 그렇지 않으면 논리 앱에 응답 작업이 포함되지 않은 경우 논리 `202 ACCEPTED` 앱은 즉시 호출자에게 응답을 반환합니다.

> [!IMPORTANT]
> 응답 작업에 이러한 헤더가 포함된 경우 Logic Apps는 경고 또는 오류를 표시하지 않고 생성된 응답 메시지에서 이러한 헤더를 제거합니다.
>
> * `Allow`
> * `Content-*`이러한 예외를 `Content-Disposition`제외하고: . `Content-Encoding``Content-Type`
> * `Cookie`
> * `Expires`
> * `Last-Modified`
> * `Set-Cookie`
> * `Transfer-Encoding`
>
> 논리 앱은 이러한 헤더를 사용한 응답 작업이 있는 논리 앱을 저장하는 것을 막을 수는 없지만 Logic Apps는 이러한 헤더를 무시합니다.

1. 논리 앱 디자이너에서 응답 작업을 추가하려는 단계 아래에 **새 단계를**선택합니다.

   예를 들어 이전의 요청 트리거를 사용하여:

   ![새 단계 추가](./media/connectors-native-reqres/add-response.png)

   단계 간에 작업을 추가하려면 포인터를 해당 단계 사이의 화살표 위로 이동합니다. 표시되는 더하기**+** 기호 ()를 선택한 다음 **작업 추가를**선택합니다.

1. **작업 선택에서**검색 상자에 "응답"을 필터로 입력하고 **응답** 작업을 선택합니다.

   ![응답 작업 선택](./media/connectors-native-reqres/select-response-action.png)

   이 예제에서는 요청 트리거가 축소되어 단순합니다.

1. 응답 메시지에 필요한 값을 추가합니다. 

   일부 필드에서 상자 내부를 클릭하면 동적 콘텐츠 목록이 열립니다. 그런 다음 워크플로의 이전 단계에서 사용 가능한 출력을 나타내는 토큰을 선택할 수 있습니다. 이전 예제에 지정된 스키마의 속성이 이제 동적 콘텐츠 목록에 표시됩니다.

   예를 들어 헤더 상자의 경우 `Content-Type` 키 이름으로 포함하고 이 항목의 `application/json` 앞에서 설명한 대로 키 값을 **설정합니다.** **Body** 상자의 경우 동적 콘텐츠 목록에서 트리거 본문 출력을 선택할 수 있습니다.

   ![응답 작업 세부 정보](./media/connectors-native-reqres/response-details.png)

   JSON 형식으로 헤더를 보려면 **텍스트 보기로 전환 을**선택합니다.

   ![헤더 - 텍스트 보기로 전환](./media/connectors-native-reqres/switch-to-text-view.png)

   다음은 응답 작업에서 설정할 수 있는 속성에 대한 자세한 정보입니다. 

   | 속성 이름 | JSON 속성 이름 | 필수 | 설명 |
   |---------------|--------------------|----------|-------------|
   | **상태 코드** | `statusCode` | 예 | 응답에서 반환할 상태 코드 |
   | **헤더** | `headers` | 예 | 응답에 포함할 하나 이상의 헤더를 설명하는 JSON 개체 |
   | **본문** | `body` | 예 | 응답 본문 |
   |||||

1. 응답 본문에 대한 JSON 스키마와 같은 추가 속성을 지정하려면 **새 매개 변수 목록 추가를** 열고 추가할 매개 변수를 선택합니다.

1. 완료되면 논리 앱을 저장합니다. 디자이너 도구 모음에서 **저장**을 선택합니다. 

## <a name="next-steps"></a>다음 단계

* [Logic Apps용 커넥터](../connectors/apis-list.md)
