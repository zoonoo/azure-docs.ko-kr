---
title: Azure Cosmos DB 계정에 대한 고객 관리형 키 구성
description: Azure Key Vault를 사용하여 Azure Cosmos DB 계정에 대한 고객 관리형 키를 구성하는 방법 알아보기
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: how-to
ms.date: 08/05/2020
ms.author: thweiss
ms.openlocfilehash: 21bb594f4e374d41cfc4184f3a72aea1717c85d8
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93086145"
---
# <a name="configure-customer-managed-keys-for-your-azure-cosmos-account-with-azure-key-vault"></a>Azure Key Vault를 사용하여 Azure Cosmos 계정에 대한 고객 관리형 키 구성
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos 계정에 저장된 데이터는 Microsoft 에서 관리하는 키( **서비스 관리형 키** )를 통해 원활하게 자동 암호화됩니다. 필요에 따라 직접 관리하는 키( **고객 관리형 키** )를 사용하여 두 번째 암호화 계층을 추가하도록 선택할 수 있습니다.

:::image type="content" source="./media/how-to-setup-cmk/cmk-intro.png" alt-text="고객 데이터 주위의 암호화 계층":::

고객 관리형 키를 [Azure Key Vault](../key-vault/general/overview.md)에 저장하고 고객 관리형 키를 사용하도록 설정된 각 Azure Cosmos 계정에 키를 제공해야 합니다. 이 키는 해당 계정에 저장된 모든 데이터를 암호화하는 데 사용됩니다.

> [!NOTE]
> 현재 고객 관리형 키는 새 Azure Cosmos 계정에만 사용할 수 있으며, 계정을 만드는 동안 구성해야 합니다.

## <a name="register-the-azure-cosmos-db-resource-provider-for-your-azure-subscription"></a><a id="register-resource-provider"></a> Azure 구독에 대한 Azure Cosmos DB 리소스 공급자 등록

1. [Azure Portal](https://portal.azure.com/)에 로그인하고 Azure 구독으로 이동하여 **설정** 탭에서 **리소스 공급자** 를 선택합니다.

   :::image type="content" source="./media/how-to-setup-cmk/portal-rp.png" alt-text="고객 데이터 주위의 암호화 계층":::

1. **Microsoft.DocumentDB** 리소스 공급자를 검색합니다. 리소스 공급자가 이미 등록된 것으로 표시되어 있는지 확인합니다. 그렇지 않으면 리소스 공급자를 선택하고 **등록** 을 선택합니다.

   :::image type="content" source="./media/how-to-setup-cmk/portal-rp-register.png" alt-text="고객 데이터 주위의 암호화 계층":::

## <a name="configure-your-azure-key-vault-instance"></a>Azure Key Vault 인스턴스 구성

Azure Cosmos DB에 고객 관리형 키를 사용하려면 암호화 키를 호스팅하는 데 사용할 Azure Key Vault 인스턴스에 **일시 삭제** 및 **제거 보호** 라는 두 가지 속성을 설정해야 합니다.

새 Azure Key Vault 인스턴스를 만드는 경우 생성 중에 이러한 속성을 사용하도록 설정합니다.

:::image type="content" source="./media/how-to-setup-cmk/portal-akv-prop.png" alt-text="고객 데이터 주위의 암호화 계층" 섹션을 참조하세요.

- [PowerShell에서 일시 삭제를 사용하는 방법](../key-vault/general/soft-delete-powershell.md)
- [Azure CLI에서 일시 삭제를 사용하는 방법](../key-vault/general/soft-delete-cli.md)

## <a name="add-an-access-policy-to-your-azure-key-vault-instance"></a>Azure Key Vault 인스턴스에 액세스 정책 추가

1. Azure Portal에서 암호화 키를 호스트하는 데 사용할 Azure Key Vault 인스턴스로 이동합니다. 왼쪽 메뉴에서 **액세스 정책** 을 선택합니다.

   :::image type="content" source="./media/how-to-setup-cmk/portal-akv-ap.png" alt-text="고객 데이터 주위의 암호화 계층":::

1. **+ 액세스 정책 추가** 를 선택합니다.

1. **키 권한** 드롭다운 메뉴에서 **가져오기** , **키 래핑 해제** , **키 래핑** 권한을 선택합니다.

   :::image type="content" source="./media/how-to-setup-cmk/portal-akv-add-ap-perm2.png" alt-text="고객 데이터 주위의 암호화 계층":::

1. **주체 선택** 에서 **선택된 항목 없음** 을 선택합니다. 그런 다음, **Azure Cosmos DB** 보안 주체를 검색하여 선택합니다. (쉽게 찾을 수 있도록 보안 주체 ID가 `57506a73-e302-42a9-b869-6f12d9ec29e9`인 Azure Government 지역을 제외하고 보안 주체 ID: `a232010e-820c-4083-83bb-3ace5fc29d0b`로 검색할 수도 있습니다.) 마지막으로 아래쪽에서 **선택** 을 선택합니다. **Azure Cosmos DB** 보안 주체가 목록에 없으면, 이 문서에서 [리소스 공급자 등록](#register-resource-provider) 섹션의 설명을 참고하여 **Microsoft.DocumentDB** 리소스 공급자를 다시 등록해야 할 수도 있습니다.

   :::image type="content" source="./media/how-to-setup-cmk/portal-akv-add-ap.png" alt-text="고객 데이터 주위의 암호화 계층":::

1. **추가** 를 선택하여 새로운 액세스 정책을 추가합니다.

1. Key Vault 인스턴스에서 **저장** 을 선택 하 여 모든 변경 내용을 저장 합니다.

## <a name="generate-a-key-in-azure-key-vault"></a>Azure Key Vault에서 새 키 생성

1. Azure Portal에서 암호화 키를 호스트하는 데 사용할 Azure Key Vault 인스턴스로 이동합니다. 그런 다음, 왼쪽 메뉴에서 **키** 를 선택합니다.

   :::image type="content" source="./media/how-to-setup-cmk/portal-akv-keys.png" alt-text="고객 데이터 주위의 암호화 계층":::

1. **생성/가져오기** 를 선택하고 새 키의 이름을 제공하고 RSA 키 크기를 선택합니다. 최상의 보안을 위해 3072 이상을 사용하는 것이 좋습니다. **만들기** 를 선택합니다.

   :::image type="content" source="./media/how-to-setup-cmk/portal-akv-gen.png" alt-text="고객 데이터 주위의 암호화 계층":::

1. 키가 생성되면 새로 생성된 키를 선택한 다음, 현재 버전을 선택합니다.

1. 마지막 슬래시 뒷 부분을 제외하고 키의 **키 식별자** 를 복사합니다.

   :::image type="content" source="./media/how-to-setup-cmk/portal-akv-keyid.png" alt-text="고객 데이터 주위의 암호화 계층":::

## <a name="create-a-new-azure-cosmos-account"></a>새 Azure Cosmos 계정 만들기

### <a name="using-the-azure-portal"></a>Azure Portal 사용

Azure Portal에서 새 Azure Cosmos DB 계정을 생성할 때 **암호화** 단계에서 **고객 관리형 키** 를 선택합니다. **키 URI** 필드에 이전 단계에서 복사한 Azure Key Vault 키의 URI/키 식별자를 붙여넣습니다.

:::image type="content" source="./media/how-to-setup-cmk/portal-cosmos-enc.png" alt-text="고객 데이터 주위의 암호화 계층":::

### <a name="using-azure-powershell"></a><a id="using-powershell"></a> Azure PowerShell 사용

PowerShell을 사용하여 새 Azure Cosmos DB 계정을 만드는 경우:

- 앞서 **PropertyObject** 의 **keyVaultKeyUri** 속성 아래에서 복사한 Azure Key Vault 키의 URI를 전달합니다.

- **2019-12-12** 이상의 API 버전을 사용합니다.

> [!IMPORTANT]
> 고객 관리형 키를 사용하여 계정을 만들려면 `locations` 속성을 명시적으로 설정해야 합니다.

```powershell
$resourceGroupName = "myResourceGroup"
$accountLocation = "West US 2"
$accountName = "mycosmosaccount"

$failoverLocations = @(
    @{ "locationName"="West US 2"; "failoverPriority"=0 }
)

$CosmosDBProperties = @{
    "databaseAccountOfferType"="Standard";
    "locations"=$failoverLocations;
    "keyVaultKeyUri" = "https://<my-vault>.vault.azure.net/keys/<my-key>";
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2019-12-12" -ResourceGroupName $resourceGroupName `
    -Location $accountLocation -Name $accountName -PropertyObject $CosmosDBProperties
```

계정이 생성되면 Azure Key Vault 키의 URI를 가져와서 고객 관리형 키를 사용하도록 설정되었는지 확인할 수 있습니다.

```powershell
Get-AzResource -ResourceGroupName $resourceGroupName -Name $accountName `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    | Select-Object -ExpandProperty Properties `
    | Select-Object -ExpandProperty keyVaultKeyUri
```

### <a name="using-an-azure-resource-manager-template"></a>Azure Resource Manager 템플릿 사용

Azure Resource Manager 템플릿을 통해 새 Azure Cosmos 계정을 만드는 경우:

- 앞서 **properties** 개체의 **keyVaultKeyUri** 속성 아래에서 복사한 Azure Key Vault 키의 URI를 전달합니다.

- **2019-12-12** 이상의 API 버전을 사용합니다.

> [!IMPORTANT]
> 고객 관리형 키를 사용하여 계정을 만들려면 `locations` 속성을 명시적으로 설정해야 합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "accountName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "keyVaultKeyUri": {
            "type": "string"
        }
    },
    "resources": 
    [
        {
            "type": "Microsoft.DocumentDB/databaseAccounts",
            "name": "[parameters('accountName')]",
            "apiVersion": "2019-12-12",
            "kind": "GlobalDocumentDB",
            "location": "[parameters('location')]",
            "properties": {
                "locations": [ 
                    {
                        "locationName": "[parameters('location')]",
                        "failoverPriority": 0,
                        "isZoneRedundant": false
                    }
                ],
                "databaseAccountOfferType": "Standard",
                "keyVaultKeyUri": "[parameters('keyVaultKeyUri')]"
            }
        }
    ]
}
```

다음 PowerShell 스크립트를 사용하여 템플릿을 배포합니다.

```powershell
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$accountLocation = "West US 2"
$keyVaultKeyUri = "https://<my-vault>.vault.azure.net/keys/<my-key>"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile "deploy.json" `
    -accountName $accountName `
    -location $accountLocation `
    -keyVaultKeyUri $keyVaultKeyUri
```

### <a name="using-the-azure-cli"></a><a id="using-azure-cli"></a> Azure CLI 사용

Azure CLI를 통해 새 Azure Cosmos 계정을 만드는 경우 앞서 `--key-uri` 매개 변수 아래에서 복사한 Azure Key Vault 키의 URI를 전달합니다.

```azurecli-interactive
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'
keyVaultKeyUri = 'https://<my-vault>.vault.azure.net/keys/<my-key>'

az cosmosdb create \
    -n $accountName \
    -g $resourceGroupName \
    --locations regionName='West US 2' failoverPriority=0 isZoneRedundant=False \
    --key-uri $keyVaultKeyUri
```

계정이 생성되면 Azure Key Vault 키의 URI를 가져와서 고객 관리형 키를 사용하도록 설정되었는지 확인할 수 있습니다.

```azurecli-interactive
az cosmosdb show \
    -n $accountName \
    -g $resourceGroupName \
    --query keyVaultKeyUri
```

## <a name="key-rotation"></a>키 회전

Azure Cosmos 계정에서 사용 하는 고객 관리 키 회전은 두 가지 방법으로 수행할 수 있습니다.

- Azure Key Vault에서 현재 사용 되는 키의 새 버전을 만듭니다.

  :::image type="content" source="./media/how-to-setup-cmk/portal-akv-rot.png" alt-text="고객 데이터 주위의 암호화 계층":::

- 계정에 대 한 키 URI를 업데이트 하 여 현재 사용 되는 키를 완전히 다른 키로 바꿉니다. Azure Portal에서 Azure Cosmos 계정으로 이동 하 고 왼쪽 메뉴에서 **데이터 암호화** 를 선택 합니다.

    :::image type="content" source="./media/how-to-setup-cmk/portal-data-encryption.png" alt-text="고객 데이터 주위의 암호화 계층":::

    그런 다음 **키 URI** 를 사용 하려는 새 키로 바꾸고 **저장** 을 선택 합니다.

    :::image type="content" source="./media/how-to-setup-cmk/portal-key-swap.png" alt-text="고객 데이터 주위의 암호화 계층":::

    PowerShell에서 동일한 결과를 얻기 위해 수행 하는 방법은 다음과 같습니다.

    ```powershell
    $resourceGroupName = "myResourceGroup"
    $accountName = "mycosmosaccount"
    $newKeyUri = "https://<my-vault>.vault.azure.net/keys/<my-new-key>"
    
    $account = Get-AzResource -ResourceGroupName $resourceGroupName -Name $accountName `
        -ResourceType "Microsoft.DocumentDb/databaseAccounts"
    
    $account.Properties.keyVaultKeyUri = $newKeyUri
    
    $account | Set-AzResource -Force
    ```

이전 키 또는 키 버전은 24 시간 후에 사용 하지 않도록 설정할 수 있으며, [Azure Key Vault 감사 로그](../key-vault/general/logging.md) 에는 더 이상 해당 키 또는 키 버전에서 Azure Cosmos DB의 활동이 표시 되지 않습니다.
    
## <a name="error-handling"></a>오류 처리

Azure Cosmos DB에서 CMK (Customer-Managed 키)를 사용 하는 경우 오류가 발생 하면 Azure Cosmos DB 응답에 HTTP 하위 상태 코드와 함께 오류 정보를 반환 합니다. 이 하위 상태 코드를 사용 하 여 문제의 근본 원인을 디버그할 수 있습니다. 지원 되는 HTTP 하위 상태 코드 목록을 가져오려면 [Azure Cosmos DB에 대 한 Http 상태 코드](/rest/api/cosmos-db/http-status-codes-for-cosmosdb) 문서를 참조 하세요.

## <a name="frequently-asked-questions"></a>질문과 대답

### <a name="is-there-an-additional-charge-to-enable-customer-managed-keys"></a>고객 관리형 키를 사용하도록 설정하는 데 추가 요금이 있나요?

아니요,이 기능을 사용하도록 설정하는 데 청구되는 요금은 없습니다.

### <a name="how-do-customer-managed-keys-impact-capacity-planning"></a>고객 관리형 키는 용량 계획에 어떤 영향을 주나요?

고객 관리형 키를 사용하는 경우 데이터의 암호화 및 암호 해독을 수행하는 데 필요한 추가 처리를 반영하여 데이터베이스 작업에 소비된 [요청 단위](./request-units.md)가 증가합니다. 이로 인해 프로비저닝된 용량의 사용률이 약간 높아질 수 있습니다. 아래 표를 참조하세요.

| 작업 유형 | 요청 단위 증가 |
|---|---|
| 포인트 읽기(ID별 항목 가져오기) | 작업당 +5% |
| 모든 쓰기 작업 | 작업당 +6%<br/>인덱싱된 속성당 +0.06RU 정도 |
| 쿼리, 변경 피드 읽기 또는 충돌 피드 | 작업당 +15% |

### <a name="what-data-gets-encrypted-with-the-customer-managed-keys"></a>고객 관리형 키로 어떤 데이터가 암호화되나요?

Azure Cosmos 계정에 저장된 모든 데이터는 고객 관리형 키로 암호화되며 단, 다음 메타데이터는 제외됩니다.

- Azure Cosmos DB [계정, 데이터베이스 및 컨테이너](./account-databases-containers-items.md#elements-in-an-azure-cosmos-account)의 이름

- [저장 프로시저](./stored-procedures-triggers-udfs.md)의 이름

- [인덱싱 정책](./index-policy.md)에 선언된 속성 경로

- 컨테이너의 [파티션 키](./partitioning-overview.md) 값

### <a name="are-customer-managed-keys-supported-for-existing-azure-cosmos-accounts"></a>기존 Azure Cosmos 계정에 고객 관리형 키가 지원되나요?

이 기능은 현재 새 계정에서만 사용할 수 있습니다.

### <a name="is-there-a-plan-to-support-finer-granularity-than-account-level-keys"></a>계정 수준 키보다 세부적인 수준을 지원할 계획이 있나요?

현재는 아니지만, 컨테이너 수준 키를 고려하고 있습니다.

### <a name="how-can-i-tell-if-customer-managed-keys-are-enabled-on-my-azure-cosmos-account"></a>Azure Cosmos 계정에서 고객 관리형 키를 사용하도록 설정되어 있는지 어떻게 알 수 있나요?

Azure Portal에서 Azure Cosmos 계정으로 이동 하 고 왼쪽 메뉴에서 **데이터 암호화** 항목을 시청 합니다. 이 항목이 있으면 계정에서 고객 관리 키를 사용할 수 있습니다.

:::image type="content" source="./media/how-to-setup-cmk/portal-data-encryption.png" alt-text="고객 데이터 주위의 암호화 계층":::

Azure Cosmos 계정에 대 한 세부 정보를 프로그래밍 방식으로 가져오고 속성이 있는지 확인할 수도 있습니다 `keyVaultKeyUri` . [PowerShell에서](#using-powershell) 그리고 [Azure CLI를 사용하여](#using-azure-cli) 수행하는 방법은 위를 참조하십시오.

### <a name="how-do-customer-managed-keys-affect-a-backup"></a>고객 관리형 키는 백업에 어떤 영향을 주나요?

Azure Cosmos DB는 계정에 저장된 데이터를 [정기적으로 자동 백업](./online-backup-and-restore.md)합니다. 이러한 작업은 암호화된 데이터를 백업합니다. 복원된 백업을 사용하려면 백업 시 사용한 암호화 키가 필요합니다. 즉, 암호화가 해지되지 않았으며 백업 시 사용한 키 버전이 계속 사용된다는 의미입니다.

### <a name="how-do-i-revoke-an-encryption-key"></a>암호화 키를 어떻게 해지하나요?

키 해지는 최신 버전의 키를 사용하지 않도록 설정하여 수행됩니다.

:::image type="content" source="./media/how-to-setup-cmk/portal-akv-rev2.png" alt-text="고객 데이터 주위의 암호화 계층":::

또는 Azure Key Vault 인스턴스에서 모든 키를 해지하려면 Azure Cosmos DB 보안 주체에 부여된 액세스 정책을 삭제하면 됩니다.

:::image type="content" source="./media/how-to-setup-cmk/portal-akv-rev.png" alt-text="고객 데이터 주위의 암호화 계층":::

### <a name="what-operations-are-available-after-a-customer-managed-key-is-revoked"></a>고객 관리형 키가 해지되면 어떤 작업을 수행할 수 있나요?

암호화 키가 해지된 경우 가능한 유일한 작업은 계정 삭제입니다.

## <a name="next-steps"></a>다음 단계

- [Azure Cosmos DB의 데이터 암호화](./database-encryption-at-rest.md)에 대해 알아봅니다.
- [Cosmos DB의 데이터에 대한 보안 액세스](secure-access-to-data.md) 개요를 살펴봅니다.