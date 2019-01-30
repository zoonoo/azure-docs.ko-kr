---
title: Azure 가상 머신 라우팅 문제 진단 | Microsoft Docs
description: 가상 머신에 대한 유효 경로을 보고 가상 머신 라우팅 문제를 진단하는 방법을 알아봅니다.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/30/2018
ms.author: jdial
ms.openlocfilehash: 56dd13f5c2c0db4af65d8bc5d4ee5c072a161964
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/22/2019
ms.locfileid: "54429637"
---
# <a name="diagnose-a-virtual-machine-routing-problem"></a>가상 머신 라우팅 문제 진단

이 아티클에서는 VM(가상 머신)에서 네트워크 인터페이스에 유효 경로를 보고 라우팅 문제를 진단하는 방법을 설명합니다. Azure에서는 각 가상 네트워크 서브넷에 여러 기본 경로를 만듭니다. 경로 테이블에서 경로를 정의한 다음, 서브넷에 경로 테이블을 연결하여 Azure의 기본 경로를 재정의할 수 있습니다. 가상 네트워크가 온-프레미스 네트워크에 연결된 경우 BGP(경계 게이트웨이 프로토콜)를 통해 만든 경로, Azure의 기본 경로 및 Azure VPN Gateway를 통해 온-프레미스 네트워크에서 전파한 모든 경로의 조합은 서브넷에 있는 모든 네트워크 인터페이스에 유효 경로입니다. 가상 네트워크, 네트워크 인터페이스 또는 라우팅 개념을 잘 모르는 경우 [가상 네트워크 개요](virtual-networks-overview.md), [네트워크 인터페이스](virtual-network-network-interface.md) 및 [라우팅 개요](virtual-networks-udr-overview.md)를 참조하세요.

## <a name="scenario"></a>시나리오

VM에 연결하려고 하지만 연결에 실패합니다. VM에 액세스할 수 없는 이유를 확인하기 위해 Azure [Portal](#diagnose-using-azure-portal), [PowerShell](#diagnose-using-powershell) 또는 [Azure CLI](#diagnose-using-azure-cli)를 사용하여 네트워크 인터페이스에 대한 유효 경로를 볼 수 있습니다.

다음 단계에서는 유효 경로를 볼 수 있는 기존 VM이 있다고 가정합니다. 기존 VM이 없는 경우 먼저 [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 또는 [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) VM을 배포하여 이 아티클의 작업을 완료합니다. 이 아티클에 있는 예제는 *myVMVMNic*라는 네트워크 인터페이스가 있는 *myVM*이라는 VM에 대한 것입니다. VM 및 네트워크 인터페이스는 *myResourceGroup*이라는 리소스 그룹에 있고, *미국 동부* 영역에 있습니다. 해당 단계에서 문제를 진단하는 VM에 대해 적절히 값을 변경합니다.

## <a name="diagnose-using-azure-portal"></a>Azure Portal을 사용하여 진단

1. [필요한 권한](virtual-network-network-interface.md#permissions)이 있는 Azure 계정으로 Azure [Portal](https://portal.azure.com)에 로그인합니다.
2. Azure Portal 맨 위에 있는 검색 상자에 실행 중 상태인 VM의 이름을 입력합니다. 검색 결과에 VM의 이름이 나타나면 선택합니다.
3. 다음 그림에 표시된 대로 **문제 진단 및 해결**을 선택한 다음, **권장 단계** 아래에서 7항목의 **유효 경로**를 선택합니다.

    ![유효한 경로 보기](./media/diagnose-network-routing-problem/view-effective-routes.png)

4. **myVMVMNic**라는 네트워크 인터페이스의 유효 경로는 다음 그림과 같습니다.

     ![유효한 경로 보기](./media/diagnose-network-routing-problem/effective-routes.png)

    VM에 연결된 여러 네트워크 인터페이스가 있는 경우 선택하여 모든 네트워크 인터페이스에 대한 유효 경로를 볼 수 있습니다. 각 네트워크 인터페이스가 다른 서브넷에 있을 수 있으므로 각 네트워크 인터페이스의 유효 경로는 다를 수 있습니다.

    이전 그림에 표시된 예제에서 나열된 경로는 각 서브넷에 대해 Azure에서 만든 기본 경로입니다. 목록에는 적어도 하나의 경로가 있지만 추가 경로가 있을 수 있습니다. 기능에 따라 다른 가상 네트워크와 피어링되거나 Azure VPN Gateway를 통해 온-프레미스 네트워크에 연결된 가상 네트워크에서 사용하도록 설정할 수 있습니다. 각 경로 및 네트워크 인터페이스에 표시될 수 있는 다른 경로에 대해 자세히 알아보려면 [가상 네트워크 트래픽 라우팅](virtual-networks-udr-overview.md)을 참조하세요. 목록에 경로가 많은 경우 경로 목록을 사용하여 .csv 파일을 다운로드하기 위해 **다운로드**를 선택하는 것이 용이합니다.

이전 단계에서 유효 경로는 VM을 통해 표시되지만 다음을 통해서도 유효 경로를 볼 수 있습니다.
- **개별 네트워크 인터페이스**: [네트워크 인터페이스를 보는](virtual-network-network-interface.md#view-network-interface-settings) 방법을 알아봅니다.
- **개별 경로 테이블**: [경로 테이블을 보는](manage-route-table.md#view-details-of-a-route-table) 방법을 알아봅니다.

## <a name="diagnose-using-powershell"></a>PowerShell을 사용하여 진단

[Azure Cloud Shell](https://shell.azure.com/powershell) 뒤에 오는 명령 또는 컴퓨터에서 PowerShell을 사용하여 실행할 수 있습니다. Azure Cloud Shell은 무료 대화형 셸입니다. 공용 Azure 도구가 사전 설치되어 계정에서 사용하도록 구성되어 있습니다. 컴퓨터에서 PowerShell을 실행하는 경우 *AzureRM* PowerShell 모듈 버전 6.0.1 이상이 필요합니다. 컴퓨터에서 `Get-Module -ListAvailable AzureRM`을 실행하여 설치된 버전을 확인합니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/azurerm/install-azurerm-ps)를 참조하세요. PowerShell을 로컬로 실행 중인 경우 `Login-AzureRmAccount`를 실행하여 [필요한 권한](virtual-network-network-interface.md#permissions)을 가진 계정으로 Azure에 로그인해야 합니다.

[Get-AzureRmEffectiveRouteTable](/powershell/module/azurerm.network/get-azurermeffectiveroutetable)을 사용하여 네트워크 인터페이스에 대한 유효 경로를 가져옵니다. 다음 예제에서는 *myResourceGroup*이라는 리소스 그룹에 있는 *myVMVMNic*라는 네트워크 인터페이스에 대한 유효 경로를 가져옵니다.

```azurepowershell-interactive
Get-AzureRmEffectiveRouteTable `
  -NetworkInterfaceName myVMVMNic `
  -ResourceGroupName myResourceGroup `
  | Format-Table
```

출력에 반환되는 정보를 이해하려면 [라우팅 개요](virtual-networks-udr-overview.md)를 참조하세요. 출력은 VM이 실행 중 상태인 경우에만 반환됩니다. VM에 연결된 여러 네트워크 인터페이스가 있는 경우 각 네트워크 인터페이스에 대한 유효 경로를 볼 수 있습니다. 각 네트워크 인터페이스가 다른 서브넷에 있을 수 있으므로 각 네트워크 인터페이스의 유효 경로는 다를 수 있습니다. 여전히 통신 문제가 있는 경우 [추가 진단](#additional-diagnosis) 및 [고려 사항](#considerations)을 참조하세요.

네트워크 인터페이스의 이름을 알지 못하지만 네트워크 인터페이스가 연결된 VM의 이름을 아는 경우 다음 명령은 VM에 연결된 모든 네트워크 인터페이스의 ID를 반환합니다.

```azurepowershell-interactive
$VM = Get-AzureRmVM -Name myVM `
  -ResourceGroupName myResourceGroup
$VM.NetworkProfile
```

다음 예제와 유사한 출력이 표시됩니다.

```powershell
NetworkInterfaces
-----------------
{/subscriptions/<ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic
```

이전 출력에서 네트워크 인터페이스 이름은 *myVMVMNic*입니다.

## <a name="diagnose-using-azure-cli"></a>Azure CLI를 사용하여 진단

[Azure Cloud Shell](https://shell.azure.com/bash) 뒤에 오는 명령 또는 컴퓨터에서 CLI를 사용하여 실행할 수 있습니다. 이 아티클에서는 Azure CLI 버전 2.0.32 이상이 필요합니다. 설치되어 있는 버전을 확인하려면 `az --version`을 실행합니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요. Azure CLI를 로컬로 실행 중인 경우 `az login`를 실행하고 [필요한 권한](virtual-network-network-interface.md#permissions)을 가진 계정으로 Azure에 로그인해야 합니다.

[az network nic show-effective-route-table](/cli/azure/network/nic#az-network-nic-show-effective-route-table)을 사용하여 네트워크 인터페이스에 대한 유효 경로를 가져옵니다. 다음 예제에서는 *myResourceGroup*이라는 리소스 그룹에 있는 *myVMVMNic*라는 네트워크 인터페이스에 대한 유효 경로를 가져옵니다.

```azurecli-interactive
az network nic show-effective-route-table \
  --name myVMVMNic \
  --resource-group myResourceGroup
```

출력에 반환되는 정보를 이해하려면 [라우팅 개요](virtual-networks-udr-overview.md)를 참조하세요. 출력은 VM이 실행 중 상태인 경우에만 반환됩니다. VM에 연결된 여러 네트워크 인터페이스가 있는 경우 각 네트워크 인터페이스에 대한 유효 경로를 볼 수 있습니다. 각 네트워크 인터페이스가 다른 서브넷에 있을 수 있으므로 각 네트워크 인터페이스의 유효 경로는 다를 수 있습니다. 여전히 통신 문제가 있는 경우 [추가 진단](#additional-diagnosis) 및 [고려 사항](#considerations)을 참조하세요.

네트워크 인터페이스의 이름을 알지 못하지만 네트워크 인터페이스가 연결된 VM의 이름을 아는 경우 다음 명령은 VM에 연결된 모든 네트워크 인터페이스의 ID를 반환합니다.

```azurecli-interactive
az vm show \
  --name myVM \
  --resource-group myResourceGroup
```

## <a name="resolve-a-problem"></a>문제 해결

라우팅 문제를 해결하는 작업은 일반적으로 다음으로 구성됩니다.

- 사용자 지정 경로를 추가하여 Azure의 기본 경로를 재정의합니다. 자세한 내용은 [사용자 지정 경로를 추가하는](manage-route-table.md#create-a-route) 방법을 알아봅니다.
- 원하지 않는 위치에 경로를 만들 수 있는 사용자 지정 경로를 변경하거나 제거합니다. 사용자 지정 경로를 [변경](manage-route-table.md#change-a-route) 또는 [삭제](manage-route-table.md#delete-a-route)하는 방법을 알아봅니다.
- 사용자 지정 경로를 포함하는 경로 테이블이 네트워크 인터페이스가 있는 서브넷에 연결되었는지 확인합니다. [서브넷에 경로 테이블을 연결하는](manage-route-table.md#associate-a-route-table-to-a-subnet) 방법을 알아봅니다.
- 배포한 Azure VPN Gateway 또는 네트워크 가상 어플라이언스가 작동할 수 있는지 확인합니다. Network Watcher의 [VPN 진단](../network-watcher/diagnose-communication-problem-between-networks.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 기능을 사용하여 Azure VPN Gateway에 발생한 문제를 확인합니다.

여전히 통신 문제가 있는 경우 [고려 사항](#considerations) 및 [추가 진단](#additional-dignosis)을 참조하세요.

## <a name="considerations"></a>고려 사항

통신 문제를 해결하려고 할 때 다음 사항을 고려합니다.

- 라우팅은 정의한 경로, BGP(Border Gateway Protocol) 및 시스템 경로 중 LPM(접두사 최대 길이 일치)에 기반합니다. 동일한 LPM 일치가 포함된 경로가 두 개 이상 있으면 [경로 개요](virtual-networks-udr-overview.md#how-azure-selects-a-route)에 나열된 순서대로 해당 원점에 따라 경로가 선택됩니다. 유효 경로를 사용하면 사용 가능한 모든 경로를 기준으로 LPM 일치에 해당하는 유효 경로만 표시될 수 있습니다. 네트워크 인터페이스에 대해 경로가 평가되는 방법이 표시되면 VM으로부터 연결에 영향을 미칠 수 있는 특정 경로 문제를 훨씬 더 쉽게 해결할 수 있습니다.
- *VirtualAppliance*를 다음 홉 형식으로 사용하여 NVA(네트워크 가상 어플라이언스)에 대한 사용자 지정 경로를 정의하는 경우 트래픽을 수신하는 NVA에 IP 전달이 사용되도록 설정되어 있는지 확인합니다. 그렇지 않으면 패킷이 삭제됩니다. [네트워크 인터페이스에 IP 전달을 사용](virtual-network-network-interface.md#enable-or-disable-ip-forwarding)하는 방법에 대해 자세히 알아봅니다. 또한 운영 체제 또는 NVA 내의 애플리케이션은 네트워크 트래픽을 전달하고 작업을 수행하도록 구성되어야 합니다.
- 0.0.0.0/0에 대한 경로를 만든 경우 모든 아웃바운드 인터넷 트래픽은 NVA 또는 VPN Gateway 등 사용자가 지정한 다음 홉으로 라우팅됩니다. 이러한 경로 생성을 흔히 강제 터널링이라고 합니다. 다음 홉이 트래픽을 처리하는 방법에 따라 인터넷에서 VM으로 RDP 또는 SSH 프로토콜을 사용하는 원격 연결은 이 경로에서 작동하지 않을 수 있습니다. 다음과 같이 강제 터널링을 사용하도록 설정할 수 있습니다.
    - 사이트 간 VPN을 사용하는 경우 *VPN Gateway*라는 다음 홉 형식을 사용하여 경로를 만듭니다. [강제 터널링을 구성](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)하는 방법에 대해 자세히 알아봅니다.
    - 사이트 간 VPN 또는 ExpressRoute 회로를 사용할 때 가상 네트워크 게이트웨이를 통해 BGP에 0.0.0.0/0(기본 경로)을 보급하는 경우입니다. [사이트 간 VPN](../vpn-gateway/vpn-gateway-bgp-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 또는 [ExpressRoute](../expressroute/expressroute-routing.md?toc=%2fazure%2fvirtual-network%2ftoc.json#ip-addresses-used-for-azure-private-peering)에서 BGP를 사용하는 방법을 자세히 알아봅니다.
- 가상 네트워크 피어링 트래픽이 제대로 작동하려면 피어링된 가상 네트워크의 접두사 범위에 대해 *VNet 피어링*이라는 다음 홉 형식의 시스템 경로가 존재해야 합니다. 이러한 경로가 존재하지 않고 가상 네트워크 피어링 링크 연결이 **연결됨** 상태인 경우:
    - 몇 초 정도 기다렸다가 다시 시도합니다. 새로 설정된 피어링 연결인 경우 경우에 따라 서브넷에 있는 모든 네트워크 인터페이스로 경로를 전파하는 데 시간이 더 오래 걸립니다. 가상 네트워크 피어링에 대한 자세한 내용은 [가상 네트워크 피어링 개요](virtual-network-peering-overview.md) 및 [가상 네트워크 피어링 관리](virtual-network-manage-peering.md)를 참조하세요.
    - 네트워크 보안 그룹 규칙이 통신에 영향을 미칠 수 있습니다. 자세한 내용은 [가상 머신 네트워크 트래픽 필터 문제 진단](diagnose-network-traffic-filter-problem.md)을 참조하세요.
- Azure가 각 Azure 네트워크 인터페이스에 기본 경로를 할당하지만 여러 네트워크 인터페이스가 VM에 연결된 경우 주 네트워크 인터페이스만이 VM의 운영 체제 내에서 기본 경로(0.0.0.0/0) 또는 게이트웨이가 할당됩니다. [Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) 또는 [Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) VM에 연결된 보조 네트워크 인터페이스에 대한 기본 경로를 만드는 방법을 알아봅니다. [기본 및 보조 네트워크 인터페이스](virtual-network-network-interface-vm.md#constraints)에 대해 자세히 알아봅니다.

## <a name="additional-diagnosis"></a>추가 진단

* 위치에 전송된 트래픽에 다음 홉 형식을 결정하기 위해 간단한 테스트를 실행하려면 Azure Network Watcher의 [다음 홉](../network-watcher/diagnose-vm-network-routing-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 기능을 사용합니다. 다음 홉 형식을 통해 지정된 위치에 전송된 트래픽의 다음 홉 형식을 알 수 있습니다.
* VM의 네트워크 통신 실패를 일으키는 경로가 없으면 VM의 운영 체제 내에서 실행되는 방화벽 소프트웨어로 인해 문제가 발생할 수 있습니다.
* VPN Gateway 또는 NVA를 통해 온-프레미스 디바이스에 대한 [강제 터널링](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 트래픽이 있는 경우 디바이스에 대한 라우팅을 구성하는 방법에 따라 인터넷을 통해 VM에 연결하지 못할 수 있습니다. 디바이스에 구성한 라우팅이 트래픽을 VM의 공개 또는 개인 IP 주소로 라우팅했는지 확인합니다.
* Network Watcher의 [연결 문제 해결](../network-watcher/network-watcher-connectivity-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 기능을 사용하여 아웃바운드 통신 문제의 라우팅, 필터링 및 OS 내 원인을 확인합니다.

## <a name="next-steps"></a>다음 단계

- [테이블 및 경로 라우팅](manage-route-table.md)에 대한 모든 작업, 속성 및 설정에 대해 알아봅니다.
- 모든 [다음 홉 형식, 시스템 경로 및 Azure에서 경로를 선택하는 방법](virtual-networks-udr-overview.md)에 대해 자세히 알아봅니다.
