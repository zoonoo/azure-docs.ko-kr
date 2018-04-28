---
title: Azure Virtual Network 서브넷 추가, 변경 또는 삭제 | Microsoft 문서
description: Azure에서 가상 네트워크 서브넷을 추가, 변경 또는 삭제하는 방법을 알아봅니다.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/09/2018
ms.author: jdial
ms.openlocfilehash: 00b0635d894ff8a41c810345cfa94363029e78e8
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="add-change-or-delete-a-virtual-network-subnet"></a>가상 네트워크 서브넷 추가, 변경 또는 삭제

가상 네트워크 서브넷을 추가, 변경 또는 삭제하는 방법을 알아봅니다. 가상 네트워크에 대해 잘 모르는 경우 서브넷을 추가, 변경 또는 삭제하기 전에 [Azure Virtual Network 개요](virtual-networks-overview.md) 및 [가상 네트워크 만들기, 변경 또는 삭제](manage-virtual-network.md)를 확인하는 것이 좋습니다. 가상 네트워크에 배포된 모든 Azure 리소스는 가상 네트워크 내의 서브넷에 배포됩니다.
 
## <a name="before-you-begin"></a>시작하기 전에

이 문서에서 설명하는 모든 섹션의 단계를 수행하기 전에 다음 작업을 완료해야 합니다.

- 아직 Azure 계정이 없으면 [평가판 계정](https://azure.microsoft.com/free)에 등록합니다.
- 포털을 사용하는 경우 https://portal.azure.com을 열고 Azure 계정으로 로그인합니다.
- 이 문서의 작업을 완료하기 위해 PowerShell 명령을 사용하는 경우 [Azure Cloud Shell](https://shell.azure.com/powershell)에서 명령을 실행하거나 컴퓨터에서 PowerShell을 실행합니다. Azure Cloud Shell은 이 항목의 단계를 실행하는 데 무료로 사용할 수 있는 대화형 셸입니다. 공용 Azure 도구가 사전 설치되어 계정에서 사용하도록 구성되어 있습니다. 이 자습서에는 Azure PowerShell 모듈 버전 5.2.0 이상이 필요합니다. 설치되어 있는 버전을 확인하려면 `Get-Module -ListAvailable AzureRM`을 실행합니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-azurerm-ps)를 참조하세요. 또한 PowerShell을 로컬로 실행하는 경우 `Connect-AzureRmAccount`를 실행하여 Azure와 연결해야 합니다.
- 이 문서의 작업을 완료하기 위해 Azure CLI(명령줄 인터페이스)를 사용하는 경우 [Azure Cloud Shell](https://shell.azure.com/bash)에서 명령을 실행하거나 컴퓨터에서 CLI를 실행합니다. 이 자습서에는 Azure CLI 버전 2.0.26 이상이 필요합니다. 설치되어 있는 버전을 확인하려면 `az --version`을 실행합니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 2.0 설치](/cli/azure/install-azure-cli)를 참조하세요. 또한 Azure CLI를 로컬로 실행하는 경우 `az login`를 실행하여 Azure와 연결해야 합니다.

## <a name="add-a-subnet"></a>서브넷 추가

1. 포털 맨 위에 있는 검색 상자에 *가상 네트워크*를 입력합니다. 검색 결과에 **가상 네트워크**가 표시되면 이를 선택합니다.
2. 가상 네트워크 목록에서 서브넷을 추가할 가상 네트워크를 선택합니다.
3. **설정**에서 **서브넷**을 선택합니다.
4. **+서브넷**을 선택합니다.
5. 다음 매개 변수에 대한 값을 입력합니다.
    - **이름:** 가상 네트워크 내에서 고유해야 합니다.
    - **주소 범위:** 범위는 가상 네트워크의 주소 공간 내에서 고유해야 합니다. 범위는 가상 네트워크 내에서 다른 서브넷 주소 범위와 겹칠 수 없습니다. CIDR(Classless Inter-Domain Routing) 표기법을 사용하여 주소 공간을 지정해야 합니다. 예를 들어 주소 공간이 10.0.0.0/16인 가상 네트워크에서 서브넷 주소 공간을 10.0.0.0/24로 정의할 수 있습니다. 지정할 수 있는 최소 범위는 /29이며, 서브넷에 대해 8개의 IP 주소를 제공합니다. Azure는 프로토콜 준수를 위해 각 서브넷의 첫 번째 및 마지막 주소를 예약합니다. 세 개의 추가 주소가 Azure 서비스를 사용하기 위해 예약되어 있습니다. 결과적으로 주소 범위가 /29인 서브넷을 정의하면 서브넷에 사용할 수 있는 3개의 IP 주소가 만들어집니다. 가상 네트워크를 VPN Gateway에 연결하려면 게이트웨이 서브넷을 만들어야 합니다. [게이트웨이 서브넷에 대한 특정 주소 범위 고려 사항](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub)에서 대해 자세히 알아보세요. 특정 조건에서는 서브넷을 추가한 후에 주소 범위를 변경할 수 있습니다. 서브넷 주소 범위를 변경하는 방법에 대한 자세한 내용은 [서브넷 설정 변경](#change-subnet-settings)을 참조하세요.
    - **네트워크 보안 그룹**: 0개 또는 1개의 기존 네트워크 보안 그룹을 서브넷에 연결하여 서브넷에 대한 인바운드 및 아웃바운드 네트워크 트래픽을 필터링할 수 있습니다. 네트워크 보안 그룹은 가상 네트워크와 동일한 구독 및 위치에 있어야 합니다. [네트워크 보안 그룹](security-overview.md) 및 [네트워크 보안 그룹을 만드는 방법](tutorial-filter-network-traffic.md)에 대해 알아봅니다.
    - **경로 테이블**: 0개 또는 1개의 기존 경로 테이블을 서브넷에 연결하여 다른 네트워크에 대한 네트워크 트래픽 라우팅을 제어할 수 있습니다. 경로 테이블은 가상 네트워크와 동일한 구독 및 위치에 있어야 합니다. [Azure 라우팅](virtual-networks-udr-overview.md) 및 [경로 테이블을 만드는 방법](tutorial-create-route-table-portal.md)에 대해 알아봅니다.
    - **서비스 끝점:** 서브넷은 0개 또는 여러 개의 서비스 끝점을 서비스에 대해 설정할 수 있습니다. 서비스에 대해 서비스 끝점을 사용하려면 **서비스** 목록에서 서비스 끝점을 사용할 서비스를 선택합니다. 서비스 끝점을 제거하려면 서비스 끝점을 제거할 서비스를 선택 취소합니다. 서비스 끝점에 대한 자세한 내용은 [가상 네트워크 서비스 끝점 개요](virtual-network-service-endpoints-overview.md)를 참조하세요. 서비스에 대한 서비스 끝점을 사용하도록 설정하면 서비스를 사용하여 만든 리소스의 서브넷에 대해서도 네트워크 액세스를 사용하도록 설정해야 합니다. 예를 들어 *Microsoft.Storage*에 대해 서비스 끝점을 사용하도록 설정하면 네트워크 액세스 권한을 부여할 모든 Azure Storage 계정에 대해서도 네트워크 액세스를 사용하도록 설정해야 합니다. 서비스 끝점을 사용하도록 설정한 서브넷에 대해 네트워크 액세스를 사용하는 방법에 대한 자세한 내용은 서비스 끝점을 사용하도록 설정한 개별 서비스에 대한 설명서를 참조하세요.
6. 선택한 가상 네트워크에 서브넷을 추가하려면 **확인**을 선택합니다.

**명령**

- Azure CLI: [az network vnet subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create)
- PowerShell: [Add-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/add-azurermvirtualnetworksubnetconfig)

## <a name="change-subnet-settings"></a>서브넷 설정 변경

1. 포털 맨 위에 있는 검색 상자에 *가상 네트워크*를 입력합니다. 검색 결과에 **가상 네트워크**가 표시되면 이를 선택합니다.
2. 가상 네트워크의 목록에서 설정을 변경할 서브넷이 포함된 가상 네트워크를 선택합니다.
3. **설정**에서 **서브넷**을 선택합니다.
4. 서브넷의 목록에서 설정을 변경할 서브넷을 선택합니다. 다음 설정을 변경할 수 있습니다.

    - **주소 범위:** 서브넷 내에 배포된 리소스가 없는 경우 주소 범위를 변경할 수 있습니다. 서브넷에 리소스가 있으면 먼저 다른 서브넷으로 리소스를 이동하거나 서브넷에서 삭제해야 합니다. 리소스 이동 또는 삭제를 수행하는 단계는 리소스에 따라 다릅니다. 서브넷에 있는 리소스를 이동 또는 삭제하는 방법에 대해 알아보려면 이동 또는 삭제하려는 각 리소스 종류의 설명서를 참조하세요. [서브넷 추가](#add-a-subnet)의 5단계에서 **주소 범위**에 대한 제약 조건을 참조하세요.
    - **사용자**: 기본 제공 역할 또는 사용자 지정 역할을 사용하여 서브넷에 대한 액세스를 제어할 수 있습니다. 역할과 사용자를 할당하여 서브넷에 액세스하는 방법에 대한 자세한 내용은 [역할 할당을 사용하여 Azure 리소스에 대한 액세스 관리](../role-based-access-control/role-assignments-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-access)를 참조하세요.
    - **네트워크 보안 그룹**, **경로 테이블**, **사용자** 및 **서비스 끝점**에 대한 자세한 내용은 [서브넷 추가](#add-a-subnet)의 5단계를 참조하세요.
5. **저장**을 선택합니다.

**명령**

- Azure CLI: [az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update)
- PowerShell: [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig)

## <a name="delete-a-subnet"></a>서브넷 삭제

서브넷에 리소스가 없는 경우에만 해당 서브넷을 삭제할 수 있습니다. 서브넷에 리소스가 있으면 서브넷을 삭제하기 전에 먼저 서브넷에 있는 리소스를 삭제해야 합니다. 리소스 삭제를 수행하는 단계는 리소스에 따라 다릅니다. 서브넷에 있는 리소스를 삭제하는 방법에 대해 알아보려면 삭제하려는 각 리소스 종류의 설명서를 참조하세요.

1. 포털 맨 위에 있는 검색 상자에 *가상 네트워크*를 입력합니다. 검색 결과에 **가상 네트워크**가 표시되면 이를 선택합니다.
2. 가상 네트워크 목록에서 삭제할 서브넷이 포함된 가상 네트워크를 선택합니다.
3. **설정**에서 **서브넷**을 선택합니다.
4. 서브넷 목록에서 삭제할 서브넷의 오른쪽에 있는 **...** 를 선택합니다.
5. **삭제**를 선택한 후 **예**를 선택합니다.

**명령**

- Azure CLI: [az network vnet delete](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-delete)
- PowerShell: [Remove-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/remove-azurermvirtualnetworksubnetconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)

## <a name="permissions"></a>권한

서브넷에 대한 작업을 수행하려면 다음 표에 나열된 적절한 권한이 할당된 [네트워크 참여자](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) 역할 또는 [사용자 지정](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 역할에 계정을 할당해야 합니다.

|작업                                                                |   작업 이름                               |
|-----------------------------------------------------------------------  |   -------------------------------------------  |
|Microsoft.Network/virtualNetworks/subnets/read                           |   Virtual Network 서브넷 가져오기                   |
|Microsoft.Network/virtualNetworks/subnets/write                          |   Virtual Network 서브넷 만들기 또는 업데이트      |
|Microsoft.Network/virtualNetworks/subnets/delete                         |   Virtual Network 서브넷 삭제                |
|Microsoft.Network/virtualNetworks/subnets/join/action                    |   Virtual Network 조인                         |
|Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action  |   서브넷에 서비스 조인                     |
|Microsoft.Network/virtualNetworks/subnets/virtualMachines/read           |   Virtual Network 서브넷 Virtual Machines 가져오기  |
