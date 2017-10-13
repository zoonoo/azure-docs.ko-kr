---
title: "Azure 네트워크 인터페이스용 IP 주소 구성 | Microsoft 문서"
description: "네트워크 인터페이스용 개인 및 공용 IP 주소를 추가, 변경 및 제거하는 방법에 대해 알아봅니다."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/24/2017
ms.author: jdial
ms.openlocfilehash: 254d5d43f0f665f64ddfe276fe31702f66f16758
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="add-change-or-remove-ip-addresses-for-an-azure-network-interface"></a>Azure 네트워크 인터페이스용 IP 주소 추가, 변경 또는 제거

네트워크 인터페이스용 공용 및 개인 IP 주소를 추가, 변경 및 제거하는 방법에 대해 알아봅니다. 가상 컴퓨터는 네트워크 인터페이스에 할당된 개인 IP 주소를 통해 Azure Virtual Network 및 연결된 네트워크의 다른 리소스와 통신할 수 있습니다. 또한 개인 IP 주소가 있으면 예측할 수 없는 IP 주소를 사용하는 인터넷으로의 아웃바운드 통신도 가능합니다. 네트워크 인터페이스에 할당된 [공용 IP 주소](virtual-network-public-ip-address.md)를 사용하면 인터넷에서 가상 컴퓨터에 대한 인바운드 통신을 수행할 수 있습니다. 또한 공용 IP 주소가 있으면 가상 컴퓨터에서 예측할 수 없는 IP 주소를 사용하는 인터넷으로의 아웃바운드 통신도 가능합니다. 자세한 내용은 [Azure에서 아웃바운드 연결 이해](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json)를 참조하세요. 

네트워크 인터페이스를 만들거나 변경하거나 삭제해야 하는 경우 [네트워크 인터페이스 관리](virtual-network-network-interface.md) 문서를 확인하세요. 네트워크 인터페이스를 추가하거나 가상 컴퓨터에서 네트워크 인터페이스를 제거해야 하는 경우에는 [네트워크 인터페이스 추가 또는 제거](virtual-network-network-interface-vm.md) 문서를 확인하세요. 


## <a name="before-you-begin"></a>시작하기 전에

이 문서에서 설명하는 섹션의 모든 단계를 수행하려면 다음 작업을 완료해야 합니다.

- [Azure 제한](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) 문서를 검토하여 공용 및 개인 IP 주소에 대한 제한 사항을 알아봅니다.
- Azure 계정으로 Azure [Portal](https://portal.azure.com), Azure CLI(명령줄 인터페이스) 또는 Azure PowerShell에 로그인합니다. 아직 Azure 계정이 없으면 [평가판 계정](https://azure.microsoft.com/free)에 등록합니다.
- PowerShell 명령을 사용하여 이 문서의 작업을 완료하는 경우 [Azure PowerShell을 설치 및 구성](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json)합니다. 최신 버전의 Azure PowerShell commandlet을 설치했는지 확인합니다. 예제와 함께 PowerShell 명령에 대한 도움말을 보려면 `get-help <command> -full`을 입력합니다.
- Azure CLI(명령줄 인터페이스) 명령을 사용하여 이 문서의 작업을 완료하는 경우 [Azure CLI를 설치 및 구성](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)합니다. 최신 버전의 Azure CLI를 설치했는지 확인합니다. CLI 명령에 대한 도움말을 보려면 `az <command> --help`를 입력합니다. CLI 및 해당 필수 구성 요소를 설치하는 대신 Azure Cloud Shell을 사용할 수 있습니다. Azure Cloud Shell은 Azure Portal에서 직접 실행할 수 있는 평가판 Bash 셸입니다. Azure CLI가 사전 설치되어 계정에서 사용하도록 구성되어 있습니다. Cloud Shell을 사용하려면 [Portal](https://portal.azure.com) 위쪽의 Cloud Shell **>_** 단추를 클릭합니다.

## <a name="add-ip-addresses"></a>IP 주소 추가

[Azure 제한](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) 문서에 나열된 제한 내에서 필요한 만큼 많은 [개인](#private) 및 [공용](#public) [IPv4](#ipv4) 주소를 네트워크 인터페이스에 추가할 수 있습니다. Portal을 사용하여 기존 네트워크 인터페이스에 IPv6 주소를 추가할 수는 없습니다. 단, 네트워크 인터페이스를 만들 때 Portal을 사용하여 개인 IPv6 주소를 네트워크 인터페이스에 추가할 수는 있습니다. PowerShell 또는 CLI를 사용하면 가상 컴퓨터에 연결되지 않은 기존 네트워크 인터페이스용으로 [보조 IP 구성](#secondary) 하나에 개인 IPv6 주소를 추가할 수 있습니다(기존 보조 IP 구성이 없는 경우). 어떤 도구로도 네트워크 인터페이스에 공용 IPv6 주소를 추가할 수는 없습니다. IPv6 주소 사용에 대한 자세한 내용은 [IPv6](#ipv6)을 참조하세요. 

1. 구독에 대한 네트워크 참가자 역할에 권한(최소)이 할당된 계정으로 [Azure Portal](https://portal.azure.com)에 로그인합니다. 계정에 역할 및 권한을 할당하는 방법에 대한 자세한 내용은 [Azure 역할 기반 액세스 제어의 기본 제공 역할](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) 문서를 참조하세요.
2. Azure Portal 위쪽의 *리소스 검색* 텍스트가 있는 상자에서 *네트워크 인터페이스*를 입력합니다. 검색 결과에 표시된 **네트워크 인터페이스**를 클릭합니다.
3. 표시되는 **네트워크 인터페이스** 블레이드에서 IPv4 주소를 추가하려는 네트워크 인터페이스를 클릭합니다.
4. 선택한 네트워크 인터페이스에 대한 블레이드의 **설정** 섹션에서 **IP 구성**을 클릭합니다.
5. IP 구성에 대해 열리는 블레이드에서 **+ 추가**를 클릭합니다.
6. 다음을 지정한 다음 **확인**을 클릭하여 **IP 구성 추가** 블레이드를 닫습니다.

    |설정|Required?|세부 정보|
    |---|---|---|
    |이름|예|네트워크 인터페이스에 대해 고유해야 합니다.|
    |형식|예|기존 네트워크 인터페이스에 IP 구성을 추가할 것이며, 각 NIC에는 [기본](#primary) IP 구성이 있어야 하므로 **보조** 옵션만 선택 가능합니다.|
    |개인 IP 주소 할당 방법|예|가상 컴퓨터를 중지(할당 취소)된 상태에서 다시 시작하면 [**동적**](#dynamic) 주소가 변경될 수 있습니다. Azure에서는 네트워크 인터페이스가 연결된 서브넷의 주소 공간에서 사용 가능한 주소를 할당합니다. [**고정**](#static) 주소는 네트워크 인터페이스를 삭제할 때까지 해제되지 않습니다. 다른 IP 구성에서 현재 사용하지 않는 서브넷 주소 공간 범위에서 IP 주소를 지정합니다.|
    |공용 IP 주소|아니요|**사용 안 함:** 현재 공용 IP 주소 리소스는 IP 구성과 연결되지 않습니다. **사용:** 기존 IPv4 공용 IP 주소를 선택하거나 새 공용 IP 주소를 만듭니다. 공용 IP 주소를 만드는 방법에 대한 자세한 내용은 [공용 IP 주소](virtual-network-public-ip-address.md#create-a-public-ip-address) 문서를 참조하세요.|
7. [가상 컴퓨터 운영 체제에 여러 IP 주소 할당](virtual-network-multiple-ip-addresses-portal.md#os-config) 문서의 지침을 수행하여 가상 컴퓨터 운영 체제에 보조 개인 IP 주소를 수동으로 추가합니다. 가상 컴퓨터 운영 체제에 IP 주소를 수동으로 추가하기 전에 고려해야 하는 특수한 사항은 [개인](#private) IP 주소를 참조하세요. 가상 컴퓨터 운영 체제에는 공용 IP 주소를 추가하지 마세요.

**명령**

|도구|명령|
|---|---|
|CLI|[az network nic ip-config create](/cli/azure/network/nic/ip-config?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|PowerShell|[Add-AzureRmNetworkInterfaceIpConfig](/powershell/module/azurerm.network/add-azurermnetworkinterfaceipconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="change-ip-address-settings"></a>IP 주소 설정 변경

IPv4 주소의 할당 방법을 변경하거나, 고정 IPv4 주소를 변경하거나, 네트워크 인터페이스에 할당된 공용 IP 주소를 변경해야 할 수 있습니다. 가상 컴퓨터의 보조 네트워크 인터페이스와 연결된 보조 IP 구성의 개인 IPv4 주소를 변경하는 경우(자세한 정보: [기본 및 보조 네트워크 인터페이스](virtual-network-network-interface-vm.md#about)) 다음 단계를 수행하기 전에 가상 컴퓨터를 중지됨(할당 취소됨) 상태로 전환합니다. 

1. 구독에 대한 네트워크 참가자 역할에 권한(최소)이 할당된 계정으로 [Azure Portal](https://portal.azure.com)에 로그인합니다. 계정에 역할 및 권한을 할당하는 방법에 대한 자세한 내용은 [Azure 역할 기반 액세스 제어의 기본 제공 역할](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) 문서를 참조하세요.
2. Azure Portal 위쪽의 *리소스 검색* 텍스트가 있는 상자에서 *네트워크 인터페이스*를 입력합니다. 검색 결과에 표시된 **네트워크 인터페이스**를 클릭합니다.
3. 표시되는 **네트워크 인터페이스** 블레이드에서 IP 주소 설정을 보거나 변경하려는 네트워크 인터페이스를 클릭합니다.
4. 선택한 네트워크 인터페이스에 대한 블레이드의 **설정** 섹션에서 **IP 구성**을 클릭합니다.
5. IP 구성에 대해 열리는 블레이드 목록에서 수정하려는 IP 구성을 클릭합니다.
6. 이 문서에 설명하는 [IP 구성 추가](#create-ip-config) 섹션의 6단계에서 설정 관련 정보를 사용하여 원하는 대로 설정을 변경합니다. **저장**을 클릭하여 변경한 IP 구성에 대한 블레이드를 닫습니다.

>[!NOTE]
>기본 네트워크 인터페이스에 여러 IP 구성이 있는 상태에서 기본 IP 구성의 개인 IP 주소를 변경하는 경우에는 Windows 내에서 기본 및 보조 IP 주소를 네트워크 인터페이스에 수동으로 다시 할당해야 합니다(Linux에서는 필요 없음). 운영 체제 내에서 IP 주소를 네트워크 인터페이스에 수동으로 할당하려면 [가상 컴퓨터에 여러 IP 주소 할당](virtual-network-multiple-ip-addresses-portal.md#os-config) 문서를 확인하세요. 가상 컴퓨터 운영 체제에 IP 주소를 수동으로 추가하기 전에 고려해야 하는 특수한 사항은 [개인](#private) IP 주소를 참조하세요. 가상 컴퓨터 운영 체제에는 공용 IP 주소를 추가하지 마세요.

**명령**

|도구|명령|
|---|---|
|CLI|[az network nic ip-config update](/cli/azure/network/nic/ip-config?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|PowerShell|[Set-AzureRMNetworkInterfaceIpConfig](/powershell/module/azurerm.network/set-azurermnetworkinterfaceipconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="remove-ip-addresses"></a>IP 주소 제거

네트워크 인터페이스에서 [개인](#private) 및 [공용](#public) IP 주소를 제거할 수는 있지만, 네트워크 인터페이스에는 항상 개인 IPv4 주소가 하나 이상 할당되어 있어야 합니다.

1. 구독에 대한 네트워크 참가자 역할에 권한(최소)이 할당된 계정으로 [Azure Portal](https://portal.azure.com)에 로그인합니다. 계정에 역할 및 권한을 할당하는 방법에 대한 자세한 내용은 [Azure 역할 기반 액세스 제어의 기본 제공 역할](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) 문서를 참조하세요.
2. Azure Portal 위쪽의 *리소스 검색* 텍스트가 있는 상자에서 *네트워크 인터페이스*를 입력합니다. 검색 결과에 표시된 **네트워크 인터페이스**를 클릭합니다.
3. 표시되는 **네트워크 인터페이스** 블레이드에서 IP 주소를 제거하려는 네트워크 인터페이스를 클릭합니다.
4. 선택한 네트워크 인터페이스에 대한 블레이드의 **설정** 섹션에서 **IP 구성**을 클릭합니다.
5. 삭제하려는 [보조](#secondary) IP 구성([기본](#primary) 구성은 삭제할 수 없음)을 마우스 오른쪽 단추로 클릭하고 **삭제**를 클릭한 다음 **예**를 클릭하여 삭제를 확인합니다. 구성에 연결된 공용 IP 주소 리소스가 있는 경우 해당 리소스는 IP 구성과 분리되지만 삭제되지는 않습니다.
6. **IP 구성** 블레이드를 닫습니다.

**명령**

|도구|명령|
|---|---|
|CLI|[az network nic ip-config delete](/cli/azure/network/nic/ip-config?toc=%2fazure%2fvirtual-network%2ftoc.json#delete)|
|PowerShell|[Remove-AzureRmNetworkInterfaceIpConfig](/powershell/module/azurerm.network/remove-azurermnetworkinterfaceipconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="ip-configurations"></a>IP 구성

[개인](#private) 및 [공용](#public)(옵션) IP 주소는 네트워크 인터페이스에 할당되어 있는 하나 이상의 IP 구성에 할당됩니다. IP 구성에는 다음과 같은 두 종류가 있습니다.

### <a name="primary"></a>보조

각 네트워크 인터페이스에는 기본 IP 구성 하나가 할당됩니다. 기본 IP 구성의 특성은 다음과 같습니다.

- [개인](#private) [IPv4](#ipv4) 주소가 할당되어 있습니다. 개인 [IPv6](#ipv6) 주소를 기본 IP 구성에 할당할 수는 없습니다.
- [공용](#public) IPv4 주소도 할당되어 있을 수 있습니다. 공용 IPv6 주소를 기본 또는 보조 IP 구성에 할당할 수는 없습니다. 그러나 공용 IPv6 주소를 Azure Load Balancer에 할당할 수는 있습니다. Azure Load Balancer는 가상 컴퓨터의 개인 IPv6 주소로 트래픽을 부하 분산할 수 있습니다. 자세한 내용은 [IPv6 관련 세부 정보 및 제한](../load-balancer/load-balancer-ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#details-and-limitations)을 참조하세요.

### <a name="secondary"></a>주

네트워크 인터페이스에는 기본 IP 구성 외의 보조 IP 구성이 하나 이상 할당되어 있을 수도 있고 그렇지 않을 수도 있습니다. 보조 IP 구성의 특징은 다음과 같습니다.

- 개인 IPv4 또는 IPv6 주소가 할당되어 있어야 합니다. 주소가 IPv6인 경우 네트워크 인터페이스에는 보조 IP 구성이 하나만 포함될 수 있습니다. 주소가 IPv4인 경우 네트워크 인터페이스에는 여러 보조 IP 구성이 할당될 수 있습니다. 네트워크 인터페이스에 할당할 수 있는 개인 및 공용 IPv4 주소의 수에 대해 자세히 알아보려면 [Azure 제한](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) 문서를 참조하세요.  
- 개인 IP 주소가 IPv4이면 공용 IPv4 주소도 할당되어 있을 수 있습니다. 개인 IP 주소가 IPv6이면 해당 IP 구성에 공용 IPv4 또는 IPv6 주소를 할당할 수 없습니다. 네트워크 인터페이스에 여러 IP 주소를 할당하면 다음과 같은 시나리오에서 유용합니다.
    - 단일 서버에서 IP 주소와 SSL 인증서를 사용하여 여러 웹 사이트 또는 서비스를 호스트할 수 있습니다.
    - 가상 컴퓨터가 방화벽, 부하 분산 장치 등의 네트워크 가상 어플라이언스로 사용되는 경우
    - Azure Load Balancer 백 엔드 풀에 네트워크 인터페이스용 개인 IPv4 주소를 추가하려는 경우 이전에는 기본 네트워크 인터페이스의 기본 IPv4 주소만 백 엔드 풀에 추가할 수 있었습니다. 여러 IPv4 구성의 부하를 분산하는 방법에 대해 자세히 알아보려면 [여러 IP 구성의 부하 분산](../load-balancer/load-balancer-multiple-ip.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 문서를 참조하세요. 
    - 네트워크 인터페이스에 할당된 IPv6 주소 하나를 부하 분산하려는 경우. 개인 IPv6 주소를 부하 분산하는 방법에 대해 자세히 알아보려면 [IPv6 주소 부하 분산](../load-balancer/load-balancer-ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 문서를 참조하세요.


## <a name="address-types"></a>주소 유형

[IP 구성](#ip-configurations)에 할당할 수 있는 IP 주소 유형은 다음과 같습니다.

### <a name="private"></a>개인

개인 [IPv4](#ipv4) 주소를 할당하면 가상 컴퓨터가 가상 네트워크 또는 기타 연결된 네트워크의 다른 리소스와 통신할 수 있습니다. 가상 컴퓨터는 인바운드로 통신할 수 없으며 개인 [IPv6](#ipv6) 주소를 사용하여 아웃바운드로 통신할 수도 없습니다. 단, 한 가지 예외적인 경우가 있습니다. 즉, 가상 컴퓨터는 IPv6 주소를 사용하여 Azure Load Balancer와 통신할 수 있습니다. 자세한 내용은 [IPv6 관련 세부 정보 및 제한](../load-balancer/load-balancer-ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#details-and-limitations)을 참조하세요. 

기본적으로 Azure DHCP 서버는 네트워크 인터페이스의 [기본 IP 구성](#primary)용 개인 IPv4 주소를 가상 컴퓨터 운영 체제 내의 네트워크 인터페이스에 할당합니다. 반드시 필요한 경우가 아니면 가상 컴퓨터 운영 체제 내에서 네트워크 인터페이스의 IP 주소를 수동으로 설정해서는 안 됩니다. 

> [!WARNING]
> 가상 컴퓨터의 운영 체제 내에서 네트워크 인터페이스의 기본 IP 주소로 설정된 IPv4 주소가 Azure 내에서 가상 컴퓨터에 연결된 기본 네트워크 인터페이스의 기본 IP 구성에 할당되어 있는 개인 IPv4 주소와 다르면 가상 컴퓨터 연결이 끊어집니다.

가상 컴퓨터 운영 체제 내에서 네트워크 인터페이스의 IP 주소를 수동으로 설정해야 하는 시나리오도 있습니다. 예를 들어 Azure 가상 컴퓨터에 여러 IP 주소를 추가할 때는 Windows 운영 체제의 기본 및 보조 IP 주소를 수동으로 설정해야 합니다. Linux 가상 컴퓨터의 경우에는 보조 IP 주소만 수동으로 설정하면 됩니다. 자세한 내용은 [VM 운영 체제에 IP 주소 추가](virtual-network-multiple-ip-addresses-portal.md#os-config)를 참조하세요. 운영 체제 내에서 IP 주소를 수동으로 설정할 때는 항상 동적 할당이 아닌 정적 할당 방법을 사용하여 네트워크 인터페이스의 IP 구성에 주소를 할당하는 것이 좋습니다. 정적 방법을 사용하여 주소를 할당하면 Azure 내에서 주소가 변경되지 않습니다. IP 구성에 할당된 주소를 변경해야 하는 경우에는 다음을 수행하는 것이 좋습니다.

1. 가상 컴퓨터가 Azure DHCP 서버에서 주소를 받으려면 운영 체제 내에서 IP 주소 할당을 DHCP로 다시 변경하고 가상 컴퓨터를 다시 시작합니다.
2. 가상 컴퓨터를 중지(할당 취소)합니다.
3. Azure 내에서 IP 구성의 IP 주소를 변경합니다.
4. 가상 컴퓨터를 시작합니다.
5. 운영 체제 내의 보조 IP 주소와 Windows 내의 기본 IP 주소를 Azure에서 설정한 주소와 일치하도록 [수동으로 구성](virtual-network-multiple-ip-addresses-portal.md#os-config)합니다.
 
위의 단계를 수행하면 가상 컴퓨터 운영 체제 내와 Azure 내에서 네트워크 인터페이스에 할당된 개인 IP 주소는 동일하게 유지됩니다. 운영 체제 내에서 IP 주소를 수동으로 설정한 구독 내의 가상 컴퓨터를 추적하려는 경우 가상 컴퓨터에 Azure [태그](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#tags)를 추가할 수 있습니다. 예를 들어 "IP 주소 할당: 정적" 등을 사용할 수 있습니다. 이렇게 하면 운영 체제 내에서 IP 주소를 수동으로 설정한 구독 내 가상 컴퓨터를 쉽게 찾을 수 있습니다.

또한 개인 IP 주소가 있으면 가상 컴퓨터가 같은 가상 네트워크 또는 연결된 가상 네트워크 내의 다른 리소스와 통신할 수 있을 뿐 아니라 인터넷과 아웃바운드로도 통신할 수 있습니다. 아웃바운드 연결은 Azure에서 예측할 수 없는 공용 IP 주소로 변환하는 소스 네트워크 주소입니다. Azure 아웃바운드 인터넷 연결에 대해 자세히 알아보려면 [Azure 아웃바운드 인터넷 연결](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 문서를 참조하세요. 인터넷에서 가상 컴퓨터 개인 IP 주소로의 인바운드 통신은 불가능합니다.

### <a name="public"></a>공용

공용 IP 주소를 사용하면 인터넷에서 가상 컴퓨터로의 인바운드 연결을 설정할 수 있습니다. 인터넷으로의 아웃바운드 연결에서는 예측 가능한 IP 주소를 사용합니다. 자세한 내용은 [Azure에서 아웃바운드 연결 이해](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json)를 참조하세요. IP 구성에 공용 IP 주소를 할당할 수 있지만, 반드시 필요한 것은 아닙니다. 공용 IP 주소를 할당하지 않더라도 가상 컴퓨터는 해당 개인 IP 주소를 사용하여 인터넷과 아웃바운드 통신을 할 수 있습니다. 공용 IP 주소에 대해 자세히 알아보려면 [공용 IP 주소](virtual-network-public-ip-address.md)를 참조하세요.

네트워크 인터페이스에 할당할 수 있는 공용 및 개인 IP 주소의 수에는 제한이 있습니다. 자세한 내용은 [Azure 제한](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) 문서를 참조하세요.

> [!NOTE]
> Azure에서는 가상 컴퓨터의 개인 IP 주소를 공용 IP 주소로 변환합니다. 따라서 운영 체제는 할당된 공용 IP 주소를 인식할 수 없으므로 운영 체제 내에서 공용 IP 주소를 수동으로 할당할 필요가 없습니다.

## <a name="assignment-methods"></a>할당 방법

다음 할당 방법을 사용하여 공용 및 개인 IP 주소를 할당합니다.

### <a name="dynamic"></a>동적

동적 개인 IPv4 및 IPv6(옵션) 주소는 기본적으로 할당됩니다. 가상 컴퓨터가 중지됨(할당 취소됨) 상태가 된 다음 다시 시작되면 동적 주소가 변경될 수 있습니다. 가상 컴퓨터의 수명 동안 IPv4 주소가 변경되지 않도록 하려면 정적 방법을 사용하여 주소를 할당하세요. 개인 IPv6 주소는 동적 할당 방법을 통해서만 할당할 수 있습니다. 공용 IPv6 주소는 어떤 방법으로도 IP 구성에 할당할 수 없습니다.

### <a name="static"></a>정적

정적 방법을 사용하여 할당한 주소는 가상 컴퓨터를 삭제할 때까지 변경되지 않습니다. 고정 개인 IPv4 주소는 네트워크 인터페이스가 있는 서브넷의 주소 공간에서 IP 구성에 수동으로 할당합니다. 필요에 따라 공용 또는 개인 고정 IPv4 주소를 IP 구성에 할당할 수 있습니다. 고정 공용 또는 개인 IPv6 주소는 IP 구성에 할당할 수 없습니다. Azure에서 고정 공용 IPv4 주소를 할당하는 방법에 대해 자세히 알아보려면 [공용 IP 주소](virtual-network-public-ip-address.md) 문서를 참조하세요.

## <a name="ip-address-versions"></a>IP 주소 버전

주소를 할당할 때 다음과 같은 버전을 지정할 수 있습니다.

### <a name="ipv4"></a>IPv4

각 네트워크 인터페이스에는 [개인](#private) [IPv4](#ipv4) 주소가 할당된 [기본](#primary) IP 구성 하나가 있어야 합니다. 각각 IPv4 개인 IP 주소와 IPv4 [공용](#public) IP 주소(옵션)를 포함하는 [보조](#secondary) IP 구성을 하나 이상 추가할 수 있습니다.

### <a name="ipv6"></a>IPv6

네트워크 인터페이스의 보조 IP 구성 하나에 개인 [IPv6](#ipv6) 주소 하나를 할당할 수도 있고 할당하지 않을 수도 있습니다. 네트워크 인터페이스는 기존 보조 IP 구성을 포함할 수 없습니다. Portal을 사용하여 IPv6 주소가 포함된 IP 구성을 추가할 수는 없습니다. 개인 IPv6 주소가 포함된 IP 구성을 기존 네트워크 인터페이스에 추가하려면 PowerShell이나 CLI를 사용합니다. 이 네트워크 인터페이스를 기존 VM에 연결할 수 없습니다.

> [!NOTE]
> 포털을 사용하여 IPv6 주소로 네트워크 인터페이스를 만들 수 있더라도 포털을 사용하여 새로운 또는 기존 가상 컴퓨터에 기존 네트워크 인터페이스를 추가할 수 없습니다. PowerShell 또는 Azure CLI 2.0을 사용하여 개인 IPv6 주소가 포함된 네트워크 인터페이스를 만든 다음 가상 컴퓨터를 만들 때 해당 네트워크 인터페이스를 연결합니다. 개인 IPv6 주소가 할당된 네트워크 인터페이스를 기존 가상 컴퓨터에 연결할 수는 없습니다. Portal, CLI, PowerShell 등 어떤 도구로도 가상 컴퓨터에 연결된 네트워크 인터페이스의 IP 구성에 개인 IPv6 주소를 추가할 수는 없습니다.

공용 IPv6 주소를 기본 또는 보조 IP 구성에 할당할 수는 없습니다.

## <a name="skus"></a>SKU

공용 IP 주소는 기본 또는 표준 SKU로 생성됩니다.  SKU 차이점에 대한 자세한 내용은 [공용 IP 주소 관리](virtual-network-public-ip-address.md)를 참조하세요.

> [!NOTE]
> 가상 컴퓨터의 네트워크 인터페이스에 표준 SKU 공용 IP 주소를 할당할 때 [네트워크 보안 그룹](security-overview.md#network-security-groups)을 사용하여 원하는 트래픽을 명시적으로 허용해야 합니다. 네트워크 보안 그룹을 만들어 연결하고 원하는 트래픽을 명시적으로 허용해야 리소스와 통신할 수 있습니다.

## <a name="next-steps"></a>다음 단계
다양한 IP 구성으로 가상 컴퓨터를 만들려면 다음 문서를 확인하세요.

|작업|도구|
|---|---|
|여러 NIC를 사용하여 VM 만들기|[CLI](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|여러 IPv4 주소가 있는 단일 NIC VM 만들기|[CLI](virtual-network-multiple-ip-addresses-cli.md), [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|
|Azure Load Balancer 뒤에 개인 IPv6 주소가 있는 단일 NIC VM 만들기|[CLI](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [Azure Resource Manager 템플릿](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
