---
title: Azure Virtual Machines에 대한 공통 PowerShell 명령 | Microsoft Docs
description: Azure에서 Windows VM 만들기 및 관리를 시작하기 위한 공통 PowerShell 명령
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ba3839a2-f3d5-4e19-a5de-95bfb1c0e61e
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 06/01/2018
ms.author: cynthn
ms.openlocfilehash: cb9f03ab87079ba33135840e3e7599d2e8cc95e9
ms.sourcegitcommit: 17fe5fe119bdd82e011f8235283e599931fa671a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/11/2018
ms.locfileid: "42145583"
---
# <a name="common-powershell-commands-for-creating-and-managing-azure-virtual-machines"></a>Azure Virtual Machines를 만들고 관리하기 위한 공통 PowerShell 명령

이 문서에서는 Azure 구독에서 가상 머신을 만들고 관리하는 데 사용할 수 있는 몇 가지 Azure PowerShell 명령을 다룹니다.  특정 명령줄 스위치 및 옵션에 대해 자세한 도움이 필요할 경우 **Get-Help** *명령*을 사용할 수 있습니다.

최신 버전의 Azure PowerShell 설치, 구독 선택, 자신의 계정에 로그인하는 방법에 대해서는 [Azure PowerShell 설치 및 구성 방법](/powershell/azure/overview)을 참조하세요.

이 문서에 나오는 둘 이상의 명령을 실행하는 경우 다음과 같은 변수가 유용할 수 있습니다.

- $location - 가상 머신의 위치입니다. [Get-AzureRmLocation](https://docs.microsoft.com/powershell/module/azurerm.resources/get-azurermlocation)을 사용하여 사용자의 [지리적 하위 지역](https://azure.microsoft.com/regions/)을 찾을 수 있습니다.
- $myResourceGroup - 가상 컴퓨터를 포함하는 리소스 그룹의 이름입니다.
- $myVM - 가상 컴퓨터의 이름입니다.

## <a name="create-a-vm---simplified"></a>VM 만들기 - 간소화됨

| Task | 명령 |
| ---- | ------- |
| 간단한 VM 만들기 | [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) -Name $myVM <BR></BR><BR></BR> New-AzureRMVM에는 모두 단일 이름이어야 하는 *간소화된* 매개 변수의 집합이 있습니다. 이름에 대한 값은 새 VM을 만드는 데 필요한 모든 리소스에 대한 이름으로 사용됩니다. 더 지정할 수 있지만 더 이상 필요하지 않습니다.|
| 사용자 지정 이미지에서 VM 만들기 | New-AzureRmVm -ResourceGroupName $myResourceGroup -Name $myVM ImageName "myImage" -Location $location  <BR></BR><BR></BR>고유한 [관리되는 이미지](capture-image-resource.md)를 이미 만들었어야 합니다. 이미지를 사용하여 여러 동일한 VM을 만들 수 있습니다. |



## <a name="create-a-vm-configuration"></a>VM 구성 만들기

| Task | 명령 |
| ---- | ------- |
| VM 구성 만들기 |$vm = [New-AzureRmVMConfig](https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermvmconfig) -VMName $myVM -VMSize "Standard_D1_v1"<BR></BR><BR></BR>VM 구성은 VM에 대한 설정을 정의하거나 업데이트하는 데 사용 됩니다. 구성은 VM의 이름 및 [크기](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 사용하여 초기화합니다. |
| 구성 설정 추가 |$vm = [Set-AzureRmVMOperatingSystem](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmoperatingsystem) -VM $vm -Windows -ComputerName $myVM -Credential $cred -ProvisionVMAgent -EnableAutoUpdate<BR></BR><BR></BR>[자격 증명](https://technet.microsoft.com/library/hh849815.aspx)을 포함하는 운영 체제 설정은 New-AzureRmVMConfig를 사용하여 이전에 만든 구성 개체에 추가됩니다. |
| 네트워크 인터페이스 추가 |$vm = [Add-AzureRmVMNetworkInterface](https://docs.microsoft.com/powershell/module/azurerm.compute/Add-AzureRmVMNetworkInterface) -VM $vm -Id $nic.Id<BR></BR><BR></BR>VM은 가상 네트워크에서 통신하도록 [네트워크 인터페이스](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)가 있어야 합니다. 또한 기존 네트워크 인터페이스 개체를 검색하는 데 [Get-AzureRmNetworkInterface](https://docs.microsoft.com/powershell/module/azurerm.compute/add-azurermvmnetworkinterface) 를 사용할 수도 있습니다. |
| 플랫폼 이미지 지정 |$vm = [Set-AzureRmVMSourceImage](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmsourceimage) -VM $vm -PublisherName "publisher_name" -Offer "publisher_offer" -Skus "product_sku" -Version "latest"<BR></BR><BR></BR>[이미지 정보](cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)는 New-AzureRmVMConfig를 사용하여 이전에 만든 구성 개체에 추가됩니다. 이 명령에서 반환되는 개체는 플랫폼 이미지를 사용하도록 OS 디스크를 설정할 때에만 사용됩니다. |
| VM 만들기 |[New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) -ResourceGroupName $myResourceGroup -Location $location -VM $vm<BR></BR><BR></BR>모든 리소스는 [리소스 그룹](../../azure-resource-manager/powershell-azure-resource-manager.md)에서 생성됩니다. 이 명령을 실행하기 전에, New-AzureRmVMConfig, Set-AzureRmVMOperatingSystem, Set-AzureRmVMSourceImage, Add-AzureRmVMNetworkInterface, and Set-AzureRmVMOSDisk을 실행합니다. |
| VM 업데이트 |[Update-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/update-azurermvm) -ResourceGroupName $myResourceGroup -VM $vm<BR></BR><BR></BR>Get-AzureRmVM을 사용하여 현재 VM 구성을 가져오고, VM 개체에서 구성 설정을 변경한 후, 이 명령을 실행합니다. |

## <a name="get-information-about-vms"></a>VM에 대한 정보 가져오기

| Task | 명령 |
| ---- | ------- |
| 구독에서 Vm 나열 |[Get AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvm) |
| 리소스 그룹에서 Vm 나열 |Get-AzureRmVM -ResourceGroupName $myResourceGroup<BR></BR><BR></BR>구독에서 리소스 그룹 목록을 가져오려면 [Get AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/get-azurermresourcegroup)을 사용할 수 있습니다. |
| VM에 대한 정보 가져오기 |Get-AzureRmVM -ResourceGroupName $myResourceGroup -Name $myVM |

## <a name="manage-vms"></a>VM 관리
| Task | 명령 |
| --- | --- |
| VM 시작 |[Start-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/start-azurermvm) -ResourceGroupName $myResourceGroup -Name $myVM |
| VM 중지 |[Stop-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/stop-azurermvm) -ResourceGroupName $myResourceGroup -Name $myVM |
| 실행 중인 VM 다시 시작 |[Restart-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/restart-azurermvm) -ResourceGroupName $myResourceGroup -Name $myVM |
| VM 삭제 |[Remove-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/remove-azurermvm) -ResourceGroupName $myResourceGroup -Name $myVM |


## <a name="next-steps"></a>다음 단계
* [리소스 관리자 및 PowerShell을 사용하여 Windows VM 만들기](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)에서 가상 컴퓨터 만들기 기본 단계를 참조합니다.

