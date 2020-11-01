---
title: 새로운 기능은 무엇입니까? 릴리스 정보 - Azure Active Directory | Microsoft Docs
description: Azure Active Directory의 새로운 기능에 대해 알아봅니다. 최신 릴리스 정보, 알려진 문제, 버그 수정, 사용 되지 않는 기능 및 예정 된 변경 사항 등이 있습니다.
services: active-directory
author: ajburnle
manager: daveba
featureFlags:
- clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 10/30/2020
ms.author: ajburnle
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 74cb1e021049b7c36c4c23f2e9a6a7512fba1110
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/01/2020
ms.locfileid: "93146140"
---
# <a name="whats-new-in-azure-active-directory"></a>Azure Active Directory의 새로운 기능

>이 URL `https://docs.microsoft.com/api/search/rss?search=%22Release+notes+-+Azure+Active+Directory%22&locale=en-us`를 ![RSS 피드 판독기 아이콘](./media/whats-new/feed-icon-16x16.png) 피드 판독기에 복사하고 붙어넣어 업데이트를 위해 이 페이지를 다시 방문해야 할 때 알림을 받습니다.

Azure AD는 지속적인 향상되고 있습니다. 최신 개발 정보를 확인할 수 있도록 이 문서에서는 다음에 대한 정보를 제공합니다.

- 최신 릴리스
- 알려진 문제
- 버그 수정
- 사용되지 않는 기능
- 변경 계획

이 페이지는 매월 업데이트되므로 정기적으로 다시 방문해 주세요. 6 개월 보다 오래 된 항목을 찾는 경우 [Azure Active Directory의 새로운 기능에 대 한 보관](whats-new-archive.md)에서 찾을 수 있습니다.

---
## <a name="october-2020"></a>2020년 10월

### <a name="azure-ad-on-premises-hybrid-agents-impacted-by-azure-tls-certificate-changes"></a>Azure TLS 인증서 변경의 영향을 받는 azure AD 온-프레미스 하이브리드 에이전트

**유형:** 변경 계획  
**서비스 범주:** 해당 없음  
**제품 기능:** 플랫폼

Microsoft는 다른 루트 CA(인증 기관)의 TLS 인증서를 사용하도록 Azure 서비스를 업데이트하 고 있습니다. 이 업데이트는 CA/브라우저 포럼 기준 요구 사항 중 하나를 준수 하지 않는 현재 CA 인증서로 인해 발생 합니다. 이 변경 내용은 고정 된 루트 인증서 목록과 함께 확정 된 환경을 갖춘 온-프레미스에 설치 된 Azure AD 하이브리드 에이전트에 영향을 주므로 새 인증서 발급자를 신뢰 하도록 업데이트 해야 합니다.

이렇게 변경 하면 즉시 조치를 취하지 않는 경우 서비스가 중단 됩니다. 이러한 에이전트에는 온-프레미스에 대 한 원격 액세스를 위한 [응용 프로그램 프록시 커넥터](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AppProxy) , 사용자가 동일한 암호를 사용 하 여 응용 프로그램에 로그인 할 수 있도록 하는 [통과 인증](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AzureADConnect) 에이전트 및 AZURE ad sync에 ad를 수행 하는 [클라우드 프로 비전 미리 보기](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AzureADConnect) 에이전트가 포함 됩니다. 

특정 CRL (인증서 해지 목록) 다운로드에 대 한 아웃 바운드 호출을 허용 하도록 방화벽 규칙이 설정 된 환경이 있는 경우 다음 CRL 및 OCSP Url을 허용 해야 합니다. 에 대 한 액세스를 사용 하도록 설정 하는 변경 내용 및 CRL 및 OCSP Url에 대 한 자세한 내용은  [AZURE TLS 인증서 변경 내용](../../security/fundamentals/tls-certificate-changes.md)을 참조 하세요.

---

### <a name="provisioning-events-will-be-removed-from-audit-logs-and-published-solely-to-provisioning-logs"></a>프로 비전 이벤트는 감사 로그에서 제거 되 고 프로 비전 로그에만 게시 됩니다.

**유형:** 변경 계획  
**서비스 범주:** 보고  
**제품 기능:** 모니터링 및 보고
 
SCIM [프로 비전 서비스](../app-provisioning/user-provisioning.md) 에의 한 활동은 감사 로그와 프로 비전 로그 모두에 로깅됩니다. 여기에는 ServiceNow의 사용자 만들기, GSuite의 그룹 또는 AWS에서 역할 가져오기 등의 작업이 포함 됩니다. 향후 이러한 이벤트는 프로 비전 로그에만 게시 됩니다. 이 변경은 로그에서 중복 이벤트를 방지 하기 위해 구현 되며 log analytics에서 로그를 사용 하는 고객에 의해 발생 하는 추가 비용이 발생 하지 않도록 합니다. 

날짜가 완료 되 면 업데이트를 제공 합니다. 이 사용 중단은 2020 년에 계획 되지 않습니다. 

> [!NOTE]
> 이는 프로 비전 서비스에서 내보낸 동기화 이벤트 외부의 감사 로그에 있는 이벤트에는 영향을 주지 않습니다. 응용 프로그램 생성, 조건부 액세스 정책, 디렉터리의 사용자 등의 이벤트는 감사 로그에서 계속 내보내집니다. [자세한 정보를 알아보세요](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs?context=azure/active-directory/app-provisioning/context/app-provisioning-context).
 

---

### <a name="azure-ad-on-premises-hybrid-agents-impacted-by-azure-transport-layer-security-tls-certificate-changes"></a>Azure AD 온-프레미스 하이브리드 에이전트가 Azure TLS (전송 계층 보안) 인증서 변경의 영향을 받습니다.

**유형:** 변경 계획  
**서비스 범주:** 해당 없음  
**제품 기능:** 플랫폼
 
Microsoft는 다른 루트 CA(인증 기관)의 TLS 인증서를 사용하도록 Azure 서비스를 업데이트하 고 있습니다. CA/브라우저 포럼 기준 요구 사항 중 하나를 따르지 않는 현재 CA 인증서 때문에 업데이트가 수행 됩니다. 이 변경 내용은 고정 된 루트 인증서 목록을 사용 하 여 확정 된 환경을 보유 하 고 있는 온-프레미스에 설치 된 Azure AD 하이브리드 에이전트에 영향을 줍니다. 이러한 에이전트는 새 인증서 발급자를 신뢰 하도록 업데이트 해야 합니다.

이렇게 변경 하면 즉시 조치를 취하지 않는 경우 서비스가 중단 됩니다. 이러한 에이전트는 다음과 같습니다. 
- 온-프레미스에 대 한 원격 액세스를 위한 [응용 프로그램 프록시 커넥터](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AppProxy) 
- 사용자가 동일한 암호를 사용 하 여 응용 프로그램에 로그인 할 수 있도록 하는 [통과 인증](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AzureADConnect) 에이전트
- Azure AD sync에 AD를 수행 하는 [클라우드 프로 비전 미리 보기](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AzureADConnect) 에이전트. 

특정 CRL (인증서 해지 목록) 다운로드에 대 한 아웃 바운드 호출을 허용 하도록 방화벽 규칙이 설정 된 환경이 있는 경우 CRL 및 OCSP Url을 허용 해야 합니다. 에 대 한 액세스를 사용 하도록 설정 하는 변경 내용 및 CRL 및 OCSP Url에 대 한 자세한 내용은  [AZURE TLS 인증서 변경 내용](../../security/fundamentals/tls-certificate-changes.md)을 참조 하세요.
 
---

### <a name="azure-active-directory-tls-10-tls-11-and-3des-deprecation-in-us-gov-cloud"></a>US Gov 클라우드에서 Azure Active Directory TLS 1.0, TLS 1.1 및 3DES 사용 중단

**유형:** 변경 계획  
**서비스 범주:** 해당 없음  
**제품 기능:** 기준
 
Azure Active Directory는 2021 년 3 월 31 일까 지 다음 프로토콜을 사용 중단 합니다.
- TLS 1.0
- TLS 1.1
- 3DES 암호 그룹 (TLS_RSA_WITH_3DES_EDE_CBC_SHA)

모든 클라이언트-서버 및 브라우저 서버 조합에서는 TLS 1.2 및 최신 암호 그룹을 사용 하 여 Azure, Office 365 및 Microsoft 365 서비스의 Azure Active Directory에 대 한 보안 연결을 유지 해야 합니다.

영향을 받는 환경은 다음과 같습니다.
- Azure US Gov
- [Office 365 GCC High & DoD](https://docs.microsoft.com/microsoft-365/compliance/tls-1-2-in-office-365-gcc?view=o365-worldwide)
 
---

### <a name="assign-applications-to-roles-on-au-and-object-scope"></a>AU 및 개체 범위에서 역할에 응용 프로그램 할당

**유형:** 새로운 기능  
**서비스 범주:** RBAC  
**제품 기능:** 액세스 제어
 
이 기능을 사용 하면 관리 단위 범위에 대 한 관리자 역할에 응용 프로그램 (SPN)을 할당할 수 있습니다. 자세한 내용은 [관리 단위에 범위 지정 역할 할당](../roles/admin-units-assign-roles.md)을 참조 하세요.

---

### <a name="now-you-can-disable-and-delete-guest-users-when-theyre-denied-access-to-a-resource"></a>이제 리소스에 대 한 액세스가 거부 된 경우 게스트 사용자를 사용 하지 않도록 설정 하 고 삭제할 수 있습니다.

**유형:** 새로운 기능  
**서비스 범주:** 액세스 검토  
**제품 기능:** Id 거 버 넌 스
 
비활성화 및 삭제는 조직이 그룹 및 앱에서 외부 게스트를 더 효율적으로 관리할 수 있도록 Azure AD 액세스 검토의 고급 제어입니다. 액세스 검토에서 게스트가 거부 된 경우 **disable 및 delete** 는 30 일 동안 로그인 하지 못하도록 자동으로 차단 합니다. 30 일 후에는 테 넌 트에서 완전히 제거 됩니다.

이 기능에 대 한 자세한 내용은 [AZURE AD 액세스 검토를 사용 하 여 외부 Id 비활성화 및 삭제 (미리 보기)](../governance/access-reviews-external-users.md#disable-and-delete-external-identities-with-azure-ad-access-reviews-preview)를 참조 하세요.
 
---

### <a name="access-review-creators-can-add-custom-messages-in-emails-to-reviewers"></a>액세스 검토 작성자는 전자 메일의 사용자 지정 메시지를 검토자에 게 추가할 수 있습니다.

**유형:** 새로운 기능  
**서비스 범주:** 액세스 검토  
**제품 기능:** Id 거 버 넌 스
 
Azure AD 액세스 검토에서 검토를 만드는 관리자는 이제 검토자에 게 사용자 지정 메시지를 작성할 수 있습니다. 검토자는 검토를 완료 하 라는 메시지를 받은 전자 메일에 메시지를 표시 합니다. 이 기능을 사용 하는 방법에 대해 자세히 알아보려면 [고급 설정](../governance/create-access-review.md#advanced-settings) 섹션의 6 단계를 참조 하세요.

---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---october-2020"></a>Azure AD 응용 프로그램 갤러리의 새로운 프로 비전 커넥터-10 월 2020

**유형:** 새로운 기능  
**서비스 범주:** 앱 프로비전  
**제품 기능:** 타사 통합
 
다음과 같은 통합된 새 앱에 대한 사용자 계정을 만들고, 업데이트하고, 삭제하는 작업을 자동화할 수 있습니다.

- [Apple Business Manager](../saas-apps/apple-business-manager-provision-tutorial.md)
- [Apple School Manager](../saas-apps/apple-school-manager-provision-tutorial.md)
- [Code42](../saas-apps/code42-provisioning-tutorial.md)
- [AlertMedia](../saas-apps/alertmedia-provisioning-tutorial.md)
- [OpenText Directory Services](../saas-apps/open-text-directory-services-provisioning-tutorial.md)
- [Cinode](../saas-apps/cinode-provisioning-tutorial.md)
- [글로벌 릴레이 ID 동기화](../saas-apps/global-relay-identity-sync-provisioning-tutorial.md)

자동화된 사용자 계정 프로비저닝을 사용하여 조직의 보안을 강화하는 방법에 대한 자세한 내용은 [Azure AD를 사용하여 SaaS 애플리케이션에 대한 사용자 프로비저닝 자동화](../app-provisioning/user-provisioning.md)를 참조하세요.
 
---

### <a name="integration-assistant-for-azure-ad-b2c"></a>Azure AD B2C 용 Integration assistant

**유형:** 새로운 기능  
**서비스 범주:** B2C - 소비자 ID 관리  
**제품 기능:** B2B/B2C
 
이제 Azure AD B2C 앱 등록에서 Integration Assistant (미리 보기) 환경을 사용할 수 있습니다. 이 환경에서는 일반적인 시나리오에 맞게 응용 프로그램을 구성 하는 방법을 안내 합니다. [Microsoft id 플랫폼 모범 사례 및 권장 사항](../develop/identity-platform-integration-checklist.md)에 대해 자세히 알아보세요.
 
---

### <a name="view-role-template-id-in-azure-portal-ui"></a>Azure Portal UI에서 역할 템플릿 ID 보기

**유형:** 새로운 기능  
**서비스 범주:** Azure 역할  
**제품 기능:** 액세스 제어
 

이제 Azure Portal에서 각 Azure AD 역할의 템플릿 ID를 볼 수 있습니다. Azure AD에서 선택한 역할에 대 한  **설명** 을 선택 합니다. 

고객은 표시 이름 대신 PowerShell 스크립트 및 코드에서 역할 템플릿 Id를 사용 하는 것이 좋습니다. 역할 템플릿 ID는 역할 및 [Roledefinition](https://docs.microsoft.com/graph/api/resources/unifiedroledefinition?view=graph-rest-beta) 개체를 [directoryroles](https://docs.microsoft.com/graph/api/resources/directoryrole?view=graph-rest-1.0) 에 사용할 수 있도록 지원 됩니다. 역할 템플릿 Id에 대 한 자세한 내용은 [역할 템플릿 id](../roles/permissions-reference.md#role-template-ids)를 참조 하십시오.

---

### <a name="api-connectors-for-azure-ad-b2c-sign-up-user-flows-is-now-in-public-preview"></a>Azure AD B2C 등록 사용자 흐름에 대 한 API 커넥터는 현재 공개 미리 보기로 제공 됩니다.

**유형:** 새로운 기능  
**서비스 범주:** B2C - 소비자 ID 관리  
**제품 기능:** B2B/B2C
 

이제 API 커넥터를 Azure Active Directory B2C 사용할 수 있습니다. API 커넥터를 사용 하면 웹 Api를 사용 하 여 등록 사용자 흐름을 사용자 지정 하 고 외부 클라우드 시스템과 통합할 수 있습니다. API 커넥터를 사용 하 여 다음을 수행할 수 있습니다.

- 사용자 지정 승인 워크플로와 통합
- 사용자 입력 데이터 유효성 검사
- 사용자 특성 덮어쓰기 
- 사용자 지정 비즈니스 논리 실행 

 [API 커넥터를 사용 하 여 등록 설명서를 사용자 지정 하 고 확장](../../active-directory-b2c/api-connectors-overview.md) 하 여 자세한 내용을 알아보세요.

---

### <a name="state-property-for-connected-organizations-in-entitlement-management"></a>자격 관리에서 연결 된 조직의 상태 속성

**유형:** 새로운 기능  
**서비스 범주:** 디렉터리 관리 **제품 기능:** 자격 관리
 

 이제 연결 된 모든 조직에는 "State" 라는 추가 속성이 있습니다. 상태는 "모든 구성 된 연결 된 조직"을 참조 하는 정책에서 연결 된 조직을 사용 하는 방법을 제어 합니다. 이 값은 "구성" (조직이 "all" 절을 사용 하는 정책 범위에 있음) 또는 "제안 됨" (조직이 범위에 있지 않음을 의미 함)입니다.  

수동으로 만든 연결 된 조직의 기본 설정은 "구성 됨"입니다. 한편, 인터넷의 모든 사용자가 액세스를 요청 하도록 허용 하는 정책을 통해 생성 된 자동으로 생성 된 항목은 기본적으로 "제안 됨"으로 나타납니다.  9 월 9 2020 일 이전에 생성 된 모든 연결 된 조직은 "구성 됨"으로 설정 됩니다. 관리자는 필요에 따라이 속성을 업데이트할 수 있습니다. [자세한 정보를 알아보세요](../governance/entitlement-management-organization.md#managing-a-connected-organization-programmatically).
 

---

### <a name="azure-active-directory-external-identities-now-has-premium-advanced-security-settings-for-b2c"></a>이제 Azure Active Directory 외부 Id에 B2C에 대 한 프리미엄 고급 보안 설정이 있습니다.

**유형:** 새로운 기능  
**서비스 범주:** B2C - 소비자 ID 관리  
**제품 기능:** B2B/B2C
 
Id 보호의 위험 기반 조건부 액세스 및 위험 검색 기능을 이제 [Azure AD B2C](../..//active-directory-b2c/conditional-access-identity-protection-overview.md)에서 사용할 수 있습니다. 이제 고객은 다음과 같은 고급 보안 기능을 사용 하 여 다음을 수행할 수 있습니다.
- Intelligent insights를 활용 하 여 B2C apps 및 최종 사용자 계정으로 위험을 평가 합니다. 검색에는 비정상적인 이동, 익명 IP 주소, 맬웨어 연결 IP 주소 및 Azure AD 위협 인텔리전스가 포함 됩니다. 포털 및 API 기반 보고서를 사용할 수도 있습니다.
- B2C 사용자에 대 한 적응 인증 정책을 구성 하 여 위험을 자동으로 해결 합니다. 앱 개발자와 관리자는 검색 된 사용자 위험 수준에 따라 MFA (multi-factor authentication)를 요구 하거나 액세스를 차단 하 여 실시간 위험을 완화 하 고, 위치, 그룹 및 앱을 기반으로 하 여 추가 제어를 사용할 수 있습니다.
- Azure AD B2C 사용자 흐름 및 사용자 지정 정책과 통합 합니다. Azure AD B2C의 기본 제공 사용자 흐름에서 조건을 트리거하거나 B2C 사용자 지정 정책에 통합할 수 있습니다. B2C 사용자 흐름의 다른 측면에서와 마찬가지로 최종 사용자 환경 메시지를 사용자 지정할 수 있습니다. 사용자 지정은 조직의 음성, 브랜드 및 완화 대안에 따라 지정 됩니다.
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---october-2020"></a>Azure AD 응용 프로그램 갤러리에서 사용할 수 있는 새로운 페더레이션된 앱-10 월 2020

**유형:** 새로운 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능:** 타사 통합
 
2020 년 10 월에 앱 갤러리에 페더레이션 지원과 함께 다음 27 개의 새 응용 프로그램을 추가 했습니다.

[Sentry](../saas-apps/sentry-tutorial.md), [Bumblebee-생산성 슈퍼 앱](https://app.yellowmessenger.com/user/login), [ABBYY FlexiCapture cloud](../saas-apps/abbyy-flexicapture-cloud-tutorial.md), [EAComposer](../saas-apps/eacomposer-tutorial.md), [genesys Azure에 대 한 클라우드 통합](https://apps.mypurecloud.com/msteams-integration/), [영역 기술 포털](https://portail.zonetechnologie.com/signin), [Beautiful.ai](../saas-apps/beautiful.ai-tutorial.md), [Datawiza Access Broker](https://console.datawiza.com/), [zokri](https://app.zokri.com/), [checkproof](../saas-apps/checkproof-tutorial.md), [Ecochallenge.org](https://events.ecochallenge.org/users/login), [atspoke](http://atspoke.com/login), [약속 미리 알림](https://app.appointmentreminder.co.nz/account/login), [Cloud. 시장](https://cloud.market/), [TravelPerk](../saas-apps/travelperk-tutorial.md), [Greetly](https://app.greetly.com/), [OrgVitality SSO} (.. /saas-apps/orgvitality-sso-tutorial.md), [웹 Cargo Air](../saas-apps/web-cargo-air-tutorial.md), [루프 흐름 CRM](../saas-apps/loop-flow-crm-tutorial.md), [starmind](../saas-apps/starmind-tutorial.md), [워크](https://hrm.workstem.com/login), [소매 Zipline](../saas-apps/retail-zipline-tutorial.md), [hoxhunt](../saas-apps/hoxhunt-tutorial.md), [mevisio](../saas-apps/mevisio-tutorial.md), [Samsara](../saas-apps/samsara-tutorial.md), [Nimbus](../saas-apps/nimbus-tutorial.md), [Pulse Secure 가상 Traffic Manager](https://docs.microsoft.com/azure/active-directory/saas-apps/pulse-secure-virtual-traffic-manager-tutorial)

여기에서 모든 응용 프로그램의 설명서를 찾을 수도 있습니다. https://aka.ms/AppsTutorial

Azure AD 앱 갤러리에서 응용 프로그램을 나열 하려면 여기에서 세부 정보를 읽으십시오. https://aka.ms/AzureADAppRequest

---

### <a name="provisioning-logs-can-now-be-streamed-to-log-analytics"></a>이제 프로 비전 로그를 log analytics로 스트리밍할 수 있습니다.

**유형:** 새로운 기능  
**서비스 범주:** 보고  
**제품 기능:** 모니터링 및 보고
 

프로 비전 로그를 log analytics에 게시 하 여 다음을 수행 합니다.
- 30 일 넘게 프로 비전 로그 저장
- 사용자 지정 경고 및 알림 정의
- 로그를 시각화 하는 대시보드 빌드
- 복잡 한 쿼리를 실행 하 여 로그 분석 

기능을 사용 하는 방법을 알아보려면 [프로 비전이 Azure Monitor 로그와 통합 하는 방법 이해](../app-provisioning/application-provisioning-log-analytics.md)를 참조 하세요.
 
---

### <a name="provisioning-logs-can-now-be-viewed-by-application-owners"></a>이제 응용 프로그램 소유자가 프로 비전 로그를 볼 수 있습니다.

**유형:** 변경된 기능  
**서비스 범주:** 보고  
**제품 기능:** 모니터링 및 보고
 
이제 응용 프로그램 소유자가 프로 비전 서비스에서 작업을 모니터링 하 고, 권한 있는 역할을 제공 하거나 병목 상태를 제공 하지 않고 문제를 해결할 수 있습니다. [자세한 정보를 알아보세요](../reports-monitoring/concept-provisioning-logs.md).
 
---

### <a name="renaming-10-azure-active-directory-roles"></a>10 개의 Azure Active Directory 역할 이름 바꾸기

**유형:** 변경된 기능  
**서비스 범주:** Azure 역할  
**제품 기능:** 액세스 제어
 
일부 AD (Azure Active Directory) 기본 제공 역할에는 Microsoft 365 관리 센터, Azure AD 포털 및 Microsoft Graph에 표시 되는 이름과 다른 이름이 있습니다. 이러한 불일치는 자동화 된 프로세스에서 문제를 일으킬 수 있습니다. 이 업데이트를 사용 하면 10 개의 역할 이름 이름을 바꿔서 일관 되도록 합니다. 다음 표에는 새 역할 이름이 있습니다.

![새 역할 이름 표](media/whats-new/azure-role.png)

---

### <a name="azure-ad-b2c-support-for-auth-code-flow-for-spas-using-msal-js-2x"></a>MSAL JS 2.x를 사용 하는 것 처럼 SPAs 인증 코드 흐름에 대 한 지원 Azure AD B2C

**유형:** 변경된 기능  
**서비스 범주:** B2C - 소비자 ID 관리  
**제품 기능:** B2B/B2C
 
MSAL.js 버전 2.x에는 이제 SPAs (단일 페이지 웹 앱)에 대 한 인증 코드 흐름 지원이 포함 됩니다. Azure AD B2C는 이제 Azure Portal에서 SPA 앱 유형 사용을 지원 하 고 단일 페이지 앱에 대해 PKCE에서 MSAL.js 인증 코드 흐름을 사용할 수 있습니다. 이렇게 하면 Azure AD B2C를 사용 하 여 최신 브라우저로 SSO를 유지 하 고 최신 인증 프로토콜 권장 사항을 준수 하는 데 사용할 수 있습니다. [Azure Active Directory B2C 자습서의 SPA (단일 페이지 응용 프로그램) 등록](../../active-directory-b2c/tutorial-register-spa.md) 을 시작 하세요.

---

### <a name="updates-to-remember-multi-factor-authentication-mfa-on-a-trusted-device-setting"></a>신뢰할 수 있는 장치 설정에서 Multi-Factor Authentication (MFA)를 기억할 업데이트

**유형:** 변경된 기능  
**서비스 범주:** MFA  
**제품 기능:** Id 보안 & 보호
 

최근 최대 365 일 동안 인증을 확장 하도록 신뢰할 수 있는 장치 기능에서 [MFA (기억을 Multi-Factor Authentication](../authentication/howto-mfa-mfasettings.md#remember-multi-factor-authentication) )를 업데이트 했습니다. Azure AD (Azure Active Directory) 프리미엄 라이선스는 재인증 설정에 더 많은 유연성을 제공 하는 [조건부 액세스 – 로그인 빈도 정책을](../conditional-access/howto-conditional-access-session-lifetime.md#user-sign-in-frequency) 사용할 수도 있습니다.

최적의 사용자 환경에서는 신뢰할 수 있는 장치에 대 한 MFA 기억을 설정 하는 대신 조건부 액세스 로그인 빈도를 사용 하 여 신뢰할 수 있는 장치, 위치 또는 낮은 위험 세션에서 세션 수명을 연장 하는 것이 좋습니다. 시작 하려면 [재인증 환경 최적화에 대 한 최신 지침](../authentication/concepts-azure-multi-factor-authentication-prompts-session-lifetime.md)을 검토 하세요.

---

## <a name="september-2020"></a>2020년 9월

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---september-2020"></a>Azure AD 응용 프로그램 갤러리의 새로운 프로 비전 커넥터-9 월 2020

**유형:** 새로운 기능  
**서비스 범주:** 앱 프로비전  
**제품 기능:** 타사 통합
 
다음과 같은 통합된 새 앱에 대한 사용자 계정을 만들고, 업데이트하고, 삭제하는 작업을 자동화할 수 있습니다.

- [Coda](../saas-apps/coda-provisioning-tutorial.md)
- [Cofense 수신자 동기화](../saas-apps/cofense-provision-tutorial.md)
- [InVision](../saas-apps/invision-provisioning-tutorial.md)
- [myday](../saas-apps/myday-provision-tutorial.md)
- [SAP Analytics Cloud](../saas-apps/sap-analytics-cloud-provisioning-tutorial.md)
- [Webroot 보안 인식](../saas-apps/webroot-security-awareness-training-provisioning-tutorial.md)

자동화된 사용자 계정 프로비저닝을 사용하여 조직의 보안을 강화하는 방법에 대한 자세한 내용은 [Azure AD를 사용하여 SaaS 애플리케이션에 대한 사용자 프로비저닝 자동화](../app-provisioning/user-provisioning.md)를 참조하세요.
 
---
### <a name="cloud-provisioning-public-preview-refresh"></a>클라우드 프로 비전 공개 미리 보기 새로 고침

**유형:** 새로운 기능  
**서비스 범주:** Azure AD 클라우드 프로 비전 **제품 기능:** Id 수명 주기 관리
 
Azure AD Connect 클라우드 프로 비전 공개 미리 보기 새로 고침 기능을 통해 사용자 의견에서 개발 된 두 가지 주요 향상 기능 

- Azure Portal를 통한 특성 매핑 환경

    IT 관리자는이 기능을 통해 현재 제공 되는 다양 한 매핑 유형을 사용 하 여 AD의 사용자, 그룹 또는 연락처 특성을 Azure AD에 매핑할 수 있습니다. 특성 매핑은 Active Directory에서 Azure Active Directory로 전달 되는 특성의 값을 표준화 하는 데 사용 되는 기능입니다. AD에서 Azure AD로 특성 값을 직접 매핑할지 또는 식을 사용 하 여 사용자를 프로 비전 할 때 특성 값을 변환할 수 있는지 여부를 결정할 수 있습니다. [자세한 정보](../cloud-provisioning/how-to-attribute-mapping.md)

- 주문형 프로 비전 또는 테스트 사용자 환경

    구성을 설정한 후에는 범위 내 모든 사용자에 게 적용 하기 전에 사용자 변환이 예상 대로 작동 하는지 테스트 하는 것이 좋습니다. 주문형 프로 비전을 사용 하 여 IT 관리자는 AD 사용자의 DN (고유 이름)을 입력 하 고 예상 대로 동기화 되는지 확인할 수 있습니다. 주문형 프로 비전을 사용 하면 이전에 수행한 특성 매핑이 예상 대로 작동 하는지 확인할 수 있습니다. [자세한 내용](../cloud-provisioning/how-to-on-demand-provision.md)
 
---

### <a name="audited-bitlocker-recovery-in-azure-ad---public-preview"></a>Azure AD에서 감사 되는 BitLocker 복구-공개 미리 보기

**유형:** 새로운 기능  
**서비스 범주:** 장치 액세스 관리  
**제품 기능:** 장치 수명 주기 관리
 
IT 관리자 또는 최종 사용자가 액세스할 수 있는 BitLocker 복구 키를 읽으면 Azure Active Directory은 이제 복구 키에 액세스 한 사용자를 캡처하는 감사 로그를 생성 합니다. 동일한 감사는 BitLocker 키가 연결 된 장치에 대 한 세부 정보를 제공 합니다.

최종 사용자는 [내 계정을 통해 복구 키에 액세스할](../user-help/my-account-portal-devices-page.md#view-a-bitlocker-key)수 있습니다. IT 관리자는 [베타의 BitLocker 복구 키 API](/graph/api/resources/bitlockerrecoverykey?view=graph-rest-beta) 또는 Azure AD 포털을 통해 복구 키에 액세스할 수 있습니다. 자세히 알아보려면 [AZURE AD 포털에서 BitLocker 키 보기 또는 복사](../devices/device-management-azure-portal.md#view-or-copy-bitlocker-keys)를 참조 하세요.

---

### <a name="teams-devices-administrator-built-in-role"></a>팀 장치 관리자 기본 제공 역할

**유형:** 새로운 기능  
**서비스 범주:** RBAC  
**제품 기능:** 액세스 제어
 
[팀 장치 관리자](../roles/permissions-reference.md#teams-devices-administrator) 역할을 가진 사용자는 팀 관리 센터에서 [팀 인증 장치](https://www.microsoft.com/microsoft-365/microsoft-teams/across-devices/devices) 를 관리할 수 있습니다. 

이 역할을 통해 사용자는 장치를 검색 하 고 필터링 할 수 있는 기능을 사용 하 여 모든 장치를 한 눈에 볼 수 있습니다. 또한 사용자는 로그인 한 계정 및 장치의 제조업체 및 모델을 포함 하 여 각 장치에 대 한 세부 정보를 확인할 수 있습니다. 사용자는 장치에서 설정을 변경 하 고 소프트웨어 버전을 업데이트할 수 있습니다. 이 역할은 팀 활동을 확인 하 고 장치의 품질을 호출할 수 있는 권한을 부여 하지 않습니다.
 
---

### <a name="advanced-query-capabilities-for-directory-objects"></a>디렉터리 개체에 대 한 고급 쿼리 기능

**유형:** 새로운 기능  
**서비스 범주:** MS 그래프  
**제품 기능:** 개발자 환경
 
이제 Azure AD Api의 디렉터리 개체에 대해 도입 된 모든 새 쿼리 기능을 v 1.0 끝점에서 사용할 수 있으며 프로덕션이 준비 됩니다. 개발자는 표준 OData 연산자를 사용 하 여 디렉터리 개체 및 관련 링크를 계산, 검색, 필터링 및 정렬할 수 있습니다.

자세한 내용은 [여기](https://aka.ms/BlogPostMezzoGA)에서 설명서를 참조 하 고,이 [간단한 설문 조사](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR_yN8EPoGo5OpR1hgmCp1XxUMENJRkNQTk5RQkpWTE44NEk2U0RIV0VZRy4u)를 통해 사용자 의견을 보낼 수도 있습니다.
 
---

### <a name="public-preview-continuous-access-evaluation-for-tenants-who-configured-conditional-access-policies"></a>공개 미리 보기: 조건부 액세스 정책을 구성 하는 테 넌 트에 대 한 지속적인 액세스 평가

**유형:** 새로운 기능  
**서비스 범주:** 인증 (로그인)  
**제품 기능:** Id 보안 & 보호
 
이제 조건부 액세스 정책을 사용 하 여 Azure AD 테 넌 트에 대 한 공개 미리 보기에서 CAE (연속 액세스 평가)를 사용할 수 있습니다. CAE를 사용 하면 중요 한 보안 이벤트와 정책이 실시간으로 평가 됩니다. 계정 사용 안 함, 암호 재설정 및 위치 변경도 포함 됩니다. 자세히 알아보려면 [연속 액세스 평가](../conditional-access/concept-continuous-access-evaluation.md)를 참조 하세요.

---

### <a name="public-preview-ask-users-requesting-an-access-package-additional-questions-to-improve-approval-decisions"></a>공개 미리 보기: 액세스 패키지를 요청 하는 사용자에 게 승인 결정을 개선 하기 위한 추가 질문

**유형:** 새로운 기능  
**서비스 범주:** 사용자 액세스 관리  
**제품 기능:** 자격 관리
 
이제 관리자는 액세스 패키지를 요청 하는 사용자가 Azure AD 자격 관리의 내 액세스 포털에서 비즈니스 근거를 벗어나 추가 질문에 대답할 수 있도록 요구할 수 있습니다. 그러면 사용자의 답변이 승인자에 게 표시 되어 더 정확한 액세스 승인 결정을 내리는 데 도움이 됩니다. 자세히 알아보려면 [승인을 위한 추가 요청자 정보 수집 (미리 보기)](../governance/entitlement-management-access-package-approval-policy.md#collect-additional-requestor-information-for-approval-preview)을 참조 하세요.
 
---

### <a name="public-preview-enhanced-user-management"></a>공개 미리 보기: 향상 된 사용자 관리

**유형:** 새로운 기능  
**서비스 범주:** 사용자 관리  
**제품 기능:** 사용자 관리
 

Azure AD 포털은 모든 사용자 및 삭제 된 사용자 페이지에서 사용자를 쉽게 찾을 수 있도록 업데이트 되었습니다. 미리 보기의 변경 내용은 다음과 같습니다. 
- 개체 ID, 디렉터리 동기화 상태, 생성 유형 및 id 발급자를 비롯 한 더 보이는 사용자 속성
- 이제 검색에서 이름, 전자 메일 및 개체 Id의 결합 된 검색을 허용 합니다.
- 사용자 유형별 고급 필터링 (구성원, 게스트 및 없음), 디렉터리 동기화 상태, 생성 유형, 회사 이름 및 도메인 이름입니다.
- 이름, 사용자 계정 이름 및 삭제 날짜와 같은 속성에 대 한 새로운 정렬 기능.
- 검색 또는 필터를 사용 하 여 업데이트 되는 새 총 사용자 수입니다.

자세한 내용은 [Azure Active Directory의 사용자 관리 향상 기능 (미리 보기)](../enterprise-users/users-search-enhanced.md)을 참조 하세요.

---

### <a name="new-notes-field-for-enterprise-applications"></a>엔터프라이즈 응용 프로그램에 대 한 새 메모 필드

**유형:** 새로운 기능  
**서비스 범주:** Enterprise Apps **제품 기능:** SSO

엔터프라이즈 응용 프로그램에 자유 텍스트 메모를 추가할 수 있습니다. 엔터프라이즈 응용 프로그램에서 응용 프로그램을 관리 하는 데 도움이 되는 관련 정보를 추가할 수 있습니다. 자세한 내용은 [빠른 시작: Azure Active Directory (AZURE AD) 테 넌 트의 응용 프로그램에 대 한 속성 구성](../manage-apps/add-application-portal-configure.md)을 참조 하세요. 

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---september-2020"></a>Azure AD 응용 프로그램 갤러리에서 사용할 수 있는 새로운 페더레이션된 앱-9 월 2020

**유형:** 새로운 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능:** 타사 통합

2020 년 9 월에 앱 갤러리에 페더레이션 지원과 함께 다음 34 새 응용 프로그램을 추가 했습니다.

[VMware 수평 통합 액세스 게이트웨이](), [펄스 보안 pc](../saas-apps/vmware-horizon-unified-access-gateway-tutorial.md), [Inventory360](../saas-apps/pulse-secure-pcs-tutorial.md), [Frontitude](https://services.enteksystems.de/sso/microsoft/signup), [bookwidgets](https://www.bookwidgets.com/sso/office365), [ZVD_Server](https://zaas.zenmutech.com/user/signin), [비즈니스에 대 한 HASHDATA](https://hashdata.app/login.xhtml), [Securelogin](https://securelogin.securelogin.nu/sso/azure/login), [CyberSolutions MAILBASEΣ/Cmss](../saas-apps/cybersolutions-mailbase-tutorial.md), [CyberSolutions CYBERMAILΣ](../saas-apps/cybersolutions-cybermail-tutorial.md), [LimbleCMMS](https://auth.limblecmms.com/), [Glint inc.](../saas-apps/glint-inc-tutorial.md), [zeroheight](../saas-apps/zeroheight-tutorial.md), [성별 적합성](https://app.genderfitness.com/), [Coeo 포털](https://my.coeo.com/), Grammarly [,](../saas-apps/fivetran-tutorial.md) [Fivetran,](../saas-apps/grammarly-tutorial.md) [Kumolus](../saas-apps/kumolus-tutorial.md), [RSA 궁 궁 Suite](../saas-apps/rsa-archer-suite-tutorial.md), [teamzskill](../saas-apps/teamzskill-tutorial.md), [raumfürraum](../saas-apps/raumfurraum-tutorial.md), [Saviynt](../saas-apps/saviynt-tutorial.md), [BizMerlinHR](https://marketplace.bizmerlin.net/bmone/signup), [Mobile 락커](../saas-apps/mobile-locker-tutorial.md), [Zengine](../saas-apps/zengine-tutorial.md), [cloudcadi](https://app.cloudcadi.com/login) [, PeopleSoft](https://simfonianalytics.com/accounts/microsoft/login/),, [개인 Identity & Access Management](https://my.priva.com/), [nitro Pro](https://www.gonitro.com/nps/product-details/downloads), [eventfinity](../saas-apps/eventfinity-tutorial.md), [fexa](../saas-apps/fexa-tutorial.md), 보안 [서명 Enterprise Portal](https://www.securedsigning.com/aad/Auth/ExternalLogin/AdminPortal), [보안 서명 Enterprise Portal AAD 설정](https://www.securedsigning.com/aad/Auth/ExternalLogin/AdminPortal), [wistec Online](https://wisteconline.com/auth/oidc), [Oracle-F5 키로 보호](../saas-apps/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial.md)

에서 모든 응용 프로그램의 설명서를 찾을 수도 있습니다 https://aka.ms/AppsTutorial .

Azure AD 앱 갤러리에서 응용 프로그램을 나열 하려면 여기에서 세부 정보를 참조 https://aka.ms/AzureADAppRequest 하세요.

---

### <a name="new-delegation-role-in-azure-ad-entitlement-management-access-package-assignment-manager"></a>Azure AD 자격 관리의 새 위임 역할: 액세스 패키지 할당 관리자

**유형:** 새로운 기능  
**서비스 범주:** 사용자 액세스 관리  
**제품 기능:** 자격 관리
 
Azure AD 자격 관리에 새 액세스 패키지 할당 관리자 역할이 추가 되어 할당을 관리할 수 있는 세부적인 권한을 제공 합니다. 이제 비즈니스 소유자에 게 액세스 패키지의 할당 관리를 위임할 수 있는 사용자에 게 작업을 위임할 수 있습니다. 그러나 액세스 패키지 할당 관리자는 관리자가 설정한 액세스 패키지 정책 또는 기타 속성을 변경할 수 없습니다. 

이 새로운 역할을 사용 하면 할당 관리를 위임 하 고 다른 모든 액세스 패키지 구성에 대 한 관리 제어를 유지 하는 데 필요한 최소한의 권한을 활용할 수 있습니다. 자세히 알아보려면 [권한 관리 역할](../governance/entitlement-management-delegate.md#entitlement-management-roles)을 참조 하세요.
 
---

### <a name="changes-to-privileged-identity-managements-onboarding-flow"></a>Privileged Identity Management의 온 보 딩 흐름에 대 한 변경 내용

**유형:** 변경된 기능  
**서비스 범주:** Privileged Identity Management  
**제품 기능:** Privileged Identity Management
 
이전에는 PIM (Privileged Identity Management)에 등록 하는 데 Azure MFA에 등록을 포함 하는 PIM의 블레이드에서 사용자 동의 및 온 보 딩 흐름이 필요 합니다. Azure AD 역할 및 관리자 블레이드에 최신 PIM 환경을 통합 하 여이 환경을 제거 하 고 있습니다. 유효한 P2 라이선스가 있는 테 넌 트는 PIM으로 자동 등록 됩니다.

PIM에 등록 하면 테 넌 트에 직접적인 부정적인 영향을 주지 않습니다. 다음과 같이 변경할 수 있습니다.
- PIM 또는 Azure AD 역할 및 관리자 블레이드에서 할당을 수행 하는 경우 활성 및 시작 및 종료 시간과 관련 된 추가 할당 옵션 
- 할당 환경에 직접 도입 된 관리 단위 및 사용자 지정 역할과 같은 추가 범위 지정 메커니즘입니다. 
- 전역 관리자 또는 권한 있는 역할 관리자 인 경우 PIM 주간 다이제스트와 같은 몇 가지 추가 전자 메일을 받을 수 있습니다. 
- 역할 할당과 관련 된 감사 로그에 ms-pim 서비스 사용자가 표시 될 수도 있습니다. 이 예상 변경 내용은 일반 워크플로에 영향을 주지 않습니다.

 자세한 내용은 [Privileged Identity Management 사용 시작](../privileged-identity-management/pim-getting-started.md)을 참조 하세요.

---

### <a name="azure-ad-entitlement-management-the-select-pane-of-access-package-resources-now-shows-by-default-the-resources-currently-in-the-selected-catalog"></a>Azure AD 자격 관리: 이제 액세스 패키지 리소스의 선택 창에는 현재 선택한 카탈로그에 있는 리소스가 기본적으로 표시 됩니다.

**유형:** 변경된 기능  
**서비스 범주:** 사용자 액세스 관리  
**제품 기능:** 자격 관리
 

액세스 패키지 생성 흐름의 리소스 역할 탭에서 창 선택 동작이 변경 됩니다. 현재 기본 동작은 사용자가 소유 하는 모든 리소스와 선택한 카탈로그에 추가 된 리소스를 표시 하는 것입니다. 

이 환경은 사용자가 카탈로그에서 리소스를 쉽게 선택할 수 있도록 기본적으로 카탈로그에 현재 추가 된 리소스만 표시 하도록 변경 됩니다. 업데이트는 액세스 패키지에 추가할 리소스를 검색 하는 데 도움이 되며 카탈로그의 일부가 아닌 사용자가 소유한 리소스를 실수로 추가 하는 위험을 줄일 수 있습니다. 자세히 알아보려면 [AZURE AD 자격 관리에서 새 액세스 패키지 만들기](../governance/entitlement-management-access-package-create.md#resource-roles)를 참조 하세요.
 
---

## <a name="august-2020"></a>2020년 8월 
 
### <a name="updates-to-azure-multi-factor-authentication-server-firewall-requirements"></a>Azure Multi-Factor Authentication 서버 방화벽 요구 사항에 대 한 업데이트

**유형:** 변경 계획  
**서비스 범주:** MFA  
**제품 기능:** Id 보안 & 보호
 
2020 년 10 월 1 일부 터 Azure MFA 서버 방화벽 요구 사항에는 추가 IP 범위가 필요 합니다.

조직에서 아웃 바운드 방화벽 규칙을 사용 하는 경우 MFA 서버가 필요한 모든 IP 범위와 통신할 수 있도록 규칙을 업데이트 합니다. IP 범위는 [Azure Multi-Factor Authentication 서버 방화벽 요구 사항](../authentication/howto-mfaserver-deploy.md#azure-multi-factor-authentication-server-firewall-requirements)에 설명 되어 있습니다.

---

### <a name="upcoming-changes-to-user-experience-in-identity-secure-score"></a>Id 보안 점수의 사용자 환경에 대 한 예정 된 변경 내용

**유형:** 변경 계획  
**서비스 범주:** Id 보호 **제품 기능:** id 보안 & 보호

Microsoft 보안 점수의 [새로운 릴리스에](/microsoft-365/security/mtp/microsoft-secure-score-whats-new?view=o365-worldwide)도입 된 변경 내용에 맞게 Id 보안 점수 포털을 업데이트 하 고 있습니다. 

변경 내용이 포함 된 미리 보기 버전은 9 월부터 사용할 수 있습니다. 미리 보기 버전의 변경 내용은 다음과 같습니다.
- Microsoft 보안 점수를 사용 하 여 브랜드 맞춤을 위해 "id 보안 점수"의 이름을 "Id 보안 점수"로 바꿨습니다.
- 표준 배율으로 정규화 되 고 점이 아닌 백분율로 보고 되는 점수

이 미리 보기에서는 고객이 기존 환경과 새 환경을 전환할 수 있습니다. 이 미리 보기는 11 월 2020이 끝날 때까지 지속 됩니다. 미리 보기 후에는 고객이 자동으로 새로운 UX 환경으로 이동 됩니다.

---

### <a name="new-restricted-guest-access-permissions-in-azure-ad---public-preview"></a>Azure AD에서 제한 된 새 게스트 액세스 권한-공개 미리 보기

**유형:** 새로운 기능  
**서비스 범주:** Access Control   
**제품 기능:** 사용자 관리

게스트 사용자에 대 한 디렉터리 수준 권한을 업데이트 했습니다. 이러한 사용 권한을 통해 관리자는 외부 게스트 사용자 액세스에 대 한 추가 제한 사항 및 제어를 요구할 수 있습니다. 이제 관리자는 외부 게스트의 사용자 및 그룹의 프로필 및 멤버 자격 정보에 대 한 추가 제한을 추가할 수 있습니다. 이 공개 미리 보기 기능을 사용 하면 고객은 게스트 사용자가 자신이 속한 그룹의 멤버 자격을 볼 수 없도록 제한 하는 것을 포함 하 여 난독 처리 그룹 멤버 자격을 통해 규모에 따라 외부 사용자 액세스를 관리할 수 있습니다.

자세히 알아보려면 [제한 된 게스트 액세스 권한](../enterprise-users/users-restrict-guest-permissions.md) 및 [사용자 기본 권한](./users-default-permissions.md)을 참조 하세요.
 
---

### <a name="general-availability-of-delta-queries-for-service-principals"></a>서비스 사용자에 대 한 델타 쿼리의 일반 가용성

**유형:** 새로운 기능  
**서비스 범주:** MS 그래프  
**제품 기능:** 개발자 환경
 
이제 Microsoft Graph 델타 쿼리가 v 1.0의 리소스 형식을 지원 합니다.
- 서비스 주체

이제 클라이언트는 해당 리소스에 대 한 변경 내용을 효율적으로 추적할 수 있으며, 해당 리소스에 대 한 변경 내용을 로컬 데이터 저장소와 동기화 하는 최상의 솔루션을 제공 합니다. 쿼리에서 이러한 리소스를 구성 하는 방법을 알아보려면 [델타 쿼리를 사용 하 여 Microsoft Graph 데이터의 변경 내용 추적](/graph/delta-query-overview)을 참조 하세요.
 
---

### <a name="general-availability-of-delta-queries-for-oauth2permissiongrant"></a>OAuth2PermissionGrant에 대 한 델타 쿼리의 일반 가용성

**유형:** 새로운 기능  
**서비스 범주:** MS 그래프  
**제품 기능:** 개발자 환경

이제 Microsoft Graph 델타 쿼리가 v 1.0의 리소스 형식을 지원 합니다.
- OAuth2PermissionGrant

클라이언트는 이제 해당 리소스에 대 한 변경 내용을 효율적으로 추적할 수 있으며, 로컬 데이터 저장소와 이러한 리소스의 변경 내용을 동기화 하는 최상의 솔루션을 제공 합니다. 쿼리에서 이러한 리소스를 구성 하는 방법을 알아보려면 [델타 쿼리를 사용 하 여 Microsoft Graph 데이터의 변경 내용 추적](/graph/delta-query-overview)을 참조 하세요.

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---august-2020"></a>Azure AD 응용 프로그램 갤러리에서 사용할 수 있는 새로운 페더레이션된 앱-8 월 2020

**유형:** 새로운 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능:** 타사 통합

8 월 2020에 앱 갤러리에 페더레이션 지원이 포함 된 다음과 같은 25 개의 새로운 응용 프로그램이 추가 되었습니다.

[Backup365](https://portal.backup365.io/login), [soapbox](https://app.soapboxhq.com/create?step=auth&provider=azure-ad2-oauth2), [alma SIS](https://almau.getalma.com/), [enlyft Dynamics 365 커넥터](http://enlyft.com/), [serraview 공간 사용률 소프트웨어 솔루션](../saas-apps/serraview-space-utilization-software-solutions-tutorial.md), [전체 q](https://web.uniq.app/), [가시적](../saas-apps/visibly-tutorial.md), [zylo](../saas-apps/zylo-tutorial.md), [Edmentum-교육용 평가 정확한 경로](https://auth.edmentum.com/elf/login), [CyberLAB](https://cyberlab.evolvesecurity.com/#/welcome), [Altamira hrm](../saas-apps/altamira-hrm-tutorial.md), [WireWheel](../saas-apps/wirewheel-tutorial.md), [Zix 준수 및 캡처](https://sminstall.zixcorp.com/teams/teams.php?install_request=true&tenant_id=common), [Greenlight Enterprise Business Controls Platform](../saas-apps/greenlight-enterprise-business-controls-platform-tutorial.md), [genetec 클리어런스](https://www.clearance.network/), [ISAMS](../saas-apps/isams-tutorial.md), [Verasmart](../saas-apps/verasmart-tutorial.md), [amiko](https://amiko.web.rivero.app/), [twingate](https://auth.twingate.com/signup), [깔때기형 임대](https://nestiolistings.com/sso/oidc/azure/authorize/), [Scalefusion](https://scalefusion.com/users/sign_in/), [Bpanda](https://goto.bpanda.com/login), [vivun 일정 연결](https://app.vivun.com/dashboard/calendar/connect), [fortigate SSL VPN](../saas-apps/fortigate-ssl-vpn-tutorial.md), [Wandera 최종 사용자](https://www.wandera.com/)

여기에서 모든 응용 프로그램의 설명서를 찾을 수도 있습니다. https://aka.ms/AppsTutorial

Azure AD 앱 갤러리에서 응용 프로그램을 나열 하려면 여기에서 세부 정보를 읽으십시오. https://aka.ms/AzureADAppRequest

---

### <a name="resource-forests-now-available-for-azure-ad-ds"></a>이제 Azure AD DS에 리소스 포리스트 사용 가능 

**유형:** 새 기능 **서비스 범주:** Azure AD Domain Services   
**제품 기능:** Azure AD Domain Services
 
Azure AD Domain Services에서 리소스 포리스트의 기능이 이제 일반 공급 됩니다. 이제 스마트 카드 권한 부여를 비롯 하 여 Azure AD Domain Services를 사용 하기 위해 암호 해시 동기화 없이 권한 부여를 사용 하도록 설정할 수 있습니다. 자세한 내용은 [Azure Active Directory Domain Services에 대 한 복제본 집합 개념 및 기능 (미리 보기)](../../active-directory-domain-services/concepts-replica-sets.md)을 참조 하세요.
 
---

### <a name="regional-replica-support-for-azure-ad-ds-managed-domains-now-available"></a>이제 Azure AD DS 관리 되는 도메인에 대 한 지역 복제본 지원이 제공 됩니다.

**유형:** 새로운 기능   
**서비스 범주:** Azure AD Domain Services  
**제품 기능:** Azure AD Domain Services
 
관리되는 도메인을 확장하여 Azure AD 테넌트마다 복제본 세트를 2개 이상 포함할 수 있습니다. Azure AD Domain Services를 지 원하는 모든 Azure 지역의 피어 링 가상 네트워크에 복제본 집합을 추가할 수 있습니다. 다른 Azure 지역에 있는 추가 복제본 세트는 Azure 지역이 오프라인 상태가 될 때 레거시 애플리케이션에 대한 지리적 재해 복구를 제공합니다. 자세한 내용은 [Azure Active Directory Domain Services에 대 한 복제본 집합 개념 및 기능 (미리 보기)](../../active-directory-domain-services/concepts-replica-sets.md)을 참조 하세요.

---

### <a name="general-availability-of-azure-ad-my-sign-ins"></a>Azure AD 내 Sign-Ins의 일반 공급

**유형:** 새로운 기능  
**서비스 범주:** 인증 (로그인)  
**제품 기능:** 최종 사용자 환경
 
Azure AD 내 Sign-Ins는 엔터프라이즈 사용자가 로그인 기록을 검토 하 여 비정상적인 활동을 확인할 수 있도록 하는 새로운 기능입니다. 또한이 기능을 사용 하면 최종 사용자가 의심 스러운 활동에 대해 "This me" 또는 "This was"를 보고할 수 있습니다. 이 기능을 사용 하는 방법에 대해 자세히 알아보려면 [내 Sign-Ins 페이지에서 최근 로그인 활동 보기 및 검색](../user-help/my-account-portal-sign-ins-page.md#confirm-unusual-activity)을 참조 하세요.
 
---

### <a name="sap-successfactors-hr-driven-user-provisioning-to-azure-ad-is-now-generally-available"></a>Azure AD에 대 한 SAP SuccessFactors HR 기반 사용자 프로비저닝이 이제 일반 공급 됩니다.

**유형:** 새로운 기능  
**서비스 범주:** 앱 프로비전  
**제품 기능:** Id 수명 주기 관리
 
이제 Azure AD를 사용 하 여 SAP SuccessFactors를 신뢰할 수 있는 id 원본으로 통합 하 고 새 채용 및 종료와 같은 HR 이벤트를 사용 하 여 종단 간 id 수명 주기를 자동화 하 여 Azure AD에서 계정 프로 비전 및 프로 비전 해제를 수행할 수 있습니다. 

Azure AD에 대 한 SAP SuccessFactors 인바운드 프로 비전을 구성 하는 방법에 대 한 자세한 내용은 [사용자 프로 비전을 ACTIVE DIRECTORY Sap SuccessFactors 구성](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md)자습서를 참조 하세요.
 
---

### <a name="custom-open-id-connect-ms-graph-api-support-for-azure-ad-b2c"></a>Azure AD B2C에 대 한 사용자 지정 Open ID Connect MS Graph API 지원

**유형:** 새로운 기능  
**서비스 범주:** B2C - 소비자 ID 관리  
**제품 기능:** B2B/B2C
 
이전에는 사용자 지정 Open ID Connect 공급자를 Azure Portal 통해서만 추가 하거나 관리할 수 있었습니다. 이제 Azure AD B2C 고객이 Microsoft Graph Api 베타 버전을 통해 추가 하 고 관리할 수 있습니다. Api를 사용 하 여이 리소스를 구성 하는 방법을 알아보려면 [identityProvider 리소스 형식](/graph/api/resources/identityprovider?view=graph-rest-beta)을 참조 하세요.
 
---

### <a name="assign-azure-ad-built-in-roles-to-cloud-groups"></a>클라우드 그룹에 Azure AD 기본 제공 역할 할당

**유형:** 새로운 기능  
**서비스 범주:** Azure AD 역할  
**제품 기능:** 액세스 제어

이제이 새로운 기능을 사용 하 여 Azure AD 기본 제공 역할을 클라우드 그룹에 할당할 수 있습니다. 예를 들어 SharePoint 관리자 역할을 Contoso_SharePoint_Admins 그룹에 할당할 수 있습니다. 또한 PIM을 사용 하 여 그룹을 적절 한 액세스 권한을 부여 하는 대신 역할의 적격 멤버로 만들 수 있습니다. 이 기능을 구성 하는 방법을 알아보려면 [클라우드 그룹을 사용 하 여 Azure Active Directory에서 역할 할당 관리 (미리 보기)](../roles/groups-concept.md)를 참조 하세요.
 
---

### <a name="insights-business-leader-built-in-role-now-available"></a>Insights 비즈니스 리더 기본 제공 역할을 이제 사용할 수 있음

**유형:** 새로운 기능  
**서비스 범주:** Azure AD 역할  
**제품 기능:** 액세스 제어
 
Insights 비즈니스 리더 역할의 사용자는 [M365 insights 응용 프로그램](https://www.microsoft.com/microsoft-365/partners/workplaceanalytics)을 통해 대시보드 및 통찰력 집합에 액세스할 수 있습니다. 여기에는 모든 대시보드에 대 한 모든 액세스 권한 및 제공 되는 정보 및 데이터 탐색 기능이 포함 됩니다. 그러나이 역할의 사용자는 정보 관리자 역할을 담당 하는 제품 구성 설정에 액세스할 수 없습니다. 이 역할에 대 한 자세한 내용은 [Azure Active Directory의 관리자 역할 권한](../roles/permissions-reference.md#insights-business-leader) 을 참조 하세요.
 
---

### <a name="insights-administrator-built-in-role-now-available"></a>이제 Insights 관리자 기본 제공 역할을 사용할 수 있음

**유형:** 새로운 기능  
**서비스 범주:** Azure AD 역할  
**제품 기능:** 액세스 제어
 
Insights 관리자 역할의 사용자는 [M365 Insights 응용 프로그램](https://www.microsoft.com/microsoft-365/partners/workplaceanalytics)의 전체 관리 기능 집합에 액세스할 수 있습니다. 이 역할의 사용자는 디렉터리 정보를 읽고, 서비스 상태를 모니터링 하 고, 파일 지원 티켓을 확인 하 고, 정보 관리자 설정 측면에 액세스할 수 있습니다. 이 역할에 대 한 자세한 내용은 [Azure Active Directory의 관리자 역할 권한](../roles/permissions-reference.md#insights-administrator) 을 참조 하세요.
 
--- 

### <a name="application-admin-and-cloud-application-admin-can-manage-extension-properties-of-applications"></a>응용 프로그램 관리자 및 클라우드 응용 프로그램 관리자는 응용 프로그램의 확장 속성을 관리할 수 있습니다.

**유형:** 변경된 기능  
**서비스 범주:** Azure AD 역할  
**제품 기능:** 액세스 제어
 
이전에는 전역 관리자만 [확장 속성](/graph/api/application-post-extensionproperty?view=graph-rest-beta&tabs=http)을 관리할 수 있었습니다. 이제 응용 프로그램 관리자 및 클라우드 응용 프로그램 관리자에 게이 기능을 사용 하도록 설정 하는 중입니다.
 
---

### <a name="mim-2016-sp2-hotfix-462630-and-connectors-1113010"></a>MIM 2016 SP2 핫픽스 4.6.263.0 및 커넥터 1.1.1301.0

**유형:** 변경된 기능  
**서비스 범주:** Microsoft Identity Manager  
**제품 기능:** Id 수명 주기 관리

[핫픽스 롤업 패키지 (build 4.6.263.0)](https://support.microsoft.com/help/4576473/hotfix-rollup-package-build-4-6-263-0-is-available-for-microsoft-ident) 는 MICROSOFT IDENTITY MANAGER (MIM) 2016 서비스 팩 2 (SP2)에서 사용할 수 있습니다. 이 롤업 패키지에는 MIM CM, MIM 동기화 관리자 및 PAM 구성 요소에 대 한 업데이트가 포함 되어 있습니다. 또한 MIM 일반 커넥터 1.1.1301.0 빌드에는 Graph 커넥터에 대 한 업데이트가 포함 되어 있습니다.

---
 
## <a name="july-2020"></a>2020년 7월

### <a name="as-an-it-admin-i-want-to-target-client-apps-using-conditional-access"></a>IT 관리자는 조건부 액세스를 사용 하 여 클라이언트 앱을 대상으로 합니다.

**유형:** 변경 계획   
**서비스 범주:** 조건부 액세스  
**제품 기능:** Id 보안 & 보호
 
조건부 액세스에서 클라이언트 앱 조건의 GA 릴리스를 사용 하면 이제 모든 클라이언트 응용 프로그램에 새 정책이 기본적으로 적용 됩니다. 여기에는 레거시 인증 클라이언트가 포함 됩니다. 기존 정책은 변경 되지 않고 그대로 유지 되지만, 정책에 따라 적용 되는 클라이언트 앱을 쉽게 확인 하기 위해 기존 정책에서 *예/아니요 구성* 토글이 제거 됩니다. 

새 정책을 만들 때 여전히 레거시 인증을 사용 하는 사용자 및 서비스 계정을 제외 해야 합니다. 그렇지 않으면 차단 됩니다. [자세한 정보를 알아보세요](../conditional-access/concept-conditional-access-conditions.md).
 
---

### <a name="upcoming-scim-compliance-fixes"></a>예정 된 SCIM 준수 픽스

**유형:** 변경 계획  
**서비스 범주:** 앱 프로비전  
**제품 기능:** Id 수명 주기 관리
 
Azure AD 프로 비전 서비스는 응용 프로그램과 통합 하기 위해 SCIM 표준을 활용 합니다. SCIM 표준의 구현은 진화 하 고 있으며, 패치 작업을 수행 하는 방법에 대 한 동작을 변경 하 고 리소스에 "active" 속성을 설정 하는 것이 좋습니다. [자세한 정보를 알아보세요](../app-provisioning/application-provisioning-config-problem-scim-compatibility.md).
 
---

### <a name="group-owner-setting-on-azure-admin-portal-will-be-changed"></a>Azure 관리 포털의 그룹 소유자 설정이 변경 됨

**유형:** 변경 계획  
**서비스 범주:** 그룹 관리  
**제품 기능:** 협업

그룹에 대 한 소유자 설정 일반 설정 페이지는 Azure 관리 포털 및 액세스 패널에서 소유자 할당 권한을 제한 된 사용자 그룹으로 제한 하도록 구성할 수 있습니다. 이러한 두 UX 포털 뿐만 아니라 그룹 소유자 권한을 할당 하는 기능이 곧 제공 될 예정 이며, PowerShell 및 Microsoft Graph 같은 끝점에서 일관 된 동작을 제공 하기 위해 백 엔드에서 정책을 적용할 수 있습니다. 

Microsoft는이를 사용 하지 않는 고객에 대 한 현재 설정을 사용 하지 않도록 설정 하 고, 향후 몇 개월 동안 그룹 소유자 권한으로 사용자의 범위를 설정 하는 옵션을 제공 합니다. 그룹 설정 업데이트에 대 한 지침은 [Azure Active Directory](./active-directory-groups-settings-azure-portal.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)를 사용 하 여 그룹 정보 편집을 참조 하세요.

---

### <a name="azure-active-directory-registration-service-is-ending-support-for-tls-10-and-11"></a>Azure Active Directory 등록 서비스가 TLS 1.0 및 1.1에 대 한 지원을 종료 합니다.

**유형:** 변경 계획  
**서비스 범주:** 장치 등록 및 관리  
**제품 기능:** 플랫폼

TLS (전송 계층 보안) 1.2 및 업데이트 서버와 클라이언트는 Azure Active Directory Device Registration 서비스와 곧 통신할 수 있습니다. Azure AD Device Registration service와 통신 하는 TLS 1.0 및 1.1에 대 한 지원은 사용 중지 됩니다.
- 모든 소 버린 클라우드의 2020 년 8 월 31 일 (GCC High, DoD 등)
- 모든 상용 클라우드의 2020 년 10 월 30 일

Azure AD 등록 서비스에 대 한 TLS 1.2에 대해 [자세히 알아보세요](../devices/reference-device-registration-tls-1-2.md) .

---

### <a name="windows-hello-for-business-sign-ins-visible-in-azure-ad-sign-in-logs"></a>Azure AD 로그인 로그에 표시 되는 비즈니스용 Windows Hello 로그인

**유형:** 고정  
**서비스 범주:** 보고  
**제품 기능:** 모니터링 및 보고
 
비즈니스용 windows Hello를 사용 하면 최종 사용자가 제스처 (예: PIN 또는 생체 인식)를 사용 하 여 Windows 컴퓨터에 로그인 할 수 있습니다. Azure AD 관리자는 조직의 경험을 통해 다른 Windows 로그인의 비즈니스용 Windows Hello 로그인을 암호 없는 인증으로 구분할 수 있습니다. 

이제 관리자는 Azure Portal의 Azure AD Sign-Ins 블레이드에서 Windows 로그인 이벤트에 대 한 인증 세부 정보 탭을 확인 하 여 Windows 인증에서 비즈니스용 Windows Hello를 사용 했는지 여부를 확인할 수 있습니다. 비즈니스용 Windows Hello 인증에는 인증 방법 필드에 "WindowsHelloForBusiness"가 포함 됩니다. Sign-In 로그를 해석 하는 방법에 대 한 자세한 내용은 [로그인 로그 설명서](../reports-monitoring/concept-sign-ins.md)를 참조 하세요.
 
---

### <a name="fixes-to-group-deletion-behavior-and-performance-improvements"></a>그룹 삭제 동작 및 성능 향상에 대 한 수정

**유형:** 고정  
**서비스 범주:** 앱 프로비전  
**제품 기능:** Id 수명 주기 관리
 
이전에는 그룹이 "범위 내"에서 "범위를 벗어났습니다."로 변경 되 고 관리자가 변경을 완료 하기 전에 다시 시작을 클릭 했을 때 그룹 개체를 삭제 하지 않았습니다. 이제 범위를 벗어나면 (사용 안 함, 삭제 됨, 할당 되지 않음 또는 범위 지정 필터를 전달 하지 않음) 그룹 개체가 대상 응용 프로그램에서 삭제 됩니다. [자세한 정보를 알아보세요](../app-provisioning/how-provisioning-works.md#incremental-cycles).
 
---

### <a name="public-preview-admins-can-now-add-custom-content-in-the-email-to-reviewers-when-creating-an-access-review"></a>공개 미리 보기: 이제 관리자가 액세스 검토를 만들 때 검토자에 게 전자 메일의 사용자 지정 콘텐츠를 추가할 수 있습니다.

**유형:** 새로운 기능  
**서비스 범주:** 액세스 검토  
**제품 기능:** Id 거 버 넌 스
 
새 액세스 검토가 만들어지면 검토자는 액세스 검토를 완료 하도록 요청 하는 전자 메일을 받습니다. 대부분의 고객은 전자 메일에 사용자 지정 콘텐츠를 추가 하는 기능 (예: 연락처 정보 또는 기타 추가 지원 콘텐츠)을 요청 하 여 검토자를 안내 합니다. 

이제 공개 미리 보기로 제공 되는 관리자는 Azure AD 액세스 검토의 "고급" 섹션에서 콘텐츠를 추가 하 여 검토자에 게 보내는 전자 메일의 사용자 지정 콘텐츠를 지정할 수 있습니다. 액세스 검토를 만드는 방법에 대 한 지침은 [AZURE AD 액세스 검토에서 그룹 및 응용 프로그램에 대 한 액세스 검토 만들기](../governance/create-access-review.md)를 참조 하세요.
 
---

### <a name="authorization-code-flow-for-single-page-apps-available"></a>단일 페이지 앱에 대 한 인증 코드 흐름 사용 가능

**유형:** 새로운 기능  
**서비스 범주:** 인증 (로그인)  
**제품 기능:** 개발자 환경
 
최신 브라우저에서 Safari ITP와 같은 타사 쿠키 제한 사항 때문에, SPAs는 SSO를 유지 관리 하기 위해 암시적 흐름이 아닌 권한 부여 코드 흐름을 사용 해야 하 고 MSAL.js v 2. x는 이제 인증 코드 흐름을 지원 합니다. 

Azure Portal에 대 한 해당 업데이트가 있으므로 SPA를 "spa" 형식으로 업데이트 하 고 인증 코드 흐름을 사용할 수 있습니다. 추가 지침은 [인증 코드 흐름을 사용 하 여 사용자 로그인 및 JAVASCRIPT SPA에서 액세스 토큰 가져오기](../develop/quickstart-v2-javascript-auth-code.md) 를 참조 하세요.
 
---

### <a name="azure-ad-application-proxy-now-supports-the-remote-desktop-services-web-client"></a>이제 Azure AD 응용 프로그램 프록시 원격 데스크톱 서비스 웹 클라이언트를 지원 합니다.

**유형:** 새로운 기능  
**서비스 범주:** 응용 프로그램 프록시  
**제품 기능:** 액세스 제어

이제 Azure AD 응용 프로그램 프록시는 RDS (원격 데스크톱 서비스) 웹 클라이언트를 지원 합니다. 사용자는 RDS 웹 클라이언트를 사용 하 여 Microsoft Edge, Internet Explorer 11, Google Chrome 등의 HTLM5 지원 브라우저를 통해 원격 데스크톱 인프라에 액세스할 수 있습니다. 사용자는 어디서 나 로컬 장치를 사용 하는 것 처럼 원격 앱 또는 데스크톱과 상호 작용할 수 있습니다. Azure AD 응용 프로그램 프록시을 사용 하 여 모든 유형의 리치 클라이언트 앱에 대 한 사전 인증 및 조건부 액세스 정책을 적용 하 여 RDS 배포의 보안을 강화할 수 있습니다. 지침은 [Azure AD 응용 프로그램 프록시를 사용 하 여 원격 데스크톱 게시](../manage-apps/application-proxy-integrate-with-remote-desktop-services.md)를 참조 하세요.
 
---

### <a name="next-generation-azure-ad-b2c-user-flows-in-public-preview"></a>공개 미리 보기로 제공 되는 다음 세대 Azure AD B2C 사용자 흐름

**유형:** 새로운 기능  
**서비스 범주:** B2C - 소비자 ID 관리  
**제품 기능:** B2B/B2C
 
간소화 된 사용자 흐름 환경에는 미리 보기 기능을 제공 하는 기능 패리티가 제공 되며 새로운 모든 기능을 위한 홈입니다. 사용자는 동일한 사용자 흐름 내에서 새로운 기능을 사용 하도록 설정할 수 있으므로 모든 새 기능 릴리스에서 여러 버전을 만들 필요가 없습니다. 마지막으로, 사용자에 게 친숙 한 새 UX를 사용 하면 사용자 흐름을 간편 하 게 선택 하 고 만들 수가 있습니다. 이제 [사용자 흐름을 만들어](../../active-directory-b2c/tutorial-create-user-flows.md)사용해 보세요. 

사용자 흐름에 대 한 자세한 내용은 [Azure Active Directory B2C 사용자 흐름 버전](../../active-directory-b2c/user-flow-versions.md#:~:text=    User flow  ,account. Usi ...  1 more rows )을 참조 하세요.

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---july-2020"></a>Azure AD 응용 프로그램 갤러리에서 사용할 수 있는 새로운 페더레이션된 앱-7 월 2020

**유형:** 새로운 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능:** 타사 통합
 
7 월 2020에 앱 갤러리에 페더레이션 지원과 함께 다음 55 새 응용 프로그램을 추가 했습니다.

[사용자의 손을 ap](http://www.rmit.com.ar/), [Appreiz](https://microsoftteams.appreiz.com/), [Inextor 자격 증명 모음](https://inexto.com/inexto-suite/inextor) [Beekast](https://my.beekast.com/), [Templafy openid connect Connect](https://app.templafy.com/), [PeterConnects 접수원](https://msteams.peterconnects.com/), a [Hac-coa](https://appfusions.alohacloud.com/auth), [Control 타워](https://bpm.tnxcorp.com/sso/microsoft), [cocoom](https://start.cocoom.com/), [동전 건설 클라우드](https://sso.coinsconstructioncloud.com/#login/), [Medxnote MT](https://task.teamsmain.medx.im/authorization), [Reflekt](https://reflekt.konsolute.com/login), [rever](https://app.reverscore.net/access), [MyCompanyArchive](https://login.mycompanyarchive.com/), [GReminders](https://app.greminders.com/o365-oauth), [Titanfile](../saas-apps/titanfile-tutorial.md), [Wootric](../saas-apps/wootric-tutorial.md), [SolarWinds Orion](https://support.solarwinds.com/SuccessCenter/s/orion-platform?language=en_US), OpenText  [Directory Services](../saas-apps/opentext-directory-services-tutorial.md), [Coite](../saas-apps/datasite-tutorial.md), [blogin](../saas-apps/blogin-tutorial.md), [IntSights](../saas-apps/intsights-tutorial.md), [Kpifire](../saas-apps/kpifire-tutorial.md), [Textline](../saas-apps/textline-tutorial.md), [Cloud 아카데미-SSO](../saas-apps/cloud-academy-sso-tutorial.md), [Community Spark](../saas-apps/community-spark-tutorial.md), [작업](../saas-apps/chatwork-tutorial.md), [cloudsign](../saas-apps/cloudsign-tutorial.md), [C3M Cloud Control](../saas-apps/c3m-cloud-control-tutorial.md), [SmartHR](https://smarthr.jp/), [numlyengage](../saas-apps/numlyengage-tutorial.md), [Michigan Data hubs Single Sign-on](../saas-apps/michigan-data-hub-single-sign-on-tutorial.md), [송신](../saas-apps/egress-tutorial.md), [Sendsafely](../saas-apps/sendsafely-tutorial.md), [Eletive](https://app.eletive.com/), [Right 사이버 보안 ADI](https://right-hand.ai/), [Fyde Enterprise Authentication](https://enterprise.fyde.com/), [verme](../saas-apps/verme-tutorial.md), [Lenses.io](../saas-apps/lensesio-tutorial.md), [Momenta](../saas-apps/momenta-tutorial.md), [uprise](https://app.uprise.co/sign-in), [Q](https://q.moduleq.com/login), [cloudsign](../saas-apps/cloudcords-tutorial.md), [TellMe 봇](https://tellme365liteweb.azurewebsites.net/), [영감](https://app.inspiresoftware.com/), [maverics id Orchestrator SAML 커넥터](https://www.strata.io/identity-fabric/), [smartschool (school Management System)](https://smartschoolz.com/login), [Zepto-Intelligent 계측](https://user.zepto-ai.com/signin), [Studi.ly](https://studi.ly/), [plan](http://www.trackplanfm.com/), [Skedda](../saas-apps/skedda-tutorial.md) [, WhosOnLocation,](../saas-apps/whos-on-location-tutorial.md) [Coggle](../saas-apps/coggle-tutorial.md), [kemp loadmaster](https://kemptechnologies.com/cloud-load-balancer/), [browserstack Single sign-on](../saas-apps/browserstack-single-sign-on-tutorial.md)

여기에서 모든 응용 프로그램의 설명서를 찾을 수도 있습니다. https://aka.ms/AppsTutorial

Azure AD 앱 갤러리에 응용 프로그램을 나열 하려면 여기에서 세부 정보를 참조 하세요. https://aka.ms/AzureADAppRequest

---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---july-2020"></a>Azure AD 응용 프로그램 갤러리의 새로운 프로 비전 커넥터-7 월 2020

**유형:** 새로운 기능  
**서비스 범주:** 앱 프로비전  
**제품 기능:** 타사 통합

이제 새로 통합 된 앱 [LinkedIn Learning](../saas-apps/linkedin-learning-provisioning-tutorial.md)에 대 한 사용자 계정을 만들고, 업데이트 하 고, 삭제할 수 있습니다.

자동화된 사용자 계정 프로비저닝을 사용하여 조직의 보안을 강화하는 방법에 대한 자세한 내용은 [Azure AD를 사용하여 SaaS 애플리케이션에 대한 사용자 프로비저닝 자동화](../app-provisioning/user-provisioning.md)를 참조하세요.

---

### <a name="view-role-assignments-across-all-scopes-and-ability-to-download-them-to-a-csv-file"></a>모든 범위에서 역할 할당을 확인 하 고 csv 파일로 다운로드할 수 있습니다.

**유형:** 변경된 기능  
**서비스 범주:** Azure AD 역할  
**제품 기능:** 액세스 제어
 
이제 Azure AD 포털의 "역할 및 관리자" 탭에서 역할에 대 한 모든 범위에서 역할 할당을 볼 수 있습니다. 각 역할에 대 한 역할 할당을 CSV 파일로 다운로드할 수도 있습니다. 역할 할당을 보고 추가 하는 방법에 대 한 지침은 [Azure Active Directory에서 관리자 역할 보기 및 할당](../roles/manage-roles-portal.md)을 참조 하세요.
 
---

### <a name="azure-multi-factor-authentication-software-development-azure-mfa-sdk-deprecation"></a>Azure Multi-Factor Authentication 소프트웨어 개발 (Azure MFA SDK) 사용 중단

**유형:** 사용되지 않음  
**서비스 범주:** MFA  
**제품 기능:** Id 보안 & 보호
 
Azure MFA SDK (Multi-Factor Authentication 소프트웨어 개발)는 11 월 2017에 처음 발표 된 2018 년 11 월 14 일에 종료 되었습니다. Microsoft는 2020 년 9 월 30 일에 적용 되는 SDK 서비스를 종료 합니다. SDK에 대 한 모든 호출이 실패 합니다.

조직에서 Azure MFA SDK를 사용 하는 경우 2020 년 9 월 30 일까 지 마이그레이션 해야 합니다.
- MIM 용 Azure MFA SDK: MIM에서 SDK를 사용 하는 경우 다음 [지침](/microsoft-identity-manager/working-with-mfaserver-for-mim)에 따라 Azure mfa 서버로 마이그레이션하고 PAM (Privileged Access Management)을 활성화 해야 합니다.   
- 사용자 지정 된 앱 용 azure MFA SDK: Azure AD에 앱을 통합 하 고 조건부 액세스를 사용 하 여 MFA를 적용 하는 것이 좋습니다. 시작 하려면이 [페이지](../manage-apps/plan-an-application-integration.md)를 검토 합니다. 

---

## <a name="june-2020"></a>2020년 6월 

### <a name="user-risk-condition-in-conditional-access-policy"></a>조건부 액세스 정책의 사용자 위험 조건

**유형:** 변경 계획  
**서비스 범주:** 조건부 액세스  
**제품 기능:** Id 보안 & 보호
 

Azure AD 조건부 액세스 정책에서 사용자 위험 지원을 통해 여러 사용자 위험 기반 정책을 만들 수 있습니다. 서로 다른 사용자 및 앱에 대해 다른 최소 사용자 위험 수준이 필요할 수 있습니다. 사용자 위험에 따라 액세스를 차단 하는 정책을 만들거나, multi-factor authentication을 요구 하거나, 보안 암호 변경을 요구 하거나, Microsoft Cloud App Security로 리디렉션하여 추가 감사와 같은 세션 정책을 적용할 수 있습니다.

사용자 위험 조건에는 P2를 제공 하는 Azure Id 보호를 사용 하기 때문에 Azure AD Premium P2가 필요 합니다. 조건부 액세스에 대 한 자세한 내용은 [AZURE AD 조건부 액세스 설명서](../conditional-access/index.yml)를 참조 하세요.

---

### <a name="saml-sso-now-supports-apps-that-require-spnamequalifier-to-be-set-when-requested"></a>이제 SAML SSO는 요청 시 SPNameQualifier를 설정 해야 하는 앱을 지원 합니다.

**유형:** 고정  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능:** SSO
 
일부 SAML 응용 프로그램의 경우 요청 시 어설션 주체가 SPNameQualifier을 반환 해야 합니다. 이제 SPNameQualifier이 요청 NameID 정책에서 요청 되 면 Azure AD가 올바르게 응답 합니다. SP가 시작한 로그인에도 적용 되 고 IdP 시작 된 로그인이 수행 됩니다.  Azure Active Directory SAML 프로토콜에 대 한 자세한 내용은 [Single Sign-On saml 프로토콜](../develop/single-sign-on-saml-protocol.md)을 참조 하세요.

---

### <a name="azure-ad-b2b-collaboration-supports-inviting-msa-and-google-users-in-azure-government-tenants"></a>Azure AD B2B 공동 작업은 Azure Government 테 넌 트의 MSA 및 Google 사용자 초대를 지원 합니다.

**유형:** 새로운 기능  
**서비스 범주:** B2B  
**제품 기능:** B2B/B2C
 

B2B 공동 작업 기능을 사용 하는 테 넌 트가 이제 Microsoft 또는 Google 계정이 있는 사용자를 초대할 수 있습니다. Azure Government 테 넌 트가 이러한 기능을 사용할 수 있는지 확인 하려면 [AZURE 미국 정부 테 넌 트에서 B2B 공동 작업을 사용할 수 있는지 어떻게 알 수 있나요?](../external-identities/current-limitations.md#how-can-i-tell-if-b2b-collaboration-is-available-in-my-azure-us-government-tenant) 의 지침을 따르세요.

 
---
 
### <a name="user-object-in-ms-graph-v1-now-includes-externaluserstate-and-externaluserstatechangeddatetime-properties"></a>이제 MS Graph v1의 사용자 개체에 externalUserState 및 externalUserStateChangedDateTime 속성이 포함 됩니다.

**유형:** 새로운 기능  
**서비스 범주:** B2B  
**제품 기능:** B2B/B2C
 

ExternalUserState 및 externalUserStateChangedDateTime 속성을 사용 하 여 초대를 수락 하지 않은 초대 된 B2B 게스트 뿐만 아니라 며칠 후에 초대를 수락 하지 않은 사용자를 삭제 하는 등의 빌드 자동화를 찾을 수 있습니다. 이제 MS Graph v1에서 이러한 속성을 사용할 수 있습니다. 이러한 속성을 사용 하는 방법에 대 한 지침은 [사용자 리소스 종류](/graph/api/resources/user?view=graph-rest-1.0)를 참조 하세요.
 
---

### <a name="manage-authentication-sessions-in-azure-ad-conditional-access-is-now-generally-available"></a>Azure AD에서 인증 세션 관리 조건부 액세스가 이제 일반 공급 됩니다.

**유형:** 새로운 기능  
**서비스 범주:** 조건부 액세스  
**제품 기능:** Id 보안 & 보호
 
인증 세션 관리 기능을 사용 하면 사용자가 로그인 자격 증명을 제공 해야 하는 빈도 및 사용자 환경에서 더 많은 보안과 유연성을 제공 하기 위해 브라우저를 닫았다가 다시 연 후 자격 증명을 제공 해야 하는지 여부를 구성할 수 있습니다.
 
또한 Azure AD 조인, 하이브리드 Azure AD 조인 및 Azure AD 등록 장치에 대 한 첫 번째 단계 인증에만 적용 되는 인증 세션 관리가 사용 됩니다. 이제 인증 세션 관리가 MFA에도 적용 됩니다. 자세한 내용은 [조건부 액세스를 사용 하 여 인증 세션 관리 구성](../conditional-access/howto-conditional-access-session-lifetime.md)을 참조 하세요.

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---june-2020"></a>Azure AD 응용 프로그램 갤러리에서 사용할 수 있는 새로운 페더레이션된 앱-6 월 2020

**유형:** 새로운 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능:** 타사 통합
 
6 월 2020에 앱 갤러리에 페더레이션 지원이 포함 된 다음 29 개의 새로운 응용 프로그램이 추가 되었습니다.

[Shopify Plus](../saas-apps/shopify-plus-tutorial.md), [Ekarda](../saas-apps/ekarda-tutorial.md), [mailgates](../saas-apps/mailgates-tutorial.md), [BullseyeTDP](../saas-apps/bullseyetdp-tutorial.md), [raketa](../saas-apps/raketa-tutorial.md), [부문](../saas-apps/segment-tutorial.md), [Ai 감사자](https://www.mindbridge.ai/products/ai-auditor/), [popoco Ca Connect](https://app.pobu.ca/), [Proto.io](../saas-apps/proto.io-tutorial.md), [게이트 키퍼](https://www.gatekeeperhq.com/), [Hub 플래너](../saas-apps/hub-planner-tutorial.md), [ansira-파트너 이동 도구 상자](https://ansira.com/technology/channel-engagement), [클라우드에서 IBM Digital Business Automation](../saas-apps/ibm-digital-business-automation-on-cloud-tutorial.md), [Kisi 물리적 보안](../saas-apps/kisi-physical-security-tutorial.md), [viewpointone](https://team.viewpoint.com/), [IntelligenceBank](../saas-apps/intelligencebank-tutorial.md), [pymetrics](../saas-apps/pymetrics-tutorial.md), [Zero](https://www.teamzero.com/), [ation](https://instation.invillia.com/), [edX FOR Business SAML 2.0 통합](../saas-apps/edx-for-business-saml-integration-tutorial.md), [mooc Office 365](https://mooc.office365-training.com/en/), [SmartKargo](../saas-apps/smartkargo-tutorial.md), [PKIsigning platform](https://platform.pkisigning.nl/), [siteintel](../saas-apps/siteintel-tutorial.md), [Field iD](../saas-apps/field-id-tutorial.md), [과정 SAML](../saas-apps/curricula-saml-tutorial.md), [perforce Helix Core-Helix Authentication Service](../saas-apps/perforce-helix-core-tutorial.md), [mycompliance 클라우드](https://cloud.metacompliance.com/), [Smallstep SSH](https://smallstep.com/sso-ssh/)  

여기에서 모든 응용 프로그램의 설명서를 찾을 수도 있습니다 https://aka.ms/AppsTutorial . Azure AD 앱 갤러리에서 응용 프로그램을 나열 하려면 여기에서 세부 정보를 참조 하세요 https://aka.ms/AzureADAppRequest .

---

### <a name="api-connectors-for-external-identities-self-service-sign-up-are-now-in-public-preview"></a>외부 Id에 대 한 API 커넥터 셀프 서비스 등록은 현재 공개 미리 보기로 제공 됩니다.

**유형:** 새로운 기능  
**서비스 범주:** B2B  
**제품 기능:** B2B/B2C
 
외부 Id API 커넥터를 사용 하면 웹 Api를 활용 하 여 셀프 서비스 등록을 외부 클라우드 시스템과 통합할 수 있습니다. 즉, 이제 웹 Api를 등록 흐름에서 특정 단계로 호출 하 여 클라우드 기반 사용자 지정 워크플로를 트리거할 수 있습니다. 예를 들어 API 커넥터를 사용 하 여 다음을 수행할 수 있습니다.

- 사용자 지정 승인 워크플로와 통합 합니다.
- Id 교정 수행
- 사용자 입력 데이터 유효성 검사
- 사용자 특성 덮어쓰기
- 사용자 지정 비즈니스 논리 실행

API 커넥터에서 사용할 수 있는 모든 환경에 대 한 자세한 내용은 [api 커넥터를 사용 하 여 셀프 서비스 등록 사용자 지정 및 확장](../external-identities/api-connectors-overview.md)또는 [웹 api 통합을 사용 하 여 외부 id 셀프 서비스 등록 사용자 지정](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/customize-external-identities-self-service-sign-up-with-web-api/ba-p/1257364#.XvNz2fImuQg.linkedin)을 참조 하세요.
 
---

### <a name="provision-on-demand-and-get-users-into-your-apps-in-seconds"></a>요청 시 프로 비전 하 고 몇 초 내에 사용자 앱에 사용자 가져오기

**유형:** 새로운 기능  
**서비스 범주:** 앱 프로비전  
**제품 기능:** Id 수명 주기 관리
 
Azure AD 프로 비전 서비스는 현재 순환 방식으로 작동 합니다. 서비스는 40 분 마다 실행 됩니다. [주문형 프로 비전 기능](https://aka.ms/provisionondemand) 을 사용 하면 사용자를 선택 하 고 몇 초만에 프로 비전 할 수 있습니다. 이 기능을 사용 하면 다시 시작을 수행 하 여 프로 비전 주기를 다시 시작 하지 않고도 프로 비전 문제를 신속 하 게 해결할 수 있습니다. 
 
---

### <a name="new-permission-for-using-azure-ad-entitlement-management-in-graph"></a>Graph에서 Azure AD 자격 관리를 사용 하기 위한 새 권한

**유형:** 새로운 기능  
**서비스 범주:** 기타  
**제품 기능:** 자격 관리
 
새 위임 된 권한 EntitlementManagement는 이제 Microsoft Graph beta의 자격 관리 API에서 사용할 수 있습니다. 사용 가능한 Api에 대해 자세히 알아보려면 [AZURE AD 자격 관리 Api 작업](/graph/api/resources/entitlementmanagement-root?view=graph-rest-beta)을 참조 하세요.

---

### <a name="identity-protection-apis-available-in-v10"></a>V 1.0에서 사용할 수 있는 id 보호 Api

**유형:** 새로운 기능  
**서비스 범주:** Id 보호  
**제품 기능:** Id 보안 & 보호
 
RiskyUsers 및 riskDetections Microsoft Graph Api는 이제 일반 공급 됩니다. 이제 v2.0 끝점에서 사용할 수 있으므로 프로덕션 환경에서 사용할 수 있도록 초대 합니다. 자세한 내용은 [Microsoft Graph 문서](/graph/api/resources/identityprotectionroot?view=graph-rest-1.0)를 확인 하세요.
 
---

### <a name="sensitivity-labels-to-apply-policies-to-microsoft-365-groups-is-now-generally-available"></a>Microsoft 365 그룹에 정책을 적용 하기 위한 민감도 레이블 이제 일반 공급

**유형:** 새로운 기능  
**서비스 범주:** 그룹 관리  
**제품 기능:** 협업
 

이제 민감도 레이블을 만들고 레이블 설정을 사용 하 여 개인 정보 (공용 또는 개인) 및 외부 사용자 액세스 정책을 포함 하 여 Microsoft 365 그룹에 정책을 적용할 수 있습니다. 개인 정보 취급 방침을 포함 하는 레이블을 만들고, 게스트 사용자를 추가 하는 것을 허용 하지 않는 외부 사용자 액세스 정책을 만들 수 있습니다. 사용자가이 레이블을 그룹에 적용 하는 경우 그룹은 비공개 이며 게스트 사용자를 그룹에 추가할 수 없습니다. 

민감도 레이블은 비즈니스에 중요 한 데이터를 보호 하 고 규정을 준수 하 고 안전한 방식으로 대규모 그룹을 관리할 수 있도록 하는 데 중요 합니다. 민감도 레이블 사용에 대 한 지침은 [Azure Active Directory (미리 보기)의 Microsoft 365 그룹에 민감도 레이블 할당](../enterprise-users/groups-assign-sensitivity-labels.md)을 참조 하세요.
 
---

### <a name="updates-to-support-for-microsoft-identity-manager-for-azure-ad-premium-customers"></a>Azure AD Premium 고객에 대 한 Microsoft Identity Manager 지원 업데이트

**유형:** 변경된 기능  
**서비스 범주:** Microsoft Identity Manager  
**제품 기능:** Id 수명 주기 관리
 
이제 Microsoft Identity Manager 2016의 Azure AD 통합 구성 요소에 대 한 azure 지원이 Microsoft Identity Manager 2016에 대 한 연장 지원 종료를 통해 제공 됩니다. [Microsoft Identity Manager를 사용 하는 Azure AD Premium 고객에 대 한 지원 업데이트](/microsoft-identity-manager/support-update-for-azure-active-directory-premium-customers)를 참조 하세요.

---

### <a name="the-use-of-group-membership-conditions-in-sso-claims-configuration-is-increased"></a>SSO 클레임 구성에서 그룹 멤버 자격 조건의 사용이 증가 합니다.

**유형:** 변경된 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능:** SSO
 
이전에는 단일 응용 프로그램 구성 내의 그룹 멤버 자격을 기준으로 클레임을 조건부로 변경할 때 사용할 수 있는 그룹의 수가 10 개로 제한 되었습니다. SSO 클레임 구성에서 그룹 멤버 자격 조건의 사용이 이제 최대 50 그룹으로 늘어났습니다. 클레임을 구성 하는 방법에 대 한 자세한 내용은 [엔터프라이즈 응용 프로그램 SSO 클레임 구성](../develop/active-directory-saml-claims-customization.md#emitting-claims-based-on-conditions)을 참조 하세요. 

---

### <a name="enabling-basic-formatting-on-the-sign-in-page-text-component-in-company-branding"></a>회사 브랜딩의 로그인 페이지 텍스트 구성 요소에서 기본 서식 지정을 사용 하도록 설정 합니다.

**유형:** 변경된 기능  
**서비스 범주:** 인증 (로그인)  
**제품 기능:** 사용자 인증
 
Azure AD/Microsoft 365 로그인 환경의 회사 브랜딩 기능이 업데이트 되어 고객이 굵은 글꼴, 밑줄, 기울임꼴 등의 간단한 서식 지정 및 하이퍼링크를 추가할 수 있습니다. 이 기능을 사용 하는 방법에 대 한 지침은 [조직의 Azure Active Directory 로그인 페이지에 브랜딩 추가](./customize-branding.md)를 참조 하세요.

---

### <a name="provisioning-performance-improvements"></a>프로 비전 성능 향상

**유형:** 변경된 기능  
**서비스 범주:** 앱 프로비전  
**제품 기능:** Id 수명 주기 관리
 
프로 비전 서비스가 [증분 주기가](../app-provisioning/how-provisioning-works.md#incremental-cycles) 완료 될 때까지 걸리는 시간을 줄이기 위해 업데이트 되었습니다. 즉, 사용자와 그룹이 이전 보다 더 빠르게 응용 프로그램에 프로 비전 됩니다. 6/10/2020 이후에 생성 되는 모든 새로운 프로 비전 작업은 성능 향상을 통해 자동으로 향상 됩니다. 6/10/2020 이전의 프로 비전을 위해 구성 된 모든 응용 프로그램은 성능 개선을 활용 하기 위해 6/10/2020 후에 한 번 다시 시작 해야 합니다. 

---

### <a name="announcing-the-deprecation-of-adal-and-ms-graph-parity"></a>ADAL 및 MS Graph 패리티 사용 중단 발표

**유형:** 사용되지 않음  
**서비스 범주:** 해당 없음  
**제품 기능:** 장치 수명 주기 관리

이제 MSAL (Microsoft 인증 라이브러리)을 사용할 수 있기 때문에 더 이상 ADAL (Azure Active Directory Authentication library)에 새 기능을 추가 하지 않으며 2022 년 6 월 30 일에 보안 패치를 종료 합니다. MSAL으로 마이그레이션하는 방법에 대 한 자세한 내용은 [MSAL (Microsoft 인증 라이브러리)로 응용 프로그램 마이그레이션](../develop/msal-migration.md)을 참조 하세요.

또한 MS Graph를 통해 모든 Azure AD Graph 기능을 사용할 수 있도록 하는 작업을 완료 했습니다. 따라서 Azure AD Graph Api는 2022 년 6 월 30 일까 서 수정 및 보안 픽스만 받습니다. 자세한 내용은 [Microsoft 인증 라이브러리를 사용 하도록 응용 프로그램 업데이트 및 MICROSOFT GRAPH API](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/update-your-applications-to-use-microsoft-authentication-library/ba-p/1257363) 를 참조 하세요.
 
---
 
## <a name="may-2020"></a>2020년 5월

### <a name="retirement-of-properties-in-signins-riskyusers-and-riskdetections-apis"></a>SignIns, riskyUsers 및 riskDetections Api에서 속성 사용 중지

**유형:** 변경 계획  
**서비스 범주:** Id 보호  
**제품 기능:** Id 보안 & 보호

현재 열거 형식은 riskDetections API와 riskyUserHistoryItem (미리 보기)에서 riskType 속성을 나타내는 데 사용 됩니다. 또한 signIns API의 riskEventTypes 속성에도 열거 형식이 사용 됩니다. 앞으로 이러한 속성을 문자열로 나타냅니다. 

고객은 beta riskDetections 및 riskyUserHistoryItem API에서 riskEventType 속성으로 전환 하 고, 베타 signIns API에서 2020 9 월 9 일까지 속성을 riskEventTypes_v2 해야 합니다. 이 날짜에 현재 riskType 및 riskEventTypes 속성을 사용 하지 않을 것입니다. 자세한 내용은 [Microsoft Graph에서 위험 이벤트 속성 및 Id 보호 api](https://developer.microsoft.com/graph/blogs/changes-to-risk-event-properties-and-identity-protection-apis-on-microsoft-graph/)에 대 한 변경 내용을 참조 하세요.

--- 

### <a name="deprecation-of-riskeventtypes-property-in-signins-v10-api-on-microsoft-graph"></a>Microsoft Graph의 signIns v1.0 API에서 riskEventTypes 속성의 사용 중단

**유형:** 변경 계획  
**서비스 범주:** 보고  
**제품 기능:** Id 보안 & 보호

2020 9 월 Microsoft Graph에서 위험 이벤트 속성을 나타내는 경우 열거 된 형식이 문자열 형식으로 전환 됩니다. 미리 보기 Api에 영향을 주는 것 외에도이 변경 내용은 프로덕션 내 서명 API에도 영향을 줍니다.

새 riskEventsTypes_v2 (문자열) 속성이 signIns v1.0 API에 도입 되었습니다. Microsoft Graph 사용 중단 정책에 따라 2022 년 6 월 11 일에 현재 riskEventTypes (enum) 속성을 사용 중지 합니다. 고객은 2022 년 6 월 11 일에 해당 하는 v 1.0 signIns API의 riskEventTypes_v2 속성으로 전환 해야 합니다. 자세한 내용은 [Microsoft Graph에서 signIns의 riskEventTypes 속성의](https://developer.microsoft.com/graph/blogs/deprecation-of-riskeventtypes-property-in-signins-v1-0-api-on-microsoft-graph//)사용 중단을 참조 하세요.

--- 

### <a name="upcoming-changes-to-mfa-email-notifications"></a>MFA 전자 메일 알림의 예정 된 변경 내용

**유형:** 변경 계획  
**서비스 범주:** MFA  
**제품 기능:** Id 보안 & 보호
 

Cloud MFA에 대 한 전자 메일 알림을 다음과 같이 변경 합니다.

전자 메일 알림은 및 주소에서 전송 됩니다. azure-noreply@microsoft.com msonlineservicesteam@microsoftonline.com 사기 행위 경고 전자 메일의 콘텐츠를 업데이트 하 여 사용 차단을 해제 하는 데 필요한 단계를 더 잘 나타낼 수 있습니다.

---

### <a name="new-self-service-sign-up-for-users-in-federated-domains-who-cant-access-microsoft-teams-because-they-arent-synced-to-azure-active-directory"></a>Microsoft 팀에 액세스할 수 없는 페더레이션 도메인의 사용자에 대 한 새 셀프 서비스 등록은 Azure Active Directory에 동기화 되지 않기 때문입니다.

**유형:** 변경 계획  
**서비스 범주:** 인증 (로그인)  
**제품 기능:** 사용자 인증
 

현재 Azure AD에 페더레이션된 도메인에 있지만 테 넌 트에 동기화 되지 않은 사용자는 팀에 액세스할 수 없습니다. 6 월 말부터이 새로운 기능을 사용 하면 기존 전자 메일 확인 등록 기능을 확장 하 여이 작업을 수행할 수 있습니다. 그러면 페더레이션 IdP에 로그인 할 수 있지만 Azure ID에 사용자 개체가 아직 없는 사용자 개체를 자동으로 만들고 팀에 대해 인증할 수 있는 사용자를 사용할 수 있습니다. 해당 사용자 개체는 "셀프 서비스 등록"으로 표시 됩니다. 이는 관리 되는 도메인의 사용자가 수행할 수 있고 동일한 플래그를 사용 하 여 제어할 수 있는 전자 메일을 확인 하는 기존 기능을 확장 한 것입니다. 이 변경 내용은 다음 두 달 동안 롤아웃 완료 됩니다. [여기](../enterprise-users/directory-self-service-signup.md)에서 설명서 업데이트를 시청 하세요.
 
---

### <a name="upcoming-fix-the-oidc-discovery-document-for-the-azure-government-cloud-is-being-updated-to-reference-the-correct-graph-endpoints"></a>예정 된 해결 방법: Azure Government 클라우드에 대 한 OIDC 검색 문서가 올바른 그래프 끝점을 참조 하도록 업데이트 되 고 있습니다.

**유형:** 변경 계획  
**서비스 범주:** 소버린 클라우드  
**제품 기능:** 사용자 인증
 
6 월부터 OIDC 검색 문서 [Microsoft identity platform And Openid connect Connect protocol](../develop/v2-protocols-oidc.md) for the login.microsoftonline.us ( [클라우드](../develop/authentication-national-cloud.md) 끝점) Azure Government에서 제공 된 테 넌 트에 따라 올바른 [국가 클라우드 그래프](/graph/deployments) 끝점 (또는)을 반환 하기 시작 합니다 https://graph.microsoft.us https://dod-graph.microsoft.us) .  현재는 잘못 된 그래프 끝점 (graph.microsoft.com) "msgraph_host" 필드를 제공 합니다.  

이 버그 수정은 약 2 개월 동안 점진적으로 출시 될 예정입니다.  

---

### <a name="azure-government-users-will-no-longer-be-able-to-sign-in-on-loginmicrosoftonlinecom"></a>Azure Government 사용자가 login.microsoftonline.com에서 더 이상 로그인 할 수 없습니다.

**유형:** 변경 계획  
**서비스 범주:** 소버린 클라우드  
**제품 기능:** 사용자 인증
 
2018 년 6 월 1 일에 Azure Government에 대 한 공식 Azure Active Directory (Azure AD) 기관이에서로 변경 되었습니다 https://login-us.microsoftonline.com https://login.microsoftonline.us . Azure Government 테 넌 트 내에서 응용 프로그램을 소유 하 고 있는 경우 us 끝점에서 사용자에 게 로그인 하도록 응용 프로그램을 업데이트 해야 합니다.

5 월 5 일부터 Azure AD는 사용자가 공용 끝점 (microsoftonline.com)을 사용 하 여 Azure Government 테 넌 트에서 호스트 되는 앱에 로그인 하지 못하도록 Azure Government 차단 하는 끝점 변경을 적용 하기 시작 합니다. 영향을 받는 앱이 AADSTS900439 오류를 표시 하기 시작 합니다. 

모든 앱에서 6 월 2020에 완료 될 것으로 예상 되는 적용으로 이러한 변경의 점진적 출시가 발생 합니다. 자세한 내용은 [Azure Government 블로그 게시물](https://devblogs.microsoft.com/azuregov/azure-government-aad-authority-endpoint-update/)을 참조 하세요.

---

### <a name="saml-single-logout-request-now-sends-nameid-in-the-correct-format"></a>이제 SAML 단일 로그 아웃 요청이 올바른 형식으로 NameID를 전송 합니다.

**유형:** 고정  
**서비스 범주:** 인증 (로그인)  
**제품 기능:** 사용자 인증
 
사용자가 로그 아웃 (예: MyApps 포털)을 클릭 하면 Azure AD는 사용자 세션에서 활성 상태이 고 로그 아웃 URL이 구성 된 각 앱에 SAML 단일 로그 아웃 메시지를 보냅니다. 이러한 메시지는 영구 형식의 NameID를 포함 합니다.

원본 SAML 로그인 토큰이 NameID에 대해 다른 형식 (예: 메일/m s)을 사용 하는 경우 SAML 앱은 로그 아웃 메시지의 NameID와 기존 세션의 상관 관계를 지정할 수 없습니다 .이로 인해 SAML 앱 및 사용자가 로그인 상태를 유지 하기 위해 로그 아웃 메시지를 삭제 하 게 됩니다. 이 수정은 응용 프로그램에 대해 구성 된 NameID와의 로그 아웃 메시지 일치를 수행 합니다.

---

### <a name="hybrid-identity-administrator-role-is-now-available-with-cloud-provisioning"></a>이제 클라우드 프로 비전에서 하이브리드 Id 관리자 역할을 사용할 수 있습니다.

**유형:** 새로운 기능  
**서비스 범주:** Azure AD 클라우드 프로 비전  
**제품 기능:** Id 수명 주기 관리
 
IT 관리자는 Azure ADConnect 클라우드 프로 비전을 설정 하는 데 필요한 최소 권한 역할로 새로운 "하이브리드 관리자" 역할을 사용 하 여 시작할 수 있습니다. 이 새로운 역할을 사용 하는 경우 더 이상 전역 관리자 역할을 사용 하 여 클라우드 프로 비전을 설정 하 고 구성할 필요가 없습니다. [자세한 정보를 알아보세요](../roles/delegate-by-task.md#connect).
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---may-2020"></a>Azure AD 응용 프로그램 갤러리에서 사용할 수 있는 새로운 페더레이션된 앱-2020 년 5 월

**유형:** 새로운 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능:** 타사 통합
 
2020 년 5 월에 앱 갤러리에 페더레이션 지원이 포함 된 다음 36 새 응용 프로그램이 추가 되었습니다.

[Moula](https://moula.com.au/pay/merchants), [Surveypal](https://www.surveypal.com/app), [Kbot365](https://www.konverso.ai/virtual-assistant-digital-workplace/), [TackleBox](http://www.tacklebox.app/), [Powell 팀](https://powell-software.com/en/powell-teams-en/), [Talentsoft Assistant](https://msteams.talent-soft.com/), [ASC 기록 정보](https://teams.asc-recording.app/product), [GO1](https://www.go1.com/), [B 개입](https://b-engaged.se/), [Competella Contact Center WORKGROUP](http://www.competella.com/), [asite](http://www.asite.com/), ImageSoft [Identity](https://identity.imagesoftinc.com/), [My IBISWorld](https://identity.imagesoftinc.com/), [insuite](../saas-apps/insuite-tutorial.md), [Change Process Management](../saas-apps/change-process-management-tutorial.md), [Cyara CX 보증 Platform](../saas-apps/cyara-cx-assurance-platform-tutorial.md), [스마트 글로벌 거 버 넌 스](../saas-apps/smart-global-governance-tutorial.md), [prezi](../saas-apps/prezi-tutorial.md), [mapbox](../saas-apps/mapbox-tutorial.md), [Datava Enterprise Service Platform](../saas-apps/datava-enterprise-service-platform-tutorial.md), Whimsical, Trelica, [Whimsical](../saas-apps/whimsical-tutorial.md), [Confluence](../saas-apps/easysso-for-confluence-tutorial.md), [EasySSO for BitBucket](../saas-apps/easysso-for-bitbucket-tutorial.md) [Trelica](../saas-apps/trelica-tutorial.md), [Bamboo](../saas-apps/easysso-for-bamboo-tutorial.md), [Torii, Axiad,](../saas-apps/torii-tutorial.md) [Humanage Cloud](../saas-apps/axiad-cloud-tutorial.md), [Vyond](../saas-apps/humanage-tutorial.md), [colortokens ztna](../saas-apps/colortokens-ztna-tutorial.md), [cch tagetik](../saas-apps/cch-tagetik-tutorial.md), [sharevault](../saas-apps/sharevault-tutorial.md), [Vyond](../saas-apps/vyond-tutorial.md), [textexpander](../saas-apps/textexpander-tutorial.md), [누구나 Home CRM](../saas-apps/anyone-home-crm-tutorial.md), [askspoke](../saas-apps/askspoke-tutorial.md), [ice 센터](../saas-apps/ice-contact-center-tutorial.md)

여기에서 모든 응용 프로그램의 설명서를 찾을 수도 있습니다 https://aka.ms/AppsTutorial .

Azure AD 앱 갤러리에 응용 프로그램을 나열 하려면 여기에서 세부 정보를 참조 하세요 https://aka.ms/AzureADAppRequest .

---

### <a name="report-only-mode-for-conditional-access-is-now-generally-available"></a>조건부 액세스에 대 한 보고서 전용 모드가 이제 일반 공급 됩니다.

**유형:** 새로운 기능  
**서비스 범주:** 조건부 액세스  
**제품 기능:** Id 보안 & 보호

[AZURE AD 조건부 액세스에 대 한 보고서 전용 모드](../conditional-access/concept-conditional-access-report-only.md) 를 통해 액세스 제어를 적용 하지 않고 정책의 결과를 평가할 수 있습니다. 조직 전체에서 보고서 전용 정책을 테스트 하 고 사용 하도록 설정 하기 전에 영향을 파악 하 여 배포를 더 안전 하 고 쉽게 만들 수 있습니다. 지난 몇 달 동안 보고서 전용 모드를 강력 하 게 채택 했습니다. 26M 사용자는 이미 보고서 전용 정책의 범위에 있습니다. 오늘 공지를 사용 하면 기본적으로 새 Azure AD 조건부 액세스 정책이 보고서 전용 모드에서 생성 됩니다. 즉, 정책을 만든 순간부터 정책의 영향을 모니터링할 수 있습니다. MS Graph Api를 사용 하는 사용자의 경우에도 [프로그래밍 방식으로 보고서 전용 정책을 관리할](/graph/api/resources/conditionalaccesspolicy?view=graph-rest-beta) 수 있습니다. 

---

### <a name="self-service-sign-up-for-guest-users"></a>게스트 사용자에 대 한 셀프 서비스 등록

**유형:** 새로운 기능  
**서비스 범주:** B2B  
**제품 기능:** B2B/B2C
 
Azure AD의 외부 Id를 사용 하 여 조직 외부 사용자가 앱과 리소스에 액세스 하도록 허용 하는 동시에 원하는 모든 id를 사용 하 여 로그인 할 수 있습니다. 외부 사용자와 애플리케이션을 공유하는 경우 애플리케이션에 액세스해야 하는 사용자를 항상 미리 알 수 있는 것은 아닙니다. [셀프 서비스 등록](../external-identities/self-service-sign-up-overview.md)을 사용 하면 게스트 사용자가 LOB (기간 업무) 앱에 등록 하 고 게스트 계정을 확보할 수 있습니다. 등록 흐름을 만들고 사용자 지정 하 여 Azure AD 및 소셜 id를 지원할 수 있습니다. 등록 하는 동안 사용자에 대 한 추가 정보를 수집할 수도 있습니다.

---

 ### <a name="conditional-access-insights-and-reporting-workbook-is-generally-available"></a>조건부 액세스 정보 및 보고 통합 문서를 일반적으로 사용할 수 있음

**유형:** 새로운 기능  
**서비스 범주:** 조건부 액세스  
**제품 기능:** Id 보안 & 보호

[Insights 및 보고 통합 문서](../conditional-access/howto-conditional-access-insights-reporting.md) 는 관리자에 게 테 넌 트의 Azure AD 조건부 액세스에 대 한 요약 보기를 제공 합니다. 관리자는 개별 정책을 선택 하는 기능을 사용 하 여 각 정책에서 수행 하는 작업을 더 잘 이해 하 고 실시간으로 변화를 모니터링할 수 있습니다. 통합 문서는 Azure Monitor에 저장 된 데이터를 스트리밍하 며 몇 분 내에 [이러한 지침에 따라](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)설정할 수 있습니다. 대시보드를 더 쉽게 검색할 수 있도록 Azure AD 조건부 액세스 메뉴 내의 새 정보 및 보고 탭으로 이동 했습니다.

---

### <a name="policy-details-blade-for-conditional-access-is-in-public-preview"></a>조건부 액세스에 대 한 정책 세부 정보 블레이드가 공개 미리 보기 상태입니다.

**유형:** 새로운 기능  
**서비스 범주:** 조건부 액세스  
**제품 기능:** Id 보안 & 보호

새 [정책 세부 정보 블레이드에](../conditional-access/troubleshoot-conditional-access.md) 는 조건부 액세스 정책을 평가 하는 동안 충족 된 할당, 조건 및 컨트롤이 표시 됩니다. 로그인 정보의 조건부 액세스 또는 보고서 전용 탭에서 행을 선택 하 여 블레이드에 액세스할 수 있습니다.

---

### <a name="new-query-capabilities-for-directory-objects-in-microsoft-graph-are-in-public-preview"></a>Microsoft Graph의 디렉터리 개체에 대 한 새로운 쿼리 기능은 공개 미리 보기로 제공 됩니다.

**유형:** 새로운 기능  
**서비스 범주:** MS Graph **제품 기능:** 개발자 환경

Microsoft Graph Directory 개체 Api에 대 한 새로운 기능이 도입 되어 개수, 검색, 필터 및 정렬 작업을 가능 하 게 합니다. 이를 통해 개발자는 메모리 내 필터링 및 정렬과 같은 해결 방법 없이 디렉터리 개체를 신속 하 게 쿼리할 수 있습니다. 이 [블로그 게시물](https://aka.ms/CountFilterMSGraphAAD)에서 자세히 알아보세요.

현재 공개 미리 보기 상태 이며 피드백을 찾고 있습니다. 이 [간단한 설문 조사](https://aka.ms/MsGraphAADSurveyDocs)를 통해 의견을 보내 주시기 바랍니다.

---

### <a name="configure-saml-based-single-sign-on-using-microsoft-graph-api-beta"></a>Microsoft Graph API (베타)를 사용 하 여 SAML 기반 Single Sign-On 구성

**유형:** 새로운 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능:** SSO
 
이제 베타에서 MS Graph Api를 사용 하 여 Azure AD 갤러리에서 응용 프로그램을 만들고 구성 하는 기능을 사용할 수 있습니다. 응용 프로그램의 여러 인스턴스에 대해 SAML 기반 Single Sign-On를 설정 해야 하는 경우 Microsoft Graph Api를 사용 하 여 [saml 기반 Single Sign-On의 구성을 자동화](/graph/application-saml-sso-configure-api)하는 시간을 절약 합니다.
 
---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---may-2020"></a>Azure AD 응용 프로그램 갤러리의 새로운 프로 비전 커넥터-2020 년 5 월

**유형:** 새로운 기능  
**서비스 범주:** 앱 프로비전  
**제품 기능:** 타사 통합
 
다음과 같은 통합된 새 앱에 대한 사용자 계정을 만들고, 업데이트하고, 삭제하는 작업을 자동화할 수 있습니다.

* [8x8](../saas-apps/8x8-provisioning-tutorial.md)
* [Juno Journey](../saas-apps/juno-journey-provisioning-tutorial.md)
* [MediusFlow](../saas-apps/mediusflow-provisioning-tutorial.md)
* [조직별 새 Relic](../saas-apps/new-relic-by-organization-provisioning-tutorial.md)
* [Oracle Cloud Infrastructure Console](../saas-apps/oracle-cloud-infratstructure-console-provisioning-tutorial.md)

자동화된 사용자 계정 프로비저닝을 사용하여 조직의 보안을 강화하는 방법에 대한 자세한 내용은 [Azure AD를 사용하여 SaaS 애플리케이션에 대한 사용자 프로비저닝 자동화](../app-provisioning/user-provisioning.md)를 참조하세요.

---

### <a name="saml-token-encryption-is-generally-available"></a>SAML 토큰 암호화를 일반적으로 사용할 수 있습니다.

**유형:** 새로운 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능:** SSO
 
[Saml 토큰 암호화](../manage-apps/howto-saml-token-encryption.md) 를 사용 하면 암호화 된 saml 어설션을 받도록 응용 프로그램을 구성할 수 있습니다. 이 기능은 이제 모든 클라우드에서 일반적으로 사용할 수 있습니다.
 
---

### <a name="group-name-claims-in-application-tokens-is-generally-available"></a>응용 프로그램 토큰의 그룹 이름 클레임이 일반적으로 사용 가능 합니다.

**유형:** 새로운 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능:** SSO
 
이제 토큰에서 발급 된 그룹 클레임을 응용 프로그램에 할당 된 그룹 으로만 제한할 수 있습니다.  이는 사용자가 많은 그룹의 구성원이 고 토큰 크기 제한을 초과할 위험이 있는 경우에 특히 중요 합니다. 이 새로운 기능을 사용 하는 경우 [토큰에 그룹 이름을 추가](../hybrid/how-to-connect-fed-group-claims.md) 하는 기능이 일반적으로 제공 됩니다.
 
---

### <a name="workday-writeback-now-supports-setting-work-phone-number-attributes"></a>이제 Workday 쓰기 저장에서 회사 전화 번호 특성 설정을 지원 합니다.

**유형:** 새로운 기능  
**서비스 범주:** 앱 프로비전  
**제품 기능:** Id 수명 주기 관리
 
이제 Workday 쓰기 저장 프로 비전 앱이 회사 전화 번호 및 모바일 번호 특성의 쓰기 저장을 지원 하도록 향상 되었습니다. 전자 메일 및 사용자 이름 외에도 이제 Workday 쓰기 저장 프로 비전 앱을 Azure AD에서 Workday로 이동 하는 전화 번호 값을 구성할 수 있습니다. 전화 번호 쓰기 저장을 구성 하는 방법에 대 한 자세한 내용은 [Workday 쓰기 저장](../saas-apps/workday-writeback-tutorial.md) 앱 자습서를 참조 하세요. 

---

### <a name="publisher-verification-preview"></a>게시자 확인 (미리 보기)

**유형:** 새로운 기능  
**서비스 범주:** 기타  
**제품 기능:** 개발자 환경
 
Microsoft ID 플랫폼과 통합하는 애플리케이션 개발자의 신뢰성을 관리자와 최종 사용자가 알 수 있도록 도와주는 것이 게시자 확인(미리 보기)입니다. 자세한 내용은 [게시자 확인 (미리 보기)](../develop/publisher-verification-overview.md)을 참조 하세요.
 
---

### <a name="authorization-code-flow-for-single-page-apps"></a>단일 페이지 앱에 대 한 인증 코드 흐름

**유형:** 변경 된 기능 **서비스 범주:** 인증 **제품 기능:** 개발자 환경

최신 브라우저에서 [Safari ITP와 같은 타사 쿠키 제한](../develop/reference-third-party-cookies-spas.md)때문에, SPAS는 SSO를 유지 하기 위해 암시적 흐름이 아닌 권한 부여 코드 흐름을 사용 해야 합니다. MSAL.js v 2. x는 이제 인증 코드 흐름을 지원 합니다. Azure Portal에 대 한 해당 업데이트가 있으므로 SPA를 "spa" 형식으로 업데이트 하 고 인증 코드 흐름을 사용할 수 있습니다. 지침은 [빠른 시작: 인증 사용자 로그인 및 인증 코드 흐름을 사용 하 여 JAVASCRIPT SPA에서 액세스 토큰 가져오기](../develop/quickstart-v2-javascript-auth-code.md)를 참조 하세요.

---

### <a name="improved-filtering-for-devices-is-in-public-preview"></a>공개 미리 보기로 제공 되는 장치에 대 한 필터링 향상

**유형:** 변경 된 기능   
**서비스 범주:** 장치 관리 **제품 기능:** 장치 수명 주기 관리
 
이전에는 사용할 수 있는 유일한 필터는 "사용" 및 "작업 날짜"입니다. 이제 OS 유형, 조인 유형, 규정 준수 등을 비롯 한 [더 많은 속성을 통해 장치 목록을 필터링](../devices/device-management-azure-portal.md#device-list-filtering-preview)할 수 있습니다. 이러한 추가 기능을 통해 특정 장치를 쉽게 찾을 것입니다.

---

### <a name="the-new-app-registrations-experience-for-azure-ad-b2c-is-now-generally-available"></a>이제 Azure AD B2C의 새로운 앱 등록 환경이 출시 되었습니다.

**유형:** 변경 된 기능   
**서비스 범주:** B2C - 소비자 ID 관리  
**제품 기능:** Id 수명 주기 관리
 
이제 Azure AD B2C에 대 한 새로운 앱 등록 환경이 출시 되었습니다. 

이전에는 레거시 ' 응용 프로그램 ' 환경을 사용 하 여 응용 프로그램의 나머지 부분과 별도로 B2C 소비자 지향 응용 프로그램을 관리 해야 했습니다. Azure의 여러 위치에서 다양 한 앱 만들기 환경을 의미 합니다.

새 환경에서는 모든 B2C 앱 등록 및 Azure AD 앱 등록을 한 곳에 표시 하 고 일관 된 방식으로 관리 하는 방법을 제공 합니다. Azure AD B2C 리소스를 프로그래밍 방식으로 관리 하기 위해 Microsoft Graph에 대 한 액세스 권한이 있는 앱 또는 고객 관련 앱을 관리 해야 하는지 여부에 관계 없이 작업을 수행 하는 한 가지 방법만 배워야 합니다.

Azure AD B2C 서비스를 탐색 하 고 앱 등록 블레이드를 선택 하 여 새 환경에 연결할 수 있습니다. 환경에도 Azure Active Directory 서비스에서 액세스할 수 있습니다.

Azure AD B2C 앱 등록 환경은 Azure AD 테 넌 트에 대 한 일반 [앱 등록 환경을](https://developer.microsoft.com/identity/blogs/new-app-registrations-experience-is-now-generally-available/) 기반으로 하지만 Azure AD B2C에 맞게 조정 됩니다. 레거시 "응용 프로그램" 환경은 향후 더 이상 사용 되지 않습니다.

자세한 내용은 [Azure AD B2C에 대 한 새로운 앱 등록 환경](../../active-directory-b2c/app-registrations-training-guide.md)을 참조 하세요.

---
