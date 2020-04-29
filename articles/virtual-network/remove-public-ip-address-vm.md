---
title: Azure VM에서 공용 IP 주소 분리
titlesuffix: Azure Virtual Network
description: VM에서 공용 IP 주소를 분리 하는 방법을 알아봅니다.
services: virtual-network
documentationcenter: ''
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/04/2019
ms.author: allensu
ms.openlocfilehash: f29e29f809faeeb486e5b6b9bacc84a61380a012
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82144466"
---
# <a name="dissociate-a-public-ip-address-from-an-azure-vm"></a>Azure VM에서 공용 IP 주소 분리 

이 문서에서는 Azure VM (가상 머신)에서 공용 IP 주소를 분리 하는 방법에 대해 알아봅니다.

[Azure Portal](#azure-portal), Azure CLI ( [명령줄 인터페이스](#azure-cli) ) 또는 [POWERSHELL](#powershell) 을 사용 하 여 VM에서 공용 IP 주소를 분리할 수 있습니다.

## <a name="azure-portal"></a>Azure portal

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 공용 IP 주소의 분리를 해제할 가상 컴퓨터를 찾아보거나 검색 한 다음 선택 합니다.
3. VM 페이지에서 **개요**를 선택 하 고 다음 그림과 같이 공용 IP 주소를 선택 합니다.

   ![공용 IP 선택](./media/remove-public-ip-address/remove-public-ip-address-2.png)

4. 다음 그림에 표시 된 것 처럼 공용 **Overview**IP 주소 페이지에서 개요 **를 선택 하**고 분리를 선택 합니다.

    ![공용 IP 분리](./media/remove-public-ip-address/remove-public-ip-address-3.png)

5. **공용 IP 주소**분리에서 **예**를 선택 합니다.

## <a name="azure-cli"></a>Azure CLI

[Azure CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)를 설치 하거나 Azure Cloud Shell를 사용 합니다. Azure Cloud Shell은 Azure Portal에서 직접 실행할 수 있는 평가판 Bash 셸입니다. Azure CLI가 사전 설치되어 계정에서 사용하도록 구성되어 있습니다. 다음 CLI 명령에서 **사용해 보기** 단추를 선택 합니다. **사용해 보기** 를 선택 하면 Azure 계정에 로그인 할 수 있는 Cloud Shell 호출 됩니다.

1. 로컬로 CLI를 Bash에서 사용 하는 경우를 사용 하 여 `az login`Azure에 로그인 합니다.
2. 공용 IP 주소는 VM에 연결 된 네트워크 인터페이스의 IP 구성에 연결 됩니다. [Az network nic-ip-config update](/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-update) 명령을 사용 하 여 ip 구성에서 공용 ip 주소를 분리 합니다. 다음 예제에서는 *Myresourcegroup*이라는 리소스 그룹에서 *MYVM* 이라는 Vm에 연결 된 *myvmvmnic* 라는 기존 네트워크 인터페이스의 *Ipconfigmyvm* 이라는 IP 구성에서 *myvmpublicip* 라는 공용 ip 주소를 분리 합니다.
  
   ```azurecli-interactive
    az network nic ip-config update \
    --name ipconfigmyVM \
    --resource-group myResourceGroup \
    --nic-name myVMVMNic \
    --remove PublicIpAddress
   ```

   VM에 연결 된 네트워크 인터페이스의 이름을 모르는 경우 [az vm nic list](/cli/azure/vm/nic?view=azure-cli-latest#az-vm-nic-list) 명령을 사용 하 여이를 확인 합니다. 예를 들어 다음 명령은 *Myvm*이라는 리소스 그룹에서 *MYVM* 이라는 vm에 연결 된 네트워크 인터페이스의 이름을 나열 합니다.

     ```azurecli-interactive
     az vm nic list --vm-name myVM --resource-group myResourceGroup
     ```

     출력에는 다음 예제와 비슷한 줄이 하나 이상 포함 되어 있습니다.
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

     이전 예제에서 *Myvmvmnic* 는 네트워크 인터페이스의 이름입니다.

   - 네트워크 인터페이스에 대 한 IP 구성의 이름을 모르는 경우 [az network nic IP-config list](/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-list) 명령을 사용 하 여 검색 합니다. 예를 들어 다음 명령은 *Myresourcegroup*이라는 리소스 그룹에 있는 *Myvmvmnic* 라는 네트워크 인터페이스에 대 한 공용 IP 구성의 이름을 나열 합니다.

     ```azurecli-interactive
     az network nic ip-config list --nic-name myVMVMNic --resource-group myResourceGroup --out table
     ```

   - 네트워크 인터페이스에 대 한 공용 IP 구성의 이름을 모르는 경우 [az network nic ip-https show](/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-show) 명령을 사용 하 여 검색 합니다. 예를 들어 다음 명령은 *Myresourcegroup*이라는 리소스 그룹에 있는 *Myvmvmnic* 라는 네트워크 인터페이스에 대 한 공용 IP 구성의 이름을 나열 합니다.

     ```azurecli-interactive
     az network nic ip-config show --name ipconfigmyVM --nic-name myVMVMNic --resource-group myResourceGroup --query publicIPAddress.id
     ```


## <a name="powershell"></a>PowerShell

[PowerShell](/powershell/azure/install-az-ps)을 설치 하거나 Azure Cloud Shell를 사용 합니다. Azure Cloud Shell은 Azure Portal에서 직접 실행할 수 있는 평가판 셸입니다. PowerShell이 미리 설치되어 계정에서 사용하도록 구성되어 있습니다. 다음 PowerShell 명령에서 **사용해 보기** 단추를 선택 합니다. **사용해 보기** 를 선택 하면 Azure 계정에 로그인 할 수 있는 Cloud Shell 호출 됩니다.

1. PowerShell을 로컬로 사용 하는 경우를 사용 하 `Connect-AzAccount`여 Azure에 로그인 합니다.
2. 공용 IP 주소는 VM에 연결 된 네트워크 인터페이스의 IP 구성에 연결 됩니다. [AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface) 명령을 사용 하 여 네트워크 인터페이스를 가져옵니다. 공용 IP 주소 값을 null로 설정 하 고 [AzNetworkInterface](/powershell/module/Az.Network/Set-AzNetworkInterface) 명령을 사용 하 여 네트워크 인터페이스에 새 IP 구성을 씁니다.

   다음 예제에서는 *Myvm*이라는 VM에 연결 된 *Myvmvmnic* 라는 네트워크 인터페이스에서 *MYVMPUBLICIP* 라는 공용 IP 주소를 분리 합니다. 모든 리소스는 *Myresourcegroup*이라는 리소스 그룹에 있습니다.
  
   ```azurepowershell
    $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroup myResourceGroup
    $nic.IpConfigurations.publicipaddress.id = $null
    Set-AzNetworkInterface -NetworkInterface $nic
   ```

  - VM에 연결 된 네트워크 인터페이스의 이름을 모르는 경우 [new-azvm](/powershell/module/Az.Compute/Get-AzVM) 명령을 사용 하 여 해당 인터페이스를 확인 합니다. 예를 들어 다음 명령은 *Myvm*이라는 리소스 그룹에서 *MYVM* 이라는 vm에 연결 된 네트워크 인터페이스의 이름을 나열 합니다.

    ```azurepowershell
    $vm = Get-AzVM -name myVM -ResourceGroupName myResourceGroup
    $vm.NetworkProfile
    ```

     출력에는 다음 예제와 비슷한 하나 이상의 줄이 포함 됩니다. 예제 출력에서 *Myvmvmnic* 는 네트워크 인터페이스의 이름입니다.
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

   - 네트워크 인터페이스에 대 한 IP 구성의 이름을 모르는 경우 [AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface) 명령을 사용 하 여 검색 합니다. 예를 들어 다음 명령은 *Myresourcegroup*이라는 리소스 그룹에 있는 *Myvmvmnic* 라는 네트워크 인터페이스에 대 한 IP 구성의 이름을 나열 합니다.

     ```azurepowershell-interactive
     $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
     $nic.IPConfigurations.id
     ```

     출력에는 다음 예제와 비슷한 하나 이상의 줄이 포함 됩니다. 예제 출력에서 *Ipconfigmyvm* 은 IP 구성의 이름입니다.
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic/ipConfigurations/ipconfigmyVM"
     ```

## <a name="next-steps"></a>다음 단계

- [VM에 공용 IP 주소를 연결](associate-public-ip-address-vm.md)하는 방법에 대해 알아봅니다.
