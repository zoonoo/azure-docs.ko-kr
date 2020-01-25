---
title: Azure DevTest Labs에서 랩 사용자 추가 자동화 | Microsoft Docs
description: 이 문서에서는 Azure Resource Manager 템플릿, PowerShell 및 CLI를 사용 하 여 Azure DevTest Labs에서 랩에서 사용자를 자동으로 추가 하는 방법을 보여 줍니다.
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
ms.openlocfilehash: 70a6359923734c83590d4677bb2c93966c925d14
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/24/2020
ms.locfileid: "76718142"
---
# <a name="automate-adding-a-lab-user-to-a-lab-in-azure-devtest-labs"></a>Azure DevTest Labs에서 랩 사용자를 랩에 추가 자동화
Azure DevTest Labs를 사용 하면 Azure Portal를 사용 하 여 셀프 서비스 개발-테스트 환경을 빠르게 만들 수 있습니다. 그러나 여러 팀과 여러 DevTest Labs 인스턴스가 있는 경우 생성 프로세스를 자동화 하면 시간을 절약할 수 있습니다. [Azure Resource Manager 템플릿을](https://github.com/Azure/azure-devtestlab/tree/master/ARMTemplates) 사용 하면 실습, 랩 vm, 사용자 지정 이미지, 수식을 만들고 자동화 된 방식으로 사용자를 추가할 수 있습니다. 이 문서에서는 특히 DevTest Labs 인스턴스에 사용자를 추가 하는 방법을 집중적으로 설명 합니다.

랩에 사용자를 추가 하려면 랩에 대 한 **DevTest Labs 사용자** 역할에 사용자를 추가 합니다. 이 문서에서는 다음 방법 중 하나를 사용 하 여 랩에 사용자를 자동으로 추가 하는 방법을 보여 줍니다.

- Azure Resource Manager 템플릿
- Azure PowerShell cmdlet 
- Azure CLI.

## <a name="use-azure-resource-manager-templates"></a>Azure 리소스 관리자 템플릿 사용
다음 샘플 리소스 관리자 템플릿에서는 랩의 **DevTest Labs 사용자** 역할에 추가할 사용자를 지정 합니다. 

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

랩을 만드는 동일한 템플릿에서 역할을 할당 하는 경우 역할 할당 리소스와 랩 간의 종속성을 추가 해야 합니다. 자세한 내용은 [Azure Resource Manager 템플릿에서 종속성 정의](../azure-resource-manager/templates/define-resource-dependency.md) 문서를 참조 하세요.

### <a name="role-assignment-resource-information"></a>역할 할당 리소스 정보
역할 할당 리소스는 유형 및 이름을 지정 해야 합니다.

가장 먼저 해야 할 일은 리소스의 형식은 리소스 그룹에 대 한 것 처럼 `Microsoft.Authorization/roleAssignments` 되지 않는다는 것입니다.  대신 리소스 형식은 `{provider-namespace}/{resource-type}/providers/roleAssignments`패턴을 따릅니다. 이 경우 리소스 종류는 `Microsoft.DevTestLab/labs/providers/roleAssignments`됩니다.

역할 할당 이름은 전역적으로 고유 해야 합니다.  할당 이름은 `{labName}/Microsoft.Authorization/{newGuid}`패턴을 사용 합니다. `newGuid`은 템플릿에 대 한 매개 변수 값입니다. 역할 할당 이름이 고유한 지 확인 합니다. Guid를 만들기 위한 템플릿 함수가 없으므로 GUID 생성기 도구를 사용 하 여 직접 GUID를 생성 해야 합니다.  

템플릿에서 역할 할당의 이름은 `fullDevTestLabUserRoleName` 변수에 의해 정의 됩니다. 템플릿에서 정확한 줄은 다음과 같습니다.

```json
"fullDevTestLabUserRoleName": "[concat(parameters('labName'), '/Microsoft.Authorization/', parameters('roleAssignmentGuid'))]"
```


### <a name="role-assignment-resource-properties"></a>역할 할당 리소스 속성
역할 할당 자체는 세 가지 속성을 정의 합니다. `roleDefinitionId`, `principalId`및 `scope`필요 합니다.

### <a name="role-definition"></a>역할 정의
역할 정의 ID는 기존 역할 정의의 문자열 식별자입니다. 역할 ID는 `/subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}`형식입니다. 

구독 ID는 `subscription().subscriptionId` 템플릿 함수를 사용 하 여 가져옵니다.  

`DevTest Labs User` 기본 제공 역할에 대 한 역할 정의를 가져와야 합니다. [DevTest Labs 사용자](../role-based-access-control/built-in-roles.md#devtest-labs-user) 역할의 GUID를 가져오기 위해 [역할 할당 REST API](/rest/api/authorization/roleassignments) 또는 [AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition?view=azps-1.8.0) cmdlet을 사용할 수 있습니다.

```powershell
$dtlUserRoleDefId = (Get-AzRoleDefinition -Name "DevTest Labs User").Id
```

역할 ID는 variables 섹션 및 명명 된 `devTestLabUserRoleId`에서 정의 됩니다. 템플릿에서 역할 ID는 111111111-0000-0000-11111111111111111로 설정 됩니다. 

```json
"devTestLabUserRoleId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/111111111-0000-0000-11111111111111111')]",
```

### <a name="principal-id"></a>보안 주체 ID
보안 주체 ID는 랩 사용자로 랩에 추가 하려는 Active Directory 사용자, 그룹 또는 서비스 사용자의 개체 ID입니다. 템플릿은 `ObjectId`를 매개 변수로 사용 합니다.

[Get-azurermaduser](/powershell/module/azurerm.resources/get-azurermaduser?view=azurermps-6.13.0), [AzureRMADGroup 또는 [new-azurermadserviceprincipal](/powershell/module/azurerm.resources/get-azurermadserviceprincipal?view=azurermps-6.13.0) PowerShell cmdlet을 사용 하 여 ObjectId를 가져올 수 있습니다. 이러한 cmdlet은 필요한 개체 ID 인 ID 속성이 있는 Active Directory 개체의 단일 또는 목록을 반환 합니다. 다음 예에서는 회사에서 단일 사용자의 개체 ID를 가져오는 방법을 보여 줍니다.

```powershell
$userObjectId = (Get-AzureRmADUser -UserPrincipalName ‘email@company.com').Id
```

[Set-msoluser](/powershell/module/msonline/get-msoluser?view=azureadps-1.0), [get-msolgroup](/powershell/module/msonline/get-msolgroup?view=azureadps-1.0)및 [new-msolserviceprincipal](/powershell/module/msonline/get-msolserviceprincipal?view=azureadps-1.0)를 포함 하는 Azure Active Directory PowerShell cmdlet을 사용할 수도 있습니다.

### <a name="scope"></a>범위
범위 역할 할당을 적용 해야 하는 리소스 또는 리소스 그룹을 지정 합니다. 리소스의 경우 범위는 `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{provider-namespace}/{resource-type}/{resource-name}`형식입니다. 템플릿은 `subscription().subscriptionId` 함수를 사용 하 여 `subscription-id` 파트를 채우고 `resourceGroup().name` 템플릿 함수를 사용 하 여 `resource-group-name` 파트를 채웁니다. 이러한 함수를 사용 하면 역할을 할당 하는 랩이 현재 구독에 존재 하 고 템플릿 배포가 수행 되는 동일한 리소스 그룹에 있어야 합니다. 마지막 부분인 `resource-name`은 랩의 이름입니다. 이 값은이 예제에서 템플릿 매개 변수를 통해 수신 됩니다. 

템플릿의 역할 범위: 

```json
"roleScope": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.DevTestLab/labs/', parameters('labName'))]"
```

### <a name="deploying-the-template"></a>템플릿 배포
먼저 리소스 관리자 템플릿에서 매개 변수에 대 한 값을 전달 하는 매개 변수 파일 (예: azuredeploy. parameters)을 만듭니다. 

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

그런 다음 [New-azurermresourcegroupdeployment](/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment?view=azurermps-6.13.0) PowerShell cmdlet을 사용 하 여 리소스 관리자 템플릿을 배포 합니다. 다음 예제 명령은 랩에 대 한 개인, 그룹 또는 서비스 주체를 DevTest Labs 사용자 역할에 할당 합니다.

```powershell
New-AzureRmResourceGroupDeployment -Name "MyLabResourceGroup-$(New-Guid)" -ResourceGroupName 'MyLabResourceGroup' -TemplateParameterFile .\azuredeploy.parameters.json -TemplateFile .\azuredeploy.json
```

그룹 배포 이름 및 역할 할당 GUID는 고유 해야 합니다. 고유 하지 않은 GUID를 사용 하 여 리소스 할당을 배포 하려고 하면 `RoleAssignmentUpdateNotPermitted` 오류가 발생 합니다.

템플릿을 여러 번 사용 하 여 랩에 대 한 DevTest Labs 사용자 역할에 여러 Active Directory 개체를 추가 하려는 경우 PowerShell 명령에서 동적 개체를 사용 하는 것이 좋습니다. 다음 예제에서는 [새 guid](/powershell/module/Microsoft.PowerShell.Utility/New-Guid?view=powershell-5.0) cmdlet을 사용 하 여 리소스 그룹 배포 이름 및 역할 할당 Guid를 동적으로 지정 합니다.

```powershell
New-AzureRmResourceGroupDeployment -Name "MyLabResourceGroup-$(New-Guid)" -ResourceGroupName 'MyLabResourceGroup' -TemplateFile .\azuredeploy.json -roleAssignmentGuid "$(New-Guid)" -labName "MyLab" -principalId "11111111-1111-1111-1111-111111111111"
```

## <a name="use-azure-powershell"></a>Azure PowerShell 사용
소개에 설명 된 대로 새 Azure 역할 할당을 만들어 랩에 대 한 **DevTest Labs 사용자** 역할에 사용자를 추가 합니다. PowerShell에서는 [new-azurermroleassignment](/powershell/module/azurerm.resources/new-azurermroleassignment?view=azurermps-6.13.0) cmdlet을 사용 하 여이 작업을 수행 합니다. 이 cmdlet에는 유연성을 허용 하는 많은 선택적 매개 변수가 있습니다. `ObjectId`, `SigninName`또는 `ServicePrincipalName`는 사용 권한을 부여 하는 개체로 지정할 수 있습니다.  

다음은 지정 된 랩에서 DevTest Labs 사용자 역할에 사용자를 추가 하는 샘플 Azure PowerShell 명령입니다.

```powershell
New-AzureRmRoleAssignment -UserPrincipalName <email@company.com> -RoleDefinitionName 'DevTest Labs User' -ResourceName '<Lab Name>' -ResourceGroupName '<Resource Group Name>' -ResourceType 'Microsoft.DevTestLab/labs'
```

권한을 부여할 리소스를 지정 하려면 `ResourceName`, `ResourceType`, `ResourceGroup` 또는 `scope` 매개 변수를 조합 하 여 지정할 수 있습니다. 사용 되는 매개 변수 조합을 사용 하 여 cmdlet에 대 한 충분 한 정보를 제공 하 여 Active Directory 개체 (사용자, 그룹 또는 서비스 사용자), 범위 (리소스 그룹 또는 리소스), 역할 정의를 고유 하 게 식별 합니다.

## <a name="use-azure-command-line-interface-cli"></a>Azure CLI (명령줄 인터페이스) 사용
Azure CLI에서 랩 사용자를 랩에 추가 하는 작업은 `az role assignment create` 명령을 사용 하 여 수행 됩니다. Azure CLI cmdlet에 대 한 자세한 내용은 [RBAC 및 Azure CLI를 사용 하 여 Azure 리소스에 대 한 액세스 관리](../role-based-access-control/role-assignments-cli.md)를 참조 하세요.

액세스 권한을 부여 하는 개체는 `objectId`, `signInName``spn` 매개 변수로 지정할 수 있습니다. 개체에 대 한 액세스 권한을 부여 하는 랩을 `scope` url 또는 `resource-name`, `resource-type`및 `resource-group` 매개 변수의 조합으로 식별할 수 있습니다.

다음 Azure CLI 예제에서는 지정 된 랩에 대 한 사용자를 DevTest Labs 사용자 역할에 추가 하는 방법을 보여 줍니다.  

```azurecli
az role assignment create --roleName "DevTest Labs User" --signInName <email@company.com> -–resource-name "<Lab Name>" --resource-type “Microsoft.DevTestLab/labs" --resource-group "<Resource Group Name>"
```

## <a name="next-steps"></a>다음 단계
다음 문서를 참조하세요.

- [Azure CLI를 사용 하 여 DevTest Labs를 사용 하 여 가상 컴퓨터 만들기 및 관리](devtest-lab-vmcli.md)
- [Azure PowerShell를 사용 하 여 DevTest Labs를 사용 하 여 가상 머신 만들기](devtest-lab-vm-powershell.md)
- [명령줄 도구를 사용 하 여 가상 컴퓨터 Azure DevTest Labs 시작 및 중지](use-command-line-start-stop-virtual-machines.md)

