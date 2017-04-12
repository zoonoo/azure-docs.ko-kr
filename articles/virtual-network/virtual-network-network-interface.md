---
title: "Azure 네트워크 인터페이스 | Microsoft Docs"
description: "NIC(네트워크 인터페이스)를 만들고, 삭제하고, 공용 및 개인 IP 주소를 NIC에 할당하는 방법에 대해 알아봅니다. Azure Virtual Machines에서 NIC를 연결하고 분리하는 방법에 대해 알아봅니다."
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
ms.date: 03/14/2017
ms.author: jdial
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: f691f3886fce217ea784237f03a4f02ed58e12ee
ms.lasthandoff: 03/28/2017


---

# <a name="network-interfaces"></a>네트워크 인터페이스

NIC(네트워크 인터페이스) 및 네트워크 인터페이스를 사용하는 방법에 대해 알아봅니다. NIC는 Azure VM(가상 컴퓨터)과 기본 소프트웨어 네트워크 간의 상호 연결입니다. 다음 그림에서는 NIC에서 제공하는 기능을 보여 줍니다.

![네트워크 인터페이스](./media/virtual-network-network-interface/nic.png)

이 문서에서는 그림에서 보여 주는 개념을 적용하는 방법에 대해 설명합니다. 다음 개념 중 하나를 클릭하면 이 문서의 해당 섹션으로 바로 이동합니다.

- [네트워크 인터페이스](#nics): NIC는 VNet(Azure Virtual Network) 내의 한 서브넷에 연결됩니다. 그림에서 **VM1**에는 서브넷에 NIC 2개가 연결되어 있고, **VM2**에는 NIC 1개가 연결되어 있습니다. 각 NIC는 동일한 VNet에 있는 서로 다른 서브넷에 연결됩니다. 이 섹션에서는 기존 NIC를 나열하고, NIC를 생성, 변경 및 삭제하는 단계를 제공합니다.
- [IP 구성](#ip-configs): 하나 이상의 IP 구성이 각 NIC에 연결됩니다. 각 IP 구성마다 개인 IP 주소가 하나씩 할당되며, 연결되는 하나의 공용 IP 주소가 있을 수 있습니다. 그림에서 **NIC1** 및 **NIC3**에는 각각 IP 구성이 하나씩 있고, **NIC2**에는 IP 구성이 두 개 있습니다. NIC1 및 NIC3에 할당된 IP 구성에는 할당된 공용 IP 주소가 있지만, NIC2에 할당된 IP 구성에는 할당된 공용 IP 주소가 없습니다. 이 섹션에서는 고정 및 동적 할당 방법을 통해 할당된 개인 IP 주소로 IP 구성을 생성, 변경 및 삭제하는 단계를 제공합니다. 또한 공용 IP 주소를 IP 구성과 연결하거나 분리하는 단계도 제공합니다.
- [네트워크 보안 그룹](#nsgs): NSG(네트워크 보안 그룹)는 하나 이상의 인바운드 또는 아웃바운드 보안 규칙을 포함합니다. 이 규칙은 네트워크 인터페이스, 서브넷 또는 둘 다에 들어오고 나가는 네트워크 트래픽 유형을 제어합니다. 그림에서 **NIC1** 및 **NIC3**에는 연결된 NSG가 있지만, **NIC2**에는 연결된 NSG가 없습니다. 이 섹션에서는 NIC에 적용된 NSG를 보고, NIC에 NSG를 추가하고, NIC에서 NSG를 제거하는 단계를 제공합니다.
- [가상 컴퓨터](#vms): VM에는 하나 이상의 NIC가 연결되지만, VM 크기에 따라 여러 개의 NIC가 연결될 수 있습니다. 각 VM 크기에서 지원하는 NIC 수를 확인하려면 [Windows](../virtual-machines/virtual-machines-windows-sizes.md) 또는 [Linux](../virtual-machines/virtual-machines-linux-sizes.md) VM 크기 문서를 참조하세요. 이 섹션에서는 단일 및 다중 NIC VM을 만들고 기존 VM과 NIC를 연결하거나 분리하는 단계를 제공합니다.

Azure에서 NIC와 VM을 처음 사용하는 경우 이 문서를 참조하기 전에 [Azure 가상 네트워크 만들기](virtual-network-get-started-vnet-subnet.md)의 연습을 수행하는 것이 좋습니다. 이 연습을 수행하면 VNet과 VM에 익숙해질 수 있습니다.

이 문서는 Azure Resource Manager 배포 모델을 통해 만든 VM과 NIC에 적용됩니다. 클래식 배포 모델보다는 Resource Manager 배포 모델을 통해 리소스를 만드는 것이 좋습니다. 두 모델의 차이점에 대해 자세히 알아보려면 [Azure 배포 모델 이해](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 문서를 참조하세요.

이 문서의 나머지 섹션에서는 모든 NIC 관련 작업을 수행하는 단계를 제공합니다. 각 섹션에서는 다음과 같이 나열합니다.
- Azure Portal에서 작업을 수행하는 단계 - 단계를 수행하려면 [Azure Portal](http://portal.azure.com)에 로그인해야 합니다. 아직 없는 경우 [평가판 계정](https://azure.microsoft.com/free)을 등록합니다.
- 명령에 대한 명령 참조에 연결되는 링크가 있는 Azure PowerShell을 사용하여 작업을 수행하는 명령 - [Azure PowerShell 설치 및 구성 방법](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json) 문서의 단계를 수행하여 PowerShell을 설치하고 구성합니다. 예제와 함께 PowerShell 명령에 대한 도움말을 보려면 `get-help <command> -full`을 입력합니다.
- 명령에 대한 명령 참조에 연결되는 링크가 있는 Azure CLI(명령줄 인터페이스)를 사용하여 작업을 수행하는 명령 - [Azure CLI 2.0 설치 및 구성 방법](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json) 문서의 단계를 수행하여 Azure CLI를 설치합니다. CLI 명령에 대한 도움말을 보려면 `az <command> -h`를 입력합니다.

## <a name="nics"></a>네트워크 인터페이스
네트워크 인터페이스 및 설정을 만들고, 보고, 변경하고, 삭제하려면 다음 섹션의 단계를 수행합니다.

### <a name="create-nic"></a>네트워크 인터페이스 만들기

NIC는 VM에 연결되거나 자체적으로 존재할 수 있습니다. NIC를 VM에 연결하는 방법을 알아보려면 이 문서의 [가상 컴퓨터에 NIC 연결](#vm-attach-nic) 섹션을 참조하세요.

NIC를 만들려면 다음 단계를 수행합니다.

1. 구독에 대한 네트워크 참가자 역할에 권한(최소)이 할당된 계정으로 [Azure Portal](https://portal.azure.com)에 로그인합니다. 계정에 역할 및 권한을 할당하는 방법에 대한 자세한 내용은 [Azure 역할 기반 액세스 제어의 기본 제공 역할](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) 문서를 참조하세요.
2. Azure Portal 위쪽의 *리소스 검색* 텍스트가 있는 상자에서 *네트워크 인터페이스*를 입력합니다. 검색 결과에 표시된 **네트워크 인터페이스**를 클릭합니다.
3. **네트워크 인터페이스** 블레이드가 표시되면 **+ 추가**를 클릭합니다.
4. 표시되는 **네트워크 인터페이스 만들기** 블레이드에서 다음 설정에 대한 값을 입력하거나 선택한 다음 **만들기**를 클릭합니다.

    |**설정**|**필수 여부**|**세부 정보**|
    |---|---|---|
    |**Name**|예|NIC를 만든 후에는 이름을 변경할 수 없습니다. 이름은 선택한 리소스 그룹 내에서 고유해야 합니다. 명명 제안에 대해서는 [명명 규칙](/azure/architecture/best-practices/naming-conventions?toc=%2fazure%2fvirtual-network%2ftoc.json#naming-rules-and-restrictions) 문서를 참조하세요.|
    |**가상 네트워크**|예|NIC와 동일한 구독과 위치에 있는 VNet에만 해당 NIC를 연결할 수 있습니다. 또한 NIC가 연결된 VM도 NIC와 동일한 위치와 구독에 있어야 합니다. VNet이 표시되지 않으면 VNet을 만들어야 합니다. VNet을 만들려면 [가상 네트워크](virtual-networks-create-vnet-arm-pportal.md) 문서의 단계를 수행합니다. NIC가 만들어지면 연결된 VNet을 변경할 수 없습니다.|
    |**서브넷**|예|선택한 VNet에 있는 서브넷을 선택합니다. NIC를 만든 후에는 연결된 서브넷을 변경할 수 있습니다.|
    |**개인 IP 주소 할당**|예| 개인 IP 주소는 NIC를 만들 때 Azure DHCP 서버에서 해당 NIC에 할당합니다. DHCP 서버는 NIC를 연결하는 서브넷에 대해 정의된 서브넷 주소 범위에서 사용 가능한 주소를 할당합니다. **동적:** 연결된 VM을 중지(할당 취소)된 상태에서 시작하면 Azure에서 NIC에 다른 주소를 할당할 수 있습니다. VM을 중지(할당 취소)되지 않은 상태에서 시작하는 경우에는 주소가 동일하게 유지됩니다. **고정:** 고정 주소는 변경하거나 NIC를 삭제할 때까지 변경되지 않습니다. NIC를 만든 후에는 할당 방법을 변경할 수 있습니다.|
    |**네트워크 보안 그룹**|아니요|네트워크 보안 그룹을 사용하면 NIC 내/외부 네트워크 트래픽의 흐름을 제어할 수 있습니다. NSG에 대한 자세한 내용은 [네트워크 보안 그룹](virtual-networks-nsg.md)을 참조하세요. NIC에 NSG(네트워크 보안 그룹)를 적용하지 않거나 하나를 적용할 수 있습니다. 또한 NIC가 연결된 서브넷에도 마찬가지입니다. NSG를 NIC 및 연결된 서브넷에 적용할 때 때로는 예기치 않은 결과가 발생할 수 있습니다. NIC에 적용되는 NSG 문제를 해결하려면 [NSG 문제 해결](virtual-network-nsg-troubleshoot-portal.md#view-effective-security-rules-for-a-network-interface) 문서를 참조하세요.|
    |**구독**|예| NIC를 연결하는 VM과 VNet은 동일한 구독에 있어야 합니다.|
    |**리소스 그룹**|예| NIC는 연결되는 VM 또는 VNet과 동일하거나 다른 리소스 그룹에 있을 수 있습니다.|
    |**위치**|예|NIC를 연결하는 VM과 VNet은 같은 위치에 있어야 합니다.|

Azure Portal은 동적 개인 IP 주소가 있는 **ipconfig1**이라는 기본 IP 구성을 만들고, 이 구성을 사용자가 만든 NIC에 연결합니다. IP 구성에 대한 자세한 내용은 이 문서의 [IP 구성](#ip-configs) 섹션을 참조하세요. 포털에서 만든 IP 구성의 이름을 지정하거나, 고정 개인 IP 주소를 할당하거나, NIC를 만들 때 공용 IP 주소를 할당할 수 없습니다. PowerShell 또는 CLI를 사용하여 NIC를 만드는 경우에는 IP 구성의 이름, 고정 IP 주소를 지정하고 공용 IP 주소를 할당할 수 있습니다. 개인 IP 주소 할당 방법과 NIC를 만든 후 공용 IP 주소가 NIC에 연결되는지 여부를 변경할 수 있습니다. NIC를 만든 후 설정을 변경하려면 이 문서에 있는 [IP 구성 변경](#change-ip-config) 섹션의 단계를 수행합니다.

>[!Note]
> NIC를 VM에 연결하고 VM을 처음 시작한 후에만 Azure에서 NIC에 MAC 주소를 할당합니다. Azure에서 NIC에 할당한 MAC 주소는 지정할 수 없습니다. NIC를 삭제하거나 기본 NIC의 기본 IP 구성에 할당된 개인 IP 주소를 변경할 때까지 MAC 주소는 NIC에 할당된 상태로 유지됩니다. IP 구성에 대한 자세한 내용은 이 문서의 [IP 구성](#ip-configs) 섹션을 참조하세요.

|**도구**|**명령**|
|:---|:---|
|**CLI**|[az network nic create](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|**PowerShell**|[새-AzureRmNetworkInterface](/powershell/resourcemanager/azurerm.network/v3.4.0/new-azurermnetworkinterface/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|

### <a name="view-nics"></a>네트워크 인터페이스와 설정 보기 및 변경

네트워크 인터페이스와 설정을 보고 변경하려면 다음 단계를 수행합니다.

1. 구독에 대한 네트워크 참가자 역할에 권한(최소)이 할당된 계정으로 [Azure Portal](https://portal.azure.com)에 로그인합니다. 계정에 역할 및 권한을 할당하는 방법에 대한 자세한 내용은 [Azure 역할 기반 액세스 제어의 기본 제공 역할](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) 문서를 참조하세요.
2. Azure Portal 위쪽의 *리소스 검색* 텍스트가 있는 상자에서 *네트워크 인터페이스*를 입력합니다. 검색 결과에 표시된 **네트워크 인터페이스**를 클릭합니다.
3. 표시되는 **네트워크 인터페이스** 블레이드에서 설정을 보거나 변경할 NIC를 클릭합니다.
4. 선택한 NIC에 대해 표시되는 블레이드에서 다음 설정이 나열됩니다.
    - **개요:** NIC에 할당된 IP 주소, NIC가 연결된 VNet/서브넷 및 NIC가 연결된 VM(연결되어 있는 경우)과 같은 NIC 관련 정보를 제공합니다. 다음 그림에서는 이름이 **mywebserver256** 이름의 NIC에 대한 개요 설정을 보여 줍니다.   ![네트워크 인터페이스 개요](./media/virtual-network-network-interface/nic-overview.png)
    - **IP 구성:** NIC에는 하나 이상의 IP 구성이 할당되지만, 여러 IP 구성이 할당되어 있을 수 있습니다. NIC에 지원되는 최대 IP 구성 수에 대한 자세한 내용은 [Azure 제한](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) 문서를 참조하세요. 각 IP 구성에는 하나의 할당된 개인 IP 주소가 있으며, 하나의 공용 IP 주소도 있을 수 있습니다. 표시되는 항목을 수정하려면 이 문서에 있는 [NIC에 보조 IP 구성 추가](#create-ip-config), [IP 구성 변경](#change-ip-config) 또는 [IP 구성 삭제](#delete-ip-config) 섹션의 단계를 수행합니다.
    - **DNS 서버:** Azure DHCP 서버에서 NIC를 할당하는 DNS 서버를 지정할 수 있습니다. Azure 내부 DNS 서버 또는 사용자 지정 DNS 서버 중에서 선택합니다. 표시되는 항목을 수정하려면 이 문서에 있는 [NIC에 대한 DNS 설정 변경](#dns) 섹션의 단계를 수행합니다.
    - **NSG(네트워크 보안 그룹):** NSG가 NIC에 연결되어 있는지 여부를 표시합니다. NSG가 NIC에 연결되어 있으면 연결된 NSG의 이름이 표시됩니다. 표시된 항목을 수정하려면 이 문서에 있는 [NSG와 네트워크 인터페이스 연결 또는 분리](#associate-nsg) 섹션의 단계를 수행합니다.
    - **속성:** NIC에 대해 해당 MAC 주소 및 구독을 포함한 주요 설정을 표시합니다. NIC와 관련된 모든 리소스를 이동하는 경우에도 다른 리소스 그룹이나 구독으로 NIC를 이동할 수 있습니다. 예를 들어 NIC가 VM에 연결되어 있는 경우 VM 및 관련된 모든 추가 리소스도 이동해야 합니다. NIC를 이동하려면 [새 리소스 그룹 또는 구독으로 리소스이동](../azure-resource-manager/resource-group-move-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json#use-portal) 문서를 참조하세요. 필수 구성 요소와 Azure Portal, PowerShell 및 Azure CLI를 사용하여 리소스를 이동하는 방법을 나열하고 있습니다.
    - **효과적인 보안 규칙:** NIC가 실행 중인 VM에 연결되어 있고 NSG가 NIC, 연결된 서브넷 또는 둘 다에 연결되어 있으면 보안 규칙이 나열됩니다. 표시되는 항목에 대한 자세한 내용은 [네트워크 보안 그룹 문제 해결](virtual-network-nsg-troubleshoot-portal.md#view-effective-security-rules-for-a-network-interface) 문서를 참조하세요. NSG에 대한 자세한 내용은 [네트워크 보안 그룹](virtual-networks-nsg.md)을 참조하세요.
    - **유효 경로:** NIC가 실행 중인 VM에 연결되어 있으면 경로가 나열됩니다. 경로는 Azure 기본 경로, 사용자 정의 경로 및 NIC가 연결된 서브넷에 있을 수 있는 BGP 경로의 조합입니다. 표시되는 항목에 대한 자세한 내용은 [경로 문제 해결](virtual-network-routes-troubleshoot-portal.md#view-effective-routes-for-a-network-interface) 문서를 참조하세요. 사용자 정의 경로에 대해 자세히 알아보려면 [사용자 정의 경로](virtual-networks-udr-overview.md) 문서를 참조하세요.
    - **일반적인 Azure Resource Manager 설정:** 일반적인 Azure Resource Manager 설정에 대한 자세한 내용은 [활동 로그](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#activity-logs), [액세스 제어(IAM)](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#access-control), [태그](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#tags), [잠금](../azure-resource-manager/resource-group-lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 및 [자동화 스크립트](../azure-resource-manager/resource-manager-export-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json#export-the-template-from-resource-group) 문서를 참조하세요.

|**도구**|**명령**|
|---|---|
|**CLI**|[az network nic list](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#list)는 구독에 있는 NIC를 보여 주고, [az network nic show](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#show)는 NIC 설정을 보여 줍니다.|
|**PowerShell**|[Get-AzureRmNetworkInterface](/powershell/resourcemanager/azurerm.network/v3.4.0/get-azurermnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)는 NIC에 대한 구독 또는 설정을 보여 줍니다.|

### <a name="dns"></a>NIC에 대한 DNS 설정 변경

NIC에 대한 DNS 설정을 변경하려면 다음 단계를 수행합니다. Azure DHCP 서버에서 VM에 DNS 서버를 할당합니다. NIC에 대한 이름 확인 설정에 대한 자세한 내용은 [VM에 대한 이름 확인](virtual-networks-name-resolution-for-vms-and-role-instances.md) 문서를 참조하세요.

1. 설정을 변경하려는 NIC에 대해 이 문서에 있는 [네트워크 인터페이스와 설정 보기 및 변경](#view-nics) 섹션의 1-3단계를 수행합니다.
2. 선택한 NIC의 블레이드에서 **DNS 서버**를 클릭합니다.
3. 다음 중 하나를 클릭합니다:
    - **가상 네트워크에서 상속(기본값)**: 이 옵션을 선택하면 NIC가 연결된 가상 네트워크에 대해 정의된 DNS 서버 설정을 상속합니다. VNet 수준에서 사용자 지정 DNS 서버 또는 Azure 제공 DNS 서버가 정의됩니다. Azure 제공 DNS 서버는 동일한 VNet에 연결된 리소스의 이름을 확인할 수 있지만, 다른 VNet에 연결된 리소스의 이름은 확인할 수 없습니다.
    - **사용자 지정**: 여러 VNet에서 이름을 확인하도록 사용자 고유의 DNS 서버를 구성할 수 있습니다. DNS 서버로 사용하려는 서버의 IP 주소를 입력합니다. 지정한 DNS 서버 주소는 이 NIC에만 할당되며, 해당 NIC가 연결된 VNet에 대한 모든 DNS 설정을 재정의합니다.
4. **Save**를 클릭합니다.

|**도구**|**명령**|
|---|---|
|**CLI**|[az network nic update](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|**PowerShell**|[Set-AzureRmNetworkInterface](/powershell/resourcemanager/azurerm.network/v3.4.0/set-azurermnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|

### <a name="ip-forwarding"></a>NIC에 대한 IP 전달 변경

IP 전달은 NIC가 연결된 VM을 활성화합니다.
- NIC에 할당된 IP 구성 중 하나에 할당된 IP 주소 중 하나를 대상으로 하지 않는 네트워크 트래픽을 받습니다.
- IP 구성 중 하나에 할당된 것과 다른 원본 IP 주소로 네트워크 트래픽을 보냅니다.

VM이 전달해야 하는 트래픽을 받는 VM에 연결된 모든 NIC에서 이 설정이 활성화되어야 합니다. NIC가 여러 개 또는 하나 중 어떻게 연결되어 있든 VM은 트래픽을 전달할 수 있습니다. IP 전달은 Azure 설정이지만, VM은 방화벽, WAN 최적화 및 부하 분산 응용 프로그램과 같이 트래픽을 전달할 수 있는 응용 프로그램도 실행해야 합니다. VM에서 네트워크 응용 프로그램을 실행하는 경우 이 VM을 종종 NVA(네트워크 가상 어플라이언스)라고 합니다. [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances)에서 NVA를 배포할 준비가 된 목록을 볼 수 있습니다. IP 전달은 일반적으로 사용자 정의 경로와 함께 사용됩니다. 사용자 정의 경로에 대해 자세히 알아보려면 [사용자 정의 경로](virtual-networks-udr-overview.md) 문서를 참조하세요.

NIC에 대한 IP 전달을 변경하려면 다음 단계를 수행합니다.

1. 수정하려는 NIC에 대해 이 문서에 있는 [네트워크 인터페이스와 설정 보기 및 변경](#view-nics) 섹션의 1-3단계를 수행합니다.
2. 선택한 NIC의 블레이드에서 IP 구성을 클릭합니다.
3. **사용** 또는 **사용 안 함**(기본 설정)을 클릭하여 설정을 변경합니다.
4. **Save**를 클릭합니다.

|**도구**|**명령**|
|---|---|
|**CLI**|[az network nic update](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|**PowerShell**|[Set-AzureRmNetworkInterface](/powershell/resourcemanager/azurerm.network/v3.4.0/set-azurermnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|

### <a name="subnet"></a>NIC가 연결된 서브넷 변경

NIC가 연결되고 Vnet이 아닌 서브넷을 변경할 수 있습니다. 서브넷을 변경하려면 NIC에 연결된 모든 IP 구성에 동적 개인 IP 주소를 할당해야 합니다. NIC가 연결된 서브넷을 변경하려면 다음 단계를 수행합니다.

1. 다른 서브넷에 연결하려는 NIC에 대해 이 문서에 있는 [네트워크 인터페이스와 설정 보기 및 변경](#view-nics) 섹션의 1-3단계를 수행합니다.
2. 선택한 NIC의 블레이드에서 **IP 구성**을 클릭합니다. 나열된 모든 IP 구성의 개인 IP 주소에 고정 방법으로 할당된 개인 IP 주소가 있는 경우 다음 단계를 수행하여 방법을 동적으로 변경해야 합니다. 주소가 동적 방법으로 할당된 경우 다음 세 단계로 계속합니다.
    - IP 구성 목록에서 변경하려는 IP 구성의 고정 IP 주소를 클릭합니다.
    - IP 구성에 대해 표시되는 블레이드에서 **할당** 방법으로 **동적**을 클릭합니다.
    - **Save**를 클릭합니다.
3. **서브넷** 드롭다운 목록에서 NIC를 연결하려는 서브넷을 선택합니다.
4. **Save**를 클릭합니다. 새 동적 주소는 새 서브넷 주소 범위에서 할당됩니다. 선택하면 새 서브넷 주소 범위에서 고정 IP 주소를 할당할 수 있습니다.

|**도구**|**명령**|
|---|---|
|**CLI**|[az network nic ip-config update](/cli/azure/network/nic/ip-config?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|**PowerShell**|[Set-AzureRmNetworkInterfaceIpConfig](/powershell/resourcemanager/azurerm.network/v3.4.0/set-azurermnetworkinterfaceipconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|


### <a name="delete-nic"></a>네트워크 인터페이스 삭제

NIC가 VM에 연결되어 있지 않으면 삭제할 수 있습니다. VM에 연결되어 있는 경우 VM을 삭제하기 전에 먼저 해당 VM에서 분리해야 합니다. VM에서 NIC를 분리하려면 이 문서에 있는 [가상 컴퓨터에서 NIC 분리](#vm-detach-nic) 섹션의 단계를 수행합니다.

1. 삭제하려는 NIC에 대해 이 문서에 있는 [네트워크 인터페이스와 설정 보기 및 변경](#view-nics) 섹션의 1-2단계를 수행합니다.
2. 삭제하려는 NIC를 마우스 오른쪽 단추로 클릭하고 **삭제**를 클릭합니다.
3. **예**를 클릭하여 NIC 삭제를 확인합니다.

NIC를 삭제하면 할당된 모든 MAC 또는 IP 주소가 해제됩니다.

|**도구**|**명령**|
|---|---|
|**CLI**|[az network nic delete](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#delete)|
|**PowerShell**|[Remove-AzureRmNetworkInterface](/powershell/resourcemanager/azurerm.network/v3.1.0/remove-azurermnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="ip-configs"></a>IP 구성
각 NIC에는 **기본** 구성이라고 하는 하나 이상의 IP 구성이 있습니다. 또한 NIC에는 하나 이상의 *보조* IP 구성도 있을 수 있습니다. NIC에 할당할 수 있는 IP 주소의 수는 제한됩니다. 자세한 내용은 [Azure 제한](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) 문서를 참조하세요. 각 IP 구성은 다음과 같습니다.
- 고정 또는 동적 할당 방법을 사용하여 하나의 개인 IP 주소가 할당됩니다. VM을 중지(할당 취소)된 상태에서 시작하는 경우 동적 IP 주소가 변경될 수 있습니다. 고정 IP 주소는 NIC를 삭제한 경우에만 해당 NIC에서 해제됩니다.
- 연결되는 공용 IP 주소는 하나만 있을 수 있습니다.

Azure DHCP 서버는 NIC의 기본 IP 구성에 대한 개인 IP 주소를 VM 운영 체제 내의 NIC에 할당합니다.

NIC에 여러 IP 주소를 할당하면 다음과 같은 시나리오에서 유용합니다.
- 단일 서버에서 IP 주소와 SSL 인증서를 사용하여 여러 웹 사이트 또는 서비스를 호스트할 수 있습니다.
- VM이 방화벽 또는 부하 분산 장치와 같은 네트워크 가상 어플라이언스로 사용됩니다.
- NIC의 개인 IP 주소를 Azure Load Balancer 백 엔드 풀에 추가할 수 있습니다. 이전에 기본 NIC의 기본 IP 주소만 백 엔드 풀에 추가할 수 있었습니다. 여러 IP 구성의 부하를 분산하는 방법에 대해 자세히 알아보려면 [여러 IP 구성의 부하 분산](../load-balancer/load-balancer-multiple-ip.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 문서를 참조하세요.

구독 내에서 사용할 수 있는 공용 IP 주소의 수가 제한되며, NIC에 할당할 수 있는 개인 IP 주소의 수도 제한됩니다. 이러한 제한에 대한 자세한 내용은 [Azure 제한](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) 문서를 참조하세요.

### <a name="create-ip-config"></a>NIC에 보조 IP 구성 추가

[Azure 제한](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) 문서에 나열된 제한 내에서 필요한 만큼 많은 IP 구성을 NIC에 추가할 수 있습니다. NIC에 IP 구성을 추가하려면 다음 단계를 수행합니다.

1. IP 구성을 추가하려는 NIC에 대해 이 문서에 있는 [네트워크 인터페이스 설정 보기](#view-nics) 섹션의 1-3단계를 수행합니다.
2. 선택한 NIC의 블레이드에서 **IP 구성**을 클릭합니다.
3. IP 구성에 대해 열리는 블레이드에서 **+ 추가**를 클릭합니다.
4. 다음을 지정한 다음 **확인**을 클릭하여 **IP 구성 추가** 블레이드를 닫습니다.

    |**설정**|**필수 여부**|**세부 정보**|
    |---|---|---|
    |**Name**|예|NIC마다 고유해야 합니다.|
    |**형식**|예|기존 NIC에 IP 구성을 추가하고 각 NIC에 기본 IP 구성이 있어야 하므로 유일한 옵션은 **보조**입니다.|
    |**개인 IP 주소 할당 방법**|예|VM을 중지(할당 취소)된 상태에서 다시 시작하면 **동적** 주소가 변경될 수 있습니다. NIC를 삭제할 때까지는 **고정** 주소가 해제되지 않습니다. 다른 IP 구성에서 현재 사용하지 않는 서브넷 주소 공간 범위에서 IP 주소를 지정합니다.|
    |**공용 IP 주소**|아니요|**사용 안 함:** 현재 공용 IP 주소 리소스는 IP 구성과 연결되지 않습니다. **사용:** 기존 공용 IP 주소를 선택하거나 새 공용 IP 주소를 만듭니다. 공용 IP 주소를 만드는 방법에 대한 자세한 내용은 [공용 IP 주소](virtual-network-public-ip-address.md#create) 문서를 참조하세요.|
5. [가상 컴퓨터에 여러 IP 주소 할당](virtual-network-multiple-ip-addresses-portal.md#os-config) 문서의 지침을 수행하여 VM 운영 체제에 보조 개인 IP 주소를 수동으로 추가합니다. VM 운영 체제에 공용 IP 주소를 추가하지 마세요.

|**도구**|**명령**|
|---|---|
|**CLI**|[az network nic ip-config create](/cli/azure/network/nic/ip-config?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|**PowerShell**|[Add-AzureRmNetworkInterfaceIpConfig](/powershell/resourcemanager/azurerm.network/v3.4.0/add-azurermnetworkinterfaceipconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|

### <a name="change-ip-config"></a>IP 구성 변경

기본 또는 보조 IP 구성에 대한 개인 및 공용 IP 주소 설정을 변경하려면 다음 단계를 수행합니다.

1. 수정하려는 NIC에 대해 이 문서에 있는 [네트워크 인터페이스 설정 보기](#view-nics) 섹션의 1-3단계를 수행합니다.
2. 선택한 NIC의 블레이드에서 **IP 구성**을 클릭합니다.
3. IP 구성에 대해 열리는 블레이드 목록에서 수정하려는 IP 구성을 클릭합니다.
4. 이 문서에 있는 [IP 구성 추가](#create-ip-config) 섹션의 설정 관련 정보를 사용하여 원하는 대로 설정을 변경한 다음, **저장**을 클릭하여 선택한 IP 구성의 블레이드를 닫습니다.

>[!NOTE]
>기본 NIC에 여러 IP 구성이 있고 기본 IP 구성의 개인 IP 주소를 변경한 경우, Windows 내에서 모든 보조 IP 주소를 NIC에 수동으로 다시 할당해야 합니다(Linux에서는 필요 없음). 운영 체제 내에서 IP 주소를 NIC에 수동으로 할당하려면 [가상 컴퓨터에 여러 IP 주소 할당](virtual-network-multiple-ip-addresses-portal.md#os-config) 문서를 참조하세요. VM 운영 체제에 공용 IP 주소를 추가하지 마세요.

|**도구**|**명령**|
|---|---|
|**CLI**|[az network nic ip-config update](/cli/azure/network/nic/ip-config?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|**PowerShell**|[Set-AzureRMNetworkInterfaceIpConfig](/powershell/resourcemanager/azurerm.network/v3.4.0/set-azurermnetworkinterfaceipconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|

### <a name="delete-ip-config"></a>NIC에서 보조 IP 구성 삭제

NIC에서 보조 IP 구성을 삭제하려면 다음 단계를 수행합니다.

1. 수정하려는 NIC에 대해 이 문서에 있는 [네트워크 인터페이스 설정 보기](#view-nics) 섹션의 1-3단계를 수행합니다.
2. 선택한 NIC의 블레이드에서 **IP 구성**을 클릭합니다.
3. 삭제하려는 보조 IP 구성을 마우스 오른쪽 단추로 클릭하고 **삭제**를 클릭합니다. 구성에 연결된 공용 IP 주소 리소스가 있는 경우 해당 리소스는 IP 구성과 분리되지만 삭제되지는 않습니다.
4. **IP 구성** 블레이드를 닫습니다.

|**도구**|**명령**|
|---|---|
|**CLI**|[az network nic ip-config delete](/cli/azure/network/nic/ip-config?toc=%2fazure%2fvirtual-network%2ftoc.json#delete)|
|**PowerShell**|[Remove-AzureRmNetworkInterfaceIpConfig](/powershell/resourcemanager/azurerm.network/v3.4.0/remove-azurermnetworkinterfaceipconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|


## <a name="nsgs"></a>네트워크 보안 그룹
NSG(네트워크 보안 그룹)에는 NIC에 대해 네트워크 트래픽을 허용하거나 거부하는 인바운드 및 아웃바운드 규칙 목록이 포함되어 있습니다. NIC 및 NIC가 연결된 서브넷에는 NSG가 연결될 필요가 없습니다. NSG는 NIC, NIC가 연결된 서브넷 또는 둘 다에 연결될 수 있습니다. 서브넷에 연결된 모든 NIC에 적용할 수 있는 규칙이 있는 NSG는 일반적으로 서브넷과 연결됩니다. 그런 다음 더 세부적인 규칙이 있는 NSG를 개별 NIC에 적용할 수 있습니다. 네트워크 보안 그룹에 대한 자세한 내용은 [네트워크 보안 그룹](virtual-networks-nsg.md) 문서를 참조하세요.

### <a name="associate-nsg"></a>NSG와 네트워크 인터페이스 연결 또는 분리

NIC에 NSG를 연결하거나 NIC에서 NSG를 분리하려면 다음 단계를 수행합니다.

1. NSG를 연결하거나 분리하려는 NIC에 대해 이 문서에 있는 [네트워크 인터페이스와 설정 보기 및 변경](#view-nics) 섹션의 1-3단계를 수행합니다.
2. 선택한 NIC의 블레이드에서 **네트워크 보안 그룹**을 클릭합니다. 블레이드는 위쪽의 **편집**과 함께 표시됩니다. NSG가 현재 NIC에 연결되어 있지 않으면 **네트워크 보안 그룹** *없음*이 표시됩니다. NSG가 현재 NIC에 연결되어 있으면 **네트워크 보안 그룹** *NSG-Name*(여기서 NSG-Name은 NIC에 현재 연결된 NSG의 이름임)이 표시됩니다.
3. **편집**을 클릭합니다.
4. **네트워크 보안 그룹**을 클릭합니다. 나열된 네트워크 보안 그룹이 없는 경우 네트워크 보안 그룹이 구독에 없기 때문입니다. NSG를 만들려면 [네트워크 보안 그룹](virtual-networks-create-nsg-arm-pportal.md) 문서의 단계를 수행합니다.
5. 표시되는 **네트워크 보안 그룹 선택** 블레이드에서 목록에서 기존 NSG를 클릭하여 NIC에 해당 NSG를 연결하거나 **없음**을 클릭하여 현재 NIC에 연결된 NSG를 분리합니다.
6. **Save**를 클릭합니다.

|**도구**|**명령**|
|---|---|
|**CLI**|[az network nic update](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|**PowerShell**|[Set-AzureRmNetworkInterface](/powershell/resourcemanager/azurerm.network/v3.4.0/set-azurermnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="vms"></a>NIC를 가상 컴퓨터에서 연결 및 분리

VM을 만들 때 기존 NIC를 해당 VM에 연결하거나 기존 NIC를 기존 VM에 연결할 수 있습니다. 또한 NIC가 둘 이상 있는 기존 VM에서 NIC를 분리할 수도 있습니다. 포털에서 VM을 만들 때 NIC를 만들지만 다음 작업은 허용되지 않습니다.

- VM을 만들 때 연결할 기존 NIC 지정
- 여러 NIC가 연결되는 VM 만들기
- NIC 이름 지정(포털에서 NIC를 기본 이름으로 만듦)
- 개인 IP 주소 할당 방법을 고정으로 지정 - 포털에서는 자동으로 동적 개인 IP 주소를 할당하지만, 포털에서 NIC를 만든 후에 할당 방법을 변경할 수 있습니다.

PowerShell 또는 CLI를 사용하여 포털을 사용할 수 없는 모든 이전 특성을 통해 NIC 또는 VM을 만들 수 있습니다. 다음 섹션의 작업을 수행하기 전에 다음 제약 조건과 동작을 고려하세요.

- VM 크기마다 다른 NIC 수를 지원합니다. 각 VM 크기에서 지원하는 NIC 수에 대한 자세한 내용은 [Linux](../virtual-machines/virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 또는 [Windows](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) VM 크기 문서를 참조하세요. 
- 기본적으로 VM에 연결된 첫 번째 NIC가 *기본* NIC로 정의됩니다. VM에 연결되는 다른 모든 NIC는 *보조* NIC입니다.
- 기본적으로 VM의 모든 아웃바운드 트래픽은 기본 NIC의 기본 IP 구성에 할당된 IP 주소로 보내집니다. 물론, VM 운영 체제 내에서 아웃바운드 트래픽에 사용되는 IP 주소를 제어할 수 있습니다.
- 과거에는 동일한 가용성 집합 내의 모든 VM에 단일 또는 다중 NIC가 있어야 했습니다. NIC가 여러 개 있는 VM은 이제 동일한 가용성 집합에 있을 수 있습니다. VM을 만들 때만 해당 VM을 가용성 집합에 추가할 수 있습니다. 가용성 집합에 대한 자세한 내용은 [Azure에서 Windows 가상 컴퓨터의 가용성 관리](../virtual-machines/virtual-machines-windows-manage-availability.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) 문서를 참조하세요.
- 동일한 VM에 연결된 NIC는 VNet 내의 다른 서브넷에 연결할 수 있지만, 해당 NIC는 모두 동일한 VNet에 연결되어야 합니다.
- 기본 또는 보조 NIC의 IP 구성에 대한 IP 주소를 Azure Load Balancer 백 엔드 풀에 추가할 수 있습니다. 이전에 기본 NIC의 기본 IP 주소만 백 엔드 풀에 추가할 수 있었습니다.
- VM을 삭제해도 연결된 NIC는 삭제되지 않습니다. VM이 삭제되면 VM에서 NIC가 분리됩니다. NIC를 다른 VM에 연결하거나 삭제할 수 있습니다.

### <a name="vm-create"></a>가상 컴퓨터를 만들 때 하나 이상의 NIC 연결

Azure Portal을 사용하여 기존 NIC를 새 VM에 연결하거나 여러 NIC가 있는 VM을 만들 수 없습니다. 다음 Azure CLI 또는 PowerShell 명령을 사용하여 VM을 만들 때 하나 이상의 기존 NIC를 연결할 수 있습니다.

- **CLI:** [az vm create](/cli/azure/vm?toc=%2fazure%2fvirtual-network%2ftoc.json#create)
- **PowerShell:** [New-AzureRmVM](/powershell/resourcemanager/azurerm.compute/v2.5.0/new-azurermvm?toc=%2fazure%2fvirtual-network%2ftoc.json)

### <a name="vm-view-nic"></a>가상 컴퓨터에 연결된 NIC 보기

1. 구독에 대한 소유자, 참가자 또는 네트워크 참가자 역할이 할당된 계정으로 [Azure Portal](https://portal.azure.com)에 로그인합니다. 계정에 역할을 할당하는 방법에 대한 자세한 내용은 [Azure 역할 기반 액세스 제어의 기본 제공 역할](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) 문서를 참조하세요.
2. Azure Portal 위쪽의 *리소스 검색* 텍스트가 있는 상자에서 *가상 컴퓨터*를 입력합니다. 검색 결과에 표시된 **가상 컴퓨터**를 클릭합니다.
3. 표시되는 **가상 컴퓨터** 블레이드에서 연결된 네트워크 인터페이스를 보려는 VM의 이름을 클릭합니다.
4. 선택한 VM에 대해 표시되는 **가상 컴퓨터** 블레이드에서 **네트워크 인터페이스**를 클릭합니다.

|**도구**|**명령**|
|---|---|
|**CLI**|[az vm show](/cli/azure/vm?toc=%2fazure%2fvirtual-network%2ftoc.json#show)|
|**PowerShell**|[Get AzureRmVM](/powershell/resourcemanager/azurerm.compute/v1.3.4/get-azurermvm?toc=%2fazure%2fvirtual-network%2ftoc.json)|

### <a name="vm-attach-nic"></a>기존 가상 컴퓨터에 NIC 연결

NIC를 연결하려는 VM은 여러 NIC를 지원하고 중지(할당 취소)된 상태여야 합니다. Azure Portal에서는 기존 VM에 NIC를 연결할 수 없습니다. 다음 Azure CLI 또는 PowerShell 명령을 사용하여 VM에 NIC를 연결할 수 있습니다.

- **CLI:** [az vm nic add](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#add)
- **PowerShell:** [Add-AzureRmVMNetworkInterface](/powershell/resourcemanager/azurerm.compute/v2.5.0/add-azurermvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)

### <a name="vm-detach-nic"></a>기존 가상 컴퓨터에서 NIC 분리

NIC를 분리하려는 VM은 중지(할당 취소)된 상태여야 하며, 현재 연결된 NIC가 둘 이상 있어야 합니다. 모든 NIC는 분리할 수 있지만, VM에는 항상 NIC가 하나 이상 연결되어 있어야 합니다. 기본 NIC를 분리하는 경우 Azure에서는 연결된 나머지 NIC 중에서 가장 오랫동안 VM에 연결된 NIC에 기본 특성을 할당합니다. 또한 NIC를 기본 NIC로 직접 지정할 수도 있습니다. Azure Portal을 사용하여 VM에서 NIC를 분리하거나 NIC의 기본 특성을 설정할 수는 없지만, CLI 또는 PowerShell을 사용하여 두 작업을 모두 수행할 수 있습니다. 다음 Azure CLI 또는 PowerShell 명령을 사용하여 VM에서 NIC를 분리할 수 있습니다.

- **CLI:** [az vm nic remove](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#remove)
- **PowerShell:** [Remove-AzureRMVMNetworkInterface](/powershell/resourcemanager/azurerm.compute/v2.5.0/remove-azurermvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)

## <a name="next-steps"></a>다음 단계
스크립트를 사용하여 여러 NIC 또는 IP 구성으로 VM을 만들려면 다음 문서를 참조하세요.

|**Task**|**도구**|
|---|---|
|**여러 NIC를 사용하여 VM 만들기**|[CLI](virtual-network-deploy-multinic-arm-cli.md) 및 [PowerShell](virtual-network-deploy-multinic-arm-ps.md)|
|**여러 개의 IP 주소가 할당된 단일 NIC VM 만들기**|[CLI](virtual-network-multiple-ip-addresses-cli.md) 및 [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|

