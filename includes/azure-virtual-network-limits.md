---
title: 포함 파일
description: 포함 파일
services: networking
author: anavinahar
ms.service: networking
ms.topic: include
ms.date: 05/15/2019
ms.author: anavin
ms.custom: include file
ms.openlocfilehash: cb586be1a139b331c259a1197cc62e7ca4f25666
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66238385"
---
<a name="virtual-networking-limits-classic"></a>네트워킹을 통해 관리 되는 리소스에 대해서만 다음과 같은 제한이 적용 된 **클래식** 구독 당 배포 모델입니다. [구독 제한을 기준으로 현재 리소스 사용량을 보는](../articles/networking/check-usage-against-limits.md) 방법을 알아봅니다.

| Resource | 기본 제한 | 최대 제한 |
| --- | --- | --- |
| 가상 네트워크 |50 |100 |
| 로컬 네트워크 사이트 수 |20 |지원에 문의 |
| 가상 네트워크 당 DNS 서버 |20 |20 |
| 가상 네트워크 당 개인 IP 주소 |4,096 |4,096 |
| 가상 머신 또는 역할 인스턴스의 NIC당 동시 TCP 또는 UDP 흐름 |두 개 이상의 Nic에 대 한 1,000,000 최대 500,000입니다. |두 개 이상의 Nic에 대 한 1,000,000 최대 500,000입니다. |
| NSG(네트워크 보안 그룹) |200 |200 |
| NSG당 NSG 규칙 |1,000 |1,000 |
| 사용자 정의 경로 테이블 |200 |200 |
| 경로 테이블당 사용자 정의 경로 |400 |400 |
| 공용 IP 주소(동적) |5 |지원에 문의 |
| 예약된 공용 IP 주소 |20 |지원에 문의 |
| 배포당 공용 VIP |5 |지원에 문의 |
| 배포당 개인 VIP (내부 부하 분산) |1 |1 |
| 끝점 액세스 제어 목록 (Acl) |50 |50 |

#### <a name="azure-resource-manager-virtual-networking-limits"></a>네트워킹 제한-Azure Resource Manager
다음과 같은 제한이 적용 네트워킹 리소스를 통해 관리 되는 동안만 **Azure Resource Manager** 지역당 구독 당 합니다. [구독 제한을 기준으로 현재 리소스 사용량을 보는](../articles/networking/check-usage-against-limits.md) 방법을 알아봅니다.

> [!NOTE]
> 최근에 모든 기본 제한을 최대 한계로 증가시켰습니다. 최대 한도 열이 없는 경우 리소스에 조정 가능한 제한이 없습니다. 한도 이전에 지원 하 여 증가 하 고 다음 테이블에서 업데이트 된 한도 표시 되지 않는 경우 [무료로 온라인 고객 지원 요청을 개설](../articles/azure-resource-manager/resource-manager-quota-errors.md)

| Resource | 기본/최대 제한 | 
| --- | --- |
| 가상 네트워크 |1,000 |
| 가상 네트워크당 서브넷 |3,000 |
| 가상 네트워크 당 가상 네트워크 피어 링 |500 |
| 가상 네트워크 당 DNS 서버 |20 |
| 가상 네트워크 당 개인 IP 주소 |65,536 |
| 네트워크 인터페이스당 개인 IP 주소 |256 |
| 가상 머신 당 개인 IP 주소 |256 |
| 가상 머신 또는 역할 인스턴스의 NIC당 동시 TCP 또는 UDP 흐름 |500,000 |
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
| Azure VPN Gateway 당 지점-사이트 간 루트 인증서 |20 |
| 가상 네트워크 탭 |100 |
| 가상 네트워크 탭당 네트워크 인터페이스 탭 구성 |100 |

#### <a name="publicip-address"></a>공용 IP 주소 구분
| Resource | 기본 제한 | 최대 제한 |
| --- | --- | --- |
| 공용 IP 주소 - 동적 | basic의 경우 1,000입니다. |지원에 문의 |
| 공용 IP 주소 - 고정 | basic의 경우 1,000입니다. |지원에 문의 |
| 공용 IP 주소 - 고정 | 표준 200입니다.|지원에 문의 |
| 공용 IP 접두사 크기 | /28 | 지원에 문의 |

#### <a name="load-balancer"></a>부하 분산 장치 제한
다음 제한은 구독당 지역별로 Azure Resource Manager를 통해 관리되는 네트워킹 리소스에 대해서만 적용됩니다. [구독 제한을 기준으로 현재 리소스 사용량을 보는](../articles/networking/check-usage-against-limits.md) 방법을 알아봅니다.

| Resource | 기본/최대 제한 |
| --- | --- |
| 부하 분산 장치 | 1,000 | 
| 리소스당 규칙 수, 기본 | 250 |
| 리소스당 규칙 수, 표준 | 1,500 | 
| IP 구성당 규칙 수 | 299 |
| NIC당 규칙 | 500 |
| 기본 프런트 엔드 IP 구성 | 200 |
| 프런트 엔드 IP 구성, 표준 | 600 |
| 백 엔드 풀 수, 기본 | 100이 고, 단일 가용성 집합 |
| 백 엔드 풀 수, 표준 | 1,000, 단일 가상 네트워크 |
| 표준 load balancer 당 백 엔드 리소스<sup>1</sup> | 150 |
| 고가용성 포트, 표준 | 프런트 엔드 당 내부 1 |

<sup>1</sup>제한은 최대 150 개의 리소스를 모든 조합의 독립 실행형 가상 컴퓨터 리소스, 리소스 및 가상 머신 확장 집합 리소스 가용성 집합입니다.

