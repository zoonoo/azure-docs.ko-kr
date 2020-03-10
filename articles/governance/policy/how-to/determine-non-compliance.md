---
title: 규정 비준수의 원인 확인
description: 리소스가 비규격 인 경우 여러 가지 원인이 있을 수 있습니다. 비준수를 일으킨 원인을 확인 하는 방법을 알아보세요.
ms.date: 04/26/2019
ms.topic: how-to
ms.openlocfilehash: c931831ddf3cc727b9861e75969eac3bf00c9e45
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78385209"
---
# <a name="determine-causes-of-non-compliance"></a>규정 비준수의 원인 확인

Azure 리소스가 정책 규칙을 준수 하지 않는 것으로 확인 되 면 리소스가 호환 되지 않는 규칙의 부분을 이해 하는 것이 도움이 됩니다. 또한 이전에 규정을 준수 하지 않는 것으로 변경 된 변경 내용에 대해 이해 하는 것이 유용 합니다. 이 정보를 찾는 방법에는 두 가지가 있습니다.

> [!div class="checklist"]
> - [호환성 정보](#compliance-details)
> - [변경 내용 (미리 보기)](#change-history)

## <a name="compliance-details"></a>호환성 정보

리소스가 비규격 인 경우 **정책 준수** 페이지에서 해당 리소스에 대 한 준수 정보를 사용할 수 있습니다. 준수 정보 창에는 다음 정보가 포함 됩니다.

- 이름, 유형, 위치 및 리소스 ID와 같은 리소스 세부 정보
- 현재 정책 할당에 대 한 마지막 평가의 준수 상태 및 타임 스탬프
- 리소스 비준수의 _원인_ 목록

> [!IMPORTANT]
> _호환 되지 않는_ 리소스에 대 한 호환성 세부 정보에 해당 리소스에 대 한 속성의 현재 값이 표시 되므로 사용자가 리소스 **유형에** 대 한 **읽기** 작업을 수행 해야 합니다. 예를 들어 _비규격_ 리소스가 **virtualMachines/** VirtualMachines 이면 사용자는 **microsoft. compute//read** 작업을 수행 해야 합니다. 사용자에 게 필요한 작업이 없는 경우 액세스 오류가 표시 됩니다.

준수 정보를 보려면 다음 단계를 수행 합니다.

1. **모든 서비스**를 클릭한 후 **정책**을 검색하고 선택하여 Azure Portal에서 Azure Policy 서비스를 시작합니다.

1. **개요** 또는 **규정 준수** 페이지에서 _호환_되지 않는 **규정 준수 상태의** 정책을 선택 합니다.

1. **정책 준수** 페이지의 **리소스 준수** 탭 _에서 비준수 인_ **준수 상태의** 리소스에 대 한 줄임표를 마우스 오른쪽 단추로 클릭 하거나 선택 합니다. 그런 다음 **호환성 세부 정보 보기**를 선택 합니다.

   ![준수 정보 보기 옵션](../media/determine-non-compliance/view-compliance-details.png)

1. **호환성 정보** 창에 현재 정책 할당에 대 한 리소스의 최신 평가 정보가 표시 됩니다. 이 예에서는 정책 정의에 _14.0_가 필요 하지만 **Microsoft .sql/servers/version** 필드는 _12.0_ 입니다. 여러 가지 이유로 리소스를 비규격으로 설정 하는 경우 각이이 창에 나열 됩니다.

   ![규정 준수 세부 정보 창 및 비호환 이유](../media/determine-non-compliance/compliance-details-pane.png)

   **AuditIfNotExists** 또는 **Deployifnotexists** 정책 정의의 경우 세부 정보에는 **details** 속성과 선택적 속성이 포함 됩니다. 목록은 [auditIfNotExists properties](../concepts/effects.md#auditifnotexists-properties) 및 [Deployifnotexists 속성](../concepts/effects.md#deployifnotexists-properties)을 참조 하세요. **마지막 평가 리소스** 는 정의의 **세부 정보** 섹션에 있는 관련 리소스입니다.

   예제 부분 **Deployifnotexists** 정의:

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

   ![준수 정보 창-* ifNotExists](../media/determine-non-compliance/compliance-details-pane-existence.png)

> [!NOTE]
> 데이터를 보호 하기 위해 속성 값이 _비밀_ 이면 현재 값에 별표가 표시 됩니다.

이러한 세부 정보는 리소스가 현재 비규격 인 이유를 설명 하지만 비준수로 인해 리소스가 변경 된 경우를 표시 하지 않습니다. 해당 정보는 아래의 [변경 내용 (미리 보기)](#change-history) 을 참조 하세요.

### <a name="compliance-reasons"></a>규정 준수 이유

다음 행렬은 가능한 각 _이유_ 를 정책 정의의 책임 [조건](../concepts/definition-structure.md#conditions) 에 매핑합니다.

|이유 | 조건 |
|-|-|
|현재 값은 대상 값을 키로 포함 해야 합니다. |containsKey 또는 **not** notContainsKey |
|현재 값은 대상 값을 포함 해야 합니다. |contains 또는 **not** notcontains |
|현재 값이 대상 값과 같아야 합니다. |같음 또는 **같지 않음** 참고 |
|현재 값은 대상 값 보다 작아야 합니다. |less 또는 **not** greaterOrEquals |
|현재 값은 대상 값 보다 크거나 같아야 합니다. |greaterOrEquals 또는 **less** |
|현재 값은 대상 값 보다 커야 합니다. |**lessOrEquals 이상** |
|현재 값은 대상 값 보다 작거나 같아야 합니다. |lessOrEquals 또는 크지 **않음** |
|현재 값이 있어야 합니다. |exists |
|현재 값은 대상 값에 있어야 합니다. |in 또는 **not** notin |
|현재 값은 대상 값과 같아야 합니다. |like 또는 **not** notlike |
|현재 값이 대상 값과 대/소문자를 구분 해야 합니다. |match 또는 **not** notmatch |
|현재 값은 대/소문자를 구분 하지 않고 대상 값과 일치 해야 합니다. |matchInsensitively 또는 **not** notMatchInsensitively |
|현재 값은 대상 값을 키로 포함 하지 않아야 합니다. |notContainsKey 또는 **not** containsKey|
|현재 값은 대상 값을 포함 하지 않아야 합니다. |notContains 또는 **not** contains |
|현재 값이 대상 값과 같지 않아야 합니다. |참고 |
|현재 값이 없어야 합니다. |존재 **하지 않음**  |
|현재 값이 대상 값에 속하지 않아야 합니다. |notIn 또는 **not** in |
|현재 값이 대상 값과 같을 수 없습니다. |notLike 또는 **not** like |
|현재 값은 대상 값과 대/소문자를 구분 하지 않아야 합니다. |notMatch 또는 **match** |
|현재 값이 대상 값과 대/소문자를 구분 하지 않아야 합니다. |notMatchInsensitively 또는 **not** matchInsensitively |
|관련 리소스가 정책 정의의 효과 세부 정보와 일치 하지 않습니다. |**Then. details. 형식** 에 정의 된 형식의 리소스와 정책 규칙의 해당 부분에 정의 된 리소스와 관련 된 리소스가 없는 **경우** |

## <a name="compliance-details-for-guest-configuration"></a>게스트 구성에 대 한 호환성 세부 정보

_게스트 구성_ 범주에 있는 _AUDITIFNOTEXISTS_ 정책의 경우 VM 내에서 여러 설정이 평가 될 수 있으며, 설정 별 세부 정보를 확인 해야 합니다. 예를 들어 암호 정책 목록에 대 한 감사를 수행 하 고 그 중 하나에만 비규격 상태가 있는 경우 준수 _하지_않는 특정 암호 정책 및 이유를 알아야 합니다.

VM에 직접 로그인 할 수 있는 액세스 권한이 없을 수도 있지만 VM이 _비규격_인 이유를 보고 해야 합니다.

### <a name="azure-portal"></a>Azure portal

정책 준수 세부 정보를 보기 위해 위의 섹션에서 설명한 것과 동일한 단계를 수행 하 여 시작 합니다.

**준수 정보** 창 보기에서 **마지막 평가 리소스**링크를 클릭 합니다.

   ![AuditIfNotExists 정의 정보 보기](../media/determine-non-compliance/guestconfig-auditifnotexists-compliance.png)

**게스트 할당** 페이지에는 사용 가능한 모든 호환성 정보가 표시 됩니다. 뷰의 각 행은 컴퓨터 내에서 수행 된 평가를 나타냅니다. **이유** 열에서 게스트 할당이 _비호환_ 인 이유를 설명 하는 구가 표시 됩니다. 예를 들어 암호 정책을 감사 하는 경우 **Reason** 열에 각 설정에 대 한 현재 값을 포함 하는 텍스트가 표시 됩니다.

![호환성 세부 정보 보기](../media/determine-non-compliance/guestconfig-compliance-details.png)

### <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell에서 준수 세부 정보를 확인할 수도 있습니다. 먼저 게스트 구성 모듈이 설치 되어 있는지 확인 합니다.

```azurepowershell-interactive
Install-Module Az.GuestConfiguration
```

다음 명령을 사용 하 여 VM에 대 한 모든 게스트 할당의 현재 상태를 볼 수 있습니다.

```azurepowershell-interactive
Get-AzVMGuestPolicyReport -ResourceGroupName <resourcegroupname> -VMName <vmname>
```

```output
PolicyDisplayName                                                         ComplianceReasons
-----------------                                                         -----------------
Audit that an application is installed inside Windows VMs                 {[InstalledApplication]bwhitelistedapp}
Audit that an application is not installed inside Windows VMs.            {[InstalledApplication]NotInstalledApplica...
```

VM이 _호환_되지 않는 이유를 설명 하는 _이유_ 문구만 보려면 reason 자식 속성만 반환 합니다.

```azurepowershell-interactive
Get-AzVMGuestPolicyReport -ResourceGroupName <resourcegroupname> -VMName <vmname> | % ComplianceReasons | % Reasons | % Reason
```

```output
The following applications are not installed: '<name>'.
```

컴퓨터 범위에서 게스트 할당에 대 한 준수 기록을 출력할 수도 있습니다. 이 명령의 출력에는 VM의 각 보고서에 대 한 세부 정보가 포함 됩니다.

> [!NOTE]
> 출력은 많은 양의 데이터를 반환할 수 있습니다. 변수에 출력을 저장 하는 것이 좋습니다.

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

이 뷰를 간소화 하려면 **Showchanged** 매개 변수를 사용 합니다. 이 명령의 출력에는 준수 상태 변경을 수행한 보고서만 포함 됩니다.

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

## <a name="a-namechange-historychange-history-preview"></a><a name="change-history"/>변경 기록 (미리 보기)

새 **공개 미리 보기**의 일부로, [전체 모드 삭제](../../../azure-resource-manager/templates/complete-mode-deletion.md)를 지 원하는 모든 Azure 리소스에 대 한 최근 14 일간의 변경 기록을 사용할 수 있습니다. 변경 기록은 변경이 검색된 시간 및 각 변경의 _시각적 차이_에 대한 세부 정보를 제공합니다. 변경 내용 검색은 리소스 관리자 속성이 추가, 제거 또는 변경 될 때 트리거됩니다.

1. **모든 서비스**를 클릭한 후 **정책**을 검색하고 선택하여 Azure Portal에서 Azure Policy 서비스를 시작합니다.

1. **개요** 또는 **규정 준수** 페이지에서 **준수 상태의**정책을 선택 합니다.

1. **정책 준수** 페이지의 **리소스 준수** 탭에서 리소스를 선택 합니다.

1. **리소스 준수** 페이지에서 **변경 기록(미리 보기)** 탭을 선택합니다. 검색된 변경 목록이 표시됩니다(있는 경우).

   ![리소스 준수 페이지의 Azure Policy 변경 기록 탭](../media/determine-non-compliance/change-history-tab.png)

1. 검색된 변경 중 하나를 선택합니다. 리소스의 _시각적 diff_ 는 **변경 기록** 페이지에 표시 됩니다.

   ![변경 기록 Azure Policy 변경 기록 페이지의 시각적 Diff](../media/determine-non-compliance/change-history-visual-diff.png)

_시각적 차이_는 리소스 변경 내용을 식별하는 데 도움이 됩니다. 검색 된 변경 내용이 리소스의 현재 호환 상태와 관련 되어 있지 않을 수 있습니다.

변경 기록 데이터는 [Azure 리소스 그래프](../../resource-graph/overview.md)에서 제공 됩니다. Azure Portal 외부에서이 정보를 쿼리하려면 [리소스 변경 내용 가져오기](../../resource-graph/how-to/get-resource-changes.md)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

- [Azure Policy 샘플](../samples/index.md)에서 예제를 검토 합니다.
- [Azure Policy 정의 구조](../concepts/definition-structure.md)를 검토합니다.
- [정책 효과 이해](../concepts/effects.md)를 검토합니다.
- [프로그래밍 방식으로 정책을 만드는](programmatically-create.md)방법을 알아봅니다.
- [준수 데이터를 가져오는](get-compliance-data.md)방법에 대해 알아봅니다.
- [비준수 리소스](remediate-resources.md)를 수정 하는 방법에 대해 알아봅니다.
- [Azure 관리 그룹으로 리소스 구성](../../management-groups/overview.md)을 포함하는 관리 그룹을 검토합니다.
