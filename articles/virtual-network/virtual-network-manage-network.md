---
title: "Azure 가상 네트워크 만들기, 변경 또는 삭제 | Microsoft Docs"
description: "가상 네트워크를 만들고, 변경하고, 삭제하는 방법에 대해 알아봅니다."
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
ms.sourcegitcommit: e7da3c6d4cfad588e8cc6850143112989ff3e481
ms.openlocfilehash: b577891afc52013b712634dd51e7e28efcfc4482
ms.contentlocale: ko-kr
ms.lasthandoff: 05/16/2017


---
# <a name="create-change-or-delete-virtual-networks"></a>가상 네트워크 만들기, 변경 또는 삭제

VNet(가상 네트워크)을 만들고 삭제하는 방법 및 기존 VNet의 DNS 서버 및 IP 주소 공간과 같은 설정을 변경하는 방법에 대해 알아봅니다. VNet은 클라우드의 사용자 네트워크를 나타내는 표현입니다. 구독 전용 Azure 클라우드를 논리적으로 격리한 것이 VNet입니다. 각 VNet에 대해 다음을 수행할 수 있습니다.
- 할당할 주소 공간을 선택합니다. 주소 공간은 10.0.0.0/16과 같이 CIDR 표기법을 사용하여 정의하는 하나 이상의 주소 범위로 구성됩니다.
- 각 VNet에 Azure 제공 DNS 서버 또는 사용자 고유의 DNS 서버를 사용하도록 선택합니다. VNet에 연결된 모든 리소스에는 이 DNS 서버가 할당되어 VNet 내에서 이름을 확인합니다.
- VNet을 해당 VNet의 주소 공간 내에서 고유한 자체의 주소 범위가 있는 서브넷으로 분할합니다. 서브넷을 만들고, 변경하고, 삭제하는 방법에 대해 알아보려면 [서브넷 추가, 변경 또는 삭제](virtual-network-manage-subnet.md) 문서를 참조하세요.

이 문서에서는 Azure Resource Manager 배포 모델을 통해 VNet을 만들고, 변경하고, 삭제하는 방법에 대해 설명합니다.
 
## <a name="before"></a>시작하기 전에

이 문서에서 설명하는 모든 섹션의 단계를 수행하기 전에 다음 작업을 완료해야 합니다.

- VNet을 처음 사용하는 경우 이 문서를 참조하기 전에 [첫 번째 Azure 가상 네트워크 만들기](virtual-network-get-started-vnet-subnet.md)의 연습을 수행하는 것이 좋습니다. 이 연습을 수행하면 VNet에 익숙해질 수 있습니다.
- [Azure 제한](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) 문서를 검토하여 VNet에 대한 제한 사항을 알아봅니다.
- Azure 계정이 있는 Azure Portal, Azure CLI(명령줄 인터페이스) 또는 Azure PowerShell에 로그인합니다. 아직 Azure 계정이 없으면 [체험 계정](https://azure.microsoft.com/free)에 등록합니다.
- Azure PowerShell 명령을 사용하여 이 문서의 작업을 수행하는 경우 먼저 [Azure PowerShell을 설치하고 구성해야 합니다](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json). 최신 버전의 Azure PowerShell cmdlet을 설치했는지 확인합니다. 예제와 함께 PowerShell 명령에 대한 도움말을 보려면 `get-help <command> -full`을 입력합니다.
- Azure CLI(명령줄 인터페이스) 명령을 사용하여 이 문서의 작업을 수행하는 경우 먼저 [Azure CLI를 설치하고 구성해야 합니다](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json). 최신 버전의 Azure CLI를 설치했는지 확인합니다. CLI 명령에 대한 도움말을 보려면 `az <command> --help`를 입력합니다.


## <a name="create-vnet"></a>가상 네트워크 만들기

1. 구독에 대한 네트워크 참가자 역할에 권한(최소)이 할당된 계정으로 [포털](https://portal.azure.com)에 로그인합니다. 계정에 역할 및 권한을 할당하는 방법에 대한 자세한 내용은 [Azure 역할 기반 액세스 제어의 기본 제공 역할](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) 문서를 참조하세요.
2. Azure Portal에서 **+ 새로 만들기**를 클릭합니다. 표시되는 **새로 만들기** 블레이드에서 **네트워킹**을 클릭합니다. 표시되는 **네트워킹** 블레이드에서 **가상 네트워크**를 클릭합니다.
3. 표시되는 **가상 네트워크** 블레이드의 **배포 모델 선택** 상자에서 *Resource Manager*를 선택한 채로 두고 **만들기**를 클릭합니다.
4. 표시되는 **가상 네트워크 만들기** 블레이드에서 다음 설정에 대한 값을 입력하거나 선택한 다음 **만들기**를 클릭합니다.
    - **이름**: VNet을 만들려고 선택하는 [리소스 그룹](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group)에서 고유해야 합니다. VNet을 만든 후에는 이름을 변경할 수 없습니다. 시간이 지남에 따라 여러 VNet을 만들 수 있습니다. 여러 VNet을 보다 쉽게 관리할 수 있도록 이름 지정 제안 에 대한 [명명 규칙](/azure/architecture/best-practices/naming-conventions?toc=%2fazure%2fvirtual-network%2ftoc.json#naming-rules-and-restrictions) 문서를 참조하세요.
    - **주소 공간**: CIDR 표기법으로 지정합니다. 정의하는 주소 공간은 공용 또는 개인 주소가 될 수 있습니다(RFC 1918). 공용 또는 개인 주소 공간 중 어느 것을 정의하든지 간에 주소 공간은 VNet 내, 상호 연결된 VNet 및 VNet에 연결된 모든 온-프레미스 네트워크에서만 연결할 수 있습니다. 다음 주소 공간은 추가할 수 없습니다.
        - 224.0.0.0/4(멀티캐스트)
        - 255.255.255.255/32(브로드캐스트)
        - 127.0.0.0/8(루프백)
        - 169.254.0.0/16(링크-로컬)
        - 168.63.129.16/32(내부 DNS)
    
      VNet을 만들 때는 주소 공간 하나만 정의할 수 있지만, VNet을 만든 후에는 추가 주소 공간을 추가할 수 있습니다. 이러한 방법에 대해 알아보려면 이 문서의 [주소 공간 추가/제거](#add-address-spaces) 섹션에서 설명하는 단계를 완료합니다.

      >[!WARNING]
      >VNet이 다른 VNet 또는 온-프레미스 네트워크와 겹치는 주소 공간을 사용하는 경우 함께 연결할 수 없습니다. 주소 공간을 정의하기 전에 나중에 해당 VNet에 연결할 수 있는 다른 VNet 또는 온-프레미스 네트워크를 고려하는 것이 좋습니다.
      >
      >
    
    - **서브넷 이름:** VNet 내에서 고유해야 합니다. 서브넷을 만든 후에는 이름을 변경할 수 없습니다. VNet에 서브넷이 필요하지는 않지만 포털에서는 VNet을 만들 때 하나의 서브넷을 정의해야 합니다. 포털을 사용하면 VNet을 만들 때 서브넷 하나만 정의할 수 있지만 이 VNet을 만든 후에 만드는 VNet에 추가 서브넷을 추가할 수 있습니다. VNet에 서브넷을 추가하려면 [서브넷 만들기, 변경 또는 삭제](virtual-network-manage-subnet.md) 문서의 [서브넷 만들기](virtual-network-manage-subnet.md#create-subnet) 섹션을 참조하세요. CLI 또는 PowerShell을 사용하여 여러 서브넷이 있는 VNet을 만들 수 있습니다.

      >[!TIP]
      >서로 다른 서브넷 간의 트래픽 라우팅을 필터링하거나 제어하기 위해 별도의 서브넷이 종종 만들어집니다. 서브넷을 정의하기 전에 서브넷 간 트래픽을 필터링하고 라우팅하는 방법을 고려하는 것이 좋습니다. 서브넷 간의 트래픽을 필터링하는 방법에 대한 자세한 내용은 [네트워크 보안 그룹](virtual-networks-nsg.md) 문서를 참조하세요. Azure에서는 서브넷 간에 자동으로 라우팅하지만 Azure의 기본 경로를 재정의할 수 있습니다. 이러한 방법에 대해 알아보려면 [사용자 정의 경로](virtual-networks-udr-overview.md) 문서를 참조하세요.
      >

    - **서브넷 주소 범위:** VNet에 대해 입력한 **주소 공간** 내에 있어야 합니다. 지정할 수 있는 최소 범위는 /29이며, 서브넷에 대해 8개의 IP 주소를 제공합니다. Azure는 프로토콜 준수를 위해 각 서브넷의 첫 번째 및 마지막 주소를 예약합니다. 세 개의 추가 주소가 Azure 서비스를 사용하기 위해 예약되어 있습니다. 결과적으로 서브넷 주소 범위가 /29인 VNet에는 세 개의 IP 주소만 사용할 수 있습니다. VPN Gateway에 VNet을 연결하려는 경우 게이트웨이 서브넷을 만들어야 합니다. [게이트웨이 서브넷에 대한 특정 주소 범위 고려 사항](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#a-namegwsubagateway-subnet)에서 대해 자세히 알아보세요. 특정 조건에서 서브넷을 만든 후에 주소 범위를 변경할 수 있습니다. 서브넷 주소 범위를 변경하는 방법에 대해 알아보려면 [서브넷 추가, 변경 또는 삭제](virtual-network-manage-subnet.md) 문서의 [서브넷 변경](#change-subnet) 섹션을 참조하세요.
    - **구독**: [구독](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription)을 선택합니다. VNet은 Azure VPN Gateway 또는 VNet 피어링을 사용하여 다른 구독의 VNet에 연결할 수 있지만, 구독을 확장할 수는 없습니다. VNet에 연결하는 Azure 리소스는 동일한 구독에 있어야 합니다.
    - **리소스 그룹**: 기존 [리소스 그룹](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-groups)을 선택하거나 새 리소스 그룹을 만듭니다. VNet에 연결하는 Azure 리소스는 동일하거나 다른 리소스 그룹에 있을 수 있습니다.
    - **위치:** 지역이라고도 하는 Azure [위치](https://azure.microsoft.com/regions/)를 선택합니다. VNet은 Azure 위치를 확장할 수 없지만, Azure VPN Gateway를 사용하여 한 위치의 VNet을 다른 위치의 VNet에 연결할 수 있습니다. VNet에 연결하는 Azure 리소스는 동일한 위치에 있어야 합니다.

**명령**

|도구|명령|
|---|---|
|CLI|[az network vnet create](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|PowerShell|[새-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name = "view-vnet"></a>가상 네트워크 및 설정 보기

1. 구독에 대한 네트워크 참가자 역할에 권한(최소)이 할당된 계정으로 [포털](https://portal.azure.com)에 로그인합니다. 계정에 역할 및 권한을 할당하는 방법에 대한 자세한 내용은 [Azure 역할 기반 액세스 제어의 기본 제공 역할](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) 문서를 참조하세요.
2. *리소스 검색* 텍스트가 있는 포털 위쪽의 상자에서 *가상 네트워크*를 입력합니다. 검색 결과에 표시되는 **가상 네트워크**를 클릭합니다.
3. 표시되는 **가상 네트워크** 블레이드에서 설정을 보려는 VNet을 클릭합니다.
4. 선택한 VNet에 대해 표시되는 블레이드에서 다음 설정이 나열됩니다.
    - **개요:** 주소 공간 및 DNS 서버와 같이 VNet에 대한 정보를 제공합니다. 다음 그림에서는 **MyVNet**이라는 VNet에 대한 개요 설정을 보여 줍니다.
    
        ![네트워크 인터페이스 개요](./media/virtual-network-manage-network/vnet-overview.png)

      이 블레이드에서 VNet을 다른 구독 또는 리소스 그룹으로 이동할 수 있습니다. VNet을 이동하는 방법에 대해 알아보려면 [다른 리소스 그룹 또는 구독으로 리소스 이동](../azure-resource-manager/resource-group-move-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 문서를 참조하세요. 필수 조건과 Azure Portal, PowerShell 및 Azure CLI를 사용하여 리소스를 이동하는 방법을 나열하고 있습니다. VNet에 연결된 모든 리소스도 VNet과 함께 이동해야 합니다. 
    - **주소 공간:** VNet에 할당된 주소 공간이 나열됩니다. 주소 공간을 추가하고 제거하는 방법에 대해 알아보려면 이 문서의 [주소 공간 추가/제거](#address-spaces) 섹션에서 설명하는 단계를 완료합니다.
    - **연결된 장치:** VNet에 연결된 모든 리소스가 표시됩니다. 앞의 그림에서 보여 주는 예에서는 세 개의 네트워크 인터페이스와 하나의 부하 분산 장치가 VNet에 연결되어 있습니다. VNet에 만들고 연결한 새 리소스가 모두 나열됩니다. VNet에 연결된 리소스를 삭제하면 목록에 더 이상 표시되지 않습니다.
    - **서브넷:** VNet 내에 있는 서브넷의 목록입니다. 서브넷을 추가하고 제거하는 방법에 대해 알아보려면 [서브넷 추가, 변경 또는 삭제](virtual-network-manage-subnet.md) 문서의 [서브넷 추가](virtual-network-manage-subnet.md#create-subnet) 및 [서브넷 삭제](virtual-network-manage-subnet.md#delete-subnet) 섹션을 참조하세요.
    - **DNS 서버:** Azure 내부 DNS 서버 또는 사용자 지정 DNS 서버에서 VNet에 연결된 장치에 대한 이름 확인을 제공하는지 여부를 지정할 수 있습니다. Azure Portal을 사용하여 VNet을 만들 때 기본적으로 Azure의 DNS 서버가 VNet 내에서 이름을 확인하는 데 사용됩니다. DNS 서버를 수정하려면 이 문서의 [DNS 서버 추가/변경/제거](#dns-servers) 섹션에서 설명하는 단계를 완료합니다. 
    - **피어링:** 구독에 기존 피어링이 있는 경우 여기에 나열됩니다. 기존 피어링에 대한 설정을 보거나, 피어링을 만들거나 변경하거나 삭제할 수 있습니다. 피어링에 대해 자세히 알아보려면 [피어링 개요](virtual-network-peering-overview.md) 문서를 참조하세요.
    - **속성:** VNet의 리소스 ID 및 존재하는 구독을 포함하여 VNet에 대한 설정을 표시합니다.
    - **다이어그램:** 해당 장치에 대한 일부 주요 정보와 함께 VNet에 연결된 모든 장치를 시각적으로 보여 줍니다. 이 보기를 통해 장치 중 하나를 클릭하여 직접 관리할 수 있습니다.
    - **일반적인 Azure 설정:** 일반적인 Azure 설정에 대해 자세히 알아보려면 [활동 로그](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#activity-logs), [IAM(액세스 제어)](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#access-control), [태그](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#tags), [잠금](../azure-resource-manager/resource-group-lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 및 [자동화 스크립트](../azure-resource-manager/resource-manager-export-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json#export-the-template-from-resource-group) 문서를 참조하세요.

**명령**

|**도구**|**명령**|
|---|---|
|CLI|[az network vnet show](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#show)|
|PowerShell|[Get-AzureRmVirtualNetwork](/powershell/resourcemanager/azurerm.network/v3.8.0/get-azurermvirtualnetwork/?toc=%2fazure%2fvirtual-network%2ftoc.json)|


## <a name="address-spaces"></a>주소 공간 추가/제거

VNet에서 주소 공간을 추가하거나 제거할 수 있습니다. 주소 공간은 CIDR 표기법으로 지정해야 하며, 동일한 VNet 내에서 겹칠 수 없습니다. 정의하는 주소 공간은 공용 또는 개인 주소가 될 수 있습니다(RFC 1918). 공용 또는 개인 주소 공간 중 어느 것을 정의하든지 간에 VNet 내 주소 공간은 상호 연결된 VNet 및 VNet에 연결된 모든 온-프레미스 네트워크에서만 연결할 수 있습니다. 다음 주소 공간은 추가할 수 없습니다.
    - 224.0.0.0/4(멀티캐스트)
    - 255.255.255.255/32(브로드캐스트)
    - 127.0.0.0/8(루프백)
    - 169.254.0.0/16(링크-로컬)
    - 168.63.129.16/32(내부 DNS)

1. 구독에 대한 네트워크 참가자 역할에 권한(최소)이 할당된 계정으로 [포털](https://portal.azure.com)에 로그인합니다. 계정에 역할 및 권한을 할당하는 방법에 대한 자세한 내용은 [Azure 역할 기반 액세스 제어의 기본 제공 역할](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) 문서를 참조하세요.
2. *리소스 검색* 텍스트가 있는 Azure Portal 위쪽의 상자에서 *가상 네트워크*를 입력합니다. 검색 결과에 표시되는 **가상 네트워크**를 클릭합니다.
3. 표시되는 **가상 네트워크** 블레이드에서 주소 공간을 추가하거나 제거할 가상 네트워크를 클릭합니다.
4. 선택한 VNet에 대해 표시되는 블레이드의 **설정** 섹션에서 **주소 공간**을 클릭합니다.
5. 주소 공간과 함께 표시되는 블레이드에서 다음 옵션 중 하나를 수행합니다.
    - **주소 공간 추가:** 상자에서 새 주소 공간을 입력합니다. 주소 공간은 VNet에 대해 정의된 기존 주소 공간과 겹칠 수 없습니다.
    - **주소 공간 제거:** 주소 공간을 마우스 오른쪽 단추로 클릭한 다음 **제거**를 클릭합니다. 주소 공간에 서브넷이 있으면 해당 주소 공간을 제거할 수 없습니다. 따라서 주소 공간을 제거하기 전에 주소 공간(및 서브넷에 연결된 모든 리소스)에 있는 모든 서브넷을 삭제해야 합니다.
6. **Save**를 클릭합니다.

**명령**

|도구|명령|
|---|---|
|CLI|리소스 관리자에만 해당|[az network vnet update](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|PowerShell|[Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork?view=azurermps-3.8.0?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="dns-servers"></a>DNS 서버 추가/변경/제거

VNet에 연결된 모든 VM은 VNet에 지정된 DNS 서버에 등록하고, 이름을 확인하는 데 DNS 서버를 사용합니다. VM의 각 NIC(네트워크 인터페이스)에는 자체의 DNS 서버 설정이 있을 수 있습니다. NIC에 자체의 DNS 서버 설정이 있는 경우 VNet에 대한 DNS 서버 설정이 이 설정으로 재정의됩니다. NIC DNS 설정에 대해 자세히 알아보려면 [네트워크 인터페이스 작업 및 설정](virtual-network-network-interface.md#dns) 문서를 참조하세요. VM 및 Cloud Services 역할 인스턴스의 이름 확인에 대해 자세히 알아보려면 [VM 및 역할 인스턴스에 대한 이름 확인](virtual-networks-name-resolution-for-vms-and-role-instances.md) 문서를 참조하세요.

1. 구독에 대한 네트워크 참가자 역할에 권한(최소)이 할당된 계정으로 [포털](https://portal.azure.com)에 로그인합니다. 계정에 역할 및 권한을 할당하는 방법에 대한 자세한 내용은 [Azure 역할 기반 액세스 제어의 기본 제공 역할](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) 문서를 참조하세요.
2. *리소스 검색* 텍스트가 있는 Azure Portal 위쪽의 상자에서 *가상 네트워크*를 입력합니다. 검색 결과에 표시되는 **가상 네트워크**를 클릭합니다. 
3. 표시되는 **가상 네트워크** 블레이드에서 DNS 설정을 변경하려는 가상 네트워크를 클릭합니다.
4. 선택한 VNet에 대해 표시되는 블레이드의 **설정** 섹션에서 **DNS 서버**를 클릭합니다.
5. DNS 서버와 함께 표시되는 블레이드에서 다음 옵션 중 하나를 선택합니다.
    - **기본값(Azure에서 제공):** 모든 리소스 이름 및 개인 IP 주소가 Azure DNS 서버에 자동으로 등록됩니다. 동일한 VNet에 연결된 모든 리소스 간에 이름을 확인할 수 있습니다. 이 옵션을 사용하면 VNet에서 이름을 확인할 수 없습니다. VNet에서 이름을 확인하려면 사용자 지정 DNS 서버를 사용해야 합니다.
    - **사용자 지정:** VNet에 대한 Azure 제한까지 하나 이상의 서버를 추가할 수 있습니다. DNS 서버 제한에 대해 자세히 알아보려면 [Azure 제한](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual-networking-limits-classic) 문서를 참조하세요. 다음 옵션이 있습니다.
        - **주소 추가:** 가상 네트워크 DNS 서버 목록에 서버를 추가하고, 해당 DNS 서버를 Azure에도 등록합니다. 이전에 Azure에 DNS 서버를 등록한 경우 표시되는 목록에서 선택할 수 있습니다. 
        - **주소 제거:** 제거하려는 서버 옆에 있는 **X**를 클릭합니다. 서버를 삭제하면 이 VNet 목록에서만 제거됩니다. 다른 VNet에서 사용할 수 있도록 해당 DNS 서버가 Azure에 등록된 상태로 유지됩니다. 
        - **DNS 서버 주소 다시 정렬**: 사용자 환경에 맞게 올바른 순서로 DNS 서버를 나열하는지 확인하는 것이 중요합니다. DNS 서버 목록은 라운드 로빈 방식으로 작동하지 않으며, 지정된 순서로 사용됩니다. 목록의 첫 번째 DNS 서버에 연결할 수 있으면 DNS 서버가 제대로 작동하는지 여부와 관계없이 클라이언트에서 해당 DNS 서버를 사용합니다. 나열된 모든 DNS 서버를 제거한 다음 원하는 순서로 다시 추가합니다.
        - **주소 변경**: 목록에서 DNS 서버를 강조 표시한 다음 새 이름을 입력합니다.
6. **Save**를 클릭합니다.
7. VNet에 연결된 VM을 다시 시작하여 새 DNS 서버 설정을 할당합니다. VM이 다시 시작될 때까지 현재 DNS 설정을 계속 사용합니다.

**명령**

|도구|명령|
|---|---|
|CLI|[az network vnet update](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|PowerShell|[Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork?view=azurermps-3.8.0?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="delete-vnet"></a>가상 네트워크 삭제

VNet에 연결된 리소스가 없는 경우에만 해당 VNet을 삭제할 수 있습니다. VNet 내의 서브넷에 연결된 리소스가 있는 경우 먼저 VNet 내의 모든 서브넷에 연결된 리소스를 삭제해야 합니다. 리소스를 삭제하는 방법에 대한 지침은 리소스에 따라 다릅니다. 서브넷에 연결된 리소스를 삭제하는 방법에 대해 알아보려면 삭제하려는 각 리소스 종류에 대한 설명서를 참조하세요. VNet을 삭제하려면 다음 단계를 수행합니다.

1. 구독에 대한 네트워크 참가자 역할에 권한(최소)이 할당된 계정으로 [포털](https://portal.azure.com)에 로그인합니다. 계정에 역할 및 권한을 할당하는 방법에 대한 자세한 내용은 [Azure 역할 기반 액세스 제어의 기본 제공 역할](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) 문서를 참조하세요.
2. *리소스 검색* 텍스트가 있는 Azure Portal 위쪽의 상자에서 *가상 네트워크*를 입력합니다. 검색 결과에 표시되는 **가상 네트워크**를 클릭합니다.
3. 표시되는 **가상 네트워크** 블레이드에서 삭제하려는 VNet을 클릭합니다.
4. 선택한 VNet에 대해 표시되는 블레이드의 **설정** 섹션에서 **연결된 장치**를 클릭하여 VNet에 연결된 장치가 없는지 확인합니다. 연결된 장치가 있으면 VNet을 삭제하기 전에 먼저 이러한 장치를 삭제해야 합니다.  연결된 장치가 없으면 블레이드에서 **개요**를 클릭합니다.
5. 블레이드 위쪽의 **삭제** 아이콘을 클릭합니다. 
6. **예**를 클릭하여 VNet 삭제를 확인합니다.


**명령**

|도구|명령|
|---|---|
|CLI|[azure network vnet delete](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#delete)|
|PowerShell|[Remove-AzureRmVirtualNetwork](/powershell/module/azurerm.network/remove-azurermvirtualnetwork?view=azurermps-3.8.0?toc=%2fazure%2fvirtual-network%2ftoc.json)|


## <a name="next-steps"></a>다음 단계

- VM을 만들어 VNet에 연결하려면 [VNet 만들기 및 VM 연결](virtual-network-get-started-vnet-subnet.md#a-namecreate-vmsacreate-virtual-machines) 문서를 참조하세요.
- VNet 내의 서브넷 간 네트워크 트래픽을 필터링하려면 [네트워크 보안 그룹 만들기](virtual-networks-create-nsg-arm-pportal.md) 문서를 참조하세요.
- VNet을 다른 VNet과 피어링하려면 [가상 네트워크 피어링 만들기](virtual-networks-create-vnetpeering-arm-portal.md#peering-vnets-in-the-same-subscription) 문서를 참조하세요.
- VNet을 온-프레미스 네트워크에 연결하는 옵션에 대해 알아보려면 [네트워크 게이트웨이 정보](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#a-namediagramsaconnection-topology-diagrams) 문서를 참조하세요.

