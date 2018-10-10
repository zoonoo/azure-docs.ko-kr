---
title: 포함 파일
description: 포함 파일
services: networking
author: jimdial
ms.service: networking
ms.topic: include
ms.date: 08/16/2018
ms.author: jdial
ms.custom: include file
ms.openlocfilehash: 673faff51c3d06e50941a6982a9f654c28598d86
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47006508"
---
<a name="virtual-networking-limits-classic"></a>다음 제한은 구독당 클래식 배포 모델을 통해 관리되는 네트워킹 리소스에 대해서만 적용됩니다. [구독 제한을 기준으로 현재 리소스 사용량을 보는](../articles/networking/check-usage-against-limits.md) 방법을 알아봅니다.

| 리소스 | 기본 제한 | 최대 제한 |
| --- | --- | --- |
| 가상 네트워크 |50 |100 |
| 로컬 네트워크 사이트 수 |20 |지원에 문의 |
| 가상 네트워크당 DNS 서버 |20 |100 |
| 가상 네트워크당 개인 IP 주소 |4096 |4096 |
| 가상 머신 또는 역할 인스턴스의 NIC당 동시 TCP 또는 UDP 흐름 |500K |500K |
| 네트워크 보안 그룹(NSG) |100 |200 |
| NSG당 NSG 규칙 |200 |1000 |
| 사용자 정의 경로 테이블 |100 |200 |
| 경로 테이블당 사용자 정의 경로 |100 |400 |
| 공용 IP 주소(동적) |5 |지원에 문의 |
| 예약된 공용 IP 주소 |20 |지원에 문의 |
| 배포당 공용 VIP |5 |지원에 문의 |
| 배포당 개인 VIP(ILB) |1 |1 |
| 엔드포인트 ACL(Access Control 목록) |50 |50 |

#### <a name="azure-resource-manager-virtual-networking-limits"></a>네트워킹 제한 - Azure Resource Manager
다음 제한은 구독당 지역별로 Azure Resource Manager를 통해 관리되는 네트워킹 리소스에 대해서만 적용됩니다. [구독 제한을 기준으로 현재 리소스 사용량을 보는](../articles/networking/check-usage-against-limits.md) 방법을 알아봅니다.

> [!NOTE]
> 최근에 모든 기본 제한을 최대 한계로 증가시켰습니다. **최대 제한** 열이 없는 경우는 리소스에 조정 가능한 제한이 없습니다. 이러한 제한이 이전의 지원으로 증가하고 아래와 같이 업데이트된 제한을 확인하지 않으려는 경우 [무료로 온라인 고객 지원 요청을 열](../articles/azure-resource-manager/resource-manager-quota-errors.md) 수 있습니다.

| 리소스 | 기본 제한 | 
| --- | --- |
| 가상 네트워크 |1000 |
| 가상 네트워크당 서브넷 |3000 |
| Virtual Network당 가상 네트워크 피어링 |100 |
| 가상 네트워크당 DNS 서버 |25 |
| 가상 네트워크당 개인 IP 주소 |65536 |
| 네트워크 인터페이스당 개인 IP 주소 |256 |
| 가상 머신 또는 역할 인스턴스의 NIC당 동시 TCP 또는 UDP 흐름 |500K |
| 네트워크 인터페이스(NIC) |24000 |
| 네트워크 보안 그룹(NSG) |5,000 |
| NSG당 NSG 규칙 |1000 |
| 보안 그룹에서 원본이나 대상에 지정된 IP 주소 및 범위 |4000 |
| 응용 프로그램 보안 그룹 |3000 |
| IP 구성당, NIC당 응용 프로그램 보안 그룹 |20 |
| 응용 프로그램 보안 그룹당 IP 구성 |4000 |
| 네트워크 보안 그룹의 모든 보안 규칙 내에서 지정할 수 있는 응용 프로그램 보안 그룹 |100 |
| 사용자 정의 경로 테이블 |200 |
| 경로 테이블당 사용자 정의 경로 |400 |
| VPN Gateway당 지점 및 사이트 간 루트 인증서 |20 |
| 가상 네트워크 탭 |100 |
| 가상 네트워크 탭당 네트워크 인터페이스 탭 구성 |100 |

#### <a name="publicip-address"></a>공용 IP 주소 구분

| 리소스 | 기본 제한 | 최대 제한 |
| --- | --- | --- |
| 공용 IP 주소 - 동적 |(기본)200 |지원에 문의 |
| 공용 IP 주소 - 고정 |(기본)200 |지원에 문의 |
| 공용 IP 주소 - 고정 |(표준)200 |지원에 문의 |

#### <a name="load-balancer"></a>부하 분산 장치 제한
다음 제한은 구독당 지역별로 Azure Resource Manager를 통해 관리되는 네트워킹 리소스에 대해서만 적용됩니다. [구독 제한을 기준으로 현재 리소스 사용량을 보는](../articles/networking/check-usage-against-limits.md) 방법을 알아봅니다.

| 리소스 | 기본 제한 | 최대 제한 |
| --- | --- | --- |
| 부하 분산 장치 | 100 | 1000 |
| 리소스당 규칙 수, 기본 | 250 | 250 |
| 리소스당 규칙 수, 표준 | 1500 | 1500 |
| IP 구성당 규칙 수 | 299 |299 |
| 프런트 엔드 IP 구성 수, 기본 | 10 | 200 |
| 프런트 엔드 IP 구성 수, 표준 | 10 | 600 |
| 백 엔드 풀 수, 기본 | 100, 단일 가용성 집합 | 100, 단일 가용성 집합 |
| 백 엔드 풀 수, 표준 | 1000, 단일 VNet | 1000, 단일 VNet |
| Load Balancer당 백 엔드 리소스, 표준 * | 150 | 150 |
| HA 포트 수, 표준 | 내부 프런트 엔드당 1 | 내부 프런트 엔드당 1 |

** 독립 실행형 가상 머신, 가용성 집합 및 가상 머신 확장 집합의 임의 조합으로 이루어진 최대 150개의 리소스.

기본값에서 제한을 늘려야 하는 경우 [지원에 문의하세요](../articles/azure-supportability/resource-manager-core-quotas-request.md ).

