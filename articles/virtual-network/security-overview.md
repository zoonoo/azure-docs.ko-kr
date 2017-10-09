---
title: "Azure 네트워크 보안 개요 | Microsoft Docs"
description: "Azure 리소스 간에 네트워크 트래픽 흐름을 제어하기 위한 보안 옵션에 대해 알아봅니다."
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/19/2017
ms.author: jdial
ms.translationtype: HT
ms.sourcegitcommit: 0e862492c9e17d0acb3c57a0d0abd1f77de08b6a
ms.openlocfilehash: 63a313d9035422207a1ce56f0da8b388e2747685
ms.contentlocale: ko-kr
ms.lasthandoff: 09/27/2017

---
# <a name="network-security"></a>네트워크 보안

네트워크 보안 그룹을 사용하여 가상 네트워크에서 리소스에 대한 네트워크 트래픽을 제한할 수 있습니다. 네트워크 보안 그룹에는 원본 또는 대상 IP 주소, 포트 및 프로토콜에 따라 인바운드 또는 아웃바운드 네트워크 트래픽을 허용하거나 거부하는 보안 규칙 목록이 포함되어 있습니다. 

## <a name="network-security-groups"></a>네트워크 보안 그룹

각 네트워크 인터페이스에는 하나의 연결된 네트워크 보안 그룹이 있거나 없을 수 있습니다. 각 네트워크 인터페이스는 [가상 네트워크](virtual-networks-overview.md) 서브넷에 위치합니다. 서브넷에는 하나의 연결된 네트워크 보안 그룹이 있거나 없을 수 있습니다. 

보안 규칙이 서브넷에 적용되는 경우 서브넷에 있는 모든 리소스에 적용됩니다. 네트워크 인터페이스 외에도 HDInsight, 가상 컴퓨터 확장 집합, 및 서브넷에 배포된 응용 프로그램 서비스 환경과 같이 다른 Azure 서비스의 인스턴스가 있을 수 있습니다.

네트워크 보안 그룹이 네트워크 인터페이스 및 해당 네트워크 인터페이스가 위치한 서브넷에 연결된 경우 해당 네트워크 보안 그룹을 적용하는 방법은 다음과 같습니다.

- **인바운드 트래픽**: 네트워크 인터페이스가 위치한 서브넷에 연결된 네트워크 보안 그룹을 먼저 평가합니다. 서브넷에 연결된 네트워크 보안 그룹을 통해 허용된 모든 트래픽은 네트워크 인터페이스에 연결된 네트워크 보안 그룹에 의해 평가됩니다. 예를 들어 인터넷의 포트 80을 통해 가상 컴퓨터에 대한 인바운드 액세스가 필요할 수 있습니다. 네트워크 인터페이스 및 해당 네트워크 인터페이스가 위치한 서브넷에 네트워크 보안 그룹을 연결하는 경우 서브넷에 연결된 네트워크 보안 그룹 및 네트워크 인터페이스는 포트 80을 허용해야 합니다. 서브넷 또는 해당 서브넷이 위치한 네트워크 인터페이스에 연결된 네트워크 보안 그룹을 통한 포트 80만을 허용하는 경우 기본 보안 규칙으로 인해 통신에 실패합니다. 자세한 내용은 [기본 보안 규칙](#default-security-rules)을 참조하세요. 예를 들어 서브넷 또는 네트워크 인터페이스에 대한 네트워크 보안 그룹 및 포트 80 인바운드 트래픽을 허용하는 규칙을 포함하는 네트워크 보안 그룹에만 적용한 경우 통신에 성공합니다. 
- **아웃바운드 트래픽**: 네트워크 인터페이스에 연결된 네트워크 보안 그룹을 먼저 평가합니다. 네트워크 인터페이스에 연결된 네트워크 보안 그룹을 통해 허용된 모든 트래픽은 서브넷에 연결된 네트워크 보안 그룹에 의해 평가됩니다.

네트워크 인터페이스와 서브넷에 네트워크 보안 그룹이 적용되는 시점을 항상 알 수는 없습니다. 네트워크 인터페이스의 [유효 보안 규칙](virtual-network-manage-nsg-arm-portal.md)을 확인하여 네트워크 인터페이스에 적용되는 집계 규칙을 쉽게 볼 수 있습니다. Azure Network Watcher에서 [IP 흐름 확인](../network-watcher/network-watcher-check-ip-flow-verify-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 기능을 사용하여 통신이 네트워크 인터페이스 간에 허용되는지 여부를 결정할 수도 있습니다. 이 도구에서는 통신이 허용되는지 여부 및 어떤 네트워크 보안 규칙이 트래픽을 허용하거나 거부하는지를 알려줍니다.
 
> [!NOTE]
> 네트워크 보안 그룹이 Resource Manager 배포 모델에서 네트워크 인터페이스가 아닌 배포 클래식 배포 모델에 배포된 서브넷 또는 가상 컴퓨터와 클라우드 서비스에 연결됩니다. Azure 배포 모델에 대해 자세히 알아보려면 [Azure 배포 모델 이해](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json)를 참조하세요.

동일한 네트워크 보안 그룹은 선택한 만큼의 개별 네트워크 인터페이스 및 서브넷에 적용될 수 있습니다.

## <a name="security-rules"></a>보안 규칙

네트워크 보안 그룹은 Azure 구독 [제한](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) 내에서 필요한 만큼 0개 또는 많은 규칙을 포함합니다. 각 규칙은 다음 속성을 지정합니다.

|속성  |설명  |
|---------|---------|
|이름|네트워크 보안 그룹 내에서 고유한 이름입니다.|
|우선 순위 | 100~4096 사이의 숫자입니다. 낮은 번호의 우선 순위가 더 높기 때문에 규칙은 낮은 번호가 높은 번호보다 먼저 처리되는 우선 순위 순서로 처리됩니다. 트래픽이 규칙과 일치하면 처리가 중지됩니다. 따라서 우선 순위가 높은 규칙과 동일한 특성을 가진 우선 순위가 낮은 규칙(높은 번호)은 처리되지 않습니다.|
|원본 또는 대상| 모두 또는 개별 IP 주소, CIDR 블록(예: 예제 10.0.0.0/24), 서비스 태그 또는 응용 프로그램 보안 그룹입니다. [태그 서비스](#service-tags) 및 [응용 프로그램 보안 그룹](#application-security-groups)에 대해 자세히 알아봅니다. 범위, 서비스 태그 또는 응용 프로그램 보안 그룹을 지정하면 더 적은 보안 규칙을 만들어도 됩니다. 규칙에서 여러 개별 IP 주소와 범위를 지정하는 기능(여러 서비스 태그 또는 응용 프로그램 그룹을 지정할 수 없음)은 미리 보기 릴리스 상태이며 보강된 보안 규칙이라고 합니다. 보강된 보안 규칙은 Resource Manager 배포 모델을 통해 만들어진 네트워크 보안 그룹에서만 만들 수 있습니다. 클래식 배포 모델을 통해 만든 네트워크 보안 그룹에서는 여러 개의 IP 주소 및 IP 주소 범위를 지정할 수 없습니다.|
|프로토콜     | TCP, UDP 또는 TCP, UDP 및 ICMP를 포함하는 모두입니다. 단독으로 ICMP를 지정할 수 없으므로 ICMP가 필요한 경우 모두를 사용합니다. |
|방향| 규칙이 인바운드 또는 아웃바운드 트래픽에 적용되는지 여부입니다.|
|포트 범위     |개별 포트나 포트의 범위를 지정할 수 있습니다. 예를 들어 80 또는 10000-10005과 같이 지정할 수 있습니다. 범위를 지정하면 더 적은 보안 규칙을 만들어도 됩니다. 규칙에서 여러 개별 포트와 포트 범위를 지정하는 기능은 미리 보기 릴리스 상태이며 보강된 보안 규칙으로 참조됩니다. 보강된 보안 규칙을 사용하기 전에 중요한 정보는 [미리 보기 기능](#preview-features)을 참고하세요. 보강된 보안 규칙은 Resource Manager 배포 모델을 통해 만들어진 네트워크 보안 그룹에서만 만들 수 있습니다. 클래식 배포 모델을 통해 만든 네트워크 보안 그룹에서는 동일한 보안 규칙에 여러 개의 포트 또는 포트 범위를 지정할 수 없습니다.   |
|동작     | 허용 또는 거부        |

**고려 사항**

- **호스트 노드의 가상 IP:** DHCP, DNS 및 상태 모니터링과 같은 기본 인프라 서비스는 가상화된 호스트 IP 주소 168.63.129.16 및 169.254.169.254를 통해 제공됩니다. 이러한 공용 IP 주소는 Microsoft에 속하며, 이 용도로 모든 지역에서 유일하게 사용되는 가상화된 IP 주소입니다. 이 IP 주소는 VM을 호스트하는 서버 컴퓨터(호스트 노드)의 실제 IP 주소에 매핑됩니다. 호스트 노드는 DHCP 릴레이, DNS 재귀 확인자, 부하 분산 장치 상태 검색 및 컴퓨터 상태 검색에 대한 검색 소스 등의 역할을 합니다. 이러한 IP 주소의 통신은 공격이 아닙니다. 이러한 IP 주소 간에 트래픽을 차단하는 경우 가상 컴퓨터가 제대로 작동하지 않을 수 있습니다.
- **라이선싱(키 관리 서비스)**: VM에서 실행되는 Windows 이미지를 사용하려면 라이선스가 있어야 합니다. 사용 허가를 위해 라이선스 요청이 이러한 쿼리를 처리하는 키 관리 서비스 호스트 서버로 전송됩니다. 요청은 1688 포트를 통해 아웃바운드로 수행됩니다.
- **부하가 분산된 풀의 가상 컴퓨터**: 적용되는 원본 포트와 주소 범위는 부하 분산 장치가 아닌 원래 컴퓨터에서 가져옵니다. 대상 포트와 주소 범위는 부하 분산 장치가 아닌 대상 컴퓨터에서 가져옵니다.
- **Azure 서비스 인스턴스**: HDInsight, 응용 프로그램 서비스 환경 및 가상 컴퓨터 확장 집합과 같은 몇 가지 Azure 서비스의 인스턴스는 가상 네트워크 서브넷에 배포됩니다. 리소스를 배포한 서브넷에 네트워크 보안 그룹을 적용하기 전에 각 서비스에 대한 포트 요구 사항을 잘 이해하도록 합니다. 서비스에 필요한 포트를 거부하는 경우 서비스가 제대로 작동하지 않습니다. 

보안 규칙은 상태 저장입니다. 예를 들어 포트 80을 통해 모든 주소에 대한 아웃바운드 보안 규칙을 지정하는 경우 아웃바운드 트래픽에 대한 응답에 인바운드 보안 규칙을 지정하지 않아도 됩니다. 통신이 외부에서 시작된 경우 인바운드 보안 규칙을 지정하기만 하면 됩니다. 반대의 경우도 마찬가지입니다. 포트를 통해 인바운드 트래픽이 허용되는 경우 포트를 통해 트래픽에 응답하도록 아웃바운드 보안 규칙을 지정하지 않아도 됩니다. 보안 규칙을 만들 경우 제한에 대한 자세한 내용은 [Azure 제한](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)을 참조하세요.

## <a name="augmented-security-rules"></a>보강된 보안 규칙

보강된 규칙은 가상 네트워크에 대한 보안 정의를 간소화하여 더 적은 규칙으로 크고 복잡한 네트워크 보안 정책을 정의할 수 있도록 합니다. 여러 개의 포트, 여러 개의 명시적 IP 주소, 서비스 태그 및 응용 프로그램 보안 그룹을 이해하기 쉬운 단일 보안 규칙으로 결합할 수 있습니다. 규칙의 원본, 대상 및 포트 필드에서 보강된 규칙을 사용합니다. 규칙을 만들 때 여러 명시적 IP 주소, CIDR 범위 및 포트를 지정할 수 있습니다. 보안 규칙 정의를 간단히 유지 관리하려면 보강된 보안 규칙을 서비스 태그 또는 응용 프로그램 보안 그룹과 결합합니다. 

보강된 보안 규칙은 미리 보기 릴리스에서 사용할 수 있습니다. 보강된 보안 규칙을 만들 경우 제한에 대한 자세한 내용은 [Azure 제한](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)을 참조하세요. 미리 보기의 기능은 일반 릴리스의 기능과 동일한 수준의 가용성 및 안정성을 제공하지 않습니다. 이 기능은 다음 지역에서만 사용할 수 있습니다. 미국 동부, 미국 서부, 미국 서부 2, 미국 중서부, 오스트레일리아 동부, 오스트레일리아 동남부 및 영국 남부

## <a name="default-security-rules"></a>기본 보안 규칙

네트워크 보안 그룹이 서브넷 또는 네트워크 인터페이스에 연결되지 않으면 모든 트래픽은 허용된 인바운드 대상이나 아웃바운드 원본, 서브넷 또는 네트워크 인터페이스입니다. 네트워크 보안 그룹이 생성되는 즉시 Azure에서는 네트워크 보안 그룹 내에서 다음과 같은 기본 규칙을 만듭니다.

### <a name="inbound"></a>인바운드

#### <a name="allowvnetinbound"></a>AllowVNetInBound

|우선 순위|원본|원본 포트|대상|대상 포트|프로토콜|Access|
|---|---|---|---|---|---|---|
|65000|VirtualNetwork|0-65535|VirtualNetwork|0-65535|모두|허용|

#### <a name="allowazureloadbalancerinbound"></a>AllowAzureLoadBalancerInBound

|우선 순위|원본|원본 포트|대상|대상 포트|프로토콜|Access|
|---|---|---|---|---|---|---|
|65001|AzureLoadBalancer|0-65535|0.0.0.0/0|0-65535|모두|허용|

#### <a name="denyallinbound"></a>DenyAllInbound

|우선 순위|원본|원본 포트|대상|대상 포트|프로토콜|Access|
|---|---|---|---|---|---|---|
|65500|0.0.0.0/0|0-65535|0.0.0.0/0|0-65535|모두|거부|

### <a name="outbound"></a>아웃바운드

#### <a name="allowvnetoutbound"></a>AllowVnetOutBound

|우선 순위|원본|원본 포트| 대상 | 대상 포트 | 프로토콜 | Access |
|---|---|---|---|---|---|---|
| 65000 | VirtualNetwork | 0-65535 | VirtualNetwork | 0-65535 | 모두 | 허용 |

#### <a name="allowinternetoutbound"></a>AllowInternetOutBound

|우선 순위|원본|원본 포트| 대상 | 대상 포트 | 프로토콜 | Access |
|---|---|---|---|---|---|---|
| 65001 | 0.0.0.0/0 | 0-65535 | 인터넷 | 0-65535 | 모두 | 허용 |

#### <a name="denyalloutbound"></a>DenyAllOutBound

|우선 순위|원본|원본 포트| 대상 | 대상 포트 | 프로토콜 | Access |
|---|---|---|---|---|---|---|
| 65500 | 0.0.0.0/0 | 0-65535 | 0.0.0.0/0 | 0-65535 | 모두 | 거부 |

**원본** 및 **대상** 열에서 *VirtualNetwork*, *AzureLoadBalancer* 및 *인터넷*은 IP 주소가 아닌 [서비스 태그](#tags)입니다. 프로토콜 열에서 **모두**는 TCP, UDP 및 ICMP를 포함합니다. 규칙을 만들 때 TCP, UDP 또는 모두를 지정할 수 있지만 ICMP만을 지정할 수는 없습니다. 따라서 규칙에 ICMP가 필요한 경우 프로토콜에서 *모두*를 선택해야 합니다. **소스** 및 **대상** 열에서 *0.0.0.0/0*은 모든 주소를 나타냅니다.
 
기본 규칙을 제거할 수 없지만 더 높은 우선 순위의 규칙을 만들어서 재정의할 수 있습니다.

## <a name="service-tags"></a>서비스 태그

 서비스 태그는 보안 규칙 생성에 대한 복잡성을 최소화할 수 있는 IP 주소 접두사의 그룹을 나타냅니다. 고유한 서비스 태그를 직접 만들거나 태그 내에 포함된 IP 주소를 지정할 수 없습니다. Microsoft에서는 서비스 태그에서 압축한 주소 접두사를 관리하고 주소를 변경하는 대로 서비스 태그를 자동으로 업데이트합니다. 보안 규칙을 만들 때 특정 IP 주소 대신 서비스 태그를 사용할 수 있습니다. 다음 서비스 태그는 보안 규칙 정의에서 사용할 수 있습니다. 해당 이름은 [Azure 배포 모델](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json)에 따라 조금씩 다릅니다.

* **VirtualNetwork**(*Resource Manager) (클래식의 경우* *VIRTUAL_NETWORK**): 이 태그에는 가상 네트워크 주소 공간(가상 네트워크에 정의된 모든 CIDR 범위), 연결된 모든 온-프레미스 주소 공간 및 [피어링된](virtual-network-peering-overview.md) 가상 네트워크 또는 [가상 네트워크 게이트웨이](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json)에 연결된 가상 네트워크가 포함됩니다.
* **AZURE_LOADBALANCER**(Resource Manager) (클래식의 경우 **AzureLoadBalancer**): 이 태그는 Azure의 인프라 부하 분산 장치를 나타내며, 태그는 Azure의 상태 검색이 시작되는 [Azure 데이터 센터 IP 주소](https://www.microsoft.com/download/details.aspx?id=41653)로 변환됩니다. Azure Load Balancer를 사용하지 않는 경우 이 규칙을 재정의할 수 있습니다.
* **Internet**(Resource Manager)(클래식의 경우 **INTERNET**): 이 태그는 Azure 공용 IP 주소 공간을 나타냅니다. 이 태그에 의해 포함된 주소는 [Azure 소유 공용 IP 공간](https://www.microsoft.com/download/details.aspx?id=41653) 문서에 나열되며 정기적으로 업데이트됩니다.
* **AzureTrafficManager**(Resource Manager에만 해당): 이 태그는 Azure Traffic Manager 서비스의 IP 주소 공간을 나타냅니다.
* **Storage**(Resource Manager에만 해당): 이 태그는 Azure Storage 서비스의 IP 주소 공간을 나타냅니다. 값의 *저장소*를 지정하는 경우 트래픽은 저장소에 대해 허용되거나 거부됩니다. 특정 [지역](https://azure.microsoft.com/regions)에서만 저장소에 대한 액세스를 허용하려는 경우 지역을 지정할 수 있습니다. 예를 들어 미국 동부 지역에서만 Azure Storage에 액세스를 허용하려는 경우 *Storage.EastUS*를 서비스 태그로 지정할 수 있습니다. 사용할 수 있는 추가 지역 서비스 태그: Storage.AustraliaEast, Storage.AustraliaSoutheast, Storage.EastUS, Storage.UKSouth, Storage.WestCentralUS, Storage.WestUS 및 Storage.WestUS2 태그는 서비스의 특정 인스턴스가 아니라 서비스를 나타냅니다. 예를 들어 태그는 특정 Azure Storage 계정이 아닌 Azure Storage 서비스를 나타냅니다.
* **Sql**(Resource Manager에만 해당): 이 태그는 Azure SQL Database 및 Azure SQL Data Warehouse 서비스의 주소 접두사를 나타냅니다. 이 서비스 태그에 특정 지역을 지정할 수 있습니다. 예를 들어 미국 동부 지역에서만 Azure SQL Database에 액세스를 허용하려는 경우 *Sql.EastUS*를 서비스 태그로 지정할 수 있습니다. 모든 Azure 지역에 Sql을 지정할 수 없습니다. 지역을 개별적으로 지정해야 합니다. 사용 가능한 다른 지역 서비스 태그: Sql.AustraliaEast, Sql.AustraliaSoutheast, Sql.EastUS, Sql.UKSouth, Sql.WestCentralUS, Sql.WestUS, and Sql.WestUS2 태그는 서비스의 특정 인스턴스가 아니라 서비스를 나타냅니다. 예를 들어 태그는 특정 Azure SQL Database가 아닌 Azure SQL Database 서비스를 나타냅니다.

> [!WARNING]
> AzureTrafficManager, Storage 및 Sql 서비스 태그는 미리 보기 릴리스에서 제공됩니다. 미리 보기의 기능은 일반 릴리스의 기능과 동일한 수준의 가용성 및 안정성을 제공하지 않습니다. 서비스 태그는 다음 지역에서만 사용할 수 있습니다. 미국 동부, 미국 서부, 미국 서부 2, 미국 중서부, 오스트레일리아 동부, 오스트레일리아 동남부 및 영국 남부

> [!NOTE]
> Azure Storage 또는 Azure SQL Database와 같은 서비스에 가상 네트워크 서비스 끝점을 구현하는 경우 Azure는 서비스의 가상 네트워크 서브넷에 경로를 추가합니다. 경로의 주소 접두사는 해당하는 서비스 태그와 동일한 주소 접두사 또는 CIDR 범위입니다.

## <a name="application-security-groups"></a>응용 프로그램 보안 그룹

응용 프로그램 보안 그룹을 사용하면 네트워크 보안을 응용 프로그램 구조의 자연 확장으로 구성하여 가상 컴퓨터를 그룹화하고 해당 그룹에 따라 네트워크 보안 정책을 정의할 수 있습니다. 이 기능을 사용하면 명시적 IP 주소를 수동으로 유지 관리하지 않고 대규모 보안 정책을 다시 사용할 수 있습니다. 플랫폼은 명시적 IP 주소 및 여러 규칙 집합의 복잡성을 처리하여 비즈니스 논리에 집중할 수 있도록 합니다.

응용 프로그램 보안 그룹을 보안 규칙의 원본 및 대상으로 지정할 수 있습니다. 보안 정책이 정의되면 가상 컴퓨터를 만들고 응용 프로그램 보안 그룹에 가상 컴퓨터의 네트워크 인터페이스를 할당할 수 있습니다. 가상 컴퓨터 내에서 각 네트워크 인터페이스의 응용 프로그램 보안 그룹 멤버 자격에 따라 정책이 적용됩니다. 다음 예제에서는 구독에서 모든 웹 서버에 응용 프로그램 보안 그룹을 사용하는 방법을 설명합니다.

1. *WebServers*라는 응용 프로그램 보안 그룹을 만듭니다.
2. *MyNSG*라는 네트워크 보안 그룹을 만듭니다.
3. 네트워크 보안 그룹에 인바운드 보안 규칙을 만들어 원본 주소의 *인터넷* 서비스 태그 및 대상 주소로 *WebServers* 응용 프로그램 보안 그룹을 지정하고 포트 80 및 443을 허용합니다.
4. 웹 서버 응용 프로그램을 실행하는 가상 컴퓨터를 배포합니다. 가상 컴퓨터의 네트워크 인터페이스를 *WebServers* 응용 프로그램 보안 그룹의 멤버로 지정합니다. 그런 다음 가상 컴퓨터에 대해 포트 80 및 443을 허용합니다. *WebServers* 응용 프로그램 보안 그룹의 멤버로 지정할 때 만든 후속 웹 서버에 대해서도 포트를 사용할 수 있습니다. 

다른 규칙을 만드는 경우 대상으로 다른 응용 프로그램 보안 그룹을 지정하기 때문에 해당 규칙이 이전 예제에서 웹 서버에 적용되지 않습니다. 응용 프로그램 보안 그룹을 지정하는 규칙은 응용 프로그램 보안 그룹의 멤버인 네트워크 인터페이스에만 적용됩니다. 보강된 보안 규칙 및 서비스 태그와 결합된 응용 프로그램 보안 그룹을 통해 최소 수의 네트워크 보안 그룹을 만들어 구독 내에서 네트워크 보안을 관리할 수 있습니다.
 
응용 프로그램 보안 그룹을 만들고 보안 규칙에서 지정하는 경우 제한에 대해 자세히 알아보려면 [Azure 제한](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)을 참조하세요.

응용 프로그램 보안 그룹은 미리 보기 릴리스에서 사용할 수 있습니다. 네트워크 보안 그룹을 사용하기 전에 [응용 프로그램 보안 그룹에서 네트워크 보안 그룹 만들기](create-network-security-group-preview.md#powershell)의 1~5단계를 완료하여 사용하도록 등록하고 중요한 정보는 [미리 보기 기능](#preview-features)을 참고해야 합니다. 미리 보기 중에 응용 프로그램 보안 그룹은 가상 네트워크의 범위로 제한됩니다. 네트워크 보안 그룹에서 응용 프로그램 보안 그룹에 대한 교차 참조로 피어링된 가상 네트워크는 적용되지 않습니다. 

미리 보기의 기능은 일반 릴리스의 기능과 동일한 수준의 가용성 및 안정성을 제공하지 않습니다. 응용 프로그램 보안 그룹을 사용하기 전에 먼저 사용하도록 등록해야 합니다. 기능은 미국 중서부 지역에서만 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [네트워크 보안 그룹 만들기](virtual-networks-create-nsg-arm-pportal.md) 자습서 완료
* [응용 프로그램 보안 그룹에서 네트워크 보안 그룹 만들기](create-network-security-group-preview.md) 자습서 완료

