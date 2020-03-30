---
title: Azure 함수에서 연결 관리
description: 정적 연결 클라이언트를 사용하여 Azure Functions에서 성능 문제를 방지하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 02/25/2018
ms.openlocfilehash: 872ad9a1b8f0a7da6fe410e68f08469ac11045a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276453"
---
# <a name="manage-connections-in-azure-functions"></a>Azure 함수에서 연결 관리

함수 앱의 함수는 리소스를 공유합니다. 이러한 공유 리소스 중에는 HTTP 연결, 데이터베이스 연결 및 Azure Storage와 같은 서비스에 대한 연결이 있습니다. 많은 함수가 동시에 실행되면 사용 가능한 연결이 부족해질 수 있습니다. 이 문서에서는 필요한 것보다 많은 연결을 사용하지 않도록 함수를 코딩하는 방법을 설명합니다.

## <a name="connection-limit"></a>연결 제한

함수 앱이 [샌드박스 환경에서](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)실행되므로 사용 가능한 연결 수가 부분적으로 제한됩니다. 샌드박스가 코드에 부과하는 제한 사항 중 하나는 인스턴스당 현재 600개의 활성 연결(총 1,200개)인 아웃바운드 연결 수에 대한 제한입니다. 이 제한에 도달하면 함수 런타임은 로그에 다음 메시지를 `Host thresholds exceeded: Connections`씁니다. 자세한 내용은 함수 [서비스 제한을](functions-scale.md#service-limits)참조하십시오.

이 제한은 인스턴스당 입니다. 확장 컨트롤러가 더 많은 요청을 처리하기 위해 [함수 앱 인스턴스를 추가하면](functions-scale.md#how-the-consumption-and-premium-plans-work) 각 인스턴스에는 독립적인 연결 제한이 있습니다. 즉, 전역 연결 제한이 없으며 모든 활성 인스턴스에서 600개 이상의 활성 연결을 가질 수 있습니다.

문제 해결 을 할 때 함수 앱에 대한 응용 프로그램 인사이트를 사용하도록 설정했는지 확인하십시오. 애플리케이션 인사이트를 사용하면 실행과 같은 기능 앱에 대한 메트릭을 볼 수 있습니다. 자세한 내용은 [응용 프로그램 인사이트 원격 분석 보기](functions-monitoring.md#view-telemetry-in-application-insights)를 참조하십시오.  

## <a name="static-clients"></a>정적 클라이언트

필요한 것보다 더 많은 연결을 유지하지 않으려면, 각 함수 호출을 사용하여 새 인스턴스를 만드는 대신 클라이언트 인스턴스를 다시 사용합니다. 함수를 작성할 수 있는 모든 언어에 대해 클라이언트 연결을 다시 사용하는 것이 좋습니다. 예를 들어 [HttpClient,](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx) [DocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
)및 Azure Storage 클라이언트와 같은 .NET 클라이언트는 정적 단일 클라이언트를 사용하는 경우 연결을 관리할 수 있습니다.

다음은 Azure Functions 응용 프로그램에서 서비스 별 클라이언트를 사용할 때 따라야 할 몇 가지 지침입니다.

- 모든 함수 호출을 사용하여 새 클라이언트를 만들지 *마십시오.*
- 모든 함수 호출에서 사용할 수 있는 단일 정적 클라이언트를 *만듭니다.*
- 다른 함수가 동일한 서비스를 사용하는 경우 공유 도우미 클래스에서 단일 정적 클라이언트를 만드는 *것이 좋습니다.*

## <a name="client-code-examples"></a>클라이언트 코드 예제

이 섹션에서는 함수 코드에서 클라이언트를 만들고 사용하기 위한 모범 사례를 보여줍니다.

### <a name="httpclient-example-c"></a>HttpClient 예제(C#)

다음은 정적 [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx) 인스턴스를 만드는 C# 함수 코드의 예입니다.

```cs
// Create a single, static HttpClient
private static HttpClient httpClient = new HttpClient();

public static async Task Run(string input)
{
    var response = await httpClient.GetAsync("https://example.com");
    // Rest of function
}
```

.NET에서 [HttpClient에](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx) 대한 일반적인 질문은 "클라이언트를 폐기해야 합니까?" 입니다. 일반적으로 개체를 사용 하 `IDisposable` 여 완료 될 때 구현 하는 개체를 삭제 합니다. 그러나 함수가 끝날 때 정적 클라이언트를 사용하지 않으므로 정적 클라이언트를 삭제하지 않습니다. 정적 클라이언트가 애플리케이션 기간 동안 지속되도록 합니다.

### <a name="http-agent-examples-javascript"></a>HTTP 에이전트 예제(자바스크립트)

더 나은 연결 관리 옵션을 제공 하므로 [`http.agent`](https://nodejs.org/dist/latest-v6.x/docs/api/http.html#http_class_http_agent) 모듈과 같은 네이티브 메서드 대신 `node-fetch` 네이티브 클래스를 사용 해야 합니다. 연결 매개 변수는 클래스의 `http.agent` 옵션을 통해 구성됩니다. HTTP 에이전트에서 사용할 수 있는 자세한 옵션은 [새 에이전트(옵션)를\[\]](https://nodejs.org/dist/latest-v6.x/docs/api/http.html#http_new_agent_options)참조하십시오.

사용되는 `http.request()` `http.globalAgent` 전역 클래스에는 이러한 모든 값이 해당 기본값으로 설정되어 있습니다. 함수에서 연결 제한을 구성하는 방법은 최대 수를 전역적으로 설정하는 것이 좋습니다. 다음 예제에서는 함수 앱에 대한 소켓의 최대 수를 설정합니다.

```js
http.globalAgent.maxSockets = 200;
```

 다음 예제는 해당 요청에 대해서만 사용자 지정 HTTP 에이전트를 통해 새 HTTP 요청을 만듭니다.

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

### <a name="cosmosclient-code-example-javascript"></a>코스모스클라이언트 코드 예제(자바스크립트)
[코스모스클라이언트는](/javascript/api/@azure/cosmos/cosmosclient) Azure 코스모스 DB 인스턴스에 연결합니다. Azure Cosmos DB 문서에서는 [애플리케이션 수명 동안 싱글톤 Azure Cosmos DB 클라이언트를 사용](../cosmos-db/performance-tips.md#sdk-usage)하도록 권장하고 있습니다. 다음 예제에서는 함수에서 이 작업을 수행하는 하나의 패턴을 보여 줍니다.

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

함수 코드는 SQL[Server(SqlClient)에](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx)대한 .NET 프레임워크 데이터 공급자를 사용하여 SQL 관계형 데이터베이스에 연결할 수 있습니다. 엔터티 프레임워크 와 같은 ADO.NET 의존하는 데이터 프레임워크의 기본 공급자이기도 [합니다.](https://msdn.microsoft.com/library/aa937723(v=vs.113).aspx) [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx) 및 [DocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
)와 달리, ADO.NET은 기본적으로 연결 풀링을 구현합니다. 그러나 여전히 연결이 부족할 수 있으므로 데이터베이스에 대한 연결을 최적화해야 합니다. 자세한 내용은 [SQL Server 연결 풀링(ADO.NET)](https://docs.microsoft.com/dotnet/framework/data/adonet/sql-server-connection-pooling)을 참조하세요.

> [!TIP]
> 엔터티 프레임워크와 같은 일부 데이터 프레임워크는 일반적으로 구성 파일의 **ConnectionStrings** 섹션에서 연결 문자열을 가져옵니다. 이 경우, 함수 앱 설정의 **연결 문자열** 컬렉션과 로컬 프로젝트의 [local.settings.json 파일](functions-run-local.md#local-settings-file)에 SQL 데이터베이스 연결 문자열을 명시적으로 추가해야 합니다. 함수 코드에서 [SqlConnection](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection(v=vs.110).aspx) 인스턴스를 만드는 경우 다른 연결과 **함께 응용 프로그램 설정에** 연결 문자열 값을 저장해야 합니다.

## <a name="next-steps"></a>다음 단계

정적 클라이언트를 권장하는 이유에 대한 자세한 내용은 [부적절한 인스턴스화 안티패턴을](https://docs.microsoft.com/azure/architecture/antipatterns/improper-instantiation/)참조하십시오.

Azure Functions 성능 팁에 대한 자세한 내용은 [Azure 함수의 성능 및 안정성 최적화](functions-best-practices.md)를 참조하세요.
