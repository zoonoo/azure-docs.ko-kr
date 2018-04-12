---
title: Azure 빠른 시작 - Windows VM 만들기 PowerShell | Microsoft Docs
description: PowerShell을 사용하여 Windows 가상 머신을 만드는 방법을 빠르게 이해합니다.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: neilpeterson
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/12/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: f1ab714407bd686ac8bffb23c7ba9ec8951902ef
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2018
---
# <a name="create-a-windows-virtual-machine-with-powershell"></a>PowerShell을 사용하여 Windows 가상 머신 만들기

PowerShell 명령줄 또는 스크립트에서 Azure 리소스를 만들고 관리하는 데 Azure PowerShell 모듈이 사용됩니다. 이 빠른 시작에서는 PowerShell을 사용하여 Windows Server 2016이 실행되는 Azure Virtual Machine을 만드는 방법을 자세히 설명합니다. 배포가 완료되면 서버에 연결하고 IIS를 설치합니다.  

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

PowerShell을 로컬로 설치하고 사용하도록 선택하는 경우 이 자습서에는 Azure PowerShell 모듈 버전 5.3.0 이상이 필요합니다. ` Get-Module -ListAvailable AzureRM`을 실행하여 버전을 찾습니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-azurerm-ps)를 참조하세요. 또한 PowerShell을 로컬로 실행하는 경우 `Login-AzureRmAccount`를 실행하여 Azure와 연결해야 합니다.



## <a name="create-resource-group"></a>리소스 그룹 만들기

[New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup)을 사용하여 Azure 리소스 그룹을 만듭니다. 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다. 

```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```


## <a name="create-virtual-machine"></a>가상 컴퓨터 만들기

[New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm)을 사용하여 가상 머신을 만듭니다. 각 리소스의 이름만 제공하면, 리소스가 없는 경우 New-AzureRMVM cmdlet을 통해 만들어집니다.

이 단계를 실행할 때 자격 증명을 묻는 메시지가 나타납니다. 입력하는 값은 가상 머신에 대한 사용자 이름 및 암호로 구성됩니다.

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVM" `
    -Location "East US" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -OpenPorts 80,3389  
```

## <a name="connect-to-virtual-machine"></a>가상 머신에 연결

배포가 완료된 후 가상 머신에 대한 원격 데스크톱 연결을 만듭니다.

[Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) 명령을 사용하여 가상 머신의 공용 IP 주소를 반환합니다. 다음 단계에서 웹 연결을 테스트하기 위해 브라우저와 연결할 수 있도록 이 IP 주소를 기록해 둡니다.

```azurepowershell-interactive
Get-AzureRmPublicIpAddress -ResourceGroupName myResourceGroup | Select IpAddress
```

다음 명령을 사용하여 로컬 컴퓨터에서 가상 머신과의 원격 데스크톱 세션을 만듭니다. 해당 IP 주소를 가상 머신의 *publicIPAddress*로 바꿉니다. 가상 머신을 만들 때 사용되는 자격 증명을 묻는 메시지가 표시되면 입력합니다.

```
mstsc /v:<publicIpAddress>
```

## <a name="install-iis-via-powershell"></a>PowerShell을 통해 IIS 설치

이제 Azure VM에 로그인했으므로 IIS를 설치하고 웹 트래픽을 허용하는 로컬 방화벽 규칙을 사용하려면 PowerShell의 단일 줄을 사용할 수 있습니다. VM에서 PowerShell 프롬프트를 열고 다음 명령을 실행합니다.

```azurepowershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

## <a name="view-the-iis-welcome-page"></a>IIS 시작 페이지 보기

IIS를 설치하고 현재 포트 80이 인터넷에서 VM에 열려 있으면 사용자가 선택한 웹 브라우저를 사용하여 기본 IIS 시작 페이지를 볼 수 있습니다. 위에 설명한 *publicIpAddress*를 사용하여 기본 페이지를 방문해야 합니다. 

![IIS 기본 사이트](./media/quick-create-powershell/default-iis-website.png) 

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) 명령을 사용하여 리소스 그룹, VM 및 모든 관련된 리소스를 제거할 수 있습니다.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서 간단한 가상 머신, 네트워크 보안 그룹 규칙을 배포했으며 웹 서버를 설치했습니다. Azure 가상 머신에 대한 자세한 내용을 알아보려면 Windows VM의 자습서를 계속 진행합니다.

> [!div class="nextstepaction"]
> [Azure Windows 가상 머신 자습서](./tutorial-manage-vm.md)
