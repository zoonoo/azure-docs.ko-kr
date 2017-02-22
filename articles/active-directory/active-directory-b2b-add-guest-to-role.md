---
title: "Azure Active Directory B2B 공동 작업 사용자를 역할에 추가 | Microsoft Docs"
description: "Azure Active Directory B2B 공동 작업 사용자 속성은 구성 가능합니다."
services: active-directory
documentationcenter: 
author: sasubram
manager: femila
editor: 
tags: 
ms.assetid: 
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 02/03/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: d3f68340592d9032999ecb5cc16ac1fedcce4c33
ms.openlocfilehash: 9ba46fe5ce6f06a69b057ebcb4af072689ac8ce9


---

# <a name="adding-an-azure-active-directory-b2b-collaboration-user-to-a-role"></a>Azure Active Directory B2B 공동 작업 사용자를 역할에 추가

Azure AD(Azure Active Directory) B2B 공동 작업 사용자가 디렉터리에 게스트 사용자로 추가되고 디렉터리의 게스트 사용 권한이 기본적으로 제한되지만 기업은 조직의 추가 권한 역할을 채우기 위해 게스트 사용자가 필요할 수 있습니다. 이를 지원하기 위해 조직의 요구에 따라 원하는 역할에 게스트 사용자를 추가할 수 있습니다.

## <a name="default-role"></a>기본 역할

![기본 역할](media/active-directory-b2b-add-guest-to-role/default-role.png)

## <a name="global-administrator-role"></a>전역 관리자 역할

![전역 관리자 역할](media/active-directory-b2b-add-guest-to-role/global-admin-role.png)

## <a name="limited-administrator-role"></a>제한된 관리자 역할

![제한된 관리자 역할](media/active-directory-b2b-add-guest-to-role/limited-admin-role.png)

## <a name="auditing-and-reporting"></a>감사 및 보고
게스트 사용자를 사용하여 이제 구성원 사용자와 동등한 감사 기능을 얻을 수 있습니다. 방금 초대한 Sam Oogle의 초대 및 상환 기록에 대한 아래 예제를 참조하세요.

![감사 로그](media/active-directory-b2b-add-guest-to-role/audit-log.png)

이러한 각 이벤트를 심층 분석하여 자세한 정보를 얻을 수 있습니다. 예를 들어 승인 세부 정보를 살펴보겠습니다.

![활동 세부 정보](media/active-directory-b2b-add-guest-to-role/activity-details.png)

Azure AD에서 이러한 로그를 내보낸 후 원하는 보고 도구를 사용하여 익숙한 보고서를 얻을 수도 있습니다.

## <a name="resending-invitations"></a>초대 다시 전송
B2B 공동 작업 사용자의 프로필 페이지로 이동하고, 어떤 이유로든 아직 상환되지 않은 게스트 사용자에게 초대를 다시 전송할 수 있습니다.

![초대 다시 전송](media/active-directory-b2b-add-guest-to-role/resend-invitation.png)

> ![NOTE] 원래 초대가 특정 앱 또는 그룹을 대상으로 하더라도 로그인한 사용자로부터 초대가 다시 전송되고, 사용자가 디렉터리로 초대됩니다.

## <a name="next-steps"></a>다음 단계

Azure AD B2B 공동 작업에 대한 다른 문서 찾아보기:

* [Azure AD B2B 공동 작업이란?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [B2B 공동 작업 사용자 속성](active-directory-b2b-user-properties.md)
* [B2bB 공동 작업 초대 위임](active-directory-b2b-delegate-invitations.md)
* [동적 그룹 및 B2B 공동 작업](active-directory-b2b-dynamic-groups.md)
* [B2B 공동 작업 코드 및 PowerShell 샘플](active-directory-b2b-code-samples.md)
* [B2B 공동 작업용 SaaS 앱 구성](active-directory-b2b-configure-saas-apps.md)
* [B2B 공동 작업 사용자 토큰](active-directory-b2b-user-token.md)
* [B2B 공동 작업 사용자 클레임 매핑](active-directory-b2b-claims-mapping.md)
* [Office 365 외부 공유](active-directory-b2b-o365-external-user.md)
* [B2B 공동 작업 현재 제한](active-directory-b2b-current-limitations.md)



<!--HONumber=Feb17_HO1-->


