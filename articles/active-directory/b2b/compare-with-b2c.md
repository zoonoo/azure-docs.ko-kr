---
title: B2B 공동 작업과 B2C 비교 - Azure Active Directory | Microsoft Docs
description: Azure Active Directory B2B 공동 작업과 Azure AD B2C 간의 차이는 무엇인가요?
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: overview
ms.date: 01/30/2019
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: sasubram
ms.collection: M365-identity-device-management
ms.openlocfilehash: ba06952b4d01e06d7925f70ee4bc26407b48e130
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/22/2019
ms.locfileid: "56670592"
---
# <a name="compare-b2b-collaboration-and-b2c-in-azure-active-directory"></a>Azure Active Directory에서 B2B 공동 작업과 B2C 비교

Azure Active Directory(Azure AD) B2B 공동 작업 및 Azure AD B2C 모두를 사용하여 Azure AD에서 외부 사용자와 함께 작업할 수 있습니다. 하지만 어떻게 비교할 수 있나요?

**Azure AD B2B**는 공동 작업할 수 있도록 외부 사용자와 파일 및 리소스를 안전하게 공유하려는 비즈니스에 유용합니다. Azure 관리자는 Azure Portal에서 B2B를 설정하고 Azure AD가 비즈니스와 외부 파트너 간에 페더레이션을 관리합니다. 사용자는 자신의 회사 또는 학교 계정이나 메일 계정으로 간단한 초대 및 사용 프로세스를 통해 공유 리소스에 로그인합니다.
 
**Azure AD B2C**는 주로 고객 관련 앱을 만드는 비즈니스 및 개발자에 유용합니다. Azure AD B2C에서 개발자는 해당 애플리케이션의 전체 기능을 갖춘 ID 시스템으로 Azure AD를 사용할 수 있고 고객이 이미 설정한 ID(예: Facebook 또는 Gmail)를 사용하여 로그인하도록 합니다.

아래 표에서는 자세한 비교를 제공합니다.


B2B 공동 작업 기능 |     Azure AD B2C 독립 실행형 제품
-------- | --------
대상: ID 공급자에 관계 없이 파트너 조직의 사용자를 인증할 수 있기를 원하는 조직 | 대상: 개인이든, 기관이든, 조직이든 상관 없이 모바일 및 웹앱 고객을 Azure AD에 초대하려는 사용자
지원되는 ID: 회사 또는 학교 계정이 있는 직원, 회사 또는 학교 계정이 있는 파트너 또는 모든 이메일 주소입니다. 직접 페더레이션 기능을 곧 지원합니다.  | 지원되는 ID: 로컬 애플리케이션 계정(모든 이메일 주소 또는 사용자 이름)이 있는 소비자 사용자 또는 직접 페더레이션이 포함된 지원되는 소셜 ID입니다.
외부 사용자는 직원과 같은 디렉터리에서 관리되지만 특별 주석이 추가됩니다. 이들은 직원과 같은 방식으로 관리하고 동일한 그룹에 추가할 수 있습니다.  | 외부 사용자는 애플리케이션 디렉터리에서 관리됩니다. 이들은 조직의 직원 및 파트너 디렉터리(있는 경우)와 별도로 관리됩니다.
모든 Azure AD 연결 앱에 대한 SSO(Single Sign-On)가 지원됩니다. 예를 들어 Office 365 또는 온-프레미스 앱 및 다른 SaaS 앱(예: Salesforce 또는 Workday)에 대한 액세스를 제공할 수 있습니다.  |  Azure AD B2C 테넌트 내의 고객 소유 앱에 대한 SSO가 지원됩니다. Office 365 또는 다른 Microsoft 앱 및 타사 SaaS 앱에 대한 SSO가 지원되지 않습니다.
파트너 수명 주기: 호스트/초대한 조직에 의해 관리됩니다.  | 고객 수명 주기: 셀프 서비스 또는 애플리케이션에 의해 관리됩니다.
보안 정책 및 규정 준수: 호스트/초대한 조직에 의해 관리됩니다(예: [조건부 액세스 정책](https://docs.microsoft.com/azure/active-directory/b2b/conditional-access) 사용).  | 보안 정책 및 규정 준수: 애플리케이션에 의해 관리됩니다.
브랜딩: 호스트/초대한 조직의 브랜드가 사용됩니다.  |    브랜딩: 애플리케이션에 의해 관리됩니다. 일반적으로 조직의 존재가 희미해지고 제품 브랜드가 되는 경향이 있습니다.
추가 정보: [블로그 게시물](https://blogs.technet.microsoft.com/enterprisemobility/2017/02/01/azure-ad-b2b-new-updates-make-cross-business-collab-easy/), [설명서](what-is-b2b.md)  | 추가 정보: [제품 페이지](https://azure.microsoft.com/services/active-directory-b2c/), [설명서](https://docs.microsoft.com/azure/active-directory-b2c/)


### <a name="next-steps"></a>다음 단계

- [Azure AD B2B 공동 작업이란?](what-is-b2b.md)
- [B2B 공동 작업 사용자 속성](user-properties.md)

