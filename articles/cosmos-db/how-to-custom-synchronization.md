---
title: Azure Cosmos DB에서 고가용성 및 성능 최적화를 위한 사용자 지정 동기화 구현 방법
description: Azure Cosmos DB에서 고가용성 및 성능 최적화를 위한 사용자 지정 동기화 구현 방법을 알아봅니다.
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 2/12/2019
ms.author: mjbrown
ms.openlocfilehash: 9033a7502919c8dc05053048272f3fa62f81b31d
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2019
ms.locfileid: "56118870"
---
# <a name="how-to-implement-custom-synchronization-to-optimize-for-higher-availability-and-performance"></a>고가용성 및 성능 최적화를 위한 사용자 지정 동기화 구현 방법

Azure Cosmos DB에서 제공하는 잘 정의된 5가지 일관성 수준 중에 선택하여 일관성, 성능, 가용성 간의 적절한 균형을 유지할 수 있습니다. 일관성이 강하면 Cosmos 계정이 구성된 모든 Azure 지역에서 데이터가 동기적으로 복제되고 내구성이 유지됩니다. 하지만 이 구성은 성능 및 가용성을 희생하는 대신 최고 수준의 내구성을 제공합니다. 애플리케이션에서 가용성을 희생하지 않고 애플리케이션 요구 사항에 맞게 내구성을 제어/완화하려는 경우 애플리케이션 레이어에서 사용자 지정 동기화를 사용하면 원하는 수준의 내구성을 달성할 수 있습니다.

아래 다이어그램은 사용자 지정 동기화 모델을 시각적으로 보여줍니다.

![사용자 지정 동기화](./media/how-to-custom-synchronization/custom-synchronization.png)

이 시나리오에서 Cosmos 컨테이너는 여러 대륙의 여러 Azure 지역에 전역적으로 복제됩니다. 이 시나리오의 모든 Azure 지역에 강력한 일관성을 사용하면 성능에 영향을 줍니다. 쓰기 대기 시간에 악영향을 주지 않고 높은 수준의 데이터 내구성을 유지하려면 애플리케이션에서 동일한 세션 토큰을 공유하는 두 개의 클라이언트를 사용하면 됩니다.

첫 번째 클라이언트는 로컬 지역(예: 미국 서부)에 데이터를 쓸 수 있습니다. 두 번째 클라이언트(예: 미국 동부에 위치)는 동기화를 보장하기 위해 사용되는 읽기 클라이언트입니다. 쓰기 응답에서 그 다음 읽기로 세션 토큰을 흘려 보내면 읽기 시 미국 동부에 대한 쓰기 동기화가 보장됩니다. Azure Cosmos DB는 원래 쓰기 지역이 다운되더라도 하나 이상의 Azure 지역에서 쓰기를 보장하고, 지역 중단 시 데이터가 유지될 것을 보증합니다. 이 시나리오의 모든 쓰기는 미국 동부에 동기화되므로 모든 Azure 지역에서 강력한 일관성을 사용하기 위한 대기 시간이 감소합니다. 쓰기가 모든 Azure 지역에서 발생하는 다중 마스터 시나리오에서는 여러 Azure 지역에 병렬로 동기화되도록 이 모델을 확장할 수 있습니다.

## <a name="configure-the-clients"></a>클라이언트 구성

아래 샘플에서는 이 용도로 두 클라이언트를 인스턴스화하는 데이터 액세스 레이어를 보여줍니다.

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

여러 Azure 지역에 병렬로 동기화되도록 이 모델을 확장할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Azure Cosmos DB의 글로벌 배포 및 일관성에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Azure Cosmos DB에서 적절한 일관성 수준 선택](consistency-levels-choosing.md)

* [Azure Cosmos DB의 일관성, 가용성 및 성능 절충](consistency-levels-tradeoffs.md)

* [Azure Cosmos DB에서 일관성을 관리하는 방법](how-to-manage-consistency.md)

* [Azure Cosmos DB에서 분할 및 데이터 배포](partition-data.md)
