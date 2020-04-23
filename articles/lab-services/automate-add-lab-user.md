---
title: Azure DevTest 랩에서 랩 사용자 추가 자동화 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 리소스 관리자 템플릿, PowerShell 및 CLI를 사용하여 Azure DevTest Labs의 랩에 사용자를 자동으로 추가하는 방법을 보여 주며, 이를 보여 주시고 있습니다.
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: 81a8c5030f716246caf3dcd8b540bb47fcaf6520
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2020
ms.locfileid: "82023625"
---
# <a name="automate-adding-a-lab-user-to-a-lab-in-azure-devtest-labs"></a>Azure DevTest 랩의 랩에 랩 사용자 추가 자동화
Azure DevTest Labs를 사용하면 Azure 포털을 사용하여 셀프 서비스 개발 테스트 환경을 빠르게 만들 수 있습니다. 그러나 여러 팀과 여러 DevTest Labs 인스턴스가 있는 경우 생성 프로세스를 자동화하면 시간을 절약할 수 있습니다. [Azure 리소스 관리자 템플릿을](https://github.com/Azure/azure-devtestlab/tree/master/Environments) 사용하면 랩, 랩 VM, 사용자 지정 이미지, 수식을 만들고 자동화된 방식으로 사용자를 추가할 수 있습니다. 이 문서에서는 특히 DevTest Labs 인스턴스에 사용자를 추가하는 데 중점을 둡니다.

사용자를 랩에 추가하려면 랩의 **DevTest Labs 사용자** 역할에 사용자를 추가합니다. 이 문서에서는 다음 방법 중 하나를 사용하여 사용자를 랩에 추가하도록 자동화하는 방법을 보여 주며 다음과 같은 방법을 보여 주며 있습니다.

- Azure 리소스 관리자 템플릿
- Azure PowerShell cmdlet 
- Azure CLI.

## <a name="use-azure-resource-manager-templates"></a>Azure 리소스 관리자 템플릿 사용
다음 샘플 리소스 관리자 템플릿은 사용자가 랩의 **DevTest Labs 사용자** 역할에 추가하도록 지정합니다. 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "principalId": {
      "type": "string",
      "metadata": {
        "description": "The objectId of the user, group, or service principal for the role."
      }
    },
    "labName": {
      "type": "string",
      "metadata": {
        "description": "The name of the lab instance to be created."
      }
    },
    "roleAssignmentGuid": {
      "type": "string",
      "metadata": {
        "description": "Guid to use as the name for the role assignment."
      }
    }
  },
  "variables": {
    "devTestLabUserRoleId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/111111111-0000-0000-11111111111111111')]",
    "fullDevTestLabUserRoleName": "[concat(parameters('labName'), '/Microsoft.Authorization/', parameters('roleAssignmentGuid'))]",
    "roleScope": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.DevTestLab/labs/', parameters('labName'))]"
  },
  "resources": [
    {
      "apiVersion": "2016-05-15",
      "type": "Microsoft.DevTestLab/labs",
      "name": "[parameters('labName')]",
      "location": "[resourceGroup().location]"
    },
    {
      "apiVersion": "2016-07-01",
      "type": "Microsoft.DevTestLab/labs/providers/roleAssignments",
      "name": "[variables('fullDevTestLabUserRoleName')]",
      "properties": {
        "roleDefinitionId": "[variables('devTestLabUserRoleId')]",
        "principalId": "[parameters('principalId')]",
        "scope": "[variables('roleScope')]"
      },
      "dependsOn": [
        "[resourceId('Microsoft.DevTestLab/labs', parameters('labName'))]"
      ]
    }
  ]
}

```

랩을 만드는 동일한 템플릿에 역할을 할당하는 경우 역할 할당 리소스와 랩 간에 종속성을 추가해야 합니다. 자세한 내용은 [Azure 리소스 관리자 템플릿의 종속성 정의](../azure-resource-manager/templates/define-resource-dependency.md) 문서를 참조하세요.

### <a name="role-assignment-resource-information"></a>역할 할당 리소스 정보
역할 할당 리소스는 형식과 이름을 지정해야 합니다.

가장 먼저 주의해야 할 점은 리소스의 형식이 리소스 그룹에 대한 `Microsoft.Authorization/roleAssignments` 형식과 다르다는 것입니다.  대신 리소스 형식은 패턴을 `{provider-namespace}/{resource-type}/providers/roleAssignments`따릅니다. 이 경우 리소스 유형은 `Microsoft.DevTestLab/labs/providers/roleAssignments`입니다.

역할 할당 이름 자체는 전역적으로 고유해야 합니다.  할당 의 이름은 패턴을 `{labName}/Microsoft.Authorization/{newGuid}`사용합니다. 는 `newGuid` 템플릿의 매개 변수 값입니다. 역할 할당 이름이 고유하도록 합니다. GUID를 만들기 위한 템플릿 기능이 없으므로 GUID 생성기 도구를 사용하여 GUID를 직접 생성해야 합니다.  

템플릿에서 역할 할당의 이름은 `fullDevTestLabUserRoleName` 변수에 의해 정의됩니다. 템플릿의 정확한 줄은 다음과 같습니다.

```json
"fullDevTestLabUserRoleName": "[concat(parameters('labName'), '/Microsoft.Authorization/', parameters('roleAssignmentGuid'))]"
```


### <a name="role-assignment-resource-properties"></a>역할 할당 리소스 속성
역할 할당 자체는 세 가지 속성을 정의합니다. 이 중 과 `scope` `roleDefinitionId` `principalId`

### <a name="role-definition"></a>역할 정의
역할 정의 ID는 기존 역할 정의에 대한 문자열 식별자입니다. 역할 ID는 형식입니다. `/subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}` 

구독 ID는 템플릿 함수를 사용하여 `subscription().subscriptionId` 가져옵니다.  

`DevTest Labs User` 기본 제공 역할에 대한 역할 정의를 받아야 합니다. [DevTest Labs 사용자](../role-based-access-control/built-in-roles.md#devtest-labs-user) 역할에 대한 GUID를 얻으려면 [역할 할당 REST API](/rest/api/authorization/roleassignments) 또는 [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition?view=azps-1.8.0) cmdlet을 사용할 수 있습니다.

```powershell
$dtlUserRoleDefId = (Get-AzRoleDefinition -Name "DevTest Labs User").Id
```

역할 ID는 변수 섹션에 정의되고 `devTestLabUserRoleId`명명됩니다. 템플릿에서 역할 ID는 111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111 

```json
"devTestLabUserRoleId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/111111111-0000-0000-11111111111111111')]",
```

### <a name="principal-id"></a>보안 주체 ID
보안 주체 ID는 랩에 랩 사용자로 추가하려는 Active Directory 사용자, 그룹 또는 서비스 주체의 개체 ID입니다. 템플릿은 매개 `ObjectId` 변수로 사용합니다.

[Get-AzureRMADUser](/powershell/module/azurerm.resources/get-azurermaduser?view=azurermps-6.13.0), [Get-AzureRMADGroup 또는 [Get-AzureRMADService PowerShell](/powershell/module/azurerm.resources/get-azurermadserviceprincipal?view=azurermps-6.13.0) cmdlet을 사용하여 ObjectId를 얻을 수 있습니다. 이러한 cmdlet은 필요한 개체 ID인 ID 속성이 있는 Active Directory 개체의 단일 또는 목록을 반환합니다. 다음 예제에서는 회사에서 단일 사용자의 개체 ID를 얻는 방법을 보여 주실 수 있습니다.

```powershell
$userObjectId = (Get-AzureRmADUser -UserPrincipalName ‘email@company.com').Id
```

[Get-MsolUser,](/powershell/module/msonline/get-msoluser?view=azureadps-1.0) [Get-MsolGroup](/powershell/module/msonline/get-msolgroup?view=azureadps-1.0)및 [Get-MsolServicePrincipal을](/powershell/module/msonline/get-msolserviceprincipal?view=azureadps-1.0)포함하는 Azure Active Directory PowerShell cmdlet을 사용할 수도 있습니다.

### <a name="scope"></a>범위
범위는 역할 할당이 적용되어야 하는 리소스 또는 리소스 그룹을 지정합니다. 리소스의 경우 범위는 다음과 같은 `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{provider-namespace}/{resource-type}/{resource-name}`형태로 되어 있습니다. `subscription().subscriptionId` 템플릿은 `subscription-id` 이 함수를 사용하여 부품을 `resourceGroup().name` 채우고 템플릿 함수를 `resource-group-name` 사용하여 파트를 채웁니다. 이러한 함수를 사용하면 역할을 할당하는 랩이 현재 구독및 템플릿 배포가 이루어지는 리소스 그룹에 있어야 합니다. 마지막 부분은 `resource-name`랩의 이름입니다. 이 값은 이 예제의 템플릿 매개 변수를 통해 수신됩니다. 

템플릿의 역할 범위는 다음과 같은 것입니다. 

```json
"roleScope": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.DevTestLab/labs/', parameters('labName'))]"
```

### <a name="deploying-the-template"></a>템플릿 배포
먼저 리소스 관리자 템플릿에서 매개 변수에 대한 값을 전달하는 매개 변수 파일(예: azuredeploy.parameters.json)을 만듭니다. 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "principalId": {
      "value": "11111111-1111-1111-1111-111111111111"
    },
    "labName": {
      "value": "MyLab"
    },
    "roleAssignmentGuid": {
      "value": "22222222-2222-2222-2222-222222222222"
    }
  }
}
```

그런 다음 [새 AzureRmResourceGroup 배포](/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment?view=azurermps-6.13.0) PowerShell cmdlet을 사용하여 리소스 관리자 템플릿을 배포합니다. 다음 예제 명령은 랩에 대한 DevTest Labs 사용자 역할에 사람, 그룹 또는 서비스 주체를 할당합니다.

```powershell
New-AzureRmResourceGroupDeployment -Name "MyLabResourceGroup-$(New-Guid)" -ResourceGroupName 'MyLabResourceGroup' -TemplateParameterFile .\azuredeploy.parameters.json -TemplateFile .\azuredeploy.json
```

그룹 배포 이름 및 역할 할당 GUID는 고유해야 합니다. 고유하지 않은 GUID를 사용하여 리소스 할당을 배포하려고 하면 `RoleAssignmentUpdateNotPermitted` 오류가 발생합니다.

랩의 DevTest Labs 사용자 역할에 여러 Active Directory 개체를 추가하려면 템플릿을 여러 번 사용하려는 경우 PowerShell 명령에서 동적 개체를 사용하는 것이 좋습니다. 다음 예제에서는 [New-Guid](/powershell/module/Microsoft.PowerShell.Utility/New-Guid?view=powershell-5.0) cmdlet을 사용하여 리소스 그룹 배포 이름 및 역할 할당 GUID를 동적으로 지정합니다.

```powershell
New-AzureRmResourceGroupDeployment -Name "MyLabResourceGroup-$(New-Guid)" -ResourceGroupName 'MyLabResourceGroup' -TemplateFile .\azuredeploy.json -roleAssignmentGuid "$(New-Guid)" -labName "MyLab" -principalId "11111111-1111-1111-1111-111111111111"
```

## <a name="use-azure-powershell"></a>Azure PowerShell 사용
소개에서 설명한 대로 새 Azure 역할 할당을 만들어 랩의 **DevTest Labs 사용자** 역할에 사용자를 추가합니다. PowerShell에서는 [새 AzureRMRole할당](/powershell/module/azurerm.resources/new-azurermroleassignment?view=azurermps-6.13.0) cmdlet을 사용하여 이렇게 합니다. 이 cmdlet에는 유연성을 허용하는 많은 선택적 매개 변수가 있습니다. `ObjectId`을 `SigninName`사용 하거나 `ServicePrincipalName` 사용 권한이 부여되는 개체로 지정할 수 있습니다.  

다음은 지정된 랩에서 DevTest Labs 사용자 역할에 사용자를 추가하는 Azure PowerShell 샘플 입니다.

```powershell
New-AzureRmRoleAssignment -UserPrincipalName <email@company.com> -RoleDefinitionName 'DevTest Labs User' -ResourceName '<Lab Name>' -ResourceGroupName '<Resource Group Name>' -ResourceType 'Microsoft.DevTestLab/labs'
```

권한이 부여되는 리소스를 지정하려면 `ResourceName`의 `ResourceType` `ResourceGroup` 조합또는 `scope` 매개 변수에 의해 지정할 수 있습니다. 매개 변수의 조합이 무엇이든 cmdlet에 충분한 정보를 제공하여 Active Directory 개체(사용자, 그룹 또는 서비스 주체), 범위(리소스 그룹 또는 리소스) 및 역할 정의를 고유하게 식별할 수 있습니다.

## <a name="use-azure-command-line-interface-cli"></a>AZURE 명령줄 인터페이스(CLI) 사용
Azure CLI에서 랩 사용자를 랩에 추가하는 작업은 `az role assignment create` 명령을 사용하여 수행됩니다. Azure CLI cmdlet에 대한 자세한 내용은 [RBAC 및 Azure CLI를 사용하여 Azure 리소스에 대한 액세스 관리를](../role-based-access-control/role-assignments-cli.md)참조하십시오.

액세스 권한이 부여되는 개체는 `objectId`" `signInName`매개 `spn` 변수에 의해 지정될 수 있습니다. 개체에 액세스 권한이 부여되는 `scope` 랩은 의 URL 또는 `resource-name`" `resource-type`및 `resource-group` 매개 변수의 조합으로 식별할 수 있습니다.

다음 Azure CLI 예제에서는 지정된 랩에 대한 개발자 테스트 랩 사용자 역할에 사용자를 추가하는 방법을 보여 주며 있습니다.  

```azurecli
az role assignment create --roleName "DevTest Labs User" --signInName <email@company.com> -–resource-name "<Lab Name>" --resource-type “Microsoft.DevTestLab/labs" --resource-group "<Resource Group Name>"
```

## <a name="next-steps"></a>다음 단계
다음 문서를 참조하세요.

- [Azure CLI를 사용하여 DevTest Labs에서 가상 머신 만들기 및 관리](devtest-lab-vmcli.md)
- [Azure PowerShell을 사용하여 DevTest 랩을 사용하여 가상 시스템 만들기](devtest-lab-vm-powershell.md)
- [명령줄 도구를 사용하여 Azure DevTest Labs 가상 컴퓨터를 시작하고 중지합니다.](use-command-line-start-stop-virtual-machines.md)

