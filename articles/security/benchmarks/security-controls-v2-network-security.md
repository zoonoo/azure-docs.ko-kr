---
title: Azure Security 벤치 마크 V2-네트워크 보안
description: Azure Security 벤치 마크 V2 네트워크 보안
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/20/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 9833f63d999ab7c24174853bd37f4e7a76f6dfbf
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91329434"
---
# <a name="security-control-v2-network-security"></a>보안 제어 V2: 네트워크 보안

네트워크 보안은 Azure 네트워크를 보호 하 고 보호 하는 컨트롤을 포함 합니다. 여기에는 가상 네트워크 보안 설정, 개인 연결 설정, 외부 공격 방지 및 완화, DNS 보호 등이 포함 됩니다.

## <a name="ns-1-implement-security-for-internal-traffic"></a>NS-1: 내부 트래픽에 대 한 보안 구현

| Azure ID | CIS 컨트롤 v 7.1 ID | NIST SP800-53 4, 000 개 ID |
|--|--|--|--|
| NS-1 | 9.2, 9.4, 14.1, 14.2, 14.3 | AC-4, 캐나다-3, SC-7 |

모든 Azure virtual network가 비즈니스 위험에 맞는 엔터프라이즈 구분 원칙을 따르는지 확인 합니다. 조직에서 더 높은 위험을 초래할 수 있는 모든 시스템은 해당 가상 네트워크 내에서 격리 되 고 NSG (네트워크 보안 그룹) 및/또는 Azure 방화벽으로 충분히 안전 하 게 보호 되어야 합니다. 

응용 프로그램 및 엔터프라이즈 조각화 전략에 따라 네트워크 보안 그룹 규칙을 기반으로 하는 내부 리소스 간의 트래픽을 제한 하거나 허용 합니다. 잘 정의 된 특정 응용 프로그램 (예: 3 계층 응용 프로그램)의 경우이는 매우 안전한 "기본적으로 거부, 예외에의 한 허용" 접근 방법 일 수 있습니다. 여러 응용 프로그램과 끝점이 서로 상호 작용 하는 경우에는이를 제대로 확장 하지 못할 수 있습니다. 또한 많은 수의 엔터프라이즈 세그먼트 또는 스포크 (허브/스포크 토폴로지)에서 중앙 관리가 필요한 경우 Azure 방화벽을 사용할 수 있습니다. 

Azure Security Center 적응 네트워크 강화를 사용 하 여 외부 네트워크 트래픽 규칙에 대 한 참조를 기반으로 포트 및 원본 Ip를 제한 하는 네트워크 보안 그룹 구성을 권장 합니다.

Azure 센티널을 사용 하 여 SSL/TLSv1, SMBv1, LM/NTLMv1, wDigest, 서명 되지 않은 LDAP 바인딩 및 Kerberos에서 weak 암호화와 같은 레거시 안전 하지 않은 프로토콜의 사용을 검색 합니다.

- [보안 규칙을 사용 하 여 네트워크 보안 그룹을 만드는 방법](../../virtual-network/tutorial-filter-network-traffic.md)

- [Azure 방화벽을 배포 및 구성 하는 방법](../../firewall/tutorial-firewall-deploy-portal.md)

- [Azure Security Center의 적응 네트워크 강화](../../security-center/security-center-adaptive-network-hardening.md)

- [Azure 센티널 안전 하지 않은 프로토콜 통합 문서](../../sentinel/quickstart-get-visibility.md#use-built-in-workbooks)

**책임**: Customer

**고객 보안 관련자** ([자세한 정보](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [보안 아키텍처](/azure/cloud-adoption-framework/organize/cloud-security-architecture) 

- [포스처 관리](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)     

- [응용 프로그램 보안 및 DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

## <a name="ns-2-connect-private-networks-together"></a>NS-2: 개인 네트워크를 함께 연결

| Azure ID | CIS 컨트롤 v 7.1 ID | NIST SP800-53 4, 000 개 ID |
|--|--|--|--|
| NS-2 | 해당 없음 | CA-3, AC-17, MA-4 |

Azure Express 경로 또는 Azure VPN (가상 사설망)을 사용 하 여 공동 배치 환경의 온-프레미스 인프라와 Azure 데이터 센터 간에 개인 연결을 만듭니다. Express 경로 연결은 공용 인터넷을 통해 이동 하지 않으며 일반적인 인터넷 연결 보다 안정적이 고 속도가 빠르며 대기 시간이 짧습니다. 지점 및 사이트 간 VPN 및 사이트 간 VPN의 경우 이러한 VPN 옵션과 Azure Express 경로를 조합 하 여 온-프레미스 장치 또는 네트워크를 가상 네트워크에 연결할 수 있습니다.

Azure에서 둘 이상의 가상 네트워크를 함께 연결 하려면 가상 네트워크 피어 링 또는 개인 링크를 사용 합니다. 피어 링 가상 네트워크 간의 네트워크 트래픽은 비공개 이며 Azure 백본 네트워크에 유지 됩니다. 

- [Express 경로 연결 모델](../../expressroute/expressroute-connectivity-models.md) 

- [Azure VPN 개요](../../vpn-gateway/vpn-gateway-about-vpngateways.md)

- [가상 네트워크 피어링](../../virtual-network/virtual-network-peering-overview.md)

- [Azure Private Link](../../private-link/private-link-service-overview.md)

**책임**: Customer

**고객 보안 관련자** ([자세한 정보](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [보안 아키텍처](/azure/cloud-adoption-framework/organize/cloud-security-architecture) 

- [포스처 관리](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)    

- [응용 프로그램 보안 및 DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

## <a name="ns-3-establish-private-network-access-to-azure-services"></a>NS-3: Azure 서비스에 대 한 개인 네트워크 액세스 설정

| Azure ID | CIS 컨트롤 v 7.1 ID | NIST SP800-53 4, 000 개 ID |
|--|--|--|--|
| NS-3 | 14.1 | AC-4, 캐나다-3, SC-7 |

Azure 개인 링크를 사용 하 여 인터넷을 거치지 않고 가상 네트워크에서 Azure 서비스에 대 한 개인 액세스를 사용 하도록 설정 합니다. Azure 개인 링크를 아직 사용할 수 없는 경우 Azure Virtual Network 서비스 끝점을 사용 합니다.  Azure Virtual Network 서비스 끝점은 Azure 백본 네트워크를 통해 최적화 된 경로를 통해 서비스에 대 한 보안 액세스를 제공 합니다.  

개인 액세스는 Azure 서비스에서 제공 하는 인증 및 트래픽 보안과 더불어 심층 측정의 추가 방어 수단입니다. 

- [Azure 개인 링크 이해](../../private-link/private-link-overview.md)

- [Virtual Network 서비스 끝점 이해](../../virtual-network/virtual-network-service-endpoints-overview.md)

**책임**: Customer

**고객 보안 관련자** ([자세한 정보](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [보안 아키텍처](/azure/cloud-adoption-framework/organize/cloud-security-architecture) 

- [포스처 관리](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)   

- [응용 프로그램 보안 및 DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

## <a name="ns-4-protect-applications-and-services-from-external-network-attacks"></a>NS-4: 외부 네트워크 공격 으로부터 응용 프로그램 및 서비스 보호

| Azure ID | CIS 컨트롤 v 7.1 ID | NIST SP800-53 4, 000 개 ID |
|--|--|--|--|
| NS-4 | 9.5, 12.3, 12.9 | SC-5, SC-7 |

DDoS (분산 서비스 거부) 공격, 응용 프로그램별 공격, 원치 않는 악의적인 인터넷 트래픽 등 외부 네트워크의 공격 으로부터 Azure 리소스를 보호 합니다. Azure에는이에 대 한 기본 기능이 포함 되어 있습니다.
-   Azure 방화벽을 사용 하 여 인터넷 및 기타 외부 위치에서 잠재적으로 악성 트래픽에 대 한 응용 프로그램 및 서비스를 보호 합니다. 

-   Azure 애플리케이션 Gateway의 WAF (웹 응용 프로그램 방화벽) 기능, Azure Front 도어 및 CDN (Azure Content Delivery Network)을 사용 하 여 응용 프로그램 계층 공격에 대해 응용 프로그램, 서비스 및 Api를 보호 합니다. 

-   Azure 가상 네트워크에서 DDoS 표준 보호를 사용 하도록 설정 하 여 DDoS 공격 으로부터 자산을 보호 하세요. 
-   Azure Security Center를 사용 하 여 위와 관련 된 잘못 된 구성 위험을 검색 합니다. 

- [Azure Firewall 설명서](/azure/firewall/)

- [Azure WAF를 배포 하는 방법](../../web-application-firewall/overview.md)

- [Azure Portal을 사용하여 Azure DDoS Protection 표준 관리](../../virtual-network/manage-ddos-protection.md)

**책임**: Customer

**고객 보안 관련자** ([자세한 정보](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

없음

## <a name="ns-5-deploy-intrusion-detectionintrusion-prevention-systems-idsips"></a>NS-5: 침입 감지/침입 방지 시스템 (IDS/IPS) 배포

| Azure ID | CIS 컨트롤 v 7.1 ID | NIST SP800-53 4, 000 개 ID |
|--|--|--|--|
| NS-5 | 12.6, 12.7 | SI-4 |

Azure 방화벽 위협 인텔리전스 기반 필터링을 사용 하 여 알려진 악성 IP 주소 및 도메인에 대 한 트래픽을 경고 및/또는 차단할 수 있습니다. IP 주소 및 도메인은 Microsoft 위협 인텔리전스 피드에서 제공됩니다. 페이로드 검사를 수행 해야 하는 경우 페이로드 검사 기능을 사용 하 여 Azure Marketplace에서 타사 침입 감지/침입 방지 시스템 (IDS/IPS)을 배포할 수 있습니다. 또는 네트워크 기반 IDS/IPS 대신 호스트 기반 IDS/IPS 또는 호스트 기반 끝점 검색 및 응답 (EDR) 솔루션을 또는와 함께 사용할 수 있습니다.  

참고: IDS/IPS 사용에 대 한 규정 또는 기타 요구 사항이 있는 경우 SIEM 솔루션에 고품질의 경고를 제공 하도록 항상 조정 해야 합니다. 

- [Azure 방화벽을 배포 하는 방법](../../firewall/tutorial-firewall-deploy-portal.md)

- [Azure Marketplace에 타사 ID 기능이 포함 되어 있습니다.](https://azuremarketplace.microsoft.com/marketplace?search=IDS)

- [Microsoft Defender ATP EDR 기능](/windows/security/threat-protection/microsoft-defender-atp/overview-endpoint-detection-response)

**책임**: Customer

**고객 보안 관련자** ([자세한 정보](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [보안 아키텍처](/azure/cloud-adoption-framework/organize/cloud-security-architecture) 

- [포스처 관리](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)   

- [응용 프로그램 보안 및 DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

## <a name="ns-6-simplify-network-security-rules"></a>NS-6: 네트워크 보안 규칙 단순화

| Azure ID | CIS 컨트롤 v 7.1 ID | NIST SP800-53 4, 000 개 ID |
|--|--|--|--|
| NS-6 | 1.5 | IA-4 |

서비스 태그 및 ASGs (응용 프로그램 보안 그룹)를 활용 하 여 네트워크 보안 규칙을 간소화 합니다. 

Virtual Network 서비스 태그를 사용 하 여 네트워크 보안 그룹 또는 Azure 방화벽에서 네트워크 액세스 제어를 정의 합니다. 보안 규칙을 만들 때 특정 IP 주소 대신 서비스 태그를 사용할 수 있습니다. 규칙의 원본 또는 대상 필드에서 서비스 태그 이름을 지정 하 여 해당 서비스에 대 한 트래픽을 허용 하거나 거부할 수 있습니다. Microsoft는 서비스 태그에 포함되는 주소 접두사를 관리하고 주소가 변경되면 서비스 태그를 자동으로 업데이트합니다.

응용 프로그램 보안 그룹을 사용 하 여 복잡 한 보안 구성의 간소화를 지원할 수도 있습니다. 네트워크 보안 그룹의 명시적 IP 주소를 기반으로 정책을 정의 하는 대신 응용 프로그램 보안 그룹을 사용 하면 네트워크 보안을 응용 프로그램 구조의 자연 확장으로 구성 하 여 가상 머신을 그룹화 하 고 해당 그룹에 따라 네트워크 보안 정책을 정의할 수 있습니다.

- [서비스 태그 이해 및 사용](../../virtual-network/service-tags-overview.md)

- [응용 프로그램 보안 그룹 이해 및 사용](/azure/virtual-network/security-overview#application-security-groups)

**책임**: Customer

**고객 보안 관련자** ([자세한 정보](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [보안 아키텍처](/azure/cloud-adoption-framework/organize/cloud-security-architecture) 

- [포스처 관리](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)    

- [응용 프로그램 보안 및 DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

## <a name="ns-7-secure-domain-name-service-dns"></a>NS-7: 보안 DNS (Domain Name Service)

| Azure ID | CIS 컨트롤 v 7.1 ID | NIST SP800-53 4, 000 개 ID |
|--|--|--|--|
| NS-7 | 해당 없음 | SC-20, SC-21 |

DNS 보안에 대 한 모범 사례에 따라 현 DNS, DNS amplifications 공격, DNS 포 이즈 및 스푸핑 등의 일반적인 공격을 완화 합니다. 

Azure DNS를 권한 있는 DNS 서비스로 사용 하는 경우 DNS 영역 및 레코드가 Azure RBAC 및 리소스 잠금을 사용 하 여 실수로 또는 악의적으로 수정 되지 않도록 보호 해야 합니다. 

- [Azure DNS 개요](../../dns/dns-overview.md)

- [보안 DNS (Domain Name System) 배포 가이드](https://csrc.nist.gov/publications/detail/sp/800-81/2/final)

- [현 수 DNS 항목을 방지 하 고 하위 도메인 인수 방지](../fundamentals/subdomain-takeover.md)

**책임**: Customer

**고객 보안 관련자** ([자세한 정보](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [보안 아키텍처](/azure/cloud-adoption-framework/organize/cloud-security-architecture) 

- [포스처 관리](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)   

- [응용 프로그램 보안 및 DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

