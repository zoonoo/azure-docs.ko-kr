---
title: "Azure 가상 네트워크 서브넷 만들기, 변경 또는 삭제 | Microsoft Docs"
description: "가상 네트워크 서브넷을 만들거나, 변경하거나, 삭제하는 방법에 대해 알아봅니다."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/10/2017
ms.author: jdial
ms.translationtype: Human Translation
ms.sourcegitcommit: 17c4dc6a72328b613f31407aff8b6c9eacd70d9a
ms.openlocfilehash: 38dcdf2f313c236a507e6a418c39812da16c6345
ms.contentlocale: ko-kr
ms.lasthandoff: 05/16/2017


---
# <a name="create-change-or-delete-virtual-network-subnets"></a>가상 네트워크 서브넷 만들기, 변경 또는 삭제

VNet(가상 네트워크) 서브넷을 만들거나, 변경하거나, 삭제하는 방법에 대해 알아봅니다. VNet에 익숙하지 않은 경우 서브넷을 만들거나, 변경하거나, 삭제하기 전에 [가상 네트워크 개요](virtual-networks-overview.md) 및 [가상 네트워크 만들기, 변경 또는 삭제](virtual-network-manage-network.md) 문서를 참조하는 것이 좋습니다. VNet에 연결할 수 있는 Azure 리소스는 VNet 내의 서브넷에 연결됩니다. 일반적으로 VNet 내에 여러 개의 서브넷을 만들어 다음과 같은 작업을 수행합니다.
- **서브넷 간 트래픽 필터링:** 서브넷에 NSG(네트워크 보안 그룹)를 적용하여 VNet에 연결된 모든 리소스(예: 가상 컴퓨터)에 대한 인바운드 및 아웃바운드 네트워크 트래픽을 필터링할 수 있습니다. NSG를 만드는 방법에 대한 자세한 내용은 [네트워크 보안 그룹 만들기](virtual-networks-create-nsg-arm-pportal.md) 문서를 참조하세요.
- **서브넷 간 라우팅 제어:** 트래픽이 서브넷 간에 자동으로 라우팅되도록 Azure에서 기본 경로를 만듭니다. UDR(사용자 정의 경로)을 만들어 기본 Azure 경로를 재정의할 수 있습니다. UDR에 대한 자세한 내용은 [사용자 정의 경로 만들기](virtual-network-create-udr-arm-ps.md) 문서를 참조하세요. 

이 문서에서는 Azure Resource Manager 배포 모델을 통해 VNet에 대한 서브넷을 만들고, 변경하고, 삭제하는 방법에 대해 설명합니다.
 
## <a name="before"></a>시작하기 전에

이 문서에서 설명하는 모든 섹션의 단계를 수행하기 전에 다음 작업을 완료해야 합니다.

- VNet과 서브넷을 처음 사용하는 경우 이 문서를 참조하기 전에 [첫 번째 Azure 가상 네트워크 만들기](virtual-network-get-started-vnet-subnet.md)의 연습을 수행하는 것이 좋습니다. 이 연습을 수행하면 VNet과 서브넷에 익숙해질 수 있습니다.
- [Azure 제한](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) 문서를 검토하여 VNet과 서브넷에 대한 제한 사항을 알아봅니다.
- Azure 계정이 있는 Azure Portal, Azure CLI(명령줄 인터페이스) 또는 Azure PowerShell에 로그인합니다. 아직 Azure 계정이 없으면 [체험 계정](https://azure.microsoft.com/free)에 등록합니다.
- Azure PowerShell 명령을 사용하여 이 문서의 작업을 수행하는 경우 먼저 [Azure PowerShell을 설치하고 구성해야 합니다](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json). 최신 버전의 Azure PowerShell cmdlet을 설치했는지 확인합니다. 예제와 함께 PowerShell 명령에 대한 도움말을 보려면 `get-help <command> -full`을 입력합니다.
- Azure CLI(명령줄 인터페이스) 명령을 사용하여 이 문서의 작업을 수행하는 경우 먼저 [Azure CLI를 설치하고 구성해야 합니다](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json). 최신 버전의 Azure CLI를 설치했는지 확인합니다. CLI 명령에 대한 도움말을 보려면 `az <command> --help`를 입력합니다.


## <a name="create-subnet"></a>서브넷 만들기

1. 구독에 대한 네트워크 참가자 역할에 권한(최소)이 할당된 계정으로 [포털](https://portal.azure.com)에 로그인합니다. 계정에 역할 및 권한을 할당하는 방법에 대한 자세한 내용은 [Azure 역할 기반 액세스 제어의 기본 제공 역할](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) 문서를 참조하세요.
2. *리소스 검색* 텍스트가 있는 Azure Portal 위쪽의 상자에서 *가상 네트워크*를 입력합니다. 검색 결과에 표시되는 **가상 네트워크**를 클릭합니다.
3. 표시되는 **가상 네트워크** 블레이드에서 서브넷을 추가하려는 가상 네트워크를 클릭합니다.
4. 선택한 가상 네트워크에 대해 표시되는 창에서 **서브넷**을 클릭합니다.
5. **+ 서브넷**을 클릭합니다.
6. **서브넷 추가** 블레이드에서 다음 매개 변수에 대한 값을 입력합니다.
    - **이름:** 가상 네트워크 내에서 고유해야 합니다.
    - **주소 범위:** VNet의 주소 공간 내에서 고유해야 하며, VNet 내의 다른 서브넷 주소 범위와 겹칠 수 없습니다. 주소 공간은 CIDR 표기법을 사용하여 지정해야 합니다. 예를 들어 주소 공간이 10.0.0.0/16인 VNet에서 서브넷 주소 공간으로 10.0.0.0/24를 정의할 수 있습니다. 지정할 수 있는 최소 범위는 /29이며, 서브넷에 대해 8개의 IP 주소를 제공합니다. Azure는 프로토콜 준수를 위해 각 서브넷의 첫 번째 및 마지막 주소를 예약합니다. 세 개의 추가 주소가 Azure 서비스를 사용하기 위해 예약되어 있습니다. 결과적으로 주소 범위가 /29인 서브넷을 정의하면 서브넷에 사용할 수 있는 3개의 IP 주소가 만들어집니다. VPN Gateway에 VNet을 연결하려는 경우 게이트웨이 서브넷을 만들어야 합니다. [게이트웨이 서브넷에 대한 특정 주소 범위 고려 사항](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#a-namegwsubagateway-subnet)에서 대해 자세히 알아보세요. 특정 조건에서 서브넷을 만든 후에 주소 범위를 변경할 수 있습니다. 서브넷 주소 범위를 변경하는 방법에 대해 알아보려면 이 문서의 [서브넷 변경](#change-subnet) 섹션을 참조하세요.
    - **NSG(네트워크 보안 그룹):** 선택적으로 기존 NSG를 서브넷에 연결하여 서브넷에 대한 인바운드 및 아웃바운드 네트워크 트래픽 필터링을 제어할 수 있습니다. NSG는 VNet과 동일한 구독 및 위치에 있어야 하며 Resource Manager 배포 모델을 통해 만들어야 합니다. NSG를 만드는 방법에 대한 자세한 내용은 [네트워크 보안 그룹](virtual-networks-create-nsg-arm-pportal.md)을 참조하세요.
    - **경로 테이블:** 선택적으로 기존 네트워크 경로 테이블을 서브넷에 연결하여 다른 네트워크에 대한 네트워크 트래픽 라우팅을 제어할 수 있습니다. 경로 테이블은 VNet과 동일한 구독 및 위치에 있어야 하며 Resource Manager 배포 모델을 통해 만들어야 합니다. 경로 테이블을 만드는 방법에 대한 자세한 내용은 [사용자 정의 경로](virtual-network-create-udr-arm-ps.md) 문서를 참조하세요.
    - **사용자**: 기본 제공 역할 또는 사용자 지정 역할을 사용하여 서브넷에 대한 액세스를 제어할 수 있습니다. 역할과 사용자를 할당하여 서브넷에 액세스하는 방법에 대한 자세한 내용은 [역할 할당을 사용하여 Azure 리소스에 대한 액세스 관리](../active-directory/role-based-access-control-configure.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-access) 문서를 참조하세요.
7. **확인** 단추를 클릭하여 선택한 VNet에 서브넷을 추가합니다.

**명령**

|도구|명령|
|---|---|
|CLI|[az network vnet subnet create](/cli/azure/network/vnet/subnet?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|PowerShell|[New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig?view=azurermps-3.8.0?toc=%2fazure%2fvirtual-network%2ftoc.json), [Add-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/add-azurermvirtualnetworksubnetconfig?view=azurermps-3.8.0?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="change-subnet"></a>서브넷 설정 변경

서브넷에 연결된 리소스를 사용하여 서브넷에 대한 NSG, 경로 테이블 및 사용자 액세스를 변경할 수 있습니다. 이러한 설정에 대해 알아보려면 이 문서의 [서브넷 만들기](#create-subnet) 섹션에서 설명하는 6단계를 참조하세요. 서브넷의 주소 공간을 변경하려면 어떤 리소스도 해당 서브넷에 연결하면 안됩니다. 서브넷에 연결된 리소스가 있으면 주소 범위를 변경하기 전에 먼저 서브넷에 연결된 리소스를 삭제해야 합니다. 리소스를 삭제하는 방법에 대한 지침은 리소스에 따라 다릅니다. 서브넷에 연결된 리소스를 삭제하는 방법에 대해 알아보려면 삭제하려는 각 리소스 종류에 대한 설명서를 참조하세요. 서브넷의 주소 범위를 변경하려면 다음 단계를 완료합니다.

1. 구독에 대한 네트워크 참가자 역할에 권한(최소)이 할당된 계정으로 [포털](https://portal.azure.com)에 로그인합니다. 계정에 역할 및 권한을 할당하는 방법에 대한 자세한 내용은 [Azure 역할 기반 액세스 제어의 기본 제공 역할](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) 문서를 참조하세요.
2. *리소스 검색* 텍스트가 있는 포털 위쪽의 상자에서 *가상 네트워크*를 입력합니다. 검색 결과에 표시되는 **가상 네트워크**를 클릭합니다.
3. 표시되는 **가상 네트워크** 블레이드에서 서브넷 주소 범위를 변경하려는 VNet을 클릭합니다.
4. 주소 범위를 변경하려는 서브넷을 클릭합니다.
5. 선택한 서브넷에 대해 표시되는 블레이드의 **주소 범위** 상자에서 새 주소 범위를 입력합니다. 범위는 VNet의 주소 공간 내에서 고유해야 하며, VNet 내의 다른 서브넷 주소 범위와 겹칠 수 없습니다. 주소 공간은 CIDR 표기법을 사용하여 지정해야 합니다. 예를 들어 주소 공간이 10.0.0.0/16인 VNet에서 서브넷 주소 공간으로 10.0.0.0/24를 정의할 수 있습니다. 지정할 수 있는 최소 범위는 /29이며, 서브넷에 대해 8개의 IP 주소를 제공합니다. Azure는 프로토콜 준수를 위해 각 서브넷의 첫 번째 및 마지막 주소를 예약합니다. 세 개의 추가 주소가 Azure 서비스를 사용하기 위해 예약되어 있습니다. 결과적으로 주소 범위가 /29인 서브넷에는 사용할 수 있는 3개의 IP 주소가 있습니다. VPN Gateway에 VNet을 연결하려는 경우 게이트웨이 서브넷을 만들어야 합니다. [게이트웨이 서브넷에 대한 특정 주소 범위 고려 사항](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#a-namegwsubagateway-subnet)에서 대해 자세히 알아보세요. 특정 조건에서 서브넷을 만든 후에 주소 범위를 변경할 수 있습니다. 서브넷 주소 범위를 변경하는 방법에 대해 알아보려면 이 문서의 [서브넷 변경](#change-subnet) 섹션을 참조하세요.
6. **Save**를 클릭합니다.

**명령**

|도구|명령|
|---|---|
|CLI|[az network vnet subnet update](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|PowerShell|[Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig?view=azurermps-3.8.0?toc=%2fazure%2fvirtual-network%2ftoc.json)|


## <a name="delete-subnet"></a>서브넷 삭제

서브넷에 연결된 리소스가 없는 경우에만 해당 서브넷을 삭제할 수 있습니다. 서브넷에 연결된 리소스가 있으면 먼저 서브넷에 연결된 리소스를 삭제해야 합니다. 리소스를 삭제하는 방법에 대한 지침은 리소스에 따라 다릅니다. 서브넷에 연결된 리소스를 삭제하는 방법에 대해 알아보려면 삭제하려는 각 리소스 종류에 대한 설명서를 참조하세요.

1. 구독에 대한 네트워크 참가자 역할에 권한(최소)이 할당된 계정으로 [포털](https://portal.azure.com)에 로그인합니다. 계정에 역할 및 권한을 할당하는 방법에 대한 자세한 내용은 [Azure 역할 기반 액세스 제어의 기본 제공 역할](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) 문서를 참조하세요.
2. *리소스 검색* 텍스트가 있는 Azure Portal 위쪽의 상자에서 *가상 네트워크*를 입력합니다. 검색 결과에 표시되는 **가상 네트워크**를 클릭합니다.
3. 표시되는 **가상 네트워크** 블레이드에서 서브넷을 삭제하려는 VNet을 클릭합니다.
4. 선택한 VNet에 대해 표시되는 블레이드의 **설정** 아래에서 **서브넷**을 클릭합니다.
5. 서브넷 블레이드에 표시되는 서브넷 목록에서 삭제하려는 서브넷을 마우스 오른쪽 단추로 클릭하고 **삭제**를 클릭한 다음 **예**를 클릭하여 해당 서브넷을 삭제합니다.

**명령**

|도구|명령|
|---|---|
|CLI|[az network vnet delete](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#delete)|
|PowerShell|[Remove-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/remove-azurermvirtualnetworksubnetconfig?view=azurermps-3.8.0?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="next-steps"></a>다음 단계

VM을 만들고 서브넷에 연결하려면 [VNet 만들기 및 VM 연결](virtual-network-get-started-vnet-subnet.md#a-namecreate-vmsacreate-virtual-machines) 문서를 참조하세요.
