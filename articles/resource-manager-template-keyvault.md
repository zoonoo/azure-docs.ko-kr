<properties
   pageTitle="키 자격 증명 모음에 대한 리소스 관리자 템플릿 | Microsoft Azure"
   description="템플릿을 통해 주요 자격 증명 모음을 배포하기 위한 리소스 관리자 스키마를 보여 줍니다."
   services="azure-resource-manager,key-vault"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/23/2016"
   ms.author="tomfitz"/>

# 키 자격 증명 모음 템플릿 스키마

키 자격 증명 모음을 만듭니다.

## 스키마 형식

키 자격 증명 모음을 만들려면 템플릿의 리소스 섹션에 다음 스키마를 추가합니다.

    {
        "type": "Microsoft.KeyVault/vaults",
        "apiVersion": "2015-06-01",
        "name": string,
        "location": string,
        "properties": {
            "enabledForDeployment": bool,
            "enabledForTemplateDeployment": bool,
            "enabledForVolumeEncryption": bool,
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

## 값

다음 표에서는 스키마에 설정해야 하는 값에 대해 설명합니다.

| 이름 | 형식 | 필수 | 허용된 값 | 설명 |
| ---- | ---- | -------- | ---------------- | ----------- |
| type | enum | 예 | **Microsoft.KeyVault/vaults** | 만들려는 리소스 형식입니다. |
| apiVersion | enum | 예 | **2015-06-01** <br /> **2014-12-19-preview** | 리소스를 만들 때 사용하는 API 버전입니다. | 
| name | string | 예 | | 만들려는 키 자격 증명 모음의 이름입니다. Azure의 모든 이름은 고유해야 합니다. 아래의 예제에 표시된 대로 명명 규칙에 따라 [uniqueString](resource-group-template-functions.md#uniquestring) 기능 사용을 고려합니다. |
| location | string | 예 | 유효한 영역을 확인하려면 [지원되는 지역](resource-manager-supported-services.md#supported-regions)을 참조하세요. | 키 자격 증명 모음을 호스팅하는 지역입니다. |
| properties | object | 예 | ([아래 참조](#properties)) | 만들려는 키 자격 증명 모음의 유형을 지정하는 개체입니다. |
| 리소스 | array | 아니요 | [키 자격 증명 모음 암호](resource-manager-template-keyvault-secret.md) | 키 자격 증명 모음의 자식 리소스입니다. |

<a id="properties" />
### properties 개체

| 이름 | 형식 | 필수 | 허용된 값 | 설명 |
| ---- | ---- | -------- | ---------------- | ----------- |
| enabledForDeployment | 부울 | 아니요 | **true** 또는 **false** | 가상 컴퓨터 또는 서비스 패브릭 배포에 자격 증명 모음을 사용할 수 있는지 지정합니다. |
| enabledForTemplateDeployment | 부울 | 아니요 | **true** 또는 **false** | 리소스 관리자 템플릿 배포에 자격 증명 모음을 사용할 수 있는지 지정합니다. 자세한 내용은 [배포 중 보안 값 전달](resource-manager-keyvault-parameter.md)을 참조하세요. |
| enabledForVolumeEncryption | 부울 | 아니요 | **true** 또는 **false** | 볼륨 암호화에 자격 증명 모음을 사용할 수 있는지 지정합니다. |
| tenantId | string | 예 | GUID | 구독의 테넌트 식별자입니다. **Get-AzureRMSubscription** PowerShell cmdlet를 사용하여 검색할 수 있습니다. |
| accessPolicies | array | 예 | ([아래 참조](#accesspolicies)) | 사용자 또는 서비스 주체에 대한 사용 권한을 지정하는 최대 16개 개체의 배열입니다. |
| sku | object | 예 | ([아래 참조](#sku)) | 키 자격 증명 모음에 대한 SKU입니다. |

<a id="accesspolicies" />
### properties.accessPolicies 개체

| 이름 | 형식 | 필수 | 허용된 값 | 설명 |
| ---- | ---- | -------- | ---------------- | ----------- |
| tenantId | string | 예 | GUID | 이 액세스 정책의 **objectId**를 포함하는 Azure Active Directory 테넌트의 테넌트 식별자입니다. |
| objectId | string | 예 | GUID | 자격 증명 모음에 액세스할 수 있는 AAD 사용자 또는 서비스 사용자의 개체 식별자입니다. **Get-AzureRMADUser** 또는 **Get-AzureRMADServicePrincipal** cmdlet에서 값을 검색할 수 있습니다. |
| 권한 | object | 예 | ([아래 참조](#permissions)) | 이 자격 증명 모음에 부여된 Active Directory 개체의 사용 권한입니다. |

<a id="permissions" />
### properties.accessPolicies.permissions 개체

| 이름 | 형식 | 필수 | 허용된 값 | 설명 |
| ---- | ---- | -------- | ---------------- | ----------- |
| 키 | array | 예 | 다음 값의 쉼표로 구분된 목록입니다.<br />**all**<br />**backup**<br />**create**<br />**decrypt**<br />**delete**<br />**encrypt**<br />**get**<br />**import**<br />**list**<br />**restore**<br />**sign**<br />**unwrapkey**<br/>**update**<br />**verify**<br />**wrapkey** | 이 자격 증명 모음의 키에 부여된 Active Directory 개체의 사용 권한입니다. 이 값은 허용되는 값의 배열로 지정해야 합니다. |
| secrets | array | 예 | 다음 값의 쉼표로 구분된 목록입니다.<br />**all**<br />**delete**<br />**get**<br />**list**<br />**set** | 이 자격 증명 모음의 암호에 부여된 Active Directory 개체의 사용 권한입니다. 이 값은 허용되는 값의 배열로 지정해야 합니다. |

<a id="sku" />
### properties.sku 개체

| 이름 | 형식 | 필수 | 허용된 값 | 설명 |
| ---- | ---- | -------- | ---------------- | ----------- |
| name | enum | 예 | **standard**<br />**premium** | 사용할 KeyVault의 서비스 계층입니다. Standard는 암호 및 소프트웨어로 보호된 키를 지원합니다. Premium은 HSM으로 보호된 키에 대한 지원을 추가합니다. |
| family | enum | 예 | **문자열(UTF-8 형식) 또는** | 사용할 sku 제품군입니다. 
 
	
## 예

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
                   "description": "Tenant Id for the subscription and use assigned access to the vault. Available from the Get-AzureRMSubscription PowerShell cmdlet"
                }
            },
            "objectId": {
                "type": "string",
                "metadata": {
                    "description": "Object Id of the AAD user or service principal that will have access to the vault. Available from the Get-AzureRMADUser or the Get-AzureRMADServicePrincipal cmdlets"
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

## 빠른 시작 템플릿

다음 빠른 시작 템플릿으로 주요 자격 증명 모음을 배포합니다.

- [주요 자격 증명 모음 만들기](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create)


## 다음 단계

- 키 자격 증명 모음에 대한 일반 정보는 [Azure 키 자격 증명 모음 시작](./key-vault/key-vault-get-started.md)을 참조하세요.
- 템플릿을 배포할 때 키 자격 증명 모음 암호를 참조하는 예제는 [배포 중 보안 값 전달](resource-manager-keyvault-parameter.md)을 참조하세요.

<!---HONumber=AcomDC_0224_2016-->