---
title: Azure Cosmos DB에서 일관성을 관리하는 방법 알아보기
description: Azure Cosmos DB에서 일관성을 관리하는 방법 알아보기
services: cosmos-db
author: christopheranderson
ms.service: cosmos-db
ms.topic: sample
ms.date: 10/17/2018
ms.author: chrande
ms.openlocfilehash: be2c68922221af848c9e484d03527d02808c071a
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2018
ms.locfileid: "51283821"
---
# <a name="manage-consistency-levels-in-azure-cosmos-db"></a>Azure Cosmos DB의 일관성 수준 관리

이 문서에서는 기본 일관성을 설정하고, 클라이언트에서 해당 일관성을 재정의하고, 수동으로 세션 토큰을 관리하고, PBS(확률적 제한된 부실) 메트릭을 이해하는 방법을 설명합니다.

## <a name="configure-the-default-consistency-level"></a>기본 일관성 수준 구성

기본 일관성 수준은 클라이언트에서 기본적으로 사용하는 일관성 수준입니다. 클라이언트가 기본 일관성 수준을 재정의할 수 있습니다.

### <a name="cli"></a>CLI

```bash
# create with a default consistency
az cosmosdb create --name <name of Cosmos DB Account> --resource-group <resource group name> --default-consistency-level Strong

# update an existing account's default consistency
az cosmosdb update --name <name of Cosmos DB Account> --resource-group <resource group name> --default-consistency-level BoundedStaleness
```

### <a name="powershell"></a>PowerShell

아래 예제는 미국 동부 및 미국 서부 지역에서 다중 마스터를 사용하고, 기본 일관성 정책을 제한된 부실로 설정하고, 최대 부실 간격이 10초이고, 허용되는 최대 부실 요청 수가 200인 새 Cosmos DB 계정을 만듭니다.

```azurepowershell-interactive
$locations = @(@{"locationName"="East US"; "failoverPriority"=0},
             @{"locationName"="West US"; "failoverPriority"=1})

$iprangefilter = ""

$consistencyPolicy = @{"defaultConsistencyLevel"="BoundedStaleness";
                       "maxIntervalInSeconds"= "10";
                       "maxStalenessPrefix"="200"}

$CosmosDBProperties = @{"databaseAccountOfferType"="Standard";
                        "locations"=$locations;
                        "consistencyPolicy"=$consistencyPolicy;
                        "ipRangeFilter"=$iprangefilter;
                        "enableMultipleWriteLocations"="true"}

New-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
  -ApiVersion "2015-04-08" `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -Name "myCosmosDbAccount" `
  -Properties $CosmosDBProperties
```

### <a name="portal"></a>포털

기본 일관성 수준을 확인 또는 수정하려면 Azure Portal에 로그인합니다. Cosmos DB 계정을 찾아서 **기본 일관성** 창을 엽니다. 여기서 새 기본값으로 사용할 일관성 수준을 선택하고 [저장]을 클릭합니다.

![Azure Portal의 일관성 메뉴 그림](./media/how-to-manage-consistency/consistency-settings.png)

## <a name="override-the-default-consistency-level"></a>기본 일관성 수준 재정의

클라이언트는 서비스에서 설정한 기본 일관성 수준을 재정의할 수 있습니다. 이 작업을 전체 클라이언트에 대해 수행할 수도 있고, 요청 단위로 수행할 수도 있습니다.

### <a id="override-default-consistency-dotnet"></a>.NET SDK

```csharp
// Override consistency at the client level
ConsistencyPolicy consistencyPolicy = new ConsistencyPolicy
    {
        DefaultConsistencyLevel = ConsistencyLevel.BoundedStaleness,
        MaxStalenessIntervalInSeconds = 5,
        MaxStalenessPrefix = 100
    };
documentClient = new DocumentClient(new Uri(endpoint), authKey, connectionPolicy, consistencyPolicy);

// Override consistency at the request level via request options
RequestOptions requestOptions = new RequestOptions { ConsistencyLevel = ConsistencyLevel.Strong };

var response = await client.CreateDocumentAsync(collectionUri, document, requestOptions);
```

### <a id="override-default-consistency-java-async"></a>Java 비동기 SDK

```java
// Override consistency at the client level
ConnectionPolicy policy = new ConnectionPolicy();

AsyncDocumentClient client =
        new AsyncDocumentClient.Builder()
                .withMasterKey(this.accountKey)
                .withServiceEndpoint(this.accountEndpoint)
                .withConsistencyLevel(ConsistencyLevel.Eventual)
                .withConnectionPolicy(policy).build();
```

### <a id="override-default-consistency-java-sync"></a>Java 동기 SDK

```java
// Override consistency at the client level
ConnectionPolicy connectionPolicy = new ConnectionPolicy();
DocumentClient client = new DocumentClient(accountEndpoint, accountKey, connectionPolicy, ConsistencyLevel.Strong);
```

### <a id="override-default-consistency-javascript"></a>Node.js/JavaScript/TypeScript SDK

```javascript
// Override consistency at the client level
const client = new CosmosClient({
  /* other config... */
  consistencyLevel: ConsistencyLevel.Strong
});

// Override consistency at the request level via request options
const { body } = await item.read({ consistencyLevel: ConsistencyLevel.Eventual });
```

### <a id="override-default-consistency-python"></a>Python SDK

```python
# Override consistency at the client level
connection_policy = documents.ConnectionPolicy()
client = cosmos_client.CosmosClient(self.account_endpoint, {'masterKey': self.account_key}, connection_policy, documents.ConsistencyLevel.Strong)
```

## <a name="utilize-session-tokens"></a>세션 토큰 사용

세션 토큰을 수동으로 관리하려는 경우 응답에서 세션 토큰을 가져와서 요청에 따라 설정하면 됩니다. 세션 토큰을 수동으로 관리할 필요가 없으면 아래 샘플을 사용할 필요가 없습니다. 세션 토큰을 직접 설정하지 않으면 SDK가 자동으로 세션 토큰을 추적하여 최신 세션 토큰을 사용합니다.

### <a id="utilize-session-tokens-dotnet"></a>.NET SDK

```csharp
var response = await client.ReadDocumentAsync(
                UriFactory.CreateDocumentUri(databaseName, collectionName, "SalesOrder1"));
string sessionToken = response.SessionToken;

RequestOptions options = new RequestOptions();
options.SessionToken = sessionToken;
var response = await client.ReadDocumentAsync(
                UriFactory.CreateDocumentUri(databaseName, collectionName, "SalesOrder1"), options);
```

### <a id="utilize-session-tokens-java-async"></a>Java 비동기 SDK

```java
// Get session token from response
RequestOptions options = new RequestOptions();
options.setPartitionKey(new PartitionKey(document.get("mypk")));
Observable<ResourceResponse<Document>> readObservable = client.readDocument(document.getSelfLink(), options);
readObservable.single()           // we know there will be one response
  .subscribe(
      documentResourceResponse -> {
          System.out.println(documentResourceResponse.getSessionToken());
      },
      error -> {
          System.err.println("an error happened: " + error.getMessage());
      });

// Resume the session by setting the session token on RequestOptions
RequestOptions options = new RequestOptions();
requestOptions.setSessionToken(sessionToken);
Observable<ResourceResponse<Document>> readObservable = client.readDocument(document.getSelfLink(), options);
```

### <a id="utilize-session-tokens-java-sync"></a>Java 동기 SDK

```java
// Get session token from response
ResourceResponse<Document> response = client.readDocument(documentLink, null);
String sessionToken = response.getSessionToken();

// Resume the session by setting the session token on the RequestOptions
RequestOptions options = new RequestOptions();
options.setSessionToken(sessionToken);
ResourceResponse<Document> response = client.readDocument(documentLink, options);
```

### <a id="utilize-session-tokens-javascript"></a>Node.js/JavaScript/TypeScript SDK

```javascript
// Get session token from response
const { headers, item } = await container.items.create({ id: "meaningful-id" });
const sessionToken = headers["x-ms-session-token"];

// Immediately or later, you can use that sessionToken from the header to resume that session.
const { body } = await item.read({ sessionToken });
```

### <a id="utilize-session-tokens-python"></a>Python SDK

```python
// Get the session token from the last response headers
item = client.ReadItem(item_link)
session_token = client.last_response_headers["x-ms-session-token"]

// Resume the session by setting the session token on the options for the request
options = {
    "sessionToken": session_token
}
item = client.ReadItem(doc_link, options)
```

## <a name="monitor-probabilistically-bounded-staleness-pbs-metric"></a>PBS(확률적 제한된 부실) 메트릭 모니터링

PBS 메트릭을 보려면 Azure Portal에서 Cosmos DB 계정으로 이동하여 **메트릭** 창을 엽니다. 이 창에서 **일관성** 탭을 클릭하고 "**워크로드를 기반으로 하는 일관된 읽기가 발생할 가능성(PBS 참조)**"이라는 그래프를 살펴봅니다.

![Azure Portal의 PBS 그래프 그림](./media/how-to-manage-consistency/pbs-metric.png)

이 메트릭을 보려면 Cosmos DB 메트릭 메뉴를 사용합니다. Azure 모니터링 메트릭 환경에는 나타나지 않습니다.

## <a name="next-steps"></a>다음 단계

다음 문서를 사용하여 데이터 충돌 관리에 대해 좀 더 자세히 알아보거나 Cosmos DB의 그 다음 핵심 개념으로 이동할 수 있습니다.

* [Azure 지역 간 충돌을 관리하는 방법](how-to-manage-conflicts.md)
* [분할 및 데이터 배포](partition-data.md)
