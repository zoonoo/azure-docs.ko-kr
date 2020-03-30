---
title: 규정 비준수의 원인 확인
description: 리소스가 비준수인 경우 여러 가지 이유가 있을 수 있습니다. 규정 미준수의 원인을 알아봅니다.
ms.date: 04/26/2019
ms.topic: how-to
ms.openlocfilehash: c931831ddf3cc727b9861e75969eac3bf00c9e45
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264636"
---
# <a name="determine-causes-of-non-compliance"></a>규정 비준수의 원인 확인

Azure 리소스가 정책 규칙을 준수하지 않는 것으로 확인되면 리소스가 호환되지 않는 규칙 부분을 이해하는 것이 좋습니다. 또한 이전에 규정을 준수하는 리소스를 비준수로 변경한 변경 내용을 이해하는 것도 유용합니다. 이 정보를 찾는 방법에는 두 가지가 있습니다.

> [!div class="checklist"]
> - [규정 준수 세부 정보](#compliance-details)
> - [변경 기록(미리 보기)](#change-history)

## <a name="compliance-details"></a>규정 준수 세부 정보

리소스가 비준수인 경우 해당 리소스에 대한 규정 준수 세부 정보는 **정책 준수** 페이지에서 확인할 수 있습니다. 규정 준수 세부 정보 창에는 다음 정보가 포함됩니다.

- 이름, 유형, 위치 및 리소스 ID와 같은 리소스 세부 정보
- 현재 정책 할당에 대한 마지막 평가의 규정 준수 상태 및 타임스탬프
- 리소스 비준수사유 목록 _reasons_

> [!IMPORTANT]
> _비준수_ 리소스에 대한 규정 준수 세부 정보에 해당 리소스의 현재 속성 값이 표시되므로 사용자는 리소스 **유형에** 대한 **읽기** 작업을 가져야 합니다. 예를 들어 _비준수_ 리소스가 **Microsoft.Compute/virtualMachine인** 경우 사용자는 **Microsoft.Compute/가상 시스템/읽기** 작업이 있어야 합니다. 사용자에게 필요한 작업이 없는 경우 액세스 오류가 표시됩니다.

규정 준수 세부 정보를 보려면 다음 단계를 따르십시오.

1. **모든 서비스**를 클릭한 후 **정책**을 검색하고 선택하여 Azure Portal에서 Azure Policy 서비스를 시작합니다.

1. **개요** 또는 **규정 준수** 페이지에서 _비준수_ **상태의 정책을** 선택합니다.

1. **정책** **준수** 페이지의 리소스 준수 탭에서 _비준수_ **상태의 리소스** 타원을 마우스 오른쪽 단추로 클릭하거나 선택합니다. 그런 다음 **규정 준수 세부 정보 보기를**선택합니다.

   ![규정 준수 세부 정보 옵션 보기](../media/determine-non-compliance/view-compliance-details.png)

1. **규정 준수 세부 정보** 창에는 리소스의 최신 평가에서 현재 정책 할당에 대한 정보가 표시됩니다. 이 예제에서는 **Microsoft.Sql/서버/버전** 필드가 _12.0인_ 반면 정책 정의는 _14.0으로_예상됩니다. 리소스가 여러 가지 이유로 비규격인 경우 각 리소스가 이 창에 나열됩니다.

   ![규정 준수 세부 정보 창 및 비준수 이유](../media/determine-non-compliance/compliance-details-pane.png)

   **auditIfNotExists** 또는 **deployIfNotExists** 정책 정의의 경우 세부 정보에는 **details.type** 속성 및 선택적 속성이 포함됩니다. 목록의 경우 [auditIfNotExists 속성](../concepts/effects.md#auditifnotexists-properties) 및 [deployIfNotExists 속성을](../concepts/effects.md#deployifnotexists-properties)참조하십시오. **마지막으로 평가된 리소스는** 정의의 **세부 정보** 섹션에서 얻은 관련 리소스입니다.

   부분 **배포IfNotExists** 정의 예:

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

   ![규정 준수 세부 정보 창 - *ifNotExists](../media/determine-non-compliance/compliance-details-pane-existence.png)

> [!NOTE]
> 데이터를 보호하기 위해 속성 값이 _비밀인_ 경우 현재 값은 별표를 표시합니다.

이러한 세부 정보는 리소스가 현재 비준수인 이유를 설명하지만 리소스가 비규격으로 변경된 시기는 표시되지 않습니다. 이 정보는 [아래의 변경 기록(미리 보기)을](#change-history) 참조하십시오.

### <a name="compliance-reasons"></a>규정 준수 이유

다음 행렬은 가능한 각 _이유를_ 정책 정의의 책임 [조건에](../concepts/definition-structure.md#conditions) 매핑합니다.

|이유 | 조건 |
|-|-|
|현재 값은 대상 값을 키로 포함해야 합니다. |포함키 또는 **하지포함키** |
|현재 값에는 대상 값이 포함되어야 합니다. |포함 **여부** |
|현재 값은 대상 값과 같아야 합니다. |같음 또는 **같지 않음** |
|현재 값은 대상 값보다 적어야 합니다. |더 적게 또는 **하지 큰Or같음** |
|현재 값은 대상 값보다 크거나 같아야 합니다. |greaterOr같거나 **not** 더 적은 |
|현재 값은 대상 값보다 커야 합니다. |더 **not** 큰 또는 이하Or같음 |
|현재 값은 대상 값보다 적거나 같아야 합니다. |이하또는같음 또는 **크지 않음** |
|현재 값이 있어야 합니다. |exists |
|현재 값은 대상 값에 있어야 합니다. |에서 또는 **하지 않음** |
|현재 값은 대상 값과 같아야 합니다. |좋아요 또는 **좋아하지 않음** |
|현재 값은 대/소문자를 지정해야 대상 값과 일치해야 합니다. |일치 **여부** 안 일치 |
|현재 값은 대/소문자를 구분하지 않아야 대상 값과 일치해야 합니다. |일치민감하지 않거나 **하지 않음민감하게** 일치 |
|현재 값은 대상 값을 키로 포함해서는 안 됩니다. |notContains키 또는 **포함키**|
|현재 값은 대상 값을 포함해서는 안 됩니다. |not포함 **여부** |
|현재 값은 대상 값과 같아서는 안 됩니다. |같지 않거나 같지 **않음** |
|현재 값이 없어야 합니다. |존재하지 **않음**  |
|현재 값은 대상 값에 없어야 합니다. |notin 또는 **하지** |
|현재 값은 대상 값과 같아서는 안 됩니다. |좋아하지 않거나 **좋아하지 않음** |
|현재 값은 대/소문자를 구분하지 않아야 대상 값과 일치합니다. |not일치 또는 일치하지 **않음** |
|현재 값은 대/소문자를 구분하지 않아야 대상 값과 일치합니다. |not민감감 또는 일치하지 **않음민감하게** |
|정책 정의의 효과 세부 정보와 일치하는 관련 리소스가 없습니다. |**그런 다음.details.type에** 정의된 형식의 리소스이며 정책 규칙의 **if** 부분에 정의된 리소스와 관련이 있습니다. |

## <a name="compliance-details-for-guest-configuration"></a>게스트 구성에 대한 규정 준수 세부 정보

게스트 _구성_ 범주에 있는 _auditIfNotExists_ 정책의 경우 VM 내에서 여러 설정이 평가될 수 있으며 설정별 세부 정보를 확인해야 합니다. 예를 들어 암호 정책 목록에 대해 감사하는 중 하나만 _비호환_상태인 경우 규정 을 준수하지 않는 특정 암호 정책과 그 이유를 알아야 합니다.

VM에 직접 로그인할 수 있는 액세스 권한이 없을 수도 있지만 VM이 _비준수인_이유를 보고해야 합니다.

### <a name="azure-portal"></a>Azure portal

정책 준수 세부 정보를 보려면 위의 섹션에서 동일한 단계를 따르는 것으로 시작합니다.

규정 **준수 세부 정보** 창보기에서 **마지막으로 평가된 리소스**링크를 클릭합니다.

   ![감사보기IfNotExists 정의 세부 정보](../media/determine-non-compliance/guestconfig-auditifnotexists-compliance.png)

**게스트 할당** 페이지에는 사용 가능한 모든 규정 준수 세부 정보가 표시됩니다. 뷰의 각 행은 컴퓨터 내부에서 수행된 평가를 나타냅니다. **이유** 열에는 게스트 할당이 _비준수인_ 이유를 설명하는 문구가 표시됩니다. 예를 들어 암호 정책을 감사하는 경우 **Reason** 열에는 각 설정의 현재 값을 포함한 텍스트가 표시됩니다.

![규정 준수 세부 정보 보기](../media/determine-non-compliance/guestconfig-compliance-details.png)

### <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell에서 규정 준수 세부 정보를 볼 수도 있습니다. 먼저 게스트 구성 모듈이 설치되어 있는지 확인합니다.

```azurepowershell-interactive
Install-Module Az.GuestConfiguration
```

다음 명령을 사용하여 VM에 대한 모든 게스트 할당의 현재 상태를 볼 수 있습니다.

```azurepowershell-interactive
Get-AzVMGuestPolicyReport -ResourceGroupName <resourcegroupname> -VMName <vmname>
```

```output
PolicyDisplayName                                                         ComplianceReasons
-----------------                                                         -----------------
Audit that an application is installed inside Windows VMs                 {[InstalledApplication]bwhitelistedapp}
Audit that an application is not installed inside Windows VMs.            {[InstalledApplication]NotInstalledApplica...
```

VM이 _비준수인_이유를 설명하는 _reason_ 구만 보려면 Reason 자식 속성만 반환합니다.

```azurepowershell-interactive
Get-AzVMGuestPolicyReport -ResourceGroupName <resourcegroupname> -VMName <vmname> | % ComplianceReasons | % Reasons | % Reason
```

```output
The following applications are not installed: '<name>'.
```

또한 컴퓨터의 범위에서 게스트 할당에 대한 규정 준수 기록을 출력할 수도 있습니다. 이 명령의 출력에는 VM에 대한 각 보고서의 세부 정보가 포함됩니다.

> [!NOTE]
> 출력은 많은 양의 데이터를 반환할 수 있습니다. 출력을 변수에 저장하는 것이 좋습니다.

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

이 보기를 단순화하려면 **ShowChanged** 매개 변수를 사용합니다. 이 명령의 출력에는 규정 준수 상태가 변경된 보고서만 포함됩니다.

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

## <a name="change-history-preview"></a><a name="change-history"/>변경 기록(미리 보기)

새 **공개 미리 보기의**일부로 [전체 모드 삭제를](../../../azure-resource-manager/templates/complete-mode-deletion.md)지원하는 모든 Azure 리소스에서 지난 14일 간의 변경 기록을 사용할 수 있습니다. 변경 기록은 변경이 검색된 시간 및 각 변경의 _시각적 차이_에 대한 세부 정보를 제공합니다. 리소스 관리자 속성을 추가, 제거 또는 변경할 때 변경 검색이 트리거됩니다.

1. **모든 서비스**를 클릭한 후 **정책**을 검색하고 선택하여 Azure Portal에서 Azure Policy 서비스를 시작합니다.

1. **개요** 또는 **규정 준수** 페이지에서 모든 규정 준수 **상태에서**정책을 선택합니다.

1. 정책 **준수** 페이지의 리소스 **준수** 탭에서 리소스를 선택합니다.

1. **리소스 준수** 페이지에서 **변경 기록(미리 보기)** 탭을 선택합니다. 검색된 변경 목록이 표시됩니다(있는 경우).

   ![리소스 준수 페이지에서 Azure 정책 변경 기록 탭](../media/determine-non-compliance/change-history-tab.png)

1. 검색된 변경 중 하나를 선택합니다. 리소스에 대한 _시각적 diff는_ **변경 기록** 페이지에 표시됩니다.

   ![변경 기록 페이지에서 Azure 정책 변경 기록 시각적 Diff](../media/determine-non-compliance/change-history-visual-diff.png)

_시각적 차이_는 리소스 변경 내용을 식별하는 데 도움이 됩니다. 검색된 변경 내용은 리소스의 현재 규정 준수 상태와 관련이 없을 수 있습니다.

변경 기록 데이터는 [Azure 리소스 그래프에서](../../resource-graph/overview.md)제공합니다. Azure 포털 외부에서 이 정보를 쿼리하려면 [리소스 변경 내용 확인을](../../resource-graph/how-to/get-resource-changes.md)참조하십시오.

## <a name="next-steps"></a>다음 단계

- Azure 정책 [샘플의 예제를 검토합니다.](../samples/index.md)
- [Azure Policy 정의 구조](../concepts/definition-structure.md)를 검토합니다.
- [정책 효과 이해 검토](../concepts/effects.md).
- [프로그래밍 방식으로 정책을 만드는](programmatically-create.md)방법을 이해합니다.
- [규정 준수 데이터를 얻는](get-compliance-data.md)방법에 대해 알아봅니다.
- [비준수 리소스를 수정하는](remediate-resources.md)방법에 대해 알아봅니다.
- 관리 그룹이 Azure 관리 그룹으로 리소스 구성을 통해 어떤 내용인지 [검토합니다.](../../management-groups/overview.md)
