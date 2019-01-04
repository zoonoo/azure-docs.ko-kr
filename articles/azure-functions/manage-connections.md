---
title: Azure Functions에서 연결을 관리하는 방법
description: 정적 연결 클라이언트를 사용하여 Azure Functions에서 성능 문제를 방지하는 방법을 알아봅니다.
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 11/02/2018
ms.author: glenga
ms.openlocfilehash: 9780f98be7d488f87e280a1f391f7cc536ce0f8c
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/06/2018
ms.locfileid: "52999577"
---
# <a name="how-to-manage-connections-in-azure-functions"></a>Azure Functions에서 연결을 관리하는 방법

함수 앱의 함수는 리소스를 공유하며, 이러한 공유된 리소스 중에는 HTTP 연결, 데이터베이스 연결 및 Storage와 같은 Azure 서비스에 대한 연결이 있습니다. 많은 함수가 동시에 실행되면 사용 가능한 연결이 부족해질 수 있습니다. 이 문서에서는 실제로 필요한 것보다 더 많은 연결을 사용하지 않도록 함수를 코딩하는 방법에 대해 설명합니다.

## <a name="connections-limit"></a>연결 제한

함수 앱이 [Azure App Service 샌드박스](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)에서 실행되므로 사용 가능한 연결 수는 부분적으로 제한됩니다. 샌드박스가 코드에 적용되는 제한 중 하나는 [연결 수에 대한 최댓값이며, 현재는 300개](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#numerical-sandbox-limits)입니다. 이 제한에 도달하면 함수 런타임에서 `Host thresholds exceeded: Connections` 메시지가 있는 로그를 만듭니다.

추가 요청을 처리하기 위해 [크기 조정 컨트롤러가 함수 앱 인스턴스를 추가](functions-scale.md#how-the-consumption-plan-works)하면 한도를 초과할 가능성이 증가합니다. 각 함수 앱 인스턴스가 한 번에 많은 함수를 실행할 수 있으며, 이러한 모든 함수는 300개 한도에 포함된 연결을 사용합니다.

## <a name="use-static-clients"></a>정적 클라이언트 사용

필요한 것보다 더 많은 연결을 유지하지 않으려면, 각 함수 호출을 사용하여 새 인스턴스를 만드는 대신 클라이언트 인스턴스를 다시 사용합니다. 단일 정적 클라이언트를 사용하는 경우, [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx), [DocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
) 등의 .NET 클라이언트와 Azure Storage 클라이언트에서 연결을 관리할 수 있습니다.

Azure Functions 애플리케이션에서 서비스 특정 클라이언트를 사용할 때 따라야 할 몇 가지 지침은 다음과 같습니다.

- **금지**: 항상 함수 호출을 사용하여 새 클라이언트를 만듭니다.
- **허용**: 모든 함수 호출에서 사용할 수 있는 단일 정적 클라이언트를 만듭니다.
- **권장**: 다른 함수에서 동일한 서비스를 사용하는 경우 공유 도우미 클래스에 단일 정적 클라이언트를 만듭니다.

## <a name="client-code-examples"></a>클라이언트 코드 예제

이 섹션에서는 함수 코드에서 클라이언트를 만들고 사용하기 위한 모범 사례를 보여줍니다.

### <a name="httpclient-example-c"></a>HttpClient 예제(C#)

정적 [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx)를 만드는 C# 함수 코드 예제는 다음과 같습니다.

```cs
// Create a single, static HttpClient
private static HttpClient httpClient = new HttpClient();

public static async Task Run(string input)
{
    var response = await httpClient.GetAsync("https://example.com");
    // Rest of function
}
```

.NET [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx)에 대한 일반적인 질문은 “내 클라이언트를 삭제해야 할까요?”입니다. 일반적으로 `IDisposable`을 구현하는 개체의 사용이 완료되면 해당 개체를 삭제합니다. 하지만 함수가 종료될 때 정적 클라이언트를 사용하지 않을 경우 이 클라이언트는 삭제하지 않습니다. 정적 클라이언트가 애플리케이션 기간 동안 지속되도록 합니다.

### <a name="http-agent-examples-nodejs"></a>HTTP 에이전트 예제(Node.js)

개선된 연결 관리 옵션을 제공하므로 비네이티브 메서드 대신 `node-fetch` 모듈과 같이 네이티브 [`http.agent`](https://nodejs.org/dist/latest-v6.x/docs/api/http.html#http_class_http_agent) 클래스를 사용해야 합니다. `http.agent` 클래스의 옵션을 사용하여 연결 매개 변수를 구성합니다. HTTP 에이전트에서 사용할 수 있는 자세한 옵션 내용은 [새 에이전트(\[옵션\])](https://nodejs.org/dist/latest-v6.x/docs/api/http.html#http_new_agent_options)를 참조하세요.

`http.request()`에서 사용하는 글로벌 `http.globalAgent`는 이러한 모든 값을 해당 기본값으로 설정합니다. 함수에서 연결 제한을 구성하는 방법은 최대 수를 전역적으로 설정하는 것이 좋습니다. 다음 예제에서는 함수 앱에 대한 소켓의 최대 수를 설정합니다.

```js
http.globalAgent.maxSockets = 200;
```

 다음 예제에서는 해당 요청에 대해서만 사용자 지정 HTTP 에이전트를 사용하여 새 HTTP 요청을 만듭니다.

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

## <a name="sqlclient-connections"></a>SqlClient 연결

함수 코드에서 .NET Framework Data Provider for SQL Server([SqlClient](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx))를 사용하여 SQL 관계형 데이터베이스에 연결할 수 있습니다. SqlClient는 Entity Framework 등 ADO.NET을 사용하는 데이터 프레임워크의 기본 공급자이기도 합니다. [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx) 및 [DocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
)와 달리, ADO.NET은 기본적으로 연결 풀링을 구현합니다. 그래도 연결이 부족해질 수 있으므로 데이터베이스에 대한 연결을 최적화해야 합니다. 자세한 내용은 [SQL Server 연결 풀링(ADO.NET)](https://docs.microsoft.com/dotnet/framework/data/adonet/sql-server-connection-pooling)을 참조하세요.

> [!TIP]
> [Entity Framework](https://msdn.microsoft.com/library/aa937723(v=vs.113).aspx) 등의 일부 데이터 프레임워크는 일반적으로 구성 파일의 **ConnectionStrings** 섹션에서 연결 문자열을 가져옵니다. 이 경우, 함수 앱 설정의 **연결 문자열** 컬렉션과 로컬 프로젝트의 [local.settings.json 파일](functions-run-local.md#local-settings-file)에 SQL 데이터베이스 연결 문자열을 명시적으로 추가해야 합니다. 함수 코드에서 [SqlConnection](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection(v=vs.110).aspx)을 만드는 경우, **애플리케이션 설정**에 다른 연결과 함께 연결 문자열 값을 저장해야 합니다.

## <a name="next-steps"></a>다음 단계

정적 클라이언트가 권장되는 이유에 대한 자세한 내용은 [부적절한 인스턴스화 안티패턴](https://docs.microsoft.com/azure/architecture/antipatterns/improper-instantiation/)을 참조하세요.

Azure Functions 성능 팁에 대한 자세한 내용은 [Azure 함수의 성능 및 안정성 최적화](functions-best-practices.md)를 참조하세요.
