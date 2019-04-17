---
title: Azure Cosmos DB에서 데이터베이스 계정을 관리하는 방법 알아보기
description: Azure Cosmos DB에서 데이터베이스 계정을 관리하는 방법 알아보기
author: rimman
ms.service: cosmos-db
ms.topic: sample
ms.date: 04/08/2019
ms.author: rimman
ms.openlocfilehash: b2b5e58ca480aa3abaa0766319977b8d1160ebeb
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2019
ms.locfileid: "59283004"
---
# <a name="manage-an-azure-cosmos-account"></a>Azure Cosmos 계정 관리

이 문서에서는 Azure Cosmos 계정을 관리하는 방법을 설명합니다. multi-homing 설정, 지역 추가 또는 제거, 여러 쓰기 지역 구성 및 장애 조치 우선 순위 설정을 수행하는 방법을 알아봅니다. 

## <a name="create-a-database-account"></a>데이터베이스 계정 만들기

### <a id="create-database-account-via-portal"></a>Azure Portal

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

### <a id="create-database-account-via-cli"></a>Azure CLI

```bash
# Create an account
az cosmosdb create --name <Azure Cosmos account name> --resource-group <Resource Group Name>
```

## <a name="configure-clients-for-multi-homing"></a>클라이언트 멀티 호밍 구성

### <a id="configure-clients-multi-homing-dotnet"></a>.NET SDK v2

```csharp
ConnectionPolicy policy = new ConnectionPolicy
    {
        ConnectionMode = ConnectionMode.Direct,
        ConnectionProtocol = Protocol.Tcp,
        UseMultipleWriteLocations = true
    };
policy.SetCurrentLocation("West US 2");

// Pass the connection policy with the preferred locations on it to the client.
DocumentClient client = new DocumentClient(new Uri(this.accountEndpoint), this.accountKey, policy);
```

### <a id="configure-clients-multi-homing-dotnet-v3"></a>.NET SDK v3(미리 보기)

```csharp
CosmosConfiguration config = new CosmosConfiguration("endpoint", "key");
config.UseCurrentRegion("West US");
CosmosClient client = new CosmosClient(config);
```

### <a id="configure-clients-multi-homing-java-async"></a>Java 비동기 SDK

```java
ConnectionPolicy policy = new ConnectionPolicy();
policy.setUsingMultipleWriteLocations(true);
policy.setPreferredLocations(Collections.singletonList(region));

AsyncDocumentClient client =
    new AsyncDocumentClient.Builder()
        .withMasterKeyOrResourceToken(this.accountKey)
        .withServiceEndpoint(this.accountEndpoint)
        .withConsistencyLevel(ConsistencyLevel.Eventual)
        .withConnectionPolicy(policy).build();
```

### <a id="configure-clients-multi-homing-javascript"></a>Node.js/JavaScript/TypeScript SDK

```javascript
const connectionPolicy: ConnectionPolicy = new ConnectionPolicy();
connectionPolicy.UseMultipleWriteLocations = true;
connectionPolicy.PreferredLocations = [region];

const client = new CosmosClient({
  endpoint: config.endpoint,
  auth: { masterKey: config.key },
  connectionPolicy,
  consistencyLevel: ConsistencyLevel.Eventual
});
```

### <a id="configure-clients-multi-homing-python"></a>Python SDK

```python
connection_policy = documents.ConnectionPolicy()
connection_policy.UseMultipleWriteLocations = True
connection_policy.PreferredLocations = [region]

client = cosmos_client.CosmosClient(self.account_endpoint, {'masterKey': self.account_key}, connection_policy, documents.ConsistencyLevel.Session)
```

## <a name="addremove-regions-from-your-database-account"></a>데이터베이스 계정에서 Azure 지역 추가/제거

### <a id="add-remove-regions-via-portal"></a>Azure Portal

1. Azure Cosmos 계정으로 이동하고, **전역적으로 데이터 복제** 메뉴를 엽니다.

2. 지역을 추가하려면 맵에서 원하는 지역에 해당하는 **+** 레이블이 있는 육각형을 선택합니다. 또는, 지역을 추가하려면 **+ 지역 추가** 옵션을 선택하고 드롭다운 메뉴에서 Azure 지역을 선택합니다.

3. 지역을 제거하려면 확인 표시가 있는 파란색 육각형을 선택하여 맵에서 하나 이상의 지역을 지웁니다. 또는 오른쪽의 지역 옆에 있는 "휴지통"(🗑) 아이콘을 선택합니다.

4. 변경 내용을 저장하려면 **확인**을 선택합니다.

   ![지역 추가 또는 제거 메뉴](./media/how-to-manage-database-account/add-region.png)

단일 지역 쓰기 모드에서는 쓰기 지역을 제거할 수 없습니다. 현재 쓰기 지역을 삭제하려면 먼저 다른 Azure 지역으로 장애 조치해야 합니다.

다중 지역 쓰기 모드에서는 하나 이상의 Azure 지역이 있으면 Azure 지역을 추가하거나 제거할 수 있습니다.

### <a id="add-remove-regions-via-cli"></a>Azure CLI

```bash
# Create an account with 1 region
az cosmosdb create --name <Azure Cosmos account name> --resource-group <Resource Group name> --locations eastus=0

# Add a region
az cosmosdb update --name <Azure Cosmos account name> --resource-group <Resource Group name> --locations eastus=0 westus=1

# Remove a region
az cosmosdb update --name <Azure Cosmos account name> --resource-group <Resource Group name> --locations westus=0
```

## <a name="configure-multiple-write-regions"></a>다중 쓰기 지역 구성

### <a id="configure-multiple-write-regions-portal"></a>Azure Portal

데이터베이스 계정을 만들 때 **다중 지역 쓰기** 설정을 사용해야 합니다.

![Azure Cosmos 계정 만들기 스크린샷](./media/how-to-manage-database-account/account-create.png)

### <a id="configure-multiple-write-regions-cli"></a>Azure CLI

```bash
az cosmosdb create --name <Azure Cosmos account name> --resource-group <Resource Group name> --enable-multiple-write-locations true
```

### <a id="configure-multiple-write-regions-arm"></a>리소스 관리자 템플릿

다음 JSON 코드는 [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) 템플릿 예제입니다. [제한된 부실 일관성 수준](consistency-levels.md)으로 Azure Cosmos 계정을 배포하는 데 사용할 수 있습니다. 최대 부실 간격은 5초로 설정됩니다. 허용되는 최대 부실 요청 수는 100개로 설정됩니다. Resource Manager 템플릿 형식 및 구문에 대해 자세히 알아보려면 [Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md)를 참조하세요.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "name": {
            "type": "String"
        },
        "location": {
            "type": "String"
        },
        "locationName": {
            "type": "String"
        },
        "defaultExperience": {
            "type": "String"
        }
    },
    "resources": [
        {
            "type": "Microsoft.DocumentDb/databaseAccounts",
            "kind": "GlobalDocumentDB",
            "name": "[parameters('name')]",
            "apiVersion": "2015-04-08",
            "location": "[parameters('location')]",
            "tags": {
                "defaultExperience": "[parameters('defaultExperience')]"
            },
            "properties": {
                "databaseAccountOfferType": "Standard",
                "consistencyPolicy": {
                    "defaultConsistencyLevel": "BoundedStaleness",
                    "maxIntervalInSeconds": 5,
                    "maxStalenessPrefix": 100
                },
                "locations": [
                    {
                        "id": "[concat(parameters('name'), '-', parameters('location'))]",
                        "failoverPriority": 0,
                        "locationName": "[parameters('locationName')]"
                    }
                ],
                "isVirtualNetworkFilterEnabled": false,
                "enableMultipleWriteLocations": true,
                "virtualNetworkRules": [],
                "dependsOn": []
            }
        }
    ]
}
```


## <a id="manual-failover"></a>Azure Cosmos 계정에 수동 장애 조치(failover) 사용

### <a id="enable-manual-failover-via-portal"></a>Azure Portal

1. Azure Cosmos 계정으로 이동하고, **전역적으로 데이터 복제** 메뉴를 엽니다.

2. 메뉴의 위쪽에서 **수동 장애 조치**를 선택합니다.

   ![[전역적으로 데이터 복제] 메뉴](./media/how-to-manage-database-account/replicate-data-globally.png)

3. **수동 장애 조치** 메뉴에서 새 쓰기 지역을 선택합니다. 이 옵션이 쓰기 영역을 변경한다는 것을 알고 있음을 나타내는 확인란을 선택합니다.

4. 장애 조치를 트리거하려면 **확인**을 선택합니다.

   ![수동 장애 조치(failover) 포털 메뉴](./media/how-to-manage-database-account/manual-failover.png)

### <a id="enable-manual-failover-via-cli"></a>Azure CLI

```bash
# Given your account currently has regions with priority: eastus=0 westus=1
# Change the priority order to trigger a failover of the write region
az cosmosdb update --name <Azure Cosmos account name> --resource-group <Resource Group name> --locations westus=0 eastus=1
```

## <a id="automatic-failover"></a>Azure Cosmos DB 계정에 자동 장애 조치 사용

### <a id="enable-automatic-failover-via-portal"></a>Azure Portal

1. Azure Cosmos DB 계정에서 **글로벌로 데이터 복제** 창을 엽니다. 

2. 창의 위쪽에서 **자동 장애 조치**를 선택합니다.

   ![[전역적으로 데이터 복제] 메뉴](./media/how-to-manage-database-account/replicate-data-globally.png)

3. **자동 장애 조치** 창에서 **자동 장애 조치 사용**을 **켜기**로 설정합니다. 

4. **저장**을 선택합니다.

   ![자동 장애 조치(failover) 포털 메뉴](./media/how-to-manage-database-account/automatic-failover.png)

또한 이 메뉴에서 장애 조치 우선 순위를 설정할 수도 있습니다.

### <a id="enable-automatic-failover-via-cli"></a>Azure CLI

```bash
# Enable automatic failover on account creation
az cosmosdb create --name <Azure Cosmos account name> --resource-group <Resource Group name> --enable-automatic-failover true

# Enable automatic failover on an existing account
az cosmosdb update --name <Azure Cosmos account name> --resource-group <Resource Group name> --enable-automatic-failover true

# Disable automatic failover on an existing account
az cosmosdb update --name <Azure Cosmos account name> --resource-group <Resource Group name> --enable-automatic-failover false
```

## <a name="set-failover-priorities-for-your-azure-cosmos-account"></a>Azure Cosmos 계정의 장애 조치(failover) 우선 순위 설정

### <a id="set-failover-priorities-via-portal"></a>Azure Portal

1. Azure Cosmos 계정에서 **전역적으로 데이터 복제** 창을 엽니다. 

2. 창의 위쪽에서 **자동 장애 조치**를 선택합니다.

   ![[전역적으로 데이터 복제] 메뉴](./media/how-to-manage-database-account/replicate-data-globally.png)

3. **자동 장애 조치** 창에서 **자동 장애 조치 사용**을 **켜기**로 설정합니다. 

4. 장애 조치 우선 순위를 수정하려면 마우스로 가리킬 때 나타나는 행 왼쪽의 점 세 개를 통해 읽기 지역을 끕니다. 

5. **저장**을 선택합니다.

   ![자동 장애 조치(failover) 포털 메뉴](./media/how-to-manage-database-account/automatic-failover.png)

이 메뉴에서는 쓰기 지역을 수정할 수 없습니다. 쓰기 지역을 수동으로 변경하려면 수동 장애 조치(failover)를 수행해야 합니다.

### <a id="set-failover-priorities-via-cli"></a>Azure CLI

```bash
# Assume region order is initially eastus=0 westus=1 automatic failover on account creation
az cosmosdb failover-priority-change --name <Azure Cosmos account name> --resource-group <Resource Group name> --failover-policies westus=0 eastus=1
```

## <a name="next-steps"></a>다음 단계

다음 문서를 읽어보세요.

* [일관성 관리](how-to-manage-consistency.md)
* [Azure 지역 간 충돌 관리](how-to-manage-conflicts.md)
* [글로벌 배포 - 내부 살펴보기](global-dist-under-the-hood.md)
* [애플리케이션에서 다중 마스터를 구성하는 방법](how-to-multi-master.md)
* [클라이언트 멀티 호밍 구성](how-to-manage-database-account.md#configure-clients-for-multi-homing)
* [Azure Cosmos DB 계정에서 지역 추가 또는 제거](how-to-manage-database-account.md#addremove-regions-from-your-database-account)
* [사용자 지정 충돌 해결 정책 만들기](how-to-manage-conflicts.md#create-a-custom-conflict-resolution-policy)

