---
title: Azure DevTest Labs에서 랩 사용자 추가 자동화 | Microsoft Docs
description: Azure DevTest Labs에서 랩에 랩 사용자 추가 자동화 하는 방법을 알아봅니다.
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/02/2019
ms.author: spelluru
ms.openlocfilehash: 0eed874d405fcf99241a702292f8ceadae6c5a07
ms.sourcegitcommit: 1d257ad14ab837dd13145a6908bc0ed7af7f50a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/09/2019
ms.locfileid: "65502041"
---
# <a name="automate-adding-a-lab-user-to-a-lab-in-azure-devtest-labs"></a>Azure DevTest Labs에서 랩에 랩 사용자 추가 자동화
Azure DevTest Labs를 사용 하면 Azure portal을 사용 하 여 셀프 서비스 개발-테스트 환경을 빠르게 만들 수 있습니다. 그러나 여러 팀 및 여러 DevTest Labs 인스턴스를 설정한 경우 생성 프로세스를 자동화 시간을 절약할 수 있습니다. [Azure Resource Manager 템플릿은](https://github.com/Azure/azure-devtestlab/tree/master/ARMTemplates) labs에서 랩 Vm, 사용자 지정 이미지, 수식을 만들 수 있습니다 하 고 자동화 된 방식으로 사용자를 추가 합니다. 이 문서에서는 특히 DevTest Labs 인스턴스에 사용자를 추가 하는 방법에 중점을 둡니다.

랩에 사용자를 추가할 사용자를 추가 하는 **DevTest Labs 사용자** 랩에 대 한 역할입니다. 이 문서에서는 다음 방법 중 하나를 사용 하 여 랩에 사용자 추가 자동화 하는 방법을 보여 줍니다.

- Azure 리소스 관리자 템플릿
- Azure PowerShell cmdlet 
- Azure CLI.

## <a name="use-azure-resource-manager-templates"></a>Azure 리소스 관리자 템플릿 사용
다음 샘플 Resource Manager 템플릿을 추가할 사용자를 지정 합니다 **DevTest Labs 사용자** 랩의 역할입니다. 

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

랩을 만드는 동일한 템플릿에서 역할을 할당 하는 경우 역할 할당 리소스와 랩 간의 종속성을 추가 해야 합니다. 자세한 내용은 [Azure Resource Manager 템플릿에서 종속성 정의](/azure-resource-manager/resource-group-define-dependencies.md) 문서.

### <a name="role-assignment-resource-information"></a>역할 할당 리소스 정보
역할 할당 리소스를 이름과 유형을 지정 해야 합니다.

첫 번째 점은 리소스에 대 한 형식 없습니다 `Microsoft.Authorization/roleAssignments` 리소스 그룹에 대 한 것입니다.  리소스 형식 패턴을 따르는 대신 `{provider-namespace}/{resource-type}/providers/roleAssignments`합니다. 리소스 종류 됩니다 여기서 `Microsoft.DevTestLab/labs/providers/roleAssignments`합니다.

역할 할당 이름 자체는 전역적으로 고유 해야 합니다.  할당의 이름 패턴을 사용 하 여 `{labName}/Microsoft.Authorization/{newGuid}`입니다. `newGuid` 템플릿에 대 한 매개 변수 값입니다. 역할 할당 이름이 고유한 지 확인 합니다. Guid 만들기에 대 한 템플릿 함수가 없는으로 GUID 생성기 도구를 사용 하 여 직접 GUID를 생성 해야 합니다.  

역할 할당에 대 한 이름을 정의한 템플릿에서 `fullDevTestLabUserRoleName` 변수입니다. 서식 파일의 정확한 줄은 다음과 같습니다.

```json
"fullDevTestLabUserRoleName": "[concat(parameters('labName'), '/Microsoft.Authorization/', parameters('roleAssignmentGuid'))]"
```


### <a name="role-assignment-resource-properties"></a>역할 할당 리소스 속성
자체 역할 할당을 세 가지 속성을 정의합니다. 해야 합니다 `roleDefinitionId`, `principalId`, 및 `scope`합니다.

### <a name="role-definition"></a>역할 정의
역할 정의 ID는 기존 역할 정의 대 한 문자열 식별자입니다. ID 형식에서 역할 `/subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}`입니다. 

구독 ID를 사용 하 여 가져옵니다 `subscription().subscriptionId` 템플릿 함수입니다.  

에 대 한 역할 정의를 가져올 필요는 `DevTest Labs User` 기본 제공 역할입니다. 에 대 한 GUID를 가져올는 [DevTest Labs 사용자](../role-based-access-control/built-in-roles.md#devtest-labs-user) 역할을 사용할 수는 [역할 할당 REST API](/rest/api/authorization/roleassignments) 또는 [Get AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition?view=azps-1.8.0) cmdlet.

```powershell
$dtlUserRoleDefId = (Get-AzRoleDefinition -Name "DevTest Labs User").Id
```

역할 ID 변수 섹션에 정의 되어 있으며 라는 `devTestLabUserRoleId`합니다. 템플릿에서 역할 ID로 설정 됩니다. 111111111-0000-0000-11111111111111111. 

```json
"devTestLabUserRoleId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/111111111-0000-0000-11111111111111111')]",
```

### <a name="principal-id"></a>보안 주체 ID
보안 주체 ID에는 Active Directory 사용자, 그룹 또는 랩에 랩 사용자로 추가 하려는 서비스 주체 개체 ID입니다. 템플릿을 사용 하 여 `ObjectId` 매개 변수로 합니다.

사용 하 여 ObjectId를 가져올 수 있습니다 합니다 [Get-azurermaduser](/powershell/module/azurerm.resources/get-azurermaduser?view=azurermps-6.13.0), [Get-azurermadgroup, 또는 [Get-azurermadserviceprincipal](/powershell/module/azurerm.resources/get-azurermadserviceprincipal?view=azurermps-6.13.0) PowerShell cmdlet. 이러한 cmdlet에는 단일 또는 해야 하는 개체 ID를 ID 속성에 있는 Active Directory 개체의 목록을 반환 합니다. 다음 예제에서는 회사에서 단일 사용자의 개체 ID를 가져오는 방법을 보여 줍니다.

```powershell
$userObjectId = (Get-AzureRmADUser -UserPrincipalName ‘email@company.com').Id
```

포함 된 Azure Active Directory PowerShell cmdlet을 사용할 수도 있습니다 [Get-msoluser](/powershell/module/msonline/get-msoluser?view=azureadps-1.0)를 [Get-msolgroup](/powershell/module/msonline/get-msolgroup?view=azureadps-1.0), 및 [Get-msolserviceprincipal](/powershell/module/msonline/get-msolserviceprincipal?view=azureadps-1.0)합니다.

### <a name="scope"></a>Scope
범위는 리소스 또는 역할 할당은 적용 되는 리소스 그룹을 지정 합니다. 범위는 리소스에 대 한 형식에서: `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{provider-namespace}/{resource-type}/{resource-name}`합니다. 템플릿을 사용 하는 `subscription().subscriptionId` 함수를 작성 하는 `subscription-id` 파트 및 `resourceGroup().name` 템플릿 함수를 작성 하는 `resource-group-name` 부분. 이러한 함수를 사용 하 여 현재 구독 및 동일한 리소스 그룹 템플릿 배포 수행 되는 역할을 할당 하는 랩 존재 해야 합니다는 하는 것을 의미 합니다. 마지막 부분 `resource-name`, 랩의 이름입니다. 이 값은이 예제에서 템플릿 매개 변수를 통해 수신 됩니다. 

템플릿에서 역할 범위: 

```json
"roleScope": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.DevTestLab/labs/', parameters('labName'))]"
```

### <a name="deploying-the-template"></a>템플릿 배포
먼저 매개 변수 파일을 만듭니다 (예: azuredeploy.parameters.json) Resource Manager 템플릿에서 매개 변수 값을 전달 하는 합니다. 

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

그런 다음 사용 합니다 [New-azurermresourcegroupdeployment](/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment?view=azurermps-6.13.0) Resource Manager 템플릿을 배포 하려면 PowerShell cmdlet. 다음 예제 명령은 랩에 DevTest Labs 사용자 역할에 사용자, 그룹 또는 서비스 주체를 할당합니다.

```powershell
New-AzureRmResourceGroupDeployment -Name "MyLabResourceGroup-$(New-Guid)" -ResourceGroupName 'MyLabResourceGroup' -TemplateParameterFile .\azuredeploy.parameters.json -TemplateFile .\azuredeploy.json
```

그룹 배포 이름 및 역할 할당 GUID 고유 해야 하는 것이 반드시 합니다. 고유 하지 않은 GUID 사용 하 여 리소스 할당을 배포 하려는 경우 얻게 된 `RoleAssignmentUpdateNotPermitted` 오류입니다.

템플릿을 사용 하 여 여러 번 실험에 DevTest Labs 사용자 역할에 여러 Active Directory 개체를 추가 하려는 경우에 PowerShell 명령에 동적 개체를 사용 하는 것이 좋습니다. 다음 예제에서는 합니다 [New-guid](/powershell/module/Microsoft.PowerShell.Utility/New-Guid?view=powershell-5.0) cmdlet을 동적으로 리소스 그룹 배포 이름 및 역할 할당 GUID를 지정 합니다.

```powershell
New-AzureRmResourceGroupDeployment -Name "MyLabResourceGroup-$(New-Guid)" -ResourceGroupName 'MyLabResourceGroup' -TemplateFile .\azuredeploy.json -roleAssignmentGuid "$(New-Guid)" -labName "MyLab" -principalId "11111111-1111-1111-1111-111111111111"
```

## <a name="use-azure-powershell"></a>Azure PowerShell 사용
만든 사용자를 추가 하는 새 Azure 역할 할당 소개에서 설명 했 듯이 합니다 **DevTest Labs 사용자** 랩에 대 한 역할입니다. PowerShell에서를 사용 하 여 그렇게 합니다 [New-azurermroleassignment](/powershell/module/azurerm.resources/new-azurermroleassignment?view=azurermps-6.13.0) cmdlet. 이 cmdlet에 유연성을 허용 하려면 선택적 매개 변수가 많이 있습니다. 합니다 `ObjectId`, `SigninName`, 또는 `ServicePrincipalName` 권한이 부여 되는 개체로 지정할 수 있습니다.  

사용자 지정 랩에서 DevTest Labs 사용자 역할에 추가 하는 샘플 Azure PowerShell 명령은 다음과 같습니다.

```powershell
New-AzureRmRoleAssignment -UserPrincipalName <email@company.com> -RoleDefinitionName 'DevTest Labs User' -ResourceName '<Lab Name>' -ResourceGroupName '<Resource Group Name>' -ResourceType 'Microsoft.DevTestLab/labs'
```

지정 사용 권한 되는 리소스 부여 수 수를 조합 하 여 `ResourceName`, `ResourceType`를 `ResourceGroup` 또는 `scope` 매개 변수입니다. 모든 조합의 매개 변수는, Active Directory 개체 (사용자, 그룹 또는 서비스 주체), 범위 (리소스 그룹 또는 리소스) 및 역할 정의 고유 하 게 식별 하도록 cmdlet에 충분 한 정보를 제공 합니다.

## <a name="use-azure-command-line-interface-cli"></a>Azure 명령줄 인터페이스 (CLI)를 사용 합니다.
Azure cli에서를 사용 하 여 수행 됩니다 labs 사용자 랩에 추가 된 `az role assignment create` 명령입니다. Azure CLI cmdlet에 대 한 자세한 내용은 참조 하세요. [RBAC 및 Azure CLI를 사용 하 여 Azure 리소스에 대 한 액세스 관리](../role-based-access-control/role-assignments-cli.md)합니다.

여는 액세스 권한이 부여 되는 개체를 지정할 수 있습니다 합니다 `objectId`, `signInName`, `spn` 매개 변수입니다. 개체는 액세스를 부여할 랩으로 식별할 수 있습니다는 `scope` url 또는 조합 합니다 `resource-name`, `resource-type`, 및 `resource-group` 매개 변수입니다.

다음 Azure CLI 예제 DevTest Labs 사용자 역할이 사용자 지정된 랩에 대 한 추가 하는 방법을 보여 줍니다.  

```azurecli
az role assignment create --roleName "DevTest Labs User" --signInName <email@company.com> -–resource-name "<Lab Name>" --resource-type “Microsoft.DevTestLab/labs" --resource-group "<Resource Group Name>"
```

## <a name="next-steps"></a>다음 단계
다음 문서를 참조하세요.

- [Azure CLI를 사용 하 여 DevTest Labs를 사용 하 여 가상 컴퓨터를 만들고](devtest-lab-vmcli.md)
- [Azure PowerShell을 사용 하 여 DevTest Labs를 사용 하 여 가상 머신 만들기](devtest-lab-vm-powershell.md)
- [명령줄 도구를 사용 하 여 Azure DevTest Labs 가상 머신을 시작 및 중지 하려면](use-command-line-start-stop-virtual-machines.md)

