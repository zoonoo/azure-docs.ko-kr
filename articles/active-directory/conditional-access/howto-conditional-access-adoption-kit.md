---
title: 조건부 액세스 도입 키트-Azure Active Directory
description: 리소스에 액세스 하기 위해 Azure AD 조건부 액세스 채택
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: martinco
ms.collection: M365-identity-device-management
ms.openlocfilehash: daaced0aae595e839f2b2476562fc5caffb63640
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73473929"
---
#  <a name="adopting-azure-ad-conditional-access"></a>Azure AD 조건부 액세스 채택

모바일 우선, 클라우드 우선 세계에서 사용자는 다양 한 종류의 장치 및 앱을 사용 하 여 어디서 나 조직의 리소스에 액세스할 수 있습니다. 따라서 리소스에 액세스할 수 있는 사용자에 게는 더 이상 충분 하지 않습니다. 사용자가 누구 이며 어떤 장치를 사용 하 고 있는지를 식별 하는 사용자를 제어할 수 있습니다.

이 컨트롤을 제공 하기 위해 **AD (Azure Active Directory) 조건부 액세스** 를 사용 하면 MFA (Multi-Factor Authentication)와 같이 사용자가 응용 프로그램에 액세스 하기 위해 충족 해야 하는 조건을 지정할 수 있습니다. 조건부 액세스 정책을 사용 하 여 권한 있는 사용자 (클라우드 앱에 대 한 액세스 권한이 부여 된 사용자)가 특정 조건에서 클라우드 앱에 액세스 하는 방법을 제어 합니다. 자세한 내용은 [Azure Active Directory의 조건부 액세스 정의](overview.md) 를 참조 하세요.

## <a name="key-benefits"></a>주요 이점

Azure AD 조건부 액세스를 사용할 경우의 주요 이점은 다음과 같습니다.

* **생산성 향상:** CA (조건부 액세스) 정책을 사용 하면 사용자에 게 MFA를 사용 하거나, 액세스를 차단 하거나, 신뢰할 수 있는 장치를 사용 하는 데 필요한 시점을 대상으로 지정할 수 있습니다. 예를 들어 회사 네트워크에서 사용자가 응용 프로그램에 MFA를 요구 하는 것과 같은 정책을 설정할 수 있습니다. MFA 요청을 줄이면 사용자가 로그인 할 때마다 MFA를 유지 해야 하는 경우 보다 생산성을 높일 수 있습니다. 또한 Azure AD 조건부 액세스를 사용 하 여 사용자별 정책을 지정 하 고 앱 별 정책을 만들 수도 있습니다.
* **위험 관리:** 조건부 액세스 정책을 사용 하도록 설정 하면 클라우드 규모의 id 보호, 위험 기반 액세스 제어 기능 및 기본 multi-factor authentication 지원이 제공 됩니다. Id 보호를 사용 하 여 조건부 액세스를 결합 하면 응용 프로그램에 대 한 액세스가 차단 되거나 제어 되는 경우를 정의할 수 있습니다.
* **주소 규정 준수 및 거 버 넌 스:** 응용 프로그램에 대 한 액세스 요청 및 승인은 감사 하 고 전체 응용 프로그램 사용을 이해 하는 것은 Azure AD에서 수행 되는 모든 응용 프로그램 액세스 요청에 대 한 기본 감사 로그를 지원 하기 때문에 감사에는 요청자 id, 요청 된 날짜, 비즈니스 근거, 승인 상태 및 승인자 id가 포함 됩니다. 이 데이터는 API에서 사용할 수 있으며,이를 통해이 데이터를 SIEM (보안 인시던트 및 이벤트 모니터링) 시스템으로 가져올 수 있습니다.
* **비용 관리:** 액세스 정책을 Azure AD로 이동 하면 조건부 액세스를 위해 ADFS (Active Directory Federation Services)와 같은 사용자 지정 또는 온-프레미스 솔루션에 대 한 의존도가 줄어들고 해당 인프라를 실행 하는 비용이 절감 됩니다.

## <a name="customer-case-studies"></a>고객 사례 연구

대부분의 조직에서 Azure AD 조건부 액세스를 사용 하 여 조건에 따라 클라우드 앱에 액세스 하는 자동화 된 액세스 제어 결정을 정의 및 구현 하는 방법을 알아봅니다. 다음 주요 스토리에서는 이러한 고객의 요구 사항을 충족 하는 방법을 보여 줍니다.

* [**Wipro** 는 Microsoft 클라우드 보안 도구를 사용 하 여 모바일 생산성을 높여 고객의 계약을 개선 합니다.](https://customers.microsoft.com/story/wipro-professional-services-enterprise-mobility-security) Azure AD의 조건부 액세스 정책을 통해 회사는 자신의 회사 데이터에 대 한 제어를 유지 하면서 자체 자격 증명---사용할 수 있는 신뢰할 수 있는 외부 엔터티---문서, 리소스 및 응용 프로그램을 공유할 수 있습니다.
* [**Accenture** App security Accenture Microsoft 클라우드를 사용 하 여 클라우드로의 이동을 보호](https://customers.microsoft.com/story/accenture-professional-services-cloud-app-security) 하는 것은 게이트에 대 한 Azure Active Directory 조건부 액세스를 사용 하는 Cloud App Security의 [조건부 액세스 앱 제어](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad) 기능을 평가 하 고 있습니다. 특정 조건에 따라 응용 프로그램 액세스 LePenske은이 기능이 다운로드를 금지 하는 동안 읽기 전용 파일 액세스를 사용 하도록 설정 하는 데 유용할 수 있음을 의미 합니다.
* [ **Aramex** 배달 제한 된 글로벌 물류 및 교통 회사는 id 및 액세스 관리 솔루션을 통해 클라우드로 연결 된 사무실을 만듭니다](https://customers.microsoft.com/story/aramex-azure-active-directory-travel-transportation-united-arab-emirates-en). Aramex의 원격 직원은 보안 액세스를 특히 어렵습니다. 회사는 이제 이러한 원격 직원이 네트워크 외부에서 SaaS 응용 프로그램에 액세스할 수 있도록 조건부 액세스를 적용 합니다. 조건부 액세스 규칙은 Multi-Factor Authentication를 적용할지 여부를 결정 하 고 올바른 사용자 에게만 권한을 부여 합니다.

Azure AD 조건부 액세스에 대 한 고객 및 파트너 환경에 대해 자세히 알아보려면 [azure에서 사용자가 수행 하는 놀라운 작업을 참조](https://azure.microsoft.com/case-studies/?service=active-directory)하세요.

## <a name="announcements"></a>알림

Azure AD는 지속적인 향상되고 있습니다. 최신 개발을 최신 상태로 유지 하려면 [Azure Active Directory의 새로운 기능](../fundamentals/whats-new.md) 을 참조 하세요.

기술 커뮤니티 및 Microsoft Id 디비전의 최근 블로그:

* 2018 년 9 월 24 일 [Azure Active Directory Azure Databricks에서 조건부 액세스](https://azure.microsoft.com/updates/azure-active-directory-conditional-access-in-azure-databricks/)
* 2018 년 9 월 21 일, [AZURE AD 조건부 액세스 사용자 지정 컨트롤은 공개 미리 보기로 제공 됩니다](https://azure.microsoft.com/updates/azure-ad-conditional-access-custom-controls-are-in-public-preview/) .
* 2018 년 9 월 21 일, [Microsoft Cloud App Security에 대 한 제한 된 액세스에 대 한 AZURE AD 조건부 액세스 지원 사용 가능](https://azure.microsoft.com/updates/azure-ad-conditional-access-support-for-limited-access-with-microsoft-cloud-app-security-is-now-available/)
* 2018 년 9 월 21 일, [AZURE AD 조건부 액세스: 이제 미리 보기로 제공 되는 iOS/Android 플랫폼에 대 한 Managed browser 지원](https://azure.microsoft.com/updates/azure-ad-conditional-access-managed-browser-support-for-ios-android-platforms-now-in-preview/)
* 2018 년 9 월 21 일, [국가 코드에 대 한 AZURE AD 조건부 액세스는 공개 미리 보기로 제공 됩니다](https://azure.microsoft.com/updates/azure-ad-conditional-access-for-country-codes-is-in-public-preview/) .
* 2018 년 9 월 21 일, [AZURE AD 사용 약관 현재 사용 가능](https://azure.microsoft.com/updates/azure-ad-terms-of-use-now-available/)

## <a name="learning-resources"></a>학습 리소스

Azure AD 조건부 액세스의 작동 방식에 대 한 개요를 보려면 아래 링크를 따르세요.

* "[Azure Active Directory에서 조건부 액세스 란?"에](overview.md)대해 알아봅니다.
* "[Azure Active Directory 조건부 액세스의 조건 이란?"를](conditions.md)알아봅니다.
* "[Azure Active Directory 조건부 액세스의 위치 조건 이란?"를 확인 합니다](location-condition.md).
* "[Azure Active Directory 조건부 액세스의 액세스 제어 정의"를](controls.md)알아봅니다.
* "[Azure Active Directory 조건부 액세스를 사용 하는 경우는 무엇 인가요?"](what-if-tool.md) 를 찾습니다.
* [Azure Active Directory의 조건부 액세스에 대 한 모범 사례](best-practices.md) 준수

또한 Azure Active Directory와 통합 된 모든 서비스에 대 한 액세스를 보호 하기 위한 지침은 다음 링크를 참조 하세요.

* [Id 및 장치 액세스 구성](https://docs.microsoft.com/microsoft-365/enterprise/microsoft-365-policies-configurations). 지정 된 조건부 액세스 정책 및 관련 기능 집합을 포함 하 여 권장 환경 및 구성을 구현 함으로써 Enterprise Mobility + Security 제품을 통해 클라우드 서비스에 대 한 보안 액세스를 구성 하는 방법을 설명 합니다.
* [조건부 액세스 설정 참조를 Azure Active Directory](technical-reference.md)합니다. 자세한
   * 조건부 액세스를 사용 하는 앱은 무엇 인가요?
   * 조건부 액세스를 사용 하도록 설정 된 서비스는 무엇 인가요?
* [보안 사용자 액세스에 대 한 조건부 액세스를 Azure Active Directory 사용 하도록 설정](https://www.youtube.com/watch?v=eLAYBwjCGoA)합니다. 이 비디오를 시청 하 여 조건부 액세스에서 다른 Enterprise 및 Mobility Suite의 워크 로드에 대 한 역할을 수행 하는 방법을 알아보세요.

### <a name="training-videos"></a>교육 비디오

**Enterprise Mobility + Security의 조건부 액세스**
   > [!VIDEO https://www.youtube.com/embed/A7IrxAH87wc]

**장치 기반 조건부 액세스**
   > [!VIDEO https://www.youtube.com/embed/AdM0zYB-3WQ]

**보안 사용자 액세스에 대 한 조건부 액세스에 대 한 Azure Active Directory 사용**
   > [!VIDEO https://www.youtube.com/embed/eLAYBwjCGoA]

### <a name="online-courses"></a>온라인 과정

[Pluralsight.com](https://www.pluralsight.com/)에서 다음과 같은 조건부 액세스 과정을 참조 하세요.

* Pluralsight.com: [Microsoft Azure에서 Id 관리 디자인](https://www.pluralsight.com/courses/microsoft-azure-identity-management-design)
   * "이 과정에서는 Azure AD를 사용 하 여 id 관리 솔루션을 디자인 하기 위해 알아야 하는 주요 항목을 안내 합니다." Azure AD 조건부 액세스는 "Azure AD와의 역할 및 Access Control 사용" 모듈에서 설명 합니다.

* Pluralsight.com: [Microsoft Azure에 대 한 인증 디자인](https://www.pluralsight.com/courses/microsoft-azure-authentication-design)
   * "이 과정에서는 Azure AD를 사용 하 여 모든 클라우드 인증 요구 사항을 해결 하는 방법을 설명 합니다." Azure AD 조건부 액세스는 "다양 한 시나리오에 대 한 인증 요구 사항" 모듈에서 다룹니다.

* Pluralsight.com: [Microsoft Azure에 대 한 디자인 권한 부여](https://www.pluralsight.com/courses/microsoft-azure-authorization-design)
   * "이 과정에서는 Azure 및 Azure AD에서 사용할 수 있는 권한 부여 옵션을 설명 합니다." Azure AD 조건부 액세스는 "Azure Resource Manager 및 Azure AD를 사용 하 여 권한 부여" 모듈에서 다룹니다.

### <a name="books"></a>온라인 설명서

* O'Reilly- [Azure 솔루션-두 번째 버전을 구현 합니다.](https://www.oreilly.com/library/view/implementing-azure-solutions/9781789343045/b7ead3db-eb1c-4ace-897e-86ee25ea86be.xhtml)
   * "Azure 서비스를 시작 및 실행 하 고 조직에서 이러한 서비스를 구현 하는 방법을 알아봅니다. Azure AD 조건부 액세스는 [Azure Active Directory 배포 및 동기화](https://learning.oreilly.com/library/view/implementing-azure-solutions/9781789343045/02ca8bba-08cf-4691-a7d0-1b96e286e7ea.xhtml)장에서 설명 합니다. "

* Wiley- [설계자를 위한 인프라 서비스 Microsoft Azure: 클라우드 솔루션 디자인](https://www.wiley.com/Microsoft+Azure+Infrastructure+Services+for+Architects%3A+Designing+Cloud+Solutions-p-9781119596547)
   * "Microsoft Azure를 활용 하는 환경을 이해, 평가, 배포 및 유지 관리 하는 데 필요한 모든 것은 다음과 같습니다."

## <a name="white-papers"></a>백서

* 2018 년 12 월 18 일 게시 [된 Azure Active Directory를 사용 하 여 복원 가능한 액세스 제어 관리 전략을 만듭니다](../authentication/concept-resilient-controls.md) .
   * 이 문서에서는 예기치 않은 중단 발생 시 잠금 위험을 줄이기 위해 조직이 채택할 수 있는 전략에 대 한 지침을 제공 합니다.

* 2018 년 9 월 18 일에 게시 된 [응용 프로그램을 Azure Active Directory으로 마이그레이션하기 위한 리소스](../manage-apps/migration-resources.md)
   * 이 백서에는 Azure Active Directory (Azure AD)로 응용 프로그램 액세스 및 인증을 마이그레이션하는 데 도움이 되는 리소스 목록이 포함 되어 있습니다.

* 2018 년 7 월 12 일 게시 됨 [Azure 보안 및 규정 준수 청사진: 영국 공식 워크 로드를 위한 PaaS 웹 응용 프로그램 호스팅](../../security/blueprints/ukofficial-paaswa-overview.md)
   * Azure Blueprint는 승인 또는 규정 준수 요구 사항이 있는 시나리오에 대한 솔루션을 제공하기 위해 클라우드 기반 아키텍처를 배포하는 지침 문서와 자동화 템플릿으로 구성되어 있습니다.

## <a name="guidance-for-it-administrators"></a>IT 관리자를 위한 지침

전역 관리자, 보안 관리자 또는 조건부 액세스 관리자 권한으로 [Azure Portal](https://portal.azure.com/) 에 로그인 합니다. [Azure Active Directory에서 관리자 역할 사용 권한을 참조 하세요.](../users-groups-roles/directory-assign-admin-roles.md)

IT 관리자는 azure [AD 조건부 액세스](overview.md) 를 사용 하 여 사용자가 azure Multi-Factor Authentication를 사용 하 여 인증 하거나 신뢰할 수 있는 네트워크 또는 신뢰할 수 있는 장치에서 로그인 하도록 요구할 수 있습니다.

시작 하는 데 도움이 되는 유용한 링크는 다음과 같습니다.

* [Azure Active Directory의 조건부 액세스에 대 한 모범 사례](best-practices.md)
* [Azure AD 액세스 검토를 사용 하 여 조건부 액세스 정책에서 제외 된 사용자 관리](../governance/conditional-access-exclusion.md)
* [방법: Azure Active Directory에서 조건부 액세스 배포 계획](plan-conditional-access.md)
* [빠른 시작: 조건부 액세스 Azure Active Directory 있는 특정 앱에 대 한 MFA 요구](app-based-mfa.md)
* [빠른 시작: 클라우드 앱에 액세스 하기 전에 동의 해야 하는 사용 약관](require-tou.md)
* [빠른 시작: Azure Active Directory 조건부 액세스를 사용 하 여 세션 위험이 감지 되 면 액세스 차단](app-sign-in-risk.md)
* [Azure AD 조건부 액세스 Faq](faqs.md)
   * 추가 질문이 있는 경우 [MSDN 포럼](https://social.msdn.microsoft.com/Forums/home?forum=WindowsAzureAD&sort=relevancedesc&brandIgnore=True&searchTerm=password+reset+azure)을 볼 수도 있습니다.
   * 문제에 대 한 답을 찾을 수 없는 경우 지원 팀에 문의 하 여 더 많은 도움을 받을 수 있습니다. [Microsoft 지원에 문의](../authentication/active-directory-passwords-troubleshoot.md#contact-microsoft-support)를 사용 합니다.

### <a name="tutorials"></a>자습서

* [**빠른 시작: 조건부 액세스 Azure Active Directory 있는 특정 앱에 대 한 MFA 요구**](app-based-mfa.md)
   * 이 빠른 시작에서는 사용자 환경에서 선택한 클라우드 앱에 대 한 multi-factor authentication을 요구 하는 Azure AD 조건부 액세스 정책을 구성 하는 방법을 보여 줍니다.

* [**빠른 시작: 클라우드 앱에 액세스 하기 전에 동의 해야 하는 사용 약관**](require-tou.md)
   * 이 빠른 시작에서는 사용자 환경에서 선택한 클라우드 앱에 대해 토우토 be를 수락 해야 하는 Azure AD 조건부 액세스 정책을 구성 하는 방법을 보여 줍니다.

* [**빠른 시작: Azure Active Directory 조건부 액세스를 사용 하 여 세션 위험이 감지 되 면 액세스 차단**](app-sign-in-risk.md)
   * 이 빠른 시작에서는 구성 된 로그인 위험 수준이 검색 될 때 로그인을 차단 하는 조건부 액세스 정책을 구성 하는 방법을 보여 줍니다.

* [자습서: **Azure Portal에서 multi-factor authentication이 필요한 클래식 정책 마이그레이션**](policy-migration-mfa.md)
   * 이 자습서에서는 클라우드 앱에 대해 MFA (multi-factor authentication)가 필요한 클래식 정책을 마이그레이션하는 방법을 보여 줍니다.

## <a name="end-user-readiness-and-communication"></a>최종 사용자 준비 및 통신

조건부 액세스는 최종 사용자 환경에 영향을 줄 수 있는 다른 Azure AD 기능을 사용 합니다. 예를 들어 Azure Multi-factor authentication을 사용 하 여 사용자에 대해 강력한 인증을 사용 하도록 설정할 수 있습니다. 이 경우 Azure MFA의 최종 사용자 템플릿을 사용 합니다.

## <a name="next-steps"></a>다음 단계

* [조건부 액세스 배포 계획 설명서](plan-conditional-access.md)를 사용 하 여 배포를 시작 합니다.
