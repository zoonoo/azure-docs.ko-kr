---
title: Azure Virtual Network 피어링 만들기 - Resource Manager - 동일한 구독 | Microsoft Docs
description: Resource Manager를 통해 만든, 같은 Azure 구독에 있는 가상 네트워크 간에 가상 네트워크 피어링을 만드는 방법을 알아봅니다.
services: virtual-network
documentationcenter: ''
author: jimdial
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: 026bca75-2946-4c03-b4f6-9f3c5809c69a
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: anavin;jdial
ms.openlocfilehash: 70fe948070147c01922fab68fb55a0f00c26a0f3
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/07/2018
ms.locfileid: "29767141"
---
# <a name="create-a-virtual-network-peering---resource-manager-same-subscription"></a>가상 네트워크 피어링 만들기 - Resource Manager, 동일한 구독

이 자습서에서는 Resource Manager를 통해 만든 가상 네트워크 간의 가상 네트워크 피어링을 만드는 방법을 알아봅니다. 두 가상 네트워크는 동일한 구독에 있어야 합니다. 두 가상 네트워크를 피어링하면 서로 다른 가상 네트워크에 있는 리소스가 같은 가상 네트워크에 있는 리소스인 것처럼 같은 대역폭 및 대기 시간으로 서로 통신할 수 있습니다. [가상 네트워크 피어링](virtual-network-peering-overview.md)에 대해 자세히 알아보세요. 

가상 네트워크 피어링을 만드는 단계는 가상 네트워크가 동일한 구독에 있는지 아니면 다른 구독에 있는지에 따라, 그리고 가상 네트워크가 어느 [Azure 배포 모델](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json)를 통해 생성되었는지에 따라 다릅니다. 다음 표에 나온 시나리오를 클릭하여 다른 시나리오에서 가상 네트워크 피어링을 만드는 방법을 알아보세요.

|Azure 배포 모델  | Azure 구독  |
|--------- |---------|
|[둘 다 Resource Manager](create-peering-different-subscriptions.md) |다름|
|[하나는 리소스 관리자, 다른 하나는 클래식](create-peering-different-deployment-models.md) |동일|
|[하나는 Resource Manager, 하나는 클래식](create-peering-different-deployment-models-subscriptions.md) |다름|

클래식 배포 모델을 통해 배포된 두 가상 네트워크 간에는 가상 네트워크 피어링을 만들 수 없습니다. 둘 다 클래식 배포 모델을 통해 생성된 가상 네트워크를 연결해야 할 경우 Azure [VPN Gateway](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)를 사용하여 가상 네트워크를 연결할 수 있습니다. 

이 자습서는 동일한 지역에 가상 네트워크를 피어링합니다. 서로 다른 지역에서 가상 네트워크를 피어링하는 기능은 현재 미리 보기 상태입니다. 서로 다른 지역에서 가상 네트워크 피어링을 시도하기 전에 [전역 가상 네트워크 피어링 등록](#register)의 단계를 완료하도록 합니다. 그렇지 않으면 피어링은 실패합니다. Azure [VPN Gateway](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)를 사용하여 서로 다른 지역에서 가상 네트워크를 연결하는 기능을 일반적으로 사용할 수 있으며 등록이 필요하지 않습니다.

[Azure Portal](#portal), Azure CLI([명령줄 인터페이스](#cli)), Azure [PowerShell](#powershell) 또는 [Azure Resource Manager 템플릿](#template)을 사용하여 가상 네트워크 피어링을 만들 수 있습니다. 앞의 도구 링크 중 원하는 도구 링크를 클릭하여 원하는 도구를 사용하여 가상 네트워크 피어링을 만드는 단계로 바로 이동하세요.

## <a name="portal"></a>피어링 만들기 - Azure Portal

1. [Azure 포털](https://portal.azure.com) 에 로그인합니다. 로그인하는 데 사용하는 계정에 가상 네트워크 피어링을 만드는 데 필요한 권한이 있어야 합니다. 자세한 내용은 이 문서의 [권한](#permissions) 섹션을 참조하세요.
2. **+ 새로 만들기**, **네트워킹**, **가상 네트워크**를 차례로 클릭합니다.
3. **가상 네트워크 만들기** 블레이드에서 다음 설정에 대한 값을 입력하거나 선택한 다음 **만들기**를 클릭합니다.
    - **이름**: *myVnet1*
    - **주소 공간**: *10.0.0.0/16*
    - **서브넷 이름**: *기본값*
    - **서브넷 주소 범위**: *10.0.0.0/24*
    - **구독**: 사용자의 구독을 선택합니다.
    - **리소스 그룹**: **새로 만들기**를 선택하고 *myResourceGroup*을 입력합니다.
    - **위치**: *미국 동부*
4. 3단계에서 다음 값을 지정하여 2~3단계를 다시 완료합니다.
    - **이름**: *myVnet2*
    - **주소 공간**: *10.1.0.0/16*
    - **서브넷 이름**: *기본값*
    - **서브넷 주소 범위**: *10.1.0.0/24*
    - **구독**: 사용자의 구독을 선택합니다.
    - **리소스 그룹**: **기존 항목 사용**을 선택하고 *myResourceGroup*을 선택합니다.
    - **위치**: *미국 동부*
5. 포털 위쪽에 있는 **리소스 검색** 상자에 *myResourceGroup*을 입력합니다. **myResourceGroup**이 검색 결과에 표시되면 클릭합니다. **myresourcegroup** 리소스 그룹에 대한 블레이드가 나타납니다. 리소스 그룹에는 이전 단계에서 만든 두 가상 네트워크가 포함되어 있습니다.
6. **myVNet1**을 클릭합니다.
7. 나타나는 **myVnet1** 블레이드의 왼쪽에 있는 세로 옵션 목록에서 **피어링**을 클릭합니다.
8. 나타난 **myVnet1 - 피어링** 블레이드에서 **+ 추가**를 클릭합니다.
9. 나타나는 **피어링 추가** 블레이드에서 다음 옵션을 입력하거나 선택한 다음 **확인**을 클릭합니다.
     - **이름**: *myVnet1ToMyVnet2*
     - **가상 네트워크 배포 모델**: **Resource Manager**를 선택합니다. 
     - **구독**: 사용자의 구독을 선택합니다.
     - **가상 네트워크**: **가상 네트워크 선택**을 클릭한 다음 **myVnet2**를 클릭합니다.
     - **가상 네트워크 액세스 허용:** **사용**이 선택되어 있는지 확인합니다.
    이 자습서에서 다른 설정은 사용되지 않습니다. 모든 피어링 설정에 대해 알아보려면 [가상 네트워크 피어링 관리](virtual-network-manage-peering.md#create-a-peering)를 읽어보세요.
10. 이전 단계에서 **확인**을 클릭한 후 **피어링 추가** 블레이드가 닫히고 **myVnet1 - 피어링** 블레이드가 다시 표시됩니다. 몇 초 후 만든 피어링이 블레이드에 나타납니다. 만든 **myVnet1ToMyVnet2** 피어링의 **피어링 상태** 열에 **시작됨**이 나열됩니다. Vnet1을 Vnet2에 피어링했지만, 이제 myVnet2를 myVnet1에 피어링해야 합니다. 피어링은 양방향으로 만들어져야 가상 네트워크의 리소스가 서로 통신할 수 있습니다.
11. myVnet2에 대해 5~10단계를 다시 완료합니다. 피어링 이름을 *myVnet2ToMyVnet1*로 지정합니다.
12. **확인**을 클릭하여 MyVnet2에 대한 피어링을 만든 후 몇 초가 지나면 방금 만든 **myVnet2ToMyVnet1** 피어링이 **피어링 상태** 열에 **연결됨**으로 나열됩니다.
13. MyVnet1에 대해 5~7단계를 다시 완료합니다. 이제 **myVnet1ToVNet2** 피어링의 **피어링 상태**도 **연결됨**입니다. 피어링의 두 가상 네트워크에 대한 **피어링 상태** 열에 둘 다 **연결됨**이 표시되고 나면 피어링이 성공적으로 설정된 것입니다.
14. **선택 사항**: 이 자습서에서 가상 머신을 만드는 내용은 다루지 않지만, 각 가상 네트워크에서 가상 머신을 만들고 한 가상 머신에서 다른 가상 머신으로 연결하여 연결의 유효성을 검사할 수 있습니다.
15. **선택 사항**: 이 자습서에서 만든 리소스를 삭제하려면 이 문서의 [리소스 삭제](#delete-portal) 섹션에서 설명하는 단계를 완료합니다.

어느 쪽 가상 네트워크에서든 만든 모든 Azure 리소스는 이제 해당 IP 주소를 통해 서로 통신할 수 있습니다. 가상 네트워크에 대해 기본 Azure 이름 확인을 사용 중인 경우 가상 네트워크의 리소스가 가상 네트워크에서 이름을 확인할 수 없습니다. 피어링의 가상 네트워크에서 이름을 확인하려면 자체 DNS 서버를 만들어야 합니다. [자체 DNS 서버를 이용한 이름 확인](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server) 설정 방법을 알아보세요.

## <a name="cli"></a>피어링 만들기 - Azure CLI

다음 스크립트는

- Azure CLI 버전 2.0.4 이상이 필요합니다. 버전을 찾으려면 `az --version` 명령을 실행합니다. 업그레이드해야 하는 경우 [Azure CLI 2.0 설치]( /cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)를 참조하세요.
- Bash 셸에서 작동합니다. Windows 클라이언트에서 Azure CLI 스크립트 실행과 관련된 옵션은 [Windows에서 Azure CLI 실행](../virtual-machines/windows/cli-options.md?toc=%2fazure%2fvirtual-network%2ftoc.json)을 참조하세요. 

CLI 및 해당 종속성을 설치하는 대신 Azure Cloud Shell을 사용할 수 있습니다. Azure Cloud Shell은 Azure Portal에서 직접 실행할 수 있는 평가판 Bash 셸입니다. Azure CLI가 사전 설치되어 계정에서 사용하도록 구성되어 있습니다. 다음 스크립트에서 **사용해보기** 단추를 클릭하면 Azure 계정 로그인 가능성을 기록하는 Cloud Shell이 호출됩니다. 스크립트를 실행하려면 **복사** 단추를 클릭하여 내용을 Cloud Shell에 붙여넣습니다.

1. 리소스 그룹 하나와 두 개의 가상 네트워크를 만듭니다.

    ```azurecli-interactive
    #!/bin/bash

    # Variables for common values used throughout the script.
    rgName="myResourceGroup"
    location="eastus"

    # Create a resource group.
    az group create \
      --name $rgName \
      --location $location

    # Create virtual network 1.
    az network vnet create \
      --name myVnet1 \
      --resource-group $rgName \
      --location $location \
      --address-prefix 10.0.0.0/16

    # Create virtual network 2.
    az network vnet create \
      --name myVnet2 \
      --resource-group $rgName \
      --location $location \
      --address-prefix 10.1.0.0/16
    ```

2. 두 가상 네트워크 간에 가상 네트워크 피어링을 만듭니다.
 
    ```azurecli-interactive
    # Get the id for VNet1.
    vnet1Id=$(az network vnet show \
      --resource-group $rgName \
      --name myVnet1 \
      --query id --out tsv)

    # Get the id for VNet2.
    vnet2Id=$(az network vnet show \
      --resource-group $rgName \
      --name myVnet2 \
      --query id \
      --out tsv)

    # Peer VNet1 to VNet2.
    az network vnet peering create \
      --name myVnet1ToMyVnet2 \
      --resource-group $rgName \
      --vnet-name myVnet1 \
      --remote-vnet-id $vnet2Id \
      --allow-vnet-access

    # Peer VNet2 to VNet1.
    az network vnet peering create \
      --name myVnet2ToMyVnet1 \
      --resource-group $rgName \
      --vnet-name myVnet2 \
      --remote-vnet-id $vnet1Id \
      --allow-vnet-access
    ```

3. 스크립트를 실행한 후 각 가상 네트워크에 대한 피어링을 검토합니다. 

    ```azurecli-interactive
    az network vnet peering list \
      --resource-group myResourceGroup \
      --vnet-name myVnet1 \
      --output table
    ```
    
    *myVnet1*을 *myVnet2*로 바꿔 이전 명령을 다시 실행합니다. 두 명령의 출력에서 **PeeringState** 열에 **Connected**가 표시됩니다.

     어느 쪽 가상 네트워크에서든 만든 모든 Azure 리소스는 이제 해당 IP 주소를 통해 서로 통신할 수 있습니다. 가상 네트워크에 대해 기본 Azure 이름 확인을 사용 중인 경우 가상 네트워크의 리소스가 가상 네트워크에서 이름을 확인할 수 없습니다. 피어링의 가상 네트워크에서 이름을 확인하려면 자체 DNS 서버를 만들어야 합니다. [자체 DNS 서버를 이용한 이름 확인](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server) 설정 방법을 알아보세요.

4. **선택 사항**: 이 자습서에서 가상 머신을 만드는 내용은 다루지 않지만, 각 가상 네트워크에서 가상 머신을 만들고 한 가상 머신에서 다른 가상 머신으로 연결하여 연결의 유효성을 검사할 수 있습니다.
5. **선택 사항**: 이 자습서에서 만든 리소스를 삭제하려면 이 문서의 [리소스 삭제](#delete-cli)에서 설명하는 단계를 완료합니다.


## <a name="powershell"></a>피어링 만들기 - PowerShell

1. 최신 버전의 PowerShell [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) 모듈을 설치합니다. Azure PowerShell을 처음 사용하는 경우 [Azure PowerShell 개요](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json)를 참조하세요.
2. PowerShell 세션을 시작하려면 **시작**으로 이동하고 **powershell**을 입력한 다음 **PowerShell**을 클릭합니다.
3. PowerShell에서 `login-azurermaccount` 명령을 입력하여 Azure에 로그인합니다. 로그인하는 데 사용하는 계정에 가상 네트워크 피어링을 만드는 데 필요한 권한이 있어야 합니다. 자세한 내용은 이 문서의 [권한](#permissions) 섹션을 참조하세요.
4. 리소스 그룹 하나와 두 개의 가상 네트워크를 만듭니다. 스크립트를 실행하려면 다음 스크립트를 복사하여 PowerShell에 붙여넣은 후 화면에 마지막 줄이 나타나면 `Enter` 키를 누릅니다.

    ```powershell
    # Variables for common values used throughout the script.
    $rgName='myResourceGroup'
    $location='eastus'

    # Create a resource group.
    New-AzureRmResourceGroup `
      -Name $rgName `
      -Location $location

    # Create virtual network 1.
    $vnet1 = New-AzureRmVirtualNetwork `
      -ResourceGroupName $rgName `
      -Name 'myVnet1' `
      -AddressPrefix '10.0.0.0/16' `
      -Location $location

    # Create virtual network 2.
    $vnet2 = New-AzureRmVirtualNetwork `
      -ResourceGroupName $rgName `
      -Name 'myVnet2' `
      -AddressPrefix '10.1.0.0/16' `
      -Location $location
    ```

5. 두 가상 네트워크 간에 가상 네트워크 피어링을 만듭니다. 다음 스크립트를 복사하여 PowerShell에 붙여넣은 후 화면에 마지막 줄이 나타나면 `Enter` 키를 누릅니다.
    ```powershell
    # Peer VNet1 to VNet2.
    Add-AzureRmVirtualNetworkPeering `
      -Name 'myVnet1ToMyVnet2' `
      -VirtualNetwork $vnet1 `
      -RemoteVirtualNetworkId $vnet2.Id

    # Peer VNet2 to VNet1.
    Add-AzureRmVirtualNetworkPeering `
      -Name 'myVnet2ToMyVnet1' `
      -VirtualNetwork $vnet2 `
      -RemoteVirtualNetworkId $vnet1.Id
    ```
6. 가상 네트워크의 서브넷을 검토하려면 다음 명령을 복사하여 PowerShell에 붙여넣은 다음 `Enter` 키를 누릅니다.

    ```powershell
    Get-AzureRmVirtualNetworkPeering `
      -ResourceGroupName myResourceGroup `
      -VirtualNetworkName myVnet1 `
      | Format-Table VirtualNetworkName, PeeringState
    ```

    *myVnet1*을 *myVnet2*로 바꿔 이전 명령을 다시 실행합니다. 두 명령의 출력에서 **PeeringState** 열에 **Connected**가 표시됩니다.
7. **선택 사항**: 이 자습서에서 가상 머신을 만드는 내용은 다루지 않지만, 각 가상 네트워크에서 가상 머신을 만들고 한 가상 머신에서 다른 가상 머신으로 연결하여 연결의 유효성을 검사할 수 있습니다.
8. **선택 사항**: 이 자습서에서 만든 리소스를 삭제하려면 이 문서의 [리소스 삭제](#delete-powershell)에서 설명하는 단계를 완료합니다.

어느 쪽 가상 네트워크에서든 만든 모든 Azure 리소스는 이제 해당 IP 주소를 통해 서로 통신할 수 있습니다. 가상 네트워크에 대해 기본 Azure 이름 확인을 사용 중인 경우 가상 네트워크의 리소스가 가상 네트워크에서 이름을 확인할 수 없습니다. 피어링의 가상 네트워크에서 이름을 확인하려면 자체 DNS 서버를 만들어야 합니다. [자체 DNS 서버를 이용한 이름 확인](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server) 설정 방법을 알아보세요.

## <a name="template"></a>피어링 만들기 - Resource Manager 템플릿

1. [가상 네트워크 피어링 만들기](https://azure.microsoft.com/resources/templates/201-vnet-to-vnet-peering) Resource Manager 템플릿을 참조합니다. 지침은 Azure Portal, PowerShell 또는 Azure CLI를 사용하여 템플릿을 배포하기 위한 템플릿으로 제공됩니다. 가상 네트워크 피어링을 만드는 데 필요한 권한이 있는 계정을 사용하여 템플릿을 배포하기 위해 선택할 원하는 도구에 로그인합니다. 자세한 내용은 이 문서의 [권한](#permissions) 섹션을 참조하세요.
2. **선택 사항**: 이 자습서에서 가상 머신을 만드는 내용은 다루지 않지만, 각 가상 네트워크에서 가상 머신을 만들고 한 가상 머신에서 다른 가상 머신으로 연결하여 연결의 유효성을 검사할 수 있습니다.
3. **선택 사항**: 이 자습서에서 만든 리소스를 삭제하려면 Azure Portal, PowerShell 또는 Azure CLI를 사용하여 이 문서의 [리소스 삭제](#delete) 섹션에서 설명하는 단계를 완료합니다.

## <a name="permissions"></a>권한

가상 네트워크 피어링을 만드는 데 사용하는 계정에 필요한 역할 또는 권한이 있어야 합니다. 예를 들어 VNet1 및 VNet2라는 두 가상 네트워크를 피어링하는 경우 계정에는 각 가상 네트워크에 대한 다음과 같은 최소 역할 또는 권한이 할당되어야 합니다.
    
|가상 네트워크|역할|권한|
|---|---|---|
|VNet1|[네트워크 참여자](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write|
|VNet2|[네트워크 참여자](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/peer|

[기본 제공 역할](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) 및 [사용자 지정 역할](../active-directory/role-based-access-control-custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)에 특정 권한 할당(Resource Manager만 해당)에 대해 자세히 알아보세요.

## <a name="delete"></a>리소스 삭제
이 자습서를 마친 경우 사용 요금이 발생하지 않도록 자습서에서 만든 리소스를 삭제하려고 할 것입니다. 리소스 그룹을 삭제하면 리소스 그룹에 있는 리소스도 모두 삭제됩니다.

### <a name="delete-portal"></a>Azure Portal

1. 포털 검색 상자에 **myResourceGroup**을 입력합니다. 검색 결과에서 **myResourceGroup**을 클릭합니다.
2. **myResourceGroup** 블레이드에서 **삭제** 아이콘을 클릭합니다.
3. 삭제를 확인하려면 **리소스 그룹 이름 입력** 상자에 **myResourceGroup**을 입력한 다음 **삭제**를 클릭합니다.

### <a name="delete-cli"></a>Azure CLI

다음 명령을 입력합니다.

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

### <a name="delete-powershell"></a>PowerShell

다음 명령을 입력합니다.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup -force
```

## <a name="register"></a>전역 가상 네트워크 피어링 미리 보기에 등록

동일한 지역의 가상 네트워크 피어링은 일반 공급됩니다. 서로 다른 지역에서 가상 네트워크를 피어링하는 기능은 현재 미리 보기 상태입니다. 사용 가능한 지역에 대해서는 [가상 네트워크 업데이트](https://azure.microsoft.com/en-us/updates/?product=virtual-network)를 참조하세요. 여러 지역에 걸쳐 가상 네트워크를 피어링하려면 먼저 Azure PowerShell 또는 Azure CLI를 사용하여 (피어링하려는 각 가상 네트워크가 있는 구독 내에서) 다음 단계를 완료하여 미리 보기에 등록해야 합니다.

### <a name="powershell"></a>PowerShell

1. 최신 버전의 PowerShell [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) 모듈을 설치합니다. Azure PowerShell을 처음 사용하는 경우 [Azure PowerShell 개요](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json)를 참조하세요.
2. PowerShell 세션을 시작하고 `Login-AzureRmAccount` 명령을 사용하여 로그인합니다.
3. 다음 명령을 입력하여 피어링하려는 각 가상 네트워크가 미리 보기를 위해 있는 구독을 등록합니다.

    ```powershell
    Register-AzureRmProviderFeature `
      -FeatureName AllowGlobalVnetPeering `
      -ProviderNamespace Microsoft.Network
    
    Register-AzureRmResourceProvider `
      -ProviderNamespace Microsoft.Network
    ```
4. 다음 명령을 입력하여 미리 보기에 등록되었는지 확인합니다.

    ```powershell    
    Get-AzureRmProviderFeature `
      -FeatureName AllowGlobalVnetPeering `
      -ProviderNamespace Microsoft.Network
    ```

    두 구독에 대해 모두 이전 명령을 입력하여 받은 **RegistrationState** 출력이 **Registered**가 되기 전까지는 이 문서의 포털, Azure 명령줄 인터페이스 또는 PowerShell 또는 리소스 관리자 템플릿 섹션에 있는 절차를 수행하지 않습니다.

### <a name="azure-cli"></a>Azure CLI

1. [Azure CLI를 설치 및 구성합니다](/cli/azure/install-azure-cli?toc=%2Fazure%2Fvirtual-network%2Ftoc.json).
2. `az --version` 명령을 입력하여 Azure CLI 버전 2.0.18 이상을 사용하고 있는지 확인합니다. 그렇지 않을 경우 최신 버전을 설치합니다.
3. `az login` 명령을 사용하여 Azure에 로그인합니다.
4. 다음 명령을 입력하여 미리 보기에 등록합니다.

    ```azurecli-interactive
    az feature register --name AllowGlobalVnetPeering --namespace Microsoft.Network
    az provider register --name Microsoft.Network
    ```

5. 다음 명령을 입력하여 미리 보기에 등록되었는지 확인합니다.

    ```azurecli-interactive
    az feature show --name AllowGlobalVnetPeering --namespace Microsoft.Network
    ```

    두 구독에 대해 모두 이전 명령을 입력하여 받은 **RegistrationState** 출력이 **Registered**가 되기 전까지는 이 문서의 포털, Azure 명령줄 인터페이스 또는 PowerShell 또는 리소스 관리자 템플릿 섹션에 있는 절차를 수행하지 않습니다.

## <a name="next-steps"></a>다음 단계

- 프로덕션 환경에 사용하기 위한 가상 네트워크 피어링을 만들기 전에 먼저 중요한 [가상 네트워크 피어링 제약 조건 및 동작](virtual-network-manage-peering.md#requirements-and-constraints)에 철저하게 익숙해집니다.
- 모든 [가상 네트워크 피어링 설정](virtual-network-manage-peering.md#create-a-peering)에 대해 알아봅니다.
- 가상 네트워크 피어링을 통해 [허브 및 스포크 네트워크 토폴로지를 만드는](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering) 방법을 알아봅니다.
