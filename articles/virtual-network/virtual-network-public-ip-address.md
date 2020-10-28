---
title: 공용 IP 주소 관리 | Microsoft Docs
titleSuffix: Azure Virtual Network
description: 공용 IP 주소를 관리합니다.  또한 공용 IP 주소를 구성 가능한 고유한 설정을 사용 하는 리소스인 경우를 알아봅니다.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
editor: ''
tags: azure-resource-manager
ms.assetid: bb71abaf-b2d9-4147-b607-38067a10caf6
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/06/2019
ms.author: kumud
ms.openlocfilehash: a8847768cf4cf1ea85df8646a8848aac02cbd621
ms.sourcegitcommit: 3e8058f0c075f8ce34a6da8db92ae006cc64151a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92629258"
---
# <a name="manage-public-ip-addresses"></a>공용 IP 주소 관리

공용 IP 주소 및 해당 IP 주소를 생성, 변경 및 삭제하는 방법에 대해 알아봅니다. 공용 IP 주소는 자체적으로 구성 가능한 설정을 사용하는 리소스입니다. 공용 IP 주소를 공용 IP 주소를 지원하는 Azure 리소스에 할당하면 다음을 수행할 수 있습니다.
- Azure Virtual Machines (VM), Azure Application Gateways, Azure Load Balancer, Azure VPN Gateways 등 인터넷에서 리소스로 인바운드 통신. VM에 공용 IP 주소가 할당되어 있지 않은 경우라도 VM이 부하 분산 장치 백엔드 풀의 일부이고 부하 분산 장치에 공용 IP 주소가 할당되어 있는 한, 인터넷에서 VM 등의 일부 리소스와 계속 통신할 수 있습니다. 특정 Azure 서비스의 리소스에 공용 IP 주소를 할당할 수 있는지 또는 다른 Azure 리소스의 공용 IP 주소를 통해 통신할 수 있는지 여부를 확인하려면 서비스 설명서를 참조하세요.
- 예측 가능한 IP 주소를 사용하는 인터넷으로의 아웃바운드 연결. 예를 들어 공용 IP 주소가 할당되지 않은 가상 머신도 인터넷으로의 아웃바운드 통신을 수행할 수는 있지만, 이 가상 머신의 주소는 기본적으로 Azure에서 예측할 수 없는 공용 주소로 변환한 네트워크 주소입니다. 공용 IP 주소를 리소스에 할당하면 아웃바운드 연결에 사용되는 IP 주소를 확인할 수 있습니다. 이 주소는 예측은 가능하지만 선택한 할당 방법에 따라 변경될 수 있습니다. 자세한 내용은 [공용 IP 주소 만들기](#create-a-public-ip-address)를 참조하세요. Azure 리소스의 아웃바운드 연결에 대한 자세한 내용은 [아웃바운드 연결 이해](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json)를 참조하세요.

## <a name="before-you-begin"></a>시작하기 전에

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

이 문서에서 설명하는 모든 섹션의 단계를 수행하기 전에 다음 작업을 완료해야 합니다.

- 아직 Azure 계정이 없으면 [평가판 계정](https://azure.microsoft.com/free)에 등록합니다.
- 포털을 사용하는 경우 https://portal.azure.com을 열고 Azure 계정으로 로그인합니다.
- 이 문서의 작업을 완료하기 위해 PowerShell 명령을 사용하는 경우 [Azure Cloud Shell](https://shell.azure.com/powershell)에서 명령을 실행하거나 컴퓨터에서 PowerShell을 실행합니다. Azure Cloud Shell은 이 항목의 단계를 실행하는 데 무료로 사용할 수 있는 대화형 셸입니다. 공용 Azure 도구가 사전 설치되어 계정에서 사용하도록 구성되어 있습니다. 이 자습서에는 Azure PowerShell 모듈 버전 1.0.0 이상이 필요합니다. 설치되어 있는 버전을 확인하려면 `Get-Module -ListAvailable Az`을 실행합니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-az-ps)를 참조하세요. 또한 PowerShell을 로컬로 실행하는 경우 `Connect-AzAccount`를 실행하여 Azure와 연결해야 합니다.
- 이 문서의 작업을 완료하기 위해 Azure CLI(명령줄 인터페이스)를 사용하는 경우 [Azure Cloud Shell](https://shell.azure.com/bash)에서 명령을 실행하거나 컴퓨터에서 CLI를 실행합니다. 이 자습서에는 Azure CLI 버전 2.0.31 이상이 필요합니다. 설치되어 있는 버전을 확인하려면 `az --version`을 실행합니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요. 또한 Azure CLI를 로컬로 실행하는 경우 `az login`를 실행하여 Azure와 연결해야 합니다.

를 사용 하 여 로그인 하거나 Azure에 연결 하는 계정은 [네트워크 참가자](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) 역할 또는 [사용 권한](#permissions)에 나열 된 적절 한 작업에 할당 된 [사용자 지정 역할](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 에 할당 되어야 합니다.

공용 IP 주소에는 명목 요금이 부과됩니다. 가격을 보려면 [IP 주소 가격](https://azure.microsoft.com/pricing/details/ip-addresses) 페이지를 참조하세요.

## <a name="create-a-public-ip-address"></a>공용 IP 주소 만들기

포털, PowerShell 또는 CLI를 사용 하 여 공용 IP 주소를 만드는 방법에 대 한 자세한 내용은 다음 페이지를 참조 하세요.

 * [공용 IP 주소 만들기 - 포털](https://docs.microsoft.com/azure/virtual-network/create-public-ip-portal?tabs=option-create-public-ip-standard-zones)
 * [공용 IP 주소 만들기 - PowerShell](https://docs.microsoft.com/azure/virtual-network/create-public-ip-powershell?tabs=option-create-public-ip-standard-zones)
 * [공용 IP 주소 만들기 - Azure CLI](https://docs.microsoft.com/azure/virtual-network/create-public-ip-cli?tabs=option-create-public-ip-standard-zones)

>[!NOTE]
>포털에서 두 개의 공용 IP 주소 리소스 (IPv4 및 IPv6 하나)를 만드는 옵션을 제공 하지만 PowerShell 및 CLI 명령은 하나의 IP 버전에 대 한 주소를 사용 하 여 하나의 리소스를 만듭니다. 각 IP 버전에 대해 하나씩 두 개의 공용 IP 주소 리소스를 원하는 경우, 공용 IP 주소 리소스에 대해 서로 다른 이름 및 IP 버전을 지정 하 여 명령을 두 번 실행 해야 합니다.

만드는 동안 공용 IP 주소의 특정 특성에 대 한 자세한 내용은 아래 표를 참조 하세요.

   |설정|필수 여부|세부 정보|
   |---|---|---|
   |IP 버전|예| IPv4 또는 IPv6을 선택 하거나 둘 다 선택 합니다. 둘 다 선택 하면 2 개의 공용 IP 주소가 생성 되 고 1 개의 IPv4 주소와 1 개의 IPv6 주소가 생성 됩니다. [Azure vnet의](../virtual-network/ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)i p v 6에 대 한 자세한 정보.|
   |SKU|예|Sku 도입 전에 생성 된 모든 공용 IP 주소는 **기본** SKU 공용 ip 주소입니다. 공용 IP 주소를 만든 후에는 SKU를 변경할 수 없습니다. 독립 실행형 가상 머신, 가용성 집합 내의 가상 머신 또는 가상 머신 확장 집합은 기본 또는 표준 SKU를 사용할 수 있습니다. 가용성 집합 또는 확장 집합 또는 독립 실행형 Vm 내의 가상 머신 간에 Sku를 혼합 하는 것은 허용 되지 않습니다. **기본** SKU: 가용성 영역을 지원하는 지역에 공용 IP 주소를 만드는 경우 **가용성 영역** 설정이 기본적으로 *없음* 으로 설정됩니다. 기본 공용 Ip는 가용성 영역을 지원 하지 않습니다. **표준** SKU: 표준 SKU 공용 IP를 가상 머신 또는 부하 분산 장치 프런트 엔드에 연결할 수 있습니다. 가용성 영역을 지원하는 지역에 공용 IP 주소를 만드는 경우 **가용성 영역** 설정이 기본적으로 *영역 중복* 으로 설정됩니다. 가용성 영역에 대한 자세한 내용은 **가용성 영역** 설정을 참조하세요. 주소를 표준 부하 분산 장치에 연결하는 경우에 표준 SKU가 필요합니다. 표준 부하 분산 장치에 대한 자세한 내용은 [Azure Load Balancer 표준 SKU](../load-balancer/load-balancer-standard-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)를 참조하세요. 가상 머신의 네트워크 인터페이스에 표준 SKU 공용 IP 주소를 할당할 때 [네트워크 보안 그룹](security-overview.md#network-security-groups)을 사용하여 원하는 트래픽을 명시적으로 허용해야 합니다. 네트워크 보안 그룹을 만들어 연결하고 원하는 트래픽을 명시적으로 허용해야 리소스와 통신할 수 있습니다.|
   |이름|예|이름은 선택한 리소스 그룹 내에서 고유해야 합니다.|
   |IP 주소 할당|예|**동적:** 동적 주소는 공용 IP 주소가 Azure 리소스에 연결되고 리소스를 처음 시작한 후에만 할당됩니다. 동적 주소는 가상 머신과 같은 리소스에 할당되고 가상 머신이 중지(할당 취소)되었다 다시 시작된 경우 변경될 수 있습니다. 가상 머신을 다시 부팅하거나 중지하는 경우(하지만 할당 취소하지 않은 경우) 주소가 동일하게 유지됩니다. 동적 주소는 공용 IP 주소 리소스가 연결된 리소스에서 분리될 때 해제됩니다. **고정:** 공용 IP 주소가 생성되면 고정 주소가 할당됩니다. 고정 주소는 공용 IP 주소 리소스가 삭제될 때까지 해제되지 않습니다. 주소가 리소스에 연결되지 않은 경우에는 주소가 생성된 후 할당 방법을 변경할 수 있습니다. 주소가 리소스에 연결된 경우에는 할당 방법을 변경할 수 없습니다. **IP 버전으로 i p** v 6을 선택 하는 경우 기본 SKU에 *대 한 할당* 방법이 *동적* 이어야 합니다.  표준 SKU 주소는 IPv4 및 IPv6 둘 다에 대해 *고정* 됩니다. |
   |유휴 제한 시간(분)|아니요|연결 유지 메시지를 보내는 데 클라이언트를 사용하지 않고 TCP 또는 HTTP 연결을 유지하는 데 걸리는 시간(분)입니다. **IP 버전** 으로 IPv6을 선택하는 경우에는 이 값을 변경할 수 없습니다. |
   |DNS 이름 레이블|아니요|이름을 만드는 Azure 위치 내에서(모든 구독 및 모든 고객에서) 고유해야 합니다. Azure는 해당 DNS에서 이름과 IP 주소를 자동으로 등록하므로 해당 이름을 사용하는 리소스에 연결할 수 있습니다. Azure에서는 정규화된 DNS 이름을 만드는 데 제공하는 이름에 *location.cloudapp.azure.com* (여기서 location은 선택한 위치임)과 같은 기본 서브넷을 추가합니다. 두 주소 버전을 모두 만드는 경우 IPv4 및 IPv6 주소 둘 다에 같은 DNS 이름이 할당됩니다. Azure의 기본 DNS는 IPv4 A 및 IPv6 AAAA 이름 레코드를 모두 포함하며, DNS 이름을 조회할 때 응답으로 두 레코드를 모두 전송합니다. 클라이언트는 어떤 주소(IPv4 또는 IPv6)와 통신할지 선택합니다. 기본 접미사로 DNS 이름 레이블을 사용하는 것 대신 또는 그 외에 Azure DNS 서비스를 사용하여 공용 IP 주소로 확인하는 사용자 지정 접미사로 DNS 이름을 구성할 수 있습니다. 자세한 내용은 [Azure 공용 IP 주소로 Azure DNS 사용](../dns/dns-custom-domain.md?toc=%2fazure%2fvirtual-network%2ftoc.json#public-ip-address)을 참조하세요.|
   |이름 (IP **버전의 IP** 버전을 선택 하는 경우에만 표시 됨)|예, **둘 다** 의 IP 버전을 선택 하는 경우|이름은 이 목록의 첫 번째 **이름** 으로 입력하는 이름과 달라야 합니다. IPv4 주소와 IPv6 주소를 모두 만들도록 선택하면 Portal에서는 각 IP 주소 버전이 할당된 개별 공용 IP 주소 리소스 두 개를 만듭니다.|
   |IP 주소 할당 (IP **버전의 Ip** 버전을 선택 하는 경우에만 표시 됨)|예, **둘 다** 의 IP 버전을 선택 하는 경우|위의 IP 주소 할당과 동일한 제한 사항|
   |Subscription|예|공용 IP를 연결할 리소스와 동일한 [구독](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) 에 있어야 합니다.|
   |Resource group|예|공용 IP를 연결할 리소스와 동일 하거나 다른 [리소스 그룹](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) 에 있을 수 있습니다.|
   |위치|예|공용 IP를 연결할 리소스와 동일한 [위치](https://azure.microsoft.com/regions)(지역이 라고도 함)에 있어야 합니다.|
   |가용성 영역| 아니요 | 이 설정은 지원되는 위치를 선택하는 경우에만 나타납니다. 지원되는 위치 목록은 [가용성 영역 개요](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)를 참조하세요. **기본** SKU를 선택한 경우 *없음* 이 자동으로 선택됩니다. 특정 영역을 보장하려는 경우 특정 영역을 선택할 수 있습니다. 두 선택 중 하나는 영역 중복이 아닙니다. **표준** SKU를 선택한 경우: 영역 중복이 자동으로 선택되며, 영역 오류 시 데이터 경로가 복원 가능합니다. 영역 오류 시 복원 불가능한 특정 영역을 보장하려는 경우 특정 영역을 선택할 수 있습니다.

## <a name="view-modify-settings-for-or-delete-a-public-ip-address"></a>공용 IP 주소 보기, 설정 수정 또는 삭제

   - **보기/목록** : SKU, 주소, 적용 가능한 모든 연결 (예: 가상 컴퓨터 NIC, Load Balancer 프런트 엔드)을 포함 하 여 공용 IP에 대 한 설정을 검토 합니다.
   - **수정** : 유휴 시간 제한, DNS 이름 레이블 또는 할당 방법과 같은 [공용 IP 주소 만들기](#create-a-public-ip-address)의 4 단계 정보를 사용 하 여 설정을 수정 합니다.  (기본에서 표준으로 공용 IP SKU를 업그레이드 하는 전체 프로세스는 [Azure 공용 ip 주소 업그레이드](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address-upgrade)를 참조 하세요.)
   >[!WARNING]
   >공용 IP 주소에 대 한 할당을 정적에서 동적으로 변경 하려면 먼저 해당 IP 구성에서 주소를 분리 해야 합니다 ( **Delete** 섹션 참조).  또한 할당 방법을 정적에서 동적으로 변경 하는 경우 공용 IP 주소에 할당 된 IP 주소가 손실 됩니다. Azure 공용 DNS 서버는 고정 또는 동적 주소와 모든 DNS 이름 레이블(사용자가 정의한 경우) 간의 매핑을 유지하지만, 가상 머신을 중지(할당 취소)된 상태에서 시작할 때 동적 IP 주소가 변경될 수 있습니다. 주소가 변경되지 않도록 하려면 고정 IP 주소를 할당합니다.
   
|작업(Operation)|Azure portal|Azure PowerShell|Azure CLI|
|---|---|---|---|
|보기 | 공용 IP의 **개요** 섹션에서 |공용 IP 주소 개체를 검색 하 고 해당 설정을 확인 하는 [AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress)| [az network 공용-ip show](/cli/azure/network/public-ip#az-network-public-ip-show) 설정을 표시 합니다.|
|목록 | **공용 IP 주소** 범주 아래에서 |[AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) 는 하나 이상의 공용 IP 주소 개체를 검색 하 고 해당 설정을 볼 수 있습니다.|공용 IP 주소를 나열 하는 [az network public ip list](/cli/azure/network/public-ip#az-network-public-ip-list)|
|수정 | 분리 된 IP의 경우 **구성** 을 선택 하 여 유휴 시간 제한, DNS 이름 레이블 또는 기본 IP의 할당을 정적에서 동적으로 수정 합니다.  |[AzPublicIpAddress](/powershell/module/az.network/set-azpublicipaddress) 설정 업데이트 |업데이트에 대 한 [az network 공용 ip 업데이트](/cli/azure/network/public-ip#az-network-public-ip-update) |

   - **삭제** : 공용 ip를 삭제 하려면 공용 IP 개체가 IP 구성 또는 가상 머신 NIC에 연결 되어 있지 않아야 합니다. 자세한 내용은 아래 표를 참조 하세요.

|리소스|Azure portal|Azure PowerShell|Azure CLI|
|---|---|---|---|
|[Virtual Machine](https://docs.microsoft.com/azure/virtual-network/remove-public-ip-address-vm)|분리를 **선택 하 여** NIC 구성에서 IP 주소를 분리 한 다음, **삭제** 를 선택 합니다.|NIC 구성에서 IP 주소를 분리 하도록 [AzPublicIpAddress을 설정](/powershell/module/az.network/set-azpublicipaddress) 합니다. 삭제할 [AzPublicIpAddress](/powershell/module/az.network/remove-azpublicipaddress)|[az network 공용-ip update--를 제거](/cli/azure/network/public-ip#az-network-public-ip-update) 하 여 NIC 구성에서 ip 주소를 분리 합니다. 삭제 하려면 [az network public ip delete](/cli/azure/network/public-ip#az-network-public-ip-delete) |
|Load Balancer 프런트 엔드 | 사용 하지 않는 공용 IP 주소로 이동 하 고 **연결** 을 선택 하 고 관련 프런트 엔드 ip 구성을 사용 하 여 Load Balancer을 선택 하 여 대체 합니다 (VM과 동일한 방법을 사용 하 여 이전 IP를 삭제할 수 있음).  | [AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/set-azloadbalancerfrontendipconfig) 를 사용 하 여 새 프런트 엔드 IP 구성을 Public Load Balancer와 연결 합니다. 삭제할 [AzPublicIpAddress](/powershell/module/az.network/remove-azpublicipaddress) 또한 [AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/remove-azloadbalancerfrontendipconfig) 를 사용 하 여 프런트 엔드 IP 구성이 두 개 이상 있는 경우 제거할 수 있습니다. |[az network lb 프런트 엔드-ip 업데이트](/cli/azure/network/lb/frontend-ip?view=azure-cli-latest#az_network_lb_frontend_ip_update) 를 사용 하 여 새 프런트 엔드 Ip 구성을 공용 Load Balancer와 연결 합니다. 삭제할 [AzPublicIpAddress](/powershell/module/az.network/remove-azpublicipaddress) [az network lb 프런트 엔드-ip delete](/cli/azure/network/lb/frontend-ip?view=azure-cli-latest#az_network_lb_frontend_ip_delete) 를 사용 하 여 프런트 엔드 ip 구성이 두 개 이상 있는 경우이를 제거할 수도 있습니다.|
|방화벽|해당 없음| [할당 취소 ()](https://docs.microsoft.com/azure/firewall/firewall-faq#how-can-i-stop-and-start-azure-firewall) 하 여 방화벽 할당을 취소 하 고 모든 IP 구성 제거 | [az network firewall ip-https delete-](/cli/azure/ext/azure-firewall/network/firewall/ip-config#ext_azure_firewall_az_network_firewall_ip_config_delete) ip를 제거 하려면 PowerShell을 사용 하 여 먼저 할당을 취소 해야 합니다.|

## <a name="virtual-machine-scale-sets"></a>Virtual Machine Scale Sets

공용 ip를 사용 하는 가상 머신 확장 집합을 사용 하는 경우 개별 가상 머신 인스턴스와 연결 된 별도의 공용 IP 개체가 없습니다. 그러나 공용 IP 접두사 개체를 [사용 하 여 인스턴스 ip를 생성할 수 있습니다](https://azure.microsoft.com/resources/templates/101-vmms-with-public-ip-prefix/).

가상 머신 확장 집합에서 공용 Ip를 나열 하려면 PowerShell ([AzPublicIpAddress-VirtualMachineScaleSetName](/powershell/module/az.network/get-azpublicipaddress)) 또는 CLI ([az vmss list-instance-ip](/cli/azure/vmss?view=azure-cli-latest#az_vmss_list_instance_public_ips))를 사용할 수 있습니다.

자세한 내용은 [Azure 가상 머신 확장 집합에 대한 네트워킹](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-networking#public-ipv4-per-virtual-machine)을 참조하세요.

## <a name="assign-a-public-ip-address"></a>공용 IP 주소 할당

다음 리소스에 공용 IP 주소를 할당하는 방법을 알아봅니다.

- [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 또는 [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 가상 머신 (만들 때) 또는 [기존 가상 머신에](virtual-network-network-interface-addresses.md#add-ip-addresses)
- [공용 Load Balancer](../load-balancer/load-balancer-get-started-internet-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Application Gateway](../application-gateway/application-gateway-create-gateway-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [VPN Gateway를 사용 하 여 사이트 간 연결](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [가상 머신 확장 집합](../virtual-machine-scale-sets/virtual-machine-scale-sets-portal-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

## <a name="permissions"></a>권한

공용 IP 주소에 대한 작업을 수행하려면 다음 표에 나열된 적절한 작업이 할당된 [사용자 지정](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 역할 또는 [네트워크 참가자](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) 역할에 계정이 할당되어야 합니다.

| 작업                                                             | Name                                                           |
| ---------                                                          | -------------                                                  |
| Microsoft.Network/publicIPAddresses/read                           | 공용 IP 주소 읽기                                          |
| Microsoft.Network/publicIPAddresses/write                          | 공용 IP 주소 만들기 또는 업데이트                           |
| Microsoft.Network/publicIPAddresses/delete                         | 공용 IP 주소 삭제                                     |
| Microsoft.Network/publicIPAddresses/join/action                    | 공용 IP 주소를 리소스에 연결                    |

## <a name="next-steps"></a>다음 단계

- [PowerShell](powershell-samples.md) 또는 [Azure CLI](cli-samples.md) 샘플 스크립트를 사용하거나 Azure [Resource Manager 템플릿](template-samples.md)을 사용하여 공용 IP 주소 만들기
- 공용 IP 주소에 대 한 [Azure Policy 정의](policy-samples.md) 만들기 및 할당
