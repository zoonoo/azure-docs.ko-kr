---
title: 가상 WAN에 대 한 Azure 보안 기준
description: 가상 WAN 보안 기준은 Azure 보안 벤치 마크에 지정 된 보안 권장 사항을 구현 하기 위한 절차 지침과 리소스를 제공 합니다.
author: msmbaldwin
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 11/03/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 409c8d163b52e56a1eef68a23e4db79956a68722
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93328695"
---
# <a name="azure-security-baseline-for-virtual-wan"></a>가상 WAN에 대 한 Azure 보안 기준

이 보안 기준은 [Azure Security 벤치 마크 버전 2.0](../security/benchmarks/overview.md) 의 지침을 MICROSOFT AZURE 가상 WAN에 적용 합니다. Azure Security Benchmark는 Azure에서 클라우드 솔루션을 보호하는 방법에 대한 권장 사항을 제공합니다. 콘텐츠는 Azure 보안 벤치 마크에 정의 된 **보안 컨트롤** 및 가상 WAN에 적용 되는 관련 지침에 따라 그룹화 됩니다. 가상 WAN에 적용할 수 없는 **컨트롤이** 제외 되었습니다.

가상 WAN이 Azure 보안 벤치 마크에 완전히 매핑되는 방식을 확인 하려면 [전체 가상 wan 보안 기준 매핑 파일](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)을 참조 하세요.

## <a name="network-security"></a>네트워크 보안

*자세한 내용은 [Azure 보안 벤치 마크: 네트워크 보안](/azure/security/benchmarks/security-controls-v2-network-security)을 참조 하세요.*

### <a name="ns-1-implement-security-for-internal-traffic"></a>NS-1: 내부 트래픽에 대 한 보안 구현

**지침** : AZURE 가상 WAN은 가상 네트워크에 직접 배포 하는 것을 지원 하지 않습니다. 그러나 계속 해 서 스포크 리소스에 네트워크 보안 그룹 규칙을 적용 하거나, Azure 방화벽의 보호를 사용 하거나, 사용자 지정 경로 테이블을 만들어 개인 트래픽을 방지 하거나 허용할 수 있습니다.

- [사용자 지정 라우팅 시나리오](scenario-any-to-any.md)

**Azure Security Center 모니터링** : 현재 사용할 수 없음

**책임** : Customer

### <a name="ns-2-connect-private-networks-together"></a>NS-2: 개인 네트워크를 함께 연결 

**지침** : azure express 경로 또는 azure VPN (가상 사설망)을 사용 하 여 공동 배치 환경의 온-프레미스 인프라와 azure 데이터 센터 간에 개인 연결을 만듭니다. Express 경로 연결은 공용 인터넷을 통해 이동 하지 않으며 일반적인 인터넷 연결 보다 안정성, 속도가 빠르며 대기 시간이 짧습니다. 

지점 및 사이트 간 VPN 및 사이트 간 VPN의 경우 이러한 VPN 옵션과 Express 경로를 조합 하 여 가상 네트워크에 온-프레미스 장치 또는 네트워크를 연결 합니다. Azure에서 둘 이상의 가상 네트워크를 함께 연결 하려면 Virtual Network 피어 링을 사용 합니다. 피어 링 가상 네트워크 간의 네트워크 트래픽은 비공개 이며 Azure 백본 네트워크에 유지 됩니다.

- [가상 WAN의 Express 경로](virtual-wan-expressroute-portal.md)

- [사이트 간 VPN 개요](virtual-wan-site-to-site-portal.md)

- [지점 및 사이트 간 VPN 개요](virtual-wan-point-to-site-portal.md)

- [Private Link](howto-private-link.md)

**Azure Security Center 모니터링** : 현재 사용할 수 없음

**책임** : 공유됨

### <a name="ns-3-establish-private-network-access-to-azure-services"></a>NS-3: Azure 서비스에 대 한 개인 네트워크 액세스 설정

**지침** : Azure 개인 링크를 사용 하 여 인터넷을 거치지 않고 가상 네트워크에서 AZURE 가상 WAN에 대 한 개인 액세스를 사용 하도록 설정 합니다. 개인 액세스는 Azure 서비스에서 제공 하는 인증 및 트래픽 보안 외에도 또 다른 심층 방어 수단입니다.

- [Azure 개인 링크 이해](../private-link/private-link-overview.md)

- [가상 Wan 개인 링크](howto-private-link.md)

**Azure Security Center 모니터링** : 현재 사용할 수 없음

**책임** : Customer

### <a name="ns-4-protect-applications-and-services-from-external-network-attacks"></a>NS-4: 외부 네트워크 공격 으로부터 응용 프로그램 및 서비스 보호

**지침** : DDoS (배포 된 서비스 거부) 공격, 응용 프로그램 관련 공격, 원치 않는 악의적인 인터넷 트래픽 등 외부 네트워크의 공격 으로부터 AZURE 가상 WAN 리소스를 보호 합니다. 

Azure 방화벽을 사용 하 여 인터넷 및 기타 외부 위치에서 잠재적으로 악성 트래픽에 대 한 응용 프로그램 및 서비스를 보호 합니다. Azure 가상 네트워크의 공격에 대 한 자산에 대 한 Azure DDoS Protection를 선택 합니다. Azure Security Center를 사용 하 여 네트워크 관련 리소스와 관련 된 잘못 된 구성 위험을 검색 합니다.

- [Azure 방화벽 설명서](/azure/firewall)

- [Azure Portal을 사용하여 Azure DDoS Protection 표준 관리](/azure/virtual-network/manage-ddos-protection) 

- [Azure Security Center 권장 사항](../security-center/recommendations-reference.md#recs-network)

**Azure Security Center 모니터링** : 예

**책임** : 공유됨

### <a name="ns-5-deploy-intrusion-detectionintrusion-prevention-systems-idsips"></a>NS-5: 침입 감지/침입 방지 시스템 (IDS/IPS) 배포

**지침** : AZURE 가상 WAN에서 침입 감지 또는 방지 시스템을 배포 하는 가장 좋은 방법은 가상 허브에 연결 된 스포크 네트워크에서 네트워크 가상 어플라이언스를 사용 하는 것입니다.  참조 된 링크의 라우팅 시나리오에서 자세한 정보를 확인할 수 있습니다. 

- [시나리오 - NVA를 통한 트래픽 라우팅](scenario-route-through-nva.md)

**Azure Security Center 모니터링** : 현재 사용할 수 없음

**책임** : Customer

### <a name="ns-6-simplify-network-security-rules"></a>NS-6: 네트워크 보안 규칙 단순화

**지침** : azure Virtual Network 서비스 태그를 사용 하 여 가상 WAN 리소스에 대해 구성 된 azure 네트워크 보안 그룹 또는 azure 방화벽에서 네트워크 액세스 제어를 정의 합니다. 

보안 규칙을 만들 때 특정 IP 주소 대신 서비스 태그를 사용 합니다. 규칙의 적절 한 원본 또는 대상 필드에서 서비스 태그 이름 (예: {VirtualWAN: Virtual Network})을 지정 하 여 해당 서비스에 대 한 트래픽을 허용 하거나 거부할 수 있습니다. Microsoft는 서비스 태그에 포함되는 주소 접두사를 관리하고 주소가 변경되면 서비스 태그를 자동으로 업데이트합니다.

**Azure Security Center 모니터링** : 현재 사용할 수 없음

**책임** : Customer

### <a name="ns-7-secure-domain-name-service-dns"></a>NS-7: 보안 DNS (Domain Name Service)

**지침** : AZURE 가상 WAN은 지점 및 사이트 간 VPN 게이트웨이에 대 한 사용자 지정 DNS 서버를 제공 합니다. 

DNS 보안에 대 한 모범 사례를 수행 하 여 현 수 DNS, DNS amplifications 공격, DNS 손상 및 스푸핑 등의 일반적인 공격을 완화 합니다.

Azure DNS를 권한 있는 DNS 서비스로 사용 하는 경우 azure RBAC (역할 기반 액세스 제어) 및 리소스 잠금을 사용 하 여 DNS 영역 및 레코드가 실수로 또는 악의적으로 수정 되지 않도록 보호 해야 합니다.

- [Azure DNS 개요](../dns/dns-overview.md) 

- [보안 DNS (Domain Name System) 배포 가이드](https://csrc.nist.gov/publications/detail/sp/800-81/2/final) 

- [현 수 DNS 항목을 방지 하 고 하위 도메인 인수 방지](../security/fundamentals/subdomain-takeover.md)

**Azure Security Center 모니터링** : 현재 사용할 수 없음

**책임** : Customer

## <a name="identity-management"></a>ID 관리

*자세한 내용은 [Azure 보안 벤치 마크: Identity Management](/azure/security/benchmarks/security-controls-v2-identity-management)를 참조 하세요.*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: 중앙 id 및 인증 시스템으로 Azure Active Directory 표준화

**지침** : AZURE 가상 WAN의 지점 및 사이트 간 VPN은 azure의 기본 id 및 액세스 관리 서비스인 Azure Active Directory (azure AD)와 통합 됩니다. 에서 조직의 id 및 액세스 관리를 관리 하려면 Azure AD를 표준화 해야 합니다.

- Azure Portal, Azure Storage, Azure Virtual Machines (Linux 및 Windows), Azure Key Vault, PaaS, SaaS 응용 프로그램 등의 리소스를 Microsoft 클라우드 합니다.
- Azure의 응용 프로그램 또는 회사 네트워크 리소스와 같은 조직의 리소스

조직의 클라우드 보안 관행에서 우선 순위가 높은 Azure AD를 보호 합니다. Azure Security Center의 보안 점수 기능을 통해 id 및 보안 상태를 평가 하 여 구성이 모범 사례 권장 사항에 얼마나 근접 하 게 일치 하는지 측정 합니다. 보안 상태를 개선 하기 위해 Microsoft의 모범 사례 권장 사항을 구현 합니다.

또한 Azure AD는 외부 id를 지원 합니다 .이 id를 사용 하면 Microsoft 계정 없는 사용자가 외부 id를 사용 하 여 응용 프로그램 및 리소스에 로그인 할 수 있습니다. 참조 된 링크에서 지점 및 사이트 간 VPN 시나리오에 Azure AD를 사용 하는 방법에 대 한 정보를 검토 합니다.

- [P2S OpenVPN 프로토콜 연결에 대 한 AD (Azure Active Directory) 테 넌 트 만들기](openvpn-azure-ad-tenant-multi-app.md)

- [사용자 VPN에 대 한 Azure Active Directory 인증 구성](virtual-wan-point-to-site-azure-ad.md)

- [Azure Active Directory의 테넌시](../active-directory/develop/single-and-multi-tenant-apps.md) 

- [Azure AD 인스턴스를 만들고 구성 하는 방법](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) 

- [응용 프로그램에 외부 id 공급자 사용](/azure/active-directory/b2b/identity-providers)

**Azure Security Center 모니터링** : 예

**책임** : 공유됨

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: 모든 Azure Active Directory 기반 액세스에 대해 강력한 인증 제어 사용

**지침** : azure 가상 WAN의 지점 및 사이트 간 VPN은 다단계 인증을 사용 하는 강력한 인증 제어를 지 원하는 Azure Active Directory (azure AD)와 통합 되며 강력한 암호 없는 메서드를 제공 합니다.

- 다단계 인증-Azure AD 다단계 인증을 사용 하도록 설정 하 고 다단계 인증 설정에 대 한 모범 사례에 대 한 Azure Security Center Id 및 액세스 관리 권장 사항을 따릅니다. Multi-factor authentication을 모든 사용자에 게 적용 하거나, 로그인 조건 및 위험 요소에 따라 사용자 또는 사용자 단위 수준에서 선택할 수 있습니다.

- 암호 없는 인증-세 개의 암호 없는 인증 옵션을 사용할 수 있습니다. 여기에는 비즈니스용 Windows Hello, Microsoft Authenticator 앱 및 스마트 카드와 같은 온-프레미스 인증 방법이 포함 됩니다.

관리자 및 권한 있는 사용자의 경우 가장 높은 수준의 강력한 인증 방법을 사용 하 고 그 다음에 적절 한 강력한 인증 정책에서 다른 사용자에 게 롤아웃 합니다.

- [가상 WAN에 대해 P2S VPN에서 MFA를 사용 하도록 설정 하는 방법](openvpn-azure-ad-mfa.md)

**Azure Security Center 모니터링** : 예

**책임** : 공유됨

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: 조건에 따라 Azure 리소스 액세스 제한

**지침** : AZURE 가상 WAN의 지점 및 사이트 간 VPN은 사용자 정의 조건에 따라 보다 세분화 된 액세스 제어를 위해 azure AD 조건부 액세스를 지원 합니다. 예를 들어 특정 IP 범위의 사용자 로그인은 로그인에 다단계 인증을 사용 해야 합니다. 여러 사용 사례에 대해 세부적인 인증 세션 관리 정책을 사용할 수도 있습니다.

- [일반적인 조건부 액세스 정책](../active-directory/conditional-access/concept-conditional-access-policy-common.md) 

- [조건부 액세스를 사용 하 여 인증 세션 관리 구성](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md)

- [가상 Wan P2S VPN 조건부 액세스](openvpn-azure-ad-mfa.md#conditional)

**Azure Security Center 모니터링** : 현재 사용할 수 없음

**책임** : Customer

### <a name="im-7-eliminate-unintended-credential-exposure"></a>IM-7: 의도 하지 않은 자격 증명 노출 제거

**지침** : 자격 증명 스캐너를 구현하여 코드 내에서 자격 증명을 식별합니다. 또한 자격 증명 스캐너는 검색된 자격 증명을 더 안전한 위치(예: Azure Key Vault)로 이동하도록 추천합니다.

GitHub의 경우 네이티브 암호 검색 기능을 사용 하 여 코드 내에서 자격 증명 또는 다른 형식의 암호를 식별할 수 있습니다.

- [자격 증명 스캐너를 설정하는 방법](https://secdevtools.azurewebsites.net/helpcredscan.html) 

- [GitHub 암호 검색](https://docs.github.com/github/administering-a-repository/about-secret-scanning)

**Azure Security Center 모니터링** : 현재 사용할 수 없음

**책임** : Customer

## <a name="privileged-access"></a>권한 있는 액세스

*자세한 내용은 [Azure 보안 벤치 마크: 권한 있는 액세스](/azure/security/benchmarks/security-controls-v2-privileged-access)를 참조 하세요.*

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: 업무상 중요 한 시스템에 대 한 관리 액세스 제한

**지침** : AZURE 가상 WAN은 azure RBAC (역할 기반 액세스 제어)를 사용 하 여 구독 및 관리 그룹에 대 한 권한 있는 액세스 권한이 부여 된 계정을 제한 함으로써 업무상 중요 한 시스템에 대 한 액세스를 격리 합니다.

또한 비즈니스에 중요 한 시스템에 설치 된 에이전트를 사용 하 여 Active Directory 도메인 컨트롤러, 보안 도구 및 시스템 관리 도구와 같이 업무상 중요 한 액세스에 대 한 관리 권한이 있는 관리, id 및 보안 시스템에 대 한 액세스를 제한 합니다. 이러한 관리 및 보안 시스템을 손상 시키는 공격자는 비즈니스에 중요 한 자산을 손상 시키기 위해 즉시 weaponize 수 있습니다.

모든 종류의 액세스 제어는 일관 된 액세스 제어를 보장 하기 위해 엔터프라이즈 조각화 전략에 맞춰야 합니다.

- [Azure 구성 요소 및 참조 모델](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151) 

- [관리 그룹 액세스](../governance/management-groups/overview.md#management-group-access) 

- [Azure 구독 관리자](../cost-management-billing/manage/add-change-subscription-administrator.md)

**Azure Security Center 모니터링** : 현재 사용할 수 없음

**책임** : Customer

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6: 권한 있는 액세스 워크스테이션 사용

**지침** : 보안이 유지 되 고 격리 된 워크스테이션은 관리자, 개발자 및 중요 서비스 운영자와 같은 중요 한 역할의 보안에 매우 중요 합니다. 관리 작업에는 매우 안전한 사용자 워크스테이션 또는 Azure 방호를 사용 합니다. Azure Active Directory (Azure AD), Microsoft Defender ATP (Advanced Threat Protection) 또는 Microsoft Intune를 사용 하 여 관리 작업을 위한 안전 하 고 관리 되는 사용자 워크스테이션을 배포할 수 있습니다. 보안 워크스테이션은 강력한 인증, 소프트웨어 및 하드웨어 기준, 제한 된 논리적 및 네트워크 액세스를 포함 하 여 보안 구성을 적용 하도록 중앙에서 관리할 수 있습니다.

- [권한 있는 액세스 워크스테이션 이해](../active-directory/devices/concept-azure-managed-workstation.md)

- [권한 있는 액세스 워크스테이션 배포](../active-directory/devices/howto-azure-managed-workstation.md)

**Azure Security Center 모니터링** : 현재 사용할 수 없음

**책임** : Customer

## <a name="data-protection"></a>데이터 보호

*자세한 내용은 [Azure 보안 벤치 마크: 데이터 보호](/azure/security/benchmarks/security-controls-v2-data-protection)를 참조 하세요.*

### <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4: 전송 중인 중요 한 정보 암호화

**지침** : 암호화는 외부 및 공용 네트워크의 트래픽에 매우 중요 합니다.

- 액세스 제어를 사용 하 여 공격자가 데이터를 쉽게 읽거나 수정할 수 없도록 암호화를 사용 하 여 ' 대역 외 ' 공격 (예: 트래픽 캡처)에 대해 전송 데이터를 보호 해야 합니다.

- Azure 리소스에 연결 하는 모든 클라이언트가 TLS v 1.2 이상을 협상할 수 있도록 HTTP 트래픽을 확인 합니다.

- 원격 관리의 경우 암호화 되지 않은 프로토콜 대신 SSH (Linux) 또는 RDP/TLS (Windows 용)를 사용 합니다. 사용 되지 않는 SSL/TLS/SSH 버전, 프로토콜 및 약한 암호를 사용 하지 않도록 설정 해야 합니다.-

- 기본 인프라에서 Azure는 Azure 데이터 센터 간의 데이터 트래픽에 대해 기본적으로 전송 암호화에 데이터를 제공 합니다.

일반적으로 Azure Express 경로 및 지점 및 사이트 간 사용자 VPN을 통해 사이트 간 VPN, 사이트 간 VPN에서 트래픽을 암호화할 수 있는 보안 Microsoft 백본 및 기회에 대 한 암호화를 제공 합니다.

- [Azure를 사용 하 여 전송 중인 암호화 이해](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

- [TLS 보안에 대 한 정보](/security/engineering/solving-tls1-problem)

- [전송 중인 Azure 데이터에 대 한 이중 암호화](../security/fundamentals/double-encryption.md#data-in-transit)

**Azure Security Center 모니터링** : 예

**책임** : 공유됨

## <a name="asset-management"></a>자산 관리

*자세한 내용은 [Azure 보안 벤치 마크: 자산 관리](/azure/security/benchmarks/security-controls-v2-asset-management)를 참조 하세요.*

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>오전-2: 보안 팀에 asset inventory and metadata에 대 한 액세스 권한이 있는지 확인

**지침** : Azure 리소스, 리소스 그룹 및 구독에 태그를 적용 하 여 논리적으로 분류로 구성 합니다. 각 태그는 이름과 값 쌍으로 이루어져 있습니다. 예를 들어 프로덕션의 모든 리소스에 "환경" 이름과 "프로덕션" 값을 적용할 수 있습니다.

Azure 가상 WAN은 Azure Resource Manager 기반 리소스 배포 및 Azure 리소스 그래프 쿼리도 지원 합니다. 

- [자산에 태그를 지정 하는 방법에 대 한 자세한 내용은 리소스 명명 및 태그 지정 결정 가이드를 참조 하세요.](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json)

- [Azure Resource Graph Explorer를 사용하여 쿼리를 만드는 방법](../governance/resource-graph/first-query-portal.md) 

- [Azure Security Center asset inventory management](../security-center/asset-inventory.md)

**Azure Security Center 모니터링** : 예

**책임** : Customer

### <a name="am-3-use-only-approved-azure-services"></a>오전 3: 승인 된 Azure 서비스만 사용

**지침** : Azure Policy을 사용 하 여 사용자 환경에서 프로 비전 할 수 있는 서비스를 제한 합니다. 구독 내에서 Azure 리소스 그래프를 사용 하 여 리소스를 쿼리하고 검색 하 고 Azure Monitor를 사용 하 여 승인 되지 않은 서비스가 검색 될 때 경고를 트리거하는 규칙을 만듭니다.

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy를 사용 하 여 특정 리소스 종류를 거부 하는 방법](../governance/policy/samples/built-in-policies.md#general)

- [Azure Resource Graph Explorer를 사용하여 쿼리를 만드는 방법](../governance/resource-graph/first-query-portal.md)

**Azure Security Center 모니터링** : 현재 사용할 수 없음

**책임** : Customer

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>오전 5: 사용자가 Azure Resource Manager 상호 작용할 수 있도록 제한

**지침** : Azure 조건부 액세스를 사용 하 여 사용자가 Azure 리소스 관리자와 상호 작용 하는 기능을 제한 하는 "Microsoft Azure 관리" 앱에 대 한 "액세스 차단"을 구성 합니다.

- [Azure Resource Manager에 대한 액세스를 차단하도록 조건부 액세스를 구성하는 방법](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center 모니터링** : 현재 사용할 수 없음

**책임** : Customer

## <a name="logging-and-threat-detection"></a>로깅 및 위협 검색

*자세한 내용은 [Azure 보안 벤치 마크: 로깅 및 위협 감지](/azure/security/benchmarks/security-controls-v2-logging-threat-protection)를 참조 하세요.*

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: Azure id 및 액세스 관리에 대 한 위협 감지 사용

**지침** : Azure Active Directory (azure ad)는 azure ad reporting에서 볼 수 있거나 Azure Monitor, azure 센티널, siem 또는 모니터링 도구와 통합 하 여 보다 정교한 모니터링 및 분석 사용 사례를 위한 다음과 같은 사용자 로그를 제공 합니다. 이러한 항목은 다음과 같습니다.

- 로그인-로그인 보고서는 관리 되는 응용 프로그램 및 사용자 로그인 활동 사용에 대 한 정보를 제공 합니다.
- 감사 로그 - Azure AD 내의 다양한 기능에 의해 수행된 모든 변경 내용에 대한 로그를 통한 추적 기능을 제공합니다. 감사 로그의 예로는 사용자, 앱, 그룹, 역할 및 정책을 추가 하거나 제거 하는 등 Azure AD 내의 모든 리소스에 대 한 변경 내용이 포함 됩니다.
- 위험한 로그인-위험한 로그인은 사용자 계정의 합법적인 소유자가 아닌 사용자가 수행 했을 수 있는 로그인 시도에 대 한 표시기입니다.
- 위험 플래그가 지정된 사용자 - 위험한 사용자는 손상되었을 수 있는 사용자 계정에 대한 표시기입니다.

Azure Security Center를 사용 하 여 구독에 사용 되지 않는 계정을 포함 하 여 실패 한 인증 시도의 과도 한 수와 같은 의심 스러운 특정 활동에 대 한 경고를 만듭니다. 기본 보안 예방 조치 외에도 Security Center의 위협 방지 모듈을 사용 하 여 개별 Azure 계산 리소스 (가상 머신, 컨테이너, app service), 데이터 리소스 (SQL DB 및 저장소) 및 Azure 서비스 계층에서 보다 심층적인 보안 경고를 수집 합니다. 이 기능을 사용 하면 개별 리소스 내에서 계정 비정상을 볼 수 있습니다.

- [Azure Active Directory의 감사 활동 보고서](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [Azure Id 보호 사용](../active-directory/identity-protection/overview-identity-protection.md)

**Azure Security Center 모니터링** : 예

**책임** : 공유됨

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3: Azure 네트워크 작업에 대 한 로깅 사용

**지침** : VPN 패킷 캡처 도구를 사용 하 여 AZURE 가상 WAN 리소스 간에 전달 되는 네트워크 패킷을 기록 합니다. 이를 통해 하이브리드 네트워크와 관련 된 디버깅 프로세스를 지원할 수 있습니다. 가상 WAN에 네트워크 보안 그룹을 배포할 수는 없지만 스포크 가상 네트워크 리소스에는 배포할 수 있습니다.

트래픽 감사를 위해 네트워크 보안 그룹에서 네트워크 보안 그룹 흐름 로그를 사용 하도록 설정 합니다.

Azure 트래픽 분석 기능을 사용 하도록 설정 하 여 저장소 계정에 보관 된 흐름 로그를 처리 한 다음 Log Analytics 작업 영역으로 보냅니다. 트래픽 분석는 Azure 네트워크에 대 한 트래픽 흐름에 대 한 추가 정보를 제공 합니다. 트래픽 분석의 장점 중 일부는 네트워크 활동을 시각화하고, 핫 스폿을 식별하며, 보안 위협을 식별하고, 트래픽 흐름 패턴을 이해하며, 잘못된 네트워크 구성을 파악할 수 있다는 것입니다.

- [네트워크 보안 그룹 흐름 로그를 사용 하도록 설정 하는 방법](../network-watcher/network-watcher-nsg-flow-logging-portal.md) 

- [트래픽 분석 사용 및 사용 방법](../network-watcher/traffic-analytics.md) 

가상 Wan은 사용 하도록 설정 해야 하는 DNS 쿼리 로그를 생성 하거나 처리 하지 않습니다.

**Azure Security Center 모니터링** : 현재 사용할 수 없음

**책임** : Customer

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: Azure 리소스에 대 한 로깅 사용

**지침** : 자동으로 사용 되는 azure 활동 로그에는 읽기 (GET) 작업을 제외 하 고 AZURE 가상 WAN 리소스에 대 한 모든 쓰기 작업 (PUT, POST, DELETE)이 포함 됩니다. 활동 로그를 사용 하 여 문제를 해결 하는 동안 오류를 찾거나 조직의 사용자가 리소스를 수정한 방법을 모니터링할 수 있습니다. 그러나 가상 WAN은 Azure 리소스 로그를 생성 하지 않습니다.

- [Azure Monitor를 사용 하 여 플랫폼 로그 및 메트릭을 수집 하는 방법](../azure-monitor/platform/diagnostic-settings.md) 
- [Azure의 로깅 및 다른 로그 유형 이해](../azure-monitor/platform/platform-logs-overview.md)

**Azure Security Center 모니터링** : 예

**책임** : 공유됨

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT: 보안 로그 관리 및 분석을 중앙 집중화

**지침** : 상관 관계를 설정 하기 위해 저장소 및 분석을 중앙 집중화 합니다. 각 로그 원본에 대해 데이터 소유자, 액세스 지침, 저장소 위치, 데이터를 처리 하 고 액세스 하는 데 사용 되는 도구 및 데이터 보존 요구 사항을 할당 했는지 확인 합니다. Azure 활동 로그를 중앙 로깅 시스템에 통합 하 고 있는지 확인 합니다. 

Azure Monitor를 통해 로그를 수집 하 여 끝점 장치, 네트워크 리소스 및 기타 보안 시스템에 의해 생성 된 보안 데이터를 집계 합니다. Azure Monitor에서 Log Analytics 작업 영역을 사용 하 여 분석을 쿼리하고 수행 하 고 장기 및 보관 저장소에 Azure storage 계정을 사용 합니다. 또한 Azure 센티널 또는 타사 SIEM에 데이터를 사용 하도록 설정 하 고 등록 합니다.

많은 조직에서 자주 사용 되는 "핫" 데이터에 대해 Azure 센티널을 사용 하 고 덜 자주 사용 되는 "콜드" 데이터를 Azure Storage 하는 것을 선택 합니다.

- [Azure Monitor를 사용 하 여 플랫폼 로그 및 메트릭을 수집 하는 방법](../azure-monitor/platform/diagnostic-settings.md) 

- [Azure Sentinel을 온보딩하는 방법](../sentinel/quickstart-onboard.md)

**Azure Security Center 모니터링** : 현재 사용할 수 없음

**책임** : 공유됨

## <a name="incident-response"></a>사고 대응

*자세한 내용은 [Azure 보안 벤치 마크: 인시던트 응답](/azure/security/benchmarks/security-controls-v2-incident-response)을 참조 하세요.*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: 준비 – Azure에 대 한 인시던트 응답 프로세스 업데이트

**지침** : 조직이 보안 인시던트에 대응 하 고, Azure에 대 한 이러한 프로세스를 업데이트 했으며, 준비 상태를 확인 하기 위해 정기적으로 수행 하는 프로세스 인지 확인 합니다. 해당 하는 경우 서비스 제공이 인시던트 응답 프로세스에 포함 되어 있는지 확인 합니다.

- [엔터프라이즈 환경에서 보안 구현](https://aka.ms/AzSec4) 
- [인시던트 대응 참조 가이드](https://aka.ms/IRRG)

**Azure Security Center 모니터링** : 현재 사용할 수 없음

**책임** : Customer

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: 준비-설치 인시던트 알림

**지침** : Azure Security Center에서 보안 인시던트 연락처 정보를 설정 합니다. 이 연락처 정보는 microsoft 보안 대응 센터 (MSRC)가 불법적인 또는 권한이 없는 당사자가 데이터에 액세스 한 것을 발견 한 경우 사용자에 게 연락 하는 데 사용 됩니다. 또한 인시던트 대응 요구 사항에 따라 다양 한 Azure 서비스에서 인시던트 경고 및 알림을 사용자 지정 하는 옵션도 있습니다.

Microsoft 보안 대응 센터 (MSRC)에서 불법적인 또는 권한이 없는 당사자가 데이터에 액세스 한 것을 발견 한 경우 Microsoft에서 사용자에 게 연락 하는 보안 인시던트 연락처 정보를 사용 합니다. 문제가 완전히 해결 되었는지 확인 하기 위해 팩트 이후에 인시던트를 검토 합니다.

- [Azure Security Center 보안 연락처를 설정 하는 방법](../security-center/security-center-provide-security-contact-details.md) 

- [Azure Security Center 보안 연락처를 설정 하는 방법](../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center 모니터링** : 예

**책임** : Customer

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: 검색 및 분석 – 고품질 경고를 기반으로 인시던트 만들기

**지침** : Azure Security Center는 각 경고에 심각도를 할당 하 여 먼저 조사 해야 하는 경고의 우선 순위를 지정할 수 있도록 합니다. 심각도는 경고를 실행 하는 데 사용 되는 찾기 또는 분석에 사용 되는 것과 경고를 발생 시킨 활동의 악의적인 의도를 받은 신뢰 수준에 Security Center 따라 달라 집니다.

또한 구독 (예: 프로덕션, 비프로덕션)을 명확 하 게 표시 하 고 Azure 리소스를 명확 하 게 식별 하 고 범주화 하기 위한 이름 지정 시스템을 만듭니다.

- [Azure Security Center의 보안 경고](../security-center/security-center-alerts-overview.md) 

- [태그를 사용하여 Azure 리소스 구성](../azure-resource-manager/management/tag-resources.md).

**Azure Security Center 모니터링** : 예

**책임** : Customer

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4: 검색 및 분석 – 인시던트 조사

**지침** : 조직에 대한 인시던트 대응 지침을 작성합니다. 정기적으로 시스템의 인시던트 응답 기능을 테스트 하는 연습을 수행 합니다. 약점과 결함을 식별하고 필요에 따라 계획을 수정합니다.

검색에서 사후 검토에 이르는 인시던트 처리/관리 단계뿐만 아니라 담당자의 모든 역할을 정의하는 인시던트 대응 계획이 있는지 확인합니다.

- [Azure Security Center 내에서 워크플로 자동화를 구성하는 방법](../security-center/security-center-planning-and-operations-guide.md) 

- [자체 보안 인시던트 대응 프로세스를 구축하는 방법에 대한 지침](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process) 

- [Microsoft 보안 대응 센터의 인시던트 분석](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process) 

- [NIST의 게시물을 참조하세요. IT 계획 및 기능에 대한 테스트, 학습 및 연습 프로그램에 대한 안내](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Azure Security Center 모니터링** : 현재 사용할 수 없음

**책임** : Customer

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: 검색 및 분석 – 인시던트 우선 순위 지정

**지침** : Azure Security Center는 각 경고에 심각도를 할당 하 여 먼저 조사 해야 하는 경고의 우선 순위를 지정할 수 있도록 합니다. 심각도는 Security Center에서 경고를 실행하는 데 사용된 결과 또는 분석의 신뢰도 및 경고가 발생된 활동의 배후에 악의적인 의도가 있었음에 대한 신뢰 수준을 기준으로 합니다.

또한 구독 (예: 프로덕션, 비프로덕션)을 명확 하 게 표시 하 고 Azure 리소스를 명확 하 게 식별 하 고 범주화 하는 명명 시스템을 만듭니다.

- [Azure Security Center의 보안 경고](../security-center/security-center-alerts-overview.md) 

- [태그를 사용하여 Azure 리소스 구성](../azure-resource-manager/management/tag-resources.md).

**Azure Security Center 모니터링** : 예

**책임** : 공유됨

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: 포함, eradication 및 복구-인시던트 처리를 자동화 합니다.

**지침** : Azure Security Center의 워크플로 자동화 기능을 사용 하 여 보안 경고 및 권장 사항에 대 한 "Azure Logic Apps"를 통해 응답을 자동으로 트리거합니다.

- [워크플로 자동화 및 Logic Apps를 구성하는 방법](../security-center/workflow-automation.md)

**Azure Security Center 모니터링** : 예

**책임** : Customer

## <a name="posture-and-vulnerability-management"></a>상황 및 취약성 관리

*자세한 내용은 [Azure 보안 벤치 마크: 상태 및 취약성 관리](/azure/security/benchmarks/security-controls-v2-vulnerability-management)를 참조 하세요.*

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV 8: 일반적인 공격 시뮬레이션 수행

**지침** : 요구 사항에 따라 Azure 리소스에 대 한 침투 테스트 또는 레드 팀 활동을 수행 하 고 모든 중요 한 보안 결과를 수정 합니다.

참여의 Microsoft 클라우드 침투 테스트 규칙에 따라 침투 테스트가 Microsoft 정책을 위반 하지 않는지 확인 합니다. Microsoft의 전략, Microsoft에서 관리 하는 클라우드 인프라, 서비스 및 응용 프로그램에 대 한 라이브 사이트 침투 테스트의 실행 전략을 사용 합니다.

- [Azure의 침투 테스트](../security/fundamentals/pen-testing.md)

- [Engagement의 침투 테스트 규칙](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft 클라우드 Red 팀](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center 모니터링** : 해당 없음

**책임** : Customer

## <a name="endpoint-security"></a>끝점 보안

*자세한 내용은 [Azure 보안 벤치 마크: 끝점 보안](/azure/security/benchmarks/security-controls-v2-endpoint-security)을 참조 하세요.*

### <a name="es-1-use-endpoint-detection-and-response-edr"></a>ES-1: EDR (끝점 검색 및 응답) 사용

**지침** : 고객은 명시적으로 끝점 검색 및 응답 설정을 구성할 수 없습니다. 그러나 Azure 가상 WAN 제공에 사용 되는 Virtual Machines는 이러한 기능을 사용 합니다. 참조 된 링크에서 이러한 일반 기능에 대해 자세히 알아보세요. 

- [Microsoft Defender Advanced Threat Protection 개요](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection)

- [Windows 서버용 Microsoft Defender ATP 서비스](/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints)

- [비 Windows 서버용 Microsoft Defender ATP 서비스](/windows/security/threat-protection/microsoft-defender-atp/configure-endpoints-non-windows)

**Azure Security Center 모니터링** : 예

**책임** : 공유됨

## <a name="governance-and-strategy"></a>거버넌스 및 전략

*자세한 내용은 [Azure 보안 벤치 마크: 거 버 넌 스 및 전략](/azure/security/benchmarks/security-controls-v2-governance-strategy)을 참조 하세요.*

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1: 자산 관리 및 데이터 보호 전략 정의 

**지침** : 시스템 및 데이터의 지속적인 모니터링과 보호를 위한 명확한 전략을 문서화 하 고 전달 해야 합니다. 비즈니스에 중요 한 데이터 및 시스템의 검색, 평가, 보호 및 모니터링에 우선 순위를 지정 합니다. 

이 전략에는 다음 요소에 대 한 문서화 된 지침, 정책 및 표준이 포함 되어야 합니다. 

- 비즈니스 위험에 따라 데이터 분류 표준
- 위험 및 자산 인벤토리에 대 한 보안 조직 가시성 
- 사용할 Azure 서비스의 보안 조직 승인 
- 수명 주기를 통한 자산의 보안
- 조직 데이터 분류에 따라 필요한 액세스 제어 전략
- Azure 기본 및 타사 데이터 보호 기능 사용
- 전송 중 및 미사용 사용 사례에 대 한 데이터 암호화 요구 사항
- 적절 한 암호화 표준

참조 된 링크에서 사용할 수 있는 추가 정보를 검토 합니다.

- [Azure 보안 아키텍처 권장 사항-저장소, 데이터 및 암호화](https://docs.microsoft.com/azure/architecture/framework/security/storage-data-encryption?toc=/security/compass/toc.json&amp;bc=/security/compass/breadcrumb/toc.json)

- [Azure 보안 기본 사항-Azure 데이터 보안, 암호화 및 저장소](../security/fundamentals/encryption-overview.md)

- [클라우드 채택 프레임 워크-Azure 데이터 보안 및 암호화 모범 사례](https://docs.microsoft.com/azure/security/fundamentals/data-encryption-best-practices?toc=/azure/cloud-adoption-framework/toc.json&amp;bc=/azure/cloud-adoption-framework/_bread/toc.json)

**Azure Security Center 모니터링** : 현재 사용할 수 없음

**책임** : Customer

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2: 엔터프라이즈 조각화 전략 정의 

**지침** : id, 네트워크, 응용 프로그램, 구독, 관리 그룹 및 기타 컨트롤의 조합을 사용 하 여 자산에 대 한 액세스를 분할 하는 엔터프라이즈 수준의 전략을 수립 합니다. 서로 통신 해야 하는 시스템의 일상 작업을 수행 하 고 데이터에 액세스 해야 하는 경우에는 보안 분리의 필요성을 신중 하 게 조정 해야 합니다.

조각화 전략은 네트워크 보안, id 및 액세스 모델, 응용 프로그램 권한 또는 액세스 모델, 사용자 프로세스 컨트롤 등을 비롯 하 여 컨트롤 형식 간에 일관 되 게 구현 됩니다.

- [Azure의 조각화 전략에 대 한 지침 (비디오)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Azure의 조각화 전략에 대 한 지침 (문서)](/security/compass/governance#enterprise-segmentation-strategy)

- [기업 구분 전략을 사용 하 여 네트워크 조각화 맞추기](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Azure Security Center 모니터링** : 현재 사용할 수 없음

**책임** : Customer

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3: 보안 상태 관리 전략 정의

**지침** : 개별 자산 및 호스트 되는 환경에 대 한 위험을 지속적으로 측정 하 고 완화 합니다. 게시 된 응용 프로그램, 네트워크 수신 및 송신 위치, 사용자 및 관리자 끝점과 같은 높은 가치 자산 및 높은 수준의 공격 노출 영역에 우선 순위를 지정 합니다.

- [Azure 보안 벤치 마크-상태 및 취약성 관리](/azure/security/benchmarks/security-benchmark-v2-posture-vulnerability-management)

**Azure Security Center 모니터링** : 현재 사용할 수 없음

**책임** : Customer

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4: 조직 역할, 책임 및 accountabilities 정렬

**지침** : 보안 조직의 역할 및 책임에 대 한 명확한 전략을 문서화 하 고 전달 하는지 확인 합니다. 보안 의사 결정에 대 한 명확한 책임을 기준으로 활동의 우선 순위를 지정 하 고, 모든 사용자를 공유 책임 모델에 교육 하 고, 클라우드를 보호 하는 기술 팀을 교육 합니다

- [Azure 보안 모범 사례 1 – 사람: 클라우드 보안 경험에 대 한 팀 교육](https://aka.ms/AzSec1)

- [Azure 보안 모범 사례 2-사람: 클라우드 보안 기술에 대 한 팀 교육](https://aka.ms/AzSec2)

- [Azure 보안 모범 사례 3-프로세스: 클라우드 보안 결정에 대 한 책임 할당](https://aka.ms/AzSec3)

**Azure Security Center 모니터링** : 현재 사용할 수 없음

**책임** : Customer

### <a name="gs-5-define-network-security-strategy"></a>GS-5: 네트워크 보안 전략 정의

**지침** : 조직의 전체 보안 액세스 제어 전략의 일부로 Azure 네트워크 보안 방법을 설정 합니다. 이 전략에는 다음 요소에 대 한 문서화 된 지침, 정책 및 표준이 포함 되어야 합니다. 

- 중앙 집중식 네트워크 관리 및 보안 책임
- 엔터프라이즈 조각화 전략에 맞춘 가상 네트워크 조각화 모델
- 다양 한 위협 및 공격 시나리오에서 수정 전략
- 인터넷에 지 및 수신 및 송신 전략
- 하이브리드 클라우드 및 온-프레미스 상호 연결과 전략
- 최신 네트워크 보안 아티팩트 (예: 네트워크 다이어그램, 참조 네트워크 아키텍처)

참조 된 링크에서 사용할 수 있는 추가 정보를 검토 합니다.

- [Azure 보안 모범 사례 11-아키텍처. 단일 통합 보안 전략](https://aka.ms/AzSec11)

- [Azure 보안 벤치 마크-네트워크 보안](/azure/security/benchmarks/security-benchmark-v2-network-security)

- [Azure 네트워크 보안 개요](../security/fundamentals/network-overview.md)

- [엔터프라이즈 네트워크 아키텍처 전략](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Azure Security Center 모니터링** : 현재 사용할 수 없음

**책임** : Customer

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6: id 및 권한 있는 액세스 전략 정의

**지침** : 조직의 전체 보안 액세스 제어 전략의 일부로 Azure id 및 권한 있는 액세스 접근 방법을 설정 합니다. 이 전략에는 다음 요소에 대 한 문서화 된 지침, 정책 및 표준이 포함 되어야 합니다. 

- 다른 내부 및 외부 id 시스템을 사용 하는 중앙 집중식 id와 인증 시스템 및 상호 연결과
- 다양 한 사용 사례 및 조건에서 강력한 인증 방법
- 높은 권한이 있는 사용자의 보호
- 변칙 사용자 활동 모니터링 및 처리  
- 사용자 id 및 액세스 검토 및 조정 프로세스

참조 된 링크에서 사용할 수 있는 추가 정보를 검토 합니다.

- [Azure 보안 벤치 마크-Id 관리](/azure/security/benchmarks/security-benchmark-v2-identity-management)

- [Azure 보안 벤치 마크-권한 있는 액세스](/azure/security/benchmarks/security-benchmark-v2-privileged-access)

- [Azure 보안 모범 사례 11-아키텍처. 단일 통합 보안 전략](https://aka.ms/AzSec11)

- [Azure ID 관리 보안 개요](../security/fundamentals/identity-management-overview.md)

**Azure Security Center 모니터링** : 현재 사용할 수 없음

**책임** : Customer

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: 로깅 및 위협 응답 전략 정의

**지침** : 규정 준수 요구 사항을 충족 하면서 신속 하 게 위협을 감지 하 고 수정할 수 있는 로깅 및 위협 대응 전략을 수립 합니다. 통합 및 수동 단계가 아닌 위협에 집중할 수 있도록 고품질 경고와 원활한 환경을 제공 하는 분석가의 우선 순위를 지정 합니다. 

이 전략에는 다음 요소에 대 한 문서화 된 지침, 정책 및 표준이 포함 되어야 합니다. 

- 보안 작업 (SecOps) 조직의 역할 및 책임
- NIST 또는 다른 업계 프레임 워크를 사용 하 여 잘 정의 된 인시던트 응답 프로세스
- 위협 감지, 인시던트 대응 및 규정 준수 요구 사항을 지원 하기 위한 로그 캡처 및 보존
- SIEM, 네이티브 Azure 기능 및 기타 소스를 사용 하 여 위협에 대 한 중앙 집중식 및 상관 관계 정보를 제공 하는 고객, 공급 업체 및 공공 당사자와의 통신 및 알림 계획
- 로깅 및 위협 검색, 관련자, 공격 수정 및 eradication 같은 인시던트 처리를 위해 Azure native 및 타사 플랫폼 사용
- 학습 및 증거 보관과 같은 인시던트 및 인시던트 후 활동을 처리 하는 프로세스

참조 된 링크에서 사용할 수 있는 추가 정보를 검토 합니다.
- [Azure 보안 벤치 마크-로깅 및 위협 검색](/azure/security/benchmarks/security-benchmark-v2-logging-threat-detection)

- [Azure 보안 벤치 마크-인시던트 응답](/azure/security/benchmarks/security-benchmark-v2-incident-response)

- [Azure 보안 모범 사례 4-프로세스. 클라우드에 대 한 인시던트 응답 프로세스 업데이트](https://aka.ms/AzSec4)

- [Azure 채택 프레임 워크, 로깅 및 보고 의사 결정 가이드](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

**Azure Security Center 모니터링** : 현재 사용할 수 없음

**책임** : Customer

## <a name="next-steps"></a>다음 단계

- [Azure Security 벤치 마크 V2 개요](/azure/security/benchmarks/overview) 를 참조 하세요.
- [Azure 보안 기준](/azure/security/benchmarks/security-baselines-overview)에 대해 자세히 알아보세요.
