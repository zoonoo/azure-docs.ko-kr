---
title: "Azure Virtual Network 만들기, 변경 또는 삭제 | Microsoft Docs"
description: "Azure에서 가상 네트워크를 만들고 변경하거나 삭제하는 방법을 알아봅니다."
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
ms.translationtype: HT
ms.sourcegitcommit: 7bf5d568e59ead343ff2c976b310de79a998673b
ms.openlocfilehash: beb4f74012359f6a0a837f0194c7f43746977cea
ms.contentlocale: ko-kr
ms.lasthandoff: 08/01/2017

---
# <a name="create-change-or-delete-a-virtual-network"></a>가상 네트워크 만들기, 변경 또는 삭제

가상 네트워크를 만들고 삭제하는 방법 및 기존 가상 네트워크의 DNS 서버 및 IP 주소 공간과 같은 설정을 변경하는 방법에 대해 알아봅니다.

가상 네트워크는 클라우드의 사용자 네트워크를 나타내는 표현입니다. 가상 네트워크는 Azure 구독 전용 Azure 클라우드를 논리적으로 격리한 것입니다. 만드는 각 가상 네트워크에 대해 다음을 수행할 수 있습니다.
- 할당할 주소 공간을 선택합니다. 주소 공간은 CIDR(Classless Inter-Domain Routing) 표기법(예: 10.0.0.0/16)을 사용하여 정의된 하나 이상의 주소 범위로 구성됩니다.
- Azure 제공 DNS 서버 또는 사용자 고유의 DNS 서버를 사용하도록 선택합니다. 가상 네트워크에 연결된 모든 리소스에는 이 DNS 서버가 할당되어 가상 네트워크 내에서 이름을 확인합니다.
- 가상 네트워크를 해당 가상 네트워크의 주소 공간 내에서 고유한 자체의 주소 범위가 있는 서브넷으로 분할합니다. 서브넷을 만들고, 변경하고, 삭제하는 방법에 대해 알아보려면 [서브넷 추가, 변경 또는 삭제](virtual-network-manage-subnet.md)를 참조하세요.

이 문서에서는 Azure Resource Manager 배포 모델을 사용하여 가상 네트워크를 만들고, 변경하고, 삭제하는 방법에 대해 설명합니다.

## <a name="before"></a>시작하기 전에

이 문서에 설명된 작업을 시작하기 전에 다음 전제 조건을 완료합니다.

- 가상 네트워크에서 처음 작업하는 경우 [첫 번째 Azure Virtual Network 만들기](virtual-network-get-started-vnet-subnet.md)의 연습을 검토하는 것이 좋습니다. 이 연습을 통해 가상 네트워크에 친숙해질 수 있습니다.
- 가상 네트워크에 대한 제한 사항은 [Azure 제한](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)을 참조하세요.
- Azure 계정을 사용하여 Azure Portal, Azure CLI(명령줄 도구) 또는 Azure PowerShell에 로그인합니다. Azure 계정이 없으면 [무료 평가판 계정](https://azure.microsoft.com/free)에 등록합니다.
- PowerShell 명령을 사용하여 이 문서의 작업을 수행하려면 먼저 [Azure PowerShell을 설치 및 구성](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json)해야 합니다. 최신 버전의 Azure PowerShell cmdlet을 설치했는지 확인합니다. 예제에서 PowerShell 명령에 대한 도움말을 보려면 `get-help <command> -full`을 입력합니다.
- Azure CLI 명령을 사용하여 이 문서의 작업을 수행하려면 먼저 [Azure CLI를 설치 및 구성](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)해야 합니다. 최신 버전의 Azure CLI를 설치했는지 확인합니다. Azure CLI 명령에 대한 도움말을 보려면 `az <command> --help`를 입력합니다.


## <a name="create-vnet"></a>가상 네트워크 만들기

가상 네트워크를 만들려면

1. 구독에 대한 네트워크 참가자 역할에 권한(최소)이 할당된 계정으로 [포털](https://portal.azure.com)에 로그인합니다. 계정에 역할 및 권한을 할당하는 방법에 대한 자세한 내용은 [Azure 역할 기반 액세스 제어의 기본 제공 역할](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)을 참조하세요.
2. **새로 만들기** > **네트워킹** > **가상 네트워크**를 클릭합니다.
3. **가상 네트워크** 블레이드의 **배포 모델 선택** 상자에서 **Resource Manager**를 선택된 상태로 두고 **만들기**를 클릭합니다.
4. **가상 네트워크 만들기** 블레이드에서 다음 설정에 대한 값을 입력하거나 선택한 다음 **만들기**를 클릭합니다.
    - **이름**: 가상 네트워크를 만들려고 선택하는 [리소스 그룹](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group)에서 고유해야 합니다. 가상 네트워크를 만든 후 이름을 변경할 수 없습니다. 시간이 지남에 따라 여러 가상 네트워크를 만들 수 있습니다. 명명 제안에 대해서는 [명명 규칙](/azure/architecture/best-practices/naming-conventions.md?toc=%2fazure%2fvirtual-network%2ftoc.json#naming-rules-and-restrictions)을 참조하세요. 명명 규칙을 따르면 여러 가상 네트워크를 보다 쉽게 관리할 수 있습니다.
    - **주소 공간**: CIDR 표기법으로 주소 공간을 지정합니다. 정의하는 주소 공간은 공용 또는 개인 주소가 될 수 있습니다(RFC 1918). 주소 공간을 공용으로 정의하든 개인으로 정의하든 가상 네트워크 내, 상호 연결된 가상 네트워크 및 가상 네트워크에 연결된 모든 온-프레미스 네트워크에서만 주소 공간에 연결할 수 있습니다. 다음 주소 공간은 추가할 수 없습니다.
        - 224.0.0.0/4(멀티캐스트)
        - 255.255.255.255/32(브로드캐스트)
        - 127.0.0.0/8(루프백)
        - 169.254.0.0/16(링크-로컬)
        - 168.63.129.16/32(내부 DNS)

      가상 네트워크를 만들 때 주소 공간을 하나만 정의할 수 있지만 가상 네트워크를 만든 후 더 많은 주소 공간을 추가할 수 있습니다. 기존 가상 네트워크에 주소 공간을 추가하는 방법은 이 문서의 [주소 공간 추가 또는 제거](#add-address-spaces)를 참조하세요.

      >[!WARNING]
      >가상 네트워크에 다른 가상 네트워크 또는 온-프레미스 네트워크와 겹치는 주소 공간이 있는 경우 두 네트워크를 연결할 수 없습니다. 주소 공간을 정의하기 전에 나중에 가상 네트워크를 다른 가상 네트워크 또는 온-프레미스 네트워크에 연결할지 고려해야 합니다.
      >
      >

    - **서브넷 이름**: 서브넷 이름은 가상 네트워크 내에서 고유해야 합니다. 서브넷을 만든 후 서브넷 이름을 변경할 수 없습니다. 가상 네트워크에 서브넷이 필요하지는 않지만 포털에서는 가상 네트워크를 만들 때 하나의 서브넷을 정의해야 합니다. 포털에서 가상 네트워크를 만들 때 서브넷을 하나만 정의할 수 있습니다. 가상 네트워크를 만든 후 나중에 서브넷을 추가할 수 있습니다. 가상 네트워크에 서브넷을 추가하려면 [서브넷 만들기, 변경 또는 삭제](virtual-network-manage-subnet.md)에서 [서브넷 만들기](virtual-network-manage-subnet.md#create-subnet)를 참조하세요. Azure CLI 또는 PowerShell을 사용하여 여러 서브넷이 있는 가상 네트워크를 만들 수 있습니다.

      >[!TIP]
      >경우에 따라 관리자는 여러 서브넷을 만들어 서브넷 간의 트래픽 라우팅을 필터링하거나 제어합니다. 서브넷을 정의하기 전에 서브넷 간에 트래픽을 필터링하고 라우팅하는 방법을 고려하는 것이 좋습니다. 서브넷 간의 트래픽을 필터링하는 방법에 대한 자세한 내용은 [네트워크 보안 그룹](virtual-networks-nsg.md)을 참조하세요. Azure에서는 서브넷 간에 트래픽을 자동으로 라우팅하지만 Azure 기본 경로를 재정의할 수 있습니다. Azure 기본 서브넷 트래픽 라우팅을 재정의하는 방법은 [사용자 정의 경로](virtual-networks-udr-overview.md)를 참조하세요.
      >

    - **서브넷 주소 범위:** 범위는 가상 네트워크에 대해 입력한 주소 공간 내에 있어야 합니다. 지정할 수 있는 최소 범위는 /29이며, 서브넷에 대해 8개의 IP 주소를 제공합니다. Azure는 프로토콜 준수를 위해 각 서브넷의 첫 번째 및 마지막 주소를 예약합니다. 세 개의 추가 주소가 Azure 서비스를 사용하기 위해 예약되어 있습니다. 결과적으로 서브넷 주소 범위가 /29인 가상 네트워크에는 세 개의 IP 주소만 사용할 수 있습니다. 가상 네트워크를 VPN Gateway에 연결하려면 게이트웨이 서브넷을 만들어야 합니다. [게이트웨이 서브넷에 대한 특정 주소 범위 고려 사항](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub)에서 대해 자세히 알아보세요. 특정 조건에서 서브넷을 만든 후에 주소 범위를 변경할 수 있습니다. 서브넷 주소 범위를 변경하는 방법은 [서브넷 추가, 변경 또는 삭제](virtual-network-manage-subnet.md)에서 [서브넷 설정 변경](#change-subnet)을 참조하세요.
    - **구독**: [구독](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription)을 선택합니다. 둘 이상의 Azure 구독에서 동일한 가상 네트워크를 사용할 수 없습니다. 그러나 단일 구독의 가상 네트워크를 다른 구독의 가상 네트워크에 연결할 수 있습니다. 여러 구독의 가상 네트워크를 연결하려면 Azure VPN Gateway 또는 가상 네트워크 피어링을 사용합니다. 가상 네트워크에 연결하는 모든 Azure 리소스는 가상 네트워크와 동일한 구독에 있어야 합니다.
    - **리소스 그룹**: 기존 [리소스 그룹](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-groups)을 선택하거나 새 리소스 그룹을 만듭니다. 가상 네트워크에 연결하는 Azure 리소스는 가상 네트워크와 동일한 리소스 그룹 또는 다른 리소스 그룹에 있을 수 있습니다.
    - **위치:** 지역이라고도 하는 Azure [위치](https://azure.microsoft.com/regions/)를 선택합니다. 가상 네트워크는 하나의 Azure 위치에만 있을 수 있습니다. 그러나 VPN Gateway를 사용하여 서로 다른 위치에 있는 가상 네트워크를 연결할 수 있습니다. 가상 네트워크에 연결하는 모든 Azure 리소스는 가상 네트워크와 동일한 위치에 있어야 합니다.

**명령**

|도구|명령|
|---|---|
|Azure CLI|[az network vnet create](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|PowerShell|[새-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name = "view-vnet"></a>가상 네트워크 및 설정 보기

가상 네트워크 및 설정을 보려면

1. 구독에 대한 네트워크 참가자 역할에 권한(최소)이 할당된 계정으로 [포털](https://portal.azure.com)에 로그인합니다. 계정에 역할 및 권한을 할당하는 방법에 대한 자세한 내용은 [Azure 역할 기반 액세스 제어의 기본 제공 역할](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)을 참조하세요.
2. 포털 검색 상자에 **가상 네트워크**를 입력합니다. 검색 결과에서 **가상 네트워크**를 클릭합니다.
3. **가상 네트워크** 블레이드에서 설정을 확인할 가상 네트워크를 클릭합니다.
4. 선택한 가상 네트워크에 대한 블레이드에 다음 설정이 나열됩니다.
    - **개요**: 주소 공간 및 DNS 서버와 같은 가상 네트워크에 대한 정보를 제공합니다. 다음 스크린샷에서는 **MyVNet**이라는 가상 네트워크에 대한 개요 설정을 보여 줍니다.

        ![네트워크 인터페이스 개요](./media/virtual-network-manage-network/vnet-overview.png)

      **개요** 블레이드에서 가상 네트워크를 다른 구독 또는 리소스 그룹으로 이동할 수 있습니다. 가상 네트워크를 이동하는 방법은 [다른 리소스 그룹 또는 구독으로 리소스 이동](../azure-resource-manager/resource-group-move-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)을 참조하세요. 필수 조건과 Azure Portal, PowerShell 및 Azure CLI를 사용하여 리소스를 이동하는 방법을 나열하고 있습니다. 가상 네트워크에 연결된 모든 리소스는 가상 네트워크와 함께 이동해야 합니다.
    - **주소 공간**: 가상 네트워크에 할당된 주소 공간이 나열됩니다. 주소 공간을 추가 및 제거하는 방법을 알아보려면 이 문서의 [주소 공간 추가 또는 제거](#address-spaces)에서 설명하는 단계를 완료합니다.
    - **연결된 장치**: 가상 네트워크에 연결된 모든 리소스가 나열됩니다. 이전 스크린샷에는 세 개의 네트워크 인터페이스와 하나의 부하 분산 장치가 가상 네트워크에 연결되어 있습니다. 만들고 가상 네트워크에 연결한 새 리소스가 모두 나열됩니다. 가상 네트워크에 연결된 리소스를 삭제하면 더 이상 목록에 표시되지 않습니다.
    - **서브넷:** 가상 네트워크 내에 있는 서브넷 목록이 표시됩니다. 서브넷을 추가 및 제거하는 방법은 [서브넷 추가, 변경 또는 삭제](virtual-network-manage-subnet.md)에서 [서브넷 만들기](virtual-network-manage-subnet.md#create-subnet) 및 [서브넷 삭제](virtual-network-manage-subnet.md#delete-subnet)를 참조하세요.
    - **DNS 서버**: Azure 내부 DNS 서버 또는 사용자 지정 DNS 서버에서 가상 네트워크에 연결된 장치에 대한 이름 확인을 제공하는지 여부를 지정할 수 있습니다. Azure Portal을 사용하여 가상 네트워크를 만들 때 기본적으로 Azure의 DNS 서버가 가상 네트워크 내에서 이름을 확인하는 데 사용됩니다. DNS 서버를 수정하려면 이 문서의 [DNS 서버 추가/변경/제거](#dns-servers)에서 설명하는 단계를 완료합니다.
    - **피어링**: 구독에 기존 피어링이 있는 경우 여기에 나열됩니다. 기존 피어링에 대한 설정을 보거나, 피어링을 만들거나 변경하거나 삭제할 수 있습니다. 피어링에 대한 자세히 내용은 [가상 네트워크 피어링](virtual-network-peering-overview.md)을 참조하세요.
    - **속성:** 가상 네트워크의 리소스 ID 및 해당 Azure 구독을 포함하여 가상 네트워크에 대한 설정을 표시합니다.
    - **다이어그램:** 가상 네트워크에 연결된 모든 장치를 시각적으로 보여 줍니다. 다이어그램에는 장치에 대한 몇 가지 중요한 정보가 있습니다. 이 보기에서 장치를 관리하려면 다이어그램에서 장치를 클릭합니다.
    - **일반적인 Azure 설정**: 일반적인 Azure 설정에 대한 자세한 내용은 다음 정보를 참조하세요.
        *   [활동 로그](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#activity-logs)
        *   [액세스 제어(IAM)](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#access-control)
        *   [태그](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#tags)
        *   [잠금](../azure-resource-manager/resource-group-lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
        *   [자동화 스크립트](../azure-resource-manager/resource-manager-export-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json#export-the-template-from-resource-group)

**명령**

|도구|명령|
|---|---|
|Azure CLI|[az network vnet show](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#show)|
|PowerShell|[Get-AzureRmVirtualNetwork](/powershell/resourcemanager/azurerm.network/v3.8.0/get-azurermvirtualnetwork/?toc=%2fazure%2fvirtual-network%2ftoc.json)|


## <a name="add-address-spaces"></a>주소 공간 추가 또는 제거

가상 네트워크에 대한 주소 공간을 추가 및 제거할 수 있습니다. 주소 공간은 CIDR 표기법으로 지정해야 하며, 동일한 가상 네트워크 내에서 다른 주소 공간과 겹칠 수 없습니다. 정의하는 주소 공간은 공용 또는 개인 주소가 될 수 있습니다(RFC 1918). 주소 공간을 공용으로 정의하든 개인으로 정의하든 가상 네트워크 내, 상호 연결된 가상 네트워크 및 가상 네트워크에 연결된 모든 온-프레미스 네트워크에서만 주소 공간에 연결할 수 있습니다. 다음 주소 공간은 추가할 수 없습니다.

- 224.0.0.0/4(멀티캐스트)
- 255.255.255.255/32(브로드캐스트)
- 127.0.0.0/8(루프백)
- 169.254.0.0/16(링크-로컬)
- 168.63.129.16/32(내부 DNS)

주소 공간을 추가하거나 제거하려면

1. 구독에 대한 네트워크 참가자 역할에 권한(최소)이 할당된 계정으로 [포털](https://portal.azure.com)에 로그인합니다. 계정에 역할 및 권한을 할당하는 방법에 대한 자세한 내용은 [Azure 역할 기반 액세스 제어의 기본 제공 역할](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)을 참조하세요.
2. 포털 검색 상자에 **가상 네트워크**를 입력합니다. 검색 결과에서 **가상 네트워크**를 선택합니다.
3. **가상 네트워크** 블레이드에서 주소 공간을 추가하거나 제거할 가상 네트워크를 클릭합니다.
4. 가상 네트워크 블레이드의 **설정**에서 **주소 공간**을 클릭합니다.
5. 주소 공간에 대한 블레이드에서 다음 옵션 중 하나를 완료합니다.
    - **주소 공간 추가:** 새 주소 공간을 입력합니다. 주소 공간은 가상 네트워크에 대해 정의된 기존 주소 공간과 겹칠 수 없습니다.
    - **주소 공간 제거:** 주소 공간을 마우스 오른쪽 단추로 클릭한 다음 **제거**를 클릭합니다. 주소 공간에 서브넷이 있으면 해당 주소 공간을 제거할 수 없습니다. 주소 공간을 제거하려면 먼저 주소 공간에 있는 모든 서브넷(및 서브넷에 연결된 모든 리소스)을 삭제해야 합니다.
6. **Save**를 클릭합니다.

**명령**

|도구|명령|
|---|---|
|Azure CLI|리소스 관리자에만 해당|[az network vnet update](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|PowerShell|[Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork?view=azurermps-3.8.0?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="dns-servers"></a>DNS 서버 추가, 변경 또는 제거

가상 네트워크에 연결된 모든 VM은 가상 네트워크에 대해 지정한 DNS 서버에 등록됩니다. 또한 지정된 DNS 서버를 이름 확인에 사용합니다. VM의 각 NIC(네트워크 인터페이스)에는 자체의 DNS 서버 설정이 있을 수 있습니다. NIC에 자체의 DNS 서버 설정이 있는 경우 가상 네트워크에 대한 DNS 서버 설정이 이 설정으로 재정의됩니다. NIC DNS 설정에 대해 자세히 알아보려면 [네트워크 인터페이스 작업 및 설정](virtual-network-network-interface.md#change-dns-servers)을 참조하세요. VM 및 Azure Cloud Services 역할 인스턴스의 이름 확인에 대해 자세히 알아보려면 [VM 및 역할 인스턴스에 대한 이름 확인](virtual-networks-name-resolution-for-vms-and-role-instances.md)을 참조하세요. DNS 서버를 추가, 변경 또는 제거하려면

1. 구독에 대한 네트워크 참가자 역할에 권한(최소)이 할당된 계정으로 [포털](https://portal.azure.com)에 로그인합니다. 계정에 역할 및 권한을 할당하는 방법에 대한 자세한 내용은 [Azure 역할 기반 액세스 제어의 기본 제공 역할](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)을 참조하세요.
2. 포털 검색 상자에 **가상 네트워크**를 입력합니다. 검색 결과에서 **가상 네트워크**를 선택합니다.
3. **가상 네트워크** 블레이드에서 DNS 설정을 변경하려는 가상 네트워크를 클릭합니다.
4. 가상 네트워크 블레이드의 **설정**에서 **DNS 서버**를 클릭합니다.
5. DNS 서버가 나열된 블레이드에서 다음 옵션 중 하나를 선택합니다.
    - **기본값(Azure에서 제공):** 모든 리소스 이름 및 개인 IP 주소가 Azure DNS 서버에 자동으로 등록됩니다. 동일한 가상 네트워크에 연결된 모든 리소스 간에 이름을 확인할 수 있습니다. 이 옵션을 사용하면 가상 네트워크에서 이름을 확인할 수 없습니다. 가상 네트워크 전체에서 이름을 확인하려면 사용자 지정 DNS 서버를 사용해야 합니다.
    - **사용자 지정:** 가상 네트워크에 대한 Azure 제한까지 하나 이상의 서버를 추가할 수 있습니다. DNS 서버 제한에 대해 자세히 알아보려면 [Azure 제한](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual-networking-limits-classic)을 참조하세요. 다음 옵션이 있습니다.
        - **주소 추가:** 가상 네트워크 DNS 서버 목록에 서버를 추가하고, 해당 DNS 서버를 Azure에 등록합니다. Azure에 DNS 서버를 이미 등록한 경우 목록에서 DNS 서버를 선택할 수 있습니다.
        - **주소 제거:** 제거하려는 서버 옆에 있는 **X**를 클릭합니다. 서버를 삭제하면 이 가상 네트워크 목록에서만 서버가 제거됩니다. DNS 서버는 다른 가상 네트워크에서 사용할 수 있도록 Azure에 등록된 상태로 유지됩니다.
        - **DNS 서버 주소 다시 정렬**: 사용자 환경에 맞게 올바른 순서로 DNS 서버를 나열하는지 확인하는 것이 중요합니다. DNS 서버 목록은 지정된 순서로 사용됩니다. 라운드 로빈 설정으로 작동하지 않습니다. 목록의 첫 번째 DNS 서버에 연결할 수 있으면 DNS 서버가 제대로 작동하는지 여부와 관계없이 클라이언트에서 해당 DNS 서버를 사용합니다. 나열된 모든 DNS 서버를 제거한 다음 원하는 순서로 다시 추가합니다.
        - **주소 변경**: 목록에서 DNS 서버를 강조 표시한 다음 새 이름을 입력합니다.
6. **Save**를 클릭합니다.
7. 가상 네트워크에 연결된 VM을 다시 시작하여 새 DNS 서버 설정을 할당합니다. VM이 다시 시작될 때까지 현재 DNS 설정을 계속 사용합니다.

**명령**

|도구|명령|
|---|---|
|Azure CLI|[az network vnet update](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|PowerShell|[Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork?view=azurermps-3.8.0?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="delete-vnet"></a>가상 네트워크 삭제

가상 네트워크에 연결된 리소스가 없는 경우에만 해당 VNet을 삭제할 수 있습니다. 가상 네트워크 내의 서브넷에 연결된 리소스가 있는 경우 먼저 가상 네트워크 내의 모든 서브넷에 연결된 리소스를 삭제해야 합니다. 리소스 삭제를 수행하는 단계는 리소스에 따라 다릅니다. 서브넷에 연결된 리소스를 삭제하는 방법에 대해 알아보려면 삭제하려는 각 리소스 종류에 대한 설명서를 참조하세요. 가상 네트워크를 삭제하려면

1. 구독에 대한 네트워크 참가자 역할에 권한(최소)이 할당된 계정으로 [포털](https://portal.azure.com)에 로그인합니다. 계정에 역할 및 권한을 할당하는 방법에 대한 자세한 내용은 [Azure 역할 기반 액세스 제어의 기본 제공 역할](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)을 참조하세요.
2. 포털 검색 상자에 **가상 네트워크**를 입력합니다. 검색 결과에서 **가상 네트워크**를 클릭합니다.
3. **가상 네트워크** 블레이드에서 삭제할 가상 네트워크를 선택합니다.
4. 가상 네트워크 블레이드에서 가상 네트워크에 연결된 장치가 없는지 확인하려면 **설정**에서 **연결된 장치**를 클릭합니다. 연결된 장치가 있으면 가상 네트워크를 삭제하기 전에 먼저 이러한 장치를 삭제해야 합니다. 연결된 장치가 없으면 **개요**를 클릭합니다.
5. 블레이드 위쪽의 **삭제** 아이콘을 클릭합니다.
6. 가상 네트워크 삭제를 확인하려면 **예**를 클릭합니다.


**명령**

|도구|명령|
|---|---|
|Azure CLI|[azure network vnet delete](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#delete)|
|PowerShell|[Remove-AzureRmVirtualNetwork](/powershell/module/azurerm.network/remove-azurermvirtualnetwork?view=azurermps-3.8.0?toc=%2fazure%2fvirtual-network%2ftoc.json)|


## <a name="next-steps"></a>다음 단계

- VM을 만들어 가상 네트워크에 연결하려면 [가상 네트워크 만들기 및 VM 연결](virtual-network-get-started-vnet-subnet.md#create-vms)을 참조하세요.
- 가상 네트워크 내의 서브넷 간 네트워크 트래픽을 필터링하려면 [네트워크 보안 그룹 만들기](virtual-networks-create-nsg-arm-pportal.md)를 참조하세요.
- 가상 네트워크를 다른 가상 네트워크에 피어링하려면 [가상 네트워크 피어링 만들기](virtual-network-create-peering.md#portal)를 참조하세요.
- 가상 네트워크를 온-프레미스 네트워크에 연결하는 옵션에 대해 알아보려면 [VPN Gateway 정보](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#diagrams)를 참조하세요.

