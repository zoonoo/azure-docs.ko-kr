---
title: 호출 하거나 Azure Functions 및 Azure Service Bus를 사용 하 여 논리 앱 트리거
description: Azure functions를 호출 하거나 Azure Service Bus를 사용 하 여 논리 앱 트리거를 만들려면
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: jehollan, klam, LADocs
ms.topic: article
ms.assetid: 19cbd921-7071-4221-ab86-b44d0fc0ecef
ms.date: 06/04/2019
ms.openlocfilehash: 3d4f642ae25a179ea2c3241240996da774cd8c23
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66494916"
---
# <a name="call-or-trigger-logic-apps-by-using-azure-functions-and-azure-service-bus"></a>호출 하거나 Azure Functions 및 Azure Service Bus를 사용 하 여 논리 앱 트리거

사용할 수 있습니다 [Azure Functions](../azure-functions/functions-overview.md) 장기 실행 수신기 또는 작업을 배포 해야 하는 경우 논리 앱을 트리거할 수 있습니다. 예를 들어에서 수신 대기 하는 Azure 함수를 만들 수 있습니다는 [Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md) 큐를 즉시 푸시 트리거로 논리 앱을 실행 합니다.

## <a name="prerequisites"></a>필수 조건

* Azure 구독. Azure 구독이 없는 경우 [체험 Azure 계정에 등록](https://azure.microsoft.com/free/)합니다.

* Azure Service Bus 네임 스페이스입니다. 네임 스페이스 없다면 [네임 스페이스를 먼저 만들어야](../service-bus-messaging/service-bus-create-namespace-portal.md)합니다.

* Azure functions에 대 한 컨테이너인 Azure 함수 앱입니다. 함수 앱을 없다면 [함수 앱을 먼저 만든](../azure-functions/functions-create-first-azure-function.md),.NET 런타임 스택으로 선택 되었는지 확인 합니다.

* [논리 앱 만드는 방법](../logic-apps/quickstart-create-first-logic-app-workflow.md)에 관한 기본 지식

## <a name="create-logic-app"></a>논리 앱 만들기

이 시나리오에 대 한 함수 트리거 하려는 각 논리 앱을 실행 해야 합니다. 먼저, HTTP 요청 트리거를 시작 하는 논리 앱을 만듭니다. 이 함수는 큐 메시지를 수신할 때마다 엔드포인트를 호출합니다.  

1. [Azure Portal](https://portal.azure.com)에 로그인하고, 빈 논리 앱을 만듭니다.

   논리 앱을 처음 접하는 경우 검토 [빠른 시작: 첫 번째 논리 앱 만들기](../logic-apps/quickstart-create-first-logic-app-workflow.md)를 검토하세요.

1. 검색 상자에 "http 요청"을 입력합니다. 트리거 목록에서 다음 트리거를 선택합니다. **HTTP 요청을 수신 되는 경우**

   ![트리거 선택](./media/logic-apps-scenario-function-sb-trigger/when-http-request-received-trigger.png)

   요청 트리거를 사용 하 여 큐 메시지와 함께 사용할 JSON 스키마를 선택적으로 입력할 수 있습니다. JSON 스키마 도움말 Logic App Designer 입력된 데이터의 구조를 이해 하 고 출력을 더 쉽게 워크플로에서 사용할 수 있습니다.

1. 스키마를 지정하려면 다음과 같이 **요청 본문 JSON 스키마** 상자에 스키마를 입력합니다.

   ![JSON 스키마 지정](./media/logic-apps-scenario-function-sb-trigger/when-http-request-received-trigger-schema.png)

   스키마는 없지만 JSON 형식의 샘플 페이로드가 있는 경우 해당 페이로드로 스키마를 생성할 수 있습니다.

   1. 요청 트리거에서 **샘플 페이로드를 사용하여 스키마 생성**을 선택합니다.

   1. **샘플 JSON 페이로드를 입력하거나 붙여넣기**에서 샘플 페이로드를 입력하고 **완료**를 선택합니다.

      ![샘플 페이로드 입력](./media/logic-apps-scenario-function-sb-trigger/enter-sample-payload.png)

   이 샘플 페이로드는 트리거에 표시되는 이 스키마를 생성합니다.

   ```json
   {
      "type": "object",
      "properties": {
         "address": {
            "type": "object",
            "properties": {
               "number": {
                  "type": "integer"
               },
               "street": {
                  "type": "string"
               },
               "city": {
                  "type": "string"
               },
               "postalCode": {
                  "type": "integer"
               },
               "country": {
                  "type": "string"
               }
            }
         }
      }
   }
   ```

1. 큐 메시지를 받으면 실행 하려는 다른 작업을 추가 합니다.

   예를 들어 Office 365 Outlook 커넥터를 사용하여 이메일을 보낼 수 있습니다.

1. 논리 앱을 저장합니다. 그러면 이 논리 앱의 트리거에 대한 콜백 URL이 생성됩니다. 나중에 사용할 콜백 URL이 코드에서 Azure Service Bus 큐 트리거에 대 한 합니다.

   콜백 URL에 표시 된 **HTTP POST URL** 속성입니다.

   ![트리거에 대해 생성된 콜백 URL](./media/logic-apps-scenario-function-sb-trigger/callback-URL-for-trigger.png)

## <a name="create-azure-function"></a>Azure 함수 만들기

다음으로 트리거로 작동하고 큐에 수신 대기하는 함수를 만들겠습니다.

1. 아직 함수 앱을 열지 않았으면 Azure Portal에서 함수 앱을 열고 확장합니다. 

1. 함수 앱 이름 아래에서 **Functions**를 확장합니다. **Functions** 창에서 **새 함수**를 선택합니다.

   !["함수"를 확장 하 고 "새 함수"를 선택 합니다.](./media/logic-apps-scenario-function-sb-trigger/create-new-function.png)

1. .NET 런타임 스택으로 선택 하는 새 함수 앱을 만들었는지 여부에 따라이 템플릿을 선택 하거나 기존 함수 앱을 사용 하는 합니다.

   * 새 함수 앱의 경우이 서식 파일을 선택 합니다. **Service Bus 큐 트리거**

     ![새 함수 앱에 대 한 템플릿 선택](./media/logic-apps-scenario-function-sb-trigger/current-add-queue-trigger-template.png)

   * 기존 함수 앱의 경우이 서식 파일을 선택 합니다. **Service Bus 큐 트리거-C#**

     ![기존 함수 앱에 대 한 템플릿 선택](./media/logic-apps-scenario-function-sb-trigger/legacy-add-queue-trigger-template.png)

1. 에 **Azure Service Bus 큐 트리거** 창에 트리거에 대 한 이름을 입력 하 고 설정 합니다 **Service Bus 연결** Azure Service Bus SDK를 사용 하는 큐에 대 한 `OnMessageReceive()` 수신기 선택 **만들**합니다.

1. 큐 메시지를 트리거로 사용 하 여 이전에 만든된 논리 앱 끝점을 호출 하는 기본 함수를 작성 합니다. 이 예제에서는 `application/json` 메시지 콘텐츠 형식을 사용하지만, 필요에 따라 형식을 변경할 수 있습니다. 가능한 경우 HTTP 클라이언트의 인스턴스를 다시 사용 합니다. 자세한 내용은 [Azure Functions에서 연결을 관리](../azure-functions/manage-connections.md)합니다.

   ```CSharp
   using System;
   using System.Threading.Tasks;
   using System.Net.Http;
   using System.Text;

   // Callback URL for previously created Request trigger
   private static string logicAppUri = @"https://prod-05.westus.logic.azure.com:443/workflows/<remaining-callback-URL>";

   // Reuse the instance of HTTP clients if possible
   private static HttpClient httpClient = new HttpClient();

   public static void Run(string myQueueItem, ILogger log)
   {
       log.LogInformation($"C# ServiceBus queue trigger function processed message: {myQueueItem}");

       var response = httpClient.PostAsync(logicAppUri, new StringContent(myQueueItem, Encoding.UTF8, "application/json")).Result;
   }
   ```

1. 함수를 테스트하기 위해 [Service Bus 탐색기](https://github.com/paolosalvatori/ServiceBusExplorer) 같은 도구를 사용하여 큐 메시지를 추가합니다.

   함수가 메시지를 받는 즉시 논리 앱이 트리거됩니다.

## <a name="next-steps"></a>다음 단계

[호출, 트리거 또는 HTTP 끝점을 사용 하 여 워크플로 중첩](../logic-apps/logic-apps-http-endpoint.md)