---
title: Azure Cosmos DB에서 데이터베이스 계정을 관리하는 방법 알아보기
description: Azure Cosmos DB에서 데이터베이스 계정을 관리하는 방법 알아보기
services: cosmos-db
author: christopheranderson
ms.service: cosmos-db
ms.topic: sample
ms.date: 10/17/2018
ms.author: chrande
ms.openlocfilehash: 0683516d16bf1501eee83901c5171811b8c0e44d
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/14/2018
ms.locfileid: "51621550"
---
# <a name="manage-database-accounts-in-azure-cosmos-db"></a>Azure Cosmos DB에서 데이터베이스 계정 관리

이 문서에서는 Azure Cosmos DB 계정을 관리하여 멀티 호밍을 설정하고, 지역을 추가/제거하고, 여러 쓰기 지역을 구성하고, 장애 조치(failover) 우선 순위를 설정하는 방법을 설명합니다. 

## <a name="create-a-database-account"></a>데이터베이스 계정 만들기

### <a id="create-database-account-via-portal"></a>Azure Portal

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

### <a id="create-database-account-via-cli"></a>Azure CLI

```bash
# Create an account
az cosmosdb create --name <Azure Cosmos account name> --resource-group <Resource Group Name>
```

## <a name="configure-clients-for-multi-homing"></a>클라이언트 멀티 호밍 구성

### <a id="configure-clients-multi-homing-dotnet"></a>.NET SDK

```csharp
// Create a new Connection Policy
ConnectionPolicy policy = new ConnectionPolicy
    {
        // Note: These aren't required settings for multi-homing,
        // just suggested defaults
        ConnectionMode = ConnectionMode.Direct,
        ConnectionProtocol = Protocol.Tcp,
        UseMultipleWriteLocations = true,
    };
// Add regions to Preferred locations
// The name of the location will match what you see in the portal/etc.
policy.PreferredLocations.Add("East US");
policy.PreferredLocations.Add("North Europe");

// Pass the Connection policy with the preferred locations on it to the client.
DocumentClient client = new DocumentClient(new Uri(this.accountEndpoint), this.accountKey, policy);
```

### <a id="configure-clients-multi-homing-java-async"></a>Java 비동기 SDK

```java
ConnectionPolicy policy = new ConnectionPolicy();
policy.setPreferredLocations(Collections.singleton("West US"));
AsyncDocumentClient client =
        new AsyncDocumentClient.Builder()
                .withMasterKey(this.accountKey)
                .withServiceEndpoint(this.accountEndpoint)
                .withConnectionPolicy(policy).build();
```

### <a id="configure-clients-multi-homing-java-sync"></a>Java 동기 SDK

```java
ConnectionPolicy connectionPolicy = new ConnectionPolicy();
Collection<String> preferredLocations = new ArrayList<String>();
preferredLocations.add("Australia East");
connectionPolicy.setPreferredLocations(preferredLocations);
DocumentClient client = new DocumentClient(accountEndpoint, accountKey, connectionPolicy);
```

### <a id="configure-clients-multi-homing-javascript"></a>Node.js/JavaScript/TypeScript SDK

```javascript
// Set up the connection policy with your preferred regions
const connectionPolicy: ConnectionPolicy = new ConnectionPolicy();
connectionPolicy.PreferredLocations = ["West US", "Australia East"];

// Pass that connection policy to the client
const client = new CosmosClient({
  endpoint: config.endpoint,
  auth: { masterKey: config.key },
  connectionPolicy
});
```

### <a id="configure-clients-multi-homing-python"></a>Python SDK

```python
connection_policy = documents.ConnectionPolicy()
connection_policy.PreferredLocations = ['West US', 'Japan West']
client = cosmos_client.CosmosClient(self.account_endpoint, {'masterKey': self.account_key}, connection_policy)

```

## <a name="addremove-regions-from-your-database-account"></a>데이터베이스 계정에서 Azure 지역 추가/제거

### <a id="add-remove-regions-via-portal"></a>Azure Portal

1. Azure Cosmos DB 계정으로 이동하여 **전역적으로 데이터 복제** 메뉴를 엽니다.

2. Azure 지역을 추가하려면 원하는 지역에 해당하는 **"+"** 레이블을 사용하여 빈 육각형을 클릭하고, 맵에서 Azure 지역을 하나 이상 선택합니다. **+ 지역 추가** 옵션을 선택하고 드롭다운 메뉴에서 Azure 지역을 선택하여 추가할 수도 있습니다.

3. Azure 지역을 제거하려면 확인 표시가 있는 파란색 육각형을 클릭하거나 오른쪽에서 Azure 지역 옆에 있는 "휴지통"(🗑) 아이콘을 선택하고, 맵에서 Azure 지역을 하나 이상 선택 취소합니다.

4. [저장]을 클릭하여 변경 내용을 저장합니다.

   ![Azure 지역 추가/제거](./media/how-to-manage-database-account/add-region.png)

단일 지역 쓰기 모드에서는 쓰기 지역을 제거할 수 없습니다. 현재 쓰기 지역을 삭제하려면 먼저 다른 Azure 지역으로 장애 조치(failover)해야 합니다.

다중 지역 쓰기 모드에서는 Azure 지역이 하나 이상 있는 한, Azure 지역을 추가/제거할 수 있습니다.

### <a id="add-remove-regions-via-cli"></a>Azure CLI

```bash
# Given an account created with 1 region like so
az cosmosdb create --name <Azure Cosmos account name> --resource-group <Resource Group name> --locations 'eastus=0'

# Add a new region by adding another region to the list
az cosmosdb update --name <Azure Cosmos account name> --resource-group <Resource Group name> --locations 'eastus=0 westus=1'

# Remove a region by removing a region from the list
az cosmosdb update --name <Azure Cosmos account name> --resource-group <Resource Group name> --locations 'westus=0'
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

다음 JSON 코드는 Resource Manager 템플릿 예제입니다. 이 템플릿을 사용하여 일관성 정책은 제한된 부실이고, 최대 부실 간격은 5초이고, 허용되는 최대 부실 요청 수가 100개인 Azure Cosmos 계정을 배포할 수 있습니다. Resource Manager 템플릿 형식 및 구문에 대해 알아보려면 [Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) 설명서를 참조하세요.

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

1. Azure Cosmos 계정으로 이동하여 **"전역적으로 데이터 복제"** 메뉴를 엽니다.

2. 메뉴 맨 위에서 **"수동 장애 조치(failover)"** 단추를 클릭합니다.

   ![[전역적으로 데이터 복제] 메뉴](./media/how-to-manage-database-account/replicate-data-globally.png)

3. **"수동 장애 조치(failover)"** 메뉴에서 새 쓰기 지역을 선택하고, 이 옵션으로 쓰기 지역이 변경된다는 사실을 이해했음을 표시하는 상자를 선택합니다.

4. "확인"을 클릭하여 장애 조치(failover)를 트리거합니다.

   ![수동 장애 조치(failover) 포털 메뉴](./media/how-to-manage-database-account/manual-failover.png)

### <a id="enable-manual-failover-via-cli"></a>Azure CLI

```bash
# Given your account currently has regions with priority like so: 'eastus=0 westus=1'
# Change the priority order to trigger a failover of the write region
az cosmosdb update --name <Azure Cosmos account name> --resource-group <Resource Group name> --locations 'eastus=1 westus=0'
```

## <a id="automatic-failover"></a>Azure Cosmos 계정에 자동 장애 조치(failover) 사용

### <a id="enable-automatic-failover-via-portal"></a>Azure Portal

1. Azure Cosmos 계정에서 **"전역적으로 데이터 복제"** 창을 엽니다. 

2. 창 맨 위에서 **"자동 장애 조치(failover)"** 단추를 클릭합니다.

   ![[전역적으로 데이터 복제] 메뉴](./media/how-to-manage-database-account/replicate-data-globally.png)

3. **"자동 장애 조치(failover)"** 창에서 **자동 장애 조치(Failover) 사용**을 **켬**으로 설정합니다. 

4. 메뉴 아래쪽에서 [저장]을 클릭합니다.

   ![자동 장애 조치(failover) 포털 메뉴](./media/how-to-manage-database-account/automatic-failover.png)

이 메뉴에서 장애 조치(failover) 우선 순위를 설정할 수도 있습니다.

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

1. Azure Cosmos 계정에서 **"전역적으로 데이터 복제"** 창을 엽니다. 

2. 창 맨 위에서 **"자동 장애 조치(failover)"** 단추를 클릭합니다.

   ![[전역적으로 데이터 복제] 메뉴](./media/how-to-manage-database-account/replicate-data-globally.png)

3. **"자동 장애 조치(failover)"** 창에서 **자동 장애 조치(Failover) 사용**을 **켬**으로 설정합니다. 

4. 읽기 지역을 마우스로 가리키면 표시되는 행 왼쪽의 점 세 개를 통해 읽기 지역을 클릭하고 끌어서 장애 조치(failover) 우선 순위를 수정할 수 있습니다. 

5. 메뉴 아래쪽에서 [저장]을 클릭합니다.

   ![자동 장애 조치(failover) 포털 메뉴](./media/how-to-manage-database-account/automatic-failover.png)

이 메뉴에서는 쓰기 지역을 수정할 수 없습니다. 쓰기 지역을 수동으로 변경하려면 수동 장애 조치(failover)를 수행해야 합니다.

### <a id="set-failover-priorities-via-cli"></a>Azure CLI

```bash
az cosmosdb failover-priority-change --name <Azure Cosmos account name> --resource-group <Resource Group name> --failover-policies 'eastus=0 westus=2 southcentralus=1'
```

## <a name="next-steps"></a>다음 단계

다음 문서를 사용하여 Azure Cosmos DB에서 일관성 수준 및 데이터 충돌을 관리하는 방법을 자세히 알아볼 수 있습니다.

* [일관성을 관리하는 방법](how-to-manage-consistency.md)
* [Azure 지역 간 충돌을 관리하는 방법](how-to-manage-conflicts.md)

