---
title: 큰 파티션 키를 사용하는 Azure Cosmos 컨테이너 만들기
description: Azure Portal 및 다양한 SDK를 사용하여 큰 파티션 키를 사용하는 Azure Cosmos DB에서 컨테이너를 만드는 방법을 알아봅니다.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 09/28/2019
ms.author: mjbrown
ms.custom: devx-track-csharp
ms.openlocfilehash: 4ad26d63ca06f5a46a4a1f77d329d04896e96c52
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93339301"
---
# <a name="create-containers-with-large-partition-key"></a>큰 파티션 키를 사용하는 컨테이너 만들기
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB는 해시 기반 분할 구성표를 사용하여 데이터의 수평 확장을 구현합니다. 2019년 5월 3일 이전에 만든 모든 Azure Cosmos 컨테이너는 파티션 키의 처음 100바이트를 기반으로 해시를 계산하는 해시 함수를 사용합니다. 처음 100바이트와 동일한 파티션 키가 여러 개 있는 경우 해당 논리 파티션은 서비스에서 동일한 논리 파티션으로 간주됩니다. 이로 인해 파티션 크기 할당량이 잘못된 것과 같은 문제가 발생할 수 있으며 파티션 키 전체에서 고유 인덱스가 적용됩니다. 이 문제를 해결하기 위해 큰 파티션 키가 도입되었습니다. 이제 Azure Cosmos DB는 최대 2KB의 값이 있는 큰 파티션 키를 지원합니다.

큰 파티션 키는 최대 2KB의 큰 파티션 키에서 고유한 해시를 생성할 수 있는 향상된 버전의 해시 기능을 사용하여 지원됩니다. 이 해시 버전은 파티션 키의 크기에 관계없이 파티션 키 카디널리티가 높은 시나리오에도 권장됩니다. 파티션 키 카디널리티는 고유한 논리 파티션의 수로 정의됩니다(예: 컨테이너에서 30000개 이하의 논리 파티션 순서). 이 문서에서는 Azure Portal 및 다양한 SDK를 사용하여 큰 파티션 키를 사용하는 컨테이너를 만드는 방법을 설명합니다.

## <a name="create-a-large-partition-key-azure-portal"></a>큰 파티션 키 만들기(Azure Portal)

큰 파티션 키를 만들려면 Azure Portal을 사용하여 새 컨테이너를 만들 때 **내 파티션 키가 100바이트보다 큽니다** 옵션을 선택합니다. 큰 파티션 키가 필요하지 않거나 1.18 이전의 SDK 버전에서 실행되는 애플리케이션이 있는 경우 확인란을 선택 취소합니다.

:::image type="content" source="./media/large-partition-keys/large-partition-key-with-portal.png" alt-text="Azure Portal을 사용하여 큰 파티션 키 만들기":::

## <a name="create-a-large-partition-key-powershell"></a>큰 파티션 키 만들기(PowerShell)

큰 파티션 키를 지원하는 컨테이너를 만들려면 다음을 참조하세요.

* [큰 파티션 키 크기를 사용하는 Azure Cosmos 컨테이너 만들기](manage-with-powershell.md#create-container-big-pk)

## <a name="create-a-large-partition-key-net-sdk"></a>큰 파티션 키 만들기(.Net SDK)

.NET SDK를 사용하여 큰 파티션 키를 사용하는 컨테이너를 만들려면 `PartitionKeyDefinitionVersion.V2` 속성을 지정합니다. 다음 예에서는 PartitionKeyDefinition 개체 내에서 Version 속성을 지정하고 이를 PartitionKeyDefinitionVersion.V2로 설정하는 방법을 보여 줍니다.

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

```csharp
await database.CreateContainerAsync(
    new ContainerProperties(collectionName, $"/longpartitionkey")
    {
        PartitionKeyDefinitionVersion = PartitionKeyDefinitionVersion.V2,
    })
```

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

```csharp
DocumentCollection collection = await newClient.CreateDocumentCollectionAsync(
database,
     new DocumentCollection
        {
           Id = Guid.NewGuid().ToString(),
           PartitionKey = new PartitionKeyDefinition
           {
             Paths = new Collection<string> {"/longpartitionkey" },
             Version = PartitionKeyDefinitionVersion.V2
           }
         },
      new RequestOptions { OfferThroughput = 400 });
```
---

## <a name="supported-sdk-versions"></a>지원되는 SDK 버전

큰 파티션 키는 다음 SDK의 최소 버전에서 지원됩니다.

|SDK 형식  | 최소 버전   |
|---------|---------|
|.Net     |    1.18     |
|Java 동기     |   2.4.0      |
|Java 비동기   |  2.5.0        |
| REST API | `2017-05-03`보다 높은 버전 - `x-ms-version` 요청 헤더 사용|
| Resource Manager 템플릿 | 버전 2 - `partitionKey` 개체 내에서 `"version":2` 속성 사용 |

현재 Power BI 및 Azure Logic Apps 내에서 큰 파티션 키를 사용하는 컨테이너를 사용할 수 없습니다. 이러한 애플리케이션에서는 큰 파티션 키를 사용하지 않는 컨테이너를 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [Azure Cosmos DB에서 분할](partitioning-overview.md)
* [Azure Cosmos DB의 요청 단위](request-units.md)
* [컨테이너 및 데이터베이스의 처리량 프로비전](set-throughput.md)
* [Azure Cosmos 계정 작업](./account-databases-containers-items.md)