---
title: "Azure Active Directory B2B 공동 작업 미리 보기 정보 | Microsoft Docs"
description: "Azure Active Directory B2B 공동 작업은 비즈니스 파트너가 선택적으로 회사 응용 프로그램에 액세스할 수 있게 함으로써 회사 간 관계를 지원합니다."
services: active-directory
documentationcenter: 
author: sasubram
manager: femila
editor: 
tags: 
ms.assetid: 1464387b-ee8b-4c7c-94b3-2c5567224c27
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 02/18/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: 8a531f70f0d9e173d6ea9fb72b9c997f73c23244
ms.openlocfilehash: 1f6ce516629fd32b5efabce8682b45a3e575eaab
ms.lasthandoff: 03/10/2017


---

# <a name="about-the-azure-ad-b2b-collaboration-preview"></a>Azure AD B2B 공동 작업 미리 보기 정보
이 문서에서는 새로운 Azure AD(Azure Active Directory) B2B 공동 작업 공개 미리 보기의 목표, 기능 및 이점을 설명합니다.

IT 전문가와 정보 근로자는 모두 여러 Azure AD B2B 공동 작업 기능의 이점을 활용할 수 있습니다. 규모, 업계 또는 규정 준수 및 정부 요구 사항에 상관 없이 어디서든 다른 조직의 파트너와 긴밀히 작업할 수 있습니다. 내부 데이터를 완전하게 제어하면서 파트너가 문서, 리소스 및 응용 프로그램에 액세스하도록 할 수 있습니다.

개발자인 경우 Azure AD B2B API를 사용하여 조직을 안전하게 통합하는 응용 프로그램을 작성할 수 있습니다. 정보 근로자 사용자의 경우 프로세스 및 탐색은 간단합니다.

## <a name="how-b2b-collaboration-works"></a>B2B 공동 작업 작동 방식

현재 미리 보기 릴리스에서 파트너 조직과 관계를 설정하기 위해 IT 전문가 및 정보 근로자는 Azure Portal 또는 초대 관리자 API를 통해 다른 조직의 사용자를 한 번에 한 명 또는 몇 명씩 추가할 수 있습니다.

관리자는 Azure Portal(https://portal.azure.com) 및 PowerShell에서 새로운 포털 환경을 사용하여 관계를 설정할 수 있습니다.

정보 근로자는 http://myapps.microsoft.com에서 액세스 패널 환경을 사용할 수 있습니다.

개발자는 Azure AD B2B 초대 관리자 API를 통해 응용 프로그램을 만들어 B2B 공동 작업 사용자를 추가하고 초대 및 온보딩 워크플로를 사용자 지정할 수 있습니다.

B2B 공동 작업 사용자는 일반적으로 초대 및 충전 프로세스를 통해 추가됩니다. 작동 방식은 다음과 같습니다.

1. WoodGrove의 John Doe는 Gmail 주소(gsamoogle@gmail.com)를 사용하여 Sam Oogle을 사용자로 추가하려고 합니다.

2. John Doe는 WoodGrove 포털(portal.azure.com) 또는 액세스 패널(myapps.microsoft.com)로 이동한 후 로그인하고 WoodGrove 디렉터리 또는 그룹이나 응용 프로그램에 사용자 Sam Oogle을 추가합니다.

3. John Doe는 Sam Oogle에게 보낼 사용자 지정 초대 전자 메일을 지정합니다.

4. John Doe가 초대를 보내면 아래와 같이 사용자 Sam Oogle이 WoodGrove용 Azure AD에 만들어집니다.

  ![portal.azure.com의 관리자 사용자 인터페이스](media/active-directory-b2b-what-is-azure-ad-b2b/user-is-added.png)

5. 사용자를 만든 후 Azure AD는 Sam Oogle에게 초대 전자 메일을 보냅니다.

  ![Sam Oogle에게 보낸 초대 메일](media/active-directory-b2b-what-is-azure-ad-b2b/invitation-mail-sent-to-sam.png)

6. 초대에서 Sam Oogle은 **시작**을 선택합니다.  
Azure Portal이 열립니다.

7. Sam Oogle이 Azure Portal에 로그인합니다.

8. Azure AD는 다음과 같이 Sam Oogle의 토큰 정보로 Azure AD에 사용자 개체를 업데이트합니다.

  ![Azure Portal의 Sam Oogle 사용자 프로필](media/active-directory-b2b-what-is-azure-ad-b2b/user-profile-is-populated.png)

9. 이제 사용자 Sam Oogle이 초대를 상환하였고 WoodGrove 리소스에 액세스할 수 있습니다. 또한 Azure AD의 다른 모든 사용자와 같이 Sam Oogle은 관리자에 의해 관리받을 수 있습니다. 사용자 목록은 다음과 같이 표시됩니다.

  ![Azure AD 사용자 목록](media/active-directory-b2b-what-is-azure-ad-b2b/sam-now-user-in-azure-ad.png)

## <a name="public-preview-features"></a>공개 미리 보기 기능
사용자의 의견에 따라 B2B 공동 작업 공개 미리 보기 새로 고침에는 다음을 포함하는 주요 기능을 제공합니다.

* 관리자의 경우 [Azure Portal](https://portal.azure.com)의 사용자 인터페이스가 개선되었습니다. 예를 들어 관리자가 B2B 사용자를 디렉터리, 그룹 또는 응용 프로그램에 초대할 수 있습니다.  

* 정보 근로자의 경우 [액세스 패널](https://myapps.microsoft.com)에 B2B 공동 작업 셀프 서비스 초대 기능이 제공됩니다. 정보 근로자는 자신이 관리하는 셀프 서비스 그룹 또는 응용 프로그램에 B2B 공동 작업 사용자를 초대할 수 있습니다.

* 사용자는 모든 전자 메일 주소를 사용할 수 있습니다. Office 365 또는 온-프레미스 Microsoft Exchange 주소, outlook.com 주소 또는 소셜 주소(Gmail, Yahoo! 등)에 상관 없이 사용자는 Azure AD에서 만든 계정이나 Microsoft 계정으로 초대된 조직에 액세스할 수 있습니다.

* 전문적인 테넌트 브랜드 초대 전자 메일을 만듭니다.

* 초대 API를 사용하여 사용자 성향을 사용자 지정합니다.

* 초대 조직의 B2B 공동 작업 사용자에 대한 다단계 인증을 설정합니다.

* 관리자 이외의 사용자에게 초대를 위임합니다.

* B2B 공동 작업에 대한 PowerShell 지원을 제공합니다.

* 감사 및 보고 기능을 제공합니다.

## <a name="next-steps"></a>다음 단계

Azure AD B2B 공동 작업에 대한 다른 문서 찾아보기:

* [Azure Active Directory 관리자가 B2B 공동 작업 사용자를 추가하는 방법](active-directory-b2b-admin-add-users.md)
* [정보 근로자가 B2B 공동 작업 사용자를 추가하는 방법](active-directory-b2b-iw-add-users.md)
* [B2B 공동 작업 초대 전자 메일의 요소](active-directory-b2b-invitation-email.md)
* [B2B 공동 작업 초대 상환](active-directory-b2b-redemption-experience.md)
* [Azure AD B2B 공동 작업 라이선스](active-directory-b2b-licensing.md)
* [Azure Active Directory B2B 공동 작업 문제 해결](active-directory-b2b-troubleshooting.md)
* [Azure Active Directory B2B 공동 작업 자주 묻는 질문(FAQ)](active-directory-b2b-faq.md)
* [Azure Active Directory B2B 공동 작업 API 및 사용자 지정](active-directory-b2b-api.md)
* [B2B 공동 작업 사용자에 대한 다단계 인증](active-directory-b2b-mfa-instructions.md)
* [초대 없이 B2B 공동 작업 사용자 추가](active-directory-b2b-add-user-without-invite.md)
* [B2B 공동 작업 사용자 감사 및 보고](active-directory-b2b-auditing-and-reporting.md)
* [Azure Active Directory의 응용 프로그램 관리를 위한 문서 인덱스](active-directory-apps-index.md)

