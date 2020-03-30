---
title: 포함 파일
description: 포함 파일
services: networking
author: anavinahar
ms.service: networking
ms.topic: include
ms.date: 01/14/2020
ms.author: anavin
ms.custom: include file
ms.openlocfilehash: 8752585e731f905636f57d31741e2be67f7140b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335032"
---
### <a name="networking-limits---azure-resource-manager"></a><a name="azure-resource-manager-virtual-networking-limits"></a>네트워킹 제한 - Azure 리소스 관리자
다음 제한은 구독당 지역별 **Azure 리소스 관리자를** 통해 관리되는 네트워킹 리소스에만 적용됩니다. [구독 한도에 대해 현재 리소스 사용량을 보는](../articles/networking/check-usage-against-limits.md)방법을 알아봅니다.

> [!NOTE]
> 최근에 모든 기본 제한을 최대 한계로 증가시켰습니다. 최대 제한 열이 없는 경우 리소스에 조정 가능한 제한이 없습니다. 과거에 지원에 의해 이러한 제한이 증가했는데 다음 표에 업데이트된 한도가 표시되지 않는 경우 [온라인 고객 지원 요청을 무료로 엽니다.](../articles/azure-resource-manager/templates/error-resource-quota.md)

| 리소스 | 제한 | 
| --- | --- |
| 가상 네트워크 |1,000 |
| 가상 네트워크당 서브넷 |3,000 |
| 가상 네트워크당 가상 네트워크 피어링 |500 |
| [가상 네트워크당 가상 네트워크 게이트웨이(VPN 게이트웨이)](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku) |1 |
| [가상 네트워크당 가상 네트워크 게이트웨이(ExpressRoute 게이트웨이)](../articles/expressroute/expressroute-about-virtual-network-gateways.md#gwsku) |1 |
| 가상 네트워크당 DNS 서버 |20 |
| 가상 네트워크당 프라이빗 IP 주소 |65,536 |
| 네트워크 인터페이스당 개인 IP 주소 |256 |
| 가상 시스템당 개인 IP 주소 |256 |
| 네트워크 인터페이스당 공용 IP 주소 |256 |
| 가상 시스템당 공용 IP 주소 |256 |
| [가상 머신 또는 역할 인스턴스의 NIC당 동시 TCP 또는 UDP 흐름](../articles/virtual-network/virtual-machine-network-throughput.md#flow-limits-and-recommendations) |500,000 |
| 네트워크 인터페이스 카드 |65,536 |
| 네트워크 보안 그룹 |5,000 |
| NSG당 NSG 규칙 |1,000 |
| 보안 그룹에서 원본이나 대상에 지정된 IP 주소 및 범위 |4,000 |
| 애플리케이션 보안 그룹 |3,000 |
| IP 구성당, NIC당 애플리케이션 보안 그룹 |20 |
| 애플리케이션 보안 그룹당 IP 구성 |4,000 |
| 네트워크 보안 그룹의 모든 보안 규칙 내에서 지정할 수 있는 애플리케이션 보안 그룹 |100 |
| 사용자 정의 경로 테이블 |200 |
| 경로 테이블당 사용자 정의 경로 |400 |
| Azure VPN 게이트웨이당 지점 간 루트 인증서 |20 |
| 가상 네트워크 탭 |100 |
| 가상 네트워크 탭당 네트워크 인터페이스 탭 구성 |100 |

#### <a name="public-ip-address-limits"></a><a name="publicip-address"></a>공용 IP 주소 구분
| 리소스 | 기본 제한 | 최대 제한 |
| --- | --- | --- |
| 공용 IP 주소<sup>1</sup> | 기본용 10. | 지원에 문의 |
| 정적 공용 IP 주소<sup>1</sup> | 기본용 10. | 지원에 문의 |
| 표준 공용 IP 주소<sup>1</sup> | 10 | 지원에 문의 |
| 공용 IP 접두사 | 구독의 표준 공용 IP 수로 제한됩니다. | 지원에 문의 |
| 공용 IP 접두사 길이 | /28 | 지원에 문의 |

<sup>1개</sup> 공용 IP 주소의 기본 한도는 무료 평가판, 종량제, CSP와 같은 오퍼 카테고리 유형에 따라 다릅니다. 예를 들어 엔터프라이즈 계약 구독의 기본값은 1000입니다.

#### <a name="load-balancer-limits"></a><a name="load-balancer"></a>로드 밸러블러 제한
다음 제한은 구독당 지역별로 Azure Resource Manager를 통해 관리되는 네트워킹 리소스에 대해서만 적용됩니다. [구독 한도에 대해 현재 리소스 사용량을 보는](../articles/networking/check-usage-against-limits.md)방법을 알아봅니다.

**표준 Load Balancer**

| 리소스                                | 제한         |
|-----------------------------------------|-------------------------------|
| 부하 분산 장치                          | 1,000                         |
| 리소스당 규칙                      | 1,500                         |
| NIC당 규칙(NIC의 모든 IP에 걸쳐) | 300                           |
| 프런트 엔드 IP 구성              | 600                           |
| 백 엔드 풀 크기                       | 1,000개의 IP 구성, 단일 가상 네트워크 |
| 고가용성 포트                 | 내부 프런트 엔드당 1       |
| 로드 밸러블러당 아웃바운드 규칙        | 20                            |
| [TCP 유휴 시간](https://docs.microsoft.com/azure/load-balancer/load-balancer-tcp-idle-timeout#tcp-idle-timeout) | 4분/30분          |


**기본 로드 밸러블러**

| 리소스                                | 제한        |
|-----------------------------------------|------------------------------|
| 부하 분산 장치                          | 1,000                        |
| 리소스당 규칙                      | 250                          |
| NIC당 규칙(NIC의 모든 IP에 걸쳐) | 300                          |
| 프런트 엔드 IP 구성              | 200                          |
| 백 엔드 풀 크기                       | 300개의 IP 구성, 단일 가용성 세트 |
| 부하 밸워서당 가용성 세트     | 150                          |

<a name="virtual-networking-limits-classic"></a>다음 제한은 구독당 **클래식** 배포 모델을 통해 관리되는 네트워킹 리소스에만 적용됩니다. [구독 한도에 대해 현재 리소스 사용량을 보는](../articles/networking/check-usage-against-limits.md)방법을 알아봅니다.

| 리소스 | 기본 제한 | 최대 제한 |
| --- | --- | --- |
| 가상 네트워크 |100 |100 |
| 로컬 네트워크 사이트 수 |20 |50 |
| 가상 네트워크당 DNS 서버 |20 |20 |
| 가상 네트워크당 프라이빗 IP 주소 |4,096 |4,096 |
| 가상 머신 또는 역할 인스턴스의 NIC당 동시 TCP 또는 UDP 흐름 |500,000, 두 개 이상의 NIC에 대해 최대 1,000,000. |500,000, 두 개 이상의 NIC에 대해 최대 1,000,000. |
| NSG(네트워크 보안 그룹) |200 |200 |
| NSG당 NSG 규칙 |1,000 |1,000 |
| 사용자 정의 경로 테이블 |200 |200 |
| 경로 테이블당 사용자 정의 경로 |400 |400 |
| 공용 IP 주소(동적) |500 |500 |
| 예약된 공용 IP 주소 |500 |500 |
| 배포당 공용 VIP |5 |지원에 문의 |
| 배포당 개인 VIP(내부 부하 분산) |1 |1 |
| 엔드포인트 액세스 제어 목록(ACL) |50 |50 |
