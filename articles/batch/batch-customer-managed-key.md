---
title: Azure Key Vault와 관리 ID를 사용하여 Azure Batch 계정에 대한 고객 관리형 키 구성하기
description: 고객 관리형 키를 사용하여 일괄 처리 데이터를 암호화하는 방법을 알아보기.
author: pkshultz
ms.topic: how-to
ms.date: 02/11/2021
ms.author: peshultz
ms.openlocfilehash: 36883489e1a9a50406ad1b1bde78d2f25ee227b7
ms.sourcegitcommit: aba63ab15a1a10f6456c16cd382952df4fd7c3ff
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/25/2021
ms.locfileid: "107989327"
---
# <a name="configure-customer-managed-keys-for-your-azure-batch-account-with-azure-key-vault-and-managed-identity"></a>Azure Key Vault와 관리 ID를 사용하여 Azure Batch 계정에 대한 고객 관리형 키 구성하기

기본적으로 Azure Batch는 플랫폼 관리형 키를 사용하여 인증서, 작업/태스크 메타데이터 등의 Azure Batch 서비스에 저장된 모든 고객 데이터를 암호화합니다. 필요에 따라 사용자 고유 키, 즉 고객 관리형 키를 사용하여 Azure Batch에 저장된 데이터를 암호화할 수 있습니다.

사용자가 제공하는 키는 [Azure Key Vault](../key-vault/general/basic-concepts.md)에서 생성되어야 하며, [Azure 리소스에 대한 관리 ID](../active-directory/managed-identities-azure-resources/overview.md)를 사용하여 액세스해야 합니다.

관리 ID는 [*시스템 할당* 과 *사용자 할당*](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types)의 두 가지 형식이 있습니다.

시스템 할당 관리 ID를 사용하여 배치 계정을 만들거나 고객 관리형 키에 액세스할 수 있는 사용자 할당 관리 ID를 별도로 만들 수 있습니다. [비교 표](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types)를 검토하여 차이점을 이해하고 솔루션에 가장 적합한 옵션을 고려합니다. 예를 들어, 동일한 관리 ID를 사용하여 여러 Azure 리소스에 액세스하려는 경우 사용자 할당 관리 ID가 필요합니다. 그렇지 않은 경우에는 배치 계정과 연결된 시스템 할당 관리 ID로도 충분히 처리할 수 있습니다. 사용자 할당 관리 ID를 사용하면 [아래 예제](#create-a-batch-account-with-user-assigned-managed-identity-and-customer-managed-keys)와 같이 배치 계정 만들기에서 고객 관리형 키를 적용하는 옵션도 제공됩니다.

## <a name="create-a-batch-account-with-system-assigned-managed-identity"></a>시스템 할당 관리 ID를 사용하는 배치 계정 만들기

별도로 사용자 할당 관리 ID가 필요하지 않은 경우, 배치 계정을 만들 때 시스템 할당 관리 ID를 사용하도록 설정할 수 있습니다.

### <a name="azure-portal"></a>Azure portal

[Azure Portal](https://portal.azure.com/)에서 배치 계정을 만들 때 **고급** 탭의 ID 형식에서 **시스템 할당** 을 선택합니다.

![시스템 할당 ID 형식을 사용하는 새 배치 계정의 스크린샷](./media/batch-customer-managed-key/create-batch-account.png)

계정이 생성된 후에는 **보안 주체 ID** 필드의 **속성** 섹션에서 고유 식별자(GUID)를 찾을 수 있습니다. **ID 형식** 이 표시됩니다`System assigned`.

![보안 주체 ID 필드에서 고유 GUID를 보여 주는 스크린샷](./media/batch-customer-managed-key/linked-batch-principal.png)

이 배치 계정에 Key Vault에 대한 액세스 권한을 부여하려면 이 값이 필요합니다.

### <a name="azure-cli"></a>Azure CLI

새 배치 계정을 만드는 경우, `SystemAssigned`매개 변수에 대해`--identity` 지정합니다.

```azurecli
resourceGroupName='myResourceGroup'
accountName='mybatchaccount'

az batch account create \
    -n $accountName \
    -g $resourceGroupName \
    --locations regionName='West US 2' \
    --identity 'SystemAssigned'
```

계정을 만든 후에는 이 계정에서 시스템 할당 관리 ID가 사용되도록 설정되었는지 확인할 수 있습니다. 배치 계정에 Key Vault에 대한 액세스 권한을 부여하는 데 이 값이 필요하므로 `PrincipalId`에 유의해야 합니다.

```azurecli
az batch account show \
    -n $accountName \
    -g $resourceGroupName \
    --query identity
```

> [!NOTE]
> 배치 계정에서 만든 시스템 할당 관리 ID는 Key Vault에서 고객 관리형 키를 검색하는 데만 사용됩니다. 이 ID는 Batch 풀에서 사용할 수 없습니다. 풀에서 사용자 할당 관리 ID를 사용하려면 [Batch 풀에서 관리 ID 구성](managed-identity-pools.md)을 참조하세요.

## <a name="create-a-user-assigned-managed-identity"></a>사용자 할당 관리 ID 만들기

원하는 경우 고객 관리형 키에 액세스하는 데 사용할 수 있는 [사용자 할당 관리 ID를 만들](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity) 수 있습니다.

이 ID에 **클라이언트 ID** 값이 있어야 Key Vault에 액세스할 수 있습니다.

## <a name="configure-your-azure-key-vault-instance"></a>Azure Key Vault 인스턴스 구성

키가 생성되는 Azure Key Vault는 배치 계정과 동일한 테넌트에 만들어져야 합니다. 동일한 리소스 그룹이나 구독에 존재할 필요는 없습니다.

### <a name="create-an-azure-key-vault"></a>Azure Key Vault 만들기

Azure Batch에 대한 고객 관리형 키를 사용하여 [Azure Key Vault 인스턴스를 만들](../key-vault/general/quick-create-portal.md) 때 **일시 삭제** 및 **삭제 보호** 를 모두 사용하도록 설정해야 합니다.

![Key Vault 만들기 화면 스크린샷](./media/batch-customer-managed-key/create-key-vault.png)

### <a name="add-an-access-policy-to-your-azure-key-vault-instance"></a>Azure Key Vault 인스턴스에 액세스 정책 추가

Azure Portal에서 Key Vault를 만든 후 **설정** 의 **액세스 정책** 에서 관리 ID를 사용하여 배치 계정 액세스를 추가합니다. **키 사용 권한** 에서 **가져오기**, **키 래핑** 및 **키 래핑 해제** 를 선택합니다.

![액세스 정책 추가 화면 스크린샷](./media/batch-customer-managed-key/key-permissions.png)

**보안 주체** 아래의 **Select** 필드에서 다음 중 하나를 입력합니다.

- 시스템 할당 관리 ID: 이전에 검색한 `principalId` 또는 배치 계정의 이름을 입력합니다.
- 사용자 할당 관리 ID: 이전에 검색한 **클라이언트 ID** 또는 사용자 할당 관리 ID의 이름을 입력합니다.

![보안 주체 화면의 스크린샷.](./media/batch-customer-managed-key/principal-id.png)

### <a name="generate-a-key-in-azure-key-vault"></a>Azure Key Vault에서 새 키 생성

Azure Portal에서 **키** 섹션의 Key Vault 인스턴스로 이동하여 **생성/가져오기** 를 선택합니다. **키 형식** 을 `RSA`로 선택하고 **RSA 키 크기** 를 `2048`비트 이상으로 선택합니다. `EC` 키 형식은 현재 배치 계정에서 고객 관리형 키로 지원되지 않습니다.

![키 만들기](./media/batch-customer-managed-key/create-key.png)

키를 만든 후 새로 만든 키와 현재 버전을 클릭하고 **속성** 섹션에서 **키 식별자** 를 복사합니다.  **허용된 작업** 아래에 **키 래핑** 및 **키 래핑 해제** 가 모두 선택되어 있는지 확인합니다.

## <a name="enable-customer-managed-keys-on-a-batch-account"></a>배치 계정에서 고객 관리형 키 사용

위의 단계를 수행한 후에는 배치 계정에서 고객 관리형 키를 사용하도록 설정할 수 있습니다.

### <a name="azure-portal"></a>Azure portal

[Azure Portal](https://portal.azure.com/)에서 배치 계정 페이지로 이동합니다. **암호화** 섹션에서 **고객 관리형 키** 를 사용하도록 설정합니다. 키 식별자를 직접 사용하거나, 키 자격 증명 모음을 선택하고 **키 자격 증명 모음 및 키 선택** 을 클릭할 수 있습니다.

![암호화 섹션과 고객 관리형 키를 사용하도록 설정하는 옵션을 보여 주는 스크린샷](./media/batch-customer-managed-key/encryption-page.png)

### <a name="azure-cli"></a>Azure CLI

시스템 할당 관리 ID를 사용하여 배치 계정을 만든 다음 Key Vault에 대한 액세스 권한이 부여되면 `keyVaultProperties`매개 변수 내 `{Key Identifier}`URL을 사용하여 배치 계정을 업데이트합니다. 또한 **encryption_key_source** 를 `Microsoft.KeyVault`로 설정합니다.

```azurecli
az batch account set \
    -n $accountName \
    -g $resourceGroupName \
    --encryption_key_source Microsoft.KeyVault \
    --encryption_key_identifier {YourKeyIdentifier} 
```

## <a name="create-a-batch-account-with-user-assigned-managed-identity-and-customer-managed-keys"></a>사용자 할당 관리 ID 및 고객 관리형 키를 사용하여 배치 계정 만들기

Batch 관리 .NET 클라이언트를 사용하여 사용자 할당 관리 ID 및 고객 관리형 키를 포함하는 배치 계정을 만들 수 있습니다.

```c#
EncryptionProperties encryptionProperties = new EncryptionProperties()
{
    KeySource = KeySource.MicrosoftKeyVault,
    KeyVaultProperties = new KeyVaultProperties()
    {
        KeyIdentifier = "Your Key Azure Resource Manager Resource ID"
    }
};

BatchAccountIdentity identity = new BatchAccountIdentity()
{
    Type = ResourceIdentityType.UserAssigned,
    UserAssignedIdentities = new Dictionary<string, BatchAccountIdentityUserAssignedIdentitiesValue>
    {
            ["Your Identity Azure Resource Manager ResourceId"] = new BatchAccountIdentityUserAssignedIdentitiesValue()
    }
};
var parameters = new BatchAccountCreateParameters(TestConfiguration.ManagementRegion, encryption:encryptionProperties, identity: identity);

var account = await batchManagementClient.Account.CreateAsync("MyResourceGroup",
    "mynewaccount", parameters); 
```

## <a name="update-the-customer-managed-key-version"></a>고객 관리형 키 버전 업데이트

새 버전 키를 만들 때 새 버전을 사용하도록 배치 계정을 업데이트해야 합니다. 다음 단계를 수행합니다.

1. Azure Portal에서 배치 계정으로 이동하여 암호화 설정을 표시합니다.
2. 새 버전 키에 URI를 입력합니다. 또는 Key Vault를 선택하고 키를 다시 선택하여 버전을 업데이트할 수 있습니다.
3. 변경 내용을 저장합니다.

Azure CLI를 사용하여 버전을 업데이트할 수도 있습니다.

```azurecli
az batch account set \
    -n $accountName \
    -g $resourceGroupName \
    --encryption_key_identifier {YourKeyIdentifierWithNewVersion} 
```

## <a name="use-a-different-key-for-batch-encryption"></a>Batch 암호화에 다른 키 사용

Batch 암호화에 사용되는 키를 변경하려면 다음 단계를 수행합니다.

1. 배치 계정으로 이동하여 암호화 설정을 표시합니다.
2. 새 키에 URI를 입력합니다. 또는 Key Vault를 선택하고 새 키를 선택할 수 있습니다.
3. 변경 내용을 저장합니다.

Azure CLI를 사용하여 다른 키를 사용할 수도 있습니다.

```azurecli
az batch account set \
    -n $accountName \
    -g $resourceGroupName \
    --encryption_key_identifier {YourNewKeyIdentifier} 
```

## <a name="frequently-asked-questions"></a>질문과 대답

- **기존 배치 계정에 고객 관리형 키가 지원되나요?** 아니요. 고객 관리형 키는 새 배치 계정에 대해서만 지원됩니다.
- **RSA 키 크기를 2048비트보다 큰 비트를 선택할 수 있나요?** 예, RSA 비트 크기가 `3072` 및 `4096`인 비트도 지원됩니다.
- **고객 관리형 키가 해지되면 어떤 작업을 수행할 수 있나요?** Batch가 고객 관리형 키에 대한 액세스 권한을 상실하면 계정 삭제 작업만 허용됩니다.
- **Key Vault 키를 실수로 삭제한 경우 배치 계정에 대한 액세스를 복원하려면 어떻게 해야 하나요?** 삭제 보호 및 일시 삭제가 가능하도록 설정했으므로 기존 키를 복원할 수 있습니다. 자세한 내용은 [Azure Key Vault 복구](../key-vault/general/key-vault-recovery.md)를 참조하세요.
- **고객 관리형 키를 사용하지 않도록 설정할 수 있나요?** 언제든지 배치 계정의 암호화 형식을 ‘Microsoft 관리형 키’로 다시 설정할 수 있습니다. 그 후에는 키를 삭제하거나 변경할 수 있습니다.
- **내 키를 회전하려면 어떻게 해야 하나요?** 고객 관리형 키는 자동으로 회전되지 않습니다. 키를 회전하려면 계정이 연결된 키 식별자를 업데이트합니다.
- **액세스를 복원한 후 배치 계정이 다시 작동하는 데 시간이 얼마나 걸리나요?** 액세스가 복원되면 계정에 다시 액세스하는 데 최대 10분이 걸릴 수 있습니다.
- **배치 계정을 사용할 수 없을 때 내 리소스는 어떻게 되나요?** 고객 관리형 키에 대한 Batch 액세스 권한을 잃은 상태에서 실행 중인 모든 풀은 계속 실행됩니다. 그러나 노드는 사용할 수 없는 상태로 전환되어 작업이 중단되고 큐에 대기하게 됩니다. 액세스가 복원되면 노드를 다시 사용할 수 있고 작업이 다시 시작됩니다.
- **암호화 메커니즘은 Batch 풀의 VM 디스크에 적용되나요?** 아니요. 클라우드 서비스 구성 풀([사용되지 않음](https://azure.microsoft.com/updates/azure-batch-cloudserviceconfiguration-pools-will-be-retired-on-29-february-2024/))의 경우 OS 및 임시 디스크에 대한 암호화가 적용되지 않습니다. Virtual Machine Configuration 풀의 경우 OS 및 지정된 모든 데이터 디스크는 기본적으로 Microsoft 플랫폼 관리형 키를 사용하여 암호화됩니다. 현재는 이와 같은 디스크에 대한 고유 키를 지정할 수 없습니다. Microsoft 플랫폼 관리 키를 사용하여 Batch 풀에 대한 VM의 임시 디스크를 암호화하려면 [가상 머신 구성](/rest/api/batchservice/pool/add#virtualmachineconfiguration) 풀에서 [diskEncryptionConfiguration](/rest/api/batchservice/pool/add#diskencryptionconfiguration) 속성을 사용하도록 설정해야 합니다. 매우 중요한 환경에서는 임시 디스크 암호화를 사용하도록 설정하고 OS 및 데이터 디스크에 중요한 데이터를 저장하지 않는 것이 좋습니다. 자세한 내용은 [디스크 암호화를 사용하여 풀 만들기](./disk-encryption.md)를 참조하세요.
- **배치 계정에 있는 시스템 할당 관리 ID는 컴퓨팅 노드에서 사용할 수 있나요?** 아니요. 현재 시스템 할당 관리 ID는 고객 관리형 키에 대한 Azure Key Vault에 액세스하는 데만 사용됩니다. 컴퓨팅 노드에서 사용자 할당 관리 ID를 사용하려면 [Batch 풀에서 관리 ID 구성](managed-identity-pools.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- [Azure Batch 보안 모범 사례](security-best-practices.md)에 대한 자세한 정보.
- [Azure Key Vault](../key-vault/general/basic-concepts.md)에 대한 자세한 정보.
