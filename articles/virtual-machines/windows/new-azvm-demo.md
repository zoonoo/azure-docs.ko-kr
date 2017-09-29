---
title: "Azure Cloud Shell에서 New-AzVM cmdlet을 사용하여 Windows VM 만들기 | Microsoft Docs"
description: "Azure Cloud Shell에서 New-AzVM cmdlet을 사용하여 Windows 가상 컴퓨터를 만드는 방법을 빠르게 이해합니다."
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 09/21/2017
ms.author: cynthn
ROBOTS: NOINDEX
ms.translationtype: HT
ms.sourcegitcommit: 4f77c7a615aaf5f87c0b260321f45a4e7129f339
ms.openlocfilehash: c303235ead2af7cfaa368a5b5f00567ae44cfb86
ms.contentlocale: ko-kr
ms.lasthandoff: 09/23/2017

---

# <a name="create-a-windows-virtual-machine-with-the-new-azvm-preview-in-cloud-shell"></a>Cloud Shell에서 New-AzVM(미리 보기)을 사용하여 Windows 가상 컴퓨터 만들기 

New-AzVM(미리 보기) cmdlet은 PowerShell을 사용하여 새 VM을 만드는 간단한 방법입니다. 이 가이드는 New-AzVM cmdlet이 사전 설치된 Azure Cloud Shell에서 PowerShell을 사용하여 Windows Server 2016을 실행하는 새 Azure 가상 컴퓨터를 만드는 방법을 자세히 설명합니다. 배포가 완료되면 RDP를 사용하여 서버에 연결합니다.  

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 을 만듭니다.


[!INCLUDE [cloud-shell-powershell](../../../includes/cloud-shell-powershell.md)]

## <a name="create-the-vm"></a>VM 만들기

**New-AzVM** cmdlet을 사용하여 Azure Marketplace의 Windows Server 2016 데이터 센터 이미지 사용을 포함하는 스마트 기본값으로 VM을 만들 수 있습니다. New-AzVM만 사용할 수 있으며 리소스 이름에 대해 기본값을 사용합니다. 이 예에서 *myVM*으로 **-Name** 매개 변수를 설정합니다. cmdlet은 리소스 이름에 대한 접두사로 *myVM*을 사용하여 필요한 모든 리소스를 만듭니다. 

Cloud Shell 및 형식에서 **PowerShell**이 선택되었는지 확인합니다.

```azurepowershell-interactive
New-AzVm -Name myVM
```

이 항목의 뒷부분에서 VM에 연결할 때 사용되는 VM에 대한 사용자 이름 및 암호를 만들 것인지 묻는 메시지가 나타납니다. 암호는 12-123자 길이여야 하며 1개의 소문자, 1개의 대문자, 1개의 숫자 및 1개의 특수 문자 등 네 가지 복잡성 요구 사항 중 적어도 세 가지를 충족해야 합니다.

VM 및 연결된 리소스를 만드는 데 1분이 걸립니다. 완료되면 [Find-AzureRmResource](/powershell/module/azurerm.resources/find-azurermresource) cmdlet을 사용하여 만든 모든 리소스를 볼 수 있습니다.

```azurepowershell-interactive
Find-AzureRmResource `
    -ResourceGroupNameEquals myVMResourceGroup | Format-Table Name
```

## <a name="connect-to-the-vm"></a>VM에 연결

배포가 완료된 후 가상 컴퓨터에 대한 원격 데스크톱 연결을 만듭니다.

[Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) 명령을 사용하여 가상 컴퓨터의 공용 IP 주소를 반환합니다. 이 IP 주소를 기록해 둡니다.

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
    -ResourceGroupName myVMResourceGroup | Select IpAddress
```

로컬 컴퓨터에서 cmd 프롬프트를 열고 **mstsc** 명령을 사용하여 새 VM으로 원격 데스크톱 세션을 시작합니다. &lt;publicIPAddress&gt;를 가상 컴퓨터의 IP 주소로 바꿉니다. 대화 상자가 나타나면 VM을 만들 때 제공한 사용자 이름 및 암호를 입력합니다.

```
mstsc /v:<publicIpAddress>
```

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) 명령을 사용하여 리소스 그룹, VM 및 모든 관련된 리소스를 제거할 수 있습니다.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myVMResourceGroup
```

## <a name="next-steps"></a>다음 단계

이 항목에서는 New-AzVM을 사용하여 간단한 가상 컴퓨터를 배포한 다음 RDP를 통해 연결했습니다. Azure 가상 컴퓨터에 대한 자세한 내용을 알아보려면 Windows VM의 자습서를 계속 진행합니다.

> [!div class="nextstepaction"]
> [Azure Windows 가상 컴퓨터 자습서](./tutorial-manage-vm.md)

