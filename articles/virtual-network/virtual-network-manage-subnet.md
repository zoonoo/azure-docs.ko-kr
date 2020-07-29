---
title: Azure Virtual Network 서브넷 만들기, 변경 또는 삭제
titlesuffix: Azure Virtual Network
description: 가상 네트워크에 대 한 정보를 찾을 수 있는 위치 및 Azure에서 가상 네트워크 서브넷을 추가, 변경 또는 삭제 하는 방법에 대해 알아봅니다.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2020
ms.author: kumud
ms.openlocfilehash: 15fe5d6d16948875253d65e70d9d440214a4a2e8
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87286107"
---
# <a name="add-change-or-delete-a-virtual-network-subnet"></a>가상 네트워크 서브넷 추가, 변경 또는 삭제

가상 네트워크 서브넷을 추가, 변경 또는 삭제하는 방법을 알아봅니다. 가상 네트워크에 배포된 모든 Azure 리소스는 가상 네트워크 내의 서브넷에 배포됩니다. 가상 네트워크를 처음 접하는 경우 [가상 네트워크 개요](virtual-networks-overview.md) 에서 또는 [빠른](quick-create-portal.md)시작을 완료 하 여 더 자세히 알아볼 수 있습니다. 가상 네트워크 관리에 대해 자세히 알아보려면 [가상 네트워크 만들기, 변경 또는 삭제](manage-virtual-network.md)를 참조 하세요.

## <a name="before-you-begin"></a>시작하기 전에

계정이 없으면 활성 구독으로 Azure 계정을 설정 합니다. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). 이 문서의 섹션에서 단계를 시작 하기 전에 다음 작업 중 하나를 완료 합니다. 

- **포털 사용자**: Azure 계정으로 [Azure Portal](https://portal.azure.com) 에 로그인 합니다.

- **Powershell 사용자**: [Azure Cloud Shell](https://shell.azure.com/powershell)에서 명령을 실행 하거나 컴퓨터에서 powershell을 실행 합니다. Azure Cloud Shell은 이 항목의 단계를 실행하는 데 무료로 사용할 수 있는 대화형 셸입니다. 공용 Azure 도구가 사전 설치되어 계정에서 사용하도록 구성되어 있습니다. Azure Cloud Shell 브라우저 탭에서 **환경 선택** 드롭다운 목록을 찾은 다음 **PowerShell** 을 선택 합니다 (아직 선택 하지 않은 경우).

    PowerShell을 로컬로 실행 하는 경우 Azure PowerShell 모듈 버전 1.0.0 이상을 사용 합니다. 설치되어 있는 버전을 확인하려면 `Get-Module -ListAvailable Az.Network`을 실행합니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-az-ps)를 참조하세요. 또한 `Connect-AzAccount` 를 실행 하 여 Azure에 대 한 연결을 만듭니다.

- **AZURE cli (명령줄 인터페이스) 사용자**: [Azure Cloud Shell](https://shell.azure.com/bash)에서 명령을 실행 하거나 컴퓨터에서 CLI를 실행 합니다. Azure CLI를 로컬로 실행 하는 경우 Azure CLI 버전 2.0.31 이상을 사용 합니다. 설치되어 있는 버전을 확인하려면 `az --version`을 실행합니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요. 또한 `az login` 를 실행 하 여 Azure에 대 한 연결을 만듭니다.

를 사용 하 여 Azure에 로그인 하거나 Azure에 연결 하는 계정은 [네트워크 참가자 역할](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) 역할이 나 [사용 권한](#permissions)에 나열 된 적절 한 작업을 할당 받은 [사용자 지정 역할](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 에 할당 되어야 합니다.

## <a name="add-a-subnet"></a>서브넷 추가

1. [Azure Portal](https://portal.azure.com) 로 이동 하 여 가상 네트워크를 확인 합니다. **가상 네트워크**를 검색하여 선택합니다.

2. 서브넷을 추가 하려는 가상 네트워크의 이름을 선택 합니다.

3. **설정**에서 **서브넷**  >  **서브넷**을 선택 합니다.

4. **서브넷 추가** 대화 상자에서 다음 설정에 대 한 값을 입력 합니다.

    | 설정 | 설명 |
    | --- | --- |
    | **이름** | 이름은 가상 네트워크 내에서 고유해야 합니다. 다른 Azure 서비스와의 호환성을 극대화하기 위해 이름의 첫 문자는 글자를 사용하는 것이 좋습니다. 예를 들어 Azure Application Gateway는 이름이 숫자로 시작하는 서브넷에는 배포되지 않습니다. |
    | **주소 범위** | <p>범위는 가상 네트워크의 주소 공간 내에서 고유해야 합니다. 범위는 가상 네트워크 내의 다른 서브넷 주소 범위와 겹칠 수 없습니다. CIDR(Classless Inter-Domain Routing) 표기법을 사용하여 주소 공간을 지정해야 합니다.</p><p>예를 들어 주소 공간이 *10.0.0.0/16*인 가상 네트워크에서 서브넷 주소 공간을 *10.0.0.0/22*로 정의할 수 있습니다. 지정할 수 있는 가장 작은 범위는 */29*이며, 서브넷에 8 개의 IP 주소를 제공 합니다. Azure는 프로토콜 준수를 위해 각 서브넷의 첫 번째 및 마지막 주소를 예약합니다. 세 개의 추가 주소가 Azure 서비스를 사용하기 위해 예약되어 있습니다. 따라서 */29* 주소 범위를 사용 하 여 서브넷을 정의 하면 서브넷에 사용 가능한 3 개의 IP 주소가 생성 됩니다.</p><p>가상 네트워크를 VPN Gateway에 연결하려면 게이트웨이 서브넷을 만들어야 합니다. [게이트웨이 서브넷에 대한 특정 주소 범위 고려 사항](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub)에서 대해 자세히 알아보세요. 특정 조건에서는 서브넷을 추가한 후에 주소 범위를 변경할 수 있습니다. 서브넷 주소 범위를 변경하는 방법에 대한 자세한 내용은 [서브넷 설정 변경](#change-subnet-settings)을 참조하세요.</p> |
    | **네트워크 보안 그룹** | 서브넷에 대 한 인바운드 및 아웃 바운드 네트워크 트래픽을 필터링 하기 위해 기존 네트워크 보안 그룹을 서브넷에 연결할 수 있습니다. 네트워크 보안 그룹은 가상 네트워크와 동일한 구독 및 위치에 있어야 합니다. [네트워크 보안 그룹](security-overview.md) 및 [네트워크 보안 그룹을 만드는 방법](tutorial-filter-network-traffic.md)에 대해 알아봅니다. |
    | **경로 테이블** | 다른 네트워크에 대 한 네트워크 트래픽 라우팅을 제어 하기 위해 필요에 따라 기존 경로 테이블을 서브넷에 연결할 수 있습니다. 경로 테이블은 가상 네트워크와 동일한 구독 및 위치에 있어야 합니다. [Azure 라우팅](virtual-networks-udr-overview.md) 및 [경로 테이블을 만드는 방법](tutorial-create-route-table-portal.md)에 대해 자세히 알아보세요. |
    | **서비스 엔드포인트** | <p>서브넷은 선택적으로 하나 이상의 서비스 끝점을 사용 하도록 설정할 수 있습니다. 서비스에 대해 서비스 엔드포인트를 사용하려면 **서비스** 목록에서 서비스 엔드포인트를 사용할 서비스를 선택합니다. Azure에서 끝점에 대 한 위치를 자동으로 구성 합니다. 기본적으로 Azure는 가상 네트워크 지역에 대 한 서비스 끝점을 구성 합니다. 지역 장애 조치 (failover) 시나리오를 지원 하기 위해 Azure는 Azure Storage에 대 한 [azure 페어링 지역](../best-practices-availability-paired-regions.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-paired-regions) 에 끝점을 자동으로 구성 합니다</p><p>서비스 엔드포인트를 제거하려면 서비스 엔드포인트를 제거할 서비스를 선택 취소합니다. 서비스 끝점 및 서비스 끝점을 사용 하도록 설정할 수 있는 서비스에 대 한 자세한 내용은 [가상 네트워크 서비스 끝점](virtual-network-service-endpoints-overview.md)을 참조 하세요. 서비스에 대한 서비스 엔드포인트를 사용하도록 설정하면 서비스를 사용하여 만든 리소스의 서브넷에 대해서도 네트워크 액세스를 사용하도록 설정해야 합니다. 예를 들어 **Microsoft.Storage**에 대해 서비스 엔드포인트를 사용하도록 설정하면 네트워크 액세스 권한을 부여할 모든 Azure Storage 계정에 대해서도 네트워크 액세스를 사용하도록 설정해야 합니다. 서비스 끝점이 사용 되는 서브넷에 대 한 네트워크 액세스를 사용 하도록 설정 하려면에 대해 서비스 끝점을 사용 하도록 설정한 개별 서비스에 대 한 설명서를 참조 하세요.</p><p>서브넷에 대해 서비스 엔드포인트를 사용할 수 있는지 검증하려면 서브넷에 있는 임의 네트워크 인터페이스의 [유효 경로](diagnose-network-routing-problem.md)를 확인합니다. 끝점을 구성 하면 서비스의 주소 접두사와 **VirtualNetworkServiceEndpoint**의 다음 홉 유형이 포함 된 *기본* 경로가 표시 됩니다. 라우팅에 대해 자세히 알아보려면 [가상 네트워크 트래픽 라우팅](virtual-networks-udr-overview.md)을 참조 하세요.</p> |
    | **서브넷 위임** | 서브넷은 선택적으로 하나 이상의 위임을 사용 하도록 설정할 수 있습니다. 서브넷 위임은 서비스를 배포 하는 동안 고유 식별자를 사용 하 여 서브넷에 서비스별 리소스를 만들도록 서비스에 명시적 권한을 부여 합니다. 서비스를 위임하려면 **서비스** 목록에서 위임할 서비스를 선택합니다. |

5. 선택한 가상 네트워크에 서브넷을 추가하려면 **확인**을 선택합니다.

### <a name="commands"></a>명령

| 도구 | 명령 |
| ---- | ------- |
| Azure CLI | [az network vnet subnet create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) |
| PowerShell | [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) |

## <a name="change-subnet-settings"></a>서브넷 설정 변경

1. [Azure Portal](https://portal.azure.com) 로 이동 하 여 가상 네트워크를 확인 합니다. **가상 네트워크**를 검색하여 선택합니다.

2. 변경할 서브넷을 포함 하는 가상 네트워크의 이름을 선택 합니다.

3. **설정**에서 **서브넷**을 선택 합니다.

4. 서브넷의 목록에서 설정을 변경할 서브넷을 선택합니다.

5. 서브넷 페이지에서 다음 설정 중 하나를 변경 합니다.

    | 설정 | 설명 |
    | --- | --- |
    | **주소 범위** | 서브넷 내에 배포된 리소스가 없는 경우 주소 범위를 변경할 수 있습니다. 서브넷에 리소스가 있으면 먼저 다른 서브넷으로 리소스를 이동하거나 서브넷에서 삭제해야 합니다. 리소스 이동 또는 삭제를 수행하는 단계는 리소스에 따라 다릅니다. 서브넷에 있는 리소스를 이동 하거나 삭제 하는 방법에 대 한 자세한 내용은 각 리소스 종류에 대 한 설명서를 참조 하세요. [서브넷 추가](#add-a-subnet)의 4 단계에서 **주소 범위** 에 대 한 제약 조건을 참조 하세요. |
    | **사용자** | 기본 제공 역할 또는 사용자 지정 역할을 사용하여 서브넷에 대한 액세스를 제어할 수 있습니다. 역할 및 사용자를 할당 하 여 서브넷에 액세스 하는 방법에 대 한 자세한 내용은 [역할 할당 추가](../role-based-access-control/role-assignments-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-role-assignment)를 참조 하세요. |
    | **네트워크 보안 그룹** 및 **경로 테이블** | [서브넷 추가](#add-a-subnet)의 4 단계를 참조 하세요. |
    | **서비스 엔드포인트** | <p>[서브넷 추가](#add-a-subnet)의 4 단계에서 서비스 끝점을 참조 하세요. 기존 서브넷에 대해 서비스 엔드포인트를 사용하도록 설정할 경우, 서브넷의 리소스에서 실행 중인 중요 작업이 없는지 확인합니다. 서비스 끝점은 서브넷에 있는 모든 네트워크 인터페이스에서 경로를 전환 합니다. 서비스 끝점은 *0.0.0.0/0* 주소 접두사 및 다음 홉 유형이 *인터넷*인 기본 경로를 사용 하 여 서비스의 주소 접두사와 *VirtualNetworkServiceEndpoint*의 다음 홉 유형으로 새 경로를 사용 합니다.</p><p>전환 중에, 열려 있는 TCP 연결이 종료될 수 있습니다. 모든 네트워크 인터페이스에 대 한 서비스에 대 한 트래픽 흐름이 새 경로로 업데이트 될 때까지 서비스 끝점은 사용 하도록 설정 되지 않습니다. 라우팅에 대해 자세히 알아보려면 [가상 네트워크 트래픽 라우팅](virtual-networks-udr-overview.md)을 참조 하세요.</p> |
    | **서브넷 위임** | [서브넷 추가](#add-a-subnet)의 4 단계에서 서비스 끝점을 참조 하세요. 서브넷 위임은 0개 이상의 위임을 사용할 수 있도록 수정할 수 있습니다. 서비스에 대 한 리소스가 서브넷에 이미 배포 된 경우에는 서비스에 대 한 모든 리소스가 제거 될 때까지 서브넷 위임을 추가 하거나 제거할 수 없습니다. 다른 서비스를 위임하려면 **서비스** 목록에서 위임할 서비스를 선택합니다. |

6. **저장**을 선택합니다.

### <a name="commands"></a>명령

| 도구 | 명령 |
| ---- | ------- |
| Azure CLI | [az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update) |
| PowerShell | [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) |

## <a name="delete-a-subnet"></a>서브넷 삭제

서브넷에 리소스가 없는 경우에만 해당 서브넷을 삭제할 수 있습니다. 리소스가 서브넷에 있는 경우 해당 리소스를 삭제 해야 서브넷을 삭제할 수 있습니다. 리소스 삭제를 수행하는 단계는 리소스에 따라 다릅니다. 서브넷에 있는 리소스를 삭제 하는 방법에 대 한 자세한 내용은 각 리소스 종류에 대 한 설명서를 참조 하세요.

1. [Azure Portal](https://portal.azure.com) 로 이동 하 여 가상 네트워크를 확인 합니다. **가상 네트워크**를 검색하여 선택합니다.

2. 삭제 하려는 서브넷을 포함 하는 가상 네트워크의 이름을 선택 합니다.

3. **설정**에서 **서브넷**을 선택 합니다.

4. 서브넷 목록에서 삭제 하려는 서브넷을 선택 합니다.

5. **삭제**를 선택한 다음 확인 대화 상자에서 **예** 를 선택 합니다.

### <a name="commands"></a>명령

| 도구 | 명령 |
| ---- | ------- |
| Azure CLI | [az network vnet subnet delete](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-delete) |
| PowerShell | [AzVirtualNetworkSubnetConfig](/powershell/module/az.network/remove-azvirtualnetworksubnetconfig?toc=%2fazure%2fvirtual-network%2ftoc.json) |

## <a name="permissions"></a>사용 권한

서브넷에 대 한 작업을 수행 하려면 다음 표에 나와 있는 적절 한 작업이 할당 된 [네트워크 참가자 역할](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) 또는 [사용자 지정 역할](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 에 계정을 할당 해야 합니다.

|작업                                                                   |   Name                                       |
|-----------------------------------------------------------------------  |   -----------------------------------------  |
|Microsoft.Network/virtualNetworks/subnets/read                           |   가상 네트워크 서브넷 읽기              |
|Microsoft.Network/virtualNetworks/subnets/write                          |   가상 네트워크 서브넷 만들기 또는 업데이트  |
|Microsoft.Network/virtualNetworks/subnets/delete                         |   가상 네트워크 서브넷 삭제            |
|Microsoft.Network/virtualNetworks/subnets/join/action                    |   가상 네트워크 연결                     |
|Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action  |   서브넷에 대해 서비스 엔드포인트 사용     |
|Microsoft.Network/virtualNetworks/subnets/virtualMachines/read           |   서브넷의 가상 머신 가져오기       |

## <a name="next-steps"></a>다음 단계

- [PowerShell](powershell-samples.md) 또는 [Azure CLI](cli-samples.md) 샘플 스크립트를 사용하거나 Azure [리소스 관리자 템플릿](template-samples.md)을 사용하여 가상 네트워크 및 서브넷 만들기
- 가상 네트워크에 대 한 [정의 Azure Policy](policy-samples.md) 만들기 및 할당
