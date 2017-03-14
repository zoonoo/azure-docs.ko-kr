---
title: "Azure Functions 개발자 참조 | Microsoft Docs"
description: "C#을 사용하여 Azure Functions를 개발하는 방법을 알아봅니다."
services: functions
documentationcenter: na
author: christopheranderson
manager: erikre
editor: 
tags: 
keywords: "Azure Functions, 함수, 이벤트 처리, webhook, 동적 계산, 서버가 없는 아키텍처"
ms.assetid: f28cda01-15f3-4047-83f3-e89d5728301c
ms.service: functions
ms.devlang: dotnet
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/13/2016
ms.author: chrande
translationtype: Human Translation
ms.sourcegitcommit: d405c58bf658222ceb72cc2b73e71f2ae1e1ed8d
ms.openlocfilehash: 6b2473ef6336aea5c9a79aad78e02bcfc38b9018
ms.lasthandoff: 02/27/2017


---
# <a name="azure-functions-c-developer-reference"></a>Azure Functions C# 개발자 참조
> [!div class="op_single_selector"]
> * [C# 스크립트](functions-reference-csharp.md)
> * [F# 스크립트](functions-reference-fsharp.md)
> * [Node.JS](functions-reference-node.md)
> 
> 

Azure Functions에 대한 C# 환경은 Azure WebJobs SDK를 기반으로 합니다. 데이터는 메서드 인수를 통해 C# 함수로 흐릅니다. 인수 이름은 `function.json`에 지정되며 함수 로거 및 취소 토큰 같은 항목에 액세스하기 위해 미리 정의된 이름이 있습니다.

이 문서에서는 [Azure Functions 개발자 참조](functions-reference.md)를 이미 읽었다고 가정합니다.

## <a name="how-csx-works"></a>.csx의 작동 원리
`.csx` 서식을 사용하면 "상용구"를 덜 작성하고 C# 함수를 작성하는 데 집중할 수 있습니다. Azure Functions의 경우 네임스페이스 및 클래스에 모든 항목을 래핑하는 대신 평소와 같이 위에 필요한 어셈블리 참조 및 네임스페이스를 포함하면 `Run` 메서드를 정의할 수 있습니다. 모든 클래스를 포함해야 하는 경우 예를 들어 POCO(Plain Old CLR Object) 개체를 정의하려면 동일한 파일 내에 클래스를 포함할 수 있습니다.   

## <a name="binding-to-arguments"></a>인수에 바인딩
다양한 바인딩은 *function.json* 구성의 `name` 속성을 통해 C# 함수에 바인딩됩니다. 각 바인딩에는 바인딩으로 문서화된 지원되는 고유한 형식이 있습니다. 예를 들어 Blob 트리거는 문자열, POCO 또는 기타 몇 가지 유형을 지원할 수 있습니다. 요구 사항에 가장 적합한 형식을 사용할 수 있습니다. POCO 개체는 각 속성에 대해 정의된 Getter 및 setter가 있어야 합니다. 

```csharp
public static void Run(string myBlob, out MyClass myQueueItem)
{
    log.Verbose($"C# Blob trigger function processed: {myBlob}");
    myQueueItem = new MyClass() { Id = "myid" };
}

public class MyClass
{
    public string Id { get; set; }
}
```

> [!TIP]
>
> HTTP 또는 WebHook 바인딩을 사용하려는 경우 [HTTPClient](https://github.com/mspnp/performance-optimization/blob/master/ImproperInstantiation/docs/ImproperInstantiation.md)에서 이 모범 사례 문서를 읽는 것이 좋습니다.
>

## <a name="logging"></a>로깅
C#의 스트리밍 로그에 대한 출력을 기록하려면 `TraceWriter` 형식의 인수를 포함할 수 있습니다. 이름을 `log`로 하는 것이 좋습니다. Azure Functions에서 `Console.Write` 는 피하는 것이 좋습니다.

```csharp
public static void Run(string myBlob, TraceWriter log)
{
    log.Info($"C# Blob trigger function processed: {myBlob}");
}
```

## <a name="async"></a>Async
비동기화 함수를 만들려면 `async` 키워드를 사용하고 `Task` 개체를 반환합니다.

```csharp
public async static Task ProcessQueueMessageAsync(
        string blobName, 
        Stream blobInput,
        Stream blobOutput)
    {
        await blobInput.CopyToAsync(blobOutput, 4096, token);
    }
```

## <a name="cancellation-token"></a>취소 토큰
어떤 경우에는 종료되기 어려운 작업이 있을 수 있습니다. 충돌을 처리할 수 있는 코드를 작성하는 데 가장 좋은 방법이지만 정상 종료 요청을 처리하려는 경우 [`CancellationToken`](https://msdn.microsoft.com/library/system.threading.cancellationtoken.aspx) 형식의 인수를 정의합니다.  호스트 종료가 트리거되는 경우 `CancellationToken` 이 제공됩니다. 

```csharp
public async static Task ProcessQueueMessageAsyncCancellationToken(
        string blobName, 
        Stream blobInput,
        Stream blobOutput,
        CancellationToken token)
    {
        await blobInput.CopyToAsync(blobOutput, 4096, token);
    }
```

## <a name="importing-namespaces"></a>네임스페이스 가져오기
네임스페이스를 가져와야 하는 경우 `using` 절과 함께 정상적으로 수행할 수 있습니다.

```csharp
using System.Net;
using System.Threading.Tasks;

public static Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
```

다음 네임스페이스를 자동으로 가져오므로 다음은 선택적입니다.

* `System`
* `System.Collections.Generic`
* `System.IO`
* `System.Linq`
* `System.Net.Http`
* `System.Threading.Tasks`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`.

## <a name="referencing-external-assemblies"></a>외부 어셈블리 참조
프레임워크 어셈블리의 경우 `#r "AssemblyName"` 지시문을 사용하여 참조를 추가합니다.

```csharp
#r "System.Web.Http"

using System.Net;
using System.Net.Http;
using System.Threading.Tasks;

public static Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
```

다음 어셈블리는 Azure Functions 호스팅 환경에 의해 자동으로 추가됩니다.

* `mscorlib`,
* `System`
* `System.Core`
* `System.Xml`
* `System.Net.Http`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`
* `Microsoft.Azure.WebJobs.Extensions`
* `System.Web.Http`
* `System.Net.Http.Formatting`.

또한 다음 어셈블리는 특수한 경우이며 simplename으로 참조할 수 있습니다(예: `#r "AssemblyName"`).

* `Newtonsoft.Json`
* `Microsoft.WindowsAzure.Storage`
* `Microsoft.ServiceBus`
* `Microsoft.AspNet.WebHooks.Receivers`
* `Microsoft.AspNet.WebHooks.Common`
* `Microsoft.Azure.NotificationHubs`

개인 어셈블리를 참조해야 하는 경우 어셈블리 파일을 함수에 상대적인 `bin` 폴더에 업로드하고 파일 이름(예: `#r "MyAssembly.dll"`)을 사용하여 참조할 수 있습니다. 함수 폴더에 파일을 업로드하는 방법에 대한 내용은 패키지 관리에 대한 다음 섹션을 참조하세요.

## <a name="package-management"></a>패키지 관리
C# 함수에서 NuGet 패키지를 사용하려면 *project.json* 파일을 함수 앱의 파일 시스템에 있는 함수의 폴더에 업로드합니다. 다음은 Microsoft.ProjectOxford.Face 버전 1.1.0에 참조를 추가하는 예제 *project.json* 파일입니다.

```json
{
  "frameworks": {
    "net46":{
      "dependencies": {
        "Microsoft.ProjectOxford.Face": "1.1.0"
      }
    }
   }
}
```

.NET Framework 4.6만 지원되므로 *project.json* 파일이 다음과 같이 `net46`을 지정하도록 합니다.

*project.json* 파일을 업로드하는 경우 런타임은 패키지를 가져오고 패키지 어셈블리에 참조를 자동으로 추가합니다. `#r "AssemblyName"` 지시문을 추가할 필요가 없습니다. 필요한 `using` 문을 *run.csx* 파일에 추가하기만 하면 NuGet 패키지에 정의된 형식을 사용할 수 있습니다.

### <a name="how-to-upload-a-projectjson-file"></a>project.json 파일을 업로드하는 방법
1. 함수 앱을 실행하여 시작하며 이는 Azure 포털에서 함수를 열어 수행할 수 있습니다. 
   
    또한 패키지 설치 출력이 표시되는 스트리밍 로그에 액세스할 수 있습니다. 
2. project.json 파일을 업로드하려면 **Azure Functions 개발자 참조 토픽** 의 [함수 앱 파일을 업데이트하는 방법](functions-reference.md#fileupdate)섹션에 설명되어 있는 방법 중 하나를 사용합니다. 
3. *project.json* 파일을 업로드하면 함수의 스트리밍 로그에 다음 예제와 같은 출력이 표시됩니다.

```
2016-04-04T19:02:48.745 Restoring packages.
2016-04-04T19:02:48.745 Starting NuGet restore
2016-04-04T19:02:50.183 MSBuild auto-detection: using msbuild version '14.0' from 'D:\Program Files (x86)\MSBuild\14.0\bin'.
2016-04-04T19:02:50.261 Feeds used:
2016-04-04T19:02:50.261 C:\DWASFiles\Sites\facavalfunctest\LocalAppData\NuGet\Cache
2016-04-04T19:02:50.261 https://api.nuget.org/v3/index.json
2016-04-04T19:02:50.261 
2016-04-04T19:02:50.511 Restoring packages for D:\home\site\wwwroot\HttpTriggerCSharp1\Project.json...
2016-04-04T19:02:52.800 Installing Newtonsoft.Json 6.0.8.
2016-04-04T19:02:52.800 Installing Microsoft.ProjectOxford.Face 1.1.0.
2016-04-04T19:02:57.095 All packages are compatible with .NETFramework,Version=v4.6.
2016-04-04T19:02:57.189 
2016-04-04T19:02:57.189 
2016-04-04T19:02:57.455 Packages restored.
```

## <a name="environment-variables"></a>환경 변수
환경 변수 또는 앱 설정 값을 가져오려면 다음 코드 예제와 같이 `System.Environment.GetEnvironmentVariable`을 사용합니다.

```csharp
public static void Run(TimerInfo myTimer, TraceWriter log)
{
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}");
    log.Info(GetEnvironmentVariable("AzureWebJobsStorage"));
    log.Info(GetEnvironmentVariable("WEBSITE_SITE_NAME"));
}

public static string GetEnvironmentVariable(string name)
{
    return name + ": " + 
        System.Environment.GetEnvironmentVariable(name, EnvironmentVariableTarget.Process);
}
```

## <a name="reusing-csx-code"></a>.csx 코드 재사용
*run.csx* 파일에 있는 다른 *.csx* 파일에 정의된 클래스와 메서드를 사용할 수 있습니다. 이를 위해 *run.csx* 파일의 `#load` 지시문을 사용합니다. 다음 예제에서는 `MyLogger`이라는 이름의 로깅 루틴이 *myLogger.csx*에서 공유되고 `#load` 지시문을 사용하여 *run.csx*로 로드됩니다. 

예제 *run.csx*:

```csharp
#load "mylogger.csx"

public static void Run(TimerInfo myTimer, TraceWriter log)
{
    log.Verbose($"Log by run.csx: {DateTime.Now}"); 
    MyLogger(log, $"Log by MyLogger: {DateTime.Now}");
}
```

예제 *mylogger.csx*:

```csharp
public static void MyLogger(TraceWriter log, string logtext)
{
    log.Verbose(logtext); 
}
```

공유된 *.csx*를 사용하는 것은 POCO 개체를 사용하는 함수 간 인수를 강력한 형식으로 만들려는 경우에 일반적인 패턴입니다. 다음 간단한 예제에서는 HTTP 트리거 및 큐 트리거는 `Order`라는 이름의 POCO 개체를 공유하여 주문 데이터를 강력한 형식으로 만듭니다.

HTTP 트리거를 위한 *run.csx* 예제:

```cs
#load "..\shared\order.csx"

using System.Net;

public static async Task<HttpResponseMessage> Run(Order req, IAsyncCollector<Order> outputQueueItem, TraceWriter log)
{
    log.Info("C# HTTP trigger function received an order.");
    log.Info(req.ToString());
    log.Info("Submitting to processing queue.");

    if (req.orderId == null)
    {
        return new HttpResponseMessage(HttpStatusCode.BadRequest);
    }
    else
    {
        await outputQueueItem.AddAsync(req);
        return new HttpResponseMessage(HttpStatusCode.OK);
    }
}
```

큐 트리거를 위한 *run.csx* 예제:

```cs
#load "..\shared\order.csx"

using System;

public static void Run(Order myQueueItem, out Order outputQueueItem,TraceWriter log)
{
    log.Info($"C# Queue trigger function processed order...");
    log.Info(myQueueItem.ToString());

    outputQueueItem = myQueueItem;
}
```

*order.csx* 예제: 

```cs
public class Order
{
    public string orderId {get; set; }
    public string custName {get; set;}
    public string custAddress {get; set;}
    public string custEmail {get; set;}
    public string cartId {get; set; }

    public override String ToString()
    {
        return "\n{\n\torderId : " + orderId + 
                  "\n\tcustName : " + custName +             
                  "\n\tcustAddress : " + custAddress +             
                  "\n\tcustEmail : " + custEmail +             
                  "\n\tcartId : " + cartId + "\n}";             
    }
}
```

`#load` 지시문으로 상대 경로를 사용할 수 있습니다.

* `#load "mylogger.csx"` 는 함수 폴더에 있는 파일을 로드합니다.
* `#load "loadedfiles\mylogger.csx"` 는 함수 폴더의 폴더에 있는 파일을 로드합니다.
* `#load "..\shared\mylogger.csx"` 는 함수 폴더와 동일한 수준의 폴더 즉, *wwwroot*에 있는 파일을 로드합니다.

`#load` 지시문은 *.cs* 파일이 아닌 *.csx*(C# 스크립트) 파일에서만 작동합니다. 

## <a name="next-steps"></a>다음 단계
자세한 내용은 다음 리소스를 참조하세요.

* [Azure Functions에 대한 모범 사례](functions-best-practices.md)
* [Azure Functions 개발자 참조](functions-reference.md)
* [Azure Functions F# 개발자 참조](functions-reference-fsharp.md)
* [Azure Functions NodeJS 개발자 참조](functions-reference-node.md)
* [Azure Functions 트리거 및 바인딩](functions-triggers-bindings.md)


