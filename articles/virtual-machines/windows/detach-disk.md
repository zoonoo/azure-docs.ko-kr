---
title: "Windows VM에서 데이터 디스크 분리 - Azure| Microsoft Docs"
description: "Resource Manager 배포 모델을 사용하여 Azure의 가상 컴퓨터에서 데이터 디스크를 분리하는 방법을 알아봅니다."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 13180343-ac49-4a3a-85d8-0ead95e2028c
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/21/2017
ms.author: cynthn
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 97aa69745d200ee76f9f859eb3a8b0ad2f202bad
ms.contentlocale: ko-kr
ms.lasthandoff: 08/21/2017

---
# <a name="how-to-detach-a-data-disk-from-a-windows-virtual-machine"></a>Windows 가상 컴퓨터에서 데이터 디스크를 분리하는 방법
가상 컴퓨터에 연결된 데이터 디스크가 더 이상 필요하지 않은 경우 쉽게 분리할 수 있습니다. 디스크를 분리하면 가상 컴퓨터에서 디스크가 제거되지만, 저장소에서는 제거되지 않습니다.

> [!WARNING]
> 디스크를 분리해도 자동으로 삭제되지 않습니다. 프리미엄 저장소를 구독하는 경우 디스크에 대한 저장소 요금이 계속 부과됩니다. 자세한 내용은 [프리미엄 저장소 사용 시 가격 책정 및 청구](../../storage/common/storage-premium-storage.md#pricing-and-billing)를 참조하세요.
>
>

디스크에 있는 기존 데이터를 다시 사용하려는 경우 동일한 또는 다른 가상 컴퓨터에 다시 연결할 수 있습니다.

## <a name="detach-a-data-disk-using-the-portal"></a>포털을 사용하여 데이터 디스크 분리
1. 포털 허브에서 **가상 컴퓨터**를 선택합니다.
2. 분리할 데이터 디스크가 있는 가상 컴퓨터를 선택하고 **중지**를 클릭하여 VM을 할당 취소합니다.
3. 가상 컴퓨터 블레이드에서 **디스크**를 선택합니다.
4. **디스크** 블레이드 상단에서 **편집**을 선택합니다.
5. **디스크** 블레이드에서 분리할 데이터 디스크의 맨 오른쪽에 있는 ![분리 단추 이미지](./media/detach-disk/detach.png) 분리 단추를 클릭합니다.
5. 디스크를 제거한 후에 블레이드 상단에서 저장을 클릭합니다.
6. 가상 컴퓨터 블레이드에서 **개요**를 클릭한 다음 블레이드 상단에서 **시작** 단추를 클릭하여 VM을 다시 시작합니다.



디스크가 저장소에 유지되지만 더 이상 가상 컴퓨터에 연결되어 있지 않습니다.

## <a name="detach-a-data-disk-using-powershell"></a>PowerShell을 사용하여 데이터 디스크 분리
이 예제에서 첫 번째 명령은 Get-AzureRmVM cmdlet을 사용하여 **RG11** 리소스 그룹에 가상 컴퓨터 **MyVM07**을 가져옵니다. 이 명령은 가상 컴퓨터를 **$VirtualMachine** 변수에 저장합니다.

두 번째 명령은 가상 컴퓨터에서 DataDisk3이라는 데이터 디스크를 제거합니다.

마지막 명령은 가상 컴퓨터의 상태를 업데이트하여 데이터 디스크 제거 프로세스를 완료합니다.

```powershell
$VirtualMachine = Get-AzureRmVM -ResourceGroupName "RG11" -Name "MyVM07"
Remove-AzureRmVMDataDisk -VM $VirtualMachine -Name "DataDisk3"
Update-AzureRmVM -ResourceGroupName "RG11" -Name "MyVM07" -VM $VirtualMachine
```

자세한 내용은 [Remove-AzureRmVMDataDisk](/powershell/module/azurerm.compute/remove-azurermvmdatadisk)를 참조하세요.

## <a name="next-steps"></a>다음 단계
데이터 디스크를 다시 사용하려는 경우 [다른 VM에 연결](attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)


