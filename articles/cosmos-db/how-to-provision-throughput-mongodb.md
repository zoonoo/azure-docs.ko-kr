---
title: Azure Cosmos DB API for MongoDB 리소스에 처리량 프로비저닝
description: Azure Cosmos DB API for MongoDB 리소스에서 컨테이너, 데이터베이스 및 자동 크기 조정 처리량을 프로비저닝하는 방법을 알아봅니다. Azure Portal, CLI, PowerShell 및 기타 다양한 SDK를 사용합니다.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 10/15/2020
ms.author: mjbrown
ms.custom: devx-track-js, devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 25eaa4a66fb4a73f976edbd30e6f82015ce84f6f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93086128"
---
# <a name="provision-database-container-or-autoscale-throughput-on-azure-cosmos-db-api-for-mongodb-resources"></a>Azure Cosmos DB API for MongoDB 리소스에 데이터베이스, 컨테이너 및 자동 크기 조정 처리량을 프로비저닝
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

이 문서에서는 Azure Cosmos DB API for MongoDB에서 처리량을 프로비저닝하는 방법을 설명합니다. 컨테이너 또는 데이터베이스에 표준(수동) 또는 자동 크기 조정 처리량을 프로비저닝하고 데이터베이스 내의 컨테이너에 공유할 수 있습니다. Azure Portal, Azure CLI 또는 Azure Cosmos DB SDK를 사용하여 처리량을 프로비저닝할 수 있습니다.

다른 API를 사용하는 경우 [SQL API](how-to-provision-container-throughput.md), [Cassandra API](how-to-provision-throughput-cassandra.md), [Gremlin API](how-to-provision-throughput-gremlin.md) 문서를 참조하여 처리량을 프로비저닝합니다.

## <a name="azure-portal"></a><a id="portal-mongodb"></a> Azure Portal

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. [새 Azure Cosmos 계정을 만들거나](create-mongodb-dotnet.md#create-a-database-account) 기존 Azure Cosmos 계정을 선택합니다.

1. **데이터 탐색기** 창을 열고 **새 컬렉션** 을 선택합니다. 다음으로, 다음과 같은 세부 정보를 제공합니다.

   * 새 데이터베이스를 만드는지 아니면 기존 데이터베이스를 사용하는지 표시합니다. 데이터베이스 수준에서 처리량을 프로비저닝하려면 **데이터베이스 처리량 프로비저닝** 옵션을 선택합니다.
   * 컬렉션 ID를 입력합니다.
   * 파티션 키 값을 입력합니다(예: `/ItemID`).
   * 프로비저닝하려는 처리량을 입력합니다(예: 1000RU).
   * **확인** 을 선택합니다.

    :::image type="content" source="./media/how-to-provision-throughput-mongodb/provision-database-throughput-portal-mongodb-api.png" alt-text="데이터베이스 수준 처리량으로 새 컬렉션을 만들 때 Data Explorer의 스크린샷":::

> [!Note]
> Azure Cosmos DB API for MongoDB를 사용하여 구성한 Azure Cosmos 계정의 컨테이너에 처리량을 프로비저닝하는 경우 파티션 키 경로로 `/myShardKey`를 사용합니다.

## <a name="net-sdk"></a><a id="dotnet-mongodb"></a> .NET SDK

```csharp
// refer to MongoDB .NET Driver
// https://docs.mongodb.com/drivers/csharp

// Create a new Client
String mongoConnectionString = "mongodb://DBAccountName:Password@DBAccountName.documents.azure.com:10255/?ssl=true&replicaSet=globaldb";
mongoUrl = new MongoUrl(mongoConnectionString);
mongoClientSettings = MongoClientSettings.FromUrl(mongoUrl);
mongoClient = new MongoClient(mongoClientSettings);

// Change the database name
mongoDatabase = mongoClient.GetDatabase("testdb");

// Change the collection name, throughput value then update via MongoDB extension commands
// https://docs.microsoft.com/en-us/azure/cosmos-db/mongodb-custom-commands#update-collection

var result = mongoDatabase.RunCommand<BsonDocument>(@"{customAction: ""UpdateCollection"", collection: ""testcollection"", offerThroughput: 400}");
```

## <a name="azure-resource-manager"></a>Azure 리소스 관리자

Azure Resource Manager 템플릿은 모든 Azure Cosmos DB API에 대한 데이터베이스 또는 컨테이너 수준 리소스에 자동 크기 조정 처리량을 프로비저닝하는 데 사용될 수 있습니다. [Azure Cosmos DB용 Azure Resource Manager 템플릿](templates-samples-mongodb.md)에서 샘플을 확인하십시오.

## <a name="azure-cli"></a>Azure CLI

Azure CLI는 모든 Azure Cosmos DB API에 대한 데이터베이스 또는 컨테이너 수준 리소스에 자동 크기 조정 처리량을 프로비저닝하는 데 사용될 수 있습니다. [Azure Cosmos DB에 대한 Azure CLI 샘플](cli-samples-mongodb.md)에서 샘플을 확인하십시오.

## <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell은 모든 Azure Cosmos DB API에 대한 데이터베이스 또는 컨테이너 수준 리소스에 자동 크기 조정 처리량을 프로비저닝하는 데 사용될 수 있습니다. [Azure Cosmos DB에 대한 Azure PowerShell 샘플](powershell-samples-mongodb.md)에서 샘플을 확인하십시오.

## <a name="next-steps"></a>다음 단계

다음 문서를 참조하여 Azure Cosmos DB에서 처리량을 프로비저닝하는 방법을 알아보세요.

* [Azure Cosmos DB의 요청 단위 및 처리량](request-units.md)