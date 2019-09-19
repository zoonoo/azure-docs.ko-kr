---
title: Gremlin을 사용한 Azure Cosmos DB 리소스 토큰
description: 리소스 토큰을 만들고 사용하여 그래프 데이터베이스에 액세스하는 방법을 알아봅니다.
author: olignat
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: overview
ms.date: 09/06/2019
ms.author: olignat
ms.openlocfilehash: fcb18fb14cf787713735da07ca2048d0853fa46c
ms.sourcegitcommit: b8578b14c8629c4e4dea4c2e90164e42393e8064
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/09/2019
ms.locfileid: "70807014"
---
# <a name="azure-cosmos-db-resource-tokens-with-gremlin"></a>Gremlin을 사용한 Azure Cosmos DB 리소스 토큰
이 문서에서는 [Cosmos DB 리소스 토큰](secure-access-to-data.md)을 사용하여 Gremlin SDK를 통해 그래프 데이터베이스에 액세스하는 방법을 설명합니다.

## <a name="create-a-resource-token"></a>리소스 토큰 만들기

TinkerPop Gremlin SDK에는 리소스 토큰을 만들기 위한 API가 없습니다. 리소스 토큰은 Cosmos DB 개념입니다. 리소스 토큰을 만들려면 [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md)를 다운로드합니다. 애플리케이션이 리소스 토큰을 만들고 사용하여 그래프 데이터베이스에 액세스해야 하는 경우, 두 개의 개별 SDK가 필요합니다.

리소스 토큰 위의 개체 모델 계층 구조:
- **Cosmos DB 계정** - DNS가 연결되어 있는 최상위 엔터티입니다(예: `contoso.gremlin.cosmos.azure.com`).
  - **Cosmos DB 데이터베이스**
    - **User**
      - **사용 권한**
        - ‘토큰’ - 허용되거나 거부되는 작업을 나타내는 **권한** 개체의 속성입니다. 

리소스 토큰의 형식은 `"type=resource&ver=1&sig=<base64 string>;<base64 string>;"`입니다. 이 문자열은 클라이언트에 표시되지 않으며, 수정하거나 해석하지 않고 그대로 사용해야 합니다.

```csharp
// Notice that document client is created against .NET SDK end-point rather than Gremlin.
DocumentClient client = new DocumentClient(
  new Uri("https://contoso.documents.azure.com:443/"), 
  "<master key>", 
  new ConnectionPolicy 
  {
    EnableEndpointDiscovery = false, 
    ConnectionMode = ConnectionMode.Direct 
  });

  // Read specific permission to obtain a token.
  // Token will not be returned during ReadPermissionReedAsync() call.
  // This call will succeed only if database id, user id and permission id already exist. 
  // Note that <database id> is not a database name, it is a base64 string that represents database identifier, for example "KalVAA==".
  // Similar comment applies to <user id> and <permission id>
  Permission permission = await client.ReadPermissionAsync(UriFactory.CreatePermissionUri("<database id>", "<user id>", "<permission id>"));

  Console.WriteLine("Obtained token {0}", permission.Token);
}
```

## <a name="use-a-resource-token"></a>리소스 토큰 사용
`GremlinServer` 클래스를 생성할 때 리소스 토큰을 “password” 속성으로 직접 사용할 수 있습니다.

```csharp
// Gremlin application needs to be given a resource token. It can't discover the token on its own.
// Token can be obtained for a given permission using Cosmos DB SDK or passed into the application as command line argument or configuration value.
string resourceToken = GetResourceToken();

// Configure gremlin servier to use resource token rather than master key
GremlinServer server = new GremlinServer(
  "contoso.gremlin.cosmosdb.azure.com",
  port: 443,
  enableSsl: true,
  username: "/dbs/<database name>/colls/<collection name>",

  // Format of the token is "type=resource&ver=1&sig=<base64 string>;<base64 string>;"
  password: resourceToken);

  using (GremlinClient gremlinClient = new GremlinClient(server, new GraphSON2Reader(), new GraphSON2Writer(), GremlinClient.GraphSON2MimeType))
  {
      await gremlinClient.SubmitAsync("g.V().limit(1)");
  }
```

동일한 접근 방식이 모든 TinkerPop Gremlin SDK에 적용됩니다.

```java
Cluster.Builder builder = Cluster.build();

AuthProperties authenticationProperties = new AuthProperties();
authenticationProperties.with(AuthProperties.Property.USERNAME,
    String.format("/dbs/%s/colls/%s", "<database name>", "<collection name>"));

// Format of the token is "type=resource&ver=1&sig=<base64 string>;<base64 string>;"
authenticationProperties.with(AuthProperties.Property.PASSWORD, resourceToken);

builder.authProperties(authenticationProperties);
```

## <a name="limit"></a>제한

단일 Gremlin 계정으로 개수 제한 없이 토큰을 발급할 수 있지만, **1시간** 이내에 최대 **100**개의 토큰만 동시에 사용할 수 있습니다. 애플리케이션이 시간당 토큰 한도를 초과하면 `"Exceeded allowed resource token limit of 100 that can be used concurrently"`라는 오류 메시지가 표시되고 인증 요청이 거부됩니다. 특정 토큰을 사용한 활성 연결을 닫아 새 토큰에 사용할 슬롯을 확보할 수 없습니다. Cosmos DB Gremlin 데이터베이스 엔진은 인증 요청 이전 1시간 동안의 고유 토큰을 추적합니다.

## <a name="permission"></a>사용 권한

애플리케이션에서 리소스 토큰을 사용하는 동안 발생하는 일반적인 오류는 `"Insufficient permissions provided in the authorization header for the corresponding request. Please retry with another authorization header."`입니다. 이 오류는 Gremlin 통과에서 모서리 또는 꼭짓점을 쓰려고 하는데 리소스 토큰이 `Read` 권한만 부여하는 경우에 반환됩니다. 통과를 검사하여 `.addV()`, `.addE()`, `.drop()` 또는 `.property()` 단계 중 하나를 포함하는지 확인합니다.

## <a name="next-steps"></a>다음 단계
* Azure Cosmos DB의 [역할 기반 액세스 제어](role-based-access-control.md)
* Azure Cosmos DB에서 [데이터 액세스를 보호하는 방법 알아보기](secure-access-to-data.md)
