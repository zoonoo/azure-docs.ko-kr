---
title: Windows VM - Azure에서 데이터 디스크 분리
description: Resource Manager 배포 모델을 사용하여 Azure의 가상 머신에서 데이터 디스크를 분리합니다.
services: virtual-machines-windows
author: cynthn
manager: gwallace
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: article
ms.date: 01/08/2020
ms.author: cynthn
ms.subservice: disks
ms.openlocfilehash: 301f3abd26f702f3f29c8833c835ba7d0e41bcaf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75834599"
---
# <a name="how-to-detach-a-data-disk-from-a-windows-virtual-machine"></a>Windows 가상 컴퓨터에서 데이터 디스크를 분리하는 방법

가상 머신에 연결된 데이터 디스크가 더 이상 필요하지 않은 경우 쉽게 분리할 수 있습니다. 디스크를 분리하면 가상 머신에서 디스크가 제거되지만, 스토리지에서는 제거되지 않습니다.

> [!WARNING]
> 디스크를 분리해도 자동으로 삭제되지 않습니다. Premium Storage를 구독하는 경우 디스크에 대한 스토리지 요금이 계속 부과됩니다. 자세한 내용은 [Premium Storage 사용 시 가격 책정 및 청구](disks-types.md#billing)를 참조하세요.

디스크에 있는 기존 데이터를 다시 사용하려는 경우 동일한 또는 다른 가상 머신에 다시 연결할 수 있습니다.

 

## <a name="detach-a-data-disk-using-powershell"></a>PowerShell을 사용하여 데이터 디스크 분리

PowerShell을 사용하여 데이터 디스크를 *작동 중* 제거(hot remove)할 수 있지만, VM에서 해당 디스크를 분리할 때 해당 디스크가 전혀 사용되고 있지 않아야 합니다.

이 예제에서는 **myResourceGroup** 리소스 그룹의 VM **myVM**에서 **myDisk**라는 디스크를 제거합니다. 먼저 [Remove-AzVMDataDisk](https://docs.microsoft.com/powershell/module/az.compute/remove-azvmdatadisk) cmdlet을 사용하여 디스크를 제거합니다. 그런 다음, [Update-AzVM](https://docs.microsoft.com/powershell/module/az.compute/update-azvm) cmdlet으로 가상 머신의 상태를 업데이트하여 데이터 디스크 제거 프로세스를 완료합니다.

```azurepowershell-interactive
$VirtualMachine = Get-AzVM `
   -ResourceGroupName "myResourceGroup" `
   -Name "myVM"
Remove-AzVMDataDisk `
   -VM $VirtualMachine `
   -Name "myDisk"
Update-AzVM `
   -ResourceGroupName "myResourceGroup" `
   -VM $VirtualMachine
```

디스크가 스토리지에 유지되지만 더 이상 가상 머신에 연결되어 있지 않습니다.

## <a name="detach-a-data-disk-using-the-portal"></a>포털을 사용하여 데이터 디스크 분리

데이터 디스크를 *핫* 제거할 수 있지만 VM에서 디스크를 분리하기 전에 디스크를 적극적으로 사용하지 않도록 할 수 있습니다.

1. 왼쪽 메뉴에서 **Virtual Machines**을 선택합니다.
1. 분리할 데이터 디스크가 있는 가상 컴퓨터를 선택합니다.
1. **설정**에서 **디스크**를 선택합니다.
1. **디스크** 창 상단에서 **편집**을 선택합니다.
1. **디스크** 창에서 분리하려는 데이터 디스크의 맨 오른쪽에 있는 **Detach**를 선택합니다.
1. 변경 내용을 저장하려면 페이지 상단에 **저장을** 선택합니다.

디스크가 스토리지에 유지되지만 더 이상 가상 머신에 연결되어 있지 않습니다.

## <a name="next-steps"></a>다음 단계

데이터 디스크를 다시 사용하려는 경우 [다른 VM에 연결](attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
