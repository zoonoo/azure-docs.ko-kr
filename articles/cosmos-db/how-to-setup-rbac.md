---
title: Azure AD를 사용 하 여 Azure Cosmos DB 계정에 대 한 역할 기반 액세스 제어 구성
description: Azure Cosmos DB 계정에 대 한 Azure Active Directory를 사용 하 여 역할 기반 액세스 제어를 구성 하는 방법을 알아봅니다.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: how-to
ms.date: 03/02/2021
ms.author: thweiss
ms.openlocfilehash: d83109f380a3044073cf2dd8d10f29027ebb9f41
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101690909"
---
# <a name="configure-role-based-access-control-with-azure-active-directory-for-your-azure-cosmos-db-account-preview"></a>Azure Cosmos DB 계정 (미리 보기)에 대 한 Azure Active Directory를 사용 하 여 역할 기반 액세스 제어 구성
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!IMPORTANT]
> Azure Cosmos DB 역할 기반 액세스 제어는 현재 미리 보기로 제공 됩니다. 이 미리 보기 버전은 Service Level Agreement(서비스 수준 약정) 없이 제공 되며 프로덕션 워크 로드에는 권장 되지 않습니다. 자세한 내용은 Microsoft Azure Preview에 대한 [추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

> [!NOTE]
> 이 문서에서는 Azure Cosmos DB의 데이터 평면 작업에 대 한 역할 기반 액세스 제어에 대해 설명 합니다. 관리 평면 작업을 사용 하는 경우 관리 평면 작업에 적용 되는 [역할 기반 액세스 제어](role-based-access-control.md) 문서를 참조 하세요.

Azure Cosmos DB는 다음을 수행할 수 있는 기본 제공 RBAC (역할 기반 액세스 제어) 시스템을 노출 합니다.

- Azure Active Directory (Azure AD) id를 사용 하 여 데이터 요청을 인증 합니다.
- 세분화 된 역할 기반 권한 모델을 사용 하 여 데이터 요청에 권한을 부여 합니다.

## <a name="concepts"></a>개념

Azure Cosmos DB 데이터 평면 RBAC는 [AZURE rbac](../role-based-access-control/overview.md)와 같은 다른 RBAC 시스템에서 일반적으로 발견 되는 개념을 기반으로 합니다.

- [권한 모델](#permission-model) 은 일련의 **동작** 으로 구성 됩니다. 이러한 각 작업은 하나 또는 여러 데이터베이스 작업에 매핑됩니다. 작업의 몇 가지 예로는 항목 읽기, 항목 쓰기 또는 쿼리 실행이 있습니다.
- Azure Cosmos DB 사용자는 허용 되는 작업 목록을 포함 하는 **[역할 정의](#role-definitions)** 를 만듭니다.
- 역할 정의는 **[역할 할당](#role-assignments)** 을 통해 특정 Azure AD id에 할당 됩니다. 역할 할당은 또한 역할 정의가 적용 되는 범위를 정의 합니다. 현재 세 범위는 다음과 같습니다.
    - Azure Cosmos DB 계정,
    - Azure Cosmos DB 데이터베이스,
    - Azure Cosmos DB 컨테이너입니다.

  :::image type="content" source="./media/how-to-setup-rbac/concepts.png" alt-text="RBAC 개념":::

> [!NOTE]
> Azure Cosmos DB RBAC는 현재 기본 제공 역할 정의를 노출 하지 않습니다.

## <a name="permission-model"></a><a id="permission-model"></a> 권한 모델

다음 표에서는 권한 모델에 의해 노출 되는 모든 작업을 나열 합니다.

| 속성 | 해당 데이터베이스 작업 |
|---|---|
| `Microsoft.DocumentDB/databaseAccounts/readMetadata` | 계정 메타 데이터를 읽습니다. 자세한 내용은 [메타 데이터 요청](#metadata-requests) 을 참조 하세요. |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/create` | 새 항목을 만듭니다. |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/read` | 해당 ID 및 파티션 키 (지점 읽기)를 기준으로 개별 항목을 읽습니다. |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/replace` | 기존 항목을 바꿉니다. |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/upsert` | 항목을 "Upsert" 합니다. 즉, 존재 하지 않는 경우 생성 하거나, 존재 하는 경우 대체 합니다. |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/delete` | 항목을 삭제 합니다. |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/executeQuery` | [SQL 쿼리](sql-query-getting-started.md)를 실행 합니다. |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/readChangeFeed` | 컨테이너의 [변경 피드에서](read-change-feed.md)읽습니다. |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/executeStoredProcedure` | [저장 프로시저](stored-procedures-triggers-udfs.md)를 실행 합니다. |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/manageConflicts` | 다중 쓰기 지역 계정에 대 한 [충돌](conflict-resolution-policies.md) 을 관리 합니다. 즉, 충돌 피드의 항목을 나열 하 고 삭제 합니다. |

와일드 카드는 *컨테이너* 와 *항목* 수준 모두에서 지원 됩니다.

- `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/*`
- `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/*`

> [!IMPORTANT]
> 이 권한 모델은 데이터를 읽고 쓸 수 있는 데이터베이스 작업만 다룹니다. 컨테이너를 만들거나 처리량을 변경 하는 것과 같은 종류의 관리 작업 **에 대해서는 다루지 않습니다.** AAD id를 사용 하 여 관리 작업을 인증 하려면 대신 [AZURE RBAC](role-based-access-control.md) 를 사용 합니다.

### <a name="metadata-requests"></a><a id="metadata-requests"></a> 메타 데이터 요청

Azure Cosmos DB Sdk를 사용 하는 경우 이러한 Sdk는 초기화 하는 동안 읽기 전용 메타 데이터 요청을 발급 하 고 특정 데이터 요청을 처리 합니다. 이러한 메타 데이터 요청은 다음과 같은 다양 한 구성 세부 정보를 가져옵니다. 

- 계정에 사용할 수 있는 Azure 지역을 포함 하는 계정의 전역 구성입니다.
- 컨테이너의 파티션 키 또는 해당 인덱싱 정책입니다.
- 컨테이너와 해당 주소를 만드는 실제 파티션 목록입니다.

계정에 저장 한 데이터를 인출 *하지* 않습니다.

권한 모델의 최상의 투명성을 보장 하기 위해 이러한 메타 데이터 요청은 작업에 의해 명시적으로 적용 됩니다 `Microsoft.DocumentDB/databaseAccounts/readMetadata` . Azure Cosmos DB Sdk 중 하나를 통해 Azure Cosmos DB 계정에 액세스 하는 모든 상황에서이 작업을 수행할 수 있어야 합니다. 역할 할당을 통해 Azure Cosmos DB 계층 구조 (즉, 계정, 데이터베이스 또는 컨테이너)의 모든 수준에서 할당 될 수 있습니다.

작업에서 허용 되는 실제 메타 데이터 요청은 `Microsoft.DocumentDB/databaseAccounts/readMetadata` 작업이 할당 된 범위에 따라 다릅니다.

| Scope | 작업에서 허용 되는 요청 |
|---|---|
| 계정 | -계정으로 데이터베이스 나열<br>-계정의 각 데이터베이스에 대해 데이터베이스 범위에서 허용 되는 작업 |
| 데이터베이스 | -데이터베이스 메타 데이터 읽기<br>-데이터베이스 아래 컨테이너를 나열 합니다.<br>-데이터베이스 아래의 각 컨테이너에 대해 컨테이너 범위에서 허용 되는 작업 |
| 컨테이너 | -컨테이너 메타 데이터 읽기<br>-컨테이너 아래에 실제 파티션을 나열 합니다.<br>-각 실제 파티션의 주소를 확인 하는 중 |

## <a name="create-role-definitions"></a><a id="role-definitions"></a> 역할 정의 만들기

역할 정의를 만들 때 다음을 제공 해야 합니다.

- Azure Cosmos DB 계정의 이름입니다.
- 계정을 포함 하는 리소스 그룹입니다.
- 역할 정의의 유형입니다. `CustomRole` 현재만 지원 됩니다.
- 역할 정의의 이름입니다.
- 역할에서 허용 하도록 할 [작업](#permission-model) 목록입니다.
- 역할 정의를 할당할 수 있는 하나 또는 여러 범위 지원 되는 범위는 다음과 같습니다.
    - `/` (계정 수준),
    - `/dbs/<database-name>` (데이터베이스 수준),
    - `/dbs/<database-name>/colls/<container-name>` (컨테이너 수준).

> [!NOTE]
> 아래에서 설명 하는 작업은 현재에서 사용할 수 있습니다.
> - Azure PowerShell: [Az. CosmosDB version 2.0.1-preview](https://www.powershellgallery.com/packages/Az.CosmosDB/2.0.1-preview)
> - Azure CLI: [' cosmosdb ' 확장 버전 0.4.0](https://github.com/Azure/azure-cli-extensions/tree/master/src/cosmosdb-preview)

### <a name="using-azure-powershell"></a>Azure PowerShell 사용

읽기 동작만 포함 하는 *MyReadOnlyRole* 라는 역할을 만듭니다.

```powershell
$resourceGroupName = "<myResourceGroup>"
$accountName = "<myCosmosAccount>"
New-AzCosmosDBSqlRoleDefinition -AccountName $accountName `
    -ResourceGroupName $resourceGroupName `
    -Type CustomRole -RoleName MyReadOnlyRole `
    -DataAction @( `
        'Microsoft.DocumentDB/databaseAccounts/readMetadata',
        'Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/read', `
        'Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/executeQuery', `
        'Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/readChangeFeed') `
    -AssignableScope "/"
```

모든 동작을 포함 하는 *Myreadwriterole* 라는 역할을 만듭니다.

```powershell
New-AzCosmosDBSqlRoleDefinition -AccountName $accountName `
    -ResourceGroupName $resourceGroupName `
    -Type CustomRole -RoleName MyReadWriteRole `
    -DataAction @( `
        'Microsoft.DocumentDB/databaseAccounts/readMetadata',
        'Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/*', `
        'Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/*') `
    -AssignableScope "/"
```

Id를 가져오기 위해 만든 역할 정의를 나열 합니다.

```powershell
Get-AzCosmosDBSqlRoleDefinition -AccountName $accountName `
    -ResourceGroupName $resourceGroupName
```

```
RoleName         : MyReadWriteRole
Id               : /subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDB/databaseAcc
                   ounts/<myCosmosAccount>/sqlRoleDefinitions/<roleDefinitionId>
Type             : CustomRole
Permissions      : {Microsoft.Azure.Management.CosmosDB.Models.Permission}
AssignableScopes : {/subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDB/databaseAc
                   counts/<myCosmosAccount>}

RoleName         : MyReadOnlyRole
Id               : /subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDB/databaseAcc
                   ounts/<myCosmosAccount>/sqlRoleDefinitions/<roleDefinitionId>
Type             : CustomRole
Permissions      : {Microsoft.Azure.Management.CosmosDB.Models.Permission}
AssignableScopes : {/subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDB/databaseAc
                   counts/<myCosmosAccount>}
```

### <a name="using-the-azure-cli"></a>Azure CLI 사용

읽기 동작만 포함 하는 *MyReadOnlyRole* 라는 역할을 만듭니다.

```json
// role-definition-ro.json
{
    "RoleName": "MyReadOnlyRole",
    "Type": "CustomRole",
    "AssignableScopes": ["/"],
    "Permissions": [{
        "DataActions": [
            "Microsoft.DocumentDB/databaseAccounts/readMetadata",
            "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/read",
            "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/executeQuery",
            "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/readChangeFeed"
        ]
    }]
}
```

```azurecli
resourceGroupName='<myResourceGroup>'
accountName='<myCosmosAccount>'
az cosmosdb sql role definition create --account-name $accountName --resource-group $resourceGroupName --body @role-definition-ro.json
```

모든 동작을 포함 하는 *Myreadwriterole* 라는 역할을 만듭니다.

```json
// role-definition-rw.json
{
    "RoleName": "MyReadWriteRole",
    "Type": "CustomRole",
    "AssignableScopes": ["/"],
    "Permissions": [{
        "DataActions": [
            "Microsoft.DocumentDB/databaseAccounts/readMetadata",
            "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/*",
            "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/*"
        ]
    }]
}
```

```azurecli
az cosmosdb sql role definition create --account-name $accountName --resource-group $resourceGroupName --body @role-definition-rw.json
```

Id를 가져오기 위해 만든 역할 정의를 나열 합니다.

```azurecli
az cosmosdb sql role definition list --account-name $accountName --resource-group $resourceGroupName
```

```
[
  {
    "assignableScopes": [
      "/subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDB/databaseAccounts/<myCosmosAccount>"
    ],
    "id": "/subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDB/databaseAccounts/<myCosmosAccount>/sqlRoleDefinitions/<roleDefinitionId>",
    "name": "<roleDefinitionId>",
    "permissions": [
      {
        "dataActions": [
          "Microsoft.DocumentDB/databaseAccounts/readMetadata",
          "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/*",
          "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/*"
        ],
        "notDataActions": []
      }
    ],
    "resourceGroup": "<myResourceGroup>",
    "roleName": "MyReadWriteRole",
    "sqlRoleDefinitionGetResultsType": "CustomRole",
    "type": "Microsoft.DocumentDB/databaseAccounts/sqlRoleDefinitions"
  },
  {
    "assignableScopes": [
      "/subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDB/databaseAccounts/<myCosmosAccount>"
    ],
    "id": "/subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDB/databaseAccounts/<myCosmosAccount>/sqlRoleDefinitions/<roleDefinitionId>",
    "name": "<roleDefinitionId>",
    "permissions": [
      {
        "dataActions": [
          "Microsoft.DocumentDB/databaseAccounts/readMetadata",
          "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/read",
          "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/executeQuery",
          "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/readChangeFeed"
        ],
        "notDataActions": []
      }
    ],
    "resourceGroup": "<myResourceGroup>",
    "roleName": "MyReadOnlyRole",
    "sqlRoleDefinitionGetResultsType": "CustomRole",
    "type": "Microsoft.DocumentDB/databaseAccounts/sqlRoleDefinitions"
  }
]
```

## <a name="create-role-assignments"></a><a id="role-assignments"></a> 역할 할당 만들기

역할 정의를 만든 후에는 AAD id와 연결할 수 있습니다. 역할 할당을 만들 때 다음을 제공 해야 합니다.

- Azure Cosmos DB 계정의 이름입니다.
- 계정을 포함 하는 리소스 그룹입니다.
- 할당할 역할 정의의 ID입니다.
- 역할 정의를 할당 해야 하는 id의 보안 주체 ID입니다.
- 역할 할당의 범위입니다. 지원 되는 범위는 다음과 같습니다.
    - `/` (계정 수준)
    - `/dbs/<database-name>` (데이터베이스 수준)
    - `/dbs/<database-name>/colls/<container-name>` (컨테이너 수준)

  범위는 역할 정의의 할당 가능한 범위 중 하나의 하위 범위 이거나 일치 해야 합니다.

> [!NOTE]
> 서비스 사용자에 대 한 역할 할당을 만들려면 **Azure Active Directory** 포털 블레이드의 **엔터프라이즈 응용 프로그램** 섹션에서 찾을 수 있도록 해당 **개체 ID** 를 사용 해야 합니다.

> [!NOTE]
> 아래에서 설명 하는 작업은 현재에서 사용할 수 있습니다.
> - Azure PowerShell: [Az. CosmosDB version 2.0.1-preview](https://www.powershellgallery.com/packages/Az.CosmosDB/2.0.1-preview)
> - Azure CLI: [' cosmosdb ' 확장 버전 0.4.0](https://github.com/Azure/azure-cli-extensions/tree/master/src/cosmosdb-preview)

### <a name="using-azure-powershell"></a>Azure PowerShell 사용

Id에 역할을 할당 합니다.

```powershell
$resourceGroupName = "<myResourceGroup>"
$accountName = "<myCosmosAccount>"
$readOnlyRoleDefinitionId = "<roleDefinitionId>" // as fetched above
$principalId = "<aadPrincipalId>"
New-AzCosmosDBSqlRoleAssignment -AccountName $accountName `
    -ResourceGroupName $resourceGroupName `
    -RoleDefinitionId $readOnlyRoleDefinitionId `
    -Scope $accountName `
    -PrincipalId $principalId
```

### <a name="using-the-azure-cli"></a>Azure CLI 사용

Id에 역할을 할당 합니다.

```azurecli
resourceGroupName='<myResourceGroup>'
accountName='<myCosmosAccount>'
readOnlyRoleDefinitionId = '<roleDefinitionId>' // as fetched above
principalId = '<aadPrincipalId>'
az cosmosdb sql role assignment create --account-name $accountName --resource-group --scope "/" --principalId $principalId --role-definition-id $readOnlyRoleDefinitionId
```

## <a name="initialize-the-sdk-with-azure-ad"></a>Azure AD를 사용 하 여 SDK 초기화

응용 프로그램에서 Azure Cosmos DB RBAC를 사용 하려면 Azure Cosmos DB SDK를 초기화 하는 방법을 업데이트 해야 합니다. 계정의 기본 키를 전달 하는 대신 클래스의 인스턴스를 전달 해야 `TokenCredential` 합니다. 이 인스턴스는 사용 하려는 id를 대신 하 여 AAD 토큰을 가져오는 데 필요한 컨텍스트를 Azure Cosmos DB SDK에 제공 합니다.

인스턴스를 만드는 방법은 `TokenCredential` 이 문서의 범위를 벗어나는 것입니다. 사용 하려는 AAD id의 형식 (사용자 계정, 서비스 주체, 그룹 등)에 따라 이러한 인스턴스를 만들 수 있는 여러 가지 방법이 있습니다. 가장 중요 한 점은 사용자의 `TokenCredential` 인스턴스가 사용자가 역할을 할당 한 id (보안 주체 id)로 확인 되어야 한다는 것입니다. 클래스 만들기에 대 한 예제를 찾을 수 있습니다 `TokenCredential` .

- [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/identity-readme#credential-classes)
- [Java에서](https://docs.microsoft.com/java/api/overview/azure/identity-readme#credential-classes)

아래 예제에서는 인스턴스에 서비스 주체를 사용 `ClientSecretCredential` 합니다.

### <a name="in-net"></a>.NET

> [!NOTE]
> `preview`이 기능에 액세스 하려면 Azure Cosmos DB .NET SDK 버전을 사용 해야 합니다.

```csharp
TokenCredential servicePrincipal = new ClientSecretCredential(
    "<azure-ad-tenant-id>",
    "<client-application-id>",
    "<client-application-secret>");
CosmosClient client = new CosmosClient("<account-endpoint>", servicePrincipal);
```

### <a name="in-java"></a>Java에서

```java
TokenCredential ServicePrincipal = new ClientSecretCredentialBuilder()
    .authorityHost("https://login.microsoftonline.com")
    .tenantId("<azure-ad-tenant-id>")
    .clientId("<client-application-id>")
    .clientSecret("<client-application-secret>")
    .build();
CosmosAsyncClient Client = new CosmosClientBuilder()
    .endpoint("<account-endpoint>")
    .credential(ServicePrincipal)
    .build();
```

## <a name="auditing-data-requests"></a>데이터 요청 감사

Azure Cosmos DB RBAC를 사용 하는 경우 [진단 로그](cosmosdb-monitor-resource-logs.md) 는 각 데이터 작업에 대 한 id 및 권한 부여 정보를 사용 하 여 확대 됩니다. 이를 통해 자세한 감사를 수행 하 고 Azure Cosmos DB 계정으로 전송 되는 모든 데이터 요청에 사용 되는 AAD id를 검색할 수 있습니다.

이 추가 정보는 **DataPlaneRequests** 로그 범주에 전달 되며 다음 두 개의 추가 열로 구성 됩니다.

- `aadPrincipalId_g` 요청을 인증 하는 데 사용 된 AAD id의 보안 주체 ID를 표시 합니다.
- `aadAppliedRoleAssignmentId_g` 요청을 인증할 때 허용 된 [역할 할당](#role-assignments) 을 표시 합니다.

## <a name="limits"></a>제한

- Azure Cosmos DB 계정 마다 최대 100 개의 역할 정의 및 2000 역할 할당을 만들 수 있습니다.
- Azure AD 그룹 확인은 현재 200 개 이상의 그룹에 속한 id에 대해 지원 되지 않습니다.
- Azure AD 토큰은 현재 Azure Cosmos DB 서비스로 전송 되는 각각의 개별 요청이 포함 된 헤더로 전달 되어 전체 페이로드 크기를 늘립니다.

## <a name="frequently-asked-questions"></a>질문과 대답

### <a name="which-azure-cosmos-db-apis-are-supported-by-rbac"></a>RBAC에서 지원 되는 Azure Cosmos DB Api는 무엇입니까?

SQL API만 현재 지원 됩니다.

### <a name="is-it-possible-to-manage-role-definitions-and-role-assignments-from-the-azure-portal"></a>Azure Portal에서 역할 정의 및 역할 할당을 관리할 수 있나요?

역할 관리에 대 한 Azure Portal 지원은 아직 사용할 수 없습니다.

### <a name="which-sdks-in-azure-cosmos-db-sql-api-support-rbac"></a>Azure Cosmos DB SQL API에서 RBAC를 지 원하는 Sdk는 무엇입니까?

[.Net V3](sql-api-sdk-dotnet-standard.md) 및 [Java V4](sql-api-sdk-java-v4.md) sdk는 현재 지원 됩니다.

### <a name="is-the-azure-ad-token-automatically-refreshed-by-the-azure-cosmos-db-sdks-when-it-expires"></a>Azure AD 토큰이 만료 될 때 Azure Cosmos DB Sdk에 의해 자동으로 새로 고쳐지도록 하나요?

예.

### <a name="is-it-possible-to-disable-the-usage-of-the-account-primary-key-when-using-rbac"></a>RBAC를 사용할 때 계정 기본 키를 사용 하지 않도록 설정할 수 있나요?

현재 계정 기본 키를 사용 하지 않도록 설정할 수 없습니다.

## <a name="next-steps"></a>다음 단계

- [Cosmos DB의 데이터에 대한 보안 액세스](secure-access-to-data.md) 개요를 살펴봅니다.
- [Azure Cosmos DB 관리를 위한 RBAC](role-based-access-control.md)에 대해 자세히 알아보세요.
