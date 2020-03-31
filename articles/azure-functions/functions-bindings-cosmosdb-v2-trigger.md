---
title: 기능에 대 한 Azure 코스모스 DB 트리거 2.x
description: Azure 함수에서 Azure 코스모스 DB 트리거를 사용하는 방법을 알아봅니다.
author: craigshoemaker
ms.topic: reference
ms.date: 02/24/2020
ms.author: cshoe
ms.openlocfilehash: de8ad39ef731af3dc272d700eeee346acda64b53
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277571"
---
# <a name="azure-cosmos-db-trigger-for-azure-functions-2x"></a>Azure 코스모스 DB Azure 함수 2.x에 대한 트리거

Azure Cosmos DB 트리거는 [Azure Cosmos DB 변경 피드](../cosmos-db/change-feed.md)를 사용하여 파티션에 대한 삽입 및 업데이트를 수신 대기합니다. 변경 피드는 삽입과 업데이트를 게시하지만 삭제는 게시하지 않습니다.

설정 및 구성 세부 정보에 대한 자세한 내용은 [개요를](./functions-bindings-cosmosdb-v2.md)참조하십시오.

<a id="example" name="example"></a>

# <a name="c"></a>[C #](#tab/csharp)

다음 예제에서는 지정한 데이터베이스 및 컬렉션에서 삽입 또는 업데이트가 있을 때 호출되는 [C# 함수](functions-dotnet-class-library.md)를 보여줍니다.

```cs
using Microsoft.Azure.Documents;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class CosmosTrigger
    {
        [FunctionName("CosmosTrigger")]
        public static void Run([CosmosDBTrigger(
            databaseName: "ToDoItems",
            collectionName: "Items",
            ConnectionStringSetting = "CosmosDBConnection",
            LeaseCollectionName = "leases",
            CreateLeaseCollectionIfNotExists = true)]IReadOnlyList<Document> documents,
            ILogger log)
        {
            if (documents != null && documents.Count > 0)
            {
                log.LogInformation($"Documents modified: {documents.Count}");
                log.LogInformation($"First document Id: {documents[0].Id}");
            }
        }
    }
}
```

# <a name="c-script"></a>[C# 스크립트](#tab/csharp-script)

다음 예에서는 *function.json* 파일의 Cosmos DB 트리거 바인딩 및 바인딩을 사용하는 [C# 스크립트 함수](functions-reference-csharp.md)를 보여줍니다. 이 함수는 Cosmos DB 레코드가 추가되거나 수정될 때 로그 메시지를 씁니다.

*function.json* 파일의 바인딩 데이터는 다음과 같습니다.

```json
{
    "type": "cosmosDBTrigger",
    "name": "documents",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "<connection-app-setting>",
    "databaseName": "Tasks",
    "collectionName": "Items",
    "createLeaseCollectionIfNotExists": true
}
```

C# 스크립트 코드는 다음과 같습니다.

```cs
    #r "Microsoft.Azure.DocumentDB.Core"

    using System;
    using Microsoft.Azure.Documents;
    using System.Collections.Generic;
    using Microsoft.Extensions.Logging;

    public static void Run(IReadOnlyList<Document> documents, ILogger log)
    {
      log.LogInformation("Documents modified " + documents.Count);
      log.LogInformation("First document Id " + documents[0].Id);
    }
```

# <a name="javascript"></a>[자바 스크립트](#tab/javascript)

다음 예에서는 *function.json* 파일의 Cosmos DB 트리거 바인딩 및 바인딩을 사용하는 [JavaScript 함수](functions-reference-node.md)를 보여줍니다. 이 함수는 Cosmos DB 레코드가 추가되거나 수정될 때 로그 메시지를 씁니다.

*function.json* 파일의 바인딩 데이터는 다음과 같습니다.

```json
{
    "type": "cosmosDBTrigger",
    "name": "documents",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "<connection-app-setting>",
    "databaseName": "Tasks",
    "collectionName": "Items",
    "createLeaseCollectionIfNotExists": true
}
```

JavaScript 코드는 다음과 같습니다.

```javascript
    module.exports = function (context, documents) {
      context.log('First document Id modified : ', documents[0].id);

      context.done();
    }
```

# <a name="python"></a>[Python](#tab/python)

다음 예제에서는 *function.json* 파일의 Cosmos DB 트리거 바인딩 및 바인딩을 사용하는 [Python 함수](functions-reference-python.md)를 보여 줍니다. 이 함수는 Cosmos DB 레코드가 수정될 때 로그 메시지를 작성합니다.

*function.json* 파일의 바인딩 데이터는 다음과 같습니다.

```json
{
    "name": "documents",
    "type": "cosmosDBTrigger",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "<connection-app-setting>",
    "databaseName": "Tasks",
    "collectionName": "Items",
    "createLeaseCollectionIfNotExists": true
}
```

다음은 Python 코드입니다.

```python
    import logging
    import azure.functions as func


    def main(documents: func.DocumentList) -> str:
        if documents:
            logging.info('First document Id modified: %s', documents[0]['id'])
```

# <a name="java"></a>[Java](#tab/java)

지정된 데이터베이스 및 컬렉션에 삽입 또는 업데이트가 있을 때 이 함수가 호출됩니다.

```java
    @FunctionName("cosmosDBMonitor")
    public void cosmosDbProcessor(
        @CosmosDBTrigger(name = "items",
            databaseName = "ToDoList",
            collectionName = "Items",
            leaseCollectionName = "leases",
            createLeaseCollectionIfNotExists = true,
            connectionStringSetting = "AzureCosmosDBConnection") String[] items,
            final ExecutionContext context ) {
                context.getLogger().info(items.length + "item(s) is/are changed.");
            }
```


[Java 함수 런타임 라이브러리](/java/api/overview/azure/functions/runtime)에서 값이 Cosmos DB에서 제공되는 매개 변수에 대한 `@CosmosDBTrigger` 주석을 사용합니다.  `Optional<T>`을 사용하여 원시 Java 형식, POJO 또는 null 허용 값으로 이 주석을 사용할 수 있습니다.

---

## <a name="attributes-and-annotations"></a>특성 및 주석

# <a name="c"></a>[C #](#tab/csharp)

[C# 클래스 라이브러리](functions-dotnet-class-library.md)에서 [CosmosDBTrigger](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/Trigger/CosmosDBTriggerAttribute.cs) 특성을 사용합니다.

특성의 생성자는 데이터베이스 이름과 컬렉션 이름을 사용합니다. 이러한 설정 및 구성할 수 있는 다른 속성에 대한 자세한 내용은 [트리거 - 구성](#configuration)을 참조하세요. 다음은 메서드 서명의 `CosmosDBTrigger` 특성 예제입니다.

```csharp
    [FunctionName("DocumentUpdates")]
    public static void Run(
        [CosmosDBTrigger("database", "collection", ConnectionStringSetting = "myCosmosDB")]
    IReadOnlyList<Document> documents,
        ILogger log)
    {
        ...
    }
```

전체 예제는 [트리거](#example)를 참조하십시오.

# <a name="c-script"></a>[C# 스크립트](#tab/csharp-script)

특성은 C# 스크립트에서 지원되지 않습니다.

# <a name="javascript"></a>[자바 스크립트](#tab/javascript)

속성은 자바 스크립트에서 지원되지 않습니다.

# <a name="python"></a>[Python](#tab/python)

특성은 파이썬에서 지원되지 않습니다.

# <a name="java"></a>[Java](#tab/java)

Java [함수 런타임 라이브러리에서](https://docs.microsoft.com/java/api/overview/azure/functions/runtime)Cosmos DB에서 `@CosmosDBInput` 데이터를 읽는 매개 변수에 대한 추가를 사용합니다.

---

## <a name="configuration"></a>Configuration

다음 표에서는 *function.json* 파일및 특성에서 설정한 바인딩 `CosmosDBTrigger` 구성 속성에 대해 설명합니다.

|function.json 속성 | 특성 속성 |설명|
|---------|---------|----------------------|
|**종류** | 해당 없음 | `cosmosDBTrigger`로 설정해야 합니다. |
|**direction** | 해당 없음 | `in`로 설정해야 합니다. 이 매개 변수는 사용자가 Azure Portal에서 트리거를 만들 때 자동으로 설정됩니다. |
|**(이름)** | 해당 없음 | 변경 사항이 포함된 문서 목록을 나타내는 함수 코드에 사용되는 변수 이름. |
|**연결문자열 설정**|**ConnectionStringSetting** | 모니터링되는 Azure Cosmos DB 계정에 연결하는 데 사용되는 연결 문자열을 포함하고 있는 앱 설정의 이름입니다. |
|**databaseName**|**Databasename**  | 컬렉션이 모니터링되는 Azure Cosmos DB 데이터베이스의 이름입니다. |
|**컬렉션이름** |**CollectionName** | 모니터링되는 컬렉션의 이름입니다. |
|**임대연결문자열 설정** | **LeaseConnectionStringSetting** | (선택 사항) 임대 컬렉션을 보유 하는 Azure Cosmos DB 계정에 연결 문자열을 포함 하는 응용 프로그램 설정의 이름입니다. 설정하지 않으면 `connectionStringSetting` 값이 사용됩니다. 이 매개 변수는 포털에서 바인딩이 생성될 때 자동으로 설정됩니다. 임대 컬렉션에 대한 연결 문자열에 쓰기 권한이 있어야 합니다.|
|**임대데이터베이스 이름** |**LeaseDatabaseName** | (선택 사항) 임대를 저장하는 데 사용되는 컬렉션을 보유하는 데이터베이스의 이름입니다. 설정하지 않으면 `databaseName` 설정 값이 사용됩니다. 이 매개 변수는 포털에서 바인딩이 생성될 때 자동으로 설정됩니다. |
|**임대컬렉션 이름** | **LeaseCollectionName** | (선택 사항) 임대를 저장하는 데 사용되는 컬렉션의 이름입니다. 설정하지 않으면 `leases` 값이 사용됩니다. |
|**createLease컬렉션If존재하지 않음** | **CreateLeaseCollectionIfNotExists** | (선택 사항) `true`로 설정하면 임대 컬렉션이 없는 경우 자동으로 임대 컬렉션이 생성됩니다. 기본값은 `false`입니다. |
|**leasesCollectionThroughput**| **리스컬렉션관통**| (선택 사항) 임대 컬렉션을 만들 때 할당할 요청 단위 수를 정의합니다. 이 설정은 로 `createLeaseCollectionIfNotExists` 설정된 `true`경우에만 사용됩니다. 이 매개 변수는 포털을 사용하여 바인딩이 생성될 때 자동으로 설정됩니다.
|**임대컬렉션사전수정**| **LeaseCollectionPrefix**| (선택 사항) 설정하면 이 함수에 대한 임대 컬렉션에서 생성된 임대에 접두사로 값이 추가됩니다. 접두사를 사용하면 두 개의 서로 다른 접두사를 사용하여 두 개의 서로 다른 Azure 함수가 동일한 임대 컬렉션을 공유할 수 있습니다.
|**피드폴딜레이**| **FeedPollDelay**| (선택 사항) 모든 현재 변경 내용이 드레인된 후 피드의 새 변경 내용을 위해 파티션을 폴링하는 사이의 지연 시간(밀리초)입니다. 기본값은 5,000밀리초 또는 5초입니다.
|**임대취득간격**| **LeaseAcquireInterval**| (선택 사항) 설정하면 파티션이 알려진 호스트 인스턴스 간에 균등하게 배포되는지를 계산하는 태스크를 시작하는 간격을 밀리초로 정의합니다. 기본값은 13000(13초)입니다.
|**리스 익스피어레이션간격**| **LeaseExpirationInterval**| (선택 사항) 설정하면 파티션을 나타내는 임대에 대한 임대 기간인 간격을 밀리초로 정의합니다. 이 간격 내에서 임대를 갱신하지 않으면 기간이 만료되어 다른 인스턴스로 파티션 소유권이 이동합니다. 기본값은 60000(60초)입니다.
|**임대갱신간격**| **LeaseRenewInterval**| (선택 사항) 설정하면 인스턴스가 현재 보유한 파티션의 모든 임대에 대한 갱신 간격을 밀리초로 정의합니다. 기본값은 17000(17초)입니다.
|**체크포인트 주파수**| **CheckpointFrequency**| (선택 사항) 설정하면 임대 검사점 간격을 밀리초로 정의합니다. 기본값은 항상 각 함수 호출 이후입니다.
|**maxItemsPerInvocation**| **맥스아이템스퍼호출**| (선택 사항) 설정하면 이 속성은 Function 호출당 수신된 항목의 최대 수를 설정합니다. 모니터링되는 컬렉션의 작업이 저장 프로시저를 통해 수행되는 경우 변경 피드에서 항목을 읽을 때 [트랜잭션 범위가](../cosmos-db/stored-procedures-triggers-udfs.md#transactions) 유지됩니다. 따라서 동일한 트랜잭션에 의해 변경된 항목이 하나의 원자 배치의 일부로 반환되도록 수신된 항목 수가 지정된 값보다 높을 수 있습니다.
|**시작 시작부터**| **StartFromBeginning**| (선택 사항) 이 옵션은 현재 시간에서 시작하는 대신 컬렉션의 변경 기록의 시작 부분에서 변경 내용을 읽도록 트리거에 알려줍니다. 처음부터 읽기는 트리거가 처음 시작될 때만 작동하며, 후속 실행에서와 같이 검사점이 이미 저장되어 있습니다. 이 옵션을 `true` 이미 생성된 임대가 있는 경우로 설정하면 효과가 없습니다. |
|**기본 설정위치**| **기본 설정 위치**| (선택 사항) Azure Cosmos DB 서비스에서 지리적으로 복제된 데이터베이스 계정에 대한 기본 위치(영역)를 정의합니다. 값은 쉼표로 구분되어야 합니다. 예를 들어 "미국 동부, 미국 중남부, 북유럽". |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>사용

트리거에는 파티션에 _임대_를 저장하는 데 사용할 보조 컬렉션이 필요합니다. 트리거가 작동할 수 있도록 모니터링되는 컬렉션과 임대를 포함하고 있는 컬렉션을 모두 사용할 수 있어야 합니다.

>[!IMPORTANT]
> 동일한 컬렉션에 Cosmos DB 트리거를 사용하도록 여러 함수가 구성된 경우 각 함수는 전용 임대 컬렉션을 사용하거나 각 함수에 다른 `LeaseCollectionPrefix`를 지정해야 합니다. 그러지 않으면 함수 중 하나만 트리거됩니다. 접두사에 대한 정보는 [구성 섹션](#configuration)을 참조하세요.

트리거는 문서가 업데이트 또는 삽입되었는지 여부를 나타내지 않고 문서 자체만 제공합니다. 업데이트 및 삽입을 다르게 처리해야 하는 경우 삽입 또는 업데이트에 대한 타임스탬프 필드를 구현하면 됩니다.

## <a name="next-steps"></a>다음 단계

- [Azure 코스모스 DB 문서 읽기(입력 바인딩)](./functions-bindings-cosmosdb-v2-input.md)
- [Azure Cosmos DB 문서에 변경 내용 저장(출력 바인딩)](./functions-bindings-cosmosdb-v2-output.md)