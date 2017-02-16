---
title: "Key Vault의 비밀에 대한 Resource Manager 템플릿 | Microsoft Docs"
description: "템플릿을 통해 주요 자격 증명 모음 암호를 배포하기 위한 리소스 관리자 스키마를 보여 줍니다."
services: azure-resource-manager,key-vault
documentationcenter: na
author: tfitzmac
manager: timlt
editor: 
ms.assetid: fd12bc81-b609-4ca8-b7e0-ee1049d70ab3
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/23/2016
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: e841c21a15c47108cbea356172bffe766003a145
ms.openlocfilehash: 885a9b09685982e9bae6b50f0dbc3edd21819859


---
# <a name="key-vault-secret-template-schema"></a>키 자격 증명 모음 암호 템플릿 스키마
키 자격 증명 모음에 저장되는 암호를 만듭니다. 이 리소스 종류는 종종 [키 자격 증명 모음](resource-manager-template-keyvault.md)의 자식 리소스로 배포됩니다.

## <a name="schema-format"></a>스키마 형식
키 자격 증명 모음 암호를 만들려면 템플릿에 다음 스키마를 추가합니다. 키 자격 증명 모음의 자식 리소스 또는 최상위 리소스로 암호를 정의할 수 있습니다. 주요 자격 증명 모음을 동일한 템플릿에 배포할 경우에는 자식 리소스로 정의할 수 있습니다. 키 자격 증명 모음을 동일한 템플릿에 배포하지 않거나, 리소스 종류에서 반복하여 여러 암호를 만들어야 하는 경우에는 암호를 최상위 리소스로 정의해야 합니다. 

    {
        "type": enum,
        "apiVersion": "2015-06-01",
        "name": string,
        "properties": {
            "value": string
        },
        "dependsOn": [ array values ]
    }

## <a name="values"></a>값
다음 표에서는 스키마에 설정해야 하는 값에 대해 설명합니다.

| 이름 | 값 |
| --- | --- |
| type |열거형<br />필수<br />**비밀**(Key Vault의 자식 리소스로 배포되는 경우) 또는<br /> **Microsoft.KeyVault/vaults/secrets**(최상위 리소스로 배포되는 경우)<br /><br />만들려는 리소스 종류입니다. |
| apiVersion |열거형<br />필수<br />**2015-06-01** 또는 **2014-12-19-preview**<br /><br />리소스를 만들 때 사용하는 API 버전입니다. |
| 이름 |문자열<br />필수<br />Key Vault의 자식 리소스로 배포되는 경우 단일 단어 또는 기존 Key Vault에 추가할 최상위 리소스로 배포되는 경우 **{key-vault-name}/{secret-name}** 형식입니다.<br /><br />만들려는 암호의 이름입니다. |
| properties |Object<br />필수<br />[properties 개체](#properties)<br /><br />만들려는 암호 값을 지정하는 개체입니다. |
| dependsOn |배열<br />옵션<br />리소스 이름 또는 리소스 고유 식별자의 쉼표로 구분된 목록입니다.<br /><br />이 링크가 종속된 리소스의 컬렉션입니다. 암호의 키 자격 증명 모음을 동일한 템플릿에 배포하는 경우 이 요소에 키 자격 증명 모음의 이름을 포함하여 먼저 배포되도록 합니다. |

<a id="properties" />

### <a name="properties-object"></a>properties 개체
| 이름 | 값 |
| --- | --- |
| 값 |String<br />필수<br /><br />키 자격 증명 모음에 저장할 암호 값입니다. 이 속성의 값을 전달할 때는 **securestring**형식의 매개 변수를 사용합니다. |

## <a name="examples"></a>예
첫 번째 예제에서는 키 자격 증명 모음의 자식 리소스로 암호를 배포합니다.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "keyVaultName": {
                "type": "string",
                "metadata": {
                    "description": "Name of the vault"
                }
            },
            "tenantId": {
                "type": "string",
                "metadata": {
                   "description": "Tenant ID for the subscription and use assigned access to the vault. Available from the Get-AzureRmSubscription PowerShell cmdlet"
                }
            },
            "objectId": {
                "type": "string",
                "metadata": {
                    "description": "Object ID of the AAD user or service principal that will have access to the vault. Available from the Get-AzureRmADUser or the Get-AzureRmADServicePrincipal cmdlets"
                }
            },
            "keysPermissions": {
                "type": "array",
                "defaultValue": [ "all" ],
                "metadata": {
                    "description": "Permissions to grant user to keys in the vault. Valid values are: all, create, import, update, get, list, delete, backup, restore, encrypt, decrypt, wrapkey, unwrapkey, sign, and verify."
                }
            },
            "secretsPermissions": {
                "type": "array",
                "defaultValue": [ "all" ],
                "metadata": {
                    "description": "Permissions to grant user to secrets in the vault. Valid values are: all, get, set, list, and delete."
                }
            },
            "vaultSku": {
                "type": "string",
                "defaultValue": "Standard",
                "allowedValues": [
                    "Standard",
                    "Premium"
                ],
                "metadata": {
                    "description": "SKU for the vault"
                }
            },
            "enabledForDeployment": {
                "type": "bool",
                "defaultValue": false,
                "metadata": {
                    "description": "Specifies if the vault is enabled for VM or Service Fabric deployment"
                }
            },
            "enabledForTemplateDeployment": {
                "type": "bool",
                "defaultValue": false,
                "metadata": {
                    "description": "Specifies if the vault is enabled for ARM template deployment"
                }
            },
            "enableVaultForVolumeEncryption": {
                "type": "bool",
                "defaultValue": false,
                "metadata": {
                    "description": "Specifies if the vault is enabled for volume encryption"
                }
            },
            "secretName": {
                "type": "string",
                "metadata": {
                    "description": "Name of the secret to store in the vault"
                }
            },
            "secretValue": {
                "type": "securestring",
                "metadata": {
                    "description": "Value of the secret to store in the vault"
                }
            }
        },
        "resources": [
        {
            "type": "Microsoft.KeyVault/vaults",
            "name": "[parameters('keyVaultName')]",
            "apiVersion": "2015-06-01",
            "location": "[resourceGroup().location]",
            "tags": {
                "displayName": "KeyVault"
            },
            "properties": {
                "enabledForDeployment": "[parameters('enabledForDeployment')]",
                "enabledForTemplateDeployment": "[parameters('enabledForTemplateDeployment')]",
                "enabledForVolumeEncryption": "[parameters('enableVaultForVolumeEncryption')]",
                "tenantId": "[parameters('tenantId')]",
                "accessPolicies": [
                {
                    "tenantId": "[parameters('tenantId')]",
                    "objectId": "[parameters('objectId')]",
                    "permissions": {
                        "keys": "[parameters('keysPermissions')]",
                        "secrets": "[parameters('secretsPermissions')]"
                    }
                }],
                "sku": {
                    "name": "[parameters('vaultSku')]",
                    "family": "A"
                }
            },
            "resources": [
            {
                "type": "secrets",
                "name": "[parameters('secretName')]",
                "apiVersion": "2015-06-01",
                "properties": {
                    "value": "[parameters('secretValue')]"
                },
                "dependsOn": [
                    "[concat('Microsoft.KeyVault/vaults/', parameters('keyVaultName'))]"
                ]
            }]
        }]
    }

두 번째 예제에서는 기존 키 자격 증명 모음에 저장되는 최상위 리소스로 암호를 배포합니다.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "keyVaultName": {
                "type": "string",
                "metadata": {
                    "description": "Name of the existing vault"
                }
            },
            "secretName": {
                "type": "string",
                "metadata": {
                    "description": "Name of the secret to store in the vault"
                }
            },
            "secretValue": {
                "type": "securestring",
                "metadata": {
                    "description": "Value of the secret to store in the vault"
                }
            }
        },
        "variables": {},
        "resources": [
            {
                "type": "Microsoft.KeyVault/vaults/secrets",
                "apiVersion": "2015-06-01",
                "name": "[concat(parameters('keyVaultName'), '/', parameters('secretName'))]",
                "properties": {
                    "value": "[parameters('secretValue')]"
                }
            }
        ],
        "outputs": {}
    }


## <a name="next-steps"></a>다음 단계
* Key Vault에 대한 일반 정보는 [Azure Key Vault 시작](../key-vault/key-vault-get-started.md)을 참조하세요.
* 템플릿을 배포할 때 키 자격 증명 모음 암호를 참조하는 예제는 [배포 중 보안 값 전달](resource-manager-keyvault-parameter.md)을 참조하세요.




<!--HONumber=Nov16_HO3-->


