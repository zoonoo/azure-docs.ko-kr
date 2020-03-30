---
title: Azure 가상 네트워크에서 서브넷 위임 추가 또는 제거
titlesuffix: Azure Virtual Network
description: Azure에서 서비스에 대해 위임된 서브넷을 추가하거나 제거하는 방법에 대해 알아봅니다.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/06/2019
ms.author: kumud
ms.openlocfilehash: 6f767abdf8673e3adffc6c4e3748733054ba723d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77201869"
---
# <a name="add-or-remove-a-subnet-delegation"></a>서브넷 위임 추가 또는 제거

서브넷 위임은 서비스를 배포할 때 고유 식별자를 사용하여 서브넷에서 서비스 특정 리소스를 만들 수 있는 서비스에 대한 명시적 권한을 제공합니다. 이 문서에서는 Azure 서비스에 대해 위임된 서브넷을 추가하거나 제거하는 방법을 설명합니다.

## <a name="portal"></a>포털

### <a name="sign-in-to-azure"></a>Azure에 로그인

[https://portal.azure.com](https://portal.azure.com) 에서 Azure Portal에 로그인합니다.

### <a name="create-the-virtual-network"></a>가상 네트워크 만들기

이 섹션에서는 나중에 Azure 서비스에 위임할 가상 네트워크와 서브넷을 만듭니다.

1. 화면 왼쪽 상단에서 > 리소스**네트워킹** > **가상 네트워크** **만들기를**선택합니다.
1. **가상 네트워크 만들기**에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | 이름 | 마이가상 네트워크 를 *입력합니다.* |
    | 주소 공간 | *10.0.0.0/16*을 입력합니다. |
    | Subscription | 구독을 선택합니다.|
    | Resource group | **새로 만들기**를 선택하고 *myResourceGroup*을 입력한 다음, **확인**을 선택합니다. |
    | 위치 | **EastUS를**선택합니다.|
    | 서브넷 - 이름 | *mySubnet*을 입력합니다. |
    | 서브넷 - 주소 범위 | *10.0.0.0/24*를 입력합니다. |
    |||
1. 나머지는 기본값으로 그대로 두고 **.**

### <a name="permissions"></a>사용 권한

Azure 서비스에 위임하려는 서브넷을 만들지 않은 경우 다음 권한이 `Microsoft.Network/virtualNetworks/subnets/write`필요합니다.

기본 제공 [네트워크 기여자](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) 역할에는 필요한 사용 권한도 포함되어 있습니다.

### <a name="delegate-a-subnet-to-an-azure-service"></a>Azure 서비스에 서브넷 위임

이 섹션에서는 이전 섹션에서 만든 서브넷을 Azure 서비스에 위임합니다.

1. 포털의 검색 창에서 *myVirtualNetwork*를 입력합니다. 검색 결과에 **myVirtualNetwork**가 표시되면 선택합니다.
2. 검색 결과에서 *myVirtualNetwork*를 선택합니다.
3. **설정에서** **서브넷을**선택한 다음 **mySubnet**을 선택합니다.
4. *mySubnet* 페이지에서 **서브넷 위임** 목록에 대해 대리자 서브넷 아래에 나열된 서비스(예: **Microsoft.DBforPostgreSQL/serversv2)에서** **선택합니다.**  

### <a name="remove-subnet-delegation-from-an-azure-service"></a>Azure 서비스에서 서브넷 위임 제거

1. 포털의 검색 창에서 *myVirtualNetwork*를 입력합니다. 검색 결과에 **myVirtualNetwork**가 표시되면 선택합니다.
2. 검색 결과에서 *myVirtualNetwork*를 선택합니다.
3. **설정에서** **서브넷을**선택한 다음 **mySubnet**을 선택합니다.
4. *mySubnet* 페이지에서 **Subnet 위임** 목록에 대해 대리자 서브넷 아래에 나열된 서비스에서 **없음을** **선택합니다.** 

## <a name="azure-cli"></a>Azure CLI

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

대신 로컬로 Azure CLI를 설치하고 사용하기로 결정한 경우 이 문서에서는 Azure CLI 버전 2.0.28 이상을 사용해야 합니다. 설치된 버전을 확인하려면 `az --version`을 실행합니다. 설치 또는 업그레이드 정보는 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

### <a name="create-a-resource-group"></a>리소스 그룹 만들기
[az group create](https://docs.microsoft.com/cli/azure/group)를 사용하여 리소스 그룹을 만듭니다. Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다.

다음 예제에서는 **동쪽** 위치에 **myResourceGroup이라는** 리소스 그룹을 만듭니다.

```azurecli-interactive

  az group create \
    --name myResourceGroup \
    --location eastus

```

### <a name="create-a-virtual-network"></a>가상 네트워크 만들기
[az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet)를 사용하여 **myResourceGroup**에 **mySubnet**이라는 서브넷이 있는 **myVnet** 가상 네트워크를 만듭니다.

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myVnet \
    --address-prefix 10.0.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 10.0.0.0/24
```
### <a name="permissions"></a>사용 권한

Azure 서비스에 위임하려는 서브넷을 만들지 않은 경우 다음 권한이 `Microsoft.Network/virtualNetworks/subnets/write`필요합니다.

기본 제공 [네트워크 기여자](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) 역할에는 필요한 사용 권한도 포함되어 있습니다.

### <a name="delegate-a-subnet-to-an-azure-service"></a>Azure 서비스에 서브넷 위임

이 섹션에서는 이전 섹션에서 만든 서브넷을 Azure 서비스에 위임합니다. 

[az 네트워크 vnet 서브넷 업데이트를](https://docs.microsoft.com/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-update) 사용하여 **mySubnet이라는** 서브넷을 Azure 서비스에 대한 위임으로 업데이트합니다.  이 예제에서 **Microsoft.DBforPostgreSQL/serversv2는** 예제 위임에 사용됩니다.

```azurecli-interactive
  az network vnet subnet update \
  --resource-group myResourceGroup \
  --name mySubnet \
  --vnet-name myVnet \
  --delegations Microsoft.DBforPostgreSQL/serversv2
```

위임이 적용되었는지 확인하려면 [az 네트워크 vnet 서브넷 쇼를](https://docs.microsoft.com/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-show)사용합니다. 서비스가 속성 **서비스이름**아래 서브넷에 위임되었는지 확인합니다.

```azurecli-interactive
  az network vnet subnet show \
  --resource-group myResourceGroup \
  --name mySubnet \
  --vnet-name myVnet \
  --query delegations
```

```json
[
  {
    "actions": [
      "Microsoft.Network/virtualNetworks/subnets/join/action"
    ],
    "etag": "W/\"8a8bf16a-38cf-409f-9434-fe3b5ab9ae54\"",
    "id": "/subscriptions/3bf09329-ca61-4fee-88cb-7e30b9ee305b/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet/delegations/0",
    "name": "0",
    "provisioningState": "Succeeded",
    "resourceGroup": "myResourceGroup",
    "serviceName": "Microsoft.DBforPostgreSQL/serversv2",
    "type": "Microsoft.Network/virtualNetworks/subnets/delegations"
  }
]
```

### <a name="remove-subnet-delegation-from-an-azure-service"></a>Azure 서비스에서 서브넷 위임 제거

[az 네트워크 vnet 서브넷 업데이트를](https://docs.microsoft.com/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-update) 사용하여 **mySubnet이라는**서브넷에서 위임을 제거합니다.

```azurecli-interactive
  az network vnet subnet update \
  --resource-group myResourceGroup \
  --name mySubnet \
  --vnet-name myVnet \
  --remove delegations
```
위임이 제거되었는지 확인하려면 [az 네트워크 vnet 서브넷 쇼를](https://docs.microsoft.com/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-show)사용합니다. 속성 **serviceName**아래의 서브넷에서 서비스가 제거되었는지 확인합니다.

```azurecli-interactive
  az network vnet subnet show \
  --resource-group myResourceGroup \
  --name mySubnet \
  --vnet-name myVnet \
  --query delegations
```
명령에서 출력은 null 대괄호입니다.
```json
[]
```

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="connect-to-azure"></a>Azure에 연결

```azurepowershell-interactive
  Connect-AzAccount
```

### <a name="create-a-resource-group"></a>리소스 그룹 만들기
[New-AzResourceGroup](https://docs.microsoft.com/cli/azure/group)을 사용하여 리소스 그룹을 만듭니다. Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다.

다음 예제에서는 *동쪽* 위치에 *myResourceGroup이라는* 리소스 그룹을 만듭니다.

```azurepowershell-interactive
  New-AzResourceGroup -Name myResourceGroup -Location eastus
```
### <a name="create-virtual-network"></a>가상 네트워크 만들기

new-AzVirtualNetwork를 사용하여 **myResourceGroup에서** [new-AzVirtualNetworkSubnetConfig를](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig?view=latest) 사용하여 **mySubnet이라는** 서브넷으로 **myVnet이라는** 가상 [네트워크를](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork?view=latest)만듭니다. 가상 네트워크의 IP 주소 공간은 **10.0.0.0/16입니다.** 가상 네트워크 내의 서브넷은 **10.0.0.0/24입니다.**  

```azurepowershell-interactive
  $subnet = New-AzVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix "10.0.0.0/24"

  New-AzVirtualNetwork -Name myVnet -ResourceGroupName myResourceGroup -Location eastus -AddressPrefix "10.0.0.0/16" -Subnet $subnet
```
### <a name="permissions"></a>사용 권한

Azure 서비스에 위임하려는 서브넷을 만들지 않은 경우 다음 권한이 `Microsoft.Network/virtualNetworks/subnets/write`필요합니다.

기본 제공 [네트워크 기여자](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) 역할에는 필요한 사용 권한도 포함되어 있습니다.

### <a name="delegate-a-subnet-to-an-azure-service"></a>Azure 서비스에 서브넷 위임

이 섹션에서는 이전 섹션에서 만든 서브넷을 Azure 서비스에 위임합니다. 

[Add-AzDelegation를](https://docs.microsoft.com/powershell/module/az.network/add-azdelegation?view=latest) 사용하여 **mySubnet이라는** 서브넷을 Azure 서비스에 **myDelegation라는** 위임으로 업데이트합니다.  이 예제에서 **Microsoft.DBforPostgreSQL/serversv2는** 예제 위임에 사용됩니다.

```azurepowershell-interactive
  $vnet = Get-AzVirtualNetwork -Name "myVNet" -ResourceGroupName "myResourceGroup"
  $subnet = Get-AzVirtualNetworkSubnetConfig -Name "mySubnet" -VirtualNetwork $vnet
  $subnet = Add-AzDelegation -Name "myDelegation" -ServiceName "Microsoft.DBforPostgreSQL/serversv2" -Subnet $subnet
  Set-AzVirtualNetwork -VirtualNetwork $vnet
```
[Get-AzDelegation를](https://docs.microsoft.com/powershell/module/az.network/get-azdelegation?view=latest) 사용하여 위임을 확인합니다.

```azurepowershell-interactive
  $subnet = Get-AzVirtualNetwork -Name "myVnet" -ResourceGroupName "myResourceGroup" | Get-AzVirtualNetworkSubnetConfig -Name "mySubnet"
  Get-AzDelegation -Name "myDelegation" -Subnet $subnet

  ProvisioningState : Succeeded
  ServiceName       : Microsoft.DBforPostgreSQL/serversv2
  Actions           : {Microsoft.Network/virtualNetworks/subnets/join/action}
  Name              : myDelegation
  Etag              : W/"9cba4b0e-2ceb-444b-b553-454f8da07d8a"
  Id                : /subscriptions/3bf09329-ca61-4fee-88cb-7e30b9ee305b/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet/delegations/myDelegation

```
### <a name="remove-subnet-delegation-from-an-azure-service"></a>Azure 서비스에서 서브넷 위임 제거

[제거-AzDelegation를](https://docs.microsoft.com/powershell/module/az.network/remove-azdelegation?view=latest) 사용하여 **mySubnet이라는**서브넷에서 위임을 제거합니다.

```azurepowershell-interactive
  $vnet = Get-AzVirtualNetwork -Name "myVnet" -ResourceGroupName "myResourceGroup"
  $subnet = Get-AzVirtualNetworkSubnetConfig -Name "mySubnet" -VirtualNetwork $vnet
  $subnet = Remove-AzDelegation -Name "myDelegation" -Subnet $subnet
  Set-AzVirtualNetwork -VirtualNetwork $vnet
```
[Get-AzDelegation를](https://docs.microsoft.com/powershell/module/az.network/get-azdelegation?view=latest) 사용하여 위임이 제거되었는지 확인합니다.

```azurepowershell-interactive
  $subnet = Get-AzVirtualNetwork -Name "myVnet" -ResourceGroupName "myResourceGroup" | Get-AzVirtualNetworkSubnetConfig -Name "mySubnet"
  Get-AzDelegation -Name "myDelegation" -Subnet $subnet

  Get-AzDelegation: Sequence contains no matching element

```

## <a name="next-steps"></a>다음 단계
- [Azure 에서 서브넷을 관리하는](virtual-network-manage-subnet.md)방법에 대해 알아봅니다.
