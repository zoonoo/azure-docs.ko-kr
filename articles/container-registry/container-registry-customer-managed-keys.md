---
title: 고객이 관리 하는 키를 사용 하 여 레지스트리 암호화
description: Azure container registry의 미사용 암호화 및에 저장 된 고객 관리 키를 사용 하 여 Premium registry를 암호화 하는 방법에 대해 알아봅니다 Azure Key Vault
ms.topic: article
ms.date: 11/17/2020
ms.custom: ''
ms.openlocfilehash: d145e861859d08b644683ea870a48fe9ef8fa459
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/21/2020
ms.locfileid: "95024844"
---
# <a name="encrypt-registry-using-a-customer-managed-key"></a>고객 관리형 키를 사용하여 레지스트리 암호화

이미지 및 기타 아티팩트를 Azure 컨테이너 레지스트리에 저장하면 Azure에서 [서비스 관리형 키](../security/fundamentals/encryption-models.md)를 사용하여 저장된 레지스트리 콘텐츠를 자동으로 암호화합니다. Azure Key Vault (고객이 관리 하는 키)에서 만들고 관리 하는 키를 사용 하 여 추가 암호화 계층으로 기본 암호화를 보완할 수 있습니다. 이 문서에서는 Azure CLI 및 Azure Portal을 사용하는 단계를 안내합니다.

고객 관리 키를 사용 하는 서버 쪽 암호화는 [Azure Key Vault](../key-vault/general/overview.md)와의 통합을 통해 지원 됩니다. 

* 사용자 고유의 암호화 키를 만들어 키 자격 증명 모음에 저장하거나 Azure Key Vault의 API를 사용하여 키를 생성할 수 있습니다. 
* Azure Key Vault를 사용하면 키 사용을 감사할 수도 있습니다.
* Azure Key Vault에서 새 키 버전을 사용할 수 있는 경우 Azure Container Registry는 레지스트리 암호화 키의 자동 회전을 지원 합니다. 레지스트리 암호화 키를 수동으로 회전할 수도 있습니다.

이 기능은 **프리미엄** 컨테이너 레지스트리 서비스 계층에서 사용할 수 있습니다. 레지스트리 서비스 계층 및 제한에 대한 자세한 내용은 [Azure Container Registry 서비스 계층](container-registry-skus.md)을 참조하세요.


## <a name="things-to-know"></a>알아야 할 사항

* 고객 관리형 키는 현재 레지스트리를 만들 때만 사용하도록 설정할 수 있습니다. 키를 사용 하도록 설정 하는 경우 *사용자 할당* 관리 id를 구성 하 여 키 자격 증명 모음에 액세스 합니다.
* 레지스트리에서 고객이 관리 하는 키를 사용 하 여 암호화를 사용 하도록 설정한 후에는 암호화를 사용 하지 않도록 설정할 수 없습니다.  
* Azure Container Registry는 RSA 또는 RSA-HSM 키만 지원 합니다. 타원 curve 키는 현재 지원 되지 않습니다.
* [콘텐츠 신뢰](container-registry-content-trust.md)는 현재 고객 관리형 키로 암호화된 레지스트리에서 지원되지 않습니다.
* 고객 관리형 키로 암호화된 레지스트리에서 [ACR 작업](container-registry-tasks-overview.md)에 대한 실행 로그는 현재 24시간 동안만 유지됩니다. 로그를 더 오랜 기간 동안 유지해야 하는 경우 [작업 실행 로그 내보내기 및 저장](container-registry-tasks-logs.md#alternative-log-storage)에 대한 지침을 참조하세요.


> [!NOTE]
> [Key Vault 방화벽](../key-vault/general/network-security.md)을 사용 하는 가상 네트워크를 사용 하 여 Azure key vault에 대 한 액세스를 제한 하는 경우 추가 구성 단계가 필요 합니다. 레지스트리를 만들고 고객이 관리 하는 키를 사용 하도록 설정한 후에는 레지스트리의 *시스템 할당* 관리 id를 사용 하 여 키에 대 한 액세스를 설정 하 고 Key Vault 방화벽을 우회 하도록 레지스트리를 구성 합니다. 먼저이 문서의 단계에 따라 고객이 관리 하는 키를 사용 하 여 암호화를 사용 하도록 설정한 다음 [고급 시나리오](#advanced-scenario-key-vault-firewall) 에 대 한 지침을 참조 하세요 .이 문서의 뒷부분에서 방화벽을 Key Vault 합니다.

## <a name="automatic-or-manual-update-of-key-versions"></a>키 버전 자동 또는 수동 업데이트

고객이 관리 하는 키를 사용 하 여 암호화 된 레지스트리 보안에 대 한 중요 한 고려 사항은 암호화 키를 업데이트 (회전) 하는 빈도입니다. 조직에는 고객이 관리 하는 키로 사용 될 때 Azure Key Vault에 저장 된 키 [버전](../key-vault/general/about-keys-secrets-certificates.md#objects-identifiers-and-versioning) 을 정기적으로 업데이트 해야 하는 규정 준수 정책이 있을 수 있습니다. 

고객 관리 키를 사용 하 여 레지스트리 암호화를 구성 하는 경우 암호화에 사용 되는 키 버전을 업데이트 하는 두 가지 옵션이 있습니다.

* **자동으로 키 버전 업데이트** -Azure Key Vault에서 새 버전을 사용할 수 있을 때 고객 관리 키를 자동으로 업데이트 하려면 고객이 관리 하는 키를 사용 하 여 레지스트리 암호화를 사용 하도록 설정할 때 키 버전을 생략 합니다. 레지스트리를 버전이 없는 키로 암호화 하는 경우 Azure Container Registry는 키 자격 증명 모음에서 새 키 버전을 정기적으로 확인 하 고 1 시간 이내에 고객이 관리 하는 키를 업데이트 합니다. Azure Container Registry는 키의 최신 버전을 자동으로 사용 합니다.

* **수동으로 키 버전 업데이트** -레지스트리 암호화에 특정 키 버전을 사용 하려면 고객이 관리 하는 키를 사용 하 여 레지스트리 암호화를 사용 하도록 설정할 때 해당 키 버전을 지정 합니다. 특정 키 버전을 사용 하 여 레지스트리를 암호화 하는 경우 고객이 관리 하는 키를 수동으로 회전할 때까지 해당 버전을 암호화에 사용 Azure Container Registry 합니다.

> [!NOTE]
> 현재는 Azure CLI만 사용 하 여 고객이 관리 하는 키 버전을 자동으로 업데이트 하도록 레지스트리를 구성할 수 있습니다. 포털을 사용 하 여 암호화를 사용 하는 경우 키 버전을 수동으로 업데이트 해야 합니다.

자세한 내용은이 문서의 뒷부분에 나오는 키 [버전을 사용 하거나 사용 하지 않고 키 ID 선택](#choose-key-id-with-or-without-key-version) 및 키 [버전 업데이트](#update-key-version)를 참조 하세요.

## <a name="prerequisites"></a>필수 조건

이 문서의 Azure CLI 단계를 사용 하려면 버전이 2.2.0 이상 이거나 Azure Cloud Shell Azure CLI 필요 합니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

## <a name="enable-customer-managed-key---cli"></a>고객 관리형 키 사용 - CLI

### <a name="create-a-resource-group"></a>리소스 그룹 만들기

필요한 경우 [az group create][az-group-create] 명령을 실행하여 키 자격 증명 모음, 컨테이너 레지스트리 및 기타 필요한 리소스를 만들기 위한 리소스 그룹을 만듭니다.

```azurecli
az group create --name <resource-group-name> --location <location>
```

### <a name="create-a-user-assigned-managed-identity"></a>사용자 할당 관리 ID 만들기

[az identity create][az-identity-create] 명령을 사용하여 [Azure 리소스에 대해 사용자가 할당한 관리 ID](../active-directory/managed-identities-azure-resources/overview.md)를 만듭니다. 이 ID는 레지스트리에서 Key Vault 서비스에 액세스하는 데 사용됩니다.

```azurecli
az identity create \
  --resource-group <resource-group-name> \
  --name <managed-identity-name>
```

명령 출력에서 `id` 및 `principalId` 값을 적어 둡니다. 이러한 값은 이후 단계에서 키 자격 증명 모음에 대한 레지스트리 액세스를 구성하는 데 필요합니다.

```JSON
{
  "clientId": "xxxx2bac-xxxx-xxxx-xxxx-192cxxxx6273",
  "clientSecretUrl": "https://control-eastus.identity.azure.net/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myresourcegroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myidentityname/credentials?tid=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx&oid=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx&aid=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myresourcegroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myresourcegroup",
  "location": "eastus",
  "name": "myidentityname",
  "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "resourceGroup": "myresourcegroup",
  "tags": {},
  "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.ManagedIdentity/userAssignedIdentities"
}
```

편의를 위해 이러한 값을 환경 변수에 저장합니다.

```azurecli
identityID=$(az identity show --resource-group <resource-group-name> --name <managed-identity-name> --query 'id' --output tsv)

identityPrincipalID=$(az identity show --resource-group <resource-group-name> --name <managed-identity-name> --query 'principalId' --output tsv)
```

### <a name="create-a-key-vault"></a>키 자격 증명 모음 만들기

[az keyvault create][az-keyvault-create]를 사용하여 키 자격 증명 모음을 만들어 레지스트리 암호화를 위한 고객 관리형 키를 저장합니다.

기본적으로 **일시 삭제** 설정은 새 key vault에서 자동으로 사용 하도록 설정 됩니다. 실수로 키 또는 키 자격 증명 모음 삭제로 인 한 데이터 손실을 방지 하려면 **보호 제거** 설정도 사용 하도록 설정 합니다.

```azurecli
az keyvault create --name <key-vault-name> \
  --resource-group <resource-group-name> \
  --enable-purge-protection
```

이후 단계에서 사용 하기 위해 key vault의 리소스 Id를 가져옵니다.

```azurecli
keyvaultID=$(az keyvault show --resource-group <resource-group-name> --name <key-vault-name> --query 'id' --output tsv)
```

### <a name="enable-key-vault-access"></a>키 자격 증명 모음 액세스 사용

ID에서 액세스할 수 있도록 키 자격 증명 모음에 대한 정책을 구성합니다. 다음 [az keyvault set-policy][az-keyvault-set-policy] 명령에서는 이전에 만들어 환경 변수에 저장한 관리 ID의 보안 주체 ID를 전달합니다. 키 권한을 **get**, **unwrapKey** 및 **wrapKey** 로 설정합니다.  

```azurecli
az keyvault set-policy \
  --resource-group <resource-group-name> \
  --name <key-vault-name> \
  --object-id $identityPrincipalID \
  --key-permissions get unwrapKey wrapKey
```

또는 Key Vault (미리 보기) [용 AZURE RBAC](../key-vault/general/rbac-guide.md) 를 사용 하 여 id에 키 자격 증명 모음에 액세스할 수 있는 권한을 할당 합니다. 예를 들어 [az role assign create](/cli/azure/role/assignment#az-role-assignment-create) 명령을 사용 하 여 Key Vault Crypto 서비스 암호화 역할을 id에 할당 합니다.

```azurecli 
az role assignment create --assignee $identityPrincipalID \
  --role "Key Vault Crypto Service Encryption (preview)" \
  --scope $keyvaultID
```

### <a name="create-key-and-get-key-id"></a>키 만들기 및 키 ID 가져오기

[az keyvault key create][az-keyvault-key-create] 명령을 실행하여 키를 키 자격 증명 모음에 만듭니다.

```azurecli
az keyvault key create \
  --name <key-name> \
  --vault-name <key-vault-name>
```

명령 출력에서 키의 ID(`kid`)를 적어 둡니다. 이 ID는 다음 단계에서 사용합니다.

```JSON
[...]
  "key": {
    "crv": null,
    "d": null,
    "dp": null,
    "dq": null,
    "e": "AQAB",
    "k": null,
    "keyOps": [
      "encrypt",
      "decrypt",
      "sign",
      "verify",
      "wrapKey",
      "unwrapKey"
    ],
    "kid": "https://mykeyvault.vault.azure.net/keys/mykey/<version>",
    "kty": "RSA",
[...]
```

### <a name="choose-key-id-with-or-without-key-version"></a>키 버전을 사용 하거나 사용 하지 않고 키 ID를 선택 합니다.

편의를 위해 $keyID 환경 변수에서 키 ID에 대해 선택한 형식을 저장 합니다. 버전 또는 키가 없는 키 ID를 사용할 수 있습니다.

#### <a name="manual-key-rotation---key-id-with-version"></a>수동 키 회전-버전을 포함 한 키 ID

고객 관리 키를 사용 하 여 레지스트리를 암호화 하는 데 사용 되는 경우이 키를 사용 하면 Azure Container Registry의 수동 키 회전이 허용 됩니다.

이 예제에서는 키의 속성을 저장 합니다 `kid` .

```azurecli
keyID=$(az keyvault key show \
  --name <keyname> \
  --vault-name <key-vault-name> \
  --query 'key.kid' --output tsv)
```

#### <a name="automatic-key-rotation---key-id-omitting-version"></a>자동 키 회전-키 ID 생략 버전 

고객 관리 키를 사용 하 여 레지스트리를 암호화 하는 데 사용 되는 경우이 키는 Azure Key Vault에서 새 키 버전이 검색 되 면 자동 키 회전을 사용 합니다.

이 예에서는 키의 속성에서 버전을 제거 합니다 `kid` .

```azurecli
keyID=$(az keyvault key show \
  --name <keyname> \
  --vault-name <key-vault-name> \
  --query 'key.kid' --output tsv)

keyID=$(echo $keyID | sed -e "s/\/[^/]*$//")
```

### <a name="create-a-registry-with-customer-managed-key"></a>고객 관리형 키를 사용하여 레지스트리 만들기

[az acr create][az-acr-create] 명령을 실행하여 레지스트리를 프리미엄 서비스 계층에 만들고 고객 관리형 키를 사용하도록 설정합니다. 이전에 환경 변수에 저장 된 관리 id ID와 키 ID를 전달 합니다.

```azurecli
az acr create \
  --resource-group <resource-group-name> \
  --name <container-registry-name> \
  --identity $identityID \
  --key-encryption-key $keyID \
  --sku Premium
```

### <a name="show-encryption-status"></a>암호화 상태 표시

고객 관리형 키를 사용하는 레지스트리 암호화를 사용하도록 설정되었는지 여부를 표시하려면 [az acr encryption show][az-acr-encryption-show] 명령을 실행합니다.

```azurecli
az acr encryption show --name <registry-name>
```

레지스트리를 암호화 하는 데 사용 되는 키에 따라 출력은 다음과 유사 합니다.

```console
{
  "keyVaultProperties": {
    "identity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "keyIdentifier": "https://myvault.vault.azure.net/keys/myresourcegroup/abcdefg123456789...",
    "versionedKeyIdentifier": "https://myvault.vault.azure.net/keys/myresourcegroup/abcdefg123456789...",
    "keyRotationEnabled": true,
    "lastKeyRotationTimestamp": xxxxxxxx
  },
  "status": "enabled"
}
```

## <a name="enable-customer-managed-key---portal"></a>고객 관리형 키 사용 - 포털

### <a name="create-a-managed-identity"></a>관리 ID 만들기

Azure Portal에서 [Azure 리소스에 대해 사용자가 할당한 관리 ID](../active-directory/managed-identities-azure-resources/overview.md)를 만듭니다. 단계는 [사용자 할당 ID 만들기](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)를 참조하세요.

ID 이름은 이후 단계에서 사용합니다.

:::image type="content" source="media/container-registry-customer-managed-keys/create-managed-identity.png" alt-text="Azure Portal에서 사용자가 할당한 ID 만들기":::

### <a name="create-a-key-vault"></a>키 자격 증명 모음 만들기

주요 자격 증명 모음을 만드는 단계는 [빠른 시작: Azure Portal를 사용 하 여 Azure Key Vault 만들기](../key-vault/general/quick-create-portal.md)를 참조 하세요.

고객 관리 키에 대 한 키 자격 증명 모음을 만들 때 **기본 사항** 탭에서 **보호 제거** 설정을 사용 하도록 설정 합니다. 이 설정은 실수로 키 또는 키 자격 증명 모음 삭제로 인 한 데이터 손실을 방지 하는 데 도움이 됩니다.

:::image type="content" source="media/container-registry-customer-managed-keys/create-key-vault.png" alt-text="Azure Portal에서 키 자격 증명 모음 만들기":::

### <a name="enable-key-vault-access"></a>키 자격 증명 모음 액세스 사용

ID에서 액세스할 수 있도록 키 자격 증명 모음에 대한 정책을 구성합니다.

1. 키 자격 증명 모음으로 이동합니다.
1. **설정** > **액세스 정책 > + 액세스 정책 추가** 를 차례로 선택합니다.
1. **키 권한** 을 선택하고, **가져오기**, **키 래핑 해제** 및 **키 래핑** 을 선택합니다.
1. **보안 주체 선택** 에서 사용자 할당 관리 id의 리소스 이름을 선택 합니다.  
1. **추가**, **저장** 을 차례로 선택합니다.

:::image type="content" source="media/container-registry-customer-managed-keys/add-key-vault-access-policy.png" alt-text="키 자격 증명 모음 액세스 정책 만들기":::

또는 Key Vault (미리 보기) [용 AZURE RBAC](../key-vault/general/rbac-guide.md) 를 사용 하 여 id에 키 자격 증명 모음에 액세스할 수 있는 권한을 할당 합니다. 예를 들어 Key Vault Crypto 서비스 암호화 역할을 id에 할당 합니다.

1. 키 자격 증명 모음으로 이동합니다.
1. **액세스 제어 (IAM)**  >  **+ 추가**  >  **역할 할당** 추가를 선택 합니다.
1. **역할 할당 추가** 창에서 다음을 수행 합니다.
    1. **Key Vault Crypto 서비스 암호화 (미리 보기)** 역할을 선택 합니다. 
    1. **사용자 할당 관리 id** 에 대 한 액세스 권한을 할당 합니다.
    1. 사용자 할당 관리 id의 리소스 이름을 선택 하 고 **저장** 을 선택 합니다.

### <a name="create-key"></a>키 만들기

1. 키 자격 증명 모음으로 이동합니다.
1. **설정** > **키** 를 차례로 선택합니다.
1. **+ 생성/가져오기** 를 선택하고, 키에 대한 고유한 이름을 입력합니다.
1. 나머지 기본값을 그대로 적용하고 **만들기** 를 선택합니다.
1. 만들어지면 키를 선택하고 현재 키 버전을 적어 둡니다.

### <a name="create-azure-container-registry"></a>Azure 컨테이너 레지스트리 만들기

1. **리소스 만들기** > **컨테이너** > **컨테이너 레지스트리** 를 선택합니다.
1. **기본 사항** 탭에서 리소스 그룹을 선택하거나 만들고, 레지스트리 이름을 입력합니다. **SKU** 에서 **프리미엄** 을 선택합니다.
1. **암호화** 탭의 **고객 관리형 키** 에서 **사용** 을 선택합니다.
1. **ID** 에서 사용자가 만든 관리 ID를 선택합니다.
1. **암호화** 에서 **Key Vault에서 선택** 을 선택합니다.
1. **Azure Key Vault에서 키 선택** 창에서 이전 섹션에서 만든 키 자격 증명 모음, 키 및 버전을 선택합니다.
1. **암호화** 탭에서 **검토 + 만들기** 를 선택합니다.
1. **만들기** 를 선택 하 여 레지스트리 인스턴스를 만듭니다.

:::image type="content" source="media/container-registry-customer-managed-keys/create-encrypted-registry.png" alt-text="Azure Portal에서 암호화 된 레지스트리 만들기":::

포털에서 레지스트리의 암호화 상태를 확인하려면 레지스트리로 이동합니다. **설정** 아래에서 **암호화** 를 선택합니다.

## <a name="enable-customer-managed-key---template"></a>고객 관리형 키 사용 - 템플릿

Resource Manager 템플릿을 사용하여 레지스트리를 만들고 고객 관리형 키를 사용하는 암호화를 사용하도록 설정할 수도 있습니다.

다음 템플릿은 새 컨테이너 레지스트리 및 사용자가 할당한 관리 ID를 만듭니다. 다음 내용을 새 파일에 복사하고 `CMKtemplate.json`과 같은 파일 이름을 사용하여 저장합니다.

```JSON
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vault_name": {
      "defaultValue": "",
      "type": "String"
    },
    "registry_name": {
      "defaultValue": "",
      "type": "String"
    },
    "identity_name": {
      "defaultValue": "",
      "type": "String"
    },
    "kek_id": {
      "type": "String"
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.ContainerRegistry/registries",
      "apiVersion": "2019-12-01-preview",
      "name": "[parameters('registry_name')]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Premium",
        "tier": "Premium"
      },
      "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
          "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name'))]": {}
        }
      },
      "dependsOn": [
        "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name'))]"
      ],
      "properties": {
        "adminUserEnabled": false,
        "encryption": {
          "status": "enabled",
          "keyVaultProperties": {
            "identity": "[reference(resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name')), '2018-11-30').clientId]",
            "KeyIdentifier": "[parameters('kek_id')]"
          }
        },
        "networkRuleSet": {
          "defaultAction": "Allow",
          "virtualNetworkRules": [],
          "ipRules": []
        },
        "policies": {
          "quarantinePolicy": {
            "status": "disabled"
          },
          "trustPolicy": {
            "type": "Notary",
            "status": "disabled"
          },
          "retentionPolicy": {
            "days": 7,
            "status": "disabled"
          }
        }
      }
    },
    {
      "type": "Microsoft.KeyVault/vaults/accessPolicies",
      "apiVersion": "2018-02-14",
      "name": "[concat(parameters('vault_name'), '/add')]",
      "dependsOn": [
        "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name'))]"
      ],
      "properties": {
        "accessPolicies": [
          {
            "tenantId": "[subscription().tenantId]",
            "objectId": "[reference(resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name')), '2018-11-30').principalId]",
            "permissions": {
              "keys": [
                "get",
                "unwrapKey",
                "wrapKey"
              ]
            }
          }
        ]
      }
    },
    {
      "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
      "apiVersion": "2018-11-30",
      "name": "[parameters('identity_name')]",
      "location": "[resourceGroup().location]"
    }
  ]
}
```

이전 섹션의 단계에 따라 다음 리소스를 만듭니다.

* 키 자격 증명 모음(이름으로 식별됨)
* 키 자격 증명 모음 키(키 ID로 식별됨)

다음 [az deployment group create][az-deployment-group-create] 명령을 실행 하 여 이전 템플릿 파일을 사용 하 여 레지스트리를 만듭니다. 표시되면 새 레지스트리 이름 및 관리 ID 이름과 사용자가 만든 키 자격 증명 모음 이름 및 키 ID를 제공합니다.

```bash
az deployment group create \
  --resource-group <resource-group-name> \
  --template-file CMKtemplate.json \
  --parameters \
    registry_name=<registry-name> \
    identity_name=<managed-identity> \
    vault_name=<key-vault-name> \
    kek_id=<key-vault-key-id>
```

### <a name="show-encryption-status"></a>암호화 상태 표시

레지스트리 암호화 상태를 표시하려면 [az acr encryption show][az-acr-encryption-show] 명령을 실행합니다.

```azurecli
az acr encryption show --name <registry-name>
```

## <a name="use-the-registry"></a>레지스트리 사용

레지스트리에서 고객 관리형 키를 사용하도록 설정되면 고객 관리형 키로 암호화되지 않은 레지스트리에서 수행하는 것과 동일한 레지스트리 작업을 수행할 수 있습니다. 예를 들어 레지스트리로 인증하고 Docker 이미지를 푸시할 수 있습니다. [이미지 밀어넣기 및 끌어오기](container-registry-get-started-docker-cli.md)의 명령 예제를 참조하세요.

## <a name="rotate-key"></a>키 회전

Azure Key Vault에서 키 버전을 업데이트 하거나 새 키를 만든 다음 키를 사용 하 여 레지스트리를 업데이트 하 여 데이터를 암호화 합니다. 이러한 단계는 Azure CLI 또는 포털을 사용하여 수행할 수 있습니다.

키를 회전시키는 경우 일반적으로 레지스트리를 만들 때 사용되는 것과 동일한 ID를 지정합니다. 필요에 따라 키 액세스를 위해 사용자가 할당한 새 ID를 구성하거나 레지스트리의 시스템이 할당한 ID를 사용하도록 설정하고 지정합니다.

> [!NOTE]
> 키 액세스용으로 구성 하는 id에 대 한 필수 [키 자격 증명 모음 액세스 권한이](#enable-key-vault-access) 설정 되어 있는지 확인 합니다.

### <a name="update-key-version"></a>키 버전 업데이트

일반적인 시나리오는 고객이 관리 하는 키로 사용 되는 키의 버전을 업데이트 하는 것입니다. 레지스트리 암호화를 구성 하는 방법에 따라 Azure Container Registry에서 고객이 관리 하는 키가 자동으로 업데이트 되거나 수동으로 업데이트 되어야 합니다.

### <a name="azure-cli"></a>Azure CLI

[az keyvault key][az-keyvault-key] 명령을 사용하여 키 자격 증명 모음 키를 만들거나 관리합니다. 새 키 버전을 만들려면 [az keyvault key create][az-keyvault-key-create] 명령을 실행 합니다.

```azurecli
# Create new version of existing key
az keyvault key create \
  –-name <key-name> \
  --vault-name <key-vault-name>
```

다음 단계는 레지스트리 암호화를 구성 하는 방법에 따라 달라 집니다.

* 레지스트리 키 버전 업데이트를 검색 하도록 구성 된 경우에는 고객이 관리 하는 키가 1 시간 이내에 자동으로 업데이트 됩니다.

* 새 키 버전의 수동 업데이트를 요구 하도록 레지스트리를 구성 하는 경우에는 [az acr encryption 회전-키][az-acr-encryption-rotate-key] 명령을 실행 하 여 구성 하려는 새 키 id와 id를 전달 합니다.

고객 관리 키 버전을 수동으로 업데이트 하려면 다음을 수행 합니다.

```azurecli
# Rotate key and use user-assigned identity
az acr encryption rotate-key \
  --name <registry-name> \
  --key-encryption-key <new-key-id> \
  --identity <principal-id-user-assigned-identity>

# Rotate key and use system-assigned identity
az acr encryption rotate-key \
  --name <registry-name> \
  --key-encryption-key <new-key-id> \
  --identity [system]
```

> [!TIP]
> 를 실행할 때 `az acr encryption rotate-key` 버전이 지정 된 키 id 또는 버전이 없는 키 id를 전달할 수 있습니다. 버전이 지정 되지 않은 키 ID를 사용 하는 경우 레지스트리는 나중에 키 버전 업데이트를 자동으로 검색 하도록 구성 됩니다.

### <a name="portal"></a>포털

레지스트리의 **암호화** 설정을 사용 하 여 고객 관리 키에 사용 되는 주요 자격 증명 모음, 키 또는 id 설정을 업데이트 합니다.

예를 들어 새 키를 구성 하려면 다음을 수행 합니다.

1. 포털에서 레지스트리로 이동합니다.
1. **설정** 아래에서 **암호화** > **키 변경** 을 차례로 선택합니다.
1. **키 선택** 을 선택 합니다.

    :::image type="content" source="media/container-registry-customer-managed-keys/rotate-key.png" alt-text="Azure Portal에서 키 회전":::
1. **Azure Key Vault에서 키 선택** 창에서 이전에 구성한 키 자격 증명 모음 및 키를 선택하고, **버전** 에서 **새로 만들기** 를 선택합니다.
1. **키 만들기** 창에서 **생성**, **만들기** 를 차례로 선택합니다.
1. 키 선택을 완료하고, **저장** 을 선택합니다.

## <a name="revoke-key"></a>키 철회

키 자격 증명 모음에 대 한 액세스 정책 또는 사용 권한을 변경 하거나 키를 삭제 하 여 고객이 관리 하는 암호화 키를 해지 합니다. 예를 들어 [az keyvault delete-policy][az-keyvault-delete-policy] 명령을 사용하여 레지스트리에서 사용하는 관리 ID의 액세스 정책을 변경합니다.

```azurecli
az keyvault delete-policy \
  --resource-group <resource-group-name> \
  --name <key-vault-name> \
  --object-id $identityPrincipalID
```

레지스트리는 암호화 키에 액세스할 수 없으므로 키를 철회하면 모든 레지스트리 데이터에 대한 액세스가 효과적으로 차단됩니다. 키에 대한 액세스를 사용하도록 설정되거나 삭제된 키가 복원되는 경우 레지스트리에서 암호화된 레지스트리 데이터에 다시 액세스할 수 있도록 키를 선택합니다.

## <a name="advanced-scenario-key-vault-firewall"></a>고급 시나리오: Key Vault 방화벽

Azure 주요 자격 증명 모음이 Key Vault 방화벽을 사용 하는 가상 네트워크에 배포 된 경우 레지스트리에서 고객이 관리 하는 키 암호화를 사용 하도록 설정한 후 다음 단계를 추가로 수행 합니다.

1. 레지스트리의 시스템이 할당 한 id를 사용 하도록 레지스트리 암호화 구성
1. 레지스트리가 Key Vault 방화벽을 우회 하도록 설정
1. 고객 관리 키 회전

### <a name="configure-system-assigned-identity"></a>시스템 할당 id 구성

암호화 키에 대한 키 자격 증명 모음에 액세스하도록 레지스트리의 시스템이 할당한 관리 ID를 구성할 수 있습니다. Azure 리소스에 대한 다양한 관리 ID에 익숙하지 않은 경우 [개요](../active-directory/managed-identities-azure-resources/overview.md)를 참조하세요.

포털에서 레지스트리의 시스템이 할당한 ID를 사용하도록 설정하려면 다음을 수행합니다.

1. 포털에서 레지스트리로 이동합니다.
1. **설정** >  **ID** 를 차례로 선택합니다.
1. **시스템 할당** 아래에서 **상태** 를 **켜기** 로 설정합니다. **저장** 을 선택합니다.
1. ID의 **개체 ID** 를 복사합니다.

키 자격 증명 모음에 대한 ID 액세스 권한을 부여하려면 다음을 수행합니다.

1. 키 자격 증명 모음으로 이동합니다.
1. **설정** > **액세스 정책 > + 액세스 정책 추가** 를 차례로 선택합니다.
1. **키 권한** 을 선택하고, **가져오기**, **키 래핑 해제** 및 **키 래핑** 을 선택합니다.
1. **보안 주체 선택** 을 선택 하 고 시스템 할당 관리 id의 개체 ID 또는 레지스트리 이름을 검색 합니다.  
1. **추가**, **저장** 을 차례로 선택합니다.

ID를 사용하도록 레지스트리의 암호화 설정을 업데이트하려면 다음을 수행합니다.

1. 포털에서 레지스트리로 이동합니다.
1. **설정** 아래에서 **암호화** > **키 변경** 을 차례로 선택합니다.
1. **ID** 에서 **시스템 할당** 을 선택하고, **저장** 을 선택합니다.

### <a name="enable-key-vault-bypass"></a>키 자격 증명 모음 바이패스 사용

Key Vault 방화벽을 사용 하 여 구성 된 key vault에 액세스 하려면 레지스트리에서 방화벽을 무시 해야 합니다. 키 자격 증명 모음이 신뢰할 수 있는 [서비스](../key-vault/general/overview-vnet-service-endpoints.md#trusted-services)의 액세스를 허용 하도록 구성 되어 있는지 확인 합니다. Azure Container Registry는 신뢰할 수 있는 서비스 중 하나입니다.

1. 포털에서 키 자격 증명 모음으로 이동 합니다.
1. **설정**  >  **네트워킹** 을 선택 합니다.
1. 가상 네트워크 설정을 확인, 업데이트 또는 추가 합니다. 자세한 단계는 [Azure Key Vault 방화벽 및 가상 네트워크 구성](../key-vault/general/network-security.md)을 참조하세요.
1. **Microsoft 신뢰할 수 있는 서비스에서이 방화벽을 무시 하도록 허용** 에서 **예** 를 선택 합니다. 

### <a name="rotate-the-customer-managed-key"></a>고객 관리 키 회전

이전 단계를 완료 한 후 키를 방화벽 뒤에 있는 key vault의 새 키로 회전 합니다. 단계는이 문서의 [회전 키](#rotate-key) 를 참조 하세요.

## <a name="troubleshoot"></a>문제 해결

### <a name="removing-user-assigned-identity"></a>사용자 할당 id 제거

암호화에 사용 되는 레지스트리에서 사용자 할당 id를 제거 하려고 하면 다음과 유사한 오류 메시지가 표시 될 수 있습니다.
 
```
Azure resource '/subscriptions/xxxx/resourcegroups/myGroup/providers/Microsoft.ContainerRegistry/registries/myRegistry' does not have access to identity 'xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx' Try forcibly adding the identity to the registry <registry name>. For more information on bring your own key, please visit 'https://aka.ms/acr/cmk'.
```
 
또한 암호화 키를 변경 (회전) 할 수 없습니다. 이 문제가 발생 하면 먼저 오류 메시지에 표시 된 GUID를 사용 하 여 id를 다시 할당 합니다. 예를 들면 다음과 같습니다.

```azurecli
az acr identity assign -n myRegistry --identities xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx
```
        
그런 다음 키를 변경 하 고 다른 id를 할당 한 후에 원래 사용자 할당 id를 제거할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [Azure에서 저장 데이터 암호화](../security/fundamentals/encryption-atrest.md)에 대해 자세히 알아봅니다.
* 액세스 정책 및 [키 자격 증명 모음에 대한 액세스를 보호하는 방법](../key-vault/general/secure-your-key-vault.md)에 대해 자세히 알아봅니다.


<!-- LINKS - external -->

<!-- LINKS - internal -->

[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-show]: /cli/azure/feature#az-feature-show
[az-group-create]: /cli/azure/group#az-group-create
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-deployment-group-create]: /cli/azure/deployment/group#az-deployment-group-create
[az-keyvault-create]: /cli/azure/keyvault#az-keyvault-create
[az-keyvault-key-create]: /cli/azure/keyvault/key#az-keyvault-key-create
[az-keyvault-key]: /cli/azure/keyvault/key
[az-keyvault-set-policy]: /cli/azure/keyvault#az-keyvault-set-policy
[az-keyvault-delete-policy]: /cli/azure/keyvault#az-keyvault-delete-policy
[az-resource-show]: /cli/azure/resource#az-resource-show
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-acr-encryption-rotate-key]: /cli/azure/acr/encryption#az-acr-encryption-rotate-key
[az-acr-encryption-show]: /cli/azure/acr/encryption#az-acr-encryption-show
