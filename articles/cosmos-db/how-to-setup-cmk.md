---
title: Azure Cosmos DB 계정의 고객이 관리 하는 키 구성
description: Azure Cosmos DB 계정에 대해 고객이 관리 하는 키를 구성 하는 방법을 알아봅니다.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/09/2020
ms.author: thweiss
ms.openlocfilehash: 32266abd5bcf8d7e137095d130ee872cc07edaf0
ms.sourcegitcommit: 3eb0cc8091c8e4ae4d537051c3265b92427537fe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/11/2020
ms.locfileid: "75904082"
---
# <a name="configure-customer-managed-keys-for-your-azure-cosmos-db-account"></a>Azure Cosmos DB 계정의 고객이 관리 하는 키 구성

> [!NOTE]
> 지금은이 기능을 사용 하기 위해 액세스를 요청 해야 합니다. 이렇게 하려면 [cosmosdbpm@microsoft.com](mailto:cosmosdbpm@microsoft.com)에 문의 하세요.

Azure Cosmos DB 계정에 저장 된 데이터는 자동으로 암호화 됩니다. Azure Cosmos DB는 미사용 데이터를 암호화 하는 데 사용 되는 키를 관리 하는 두 가지 옵션을 제공 합니다.
- **서비스 관리 키**. 기본적으로 Microsoft는 Azure Cosmos DB 계정을 암호화 하는 데 사용 되는 키를 관리 합니다.
- **CMK (고객이 관리 하는 키)** . 필요에 따라 관리 하는 키를 사용 하 여 두 번째 암호화 계층을 추가 하도록 선택할 수 있습니다.

고객 관리 키는 [Azure Key Vault](../key-vault/key-vault-overview.md)에 저장 되어야 합니다. 각 CMK 사용 계정에 대해 키를 제공 해야 하 고 해당 계정에 저장 된 모든 데이터를 암호화 하는 데 사용 됩니다.

## <a name="setup"></a>설치 프로그램

현재, 고객 관리 키는 새 계정에만 사용할 수 있으며 계정을 만드는 동안 설정 해야 합니다.

### <a name="1-make-sure-the-azure-cosmos-db-resource-provider-is-registered-for-your-azure-subscription"></a>1. Azure Cosmos DB 리소스 공급자가 Azure 구독에 등록 되어 있는지 확인 합니다.

Azure Portal에서 Azure 구독으로 이동 하 고 왼쪽 메뉴에서 "리소스 공급자"를 선택 합니다.

![왼쪽 메뉴의 "리소스 공급자" 항목](./media/how-to-setup-cmk/portal-rp.png)

"Microsoft DocumentDB" 리소스 공급자를 검색 합니다.
- 리소스 공급자가 이미 등록 된 것으로 표시 된 경우 아무 작업도 수행 하지 않아도 됩니다.
- 그렇지 않은 경우 선택 하 고 "등록"을 클릭 합니다.

    ![Microsoft DocumentDB 리소스 공급자를 등록 하는 중](./media/how-to-setup-cmk/portal-rp-register.png)

### <a name="2-configure-your-azure-key-vault-instance"></a>2. Azure Key Vault 인스턴스 구성

Azure Cosmos DB에서 고객이 관리 하는 키를 사용 하려면 암호화 키를 호스트 하는 데 사용할 Azure Key Vault 인스턴스에서 두 개의 속성을 설정 해야 합니다. **일시 삭제** 및 **제거 안 함** 이러한 속성은 기본적으로 사용 하도록 설정 되어 있지 않지만 PowerShell 또는 Azure CLI를 사용 하 여 사용 하도록 설정할 수 있습니다.

기존 Azure Key Vault 인스턴스에서 이러한 속성을 사용 하도록 설정 하는 방법에 대 한 자세한 내용은 다음 문서 중 하나에서 일시 삭제 사용 및 제거 보호 사용 섹션을 참조 하세요.
- [PowerShell에서 일시 삭제를 사용 하는 방법](../key-vault/key-vault-soft-delete-powershell.md)
- [Azure CLI에서 일시 삭제를 사용 하는 방법](../key-vault/key-vault-soft-delete-cli.md)

### <a name="3-add-an-access-policy-to-your-azure-key-vault-instance"></a>3. Azure Key Vault 인스턴스에 액세스 정책을 추가 합니다.

Azure Portal에서 암호화 키를 호스트 하는 데 사용할 Azure Key Vault 인스턴스로 이동 합니다. 그런 다음 왼쪽 메뉴에서 "액세스 정책"을 선택 합니다.

![왼쪽 메뉴의 "액세스 정책"](./media/how-to-setup-cmk/portal-akv-ap.png)

- "+ 액세스 정책 추가"를 클릭 합니다.
- "키 사용 권한" 드롭다운 메뉴에서 "가져오기", "키 래핑 해제" 및 "키 래핑"을 선택 합니다.

    ![적절 한 사용 권한 선택](./media/how-to-setup-cmk/portal-akv-add-ap-perm2.png)

- "보안 주체 선택"에서 "선택 하지 않음"을 클릭 하 고 "Azure Cosmos DB" 보안 주체를 검색 하 여 선택한 다음 맨 아래에 있는 "선택"을 클릭 합니다. "Azure Cosmos DB" 보안 주체를 찾을 수 없는 경우 "Microsoft DocumentDB" 리소스를 다시 등록 해야 할 수 있습니다. 2 단계에서 제공 되는 공급자):

    ![Azure Cosmos DB 보안 주체 선택](./media/how-to-setup-cmk/portal-akv-add-ap.png)

- "추가"를 클릭 하 여 새 액세스 정책을 추가 합니다.

### <a name="4-generate-a-key-in-azure-key-vault"></a>4. Azure Key Vault에서 키를 생성 합니다.

Azure Portal에서 암호화 키를 호스트 하는 데 사용할 Azure Key Vault 인스턴스로 이동 합니다. 그런 다음 왼쪽 메뉴에서 "키"를 선택 합니다.

![왼쪽 메뉴의 "키" 항목](./media/how-to-setup-cmk/portal-akv-keys.png)

- "생성/가져오기"를 클릭 합니다.
- 새 키의 이름을 제공 하 고, RSA 키 크기를 선택 하 고 (최상의 보안을 위해 최소 3072을 권장) "만들기"를 클릭 합니다.

    ![새 키 만들기](./media/how-to-setup-cmk/portal-akv-gen.png)

- 키를 만든 후에는 새로 만든 키를 클릭 한 다음 현재 버전에서
- 마지막 슬래시 뒤의 부분을 제외 하 고 키의 "키 식별자"를 복사 합니다.

    ![키의 키 식별자 복사](./media/how-to-setup-cmk/portal-akv-keyid.png)

### <a name="5-create-a-new-azure-cosmos-db-account"></a>5. 새 Azure Cosmos DB 계정 만들기

#### <a name="using-the-azure-portal"></a>Azure Portal 사용

Azure Portal에서 새 Azure Cosmos DB 계정을 만들 때 "암호화" 단계에서 "고객 관리 키"를 선택 합니다. "키 URI" 필드에서 4 단계에서 복사한 Azure Key Vault 키의 URI를 전달 합니다.

![Azure Portal에서 CMK 매개 변수 설정](./media/how-to-setup-cmk/portal-cosmos-enc.png)

#### <a name="using-powershell"></a>PowerShell 사용

PowerShell을 사용 하 여 새 Azure Cosmos DB 계정을 만들 때
- "PropertyObject"의 "keyVaultKeyUri" 속성 아래에서 4 단계에서 복사한 Azure Key Vault 키의 URI를 전달 합니다.
- API 버전으로 "2019-12-12"을 사용 해야 합니다.

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

#### <a name="using-azure-resource-manager-templates"></a>Azure 리소스 관리자 템플릿 사용

Azure Resource Manager 템플릿을 통해 새 Azure Cosmos DB 계정을 만들 때:
- "properties" 개체의 "keyVaultKeyUri" 속성 아래에서 4 단계에서 복사한 Azure Key Vault 키의 URI를 전달 합니다.
- API 버전으로 "2019-12-12"을 사용 해야 합니다.

```
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

## <a name="frequently-asked-questions"></a>FAQ(질문과 대답)

### <a name="is-there-any-additional-charge-when-using-customer-managed-keys"></a>고객 관리 키를 사용 하는 경우 추가 요금이 있나요?

예. 고객 관리 키를 사용 하 여 데이터 암호화 및 암호 해독을 관리 하는 데 필요한 추가 계산 로드를 고려 하 여, Azure Cosmos DB 계정에 대해 실행 되는 모든 작업은 사용 되는 [요청 단위의](./request-units.md) 25% 증가를 가져옵니다.

### <a name="what-data-gets-encrypted-with-the-cmk"></a>CMK를 사용 하 여 암호화 되는 데이터는 무엇 인가요?

Azure Cosmos DB 계정에 저장 된 모든 데이터는 다음 메타 데이터를 제외 하 고 CMK를 사용 하 여 암호화 됩니다.
- Azure Cosmos DB [계정, 데이터베이스 및 컨테이너](./account-overview.md#elements-in-an-azure-cosmos-account)의 이름
- [저장 프로시저](./stored-procedures-triggers-udfs.md)의 이름입니다.
- [인덱싱 정책](./index-policy.md)에 선언 된 속성 경로
- 컨테이너의 [파티션 키](./partitioning-overview.md)값입니다.

### <a name="will-customer-managed-keys-be-supported-for-existing-accounts"></a>고객 관리 키가 기존 계정에 대해 지원 되나요?

이 기능은 현재 새 계정에만 사용할 수 있습니다.

### <a name="is-there-a-plan-to-support-finer-granularity-than-account-level-keys"></a>계정 수준 키 보다 세부적인 세분성을 지원할 계획이 있나요?

현재는 없지만 컨테이너 수준 키를 고려 하 고 있습니다.

### <a name="how-does-customer-managed-keys-affect-backups"></a>고객 관리 키는 백업에 어떤 영향을 미칩니까?

Azure Cosmos DB는 계정에 저장 된 데이터의 [자동 백업을 정기적](./online-backup-and-restore.md) 으로 수행 합니다. 이 작업은 암호화 된 데이터를 백업 합니다. 복원 된 백업을 사용할 수 있도록 하려면 백업 시에 사용 되는 암호화 키를 계속 사용할 수 있어야 합니다. 즉, 해지를 수행 하지 않고 백업 시 사용 된 키의 버전을 계속 사용 하도록 설정 해야 합니다.

### <a name="how-do-i-revoke-an-encryption-key"></a>암호화 키를 취소 어떻게 할까요??

키 해지는 키의 최신 버전을 사용 하지 않도록 설정 하 여 수행 됩니다.

![키 버전 비활성화](./media/how-to-setup-cmk/portal-akv-rev2.png)

또는 Azure Key Vault 인스턴스에서 모든 키를 해지 하려면 Azure Cosmos DB 보안 주체에 부여 된 액세스 정책을 삭제할 수 있습니다.

![Azure Cosmos DB 보안 주체에 대 한 액세스 정책을 삭제 하는 중](./media/how-to-setup-cmk/portal-akv-rev.png)

### <a name="what-operations-are-available-after-a-customer-managed-key-is-revoked"></a>고객 관리 키가 해지 된 후에 사용할 수 있는 작업은 무엇 인가요?

암호화 키가 해지 된 경우에만 수행할 수 있는 작업은 계정 삭제입니다.

## <a name="next-steps"></a>다음 단계

- [Azure Cosmos DB의 데이터 암호화](./database-encryption-at-rest.md) 에 대 한 자세한 정보
- [Cosmos DB 데이터에 대 한 보안 액세스](secure-access-to-data.md) 개요 보기