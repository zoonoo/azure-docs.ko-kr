---
title: 큰 파티션 키가 있는 Azure Cosmos 컨테이너 만들기
description: Azure 포털 및 다른 SDK를 사용하여 큰 파티션 키가 있는 Azure Cosmos DB에서 컨테이너를 만드는 방법에 대해 알아봅니다.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/28/2019
ms.author: mjbrown
ms.openlocfilehash: 7184a6b85e93c41dfe914813301a4b1a0c88f2cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75887685"
---
# <a name="create-containers-with-large-partition-key"></a>큰 파티션 키가 있는 컨테이너 만들기

Azure Cosmos DB는 해시 기반 분할 체계를 사용하여 데이터의 수평 크기 조정을 달성합니다. 2019년 5월 3일 이전에 생성된 모든 Azure Cosmos 컨테이너는 파티션 키의 처음 100바이트를 기반으로 해시를 계산하는 해시 함수를 사용합니다. 처음 100바이트가 동일한 파티션 키가 여러 개 있는 경우 이러한 논리 파티션은 서비스에서 동일한 논리 파티션으로 간주됩니다. 이로 인해 파티션 크기 할당량이 올바르지 않고 파티션 키에 고유한 인덱스가 적용되는 등의 문제가 발생할 수 있습니다. 이 문제를 해결하기 위해 큰 파티션 키가 도입되었습니다. Azure Cosmos DB는 이제 최대 2KB의 값으로 큰 파티션 키를 지원합니다.

큰 파티션 키는 최대 2KB의 큰 파티션 키에서 고유한 해시를 생성할 수 있는 해시 함수의 향상된 버전의 기능을 사용하여 지원됩니다. 이 해시 버전은 파티션 키의 크기에 관계없이 파티션 키 카디널리티가 높은 시나리오에도 권장됩니다. 파티션 키 카디널리티는 컨테이너에서 ~30000 논리 파티션의 순서와 같은 고유 논리 파티션의 수로 정의됩니다. 이 문서에서는 Azure 포털 및 다른 SDK를 사용하여 큰 파티션 키가 있는 컨테이너를 만드는 방법에 대해 설명합니다.

## <a name="create-a-large-partition-key-azure-portal"></a>큰 파티션 키 만들기(Azure 포털)

큰 파티션 키를 만들려면 Azure 포털을 사용하여 새 컨테이너를 만들 때 **내 파티션 키가 100바이트** 옵션을 초과하고 있는지 확인합니다. 큰 파티션 키가 필요하지 않거나 1.18보다 오래된 SDK 버전에서 실행 중인 응용 프로그램이 있는 경우 확인란을 선택 취소합니다.

![Azure 포털을 사용하여 큰 파티션 키 만들기](./media/large-partition-keys/large-partition-key-with-portal.png)

## <a name="create-a-large-partition-key-powershell"></a>큰 파티션 키 만들기(PowerShell)

큰 파티션 키 지원이 있는 컨테이너를 만들려면

* [파티션 키 크기가 큰 Azure Cosmos 컨테이너 만들기](manage-with-powershell.md#create-container-big-pk)

## <a name="create-a-large-partition-key-net-sdk"></a>큰 파티션 키 만들기(.Net SDK)

.NET SDK를 사용하여 큰 파티션 키가 있는 `PartitionKeyDefinitionVersion.V2` 컨테이너를 만들려면 속성을 지정합니다. 다음 예제에서는 PartitionKeyDefinition 개체 내에서 버전 속성을 지정 하 고 PartitionKeyDefinitionVersion.V2로 설정 하는 방법을 보여 주실 수 있습니다.

### <a name="v3-net-sdk"></a>v3 .NET SDK

```csharp
await database.CreateContainerAsync(
    new ContainerProperties(collectionName, $"/longpartitionkey")
    {
        PartitionKeyDefinitionVersion = PartitionKeyDefinitionVersion.V2,
    })
```

### <a name="v2-net-sdk"></a>v2 .NET SDK

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

## <a name="supported-sdk-versions"></a>지원되는 SDK 버전

큰 파티션 키는 다음과 같은 최소 버전의 SDK에서 지원됩니다.

|SDK 형식  | 최소 버전   |
|---------|---------|
|.Net     |    1.18     |
|자바 동기화     |   2.4.0      |
|Java 비동기   |  2.5.0        |
| REST API | 요청 헤더를 사용하는 것보다 `2017-05-03` 높은 버전입니다. `x-ms-version`|
| Resource Manager 템플릿 | 버전 2는 `"version":2` `partitionKey` 개체 내의 속성을 사용하여 |

현재 Power BI 및 Azure 논리 앱내에서 큰 파티션 키가 있는 컨테이너를 사용할 수 없습니다. 이러한 응용 프로그램에서 큰 파티션 키 없이 컨테이너를 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [Azure Cosmos DB에서 분할](partitioning-overview.md)
* [Azure 코스모스 DB의 요청 단위](request-units.md)
* [컨테이너 및 데이터베이스의 처리량 프로비전](set-throughput.md)
* [Azure Cosmos 계정 작업](account-overview.md)
