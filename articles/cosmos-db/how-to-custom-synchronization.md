---
title: Azure Cosmos DB에서 고가용성 및 성능 최적화를 위한 사용자 지정 동기화 구현 방법
description: Azure Cosmos DB에서 고가용성 및 성능 최적화를 위한 사용자 지정 동기화 구현 방법을 알아봅니다.
author: rimman
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/23/2019
ms.author: rimman
ms.openlocfilehash: cd89a145f5746696cc8fc163eb46896081d85a90
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66240955"
---
# <a name="implement-custom-synchronization-to-optimize-for-higher-availability-and-performance"></a>고가용성 및 성능 최적화를 위한 사용자 지정 동기화 구현

Azure Cosmos DB에서 제공하는 [잘 정의된 5가지 일관성 수준](consistency-levels.md) 중에서 선택하여 일관성, 성능, 가용성 간의 적절한 균형을 유지할 수 있습니다. 강력한 일관성은 Azure Cosmos 계정을 사용할 수 있는 모든 Azure 지역에서 데이터가 동기화되어 복제되고 내구성이 유지되도록 합니다. 이 구성은 최고 수준의 내구성을 제공하지만 성능 및 가용성을 희생합니다. 애플리케이션에서 가용성을 희생하지 않고 애플리케이션 요구에 맞게 내구성을 제어하거나 완화하려는 경우 애플리케이션 레이어에서 *사용자 지정 동기화*를 사용하면 원하는 수준의 내구성을 달성할 수 있습니다.

다음 이미지는 사용자 지정 동기화 모델을 시각적으로 보여줍니다.

![사용자 지정 동기화](./media/how-to-custom-synchronization/custom-synchronization.png)

이 시나리오에서 Azure Cosmos 컨테이너는 여러 대륙의 여러 Azure 지역에 전역적으로 복제됩니다. 이 시나리오의 모든 Azure 지역에 강력한 일관성을 사용하면 성능에 영향을 줍니다. 쓰기 대기 시간에 악영향을 주지 않고 높은 수준의 데이터 내구성을 유지하려면 애플리케이션에서 동일한 [세션 토큰](how-to-manage-consistency.md#utilize-session-tokens)을 공유하는 두 개의 클라이언트를 사용하면 됩니다.

첫 번째 클라이언트는 로컬 지역(예: 미국 서부)에 데이터를 쓸 수 있습니다. 두 번째 클라이언트(예: 미국 동부에 위치)는 동기화를 보장하기 위해 사용되는 읽기 클라이언트입니다. 쓰기 응답에서 그 다음 읽기로 세션 토큰을 흘려 보내면 읽기에서 미국 동부에 대한 쓰기 동기화가 보장됩니다. Azure Cosmos DB는 쓴 내용이 적어도 하나의 Azure 지역에서 보이는지 확인합니다. 원래 쓰기 지역이 다운되는 경우 지역 가동 중단 시에도 쓴 내용이 유지됩니다. 이 시나리오에서 모든 쓰기는 미국 동부에 동기화되므로 모든 Azure 지역 간에 강력한 일관성이 유지되어 대기 시간이 감소합니다. 모든 Azure 지역에서 쓰기가 발생하는 다중 마스터 시나리오에서는 이 모델을 확장하여 여러 Azure 지역에 병렬로 동기화할 수 있습니다.

## <a name="configure-the-clients"></a>클라이언트 구성

다음 샘플은 사용자 지정 동기화를 위한 클라이언트 두 개를 인스턴스화하는 데이터 액세스 레이어를 보여줍니다.

```csharp
class MyDataAccessLayer
{
    private DocumentClient writeClient;
    private DocumentClient readClient;

    public async Task InitializeAsync(Uri accountEndpoint, string key)
    {
        ConnectionPolicy writeConnectionPolicy = new ConnectionPolicy
        {
            ConnectionMode = ConnectionMode.Direct,
            ConnectionProtocol = Protocol.Tcp,
            UseMultipleWriteLocations = true
        };
        writeConnectionPolicy.SetCurrentLocation(LocationNames.WestUS);

        ConnectionPolicy readConnectionPolicy = new ConnectionPolicy
        {
            ConnectionMode = ConnectionMode.Direct,
            ConnectionProtocol = Protocol.Tcp
        };
        readConnectionPolicy.SetCurrentLocation(LocationNames.EastUS);

        writeClient = new DocumentClient(accountEndpoint, key, writeConnectionPolicy);
        readClient = new DocumentClient(accountEndpoint, key, readConnectionPolicy, ConsistencyLevel.Session);

        await Task.WhenAll(new Task[]
        {
            writeClient.OpenAsync(),
            readClient.OpenAsync()
        });
    }
}
```

## <a name="implement-custom-synchronization"></a>사용자 지정 동기화 구현

클라이언트가 초기화되면 애플리케이션에서는 다음과 같이 로컬 Azure 지역(미국 서부)에 쓰기를 수행하고 미국 동부에 대한 쓰기 동기화를 강제할 수 있습니다.

```csharp
class MyDataAccessLayer
{
    public async Task CreateItem(string containerLink, Document document)
    {
        ResourceResponse<Document> response = await writeClient.CreateDocumentAsync(
            containerLink, document);

        await readClient.ReadDocumentAsync(response.Resource.SelfLink,
            new RequestOptions { SessionToken = response.SessionToken });
    }
}
```

여러 Azure 지역에 병렬로 동기화하도록 모델을 확장할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Azure Cosmos DB의 글로벌 배포 및 일관성에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Azure Cosmos DB에서 올바른 일관성 수준 선택](consistency-levels-choosing.md)
* [Azure Cosmos DB의 일관성, 가용성 및 성능 절충](consistency-levels-tradeoffs.md)
* [Azure Cosmos DB에서 일관성 관리](how-to-manage-consistency.md)
* [Azure Cosmos DB에서 분할 및 데이터 배포](partition-data.md)
