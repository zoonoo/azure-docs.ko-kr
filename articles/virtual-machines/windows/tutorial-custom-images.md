---
title: 자습서 - Azure PowerShell을 사용하여 사용자 지정 VM 이미지 만들기 | Microsoft Docs
description: 이 자습서에서는 Azure PowerShell을 사용하여 Azure에서 사용자 지정 가상 머신 이미지를 만드는 방법을 알아봅니다.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/30/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 74087a6d1ce00293c968837e72c636847081e39e
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2019
ms.locfileid: "58285985"
---
# <a name="tutorial-create-a-custom-image-of-an-azure-vm-with-azure-powershell"></a>자습서: Azure PowerShell을 사용하여 Azure VM의 사용자 지정 이미지 만들기

사용자 지정 이미지는 Marketplace 이미지와 같지만 직접 만듭니다. 사용자 지정 이미지를 사용하여 배포를 부트스트랩하고 여러 VM에서 일관성을 유지할 수 있습니다. 이 자습서에서는 PowerShell을 사용하여 Azure 가상 머신의 사용자 지정 이미지를 만듭니다. 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * VM Sysprep 및 일반화
> * 사용자 지정 이미지 만들기
> * 사용자 지정 이미지에서 VM 만들기
> * 구독에 모든 이미지 나열
> * 이미지 삭제

## <a name="before-you-begin"></a>시작하기 전에

아래 단계에서는 기존 VM을 가져와서 새 VM 인스턴스를 만드는 데 사용할 수 있는 재사용 가능 사용자 지정 이미지로 변환하는 방법을 설명합니다.

이 자습서의 예제를 완료하려면 기존 가상 머신이 있어야 합니다. 필요한 경우 이 [스크립트 샘플](../scripts/virtual-machines-windows-powershell-sample-create-vm.md)을 사용하여 가상 컴퓨터를 만들 수 있습니다. 이 자습서를 진행할 때 필요한 경우 리소스 그룹 및 VM 이름을 바꿉니다.

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell 시작

Azure Cloud Shell은 이 항목의 단계를 실행하는 데 무료로 사용할 수 있는 대화형 셸입니다. 공용 Azure 도구가 사전 설치되어 계정에서 사용하도록 구성되어 있습니다. 

Cloud Shell을 열려면 코드 블록의 오른쪽 위 모서리에 있는 **사용해 보세요**를 선택하기만 하면 됩니다. 또한 [https://shell.azure.com/powershell](https://shell.azure.com/powershell)로 이동하여 별도의 브라우저 탭에서 Cloud Shell을 시작할 수도 있습니다. **복사**를 선택하여 코드 블록을 복사하여 Cloud Shell에 붙여넣고, Enter 키를 눌러 실행합니다.

## <a name="prepare-vm"></a>VM 준비

가상 머신의 이미지를 만들려면 원본 VM을 일반화하고 할당을 취소한 다음, Azure에서 일반화된 것으로 표시하여 준비해야 합니다.

### <a name="generalize-the-windows-vm-using-sysprep"></a>Sysprep을 사용하여 Windows VM 일반화

Sysprep은 여러 정보 중에서 모든 개인 계정 정보를 제거하고 이미지로 사용할 컴퓨터를 준비합니다. Sysprep에 대한 자세한 내용은 [Sysprep 사용 방법: 소개](https://technet.microsoft.com/library/bb457073.aspx)를 참조하세요.


1. 가상 머신에 연결합니다.
2. 관리자로 명령 프롬프트 창을 엽니다. 디렉터리를 *%windir%\system32\sysprep*로 변경한 후 `sysprep.exe`를 실행합니다.
3. **시스템 준비 도구** 대화 상자에서 **시스템 OOBE(첫 실행 경험) 입력**을 선택하고 **일반화** 확인란을 선택했는지 확인합니다.
4. **종료 옵션**에서 **종료**를 선택한 다음 **확인**을 클릭합니다.
5. Sysprep이 완료되면 가상 머신을 종료합니다. **VM을 다시 시작하지 마세요**.

### <a name="deallocate-and-mark-the-vm-as-generalized"></a>할당을 취소하고 VM을 일반화된 것으로 표시

이미지를 만들려면 VM의 할당을 취소하고 Azure에서 일반화된 것으로 표시해야 합니다.

[Stop-AzVM](https://docs.microsoft.com/powershell/module/az.compute/stop-azvm)을 사용하여 VM의 할당을 취소합니다.

```azurepowershell-interactive
Stop-AzVM `
   -ResourceGroupName myResourceGroup `
   -Name myVM -Force
```

[Set-AzVm](https://docs.microsoft.com/powershell/module/az.compute/set-azvm)을 사용하여 가상 머신의 상태를 `-Generalized`으로 설정합니다. 
   
```azurepowershell-interactive
Set-AzVM `
   -ResourceGroupName myResourceGroup `
   -Name myVM -Generalized
```


## <a name="create-the-image"></a>이미지 만들기

이제 [New-AzImageConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azimageconfig) 및 [New-AzImage](https://docs.microsoft.com/powershell/module/az.compute/new-azimage)를 사용하여 VM 이미지를 만들 수 있습니다. 다음 예제에서는 *myVM*이라는 VM에서 *myImage*라는 이미지를 만듭니다.

가상 컴퓨터를 가져옵니다. 

```azurepowershell-interactive
$vm = Get-AzVM `
   -Name myVM `
   -ResourceGroupName myResourceGroup
```

이미지 구성을 만듭니다.

```azurepowershell-interactive
$image = New-AzImageConfig `
   -Location EastUS `
   -SourceVirtualMachineId $vm.ID 
```

이미지를 만듭니다.

```azurepowershell-interactive
New-AzImage `
   -Image $image `
   -ImageName myImage `
   -ResourceGroupName myResourceGroup
``` 

 
## <a name="create-vms-from-the-image"></a>이미지에서 VM 만들기

이미지가 생겼으니, 이 이미지에서 하나 이상의 새 VM을 만들 수 있습니다. 사용자 지정 이미지에서 VM을 만드는 방법은 Marketplace 이미지를 사용하여 VM을 만드는 방법과 비슷합니다. Marketplace 이미지를 사용하는 경우 이미지, 이미지 공급자, 제품, SKU 및 버전에 대한 정보를 제공해야 합니다. 동일한 리소스 그룹에 있는 경우 [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) cmdlet에 설정된 간소화된 매개 변수를 사용하여 사용자 지정 이미지의 이름을 입력해야 합니다. 

이 예제에서는 *myResourceGroup*의 *myImage*에서 *myVMfromImage*라는 VM을 만듭니다.


```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVMfromImage" `
    -ImageName "myImage" `
    -Location "East US" `
    -VirtualNetworkName "myImageVnet" `
    -SubnetName "myImageSubnet" `
    -SecurityGroupName "myImageNSG" `
    -PublicIpAddressName "myImagePIP" `
    -OpenPorts 3389
```

## <a name="image-management"></a>이미지 관리 

다음은 일반적인 관리형 이미지 작업의 예제와 PowerShell을 사용하여 완료하는 방법입니다.

이름별로 모든 이미지를 나열합니다.

```azurepowershell-interactive
$images = Get-AzResource -ResourceType Microsoft.Compute/images 
$images.name
```

이미지 삭제를 삭제합니다. 이 예제에서는 *myResourceGroup*에서 *myImage*라는 이미지를 삭제합니다.

```azurepowershell-interactive
Remove-AzImage `
    -ImageName myImage `
    -ResourceGroupName myResourceGroup
```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 사용자 지정 VM 이미지를 만들었습니다. 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * VM Sysprep 및 일반화
> * 사용자 지정 이미지 만들기
> * 사용자 지정 이미지에서 VM 만들기
> * 구독에 모든 이미지 나열
> * 이미지 삭제

다음 자습서로 이동하여 고가용성 가상 머신을 만드는 방법을 알아보세요.

> [!div class="nextstepaction"]
> [고가용성 VM 만들기](tutorial-availability-sets.md)



