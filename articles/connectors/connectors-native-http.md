---
title: HTTP 또는 HTTPS를 사용하여 서비스 끝점을 호출합니다.
description: Azure 논리 앱에서 서비스 끝점으로 아웃바운드 HTTP 또는 HTTPS 요청 보내기
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/12/2020
tags: connectors
ms.openlocfilehash: 6c52f2df34faf441ab70b48b11bbc393ebcecb65
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617612"
---
# <a name="call-service-endpoints-over-http-or-https-from-azure-logic-apps"></a>Azure 논리 앱에서 HTTP 또는 HTTPS를 통해 서비스 끝점을 호출합니다.

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) 및 기본 제공 HTTP 트리거 또는 작업을 사용하면 HTTP 또는 HTTPS를 통해 서비스 끝점으로 요청을 보내는 자동화된 작업 및 워크플로를 만들 수 있습니다. 예를 들어 특정 일정에 따라 해당 끝점을 확인하여 웹 사이트의 서비스 끝점을 모니터링할 수 있습니다. 웹 사이트가 다운되는 등 지정된 이벤트가 해당 끝점에서 발생하면 이벤트는 논리 앱의 워크플로를 트리거하고 해당 워크플로에서 작업을 실행합니다. 대신 인바운드 HTTPS 호출을 수신하고 응답하려면 기본 제공 [요청 트리거 또는 응답 작업을](../connectors/connectors-native-reqres.md)사용합니다.

> [!NOTE]
> 대상 엔드포인트의 기능을 기반으로 HTTP 커넥터는 TLS(전송 계층 보안) 버전 1.0, 1.1 및 1.2를 지원합니다. Logic Apps는 지원되는 가장 높은 버전을 사용하여 엔드포인트와 협상합니다. 예를 들어 끝점이 1.2를 지원하는 경우 커넥터는 먼저 1.2를 사용합니다. 그렇지 않으면 커넥터에서 가장 높은 지원되는 버전을 사용합니다.

되풀이 일정에 대한 끝점을 확인하거나 *폴링하려면* [HTTP 트리거를](#http-trigger) 워크플로의 첫 번째 단계로 추가합니다. 트리거가 끝점을 검사할 때마다 트리거는 *호출하거나 끝점에 요청을* 보냅니다. 엔드포인트의 응답은 논리 앱의 워크플로가 실행될지 여부를 결정합니다. 트리거는 논리 앱의 작업에 대한 끝점의 응답에서 모든 콘텐츠를 전달합니다.

워크플로의 다른 위치에서 끝점을 호출하려면 [HTTP 작업을 추가합니다.](#http-action) 엔드포인트의 응답은 워크플로의 나머지 작업을 실행하는 방법을 결정합니다.

> [!IMPORTANT]
> HTTP 트리거 또는 작업에 이러한 헤더가 포함된 경우 Logic Apps는 경고 또는 오류를 표시하지 않고 생성된 요청 메시지에서 이러한 헤더를 제거합니다.
>
> * `Accept-*`
> * `Allow`
> * `Content-*`이러한 예외를 `Content-Disposition`제외하고: . `Content-Encoding``Content-Type`
> * `Cookie`
> * `Expires`
> * `Host`
> * `Last-Modified`
> * `Origin`
> * `Set-Cookie`
> * `Transfer-Encoding`
>
> Logic Apps는 이러한 헤더를 사용하여 HTTP 트리거 또는 작업을 사용하는 논리 앱을 저장하는 것을 막을 수는 없지만 Logic Apps는 이러한 헤더를 무시합니다.

이 문서에서는 논리 앱의 워크플로에 HTTP 트리거 또는 작업을 추가하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 Azure 구독이 없는 경우 [체험 Azure 계정에 등록](https://azure.microsoft.com/free/)합니다.

* 호출할 대상 끝점의 URL

* [논리 앱 만드는 방법](../logic-apps/quickstart-create-first-logic-app-workflow.md)에 관한 기본 지식 논리 앱을 새로 접하는 경우 [Azure 논리 앱이란 무엇입니까?](../logic-apps/logic-apps-overview.md)

* 대상 끝점을 호출할 논리 앱입니다. HTTP 트리거로 시작하려면 [빈 논리 앱을 만듭니다.](../logic-apps/quickstart-create-first-logic-app-workflow.md) HTTP 작업을 사용하려면 원하는 트리거로 논리 앱을 시작합니다. 이 예제에서는 HTTP 트리거를 첫 번째 단계로 사용합니다.

<a name="http-trigger"></a>

## <a name="add-an-http-trigger"></a>HTTP 트리거 추가

이 기본 제공 트리거는 끝점에 대해 지정된 URL에 대한 HTTP 호출을 만들고 응답을 반환합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 논리 앱 디자이너에서 빈 논리 앱을 엽니다.

1. 디자이너의 검색 상자에서 **기본 제공**을 선택합니다. 검색 상자에서 필터로 `http`을 입력합니다. 트리거 목록에서 **HTTP** 트리거를 **선택합니다.**

   ![HTTP 트리거 선택](./media/connectors-native-http/select-http-trigger.png)

   이 예제는 트리거의 이름을 "HTTP 트리거"로 변경하여 단계에 보다 설명적인 이름을 지정합니다. 또한 이 예제는 나중에 HTTP 작업을 추가하고 두 이름은 고유해야 합니다.

1. 대상 끝점에 대한 호출에 포함할 [HTTP 트리거 매개 변수에](../logic-apps/logic-apps-workflow-actions-triggers.md#http-trigger) 대한 값을 제공합니다. 트리거가 대상 끝점을 확인하는 빈도에 대한 되풀이를 설정합니다.

   ![HTTP 트리거 매개 변수 입력](./media/connectors-native-http/http-trigger-parameters.png)

   **없음**이외의 인증 유형을 선택하는 경우 선택한 항목과 따라 인증 설정이 다릅니다. HTTP에서 사용할 수 있는 인증 유형에 대한 자세한 내용은 다음 항목을 참조하십시오.

   * [아웃바운드 호출에 인증 추가](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
   * [관리되는 ID로 리소스에 대한 액세스 인증](../logic-apps/create-managed-service-identity.md)

1. 사용 가능한 다른 매개 변수를 추가하려면 새 매개 변수 목록 **추가를** 열고 원하는 매개 변수를 선택합니다.

1. 트리거가 발생할 때 실행되는 작업을 사용하여 논리 앱의 워크플로를 계속해서 작성합니다.

1. 작업이 완료되면 논리 앱을 저장해야 합니다. 디자이너 도구 모음에서 **저장**을 선택합니다.

<a name="http-action"></a>

## <a name="add-an-http-action"></a>HTTP 작업 추가

이 기본 제공 작업은 끝점에 대해 지정된 URL에 대한 HTTP 호출을 만들고 응답을 반환합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. Logic Apps 디자이너에서 논리 앱을 엽니다.

   이 예제에서는 HTTP 트리거를 첫 번째 단계로 사용합니다.

1. HTTP 작업을 추가할 단계에서 **새 단계를**선택합니다.

   단계 사이에서 작업을 추가하려면 단계 사이에 있는 화살표 위로 포인터를 이동합니다. 표시되는 더하기**+** 기호 ()를 선택한 다음 **작업 추가를**선택합니다.

1. **작업 선택** 아래에서 **기본 제공**을 선택합니다. 검색 상자에서 필터로 `http`을 입력합니다. **작업** 목록에서 **HTTP** 작업을 선택합니다.

   ![HTTP 작업 선택](./media/connectors-native-http/select-http-action.png)

   이 예제는 단계의 설명이 많은 이름을 가지도록 작업의 이름을 "HTTP 작업"으로 바꿉니다.

1. 대상 끝점에 대한 호출에 포함할 [HTTP 작업 매개 변수에](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action) 대한 값을 제공합니다.

   ![HTTP 작업 매개 변수 입력](./media/connectors-native-http/http-action-parameters.png)

   **없음**이외의 인증 유형을 선택하는 경우 선택한 항목과 따라 인증 설정이 다릅니다. HTTP에서 사용할 수 있는 인증 유형에 대한 자세한 내용은 다음 항목을 참조하십시오.

   * [아웃바운드 호출에 인증 추가](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
   * [관리되는 ID로 리소스에 대한 액세스 인증](../logic-apps/create-managed-service-identity.md)

1. 사용 가능한 다른 매개 변수를 추가하려면 새 매개 변수 목록 **추가를** 열고 원하는 매개 변수를 선택합니다.

1. 작업이 완료되면 논리 앱을 저장해야 합니다. 디자이너 도구 모음에서 **저장**을 선택합니다.

## <a name="content-with-multipartform-data-type"></a>다중 파트/양식 데이터 형식의 콘텐츠

HTTP 요청에 형식이 `multipart/form-data` 있는 콘텐츠를 처리하려면 이 형식을 사용하여 `$content-type` HTTP `$multipart` 요청본문에 및 특성을 포함하는 JSON 개체를 추가할 수 있습니다.

```json
"body": {
   "$content-type": "multipart/form-data",
   "$multipart": [
      {
         "body": "<output-from-trigger-or-previous-action>",
         "headers": {
            "Content-Disposition": "form-data; name=file; filename=<file-name>"
         }
      }
   ]
}
```

예를 들어 형식을 지원하는 해당 사이트의 API를 사용하여 Excel 파일에 대한 HTTP POST 요청을 웹 사이트로 `multipart/form-data` 보내는 논리 앱이 있다고 가정합니다. 이 작업의 모양은 다음과 같습니다.

![다중 파트 양식 데이터](./media/connectors-native-http/http-action-multipart.png)

다음은 기본 워크플로 정의에서 HTTP 작업의 JSON 정의를 보여 주는 동일한 예제입니다.

```json
"HTTP_action": {
   "inputs": {
      "body": {
         "$content-type": "multipart/form-data",
         "$multipart": [
            {
               "body": "@trigger()",
               "headers": {
                  "Content-Disposition": "form-data; name=file; filename=myExcelFile.xlsx"
               }
            }
         ]
      },
      "method": "POST",
      "uri": "https://finance.contoso.com"
   },
   "runAfter": {},
   "type": "Http"
}
```

## <a name="connector-reference"></a>커넥터 참조

트리거 및 작업 매개 변수에 대한 자세한 내용은 다음 섹션을 참조하십시오.

* [HTTP 트리거 매개 변수](../logic-apps/logic-apps-workflow-actions-triggers.md#http-trigger)
* [HTTP 작업 매개 변수](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action)

### <a name="output-details"></a>출력 세부 정보

다음은 이 정보를 반환하는 HTTP 트리거 또는 작업의 출력에 대한 자세한 정보입니다.

| 속성 이름 | Type | Description |
|---------------|------|-------------|
| headers | object | 요청의 헤더 |
| 본문 | object | JSON 개체 | 요청의 본문 콘텐츠가 있는 개체 |
| 상태 코드 | int | 요청의 상태 코드 |
|||

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

## <a name="next-steps"></a>다음 단계

* 다른 [Logic Apps 커넥터](../connectors/apis-list.md)에 대해 알아봅니다.
