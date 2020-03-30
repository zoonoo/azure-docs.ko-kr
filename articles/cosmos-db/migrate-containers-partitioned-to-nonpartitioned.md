---
title: 분할되지 않은 Azure Cosmos 컨테이너를 분할된 컨테이너로 마이그레이션합니다.
description: 분할되지 않은 모든 기존 컨테이너를 분할된 컨테이너로 마이그레이션하는 방법을 알아봅니다.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/25/2019
ms.author: mjbrown
ms.openlocfilehash: 742ef62895f3ef64e8fa22ab21d2947bee57776b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77623362"
---
# <a name="migrate-non-partitioned-containers-to-partitioned-containers"></a>분할되지 않은 컨테이너를 분할된 컨테이너로 마이그레이션

Azure Cosmos DB는 파티션 키 없이 컨테이너 만들기를 지원합니다. 현재 버전이 2.x 미만이거나 같을 버전이 있는 Azure CLI 및 Azure Cosmos DB SDK(.Net, Java, NodeJs)를 사용하여 분할되지 않은 컨테이너를 만들 수 있습니다. Azure 포털을 사용하여 분할되지 않은 컨테이너를 만들 수 없습니다. 그러나 이러한 분할되지 않은 컨테이너는 탄력적이지 않으며 20GB의 고정 저장 용량과 10K RU/s의 처리량 제한을 가지고 있습니다.

분할되지 않은 컨테이너는 레거시이며 기존 분할되지 않은 컨테이너를 분할된 컨테이너로 마이그레이션하여 저장소 및 처리량을 확장해야 합니다. Azure Cosmos DB는 분할되지 않은 컨테이너를 분할된 컨테이너로 마이그레이션하는 시스템 정의 메커니즘을 제공합니다. 이 문서에서는 분할되지 않은 모든 기존 컨테이너가 분할된 컨테이너로 자동 마이그레이션되는 방법을 설명합니다. 모든 언어에서 V3 버전의 SDK를 사용하는 경우에만 자동 마이그레이션 기능을 활용할 수 있습니다.

> [!NOTE]
> 현재 이 문서에서 설명하는 단계를 사용하여 Azure Cosmos DB MongoDB 및 Gremlin API 계정을 마이그레이션할 수 없습니다.

## <a name="migrate-container-using-the-system-defined-partition-key"></a>시스템 정의 파티션 키를 사용하여 컨테이너 마이그레이션

마이그레이션을 지원하기 위해 Azure Cosmos DB는 파티션 키가 없는 모든 컨테이너에 명명된 `/_partitionkey` 시스템 정의 파티션 키를 제공합니다. 컨테이너를 마이그레이션한 후에는 파티션 키 정의를 변경할 수 없습니다. 예를 들어 분할된 컨테이너로 마이그레이션되는 컨테이너의 정의는 다음과 같습니다.

```json
{
    "Id": "CollId" 
  "partitionKey": {
    "paths": [
      "/_partitionKey"
    ],
    "kind": "Hash"
  },
}
```

컨테이너를 마이그레이션한 후 문서의 `_partitionKey` 다른 속성과 함께 속성을 채우면 문서를 만들 수 있습니다. 속성은 `_partitionKey` 문서의 파티션 키를 나타냅니다.

프로비저닝된 처리량을 최적으로 활용하려면 올바른 파티션 키를 선택하는 것이 중요합니다. 자세한 내용은 파티션 키 문서를 [선택하는 방법을 참조하세요.](partitioning-overview.md)

> [!NOTE]
> 모든 언어에서 최신/V3 버전의 SDK를 사용하는 경우에만 시스템 정의 파티션 키를 활용할 수 있습니다.

다음 예제에서는 시스템 정의 파티션 키가 있는 문서를 만들고 해당 문서를 읽는 샘플 코드를 보여 주며 다음 을 보여 주며 해당 문서를 읽습니다.

**문서의 JSON 표현**

```csharp
DeviceInformationItem = new DeviceInformationItem
{
   "id": "elevator/PugetSound/Building44/Floor1/1",
   "deviceId": "3cf4c52d-cc67-4bb8-b02f-f6185007a808",
   "_partitionKey": "3cf4c52d-cc67-4bb8-b02f-f6185007a808"
} 

public class DeviceInformationItem
{
    [JsonProperty(PropertyName = "id")]
    public string Id { get; set; }

    [JsonProperty(PropertyName = "deviceId")]
    public string DeviceId { get; set; }

    [JsonProperty(PropertyName = "_partitionKey", NullValueHandling = NullValueHandling.Ignore)]
    public string PartitionKey {get {return this.DeviceId; set; }
}

CosmosContainer migratedContainer = database.Containers["testContainer"];

DeviceInformationItem deviceItem = new DeviceInformationItem() {
  Id = "1234",
  DeviceId = "3cf4c52d-cc67-4bb8-b02f-f6185007a808"
}

ItemResponse<DeviceInformationItem > response = 
  await migratedContainer.CreateItemAsync<DeviceInformationItem>(
    deviceItem.PartitionKey, 
    deviceItem
  );

// Read back the document providing the same partition key
ItemResponse<DeviceInformationItem> readResponse = 
  await migratedContainer.ReadItemAsync<DeviceInformationItem>( 
    partitionKey:deviceItem.PartitionKey, 
    id: device.Id
  );

```

전체 샘플은 [.Net 샘플][1] GitHub 리포지토리를 참조하십시오.
                      
## <a name="migrate-the-documents"></a>문서 마이그레이션

컨테이너 정의는 파티션 키 속성으로 향상되지만 컨테이너 내의 문서는 자동으로 마이그레이션되지 않습니다. 즉, 시스템 파티션 `/_partitionKey` 키 속성 경로가 기존 문서에 자동으로 추가되지 않습니다. 파티션 키 없이 만든 문서를 읽고 문서의 속성으로 `_partitionKey` 다시 작성하여 기존 문서를 다시 분할해야 합니다.

## <a name="access-documents-that-dont-have-a-partition-key"></a>파티션 키가 없는 문서에 액세스

응용 프로그램은 "PartitionKey.None"이라는 특수 시스템 속성을 사용하여 파티션 키가 없는 기존 문서에 액세스할 수 있습니다. 모든 CRUD 및 쿼리 작업에서 이 속성을 사용할 수 있습니다. 다음 예제에서는 NonePartitionKey에서 단일 문서를 읽는 샘플을 보여 주다. 

```csharp
CosmosItemResponse<DeviceInformationItem> readResponse = 
await migratedContainer.Items.ReadItemAsync<DeviceInformationItem>( 
  partitionKey: PartitionKey.None, 
  id: device.Id
); 

```

문서를 다시 분할하는 방법에 대한 전체 샘플은 [.Net 샘플][1] GitHub 리포지토리를 참조하십시오. 

## <a name="compatibility-with-sdks"></a>SDK와의 호환성

V2.x.x 및 V1.x.x와 같은 Azure Cosmos DB SDK의 이전 버전은 시스템 정의 파티션 키 속성을 지원하지 않습니다. 따라서 이전 SDK에서 컨테이너 정의를 읽을 때 파티션 키 정의가 포함되어 있지 않으며 이러한 컨테이너는 이전과 똑같이 작동합니다. 이전 버전의 SDK로 빌드된 응용 프로그램은 변경 없이 분할되지 않은 것으로 계속 작동합니다. 

마이그레이션된 컨테이너가 최신/V3 버전의 SDK에서 사용되고 새 문서 내에서 시스템 정의 파티션 키를 채우기 시작하면 이전 SDK의 해당 문서에 더 이상 액세스(읽기, 업데이트, 삭제, 쿼리)할 수 없습니다.

## <a name="known-issues"></a>알려진 문제

**V3 SDK를 사용하여 파티션 키 없이 삽입된 항목수를 쿼리하려면 처리량 소비가 더 많을 수 있습니다.**

V2 SDK를 사용하여 삽입된 항목에 대해 V3 SDK에서 쿼리하거나 V3 SDK를 매개 변수와 함께 `PartitionKey.None` 사용하여 삽입한 항목을 쿼리하는 `PartitionKey.None` 경우 피드옵션에서 매개 변수가 제공되는 경우 개수 쿼리에서 더 많은 RU/s를 사용할 수 있습니다. 파티션 키로 `PartitionKey.None` 다른 항목을 삽입하지 않으면 매개 변수를 제공하지 않는 것이 좋습니다.

파티션 키에 대해 다른 값으로 새 항목을 삽입하는 경우 해당 키를 `FeedOptions` 전달하여 해당 항목 수를 쿼리하는 데 문제가 없습니다. 파티션 키를 사용하여 새 문서를 삽입한 후 파티션 키 값 없이 문서 개수만 쿼리해야 하는 경우 해당 쿼리는 일반 분할 된 컬렉션과 유사한 RU/s가 다시 발생할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [Azure Cosmos DB에서 분할](partitioning-overview.md)
* [Azure 코스모스 DB의 요청 단위](request-units.md)
* [컨테이너 및 데이터베이스의 처리량 프로비전](set-throughput.md)
* [Azure Cosmos 계정 작업](account-overview.md)

[1]: https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/NonPartitionContainerMigration