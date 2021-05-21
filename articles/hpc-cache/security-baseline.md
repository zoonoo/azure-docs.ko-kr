---
title: Azure HPC Cache에 대한 Azure 보안 기준
description: Azure HPC Cache 보안 기준은 Azure Security Benchmark에 지정된 보안 추천 사항을 구현하기 위한 절차 지침과 리소스를 제공합니다.
author: msmbaldwin
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 11/19/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: e5a395f71ed1b1ab06d97b8aa6d83278d59d15c0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101091963"
---
# <a name="azure-security-baseline-for-azure-hpc-cache"></a>Azure HPC Cache에 대한 Azure 보안 기준

이 보안 기준은 [Azure Security Benchmark 버전 2.0](../security/benchmarks/overview.md)의 지침을 Microsoft Azure HPC Cache에 적용합니다. Azure Security Benchmark는 Azure에서 클라우드 솔루션을 보호하는 방법에 대한 권장 사항을 제공합니다. 콘텐츠는 Azure Security Benchmark에서 정의한 **보안 컨트롤** 및 Azure HPC Cache에 적용되는 관련 지침에 따라 그룹화됩니다. Azure HPC Cache에 적용할 수 없는 **컨트롤** 은 제외되었습니다.

Azure HPC Cache가 Azure Security Benchmark에 완전히 매핑되는 방식을 확인하려면 [전체 Azure HPC Cache 보안 기준 매핑 파일](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)을 참조하세요.

## <a name="network-security"></a>네트워크 보안

자세한 내용은 [Azure Security Benchmark: 네트워크 보안](../security/benchmarks/security-controls-v2-network-security.md)을 참조하세요.

### <a name="ns-1-implement-security-for-internal-traffic"></a>NS-1: 내부 트래픽에 대한 보안 구현

**지침**: Azure HPC Cache 리소스를 배포할 때 가상 네트워크를 만들거나 기존 가상 네트워크를 사용해야 합니다. 

모든 Azure 가상 네트워크가 비즈니스 위험에 맞는 엔터프라이즈 구분 원칙을 따르는지 확인합니다. 조직에서 더 높은 위험을 초래할 수 있는 모든 시스템은 자체 가상 네트워크 내에서 격리되어야 하고 NSG(네트워크 보안 그룹) 및/또는 Azure Firewall로 충분히 보호되어야 합니다.

캐시를 사용하려면 먼저 두 개의 네트워크 관련 필수 구성 요소를 설정해야 합니다. 

- Azure HPC Cache 인스턴스의 전용 서브넷

- 캐시에서 스토리지 및 기타 리소스에 액세스할 수 있도록 DNS 지원

Azure HPC Cache에는 다음과 같은 품질의 전용 서브넷이 필요합니다. 

- 서브넷에 사용 가능한 IP 주소가 64개 이상이어야 합니다.

- 서브넷은 다른 VM을(클라이언트 머신과 같은 관련 서비스인 경우에도) 호스트할 수 없습니다.

- 여러 Azure HPC Cache 인스턴스를 사용하는 경우 각각에 자체 서브넷이 필요합니다.

각 캐시에 대해 새 서브넷을 만드는 것이 가장 좋습니다. 캐시를 만드는 과정에서 새 가상 네트워크 및 서브넷을 만들 수 있습니다.

온-프레미스 NAS 스토리지에서 HPC Cache를 사용하려면 온-프레미스 네트워크의 특정 포트가 Azure HPC Cache 서브넷의 무제한 트래픽을 허용하는지 확인해야 합니다. 

- [NFS 스토리지 액세스용 포트를 구성하는 방법](hpc-cache-prerequisites.md#nfs-storage-requirements)

- [보안 규칙을 사용하여 네트워크 보안 그룹을 만드는 방법](../virtual-network/tutorial-filter-network-traffic.md)

 

- [Azure Firewall을 배포하고 구성하는 방법](../firewall/tutorial-firewall-deploy-portal.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="ns-2-connect-private-networks-together"></a>NS-2: 여러 사설망 함께 연결

**지침**: Azure ExpressRoute 또는 Azure VPN(가상 사설망)을 사용하면 공동 배치 환경의 온-프레미스 인프라와 Azure 데이터 센터와 간에 프라이빗 연결을 만들 수 있습니다. ExpressRoute 연결은 공용 인터넷을 통해 이동하지 않으며 일반적인 인터넷 연결보다 안정적이고 속도가 빠르며 대기 시간이 짧습니다. 지점 및 사이트 간 VPN 및 사이트 간 VPN의 경우 이러한 VPN 옵션과 Azure ExpressRoute를 조합하여 온-프레미스 디바이스 또는 네트워크를 가상 네트워크에 연결할 수 있습니다. 

Azure에 둘 이상의 가상 네트워크를 함께 연결하려면 가상 네트워크 피어링을 사용합니다. 피어링된 가상 네트워크 간의 네트워크 트래픽은 비공개이며 Azure 백본 네트워크에 유지됩니다.

- [ExpressRoute 연결 모델이란?](../expressroute/expressroute-connectivity-models.md) 

- [Azure VPN 개요](../vpn-gateway/vpn-gateway-about-vpngateways.md) 

- [가상 네트워크 피어링](../virtual-network/virtual-network-peering-overview.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="ns-3-establish-private-network-access-to-azure-services"></a>NS-3: Azure 서비스에 대한 프라이빗 네트워크 액세스 설정

**지침**: Azure Virtual Network 서비스 엔드포인트를 사용하여 HPC Cache에 대한 보안 액세스를 제공합니다. 서비스 엔드포인트는 인터넷을 거치지 않고 Azure 백본 네트워크를 통해 최적화된 경로입니다. 

HPC Cache는 Azure Private Link를 사용하여 사설망에 대한 관리 엔드포인트를 보호하는 기능을 지원하지 않습니다. 

프라이빗 액세스는 Azure 서비스에서 제공하는 인증 및 트래픽 보안 외에 추가적인 심층 방어 수단입니다.

- [Virtual Network 서비스 엔드포인트 이해](../virtual-network/virtual-network-service-endpoints-overview.md) 

- [Azure HPC Cache를 탑재하는 방법 이해](hpc-cache-mount.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="ns-4-protect-applications-and-services-from-external-network-attacks"></a>NS-4: 외부의 네트워크 공격으로부터 애플리케이션 및 서비스 보호

**지침**: 분산 서비스 거부(DDoS) 공격, 애플리케이션 관련 공격, 원치 않는 잠재적 악성 인터넷 트래픽 등의 외부 네트워크의 공격으로부터 HPC Cache 리소스를 보호합니다. 

이러한 보호를 위해 Azure에는 기본 기능이 포함되어 있습니다. 

- Azure Firewall을 사용하면 인터넷 및 기타 외부 위치의 잠재적인 악성 트래픽으로부터 애플리케이션 및 서비스를 보호할 수 있습니다. 
- Azure 가상 네트워크에서 DDoS 표준 보호를 사용하도록 설정하면 DDoS 공격으로부터 자산을 보호할 수 있습니다. 
- Azure Security Center를 사용하면 네트워크 리소스와 관련하여 잘못된 구성 위험을 감지할 수 있습니다.

Azure HPC Cache는 웹 애플리케이션을 실행하기 위한 것이 아니며, 웹 애플리케이션을 대상으로 하는 외부 네트워크 공격으로부터 보호하기 위해 추가 설정을 구성하거나 추가 네트워크 서비스를 배포할 필요가 없습니다.

- [Azure Firewall 설명서](../firewall/index.yml) 

- [Azure Portal을 사용하여 Azure DDoS Protection 표준 관리](../ddos-protection/manage-ddos-protection.md) 

- [Azure Security Center 권장 사항](../security-center/recommendations-reference.md#recs-networking)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="ns-5-deploy-intrusion-detectionintrusion-prevention-systems-idsips"></a>NS-5: IDS/IPS(침입 탐지 시스템/침입 방지 시스템) 배포

**지침**: Azure Firewall을 위협 인텔리전스 기반 필터링과 함께 사용하여, 알려진 악성 IP 주소 및 도메인을 오가는 트래픽을 경고 및/또는 차단합니다. IP 주소 및 도메인은 Microsoft 위협 인텔리전스 피드에서 제공됩니다. 

페이로드 검사가 필요하면 페이로드 검사 기능이 있는 Azure Marketplace의 타사 IDS/IPS(침입 탐지 시스템/침입 방지 시스템) 솔루션을 배포하면 됩니다. 또는 네트워크 기반 IDS/IPS와 함께 또는 대신 호스트 기반 IDS/IPS 또는 호스트 기반 EDR(엔드포인트 검색 및 응답) 솔루션을 사용하도록 선택할 수 있습니다.

참고: IDS/IPS 사용에 대한 규제나 기타 요구 사항이 있는 경우에는 SIEM 솔루션에 고품질 경고를 제공하도록 항상 조정되어 있는지 확인하십시오.

- [Azure Firewall을 배포하는 방법](../firewall/tutorial-firewall-deploy-portal.md) 

- [Azure Marketplace 타사 IDS 기능](https://azuremarketplace.microsoft.com/marketplace?search=IDS) 

- [Microsoft Defender ATP EDR 기능](/windows/security/threat-protection/microsoft-defender-atp/overview-endpoint-detection-response)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="ns-6-simplify-network-security-rules"></a>NS-6: 네트워크 보안 규칙 간소화

**지침**: 해당 없음; 이 권장 사항은 Azure Virtual Network에 배포할 수 있거나 효율적인 관리를 위해 허용된 IP 범위 그룹을 정의하는 기능이 있는 제품을 위한 것입니다. HPC Cache는 현재 서비스 태그를 지원하지 않습니다. 

각 캐시에 대해 새 서브넷을 만드는 것이 가장 좋습니다. 캐시를 만드는 과정에서 새 가상 네트워크 및 서브넷을 만들 수 있습니다.

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="ns-7-secure-domain-name-service-dns"></a>NS-7: 보안 DNS(Domain Name Service)

**지침**: DNS 보안에 대한 모범 사례에 따라 현수 DNS, DNS 증폭 공격, DNS 포이즈닝 및 스푸핑 등의 일반적인 공격을 완화합니다.

Azure HPC Cache가 캐시의 프라이빗 가상 네트워크 외부에 있는 리소스에 액세스하려면 DNS가 필요합니다. 워크플로에 Azure 외부 리소스가 포함된 경우에는 Azure DNS를 사용하는 것 외에도 자체 DNS 서버를 설정하고 보호해야 합니다.

- Azure Blob Storage 엔드포인트, Azure 기반 클라이언트 컴퓨터 또는 기타 Azure 리소스에 액세스하려면 Azure DNS를 사용합니다.
- 온-프레미스 스토리지에 액세스하거나 Azure 외부 클라이언트의 캐시에 연결하려면 해당 호스트 이름을 확인할 수 있는 사용자 지정 DNS 서버를 만들어야 합니다.
- 워크플로에 내부 및 외부 리소스가 모두 포함된 경우에는 Azure 관련 확인 요청을 Azure DNS 서버로 전달하도록 사용자 지정 DNS 서버를 설정합니다. 

Azure DNS를 신뢰할 수 있는 DNS 서비스로 사용하는 경우에는 Azure RBAC 및 리소스 잠금을 사용하여 DNS 영역 및 레코드가 우발적으로 또는 악의적으로 수정되지 않도록 보호해야 합니다.

자체 DNS 서버를 구성하는 경우에는 다음 보안 지침을 따라야 합니다.

- [보안 DNS(Domain Name System) 배포 가이드](https://csrc.nist.gov/publications/detail/sp/800-81/2/final) 

- [현수 DNS 항목 방지 및 하위 도메인 인수 방지](../security/fundamentals/subdomain-takeover.md) 

- [HPC Cache에 대한 DNS 액세스 요구 사항에 대해 자세히 알아보기](hpc-cache-prerequisites.md#dns-access)

- [Azure DNS 개요](../dns/dns-overview.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

## <a name="identity-management"></a>ID 관리

자세한 내용은 [Azure Security Benchmark: ID 관리](../security/benchmarks/security-controls-v2-identity-management.md)를 참조하세요.

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: Azure Active Directory를 중앙 ID 및 인증 시스템으로 표준화

**지침**: Azure HPC Cache는 내부 작업을 위해 Azure Active Directory와 통합되지 않습니다. 하지만 Azure AD를 사용하여 HPC Cache 배포 및 관련 구성 요소를 만들고, 보고, 관리하기 위해 Azure Portal 또는 CLI에서 사용자를 인증할 수 있습니다.

Azure AD(Azure Active Directory)는 Azure의 기본 ID 및 액세스 관리 서비스입니다. Azure AD를 표준화하여 다음 리소스에서 조직의 ID 및 액세스 관리를 관리해야 합니다.

- Microsoft 클라우드 리소스(예: Azure Portal, Azure Storage, Azure Virtual Machines(Linux 및 Windows), Azure Key Vault, PaaS 및 SaaS 애플리케이션)

- 조직의 리소스(예: Azure의 애플리케이션 또는 회사 네트워크 리소스)

Azure AD 보안은 조직의 클라우드 보안 사례에서 높은 우선 순위여야 합니다. Azure AD는 Microsoft의 모범 사례 권장 사항을 기준으로 ID 보안 태세를 평가하는 데 도움이 되는 ID 보안 점수를 제공합니다. 점수를 사용하여 구성이 모범 사례 권장 사항에 얼마나 근접하게 일치하는지 측정하고 보안 태세를 개선할 수 있습니다.

참고: Azure AD는 Microsoft 계정이 없는 사용자가 자신의 외부 ID를 사용하여 애플리케이션 및 리소스에 로그인할 수 있도록 외부 ID를 지원합니다.

- [Azure Active Directory의 테넌시](../active-directory/develop/single-and-multi-tenant-apps.md) 

- [Azure AD 인스턴스를 만들고 구성하는 방법](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) 

- [애플리케이션에 외부 ID 공급자 사용](../active-directory/external-identities/identity-providers.md) 

- [Azure Active Directory의 ID 보안 점수란?](../active-directory/fundamentals/identity-secure-score.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="im-2-manage-application-identities-securely-and-automatically"></a>IM-2: 애플리케이션 ID를 안전하게 자동으로 관리

**지침**: HPC Cache는 서비스 또는 자동화와 같은 비사용자 계정에 대해 Azure 관리 ID를 사용합니다. 보다 강력한 사용자 계정을 만드는 대신 Azure의 관리 ID 기능을 사용하여 리소스에 액세스하거나 실행하는 것이 좋습니다. 

HPC Cache는 미리 정의된 액세스 권한 부여 규칙을 통해 Azure AD 인증을 지원하는 Azure 서비스/리소스에 대해 기본적으로 인증될 수 있습니다. 이렇게 하면 소스 코드 또는 구성 파일에서 하드 코딩된 자격 증명을 사용하지 않아도 됩니다.

- [Azure 관리 ID](../active-directory/managed-identities-azure-resources/overview.md) 

- [Azure 리소스에 대한 관리 ID를 지원하는 서비스](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3: 애플리케이션 액세스에 Azure AD SSO(Single Sign-On) 사용

**지침**: Azure HPC Cache는 내부 작업을 위해 Azure AD와 통합되지 않습니다. 하지만 Azure AD를 사용하여 HPC Cache 배포 및 관련 구성 요소를 만들고, 보고, 관리하기 위해 Azure Portal 또는 CLI에서 사용자를 인증할 수 있습니다.

Azure Active Directory는 Azure 리소스, 클라우드 애플리케이션, 온-프레미스 애플리케이션에 대한 ID 및 액세스 관리를 제공합니다. 여기에는 엔터프라이즈 ID(예: 직원) 및 외부 ID(예: 파트너, 공급업체 및 공급자)가 포함됩니다. 이를 통해 SSO(Single Sign-On)가 온-프레미스 및 클라우드에서 조직의 데이터와 리소스에 대한 액세스를 관리하고 보호할 수 있습니다. 모든 사용자, 애플리케이션, 디바이스를 Azure AD에 연결하여 원활하고 안전하게 액세스하고 가시성과 제어를 강화할 수 있습니다.

- [Azure AD에서의 애플리케이션 SSO 이해](../active-directory/manage-apps/what-is-single-sign-on.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: 모든 Azure Active Directory 기반 액세스에 강력한 인증 제어 사용

**지침**: Azure HPC Cache는 내부 작업을 위해 Azure AD와 통합되지 않지만, Azure AD를 사용하여 HPC Cache 배포 및 관련 구성 요소를 만들고, 보고, 관리하기 위해 Azure Portal 또는 CLI에서 사용자를 인증할 수 있습니다.  

Azure AD는 MFA(다단계 인증)를 통한 강력한 인증 컨트롤 및 강력한 암호 없는 메서드를 지원합니다.

- 다단계 인증: Azure AD MFA를 사용하도록 설정하고 MFA 설정의 몇 가지 모범 사례에 대한 Azure Security Center ID 및 액세스 관리 권장 사항을 따릅니다. MFA는 로그인 조건 및 위험 요인에 따라 모든 사용자, 일부 사용자 또는 사용자 단위 수준에서 적용할 수 있습니다.
- 암호 없는 인증 – 세 가지 암호 없는 인증 옵션을 사용할 수 있습니다. Windows Hello for Business, Microsoft Authenticator 앱 및 스마트 카드와 같은 온-프레미스 인증 방법입니다.

관리자 및 권한 있는 사용자의 경우 가장 높은 수준의 강력한 인증 방법을 사용해야 합니다. 기타 사용자에게는 적절하며 강력한 인증 정책을 펼쳐야 합니다.

Azure HPC Cache는 캐시에 연결하는 클라이언트 시스템에 대한 레거시 암호 기반 인증도 지원합니다. 이런 종류의 연결에는 기준 암호 정책이 있는 클라우드 전용 계정(Azure에서 직접 만든 사용자 계정) 또는 온-프레미스 암호 정책을 따르는 하이브리드 계정(온-프레미스 Active Directory에서 가져온 사용자 계정)이 포함됩니다. 

암호 기반 인증을 사용하는 경우 Azure AD는 사용자가 추측하기 쉬운 암호를 설정하지 못하도록 하는 암호 보호 기능을 제공합니다. Microsoft는 원격 분석 데이터를 기반으로 업데이트되는 금지된 암호의 전체 목록을 제공하며, 고객은 필요(예: 브랜드, 문화권 참조 등)에 따라 목록을 보강할 수 있습니다. 이러한 암호 보호는 클라우드 전용 및 하이브리드 계정에 사용할 수 있습니다.

참고: 암호 자격 증명에만 기반한 인증은 일반적인 공격 방법에 취약합니다. 보안을 강화하려면 MFA 및 강력한 암호 정책과 같은 강력한 인증을 사용합니다. 기본 암호가 있는 타사 애플리케이션 및 마켓플레이스 서비스를 사용하는 경우에는 서비스를 처음 설정할 때 새로운 보안 암호를 설정합니다. 

- [Azure에서 MFA를 사용하도록 설정하는 방법](../active-directory/authentication/howto-mfa-getstarted.md) 

- [Azure Active Directory에 대한 암호 없는 인증 옵션 소개](../active-directory/authentication/concept-authentication-passwordless.md)

- [Azure AD 기본 암호 정책](../active-directory/authentication/concept-sspr-policy.md#password-policies-that-only-apply-to-cloud-user-accounts)

- [Azure AD 암호 보호를 사용하여 잘못된 암호 제거](../active-directory/authentication/concept-password-ban-bad.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5: 비정상 계정 활동 모니터링 및 경고

**지침**: Azure HPC Cache는 Azure Portal에서 사용자 관리를 위해 Azure Active Directory를 사용할 수 있습니다.  Azure Active Directory는 다음과 같은 데이터 원본을 제공합니다.

- 로그인 – 로그인 보고서는 관리형 애플리케이션 및 사용자 로그인 활동의 사용에 대한 정보를 제공합니다.

- 감사 로그 - Azure AD 내의 다양한 기능에 의해 수행된 모든 변경 내용에 대한 로그를 통한 추적 기능을 제공합니다. 감사 로그의 예제로는 사용자, 앱, 그룹, 역할 및 정책 추가 또는 제거와 같은 Azure AD 내의 모든 리소스에 대한 변경 내용이 있습니다.

- 위험한 로그인 - 위험한 로그인은 사용자 계정의 정당한 소유자가 아닌 사용자에 의해 수행된 로그인 시도에 대한 지표입니다.

- 위험 플래그가 지정된 사용자 - 위험한 사용자는 손상되었을 수 있는 사용자 계정에 대한 표시기입니다.

이러한 데이터 원본은 Azure Monitor, Azure Sentinel 또는 타사 SIEM 시스템과 통합할 수 있습니다.

또한 Azure Security Center는 과도한 인증 시도 실패와 같은 의심스러운 특정 활동 및 구독에서 더 이상 사용되지 않는 계정에 대해 경고할 수 있습니다.

Azure ATP(Advanced Threat Protection)는 Active Directory 신호를 사용하여 지능형 위협, 손상된 ID 및 악의적인 참가자 작업을 식별, 탐지 및 조사할 수 있는 보안 솔루션입니다.

- [Azure Active Directory의 감사 활동 보고서](../active-directory/reports-monitoring/concept-audit-logs.md)

- [Azure AD 위험한 로그인을 확인하는 방법](../active-directory/identity-protection/overview-identity-protection.md) 

- [위험한 활동에 대해 플래그가 지정된 Azure AD 사용자를 식별하는 방법](../active-directory/identity-protection/overview-identity-protection.md) 

- [Azure Security Center에서 사용자의 ID 및 액세스 활동을 모니터링하는 방법](../security-center/security-center-identity-access.md)

 

- [Azure Security Center 위협 인텔리전스 보호 모듈의 경고](../security-center/alerts-reference.md) 

- [Azure 활동 로그를 Azure Monitor에 통합하는 방법](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Azure Security Center 모니터링**: 예

**책임**: 공유됨

### <a name="im-7-eliminate-unintended-credential-exposure"></a>IM-7: 의도하지 않은 자격 증명 노출 제거

**지침**: 해당 없음; HPC Cache는 고객이 실행 환경에 영구 데이터를 배포하도록 허용하지 않습니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="privileged-access"></a>권한 있는 액세스

자세한 내용은 [Azure Security Benchmark: 권한 있는 액세스](../security/benchmarks/security-controls-v2-privileged-access.md)를 참조하세요.

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: 중요 비즈니스용 시스템에 대한 관리 액세스 제한

**지침**: HPC Cache는 Azure RBAC를 사용하여 구독 및 관리 그룹에 대한 권한 있는 액세스 권한이 부여된 계정을 제한함으로써 중요 비지니스용 시스템에 대한 액세스를 격리합니다.

전용 관리 계정 사용에 대한 표준 운영 절차를 만듭니다. 캐시를 생성하기 위해 HPC Cache를 사용하려면 구독에서 NIC를 생성하기에 충분한 권한이 사용자에게 있어야 합니다. Blob Storage를 사용하는 경우, HPC Cache가 스토리지에 액세스하려면 Azure 역할 Storage 계정 기여자 및 Storage Blob 데이터 기여자가 필요합니다. 

또한 중요 비즈니스용 시스템에 에이전트를 설치하여 Active Directory 도메인 컨트롤러(DC), 보안 도구 및 시스템 관리 도구 등의 중요 비즈니스용 자산에 대한 관리 액세스 권한이 있는 관리, ID 및 보안 시스템에 대한 액세스를 제한해야 합니다. 이러한 관리 및 보안 시스템을 손상시키는 공격자는 이러한 사항을 즉시 무기화하여 중요 비즈니스용 자산을 손상시킬 수 있습니다.

모든 유형의 액세스 제어는 일관된 액세스 제어를 보장하기 위해 엔터프라이즈 조각화 전략에 맞게 조정되어야 합니다.

- [Azure HPC Cache RBAC 권한](hpc-cache-prerequisites.md#permissions)

- [Azure 구성 요소 및 참조 모델](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151) 

- [관리 그룹 액세스](../governance/management-groups/overview.md#management-group-access)

- [Azure 구독 관리자](../cost-management-billing/manage/add-change-subscription-administrator.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3: 정기적으로 사용자 액세스 검토 및 조정

**지침**: 사용자 계정 및 액세스 할당을 정기적으로 검토하여 계정 및 해당 액세스 수준이 유효한지 확인합니다. 

Azure HPC Cache는 Azure AD(Azure Active Directory) 계정을 사용하여 Azure Portal 및 관련 인터페이스를 통해 사용자 액세스를 관리할 수 있습니다. Azure AD는 액세스 검토를 제공하며, 이를 통해 그룹 멤버 자격, 애플리케이션에 대한 액세스 및 역할 할당을 검토할 수 있습니다. Azure AD 보고는 부실 계정을 검색하는 데 도움이 되는 로그를 제공할 수 있습니다. Azure AD Privileged Identity Management를 사용하여 액세스 검토 보고서 워크플로를 만들어서 액세스 검토 프로세스를 용이하게 할 수도 있습니다.

또한 과도한 수의 관리자 계정이 만들어질 때 경고하고, 부실하거나 부적절하게 구성된 관리자 계정을 식별하도록 Azure Privileged Identity Management를 구성할 수 있습니다.

참고: 일부 Azure 서비스는 Azure AD를 통해 관리되지 않는 로컬 사용자 및 역할을 지원합니다. 이러한 사용자를 별도로 관리하는 것은 사용자의 책임입니다.

NFS 스토리지 대상을 사용하는 경우 네트워크 관리자 및 방화벽 관리자와 협력하여 액세스 설정을 확인하고 Azure HPC Cache가 NFS 스토리지 시스템과 통신할 수 있는지 확인해야 합니다.

- [HPC Cache 권한 목록은 Azure HPC Cache 필수 구성 요소를 참조하세요.](hpc-cache-prerequisites.md) 

- [PIM(Privileged Identity Management)에서 Azure 리소스 역할에 대한 액세스 검토 만들기](../active-directory/privileged-identity-management/pim-resource-roles-start-access-review.md) 

- [Azure AD ID 및 액세스 검토를 사용하는 방법](../active-directory/governance/access-reviews-overview.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4: Azure AD에서 응급 액세스 설정

**지침**: HPC Cache는 Azure Active Directory를 사용하여 Azure Portal을 통해 리소스 액세스를 관리할 수 있습니다. Azure AD 조직이 실수로 잠기는 것을 방지하려면 일반 관리 계정을 사용할 수 없을 경우 액세스를 위한 응급 액세스 계정을 설정합니다. 응급 액세스 계정은 일반적으로 권한이 높으며 특정 사용자에게 할당되면 안 됩니다. 응급 액세스 계정은 일반 관리 계정을 사용할 수 없는 '비상' 시나리오의 긴급한 상황으로 제한됩니다.

응급 액세스 계정의 자격 증명(예: 암호, 인증서 또는 스마트 카드)을 안전하게 유지하고 비상시에만 사용할 권한이 있는 사용자에게만 알립니다.

- [Azure AD에서 응급 액세스 계정 관리](../active-directory/roles/security-emergency-access.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="pa-5-automate-entitlement-management"></a>PA-5: 권한 관리 자동화 

**지침**: HPC Cache는 Azure Active Directory를 사용하여 Azure Portal을 통해 캐시 리소스에 대한 액세스를 관리할 수 있습니다. 

Azure AD 권한 관리 기능을 사용하여 액세스 권한 할당, 검토, 만료 등의 액세스 요청 워크플로를 자동화할 수 있습니다. 이중 또는 다단계 승인도 지원됩니다. 

- [Azure AD 액세스 검토란?](../active-directory/governance/access-reviews-overview.md) 

- [Azure AD 권한 관리란?](../active-directory/governance/entitlement-management-overview.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7: 충분한 관리 수행(최소 권한 원칙) 

**지침**: HPC Cache는 Azure RBAC(역할 기반 액세스 제어)와 통합되어 해당 리소스를 관리합니다. Azure RBAC를 사용하면 역할 할당을 통해 Azure 리소스 액세스를 관리할 수 있습니다. 이러한 역할은 사용자, 그룹 서비스 주체 및 관리 ID에 할당할 수 있습니다. 특정 리소스에 대해 미리 정의된 기본 제공 역할이 있으며 이러한 역할은 Azure CLI, Azure PowerShell 또는 Azure Portal과 같은 도구를 통해 쿼리하거나 인벤토리에 포함할 수 있습니다. 

Azure RBAC를 통해 리소스에 할당하는 권한은 항상 역할에 필요한 권한으로 제한되어야 합니다. 이러게 Azure AD PIM(Privileged Identity Management)의 JIT(Just-in-Time) 접근 방식을 보완하고 정기적으로 검토해야 합니다.

기본 제공 역할을 사용하여 권한을 할당하고 필요한 경우에만 사용자 지정 역할을 만듭니다.

- [Azure RBAC(Azure 역할 기반 액세스 제어)란?](../role-based-access-control/overview.md) 

- [Azure RBAC를 구성하는 방법](../role-based-access-control/role-assignments-portal.md) 

- [Azure AD ID 및 액세스 검토를 사용하는 방법](../active-directory/governance/access-reviews-overview.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

## <a name="data-protection"></a>데이터 보호

자세한 내용은 [Azure Security Benchmark: 데이터 보호](../security/benchmarks/security-controls-v2-data-protection.md)를 참조하세요.

### <a name="dp-1-discover-classify-and-label-sensitive-data"></a>DP-1: 중요한 데이터 검색, 분류 및 레이블 지정 

**지침**: HPC Cache는 중요한 데이터를 관리하지만 중요한 데이터를 검색, 분류 및 레이블 지정하는 기능이 없습니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 공유됨

### <a name="dp-2-protect-sensitive-data"></a>DP-2: 중요한 데이터 보호

**지침**: Azure RBAC(Azure 역할 기반 액세스 제어), 네트워크 기반 액세스 제어 및 Azure 서비스의 특정 제어(예: SQL 및 기타 데이터베이스의 암호화)를 통해 액세스를 제한하여 중요한 데이터를 보호합니다.

일관된 액세스 제어를 보장하려면 모든 유형의 액세스 제어를 엔터프라이즈 조각화 전략에 맞게 조정해야 합니다. 또한 엔터프라이즈 조각화 전략은 중요 비즈니스용 데이터 및 시스템의 위치를 통해 알려야 합니다.

Microsoft에서 관리하는 기본 플랫폼의 경우 Microsoft는 모든 고객 콘텐츠를 중요한 것으로 간주하고, 고객 데이터 손실 및 노출로부터 보호합니다. Azure 내의 고객 데이터를 안전하게 유지하기 위해 Microsoft는 몇 가지 기본 데이터 보호 제어 및 기능을 구현했습니다.

- [Azure RBAC(역할 기반 액세스 제어)](../role-based-access-control/overview.md) 

- [Azure의 고객 데이터 보호 이해](../security/fundamentals/protection-customer-data.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: 공유됨

### <a name="dp-3-monitor-for-unauthorized-transfer-of-sensitive-data"></a>DP-3: 중요한 데이터의 무단 전송 모니터링

**참고**: HPC Cache는 중요한 데이터를 전송하지만 중요한 데이터의 무단 전송에 대한 모니터링은 지원하지 않습니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 공유됨

### <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4: 전송 중인 중요한 정보 암호화

**지침**: HPC Cache는 TLS v1.2 이상을 사용하여 전송 중 데이터 암호화를 지원합니다.

사설망의 트래픽에서는 선택 사항이지만, 외부 및 공용 네트워크의 트래픽에서 매우 중요합니다. HTTP 트래픽의 경우 Azure 리소스에 연결하는 모든 클라이언트에서 TLS v1.2 이상을 협상할 수 있는지 확인합니다. 원격 관리의 경우 암호화되지 않은 프로토콜 대신 SSH(Linux용) 또는 RDP/TLS(Windows용)를 사용합니다. 더 이상 사용되지 않는 SSL, TLS 및 SSH 버전/프로토콜 및 약한 암호는 사용하지 않도록 설정해야 합니다.

Azure는 기본적으로 Azure 데이터 센터 간에 전송 중인 데이터에 대한 암호화를 제공합니다.

- [Azure를 통한 전송 중 암호화 이해](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit) 

- [TLS 보안에 대한 정보](/security/engineering/solving-tls1-problem) 

- [전송 중인 Azure 데이터에 대한 이중 암호화](../security/fundamentals/double-encryption.md#data-in-transit)

**Azure Security Center 모니터링**: 예

**책임**: 공유됨

### <a name="dp-5-encrypt-sensitive-data-at-rest"></a>DP-5: 중요한 미사용 데이터 암호화

**지침**: 액세스 제어를 보완하려면 암호화를 사용하여 미사용 데이터를 "대역 외" 공격(예: 기본 스토리지 액세스)으로부터 보호해야 합니다. 이렇게 하면 공격자가 데이터를 쉽게 읽거나 수정할 수 없습니다.

Azure는 기본적으로 미사용 데이터 암호화를 제공합니다. 매우 중요한 데이터의 경우, 사용 가능한 모든 Azure 리소스에서 미사용 시 암호화를 추가로 구현하는 옵션을 사용할 수 있습니다. Azure는 기본적으로 암호화 키를 관리하지만, Azure는 특정 Azure 서비스에 대한 자체 키(고객 관리형 키)를 관리하는 옵션을 제공합니다.

캐시 디스크를 포함하여 Azure에 저장된 모든 데이터는 기본적으로 Microsoft 관리형 키를 사용하여 미사용 상태에서 암호화됩니다. 데이터를 암호화하는 데 사용되는 키를 관리하려는 경우에만 Azure HPC Cache 설정을 사용자 지정하면 됩니다.

컴퓨팅 리소스에 대한 규정 준수를 위해 필요한 경우에는 자동화된 호스트 기반 데이터 손실 방지 솔루션과 같은 타사 도구를 구현하여 데이터가 시스템에서 복사되는 경우에도 데이터에 대한 액세스 제어를 적용합니다.

- [Azure HPC Cache에서 고객 관리형 암호화 키를 사용하는 방법](./hpc-cache-create.md?tabs=azure-portal#enable-azure-key-vault-encryption-optional)

- [Azure의 저장 데이터 암호화 이해](../security/fundamentals/encryption-atrest.md#encryption-at-rest-in-microsoft-cloud-services) 

- [고객 관리형 암호화 키를 구성하는 방법](../storage/common/customer-managed-keys-configure-key-vault.md) 

- [암호화 모델 및 키 관리 테이블](../security/fundamentals/encryption-atrest.md)

 

- [Azure의 미사용 데이터 이중 암호화](../security/fundamentals/double-encryption.md#data-at-rest)

**Azure Security Center 모니터링**: 해당 없음

**책임**: 공유됨

## <a name="asset-management"></a>자산 관리

자세한 내용은 [Azure Security Benchmark: 자산 관리](../security/benchmarks/security-controls-v2-asset-management.md)를 참조하세요.

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1: 보안 팀에서 자산 위험에 대한 가시성을 확보하도록 보장

**지침**: Azure Security Center를 사용하여 보안 위험을 모니터링할 수 있도록 Azure 테넌트 및 구독에서 보안 읽기 권한자 권한을 보안 팀에 부여합니다. 

보안 팀의 책임이 구성된 방식에 따라 보안 위험 모니터링은 중앙 보안 팀 또는 로컬 팀의 책임이 될 수 있습니다. 즉, 보안 인사이트 및 위험이 항상 조직 내의 중앙에서 집계되어야 합니다. 

보안 읽기 권한자 권한은 전체 테넌트(루트 관리 그룹)에 광범위하게 적용하거나 범위를 관리 그룹 또는 특정 구독으로 지정할 수 있습니다. 

참고: 워크로드 및 서비스에 대한 가시성을 얻으려면 추가 권한이 필요할 수 있습니다. 

- [보안 읽기 권한자 역할 개요](../role-based-access-control/built-in-roles.md#security-reader)

- [Azure 관리 그룹 개요](../governance/management-groups/overview.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2: 보안 팀에 자산 인벤토리 및 메타데이터에 대한 액세스 권한이 있는지 확인

**지침**: Azure HPC Cache는 태그 사용을 지원합니다. Azure 리소스, 리소스 그룹, 구독에 태그를 적용하여 논리적인 분류 체계로 구성합니다. 각 태그는 이름과 값 쌍으로 이루어져 있습니다. 

예를 들어 프로덕션의 모든 리소스에 "환경" 이름과 "프로덕션" 값을 적용할 수 있습니다. 태그는 캐시를 만들 때는 물론 캐시가 배포된 후에도 추가할 수 있습니다. 

Azure Virtual Machine 인벤토리를 사용하여 Virtual Machines의 소프트웨어에 대한 정보 수집을 자동화할 수 있습니다. 소프트웨어 이름, 버전, 게시자 및 새로 고침 시간은 Azure Portal에서 사용할 수 있습니다. 설치 날짜 및 기타 정보에 대한 액세스 권한을 얻으려면 게스트 수준 진단을 사용하도록 설정하고 Windows 이벤트 로그를 Log Analytics 작업 영역으로 가져와야 합니다.
HPC Cache는 애플리케이션을 실행하거나 자체 리소스에 소프트웨어를 설치하도록 허용하지 않습니다. 

- [Azure Resource Graph Explorer를 사용하여 쿼리를 만드는 방법](../governance/resource-graph/first-query-portal.md) 

- [Azure Security Center 자산 인벤토리 관리](../security-center/asset-inventory.md) 

- [리소스 명명 및 태그 지정 의사 결정 가이드](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=%2fazure%2fazure-resource-manager%2fmanagement%2ftoc.json) 

- [Azure 가상 머신 인벤토리를 사용하도록 설정하는 방법](../automation/automation-tutorial-installed-software.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: 승인된 Azure 서비스만 사용

**지침**: HPC Cache는 Azure Resource Manager 배포를 지원합니다. Azure Policy를 사용하여 환경에서 사용자가 프로비저닝할 수 있는 서비스를 감사하고 제한합니다. Azure Resource Graph를 사용하여 구독 내에서 리소스를 쿼리하고 검색합니다. 또한 Azure Monitor를 사용하여 승인되지 않은 서비스가 검색되면 경고를 트리거하는 규칙을 만들 수 있습니다.

- [Azure Policy 구성 및 관리](../governance/policy/tutorials/create-and-manage.md) 

- [Azure Policy를 사용하여 특정 리소스 종류를 거부하는 방법](../governance/policy/samples/index.md) 

- [Azure Resource Graph Explorer를 사용하여 쿼리를 만드는 방법](../governance/resource-graph/first-query-portal.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>AM-4: 자산 수명 주기 관리의 보안 보장

**지침**: 해당 사항 없음 Azure HPC Cache는 수명 주기 관리 프로세스에서 자산에 대한 보안을 보장하는 데 사용할 수 없습니다. 높은 영향을 미치는 것으로 간주되는 자산의 네트워크 구성과 특성을 유지 관리하는 것은 고객의 책임입니다. 

특성 및 네트워크 구성 변경 사항을 캡처하고 변경 영향을 측정하고 해당하는 경우 수정 작업을 생성하는 프로세스를 만드는 것이 좋습니다.

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

## <a name="logging-and-threat-detection"></a>로깅 및 위협 탐지

자세한 내용은 [Azure Security Benchmark: 로깅 및 위협 탐지](../security/benchmarks/security-controls-v2-logging-threat-detection.md)를 참조하세요.

### <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1: Azure 리소스에 위협 탐지 사용

**지침**: Azure Security Center 기본 제공 위협 탐지 기능을 사용하고 HPC Cache 리소스에 대해 Azure Defender(공식 이름 Azure Advanced Threat Protection)를 사용하도록 설정합니다. HPC Cache용 Azure Defender는 캐시 리소스에 액세스하거나 악용하려는 비정상적이고 잠재적으로 유해한 시도를 탐지하는 추가 보안 인텔리전스 계층을 제공합니다.

사용자 지정 위협 탐지를 설정하는 데 사용할 수 있는 HPC Cache의 모든 로그를 SIEM으로 전달합니다. 다양한 유형의 Azure 자산에서 잠재적인 위협과 이상 징후를 모니터링해야 확인합니다. 경고의 품질을 높이는 데 집중하여 분석가가 분류하는 가양성을 줄여야 합니다. 경고는 로그 데이터, 에이전트 또는 기타 데이터로부터 제공될 수 있습니다.

- [위협 방지 및 Azure Security Center](../security-center/azure-defender.md) 

- [Azure Security Center 보안 경고 참조 지침](../security-center/alerts-reference.md) 

- [위협 탐지를 위한 사용자 지정 분석 규칙 만들기](../sentinel/tutorial-detect-threats-custom.md) 

- [Azure Sentinel을 사용한 사이버 위협 인텔리전스](/azure/architecture/example-scenario/data/sentinel-threat-intelligence)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: Azure ID 및 액세스 관리에 위협 탐지 사용

**지침**: Azure AD는 Azure AD 보고를 통해 보거나 더 정교한 모니터링 및 분석 사용 사례를 위해 Azure Monitor, Azure Sentinel 또는 기타 SIEM/모니터링 도구와 통합할 수 있는 다음과 같은 사용자 로그를 제공합니다.
- 로그인 – 로그인 보고서는 관리되는 애플리케이션 및 사용자 로그인 활동의 사용 정보를 제공합니다.

- 감사 로그 - Azure AD 내의 다양한 기능에 의해 수행된 모든 변경 내용에 대한 로그를 통해 추적 가능성을 제공합니다. 감사 로그의 예제로는 사용자, 앱, 그룹, 역할 및 정책 추가 또는 제거와 같은 Azure AD 내의 모든 리소스에 대한 변경 내용이 있습니다.

- 위험한 로그인 - 위험한 로그인은 사용자 계정의 정당한 소유자가 아닌 사용자에 의해 수행된 로그인 시도에 대한 지표입니다.

- 위험 플래그가 지정된 사용자 - 위험한 사용자는 손상되었을 수 있는 사용자 계정에 대한 표시기입니다.

또한 Azure Security Center는 과도한 인증 시도 실패 횟수 또는 구독에서 사용되지 않는 계정과 같은 의심스러운 특정 활동에 대해 경고할 수 있습니다. 기본 보안 예방 조치 모니터링 외에도 Azure Security Center의 위협 방지 모듈은 개별 Azure 컴퓨팅 리소스(가상 머신, 컨테이너, 앱 서비스), 데이터 리소스(SQL DB 및 스토리지) 및 Azure 서비스 계층에서 더 심층적인 보안 경고를 수집할 수 있습니다. 이 기능을 사용하면 개별 리소스 내에서 비정상 계정 활동을 볼 수 있습니다.

- [Azure Active Directory의 감사 활동 보고서](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [Azure ID 보호 사용](../active-directory/identity-protection/overview-identity-protection.md) 

- [위협 방지 및 Azure Security Center](../security-center/azure-defender.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3: Azure 네트워크 활동에 대한 로깅 사용

**지침**: 일반적으로 사용 가능한 패킷 캡처 도구 외에도 VPN 게이트웨이 및 이것의 패킷 캡처 기능을 사용하여 가상 네트워크 간에 이동하는 네트워크 패킷을 기록할 수 있습니다.

Azure HPC Cache 리소스가 배포된 네트워크에 네트워크 보안 그룹을 배포합니다. 트래픽 감사를 위해 네트워크 보안 그룹에서 네트워크 보안 그룹 흐름 로그를 사용하도록 설정합니다.

흐름 로그는 스토리지 계정에 보관됩니다. 트래픽 분석 솔루션을 사용하도록 설정하여 이러한 흐름 로그를 처리하고 Log Analytics 작업 영역으로 보냅니다. 트래픽 분석은 Azure 네트워크의 트래픽 흐름에 대한 추가적인 인사이트를 제공합니다. 트래픽 분석은 네트워크 활동을 시각화하고, 핫 스폿을 식별하며, 보안 위협을 식별하고, 트래픽 흐름 패턴을 이해하며, 잘못된 네트워크 구성 정확히 찾아내는 데 유용할 수 있습니다.

가상 네트워크 외부의 리소스에 액세스하려면 캐시에 DNS가 필요합니다. 사용 중인 리소스에 따라 사용자 지정된 DNS 서버를 설정하고 해당 서버와 Azure DNS 서버 간에 전달을 구성해야 할 수도 있습니다. 

조직의 요구 사항에 맞는 DNS 로깅 솔루션을 위해 Azure Marketplace에서 타사 솔루션을 구현합니다.

- [VPN 게이트웨이에 대한 패킷 캡처 구성](../vpn-gateway/packet-capture.md) 

- [네트워크 보안 그룹 흐름 로그를 사용하도록 설정하는 방법](../network-watcher/network-watcher-nsg-flow-logging-portal.md) 

- [트래픽 분석을 사용하도록 설정하고 사용하는 방법](../network-watcher/traffic-analytics.md) 

- [Azure Security Center에서 제공하는 네트워크 보안 이해](../security-center/security-center-network-recommendations.md) 

- [DNS 사전 요구 사항에 대해 자세히 알아보기](hpc-cache-prerequisites.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: Azure 리소스에 대한 로깅 사용

**지침**: Azure HPC Cache 리소스는 활동 로그를 자동으로 만듭니다. 이 로그에는 모든 쓰기 작업(PUT, POST, DELETE)이 포함되지만 읽기 작업(GET)은 포함되지 않습니다. 활동 로그는 문제를 해결할 때 오류를 찾거나 조직의 사용자가 리소스를 수정한 방식을 모니터링하는 데 사용할 수 있습니다.

또한 Azure Security Center 및 Azure Policy를 사용하여 HPC Cache에 대한 Azure 리소스 로그를 사용하도록 설정하고 데이터 수집을 기록할 수 있습니다. 이러한 로그는 나중에 보안 인시던트를 조사하고 법정 분석 연습을 수행하는 데 중요할 수 있습니다.

- [Azure Monitor를 사용하여 플랫폼 로그 및 메트릭을 수집하는 방법](../azure-monitor/essentials/diagnostic-settings.md) 

- [Azure의 로깅 및 다양한 로깅 형식 이해](../azure-monitor/essentials/platform-logs-overview.md) 

- [Azure Security Center 데이터 수집 이해](../security-center/security-center-enable-data-collection.md)

**Azure Security Center 모니터링**: 예

**책임**: 공유됨

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: 보안 로그 관리 및 분석 중앙 집중화

**지침**: 로깅 스토리지 및 분석을 중앙 집중화하면 상관 관계를 활용할 수 있습니다. 각 로그 원본에 대해 데이터 소유자, 액세스 지침, 스토리지 위치, 데이터를 처리하고 액세스하는 데 사용되는 도구, 데이터 보존 요구 사항을 할당했는지 확인합니다.

Azure 활동 로그를 중앙 로깅에 통합하고 있는지 확인합니다. Azure Monitor를 통해 로그를 수집하여 엔드포인트 디바이스, 네트워크 리소스, 기타 보안 시스템에 의해 생성된 보안 데이터를 집계합니다. Azure Monitor에서 Log Analytics 작업 영역을 사용하여 분석을 쿼리 및 수행하고 장기 보관 스토리지에 Azure Storage 계정을 사용합니다.

또한 데이터를 Azure Sentinel 또는 타사 SIEM에서 사용하도록 설정하고 온보딩할 수 있습니다.

많은 조직에서 자주 사용되는 "핫" 데이터에 Azure Sentinel을 사용하고, 낮은 빈도로 사용되는 "콜드" 데이터에는 Azure Storage를 사용합니다.

- [Azure Monitor를 사용하여 플랫폼 로그 및 메트릭을 수집하는 방법](../azure-monitor/essentials/diagnostic-settings.md) 

- [Azure Sentinel을 온보딩하는 방법](../sentinel/quickstart-onboard.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

## <a name="incident-response"></a>사고 대응

자세한 내용은 [Azure Security Benchmark: 인시던트 응답](../security/benchmarks/security-controls-v2-incident-response.md)을 참조하세요.

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: 준비 - Azure에 대한 인시던트 응답 프로세스 업데이트

**지침**: 조직이 보안 인시던트에 대응하는 프로세스를 보유하고, 해당 프로세스를 Azure에 대해 업데이트했으며, 준비 상태를 확인하기 위해 프로세스를 정기적으로 수행하는지 확인합니다.

- [엔터프라이즈 환경에서 보안 구현](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [인시던트 응답 참조 가이드](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="ir-2-preparation--set-up-incident-notification"></a>IR-2: 준비 - 인시던트 알림 설정 

**지침**: Azure Security Center에서 보안 인시던트 연락처 정보를 설정합니다. 이 연락처 정보는 MSRC(Microsoft 보안 대응 센터)가 불법적인 당사자나 권한 없는 당사자가 데이터에 액세스한 것을 발견한 경우 Microsoft가 연락하는 데 사용됩니다. 또한 인시던트 응답 요구 사항에 따라 다양한 Azure 서비스에서 인시던트 경고 및 알림을 사용자 지정하는 옵션도 있습니다. 

- [Azure Security Center 보안 연락처를 설정하는 방법](../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: 탐지 및 분석 – 고품질 경고를 기반으로 인시던트 만들기

**지침**: 고품질 경고를 만들고 경고의 품질을 측정하는 프로세스가 있는지 확인합니다. 이렇게 하면 이전 인시던트에서 상황을 확인하고 분석가를 위한 경고의 우선 순위를 지정할 수 있으므로 가양성으로 인해 시간을 낭비하지 않습니다. 

고품질 경고는 이전 인시던트, 유효성이 검사된 커뮤니티 소스, 다양한 신호 원본을 결합하고 상관 관계를 설정하여 경고를 생성하고 정리하도록 설계된 도구 등에서 얻은 경험을 기반으로 구축할 수 있습니다. 

Azure Security Center는 많은 Azure 자산에 대해 고품질 경고를 제공합니다. ASC 데이터 커넥터를 사용하여 경고를 Azure Sentinel로 스트림할 수 있습니다. Azure Sentinel을 사용하면 조사를 위해 인시던트를 자동으로 생성하는 고급 경고 규칙을 만들 수 있습니다. 

Azure 리소스에 대한 위험을 식별하는 데 도움이 되도록 내보내기 기능을 사용하여 Azure Security Center 경고 및 추천 사항을 내보냅니다. 경고 및 추천 사항을 수동 또는 지속적인 방식으로 내보냅니다.

- [내보내기를 구성하는 방법](../security-center/continuous-export.md)

- [경고를 Azure Sentinel로 스트림하는 방법](../sentinel/connect-azure-security-center.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4: 검색 및 분석 – 인시던트 조사

**지침**: 분석가가 잠재적 인시던트를 조사할 때 다양한 데이터 원본을 쿼리하고 사용하여 발생한 상황에 대한 전체 보기를 작성할 수 있는지 확인합니다. 사각지대를 방지하기 위해 다양한 로그를 수집하여 킬 체인 전체에서 잠재적 공격자의 활동을 추적해야 합니다.  또한 다른 분석가 및 향후 기록 참조를 위해 인사이트 및 습득 지식을 캡처해야 합니다.  

조사할 데이터 원본에는 범위 내 서비스 및 실행 중인 시스템에서 이미 수집되고 있는 중앙 집중식 로깅 원본이 포함되지만 다음과 같은 원본도 포함될 수 있습니다.

- 네트워크 데이터 – 네트워크 보안 그룹의 흐름 로그, Azure Network Watcher 및 Azure Monitor를 사용하여 네트워크 흐름 로그 및 기타 분석 정보를 캡처합니다. 

- 실행 중인 시스템의 스냅샷 

    - Azure 가상 머신의 스냅샷 기능을 사용하여 실행 중인 시스템의 디스크에 대한 스냅샷을 만듭니다. 

    - 운영 체제의 기본 메모리 덤프 기능을 사용하여 실행 중인 시스템의 메모리에 대한 스냅샷을 만듭니다.

    - Azure 서비스의 스냅샷 기능 또는 소프트웨어의 자체 기능을 사용하여 실행 중인 시스템의 스냅샷을 만듭니다.

Azure Sentinel은 거의 모든 로그 원본 및 사례 관리 포털에서 광범위한 데이터 분석을 제공하여 인시던트의 전체 수명 주기를 관리합니다. 조사 중에 인텔리전스 정보는 추적 및 보고를 위해 인시던트에 연결할 수 있습니다. 

- [Windows 컴퓨터의 디스크 스냅샷 만들기](../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [Linux 컴퓨터의 디스크 스냅샷 만들기](../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Microsoft Azure 지원 진단 정보 및 메모리 덤프 수집](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [Azure Sentinel을 사용하여 인시던트 조사](../sentinel/tutorial-investigate-cases.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: 탐지 및 분석 – 인시던트 우선 순위 지정

**지침**: 경고 심각도 및 자산 민감도에 따라 먼저 집중해야 하는 인시던트에 대한 컨텍스트를 분석가에게 제공합니다. 

Azure Security Center는 먼저 조사해야 하는 경고의 우선 순위를 지정하는 데 도움이 되도록 심각도를 각 경고에 할당합니다. 심각도는 Security Center가 결과에 얼마나 확신하는지 또는 경고를 발행하는 데 분석적으로 사용되었는지, 그리고 경고를 유발한 활동 배경에 악의적인 의도가 있었다는 신뢰 수준을 기반으로 합니다.

또한 태그를 사용하여 리소스를 표시하고, Azure 리소스, 특히 중요한 데이터를 처리하는 리소스를 식별하고 분류할 수 있는 명명 시스템을 만듭니다.  인시던트가 발생한 Azure 리소스 및 환경의 중요도에 따라 경고의 수정에 대한 우선 순위를 지정해야 합니다.

- [Azure Security Center의 보안 경고](../security-center/security-center-alerts-overview.md)

- [태그를 사용하여 Azure 리소스 구성](../azure-resource-manager/management/tag-resources.md).

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: 차단, 제거, 복구 - 인시던트 처리 자동화

**지침**: 수동 반복 작업을 자동화하여 응답 시간을 단축하고 분석가의 부담을 줄입니다. 수동 작업은 실행하는 데 더 오래 걸려 각 인시던트의 속도를 늦추고 분석가가 처리할 수 있는 인시던트 수를 줄입니다. 또한 수동 작업은 분석가를 지치게 하여 지연을 유발하는 인간 오류의 위험을 높이고, 복잡한 작업에 효과적으로 집중할 수 있는 분석가의 능력을 저하시킵니다. Azure Security Center 및 Azure Sentinel의 워크플로 자동화 기능을 사용하여 작업을 자동으로 트리거하거나 플레이북을 실행하여 들어오는 보안 경고에 대응합니다. 플레이북은 알림 보내기, 계정 사용 안 함 및 문제가 있는 네트워크 격리와 같은 작업을 수행합니다. 

- [Security Center에서 워크플로 자동화 구성](../security-center/workflow-automation.md)

- [Azure Security Center에서 자동화된 위협 대응 설정](../security-center/tutorial-security-incident.md#triage-security-alerts)

- [Azure Sentinel에서 자동화된 위협 응답 설정](../sentinel/tutorial-respond-threats-playbook.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

## <a name="posture-and-vulnerability-management"></a>태세 및 취약성 관리

*자세한 내용은 [Azure Security Benchmark: 보안 상태 및 취약성 관리](../security/benchmarks/security-controls-v2-posture-vulnerability-management.md)를 참조하세요.*

### <a name="pv-3-establish-secure-configurations-for-compute-resources"></a>PV-3: 컴퓨팅 리소스에 대한 보안 구성 설정

**지침**: Azure Security Center 및 Azure Policy를 사용하여 VM, 컨테이너 등을 비롯한 모든 컴퓨팅 리소스에 대한 보안 구성을 설정합니다.

- [Azure Security Center 권장 사항을 모니터링하는 방법](../security-center/security-center-recommendations.md) 

- [보안 권장 사항 - 참조 가이드](../security-center/recommendations-reference.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: 정기적인 공격 시뮬레이션 수행

**지침**: 필요에 따라 Azure 리소스에 대한 침투 테스트 또는 레드 팀 활동을 수행하고 모든 중요한 보안 결과를 수정해야 합니다.
Microsoft Cloud 침투 테스트 시행 규칙에 따라 침투 테스트가 Microsoft 정책을 위반하지 않는지 확인합니다. Microsoft의 전략과 Microsoft에서 관리하는 클라우드 인프라, 서비스, 애플리케이션에 대한 레드 팀 실행 및 실시간 사이트 침투 테스트를 사용합니다.

- [Azure의 침투 테스트](../security/fundamentals/pen-testing.md)

- [침투 테스트 시행 규칙](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud 레드 팀](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center 모니터링**: 해당 없음

**책임**: 공유됨

## <a name="backup-and-recovery"></a>백업 및 복구

자세한 내용은 [Azure Security Benchmark: 백업 및 복구](../security/benchmarks/security-controls-v2-backup-recovery.md)를 참조하세요.

### <a name="br-1-ensure-regular-automated-backups"></a>BR-1: 자동화된 정기 백업 보장

**지침**: Azure HPC Cache는 스토리지 시스템이 아닌 캐싱 솔루션이기 때문에 스토리지 대상의 데이터를 정기적으로 백업하는 데 중점을 둡니다. Azure Blob 컨테이너 및 온-프레미스 스토리지 대상 백업에 대한 표준 절차를 따릅니다. 

지역 가동 중단 발생 시 중단을 최소화하기 위해 지역 간 데이터 액세스를 보장하는 조치를 취할 수 있습니다.  

각 Azure HPC Cache 인스턴스는 특정 구독 내 한 지역에서 실행됩니다. 즉, 지역 전체 가동이 중단되면 캐시 워크플로가 중단될 수 있습니다. 이러한 중단을 최소화하려면 조직의 여러 지역에서 액세스할 수 있는 백 엔드 스토리지를 사용해야 합니다. 이런 스토리지는 적절한 DNS를 지원하는 온-프레미스 NAS 시스템이거나 캐시와 다른 지역에 상주하는 Azure Blob 스토리지일 수 있습니다.

주 지역에서 워크플로가 진행되는 동안 데이터는 지역 외부의 장기 스토리지에 저장됩니다. 캐시 지역을 사용할 수 없게 되면 중복된 Azure HPC Cache 인스턴스를 보조 지역에 만들고 동일한 스토리지에 연결하여 새 캐시에서 작업을 재개할 수 있습니다.

- [지역 장애 조치(failover)에 대해 자세히 알아보기](hpc-region-recovery.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="br-2-encrypt-backup-data"></a>BR-2: 백업 데이터 암호화

**지침**: 백업은 공격으로부터 보호되어야 합니다. 여기에는 기밀성 손실을 방지하기 위한 백업 암호화가 포함됩니다.

Azure Backup을 사용하는 온-프레미스 백업의 경우 사용자가 제공한 암호를 사용하여 미사용 암호화가 제공됩니다. 정기 Azure 서비스 백업의 경우 백업 데이터는 Azure 플랫폼 관리형 키를 사용하여 자동으로 암호화됩니다. 고객 관리형 키를 사용하여 백업을 암호화하도록 선택할 수 있습니다. 이런 경우 키 자격 증명 모음에서 이 고객 관리형 키도 백업 범위에 있는지 확인합니다.

또한 Azure HPC Cache는 캐시 디스크에 대한 고객 키를 추가하는 경우에도 캐시된 데이터를 보관하는 관리 디스크에서 VM 호스트 암호화를 통해 보호됩니다. 이중 암호화를 위해 고객 관리형 키를 추가하면 보안 요구 사항이 높은 고객에게 추가 보안 수준이 제공됩니다. 자세한 내용은 Azure Disk Storage의 서버 쪽 암호화를 참조하세요.

Azure Backup, Azure Key Vault 또는 기타 리소스에서 역할 기반 액세스 제어를 사용하여 백업 및 고객 관리형 키를 보호합니다. 또한 고급 보안 기능을 사용하도록 설정하여 백업을 변경하거나 삭제하기 전에 MFA를 요구할 수 있습니다.

- [VM 호스트 암호화](../virtual-machines/disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data)

- [Azure Disk Storage의 서버 쪽 암호화](../virtual-machines/disk-encryption.md)

- [Azure Backup의 보안 기능 개요](../backup/security-overview.md) 

- [고객 관리형 키를 사용하여 백업 데이터 암호화](../backup/encryption-at-rest-with-cmk.md)  

- [Azure에서 Key Vault 키를 백업하는 방법](/powershell/module/az.keyvault/backup-azkeyvaultkey?preserve-view=true&view=azps-5.1.0)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="br-3-validate-all-backups-including-customer-managed-keys"></a>BR-3: 고객 관리형 키를 비롯한 모든 백업 유효성 검사

**지침**: 백업된 고객 관리형 키를 복원할 수 있는지 정기적으로 확인합니다.

- [Azure에서 Key Vault 키를 복원하는 방법](/powershell/module/az.keyvault/restore-azkeyvaultkey?preserve-view=true&view=azps-5.1.0)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="br-4-mitigate-risk-of-lost-keys"></a>BR-4: 분실한 키의 위험 완화

**지침**: 키 손실을 방지하고 복구할 수 있는 방안이 준비되어 있는지 확인합니다. Azure Key Vault에서 일시 삭제 및 제거 보호를 사용하도록 설정하여 실수로 또는 악의적으로 삭제되지 않도록 키를 보호합니다.

- [Key Vault에서 일시 삭제 및 제거 보호를 사용하도록 설정하는 방법](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="governance-and-strategy"></a>거버넌스 및 전략

자세한 내용은 [Azure Security Benchmark: 거버넌스 및 전략](../security/benchmarks/security-controls-v2-governance-strategy.md)을 참조하세요.

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1: 자산 관리 및 데이터 보호 전략 정의 

**지침**: 시스템 및 데이터를 지속적으로 모니터링하고 보호하기 위한 명확한 전략을 문서화하고 전달해야 합니다. 중요 비즈니스용 데이터 및 시스템의 검색, 평가, 보호, 모니터링에 우선 순위를 지정합니다. 

이 전략에는 다음 요소에 대한 문서화된 지침, 정책 및 표준이 포함되어야 합니다. 

-   비즈니스 위험에 따른 데이터 분류 표준

-   위험 및 자산 인벤토리에 대한 보안 조직의 가시성 

-   사용할 Azure 서비스에 대한 보안 조직의 승인 

-   수명 주기 전체에서 자산 보안

-   조직 데이터 분류에 따라 필요한 액세스 제어 전략

-   Azure 기본 및 타사 데이터 보호 기능 사용

-   전송 중 및 미사용 사용 사례에 대한 데이터 암호화 요구 사항

-   적절한 암호화 표준

자세한 내용은 다음 참조 문서를 참조하세요.
- [Azure 보안 아키텍처 권장 사항 - 스토리지, 데이터, 암호화](/azure/architecture/framework/security/storage-data-encryption?bc=%2fsecurity%2fcompass%2fbreadcrumb%2ftoc.json&toc=%2fsecurity%2fcompass%2ftoc.json)

- [Azure 보안 기본 사항 - Azure 데이터 보안, 암호화, 스토리지](../security/fundamentals/encryption-overview.md)

- [클라우드 채택 프레임워크 - Azure 데이터 보안 및 암호화 모범 사례](../security/fundamentals/data-encryption-best-practices.md?bc=%2fazure%2fcloud-adoption-framework%2f_bread%2ftoc.json&toc=%2fazure%2fcloud-adoption-framework%2ftoc.json)

- [Azure Security Benchmark - 자산 관리](../security/benchmarks/security-controls-v2-asset-management.md)

- [Azure Security Benchmark - 데이터 보호](../security/benchmarks/security-controls-v2-data-protection.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2: 엔터프라이즈 구분 전략 정의 

**지침**: ID, 네트워크, 애플리케이션, 구독, 관리 그룹 및 기타 컨트롤의 조합을 사용하여 자산에 대한 액세스를 구분하는 엔터프라이즈 전체 전략을 수립합니다.

서로 통신하고 데이터에 액세스해야 하는 시스템의 일상 작업을 사용하도록 설정해야 할 필요성과 보안 분리의 필요성을 신중하게 조정해야 합니다.

구분 전략이 네트워크 보안, ID 및 액세스 모델, 애플리케이션 권한/액세스 모델 및 사용자 프로세스 컨트롤을 비롯하여 컨트롤 형식 간에 일관되게 구현되는지 확인합니다.

- [Azure의 구분 전략에 대한 지침(동영상)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Azure의 구분 전략에 대한 지침(문서)](/security/compass/governance#enterprise-segmentation-strategy)

- [엔터프라이즈 구분 전략에 맞춰 네트워크 구분 조정](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3: 보안 태세 관리 전략 정의

**지침**: 개별 자산과 해당 자산이 호스트되는 환경에 대한 위험을 지속적으로 측정하고 완화합니다. 게시된 애플리케이션, 네트워크 수신 및 송신 지점, 사용자 및 관리자 엔드포인트 등과 같은 고가치 자산과 노출이 많은 공격 노출 영역에 우선 순위를 지정합니다.

- [Azure Security Benchmark - 태세 및 취약성 관리](../security/benchmarks/security-controls-v2-posture-vulnerability-management.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4: 조직 역할, 책임, 의무 조정

**지침**: 보안 조직의 역할 및 책임에 대한 명확한 전략을 문서화하고 전달하는지 확인합니다. 보안 의사 결정에 대한 명확한 책임을 제시하고, 공유 책임 모델을 모두에게 교육하고, 클라우드를 보호하는 기술에 관해 기술 팀을 교육하는 일에 우선 순위를 지정합니다.

- [Azure 보안 모범 사례 1 – 사용자: 클라우드 보안 경험에 대한 팀 교육](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey)

- [Azure 보안 모범 사례 2 – 사용자: 클라우드 보안 기술에 대한 팀 교육](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology)

- [Azure 보안 모범 사례 3 – 프로세스: 클라우드 보안 결정에 대한 책임 할당](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="gs-5-define-network-security-strategy"></a>GS-5: 네트워크 보안 전략 정의

**지침**: 조직의 전반적인 보안 액세스 제어 전략의 일부로 Azure 네트워크 보안 방법을 설정합니다.  

이 전략에는 다음 요소에 대한 문서화된 지침, 정책 및 표준이 포함되어야 합니다. 

-   중앙 집중식 네트워크 관리 및 보안 책임

-   엔터프라이즈 구분 전략에 맞춰 조정된 가상 네트워크 구분 모델

-   다양한 위협 및 공격 시나리오에서 수정 전략

-   인터넷 에지 및 수신/송신 전략

-   하이브리드 클라우드 및 온-프레미스 상호 연결 전략

-   최신 네트워크 보안 아티팩트(예: 네트워크 다이어그램, 참조 네트워크 아키텍처)

자세한 내용은 다음 참조 문서를 참조하세요.
- [Azure 보안 모범 사례 11 – 아키텍처 단일 통합 보안 전략](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Azure Security Benchmark - 네트워크 보안](../security/benchmarks/security-controls-v2-network-security.md)

- [Azure 네트워크 보안 개요](../security/fundamentals/network-overview.md)

- [엔터프라이즈 네트워크 아키텍처 전략](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6: ID 및 권한 있는 액세스 전략 정의

**지침**: 조직의 전반적인 보안 액세스 제어 전략의 일부로 Azure ID 및 권한 있는 액세스 방법을 설정합니다.  

이 전략에는 다음 요소에 대한 문서화된 지침, 정책 및 표준이 포함되어야 합니다. 

-   중앙 집중식 ID 및 인증 시스템과 다른 내부 및 외부 ID 시스템 간의 상호 연결

-   다양한 사용 사례 및 조건에서 강력한 인증 방법

-   권한이 높은 사용자 보호

-   변칙 사용자 활동 모니터링 및 처리  

-   사용자 ID 및 액세스 검토와 조정 프로세스

자세한 내용은 다음 참조 문서를 참조하세요.

- [Azure Security Benchmark - ID 관리](../security/benchmarks/security-controls-v2-identity-management.md)

- [Azure Security Benchmark - 권한 있는 액세스](../security/benchmarks/security-controls-v2-privileged-access.md)

- [Azure 보안 모범 사례 11 – 아키텍처 단일 통합 보안 전략](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Azure ID 관리 보안 개요](../security/fundamentals/identity-management-overview.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: 로깅 및 위협 대응 전략 정의

**지침**: 규정 준수 요구 사항을 충족하면서 신속하게 위협을 탐지하고 수정할 수 있는 로깅 및 위협 대응 전략을 수립합니다. 통합 및 수동 단계가 아닌 위협에 집중할 수 있도록 분석가에게 고품질 경고 및 원활한 환경을 제공하는 것에 우선 순위를 지정합니다. 

이 전략에는 다음 요소에 대한 문서화된 지침, 정책, 표준이 포함되어야 합니다. 

-   보안 운영(SecOps) 조직의 역할 및 책임 

-   NIST 또는 다른 업계 프레임워크를 사용하여 잘 정의된 인시던트 응답 프로세스 

-   위협 탐지, 인시던트 응답 및 규정 준수 요구 사항을 지원하는 로그 캡처 및 보존

-   SIEM, 네이티브 Azure 기능, 기타 소스를 사용하여 위협에 대한 중앙 집중식 가시성 및 상관 관계 정보 

-   고객, 공급자 및 공공 당사자와의 통신 및 알림 계획

-   로깅 및 위협 탐지, 포렌식, 공격 수정, 제거와 같은 인시던트 처리를 위해 Azure 네이티브 및 타사 플랫폼 사용

-   확인한 상황 및 증거 보존을 포함하여 인시던트 및 인시던트 후 활동을 처리하는 프로세스

자세한 내용은 다음 참조 문서를 참조하세요.

- [Azure Security Benchmark - 로깅 및 위협 탐지](../security/benchmarks/security-controls-v2-logging-threat-detection.md)

- [Azure Security Benchmark - 인시던트 응답](../security/benchmarks/security-controls-v2-incident-response.md)

- [Azure 보안 모범 사례 4 - 프로세스 클라우드에 대한 인시던트 응답 프로세스 업데이트](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Azure 채택 프레임워크, 로깅, 보고 의사 결정 가이드](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Azure 엔터프라이즈 규모, 관리, 모니터링](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="next-steps"></a>다음 단계

- [Azure Security Benchmark V2 개요](../security/benchmarks/overview.md)를 참조하세요.
- [Azure 보안 기준](../security/benchmarks/security-baselines-overview.md)에 대해 자세히 알아보세요.