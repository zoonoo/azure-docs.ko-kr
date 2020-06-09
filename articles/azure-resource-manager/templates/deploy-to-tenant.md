---
title: 테넌트에 리소스 배포
description: Azure Resource Manager 템플릿의 테넌트 범위에서 리소스를 배포하는 방법을 설명합니다.
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: d72b4a63e564732a9a4baaf8b8cd94d0f165e12a
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83653343"
---
# <a name="create-resources-at-the-tenant-level"></a>테넌트 수준에서 리소스 만들기

조직이 성숙되면 Azure AD 테넌트 전체에서 [정책](../../governance/policy/overview.md) 또는 [역할 기반 액세스 제어](../../role-based-access-control/overview.md)를 정의하거나 할당해야 할 수 있습니다. 테넌트 수준 템플릿을 사용하면 선언적으로 정책을 적용하고 전역 수준에서 역할을 할당할 수 있습니다.

## <a name="supported-resources"></a>지원되는 리소스

테넌트 수준에서 다음과 같은 리소스 종류를 배포할 수 있습니다.

* [배포](/azure/templates/microsoft.resources/deployments) - 관리 그룹 또는 구독에 배포하는 중첩된 템플릿의 경우
* [managementGroups](/azure/templates/microsoft.management/managementgroups)
* [policyAssignments](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)
* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)
* [roleDefinitions](/azure/templates/microsoft.authorization/roledefinitions)

### <a name="schema"></a>스키마

테넌트 배포에 사용하는 스키마는 리소스 그룹 배포에 대한 스키마와 다릅니다.

템플릿의 경우 다음을 사용합니다.

```json
https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#
```

매개 변수 파일에 대한 스키마는 모든 배포 범위에 대해 동일합니다. 매개 변수 파일의 경우 다음을 사용합니다.

```json
https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#
```

## <a name="required-access"></a>필요한 액세스

템플릿을 배포하는 주체는 테넌트 범위에서 리소스를 만들 수 있는 권한이 있어야 합니다. 주체는 배포 작업(`Microsoft.Resources/deployments/*`)을 실행하고 템플릿에 정의된 리소스를 만들 수 있는 권한이 있어야 합니다. 예를 들어 관리 그룹을 만들려면 주체는 테넌트 범위에서 기여자 권한이 있어야 합니다. 역할 할당을 만들려면 주체에 소유자 권한이 있어야 합니다.

Azure Active Directory의 전역 관리자에게 역할을 할당할 수 있는 권한이 자동으로 생기지는 않습니다. 테넌트 범위에서 템플릿 배포를 사용하도록 설정하려면 전역 관리자가 다음 단계를 수행해야 합니다.

1. 계정 액세스 권한을 승격하여 전역 관리자가 역할을 할당할 수 있도록 합니다. 자세한 내용은 [모든 Azure 구독 및 관리 그룹을 관리할 수 있도록 액세스 권한 상승](../../role-based-access-control/elevate-access-global-admin.md)을 참조하세요.

1. 템플릿을 배포해야 하는 주체에 소유자 또는 기여자를 할당합니다.

   ```azurepowershell-interactive
   New-AzRoleAssignment -SignInName "[userId]" -Scope "/" -RoleDefinitionName "Owner"
   ```

   ```azurecli-interactive
   az role assignment create --assignee "[userId]" --scope "/" --role "Owner"
   ```

이제 주체는 템플릿을 배포하는 데 필요한 권한이 있습니다.

## <a name="deployment-commands"></a>배포 명령

테넌트 배포에 대한 명령은 리소스 그룹 배포에 대한 명령과 다릅니다.

Azure CLI의 경우 [az deployment tenant create](/cli/azure/deployment/tenant?view=azure-cli-latest#az-deployment-tenant-create)를 사용합니다.

```azurecli-interactive
az deployment tenant create \
  --name demoTenantDeployment \
  --location WestUS \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/tenant-level-deployments/new-mg/azuredeploy.json"
```

Azure PowerShell의 경우 [New-AzTenantDeployment](/powershell/module/az.resources/new-aztenantdeployment)를 사용합니다.

```azurepowershell-interactive
New-AzTenantDeployment `
  -Name demoTenantDeployment `
  -Location "West US" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/tenant-level-deployments/new-mg/azuredeploy.json"
```

REST API의 경우 [배포 - 테넌트 범위에서 만들기 또는 업데이트](/rest/api/resources/deployments/createorupdateattenantscope)를 사용합니다.

## <a name="deployment-location-and-name"></a>배포 위치 및 이름

테넌트 수준 배포의 경우 배포할 위치를 제공해야 합니다. 배포 위치는 배포하는 리소스의 위치와는 별개입니다. 배포 위치는 배포 데이터를 저장할 위치를 지정합니다.

배포 이름을 제공하거나 기본 배포 이름을 사용할 수 있습니다. 기본 이름은 템플릿 파일의 이름입니다. 예를 들어 **azuredeploy.json**이라는 템플릿을 배포하면 **azuredeploy**라는 기본 배포 이름을 만듭니다.

각 배포 이름의 경우 위치는 변경할 수 없습니다. 다른 위치의 이름이 동일한 기존 배포가 있는 경우 하나의 위치에서 배포를 만들 수 없습니다. 오류 코드 `InvalidDeploymentLocation`을 수신하게 되면 해당 이름의 이전 배포와 다른 이름이나 동일한 위치를 사용합니다.

## <a name="use-template-functions"></a>템플릿 함수 사용

테넌트 배포의 경우 템플릿 함수를 사용할 때 몇 가지 중요한 고려 사항이 있습니다.

* [resourceGroup()](template-functions-resource.md#resourcegroup) 함수는 지원되지 **않습니다**.
* [subscription()](template-functions-resource.md#subscription) 함수는 지원되지 **않습니다**.
* [reference()](template-functions-resource.md#reference) 및 [list()](template-functions-resource.md#list) 함수는 지원됩니다.
* [tenantResourceId()](template-functions-resource.md#tenantresourceid) 함수를 사용하여 테넌트 수준에서 배포된 리소스에 대한 리소스 ID를 가져옵니다.

  예를 들어 정책 정의에 대한 리소스 ID를 가져오려면 다음을 사용합니다.

  ```json
  tenantResourceId('Microsoft.Authorization/policyDefinitions/', parameters('policyDefinition'))
  ```

  반환된 리소스 ID 형식은 다음과 같습니다.

  ```json
  /providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
  ```

## <a name="create-management-group"></a>관리 그룹 만들기

[다음 템플릿](https://github.com/Azure/azure-quickstart-templates/tree/master/tenant-level-deployments/new-mg)은 관리 그룹을 만듭니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "mgName": {
      "type": "string",
      "defaultValue": "[concat('mg-', uniqueString(newGuid()))]"
    }
  },
  "resources": [
    {
      "type": "Microsoft.Management/managementGroups",
      "apiVersion": "2019-11-01",
      "name": "[parameters('mgName')]",
      "properties": {
      }
    }
  ]
}
```

## <a name="assign-role"></a>역할 할당

[다음 템플릿](https://github.com/Azure/azure-quickstart-templates/tree/master/tenant-level-deployments/tenant-role-assignment)은 테넌트 범위에서 역할을 할당합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "principalId": {
      "type": "string",
      "metadata": {
        "description": "principalId if the user that will be given contributor access to the resourceGroup"
      }
    },
    "roleDefinitionId": {
      "type": "string",
      "defaultValue": "8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
      "metadata": {
        "description": "roleDefinition for the assignment - default is owner"
      }
    }
  },
  "variables": {
    // This creates an idempotent guid for the role assignment
    "roleAssignmentName": "[guid('/', parameters('principalId'), parameters('roleDefinitionId'))]"
  },
  "resources": [
    {
      "name": "[variables('roleAssignmentName')]",
      "type": "Microsoft.Authorization/roleAssignments",
      "apiVersion": "2019-04-01-preview",
      "properties": {
        "roleDefinitionId": "[tenantResourceId('Microsoft.Authorization/roleDefinitions', parameters('roleDefinitionId'))]",
        "principalId": "[parameters('principalId')]",
        "scope": "/"
      }
    }
  ]
}
```

## <a name="next-steps"></a>다음 단계

* 역할 할당에 관해 알아보려면 [RBAC 및 Azure Resource Manager 템플릿을 사용하여 Azure 리소스에 대한 액세스 관리](../../role-based-access-control/role-assignments-template.md)를 참조하세요.
* [구독 수준](deploy-to-subscription.md) 또는 [관리 그룹 수준](deploy-to-management-group.md)에서 템플릿을 배포할 수도 있습니다.
