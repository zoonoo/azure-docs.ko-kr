---
title: 규정 비준수 리소스 수정
description: 이 가이드에서는 Azure Policy 정책을 준수 하지 않는 리소스를 수정 하는 과정을 안내 합니다.
ms.date: 02/26/2020
ms.topic: how-to
ms.openlocfilehash: 5cf26f5235fbc35cdc9bfc8527967c3cc5ca91b8
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/27/2020
ms.locfileid: "77670800"
---
# <a name="remediate-non-compliant-resources-with-azure-policy"></a>Azure Policy를 사용하여 비준수 리소스 수정

**Deployifnotexists** 또는 **수정** 정책을 준수 하지 않는 리소스는 **재구성**을 통해 규격 상태로 전환할 수 있습니다. 관리 그룹, 구독, 리소스 그룹 또는 개별 리소스에 대 한 할당 인지 여부에 관계 없이 기존 리소스에 대해 **Deployifnotexists** 효과 나 할당 된 정책의 태그 **작업** 을 실행 하도록 Azure Policy를 지시 하 여 수정 작업을 수행 합니다. 이 문서에서는 Azure Policy를 사용 하 여 수정 사항을 이해 하 고 수행 하는 데 필요한 단계를 보여 줍니다.

## <a name="how-remediation-security-works"></a>수정 보안의 작동 방식

Azure Policy **Deployifnotexists** 정책 정의에서 템플릿을 실행 하는 경우 [관리 되는 id](../../../active-directory/managed-identities-azure-resources/overview.md)를 사용 합니다.
Azure Policy는 각 할당에 대 한 관리 id를 만들지만 관리 되는 id를 부여할 역할에 대 한 세부 정보가 있어야 합니다. 관리 ID에서 역할이 누락된 경우 정책 또는 이니셔티브 할당 중에 이 오류가 표시됩니다. 포털을 사용 하는 경우 할당이 시작 되 면 나열 된 역할에 대 한 관리 되는 id를 자동으로 부여 Azure Policy 합니다.

![관리 ID - 역할 누락](../media/remediate-resources/missing-role.png)

> [!IMPORTANT]
> **Deployifnotexists** 또는 **modify** 로 수정 된 리소스가 정책 할당 범위를 벗어난 경우 또는 템플릿에서 정책 할당 범위를 벗어난 리소스의 속성에 액세스 하는 경우 할당의 관리 되는 id에는 [수동으로 액세스 권한을 부여](#manually-configure-the-managed-identity) 해야 합니다. 그렇지 않으면 재구성 배포가 실패 합니다.

## <a name="configure-policy-definition"></a>정책 정의 구성

첫 번째 단계는 포함 된 템플릿의 콘텐츠를 성공적으로 배포 하기 위해 정책 정의에 **Deployifnotexists** 및 **modify** 필요한 역할을 정의 하는 것입니다. 이렇게 하려면 **details** 속성 아래에 **roleDefinitionIds** 속성을 추가합니다. 이 속성은 환경의 역할과 일치하는 문자열 배열입니다. 전체 예제는 [Deployifnotexists 예제](../concepts/effects.md#deployifnotexists-example) 또는 [수정 예제](../concepts/effects.md#modify-examples)를 참조 하세요.

```json
"details": {
    ...
    "roleDefinitionIds": [
        "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/{roleGUID}",
        "/providers/Microsoft.Authorization/roleDefinitions/{builtinroleGUID}"
    ]
}
```

**Roledefinitionids** 속성은 전체 리소스 식별자를 사용 하며 역할의 짧은 **roleName** 을 취하지 않습니다. 환경의 ‘Contributor’ 역할 ID를 가져오려면 다음 코드를 사용합니다.

```azurecli-interactive
az role definition list --name 'Contributor'
```

## <a name="manually-configure-the-managed-identity"></a>관리 ID 수동 구성

포털을 사용 하 여 할당을 만들 때 Azure Policy는 모두 관리 되는 id를 생성 하 고 **Roledefinitionids**에 정의 된 역할을 부여 합니다. 다음과 같은 상황에서는 관리 ID를 만들고 권한을 할당하는 단계를 수동으로 수행해야 합니다.

- Azure PowerShell 등의 SDK를 사용하는 경우
- 할당 범위 외부의 리소스를 템플릿이 수정하는 경우
- 할당 범위 외부의 리소스를 템플릿이 읽는 경우

> [!NOTE]
> 현재 이 기능을 지원하는 SDK는 Azure PowerShell 및 .NET뿐입니다.

### <a name="create-managed-identity-with-powershell"></a>PowerShell을 사용하여 관리 ID 만들기

정책 할당 중에 관리 ID를 만들려면 **Location**을 정의하고 **AssignIdentity**를 사용해야 합니다. 다음 예제에서는 기본 제공 정책 **SQL DB 투명 데이터 암호화 배포**의 정의를 가져오고 대상 리소스 그룹을 설정한 다음 할당을 만듭니다.

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get the built-in "Deploy SQL DB transparent data encryption" policy definition
$policyDef = Get-AzPolicyDefinition -Id '/providers/Microsoft.Authorization/policyDefinitions/86a912f6-9a06-4e26-b447-11b16ba8659f'

# Get the reference to the resource group
$resourceGroup = Get-AzResourceGroup -Name 'MyResourceGroup'

# Create the assignment using the -Location and -AssignIdentity properties
$assignment = New-AzPolicyAssignment -Name 'sqlDbTDE' -DisplayName 'Deploy SQL DB transparent data encryption' -Scope $resourceGroup.ResourceId -PolicyDefinition $policyDef -Location 'westus' -AssignIdentity
```

이제 `$assignment` 변수에는 관리 ID의 보안 주체 ID와 정책 할당 생성 시에 반환된 표준 값이 포함되어 있습니다. `$assignment.Identity.PrincipalId`를 통해 이 변수에 액세스할 수 있습니다.

### <a name="grant-defined-roles-with-powershell"></a>PowerShell을 사용하여 정의된 역할 부여

Azure Active Directory를 통해 새 관리 ID 복제를 완료해야 필요한 역할을 해당 ID에 부여할 수 있습니다. 다음 예제에서는 복제가 완료된 후 `$policyDef`roleDefinitionIds**에 대해** 에서 정책 정의를 반복하며, [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment)를 사용하여 새 관리 ID에 역할을 부여합니다.

```azurepowershell-interactive
# Use the $policyDef to get to the roleDefinitionIds array
$roleDefinitionIds = $policyDef.Properties.policyRule.then.details.roleDefinitionIds

if ($roleDefinitionIds.Count -gt 0)
{
    $roleDefinitionIds | ForEach-Object {
        $roleDefId = $_.Split("/") | Select-Object -Last 1
        New-AzRoleAssignment -Scope $resourceGroup.ResourceId -ObjectId $assignment.Identity.PrincipalId -RoleDefinitionId $roleDefId
    }
}
```

### <a name="grant-defined-roles-through-portal"></a>포털을 통해 정의된 역할 부여

포털을 사용하여 할당의 관리 ID에 정의된 역할을 부여하는 방법에는 두 가지가 있습니다. 그중 하나는 **액세스 제어(IAM)** 를 사용하는 것이고, 다른 하나는 정책 또는 이니셔티브 할당을 편집한 후에 **저장**을 클릭하는 것입니다.

할당의 관리 ID에 역할을 추가하려면 다음 단계를 수행합니다.

1. **모든 서비스**를 클릭한 후 **정책**을 검색하고 선택하여 Azure Portal에서 Azure Policy 서비스를 시작합니다.

1. Azure Policy 페이지의 왼쪽에서 **할당**을 선택합니다.

1. 관리 ID가 있는 할당을 찾아서 해당 이름을 클릭합니다.

1. 편집 페이지에서 **할당 ID** 속성을 찾습니다. 할당 ID는 다음과 같이 표시됩니다.

   ```output
   /subscriptions/{subscriptionId}/resourceGroups/PolicyTarget/providers/Microsoft.Authorization/policyAssignments/2802056bfc094dfb95d4d7a5
   ```

   관리 ID의 이름은 할당 리소스 ID의 끝부분(이 예제에서는 `2802056bfc094dfb95d4d7a5`)입니다. 할당 리소스 ID의 이 부분을 복사합니다.

1. 역할 정의를 수동으로 추가해야 하는 리소스 또는 리소스 상위 컨테이너(리소스 그룹, 구독, 관리 그룹)로 이동합니다.

1. 리소스 페이지에서 **액세스 제어(IAM)** 링크를 클릭한 다음 액세스 제어 페이지 위쪽의 **+ 역할 할당 추가**를 클릭합니다.

1. 정책 정의에서 **roleDefinitionIds**와 일치하는 적절한 역할을 선택합니다.
   **다음에 대한 액세스 할당:** 은 기본값인 ‘Azure AD 사용자, 그룹 또는 애플리케이션’으로 설정해 둡니다. **선택** 상자에 앞에서 찾은 할당 리소스 ID 부분을 붙여넣거나 입력합니다. 검색이 완료되면 이름이 같은 개체를 클릭하여 ID를 선택하고 **저장**을 클릭합니다.

## <a name="create-a-remediation-task"></a>수정 작업 만들기

### <a name="create-a-remediation-task-through-portal"></a>포털을 통해 수정 작업 만들기

평가 하는 동안 **Deployifnotexists** 또는 **modify** effects를 사용 하는 정책 할당은 비준수 리소스가 있는지 여부를 확인 합니다. 비준수 리소스가 있으면 **수정** 페이지에서 세부 정보가 제공됩니다. 비준수 리소스가 있는 정책 목록을 통해 **수정 작업**이 트리거됩니다. 이 옵션은 **Deployifnotexists** 템플릿 또는 **수정** 작업에서 배포를 만드는 것입니다.

**수정 작업**을 만들려면 다음 작업을 수행합니다.

1. **모든 서비스**를 클릭한 후 **정책**을 검색하고 선택하여 Azure Portal에서 Azure Policy 서비스를 시작합니다.

   ![모든 서비스에서 정책 검색](../media/remediate-resources/search-policy.png)

1. Azure Policy 페이지의 왼쪽에서 **수정**을 선택합니다.

   ![정책 페이지에서 업데이트 관리를 선택 합니다.](../media/remediate-resources/select-remediation.png)

1. 모든 **Deployifnotexists** 및 비규격 리소스를 사용 하는 정책 할당 **수정** 은 수정할 **정책** 탭 및 데이터 테이블에 포함 됩니다. 비준수 리소스가 있는 정책을 클릭합니다. **새 수정 작업** 페이지가 열립니다.

   > [!NOTE]
   > **규정 준수** 페이지에서 정책을 찾아서 클릭한 다음 **수정 작업 만들기** 단추를 클릭하여 **수정 작업** 페이지를 열 수도 있습니다.

1. **새 수정 작업** 페이지에서 **범위** 줄임표를 사용해 수정할 리소스를 필터링하여 정책이 할당된 하위 리소스를 선택합니다. 개별 리소스 개체까지 포함하여 선택해야 합니다. 또한 **위치** 드롭다운을 사용하여 리소스를 추가로 필터링합니다. 테이블에 나열된 리소스만 수정됩니다.

   ![재구성-수정할 리소스를 선택 합니다.](../media/remediate-resources/select-resources.png)

1. 리소스를 필터링한 후에 **수정**을 클릭하여 수정 작업을 시작합니다. 정책 준수 페이지가 작업 진행률의 상태를 표시 하는 **재구성 작업** 탭으로 열립니다. 업데이트 관리 작업으로 만든 배포가 바로 시작 됩니다.

   ![재구성-재구성 작업의 진행 상황](../media/remediate-resources/task-progress.png)

1. 정책 준수 페이지에서 **수정 작업**을 클릭하여 진행 상황 관련 세부 정보를 확인합니다. 작업에 사용된 필터링과 수정 중인 리소스 목록이 표시됩니다.

1. **수정 작업** 페이지에서 리소스를 마우스 오른쪽 단추로 클릭하여 수정 작업의 배포 또는 리소스를 확인합니다. 행 끝부분의 **관련 이벤트**를 클릭하여 오류 메시지와 같은 세부 정보를 확인합니다.

   ![수정 - 리소스 작업 상황에 맞는 메뉴](../media/remediate-resources/resource-task-context-menu.png)

**수정 작업**을 통해 배포한 리소스는 정책 준수 페이지의 **배포된 리소스** 탭에 추가됩니다.

### <a name="create-a-remediation-task-through-azure-cli"></a>Azure CLI를 통해 재구성 작업을 만듭니다.

Azure CLI를 사용 하 여 **수정 작업** 을 만들려면 `az policy remediation` 명령을 사용 합니다. `{subscriptionId}`를 사용자의 구독 ID로 바꾸고,을 **Deployifnotexists** 로 `{myAssignmentId}` 하거나 정책 할당 ID를 **수정** 합니다.

```azurecli-interactive
# Login first with az login if not using Cloud Shell

# Create a remediation for a specific assignment
az policy remediation create --name myRemediation --policy-assignment '/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyAssignments/{myAssignmentId}'
```

다른 수정 명령과 예제는 [az policy 재구성](/cli/azure/policy/remediation) 명령을 참조 하세요.

### <a name="create-a-remediation-task-through-azure-powershell"></a>Azure PowerShell를 통해 재구성 작업을 만듭니다.

Azure PowerShell를 사용 하 여 **수정 작업** 을 만들려면 `Start-AzPolicyRemediation` 명령을 사용 합니다. `{subscriptionId}`를 사용자의 구독 ID로 바꾸고,을 **Deployifnotexists** 로 `{myAssignmentId}` 하거나 정책 할당 ID를 **수정** 합니다.

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Create a remediation for a specific assignment
Start-AzPolicyRemediation -Name 'myRemedation' -PolicyAssignmentId '/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyAssignments/{myAssignmentId}'
```

다른 수정 cmdlet 및 예제는 [Az. PolicyInsights](/powershell/module/az.policyinsights/#policy_insights) 모듈을 참조 하세요.

## <a name="next-steps"></a>다음 단계

- [Azure Policy 샘플](../samples/index.md)에서 예제를 검토 합니다.
- [Azure Policy 정의 구조](../concepts/definition-structure.md)를 검토합니다.
- [정책 효과 이해](../concepts/effects.md)를 검토합니다.
- [프로그래밍 방식으로 정책을 만드는](programmatically-create.md)방법을 알아봅니다.
- [준수 데이터를 가져오는](get-compliance-data.md)방법에 대해 알아봅니다.
- [Azure 관리 그룹으로 리소스 구성](../../management-groups/overview.md)을 포함하는 관리 그룹을 검토합니다.
