<properties
	pageTitle="특정 랩 정책에 사용자 권한 부여| Microsoft Azure"
	description="각 사용자의 요구에 따라 DevTest Lab에서 특정 랩 정책에 사용자 권한을 부여하는 방법 알아보기"
	services="devtest-lab,virtual-machines,visual-studio-online"
	documentationCenter="na"
	authors="tomarcher"
	manager="douge"
	editor=""/>

<tags
	ms.service="devtest-lab"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/25/2016"
	ms.author="tarcher"/>

# 특정 랩 정책에 사용자 권한 부여

## 개요

이 문서에서는 PowerShell을 사용하여 특정 랩 정책에 사용자 권한을 부여하는 방법을 보여줍니다. 이런 방식으로 각 사용자의 요구 사항에 따라 권한을 적용할 수 있습니다. 예를 들어 특정 사용자에게 VM 정책 설정을 변경할 수 있는 기능은 부여하지만 비용 정책에 대해서는 부여하지 않을 수 있습니다.

## 리소스인 정책

[Azure 역할 기반 액세스 제어](../active-directory/role-based-access-control-configure.md) 문서에 설명된 대로 RBAC를 사용하면 Azure 리소스에 대해 세밀한 리소스 액세스 관리가 가능합니다. RBAC를 사용하여 DevOps 팀 내에서 업무를 분리하고 사용자에게 해당 작업을 수행하는 데 필요한 만큼의 권한만 부여할 수 있습니다.

DevTest Lab에서 정책은 RBAC 작업 **Microsoft.DevTestLab/labs/policySets/policies/**를 지원하는 리소스 종류입니다. 각 랩 정책은 정책 리소스 종류에 속한 리소스이며 RBAC 역할에 범위로 할당할 수 있습니다.

예를 들어 사용자에게 **허용된 VM 크기** 정책에 대한 읽기/쓰기 권한을 부여하려는 경우 **Microsoft.DevTestLab/labs/policySets/policies/*** 작업을 수행하는 사용자 지정 역할을 만든 다음 적절한 사용자를 **Microsoft.DevTestLab/labs/policySets/policies/AllowedVmSizesInLab** 범위에서 이 사용자 지정 역할에 할당합니다.

RBAC의 사용자 지정 역할에 대한 자세한 내용은 [Azure 역할 기반 액세스 제어](../active-directory/role-based-access-control-configure.md) 문서의 [Azure RBAC에서 사용자 지정 역할](../active-directory/role-based-access-control-configure.md#custom-roles-in-azure-rbac) 섹션을 참조하세요.

##PowerShell을 사용하여 랩 사용자 지정 역할 만들기
작업을 시작하려면 Azure PowerShell cmdlet을 설치하고 구성하는 방법을 설명하는 [https://azure.microsoft.com/blog/azps-1-0-pre](https://azure.microsoft.com/blog/azps-1-0-pre) 문서의 내용을 확인해야 합니다.

Azure PowerShell cmdlet을 설정한 후 다음 작업을 수행할 수 있습니다.

- 리소스 공급자에 대한 모든 작업 나열
- 특정 역할의 작업 나열
- 사용자 지정 역할 만들기

다음 PowerShell 스크립트는 이러한 작업을 수행하는 방법의 예제를 보여 줍니다.

    ‘List all the operations/actions for a resource provider.
    Get-AzureRmProviderOperation -OperationSearchString "Microsoft.DevTestLab/*"

    ‘List actions in a particular role.
    (Get-AzureRmRoleDefinition "DevTest Labs User").Actions

    ‘Create custom role.
    $policyRoleDef = (Get-AzureRmRoleDefinition "DevTest Labs User")
    $policyRoleDef.Id = $null
    $policyRoleDef.Name = "Policy Contributor"
    $policyRoleDef.IsCustom = $true
    $policyRoleDef.AssignableScopes.Clear()
    $policyRoleDef.AssignableScopes.Add("/subscriptions/<SubscriptionID> ")
    $policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/policySets/policies/*")
    $policyRoleDef = (New-AzureRmRoleDefinition -Role $policyRoleDef)

##사용자 지정 역할을 사용하여 특정 정책에 대해 사용자에게 권한 할당
사용자 지정 역할을 정의하고 나면 사용자에게 이 역할을 할당할 수 있습니다. 사용자 지정 역할을 사용자에게 할당하려면 먼저 해당 사용자를 나타내는 **ObjectId**를 가져와야 합니다. 이 작업을 수행하려면 **Get-AzureRmADUser** cmdlet을 사용합니다.

다음 예제에서 *SomeUser* 사용자의 **ObjectId**는 05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3입니다.

    PS C:\>Get-AzureRmADUser -SearchString "SomeUser"

    DisplayName                    Type                           ObjectId
    -----------                    ----                           --------
    someuser@hotmail.com                                          05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3

사용자 및 사용자 지정 역할 이름에 대한 **ObjectId**를 가져온 후에는 **New-AzureRmRoleAssignment** cmdlet을 사용하여 해당 역할을 사용자에게 할당할 수 있습니다.

    PS C:\>New-AzureRmRoleAssignment -ObjectId 05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3 -RoleDefinitionName "Policy Contributor" -Scope /subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.DevTestLab/labs/<LabName>/policySets/policies/AllowedVmSizesInLab

앞의 예제에서는 **AllowedVmSizesInLab** 정책이 사용되었습니다. 다음 정책 중 하나를 사용할 수 있습니다.

- MaxVmsAllowedPerUser
- MaxVmsAllowedPerLab
- AllowedVmSizesInLab
- LabVmsShutdown

[AZURE.INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## 다음 단계

특정 랩 정책에 사용자 권한을 부여한 경우 다음 몇 가지 단계를 고려해야 합니다.

- [랩에 안전하게 액세스](devtest-lab-add-devtest-user.md).

- [랩 정책 설정](devtest-lab-set-lab-policy.md).

- [랩 템플릿 만들기](devtest-lab-create-template.md).

- [VM에 대한 사용자 지정 아티팩트 만들기](devtest-lab-artifact-author.md).

- [아티팩트가 지정된 VM을 랩에 추가](devtest-lab-add-vm-with-artifacts.md).

<!---HONumber=AcomDC_0831_2016-->