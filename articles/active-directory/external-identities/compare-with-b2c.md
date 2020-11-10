---
title: 외부 ID 비교 - Azure Active Directory | Microsoft Docs
description: Azure AD 외부 ID를 사용하면 조직 외부 사용자가 자신의 ID를 사용하여 앱과 리소스에 액세스할 수 있습니다. Azure Active Directory B2B 협업 및 Azure AD B2C를 비롯한 외부 ID에 대한 솔루션을 비교합니다.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: overview
ms.date: 10/23/2020
ms.custom: project-no-code
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: 420cc2bc38d079fa95f6b90ed20cb31e994f4ea3
ms.sourcegitcommit: daab0491bbc05c43035a3693a96a451845ff193b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/29/2020
ms.locfileid: "93027077"
---
# <a name="what-are-external-identities-in-azure-active-directory"></a>Azure Active Directory의 외부 ID란?

Azure AD의 외부 ID를 사용하여 조직 외부 사용자가 앱과 리소스에 액세스하도록 허용하는 동시에 원하는 모든 ID를 사용하여 로그인하도록 할 수 있습니다. 파트너, 유통업체, 제공업체, 공급업체 및 기타 게스트 사용자는 "자신의 ID를 가져올 수 있습니다". 회사 또는 정부에서 발급한 디지털 ID 또는 비관리형 소셜 ID(예: Google 또는 Facebook)가 있는지에 관계없이 자체 자격 증명을 사용하여 로그인할 수 있습니다. 외부 사용자의 ID 공급자는 해당 ID를 관리하고, 사용자는 Azure AD를 통해 앱에 대한 액세스를 관리하여 리소스를 보호된 상태로 유지합니다.

## <a name="external-identities-scenarios"></a>외부 ID 시나리오

Azure AD 외부 ID는 조직에 대한 사용자의 관계보다는 사용자가 앱과 리소스에 로그인하려는 방법에 더 중점을 둡니다. Azure AD는 이 프레임워크 내에서 B2B(기업 간) 협업에서 소비자/고객 또는 시민 관련 애플리케이션(B2C(비즈니스-고객 간))에 대한 액세스 관리에 이르기까지 다양한 시나리오를 지원합니다.

- **외부 사용자와 앱 및 리소스를 공유합니다(B2B 협업)** . 외부 사용자를 "게스트" 사용자로 사용자 고유의 테넌트에 초대하여 권한을 할당할 수 있고(권한 부여의 경우), 해당 사용자가 기존 자격 증명을 사용하도록 허용할 수 있습니다(인증의 경우). 사용자는 자신의 회사, 학교 또는 기타 이메일 계정으로 간단한 초대 및 사용 프로세스를 통해 공유 리소스에 로그인합니다. 또한 [Azure AD 권한 관리](../governance/entitlement-management-overview.md)를 사용하여 [외부 사용자에 대한 액세스를 관리](../governance/entitlement-management-external-users.md#how-access-works-for-external-users)하는 정책을 구성할 수 있습니다. 이제 [셀프 서비스 가입 사용자 흐름(미리 보기)](self-service-sign-up-overview.md)을 사용할 수 있으므로 외부 사용자가 애플리케이션에 직접 가입하도록 허용할 수 있습니다. 회사, 학교 또는 소셜 ID(예: Google 또는 Facebook)에 가입할 수 있도록 환경을 사용자 지정할 수 있습니다. 또한 가입 프로세스 중에 사용자에 대한 정보를 수집할 수 있습니다. 자세한 내용은 [Azure AD B2B 설명서](index.yml)를 참조하세요.

- **소비자 및 고객 관련 앱(Azure AD B2C)에 대한 흰색 레이블 ID 관리 솔루션을 사용하여 사용자 경험을 빌드합니다**. 고객 관련 앱을 만드는 기업 또는 개발자는 Azure AD B2C를 사용하여 수백만 명의 소비자, 고객 또는 시민으로 확장할 수 있습니다. 개발자는 Azure AD를 애플리케이션에 대한 모든 기능을 갖춘 CIAM(Customer Identity and Access Management) 시스템으로 사용할 수 있습니다. 고객은 이미 설정한 ID(예: Facebook 또는 Gmail)를 사용하여 로그인할 수 있습니다. Azure AD B2C를 사용하여 고객이 가입하고, 로그인하고, 애플리케이션 사용 중에 자신의 프로필을 관리하는 방법을 완전히 사용자 지정하고 제어할 수 있습니다. 자세한 내용은 [Azure AD B2C 설명서](../../active-directory-b2c/index.yml)를 참조하세요.

## <a name="compare-external-identities-solutions"></a>External Identities 솔루션 비교

아래 표에서는 Azure AD 외부 ID를 사용하여 구현할 수 있는 시나리오를 자세히 비교합니다.

|   | 외부 사용자 협업(B2B) | 소비자/고객 관련 앱에 대한 액세스(B2C)  |
| ---- | --- | --- |
| **기본 시나리오** | Microsoft 애플리케이션(Microsoft 365, Teams 등) 또는 사용자 고유의 애플리케이션(SaaS 앱, 사용자 지정 개발 앱 등)을 사용하는 협업  | 최신 SaaS 또는 사용자 지정 개발 애플리케이션에 대한 ID 및 액세스 관리(자사 Microsoft 앱이 아님)   |
| **대상 사용자**    | 공급자, 파트너, 공급업체와 같은 외부 조직의 비즈니스 파트너와 협업. 사용자는 디렉터리에서 게스트 사용자로 표시됩니다. 이러한 사용자는 IT를 관리하거나 관리하지 않았을 수 있습니다.  | 제품의 고객. 이러한 사용자는 별도의 Azure AD 디렉터리에서 관리됩니다.  |
| **지원되는 ID 공급자** | 외부 사용자는 회사 계정, 학교 계정, 모든 이메일 주소, SAML 및 WS-Fed 기반 ID 공급자, Gmail 및 Facebook을 사용하여 협업할 수 있습니다.  | 로컬 애플리케이션 계정(이메일 주소 또는 사용자 이름)이 있는 소비자 사용자, 지원되는 다양한 소셜 ID, 직접 페더레이션을 통해 기업 및 정부에서 발급한 ID가 있는 사용자       |
| **외부 사용자 관리**   | 외부 사용자는 직원과 동일한 디렉터리에서 관리되지만 일반적으로 게스트 사용자라는 주석이 추가됩니다. 게스트 사용자는 직원과 동일한 방식으로 관리하고 동일한 그룹에 추가할 수 있습니다.    | 외부 사용자는 Azure AD B2C 디렉터리에서 관리됩니다. 이들은 조직의 직원 및 파트너 디렉터리(있는 경우)와 별도로 관리됩니다.  |
| **SSO(Single Sign-On)**      | 모든 Azure AD 연결 앱에 대한 SSO가 지원됩니다. 예를 들어 Microsoft 365 또는 온-프레미스 앱 및 다른 SaaS 앱(예: Salesforce 또는 Workday)에 대한 액세스를 제공할 수 있습니다.    | Azure AD B2C 테넌트 내의 고객 소유 앱에 대한 SSO가 지원됩니다. Microsoft 365 또는 다른 Microsoft SaaS 앱에 대한 SSO는 지원되지 않습니다.    |
| **보안 정책 및 규정 준수**        | 호스트/초대한 조직에 의해 관리됩니다(예: [조건부 액세스 정책](conditional-access.md) 사용). | 조직에서 조건부 액세스 및 ID 보호를 통해 관리합니다.        |
| **브랜딩**  | 호스트/초대한 조직의 브랜드가 사용됩니다.    | 브랜딩은 애플리케이션 또는 조직별로 완전히 사용자 지정할 수 있습니다.   |
| **청구 모델** | MAU(월간 활성 사용자 수)에 기반한 [외부 ID 가격 책정](https://azure.microsoft.com/en-us/pricing/details/active-directory/external-identities/) <br>(참고 항목: [B2B 설정 세부 정보](external-identities-pricing.md)) | MAU(월간 활성 사용자 수)에 기반한 [외부 ID 가격 책정](https://azure.microsoft.com/en-us/pricing/details/active-directory/external-identities/) <br>(참고 항목: [B2C 설정 세부 정보](../../active-directory-b2c/billing.md)) |
| **자세한 정보** | [블로그 게시물](https://blogs.technet.microsoft.com/enterprisemobility/2017/02/01/azure-ad-b2b-new-updates-make-cross-business-collab-easy/), [설명서](what-is-b2b.md)                   | [제품 페이지](https://azure.microsoft.com/services/active-directory-b2c/), [설명서](../../active-directory-b2c/index.yml)       |

Azure AD 외부 ID를 사용하여 조직 외부에 있는 고객과 파트너를 보호하고 관리합니다.

## <a name="about-multitenant-applications"></a>다중 테넌트 애플리케이션 정보

앱을 서비스로 제공하고 고객의 사용자 계정을 관리하지 않으려는 경우 다중 테넌트 앱을 선택하는 것이 좋습니다. 다른 Azure AD 테넌트용 애플리케이션을 개발하는 경우 단일 조직(단일 테넌트)의 사용자 또는 이미 Azure AD 테넌트가 있는 조직(다중 테넌트 애플리케이션)의 사용자를 대상으로 지정할 수 있습니다. Azure AD의 앱 등록은 기본적으로 단일 테넌트이지만 등록을 다중 테넌트로 만들 수 있습니다. 이 다중 테넌트 애플리케이션은 사용자 고유의 Azure AD에 한 번만 직접 등록됩니다. 그러나 조직의 모든 Azure AD 사용자는 사용자 부분에 대한 추가 작업 없이 애플리케이션을 사용할 수 있습니다. 자세한 내용은 [다중 테넌트 애플리케이션의 ID 관리](https://docs.microsoft.com/azure/architecture/multitenant-identity/), [방법 가이드](../develop/howto-convert-app-to-be-multi-tenant.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- [Azure AD B2B 협업이란?](what-is-b2b.md)
- [Azure AD B2C 정보](../../active-directory-b2c/overview.md)
