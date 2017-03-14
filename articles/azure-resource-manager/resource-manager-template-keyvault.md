---
title: "키 자격 증명 모음에 대한 Resource Manager 템플릿 | Microsoft Docs"
description: "템플릿을 통해 주요 자격 증명 모음을 배포하기 위한 리소스 관리자 스키마를 보여 줍니다."
services: azure-resource-manager,key-vault
documentationcenter: na
author: tfitzmac
manager: wpickett
editor: 
ms.assetid: 96433b1a-68ee-4292-85b6-a581618b921b
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/23/2016
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: f2d009477a614c3b2876ce98a355d3775abf772b
ms.openlocfilehash: 04f2d5d8e501ebf41cf95ea925d238f64b096c1d
ms.lasthandoff: 02/27/2017


---
# <a name="key-vault-template-schema"></a>키 자격 증명 모음 템플릿 스키마
키 자격 증명 모음을 만듭니다.

## <a name="schema-format"></a>스키마 형식
키 자격 증명 모음을 만들려면 템플릿의 리소스 섹션에 다음 스키마를 추가합니다.

    {
        "type": "Microsoft.KeyVault/vaults",
        "apiVersion": "2015-06-01",
        "name": string,
        "location": string,
        "properties": {
            "enabledForDeployment": bool,
            "enabledForTemplateDeployment": bool,
            "enabledForDiskEncryption": bool,
            "tenantId": string,
            "accessPolicies": [
                {
                    "tenantId": string,
                    "objectId": string,
                    "permissions": {
                        "keys": [ keys permissions ],
                        "secrets": [ secrets permissions ]
                    }
                }
            ],
            "sku": {
                "name": enum,
                "family": "A"
            }
        },
        "resources": [
             child resources
        ]
    }

## <a name="values"></a>값
다음 표에서는 스키마에 설정해야 하는 값에 대해 설명합니다.

| 이름 | 값 |
| --- | --- |
| type |열거형<br />필수<br />**Microsoft.KeyVault/vaults**<br /><br />만들려는 리소스 종류입니다. |
| apiVersion |열거형<br />필수<br />**2015-06-01** 또는 **2014-12-19-preview**<br /><br />리소스를 만들 때 사용하는 API 버전입니다. |
| 이름 |문자열<br />필수<br />Azure에 고유한 이름입니다.<br /><br />만들려는 키 자격 증명 모음의 이름입니다. 아래의 예제에 표시된 대로 명명 규칙에 따라 [uniqueString](resource-group-template-functions.md#uniquestring) 함수를 사용하여 고유한 이름을 만드는 것이 좋습니다. |
| 위치 |String<br />필수<br />키 자격 증명 모음의 유효한 하위 지역입니다. 유효한 영역을 확인하려면 [지원되는 지역](resource-manager-supported-services.md#supported-regions)을 참조하세요.<br /><br />키 자격 증명 모음을 호스트하는 하위 지역입니다. |
| properties |Object<br />필수<br />[properties 개체](#properties)<br /><br />만들려는 키 자격 증명 모음의 유형을 지정하는 개체입니다. |
| 리소스 |배열<br />옵션<br />허용된 값: [Key Vault 암호 리소스](resource-manager-template-keyvault-secret.md)<br /><br />키 자격 증명 모음의 자식 리소스입니다. |

<a id="properties" />

### <a name="properties-object"></a>properties 개체
| 이름 | 값 |
| --- | --- |
| enabledForDeployment |Boolean<br />옵션<br />**true** 또는 **false**<br /><br />가상 컴퓨터 또는 서비스 패브릭 배포에 자격 증명 모음을 사용할 수 있는지 지정합니다. |
| enabledForTemplateDeployment |Boolean<br />옵션<br />**true** 또는 **false**<br /><br />Resource Manager 템플릿 배포에 자격 증명 모음을 사용할 수 있는지 지정합니다. 자세한 내용은 [배포 중 보안 값 전달](resource-manager-keyvault-parameter.md) |
| enabledForDiskEncryption |Boolean<br />옵션<br />**true** 또는 **false**<br /><br />볼륨 암호화에 자격 증명 모음을 사용할 수 있는지 지정합니다. |
| tenantId |문자열<br />필수<br />**GUID(Globally Unique Identifier)**<br /><br />구독의 테넌트 식별자입니다. [Get-AzureRmSubscription](https://msdn.microsoft.com/library/azure/mt619284.aspx) PowerShell cmdlet 또는 **azure account show** Azure CLI 명령을 사용하여 검색할 수 있습니다. |
| accessPolicies |배열<br />필수<br />[accessPolicies 개체](#accesspolicies)<br /><br />사용자 또는 서비스 주체에 대한 사용 권한을 지정하는 최대 16개 개체의 배열입니다. |
| sku |Object<br />필수<br />[sku 개체](#sku)<br /><br />키 자격 증명 모음의 SKU입니다. |

<a id="accesspolicies" />

### <a name="propertiesaccesspolicies-object"></a>properties.accessPolicies 개체
| 이름 | 값 |
| --- | --- |
| tenantId |문자열<br />필수<br />**GUID(Globally Unique Identifier)**<br /><br />이 액세스 정책의 **objectId** 를 포함하는 Azure Active Directory 테넌트의 테넌트 식별자입니다. |
| objectId |String<br />필수<br />**GUID(Globally Unique Identifier)**<br /><br />자격 증명 모음에 액세스할 수 있는 Azure Active Directory 사용자 또는 서비스 사용자의 개체 식별자입니다. [Get-AzureRmADUser](https://msdn.microsoft.com/library/azure/mt679001.aspx) 또는 [Get-AzureRmADServicePrincipal](https://msdn.microsoft.com/library/azure/mt678992.aspx) PowerShell cmdlet이나 **azure ad user** 또는 **azure ad sp** Azure CLI 명령에서 값을 검색할 수 있습니다. |
| 권한 |Object<br />필수<br />[permissions 개체](#permissions)<br /><br />이 자격 증명 모음에 부여된 Active Directory 개체의 사용 권한입니다. |

<a id="permissions" />

### <a name="propertiesaccesspoliciespermissions-object"></a>properties.accessPolicies.permissions 개체
| 이름 | 값 |
| --- | --- |
| 키 |배열<br />필수<br />**all**, **backup**, **create**, **decrypt**, **delete**, **encrypt**, **get**, **import**, **list**, **restore**, **sign**, **unwrapkey**, **update**, **verify**, **wrapkey**<br /><br />이 자격 증명 모음의 키에 부여된 Active Directory 개체의 사용 권한입니다. 이 값은 하나 이상의 허용되는 값 배열로 지정해야 합니다. |
| secrets |배열<br />필수<br />**all**, **delete**, **get**, **list**, **set**<br /><br />이 자격 증명 모음의 암호에 부여된 Active Directory 개체의 사용 권한입니다. 이 값은 하나 이상의 허용되는 값 배열로 지정해야 합니다. |

<a id="sku" />

### <a name="propertiessku-object"></a>properties.sku 개체
| 이름 | 값 |
| --- | --- |
| 이름 |열거형<br />필수<br />**standard** 또는 **premium** <br /><br />사용할 KeyVault의 서비스 계층입니다.  Standard는 암호 및 소프트웨어로 보호된 키를 지원합니다.  Premium은 HSM으로 보호된 키에 대한 지원을 추가합니다. |
| family |열거형<br />필수<br />**A** <br /><br />사용할 sku 제품군입니다. |

## <a name="examples"></a>예
다음 예제에서는 키 자격 증명 모음 및 암호를 배포합니다.

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
            "enableVaultForDiskEncryption": {
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
                "enabledForDiskEncryption": "[parameters('enableVaultForDiskEncryption')]",
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

## <a name="quickstart-templates"></a>빠른 시작 템플릿
다음 빠른 시작 템플릿으로 주요 자격 증명 모음을 배포합니다.

* [주요 자격 증명 모음 만들기](https://azure.microsoft.com/documentation/templates/101-key-vault-create/)

## <a name="next-steps"></a>다음 단계
* 키 자격 증명 모음에 대한 일반 정보는 [Azure Key Vault 시작](../key-vault/key-vault-get-started.md)을 참조하세요.
* 템플릿을 배포할 때 키 자격 증명 모음 암호를 참조하는 예제는 [배포 중 보안 값 전달](resource-manager-keyvault-parameter.md)을 참조하세요.


