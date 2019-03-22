---
title: 논리적 조직을 위한 Azure 리소스에 태그 지정 | Microsoft Docs
description: 태그를 적용하여 대금 청구 및 관리를 위해 Azure 리소스를 구성하는 방법을 보여 줍니다.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 003a78e5-2ff8-4685-93b4-e94d6fb8ed5b
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: AzurePortal
ms.devlang: na
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: tomfitz
ms.openlocfilehash: bc89b8d830e364c70fef5a5959d4da85c517a5ee
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/12/2019
ms.locfileid: "57764936"
---
# <a name="use-tags-to-organize-your-azure-resources"></a>태그를 사용하여 Azure 리소스 구성

[!INCLUDE [resource-manager-governance-tags](../../includes/resource-manager-governance-tags.md)]

리소스에 태그를 적용하려면 사용자에게 해당 리소스 유형에 대한 쓰기 액세스 권한이 있어야 합니다. 모든 리소스 유형에 태그를 적용하려면 [기여자](../role-based-access-control/built-in-roles.md#contributor) 역할을 사용합니다. 하나의 리소스 형식에만 태그를 적용하려면 해당 리소스에 대한 기여자 역할을 사용합니다. 예를 들어, 가상 머신에 태그를 적용하려면 [가상 머신 기여자](../role-based-access-control/built-in-roles.md#virtual-machine-contributor)를 사용합니다.

[!INCLUDE [Handle personal data](../../includes/gdpr-intro-sentence.md)]

## <a name="policies"></a>정책

[Azure Policy](../governance/policy/overview.md)를 사용하여 태그 지정 규칙을 적용할 수 있습니다. 정책을 만들어 조직에 대해 예상되는 태그를 준수하지 않는 리소스의 시나리오가 구독에 배포되지 않도록 합니다. 수동으로 태그를 적용하거나 준수하지 않는 리소스를 검색하는 대신 배포 중에 필요한 태그를 자동으로 적용하는 정책을 만들 수 있습니다. 다음 섹션에서 태그에 대한 예제 정책을 보여줍니다.

[!INCLUDE [Tag policies](../../includes/azure-policy-samples-general-tags.md)]

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

*리소스 그룹*에 대한 기존 태그를 보려면 다음을 사용합니다.

```azurepowershell-interactive
(Get-AzResourceGroup -Name examplegroup).Tags
```

그러면 스크립트가 다음 형식을 반환합니다.

```powershell
Name                           Value
----                           -----
Dept                           IT
Environment                    Test
```

*지정된 리소스 ID를 포함한 리소스*에 대한 기존 태그를 보려면 다음을 사용합니다.

```azurepowershell-interactive
(Get-AzResource -ResourceId /subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Storage/storageAccounts/<storage-name>).Tags
```

또는 *지정된 이름 및 리소스 그룹을 포함한 리소스*에 대한 기존 태그를 보려면 다음을 사용합니다.

```azurepowershell-interactive
(Get-AzResource -ResourceName examplevnet -ResourceGroupName examplegroup).Tags
```

*특정 태그가 있는 리소스 그룹*을 가져오려면 다음을 사용합니다.

```azurepowershell-interactive
(Get-AzResourceGroup -Tag @{ Dept="Finance" }).ResourceGroupName
```

*특정 태그가 있는 리소스*를 가져오려면 다음을 사용합니다.

```azurepowershell-interactive
(Get-AzResource -Tag @{ Dept="Finance"}).Name
```

*특정 태그 이름이 있는 리소스*를 가져오려면 다음을 사용합니다.

```azurepowershell-interactive
(Get-AzResource -TagName Dept).Name
```

리소스 또는 리소스 그룹에 태그를 적용할 때마다 해당 리소스 또는 리소스 그룹의 기존 태그가 덮어써집니다. 따라서 리소스 또는 리소스 그룹에 기존 태그가 있는지에 따라 다른 방법을 사용해야 합니다.

*기존 태그를 포함하지 않는 리소스 그룹*에 태그를 추가하려면 다음을 사용합니다.

```azurepowershell-interactive
Set-AzResourceGroup -Name examplegroup -Tag @{ Dept="IT"; Environment="Test" }
```

*기존 태그를 포함하는 리소스 그룹*에 태그를 추가하려면 기존 태그를 검색하고, 새 태그를 추가하고, 태그를 다시 적용합니다.

```azurepowershell-interactive
$tags = (Get-AzResourceGroup -Name examplegroup).Tags
$tags.Add("Status", "Approved")
Set-AzResourceGroup -Tag $tags -Name examplegroup
```

*기존 태그를 포함하지 않는 리소스*에 태그를 추가하려면 다음을 사용합니다.

```azurepowershell-interactive
$r = Get-AzResource -ResourceName examplevnet -ResourceGroupName examplegroup
Set-AzResource -Tag @{ Dept="IT"; Environment="Test" } -ResourceId $r.ResourceId -Force
```

*기존 태그를 포함하는 리소스*에 태그를 추가하려면 다음을 사용합니다.

```azurepowershell-interactive
$r = Get-AzResource -ResourceName examplevnet -ResourceGroupName examplegroup
$r.Tags.Add("Status", "Approved")
Set-AzResource -Tag $r.Tags -ResourceId $r.ResourceId -Force
```

리소스에 리소스 그룹의 모든 태그를 적용 하려면 및 *리소스를 기존 태그를 유지 하지 않도록*, 다음 스크립트를 사용 합니다.

```azurepowershell-interactive
$groups = Get-AzResourceGroup
foreach ($g in $groups)
{
    Get-AzResource -ResourceGroupName $g.ResourceGroupName | ForEach-Object {Set-AzResource -ResourceId $_.ResourceId -Tag $g.Tags -Force }
}
```

리소스에 리소스 그룹의 모든 태그를 적용 하려면 및 *중복 되지 않는 리소스를 기존 태그를 유지*, 다음 스크립트를 사용 합니다.

```azurepowershell-interactive
$group = Get-AzResourceGroup "examplegroup"
if ($null -ne $group.Tags) {
    $resources = Get-AzResource -ResourceGroupName $group.ResourceGroupName
    foreach ($r in $resources)
    {
        $resourcetags = (Get-AzResource -ResourceId $r.ResourceId).Tags
        if ($resourcetags)
        {
            foreach ($key in $group.Tags.Keys)
            {
                if (-not($resourcetags.ContainsKey($key)))
                {
                    $resourcetags.Add($key, $group.Tags[$key])
                }
            }
            Set-AzResource -Tag $resourcetags -ResourceId $r.ResourceId -Force
        }
        else
        {
            Set-AzResource -Tag $group.Tags -ResourceId $r.ResourceId -Force
        }
    }
}
```

모든 태그를 제거하려면 빈 해시 테이블을 전달합니다.

```azurepowershell-interactive
Set-AzResourceGroup -Tag @{} -Name examplegroup
```

## <a name="azure-cli"></a>Azure CLI

*리소스 그룹*에 대한 기존 태그를 보려면 다음을 사용합니다.

```azurecli
az group show -n examplegroup --query tags
```

그러면 스크립트가 다음 형식을 반환합니다.

```json
{
  "Dept"        : "IT",
  "Environment" : "Test"
}
```

또는 *지정된 이름, 형식 및 리소스 그룹을 포함한 리소스*에 대한 기존 태그를 보려면 다음을 사용합니다.

```azurecli
az resource show -n examplevnet -g examplegroup --resource-type "Microsoft.Network/virtualNetworks" --query tags
```

리소스 컬렉션을 반복할 때 리소스 ID별로 리소스를 표시하려고 할 수 있습니다. 전체 예제는 이 문서의 뒷부분에 나와 있습니다. *지정된 리소스 ID를 포함한 리소스*에 대한 기존 태그를 보려면 다음을 사용합니다.

```azurecli
az resource show --id <resource-id> --query tags
```

특정 태그가 있는 리소스 그룹을 가져오려면 `az group list`를 사용합니다.

```azurecli
az group list --tag Dept=IT
```

특정 태그 및 값이 있는 모든 리소스를 가져오려면 `az resource list`를 사용합니다.

```azurecli
az resource list --tag Dept=Finance
```

리소스 또는 리소스 그룹에 태그를 적용할 때마다 해당 리소스 또는 리소스 그룹의 기존 태그가 덮어써집니다. 따라서 리소스 또는 리소스 그룹에 기존 태그가 있는지에 따라 다른 방법을 사용해야 합니다.

*기존 태그를 포함하지 않는 리소스 그룹*에 태그를 추가하려면 다음을 사용합니다.

```azurecli
az group update -n examplegroup --set tags.Environment=Test tags.Dept=IT
```

*기존 태그를 포함하지 않는 리소스*에 태그를 추가하려면 다음을 사용합니다.

```azurecli
az resource tag --tags Dept=IT Environment=Test -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks"
```

태그가 이미 있는 리소스에 태그를 추가하려면 기존 태그를 검색하고, 해당 값의 서식을 다시 지정한 후 기존 태그와 새 태그를 다시 적용합니다.

```azurecli
jsonrtag=$(az resource show -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks" --query tags)
rt=$(echo $jsonrtag | tr -d '"{},' | sed 's/: /=/g')
az resource tag --tags $rt Project=Redesign -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks"
```

리소스에 리소스 그룹의 모든 태그를 적용 하려면 및 *리소스를 기존 태그를 유지 하지 않도록*, 다음 스크립트를 사용 합니다.

```azurecli
groups=$(az group list --query [].name --output tsv)
for rg in $groups
do
  jsontag=$(az group show -n $rg --query tags)
  t=$(echo $jsontag | tr -d '"{},' | sed 's/: /=/g')
  r=$(az resource list -g $rg --query [].id --output tsv)
  for resid in $r
  do
    az resource tag --tags $t --id $resid
  done
done
```

리소스에 리소스 그룹의 모든 태그를 적용 하려면 및 *리소스의 기존 태그를 유지*, 다음 스크립트를 사용 합니다.

```azurecli
groups=$(az group list --query [].name --output tsv)
for rg in $groups
do
  jsontag=$(az group show -n $rg --query tags)
  t=$(echo $jsontag | tr -d '"{},' | sed 's/: /=/g')
  r=$(az resource list -g $rg --query [].id --output tsv)
  for resid in $r
  do
    jsonrtag=$(az resource show --id $resid --query tags)
    rt=$(echo $jsonrtag | tr -d '"{},' | sed 's/: /=/g')
    az resource tag --tags $t$rt --id $resid
  done
done
```

## <a name="templates"></a>템플릿

[!INCLUDE [resource-manager-tags-in-templates](../../includes/resource-manager-tags-in-templates.md)]

## <a name="portal"></a>포털

[!INCLUDE [resource-manager-tag-resource](../../includes/resource-manager-tag-resources.md)]

## <a name="rest-api"></a>REST API

Azure Portal 및 PowerShell 둘 다 백그라운드에서 [Resource Manager REST API](https://docs.microsoft.com/rest/api/resources/) 를 사용합니다. 태그를 다른 환경으로 통합해야 하는 경우 리소스 ID에 대해 **GET**을 사용하여 태그를 가져온 다음 **PATCH** 호출을 사용하여 태그 집합을 업데이트할 수 있습니다.

## <a name="tags-and-billing"></a>태그 및 청구

태그를 사용하여 청구 데이터를 그룹화할 수 있습니다. 예를 들어 서로 다른 조직에 여러 VM을 실행하는 경우 태그를 사용하여 비용 센터별로 사용량을 그룹화합니다. 또한 프로덕션 환경에서 실행 중인 VM에 대한 청구 사용량과 같이 런타임 환경별로 비용을 분류하는 데 태그를 사용할 수도 있습니다.

[Azure 리소스 사용 및 RateCard API](../billing/billing-usage-rate-card-overview.md) 또는 사용 CSV(쉼표로 구분된 값) 파일을 통해 태그에 대한 정보를 검색할 수 있습니다. [Azure 계정 센터](https://account.azure.com/Subscriptions) 또는 Azure Portal에서 사용량 파일을 다운로드할 수 있습니다. 자세한 내용은 [Azure 청구서 및 일간 사용량 데이터 다운로드 또는 보기](../billing/billing-download-azure-invoice-daily-usage-date.md)를 참조하세요. Azure 계정 센터에서 사용량 파일을 다운로드하는 경우 **버전 2**를 선택합니다. 대금 청구에 태그를 지원하는 서비스의 경우 **태그** 열에 태그가 나타납니다.

REST API 작업에 대한 내용은 [Azure 청구 REST API 참조](/rest/api/billing/)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* 일부 리소스 유형은 태그를 지원하지 않습니다. 리소스 유형에 태그를 적용할 수 있는지 확인하려면 [Azure 리소스에 대한 태그 지원](tag-support.md)을 참조하세요.
* 포털 사용에 대한 소개는 [Azure Portal을 사용하여 Azure 리소스 관리](manage-resource-groups-portal.md)를 참조하세요.  
