---
title: Azure VM에서 공용 IP 주소를 분리
titlesuffix: Azure Virtual Network
description: VM에서 공용 IP 주소를 분리하는 방법 알아보기
services: virtual-network
documentationcenter: ''
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/04/2019
ms.author: kumud
ms.openlocfilehash: 1c27af30f97ea967d170b2cccaefb2e95f8fedaf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74900753"
---
# <a name="dissociate-a-public-ip-address-from-an-azure-vm"></a>Azure VM에서 공용 IP 주소를 분리 

이 문서에서는 Azure 가상 시스템(VM)에서 공용 IP 주소를 분리하는 방법을 배웁니다.

[Azure 포털,](#azure-portal)CLI(Azure [명령줄 인터페이스)](#azure-cli) 또는 [PowerShell을](#powershell) 사용하여 VM에서 공용 IP 주소를 분리할 수 있습니다.

## <a name="azure-portal"></a>Azure portal

1. [Azure 포털에](https://portal.azure.com)로그인합니다.
2. 공용 IP 주소의 연결을 해제하려는 가상 컴퓨터를 검색하거나 검색한 다음 선택합니다.
3. VM 페이지에서 **개요를**선택하여 다음 그림과 같이 공용 IP 주소를 선택합니다.

   ![공용 IP 선택](./media/remove-public-ip-address/remove-public-ip-address-2.png)

4. 공용 IP 주소 페이지에서 **개요를**선택한 다음 다음 그림과 같이 **구분 해제를**선택합니다.

    ![공용 IP 해리](./media/remove-public-ip-address/remove-public-ip-address-3.png)

5. 공용 **IP 주소 분리에서** **예**를 선택합니다.

## <a name="azure-cli"></a>Azure CLI

Azure [CLI를](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)설치하거나 Azure 클라우드 셸을 사용합니다. Azure Cloud Shell은 Azure Portal에서 직접 실행할 수 있는 평가판 Bash 셸입니다. Azure CLI가 사전 설치되어 계정에서 사용하도록 구성되어 있습니다. 다음 CLI 명령에서 **시도** 단추를 선택합니다. **시도 를 선택하면** Azure 계정에 로그인할 수 있는 클라우드 셸이 호출됩니다.

1. Bash에서 로컬로 CLI를 사용하는 경우 `az login`을 사용하여 Azure에 로그인합니다.
2. 공용 IP 주소는 VM에 연결된 네트워크 인터페이스의 IP 구성에 연결됩니다. AZ [네트워크 nic-ip-config 업데이트](/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-update) 명령을 사용하여 IP 구성에서 공용 IP 주소를 분리합니다. 다음 예제에서는 *myResourceGroup이라는*리소스 그룹의 vM에 연결된 *myVMMMNic이라는* 기존 네트워크 인터페이스의 *ipconfigmyVM이라는* IP 구성에서 *myVMPublicIP라는* 공용 IP 주소를 분리합니다. *myVMPublicIP*
  
   ```azurecli-interactive
    az network nic ip-config update \
    --name ipconfigmyVM \
    --resource-group myResourceGroup \
    --nic-name myVMVMNic \
    --remove PublicIpAddress
   ```

   VM에 연결된 네트워크 인터페이스의 이름을 모르는 경우 [az vm nic 목록](/cli/azure/vm/nic?view=azure-cli-latest#az-vm-nic-list) 명령을 사용하여 해당 인터페이스를 봅니다. 예를 들어 다음 명령은 *myResourceGroup이라는*리소스 그룹에서 *myVM이라는* VM에 연결된 네트워크 인터페이스의 이름을 나열합니다.

     ```azurecli-interactive
     az vm nic list --vm-name myVM --resource-group myResourceGroup
     ```

     출력에는 다음 예제와 유사한 하나 이상의 선이 포함됩니다.
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

     이전 예제에서 *myVMMNic은* 네트워크 인터페이스의 이름입니다.

   - 네트워크 인터페이스에 대한 IP 구성의 이름을 모르는 경우 [az 네트워크 nic ip-config 목록](/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-list) 명령을 사용하여 검색합니다. 예를 들어 다음 명령은 *myResourceGroup이라는*리소스 그룹에서 *myVMMNic이라는* 네트워크 인터페이스에 대한 공용 IP 구성의 이름을 나열합니다.

     ```azurecli-interactive
     az network nic ip-config list --nic-name myVMVMNic --resource-group myResourceGroup --out table
     ```

   - 네트워크 인터페이스에 대한 공용 IP 구성의 이름을 모르는 경우 [az 네트워크 nic ip-config 표시](/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-show) 명령을 사용하여 검색합니다. 예를 들어 다음 명령은 *myResourceGroup이라는*리소스 그룹에서 *myVMMNic이라는* 네트워크 인터페이스에 대한 공용 IP 구성의 이름을 나열합니다.

     ```azurecli-interactive
     az network nic ip-config show --name ipconfigmyVM --nic-name myVMVMNic --resource-group myResourceGroup --query publicIPAddress.id
     ```


## <a name="powershell"></a>PowerShell

[PowerShell을](/powershell/azure/install-az-ps)설치하거나 Azure 클라우드 셸을 사용합니다. Azure Cloud Shell은 Azure Portal에서 직접 실행할 수 있는 평가판 셸입니다. PowerShell이 미리 설치되어 계정에서 사용하도록 구성되어 있습니다. 다음 PowerShell 명령에서 **시도** 단추를 선택합니다. **시도 를 선택하면** Azure 계정에 로그인할 수 있는 클라우드 셸이 호출됩니다.

1. PowerShell을 로컬로 사용하는 경우 `Connect-AzAccount`을 사용하여 Azure에 로그인합니다.
2. 공용 IP 주소는 VM에 연결된 네트워크 인터페이스의 IP 구성에 연결됩니다. 네트워크 인터페이스를 얻으려면 [AzNetworkInterface Get를](/powershell/module/Az.Network/Get-AzNetworkInterface) 사용합니다. 공용 IP 주소 값을 null로 설정한 다음 [Set-AzNetworkInterface](/powershell/module/Az.Network/Set-AzNetworkInterface) 명령을 사용하여 새 IP 구성을 네트워크 인터페이스에 작성합니다.

   다음 예제는 myVM이라는 이름의 VM에 연결된 *myVMVMNic이라는* 네트워크 인터페이스에서 *myVMPublicIP라는* 공용 IP 주소를 분리합니다. *myVM* 모든 리소스는 *myResourceGroup이라는*리소스 그룹에 있습니다.
  
   ```azurepowershell
    $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroup myResourceGroup
    $nic.IpConfigurations.publicipaddress.id = $null
    Set-AzNetworkInterface -NetworkInterface $nic
   ```

  - VM에 연결된 네트워크 인터페이스의 이름을 모르는 경우 [Get-AzVM](/powershell/module/Az.Compute/Get-AzVM) 명령을 사용하여 해당 인터페이스를 봅니다. 예를 들어 다음 명령은 *myResourceGroup이라는*리소스 그룹에서 *myVM이라는* VM에 연결된 네트워크 인터페이스의 이름을 나열합니다.

    ```azurepowershell
    $vm = Get-AzVM -name myVM -ResourceGroupName myResourceGroup
    $vm.NetworkProfile
    ```

     출력에는 다음 예제와 유사한 하나 이상의 선이 포함됩니다. 예제 출력에서 *myVMMNic은* 네트워크 인터페이스의 이름입니다.
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

   - 네트워크 인터페이스에 대한 IP 구성의 이름을 모르는 경우 [Get-AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface) 명령을 사용하여 검색합니다. 예를 들어 다음 명령은 *myResourceGroup이라는*리소스 그룹에 *myVMMMNic이라는* 네트워크 인터페이스에 대한 IP 구성의 이름을 나열합니다.

     ```azurepowershell-interactive
     $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
     $nic.IPConfigurations.id
     ```

     출력에는 다음 예제와 유사한 하나 이상의 선이 포함됩니다. 예제 출력에서 *ipconfigmyVM은* IP 구성의 이름입니다.
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic/ipConfigurations/ipconfigmyVM"
     ```

## <a name="next-steps"></a>다음 단계

- [공용 IP 주소를 VM에 연결하는](associate-public-ip-address-vm.md)방법에 대해 알아봅니다.
