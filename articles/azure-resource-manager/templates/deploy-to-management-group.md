---
title: 관리 그룹에 리소스 배포
description: Azure Resource Manager 템플릿의 관리 그룹 범위에서 리소스를 배포 하는 방법을 설명 합니다.
ms.topic: conceptual
ms.date: 09/15/2020
ms.openlocfilehash: 2325e9f5a03f7451492c9b9b8e929df95ddc3852
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/16/2020
ms.locfileid: "90605229"
---
# <a name="create-resources-at-the-management-group-level"></a>관리 그룹 수준에서 리소스 만들기

조직이 완성 되 면 Azure Resource Manager 템플릿 (ARM 템플릿)을 배포 하 여 관리 그룹 수준에서 리소스를 만들 수 있습니다. 예를 들어 관리 그룹에 대 한 [정책을](../../governance/policy/overview.md) 정의 하 고 할당 하거나 azure [RBAC (역할 기반 액세스 제어)](../../role-based-access-control/overview.md) 를 할당 해야 할 수 있습니다. 관리 그룹 수준 템플릿을 사용 하면 정책을 선언적으로 적용 하 고 관리 그룹 수준에서 역할을 할당할 수 있습니다.

## <a name="supported-resources"></a>지원되는 리소스

모든 리소스 종류를 관리 그룹 수준에 배포할 수 있는 것은 아닙니다. 이 섹션에서는 지원 되는 리소스 종류를 나열 합니다.

Azure 청사진의 경우 다음을 사용 합니다.

* [artifacts](/azure/templates/microsoft.blueprint/blueprints/artifacts)
* [blueprints](/azure/templates/microsoft.blueprint/blueprints)
* [blueprintAssignments](/azure/templates/microsoft.blueprint/blueprintassignments)
* [버전](/azure/templates/microsoft.blueprint/blueprints/versions)

Azure 정책의 경우 다음을 사용 합니다.

* [policyAssignments](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)
* [remediations](/azure/templates/microsoft.policyinsights/remediations)

역할 기반 액세스 제어를 사용 하려면 다음을 사용 합니다.

* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)
* [roleDefinitions](/azure/templates/microsoft.authorization/roledefinitions)

구독 또는 리소스 그룹에 배포 하는 중첩 된 템플릿의 경우 다음을 사용 합니다.

* [배포](/azure/templates/microsoft.resources/deployments)

리소스를 관리 하려면 다음을 사용 합니다.

* [태그](/azure/templates/microsoft.resources/tags)

### <a name="schema"></a>스키마

관리 그룹 배포에 사용 하는 스키마는 리소스 그룹 배포에 대 한 스키마와 다릅니다.

템플릿의 경우 다음을 사용합니다.

```json
https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#
```

매개 변수 파일에 대한 스키마는 모든 배포 범위에 대해 동일합니다. 매개 변수 파일의 경우 다음을 사용합니다.

```json
https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#
```

## <a name="deployment-commands"></a>배포 명령

관리 그룹 배포에 대 한 명령은 리소스 그룹 배포에 대 한 명령과 다릅니다.

Azure CLI의 경우 [az deployment mg create](/cli/azure/deployment/mg#az-deployment-mg-create)를 사용 합니다.

```azurecli-interactive
az deployment mg create \
  --name demoMGDeployment \
  --location WestUS \
  --management-group-id myMG \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/management-level-deployment/azuredeploy.json"
```

Azure PowerShell의 경우 [AzManagementGroupDeployment](/powershell/module/az.resources/new-azmanagementgroupdeployment)를 사용 합니다.

```azurepowershell-interactive
New-AzManagementGroupDeployment `
  -Name demoMGDeployment `
  -Location "West US" `
  -ManagementGroupId "myMG" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/management-level-deployment/azuredeploy.json"
```

REST API의 경우, [관리 그룹 범위에서 배포-만들기](/rest/api/resources/deployments/createorupdateatmanagementgroupscope)를 사용 합니다.

## <a name="deployment-location-and-name"></a>배포 위치 및 이름

관리 그룹 수준 배포의 경우 배포 위치를 제공 해야 합니다. 배포의 위치는 배포하는 리소스의 위치와는 별개입니다. 배포 위치는 배포 데이터를 저장할 위치를 지정합니다.

배포 이름을 제공하거나 기본 배포 이름을 사용할 수 있습니다. 기본 이름은 템플릿 파일의 이름입니다. 예를 들어 **azuredeploy.json**이라는 템플릿을 배포하면 **azuredeploy**라는 기본 배포 이름을 만듭니다.

각 배포 이름의 경우 위치는 변경할 수 없습니다. 다른 위치의 이름이 동일한 기존 배포가 있는 경우 하나의 위치에서 배포를 만들 수 없습니다. 오류 코드 `InvalidDeploymentLocation`을 수신하게 되면 해당 이름의 이전 배포와 다른 이름이나 동일한 위치를 사용합니다.

## <a name="deployment-scopes"></a>배포 범위

관리 그룹에 배포 하는 경우 배포 명령 또는 테 넌 트의 다른 관리 그룹에 지정 된 관리 그룹을 대상으로 지정할 수 있습니다. 관리 그룹 내에서 구독 또는 리소스 그룹을 대상으로 지정할 수도 있습니다. 템플릿을 배포 하는 사용자에 게는 지정 된 범위에 대 한 액세스 권한이 있어야 합니다.

템플릿의 resources 섹션 내에 정의 된 리소스는 배포 명령에서 관리 그룹에 적용 됩니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        management-group-level-resources
    ],
    "outputs": {}
}
```

다른 관리 그룹을 대상으로 지정 하려면 중첩 된 배포를 추가 하 고 속성을 지정 `scope` 합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "mgName": {
            "type": "string"
        }
    },
    "variables": {
        "mgId": "[concat('Microsoft.Management/managementGroups/', parameters('mgName'))]"
    },
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2019-10-01",
            "name": "nestedDeployment",
            "scope": "[variables('mgId')]",
            "location": "eastus",
            "properties": {
                "mode": "Incremental",
                "template": {
                    nested-template-with-resources-in-different-mg
                }
            }
        }
    ],
    "outputs": {}
}
```

관리 그룹 내에서 구독을 대상으로 하려면 중첩 된 배포 및 속성을 사용 `subscriptionId` 합니다. 해당 구독 내의 리소스 그룹을 대상으로 하려면 중첩 된 다른 배포 및 속성을 추가 `resourceGroup` 합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-06-01",
      "name": "nestedSub",
      "location": "westus2",
      "subscriptionId": "00000000-0000-0000-0000-000000000000",
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "resources": [
            {
              "type": "Microsoft.Resources/deployments",
              "apiVersion": "2020-06-01",
              "name": "nestedRG",
              "resourceGroup": "rg2",
              "properties": {
                "mode": "Incremental",
                "template": {
                  nested-template-with-resources-in-resource-group
                }
              }
            }
          ]
        }
      }
    }
  ]
}
```

구독 내에서 리소스 그룹을 만들고 해당 리소스 그룹에 저장소 계정을 배포 하는 데 관리 그룹 배포를 사용 하려면 [구독 및 리소스 그룹에 배포](#deploy-to-subscription-and-resource-group)를 참조 하세요.

## <a name="use-template-functions"></a>템플릿 함수 사용

관리 그룹 배포의 경우 템플릿 함수를 사용할 때 고려해 야 할 몇 가지 중요 한 사항이 있습니다.

* [resourceGroup()](template-functions-resource.md#resourcegroup) 함수는 지원되지 **않습니다**.
* [subscription()](template-functions-resource.md#subscription) 함수는 지원되지 **않습니다**.
* [reference()](template-functions-resource.md#reference) 및 [list()](template-functions-resource.md#list) 함수는 지원됩니다.
* 관리 그룹에 배포 된 리소스에는 [resourceId ()](template-functions-resource.md#resourceid) 함수를 사용 하지 마세요.

  대신 관리 그룹의 확장으로 구현 된 리소스에 대해 [Extensionresourceid ()](template-functions-resource.md#extensionresourceid) 함수를 사용 합니다. 관리 그룹에 배포 되는 사용자 지정 정책 정의는 관리 그룹의 확장입니다.

  관리 그룹 수준에서 사용자 지정 정책 정의에 대 한 리소스 ID를 가져오려면 다음을 사용 합니다.
  
  ```json
  "policyDefinitionId": "[extensionResourceId(variables('mgScope'), 'Microsoft.Authorization/policyDefinitions', parameters('policyDefinitionID'))]"
  ```

  관리 그룹 내에서 사용할 수 있는 테 넌 트 리소스에 대해 [Tenantresourceid](template-functions-resource.md#tenantresourceid) 함수를 사용 합니다. 기본 제공 정책 정의는 테 넌 트 수준 리소스입니다.

  기본 제공 정책 정의에 대 한 리소스 ID를 가져오려면 다음을 사용 합니다.
  
  ```json
  "policyDefinitionId": "[tenantResourceId('Microsoft.Authorization/policyDefinitions', parameters('policyDefinitionID'))]"
  ```

## <a name="azure-policy"></a>Azure Policy

다음 예제에서는 관리 그룹 수준에서 정책을 [정의](../../governance/policy/concepts/definition-structure.md) 하 고 할당 하는 방법을 보여 줍니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "targetMG": {
            "type": "string",
            "metadata": {
                "description": "Target Management Group"
            }
        },
        "allowedLocations": {
            "type": "array",
            "defaultValue": [
                "australiaeast",
                "australiasoutheast",
                "australiacentral"
            ],
            "metadata": {
                "description": "An array of the allowed locations, all other locations will be denied by the created policy."
            }
        }
    },
    "variables": {
        "mgScope": "[tenantResourceId('Microsoft.Management/managementGroups', parameters('targetMG'))]",
        "policyDefinition": "LocationRestriction"
    },
    "resources": [
        {
            "type": "Microsoft.Authorization/policyDefinitions",
            "name": "[variables('policyDefinition')]",
            "apiVersion": "2019-09-01",
            "properties": {
                "policyType": "Custom",
                "mode": "All",
                "parameters": {
                },
                "policyRule": {
                    "if": {
                        "not": {
                            "field": "location",
                            "in": "[parameters('allowedLocations')]"
                        }
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
            "apiVersion": "2019-09-01",
            "dependsOn": [
                "[variables('policyDefinition')]"
            ],
            "properties": {
                "scope": "[variables('mgScope')]",
                "policyDefinitionId": "[extensionResourceId(variables('mgScope'), 'Microsoft.Authorization/policyDefinitions', variables('policyDefinition'))]"
            }
        }
    ]
}
```

## <a name="deploy-to-subscription-and-resource-group"></a>구독 및 리소스 그룹에 배포

관리 그룹 수준 배포에서 관리 그룹 내의 구독을 대상으로 지정할 수 있습니다. 다음 예제에서는 구독 내에 리소스 그룹을 만들고 해당 리소스 그룹에 저장소 계정을 배포 합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "nestedsubId": {
      "type": "string"
    },
    "nestedRG": {
      "type": "string"
    },
    "storageAccountName": {
      "type": "string"
    },
    "nestedLocation": {
      "type": "string"
    }
  },
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-06-01",
      "name": "nestedSub",
      "location": "[parameters('nestedLocation')]",
      "subscriptionId": "[parameters('nestedSubId')]",
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
          },
          "variables": {
          },
          "resources": [
            {
              "type": "Microsoft.Resources/resourceGroups",
              "apiVersion": "2020-06-01",
              "name": "[parameters('nestedRG')]",
              "location": "[parameters('nestedLocation')]",
            },
            {
              "type": "Microsoft.Resources/deployments",
              "apiVersion": "2020-06-01",
              "name": "nestedSubRG",
              "resourceGroup": "[parameters('nestedRG')]",
              "dependsOn": [
                "[parameters('nestedRG')]"
              ],
              "properties": {
                "mode": "Incremental",
                "template": {
                  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                  "contentVersion": "1.0.0.0",
                  "resources": [
                    {
                      "type": "Microsoft.Storage/storageAccounts",
                      "apiVersion": "2019-04-01",
                      "name": "[parameters('storageAccountName')]",
                      "location": "[parameters('nestedLocation')]",
                      "sku": {
                        "name": "Standard_LRS"
                      }
                    }
                  ]
                }
              }
            }
          ]
        }
      }
    }
  ]
}
```

## <a name="next-steps"></a>다음 단계

* 역할 할당에 대해 알아보려면 [Azure Resource Manager 템플릿을 사용 하 여 Azure 역할 할당 추가](../../role-based-access-control/role-assignments-template.md)를 참조 하세요.
* Azure Security Center에 대한 작업 영역 설정을 배포하는 예제는 [deployASCwithWorkspaceSettings.json](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json)을 참조하세요.
* [구독 수준](deploy-to-subscription.md) 및 [테 넌 트 수준](deploy-to-tenant.md)에서 템플릿을 배포할 수도 있습니다.
