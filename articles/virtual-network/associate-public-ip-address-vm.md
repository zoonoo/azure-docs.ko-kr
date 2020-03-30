---
title: 공용 IP 주소를 가상 시스템에 연결
titlesuffix: Azure Virtual Network
description: 공용 IP 주소를 가상 시스템에 연결하는 방법을 알아봅니다.
services: virtual-network
documentationcenter: ''
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/21/2019
ms.author: kumud
ms.openlocfilehash: 5acda69ce08bc493d5349b084d1cfafc8432145b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75647461"
---
# <a name="associate-a-public-ip-address-to-a-virtual-machine"></a>공용 IP 주소를 가상 시스템에 연결

이 문서에서는 공용 IP 주소를 기존 VM(가상 시스템)에 연결하는 방법을 알아봅니다. 인터넷에서 VM에 연결하려면 VM에 연결된 공용 IP 주소가 있어야 합니다. 공용 IP 주소가 있는 새 VM을 만들려면 [Azure 포털,](virtual-network-deploy-static-pip-arm-portal.md) [CLI(Azure 명령줄 인터페이스)](virtual-network-deploy-static-pip-arm-cli.md)또는 [PowerShell](virtual-network-deploy-static-pip-arm-ps.md)을 사용하여 만들 수 있습니다. 공용 IP 주소에는 명목 요금이 부과됩니다. 자세한 내용은 [가격 책정](https://azure.microsoft.com/pricing/details/ip-addresses/)을 참조하세요. 구독당 사용할 수 있는 공용 IP 주소 수에는 제한이 있습니다. 자세한 내용은 [제한을](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#publicip-address)참조하십시오.

[Azure 포털,](#azure-portal)CLI(Azure [명령줄 인터페이스)](#azure-cli) 또는 [PowerShell을](#powershell) 사용하여 공용 IP 주소를 VM에 연결할 수 있습니다.

## <a name="azure-portal"></a>Azure portal

1. [Azure 포털에](https://portal.azure.com)로그인합니다.
2. 공용 IP 주소를 추가하려는 가상 컴퓨터를 검색하거나 검색한 다음 선택합니다.
3. **설정에서** **네트워킹을**선택한 다음 다음 그림과 같이 공용 IP 주소를 추가할 네트워크 인터페이스를 선택합니다.

   ![네트워크 인터페이스 선택](./media/associate-public-ip-address-vm/select-nic.png)

   > [!NOTE]
   > 공용 IP 주소는 VM에 연결된 네트워크 인터페이스에 연결됩니다. 이전 그림에서 VM에는 하나의 네트워크 인터페이스만 있습니다. VM에 여러 네트워크 인터페이스가 있는 경우 모두 표시되며 공용 IP 주소를 연결하려는 네트워크 인터페이스를 선택합니다.

4. **IP 구성을** 선택한 다음 그림과 같이 IP 구성을 선택합니다.

   ![IP 구성 선택](./media/associate-public-ip-address-vm/select-ip-configuration.png)

   > [!NOTE]
   > 공용 IP 주소는 네트워크 인터페이스의 IP 구성에 연결됩니다. 이전 그림에서 네트워크 인터페이스에는 하나의 IP 구성이 있습니다. 네트워크 인터페이스에 여러 IP 구성이 있는 경우 모두 목록에 표시되며 공용 IP 주소를 연결할 IP 구성을 선택합니다.

5. **활성화를**선택한 다음 **IP*주소(필수 설정 구성)를*** 선택합니다. 공용 IP 주소 선택 상자를 자동으로 닫는 기존 **공용 IP 주소를** 선택합니다. 사용 가능한 공용 IP 주소가 나열되지 않은 경우 해당 주소를 만들어야 합니다. 방법을 알아보려면 [공용 IP 주소 만들기를](virtual-network-public-ip-address.md#create-a-public-ip-address)참조하십시오. 다음 그림과 같이 **저장을**선택한 다음 IP 구성에 대한 상자를 닫습니다.

   ![공용 IP 주소 사용](./media/associate-public-ip-address-vm/enable-public-ip-address.png)

   > [!NOTE]
   > 표시되는 공용 IP 주소는 VM과 동일한 지역에 있는 주소입니다. 리전에서 여러 개의 공용 IP 주소가 생성된 경우 모두 여기에 표시됩니다. 회색으로 표시되어 있는 경우 주소가 이미 다른 리소스에 연결되어 있기 때문입니다.

6. 다음 그림과 같이 IP 구성에 할당된 공용 IP 주소를 봅니다. IP 주소가 표시되는 데 몇 초 정도 걸릴 수 있습니다.

   ![할당된 공용 IP 주소 보기](./media/associate-public-ip-address-vm/view-assigned-public-ip-address.png)

   > [!NOTE]
   > 주소는 각 Azure 리전에서 사용되는 주소 풀에서 할당됩니다. 각 리전에서 사용되는 주소 풀 목록을 보려면 [Microsoft Azure 데이터 센터 IP 범위를](https://www.microsoft.com/download/details.aspx?id=41653)참조하십시오. 할당된 주소는 지역에 사용되는 풀의 모든 주소일 수 있습니다. 리전의 특정 풀에서 주소를 할당해야 하는 경우 [공용 IP 주소 접두사를](public-ip-address-prefix.md)사용합니다.

7. 네트워크 보안 그룹의 보안 규칙을 사용하여 [VM에](#allow-network-traffic-to-the-vm) 대한 네트워크 트래픽을 허용합니다.

## <a name="azure-cli"></a>Azure CLI

Azure [CLI를](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)설치하거나 Azure 클라우드 셸을 사용합니다. Azure Cloud Shell은 Azure Portal에서 직접 실행할 수 있는 평가판 Bash 셸입니다. Azure CLI가 사전 설치되어 계정에서 사용하도록 구성되어 있습니다. 다음 CLI 명령에서 **시도** 단추를 선택합니다. **시도 를 선택하면** Azure 계정에 로그인할 수 있는 클라우드 셸이 호출됩니다.

1. Bash에서 로컬로 CLI를 사용하는 경우 `az login`을 사용하여 Azure에 로그인합니다.
2. 공용 IP 주소는 VM에 연결된 네트워크 인터페이스의 IP 구성에 연결됩니다. az [네트워크 nic-ip-config 업데이트](/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-update) 명령을 사용하여 공용 IP 주소를 IP 구성에 연결합니다. 다음 예제에서는 *myVMPublicIP라는* 기존 공용 IP 주소를 *myResourceGroup이라는*리소스 그룹에 있는 *myVMVMNic이라는* 기존 네트워크 인터페이스의 *ipconfigmyVM이라는* IP 구성에 연결합니다.
  
   ```azurecli-interactive
   az network nic ip-config update \
     --name ipconfigmyVM \
     --nic-name myVMVMNic \
     --resource-group myResourceGroup \
     --public-ip-address myVMPublicIP
   ```

   - 기존 공용 IP 주소가 없는 경우 [az 네트워크 공용 IP create](/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create) 명령을 사용하여 공용 IP 주소를 만듭니다. 예를 들어 다음 명령은 *myResourceGroup이라는*리소스 그룹에 *myVMPublicIP라는* 공용 IP 주소를 만듭니다.
  
     ```azurecli-interactive
     az network public-ip create --name myVMPublicIP --resource-group myResourceGroup
     ```

     > [!NOTE]
     > 이전 명령은 사용자 지정할 수 있는 여러 설정에 대한 기본값이 있는 공용 IP 주소를 만듭니다. 모든 공용 IP 주소 설정에 대해 자세히 알아보려면 [공용 IP 주소 만들기를](virtual-network-public-ip-address.md#create-a-public-ip-address)참조하십시오. 주소는 각 Azure 지역에 사용되는 공용 IP 주소 풀에서 할당됩니다. 각 리전에서 사용되는 주소 풀 목록을 보려면 [Microsoft Azure 데이터 센터 IP 범위를](https://www.microsoft.com/download/details.aspx?id=41653)참조하십시오.

   - VM에 연결된 네트워크 인터페이스의 이름을 모르는 경우 [az vm nic 목록](/cli/azure/vm/nic?view=azure-cli-latest#az-vm-nic-list) 명령을 사용하여 해당 인터페이스를 봅니다. 예를 들어 다음 명령은 *myResourceGroup이라는*리소스 그룹에서 *myVM이라는* VM에 연결된 네트워크 인터페이스의 이름을 나열합니다.

     ```azurecli-interactive
     az vm nic list --vm-name myVM --resource-group myResourceGroup
     ```

     출력에는 다음 예제와 유사한 하나 이상의 선이 포함됩니다.
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

     이전 예제에서 *myVMMNic은* 네트워크 인터페이스의 이름입니다.

   - 네트워크 인터페이스에 대한 IP 구성의 이름을 모르는 경우 [az 네트워크 nic ip-config 목록](/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-list) 명령을 사용하여 검색합니다. 예를 들어 다음 명령은 *myResourceGroup이라는*리소스 그룹에 *myVMMMNic이라는* 네트워크 인터페이스에 대한 IP 구성의 이름을 나열합니다.

     ```azurecli-interactive
     az network nic ip-config list --nic-name myVMVMNic --resource-group myResourceGroup --out table
     ```

3. [az vm 목록 ip-주소](/cli/azure/vm?view=azure-cli-latest#az-vm-list-ip-addresses) 명령을 사용하여 IP 구성에 할당된 공용 IP 주소를 봅니다. 다음 예제에서는 *myResourceGroup이라는*리소스 그룹의 *myVM이라는* 기존 VM에 할당된 IP 주소를 보여 주십니다.

   ```azurecli-interactive
   az vm list-ip-addresses --name myVM --resource-group myResourceGroup --out table
   ```

   > [!NOTE]
   > 주소는 각 Azure 리전에서 사용되는 주소 풀에서 할당됩니다. 각 리전에서 사용되는 주소 풀 목록을 보려면 [Microsoft Azure 데이터 센터 IP 범위를](https://www.microsoft.com/download/details.aspx?id=41653)참조하십시오. 할당된 주소는 지역에 사용되는 풀의 모든 주소일 수 있습니다. 리전의 특정 풀에서 주소를 할당해야 하는 경우 [공용 IP 주소 접두사를](public-ip-address-prefix.md)사용합니다.

4. 네트워크 보안 그룹의 보안 규칙을 사용하여 [VM에](#allow-network-traffic-to-the-vm) 대한 네트워크 트래픽을 허용합니다.

## <a name="powershell"></a>PowerShell

[PowerShell을](/powershell/azure/install-az-ps)설치하거나 Azure 클라우드 셸을 사용합니다. Azure Cloud Shell은 Azure Portal에서 직접 실행할 수 있는 평가판 셸입니다. PowerShell이 미리 설치되어 계정에서 사용하도록 구성되어 있습니다. 다음 PowerShell 명령에서 **시도** 단추를 선택합니다. **시도 를 선택하면** Azure 계정에 로그인할 수 있는 클라우드 셸이 호출됩니다.

1. PowerShell을 로컬로 사용하는 경우 `Connect-AzAccount`을 사용하여 Azure에 로그인합니다.
2. 공용 IP 주소는 VM에 연결된 네트워크 인터페이스의 IP 구성에 연결됩니다. [Get-AzVirtualNetwork](/powershell/module/Az.Network/Get-AzVirtualNetwork) 및 [Get-AzVirtualNetworkSubnetConfig](/powershell/module/Az.Network/Get-AzVirtualNetworkSubnetConfig) 명령을 사용하여 네트워크 인터페이스가 있는 가상 네트워크 및 서브넷을 가져옵니다. 다음으로 [Get-AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface) 명령을 사용하여 네트워크 인터페이스와 [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) 명령을 사용하여 기존 공용 IP 주소를 가져옵니다. 그런 다음 [Set-AzNetworkInterfaceIpConfig](/powershell/module/Az.Network/Set-AzNetworkInterfaceIpConfig) 명령을 사용하여 공용 IP 주소를 IP 구성및 [Set-AzNetworkInterface](/powershell/module/Az.Network/Set-AzNetworkInterface) 명령에 연결하여 새 IP 구성을 네트워크 인터페이스에 작성합니다.

   다음 예제에서는 *myVMPublicIP라는* 이름의 기존 공용 IP 주소를 *myVMVNet이라는*가상 네트워크의 하위 네트워크에 있는 myVMSubnet이라는 *myVMSubnet* 하위 네트워크에 있는 *myVMVMNic이라는* 기존 네트워크 인터페이스의 *ipconfigmyVM이라는* IP 구성에 연결합니다. 모든 리소스는 *myResourceGroup이라는*리소스 그룹에 있습니다.
  
   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name myVMVNet -ResourceGroupName myResourceGroup
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name myVMSubnet -VirtualNetwork $vnet
   $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
   $pip = Get-AzPublicIpAddress -Name myVMPublicIP -ResourceGroupName myResourceGroup
   $nic | Set-AzNetworkInterfaceIpConfig -Name ipconfigmyVM -PublicIPAddress $pip -Subnet $subnet
   $nic | Set-AzNetworkInterface
   ```

   - 기존 공용 IP 주소가 없는 경우 [New-AzPublicIpAddress](/powershell/module/Az.Network/New-AzPublicIpAddress) 명령을 사용하여 공용 IP 주소를 만듭니다. 예를 들어 다음 명령은 *동쪽* 지역의 *myResourceGroup이라는* 리소스 그룹에 *myVMPublicIP라는* *동적* 공용 IP 주소를 만듭니다.
  
     ```azurepowershell-interactive
     New-AzPublicIpAddress -Name myVMPublicIP -ResourceGroupName myResourceGroup -AllocationMethod Dynamic -Location eastus
     ```

     > [!NOTE]
     > 이전 명령은 사용자 지정할 수 있는 여러 설정에 대한 기본값이 있는 공용 IP 주소를 만듭니다. 모든 공용 IP 주소 설정에 대해 자세히 알아보려면 [공용 IP 주소 만들기를](virtual-network-public-ip-address.md#create-a-public-ip-address)참조하십시오. 주소는 각 Azure 지역에 사용되는 공용 IP 주소 풀에서 할당됩니다. 각 리전에서 사용되는 주소 풀 목록을 보려면 [Microsoft Azure 데이터 센터 IP 범위를](https://www.microsoft.com/download/details.aspx?id=41653)참조하십시오.

   - VM에 연결된 네트워크 인터페이스의 이름을 모르는 경우 [Get-AzVM](/powershell/module/Az.Compute/Get-AzVM) 명령을 사용하여 해당 인터페이스를 봅니다. 예를 들어 다음 명령은 *myResourceGroup이라는*리소스 그룹에서 *myVM이라는* VM에 연결된 네트워크 인터페이스의 이름을 나열합니다.

     ```azurepowershell-interactive
     $vm = Get-AzVM -name myVM -ResourceGroupName myResourceGroup
     $vm.NetworkProfile
     ```

     출력에는 다음 예제와 유사한 하나 이상의 선이 포함됩니다. 예제 출력에서 *myVMMNic은* 네트워크 인터페이스의 이름입니다.
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

   - 네트워크 인터페이스가 있는 가상 네트워크 또는 서브넷의 이름을 모르는 경우 `Get-AzNetworkInterface` 명령을 사용하여 정보를 봅니다. 예를 들어 다음 명령은 *myResourceGroup이라는*리소스 그룹에서 *myVMMNic이라는* 네트워크 인터페이스에 대한 가상 네트워크 및 서브넷 정보를 가져옵니다.

     ```azurepowershell-interactive
     $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
     $ipConfigs = $nic.IpConfigurations
     $ipConfigs.Subnet | Select Id
     ```

     출력에는 다음 예제와 유사한 하나 이상의 선이 포함됩니다. 예제 출력에서 *myVMVNET은* 가상 네트워크의 이름이며 *myVMSubnet은* 서브넷의 이름입니다.
  
     ```
     "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVMVNET/subnets/myVMSubnet",
     ```

   - 네트워크 인터페이스에 대한 IP 구성의 이름을 모르는 경우 [Get-AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface) 명령을 사용하여 검색합니다. 예를 들어 다음 명령은 *myResourceGroup이라는*리소스 그룹에 *myVMMMNic이라는* 네트워크 인터페이스에 대한 IP 구성의 이름을 나열합니다.

     ```azurepowershell-interactive
     $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
     $nic.IPConfigurations
     ```

     출력에는 다음 예제와 유사한 하나 이상의 선이 포함됩니다. 예제 출력에서 *ipconfigmyVM은* IP 구성의 이름입니다.
  
     ```
     Id     : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic/ipConfigurations/ipconfigmyVM
     ```

3. [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) 명령을 사용하여 IP 구성에 할당된 공용 IP 주소를 봅니다. 다음 예제에서는 *myResourceGroup이라는*리소스 그룹에서 *myVMPublicIP라는* 공용 IP 주소에 할당된 주소를 보여 주십니다.

   ```azurepowershell-interactive
   Get-AzPublicIpAddress -Name myVMPublicIP -ResourceGroupName myResourceGroup | Select IpAddress
   ```

   IP 구성에 할당된 공용 IP 주소의 이름을 모르는 경우 다음 명령을 실행하여 가져옵니다.

   ```azurepowershell-interactive
   $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
   $nic.IPConfigurations
   $address = $nic.IPConfigurations.PublicIpAddress
   $address | Select Id
   ```

   출력에는 다음 예제와 유사한 하나 이상의 선이 포함됩니다. 예제 출력에서 *myVMPublicIP는* IP 구성에 할당된 공용 IP 주소의 이름입니다.

   ```
   "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myVMPublicIP"
   ```

   > [!NOTE]
   > 주소는 각 Azure 리전에서 사용되는 주소 풀에서 할당됩니다. 각 리전에서 사용되는 주소 풀 목록을 보려면 [Microsoft Azure 데이터 센터 IP 범위를](https://www.microsoft.com/download/details.aspx?id=41653)참조하십시오. 할당된 주소는 지역에 사용되는 풀의 모든 주소일 수 있습니다. 리전의 특정 풀에서 주소를 할당해야 하는 경우 [공용 IP 주소 접두사를](public-ip-address-prefix.md)사용합니다.

4. 네트워크 보안 그룹의 보안 규칙을 사용하여 [VM에](#allow-network-traffic-to-the-vm) 대한 네트워크 트래픽을 허용합니다.

## <a name="allow-network-traffic-to-the-vm"></a>VM에 대한 네트워크 트래픽 허용

인터넷에서 공용 IP 주소에 연결하기 전에 네트워크 인터페이스에 연결했을 수 있는 네트워크 보안 그룹에서 필요한 포트가 열려 있는지, 네트워크 인터페이스가 있는 서브넷이 있는지 확인합니다. 보안 그룹이 네트워크 인터페이스의 개인 IP 주소로 트래픽을 필터링하지만, 인바운드 인터넷 트래픽이 공용 IP 주소에 도착하면 Azure는 공용 주소를 개인 IP 주소로 변환하므로 네트워크 보안 그룹이 공용 IP 주소와의 통신이 실패합니다. [포털,](diagnose-network-traffic-filter-problem.md#diagnose-using-azure-portal) [CLI](diagnose-network-traffic-filter-problem.md#diagnose-using-azure-cli)또는 [PowerShell을](diagnose-network-traffic-filter-problem.md#diagnose-using-powershell)사용하여 네트워크 인터페이스 및 해당 서브넷에 대한 효과적인 보안 규칙을 볼 수 있습니다.

## <a name="next-steps"></a>다음 단계

네트워크 보안 그룹을 사용하여 VM으로 의 인바운드 인터넷 트래픽을 허용합니다. 네트워크 보안 그룹을 만드는 방법에 대해 알아보려면 [네트워크 보안 그룹과의 작업](manage-network-security-group.md#work-with-network-security-groups)보기를 참조하십시오. 네트워크 보안 그룹에 대한 자세한 내용은 [보안 그룹을](security-overview.md)참조하십시오.
