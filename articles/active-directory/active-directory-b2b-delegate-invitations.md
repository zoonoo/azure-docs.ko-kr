---
title: "Azure Active Directory B2B 공동 작업에 대한 초대 위임 | Microsoft Docs"
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
ms.date: 02/06/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: 0c05cd490ee9125f7e5182cb502db6f4e9390094
ms.openlocfilehash: 6c6d757a770613498bedca0f8f3a965241d692eb


---

# <a name="delegate-invitations-for-azure-active-directory-b2b-collaboration"></a>Azure Active Directory B2B 공동 작업에 대한 초대 위임

Azure AD(Azure Active Directory) B2B 공동 작업 공개 미리 보기 새로 고침을 사용하면 전역 관리자가 아니더라도 사용자를 초대할 수 있습니다. 정책을 통해 초대할 수 있는 사용자를 제어하고, 초대 역할이 허용된 사용자에게 초대를 위임할 수 있습니다. 게스트 사용자 초대를 위임하는 중요한 새 방식은 게스트 초대자 역할을 사용하는 것입니다.

## <a name="guest-inviter-role"></a>게스트 초대자 역할
사용자에게 초대를 전송할 수 있는 게스트 초대자 역할에 할당할 수 있습니다. 따라서 전역 관리자가 아니더라도 초대를 보낼 수 있습니다. 기본적으로 일반 사용자도 전역 관리자가 해당 초대를 사용하지 않도록 설정한 경우가 아니면 초대 API를 호출할 수 있습니다. Azure Portal 및 PowerShell을 통해 이 작업을 수행할 수 있습니다.

PowerShell을 통해 게스트 초대자 역할에 사용자를 추가하는 방법을 보여 주는 예제는 다음과 같습니다.

```
Add-MsolRoleMember -RoleObjectId 95e79109-95c0-4d8e-aee3-d01accf2d47b -RoleMemberEmailAddress <RoleMemberEmailAddress >
```

## <a name="controlling-who-can-invite"></a>초대할 수 있는 사용자 제어

![초대 방법 제어](media/active-directory-b2b-delegate-invitations/control-who-to-invite.png)

Azure AD B2B 공동 작업을 사용하면 테넌트 관리자가 다음 초대 정책을 설정할 수 있습니다.

1. 초대 해제
2. 관리자 및 게스트 초대자 역할의 사용자만 초대를 수행할 수 있습니다.
3. 관리자, 게스트 초대자 역할 및 구성원은 초대를 수행할 수 있습니다.
4. 게스트를 포함한 모든 사용자가 초대를 수행할 수 있습니다.

기본적으로 테넌트는 #4(게스트를 포함한 모든 사용자가 B2B 사용자를 초대할 수 있음)로 설정됩니다.

## <a name="next-steps"></a>다음 단계

Azure AD B2B 공동 작업에 대한 다른 문서 찾아보기:

* [Azure AD B2B 공동 작업이란?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [B2B 공동 작업 사용자 속성](active-directory-b2b-user-properties.md)
* [역할에 B2B 공동 작업 사용자 추가](active-directory-b2b-add-guest-to-role.md)
* [동적 그룹 및 B2B 공동 작업](active-directory-b2b-dynamic-groups.md)
* [B2B 공동 작업 코드 및 PowerShell 샘플](active-directory-b2b-code-samples.md)
* [B2B 공동 작업용 SaaS 앱 구성](active-directory-b2b-configure-saas-apps.md)
* [B2B 공동 작업 사용자 토큰](active-directory-b2b-user-token.md)
* [B2B 공동 작업 사용자 클레임 매핑](active-directory-b2b-claims-mapping.md)
* [Office 365 외부 공유](active-directory-b2b-o365-external-user.md)
* [B2B 공동 작업 현재 제한](active-directory-b2b-current-limitations.md)



<!--HONumber=Feb17_HO1-->


