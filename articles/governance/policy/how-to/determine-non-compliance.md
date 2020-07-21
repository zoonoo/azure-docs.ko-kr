---
title: 규정 비준수의 원인 확인
description: 리소스가 규정 비준수인 경우 여러 가지 원인이 있을 수 있습니다. 규정 비준수를 일으킨 원인을 확인하는 방법을 알아보세요.
ms.date: 07/06/2020
ms.topic: how-to
ms.openlocfilehash: ee027ff0f2936dc3eb7153869c52d4e70c83918b
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86512030"
---
# <a name="determine-causes-of-non-compliance"></a>규정 비준수의 원인 확인

Azure 리소스가 정책 규칙을 준수하지 않는 것으로 확인되면 리소스가 호환되지 않는 규칙의 부분을 이해하는 것이 도움이 됩니다. 또한 이전 준수 리소스를 규정 비준수로 만든 변경 내용을 이해하는 것도 유용합니다. 이 정보를 찾는 방법에는 두 가지가 있습니다.

- [준수 세부 정보](#compliance-details)
- [변경 기록(미리 보기)](#change-history)

## <a name="compliance-details"></a>준수 세부 정보

리소스가 규정 비준수인 경우 해당 리소스에 대한 준수 세부 정보는 **정책 준수** 페이지에서 사용할 수 있습니다. 준수 세부 정보 창에는 다음 정보가 포함됩니다.

- 이름, 유형, 위치 및 리소스 ID와 같은 리소스 세부 정보
- 현재 정책 할당에 대한 마지막 평가의 준수 상태 및 타임스탬프
- 리소스 규정 비준수에 대한 _이유_ 목록

> [!IMPORTANT]
> _규정 비준수_ 리소스에 대한 준수 세부 정보에는 해당 리소스에 대한 현재 속성 값이 표시되므로 사용자에게 리소스 **형식**에 대한 **읽기** 작업이 있어야 합니다. 예를 들어 _규정 비준수_ 리소스가 **Microsoft.Compute/virtualMachines**인 경우 사용자에게 **Microsoft.Compute/virtualMachines/read** 작업이 있어야 합니다. 사용자에게 필요한 작업이 없는 경우 액세스 오류가 표시됩니다.

준수 세부 정보를 보려면 다음 단계를 수행합니다.

1. **모든 서비스**를 클릭한 후 **정책**을 검색하고 선택하여 Azure Portal에서 Azure Policy 서비스를 시작합니다.

1. **개요** 또는 **준수** 페이지에서 _규정 비준수_인 **준수 상태**에서 정책을 선택합니다.

1. **정책 준수** 페이지의 **리소스 준수** 탭에서 _규정 비준수_인 **준수 상태**에서 리소스의 줄임표를 마우스 오른쪽 단추로 클릭하거나 선택합니다. 그런 다음, **준수 세부 정보 보기**를 선택합니다.

   :::image type="content" source="../media/determine-non-compliance/view-compliance-details.png" alt-text="준수 세부 정보 보기 옵션" border="false":::

1. **준수 세부 정보** 창에는 현재 정책 할당에 대한 리소스의 최신 평가 정보가 표시됩니다. 이 예제에서 **Microsoft.Sql/servers/version** 필드는 _12.0_으로 확인되며 정책 정의는 _14.0_으로 예상됩니다. 여러 가지 이유로 리소스가 규정 비준수인 경우 각각이 이 창에 나열됩니다.

   :::image type="content" source="../media/determine-non-compliance/compliance-details-pane.png" alt-text="준수 세부 정보 창 및 규정 비준수에 대한 이유" border="false":::

   **auditIfNotExists** 또는 **deployIfNotExists** 정책 정의의 경우 세부 정보는 **details.type** 속성 및 선택적 속성을 포함합니다. 목록은 [auditIfNotExists 속성](../concepts/effects.md#auditifnotexists-properties) 및 [deployIfNotExists 속성](../concepts/effects.md#deployifnotexists-properties)을 참조하세요. **마지막으로 평가된 리소스**는 정의의 **세부 정보** 섹션에서 관련된 리소스입니다.

   예제 부분 **deployIfNotExists** 정의:

   ```json
   {
       "if": {
           "field": "type",
           "equals": "[parameters('resourceType')]"
       },
       "then": {
           "effect": "DeployIfNotExists",
           "details": {
               "type": "Microsoft.Insights/metricAlerts",
               "existenceCondition": {
                   "field": "name",
                   "equals": "[concat(parameters('alertNamePrefix'), '-', resourcegroup().name, '-', field('name'))]"
               },
               "existenceScope": "subscription",
               "deployment": {
                   ...
               }
           }
       }
   }
   ```

   :::image type="content" source="../media/determine-non-compliance/compliance-details-pane-existence.png" alt-text="준수 세부 정보 창 - *ifNotExists" border="false":::

> [!NOTE]
> 데이터를 보호하기 위해 속성 값이 _비밀_이면 현재 값이 별표를 표시합니다.

이러한 세부 정보는 리소스가 현재 규정 비준수인 이유를 설명하지만 규정 비준수가 되도록 만든 리소스가 변경된 경우를 표시하지 않습니다. 해당 정보는 아래 [변경 기록(미리 보기)](#change-history)을 참조하세요.

### <a name="compliance-reasons"></a>준수 이유

다음 행렬에서는 가능한 각 _이유_를 정책 정의의 책임 있는 [조건](../concepts/definition-structure.md#conditions)에 매핑합니다.

|이유 | 조건 |
|-|-|
|현재 값은 대상 값을 키로 포함해야 합니다. |containsKey 또는 notContainsKey **아님** |
|현재 값은 대상 값을 포함해야 합니다. |contains 또는 notContains **아님** |
|현재 값은 대상 값과 같아야 합니다. |equals 또는 notEquals **아님** |
|현재 값은 대상 값보다 작아야 합니다. |less 또는 greaterOrEquals **아님** |
|현재 값은 대상 값보다 크거나 같아야 합니다. |greaterOrEquals 또는 less **아님** |
|현재 값은 대상 값보다 커야 합니다. |greater 또는 lessOrEquals **아님** |
|현재 값은 대상 값보다 작거나 같아야 합니다. |lessOrEquals 또는 greater **아님** |
|현재 값이 있어야 합니다. |exists |
|현재 값이 대상 값에 포함되어야 합니다. |in 또는 notIn **아님** |
|현재 값은 대상 값과 같아야 합니다. |like 또는 notLike **아님** |
|현재 값은 대/소문자를 구분하며 대상 값과 일치해야 합니다. |match 또는 notMatch **아님** |
|현재 값은 대/소문자를 구분하지 않으며 대상 값과 일치해야 합니다. |matchInsensitively 또는 notMatchInsensitively **아님** |
|현재 값은 대상 값을 키로 포함할 수 없습니다. |notContainsKey 또는 containsKey **아님**|
|현재 값은 대상 값을 포함할 수 없습니다. |notContains 또는 contains **아님** |
|현재 값은 대상 값과 같을 수 없습니다. |notEquals 또는 equals **아님** |
|현재 값이 없어야 합니다. |exists **아님**  |
|현재 값이 대상 값에 포함될 수 없습니다. |notIn 또는 in **아님** |
|현재 값은 대상 값과 같을 수 없습니다. |notLike 또는 like **아님** |
|현재 값은 대/소문자를 구분하며 대상 값과 일치할 수 없습니다. |notMatch 또는 match **아님** |
|현재 값은 대/소문자를 구분하지 않으며 대상 값과 일치할 수 없습니다. |notMatchInsensitively 또는 matchInsensitively **아님** |
|정책 정의의 효과 세부 정보와 일치하는 관련 리소스가 없습니다. |**then.details.type**에서 정의되고 정책 규칙의 **if** 부분에 정의된 리소스와 관련된 유형의 리소스가 존재하지 않습니다. |

## <a name="compliance-details-for-guest-configuration"></a>게스트 구성에 대한 준수 세부 정보

_게스트 구성_ 범주의 _auditIfNotExists_ 정책의 경우 VM 내에서 여러 설정이 평가될 수 있으며, 설정별 세부 정보를 확인해야 합니다. 예를 들어 암호 정책 목록에 대한 감사를 수행하고 그 중 하나에만 _규정 비준수_ 상태가 있는 경우 준수하지 않는 특정 암호 정책 및 이유를 알아야 합니다.

또한 VM에 직접 로그인할 수 있는 액세스 권한이 없을 수도 있지만 VM이 _규정 비준수_인 이유를 보고해야 합니다.

### <a name="azure-portal"></a>Azure portal

정책 준수 세부 정보를 보기 위해 위의 섹션에서 설명한 것과 동일한 단계를 수행하여 시작합니다.

준수 세부 정보 창 보기에서 **마지막으로 평가된 리소스** 링크를 클릭합니다.

:::image type="content" source="../media/determine-non-compliance/guestconfig-auditifnotexists-compliance.png" alt-text="auditIfNotExists 정의 세부 정보 보기" border="false":::

**게스트 할당** 페이지에는 사용 가능한 모든 준수 세부 정보가 표시됩니다. 뷰의 각 행은 머신 내에서 수행된 평가를 나타냅니다. **Reason** 열에서 게스트 할당이 _규정 비준수_인 이유를 설명하는 구가 표시됩니다. 예를 들어 암호 정책을 감사하는 경우 **Reason** 열에 각 설정에 대한 현재 값을 포함한 텍스트가 표시됩니다.

:::image type="content" source="../media/determine-non-compliance/guestconfig-compliance-details.png" alt-text="준수 세부 정보 보기" border="false":::

### <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell에서 준수 세부 정보를 확인할 수도 있습니다. 우선 게스트 구성 모듈이 설치되어 있는지 확인합니다.

```azurepowershell-interactive
Install-Module Az.GuestConfiguration
```

다음 명령을 사용하여 VM에 대한 모든 게스트 할당의 현재 상태를 볼 수 있습니다.

```azurepowershell-interactive
Get-AzVMGuestPolicyStatus -ResourceGroupName <resourcegroupname> -VMName <vmname>
```

```output
PolicyDisplayName                                                         ComplianceReasons
-----------------                                                         -----------------
Audit that an application is installed inside Windows VMs                 {[InstalledApplication]bwhitelistedapp}
Audit that an application is not installed inside Windows VMs.            {[InstalledApplication]NotInstalledApplica...
```

VM이 _규정 비준수_인 이유를 설명하는 _이유_ 구만 보려면 Reason 자식 속성만 반환합니다.

```azurepowershell-interactive
Get-AzVMGuestPolicyStatus -ResourceGroupName <resourcegroupname> -VMName <vmname> | % ComplianceReasons | % Reasons | % Reason
```

```output
The following applications are not installed: '<name>'.
```

머신 범위에서 게스트 할당에 대한 준수 기록을 출력할 수도 있습니다. 이 명령의 출력에는 VM의 각 보고서에 대한 세부 정보가 포함됩니다.

> [!NOTE]
> 출력은 많은 양의 데이터를 반환할 수 있습니다. 변수에 출력을 저장하는 것이 좋습니다.

```azurepowershell-interactive
$guestHistory = Get-AzVMGuestPolicyStatusHistory -ResourceGroupName <resourcegroupname> -VMName <vmname>
$guestHistory
```

```output
PolicyDisplayName                                                         ComplianceStatus ComplianceReasons StartTime              EndTime                VMName LatestRepor
                                                                                                                                                                  tId
-----------------                                                         ---------------- ----------------- ---------              -------                ------ -----------
[Preview]: Audit that an application is installed inside Windows VMs      NonCompliant                       02/10/2019 12:00:38 PM 02/10/2019 12:00:41 PM VM01  ../17fg0...
<truncated>
```

이 뷰를 간소화하려면 **ShowChanged** 매개 변수를 사용합니다. 이 명령의 출력에는 준수 상태 변경을 수행한 보고서만 포함됩니다.

```azurepowershell-interactive
$guestHistory = Get-AzVMGuestPolicyStatusHistory -ResourceGroupName <resourcegroupname> -VMName <vmname> -ShowChanged
$guestHistory
```

```output
PolicyDisplayName                                                         ComplianceStatus ComplianceReasons StartTime              EndTime                VMName LatestRepor
                                                                                                                                                                  tId
-----------------                                                         ---------------- ----------------- ---------              -------                ------ -----------
Audit that an application is installed inside Windows VMs                 NonCompliant                       02/10/2019 10:00:38 PM 02/10/2019 10:00:41 PM VM01  ../12ab0...
Audit that an application is installed inside Windows VMs.                Compliant                          02/09/2019 11:00:38 AM 02/09/2019 11:00:39 AM VM01  ../e3665...
Audit that an application is installed inside Windows VMs                 NonCompliant                       02/09/2019 09:00:20 AM 02/09/2019 09:00:23 AM VM01  ../15ze1...
```

## <a name="change-history-preview"></a><a name="change-history"></a>변경 기록(미리 보기)

새 **공개 미리 보기**의 일부로, [전체 모드 삭제](../../../azure-resource-manager/templates/complete-mode-deletion.md)를 지원하는 모든 Azure 리소스에 대해 지난 14일 간의 변경 기록을 사용할 수 있습니다. 변경 기록은 변경이 검색된 시간 및 각 변경의 _시각적 차이_에 대한 세부 정보를 제공합니다. 변경 내용 검색은 Azure Resource Manager 속성이 추가, 제거 또는 변경 될 때 트리거됩니다.

1. **모든 서비스**를 클릭한 후 **정책**을 검색하고 선택하여 Azure Portal에서 Azure Policy 서비스를 시작합니다.

1. **개요** 또는 **준수** 페이지에서 **준수 상태**에 있는 정책을 선택합니다.

1. **정책 준수** 페이지의 **리소스 준수** 탭에서 리소스를 선택합니다.

1. **리소스 준수** 페이지에서 **변경 기록(미리 보기)** 탭을 선택합니다. 검색된 변경 목록이 표시됩니다(있는 경우).

   :::image type="content" source="../media/determine-non-compliance/change-history-tab.png" alt-text="리소스 준수 페이지의 Azure Policy 변경 기록 탭" border="false":::

1. 검색된 변경 중 하나를 선택합니다. 리소스의 _시각적 차이_는 **변경 기록** 페이지에 표시됩니다.

   :::image type="content" source="../media/determine-non-compliance/change-history-visual-diff.png" alt-text="변경 기록 페이지의 Azure Policy 변경 기록 시각적 차이" border="false":::

_시각적 차이_는 리소스 변경 내용을 식별하는 데 도움이 됩니다. 검색된 변경 내용이 리소스의 현재 호환 상태와 관련되어 있지 않을 수 있습니다.

변경 기록 데이터는 [Azure Resource Graph](../../resource-graph/overview.md)에 의해 제공됩니다. Azure Portal 외부에서 이 정보를 쿼리하려면 [리소스 변경 내용 가져오기](../../resource-graph/how-to/get-resource-changes.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- [Azure Policy 샘플](../samples/index.md)에서 예제를 검토합니다.
- [Azure Policy 정의 구조](../concepts/definition-structure.md)를 검토합니다.
- [정책 효과 이해](../concepts/effects.md)를 검토합니다.
- [프로그래밍 방식으로 정책을 생성하는](programmatically-create.md) 방법을 이해합니다.
- [규정 준수 데이터를 가져오는](get-compliance-data.md) 방법을 알아봅니다.
- [규정 비준수 리소스를 수정](remediate-resources.md)하는 방법을 알아봅니다.
- [Azure 관리 그룹으로 리소스 구성](../../management-groups/overview.md)을 포함하는 관리 그룹을 검토합니다.
