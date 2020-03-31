---
title: 고객이 관리하는 키로 미사용 암호화
description: Azure 컨테이너 레지스트리의 나머지 부분 암호화 및 Azure Key Vault에 저장된 고객 관리 키로 레지스트리를 암호화하는 방법에 대해 알아봅니다.
ms.topic: article
ms.date: 03/10/2020
ms.custom: ''
ms.openlocfilehash: fe0736f83db2ba5b872d50bcf1262ca423de9f09
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79498955"
---
# <a name="encryption-using-customer-managed-keys"></a>고객 관리 키를 사용한 암호화

Azure 컨테이너 레지스트리에 이미지 및 기타 아티팩트를 저장할 때 Azure는 서비스 관리 [키로](../security/fundamentals/encryption-atrest.md#data-encryption-models)미사용 레지스트리 콘텐츠를 자동으로 암호화합니다. Azure Key Vault에서 만들고 관리하는 키를 사용하여 기본 암호화를 추가 암호화 계층으로 보완할 수 있습니다. 이 문서에서는 Azure CLI 및 Azure 포털을 사용하는 단계를 안내합니다.

[Azure Key Vault와의](../key-vault/key-vault-overview.md)통합을 통해 고객 관리 키가 있는 서버 측 암호화가 지원됩니다. 사용자 고유의 암호화 키를 만들고 Azure Key Vault에 저장할 수도 있고 Azure Key Vault의 API를 사용하여 암호화 키를 생성할 수도 있습니다. Azure 키 자격 증명 모음을 사용하면 키 사용량을 감사할 수도 있습니다.

이 기능은 **프리미엄** 컨테이너 레지스트리 서비스 계층에서 사용할 수 있습니다. 레지스트리 서비스 계층 및 제한에 대한 자세한 내용은 [Azure 컨테이너 레지스트리 SCO를](container-registry-skus.md)참조하십시오.

> [!IMPORTANT]
> 이 기능은 현재 미리 보기 중이며 일부 [제한 사항이](#preview-limitations) 적용됩니다. [추가 사용 조건][terms-of-use]에 동의하는 조건으로 미리 보기를 사용할 수 있습니다. 이 기능의 몇 가지 측면은 일반 공급(GA) 전에 변경될 수 있습니다.
>
   
## <a name="preview-limitations"></a>미리 보기 제한 사항 

* 현재 레지스트리를 만들 때만 이 기능을 활성화할 수 있습니다.
* 레지스트리에서 고객 관리 키를 사용하도록 설정한 후에는 비활성화할 수 없습니다.
* [콘텐츠 신뢰는](container-registry-content-trust.md) 현재 고객 관리 키로 암호화된 레지스트리에서 지원되지 않습니다.
* 고객 관리 키로 암호화된 레지스트리에서 [ACR 작업에](container-registry-tasks-overview.md) 대한 실행 로그는 현재 24시간 동안만 유지됩니다. 더 긴 기간 동안 로그를 유지해야 하는 경우 [작업 실행 로그를 내보내고 저장하는 지침을 참조하세요.](container-registry-tasks-logs.md#alternative-log-storage)

## <a name="prerequisites"></a>사전 요구 사항

이 문서에서 Azure CLI 단계를 사용하려면 Azure CLI 버전 2.2.0 이상이 필요합니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하십시오.

## <a name="enable-customer-managed-key---cli"></a>고객 관리 키 사용 - CLI

### <a name="create-a-resource-group"></a>리소스 그룹 만들기

필요한 경우 [az 그룹 만들기][az-group-create] 명령을 실행하여 키 자격 증명 모음, 컨테이너 레지스트리 및 기타 필수 리소스를 만들기 위한 리소스 그룹을 만듭니다.

```azurecli
az group create --name <resource-group-name> --location <location>
```

### <a name="create-a-user-assigned-managed-identity"></a>사용자 할당 관리 ID 만들기

az ID create 명령을 사용하여 Azure 리소스에 대해 사용자 지정 [관리되는 ID를](../active-directory/managed-identities-azure-resources/overview.md) [만듭니다.][az-identity-create] 이 ID는 레지스트리에서 Key Vault 서비스에 액세스하는 데 사용됩니다.

```azurecli
az identity create \
  --resource-group <resource-group-name> \
  --name <managed-identity-name> 
```

명령 출력에서 다음 값을 `id` 기록합니다. `principalId` 키 자격 증명 모음에 대한 레지스트리 액세스를 구성하려면 이후 단계에서 이러한 값이 필요합니다.

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

편의를 위해 환경 변수에 이러한 값을 저장합니다.

```azurecli
identityID=$(az identity show --resource-group <resource-group-name> --name <managed-identity-name> --query 'id' --output tsv)

identityPrincipalID=$(az identity show --resource-group <resource-group-name> --name <managed-identity-name> --query 'principalId' --output tsv)
```

### <a name="create-a-key-vault"></a>키 자격 증명 모음 만들기

[az keyvault를 사용하여][az-keyvault-create] 키 자격 증명 모음을 만들어 레지스트리 암호화를 위해 고객 관리 키를 저장합니다. 

실수로 키 또는 키 볼트 삭제로 인한 데이터 손실을 방지하려면 **소프트 삭제** 및 **지우기 보호**의 다음 설정을 사용하도록 설정해야 합니다. 다음 예제에는 이러한 설정에 대한 매개 변수가 포함되어 있습니다. 

```azurecli
az keyvault create --name <key-vault-name> \
  --resource-group <resource-group-name> \
  --enable-soft-delete \
  --enable-purge-protection
```

### <a name="add-key-vault-access-policy"></a>키 자격 증명 모음 액세스 정책 추가

ID가 액세스할 수 있도록 키 자격 증명 모음에 대한 정책을 구성합니다. 다음 [az keyvault 집합 정책][az-keyvault-set-policy] 명령에서는 이전에 환경 변수에 저장한 관리되는 ID의 주 ID를 전달합니다. 키 권한을 **설정하여 ,** **WrapKey**및 **wrapKey**를 가져옵니다.  

```azurecli
az keyvault set-policy \
  --resource-group <resource-group-name> \
  --name <key-vault-name> \
  --object-id $identityPrincipalID \
  --key-permissions get unwrapKey wrapKey 
```

### <a name="create-key-and-get-key-id"></a>키 만들기 및 키 ID 받기

az [키볼트 만들기][az-keyvault-key-create] 명령을 실행하여 키 자격 증명 모음에 키를 만듭니다.

```azurecli
az keyvault key create \
  --name <key-name> \
  --vault-name <key-vault-name>
```

명령 출력에서 키의 ID를 기록해 `kid`둡자 수 있습니다. 다음 단계에서 이 ID를 사용합니다.

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
편의를 위해 환경 변수에 이 값을 저장합니다.

```azurecli
keyID=$(az keyvault key show --name <keyname> --vault-name <key-vault-name> --query 'key.kid' --output tsv)
```

### <a name="create-a-registry-with-customer-managed-key"></a>고객 관리 키로 레지스트리 만들기

az [acr create][az-acr-create] 명령을 실행하여 레지스트리를 만들고 고객 관리 키를 활성화합니다. 환경 변수에 이전에 저장된 관리되는 ID 보안 주체 ID와 키 ID를 전달합니다.

```azurecli
az acr create \
  --resource-group <resource-group-name> \
  --name <container-registry-name> \
  --identity $identityID \
  --key-encryption-key $keyID \
  --sku Premium
```

### <a name="show-encryption-status"></a>암호화 상태 표시

고객 관리 키가 있는 레지스트리 암호화가 활성화되어 있는지 여부를 표시하려면 [az acr 암호화 표시][az-acr-encryption-show] 명령을 실행합니다.

```azurecli
az acr encryption show --name <registry-name> 
```

## <a name="enable-customer-managed-key---portal"></a>고객 관리 키 - 포털 사용

### <a name="create-a-managed-identity"></a>관리되는 ID 만들기

Azure 포털에서 Azure 리소스에 대해 사용자 지정 [관리되는 ID를](../active-directory/managed-identities-azure-resources/overview.md) 만듭니다. 단계의 경우 [사용자 할당ID 만들기를](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)참조하십시오.

관리되는 ID의 **리소스 이름을** 기록해 둡니다. 이후 단계에서 이 이름이 필요합니다.

![Azure 포털에서 사용자 할당된 관리되는 ID 만들기](./media/container-registry-customer-managed-keys/create-managed-identity.png)

### <a name="create-a-key-vault"></a>키 자격 증명 모음 만들기

키 자격 증명 모음을 만드는 단계는 [빠른 시작: Azure 포털을 사용하여 Azure 키 자격 증명 모음에서 비밀을 설정하고 검색하는](../key-vault/quick-create-portal.md)것을 참조합니다.

고객 관리 키에 대한 키 자격 증명 모음을 만들 때 **기본** 탭에서 **다음** **Purge protection**보호 설정을 사용하도록 설정해야 합니다. 이러한 설정은 실수로 키 또는 키 볼트 삭제로 인한 데이터 손실을 방지하는 데 도움이 됩니다.

![Azure 포털에서 키 자격 증명 모음 만들기](./media/container-registry-customer-managed-keys/create-key-vault.png)

### <a name="add-key-vault-access-policy"></a>키 자격 증명 모음 액세스 정책 추가

ID가 액세스할 수 있도록 키 자격 증명 모음에 대한 정책을 구성합니다.

1. 키 자격 증명 모음으로 이동합니다.
1. **설정** > **액세스 정책 > +액세스 정책**추가 를 선택합니다.
1. **키 사용 권한을**선택하고 **받기**, 키 **줄 바꿈**및 키 줄 **바꿈을**선택합니다.
1. **보안 주체 선택을** 선택하고 사용자가 할당한 관리 ID의 리소스 이름을 선택합니다.  
1. **추가를**선택한 다음 **저장을**선택합니다.

![키 자격 증명 모음 액세스 정책 만들기](./media/container-registry-customer-managed-keys/add-key-vault-access-policy.png)

### <a name="create-key"></a>키 만들기

1. 키 자격 증명 모음으로 이동합니다.
1. **설정** > **키를 선택합니다.**
1. **+생성/가져오기를** 선택하고 키에 대한 고유한 이름을 입력합니다.
1. 나머지 기본값을 수락하고 **을 선택합니다.**
1. 생성 후 키를 선택하고 현재 키 버전을 기록해 둡을 기록합니다.

### <a name="create-azure-container-registry"></a>Azure 컨테이너 레지스트리 만들기

1. **리소스** > 컨테이너 컨테이너**레지스트리**만들기를**선택합니다.** > 
1. 기본 탭에서 리소스 그룹을 선택하거나 만들고 레지스트리 이름을 **입력합니다.** **SKU에서** **프리미엄**을 선택합니다.
1. **암호화** 탭에서 **고객 관리 키에서** **사용 을**선택합니다.
1. **ID에서**만든 관리되는 ID를 선택합니다.
1. **암호화 키에서** **키 자격 증명 모음에서 선택합니다.**
1. Azure **키 볼트 창에서 키 선택 창에서** 이전 섹션에서 만든 키 자격 증명 모음, 키 및 버전을 선택합니다.
1. **암호화** 탭에서 **검토 + 만들기를**선택합니다.
1. 레지스트리 인스턴스를 배포하려면 **만들기를** 선택합니다.

![Azure Portal에서 컨테이너 레지스트리 만들기](./media/container-registry-customer-managed-keys/create-encrypted-registry.png)

포털에서 레지스트리의 암호화 상태를 보려면 레지스트리로 이동합니다. **설정에서** **암호화(미리 보기)를 선택합니다.**

## <a name="enable-customer-managed-key---template"></a>고객 관리 키 사용 - 템플릿

리소스 관리자 템플릿을 사용하여 레지스트리를 만들고 고객 관리 키를 사용하여 암호화를 활성화할 수도 있습니다. 

다음 템플릿은 새 컨테이너 레지스트리와 사용자가 할당한 관리되는 ID를 만듭니다. 다음 내용을 새 파일에 복사하고 `CMKtemplate.json`와 같은 파일 이름을 사용하여 저장합니다.

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

이전 섹션의 단계를 수행하여 다음 리소스를 만듭니다.

* 이름으로 식별된 키 자격 증명 모음
* 키 ID로 식별된 키 자격 증명 모음 키

다음 [az 그룹 배포 만들기][az-group-deployment-create] 명령을 실행하여 이전 템플릿 파일을 사용하여 레지스트리를 만듭니다. 표시된 경우 새 레지스트리 이름과 관리되는 ID 이름, 만든 키 자격 증명 모음 이름 및 키 ID를 제공합니다. 

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

레지스트리 암호화 상태를 표시하려면 [az acr 암호화 표시 상태][az-acr-암호화-표시 상태] 명령을 실행합니다.

```azurecli
az acr encryption show-status --name <registry-name> 
```

## <a name="use-the-registry"></a>레지스트리 사용

레지스트리에서 고객 관리 키를 사용하여 데이터를 암호화하도록 설정하면 고객 관리 키로 암호화되지 않은 레지스트리에서 수행하는 것과 동일한 레지스트리 작업을 수행할 수 있습니다. 예를 들어 레지스트리를 통해 인증하고 Docker 이미지를 푸시할 수 있습니다. [푸시 및 끌어오기 의](container-registry-get-started-docker-cli.md)예제 명령을 참조하십시오.

## <a name="rotate-key"></a>키 회전

규정 준수 정책에 따라 Azure Key Vault에서 고객 관리 키를 회전할 수 있습니다. 새 키를 만든 다음 레지스트리를 업데이트하여 새 키를 사용하여 데이터를 암호화합니다. Azure CLI 또는 포털에서 이러한 단계를 수행할 수 있습니다.

예를 들어 [az 키볼트 만들기][az-keyvault-key-create] 명령을 실행하여 새 키를 만듭니다.

```azurecli
az keyvault key create –-name <new-key-name> --vault-name <key-vault-name> 
```

그런 다음 [az acr 암호화 회전 키][az-acr-encryption-rotate-key] 명령을 실행하여 이전에 구성한 관리 ID의 새 키 ID와 주 ID를 전달합니다.

```azurecli
az acr encryption rotatekey \
  --name <registry-name> \
  --key-encryption-key <new-key-id> \
  --identity $identityPrincipalID
```

## <a name="revoke-key"></a>키 취소

키 자격 증명 모음의 액세스 정책을 변경하거나 키를 삭제하여 고객이 관리하는 암호화 키를 해지합니다. 예를 들어 [az keyvault 삭제 정책][az-keyvault-delete-policy] 명령을 사용하여 레지스트리에서 사용하는 관리되는 ID의 액세스 정책을 변경합니다. 예를 들어:

```azurecli
az keyvault delete-policy \
  --resource-group <resource-group-name> \
  --name <key-vault-name> \
  --object-id $identityPrincipalID
```

레지스트리가 암호화 키에 액세스할 수 없으므로 키를 취소하면 모든 레지스트리 데이터에 대한 액세스가 효과적으로 차단됩니다. 키에 대한 액세스가 활성화되었거나 삭제된 키가 복원되면 레지스트리에서 키를 선택하여 암호화된 레지스트리 데이터에 다시 액세스할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [Azure에서 사용 중 암호화에](../security/fundamentals/encryption-atrest.md)대해 자세히 알아봅니다.
* 액세스 정책 및 키 [자격 증명 모음에 대한 액세스를 보호하는](../key-vault/key-vault-secure-your-key-vault.md)방법에 대해 자세히 알아봅니다.
* Azure 컨테이너 레지스트리에 대한 고객 관리 키에 대한 피드백을 제공하려면 [ACR GitHub 사이트를](https://aka.ms/acr/issues)방문하십시오.


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
