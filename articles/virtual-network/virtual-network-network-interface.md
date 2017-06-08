---
title: "Azure 네트워크 인터페이스 만들기, 변경 또는 삭제 | Microsoft Docs"
description: "NIC(네트워크 인터페이스)의 정의, NIC를 만들고 삭제하는 방법 및 해당 설정을 변경하는 방법에 대해 알아봅니다."
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
ms.date: 05/04/2017
ms.author: jdial
ms.translationtype: Human Translation
ms.sourcegitcommit: afa23b1395b8275e72048bd47fffcf38f9dcd334
ms.openlocfilehash: f1fb0f6348b579121be64bff4411952026f8528d
ms.contentlocale: ko-kr
ms.lasthandoff: 05/13/2017


---

# <a name="create-change-or-delete-network-interfaces"></a>네트워크 인터페이스 만들기, 변경 또는 삭제

NIC(네트워크 인터페이스)를 만들고 삭제하는 방법 및 해당 설정을 변경하는 방법에 대해 알아봅니다. NIC를 사용하면 Azure VM(가상 컴퓨터)에서 인터넷, Azure 및 온-프레미스 리소스와 통신할 수 있습니다. Azure Portal을 사용하여 VM을 만들 때 포털에서는 사용자에 대한 기본 설정이 포함된 하나의 NIC를 만듭니다. 대신 사용자 지정 설정이 포함된 NIC를 만들고 VM을 만들 때 해당 VM에 하나 이상을 추가하도록 선택할 수 있습니다. 또한 기존 NIC의 기본 NIC 설정을 변경할 수도 있습니다. 이 문서에서는 사용자 지정 설정이 포함된 NIC를 만들고, 기존 NIC 설정(예: 네트워크 필터 할당(네트워크 보안 그룹), 서브넷 할당, DNS 서버 설정 및 IP 전달)을 변경하며, NIC를 삭제하는 방법에 대해 설명합니다. 

NIC에 대한 IP 주소를 추가, 변경 또는 제거해야 하는 경우 [IP 주소 추가, 변경 또는 제거](virtual-network-network-interface-addresses.md) 문서를 참조하세요. VM에서 NIC를 추가하거나 제거해야 하는 경우 [NIC 추가 또는 제거](virtual-network-network-interface-vm.md) 문서를 참조하세요. 


## <a name="before"></a>시작하기 전에

이 문서에서 설명하는 섹션의 모든 단계를 수행하기 전에 다음 작업을 완료해야 합니다.

- [Azure 제한](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) 문서를 검토하여 NIC에 대한 제한 사항을 알아봅니다.
- Azure 계정이 있는 Azure Portal, Azure CLI(명령줄 인터페이스) 또는 Azure PowerShell에 로그인합니다. 아직 Azure 계정이 없으면 [체험 계정](https://azure.microsoft.com/free)에 등록합니다.
- PowerShell 명령을 사용하여 이 문서의 작업을 수행하는 경우 [Azure PowerShell 설치 및 구성 방법](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json) 문서의 단계를 완료하여 Azure PowerShell을 설치하고 구성합니다. 최신 버전의 Azure PowerShell cmdlet을 설치했는지 확인합니다. 예제와 함께 PowerShell 명령에 대한 도움말을 보려면 `get-help <command> -full`을 입력합니다.
- Azure CLI(명령줄 인터페이스) 명령을 사용하여 이 문서의 작업을 수행하는 경우 [Azure CLI 설치 및 구성 방법](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json) 문서의 단계를 완료하여 Azure CLI를 설치하고 구성합니다. 최신 버전의 Azure CLI를 설치했는지 확인합니다. CLI 명령에 대한 도움말을 보려면 `az <command> --help`를 입력합니다.

## <a name="create-nic"></a>NIC 만들기
Azure Portal을 사용하여 VM을 만들 때 포털에서는 사용자에 대한 기본 설정이 포함된 하나의 NIC를 만듭니다. 대신 모든 NIC 설정을 지정하려면 사용자 지정 설정이 포함된 NIC를 만들고 VM을 만들 때 이 NIC를 해당 VM에 연결할 수 있습니다. 또한 NIC를 만들어 기존 VM에 추가할 수도 있습니다. 기존 NIC가 있는 VM을 만들거나 기존 VM에서 NIC를 추가하거나 제거하려면 [NIC 추가 또는 제거](virtual-network-network-interface-vm.md) 문서를 참조하세요. NIC를 만들기 전에 NIC를 만든 동일한 위치 및 구독에 기존 VNet(가상 네트워크)가 있어야 합니다. VNet을 만드는 방법에 대한 내용은 [VNet 만들기](virtual-networks-create-vnet-arm-pportal.md) 문서를 참조하세요.

1. 구독에 대한 네트워크 참가자 역할에 권한(최소)이 할당된 계정으로 [Azure Portal](https://portal.azure.com)에 로그인합니다. 계정에 역할 및 권한을 할당하는 방법에 대한 자세한 내용은 [Azure 역할 기반 액세스 제어의 기본 제공 역할](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) 문서를 참조하세요.
2. Azure Portal 위쪽의 *리소스 검색* 텍스트가 있는 상자에서 *네트워크 인터페이스*를 입력합니다. 검색 결과에 표시된 **네트워크 인터페이스**를 클릭합니다.
3. **네트워크 인터페이스** 블레이드가 표시되면 **+ 추가**를 클릭합니다.
4. 표시되는 **네트워크 인터페이스 만들기** 블레이드에서 다음 설정에 대한 값을 입력하거나 선택한 다음 **만들기**를 클릭합니다.

    |설정|Required?|세부 정보|
    |---|---|---|
    |이름|예|이름은 선택한 리소스 그룹 내에서 고유해야 합니다. 시간이 지남에 따라 Azure 구독에는 여러 개의 NIC가 있을 것입니다. 여러 NIC를 더 쉽게 관리할 수 있도록 명명 규칙을 만들 때 제안 사항에 대한 [명명 규칙](/azure/architecture/best-practices/naming-conventions?toc=%2fazure%2fvirtual-network%2ftoc.json#naming-rules-and-restrictions) 문서를 참조하세요. NIC를 만든 후에는 이름을 변경할 수 없습니다.|
    |가상 네트워크|예|NIC를 연결할 VNet을 선택합니다. NIC와 동일한 구독과 위치에 있는 VNet에만 해당 NIC를 연결할 수 있습니다. NIC가 만들어지면 연결된 VNet을 변경할 수 없습니다. 또한 NIC를 추가하는 VM은 NIC와 동일한 위치 및 구독에 있어야 합니다.|
    |서브넷|예|선택한 VNet에 있는 서브넷을 선택합니다. NIC를 만든 후에는 연결된 서브넷을 변경할 수 있습니다.|
    |개인 IP 주소 할당|예| 다음 할당 방법 중에서 선택합니다. **동적:** 이 옵션을 선택하면 선택한 서브넷의 주소 공간에서 사용 가능한 주소를 Azure에서 자동으로 할당합니다. VM이 중지됨(할당 취소됨) 상태에서 시작될 때 Azure에서 다른 주소를 NIC에 할당할 수 있습니다. VM을 중지(할당 취소)되지 않은 상태에서 시작하는 경우에는 주소가 동일하게 유지됩니다. **고정:** 이 옵션을 선택하면 선택한 서브넷의 주소 공간에서 사용 가능한 IP 주소를 수동으로 할당해야 합니다. 고정 주소는 변경하거나 NIC를 삭제할 때까지 변경되지 않습니다. NIC를 만든 후에는 할당 방법을 변경할 수 있습니다. Azure DHCP 서버는 이 주소를 VM 운영 체제 내의 NIC에 할당합니다.|
    |네트워크 보안 그룹|아니요| **없음**으로 설정된 상태로 두거나 기존 NSG(네트워크 보안 그룹)을 선택하거나 NSG를 만듭니다. NSG를 사용하면 NIC 내부 및 외부 네트워크 트래픽을 필터링할 수 있습니다. NSG에 대한 자세한 내용은 [네트워크 보안 그룹](virtual-networks-nsg.md)을 참조하세요. NSG를 만들려면 [NSG 만들기](virtual-networks-create-nsg-arm-pportal.md) 문서를 참조하세요. NIC에 0-1개의 NSG를 적용할 수 있습니다. 또한 NIC가 연결된 서브넷에도 마찬가지입니다. NIC 및 해당 NIC가 연결된 서브넷에 NSG를 적용할 때 때로는 예기치 않은 결과가 발생할 수 있습니다. NIC 및 서브넷에 적용되는 NSG 문제를 해결하려면 [NSG 문제 해결](virtual-network-nsg-troubleshoot-portal.md#view-effective-security-rules-for-a-network-interface) 문서를 참조하세요.|
    |구독|예|Azure [구독](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) 중 하나를 선택합니다. NIC를 연결하는 VM과 VNet은 동일한 구독에 있어야 합니다.|
    |리소스 그룹|예|기존 [리소스 그룹](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group)을 선택하거나 리소스 그룹을 만듭니다. NIC는 연결한 VM 또는 연결된 VNet과 동일하거나 다른 리소스 그룹에 있을 수 있습니다.|
    |위치|예|NIC를 연결하는 VM과 VNet을 연결하는 VM은 동일한 [위치](https://azure.microsoft.com/regions)에 있어야 하며, 이 위치를 지역이라고도 합니다.|

포털에서는 NIC를 만들 때 해당 NIC에 공용 IP 주소를 할당하는 옵션을 제공하지는 않지만, 포털을 사용하여 VM을 만들 때 NIC에 공용 IP 주소를 할당합니다. NIC를 만든 후 공용 IP 주소를 추가하는 방법에 대한 내용은 [IP 주소 추가, 변경 또는 제거](virtual-network-network-interface-addresses.md) 문서를 참조하세요. 공용 IP 주소가 있는 NIC를 만들려면 CLI 또는 PowerShell을 사용하여 NIC를 만들어야 합니다.

>[!Note]
> NIC를 VM에 연결하고 VM을 처음 시작한 후에만 Azure에서 NIC에 MAC 주소를 할당합니다. Azure에서 NIC에 할당한 MAC 주소는 지정할 수 없습니다. NIC를 삭제하거나 기본 NIC의 기본 IP 구성에 할당된 개인 IP 주소를 변경할 때까지 MAC 주소는 NIC에 할당된 상태로 유지됩니다. IP 주소 및 IP 구성에 대한 자세한 내용은 [IP 주소 추가, 변경 또는 제거](virtual-network-network-interface-addresses.md) 문서를 참조하세요.

**명령**

|도구|명령|
|---|---|
|CLI|[az network nic create](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|PowerShell|[새-AzureRmNetworkInterface](/powershell/resourcemanager/azurerm.network/v3.4.0/new-azurermnetworkinterface/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|

## <a name="view-nics"></a>NIC 설정 보기

NIC에 대한 대부분의 설정을 보고 변경할 수 있습니다.

1. 구독에 대한 네트워크 참가자 역할에 권한(최소)이 할당된 계정으로 [Azure Portal](https://portal.azure.com)에 로그인합니다. 계정에 역할 및 권한을 할당하는 방법에 대한 자세한 내용은 [Azure 역할 기반 액세스 제어의 기본 제공 역할](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) 문서를 참조하세요.
2. Azure Portal 위쪽의 *리소스 검색* 텍스트가 있는 상자에서 *네트워크 인터페이스*를 입력합니다. 검색 결과에 표시된 **네트워크 인터페이스**를 클릭합니다.
3. 표시되는 **네트워크 인터페이스** 블레이드에서 설정을 보거나 변경할 NIC를 클릭합니다.
4. 선택한 NIC에 대해 표시되는 블레이드에서 다음 설정이 나열됩니다.
    - **개요:** NIC에 할당된 IP 주소, NIC가 연결된 VNet/서브넷 및 NIC가 연결된 VM(연결되어 있는 경우)과 같은 NIC 관련 정보를 제공합니다. 다음 그림에서는 **mywebserver256**이라는 NIC에 대한 개요 설정을 보여 줍니다.   ![네트워크 인터페이스 개요](./media/virtual-network-network-interface/nic-overview.png) **리소스 그룹** 또는 **구독 이름** 옆에 있는 **변경**을 클릭하여 NIC를 다른 리소스 그룹 또는 구독으로 이동할 수 있습니다. NIC를 이동하는 경우 NIC와 관련된 모든 리소스를 이동해야 합니다. 예를 들어 NIC가 VM에 연결되어 있으면 VM 및 기타 VM 관련 리소스도 이동해야 합니다. NIC를 이동하려면 [새 리소스 그룹 또는 구독으로 리소스이동](../azure-resource-manager/resource-group-move-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json#use-portal) 문서를 참조하세요. 필수 조건과 Azure Portal, PowerShell 및 Azure CLI를 사용하여 리소스를 이동하는 방법을 나열하고 있습니다.
    - **IP 구성:** 공용 및 개인 IP 주소는 NIC에 대한 하나 이상의 IP 구성에 할당됩니다. NIC에 지원되는 최대 IP 구성 수에 대한 자세한 내용은 [Azure 제한](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) 문서를 참조하세요. 각 IP 구성에는 하나의 할당된 개인 IP 주소가 있으며, 하나의 공용 IP 주소도 있을 수 있습니다. NIC에서 IP 구성을 추가, 변경 또는 삭제하려면 [IP 주소 추가, 변경 또는 제거](virtual-network-network-interface-addresses.md) 문서의 [NIC에 보조 IP 구성 추가](virtual-network-network-interface-addresses.md#create-ip-config), [IP 구성 변경](virtual-network-network-interface-addresses.md#change-ip-config) 또는 [IP 구성 삭제](virtual-network-network-interface-addresses.md#delete-ip-config) 섹션의 단계를 완료합니다. 또한 IP 전달 및 서브넷 할당도 이 섹션에서 구성합니다. 이러한 설정에 대한 자세한 내용은 이 문서의 [IP 전달 사용/사용 안 함](#ip-forwarding) 및 [서브넷 할당 변경](#subnet)섹션을 참조하세요.
    - **DNS 서버:** Azure DHCP 서버에서 NIC를 할당하는 DNS 서버를 지정할 수 있습니다. NIC는 해당 NIC가 연결된 VNet에서 설정을 상속하거나 연결된 VNet에 대한 설정을 재정의하는 사용자 지정 설정을 가질 수 있습니다. 표시되는 항목을 수정하려면 이 문서의 [DNS 서버 변경](#dns) 섹션에서 설명하는 단계를 수행합니다.
    - **NSG(네트워크 보안 그룹):** NIC에 연결된 NSG를 표시합니다(있는 경우). NSG에는 인바운드 및 아웃바운드 규칙이 포함되어 NIC의 네트워크 트래픽을 필터링합니다. NSG가 NIC에 연결되어 있으면 연결된 NSG의 이름이 표시됩니다. 표시된 항목을 수정하려면 이 문서에 있는 [NSG와 네트워크 인터페이스 연결 또는 분리](#associate-nsg) 섹션의 단계를 수행합니다.
    - **속성:** NIC의 MAC 주소(NIC가 VM에 연결되어 있지 않으면 비어 있음)와 해당 NIC가 있는 구독에 대한 키 설정을 표시합니다.
    - **효과적인 보안 규칙:** NIC가 실행 중인 VM에 연결되어 있고 NSG가 NIC, 연결된 서브넷 또는 둘 다에 연결되어 있으면 보안 규칙이 나열됩니다. 표시되는 항목에 대한 자세한 내용은 [네트워크 보안 그룹 문제 해결](virtual-network-nsg-troubleshoot-portal.md#view-effective-security-rules-for-a-network-interface) 문서를 참조하세요. NSG에 대한 자세한 내용은 [네트워크 보안 그룹](virtual-networks-nsg.md)을 참조하세요.
    - **유효 경로:** NIC가 실행 중인 VM에 연결되어 있으면 경로가 나열됩니다. 경로는 Azure 기본 경로, UDR(사용자 정의 경로) 및 NIC가 연결된 서브넷에 있을 수 있는 BGP 경로의 조합입니다. 표시되는 항목에 대한 자세한 내용은 [경로 문제 해결](virtual-network-routes-troubleshoot-portal.md#view-effective-routes-for-a-network-interface) 문서를 참조하세요. Azure 기본 경로 및 UDR에 대한 자세한 내용은 [사용자 정의 경로](virtual-networks-udr-overview.md) 문서를 참조하세요.
    - **일반적인 Azure Resource Manager 설정:** 일반적인 Azure Resource Manager 설정에 대한 자세한 내용은 [활동 로그](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#activity-logs), [액세스 제어(IAM)](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#access-control), [태그](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#tags), [잠금](../azure-resource-manager/resource-group-lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 및 [자동화 스크립트](../azure-resource-manager/resource-manager-export-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json#export-the-template-from-resource-group) 문서를 참조하세요.

**명령**

|도구|명령|
|---|---|
|CLI|[az network nic list](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#list)는 구독에 있는 NIC를 보여 주고, [az network nic show](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#show)는 NIC 설정을 보여 줍니다.|
|PowerShell|[Get-AzureRmNetworkInterface](/powershell/resourcemanager/azurerm.network/v3.4.0/get-azurermnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)는 NIC에 대한 구독 또는 설정을 보여 줍니다.|

## <a name="dns"></a>DNS 서버 변경

DNS 서버는 Azure DHCP 서버에서 VM 운영 체제 내의 NIC에 할당됩니다. 할당된 DNS 서버는 NIC에 대한 DNS 서버 설정입니다. NIC에 대한 이름 확인 설정에 대한 자세한 내용은 [VM에 대한 이름 확인](virtual-networks-name-resolution-for-vms-and-role-instances.md) 문서를 참조하세요. NIC는 VNet에서 설정을 상속하거나 VNet에 대한 설정을 재정의하는 고유한 설정을 사용할 수 있습니다.

1. 구독에 대한 네트워크 참가자 역할에 권한(최소)이 할당된 계정으로 [Azure Portal](https://portal.azure.com)에 로그인합니다. 계정에 역할 및 권한을 할당하는 방법에 대한 자세한 내용은 [Azure 역할 기반 액세스 제어의 기본 제공 역할](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) 문서를 참조하세요.
2. Azure Portal 위쪽의 *리소스 검색* 텍스트가 있는 상자에서 *네트워크 인터페이스*를 입력합니다. 검색 결과에 표시된 **네트워크 인터페이스**를 클릭합니다.
3. 표시되는 **네트워크 인터페이스** 블레이드에서 설정을 보거나 변경할 NIC를 클릭합니다.
4. 선택한 NIC에 대한 블레이드의 **설정** 아래에서 **DNS 서버**를 클릭합니다.
5. 다음 중 하나를 클릭합니다:
    - **가상 네트워크에서 상속(기본값)**: 이 옵션을 선택하면 NIC가 연결된 가상 네트워크에 대해 정의된 DNS 서버 설정을 상속합니다. VNet 수준에서 사용자 지정 DNS 서버 또는 Azure 제공 DNS 서버가 정의됩니다. Azure에서 제공한 DNS 서버는 동일한 VNet에 연결된 리소스에 대한 호스트 이름을 확인할 수 있습니다. FQDN을 사용하여 서로 다른 VNet에 연결된 리소스를 확인해야 합니다.
    - **사용자 지정**: 여러 VNet에서 이름을 확인하도록 사용자 고유의 DNS 서버를 구성할 수 있습니다. DNS 서버로 사용하려는 서버의 IP 주소를 입력합니다. 지정한 DNS 서버 주소는 이 NIC에만 할당되며, 해당 NIC가 연결된 VNet에 대한 모든 DNS 설정을 재정의합니다.
6. **Save**를 클릭합니다.

**명령**

|도구|명령|
|---|---|
|CLI|[az network nic update](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|PowerShell|[Set-AzureRmNetworkInterface](/powershell/resourcemanager/azurerm.network/v3.4.0/set-azurermnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="ip-forwarding"></a>IP 전달 사용/사용 안 함

IP 전달은 NIC가 연결된 VM을 활성화합니다.
- NIC에 할당된 IP 구성 중 하나에 할당된 IP 주소 중 하나를 대상으로 하지 않는 네트워크 트래픽을 받습니다.
- NIC의 IP 구성 중 하나에 할당된 것과 다른 원본 IP 주소로 네트워크 트래픽을 보냅니다.

VM이 전달해야 하는 트래픽을 받는 VM에 연결된 모든 NIC에서 이 설정이 활성화되어야 합니다. NIC가 여러 개 또는 하나 중 어떻게 연결되어 있든 VM은 트래픽을 전달할 수 있습니다. IP 전달은 Azure 설정이지만, VM은 방화벽, WAN 최적화 및 부하 분산 응용 프로그램과 같이 트래픽을 전달할 수 있는 응용 프로그램도 실행해야 합니다. VM에서 네트워크 응용 프로그램을 실행하는 경우 이 VM을 종종 NVA(네트워크 가상 어플라이언스)라고 합니다. [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances)에서 NVA를 배포할 준비가 된 목록을 볼 수 있습니다. IP 전달은 일반적으로 사용자 정의 경로와 함께 사용됩니다. 사용자 정의 경로에 대해 자세히 알아보려면 [사용자 정의 경로](virtual-networks-udr-overview.md) 문서를 참조하세요.

1. 구독에 대한 네트워크 참가자 역할에 권한(최소)이 할당된 계정으로 [Azure Portal](https://portal.azure.com)에 로그인합니다. 계정에 역할 및 권한을 할당하는 방법에 대한 자세한 내용은 [Azure 역할 기반 액세스 제어의 기본 제공 역할](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) 문서를 참조하세요.
2. Azure Portal 위쪽의 *리소스 검색* 텍스트가 있는 상자에서 *네트워크 인터페이스*를 입력합니다. 검색 결과에 표시된 **네트워크 인터페이스**를 클릭합니다.
3. 표시되는 **네트워크 인터페이스** 블레이드에서 IP 전달을 사용하거나 사용하지 않도록 설정하려는 NIC를 클릭합니다.
4. 선택한 NIC에 대한 블레이드의 **설정** 섹션에서 **IP 구성**을 클릭합니다.
5. **사용** 또는 **사용 안 함**(기본 설정)을 클릭하여 설정을 변경합니다.
6. **Save**를 클릭합니다.

**명령**

|도구|명령|
|---|---|
|CLI|[az network nic update](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|PowerShell|[Set-AzureRmNetworkInterface](/powershell/resourcemanager/azurerm.network/v3.4.0/set-azurermnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="subnet"></a>서브넷 할당 변경

NIC가 연결되고 Vnet이 아닌 서브넷을 변경할 수 있습니다.

1. 구독에 대한 네트워크 참가자 역할에 권한(최소)이 할당된 계정으로 [Azure Portal](https://portal.azure.com)에 로그인합니다. 계정에 역할 및 권한을 할당하는 방법에 대한 자세한 내용은 [Azure 역할 기반 액세스 제어의 기본 제공 역할](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) 문서를 참조하세요.
2. Azure Portal 위쪽의 *리소스 검색* 텍스트가 있는 상자에서 *네트워크 인터페이스*를 입력합니다. 검색 결과에 표시된 **네트워크 인터페이스**를 클릭합니다.
3. 표시되는 **네트워크 인터페이스** 블레이드에서 설정을 보거나 변경할 NIC를 클릭합니다.
4. 선택한 NIC에 대한 블레이드의 **설정** 아래에서 **IP 구성**을 클릭합니다. 나열된 모든 IP 구성의 개인 IP 주소 옆에 **(고정)**이 있으면 다음 단계를 수행하여 IP 주소 할당 방법을 동적으로 변경해야 합니다. NIC에 대한 서브넷 할당을 변경하려면 모든 개인 IP 주소에 동적 할당 방법을 할당해야 합니다. 주소가 동적 방법으로 할당되면 5단계로 진행합니다. 고정 할당 방법으로 할당된 주소가 있으면 다음 단계를 수행하여 할당 방법을 동적으로 변경합니다.
    - IP 구성 목록에서 IP 주소 할당 방법을 변경하려는 IP 구성을 클릭합니다.
    - IP 구성에 대해 표시되는 블레이드에서 **할당** 방법으로 **동적**을 클릭합니다.
    - **Save**를 클릭합니다.
5. **서브넷** 드롭다운 목록에서 NIC를 연결하려는 서브넷을 선택합니다.
6. **Save**를 클릭합니다. 새 동적 주소는 새 서브넷의 서브넷 주소 범위에서 할당됩니다. NIC를 새 서브넷에 할당한 후에 선택하는 경우 새 서브넷 주소 범위에서 고정 IP 주소를 할당할 수 있습니다. NIC에 대한 IP 주소의 추가, 변경 및 제거에 대한 자세한 내용은 [IP 주소 추가, 변경 또는 제거](virtual-network-network-interface-addresses.md) 문서를 참조하세요.

**명령**

|도구|명령|
|---|---|
|CLI|[az network nic ip-config update](/cli/azure/network/nic/ip-config?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|PowerShell|[Set-AzureRmNetworkInterfaceIpConfig](/powershell/resourcemanager/azurerm.network/v3.4.0/set-azurermnetworkinterfaceipconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|


## <a name="delete-nic"></a>NIC 삭제

NIC가 VM에 연결되어 있지 않으면 삭제할 수 있습니다. VM에 연결되어 있으면 먼저 VM을 중지됨(할당 취소됨) 상태로 전환한 다음 해당 VM에서 NIC를 분리해야 해당 NIC를 삭제할 수 있습니다. VM에서 NIC를 분리하려면 [네트워크 인터페이스 추가 또는 제거](virtual-network-network-interface-vm.md) 문서의 [가상 컴퓨터에서 NIC 분리](virtual-network-network-interface-vm.md#vm-remove-nic) 섹션의 단계를 완료합니다. VM을 삭제하면 연결된 모든 NIC가 분리되지만 삭제되는 것은 아닙니다.

1. 구독에 대한 네트워크 참가자 역할에 권한(최소)이 할당된 계정으로 [Azure Portal](https://portal.azure.com)에 로그인합니다. 계정에 역할 및 권한을 할당하는 방법에 대한 자세한 내용은 [Azure 역할 기반 액세스 제어의 기본 제공 역할](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) 문서를 참조하세요.
2. Azure Portal 위쪽의 *리소스 검색* 텍스트가 있는 상자에서 *네트워크 인터페이스*를 입력합니다. 검색 결과에 표시된 **네트워크 인터페이스**를 클릭합니다.
3. 삭제하려는 NIC를 마우스 오른쪽 단추로 클릭하고 **삭제**를 클릭합니다.
4. **예**를 클릭하여 NIC 삭제를 확인합니다.

NIC를 삭제하면 할당된 모든 MAC 또는 IP 주소가 해제됩니다.

**명령**

|도구|명령|
|---|---|
|CLI|[az network nic delete](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#delete)|
|PowerShell|[Remove-AzureRmNetworkInterface](/powershell/resourcemanager/azurerm.network/v3.1.0/remove-azurermnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|


## <a name="next-steps"></a>다음 단계
여러 NIC 또는 IP 주소가 있는 VM을 만들려면 다음 문서를 참조하세요.

**명령**

|작업|도구|
|---|---|
|여러 NIC를 사용하여 VM 만들기|[CLI](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
||[PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|여러 IP 주소가 있는 단일 NIC VM 만들기|[CLI](virtual-network-multiple-ip-addresses-cli.md)|
||[PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|

