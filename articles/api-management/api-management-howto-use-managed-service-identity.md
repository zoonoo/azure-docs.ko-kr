---
title: Azure API Management |에서 관리 되는 id 사용 Microsoft Docs
description: Azure Portal, PowerShell 및 리소스 관리자 템플릿을 사용 하 여 API Management에서 시스템 할당 및 사용자 할당 id를 만드는 방법에 대해 알아봅니다.
services: api-management
documentationcenter: ''
author: miaojiang
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 06/12/2020
ms.author: apimpm
ms.openlocfilehash: 8a7fa295bdc8881c0c1ba58c95872a9380231b81
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "85558038"
---
# <a name="use-managed-identities-in-azure-api-management"></a>Azure API Management에서 관리 되는 id 사용

이 문서에서는 Azure API Management 인스턴스에 대 한 관리 되는 id를 만드는 방법과 다른 리소스에 액세스 하는 방법을 보여 줍니다. Azure Active Directory (Azure AD)에서 생성 된 관리 되는 id를 사용 하면 API Management 인스턴스가 Azure Key Vault 같은 다른 Azure AD 보호 리소스에 쉽고 안전 하 게 액세스할 수 있습니다. Azure에서이 id를 관리 하므로 암호를 프로 비전 하거나 회전할 필요가 없습니다. 관리 id에 대 한 자세한 내용은 [Azure 리소스에 대 한 관리 되는 Id 란?](../active-directory/managed-identities-azure-resources/overview.md)을 참조 하세요.

두 가지 유형의 id를 API Management 인스턴스에 부여할 수 있습니다.

- *시스템 할당 id* 는 서비스에 연결 되며, 서비스가 삭제 되 면 삭제 됩니다. 서비스에는 시스템 할당 id가 하나만 있을 수 있습니다.
- *사용자 할당 id* 는 서비스에 할당할 수 있는 독립 실행형 Azure 리소스입니다. 서비스에는 사용자가 할당 된 id가 여러 개 있을 수 있습니다.

## <a name="create-a-system-assigned-managed-identity"></a>시스템 할당 관리 id 만들기

### <a name="azure-portal"></a>Azure portal

Azure Portal에서 관리 되는 id를 설정 하려면 먼저 API Management 인스턴스를 만든 후 기능을 사용 하도록 설정 합니다.

1. 평소처럼 포털에서 API Management 인스턴스를 만듭니다. 포털에서 찾습니다.
2. **관리 id**를 선택 합니다.
3. **시스템 할당 됨** 탭에서 **상태** 를 **켜기**로 전환 합니다. **저장**을 선택합니다.

    :::image type="content" source="./media/api-management-msi/enable-system-msi.png" alt-text="시스템 할당 관리 id 사용을 위한 선택 항목" border="true":::


### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

다음 단계에서는 API Management 인스턴스를 만들고 Azure PowerShell를 사용 하 여 id를 할당 하는 과정을 안내 합니다. 

1. 필요한 경우 [Azure PowerShell 가이드](/powershell/azure/install-az-ps)의 지침을 사용 하 여 Azure PowerShell를 설치 합니다. 그런 다음 `Connect-AzAccount` 를 실행 하 여 Azure에 대 한 연결을 만듭니다.

2. 다음 코드를 사용 하 여 인스턴스를 만듭니다. API Management 인스턴스와 Azure PowerShell를 사용 하는 방법에 대 한 자세한 예제는 [API Management PowerShell 샘플](powershell-samples.md)을 참조 하세요.

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create an API Management Consumption Sku service.
    New-AzApiManagement -ResourceGroupName $resourceGroupName -Name consumptionskuservice -Location $location -Sku Consumption -Organization contoso -AdminEmail contoso@contoso.com -SystemAssignedIdentity
    ```

3. 기존 인스턴스를 업데이트 하 여 id를 만듭니다.

    ```azurepowershell-interactive
    # Get an API Management instance
    $apimService = Get-AzApiManagement -ResourceGroupName $resourceGroupName -Name $apiManagementName

    # Update an API Management instance
    Set-AzApiManagement -InputObject $apimService -SystemAssignedIdentity
    ```

### <a name="azure-resource-manager-template"></a>Azure Resource Manager 템플릿

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
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "0.9.0.0",
    "resources": [{
        "apiVersion": "2019-01-01",
        "name": "contoso",
        "type": "Microsoft.ApiManagement/service",
        "location": "[resourceGroup().location]",
        "tags": {},
        "sku": {
            "name": "Developer",
            "capacity": "1"
        },
        "properties": {
            "publisherEmail": "admin@contoso.com",
            "publisherName": "Contoso"
        },
        "identity": {
            "type": "systemAssigned"
        }
    }]
}
```

인스턴스가 생성 되 면 다음과 같은 추가 속성이 있습니다.

```json
"identity": {
    "type": "SystemAssigned",
    "tenantId": "<TENANTID>",
    "principalId": "<PRINCIPALID>"
}
```

`tenantId`속성은 id가 속하는 AZURE AD 테 넌 트를 식별 합니다. `principalId`속성은 인스턴스의 새 id에 대 한 고유 식별자입니다. Azure AD 내에서 서비스 주체는 API Management 인스턴스에 제공한 이름과 동일 합니다.


> [!NOTE]
> API Management 인스턴스에는 시스템 할당 id와 사용자 할당 id가 동시에 있을 수 있습니다. 이 경우 `type` 속성은 `SystemAssigned,UserAssigned` 입니다.

### <a name="supported-scenarios"></a>지원되는 시나리오

#### <a name="obtain-a-custom-tlsssl-certificate-for-the-api-management-instance-from-azure-key-vault"></a><a name="use-ssl-tls-certificate-from-azure-key-vault"></a>Azure Key Vault에서 API Management 인스턴스에 대 한 사용자 지정 TLS/SSL 인증서를 가져옵니다.
API Management 인스턴스의 시스템 할당 id를 사용 하 여 Azure Key Vault에 저장 된 사용자 지정 TLS/SSL 인증서를 검색할 수 있습니다. 그런 다음 API Management 인스턴스의 사용자 지정 도메인에 이러한 인증서를 할당할 수 있습니다. 다음 고려 사항을 염두에 두십시오.

- 비밀의 콘텐츠 형식은 *application/x pkcs12*이어야 합니다.
- 비밀을 포함 하는 Key Vault 인증서 비밀 끝점을 사용 합니다.

> [!Important]
> 인증서의 개체 버전을 제공 하지 않으면 API Management는 Key Vault에서 업데이트 된 후 4 시간 이내에 최신 버전의 인증서를 자동으로 가져옵니다.

다음 예제에서는 다음 단계를 포함하는 Azure Resource Manager 템플릿을 보여줍니다.

1. 관리 id를 사용 하 여 API Management 인스턴스를 만듭니다.
2. Azure Key Vault 인스턴스의 액세스 정책을 업데이트하고 API Management 인스턴스가 비밀을 가져올 수 있도록 허용합니다.
3. Key Vault 인스턴스의 인증서를 통해 사용자 지정 도메인 이름을 설정하여 API Management 인스턴스를 업데이트합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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
                "description": "The pricing tier of this API Management instance"
            }
        },
        "skuCount": {
            "type": "int",
            "defaultValue": 1,
            "metadata": {
                "description": "The instance size of this API Management instance."
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
                "description": "Reference to the Key Vault certificate. https://contoso.vault.azure.net/secrets/contosogatewaycertificate."
            }
        }
    },
    "variables": {
        "apiManagementServiceName": "[concat('apiservice', uniqueString(resourceGroup().id))]",
        "apimServiceIdentityResourceId": "[concat(resourceId('Microsoft.ApiManagement/service', variables('apiManagementServiceName')),'/providers/Microsoft.ManagedIdentity/Identities/default')]"
    },
    "resources": [{
        "apiVersion": "2019-01-01",
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

#### <a name="authenticate-to-the-back-end-by-using-an-api-management-identity"></a>API Management id를 사용 하 여 백 엔드에 인증

시스템이 할당 한 id를 사용 하 여 [인증 관리 id](api-management-authentication-policies.md#ManagedIdentity) 정책을 통해 백 엔드에 인증할 수 있습니다.


## <a name="create-a-user-assigned-managed-identity"></a>사용자 할당 관리 ID 만들기

> [!NOTE]
> 사용자 할당 관리 id를 최대 10 개까지 API Management 인스턴스를 연결할 수 있습니다.

### <a name="azure-portal"></a>Azure portal

포털에서 관리 id를 설정 하려면 먼저 API Management 인스턴스를 만든 후 기능을 사용 하도록 설정 합니다.

1. 평소처럼 포털에서 API Management 인스턴스를 만듭니다. 포털에서 찾습니다.
2. **관리 id**를 선택 합니다.
3. **사용자 할당 됨** 탭에서 **추가**를 선택 합니다.
4. 이전에 만든 id를 검색 하 여 선택 합니다. **추가**를 선택합니다.

   :::image type="content" source="./media/api-management-msi/enable-user-assigned-msi.png" alt-text="시스템 할당 관리 id 사용을 위한 선택 항목" border="true":::

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

다음 단계에서는 API Management 인스턴스를 만들고 Azure PowerShell를 사용 하 여 id를 할당 하는 과정을 안내 합니다. 

1. 필요한 경우 [Azure PowerShell 가이드](/powershell/azure/install-az-ps)의 지침을 사용 하 여 Azure PowerShell를 설치 합니다. 그런 다음 `Connect-AzAccount` 를 실행 하 여 Azure에 대 한 연결을 만듭니다.

2. 다음 코드를 사용 하 여 인스턴스를 만듭니다. API Management 인스턴스와 Azure PowerShell를 사용 하는 방법에 대 한 자세한 예제는 [API Management PowerShell 샘플](powershell-samples.md)을 참조 하세요.

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create a user-assigned identity. This requires installation of the "Az.ManagedServiceIdentity" module.
    $userAssignedIdentity = New-AzUserAssignedIdentity -Name $userAssignedIdentityName -ResourceGroupName $resourceGroupName

    # Create an API Management Consumption Sku service.
    $userIdentities = @($userAssignedIdentity.Id)

    New-AzApiManagement -ResourceGroupName $resourceGroupName -Location $location -Name $apiManagementName -Organization contoso -AdminEmail admin@contoso.com -Sku Consumption -UserAssignedIdentity $userIdentities
    ```

3. 서비스에 id를 할당 하도록 기존 서비스를 업데이트 합니다.

    ```azurepowershell-interactive
    # Get an API Management instance
    $apimService = Get-AzApiManagement -ResourceGroupName $resourceGroupName -Name $apiManagementName

    # Create a user-assigned identity. This requires installation of the "Az.ManagedServiceIdentity" module.
    $userAssignedIdentity = New-AzUserAssignedIdentity -Name $userAssignedIdentityName -ResourceGroupName $resourceGroupName

    # Update an API Management instance
    $userIdentities = @($userAssignedIdentity.Id)
    Set-AzApiManagement -InputObject $apimService -UserAssignedIdentity $userIdentities
    ```

### <a name="azure-resource-manager-template"></a>Azure Resource Manager 템플릿

API Management 인스턴스는 ID를 사용하여 리소스 정의에 다음 속성을 포함하는 방법으로 만들 수 있습니다.

```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "<RESOURCEID>": {}
    }
}
```

사용자 할당 형식을 추가 하면 인스턴스에 지정 된 사용자 할당 id를 사용 하도록 Azure에 지시 합니다.

예를 들어 전체 Azure Resource Manager 템플릿은 다음과 같이 보일 수 있습니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "0.9.0.0",
    "resources": [{
        "apiVersion": "2019-12-01",
        "name": "contoso",
        "type": "Microsoft.ApiManagement/service",
        "location": "[resourceGroup().location]",
        "tags": {},
        "sku": {
            "name": "Developer",
            "capacity": "1"
        },
        "properties": {
            "publisherEmail": "admin@contoso.com",
            "publisherName": "Contoso"
        },
        "identity": {
            "type": "UserAssigned",
             "userAssignedIdentities": {
                "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName'))]": {}
             }
        },
        "dependsOn": [       
          "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName'))]"
        ]
    }]
}
```

서비스를 만들면 다음과 같은 추가 속성이 있습니다.

```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "<RESOURCEID>": {
            "principalId": "<PRINCIPALID>",
            "clientId": "<CLIENTID>"
        }
    }
}
```

`principalId`속성은 AZURE AD 관리에 사용 되는 id의 고유 식별자입니다. `clientId`속성은 런타임 호출 중에 사용할 id를 지정 하는 데 사용 되는 응용 프로그램의 새 id에 대 한 고유 식별자입니다.

> [!NOTE]
> API Management 인스턴스에는 시스템 할당 id와 사용자 할당 id가 동시에 있을 수 있습니다. 이 경우 `type` 속성은 `SystemAssigned,UserAssigned` 입니다.

### <a name="supported-scenarios"></a>지원되는 시나리오

#### <a name="authenticate-to-the-back-end-by-using-a-user-assigned-identity"></a>사용자 할당 id를 사용 하 여 백 엔드 인증

사용자 할당 id를 사용 하 여 [인증 관리 id](api-management-authentication-policies.md#ManagedIdentity) 정책을 통해 백 엔드에 인증할 수 있습니다.


## <a name="remove-an-identity"></a><a name="remove"></a>ID 제거

포털이 생성 된 것과 동일한 방식으로 포털 또는 Azure Resource Manager 템플릿을 통해 기능을 사용 하지 않도록 설정 하 여 시스템 할당 id를 제거할 수 있습니다. 사용자 할당 ID는 개별 제거할 수 있습니다. 모든 id를 제거 하려면 id 유형을로 설정 `"None"` 합니다.

이런 방식으로 시스템 할당 ID를 제거하면 Azure AD에서도 삭제됩니다. 시스템 할당 id는 API Management 인스턴스가 삭제 될 때 Azure AD 에서도 자동으로 제거 됩니다.

Azure Resource Manager 템플릿을 사용 하 여 모든 id를 제거 하려면이 섹션을 업데이트 합니다.

```json
"identity": {
    "type": "None"
}
```

> [!Important]
> Key Vault에서 사용자 지정 SSL 인증서를 사용 하 여 API Management 인스턴스를 구성 하 고 관리 되는 id를 사용 하지 않도록 설정 하려고 하면 요청이 실패 합니다.
>
> Azure Key Vault 인증서에서 인라인 인코딩된 인증서로 전환 하 고 관리 되는 id를 사용 하지 않도록 설정 하 여 스스로 차단을 해제할 수 있습니다. 자세한 내용은 [사용자 지정 도메인 이름 구성](configure-custom-domain.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

Azure 리소스에 대 한 관리 id에 대해 자세히 알아보세요.

* [Azure 리소스에 대한 관리 ID란?](../active-directory/managed-identities-azure-resources/overview.md)
* [Azure 리소스 관리자 템플릿](https://github.com/Azure/azure-quickstart-templates)
* [정책에서 관리 id를 사용 하 여 인증](./api-management-authentication-policies.md#ManagedIdentity)
