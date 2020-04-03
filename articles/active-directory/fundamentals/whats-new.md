---
title: 새로운 기능 릴리스 정보 - Azure Active Directory | Microsoft Docs
description: Azure Active Directory의 최신 릴리스 정보, 알려진 문제, 버그 수정, 사용되지 않는 기능, 예정된 변경 내용 등을 알아봅니다.
services: active-directory
author: msmimart
manager: daveba
featureFlags:
- clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: mimart
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: b960bf63cae8b55ae2f66b6b809ee0d11229a312
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80582786"
---
# <a name="whats-new-in-azure-active-directory"></a>Azure Active Directory의 새로운 기능

>이 URL을 복사하고 붙여넣기하여 이 페이지를 다시 방문할 시기에 `https://docs.microsoft.com/api/search/rss?search=%22release+notes+for+azure+AD%22&locale=en-us` 대한 알림을 RSS](./media/whats-new/feed-icon-16x16.png) 피드 리더 아이콘 피드 리더에 붙여넣습니다. ![

Azure AD는 지속적인 향상되고 있습니다. 최신 개발 정보를 확인할 수 있도록 이 문서에서는 다음에 대한 정보를 제공합니다.

- 최신 릴리스
- 알려진 문제
- 버그 수정
- 사용되지 않는 기능
- 변경 계획

이 페이지는 매월 업데이트되므로 정기적으로 다시 방문해 주세요. 6개월 이상된 항목을 찾으려는 경우 [Azure Active Directory의 새로운 기능 아카이브](whats-new-archive.md)에서 찾을 수 있습니다.

---

## <a name="march-2020"></a>2020년 3월

### <a name="unmanaged-azure-active-directory-accounts-in-b2b-update-for-march-2021"></a>2021년 3월 B2B 업데이트에서 관리되지 않는 Azure Active Directory 계정

**유형:** 변경 계획  
**서비스 범주:** B2B  
**제품 기능:** B2B/B2C
 
**2021년 3월 31일부터**Microsoft는 B2B 공동 작업 시나리오에 대해 관리되지 않는 Azure Active Directory(Azure AD) 계정 및 테넌드를 만들어 초대 사용의 상환을 더 이상 지원하지 않습니다. 이에 대비하여 일회성 암호 인증을 [전자 메일로](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode)선택하도록 선택하는 것이 좋습니다.

### <a name="users-with-the-default-access-role-will-be-in-scope-for-provisioning"></a>기본 액세스 역할이 있는 사용자는 프로비저닝 범위에 속합니다.

**유형:** 변경 계획  
**서비스 범주:** 앱 프로비전  
**제품 기능:** ID 라이프사이클 관리
 
지금까지 기본 액세스 역할이 있는 사용자는 프로비저닝 범위를 벗어났습니다. 고객이 이 역할을 가진 사용자가 프로비저닝 범위에 있기를 원한다는 피드백을 들었습니다. 모든 새 프로비저닝 구성을 통해 기본 액세스 역할을 가진 사용자가 프로비전할 수 있도록 변경 작업을 배포하고 있습니다. 점차적으로 이 역할의 프로비전 사용자를 지원하기 위해 기존 프로비저닝 구성에 대한 동작을 변경합니다. 고객 조치가 필요하지 않습니다. 이 변경이 완료되면 [설명서에](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-config-problem-no-users-provisioned) 업데이트를 게시할 것입니다.

---

### <a name="azure-ad-b2b-collaboration-will-be-available-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet-tenants"></a>Azure AD B2B 공동 작업은 21Vianet(Azure China 21Vianet) 테넌티에서 운영하는 Microsoft Azure에서 사용할 수 있습니다.

**유형:** 변경 계획  
**서비스 범주:** B2B  
**제품 기능:** B2B/B2C
 
Azure AD B2B 공동 작업 기능은 21Vianet(Azure China 21Vianet) 테넌트가 운영하는 Microsoft Azure에서 사용할 수 있으며, Azure China 21Vianet 테넌트의 사용자가 다른 Azure China 21Vianet 테넌트의 사용자와 원활하게 공동 작업할 수 있습니다. [Azure AD B2B 공동 작업에 대해 자세히 알아봅니다.](https://docs.microsoft.com/azure/active-directory/b2b/)

---
 
### <a name="azure-ad-b2b-collaboration-invitation-email-redesign"></a>Azure AD B2B 공동 작업 초대 전자 메일 재설계

**유형:** 변경 계획  
**서비스 범주:** B2B  
**제품 기능:** B2B/B2C
 
사용자를 디렉터리로 초대하기 위해 Azure AD B2B 공동 작업 초대 서비스에서 보내는 [전자 메일은](https://docs.microsoft.com/azure/active-directory/b2b/invitation-email-elements) 초대 정보와 사용자의 다음 단계를 더 명확하게 하기 위해 다시 디자인됩니다.

---

### <a name="homerealmdiscovery-policy-changes-will-appear-in-the-audit-logs"></a>HomeRealm디스커버리 정책 변경 사항은 감사 로그에 표시됩니다.

**유형:** 고정  
**서비스 범주:** 감사  
**제품 기능:** 모니터링 및 보고
 
[HomeRealmDiscovery 정책의](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-authentication-for-federated-users-portal) 변경 내용이 감사 로그에 포함되지 않은 버그를 수정했습니다. 이제 정책이 변경된 시기와 방법, 누가 누구인지 확인할 수 있습니다. 

---

### <a name="azure-ad-b2b-collaboration-available-in-azure-government-tenants"></a>Azure 정부 테넌에서 사용할 수 있는 Azure AD B2B 공동 작업

**유형:** 새로운 기능  
**서비스 범주:** B2B  
**제품 기능:** B2B/B2C
 
이제 일부 Azure 정부 테넌자 간에 Azure AD B2B 공동 작업 기능을 사용할 수 있습니다.  테넌트가 이러한 기능을 사용할 수 있는지 확인하려면 [Azure 미국 정부 테넌트에서 B2B 공동 작업을 사용할 수 있는지 어떻게 알 수 있습니까?](https://docs.microsoft.com/azure/active-directory/b2b/current-limitations#how-can-i-tell-if-b2b-collaboration-is-available-in-my-azure-us-government-tenant)

---

### <a name="azure-monitor-integration-for-azure-logs-is-now-available-in-azure-government"></a>Azure 로그에 대한 Azure 모니터 통합은 이제 Azure 정부에서 사용할 수 있습니다.

**유형:** 새로운 기능  
**서비스 범주:** 보고  
**제품 기능:** 모니터링 및 보고
 
Azure AD 로그와의 Azure 모니터 통합을 Azure 정부에서 사용할 수 있습니다. Azure AD 로그(감사 및 로그인 로그)를 저장소 계정, 이벤트 허브 및 로그 분석으로 라우팅할 수 있습니다. Azure AD 시나리오에 [대한 보고 및 모니터링을 위한](https://docs.microsoft.com/azure/active-directory/reports-monitoring/plan-monitoring-and-reporting) 자세한 [설명서와](https://aka.ms/aadlogsinamd) 배포 계획을 확인하십시오.

---

### <a name="identity-protection-refresh-in-azure-government"></a>Azure 정부의 ID 보호 새로 고침

**유형:** 새로운 기능  
**서비스 범주:** ID 보호  
**제품 기능:** ID 보안 & 보호

Microsoft [Azure 정부 포털에서](https://portal.azure.us/)새로 고친 [Azure AD ID 보호](https://aka.ms/IdentityProtectionDocs) 환경을 출시했다는 것을 공유하게 되어 기쁩니다. 자세한 내용은 공지 [블로그 게시물을](https://techcommunity.microsoft.com/t5/public-sector-blog/identity-protection-refresh-in-microsoft-azure-government/ba-p/1223667)참조하십시오.

---

### <a name="disaster-recovery-download-and-store-your-provisioning-configuration"></a>재해 복구: 프로비저닝 구성 다운로드 및 저장

**유형:** 새로운 기능  
**서비스 범주:** 앱 프로비전  
**제품 기능:** ID 라이프사이클 관리
 
Azure AD 프로비전 서비스는 다양한 구성 기능을 제공합니다. 고객은 나중에 참조하거나 알려진 양호한 버전으로 롤백할 수 있도록 구성을 저장할 수 있어야 합니다. 프로비저닝 구성을 JSON 파일로 다운로드하고 필요할 때 업로드할 수 있는 기능이 추가되었습니다. [자세히 알아보기](https://docs.microsoft.com/azure/active-directory/app-provisioning/export-import-provisioning-configuration).

---
 
### <a name="sspr-self-service-password-reset-now-requires-two-gates-for-admins-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet"></a>SSPR (셀프 서비스 암호 재설정) 이제 21Vianet (Azure 중국 21Vianet)에 의해 운영 되는 마이크로소프트 Azure에서 관리자에 대 한 두 개의 게이트필요 

**유형:** 변경된 기능  
**서비스 범주:** 셀프 서비스 암호 재설정  
**제품 기능:** ID 보안 & 보호
 
이전에는 21Vianet(Azure China 21Vianet)에서 운영하는 Microsoft Azure에서 자체 서비스 암호 재설정(SSPR)을 사용하여 자체 암호를 재설정하는 관리자는 자신의 ID를 증명하기 위해 하나의 "게이트"(챌린지)만 필요했습니다. 공용 및 기타 국가 클라우드에서 관리자는 일반적으로 SSPR을 사용할 때 두 개의 게이트를 사용하여 신원을 증명해야 합니다. 그러나 Azure China 21Vianet에서 SMS 또는 전화 통화를 지원하지 않았기 때문에 관리자가 한 게이트 암호를 재설정할 수 있었습니다.

Azure China 21Vianet과 퍼블릭 클라우드 간에 SSPR 기능 패리티를 만들고 있습니다. 앞으로 관리자는 SSPR을 사용할 때 두 개의 게이트를 사용해야 합니다. SMS, 전화 통화 및 인증자 앱 알림 및 코드가 지원됩니다. [자세히 알아보기](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-policy#administrator-reset-policy-differences).

---

### <a name="password-length-is-limited-to-256-characters"></a>암호 길이는 256자로 제한됩니다.

**유형:** 변경된 기능  
**서비스 범주:** 인증(로그인)  
**제품 기능:** 사용자 인증
 
Azure AD 서비스의 안정성을 보장하기 위해 이제 사용자 암호의 길이가 256자로 제한됩니다. 이보다 긴 암호를 가진 사용자는 관리자에게 문의하거나 셀프 서비스 암호 재설정 기능을 사용하여 후속 로그인 시 암호를 변경하라는 메시지가 표시됩니다.

이 변경 사항은 2020년 3월 13일 오전 10시 PST(18:00 UTC)에서 활성화되었으며 오류는 AADSTS 50052, 잘못된PasswordExceedsMaxLength입니다. 자세한 내용은 [주요 변경 사항 알림을](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#user-passwords-will-be-restricted-to-256-characters) 참조하십시오.

---

### <a name="azure-ad-sign-in-logs-are-now-available-for-all-free-tenants-through-the-azure-portal"></a>이제 Azure 포털을 통해 모든 무료 테넌에서 Azure AD 로그인 로그를 사용할 수 있습니다.

**유형:** 변경된 기능  
**서비스 범주:** 보고  
**제품 기능:** 모니터링 및 보고
 
이제 부터 무료 테넌스가 있는 고객은 Azure 포털에서 최대 7일 동안 [Azure AD 로그인 로그인에](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins) 액세스할 수 있습니다. 이전에는 Azure Active Directory Premium 라이선스를 가진 고객만 로그인 로그를 사용할 수 있었습니다. 이 변경으로 모든 테넌티는 포털을 통해 이러한 로그에 액세스할 수 있습니다.

> [!NOTE]
> 고객은 Microsoft 그래프 API 및 Azure 모니터를 통해 로그인 로그인에 액세스하려면 프리미엄 라이선스(Azure Active Directory Premium P1 또는 P2)가 필요합니다.

---

### <a name="deprecation-of-directory-wide-groups-option-from-groups-general-settings-on-azure-portal"></a>Azure 포털의 일반 설정 그룹에서 디렉터리 전체 그룹 옵션 사용 중단

**유형:** 사용되지 않음  
**서비스 범주:** 그룹 관리  
**제품 기능:** 협업

고객이 자신의 요구에 가장 적합한 디렉터리 전체 그룹을 만들 수 있는 보다 유연한 방법을 제공하기 위해 Azure Portal의 **그룹** > **일반** 설정에서 동적 [그룹 설명서에](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership)대한 링크로 **디렉터리 전체 그룹** 옵션을 대체했습니다. 관리자가 게스트 사용자를 포함하거나 제외하는 모든 사용자 그룹을 만들 수 있도록 더 많은 지침을 포함하도록 설명서를 개선했습니다.

---

## <a name="february-2020"></a>2020년 2월

### <a name="upcoming-changes-to-custom-controls"></a>사용자 지정 컨트롤에 대한 향후 변경 사항

**유형:** 변경 계획  
**서비스 범주:** Mfa  
**제품 기능:** ID 보안 & 보호
 
현재 사용자 지정 컨트롤 미리 보기를 파트너 제공 인증 기능이 Azure Active Directory 관리자 및 최종 사용자 환경과 원활하게 작동할 수 있는 접근 방식으로 대체할 계획입니다. 오늘날 파트너 MFA 솔루션은 다음과 같은 제한 사항에 직면해 있습니다. 다른 주요 시나리오에서는 단계별 인증을 위해 MFA역할을 하지 않습니다. 최종 사용자 또는 관리 자격 증명 관리 기능과 통합되지 않습니다. 새로운 구현을 통해 파트너에서 제공한 인증 요소는 등록, 사용, MFA 클레임, 인증 단계, 보고 및 로깅을 비롯한 주요 시나리오에 대한 기본 제공 요소와 함께 작동할 수 있습니다. 

사용자 지정 컨트롤은 일반 공급에 도달할 때까지 새 디자인과 함께 미리 보기에서 계속 지원됩니다. 이 시점에서 고객에게 새로운 디자인으로 마이그레이션할 시간을 제공합니다. 현재 접근 방식의 한계로 인해 새 설계를 사용할 수 있게 될 때까지 새 공급자를 온보온하지 않습니다. 우리는 고객 및 제공 업체와 긴밀히 협력하고 있으며 가까워지면 일정을 전달할 것입니다. [자세히 알아보기](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/upcoming-changes-to-custom-controls/ba-p/1144696#).

---

### <a name="identity-secure-score---mfa-improvement-action-updates"></a>ID 보안 점수 - MFA 개선 작업 업데이트

**유형:** 변경 계획  
**서비스 범주:** Mfa  
**제품 기능:** ID 보안 & 보호
 
비즈니스와 함께 작동하는 정책을 적용하면서 최대 보안을 보장해야 하는 기업의 필요성을 반영하기 위해 Microsoft Secure Score는 다단계 인증(MFA)을 중심으로 한 세 가지 개선 작업을 제거하고 두 가지를 추가합니다.

다음과 같은 개선 작업이 제거됩니다.

- MFA에 대한 모든 사용자 등록
- 모든 사용자용 MFA 필요
- Azure AD 권한 역할에 대한 MFA 필요

다음과 같은 개선 작업이 추가됩니다.

- 모든 사용자가 보안 액세스를 위해 MFA를 완료할 수 있도록 보장
- 관리 역할에 대한 MFA 필요

이러한 새로운 개선 작업을 수행하려면 디렉터리 전체에서 MFA에 대한 사용자 또는 관리자를 등록하고 조직의 요구에 맞는 올바른 정책 집합을 설정해야 합니다. 주요 목표는 모든 사용자와 관리자가 여러 가지 요인 또는 위험 기반 신원 확인 프롬프트를 사용하여 인증할 수 있도록 하면서 유연성을 확보하는 것입니다. 이는 Microsoft가 MFA에 대해 사용자에게 이의를 제기할 시기를 결정하거나 범위별 결정을 적용하는 여러 정책을 갖는 보안 기본값을 설정하는 형식을 취할 수 있습니다. 이러한 개선 작업 업데이트의 일부로 기준 보호 정책은 더 이상 점수 매기기 계산에 포함되지 않습니다. [마이크로소프트 보안 점수에 오는 것에 대](https://docs.microsoft.com/microsoft-365/security/mtp/microsoft-secure-score-whats-coming?view=o365-worldwide)한 자세한 내용은 .

---

### <a name="azure-ad-domain-services-sku-selection"></a>Azure AD 도메인 서비스 SKU 선택

**유형:** 새로운 기능  
**서비스 범주:** Azure AD Domain Services  
**제품 기능:** Azure AD Domain Services
 
Azure AD 도메인 서비스 고객이 인스턴스에 대한 성능 수준을 보다 유연게 선택할 수 있다는 피드백을 들었습니다. 2020년 2월 1일부터 Azure AD가 개체 수에 따라 성능 및 가격 책정 계층을 결정하는 동적 모델에서 자체 선택 모델로 전환했습니다. 이제 고객은 자신의 환경과 일치하는 성능 계층을 선택할 수 있습니다. 또한 이러한 변경을 통해 리소스 포리스트와 같은 새로운 시나리오및 일일 백업과 같은 프리미엄 기능을 사용할 수 있습니다. 이제 모든 SCO에 대해 개체 수가 무제한이지만 각 계층에 대한 개체 수 제안을 계속 제공합니다.

**즉각적인 고객 조치가 필요하지 않습니다.** 기존 고객의 경우 2020년 2월 1일에 사용 중이던 동적 계층이 새 기본 계층을 결정합니다. 이 변경의 결과로 가격 이나 성능 에 영향을 미치지 않습니다. 앞으로 Azure AD DS 고객은 디렉터리 크기 및 워크로드 특성이 변경될 때 성능 요구 사항을 평가해야 합니다. 서비스 계층 간 전환은 중단 시간 없는 작업으로 계속 진행되며 디렉터리 증가에 따라 고객을 자동으로 새 계층으로 이동하지 않습니다. 또한 가격 인상은 없으며 새로운 가격은 현재 청구 모델과 일치합니다. 자세한 내용은 Azure [AD DS SCO 설명서](https://docs.microsoft.com/azure/active-directory-domain-services/administration-concepts#azure-ad-ds-skus) 및 [Azure AD 도메인 서비스 가격 페이지를](https://azure.microsoft.com/pricing/details/active-directory-ds/)참조하십시오.

---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery---february-2020"></a>Azure AD 앱 갤러리에서 사용할 수 있는 새로운 페더레이션 앱 - 2020년 2월

**유형:** 새로운 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능:** 제3자 통합
 
2020년 2월에 페더레이션 을 지원하는 31개의 새로운 앱을 앱 갤러리에 추가했습니다. 

[IamIP 특허 플랫폼,](https://docs.microsoft.com/azure/active-directory/saas-apps/iamip-patent-platform-tutorial) [경험 클라우드,](https://docs.microsoft.com/azure/active-directory/saas-apps/experience-cloud-tutorial) [NS1 SSO 용 Azure,](https://docs.microsoft.com/azure/active-directory/saas-apps/ns1-sso-azure-tutorial) [바라쿠다 이메일 보안 서비스,](https://ess.barracudanetworks.com/sso/azure) [ABa 보고](https://myaba.co.uk/client-access/signin/auth/msad), [위기 의 경우 - 온라인 포털,](https://docs.microsoft.com/azure/active-directory/saas-apps/in-case-of-crisis-online-portal-tutorial) [BIC 클라우드 디자인,](https://docs.microsoft.com/azure/active-directory/saas-apps/bic-cloud-design-tutorial) [양봉가 Azure AD 데이터 커넥터,](https://docs.microsoft.com/azure/active-directory/saas-apps/beekeeper-azure-ad-data-connector-tutorial) [콘 페리 평가,](https://www.kornferry.com/solutions/kf-digital/kf-assess) [Verkada 명령,](https://docs.microsoft.com/azure/active-directory/saas-apps/verkada-command-tutorial) [스플래시 탑,](https://docs.microsoft.com/azure/active-directory/saas-apps/splashtop-tutorial) [Syxsense](https://docs.microsoft.com/azure/active-directory/saas-apps/syxsense-tutorial), [EAB 탐색](https://docs.microsoft.com/azure/active-directory/saas-apps/eab-navigate-tutorial), 새로운 유물 ( [한정 된 릴리스)](https://docs.microsoft.com/azure/active-directory/saas-apps/new-relic-limited-release-tutorial), [Thulium,](https://admin.thulium.com/login/instance) [티켓 관리자,](https://docs.microsoft.com/azure/active-directory/saas-apps/ticketmanager-tutorial) [팀,](https://links.officeatwork.com/templatechooser-download-teams) [꿀벌,](https://www.beesy.me/index.php/site/login) [건강 지원 시스템,](https://docs.microsoft.com/azure/active-directory/saas-apps/health-support-system-tutorial) [벽화,](https://app.mural.co/signup) [하이브,](https://docs.microsoft.com/azure/active-directory/saas-apps/hive-tutorial) [라바도,](https://appsource.microsoft.com/product/web-apps/lavaloon.lavado_standard?tab=Overview) [웨이크 렛,](https://wakelet.com/login) [Firmex VDR,](https://docs.microsoft.com/azure/active-directory/saas-apps/firmex-vdr-tutorial) [교사와 학교를위한 ThingLink,](https://www.thinglink.com/) [코다](https://docs.microsoft.com/azure/active-directory/saas-apps/coda-tutorial), [NearpodApp,](https://nearpod.com/signup/?oc=Microsoft&utm_campaign=Microsoft&utm_medium=site&utm_source=product) [WEDO](https://docs.microsoft.com/azure/active-directory/saas-apps/wedo-tutorial), [InvitePeople,](https://invitepeople.com/login) [재인쇄 데스크 - 기사 갤럭시](https://docs.microsoft.com/azure/active-directory/saas-apps/reprints-desk-article-galaxy-tutorial), [TeamViewer](https://docs.microsoft.com/azure/active-directory/saas-apps/teamviewer-tutorial)

 
앱에 대한 자세한 내용은 [Azure Active Directory와 SaaS 애플리케이션 통합](https://aka.ms/appstutorial)을 참조하세요. Azure AD 앱 갤러리에 애플리케이션을 나열하는 방법에 대한 자세한 내용은 [Azure Active Directory 애플리케이션 갤러리에 애플리케이션 나열](https://aka.ms/azureadapprequest)을 참조하세요.

---
 
### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---february-2020"></a>Azure AD 응용 프로그램 갤러리의 새 프로비저닝 커넥터 - 2020년 2월

**유형:** 새로운 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능:** 제3자 통합
 
이제 새로 통합된 앱에 대한 사용자 계정을 생성, 업데이트 및 삭제할 수 있습니다.

- [Mixpanel](https://docs.microsoft.com/azure/active-directory/saas-apps/mixpanel-provisioning-tutorial)
- [TeamViewer](https://docs.microsoft.com/azure/active-directory/saas-apps/teamviewer-provisioning-tutorial)
- [Azure Databricks](https://docs.microsoft.com/azure/active-directory/saas-apps/azure-databricks-scim-connector-provisioning-tutorial)
- [PureCloud by Genesys](https://docs.microsoft.com/azure/active-directory/saas-apps/purecloud-by-genesys-provisioning-tutorial)
- [Zapier](https://docs.microsoft.com/azure/active-directory/saas-apps/zapier-provisioning-tutorial)

자동화된 사용자 계정 프로비전을 사용하여 조직의 보안을 높이는 방법에 대한 자세한 내용은 [Azure AD를 사용하여 SaaS 응용 프로그램에 대한 사용자 프로비저닝 자동화를](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)참조하십시오.

---
 
### <a name="azure-ad-support-for-fido2-security-keys-in-hybrid-environments"></a>하이브리드 환경에서 FIDO2 보안 키에 대한 Azure AD 지원

**유형:** 새로운 기능  
**서비스 범주:** 인증(로그인)  
**제품 기능:** 사용자 인증
 
하이브리드 환경에서 FIDO2 보안 키에 대한 Azure AD 지원의 공개 미리 보기를 발표합니다. 이제 사용자는 FIDO2 보안 키를 사용하여 Windows 10 장치에 가입한 하이브리드 Azure AD에 로그인하고 온-프레미스 및 클라우드 리소스에 원활하게 로그온할 수 있습니다. Azure AD 조인 장치에서 FIDO2 지원에 대한 공개 미리 보기를 처음 시작한 이래로 하이브리드 환경에 대한 지원은 암호가 없는 고객으로부터 가장 많이 요청된 기능입니다. 생체 인식 및 공개/개인 키 암호화와 같은 고급 기술을 사용한 암호 없는 인증은 보안이 유지되는 동시에 편리함과 사용 편의성을 제공합니다. 이 공개 미리 보기를 사용하면 FIDO2 보안 키와 같은 최신 인증을 사용하여 기존 Active Directory 리소스에 액세스할 수 있습니다. 자세한 내용은 [SSO에서 온-프레미스 리소스로](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-security-key-on-premises)이동하십시오. 

시작하려면 [테넌트에 대한 FIDO2 보안 키를 활성화하여](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-security-key) 단계별 지침을 확인하십시오. 

---
 
### <a name="the-new-my-account-experience-is-now-generally-available"></a>이제 새 내 계정 환경을 일반적으로 사용할 수 있습니다.

**유형:** 변경된 기능  
**서비스 범주:** 내 프로필/계정  
**제품 기능:** 최종 사용자 환경
 
모든 최종 사용자 계정 관리 요구 사항에 맞는 원스톱 샵인 내 계정이 이제 일반적으로 사용할 수 있습니다! 최종 사용자는 URL을 통해 또는 새 내 앱 환경의 헤더에서 이 새 사이트에 액세스할 수 있습니다. [내 계정 포털 개요에서](https://docs.microsoft.com/azure/active-directory/user-help/my-account-portal-overview)새로운 환경이 제공하는 모든 셀프 서비스 기능에 대해 자세히 알아보십시오.

---
 
### <a name="my-account-site-url-updating-to-myaccountmicrosoftcom"></a>내 계정 사이트 URL이 myaccount.microsoft.com 업데이트됨

**유형:** 변경된 기능  
**서비스 범주:** 내 프로필/계정  
**제품 기능:** 최종 사용자 환경
 
새로운 내 계정 최종 사용자 환경은 다음 `https://myaccount.microsoft.com` 달에 해당 URL을 업데이트합니다. 내 계정 [포털 도움말에서](https://docs.microsoft.com/azure/active-directory/user-help/my-account-portal-overview)최종 사용자에게 제공하는 모든 계정 셀프 서비스 기능 및 환경및 모든 계정 셀프 서비스 기능에 대한 자세한 내용을 알아보십시오.

---
 
## <a name="january-2020"></a>2020년 1월
 
### <a name="the-new-my-apps-portal-is-now-generally-available"></a>이제 새로운 내 앱 포털을 일반적으로 사용할 수 있습니다.

**유형:** 변경 계획  
**서비스 범주:** 내 앱  
**제품 기능:** 최종 사용자 환경
 
조직을 현재 일반적으로 사용할 수 있는 새 내 앱 포털로 업그레이드하십시오! 내 앱 포털에서 컬렉션 만들기에서 새 포털 및 [컬렉션에 대한](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-collections)자세한 내용을 알아보십시오.

---
 
### <a name="workspaces-in-azure-ad-have-been-renamed-to-collections"></a>Azure AD의 작업 영역이름이 컬렉션으로 변경되었습니다.

**유형:** 변경된 기능  
**서비스 범주:** 내 앱   
**제품 기능:** 최종 사용자 환경
 
작업 영역, 필터 관리자는 사용자 응용 프로그램을 구성 하도록 구성할 수 있습니다., 이제 컬렉션이라고 합니다. [내 앱 포털에서 컬렉션 만들기에서](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-collections)구성 방법을 자세히 알아보십시오.

---
 
### <a name="azure-ad-b2c-phone-sign-up-and-sign-in-using-custom-policy-public-preview"></a>Azure AD B2C 전화 등록 및 사용자 지정 정책을 사용하여 로그인(공개 미리 보기)

**유형:** 새로운 기능  
**서비스 범주:** B2C - 소비자 ID 관리  
**제품 기능:** B2B/B2C
 
개발자와 기업은 전화 번호 등록 및 로그인을 통해 고객이 SMS를 통해 사용자의 전화 번호로 전송된 일회성 암호를 사용하여 등록하고 로그인할 수 있도록 허용할 수 있습니다. 이 기능을 사용하면 고객이 휴대폰에 액세스할 수 없는 경우 전화번호를 변경할 수 있습니다. 사용자 지정 정책의 힘으로 전화 등록 및 로그인을 통해 개발자와 기업은 페이지 사용자 지정을 통해 브랜드를 전달할 수 있습니다. [Azure AD B2C에서 사용자 지정 정책을 사용하여 전화 등록 및 로그인을 설정하는](https://docs.microsoft.com/azure/active-directory-b2c/phone-authentication)방법을 알아보십시오.
 
---
 
### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---january-2020"></a>Azure AD 응용 프로그램 갤러리의 새 프로비저닝 커넥터 - 2020년 1월

**유형:** 새로운 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능:** 제3자 통합
 
이제 새로 통합된 앱에 대한 사용자 계정을 생성, 업데이트 및 삭제할 수 있습니다.

- [Promapp]( https://docs.microsoft.com/azure/active-directory/saas-apps/promapp-provisioning-tutorial)
- [Zscaler Private Access](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-private-access-provisioning-tutorial)

자동화된 사용자 계정 프로비전을 사용하여 조직의 보안을 높이는 방법에 대한 자세한 내용은 [Azure AD를 사용하여 SaaS 응용 프로그램에 대한 사용자 프로비저닝 자동화를](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)참조하십시오.

---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery---january-2020"></a>Azure AD 앱 갤러리에서 사용할 수 있는 새로운 페더레이션 앱 - 2020년 1월

**유형:** 새로운 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능:** 제3자 통합
 
2020년 1월에 는 페더레이션 지원이 있는 33개의 새로운 앱을 앱 갤러리에 추가했습니다. 

[JOSA,](https://docs.microsoft.com/azure/active-directory/saas-apps/josa-tutorial)빠른 [에지 클라우드,](https://docs.microsoft.com/azure/active-directory/saas-apps/fastly-edge-cloud-tutorial) [테라폼 엔터프라이즈,](https://docs.microsoft.com/azure/active-directory/saas-apps/terraform-enterprise-tutorial) [스핀트르 SSO,](https://docs.microsoft.com/azure/active-directory/saas-apps/spintr-sso-tutorial) [아비봇 넷로지스틱,](https://azuremarketplace.microsoft.com/marketplace/apps/aad.abibotnetlogistik) `https://login.skykick.com/login?state=g6Fo2SBTd3M5Q0xBT0JMd3luS2JUTGlYN3pYTE1remJQZnR1c6N0aWTZIDhCSkwzYVQxX2ZMZjNUaWxNUHhCSXg2OHJzbllTcmYto2NpZNkgM0h6czk3ZlF6aFNJV1VNVWQzMmpHeFFDbDRIMkx5VEc&client=3Hzs97fQzhSIWUMUd32jGxQCl4H2LyTG&protocol=oauth2&audience=https://papi.skykick.com&response_type=code&redirect_uri=https://portal.skykick.com/callback&scope=openid%20profile%20offline_access` [업샷,](https://docs.microsoft.com/azure/active-directory/saas-apps/upshotly-tutorial) [리브봇,](https://leavebot.io/#home) [데이터캠프,](https://docs.microsoft.com/azure/active-directory/saas-apps/datacamp-tutorial) [트립액션,](https://docs.microsoft.com/azure/active-directory/saas-apps/tripactions-tutorial) [스마트워크,](https://www.intumit.com/english/SmartWork.html)닷컴 [모니터,](https://docs.microsoft.com/azure/active-directory/saas-apps/dotcom-monitor-tutorial) [AssetSonar](https://docs.microsoft.com/azure/active-directory/saas-apps/assetsonar-tutorial) [SSOGEN - 오라클 E-비즈니스 스위트용 Azure AD SSO 게이트웨이 - EBS, 피플소프트, JDE,](https://docs.microsoft.com/azure/active-directory/saas-apps/ssogen-tutorial)호스팅 [MyCirqa SSO,](https://docs.microsoft.com/azure/active-directory/saas-apps/hosted-mycirqa-sso-tutorial) [유후 자산 관리 플랫폼,](https://docs.microsoft.com/azure/active-directory/saas-apps/yuhu-property-management-platform-tutorial) `https://sites.lumapps.com/login` [Akari Virtual Assistant](https://akari.io/akari-virtual-assistant/) [업워크 엔터프라이즈,](https://docs.microsoft.com/azure/active-directory/saas-apps/upwork-enterprise-tutorial) [탤런트소프트,](https://docs.microsoft.com/azure/active-directory/saas-apps/talentsoft-tutorial) [마이크로소프트 팀을 위한 SmartDB,](http://teams.smartdb.jp/login/) [프레스페이지,](https://docs.microsoft.com/azure/active-directory/saas-apps/presspage-tutorial) [ContractSafe Saml2 SSO,](https://docs.microsoft.com/azure/active-directory/saas-apps/contractsafe-saml2-sso-tutorial) `https://app.sandwai.com/` [Maxient conduct Manager 소프트웨어,](https://docs.microsoft.com/azure/active-directory/saas-apps/maxient-conduct-manager-software-tutorial) [헬프시프트,](https://docs.microsoft.com/azure/active-directory/saas-apps/helpshift-tutorial) [PortalTalk 365,](https://www.portaltalk.com/) [CoreView,](https://portal.coreview.com/) [스켈리치 클라우드 오피스365 커넥터,](https://laxmi.squelch.io/login) [핑플로우 인증](https://app-staging.pingview.io/), [프린터로직 SaaS,](https://docs.microsoft.com/azure/active-directory/saas-apps/printerlogic-saas-tutorial)연결 , [Taskize Connect](https://docs.microsoft.com/azure/active-directory/saas-apps/taskize-connect-tutorial) [EZRentOut](https://docs.microsoft.com/azure/active-directory/saas-apps/ezrentout-tutorial)

앱에 대한 자세한 내용은 [Azure Active Directory와 SaaS 애플리케이션 통합](https://aka.ms/appstutorial)을 참조하세요. Azure AD 앱 갤러리에 애플리케이션을 나열하는 방법에 대한 자세한 내용은 [Azure Active Directory 애플리케이션 갤러리에 애플리케이션 나열](https://aka.ms/azureadapprequest)을 참조하세요.

---

### <a name="two-new-identity-protection-detections"></a>두 가지 새로운 ID 보호 검색

**유형:** 새로운 기능  
**서비스 범주:** ID 보호  
**제품 기능:** ID 보안 & 보호
 
ID 보호에 두 가지 새로운 로그인 연결 검색 유형( 의심스러운 받은 편지함 조작 규칙 및 불가능한 여행)이 추가되었습니다. 이러한 오프라인 검색은 McAS(Microsoft 클라우드 앱 보안)에서 검색되며 ID 보호의 사용자 및 로그인 위험에 영향을 미칩니다. 이러한 검색에 대한 자세한 내용은 [로그인 위험 유형을](https://docs.microsoft.com/azure/active-directory/identity-protection/concept-identity-protection-risks#sign-in-risk)참조하십시오.
 
---
 
### <a name="breaking-change-uri-fragments-will-not-be-carried-through-the-login-redirect"></a>주요 변경 사항: URI 조각은 로그인 리디렉션을 통해 수행되지 않습니다.

**유형:** 변경된 기능  
**서비스 범주:** 인증(로그인)  
**제품 기능:** 사용자 인증
 
2020년 2월 8일부터 사용자에게 로그인할 login.microsoftonline.com 요청이 전송되면 서비스는 요청에 빈 조각을 추가합니다.  이렇게 하면 브라우저가 요청의 기존 조각을 지워내도록 하여 리디렉션 공격 클래스를 방지할 수 있습니다. 어떤 응용 프로그램도 이 동작에 대한 종속성을 가져야 합니다. 자세한 내용은 Microsoft ID 플랫폼 [설명서의 주요 변경 내용을](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#february-2020) 참조하세요.

---

## <a name="december-2019"></a>2019년 12월

### <a name="integrate-sap-successfactors-provisioning-into-azure-ad-and-on-premises-ad-public-preview"></a>Azure AD 및 온-프레미스 AD에 SAP SuccessFactors 프로비저닝 통합(공개 미리 보기)

**유형:** 새로운 기능  
**서비스 범주:** 앱 프로비전  
**제품 기능:** ID 라이프사이클 관리

이제 Azure AD에서 SAP SuccessFactors를 신뢰할 수 있는 ID 원본으로 통합할 수 있습니다. 이러한 통합을 통해 새 채용 또는 종료와 같은 HR 기반 이벤트를 사용하여 Azure AD 계정의 프로비저닝을 제어하는 등 종단 간 ID 수명 주기를 자동화할 수 있습니다.

Azure AD에 대한 SAP SuccessFactors 인바운드 프로비저닝을 설정하는 방법에 대한 자세한 내용은 [SAP SuccessFactors 자동 프로비저닝](https://aka.ms/SAPSuccessFactorsInboundTutorial) 자습서를 참조하십시오.

---

### <a name="support-for-customized-emails-in-azure-ad-b2c-public-preview"></a>Azure AD B2C(공개 미리 보기)에서 사용자 지정된 전자 메일 지원

**유형:** 새로운 기능  
**서비스 범주:** B2C - 소비자 ID 관리  
**제품 기능:** B2B/B2C

이제 Azure AD B2C를 사용하여 사용자가 앱을 사용하기 위해 등록할 때 사용자 지정된 전자 메일을 만들 수 있습니다. DisplayControls(현재 미리 보기) 및 타사 전자 메일 공급자(예: [SendGrid,](https://sendgrid.com/) [SparkPost](https://sparkpost.com/)또는 사용자 지정 REST API)를 사용하여 고유한 이메일 템플릿, **From** 주소 및 제목 텍스트를 사용할 수 있을 뿐만 아니라 현지화 및 사용자 지정 일회용 암호(OTP) 설정을 지원할 수 있습니다.

자세한 내용은 [Azure Active Directory B2C에서 사용자 지정 전자 메일 확인을](https://docs.microsoft.com/azure/active-directory-b2c/custom-email)참조하세요.

---

### <a name="replacement-of-baseline-policies-with-security-defaults"></a>기본 정책과 보안 기본값 의 교체

**유형:** 변경된 기능  
**서비스 범주:** 기타  
**제품 기능:** ID 보안 및 보호

인증에 대한 기본 보안 모델의 일부로 모든 테넌에서 기존 기준 보호 정책을 제거합니다. 이 제거는 2 월 말에 완료 대상입니다. 이러한 기준 보호 정책을 대체하는 것은 보안 기본값입니다. 기준 보호 정책을 사용하는 경우 새 보안 기본값 정책또는 조건부 액세스로 이동하도록 계획해야 합니다. 이러한 정책을 사용하지 않은 경우 수행할 조치가 없습니다.

새 보안 기본값에 대한 자세한 내용은 [보안 기본값이란 무엇입니까?](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-security-defaults) 조건부 액세스 정책에 대한 자세한 내용은 [일반 조건부 액세스 정책을](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-policy-common)참조하십시오.

---

## <a name="november-2019"></a>2019년 11월

### <a name="support-for-the-samesite-attribute-and-chrome-80"></a>동일 사이트 속성 및 크롬에 대 한 지원 80

**유형:** 변경 계획  
**서비스 범주:** 인증(로그인)  
**제품 기능:** 사용자 인증

쿠키에 대한 보안 기본 모델의 일환으로 Chrome 80 브라우저는 `SameSite` 속성 없이 쿠키를 처리하는 방법을 변경하고 있습니다. `SameSite` 속성을 지정하지 않은 모든 쿠키는 `SameSite=Lax`설정된 것처럼 처리되며, 이로 인해 Chrome은 앱이 종속될 수 있는 특정 도메인 간 쿠키 공유 시나리오를 차단합니다. 이전 Chrome 동작을 유지하려면 `SameSite=None` 속성을 사용하고 추가 `Secure` 속성을 추가할 수 있으므로 사이트 간 쿠키는 HTTPS 연결을 통해서만 액세스할 수 있습니다. Chrome은 2020년 2월 4일까지 이 변경 작업을 완료할 예정입니다.

모든 개발자는 이 지침을 사용하여 앱을 테스트하는 것이 좋습니다.

- **보안 쿠키 사용** 설정의 기본값을 **예로**설정합니다.

- **동일사이트** 특성의 기본값을 **없음으로**설정합니다.

- Secure 의 `SameSite` 추가 특성을 **추가합니다.**

자세한 내용은 [chrome 버전 79 이상에서 고객 웹 사이트](https://support.microsoft.com/help/4522904/potential-disruption-to-microsoft-services-in-chrome-beta-version-79)및 Microsoft 제품 및 서비스에 대한 ASP.NET 및 ASP.NET 핵심 및 잠재적 중단의 [예정된 동일사이트 쿠키 변경](https://devblogs.microsoft.com/aspnet/upcoming-samesite-cookie-changes-in-asp-net-and-asp-net-core/) 사항을 참조하십시오.

---

### <a name="new-hotfix-for-microsoft-identity-manager-mim-2016-service-pack-2-sp2"></a>마이크로 소프트 ID 관리자 (MIM)에 대한 새로운 핫픽스 2016 서비스 팩 2 (SP2)

**유형:** 고정  
**서비스 범주:** 마이크로소프트 아이덴티티 매니저  
**제품 기능:** ID 라이프사이클 관리

핫픽스 롤업 패키지(빌드 4.6.34.0)는 MIM(MiM) 2016 서비스 팩 2(SP2)에 사용할 수 있습니다. 이 롤업 패키지는 문제를 해결하고 "이 업데이트에 추가된 문제 및 개선 사항" 절에 설명된 개선 사항이 추가됩니다.

자세한 정보 및 핫픽스 패키지를 다운로드하려면 [Microsoft ID 관리자 2016 서비스 팩 2(빌드 4.6.34.0) 업데이트 롤업을 사용할 수 있습니다.](https://support.microsoft.com/help/4512924/microsoft-identity-manager-2016-service-pack-2-build-4-6-34-0-update-r)

---

### <a name="new-ad-fs-app-activity-report-to-help-migrate-apps-to-azure-ad-public-preview"></a>Azure AD로 앱을 마이그레이션하는 데 도움이 되는 새 AD FS 앱 활동 보고서(공개 미리 보기)

**유형:** 새로운 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능:** SSO

Azure 포털에서 새 AD FS(활성 디렉터리 페더레이션 서비스) 앱 활동 보고서를 사용하여 Azure AD로 마이그레이션할 수 있는 앱을 식별합니다. 이 보고서는 모든 AD FS 앱이 Azure AD와의 호환성을 평가하고, 모든 문제를 검사하며, 마이그레이션을 위한 개별 앱 준비에 대한 지침을 제공합니다.

자세한 내용은 [AD FS 응용 프로그램 활동 보고서 사용을 참조하여 응용 프로그램을 Azure AD로 마이그레이션합니다.](https://docs.microsoft.com/azure/active-directory/manage-apps/migrate-adfs-application-activity)

---

### <a name="new-workflow-for-users-to-request-administrator-consent-public-preview"></a>사용자가 관리자 동의를 요청할 수 있는 새 워크플로(공개 미리 보기)

**유형:** 새로운 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능:** 액세스 제어

새 관리자 동의 워크플로는 관리자에게 관리자 승인이 필요한 앱에 대한 액세스 권한을 부여하는 방법을 제공합니다. 사용자가 앱에 액세스하려고 하지만 동의를 제공할 수 없는 경우 이제 관리자 승인 요청을 보낼 수 있습니다. 요청은 전자 메일로 전송되고 Azure 포털에서 검토자로 지정된 모든 관리자에게 액세스할 수 있는 큐에 배치됩니다. 검토자가 보류 중인 요청에 대해 작업을 수행하면 요청한 사용자에게 작업에 대한 알림이 전송됩니다.

자세한 내용은 [관리자 동의 워크플로 구성(미리 보기)을](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-admin-consent-workflow)참조하십시오.

---

### <a name="new-azure-ad-app-registrations-token-configuration-experience-for-managing-optional-claims-public-preview"></a>선택적 클레임 관리를 위한 새로운 Azure AD 앱 등록 토큰 구성 환경(공개 미리 보기)

**유형:** 새로운 기능  
**서비스 범주:** 기타  
**제품 기능:** 개발자 환경

Azure 포털의 새 **Azure AD 앱 등록 토큰 구성** 블레이드에는 앱 개발자가 앱에 대한 선택적 클레임의 동적 목록을 표시합니다. 이 새로운 환경은 Azure AD 앱 마이그레이션을 간소화하고 선택적 클레임 잘못된 구성을 최소화하는 데 도움이 됩니다.

자세한 내용은 [Azure AD 앱에 대한 선택적 클레임 제공을](https://docs.microsoft.com/azure/active-directory/develop/active-directory-optional-claims)참조하십시오.

---

### <a name="new-two-stage-approval-workflow-in-azure-ad-entitlement-management-public-preview"></a>Azure AD 권한 관리의 새로운 2단계 승인 워크플로(공개 미리 보기)

**유형:** 새로운 기능  
**서비스 범주:** 기타  
**제품 기능:** 권한 관리

액세스 패키지에 대한 사용자의 요청을 승인하기 위해 두 명의 승인자가 필요한 새로운 2단계 승인 워크플로가 도입되었습니다. 예를 들어 요청하는 사용자의 관리자가 먼저 승인해야 하도록 설정한 다음 리소스 소유자가 승인하도록 요구할 수도 있습니다. 승인자 중 하나가 승인하지 않으면 액세스가 부여되지 않습니다.

자세한 내용은 [Azure AD 권한 관리의 액세스 패키지에 대한 변경 요청 및 승인 설정을](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-access-package-request-policy)참조하십시오.

---

### <a name="updates-to-the-my-apps-page-along-with-new-workspaces-public-preview"></a>새 작업 영역과 함께 내 앱 페이지 업데이트(공개 미리 보기)

**유형:** 새로운 기능  
**서비스 범주:** 내 앱  
**제품 기능:** 제3자 통합

이제 조직의 사용자가 새로 고친 내 앱 환경을 보고 액세스하는 방식을 사용자 지정할 수 있습니다. 이 새로운 경험에는 사용자가 앱을 쉽게 찾고 구성할 수 있는 새로운 작업 영역 기능이 포함되어 있습니다.

새로운 내 앱 환경 및 작업 영역 만들기에 대한 자세한 내용은 [내 앱 포털에서 작업 영역 만들기를](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-workspaces)참조하십시오.

---

### <a name="google-social-id-support-for-azure-ad-b2b-collaboration-general-availability"></a>Azure AD B2B 공동 작업을 위한 Google 소셜 ID 지원(일반 공급)

**유형:** 새로운 기능  
**서비스 범주:** B2B  
**제품 기능:** 사용자 인증

Azure AD에서 Google 소셜 ID(Gmail 계정)를 새로 사용하면 사용자와 파트너의 공동 작업을 더 쉽게 만들 수 있습니다. 파트너가 더 이상 새 Microsoft 관련 계정을 만들고 관리할 필요가 없습니다. 이제 Microsoft Teams는 모든 클라이언트와 공통 및 테넌트 관련 인증 끝점에서 Google 사용자를 완벽하게 지원합니다.

자세한 내용은 [B2B 게스트 사용자의 ID 공급자로 Google 추가를](https://docs.microsoft.com/azure/active-directory/b2b/google-federation)참조하십시오.

---

### <a name="microsoft-edge-mobile-support-for-conditional-access-and-single-sign-on-general-availability"></a>조건부 액세스 및 단일 사인온을 위한 Microsoft Edge 모바일 지원(일반 공급)

**유형:** 새로운 기능  
**서비스 범주:** 조건부 액세스  
**제품 기능:** ID 보안 & 보호

iOS 및 Android에서 Microsoft Edge용 Azure AD는 이제 Azure AD 단일 사인온 및 조건부 액세스를 지원합니다.

- **마이크로 소프트 에지 단일 사인온 (SSO) :** 이제 모든 Azure AD 연결 앱에 대해 네이티브 클라이언트(예: Microsoft Outlook 및 Microsoft Edge)에서 단일 사인온을 사용할 수 있습니다.

- **마이크로 소프트 에지 조건부 액세스 :** 응용 프로그램 기반 조건부 액세스 정책을 통해 사용자는 Microsoft Edge와 같은 Microsoft Intune 으로 보호된 브라우저를 사용해야 합니다.

Microsoft Edge의 조건부 액세스 및 SSO에 대한 자세한 내용은 [조건부 액세스 및 Single Sign-on 현재 일반적으로 사용 가능한](https://techcommunity.microsoft.com/t5/Intune-Customer-Success/Microsoft-Edge-Mobile-Support-for-Conditional-Access-and-Single/ba-p/988179) 블로그 게시물에 대한 Microsoft Edge 모바일 지원을 참조하십시오. [앱 기반 조건부 액세스](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access) 또는 장치 기반 [조건부 액세스를](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices)사용하여 클라이언트 앱을 설정하는 방법에 대한 자세한 내용은 Microsoft [Intune 정책 보호 브라우저를 사용하여 웹 액세스 관리를](https://docs.microsoft.com/intune/apps/app-configuration-managed-browser)참조하십시오.

---

### <a name="azure-ad-entitlement-management-general-availability"></a>Azure AD 권한 관리(일반 공급)

**유형:** 새로운 기능  
**서비스 범주:** 기타  
**제품 기능:** 권한 관리

Azure AD 권한 관리는 조직이 ID 및 액세스 수명 주기를 대규모로 관리하는 데 도움이 되는 새로운 ID 거버넌스 기능입니다. 이 새로운 기능은 그룹, 앱 및 SharePoint Online 사이트 전반에 걸쳐 액세스 요청 워크플로, 액세스 할당, 검토 및 만료를 자동화하는 데 도움이 됩니다.

Azure AD 권한 관리를 사용하면 직원 및 해당 리소스에 액세스해야 하는 조직 외부 사용자에 대한 액세스를 보다 효율적으로 관리할 수 있습니다.

자세한 내용은 [Azure AD 권한 관리란 무엇입니까?](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview#license-requirements)

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>새로 지원되는 SaaS 앱에 대한 사용자 계정 프로비저닝 자동화

**유형:** 새로운 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능:** 제3자 통합  

이제 새로 통합된 앱에 대한 사용자 계정을 생성, 업데이트 및 삭제할 수 있습니다.

[SAP 클라우드 플랫폼 ID 인증 서비스,](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial) [링센트럴,](https://docs.microsoft.com/azure/active-directory/saas-apps/ringcentral-provisioning-tutorial) [스페이스IQ,](https://docs.microsoft.com/azure/active-directory/saas-apps/spaceiq-provisioning-tutorial) [미로,](https://docs.microsoft.com/azure/active-directory/saas-apps/miro-provisioning-tutorial) [클라우드게이트,](https://docs.microsoft.com/azure/active-directory/saas-apps/soloinsight-cloudgate-sso-provisioning-tutorial) [인포클라우드스위트,](https://docs.microsoft.com/azure/active-directory/saas-apps/infor-cloudsuite-provisioning-tutorial) [오피스스페이스 소프트웨어,](https://docs.microsoft.com/azure/active-directory/saas-apps/officespace-software-provisioning-tutorial) [우선매트릭스](https://docs.microsoft.com/azure/active-directory/saas-apps/priority-matrix-provisioning-tutorial)

자동화된 사용자 계정 프로비전을 사용하여 조직의 보안을 높이는 방법에 대한 자세한 내용은 [Azure AD를 사용하여 SaaS 응용 프로그램에 대한 사용자 프로비저닝 자동화를](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)참조하십시오.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---november-2019"></a>Azure AD 앱 갤러리에서 사용할 수 있는 새로운 페더레이션 앱 - 2019년 11월

**유형:** 새로운 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능:** 제3자 통합

2019년 11월에 는 페더레이션 지원이 있는 21개의 새로운 앱을 앱 갤러리에 추가했습니다.

[에어 테이블,](https://docs.microsoft.com/azure/active-directory/saas-apps/airtable-tutorial) [Hootsuite,](https://docs.microsoft.com/azure/active-directory/saas-apps/hootsuite-tutorial) [회원을위한 블루 액세스 (BAM)](https://docs.microsoft.com/azure/active-directory/saas-apps/blue-access-for-members-tutorial), [Bitly,](https://docs.microsoft.com/azure/active-directory/saas-apps/bitly-tutorial) [리바,](https://docs.microsoft.com/azure/active-directory/saas-apps/riva-tutorial) [ResLife 포털,](https://app.reslifecloud.com/hub5_signin/microsoft_azuread/?g=44BBB1F90915236A97502FF4BE2952CB&c=5&uid=0&ht=2&ref=) [NegometrixPortal 단일 사인온 (SSO)](https://docs.microsoft.com/azure/active-directory/saas-apps/negometrixportal-tutorial), , `https://login.microsoftonline.com/551f45da-b68e-4498-a7f5-a6e1efaeb41c/adminconsent?client_id=ca9bbfa4-1316-4c0f-a9ee-1248ac27f8ab&redirect_uri=https://admin.teamschamp.com/api/adminconsent&state=6883c143-cb59-42ee-a53a-bdb5faabf279` [모투스,](https://docs.microsoft.com/azure/active-directory/saas-apps/motus-tutorial) [Myaryaka,](https://docs.microsoft.com/azure/active-directory/saas-apps/myaryaka-tutorial)블루 [메일,](https://loginself1.bluemail.me/) [베들,](https://teams-web.beedle.co/#/) [비스마,](https://docs.microsoft.com/azure/active-directory/saas-apps/visma-tutorial)원 [데스크,](https://docs.microsoft.com/azure/active-directory/saas-apps/onedesk-tutorial) [포코 소매,](https://docs.microsoft.com/azure/active-directory/saas-apps/foko-retail-tutorial) [Qmarkets 아이디어 & 혁신 관리,](https://docs.microsoft.com/azure/active-directory/saas-apps/qmarkets-idea-innovation-management-tutorial) [Netskope 사용자 인증,](https://docs.microsoft.com/azure/active-directory/saas-apps/netskope-user-authentication-tutorial) [유니플로우 온라인, 유니플로우,](https://docs.microsoft.com/azure/active-directory/saas-apps/uniflow-online-tutorial) [클라로 멘티스,](https://docs.microsoft.com/azure/active-directory/saas-apps/claromentis-tutorial) [지스](https://docs.microsoft.com/azure/active-directory/saas-apps/jisc-student-voter-registration-tutorial) [e4enable](https://portal.e4enable.com/)

앱에 대한 자세한 내용은 [Azure Active Directory와 SaaS 애플리케이션 통합](https://aka.ms/appstutorial)을 참조하세요. Azure AD 앱 갤러리에 애플리케이션을 나열하는 방법에 대한 자세한 내용은 [Azure Active Directory 애플리케이션 갤러리에 애플리케이션 나열](https://aka.ms/azureadapprequest)을 참조하세요.

---

### <a name="new-and-improved-azure-ad-application-gallery"></a>새 및 개선된 Azure AD 응용 프로그램 갤러리

**유형:** 변경된 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능:** SSO

Azure Active Directory 테넌트에서 프로비저닝, OpenID Connect 및 SAML을 지원하는 사전 통합 된 앱을 쉽게 찾을 수 있도록 Azure AD 응용 프로그램 갤러리를 업데이트했습니다.

자세한 내용은 [Azure Active Directory 테넌트에 응용 프로그램 추가를 참조하세요.](https://docs.microsoft.com/azure/active-directory/manage-apps/add-application-portal)

---

### <a name="increased-app-role-definition-length-limit-from-120-to-240-characters"></a>앱 역할 정의 길이 제한이 120자에서 240자로 증가했습니다.

**유형:** 변경된 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능:** SSO

일부 앱 및 서비스의 앱 역할 정의 값에 대한 길이 제한이 120자로 너무 짧다는 소식을 고객으로부터 들었습니다. 이에 대한 응답으로 역할 값 정의의 최대 길이를 240자로 늘렸습니다.

응용 프로그램별 역할 정의 사용에 대한 자세한 내용은 [응용 프로그램에서 앱 역할 추가를 참조하고 토큰에서 수신합니다.](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps)

---

## <a name="october-2019"></a>2019년 10월

### <a name="deprecation-of-the-identityriskevent-api-for-azure-ad-identity-protection-risk-detections"></a>Azure AD ID 보호 위험 검색에 대한 identityRiskEvent API 사용 중단  

**유형:** 변경 계획  
**서비스 범주:** ID 보호  
**제품 기능:** ID 보안 & 보호

개발자 피드백에 따라 Azure AD Premium P2 구독자는 이제 Microsoft 그래프에 대한 새로운 위험 검색 API를 사용하여 Azure AD Id Protection의 위험 검색 데이터에 대한 복잡한 쿼리를 수행할 수 있습니다. 기존 [identityRiskEvent](https://docs.microsoft.com/graph/api/resources/identityriskevent?view=graph-rest-beta) API 베타 버전은 **2020년 1월 10일**경에 데이터 반환을 중지합니다. 조직에서 identityRiskEvent API를 사용하는 경우 새 위험 감지 API로 전환해야 합니다.

새 위험 감지 API에 대한 자세한 내용은 [위험 검색 API 참조 문서를](https://aka.ms/RiskDetectionsAPI)참조하십시오.

---

### <a name="application-proxy-support-for-the-samesite-attribute-and-chrome-80"></a>동일한 사이트 속성 및 크롬 80에 대 한 응용 프로그램 프록시 지원

**유형:** 변경 계획  
**서비스 범주:** 응용 프로그램 프록시  
**제품 기능:** 액세스 제어

Chrome 80 브라우저 릴리스 몇 주 전에 애플리케이션 프록시 쿠키가 **SameSite** 특성을 처리하는 방법을 업데이트할 계획입니다. Chrome 80이 출시되면 **SameSite** 특성을 지정하지 않는 모든 쿠키는 `SameSite=Lax`로 설정된 것처럼 처리됩니다.

이러한 변경으로 인해 잠재적으로 부정적인 영향을 받지 않도록 하려면 다음을 통해 응용 프로그램 프록시 액세스 및 세션 쿠키를 업데이트합니다.

- **보안 쿠키 사용** 설정의 기본값을 **예로**설정합니다.

- **동일사이트** 특성의 기본값을 **없음으로**설정합니다.

    >[!NOTE]
    > 응용 프로그램 프록시 액세스 쿠키는 항상 보안 채널을 통해 독점적으로 전송되었습니다. 이러한 변경 사항은 세션 쿠키에만 적용됩니다.

응용 프로그램 프록시 쿠키 설정에 대한 자세한 내용은 [Azure Active Directory 의 온-프레미스 응용 프로그램에 액세스하기 위한 쿠키 설정을](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-cookie-settings)참조하십시오.

---

### <a name="app-registrations-legacy-and-converged-app-management-from-the-application-registration-portal-appsdevmicrosoftcom-will-no-longer-be-available"></a>응용 프로그램 등록 포털(apps.dev.microsoft.com)의 앱 등록(레거시) 및 컨버지드 앱 관리를 더 이상 사용할 수 없습니다.

**유형:** 변경 계획  
**서비스 범주:** 해당 없음  
**제품 기능:** 개발자 환경

가까운 장래에 Azure AD 계정이 있는 사용자는 더 이상 응용 프로그램 등록 포털(apps.dev.microsoft.com)을 사용하여 컨버지드 응용 프로그램을 등록 및 관리하거나 Azure Portal의 앱 등록(레거시) 환경에서 응용 프로그램을 등록 및 관리할 수 없습니다.

새로운 앱 등록 경험에 대한 자세한 내용은 [Azure 포털 교육 가이드에서 앱 등록을](../develop/app-registrations-training-guide-for-app-registrations-legacy-users.md)참조하세요.

---

### <a name="users-are-no-longer-required-to-re-register-during-migration-from-per-user-mfa-to-conditional-access-based-mfa"></a>사용자는 더 이상 사용자당 MFA에서 조건부 액세스 기반 MFA로 마이그레이션하는 동안 다시 등록할 필요가 없습니다.

**유형:** 고정  
**서비스 범주:** Mfa  
**제품 기능:** ID 보안 & 보호

MFA(사용자별 다단계 인증)에 대해 비활성화된 다음 조건부 액세스 정책을 통해 MFA에 대해 사용하도록 설정한 경우 사용자가 다시 등록해야 하는 경우 알려진 문제를 해결했습니다.

사용자가 다시 등록하도록 요구하려면 Azure AD 포털에서 사용자의 인증 방법에서 **필수 다시 등록 MFA** 옵션을 선택할 수 있습니다. 사용자별 MFA에서 조건부 액세스 기반 MFA로 사용자를 마이그레이션하는 것에 대한 자세한 내용은 [사용자별 MFA에서 조건부 액세스 기반 MFA로 사용자 변환을](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted#convert-users-from-per-user-mfa-to-conditional-access-based-mfa)참조하십시오.

---

### <a name="new-capabilities-to-transform-and-send-claims-in-your-saml-token"></a>SAML 토큰에서 클레임을 변환하고 전송하는 새로운 기능

**유형:** 새로운 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능:** SSO

SAML 토큰에서 클레임을 사용자 지정하고 보낼 수 있도록 추가 기능이 추가되었습니다. 이러한 새로운 기능은 다음과 같습니다.

- 추가 클레임 변환 기능을 통해 클레임에서 보내는 값을 수정할 수 있습니다.

- 단일 클레임에 여러 변환을 적용할 수 있는 기능입니다.

- 사용자 유형 및 사용자가 속한 그룹에 따라 클레임 소스를 지정하는 기능입니다.

이러한 새로운 기능을 사용하는 방법을 포함하여 이러한 새 기능에 대한 자세한 내용은 [엔터프라이즈 응용 프로그램에 대한 SAML 토큰에서 발급된 사용자 지정 클레임을](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)참조하십시오.

---

### <a name="new-my-sign-ins-page-for-end-users-in-azure-ad"></a>Azure AD의 최종 사용자를 위한 새 내 로그인 페이지

**유형:** 새로운 기능  
**서비스 범주:** 인증(로그인)  
**제품 기능:** 모니터링 및 보고

조직의 사용자가 최근 로그인 기록을 보고 비정상적인https://mysignins.microsoft.com) 활동을 확인할 수 있도록 새 내 **로그인** 페이지가 추가되었습니다. 이 새 페이지를 사용하면 사용자가 다음을 볼 수 있습니다.

- 누군가가 자신의 암호를 추측하려고하는 경우.

- 공격자가 자신의 계정과 위치에서 로그인한 경우

- 공격자가 액세스하려고 시도한 앱입니다.

자세한 내용은 이제 [사용자가 로그인 기록에서 비정상적인 활동 블로그를 확인할 수 있는지](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Users-can-now-check-their-sign-in-history-for-unusual-activity/ba-p/916066) 를 참조하세요.

---

### <a name="migration-of-azure-ad-domain-services-azure-ad-ds-from-classic-to-azure-resource-manager-virtual-networks"></a>Azure AD 도메인 서비스(Azure AD DS)를 클래식에서 Azure 리소스 관리자 가상 네트워크로 마이그레이션

**유형:** 새로운 기능  
**서비스 범주:** Azure AD Domain Services  
**제품 기능:** Azure AD Domain Services

고전적인 가상 네트워크에 갇혀있는 고객에게 - 우리는 당신을 위해 좋은 소식이 있습니다! 이제 기존 가상 네트워크에서 기존 Resource Manager 가상 네트워크로 일회성 마이그레이션을 수행할 수 있습니다. Resource Manager 가상 네트워크로 이동한 후에는 세분화된 암호 정책, 전자 메일 알림 및 감사 로그와 같은 추가 및 업그레이드된 기능을 활용할 수 있습니다.

자세한 내용은 [미리 보기 - Azure AD 도메인 서비스를 클래식 가상 네트워크 모델에서 리소스 관리자로 마이그레이션합니다.](https://docs.microsoft.com/azure/active-directory-domain-services/migrate-from-classic-vnet)

---

### <a name="updates-to-the-azure-ad-b2c-page-contract-layout"></a>Azure AD B2C 페이지 계약 레이아웃 업데이트

**유형:** 새로운 기능  
**서비스 범주:** B2C - 소비자 ID 관리  
**제품 기능:** B2B/B2C

Azure AD B2C에 대한 페이지 계약의 버전 1.2.0에 대한 몇 가지 새로운 변경 사항을 도입했습니다. 이 업데이트된 버전에서는 이제 요소의 로드 순서를 제어할 수 있으므로 스타일 시트(CSS)가 로드될 때 발생하는 깜박임을 중지하는 데 도움이 될 수 있습니다.

페이지 계약에 대한 변경 내용의 전체 목록은 [버전 변경 로그를](https://docs.microsoft.com/azure/active-directory-b2c/page-layout#120)참조하십시오.

---

### <a name="update-to-the-my-apps-page-along-with-new-workspaces-public-preview"></a>새 작업 영역과 함께 내 앱 페이지로 업데이트(공개 미리 보기)

**유형:** 새로운 기능  
**서비스 범주:** 내 앱  
**제품 기능:** 액세스 제어

이제 조직의 사용자가 새로운 작업 영역 기능을 사용하여 앱을 쉽게 찾을 수 있도록 하는 등 조직의 사용자가 새로운 내 앱 환경을 보고 액세스하는 방식을 사용자 지정할 수 있습니다. 새 작업 영역 기능은 조직의 사용자가 이미 액세스할 수 있는 앱에 대한 필터 역할을 합니다.

새 내 앱 환경을 롤아웃하고 작업 영역을 만드는 방법에 대한 자세한 내용은 [내 앱(미리 보기) 포털에서 작업 영역 만들기를](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-workspaces)참조하십시오.

---

### <a name="support-for-the-monthly-active-user-based-billing-model-general-availability"></a>월별 활성 사용자 기반 청구 모델 지원(일반 공급)

**유형:** 새로운 기능  
**서비스 범주:** B2C - 소비자 ID 관리  
**제품 기능:** B2B/B2C

Azure AD B2C는 이제 월별 활성 사용자(MAU) 청구를 지원합니다. MAU 청구는 한 달 동안 인증 활동이 있는 고유 사용자의 수를 기반으로 합니다. 기존 고객은 언제든지 이 새 결제 방법으로 전환할 수 있습니다.

2019년 11월 1일부터 모든 신규 고객은 이 방법을 사용하여 자동으로 요금이 청구됩니다. 이 청구 방법은 비용 이점과 미리 계획할 수 있는 기능을 통해 고객에게 이점을 제공합니다.

자세한 내용은 [월별 활성 사용자 청구 모델로 업그레이드를](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-how-to-enable-billing#upgrade-to-monthly-active-users-billing-model)참조하십시오.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---october-2019"></a>Azure AD 앱 갤러리에서 사용할 수 있는 새로운 페더레이션 앱 - 2019년 10월

**유형:** 새로운 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능:** 제3자 통합

2019년 10월에 는 페더레이션 지원이 있는 35개의 새로운 앱을 앱 갤러리에 추가했습니다.

[위기의 경우 – 모바일,](https://docs.microsoft.com/azure/active-directory/saas-apps/in-case-of-crisis-mobile-tutorial) [주노 여행,](https://docs.microsoft.com/azure/active-directory/saas-apps/juno-journey-tutorial) [지수HR,](https://docs.microsoft.com/azure/active-directory/saas-apps/exponenthr-tutorial) [택트,](https://tact.ai/assistant/) [오푸스 캐피타 현금 관리,](http://cm1.opuscapita.com/tenantname)세일즈 [팀,](https://prd.salestim.io/forms) [학습기,](https://docs.microsoft.com/azure/active-directory/saas-apps/learnster-tutorial) [Dynatrace,](https://docs.microsoft.com/azure/active-directory/saas-apps/dynatrace-tutorial) [HunchBuzz,](https://login.hunchbuzz.com/integrations/azure/process)신선 [작품,](https://docs.microsoft.com/azure/active-directory/saas-apps/freshworks-tutorial) [eCornell,](https://docs.microsoft.com/azure/active-directory/saas-apps/ecornell-tutorial) [ShipHazmat,](https://docs.microsoft.com/azure/active-directory/saas-apps/shiphazmat-tutorial) [Netskope 클라우드 보안,](https://docs.microsoft.com/azure/active-directory/saas-apps/netskope-cloud-security-tutorial) [만족](https://docs.microsoft.com/azure/active-directory/saas-apps/contentful-tutorial), [바인드 튜닝,](https://bindtuning.com/login) [HireVue 좌표 – 유럽,](https://www.hirevue.com/) [HireVue 좌표 - USOnly,](https://www.hirevue.com/) [HireVue 좌표 - 미국,](https://www.hirevue.com/) [위트 파로 트 지식 상자,](https://wittyapi.wittyparrot.com/wittyparrot/api/provision/trail/signup)클라우드 [모어,](https://docs.microsoft.com/azure/active-directory/saas-apps/cloudmore-tutorial) [Visit.org,](https://docs.microsoft.com/azure/active-directory/saas-apps/visitorg-tutorial) [Cambium 시루스 EasyPass 포털,](https://login.xirrus.com/azure-signup) [페이로 시티,](https://docs.microsoft.com/azure/active-directory/saas-apps/paylocity-tutorial) [메일 행운!](https://docs.microsoft.com/azure/active-directory/saas-apps/mail-luck-tutorial), [Teamie,](https://theteamie.com/)팀, [SIGNL4,](https://account.signl4.com/manage) [EAB 탐색 IMPL,](https://docs.microsoft.com/azure/active-directory/saas-apps/eab-navigate-impl-tutorial)스크린 [미팅,](https://console.screenmeet.com/) [오메가 포인트,](https://pi.ompnt.com/) [인튠 (아이폰)에 대한 말하기 이메일,](https://speaking.email/FAQ/98/email-access-via-microsoft-intune)사무실 [365 직접 (아이폰 / 안드로이드)](https://speaking.email/FAQ/126/email-access-via-microsoft-office-365-direct)말하기 이메일, [ExactCare SSO,](https://docs.microsoft.com/azure/active-directory/saas-apps/exactcare-sso-tutorial) [iHealthHome 케어 네비게이션 시스템,](https://ihealthnav.com/account/signin) [Qubie](https://qubie.azurewebsites.net/static/adminTab/authorize.html) [Velocity for Teams](https://velocity.peakup.org/teams/login)

앱에 대한 자세한 내용은 [Azure Active Directory와 SaaS 애플리케이션 통합](https://aka.ms/appstutorial)을 참조하세요. Azure AD 앱 갤러리에 애플리케이션을 나열하는 방법에 대한 자세한 내용은 [Azure Active Directory 애플리케이션 갤러리에 애플리케이션 나열](https://aka.ms/azureadapprequest)을 참조하세요.

---

### <a name="consolidated-security-menu-item-in-the-azure-ad-portal"></a>Azure AD 포털의 통합 보안 메뉴 항목

**유형:** 변경된 기능  
**서비스 범주:** ID 보호  
**제품 기능:** ID 보안 & 보호

이제 새 **보안** 메뉴 항목과 검색 창에서 Azure Portal에서 사용 가능한 모든 **Azure** AD 보안 기능에 액세스할 수 있습니다. 또한 **보안 - 시작이라는**새 **보안** 방문 페이지에서 공개 문서, 보안 지침 및 배포 가이드에 대한 링크를 제공합니다.

새로운 **보안** 메뉴에는 다음이 포함됩니다.

- 조건부 액세스
- ID 보호
- Security Center
- ID 보안 점수
- 인증 방법
- Mfa
- 위험 보고서 - 위험한 사용자, 위험한 로그인, 위험 감지
- 기타...

자세한 내용은 [보안 - 시작](https://portal.azure.com/#blade/Microsoft_AAD_IAM/SecurityMenuBlade/GettingStarted)을 참조하십시오.

---

### <a name="office-365-groups-expiration-policy-enhanced-with-autorenewal"></a>자동 갱신을 통해 강화된 Office 365 그룹 만료 정책

**유형:** 변경된 기능  
**서비스 범주:** 그룹 관리  
**제품 기능:** ID 라이프사이클 관리

Office 365 그룹 만료 정책이 강화되어 구성원이 적극적으로 사용하는 그룹을 자동으로 갱신합니다. 그룹은 Outlook, SharePoint 및 팀을 포함한 모든 Office 365 앱에서 사용자 활동을 기반으로 자동으로 갱신됩니다.

이 향상된 기능은 그룹 만료 알림을 줄이는 데 도움이 되며 활성 그룹을 계속 사용할 수 있도록 하는 데 도움이 됩니다. Office 365 그룹에 대한 활성 만료 정책이 이미 있는 경우 이 새 기능을 설정하기 위해 아무 것도 수행할 필요가 없습니다.

자세한 내용은 [Office 365 그룹에 대한 만료 정책 구성을](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-lifecycle)참조하십시오.

---

### <a name="updated-azure-ad-domain-services-azure-ad-ds-creation-experience"></a>업데이트된 Azure AD 도메인 서비스(Azure AD DS) 만들기 환경

**유형:** 변경된 기능  
**서비스 범주:** Azure AD Domain Services  
**제품 기능:** Azure AD Domain Services

Azure AD 도메인 서비스(Azure AD DS)를 업데이트하여 새롭고 향상된 생성 환경을 포함하여 단 3번의 클릭만으로 관리되는 도메인을 만들 수 있도록 했습니다! 또한 이제 템플릿에서 Azure AD DS를 업로드하고 배포할 수 있습니다.

자세한 내용은 [자습서: Azure Active Directory 도메인 서비스 인스턴스 만들기 및 구성을](https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance)참조하세요.

---
