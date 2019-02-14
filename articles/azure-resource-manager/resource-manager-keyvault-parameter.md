---
title: Azure Resource Manager 템플릿에서 Key Vault 비밀 | Microsoft Docs
description: 배포하는 동안 키 자격 증명 모음의 비밀을 매개 변수로 전달하는 방법을 보여 줍니다.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/30/2019
ms.author: tomfitz
ms.openlocfilehash: cd45384bfd548cb73c10306dfee942cbcf7c8ca1
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55491912"
---
# <a name="use-azure-key-vault-to-pass-secure-parameter-value-during-deployment"></a>Azure Key Vault를 사용하여 배포 중에 보안 매개 변수 값 전달

매개 변수 파일에 암호 등의 보안 값을 직접 포함하는 대신 배포 중에 [Azure Key Vault](../key-vault/key-vault-whatis.md)에서 값을 검색할 수 있습니다. 매개 변수 파일에서 Key Vault 및 비밀을 참조하여 이 값을 검색합니다. 해당 Key Vault ID만 참조하므로 이 값은 절대 노출되지 않습니다. Key Vault는 배포하는 리소스 그룹과는 다른 구독에 있을 수 있습니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="deploy-key-vaults-and-secrets"></a>키 자격 증명 모음 및 비밀 배포

Key Vault를 만들고 비밀을 추가하려면 다음 항목을 참조하세요.

- [CLI를 사용하여 비밀 설정 및 검색](../key-vault/quick-create-cli.md)
- [PowerShell을 사용하여 비밀 설정 및 검색](../key-vault/quick-create-powershell.md)
- [포털을 사용하여 비밀 설정 및 검색](../key-vault/quick-create-portal.md)
- [.NET을 사용하여 비밀 설정 및 검색](../key-vault/quick-create-net.md)
- [Node.js를 사용하여 비밀 설정 및 검색](../key-vault/quick-create-node.md)

Key Vault를 Resource Manager 템플릿 배포와 통합할 때는 추가로 몇 가지 사항을 고려해야 하며 요구 사항을 충족해야 합니다.

- `enabledForTemplateDeployment`는 Key Vault 속성입니다. 리소스 관리자 배치에서 이 Key Vault의 비밀에 액세스하려면 `enabledForTemplateDeployment`이 `true`여야 합니다. 
- Key Vault 소유자가 아닌 경우에는 소유자가 Key Vault 보안 정책 설정을 업데이트해야 비밀을 추가할 수 있습니다.

다음 Azure CLI 및 Azure PowerShell 샘플에서는 이 작업을 수행하는 방법을 보여 줍니다.

```azurecli
# Create a Key Vault
az keyvault create \
  --name $keyVaultName \
  --resource-group $resourceGroupName \
  --location $location \
  --enabled-for-template-deployment true
az keyvault set-policy --upn $userPrincipalName --name $keyVaultName --secret-permissions set delete get list
```

```azurepowershell
New-AzKeyVault `
  -VaultName $keyVaultName `
  -resourceGroupName $resourceGroupName `
  -Location $location `
  -EnabledForTemplateDeployment
Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -UserPrincipalName $userPrincipalName -PermissionsToSecrets set,delete,get,list
```

## <a name="grant-access-to-the-secrets"></a>비밀 액세스 권한 부여

템플릿을 배포하는 사용자에게는 Key Vault가 포함된 범위에 대한 `Microsoft.KeyVault/vaults/deploy/action` 권한(리소스 그룹 및 Key Vault에 대한 권한 포함)이 있어야 합니다. [소유자](../role-based-access-control/built-in-roles.md#owner) 및 [참여자](../role-based-access-control/built-in-roles.md#contributor) 역할 모두 이 액세스 권한을 부여합니다. Key Vault를 만드는 경우 소유자가 되며 권한을 갖게 됩니다. Key Vault가 다른 구독에 속하는 경우 Key Vault의 소유자에게 액세스 권한을 부여해야 합니다.

다음 절차는 최소의 권한을 가진 역할을 만드는 방법과 사용자에게 할당하는 방법을 나타냅니다.

1. 사용자 지정 역할 정의 JSON 파일 만들기

    ```json
    {
      "Name": "Key Vault resource manager template deployment operator",
      "IsCustom": true,
      "Description": "Lets you deploy a resource manager template with the access to the secrets in the Key Vault.",
      "Actions": [
        "Microsoft.KeyVault/vaults/deploy/action"
      ],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/subscriptions/00000000-0000-0000-0000-000000000000"
      ]
    }
    ```
    “00000000-0000-0000-0000-000000000000”을 템플릿을 배포해야 하는 사용자의 구독 ID로 바꾸세요.

2. JSON 파일을 사용하여 새 역할 만들기:

    ```azurepowershell
    $resourceGroupName= "<Resource Group Name>" # the resource group which contains the Key Vault
    $userPrincipalName = "<Email Address of the deployment operator>"
    New-AzRoleDefinition -InputFile "<PathToTheJSONFile>" 
    New-AzRoleAssignment -ResourceGroupName $resourceGroupName -RoleDefinitionName "Key Vault resource manager template deployment operator" -SignInName $userPrincipalName
    ```

    `New-AzRoleAssignment` 샘플은 리소스 그룹 수준에서 사용자에게 사용자 지정 역할을 할당합니다.  

[관리되는 애플리케이션](../managed-applications/overview.md) 템플릿과 함께 Key Vault를 사용하는 경우, **어플라이언스 리소스 공급자** 서비스 주체에 액세스를 허용해야 합니다. 자세한 내용은 [Access Key Vault secret when deploying Azure Managed Applications](../managed-applications/key-vault-access.md)(Azure Managed Applications를 배포할 때 Key Vault 비밀 액세스)를 참조하세요.

## <a name="reference-secrets-with-static-id"></a>정적 ID로 비밀 참조

이 방식을 사용할 때는 템플릿이 아닌 매개 변수 파일에서 Key Vault를 참조합니다. 다음 이미지에서는 매개 변수 파일이 암호를 참조하고 템플릿에 해당 값을 전달하는 방법을 보여줍니다.

![Resource Manager Key Vault 통합 정적 ID 다이어그램](./media/resource-manager-keyvault-parameter/statickeyvault.png)

[자습서: Resource Manager 템플릿 배포 시 Azure Key Vault 통합](./resource-manager-tutorial-use-key-vault.md)에서는 이 방법을 사용합니다. 이 자습서에서는 관리자 암호를 포함하는 Virtual Machine을 배포합니다. 암호 매개 변수는 보안 문자열로 설정됩니다.

![Resource Manager Key Vault 통합 정적 ID 템플릿 파일](./media/resource-manager-keyvault-parameter/resource-manager-key-vault-static-id-template-file.png)

이제 위 템플릿용으로 매개 변수 파일을 만들어야 합니다. 매개 변수 파일에서 템플릿의 매개 변수 이름과 일치하는 매개 변수를 지정합니다. 매개 변수 값으로는 Key Vault의 비밀을 참조합니다. Key Vault의 리소스 식별자와 비밀의 이름을 전달하여 비밀을 참조합니다.

![Resource Manager Key Vault 통합 정적 ID 매개 변수 파일](./media/resource-manager-keyvault-parameter/resource-manager-key-vault-static-id-parameter-file.png)

현재 버전이 아닌 암호 버전을 사용해야 할 경우 `secretVersion` 속성을 사용합니다.

```json
"secretName": "examplesecret",
"secretVersion": "cd91b2b7e10e492ebb870a6ee0591b68"
```

템플릿을 배포하고 매개 변수 파일을 전달합니다.

Azure CLI의 경우 

```azurecli
az group create --name $resourceGroupName --location $location
az group deployment create \
    --name $deploymentName \
    --resource-group $resourceGroupName \
    --template-uri <The Template File URI> \
    --parameters <The Parameter File>
```

PowerShell의 경우 다음을 사용합니다.

```powershell
New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
  -Name $deploymentName `
  -ResourceGroupName $resourceGroupName `
  -TemplateUri <The Template File URI> `
  -TemplateParameterFile <The Parameter File>
```

## <a name="reference-secrets-with-dynamic-id"></a>동적 ID로 비밀 참조

이전 섹션에서는 매개 변수에서 Key Vault 비밀의 정적 리소스 ID를 전달하는 방법을 살펴보았습니다. 하지만, 일부 시나리오에서는, 현재 배포를 기반으로 달라지는 키 자격 증명 모음 비밀을 참조해야 합니다. 매개 변수 파일에 참조 매개 변수를 만드는 대신 템플릿에 매개 변수 값을 전달할 수도 있습니다. 두 경우 모두 링크된 템플릿을 사용하여 Key Vault 비밀의 리소스 ID를 동적으로 생성할 수 있습니다.

매개 변수 파일에 템플릿 식이 허용되지 않기 때문에 매개 변수 파일에 리소스 ID를 동적으로 생성할 수 없습니다. 

부모 템플릿에서 연결된 템플릿을 추가하고 동적으로 생성된 리소스 ID를 포함하는 매개 변수에 전달합니다. 다음 이미지에서는 연결된 템플릿의 매개 변수가 암호를 참조하는 방법을 보여줍니다.

![동적 ID](./media/resource-manager-keyvault-parameter/dynamickeyvault.png)

[다음 템플릿](https://github.com/Azure/azure-quickstart-templates/tree/master/201-key-vault-use-dynamic-id)은 동적으로 키 자격 증명 모음 ID를 만들고 매개 변수로 전달합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "The location where the resources will be deployed."
            }
        },
        "vaultName": {
            "type": "string",
            "metadata": {
                "description": "The name of the keyvault that contains the secret."
            }
        },
        "secretName": {
            "type": "string",
            "metadata": {
                "description": "The name of the secret."
            }
        },
        "vaultResourceGroupName": {
            "type": "string",
            "metadata": {
                "description": "The name of the resource group that contains the keyvault."
            }
        },
        "vaultSubscription": {
            "type": "string",
            "defaultValue": "[subscription().subscriptionId]",
            "metadata": {
                "description": "The name of the subscription that contains the keyvault."
            }
        },
        "_artifactsLocation": {
            "type": "string",
            "metadata": {
                "description": "The base URI where artifacts required by this template are located. When the template is deployed using the accompanying scripts, a private location in the subscription will be used and this value will be automatically generated."
            },
            "defaultValue": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-key-vault-use-dynamic-id/"
        },
        "_artifactsLocationSasToken": {
            "type": "securestring",
            "metadata": {
                "description": "The sasToken required to access _artifactsLocation.  When the template is deployed using the accompanying scripts, a sasToken will be automatically generated."
            },
            "defaultValue": ""
        }
    },
    "resources": [
        {
            "apiVersion": "2018-05-01",
            "name": "dynamicSecret",
            "type": "Microsoft.Resources/deployments",
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                    "contentVersion": "1.0.0.0",
                    "uri": "[uri(parameters('_artifactsLocation'), concat('./nested/sqlserver.json', parameters('_artifactsLocationSasToken')))]"
                },
                "parameters": {
                    "location": {
                        "value": "[parameters('location')]"
                    },
                    "adminLogin": {
                        "value": "ghuser"
                    },
                    "adminPassword": {
                        "reference": {
                            "keyVault": {
                                "id": "[resourceId(parameters('vaultSubscription'), parameters('vaultResourceGroupName'), 'Microsoft.KeyVault/vaults', parameters('vaultName'))]"
                            },
                            "secretName": "[parameters('secretName')]"
                        }
                    }
                }
            }
        }
    ],
    "outputs": {
        "sqlFQDN": {
            "type": "string",
            "value": "[reference('dynamicSecret').outputs.sqlFQDN.value]"
        }
    }
}
```

이전 템플릿을 배포하고 매개 변수에 값을 제공합니다. GitHub에서 예제 템플릿을 사용할 수 있지만 사용자 환경에 매개 변수 값을 제공해야 합니다.

Azure CLI의 경우 

```azurecli
az group create --name $resourceGroupName --location $location
az group deployment create \
    --name $deploymentName \
    --resource-group $resourceGroupName \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-key-vault-use-dynamic-id/azuredeploy.json \
    --parameters vaultName=$keyVaultName vaultResourceGroupName=examplegroup secretName=examplesecret
```

PowerShell의 경우 다음을 사용합니다.

```powershell
New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
  -Name $deploymentName `
  -ResourceGroupName $resourceGroupName `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-key-vault-use-dynamic-id/azuredeploy.json `
  -vaultName $keyVaultName -vaultResourceGroupName $keyVaultResourceGroupName -secretName $secretName
```

## <a name="next-steps"></a>다음 단계

- 키 자격 증명 모음에 대한 일반 정보는 [Azure Key Vault 시작](../key-vault/key-vault-get-started.md)을 참조하세요.
- 키 비밀을 참조하는 전체 예제는 [키 자격 증명 모음 예제](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples)를 참조하세요.
