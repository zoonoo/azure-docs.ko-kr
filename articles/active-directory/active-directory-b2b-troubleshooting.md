---
title: "Azure Active Directory B2B 공동 작업 문제 해결 | Microsoft Docs"
description: "Azure Active Directory B2B 공동 작업과 관련된 일반 문제를 해결하는 방법"
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
ms.date: 02/09/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: a4e59dfa8a098f63c3173176c4d2675d6a59af00
ms.openlocfilehash: f85c6bcc2abbd14c7879462f7013a97f550fdca5


---

# <a name="troubleshooting-azure-active-directory-b2b-collaboration"></a>Azure Active Directory B2B 공동 작업 문제 해결

Azure AD(Azure Active Directory) B2B 공동 작업과 관련된 일반적인 문제에 대한 몇 가지 해결책은 다음과 같습니다.

## <a name="i-cant-create-an-external-user-due-to-an-existing-contact"></a>기존 연락처로 인해 외부 사용자를 만들 수 없습니다.

기존 연락처 개체가 초대할 외부 사용자에게 이미 있는 경우 대개 해당 연락처 개체를 제거하여 충돌을 해결할 때까지는 해당 사용자를 초대할 수 없습니다. B2B 공동 작업을 일반적으로 사용할 수 있을 때까지 수동으로 충돌을 해결해야 합니다.

## <a name="ive-added-an-external-user-but-do-not-see-them-in-my-global-address-book-or-in-the-people-picker"></a>외부 사용자를 추가했지만 [전체 주소 목록]이나 사용자 선택에서 볼 수 없습니다.

외부 사용자가 목록에 채워지지 않은 경우 개체를 복제하는 데 몇 분이 걸릴 수 있습니다.

## <a name="invitations-have-been-disabled-for-directory"></a>디렉터리에 대한 초대가 사용하도록 설정되지 않았습니다.

사용자를 초대할 수 있는 권한이 없다는 오류 메시지가 나타나면 사용자 계정이 외부 사용자를 초대할 권한이 있는지 확인합니다. 이 작업은 다음과 같이 [사용자 설정] 아래에서 수행할 수 있습니다.

![](media/active-directory-b2b-troubleshooting/external-user-settings.png)

최근에 이러한 설정을 수정했거나 사용자에게 [게스트 초대자] 역할을 할당한 경우 변경 내용이 적용되는 데 15-60분 정도 걸릴 수 있습니다.

## <a name="the-user-that-i-invited-is-receiving-an-error-during-redemption"></a>상환 중에 내가 초대한 사용자에게 오류가 발생했습니다.

일반적인 오류는 다음과 같습니다.

### <a name="invitees-admin-has-disallowed-emailverified-users-from-being-created-in-their-tenant"></a>초대 대상자의 관리자가 테넌트에 EmailVerified 사용자를 만들지 못하도록 했습니다.

특정 사용자의 계정이 없는 Azure Active Directory를 사용하는 조직의 사용자(AAD contoso.com에 존재하지 않는 사용자)를 초대하는 경우 contoso.com의 관리자가 정책을 사용하여 사용자를 만들지 못하게 할 수 있습니다. 외부 사용자는 자신이 허용되는지 확인하려면 관리자를 통해 확인해야 하며, 외부 사용자의 관리자는 도메인에서 전자 메일 확인 사용자를 허용해야 할 수 있습니다(EmailVerified 사용자 허용에 대해 [이 문서](https://docs.microsoft.com/en-us/powershell/msonline/v1/set-msolcompanysettings#parameters) 참조).

![](media/active-directory-b2b-troubleshooting/allow-email-verified-users.png)

### <a name="external-user-does-not-exist-already-in-a-federated-domain"></a>외부 사용자가 이미 페더레이션된 도메인에 존재하지 않습니다.

외부 사용자가 온-프레미스에서 인증을 수행하고 사용자가 Azure Active Directory에 아직 존재하지 않는 페더레이션 솔루션을 사용하는 경우 사용자를 초대할 수 없습니다.

이 문제를 해결하려면 외부 사용자의 관리자가 사용자 계정을 Azure Active Directory와 동기화해야 합니다.

## <a name="how-does--which-is-normally-an-invalid-character-sync-with-azure-ad"></a>일반적으로 잘못된 문자인 '\#'은 Azure AD와 어떻게 동기화됩니까?

"\#"은 Azure AD B2B 공동 작업 또는 외부 사용자를 위한 UPN의 예약된 문자이므로(즉 &lt;user@contoso.com&gt;이 초대되면 &lt;user_contoso.com#EXT@fabrikam.onmicrosoft.com&gt;이 되므로) 온-프레미스에서 나온 UPN의 \#은 Azure Portal에 로그인할 수 없습니다.

## <a name="i-receive-an-error-when-adding-external-users-to-a-synchronized-group"></a>동기화된 그룹에 외부 사용자를 추가할 때 오류가 발생합니다.

외부 사용자는 "할당된" 그룹 또는 "보안" 그룹에만 추가할 수 있으며 온-프레미스에 마스터된 그룹에는 추가할 수 없습니다.

## <a name="my-external-user-did-not-receive-an-email-to-redeem"></a>외부 사용자가 상환할 전자 메일을 받지 못했습니다.

초대 대상자는 ISP 또는 스팸 필터를 확인하여 &lt;Invites@microsoft.com&gt; 주소가 허용되는지 확인해야 합니다.

## <a name="my-recipient-received-multiple-emails-from-me"></a>내 받는 사람이 나에게서 전자 메일을 여러 개 받았습니다.

초대를 받는 사람에게 자신의 계정에 대한 별칭이 여러 개 있는 경우 초대를 두 번 받을 수도 있습니다. 이 경우 처음 상환된 링크는 만들어진 계정이며 두 번째 상환 링크는 유효하지 않습니다.

## <a name="next-steps"></a>다음 단계

Azure AD B2B 공동 작업에 대한 다른 문서 찾아보기:

* [Azure AD B2B 공동 작업이란?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Azure Active Directory 관리자가 B2B 공동 작업 사용자를 추가하는 방법](active-directory-b2b-admin-add-users.md)
* [정보 근로자가 B2B 공동 작업 사용자를 추가하는 방법](active-directory-b2b-iw-add-users.md)
* [B2B 공동 작업 초대 전자 메일의 요소](active-directory-b2b-invitation-email.md)
* [B2B 공동 작업 초대 상환](active-directory-b2b-redemption-experience.md)
* [Azure AD B2B 공동 작업 라이선스](active-directory-b2b-licensing.md)
* [Azure Active Directory B2B 공동 작업 자주 묻는 질문(FAQ)](active-directory-b2b-faq.md)
* [Azure Active Directory B2B 공동 작업 API 및 사용자 지정](active-directory-b2b-api.md)
* [B2B 공동 작업 사용자에 대한 다단계 인증](active-directory-b2b-mfa-instructions.md)
* [초대 없이 B2B 공동 작업 사용자 추가](active-directory-b2b-add-user-without-invite.md)
* [Azure Active Directory의 응용 프로그램 관리를 위한 문서 인덱스](active-directory-apps-index.md)



<!--HONumber=Feb17_HO2-->


