---
title: 온-프레미스 워크 로드에 대 한 azure AD 클라우드 관리 관리-Azure
description: 이 항목에서는 온-프레미스 워크 로드에 대 한 클라우드 관리 관리에 대해 설명 합니다.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9cb101e415499150cd3d825fe5f42ce0dbc766fb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89662517"
---
# <a name="how-azure-ad-delivers-cloud-governed-management-for-on-premises-workloads"></a>Azure AD가 온-프레미스 워크 로드에 대 한 클라우드 관리 관리를 제공 하는 방법

Azure AD (Azure Active Directory)는 id, 액세스 관리 및 보안의 모든 측면을 포괄 하는 수백만 개의 조직에서 사용 하는 IDaaS (종합 id as a service) 솔루션입니다. Azure AD는 10억 개 이상의 사용자 id를 보유 하 고 있으며 사용자가 로그인 하 고 안전 하 게 액세스할 수 있도록 지원 합니다.

* Microsoft 365, Azure Portal, 수천 개의 기타 SaaS (SaaS) 응용 프로그램 등의 외부 리소스
* 조직에서 개발한 클라우드 응용 프로그램과 함께 조직의 회사 네트워크 및 인트라넷에 있는 응용 프로그램과 같은 내부 리소스

조직에서는 Azure AD가 ' 순수 클라우드 ' 인 경우 Azure AD를 사용 하 고 온-프레미스 워크 로드가 있는 경우 ' 하이브리드 ' 배포로 사용할 수 있습니다. Azure AD의 하이브리드 배포는 IT 자산을 클라우드로 마이그레이션하는 조직이 나 기존 온-프레미스 인프라를 새 클라우드 서비스와 함께 계속 통합 하기 위한 전략의 일부일 수 있습니다.

지금까지 ' 하이브리드 ' 조직은 기존 온-프레미스 인프라의 확장으로 Azure AD를 보았을 것입니다. 이러한 배포에서 온-프레미스 id 거 버 넌 스 관리, Windows Server Active Directory 또는 기타 사내 디렉터리 시스템은 제어 지점이 며, 사용자 및 그룹은 해당 시스템에서 Azure AD와 같은 클라우드 디렉터리로 동기화 됩니다. 이러한 id가 클라우드에 있으면 Microsoft 365, Azure 및 기타 응용 프로그램에서 사용할 수 있습니다.

![ID 수명 주기](media/cloud-governed-management-for-on-premises//image1.png)

조직에서 응용 프로그램을 클라우드로 사용 하 여 IT 인프라를 더 많이 이동할 때 많은 기능을 제공 하는 것은 id 관리를 서비스로 제공 하는 향상 된 보안 및 간소화 된 관리 기능을 찾고 있습니다. Azure AD의 클라우드 제공 IDaaS 기능을 통해 조직은 기존 온-프레미스 시스템에서 Azure AD로의 id 관리를 신속 하 게 채택 하 고 이동 하 여 기존 응용 프로그램 뿐만 아니라 새로운 응용 프로그램을 계속 지원할 수 있는 솔루션 및 기능을 제공할 수 있습니다.

이 문서에서는 하이브리드 IDaaS의 Microsoft 전략에 대해 설명 하 고 조직에서 기존 응용 프로그램에 Azure AD를 사용 하는 방법을 설명 합니다.

## <a name="the-azure-ad-approach-to-cloud-governed-identity-management"></a>클라우드 관리 id 관리에 대 한 Azure AD 접근 방법

조직에서 클라우드로 전환 하는 경우 완벽 한 환경에 대 한 제어 권한을 보유 하 고, 자동화 및 예방적 정보를 지 원하는 활동에 대 한 추가 보안 및 가시성을 보유 하 고 있음을 보장 해야 합니다. "**클라우드 관리 관리**"는 조직이 클라우드에서 사용자, 응용 프로그램, 그룹 및 장치를 관리 하 고 제어 하는 방법을 설명 합니다.

이 현대적인 세계에서 조직은 SaaS 응용 프로그램의 확산 및 공동 작업 및 외부 id의 증가에 의해 규모에 맞게 효과적으로 관리할 수 있어야 합니다. 클라우드의 새로운 위험 요소는 조직의 응답성이 향상 됨을 의미 합니다. 클라우드 사용자를 손상 시키는 악의적인 행위자는 클라우드 및 온-프레미스 응용 프로그램에 영향을 줄 수 있습니다.

특히 하이브리드 조직은 작업을 수동으로 위임 하 고 자동화할 수 있어야 합니다. 작업을 자동화 하려면 다양 한 id 관련 리소스 (사용자, 그룹, 응용 프로그램, 장치)의 수명 주기를 오케스트레이션 하는 Api 및 프로세스가 필요 하므로, 해당 리소스의 일상 관리를 핵심 IT 직원 외부의 더 많은 개인에 게 위임할 수 있습니다. Azure AD는 온-프레미스 id 인프라를 요구 하지 않고 사용자를 위해 사용자 계정 관리 및 기본 인증을 통해 이러한 요구 사항을 해결 합니다. 온-프레미스 인프라를 구축 하지 않으면 비즈니스 파트너와 같이 온-프레미스 디렉터리에는 없지만 액세스 관리가 비즈니스 결과를 달성 하는 데 중요 한 새 커뮤니티가 있는 조직의 이점을 누릴 수 있습니다.

또한 거 버 넌 스---없이 관리가 완료 되지 않으며,이 새로운 세계의 거 버 넌 스는 추가 기능이 아닌 id 시스템의 통합 부분입니다. Id 거 버 넌 스는 직원, 비즈니스 파트너 및 공급 업체, 서비스 및 응용 프로그램에 대해 id 및 액세스 수명 주기를 관리 하는 기능을 조직에 제공 합니다.

Id 거 버 넌 스를 통합 하면 조직이 클라우드 관리 관리로 전환 하 여 규모를 확장 하 고, 게스트가 새로운 문제를 해결 하 고, 고객이 온-프레미스 인프라를 사용 하는 것 보다 깊은 통찰력과 자동화를 제공할 수 있습니다. 이 새로운 세계의 거 버 넌 스는 조직에서 조직의 리소스에 대 한 액세스에 대 한 투명도, 가시성 및 적절 한 제어를 제공 하는 기능을 의미 합니다. Azure AD를 사용 하는 경우 보안 작업 및 감사 팀은 조직 내 리소스 (장치), 해당 액세스로 수행 하는 작업 및 조직에서 사용 하는 작업, 회사 또는 규정 정책에 따라 적절 한 컨트롤을 사용 하 여 액세스를 제거 하거나 제한 하는 사용자에 대 한---가시성을 제공 합니다.

새 관리 모델은 이러한 응용 프로그램에 대 한 액세스를 보다 쉽게 관리 하 고 보호할 수 있으므로 SaaS 및 LOB (기간 업무) 응용 프로그램을 모두 사용 하는 조직에 유용 합니다. 조직에서는 응용 프로그램을 Azure AD와 통합 하 여 클라우드 및 온-프레미스에서 발생 하는 id의 액세스를 일관 되 게 사용 하 고 관리할 수 있습니다. 응용 프로그램 수명 주기 관리는 더욱 자동화 되며, Azure AD는 온-프레미스 id 관리에서 쉽게 달성할 수 없는 응용 프로그램 사용에 대 한 풍부한 통찰력을 제공 합니다. 조직에서는 Azure AD, Microsoft 365 그룹 및 팀 셀프 서비스 기능을 통해 액세스 관리 및 공동 작업을 위한 그룹을 쉽게 만들고, 클라우드에서 사용자를 추가 하거나 제거 하 여 공동 작업 및 액세스 관리 요구 사항을 설정할 수 있습니다.

클라우드 관리 관리에 적합 한 Azure AD 기능을 선택 하는 것은 사용할 응용 프로그램 및 Azure AD와 이러한 응용 프로그램을 통합 하는 방법에 따라 달라 집니다. 다음 섹션에서는 AD 통합 응용 프로그램에 대해 수행할 방법과 페더레이션 프로토콜을 사용 하는 응용 프로그램 (예: SAML, OAuth 또는 Openid connect Connect)에 대해 간략하게 설명 합니다.

## <a name="cloud-governed-management-for-ad-integrated-applications"></a>AD 통합 응용 프로그램에 대 한 클라우드 관리 관리

Azure AD는 보안 원격 액세스 및 해당 응용 프로그램에 대 한 조건부 액세스를 통해 조직의 온-프레미스 Active Directory 통합 응용 프로그램에 대 한 관리를 개선 합니다. 또한 Azure AD는 다음을 비롯 하 여 사용자의 기존 AD 계정에 대 한 계정 수명 주기 관리 및 자격 증명 관리도 제공 합니다.

* **온-프레미스 응용 프로그램에 대 한 보안 원격 액세스 및 조건부 액세스**

많은 조직에서 온-프레미스 AD 통합 웹 및 원격 데스크톱 기반 응용 프로그램에 대 한 클라우드에서 액세스를 관리 하는 첫 번째 단계는 보안 원격 액세스를 제공 하기 위해 이러한 응용 프로그램 앞에 [응용 프로그램 프록시](../manage-apps/application-proxy.md) 를 배포 하는 것입니다.

Azure AD에 Single Sign-On 되면 사용자는 외부 URL 또는 내부 애플리케이션 포털을 통해 클라우드와 온-프레미스 애플리케이션에 모두 액세스할 수 있습니다. 예를 들어 응용 프로그램 프록시는 원격 데스크톱, SharePoint 및 응용 프로그램 (예: Tableau, Qlik 및 LOB (기간 업무) 응용 프로그램)에 원격 액세스 하 고 Single Sign-On를 제공 합니다. 또한 조건부 액세스 정책에는 [사용 약관](../conditional-access/terms-of-use.md) 을 표시 하 고 사용자가 응용 프로그램에 액세스 하기 전에 [동의 했는지](../conditional-access/require-tou.md) 확인 하는 작업이 포함 될 수 있습니다.

![앱 프록시 아키텍처](media/cloud-governed-management-for-on-premises/image2.png)

* **Active Directory 계정에 대 한 자동 수명 주기 관리**

Id 거 버 넌 스를 사용 하면 조직에 참여 하는 경우와 같이 사용자가 필요한 리소스에 얼마나 빠르게 액세스할 수 있는지에 따라 *생산성* ---간에 균형을 유지할 수 있습니다. ---및 *보안* ---사용자의 고용 상태가 변경 되는 경우와 같이 시간이 지남에 따라 해당 액세스를 어떻게 변경 하나요? ID 수명 주기 관리는 ID 거버넌스의 기반이며 대규모의 효과적인 거버넌스에는 애플리케이션에 대한 ID 수명 주기 관리 인프라를 현대화해야 합니다.

많은 조직에서 직원의 id 수명 주기는 HCM (인간 자본 관리) 시스템에서 해당 사용자의 표현에 연결 됩니다. Workday를 HCM 시스템으로 사용 하는 조직의 경우 Azure AD는 AD의 사용자 계정이 [workday의 작업자에 대해 자동으로 프로 비전 되 고 프로 비전 해제](../saas-apps/workday-inbound-tutorial.md)를 보장할 수 있습니다. 이렇게 하면 인터넷용 계정을 자동화 하 여 사용자 생산성을 개선 하 고 사용자가 역할을 변경 하거나 조직을 떠날 때 응용 프로그램 액세스가 자동으로 업데이트 되도록 하 여 위험을 관리할 수 있습니다. Workday 기반 사용자 프로 비전 [배포 계획](https://aka.ms/WorkdayDeploymentPlan) 은 5 단계 프로세스에서 사용자 프로 비전 솔루션을 Active Directory 하는 데 workday의 모범 사례 구현을 통해 조직을 안내 하는 단계별 가이드입니다.

또한 Azure AD Premium에는 SAP, Oracle eBusiness, Oracle PeopleSoft 등의 다른 온-프레미스 HCM 시스템에서 레코드를 가져올 수 있는 Microsoft Identity Manager 포함 되어 있습니다.

B2b 공동 작업은 점점 더 많은 조직 외부 사용자에 게 액세스 권한을 부여 해야 합니다. [AZURE AD B2B](/azure/active-directory/b2b/) 공동 작업을 통해 조직은 자체 회사 데이터에 대 한 제어를 유지 하면서 게스트 사용자 및 외부 파트너와 응용 프로그램 및 서비스를 안전 하 게 공유할 수 있습니다.

Azure AD는 필요에 따라 [게스트 사용자를 위해 AD에서 자동으로 계정을 만들어](../external-identities/hybrid-cloud-to-on-premises.md) 비즈니스 게스트가 다른 암호 없이 온-프레미스 AD 통합 응용 프로그램에 액세스할 수 있도록 합니다. 조직에서는 [게스트 사용자에 대 한 mfa (multi-factor authentication) 정책을](../external-identities/conditional-access.md)설정할 수 있으므로 응용 프로그램 프록시 인증 중에 mfa 검사가 수행 됩니다. 또한 클라우드 B2B 사용자에 대해 수행 되는 모든 [액세스 검토](../governance/manage-guest-access-with-access-reviews.md) 는 온-프레미스 사용자에 게 적용 됩니다. 예를 들어 수명 주기 관리 정책을 통해 클라우드 사용자를 삭제 하는 경우 온-프레미스 사용자도 삭제 됩니다.

**Active Directory 계정에 대 한 자격 증명 관리** Azure AD의 셀프 서비스 암호 재설정을 사용 하면 암호를 잊어버린 사용자가 다시 인증을 받을 수 있으며, [온-프레미스 Active Directory에 기록](../authentication/concept-sspr-writeback.md)된 변경 된 암호를 사용 하 여 암호를 다시 설정할 수 있습니다. 암호 재설정 프로세스는 온-프레미스 Active Directory 암호 정책을 사용할 수도 있습니다. 사용자가 암호를 다시 설정 하는 경우 해당 디렉터리에 커밋하기 전에 온-프레미스 Active Directory 정책을 충족 하는지 확인 합니다. 셀프 서비스 암호 재설정 [배포 계획](https://aka.ms/deploymentplans/sspr) 은 웹 및 Windows 통합 환경을 통해 사용자에 게 셀프 서비스 암호 재설정을 배포 하는 모범 사례를 간략하게 설명 합니다.

![Azure AD SSPR 아키텍처](media/cloud-governed-management-for-on-premises/image3.png)

마지막으로, 사용자가 AD에서 자신의 암호를 변경할 수 있도록 허용 하는 조직의 경우, 현재 공개 미리 보기로 제공 되는 [AZURE ad 암호 보호 기능](../authentication/concept-password-ban-bad-on-premises.md)을 통해 azure Ad에서 azure ad를 사용 하는 것과 동일한 암호 정책을 사용 하도록 ad를 구성할 수 있습니다.

응용 프로그램을 호스트 하는 운영 체제를 Azure로 이동 하 여 AD 통합 응용 프로그램을 클라우드로 이동할 준비가 되 면 AD 호환 도메인 서비스 (예: 도메인 가입, 그룹 정책, LDAP, Kerberos/NTLM 인증)를 제공 [Azure AD Domain Services](../../active-directory-domain-services/overview.md) 합니다. Azure AD Domain Services는 조직의 기존 Azure AD 테 넌 트와 통합 되어 사용자가 회사 자격 증명을 사용 하 여 로그인 할 수 있도록 합니다. 또한 기존 그룹 및 사용자 계정을 사용 하 여 리소스에 대 한 액세스를 보호 하 고 Azure 인프라 서비스에 대 한 온-프레미스 리소스의 ' 리프트 앤 시프트 '를 원활 하 게 수행할 수 있습니다.

![Azure AD Domain Services](media/cloud-governed-management-for-on-premises/image4.png)

## <a name="cloud-governed-management-for-on-premises-federation-based-applications"></a>온-프레미스 페더레이션 기반 응용 프로그램에 대 한 클라우드 관리 관리

이미 온-프레미스 id 공급자를 사용 하는 조직의 경우 Azure AD로 응용 프로그램을 이동 하면 더 안전한 액세스와 페더레이션 관리를 위한 보다 쉬운 관리 환경이 가능 합니다. Azure AD는 azure AD 조건부 액세스를 사용 하 여 Azure Multi-Factor Authentication를 비롯 한 세부적인 응용 프로그램별 액세스 제어를 구성할 수 있도록 합니다. Azure AD는 응용 프로그램 관련 토큰 서명 인증서 및 구성 가능한 인증서 만료 날짜를 포함 하 여 더 많은 기능을 지원 합니다. 이러한 기능, 도구 및 지침을 사용 하 여 조직에서 온-프레미스 id 공급자를 사용 중지할 수 있습니다. 한 가지 예의 microsoft 자체 IT에서는 17987 응용 프로그램을 Microsoft 내부 Active Directory Federation Services (AD FS)에서 Azure AD로 이동 했습니다.

![Azure AD 진화](media/cloud-governed-management-for-on-premises/image5.png)

페더레이션 응용 프로그램을 id 공급자로 Azure AD로 마이그레이션하기 시작 하려면 https://aka.ms/migrateapps 다음에 대 한 링크를 포함 하는를 참조 하세요.

* [응용 프로그램을 Azure Active Directory로 마이그레이션하](https://aka.ms/migrateapps/whitepaper)는 백서로, 마이그레이션의 이점을 제시 하 고, 검색, 분류, 마이그레이션 및 지속적인 관리의 네 가지 명확 하 게 설명 된 단계로 마이그레이션을 계획 하는 방법을 설명 합니다. 프로세스에 대해 생각 하 고 프로젝트를 사용 하기 쉬운 조각으로 분할 하는 방법을 안내 합니다. 문서 전체에는 과정에 도움이 되는 중요한 리소스에 대한 링크가 있습니다.

* 응용 프로그램 [인증을 Active Directory Federation Services에서 Azure Active Directory으로 마이그레이션하](https://aka.ms/migrateapps/adfssolutionguide) 는 솔루션 가이드에서는 응용 프로그램 마이그레이션 프로젝트를 계획 하 고 실행 하는 것과 동일한 4 단계를 자세히 살펴봅니다. 이 가이드에서는 Active Directory Federation Services (AD FS)에서 Azure AD로 응용 프로그램을 이동 하는 특정 목표에 이러한 단계를 적용 하는 방법을 알아봅니다.

* [Active Directory Federation Services 마이그레이션 준비 스크립트](https://aka.ms/migrateapps/adfstools) 를 기존 온-프레미스 Active Directory Federation Services (AD FS) 서버에서 실행 하 여 Azure AD로 마이그레이션하도록 응용 프로그램의 준비 상태를 확인할 수 있습니다.

## <a name="ongoing-access-management-across-cloud-and-on-premises-applications"></a>클라우드 및 온-프레미스 응용 프로그램에서 지속적인 액세스 관리

조직에는 확장 가능한 액세스를 관리 하는 프로세스가 필요 합니다. 사용자는 계속 해 서 액세스 권한을 축적 하 고 처음으로 프로 비전 된 것 이상으로 종료 합니다. 또한 엔터프라이즈 조직은 지속적으로 액세스 정책 및 제어를 개발 하 고 적용 하기 위해 효율적으로 확장할 수 있어야 합니다.

IT 대리자는 일반적으로 비즈니스 의사 결정권자에 대한 승인 결정에 액세스합니다. 또한 IT에는 사용자가 스스로가 포함될 수 있습니다. 예를 들어 유럽에 있는 회사의 마케팅 애플리케이션에서 기밀 고객 데이터에 액세스하는 사용자는 회사의 정책을 알아야 합니다. 또한 게스트 사용자는 초대 된 조직의 데이터에 대 한 처리 요구 사항을 모르고 있을 수 있습니다.

조직은 [동적 그룹과](../users-groups-roles/groups-dynamic-membership.md)같은 기술 (예: [SaaS 응용 프로그램](../saas-apps/tutorial-list.md)에 대 한 사용자 프로 비전 및 [Scim (도메인 간 Id 관리) 표준을 위해 시스템을 사용 하 여 통합 된 응용 프로그램](../app-provisioning/use-scim-to-provision-users-and-groups.md))을 통해 액세스 수명 주기 프로세스를 자동화할 수 있습니다. 또한 조직에서는 [온-프레미스 응용 프로그램에 대 한 액세스 권한이 있는 게스트 사용자](../external-identities/hybrid-cloud-to-on-premises.md)를 제어할 수 있습니다. 그런 다음, 정기 [Azure AD 액세스 검토](../governance/access-reviews-overview.md)를 사용하여 이러한 액세스 권한을 정기적으로 검토할 수 있습니다.

## <a name="future-directions"></a>이후 지침

하이브리드 환경에서 Microsoft의 전략은 **클라우드가 id의 제어 평면 인**배포를 사용 하도록 설정 하는 것이 고, 온-프레미스 디렉터리와 Active Directory 및 기타 온-프레미스 응용 프로그램과 같은 기타 id 시스템은 액세스 권한이 있는 사용자를 프로 비전 하는 대상입니다. 이 전략은 해당 응용 프로그램 및 해당 응용 프로그램에 의존 하는 작업에서 권한, id 및 액세스를 계속 보장 합니다. 이 최종 사용자는 조직에서 완전히 최종 사용자 생산성을 높일 수 있습니다.

![Azure AD 아키텍처](media/cloud-governed-management-for-on-premises/image6.png)

## <a name="next-steps"></a>다음 단계

이 과정을 시작 하는 방법에 대 한 자세한 내용은에 있는 Azure AD 배포 계획을 참조 하세요 <https://aka.ms/deploymentplans> . Azure AD (Azure Active Directory) 기능을 배포 하는 방법에 대 한 종단 간 지침을 제공 합니다. 각 계획은 일반적인 Azure AD 기능을 성공적으로 출시 하는 데 필요한 비즈니스 가치, 계획 고려 사항, 디자인 및 운영 절차를 설명 합니다. Microsoft는 Azure AD를 사용 하 여 클라우드에서 관리 하는 새로운 기능을 추가할 때 고객 배포 및 기타 피드백에서 얻은 모범 사례를 사용 하 여 배포 계획을 지속적으로 업데이트 합니다.