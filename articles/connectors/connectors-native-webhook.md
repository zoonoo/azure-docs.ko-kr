---
title: Azure Logic Apps에서 이벤트 기반 작업 및 워크플로 만들기
description: 트리거, 일시 중지 및 자동화 된 작업, 프로세스 및 Azure Logic Apps를 사용 하 여 끝점에서 발생 하는 이벤트를 기반으로 하는 워크플로 다시 시작
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 07/05/2019
tags: connectors
ms.openlocfilehash: c2658df185d4836210c496d2c46a00a3541257a2
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67541412"
---
# <a name="automate-event-based-tasks-and-workflows-by-using-http-webhooks-in-azure-logic-apps"></a>Azure Logic Apps에서 HTTP 웹 후크를 사용 하 여 이벤트 기반 작업 및 워크플로 자동화

사용 하 여 [Azure Logic Apps](../logic-apps/logic-apps-overview.md) 및 기본 제공 HTTP 웹 후크 커넥터를 기다린 후 논리 앱을 작성 하 여 HTTP 또는 HTTPS 끝점에서 발생 하는 특정 이벤트에 따라 실행 하는 워크플로 자동화할 수 있습니다. 예를 들어, 워크플로 트리거하 및 지정 된 작업을 실행 하지 않고 정기적으로 검사 하기 전에 특정 이벤트를 대기 하 여 서비스 끝점을 모니터링 하는 논리 앱을 만들 수 있습니다 또는 *폴링* 끝점입니다.

일부 예제에서는 이벤트 기반 워크플로 다음과 같습니다.

* 항목에서 도착을 대기는 [Azure Event Hub](https://github.com/logicappsio/EventHubAPI) 논리 앱 실행을 트리거하기 전에 합니다.
* 워크플로 계속 하기 전에 승인을 대기 합니다.

## <a name="how-do-webhooks-work"></a>웹 후크는 어떻게 작동 하나요?

HTTP 웹 후크 트리거는 이벤트 기반, 검사 또는 정기적으로 새 항목에 대 한 폴링에 종속 되지 않습니다는입니다. 시작 하는 웹 후크 트리거를 통해 또는 웹 후크 트리거 사용 안 함 상태에서 논리 앱을 변경 하면 논리 앱을 저장할 때 *구독* 특정 서비스에 등록 하 여 끝점을 *콜백URL* 해당 서비스 또는 끝점을 사용 하 여 합니다. 그런 다음 트리거는 서비스 또는 논리 앱 실행을 시작 하는 URL을 호출 하는 끝점을 기다립니다. 비슷합니다는 [요청 트리거](connectors-native-reqres.md), 지정된 된 이벤트가 발생할 때 즉시 논리 앱에서 발생 합니다. 트리거가 *구독 취소* 서비스 또는 끝점에서 트리거를 제거 하 고 논리 앱을 저장 하는 경우 또는에서 논리 앱을 변경 하면 설정 사용 안 함으로 설정 합니다.

HTTP 웹 후크 동작 이기도 이벤트 기반 및 *구독* 특정 서비스 또는 등록 하 여 끝점에는 *콜백 URL* 해당 서비스 또는 끝점을 사용 하 여 합니다. 웹 후크 동작을 논리 앱 워크플로 일시 중지 하 고 호출 실행 논리 앱 다시 시작 하기 전에 URL를 서비스 또는 끝점 때까지 대기 합니다. 작업 논리 앱 *구독 취소* 서비스 또는 이러한 경우에는 끝점에서:

* 웹 후크 동작을 성공적으로 완료 되 면
* 논리 앱 실행 응답을 기다리는 동안 취소 되 면
* 앱 제한 논리 전에

예를 들어 Office 365 Outlook 커넥터의 [ **승인 전자 메일 보내기** ](connectors-create-api-office365-outlook.md) 작업은이 패턴을 따르는 웹 후크 동작의 예입니다. 웹 후크 동작을 사용 하 여 모든 서비스에이 패턴을 확장할 수 있습니다.

자세한 내용은 다음 항목을 참조하세요.

* [HTTP 웹 후크 트리거 매개 변수](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger)
* [웹 후크 및 구독](../logic-apps/logic-apps-workflow-actions-triggers.md#webhooks-and-subscriptions)
* [웹 후크를 지 원하는 사용자 지정 Api 만들기](../logic-apps/logic-apps-create-api-app.md)

## <a name="prerequisites"></a>필수 조건

* Azure 구독. Azure 구독이 없는 경우 [체험 Azure 계정에 등록](https://azure.microsoft.com/free/)합니다.

* 이미 배포 된 끝점에 대 한 URL 또는 웹 후크를 지 원하는 API 구독 및 구독 취소 패턴에 대 한 [logic apps에서 웹 후크 트리거](../logic-apps/logic-apps-create-api-app.md#webhook-triggers) 하거나 [logic apps에서 웹 후크 작업](../logic-apps/logic-apps-create-api-app.md#webhook-actions) 적절 하 게

* [논리 앱 만드는 방법](../logic-apps/quickstart-create-first-logic-app-workflow.md)에 관한 기본 지식 논리 앱을 처음 접하는 경우 [Azure Logic Apps란?](../logic-apps/logic-apps-overview.md)을 검토합니다.

* 대상 끝점에서 특정 이벤트를 대기 하려는 논리 앱. HTTP 웹 후크 트리거를 사용 하 여 시작 [빈 논리 앱 만들기](../logic-apps/quickstart-create-first-logic-app-workflow.md)합니다. HTTP 웹 후크 작업을 사용 하려면 원하는 모든 트리거를 사용 하 여 논리 앱을 시작 합니다. 이 예제에서는 첫 번째 단계로 HTTP 트리거를 사용 합니다.

## <a name="add-an-http-webhook-trigger"></a>HTTP 웹 후크 트리거를 추가 합니다.

이 기본 제공 트리거 콜백 URL을 지정된 된 서비스를 사용 하 여 등록 하 고 해당 서비스가 해당 URL에 HTTP POST 요청을 보낼 때까지 대기 합니다. 이 이벤트가 발생 하면 트리거가 발생 하 고 즉시 논리 앱을 실행 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 논리 앱 디자이너에서 빈 논리 앱을 엽니다.

1. 디자이너에서 검색 상자에서 필터로 "http 웹 후크"를 입력 합니다. **트리거** 목록에서 선택 합니다 **HTTP 웹 후크** 트리거.

   ![HTTP 웹 후크 트리거를 선택 합니다.](./media/connectors-native-webhook/select-http-webhook-trigger.png)

   이 예의 단계 보다 설명적인 이름을 갖도록 "HTTP 웹 후크 트리거" 트리거를 이름을 바꿉니다. 또한 HTTP 웹 후크 작업을 나중에 추가 하는 예제 및 이름이 모두 고유 해야 합니다.

1. 에 대 한 값을 제공 합니다 [HTTP 웹 후크 트리거 매개 변수](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger) 구독에 대 한 사용 및 구독 취소 호출, 예를 들어 하려는:

   ![HTTP 웹 후크 트리거 매개 변수를 입력 합니다.](./media/connectors-native-webhook/http-webhook-trigger-parameters.png)

1. 다른 사용 가능한 매개 변수를 추가 하려면 열을 **새 매개 변수 추가** 목록 및 매개 변수를 선택 합니다.

   HTTP Webhook에 대 한 인증 유형 사용할 수 있는 방법에 대 한 자세한 내용은 참조 [인증 HTTP 트리거 및 작업](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication)합니다.

1. 트리거가 발생할 때 실행되는 작업을 사용하여 논리 앱의 워크플로를 계속해서 작성합니다.

1. 완료 하면 논리 앱을 저장 해야 하는 완료 합니다. 디자이너 도구 모음에서 선택 **저장할**합니다.

   논리 앱 저장 구독 끝점을 호출 하 고이 논리 앱을 트리거하는 콜백 URL을 등록 합니다.

1. 대상 서비스가 보내는 때마다 이제는 `HTTP POST` 콜백 URL을 논리 앱 실행에 대 한 요청 및 요청을 통해 전달 되는 모든 데이터를 포함 합니다.

## <a name="add-an-http-webhook-action"></a>HTTP 웹 후크 작업 추가

이 기본 제공 작업 콜백 URL을 지정된 된 서비스를 사용 하 여 등록 하 고 논리 앱 워크플로 일시 중지 및 해당 서비스가 해당 URL에 HTTP POST 요청을 보낼 때까지 대기 합니다. 이 이벤트가 발생 하면 작업을 논리 앱 실행 다시 시작 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. Logic Apps 디자이너에서 논리 앱을 엽니다.

   이 예제에서는 첫 번째 단계로 HTTP 웹 후크 트리거를 사용 합니다.

1. HTTP 웹 후크 동작을 추가 하려는 단계를 아래에서 선택 **새 단계**합니다.

   단계 사이에서 작업을 추가하려면 단계 사이에 있는 화살표 위로 포인터를 이동합니다. 더하기 기호 ( **+** ) 표시 하 고 선택한 **작업 추가**합니다.

1. 디자이너에서 검색 상자에서 필터로 "http 웹 후크"를 입력 합니다. **작업** 목록에서 선택 합니다 **HTTP 웹 후크** 작업 합니다.

   ![HTTP 웹 후크 작업 선택](./media/connectors-native-webhook/select-http-webhook-action.png)

   이 예제에서는 단계 보다 설명적인 이름을 갖도록를 "HTTP 웹 후크 작업" 작업을 이름을 바꿉니다.

1. 작업 매개 변수는 유사한 HTTP Webhook에 대 한 값을 제공 합니다 [HTTP 웹 후크 트리거 매개 변수](../logic-apps/logic-apps-workflow-actions-triggers.md##http-webhook-trigger) 구독에 대 한 사용 및 구독 취소 호출, 예를 들어 하려는:

   ![HTTP 웹 후크 작업 매개 변수를 입력 합니다.](./media/connectors-native-webhook/http-webhook-action-parameters.png)

   런타임 중 논리 앱이이 작업을 실행 하는 경우 구독 끝점을 호출 합니다. 논리 앱에서 다음 워크플로 일시 중지 하 고 보낼 대상 서비스에 대 한 대기를 `HTTP POST` 콜백 URL로 요청 합니다. 작업은 완료 하 고 끝점에서 구독 취소 작업을 논리 앱을 다시 시작 하는 경우 워크플로 실행 합니다.

1. 다른 사용 가능한 매개 변수를 추가 하려면 열을 **새 매개 변수 추가** 목록 및 매개 변수를 선택 합니다.

   HTTP Webhook에 대 한 인증 유형 사용할 수 있는 방법에 대 한 자세한 내용은 참조 [인증 HTTP 트리거 및 작업](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication)합니다.

1. 완료 하면 논리 앱을 저장 해야 합니다. 디자이너 도구 모음에서 선택 **저장할**합니다.

## <a name="connector-reference"></a>커넥터 참조

서로 비슷합니다는 트리거 및 작업 매개 변수에 대 한 자세한 내용은 참조 [HTTP 웹 후크 매개 변수](../logic-apps/logic-apps-workflow-actions-triggers.md##http-webhook-trigger)합니다.

### <a name="output-details"></a>출력 세부 정보

HTTP 웹 후크 트리거 또는이 정보를 반환 하는 작업에서 출력에 대 한 자세한 정보는 다음과 같습니다.

| 속성 이름 | 형식 | 설명 |
|---------------|------|-------------|
| headers | object | 요청에서 헤더 |
| body | object | JSON 개체 | 요청 본문 내용 사용 하 여 개체 |
| status code | int | 요청에서 상태 코드 |
|||

| status code | 설명 |
|-------------|-------------|
| 200 | 확인 |
| 202 | 동의함 |
| 400 | 잘못된 요청 |
| 401 | 권한 없음 |
| 403 | 사용할 수 없음 |
| 404 | 찾을 수 없음 |
| 500 | 내부 서버 오류. 알 수 없는 오류 발생. |
|||

## <a name="next-steps"></a>다음 단계

* 다른 [Logic Apps 커넥터](../connectors/apis-list.md)에 대해 알아봅니다.
