---
title: 구독에 리소스 그룹 및 리소스 만들기 - Azure Resource Manager 템플릿
description: Azure Resource Manager 템플릿에서 리소스 그룹을 만드는 방법을 설명합니다. 또한 Azure 구독 범위에서 리소스를 배포하는 방법도 보여 줍니다.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/30/2019
ms.author: tomfitz
ms.openlocfilehash: ce95406cbcb6d9514159d84d653a9ff361713aec
ms.sourcegitcommit: 947b331c4d03f79adcb45f74d275ac160c4a2e83
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/05/2019
ms.locfileid: "55744401"
---
# <a name="create-resource-groups-and-resources-at-the-subscription-level"></a>구독 수준에서 리소스 그룹 및 리소스 만들기

일반적으로 Azure 리소스는 Azure 구독의 리소스 그룹에 배포합니다. 그러나 Azure 리소스 그룹을 만들고 구독 수준에서 Azure 리소스를 만들 수도 있습니다. 구독 수준에서 템플릿을 배포하려면 Azure CLI 및 Azure PowerShell을 사용합니다. Azure Portal은 구독 수준의 배포를 지원하지 않습니다.

Azure Resource Manager 템플릿에서 리소스 그룹을 만들려면 리소스 그룹의 이름 및 위치를 사용하여 [**Microsoft.Resources/resourceGroups**](/azure/templates/microsoft.resources/allversions) 리소스를 정의합니다. 리소스 그룹을 만든 후 동일한 템플릿에서 해당 리소스 그룹에 리소스를 배포할 수 있습니다. 구독 수준에서 배포할 수 있는 리소스는 다음과 같습니다. [정책](../azure-policy/azure-policy-introduction.md) 및 [역할 기반 액세스 제어](../role-based-access-control/overview.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="deployment-considerations"></a>배포 고려 사항

구독 수준 배포는 다음 측면에서 리소스 그룹 배포와 다릅니다.

### <a name="schema-and-commands"></a>스키마 및 명령

구독 수준 배포에 사용하는 스키마 및 명령은 리소스 그룹 배포와 다릅니다. 

스키마의 경우 `https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#`을 사용합니다.

Azure CLI 배포 명령의 경우 [az deployment create](/cli/azure/deployment?view=azure-cli-latest#az-deployment-create)를 사용합니다. 예를 들어 다음 CLI 명령은 리소스 그룹을 만드는 템플릿을 배포합니다.

```azurecli
az deployment create \
  --name demoDeployment \
  --location centralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json \
  --parameters rgName=demoResourceGroup rgLocation=centralus
```

PowerShell 배포 명령의 경우 [New-AzDeployment](/powershell/module/az.resources/new-azdeployment)를 사용합니다. 예를 들어 다음 PowerShell 명령은 리소스 그룹을 만드는 템플릿을 배포합니다.

```azurepowershell
New-AzDeployment `
  -Name demoDeployment `
  -Location centralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json `
  -rgName demoResourceGroup `
  -rgLocation centralus
```

### <a name="deployment-name-and-location"></a>배포 이름 및 위치

구독에 배포할 때 배포할 위치를 입력해야 합니다. 배포할 이름을 입력할 수도 있습니다. 배포할 이름을 지정하지 않으면 템플릿의 이름을 배포 이름으로 사용합니다. 예를 들어 **azuredeploy.json**이라는 템플릿을 배포하면 **azuredeploy**라는 기본 배포 이름을 만듭니다.

구독 수준 배포의 위치를 변경할 수 없습니다. 이름이 동일하지만 위치가 다른 기존 배포가 있는 경우 하나의 위치에서 배포를 만들 수 없습니다. 오류 코드 `InvalidDeploymentLocation`을 수신하게 되면 해당 이름의 이전 배포와 다른 이름이나 동일한 위치를 사용합니다.

### <a name="use-template-functions"></a>템플릿 함수 사용

구독 수준 배포의 경우 템플릿 함수를 사용할 때 몇 가지 중요한 고려 사항이 있습니다.

* [resourceGroup()](resource-group-template-functions-resource.md#resourcegroup) 함수는 지원되지 **않습니다**.
* [resourceId()](resource-group-template-functions-resource.md#resourceid) 함수는 지원됩니다. 이를 사용하여 구독 수준 배포에 사용되는 리소스에 대한 리소스 ID를 가져옵니다. 예를 들어 `resourceId('Microsoft.Authorization/roleDefinitions/', parameters('roleDefinition'))`를 사용하여 정책 정의에 대한 리소스 ID를 가져옵니다.
* [reference()](resource-group-template-functions-resource.md#reference) 및 [list()](resource-group-template-functions-resource.md#list) 함수는 지원됩니다.

## <a name="create-resource-groups"></a>리소스 그룹 만들기

다음 템플릿은 빈 리소스 그룹을 만듭니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.1",
    "parameters": {
        "rgName": {
            "type": "string"
        },
        "rgLocation": {
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Resources/resourceGroups",
            "apiVersion": "2018-05-01",
            "location": "[parameters('rgLocation')]",
            "name": "[parameters('rgName')]",
            "properties": {}
        }
    ],
    "outputs": {}
}
```

템플릿 스키마는 [여기](/azure/templates/microsoft.resources/allversions)에서 찾을 수 있습니다. 유사한 템플릿은 [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-level-deployments)에서 찾을 수 있습니다.

## <a name="create-multiple-resource-groups"></a>여러 리소스 그룹 만들기

리소스 그룹을 2개 이상 만들려면 리소스 그룹에서 [요소 복사](resource-group-create-multiple.md)를 사용합니다. 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.1",
    "parameters": {
        "rgNamePrefix": {
            "type": "string"
        },
        "rgLocation": {
            "type": "string"
        },
        "instanceCount": {
            "type": "int"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Resources/resourceGroups",
            "apiVersion": "2018-05-01",
            "location": "[parameters('rgLocation')]",
            "name": "[concat(parameters('rgNamePrefix'), copyIndex())]",
            "copy": {
                "name": "rgCopy",
                "count": "[parameters('instanceCount')]"
            },
            "properties": {}
        }
    ],
    "outputs": {}
}
```

리소스 반복에 대한 자세한 내용은 [Azure Resource Manager 템플릿에서 두 개 이상의 리소스 또는 속성 인스턴스 배포](./resource-group-create-multiple.md) 및 [자습서: Resource Manager 템플릿을 사용하여 여러 리소스 인스턴스 만들기](./resource-manager-tutorial-create-multiple-instances.md)를 참조하세요.

## <a name="create-resource-group-and-deploy-resources"></a>리소스 그룹 만들기 및 리소스 배포

리소스 그룹을 만들고 거기에 리소스를 배포하려면 중첩 템플릿을 사용합니다. 중첩 템플릿은 리소스 그룹에 배포할 리소스를 정의합니다. 리소스를 배포하기 전에 리소스 그룹이 존재하도록 중첩 템플릿을 리소스 그룹에 종속된 것으로 설정합니다.

다음 예제는 리소스 그룹을 만들고 저장소 계정을 배포합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.1",
    "parameters": {
        "rgName": {
            "type": "string"
        },
        "rgLocation": {
            "type": "string"
        },
        "storagePrefix": {
            "type": "string",
            "maxLength": 11
        }
    },
    "variables": {
        "storageName": "[concat(parameters('storagePrefix'), uniqueString(subscription().id, parameters('rgName')))]"
    },
    "resources": [
        {
            "type": "Microsoft.Resources/resourceGroups",
            "apiVersion": "2018-05-01",
            "location": "[parameters('rgLocation')]",
            "name": "[parameters('rgName')]",
            "properties": {}
        },
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2018-05-01",
            "name": "storageDeployment",
            "resourceGroup": "[parameters('rgName')]",
            "dependsOn": [
                "[resourceId('Microsoft.Resources/resourceGroups/', parameters('rgName'))]"
            ],
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {},
                    "variables": {},
                    "resources": [
                        {
                            "type": "Microsoft.Storage/storageAccounts",
                            "apiVersion": "2017-10-01",
                            "name": "[variables('storageName')]",
                            "location": "[parameters('rgLocation')]",
                            "kind": "StorageV2",
                            "sku": {
                                "name": "Standard_LRS"
                            }
                        }
                    ],
                    "outputs": {}
                }
            }
        }
    ],
    "outputs": {}
}
```

## <a name="create-policies"></a>정책 만들기

### <a name="assign-policy"></a>정책 할당

다음 예제는 기존 정책 정의를 구독에 할당합니다. 정책이 매개 변수를 사용하는 경우 개체로 제공합니다. 정책이 매개 변수를 사용하지 않으면 기본 빈 개체를 사용합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "policyDefinitionID": {
            "type": "string"
        },
        "policyName": {
            "type": "string"
        },
        "policyParameters": {
            "type": "object",
            "defaultValue": {}
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Authorization/policyAssignments",
            "name": "[parameters('policyName')]",
            "apiVersion": "2018-03-01",
            "properties": {
                "scope": "[subscription().id]",
                "policyDefinitionId": "[parameters('policyDefinitionID')]",
                "parameters": "[parameters('policyParameters')]"
            }
        }
    ]
}
```

Azure 구독에 기본 제공 정책을 적용하려면 다음 Azure CLI 명령을 사용합니다.

```azurecli
# Built-in policy that does not accept parameters
definition=$(az policy definition list --query "[?displayName=='Audit resource location matches resource group location'].id" --output tsv)

az deployment create \
  --name demoDeployment \
  --location centralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json \
  --parameters policyDefinitionID=$definition policyName=auditRGLocation
```

PowerShell에서 이 템플릿을 배포하려면 다음 기능을 사용합니다.

```azurepowershell
$definition = Get-AzPolicyDefinition | Where-Object { $_.Properties.DisplayName -eq 'Audit resource location matches resource group location' }

New-AzDeployment `
  -Name policyassign `
  -Location centralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json `
  -policyDefinitionID $definition.PolicyDefinitionId `
  -policyName auditRGLocation
```

Azure 구독에 기본 제공 정책을 적용하려면 다음 Azure CLI 명령을 사용합니다.

```azurecli
# Built-in policy that accepts parameters
definition=$(az policy definition list --query "[?displayName=='Allowed locations'].id" --output tsv)

az deployment create \
  --name demoDeployment \
  --location centralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json \
  --parameters policyDefinitionID=$definition policyName=setLocation policyParameters="{'listOfAllowedLocations': {'value': ['westus']} }"
```

PowerShell에서 이 템플릿을 배포하려면 다음 기능을 사용합니다.

```azurepowershell
$definition = Get-AzPolicyDefinition | Where-Object { $_.Properties.DisplayName -eq 'Allowed locations' }

$locations = @("westus", "westus2")
$policyParams =@{listOfAllowedLocations = @{ value = $locations}}

New-AzDeployment `
  -Name policyassign `
  -Location centralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json `
  -policyDefinitionID $definition.PolicyDefinitionId `
  -policyName setLocation `
  -policyParameters $policyParams
```

### <a name="define-and-assign-policy"></a>정책 정의 및 할당

정책은 같은 서식 파일에 [정의](../azure-policy/policy-definition.md)하고 할당할 수 있습니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Authorization/policyDefinitions",
            "name": "locationpolicy",
            "apiVersion": "2018-05-01",
            "properties": {
                "policyType": "Custom",
                "parameters": {},
                "policyRule": {
                    "if": {
                        "field": "location",
                        "equals": "northeurope"
                    },
                    "then": {
                        "effect": "deny"
                    }
                }
            }
        },
        {
            "type": "Microsoft.Authorization/policyAssignments",
            "name": "location-lock",
            "apiVersion": "2018-05-01",
            "dependsOn": [
                "locationpolicy"
            ],
            "properties": {
                "scope": "[subscription().id]",
                "policyDefinitionId": "[resourceId('Microsoft.Authorization/policyDefinitions', 'locationpolicy')]"
            }
        }
    ]
}
```

구독에서 정책 정의를 만들어 구독에 적용하려면 다음 CLI 명령을 사용합니다.

```azurecli
az deployment create \
  --name demoDeployment \
  --location centralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policydefineandassign.json
```

PowerShell에서 이 템플릿을 배포하려면 다음 기능을 사용합니다.

```azurepowershell
New-AzDeployment `
  -Name definePolicy `
  -Location centralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policydefineandassign.json
```

## <a name="create-roles"></a>역할 만들기

### <a name="assign-role-at-subscription"></a>구독에서 역할 할당

다음 예에서는 구독의 사용자나 그룹에 역할을 할당합니다. 이 예에서는 할당 범위가 구독으로 자동 설정되므로 범위를 지정하지 않습니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "principalId": {
            "type": "string"
        },
        "roleDefinitionId": {
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "name": "[guid(parameters('principalId'), deployment().name)]",
            "apiVersion": "2017-09-01",
            "properties": {
                "roleDefinitionId": "[resourceId('Microsoft.Authorization/roleDefinitions', parameters('roleDefinitionId'))]",
                "principalId": "[parameters('principalId')]"
            }
        }
    ]
}
```

구독의 역할에 Active Directory 그룹을 할당하려면 다음 Azure CLI 명령을 사용합니다.

```azurecli
# Get ID of the role you want to assign
role=$(az role definition list --name Contributor --query [].name --output tsv)

# Get ID of the AD group to assign the role to
principalid=$(az ad group show --group demogroup --query objectId --output tsv)

az deployment create \
  --name demoDeployment \
  --location centralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/roleassign.json \
  --parameters principalId=$principalid roleDefinitionId=$role
```

PowerShell에서 이 템플릿을 배포하려면 다음 기능을 사용합니다.

```azurepowershell
$role = Get-AzRoleDefinition -Name Contributor

$adgroup = Get-AzADGroup -DisplayName demogroup

New-AzDeployment `
  -Name demoRole `
  -Location centralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/roleassign.json `
  -roleDefinitionId $role.Id `
  -principalId $adgroup.Id
```

### <a name="assign-role-at-scope"></a>범위에서 역할 할당

다음 구독 수준 템플릿은 구독 내의 리소스 그룹으로 범위가 지정된 사용자나 그룹에 역할을 할당합니다. 해당 범위는 배포 수준 이하여야 합니다. 구독에 배포를 한 다음 해당 구독 내의 리소스 그룹으로 범위가 지정된 역할 할당을 지정할 수 있습니다. 그러나 리소스 그룹에 배포를 한 다음 역할 할당 범위를 구독으로 지정할 수는 없습니다.

범위에서 역할을 할당하려면 중첩 배포를 사용합니다. 리소스 그룹 이름은 배포 리소스의 속성과 역할 할당의 범위 속성에서 모두 지정됩니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.1",
    "parameters": {
        "principalId": {
            "type": "string"
        },
        "roleDefinitionId": {
            "type": "string"
        },
        "rgName": {
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2018-05-01",
            "name": "assignRole",
            "resourceGroup": "[parameters('rgName')]",
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {},
                    "variables": {},
                    "resources": [
                        {
                            "type": "Microsoft.Authorization/roleAssignments",
                            "name": "[guid(parameters('principalId'), deployment().name)]",
                            "apiVersion": "2017-09-01",
                            "properties": {
                                "roleDefinitionId": "[resourceId('Microsoft.Authorization/roleDefinitions', parameters('roleDefinitionId'))]",
                                "principalId": "[parameters('principalId')]",
                                "scope": "[concat(subscription().id, '/resourceGroups/', parameters('rgName'))]"
                            }
                        }
                    ],
                    "outputs": {}
                }
            }
        }
    ],
    "outputs": {}
}
```

구독의 역할에 Active Directory 그룹을 할당하려면 다음 Azure CLI 명령을 사용합니다.

```azurecli
# Get ID of the role you want to assign
role=$(az role definition list --name Contributor --query [].name --output tsv)

# Get ID of the AD group to assign the role to
principalid=$(az ad group show --group demogroup --query objectId --output tsv)

az deployment create \
  --name demoDeployment \
  --location centralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/scopedRoleAssign.json \
  --parameters principalId=$principalid roleDefinitionId=$role rgName demoRg
```

PowerShell에서 이 템플릿을 배포하려면 다음 기능을 사용합니다.

```azurepowershell
$role = Get-AzRoleDefinition -Name Contributor

$adgroup = Get-AzADGroup -DisplayName demogroup

New-AzDeployment `
  -Name demoRole `
  -Location centralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/scopedRoleAssign.json `
  -roleDefinitionId $role.Id `
  -principalId $adgroup.Id `
  -rgName demoRg
```

## <a name="next-steps"></a>다음 단계

* Azure Security Center에 대한 작업 영역 설정을 배포하는 예제는 [deployASCwithWorkspaceSettings.json](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json)을 참조하세요.
* Azure 리소스 관리자 템플릿을 만드는 방법에 대한 자세한 내용은 [템플릿 작성](resource-group-authoring-templates.md)을 참조하세요. 
* 템플릿에서 사용할 수 있는 함수 목록은 [템플릿 함수](resource-group-template-functions.md)를 참조하세요.
