---
title: 시작 쿼리 샘플
description: Azure Resource Graph를 사용하여 리소스 개수 계산, 리소스 정렬 또는 특정 태그를 포함한 일부 시작 쿼리를 실행합니다.
author: DCtheGeek
ms.author: dacoulte
ms.date: 04/04/2019
ms.topic: quickstart
ms.service: resource-graph
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 2ba48e2a21bdee0c5698bdfa314dd3bf462c1c7e
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59267772"
---
# <a name="starter-resource-graph-queries"></a>Resource Graph 시작 쿼리

Azure Resource Graph를 사용하는 쿼리를 이해하는 첫 번째 단계는 [쿼리 언어](../concepts/query-language.md)를 기본적으로 이해하는 것입니다. 아직 [Azure Data Explorer](../../../data-explorer/data-explorer-overview.md)에 익숙하지 않은 경우에는 기본 사항을 검토하여 원하는 리소스에 대한 요청을 작성하는 방법을 이해하는 것이 좋습니다.

다음 시작 쿼리를 살펴보겠습니다.

> [!div class="checklist"]
> - [Azure 리소스 개수 계산](#count-resources)
> - [리소스를 이름별로 나열](#list-resources)
> - [이름의 내림차순으로 모든 가상 머신 나열](#show-vms)
> - [이름 및 해당 OS 유형별로 처음 5개의 가상 머신 표시](#show-sorted)
> - [OS 유형별 가상 머신 개수 계산](#count-os)
> - [저장소를 포함하는 리소스 표시](#show-storage)
> - [모든 공용 IP 주소 나열](#list-publicip)
> - [구독에 의해 IP 주소가 구성된 리소스 개수 계산](#count-resources-by-ip)
> - [특정 태그 값이 있는 리소스 나열](#list-tag)
> - [특정 태그 값이 있는 모든 저장소 계정 나열](#list-specific-tag)

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free)을 만듭니다.

[!INCLUDE [az-powershell-update](../../../../includes/updated-for-az.md)]

## <a name="language-support"></a>언어 지원

Azure CLI(확장을 통해) 및 Azure PowerShell(모듈을 통해)은 Azure Resource Graph를 지원합니다. 다음 쿼리를 실행하기 전에 사용자 환경이 준비되었는지 확인합니다. 선택한 셸 환경의 설치 및 유효성 검사 단계에 대해서는 [Azure CLI](../first-query-azurecli.md#add-the-resource-graph-extension) 및 [Azure PowerShell](../first-query-powershell.md#add-the-resource-graph-module)을 참조하세요.

## <a name="count-resources"></a>Azure 리소스 개수 계산

이 쿼리는 액세스 권한이 있는 구독에 있는 Azure 리소스의 개수를 반환합니다. 또한 이 쿼리는 선택한 셀에 적절한 Azure Resource Graph 구성 요소가 설치되어 있고 작동 중인지 검증하는 데도 적절합니다.

```Query
summarize count()
```

```azurecli-interactive
az graph query -q "summarize count()"
```

```azurepowershell-interactive
Search-AzGraph -Query "summarize count()"
```

## <a name="list-resources"></a>리소스를 이름별로 나열

이 쿼리는 모든 종류의 리소스를 반환하지만 **name**, **type** 및 **location** 속성만 반환합니다. `order by`를 사용하여 속성을 **name** 속성의 오름차순(`asc`)으로 정렬합니다.

```Query
project name, type, location
| order by name asc
```

```azurecli-interactive
az graph query -q "project name, type, location | order by name asc"
```

```azurepowershell-interactive
Search-AzGraph -Query "project name, type, location | order by name asc"
```

## <a name="show-vms"></a>이름의 내림차순으로 모든 가상 머신 나열

가상 머신(type이 `Microsoft.Compute/virtualMachines`로 지정됨)만 나열하려면 결과에서 **type** 속성만 매칭하면 됩니다. 이전 쿼리와 마찬가지로 `desc`는 `order by`를 내림차순으로 변경합니다. 형식 일치의 `=~`는 Resource Graph에 대/소문자를 구분하지 않도록 알립니다.

```Query
project name, location, type
| where type =~ 'Microsoft.Compute/virtualMachines'
| order by name desc
```

```azurecli-interactive
az graph query -q "project name, location, type| where type =~ 'Microsoft.Compute/virtualMachines' | order by name desc"
```

```azurepowershell-interactive
Search-AzGraph -Query "project name, location, type| where type =~ 'Microsoft.Compute/virtualMachines' | order by name desc"
```

## <a name="show-sorted"></a>이름 및 해당 OS 유형별로 처음 5개의 가상 머신 표시

이 쿼리는 `limit`를 사용하여 이름별로 정렬된 5개 일치 레코드만 검색합니다. Azure 리소스의 형식은 `Microsoft.Compute/virtualMachines`입니다. `project`는 Azure Resource Graph에 포함할 속성을 알려 줍니다.

```Query
where type =~ 'Microsoft.Compute/virtualMachines'
| project name, properties.storageProfile.osDisk.osType
| top 5 by name desc
```

```azurecli-interactive
az graph query -q "where type =~ 'Microsoft.Compute/virtualMachines' | project name, properties.storageProfile.osDisk.osType | top 5 by name desc"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type =~ 'Microsoft.Compute/virtualMachines' | project name, properties.storageProfile.osDisk.osType | top 5 by name desc"
```

## <a name="count-os"></a>OS 유형별 가상 머신 개수 계산

이전 쿼리를 토대로 작성되며 `Microsoft.Compute/virtualMachines` 종류의 Azure 리소스에 의해 제한되지만 반환되는 레코드 수가 더 이상 제한되지 않습니다.
대신 `summarize` 및 `count()`를 사용하여 속성(이 예제에서 `properties.storageProfile.osDisk.osType`)별로 값을 그룹화하고 집계하는 방법을 정의했습니다. 전체 개체에서 이 문자열이 나타나는 방법에 대한 예제를 보려면 [리소스 탐색 - 가상 머신 검색](../concepts/explore-resources.md#virtual-machine-discovery)을 참조하세요.

```Query
where type =~ 'Microsoft.Compute/virtualMachines'
| summarize count() by tostring(properties.storageProfile.osDisk.osType)
```

```azurecli-interactive
az graph query -q "where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by tostring(properties.storageProfile.osDisk.osType)"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by tostring(properties.storageProfile.osDisk.osType)"
```

동일한 쿼리를 작성하는 다른 방법은 속성을 `extend`한 후 쿼리 내에서 사용할 임시 이름(이 경우에는 **os**)을 부여하는 것입니다. 그런 다음 **OS**는 이전 예제에서와 같이 `summarize` 및 `count()`에서 사용됩니다.

```Query
where type =~ 'Microsoft.Compute/virtualMachines'
| extend os = properties.storageProfile.osDisk.osType
| summarize count() by tostring(os)
```

```azurecli-interactive
az graph query -q "where type =~ 'Microsoft.Compute/virtualMachines' | extend os = properties.storageProfile.osDisk.osType | summarize count() by tostring(os)"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type =~ 'Microsoft.Compute/virtualMachines' | extend os = properties.storageProfile.osDisk.osType | summarize count() by tostring(os)"
```

> [!NOTE]
> `=~`는 대/소문자를 구분하지 않는 일치를 허용하지만, 쿼리에서 속성(예: **properties.storageProfile.osDisk.osType**)을 사용하려면 대/소문자를 올바르게 사용해야 합니다. 속성의 대/소문자가 올바르지 않은 경우에도 값이 반환될 수 있지만 그룹화 또는 요약이 올바르지 않게 됩니다.

## <a name="show-storage"></a>저장소를 포함하는 리소스 표시

일치시킬 형식을 명시적으로 정의하는 대신, 이 예제에서는 단어 **storage**를 `contains`하는 Azure 리소스를 찾습니다.

```Query
where type contains 'storage' | distinct type
```

```azurecli-interactive
az graph query -q "where type contains 'storage' | distinct type"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type contains 'storage' | distinct type"
```

## <a name="list-publicip"></a>모든 공용 IP 주소 나열

이전 쿼리와 마찬가지로 **publicIPAddresses** 단어를 포함하는 형식에 해당하는 모든 항목을 찾습니다.
이 쿼리는 **properties.ipAddress**
`isnotempty`인 결과만 포함하고 **properties.ipAddress**를 반환하고, 맨 위에서 `limit`까지의 결과만 포함하도록 확장됩니다.
100. 선택한 셸에 따라 따옴표를 이스케이프해야 할 수도 있습니다.

```Query
where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress)
| project properties.ipAddress
| limit 100
```

```azurecli-interactive
az graph query -q "where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | project properties.ipAddress | limit 100"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | project properties.ipAddress | limit 100"
```

## <a name="count-resources-by-ip"></a>구독에 의해 IP 주소가 구성된 리소스 개수 계산

이전 예제 쿼리를 사용하고 `summarize` 및 `count()`를 추가하면 구성된 IP 주소를 갖는 리소스 구독별 목록을 가져올 수 있습니다.

```Query
where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress)
| summarize count () by subscriptionId
```

```azurecli-interactive
az graph query -q "where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | summarize count () by subscriptionId"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | summarize count () by subscriptionId"
```

## <a name="list-tag"></a>특정 태그 값이 있는 리소스 나열

태그와 같은 Azure 리소스 형식 이외의 속성으로 결과를 제한할 수 있습니다. 이 예제에서는 값이 **Internal**이며 태그 이름이 **Environment**인 Azure 리소스를 필터링합니다.

```Query
where tags.environment=~'internal'
| project name
```

```azurecli-interactive
az graph query -q "where tags.environment=~'internal' | project name"
```

```azurepowershell-interactive
Search-AzGraph -Query "where tags.environment=~'internal' | project name"
```

또한 리소스의 태그와 값을 지정하려면 `project` 키워드에 **tags** 속성을 추가합니다.

```Query
where tags.environment=~'internal'
| project name, tags
```

```azurecli-interactive
az graph query -q "where tags.environment=~'internal' | project name, tags"
```

```azurepowershell-interactive
Search-AzGraph -Query "where tags.environment=~'internal' | project name, tags"
```

## <a name="list-specific-tag"></a>특정 태그 값이 있는 모든 저장소 계정 나열

이전 예제의 필터 기능을 결합하고 Azure 리소스 유형을 **type** 속성으로 필터링합니다. 이 쿼리는 또한 특정 태그 이름 및 값을 사용하여 특정한 종류의 Azure 리소스에 대한 검색을 제한합니다.

```Query
where type =~ 'Microsoft.Storage/storageAccounts'
| where tags['tag with a space']=='Custom value'
```

```azurecli-interactive
az graph query -q "where type =~ 'Microsoft.Storage/storageAccounts' | where tags['tag with a space']=='Custom value'"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type =~ 'Microsoft.Storage/storageAccounts' | where tags['tag with a space']=='Custom value'"
```

> [!NOTE]
> 이 예제에서는 일치를 위해 `=~` 조건 대신 `==`을 사용합니다. `==`은 대/소문자 구분 일치입니다.

## <a name="next-steps"></a>다음 단계

- [쿼리 언어](../concepts/query-language.md)에 대해 자세히 알아보기
- [리소스 탐색](../concepts/explore-resources.md)에 대해 알아보기
- [고급 쿼리](advanced.md) 샘플 참조