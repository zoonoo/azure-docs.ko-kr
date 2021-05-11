---
title: Azure 경로 테이블 만들기, 변경 또는 삭제
titlesuffix: Azure Virtual Network
description: 가상 네트워크 트래픽 라우팅에 대한 정보를 찾을 수 있는 위치 및 경로 테이블을 만들거나 변경하거나 삭제하는 방법을 알아봅니다.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/19/2020
ms.author: kumud
ms.openlocfilehash: 642a9a9f798492d85ee2a9784a1fe5ad4f854d58
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100574121"
---
# <a name="create-change-or-delete-a-route-table"></a>경로 테이블 만들기, 변경 또는 삭제

Azure는 Azure 서브넷, 가상 네트워크 및 온-프레미스 네트워크 간에 트래픽을 자동으로 라우트합니다. Azure의 기본 라우팅 중 하나를 변경하려면 경로 테이블을 만듭니다. 가상 네트워크의 라우팅에 익숙하지 않은 경우 [가상 네트워크 트래픽 라우팅](virtual-networks-udr-overview.md)을 참조하거나 [자습서](tutorial-create-route-table-portal.md)를 완료하여 자세히 알아볼 수 있습니다.

## <a name="before-you-begin"></a>시작하기 전에

구독이 활성화된 Azure 계정이 없는 경우 계정을 설정합니다. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). 이 문서의 섹션에서 단계를 시작하기 전에 다음 작업 중 하나를 완료합니다.

- **포털 사용자**: Azure 계정으로 [Azure Portal](https://portal.azure.com)에 로그인합니다.

- **PowerShell 사용자**: [Azure Cloud Shell](https://shell.azure.com/powershell)에서 명령을 실행하거나 사용자의 컴퓨터에서 PowerShell을 실행합니다. Azure Cloud Shell은 이 항목의 단계를 실행하는 데 무료로 사용할 수 있는 대화형 셸입니다. 공용 Azure 도구가 사전 설치되어 계정에서 사용하도록 구성되어 있습니다. Azure Cloud Shell 브라우저 탭에서 **환경 선택** 드롭다운 목록을 찾은 다음 **PowerShell** 을 선택합니다(아직 선택하지 않은 경우).

    PowerShell을 로컬로 실행하는 경우 Azure PowerShell 모듈 버전 1.0.0 이상을 사용하세요. 설치되어 있는 버전을 확인하려면 `Get-Module -ListAvailable Az.Network`을 실행합니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-az-ps)를 참조하세요. `Connect-AzAccount`를 실행하여 Azure와 연결합니다.

- **Azure CLI(명령줄 인터페이스) 사용자**: [Azure Cloud Shell](https://shell.azure.com/bash)에서 명령을 실행하거나 사용자의 컴퓨터에서 CLI를 실행합니다. Azure CLI를 로컬로 실행하는 경우 Azure CLI 버전 2.0.31 이상을 사용하세요. 설치되어 있는 버전을 확인하려면 `az --version`을 실행합니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요. `az login`을 실행하여 Azure와 연결합니다.

Azure에 로그인하거나 연결할 때 사용하는 계정이 [권한](#permissions)에 나열된 적절한 작업이 할당된 [사용자 지정 역할](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)이나 [네트워크 기여자](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) 역할에 할당되어야 합니다.

## <a name="create-a-route-table"></a>경로 테이블 만들기

Azure 위치와 구독별로 만들 수 있는 경로 테이블 수에 제한이 있습니다. 자세한 내용은 [네트워킹 제한 - Azure Resource Manager](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)를 참조하세요.

1. [Azure Portal](https://portal.azure.com) 메뉴 또는 **홈** 페이지에서 **리소스 만들기** 를 선택합니다.

1. 검색 상자에 *경로 테이블* 을 입력합니다. 검색 결과에 **경로 테이블** 이 나타나면 이를 선택합니다.

1. **경로 테이블** 페이지에서 **만들기** 를 선택합니다.

1. **경로 테이블 만들기** 대화 상자에서 다음을 수행합니다.

    1. 경로 테이블의 **이름** 을 입력합니다.
    1. **구독** 을 선택합니다.
    1. 기존 **리소스 그룹** 을 선택하거나 **새로 만들기** 를 선택하여 새 리소스 그룹을 만듭니다.
    1. **위치** 를 선택합니다.
    1. VPN 게이트웨이를 통해 온-프레미스 네트워크에 연결된 가상 네트워크의 서브넷에 경로 테이블을 연결하고 온-프레미스 경로를 서브넷의 네트워크 인터페이스에 전파하지 않으려는 경우 **가상 네트워크 게이트웨이 경로 전파** 를 **사용하지 않음** 으로 설정합니다.

1. **만들기** 를 선택하여 새 경로 테이블을 만듭니다.

### <a name="create-route-table---commands"></a>경로 테이블 만들기 - 명령

| 도구 | 명령 |
| ---- | ------- |
| Azure CLI | [az network route-table create](/cli/azure/network/route-table#az-network-route-table-create) |
| PowerShell | [New-AzRouteTable](/powershell/module/az.network/new-azroutetable) |

## <a name="view-route-tables"></a>경로 테이블 보기

가상 네트워크를 관리하기 위해 [Azure Portal](https://portal.azure.com)로 이동합니다. **경로 테이블** 을 검색하여 선택합니다. 구독에 있는 경로 테이블이 나열됩니다.

### <a name="view-route-table---commands"></a>경로 테이블 보기 - 명령

| 도구 | 명령 |
| ---- | ------- |
| Azure CLI | [az network route-table list](/cli/azure/network/route-table#az-network-route-table-list) |
| PowerShell | [Get-AzRouteTable](/powershell/module/az.network/get-azroutetable) |

## <a name="view-details-of-a-route-table"></a>경로 테이블의 세부 정보 보기

1. 가상 네트워크를 관리하기 위해 [Azure Portal](https://portal.azure.com)로 이동합니다. **경로 테이블** 을 검색하여 선택합니다.

1. 경로 테이블 목록에서 세부 정보를 보려는 경로 테이블을 선택합니다.

1. **설정** 의 경로 테이블에서 **경로** 또는 경로 테이블이 연결된 **서브넷** 을 볼 수 있습니다.

일반적인 Azure 설정에 대한 자세한 내용은 다음 정보를 참조하세요.

- [활동 로그](../azure-monitor/essentials/platform-logs-overview.md)
- [액세스 제어(IAM)](../role-based-access-control/overview.md)
- [태그](../azure-resource-manager/management/tag-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [잠금](../azure-resource-manager/management/lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Automation 스크립트](../azure-resource-manager/templates/export-template-portal.md)

### <a name="view-details-of-route-table---commands"></a>경로 테이블의 세부 정보 보기 - 명령

| 도구 | 명령 |
| ---- | ------- |
| Azure CLI | [az network route-table show](/cli/azure/network/route-table#az-network-route-table-show) |
| PowerShell | [Get-AzRouteTable](/powershell/module/az.network/get-azroutetable) |

## <a name="change-a-route-table"></a>경로 테이블 변경

1. 가상 네트워크를 관리하기 위해 [Azure Portal](https://portal.azure.com)로 이동합니다. **경로 테이블** 을 검색하여 선택합니다.

1. 경로 테이블 목록에서 변경하려는 경로 테이블을 선택합니다.

가장 일반적인 변경 사항은 경로 [추가](#create-a-route), 경로 [제거](#delete-a-route), 서브넷에 경로 테이블 [연결](#associate-a-route-table-to-a-subnet) 또는 서브넷에서 경로 테이블 [연결 해제](#dissociate-a-route-table-from-a-subnet)입니다.

### <a name="change-a-route-table---commands"></a>경로 테이블 변경 - 명령

| 도구 | 명령 |
| ---- | ------- |
| Azure CLI | [az network route-table update](/cli/azure/network/route-table#az-network-route-table-update) |
| PowerShell | [Set-AzRouteTable](/powershell/module/az.network/set-azroutetable) |

## <a name="associate-a-route-table-to-a-subnet"></a>서브넷에 경로 테이블 연결

서브넷에 경로 테이블을 연결할 수 있습니다. 경로 테이블은 0개 또는 여러 개의 서브넷에 연결할 수 있습니다. 경로 테이블은 가상 네트워크에 연결되지 않기 때문에 경로 테이블을 연결하려는 각 서브넷에 경로 테이블을 연결해야 합니다. Azure는 서브넷에서 나가는 모든 트래픽을 경로 테이블 내에서 만든 경로인 [기본 경로](virtual-networks-udr-overview.md#default)를 기반으로 라우팅하고, 가상 네트워크가 Azure 가상 네트워크 게이트웨이에 연결된 경우에는 온-프레미스 네트워크에서 전파된 경로(ExpressRoute 또는 VPN)를 기반으로 라우팅합니다. 경로 테이블과 동일한 Azure 위치 및 구독에 있는 가상 네트워크의 서브넷에만 경로 테이블을 연결할 수 있습니다.

1. 가상 네트워크를 관리하기 위해 [Azure Portal](https://portal.azure.com)로 이동합니다. **가상 네트워크** 를 검색하여 선택합니다.

1. 가상 네트워크 목록에서 경로 테이블을 연결하고자 하는 서브넷이 포함된 가상 네트워크를 선택합니다.

1. 가상 네트워크 메뉴 모음에서 **서브넷** 을 선택합니다.

1. 경로 테이블을 연결할 서브넷을 선택합니다.

1. **경로 테이블** 에서 서브넷에 연결할 경로 테이블을 선택합니다.

1. **저장** 을 선택합니다.

가상 네트워크가 Azure VPN 게이트웨이에 연결된 경우 대상이 0.0.0.0/0인 경로가 포함된 [게이트웨이 서브넷](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub)에 경로 테이블을 연결하지 않습니다. 그러면 게이트웨이가 제대로 작동하지 못할 수 있습니다. 경로에서 0.0.0.0/0을 사용하는 방법에 대한 자세한 내용은 [가상 네트워크 트래픽 라우팅](virtual-networks-udr-overview.md#default-route)을 참조하세요.

### <a name="associate-a-route-table---commands"></a>경로 테이블 연결 - 명령

| 도구 | 명령 |
| ---- | ------- |
| Azure CLI | [az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update) |
| PowerShell | [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) |

## <a name="dissociate-a-route-table-from-a-subnet"></a>서브넷에서 경로 테이블 분리

서브넷에서 경로 테이블을 분리하면 Azure는 [기본 경로](virtual-networks-udr-overview.md#default)를 기반으로 트래픽을 라우트합니다.

1. 가상 네트워크를 관리하기 위해 [Azure Portal](https://portal.azure.com)로 이동합니다. **가상 네트워크** 를 검색하여 선택합니다.

1. 가상 네트워크 목록에서 경로 테이블 연결을 해제하고자 하는 서브넷이 포함된 가상 네트워크를 선택합니다.

1. 가상 네트워크 메뉴 모음에서 **서브넷** 을 선택합니다.

1. 경로 테이블을 분리할 서브넷을 선택합니다.

1. **경로 테이블** 에서 **없음** 을 선택합니다.

1. **저장** 을 선택합니다.

### <a name="dissociate-a-route-table---commands"></a>경로 테이블 연결 해제 - 명령

| 도구 | 명령 |
| ---- | ------- |
| Azure CLI | [az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update) |
| PowerShell | [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) |

## <a name="delete-a-route-table"></a>경로 테이블 삭제

서브넷에 연결된 경로 테이블은 삭제할 수 없습니다. 경로 테이블을 삭제하기 전에 모든 서브넷에서 [분리](#dissociate-a-route-table-from-a-subnet)합니다.

1. 경로 테이블을 관리하기 위해 [Azure Portal](https://portal.azure.com)로 이동합니다. **경로 테이블** 을 검색하여 선택합니다.

1. 경로 테이블 목록에서 삭제하려는 경로 테이블을 선택합니다.

1. **삭제** 를 선택한 다음 확인 대화 상자에서 **예** 를 선택합니다.

### <a name="delete-a-route-table---commands"></a>경로 테이블 삭제 - 명령

| 도구 | 명령 |
| ---- | ------- |
| Azure CLI | [az network route-table delete](/cli/azure/network/route-table#az-network-route-table-delete) |
| PowerShell | [Remove-AzRouteTable](/powershell/module/az.network/remove-azroutetable) |

## <a name="create-a-route"></a>경로 만들기

Azure 위치와 구독별로 만들 수 있는 경로 테이블당 경로 수에 제한이 있습니다. 자세한 내용은 [네트워킹 제한 - Azure Resource Manager](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)를 참조하세요.

1. 경로 테이블을 관리하기 위해 [Azure Portal](https://portal.azure.com)로 이동합니다. **경로 테이블** 을 검색하여 선택합니다.

1. 경로 테이블 목록에서 경로를 추가하려는 경로 테이블을 선택합니다.

1. 경로 테이블 메뉴 모음에서 **경로** > **추가** 를 선택합니다.

1. 경로 테이블 내의 경로에 고유한 **경로 이름** 을 입력합니다.

1. 트래픽을 라우팅할 **주소 접두사** 를 CIDR(Classless Inter-Domain Routing) 표기법으로 입력합니다. 접두사는 경로 테이블 내 둘 이상의 경로에서 중복될 수 없지만 다른 접두사 내에서는 중복 가능합니다. 예를 들어 하나의 경로에서 접두사로 10.0.0.0/16을 정의한 경우 10.0.0.0/22 주소 접두사로 다른 경로를 정의할 수 있습니다. Azure는 가장 긴 접두사 일치를 기반으로 트래픽 경로를 선택합니다. 자세한 내용은 [Azure에서 경로를 선택하는 방법](virtual-networks-udr-overview.md#how-azure-selects-a-route)을 참조하세요.

1. **다음 홉 유형** 을 선택합니다. 다음 홉 유형에 대한 자세한 정보는 [가상 네트워크 트래픽 라우팅](virtual-networks-udr-overview.md)을 참조하세요.

1. **가상 어플라이언스** 의 **다음 홉 유형** 을 선택한 경우 **다음 홉 주소** 에 IP 주소를 입력합니다.

1. **확인** 을 선택합니다.

### <a name="create-a-route---commands"></a>경로 만들기 - 명령

| 도구 | 명령 |
| ---- | ------- |
| Azure CLI | [az network route-table route create](/cli/azure/network/route-table/route#az-network-route-table-route-create) |
| PowerShell | [New-AzRouteConfig](/powershell/module/az.network/new-azrouteconfig) |

## <a name="view-routes"></a>경로 보기

경로 테이블에는 0개 또는 여러 개의 경로가 포함됩니다. 경로를 볼 때 나열되는 정보에 대한 자세한 내용은 [가상 네트워크 트래픽 라우팅](virtual-networks-udr-overview.md)을 참조하세요.

1. 경로 테이블을 관리하기 위해 [Azure Portal](https://portal.azure.com)로 이동합니다. **경로 테이블** 을 검색하여 선택합니다.

1. 경로 테이블 목록에서 경로를 보려는 경로 테이블을 선택합니다.

1. 경로 테이블 메뉴 모음에서 **경로** 를 선택하면 경로 목록이 표시됩니다.

### <a name="view-routes---commands"></a>경로 보기 - 명령

| 도구 | 명령 |
| ---- | ------- |
| Azure CLI | [az network route-table route list](/cli/azure/network/route-table/route#az-network-route-table-route-list) |
| PowerShell | [Get-AzRouteConfig](/powershell/module/az.network/get-azrouteconfig) |

## <a name="view-details-of-a-route"></a>경로의 세부 정보 보기

1. 경로 테이블을 관리하기 위해 [Azure Portal](https://portal.azure.com)로 이동합니다. **경로 테이블** 을 검색하여 선택합니다.

1. 경로 테이블 목록에서 세부 정보를 보려는 경로가 포함된 테이블을 선택합니다.

1. 경로 테이블 메뉴 모음에서 **경로** 를 선택하면 경로 목록이 표시됩니다.

1. 세부 정보를 볼 경로를 선택합니다.

### <a name="view-details-of-a-route---commands"></a>경로 세부 정보 보기 - 명령

| 도구 | 명령 |
| ---- | ------- |
| Azure CLI | [az network route-table route show](/cli/azure/network/route-table/route#az-network-route-table-route-show) |
| PowerShell | [Get-AzRouteConfig](/powershell/module/az.network/get-azrouteconfig) |

## <a name="change-a-route"></a>경로 변경

1. 경로 테이블을 관리하기 위해 [Azure Portal](https://portal.azure.com)로 이동합니다. **경로 테이블** 을 검색하여 선택합니다.

1. 경로 테이블 목록에서 변경하려는 경로가 포함된 테이블을 선택합니다.

1. 경로 테이블 메뉴 모음에서 **경로** 를 선택하면 경로 목록이 표시됩니다.

1. 변경할 경로를 선택합니다.

1. 기존 설정을 새 설정으로 변경한 후 **저장** 을 선택합니다.

### <a name="change-a-route---commands"></a>경로 변경 - 명령

| 도구 | 명령 |
| ---- | ------- |
| Azure CLI | [az network route-table route update](/cli/azure/network/route-table/route#az-network-route-table-route-update) |
| PowerShell | [Set-AzRouteConfig](/powershell/module/az.network/set-azrouteconfig) |

## <a name="delete-a-route"></a>경로 삭제

1. 경로 테이블을 관리하기 위해 [Azure Portal](https://portal.azure.com)로 이동합니다. **경로 테이블** 을 검색하여 선택합니다.

1. 경로 테이블 목록에서 삭제하려는 경로가 포함된 테이블을 선택합니다.

1. 경로 테이블 메뉴 모음에서 **경로** 를 선택하면 경로 목록이 표시됩니다.

1. 삭제할 경로를 선택합니다.

1. **삭제** 를 선택한 다음 확인 대화 상자에서 **예** 를 선택합니다.

### <a name="delete-a-route---commands"></a>경로 삭제 - 명령

| 도구 | 명령 |
| ---- | ------- |
| Azure CLI | [az network route-table route delete](/cli/azure/network/route-table/route#az-network-route-table-route-delete) |
| PowerShell | [Remove-AzRouteConfig](/powershell/module/az.network/remove-azrouteconfig) |

## <a name="view-effective-routes"></a>유효한 경로 보기

VM 연결 네트워크 인터페이스의 효과적인 경로는 사용자가 만든 경로 테이블, Azure의 기본 경로, Azure 가상 네트워크 게이트웨이에서 BGP(Border Gateway Protocol)를 통해 온-프레미스 네트워크에서 전파된 모든 경로의 조합입니다. 네트워크 인터페이스의 유효 경로를 파악하면 라우팅 문제를 해결할 때 유용합니다. 실행 중인 VM에 연결된 모든 네트워크 인터페이스의 유효 경로를 볼 수 있습니다.

1. VM을 관리하기 위해 [Azure Portal](https://portal.azure.com)로 이동합니다. **가상 머신** 을 검색하여 선택합니다.

1. 가상 머신 목록에서 유효 경로를 보려는 VM을 선택합니다.

1. VM 메뉴 모음에서 **네트워킹** 을 선택합니다.

1. 네트워크 인터페이스의 이름을 선택합니다.

1. 네트워크 인터페이스 메뉴 모음에서 **유효 경로** 를 선택합니다.

1. 유효 경로 목록을 검토하여 트래픽을 라우팅할 올바른 경로가 있는지 확인합니다. [가상 네트워크 트래픽 라우팅](virtual-networks-udr-overview.md)의 이 목록에서 다음 홉 유형에 대해 자세히 알아보세요.

### <a name="view-effective-routes---commands"></a>유효 경로 보기 - 명령

| 도구 | 명령 |
| ---- | ------- |
| Azure CLI | [az network nic show-effective-route-table](/cli/azure/network/nic#az-network-nic-show-effective-route-table) |
| PowerShell | [Get-AzEffectiveRouteTable](/powershell/module/az.network/get-azeffectiveroutetable) |

## <a name="validate-routing-between-two-endpoints"></a>두 개의 엔드포인트 간의 라우팅 유효성 검사

가상 머신 및 다른 Azure 리소스, 온-프레미스 리소스 또는 인터넷 리소스의 IP 주소 사이에서 다음 홉 유형을 확인할 수 있습니다. Azure 라우팅을 확인하면 라우팅 문제를 해결할 때 유용합니다. 이 작업을 완료하려면 기존 Network Watcher가 있어야 합니다. 기존 Network Watcher가 없는 경우 [Network Watcher 만들기](../network-watcher/network-watcher-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json)의 단계를 완료하여 만듭니다.

1. Network Watcher를 관리하기 위해 [Azure Portal](https://portal.azure.com)로 이동합니다. **Network Watcher** 를 검색하고 선택합니다.

1. Network Watch 메뉴 모음에서 **다음 홉** 을 선택합니다.

1. **Network Watcher | 다음 홉** 페이지에서 다음을 수행합니다.

    1. 라우팅 유효성 검사를 실시할 원본 VM의 **리소스 그룹** 및 **구독** 을 선택합니다.

    1. **가상 머신** 과 VM에 연결된 **네트워크 인터페이스** 를 선택합니다.
    
    1. 라우팅 유효성을 검사할 네트워크 인터페이스에 할당된 **원본 IP 주소** 를 입력합니다.

    1. 라우팅 유효성 검사를 실시할 **대상 IP 주소** 를 입력합니다.

1. **다음 홉** 을 선택합니다.

잠시 후 Azure 다음 홉 유형 및 트래픽을 라우팅한 경로 ID를 제공합니다. [가상 네트워크 트래픽 라우팅](virtual-networks-udr-overview.md)에서 반환된 사항을 확인하고 다음 홉 유형에 대해 자세히 알아보세요.

### <a name="validate-routing-between-two-endpoints---commands"></a>두 개의 엔드포인트 간의 라우팅 유효성 검사 - 명령

| 도구 | 명령 |
| ---- | ------- |
| Azure CLI | [az network watcher show-next-hop](/cli/azure/network/watcher#az-network-watcher-show-next-hop) |
| PowerShell | [Get-AzNetworkWatcherNextHop](/powershell/module/az.network/get-aznetworkwatchernexthop) |

## <a name="permissions"></a>권한

경로 테이블 및 경로에 대한 작업을 수행하려면 다음 표에 나열된 적절한 작업이 할당된 [네트워크 기여자 역할](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) 또는 [사용자 지정 역할](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)에 계정을 할당해야 합니다.

| 작업                                                          |   이름                                                  |
|--------------------------------------------------------------   |   -------------------------------------------           |
| Microsoft.Network/routeTables/read                              |   경로 테이블 읽기                                    |
| Microsoft.Network/routeTables/write                             |   경로 테이블 만들기 또는 업데이트                        |
| Microsoft.Network/routeTables/delete                            |   경로 테이블 삭제                                  |
| Microsoft.Network/routeTables/join/action                       |   서브넷에 경로 테이블 연결                   |
| Microsoft.Network/routeTables/routes/read                       |   경로 읽기                                          |
| Microsoft.Network/routeTables/routes/write                      |   경로 만들기 또는 업데이트                              |
| Microsoft.Network/routeTables/routes/delete                     |   경로 삭제                                        |
| Microsoft.Network/networkInterfaces/effectiveRouteTable/action  |   네트워크 인터페이스 유효 경로 테이블 가져오기 |
| Microsoft.Network/networkWatchers/nextHop/action                |   VM에서 다음 홉 가져오기                           |

## <a name="next-steps"></a>다음 단계

- [PowerShell](powershell-samples.md) 또는 [Azure CLI](cli-samples.md) 샘플 스크립트 또는 Azure [Resource Manager 템플릿](template-samples.md)을 사용하여 경로 테이블 만들기
- 가상 네트워크에 대한 [Azure Policy 정의](./policy-reference.md) 만들기 및 할당