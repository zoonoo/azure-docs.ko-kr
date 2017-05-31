---
title: "Azure 네트워크 인터페이스에 대한 IP 주소 구성 | Microsoft Docs"
description: "공용 및 개인 IP 주소를 NIC에 추가, 변경 및 제거하는 방법에 대해 알아봅니다."
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
ms.openlocfilehash: cf7dbc648136897772bbd068687313eec16bed75
ms.contentlocale: ko-kr
ms.lasthandoff: 05/13/2017


---

# <a name="add-change-or-remove-ip-addresses-for-azure-network-interfaces"></a>Azure 네트워크 인터페이스에 대한 IP 주소 추가, 변경 또는 제거

NIC(네트워크 인터페이스)에 대한 공용 및 개인 IP 주소를 추가, 변경 및 제거하는 방법에 대해 알아봅니다. NIC에 할당된 개인 IP 주소를 사용하면 VM에서 인터넷 및 Azure VNet(가상 네트워크)에 연결된 다른 리소스와 통신할 수 있습니다. NIC에 할당된 공용 IP 주소를 사용하면 인터넷에서 VM으로의 인바운드 방식으로 통신할 수 있습니다. 

NIC를 만들거나 변경하거나 삭제해야 하는 경우 [NIC 설정 및 작업](virtual-network-network-interface.md) 문서를 참조하세요. 가상 컴퓨터에서 NIC를 추가하거나 제거해야 하는 경우 [NIC 추가 또는 제거](virtual-network-network-interface-vm.md) 문서를 참조하세요. 


## <a name="before"></a>시작하기 전에

이 문서에서 설명하는 섹션의 모든 단계를 수행하기 전에 다음 작업을 완료해야 합니다.

- [Azure 제한](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) 문서를 검토하여 공용 및 개인 IP 주소에 대한 제한 사항을 알아봅니다.
- Azure 계정이 있는 Azure Portal, Azure CLI(명령줄 인터페이스) 또는 Azure PowerShell에 로그인합니다. 아직 Azure 계정이 없으면 [체험 계정](https://azure.microsoft.com/free)에 등록합니다.
- PowerShell 명령을 사용하여 이 문서의 작업을 수행하는 경우 [Azure PowerShell 설치 및 구성 방법](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json) 문서의 단계를 완료하여 Azure PowerShell을 설치하고 구성합니다. 최신 버전의 Azure PowerShell cmdlet을 설치했는지 확인합니다. 예제와 함께 PowerShell 명령에 대한 도움말을 보려면 `get-help <command> -full`을 입력합니다.
- Azure CLI(명령줄 인터페이스) 명령을 사용하여 이 문서의 작업을 수행하는 경우 [Azure CLI 설치 및 구성 방법](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json) 문서의 단계를 완료하여 Azure CLI를 설치하고 구성합니다. 최신 버전의 Azure CLI를 설치했는지 확인합니다. CLI 명령에 대한 도움말을 보려면 `az <command> --help`를 입력합니다.

## <a name="about"></a>NIC 및 IP 주소 정보

각 NIC에는 [Azure 제한](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)에 해당하는 여러 개인 및 공용 IP 주소를 할당할 수 있습니다. NIC에 여러 IP 주소를 할당하면 다음과 같은 시나리오에서 유용합니다.
- 단일 서버에서 IP 주소와 SSL 인증서를 사용하여 여러 웹 사이트 또는 서비스를 호스트할 수 있습니다.
- VM이 방화벽 또는 부하 분산 장치와 같은 네트워크 가상 어플라이언스로 사용됩니다.
- NIC의 개인 IP 주소를 Azure Load Balancer 백 엔드 풀에 추가할 수 있습니다. 이전에 기본 NIC의 기본 IP 주소만 백 엔드 풀에 추가할 수 있었습니다. 여러 IP 구성의 부하를 분산하는 방법에 대해 자세히 알아보려면 [여러 IP 구성의 부하 분산](../load-balancer/load-balancer-multiple-ip.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 문서를 참조하세요.

IP 주소는 IP 구성에 할당됩니다. NIC에는 항상 **기본** IP 구성이 할당되지만 여러 **보조** 구성도 할당될 수 있습니다. 각 IP 구성에는 다음 유형의 주소 중 하나 또는 둘 다 할당됩니다.
- **개인:** 개인 IP 주소를 사용하면 VM에서 NIC가 있는 VNet에 연결된 다른 리소스와 통신할 수 있습니다. IP 구성에는 하나의 개인 IP 주소가 할당되어 있어야 합니다. Azure DHCP 서버는 NIC의 기본 IP 구성에 대한 개인 IP 주소를 VM 운영 체제 내의 NIC에 할당합니다. 또한 개인 IP 주소를 사용하면 VM에서 인터넷으로의 아웃바운드 방식으로 통신할 수 있습니다. 아웃바운드 연결은 SNAT(원본 네트워크 주소 변환) 모드가 됩니다. Azure 아웃바운드 인터넷 연결에 대해 자세히 알아보려면 [Azure 아웃바운드 인터넷 연결](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 문서를 참조하세요. 인터넷에서 VM의 개인 IP 주소로의 인바운드 방식으로는 통신할 수 없습니다.
- **공용:** 공용 IP 주소를 사용하면 인터넷에서 VM으로의 인바운드 연결을 설정할 수 있습니다. 인터넷으로의 아웃바운드 연결은 SNAT 모드가 되지 않습니다. IP 구성에 공용 IP 주소를 할당할 수 있지만, 반드시 필요한 것은 아닙니다. 공용 IP 주소에 대해 자세히 알아보려면 [공용 IP 주소](virtual-network-public-ip-address.md)를 참조하세요.

NIC에 할당할 수 있는 공용 및 개인 IP 주소의 수는 제한됩니다. 자세한 내용은 [Azure 제한](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) 문서를 참조하세요.

공용 및 개인 IP 주소는 다음 할당 방법을 사용하여 할당할 수 있습니다.
- **동적:** 동적 개인 및 공용 IP 주소는 기본적으로 할당됩니다. VM이 중지됨(할당 취소됨) 상태가 된 다음 다시 시작되면 동적 주소가 변경될 수 있습니다. VM 수명이 존속하는 동안 IP 주소를 변경하지 않으려면 고정 주소를 사용합니다.
- **고정:** 고정 주소는 VM을 삭제할 때까지 변경되지 않습니다. NIC가 연결된 서브넷의 주소 공간에서 고정 개인 IP 주소를 할당합니다. Azure에서 고정 공용 IP 주소를 할당하는 방법에 대해 자세히 알아보려면 [공용 IP 주소](virtual-network-public-ip-address.md) 문서를 참조하세요.

## <a name="create-ip-config"></a>IP 주소 추가

[Azure 제한](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) 문서에 나열된 제한 내에서 필요한 만큼 많은 IP 주소를 NIC에 추가할 수 있습니다.

1. 구독에 대한 네트워크 참가자 역할에 권한(최소)이 할당된 계정으로 [Azure Portal](https://portal.azure.com)에 로그인합니다. 계정에 역할 및 권한을 할당하는 방법에 대한 자세한 내용은 [Azure 역할 기반 액세스 제어의 기본 제공 역할](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) 문서를 참조하세요.
2. Azure Portal 위쪽의 *리소스 검색* 텍스트가 있는 상자에서 *네트워크 인터페이스*를 입력합니다. 검색 결과에 표시된 **네트워크 인터페이스**를 클릭합니다.
3. 표시되는 **네트워크 인터페이스** 블레이드에서 IP 주소를 추가하려는 NIC를 클릭합니다.
4. 선택한 NIC에 대한 블레이드의 **설정** 섹션에서 **IP 구성**을 클릭합니다.
5. IP 구성에 대해 열리는 블레이드에서 **+ 추가**를 클릭합니다.
6. 다음을 지정한 다음 **확인**을 클릭하여 **IP 구성 추가** 블레이드를 닫습니다.

    |설정|Required?|세부 정보|
    |---|---|---|
    |이름|예|NIC마다 고유해야 합니다.|
    |형식|예|기존 NIC에 IP 구성을 추가하고 각 NIC에 기본 IP 구성이 있어야 하므로 유일한 옵션은 **보조**입니다.|
    |개인 IP 주소 할당 방법|예|VM을 중지(할당 취소)된 상태에서 다시 시작하면 **동적** 주소가 변경될 수 있습니다. Azure에서는 NIC가 연결된 서브넷의 주소 공간에서 사용 가능한 주소를 할당합니다. NIC를 삭제할 때까지는 **고정** 주소가 해제되지 않습니다. 다른 IP 구성에서 현재 사용하지 않는 서브넷 주소 공간 범위에서 IP 주소를 지정합니다.|
    |공용 IP 주소|아니요|**사용 안 함:** 현재 공용 IP 주소 리소스는 IP 구성과 연결되지 않습니다. **사용:** 기존 공용 IP 주소를 선택하거나 새 공용 IP 주소를 만듭니다. 공용 IP 주소를 만드는 방법에 대한 자세한 내용은 [공용 IP 주소](virtual-network-public-ip-address.md#create) 문서를 참조하세요.|
7. [가상 컴퓨터에 여러 IP 주소 할당](virtual-network-multiple-ip-addresses-portal.md#os-config) 문서의 지침을 수행하여 VM 운영 체제에 보조 개인 IP 주소를 수동으로 추가합니다. VM 운영 체제에 공용 IP 주소를 추가하지 마세요.

**명령**

|도구|명령|
|---|---|
|CLI|[az network nic ip-config create](/cli/azure/network/nic/ip-config?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|PowerShell|[Add-AzureRmNetworkInterfaceIpConfig](/powershell/resourcemanager/azurerm.network/v3.4.0/add-azurermnetworkinterfaceipconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="change-ip-config"></a>IP 주소 설정 변경

IP 주소의 할당 방법을 변경하거나, 고정 IP 주소를 변경하거나, NIC에 할당된 공용 IP 주소를 변경해야 할 수 있습니다. VM의 보조 NIC와 연결된 보조 IP 구성의 개인 IP 주소를 변경하는 경우(자세한 정보: [기본 및 보조 NIC](virtual-network-network-interface-vm.md#about)) 다음 단계를 수행하기 전에 VM을 중지됨(할당 취소됨) 상태로 전환합니다. 

1. 구독에 대한 네트워크 참가자 역할에 권한(최소)이 할당된 계정으로 [Azure Portal](https://portal.azure.com)에 로그인합니다. 계정에 역할 및 권한을 할당하는 방법에 대한 자세한 내용은 [Azure 역할 기반 액세스 제어의 기본 제공 역할](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) 문서를 참조하세요.
2. Azure Portal 위쪽의 *리소스 검색* 텍스트가 있는 상자에서 *네트워크 인터페이스*를 입력합니다. 검색 결과에 표시된 **네트워크 인터페이스**를 클릭합니다.
3. 표시되는 **네트워크 인터페이스** 블레이드에서 IP 주소 설정을 보거나 변경하려는 NIC를 클릭합니다.
4. 선택한 NIC에 대한 블레이드의 **설정** 섹션에서 **IP 구성**을 클릭합니다.
5. IP 구성에 대해 열리는 블레이드 목록에서 수정하려는 IP 구성을 클릭합니다.
6. 이 문서에 설명하는 [IP 구성 추가](#create-ip-config) 섹션의 6단계에서 설정 관련 정보를 사용하여 원하는 대로 설정을 변경합니다. **저장**을 클릭하여 변경한 IP 구성에 대한 블레이드를 닫습니다.

>[!NOTE]
>기본 NIC에 여러 IP 구성이 있고 기본 IP 구성의 개인 IP 주소를 변경한 경우, Windows 내에서 모든 보조 IP 주소를 NIC에 수동으로 다시 할당해야 합니다(Linux에서는 필요 없음). 운영 체제 내에서 IP 주소를 NIC에 수동으로 할당하려면 [가상 컴퓨터에 여러 IP 주소 할당](virtual-network-multiple-ip-addresses-portal.md#os-config) 문서를 참조하세요. VM 운영 체제에 공용 IP 주소를 추가하지 마세요.

**명령**

|도구|명령|
|---|---|
|CLI|[az network nic ip-config update](/cli/azure/network/nic/ip-config?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|PowerShell|[Set-AzureRMNetworkInterfaceIpConfig](/powershell/resourcemanager/azurerm.network/v3.4.0/set-azurermnetworkinterfaceipconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="delete-ip-config"></a>IP 주소 제거

NIC에서 개인 및 공용 IP 주소를 제거할 수 있지만, NIC에는 항상 개인 IP 주소가 하나 이상 할당되어 있어야 합니다.

1. 구독에 대한 네트워크 참가자 역할에 권한(최소)이 할당된 계정으로 [Azure Portal](https://portal.azure.com)에 로그인합니다. 계정에 역할 및 권한을 할당하는 방법에 대한 자세한 내용은 [Azure 역할 기반 액세스 제어의 기본 제공 역할](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) 문서를 참조하세요.
2. Azure Portal 위쪽의 *리소스 검색* 텍스트가 있는 상자에서 *네트워크 인터페이스*를 입력합니다. 검색 결과에 표시된 **네트워크 인터페이스**를 클릭합니다.
3. 표시되는 **네트워크 인터페이스** 블레이드에서 IP 주소를 제거하려는 NIC를 클릭합니다.
4. 선택한 NIC에 대한 블레이드의 **설정** 섹션에서 **IP 구성**을 클릭합니다.
5. 삭제하려는 보조 IP 구성(기본 구성은 삭제할 수 없음)을 마우스 오른쪽 단추로 클릭하고 **삭제**를 클릭한 다음 **예**를 클릭하여 삭제를 확인합니다. 구성에 연결된 공용 IP 주소 리소스가 있는 경우 해당 리소스는 IP 구성과 분리되지만 삭제되지는 않습니다.
6. **IP 구성** 블레이드를 닫습니다.

**명령**

|도구|명령|
|---|---|
|CLI|[az network nic ip-config delete](/cli/azure/network/nic/ip-config?toc=%2fazure%2fvirtual-network%2ftoc.json#delete)|
|PowerShell|[Remove-AzureRmNetworkInterfaceIpConfig](/powershell/resourcemanager/azurerm.network/v3.4.0/remove-azurermnetworkinterfaceipconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|


## <a name="next-steps"></a>다음 단계
여러 NIC 또는 IP 주소가 있는 VM을 만들려면 다음 문서를 참조하세요.

**명령**

|작업|도구|
|---|---|
|여러 NIC를 사용하여 VM 만들기|[CLI](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
||[PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|여러 IP 주소가 있는 단일 NIC VM 만들기|[CLI](virtual-network-multiple-ip-addresses-cli.md)|
||[PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|

