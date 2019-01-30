---
title: Azure Stack에 대 한 계획 된 azure Stack 방화벽 통합 시스템 | Microsoft Docs
description: 다중 노드 배포 Azure Stack에 연결 된 Azure Stack 방화벽 고려 사항을 설명합니다.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2018
ms.author: jeffgilb
ms.reviewer: wfayed
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: eff526118f6fd127ba720d28296baf86abd01393
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55246443"
---
# <a name="azure-stack-firewall-integration"></a>Azure Stack 방화벽 통합
Azure Stack 보안 하는 데 방화벽 장치를 사용 하는 것이 좋습니다. 방화벽이 배포 된 서비스 거부 (DDOS) 공격, 침입 검색 및 콘텐츠 검사 등을 사용 하 여 도움을 줄 수 있지만 blob, 테이블 및 큐와 같은 Azure storage 서비스에 대 한 처리량 병목 지점이 될 수도 있습니다.

Azure Active Directory (Azure AD) 또는 Windows Server Active Directory Federation Services (AD FS) id 모델에 따라 해야 할 수 있습니다 AD FS 끝점에 게시 합니다. 연결이 끊긴된 배포 모드를 사용 하는 경우 AD FS 끝점에 게시 해야 합니다. 자세한 내용은 참조는 [데이터 센터 통합 identity 문서](azure-stack-integrate-identity.md)합니다.

Azure 리소스 관리자 (관리자), 관리자 포털 및 Key Vault (관리자) 끝점 반드시 필요 하지 않습니다 외부 게시 합니다. 예를 들어, 서비스 공급자 하려는 공격 노출 영역을 제한 하 고 네트워크, 내부 및 인터넷에서에서 Azure Stack만 관리 합니다.

엔터프라이즈 조직에 기존 회사 네트워크 외부 네트워크 일 수 있습니다. 이러한 시나리오에서는 Azure Stack을 회사 네트워크에서 작동 하도록 해당 끝점을 게시 해야 합니다.

### <a name="network-address-translation"></a>NAT(Network Address Translation)
네트워크 주소 변환 (NAT)은 응급 복구 콘솔 (ERCS) Vm 뿐만 아니라 외부 리소스 및 배포 하는 동안 인터넷에 액세스할 수 (dvm이)에 가상 컴퓨터를 배포 하거나 권한 있는 끝점 (PEP) 하는 동안 허용 하는 것이 좋습니다. 등록 및 문제 해결입니다.

NAT는 공용 IP 주소에서 외부 네트워크 또는 공용 Vip 대신 수도 있습니다. 그러나 테 넌 트 사용자 경험을 제한 하 고 복잡성 증가 하므로 이렇게 하지 좋습니다. 두 옵션 1:1 NAT 풀에서 IP 사용자 당 하나의 공용 IP 또는 여러 계속 해야 하는 것입니다. 모든 포트에 연결을 포함 하는 VIP 사용자별 NAT 규칙을 요구 하는 1 NAT 사용자를 사용할 수 있습니다.

공용 VIP에 대 한 NAT를 사용 하 여의 단점 중 일부는:
- 사용자가 고유한 끝점 및 소프트웨어 정의 네트워킹 (SDN) 스택의 자체 게시 규칙을 제어 하기 때문에 방화벽 규칙을 관리 하는 경우 NAT 오버 헤드가 추가 됩니다. 사용자가 게시 하는 Vip를 얻을 포트 목록을 업데이트 하려면 Azure Stack 운영자를 문의 해야 합니다.
- NAT 사용 제한 사용자 경험을 하는 동안이 전체 운영자에 게 효과적으로 제어할을 수 게시 요청을 처리 합니다.
- Azure 사용 하 여 하이브리드 클라우드 시나리오에 대 한 Azure 설정 NAT.를 사용 하는 끝점에 대 한 VPN 터널을 지원 하지 않습니다 것이 좋습니다.

### <a name="ssl-decryption"></a>SSL 암호 해독
현재 모든 Azure Stack 트래픽에서 SSL 암호 해독을 사용 하지 않도록 설정 하는 것이 좋습니다. 향후 업데이트에서 원하는 경우 Azure Stack에 대 한 SSL 암호 해독을 설정 하는 방법에 대 한 지침을 제공 합니다.

## <a name="edge-firewall-scenario"></a>에 지 방화벽 시나리오
Edge 배포를 Azure Stack에 지 라우터 또는 방화벽 뒤에 직접 배포 됩니다. 이러한 시나리오에는 활성-활성 및 활성-수동 방화벽 구성 또는 역할을 하는 테두리 장치 (시나리오 2)는 활성-활성 방화벽만 지원 모두 지원 있는 테두리 (시나리오 1) 이상이 될 방화벽 같은 비용 다중 경로 (ECMP) BGP 또는 장애 조치에 대 한 정적 라우팅을 사용 하 여 신뢰를 구성 합니다.

일반적으로 라우팅할 수 있는 공용 IP 주소는 외부 네트워크에서 공용 VIP 풀에 대 한 배포 시 지정 됩니다. Edge 시나리오에서 좋지 않습니다 보안상의 이유로 다른 네트워크에 라우팅할 수 있는 공용 Ip를 사용 하도록 합니다. 이 시나리오에서는 Azure와 같은 공용 클라우드에 같이 자체 제어 되는 전체 클라우드 환경을 경험 하기 사용자.  

![Azure Stack에 지 방화벽 예제](./media/azure-stack-firewall/firewallScenarios.png)

## <a name="enterprise-intranet-or-perimeter-network-firewall-scenario"></a>엔터프라이즈 인트라넷 이나 경계 네트워크 방화벽 시나리오
엔터프라이즈 인트라넷 이나 경계 배포에 다중 영역 방화벽 또는 지 방화벽 및 회사 내부 네트워크 방화벽 사이 Azure Stack 배포 됩니다. 해당 트래픽을 분산 하는 다음의 보안 경계 네트워크 (또는 DMZ) 사이 및 아래에 설명 된 안전 하지 않은 영역으로:

- **보안 영역**: 이것이 내부 또는 회사 라우팅할 수 있는 IP 주소를 사용 하는 내부 네트워크입니다. 보안 네트워크 나눌 수 있습니다, 방화벽에서 NAT 통해 아웃 바운드 인터넷 연결 되어 및 내부 네트워크를 통해 데이터 센터 내에서 일반적으로 액세스할 수 있습니다. 모든 Azure Stack 네트워크는 외부 네트워크의 공용 VIP 풀 제외 하 고 보안 영역에 있어야 합니다.
- **경계 영역**합니다. 경계 네트워크가 인터넷 연결 웹 서버는 일반적으로 배포와 같은 응용 프로그램 또는 외부 위치입니다. 일반적으로 인터넷에서 지정 된 인바운드 트래픽을 허용 하는 동안 (해킹) 침입 및 DDoS와 같은 공격을 방지 하려면 방화벽에 의해 모니터링 됩니다. 만 외부 네트워크 공용 VIP 풀을 Azure Stack의 DMZ 영역에 있어야 합니다.
- **보안 되지 않은 영역**합니다. 이것이 외부 네트워크와 인터넷입니다. 것 **아닙니다** 안전 하지 않은 영역에 Azure Stack을 배포 하는 것이 좋습니다.

![Azure Stack 경계 네트워크 예](./media/azure-stack-firewall/perimeter-network-scenario.png)

## <a name="learn-more"></a>자세한 정보
에 대해 자세히 알아보세요 [포트 및 Azure Stack 끝점에서 사용 되는 프로토콜](azure-stack-integrate-endpoints.md)합니다.

## <a name="next-steps"></a>다음 단계
[Azure Stack PKI 요구 사항](azure-stack-pki-certs.md)

