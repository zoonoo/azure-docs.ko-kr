---
title: 규정 비준수의 원인 확인
description: 리소스를 호환 되지 않는 경우 여러 가지 가능한 이유가 있습니다. 비 호환성을 일으킨 원인을 확인 하는 방법을 알아봅니다.
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 02/26/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: dba8d9413229a0fa236b082e2e11dbd1a9fe5a5f
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/04/2019
ms.locfileid: "57314129"
---
# <a name="determine-causes-of-non-compliance"></a>규정 비준수의 원인 확인

Azure 리소스 정책 규칙을 준수 하도록 결정 되 면 리소스를 사용 하 여 미준수 규칙의 부분을 이해 하는 것이 유용 합니다. 변경 내용 변경와 호환 되도록 이전에 규격이 리소스를 이해 하는 것이 유용 합니다. 두 가지 방법으로이 정보를 찾을 수 있습니다.

> [!div class="checklist"]
> - [준수 세부 정보](#compliance-details)
> - [변경 내용 (미리 보기)](#change-history-preview)

## <a name="compliance-details"></a>준수 세부 정보

해당 리소스에 대 한 준수 세부 정보에서 사용할 리소스를 호환 되지 않는 경우는 **정책 준수** 페이지입니다. 규정 준수 세부 정보 창에는 다음 정보가 포함 됩니다.

- 이름, 형식, 위치 및 리소스 ID와 같은 리소스 세부 정보
- 준수 상태 및 현재 정책 할당에 대 한 마지막 평가의 타임 스탬프
- 목록을 _이유가_ 비준수에 대 한는 리소스

> [!IMPORTANT]
> 에 대 한 준수 세부 정보를 _규격이 아닌_ 리소스 표시 속성의 현재 값을 해당 리소스에 대해 사용자에 게 **읽을** 작업을 합니다 **형식** 의 리소스입니다. 예를 들어 경우는 _규격이 아닌_ 리소스가 **microsoft.compute/virtualmachines** 는 사용자가를 **Microsoft.Compute/virtualMachines/read** 작업입니다. 사용자에 게 필요한 작업을 하는 경우 액세스 오류가 표시 됩니다.

규정 준수 세부 정보를 보려면 다음을 수행 합니다.

1. **모든 서비스**를 클릭한 후 **정책**을 검색하고 선택하여 Azure Portal에서 Azure Policy 서비스를 시작합니다.

1. 에 **개요** 또는 **준수** 페이지에서 정책을 선택 합니다는 **준수 상태** 즉 _규격이 아닌_합니다.

1. 아래는 **리소스 규정 준수** 탭의 **정책 준수** 페이지에서 마우스 오른쪽 단추로 클릭 하거나 리소스의 줄임표를 선택를 **준수 상태** 즉  _호환 되지 않는_합니다. 선택한 **준수 세부 정보를 보려면**합니다.

   ![규정 준수 세부 정보 보기 옵션](../media/determine-non-compliance/view-compliance-details.png)

1. 합니다 **준수 세부 정보** 현재 정책 할당 된 리소스의 최신 평가의 정보를 창에 표시 됩니다. 이 예제에서는 필드 **Microsoft.Sql/servers/version** 것 _12.0_ 예상 하는 정책을 정의 하는 동안 _14.0_합니다. 여러 가지 이유로 리소스 비준수를 각이 창에 나열 됩니다.

   ![규정 준수 세부 정보 창](../media/determine-non-compliance/compliance-details-pane.png)

   에 **auditIfNotExists** 또는 **deployIfNotExists** 세부 정보를 포함 하는 정책 정의 **details.type** 속성과 선택적 속성입니다. 목록에 대해서 [auditIfNotExists 속성](../concepts/effects.md#auditifnotexists-properties) 하 고 [deployIfNotExists 속성](../concepts/effects.md#deployifnotexists-properties)합니다. **마지막으로 리소스를 평가** 에서 관련 리소스를 **세부 정보** 정의의 섹션입니다.

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

   ![규정 준수 세부 정보 창-* ifNotExists](../media/determine-non-compliance/compliance-details-pane-existence.png)

> [!NOTE]
> 속성 값은 데이터를 보호 하는 _비밀_ 현재 값을 별표를 표시 합니다.

이러한 세부 정보 리소스를 현재 비준수 이유를 설명 하지만 때 변경 된 비준수 상태가 되도록를 발생 시킨 리소스에 표시 되지 않습니다. 해당 정보를 참조 하세요 [변경 내용 (미리 보기)](#change-history-preview) 아래.

### <a name="compliance-reasons"></a>규정 준수

다음 표에 각 가능한 매핑합니다 _이유_ 책임을 [조건](../concepts/definition-structure.md#conditions) 정책 정의에:

|이유 | 조건 |
|-|-|
|현재 값은 대상 값을 키로 포함해야 합니다. |containsKey 또는 **되지** notContainsKey |
|현재 값은 대상 값을 포함해야 합니다. |포함 하거나 **되지** notContains |
|현재 값은 대상 값과 같아야 합니다. |같음 또는 **되지** notEquals |
|현재 값이 있어야 합니다. |exists |
|현재 값은 대상 값에 있어야 합니다. |또는 **되지** notIn |
|현재 값은 대상 값과 같아야 합니다. |같은 또는 **되지** notLike |
|현재 값은 대/소문자를 구분하며 대상 값과 일치해야 합니다. |일치 하거나 **되지** notMatch |
|현재 값은 대/소문자를 구분하지 않으며 대상 값과 일치해야 합니다. |matchInsensitively 또는 **되지** notMatchInsensitively |
|현재 값은 대상 값을 키로 포함할 수 없습니다. |notContainsKey 또는 **되지** containsKey|
|현재 값은 대상 값을 포함할 수 없습니다. |notContains 또는 **되지** 포함 |
|현재 값은 대상 값과 같을 수 없습니다. |notEquals 또는 **되지** 같음 |
|현재 값이 없어야 합니다. |**하지** 존재  |
|현재 값이 대상 값에 포함될 수 없습니다. |notIn 또는 **되지** 에서 |
|현재 값은 대상 값과 같을 수 없습니다. |notLike 또는 **되지** 같은 |
|현재 값은 대/소문자를 구분하며 대상 값과 일치할 수 없습니다. |notMatch 또는 **되지** 일치 |
|현재 값은 대/소문자를 구분하지 않으며 대상 값과 일치할 수 없습니다. |notMatchInsensitively 또는 **되지** matchInsensitively |
|정책 정의에 적용 세부 정보와 일치 하는 관련된 리소스가 없습니다. |에 정의 된 형식의 리소스 **then.details.type** 에 정의 된 리소스에 관련 된 합니다 **경우** 부분 정책 규칙이 존재 하지 않습니다. |

## <a name="change-history-preview"></a>변경 기록(미리 보기)

새의 일부로 **공개 미리 보기**지난 14 일, 변경 기록은 지 원하는 모든 Azure 리소스에 대 한 사용 가능한 [모드 삭제 완료](../../../azure-resource-manager/complete-mode-deletion.md)합니다. 변경 기록은 변경이 검색된 시간 및 각 변경의 _시각적 차이_에 대한 세부 정보를 제공합니다. 변경 탐지는 리소스 관리자 속성을 추가, 제거 또는 변경할 때 트리거됩니다.

1. **모든 서비스**를 클릭한 후 **정책**을 검색하고 선택하여 Azure Portal에서 Azure Policy 서비스를 시작합니다.

1. 에 **개요** 또는 **준수** 페이지에서 정책을 선택 합니다 **준수 상태**합니다.

1. 아래는 **리소스 규정 준수** 탭을 **정책 준수** 페이지, 리소스를 선택 합니다.

1. **리소스 준수** 페이지에서 **변경 기록(미리 보기)** 탭을 선택합니다. 검색된 변경 목록이 표시됩니다(있는 경우).

   ![정책 변경 기록 - 탭](../media/determine-non-compliance/change-history-tab.png)

1. 검색된 변경 중 하나를 선택합니다. _visual diff_ 리소스가 표시 됩니다에 대 한 합니다 **변경 내용** 페이지입니다.

   ![정책 변경 기록 - 시각적 차이](../media/determine-non-compliance/change-history-visual-diff.png)

_시각적 차이_는 리소스 변경 내용을 식별하는 데 도움이 됩니다. 검색 된 변경 내용은 리소스의 현재 호환성 상태 관련이 있을 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure Policy 샘플](../samples/index.md)에서 예제를 검토합니다.
- [Policy 정의 구조](../concepts/definition-structure.md)를 검토합니다.
- [정책 효과 이해](../concepts/effects.md)를 검토합니다.
- [프로그래밍 방식으로 정책을 생성](programmatically-create.md)하는 방법을 이해합니다.
- [규정 준수 데이터를 가져오는 방법](getting-compliance-data.md)을 알아봅니다.
- [비준수 리소스를 수정](remediate-resources.md)하는 방법을 알아봅니다.
- [Azure 관리 그룹으로 리소스 구성](../../management-groups/overview.md)을 포함하는 관리 그룹을 검토합니다.