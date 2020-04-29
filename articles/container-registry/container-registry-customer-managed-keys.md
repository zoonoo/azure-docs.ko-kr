---
title: 고객 관리 키를 사용 하 여 미사용 암호화
description: Azure container registry의 미사용 암호화 및에 저장 된 고객이 관리 하는 키를 사용 하 여 레지스트리를 암호화 하는 방법에 대해 알아봅니다 Azure Key Vault
ms.topic: article
ms.date: 03/10/2020
ms.custom: ''
ms.openlocfilehash: 2d5561998cf0b19698c8059a861a4014a171a7e7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81461755"
---
# <a name="encryption-using-customer-managed-keys"></a>고객 관리 키를 사용 하 여 암호화

Azure container registry에 이미지 및 기타 아티팩트를 저장 하는 경우 Azure는 [서비스 관리 키](../security/fundamentals/encryption-atrest.md#data-encryption-models)를 사용 하 여 미사용 레지스트리 콘텐츠를 자동으로 암호화 합니다. Azure Key Vault에서 만들고 관리 하는 키를 사용 하 여 추가 암호화 계층으로 기본 암호화를 보완할 수 있습니다. 이 문서에서는 Azure CLI 및 Azure Portal를 사용 하는 단계를 안내 합니다.

고객 관리 키를 사용 하는 서버 쪽 암호화는 [Azure Key Vault](../key-vault/general/overview.md)와의 통합을 통해 지원 됩니다. 사용자 고유의 암호화 키를 만들고 Azure Key Vault에 저장할 수도 있고 Azure Key Vault의 API를 사용하여 암호화 키를 생성할 수도 있습니다. Azure Key Vault를 사용 하 여 키 사용을 감사할 수도 있습니다.

이 기능은 **프리미엄** 컨테이너 레지스트리 서비스 계층에서 사용할 수 있습니다. 레지스트리 서비스 계층 및 제한에 대 한 자세한 내용은 [Azure Container Registry sku](container-registry-skus.md)를 참조 하세요.

> [!IMPORTANT]
> 이 기능은 현재 미리 보기로 제공 되며 일부 [제한 사항이](#preview-limitations) 적용 됩니다. [부속 사용 약관][terms-of-use]에 동의하면 미리 보기를 사용할 수 있습니다. 이 기능의 몇 가지 측면은 일반 공급(GA) 전에 변경될 수 있습니다.
>
   
## <a name="preview-limitations"></a>미리 보기 제한 사항 

* 현재 레지스트리를 만들 때만이 기능을 사용 하도록 설정할 수 있습니다.
* 레지스트리에서 고객이 관리 하는 키를 사용 하도록 설정한 후에는 사용 하지 않도록 설정할 수 없습니다.
* [콘텐츠 트러스트](container-registry-content-trust.md) 는 현재 고객이 관리 하는 키로 암호화 된 레지스트리에서 지원 되지 않습니다.
* 고객이 관리 하는 키로 암호화 된 레지스트리에서 [ACR 태스크](container-registry-tasks-overview.md) 에 대 한 실행 로그는 현재 24 시간 동안만 보존 됩니다. 로그를 오랜 기간 동안 보관 해야 하는 경우 [작업 실행 로그를 내보내고 저장](container-registry-tasks-logs.md#alternative-log-storage)하는 지침을 참조 하세요.

## <a name="prerequisites"></a>전제 조건

이 문서의 Azure CLI 단계를 사용 하려면 2.2.0 이상 버전을 Azure CLI 해야 합니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

## <a name="enable-customer-managed-key---cli"></a>고객이 관리 하는 키-CLI 사용

### <a name="create-a-resource-group"></a>리소스 그룹 만들기

필요한 경우 [az group create][az-group-create] 명령을 실행 하 여 key vault, container registry 및 기타 필수 리소스를 만들기 위한 리소스 그룹을 만듭니다.

```azurecli
az group create --name <resource-group-name> --location <location>
```

### <a name="create-a-user-assigned-managed-identity"></a>사용자 할당 관리 ID 만들기

[Az identity create][az-identity-create] 명령을 사용 하 여 [Azure 리소스에 대 한 사용자 할당 관리 id](../active-directory/managed-identities-azure-resources/overview.md) 를 만듭니다. 이 id는 레지스트리에서 Key Vault 서비스에 액세스 하는 데 사용 됩니다.

```azurecli
az identity create \
  --resource-group <resource-group-name> \
  --name <managed-identity-name> 
```

명령 출력에서 `id` 및 `principalId`값을 기록해 둡니다. 이후 단계에서 키 자격 증명 모음에 대 한 레지스트리 액세스를 구성 하는 데 이러한 값이 필요 합니다.

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

편의를 위해 환경 변수에 다음 값을 저장 합니다.

```azurecli
identityID=$(az identity show --resource-group <resource-group-name> --name <managed-identity-name> --query 'id' --output tsv)

identityPrincipalID=$(az identity show --resource-group <resource-group-name> --name <managed-identity-name> --query 'principalId' --output tsv)
```

### <a name="create-a-key-vault"></a>키 자격 증명 모음 만들기

[Az keyvault create][az-keyvault-create] 를 사용 하 여 키 자격 증명 모음을 만들어 레지스트리 암호화를 위한 고객 관리 키를 저장 합니다. 

실수로 키 또는 키 자격 증명 모음을 삭제 하 여 발생 하는 데이터 손실을 방지 하려면 **일시 삭제** 및 **보호 제거**설정을 사용 하도록 설정 해야 합니다. 다음 예제에는 이러한 설정에 대 한 매개 변수가 포함 되어 있습니다. 

```azurecli
az keyvault create --name <key-vault-name> \
  --resource-group <resource-group-name> \
  --enable-soft-delete \
  --enable-purge-protection
```

### <a name="add-key-vault-access-policy"></a>키 자격 증명 모음 액세스 정책 추가

키 자격 증명 모음에 대 한 정책을 구성 하 여 id가 액세스할 수 있도록 합니다. 다음 [az keyvault set-policy][az-keyvault-set-policy] 명령에서 이전에 환경 변수에 저장 한 관리 id의 보안 주체 id를 전달 합니다. 키 사용 권한을 **get**, **unwrapKey**및 **wrapKey**로 설정 합니다.  

```azurecli
az keyvault set-policy \
  --resource-group <resource-group-name> \
  --name <key-vault-name> \
  --object-id $identityPrincipalID \
  --key-permissions get unwrapKey wrapKey 
```

### <a name="create-key-and-get-key-id"></a>키 만들기 및 키 ID 가져오기

[Az keyvault key create][az-keyvault-key-create] 명령을 실행 하 여 키 자격 증명 모음에 키를 만듭니다.

```azurecli
az keyvault key create \
  --name <key-name> \
  --vault-name <key-vault-name>
```

명령 출력에서 키의 ID를 `kid`기록해 둡니다. 다음 단계에서이 ID를 사용 합니다.

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
    "kid": "https://mykeyvault.vault.azure.net/keys/mykey/xxxxxxxxxxxxxxxxxxxxxxxx",
    "kty": "RSA",
[...]
```
편의를 위해이 값을 환경 변수에 저장 합니다.

```azurecli
keyID=$(az keyvault key show --name <keyname> --vault-name <key-vault-name> --query 'key.kid' --output tsv)
```

### <a name="create-a-registry-with-customer-managed-key"></a>고객 관리 키를 사용 하 여 레지스트리 만들기

[Az acr create][az-acr-create] 명령을 실행 하 여 레지스트리를 만들고 고객이 관리 하는 키를 사용 하도록 설정 합니다. 이전에 환경 변수에 저장 된 관리 되는 id 보안 주체 ID와 키 ID를 전달 합니다.

```azurecli
az acr create \
  --resource-group <resource-group-name> \
  --name <container-registry-name> \
  --identity $identityID \
  --key-encryption-key $keyID \
  --sku Premium
```

### <a name="show-encryption-status"></a>암호화 상태 표시

고객 관리 키를 사용 하 여 레지스트리 암호화를 사용 하도록 설정할지 여부를 표시 하려면 [az acr encryption show][az-acr-encryption-show] 명령을 실행 합니다.

```azurecli
az acr encryption show --name <registry-name> 
```

## <a name="enable-customer-managed-key---portal"></a>고객이 관리 하는 키 사용-포털

### <a name="create-a-managed-identity"></a>관리 id 만들기

Azure Portal에서 [Azure 리소스에 대 한 사용자 할당 관리 id](../active-directory/managed-identities-azure-resources/overview.md) 를 만듭니다. 단계는 [사용자 할당 Id 만들기](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)를 참조 하세요.

관리 id의 **리소스 이름을** 적어 둡니다. 이후 단계에서이 이름이 필요 합니다.

![Azure Portal에서 사용자 할당 관리 id 만들기](./media/container-registry-customer-managed-keys/create-managed-identity.png)

### <a name="create-a-key-vault"></a>키 자격 증명 모음 만들기

주요 자격 증명 모음을 만드는 단계는 [빠른 시작: Azure Portal를 사용 하 여 Azure Key Vault에서 비밀 설정 및 검색](../key-vault/secrets/quick-create-portal.md)을 참조 하세요.

고객 관리 키에 대 한 키 자격 증명 모음을 만들 때 **기본 사항** 탭에서 다음 보호 설정을 사용 하도록 설정 해야 합니다. **일시 삭제** 및 **보호 제거** 이러한 설정은 실수로 키 또는 키 자격 증명 모음 삭제로 인 한 데이터 손실을 방지 하는 데 도움이 됩니다.

![Azure Portal에서 키 자격 증명 모음을 만듭니다.](./media/container-registry-customer-managed-keys/create-key-vault.png)

### <a name="add-key-vault-access-policy"></a>키 자격 증명 모음 액세스 정책 추가

키 자격 증명 모음에 대 한 정책을 구성 하 여 id가 액세스할 수 있도록 합니다.

1. 키 자격 증명 모음으로 이동 합니다.
1. **설정** > **액세스 정책 > + 액세스 정책 추가**를 선택 합니다.
1. **키 사용 권한**을 선택 하 **고 가져오기**, **키 래핑**해제 및 **키 래핑**을 선택 합니다.
1. **보안 주체 선택** 을 선택 하 고 사용자 할당 관리 id의 리소스 이름을 선택 합니다.  
1. **추가**를 선택한 다음, **저장**을 선택 합니다.

![키 자격 증명 모음 액세스 정책 만들기](./media/container-registry-customer-managed-keys/add-key-vault-access-policy.png)

### <a name="create-key"></a>키 만들기

1. 키 자격 증명 모음으로 이동 합니다.
1. **설정** > **키**를 선택 합니다.
1. **+ 생성/가져오기** 를 선택 하 고 키에 대 한 고유한 이름을 입력 합니다.
1. 나머지 기본값을 그대로 적용 하 고 **만들기**를 선택 합니다.
1. 만든 후에는 키를 선택 하 고 현재 키 버전을 기록해 둡니다.

### <a name="create-azure-container-registry"></a>Azure 컨테이너 레지스트리 만들기

1. **리소스** > 만들기**컨테이너** > **Container Registry**를 선택 합니다.
1. **기본 사항** 탭에서 리소스 그룹을 선택 하거나 만들고 레지스트리 이름을 입력 합니다. **SKU**에서 **프리미엄**을 선택 합니다.
1. **암호화** 탭의 **고객 관리 키**에서 **사용**을 선택 합니다.
1. **Id**에서 사용자가 만든 관리 되는 id를 선택 합니다.
1. **암호화 키**에서 **Key Vault 선택**을 선택 합니다.
1. **Azure Key Vault에서 키 선택** 창에서 이전 섹션에서 만든 키 자격 증명 모음, 키 및 버전을 선택 합니다.
1. **암호화** 탭에서 **검토 + 만들기**를 선택 합니다.
1. **만들기** 를 선택 하 여 레지스트리 인스턴스를 배포 합니다.

![Azure Portal에서 컨테이너 레지스트리 만들기](./media/container-registry-customer-managed-keys/create-encrypted-registry.png)

포털에서 레지스트리의 암호화 상태를 확인 하려면 레지스트리로 이동 합니다. **설정**에서 **암호화 (미리 보기)** 를 선택 합니다.

## <a name="enable-customer-managed-key---template"></a>고객이 관리 하는 키 템플릿 사용

리소스 관리자 템플릿을 사용 하 여 레지스트리를 만들고 고객이 관리 하는 키를 사용 하 여 암호화를 사용 하도록 설정할 수도 있습니다. 

다음 템플릿은 새 컨테이너 레지스트리 및 사용자 할당 관리 id를 만듭니다. 다음 내용을 새 파일에 복사 하 고와 `CMKtemplate.json`같은 파일 이름을 사용 하 여 저장 합니다.

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

* 키 자격 증명 모음 (이름으로 식별 됨)
* 키 자격 증명 모음 키 (키 ID로 식별 됨)

다음 [az group deployment create][az-group-deployment-create] 명령을 실행 하 여 앞의 템플릿 파일을 사용 하 여 레지스트리를 만듭니다. 지정 된 경우 새 레지스트리 이름과 관리 id 이름을 제공 하 고 사용자가 만든 키 자격 증명 모음 이름 및 키 ID를 제공 합니다. 

```bash
az group deployment create \
  --resource-group <resource-group-name> \
  --template-file CMKtemplate.json \
  --parameters \
    registry_name=<registry-name> \
    identity_name=<managed-identity> \
    vault_name=<key-vault-name> \
    kek_id=<key-vault-key-id>
```

### <a name="show-encryption-status"></a>암호화 상태 표시

레지스트리 암호화의 상태를 표시 하려면 [az acr encryption show-status] [az-acr-encryption-show-status] 명령을 실행 합니다.

```azurecli
az acr encryption show-status --name <registry-name> 
```

## <a name="use-the-registry"></a>레지스트리 사용

고객이 관리 하는 키를 사용 하 여 데이터를 암호화 하는 레지스트리를 설정한 후에는 고객이 관리 하는 키로 암호화 되지 않은 레지스트리에서 수행 하는 것과 동일한 레지스트리 작업을 수행할 수 있습니다. 예를 들어 레지스트리 및 푸시 Docker 이미지를 사용 하 여 인증할 수 있습니다. [이미지 밀어넣기 및 끌어오기](container-registry-get-started-docker-cli.md)의 예제 명령을 참조 하세요.

## <a name="rotate-key"></a>키 회전

규정 준수 정책에 따라 Azure Key Vault에서 고객이 관리 하는 키를 회전할 수 있습니다. 새 키를 만든 다음 새 키를 사용 하 여 데이터를 암호화 하도록 레지스트리를 업데이트 합니다. Azure CLI 또는 포털을 사용 하 여 이러한 단계를 수행할 수 있습니다.

예를 들어 [az keyvault key create][az-keyvault-key-create] 명령을 실행 하 여 새 키를 만듭니다.

```azurecli
az keyvault key create –-name <new-key-name> --vault-name <key-vault-name> 
```

그런 다음 이전에 구성한 관리 되는 id의 새 키 ID와 보안 주체 ID를 전달 하 여 [az acr encryption rotate 키][az-acr-encryption-rotate-key] 명령을 실행 합니다.

```azurecli
az acr encryption rotatekey \
  --name <registry-name> \
  --key-encryption-key <new-key-id> \
  --identity $identityPrincipalID
```

## <a name="revoke-key"></a>키 취소

키 자격 증명 모음에 대 한 액세스 정책을 변경 하거나 키를 삭제 하 여 고객이 관리 하는 암호화 키를 해지 합니다. 예를 들어 [az keyvault delete-policy][az-keyvault-delete-policy] 명령을 사용 하 여 레지스트리에서 사용 되는 관리 id의 액세스 정책을 변경 합니다. 다음은 그 예입니다.

```azurecli
az keyvault delete-policy \
  --resource-group <resource-group-name> \
  --name <key-vault-name> \
  --object-id $identityPrincipalID
```

레지스트리는 암호화 키에 액세스할 수 없으므로 키를 해지 하면 모든 레지스트리 데이터에 대 한 액세스를 효과적으로 차단할 수 있습니다. 키에 대 한 액세스를 사용 하도록 설정 하거나 삭제 된 키를 복원 하는 경우 레지스트리는 암호화 된 레지스트리 데이터에 다시 액세스할 수 있도록 키를 선택 합니다.

## <a name="next-steps"></a>다음 단계

* [Azure에서 미사용 암호화](../security/fundamentals/encryption-atrest.md)에 대해 자세히 알아보세요.
* 액세스 정책 및 [키 자격 증명 모음에](../key-vault/general/secure-your-key-vault.md)대 한 액세스를 보호 하는 방법에 대해 자세히 알아보세요.
* Azure Container Registry에 대 한 고객 관리 키에 대 한 피드백을 제공 하려면 [ACR GitHub 사이트](https://aka.ms/acr/issues)를 방문 하세요.


<!-- LINKS - external -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms

<!-- LINKS - internal -->

[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-show]: /cli/azure/feature#az-feature-show
[az-group-create]: /cli/azure/group#az-group-create
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[az-keyvault-create]: /cli/azure/keyvault#az-keyvault-create
[az-keyvault-key-create]: /cli/azure/keyvault/key#az-keyvault-key-create
[az-keyvault-set-policy]: /cli/azure/keyvault#az-keyvault-set-policy
[az-keyvault-delete-policy]: /cli/azure/keyvault#az-keyvault-delete-policy
[az-resource-show]: /cli/azure/resource#az-resource-show
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-acr-encryption-rotate-key]: /cli/azure/acr/encryption#az-acr-encryption-rotate-key
[az-acr-encryption-show]: /cli/azure/acr/encryption#az-acr-encryption-show
