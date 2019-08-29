---
title: 새로운 기능 릴리스 정보 - Azure Active Directory | Microsoft Docs
description: Azure Active Directory의 최신 릴리스 정보, 알려진 문제, 버그 수정, 사용되지 않는 기능, 예정된 변경 내용 등을 알아봅니다.
services: active-directory
author: eross-msft
manager: daveba
featureFlags:
- clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: lizross
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: afab194c984dcbfa96a9342f46b892c7ec08d321
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/29/2019
ms.locfileid: "70135681"
---
# <a name="whats-new-in-azure-active-directory"></a>Azure Active Directory의 새로운 기능

>이 URL을 복사 하 고이 URL을 붙여 넣어 업데이트에 대 한이 페이지 `https://docs.microsoft.com/api/search/rss?search=%22release+notes+for+azure+AD%22&locale=en-us` 를 다시 ![방문 하는 시기](./media/whats-new/feed-icon-16x16.png) 에 대 한 알림 받기: RSS 피드 판독기 아이콘 피드 판독기.

Azure AD는 지속적인 향상되고 있습니다. 최신 개발 정보를 확인할 수 있도록 이 문서에서는 다음과 같은 정보를 제공합니다.

- 최신 릴리스
- 알려진 문제
- 버그 수정
- 사용되지 않는 기능
- 변경 계획

이 페이지는 매월 업데이트되므로 정기적으로 다시 방문해 주세요. 6개월 이상된 항목을 찾으려는 경우 [Azure Active Directory의 새로운 기능 아카이브](whats-new-archive.md)에서 찾을 수 있습니다.

---

## <a name="august-2019"></a>2019년 8월

### <a name="adalnet-ignores-the-msalnet-shared-cache-for-on-behalf-of-scenarios"></a>ADAL.NET는 시나리오를 대신해 MSAL.NET 공유 캐시를 무시 합니다.

**유형:** 수정됨  
**서비스 범주:** 인증(로그인)  
**제품 기능:** 사용자 인증

Azure AD 인증 라이브러리 (ADAL.NET) 버전 5.0.0-preview부터 앱 개발자는 [웹 앱 및 웹 api에 대 한 계정 당 캐시 하나를 직렬화](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization#custom-token-cache-serialization-in-web-applications--web-api)해야 합니다. 그렇지 않은 경우의 `UserAssertion`일부 특정 사용 사례와 함께 [진행 중인 흐름](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-api-call-api-app-configuration#on-behalf-of-flow)을 사용 하는 일부 시나리오에서는 권한 상승이 발생할 수 있습니다. 이 취약점을 방지 하기 위해 ADAL.NET는 이제 시나리오를 대신해 MSAL.NET (Microsoft authentication library for dotnet) 공유 캐시를 무시 합니다.

이 문제에 대 한 자세한 내용은 [인증 라이브러리 권한 상승 취약점 Azure Active Directory](https://portal.msrc.microsoft.com/security-guidance/advisory/CVE-2019-1258)을 참조 하세요.

---

## <a name="july-2019"></a>7 월 2019

### <a name="plan-for-change-application-proxy-service-update-to-support-only-tls-12"></a>변경 계획: TLS 1.2만 지원 하도록 응용 프로그램 프록시 서비스 업데이트

**유형:** 변경 계획  
**서비스 범주:** 앱 프록시  
**제품 기능:** Access Control

가장 강력한 암호화를 제공 하기 위해 응용 프로그램 프록시 서비스 액세스를 TLS 1.2 프로토콜로만 제한 하기 시작 하겠습니다. 이 제한은 처음에는 이미 TLS 1.2 프로토콜을 사용 하는 고객에 게 롤아웃 되므로 영향을 볼 수 없습니다. TLS 1.0 및 TLS 1.1 프로토콜의 사용 중단은 2019 년 8 월 31 일에 완료 될 예정입니다. 여전히 TLS 1.0 및 TLS 1.1를 사용 하는 고객은이 변경 내용을 준비 하기 위한 고급 알림을 받게 됩니다.

이 변경 전체에서 응용 프로그램 프록시 서비스에 대 한 연결을 유지 하려면 TLS 1.2를 사용 하도록 클라이언트 서버와 브라우저 서버 조합이 업데이트 되도록 하는 것이 좋습니다. 또한 직원이 응용 프로그램 프록시 서비스를 통해 게시 된 앱에 액세스 하는 데 사용 하는 클라이언트 시스템을 포함 하는 것이 좋습니다.

자세한 내용은 [Azure Active Directory에서 응용 프로그램 프록시를 통해 원격 액세스를 위한 온-프레미스 응용 프로그램 추가](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application)를 참조 하세요.

---

### <a name="plan-for-change-design-updates-are-coming-for-the-application-gallery"></a>변경 계획: 응용 프로그램 갤러리에 대 한 디자인 업데이트가 제공 됩니다.

**유형:** 변경 계획  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능:** SSO

새 사용자 인터페이스 변경 내용은 **응용 프로그램 추가** 블레이드의 **갤러리에서 추가** 의 디자인에 적용 됩니다. 이러한 변경 내용을 통해 자동 프로 비전, SAML (Openid connect Connect, Security Assertion Markup Language) 및 SSO (암호 Single Sign-On)를 지 원하는 앱을 보다 쉽게 찾을 수 있습니다.

---

### <a name="plan-for-change-removal-of-the-mfa-server-ip-address-from-the-office-365-ip-address"></a>변경 계획: Office 365 IP 주소에서 MFA 서버 IP 주소 제거

**유형:** 변경 계획  
**서비스 범주:** MFA  
**제품 기능:** ID 보안 및 보호

[Office 365 Ip 주소 및 URL 웹 서비스](https://docs.microsoft.com/office365/enterprise/office-365-ip-web-service)에서 MFA 서버 ip 주소를 제거 하는 중입니다. 현재 이러한 페이지를 사용 하 여 방화벽 설정을 업데이트 하는 경우 시작의 **Azure Multi-Factor Authentication 서버 방화벽 요구 사항** 섹션에 설명 된 IP 주소 목록도 포함 하 고 있는지 확인 해야 합니다. [ Azure Multi-Factor Authentication 서버 문서를](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfaserver-deploy#azure-multi-factor-authentication-server-firewall-requirements) 참조 하세요.

---

### <a name="app-only-tokens-now-require-the-client-app-to-exist-in-the-resource-tenant"></a>이제 앱 전용 토큰에는 클라이언트 앱이 리소스 테 넌 트에 있어야 합니다.

**유형:** 수정됨  
**서비스 범주:** 인증(로그인)  
**제품 기능:** 사용자 인증

2019 년 7 월 26 일에 [클라이언트 자격 증명 부여](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow)를 통해 앱 전용 토큰을 제공 하는 방법을 변경 했습니다. 이전에는 앱이 테 넌 트에서 클라이언트 앱이 있는지 여부에 관계 없이 다른 앱을 호출 하는 토큰을 가져올 수 있었습니다. 이 동작은 웹 Api 라고도 하는 단일 테 넌 트 리소스를 리소스 테 넌 트에 있는 클라이언트 앱 에서만 호출할 수 있도록 업데이트 되었습니다.

앱이 리소스 테 넌 트에 없는 경우이 문제를 해결 `The service principal named <app_name> was not found in the tenant named <tenant_name>. This can happen if the application has not been installed by the administrator of the tenant.` 하려면 [관리자 동의 끝점](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#using-the-admin-consent-endpoint) 또는 [PowerShell을 사용 하 여 테 넌 트에서 클라이언트 앱 서비스 주체를 만들어야 한다는 오류 메시지가 표시 됩니다. ](https://docs.microsoft.com/azure/active-directory/develop/howto-authenticate-service-principal-powershell)-테 넌 트가 테 넌 트 내에서 작동 하는 앱 권한을 제공 하도록 합니다.

자세한 내용은 [인증의 새로운 기능](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#app-only-tokens-for-single-tenant-applications-are-only-issued-if-the-client-app-exists-in-the-resource-tenant)을 참조 하세요.

> [!NOTE]
> 클라이언트와 API 간의 기존 동의가 계속 필요 하지 않습니다. 앱은 여전히 자체 권한 부여 검사를 수행 해야 합니다.

---

### <a name="new-passwordless-sign-in-to-azure-ad-using-fido2-security-keys"></a>FIDO2 보안 키를 사용 하 여 Azure AD에 대 한 새로운 암호 없는 로그인

**유형:** 새 기능  
**서비스 범주:** 인증(로그인)  
**제품 기능:** 사용자 인증

Azure AD 고객은 이제 조직의 사용자 및 그룹에 대 한 FIDO2 보안 키를 관리 하는 정책을 설정할 수 있습니다. 최종 사용자는 자신의 보안 키를 직접 등록 하 고, 키를 사용 하 여 FIDO 지원 장치에서 웹 사이트의 Microsoft 계정에 로그인 하 고, Azure AD에 가입 된 Windows 10 장치에 로그인 할 수도 있습니다.

자세한 내용은 관리자 관련 정보는 [Azure AD에 대해 암호 없는 로그인 사용 (미리 보기)](/azure/active-directory/authentication/concept-authentication-passwordless) 및 최종 사용자 관련 정보에 [보안 키 (미리 보기)를 사용 하도록 보안 정보 설정](https://docs.microsoft.com/azure/active-directory/user-help/security-info-setup-security-key) 을 참조 하세요.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---july-2019"></a>Azure AD 앱 갤러리에서 사용할 수 있는 새 페더레이션된 앱-7 월 2019

**유형:** 새 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능:** 타사 통합

7 월 2019에는 앱 갤러리에 대 한 페더레이션 지원을 통해 다음과 같은 18 개의 새 앱을 추가 했습니다.

[Ungerboeck Software](https://docs.microsoft.com/azure/active-directory/saas-apps/ungerboeck-software-tutorial), [브라이트 패턴 Omnichannel 연락처 센터](https://docs.microsoft.com/azure/active-directory/saas-apps/bright-pattern-omnichannel-contact-center-tutorial), [영리 Nelly](https://docs.microsoft.com/azure/active-directory/saas-apps/clever-nelly-tutorial), [AcquireIO](https://docs.microsoft.com/azure/active-directory/saas-apps/acquireio-tutorial), [looop](https://www.looop.co/schedule-a-demo/), [제품 보드](https://docs.microsoft.com/azure/active-directory/saas-apps/productboard-tutorial), [Ethidex 준수 Office™에 대 한 MS Azure SSO 액세스](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on#password-based-sso), [믿으세요](https://docs.microsoft.com/azure/active-directory/saas-apps/hype-tutorial) [ Abstract](https://docs.microsoft.com/azure/active-directory/saas-apps/abstract-tutorial), [Ascentis](https://docs.microsoft.com/azure/active-directory/saas-apps/ascentis-tutorial), [Flipsnack](https://www.flipsnack.com/accounts/sign-in-sso.html), [Wandera](https://docs.microsoft.com/azure/active-directory/saas-apps/wandera-tutorial), [TwineSocial](https://twinesocial.com/), [Kallidus](https://docs.microsoft.com/azure/active-directory/saas-apps/kallidus-tutorial), [HyperAnna](https://docs.microsoft.com/azure/active-directory/saas-apps/hyperanna-tutorial), [PharmID WasteWitness](https://www.pharmid.com/), [i2B Connect](https://www.i2b-online.com/sign-up-to-use-i2b-connect-here-sso-access/), [JFrog artifactory](https://docs.microsoft.com/azure/active-directory/saas-apps/jfrog-artifactory-tutorial)

앱에 대한 자세한 내용은 [Azure Active Directory와 SaaS 애플리케이션 통합](https://aka.ms/appstutorial)을 참조하세요. Azure AD 앱 갤러리에 애플리케이션을 나열하는 방법에 대한 자세한 내용은 [Azure Active Directory 애플리케이션 갤러리에 애플리케이션 나열](https://aka.ms/azureadapprequest)을 참조하세요.

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>새로 지원 되는 SaaS 앱에 대 한 사용자 계정 프로 비전 자동화

**유형:** 새 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능:** 모니터링 및 보고

이제 새로 통합 되는 앱에 대 한 사용자 계정을 만들고, 업데이트 하 고, 삭제할 수 있습니다.

- [Dialpad](https://docs.microsoft.com/azure/active-directory/saas-apps/dialpad-provisioning-tutorial)

- [페더레이션된 디렉터리](https://docs.microsoft.com/azure/active-directory/saas-apps/federated-directory-provisioning-tutorial)

- [Figma](https://docs.microsoft.com/azure/active-directory/saas-apps/figma-provisioning-tutorial)

- [Leapsome](https://docs.microsoft.com/azure/active-directory/saas-apps/leapsome-provisioning-tutorial)

- [Peakon](https://docs.microsoft.com/azure/active-directory/saas-apps/peakon-provisioning-tutorial)

- [Smartsheet](https://docs.microsoft.com/azure/active-directory/saas-apps/smartsheet-provisioning-tutorial)

자동 사용자 계정 프로 비전을 사용 하 여 조직의 보안을 강화 하는 방법에 대 한 자세한 내용은 Azure AD를 사용 하 여 [SaaS 응용 프로그램에 사용자 프로 비전 자동화](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning) 를 참조 하세요.

---

### <a name="new-azure-ad-domain-services-service-tag-for-network-security-group"></a>네트워크 보안 그룹에 대 한 새 Azure AD Domain Services 서비스 태그

**유형:** 새 기능  
**서비스 범주:** Azure AD Domain Services  
**제품 기능:** Azure AD Domain Services

IP 주소 및 범위의 긴 목록을 관리 하는 경우 Azure 네트워크 보안 그룹에서 new **AzureActiveDirectoryDomainServices** network service 태그를 사용 하 여 Azure AD Domain Services 가상으로의 인바운드 트래픽을 보호할 수 있습니다. 네트워크 서브넷.

이 새 서비스 태그에 대 한 자세한 내용은 [Azure AD Domain Services의 네트워크 보안 그룹](../../active-directory-domain-services/network-considerations.md#network-security-groups-and-required-ports)을 참조 하세요.

---

### <a name="new-security-audits-for-azure-ad-domain-services-public-preview"></a>Azure AD Domain Services에 대 한 새로운 보안 감사 (공개 미리 보기)

**유형:** 새 기능  
**서비스 범주:** Azure AD Domain Services  
**제품 기능:** Azure AD Domain Services

Azure AD 도메인 서비스 보안 감사 릴리스를 공개 미리 보기로 발표 하 게 되어 기쁘게 생각 합니다. 보안 감사는 Azure AD 도메인 서비스를 사용 하 여 Azure Storage, Azure Log Analytics 작업 영역 및 Azure Event Hub를 비롯 한 대상 리소스에 보안 감사 이벤트를 스트리밍하 여 인증 서비스에 대 한 중요 한 통찰력을 제공 합니다. 포탈.

자세한 내용은 [Azure AD Domain Services에 대 한 보안 감사 사용 (미리 보기)](https://docs.microsoft.com/azure/active-directory-domain-services/security-audit-events)을 참조 하세요.

---

### <a name="new-authentication-methods-usage--insights-public-preview"></a>새 인증 방법 사용 & 정보 (공개 미리 보기)

**유형:** 새 기능  
**서비스 범주:** 셀프 서비스 암호 재설정  
**제품 기능:** 모니터링 및 보고

새 인증 방법 사용 & insights 보고서를 사용 하면 등록 된 수를 비롯 하 여 Azure Multi-Factor Authentication 및 셀프 서비스 암호 재설정과 같은 기능을 조직에서 등록 하 고 사용 하는 방법을 이해 하는 데 도움이 될 수 있습니다. 각 기능에 대 한 사용자, 암호 재설정을 위해 셀프 서비스 암호 재설정을 사용 하는 빈도 및 재설정을 수행 하는 방법입니다.

자세한 내용은 [인증 방법 사용 & insights (미리 보기)](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-methods-usage-insights)를 참조 하세요.

---

### <a name="new-security-reports-are-available-for-all-azure-ad-administrators-public-preview"></a>모든 Azure AD 관리자 (공개 미리 보기)에 대해 새 보안 보고서를 사용할 수 있습니다.

**유형:** 새 기능  
**서비스 범주:** Identity Protection  
**제품 기능:** ID 보안 및 보호

모든 Azure AD 관리자는 이제 **위험한 사용자** 및 **위험한 로그인** 보고서에 표시 된 대로 새 보안 환경 사용을 시작 하기 위해 **위험 플래그가 지정 된 사용자** 보고서와 같은 기존 보안 보고서의 맨 위에 있는 배너를 선택할 수 있습니다. . 시간이 지남에 따라 모든 보안 보고서는 이전 버전에서 새 버전으로 이동 하 고 다음과 같은 추가 기능을 제공 하는 새로운 보고서를 제공 합니다.

- 고급 필터링 및 정렬

- 사용자 위험 해제와 같은 대량 작업

- 손상 되거나 안전 된 엔터티 확인

- 위험 상태, 다음을 다룹니다. 위험에 노출 됨, 재구성 됨 및 확인 됨

자세한 내용은 [위험한 사용자 보고서](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risky-users-signins#risky-users-report) 및 [위험한 로그인 보고서](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risky-users-signins#risky-sign-ins-report)를 참조 하세요.

---

### <a name="new-security-audits-for-azure-ad-domain-services-public-preview"></a>Azure AD Domain Services에 대 한 새로운 보안 감사 (공개 미리 보기)

**유형:** 새 기능  
**서비스 범주:** Azure AD Domain Services  
**제품 기능:** Azure AD Domain Services

Azure AD 도메인 서비스 보안 감사 릴리스를 공개 미리 보기로 발표 하 게 되어 기쁘게 생각 합니다. 보안 감사는 Azure AD 도메인 서비스를 사용 하 여 Azure Storage, Azure Log Analytics 작업 영역 및 Azure Event Hub를 비롯 한 대상 리소스에 보안 감사 이벤트를 스트리밍하 여 인증 서비스에 대 한 중요 한 통찰력을 제공 합니다. 포탈.

자세한 내용은 [Azure AD Domain Services에 대 한 보안 감사 사용 (미리 보기)](https://docs.microsoft.com/azure/active-directory-domain-services/security-audit-events)을 참조 하세요.

---

### <a name="new-b2b-direct-federation-using-samlws-fed-public-preview"></a>SAML/WS를 사용 하는 새 B2B direct 페더레이션 (공개 미리 보기)

**유형:** 새 기능  
**서비스 범주:** B2B  
**제품 기능:** B2B/B2C

직접 페더레이션은 SAML 또는 WS 공급 표준을 지 원하는 id 시스템을 사용 하 여 IT 관리 id 솔루션이 Azure AD가 아닌 파트너와 쉽게 작업할 수 있도록 도와줍니다. 파트너와의 직접 페더레이션 관계를 설정한 후에는 해당 도메인에서 초대 하는 모든 새 게스트 사용자가 기존 조직 계정을 사용 하 여 공동 작업을 수행할 수 있으므로 게스트에 대 한 사용자 환경을 더욱 원활 하 게 만들 수 있습니다.

자세한 내용은 [AD FS를 사용 하 여 직접 페더레이션 및 게스트 사용자를 위한 타사 공급자 (미리 보기)](https://docs.microsoft.com/azure/active-directory/b2b/direct-federation)를 참조 하세요.

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>새로 지원 되는 SaaS 앱에 대 한 사용자 계정 프로 비전 자동화

**유형:** 새 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능:** 모니터링 및 보고

이제 새로 통합 되는 앱에 대 한 사용자 계정을 만들고, 업데이트 하 고, 삭제할 수 있습니다.

- [Dialpad](https://docs.microsoft.com/azure/active-directory/saas-apps/dialpad-provisioning-tutorial)

- [페더레이션된 디렉터리](https://docs.microsoft.com/azure/active-directory/saas-apps/federated-directory-provisioning-tutorial)

- [Figma](https://docs.microsoft.com/azure/active-directory/saas-apps/figma-provisioning-tutorial)

- [Leapsome](https://docs.microsoft.com/azure/active-directory/saas-apps/leapsome-provisioning-tutorial)

- [Peakon](https://docs.microsoft.com/azure/active-directory/saas-apps/peakon-provisioning-tutorial)

- [Smartsheet](https://docs.microsoft.com/azure/active-directory/saas-apps/smartsheet-provisioning-tutorial)

자동 사용자 계정 프로 비전을 사용 하 여 조직의 보안을 강화 하는 방법에 대 한 자세한 내용은 Azure AD를 사용 하 여 [SaaS 응용 프로그램에 사용자 프로 비전 자동화](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)를 참조 하세요.

---

### <a name="new-check-for-duplicate-group-names-in-the-azure-ad-portal"></a>Azure AD 포털에서 중복 그룹 이름에 대 한 새 확인

**유형:** 새 기능  
**서비스 범주:** 그룹 관리  
**제품 기능:** 공동 작업

이제 Azure AD 포털에서 그룹 이름을 만들거나 업데이트 하면 리소스에서 기존 그룹 이름을 복제 하 고 있는지 확인 하는 검사가 수행 됩니다. 이름이 다른 그룹에서 이미 사용 중인 것으로 확인 되 면 이름을 수정 하 라는 메시지가 표시 됩니다.

자세한 내용은 [AZURE AD 포털에서 그룹 관리](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal?context=azure/active-directory/users-groups-roles/context/ugr-context)를 참조 하세요.

---

### <a name="azure-ad-now-supports-static-query-parameters-in-reply-redirect-uris"></a>Azure AD는 이제 회신 (리디렉션) Uri에서 정적 쿼리 매개 변수를 지원 합니다.

**유형:** 새 기능  
**서비스 범주:** 인증(로그인)  
**제품 기능:** 사용자 인증

이제 Azure AD 앱은 OAuth 2.0 요청에 대해 정적 쿼리 매개 변수 (예: `https://contoso.com/oauth2?idp=microsoft`)를 사용 하 여 reply (리디렉션) uri를 등록 하 고 사용할 수 있습니다. 정적 쿼리 매개 변수는 회신 URI의 다른 부분과 마찬가지로 회신 uri에 대해 문자열 일치가 적용 됩니다. URL 디코딩된 리디렉션 uri와 일치 하는 등록 된 문자열이 없는 경우 요청이 거부 됩니다. 회신 URI가 있는 경우 정적 쿼리 매개 변수를 포함 하 여 전체 문자열이 사용자를 리디렉션하는 데 사용 됩니다.

동적 회신 Uri는 보안 위험을 나타내므로 인증 요청에 대 한 상태 정보를 유지 하는 데 사용할 수 없기 때문에 계속 사용할 수 없습니다. 이러한 목적을 위해 `state` 매개 변수를 사용 합니다.

현재 Azure Portal의 앱 등록 화면은 쿼리 매개 변수를 계속 차단 합니다. 그러나 응용 프로그램 매니페스트를 수동으로 편집 하 여 앱에서 쿼리 매개 변수를 추가 하 고 테스트할 수 있습니다. 자세한 내용은 [인증의 새로운 기능](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#redirect-uris-can-now-contain-query-string-parameters)을 참조 하세요.

---

### <a name="activity-logs-ms-graph-apis-for-azure-ad-are-now-available-through-powershell-cmdlets"></a>이제 PowerShell Cmdlet을 통해 Azure AD에 대 한 활동 로그 (MS Graph Api)를 사용할 수 있습니다.

**유형:** 새 기능  
**서비스 범주:** 보고  
**제품 기능:** 모니터링 및 보고

Azure ad 활동 로그 (감사 및 로그인 보고서)는 이제 Azure AD PowerShell 모듈을 통해 제공 된다는 것을 기쁘게 생각 합니다. 이전에는 MS Graph API 끝점을 사용 하 여 사용자 고유의 스크립트를 만들 수 있었습니다. 이제 PowerShell cmdlet에 대 한 기능을 확장 했습니다.

이러한 cmdlet을 사용 하는 방법에 대 한 자세한 내용은 [보고를 위한 AZURE AD PowerShell cmdlet](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-powershell-reporting)을 참조 하세요.

---

### <a name="updated-filter-controls-for-audit-and-sign-in-logs-in-azure-ad"></a>Azure AD의 감사 및 로그인 로그에 대 한 업데이트 된 필터 컨트롤

**유형:** 변경된 기능  
**서비스 범주:** 보고  
**제품 기능:** 모니터링 및 보고

이제 감사 및 로그인 로그 보고서를 업데이트 하 여 보고서 화면에 열로 추가 하지 않고도 다양 한 필터를 적용할 수 있습니다. 또한 이제 화면에 표시 하려는 필터의 수를 결정할 수 있습니다. 이러한 업데이트는 모두 함께 작동 하 여 보고서를 더 쉽게 읽고 요구 사항에 더 쉽게 확인할 수 있도록 합니다.

이러한 업데이트에 대 한 자세한 내용은 [감사 로그 필터링](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-audit-logs#filtering-audit-logs) 및 [로그인 작업 필터링](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins#filter-sign-in-activities)을 참조 하세요.

---

## <a name="june-2019"></a>2019년 6월

### <a name="new-riskdetections-api-for-microsoft-graph-public-preview"></a>Microsoft Graph에 대 한 새 riskDetections API (공개 미리 보기)

**유형:** 새 기능  
**서비스 범주:** Identity Protection  
**제품 기능:** ID 보안 및 보호

현재 공개 미리 보기로 제공 되는 Microsoft Graph에 대 한 새 riskDetections API를 발표 하 게 되어 기쁘게 생각 합니다. 이 새 API를 사용 하 여 조직의 Id 보호 관련 사용자 및 로그인 위험 검색 목록을 볼 수 있습니다. 이 API를 사용 하 여 검색 유형, 상태, 수준 등에 대 한 세부 정보를 포함 하 여 위험 검색을 보다 효율적으로 쿼리할 수도 있습니다.

자세한 내용은 [위험 검색 API 참조 설명서](https://docs.microsoft.com/graph/api/resources/riskdetection?view=graph-rest-beta)를 참조 하세요.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---june-2019"></a>Azure AD 앱 갤러리에서 사용할 수 있는 새로운 페더레이션된 앱-6 월 2019

**유형:** 새 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능:** 타사 통합

6 월 2019에 앱 갤러리에 대 한 페더레이션 지원을 포함 하는 다음과 같은 22 개의 새 앱을 추가 했습니다.

[Azure AD SAML Toolkit](https://docs.microsoft.com/azure/active-directory/saas-apps/saml-toolkit-tutorial), [Otsuka Shokai (大塚商会)](https://docs.microsoft.com/azure/active-directory/saas-apps/otsuka-shokai-tutorial), [ANAQUA](https://docs.microsoft.com/azure/active-directory/saas-apps/anaqua-tutorial), [Azure VPN Client](https://portal.azure.com/), [ExpenseIn](https://docs.microsoft.com/azure/active-directory/saas-apps/expensein-tutorial), [Helper Helper](https://docs.microsoft.com/azure/active-directory/saas-apps/helper-helper-tutorial), [Costpoint](https://docs.microsoft.com/azure/active-directory/saas-apps/costpoint-tutorial), [GlobalOne](https://docs.microsoft.com/azure/active-directory/saas-apps/globalone-tutorial), [Mercedes-Benz In-Car Office](https://me.secure.mercedes-benz.com/), [Skore](https://app.justskore.it/), [Oracle Cloud Infrastructure Console](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-cloud-tutorial), [CyberArk SAML Authentication](https://docs.microsoft.com/azure/active-directory/saas-apps/cyberark-saml-authentication-tutorial), [Scrible Edu](https://www.scrible.com/sign-in/#/create-account), [PandaDoc](https://docs.microsoft.com/azure/active-directory/saas-apps/pandadoc-tutorial), [Perceptyx](https://apexdata.azurewebsites.net/docs.microsoft.com/azure/active-directory/saas-apps/perceptyx-tutorial), [Proptimise OS](https://proptimise.co.uk/software/), [Vtiger CRM (SAML)](https://docs.microsoft.com/azure/active-directory/saas-apps/vtiger-crm-saml-tutorial), Oracle Access Manager for Oracle Retail Merchandising, Oracle Access Manager for Oracle E-Business Suite, Oracle IDCS for E-Business Suite, Oracle IDCS for PeopleSoft, Oracle IDCS for JD Edwards

앱에 대한 자세한 내용은 [Azure Active Directory와 SaaS 애플리케이션 통합](https://aka.ms/appstutorial)을 참조하세요. Azure AD 앱 갤러리에 애플리케이션을 나열하는 방법에 대한 자세한 내용은 [Azure Active Directory 애플리케이션 갤러리에 애플리케이션 나열](https://aka.ms/azureadapprequest)을 참조하세요.

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>새로 지원 되는 SaaS 앱에 대 한 사용자 계정 프로 비전 자동화

**유형:** 새 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능:** 모니터링 및 보고

이제 새로 통합 되는 앱에 대 한 사용자 계정을 만들고, 업데이트 하 고, 삭제할 수 있습니다.

- [확대/축소](https://docs.microsoft.com/azure/active-directory/saas-apps/zoom-provisioning-tutorial)

- [Envoy](https://docs.microsoft.com/azure/active-directory/saas-apps/envoy-provisioning-tutorial)

- [Proxyclick](https://docs.microsoft.com/azure/active-directory/saas-apps/proxyclick-provisioning-tutorial)

- [4me](https://docs.microsoft.com/azure/active-directory/saas-apps/4me-provisioning-tutorial)

자동 사용자 계정 프로 비전을 사용 하 여 조직의 보안을 강화 하는 방법에 대 한 자세한 내용은 Azure AD를 사용 하 여 [SaaS 응용 프로그램에 사용자 프로 비전 자동화](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning) 를 참조 하세요.

---

### <a name="view-the-real-time-progress-of-the-azure-ad-provisioning-service"></a>Azure AD 프로 비전 서비스의 실시간 진행률 보기

**유형:** 변경된 기능  
**서비스 범주:** 앱 프로비전  
**제품 기능:** ID 수명 주기 관리

사용자 프로 비전 프로세스에 얼마나 많은 시간이 표시 되는 새로운 진행률 표시줄을 포함 하도록 Azure AD 프로 비전 환경을 업데이트 했습니다. 이 업데이트 된 환경에서는 현재 주기 동안 프로 비전 된 사용자 수 및 현재까지 프로 비전 된 사용자 수에 대 한 정보도 제공 합니다.

자세한 내용은 [사용자 프로 비전 상태 확인](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user)을 참조 하세요.

---

### <a name="company-branding-now-appears-on-sign-out-and-error-screens"></a>이제 회사 브랜딩이 로그 아웃 및 오류 화면에 표시 됩니다.

**유형:** 변경된 기능  
**서비스 범주:** 인증(로그인)  
**제품 기능:** 사용자 인증

이제 Azure AD가 업데이트 되어 로그인 페이지 뿐만 아니라 로그 아웃 및 오류 화면에 회사 브랜딩이 표시 됩니다. 이 기능을 설정 하기 위해 어떠한 작업도 수행할 필요가 없습니다. Azure AD는 단순히 Azure Portal의 **회사 브랜딩** 영역에 이미 설정 된 자산을 사용 합니다.

회사 브랜드를 설정 하는 방법에 대 한 자세한 내용은 [조직의 Azure Active Directory 페이지에 브랜딩 추가](https://docs.microsoft.com/azure/active-directory/fundamentals/customize-branding)를 참조 하세요.

---

### <a name="azure-multi-factor-authentication-mfa-server-is-no-longer-available-for-new-deployments"></a>MFA (Azure Multi-Factor Authentication) 서버를 새 배포에 더 이상 사용할 수 없습니다.

**유형:** 사용되지 않음  
**서비스 범주:** MFA  
**제품 기능:** ID 보안 및 보호

2019 년 7 월 1 일부 터 Microsoft는 더 이상 새 배포에 대해 MFA 서버를 제공 하지 않습니다. 조직에서 multi-factor authentication을 요구 하려는 신규 고객은 이제 클라우드 기반 Azure Multi-Factor Authentication를 사용 해야 합니다. 7 월 1 일 이전에 MFA 서버를 활성화 한 고객은 변경 내용이 표시 되지 않습니다. 계속 최신 버전을 다운로드 하 고, 향후 업데이트를 가져오고, 활성화 자격 증명을 생성할 수 있습니다.

자세한 내용은 [Azure Multi-Factor Authentication 서버 시작](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfaserver-deploy)을 참조 하세요. 클라우드 기반 Azure Multi-Factor Authentication에 대 한 자세한 내용은 [클라우드 기반 azure Multi-Factor Authentication 배포 계획](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)을 참조 하세요.

---

## <a name="may-2019"></a>2019년 5월

### <a name="service-change-future-support-for-only-tls-12-protocols-on-the-application-proxy-service"></a>서비스 변경: 응용 프로그램 프록시 서비스의 TLS 1.2 프로토콜에 대 한 향후 지원

**유형:** 변경 계획  
**서비스 범주:** 앱 프록시  
**제품 기능:** Access Control

고객에 대 한 최고의 암호화를 제공 하기 위해 응용 프로그램 프록시 서비스에서 TLS 1.2 프로토콜만 액세스를 제한 합니다. 이 변경은 이미 TLS 1.2 프로토콜을 사용 하는 고객에 게 점진적으로 롤아웃 되므로 변경 내용이 표시 되지 않습니다.

TLS 1.0 및 TLS 1.1의 사용 중단은 2019 년 8 월 31 일에 발생 하지만,이 변경 내용을 준비 하는 데 도움이 되는 추가 고급 알림을 제공 합니다. 이 변경 내용을 준비 하려면 응용 프로그램 프록시를 통해 게시 된 앱에 액세스 하기 위해 사용자가 사용 하는 클라이언트를 포함 하 여 클라이언트-서버 및 브라우저-서버 조합이 업데이트 되었는지 확인 하 고 응용 프로그램에 대 한 연결을 유지 하기 위해 TLS 1.2 프로토콜을 사용 하도록 업데이트 합니다. 프록시 서비스. 자세한 내용은 [Azure Active Directory에서 응용 프로그램 프록시를 통해 원격 액세스를 위한 온-프레미스 응용 프로그램 추가](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#before-you-begin)를 참조 하세요.

---

### <a name="use-the-usage-and-insights-report-to-view-your-app-related-sign-in-data"></a>사용량 및 통찰력 보고서를 사용 하 여 앱 관련 로그인 데이터를 확인 합니다.

**유형:** 새 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능:** 모니터링 및 보고

이제 Azure Portal의 **엔터프라이즈 응용 프로그램** 영역에 있는 사용 및 정보 보고서를 사용 하 여 다음에 대 한 정보를 포함 하 여 로그인 데이터의 응용 프로그램 중심 보기를 가져올 수 있습니다.

- 조직에 가장 많이 사용 되는 앱

- 실패 한 로그인이 가장 많은 앱

- 각 앱에 대 한 상위 로그인 오류

이 기능에 대 한 자세한 내용은 [Azure Active Directory 포털의 사용 현황 및 정보 보고서](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-usage-insights-report) 를 참조 하세요.

---

### <a name="automate-your-user-provisioning-to-cloud-apps-using-azure-ad"></a>Azure AD를 사용 하 여 클라우드 앱에 사용자 프로 비전 자동화

**유형:** 새 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능:** 모니터링 및 보고

Azure AD 프로 비전 서비스를 사용 하 여 다음 클라우드 기반 앱에 대 한 사용자 계정의 생성, 삭제 및 업데이트를 자동화 하려면 다음 새 자습서를 따르세요.

- [Comeet](https://docs.microsoft.com/azure/active-directory/saas-apps/comeet-recruiting-software-provisioning-tutorial)

- [DynamicSignal](https://docs.microsoft.com/azure/active-directory/saas-apps/dynamic-signal-provisioning-tutorial)

- [KeeperSecurity](https://docs.microsoft.com/azure/active-directory/saas-apps/keeper-password-manager-digitalvault-provisioning-tutorial)

그룹 개체를 프로 비전 하는 방법에 대 한 정보를 제공 하는이 새로운 [Dropbox 자습서](https://docs.microsoft.com/azure/active-directory/saas-apps/dropboxforbusiness-provisioning-tutorial)를 따를 수도 있습니다.

자동화 된 사용자 계정 프로 비전을 통해 조직의 보안을 강화 하는 방법에 대 한 자세한 내용은 [AZURE AD를 사용 하 여 SaaS 응용 프로그램에 사용자 프로 비전 자동화](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)를 참조 하세요.

---

### <a name="identity-secure-score-is-now-available-in-azure-ad-general-availability"></a>이제 Azure AD에서 id 보안 점수를 사용할 수 있습니다 (일반 공급).

**유형:** 새 기능  
**서비스 범주:** 해당 사항 없음  
**제품 기능:** ID 보안 및 보호

이제 Azure AD의 id 보안 점수 기능을 사용 하 여 id 보안 상태를 모니터링 하 고 개선할 수 있습니다. Id 보안 점수 기능은 단일 대시보드를 사용 하 여 다음과 같은 도움을 줍니다.

- 1에서 223 사이의 점수를 기준으로 id 보안 상태를 객관적으로 측정 합니다.

- Id 보안 향상에 대 한 계획

- 향상 된 보안 기능을 검토 합니다.

Id 보안 점수 기능에 대 한 자세한 내용은 [Azure Active Directory의 id 보안 점수는 무엇입니까?](https://docs.microsoft.com/azure/active-directory/fundamentals/identity-secure-score)를 참조 하세요.

---

### <a name="new-app-registrations-experience-is-now-available-general-availability"></a>이제 새로운 앱 등록 환경을 사용할 수 있습니다 (일반 공급).

**유형:** 새 기능  
**서비스 범주:** 인증(로그인)  
**제품 기능:** 개발자 환경

새로운 [앱 등록](https://aka.ms/appregistrations) 환경은 이제 일반 공급으로 제공 됩니다. 이 새로운 환경에는 Azure Portal 및 응용 프로그램 등록 포털에서 친숙 한 모든 주요 기능이 포함 되어 있으며 다음을 통해 개선 되었습니다.

- **더 나은 앱 관리.** 앱을 다른 포털에 표시 하는 대신, 이제 모든 앱을 한 곳에서 볼 수 있습니다.

- **간소화 된 앱 등록.** 향상 된 탐색 환경에서 개선 된 권한 선택 환경으로 앱을 더 쉽게 등록 하 고 관리할 수 있습니다.

- **자세한 정보.** 빠른 시작 가이드 등을 비롯 하 여 앱에 대 한 자세한 정보를 찾을 수 있습니다.

자세한 내용은 [Microsoft id 플랫폼](https://docs.microsoft.com/azure/active-directory/develop/) 및 현재 일반 공급 [되는 앱 등록 환경을](https://developer.microsoft.com/identity/blogs/new-app-registrations-experience-is-now-generally-available/) 참조 하세요. 블로그 알림.

---

### <a name="new-capabilities-available-in-the-risky-users-api-for-identity-protection"></a>Id 보호에 대 한 위험한 사용자 API에서 사용할 수 있는 새로운 기능

**유형:** 새 기능  
**서비스 범주:** Identity Protection  
**제품 기능:** ID 보안 및 보호

이제 위험한 사용자 API를 사용 하 여 사용자의 위험 기록을 검색 하 고, 위험한 사용자를 해제 하 고, 사용자가 손상 된 것으로 확인할 수 있습니다. 이러한 변경은 사용자의 위험 상태를 보다 효율적으로 업데이트 하 고 위험 기록을 이해 하는 데 도움이 됩니다.

자세한 내용은 [위험한 사용자 API 참조 설명서](https://docs.microsoft.com/graph/api/resources/riskyuser?view=graph-rest-beta)를 참조 하세요.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---may-2019"></a>Azure AD 앱 갤러리에서 사용할 수 있는 새로운 페더레이션된 앱-2019 년 5 월

**유형:** 새 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능:** 타사 통합

5 월 2019에 앱 갤러리에 대 한 페더레이션 지원을 포함 하는 새로운 21 개 앱을 추가 했습니다.

[Freedcamp](https://docs.microsoft.com/azure/active-directory/saas-apps/freedcamp-tutorial), [Real Links](https://docs.microsoft.com/azure/active-directory/saas-apps/real-links-tutorial), [Kianda](https://app.kianda.com/sso/OpenID/AzureAD/), [Simple Sign](https://docs.microsoft.com/azure/active-directory/saas-apps/simple-sign-tutorial), [Braze](https://docs.microsoft.com/azure/active-directory/saas-apps/braze-tutorial), [displayr](https://docs.microsoft.com/azure/active-directory/saas-apps/displayr-tutorial), [Templafy](https://docs.microsoft.com/azure/active-directory/saas-apps/templafy-tutorial), [Marketo Sales 참여](https://toutapp.com/login), [aclp](https://docs.microsoft.com/azure/active-directory/saas-apps/aclp-tutorial), [outsystems](https://docs.microsoft.com/azure/active-directory/saas-apps/outsystems-tutorial), [Meta4 Global HR](https://docs.microsoft.com/azure/active-directory/saas-apps/meta4-global-hr-tutorial), [퀀텀 작업 공간](https://docs.microsoft.com/azure/active-directory/saas-apps/quantum-workplace-tutorial), [코발트](https://docs.microsoft.com/azure/active-directory/saas-apps/cobalt-tutorial), [webMethods API 클라우드](https://docs.microsoft.com/azure/active-directory/saas-apps/webmethods-integration-cloud-tutorial), [RedFlag](https://pocketstop.com/redflag/), [Whatfix](https://docs.microsoft.com/azure/active-directory/saas-apps/whatfix-tutorial), [제어](https://docs.microsoft.com/azure/active-directory/saas-apps/control-tutorial), [jobhub](https://docs.microsoft.com/azure/active-directory/saas-apps/jobhub-tutorial), [NEOGOV](https://docs.microsoft.com/azure/active-directory/saas-apps/neogov-tutorial), [Foodee](https://docs.microsoft.com/azure/active-directory/saas-apps/foodee-tutorial), [myvr](https://docs.microsoft.com/azure/active-directory/saas-apps/myvr-tutorial)

앱에 대한 자세한 내용은 [Azure Active Directory와 SaaS 애플리케이션 통합](https://aka.ms/appstutorial)을 참조하세요. Azure AD 앱 갤러리에 애플리케이션을 나열하는 방법에 대한 자세한 내용은 [Azure Active Directory 애플리케이션 갤러리에 애플리케이션 나열](https://aka.ms/azureadapprequest)을 참조하세요.

---

### <a name="improved-groups-creation-and-management-experiences-in-the-azure-ad-portal"></a>Azure AD 포털에서 그룹 만들기 및 관리 환경 개선

**유형:** 새 기능  
**서비스 범주:** 그룹 관리  
**제품 기능:** 공동 작업

Azure AD 포털에서 그룹 관련 환경을 개선 했습니다. 이러한 향상 된 기능을 통해 관리자는 그룹 목록, 멤버 목록 및 추가 만들기 옵션을 보다 효율적으로 관리할 수 있습니다.

향상된 기능은 다음과 같습니다.

- 멤버 자격 유형 및 그룹 유형별 기본 필터링입니다.

- 원본 및 전자 메일 주소와 같은 새 열 추가

- 쉽게 삭제할 수 있도록 그룹, 멤버 및 소유자 목록을 다중 선택 하는 기능입니다.

- 그룹을 만드는 동안 전자 메일 주소를 선택 하 고 소유자를 추가할 수 있습니다.

자세한 내용은 [Azure Active Directory를 사용 하 여 기본 그룹 만들기 및 멤버 추가](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal)를 참조 하세요.

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-general-availability"></a>Azure AD 포털에서 Office 365 그룹에 대 한 명명 정책 구성 (일반 공급)

**유형:** 변경된 기능  
**서비스 범주:** 그룹 관리  
**제품 기능:** 공동 작업

이제 관리자는 Azure AD 포털을 사용 하 여 Office 365 그룹에 대 한 명명 정책을 구성할 수 있습니다. 이렇게 변경 하면 조직의 사용자가 만들거나 편집 하는 Office 365 그룹에 일관 된 명명 규칙을 적용 하는 데 도움이 됩니다.

두 가지 방법으로 Office 365 그룹에 대 한 명명 정책을 구성할 수 있습니다.

- 그룹 이름에 자동으로 추가 되는 접두사 또는 접미사를 정의 합니다.

- 그룹 이름에 허용 되지 않는 조직에 대해 사용자 지정 된 차단 된 단어 집합을 업로드 합니다 (예: "CEO, 급여, HR").

자세한 내용은 [Office 365 그룹에 대 한 명명 정책 적용](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-naming-policy)을 참조 하세요.

---

### <a name="microsoft-graph-api-endpoints-are-now-available-for-azure-ad-activity-logs-general-availability"></a>Microsoft Graph API 끝점은 이제 Azure AD 활동 로그 (일반 공급)에서 사용할 수 있습니다.

**유형:** 변경된 기능  
**서비스 범주:** 보고  
**제품 기능:** 모니터링 및 보고

Azure AD 활동 로그에 대 한 Microsoft Graph API 끝점 지원의 일반 공급을 발표 하 게 되어 기쁘게 생각 합니다. 이 릴리스에서는 이제 Azure AD 감사 로그와 로그인 로그 Api 모두의 버전 1.0을 사용할 수 있습니다.

자세한 내용은 [AZURE AD audit LOG API 개요](https://docs.microsoft.com/graph/api/resources/azure-ad-auditlog-overview?view=graph-rest-1.0)를 참조 하세요.

---

### <a name="administrators-can-now-use-conditional-access-for-the-combined-registration-process-public-preview"></a>이제 관리자는 결합 된 등록 프로세스 (공개 미리 보기)에 대 한 조건부 액세스를 사용할 수 있습니다.

**유형:** 새 기능  
**서비스 범주:** 조건부 액세스  
**제품 기능:** ID 보안 및 보호  

이제 관리자는 결합 된 등록 페이지에서 사용할 조건부 액세스 정책을 만들 수 있습니다. 여기에는 다음과 같은 경우 등록을 허용 하는 정책이 적용 됩니다.

- 사용자가 신뢰할 수 있는 네트워크에 있습니다.

- 사용자에 게는 낮은 로그인 위험이 있습니다.

- 사용자가 관리 되는 장치에 있습니다.

- 사용자는 조직의 사용 약관 (토우토 in)에 동의 합니다.

조건부 액세스 및 암호 재설정에 대 한 자세한 내용은 [AZURE AD 결합 MFA 및 암호 재설정 등록 환경 블로그 게시물에 대 한 조건부 액세스](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Conditional-access-for-the-Azure-AD-combined-MFA-and-password/ba-p/566348)를 참조 하세요. 결합 된 등록 프로세스에 대 한 조건부 액세스 정책에 대 한 자세한 내용은 [결합 된 등록에 대 한 조건부 액세스 정책](https://docs.microsoft.com/azure/active-directory/authentication/howto-registration-mfa-sspr-combined#conditional-access-policies-for-combined-registration)을 참조 하세요. Azure AD 사용 약관 기능에 대 한 자세한 내용은 [Azure Active Directory 사용 약관 기능](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use)을 참조 하세요.

---

## <a name="april-2019"></a>2019년 4월

### <a name="new-azure-ad-threat-intelligence-detection-is-now-available-as-part-of-azure-ad-identity-protection"></a>이제 새로운 Azure AD 위협 인텔리전스 검색을 Azure AD ID 보호의 일부로 사용할 수 있습니다.

**유형:** 새 기능  
**서비스 범주:** Azure AD Identity Protection  
**제품 기능:** ID 보안 및 보호

이제 업데이트 된 Azure AD ID 보호 기능의 일부로 Azure AD 위협 인텔리전스 검색을 사용할 수 있습니다. 이 새로운 기능을 사용 하면 Microsoft의 내부 및 외부 위협 인텔리전스 소스를 기반으로 알려진 공격 패턴과 일치 하는 특정 사용자 또는 활동에 대해 비정상적인 사용자 활동을 나타낼 수 있습니다.

새로 고친 버전의 Azure AD ID 보호에 대 한 자세한 내용은 [현재 공개 미리 보기 블로그의 4 가지 주요 Azure AD ID 보호 향상 된 기능](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Four-major-Azure-AD-Identity-Protection-enhancements-are-now-in/ba-p/326935) 및 [Azure Active Directory Identity Protection (새로 고침)](https://docs.microsoft.com/azure/active-directory/identity-protection/overview-v2) 을 참조 하세요. 자료. Azure AD 위협 인텔리전스 검색에 대 한 자세한 내용은 [Azure Active Directory Identity Protection 위험](https://docs.microsoft.com/azure/active-directory/identity-protection/risk-events-reference#azure-ad-threat-intelligence) 검색 문서를 참조 하세요.

---

### <a name="azure-ad-entitlement-management-is-now-available-public-preview"></a>이제 Azure AD 자격 관리 사용 가능 (공개 미리 보기)

**유형:** 새 기능  
**서비스 범주:** ID 거버넌스  
**제품 기능:** ID 거버넌스

현재 공개 미리 보기로 제공 되는 Azure AD 자격 관리를 통해 고객은 액세스 패키지 관리를 위임할 수 있습니다 .이 관리는 직원과 비즈니스 파트너가 액세스를 요청 하는 방법, 승인 해야 하는 사용자 및 액세스 기간을 정의 합니다. 액세스 패키지는 Azure AD 및 Office 365 그룹의 멤버 자격, 엔터프라이즈 응용 프로그램의 역할 할당 및 SharePoint Online 사이트에 대 한 역할 할당을 관리할 수 있습니다. 자격 관리에 대 한 자세한 내용은 [AZURE AD 자격 관리 개요](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview)를 참조 하세요. Privileged Identity Management, 액세스 검토 및 사용 약관을 포함 하 여 다양 한 Azure AD Identity Governance 기능에 대 한 자세한 내용은 [Azure AD Identity Governance 이란?](../governance/identity-governance-overview.md)를 참조 하세요.

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-public-preview"></a>Azure AD 포털에서 Office 365 그룹에 대 한 명명 정책 구성 (공개 미리 보기)

**유형:** 새 기능  
**서비스 범주:** 그룹 관리  
**제품 기능:** 공동 작업

이제 관리자는 Azure AD 포털을 사용 하 여 Office 365 그룹에 대 한 명명 정책을 구성할 수 있습니다. 이렇게 변경 하면 조직의 사용자가 만들거나 편집 하는 Office 365 그룹에 일관 된 명명 규칙을 적용 하는 데 도움이 됩니다.

두 가지 방법으로 Office 365 그룹에 대 한 명명 정책을 구성할 수 있습니다.

- 그룹 이름에 자동으로 추가 되는 접두사 또는 접미사를 정의 합니다.

- 그룹 이름에 허용 되지 않는 조직에 대해 사용자 지정 된 차단 된 단어 집합을 업로드 합니다 (예: "CEO, 급여, HR").

자세한 내용은 [Office 365 그룹에 대 한 명명 정책 적용](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-naming-policy)을 참조 하세요.

---

### <a name="azure-ad-activity-logs-are-now-available-in-azure-monitor-general-availability"></a>Azure AD 활동 로그는 이제 Azure Monitor (일반 공급)에서 사용할 수 있습니다.

**유형:** 새 기능  
**서비스 범주:** 보고  
**제품 기능:** 모니터링 및 보고

Azure AD 활동 로그를 사용 하 여 시각화에 대 한 피드백을 해결 하는 데 도움이 되도록 Log Analytics의 새로운 정보 활용 기능을 소개 하 고 있습니다. 이 기능을 사용 하면 통합 문서 라고 하는 대화형 템플릿을 사용 하 여 Azure AD 리소스에 대 한 정보를 얻을 수 있습니다. 이러한 미리 작성 된 통합 문서는 앱 또는 사용자에 대 한 세부 정보를 제공할 수 있으며 다음을 포함 합니다.

- **로그인.** 로그인 위치, 사용 중인 운영 체제 또는 브라우저 클라이언트 및 버전, 성공 또는 실패 한 로그인 수를 포함 하 여 앱 및 사용자에 대 한 세부 정보를 제공 합니다.

- **레거시 인증 및 조건부 액세스.** 조건부 액세스 정책에 의해 트리거되는 Multi-Factor Authentication 사용, 조건부 액세스 정책을 사용 하는 앱 등을 포함 하 여 레거시 인증을 사용 하는 앱 및 사용자에 대 한 세부 정보를 제공 합니다.

- **로그인 오류 분석** 사용자 작업, 정책 문제 또는 인프라로 인해 로그인 오류가 발생 하는지 여부를 확인 하는 데 도움이 됩니다.

- **사용자 지정 보고서.** 새로 만들거나 기존 통합 문서를 편집 하 여 조직에 대 한 통찰력 기능을 사용자 지정할 수 있습니다.

자세한 내용은 [Azure Active Directory 보고서에 Azure Monitor 통합 문서를 사용 하는 방법](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-use-azure-monitor-workbooks)을 참조 하세요.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---april-2019"></a>Azure AD 앱 갤러리에서 사용할 수 있는 새로운 페더레이션된 앱-4 월 2019

**유형:** 새 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능:** 타사 통합

4 월 2019에 앱 갤러리에 대 한 페더레이션 지원을 포함 하는 새로운 21 개 앱을 추가 했습니다.

[SAP Fiori](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-fiori-tutorial), [Hrworks Single Sign-on](https://docs.microsoft.com/azure/active-directory/saas-apps/hrworks-single-sign-on-tutorial), [percolate](https://docs.microsoft.com/azure/active-directory/saas-apps/percolate-tutorial), [MobiControl](https://docs.microsoft.com/azure/active-directory/saas-apps/mobicontrol-tutorial), [Citrix NetScaler](https://docs.microsoft.com/azure/active-directory/saas-apps/citrix-netscaler-tutorial), [Shibumi](https://docs.microsoft.com/azure/active-directory/saas-apps/shibumi-tutorial), [Benchling](https://docs.microsoft.com/azure/active-directory/saas-apps/benchling-tutorial), [MileIQ](https://mileiq.onelink.me/991934284/7e980085), [PageDNA](https://docs.microsoft.com/azure/active-directory/saas-apps/pagedna-tutorial), [EduBrite LMS](https://docs.microsoft.com/azure/active-directory/saas-apps/edubrite-lms-tutorial), [rstudio Connect ](https://docs.microsoft.com/azure/active-directory/saas-apps/rstudio-connect-tutorial), [Amms](https://docs.microsoft.com/azure/active-directory/saas-apps/amms-tutorial), [Mitel Connect](https://docs.microsoft.com/azure/active-directory/saas-apps/mitel-connect-tutorial), [Alibaba Cloud (역할 기반 SSO)](https://docs.microsoft.com/azure/active-directory/saas-apps/alibaba-cloud-service-role-based-sso-tutorial), [텐트 지분 관리](https://docs.microsoft.com/azure/active-directory/saas-apps/certent-equity-management-tutorial), [sectigo 인증서 관리자](https://docs.microsoft.com/azure/active-directory/saas-apps/sectigo-certificate-manager-tutorial), [GreenOrbit](https://docs.microsoft.com/azure/active-directory/saas-apps/greenorbit-tutorial), [워크 표](https://docs.microsoft.com/azure/active-directory/saas-apps/workgrid-tutorial), [monday.com](https://docs.microsoft.com/azure/active-directory/saas-apps/mondaycom-tutorial), [SurveyMonkey Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/surveymonkey-enterprise-tutorial), [Indiggo](https://indiggolead.com/)

앱에 대한 자세한 내용은 [Azure Active Directory와 SaaS 애플리케이션 통합](https://aka.ms/appstutorial)을 참조하세요. Azure AD 앱 갤러리에 애플리케이션을 나열하는 방법에 대한 자세한 내용은 [Azure Active Directory 애플리케이션 갤러리에 애플리케이션 나열](https://aka.ms/azureadapprequest)을 참조하세요.

---

### <a name="new-access-reviews-frequency-option-and-multiple-role-selection"></a>새 액세스 검토 빈도 옵션 및 여러 역할 선택

**유형:** 새 기능  
**서비스 범주:** 액세스 검토  
**제품 기능:** ID 거버넌스

Azure AD 액세스 검토의 새로운 업데이트를 통해 다음을 수행할 수 있습니다.

- 매주, 매월, 분기별 및 매년의 기존 옵션을 비롯 하 여 액세스 검토의 빈도를 **반기**로 변경 합니다.

- 단일 액세스 검토를 만들 때 여러 Azure AD 및 Azure 리소스 역할을 선택 합니다. 이 경우 모든 역할이 동일한 설정으로 설정 되 고 모든 검토자에 게 동시에 알림이 표시 됩니다.

액세스 검토를 만드는 방법에 대 한 자세한 내용은 [AZURE AD 액세스 검토에서 그룹 또는 응용 프로그램에 대 한 액세스 검토 만들기](https://docs.microsoft.com/azure/active-directory/governance/create-access-review)를 참조 하세요.

---

### <a name="azure-ad-connect-email-alert-systems-are-transitioning-sending-new-email-sender-information-for-some-customers"></a>전자 메일 경고 시스템이 전환 중 이며, 일부 고객에 대 한 새 전자 메일 보낸 사람 정보를 보내는 Azure AD Connect

**유형:** 변경된 기능  
**서비스 범주:** AD Sync  
**제품 기능:** 플랫폼

Azure AD Connect는 전자 메일 경고 시스템을 전환 하 여 일부 고객이 새 전자 메일을 보낸 사람을 보여 줄 수 있습니다. 이를 해결 하려면 조직의 허용 목록 `azure-noreply@microsoft.com` 에를 추가 해야 합니다. 그렇지 않으면 Office 365, Azure 또는 동기화 서비스에서 중요 한 경고를 계속 받을 수 없습니다.

---

### <a name="upn-suffix-changes-are-now-successful-between-federated-domains-in-azure-ad-connect"></a>이제의 페더레이션된 도메인 간에 UPN 접미사 변경이 성공 Azure AD Connect

**유형:** 수정됨  
**서비스 범주:** AD Sync  
**제품 기능:** 플랫폼

이제 Azure AD Connect의 페더레이션 도메인 간에 사용자의 UPN 접미사를 성공적으로 변경할 수 있습니다. 이 수정은 동기화 주기 중에 FederatedDomainChangeError 오류 메시지를 더 이상 표시 하지 않거나 "Azure Active Directory에서이 개체를 업데이트할 수 없습니다. FederatedUser UserPrincipalName]가 잘못 되었습니다. 로컬 디렉터리 서비스에서 값을 업데이트 합니다. "

자세한 내용은 [동기화 중 오류 문제 해결](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-sync-errors#federateddomainchangeerror)을 참조 하세요.

---

### <a name="increased-security-using-the-app-protection-based-conditional-access-policy-in-azure-ad-public-preview"></a>Azure AD에서 앱 보호 기반 조건부 액세스 정책을 사용 하 여 보안 강화 (공개 미리 보기)

**유형:** 새 기능  
**서비스 범주:** 조건부 액세스  
**제품 기능:** ID 보안 및 보호

앱 보호 기반 조건부 액세스는 이제 **앱 보호 필요** 정책을 사용 하 여 사용할 수 있습니다. 이 새로운 정책을 사용 하면 다음을 방지 하 여 조직의 보안을 강화할 수 있습니다.

- 사용자는 Microsoft Intune 라이선스 없이 앱에 액세스할 수 있습니다.

- 사용자가 Microsoft Intune 앱 보호 정책을 가져올 수 없습니다.

- 사용자는 구성 된 Microsoft Intune 앱 보호 정책을 사용 하지 않고 앱에 액세스할 수 있습니다.

자세한 내용은 [조건부 액세스를 사용 하 여 cloud app access에 앱 보호 정책을 요구 하는 방법](https://docs.microsoft.com/azure/active-directory/conditional-access/app-protection-based-conditional-access)을 참조 하세요.

---

### <a name="new-support-for-azure-ad-single-sign-on-and-conditional-access-in-microsoft-edge-public-preview"></a>Microsoft Edge에서 Azure AD Single Sign-On 및 조건부 액세스에 대 한 새로운 지원 (공개 미리 보기)

**유형:** 새 기능  
**서비스 범주:** 조건부 액세스  
**제품 기능:** ID 보안 및 보호

Azure AD Single Sign-On 및 조건부 액세스에 대 한 새로운 지원을 제공 하는 것을 포함 하 여 Microsoft Edge에 대 한 Azure AD 지원 기능이 향상 되었습니다. 이전에 Managed Browser Microsoft Intune 사용한 경우 이제 Microsoft Edge를 대신 사용할 수 있습니다.

조건부 액세스를 사용 하 여 장치 및 앱을 설정 하 고 관리 하는 방법에 대 한 자세한 내용은 조건부 액세스를 사용 하는 클라우드 앱 액세스를 [위한 관리 되는 장치 필요](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices) 및 조건부 액세스를 사용 하는 [cloud app access에 승인 된 ](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access). Microsoft Intune 정책과 함께 Microsoft Edge를 사용 하 여 액세스를 관리 하는 방법에 대 한 자세한 내용은 [Microsoft Intune 정책으로 보호 된 브라우저를 사용 하 여 인터넷 액세스 관리](https://docs.microsoft.com/intune/app-configuration-managed-browser)를 참조 하세요.

---

## <a name="march-2019"></a>2019년 3월

### <a name="identity-experience-framework-and-custom-policy-support-in-azure-active-directory-b2c-is-now-available-ga"></a>Azure Active Directory B2C에서 id 경험 프레임 워크 및 사용자 지정 정책 지원 제공 (GA)

**유형:** 새 기능  
**서비스 범주:** B2C - 소비자 ID 관리  
**제품 기능:** B2B/B2C

이제 규모에 따라 지원 되는 다음 작업과 Azure SLA를 포함 하 여 Azure AD B2C에서 사용자 지정 정책을 만들 수 있습니다.

- 사용자 지정 정책을 사용 하 여 사용자 지정 인증 사용자 경험를 만들고 업로드 합니다.

- 클레임 공급자 간의 교환으로 사용자 경험을 단계별로 설명합니다.

- 사용자 경험에서 조건부 분기를 정의합니다.

- 실시간 의사 결정 및 통신에서 사용할 클레임을 변환 하 고 매핑합니다.

- 사용자 지정 인증 사용자 경험에서 REST API 사용 서비스를 사용 합니다. 예를 들어 전자 메일 공급자, CRMs 및 독점 권한 부여 시스템이 있습니다.

- OpenIDConnect 프로토콜을 준수 하는 id 공급자와 페더레이션 합니다. 예를 들어 다중 테 넌 트 Azure AD, 소셜 계정 공급자 또는 2 단계 인증 공급자를 사용 합니다.

사용자 지정 정책을 만드는 방법에 대 한 자세한 내용은 [Azure Active Directory B2C의 사용자 지정 정책에 대 한 개발자](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-developer-notes-custom) 정보 및 [사례 연구를 비롯 한 Alex Simon의 블로그 게시물을](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-custom-policies-to-build-your-own-identity-journeys/ba-p/382791)참조 하세요.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---march-2019"></a>Azure AD 앱 갤러리에서 사용할 수 있는 새로운 페더레이션된 앱-3 월 2019

**유형:** 새 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능:** 타사 통합

3 월 2019에 앱 갤러리에 대 한 페더레이션 지원을 통해 이러한 14 개의 새 앱을 추가 했습니다.

[ISEC7 Mobile Exchange Delegate](https://www.isec7.com/english/), [MediusFlow](https://office365.cloudapp.mediusflow.com/), [ePlatform](https://docs.microsoft.com/azure/active-directory/saas-apps/eplatform-tutorial), [Fulcrum](https://docs.microsoft.com/azure/active-directory/saas-apps/fulcrum-tutorial), [ExcelityGlobal](https://docs.microsoft.com/azure/active-directory/saas-apps/excelityglobal-tutorial), [Explanation-Based Auditing System](https://docs.microsoft.com/azure/active-directory/saas-apps/explanation-based-auditing-system-tutorial), [Lean](https://docs.microsoft.com/azure/active-directory/saas-apps/lean-tutorial), [Powerschool Performance Matters](https://docs.microsoft.com/azure/active-directory/saas-apps/powerschool-performance-matters-tutorial), [Cinode](https://cinode.com/), [Iris Intranet](https://docs.microsoft.com/azure/active-directory/saas-apps/iris-intranet-tutorial), [Empactis](https://docs.microsoft.com/azure/active-directory/saas-apps/empactis-tutorial), [SmartDraw](https://docs.microsoft.com/azure/active-directory/saas-apps/smartdraw-tutorial), [Confirmit Horizons](https://docs.microsoft.com/azure/active-directory/saas-apps/confirmit-horizons-tutorial), [TAS](https://docs.microsoft.com/azure/active-directory/saas-apps/tas-tutorial)

앱에 대한 자세한 내용은 [Azure Active Directory와 SaaS 애플리케이션 통합](https://aka.ms/appstutorial)을 참조하세요. Azure AD 앱 갤러리에 애플리케이션을 나열하는 방법에 대한 자세한 내용은 [Azure Active Directory 애플리케이션 갤러리에 애플리케이션 나열](https://aka.ms/azureadapprequest)을 참조하세요.

---

### <a name="new-zscaler-and-atlassian-provisioning-connectors-in-the-azure-ad-gallery---march-2019"></a>Azure AD 갤러리에서 새로운 Zscaler 및 Atlassian 프로 비전 커넥터-3 월 2019

**유형:** 새 기능  
**서비스 범주:** 앱 프로비전  
**제품 기능:** 타사 통합

다음 앱의 사용자 계정을 만들고, 업데이트 하 고, 삭제 하는 작업을 자동화 합니다.

[Zscaler](https://aka.ms/ZscalerProvisioning), [Zscaler Beta](https://aka.ms/ZscalerBetaProvisioning), [Zscaler One](https://aka.ms/ZscalerOneProvisioning), [Zscaler 2](https://aka.ms/ZscalerTwoProvisioning), Zscaler [3](https://aka.ms/ZscalerThreeProvisioning), Zscaler [zscloud](https://aka.ms/ZscalerZSCloudProvisioning), [Atlassian cloud](https://aka.ms/atlassianCloudProvisioning)

자동화 된 사용자 계정 프로 비전을 통해 조직의 보안을 강화 하는 방법에 대 한 자세한 내용은 [AZURE AD를 사용 하 여 SaaS 응용 프로그램에 사용자 프로 비전 자동화](https://aka.ms/ProvisioningDocumentation)를 참조 하세요.

---

### <a name="restore-and-manage-your-deleted-office-365-groups-in-the-azure-ad-portal"></a>Azure AD 포털에서 삭제 된 Office 365 그룹 복원 및 관리

**유형:** 새 기능  
**서비스 범주:** 그룹 관리  
**제품 기능:** 공동 작업

이제 Azure AD 포털에서 삭제 된 Office 365 그룹을 보고 관리할 수 있습니다. 이렇게 변경 하면 조직에서 필요 하지 않은 모든 그룹을 영구적으로 삭제 하는 것과 함께 복원할 수 있는 그룹을 확인할 수 있습니다.

자세한 내용은 [만료 또는 삭제 된 그룹 복원](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-restore-deleted#view-and-manage-the-deleted-office-365-groups-that-are-available-to-restore)을 참조 하세요.

---

### <a name="single-sign-on-is-now-available-for-azure-ad-saml-secured-on-premises-apps-through-application-proxy-public-preview"></a>이제 응용 프로그램 프록시 (공개 미리 보기)를 통해 Azure AD SAML 보안이 설정 된 온-프레미스 앱에 대해 Single sign-on을 사용할 수 있습니다.

**유형:** 새 기능  
**서비스 범주:** 앱 프록시  
**제품 기능:** Access Control

이제 응용 프로그램 프록시를 통해 이러한 앱에 대 한 원격 액세스와 함께 온-프레미스 SAML 인증 앱에 대 한 SSO (Single Sign-On) 환경을 제공할 수 있습니다. 온-프레미스 앱에서 SAML SSO를 설정 하는 방법에 대 한 자세한 내용은 [응용 프로그램 프록시를 사용 하는 온-프레미스 응용 프로그램에 대 한 saml Single Sign-On (미리 보기)](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-single-sign-on-on-premises-apps)를 참조 하세요.

---

### <a name="client-apps-in-request-loops-will-be-interrupted-to-improve-reliability-and-user-experience"></a>요청 루프의 클라이언트 앱이 중단 되어 안정성 및 사용자 환경을 개선 합니다.

**유형:** 새 기능  
**서비스 범주:** 인증(로그인)  
**제품 기능:** 사용자 인증

클라이언트 앱은 짧은 시간 동안 수백 개의 동일한 로그인 요청을 잘못 발행할 수 있습니다. 이러한 요청은 성공 여부에 관계 없이, 모든 사용자에 대 한 대기 시간이 IDP 모든 사용자에 대 한 대기 시간이 증가 하 고 IDP의 가용성을 줄이도록 합니다.

이 업데이트는 일반 `invalid_grant` 작업의 `AADSTS50196: The server terminated an operation because it encountered a loop while processing a request` 범위를 벗어나 짧은 시간 동안 중복 요청을 여러 번 실행 하는 클라이언트 앱에 오류를 보냅니다. 이 문제가 발생 하는 클라이언트 앱은 대화형 프롬프트를 표시 하 여 사용자가 다시 로그인 하도록 해야 합니다. 이 변경 내용 및이 오류가 발생 하는 경우 앱을 수정 하는 방법에 대 한 자세한 내용은 [인증의 새로운 기능](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#looping-clients-will-be-interrupted)을 참조 하세요.

---

### <a name="new-audit-logs-user-experience-now-available"></a>새 감사 로그 사용자 환경을 지금 사용 가능

**유형:** 변경된 기능  
**서비스 범주:** 보고  
**제품 기능:** 모니터링 및 보고

가독성을 높이고 정보를 검색 하는 방법을 개선 하는 데 도움이 되는 새로운 Azure AD **감사 로그** 페이지를 만들었습니다. 새 **감사 로그** 페이지를 표시 하려면 Azure AD의 **작업** 섹션에서 **감사 로그** 를 선택 합니다.

![새 감사 로그 페이지, 샘플 정보 포함](media/whats-new/audit-logs-page.png)

새 **감사 로그** 페이지에 대 한 자세한 내용은 [Azure Active Directory 포털에서 감사 작업 보고서](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-audit-logs#audit-logs)를 참조 하세요.

---

### <a name="new-warnings-and-guidance-to-help-prevent-accidental-administrator-lockout-from-misconfigured-conditional-access-policies"></a>잘못 구성 된 조건부 액세스 정책에서 실수로 인 한 관리자의 잠금 방지에 도움이 되는 새로운 경고 및 지침

**유형:** 변경된 기능  
**서비스 범주:** 조건부 액세스  
**제품 기능:** ID 보안 및 보호

잘못 구성 된 조건부 액세스 정책을 통해 관리자가 실수로 자체 테 넌 트를 잠그지 못하도록 방지 하기 위해 Azure Portal에서 새로운 경고 및 업데이트 된 지침을 만들었습니다. 새 지침에 대 한 자세한 내용은 [Azure Active Directory 조건부 액세스의 서비스 종속성 이란?](https://docs.microsoft.com/azure/active-directory/conditional-access/service-dependencies)을 참조 하세요.

---

### <a name="improved-end-user-terms-of-use-experiences-on-mobile-devices"></a>모바일 장치에 대 한 최종 사용자 사용 환경 개선

**유형:** 변경된 기능  
**서비스 범주:** 서비스 계약  
**제품 기능:** 거버넌스

Microsoft는 모바일 장치에서 사용 약관을 검토 하 고 동의 하는 방법을 개선 하는 데 도움이 되는 기존 사용 환경을 업데이트 했습니다. 이제 확대 및 축소 하 고, 뒤로 돌아가서 정보를 다운로드 하 고, 하이퍼링크를 선택할 수 있습니다. 업데이트 된 사용 약관에 대 한 자세한 내용은 [Azure Active Directory 사용 약관 기능](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#what-terms-of-use-looks-like-for-users)을 참조 하세요.

---

### <a name="new-azure-ad-activity-logs-download-experience-available"></a>새 Azure AD 활동 로그 다운로드 환경 사용 가능

**유형:** 변경된 기능  
**서비스 범주:** 보고  
**제품 기능:** 모니터링 및 보고

이제 Azure Portal에서 직접 대량의 활동 로그를 다운로드할 수 있습니다. 이 업데이트를 통해 다음을 수행할 수 있습니다.

- 최대 25만 개의 행을 다운로드 합니다.

- 다운로드가 완료 되 면 알림 받기

- 파일 이름을 사용자 지정 합니다.

- 출력 형식 (JSON 또는 CSV)을 결정 합니다.

이 기능 [에 대 한 자세한 내용은 빠른 시작: Azure Portal를 사용 하 여 감사 보고서 다운로드](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-download-audit-report)

---

### <a name="breaking-change-updates-to-condition-evaluation-by-exchange-activesync-eas"></a>호환성이 손상되는 변경 내용: EAS (Exchange ActiveSync)에의 한 조건 평가 업데이트

**유형:** 변경 계획  
**서비스 범주:** 조건부 액세스  
**제품 기능:** Access Control

EAS (Exchange ActiveSync)에서 다음과 같은 조건을 평가 하는 방법을 업데이트 하는 중입니다.

- 국가, 지역 또는 IP 주소를 기준으로 하는 사용자 위치

- 로그인 위험

- 디바이스 플랫폼

이전에 조건부 액세스 정책에서 이러한 조건을 사용한 경우 조건 동작이 변경 될 수 있다는 점에 유의 해야 합니다. 예를 들어 이전에 정책에서 사용자 위치 조건을 사용 하는 경우 사용자의 위치를 기준으로 현재 건너뛴 정책을 찾을 수 있습니다.

---

## <a name="february-2019"></a>2019년 2월

### <a name="configurable-azure-ad-saml-token-encryption-public-preview"></a>구성 가능한 Azure AD SAML 토큰 암호화(공개 미리 보기) 

**유형:** 새 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능:** SSO

이제 암호화 된 SAML 토큰을 수신 하도록 지원 되는 SAML 앱을 구성할 수 있습니다. 앱에서 구성 하 고 사용 하는 경우 azure AD는 Azure AD에 저장 된 인증서에서 가져온 공개 키를 사용 하 여 내보낸 SAML 어설션을 암호화 합니다.

SAML 토큰 암호화를 구성 하는 방법에 대 한 자세한 내용은 [AZURE AD saml 토큰 암호화 구성](https://docs.microsoft.com/azure/active-directory/manage-apps/howto-saml-token-encryption)을 참조 하세요.

---

### <a name="create-an-access-review-for-groups-or-apps-using-azure-ad-access-reviews"></a>Azure AD 액세스 검토를 사용하여 그룹 또는 앱에 대한 액세스 검토 만들기

**유형:** 새 기능  
**서비스 범주:** 액세스 검토  
**제품 기능:** 거버넌스

이제 그룹 멤버 자격 또는 앱 할당에 대 한 단일 Azure AD 액세스 검토에 여러 그룹 또는 앱을 포함할 수 있습니다. 여러 그룹 또는 앱을 사용한 액세스 검토는 동일한 설정을 사용 하 여 설정 되며 포함 된 모든 검토자에 게 동시에 알림이 표시 됩니다.

Azure AD 액세스 검토를 사용 하 여 액세스 검토를 만드는 방법에 대 한 자세한 내용은 [AZURE Ad 액세스 검토에서 그룹 또는 응용 프로그램에 대 한 액세스 검토 만들기](https://docs.microsoft.com/azure/active-directory/governance/create-access-review) 를 참조 하세요.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---february-2019"></a>Azure AD 앱 갤러리에서 사용할 수 있는 새로운 페더레이션된 앱 - 2019년 2월

**유형:** 새 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능:** 타사 통합
 
2 월 2019에 앱 갤러리에 페더레이션 지원이 있는 27 개의 새 앱을 추가 했습니다.

[Euromonitor Passport](https://docs.microsoft.com/azure/active-directory/saas-apps/euromonitor-passport-tutorial), [MINDTICKLE](https://docs.microsoft.com/azure/active-directory/saas-apps/mindtickle-tutorial), [FAT 손가락](https://seeforgetest-exxon.azurewebsites.net/Account/create?Length=7), 여는 [스택](https://docs.microsoft.com/azure/active-directory/saas-apps/airstack-tutorial), [Oracle Fusion ERP](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-fusion-erp-tutorial), [IDrive](https://docs.microsoft.com/azure/active-directory/saas-apps/idrive-tutorial), [Skyward QMLATIV](https://docs.microsoft.com/azure/active-directory/saas-apps/skyward-qmlativ-tutorial), [Brightidea](https://docs.microsoft.com/azure/active-directory/saas-apps/brightidea-tutorial), [AlertOps](https://docs.microsoft.com/azure/active-directory/saas-apps/alertops-tutorial), [Soloinsight-cloudgate SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/soloinsight-cloudgate-sso-tutorial), 권한 클릭, [Brandfolder](https://docs.microsoft.com/azure/active-directory/saas-apps/brandfolder-tutorial), [StoregateSmartFile](https://docs.microsoft.com/azure/active-directory/saas-apps/smartfile-tutorial), [pexip](https://docs.microsoft.com/azure/active-directory/saas-apps/pexip-tutorial), [stormboard](https://docs.microsoft.com/azure/active-directory/saas-apps/stormboard-tutorial), [지진](https://docs.microsoft.com/azure/active-directory/saas-apps/seismic-tutorial), [꿈](https://www.shareadream.org/how-it-works), [Bugsnag](https://docs.microsoft.com/azure/active-directory/saas-apps/bugsnag-tutorial), [webMethods Integration Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/webmethods-integration-cloud-tutorial), [기술 자료 모든 위치에서 LMS](https://docs.microsoft.com/azure/active-directory/saas-apps/knowledge-anywhere-lms-tutorial), [OU 캠퍼스](https://docs.microsoft.com/azure/active-directory/saas-apps/ou-campus-tutorial), [periscope 데이터](https://docs.microsoft.com/azure/active-directory/saas-apps/periscope-data-tutorial), [netop 포털](https://docs.microsoft.com/azure/active-directory/saas-apps/netop-portal-tutorial), [Smartvid.io](https://docs.microsoft.com/azure/active-directory/saas-apps/smartvid.io-tutorial), [PureCloud by genesys](https://docs.microsoft.com/azure/active-directory/saas-apps/purecloud-by-genesys-tutorial), [생산성 플랫폼](https://docs.microsoft.com/azure/active-directory/saas-apps/clickup-productivity-platform-tutorial)

앱에 대한 자세한 내용은 [Azure Active Directory와 SaaS 애플리케이션 통합](https://aka.ms/appstutorial)을 참조하세요. Azure AD 앱 갤러리에 애플리케이션을 나열하는 방법에 대한 자세한 내용은 [Azure Active Directory 애플리케이션 갤러리에 애플리케이션 나열](https://aka.ms/azureadapprequest)을 참조하세요.

---

### <a name="enhanced-combined-mfasspr-registration"></a>향상된 통합 MFA/SSPR 등록

**유형:** 변경된 기능  
**서비스 범주:** 셀프 서비스 암호 재설정  
**제품 기능:** 사용자 인증

고객 피드백에 대 한 응답으로, 통합 된 MFA/SSPR 등록 미리 보기 환경을 개선 하 여 사용자가 MFA와 SSPR에 대 한 보안 정보를 더 빠르게 등록할 수 있도록 지원 합니다. 

**현재 사용자에 게 향상 된 환경을 설정 하려면 다음 단계를 수행 합니다.**

1. 전역 관리자 또는 사용자 관리자는 Azure Portal에 로그인 하 여 **Azure Active Directory > 사용자 설정 > 액세스 패널 미리 보기 기능에 대 한 설정 관리**로 이동 합니다. 

2. **보안 정보를 등록 하 고 관리 하기 위한 미리 보기 기능을 사용할 수 있는 사용자 – 새로 고침** 옵션에서 **선택한 사용자 그룹** 또는 **모든 사용자**에 대 한 기능을 설정 하도록 선택 합니다.

다음 몇 주 동안 아직 켜져 있지 않은 테 넌 트에 대해 이전에 결합 된 MFA/SSPR 등록 미리 보기 환경을 설정 하는 기능을 제거 합니다.

**테 넌 트에서 컨트롤이 제거 되는지 확인 하려면 다음 단계를 수행 합니다.**

1. 전역 관리자 또는 사용자 관리자는 Azure Portal에 로그인 하 여 **Azure Active Directory > 사용자 설정 > 액세스 패널 미리 보기 기능에 대 한 설정 관리**로 이동 합니다.  

2. **보안 정보를 등록 하 고 관리 하기 위한 미리 보기 기능을 사용할 수 있는 사용자가** **없음**으로 설정 된 경우이 옵션은 테 넌 트에서 제거 됩니다.

이전에 사용자의 이전에 결합 된 MFA/SSPR 등록 미리 보기 환경을 설정 했는지 여부에 관계 없이 이전 환경은 이후 날짜에 해제 됩니다. 따라서 가능한 한 빨리 새로운 고급 환경으로 전환 하는 것이 좋습니다.

향상 된 등록 환경에 대 한 자세한 내용은 [AZURE AD 결합 MFA 및 암호 재설정 등록 환경에 대 한 유용한 기능](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Cool-enhancements-to-the-Azure-AD-combined-MFA-and-password/ba-p/354271)을 참조 하세요.

---

### <a name="updated-policy-management-experience-for-user-flows"></a>사용자 흐름의 업데이트된 정책 관리 환경

**유형:** 변경된 기능  
**서비스 범주:** B2C - 소비자 ID 관리  
**제품 기능:** B2B/B2C

사용자 흐름 (이전의 기본 제공 정책)에 대 한 정책 만들기 및 관리 프로세스를 보다 쉽게 업데이트 했습니다. 이 새로운 환경은 이제 모든 Azure AD 테 넌 트에 대 한 기본값입니다.

포털 화면 위쪽의 **사용자 의견 보내기** 영역에서 웃는 얼굴 또는 찡그린 얼굴 보내기 아이콘을 사용 하 여 추가 피드백과 제안을 제공할 수 있습니다.

새 정책 관리 환경에 대 한 자세한 내용은 [이제 JavaScript 사용자 지정을 포함 하는 Azure AD B2C 및 더 많은 새 기능](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595) 블로그를 참조 하세요.

---

### <a name="choose-specific-page-element-versions-provided-by-azure-ad-b2c"></a>Azure AD B2C에서 제공하는 특정 페이지 요소 버전 선택

**유형:** 새 기능  
**서비스 범주:** B2C - 소비자 ID 관리  
**제품 기능:** B2B/B2C

이제 Azure AD B2C에서 제공 하는 특정 버전의 페이지 요소를 선택할 수 있습니다. 특정 버전을 선택 하 여 페이지에 표시 되기 전에 업데이트를 테스트 하 고 예측 가능한 동작을 얻을 수 있습니다. 또한 이제 특정 페이지 버전을 적용 하 여 JavaScript 사용자 지정을 허용 하도록 선택할 수 있습니다. 이 기능을 켜려면 사용자 흐름의 **속성** 페이지로 이동 합니다.

특정 버전의 페이지 요소를 선택 하는 방법에 대 한 자세한 내용은 [이제 JavaScript 사용자 지정 Azure AD B2C 및 더 많은 새 기능](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595) 블로그를 참조 하세요.

---

### <a name="configurable-end-user-password-requirements-for-b2c-ga"></a>B2C를 위한 구성 가능한 최종 사용자 암호 요구 사항(GA)

**유형:** 새 기능  
**서비스 범주:** B2C - 소비자 ID 관리  
**제품 기능:** B2B/B2C

이제 기본 Azure AD 암호 정책을 사용 하지 않고 최종 사용자에 대 한 조직의 암호 복잡성을 설정할 수 있습니다. 사용자 흐름의 **속성** 블레이드 (이전에 기본 제공 정책 이라고 함)에서 **단순** 또는 **강력한**암호 복잡성을 선택 하거나 **사용자 지정** 요구 사항 집합을 만들 수 있습니다.

암호 복잡성 요구 사항 구성에 대 한 자세한 내용은 [Azure Active Directory B2C의 암호 복잡성 요구 사항 구성](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-password-complexity)을 참조 하세요.

---

### <a name="new-default-templates-for-custom-branded-authentication-experiences"></a>사용자 지정 브랜드 인증 환경을 위한 새로운 기본 템플릿

**유형:** 새 기능  
**서비스 범주:** B2C - 소비자 ID 관리  
**제품 기능:** B2B/B2C

사용자 흐름의 **페이지 레이아웃** 블레이드 (이전의 기본 제공 정책)에 있는 새 기본 템플릿을 사용 하 여 사용자에 대 한 사용자 지정 브랜드 인증 환경을 만들 수 있습니다.

템플릿을 사용 하는 방법에 대 한 자세한 내용은 [이제 JavaScript 사용자 지정 및 더 많은 새 기능을 Azure AD B2C](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595)를 참조 하세요.

---
