---
title: 이벤트 대기 및 응답
description: Azure Logic Apps를 사용하여 서비스 엔드포인트의 이벤트를 기반으로 트리거, 일시 중지, 계속하는 워크플로를 자동화합니다.
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: conceptual
ms.date: 08/27/2020
tags: connectors
ms.openlocfilehash: cdbf853a96f319cb27c10136004a1398014e602f
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106109155"
---
# <a name="create-and-run-automated-event-based-workflows-by-using-http-webhooks-in-azure-logic-apps"></a>Azure Logic Apps에서 HTTP 웹후크를 사용하여 자동화된 이벤트 기반 워크플로 만들기 및 실행

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) 및 기본 제공 HTTP Webhook 커넥터를 사용하여 서비스 엔드포인트를 정기적으로 확인하거나 *폴링* 하는 대신 해당 엔드포인트를 구독하는 자동화된 작업 및 워크플로를 만들고, 특정 이벤트에 대기하고, 해당 이벤트를 기반으로 실행하는 워크플로를 만들 수 있습니다.

다음은 웹후크 기반 워크플로 예제입니다.

* 논리 앱 실행을 활성화하기 전에 [Azure Event Hub](https://github.com/logicappsio/EventHubAPI)에서 항목이 도착할 때까지 기다립니다.
* 워크플로를 계속하기 전에 승인을 기다립니다.

이 문서에서는 논리 앱이 서비스 엔드포인트에서 이벤트를 수신하고 응답할 수 있도록 웹후크 트리거와 웹후크 작업을 사용하는 방법을 보여 줍니다.

## <a name="how-do-webhooks-work"></a>웹후크는 어떻게 작동하나요?

웹후크 트리거는 이벤트 기반이므로 정기적으로 새 항목을 확인하거나 폴링하는 데 의존하지 않습니다. 웹후크 트리거로 시작하는 논리 앱을 저장하거나, 논리 앱을 사용 안 함에서 사용으로 변경하는 경우 웹후크 트리거는 지정된 서비스 엔드포인트로 *콜백 URL* 을 등록하여 해당 엔드포인트를 *구독* 합니다. 그런 다음 트리거는 해당 서비스 엔드포인트가 논리 앱 실행을 시작하는 URL을 호출할 때까지 기다립니다. [요청 트리거와](connectors-native-reqres.md)마찬가지로 논리 앱은 지정된 이벤트가 발생할 때 즉시 발생합니다. 트리거를 제거하고 논리 앱을 저장하거나, 논리 앱을 사용에서 사용 안 함으로 변경하는 경우 웹후크 트리거는 서비스 엔드포인트에서 *구독 취소* 합니다.

웹후크 작업은 이벤트 기반이기도 하며 지정된 서비스 엔드포인트로 *콜백 URL* 을 등록하여 해당 엔드포인트를 *구독* 합니다. 웹후크 작업은 논리 앱의 워크플로를 일시 중지하고 논리 앱이 실행을 계속하기 전에 서비스 엔드포인트가 URL을 호출할 때까지 대기합니다. 이 경우 웹후크 작업은 서비스 엔드포인트에서 *구독 취소* 합니다.

* 웹후크 작업이 성공적으로 완료되면
* 응답을 기다리는 동안 논리 앱 실행을 취소하는 경우
* 논리 앱 시간이 초과하기 전에

예를 들어 Office 365 Outlook 커넥터의 [**승인 메일 보내기**](connectors-create-api-office365-outlook.md) 작업은 이 패턴을 따르는 웹후크 작업의 예입니다. 이 패턴을 웹후크 동작을 통해 서비스로 확장할 수 있습니다.

자세한 내용은 다음 항목을 참조하세요.

* [웹후크 및 구독](../logic-apps/logic-apps-workflow-actions-triggers.md#webhooks-and-subscriptions)
* [웹후크를 지원하는 사용자 지정 API 만들기](../logic-apps/logic-apps-create-api-app.md)

이전에 SSL(Secure Sockets Layer) 또는 [Azure AD OAuth(Azure Active Directory Open Authentication)](../active-directory/develop/index.yml)로 알려진 [TLS(전송 계층 보안)](https://en.wikipedia.org/wiki/Transport_Layer_Security)과 같은 논리 앱에 대한 인바운드 호출을 위한 암호화, 보안, 권한 부여에 관한 자세한 내용은 [보안 액세스 및 데이터 - 요청 기반 트리거에 대한 인바운드 호출 액세스](../logic-apps/logic-apps-securing-a-logic-app.md#secure-inbound-requests)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 계정 및 구독 Azure 구독이 없는 경우 [체험 Azure 계정에 등록](https://azure.microsoft.com/free/)합니다.

* 상황에 따라 [논리 앱의 웹후크 트리거](../logic-apps/logic-apps-create-api-app.md#webhook-triggers)를 위한 웹후크 구독 및 구독 취소 패턴을 지원하는 이미 배포된 엔드포인트 또는 API의 URL 및 [논리 앱의 웹후크 작업](../logic-apps/logic-apps-create-api-app.md#webhook-actions)

* [논리 앱 만드는 방법](../logic-apps/quickstart-create-first-logic-app-workflow.md)에 관한 기본 지식 논리 앱을 처음 접하는 경우 [Azure Logic Apps란?](../logic-apps/logic-apps-overview.md)을 검토합니다.

* 대상 엔드포인트에서 특정 이벤트를 대기하려는 논리 앱. Http Webhook 트리거를 시작하려면 [빈 논리 앱을 만듭니다](../logic-apps/quickstart-create-first-logic-app-workflow.md). Http Webhook 동작을 사용하려면 원하는 트리거를 사용하여 논리 앱을 시작합니다. 이 예제에서는 첫 번째 단계로 HTTP 트리거를 사용합니다.

## <a name="add-an-http-webhook-trigger"></a>Http Webhook 트리거 추가

이 기본 제공 트리거는 대상 서비스의 구독 엔드포인트를 호출하고 대상 서비스로 콜백 URL을 등록합니다. 그런 다음 논리 앱은 대상 서비스가 콜백 URL에 `HTTP POST` 요청을 보낼 때까지 대기합니다. 이 이벤트가 발생하면 트리거가 발생하고 해당 요청의 모든 데이터를 워크플로로 전달합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 논리 앱 디자이너에서 빈 논리 앱을 엽니다.

1. 디자이너의 검색 상자에서 `http webhook`를 필터로 입력합니다. **트리거** 목록에서 **Http Webhook** 트리거를 선택합니다.

   ![Http Webhook 트리거 선택](./media/connectors-native-webhook/select-http-webhook-trigger.png)

   이 예에서는 트리거 이름을 `HTTP Webhook trigger`로 변경하여 단계의 이름에 설명이 포함되게 합니다. 또한 나중에 이 예제에서는 Http Webhook 작업을 추가하는데, 두 이름이 모두 고유해야 합니다.

1. 구독 호출 및 구독 취소 호출에 사용할 [Http Webhook 트리거 매개 변수](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger)를 위한 값을 제공합니다.

   이 예제에서 트리거는 구독 및 구독 취소 작업을 수행할 때 사용할 메서드, URI, 메시지 본문을 포함합니다.

   ![Http Webhook 트리거 매개 변수 입력](./media/connectors-native-webhook/http-webhook-trigger-parameters.png)

   | 속성 | 필수 | 설명 |
   |----------|----------|-------------|
   | **구독 - 메서드** | 예 | 대상 엔드포인트를 구독할 때 사용할 메서드입니다. |
   | **구독 - URI** | 예 | 대상 엔드포인트를 구독할 때 사용할 URL입니다. |
   | **구독 - 본문** | 예 | 구독 요청에 포함할 메시지 본문입니다. 이 예에는 논리 앱의 콜백 URL을 검색하는 `@listCallbackUrl()` 식을 사용하여 논리 앱인 구독자를 고유하게 식별하는 콜백 URL이 포함되어 있습니다. |
   | **구독 취소 - 메서드** | 예 | 대상 엔드포인트에서 구독을 해제할 때 사용할 메서드입니다. |
   | **구독 취소 - URI** | 예 | 대상 엔드포인트에서 구독을 취소하는 데 사용할 URL입니다. |
   | **구독 취소 - 본문** | 예 | 구독 취소 요청에 포함할 옵션 메시지 본문입니다. <p><p>**참고**: 이 속성은 `listCallbackUrl()` 함수 사용을 지원하지 않습니다. 그러나 해당 트리거는 대상 서비스가 구독자를 고유하게 식별하는 데 사용할 수 있는 `x-ms-client-tracking-id` 및 `x-ms-workflow-operation-name` 헤더를 자동으로 포함하고 보냅니다. |
   ||||

1. 다른 트리거 속성을 추가하려면 **새 매개 변수 추가** 목록을 엽니다.

   ![트리거 속성 추가](./media/connectors-native-webhook/http-webhook-trigger-add-properties.png)

   예를 들어 인증을 사용해야 하는 경우 **구독 - 인증** 및 **구독 취소 - 인증** 속성을 추가할 수 있습니다. Http Webhook에 사용 가능한 인증 형식에 관한 자세한 내용은 [아웃바운드 호출에 인증 추가](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)를 참조하세요.

1. 트리거가 발생할 때 실행되는 작업을 사용하여 논리 앱의 워크플로를 계속해서 작성합니다.

1. 마치면 논리 앱을 저장해야 합니다. 디자이너 도구 모음에서 **저장** 을 선택합니다.

   논리 앱을 저장하면 대상 서비스에서 구독 엔드포인트를 호출하고 콜백 URL을 등록합니다. 그런 다음 논리 앱은 대상 서비스가 콜백 URL에 `HTTP POST` 요청을 보낼 때까지 대기합니다. 이 이벤트가 발생하면 트리거가 발생하고 해당 요청의 모든 데이터를 워크플로로 전달합니다. 성공적으로 완료되면 트리거는 엔드포인트에서 구독을 취소하고 논리 앱에서 남은 워크플로를 계속합니다.

## <a name="add-an-http-webhook-action"></a>Http Webhook 작업 추가

이 기본 제공 작업은 대상 서비스의 구독 엔드포인트를 호출하고 대상 서비스로 콜백 URL을 등록합니다. 그런 다음 논리 앱은 일시 중지하고 대상 서비스가 콜백 URL에 `HTTP POST` 요청을 보낼 때까지 대기합니다. 이 이벤트가 발생하면 작업은 요청에 있는 모든 데이터를 워크플로로 전달합니다. 성공적으로 완료되면 작업은 엔드포인트에서 구독을 취소하고 논리 앱에서 남은 워크플로를 계속 실행합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. Logic Apps 디자이너에서 논리 앱을 엽니다.

   이 예제에서는 첫 번째 단계로 Http Webhook 트리거를 사용합니다.

1. Http Webhook 동작을 추가하려는 단계에서 **새 단계** 를 선택합니다.

   단계 사이에서 작업을 추가하려면 단계 사이에 있는 화살표 위로 포인터를 이동합니다. 표시되는 더하기 기호( **+** )를 선택한 다음, **작업 추가** 를 선택합니다.

1. 디자이너의 검색 상자에서 `http webhook`를 필터로 입력합니다. **작업** 목록에서 **Http Webhook** 작업을 선택합니다.

   ![Http Webhook 작업 선택](./media/connectors-native-webhook/select-http-webhook-action.png)

   이 예에서는 작업의 이름을 "Http Webhook 작업"으로 변경하여 단계의 이름에 설명이 포함되게 합니다.

1. Http Webhook 작업 매개 변수를 위한 값을 제공합니다. 이 매개 변수는 [Http Webhook 트리거 매개 변수와](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger) 비슷하며 구독 및 구독 취소 호출에 사용할 수 있습니다.

   이 예제에서 작업은 구독 및 구독 취소 작업을 수행할 때 사용할 메서드, URI, 메시지 본문을 포함합니다.

   ![HTTP 웹후크 작업 매개 변수 입력](./media/connectors-native-webhook/http-webhook-action-parameters.png)

   | 속성 | 필수 | 설명 |
   |----------|----------|-------------|
   | **구독 - 메서드** | 예 | 대상 엔드포인트를 구독할 때 사용할 메서드입니다. |
   | **구독 - URI** | 예 | 대상 엔드포인트를 구독할 때 사용할 URL입니다. |
   | **구독 - 본문** | 예 | 구독 요청에 포함할 메시지 본문입니다. 이 예에는 논리 앱의 콜백 URL을 검색하는 `@listCallbackUrl()` 식을 사용하여 논리 앱인 구독자를 고유하게 식별하는 콜백 URL이 포함되어 있습니다. |
   | **구독 취소 - 메서드** | 예 | 대상 엔드포인트에서 구독을 해제할 때 사용할 메서드입니다. |
   | **구독 취소 - URI** | 예 | 대상 엔드포인트에서 구독을 취소하는 데 사용할 URL입니다. |
   | **구독 취소 - 본문** | 예 | 구독 취소 요청에 포함할 옵션 메시지 본문입니다. <p><p>**참고**: 이 속성은 `listCallbackUrl()` 함수 사용을 지원하지 않습니다. 그러나 이 작업은 대상 서비스가 구독자를 고유하게 식별하는 데 사용할 수 있는 `x-ms-client-tracking-id` 및 `x-ms-workflow-operation-name` 헤더를 자동으로 포함하고 보냅니다. |
   ||||

1. 다른 작업 속성을 추가하려면 **새 매개 변수 추가** 목록을 엽니다.

   ![작업 속성 추가](./media/connectors-native-webhook/http-webhook-action-add-properties.png)

   예를 들어 인증을 사용해야 하는 경우 **구독 - 인증** 및 **구독 취소 - 인증** 속성을 추가할 수 있습니다. Http Webhook에 사용 가능한 인증 형식에 관한 자세한 내용은 [아웃바운드 호출에 인증 추가](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)를 참조하세요.

1. 마치면 논리 앱을 저장해야 합니다. 디자이너 도구 모음에서 **저장** 을 선택합니다.

   이제 이 작업이 실행되면 논리 앱은 대상 서비스에서 구독 엔드포인트를 호출하고 콜백 URL을 등록합니다. 그러면 논리 앱이 워크플로를 일시 중지하고 대상 서비스에서 콜백 URL에 `HTTP POST` 요청을 보낼 때까지 대기합니다. 이 이벤트가 발생하면 작업은 요청에 있는 모든 데이터를 워크플로로 전달합니다. 성공적으로 완료되면 작업은 엔드포인트에서 구독을 취소하고 논리 앱에서 남은 워크플로를 계속 실행합니다.

## <a name="trigger-and-action-outputs"></a>트리거 및 작업 출력

Http Webhook 트리거 또는 작업의 출력에 관한 자세한 내용은 다음과 같습니다.

| 속성 이름 | Type | Description |
|---------------|------|-------------|
| headers | object | 요청의 헤더 |
| 본문 | object | 요청의 본문 콘텐츠가 포함된 개체 |
| 상태 코드 | int | 요청의 상태 코드 |
||||

| 상태 코드 | Description |
|-------------|-------------|
| 200 | 확인 |
| 202 | 수락됨 |
| 400 | 잘못된 요청 |
| 401 | 권한 없음 |
| 403 | 사용할 수 없음 |
| 404 | 찾을 수 없음 |
| 500 | 내부 서버 오류. 알 수 없는 오류 발생. |
|||

## <a name="connector-reference"></a>커넥터 참조

서로 비슷한 트리거와 동작 매개 변수에 관한 자세한 내용은 [Http Webhook 매개 변수](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* [보안 액세스 및 데이터 - 요청 기반 트리거에 대한 인바운드 호출 액세스](../logic-apps/logic-apps-securing-a-logic-app.md#secure-inbound-requests)
* [Logic Apps용 커넥터](../connectors/apis-list.md)
