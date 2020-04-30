---
title: 가상 컴퓨터에 공용 IP 주소 연결
titlesuffix: Azure Virtual Network
description: 가상 머신에 공용 IP 주소를 연결 하는 방법에 대해 알아봅니다.
services: virtual-network
documentationcenter: ''
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/21/2019
ms.author: allensu
ms.openlocfilehash: 2170a4d5f66cf6d1f699ae943f2a80b1b8127e39
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82146593"
---
# <a name="associate-a-public-ip-address-to-a-virtual-machine"></a>가상 컴퓨터에 공용 IP 주소 연결

이 문서에서는 기존 VM (가상 컴퓨터)에 공용 IP 주소를 연결 하는 방법에 대해 알아봅니다. 인터넷에서 VM에 연결 하려면 VM에 연결 된 공용 IP 주소가 있어야 합니다. 공용 IP 주소를 사용 하 여 새 VM을 만들려는 경우 [Azure Portal](virtual-network-deploy-static-pip-arm-portal.md), [Azure CLI (명령줄 인터페이스)](virtual-network-deploy-static-pip-arm-cli.md)또는 [PowerShell](virtual-network-deploy-static-pip-arm-ps.md)을 사용 하 여 만들 수 있습니다. 공용 IP 주소에는 명목 요금이 부과됩니다. 자세한 내용은 [가격 책정](https://azure.microsoft.com/pricing/details/ip-addresses/)을 참조하세요. 구독 당 사용할 수 있는 공용 IP 주소의 수는 제한 되어 있습니다. 자세한 내용은 [제한](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#publicip-address)을 참조 하세요.

[Azure Portal](#azure-portal), Azure CLI ( [명령줄 인터페이스](#azure-cli) ) 또는 [PowerShell](#powershell) 을 사용 하 여 공용 IP 주소를 VM에 연결할 수 있습니다.

## <a name="azure-portal"></a>Azure portal

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 공용 IP 주소를 추가 하려는 가상 컴퓨터를 찾아보거나 검색 한 다음 선택 합니다.
3. 아래 그림에 표시 된 것 처럼 **설정**에서 **네트워킹**을 선택 하 고 공용 IP 주소를 추가 하려는 네트워크 인터페이스를 선택 합니다.

   ![네트워크 인터페이스 선택](./media/associate-public-ip-address-vm/select-nic.png)

   > [!NOTE]
   > 공용 IP 주소는 VM에 연결 된 네트워크 인터페이스에 연결 됩니다. 위의 그림에서 VM에는 네트워크 인터페이스가 하나만 있습니다. VM에 여러 네트워크 인터페이스가 있으면 모두 표시 되 고 공용 IP 주소를 연결 하려는 네트워크 인터페이스를 선택 합니다.

4. **Ip** 구성을 선택 하 고 다음 그림과 같이 ip 구성을 선택 합니다.

   ![IP 구성 선택](./media/associate-public-ip-address-vm/select-ip-configuration.png)

   > [!NOTE]
   > 공용 IP 주소는 네트워크 인터페이스에 대 한 IP 구성에 연결 됩니다. 이전 그림에서 네트워크 인터페이스에는 IP 구성이 하나 있습니다. 네트워크 인터페이스에 여러 IP 구성이 있는 경우 모두 목록에 표시 되며, 공용 IP 주소를 연결 하려는 IP 구성을 선택 합니다.

5. **사용**을 선택 하 고 **IP 주소 (*필수 설정 구성*)** 를 선택 합니다. **공용 ip 주소 선택** 상자를 자동으로 닫는 기존 공용 ip 주소를 선택 합니다. 사용 가능한 공용 IP 주소가 나열 되지 않은 경우 새로 만들어야 합니다. 방법을 알아보려면 [공용 IP 주소 만들기](virtual-network-public-ip-address.md#create-a-public-ip-address)를 참조 하세요. 다음 그림에 표시 된 것 처럼 **저장**을 선택 하 고 IP 구성 상자를 닫습니다.

   ![공용 IP 주소 사용](./media/associate-public-ip-address-vm/enable-public-ip-address.png)

   > [!NOTE]
   > 표시 되는 공용 IP 주소는 VM과 동일한 지역에 존재 합니다. 지역에 여러 개의 공용 IP 주소가 생성 된 경우 모두 여기에 표시 됩니다. 확인란이 회색으로 표시 되 면 주소가 이미 다른 리소스에 연결 되어 있기 때문입니다.

6. 다음 그림에 표시 된 것 처럼 IP 구성에 할당 된 공용 IP 주소를 확인 합니다. IP 주소가 표시 되는 데 몇 초 정도 걸릴 수 있습니다.

   ![할당 된 공용 IP 주소 보기](./media/associate-public-ip-address-vm/view-assigned-public-ip-address.png)

   > [!NOTE]
   > 주소는 각 Azure 지역에서 사용 되는 주소 풀에서 할당 됩니다. 각 지역에서 사용 되는 주소 풀 목록을 보려면 [Microsoft Azure 데이터 센터 IP 범위](https://www.microsoft.com/download/details.aspx?id=41653)를 참조 하세요. 할당 된 주소는 해당 지역에 사용 되는 풀의 모든 주소일 수 있습니다. 해당 지역의 특정 풀에서 주소를 할당 해야 하는 경우 [공용 IP 주소 접두사](public-ip-address-prefix.md)를 사용 합니다.

7. 네트워크 보안 그룹의 보안 규칙을 사용 하 여 [VM에 대 한 네트워크 트래픽을 허용](#allow-network-traffic-to-the-vm) 합니다.

## <a name="azure-cli"></a>Azure CLI

[Azure CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)를 설치 하거나 Azure Cloud Shell를 사용 합니다. Azure Cloud Shell은 Azure Portal에서 직접 실행할 수 있는 평가판 Bash 셸입니다. Azure CLI가 사전 설치되어 계정에서 사용하도록 구성되어 있습니다. 다음 CLI 명령에서 **사용해 보기** 단추를 선택 합니다. **사용해 보기** 를 선택 하면 Azure 계정에 로그인 할 수 있는 Cloud Shell 호출 됩니다.

1. 로컬로 CLI를 Bash에서 사용 하는 경우를 사용 하 여 `az login`Azure에 로그인 합니다.
2. 공용 IP 주소는 VM에 연결 된 네트워크 인터페이스의 IP 구성에 연결 됩니다. [Az network nic-ip-https update](/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-update) 명령을 사용 하 여 ip 구성에 공용 ip 주소를 연결 합니다. 다음 예제에서는 *Myvmpublicip* 이라는 기존 공용 IP 주소를 *myresourcegroup*이라는 리소스 그룹에 있는 *myvmvmnic* 라는 기존 네트워크 인터페이스의 *ipconfigmyvm* 이라는 ip 구성에 연결 합니다.
  
   ```azurecli-interactive
   az network nic ip-config update \
     --name ipconfigmyVM \
     --nic-name myVMVMNic \
     --resource-group myResourceGroup \
     --public-ip-address myVMPublicIP
   ```

   - 기존 공용 IP 주소가 없는 경우 [az network 공용-IP create](/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create) 명령을 사용 하 여 하나를 만듭니다. 예를 들어 다음 명령은 *Myresourcegroup*이라는 리소스 그룹에 *myvmpublicip* 라는 공용 IP 주소를 만듭니다.
  
     ```azurecli-interactive
     az network public-ip create --name myVMPublicIP --resource-group myResourceGroup
     ```

     > [!NOTE]
     > 이전 명령은 사용자 지정 하려는 여러 설정에 대 한 기본값을 사용 하 여 공용 IP 주소를 만듭니다. 모든 공용 IP 주소 설정에 대해 자세히 알아보려면 [공용 ip 주소 만들기](virtual-network-public-ip-address.md#create-a-public-ip-address)를 참조 하세요. 주소는 각 Azure 지역에 사용 되는 공용 IP 주소 풀에서 할당 됩니다. 각 지역에서 사용 되는 주소 풀 목록을 보려면 [Microsoft Azure 데이터 센터 IP 범위](https://www.microsoft.com/download/details.aspx?id=41653)를 참조 하세요.

   - VM에 연결 된 네트워크 인터페이스의 이름을 모르는 경우 [az vm nic list](/cli/azure/vm/nic?view=azure-cli-latest#az-vm-nic-list) 명령을 사용 하 여이를 확인 합니다. 예를 들어 다음 명령은 *Myvm*이라는 리소스 그룹에서 *MYVM* 이라는 vm에 연결 된 네트워크 인터페이스의 이름을 나열 합니다.

     ```azurecli-interactive
     az vm nic list --vm-name myVM --resource-group myResourceGroup
     ```

     출력에는 다음 예제와 비슷한 줄이 하나 이상 포함 되어 있습니다.
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

     이전 예제에서 *Myvmvmnic* 는 네트워크 인터페이스의 이름입니다.

   - 네트워크 인터페이스에 대 한 IP 구성의 이름을 모르는 경우 [az network nic IP-config list](/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-list) 명령을 사용 하 여 검색 합니다. 예를 들어 다음 명령은 *Myresourcegroup*이라는 리소스 그룹에 있는 *Myvmvmnic* 라는 네트워크 인터페이스에 대 한 IP 구성의 이름을 나열 합니다.

     ```azurecli-interactive
     az network nic ip-config list --nic-name myVMVMNic --resource-group myResourceGroup --out table
     ```

3. [Az vm list-ip addresses](/cli/azure/vm?view=azure-cli-latest#az-vm-list-ip-addresses) 명령을 사용 하 여 ip 구성에 할당 된 공용 ip 주소를 확인 합니다. 다음 예제에서는 *Myvm*이라는 리소스 그룹에서 *MYVM* 이라는 기존 VM에 할당 된 IP 주소를 보여 줍니다.

   ```azurecli-interactive
   az vm list-ip-addresses --name myVM --resource-group myResourceGroup --out table
   ```

   > [!NOTE]
   > 주소는 각 Azure 지역에서 사용 되는 주소 풀에서 할당 됩니다. 각 지역에서 사용 되는 주소 풀 목록을 보려면 [Microsoft Azure 데이터 센터 IP 범위](https://www.microsoft.com/download/details.aspx?id=41653)를 참조 하세요. 할당 된 주소는 해당 지역에 사용 되는 풀의 모든 주소일 수 있습니다. 해당 지역의 특정 풀에서 주소를 할당 해야 하는 경우 [공용 IP 주소 접두사](public-ip-address-prefix.md)를 사용 합니다.

4. 네트워크 보안 그룹의 보안 규칙을 사용 하 여 [VM에 대 한 네트워크 트래픽을 허용](#allow-network-traffic-to-the-vm) 합니다.

## <a name="powershell"></a>PowerShell

[PowerShell](/powershell/azure/install-az-ps)을 설치 하거나 Azure Cloud Shell를 사용 합니다. Azure Cloud Shell은 Azure Portal에서 직접 실행할 수 있는 평가판 셸입니다. PowerShell이 미리 설치되어 계정에서 사용하도록 구성되어 있습니다. 다음 PowerShell 명령에서 **사용해 보기** 단추를 선택 합니다. **사용해 보기** 를 선택 하면 Azure 계정에 로그인 할 수 있는 Cloud Shell 호출 됩니다.

1. PowerShell을 로컬로 사용 하는 경우를 사용 하 `Connect-AzAccount`여 Azure에 로그인 합니다.
2. 공용 IP 주소는 VM에 연결 된 네트워크 인터페이스의 IP 구성에 연결 됩니다. [AzVirtualNetwork](/powershell/module/Az.Network/Get-AzVirtualNetwork) 및 [AzVirtualNetworkSubnetConfig](/powershell/module/Az.Network/Get-AzVirtualNetworkSubnetConfig) 명령을 사용 하 여 네트워크 인터페이스가 있는 가상 네트워크 및 서브넷을 가져옵니다. 그런 다음 [AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface) 명령을 사용 하 여 네트워크 인터페이스를 가져오고 [AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) 명령을 사용 하 여 기존 공용 IP 주소를 가져옵니다. 그런 다음 [AzNetworkInterfaceIpConfig](/powershell/module/Az.Network/Set-AzNetworkInterfaceIpConfig) 명령을 사용 하 여 공용 ip 주소를 IP 구성에 연결 하 고 [AzNetworkInterface](/powershell/module/Az.Network/Set-AzNetworkInterface) 명령을 사용 하 여 네트워크 인터페이스에 새 ip 구성을 작성 합니다.

   다음 예제에서는 *Myvmpublicip* 이라는 기존 공용 ip 주소를 *myvmpublicip*이라는 가상 네트워크에서 *Myvmsubnet* 이라는 서브넷에 있는 *myvmvmnic* 라는 기존 네트워크 인터페이스의 *ipconfigmyvm* 이라는 ip 구성에 연결 합니다. 모든 리소스는 *Myresourcegroup*이라는 리소스 그룹에 있습니다.
  
   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name myVMVNet -ResourceGroupName myResourceGroup
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name myVMSubnet -VirtualNetwork $vnet
   $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
   $pip = Get-AzPublicIpAddress -Name myVMPublicIP -ResourceGroupName myResourceGroup
   $nic | Set-AzNetworkInterfaceIpConfig -Name ipconfigmyVM -PublicIPAddress $pip -Subnet $subnet
   $nic | Set-AzNetworkInterface
   ```

   - 기존 공용 IP 주소가 없는 경우 AzPublicIpAddress 명령을 사용 하 여 [새로](/powershell/module/Az.Network/New-AzPublicIpAddress) 만듭니다. 예를 들어 다음 명령은 *에서는 eastus* 지역에 *myresourcegroup* 이라는 리소스 그룹에 *myvmpublicip* 라는 *동적* 공용 IP 주소를 만듭니다.
  
     ```azurepowershell-interactive
     New-AzPublicIpAddress -Name myVMPublicIP -ResourceGroupName myResourceGroup -AllocationMethod Dynamic -Location eastus
     ```

     > [!NOTE]
     > 이전 명령은 사용자 지정 하려는 여러 설정에 대 한 기본값을 사용 하 여 공용 IP 주소를 만듭니다. 모든 공용 IP 주소 설정에 대해 자세히 알아보려면 [공용 ip 주소 만들기](virtual-network-public-ip-address.md#create-a-public-ip-address)를 참조 하세요. 주소는 각 Azure 지역에 사용 되는 공용 IP 주소 풀에서 할당 됩니다. 각 지역에서 사용 되는 주소 풀 목록을 보려면 [Microsoft Azure 데이터 센터 IP 범위](https://www.microsoft.com/download/details.aspx?id=41653)를 참조 하세요.

   - VM에 연결 된 네트워크 인터페이스의 이름을 모르는 경우 [new-azvm](/powershell/module/Az.Compute/Get-AzVM) 명령을 사용 하 여 해당 인터페이스를 확인 합니다. 예를 들어 다음 명령은 *Myvm*이라는 리소스 그룹에서 *MYVM* 이라는 vm에 연결 된 네트워크 인터페이스의 이름을 나열 합니다.

     ```azurepowershell-interactive
     $vm = Get-AzVM -name myVM -ResourceGroupName myResourceGroup
     $vm.NetworkProfile
     ```

     출력에는 다음 예제와 비슷한 하나 이상의 줄이 포함 됩니다. 예제 출력에서 *Myvmvmnic* 는 네트워크 인터페이스의 이름입니다.
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

   - 네트워크 인터페이스가 있는 가상 네트워크 또는 서브넷의 이름을 모르는 경우 `Get-AzNetworkInterface` 명령을 사용 하 여 정보를 확인 합니다. 예를 들어 다음 명령은 *Myresourcegroup*이라는 리소스 그룹에 있는 *Myvmvmnic* 라는 네트워크 인터페이스에 대 한 가상 네트워크 및 서브넷 정보를 가져옵니다.

     ```azurepowershell-interactive
     $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
     $ipConfigs = $nic.IpConfigurations
     $ipConfigs.Subnet | Select Id
     ```

     출력에는 다음 예제와 비슷한 하나 이상의 줄이 포함 됩니다. 예제 출력에서 *Myvmvnet* 은 가상 네트워크의 이름이 고 *myvmsubnet* 은 서브넷의 이름입니다.
  
     ```
     "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVMVNET/subnets/myVMSubnet",
     ```

   - 네트워크 인터페이스에 대 한 IP 구성의 이름을 모르는 경우 [AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface) 명령을 사용 하 여 검색 합니다. 예를 들어 다음 명령은 *Myresourcegroup*이라는 리소스 그룹에 있는 *Myvmvmnic* 라는 네트워크 인터페이스에 대 한 IP 구성의 이름을 나열 합니다.

     ```azurepowershell-interactive
     $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
     $nic.IPConfigurations
     ```

     출력에는 다음 예제와 비슷한 하나 이상의 줄이 포함 됩니다. 예제 출력에서 *Ipconfigmyvm* 은 IP 구성의 이름입니다.
  
     ```
     Id     : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic/ipConfigurations/ipconfigmyVM
     ```

3. [AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) 명령을 사용 하 여 IP 구성에 할당 된 공용 ip 주소를 확인 합니다. 다음 예제에서는 *Myresourcegroup*이라는 리소스 그룹에서 *myvmpublicip* 라는 공용 IP 주소에 할당 된 주소를 보여 줍니다.

   ```azurepowershell-interactive
   Get-AzPublicIpAddress -Name myVMPublicIP -ResourceGroupName myResourceGroup | Select IpAddress
   ```

   IP 구성에 할당 된 공용 IP 주소의 이름을 모르는 경우 다음 명령을 실행 하 여 가져옵니다.

   ```azurepowershell-interactive
   $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
   $nic.IPConfigurations
   $address = $nic.IPConfigurations.PublicIpAddress
   $address | Select Id
   ```

   출력에는 다음 예제와 비슷한 하나 이상의 줄이 포함 됩니다. 예제 출력에서 *Myvmpublicip* 는 ip 구성에 할당 된 공용 ip 주소의 이름입니다.

   ```
   "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myVMPublicIP"
   ```

   > [!NOTE]
   > 주소는 각 Azure 지역에서 사용 되는 주소 풀에서 할당 됩니다. 각 지역에서 사용 되는 주소 풀 목록을 보려면 [Microsoft Azure 데이터 센터 IP 범위](https://www.microsoft.com/download/details.aspx?id=41653)를 참조 하세요. 할당 된 주소는 해당 지역에 사용 되는 풀의 모든 주소일 수 있습니다. 해당 지역의 특정 풀에서 주소를 할당 해야 하는 경우 [공용 IP 주소 접두사](public-ip-address-prefix.md)를 사용 합니다.

4. 네트워크 보안 그룹의 보안 규칙을 사용 하 여 [VM에 대 한 네트워크 트래픽을 허용](#allow-network-traffic-to-the-vm) 합니다.

## <a name="allow-network-traffic-to-the-vm"></a>VM에 대 한 네트워크 트래픽 허용

인터넷에서 공용 IP 주소에 연결 하려면 먼저 네트워크 인터페이스에 연결 되어 있을 수 있는 모든 네트워크 보안 그룹, 네트워크 인터페이스가 있는 서브넷 또는 둘 모두에 필요한 포트가 열려 있는지 확인 합니다. 보안 그룹이 네트워크 인터페이스의 개인 IP 주소에 대 한 트래픽을 필터링 하지만 인바운드 인터넷 트래픽이 공용 IP 주소에 도착 하면 Azure는 공용 주소를 개인 IP 주소로 변환 하므로 네트워크 보안 그룹에서 트래픽 흐름을 방지 하는 경우 공용 IP 주소와의 통신이 실패 합니다. [Portal](diagnose-network-traffic-filter-problem.md#diagnose-using-azure-portal), [CLI](diagnose-network-traffic-filter-problem.md#diagnose-using-azure-cli)또는 [PowerShell](diagnose-network-traffic-filter-problem.md#diagnose-using-powershell)을 사용 하 여 네트워크 인터페이스 및 서브넷에 대 한 유효 보안 규칙을 볼 수 있습니다.

## <a name="next-steps"></a>다음 단계

네트워크 보안 그룹을 사용 하 여 VM에 대 한 인바운드 인터넷 트래픽을 허용 합니다. 네트워크 보안 그룹을 만드는 방법에 대 한 자세한 내용은 [네트워크 보안 그룹 작업](manage-network-security-group.md#work-with-network-security-groups)을 참조 하세요. 네트워크 보안 그룹에 대 한 자세한 내용은 [보안 그룹](security-overview.md)을 참조 하세요.
