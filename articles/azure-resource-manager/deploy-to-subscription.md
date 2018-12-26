---
title: Azure 구독에 리소스 배포 | Microsoft Docs
description: 구독 범위에서 리소스를 배포하는 Azure Resource Manager 템플릿을 만드는 방법을 설명합니다.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/10/2018
ms.author: tomfitz
ms.openlocfilehash: 1d281ebe80c6089c559cfaa77f4875a856566092
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/10/2018
ms.locfileid: "49079381"
---
# <a name="deploy-resources-to-an-azure-subscription"></a>Azure 구독에 리소스 배포

일반적으로 리소스는 Azure 구독의 리소스 그룹에 배포합니다. 하지만 일부 리소스는 Azure 구독 수준에서 배포할 수 있습니다. 이러한 리소스는 구독 전체에 적용됩니다. [정책](../azure-policy/azure-policy-introduction.md), [역할 기반 액세스 제어](../role-based-access-control/overview.md) 및 [Azure Security Center](../security-center/security-center-intro.md)는 리소스 그룹 수준이 아닌 구독 수준에서 적용하는 서비스입니다.

이 아티클에서는 Azure CLI 및 PowerShell을 사용하여 템플릿을 배포합니다.

## <a name="name-and-location-for-deployment"></a>배포할 위치의 이름을 지정합니다.

구독에 배포할 때 배포할 위치를 입력해야 합니다. 배포할 이름을 입력할 수도 있습니다. 배포할 이름을 지정하지 않으면 템플릿의 이름을 배포 이름으로 사용합니다. 예를 들어 **azuredeploy.json**이라는 템플릿을 배포하면 **azuredeploy**라는 기본 배포 이름을 만듭니다.

구독 수준 배포의 위치를 변경할 수 없습니다. 이름이 동일하지만 위치가 다른 기존 배포가 있는 경우 하나의 위치에서 배포를 만들 수 없습니다. 오류 코드 `InvalidDeploymentLocation`을 수신하게 되면 해당 이름의 이전 배포와 다른 이름이나 동일한 위치를 사용합니다.

## <a name="using-template-functions"></a>템플릿 함수 사용

구독 수준 배포의 경우 템플릿 함수를 사용할 때 몇 가지 중요한 고려 사항이 있습니다.

* [resourceGroup()](resource-group-template-functions-resource.md#resourcegroup) 함수는 지원되지 **않습니다**.
* [resourceId()](resource-group-template-functions-resource.md#resourceid) 함수는 지원됩니다. 이를 사용하여 구독 수준 배포에 사용되는 리소스에 대한 리소스 ID를 가져옵니다. 예를 들어 `resourceId('Microsoft.Authorization/roleDefinitions/', parameters('roleDefinition'))`를 사용하여 정책 정의에 대한 리소스 ID를 가져옵니다.
* [reference()](resource-group-template-functions-resource.md#reference) 및 [list()](resource-group-template-functions-resource.md#list) 함수는 지원됩니다.

## <a name="assign-policy"></a>정책 할당

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

Azure 구독에 기본 제공 정책을 적용하려면 다음 Azure CLI 명령을 사용합니다. 이 예제에는 정책에 매개 변수가 없습니다.

```azurecli-interactive
# Built-in policy that does not accept parameters
definition=$(az policy definition list --query "[?displayName=='Audit resource location matches resource group location'].id" --output tsv)

az deployment create \
  -n policyassign \
  -l southcentralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json \
  --parameters policyDefinitionID=$definition policyName=auditRGLocation
```

PowerShell에서 이 템플릿을 배포하려면 다음 기능을 사용합니다.

```azurepowershell-interactive
$definition = Get-AzureRmPolicyDefinition | Where-Object { $_.Properties.DisplayName -eq 'Audit resource location matches resource group location' }

New-AzureRmDeployment `
  -Name policyassign `
  -Location southcentralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json `
  -policyDefinitionID $definition.PolicyDefinitionId `
  -policyName auditRGLocation
```

Azure 구독에 기본 제공 정책을 적용하려면 다음 Azure CLI 명령을 사용합니다. 이 예제에는 정책에 매개 변수가 있습니다.

```azurecli-interactive
# Built-in policy that accepts parameters
definition=$(az policy definition list --query "[?displayName=='Allowed locations'].id" --output tsv)

az deployment create \
  -n policyassign \
  -l southcentralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json \
  --parameters policyDefinitionID=$definition policyName=setLocation policyParameters="{'listOfAllowedLocations': {'value': ['westus']} }"
```

PowerShell에서 이 템플릿을 배포하려면 다음 기능을 사용합니다.

```azurepowershell-interactive
$definition = Get-AzureRmPolicyDefinition | Where-Object { $_.Properties.DisplayName -eq 'Allowed locations' }

$locations = @("westus", "westus2")
$policyParams =@{listOfAllowedLocations = @{ value = $locations}}

New-AzureRmDeployment `
  -Name policyassign `
  -Location southcentralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json `
  -policyDefinitionID $definition.PolicyDefinitionId `
  -policyName setLocation `
  -policyParameters $policyParams
```

## <a name="define-and-assign-policy"></a>정책 정의 및 할당

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

구독에서 정책 정의를 만들고 구독에 적용하려면 다음 CLI 명령을 사용합니다.

```azurecli-interactive
az deployment create \
  -n definePolicy \
  -l southcentralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policydefineandassign.json
```

PowerShell에서 이 템플릿을 배포하려면 다음 기능을 사용합니다.

```azurepowershell-interactive
New-AzureRmDeployment `
  -Name definePolicy `
  -Location southcentralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policydefineandassign.json
```

## <a name="assign-role"></a>역할 할당

다음 예제는 사용자 또는 그룹에 역할을 할당합니다.

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

```azurecli-interactive
# Get ID of the role you want to assign
role=$(az role definition list --name Contributor --query [].name --output tsv)

# Get ID of the AD group to assign the role to
principalid=$(az ad group show --group demogroup --query objectId --output tsv)

az deployment create \
  -n demoRole \
  -l southcentralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/roleassign.json \
  --parameters principalId=$principalid roleDefinitionId=$role
```

PowerShell에서 이 템플릿을 배포하려면 다음 기능을 사용합니다.

```azurepowershell-interactive
$role = Get-AzureRmRoleDefinition -Name Contributor

$adgroup = Get-AzureRmADGroup -DisplayName demogroup

New-AzureRmDeployment `
  -Name demoRole `
  -Location southcentralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/roleassign.json `
  -roleDefinitionId $role.Id `
  -principalId $adgroup.Id
```

## <a name="next-steps"></a>다음 단계
* Azure Security Center에 대한 작업 영역 설정을 배포하는 예제는 [deployASCwithWorkspaceSettings.json](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json)을 참조하세요.
* 리소스 그룹을 만들려면 [Azure Resource Manager 템플릿에서 리소스 그룹 만들기](create-resource-group-in-template.md)를 참조하세요.
* Azure 리소스 관리자 템플릿을 만드는 방법에 대한 자세한 내용은 [템플릿 작성](resource-group-authoring-templates.md)을 참조하세요. 
* 템플릿에서 사용할 수 있는 함수 목록은 [템플릿 함수](resource-group-template-functions.md)를 참조하세요.

