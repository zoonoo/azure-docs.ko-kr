---
title: Azure Cloud Shell에서 간소화된 New-AzVM cmdlet을 사용하여 Windows VM 만들기 | Microsoft Docs
description: Azure Cloud Shell에서 간소화된 New-AzVM cmdlet을 사용하여 Windows 가상 머신을 만드는 방법을 빠르게 이해합니다.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 12/12/2017
ms.author: cynthn
ROBOTS: NOINDEX
ms.openlocfilehash: cdf7385b3686c5c3f91e66c67ab5f0758935b39f
ms.sourcegitcommit: 1aefdf876c95bf6c07b12eb8c5fab98e92948000
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/06/2019
ms.locfileid: "66730136"
---
# <a name="create-a-windows-virtual-machine-with-the-simplified-new-azvm-cmdlet-in-cloud-shell"></a>Cloud Shell에서 간소화된 New-AzVM cmdlet을 사용하여 Windows 가상 머신 만들기 

[New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) cmdlet은 PowerShell을 사용하여 새 VM을 만드는 간소화된 집합의 매개 변수를 추가했습니다. 이 항목에서는 최신 버전의 New-AzureVM cmdlet이 사전 설치된 Azure Cloud Shell에서 PowerShell을 사용하여 새 VM을 만드는 방법을 보여줍니다. 스마트 기본값을 사용하여 필요한 모든 리소스를 자동으로 만드는 간소화된 매개 변수 집합을 사용합니다. 

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]


## <a name="create-the-vm"></a>VM 만들기

[New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) cmdlet을 사용하여 Azure Marketplace의 Windows Server 2016 데이터 센터 이미지 사용을 포함하는 스마트 기본값으로 VM을 만들 수 있습니다. **-Name** 매개 변수와 함께 New-AzVM을 사용할 수 있으며 모든 리소스 이름에 해당 값을 사용합니다. 이 예에서 *myVM*으로 **-Name** 매개 변수를 설정합니다. 

Cloud Shell 및 형식에서 **PowerShell**이 선택되었는지 확인합니다.

```azurepowershell-interactive
New-AzVm -Name myVM
```

이 항목의 뒷부분에서 VM에 연결할 때 사용되는 VM에 대한 사용자 이름 및 암호를 만들 것인지 묻는 메시지가 나타납니다. 암호는 12-123자 길이여야 하며 1개의 소문자, 1개의 대문자, 1개의 숫자 및 1개의 특수 문자 등 네 가지 복잡성 요구 사항 중 적어도 세 가지를 충족해야 합니다.

VM 및 연결된 리소스를 만드는 데 1분이 걸립니다. 완료되면 [Get-AzResource](https://docs.microsoft.com/powershell/module/az.resources/get-azresource) cmdlet을 사용하여 만든 모든 리소스를 볼 수 있습니다.

```azurepowershell-interactive
Get-AzResource `
    -ResourceGroupName myVMResourceGroup | Format-Table Name
```

## <a name="connect-to-the-vm"></a>VM에 연결

배포가 완료된 후 가상 머신에 대한 원격 데스크톱 연결을 만듭니다.

[Get-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress) 명령을 사용하여 가상 머신의 공용 IP 주소를 반환합니다. 이 IP 주소를 기록해 둡니다.

```azurepowershell-interactive
Get-AzPublicIpAddress `
    -ResourceGroupName myVMResourceGroup | Select IpAddress
```

로컬 컴퓨터에서 cmd 프롬프트를 열고 **mstsc** 명령을 사용하여 새 VM으로 원격 데스크톱 세션을 시작합니다. &lt;publicIPAddress&gt;를 가상 머신의 IP 주소로 바꿉니다. 대화 상자가 나타나면 VM을 만들 때 제공한 사용자 이름 및 암호를 입력합니다.

```
mstsc /v:<publicIpAddress>
```
## <a name="specify-different-resource-names"></a>다른 리소스 이름 지정

리소스에 보다 설명적인 이름을 제공할 수 있으며 여전히 자동으로 생성할 수도 있습니다. 새 리소스 그룹을 포함하여 새 VM에 대한 여러 리소스 이름을 지정한 예제는 다음과 같습니다.

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVM" `
    -Location "East US" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -OpenPorts 3389
```

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) 명령을 사용하여 리소스 그룹, VM 및 모든 관련 리소스를 제거할 수 있습니다.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myVM
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>다음 단계

이 항목에서는 New-AzVM을 사용하여 간단한 가상 컴퓨터를 배포한 다음 RDP를 통해 연결했습니다. Azure 가상 머신에 대한 자세한 내용을 알아보려면 Windows VM의 자습서를 계속 진행합니다.

> [!div class="nextstepaction"]
> [Azure Windows 가상 머신 자습서](./tutorial-manage-vm.md)
