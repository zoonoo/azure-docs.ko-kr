---
title: "Azure Service Bus-Event Grid 통합 예제 | Microsoft Docs"
description: "Service Bus 메시지 및 Event Grid 통합"
services: service-bus-messaging
documentationcenter: .net
author: ChristianWolf42
manager: timlt
editor: 
ms.assetid: f99766cb-8f4b-4baf-b061-4b1e2ae570e4
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: get-started-article
ms.date: 02/15/2018
ms.author: chwolf
ms.openlocfilehash: 8aff62dd32395c1ef292942e977467779f945931
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/21/2018
---
# <a name="azure-service-bus-to-azure-event-grid-examples"></a>Azure Service Bus-Azure Event Grid 예제

이 문서에서는 Event Grid의 이벤트를 기반으로 메시지를 수신하는 azure 함수 및 논리 앱을 설정하는 방법을 배웁니다. 이 예에서는 두 개의 구독이 있는 서비스 버스 토픽을 가정하고 한 Service Bus 구독에 대해서만 이벤트를 보내도록 Event Grid 구독이 생성된다고 가정합니다. 그런 후 Service Bus 토픽에 메시지를 보내고 이 Service Bus 구독에 대해 이벤트가 생성되었는지 확인하면 함수 또는 논리 앱이 Service Bus 구독에서 메시지를 수신하여 완성합니다.

* 우선 모든 [필수 구성 요소](#prerequisites)가 준비되었는지 확인합니다.
* Event Grid의 이벤트 초기 흐름을 디버깅하고 살펴볼 [간단한 테스트 Azure 함수](#test-function-setup)를 만듭니다.  **이 단계는 3 또는 4단계의 실행 여부에 관계없이 수행되어야 합니다.**
* Event Grid 이벤트를 기반으로 [Service Bus 메시지를 받아서 처리하는 Azure 함수](#receive-messages-using-azure-function)를 만듭니다.
* [Logic Apps](#receive-messages-using-azure-logic-app)를 활용합니다.

## <a name="prerequisites"></a>필수 조건

### <a name="service-bus-namespace"></a>Service Bus 네임스페이스

Service Bus 프리미엄 네임스페이스를 만듭니다. 두 개의 구독이 있는 Service Bus 토픽을 만듭니다.

### <a name="code-to-send-message-to-the-service-bus-topic"></a>Service Bus 토픽으로 메시지를 보내는 코드

아무 방법을 사용하여 Service Bus 토픽에 메시지를 보낼 수 있습니다. 또는 아래의 샘플을 사용할 수 있습니다. 샘플 코드에서는 Visual Studio 2017을 사용하는 것으로 가정합니다.

[이 GitHub 리포지토리](https://github.com/Azure/azure-service-bus/)를 복제합니다.

다음 폴더

\samples\DotNet\Microsoft.ServiceBus.Messaging\ServiceBusEventGridIntegration으로 이동하여 SBEventGridIntegration.sln 파일을 엽니다.

그런 다음, MessageSender 프로젝트로 이동하여 Program.cs를 엽니다.

![8][]

토픽 이름과 연결 문자열을 입력하고 콘솔 응용 프로그램을 실행합니다.

```CSharp
const string ServiceBusConnectionString = "YOUR CONNECTION STRING";
const string TopicName = "YOUR TOPIC NAME";
```

## <a name="test-function-setup"></a>테스트 함수 설정

종단 간 시나리오 전체를 수행하려면 적어도 현재 진행 중인 이벤트를 디버그하고 살펴볼 수 있는 작은 테스트 함수가 필요합니다.

포털에서 새 Azure 함수 응용 프로그램을 만듭니다. 이 [링크](https://docs.microsoft.com/en-us/azure/azure-functions/)를 따라 이동하여 Azure Functions의 기본 내용을 알아봅니다.

새로 만든 함수에서 작은 더하기 기호를 클릭하여 http 트리거 함수를 추가합니다.

![2][]

![3][]

그런 후 다음 코드를 함수에 복사합니다.

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

[저장 및 실행]을 클릭합니다.

## <a name="connect-function-and-namespace-via-event-grid"></a>Event Grid를 통해 함수와 네임스페이스 연결

그 다음 단계는 함수와 Service Bus 네임스페이스를 연결하는 것입니다. 이 예에서는 Azure Portal을 사용합니다. PowerShell 또는 Azure CLI를 사용하여 똑같은 작업을 수행하는 방법을 알아보려면 [개념](service-bus-to-event-grid-integration-concept.md 페이지를 참조하세요.

새 Azure Event Grid 구독을 만들려면 Azure Portal에서 네임스페이스로 이동하여 Event Grid 블레이드를 선택합니다. “+ 이벤트 구독”을 클릭합니다.

다음 스크린샷은 이미 Event Grid 구독이 몇 개 있는 네임스페이스를 보여줍니다.

![20][]

다음 스크린샷은 특정 필터링 없이 Azure 함수 또는 웹 후크를 구독하는 방법을 보여줍니다. 잊지 말고 Service Bus 구독에 대한 관련 필터를 "접미사 필터"로 추가합니다.

![21][]

필수 구성 요소 섹션에서 설명했듯이 Service Bus 토픽으로 메시지를 보내고 Azure 함수의 모니터링 기능을 통해 이벤트가 흐르고 있는지 확인합니다.

![9][]

### <a name="receive-messages-using-azure-function"></a>Azure 함수를 사용하여 메시지 받기

이전 섹션에서 간단한 테스트 및 디버깅 시나리오를 살펴보고 이벤트가 흐르는지 확인했습니다. 설명서의 이 부분에서는 이벤트를 받은 후 메시지를 받아서 처리하는 방법을 살펴보겠습니다.

Azure 함수를 다음과 같은 방식으로 추가하는 이유는 Azure Functions 내부의 Service Bus 함수 그 자체는 아직 새 Event Grid 통합을 기본적으로 지원하지 않기 때문입니다.

필수 구성 요소 섹션에서 열었던 동일한 Visual Studio 솔루션을 열고 ReceiveMessagesOnEvent.cs를 선택합니다. 코드에서 연결 문자열을 입력합니다.

![10][]

```Csharp
const string ServiceBusConnectionString = "YOUR CONNECTION STRING";
```

Azure Portal로 이동하여 [2. 테스트 함수 설정](#2-test-function-setup)에서 만든 Azure 함수에 대한 게시 프로필을 다운로드합니다.

![11][]

Visual Studio에서 SBEventGridIntegration을 마우스 오른쪽 단추로 클릭하고 [게시]를 선택합니다. 방금 다운로드한 게시 프로필을 사용하고, [프로필 가져오기]를 선택하고 [게시]를 클릭합니다.

![12][]

새 Azure 함수를 게시한 후에는 새 Azure 함수를 가리키는 새 Azure Event Grid 구독을 만듭니다. Service Bus 구독 이름이 될 올바른 "끝 문자" 필터를 적용합니다.

앞에서 만든 Azure Service Bus 토픽에 메시지를 보내고 포털의 Azure 함수 로그에서 이벤트가 흐르고 있으며 메시지가 수신되는지 검사합니다.

![12-1][]

### <a name="receive-messages-using-azure-logic-app"></a>Azure 논리 앱을 사용하여 메시지 받기

다음 지침은 Azure 논리 앱을 Azure Service Bus 및 Azure Event Grid와 연결하는 방법을 보여줍니다.

먼저, 시작 작업으로 Azure Portal에서 새 논리 앱을 만들고 Event Grid를 선택합니다.

![13][]

Logic Apps 디자이너의 초기 보기는 다음 스크린샷처럼 생겼으며, "리소스 이름"을 개발자의 네임스페이스 이름으로 바꿔야 합니다. 또한 고급 옵션을 확장하고 구독의 접미사 필터를 추가합니다.

![14][]

토픽 구독에서 수신하는 Service Bus 수신 작업을 추가합니다. 마지막 작업은 다음 스크린샷과 유사합니다.

![15][]

다음과 비슷한 완료 이벤트를 추가합니다.

![16][]

필수 구성 요소 섹션에서 설명한 것처럼 논리 앱을 저장하고 Service Bus 토픽을 보냅니다. 그런 후 논리 앱 실행을 관찰합니다. "개요"를 클릭한 후 "실행 기록"을 클릭하면 실행에 대한 더 많은 데이터를 얻을 수 있습니다.

![17][]

![18][]

## <a name="next-steps"></a>다음 단계

* [Azure Event Grid](https://docs.microsoft.com/en-us/azure/azure-functions/)에 대해 자세히 알아봅니다.
* [Azure Functions](https://docs.microsoft.com/en-us/azure/azure-functions/)에 대해 자세히 알아봅니다.
* [Azure Logic Apps](https://docs.microsoft.com/en-us/azure/logic-apps/)에 대해 자세히 알아봅니다.
* [Azure Service Bus](https://docs.microsoft.com/en-us/azure/azure-functions/)에 대해 자세히 알아보세요.

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