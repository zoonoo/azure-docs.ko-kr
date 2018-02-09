---
title: "Azure에서 가상 네트워크 만들기 - PowerShell | Microsoft Docs"
description: "PowerShell을 사용하여 가상 네트워크를 만드는 방법을 빠르게 알아봅니다. 가상 네트워크를 사용하면 다양한 유형의 Azure 리소스가 서로 개인적으로 통신할 수 있습니다."
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: 
ms.topic: 
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 01/25/2018
ms.author: jdial
ms.custom: 
ms.openlocfilehash: 091e7e6cabf325cdd9d4289e7d22e71c583d91db
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/01/2018
---
# <a name="create-a-virtual-network-using-powershell"></a>PowerShell을 사용하여 가상 네트워크 만들기

이 문서에서는 가상 네트워크를 만드는 방법을 설명합니다. 가상 네트워크를 만든 후, 가상 네트워크에 두 개의 가상 머신을 배포하고 서로 개인적으로 통신하도록 합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-powershell.md)]

PowerShell을 로컬로 설치하고 사용하도록 선택하는 경우 이 자습서에는 Azure PowerShell 모듈 버전 5.1.1 이상이 필요합니다. 설치되어 있는 버전을 확인하려면 ` Get-Module -ListAvailable AzureRM`을 실행합니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-azurerm-ps)를 참조하세요. 또한 PowerShell을 로컬로 실행하는 경우 `Login-AzureRmAccount`를 실행하여 Azure와 연결해야 합니다.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

[New-AzureRmResourceGroup](/powershell/module/AzureRM.Resources/New-AzureRmResourceGroup)을 사용하여 Azure 리소스 그룹을 만듭니다. 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다. 다음 예제에서는 *eastus* 위치에 *myResourceGroup*이라는 리소스 그룹을 만듭니다. 모든 Azure 리소스는 Azure 위치(또는 지역) 내에서 만들어집니다.

```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-a-virtual-network"></a>가상 네트워크 만들기

[New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork)를 사용하여 가상 네트워크를 만듭니다. 다음 예제에서는 *EastUS* 위치에 *myVirtualNetwork*라는 기본 가상 네트워크를 만듭니다.

```azurepowershell-interactive
$virtualNetwork = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/24
```

모든 가상 네트워크에는 하나 이상의 주소 접두사가 할당됩니다. 주소 공간은 CIDR 표기법으로 지정됩니다. 주소 공간 10.0.0.0/24는 10.0.0.0-10.0.0.254를 포함합니다. 가상 네트워크 내에는 0개 이상의 서브넷이 있습니다. 리소스는 가상 네트워크의 서브넷에 배포됩니다. 

[New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig)를 사용하여 서브넷 구성을 만듭니다. 모든 서브넷은 가상 네트워크의 주소 접두사 내에 존재하는 주소 접두사를 포함합니다. 이 예제에서는 가상 네트워크의 주소 접두사와 동일한 주소 접두사를 갖는 서브넷 구성이 만들어집니다.

```azurepowershell-interactive
$subnetConfig = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name default `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork
```

서브넷 주소 접두사는 10.0.0.0-10.0.0.254를 포함하지만, Azure가 각 서브넷에서 처음 4개 주소(0-3)와 마지막 주소를 예약하기 때문에 주소 10.0.0.4-10.0.0.254만 사용할 수 있습니다. 서브넷 주소 접두사는 가상 네트워크 주소 접두사와 동일하므로 이 가상 네트워크에는 서브넷이 하나만 존재할 수 있습니다.

[Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/Set-AzureRmVirtualNetwork)를 사용하여 가상 네트워크에 서브넷 구성을 씁니다. 그러면 서브넷이 만들어집니다.

```azurepowershell-interactive
$virtualNetwork | Set-AzureRmVirtualNetwork
```

## <a name="create-virtual-machines"></a>가상 머신 만들기

가상 네트워크를 사용하면 여러 유형의 Azure 리소스가 서로 개인적으로 통신할 수 있습니다. 가상 네트워크에 배포할 수 있는 한 가지 유형의 리소스는 가상 머신입니다. 가상 네트워크의 가상 머신 간 통신이 이후 단계에서 작동하는 방식이 유효한지 검사하고 이러한 방식을 이해할 수 있도록 가상 네트워크에 두 개의 가상 머신을 만듭니다.

[New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm)을 사용하여 가상 머신을 만듭니다. 이 단계를 실행할 때 자격 증명을 묻는 메시지가 나타납니다. 입력하는 값은 가상 머신에 대한 사용자 이름 및 암호로 구성됩니다. 가상 머신이 생성되는 위치는 가상 네트워크가 있는 동일한 위치여야 합니다. 이 문서에는 그렇게 설명되어 있지만 가상 머신을 가상 네트워크가 있는 동일한 리소스 그룹에 둘 필요는 없습니다. `-AsJob` 매개 변수를 사용하면 명령을 백그라운드에서 실행할 수 있으므로 다음 태스크를 계속 진행할 수 있습니다.

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroup" `
    -Location "East US" `
    -VirtualNetworkName "myVirtualNetwork" `
    -SubnetName "default" `
    -Name "myVm1" `
    -AsJob
```

다음 예제 출력과 비슷한 출력이 반환되며, Azure는 백그라운드에서 가상 머신 생성을 시작합니다.

```powershell
Id     Name            PSJobTypeName   State         HasMoreData     Location             Command                  
--     ----            -------------   -----         -----------     --------             -------                  
1      Long Running... AzureLongRun... Running       True            localhost            New-AzureRmVM     
```

Azure DHCP는 10.0.0.4가 *default* 서브넷에서 사용 가능한 첫 번째 주소이므로 이 주소를 가상 머신 생성 중에 자동으로 할당합니다.

두 번째 가상 머신을 만듭니다. 

```azurepowershell-interactive
New-AzureRmVm `
  -ResourceGroupName "myResourceGroup" `
  -VirtualNetworkName "myVirtualNetwork" `
  -SubnetName "default" `
  -Name "myVm2"
```
가상 머신을 만드는 데 몇 분 정도 걸립니다. 가상 머신을 만든 후 Azure는 만든 가상 컴퓨터에 대한 출력을 반환합니다. 출력에 반환되지는 않지만, Azure는 서브넷에서 다음으로 사용 가능한 주소인 *10.0.0.5*를 *myVm2* 가상 머신에 할당했습니다.

## <a name="connect-to-a-virtual-machine"></a>가상 머신에 연결

[Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) 명령을 사용하여 가상 머신의 공용 IP 주소를 반환합니다. Azure는 기본적으로 각 가상 머신에 공용 인터넷 라우팅 가능 IP 주소를 할당합니다. 공용 IP 주소는 [각 Azure 지역에 할당된 주소 풀](https://www.microsoft.com/download/details.aspx?id=41653)에서 가상 머신에 할당됩니다. Azure는 가상 머신에 할당된 공용 IP 주소를 알고 있지만, 가상 머신에서 실행되는 운영 체제는 할당된 공용 IP 주소를 인식하지 못합니다. 다음 예제에서는 *myVm1* 가상 머신의 공용 IP 주소를 반환합니다.

```azurepowershell-interactive
Get-AzureRmPublicIpAddress -Name myVm1 -ResourceGroupName myResourceGroup | Select IpAddress
```

다음 명령을 사용하여 로컬 컴퓨터에서 *myVm1* 가상 머신과의 원격 데스크톱 세션을 만듭니다. `<publicIpAddress>`를 이전 명령에서 반환된 IP 주소로 바꿉니다.

```
mstsc /v:<publicIpAddress>
```

원격 데스크톱 프로토콜(.rdp) 파일이 마들어지고 컴퓨터에 다운로드된 후 열립니다. 가상 머신을 만들 때 지정한 사용자 이름 및 암호를 입력하고 **확인**을 클릭합니다. 로그인 프로세스 중에 인증서 경고가 나타날 수 있습니다. **예** 또는 **계속**을 클릭하여 연결을 진행합니다.

## <a name="validate-communication"></a>통신 유효성 검사

Ping은 Windows 방화벽을 통과하도록 허용되지 않으므로 기본적으로 Windows 가상 머신으로의 Ping 시도는 실패합니다. *myVm1*에 대한 Ping을 허용하려면 명령 프롬프트에서 다음 명령을 입력합니다.

```
netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow
```

*myVm2*와의 통신 유효성을 검사하려면 *myVm1* 가상 머신의 명령 프롬프트에서 다음 명령을 입력합니다. 가상 머신을 만들 때 사용한 자격 증명을 제공한 후 연결을 완료합니다.

```
mstsc /v:myVm2
```

두 가상 머신이 *default* 서브넷에서 할당된 개인 IP 주소를 가지고 기본적으로 Windows 방화벽을 통해 원격 데스크톱이 열려 으므로 원격 데스크톱 연결은 성공적으로 수행됩니다. Azure는 가상 네트워크 내의 모든 호스트에 대해 DNS 이름 확인을 제공하므로 호스트 이름으로 *myVm2*에 연결할 수 있습니다. 명령 프롬프트에서 *myVm2*에서 내 *myVm1*으로 Ping합니다.

```
ping myvm1
```

이전 단계에서 *myVm1* 가상 머신의 Windows 방화벽을 통해 Ping이 수행되도록 허용했으므로 Ping은 성공적으로 수행됩니다. 인터넷으로의 아웃바운드 통신을 확인하려면 다음 명령을 입력합니다.

```
ping bing.com
```

bing.com에서 4개의 응답을 받게 됩니다. 기본적으로 가상 네트워크의 모든 가상 머신은 인터넷으로 아웃바운드 통신을 할 수 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) 명령을 사용하여 리소스 그룹 및 포함하는 모든 리소스를 제거할 수 있습니다. 원격 데스크톱 세션을 종료하고 컴퓨터에서 다음 명령을 실행하여 리소스 그룹을 삭제합니다.

```azurepowershell-interactive 
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>다음 단계

이 문서에서는 1개의 서브넷과 2개의 가상 머신이 있는 기본 가상 네트워크를 배포했습니다. 다중 서브넷으로 사용자 지정 가상 네트워크를 만들고 기본 가상 네트워크 관리 작업을 수행하는 방법을 알아보려면 사용자 지정 가상 네트워크를 만들고 관리하기 위한 자습서를 계속 진행합니다.


> [!div class="nextstepaction"]
> [사용자 지정 가상 네트워크 만들기 및 관리](virtual-networks-create-vnet-arm-pportal.md#powershell)
