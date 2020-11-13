---
title: 모든 앱을 Azure AD와 통합 하는 5 단계
description: 이 가이드에서는 모든 응용 프로그램을 Azure AD와 통합 하는 방법을 설명 합니다. 각 단계에서 값을 설명 하 고 기술 세부 정보를 설명 하는 리소스에 대 한 링크를 제공 합니다.
services: active-directory
author: knicholasa
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 08/05/2020
ms.author: nichola
ms.openlocfilehash: 26dd255e6edff7c339ab9766bb71889cf4e2deaa
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/13/2020
ms.locfileid: "94577646"
---
# <a name="five-steps-for-integrating-all-your-apps-with-azure-ad"></a>모든 앱을 Azure AD와 통합 하는 5 단계

Azure AD (Azure Active Directory)는 Microsoft 클라우드 기반 id 및 액세스 관리 서비스입니다. Azure AD는 고객, 파트너 및 직원이 필요한 응용 프로그램에 액세스할 수 있도록 보안 인증 및 권한 부여 솔루션을 제공 합니다. Azure AD에서 [조건부 액세스](../conditional-access/overview.md), [multi-factor authentication](../authentication/concept-mfa-howitworks.md), [single sign-on](../hybrid/how-to-connect-sso.md)및 [자동 사용자 프로 비전](../app-provisioning/user-provisioning.md) 을 사용 하면 id 및 액세스 관리를 쉽고 안전 하 게 수행할 수 있습니다.

회사에 Microsoft 365 구독이 있는 경우 이미 Azure AD를 [사용](/office365/enterprise/about-office-365-identity) 하 고 있을 것입니다. 그러나 Azure AD는 모든 응용 프로그램에 사용할 수 있으며, [응용 프로그램 관리를 중앙 집중화](../manage-apps/common-scenarios.md) 하 여 전체 앱 포트폴리오에서 동일한 id 관리 기능, 도구 및 정책을 사용할 수 있습니다. 이렇게 하면 보안을 강화 하 고, 비용을 절감 하 고, 생산성을 높이고, 규정 준수를 보장할 수 있는 통합 솔루션을 제공 합니다. 온-프레미스 앱에 대 한 원격 액세스 권한을 얻게 됩니다.

이 가이드에서는 모든 응용 프로그램을 Azure AD와 통합 하는 방법을 설명 합니다. 각 단계에서 값을 설명 하 고 기술 세부 정보를 설명 하는 리소스에 대 한 링크를 제공 합니다. 이러한 단계를 권장 하는 순서 대로 제공 합니다. 그러나 가장 많은 값을 추가 하는 항목을 사용 하 여 시작 하려면 프로세스의 모든 부분으로 이동할 수 있습니다.

[응용 프로그램을 Azure Active Directory 페이지로 마이그레이션하기 위한 리소스](../manage-apps/migration-resources.md) 에서 찾을 수 있는 심층 비즈니스 프로세스 백서를 포함 하 여이 항목의 기타 참고 자료입니다.

## <a name="1-use-azure-ad-for-new-applications"></a>1. 새 응용 프로그램에 Azure AD 사용

먼저 새로 획득 한 응용 프로그램에 집중 합니다. 비즈니스에서 새 응용 프로그램을 사용 하기 시작 하면 [AZURE AD 테 넌 트에 즉시 추가](../manage-apps/add-application-portal.md) 합니다. 조직에서 Azure AD에 새 앱을 추가 하는 것이 표준 관행 인 회사 정책을 설정 합니다. 이는 기존 비즈니스 프로세스를 최소화 하 고 사용자가 현재 환경에서 업무를 수행 하는 방식을 변경 하지 않고 앱을 통합 하 여 얻은 가치를 조사 하 고 입증할 수 있도록 합니다.

Azure Active Directory (Azure AD)에는 쉽게 시작할 수 있는 수천 개의 사전 통합 된 응용 프로그램이 포함 된 갤러리가 있습니다. 다음과 같이 인기 있는 앱과 통합 하기 위한 단계별 [자습서](../saas-apps/tutorial-list.md) 를 사용 하 여 [Azure AD 조직에 갤러리 앱을 추가할](../manage-apps/add-application-portal.md) 수 있습니다.

- [ServiceNow](../saas-apps/servicenow-tutorial.md)
- [Workday](../saas-apps/workday-tutorial.md)
- [Salesforce](../saas-apps/salesforce-tutorial.md)
- [AWS](../saas-apps/amazon-web-service-tutorial.md)
- [Slack](../saas-apps/slack-tutorial.md)

또한 조직에 이미 있는 응용 프로그램이 나 아직 Azure AD 갤러리에 속하지 않은 공급 업체의 타사 응용 프로그램을 포함 하 여 [갤러리에 없는 응용 프로그램을 통합할](../manage-apps/view-applications-portal.md)수 있습니다. [응용 프로그램이 없는 경우 갤러리에 앱을 추가할](../azuread-dev/howto-app-gallery-listing.md) 수도 있습니다.

마지막으로, 사내에서 개발 하는 앱을 통합할 수도 있습니다. 이 내용은이 가이드의 5 단계에서 다룹니다.

## <a name="2-determine-existing-application-usage-and-prioritize-work"></a>2. 기존 응용 프로그램 사용 및 작업 우선 순위 결정

그런 다음 직원이 자주 사용 하는 응용 프로그램을 검색 하 고 Azure AD와 통합 하기 위한 작업의 우선 순위를 지정 합니다.

Microsoft Cloud App Security&#39;s [클라우드 검색 도구](/cloud-app-security/tutorial-shadow-it) 를 사용 하 여 네트워크에서 섀도 it를 검색 하 고 관리할 수 있습니다 &quot; &quot; (즉, IT 부서에서 관리 하지 않는 앱). [Microsoft DEFENDER ATP (Advanced Threat Protection)를 사용](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection) 하 여 검색 프로세스를 간소화 하 고 확장할 수 있습니다.

또한 Azure Portal의 [AD FS 응용 프로그램 작업 보고서](../manage-apps/migrate-adfs-application-activity.md) 를 사용 하 여 조직의 모든 AD FS 앱을 검색 하 고, 해당 앱에 로그인 한 고유 사용자 수를 검색 하 고, Azure AD와 통합 하는 데 필요한 호환성을 확인할 수 있습니다.

기존 가로를 검색 한 후에 [는 계획을 만들고](../manage-apps/migration-resources.md) 통합할 우선 순위가 가장 높은 앱의 우선 순위를 지정 합니다. 이 프로세스를 안내 하기 위해 수행할 수 있는 몇 가지 예제 질문은 다음과 같습니다.

- 가장 많이 사용 되는 앱은 무엇 인가요?
- Riskiest?
- 향후에 서비스를 해제할 앱은 불필요 하 게 이동
- 온-프레미스에 유지 해야 하 고 클라우드로 이동할 수 없는 앱은 무엇 인가요?

모든 앱이 통합 되 고 더 이상 여러 id 솔루션을 사용 하지 않는 경우 가장 큰 이점과 비용 절감 효과를 볼 수 있습니다. 그러나이 목표를 달성 하는 과정에서 보다 쉽게 id를 관리 하 고 보안을 강화할 수 있습니다. 이 시간을 사용 하 여 작업의 우선 순위를 지정 하 고 상황에 맞는 의미를 결정 하려고 합니다.

## <a name="3-integrate-apps-that-rely-on-other-identity-providers"></a>3. 다른 id 공급자를 사용 하는 앱 통합

검색 프로세스 중에 IT 부서에서 추적 되지 않는 응용 프로그램을 발견 했을 수 있으며,이로 인해 데이터와 리소스가 취약 하 게 유지 됩니다. 또한 Active Directory Federation Services (ADFS) 또는 다른 id 공급자를 비롯 한 대체 id 솔루션을 사용 하는 응용 프로그램이 있을 수 있습니다. Id 및 액세스 관리를 통합 하 여 비용을 절감 하 고 보안을 강화 하는 방법을 고려 합니다. 보유 하 고 있는 id 솔루션의 수를 줄입니다.

- 온-프레미스 사용자 프로 비전 및 인증 뿐만 아니라 동일한 서비스에 대 한 다른 클라우드 id 공급자에 게 지불 된 라이선스 요금을 제거 하 여 비용을 절감할 수 있습니다.
- Id 및 액세스 관리 프로세스에서 관리 오버 헤드를 줄이고 중복성이 더 적게 강화 된 보안 기능을 사용할 수 있습니다.
- 직원이 [Myapps 포털](../manage-apps/access-panel-collections.md)을 통해 필요한 모든 응용 프로그램에 대 한 보안 Single Sign-On 액세스를 얻을 수 있도록 합니다.
- 앱 사용에서 가져오는 데이터의 양을 늘리고, 새로 추가 된 앱에 대 한 혜택을 확장 하 여 조건부 액세스와 같은 Azure AD&#39;s [identity protection](../identity-protection/overview-identity-protection.md) 관련 서비스의 인텔리전스를 향상 합니다.

Azure AD와 앱을 통합 하는 비즈니스 프로세스를 관리 하기 위한 지침을 게시 했습니다. 여기에는 비즈니스 및 응용 프로그램 소유자가 인식 하 고 관심이 있는 [포스터](https://aka.ms/AppOnePager) 와 [프레젠테이션을](https://aka.ms/AppGuideline) 사용할 수 있습니다. 사용자 고유의 브랜딩을 사용 하 여 이러한 샘플을 수정 하 고이 프로세스를 완료 하는 동안 회사 포털, 뉴스레터 또는 기타 매체를 통해 조직에 게시할 수 있습니다.

먼저 Active Directory Federation Services (ADFS) 사용을 평가 하는 것이 좋습니다. 많은 조직에서 ADFS를 사용 하 여 SaaS 앱, 사용자 지정 기간 업무 앱, Microsoft 365 및 Azure AD 기반 앱을 인증 합니다.

![다이어그램은 온-프레미스 앱, lob (기간 업무) 앱, SaaS 앱 및 Azure AD를 통해 Office 365을 통해 점선으로 연결 하 여 Active Directory 및 AD FS에 연결 하는 기능을 보여 줍니다.](\media\five-steps-to-full-application-integration-with-azure-ad\adfs-integration-1.png)

Id 관리 솔루션의 [중심으로 ADFS를 AZURE AD로 바꿔](../manage-apps/migrate-adfs-apps-to-azure.md) 이 구성을 업그레이드할 수 있습니다. 이렇게 하면 직원 들이 액세스 하려고 하는 모든 앱에 대해 로그온 할 수 있으며, 위에서 언급 한 다른 혜택 외에도 직원이 [Myapps 포털](../user-help/my-apps-portal-end-user-access.md)을 통해 필요한 모든 비즈니스 응용 프로그램을 쉽게 찾을 수 있습니다.

![다이어그램은 Active Directory 및 AD FS, lob (기간 업무) 앱, SaaS 앱 및 Office 365을 통해 점선으로 연결 하 여 Azure Active Directory에 온-프레미스 앱을 보여 줍니다.](\media\five-steps-to-full-application-integration-with-azure-ad\adfs-integration-2.png)

Azure AD가 중앙 id 공급자가 되 면 페더레이션 솔루션을 사용 하는 대신 ADFS에서 전체를 전환할 수 있습니다. 이전에 인증을 위해 ADFS를 사용한 앱은 이제 Azure AD만 사용할 수 있습니다.

![다이어그램은 온-프레미스, lob (기간 업무) 앱, SaaS 앱 및 Office 365을 표시 하는 모든 기능을 Azure Active Directory에 점선으로 연결 합니다. Active Directory 및 AD FS가 없습니다.](\media\five-steps-to-full-application-integration-with-azure-ad\adfs-integration-3.png)

다른 클라우드 기반 id 공급자를 사용 하는 앱을 Azure AD로 마이그레이션할 수도 있습니다. 조직에서 여러 IAM (Id 액세스 관리) 솔루션을 사용할 수 있습니다. 하나의 Azure AD 인프라로 마이그레이션하는 것은 IAM 라이선스 (온-프레미스 또는 클라우드)에 대 한 종속성 및 인프라 비용을 줄일 수 있는 기회입니다. M365 라이선스를 통해 Azure AD에 이미 지불 했을 수 있는 경우 다른 IAM 솔루션의 추가 비용을 지불할 이유가 없습니다.

## <a name="4-integrate-on-premises-applications"></a>4. 온-프레미스 응용 프로그램 통합

일반적으로 응용 프로그램은 회사 네트워크에 연결 된 동안에만 액세스를 허용 하 여 보안을 유지 했습니다. 그러나 점점 연결 된 지역에서는 전 세계 어디에 있든 상관 없이 고객, 파트너 및/또는 직원을 위한 앱에 대 한 액세스를 허용 하려고 합니다. Appproxy ( [azure AD 응용 프로그램 프록시](../manage-apps/what-is-application-proxy.md) )는 기존 온-프레미스 앱을 azure ad에 연결 하는 azure ad의 기능이 며,이를 위해에 지 서버 또는 기타 추가 인프라를 유지 관리할 필요가 없습니다.

![다이어그램은 작동 중인 응용 프로그램 프록시 서비스를 보여 줍니다. 사용자가 ""에 액세스 https://sales.contoso.com 하 고 해당 요청이 ""를 통해 온 https://sales-contoso.msappproxy.net -프레미스 주소에 Azure Active Directory ""를 통해 리디렉션됩니다. http://sales](./media/five-steps-to-full-application-integration-with-azure-ad\app-proxy.png)

자습서: 응용 프로그램 프록시를 사용 하도록 설정 하 고 온-프레미스 응용 프로그램을 Azure AD 테 넌 트에 추가 하기 위해 [Azure Active Directory에서 응용 프로그램 프록시를 통해 원격 액세스용 온-프레미스 응용 프로그램 추가](../manage-apps/application-proxy-add-on-premises-application.md) 를 사용할 수 있습니다.

또한 F5 빅 IP APM 또는 Zscaler Private 액세스와 같은 응용 프로그램 배달 컨트롤러를 통합할 수 있습니다. Azure AD와 통합 하면 파트너 제품의 트래픽 관리 및 보안 기능과 함께 Azure AD의 최신 인증 및 id 관리가 제공 됩니다. 이 솔루션의 [보안 하이브리드 액세스](../manage-apps/secure-hybrid-access.md)를 호출 합니다. 현재 다음 서비스 중 하나를 사용 하는 경우 Azure AD와 통합 하는 방법을 단계별로 안내 하는 자습서가 있습니다.

- [Akamai EAA(엔터프라이즈 애플리케이션 액세스)](../saas-apps/akamai-tutorial.md)
- [Citrix 응용 프로그램 제공 컨트롤러 (ADC)](../saas-apps/citrix-netscaler-tutorial.md) (이전의 citrix Netscaler)
- [F5 Big-IP APM](../saas-apps/headerf5-tutorial.md)
- [ZPA(Zscaler Private Access)](../saas-apps/zscalerprivateaccess-tutorial.md)

## <a name="5-integrate-apps-your-developers-build"></a>5. 개발자가 빌드한 앱 통합

회사 내에서 빌드된 앱의 경우 개발자는 [Microsoft id 플랫폼](../develop/index.yml) 을 사용 하 여 인증 및 권한 부여를 구현할 수 있습니다. 플랫폼과 통합 된 응용 프로그램은 [AZURE AD에 등록](../develop/quickstart-register-app.md) 되 고 포트폴리오의 다른 앱과 마찬가지로 관리 됩니다.

개발자는 내부용 앱과 고객 연결 앱 모두에 플랫폼을 사용할 수 있으며 플랫폼을 사용 하는 다른 이점도 있습니다. 플랫폼의 일부인 [MSAL (Microsoft 인증 라이브러리)](../develop/msal-overview.md)을 사용 하면 개발자는 multi-factor Authentication과 같은 최신 환경을 지원 하 고 보안 키를 사용 하 여 앱을 직접 구현할 필요 없이 앱에 액세스할 수 있습니다. 또한 Microsoft id 플랫폼과 통합 된 앱은 조직에서 생산성, id 및 보안의 패턴을 설명 하는 Microsoft 365 데이터를 제공 하는 통합 API 끝점 [Microsoft Graph](../develop/microsoft-graph-intro.md) 액세스할 수 있습니다. 개발자는이 정보를 사용 하 여 사용자의 생산성을 높이는 기능을 구현할 수 있습니다. 예를 들어 사용자가 최근에 상호 작용 한 사용자를 식별 하 고 앱&#39;s UI에서이를 인식 합니다.

지원 되는 언어 및 플랫폼의 [많은 코드 샘플](../develop/sample-v2-code.md) 뿐만 아니라 플랫폼에 대 한 포괄적인 소개를 제공 하는 [비디오 시리즈](https://www.youtube.com/watch?v=zjezqZPPOfc&amp;list=PLLasX02E8BPBxGouWlJV-u-XZWOc2RkiX) 를 제공 합니다.

## <a name="next-steps"></a>다음 단계

- [애플리케이션을 Azure Active Directory로 마이그레이션하기 위한 리소스](../manage-apps/migration-resources.md)