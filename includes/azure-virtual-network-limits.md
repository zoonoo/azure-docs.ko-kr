---
title: 포함 파일
description: 포함 파일
services: networking
author: anavinahar
ms.service: networking
ms.topic: include
ms.date: 06/25/2019
ms.author: anavin
ms.custom: include file
ms.openlocfilehash: ac1687d371630089436640af15cf46491a38ab51
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/25/2019
ms.locfileid: "74485513"
---
<a name="azure-resource-manager-virtual-networking-limits"></a>Networking limits - Azure Resource Manager The following limits apply only for networking resources managed through **Azure Resource Manager** per region per subscription. [구독 제한을 기준으로 현재 리소스 사용량을 보는](../articles/networking/check-usage-against-limits.md) 방법을 알아봅니다.

> [!NOTE]
> 최근에 모든 기본 제한을 최대 한계로 증가시켰습니다. If there's no maximum limit column, the resource doesn't have adjustable limits. If you had these limits increased by support in the past and don't see updated limits in the following tables, [open an online customer support request at no charge](../articles/azure-resource-manager/resource-manager-quota-errors.md)

| 리소스 | Default/maximum limit | 
| --- | --- |
| 가상 네트워크 |1,000 |
| 가상 네트워크당 서브넷 |3,000 |
| Virtual network peerings per virtual network |500 |
| [Virtual network gateways (VPN Gateways) per virtual network](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku) |30 |
| DNS servers per virtual network |20 |
| Private IP addresses per virtual network |65,536 |
| Private IP addresses per network interface |256 |
| Private IP addresses per virtual machine |256 |
| Public IP addresses per network interface |256 |
| Public IP addresses per virtual machine |256 |
| 가상 머신 또는 역할 인스턴스의 NIC당 동시 TCP 또는 UDP 흐름 |500,000 |
| Network interface cards |65,536 |
| 네트워크 보안 그룹 |5,000 |
| NSG당 NSG 규칙 |1,000 |
| 보안 그룹에서 원본이나 대상에 지정된 IP 주소 및 범위 |4,000 |
| 애플리케이션 보안 그룹 |3,000 |
| IP 구성당, NIC당 애플리케이션 보안 그룹 |20 |
| 애플리케이션 보안 그룹당 IP 구성 |4,000 |
| 네트워크 보안 그룹의 모든 보안 규칙 내에서 지정할 수 있는 애플리케이션 보안 그룹 |100 |
| User-defined route tables |200 |
| User-defined routes per route table |400 |
| Point-to-site root certificates per Azure VPN Gateway |20 |
| 가상 네트워크 탭 |100 |
| 가상 네트워크 탭당 네트워크 인터페이스 탭 구성 |100 |

#### <a name="publicip-address"></a>공용 IP 주소 구분
| 리소스 | 기본 제한 | 최대 제한 |
| --- | --- | --- |
| 공용 IP 주소 - 동적 | 1,000 for Basic. |지원에 문의하세요. |
| 공용 IP 주소 - 고정 | 1,000 for Basic. |지원에 문의하세요. |
| 공용 IP 주소 - 고정 | 1,000 for Standard.|지원에 문의하세요. |
| Public IP prefix length | /28 | 지원에 문의하세요. |

#### <a name="load-balancer"></a>Load balancer limits
다음 제한은 구독당 지역별로 Azure Resource Manager를 통해 관리되는 네트워킹 리소스에 대해서만 적용됩니다. [구독 제한을 기준으로 현재 리소스 사용량을 보는](../articles/networking/check-usage-against-limits.md) 방법을 알아봅니다.

| 리소스 | Default/maximum limit |
| --- | --- |
| 부하 분산 장치 | 1,000 | 
| 리소스당 규칙 수, 기본 | 250 |
| 리소스당 규칙 수, 표준 | 1,500 | 
| IP 구성당 규칙 수 | 299 |
| NIC당 규칙 | 300 |
| Front-end IP configurations, Basic | 200 |
| Front-end IP configurations, Standard | 600 |
| Back-end pool, Basic | 100, single availability set |
| Back-end pool, Standard | 1,000, single virtual network |
| Back-end resources per load balancer, Standard<sup>1</sup> | 150 |
| High-availability ports, Standard | 1 per internal front-end |

<sup>1</sup>The limit is up to 150 resources, in any combination of standalone virtual machine resources, availability set resources, and virtual machine scale-set resources.

#### <a name="virtual-networking-limits-classic"></a>The following limits apply only for networking resources managed through the **classic** deployment model per subscription. [구독 제한을 기준으로 현재 리소스 사용량을 보는](../articles/networking/check-usage-against-limits.md) 방법을 알아봅니다.

| 리소스 | 기본 제한 | 최대 제한 |
| --- | --- | --- |
| 가상 네트워크 |100 |100 |
| 로컬 네트워크 사이트 수 |20 |50 |
| DNS servers per virtual network |20 |20 |
| Private IP addresses per virtual network |4,096 |4,096 |
| 가상 머신 또는 역할 인스턴스의 NIC당 동시 TCP 또는 UDP 흐름 |500,000, up to 1,000,000 for two or more NICs. |500,000, up to 1,000,000 for two or more NICs. |
| NSG(네트워크 보안 그룹) |200 |200 |
| NSG당 NSG 규칙 |1,000 |1,000 |
| User-defined route tables |200 |200 |
| User-defined routes per route table |400 |400 |
| 공용 IP 주소(동적) |500 |500 |
| 예약된 공용 IP 주소 |500 |500 |
| 배포당 공용 VIP |5 |고객 지원 |
| Private VIP (internal load balancing) per deployment |1 |1 |
| Endpoint access control lists (ACLs) |50 |50 |
