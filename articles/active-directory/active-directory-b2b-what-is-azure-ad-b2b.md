---
title: "Azure Active Directory B2B 공동 작업 미리 보기란? | Microsoft Docs"
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
ms.sourcegitcommit: 14028332ecc17478230d035206e8e00f6af67f7b
ms.openlocfilehash: 8b6bf676dfa0df45d59763db70655d85fa08809a
ms.lasthandoff: 02/23/2017


---

# <a name="what-is-azure-ad-b2b-collaboration-preview"></a>Azure AD B2B 공동 작업 미리 보기란?

Azure AD B2B 공동 작업 기능을 사용하여 IT 전문가 및 정보 근로자는 전 세계의 다른 조직에 있는 해당 파트너와 밀접하게 협력할 수 있습니다. 내부 데이터를 완전하게 제어하면서 문서, 리소스 및 응용 프로그램에 액세스하도록 할 수 있습니다. 개발자는 Azure AD 비즈니스 간 API를 사용하여 두 조직을 정보 근로자에게 원활하고 탐색이 용이한 안전한 방식으로 함께 불러오는 응용 프로그램을 작성할 수 있습니다.

Azure AD B2B 공동 작업 기능을 사용하여 해당 규정 준수 및 거버넌스 요구에 관계 없이, 모든 업계에 속하는 다양한 규모의 조직이 전 세계 공동 작업자와 함께 쉽고 안전하게 작업할 수 있습니다. 이것이 이러한 B2B 공동 작업 공개 미리 보기 갱신의 목표입니다.

## <a name="how-does-it-work"></a>작동 원리

현재 미리 보기 릴리스에서 조직과 관계를 설정하기 위해 IT 전문가 및 정보 근로자는 포털 또는 초대 관리자 API를 통해 다른 조직의 사용자를 한 번에 한 명씩 추가할 수 있습니다. 관리자는 Azure Portal(https://portal.azure.com) 및 PowerShell에서 새로운 포털 환경을 사용할 수 있습니다. 또한 정보 근로자는 http://myapps.microsoft.com에서 액세스 패널 환경을 사용할 수 있습니다. 개발자는 Azure AD B2B 초대 관리자 API를 통해 응용 프로그램을 만들어 B2B 공동 작업 사용자를 추가하고 초대 및 온보딩 워크플로를 사용자 지정할 수 있습니다.

B2B 공동 작업 사용자는 일반적으로 초대 + 충전 프로세스를 통해 온보드에 추가됩니다. 방법은 다음과 같습니다.

1. WoodGrove의 John Doe는 gmail 주소 gsamoogle@gmail.com을 사용하여 Sam Oogle을 추가하려고 합니다.

2. John은 WoodGrove 포털(portal.azure.com) 또는 액세스 패널(myapps.microsoft.com)로 이동한 후 로그인하고 WoodGrove 디렉터리 또는 그룹이나 응용 프로그램에 사용자를 추가합니다.

3. John은 Sam에 보낼 사용자 지정 초대 전자 메일을 지정합니다.

4. John이 작업을 끝내는 즉시 다음 사용자가 WoodGrove AD에서 생성됩니다(스크린 샷은 portal.azure.com의 관리자 UX에서 생성한 것임).

  ![사용자가 추가됩니다.](media/active-directory-b2b-what-is-azure-ad-b2b/user-is-added.png)

5. 이 사용자의 추가가 완료되는 즉시, Azure AD는 Sam에게 초대 전자 메일을 보냅니다.

  ![Sam에 보낸 초대 메일](media/active-directory-b2b-what-is-azure-ad-b2b/invitation-mail-sent-to-sam.png)

6. 이제 Sam은 **시작**을 선택하고 로그인합니다. 이때 Azure AD는 디렉터리의 사용자 개체를 토큰의 정보로 업데이트합니다(스크린 샷은 [Azure Portal](https://portal.azure.com)의 관리자 UX에서 생성한 것임).

  ![사용자 프로필이 채워집니다.](media/active-directory-b2b-what-is-azure-ad-b2b/user-profile-is-populated.png)

7. Sam의 초대가 충전되었으므로 WoodGrove 리소스에 액세스할 수 있으며 관리자는 Sam을 디렉터리의 다른 사용자처럼 관리할 수 있습니다(스크린 샷은 [Azure Portal](https://portal.azure.com)의 관리자 UX에서 생성한 것임).

  ![Sam은 이제 Azure AD의 사용자입니다.](media/active-directory-b2b-what-is-azure-ad-b2b/sam-now-user-in-azure-ad.png)

## <a name="public-preview-features"></a>공개 미리 보기 기능
공개 미리 보기에 제공되었던 B2B 공동 작업 기능을 사용하고 있으며 이 기능을 통해 유용한 피드백을 다양하게 얻었습니다. 또한 이러한 피드백을 경청해왔습니다. 모든 개선 사항은 이 공개 미리 보기 갱신 버전에 패키지할 예정입니다. 다음은 B2B 공동 작업 공개 미리 보기 갱신 버전의 주요 기능입니다.

1. 관리자 UX의 향상된 B2B 환경
  - https://portal.azure.com에 제공
  - 관리자가 B2B 사용자를 디렉터리, 그룹 또는 응용 프로그램에 초대하는 기능

2. 액세스 패널 https://myapps.microsoft.com에 제공되는 정보 근로자를 위한 B2B 공동 작업 셀프 서비스 초대 기능 정보 근로자는 자신이 관리하는 셀프 서비스 그룹 또는 응용 프로그램에 B2B 공동 작업 사용자를 초대할 수 있습니다.

3. 이제 전자 메일 주소가 있는 사용자를 초대할 수 있습니다. Office&365; 또는 온-프레미스 Microsoft Exchange 전자 메일 주소, outlook.com 전자 메일 주소, 소셜 전자 메일 주소(예: Gmail, Yahoo 등)를 사용하는 모든 사용자는 이제 Azure AD 계정 또는 Microsoft 계정을 인라인으로 간단히 만들어 초대된 조직에 원활하게 액세스할 수 있습니다.

4. 전문적인 테넌트 브랜드 초대 전자 메일의 유용한 기능

5. 초대 API를 사용하여 온보딩을 사용자 지정할 수 있는 포괄적인 기능

6. 초대 조직의 B2B 공동 작업 사용자에 대한 다단계 인증

7. 관리자 이외의 사용자에게 초대를 위임하는 기능

8. B2B 공동 작업에 대한 PowerShell 지원

9. 감사 및 보고 기능

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

