---
title: 샘플 - Linux VM 내에 애플리케이션이 설치되어 있지 않은지 감사
description: 샘플 정책 게스트 구성의 이니셔티브 및 정의는 지정된 애플리케이션이 Linux 가상 머신 내에 설치되어 있지 않은 경우 감사합니다.
services: azure-policy
author: DCtheGeek
manager: carmonm
ms.service: azure-policy
ms.topic: sample
ms.date: 03/18/2019
ms.author: dacoulte
ms.openlocfilehash: 8b383ed4a9e13380ec9d9ad2508c5b48b1e23e9e
ms.sourcegitcommit: 04716e13cc2ab69da57d61819da6cd5508f8c422
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/02/2019
ms.locfileid: "58849260"
---
# <a name="sample---audit-if-specified-applications-are-not-installed-inside-linux-vms"></a>샘플 - Linux VM 내에 지정된 애플리케이션이 설치되어 있지 않은 경우 감사

이 정책 게스트 구성 이니셔티브는 Linux 가상 머신 내에 지정된 애플리케이션이 설치되어 있는지를 감사합니다. 이 기본 제공 이니셔티브의 ID는 `/providers/Microsoft.Authorization/policySetDefinitions/c937dcb4-4398-4b39-8d63-4a6be432252e`입니다.

> [!IMPORTANT]
> 모든 게스트 구성 이니셔티브는 **audit** 및 **deployIfNotExists** 정책 정의로 구성됩니다. 정책 정의 중 하나만 할당하면 게스트 구성이 제대로 작동하지 않습니다.

이 샘플은 다음을 사용하여 할당할 수 있습니다.

- [Azure Portal](#azure-portal)
- [Azure PowerShell](#azure-powershell)

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="components-of-the-initiative"></a>이니셔티브의 구성 요소

이 [게스트 구성](../concepts/guest-configuration.md) 이니셔티브를 구성하는 정책은 다음과 같습니다.

- [audit](#audit-definition) - 애플리케이션이 Linux VM 내부에 설치되어 있는지 감사합니다.
  - ID: `/providers/Microsoft.Authorization/policyDefinitions/fee5cb2b-9d9b-410e-afe3-2902d90d0004`
- [deployIfNotExists](#deployIfNotExists-definition) - VM 확장을 배포하여 애플리케이션이 Linux VM 내에 설치되어 있는지 감사합니다.
  - ID: `/providers/Microsoft.Authorization/policyDefinitions/4d1c04de-2172-403f-901b-90608c35c721`

### <a name="initiative-definition"></a>이니셔티브 정의

이니셔티브는 **audit** 및 **deployIfNotExists** 정의와 [이니셔티브 매개 변수](#initiative-parameters)를 결합하여 만들어집니다. 정의의 JSON은 다음과 같습니다.

[!code-json[initiative-definition](../../../../policy-templates/samples/GuestConfiguration/installed-application-linux/azurepolicyset.json "Initiative definition (JSON)")]

### <a name="initiative-parameters"></a>이니셔티브 매개 변수

|이름 |형식 ||설명 | |---|---|| ---| | applicationName |문자열 |애플리케이션 이름입니다. 예를 들어 'python', 'powershell' 또는 쉼표로 구분 된 목록(예: 'python, powershell')입니다. 와일드카드 일치에 \*를 사용합니다(예: 'power\*').|

PowerShell 또는 Azure CLI를 통해 할당을 만드는 경우 `-PolicyParameter`(PowerShell) 또는 `--params`(Azure CLI) 명령을 사용하여 매개 변수 값을 문자열로 또는 파일을 통해 JSON으로 전달할 수 있습니다.
PowerShell은 cmdlet에 이름/값 해시 테이블을 전달해야 하는 `-PolicyParameterObject` 명령도 지원하는데, 여기서 **이름**은 매개 변수 이름이고 **값**은 할당 과정에 전달되는 단일 값 또는 값 배열입니다.

다음 매개 변수 예제에서는 _python_ 및 _powershell_ 애플리케이션의 설치를 감사합니다.

```json
{
    "applicationName": {
        "value": "python,powershell"
    }
}
```

**deployIfNotExists** 정책 정의만 이니셔티브 매개 변수를 사용합니다.

### <a name="audit-definition"></a>audit 정의

**audit** 정책 정의의 규칙을 정의하는 JSON입니다.

[!code-json[audit-definition](../../../../policy-templates/samples/GuestConfiguration/installed-application-linux/audit/azurepolicy.rules.json "audit policy rules (JSON)")]

### <a name="deployifnotexists-definition"></a>deployIfNotExists 정의

**deployIfNotExists** 정책 정의의 규칙을 정의하는 JSON입니다.

[!code-json[deployIfNotExists-definition](../../../../policy-templates/samples/GuestConfiguration/installed-application-linux/deployIfNotExists/azurepolicy.rules.json "deployIfNotExists policy rules (JSON)")]

**deployIfNotExists** 정책 정의는 정책의 유효성을 검사한 Azure 이미지를 정의합니다.

|게시자 |제안 |SKU |
|-|-|-|
|OpenLogic |CentOS\* |6을 제외하고 모두\* |
|RedHat |RHEL |6을 제외하고 모두\* |
|RedHat |osa | 모두 |
|credativ |Debian | 7을 제외하고 모두\* |
|Suse |SLES\* |11을 제외하고 모두\* |
|Canonical| UbuntuServer |12를 제외하고 모두\* |
|microsoft-dsvm |linux-data-science-vm-ubuntu |모두 |
|microsoft-dsvm |azureml |모두 |
|cloudera |cloudera-centos-os |6을 제외하고 모두\* |
|cloudera |cloudera-altus-centos-os |모두 |
|microsoft-ads |linux\* |모두 |
|microsoft-aks |모두 |모두 |
|AzureDatabricks |모두 |모두 |
|qubole-inc |모두 |모두 |
|datastax |모두 |모두 |
|couchbase |모두 |모두 |
|scalegrid |모두 |모두 |
|checkpoint |모두 |모두 |
|paloaltonetworks |모두 |모두 |

규칙의 **deployment** 부분은 _installedApplication_ 매개 변수를 가상 머신의 게스트 구성 에이전트에 전달합니다. 이 구성을 사용하면 에이전트에서 **audit** 정책 정의를 통해 유효성을 검사하고 규정 준수를 보고할 수 있습니다.

## <a name="azure-portal"></a>Azure portal

포털에 **audit** 및 **deployIfNotExists** 정의가 만들어지면 할당을 위해 [이니셔티브](../concepts/definition-structure.md#initiatives)로 그룹화하는 것이 좋습니다.

### <a name="create-copy-of-audit-definition"></a>audit 정의의 복사본 만들기

[![Azure에 Policy 샘플 배포](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/?#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FGuestConfiguration%2Finstalled-application-linux%2Faudit%2Fazurepolicy.json)
[![Azure Gov에 Policy 샘플 배포](https://docs.microsoft.com/azure/governance/policy/media/deploy/deployGovbutton.png)](https://portal.azure.us/?#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FGuestConfiguration%2Finstalled-application-linux%2Faudit%2Fazurepolicy.json)

이 단추를 사용하여 포털을 통해 배포하면 **audit** 정책 정의의 복사본이 만들어집니다.
쌍을 이루는 **deployIfNotExists** 정책 정의가 없으면 게스트 구성이 제대로 작동하지 않습니다.

### <a name="create-copy-of-deployifnotexists-definition"></a>deployIfNotExists 정의의 복사본 만들기

[![Azure에 Policy 샘플 배포](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/?#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FGuestConfiguration%2Finstalled-application-linux%2FdeployIfNotExists%2Fazurepolicy.json)
[![Azure Gov에 Policy 샘플 배포](https://docs.microsoft.com/azure/governance/policy/media/deploy/deployGovbutton.png)](https://portal.azure.us/?#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FGuestConfiguration%2Finstalled-application-linux%2FdeployIfNotExists%2Fazurepolicy.json)

이 단추를 사용하여 포털을 통해 배포하면 **deployIfNotExists** 정책 정의의 복사본이 만들어집니다. 쌍을 이루는 **audit** 정책 정의가 없으면 게스트 구성이 제대로 작동하지 않습니다.

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh-az.md)]

### <a name="deploy-with-azure-powershell"></a>Azure PowerShell을 사용하여 배포

#### <a name="copy-and-assign-the-initiative"></a>이니셔티브 복사 및 할당

다음 단계에서는 **audit** 및 **deployIfNotExists**에 대한 기본 제공 정책이 포함된 이니셔티브의 복사본을 만들고, 해당 이니셔티브를 리소스 그룹에 할당합니다.

```azurepowershell-interactive
# Create the policy initiative (Subscription scope)
$initDef = New-AzPolicySetDefinition -Name 'guestconfig-installed-application-linux' -DisplayName 'GuestConfig - Audit that an application is installed inside Linux VMs' -description 'This initiative will both deploy the policy requirements and audit that the specified application is installed inside Linux virtual machines.' -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/GuestConfiguration/installed-application-linux/azurepolicyset.definitions.json' -Parameter 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/GuestConfiguration/installed-application-linux/azurepolicyset.parameters.json' -Mode All

# Set the scope to a resource group; may also be a resource, subscription, or management group
$scope = Get-AzResourceGroup -Name 'YourResourceGroup'

# Set the initiative parameter (JSON format)
$initParam = '{ "applicationName": { "value": "python,powershell" } }'

# Create the initiative assignment
$assignment = New-AzPolicyAssignment -Name 'guestconfig-installed-application-linux-assignment' -DisplayName 'GuestConfig - Python and PowerShell apps on Linux' -Scope $scope.ResourceID -PolicySetDefinition $initDef -PolicyParameter $initParam -AssignIdentity -Location 'westus2'

# Get the system-assigned managed identity created by the assignment with -AssignIdentity
$saIdentity = $assignment.Identity.principalId

# Give the system-assigned managed identity the 'Contributor' role on the scope (needed by deployIfNotExists)
$roleAssignment = New-AzRoleAssignment -ObjectId $saIdentity -Scope $scope.ResourceId -RoleDefinitionName 'Contributor'
```

이전 할당 및 정의를 제거하려면 다음 명령을 실행합니다.

```azurepowershell-interactive
# Remove the initiative assignment
Remove-AzPolicyAssignment -Id $assignment.ResourceId

# Remove the 'Contributor' role from the system-assigned managed identity
Remove-AzRoleAssignment -ObjectId $saIdentity -Scope $scope.ResourceId -RoleDefinitionName 'Contributor'

# Remove the initiative definition
Remove-AzPolicySetDefinition -Id $initDef
```

#### <a name="copy-and-assign-the-audit-definition"></a>audit 정의 복사 및 할당

다음 단계에서는 **audit** 정의의 복사본을 만들고, 이를 리소스 그룹에 할당합니다. 쌍을 이루는 **deployIfNotExists** 정의도 할당되지 않으면 이 정의가 제대로 작동하지 않습니다.

```azurepowershell-interactive
# Create the policy definition (Subscription scope)
$definition = New-AzPolicyDefinition -Name 'guestconfig-installed-application-linux-audit' -DisplayName 'GuestConfig - Audit that an application is installed inside Linux VMs' -description 'This policy audits that the specified application is installed inside Linux virtual machines. This policy should only be used along with its corresponding deploy policy in an initiative/policy set.' -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/GuestConfiguration/installed-application-linux/audit/azurepolicy.rules.json' -Mode All

# Set the scope to a resource group; may also be a resource, subscription, or management group
$scope = Get-AzResourceGroup -Name 'YourResourceGroup'

# Create the policy assignment
$assignment = New-AzPolicyAssignment -Name 'guestconfig-installed-application-linux-audit-assignment' -DisplayName 'GuestConfig - Python and PowerShell apps on Linux' -Scope $scope.ResourceID -PolicyDefinition $definition
```

이전 할당 및 정의를 제거하려면 다음 명령을 실행합니다.

```azurepowershell-interactive
# Remove the policy definition
Remove-AzPolicyAssignment -Id $assignment.ResourceId

# Remove the policy definition
Remove-AzPolicyDefinition -Id $definition
```

#### <a name="copy-and-assign-the-deployifnotexists-definition"></a>deployIfNotExists 복사 및 할당

다음 단계에서는 **deployIfNotExists** 정의의 복사본을 만들고, 이를 리소스 그룹에 할당합니다.
쌍을 이루는 **audit** 정의도 할당되지 않으면 이 정의가 제대로 작동하지 않습니다.

```azurepowershell-interactive
# Create the policy definition (Subscription scope)
$definition = New-AzPolicyDefinition -Name 'guestconfig-installed-application-linux-deployIfNotExists' -DisplayName 'GuestConfig - Deploy VM extension to audit that an application is installed inside Linux VMs' -description 'Include this rule to deploy the VM extension for Microsoft Guest Configuration, the VM extension for Microsoft Azure Managed Service Identity, and the content required to audit that an application is installed inside Linux virtual machines. This policy should only be used along with its corresponding audit policy in an initiative/policy set.' -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/GuestConfiguration/installed-application-linux/deployIfNotExists/azurepolicy.rules.json' -Parameter 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/GuestConfiguration/installed-application-linux/deployIfNotExists/azurepolicy.parameters.json' -Mode All

# Set the scope to a resource group; may also be a resource, subscription, or management group
$scope = Get-AzResourceGroup -Name 'YourResourceGroup'

# Set the definition parameter (JSON format)
$policyParam  = '{ "applicationName": { "value": "python,powershell" } }'

# Create the policy assignment
$assignment = New-AzPolicyAssignment -Name 'guestconfig-installed-application-linux-deployIfNotExists-assignment' -DisplayName 'GuestConfig - Deploy VM extension to audit that Python and PowerShell are installed inside Linux VMs' -Scope $scope.ResourceID -PolicyDefinition $definition -PolicyParameter $policyParam -AssignIdentity -Location 'westus2'

# Get the system-assigned managed identity created by the assignment with -AssignIdentity
$saIdentity = $assignment.Identity.principalId

# Give the system-assigned managed identity the 'Contributor' role on the scope (needed by deployIfNotExists)
$roleAssignment = New-AzRoleAssignment -ObjectId $saIdentity -Scope $scope.ResourceId -RoleDefinitionName 'Contributor'
```

이전 할당 및 정의를 제거하려면 다음 명령을 실행합니다.

```azurepowershell-interactive
# Remove the policy assignment
Remove-AzPolicyAssignment -Id $assignment.ResourceId

# Remove the 'Contributor' role from the system-assigned managed identity
Remove-AzRoleAssignment -ObjectId $saIdentity -Scope $scope.ResourceId -RoleDefinitionName 'Contributor'

# Remove the policy definition
Remove-AzPolicyDefinition -Id $definition
```

### <a name="azure-powershell-explanation"></a>Azure PowerShell 설명

배포 및 제거 스크립트는 다음 명령을 사용합니다. 다음 표에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [New-AzPolicySetDefinition](/powershell/module/az.resources/New-AzPolicySetDefinition) | Azure Policy 이니셔티브를 만듭니다. |
| [New-AzPolicyDefinition](/powershell/module/az.resources/New-AzPolicyDefinition) | Azure Policy 정의를 만듭니다. |
| [Get-AzResourceGroup](/powershell/module/az.resources/Get-AzResourceGroup) | 단일 리소스 그룹을 가져옵니다. |
| [New-AzPolicyAssignment](/powershell/module/az.resources/New-AzPolicyAssignment) | 이니셔티브 또는 정의에 대한 새 Azure Policy 할당을 만듭니다. |
| [New-AzRoleAssignment](/powershell/module/az.resources/New-AzRoleAssignment) | 특정 주체에게 기존 역할 할당을 제공합니다. |
| [Remove-AzPolicyAssignment](/powershell/module/az.resources/Remove-AzPolicyAssignment) | 기존 Azure Policy 할당을 제거합니다. |
| [Remove-AzPolicySetDefinition](/powershell/module/az.resources/Remove-AzPolicySetDefinition) | 이니셔티브를 제거합니다. |
| [Remove-AzPolicyDefinition](/powershell/module/az.resources/Remove-AzPolicyDefinition) | 정의를 제거합니다. |

## <a name="next-steps"></a>다음 단계

- 추가 [Azure Policy 샘플](index.md)을 검토합니다.
- [Azure Policy 게스트 구성](../concepts/guest-configuration.md)에 대해 자세히 알아봅니다.
- [Azure Policy 정의 구조](../concepts/definition-structure.md)를 검토합니다.
