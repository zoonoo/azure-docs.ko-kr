---
title: "Resource Manager 템플릿에서 Key Vault 비밀 | Microsoft Docs"
description: "배포하는 동안 키 자격 증명 모음의 비밀을 매개 변수로 전달하는 방법을 보여 줍니다."
services: azure-resource-manager,key-vault
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: c582c144-4760-49d3-b793-a3e1e89128e2
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/25/2017
ms.author: tomfitz
ms.openlocfilehash: 1ca72599e67e79d42a3d430dbb13e89ea7265334
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="use-key-vault-to-pass-secure-parameter-value-during-deployment"></a>Key Vault를 사용하여 배포 중에 보안 매개 변수 값 전달

배포 중에 보안 값(예: 암호)을 매개 변수로 전달해야 할 경우 [Azure Key Vault](../key-vault/key-vault-whatis.md)에서 값을 검색할 수 있습니다. 매개 변수 파일에서 Key Vault 및 비밀을 참조하여 이 값을 검색합니다. 해당 Key Vault ID만 참조하므로 이 값은 절대 노출되지 않습니다. 리소스를 배포할 때마다 수동으로 비밀 값을 입력하지 않아도 됩니다. Key Vault는 배포하는 리소스 그룹과는 다른 구독에 있을 수 있습니다. Key Vault를 참조할 때는 구독 ID를 포함합니다.

Key Vault를 만들 때는 *enabledForTemplateDeployment* 속성을 *true*로 설정합니다. 이 값을 true로 설정하면 배포 중에 Resource Manager 템플릿으로부터의 액세스가 허용됩니다.  

## <a name="deploy-a-key-vault-and-secret"></a>키 자격 증명 모음 및 비밀 배포

Key Vault 및 비밀을 만들려면 Azure CLI 또는 PowerShell을 사용합니다. Key Vault가 템플릿 배포에 사용할 수 있도록 설정되었는지 확인합니다. 

Azure CLI의 경우 

```azurecli
vaultname={your-unique-vault-name}
password={password-value}

az group create --name examplegroup --location 'South Central US'
az keyvault create --name $vaultname --resource-group examplegroup --location 'South Central US' --enabled-for-template-deployment true
az keyvault secret set --vault-name $vaultname --name examplesecret --value $password
```

PowerShell의 경우 다음을 사용합니다.

```powershell
$vaultname = "{your-unique-vault-name}"
$password = "{password-value}"

New-AzureRmResourceGroup -Name examplegroup -Location "South Central US"
New-AzureRmKeyVault -VaultName $vaultname -ResourceGroupName examplegroup -Location "South Central US" -EnabledForTemplateDeployment
$secretvalue = ConvertTo-SecureString $password -AsPlainText -Force
Set-AzureKeyVaultSecret -VaultName $vaultname -Name "examplesecret" -SecretValue $secretvalue
```

## <a name="enable-access-to-the-secret"></a>비밀에 대한 액세스를 사용하도록 설정

새 Key Vault를 사용하든, 기존 Key Vault를 사용하든, 템플릿을 배포하는 사용자가 비밀에 액세스할 수 있어야 합니다. 비밀을 참조하는 템플릿을 배포하는 사용자에게는 Key Vault에 대한 `Microsoft.KeyVault/vaults/deploy/action` 권한이 있어야 합니다. [소유자](../active-directory/role-based-access-built-in-roles.md#owner) 및 [참여자](../active-directory/role-based-access-built-in-roles.md#contributor) 역할 모두 이 액세스 권한을 부여합니다. 또한 이 사용 권한을 부여하는 [사용자 지정 역할](../active-directory/role-based-access-control-custom-roles.md)을 만들고 해당 역할에 사용자를 추가할 수도 있습니다. 사용자를 역할에 추가하는 방법에 대한 자세한 내용은 [Azure Active Directory에서 관리자 역할에 사용자 할당](../active-directory/active-directory-users-assign-role-azure-portal.md)을 참조하세요.


## <a name="reference-a-secret-with-static-id"></a>정적 ID로 비밀 참조

Key Vault 비밀을 수신하는 템플릿은 다른 템플릿과 비슷합니다. **템플릿이 아닌 매개 변수 파일에서 Key Vault를 참조**하기 때문입니다. 예를 들어 다음 템플릿은 관리자 암호를 포함하는 SQL 데이터베이스를 배포합니다. 암호 매개 변수는 보안 문자열로 설정됩니다. 하지만 이 템플릿은 해당 값이 제공되는 위치를 지정하지는 않습니다.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "administratorLogin": {
            "type": "string"
        },
        "administratorLoginPassword": {
            "type": "securestring"
        },
        "collation": {
            "type": "string"
        },
        "databaseName": {
            "type": "string"
        },
        "edition": {
            "type": "string"
        },
        "requestedServiceObjectiveId": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "maxSizeBytes": {
            "type": "string"
        },
        "serverName": {
            "type": "string"
        },
        "sampleName": {
            "type": "string",
            "defaultValue": ""
        }
    },
    "resources": [
        {
            "apiVersion": "2015-05-01-preview",
            "location": "[parameters('location')]",
            "name": "[parameters('serverName')]",
            "properties": {
                "administratorLogin": "[parameters('administratorLogin')]",
                "administratorLoginPassword": "[parameters('administratorLoginPassword')]",
                "version": "12.0"
            },
            "resources": [
                {
                    "apiVersion": "2014-04-01-preview",
                    "dependsOn": [
                        "[concat('Microsoft.Sql/servers/', parameters('serverName'))]"
                    ],
                    "location": "[parameters('location')]",
                    "name": "[parameters('databaseName')]",
                    "properties": {
                        "collation": "[parameters('collation')]",
                        "edition": "[parameters('edition')]",
                        "maxSizeBytes": "[parameters('maxSizeBytes')]",
                        "requestedServiceObjectiveId": "[parameters('requestedServiceObjectiveId')]",
                        "sampleName": "[parameters('sampleName')]"
                    },
                    "type": "databases"
                },
                {
                    "apiVersion": "2014-04-01-preview",
                    "dependsOn": [
                        "[concat('Microsoft.Sql/servers/', parameters('serverName'))]"
                    ],
                    "location": "[parameters('location')]",
                    "name": "AllowAllWindowsAzureIps",
                    "properties": {
                        "endIpAddress": "0.0.0.0",
                        "startIpAddress": "0.0.0.0"
                    },
                    "type": "firewallrules"
                }
            ],
            "type": "Microsoft.Sql/servers"
        }
    ]
}
```

이제 위 템플릿용으로 매개 변수 파일을 만들어야 합니다. 매개 변수 파일에서 템플릿의 매개 변수 이름과 일치하는 매개 변수를 지정합니다. 매개 변수 값으로는 Key Vault의 비밀을 참조합니다. 키 자격 증명 모음의 리소스 식별자와 비밀 이름을 전달하여 암호를 참조합니다. 다음 예에서는 키 자격 증명 모음 비밀이 이미 있어야 하고, 리소스 ID에 정적 값을 제공합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "administratorLogin": {
            "value": "exampleadmin"
        },
        "administratorLoginPassword": {
            "reference": {
              "keyVault": {
                "id": "/subscriptions/{guid}/resourceGroups/examplegroup/providers/Microsoft.KeyVault/vaults/{vault-name}"
              },
              "secretName": "examplesecret"
            }
        },
        "collation": {
            "value": "SQL_Latin1_General_CP1_CI_AS"
        },
        "databaseName": {
            "value": "exampledb"
        },
        "edition": {
            "value": "Standard"
        },
        "location": {
            "value": "southcentralus"
        },
        "maxSizeBytes": {
            "value": "268435456000"
        },
        "requestedServiceObjectiveId": {
            "value": "455330e1-00cd-488b-b5fa-177c226f28b7"
        },
        "sampleName": {
            "value": ""
        },
        "serverName": {
            "value": "exampleserver"
        }
    }
}
```

## <a name="reference-a-secret-with-dynamic-id"></a>동적 ID로 비밀 참조

이전 섹션에서는 키 자격 증명 모음 비밀에 대해 정적 리소스 ID를 전달하는 방법을 살펴보았습니다. 하지만, 일부 시나리오에서는, 현재 배포를 기반으로 달라지는 키 자격 증명 모음 비밀을 참조해야 합니다. 이런 경우, 매개 변수 파일에 리소스 ID를 하드 코딩할 수 없습니다. 아쉽게도, 매개 변수 파일에 템플릿 식이 허용되지 않기 때문에 매개 변수 파일에 리소스 ID를 동적으로 생성할 수 없습니다.

키 자격 증명 모음 비밀에 대한 리소스 ID를 동적으로 생성하려면, 비밀이 필요한 리소스를 중첩된 템플릿으로 이동해야 합니다. 마스터 템플릿에서, 중첩된 템플릿을 추가하고 동적으로 생성된 리소스 ID를 포함하는 매개 변수에 전달합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "vaultName": {
        "type": "string"
      },
      "secretName": {
        "type": "string"
      }
    },
    "resources": [
    {
      "apiVersion": "2015-01-01",
      "name": "nestedTemplate",
      "type": "Microsoft.Resources/deployments",
      "properties": {
        "mode": "incremental",
        "templateLink": {
          "uri": "https://www.contoso.com/AzureTemplates/newVM.json",
          "contentVersion": "1.0.0.0"
        },
        "parameters": {
          "adminPassword": {
            "reference": {
              "keyVault": {
                "id": "[concat(resourceGroup().id, '/providers/Microsoft.KeyVault/vaults/', parameters('vaultName'))]"
              },
              "secretName": "[parameters('secretName')]"
            }
          }
        }
      }
    }],
    "outputs": {}
}
```

## <a name="next-steps"></a>다음 단계
* 키 자격 증명 모음에 대한 일반 정보는 [Azure Key Vault 시작](../key-vault/key-vault-get-started.md)을 참조하세요.
* 키 비밀을 참조하는 전체 예제는 [키 자격 증명 모음 예제](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples)를 참조하세요.

