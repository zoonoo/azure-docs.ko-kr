---
title: DNS 영역 및 레코드 보호-Azure DNS
description: 이 학습 경로에서 dns Microsoft Azure dns 영역 및 레코드 집합 보호를 시작 합니다.
services: dns
author: asudbring
ms.service: dns
ms.topic: how-to
ms.date: 2/20/2020
ms.author: allensu
ms.openlocfilehash: b06ae396ae15c8572cf8160ce576651f47001add
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87920503"
---
# <a name="how-to-protect-dns-zones-and-records"></a>DNS 영역 및 레코드를 보호하는 방법

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

DNS 영역 및 레코드는 중요한 리소스입니다. DNS 영역 또는 단일 DNS 레코드를 삭제 하면 서비스가 중단 될 수 있습니다. DNS 영역 및 레코드를 무단 또는 실수로 변경 하지 않도록 보호 하는 것이 중요 합니다.

이 문서에서는 이러한 변경 내용 으로부터 개인 DNS 영역 및 레코드를 보호 하는 Azure DNS 방법을 설명 합니다.  [Azure 역할 기반 액세스 제어 (AZURE RBAC)](../role-based-access-control/overview.md) 및 [리소스 잠금](../azure-resource-manager/management/lock-resources.md)Azure Resource Manager에서 제공 하는 강력한 두 가지 강력한 기능을 적용 합니다.

## <a name="role-based-access-control"></a>역할 기반 액세스 제어

Azure RBAC (역할 기반 액세스 제어)를 사용 하면 Azure 사용자, 그룹 및 리소스에 대 한 세밀 한 액세스 관리를 지원 합니다. RBAC를 사용 하면 사용자에 게 필요한 액세스 수준을 부여할 수 있습니다. RBAC를 사용 하 여 액세스를 관리 하는 방법에 대 한 자세한 내용은 [azure 역할 기반 액세스 제어 란? (AZURE RBAC)](../role-based-access-control/overview.md)을 참조 하세요.

### <a name="the-dns-zone-contributor-role"></a>DNS 영역 기여자 역할

DNS 영역 참가자 역할은 개인 DNS 리소스를 관리 하기 위한 기본 제공 역할입니다. 사용자 또는 그룹에 적용 된이 역할은 DNS 리소스를 관리할 수 있도록 합니다.

리소스 그룹 *Myresourcegroup* 에는 Contoso Corporation에 대 한 5 개의 영역이 포함 되어 있습니다. 해당 리소스 그룹에 DNS 영역 기여자 DNS 관리자 권한을 부여할 경우 해당 DNS 영역을 완전히 관리할 수 있습니다. 불필요 한 권한 부여를 방지 합니다. DNS 관리자가 가상 컴퓨터를 만들거나 중지할 수 없습니다.

RBAC 권한을 할당하는 가장 간단한 방법은 [Azure Portal을 사용](../role-based-access-control/role-assignments-portal.md)하는 것입니다.  

리소스 그룹에 대 한 **액세스 제어 (IAM)** 를 열고 **추가**를 선택한 다음 **DNS 영역 참가자** 역할을 선택 합니다. 필요한 사용자 또는 그룹을 선택 하 여 사용 권한을 부여 합니다.

![Azure Portal을 통한 리소스 그룹 수준 RBAC](./media/dns-protect-zones-recordsets/rbac1.png)

권한은 [Azure PowerShell을 사용하여 부여](../role-based-access-control/role-assignments-powershell.md)할 수도 있습니다.

```azurepowershell
# Grant 'DNS Zone Contributor' permissions to all zones in a resource group

$usr = "<user email address>"
$rol = "DNS Zone Contributor"
$rsg = "<resource group name>"

New-AzRoleAssignment -SignInName $usr -RoleDefinitionName $rol -ResourceGroupName $rsg
```

동일한 명령을 [Azure CLI를 통해 사용](../role-based-access-control/role-assignments-cli.md)할 수도 있습니다.

```azurecli
# Grant 'DNS Zone Contributor' permissions to all zones in a resource group

az role assignment create \
--assignee "<user email address>" \
--role "DNS Zone Contributor" \
--resource-group "<resource group name>"
```

### <a name="zone-level-rbac"></a>영역 수준 RBAC

구독, 리소스 그룹 또는 개별 리소스에 Azure RBAC 규칙을 적용할 수 있습니다. 해당 리소스는 개별 DNS 영역 또는 개별 레코드 집합이 될 수 있습니다.

예를 들어 리소스 그룹 *Myresourcegroup* 에는 영역 *contoso.com* 및 하위 영역 *customers.contoso.com*포함 됩니다. CNAME 레코드는 각 고객 계정에 대해 생성 됩니다. CNAME 레코드를 관리 하는 데 사용 되는 관리자 계정에는 *customers.contoso.com* 영역에 레코드를 만들 수 있는 권한이 할당 됩니다. 이 계정은 *customers.contoso.com* 관리할 수 있습니다.

영역 수준 RBAC 권한은 Azure Portal을 통해 부여할 수 있습니다.  영역에 대 한 **액세스 제어 (IAM)** 를 열고 **추가**를 선택한 다음 **DNS 영역 참가자** 역할을 선택 하 고 사용 권한을 부여 하는 데 필요한 사용자 또는 그룹을 선택 합니다.

![Azure Portal을 통한 DNS 영역 수준 RBAC](./media/dns-protect-zones-recordsets/rbac2.png)

권한은 [Azure PowerShell을 사용하여 부여](../role-based-access-control/role-assignments-powershell.md)할 수도 있습니다.

```azurepowershell
# Grant 'DNS Zone Contributor' permissions to a specific zone

$usr = "<user email address>"
$rol = "DNS Zone Contributor"
$rsg = "<resource group name>"
$zon = "<zone name>"
$typ = "Microsoft.Network/DNSZones"

New-AzRoleAssignment -SignInName $usr -RoleDefinitionName $rol -ResourceGroupName $rsg -ResourceName $zon -ResourceType $typ
```

동일한 명령을 [Azure CLI를 통해 사용](../role-based-access-control/role-assignments-cli.md)할 수도 있습니다.

```azurecli
# Grant 'DNS Zone Contributor' permissions to a specific zone

az role assignment create \
--assignee <user email address> \
--role "DNS Zone Contributor" \
--scope "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/DnsZones/<zone name>/"
```

### <a name="record-set-level-rbac"></a>레코드 집합 수준 RBAC

사용 권한은 레코드 집합 수준에서 적용 됩니다.  사용자에 게 필요한 항목에 대 한 제어 권한이 부여 되 고 다른 변경 작업은 수행할 수 없습니다.

레코드 집합 수준 RBAC 권한은 Azure Portal를 통해 구성할 수 있으며, 레코드 집합 페이지의 **Access Control (IAM)** 단추를 사용 하 여 구성할 수 있습니다.

![Azure Portal을 통한 레코드 집합 수준 RBAC](./media/dns-protect-zones-recordsets/rbac3.png)

레코드 집합 수준 RBAC 권한은 [Azure PowerShell을 사용하여 부여](../role-based-access-control/role-assignments-powershell.md)할 수도 있습니다.

```azurepowershell
# Grant permissions to a specific record set

$usr = "<user email address>"
$rol = "DNS Zone Contributor"
$sco = 
"/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/dnszones/<zone name>/<record type>/<record name>"

New-AzRoleAssignment -SignInName $usr -RoleDefinitionName $rol -Scope $sco
```

동일한 명령을 [Azure CLI를 통해 사용](../role-based-access-control/role-assignments-cli.md)할 수도 있습니다.

```azurecli
# Grant permissions to a specific record set

az role assignment create \
--assignee "<user email address>" \
--role "DNS Zone Contributor" \
--scope "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/dnszones/<zone name>/<record type>/<record name>"
```

### <a name="custom-roles"></a>사용자 지정 역할

기본 제공 DNS 영역 기여자 역할을 사용하면 DNS 리소스를 완벽히 관리할 수 있습니다. 사용자 고유의 사용자 지정 Azure 역할을 빌드하여 세부적인 제어를 제공할 수 있습니다.

CNAMEs를 관리 하는 데 사용 되는 계정에는 CNAME 레코드만 관리할 수 있는 권한이 부여 됩니다. 계정이 다른 형식의 레코드를 수정할 수 없습니다. 계정이 영역 삭제와 같은 영역 수준 작업을 수행할 수 없습니다.

다음 예는 CNAME 레코드 관리에 대한 사용자 지정 역할 정의를 보여줍니다.

```json
{
    "Name": "DNS CNAME Contributor",
    "Id": "",
    "IsCustom": true,
    "Description": "Can manage DNS CNAME records only.",
    "Actions": [
        "Microsoft.Network/dnsZones/CNAME/*",
        "Microsoft.Network/dnsZones/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
    ],
    "NotActions": [
    ],
    "AssignableScopes": [
        "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ]
}
```

작업 속성은 다음과 같은 DNS별 권한을 정의합니다.

* `Microsoft.Network/dnsZones/CNAME/*`은 CNAME 레코드에 대한 모든 권한을 부여합니다.
* `Microsoft.Network/dnsZones/read`는 DNS 영역을 읽을 권한을 부여하지만 수정 권한은 부여하지 않아 CNAME이 생성되는 영역을 확인할 수 있습니다.

나머지 작업은 [DNS 영역 참가자의 기본 역할](../role-based-access-control/built-in-roles.md#dns-zone-contributor)에서 복사됩니다.

> [!NOTE]
> Azure 사용자 지정 역할을 사용 하 여 레코드 집합을 업데이트 하도록 허용 하는 동안 레코드 집합 삭제를 방지 하는 것은 효과적인 제어가 아닙니다. 이 방식에서는 레코드 집합을 삭제할 수 없지만 수정은 방지할 수 없습니다.  허용되는 수정 작업에는 레코드 집합을 비우기 위해 레코드를 모두 제거하는 작업을 포함하여 레코드 집합에서 레코드를 추가 및 제거하는 작업이 포함됩니다. 이 경우 DNS 확인 관점에서 레코드 집합을 삭제하는 것과 같은 효과를 가집니다.

현재 Azure Portal를 통해 사용자 지정 역할 정의를 정의할 수 없습니다. 이 역할 정의를 기준으로 하는 사용자 지정 역할은 Azure PowerShell을 사용하여 만들 수 있습니다.

```azurepowershell
# Create new role definition based on input file
New-AzRoleDefinition -InputFile <file path>
```

Azure CLI를 통해 만들 수도 있습니다.

```azurecli
# Create new role definition based on input file
az role create -inputfile <file path>
```

그런 다음 이 문서의 앞부분에서 설명한 대로 역할을 기본 역할과 동일한 방식으로 할당할 수 있습니다.

사용자 지정 역할을 만들고, 관리 하 고, 할당 하는 방법에 대 한 자세한 내용은 [AZURE RBAC의 사용자 지정 역할](../role-based-access-control/custom-roles.md)을 참조 하세요.

## <a name="resource-locks"></a>리소스 잠금

Azure Resource Manager는 리소스를 잠글 수 있는 다른 유형의 보안 제어를 지원 합니다. 리소스 잠금은 리소스에 적용 되 고 모든 사용자 및 역할에서 적용 됩니다. 자세한 내용은 [Azure Resource Manager를 사용 하 여 리소스 잠그기](../azure-resource-manager/management/lock-resources.md)를 참조 하세요.

리소스 잠금의 두 가지 유형은 **Cannotdelete** 및 **ReadOnly**입니다. 이러한 잠금 유형은 사설 DNS 영역 또는 개별 레코드 집합에 적용 될 수 있습니다. 다음 섹션에서는 몇 가지 일반적인 시나리오와 리소스 잠금을 사용하여 지원하는 방법에 대해 설명합니다.

### <a name="protecting-against-all-changes"></a>모든 변경으로부터 보호

변경을 방지 하려면 영역에 ReadOnly 잠금을 적용 합니다. 이 잠금은 새 레코드 집합을 만들지 못하도록 하며 기존 레코드 집합을 수정 또는 삭제 하지 못하도록 합니다.

Azure Portal을 통해 영역 수준의 리소스 잠금을 만들 수 있습니다.  DNS 영역 페이지에서 **잠금**을 선택한 다음, **+추가**를 선택합니다.

![Azure Portal을 통한 영역 수준 리소스 잠금](./media/dns-protect-zones-recordsets/locks1.png)

[Azure PowerShell](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcelock?view=latest)를 통해 영역 수준 리소스 잠금을 만들 수도 있습니다.

```azurepowershell
# Lock a DNS zone

$lvl = "<lock level>"
$lnm = "<lock name>"
$rsc = "<zone name>"
$rty = "Microsoft.Network/DNSZones"
$rsg = "<resource group name>"

New-AzResourceLock -LockLevel $lvl -LockName $lnm -ResourceName $rsc -ResourceType $rty -ResourceGroupName $rsg
```

동일한 명령을 [Azure CLI를 통해 사용](https://docs.microsoft.com/cli/azure/lock?view=azure-cli-latest#az-lock-create)할 수도 있습니다.

```azurecli
# Lock a DNS zone

az lock create \
--lock-type "<lock level>" \
--name "<lock name>" \
--resource-name "<zone name>" \
--namespace "Microsoft.Network" \
--resource-type "DnsZones" \
--resource-group "<resource group name>"
```

### <a name="protecting-individual-records"></a>개별 레코드 보호

기존 DNS 레코드 집합이 수정되지 않도록 하려면 레코드 집합에 ReadOnly 잠금을 적용합니다.

> [!NOTE]
> 레코드 집합에 CanNotDelete 잠금을 적용하는 것은 효과적인 관리 방법이 아닙니다. 이 방식에서는 레코드 집합을 삭제할 수 없지만 수정은 방지할 수 없습니다.  허용되는 수정 작업에는 레코드 집합을 비우기 위해 레코드를 모두 제거하는 작업을 포함하여 레코드 집합에서 레코드를 추가 및 제거하는 작업이 포함됩니다. 이 경우 DNS 확인 관점에서 레코드 집합을 삭제하는 것과 같은 효과를 가집니다.

레코드 집합 수준 리소스 잠금은 현재 Azure PowerShell을 사용해야 구성할 수 있으며  Azure Portal 또는 Azure CLI에서 지원 되지 않습니다.

```azurepowershell
# Lock a DNS record set

$lvl = "<lock level>"
$lnm = "<lock name>"
$rsc = "<zone name>/<record set name>"
$rty = "Microsoft.Network/DNSZones/<record type>"
$rsg = "<resource group name>"

New-AzResourceLock -LockLevel $lvl -LockName $lnm -ResourceName $rsc -ResourceType $rty -ResourceGroupName $rsg
```

### <a name="protecting-against-zone-deletion"></a>영역 삭제로부터 보호

Azure DNS에서 영역을 삭제 하면 영역에 있는 모든 레코드 집합이 삭제 됩니다.  이 작업은 실행 취소할 수 없습니다. 중요 영역을 실수로 삭제할 경우 비즈니스에 심각한 영향을 미칩니다.  실수로 영역을 삭제 하지 않도록 보호 하는 것이 중요 합니다.

영역에 CanNotDelete 잠금을 적용할 경우 영역이 삭제되지 않습니다. 잠금은 자식 리소스에 의해 상속 됩니다. 잠금은 영역에 있는 모든 레코드 집합이 삭제 되는 것을 방지 합니다. 위의 참고에 설명 된 것 처럼 기존 레코드 집합에서 레코드를 제거할 수 있으므로이는 유용 하지 않습니다.

또는 SOA 레코드 집합과 같은 영역에 있는 레코드 집합에 CanNotDelete 잠금을 적용 합니다. 영역을 삭제 하지 않은 경우에도 레코드 집합을 삭제 합니다. 이 잠금은 영역 삭제를 방지 하지만 영역 내의 레코드 집합을 자유롭게 수정할 수 있습니다. 영역을 삭제 하려고 하면 Azure Resource Manager이 제거를 검색 합니다. 또한 제거 하면 soa가 잠기기 때문에 호출을 차단 하는 Azure Resource Manager SOA 레코드 집합도 삭제 됩니다.  삭제되는 레코드 집합이 없습니다.

다음 PowerShell 명령은 지정된 영역의 SOA 레코드에 대해 CanNotDelete 잠금을 만듭니다.

```azurepowershell
# Protect against zone delete with CanNotDelete lock on the record set

$lvl = "CanNotDelete"
$lnm = "<lock name>"
$rsc = "<zone name>/@"
$rty = "Microsoft.Network/DNSZones/SOA"
$rsg = "<resource group name>"

New-AzResourceLock -LockLevel $lvl -LockName $lnm -ResourceName $rsc -ResourceType $rty -ResourceGroupName $rsg
```

실수로 인 한 영역 삭제를 방지 하는 또 다른 옵션은 사용자 지정 역할을 사용 하는 것입니다. 이 역할은 영역을 관리 하는 데 사용 되는 계정에 영역 삭제 권한이 없도록 합니다. 

영역을 삭제 해야 할 경우 다음 두 단계를 삭제할 수 있습니다.

 - 먼저 영역 삭제 권한을 부여 합니다.
 - 둘째, 영역을 삭제할 수 있는 권한을 부여 합니다.

사용자 지정 역할은 해당 계정에서 액세스 하는 모든 영역에 대해 작동 합니다. 구독 소유자와 같이 영역 삭제 권한이 있는 계정은 여전히 영역을 실수로 삭제할 수 있습니다.

DNS 영역 보호에 대 한 심층 방어 접근 방법으로 리소스 잠금 및 사용자 지정 역할을 모두 동시에 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* RBAC를 사용하는 방법은 [Azure Portal에서 액세스 관리 시작](../role-based-access-control/overview.md)을 참조하세요.
* 리소스 잠금에 대한 자세한 내용은[ Azure Resource Manager를 사용하여 리소스 잠그기](../azure-resource-manager/management/lock-resources.md)를 참조하세요.
