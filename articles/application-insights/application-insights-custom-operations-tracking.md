---
title: "Azure Application Insights .NET SDK를 통한 사용자 지정 작업 추적 | Microsoft Docs"
description: "Azure Application Insights .NET SDK를 통한 사용자 지정 작업 추적 "
services: application-insights
documentationcenter: .net
author: SergeyKanzhelev
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 06/31/2017
ms.author: sergkanz
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 883750c939aa3bd605189f513e5ce74642f91709
ms.contentlocale: ko-kr
ms.lasthandoff: 07/21/2017

---

# <a name="tracking-custom-operations-with-application-insights-net-sdk"></a>Application Insights .NET SDK를 통한 사용자 지정 작업 추적 

Application Insights SDK는 HTTP 요청, SQL 쿼리 등 종속 서비스에 대한 호출과 수신되는 HTTP 요청을 자동으로 추적합니다. 여러 마이크로 서비스가 결합된 응용 프로그램의 역할을 합니다. 요청 및 종속성의 추적과 상관은 모든 마이크로 서비스 전체에서 전체 응용 프로그램 응답성 및 안정성에 대한 가시성을 제공합니다. 점진적으로 그 목록을 확장해 나가고 다른 유명 플랫폼과 프레임워크의 자동 취합도 제공할 것입니다. 

일반적으로는 지원할 수 없는 응용 프로그램 패턴 종류가 있습니다. 이러한 패턴의 적절한 모니터링에는 수동 코드 계측이 필요합니다. 이 문서에서는 수동 계측이 필요할 수 있는 몇 가지 패턴에 대해 설명합니다. 사용자 지정 큐 처리나 장기 실행 백그라운드 작업이 여기에 포함됩니다.

이 문서는 Application Insights SDK를 통해 사용자 지정 작업을 추적하는 방법에 대한 지침을 제공합니다.

이 문서와 관련된 항목은 다음과 같습니다.
- .NET용 Application Insights(즉 Base SDK) 버전 `2.4+`
- 웹 응용 프로그램용 Application Insights(ASP.NET 실행) 버전 `2.4+`
- AspNetCore용 Application Insights 버전 `2.1+`

## <a name="overview"></a>개요
이 작업을 통해 응용 프로그램 작업 실행의 일부 논리적 부분을 이해합니다. 여기에는 이름, 시작 시간 및 길이, 실행 컨텍스트(예: 사용자 이름), 속성, 결과가 있습니다. 작업 `B`에서 작업 `A`를 시작한 경우 - 작업 `B`는 작업 `A`의 부모로 설정됩니다.  작업에는 하나의 부모와 여러 자식 작업이 있을 수 있습니다. 작업과 원격 분석 상관 관계에 대한 자세한 내용은 [여기](application-insights-correlation.md)에서 확인할 수 있습니다.

Application Insights .NET SDK에서 작업은 추상 클래스 [OperationTelemetry](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/Core/Managed/Shared/Extensibility/Implementation/OperationTelemetry.cs)와 해당하는 하위 항목 [RequestTelemetry](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/Core/Managed/Shared/DataContracts/RequestTelemetry.cs) 및 [DependencyTelemetry](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/Core/Managed/Shared/DataContracts/DependencyTelemetry.cs)로 설명됩니다.

## <a name="incoming-operations-tracking"></a>들어오는 작업 추적 
Application Insights 웹 SDK는 IIS 파이프라인과 모든 ASP.NET Core 응용 프로그램에서 실행되는 ASP.NET 응용 프로그램에 대한 HTTP 요청을 자동으로 수집합니다. 다른 플랫폼과 프레임워크에 대해서 커뮤니티 지원 솔루션이 있습니다. 그러나 응용 프로그램이 표준 또는 커뮤니티 지원 솔루션을 통해 지원되지 않는 경우 직접 계측할 수 있습니다.

사용자 지정 추적이 필요한 다른 예로, 큐에서 항목을 받는 작업자를 들 수 있습니다. 일부 큐의 경우 메시지를 이 큐에 추가하는 호출이 종속 항목으로 추적됩니다. 그러나 메시지 처리를 설명하는 상위 수준 항목은 자동으로 수집되지 않습니다.

이러한 작업을 어떻게 추적할 수 있는지 살펴보겠습니다.

상위 수준 이 작업은 `RequestTelemetry`를 만들고 알려진 속성을 설정합니다. 작업이 완료되면 원격 분석을 추적합니다. 다음 예제에서 이것을 보여 줍니다.

### <a name="http-request-in-owin-self-hosted-app"></a>Owin 자체 호스팅 앱의 HTTP 요청
[상관을 위한 HTTP 프로토콜](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md)을 따르며 이를 설명하는 헤더를 수신하게 됩니다.

``` C#
public class ApplicationInsightsMiddleware : OwinMiddleware
{
    private readonly TelemetryClient telemetryClient = new TelemetryClient(TelemetryConfiguration.Active);
    
    public ApplicationInsightsMiddleware(OwinMiddleware next) : base(next) {}

    public override async Task Invoke(IOwinContext context)
    {
        // Let's create and start RequestTelemetry
        var requestTelemetry = new RequestTelemetry
        {
            Name = $"{context.Request.Method} {context.Request.Uri.GetLeftPart(UriPartial.Path)}"
        };

        // if there is Request-Id recevied from the upstream service, set telemetry context accordingly
        if (context.Request.Headers.ContainsKey("Request-Id"))
        {
            var requestId = context.Request.Headers.Get("Request-Id");
            // get the operation id from the Request-Id (if you follow the 'Http Protocol for Correlation')
            requestTelemetry.Context.Operation.Id = GetOperationId(requestId);
            requestTelemetry.Context.Operation.ParentId = requestId;
        }

        // StartOperation is a helper method that allows to correlate 
        // current operations with nested operations/telemetry
        // and initializes start time and duration on telemetry item
        var operation = telemetryClient.StartOperation(requestTelemetry);

        // process request
        try
        {
            await Next.Invoke(context);
        }
        catch (Exception e)
        {
            requestTelemetry.Success = false;
            telemetryClient.TrackException(e);
            throw;
        }
        finally
        {
            // update status code and success as appropriate
            if (context.Response != null)
            {
                requestTelemetry.ResponseCode = context.Response.StatusCode.ToString();
                requestTelemetry.Success = context.Response.StatusCode >= 200 && context.Response.StatusCode <= 299;
            }
            else
            {
                requestTelemetry.Success = false;
            }

            // now it's time to stop operation (and track telemetry)
            telemetryClient.StopOperation(operation);
        }
    }
    
    public static string GetOperationId(string id)
    {
        // returns root Id from the '|' to first '.' if any
        int rootEnd = id.IndexOf('.');
        if (rootEnd < 0)
            rootEnd = id.Length;

        int rootStart = id[0] == '|' ? 1 : 0;
        return id.Substring(rootStart, rootEnd - rootStart);
    }
}
```

HTTP 상관 프로토콜도 `Correlation-Context` 헤더를 선언하나 편의상 생략하기로 합니다.

## <a name="queue-instrumentation"></a>큐 계측
HTTP 통신을 위해 상관 세부 정보를 전달하는 프로토콜을 생성했습니다. 어떤 큐 프로토콜에서는 메시지와 함께 추가 메타데이터를 전달할 수 있고 다른 프로토콜은 그렇지 못합니다.

### <a name="service-bus-queue"></a>서비스 버스 큐
[서비스 버스 큐](../service-bus-messaging/index.md)에서는 메시지와 함께 속성 모음을 전달할 수 있으며 이를 통해 상관 ID를 전달하겠습니다.

서비스 버스 큐는 TCP 기반 프로토콜을 사용합니다. Application Insights는 큐 작업을 자동으로 추적하지 않으므로 수동으로 추적하겠습니다. 큐에서 제거 작업은 푸시 스타일 API로, 전혀 추적할 수 없습니다.

#### <a name="enqueue"></a>큐에 넣기

```C#
public async Task Enqueue(string payload)
{
    // StartOperation is a helper method that initializes telemetry item
    // and allows to correlate this operation with its parent and children
    var operation = telemetryClient.StartOperation<DependencyTelemetry>("enqueue " + queueName);
    operation.Telemetry.Type = "Queue";
    operation.Telemetry.Data = "Enqueue " + queueName;

    var message = new BrokeredMessage(payload);
    // ServiceBus Queue allows to pass property bag along with the message
    // we will use them to pass our correlation identifiers (and other context)
    // to the consumer
    message.Properties.Add("ParentId", operation.Telemetry.Id);
    message.Properties.Add("RootId", operation.Telemetry.Context.Operation.Id);

    try
    {
        await queue.SendAsync(message);
        
        // set operation.Telemetry Success and ResponseCode here
        operation.Telemetry.Success = true;
    }
    catch (Exception e)
    {
        telemetryClient.TrackException(e);
        // set operation.Telemetry Success and ResponseCode here
        operation.Telemetry.Success = false;
        throw;
    }
    finally
    {
        telemetryClient.StopOperation(operation);
    }
}
```

#### <a name="process"></a>Process
```C#
public async Task Process(BrokeredMessage message)
{
    // once the message is taken from the queue, create ReqeustTelemetry to track its processing
    // it may also make sense to get name from the message
    RequestTelemetry requestTelemetry = new RequestTelemetry { Name = "Dequeue " + queueName };

    var rootId = message.Properties["RootId"].ToString();
    var parentId = message.Properties["ParentId"].ToString();
    // get the operation id from the Request-Id (if you follow the 'Http Protocol for Correlation')
    requestTelemetry.Context.Operation.Id = rootId;
    requestTelemetry.Context.Operation.ParentId = parentId;

    var operation = telemetryClient.StartOperation(requestTelemetry);

    try
    {
        await ProcessMessage();
    }
    catch (Exception e)
    {
        telemetryClient.TrackException(e);
        throw;
    }
    finally
    {
        // update status code and success as appropriate
        telemetryClient.StopOperation(operation);
    }
}
```

### <a name="azure-storage-queue"></a>Azure Storage Queue
다음 예에서는 [Azure Storage Queue](../storage/storage-dotnet-how-to-use-queues.md) 작업을 추적하고 제작자, 소비자 및 Azure Storage 간의 원격 분석 상관 방법을 보여 줍니다. 

Azure Storage Queue에는 HTTP 요청에 대해 ApplicationInsights DependencyCollector가 추적하는 큐에 대한 모든 호출과 HTTP API가 있습니다.
`applicationInsights.config`에 `Microsoft.ApplicationInsights.DependencyCollector.HttpDependenciesParsingTelemetryInitializer`가 있는지 확인하거나, [여기](app-insights-api-filtering-sampling.md)에서 설명한 대로 프로그래밍 방식으로 추가합니다.

ApplicationInsights를 수동으로 구성할 경우 다음과 유사하게 `Microsoft.ApplicationInsights.DependencyCollector.DependencyTrackingTelemetryModule`을 생성 및 초기화해야 합니다.
 
``` C#
DependencyTrackingTelemetryModule module = new DependencyTrackingTelemetryModule();

// you can prevent correlation header injection to some domains by adding it to the excluded list.
// Make sure you add Azure Storage endpoint, otherwise you may experience request signature validation issues on the Storage service side
module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.windows.net");
module.Initialize(TelemetryConfiguration.Active);

// do not forget to dispose module during application shutdown
```

ApplicationInsights 작업 ID를 Azure Storage 요청 ID와 상관시키려 할 수도 있습니다. 저장 장치 요청 클라이언트 및 서버 요청 ID를 설정 및 확보하는 방법은 [이 문서](../storage/storage-monitoring-diagnosing-troubleshooting.md#end-to-end-tracing)에서 확인하세요.

#### <a name="enqueue"></a>큐에 넣기
Azure Storage Queue는 HTTP API를 지원하므로 이 큐를 통한 모든 작업은 ApplicationInsights에서 자동으로 추적됩니다. 대부분의 경우 이 계측으로 충분합니다.
그러나 제작자 추적과 소비자 측의 추적을 상호 연결하기 위해서는 '상관을 위한 HTTP 프로토콜'에서 수행한 것과 유사하게 일부 상관 컨텍스트를 전달해야 합니다. 

아래 예에서는 선택적 `Enqueue` 작업을 추적합니다. 이를 통해 다음이 가능합니다.
 - 재시도 상호 연결(있는 경우): 모두 `Enqueue` 작업인 공통 부모가 있음. 그렇지 않으면 들어오는 요청의 자식으로 추적됩니다. 따라서 큐에 대한 여러 논리 요청이 있으면 재시도의 원인이 되는 호출을 찾는 것이 어려울 수 있습니다.
 - Azure Storage 로그(있고 필요한 경우)와 ApplicationInsights 원격 분석 상호 연결

`Enqueue` 작업은 '부모' 작업(예: 들어오는 HTTP 요청)의 자식이며 'HTTP' 종속성 호출은 `Enqueue` 작업의 자식이고 들어오는 요청의 손자입니다.

```C#
public async Task Enqueue(CloudQueue queue, string message)
{
    var operation = telemetryClient.StartOperation<DependencyTelemetry>("enqueue " + queue.Name);
    operation.Telemetry.Type = "Queue";
    operation.Telemetry.Data = "Enqueue " + queue.Name;

    // MessagePayload represents your custom message and also serializes correlation identifiers into payload
    // e.g. if you choose to pass payload serialized to json, it may look like
    // {'RootId' : 'some-id', 'ParentId' : '|some-id.1.2.3.', 'message' : 'your message to process'}
    var jsonPayload = JsonConvert.SerializeObject(new MessagePayload
    {
        RootId = operation.Telemetry.Context.Operation.Id,
        ParentId = operation.Telemetry.Id,
        Payload = message
    });
    
    CloudQueueMessage queueMessage = new CloudQueueMessage(jsonPayload);

    // Add operation.Telemetry.Id to the OperationContext to correlate Azure Storage logs and ApplciationInsights telemetry
    OperationContext context = new OperationContext { ClientRequestID = operation.Telemetry.Id};

    try
    {
        await queue.AddMessageAsync(queueMessage, null, null, new QueueRequestOptions(), context);
    }
    catch (StorageException e)
    {
        operation.Telemetry.Properties.Add("AzureServiceRequestID", e.RequestInformation.ServiceRequestID);
        operation.Telemetry.Success = false;
        operation.Telemetry.ResultCode = e.RequestInformation.HttpStatusCode.ToString();
        telemetryClient.TrackException(e);
    }
    finally
    {
        // update status code and success as appropriate
        telemetryClient.StopOperation(operation);
    }
}  
```

응용 프로그램 보고서에서 원격 분석의 크기를 줄이려는 경우나 어떤 이유로 `enqueue` 작업을 추적하지 않으려는 경우 `Activity` API를 직접 사용할 수 있습니다.

- ApplicationInsights 작업을 시작하는 대신 새 `Activity`를 만들어 시작합니다(작업 이름을 제외한 다른 속성은 할당하지 않아도 됨).
- `operation.Telemetry.Id` 대신 메시지 페이로드에 `yourActivity.Id`를 직렬화합니다. `Activity.Current.Id`를 사용할 수도 있습니다.


#### <a name="dequeue"></a>큐에서 제거
`Enqueue`와 유사하게 Azure Storage Queue에 대한 실제 HTTP 요청은 ApplicationInsights에서 자동으로 추적됩니다. 그러나 `Enqueue` 작업은 '들어오는' 요청 컨텍스트 같은 부모 컨텍스트에서 일어날 것입니다. Application Insights SDK는 이러한 작업(및 해당하는 HTTP 부분)을 부모 요청 및 동일한 범위에서 보고되는 다른 원격 분석과 자동으로 상호 연결합니다.

까다로운 `Dequeue`작업: Application Insights SDK는 HTTP 요청을 자동으로 추적하지만 메시지가 구문 분석되기 전까지는 상관 컨텍스트를 알지 못합니다. 원격 분석의 나머지 부분과, 메시지를 받기 위한 HTTP 요청을 상호 연결할 수는 없습니다.

많은 경우 큐에 대한 HTTP 요청을 다른 추적과도 상호 연결하는 것이 유용할 수 있습니다. 다음 예제에서는 이 방법을 보여 줍니다.

``` C#
public async Task<MessagePayload> Dequeue(CloudQueue queue)
{
    var telemetry = new DependencyTelemetry
    {
        Type = "Queue",
        Name = "Dequeue " + queue.Name
    };

    telemetry.Start();

    try
    {
        var message = await queue.GetMessageAsync();

        if (message != null)
        {
            var payload = JsonConvert.DeserializeObject<MessagePayload>(message.AsString);

            // if there is a message, we want to correlate Dequeue operation with processing
            // However we will only know what correlation Id to use AFTER we will get it from the message
            // So we will report telemetry once we know the Ids.
            telemetry.Context.Operation.Id = payload.RootId;
            telemetry.Context.Operation.ParentId = payload.ParentId;

            // delete message
            return payload;
        }
    }
    catch (StorageException e)
    {
        telemetry.Properties.Add("AzureServiceRequestID", e.RequestInformation.ServiceRequestID);
        telemetry.Success = false;
        telemetry.ResultCode = e.RequestInformation.HttpStatusCode.ToString();
        telemetryClient.TrackException(e);
    }
    finally
    {
        // update status code and success as appropriate
        telemetry.Stop();
        telemetryClient.Track(telemetry);
    }

    return null;
}
```

#### <a name="process"></a>Process

다음 예제에서는 들어오는 HTTP 요청을 추적한 방법과 유사하게 '들어오는' 메시지를 추적합니다.

```C#
public async Task Process(MessagePayload message)
{
    // once the message is dequeued from the queue, create ReqeustTelemetry to track it's processing
    RequestTelemetry requestTelemetry = new RequestTelemetry { Name = "Dequeue " + queueName };
    // it may also make sense to get name from the message
    requestTelemetry.Context.Operation.Id = message.RootId;
    requestTelemetry.Context.Operation.ParentId = message.ParentId;

    var operation = telemetryClient.StartOperation(requestTelemetry);

    try
    {
        await ProcessMessage();
    }
    catch (Exception e)
    {
        telemetryClient.TrackException(e);
        throw;
    }
    finally
    {
        // update status code and success as appropriate
        telemetryClient.StopOperation(operation);
    }
}
```

마찬가지로 다른 큐 작업을 계측할 수 있습니다. 피크 작업은 큐에서 제거와 유사한 방식으로 계측해야 합니다. 큐 관리 작업의 계측은 필요하지 않습니다. Application Insights는 HTTP와 같은 작업을 추적하며 대부분의 경우 이 정도로 충분합니다.

메시지 삭제를 계측할 때는 작업(상관) 식별자를 설정해야 합니다. 또는 원격 분석 항목에서 `Activity` API를 사용할 수 있습니다(그러면 원격 분석 항목에서 작업 식별자를 설정할 필요가 없음. ApplicationInsights가 이 작업을 수행함).

- 큐에서 항목을 가져온 후 새 `Activity` 만들기
- `Activity.SetParentId(message.ParentId)`를 사용하여 소비자와 제작자 로그 상호 연결
- `Activity` 시작
- `Start/StopOperation` 도우미를 사용하여 큐에서 제거, 프로세스, 삭제 작업을 추적합니다. 동일한 비동기 제어 흐름(실행 컨텍스트)에서 수행합니다. 이런 식으로 제대로 상호 연결됩니다.
- 그런 다음 `Activity` 중지
- `Start/StopOperation`을 사용하거나 수동으로 추적 원격 분석 호출 

### <a name="batch-processing"></a>일괄 처리
일부 큐에서는 한 요청을 통해 여러 메시지를 큐에서 제거할 수 있으나 이러한 메시지 처리는 독립적으로 일어나고 서로 다른 논리 작업에 속할 수 있습니다.
이 경우 `Dequeue` 작업을 특정 메시지 처리와 상호 연결할 수 없게 됩니다.

각각의 메시지 처리는 자체 비동기 제어 흐름에서 발생해야 합니다. [나가는 종속성 추적](#outgoing-dependencies-tracking) 섹션에서 이에 관한 자세한 정보를 확인할 수 있습니다.

## <a name="long-running-background-tasks"></a>장기 실행 백그라운드 작업 실행 
일부 응용 프로그램은 사용자 요청에 따라 일어날 수 있는 장기 실행 작업을 시작합니다. 추적/계측 관점에서 보면 이것이 요청이나 종속성 계측과 다르지 않습니다. 

``` C#
async Task BackgroundTask()
{
    var operation = telemetryClient.StartOperation<RequestTelemetry>(taskName);
    operation.Telemetry.Type = "Background";
    try
    {
        int progress = 0;
        while (progress < 100)
        {
            // process task
            telemetryClient.TrackTrace($"done {progress++}%");
        }
        // update status code and success as appropriate
    }
    catch (Exception e)
    {
        telemetryClient.TrackException(e);
        // update status code and success as appropriate
        throw;
    }
    finally
    {
        telemetryClient.StopOperation(operation);
    }
}
```

이 예에서는 `telemetryClient.StartOperation`을 사용하여 `RequestTelemetry`를 만들고 상관 컨텍스트를 채웁니다. 작업을 예약한 들어오는 요청에 의해 만들어진 부모 작업을 가정해 보겠습니다. `BackgroundTask`가 들어오는 요청과 동일한 비동기 제어 흐름에서 시작되는 한 이 작업은 해당 부모 작업과 상호 연결됩니다. `BackgroundTask` 및 모든 중첩 원격 분석 항목은 요청이 종료된 후에도 원인이 된 요청과 자동으로 상호 연결됩니다.

연결된 작업(활동)이 없는 백그라운드 스레드로부터 작업이 시작된 경우 `BackgroundTask`에는 부모가 없습니다. 그러나 중첩된 작업이 있을 수 있고 이 작업에서 보고되는 모든 원격 분석 항목은 `BackgroundTask`에서 생성된 `RequestTelemetry`에 상호 연결됩니다.

## <a name="outgoing-dependencies-tracking"></a>나가는 종속성 추적
ApplicationInsights에서 지원하지 않은 일부 작업이나 자체 '종속성' 종류를 추적하고자 할 수 있습니다.

ServiceBus Queue의 `Enqueue` 메서드나 Azure Storage Queue를 이러한 사용자 지정 추적의 예제로 사용할 수 있습니다.

사용자 지정 종속성 추적을 위한 일반적인 접근 방법은 다음과 같습니다.
- 상관에 필요한 `DependencyTelemetry` 속성과 기타 속성(시작 타임스탬프, 길이)을 입력하는 `TelemetryClient.StartOperation`(확장) 메서드를 호출합니다.
- `DependencyTelemetry`에서 다른 사용자 지정 속성 설정: 이름 및 기타 필요한 다른 컨텍스트
- 종속성 호출 및 대기
- 완료되면 `StopOperation`을 통해 작업 중지
- 예외 처리

`StopOperation`은 시작된 작업만 중지함: 현재 실행 중인 작업이 중지하려는 작업이 아닌 경우 StopOperation은 아무 것도 수행하지 않습니다. 동일한 실행 컨텍스트에서 병렬로 여러 작업을 시작한 경우 발생할 수 있음:

```C#
var firstOperation = telemetryClient.StartOperation<DependencyTelemetry>("task 1");
var firstOperation = telemetryClient.StartOperation<DependencyTelemetry>("task 1");
var firstTask = RunMyTaskAsync();

var secondOperation = telemetryClient.StartOperation<DependencyTelemetry>("task 2");
var secondTask = RunMyTaskAsync();

await firstTask;

// this will do nothing and will not report telemetry for the first operation
// as currently secondOperation is active
telemetryClient.StopOperation(firstOperation); 

await secondTask;
```

따라서 항상 `StartOperation`을 호출하고 자체 컨텍스트에서 작업을 실행할 것:
```C#
public async Task RunMyTaskAsync()
{
    var operation = telemetryClient.StartOperation<DependencyTelemetry>("task 1");
    try 
    {
        var myTask = await StartMyTaskAsync();
        // update status code and success as appropriate
    }
    catch(...) 
    {
        // update status code and success as appropriate
    }
    finally 
    {
        telemetryClient.StopOperation(operation);
    }
}
```

## <a name="next-steps"></a>다음 단계

- Application Insights의 [원격 분석 상관](application-insights-correlation.md) 기본 사항을 알아봅니다.
- Application Insights 형식 및 데이터 모델에 대한 자세한 내용은 [데이터 모델](application-insights-data-model.md)을 참조하세요.
- 사용자 지정 [이벤트 및 메트릭](app-insights-api-custom-events-metrics.md)을 Application Insights에 보고합니다.
- 표준 컨텍스트 속성 컬렉션 [구성](app-insights-configuration-with-applicationinsights-config.md#telemetry-initializers-aspnet)을 확인합니다.
- [System.Diagnostics.Activity 사용자 가이드](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md)에서 원격 분석 상관 방법 확인

