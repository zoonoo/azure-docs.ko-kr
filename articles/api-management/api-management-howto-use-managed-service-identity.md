---
title: "Azure API Management에서 Azure 관리 서비스 ID 사용 | Microsoft Docs"
description: "API Management에서 Azure 관리 서비스 ID 사용 방법 알아보기"
services: api-management
documentationcenter: 
author: miaojiang
manager: anneta
editor: 
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 10/18/2017
ms.author: apimpm
ms.openlocfilehash: ded0809fa90e98b2e845d328fbeec6d21507c46b
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2017
---
# <a name="use-azure-managed-service-identity-in-azure-api-management"></a>Azure API Management에서 Azure 관리 서비스 ID 사용

> [!Note]
> Azure API Management의 관리 서비스 ID는 현재 미리 보기로 제공됩니다.

이 문서에서는 API Management 서비스 인스턴스에 대한 관리 서비스 ID를 만드는 방법과 다른 리소스에 액세스하는 방법을 보여 줍니다. Azure AD(Azure Active Directory)에서 생성된 관리 서비스 ID를 사용하면 API Management 인스턴스에서 Azure Key Vault처럼 Azure AD로 보호되는 다른 리소스에 쉽고 안전하게 액세스할 수 있습니다. 이 관리 서비스 ID는 Azure에서 관리하며 암호를 프로비전하거나 순환할 필요가 없습니다. Azure 관리 서비스 ID에 대한 자세한 내용은 [Azure 리소스에 대한 관리 서비스 ID](../active-directory/msi-overview.md)를 참조하세요.

## <a name="create-an-api-management-instance-with-an-identity-by-using-a-resource-manager-template"></a>Resource Manager 템플릿을 사용하여 ID로 API Management 인스턴스 만들기

API Management 인스턴스는 ID를 사용하여 리소스 정의에 다음 속성을 포함하는 방법으로 만들 수 있습니다. 

```json
"identity" : {
    "type" : "SystemAssigned"
}
```

이 속성에 의해 Azure에서 API Management 인스턴스에 대한 ID를 만들어서 관리합니다. 

예를 들어 전체 Azure Resource Manager 템플릿은 다음과 같이 보일 수 있습니다.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "0.9.0.0",
    "parameters": {
        "serviceName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "The name of the api management service"
            }
        },
        "publisherEmail": {
            "type": "string",
            "minLength": 1,
            "defaultValue": "admin@contoso.com",
            "metadata": {
                "description": "The email address of the owner of the service"
            }
        },
        "publisherName": {
            "type": "string",
            "minLength": 1,
            "defaultValue": "Contoso",
            "metadata": {
                "description": "The name of the owner of the service"
            }
        },
        "sku": {
            "type": "string",
            "allowedValues": [
                "Developer",
                "Standard",
                "Premium"
            ],
            "defaultValue": "Developer",
            "metadata": {
                "description": "The pricing tier of this API Management service"
            }
        },
        "skuCount": {
            "type": "int",
            "defaultValue": 1,
            "metadata": {
                "description": "The instance size of this API Management service."
            }
        }
    },
    "resources": [
        {
            "apiVersion": "2017-03-01",
            "name": "[parameters('serviceName')]",
            "type": "Microsoft.ApiManagement/service",
            "location": "[resourceGroup().location]",
            "tags": {},
            "sku": {
                "name": "[parameters('sku')]",
                "capacity": "[parameters('skuCount')]"
            },
            "properties": {
                "publisherEmail": "[parameters('publisherEmail')]",
                "publisherName": "[parameters('publisherName')]"
            },
            "identity": { 
                "type": "systemAssigned" 
            }
        }
    ]
}
```

## <a name="obtain-a-certificate-from-azure-key-vault"></a>Azure Key Vault에서 인증서 가져오기

다음 예제에서는 Azure Key Vault에서 인증서를 가져오는 방법을 보여 줍니다. 여기에는 다음 단계가 포함됩니다.

1. ID를 사용하여 API Management 인스턴스를 만듭니다.
2. Azure Key Vault 인스턴스의 액세스 정책을 업데이트하고 API Management 인스턴스가 비밀을 가져올 수 있도록 허용합니다.
3. Key Vault 인스턴스의 인증서를 통해 사용자 지정 도메인 이름을 설정하여 API Management 인스턴스를 업데이트합니다.

> [!Important]
> 인증서의 개체 버전이 제공되지 않은 경우 API Management는 인증서의 최신 버전이 Key Vault로 업로드된 후에 자동으로 해당 인증서를 가져옵니다. 

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "publisherEmail": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "The email address of the owner of the service"
            }
        },
        "publisherName": {
            "type": "string",
            "defaultValue": "Contoso",
            "minLength": 1,
            "metadata": {
                "description": "The name of the owner of the service"
            }
        },
        "sku": {
            "type": "string",
            "allowedValues": ["Developer",
            "Standard",
            "Premium"],
            "defaultValue": "Developer",
            "metadata": {
                "description": "The pricing tier of this API Management service"
            }
        },
        "skuCount": {
            "type": "int",
            "defaultValue": 1,
            "metadata": {
                "description": "The instance size of this API Management service."
            }
        },
        "keyVaultName": {
            "type": "string",
            "metadata": {
                "description": "Name of the vault"
            }
        },
        "proxyCustomHostname1": {
            "type": "string",
            "metadata": {
                "description": "Proxy Custom hostname."
            }
        },
        "keyVaultIdToCertificate": {
            "type": "string",
            "metadata": {
                "description": "Reference to the KeyVault certificate."
            }
        }
    },
    "variables": {
        "apiManagementServiceName": "[concat('apiservice', uniqueString(resourceGroup().id))]",
        "apimServiceIdentityResourceId": "[concat(resourceId('Microsoft.ApiManagement/service', variables('apiManagementServiceName')),'/providers/Microsoft.ManagedIdentity/Identities/default')]"
    },
    "resources": [{
        "apiVersion": "2017-03-01",
        "name": "[variables('apiManagementServiceName')]",
        "type": "Microsoft.ApiManagement/service",
        "location": "[resourceGroup().location]",
        "tags": {
            
        },
        "sku": {
            "name": "[parameters('sku')]",
            "capacity": "[parameters('skuCount')]"
        },
        "properties": {
            "publisherEmail": "[parameters('publisherEmail')]",
            "publisherName": "[parameters('publisherName')]"
        },
        "identity": {
            "type": "systemAssigned"
        }
    },
    {
        "type": "Microsoft.KeyVault/vaults/accessPolicies",
        "name": "[concat(parameters('keyVaultName'), '/add')]",
        "apiVersion": "2015-06-01",        
      "dependsOn": [
        "[resourceId('Microsoft.ApiManagement/service', variables('apiManagementServiceName'))]"
      ],
        "properties": {
            "accessPolicies": [{
                "tenantId": "[reference(variables('apimServiceIdentityResourceId'), '2015-08-31-PREVIEW').tenantId]",
                "objectId": "[reference(variables('apimServiceIdentityResourceId'), '2015-08-31-PREVIEW').principalId]",
                "permissions": {
                    "secrets": ["get"]
                }
            }]
        }
    },
    { 
      "apiVersion": "2017-05-10", 
      "name": "apimWithKeyVault", 
      "type": "Microsoft.Resources/deployments",
      "dependsOn": [
        "[resourceId('Microsoft.ApiManagement/service', variables('apiManagementServiceName'))]"
      ],
      "properties": { 
        "mode": "incremental", 
        "templateLink": {
          "uri": "https://raw.githubusercontent.com/solankisamir/arm-templates/master/basicapim.keyvault.json",
          "contentVersion": "1.0.0.0"
        }, 
        "parameters": {
            "publisherEmail": { "value": "[parameters('publisherEmail')]"},
            "publisherName": { "value": "[parameters('publisherName')]"},
            "sku": { "value": "[parameters('sku')]"},
            "skuCount": { "value": "[parameters('skuCount')]"},
            "proxyCustomHostname1": {"value" : "[parameters('proxyCustomHostname1')]"},
            "keyVaultIdToCertificate": {"value" : "[parameters('keyVaultIdToCertificate')]"}
        }
      } 
    }]
}
```

## <a name="next-steps"></a>다음 단계

Azure 관리 서비스 ID에 대해 자세히 알아봅니다.

* [Azure 리소스용 관리 서비스 ID](../active-directory/msi-overview.md)
* [Azure 리소스 관리자 템플릿](https://github.com/Azure/azure-quickstart-templates)

