---
title: 테넌트에 리소스 배포
description: Azure 리소스 관리자 템플릿에서 테넌트 범위에서 리소스를 배포하는 방법을 설명합니다.
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: fcdfc5b1c4333a0d7eeec80a09ad85579a1f8b77
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460265"
---
# <a name="create-resources-at-the-tenant-level"></a>테넌트 수준에서 리소스 만들기

조직이 성숙함에 따라 Azure AD 테넌트 전체에서 [정책](../../governance/policy/overview.md) 또는 [역할 기반 액세스 제어를](../../role-based-access-control/overview.md) 정의하고 할당해야 할 수 있습니다. 테넌트 수준 템플릿을 사용하면 정책을 선언적으로 적용하고 전역 수준에서 역할을 할당할 수 있습니다.

## <a name="supported-resources"></a>지원되는 리소스

테넌트 수준에서 다음 리소스 유형을 배포할 수 있습니다.

* [배포](/azure/templates/microsoft.resources/deployments) - 관리 그룹 또는 구독에 배포하는 중첩 된 템플릿의 경우
* [정책할당](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)
* [역할할당](/azure/templates/microsoft.authorization/roleassignments)
* [roleDefinitions](/azure/templates/microsoft.authorization/roledefinitions)

### <a name="schema"></a>스키마

테넌트 배포에 사용하는 스키마는 리소스 그룹 배포에 대한 스키마와 다릅니다.

템플릿의 경우 다음을 사용합니다.

```json
https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#
```

매개 변수 파일의 스키마는 모든 배포 범위에 대해 동일합니다. 매개 변수 파일의 경우 다음을 사용합니다.

```json
https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#
```

## <a name="required-access"></a>필수 액세스

템플릿을 배포하는 주 서버는 테넌트 범위에서 리소스를 만들 수 있는 권한이 있어야 합니다. 보안 주체는 배포 작업()`Microsoft.Resources/deployments/*`을 실행하고 템플릿에 정의된 리소스를 만들 수 있는 권한이 있어야 합니다. 예를 들어 관리 그룹을 만들려면 보안 주체는 테넌트 범위에 기여자 권한이 있어야 합니다. 역할 할당을 만들려면 보안 주체에 소유자 권한이 있어야 합니다.

Azure Active Directory의 전역 관리자에는 역할을 할당할 수 있는 권한이 자동으로 없습니다. 테넌트 범위에서 템플릿 배포를 활성화하려면 전역 관리자는 다음 단계를 수행해야 합니다.

1. 전역 관리자가 역할을 할당할 수 있도록 계정 액세스 권한을 높입니다. 자세한 내용은 [모든 Azure 구독 및 관리 그룹을 관리하기 위한 액세스 상승 을](../../role-based-access-control/elevate-access-global-admin.md)참조하십시오.

1. 템플릿을 배포해야 하는 보안 주체에 소유자 또는 기여자를 할당합니다.

   ```azurepowershell-interactive
   New-AzRoleAssignment -SignInName "[userId]" -Scope "/" -RoleDefinitionName "Owner"
   ```

   ```azurecli-interactive
   az role assignment create --assignee "[userId]" --scope "/" --role "Owner"
   ```

이제 보안 주체에 템플릿을 배포하는 데 필요한 권한이 있습니다.

## <a name="deployment-commands"></a>배포 명령

테넌트 배포에 대한 명령은 리소스 그룹 배포에 대한 명령과 다릅니다.

Azure CLI의 경우 [az 배포 테넌트 만들기를](/cli/azure/deployment/tenant?view=azure-cli-latest#az-deployment-tenant-create)사용합니다.

```azurecli-interactive
az deployment tenant create \
  --name demoTenantDeployment \
  --location WestUS \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/tenant-level-deployments/new-mg/azuredeploy.json"
```

Azure PowerShell의 경우 [New-Az테넌트 배포를](/powershell/module/az.resources/new-aztenantdeployment)사용합니다.

```azurepowershell-interactive
New-AzTenantDeployment `
  -Name demoTenantDeployment `
  -Location "West US" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/tenant-level-deployments/new-mg/azuredeploy.json"
```

REST API의 경우 [배포 - 테넌트 범위에서 만들기 또는 업데이트합니다.](/rest/api/resources/deployments/createorupdateattenantscope)

## <a name="deployment-location-and-name"></a>배포 위치 및 이름

테넌트 수준 배포의 경우 배포에 대한 위치를 제공해야 합니다. 배포 위치는 배포하는 리소스의 위치와 별개입니다. 배포 위치는 배포 데이터를 저장할 위치를 지정합니다.

배포 이름을 제공하거나 기본 배포 이름을 사용할 수 있습니다. 기본 이름은 템플릿 파일의 이름입니다. 예를 들어 **azuredeploy.json**이라는 템플릿을 배포하면 **azuredeploy**라는 기본 배포 이름을 만듭니다.

각 배포 이름에 대해 위치는 변경할 수 없습니다. 다른 위치에 이름이 같은 기존 배포가 있는 경우 한 위치에서 배포를 만들 수 없습니다. 오류 코드 `InvalidDeploymentLocation`을 수신하게 되면 해당 이름의 이전 배포와 다른 이름이나 동일한 위치를 사용합니다.

## <a name="use-template-functions"></a>템플릿 함수 사용

테넌트 배포의 경우 템플릿 함수를 사용할 때 몇 가지 중요한 고려 사항이 있습니다.

* [resourceGroup()](template-functions-resource.md#resourcegroup) 함수는 지원되지 **않습니다**.
* [구독()](template-functions-resource.md#subscription) 기능은 **지원되지 않습니다.**
* [reference()](template-functions-resource.md#reference) 및 [list()](template-functions-resource.md#list) 함수는 지원됩니다.
* [테넌트ResourceId()](template-functions-resource.md#tenantresourceid) 함수를 사용하여 테넌트 수준에서 배포되는 리소스에 대한 리소스 ID를 가져옵니다.

  예를 들어 정책 정의에 대한 리소스 ID를 얻으려면 다음을 사용합니다.
  
  ```json
  tenantResourceId('Microsoft.Authorization/policyDefinitions/', parameters('policyDefinition'))
  ```
  
  반환된 리소스 ID에는 다음과 같은 형식이 있습니다.
  
  ```json
  /providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
  ```

## <a name="create-management-group"></a>관리 그룹 만들기

[다음 템플릿은](https://github.com/Azure/azure-quickstart-templates/tree/master/tenant-level-deployments/new-mg) 관리 그룹을 만듭니다.

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

[다음 템플릿은](https://github.com/Azure/azure-quickstart-templates/tree/master/tenant-level-deployments/tenant-role-assignment) 테넌트 범위에서 역할을 할당합니다.

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

* 역할 할당에 대해 자세히 알아보려면 [RBAC 및 Azure 리소스 관리자 템플릿을 사용하여 Azure 리소스에 대한 액세스 관리를 참조하세요.](../../role-based-access-control/role-assignments-template.md)
* [구독 수준](deploy-to-subscription.md) 또는 관리 그룹 [수준에서](deploy-to-management-group.md)템플릿을 배포할 수도 있습니다.
