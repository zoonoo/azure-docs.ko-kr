---
title: Azure의 네트워크 보안 그룹 | Microsoft Docs
description: 네트워크 보안 그룹을 사용하여 Azure에서 분산된 방화벽을 사용하여 가상 네트워크 내에서 트래픽 흐름을 격리하고 제어하는 방법을 알아봅니다.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: tysonn
ms.assetid: 20e850fc-6456-4b5f-9a3f-a8379b052bc9
ms.service: virtual-network
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/11/2016
ms.author: jdial
ms.openlocfilehash: 87ca0a1cd9766d3ad76d0fe5dd29a34ec40ea276
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2018
---
# <a name="filter-network-traffic-with-network-security-groups"></a>네트워크 보안 그룹을 사용하여 네트워크 트래픽 필터링

NSG(네트워크 보안 그룹)에는 VNet(Azure Virtual Network)에 연결된 리소스에 대한 네트워크 트래픽을 허용하거나 거부하는 보안 규칙 목록이 포함되어 있습니다. NSG는 서브넷, 개별 VM(클래식) 또는 VM(Resource Manager)에 연결된 개별 NIC(네트워크 인터페이스)와 연결될 수 있습니다. NSG를 서브넷에 연결하면 규칙이 서브넷에 연결된 모든 리소스에 적용됩니다. 또한 NSG를 VM 또는 NIC에 연결하여 트래픽을 더욱 제한할 수 있습니다.
 
> [!NOTE]
> Azure에는 리소스를 만들고 작업하는 [Resource Manager와 클래식](../resource-manager-deployment-model.md)이라는 두 가지 배포 모델이 있습니다. 이 문서에서는 두 모델을 모두 사용하여 설명하지만 대부분의 새로운 배포에는 리소스 관리자 모델을 사용하는 것이 좋습니다.

## <a name="nsg-resource"></a>NSG 리소스
NSG에는 다음과 속성이 포함됩니다.

| 자산 | 설명 | 제약 조건 | 고려 사항 |
| --- | --- | --- | --- |
| Name |NSG 이름 |지역 내에서 고유해야 합니다.<br/>문자, 숫자, 밑줄, 마침표 및 하이픈을 포함할 수 있습니다.<br/>문자 또는 숫자로 시작해야 합니다.<br/>문자, 숫자 또는 밑줄로 끝나야 합니다.<br/>80자를 초과할 수 없습니다. |NSG를 여러 개 만들어야 하는 경우 NSG 기능을 쉽게 식별할 수 있는 명명 규칙을 사용해야 합니다. |
| 지역 |NSG를 만들 Azure [지역](https://azure.microsoft.com/regions) |NSG는 NSG와 동일한 지역 내의 리소스에만 연결할 수 있습니다. |지역별 NSG 수에 대한 자세한 내용은 [Azure 제한](../azure-subscription-service-limits.md#virtual-networking-limits-classic) 문서를 참조하세요.|
| 리소스 그룹 |NSG가 속한 [리소스 그룹](../azure-resource-manager/resource-group-overview.md#resource-groups) |NSG는 하나의 리소스 그룹에 속하지만, NSG와 동일한 Azure 지역의 일부인 리소스는 모든 리소스 그룹의 리소스에 연결될 수 있습니다. |리소스 그룹은 하나의 배포 단위로 여러 리소스를 함께 관리하는 데 사용됩니다.<br/>연결된 리소스와 함께 NSG를 그룹화하는 것이 좋습니다. |
| 규칙 |허용되거나 거부되는 트래픽을 정의하는 인바운드 또는 아웃바운드 규칙 | |이 문서의 [NSG 규칙](#Nsg-rules) 섹션을 참조하세요. |

> [!NOTE]
> 끝점 기반 ACL과 네트워크 보안 그룹은 동일한 VM 인스턴스에서 지원되지 않습니다. NSG를 사용하려는데 끝점 ACL이 이미 있는 경우 먼저, 끝점 ACL을 제거합니다. ACL을 제거하는 방법에 대한 자세한 내용은 [PowerShell을 사용하여 끝점에 대한 ACL(Access Control 목록) 관리](virtual-networks-acl-powershell.md) 문서를 참조하세요.
> 

### <a name="nsg-rules"></a>NSG 규칙
NSG 규칙은 다음 속성을 포함합니다.

| 자산 | 설명 | 제약 조건 | 고려 사항 |
| --- | --- | --- | --- |
| **Name** |규칙의 이름 |지역 내에서 고유해야 합니다.<br/>문자, 숫자, 밑줄, 마침표 및 하이픈을 포함할 수 있습니다.<br/>문자 또는 숫자로 시작해야 합니다.<br/>문자, 숫자 또는 밑줄로 끝나야 합니다.<br/>80자를 초과할 수 없습니다. |NSG에는 여러 규칙이 있을 수 있으므로 규칙의 기능을 식별할 수 있는 명명 규칙을 따라야 합니다. |
| **프로토콜** |규칙과 일치하는 프로토콜 |TCP, UDP, 또는 * |프로토콜로 *(별표)를 사용하면 UDP와 TCP뿐만 아니라 ICMP(동부 및 서부 트래픽만)도 포함되며, 필요한 규칙의 수를 줄일 수 있습니다.<br/>동시에 *를 사용하면 너무 광범위할 수 있으므로 필요한 경우에만 사용하는 것이 좋습니다. |
| **원본 포트 범위** |규칙과 일치하는 원본 포트 범위 |1-65535의 단일 포트 번호, 포트 범위(예: 1-65535) 또는 *(모든 포트의 경우) |원본 포트는 사용 후 삭제될 수 있습니다. 클라이언트 프로그램에서 특정 포트를 사용하지 않는 한 대부분의 경우 "*"를 사용합니다.<br/>여러 규칙이 필요하지 않도록 포트 범위를 가능한 한 많이 사용하도록 합니다.<br/>여러 포트 또는 포트 범위는 쉼표로 그룹화할 수 없습니다. |
| **대상 포트 범위** |규칙과 일치하는 대상 포트 범위 |1-65535의 단일 포트 번호, 포트 범위(예: 1-65535) 또는 \*(모든 포트의 경우) |여러 규칙이 필요하지 않도록 포트 범위를 가능한 한 많이 사용하도록 합니다.<br/>여러 포트 또는 포트 범위는 쉼표로 그룹화할 수 없습니다. |
| **원본 주소 접두사** |규칙과 일치하는 원본 주소 접두사 또는 태그 |단일 IP 주소(예: 10.10.10.10), IP 서브넷(예: 192.168.1.0/24), [서비스 태그](#service-tags) 또는 *(모든 주소의 경우). |범위, 서비스 태그, *를 사용하여 규칙의 수를 줄이는 것이 좋습니다. |
| **대상 주소 접두사** |규칙과 일치하는 대상 주소 접두사 또는 태그 | 단일 IP 주소(예: 10.10.10.10), IP 서브넷(예: 192.168.1.0/24), [기본 태그](#service-tags) 또는 *(모든 주소의 경우) |범위, 서비스 태그, *를 사용하여 규칙의 수를 줄이는 것이 좋습니다. |
| **방향** |규칙과 일치하는 트래픽의 방향 |인바운드 또는 아웃바운드 |인바운드 규칙과 아웃바운드 규칙은 방향에 따라 개별적으로 처리됩니다. |
| **우선 순위** |규칙은 우선 순위에 따라 검사됩니다. 규칙이 적용되면 일치하는 규칙은 더 이상 테스트되지 않습니다. | 100~4096 사이의 숫자 | 나중에 만들 수 있는 새로운 규칙을 위해 각 규칙마다 우선 순위를 100씩 건너뛰도록 만드는 것이 좋습니다. |
| **Access** |규칙이 일치하는 경우 적용할 액세스 유형 | 허용 또는 거부 | 패킷에 대한 허용 규칙을 찾을 수 없으면 해당 패킷이 삭제됩니다. |

NSG에는 두 가지 규칙 집합, 즉 인바운드 및 아웃바운드가 포함됩니다. 규칙에 대한 우선 순위는 각 집합 내에서 고유해야 합니다. 

![NSG 규칙 처리](./media/virtual-network-nsg-overview/figure3.png) 

위의 그림에서는 NSG 규칙을 처리하는 방법을 보여 줍니다.

### <a name="default-tags"></a>시스템 태그

서비스 태그는 IP 주소의 범주를 다루기 위해 시스템에서 제공한 식별자입니다. 모든 보안 규칙의 **원본 주소 접두사** 및 **대상 주소 접두사** 속성에 있는 서비스 태그를 사용할 수 있습니다. [서비스 태그](security-overview.md#service-tags)에 대해 자세히 알아보세요.

### <a name="default-rules"></a>기본 보안 규칙

모든 NSG에는 기본 보안 규칙 집합이 포함되어 있습니다. 기본 규칙은 삭제할 수 없지만, 가장 낮은 우선순위가 할당되기 때문에 직접 만든 규칙으로 재정의할 수 있습니다. [기본 보안 규칙](security-overview.md#default-security-rules)에 대해 자세히 알아보세요.

## <a name="associating-nsgs"></a>NSG 연결
다음과 같이 사용하는 배포 모델에 따라 NSG를 VM, NIC 및 서브넷에 연결할 수 있습니다.

* **VM(클래식만):** 보안 규칙이 VM과의 모든 트래픽에 적용됩니다. 
* **NIC(Resource Manager만):** 보안 규칙이 NSG가 연결된 NIC와의 모든 트래픽에 적용됩니다. 다중 NIC VM에서 각각의 NIC에 서로 다르거나 동일한 NSG를 개별적으로 적용할 수 있습니다. 
* **서브넷(Resource Manager 및 클래식):** 보안 규칙이 서브넷에 연결된 모든 리소스에 대한 모든 트래픽에 적용됩니다.

다른 NSG를 VM(또는 배포 모델에 따라 NIC)에 연결할 수 있고, NIC 또는 VM이 바인딩된 서브넷에 연결될 수 있습니다. 보안 규칙은 우선 순위에 따라 각 NSG에서 다음과 같은 순서로 트래픽에 적용됩니다.

- **인바운드 트래픽**

  1. **서브넷에 적용된 NSG:** 서브넷 NSG에 트래픽을 거부하는 규칙과 일치하는 규칙이 있는 경우 패킷이 삭제됩니다.

  2. **NIC에 적용된 NSG**(Resource Manager) 또는 VM(클래식): VM\NIC NSG에 트래픽을 거부하는 규칙과 일치하는 규칙이 있는 경우 서브넷 NSG에 트래픽을 허용하는 규칙과 일치하는 규칙이 있더라도 VM\NIC에서 패킷이 삭제됩니다.

- **아웃바운드 트래픽**

  1. **NIC에 적용된 NSG**(Resource Manager) 또는 VM(클래식): VM\NIC NSG에 트래픽을 거부하는 규칙과 일치하는 규칙이 있는 경우 패킷이 삭제됩니다.

  2. **서브넷에 적용된 NSG:** 서브넷 NSG에 트래픽을 거부하는 규칙과 일치하는 규칙이 있는 경우 VM\NIC NSG에 트래픽을 허용하는 규칙과 일치하는 규칙이 있더라도 패킷이 삭제됩니다.

> [!NOTE]
> 서브넷, VM 또는 NIC에 단일 NSG만 연결할 수 있지만 동일한 NSG를 원하는 수만큼 많은 리소스에 연결할 수 있습니다.
>

## <a name="implementation"></a>구현
다음 도구를 사용하여 Resource Manager 또는 클래식 배포 모델에서 NSG를 구현할 수 있습니다.

| 배포 도구 | 클래식 | 리소스 관리자 |
| --- | --- | --- |
| Azure portal   | 예 | [예](virtual-networks-create-nsg-arm-pportal.md) |
| PowerShell     | [예](virtual-networks-create-nsg-classic-ps.md) | [예](tutorial-filter-network-traffic.md) |
| Azure CLI **V1**   | [예](virtual-networks-create-nsg-classic-cli.md) | [예](tutorial-filter-network-traffic-cli.md) |
| Azure CLI **V2**   | 아니오 | [예](tutorial-filter-network-traffic-cli.md) |
| Azure Resource Manager 템플릿   | 아니오  | [예](template-samples.md) |

## <a name="planning"></a>계획
NSG를 구현하기 전에 아래 질문에 답변해야 합니다.

1. 트래픽을 필터링할 리소스 종류는 무엇입니까? NIC(Resource Manager), VM(클래식), Cloud Services, Application Service Environments 및 VM Scale Sets와 같은 리소스를 연결할 수 있습니다. 
2. 기존 VNet의 서브넷에 연결되거나 연결하는 트래픽을 필터링하려는 리소스가 있습니까?

Azure의 네트워크 보안 계획에 대한 자세한 내용은 [클라우드 서비스 및 네트워크 보안](../best-practices-network-security.md) 문서를 참조하세요. 

## <a name="design-considerations"></a>디자인 고려 사항
[계획](#Planning) 섹션에서 질문에 대한 답변을 확인한 후에 NSG를 정의하기 전에 다음 섹션을 검토합니다.

### <a name="limits"></a>제한
구독에서 보유할 수 있는 NSG 수와 NSG당 규칙의 수에 제한이 있습니다. 이러한 한에 대한 자세한 내용은 [Azure 제한](../azure-subscription-service-limits.md#networking-limits) 문서를 참조하세요.

### <a name="vnet-and-subnet-design"></a>VNet 및 서브넷 디자인
NSG가 서브넷에 적용될 수 있기 때문에, 서브넷에 따라서 리소스를 그룹화고 서브넷에 NSG를 적용하여 NSG의 수를 최소화할 수 있습니다.  NSG를 서브넷에 적용하기로 결정하고 나면, 사용자의 기존 VNet과 서브넷이 NSG를 감안하지 않고 정의되었다는 점을 발견할 수 있습니다. 새 VNet 및 서브넷을 정의하여 NSG 디자인을 지원하고 새 리소스를 새 서브넷에 배포해야 할 수 있습니다. 그 후 기존 리소스를 새 서브넷으로 이동하는 마이그레이션 전략을 정의합니다. 

### <a name="special-rules"></a>특별한 규칙
다음 규칙에서 허용하는 트래픽을 차단하면 인프라에서 필수 Azure 서비스와 통신할 수 없습니다.

* **호스트 노드의 가상 IP:** DHCP, DNS 및 상태 모니터링과 같은 기본 인프라 서비스는 가상화된 168.63.129.16 호스트 IP 주소를 통해 제공됩니다. 이 공용 IP 주소는 Microsoft에 속하며, 이 목적을 위해 모든 지역에서 유일하게 사용되는 가상화된 IP 주소입니다. 이 IP 주소는 VM을 호스트하는 서버 컴퓨터(호스트 노드)의 실제 IP 주소에 매핑됩니다. 호스트 노드는 DHCP 릴레이, DNS 재귀 확인자, 부하 분산 장치 상태 검색 및 컴퓨터 상태 검색에 대한 검색 소스 등의 역할을 합니다. 이 IP 주소로의 통신은 공격이 아닙니다.
* **라이선싱(키 관리 서비스)**: VM에서 실행되는 Windows 이미지를 사용하려면 라이선스가 있어야 합니다. 사용 허가를 위해 라이선스 요청이 이러한 쿼리를 처리하는 키 관리 서비스 호스트 서버로 전송됩니다. 요청은 1688 포트를 통해 아웃바운드로 수행됩니다.

### <a name="icmp-traffic"></a>ICMP 트래픽
현재 NSG 규칙에는 *TCP* 또는 *UDP* 프로토콜만 허용됩니다. *ICMP*에 대한 고유 태그는 없습니다. 그러나 ICMP 트래픽은 VNet 내의 모든 포트와 프로토콜로 들어오고 나가는 트래픽을 허용하는 AllowVNetInBound 기본 규칙에 따라 VNet 내에서 허용됩니다.

### <a name="subnets"></a>서브넷
* 워크로드에 필요한 계층의 수를 고려합니다. 각 계층은 서브넷에 적용되는 NSG로 서브넷을 사용하여 격리될 수 있습니다. 
* VPN 게이트웨이 또는 ExpressRoute 회로에 대한 서브넷을 구현해야 하는 경우 해당 서브넷에 NSG를 적용하지 **않습니다**. 그렇게 하면 VNet 간 또는 온-프레미스 간 연결이 실패할 수 있습니다. 
* NVA(네트워크 가상 어플라이언스)를 구현해야 하는 경우 NVA를 자체 서브넷에 연결하고 NVA에 대한 UDR(사용자 정의 경로)를 만듭니다. 서브넷에 들어오고 나가는 트래픽을 필터링하도록 서브넷 수준 NSG를 구현할 수 있습니다. UDR에 대한 자세한 내용은 [사용자 정의 경로](virtual-networks-udr-overview.md) 문서를 참조하세요.

### <a name="load-balancers"></a>부하 분산 장치
* 각 워크로드에서 사용하는 각 부하 분산 장치에 대한 부하 분산 및 NAT(Network Address Translation) 규칙을 사용하는 것이 좋습니다. NAT 규칙은 NIC(Resource Manager) 또는 VM/Cloud Services 역할 인스턴스(클래식)가 포함된 백 엔드 풀에 바인딩됩니다. 각 백 엔드 풀에 대해 NSG를 만들어 부하 분산 장치에 구현된 규칙을 통해 매핑되는 트래픽만 허용하는 것이 좋습니다. 각 백 엔드 풀에 대한 NSG를 만들면 부하 분산 장치를 통하지 않고 직접 백 엔드 풀로 들어오는 트래픽도 필터링됩니다.
* 클래식 배포의 경우, 부하 분산 장치의 포트를 VM 또는 역할 인스턴스의 포트에 매핑하는 끝점을 만듭니다. 또한 Resource Manager를 통해 사용자 고유의 개별적인 공용 부하 분산 장치를 만들 수도 있습니다. 들어오는 트래픽의 대상 포트는 부하 분산 장치에서 노출하는 포트가 아닌 VM이나 역할 인스턴스의 실제 포트입니다. VM에 연결하기 위한 원본 포트와 주소는 부하 분산 장치에서 노출하는 포트와 주소가 아닌 인터넷 상의 원격 컴퓨터의 포트와 주소입니다.
* NSG를 만들어 Azure Load Balancer를 통해 들어오는 트래픽을 필터링하는 경우, 적용되는 원본 포트와 주소 범위는 부하 분산 장치 프런트 엔드가 아닌 원래 컴퓨터에서 가져옵니다. 대상 포트와 주소 범위는 부하 분산 장치 프런트 엔드가 아닌 대상 컴퓨터의 포트와 주소 범위입니다.
* AzureLoadBalancer 태그를 차단하면 Azure Load Balancer의 상태 프로브가 실패하고 서비스에 영향을 줄 수 있습니다.

### <a name="other"></a>기타
* 끝점 기반 ACL(액세스 제어 목록)과 NSG는 동일한 VM 인스턴스에서 지원되지 않습니다. NSG를 사용하려는데 끝점 ACL이 이미 있는 경우 먼저, 끝점 ACL을 제거합니다. 끝점 ACL을 제거하는 방법에 대한 내용은 [끝점 ACL 관리](virtual-networks-acl-powershell.md) 문서를 참조하세요.
* Resource Manager에서는 여러 NIC가 있는 VM의 NIC에 연결된 NSG를 사용하여 NIC별로 관리(원격 액세스)를 활성화할 수 있습니다. 고유한 NSG를 각 NIC에 연결하면 NIC에서 트래픽 종류를 분리할 수 있습니다.
* 부하 분산 장치의 사용과 마찬가지로 다른 VNet의 트래픽을 필터링하는 경우 VNet을 연결하는 게이트웨이가 아닌 원격 컴퓨터의 원본 주소 범위를 사용해야 합니다.
* 많은 Azure 서비스에서 VNet에 연결할 수 없습니다. Azure 리소스가 VNet에 연결되어 있지 않으면 NSG를 사용하여 리소스에 대한 트래픽을 필터링할 수 없습니다.  서비스가 VNet에 연결될 수 있는지 여부를 결정하려면 사용하는 서비스에 대한 문서를 참조하세요.

## <a name="sample-deployment"></a>샘플 배포
이 문서의 정보를 적용하는 방식을 설명하기 위해 다음 그림과 같은 2계층 응용 프로그램의 일반적인 시나리오를 고려해 보겠습니다.

![NSG](./media/virtual-network-nsg-overview/figure1.png)

위의 다이어그램에서 보여 주듯이 *WEB1* 및 *WEB2* VM은 *FrontEnd* 서브넷에 연결되고, *DB1* 및 *DB2* VM은 *BackEnd* 서브넷에 연결됩니다.  두 서브넷은 *TestVNet* VNet의 일부입니다. 응용 프로그램 구성 요소는 각각 VNet에 연결된 Azure VM 내에서 실행됩니다. 시나리오에는 다음 요구 사항이 있습니다.

1. WEB과 DB 서버 간의 트래픽을 분리합니다.
2. 부하 분산 규칙은 트래픽을 부하 분산 장치에서 80 포트의 모든 웹 서버로 전달합니다.
3. 부하 분산 장치 NAT 규칙은 50001 포트의 부하 분산 장치로 들어오는 트래픽을 WEB1 VM의 3389 포트로 전달합니다.
4. 요구 사항 2와 3을 제외하고는 인터넷에서 프런트 엔드 또는 백 엔드 VM에 액세스할 수 없습니다.
5. WEB 또는 DB 서버에서 아웃바운드로 인터넷에 액세스할 수 없습니다.
6. FrontEnd 서브넷에서의 액세스는 모든 웹 서버의 3389 포트에 허용됩니다.
7. FrontEnd 서브넷에서의 액세스는 모든 DB 서버의 3389 포트에 허용됩니다.
8. FrontEnd 서브넷에서의 액세스는 모든 DB 서버의 1433 포트에 허용됩니다.
9. DB 서버의 다른 NIC에서 관리 트래픽(3389 포트)과 데이터베이스 트래픽(1433 포트)을 분리합니다.

요구 사항 1-6(요구 사항 3 및 4 제외)은 모두 서브넷 공간으로 제한됩니다. 다음 NSG는 이전 요구 사항을 충족하면서 필요한 NSG 수를 최소화합니다.

### <a name="frontend"></a>FrontEnd
**인바운드 규칙**

| 규칙 | Access | 우선 순위 | 원본 주소 범위 | 원본 포트 | 대상 주소 범위 | 대상 포트 | 프로토콜 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Allow-Inbound-HTTP-Internet(인바운드 HTTP 인터넷 허용) | 허용 | 100 | 인터넷 | * | * | 80 | TCP |
| Allow-Inbound-RDP-Internet(인바운드 RDP 인터넷 허용) | 허용 | 200 | 인터넷 | * | * | 3389 | TCP |
| Deny-Inbound-All(모든 인바운드 거부) | 거부 | 300 | 인터넷 | * | * | * | TCP |

**아웃바운드 규칙**

| 규칙 | Access | 우선 순위 | 원본 주소 범위 | 원본 포트 | 대상 주소 범위 | 대상 포트 | 프로토콜 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Deny-Internet-All(모든 인터넷 거부) |거부 |100 | * | * | 인터넷 | * | * |

### <a name="backend"></a>BackEnd
**인바운드 규칙**

| 규칙 | Access | 우선 순위 | 원본 주소 범위 | 원본 포트 | 대상 주소 범위 | 대상 포트 | 프로토콜 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Deny-Internet-All(모든 인터넷 거부) | 거부 | 100 | 인터넷 | * | * | * | * |

**아웃바운드 규칙**

| 규칙 | Access | 우선 순위 | 원본 주소 범위 | 원본 포트 | 대상 주소 범위 | 대상 포트 | 프로토콜 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Deny-Internet-All(모든 인터넷 거부) | 거부 | 100 | * | * | 인터넷 | * | * |

다음 NSG가 만들어지고 다음 VM의 NIC와 연결됩니다.

### <a name="web1"></a>WEB1
**인바운드 규칙**

| 규칙 | Access | 우선 순위 | 원본 주소 범위 | 원본 포트 | 대상 주소 범위 | 대상 포트 | 프로토콜 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Allow-Inbound-RDP-Internet(인바운드 RDP 인터넷 허용) | 허용 | 100 | 인터넷 | * | * | 3389 | TCP |
| Allow-Inbound-HTTP-Internet(인바운드 HTTP 인터넷 허용) | 허용 | 200 | 인터넷 | * | * | 80 | TCP |

> [!NOTE]
> 이전 규칙의 원본 주소 범위는 부하 분산 장치의 가상 IP 주소가 아니라 **Internet**입니다. 원본 포트는 500001이 아니라 *입니다. 부하 분산 장치의 NAT 규칙은 NSG 보안 규칙과 다릅니다. NSG 보안 규칙은 원본과 대상 사이의 부하 분산 장치가 **아니라** 항상 트랙픽의 원래 원본 및 최종 대상과 관련이 있습니다. Azure Load Balancer는 항상 원본 IP 주소와 포트를 유지합니다.
> 
> 

### <a name="web2"></a>WEB2
**인바운드 규칙**

| 규칙 | Access | 우선 순위 | 원본 주소 범위 | 원본 포트 | 대상 주소 범위 | 대상 포트 | 프로토콜 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Deny-Inbound-RDP-Internet( 인바운드 RDP 인터넷 거부) | 거부 | 100 | 인터넷 | * | * | 3389 | TCP |
| Allow-Inbound-HTTP-Internet(인바운드 HTTP 인터넷 허용) | 허용 | 200 | 인터넷 | * | * | 80 | TCP |

### <a name="db-servers-management-nic"></a>DB 서버(관리 NIC)
**인바운드 규칙**

| 규칙 | Access | 우선 순위 | 원본 주소 범위 | 원본 포트 | 대상 주소 범위 | 대상 포트 | 프로토콜 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Allow-Inbound-RDP-Front-end(인바운드 RDP 프런트 엔드 허용) | 허용 | 100 | 192.168.1.0/24 | * | * | 3389 | TCP |

### <a name="db-servers-database-traffic-nic"></a>DB 서버(데이터베이스 트래픽 NIC)
**인바운드 규칙**

| 규칙 | Access | 우선 순위 | 원본 주소 범위 | 원본 포트 | 대상 주소 범위 | 대상 포트 | 프로토콜 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Allow-Inbound-SQL-Front-end(인바운드 SQL 프런트 엔드 허용) | 허용 | 100 | 192.168.1.0/24 | * | * | 1433 | TCP |

NSG 중 일부는 개별 NIC와 연결되므로 Resource Manager를 통해 배포된 리소스에 대한 규칙이 있습니다. 규칙은 연결되는 방법에 따라 서브넷과 NIC에 대해 결합됩니다. 

## <a name="next-steps"></a>다음 단계
* [NSG 배포(Resource Manager)](virtual-networks-create-nsg-arm-pportal.md).
* [NSG 배포(클래식)](virtual-networks-create-nsg-classic-ps.md).
* [NSG 로그를 관리](virtual-network-nsg-manage-log.md)합니다.
* [NSG 문제 해결](virtual-network-nsg-troubleshoot-portal.md)
