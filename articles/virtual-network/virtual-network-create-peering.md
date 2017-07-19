---
title: "Azure Virtual Network 피어링 만들기 | Microsoft Docs"
description: "두 가상 네트워크 간에 가상 네트워크 피어링을 만드는 방법을 알아봅니다."
services: virtual-network
documentationcenter: 
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 026bca75-2946-4c03-b4f6-9f3c5809c69a
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/06/2017
ms.author: jdial;narayan;annahar
ms.translationtype: Human Translation
ms.sourcegitcommit: 245ce9261332a3d36a36968f7c9dbc4611a019b2
ms.openlocfilehash: 666165115e80a39d02386719b0d7e64d7ae17749
ms.contentlocale: ko-kr
ms.lasthandoff: 06/09/2017


---
# <a name="create-a-virtual-network-peering"></a>가상 네트워크 피어링 만들기

이 자습서에서는 두 가상 네트워크 간에 가상 네트워크 피어링을 만드는 방법을 알아봅니다. 두 가상 네트워크를 피어링하면 서로 다른 가상 네트워크에 있는 리소스가 같은 가상 네트워크에 있는 리소스인 것처럼 같은 대역폭 및 대기 시간으로 서로 통신할 수 있습니다. 가상 네트워크 피어링은 같은 Azure 지역에 있는 두 가상 네트워크 간에만 만들 수 있습니다. 가상 네트워크 피어링에 대한 자세한 내용은 [가상 네트워크 피어링](virtual-network-peering-overview.md) 개요 문서를 참조하세요. 

서로 다른 Azure 지역에 있는 가상 네트워크를 연결해야 할 경우 Azure를 [VPN 게이트웨이](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)로 사용하여 가상 네트워크를 연결할 수 있습니다. 

[Azure Portal](#portal), Azure [PowerShell](#powershell), Azure CLI([명령줄 인터페이스](#cli)) 또는 [Azure Resource Manager 템플릿](#template)을 사용하여 가상 네트워크 피어링을 만들 수 있습니다. 앞의 도구 링크 중 원하는 도구 링크를 클릭하여 원하는 도구를 사용하여 가상 네트워크 피어링을 만드는 단계로 바로 이동하세요.

## <a name="portal"></a>피어링 만들기 - Azure Portal

다음 단계를 완료하여 Resource Manager를 통해 배포된, 같은 구독에 있는 두 가상 네트워크 간에 가상 네트워크 피어링을 만듭니다. [서로 다른 구독에 있는 두 가상 네트워크를 피어링](#different-subscriptions)하거나 [가상 네트워크(클래식)를 통해 하나의 가상 네트워크(Resource Manager)를 피어링](#different-models)할 수도 있습니다. 

1. [Azure 포털](https://portal.azure.com)에 로그인합니다. 로그인하는 데 사용하는 계정에 가상 네트워크 피어링을 만드는 데 필요한 권한이 있어야 합니다. 자세한 내용은 이 문서의 [권한](#permissions) 섹션을 참조하세요.
2. **+ 새로 만들기**, **네트워킹**, **가상 네트워크**를 차례로 클릭합니다.
3. **가상 네트워크** 블레이드의 **배포 모델 선택** 상자에서 **Resource Manager**를 선택된 상태로 두고 **만들기**를 클릭합니다.
4. **가상 네트워크 만들기** 블레이드에서 다음 설정에 대한 값을 입력하거나 선택한 다음 **만들기**를 클릭합니다.
    - **이름**: *myVnet1*
    - **주소 공간**: *10.0.0.0/16*
    - **서브넷 이름**: *기본값*
    - **서브넷 주소 범위**: *10.0.0.0/24*
    - **구독**: 사용자의 구독을 선택합니다.
    - **리소스 그룹**: **새로 만들기**를 선택하고 *myResourceGroup*을 입력합니다.
    - **위치**: *미국 동부*
5. 4단계에서 다음 값을 지정하여 2~4단계를 다시 완료합니다.
    - **이름**: *myVnet2*
    - **주소 공간**: *10.1.0.0/16*
    - **서브넷 이름**: *기본값*
    - **서브넷 주소 범위**: *10.1.0.0/24*
    - **구독**: 사용자의 구독을 선택합니다.
    - **리소스 그룹**: **기존 항목 사용**을 선택하고 *myResourceGroup*을 선택합니다.
    - **위치**: *미국 동부*
6. 포털 위쪽에 있는 **리소스 검색** 상자에 *myResourceGroup*을 입력합니다. **myResourceGroup**이 검색 결과에 표시되면 클릭합니다. **myresourcegroup** 리소스 그룹에 대한 블레이드가 나타납니다. 리소스 그룹에는 이전 단계에서 만든 두 가상 네트워크가 포함되어 있습니다.
7. **myVNet1**을 클릭합니다.
8. 나타나는 **myVnet1** 블레이드의 왼쪽에 있는 세로 옵션 목록에서 **피어링**을 클릭합니다.
9. 나타난 **myVnet1 - 피어링** 블레이드에서 **+ 추가**를 클릭합니다.
10. 나타나는 **피어링 추가** 블레이드에서 다음 옵션을 입력하거나 선택한 다음 **확인**을 클릭합니다.
     - **이름**: *myVnet1ToMyVnet2*
     - **가상 네트워크 배포 모델**: **Resource Manager**를 선택합니다. 
     - **구독**: 사용자의 구독을 선택합니다.
     - **가상 네트워크**: **가상 네트워크 선택**을 클릭한 다음 **myVnet2**를 클릭합니다.
     - **가상 네트워크 액세스 허용:** **사용**이 선택되어 있는지 확인합니다.
    이 자습서에서 다른 설정은 사용되지 않습니다. 모든 피어링 설정에 대해 알아보려면 [가상 네트워크 피어링 관리](virtual-network-manage-peering.md#create-peering)를 읽어보세요.
11. 이전 단계에서 **확인**을 클릭한 후 **피어링 추가** 블레이드가 닫히고 **myVnet1 - 피어링** 블레이드가 다시 표시됩니다. 몇 초 후 만든 피어링이 블레이드에 나타납니다. 만든 **myVnet1ToMyVnet2** 피어링의 **피어링 상태** 열에 **시작됨**이 나열됩니다. Vnet1을 Vnet2에 피어링했지만, 이제 Vnet2를 Vnet1에 피어링해야 합니다. 피어링은 양방향으로 만들어져야 가상 네트워크의 리소스가 서로 통신할 수 있습니다.
12. myVnet2에 대해 6~11단계를 다시 완료합니다.  피어링 이름을 *myVnet2ToMyVnet1*로 지정합니다.
13. **확인**을 클릭하여 MyVnet2에 대한 피어링을 만든 후 몇 초가 지나면 방금 만든 **myVnet2ToMyVnet1** 피어링이 **피어링 상태** 열에 **연결됨**으로 나열됩니다.
14. MyVnet1에 대해 6~8단계를 다시 완료합니다. 이제 **myVnet1ToVNet2** 피어링의 **피어링 상태**도 **연결됨**입니다. 피어링의 두 가상 네트워크에 대한 **피어링 상태** 열에 둘 다 **연결됨**이 표시되고 나면 피어링이 성공적으로 설정된 것입니다.
15. **선택 사항**: 이 자습서에서 가상 컴퓨터를 만드는 내용은 다루지 않지만, 각 가상 네트워크에서 가상 컴퓨터를 만들고 한 가상 컴퓨터에서 다른 가상 컴퓨터로 연결하여 연결의 유효성을 검사할 수 있습니다.
16. **선택 사항**: 이 자습서에서 만든 리소스를 삭제하려면 이 문서의 [리소스 삭제](#delete-portal) 섹션에서 설명하는 단계를 완료합니다.

어느 쪽 가상 네트워크에서든 만든 모든 Azure 리소스는 이제 해당 IP 주소를 통해 서로 통신할 수 있습니다. 가상 네트워크에 대해 기본 Azure 이름 확인을 사용 중인 경우 가상 네트워크의 리소스가 가상 네트워크에서 이름을 확인할 수 없습니다. 피어링의 가상 네트워크에서 이름을 확인하려면 자체 DNS 서버를 만들어야 합니다. [자체 DNS 서버를 이용한 이름 확인](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server) 설정 방법을 알아보세요.

## <a name="cli"></a>피어링 만들기 - Azure CLI

다음 스크립트는

- Azure CLI 버전 2.0.4 이상이 필요합니다. `az --version`을 실행하여 버전을 찾습니다. 업그레이드해야 하는 경우 [Azure CLI 2.0 설치]( /cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)를 참조하세요.
- Bash 셸에서 작동합니다. Windows 클라이언트에서 Azure CLI 스크립트 실행과 관련된 옵션은 [Windows에서 Azure CLI 실행](../virtual-machines/windows/cli-options.md?toc=%2fazure%2fvirtual-network%2ftoc.json)을 참조하세요.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

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
    #
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
    #
    ```
3. 스크립트를 실행한 후 각 가상 네트워크에 대한 피어링을 검토합니다. 다음 명령을 복사한 다음 명령 창에 붙여넣습니다.

    ```azurecli-interactive
    az network vnet peering list \
      --resource-group myResourceGroup \
      --vnet-name myVnet1 \
      --output table
    #
    ```
*myVnet1*을 *myVnet2*로 바꿔 이전 명령을 다시 실행합니다. 두 명령의 출력에서 **PeeringState** 열에 **Connected**가 표시됩니다.
4. **선택 사항**: 이 자습서에서 가상 컴퓨터를 만드는 내용은 다루지 않지만, 각 가상 네트워크에서 가상 컴퓨터를 만들고 한 가상 컴퓨터에서 다른 가상 컴퓨터로 연결하여 연결의 유효성을 검사할 수 있습니다.
5. **선택 사항**: 이 자습서에서 만든 리소스를 삭제하려면 이 문서의 [리소스 삭제](#delete-cli)에서 설명하는 단계를 완료합니다.

어느 쪽 가상 네트워크에서든 만든 모든 Azure 리소스는 이제 해당 IP 주소를 통해 서로 통신할 수 있습니다. 가상 네트워크에 대해 기본 Azure 이름 확인을 사용 중인 경우 가상 네트워크의 리소스가 가상 네트워크에서 이름을 확인할 수 없습니다. 피어링의 가상 네트워크에서 이름을 확인하려면 자체 DNS 서버를 만들어야 합니다. [자체 DNS 서버를 이용한 이름 확인](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server) 설정 방법을 알아보세요.
 
## <a name="powershell"></a>피어링 만들기 - PowerShell

1. 최신 버전의 PowerShell [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) 모듈을 설치합니다. Azure PowerShell을 처음 사용하는 경우 [Azure PowerShell 개요](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json)를 참조하세요.
2. PowerShell 세션을 시작하려면 **시작**으로 이동하고 **powershell**을 입력한 다음 **PowerShell**을 클릭합니다.
3. PowerShell 창에서 `login-azurermaccount` 명령을 입력하여 Azure에 로그인합니다. 로그인하는 데 사용하는 계정에 가상 네트워크 피어링을 만드는 데 필요한 권한이 있어야 합니다. 자세한 내용은 이 문서의 [권한](#permissions) 섹션을 참조하세요.
4. 브라우저에서 다음 스크립트를 복사한 다음 PowerShell 창에서 마우스 오른쪽 단추를 클릭하여 리소스 그룹 하나와 두 개의 가상 네트워크를 만드는 스크립트를 실행합니다.
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
    #
    ```
5. 브라우저에서 다음 스크립트를 복사한 다음 PowerShell 창에서 마우스 오른쪽 단추를 클릭하여 두 가상 네트워크 간에 가상 네트워크 피어링을 만드는 스크립트를 실행합니다.
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
    #
    ```
6. 가상 네트워크의 서브넷을 검토하려면 다음 명령을 복사한 다음 PowerShell 창에 붙여넣습니다.

    ```powershell
    Get-AzureRmVirtualNetworkPeering `
      -ResourceGroupName myResourceGroup `
      -VirtualNetworkName myVnet1 `
      | Format-Table VirtualNetworkName, PeeringState
    #
    ```

*myVnet1*을 *myVnet2*로 바꿔 이전 명령을 다시 실행합니다. 두 명령의 출력에서 **PeeringState** 열에 **Connected**가 표시됩니다.
7. **선택 사항**: 이 자습서에서 가상 컴퓨터를 만드는 내용은 다루지 않지만, 각 가상 네트워크에서 가상 컴퓨터를 만들고 한 가상 컴퓨터에서 다른 가상 컴퓨터로 연결하여 연결의 유효성을 검사할 수 있습니다.
8. **선택 사항**: 이 자습서에서 만든 리소스를 삭제하려면 이 문서의 [리소스 삭제](#delete-powershell)에서 설명하는 단계를 완료합니다.

어느 쪽 가상 네트워크에서든 만든 모든 Azure 리소스는 이제 해당 IP 주소를 통해 서로 통신할 수 있습니다. 가상 네트워크에 대해 기본 Azure 이름 확인을 사용 중인 경우 가상 네트워크의 리소스가 가상 네트워크에서 이름을 확인할 수 없습니다. 피어링의 가상 네트워크에서 이름을 확인하려면 자체 DNS 서버를 만들어야 합니다. [자체 DNS 서버를 이용한 이름 확인](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server) 설정 방법을 알아보세요.

## <a name="template"></a>피어링 만들기 - Resource Manager 템플릿

1. [가상 네트워크 피어링 만들기](https://azure.microsoft.com/resources/templates/201-vnet-to-vnet-peering) Resource Manager 템플릿을 참조합니다. 지침은 Azure Portal, PowerShell 또는 Azure CLI를 사용하여 템플릿을 배포하기 위한 템플릿으로 제공됩니다. 가상 네트워크 피어링을 만드는 데 필요한 권한이 있는 계정을 사용하여 템플릿을 배포하기 위해 선택할 원하는 도구에 로그인합니다. 자세한 내용은 이 문서의 [권한](#permissions) 섹션을 참조하세요.
2. **선택 사항**: 이 자습서에서 가상 컴퓨터를 만드는 내용은 다루지 않지만, 각 가상 네트워크에서 가상 컴퓨터를 만들고 한 가상 컴퓨터에서 다른 가상 컴퓨터로 연결하여 연결의 유효성을 검사할 수 있습니다.
3. **선택 사항**: 이 자습서에서 만든 리소스를 삭제하려면 Azure Portal, PowerShell 또는 Azure CLI를 사용하여 이 문서의 [리소스 삭제](#delete) 섹션에서 설명하는 단계를 완료합니다.

## <a name="different-models"></a>다양한 배포 모델을 통해 만든 가상 네트워크 피어링

이전 섹션의 단계에서는 Resource Manager 배포 모델을 통해 만든 두 가상 네트워크 간에 가상 네트워크 피어링을 만드는 방법을 설명했습니다. 가상 네트워크(Resource Manager)와 가상 네트워크(클래식) 간에도 피어링을 만들 수 있습니다. 클래식 배포 모델을 통해 만든 두 가상 네트워크 간에는 가상 네트워크 피어링을 만들 수 없습니다. Azure 배포 모델에 대해 자세히 알아보려면 [Azure 배포 모델 이해](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 문서를 읽어보세요.

가상 네트워크 피어링을 만들려면 이 문서의 [Portal](#portal) 섹션에서 설명하는 1~11단계를 완료하되, 3단계에서 myVnet2를 만들 때 **클래식** 배포 모델을 선택합니다.

가상 네트워크(Resource Manager)와 가상 네트워크(클래식) 간에 가상 네트워크 피어링을 만들 때는 가상 네트워크(Resource Manager)에서 가상 네트워크(클래식)로의 피어링만 구성합니다. 가상 네트워크(Resource Manager)에 대한 피어링을 만든 후 해당 피어링 상태는 **업데이트 중**이 됩니다. 가상 네트워크(클래식)에 대한 피어링을 만들 필요가 없으므로 몇 초 후 상태가 자동으로 **연결됨**으로 변경됩니다. 

> [!NOTE]
> 이 자습서에서 다루지는 않지만, 이 섹션에 설명된 차이점이 반영되도록 [Azure CLI](#cli) 및 [PowerShell](#powershell) 섹션의 스크립트를 조정할 수 있습니다.

## <a name="different-subscriptions"></a>서로 다른 구독에 있는 가상 네트워크 피어링

이전 섹션의 단계에서는 같은 구독에 있는 두 가상 네트워크 간에 가상 네트워크 피어링을 만드는 방법을 설명했습니다. 두 구독이 같은 Azure Active Directory 테넌트에 연결되어 있다면 서로 다른 구독에 있는 가상 네트워크 간에 피어링을 만들 수도 있습니다. 아직 AD 테넌트가 없는 경우 빠르게 [만들](../active-directory/develop/active-directory-howto-tenant.md?toc=%2fazure%2fvirtual-network%2ftoc.json#start-from-scratch) 수 있습니다. 구독이 서로 다른 Active Directory 테넌트에 연결되어 있는 경우에는 서로 다른 구독에 있는 가상 네트워크 간에 가상 네트워크 피어링을 만들 수 없습니다. 피어링을 만드는 단계는 가상 네트워크를 만든 배포 모델에 따라 약간 다르지만, 다음과 같습니다.

### <a name="different-subscriptions-same-deployment-model"></a>Resource Manager를 통해 두 가상 네트워크를 만든 경우

1. 이 문서의 [Portal](#portal) 섹션에서 설명하는 1~7단계를 완료하되, 5단계에서 myVnet2에 대해 4단계에서 myVnet1에 대해 선택한 것과 다른 구독(같은 Azure Active Directory 테넌트에 연결되어 있음)을 선택합니다.
2. 나타나는 **myVnet1** 블레이드의 왼쪽에 있는 세로 옵션 목록에서 **액세스 제어(IAM)**를 클릭합니다.
3. 나타나는 **myVnet1 - 액세스 제어(IAM)** 블레이드에서 **+ 추가**를 클릭합니다.
4. 나타나는 **권한 추가** 블레이드의 **역할** 상자에서 **네트워크 참가자**를 선택합니다.
5. **선택** 상자에서 사용자 이름을 선택하거나 메일 주소를 입력하여 검색합니다. 피어링을 설정 중인 가상 네트워크와 같은 Azure Active Directory 테넌트의 사용자 목록이 표시됩니다.
6. **Save**를 클릭합니다.
7. 나타나는 **myVnet1 - 액세스 제어(IAM)** 블레이드의 왼쪽에 있는 세로 옵션 목록에서 **속성**을 클릭합니다. 다음 예와 같은 **리소스 ID**를 복사합니다. /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet1
8. myVnet2에 대해 이 섹션의 1~7단계를 완료합니다.
9. 권한 부여가 성공적으로 설정되었는지 확인하려면 Azure에서 로그아웃한 다음 다시 로그인합니다. 두 가상 네트워크에 대해 서로 다른 계정을 사용한 경우 두 계정 모두에서 로그아웃한 다음 두 계정 모두에 다시 로그인합니다.
10. 포털 위쪽에 있는 **리소스 검색** 상자에 *myResourceGroup*을 입력합니다. **myResourceGroup**이 검색 결과에 표시되면 클릭합니다. **myresourcegroup** 리소스 그룹에 대한 블레이드가 나타납니다. 리소스 그룹에는 이전 단계에서 만든 두 가상 네트워크가 포함되어 있습니다.
11. **myVNet1**을 클릭합니다.
12. 나타나는 **myVnet1** 블레이드의 왼쪽에 있는 세로 옵션 목록에서 **피어링**을 클릭합니다.
13. 나타난 **myVnet1 - 피어링** 블레이드에서 **+ 추가**를 클릭합니다.
14. 나타나는 **피어링 추가** 블레이드에서 다음 옵션을 입력하거나 선택한 다음 **확인**을 클릭합니다.
     - **이름**: *myVnet1ToMyVnet2*
     - **가상 네트워크 배포 모델**: **클래식**을 선택합니다. 
     - **리소스 ID를 알고 있음**: 이 확인란을 선택합니다.
     - **리소스 ID**: myVnet2의 리소스 ID를 입력합니다. 이 상자는 **리소스 ID를 알고 있음** 확인란이 선택된 경우에만 나타납니다.
     - **가상 네트워크 액세스 허용:** **사용**이 선택되어 있는지 확인합니다.
    이 자습서에서 다른 설정은 사용되지 않습니다. 모든 피어링 설정에 대해 알아보려면 [가상 네트워크 피어링 관리](virtual-network-manage-peering.md#create-peering)를 읽어보세요.
15. 이전 단계에서 **확인**을 클릭한 후 **피어링 추가** 블레이드가 닫히고 **myVnet1 - 피어링** 블레이드가 다시 표시됩니다. 몇 초 후 만든 피어링이 블레이드에 나타납니다. 만든 **myVnet1ToMyVnet2** 피어링의 **피어링 상태** 열에 **시작됨**이 나열됩니다. Vnet1을 Vnet2에 피어링했지만, 이제 Vnet2를 Vnet1에 피어링해야 합니다. 피어링은 양방향으로 만들어져야 가상 네트워크의 리소스가 서로 통신할 수 있습니다.
16. myVnet2에 대해 이 섹션의 10~14단계를 다시 완료합니다.  피어링 이름을 *myVnet2ToMyVnet1*로 지정하고 **리소스 ID**에 myVnet1의 리소스 ID를 입력합니다.
17. **확인**을 클릭하여 MyVnet2에 대한 피어링을 만든 후 몇 초가 지나면 방금 만든 **myVnet2ToMyVnet1** 피어링이 **피어링 상태** 열에 **연결됨**으로 나열됩니다.
18. MyVnet1에 대해 이 섹션의 10~11단계를 다시 완료합니다. 이제 **myVnet1ToVNet2** 피어링의 **피어링 상태**도 **연결됨**입니다. 피어링의 두 가상 네트워크에 대한 **피어링 상태** 열에 둘 다 **연결됨**이 표시되고 나면 피어링이 성공적으로 설정된 것입니다.
19. **선택 사항**: 이 자습서에서 가상 컴퓨터를 만드는 내용은 다루지 않지만, 각 가상 네트워크에서 가상 컴퓨터를 만들고 한 가상 컴퓨터에서 다른 가상 컴퓨터로 연결하여 연결의 유효성을 검사할 수 있습니다.
20. **선택 사항**: 이 자습서에서 만든 리소스를 삭제하려면 이 문서의 [리소스 삭제](#delete-portal) 섹션에서 설명하는 단계를 완료합니다.

### <a name="different-subscriptions-different-deployment-models"></a>하나의 가상 네트워크(Resource Manager), 하나의 가상 네트워크(클래식)
 
[!INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-preview.md)]

1. 미리 보기에 등록합니다. 미리 보기에 등록할 때까지 다른 구독에 있는 다른 배포 모델을 통해 만든 가상 네트워크를 피어링할 수 없습니다. Portal에서나 Azure CLI를 사용해서는 미리 보기에 등록할 수 없습니다. PowerShell을 사용해서만 미리 보기에 등록할 수 있습니다. 미리 보기에 등록하려면 다음 작업을 완료합니다.
    - 최신 버전의 PowerShell [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) 모듈을 설치합니다. Azure PowerShell을 처음 사용하는 경우 [Azure PowerShell 개요](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json)를 참조하세요.
    - Windows PC에서 PowerShell 세션을 시작하려면 **시작**으로 이동하고 **powershell**을 입력한 다음 **PowerShell**을 클릭합니다.
    - PowerShell 창에서 `login-azurermaccount` 명령을 입력하여 Azure에 로그인합니다. 로그인하는 데 사용하는 계정에 가상 네트워크 피어링을 만드는 데 필요한 권한이 있어야 합니다. 자세한 내용은 이 문서의 [권한](#permissions) 섹션을 참조하세요.
    - 두 Azure 구독 모두에 대해 미리 보기 기능을 등록하고 PowerShell에서 다음 명령을 입력합니다. 
    
    ```powershell
    Register-AzureRmProviderFeature `
      -FeatureName AllowClassicCrossSubscriptionPeering `
      -ProviderNamespace Microsoft.Network
    Register-AzureRmResourceProvider `
      -ProviderNamespace Microsoft.Network
    #
    ```
    각 구독에 로그인한 상태에서 미리 보기에 등록합니다. 다음 명령을 입력한 후 받은 **RegistrationState** 출력이 두 구독 모두에서 **Registered**가 될 때까지 다음 단계를 진행하지 마세요.

    ```powershell
    Get-AzureRmProviderFeature `
      -FeatureName AllowClassicCrossSubscriptionPeering `
      -ProviderNamespace Microsoft.Network
    #
    ```

2. 다음 변경 내용을 사용하여 이 문서의 [Portal](#portal) 섹션에서 설명하는 1~7단계를 완료합니다.
    - 5단계에서 myVnet2에 대해 4단계에서 myVnet1에 대해 선택한 것과 다른 구독(같은 Azure Active Directory 테넌트에 연결되어 있음)을 선택합니다.
    - myVnet2를 만들 때 3단계에서 **클래식**을 선택합니다.
3. 포털 위쪽에 있는 **리소스 검색** 상자에 *myResourceGroup*을 입력합니다. **myResourceGroup**이 검색 결과에 표시되면 클릭합니다. **myresourcegroup** 리소스 그룹에 대한 블레이드가 나타납니다. 리소스 그룹에는 이전 단계에서 만든 두 가상 네트워크가 포함되어 있습니다.
4. **myVNet1**을 클릭합니다.
5. 이 문서의 [Resource Manager를 통해 두 가상 네트워크를 만든 경우](#different-subscriptions-same-deployment-model) 섹션에서 설명하는 2~9단계를 완료합니다.
6. 포털 위쪽에 있는 **리소스 검색** 상자에 *myResourceGroup*을 입력합니다. **myResourceGroup**이 검색 결과에 표시되면 클릭합니다. **myresourcegroup** 리소스 그룹에 대한 블레이드가 나타납니다. 리소스 그룹에는 이전 단계에서 만든 두 가상 네트워크가 포함되어 있습니다.
7. **myVNet1**을 클릭합니다.
8. 나타나는 **myVnet1** 블레이드의 왼쪽에 있는 세로 옵션 목록에서 **피어링**을 클릭합니다.
9. 나타난 **myVnet1 - 피어링** 블레이드에서 **+ 추가**를 클릭합니다.
10. 나타나는 **피어링 추가** 블레이드에서 다음 옵션을 입력하거나 선택한 다음 **확인**을 클릭합니다.
     - **이름**: *myVnet1ToMyVnet2*
     - **가상 네트워크 배포 모델**: **클래식**을 선택합니다. 
     - **리소스 ID를 알고 있음**: 이 확인란을 선택합니다.
     - **리소스 ID**: myVnet2의 리소스 ID를 입력합니다. 이 상자는 **리소스 ID를 알고 있음** 확인란이 선택된 경우에만 나타납니다.
     - **가상 네트워크 액세스 허용:** **사용**이 선택되어 있는지 확인합니다.
    이 자습서에서 다른 설정은 사용되지 않습니다. 모든 피어링 설정에 대해 알아보려면 [가상 네트워크 피어링 관리](virtual-network-manage-peering.md#create-peering)를 읽어보세요.

    가상 네트워크(Resource Manager)와 가상 네트워크(클래식) 간에 가상 네트워크 피어링을 만들 때는 가상 네트워크(Resource Manager)에서 가상 네트워크(클래식)로의 피어링만 구성합니다. 가상 네트워크(Resource Manager)에 대한 피어링을 만든 후 해당 피어링 상태는 **업데이트 중**이 됩니다. 가상 네트워크(클래식)에 대한 피어링을 만들 필요가 없으므로 몇 초 후 상태가 자동으로 **연결됨**으로 변경됩니다. 
11. **선택 사항**: 이 자습서에서 가상 컴퓨터를 만드는 내용은 다루지 않지만, 각 가상 네트워크에서 가상 컴퓨터를 만들고 한 가상 컴퓨터에서 다른 가상 컴퓨터로 연결하여 연결의 유효성을 검사할 수 있습니다.
12. **선택 사항**: 이 자습서에서 만든 리소스를 삭제하려면 이 문서의 [리소스 삭제](#delete-portal) 섹션에서 설명하는 단계를 완료합니다.

> [!NOTE]
> 이 자습서에서 다루지는 않지만, 이 섹션에 설명된 차이점이 반영되도록 [Azure CLI](#cli) 및 [PowerShell](#powershell) 섹션의 스크립트를 조정할 수 있습니다.

## <a name="permissions"></a>권한

가상 네트워크 피어링을 만드는 데 사용하는 계정에 필요한 역할 또는 권한이 있어야 합니다. 예를 들어 VNet1 및 VNet2라는 두 가상 네트워크를 피어링하는 경우 계정에는 각 가상 네트워크에 대한 다음과 같은 최소 역할 또는 권한이 할당되어야 합니다.
    
|가상 네트워크|배포 모델|역할|권한|
|---|---|---|---|
|VNet1|리소스 관리자|[네트워크 참여자](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write|
| |클래식|[클래식 네트워크 참여자](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|해당 없음|
|VNet2|리소스 관리자|[네트워크 참여자](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/peer|
||클래식|[클래식 네트워크 참여자](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|Microsoft.ClassicNetwork/virtualNetworks/peer|

[기본 제공 역할](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) 및 [사용자 지정 역할](../active-directory/role-based-access-control-custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)에 특정 권한 할당(Resource Manager만 해당)에 대해 자세히 알아보세요.

## <a name="delete"></a>리소스 삭제
이 자습서를 마친 경우 사용 요금이 발생하지 않도록 자습서에서 만든 리소스를 삭제하려고 할 것입니다. 리소스 그룹을 삭제하면 리소스 그룹에 있는 리소스도 모두 삭제됩니다.

### <a name="delete-portal"></a>Azure Portal

1. 포털 검색 상자에 **myResourceGroup**을 입력합니다. 검색 결과에서 **myResourceGroup**을 클릭합니다.
2. **myResourceGroup** 블레이드에서 **삭제** 아이콘을 클릭합니다.
3. 삭제를 확인하려면 **리소스 그룹 이름 입력** 상자에 **myResourceGroup**을 입력한 다음 **삭제**를 클릭합니다.

### <a name="delete-cli"></a>Azure CLI

Linux, macOS 또는 Windows 명령 셸에서 다음 명령을 입력합니다. 또는 다음 코드 블록의 오른쪽 위에 있는 Try It(사용해 보세요) 단추를 클릭하여 Cloud Shell을 시작합니다. 그런 다음 복사 단추를 사용하여 샘플 코드를 복사한 다음 Cloud Shell에 붙여넣습니다.

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

### <a name="delete-powershell"></a>PowerShell

PowerShell 명령 프롬프트에서 다음 명령을 입력합니다.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>다음 단계

- 프로덕션 환경에 사용하기 위한 가상 네트워크 피어링을 만들기 전에 먼저 중요한 [가상 네트워크 피어링 제약 조건 및 동작](virtual-network-manage-peering.md#about-peering)에 철저하게 익숙해집니다.
- 모든 [가상 네트워크 피어링 설정](virtual-network-manage-peering.md#create-peering)에 대해 알아봅니다.
- 가상 네트워크 피어링을 통해 [허브 및 스포크 네트워크 토폴로지를 만드는](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering) 방법을 알아봅니다.

