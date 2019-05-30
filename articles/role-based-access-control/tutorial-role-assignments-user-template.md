---
title: 자습서 - RBAC 및 Resource Manager 템플릿을 사용하여 Azure 리소스에 대한 사용자 액세스 권한 부여 | Microsoft Docs
description: Azure Resource Manager 템플릿을 사용하여 RBAC(역할 기반 액세스 제어)를 통해 사용자에게 Azure 리소스에 대한 액세스 권한을 부여하는 방법을 알아봅니다.
services: role-based-access-control,azure-resource-manager
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control,azure-resource-manager
ms.devlang: ''
ms.topic: tutorial
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 05/15/2019
ms.author: rolyon
ms.openlocfilehash: e99a9d2cfa38c9b2ea74f9075b18f81006b34881
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2019
ms.locfileid: "65802752"
---
# <a name="tutorial-grant-a-user-access-to-azure-resources-using-rbac-and-resource-manager-template"></a>자습서: RBAC 및 Resource Manager 템플릿을 사용하여 사용자에게 Azure 리소스에 대한 액세스 권한 부여

[RBAC(역할 기반 액세스 제어)](overview.md)는 Azure 리소스에 대한 액세스를 관리하는 방법입니다. 이 자습서에서는 리소스 그룹을 만들고 리소스 그룹에서 가상 머신을 만들고 관리할 수 있는 액세스 권한을 사용자에게 부여합니다. 이 자습서는 Resource Manager 템플릿을 배포하여 액세스 권한을 부여하는 프로세스를 중점적으로 다룹니다. Resource Manager 탬플릿 개발에 대한 자세한 내용은 [Resource Manager 설명서](/azure/azure-resource-manager/) 및 [템플릿 참조](/azure/templates/microsoft.authorization/allversions
)를 참조하세요.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 리소스 그룹 범위에 속한 사용자에게 액세스 권한 부여
> * 배포 유효성 검사
> * 정리

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>필수 조건

역할 할당을 추가하거나 제거하려면 다음이 필요합니다.

* `Microsoft.Authorization/roleAssignments/write` 및 `Microsoft.Authorization/roleAssignments/delete` 사용 권한(예: [사용자 액세스 관리자](built-in-roles.md#user-access-administrator) 또는 [소유자](built-in-roles.md#owner))

## <a name="grant-access"></a>액세스 권한 부여

이 빠른 시작에 사용되는 템플릿은 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/101-rbac-builtinrole-resourcegroup/)에서 나온 것입니다. [여기](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Authorization)에서 추가 Azure 권한 부여 관련 템플릿을 찾을 수 있습니다.

템플릿을 배포하려면 **사용해 보세요!** 를 선택하여 Azure Cloud Shell을 열고, 다음 PowerShell 스크립트를 셸 창에 붙여넣습니다. 코드를 붙여넣으려면 셸 창을 마우스 오른쪽 단추로 클릭하고 **붙여넣기**를 선택합니다.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used to generate Azure resource names"
$emailAddress = Read-Host -Prompt "Enter your email address that is associated with your Azure subscription (used to find the principal ID)"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

$resourceGroupName = "${projectName}rg"
$roleAssignmentName = New-Guid
$principalId = (Get-AzAdUser -Mail $emailAddress).id
$roleDefinitionId = (Get-AzRoleDefinition -name "Virtual Machine Contributor").id
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-rbac-builtinrole-resourcegroup/azuredeploy.json"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -roleAssignmentName $roleAssignmentName -roleDefinitionID $roleDefinitionId -principalId $principalId
```

## <a name="validate-the-deployment"></a>배포 유효성 검사

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 마지막 절차에서 만든 리소스 그룹을 엽니다. 기본 이름은 **rg**가 추가된 프로젝트 이름입니다.
1. 왼쪽 메뉴에서 **액세스 제어(IAM)** 를 선택합니다.
1. **역할 할당**을 선택합니다. 
1. **이름**에 마지막 절차에서 입력한 메일 주소를 입력합니다. 메일 주소가 있는 사용자에게는 **가상 머신 참가자** 역할이 있습니다.

## <a name="clean-up"></a>정리

마지막 절차에서 만든 리소스 그룹을 제거하려면 **사용해 보세요!** 를 선택하여 Azure Cloud 셸을 열고, 셸 창에 다음 PowerShell 스크립트를 붙여넣습니다.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a same project name you used in the last procedure"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -Name $resourceGroupName
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [자습서: RBAC 및 Azure PowerShell을 사용하여 Azure 리소스에 대한 사용자 액세스 권한 부여](tutorial-role-assignments-user-powershell.md)