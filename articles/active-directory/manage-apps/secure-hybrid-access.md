---
title: Azure AD 보안 하이브리드 액세스 | Microsoft Docs
description: 이 문서에서는 레거시 온-프레미스, 퍼블릭 클라우드 또는 프라이빗 클라우드 애플리케이션을 Azure AD와 통합하기 위한 파트너 솔루션에 대해 설명합니다. 앱 배달 컨트롤러 또는 네트워크를 Azure AD에 연결하여 레거시 앱을 보호하세요.
services: active-directory
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 2/16/2021
ms.author: gasinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: ad3946dcd850a4a3d05f4d28882d08408e39b707
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111964691"
---
# <a name="secure-hybrid-access-secure-legacy-apps-with-azure-active-directory"></a>보안 하이브리드 액세스: Azure Active Directory를 사용한 보안 레거시 앱

이제 온-프레미스 및 클라우드 레거시 인증 애플리케이션을 다음과 함께 Azure AD(Active Directory)에 연결하여 보호할 수 있습니다.

- [Azure AD 애플리케이션 프록시](#secure-hybrid-access-sha-through-azure-ad-application-proxy)

- [기존 애플리케이션 배달 컨트롤러 및 네트워크](#sha-through-networking-and-delivery-controllers)

- [VPN(가상 사설망) 및 SDP(소프트웨어 정의 경계) 애플리케이션](#sha-through-vpn-and-sdp-applications)

Azure AD [조건부 액세스](../conditional-access/overview.md) 및 Azure AD [ID 보호](../identity-protection/overview-identity-protection.md) 같은 Azure AD 기능을 사용하여 모든 애플리케이션에서 간극을 연결하고, 보안 태세를 강화할 수 있습니다.

## <a name="secure-hybrid-access-sha-through-azure-ad-application-proxy"></a>Azure AD 애플리케이션 프록시를 통한 SHA(보안 하이브리드 액세스)
  
[애플리케이션 프록시](../app-proxy/what-is-application-proxy.md)를 통해 온-프레미스 웹 애플리케이션에 대한 [보안 원격 액세스](../app-proxy/application-proxy.md)를 제공할 수 있습니다. 사용자가 VPN을 사용할 필요가 없습니다. 사용자는 [Single Sign-On](add-application-portal-setup-sso.md) 후에 모든 디바이스에서 애플리케이션에 쉽게 연결하여 이점을 누릴 수 있습니다. 애플리케이션 프록시는 원격 액세스를 서비스로 제공하므로 [온-프레미스 애플리케이션](../app-proxy/application-proxy-add-on-premises-application.md)을 회사 네트워크 외부의 사용자에게 쉽게 게시할 수 있습니다. 온-프레미스 애플리케이션을 수정할 필요 없이 클라우드 액세스 관리를 확장하는 데 도움이 됩니다. 다음 단계로 [Azure AD 애플리케이션 프록시 배포를 계획](../app-proxy/application-proxy-deployment-plan.md)합니다.

## <a name="azure-ad-partner-integrations"></a>Azure AD 파트너 통합

### <a name="sha-through-networking-and-delivery-controllers"></a>네트워킹 및 배달 컨트롤러를 통한 SHA

[Azure AD 애플리케이션 프록시](../app-proxy/what-is-application-proxy.md)외에도, [제로 트러스트 프레임워크](https://www.microsoft.com/security/blog/2020/04/02/announcing-microsoft-zero-trust-assessment-tool/)를 사용할 수 있도록 Microsoft는 타사 공급자와 협력하고 있습니다. 기존 네트워킹 및 배달 컨트롤러를 사용하여 비즈니스 프로세스에 중요하지만 이전에는 Azure AD로 보호할 수 없었던 레거시 애플리케이션을 쉽게 보호할 수 있습니다. 이러한 애플리케이션을 보호하는 데 필요한 모든 조건은 이미 충족되었을 것입니다.

![네트워킹 파트너 및 앱 프록시를 사용하여 보안 하이브리드 액세스를 보여 주는 이미지](./media/secure-hybrid-access/secure-hybrid-access.png)

다음 네트워킹 공급업체는 Azure AD와 통합하기 위한 미리 빌드한 솔루션과 자세한 지침을 제공합니다.

- [Akamai EAA(엔터프라이즈 애플리케이션 액세스)](../saas-apps/akamai-tutorial.md)

- [Citrix ADC(Application Delivery Controller)](../saas-apps/citrix-netscaler-tutorial.md)

- [F5 Big-IP APM](./f5-aad-integration.md)

- [Kemp](../saas-apps/kemp-tutorial.md)

- [Pulse Secure VTM(Virtual Traffic Manager)](../saas-apps/pulse-secure-virtual-traffic-manager-tutorial.md)

### <a name="sha-through-vpn-and-sdp-applications"></a>VPN 및 SDP 애플리케이션을 통한 SHA

VPN 및 SDP 솔루션을 사용하여 조직의 데이터를 보호하면서 언제 어디서나 모든 디바이스에서 엔터프라이즈 네트워크에 대한 보안 액세스를 제공할 수 있습니다. IDP(ID 공급자)로 Azure AD를 사용하여 Azure AD [Single Sign-On](./what-is-single-sign-on.md) 및 [다단계 인증](../authentication/concept-mfa-howitworks.md)과 같은 최신 인증 및 권한 부여 방법을 통해 온-프레미스 레거시 애플리케이션을 보호할 수 있습니다.  

![VPN 파트너 및 앱 프록시를 사용하여 보안 하이브리드 액세스를 보여 주는 이미지 ](./media/secure-hybrid-access/app-proxy-vpn.png)

다음 VPN 공급업체는 Azure AD와 통합하기 위한 미리 빌드한 솔루션과 자세한 지침을 제공합니다.

- [Cisco AnyConnect](../saas-apps/cisco-anyconnect.md)

- [Fortinet](../saas-apps/fortigate-ssl-vpn-tutorial.md)

- [F5 Big-IP APM](./f5-aad-password-less-vpn.md)

- [Palo Alto Networks Global Protect](../saas-apps/paloaltoadmin-tutorial.md)

- [Pulse Secure PCS(Pulse Connect Secure)](../saas-apps/pulse-secure-pcs-tutorial.md)

다음 SDP 공급업체는 Azure AD와 통합하기 위한 미리 빌드한 솔루션과 자세한 지침을 제공합니다.

- [Datawiza Access Broker](./add-application-portal-setup-oidc-sso.md)

- [Perimeter 81](../saas-apps/perimeter-81-tutorial.md)


- [Silverfort 인증 플랫폼](./add-application-portal-setup-oidc-sso.md)

- [Strata](../saas-apps/maverics-identity-orchestrator-saml-connector-tutorial.md)

- [ZPA(Zscaler Private Access)](../saas-apps/zscalerprivateaccess-tutorial.md)