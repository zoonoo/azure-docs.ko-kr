---
title: Azure Stack 네트워킹 차이점 및 고려 사항 | Microsoft Docs
description: Azure Stack에서 네트워킹을 사용 하 여 작업 하는 경우 차이점 및 고려 사항에 알아봅니다.
services: azure-stack
keywords: ''
author: mattbriggs
manager: femila
ms.author: brenduns
ms.date: 08/02/2018
ms.topic: article
ms.service: azure-stack
ms.reviewer: scottnap
ms.openlocfilehash: 50fe3c0c7fda745047c71afb8eedf7fa8806c4ec
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/10/2018
ms.locfileid: "42139538"
---
# <a name="considerations-for-azure-stack-networking"></a>Azure Stack 네트워킹에 대 한 고려 사항

*적용 대상: Azure Stack 통합 시스템 및 Azure Stack 개발 키트*

Azure Stack 네트워킹에는 다양 한 Azure 네트워킹에서 제공 하는 기능 그러나 Azure Stack 네트워크를 배포 하기 전에 이해 해야 하는 몇 가지 주요 차이점이 있습니다.

이 문서에서는 Azure Stack 네트워킹 및 해당 기능에 대 한 고유한 고려 사항의 개요를 제공합니다. Azure Stack 및 Azure 간의 대략적인 차이 대 한 자세한 내용은 참조는 [고려 사항 키](azure-stack-considerations.md) 문서.

## <a name="cheat-sheet-networking-differences"></a>치트 시트: 네트워킹 차이점

| 서비스 | 기능 | Azure (전역) | Azure Stack |
|--------------------------|----------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------|
| DNS | 다중 테 넌 트 DNS | 지원됨 | 아직 지원 되지 않음 |
|  | DNS AAAA 레코드 | 지원됨 | 지원되지 않음 |
|  | 구독 당 DNS 영역 | 100 (기본값)<br>요청 시 늘릴 수 있습니다. | 100 |
|  | 영역당 DNS 레코드 집합 | 5000 (기본값)<br>요청 시 늘릴 수 있습니다. | 5,000 |
|  | 영역 위임을 위해 이름 서버 | Azure는 만든 각 사용자 (테 넌 트) 영역에 대 한 4 명의 이름 서버를 제공 합니다. | Azure Stack 만든 각 사용자 (테 넌 트) 영역에 대 한 두 명의 이름 서버를 제공 합니다. |
| Virtual Network | 가상 네트워크 피어링 | Azure 백본 네트워크를 통해 동일한 지역의 두 가상 네트워크를 연결 합니다. | 아직 지원 되지 않음 |
|  | IPv6 주소 | 일부분으로 IPv6 주소를 할당할 수 있습니다 합니다 [네트워크 인터페이스 구성](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface-addresses#ip-address-versions)합니다. | IPv4만 지원됩니다. |
|  | DDoS 보호 계획 | 지원됨 | 아직 지원 되지 않습니다. |
|  | 확장 집합 IP 구성 | 지원됨 | 아직 지원 되지 않습니다. |
|  | 개인 액세스 서비스 (서브넷) | 지원됨 | 아직 지원 되지 않습니다. |
|  | 서비스 엔드포인트 | Azure 서비스에 내부 (인터넷이 아닌) 연결에 대 한 지원. | 아직 지원 되지 않습니다. |
| IPv4만 지원됩니다. | 서비스 끝점 정책 | 지원됨 | 아직 지원 되지 않습니다. |
|  | 서비스 터널 | 지원됨 | 아직 지원 되지 않습니다.  |
| 네트워크 보안 그룹 | 보강 된 보안 규칙 | 지원됨 | 아직 지원 되지 않습니다. |
|  | 효과적인 보안 규칙 | 지원됨 | 아직 지원 되지 않습니다. |
|  | 응용 프로그램 보안 그룹 | 지원됨 | 아직 지원 되지 않습니다. |
| Virtual Network 게이트웨이 | 지점-사이트 간 VPN Gateway | 지원됨 | 아직 지원 되지 않습니다. |
|  | Vnet 대 Vnet 게이트웨이 | 지원됨 | 아직 지원 되지 않습니다. |
|  | 가상 네트워크 게이트웨이 유형 | Azure에서는 VPN<br> Express 경로 <br> 하이퍼 Net | Azure Stack이 이번에만 VPN 유형을 지원합니다. |
|  | VPN Gateway Sku | Basic, GW1, GW2, GW3, 표준 고성능, 아주 높은 성능을 지원 합니다. | Basic, Standard 및 고성능 Sku를 지원 합니다. |
|  | VPN 유형 | Azure 정책 기반 및 경로 기반을 모두 지원합니다. | Azure Stack 경로 기반만 지원 합니다. |
|  | BGP 설정 | Azure는 BGP 피어 링 주소 및 피어 무게의 구성을 지원합니다. | BGP 피어 링 주소 및 피어 무게 Azure Stack에서 자동으로 구성 됩니다. 고유한 값을 사용 하 여 이러한 설정을 구성 하려면 사용자에 대 한 방법이 없습니다. |
|  | 기본 게이트웨이 사이트 | Azure는 강제 터널링에 대 한 기본 사이트의 구성을 지원 합니다. | 아직 지원 되지 않습니다. |
|  | 게이트웨이 크기 조정 | Azure는 배포 후 게이트웨이 크기 조정 지원 합니다. | 크기 조정 하지 지원 합니다. |
|  | 활성/비활성 구성 | 지원됨 | 아직 지원 되지 않습니다. |
|  | IKE/IPSec 정책 | Azure에서는 사용자 지정 IPSec 정책 구성 합니다. | 아직 지원 되지 않습니다. |
|  | UsePolicyBasedTrafficSelectors | Azure 정책 기반 트래픽 선택기를 사용 하 여 경로 기반 게이트웨이 연결을 지원 합니다. | 아직 지원 되지 않습니다. |
| 부하 분산 장치 | SKU | 기본 및 표준 Load Balancer는 지원 | 기본 Load Balancer만 지원 됩니다.  SKU 속성은 지원 되지 않습니다. |
|  | 영역 | 가용성 영역 지원 됩니다. | 아직 지원 되지 않음 |
|  | 서비스 끝점에 대 한 인바운드 NAT 규칙 지원 | Azure는 인바운드 NAT 규칙에 대 한 서비스 끝점 지정을 지원합니다. | 이 지정할 수 있도록 azure Stack 서비스 끝점을 아직 지원 하지 않습니다. |
|  | 프로토콜 | Azure 지원 GRE 또는 ESP를 지정 합니다. | 프로토콜 클래스는 Azure Stack에서 지원 되지 않습니다. |
| 공용 IP 주소 | 공용 IP 주소 버전 | Azure에서는 IPv6과 IPv4를 모두 지원합니다. | IPv4만 지원됩니다. |
| 네트워크 인터페이스 | 유효한 경로 테이블 가져오기 | 지원됨 | 아직 지원 되지 않습니다. |
|  | 효과적인 Acl 가져오기 | 지원됨 | 아직 지원 되지 않습니다. |
|  | 가속화 된 네트워킹을 사용 하도록 설정 | 지원됨 | 아직 지원 되지 않습니다. |
|  | IP 전달 | 기본적으로 사용 하지 않도록 설정 합니다.  사용할 수 있습니다. | 이 설정을 토글 하면 지원 되지 않습니다.  기본적으로 합니다. |
|  | 인터페이스 당 여러 IP 구성 | 지원됨 | 아직 지원 되지 않습니다. |
|  | 응용 프로그램 보안 그룹 | 지원됨 | 아직 지원 되지 않습니다. |
|  | 내부 DNS 이름 레이블 | 지원됨 | 아직 지원 되지 않습니다. |
|  | 개인 IP 주소 버전 | IPv6 및 IPv4 둘 다 지원 됩니다. | IPv4만 지원됩니다. |
|  | 기본 IP 구성 | 지원됩니다. 인터페이스의 기본 IP 구성을 식별합니다. | 아직 지원 되지 않습니다. |
| Network Watcher | 네트워크 감시자 테 넌 트 네트워크 모니터링 기능 | 지원됨 | 아직 지원 되지 않습니다. |
| CDN | Content Delivery Network 프로필 | 지원됨 | 아직 지원 되지 않습니다. |
| 응용 프로그램 게이트웨이 | 계층 7 부하 분산 | 지원됨 | 아직 지원 되지 않습니다. |
| Traffic Manager | 최적의 응용 프로그램 성능과 안정성을 위해 들어오는 트래픽을 라우팅하십시오. | 지원됨 | 아직 지원 되지 않습니다. |
| Express 경로 | 에 온-프레미스 인프라 또는 공동 배치 시설에서 Microsoft 클라우드 서비스에 신속 하 고 개인 연결을 설정 합니다. | 지원됨 | Expressroute 회로에 Azure Stack에 연결을 지원 합니다. |

## <a name="next-steps"></a>다음 단계

[Azure Stack의 DNS](azure-stack-dns.md)