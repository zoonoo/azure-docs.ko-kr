---
title: Azure Key Vault 및 관리 Id를 사용 하 여 Azure Batch 계정에 대 한 고객 관리 키 구성
description: 고객 관리 키를 사용 하 여 일괄 처리 데이터를 암호화 하는 방법을 알아봅니다.
author: pkshultz
ms.topic: how-to
ms.date: 02/11/2021
ms.author: peshultz
ms.openlocfilehash: d3f10436b95aaeb5eb35a873c2a3862c1492bd47
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100385067"
---
# <a name="configure-customer-managed-keys-for-your-azure-batch-account-with-azure-key-vault-and-managed-identity"></a>Azure Key Vault 및 관리 Id를 사용 하 여 Azure Batch 계정에 대 한 고객 관리 키 구성

기본적으로 Azure Batch는 플랫폼 관리 키를 사용 하 여 인증서, 작업/태스크 메타 데이터 등의 Azure Batch 서비스에 저장 된 모든 고객 데이터를 암호화 합니다. 필요에 따라 사용자 고유의 키, 즉 고객이 관리 하는 키를 사용 하 여 Azure Batch에 저장 된 데이터를 암호화할 수 있습니다.

사용자가 제공 하는 키는 [Azure Key Vault](../key-vault/general/basic-concepts.md)에서 생성 되어야 하며, [Azure 리소스에 대 한 관리 id](../active-directory/managed-identities-azure-resources/overview.md)를 사용 하 여 액세스 해야 합니다.

관리 id에는 [ *시스템 할당* 및 *사용자 할당*](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types)이라는 두 가지 유형이 있습니다.

시스템 할당 관리 id를 사용 하 여 Batch 계정을 만들거나 고객이 관리 하는 키에 액세스할 수 있는 별도의 사용자 할당 관리 id를 만들 수 있습니다. [비교 표](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) 를 검토 하 여 차이점을 이해 하 고 솔루션에 가장 적합 한 옵션을 고려 합니다. 예를 들어 동일한 관리 되는 id를 사용 하 여 여러 Azure 리소스에 액세스 하려는 경우 사용자 할당 관리 id가 필요 합니다. 그렇지 않으면 Batch 계정과 연결 된 시스템 할당 관리 id가 충분할 수 있습니다. 사용자 할당 관리 id를 사용 하면 [아래 예제](#create-a-batch-account-with-user-assigned-managed-identity-and-customer-managed-keys)와 같이 Batch 계정 만들기에서 고객 관리 키를 적용 하는 옵션도 제공 됩니다.

## <a name="create-a-batch-account-with-system-assigned-managed-identity"></a>시스템 할당 관리 id를 사용 하 여 Batch 계정 만들기

별도의 사용자 할당 관리 id가 필요 하지 않은 경우 Batch 계정을 만들 때 시스템 할당 관리 id를 사용 하도록 설정할 수 있습니다.

### <a name="azure-portal"></a>Azure portal

[Azure Portal](https://portal.azure.com/)에서 Batch 계정을 만들 때 **고급** 탭의 id 형식에서 **시스템 할당** 을 선택 합니다.

![시스템 할당 id 유형을 사용 하는 새 Batch 계정의 스크린샷](./media/batch-customer-managed-key/create-batch-account.png)

계정이 생성 된 후에는 **id 보안 주체 id** 필드의 **속성** 섹션에서 고유한 GUID를 찾을 수 있습니다. **Id 유형이** 표시 됩니다 `System assigned` .

![Id 보안 주체 Id 필드에 고유한 GUID를 보여 주는 스크린샷](./media/batch-customer-managed-key/linked-batch-principal.png)

이 배치 계정에 Key Vault에 대 한 액세스 권한을 부여 하려면이 값이 필요 합니다.

### <a name="azure-cli"></a>Azure CLI

새 Batch 계정을 만드는 경우 `SystemAssigned` 매개 변수에 대해를 지정 `--identity` 합니다.

```azurecli
resourceGroupName='myResourceGroup'
accountName='mybatchaccount'

az batch account create \
    -n $accountName \
    -g $resourceGroupName \
    --locations regionName='West US 2' \
    --identity 'SystemAssigned'
```

계정을 만든 후에는이 계정에서 시스템 할당 관리 id가 사용 하도록 설정 되었는지 확인할 수 있습니다. 이 `PrincipalId` 일괄 처리 계정에 Key Vault에 대 한 액세스 권한을 부여 하는 데이 값이 필요 하므로에 유의 해야 합니다.

```azurecli
az batch account show \
    -n $accountName \
    -g $resourceGroupName \
    --query identity
```

> [!NOTE]
> Batch 계정에서 만든 시스템 할당 관리 id는 Key Vault에서 고객 관리 키를 검색 하는 데만 사용 됩니다. 이 id는 Batch 풀에서 사용할 수 없습니다. 풀에서 사용자 할당 관리 id를 사용 하려면 [Batch 풀에서 관리 되](managed-identity-pools.md)는 id 구성을 참조 하세요.

## <a name="create-a-user-assigned-managed-identity"></a>사용자 할당 관리 ID 만들기

원하는 경우 고객이 관리 하는 키에 액세스 하는 데 사용할 수 있는 [사용자 할당 관리 id를 만들](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity) 수 있습니다.

Key Vault에 액세스 하려면이 id의 **클라이언트 id** 값이 필요 합니다.

## <a name="configure-your-azure-key-vault-instance"></a>Azure Key Vault 인스턴스 구성

키가 생성 되는 Azure Key Vault는 Batch 계정과 동일한 테 넌 트에 만들어져야 합니다. 동일한 리소스 그룹 또는 동일한 구독에 있을 필요는 없습니다.

### <a name="create-an-azure-key-vault"></a>Azure Key Vault 만들기

Azure Batch에 대 한 고객 관리 키를 사용 하 여 [Azure Key Vault 인스턴스를 만들](../key-vault/general/quick-create-portal.md) 때 **일시 삭제** 및 **제거 보호** 를 모두 사용 하도록 설정 해야 합니다.

![Key Vault 만들기 화면의 스크린샷](./media/batch-customer-managed-key/create-key-vault.png)

### <a name="add-an-access-policy-to-your-azure-key-vault-instance"></a>Azure Key Vault 인스턴스에 액세스 정책 추가

Azure Portal에서 Key Vault를 만든 후 **설정** 의 **액세스 정책** 에서 관리 id를 사용 하 여 Batch 계정 액세스를 추가 합니다. **키 사용 권한** 에서 **가져오기**, **키 래핑** 및 **키 래핑** 해제를 선택 합니다.

![액세스 정책 추가 화면을 보여 주는 스크린샷](./media/batch-customer-managed-key/key-permissions.png)

**보안 주체** 아래의 **Select** 필드에서 다음 중 하나를 입력 합니다.

- 시스템 할당 관리 id: 이전에 검색 한를 입력 `principalId` 하거나 Batch 계정의 이름을 입력 합니다.
- 사용자 할당 관리 id: 이전에 검색 한 **클라이언트 ID** 또는 사용자 할당 관리 id의 이름을 입력 합니다.

![주 화면의 스크린샷.](./media/batch-customer-managed-key/principal-id.png)

### <a name="generate-a-key-in-azure-key-vault"></a>Azure Key Vault에서 새 키 생성

Azure Portal에서 **키** 섹션의 Key Vault 인스턴스로 이동 하 여 **생성/가져오기** 를 선택 합니다. **키 유형과** `RSA` **RSA 키 크기** 를 비트 이상으로 선택 합니다 `2048` . `EC` 키 유형은 현재 Batch 계정에서 고객 관리 키로 지원 되지 않습니다.

![키 만들기](./media/batch-customer-managed-key/create-key.png)

키를 만든 후 새로 만든 키와 현재 버전을 클릭 하 고 **속성** 섹션에서 **키 식별자** 를 복사 합니다.  **허용 된 작업** 아래에 **줄 바꿈** 키와 래핑 해제 **키** 가 모두 선택 되어 있는지 확인 합니다.

## <a name="enable-customer-managed-keys-on-a-batch-account"></a>Batch 계정에서 고객이 관리 하는 키 사용

위의 단계를 수행한 후에는 Batch 계정에서 고객이 관리 하는 키를 사용 하도록 설정할 수 있습니다.

### <a name="azure-portal"></a>Azure portal

[Azure Portal](https://portal.azure.com/)에서 Batch 계정 페이지로 이동 합니다. **암호화** 섹션에서 **고객이 관리** 하는 키를 사용 하도록 설정 합니다. 키 식별자를 직접 사용 하거나, 키 자격 증명 모음을 선택 하 고 키 **자격 증명 모음 및 키 선택** 을 클릭할 수 있습니다.

![암호화 섹션과 고객이 관리 하는 키를 사용 하도록 설정 하는 옵션을 보여 주는 스크린샷](./media/batch-customer-managed-key/encryption-page.png)

### <a name="azure-cli"></a>Azure CLI

시스템 할당 관리 id를 사용 하 여 Batch 계정을 만든 다음 Key Vault에 대 한 액세스 권한이 부여 되 면 매개 변수 아래의 URL을 사용 하 여 Batch 계정을 업데이트 `{Key Identifier}` `keyVaultProperties` 합니다. 또한 **encryption_key_source** 를로 설정 `Microsoft.KeyVault` 합니다.

```azurecli
az batch account set \
    -n $accountName \
    -g $resourceGroupName \
    --encryption_key_source Microsoft.KeyVault \
    --encryption_key_identifier {YourKeyIdentifier} 
```

## <a name="create-a-batch-account-with-user-assigned-managed-identity-and-customer-managed-keys"></a>사용자 할당 관리 id 및 고객이 관리 하는 키를 사용 하 여 Batch 계정 만들기

Batch 관리 .NET 클라이언트를 사용 하 여 사용자 할당 관리 id 및 고객이 관리 하는 키를 포함 하는 Batch 계정을 만들 수 있습니다.

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

## <a name="update-the-customer-managed-key-version"></a>고객 관리 키 버전 업데이트

새 버전의 키를 만드는 경우 새 버전을 사용 하도록 Batch 계정을 업데이트 합니다. 다음 단계를 수행합니다.

1. Azure Portal에서 배치 계정으로 이동 하 여 암호화 설정을 표시 합니다.
2. 새 키 버전의 URI를 입력 합니다. 또는 Key Vault를 선택 하 고 키를 다시 선택 하 여 버전을 업데이트할 수 있습니다.
3. 변경 내용을 저장합니다.

Azure CLI를 사용 하 여 버전을 업데이트할 수도 있습니다.

```azurecli
az batch account set \
    -n $accountName \
    -g $resourceGroupName \
    --encryption_key_identifier {YourKeyIdentifierWithNewVersion} 
```

## <a name="use-a-different-key-for-batch-encryption"></a>일괄 암호화에 다른 키 사용

일괄 암호화에 사용 되는 키를 변경 하려면 다음 단계를 수행 합니다.

1. Batch 계정으로 이동 하 여 암호화 설정을 표시 합니다.
2. 새 키에 대 한 URI를 입력 합니다. 또는 Key Vault를 선택 하 고 새 키를 선택할 수 있습니다.
3. 변경 내용을 저장합니다.

Azure CLI를 사용 하 여 다른 키를 사용할 수도 있습니다.

```azurecli
az batch account set \
    -n $accountName \
    -g $resourceGroupName \
    --encryption_key_identifier {YourNewKeyIdentifier} 
```

## <a name="frequently-asked-questions"></a>질문과 대답

- **고객 관리 키가 기존 Batch 계정에 대해 지원 되나요?** 아니요. 고객 관리 키는 새 Batch 계정에 대해서만 지원 됩니다.
- **RSA 키 크기를 2048 비트 보다 크게 선택할 수 있나요?** 예, 및 비트의 RSA 키 크기도 `3072` `4096` 지원 됩니다.
- **고객 관리형 키가 해지되면 어떤 작업을 수행할 수 있나요?** Batch에서 고객이 관리 하는 키에 대 한 액세스 권한을 상실 한 경우에만 허용 되는 작업입니다.
- **Key Vault 키를 실수로 삭제 한 경우 Batch 계정에 대 한 액세스를 복원 하려면 어떻게 해야 하나요?** 보호 제거 및 일시 삭제를 사용 하도록 설정 했으므로 기존 키를 복원할 수 있습니다. 자세한 내용은 [Azure Key Vault 복구](../key-vault/general/key-vault-recovery.md)를 참조 하세요.
- **고객 관리 키를 사용 하지 않도록 설정할 수 있나요?** 언제 든 지 Batch 계정의 암호화 유형을 "Microsoft 관리 키"로 다시 설정할 수 있습니다. 그 후에는 키를 삭제 하거나 변경할 수 있습니다.
- **내 키를 회전 하려면 어떻게 해야 하나요?** 고객 관리 키는 자동으로 회전 되지 않습니다. 키를 회전 하려면 계정이 연결 된 키 식별자를 업데이트 합니다.
- **액세스를 복원한 후 Batch 계정이 다시 작동 하는 데 걸리는 시간** 액세스가 복원 되 면 계정에 다시 액세스 하는 데 최대 10 분이 걸릴 수 있습니다.
- **Batch 계정을 사용할 수 없지만 내 리소스는 어떻게 되나요?** 고객 관리 키에 대 한 Batch 액세스 권한이 손실 될 때 실행 되는 모든 풀은 계속 실행 됩니다. 그러나 노드는 사용할 수 없는 상태로 전환 되 고 작업은 중단 되 고 다시 큐에 대기 됩니다. 액세스가 복원 되 면 노드를 다시 사용할 수 있게 되 고 태스크가 다시 시작 됩니다.
- **이 암호화 메커니즘은 Batch 풀의 VM 디스크에 적용 되나요?** 아니요. 클라우드 서비스 구성 풀의 경우 OS 및 임시 디스크에 대 한 암호화가 적용 되지 않습니다. 가상 컴퓨터 구성 풀의 경우 OS 및 지정 된 모든 데이터 디스크는 기본적으로 Microsoft 플랫폼 관리 키를 사용 하 여 암호화 됩니다. 현재는 이러한 디스크에 대 한 고유한 키를 지정할 수 없습니다. Microsoft platform 관리 키를 사용 하 여 Batch 풀에 대 한 Vm의 임시 디스크를 암호화 하려면 [가상 머신 구성](/rest/api/batchservice/pool/add#virtualmachineconfiguration) 풀에서 [diskencryptionconfiguration](/rest/api/batchservice/pool/add#diskencryptionconfiguration) 속성을 사용 하도록 설정 해야 합니다. 매우 중요 한 환경에서는 임시 디스크 암호화를 사용 하도록 설정 하 고 OS 및 데이터 디스크에 중요 한 데이터를 저장 하지 않는 것이 좋습니다. 자세한 내용은 [디스크 암호화를 사용 하 여 풀 만들기](./disk-encryption.md) 를 참조 하세요.
- **시스템 할당 관리 id가 계산 노드에서 사용할 수 있는 Batch 계정 입니까?** 아니요. 시스템 할당 관리 id는 현재 고객이 관리 하는 키에 대 한 Azure Key Vault에 액세스 하는 데만 사용 됩니다. 계산 노드에서 사용자 할당 관리 id를 사용 하려면 [Batch 풀에서 관리 되](managed-identity-pools.md)는 id 구성을 참조 하세요.

## <a name="next-steps"></a>다음 단계

- [Azure Batch의 보안 모범 사례](security-best-practices.md)에 대해 자세히 알아보세요.
- [Azure Key Vault](../key-vault/general/basic-concepts.md)에 대해 자세히 알아보세요.
