---
title: Azure 경로 테이블 만들기, 변경 또는 삭제
titlesuffix: Azure Virtual Network
description: 경로 테이블을 만들거나 변경하거나 삭제하는 방법을 알아봅니다.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/19/2020
ms.author: kumud
ms.openlocfilehash: 79310ddf121d6ada10755b198b515fdc9c1114d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247064"
---
# <a name="create-change-or-delete-a-route-table"></a>경로 테이블 만들기, 변경 또는 삭제

Azure는 Azure 서브넷, 가상 네트워크 및 온-프레미스 네트워크 간에 트래픽을 자동으로 라우트합니다. Azure의 기본 라우팅 중 하나를 변경하려면 경로 테이블을 만듭니다. 가상 네트워크에서 라우팅을 새로 접하는 경우 [가상 네트워크 트래픽 라우팅](virtual-networks-udr-overview.md) 또는 [자습서를](tutorial-create-route-table-portal.md)완료하여 이에 대해 자세히 알아볼 수 있습니다.

## <a name="before-you-begin"></a>시작하기 전에

없는 경우 활성 구독을 사용 하 고 Azure 계정을 설정 합니다. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). 그런 다음 이 문서의 모든 섹션에서 단계를 시작하기 전에 다음 작업 중 하나를 완료합니다.

- **포털 사용자**: Azure 계정으로 [Azure 포털에](https://portal.azure.com) 로그인합니다.

- **PowerShell 사용자**: Azure 클라우드 [셸에서](https://shell.azure.com/powershell)명령을 실행하거나 컴퓨터에서 PowerShell을 실행합니다. Azure Cloud Shell은 이 항목의 단계를 실행하는 데 무료로 사용할 수 있는 대화형 셸입니다. 공용 Azure 도구가 사전 설치되어 계정에서 사용하도록 구성되어 있습니다. Azure Cloud Shell 브라우저 탭에서 **환경 드롭다운 선택** 목록을 찾은 다음 아직 선택되지 않은 경우 **PowerShell을** 선택합니다.

    로컬로 PowerShell을 실행하는 경우 Azure PowerShell 모듈 버전 1.0.0 이상을 사용합니다. 설치되어 있는 버전을 확인하려면 `Get-Module -ListAvailable Az.Network`을 실행합니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-az-ps)를 참조하세요. 또한 `Connect-AzAccount` Azure와의 연결을 만들기 위해 실행합니다.

- **CLI(Azure 명령줄 인터페이스) 사용자**: [Azure Cloud Shell에서](https://shell.azure.com/bash)명령을 실행하거나 컴퓨터에서 CLI를 실행합니다. Azure CLI를 로컬로 실행하는 경우 Azure CLI 버전 2.0.31 이상을 사용합니다. 설치되어 있는 버전을 확인하려면 `az --version`을 실행합니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하십시오. 또한 `az login` Azure와의 연결을 만들기 위해 실행합니다.

로그인하거나 Azure에 연결하는 계정은 [네트워크 기여자 역할](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) 또는 [사용 권한에](#permissions)나열된 적절한 작업을 할당한 사용자 지정 [역할에](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 할당되어야 합니다.

## <a name="create-a-route-table"></a>경로 테이블 만들기

Azure 위치 및 구독당 만들 수 있는 경로 테이블 수에는 제한이 있습니다. 자세한 내용은 [네트워킹 제한 - Azure 리소스 관리자](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)를 참조하십시오.

1. Azure [포털](https://portal.azure.com) 메뉴 또는 **홈** 페이지에서 **리소스 만들기를**선택합니다.

1. 검색 상자에 *경로 테이블*을 입력합니다. 검색 결과에 **경로 테이블**이 나타나면 이를 선택합니다.

1. **경로 테이블** 페이지에서 **만들기**를 선택합니다.

1. 경로 테이블 만들기 대화 상자에서 다음을 **수행합니다.**

    1. 경로 테이블에 대한 **이름을** 입력합니다.
    1. **구독을**선택합니다.
    1. 기존 **리소스 그룹을** 선택하거나 새 리소스 그룹을 만들려면 새 **만들기를** 선택합니다.
    1. **위치**를 선택합니다.
    1. VPN 게이트웨이를 통해 온-프레미스 네트워크에 연결된 가상 네트워크의 서브넷에 경로 테이블을 연결하려는 경우 온-프레미스 경로를 서브넷의 네트워크 인터페이스에 전파하지 않으려면 가상 **네트워크 게이트웨이 경로 전파를** **비활성화로**설정합니다.

1. 새 경로 테이블을 만들려면 **만들기를** 선택합니다.

### <a name="create-route-table---commands"></a>경로 테이블 만들기 - 명령

| 도구 | 명령 |
| ---- | ------- |
| Azure CLI | [az network route-table create](/cli/azure/network/route-table#az-network-route-table-create) |
| PowerShell | [New-AzRouteTable](/powershell/module/az.network/new-azroutetable) |

## <a name="view-route-tables"></a>경로 테이블 보기

[Azure 포털로](https://portal.azure.com) 이동하여 가상 네트워크를 관리합니다. **경로 테이블을**검색하고 선택합니다. 구독에 있는 경로 테이블이 나열됩니다.

### <a name="view-route-table---commands"></a>경로 테이블 보기 - 명령

| 도구 | 명령 |
| ---- | ------- |
| Azure CLI | [az 네트워크 경로 테이블 목록](/cli/azure/network/route-table#az-network-route-table-list) |
| PowerShell | [Get-AzRouteTable](/powershell/module/az.network/get-azroutetable) |

## <a name="view-details-of-a-route-table"></a>경로 테이블의 세부 정보 보기

1. [Azure 포털로](https://portal.azure.com) 이동하여 가상 네트워크를 관리합니다. **경로 테이블을**검색하고 선택합니다.

1. 경로 테이블 목록에서 세부 정보를 볼 경로 테이블을 선택합니다.

1. 경로 테이블 페이지에서 **설정**에서 경로 테이블또는 라우트 테이블이 연결된 **서브넷의** **경로를** 봅니다.

일반적인 Azure 설정에 대한 자세한 내용은 다음 정보를 참조하세요.

- [활동 로그](../azure-monitor/platform/platform-logs-overview.md)
- [액세스 제어(IAM)](../role-based-access-control/overview.md)
- [태그](../azure-resource-manager/management/tag-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [잠금](../azure-resource-manager/management/lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [자동화 스크립트](../azure-resource-manager/templates/export-template-portal.md)

### <a name="view-details-of-route-table---commands"></a>경로 테이블 - 명령의 세부 정보 보기

| 도구 | 명령 |
| ---- | ------- |
| Azure CLI | [az 네트워크 경로 테이블 표시](/cli/azure/network/route-table#az-network-route-table-show) |
| PowerShell | [Get-AzRouteTable](/powershell/module/az.network/get-azroutetable) |

## <a name="change-a-route-table"></a>경로 테이블 변경

1. [Azure 포털로](https://portal.azure.com) 이동하여 가상 네트워크를 관리합니다. **경로 테이블을**검색하고 선택합니다.

1. 경로 테이블 목록에서 변경할 경로 테이블을 선택합니다.

가장 일반적인 변경 사항은 경로를 [추가하거나,](#create-a-route) 경로를 [제거하거나,](#delete-a-route) 서브넷에 경로 테이블을 [연결하거나,](#associate-a-route-table-to-a-subnet) 서브넷에서 경로 테이블을 [분리하는](#dissociate-a-route-table-from-a-subnet) 것입니다.

### <a name="change-a-route-table---commands"></a>경로 테이블 변경 - 명령

| 도구 | 명령 |
| ---- | ------- |
| Azure CLI | [az 네트워크 경로 테이블 업데이트](/cli/azure/network/route-table#az-network-route-table-update) |
| PowerShell | [Set-AzRouteTable](/powershell/module/az.network/set-azroutetable) |

## <a name="associate-a-route-table-to-a-subnet"></a>서브넷에 경로 테이블 연결

선택적으로 라우트 테이블을 서브넷에 연결할 수 있습니다. 경로 테이블은 0개 이상의 서브넷에 연결할 수 있습니다. 경로 테이블은 가상 네트워크에 연결되지 않으므로 라우트 테이블을 연관된 경로 테이블을 원하는 각 서브넷에 연결해야 합니다. Azure는 가상 네트워크가 Azure 가상 네트워크 게이트웨이(ExpressRoute 또는 VPN)에 연결되어 있는 경우 경로 테이블, [기본 경로](virtual-networks-udr-overview.md#default)및 온-프레미스 네트워크에서 전파된 경로 내에서 만든 경로를 기반으로 서브넷을 떠나는 모든 트래픽을 라우팅합니다. 경로 테이블과 동일한 Azure 위치 및 구독에 있는 가상 네트워크의 서브넷에만 경로 테이블을 연결할 수 있습니다.

1. [Azure 포털로](https://portal.azure.com) 이동하여 가상 네트워크를 관리합니다. **가상 네트워크를**검색하고 선택합니다.

1. 가상 네트워크 목록에서 경로 테이블을 연결할 서브넷이 포함된 가상 네트워크를 선택합니다.

1. 가상 네트워크 메뉴 모음에서 **Subnet을 선택합니다.**

1. 경로 테이블을 연결할 서브넷을 선택합니다.

1. **경로 테이블에서**서브넷에 연결할 경로 테이블을 선택합니다.

1. **저장**을 선택합니다.

가상 네트워크가 Azure VPN 게이트웨이에 연결되어 있는 경우 *0.0.0.0/0의*대상이 있는 경로가 포함된 [게이트웨이 서브넷에](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub) 경로 테이블을 연결하지 마십시오. 그러면 게이트웨이가 제대로 작동하지 못할 수 있습니다. 경로에서 *0.0.0.0/0* 을 사용하는 것에 대한 자세한 내용은 [가상 네트워크 트래픽 라우팅을](virtual-networks-udr-overview.md#default-route)참조하십시오.

### <a name="associate-a-route-table---commands"></a>경로 테이블 연결 - 명령

| 도구 | 명령 |
| ---- | ------- |
| Azure CLI | [az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update) |
| PowerShell | [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) |

## <a name="dissociate-a-route-table-from-a-subnet"></a>서브넷에서 경로 테이블 분리

서브넷에서 경로 테이블을 분리하면 Azure는 [기본 경로](virtual-networks-udr-overview.md#default)를 기반으로 트래픽을 라우트합니다.

1. [Azure 포털로](https://portal.azure.com) 이동하여 가상 네트워크를 관리합니다. **가상 네트워크를**검색하고 선택합니다.

1. 가상 네트워크 목록에서 경로 테이블을 분리할 서브넷이 포함된 가상 네트워크를 선택합니다.

1. 가상 네트워크 메뉴 모음에서 **Subnet을 선택합니다.**

1. 경로 테이블을 분리할 서브넷을 선택합니다.

1. **라우팅 테이블에서** **없음을**선택합니다.

1. **저장**을 선택합니다.

### <a name="dissociate-a-route-table---commands"></a>경로 테이블 해리 - 명령

| 도구 | 명령 |
| ---- | ------- |
| Azure CLI | [az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update) |
| PowerShell | [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) |

## <a name="delete-a-route-table"></a>경로 테이블 삭제

서브넷에 연결된 경로 테이블은 삭제할 수 없습니다. 경로 테이블을 삭제하기 전에 모든 서브넷에서 [분리](#dissociate-a-route-table-from-a-subnet)합니다.

1. [Azure 포털로](https://portal.azure.com) 이동하여 경로 테이블을 관리합니다. **경로 테이블을**검색하고 선택합니다.

1. 경로 테이블 목록에서 삭제할 경로 테이블을 선택합니다.

1. **삭제를**선택한 다음 확인 대화 상자에서 **예를** 선택합니다.

### <a name="delete-a-route-table---commands"></a>경로 테이블 삭제 - 명령

| 도구 | 명령 |
| ---- | ------- |
| Azure CLI | [az 네트워크 경로 테이블 삭제](/cli/azure/network/route-table#az-network-route-table-delete) |
| PowerShell | [제거-아즈루트 테이블](/powershell/module/az.network/remove-azroutetable) |

## <a name="create-a-route"></a>경로 만들기

Azure 위치 및 구독당 만들 수 있는 경로 테이블당 경로 수에는 제한이 있습니다. 자세한 내용은 [네트워킹 제한 - Azure 리소스 관리자](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)를 참조하십시오.

1. [Azure 포털로](https://portal.azure.com) 이동하여 경로 테이블을 관리합니다. **경로 테이블을**검색하고 선택합니다.

1. 경로 테이블 목록에서 경로를 추가할 경로 테이블을 선택합니다.

1. 경로 테이블 메뉴 모음에서 **경로** > **추가를**선택합니다.

1. 경로 테이블 내에서 경로에 대한 고유한 경로 **이름을** 입력합니다.

1. 트래픽을 라우팅할 클래스리스 도메인 간 라우팅(CIDR) 표기에 **주소 접두사를**입력합니다. 접두사는 다른 접두사 내에 있을 수 있지만 배관 테이블 내의 두 개 이상의 경로에서 접두사를 복제할 수 없습니다. 예를 들어 한 경로의 접두사로 *10.0.0.0/16을* 정의한 경우에도 *10.0.0.0/22* 주소 접두사를 사용할 수 있습니다. Azure는 가장 긴 접두사 일치를 기반으로 트래픽 경로를 선택합니다. 자세한 내용은 [Azure에서 경로를 선택하는 방법을 참조하세요.](virtual-networks-udr-overview.md#how-azure-selects-a-route)

1. 다음 **홉 유형을**선택합니다. 다음 홉 유형에 대한 자세한 내용은 [가상 네트워크 트래픽 라우팅을](virtual-networks-udr-overview.md)참조하십시오.

1. **가상 어플라이언스의** **다음 홉 유형을** 선택한 경우 다음 홉 주소에 대한 IP 주소를 **입력합니다.**

1. **확인**을 선택합니다.

### <a name="create-a-route---commands"></a>경로 만들기 - 명령

| 도구 | 명령 |
| ---- | ------- |
| Azure CLI | [az network route-table route create](/cli/azure/network/route-table/route#az-network-route-table-route-create) |
| PowerShell | [New-AzRouteConfig](/powershell/module/az.network/new-azrouteconfig) |

## <a name="view-routes"></a>경로 보기

경로 테이블에는 0개 또는 여러 개의 경로가 포함됩니다. 경로를 볼 때 나열된 정보에 대한 자세한 내용은 [가상 네트워크 트래픽 라우팅을](virtual-networks-udr-overview.md)참조하십시오.

1. [Azure 포털로](https://portal.azure.com) 이동하여 경로 테이블을 관리합니다. **경로 테이블을**검색하고 선택합니다.

1. 경로 테이블 목록에서 경로를 볼 경로 테이블을 선택합니다.

1. 경로 테이블 메뉴 모음에서 **경로를** 선택하여 경로 목록을 확인합니다.

### <a name="view-routes---commands"></a>경로 보기 - 명령

| 도구 | 명령 |
| ---- | ------- |
| Azure CLI | [az 네트워크 경로-테이블 경로 목록](/cli/azure/network/route-table/route#az-network-route-table-route-list) |
| PowerShell | [겟 아즈루트콩피그](/powershell/module/az.network/get-azrouteconfig) |

## <a name="view-details-of-a-route"></a>경로의 세부 정보 보기

1. [Azure 포털로](https://portal.azure.com) 이동하여 경로 테이블을 관리합니다. **경로 테이블을**검색하고 선택합니다.

1. 경로 테이블 목록에서 세부 정보를 볼 루트를 포함하는 경로 테이블을 선택합니다.

1. 경로 테이블 메뉴 모음에서 **경로를** 선택하여 경로 목록을 확인합니다.

1. 세부 정보를 볼 경로를 선택합니다.

### <a name="view-details-of-a-route---commands"></a>경로 - 명령의 세부 정보 보기

| 도구 | 명령 |
| ---- | ------- |
| Azure CLI | [az 네트워크 경로 테이블 경로 표시](/cli/azure/network/route-table/route#az-network-route-table-route-show) |
| PowerShell | [겟 아즈루트콩피그](/powershell/module/az.network/get-azrouteconfig) |

## <a name="change-a-route"></a>경로 변경

1. [Azure 포털로](https://portal.azure.com) 이동하여 경로 테이블을 관리합니다. **경로 테이블을**검색하고 선택합니다.

1. 경로 테이블 목록에서 변경할 경로가 포함된 경로 테이블을 선택합니다.

1. 경로 테이블 메뉴 모음에서 **경로를** 선택하여 경로 목록을 확인합니다.

1. 변경하려는 경로를 선택합니다.

1. 기존 설정을 새 설정으로 변경한 후 **저장**을 선택합니다.

### <a name="change-a-route---commands"></a>경로 변경 - 명령

| 도구 | 명령 |
| ---- | ------- |
| Azure CLI | [az 네트워크 경로 테이블 경로 업데이트](/cli/azure/network/route-table/route#az-network-route-table-route-update) |
| PowerShell | [세트-아즈루트콩피그](/powershell/module/az.network/set-azrouteconfig) |

## <a name="delete-a-route"></a>경로 삭제

1. [Azure 포털로](https://portal.azure.com) 이동하여 경로 테이블을 관리합니다. **경로 테이블을**검색하고 선택합니다.

1. 경로 테이블 목록에서 삭제할 경로가 포함된 경로 테이블을 선택합니다.

1. 경로 테이블 메뉴 모음에서 **경로를** 선택하여 경로 목록을 확인합니다.

1. 삭제할 경로를 선택합니다.

1. **삭제를**선택한 다음 확인 대화 상자에서 **예를** 선택합니다.

### <a name="delete-a-route---commands"></a>경로 삭제 - 명령

| 도구 | 명령 |
| ---- | ------- |
| Azure CLI | [az 네트워크 경로 테이블 경로 삭제](/cli/azure/network/route-table/route#az-network-route-table-route-delete) |
| PowerShell | [제거-아즈루트콘피그](/powershell/module/az.network/remove-azrouteconfig) |

## <a name="view-effective-routes"></a>유효한 경로 보기

각 VM 연결 네트워크 인터페이스에 대한 효과적인 경로는 생성한 경로 테이블, Azure의 기본 경로 및 Azure 가상 네트워크를 통해 BGP(보더 게이트웨이 프로토콜)를 통해 온-프레미스 네트워크에서 전파되는 모든 경로의 조합입니다. 게이트웨이. 네트워크 인터페이스의 유효 경로를 파악하면 라우팅 문제를 해결할 때 유용합니다. 실행 중인 VM에 연결된 모든 네트워크 인터페이스에 대한 유효 경로를 볼 수 있습니다.

1. [Azure 포털로](https://portal.azure.com) 이동하여 VM을 관리합니다. **가상 컴퓨터를**검색하고 선택합니다.

1. 가상 시스템 목록에서 효과적인 경로를 보려는 VM을 선택합니다.

1. VM 메뉴 모음에서 **네트워킹을 선택합니다.**

1. 네트워크 인터페이스의 이름을 선택합니다.

1. 네트워크 인터페이스 메뉴 모음에서 **유효 경로를**선택합니다.

1. 효과적인 경로 목록을 검토하여 트래픽을 라우팅하려는 위치에 대한 올바른 경로가 있는지 확인합니다. [가상 네트워크 트래픽 라우팅에서](virtual-networks-udr-overview.md)이 목록에 표시되는 다음 홉 유형에 대해 자세히 알아봅니다.

### <a name="view-effective-routes---commands"></a>효과적인 경로 보기 - 명령

| 도구 | 명령 |
| ---- | ------- |
| Azure CLI | [az 네트워크 닉 쇼 효과적인 경로 테이블](/cli/azure/network/nic#az-network-nic-show-effective-route-table) |
| PowerShell | [Get-AzEffectiveRouteTable](/powershell/module/az.network/get-azeffectiveroutetable) |

## <a name="validate-routing-between-two-endpoints"></a>두 개의 엔드포인트 간의 라우팅 유효성 검사

가상 머신 및 다른 Azure 리소스, 온-프레미스 리소스 또는 인터넷 리소스의 IP 주소 사이에서 다음 홉 유형을 확인할 수 있습니다. Azure 라우팅을 확인하면 라우팅 문제를 해결할 때 유용합니다. 이 작업을 완료하려면 기존 네트워크 감시자가 있어야 합니다. 기존 네트워크 감시자가 없는 경우 [네트워크 감시자 인스턴스 만들기의](../network-watcher/network-watcher-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json)단계를 완료하여 작성합니다.

1. [Azure 포털로](https://portal.azure.com) 이동하여 네트워크 감시자를 관리합니다. 네트워크 감시자 를 검색하고 **선택합니다.**

1. 네트워크 감시자 메뉴 모음에서 **다음 홉을**선택합니다.

1. 네트워크 **감시자 | 다음 홉** 페이지:

    1. 라우팅의 유효성을 검사할 원본 VM의 **구독** 및 **리소스 그룹을** 선택합니다.

    1. **VM에** 연결된 가상 컴퓨터 및 **네트워크 인터페이스를** 선택합니다.
    
    1. 라우팅의 유효성을 검사할 네트워크 인터페이스에 할당된 **Source IP 주소를** 입력합니다.

    1. 라우팅의 유효성을 검사할 **대상 IP 주소를** 입력합니다.

1. **다음 홉**을 선택합니다.

잠시 후 Azure는 다음 홉 유형과 트래픽을 라우팅한 경로의 ID를 알려줍니다. [가상 네트워크 트래픽 라우팅에서](virtual-networks-udr-overview.md)반환되는 다음 홉 유형에 대해 자세히 알아봅니다.

### <a name="validate-routing-between-two-endpoints---commands"></a>두 끝점- 명령 간의 라우팅 유효성 검사

| 도구 | 명령 |
| ---- | ------- |
| Azure CLI | [az 네트워크 감시자 쇼 - 다음 홉](/cli/azure/network/watcher#az-network-watcher-show-next-hop) |
| PowerShell | [겟-아즈네트워크워처넥스트홉](/powershell/module/az.network/get-aznetworkwatchernexthop) |

## <a name="permissions"></a>사용 권한

라우트 테이블 및 경로에서 작업을 수행하려면 계정을 [네트워크 기여자 역할](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) 또는 다음 표에 나열된 적절한 작업을 할당한 [사용자 지정 역할에](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 할당해야 합니다.

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

- [PowerShell](powershell-samples.md) 또는 [Azure CLI](cli-samples.md) 샘플 스크립트 또는 Azure [리소스 관리자 템플릿을](template-samples.md) 사용하여 경로 테이블 만들기
- 가상 네트워크에 대한 [Azure 정책](policy-samples.md) 만들기 및 적용
