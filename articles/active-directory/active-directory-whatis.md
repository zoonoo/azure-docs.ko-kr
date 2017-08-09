---
title: "Azure Active Directory란?"
description: "Azure Active Directory를 사용하여 기존 온-프레미스 ID를 클라우드로 확장하거나 Azure AD 통합 응용 프로그램을 개발할 수 있습니다."
services: active-directory
documentationcenter: 
author: jeffgilb
manager: femila
ms.reviewer: jsnow
ms.author: jeffgilb
ms.assetid: 498820c4-9ebe-42be-bda2-ecf38cc514ca
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.custom: it-pro
ms.translationtype: HT
ms.sourcegitcommit: 349fe8129b0f98b3ed43da5114b9d8882989c3b2
ms.openlocfilehash: b6746afd508832afbd54153851b6f2ae404af147
ms.contentlocale: ko-kr
ms.lasthandoff: 07/26/2017

---
# <a name="what-is-azure-active-directory"></a>Azure Active Directory란?
Azure AD(Azure Active Directory)는 Microsoft의 다중 테넌트 클라우드 기반 디렉터리 및 ID 관리 서비스입니다. Azure AD는 핵심 디렉터리 서비스, 고급 ID 통제 및 응용 프로그램 액세스 관리 기능을 모두 제공합니다. 또한 Azure AD는 개발자가 중앙 집중식 정책 및 규칙에 따라 응용 프로그램에 대한 액세스 제어를 제공하도록 지원하는 다양한 기능의 표준 기반 플랫폼을 제공합니다. 

Azure AD는 IT 관리자를 위해 직원 및 비즈니스 파트너에게 Office365, Salesforce.com, DropBox, Concur와 같은 [수천 개의 SaaS 응용 프로그램 클라우드](active-directory-saas-tutorial-list.md) 에 대한 SSO(Single Sign-On) 액세스 권한을 부여하는 사용하기 편리한 솔루션을 제공합니다.

응용 프로그램 개발자는 Azure AD를 사용하여, 전 세계 수백 만 조직에서 사용하는 ID 관리 솔루션과 빠르고 쉽게 통합함으로써, 응용 프로그램을 구축하는 데 집중할 수 있습니다.

Azure AD에는 다단계 인증, 장치 등록, 셀프 서비스 암호 관리, 셀프서비스 그룹 관리, 권한 있는 계정 관리, 역할 기반 액세스 제어, 응용 프로그램 사용 모니터링, 광범위한 감사 및 보안 모니터링 및 경고를 포함하는 완전한 ID 관리 기능이 포함되어 있습니다. 이러한 기능을 통해 클라우드 기반 응용 프로그램을 보호하고, IT 프로세스를 간소화하고, 비용을 절감하고, 회사의 규정 준수 목표를 충족할 수 있도록 합니다.

또한 [네 번 클릭](./connect/active-directory-aadconnect-get-started-express.md)만으로 Azure AD를 기존 Windows Server Active Directory와 통합하여 기존에 투자된 온-프레미스 ID를 활용할 수 있고, 클라우드를 기반으로 하는 SaaS 응용 프로그램에 대한 액세스를 관리하는 기능을 조직에 제공할 수 있습니다.

Office 365, Azure 또는 Dynamics CRM Online 고객인 경우 이미 Azure AD를 사용하고 있다는 것을 알지 못할 수도 있습니다. 모든 Office 365, Azure 및 Dynamics CRM 테넌트는 실제로 이미 Azure AD 테넌트입니다. 따라서 언제든 해당 Azure AD 테넌트를 사용하여 Azure AD와 수천개의 클라우드 응용 프로그램을 통합할 수 있으며 이에 대한 액세스를 관리할 수 있습니다.

![Azure AD Connect 스택](./media/active-directory-whatis/Azure_Active_Directory.png)

## <a name="how-reliable-is-azure-ad"></a>Azure AD가 안정적입니까?
Azure AD의 다중 테넌트, 지리적으로 분산된 고가용성 디자인은, 중요한 비즈니스 요구사항이 있을 때 Azure AD를 신뢰할 수 있음을 의미합니다. 자동 장애 조치로 전세계 34개 데이터 센터를 운영중이므로 Azure AD는 안전성이 뛰어납니다. 또한 데이터 센터의 작동이 중단되더라도 디렉터리 데이터 복사본이 두 개 이상의 지역에 있는 데이터 센터에 분산되어 있고 즉시 액세스할 수 있다는 점을 미루어 볼 때 안전성을 신뢰할 수 있습니다.

자세한 내용은 [서비스 수준 계약](https://azure.microsoft.com/support/legal/sla/)을 참조하세요.

## <a name="choose-an-edition"></a>버전 선택
모든 Microsoft Online 비즈니스 서비스는 로그온 및 기타 ID가 필요로 할 때 Azure Active Directory(Azure AD)를 사용합니다. Microsoft Online 비즈니스 서비스 중 하나를 구독하는 경우(예: Office 365 또는 Microsoft Azure) 무료 기능 모두에 액세스할 수 있는 Azure AD를 가져옵니다. Azure Active Directory Free Edition를 사용하면 사용자 및 그룹을 관리하고, 온-프레미스 디렉터리와 동기화하고, Azure, Office 365 및 인기 많은 수천 가지의 SaaS 응용 프로그램(예: Salesforce, Workday, Concur, DocuSign, Google Apps, Box, ServiceNow, Dropbox 등)에서 SSO(Single Sign-On)를 수행할 수 있습니다. 

Azure Active Directory를 강화하려면 Azure Active Directory Basic, Premium P1 및 Premium P2 Edition을 사용하여 유료 기능을 추가할 수 있습니다. Azure Active Directory 유료 버전은 기존 무료 디렉터리 위에 구축되어 모바일 작업자를 위한 셀프 서비스를 확장하는 엔터프라이즈 클래스 기능, 향상 된 모니터링, 보안 보고, Multi-Factor Authentication(MFA) 및 보안 액세스를 제공합니다.

> [!NOTE]
> 이러한 버전의 가격 옵션은 [Azure Active Directory 가격 책정](https://azure.microsoft.com/pricing/details/active-directory/)을 참조하세요. Azure Active Directory Premium P1, Premium P2 및 Azure Active Directory Basic은 중국에서 현재 지원되지 않습니다. 자세한 내용은 Azure Active Directory 포럼에 문의하세요.
>

* **Azure Active Directory Basic** - 클라우드를 주로 필요로 하는 작업자를 위해 설계된 이 버전은 클라우드 중심 응용 프로그램 액세스 및 셀프 서비스 ID 관리 솔루션을 제공합니다. Azure Active Directory Basic 버전을 사용하면 그룹 기반 액세스 관리, 클라우드 응용 프로그램에 대한 셀프 서비스 암호 재설정 및 Azure Active Directory 응용 프로그램 프록시(Azure Active Directory를 사용하여 온-프레미스 웹 응용 프로그램을 게시하는 데 필요)와 같은 생산성 강화 및 비용 절감 기능을 활용할 수 있고 이는 모두 엔터프라이즈급의 99.9% 가동 시간 SLA에서 지원됩니다.
* **Azure Active Directory Premium P1** - ID 및 액세스 관리를 필요로 하는 조직을 지원하도록 설계된 Azure Active Directory Premium Edition은 풍부한 기능의 엔터프라이즈 수준 ID 관리 기능을 추가하고 하이브리드 사용자가 원활하게 온-프레미스 및 클라우드 기능에 액세스할 수 있도록 합니다. 이 버전은 하이브리드 환경에서 응용 프로그램 액세스, 셀프 서비스 ID 및 액세스 관리(IAM), 클라우드에서 ID 보호 및 보안에 대해 정보 작업자 및 ID 관리자를 위해 필요한 모든 것을 포함합니다. 동적 그룹 및 셀프 서비스 그룹 관리와 같은 고급 관리 및 위임 리소스를 지원합니다. 온-프레미스 사용자를 위한 셀프 서비스 암호 재설정과 같은 솔루션을 사용하여 Microsoft ID 관리자(온-프레미스 ID 및 액세스 관리 제품군)를 포함하며 클라우드 쓰기 저장 기능을 제공합니다.
* **Azure Active Directory Premium P2** - 모든 사용자와 관리자에 대한 고급 보호로 설계되었으며 이 새 제품은 Azure AD Premium P1의 모든 기능과 새로운 ID 보호 및 Privileged Identity Management를 포함합니다. Azure Active Directory ID 보호는 수많은 신호를 활용하여 응용 프로그램 및 중요한 회사 데이터에 위험 기반 조건부 액세스를 제공합니다. 관리자 및 관리자의 리소스에 대한 액세스를 검색, 제한 및 모니터링할 수 있도록 Azure Active Directory Privileged Identity Management를 사용하여 권한 있는 계정을 관리 및 보호할 수 있도록 도움을 주고 필요한 경우 적시에 액세스를 제공합니다.  

> [!NOTE]
> 다양한 Azure Active Directory 기능은 "종량제" 버전을 통해 사용할 수 있습니다.
>
> * Active Directory B2C는 소비자 지향 응용 프로그램에 대한 ID 및 액세스 관리 솔루션입니다. 자세한 내용은 [Azure Active Directory B2C](https://azure.microsoft.com/documentation/services/active-directory-b2c/)
> * Azure Multi-Factor Authentication는 사용자 또는 인증 공급자 단위를 통해 사용될 수 있습니다. 자세한 내용은 [Azure Multi-Factor Authentication 정의](../multi-factor-authentication/multi-factor-authentication.md)
>

## <a name="how-can-i-get-started"></a>어떻게 시작하나요?

**IT 관리자인 경우:**

* [사용해 보세요!](https://azure.microsoft.com/trial/get-started-active-directory/) - 지금 무료 30일 평가판에 등록하면 이 링크를 사용하여 5분 내에 첫 번째 클라우드 솔루션을 배포할 수 있습니다.

* Azure AD 테넌트를 시작하고 빠르게 실행하는 팁과 요령은 [Azure AD 시작](https://docs.microsoft.com/azure/active-directory/active-directory-get-started-premium)을 참조하세요.

**개발자인 경우:**
 
* Azure Active Directory에 대한 [개발자 가이드](active-directory-developers-guide.md) 를 확인하세요.

* [평가판 시작](https://azure.microsoft.com/trial/get-started-active-directory/) – 지금 무료 30일 평가판에 등록하고 Azure AD와 앱을 통합하기 시작합니다.

## <a name="next-steps"></a>다음 단계
[Azure ID 및 액세스 관리의 기본 사항에 대해 자세히 알아보기](https://docs.microsoft.com/azure/active-directory/identity-fundamentals)

