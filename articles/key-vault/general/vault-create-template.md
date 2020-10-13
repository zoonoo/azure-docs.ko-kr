---
title: Azure 가이드-Azure Resource Manager 템플릿을 사용 하 여 Azure key vault 및 자격 증명 모음 액세스 정책 만들기 | Microsoft Docs
description: Azure Resource Manager 템플릿을 사용 하 여 Azure 키 자격 증명 모음 및 자격 증명 모음 액세스 정책을 만드는 방법을 보여 줍니다.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 10/5/2020
ms.author: mbaldwin
ms.openlocfilehash: cf19561005fe2e98b7b5cf6812ff9224fd9474dc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91804395"
---
# <a name="how-to-create-azure-key-vault-and-vault-access-policy-using-a-resource-manager-template"></a>리소스 관리자 템플릿을 사용 하 여 Azure Key Vault 및 자격 증명 모음 액세스 정책을 만드는 방법

[Azure Key Vault](../general/overview.md)는 키, 암호, 인증서 및 기타 비밀 등, 비밀에 안전한 자격 증명을 제공하는 클라우드 서비스입니다. 이 가이드에서는 키 자격 증명 모음을 만들기 위해 Azure Resource Manager 템플릿 (ARM 템플릿)을 배포 하는 과정을 중점적으로 설명 합니다.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>필수 구성 요소

이 문서를 완료하려면 다음이 필요합니다.

* Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.


## <a name="create-key-vault-resource-manager-template"></a>Key Vault 리소스 관리자 템플릿 만들기

다음 템플릿은 주요 자격 증명 모음을 만드는 기본적인 방법을 보여 줍니다. 일부 값은 템플릿 내에서 지정됩니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "keyVaultName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the key vault."
      }
    },
    "skuName": {
      "type": "string",
      "defaultValue": "Standard",
      "allowedValues": [
        "Standard",
        "Premium"
      ],
      "metadata": {
        "description": "Specifies whether the key vault is a standard vault or a premium vault."
      }
    }
   },
  "resources": [
    {
      "type": "Microsoft.KeyVault/vaults",
      "apiVersion": "2019-09-01",
      "name": "[parameters('keyVaultName')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "enabledForDeployment": "false",
        "enabledForDiskEncryption": "false",
        "enabledForTemplateDeployment": "false",
        "tenantId": "[subscription().tenantId]",
        "accessPolicies": [],
        "sku": {
          "name": "[parameters('skuName')]",
          "family": "A"
        },
        "networkAcls": {
          "defaultAction": "Allow",
          "bypass": "AzureServices"
        }
      }
    }
  ]
}

```

Key Vault 템플릿 설정에 대 한 자세한 내용은 [ARM 템플릿 참조 Key Vault](https://docs.microsoft.com/azure/templates/microsoft.keyvault/vaults)를 참조 하세요.

> [!IMPORTANT]
> 템플릿을 다시 배포 하는 경우 키 자격 증명 모음에 있는 기존 액세스 정책을 모두 재정의 합니다. `accessPolicies`키 자격 증명 모음에 대 한 끊어질 액세스를 방지 하기 위해 속성을 기존 액세스 정책으로 채우는 것이 좋습니다. 

## <a name="add-access-policy-to-key-vault-resource-manager-template"></a>Key Vault 리소스 관리자 템플릿에 액세스 정책 추가

전체 키 자격 증명 모음 템플릿을 다시 배포 하지 않고 기존 키 자격 증명 모음에 액세스 정책을 배포할 수 있습니다. 다음 템플릿에서는 액세스 정책을 만드는 기본적인 방법을 보여 줍니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "keyVaultName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the key vault."
      }
    },
    "objectId": {
      "type": "string",
      "metadata": {
        "description": "Specifies the object ID of a user, service principal or security group in the Azure Active Directory tenant for the vault. The object ID must be unique for the list of access policies. Get it by using Get-AzADUser or Get-AzADServicePrincipal cmdlets."
      }
    },
    "keysPermissions": {
      "type": "array",
      "defaultValue": [
        "list"
      ],
      "metadata": {
        "description": "Specifies the permissions to keys in the vault. Valid values are: all, encrypt, decrypt, wrapKey, unwrapKey, sign, verify, get, list, create, update, import, delete, backup, restore, recover, and purge."
      }
    },
    "secretsPermissions": {
      "type": "array",
      "defaultValue": [
        "list"
      ],
      "metadata": {
        "description": "Specifies the permissions to secrets in the vault. Valid values are: all, get, list, set, delete, backup, restore, recover, and purge."
      }
    },
    "certificatePermissions": {
      "type": "array",
      "defaultValue": [
        "list"
      ],
      "metadata": {
        "description": "Specifies the permissions to certificates in the vault. Valid values are: all,  create, delete, update, deleteissuers, get, getissuers, import, list, listissuers, managecontacts, manageissuers,  recover, backup, restore, setissuers, and purge."
      }
    },
  "resources": [
     {
      "type": "Microsoft.KeyVault/vaults/accessPolicies",
      "name": "[concat(parameters('keyVaultName'), '/add')]",
      "apiVersion": "2019-09-01",
      "properties": {
        "accessPolicies": [
          {
            "tenantId": "[subscription().tenantId]",
            "objectId": "[parameters('objectId')]",
            "permissions": {
              "keys": "[parameters('keysPermissions')]",
              "secrets": "[parameters('secretsPermissions')]",
              "certificates": [parameters('certificatesPermissions')]
            }
          }
        ]
      }
    }
  ]
}

```
Key Vault 템플릿 설정에 대 한 자세한 내용은 [ARM 템플릿 참조 Key Vault](https://docs.microsoft.com/azure/templates/microsoft.keyvault/vaults/accesspolicies)를 참조 하세요.

## <a name="other-available-key-vault-resource-manager-templates"></a>기타 사용 가능한 Key Vault 리소스 관리자 템플릿

Key Vault 개체에 사용할 수 있는 다른 리소스 관리자 템플릿이 있습니다.

| 비밀 | 구성 | 인증서 |
|--|--|--|
|[빠른 시작](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-template)<br>[참조](https://docs.microsoft.com/azure/templates/microsoft.keyvault/vaults/secrets)|N/A|N/A|

[Key Vault 리소스 관리자 참조](https://docs.microsoft.com/azure/templates/microsoft.keyvault/allversions) 에서 찾을 수 있는 Key Vault 템플릿이 더 있습니다.

## <a name="deploy-the-templates"></a>템플릿 배포

이 Azure Portal 사용 하 여 아래 가이드의 ' 편집기에서 고유한 템플릿 빌드 ' 옵션을 사용 하 여 위의 템플릿을 배포할 수 있습니다. [사용자 지정 템플릿에서 리소스 배포](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-portal#deploy-resources-from-custom-template)

위의 템플릿을 파일에 저장 하 고 다음 명령을 사용할 수도 있습니다.  [AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) 및 [az group deployment create](/cli/azure/group/deployment#az-group-deployment-create):

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile key-vault-template.json
```

```azurecli
az group deployment create --resource-group ExampleGroup --template-file key-vault-template.json
```

## <a name="clean-up-resources"></a>리소스 정리

후속 빠른 시작 및 자습서를 계속 진행 하려는 경우 이러한 리소스를 그대로 둘 수 있습니다. 리소스가 더 이상 필요 하지 않으면 리소스 그룹을 삭제 합니다. 그러면 키 자격 증명 모음 및 관련 리소스가 삭제 됩니다. Azure CLI 또는 Azure PowerShell를 사용 하 여 리소스 그룹을 삭제 하려면 다음 단계를 사용 합니다.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="resources"></a>리소스

- [Azure Key Vault 개요](../general/overview.md) 참조
- [Azure Resource Manager](../../azure-resource-manager/management/overview.md)에 대해 자세히 알아보기
- [Azure Key Vault 모범 사례](../general/best-practices.md) 검토

## <a name="next-steps"></a>다음 단계

- [Key vault에 대한 액세스 보안](secure-your-key-vault.md)
- [키 자격 증명 모음에 인증](authentication.md)
- [Azure Key Vault 개발자 가이드](developers-guide.md)
