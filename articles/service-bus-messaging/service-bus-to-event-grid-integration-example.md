---
title: Azure Service Bus-Event Grid 통합 예제 | Microsoft Docs
description: 이 아티클에서는 Service Bus 메시지 및 Event Grid 통합 예제를 제공합니다.
services: service-bus-messaging
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: f99766cb-8f4b-4baf-b061-4b1e2ae570e4
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: tutorial
ms.date: 05/14/2019
ms.author: spelluru
ms.openlocfilehash: b7dbc7dbc0b670de81a3f4603b0d52bce7559af8
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/31/2019
ms.locfileid: "66428325"
---
# <a name="respond-to-azure-service-bus-events-received-via-azure-event-grid-by-using-azure-functions-and-azure-logic-apps"></a>Azure Functions 및 Azure Logic Apps를 사용하여 Azure Event Grid를 통해 받은 Azure Service Bus 이벤트에 응답
이 자습서에서는 Azure Functions 및 Azure Logic Apps를 사용하여 Azure Event Grid를 통해 받은 Azure Service Bus 이벤트에 응답하는 방법을 알아봅니다. 다음 단계를 수행합니다.
 
- Event Grid의 이벤트 초기 흐름을 디버깅하고 살펴볼 테스트 Azure 함수를 만듭니다.
- Event Grid 이벤트를 기반으로 Azure Service Bus 메시지를 받아서 처리하는 Azure 함수를 만듭니다.
- Event Grid 이벤트에 응답하는 논리 앱 만들기

Service Bus, Event Grid, Azure Functions 및 Logic Apps 아티팩트를 만든 후 다음 작업을 수행합니다. 

1. Service Bus 항목에 메시지를 보냅니다. 
2. 항목에 대한 구독이 해당 메시지를 수신했는지 확인
3. 이벤트를 구독한 함수 또는 논리 앱이 이벤트를 받았는지 확인합니다. 

## <a name="create-a-service-bus-namespace"></a>Service Bus 네임스페이스 만들기
다음 자습서의 지침을 따르세요. [빠른 시작: Azure Portal을 사용하여 Service Bus 항목 및 해당 항목에 대한 하나 이상의 구독 만들기](service-bus-quickstart-topics-subscriptions-portal.md): 다음 작업을 수행합니다.

- **프리미엄** Service Bus 네임스페이스를 만듭니다. 
- 연결 문자열을 가져옵니다. 
- Service Bus 항목을 만듭니다.
- 항목에 대한 두 개의 구독을 만듭니다. 

## <a name="prepare-a-sample-application-to-send-messages"></a>메시지를 전송하도록 예제 애플리케이션 준비
모든 메서드를 사용하여 Service Bus 토픽에 메시지를 보낼 수 있습니다. 이 프로시저 끝의 샘플 코드는 Visual Studio 2017을 사용한다고 가정합니다.

1. [ GitHub azure-service-bus 리포지토리](https://github.com/Azure/azure-service-bus/)를 복제합니다.
2. Visual Studio에서 *\samples\DotNet\Microsoft.ServiceBus.Messaging\ServiceBusEventGridIntegration* 폴더로 이동하여 *SBEventGridIntegration.sln* 파일을 엽니다.
3. **MessageSender** 프로젝트로 이동한 다음, **Program.cs**를 선택합니다.
4. 이전 단계에서 가져온 Service Bus 항목 이름 및 연결 문자열을 입력합니다.

    ```CSharp
    const string ServiceBusConnectionString = "YOUR CONNECTION STRING";
    const string TopicName = "YOUR TOPIC NAME";
    ```
5. 프로그램을 빌드한 후 실행하여 Service Bus 항목에 테스트 메시지를 보냅니다. 

## <a name="set-up-a-test-function-on-azure"></a>Azure에서 테스트 함수 설정 
시나리오 전체를 수행하려면 적어도 현재 진행 중인 이벤트를 디버그하고 살펴볼 수 있는 작은 테스트 함수가 필요합니다. [Azure Portal에서 첫 번째 함수 만들기](../azure-functions/functions-create-first-azure-function.md) 문서의 지침에 따라 다음 작업을 수행합니다. 

1. Function App을 만듭니다.
2. HTTP 트리거 함수를 만듭니다. 

그런 후 다음 단계를 수행합니다. 


# <a name="azure-functions-v2tabv2"></a>[Azure Functions V2](#tab/v2)

1. 트리 보기에서 **함수**를 확장하고 함수를 선택합니다. 함수의 코드를 다음 코드로 바꿉니다. 

    ```CSharp
    #r "Newtonsoft.Json"
    
    using System.Net;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;
    
    public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");
        var content = req.Body;
        string jsonContent = await new StreamReader(content).ReadToEndAsync();
        log.LogInformation($"Received Event with payload: {jsonContent}");
    
        IEnumerable<string> headerValues;
        headerValues = req.Headers.GetCommaSeparatedValues("Aeg-Event-Type");
    
        if (headerValues.Count() != 0)
        {
            var validationHeaderValue = headerValues.FirstOrDefault();
            if(validationHeaderValue == "SubscriptionValidation")
            {
                var events = JsonConvert.DeserializeObject<GridEvent[]>(jsonContent);
                var code = events[0].Data["validationCode"];
                log.LogInformation("Validation code: {code}");
                return (ActionResult) new OkObjectResult(new { validationResponse = code });
            }
        }
    
        return jsonContent == null
            ? new BadRequestObjectResult("Please pass a name on the query string or in the request body")
            : (ActionResult)new OkObjectResult($"Hello, {jsonContent}");
    }
    
    public class GridEvent
    {
        public string Id { get; set; }
        public string EventType { get; set; }
        public string Subject { get; set; }
        public DateTime EventTime { get; set; }
        public Dictionary<string, string> Data { get; set; }
        public string Topic { get; set; }
    }
    
    ```
2. **저장 및 실행**을 선택합니다.

    ![Function App 출력](./media/service-bus-to-event-grid-integration-example/function-run-output.png)
3. **함수 URL 가져오기**를 선택하고 URL을 기록해 둡니다. 

    ![함수 URL 가져오기](./media/service-bus-to-event-grid-integration-example/get-function-url.png)

# <a name="azure-functions-v1tabv1"></a>[Azure Functions V1](#tab/v1)

1. 다음과 같이 **V1** 버전을 사용하도록 함수를 구성합니다. 
    1. 트리 보기에서 Function App을 선택하고 **Function App 설정**을 선택합니다. 

        ![함수 앱 설정]()./media/service-bus-to-event-grid-integration-example/function-app-settings.png)
    2. **런타임 버전**으로 **~1**을 선택합니다. 
2. 트리 보기에서 **함수**를 확장하고 함수를 선택합니다. 함수의 코드를 다음 코드로 바꿉니다. 

    ```CSharp
    #r "Newtonsoft.Json"
    using System.Net;
    using Newtonsoft.Json;
    using Newtonsoft.Json.Linq;
    
    public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
    {
        log.Info("C# HTTP trigger function processed a request.");
        // parse query parameter
        var content = req.Content;
    
        string jsonContent = await content.ReadAsStringAsync(); 
        log.Info($"Received Event with payload: {jsonContent}");
    
        IEnumerable<string> headerValues;
        if (req.Headers.TryGetValues("Aeg-Event-Type", out headerValues))
        {
            var validationHeaderValue = headerValues.FirstOrDefault();
            if(validationHeaderValue == "SubscriptionValidation")
            {
            var events = JsonConvert.DeserializeObject<GridEvent[]>(jsonContent);
                 var code = events[0].Data["validationCode"];
                 return req.CreateResponse(HttpStatusCode.OK,
                 new { validationResponse = code });
            }
        }
    
        return jsonContent == null
        ? req.CreateResponse(HttpStatusCode.BadRequest, "Pass a name on the query string or in the request body")
        : req.CreateResponse(HttpStatusCode.OK, "Hello " + jsonContent);
    }
    
    public class GridEvent
    {
        public string Id { get; set; }
        public string EventType { get; set; }
        public string Subject { get; set; }
        public DateTime EventTime { get; set; }
        public Dictionary<string, string> Data { get; set; }
        public string Topic { get; set; }
    }
    ```
4. **저장 및 실행**을 선택합니다.

    ![Function App 출력](./media/service-bus-to-event-grid-integration-example/function-run-output.png)
4. **함수 URL 가져오기**를 선택하고 URL을 기록해 둡니다. 

    ![함수 URL 가져오기](./media/service-bus-to-event-grid-integration-example/get-function-url.png)

---

## <a name="connect-the-function-and-namespace-via-event-grid"></a>Event Grid를 통해 함수와 네임스페이스 연결
이 섹션에서는 Azure Portal을 사용하여 함수와 Service Bus 네임스페이스를 연결합니다. 

Azure Event Grid 구독을 만들려면 다음 단계를 수행합니다.

1. Azure Portal에서 네임스페이스로 이동한 다음, 왼쪽 창에서 **이벤트**를 선택합니다. 오른쪽 창에 두 개의 Event Grid 구독이 표시되며 네임스페이스 창이 열립니다. 
    
    ![Service Bus - 이벤트 페이지](./media/service-bus-to-event-grid-integration-example/service-bus-events-page.png)
2. 도구 모음에서 **+ 이벤트 구독**을 선택합니다. 
3. **이벤트 구독 만들기** 페이지에서 다음 단계를 수행합니다.
    1. 구독의 **이름**을 입력합니다. 
    2. **엔드포인트 유형**으로 **웹후크**를 선택합니다. 

        ![Service Bus - Event Grid 구독](./media/service-bus-to-event-grid-integration-example/event-grid-subscription-page.png)
    3. **엔드포인트 선택**을 선택하고 함수 URL을 붙여 넣은 후 **선택 확인**을 선택합니다. 

        ![함수 - 엔드포인트 선택](./media/service-bus-to-event-grid-integration-example/function-select-endpoint.png)
    4. **필터** 탭으로 전환하고 **첫 번째 구독**의 이름을 이전에 만든 Service Bus 항목에 입력한 후 **만들기** 단추를 선택합니다. 

        ![이벤트 구독 필터](./media/service-bus-to-event-grid-integration-example/event-subscription-filter.png)
4. 목록에 해당 이벤트 구독이 표시되는지 확인합니다.

    ![목록의 이벤트 구독](./media/service-bus-to-event-grid-integration-example/event-subscription-in-list.png)

## <a name="send-messages-to-the-service-bus-topic"></a>Service Bus 항목으로 메시지 보내기
1. Service Bus 항목으로 메시지를 보내는 .NET C# 애플리케이션을 실행합니다. 

    ![콘솔 앱 출력](./media/service-bus-to-event-grid-integration-example/console-app-output.png)
1. Azure Function App의 페이지에서 **함수**, 사용자 **함수**를 차례로 확장한 후 **모니터링**를 선택합니다. 

    ![함수 모니터링](./media/service-bus-to-event-grid-integration-example/function-monitor.png)

## <a name="receive-messages-by-using-azure-functions"></a>Azure Functions를 사용하여 메시지 받기
이전 섹션에서 간단한 테스트 및 디버깅 시나리오를 살펴보고 이벤트가 흐르는지 확인했습니다. 

이 섹션에서는 이벤트를 받은 후 메시지를 수신하고 처리하는 방법을 알아봅니다.

### <a name="publish-a-function-from-visual-studio"></a>Visual Studio에서 함수 게시
1. 열어 놓은 동일한 Visual Studio 솔루션(**SBEventGridIntegration**)의 **SBEventGridIntegration** 프로젝트에서 **ReceiveMessagesOnEvent.cs**를 선택합니다. 
2. 다음 코드에서 Service Bus 연결 문자열을 입력합니다.

    ```Csharp
    const string ServiceBusConnectionString = "YOUR CONNECTION STRING";
    ```
3. 함수의 **게시 프로필**을 다운로드합니다.
    1. Function App을 선택합니다. 
    2. **개요** 탭을 아직 선택하지 않은 경우 선택합니다. 
    3. 도구 모음에서 **게시 프로필 가져오기**를 선택합니다. 

        ![함수에 대한 게시 프로필 가져오기](./media/service-bus-to-event-grid-integration-example/function-download-publish-profile.png)
    4. 프로젝트의 폴더에 파일을 저장합니다. 
4. Visual Studio에서 **SBEventGridIntegration**을 마우스 오른쪽 단추로 클릭하고 **Publish**를 선택합니다. 
5. **게시** 페이지에서 *시작**을 선택합니다. 
6. **게시 대상 선택** 페이지에서 다음 단계를 수행하고 **프로필 가져오기**를 선택합니다. 

    ![Visual Studio - 프로필 가져오기 단추](./media/service-bus-to-event-grid-integration-example/visual-studio-import-profile-button.png)
7. 이전에 다운로드한 **프로필 파일 게시**를 선택합니다. 
8. **게시** 페이지에서 **게시**를 선택합니다. 

    ![Visual Studio - 게시](./media/service-bus-to-event-grid-integration-example/select-publish.png)
9. 새 Azure 함수 **ReceiveMessagesOnEvent**가 표시되는지 확인합니다. 필요한 경우 페이지를 새로 고칩니다. 

    ![새 함수가 생성되었는지 확인](./media/service-bus-to-event-grid-integration-example/function-receive-messages.png)
10. 새 함수에 대한 URL을 가져온 후 기록해 둡니다. 

### <a name="event-grid-subscription"></a>Event Grid 구독

1. 다음과 같이 기존 Event Grid 구독을 삭제합니다.
    1. **Service Bus 네임스페이스** 페이지의 왼쪽 메뉴에서 **이벤트**를 선택합니다. 
    2. 기존 이벤트 구독을 선택합니다. 
    3. **이벤트 구독** 페이지에서 **삭제**를 선택합니다.
2. [Event Grid를 통해 함수 및 네임스페이스 연결](#connect-the-function-and-namespace-via-event-grid) 섹션의 지침에 따라 새 함수 URL을 사용하여 Event Grid 구독을 만듭니다.
3. [Service Bus 항목으로 메시지 보내기](#send-messages-to-the-service-bus-topic) 섹션의 지침에 따라 항목에 항목으로 메시지를 보내고 함수를 모니터링합니다. 

## <a name="receive-messages-by-using-logic-apps"></a>Logic Apps를 사용하여 메시지 받기
다음 단계에 따라 Azure Service Bus 및 Azure Event Grid와 논리 앱을 연결합니다.

1. Azure Portal에서 논리 앱을 만듭니다.
    1. **+ 리소스 만들기**를 선택하고 **통합**을 선택한 후 **논리 앱**을 선택합니다. 
    2. **논리 앱 - 만들기** 페이지에서 논리 앱의 **이름**을 입력합니다.
    3. Azure **구독**을 선택합니다. 
    4. **리소스 그룹**에 대해 **기존 항목 사용**을 선택하고 다른 리소스(예: Azure 함수, Service Bus 네임스페이스)에 사용한 리소스 그룹을 선택합니다. 
    5. 논리 앱의 **위치**를 선택합니다. 
    6. **만들기**를 선택하여 논리 앱을 만듭니다. 
2. **Logic Apps 디자이너** 페이지에서 **템플릿** 아래에 있는 **비어 있는 논리 앱**을 선택합니다. 
3. 디자이너에서 다음 단계를 수행합니다.
    1. **Event Grid**를 검색합니다. 
    2. **리소스 이벤트가 발생하는 경우(미리 보기) - Azure Event Grid**를 선택합니다. 

        ![Logic Apps 디자이너 - Event Grid 트리거 선택](./media/service-bus-to-event-grid-integration-example/logic-apps-event-grid-trigger.png)
4. **로그인**을 선택하고 Azure 자격 증명을 입력한 후 **액세스 허용**을 선택합니다. 
5. **리소스 이벤트가 발생하는 경우** 페이지에서 다음 단계를 수행합니다.
    1. Azure 구독을 선택합니다. 
    2. **리소스 종류**로 **Microsoft.ServiceBus.Namespaces**를 선택합니다. 
    3. **리소스 이름**에 대해 Service Bus 네임스페이스를 선택합니다. 
    4. **새 매개 변수 추가**를 선택하고 **접미사 필터**를 선택합니다. 
    5. **접미사 필터**에 대해 두 번째 Service Bus 항목 구독의 이름을 입력합니다. 
        ![Logic Apps 디자이너 - 이벤트 구성](./media/service-bus-to-event-grid-integration-example/logic-app-configure-event.png)
6. 디자이너에서 **+ 새 단계**를 선택하고 다음 단계를 수행합니다.
    1. **Service Bus**를 검색합니다.
    2. 목록에서 **Service Bus**를 선택합니다. 
    3. **작업** 목록에서 **메시지 가져오기**를 선택합니다. 
    4. **항목 구독에서 메시지 가져오기(보기 잠금)** 를 선택합니다. 

        ![Logic Apps 디자이너 - 메시지 가져오기 작업](./media/service-bus-to-event-grid-integration-example/service-bus-get-messages-step.png)
    5. **연결의 이름**을 입력합니다. 예:  **항목 구독에서 메시지를 가져오고**, Service Bus 네임스페이스를 선택합니다. 

        ![Logic Apps 디자이너 - Service Bus 네임스페이스 선택](./media/service-bus-to-event-grid-integration-example/logic-apps-select-namespace.png) 
    6. **RootManageSharedAccessKey**를 선택합니다.

        ![Logic Apps 디자이너 - 공유 액세스 키 선택](./media/service-bus-to-event-grid-integration-example/logic-app-shared-access-key.png) 
    7. **만들기**를 선택합니다. 
    8. 항목 및 구독을 선택합니다. 
    
        ![Logic Apps 디자이너 - Service Bus 항목 및 구독 선택](./media/service-bus-to-event-grid-integration-example/logic-app-select-topic-subscription.png)
7. **+ 새 단계**를 선택하고 다음 단계를 수행합니다. 
    1. **Service Bus**를 선택합니다.
    2. 작업 목록에서 **항목 구독의 메시지 완료**를 선택합니다. 
    3. Service Bus **항목**을 선택합니다.
    4. 항목에 대한 두 번째 **구독**을 선택합니다.
    5. **메시지의 잠금 토큰**에 대해 **동적 콘텐츠**의 **잠금 토큰**을 선택합니다. 

        ![Logic Apps 디자이너 - Service Bus 항목 및 구독 선택](./media/service-bus-to-event-grid-integration-example/logic-app-complete-message.png)
8. Logic Apps 디자이너 도구 모음에서 **저장**을 선택하여 논리 앱을 저장합니다. 
9. [Service Bus 항목으로 메시지 보내기](#send-messages-to-the-service-bus-topic) 섹션의 지침에 따라 항목에 항목으로 메시지를 보냅니다. 
10. 논리 앱의 **개요** 페이지로 전환합니다. 전송한 메시지의 **실행 기록**에서 논리 앱이 실행되는 것을 확인합니다.

    ![Logic Apps 디자이너 - 논리 앱 실행](./media/service-bus-to-event-grid-integration-example/logic-app-runs.png)

## <a name="next-steps"></a>다음 단계

* [Azure Event Grid](https://docs.microsoft.com/azure/event-grid/)에 대해 자세히 알아봅니다.
* [Azure Functions](https://docs.microsoft.com/azure/azure-functions/)에 대해 자세히 알아봅니다.
* [Azure App Service의 Logic Apps 기능](https://docs.microsoft.com/azure/logic-apps/)에 대해 자세히 알아봅니다.
* [Azure Service Bus](https://docs.microsoft.com/azure/service-bus/)에 대해 자세히 알아보세요.


[2]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid2.png
[3]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid3.png
[7]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid7.png
[8]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid8.png
[9]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid9.png
[10]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid10.png
[11]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid11.png
[12]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid12.png
[12-1]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid12-1.png
[12-2]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid12-2.png
[13]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid13.png
[14]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid14.png
[15]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid15.png
[16]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid16.png
[17]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid17.png
[18]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid18.png
[20]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal.png
[21]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal2.png
