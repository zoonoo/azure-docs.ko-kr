---
title: Azure Marketplace SaaS 응용 프로그램 기술 게시 가이드
description: Azure Marketplace에 SaaS 응용 프로그램을 게시하는 단계별 가이드 및 게시 검사 목록
services: Marketplace, Compute, Storage, Networking, Blockchain, Security, SaaS
documentationcenter: ''
author: BrentL-Collabera
manager: ''
editor: BrentL-Collabera
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 02/28/2018
ms.author: pabutler
ms.openlocfilehash: eb6db45ca0fcb6879aeaeaaf70715691cac438b0
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="saas-applications-technical-publishing-guide"></a>SaaS 응용 프로그램 기술 게시 가이드

Azure Marketplace SaaS 응용 프로그램 기술 게시 가이드를 시작합니다. 이 가이드는 향후 게시자 및 기존 게시자가 SaaS 응용 프로그램 제품을 사용하여 Azure Marketplace에 응용 프로그램과 서비스를 나열하는 데 도움을 주기 위해 작성되었습니다.  
자신의 Azure 구독에 솔루션을 배포하고, 응용 프로그램을 테스트하기 위해 디자인하고 관리하는 인터페이스를 통해 고객이 로그인하는 경우 SaaS 응용 프로그램 제품을 사용할 수 있습니다. 이러한 작업은 [Azure AD(Azure Active Directory)](https://docs.microsoft.com/azure/active-directory/active-directory-whatis)를 사용하고 기존 평가판 환경을 활용하여 수행됩니다. 다시 말해, 고객이 주도하고 파트너가 호스트하는 평가판입니다. 클라우드 구매자가 무료 또는 유료 솔루션을 독립적으로 경험할 수 있는 방식으로 솔루션을 공개하는 것이 중요하기 때문에 이러한 제품 유형은 고객이 클라우드 솔루션을 검색하는 방식과 일치하는 평가판 환경을 제공합니다.  

다른 모든 Marketplace 제품에 대한 개요는 [Marketplace 게시자 가이드](https://aka.ms/sellerguide)를 참조하세요.

## <a name="saas-application-technical-guidance"></a>SaaS 응용 프로그램 기술 지침
SaaS 응용 프로그램에 대한 기술 요구 사항은 간단합니다. 게시할 Azure AD와 게시자가 통합되기만 하면 됩니다.  Azure AD와 응용 프로그램의 통합은 문서화가 잘 되어있으며 이를 위해 Microsoft는 다수의 SDK와 리소스가 제공됩니다.  

시작하려면 다른 이니셔티브와 작업을 격리할 수 있도록 Azure Marketplace 게시 전용 구독을 마련하는 것이 좋습니다. 또한 개발 환경의 일부로 다음 도구가 아직 설치되지 않은 경우 설치하는 것이 좋습니다. 
- [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)  
- [Azure Powershell](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-5.0.0)  
- [Azure 개발자 도구(사용 가능한 항목 검토)](https://azure.microsoft.com/tools/)  
- [Visual Studio Code](https://code.visualstudio.com/)  

### <a name="resources"></a>리소스
다음 목록에는 시작하기에 가장 좋은 Azure AD 리소스에 대한 링크가 있습니다. 

**설명서**

- [Azure Active Directory 개발자 가이드](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)

- [Azure Active Directory와 통합](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-to-integrate)

- [Azure Active Directory와 응용 프로그램 통합](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)

- [Azure 로드맵 - 보안 및 ID](https://azure.microsoft.com/roadmap/?category=security-identity)

**비디오**

- [Vittorio Bertocci가 소개하는 Azure Active Directory 인증](https://channel9.msdn.com/Shows/XamarinShow/Episode-27-Azure-Active-Directory-Authentication-with-Vittorio-Bertocci?term=azure%20active%20directory%20integration)

- [Azure Active Directory ID 기술 브리핑 - 1/2부](https://channel9.msdn.com/Blogs/MVP-Enterprise-Mobility/Azure-Active-Directory-Identity-Technical-Briefing-Part-1-of-2?term=azure%20active%20directory%20integration)

- [Azure Active Directory ID 기술 브리핑 - 2/2부](https://channel9.msdn.com/Blogs/MVP-Azure/Azure-Active-Directory-Identity-Technical-Briefing-Part-2-of-2?term=azure%20active%20directory%20integration)

- [Microsoft Azure Active Directory로 앱 구축](https://channel9.msdn.com/Blogs/Windows-Development-for-the-Enterprise/Building-Apps-with-Microsoft-Azure-Active-Directory?term=azure%20active%20directory%20integration)

- [Active Directory에 중점을 둔 Microsoft Azure 비디오](https://azure.microsoft.com/resources/videos/index/?services=active-directory)

**학습**  
- [IT 전문가용 Microsoft Azure 콘텐츠 시리즈: Azure Active Directory](https://mva.microsoft.com/en-US/training-courses/microsoft-azure-for-it-pros-content-series-azure-active-directory-16754?l=N0e23wtxC_2106218965)

**Azure Active Directory 서비스 업데이트**  
- [Azure AD 서비스 업데이트](https://azure.microsoft.com/updates/?product=active-directory)

지원이 필요하면 다음 리소스를 사용하세요.
- [MSDN 포럼](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD)
- [StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)

## <a name="the-azure-active-directory-gallery"></a>Azure Active Directory 갤러리
Azure Marketplace/AppSource에 응용 프로그램을 나열할 수 있고 Azure Marketplace AppStore의 일부인 Azure AD 응용 프로그램 갤러리에도 응용 프로그램을 나열할 수 있습니다. Azure AD를 ID 공급자로 사용하는 고객은 여기에서 게시된 다른 SaaS 응용 프로그램 커넥터를 찾을 수 있습니다. IT 관리자는 앱 갤러리에서 커넥터를 추가한 다음, SSO(Single Sign-On) 및 프로비전을 위해 커넥터를 구성하고 사용할 수 있습니다. Azure AD는 SAML 2.0, OpenID Connect, OAuth 및 WS-Fed를 비롯한 SSO용 주요 페더레이션 프로토콜을 모두 지원합니다.  

응용 프로그램 통합이 Azure AD에서 작동하는지 테스트 한 후에, 응용 프로그램 네트워크 포털에서 액세스 요청을 제출합니다. Office 365 계정이 있는 경우 이를 사용하여 이 포털에 로그인합니다. Office 365 계정이 없는 경우 Microsoft 계정(예: Outlook 또는 Hotmail)을 사용하여 로그인합니다.

## <a name="program-benefits"></a>프로그램 이점
- 고객에게 최상의 Single Sign-On 환경을 제공합니다.
- 응용 프로그램 구성이 간단하고 최소화됩니다.
- 고객이 갤러리에서 응용 프로그램을 검색하여 찾을 수 있습니다.
- 어떤 Azure AD SKU(Free, Basic 또는 Premium)를 사용하든 모든 고객이 이 통합을 사용할 수 있습니다.
- 상호 고객을 위한 단계별 구성 자습서가 있습니다.
- SCIM을 사용하는 경우 동일한 앱에 대해 사용자 프로비전을 사용할 수 있습니다.

## <a name="prerequisites"></a>필수 조건
Azure AD 갤러리에 응용 프로그램을 나열하려면 먼저 Azure AD에서 지원하는 페더레이션 프로토콜 중 하나를 응용 프로그램에서 구현해야 합니다. [Microsoft Azure 법률 정보](https://azure.microsoft.com/support/legal/)에서 Azure AD 응용 프로그램 갤러리의 사용 약관을 참조하세요.  

다음은 사용 중인 프로토콜에 따른 추가 필수 구성 요소 정보를 설명합니다.

**OpenID Connect** - Azure AD에서 다중 테넌트 응용 프로그램을 만들고 응용 프로그램에 승인 프레임워크를 구현합니다. 모든 고객이 응용 프로그램에 동의를 제공할 수 있도록 공통 엔드포인트에 로그인 요청을 보냅니다. 토큰에 수신된 테넌트 ID 및 사용자의 UPN을 기반으로 고객 사용자 액세스를 제어할 수 있습니다.  
**SAML 2.0 또는 WS-Fed** - SP 또는 IdP 모드에서 SAML 또는 WS-Fed SSO 통합을 수행하는 기능이 응용 프로그램에 있어야 합니다.
