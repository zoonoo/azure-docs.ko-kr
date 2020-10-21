---
title: ARM 템플릿을 사용 하 여 Azure key vault 및 자격 증명 모음 액세스 정책 만들기
description: 이 문서에서는 Azure Resource Manager 템플릿을 사용 하 여 Azure 키 자격 증명 모음 및 자격 증명 모음 액세스 정책을 만드는 방법을 보여 줍니다.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 10/5/2020
ms.author: mbaldwin
ms.openlocfilehash: 1f62e0b3a40382c911cd07c777c521adb3649c4d
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92282324"
---
# <a name="how-to-create-an-azure-key-vault-and-vault-access-policy-by-using-a-resource-manager-template"></a>리소스 관리자 템플릿을 사용 하 여 Azure key vault 및 자격 증명 모음 액세스 정책을 만드는 방법

[Azure Key Vault](../general/overview.md) 는 키, 암호 및 인증서와 같은 암호에 대 한 보안 저장소를 제공 하는 클라우드 서비스입니다. 이 문서에서는 키 자격 증명 모음을 만들기 위해 Azure Resource Manager 템플릿 (ARM 템플릿)을 배포 하는 프로세스를 설명 합니다.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>필수 구성 요소

이 문서의 단계를 완료하려면 다음을 수행합니다.

* Azure 구독이 없는 경우 시작 하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 을 만듭니다.


## <a name="create-a-key-vault-resource-manager-template"></a>Key Vault 리소스 관리자 템플릿 만들기

다음 템플릿은 주요 자격 증명 모음을 만드는 기본적인 방법을 보여 줍니다. 일부 값은 템플릿에 지정 됩니다.

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
> 템플릿을 다시 배포 하는 경우 키 자격 증명 모음에 있는 기존 액세스 정책이 모두 재정의 됩니다. `accessPolicies`키 자격 증명 모음에 대 한 액세스를 잃지 않으려면 기존 액세스 정책으로 속성을 채우는 것이 좋습니다. 

## <a name="add-an-access-policy-to-a-key-vault-resource-manager-template"></a>Key Vault 리소스 관리자 템플릿에 액세스 정책 추가

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
템플릿 설정 Key Vault에 대 한 자세한 내용은 [ARM 템플릿 참조 Key Vault](https://docs.microsoft.com/azure/templates/microsoft.keyvault/vaults/accesspolicies)를 참조 하세요.

## <a name="more-key-vault-resource-manager-templates"></a>템플릿 추가 Key Vault 리소스 관리자

Key Vault 개체에 사용할 수 있는 다른 리소스 관리자 템플릿이 있습니다.

| 비밀 | 구성 | 인증서 |
|--|--|--|
|<ul><li>[빠른 시작](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-template)<li>[참조](https://docs.microsoft.com/azure/templates/microsoft.keyvault/vaults/secrets)|해당 없음|해당 없음|

[Key Vault 리소스 관리자 참조](https://docs.microsoft.com/azure/templates/microsoft.keyvault/allversions)에서 더 많은 Key Vault 템플릿을 찾을 수 있습니다.

## <a name="deploy-the-templates"></a>템플릿 배포

Azure Portal를 사용 하 여 [사용자 지정 템플릿에서 리소스 배포](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-portal#deploy-resources-from-custom-template)에 설명 된 대로 **편집기에서 사용자 고유의 템플릿 빌드** 옵션을 사용 하 여 위의 템플릿을 배포할 수 있습니다.

위의 템플릿을 파일에 저장 하 고 다음 명령을 사용할 수도 있습니다.  [AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) 및 [az group deployment create](/cli/azure/group/deployment#az-group-deployment-create):

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile key-vault-template.json
```

```azurecli
az group deployment create --resource-group ExampleGroup --template-file key-vault-template.json
```

## <a name="clean-up-resources"></a>리소스 정리

후속 빠른 시작 및 자습서를 계속 진행 하려는 경우 이러한 리소스를 그대로 둘 수 있습니다. 리소스가 더 이상 필요 하지 않은 경우 리소스 그룹을 삭제 합니다. 그룹을 삭제 하면 주요 자격 증명 모음 및 관련 리소스도 삭제 됩니다. Azure CLI 또는 Azure PowerShell를 사용 하 여 리소스 그룹을 삭제 하려면 다음 단계를 완료 합니다.

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

- [Azure Key Vault 개요](../general/overview.md)를 참조 하세요.
- [Azure Resource Manager](../../azure-resource-manager/management/overview.md)에 대해 자세히 알아보세요.
- [Azure Key Vault 모범 사례](../general/best-practices.md)를 검토 합니다.

## <a name="next-steps"></a>다음 단계

- [Key vault에 대한 액세스 보안](secure-your-key-vault.md)
- [키 자격 증명 모음에 인증](authentication.md)
- [Azure Key Vault 개발자 가이드](developers-guide.md)
