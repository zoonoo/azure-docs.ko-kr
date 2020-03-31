---
title: 온-프레미스 워크로드에 대한 Azure AD 클라우드 관리 관리 - Azure
description: 이 항목에서는 온-프레미스 워크로드에 대한 클라우드 관리 관리에 대해 설명합니다.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/05/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 510a5562740260eb2946ded074a5c37804c55375
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "67109513"
---
# <a name="how-azure-ad-delivers-cloud-governed-management-for-on-premises-workloads"></a>Azure AD가 온-프레미스 워크로드에 대해 클라우드 관리 관리를 제공하는 방법

Azure Active Directory(Azure AD)는 ID, 액세스 관리 및 보안의 모든 측면에 걸쳐 있는 수백만 개의 조직에서 사용하는 서비스(IdaaS) 솔루션으로서의 포괄적인 ID입니다. Azure AD는 10억 개 이상의 사용자 ID를 보유하고 있으며 사용자가 로그인하여 두 ID 모두에 안전하게 액세스할 수 있도록 도와줍니다.

* Microsoft Office 365, Azure 포털 및 기타 서비스형 소프트웨어(SaaS) 응용 프로그램과 같은 외부 리소스입니다.
* 조직의 회사 네트워크 및 인트라넷의 응용 프로그램과 같은 내부 리소스와 해당 조직에서 개발한 모든 클라우드 응용 프로그램입니다.

조직은 '순수 클라우드'인 경우 Azure AD를 사용하거나 온-프레미스 워크로드가 있는 경우 '하이브리드' 배포로 사용할 수 있습니다. Azure AD의 하이브리드 배포는 조직이 IT 자산을 클라우드로 마이그레이션하거나 기존 온-프레미스 인프라를 새 클라우드 서비스와 함께 계속 통합하는 전략의 일부일 수 있습니다.

역사적으로 '하이브리드' 조직은 Azure AD를 기존 온-프레미스 인프라의 확장으로 간주해 왔습니다. 이러한 배포에서 온-프레미스 ID 거버넌스 관리, Windows Server Active Directory 또는 기타 사내 디렉터리 시스템은 제어 지점이며 사용자 및 그룹은 이러한 시스템에서 Azure AD와 같은 클라우드 디렉터리로 동기화됩니다. 이러한 ID가 클라우드에 있으면 Office 365, Azure 및 기타 응용 프로그램에서 사용할 수 있습니다.

![ID 수명 주기](media/cloud-governed-management-for-on-premises//image1.png)

조직이 애플리케이션과 함께 더 많은 IT 인프라를 클라우드로 이동함에 따라 많은 조직이 서비스로서 ID 관리의 향상된 보안 및 간소화된 관리 기능을 찾고 있습니다. Azure AD의 클라우드 제공 IDaaS 기능은 조직이 기존 온-프레미스에서 ID 관리를 신속하게 채택하고 이동할 수 있는 솔루션과 기능을 제공함으로써 클라우드 관리 관리로의 전환을 가속화합니다. 기존 및 새 응용 프로그램을 계속 지원하면서 Azure AD에 시스템을 제공합니다.

이 백서에서는 하이브리드 IDaaS에 대한 Microsoft의 전략을 간략하게 설명하고 조직에서 기존 응용 프로그램에 Azure AD를 사용하는 방법을 설명합니다.

## <a name="the-azure-ad-approach-to-cloud-governed-identity-management"></a>클라우드관리 ID 관리에 대한 Azure AD 접근 방식

조직이 클라우드로 전환함에 따라 자동화및 사전 예방적 통찰력을 통해 활동에 대한 보안 및 가시성을 높이는 완벽한 환경을 제어할 수 있다는 확신이 필요합니다. "**클라우드 관리**" 조직은 클라우드에서 사용자, 응용 프로그램, 그룹 및 장치를 관리하고 관리하는 방법을 설명합니다.

이러한 현대 사회에서 조직은 SaaS 응용 프로그램의 확산과 협업 및 외부 ID의 역할이 증가함에 따라 대규모로 효과적으로 관리할 수 있어야 합니다. 클라우드의 새로운 위험 환경은 클라우드 사용자가 클라우드 및 온-프레미스 애플리케이션에 영향을 줄 수 있는 악의적인 행위자인 조직이 보다 신속하게 대응해야 함을 의미합니다.

특히 하이브리드 조직은 과거에 IT에서 수동으로 수행했던 작업을 위임하고 자동화할 수 있어야 합니다. 작업을 자동화하려면 다양한 ID 관련 리소스(사용자, 그룹, 응용 프로그램, 장치)의 수명 주기를 오케스트레이션하는 API 와 프로세스가 필요하므로 해당 리소스의 일상적인 관리를 외부의 더 많은 개인에게 위임할 수 있습니다. 핵심 IT 인력으로 전가됩니다. Azure AD는 온-프레미스 ID 인프라를 필요로 하지 않고 사용자에 대한 사용자 계정 관리 및 기본 인증을 통해 이러한 요구 사항을 해결합니다. 온-프레미스 인프라를 구축하지 않으면 온-프레미스 디렉터리에서 시작되지 않았지만 액세스 관리가 비즈니스 결과를 달성하는 데 중요한 비즈니스 파트너와 같은 새로운 사용자 커뮤니티가 있는 조직에 혜택을 줄 수 있습니다.

또한 이 새로운 세계에서 거버넌스 --- 관리없이 관리는 추가 기능이 아닌 ID 시스템의 통합 된 부분입니다. ID 거버넌스를 통해 조직은 직원, 비즈니스 파트너 및 공급업체, 서비스 및 응용 프로그램 전반에서 ID 및 액세스 수명 주기를 관리할 수 있습니다.

ID 거버넌스를 통합하면 조직이 클라우드 관리 관리로 전환하는 것을 더 쉽게 지원하고, IT가 확장가능하고, 게스트와 함께 새로운 과제를 해결하고, 고객이 보유한 것보다 더 깊은 통찰력과 자동화를 제공합니다. 온-프레미스 인프라스트럭시볼 서비스. 이 새로운 세상의 거버넌스는 조직이 조직 내의 리소스에 대한 액세스에 대한 투명성, 가시성 및 적절한 제어를 가질 수 있는 능력을 의미합니다. Azure AD를 통해 보안 운영 및 감사 팀은 --- 있는 사용자와 있어야 하는 사람(어떤 장치) 리소스, 해당 사용자가 해당 액세스로 수행하는 작업 및 조직이 적절한 용도를 가지고 있는지 여부에 대한 액세스 권한을 갖습니다. 회사 또는 규제 정책에 따라 액세스를 제거하거나 제한할 수 있습니다.

새로운 관리 모델은 SaaS 및 LOB(업무 용) 응용 프로그램을 모두 사용하여 이러한 응용 프로그램에 대한 액세스를 보다 쉽게 관리하고 보호할 수 있기 때문에 조직에 이점을 제공합니다. 응용 프로그램을 Azure AD와 통합하면 조직은 클라우드 및 온-프레미스에서 시작된 ID 모두에서 액세스를 일관되게 사용하고 관리할 수 있습니다. 응용 프로그램 수명 주기 관리가 더욱 자동화되고 Azure AD는 온-프레미스 ID 관리에서 쉽게 달성할 수 없었던 응용 프로그램 사용에 대한 풍부한 통찰력을 제공합니다. 조직은 Azure AD, Office 365 그룹 및 Teams 셀프 서비스 기능을 통해 액세스 관리 및 공동 작업을 위한 그룹을 쉽게 만들고 클라우드에서 사용자를 추가 하거나 제거하여 공동 작업 및 액세스 관리 요구 사항을 활성화할 수 있습니다.

클라우드 관리 관리에 적합한 Azure AD 기능을 선택하는 방법은 사용할 응용 프로그램과 이러한 응용 프로그램을 Azure AD와 통합하는 방법에 따라 달라집니다. 다음 섹션에서는 AD 통합 응용 프로그램 및 페더레이션 프로토콜을 사용하는 응용 프로그램(예: SAML, OAuth 또는 OpenID Connect)에 대해 취해야 할 방법을 간략하게 설명합니다.

## <a name="cloud-governed-management-for-ad-integrated-applications"></a>AD 통합 애플리케이션을 위한 클라우드 관리

Azure AD는 안전한 원격 액세스 및 해당 응용 프로그램에 대한 조건부 액세스를 통해 조직의 온-프레미스 Active Directory 통합 응용 프로그램에 대한 관리를 개선합니다. 또한 Azure AD는 다음과 같은 사용자의 기존 AD 계정에 대한 계정 수명 주기 관리 및 자격 증명 관리도 제공합니다.

* **온-프레미스 애플리케이션을 위한 안전한 원격 액세스 및 조건부 액세스**

많은 조직에서 온-프레미스 AD 통합 웹 및 원격 데스크톱 기반 응용 프로그램에 대한 클라우드 액세스를 관리하는 첫 번째 단계는 이러한 응용 프로그램 앞에 [응용 프로그램 프록시를](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy) 배포하여 안전한 원격 액세스를 제공하는 것입니다.

Azure AD에 Single Sign-On 되면 사용자는 외부 URL 또는 내부 애플리케이션 포털을 통해 클라우드와 온-프레미스 애플리케이션에 모두 액세스할 수 있습니다. 예를 들어 응용 프로그램 프록시는 원격 데스크톱, SharePoint뿐만 아니라 Tableau 및 Qlik와 같은 앱 및 LOB(업무) 응용 프로그램에 대한 원격 액세스 및 단일 사인온을 제공합니다. 또한 조건부 액세스 정책에는 [사용 약관을](https://docs.microsoft.com/azure/active-directory/governance/active-directory-tou) 표시하고 사용자가 응용 프로그램에 액세스하기 전에 [동의했는지 확인하는](https://docs.microsoft.com/azure/active-directory/conditional-access/require-tou) 것이 포함될 수 있습니다.

![앱 프록시 아키텍처](media/cloud-governed-management-for-on-premises/image2.png)

* **Active Directory 계정에 대한 자동 수명 주기 관리**

ID 거버넌스를 통해 조직은 *생산성* --- 조직에 가입할 때와 같이 필요한 리소스에 얼마나 빨리 액세스할 수 있습니까? --- 및 *보안* --- 해당 사용자의 고용 상태가 변경될 때와 같이 시간이 지남에 따라 액세스가 어떻게 변경되어야 합니까? ID 수명 주기 관리는 ID 거버넌스의 기반이며 대규모의 효과적인 거버넌스에는 애플리케이션에 대한 ID 수명 주기 관리 인프라를 현대화해야 합니다.

많은 조직에서 직원의 ID 수명 주기는 HCM(인적 자본 관리) 시스템에서 해당 사용자의 표현과 관련이 있습니다. Workday를 HCM 시스템으로 사용하는 조직의 경우 Azure AD는 AD의 사용자 계정이 [Workday의 작업자에 대해 자동으로 프로비전되고 프로비저닝해제되도록](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial)할 수 있습니다. 이렇게 하면 장자권 계정의 자동화를 통해 사용자 생산성이 향상되고 사용자가 역할을 변경하거나 조직을 떠날 때 응용 프로그램 액세스가 자동으로 업데이트되도록 하여 위험을 관리합니다. Workday 기반 사용자 프로비저닝 [배포 계획은](https://aka.ms/WorkdayDeploymentPlan) 5단계 프로세스에서 Workday의 모범 사례 구현에서 활성 디렉터리 사용자 프로비저닝 솔루션으로 조직을 안내하는 단계별 가이드입니다.

Azure AD 프리미엄에는 SAP, Oracle eBusiness 및 Oracle PeopleSoft를 비롯한 다른 온-프레미스 HCM 시스템에서 레코드를 가져올 수 있는 Microsoft ID 관리자도 포함되어 있습니다.

비즈니스 간 협업을 통해 조직 외부의 사람들에게 액세스 권한을 부여해야 하는 것이 점점 더 필요해지고 있습니다. [Azure AD B2B](https://docs.microsoft.com/azure/active-directory/b2b/) 공동 작업을 통해 조직은 자신의 회사 데이터에 대한 제어를 유지하면서 게스트 사용자 및 외부 파트너와 응용 프로그램 및 서비스를 안전하게 공유할 수 있습니다.

Azure AD는 필요에 따라 [게스트 사용자를 위한 AD에서 계정을 자동으로 만들](https://docs.microsoft.com/azure/active-directory/b2b/hybrid-cloud-to-on-premises) 수 있으므로 비즈니스 사용자는 다른 암호 없이 온-프레미스 AD 통합 응용 프로그램에 액세스할 수 있습니다. 조직은 게스트 [사용자 s에 대한 다단계 인증(MFA) 정책을](https://docs.microsoft.com/azure/active-directory/b2b/conditional-access)설정할 수 있으므로 응용 프로그램 프록시 인증 중에 MFA 검사가 수행됩니다. 또한 클라우드 B2B 사용자에서 수행되는 모든 [액세스 검토는](https://docs.microsoft.com/azure/active-directory/governance/manage-guest-access-with-access-reviews) 온-프레미스 사용자에게 적용됩니다. 예를 들어 클라우드 사용자가 수명 주기 관리 정책을 통해 삭제되면 온-프레미스 사용자도 삭제됩니다.

**Active Directory 계정에 대한 자격 증명 관리** Azure AD의 셀프 서비스 암호 재설정을 사용하면 암호를 잊어버린 사용자가 [온-프레미스 Active Directory에 기록된](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-writeback)변경된 암호를 사용하여 암호를 다시 인증하고 재설정할 수 있습니다. 암호 재설정 프로세스는 온-프레미스 Active Directory 암호 정책을 사용할 수도 있습니다. 셀프 서비스 암호 재설정 [배포 계획은](https://aka.ms/deploymentplans/sspr) 웹 및 Windows 통합 환경을 통해 사용자에게 셀프 서비스 암호 재설정을 롤아웃하는 모범 사례를 간략하게 설명합니다.

![Azure AD SSPR 아키텍처](media/cloud-governed-management-for-on-premises/image3.png)

마지막으로 사용자가 AD에서 암호를 변경할 수 있도록 허용하는 조직의 경우 AD는 현재 공개 미리 보기에서 [Azure AD 암호 보호 기능을](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad-on-premises)통해 Azure AD에서 사용하는 것과 동일한 암호 정책을 사용하도록 구성할 수 있습니다.

조직에서 응용 프로그램을 호스팅하는 운영 체제를 Azure로 이동하여 AD 통합 응용 프로그램을 클라우드로 이동할 준비가 되면 [Azure AD 도메인 서비스는](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-overview) AD 호환 도메인 서비스(예: 도메인 조인, 그룹 정책, LDAP 및 Kerberos/NTLM 인증)를 제공합니다. Azure AD 도메인 서비스는 조직의 기존 Azure AD 테넌트와 통합되므로 사용자가 회사 자격 증명을 사용하여 로그인할 수 있습니다. 또한 기존 그룹 및 사용자 계정을 사용하여 리소스에 대한 액세스를 보호하여 온-프레미스 리소스를 Azure 인프라 서비스에 보다 원활하게 '리프트 앤 시프트'할 수 있습니다.

![Azure AD Domain Services](media/cloud-governed-management-for-on-premises/image4.png)

## <a name="cloud-governed-management-for-on-premises-federation-based-applications"></a>온-프레미스 페더레이션 기반 애플리케이션에 대한 클라우드 관리 관리

온-프레미스 ID 공급자를 이미 사용 하는 조직의 경우 Azure AD로 응용 프로그램을 이동 하면 더 안전한 액세스 및 페더레이션 관리에 대 한 쉬운 관리 환경을 사용할 수 있습니다. Azure AD를 사용하면 Azure AD 조건부 액세스를 사용하여 Azure 다단계 인증을 비롯한 응용 프로그램별 세부 액세스 컨트롤을 구성할 수 있습니다. Azure AD는 응용 프로그램별 토큰 서명 인증서 및 구성 가능한 인증서 만료 날짜를 포함하여 더 많은 기능을 지원합니다. 이러한 기능, 도구 및 지침을 통해 조직은 온-프레미스 ID 공급자를 폐기할 수 있습니다. 예를 들어 Microsoft의 자체 IT는 17,987개의 응용 프로그램을 Microsoft의 내부 Active Directory 페더레이션 서비스(AD FS)에서 Azure AD로 옮겼습니다.

![Azure AD 진화](media/cloud-governed-management-for-on-premises/image5.png)

페더레이션된 응용 프로그램을 Id 공급자로 Azure AD로 https://aka.ms/migrateapps 마이그레이션하려면 다음에 대한 링크를 포함합니다.

* [응용 프로그램을 Azure Active Directory로 마이그레이션하는](https://aka.ms/migrateapps/whitepaper)백서에서는 마이그레이션의 이점을 제시하고 검색, 분류, 마이그레이션 및 진행 중인 관리의 네 가지 단계에서 마이그레이션을 계획하는 방법을 설명합니다. 프로세스에 대해 생각하고 프로젝트를 사용하기 쉬운 조각으로 세분화하는 방법을 안내합니다. 문서 전체에는 과정에 도움이 되는 중요한 리소스에 대한 링크가 있습니다.

* 솔루션 [가이드는 응용 프로그램 인증 마이그레이션 을 Active Directory 페더리 서비스에서 Azure Active Directory로 마이그레이션하는](https://aka.ms/migrateapps/adfssolutionguide) 것과 동일한 네 단계의 응용 프로그램 마이그레이션 프로젝트를 계획하고 실행하는 방법을 자세히 살펴봅습니다. 이 가이드에서는 응용 프로그램을 AD FS(Active DS)에서 Azure AD로 이동하는 특정 목표에 이러한 단계를 적용하는 방법을 알아봅니다.

* [Active Directory 페더리 서비스 마이그레이션 준비 스크립트는](https://aka.ms/migrateapps/adfstools) 기존 온-프레미스 Active Directory 페더레이션 서비스(AD FS) 서버에서 실행하여 Azure AD로 마이그레이션할 응용 프로그램의 준비 상태를 확인할 수 있습니다.

## <a name="ongoing-access-management-across-cloud-and-on-premises-applications"></a>클라우드 및 온-프레미스 애플리케이션 전반에 걸친 지속적인 액세스 관리

조직은 확장 가능한 액세스를 관리하는 프로세스가 필요합니다. 사용자는 계속해서 액세스 권한을 누적하고 처음에 프로비전된 것 이상으로 끝납니다. 또한 엔터프라이즈 조직은 액세스 정책 및 제어를 지속적으로 개발하고 시행하기 위해 효율적으로 확장할 수 있어야 합니다.

IT 대리자는 일반적으로 비즈니스 의사 결정권자에 대한 승인 결정에 액세스합니다. 또한 IT에는 사용자가 스스로가 포함될 수 있습니다. 예를 들어 유럽에 있는 회사의 마케팅 애플리케이션에서 기밀 고객 데이터에 액세스하는 사용자는 회사의 정책을 알아야 합니다. 게스트 사용자는 초대받은 조직의 데이터에 대한 처리 요구 사항을 인식하지 못할 수도 있습니다.

조직은 [SaaS 응용 프로그램에](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)사용자 프로비전또는 [SCIM(도메인 간 ID 관리)](https://docs.microsoft.com/azure/active-directory/manage-apps/use-scim-to-provision-users-and-groups)표준을 사용하여 통합된 응용 프로그램과 결합된 동적 [그룹과](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership)같은 기술을 통해 액세스 수명 주기 프로세스를 자동화할 수 있습니다. 또한 조직은 [온-프레미스 응용 프로그램에 액세스할 수 있는 게스트 사용자를 제어할](https://docs.microsoft.com/azure/active-directory/b2b/hybrid-cloud-to-on-premises)수 있습니다. 그런 다음, 정기 [Azure AD 액세스 검토](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)를 사용하여 이러한 액세스 권한을 정기적으로 검토할 수 있습니다.

## <a name="future-directions"></a>향후 방향

하이브리드 환경에서 Microsoft의 전략은 **클라우드가 ID를 제어하는**배포를 활성화하고 온-프레미스 디렉터리 및 기타 온-프레미스 응용 프로그램과 같은 기타 ID 시스템이 액세스 권한을 가진 사용자를 프로비전하는 대상입니다. 이 전략은 해당 응용 프로그램 및 워크로드에 의존하는 권한, ID 및 액세스를 계속 보장합니다. 이 경우 조직은 클라우드에서 최종 사용자 생산성을 전적으로 높일 수 있습니다.

![Azure AD 아키텍처](media/cloud-governed-management-for-on-premises/image6.png)

## <a name="next-steps"></a>다음 단계

이 여정을 시작하는 방법에 대한 자세한 내용은 에 <https://aka.ms/deploymentplans> 있는 Azure AD 배포 계획을 참조하십시오. Azure Active Directory(Azure AD) 기능을 배포하는 방법에 대한 종단 간 지침을 제공합니다. 각 계획은 일반적인 Azure AD 기능을 성공적으로 출시하는 데 필요한 비즈니스 가치, 계획 고려 사항, 디자인 및 운영 절차를 설명합니다. Microsoft는 Azure AD를 사용하여 클라우드에서 관리하는 데 새로운 기능을 추가할 때 고객 배포 및 기타 피드백을 통해 얻은 모범 사례를 사용하여 배포 계획을 지속적으로 업데이트합니다.
