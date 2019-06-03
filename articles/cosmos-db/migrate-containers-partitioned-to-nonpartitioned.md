---
title: Azure Cosmos DB 컨테이너 분할 되지 않은 분할 된 컨테이너로 마이그레이션
description: 분할 된 컨테이너에 모든 기존 분할 되지 않은 컨테이너를 마이그레이션하는 방법에 알아봅니다.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: mjbrown
ms.openlocfilehash: 8ba9489496a8f9e3703702e344684b4028a002cc
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66241935"
---
# <a name="migrate-non-partitioned-containers-to-partitioned-containers"></a>분할 된 컨테이너로 분할 되지 않은 컨테이너 마이그레이션

Azure Cosmos DB는 파티션 키 없이 컨테이너를 만들기를 지원합니다. 현재 Azure CLI 및 Azure Cosmos DB Sdk (.NET, Java, NodeJs) 2.x 작은 버전을 사용 하 여 분할 되지 않은 컨테이너를 만들 수 있습니다. Azure portal을 사용 하 여 분할 되지 않은 컨테이너를 만들 수 없습니다. 그러나 이러한 분할 되지 않은 컨테이너 탄력적 되지 및 10GB와 처리량 5k RU/s 10 제한인 용량의 저장소를 수정 합니다.

분할 되지 않은 컨테이너는 레거시 및 저장소 크기 조정 및 처리량에 분할 된 컨테이너로 분할 되지 않은 사용 하 여 기존 컨테이너를 마이그레이션해야 합니다. Azure Cosmos DB는 분할 된 컨테이너로 분할 되지 않은 컨테이너를 마이그레이션하는 시스템 정의 메커니즘을 제공 합니다. 이 문서에서는 분할 된 컨테이너에 자동으로 마이그레이션된 모든 기존 분할 되지 않은 컨테이너 방법을 합니다. 모든 언어에서 Sdk의 V3 버전을 사용 하는 경우에 자동 마이그레이션 기능을 걸릴 수 있습니다.

> [!NOTE] 
> 현재이 문서에 설명 된 단계를 사용 하 여 Azure Cosmos DB MongoDB 계정과 Gremlin API를 마이그레이션할 수 없습니다. 

## <a name="migrate-container-using-the-system-defined-partition-key"></a>시스템 정의 파티션 키를 사용 하 여 컨테이너를 마이그레이션

Azure Cosmos DB를 지원 하기 위해 마이그레이션을 라는 시스템 정의 파티션 키를 정의 `/_partitionkey` 파티션 키가 없는 모든 컨테이너에서. 컨테이너 마이그레이션된 후에 파티션 키 정의 변경할 수 없습니다. 예를 들어, 분할 된 컨테이너에 마이그레이션된 컨테이너의 정의 다음과 같이 됩니다. 

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
 
컨테이너를 마이그레이션한 후 채워 문서를 만들 수 있습니다는 `_partitionKey` 문서의 다른 속성과 함께 속성입니다. `_partitionKey` 속성 문서의 파티션 키를 나타냅니다. 

올바른 파티션 키를 선택 하는 것이 최적으로 프로 비전된 된 처리량을 활용 해야 합니다. 자세한 내용은 [파티션 키를 선택 하는 방법을](partitioning-overview.md) 문서. 

> [!NOTE]
> 모든 언어에서 Sdk의 최신/V3 버전을 사용 하는 경우에에 시스템 정의 된 파티션 키 활용을 걸릴 수 있습니다.

다음 예제에서는 시스템 정의 된 파티션 키를 사용 하 여 문서를 만들고 해당 문서의 내용을 확인 하는 샘플 코드를 보여 줍니다.

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

    [JsonProperty(PropertyName = "_partitionKey")]
    public string PartitionKey {get {return this.DeviceId; set; }
}

CosmosContainer migratedContainer = database.Containers["testContainer"];

DeviceInformationItem deviceItem = new DeviceInformationItem() {
  Id = "1234", 
  DeviceId = "3cf4c52d-cc67-4bb8-b02f-f6185007a808"
} 

CosmosItemResponse<DeviceInformationItem > response = 
  await migratedContainer.Items.CreateItemAsync(
    deviceItem.PartitionKey, 
    deviceItem
  );

// Read back the document providing the same partition key
CosmosItemResponse<DeviceInformationItem> readResponse = 
  await migratedContainer.Items.ReadItemAsync<DeviceInformationItem>( 
    partitionKey:deviceItem.PartitionKey, 
    id: device.Id
  ); 

```

전체 샘플을 참조 하세요. 합니다 [.NET 샘플](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/CodeSamples) GitHub 리포지토리. 
                      
## <a name="migrate-the-documents"></a>문서를 마이그레이션

컨테이너 내에서 문서를 자동 없는 경우 컨테이너 정의 파티션 키 속성을 사용 하 여 향상 되었으며 하는 동안 마이그레이션됩니다. 즉, 시스템 파티션 키 속성 `/_partitionKey` 경로에 기존 문서를 자동으로 추가 되지 않습니다. 파티션 키 없이 생성 된 문서를 참조 하 여 기존 문서를 다시 분할 하 고 다시 사용 하 여 다시 작성 해야 `_partitionKey` 문서의 속성입니다. 

## <a name="access-documents-that-dont-have-a-partition-key"></a>파티션 키가 없는 문서에 액세스

기존 문서를 "CosmosContainerSettings.NonePartitionKeyValue" 이라는 특수 한 시스템 속성을 사용 하 여 파티션 키가 없는 응용 프로그램에 액세스할 수, 마이그레이션되지 않은 문서의 값입니다. 모든 CRUD 및 쿼리 작업에서이 속성을 사용할 수 있습니다. 다음 예제는 NonePartitionKey에서 단일 문서를 읽기 위해 예제를 보여 줍니다. 

```csharp
CosmosItemResponse<DeviceInformationItem> readResponse = 
await migratedContainer.Items.ReadItemAsync<DeviceInformationItem>( 
  partitionKey: CosmosContainerSettings.NonePartitionKeyValue, 
  id: device.Id
); 

```

문서를 다시 분할 하는 방법에 대 한 전체 샘플을 참조 하세요. 합니다 [.NET 샘플](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/CodeSamples) GitHub 리포지토리. 

## <a name="compatibility-with-sdks"></a>Sdk와의 호환성

이전 버전 V2.x.x V1.x.x 등 Azure Cosmos DB Sdk의 시스템 정의 파티션 키 속성을 지원 하지 않습니다. 따라서 이전 SDK의 경우 컨테이너 정의 읽을 때 모든 파티션 키 정의 포함 하지 않습니다 하 고 이러한 컨테이너 이전과 똑같이 작동 합니다. 이전 버전의 Sdk 사용 하 여 빌드된 응용 프로그램 계속 분할 되지 않은 변경 하지 않고 그대로 사용 하 여 작동 합니다. 

SDK의 최신/V3 버전에서 마이그레이션된 컨테이너를 사용 하 고 새 문서 내에서 정의 된 시스템 파티션 키를 채우도록 (읽기, 업데이트, 삭제, 쿼리)에 액세스할 수 없는 경우 이러한 문서는 이전 Sdk에서 더 이상.

## <a name="next-steps"></a>다음 단계

* [Azure Cosmos DB에서 분할](partitioning-overview.md)
* [Azure Cosmos DB의 요청 단위](request-units.md)
* [컨테이너 및 데이터베이스에 대한 처리량 프로비전](set-throughput.md)
* [Azure Cosmos 계정 작업](account-overview.md)