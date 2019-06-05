---
title: Azure Cosmos DB에서 일관성을 관리하는 방법 알아보기
description: Azure Cosmos DB에서 일관성을 관리하는 방법 알아보기
author: rimman
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/23/2019
ms.author: rimman
ms.openlocfilehash: 68a1a757b5c5e4ce63d7f12a8502d57942d4ec42
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66240910"
---
# <a name="manage-consistency-levels-in-azure-cosmos-db"></a>Azure Cosmos DB의 일관성 수준 관리

이 문서에서는 Azure Cosmos DB에서 일관성 수준을 관리하는 방법에 대해 설명합니다. 기본 일관성 수준을 구성하고, 기본 일관성을 재정의하며, 세션 토큰을 수동으로 관리하고, PBS(확률적 제한된 부실) 메트릭을 이해하는 방법을 알아봅니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="configure-the-default-consistency-level"></a>기본 일관성 수준 구성

[기본 일관성 수준](consistency-levels.md)은 클라이언트에서 기본적으로 사용하는 일관성 수준입니다. 클라이언트는 언제든지 기본 일관성 수준을 재정의할 수 있습니다.

### <a name="cli"></a>CLI

```bash
# create with a default consistency
az cosmosdb create --name <name of Cosmos DB Account> --resource-group <resource group name> --default-consistency-level Strong

# update an existing account's default consistency
az cosmosdb update --name <name of Cosmos DB Account> --resource-group <resource group name> --default-consistency-level BoundedStaleness
```

### <a name="powershell"></a>PowerShell

이 예제에서는 미국 동부 및 미국 서부 지역에 다중 쓰기 지역을 사용하도록 설정된 새 Azure Cosmos 계정을 만듭니다. 기본 일관성 수준은 *세션* 일관성으로 설정됩니다.

```azurepowershell-interactive
$locations = @(@{"locationName"="East US"; "failoverPriority"=0},
             @{"locationName"="West US"; "failoverPriority"=1})

$iprangefilter = ""

$consistencyPolicy = @{"defaultConsistencyLevel"="Session"}

$CosmosDBProperties = @{"databaseAccountOfferType"="Standard";
                        "locations"=$locations;
                        "consistencyPolicy"=$consistencyPolicy;
                        "ipRangeFilter"=$iprangefilter;
                        "enableMultipleWriteLocations"="true"}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
  -ApiVersion "2015-04-08" `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -Name "myCosmosDbAccount" `
  -Properties $CosmosDBProperties
```

### <a name="azure-portal"></a>Azure portal

기본 일관성 수준을 보거나 수정하려면 Azure Portal에 로그인합니다. Azure Cosmos 계정을 찾아서 **기본 일관성** 창을 엽니다. 새 기본값으로 사용하려는 일관성 수준을 선택한 다음, **저장**을 선택합니다. 또한 Azure Portal은 음악 메모와 함께 다양한 일관성 수준의 시각화를 제공합니다. 

![Azure Portal의 일관성 메뉴](./media/how-to-manage-consistency/consistency-settings.png)

## <a name="override-the-default-consistency-level"></a>기본 일관성 수준 재정의

클라이언트는 서비스에서 설정한 기본 일관성 수준을 재정의할 수 있습니다. 일관성 수준을 요청별로 설정할 수 있으며, 이렇게 하면 계정 수준에서 설정된 기본 일관성 수준이 재정의됩니다.

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

Azure Cosmos DB의 일관성 수준 중 하나는 *세션* 일관성입니다. 기본적으로 Cosmos 계정에 적용되는 기본 수준입니다. *세션* 일관성을 작업할 때 클라이언트는 설정된 일관성 수준을 유지하기 위해 각 읽기/쿼리 요청과 관련하여 내부적으로 세션 토큰을 사용합니다.

세션 토큰을 수동으로 관리하려면 응답에서 세션 토큰을 가져와서 요청별로 설정합니다. 세션 토큰을 수동으로 관리할 필요가 없으면 다음 샘플을 사용할 필요가 없습니다. SDK는 세션 토큰을 자동으로 추적합니다. 세션 토큰을 수동으로 설정하지 않으면 기본적으로 SDK에서 최신 세션 토큰을 사용합니다.

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

최종 일관성은 어떻게 최종인가요? 평균적인 사례의 경우 버전 기록 및 시간과 관련하여 부실 범위를 제공할 수 있습니다. [**PBS(확률적 제한된 부실)** ](https://pbs.cs.berkeley.edu/) 메트릭은 부실의 확률을 수량화하여 메트릭으로 표시하려고 시도합니다. PBS 메트릭을 보려면 Azure Portal에서 Cosmos 계정으로 이동합니다. **메트릭** 창을 열고 **일관성** 탭을 선택합니다. **강력하게 일관된 워크로드 기반 읽기 확률(PBS 참조)** 이라는 그래프를 살펴봅니다.

![Azure Portal의 PBS 그래프](./media/how-to-manage-consistency/pbs-metric.png)


## <a name="next-steps"></a>다음 단계

데이터 충돌을 관리하는 방법을 자세히 알아보거나 Azure Cosmos DB의 다음 핵심 개념으로 이동합니다. 다음 문서를 참조하세요.

* [Azure Cosmos DB의 일관성 수준](consistency-levels.md)
* [Azure 지역 간 충돌 관리](how-to-manage-conflicts.md)
* [분할 및 데이터 배포](partition-data.md)
* [최신 분산 데이터베이스 시스템 디자인의 일관성 절충](https://www.computer.org/csdl/magazine/co/2012/02/mco2012020037/13rRUxjyX7k)
* [고가용성](high-availability.md)
* [Azure Cosmos DB SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
