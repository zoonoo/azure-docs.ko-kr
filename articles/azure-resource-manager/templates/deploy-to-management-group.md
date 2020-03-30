---
title: 관리 그룹에 리소스 배포
description: Azure 리소스 관리자 템플릿에서 관리 그룹 범위에서 리소스를 배포하는 방법을 설명합니다.
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: 863d1330412fa238b820eb0f1f05351fc723de6f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460316"
---
# <a name="create-resources-at-the-management-group-level"></a>관리 그룹 수준에서 리소스 만들기

조직이 성숙해짐에 따라 관리 그룹에 대한 [정책](../../governance/policy/overview.md) 또는 [역할 기반 액세스 제어를](../../role-based-access-control/overview.md) 정의하고 할당해야 할 수 있습니다. 관리 그룹 수준 템플릿을 사용하면 정책을 선언적으로 적용하고 관리 그룹 수준에서 역할을 할당할 수 있습니다.

## <a name="supported-resources"></a>지원되는 리소스

관리 그룹 수준에서 다음 리소스 유형을 배포할 수 있습니다.

* [배포](/azure/templates/microsoft.resources/deployments) - 구독 또는 리소스 그룹에 배포하는 중첩 된 템플릿의 경우
* [정책할당](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)
* [역할할당](/azure/templates/microsoft.authorization/roleassignments)
* [roleDefinitions](/azure/templates/microsoft.authorization/roledefinitions)

### <a name="schema"></a>스키마

관리 그룹 배포에 사용하는 스키마는 리소스 그룹 배포에 대한 스키마와 다릅니다.

템플릿의 경우 다음을 사용합니다.

```json
https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#
```

매개 변수 파일의 스키마는 모든 배포 범위에 대해 동일합니다. 매개 변수 파일의 경우 다음을 사용합니다.

```json
https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#
```

## <a name="deployment-commands"></a>배포 명령

관리 그룹 배포에 대한 명령은 리소스 그룹 배포에 대한 명령과 다릅니다.

Azure CLI의 경우 [az 배포 mg 만들기를](/cli/azure/deployment/mg?view=azure-cli-latest#az-deployment-mg-create)사용합니다.

```azurecli-interactive
az deployment mg create \
  --name demoMGDeployment \
  --location WestUS \
  --management-group-id myMG \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/management-level-deployment/azuredeploy.json"
```

Azure PowerShell의 경우 [New-AzManagementGroup 배포를](/powershell/module/az.resources/new-azmanagementgroupdeployment)사용합니다.

```azurepowershell-interactive
New-AzManagementGroupDeployment `
  -Name demoMGDeployment `
  -Location "West US" `
  -ManagementGroupId "myMG" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/management-level-deployment/azuredeploy.json"
```

REST API의 경우 [배포 - 관리 그룹 범위에서 만들기](/rest/api/resources/deployments/createorupdateatmanagementgroupscope)를 사용합니다.

## <a name="deployment-location-and-name"></a>배포 위치 및 이름

관리 그룹 수준 배포의 경우 배포에 대한 위치를 제공해야 합니다. 배포 위치는 배포하는 리소스의 위치와 별개입니다. 배포 위치는 배포 데이터를 저장할 위치를 지정합니다.

배포 이름을 제공하거나 기본 배포 이름을 사용할 수 있습니다. 기본 이름은 템플릿 파일의 이름입니다. 예를 들어 **azuredeploy.json**이라는 템플릿을 배포하면 **azuredeploy**라는 기본 배포 이름을 만듭니다.

각 배포 이름에 대해 위치는 변경할 수 없습니다. 다른 위치에 이름이 같은 기존 배포가 있는 경우 한 위치에서 배포를 만들 수 없습니다. 오류 코드 `InvalidDeploymentLocation`을 수신하게 되면 해당 이름의 이전 배포와 다른 이름이나 동일한 위치를 사용합니다.

## <a name="use-template-functions"></a>템플릿 함수 사용

관리 그룹 배포의 경우 템플릿 함수를 사용할 때 몇 가지 중요한 고려 사항이 있습니다.

* [resourceGroup()](template-functions-resource.md#resourcegroup) 함수는 지원되지 **않습니다**.
* [구독()](template-functions-resource.md#subscription) 기능은 **지원되지 않습니다.**
* [reference()](template-functions-resource.md#reference) 및 [list()](template-functions-resource.md#list) 함수는 지원됩니다.
* [resourceId()](template-functions-resource.md#resourceid) 함수는 지원됩니다. 관리 그룹 수준 배포에서 사용되는 리소스에 대한 리소스 ID를 얻는 데 사용합니다. 리소스 그룹 매개 변수에 대한 값을 제공하지 마십시오.

  예를 들어 정책 정의에 대한 리소스 ID를 얻으려면 다음을 사용합니다.
  
  ```json
  resourceId('Microsoft.Authorization/policyDefinitions/', parameters('policyDefinition'))
  ```
  
  반환된 리소스 ID에는 다음과 같은 형식이 있습니다.
  
  ```json
  /providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
  ```

## <a name="create-policies"></a>정책 만들기

### <a name="define-policy"></a>정책 정의

다음 예제에서는 관리 그룹 수준에서 정책을 [정의하는](../../governance/policy/concepts/definition-structure.md) 방법을 보여 주었습니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
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
    }
  ]
}
```

### <a name="assign-policy"></a>정책 할당

다음 예제는 관리 그룹에 기존 정책 정의를 할당합니다. 정책이 매개 변수를 사용하는 경우 개체로 제공합니다. 정책이 매개 변수를 사용하지 않으면 기본 빈 개체를 사용합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
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
        "policyDefinitionId": "[parameters('policyDefinitionID')]",
        "parameters": "[parameters('policyParameters')]"
      }
    }
  ]
}
```

## <a name="template-sample"></a>템플릿 샘플

* [리소스 그룹, 정책 및 정책 할당을 만듭니다.](https://github.com/Azure/azure-docs-json-samples/blob/master/management-level-deployment/azuredeploy.json)

## <a name="next-steps"></a>다음 단계

* 역할 할당에 대해 자세히 알아보려면 [RBAC 및 Azure 리소스 관리자 템플릿을 사용하여 Azure 리소스에 대한 액세스 관리를 참조하세요.](../../role-based-access-control/role-assignments-template.md)
* Azure Security Center에 대한 작업 영역 설정을 배포하는 예제는 [deployASCwithWorkspaceSettings.json](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json)을 참조하세요.
* [구독 수준](deploy-to-subscription.md) 및 [테넌트 수준에서](deploy-to-tenant.md)템플릿을 배포할 수도 있습니다.
