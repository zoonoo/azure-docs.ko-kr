---
title: Azure portal 및 다양 한 Sdk를 사용 하 여 큰 파티션 키를 사용 하 여 Azure Cosmos 컨테이너를 만듭니다.
description: Azure portal 및 다양 한 Sdk를 사용 하 여 큰 파티션 키를 사용 하 여 Azure Cosmos DB에서 컨테이너를 만드는 방법에 알아봅니다.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: mjbrown
ms.openlocfilehash: 33f871564b7c8435395db6b97122ba6a75800271
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66225988"
---
# <a name="create-containers-with-large-partition-key"></a>큰 파티션 키를 사용 하 여 컨테이너 만들기

Azure Cosmos DB는 데이터의 수평적 확장을 달성 하기 위해 해시 기반 분할 체계를 사용 합니다. 3 2019 년 5 월 이전에 만든 모든 Azure Cosmos 컨테이너는 파티션 키의 처음 100 바이트가 기반으로 해시를 계산 하는 해시 함수를 사용 합니다. 동일한 처음 100 바이트가 있는 여러 파티션 키가 없으면 다음 논리 파티션은으로 간주 됩니다 동일한 논리 파티션 서비스. 이 파티션 크기 할당량 잘못 되지 및 파티션 키 간에 적용 되는 고유 인덱스와 같은 문제가 발생할 수 있습니다. 이 문제를 해결 하기 위해 많은 파티션 키 도입 됩니다. Azure Cosmos DB에서 지 원하는 많은 파티션 키와 이제 데 최대 2KB 값입니다. 

큰 파티션의 고유 해시를 생성할 수 있는 해시 함수, 향상 된 버전의 기능을 사용 하 여 키가 지원 되는 큰 파티션 키는 데 최대 2KB입니다. 이 해시 버전은 파티션 키의 크기에 관계 없이 많은 파티션 키 카디널리티가 사용 하 여 시나리오에도 권장 됩니다. 파티션 키 카디널리티가 ~ 30000 논리 파티션 컨테이너에서 순으로 예를 들어의 고유 논리 파티션 수로 정의 됩니다. 이 문서에서는 Azure portal 및 다양 한 Sdk를 사용 하 여 큰 파티션 키를 사용 하 여 컨테이너를 만드는 방법을 설명 합니다. 

## <a name="create-a-large-partition-key-net-sdk-v2"></a>큰 파티션 키를 만듭니다 (.NET SDK V2)

.NET SDK를 사용 하 여 큰 파티션 키를 사용 하 여 컨테이너를 만듭니다를 하는 경우 지정 해야 합니다 `PartitionKeyDefinitionVersion.V2` 속성입니다. 다음 예제에서는 PartitionKeyDefinition 개체 내에서 버전 속성을 지정 하 고 PartitionKeyDefinitionVersion.V2로 설정 하는 방법을 보여 줍니다.

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

## <a name="create-a-large-partition-key-azure-portal"></a>큰 파티션 키 (Azure portal) 만들기 

Azure portal을 사용 하 여 새 컨테이너를 만들어야 하는 동안 많은 파티션 키를 만들려면 합니다 **내 파티션 키가 100 바이트 보다 큰** 옵션입니다. 기본적으로 모든 새 컨테이너는 큰 파티션 키를 사용 하 여 옵트인 합니다. 큰 파티션 키 필요가 없는 경우 또는 1.18 이전 Sdk 버전을 실행 하는 응용 프로그램이 있는 경우 확인란을 선택 취소 합니다.

![Azure portal을 사용 하 여 큰 파티션 키를 만들려면](./media/large-partition-keys/large-partition-key-with-portal.png)


## <a name="supported-sdk-versions"></a>지원되는 SDK 버전

큰 파티션 키가 다음 최소 버전의 Sdk 사용 하 여 지원 됩니다.

|SDK 유형  | 최소 버전   |
|---------|---------|
|.Net     |    1.18     |
|Java 동기화     |   2.4.0      |
|Java Async   |  2.5.0        |
| REST API | 버전 보다 높은 `2017-05-03` 를 사용 하 여는 `x-ms-version` 요청 헤더입니다.|

현재 Power BI 및 Azure Logic Apps 내에서 많은 파티션 키를 사용 하 여 컨테이너를 사용할 수 없습니다. 이러한 응용 프로그램에서 큰 파티션 키 없이 컨테이너를 사용할 수 있습니다. 
 
## <a name="next-steps"></a>다음 단계

* [Azure Cosmos DB에서 분할](partitioning-overview.md)
* [Azure Cosmos DB의 요청 단위](request-units.md)
* [컨테이너 및 데이터베이스에 대한 처리량 프로비전](set-throughput.md)
* [Azure Cosmos 계정 작업](account-overview.md)


