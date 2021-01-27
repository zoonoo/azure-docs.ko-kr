---
title: PowerShell을 사용 하 여 VM에 태그를 만드는 방법
description: PowerShell을 사용 하 여 가상 머신의 태그 지정에 대 한 자세한 정보
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 11/11/2020
ms.author: cynthn
ms.openlocfilehash: bf13d5c0caeb0bf31a383cd23155a6856c81c53b
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98897394"
---
# <a name="how-to-tag-a-virtual-machine-in-azure-using-powershell"></a>PowerShell을 사용 하 여 Azure에서 가상 머신에 태그를 만드는 방법

이 문서에서는 PowerShell을 사용 하 여 Azure에서 VM에 태그를 만드는 방법을 설명 합니다. 태그는 리소스 또는 리소스 그룹에 직접 배치할 수 있는 사용자 정의 키/값 쌍입니다. Azure는 현재 리소스 및 리소스 그룹당 최대 50 개의 태그를 지원 합니다. 태그를 만들 때 리소스에 배치하거나 기존 리소스에 추가할 수 있습니다. Azure CLI를 사용 하 여 가상 컴퓨터에 태그를 지정할 경우 [Azure CLI를 사용 하 여 Azure에서 가상 컴퓨터에 태그](tag-cli.md)를 표시 하는 방법을 참조 하세요.

Cmdlet을 사용 `Get-AzVM` 하 여 VM의 현재 태그 목록을 볼 수 있습니다.

```azurepowershell-interactive
Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM" | Format-List -Property Tags
```

가상 컴퓨터에 이미 태그가 포함 된 경우 모든 태그가 목록 형식으로 표시 됩니다.

태그를 추가 하려면 명령을 사용 `Set-AzResource` 합니다. PowerShell을 통해 태그를 업데이트할 때 태그가 전체적으로 업데이트 됩니다. 이미 태그가 있는 리소스에 태그를 하나 추가 하는 경우 리소스에 배치 하려는 모든 태그를 포함 해야 합니다. 다음은 PowerShell Cmdlet을 통해 리소스에 태그를 더 추가하는 방법의 예입니다.

및 속성을 사용 하 여 VM에 대 한 모든 현재 태그 `$tags` 를 변수에 `Get-AzResource` 할당 `Tags` 합니다.

```azurepowershell-interactive
$tags = (Get-AzResource -ResourceGroupName myResourceGroup -Name myVM).Tags
```

현재 태그를 보려면 변수를 입력 합니다.

```azurepowershell-interactive
$tags
```

출력은 다음과 같습니다.

```output
Key           Value
----          -----
Department    MyDepartment
Application   MyApp1
Created By    MyName
Environment   Production
```

다음 예제에서는 `Location` 값을 사용 하 여 라는 태그를 추가 합니다 `myLocation` . `+=`새 키/값 쌍을 목록에 추가 하려면를 사용 `$tags` 합니다.

```azurepowershell-interactive
$tags += @{Location="myLocation"}
```

`Set-AzResource`를 사용 하 여 VM의 *$tags* 변수에 정의 된 모든 태그를 설정 합니다.

```azurepowershell-interactive
Set-AzResource -ResourceGroupName myResourceGroup -Name myVM -ResourceType "Microsoft.Compute/VirtualMachines" -Tag $tags
```

`Get-AzResource`를 사용 하 여 리소스에 대 한 모든 태그를 표시 합니다.

```azurepowershell-interactive
(Get-AzResource -ResourceGroupName myResourceGroup -Name myVM).Tags

```

출력은 다음과 유사 하 게 표시 됩니다. 이제 새로운 태그가 포함 됩니다.

```output

Key           Value
----          -----
Department    MyDepartment
Application   MyApp1
Created By    MyName
Environment   Production
Location      MyLocation
```

### <a name="next-steps"></a>다음 단계

- Azure 리소스에 태그를 지정하는 방법에 대한 자세한 내용은 [Azure Resource Manager 개요](../azure-resource-manager/management/overview.md) 및 [태그를 사용하여 Azure 리소스 구성](../azure-resource-manager/management/tag-resources.md)을 참조하세요.
- 태그를 사용 하 여 Azure 리소스의 사용을 관리 하는 방법을 알아보려면 [Azure 청구서 이해](../cost-management-billing/understand/review-individual-bill.md)를 참조 하세요.
