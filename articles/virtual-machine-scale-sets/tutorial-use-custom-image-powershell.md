---
title: 자습서 - Azure PowerShell을 사용하여 확장 집합에서 사용자 지정 VM 이미지 사용 | Microsoft Docs
description: Azure PowerShell을 사용하여 가상 머신 확장 집합을 배포하는 데 사용할 수 있는 사용자 지정 VM 이미지를 만드는 방법을 알아봅니다.
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: bd605ac3a4dd3f878dd3d5b861374816243f3467
ms.sourcegitcommit: 1aefdf876c95bf6c07b12eb8c5fab98e92948000
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/06/2019
ms.locfileid: "66728544"
---
# <a name="tutorial-create-and-use-a-custom-image-for-virtual-machine-scale-sets-with-azure-powershell"></a>자습서: Azure PowerShell을 사용하여 가상 머신 확장 집합에 대한 사용자 지정 이미지 만들기 및 사용

확장 집합을 만들 때 VM 인스턴스 배포 시 사용할 이미지를 지정합니다. VM 인스턴스가 배포된 후 작업의 수를 줄이려면 사용자 지정 VM 이미지를 사용할 수 있습니다. 이 사용자 지정 VM 이미지에는 필요한 모든 애플리케이션 설치 또는 구성이 포함됩니다. 확장 집합에서 만들어진 모든 VM 인스턴스는 사용자 지정 VM 이미지를 사용하며, 애플리케이션 트래픽을 처리할 준비가 되어 있습니다. 이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * VM 만들기 및 사용자 지정
> * VM 프로비전 해제 및 일반화
> * 원본 VM에서 사용자 지정 VM 이미지 만들기
> * 사용자 지정 VM 이미지를 사용하는 확장 집합 배포

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 을 만듭니다.

[!INCLUDE [updated-for-az.md](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]


## <a name="create-and-configure-a-source-vm"></a>원본 VM 만들기 및 구성

>[!NOTE]
> 이 자습서는 일반화된 VM 이미지를 만들고 사용하는 과정을 안내합니다. 특수 VHD로 확장 집합 만들기는 지원되지 않습니다.

먼저 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)을 사용하여 리소스 그룹을 만든 다음, [New-AzVM](/powershell/module/az.compute/new-azvm)을 사용하여 VM을 만듭니다. 이 VM은 사용자 지정 VM 이미지에 대한 원본으로 사용됩니다. 다음 예제에서는 *myResourceGroup* 리소스 그룹에 *myCustomVM*이라는 VM을 만듭니다. 메시지가 표시되면 VM에 대한 로그온 자격 증명으로 사용할 사용자 이름과 암호를 입력합니다.

```azurepowershell-interactive
# Create a resource a group
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"

# Create a Windows Server 2016 Datacenter VM
New-AzVm `
  -ResourceGroupName "myResourceGroup" `
  -Name "myCustomVM" `
  -ImageName "Win2016Datacenter" `
  -OpenPorts 3389
```

VM에 연결하려면 다음과 같이 [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress)를 사용하여 공용 IP 주소를 나열합니다.

```azurepowershell-interactive
Get-AzPublicIpAddress -ResourceGroupName myResourceGroup | Select IpAddress
```

VM과의 원격 연결을 만듭니다. Azure Cloud Shell을 사용하는 경우 로컬 PowerShell 프롬프트 또는 원격 데스크톱 클라이언트에서 이 단계를 수행합니다. 이전 명령에서 사용자의 IP 주소를 제공합니다. 메시지가 표시되면 첫 번째 단계에서 VM을 만들 때 사용한 자격 증명을 입력합니다.

```powershell
mstsc /v:<IpAddress>
```

VM을 사용자 지정하기 위해 기본 웹 서버를 설치해 보겠습니다. 확장 집합의 VM 인스턴스가 배포되면 웹 애플리케이션을 실행하는 데 필요한 모든 패키지가 포함되어 있습니다. VM에서 로컬 PowerShell 프롬프트를 열고, 다음과 같이 [Install-WindowsFeature](/powershell/module/servermanager/install-windowsfeature)를 사용하여 IIS 웹 서버를 설치합니다.

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

사용자 지정 이미지로 사용할 VM을 준비하는 마지막 단계는 VM을 일반화하는 것입니다. Sysprep은 모든 개인 계정 정보 및 구성을 제거하고, 이후의 배포를 위해 VM을 빈 상태로 다시 설정합니다. 자세한 내용은 [Sysprep 사용 방법: 소개](https://technet.microsoft.com/library/bb457073.aspx)를 참조하세요.

VM을 일반화하려면 Sysprep을 실행하고, VM을 즉시 사용할 수 있는 환경으로 설정합니다. 완료되면 Sysprep에 VM을 종료하도록 지시합니다.

```powershell
C:\Windows\system32\sysprep\sysprep.exe /oobe /generalize /shutdown
```

Sysprep에서 프로세스를 완료하고 VM을 종료하면 VM에 대한 원격 연결이 자동으로 닫힙니다.


## <a name="create-a-custom-vm-image-from-the-source-vm"></a>원본 VM에서 사용자 지정 VM 이미지 만들기
이제 원본 VM이 IIS 웹 서버가 설치된 상태로 사용자 지정됩니다. 확장 집합에 사용할 사용자 지정 VM 이미지를 만들어 보겠습니다.

이미지를 만들려면 VM을 할당 취소해야 합니다. [Stop-AzVm](/powershell/module/az.compute/stop-azvm)을 사용하여 VM을 할당 취소합니다. 그런 다음, Azure 플랫폼에서 VM이 사용자 지정 이미지를 사용할 준비가 되었음을 인식할 수 있도록 [Set-AzVm](/powershell/module/az.compute/set-azvm)을 사용하여 VM의 상태를 일반화됨으로 설정합니다. 일반화된 VM에서만 이미지를 만들 수 있습니다.

```azurepowershell-interactive
Stop-AzVM -ResourceGroupName "myResourceGroup" -Name "myCustomVM" -Force
Set-AzVM -ResourceGroupName "myResourceGroup" -Name "myCustomVM" -Generalized
```

VM을 할당 취소하고 일반화하는 데 몇 분이 걸릴 수 있습니다.

이제 [New-AzImageConfig](/powershell/module/az.compute/new-azimageconfig) 및 [New-AzImage](/powershell/module/az.compute/new-azimage)를 사용하여 VM 이미지를 만듭니다. 다음 예제에서는 VM에서 *myImage*라는 이미지를 만듭니다.

```azurepowershell-interactive
# Get VM object
$vm = Get-AzVM -Name "myCustomVM" -ResourceGroupName "myResourceGroup"

# Create the VM image configuration based on the source VM
$image = New-AzImageConfig -Location "EastUS" -SourceVirtualMachineId $vm.ID 

# Create the custom VM image
New-AzImage -Image $image -ImageName "myImage" -ResourceGroupName "myResourceGroup"
```


## <a name="create-a-scale-set-from-the-custom-vm-image"></a>사용자 지정 VM 이미지에서 확장 집합 만들기
이제 이전 단계에서 만든 사용자 지정 VM 이미지를 정의하기 위해 `-ImageName` 매개 변수를 사용하는 [New-AzVmss](/powershell/module/az.compute/new-azvmss)를 사용하여 확장 집합을 만듭니다. 트래픽을 개별 VM 인스턴스로 배포하기 위해 부하 분산 장치도 생성됩니다. 부하 분산 장치에는 80 TCP 포트에서 트래픽을 분산할 뿐만 아니라 3389 TCP 포트의 원격 데스크톱 트래픽 및 5985 TCP 포트의 PowerShell 원격을 허용하는 규칙이 포함되어 있습니다. 메시지가 표시되면 확장 집합에서 VM 인스턴스에 대해 원하는 관리 자격 증명을 제공합니다.

```azurepowershell-interactive
New-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS" `
  -VMScaleSetName "myScaleSet" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicyMode "Automatic" `
  -ImageName "myImage"
```

확장 집합 리소스와 VM을 모두 만들고 구성하는 데 몇 분 정도 걸립니다.


## <a name="test-your-scale-set"></a>확장 집합 테스트
작동 중인 확장 집합을 확인하려면 다음과 같이 [Get-AzPublicIpAddress](/powershell/module/az.network/Get-AzPublicIpAddress)를 사용하여 부하 분산 장치의 공용 IP 주소를 가져옵니다.


```azurepowershell-interactive
Get-AzPublicIpAddress `
  -ResourceGroupName "myResourceGroup" `
  -Name "myPublicIPAddress" | Select IpAddress
```

웹 브라우저에 공용 IP 주소를 입력합니다. 기본 IIS 웹 페이지가 다음 예제와 같이 표시됩니다.

![사용자 지정 VM 이미지에서 실행된 IIS](media/tutorial-use-custom-image-powershell/default-iis-website.png)


## <a name="clean-up-resources"></a>리소스 정리
확장 집합 및 추가 리소스를 제거하려면 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup)을 사용하여 리소스 그룹 및 모든 해당 리소스를 삭제합니다. `-Force` 매개 변수는 작업을 수행하는 추가 프롬프트 없이 리소스를 삭제할 것인지 확인합니다. `-AsJob` 매개 변수는 작업이 완료될 때까지 대기하지 않고 프롬프트로 제어를 반환합니다.

```azurepowershell-interactive
Remove-AzResourceGroup -Name "myResourceGroup" -Force -AsJob
```


## <a name="next-steps"></a>다음 단계
이 자습서에서는 Azure PowerShell을 사용하여 확장 집합에 대한 사용자 지정 VM 이미지를 만들고 사용하는 방법을 알아보았습니다.

> [!div class="checklist"]
> * VM 만들기 및 사용자 지정
> * VM 프로비전 해제 및 일반화
> * 사용자 지정 VM 이미지 만들기
> * 사용자 지정 VM 이미지를 사용하는 확장 집합 배포

애플리케이션을 확장 집합에 배포하는 방법을 알아보려면 다음 자습서로 계속 진행하세요.

> [!div class="nextstepaction"]
> [확장 집합에 애플리케이션 배포](tutorial-install-apps-powershell.md)
