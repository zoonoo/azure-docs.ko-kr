---
title: Azure Cosmos DB 계정에 대한 고객 관리 키 구성
description: Azure 키 자격 증명 모음을 사용하여 Azure Cosmos DB 계정에 대한 고객 관리 키를 구성하는 방법에 대해 알아봅니다.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: thweiss
ROBOTS: noindex, nofollow
ms.openlocfilehash: 733a85e492185e7e83922a3cc91d53c848b939a1
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81411153"
---
# <a name="configure-customer-managed-keys-for-your-azure-cosmos-account-with-azure-key-vault"></a>Azure 키 자격 증명 모음을 통해 Azure Cosmos 계정에 대한 고객 관리 키 구성

> [!NOTE]
> 이 때 이 기능을 사용하려면 액세스를 요청해야 합니다. 이렇게 하려면 다음 [azurecosmosdbcmk@service.microsoft.com](mailto:azurecosmosdbcmk@service.microsoft.com)연락처로 문의하십시오.

Azure Cosmos 계정에 저장된 데이터는**Microsoft(서비스 관리 키)에서**관리하는 키로 자동으로 원활하게 암호화됩니다. 선택적으로 관리하는 키(고객 관리 키)를 사용하여 두 번째 암호화 계층을 추가하도록 선택할 수**있습니다.**

![고객 데이터를 둘러싼 암호화 계층](./media/how-to-setup-cmk/cmk-intro.png)

Azure [키 볼트에](../key-vault/key-vault-overview.md) 고객 관리 키를 저장하고 고객 관리 키로 활성화된 각 Azure Cosmos 계정에 대한 키를 제공해야 합니다. 이 키는 해당 계정에 저장된 모든 데이터를 암호화하는 데 사용됩니다.

> [!NOTE]
> 현재 고객 관리 키는 새 Azure Cosmos 계정에서만 사용할 수 있습니다. 계정을 만드는 동안 구성해야 합니다.

## <a name="register-the-azure-cosmos-db-resource-provider-for-your-azure-subscription"></a><a id="register-resource-provider"></a>Azure 구독에 대한 Azure Cosmos DB 리소스 공급자 등록

1. [Azure 포털에](https://portal.azure.com/)로그인하고 Azure 구독으로 이동한 다음 **설정** 탭에서 **리소스 공급자를** 선택합니다.

   ![왼쪽 메뉴에서 "리소스 공급자" 항목](./media/how-to-setup-cmk/portal-rp.png)

1. **Microsoft.DocumentDB** 리소스 공급자를 검색합니다. 리소스 공급자가 이미 등록된 것으로 표시되어 있는지 확인합니다. 그렇지 않은 경우 리소스 공급자를 선택하고 **레지스터를**선택합니다.

   ![Microsoft.DocumentDB 리소스 공급자 등록](./media/how-to-setup-cmk/portal-rp-register.png)

## <a name="configure-your-azure-key-vault-instance"></a>Azure 키 볼트 인스턴스 구성

Azure Cosmos DB에서 고객 관리 키를 사용하려면 암호화 키를 호스트하는 데 사용할 Azure Key Vault 인스턴스에서 두 개의 속성을 설정해야 합니다. 이러한 속성에는 **소프트 삭제** 및 **제거 안 추가**가 포함됩니다. 이러한 속성은 기본적으로 활성화되지 않습니다. PowerShell 또는 Azure CLI를 사용하여 활성화할 수 있습니다.

기존 Azure Key Vault 인스턴스에서 이러한 속성을 활성화하는 방법을 알아보려면 다음 문서 중 하나에서 "소프트 삭제 사용" 및 "지우기 방지 사용" 섹션을 참조하십시오.

- [PowerShell에서 소프트 삭제를 사용하는 방법](../key-vault/key-vault-soft-delete-powershell.md)
- [Azure CLI에서 소프트 삭제를 사용하는 방법](../key-vault/key-vault-soft-delete-cli.md)

## <a name="add-an-access-policy-to-your-azure-key-vault-instance"></a>Azure 키 볼트 인스턴스에 액세스 정책 추가

1. Azure 포털에서 암호화 키를 호스트하는 데 사용할 Azure Key Vault 인스턴스로 이동합니다. 왼쪽 메뉴에서 **액세스 정책을** 선택합니다.

   ![왼쪽 메뉴에서 "정책 액세스"](./media/how-to-setup-cmk/portal-akv-ap.png)

1. **+ 액세스 정책 추가를**선택합니다.

1. 키 **사용 권한** 드롭다운 메뉴에서 **키 열기**, 키 **둘러싸기**해제 및 **키 줄 바꿈** 사용 권한을 선택합니다.

   ![올바른 사용 권한 선택](./media/how-to-setup-cmk/portal-akv-add-ap-perm2.png)

1. **선택 주체에서** **선택 없음을 선택합니다.** 그런 다음 **Azure Cosmos DB** 보안 주체를 검색하고 선택합니다(쉽게 찾을 수 `a232010e-820c-4083-83bb-3ace5fc29d0b` 있도록 주체 ID가 있는 Azure `57506a73-e302-42a9-b869-6f12d9ec29e9`정부 지역을 제외한 모든 Azure 지역)에서 보안 주체 ID로 검색할 수도 있습니다. 마지막으로 하단에서 **선택을** 선택합니다. Azure **Cosmos DB** 보안 주체가 목록에 없는 경우 이 문서의 리소스 공급자 등록 섹션에 설명된 대로 **Microsoft.DocumentDB** 리소스 [공급자를](#register-resource-provider) 다시 등록해야 할 수 있습니다.

   ![Azure 코스모스 DB 보안 주체 선택](./media/how-to-setup-cmk/portal-akv-add-ap.png)

1. 새 액세스 정책을 추가하려면 **추가를** 선택합니다.

## <a name="generate-a-key-in-azure-key-vault"></a>Azure 키 자격 증명 모음에서 키 생성

1. Azure 포털에서 암호화 키를 호스트하는 데 사용할 Azure Key Vault 인스턴스로 이동합니다. 그런 다음 왼쪽 메뉴에서 **키를** 선택합니다.

   ![왼쪽 메뉴에서 "키" 항목](./media/how-to-setup-cmk/portal-akv-keys.png)

1. **생성/가져오기를**선택하고 새 키에 대한 이름을 제공하고 RSA 키 크기를 선택합니다. 최상의 보안을 위해 최소 3072를 권장합니다. 그런 다음 **만들기를**선택합니다.

   ![새 키 만들기](./media/how-to-setup-cmk/portal-akv-gen.png)

1. 키를 만든 후 새로 만든 키를 선택한 다음 현재 버전을 선택합니다.

1. 키의 키 **식별자를**복사합니다.

   ![키의 키 식별자 복사](./media/how-to-setup-cmk/portal-akv-keyid.png)

## <a name="create-a-new-azure-cosmos-account"></a>새 Azure 코스모스 계정 만들기

### <a name="using-the-azure-portal"></a>Azure Portal 사용

Azure 포털에서 새 Azure Cosmos DB 계정을 만들 때 **암호화** 단계에서 **고객 관리 키를** 선택합니다. 키 **URI** 필드에서 이전 단계에서 복사한 Azure Key Vault 키의 URI/키 식별자를 붙여넣습니다.

![Azure 포털에서 CMK 매개 변수 설정](./media/how-to-setup-cmk/portal-cosmos-enc.png)

### <a name="using-azure-powershell"></a>Azure PowerShell 사용

PowerShell을 사용하여 새 Azure 코스모스 DB 계정을 만들 때:

- PropertyObject의 **keyVaultKeyUri** 속성 에서 이전에 복사된 Azure 키 볼트 키의 URI를 **전달합니다.**

- **2019-12-12를** API 버전으로 사용합니다.

> [!IMPORTANT]
> 고객이 관리하는 `Location` 키로 계정을 성공적으로 만들려면 매개 변수를 명시적으로 설정해야 합니다.

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

### <a name="using-an-azure-resource-manager-template"></a>Azure Resource Manager 템플릿 사용

Azure 리소스 관리자 템플릿을 통해 새 Azure Cosmos 계정을 만들 때:

- **속성** 개체의 **keyVaultKeyUri** 속성 에서 이전에 복사한 Azure 키 볼트 키의 URI를 전달합니다.

- **2019-12-12를** API 버전으로 사용합니다.

> [!IMPORTANT]
> 고객이 관리하는 `Location` 키로 계정을 성공적으로 만들려면 매개 변수를 명시적으로 설정해야 합니다.

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

다음 PowerShell 스크립트를 통해 템플릿을 배포합니다.

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

### <a name="using-the-azure-cli"></a>Azure CLI 사용

Azure CLI를 통해 새 Azure Cosmos 계정을 만들 때 **--key-uri** 매개 변수 에서 이전에 복사한 Azure 키 볼트 키의 URI를 전달합니다.

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

## <a name="frequently-asked-questions"></a>질문과 대답

### <a name="is-there-any-additional-charge-for-using-customer-managed-keys"></a>고객 관리 키 사용에 대한 추가 요금이 있습니까?

예. 고객 관리 키로 데이터 암호화 및 암호 해독을 관리하는 데 필요한 추가 계산 로드를 고려하기 위해 Azure Cosmos 계정에 대해 실행되는 모든 작업은 [요청 단위의](./request-units.md)25% 증가를 소비합니다.

### <a name="what-data-gets-encrypted-with-the-customer-managed-keys"></a>고객이 관리하는 키로 암호화되는 데이터는 무엇입니까?

Azure Cosmos 계정에 저장된 모든 데이터는 다음 메타데이터를 제외하고 고객 관리 키로 암호화됩니다.

- Azure Cosmos DB [계정, 데이터베이스 및 컨테이너의](./account-overview.md#elements-in-an-azure-cosmos-account) 이름

- [저장 프로시저의](./stored-procedures-triggers-udfs.md) 이름

- [인덱싱 정책에](./index-policy.md) 선언된 속성 경로

- 컨테이너의 [파티션 키](./partitioning-overview.md) 값

### <a name="are-customer-managed-keys-supported-for-existing-azure-cosmos-accounts"></a>기존 Azure Cosmos 계정에 대해 고객 관리 키가 지원됩니까?

이 기능은 현재 새 계정에서만 사용할 수 있습니다.

### <a name="is-there-a-plan-to-support-finer-granularity-than-account-level-keys"></a>계정 수준 키보다 세밀한 세분성을 지원할 계획이 있습니까?

현재는 아니지만 컨테이너 수준 키가 고려되고 있습니다.

### <a name="how-do-customer-managed-keys-affect-a-backup"></a>고객 관리 키는 백업에 어떤 영향을 미칩니까?

Azure Cosmos DB는 계정에 저장된 데이터의 [정기적이고 자동적인 백업을](./online-backup-and-restore.md) 수행합니다. 이 작업은 암호화된 데이터를 백업합니다. 복원된 백업을 사용하려면 백업 시 사용한 암호화 키가 필요합니다. 즉, 해지되지 않았으며 백업 시 사용된 키 의 버전은 계속 활성화됩니다.

### <a name="how-do-i-revoke-an-encryption-key"></a>암호화 키를 해지하려면 어떻게 해야 합니까?

키 해지는 키의 최신 버전을 사용하지 않도록 설정하여 수행됩니다.

![키 버전 사용 안 함](./media/how-to-setup-cmk/portal-akv-rev2.png)

또는 Azure Key Vault 인스턴스에서 모든 키를 해지하려면 Azure Cosmos DB 보안 주체에 부여된 액세스 정책을 삭제할 수 있습니다.

![Azure Cosmos DB 보안 주체에 대한 액세스 정책 삭제](./media/how-to-setup-cmk/portal-akv-rev.png)

### <a name="what-operations-are-available-after-a-customer-managed-key-is-revoked"></a>고객 관리 키가 해지된 후 사용할 수 있는 작업은 무엇입니까?

암호화 키가 해지되었을 때 가능한 유일한 작업은 계정 삭제입니다.

## <a name="next-steps"></a>다음 단계

- [Azure Cosmos DB에서 데이터 암호화에](./database-encryption-at-rest.md)대해 자세히 알아보십시오.
- [코스모스 DB의 데이터에 대한 보안 액세스에](secure-access-to-data.md)대한 개요를 가져옵니다.
