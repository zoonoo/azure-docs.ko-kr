---
title: "Azure Functions에서 .NET 클래스 라이브러리 사용 | Microsoft Docs"
description: "Azure Functions와 함께 사용할 .NET 클래스 라이브러리를 작성하는 방법을 알아봅니다."
services: functions
documentationcenter: na
author: lindydonna
manager: erikre
editor: 
tags: 
keywords: "Azure Functions, 함수, 이벤트 처리, 동적 계산, 서버를 사용하지 않는 아키텍처"
ms.assetid: 9f5db0c2-a88e-4fa8-9b59-37a7096fc828
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 06/09/2017
ms.author: donnam
ms.translationtype: HT
ms.sourcegitcommit: 137671152878e6e1ee5ba398dd5267feefc435b7
ms.openlocfilehash: 0613bb96d3afb85ff7e684246b128e4eef518d23
ms.contentlocale: ko-kr
ms.lasthandoff: 07/28/2017

---
# <a name="using-net-class-libraries-with-azure-functions"></a>Azure Functions에서 .NET 클래스 라이브러리 사용

Azure Functions는 스크립트 파일 외에도 하나 이상의 함수 구현으로서 클래스 라이브러리를 게시하는 것을 지원합니다. [Azure Function Tools for Visual Studio 2017](https://blogs.msdn.microsoft.com/webdev/2017/05/10/azure-function-tools-for-visual-studio-2017/)를 사용하는 것이 좋습니다.

## <a name="prerequisites"></a>필수 조건 

이 문서에는 다음과 같은 필수 조건이 있습니다.

- [Visual Studio 2017 15.3 미리 보기](https://www.visualstudio.com/vs/preview/) - **ASP.NET 및 웹 개발** 및 **Azure 개발** 워크로드를 설치합니다.
- [Azure Function Tools for Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=AndrewBHall-MSFT.AzureFunctionToolsforVisualStudio2017)

## <a name="functions-class-library-project"></a>Functions 클래스 라이브러리 프로젝트

Visual Studio에서 새 Azure Functions 프로젝트를 만듭니다. 새 프로젝트 템플릿은 *host.json* 및 *local.settings.json* 파일을 만듭니다. [host.json에서 Azure Functions 런타임 설정을 사용자 지정](https://github.com/Azure/azure-webjobs-sdk-script/wiki/host.json)할 수 있습니다. 

*local.settings.json* 파일은 Azure Functions Core Tools에 대한 앱 설정, 연결 문자열 및 설정을 저장합니다. 구조에 대한 자세한 내용은 [로컬로 Azure Functions 코딩 및 테스트](functions-run-local.md#local-settings)를 참조하세요.

### <a name="functionname-attribute"></a>FunctionName 특성

[`FunctionNameAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/FunctionNameAttribute.cs) 특성은 메서드를 함수 진입점으로 표시합니다. 정확히 하나의 트리거와 0개 이상의 입력 및 출력 바인딩을 포함하여 사용해야 합니다.

### <a name="conversion-to-functionjson"></a>Function.json으로 변환

Azure Functions 프로젝트를 빌드하면 `[FunctionName]`에 정의된 함수 이름과 일치하는 디렉터리에 `function.json` 파일이 생성됩니다. 트리거 및 바인딩을 지정하고 프로젝트 어셈블리 파일을 가리킵니다.

이 변환은 [Microsoft\.NET\.Sdk\.Functions](http://www.nuget.org/packages/Microsoft.NET.Sdk.Functions) NuGet 패키지에서 수행됩니다. 원본은 [azure\-functions\-vs\-build\-sdk](https://github.com/Azure/azure-functions-vs-build-sdk) GitHub 리포지토리에서 사용할 수 있습니다.

## <a name="triggers-and-bindings"></a>트리거 및 바인딩

다음 표에서는 Azure Functions 클래스 라이브러리 프로젝트에서 사용할 수 있는 트리거와 바인딩을 나열합니다. 모든 특성은 `Microsoft.Azure.WebJobs` 네임스페이스에 있습니다.

| 바인딩 | 특성 | NuGet 패키지 |
|------   | ------    | ------        |
| [Blob 저장소 트리거, 입력, 출력](#blob-storage) | [BlobAttribute], [StorageAccountAttribute] | [Microsoft.Azure.WebJobs] | [Blob 저장소] |
| [Cosmos DB 입력 및 출력 바인딩](#cosmos-db) | [DocumentDBAttribute] | [Microsoft.Azure.WebJobs.Extensions.DocumentDB] | 
| [Event Hubs 트리거 및 출력](#event-hub) | [EventHubTriggerAttribute], [EventHubAttribute] | [Microsoft.Azure.WebJobs.ServiceBus] |
| [외부 파일 입력 및 출력](#api-hub) | [ApiHubFileAttribute] | [Microsoft.Azure.WebJobs.Extensions.ApiHub] |
| [HTTP 및 웹후크 트리거](#http) | [HttpTriggerAttribute] | [Microsoft.Azure.WebJobs.Extensions.Http] |
| [Mobile Apps 입력 및 출력](#mobile-apps) | [MobileTableAttribute] | [Microsoft.Azure.WebJobs.Extensions.MobileApps] | 
| [Notification Hubs 출력](#nh) | [NotificationHubAttribute] | [Microsoft.Azure.WebJobs.Extensions.NotificationHubs] | 
| [큐 저장소 트리거 및 출력](#queue) | [QueueAttribute], [StorageAccountAttribute] | [Microsoft.Azure.WebJobs] | 
| [SendGrid 출력](#sendgrid) | [SendGridAttribute] | [Microsoft.Azure.WebJobs.Extensions.SendGrid] | 
| [Service Bus 트리거 및 출력](#service-bus) | [ServiceBusAttribute], [ServiceBusAccountAttribute] | [Microsoft.Azure.WebJobs.ServiceBus]
| [테이블 저장소 입력 및 출력](#table) | [TableAttribute], [StorageAccountAttribute] | [Microsoft.Azure.WebJobs] | 
| [타이머 트리거](#timer) | [TimerTriggerAttribute] | [Microsoft.Azure.WebJobs.Extensions] | 
| [Twilio 출력](#twilio) | [TwilioSmsAttribute] | [Microsoft.Azure.WebJobs.Extensions.Twilio] | 

<a name="blob-storage"></a>

### <a name="blob-storage-trigger-input-and-output-bindings"></a>Blob 저장소 트리거, 입력 및 출력 바인딩

Azure Functions는 Azure Blob Storage에 대한 트리거, 입력 및 출력 바인딩을 지원합니다. 식 및 메타데이터 바인딩에 대한 자세한 내용은 [Azure Functions Blob Storage 바인딩](functions-bindings-storage-blob.md)을 참조하세요.

Blob 트리거는 `[BlobTrigger]` 특성으로 정의됩니다. `[StorageAccount]` 특성을 사용하여 전체 함수 또는 클래스에서 사용되는 저장소 계정을 정의할 수 있습니다.

```csharp
[StorageAccount("AzureWebJobsStorage")]
[FunctionName("BlobTriggerCSharp")]        
public static void Run([BlobTrigger("samples-workitems/{name}")] Stream myBlob, string name, TraceWriter log)
{
    log.Info($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

Blob 입력 및 출력은 읽기 또는 쓰기를 나타내는 `FileAccess` 매개 변수와 함께 `[Blob]` 특성을 사용하여 정의됩니다. 다음 예제에서는 Blob 트리거와 Blob 출력 바인딩을 사용합니다.

```csharp
[FunctionName("ResizeImage")]
[StorageAccount("AzureWebJobsStorage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image, 
    [Blob("sample-images-sm/{name}", FileAccess.Write)] Stream imageSmall, 
    [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageMedium)
{
    var imageBuilder = ImageResizer.ImageBuilder.Current;
    var size = imageDimensionsTable[ImageSize.Small];

    imageBuilder.Build(image, imageSmall,
        new ResizeSettings(size.Item1, size.Item2, FitMode.Max, null), false);

    image.Position = 0;
    size = imageDimensionsTable[ImageSize.Medium];

    imageBuilder.Build(image, imageMedium,
        new ResizeSettings(size.Item1, size.Item2, FitMode.Max, null), false);
}

public enum ImageSize { ExtraSmall, Small, Medium }

private static Dictionary<ImageSize, (int, int)> imageDimensionsTable = new Dictionary<ImageSize, (int, int)>() {
    { ImageSize.ExtraSmall, (320, 200) },
    { ImageSize.Small,      (640, 400) },
    { ImageSize.Medium,     (800, 600) }
};
```        

<a name="cosmos-db"></a>

### <a name="cosmos-db-input-and-output-bindings"></a>Cosmos DB 입력 및 출력 바인딩

Azure Functions는 Cosmos DB에 대한 입력 및 출력 바인딩을 지원합니다. Cosmos DB 바인딩의 기능에 대한 자세한 내용은 [Azure Functions Cosmos DB 바인딩](functions-bindings-documentdb.md)를 참조하세요.

Cosmos DB 문서에 바인딩하려면 [Microsoft.Azure.WebJobs.Extensions.DocumentDB] NuGet 패키지에서 `[DocumentDB]` 특성을 사용합니다. 다음 예제에는 큐 트리거와 DocumentDB API 출력 바인딩이 있습니다.

```csharp
[FunctionName("QueueToDocDB")]        
public static void Run(
    [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem, 
    [DocumentDB("ToDoList", "Items", ConnectionStringSetting = "DocDBConnection")] out dynamic document)
{
    document = new { Text = myQueueItem, id = Guid.NewGuid() };
}

```

<a name="event-hub"></a>

### <a name="event-hubs-trigger-and-output"></a>Event Hubs 트리거 및 출력

Azure Functions는 이벤트 허브에 대한 트리거 및 출력 바인딩을 지원합니다. 자세한 내용은 [Azure Functions Event Hub 바인딩](functions-bindings-event-hubs.md)를 참조하세요.

`[Microsoft.Azure.WebJobs.ServiceBus.EventHubTriggerAttribute]` 및 `[Microsoft.Azure.WebJobs.ServiceBus.EventHubAttribute]`형식은 [Microsoft.Azure.WebJobs.ServiceBus] NuGet 패키지에 정의되어 있습니다. 

다음 예제에서는 Event Hub 트리거를 사용합니다.

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnection")] string myEventHubMessage, TraceWriter log)
{
    log.Info($"C# Event Hub trigger function processed a message: {myEventHubMessage}");
}
```

다음 예제에는 메서드 반환 값을 출력으로 사용하는 Event Hub 출력이 있습니다.

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnection")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, TraceWriter log)
{
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}");
    return $"{DateTime.Now}";
}
```

<a name="api-hub"></a>

### <a name="external-file-input-and-output"></a>외부 파일 입력 및 출력

Azure Functions는 외부 파일(예: Google Drive, Dropbox 및 OneDrive)에 대한 트리거, 입력 및 출력 바인딩을 지원합니다. 자세한 내용은 [Azure Functions 외부 파일 바인딩](functions-bindings-external-file.md)을 참조하세요. `[ExternalFileTrigger]` 및 `[ExternalFile]` 특성은 [Microsoft.Azure.WebJobs.Extensions.ApiHub] NuGet 패키지에 정의되어 있습니다.

다음 C# 예제에서는 외부 파일 입력 및 출력 바인딩을 보여 줍니다. 이 코드는 입력 파일을 출력 파일에 복사합니다.

```csharp
[StorageAccount("MyStorageConnection")]
[FunctionName("ExternalFile")]
[return: ApiHubFile("MyFileConnection", "samples-workitems/{queueTrigger}-Copy", FileAccess.Write)]
public static string Run([QueueTrigger("myqueue-items")] string myQueueItem, 
    [ApiHubFile("MyFileConnection", "samples-workitems/{queueTrigger}", FileAccess.Read)] string myInputFile, 
    TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    return myInputFile;
}
```

<a name="http"></a>

### <a name="http-and-webhooks"></a>HTTP 및 웹후크

`HttpTrigger` 특성을 사용하여 HTTP 트리거 또는 웹후크를 정의합니다. 이 특성은 [Microsoft.Azure.WebJobs.Extensions.Http] NuGet 패키지에 정의되어 있습니다. 권한 부여 수준, 웹후크 유형, 경로 및 메서드를 사용자 지정할 수 있습니다. 다음 예제에서는 익명 인증 및 _genericJson_ 웹후크 유형으로 HTTP 트리거를 정의합니다.

```csharp
[FunctionName("HttpTriggerCSharp")]
public static HttpResponseMessage Run([HttpTrigger(AuthorizationLevel.Anonymous, WebHookType = "genericJson")] HttpRequestMessage req)
{
    return req.CreateResponse(HttpStatusCode.OK);
}
```

<a name="mobile-apps"></a>

### <a name="mobile-apps-input-and-output"></a>Mobile Apps 입력 및 출력

Azure Functions는 Mobile Apps에 대한 입력 및 출력 바인딩을 지원합니다. 자세한 내용은 [Azure Functions Mobile Apps 바인딩 ](functions-bindings-mobile-apps.md)을 참조하세요. `[MobileTable]` 특성은 [Microsoft.Azure.WebJobs.Extensions.MobileApps]NuGet 패키지에 정의되어 있습니다.

다음 예제에서는 Mobile Apps 출력 바인딩을 보여 줍니다.

```csharp
[FunctionName("MobileAppsOutput")]        
[return: MobileTable(ApiKeySetting = "MyMobileAppKey", TableName = "MyTable", MobileAppUriSetting = "MyMobileAppUri")]
public static object Run([QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem, TraceWriter log)
{
    return new { Text = $"I'm running in a C# function! {myQueueItem}" };
}
```

<a name="nh"></a>

### <a name="notification-hubs-output"></a>Notification Hubs 출력

Azure Functions는 Notification Hubs에 대한 출력 바인딩을 지원합니다. 자세한 내용은 [Azure Functions Notification Hub 출력 바인딩](functions-bindings-notification-hubs.md)을 참조하세요. `[NotificationHub]` 특성은 [Microsoft.Azure.WebJobs.Extensions.NotificationHubs] NuGet 패키지에 정의되어 있습니다.

<a name="queue"></a>

### <a name="queue-storage-trigger-and-output"></a>큐 저장소 트리거 및 출력

Azure Functions는 Azure 큐에 대한 트리거 및 출력 바인딩을 지원합니다. 자세한 내용은 [Azure Functions Queue Storage 바인딩](functions-bindings-storage-queue.md)을 참조하세요.

다음 예제에서는 `[Queue]` 특성을 사용하여 큐 출력 바인딩이 포함된 함수 반환 형식을 사용하는 방법을 보여 줍니다. 큐 트리거를 정의하려면 `[QueueTrigger]` 특성을 사용합니다.

```csharp
[StorageAccount("AzureWebJobsStorage")]
public static class QueueFunctions
{
    // HTTP trigger with queue output binding
    [FunctionName("QueueOutput")]
    [return: Queue("myqueue-items")]
    public static string QueueOutput([HttpTrigger] dynamic input,  TraceWriter log)
    {
        log.Info($"C# function processed: {input.Text}");
        return input.Text;
    }

    // Queue trigger
    [FunctionName("QueueTrigger")]
    [StorageAccount("AzureWebJobsStorage")]
    public static void QueueTrigger([QueueTrigger("myqueue-items")] string myQueueItem, TraceWriter log)
    {
        log.Info($"C# function processed: {myQueueItem}");
    }
}

```

<a name="sendgrid"></a>

### <a name="sendgrid-output"></a>SendGrid 출력

Azure Functions는 프로그래밍 방식으로 전자 메일을 보내기 위한 SendGrid 출력 바인딩을 지원합니다. 자세한 내용은 [Azure Functions SendGrid 바인딩](functions-bindings-sendgrid.md)을 참조하세요.

`[SendGrid]` 특성은 [Microsoft.Azure.WebJobs.Extensions.SendGrid] NuGet 패키지에 정의되어 있습니다.

다음은 `SendGridMessage`를 사용하여 Service Bus 큐 트리거 및 SendGrid 출력 바인딩을 사용하는 예입니다.

```csharp
[FunctionName("SendEmail")]
public static void Run(
    [ServiceBusTrigger("myqueue", AccessRights.Manage, Connection = "ServiceBusConnection")] OutgoingEmail email,
    [SendGrid] out SendGridMessage message)
{
    message = new SendGridMessage();
    message.AddTo(email.To);
    message.AddContent("text/html", email.Body);
    message.SetFrom(new EmailAddress(email.From));
    message.SetSubject(email.Subject);
}

public class OutgoingEmail
{
    public string To { get; set; }
    public string From { get; set; }
    public string Subject { get; set; }
    public string Body { get; set; }
}
```

<a name="service-bus"></a>

### <a name="service-bus-trigger-and-output"></a>Service Bus 트리거 및 출력

Azure Functions는 Service Bus 큐 및 토픽에 대한 트리거 및 출력 바인딩을 지원합니다. 바인딩 구성에 대한 자세한 내용은 [Azure Functions Service Bus 바인딩](functions-bindings-service-bus.md)을 참조하세요.

`[ServiceBusTrigger]` 및 `[ServiceBus]` 특성은 [Microsoft.Azure.WebJobs.ServiceBus] NuGet 패키지에 정의되어 있습니다. 

다음은 Service Bus 큐 트리거의 예제입니다.

```csharp
[FunctionName("ServiceBusQueueTriggerCSharp")]                    
public static void Run([ServiceBusTrigger("myqueue", AccessRights.Manage, Connection = "ServiceBusConnection")] string myQueueItem, TraceWriter log)
{
    log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");
}
```

다음은 메서드 반환 형식을 출력으로 사용하는 Service Bus 출력 바인딩의 예제입니다.

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string ServiceBusOutput([HttpTrigger] dynamic input, TraceWriter log)
{
    log.Info($"C# function processed: {input.Text}");
    return input.Text;
}
```        

<a name="table"></a>

### <a name="table-storage-input-and-output"></a>테이블 저장소 입력 및 출력

Azure Functions는 Azure Table 저장소에 대한 입력 및 출력 바인딩을 지원합니다. 자세한 내용은 [Azure Functions 테이블 저장소 바인딩 ](functions-bindings-storage-table.md)을 참조하세요.

다음 예제에서는 테이블 저장소 출력 및 입력 바인딩을 보여 주는 두 개의 함수가 포함된 클래스입니다. 

```csharp
[StorageAccount("AzureWebJobsStorage")]
public class TableStorage
{
    public class MyPoco
    {
        public string PartitionKey { get; set; }
        public string RowKey { get; set; }
        public string Text { get; set; }
    }

    [FunctionName("TableOutput")]
    [return: Table("MyTable")]
    public static MyPoco TableOutput([HttpTrigger] dynamic input, TraceWriter log)
    {
        log.Info($"C# http trigger function processed: {input.Text}");
        return new MyPoco { PartitionKey = "Http", RowKey = Guid.NewGuid().ToString(), Text = input.Text };
    }

    // use the metadata parameter "queueTrigger" to bind the queue payload
    [FunctionName("TableInput")]
    public static void TableInput([QueueTrigger("table-items")] string input, [Table("MyTable", "Http", "{queueTrigger}")] MyPoco poco, TraceWriter log)
    {
        log.Info($"C# function processed: {poco.Text}");
    }
}

```

<a name="timer"></a>

### <a name="timer-trigger"></a>타이머 트리거

Azure Functions에는 정의된 일정에 따라 함수 코드를 실행할 수 있는 타이머 트리거 바인딩이 있습니다. 바인딩 기능에 대한 자세한 내용은 [Azure Functions를 사용하여 코드 실행 예약](functions-bindings-timer.md)을 참조하세요.

사용 계획에서 [CRON 식](http://en.wikipedia.org/wiki/Cron#CRON_expression)을 사용하여 일정을 정의할 수 있습니다. App Service 계획을 사용하는 경우 TimeSpan 문자열을 사용할 수도 있습니다. 

다음 예제에서는 5분마다 실행되는 타이머 트리거를 정의합니다.

```csharp
[FunctionName("TimerTriggerCSharp")]
public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, TraceWriter log)
{
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}");
}
```

<a name="twilio"></a>

### <a name="twilio-output"></a>Twilio 출력

Azure Functions는 함수에서 SMS 문자 메시지를 보낼 수 있도록 Twilio 출력 바인딩을 지원합니다. 자세한 내용은 [Twilio 출력 바인딩을 사용하여 Azure Functions에서 SMS 메시지 전송](functions-bindings-twilio.md)을 참조하세요. 

`[TwilioSms]` 특성은 [Microsoft.Azure.WebJobs.Extensions.Twilio] 패키지에 정의되어 있습니다.

다음 C# 예제에서는 큐 트리거와 Twilio 출력 바인딩을 사용합니다.

```csharp
[FunctionName("QueueTwilio")]
[return: TwilioSms(AccountSidSetting = "TwilioAccountSid", AuthTokenSetting = "TwilioAuthToken", From = "+1425XXXXXXX" )]
public static SMSMessage Run([QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] JObject order, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {order}");

    var message = new SMSMessage()
    {
        Body = $"Hello {order["name"]}, thanks for your order!",
        To = order["mobileNumber"].ToString()
    };

    return message;
}
```

## <a name="next-steps"></a>다음 단계

C# 스크립팅에서 Azure Functions 사용에 대한 자세한 내용은 [Azure Functions C\# 스크립트 개발자 참조](functions-reference-csharp.md)를 참조하세요.

[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]


<!-- NuGet packages --> 
[Microsoft.Azure.WebJobs]: http://www.nuget.org/packages/Microsoft.Azure.WebJobs/2.1.0-beta1
[Microsoft.Azure.WebJobs.Extensions.DocumentDB]: http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DocumentDB/1.1.0-beta1
[Microsoft.Azure.WebJobs.ServiceBus]: http://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus/2.1.0-beta1
[Microsoft.Azure.WebJobs.Extensions.MobileApps]: http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.MobileApps/1.1.0-beta1
[Microsoft.Azure.WebJobs.Extensions.NotificationHubs]: http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.NotificationHubs/1.1.0-beta1
[Microsoft.Azure.WebJobs.ServiceBus]: http://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus/2.1.0-beta1
[Microsoft.Azure.WebJobs.Extensions.SendGrid]: http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SendGrid/2.1.0-beta1
[Microsoft.Azure.WebJobs.Extensions.Http]: http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Http/1.0.0-beta1
[Microsoft.Azure.WebJobs.Extensions.BotFramework]: http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.BotFramework/1.0.15-beta
[Microsoft.Azure.WebJobs.Extensions.ApiHub]: http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.ApiHub/1.0.0-beta4
[Microsoft.Azure.WebJobs.Extensions.Twilio]: http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Twilio/1.1.0-beta1
[Microsoft.Azure.WebJobs.Extensions]: http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions/2.1.0-beta1


<!-- Links to source --> 
[DocumentDBAttribute]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.DocumentDB/DocumentDBAttribute.cs
[EventHubAttribute]: https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/EventHubAttribute.cs
[EventHubTriggerAttribute]: https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/EventHubTriggerAttribute.cs
[MobileTableAttribute]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs
[NotificationHubAttribute]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.NotificationHubs/NotificationHubAttribute.cs 
[ServiceBusAttribute]: https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAttribute.cs
[ServiceBusAccountAttribute]: https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAccountAttribute.cs
[QueueAttribute]: https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/QueueAttribute.cs
[StorageAccountAttribute]: https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs
[BlobAttribute]: https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobAttribute.cs
[TableAttribute]: https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TableAttribute.cs
[TwilioSmsAttribute]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Twilio/TwilioSMSAttribute.cs
[SendGridAttribute]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.SendGrid/SendGridAttribute.cs
[HttpTriggerAttribute]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/dev/src/WebJobs.Extensions.Http/HttpTriggerAttribute.cs
[ApiHubFileAttribute]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.ApiHub/ApiHubFileAttribute.cs
[TimerTriggerAttribute]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerTriggerAttribute.cs

