---
title: 고급 쿼리 샘플
description: Azure Resource Graph를 사용하여 가상 머신 확장 집합 용량, 사용된 모든 태그 나열 및 정규식과 일치하는 가상 머신을 비롯한 일부 고급 쿼리를 실행합니다.
author: DCtheGeek
ms.author: dacoulte
ms.date: 10/18/2019
ms.topic: quickstart
ms.service: resource-graph
ms.openlocfilehash: 8b848eac338260340cb9160a72f33294e51101f2
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387568"
---
# <a name="advanced-resource-graph-queries"></a>고급 Resource Graph 쿼리

Azure Resource Graph를 사용하는 쿼리를 이해하는 첫 번째 단계는 [쿼리 언어](../concepts/query-language.md)를 기본적으로 이해하는 것입니다. 아직 [Azure Data Explorer](../../../data-explorer/data-explorer-overview.md)에 익숙하지 않은 경우에는 기본 사항을 검토하여 원하는 리소스에 대한 요청을 작성하는 방법을 이해하는 것이 좋습니다.

다음 고급 쿼리를 살펴보겠습니다.

> [!div class="checklist"]
> - [가상 머신 확장 집합 용량 및 크기 가져오기](#vmss-capacity)
> - [결과에서 열 제거](#remove-column)
> - [모든 태그 이름 나열](#list-all-tags)
> - [정규식과 일치하는 가상 머신](#vm-regex)
> - [특정 쓰기 위치를 사용하여 Cosmos DB 나열](#mvexpand-cosmosdb)
> - [구독 이름이 있는 키 자격 증명 모음](#join)
> - [SQL Database 및 탄력적 풀 나열](#join-sql)
> - [네트워크 인터페이스 및 공용 IP를 사용하여 가상 머신 나열](#join-vmpip)
> - [리소스 그룹에서 특정 태그를 사용하여 스토리지 계정 찾기](#join-findstoragetag)
> - [두 쿼리의 결과를 단일 결과로 결합](#unionresults)
> - [DisplayNames를 사용한 테넌트 및 구독 이름 포함](#displaynames)

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free)을 만듭니다.

## <a name="language-support"></a>언어 지원

Azure CLI(확장을 통해) 및 Azure PowerShell(모듈을 통해)은 Azure Resource Graph를 지원합니다. 다음 쿼리를 실행하기 전에 사용자 환경이 준비되었는지 확인합니다. 선택한 셸 환경의 설치 및 유효성 검사 단계에 대해서는 [Azure CLI](../first-query-azurecli.md#add-the-resource-graph-extension) 및 [Azure PowerShell](../first-query-powershell.md#add-the-resource-graph-module)을 참조하세요.

## <a name="vmss-capacity"></a>가상 머신 확장 집합 용량 및 크기 가져오기

이 쿼리는 가상 머신 확장 집합 리소스를 찾고 가상 머신 크기 및 확장 집합의 용량을 포함하는 다양한 세부 정보를 가져옵니다. 이 쿼리는 `toint()` 함수를 사용하여 정렬할 수 있도록 용량을 숫자로 캐스팅합니다. 마지막으로, 열의 이름이 사용자 지정 명명 속성으로 바뀝니다.

```kusto
Resources
| where type=~ 'microsoft.compute/virtualmachinescalesets'
| where name contains 'contoso'
| project subscriptionId, name, location, resourceGroup, Capacity = toint(sku.capacity), Tier = sku.name
| order by Capacity desc
```

```azurecli-interactive
az graph query -q "Resources | where type=~ 'microsoft.compute/virtualmachinescalesets' | where name contains 'contoso' | project subscriptionId, name, location, resourceGroup, Capacity = toint(sku.capacity), Tier = sku.name | order by Capacity desc"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type=~ 'microsoft.compute/virtualmachinescalesets' | where name contains 'contoso' | project subscriptionId, name, location, resourceGroup, Capacity = toint(sku.capacity), Tier = sku.name | order by Capacity desc"
```

## <a name="remove-column"></a>결과에서 열 제거

다음 쿼리에서는 `summarize`를 사용하여 구독별로 리소스를 계산하고, `join`을 사용하여 _ResourceContainers_ 테이블의 구독 세부 정보와 결합한 다음, `project-away`를 사용하여 일부 열을 제거합니다.

```kusto
Resources
| summarize resourceCount=count() by subscriptionId
| join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId
| project-away subscriptionId, subscriptionId1
```

```azurecli-interactive
az graph query -q "Resources | summarize resourceCount=count() by subscriptionId | join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId| project-away subscriptionId, subscriptionId1"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | summarize resourceCount=count() by subscriptionId | join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId| project-away subscriptionId, subscriptionId1"
```

## <a name="list-all-tags"></a>모든 태그 이름 나열

이 쿼리는 태그를 사용하여 시작하고 모든 고유한 태그 이름 및 해당 형식을 나열하는 JSON 개체를 빌드합니다.

```kusto
Resources
| project tags
| summarize buildschema(tags)
```

```azurecli-interactive
az graph query -q "Resources | project tags | summarize buildschema(tags)"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project tags | summarize buildschema(tags)"
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
Resources
| where type =~ 'microsoft.compute/virtualmachines' and name matches regex @'^Contoso(.*)[0-9]+$'
| project name
| order by name asc
```

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.compute/virtualmachines' and name matches regex @'^Contoso(.*)[0-9]+$' | project name | order by name asc"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.compute/virtualmachines' and name matches regex @'^Contoso(.*)[0-9]+$' | project name | order by name asc"
```

## <a name="mvexpand-cosmosdb"></a>특정 쓰기 위치를 사용하여 Cosmos DB 나열

다음 쿼리는 Cosmos DB 리소스를 제한하고, `mv-expand`를 사용하여 **properties.writeLocations**와 프로젝트 관련 필드에 대한 속성 모음을 차례로 확장한 다음, '미국 동부' 또는 '미국 서부'와 일치하는 **properties.writeLocations.locationName** 값으로 결과를 제한합니다.

```kusto
Resources
| where type =~ 'microsoft.documentdb/databaseaccounts'
| project id, name, writeLocations = (properties.writeLocations)
| mv-expand writeLocations
| project id, name, writeLocation = tostring(writeLocations.locationName)
| where writeLocation in ('East US', 'West US')
| summarize by id, name
```

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.documentdb/databaseaccounts' | project id, name, writeLocations = (properties.writeLocations) | mv-expand writeLocations | project id, name, writeLocation = tostring(writeLocations.locationName) | where writeLocation in ('East US', 'West US') | summarize by id, name"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.documentdb/databaseaccounts' | project id, name, writeLocations = (properties.writeLocations) | mv-expand writeLocations | project id, name, writeLocation = tostring(writeLocations.locationName) | where writeLocation in ('East US', 'West US') | summarize by id, name"
```

## <a name="join"></a>구독 이름이 있는 키 자격 증명 모음

다음 쿼리는 `join`의 복잡한 사용을 보여 줍니다. 이 쿼리는 조인된 테이블을 구독 리소스로 제한하고 `project`를 사용하여 원래 필드 _subscriptionId_ 및 _SubName_으로 이름이 바뀐 _name_ 필드만 포함합니다. 필드 이름 바꾸기는 필드가 _리소스_에 이미 있으므로 `join`이 _name1_로 추가하는 것을 방지합니다. 원래 테이블은 `where`를 사용하여 필터링되고 다음 `project`에는 두 테이블의 열이 포함됩니다. 쿼리 결과는 유형, 키 자격 증명 모음 이름 및 해당하는 구독 이름을 표시하는 단일 키 자격 증명 모음입니다.

```kusto
Resources
| join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId
| where type == 'microsoft.keyvault/vaults'
| project type, name, SubName
| limit 1
```

```azurecli-interactive
az graph query -q "Resources | join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId | where type == 'microsoft.keyvault/vaults' | project type, name, SubName| limit 1"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId | where type == 'microsoft.keyvault/vaults' | project type, name, SubName| limit 1"
```

## <a name="join-sql"></a>SQL Database 및 탄력적 풀 나열

다음 쿼리에서는 **leftouter** `join`를 사용하여 SQL Database 리소스와 관련된 탄력적 풀(있는 경우)을 함께 가져옵니다.

```kusto
Resources
| where type =~ 'microsoft.sql/servers/databases'
| project databaseId = id, databaseName = name, elasticPoolId = tolower(tostring(properties.elasticPoolId))
| join kind=leftouter (
    Resources
    | where type =~ 'microsoft.sql/servers/elasticpools'
    | project elasticPoolId = tolower(id), elasticPoolName = name, elasticPoolState = properties.state)
on elasticPoolId
| project-away elasticPoolId1
```

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.sql/servers/databases' | project databaseId = id, databaseName = name, elasticPoolId = tolower(tostring(properties.elasticPoolId)) | join kind=leftouter ( Resources | where type =~ 'microsoft.sql/servers/elasticpools' | project elasticPoolId = tolower(id), elasticPoolName = name, elasticPoolState = properties.state) on elasticPoolId | project-away elasticPoolId1"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.sql/servers/databases' | project databaseId = id, databaseName = name, elasticPoolId = tolower(tostring(properties.elasticPoolId)) | join kind=leftouter ( Resources | where type =~ 'microsoft.sql/servers/elasticpools' | project elasticPoolId = tolower(id), elasticPoolName = name, elasticPoolState = properties.state) on elasticPoolId | project-away elasticPoolId1"
```

## <a name="join-vmpip"></a>네트워크 인터페이스 및 공용 IP를 사용하여 가상 머신 나열

이 쿼리는 두 개의 **leftouter** `join` 명령을 사용하여 가상 머신, 해당 네트워크 인터페이스 및 해당 네트워크 인터페이스와 관련된 공용 IP 주소를 함께 가져옵니다.

```kusto
Resources
| where type =~ 'microsoft.compute/virtualmachines'
| extend nics=array_length(properties.networkProfile.networkInterfaces) 
| mvexpand nic=properties.networkProfile.networkInterfaces 
| where nics == 1 or nic.properties.primary =~ 'true' or isempty(nic) 
| project vmId = id, vmName = name, vmSize=tostring(properties.hardwareProfile.vmSize), nicId = tostring(nic.id) 
| join kind=leftouter (
    Resources
    | where type =~ 'microsoft.network/networkinterfaces'
    | extend ipConfigsCount=array_length(properties.ipConfigurations) 
    | mvexpand ipconfig=properties.ipConfigurations 
    | where ipConfigsCount == 1 or ipconfig.properties.primary =~ 'true'
    | project nicId = id, publicIpId = tostring(ipconfig.properties.publicIPAddress.id))
on nicId
| project-away nicId1
| summarize by vmId, vmName, vmSize, nicId, publicIpId
| join kind=leftouter (
    Resources
    | where type =~ 'microsoft.network/publicipaddresses'
    | project publicIpId = id, publicIpAddress = properties.ipAddress)
on publicIpId
| project-away publicIpId1
```

```azurecli-interactive
azure graph query -q "Resources | where type =~ 'microsoft.compute/virtualmachines' | extend nics=array_length(properties.networkProfile.networkInterfaces) | mvexpand nic=properties.networkProfile.networkInterfaces | where nics == 1 or nic.properties.primary =~ 'true' or isempty(nic) | project vmId = id, vmName = name, vmSize=tostring(properties.hardwareProfile.vmSize), nicId = tostring(nic.id) | join kind=leftouter ( Resources | where type =~ 'microsoft.network/networkinterfaces' | extend ipConfigsCount=array_length(properties.ipConfigurations) | mvexpand ipconfig=properties.ipConfigurations | where ipConfigsCount == 1 or ipconfig.properties.primary =~ 'true' | project nicId = id, publicIpId = tostring(ipconfig.properties.publicIPAddress.id)) on nicId | project-away nicId1 | summarize by vmId, vmName, vmSize, nicId, publicIpId | join kind=leftouter ( Resources | where type =~ 'microsoft.network/publicipaddresses' | project publicIpId = id, publicIpAddress = properties.ipAddress) on publicIpId | project-away publicIpId1"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.compute/virtualmachines' | extend nics=array_length(properties.networkProfile.networkInterfaces) | mvexpand nic=properties.networkProfile.networkInterfaces | where nics == 1 or nic.properties.primary =~ 'true' or isempty(nic) | project vmId = id, vmName = name, vmSize=tostring(properties.hardwareProfile.vmSize), nicId = tostring(nic.id) | join kind=leftouter ( Resources | where type =~ 'microsoft.network/networkinterfaces' | extend ipConfigsCount=array_length(properties.ipConfigurations) | mvexpand ipconfig=properties.ipConfigurations | where ipConfigsCount == 1 or ipconfig.properties.primary =~ 'true' | project nicId = id, publicIpId = tostring(ipconfig.properties.publicIPAddress.id)) on nicId | project-away nicId1 | summarize by vmId, vmName, vmSize, nicId, publicIpId | join kind=leftouter ( Resources | where type =~ 'microsoft.network/publicipaddresses' | project publicIpId = id, publicIpAddress = properties.ipAddress) on publicIpId | project-away publicIpId1"
```

## <a name="join-findstoragetag"></a>리소스 그룹에서 특정 태그를 사용하여 스토리지 계정 찾기

다음 쿼리는 **innerunique** `join`를 사용하여 스토리지 계정을 지정된 태그 이름 및 태그 값을 가진 리소스 그룹에 연결합니다.

```kusto
Resources
| where type =~ 'microsoft.storage/storageaccounts'
| join kind=innerunique (
    ResourceContainers
    | where type =~ 'microsoft.resources/subscriptions/resourcegroups'
    | where tags['key1'] == 'value1'
    | project subscriptionId, resourceGroup)
on subscriptionId, resourceGroup
| project-away subscriptionId1, resourceGroup1
```

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.storage/storageaccounts' | join kind=innerunique ( ResourceContainers | where type =~ 'microsoft.resources/subscriptions/resourcegroups' | where tags['key1'] == 'value1' | project subscriptionId, resourceGroup) on subscriptionId, resourceGroup | project-away subscriptionId1, resourceGroup1"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.storage/storageaccounts' | join kind=innerunique ( ResourceContainers | where type =~ 'microsoft.resources/subscriptions/resourcegroups' | where tags['key1'] == 'value1' | project subscriptionId, resourceGroup) on subscriptionId, resourceGroup | project-away subscriptionId1, resourceGroup1"
```

## <a name="unionresults"></a>두 쿼리의 결과를 단일 결과로 결합

다음 쿼리에서는 `union`을 사용하여 _ResourceContainers_ 테이블에서 결과를 가져오고 _Resources_ 테이블의 결과에 추가합니다.

```kusto
ResourceContainers
| where type=='microsoft.resources/subscriptions/resourcegroups' | project name, type  | limit 5
| union  (Resources | project name, type | limit 5)
```

```azurecli-interactive
az graph query -q "ResourceContainers | where type=='microsoft.resources/subscriptions/resourcegroups' | project name, type  | limit 5 | union  (Resources | project name, type | limit 5)"
```

```azurepowershell-interactive
Search-AzGraph -Query "ResourceContainers | where type=='microsoft.resources/subscriptions/resourcegroups' | project name, type  | limit 5 | union  (Resources | project name, type | limit 5)"
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