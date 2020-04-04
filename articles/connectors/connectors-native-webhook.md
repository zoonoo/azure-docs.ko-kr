---
title: 이벤트 대기 및 응답
description: Azure Logic Apps를 사용하여 서비스 끝점에서 이벤트를 트리거, 일시 중지 및 다시 시작하도록 하는 워크플로자동화
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/06/2020
tags: connectors
ms.openlocfilehash: 0a3fb9a8a72b384d2af4af38bdc382e541ddf535
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656291"
---
# <a name="create-and-run-automated-event-based-workflows-by-using-http-webhooks-in-azure-logic-apps"></a>Azure Logic Apps에서 HTTP 웹후크를 사용하여 자동화된 이벤트 기반 워크플로를 만들고 실행합니다.

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) 및 기본 제공 HTTP Webhook 커넥터를 사용하면 논리 앱을 빌드하여 HTTP 또는 HTTPS 끝점에서 발생하는 특정 이벤트를 기반으로 대기 및 실행되는 워크플로를 자동화할 수 있습니다. 예를 들어 워크플로를 트리거하고 지정된 작업을 실행하기 전에 해당 끝점을 정기적으로 검사하거나 *폴링하는* 대신 특정 이벤트를 대기하여 서비스 끝점을 모니터링하는 논리 앱을 만들 수 있습니다.

다음은 이벤트 기반 워크플로우의 예입니다.

* 논리 앱 실행을 트리거하기 전에 [Azure Event Hub에서](https://github.com/logicappsio/EventHubAPI) 항목이 도착할 때까지 기다립니다.
* 워크플로를 계속하기 전에 승인을 기다립니다.

## <a name="how-do-webhooks-work"></a>웹후크는 어떻게 작동하나요?

HTTP 웹후크 트리거는 이벤트 기반이며 새 항목에 대해 정기적으로 확인하거나 폴링하는 데 의존하지 않습니다. 웹후크 트리거로 시작하는 논리 앱을 저장하거나 논리 앱을 사용 안 함에서 사용하도록 변경할 때 웹후크 *트리거는* 해당 서비스 또는 끝점에 *콜백 URL을* 등록하여 특정 서비스 또는 끝점을 구독합니다. 그런 다음 트리거는 해당 서비스 또는 끝점이 논리 앱 실행을 시작하는 URL을 호출할 때까지 기다립니다. [요청 트리거와](connectors-native-reqres.md)마찬가지로 논리 앱은 지정된 이벤트가 발생하면 즉시 발생합니다. 트리거를 제거하고 논리 앱을 저장하거나 논리 앱을 사용 설정에서 사용 해제로 변경하는 경우 트리거가 서비스 또는 끝점에서 *구독을 취소합니다.*

HTTP 웹후크 작업은 이벤트 기반이며 해당 서비스 또는 끝점에 *콜백 URL을* 등록하여 특정 서비스 또는 끝점을 *구독합니다.* webhook 작업은 논리 앱의 워크플로를 일시 중지하고 논리 앱이 실행되기 전에 서비스 또는 끝점이 URL을 호출할 때까지 기다립니다. 다음과 같은 경우 작업 논리 앱은 서비스 또는 끝점에서 *구독을 취소합니다.*

* 웹후크 작업이 성공적으로 완료되면
* 응답을 기다리는 동안 논리 앱 실행이 취소된 경우
* 논리 앱이 시간 시간 단축되기 전에

예를 들어 Office 365 Outlook 커넥터의 [**승인 전자 메일 보내기**](connectors-create-api-office365-outlook.md) 작업은 이 패턴을 따르는 웹후크 작업의 예입니다. 웹후크 작업을 사용하여 이 패턴을 모든 서비스로 확장할 수 있습니다.

> [!NOTE]
> Logic Apps는 HTTP 웹후크 트리거 또는 동작으로 다시 호출을 받을 때 전송 계층 보안(TLS) 1.2를 적용합니다. TLS 핸드셰이크 오류가 표시되면 TLS 1.2를 사용해야 합니다. 수신 호출의 경우 지원되는 암호 제품군은 다음과 같습니다.
>
> * TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
> * TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
> * TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
> * TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
> * TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
> * TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
> * TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
> * TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256

자세한 내용은 다음 항목을 참조하세요.

* [HTTP 웹후크 트리거 매개 변수](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger)
* [웹후크 및 구독](../logic-apps/logic-apps-workflow-actions-triggers.md#webhooks-and-subscriptions)
* [웹후크를 지원하는 사용자 지정 API 만들기](../logic-apps/logic-apps-create-api-app.md)

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 Azure 구독이 없는 경우 [체험 Azure 계정에 등록](https://azure.microsoft.com/free/)합니다.

* [로직 앱의 웹후크 트리거](../logic-apps/logic-apps-create-api-app.md#webhook-triggers) 또는 [논리 앱의 웹후크 작업에](../logic-apps/logic-apps-create-api-app.md#webhook-actions) 대한 웹후크 구독 및 구독 취소 패턴을 지원하는 이미 배포된 엔드포인트 또는 API에 대한 URL

* [논리 앱 만드는 방법](../logic-apps/quickstart-create-first-logic-app-workflow.md)에 관한 기본 지식 로직 앱을 새로 접하는 경우 [Azure 논리 앱이란 무엇입니까?](../logic-apps/logic-apps-overview.md)

* 대상 끝점에서 특정 이벤트를 기다릴 논리 앱입니다. HTTP Webhook 트리거로 시작하려면 [빈 논리 앱을 만듭니다.](../logic-apps/quickstart-create-first-logic-app-workflow.md) HTTP Webhook 작업을 사용하려면 원하는 트리거로 논리 앱을 시작합니다. 이 예제에서는 HTTP 트리거를 첫 번째 단계로 사용합니다.

## <a name="add-an-http-webhook-trigger"></a>HTTP 웹후크 트리거 추가

이 기본 제공 트리거는 대상 서비스의 구독 끝점을 호출하고 대상 서비스에 콜백 URL을 등록합니다. 그런 다음 논리 앱은 대상 서비스가 `HTTP POST` 콜백 URL로 요청을 보낼 때까지 기다립니다. 이 이벤트가 발생하면 트리거가 트리거에 따라 요청의 모든 데이터를 트리거에 전달합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 논리 앱 디자이너에서 빈 논리 앱을 엽니다.

1. 디자이너의 검색 상자에 필터로 `http webhook` 입력합니다. 트리거 목록에서 **HTTP 웹후크** 트리거를 **선택합니다.**

   ![HTTP 웹후크 트리거 선택](./media/connectors-native-webhook/select-http-webhook-trigger.png)

   이 예제는 트리거의 `HTTP Webhook trigger` 이름을 더 설명적인 이름을 지정하도록 변경합니다. 또한 이 예제에서는 나중에 HTTP Webhook 작업을 추가하고 두 이름은 모두 고유해야 합니다.

1. 구독 및 구독 취소 호출에 사용할 [HTTP Webhook 트리거 매개 변수에](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger) 대한 값을 제공합니다.

   이 예제에서 트리거에는 구독 및 구독 취소 작업을 수행할 때 사용할 메서드, URI 및 메시지 본문이 포함됩니다.

   ![HTTP 웹후크 트리거 매개 변수 입력](./media/connectors-native-webhook/http-webhook-trigger-parameters.png)

   | 속성 | 필수 | 설명 |
   |----------|----------|-------------|
   | **구독 - 방법** | 예 | 대상 끝점에 가입할 때 사용하는 방법 |
   | **구독 - URI** | 예 | 대상 끝점에 가입하는 데 사용할 URL |
   | **구독 - 본문** | 예 | 구독 요청에 포함할 모든 메시지 본문입니다. 이 예제에는 `@listCallbackUrl()` 논리 앱의 콜백 URL을 검색하기 위해 식을 사용하여 논리 앱인 구독자를 고유하게 식별하는 콜백 URL이 포함됩니다. |
   | **구독 취소 - 방법** | 예 | 대상 끝점에서 구독을 취소할 때 사용하는 방법 |
   | **구독 취소 - URI** | 예 | 대상 끝점에서 구독 취소에 사용할 URL |
   | **구독 취소 - 본체** | 예 | 구독 취소 요청에 포함할 선택적 메시지 본문 <p><p>**참고**: 이 속성은 `listCallbackUrl()` 함수를 사용하지 않습니다. 그러나 트리거에는 `x-ms-client-tracking-id` 헤더와 `x-ms-workflow-operation-name`대상 서비스가 구독자를 고유하게 식별하는 데 사용할 수 있는 헤더가 자동으로 포함되고 전송됩니다. |
   ||||

1. 다른 트리거 속성을 추가하려면 **새 매개 변수 목록 추가를** 엽니다.

   ![트리거 속성 추가](./media/connectors-native-webhook/http-webhook-trigger-add-properties.png)

   예를 들어 인증을 사용해야 하는 경우 구독 **- 인증** 및 **구독 취소 - 인증** 속성을 추가할 수 있습니다. HTTP Webhook에서 사용할 수 있는 인증 유형에 대한 자세한 내용은 [아웃바운드 호출에 대한 인증 추가를](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)참조하십시오.

1. 트리거가 발생할 때 실행되는 작업을 사용하여 논리 앱의 워크플로를 계속해서 작성합니다.

1. 작업이 완료되면 논리 앱을 저장해야 합니다. 디자이너 도구 모음에서 **저장**을 선택합니다.

   논리 앱을 저장하면 대상 서비스에서 구독 끝점이 호출되고 콜백 URL이 등록됩니다. 그런 다음 논리 앱은 대상 서비스가 `HTTP POST` 콜백 URL로 요청을 보낼 때까지 기다립니다. 이 이벤트가 발생하면 트리거가 트리거에 따라 요청의 모든 데이터를 트리거에 전달합니다. 이 작업이 성공적으로 완료되면 트리거가 끝점에서 구독을 취소하고 논리 앱이 나머지 워크플로를 계속 합니다.

## <a name="add-an-http-webhook-action"></a>HTTP 웹후크 작업 추가

이 기본 제공 작업은 대상 서비스의 구독 끝점을 호출하고 대상 서비스에 콜백 URL을 등록합니다. 그런 다음 논리 앱이 일시 중지되고 대상 `HTTP POST` 서비스가 콜백 URL로 요청을 보낼 때까지 기다립니다. 이 이벤트가 발생하면 작업은 요청의 모든 데이터를 워크플로에 전달합니다. 작업이 성공적으로 완료되면 작업이 끝점에서 구독을 취소하고 논리 앱이 나머지 워크플로를 계속 실행합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. Logic Apps 디자이너에서 논리 앱을 엽니다.

   이 예제에서는 HTTP 웹후크 트리거를 첫 번째 단계로 사용합니다.

1. HTTP Webhook 작업을 추가하려는 단계에서 **새 단계를**선택합니다.

   단계 사이에서 작업을 추가하려면 단계 사이에 있는 화살표 위로 포인터를 이동합니다. 표시되는 더하기**+** 기호 ()를 선택한 다음 **작업 추가를**선택합니다.

1. 디자이너의 검색 상자에 필터로 `http webhook` 입력합니다. **작업** 목록에서 **HTTP 웹후크** 작업을 선택합니다.

   ![HTTP 웹후크 작업 선택](./media/connectors-native-webhook/select-http-webhook-action.png)

   이 예제는 단계의 설명이 많은 이름을 가지도록 작업의 이름을 "HTTP Webhook 작업"으로 바꿉니다.

1. HTTP Webhook 트리거 매개 변수와 유사한 HTTP Webhook 작업 매개 변수에 대한 값을 제공하여 구독 및 구독 취소 호출에 사용할 수 [있습니다.](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger)

   이 예제에서는 구독 및 구독 취소 작업을 수행할 때 사용할 메서드, URI 및 메시지 본문이 작업에 포함됩니다.

   ![HTTP 웹후크 작업 매개 변수 입력](./media/connectors-native-webhook/http-webhook-action-parameters.png)

   | 속성 | 필수 | 설명 |
   |----------|----------|-------------|
   | **구독 - 방법** | 예 | 대상 끝점에 가입할 때 사용하는 방법 |
   | **구독 - URI** | 예 | 대상 끝점에 가입하는 데 사용할 URL |
   | **구독 - 본문** | 예 | 구독 요청에 포함할 모든 메시지 본문입니다. 이 예제에는 `@listCallbackUrl()` 논리 앱의 콜백 URL을 검색하기 위해 식을 사용하여 논리 앱인 구독자를 고유하게 식별하는 콜백 URL이 포함됩니다. |
   | **구독 취소 - 방법** | 예 | 대상 끝점에서 구독을 취소할 때 사용하는 방법 |
   | **구독 취소 - URI** | 예 | 대상 끝점에서 구독 취소에 사용할 URL |
   | **구독 취소 - 본체** | 예 | 구독 취소 요청에 포함할 선택적 메시지 본문 <p><p>**참고**: 이 속성은 `listCallbackUrl()` 함수를 사용하지 않습니다. 그러나 작업에는 자동으로 `x-ms-client-tracking-id` 헤더와 `x-ms-workflow-operation-name`대상 서비스가 구독자를 고유하게 식별하는 데 사용할 수 있는 헤더가 포함되고 전송됩니다. |
   ||||

1. 다른 작업 속성을 추가하려면 새 매개 변수 목록 **추가를** 엽니다.

   ![작업 속성 추가](./media/connectors-native-webhook/http-webhook-action-add-properties.png)

   예를 들어 인증을 사용해야 하는 경우 구독 **- 인증** 및 **구독 취소 - 인증** 속성을 추가할 수 있습니다. HTTP Webhook에서 사용할 수 있는 인증 유형에 대한 자세한 내용은 [아웃바운드 호출에 대한 인증 추가를](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)참조하십시오.

1. 작업이 완료되면 논리 앱을 저장해야 합니다. 디자이너 도구 모음에서 **저장**을 선택합니다.

   이제 이 작업이 실행되면 논리 앱이 대상 서비스의 구독 끝점을 호출하고 콜백 URL을 등록합니다. 그런 다음 논리 앱은 워크플로를 일시 중지하고 `HTTP POST` 대상 서비스가 콜백 URL로 요청을 보낼 때까지 기다립니다. 이 이벤트가 발생하면 작업은 요청의 모든 데이터를 워크플로에 전달합니다. 작업이 성공적으로 완료되면 작업이 끝점에서 구독을 취소하고 논리 앱이 나머지 워크플로를 계속 실행합니다.

## <a name="connector-reference"></a>커넥터 참조

서로 유사한 트리거 및 작업 매개 변수에 대한 자세한 내용은 [HTTP Webhook 매개 변수를](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger)참조하십시오.

### <a name="output-details"></a>출력 세부 정보

다음은 HTTP Webhook 트리거 또는 작업의 출력에 대한 자세한 정보입니다.

| 속성 이름 | Type | Description |
|---------------|------|-------------|
| headers | object | 요청의 헤더 |
| 본문 | object | JSON 개체 | 요청의 본문 콘텐츠가 있는 개체 |
| 상태 코드 | int | 요청의 상태 코드 |
|||

| 상태 코드 | 설명 |
|-------------|-------------|
| 200 | 확인 |
| 202 | 수락됨 |
| 400 | 잘못된 요청 |
| 401 | 권한 없음 |
| 403 | 사용할 수 없음 |
| 404 | 찾을 수 없음 |
| 500 | 내부 서버 오류. 알 수 없는 오류 발생. |
|||

## <a name="next-steps"></a>다음 단계

* 다른 [Logic Apps 커넥터](../connectors/apis-list.md)에 대해 알아봅니다.
