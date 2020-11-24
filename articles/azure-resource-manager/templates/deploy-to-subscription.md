---
title: 구독에 리소스 배포
description: Azure Resource Manager 템플릿에서 리소스 그룹을 만드는 방법을 설명합니다. 또한 Azure 구독 범위에서 리소스를 배포하는 방법도 보여 줍니다.
ms.topic: conceptual
ms.date: 11/23/2020
ms.openlocfilehash: c87f6fa590e1f769816fb0ee3cba3aad1997de15
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2020
ms.locfileid: "95519866"
---
# <a name="subscription-deployments-with-arm-templates"></a>ARM 템플릿을 사용 하 여 구독 배포

리소스 관리를 간소화 하기 위해 Azure Resource Manager 템플릿 (ARM 템플릿)을 사용 하 여 Azure 구독 수준에서 리소스를 배포할 수 있습니다. 예를 들어 구독에 [정책](../../governance/policy/overview.md) 및 [azure 역할 기반 액세스 제어 (azure RBAC)](../../role-based-access-control/overview.md) 를 배포 하 여 구독 전체에 적용할 수 있습니다. 구독 내에서 리소스 그룹을 만들고 구독의 리소스 그룹에 리소스를 배포할 수도 있습니다.

> [!NOTE]
> 구독 수준 배포에서 800개의 다른 리소스 그룹에 배포할 수 있습니다.

구독 수준에서 템플릿을 배포 하려면 Azure CLI, PowerShell, REST API 또는 포털을 사용 합니다.

## <a name="supported-resources"></a>지원되는 리소스

모든 리소스 종류를 구독 수준에 배포할 수 있는 것은 아닙니다. 이 섹션에서는 지원 되는 리소스 종류를 나열 합니다.

Azure 청사진의 경우 다음을 사용 합니다.

* [artifacts](/azure/templates/microsoft.blueprint/blueprints/artifacts)
* [blueprints](/azure/templates/microsoft.blueprint/blueprints)
* [blueprintAssignments](/azure/templates/microsoft.blueprint/blueprintassignments)
* [버전 (청사진)](/azure/templates/microsoft.blueprint/blueprints/versions)

Azure 정책의 경우 다음을 사용 합니다.

* [policyAssignments](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)
* [remediations](/azure/templates/microsoft.policyinsights/remediations)

Azure RBAC (역할 기반 액세스 제어)의 경우 다음을 사용 합니다.

* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)
* [roleDefinitions](/azure/templates/microsoft.authorization/roledefinitions)

리소스 그룹에 배포 하는 중첩 된 템플릿의 경우 다음을 사용 합니다.

* [배포](/azure/templates/microsoft.resources/deployments)

새 리소스 그룹을 만들려면 다음을 사용 합니다.

* [resourceGroups](/azure/templates/microsoft.resources/resourcegroups)

구독을 관리 하려면 다음을 사용 합니다.

* [Advisor 구성](/azure/templates/microsoft.advisor/configurations)
* [budgets](/azure/templates/microsoft.consumption/budgets)
* [분석 프로필 변경](/azure/templates/microsoft.changeanalysis/profile)
* [supportPlanTypes](/azure/templates/microsoft.addons/supportproviders/supportplantypes)
* [태그](/azure/templates/microsoft.resources/tags)

다른 지원 되는 형식은 다음과 같습니다.

* [scopeAssignments](/azure/templates/microsoft.managednetwork/scopeassignments)
* [eventSubscriptions](/azure/templates/microsoft.eventgrid/eventsubscriptions)
* [peerAsns](/azure/templates/microsoft.peering/2019-09-01-preview/peerasns)

## <a name="schema"></a>스키마

구독 수준 배포에 사용하는 스키마는 리소스 그룹 배포에 대한 스키마와 다릅니다.

템플릿의 경우 다음을 사용합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    ...
}
```

매개 변수 파일에 대한 스키마는 모든 배포 범위에 대해 동일합니다. 매개 변수 파일의 경우 다음을 사용합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    ...
}
```

## <a name="deployment-commands"></a>배포 명령

구독에 배포 하려면 구독 수준 배포 명령을 사용 합니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI의 경우 [az deployment sub create](/cli/azure/deployment/sub#az-deployment-sub-create)를 사용합니다. 다음 예제에서는 리소스 그룹을 만드는 템플릿을 배포합니다.

```azurecli-interactive
az deployment sub create \
  --name demoSubDeployment \
  --location centralus \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json" \
  --parameters rgName=demoResourceGroup rgLocation=centralus
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell 배포 명령의 경우 [New-AzDeployment](/powershell/module/az.resources/new-azdeployment) 또는 **New-AzSubscriptionDeployment** 를 사용합니다. 다음 예제에서는 리소스 그룹을 만드는 템플릿을 배포합니다.

```azurepowershell-interactive
New-AzSubscriptionDeployment `
  -Name demoSubDeployment `
  -Location centralus `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json" `
  -rgName demoResourceGroup `
  -rgLocation centralus
```

---

ARM 템플릿 배포에 대 한 배포 명령 및 옵션에 대 한 자세한 내용은 다음을 참조 하세요.

* [ARM 템플릿을 사용 하 여 리소스 배포 및 Azure Portal](deploy-portal.md)
* [ARM 템플릿 및 Azure CLI를 사용하여 리소스 배포](deploy-cli.md)
* [ARM 템플릿 및 Azure PowerShell을 사용하여 리소스 배포](deploy-powershell.md)
* [ARM 템플릿 및 Azure Resource Manager REST API를 사용 하 여 리소스 배포](deploy-rest.md)
* [배포 단추를 사용 하 여 GitHub 리포지토리에서 템플릿 배포](deploy-to-azure-button.md)
* [Cloud Shell에서 ARM 템플릿 배포](deploy-cloud-shell.md)

## <a name="deployment-scopes"></a>배포 범위

구독에 배포 하는 경우 다음에 리소스를 배포할 수 있습니다.

* 작업의 대상 구독
* 테 넌 트의 모든 구독
* 구독 또는 다른 구독 내의 리소스 그룹
* 구독에 대 한 테 넌 트
* 리소스에 [확장 리소스](scope-extension-resources.md) 를 적용할 수 있습니다.

템플릿을 배포 하는 사용자에 게는 지정 된 범위에 대 한 액세스 권한이 있어야 합니다.

이 섹션에서는 다양 한 범위를 지정 하는 방법을 보여 줍니다. 단일 템플릿에서 이러한 여러 범위를 결합할 수 있습니다.

### <a name="scope-to-target-subscription"></a>대상 구독에 대 한 범위

대상 구독에 리소스를 배포 하려면 템플릿의 리소스 섹션에 해당 리소스를 추가 합니다.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/default-sub.json" highlight="5":::

구독에 배포 하는 예제는 [리소스 그룹 만들기](#create-resource-groups) 및 [정책 정의 할당](#assign-policy-definition)을 참조 하세요.

### <a name="scope-to-other-subscription"></a>다른 구독에 대 한 범위

작업의 구독과 다른 구독에 리소스를 배포 하려면 중첩 된 배포를 추가 합니다. 속성을 `subscriptionId` 배포 하려는 구독의 ID로 설정 합니다. `location`중첩 된 배포에 대 한 속성을 설정 합니다.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/sub-to-sub.json" highlight="9,10,14":::

### <a name="scope-to-resource-group"></a>리소스 그룹에 대 한 범위

구독 내의 리소스 그룹에 리소스를 배포 하려면 중첩 된 배포를 추가 하 고 속성을 포함 `resourceGroup` 합니다. 다음 예제에서 중첩 된 배포는 이라는 리소스 그룹을 대상으로 `demoResourceGroup` 합니다.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/sub-to-resource-group.json" highlight="9,13":::

리소스 그룹에 배포 하는 예제는 [리소스 그룹 및 리소스 만들기](#create-resource-group-and-resources)를 참조 하세요.

### <a name="scope-to-tenant"></a>테 넌 트로 범위

로 설정 된를 설정 하 여 테 넌 트에서 리소스를 만들 수 있습니다 `scope` `/` . 템플릿을 배포 하는 사용자에 게는 [테 넌 트에 배포 하는 데 필요한 액세스](deploy-to-tenant.md#required-access)권한이 있어야 합니다.

에서 중첩 된 배포를 사용 하 `scope` 고를 설정할 수 있습니다 `location` .

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/subscription-to-tenant.json" highlight="9,10,14":::

또는 `/` 관리 그룹과 같은 일부 리소스 형식에 대해 범위를로 설정할 수 있습니다.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/subscription-create-mg.json" highlight="12,15":::

## <a name="deployment-location-and-name"></a>배포 위치 및 이름

구독 수준 배포의 경우 배포를 위한 위치를 제공해야 합니다. 배포의 위치는 배포하는 리소스의 위치와는 별개입니다. 배포 위치는 배포 데이터를 저장할 위치를 지정합니다.

배포 이름을 제공하거나 기본 배포 이름을 사용할 수 있습니다. 기본 이름은 템플릿 파일의 이름입니다. 예를 들어 **azuredeploy.json** 이라는 템플릿을 배포하면 **azuredeploy** 라는 기본 배포 이름을 만듭니다.

각 배포 이름의 경우 위치는 변경할 수 없습니다. 다른 위치의 이름이 동일한 기존 배포가 있는 경우 하나의 위치에서 배포를 만들 수 없습니다. 오류 코드 `InvalidDeploymentLocation`을 수신하게 되면 해당 이름의 이전 배포와 다른 이름이나 동일한 위치를 사용합니다.

## <a name="resource-groups"></a>리소스 그룹

### <a name="create-resource-groups"></a>리소스 그룹 만들기

ARM 템플릿에서 리소스 그룹을 만들려면 리소스 그룹의 이름 및 위치를 사용 하 여 [Microsoft .resources/resourceGroups](/azure/templates/microsoft.resources/allversions) 리소스를 정의 합니다.

다음 템플릿은 빈 리소스 그룹을 만듭니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
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
      "apiVersion": "2020-06-01",
      "name": "[parameters('rgName')]",
      "location": "[parameters('rgLocation')]",
      "properties": {}
    }
  ],
  "outputs": {}
}
```

리소스 그룹을 2개 이상 만들려면 리소스 그룹에서 [요소 복사](copy-resources.md)를 사용합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
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
      "apiVersion": "2020-06-01",
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

리소스 반복에 대한 자세한 내용은 [Azure Resource Manager 템플릿에서 두 개 이상의 리소스 인스턴스 배포](./copy-resources.md) 및 [자습서: Resource Manager 템플릿을 사용하여 여러 리소스 인스턴스 만들기](./template-tutorial-create-multiple-instances.md)를 참조하세요.

### <a name="create-resource-group-and-resources"></a>리소스 그룹 및 리소스 만들기

리소스 그룹을 만들고 거기에 리소스를 배포하려면 중첩 템플릿을 사용합니다. 중첩 템플릿은 리소스 그룹에 배포할 리소스를 정의합니다. 리소스를 배포하기 전에 리소스 그룹이 존재하도록 중첩 템플릿을 리소스 그룹에 종속된 것으로 설정합니다. 최대 800개의 리소스 그룹에 배포할 수 있습니다.

다음 예제는 리소스 그룹을 만들고 스토리지 계정을 배포합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
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
      "apiVersion": "2020-06-01",
      "name": "[parameters('rgName')]",
      "location": "[parameters('rgLocation')]",
      "properties": {}
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-06-01",
      "name": "storageDeployment",
      "resourceGroup": "[parameters('rgName')]",
      "dependsOn": [
        "[resourceId('Microsoft.Resources/resourceGroups/', parameters('rgName'))]"
      ],
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {},
          "variables": {},
          "resources": [
            {
              "type": "Microsoft.Storage/storageAccounts",
              "apiVersion": "2019-06-01",
              "name": "[variables('storageName')]",
              "location": "[parameters('rgLocation')]",
              "sku": {
                "name": "Standard_LRS"
              },
              "kind": "StorageV2"
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

## <a name="azure-policy"></a>Azure Policy

### <a name="assign-policy-definition"></a>정책 정의 할당

다음 예제는 기존 정책 정의를 구독에 할당합니다. 정책 정의에서 매개 변수를 사용하는 경우 개체로 제공합니다. 정책 정의에서 매개 변수를 사용하지 않으면 기본 빈 개체를 사용합니다.

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
      "apiVersion": "2018-03-01",
      "name": "[parameters('policyName')]",
      "properties": {
        "scope": "[subscription().id]",
        "policyDefinitionId": "[parameters('policyDefinitionID')]",
        "parameters": "[parameters('policyParameters')]"
      }
    }
  ]
}
```

Azure CLI를 사용하여 이 템플릿을 배포하려면 다음 기능을 사용합니다.

```azurecli-interactive
# Built-in policy definition that accepts parameters
definition=$(az policy definition list --query "[?displayName=='Allowed locations'].id" --output tsv)

az deployment sub create \
  --name demoDeployment \
  --location centralus \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json" \
  --parameters policyDefinitionID=$definition policyName=setLocation policyParameters="{'listOfAllowedLocations': {'value': ['westus']} }"
```

PowerShell에서 이 템플릿을 배포하려면 다음 기능을 사용합니다.

```azurepowershell-interactive
$definition = Get-AzPolicyDefinition | Where-Object { $_.Properties.DisplayName -eq 'Allowed locations' }

$locations = @("westus", "westus2")
$policyParams =@{listOfAllowedLocations = @{ value = $locations}}

New-AzSubscriptionDeployment `
  -Name policyassign `
  -Location centralus `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json" `
  -policyDefinitionID $definition.PolicyDefinitionId `
  -policyName setLocation `
  -policyParameters $policyParams
```

### <a name="create-and-assign-policy-definitions"></a>정책 정의 만들기 및 할당

정책 정의를 동일한 템플릿에 [정의](../../governance/policy/concepts/definition-structure.md)하고 할당할 수 있습니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Authorization/policyDefinitions",
      "apiVersion": "2018-05-01",
      "name": "locationpolicy",
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
      "apiVersion": "2018-05-01",
      "name": "location-lock",
      "dependsOn": [
        "locationpolicy"
      ],
      "properties": {
        "scope": "[subscription().id]",
        "policyDefinitionId": "[subscriptionResourceId('Microsoft.Authorization/policyDefinitions', 'locationpolicy')]"
      }
    }
  ]
}
```

구독에서 정책 정의를 만들어 구독에 할당하려면 다음 CLI 명령을 사용합니다.

```azurecli
az deployment sub create \
  --name demoDeployment \
  --location centralus \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policydefineandassign.json"
```

PowerShell에서 이 템플릿을 배포하려면 다음 기능을 사용합니다.

```azurepowershell
New-AzSubscriptionDeployment `
  -Name definePolicy `
  -Location centralus `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policydefineandassign.json"
```

## <a name="azure-blueprints"></a>Azure Blueprints

### <a name="create-blueprint-definition"></a>청사진 정의 만들기

템플릿에서 청사진 정의를 [만들](../../governance/blueprints/tutorials/create-from-sample.md) 수 있습니다.

:::code language="json" source="~/quickstart-templates/subscription-deployments/blueprints-new-blueprint/azuredeploy.json":::

구독에서 청사진 정의를 만들려면 다음 CLI 명령을 사용합니다.

```azurecli
az deployment sub create \
  --name demoDeployment \
  --location centralus \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/subscription-deployments/blueprints-new-blueprint/azuredeploy.json"
```

PowerShell에서 이 템플릿을 배포하려면 다음 기능을 사용합니다.

```azurepowershell
New-AzSubscriptionDeployment `
  -Name demoDeployment `
  -Location centralus `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/subscription-deployments/blueprints-new-blueprint/azuredeploy.json"
```

## <a name="access-control"></a>Access Control

역할 할당에 대해 알아보려면 [Azure Resource Manager 템플릿을 사용 하 여 Azure 역할 할당 추가](../../role-based-access-control/role-assignments-template.md)를 참조 하세요.

다음 예제에서는 리소스 그룹을 만들고 해당 그룹에 잠금을 적용 한 다음 보안 주체에 역할을 할당 합니다.

:::code language="json" source="~/quickstart-templates/subscription-deployments/create-rg-lock-role-assignment/azuredeploy.json":::

## <a name="next-steps"></a>다음 단계

* Azure Security Center에 대한 작업 영역 설정을 배포하는 예제는 [deployASCwithWorkspaceSettings.json](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json)을 참조하세요.
* 샘플 템플릿은 [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-deployments)에서 찾을 수 있습니다.
* [관리 그룹 수준](deploy-to-management-group.md) 및 [테넌트 수준](deploy-to-tenant.md)에서 템플릿을 배포할 수도 있습니다.
