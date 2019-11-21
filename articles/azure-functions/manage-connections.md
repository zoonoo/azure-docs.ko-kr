---
title: Manage connections in Azure Functions
description: 정적 연결 클라이언트를 사용하여 Azure Functions에서 성능 문제를 방지하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 02/25/2018
ms.openlocfilehash: 872ad9a1b8f0a7da6fe410e68f08469ac11045a5
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74226493"
---
# <a name="manage-connections-in-azure-functions"></a>Manage connections in Azure Functions

Functions in a function app share resources. Among those shared resources are connections: HTTP connections, database connections, and connections to services such as Azure Storage. 많은 함수가 동시에 실행되면 사용 가능한 연결이 부족해질 수 있습니다. This article explains how to code your functions to avoid using more connections than they need.

## <a name="connection-limit"></a>Connection limit

The number of available connections is limited partly because a function app runs in a [sandbox environment](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox). One of the restrictions that the sandbox imposes on your code is a limit on the number of outbound connections, which is currently 600 active (1,200 total) connections per instance. When you reach this limit, the functions runtime writes the following message to the logs: `Host thresholds exceeded: Connections`. For more information, see the [Functions service limits](functions-scale.md#service-limits).

This limit is per instance. When the [scale controller adds function app instances](functions-scale.md#how-the-consumption-and-premium-plans-work) to handle more requests, each instance has an independent connection limit. That means there's no global connection limit, and you can have much more than 600 active connections across all active instances.

When troubleshooting, make sure that you have enabled Application Insights for your function app. Application Insights lets you view metrics for your function apps like executions. For more information, see [View telemetry in Application Insights](functions-monitoring.md#view-telemetry-in-application-insights).  

## <a name="static-clients"></a>Static clients

필요한 것보다 더 많은 연결을 유지하지 않으려면, 각 함수 호출을 사용하여 새 인스턴스를 만드는 대신 클라이언트 인스턴스를 다시 사용합니다. We recommend reusing client connections for any language that you might write your function in. For example, .NET clients like the [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx), [DocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
), and Azure Storage clients can manage connections if you use a single, static client.

Here are some guidelines to follow when you're using a service-specific client in an Azure Functions application:

- *Do not* create a new client with every function invocation.
- *Do* create a single, static client that every function invocation can use.
- *Consider* creating a single, static client in a shared helper class if different functions use the same service.

## <a name="client-code-examples"></a>클라이언트 코드 예제

이 섹션에서는 함수 코드에서 클라이언트를 만들고 사용하기 위한 모범 사례를 보여줍니다.

### <a name="httpclient-example-c"></a>HttpClient 예제(C#)

Here's an example of C# function code that creates a static [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx) instance:

```cs
// Create a single, static HttpClient
private static HttpClient httpClient = new HttpClient();

public static async Task Run(string input)
{
    var response = await httpClient.GetAsync("https://example.com");
    // Rest of function
}
```

A common question about [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx) in .NET is "Should I dispose of my client?" In general, you dispose of objects that implement `IDisposable` when you're done using them. But you don't dispose of a static client because you aren't done using it when the function ends. 정적 클라이언트가 애플리케이션 기간 동안 지속되도록 합니다.

### <a name="http-agent-examples-javascript"></a>HTTP agent examples (JavaScript)

개선된 연결 관리 옵션을 제공하므로 비네이티브 메서드 대신 `node-fetch` 모듈과 같이 네이티브 [`http.agent`](https://nodejs.org/dist/latest-v6.x/docs/api/http.html#http_class_http_agent) 클래스를 사용해야 합니다. Connection parameters are configured through options on the `http.agent` class. For detailed options available with the HTTP agent, see [new Agent(\[options\])](https://nodejs.org/dist/latest-v6.x/docs/api/http.html#http_new_agent_options).

The global `http.globalAgent` class used by `http.request()` has all of these values set to their respective defaults. 함수에서 연결 제한을 구성하는 방법은 최대 수를 전역적으로 설정하는 것이 좋습니다. 다음 예제에서는 함수 앱에 대한 소켓의 최대 수를 설정합니다.

```js
http.globalAgent.maxSockets = 200;
```

 The following example creates a new HTTP request with a custom HTTP agent only for that request:

```js
var http = require('http');
var httpAgent = new http.Agent();
httpAgent.maxSockets = 200;
options.agent = httpAgent;
http.request(options, onResponseCallback);
```

### <a name="documentclient-code-example-c"></a>DocumentClient 코드 예제(C#)

[DocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
)는 Azure Cosmos DB 인스턴스에 연결합니다. Azure Cosmos DB 문서에서는 [애플리케이션 수명 동안 싱글톤 Azure Cosmos DB 클라이언트를 사용](https://docs.microsoft.com/azure/cosmos-db/performance-tips#sdk-usage)하도록 권장하고 있습니다. 다음 예제에서는 함수에서 이 작업을 수행하는 하나의 패턴을 보여 줍니다.

```cs
#r "Microsoft.Azure.Documents.Client"
using Microsoft.Azure.Documents.Client;

private static Lazy<DocumentClient> lazyClient = new Lazy<DocumentClient>(InitializeDocumentClient);
private static DocumentClient documentClient => lazyClient.Value;

private static DocumentClient InitializeDocumentClient()
{
    // Perform any initialization here
    var uri = new Uri("example");
    var authKey = "authKey";
    
    return new DocumentClient(uri, authKey);
}

public static async Task Run(string input)
{
    Uri collectionUri = UriFactory.CreateDocumentCollectionUri("database", "collection");
    object document = new { Data = "example" };
    await documentClient.UpsertDocumentAsync(collectionUri, document);
    
    // Rest of function
}
```

### <a name="cosmosclient-code-example-javascript"></a>CosmosClient code example (JavaScript)
[CosmosClient](/javascript/api/@azure/cosmos/cosmosclient) connects to an Azure Cosmos DB instance. Azure Cosmos DB 문서에서는 [애플리케이션 수명 동안 싱글톤 Azure Cosmos DB 클라이언트를 사용](../cosmos-db/performance-tips.md#sdk-usage)하도록 권장하고 있습니다. 다음 예제에서는 함수에서 이 작업을 수행하는 하나의 패턴을 보여 줍니다.

```javascript
const cosmos = require('@azure/cosmos');
const endpoint = process.env.COSMOS_API_URL;
const key = process.env.COSMOS_API_KEY;
const { CosmosClient } = cosmos;

const client = new CosmosClient({ endpoint, key });
// All function invocations also reference the same database and container.
const container = client.database("MyDatabaseName").container("MyContainerName");

module.exports = async function (context) {
    const { resources: itemArray } = await container.items.readAll().fetchAll();
    context.log(itemArray);
}
```

## <a name="sqlclient-connections"></a>SqlClient 연결

Your function code can use the .NET Framework Data Provider for SQL Server ([SqlClient](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx)) to make connections to a SQL relational database. This is also the underlying provider for data frameworks that rely on ADO.NET, such as [Entity Framework](https://msdn.microsoft.com/library/aa937723(v=vs.113).aspx). [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx) 및 [DocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
)와 달리, ADO.NET은 기본적으로 연결 풀링을 구현합니다. But because you can still run out of connections, you should optimize connections to the database. 자세한 내용은 [SQL Server 연결 풀링(ADO.NET)](https://docs.microsoft.com/dotnet/framework/data/adonet/sql-server-connection-pooling)을 참조하세요.

> [!TIP]
> Some data frameworks, such as Entity Framework, typically get connection strings from the **ConnectionStrings** section of a configuration file. 이 경우, 함수 앱 설정의 **연결 문자열** 컬렉션과 로컬 프로젝트의 [local.settings.json 파일](functions-run-local.md#local-settings-file)에 SQL 데이터베이스 연결 문자열을 명시적으로 추가해야 합니다. If you're creating an instance of [SqlConnection](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection(v=vs.110).aspx) in your function code, you should store the connection string value in **Application settings** with your other connections.

## <a name="next-steps"></a>다음 단계

For more information about why we recommend static clients, see [Improper instantiation antipattern](https://docs.microsoft.com/azure/architecture/antipatterns/improper-instantiation/).

Azure Functions 성능 팁에 대한 자세한 내용은 [Azure 함수의 성능 및 안정성 최적화](functions-best-practices.md)를 참조하세요.
