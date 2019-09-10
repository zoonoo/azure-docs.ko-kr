---
title: 고급 쿼리 샘플
description: Azure Resource Graph를 사용하여 가상 머신 확장 집합 용량, 사용된 모든 태그 나열 및 정규식과 일치하는 가상 머신을 비롯한 일부 고급 쿼리를 실행합니다.
author: DCtheGeek
ms.author: dacoulte
ms.date: 08/29/2019
ms.topic: quickstart
ms.service: resource-graph
manager: carmonm
ms.openlocfilehash: 33c67f77a26e2a4fc97d7f5483aad53c121e117b
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70239007"
---
# <a name="advanced-resource-graph-queries"></a>고급 Resource Graph 쿼리

Azure Resource Graph를 사용하는 쿼리를 이해하는 첫 번째 단계는 [쿼리 언어](../concepts/query-language.md)를 기본적으로 이해하는 것입니다. 아직 [Azure Data Explorer](../../../data-explorer/data-explorer-overview.md)에 익숙하지 않은 경우에는 기본 사항을 검토하여 원하는 리소스에 대한 요청을 작성하는 방법을 이해하는 것이 좋습니다.

다음 고급 쿼리를 살펴보겠습니다.

> [!div class="checklist"]
> - [가상 머신 확장 집합 용량 및 크기 가져오기](#vmss-capacity)
> - [모든 태그 이름 나열](#list-all-tags)
> - [정규식과 일치하는 가상 머신](#vm-regex)
> - [DisplayNames를 사용한 테넌트 및 구독 이름 포함](#displaynames)

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free)을 만듭니다.

## <a name="language-support"></a>언어 지원

Azure CLI(확장을 통해) 및 Azure PowerShell(모듈을 통해)은 Azure Resource Graph를 지원합니다. 다음 쿼리를 실행하기 전에 사용자 환경이 준비되었는지 확인합니다. 선택한 셸 환경의 설치 및 유효성 검사 단계에 대해서는 [Azure CLI](../first-query-azurecli.md#add-the-resource-graph-extension) 및 [Azure PowerShell](../first-query-powershell.md#add-the-resource-graph-module)을 참조하세요.

## <a name="vmss-capacity"></a>가상 머신 확장 집합 용량 및 크기 가져오기

이 쿼리는 가상 머신 확장 집합 리소스를 찾고 가상 머신 크기 및 확장 집합의 용량을 포함하는 다양한 세부 정보를 가져옵니다. 이 쿼리는 `toint()` 함수를 사용하여 정렬할 수 있도록 용량을 숫자로 캐스팅합니다. 마지막으로, 열의 이름이 사용자 지정 명명 속성으로 바뀝니다.

```kusto
where type=~ 'microsoft.compute/virtualmachinescalesets'
| where name contains 'contoso'
| project subscriptionId, name, location, resourceGroup, Capacity = toint(sku.capacity), Tier = sku.name
| order by Capacity desc
```

```azurecli-interactive
az graph query -q "where type=~ 'microsoft.compute/virtualmachinescalesets' | where name contains 'contoso' | project subscriptionId, name, location, resourceGroup, Capacity = toint(sku.capacity), Tier = sku.name | order by Capacity desc"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type=~ 'microsoft.compute/virtualmachinescalesets' | where name contains 'contoso' | project subscriptionId, name, location, resourceGroup, Capacity = toint(sku.capacity), Tier = sku.name | order by Capacity desc"
```

## <a name="list-all-tags"></a>모든 태그 이름 나열

이 쿼리는 태그를 사용하여 시작하고 모든 고유한 태그 이름 및 해당 형식을 나열하는 JSON 개체를 빌드합니다.

```kusto
project tags
| summarize buildschema(tags)
```

```azurecli-interactive
az graph query -q "project tags | summarize buildschema(tags)"
```

```azurepowershell-interactive
Search-AzGraph -Query "project tags | summarize buildschema(tags)"
```

## <a name="vm-regex"></a>정규식과 일치하는 가상 머신

이 쿼리는 [정규식](/dotnet/standard/base-types/regular-expression-language-quick-reference)(_regex_로 알려짐)과 일치하는 가상 머신을 찾습니다. **matches regex \@** 는 일치시킬 정규식을 정의할 수 있으며, `^Contoso(.*)[0-9]+$`입니다.
해당 regex 정의는 다음으로 설명되어 있습니다.

- `^` - 일치는 문자열의 시작 부분에서 시작해야 합니다.
- `Contoso` - 대/소문자 구분 문자열입니다.
- `(.*)` - 하위 식 일치:
  - `.` - 단일 문자를 일치시킵니다(새 줄 제외).
  - `*` - 이전 요소를 0번 이상 일치시킵니다.
- `[0-9]` - 0~9의 숫자에 대한 문자 그룹 일치입니다.
- `+` - 이전 요소를 한 번 이상 일치시킵니다.
- `$` - 이전 요소의 일치는 문자열의 끝에서 발생해야 합니다.

이름으로 일치시킨 후 쿼리는 이름 오름차순으로 이름 및 순서를 프로젝션합니다.

```kusto
where type =~ 'microsoft.compute/virtualmachines' and name matches regex @'^Contoso(.*)[0-9]+$'
| project name
| order by name asc
```

```azurecli-interactive
az graph query -q "where type =~ 'microsoft.compute/virtualmachines' and name matches regex @'^Contoso(.*)[0-9]+$' | project name | order by name asc"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type =~ 'microsoft.compute/virtualmachines' and name matches regex @'^Contoso(.*)[0-9]+$' | project name | order by name asc"
```

## <a name="displaynames"></a>DisplayNames를 사용한 테넌트 및 구독 이름 포함

이 쿼리는 _DisplayNames_ 옵션과 함께 새로운 **Include** 매개 변수를 사용하여 **subscriptionDisplayName** 및 **tenantDisplayName**을 결과에 추가합니다. 이 매개 변수는 Azure CLI 및 Azure PowerShell에 대해서만 사용할 수 있습니다.

```azurecli-interactive
az graph query -q "limit 1" --include displayNames
```

```azurepowershell-interactive
Search-AzGraph -Query "limit 1" -Include DisplayNames
```

> [!NOTE]
> 쿼리에서 **project**를 사용하여 반환된 속성을 지정하지 않으면 **subscriptionDisplayName** 및 **tenantDisplayName**이 결과에 자동으로 포함됩니다.
> 쿼리에서 **project**를 사용하는 경우 각 _DisplayName_ 필드를 **project**에 명시적으로 포함해야 합니다. 그렇지 않으면 **Include** 매개 변수가 사용되는 경우에도 이러한 매개 변수는 결과에 반환되지 않습니다.

## <a name="next-steps"></a>다음 단계

- [시작 쿼리](starter.md) 샘플 보기
- [쿼리 언어](../concepts/query-language.md)에 대해 자세히 알아보기
- [리소스 탐색](../concepts/explore-resources.md)에 대해 알아보기