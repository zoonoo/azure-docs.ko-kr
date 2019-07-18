---
title: 가상 머신에 공용 IP 주소를 연결 합니다.
titlesuffix: Azure Virtual Network
description: 가상 머신에 공용 IP 주소를 연결 하는 방법에 알아봅니다.
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
ms.openlocfilehash: 1b201957a33acd609eed8a2373c8201bdefe9d7d
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/17/2019
ms.locfileid: "64692004"
---
# <a name="associate-a-public-ip-address-to-a-virtual-machine"></a>가상 머신에 공용 IP 주소를 연결 합니다.

이 문서에서는 기존 가상 컴퓨터 (VM)에 공용 IP 주소를 연결 하는 방법을 알아봅니다. 인터넷에서 VM에 연결 하려는 경우 VM에 연결 된 공용 IP가 있어야 합니다. 공용 IP 주소를 사용 하 여 새 VM을 만들려는 경우 사용 하 여 수행할 수 있습니다 합니다 [Azure portal](virtual-network-deploy-static-pip-arm-portal.md)의 [Azure CLI (명령줄 인터페이스)](virtual-network-deploy-static-pip-arm-cli.md), 또는 [PowerShell](virtual-network-deploy-static-pip-arm-ps.md)합니다. 공용 IP 주소에는 명목 요금이 부과됩니다. 자세한 내용은 [가격 책정](https://azure.microsoft.com/pricing/details/ip-addresses/)을 참조하세요. 구독 당 사용할 수 있는 공용 IP 주소의 수에 제한이 있습니다. 자세한 내용은 참조 하세요 [제한](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#publicip-address)합니다.

사용할 수 있습니다는 [Azure portal](#azure-portal), Azure [명령줄 인터페이스](#azure-cli) (CLI) 또는 [PowerShell](#powershell) VM에 공용 IP 주소를 연결 합니다.

## <a name="azure-portal"></a>Azure portal

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 찾아보거나에 공용 IP 주소를 추가 하 고 선택 하려는 가상 컴퓨터를 검색 합니다.
3. 아래 **설정을**를 선택 **네트워킹**를 선택한 후 다음 그림에 나와 있는 것 처럼, 공용 IP 주소를 추가 하려는 네트워크 인터페이스:

   ![네트워크 인터페이스 선택](./media/associate-public-ip-address-vm/select-nic.png)

   > [!NOTE]
   > 공용 IP 주소는 VM에 연결 하는 네트워크 인터페이스에 연결 합니다. VM에만 이전 그림에서 하나의 네트워크 인터페이스가 있습니다. VM에 여러 네트워크 인터페이스가 있으면 모든 표시 되는, 및 공용 IP 주소를 연결 하려는 네트워크 인터페이스를 선택 합니다.

4. 선택 **IP 구성** 및 다음 그림에 표시 된 것과 같이 IP 구성을 선택 합니다.

   ![IP 구성 선택](./media/associate-public-ip-address-vm/select-ip-configuration.png)

   > [!NOTE]
   > 공용 IP 주소는 네트워크 인터페이스 IP 구성에 연결 됩니다. 이전 그림에서 네트워크 인터페이스에는 IP 구성이 하나만 있습니다. 네트워크 인터페이스에 여러 IP 구성이 있으면 모두에 나타나는 목록 및 공용 IP 주소를 연결 하려는 IP 구성을 선택 합니다.

5. 선택 **Enabled**을 선택한 후 **IP 주소 (*필수 설정 구성*)** 합니다. 기존 공용 IP 주소를 자동으로 닫습니다 선택 합니다 **공용 IP 주소 선택** 상자입니다. 나열 된 모든 사용 가능한 공용 IP 주소가 없으면 하나 만듭니다 해야 합니다. 자세한 내용은 [공용 IP 주소 만들기](virtual-network-public-ip-address.md#create-a-public-ip-address)합니다. 선택 **저장할**따르며 IP 구성에 대 한 상자를 닫습니다 그림에 나와 있는 것 처럼 합니다.

   ![공용 IP 주소를 사용 하도록 설정](./media/associate-public-ip-address-vm/enable-public-ip-address.png)

   > [!NOTE]
   > 표시 되는 공용 IP 주소는 VM과 동일한 지역에 존재 하는 합니다. 지역에서 생성 하는 여러 공용 IP 주소에 있는 경우 모두 여기에 나타납니다. 하나는 회색으로 표시 하는 경우 주소가 이미 다른 리소스에 연결 하는 때문입니다.

6. 다음에 나오는 그림에서 보여준 대로 IP 구성에 할당 된 공용 IP 주소를 봅니다. 표시할 IP 주소에 대 한 몇 초 정도 걸립니다.

   ![공용 IP 주소를 할당 하는 보기](./media/associate-public-ip-address-vm/view-assigned-public-ip-address.png)

   > [!NOTE]
   > 각 Azure 지역에서 사용 되는 주소 풀에서 주소를 할당 합니다. 각 지역에서 사용 되는 주소 풀의 목록을 참조 하세요 [Microsoft Azure Datacenter IP Ranges](https://www.microsoft.com/download/details.aspx?id=41653)합니다. 할당 된 주소에는 지역에 사용 되는 풀의 모든 주소일 수 있습니다. 사용 하 여 지역에서 특정 풀에서 할당에 대 한 주소를 해야 하는 경우는 [공용 IP 주소 접두사](public-ip-address-prefix.md)합니다.

7. [VM에 네트워크 트래픽을 허용](#allow-network-traffic-to-the-vm) 네트워크 보안 그룹의 보안 규칙을 사용 하 여 합니다.

## <a name="azure-cli"></a>Azure CLI

설치를 [Azure CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json), 또는 Azure Cloud Shell을 사용 합니다. Azure Cloud Shell은 Azure Portal에서 직접 실행할 수 있는 평가판 Bash 셸입니다. Azure CLI가 사전 설치되어 계정에서 사용하도록 구성되어 있습니다. 선택 합니다 **사용해** 단추 CLI에서 다음 명령입니다. 선택 **사용해** 사용 하 여 Azure 계정에 로그인 할 수 있는 Cloud Shell을 호출 합니다.

1. CLI를 로컬로 bash에서를 사용 하는 경우 사용 하 여 Azure에 로그인 `az login`합니다.
2. 공용 IP 주소를 VM에 연결 된 네트워크 인터페이스의 IP 구성에 연결 됩니다. 사용 된 [az network nic ip 구성 업데이트](/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-update) IP 구성에 공용 IP 주소를 연결할 명령입니다. 다음 예제에서는 명명 된 기존 공용 IP 주소에 연결 *myVMPublicIP* 라는 IP 구성을 *ipconfigmyVM* 라는 기존 네트워크 인터페이스의 *myVMVMNic* 이라는 리소스 그룹에 있는 *myResourceGroup*합니다.
  
   ```azurecli-interactive
   az network nic ip-config update \
     --name ipconfigmyVM \
     --nic-name myVMVMNic \
     --resource-group myResourceGroup \
     --public-ip-address myVMPublicIP
   ```

   - 사용 하 여 기존 공용 IP 주소에 없는 경우는 [az network public ip 만들기](/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create) 명령을 만들어야 합니다. 예를 들어, 다음 명령은 라는 공용 IP 주소를 만듭니다 *myVMPublicIP* 이라는 리소스 그룹에서 *myResourceGroup*합니다.
  
     ```azurecli-interactive
     az network public-ip create --name myVMPublicIP --resource-group myResourceGroup
     ```

     > [!NOTE]
     > 이전 명령은 사용자 지정할 수 있는 몇 가지 설정에 대 한 기본값을 사용 하 여 공용 IP 주소를 만듭니다. 모든 공용 IP 주소 설정에 대 한 자세한 내용은 참조 하세요 [공용 IP 주소 만들기](virtual-network-public-ip-address.md#create-a-public-ip-address)합니다. 각 Azure 지역에 사용 되는 공용 IP 주소 풀에서 주소를 할당 합니다. 각 지역에서 사용 되는 주소 풀의 목록을 참조 하세요 [Microsoft Azure Datacenter IP Ranges](https://www.microsoft.com/download/details.aspx?id=41653)합니다.

   - 사용 하 여 VM에 연결 된 네트워크 인터페이스의 이름을 모르는 경우 합니다 [az vm nic 목록](/cli/azure/vm/nic?view=azure-cli-latest#az-vm-nic-list) 표시 하는 명령입니다. 다음 명령은 이라는 VM에 연결 된 네트워크 인터페이스의 이름을 나열 하는 예를 들어 *myVM* 이라는 리소스 그룹에서 *myResourceGroup*:

     ```azurecli-interactive
     az vm nic list --vm-name myVM --resource-group myResourceGroup
     ```

     다음 예제와 유사 하 게 연결 된 하나 이상의 줄을 포함 하는 출력:
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

     이전 예에서 *myVMVMNic* 네트워크 인터페이스의 이름입니다.

   - 네트워크 인터페이스용 IP 구성의 이름을 모르는 경우 사용 합니다 [az network nic ip 구성 목록](/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-list) 검색 하는 명령입니다. 다음 명령은 이라는 네트워크 인터페이스에 대 한 IP 구성의 이름을 나열 하는 예를 들어 *myVMVMNic* 이라는 리소스 그룹에서 *myResourceGroup*:

     ```azurecli-interactive
     az network nic ip-config list --nic-name myVMVMNic --resource-group myResourceGroup --out table
     ```

3. 사용 하 여 IP 구성에 할당 된 공용 IP 주소를 확인 합니다 [az vm 목록-ip 주소](/cli/azure/vm?view=azure-cli-latest#az-vm-list-ip-addresses) 명령입니다. 다음 예제에서는 명명 된 기존 VM에 할당 된 IP 주소 *myVM* 이라는 리소스 그룹에서 *myResourceGroup*합니다.

   ```azurecli-interactive
   az vm list-ip-addresses --name myVM --resource-group myResourceGroup --out table
   ```

   > [!NOTE]
   > 각 Azure 지역에서 사용 되는 주소 풀에서 주소를 할당 합니다. 각 지역에서 사용 되는 주소 풀의 목록을 참조 하세요 [Microsoft Azure Datacenter IP Ranges](https://www.microsoft.com/download/details.aspx?id=41653)합니다. 할당 된 주소에는 지역에 사용 되는 풀의 모든 주소일 수 있습니다. 사용 하 여 지역에서 특정 풀에서 할당에 대 한 주소를 해야 하는 경우는 [공용 IP 주소 접두사](public-ip-address-prefix.md)합니다.

4. [VM에 네트워크 트래픽을 허용](#allow-network-traffic-to-the-vm) 네트워크 보안 그룹의 보안 규칙을 사용 하 여 합니다.

## <a name="powershell"></a>PowerShell

설치할 [PowerShell](/powershell/azure/install-az-ps), 또는 Azure Cloud Shell을 사용 합니다. Azure Cloud Shell은 Azure Portal에서 직접 실행할 수 있는 평가판 셸입니다. PowerShell이 미리 설치되어 계정에서 사용하도록 구성되어 있습니다. 선택 된 **사용해** powershell에서 단추는 다음 명령입니다. 선택 **사용해** 사용 하 여 Azure 계정에 로그인 할 수 있는 Cloud Shell을 호출 합니다.

1. PowerShell을 로컬로 사용 하는 경우 사용 하 여 Azure에 로그인 `Connect-AzAccount`합니다.
2. 공용 IP 주소를 VM에 연결 된 네트워크 인터페이스의 IP 구성에 연결 됩니다. 사용 합니다 [Get AzVirtualNetwork](/powershell/module/Az.Network/Get-AzVirtualNetwork) 및 [Get AzVirtualNetworkSubnetConfig](/powershell/module/Az.Network/Get-AzVirtualNetworkSubnetConfig) 가상 네트워크 및 서브넷에 네트워크 인터페이스에 있는 명령입니다. 다음을 사용 하 여는 [Get AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface) 네트워크 인터페이스를 가져오려면 명령 및 [Get AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) 기존 공용 IP 주소를 가져오려면 명령입니다. 사용 하 여 합니다 [집합 AzNetworkInterfaceIpConfig](/powershell/module/Az.Network/Set-AzNetworkInterfaceIpConfig) IP 구성에 공용 IP 주소를 연결 하는 명령 및 [집합 AzNetworkInterface](/powershell/module/Az.Network/Set-AzNetworkInterface) 새 IP 구성을 쓰는 명령을 네트워크 인터페이스입니다.

   다음 예제에서는 명명 된 기존 공용 IP 주소에 연결 *myVMPublicIP* 라는 IP 구성을 *ipconfigmyVM* 라는 기존 네트워크 인터페이스의 *myVMVMNic* 이라는 서브넷에 있는 *myVMSubnet* 이라는 가상 네트워크에서 *myVMVNet*합니다. 명명 된 리소스 그룹의 모든 리소스가 *myResourceGroup*합니다.
  
   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name myVMVNet -ResourceGroupName myResourceGroup
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name myVMSubnet -VirtualNetwork $vnet
   $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
   $pip = Get-AzPublicIpAddress -Name myVMPublicIP -ResourceGroupName myResourceGroup
   $nic | Set-AzNetworkInterfaceIpConfig -Name ipconfigmyVM -PublicIPAddress $pip -Subnet $subnet
   $nic | Set-AzNetworkInterface
   ```

   - 사용 하 여 기존 공용 IP 주소에 없는 경우는 [새로 만들기-AzPublicIpAddress](/powershell/module/Az.Network/New-AzPublicIpAddress) 만들려면 명령입니다. 예를 들어 다음 명령은 만듭니다는 *동적* 라는 공용 IP 주소 *myVMPublicIP* 이라는 리소스 그룹의 *myResourceGroup* 에  *eastus* 지역입니다.
  
     ```azurepowershell-interactive
     New-AzPublicIpAddress -Name myVMPublicIP -ResourceGroupName myResourceGroup -AllocationMethod Dynamic -Location eastus
     ```

     > [!NOTE]
     > 이전 명령은 사용자 지정할 수 있는 몇 가지 설정에 대 한 기본값을 사용 하 여 공용 IP 주소를 만듭니다. 모든 공용 IP 주소 설정에 대 한 자세한 내용은 참조 하세요 [공용 IP 주소 만들기](virtual-network-public-ip-address.md#create-a-public-ip-address)합니다. 각 Azure 지역에 사용 되는 공용 IP 주소 풀에서 주소를 할당 합니다. 각 지역에서 사용 되는 주소 풀의 목록을 참조 하세요 [Microsoft Azure Datacenter IP Ranges](https://www.microsoft.com/download/details.aspx?id=41653)합니다.

   - 사용 하 여 VM에 연결 된 네트워크 인터페이스의 이름을 모르는 경우 합니다 [Get AzVM](/powershell/module/Az.Compute/Get-AzVM) 표시 하는 명령입니다. 다음 명령은 이라는 VM에 연결 된 네트워크 인터페이스의 이름을 나열 하는 예를 들어 *myVM* 이라는 리소스 그룹에서 *myResourceGroup*:

     ```azurepowershell-interactive
     $vm = Get-AzVM -name myVM -ResourceGroupName myResourceGroup
     $vm.NetworkProfile
     ```

     출력에 나오는 예제와 유사한 줄을 한 개 이상 포함 되어 있습니다. 예제 출력에서 *myVMVMNic* 네트워크 인터페이스의 이름입니다.
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

   - 사용 하 여 가상 네트워크 또는 네트워크 인터페이스에 있는 서브넷의 이름을 모르는 경우는 `Get-AzNetworkInterface` 명령 정보를 볼 수 있습니다. 예를 들어 다음 명령은 이라는 네트워크 인터페이스에 대 한 가상 네트워크 및 서브넷 정보를 가져옵니다 *myVMVMNic* 이라는 리소스 그룹에서 *myResourceGroup*:

     ```azurepowershell-interactive
     $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
     $ipConfigs = $nic.IpConfigurations
     $ipConfigs.Subnet | Select Id
     ```

     출력에 나오는 예제와 유사한 줄을 한 개 이상 포함 되어 있습니다. 예제 출력에서 *myVMVNET* 가상 네트워크의 이름 및 *myVMSubnet* 서브넷의 이름입니다.
  
     ```
     "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVMVNET/subnets/myVMSubnet",
     ```

   - 네트워크 인터페이스용 IP 구성의 이름을 모르는 경우 사용 합니다 [Get AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface) 검색 하는 명령. 다음 명령은 이라는 네트워크 인터페이스에 대 한 IP 구성의 이름을 나열 하는 예를 들어 *myVMVMNic* 이라는 리소스 그룹에서 *myResourceGroup*:

     ```azurepowershell-interactive
     $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
     $nic.IPConfigurations
     ```

     출력에 나오는 예제와 유사한 줄을 한 개 이상 포함 되어 있습니다. 예제 출력에서 *ipconfigmyVM* IP 구성의 이름입니다.
  
     ```
     Id     : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic/ipConfigurations/ipconfigmyVM
     ```

3. 사용 하 여 IP 구성에 할당 된 공용 IP 주소를 확인 합니다 [Get AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) 명령입니다. 다음 예제에서는 라는 공용 IP 주소를 할당할 주소 *myVMPublicIP* 이라는 리소스 그룹에서 *myResourceGroup*합니다.

   ```azurepowershell-interactive
   Get-AzPublicIpAddress -Name myVMPublicIP -ResourceGroupName myResourceGroup | Select IpAddress
   ```

   IP 구성에 할당 된 공용 IP 주소의 이름을 모르는 경우에 다음 명령을 실행 합니다.

   ```azurepowershell-interactive
   $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
   $nic.IPConfigurations
   $address = $nic.IPConfigurations.PublicIpAddress
   $address | Select Id
   ```

   출력에 나오는 예제와 유사한 줄을 한 개 이상 포함 되어 있습니다. 예제 출력에서 *myVMPublicIP* IP 구성에 할당 된 공용 ip의 이름입니다.

   ```
   "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myVMPublicIP"
   ```

   > [!NOTE]
   > 각 Azure 지역에서 사용 되는 주소 풀에서 주소를 할당 합니다. 각 지역에서 사용 되는 주소 풀의 목록을 참조 하세요 [Microsoft Azure Datacenter IP Ranges](https://www.microsoft.com/download/details.aspx?id=41653)합니다. 할당 된 주소에는 지역에 사용 되는 풀의 모든 주소일 수 있습니다. 사용 하 여 지역에서 특정 풀에서 할당에 대 한 주소를 해야 하는 경우는 [공용 IP 주소 접두사](public-ip-address-prefix.md)합니다.

4. [VM에 네트워크 트래픽을 허용](#allow-network-traffic-to-the-vm) 네트워크 보안 그룹의 보안 규칙을 사용 하 여 합니다.

## <a name="allow-network-traffic-to-the-vm"></a>VM에 네트워크 트래픽을 허용합니다

인터넷에서 공용 IP 주소에 연결할 수 있습니다, 전에 필요한 포트를 열고 네트워크 인터페이스, 네트워크 인터페이스, 서브넷 또는 둘 다에 연결 된 네트워크 보안 그룹에 있는지 확인 합니다. 보안 그룹 인바운드 인터넷 트래픽을 공용 IP 주소를 Azure에 도착 하면 네트워크 인터페이스의 개인 IP 주소로 트래픽을 필터링 변환 하지만 공용 주소를 개인 IP 주소, 네트워크 보안 그룹을 방지 하는 경우는 트래픽 흐름에 공용 IP 주소와의 통신이 실패합니다. 네트워크 인터페이스 및 사용 하 여 해당 서브넷에 대 한 유효 보안 규칙을 볼 수 있습니다 합니다 [포털](diagnose-network-traffic-filter-problem.md#diagnose-using-azure-portal)를 [CLI](diagnose-network-traffic-filter-problem.md#diagnose-using-azure-cli), 또는 [PowerShell](diagnose-network-traffic-filter-problem.md#diagnose-using-powershell)합니다.

## <a name="next-steps"></a>다음 단계

네트워크 보안 그룹을 사용 하 여 VM에 인바운드 인터넷 트래픽을 허용 합니다. 네트워크 보안 그룹을 만드는 방법에 알아보려면 참조 [네트워크 보안 그룹을 사용 하 여 작동](manage-network-security-group.md#work-with-network-security-groups)합니다. 네트워크 보안 그룹에 대 한 자세한 내용은 참조 하세요 [보안 그룹](security-overview.md)합니다.
