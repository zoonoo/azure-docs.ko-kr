---
title: Azure AD(Azure Active Directory)란? | Microsoft Docs
description: Azure Active Directory를 사용하여 기존 온-프레미스 ID를 클라우드로 확장하거나 Azure AD 통합 앱을 개발하는 방법에 대해 알아봅니다.
services: active-directory
author: eross-msft
manager: mtillman
ms.author: lizross
ms.assetid: 498820c4-9ebe-42be-bda2-ecf38cc514ca
ms.service: active-directory
ms.component: fundamentals
ms.workload: identity
ms.topic: overview
ms.date: 09/13/2018
ms.custom: it-pro
ms.openlocfilehash: 755c301651e7c49faa8b05b2b443c5cce1a03c90
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/19/2018
ms.locfileid: "46364057"
---
# <a name="what-is-azure-active-directory"></a>Azure Active Directory란?
Azure AD(Azure Active Directory)는 Microsoft의 다중 테넌트 클라우드 기반 디렉터리 및 ID 관리 서비스입니다. Azure AD는 핵심 디렉터리 서비스, 응용 프로그램 액세스 관리 및 ID 보호를 단일 솔루션에 결합하여 개발자가 중앙 집중식 정책 및 규칙에 따라 앱의 액세스를 제어할 수 있도록 지원하는 표준 기반 플랫폼을 제공합니다.

![Azure AD Connect 스택](./media/active-directory-whatis/Azure_Active_Directory.png)

## <a name="benefits-of-azure-ad"></a>Azure AD의 이점
Azure AD를 사용하면 다음과 같은 이점이 제공됩니다.

-   엔터프라이즈 전체에서 각 사용자에 대한 단일 ID를 만들고 관리하여 사용자, 그룹 및 장치를 [Azure AD Connect](../connect/active-directory-aadconnect.md)와 동기화된 상태로 유지합니다.

-   사전 통합된 수천 개의 SaaS 앱을 비롯하여 앱에 대한 SSO(Single sign-on) 액세스를 제공하며 [Azure AD 응용 프로그램 프록시](../manage-apps/application-proxy.md)를 사용하여 온-프레미스 SaaS 응용 프로그램에 더욱 안전한 원격 액세스를 제공합니다.

-   온-프레미스 및 클라우드 앱 모두에 대해 규칙 기반 [Multi-Factor Authentication](../authentication/concept-mfa-howitworks.md) 정책을 적용하여 응용 프로그램 액세스 보안을 가능하게 합니다.

-   [셀프 서비스 암호 재설정](../user-help/user-help-reset-password.md) 및 [MyApps 포털](../user-help/active-directory-saas-access-panel-introduction.md)을 사용한 그룹 및 응용 프로그램 액세스 요청으로 사용자 생산성을 개선합니다.

-   전 세계 엔터프라이즈급, 클라우드 기반 ID 및 액세스 관리 솔루션의 [고가용성 및 안정성](https://docs.microsoft.com/azure/architecture/checklist/availability)을 활용합니다.

## <a name="who-uses-azure-ad"></a>Azure AD를 사용하는 사용자
Azure AD는 IT 관리자, 앱 개발자 및 Office 365, Azure 또는 Dynamics CRM Online 사용자용으로 만들어졌습니다.

- **IT 관리자.** Azure AD는 수천 개의 [클라우드 기반 SaaS 앱](../saas-apps/tutorial-list.md) 및 온-프레미스 앱에 대한 보다 강력한 ID 관리 및 SSO(Single Sign On) 액세스를 사용하여 조직에 보다 안전한 솔루션을 제공합니다. 이러한 앱을 통해 사용자에 대한 클라우드 기반 앱 보안, 원활한 액세스, 향상된 공동 작업 및 ID 수명 주기의 자동화가 구현되어 보안 및 규정 준수를 높이는 데 도움이 될 것입니다.

    또한 [Azure AD Connect](../connect/active-directory-aadconnect-get-started-express.md)로 Azure AD를 기존 Windows Server Active Directory와 통합하여 조직에서 기존에 투자된 온-프레미스 ID를 통해 클라우드 기반 SaaS 앱 액세스를 관리할 수 있습니다.

- **앱 개발자.** Azure AD를 사용하면 전 세계 수백만 조직에서 사용하는 ID 관리 솔루션과의 통합을 제공하여 앱 빌드에 주력하는 데 도움이 됩니다.

- **Office 365, Azure 또는 Dynamics CRM Online 고객.** 여러분은 Azure AD를 이미 사용하고 있습니다. 각 Office 365, Azure 및 Dynamics CRM Online 테넌트는 실제로 Azure AD 테넌트이며, 통합된 클라우드 앱에 대한 사용자 액세스를 즉시 관리할 수 있습니다.

## <a name="how-reliable-is-azure-ad"></a>Azure AD는 얼마나 안정적이나요?
Azure AD의 다중 테넌트 방식의 지리적으로 분산된 고가용성 디자인은, 중요한 비즈니스 요구사항이 있을 때 Azure AD를 신뢰할 수 있음을 의미합니다. Azure AD는 자동화된 장애 조치(failover)를 사용하여 전 세계 28개의 데이터 센터에서 실행됩니다. 즉, 한 데이터 센터의 가동이 중지되더라도 디렉터리 데이터의 사본이 지역적으로 분산된 둘 이상의 데이터 센터에 있어서 즉시 사용할 수 있습니다.

서비스 수준 계약에 대한 자세한 내용은 [서비스 수준 계약](https://azure.microsoft.com/support/legal/sla/)을 참조하세요.

## <a name="choose-an-edition"></a>버전 선택
모든 Microsoft Online 비즈니스 서비스는 로그인 및 기타 ID가 필요할 때 Azure AD를 사용합니다. Microsoft Online 비즈니스 서비스를 구독하는 경우(예: Office 365 또는 Microsoft Azure) 무료 기능 모두에 액세스할 수 있는 Azure AD를 자동으로 가져옵니다. Azure Active Directory Free 버전을 사용하면 사용자 및 그룹을 관리하고, 온-프레미스 디렉터리와 동기화하고, Azure, Office 365 및 인기 많은 수천 가지의 SaaS 앱(예: Salesforce, Workday, Concur, DocuSign, Google Apps, Box, ServiceNow, Dropbox 등)에서 SSO(Single Sign-On)를 수행할 수 있습니다. 

Azure AD 구현을 향상시키기 위해 Azure Active Directory Basic, Premium P1 또는 Premium P2 Edition으로 업그레이드하여 유료 기능을 추가할 수도 있습니다. Azure AD 유료 버전은 기존 무료 디렉터리 위에 빌드되어 모바일 인력에게 셀프 서비스를 포괄하는 엔터프라이즈 클래스 기능, 향상된 모니터링, 보안 보고, MFA(Multi-Factor Authentication), 보안 액세스 등을 제공합니다.

> [!NOTE]
> 이러한 버전의 가격 옵션은 [Azure Active Directory 가격 책정](https://azure.microsoft.com/pricing/details/active-directory/)을 참조하세요. Azure Active Directory Premium P1, Premium P2 및 Azure Active Directory Basic은 중국에서 현재 지원되지 않습니다. Azure AD 가격에 대한 자세한 내용은 Azure Active Directory 포럼에 문의할 수 있습니다.

- **Azure Active Directory Basic.** 클라우드 우선 작업 작업자를 위해 설계된 이 버전에서는 클라우드 중심 응용 프로그램 액세스 및 셀프 서비스 ID 관리 솔루션을 제공합니다. Basic 버전을 사용하면 그룹 기반 액세스 관리, 클라우드 앱의 셀프 서비스 암호 재설정, Azure Active Directory 응용 프로그램 프록시(Azure AD를 사용하여 온-프레미스 웹앱을 게시하는 데 필요) 등의 생산성 강화 및 비용 절감 기능을 활용할 수 있습니다. 이 모든 기능은 99.9% 작동 시간 엔터프라이즈 SLA로 지원됩니다.

- **Azure Active Directory Premium P1.** ID 및 액세스 관리에 대한 요구 사항이 많은 조직에게 권한을 부여하도록 설계된 Azure Active Directory Premium 버전은 풍부한 기능을 갖춘 엔터프라이즈급 ID 관리 기능을 추가하며 하이브리드 사용자가 원활하게 온-프레미스 및 클라우드 기능에 액세스할 수 있도록 합니다. 이 버전은 하이브리드 환경에서 응용 프로그램 액세스, 셀프 서비스 ID 및 액세스 관리(IAM), 클라우드에서 ID 보호 및 보안에 대해 정보 작업자 및 ID 관리자를 위해 필요한 모든 것을 포함합니다. 동적 그룹 및 셀프 서비스 그룹 관리와 같은 고급 관리 및 위임 리소스를 지원합니다. 온-프레미스 사용자를 위한 셀프 서비스 암호 재설정과 같은 솔루션을 사용하여 Microsoft ID 관리자(온-프레미스 ID 및 액세스 관리 제품군)를 포함하며 클라우드 쓰기 저장 기능을 제공합니다.

- **Azure Active Directory Premium P2.** 사용자와 관리자에 대한 고급 보호를 중심으로 설계되었으며 이 새 제품은 Azure AD Premium P1의 모든 기능과 ID 보호 및 Privileged Identity Management를 포함합니다. Azure Active Directory Identity Protection은 수많은 신호를 활용하여 앱 및 중요한 회사 데이터에 위험 기반 조건부 액세스를 제공합니다. 관리자 및 관리자의 리소스에 대한 액세스를 검색, 제한 및 모니터링할 수 있도록 Azure Active Directory Privileged Identity Management를 사용하여 권한 있는 계정을 관리 및 보호할 수 있도록 도움을 주고 필요한 경우 적시에 액세스를 제공합니다.  

> [!NOTE]
> 다양한 Azure Active Directory 기능은 “종량제” 버전을 통해 사용할 수도 있습니다.<ul><li>**Azure Active Directory B2C.** 소비자 지향 앱에 대한 ID 및 액세스 관리 솔루션입니다. 자세한 내용은 [Azure Active Directory B2C](https://azure.microsoft.com/documentation/services/active-directory-b2c/)를 참조하세요.</li><li>**Azure Multi-Factor Authentication.** 사용자별 또는 인증 공급자별로 사용됩니다. 자세한 내용은 [Azure Multi-Factor Authentication이란?](../authentication/multi-factor-authentication.md)을 참조하세요.

## <a name="as-an-admin-how-do-i-get-started"></a>관리자의 경우 어떻게 시작하나요?
30일 평가판을 등록하고 첫 번째 클라우드 솔루션을 배포하세요. [Azure Active Directory Premium 평가판](https://azure.microsoft.com/trial/get-started-active-directory/)을 참조합니다.

## <a name="as-a-developer-how-do-i-get-started"></a>개발자의 경우 어떻게 시작하나요?
30일 평가판을 등록하고 Azure AD와 앱의 통합을 시작하세요. [Azure Active Directory Premium 평가판](https://azure.microsoft.com/trial/get-started-active-directory/)을 참조합니다. 자세한 내용은 Azure Active Directory에 대한 [개발자 가이드](../develop/azure-ad-developers-guide.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계
- [Azure ID 및 액세스 관리의 기본 사항에 대해 자세히 알아보기](identity-fundamentals.md).

- [Azure AD를 Windows Server Active Directory와 통합](../hybrid/how-to-connect-install-express.md).