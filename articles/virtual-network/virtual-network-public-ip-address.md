---
title: Azure 공용 IP 주소 만들기, 변경 또는 삭제 | Microsoft Docs
description: 공용 IP 주소를 생성, 변경 또는 삭제 하는 방법에 대해 알아봅니다.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: bb71abaf-b2d9-4147-b607-38067a10caf6
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/06/2019
ms.author: kumud
ms.openlocfilehash: 38ff40b537730418fe9f0f8295884dae98a2fe0d
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74974141"
---
# <a name="create-change-or-delete-a-public-ip-address"></a>Nyilvános IP-címek létrehozása, módosítása és törlése

공용 IP 주소에 대해 알아보고, 공용 IP 주소를 만들고, 변경 하 고, 삭제 하는 방법에 대해 알아봅니다. 공용 IP 주소는 자체의 구성 가능한 설정이 있는 리소스입니다. 공용 ip 주소를 지 원하는 Azure 리소스에 공용 IP 주소를 할당 하면 다음과 같은 작업을 수행할 수 있습니다.
- 인터넷에서 리소스 (예: Azure Virtual Machines (VM), Azure 애플리케이션 게이트웨이, Azure 부하 분산 장치, Azure VPN gateway 및 기타)로의 인바운드 통신. Vm이 부하 분산 장치 백 엔드 풀의 일부이 고 부하 분산 장치에 공용 IP 주소가 할당 된 경우에는 인터넷에서 vm과 같은 일부 리소스 (예: vm에 할당 된 공용 IP 주소가 없는 경우)와 계속 통신할 수 있습니다. 특정 Azure 서비스에 대 한 리소스에 공용 IP 주소를 할당할 수 있는지 또는 다른 Azure 리소스의 공용 IP 주소를 통해 통신할 수 있는지 여부를 확인 하려면 서비스에 대 한 설명서를 참조 하세요.
- 예측 가능한 IP 주소를 사용 하 여 인터넷에 대 한 아웃 바운드 연결 예를 들어, 가상 머신은 공용 IP 주소를 할당 하지 않고 인터넷에 대 한 아웃 바운드 통신을 할 수 있지만, 해당 주소는 기본적으로 Azure에서 예측할 수 없는 공용 주소로 변환 된 네트워크 주소입니다. 공용 IP 주소를 리소스에 할당 하면 아웃 바운드 연결에 사용 되는 IP 주소를 알 수 있습니다. 예측 가능 하지만 선택한 할당 방법에 따라 주소가 변경 될 수 있습니다. 자세한 내용은 [공용 IP 주소 만들기](#create-a-public-ip-address)를 참조 하세요. Azure 리소스에서의 아웃 바운드 연결에 대해 자세히 알아보려면 [아웃 바운드 연결 이해](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json)를 참조 하세요.

## <a name="before-you-begin"></a>Előzetes teendők

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

이 문서의 섹션에서 단계를 완료 하기 전에 다음 작업을 완료 합니다.

- Azure 계정이 아직 없는 경우 [무료 평가판 계정](https://azure.microsoft.com/free)에 등록 합니다.
- 포털을 사용 하는 경우 https://portal.azure.com 를 열고 Azure 계정으로 로그인 합니다.
- PowerShell 명령을 사용 하 여이 문서의 작업을 완료 하는 경우 [Azure Cloud Shell](https://shell.azure.com/powershell)에서 명령을 실행 하거나 컴퓨터에서 powershell을 실행 합니다. Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. 이 자습서에는 Azure PowerShell 모듈 버전 1.0.0 이상이 필요 합니다. A telepített verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.
- Azure CLI (명령줄 인터페이스) 명령을 사용 하 여이 문서의 작업을 완료 하는 경우 [Azure Cloud Shell](https://shell.azure.com/bash)에서 명령을 실행 하거나 컴퓨터에서 cli를 실행 합니다. 이 자습서에는 Azure CLI 버전 2.0.31 이상이 필요 합니다. A telepített verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli). Azure CLI를 로컬로 실행 하는 경우 `az login`를 실행 하 여 Azure와 연결 해야 합니다.

를 사용 하 여 로그인 하거나 Azure에 연결 하는 계정은 [네트워크 참가자](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) 역할 또는 [사용 권한](#permissions)에 나열 된 적절 한 작업에 할당 된 [사용자 지정 역할](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 에 할당 되어야 합니다.

공용 IP 주소에는 명목 요금이 부과 됩니다. 가격 책정을 보려면 [IP 주소 가격 책정](https://azure.microsoft.com/pricing/details/ip-addresses) 페이지를 참조 하세요.

## <a name="create-a-public-ip-address"></a>Hozzon létre egy nyilvános IP-címet

1. Az Azure Portal menüjében vagy a **Kezdőlapon** lapon válassza az **Erőforrás létrehozása** elemet.
2. *Marketplace 검색* 상자에 *공용 ip 주소* 를 입력 합니다. 검색 결과에 표시 되는 **공용 IP 주소** 를 선택 합니다.
3. **공용 IP 주소**아래에서 **만들기**를 선택 합니다.
4. **공용 IP 주소 만들기**에서 다음 설정에 대 한 값을 입력 하거나 선택 하 고 **만들기**를 선택 합니다.

   |Beállítás|Kötelező?|Részletek|
   |---|---|---|
   |IP-verzió|Igen| IPv4 또는 IPv6을 선택 하거나 둘 다 선택 합니다. 둘 다 선택 하면 2 개의 공용 IP 주소가 생성 되 고 1 개의 IPv4 주소와 1 개의 IPv6 주소가 생성 됩니다. [Azure vnet의](../virtual-network/ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)i p v 6에 대 한 자세한 정보.|
   |SKU (Cikkszám)|Igen|Sku 도입 전에 생성 된 모든 공용 IP 주소는 **기본** SKU 공용 ip 주소입니다. 공용 IP 주소가 생성 된 후에는 SKU를 변경할 수 없습니다. 독립 실행형 가상 머신, 가용성 집합 내의 가상 머신 또는 가상 머신 확장 집합은 기본 또는 표준 Sku를 사용할 수 있습니다. 가용성 집합 또는 확장 집합 또는 독립 실행형 Vm 내의 가상 머신 간에 Sku를 혼합 하는 것은 허용 되지 않습니다. **기본** SKU: 가용성 영역을 지 원하는 지역에 공용 IP 주소를 만드는 경우 **가용성 영역** 설정이 기본적으로 *없음* 으로 설정 됩니다. 기본 공용 Ip는 가용성 영역을 지원 하지 않습니다. **표준** SKU: 표준 SKU 공용 IP는 가상 머신 또는 부하 분산 장치 프런트 엔드에 연결할 수 있습니다. 가용성 영역을 지 원하는 지역에 공용 IP 주소를 만드는 경우 **가용성 영역** 설정이 기본적으로 *영역 중복* 으로 설정 됩니다. 가용성 영역에 대 한 자세한 내용은 **가용성 영역** 설정을 참조 하세요. 표준 SKU는 주소를 표준 부하 분산 장치에 연결 하는 경우에 필요 합니다. 표준 부하 분산 장치에 대 한 자세한 내용은 [Azure 부하 분산 장치 표준 SKU](../load-balancer/load-balancer-standard-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)를 참조 하세요. Ha egy standard termékváltozatú nyilvános IP-címet hozzárendel egy virtuális gép hálózati adapteréhez, kifejezetten engedélyeznie kell a kívánt forgalmat egy [hálózati biztonsági csoporttal](security-overview.md#network-security-groups). Az erőforrással történő kommunikáció meghiúsul, amíg nem hoz létre és rendel hozzá egy hálózati biztonsági csoportot, és kifejezetten nem engedélyezi a kívánt forgalmat.|
   |Név|Igen|이름은 선택한 리소스 그룹 내에서 고유 해야 합니다.|
   |IP 주소 할당|Igen|**동적:** 동적 주소는 공용 IP 주소가 Azure 리소스에 연결 된 후에만 할당 되 고 리소스는 처음 시작 됩니다. 동적 주소는 가상 머신과 같은 리소스에 할당 된 경우, 가상 머신이 중지 (할당 취소) 된 후 다시 시작 될 때 변경 될 수 있습니다. 가상 컴퓨터를 다시 부팅 하거나 중지 하는 경우 (하지만 할당 취소 하지 않은 경우) 주소는 동일 하 게 유지 됩니다. 동적 주소는 공용 IP 주소 리소스가 연결 된 리소스에서 분리 될 때 릴리스됩니다. **정적:** 고정 주소는 공용 IP 주소를 만들 때 할당 됩니다. 고정 주소는 공용 IP 주소 리소스를 삭제할 때까지 해제 되지 않습니다. 주소가 리소스에 연결 되지 않은 경우에는 주소를 만든 후에 할당 방법을 변경할 수 있습니다. 주소가 리소스에 연결 된 경우 할당 방법을 변경 하지 못할 수 있습니다. **IP 버전으로 i p**v 6을 선택 하는 경우 기본 SKU에 *대 한 할당* 방법이 *동적* 이어야 합니다.  표준 SKU 주소는 IPv4 및 IPv6 둘 다에 대해 *고정* 됩니다. |
   |Üresjárat időkorlátja (perc)|Nem|클라이언트를 사용 하 여 연결 유지 메시지를 보내지 않고 TCP 또는 HTTP 연결을 열어 둘 수 있는 시간 (분)입니다. **IP 버전으로 i p**v 6을 선택 하는 경우이 값을 변경할 수 없습니다. |
   |DNS-névcímke|Nem|는 이름을 만든 Azure 위치 (모든 구독 및 모든 고객) 내에서 고유 해야 합니다. Azure는 이름 및 IP 주소를 DNS에 자동으로 등록 하므로 해당 이름을 사용 하 여 리소스에 연결할 수 있습니다. Azure는 사용자가 제공 하는 이름에 *location.cloudapp.azure.com* (여기서 location은 선택한 위치)와 같은 기본 서브넷을 추가 하 여 정규화 된 DNS 이름을 만듭니다. 두 주소 버전을 모두 만들도록 선택 하는 경우 동일한 DNS 이름이 IPv4 및 IPv6 주소 둘 다에 할당 됩니다. Azure의 기본 DNS는 IPv4 A 및 IPv6 AAAA 이름 레코드를 모두 포함 하며, DNS 이름을 조회할 때 두 레코드를 모두 사용 하 여 응답 합니다. 클라이언트는 통신할 주소 (IPv4 또는 IPv6)를 선택 합니다. Ahelyett vagy amellett, hogy az alapértelmezett utótaggal használná a DNS-név címkét, az Azure DNS szolgáltatással konfigurálhatja a DNS-nevet egy egyéni utótaggal, amelynek feloldása a nyilvános IP-címmé történik. További információt [az Azure DNS szolgáltatásnak nyilvános Azure IP-címmel történő használatával](../dns/dns-custom-domain.md?toc=%2fazure%2fvirtual-network%2ftoc.json#public-ip-address) foglalkozó témakörben tekinthet meg.|
   |이름 (IP **버전의 IP**버전을 선택 하는 경우에만 표시 됨)|예, **둘 다** 의 IP 버전을 선택 하는 경우|이름은이 **목록에서 이름에 대해** 입력 한 이름과 달라 야 합니다. IPv4 및 IPv6 주소를 둘 다 만들도록 선택 하면 포털에서 각각의 IP 주소 버전이 할당 된 두 개의 공용 IP 주소 리소스를 만듭니다.|
   |IP 주소 할당 (IP **버전의 Ip**버전을 선택 하는 경우에만 표시 됨)|예, **둘 다** 의 IP 버전을 선택 하는 경우|위의 IP 주소 할당과 동일한 제한 사항|
   |Előfizetés|Igen|공용 IP를 연결할 리소스와 동일한 [구독](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) 에 있어야 합니다.|
   |Erőforráscsoport|Igen|공용 IP를 연결할 리소스와 동일 하거나 다른 [리소스 그룹](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) 에 있을 수 있습니다.|
   |Földrajzi egység|Igen|공용 IP를 연결할 리소스와 동일한 [위치](https://azure.microsoft.com/regions)(지역이 라고도 함)에 있어야 합니다.|
   |A rendelkezésre állási zóna| Nem | 이 설정은 지원 되는 위치를 선택 하는 경우에만 표시 됩니다. 지원 되는 위치 목록은 [가용성 영역 개요](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)를 참조 하세요. **기본** SKU를 선택한 경우 *없음* 이 자동으로 선택 됩니다. 특정 영역을 보장 하려는 경우 특정 영역을 선택할 수 있습니다. 선택 항목 중 하나는 영역 중복이 아닙니다. **표준** SKU를 선택한 경우: 영역 중복이 자동으로 선택 되 고 영역 오류에 대 한 데이터 경로가 복원 됩니다. 영역 오류에 대 한 복원 력이 아닌 특정 영역을 보장 하려는 경우 특정 영역을 선택할 수 있습니다.

**도구**

포털에서 두 개의 공용 IP 주소 리소스 (IPv4 및 IPv6 하나)를 만드는 옵션을 제공 하지만 다음 CLI 및 PowerShell 명령은 하나의 IP 버전에 대 한 주소를 사용 하 여 하나의 리소스를 만듭니다. 각 IP 버전에 대해 하나씩 두 개의 공용 IP 주소 리소스를 원하는 경우, 공용 IP 주소 리소스에 대해 서로 다른 이름 및 IP 버전을 지정 하 여 명령을 두 번 실행 해야 합니다.

|Eszköz|Parancs|
|---|---|
|CLI|[az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create)|
|PowerShell|[AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress)|

## <a name="view-change-settings-for-or-delete-a-public-ip-address"></a>공용 IP 주소 보기, 설정 변경 또는 삭제

1. Azure Portal 맨 위에 텍스트 *검색 리소스가* 포함 된 상자에 *공용 ip 주소*를 입력 합니다. 검색 결과에 표시 되는 **공용 IP 주소** 를 선택 합니다.
2. 보기, 설정 변경 또는 목록에서 삭제 하려는 공용 IP 주소의 이름을 선택 합니다.
3. 공용 IP 주소를 확인, 삭제 또는 변경할 지 여부에 따라 다음 옵션 중 하나를 완료 합니다.
   - **보기**: **개요** 섹션에는 연결 된 네트워크 인터페이스 (주소가 네트워크 인터페이스에 연결 된 경우)와 같은 공용 IP 주소에 대 한 키 설정이 표시 됩니다. 포털에는 주소 버전 (IPv4 또는 IPv6)이 표시 되지 않습니다. 버전 정보를 보려면 PowerShell 또는 CLI 명령을 사용 하 여 공용 IP 주소를 확인 합니다. IP 주소 버전이 i p v 6 인 경우 할당 된 주소는 포털, PowerShell 또는 CLI에서 표시 되지 않습니다.
   - **삭제**: 공용 IP 주소를 삭제 하려면 **개요** 섹션에서 **삭제** 를 선택 합니다. 주소가 현재 IP 구성에 연결 되어 있으면 삭제할 수 없습니다. 주소가 현재 구성과 연결 되어 있으면 분리 **를 선택 하 여 IP** 구성에서 주소를 분리 합니다.
   - **변경**: **구성**을 선택 합니다. [공용 IP 주소 만들기](#create-a-public-ip-address)의 4 단계에 있는 정보를 사용 하 여 설정을 변경 합니다. IPv4 주소에 대 한 할당을 정적에서 동적으로 변경 하려면 먼저 연결 된 IP 구성에서 공용 IPv4 주소를 분리 해야 합니다. 그런 다음 할당 방법을 동적으로 변경 하 고 **연결** 을 선택 하 여 ip 주소를 동일한 ip 구성 또는 다른 구성에 연결 하거나 분리 된 상태로 둘 수 있습니다. 공용 IP 주소를 분리 하려면 **개요** 섹션에서 분리 **를 선택 합니다**.

   >[!WARNING]
   >할당 방법을 정적에서 동적으로 변경 하는 경우 공용 IP 주소에 할당 된 IP 주소가 손실 됩니다. Azure 공용 DNS 서버는 고정 또는 동적 주소와 모든 DNS 이름 레이블 (사용자가 정의한 경우) 간의 매핑을 유지 하지만, 가상 컴퓨터가 중지 (할당 취소) 된 상태에서 시작 되 면 동적 IP 주소가 변경 될 수 있습니다. 주소가 변경 되지 않도록 하려면 고정 IP 주소를 할당 합니다.

**도구**

|Eszköz|Parancs|
|---|---|
|CLI|공용 IP 주소를 나열 하는 [az network 공용 ip list](/cli/azure/network/public-ip#az-network-public-ip-list) , 설정을 표시 하는 [az network 공용 ip show](/cli/azure/network/public-ip#az-network-public-ip-show) 업데이트에 대 한 [az network 공용 ip 업데이트](/cli/azure/network/public-ip#az-network-public-ip-update) . 삭제 하려면 [az network public ip delete](/cli/azure/network/public-ip#az-network-public-ip-delete)|
|PowerShell|공용 IP 주소 개체를 검색 하 고 해당 설정을 [AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) [-AzPublicIpAddress](/powershell/module/az.network/set-azpublicipaddress) 를 설정 하 여 설정을 업데이트 합니다. 삭제할 [AzPublicIpAddress](/powershell/module/az.network/remove-azpublicipaddress)|

## <a name="assign-a-public-ip-address"></a>공용 IP 주소 할당

다음 리소스에 공용 IP 주소를 할당 하는 방법을 알아봅니다.

- [Windows](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 또는 [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) vm (만들 경우) 또는 [기존 vm](virtual-network-network-interface-addresses.md#add-ip-addresses) 에
- [인터넷 연결 Load Balancer](../load-balancer/load-balancer-get-started-internet-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Azure Application Gateway](../application-gateway/application-gateway-create-gateway-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Azure VPN Gateway를 사용 하 여 사이트 간 연결](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Azure 가상 머신 확장 집합](../virtual-machine-scale-sets/virtual-machine-scale-sets-portal-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

## <a name="permissions"></a>Engedélyek

공용 IP 주소에 대 한 작업을 수행 하려면 다음 표에 나열 된 적절 한 작업을 할당 받은 [네트워크 기여자](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) 역할 또는 [사용자 지정](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 역할에 계정을 할당 해야 합니다.

| Műveletek                                                             | Név                                                           |
| ---------                                                          | -------------                                                  |
| Microsoft. Network/publicIPAddresses/read                           | 공용 IP 주소 읽기                                          |
| Microsoft. Network/publicIPAddresses/write                          | 공용 IP 주소 만들기 또는 업데이트                           |
| Microsoft. Network/publicIPAddresses/delete                         | 공용 IP 주소 삭제                                     |
| Microsoft. Network/publicIPAddresses/join/action                    | 리소스에 공용 IP 주소 연결                    |

## <a name="next-steps"></a>Következő lépések

- [PowerShell](powershell-samples.md) 또는 [Azure CLI](cli-samples.md) 샘플 스크립트를 사용 하거나 Azure [리소스 관리자 템플릿을](template-samples.md) 사용 하 여 공용 IP 주소 만들기
- 공용 IP 주소에 대 한 [Azure 정책](policy-samples.md) 만들기 및 적용
