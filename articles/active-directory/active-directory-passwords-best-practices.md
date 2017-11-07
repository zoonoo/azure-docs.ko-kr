---
title: "롤아웃: Azure AD SSPR | Microsoft 문서"
description: "Azure AD 셀프 서비스 암호 재설정을 성공적으로 롤아웃하기 위한 팁"
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: sahenry
ms.assetid: f8cd7e68-2c8e-4f30-b326-b22b16de9787
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/24/2017
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: e28324fe0d7e52f1721af6cd835369f024d4c58f
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/31/2017
---
# <a name="how-to-successfully-rollout-self-service-password-reset"></a>셀프 서비스 암호 재설정을 성공적으로 롤아웃하는 방법

대부분의 고객은 다음 단계에 따라 SSPR(셀프 서비스 암호 재설정) 기능을 원활하게 롤아웃합니다.

1. [디렉터리에서 암호 재설정을 사용하도록 설정합니다](active-directory-passwords-getting-started.md).
2. [비밀번호 쓰기 저장에 대한 온-프레미스 AD 권한을 구성합니다](active-directory-passwords-writeback.md#active-directory-permissions).
3. [비밀번호 쓰기 저장을 구성](active-directory-passwords-writeback.md#configuring-password-writeback)하여 Azure AD에서 온-프레미스 디렉터리로 비밀번호 다시 쓰기
4. [필요한 라이선스를 할당하고 확인합니다](active-directory-passwords-licensing.md).
5. SSPR을 점진적으로 롤아웃하려면 사용자 그룹에 대한 액세스를 제한하여 특정 그룹에서 파일럿을 수행할 수 있습니다. 이렇게 하려면 **셀프 서비스 암호 재설정이 사용하도록 설정됨**을 **선택됨**으로 전환하고, 암호 재설정을 사용하도록 설정할 보안 그룹을 선택합니다. 
6. 사무실 전화, 휴대폰 및 보조 이메일 주소와 같이 사용자에 대한 [인증 데이터](active-directory-passwords-data.md)를 채웁니다.
7. [회사 브랜딩이 포함되도록 Azure AD 로그인 환경을 사용자 지정합니다](active-directory-passwords-customize.md).
8. 사용자에게 등록 방법 및 재설정 방법을 보여 주는 지침을 제공하여 SSPR 사용 방법을 알려 줍니다.
9. 언제든지 등록을 적용할 수 있으며, 일정 기간이 지나면 인증 정보를 다시 확인하도록 사용자에게 요구할 수 있습니다.
10. 시간이 지나면 [Azure AD에서 제공하는 보고서](active-directory-passwords-reporting.md)를 검토하여 사용자의 등록과 사용을 검토합니다.
11. 준비가 되면 모든 사용자에 대해 암호 재설정을 사용하도록 설정하고, **셀프 서비스 암호 재설정이 사용하도록 설정됨**을 **모두**로 전환합니다. 

    > [!IMPORTANT]
    > 관리자가 아닌 사용자로 SSPR을 테스트합니다. Microsoft가 Azure 관리자 유형 계정에 강력한 인증 요구 사항을 적용하기 때문입니다. 관리자 암호 정책에 대한 자세한 내용은 [심층 분석 문서](active-directory-passwords-how-it-works.md)를 참조하세요.

## <a name="email-based-rollout"></a>전자 메일 기반 롤아웃

사용자가 SSPR을 사용하게 만드는 가장 쉬운 방법은 간단한 지침이 포함된 전자 메일 캠페인이라는 사실을 많은 고객이 알고 있습니다. [Microsoft는 고객의 롤아웃에 도움이 되도록 템플릿으로 사용할 수 있는 세 가지 간단한 전자 메일을 만들었습니다.](https://onedrive.live.com/?authkey=%21AD5ZP%2D8RyJ2Cc6M&id=A0B59A91C740AB16%2125063&cid=A0B59A91C740AB16)

* **출시 예정** 전자 메일 템플릿 - 롤아웃 몇 주 또는 며칠 전에 사용자에게 어떤 작업을 수행해야 한다는 사실을 알리기 위해 사용됩니다.
* **현재 사용 가능한** 전자 메일 템플릿 - 사용자가 필요할 때 SSPR을 사용할 수 있도록 출시 당일에 사용자가 인증 데이터를 등록하고 확인하도록 유도하기 위해 사용됩니다.
* **등록 미리 알림** 전자 메일 템플릿 - 배포 며칠 후 또는 몇 주 후에 사용자에게 인증 데이터를 등록하고 확인하라고 알리기 위해 사용됩니다.

![Email][Email]

## <a name="creating-your-own-password-portal"></a>고유의 암호 포털 만들기

대부분의 대기업 고객은 웹 페이지를 호스팅하고 https://passwords.contoso.com 같은 루트 DNS 항목을 만드는 방법을 선택합니다. 고객은 이 페이지를 Azure AD 암호 재설정, 암호 재설정 등록, 암호 변경 포털 및 기타 조직 관련 정보의 링크로 채웁니다. 이러한 방식으로 보내는 전자 메일 통신 또는 전단지에서, 사용자가 서비스를 사용해야 할 때 이동할 수 있는 유명하고 기억하기 쉬운 URL을 포함할 수 있습니다.

* 암호 재설정 포털 - https://aka.ms/sspr
* 암호 재설정 등록 포털 - http://aka.ms/ssprsetup
* 암호 변경 포털 - https://account.activedirectory.windowsazure.com/ChangePassword.aspx

사용자에게 도움이 되도록 조직의 필요에 맞게 사용하고 사용자 지정할 수 있는 샘플 페이지를 만들었으며 [GitHub](https://github.com/ajamess/password-reset-page)에서 다운로드할 수 있습니다.

## <a name="using-enforced-registration"></a>강제 등록 사용

사용자가 암호 재설정을 등록하게 하려면 사용자가 Azure AD를 사용하여 로그인할 때 강제로 등록하게 만들 수 있습니다. **등록** 탭의 **로그인할 때 사용자가 등록해야 함** 옵션을 사용하여 디렉터리의 **암호 재설정** 블레이드에서 이 옵션을 사용할 수 있습니다.

관리자는 **사용자가 인증 정보를 다시 확인해야 할 때까지 남은 일 수**를 0-730일 사이로 설정하여 일정 기간 후 사용자가 다시 등록하게 만들 수 있습니다.

이 옵션을 설정한 다음 사용자가 로그인하면 사용자의 요청에 따라 인증 정보를 확인해야 한다는 내용의 메시지가 표시됩니다.

## <a name="populate-authentication-data"></a>인증 데이터 채우기

[사용자의 인증 데이터를 채우면](active-directory-passwords-data.md) 사용자가 암호 재설정을 등록하지 않아도 SSPR을 사용할 수 있습니다. 관리자가 정의한 암호 재설정 정책을 충족하는 인증 데이터가 정의되어 있는 한, 사용자는 암호를 재설정할 수 있습니다.

## <a name="disabling-self-service-password-reset"></a>셀프 서비스 암호 재설정 해제

셀프 서비스 암호 재설정을 해제하는 방법은 아주 간단합니다. Azure AD 테넌트를 열고, **암호 재설정**, **속성**으로 이동하여 **셀프 서비스 암호 재설정이 사용하도록 설정됨** 아래에서 **아무도 없음**을 선택하면 됩니다.

## <a name="next-steps"></a>다음 단계

* [암호 재설정 또는 변경](active-directory-passwords-update-your-own-password.md)
* [셀프 서비스 암호 재설정 등록](active-directory-passwords-reset-register.md)
* [라이선스 관련 질문이 있습니까?](active-directory-passwords-licensing.md)
* [SSPR에서 사용하는 데이터는 무엇이며, 사용자에 대해 어떤 데이터를 채워야 합니까?](active-directory-passwords-data.md)
* [SSPR에서 사용하는 정책 옵션은 무엇입니까?](active-directory-passwords-policy.md)
* [비밀번호 쓰기 저장은 무엇이며, 왜 관심을 가져야 합니까?](active-directory-passwords-writeback.md)
* [SSPR 작업은 어떻게 보고 합니까?](active-directory-passwords-reporting.md)
* [모든 SSPR 옵션과 그 의미는 무엇입니까?](active-directory-passwords-how-it-works.md)
* [무엇인가 손상된 문제가 있습니다. SSPR 문제는 어떻게 해결합니까?](active-directory-passwords-troubleshoot.md)
* [다른 곳에서 다루지 않았던 질문이 있습니다.](active-directory-passwords-faq.md)

[Email]: ./media/active-directory-passwords-best-practices/sspr-emailtemplates.png "조직의 요구 사항에 맞게 이러한 전자 메일 템플릿을 사용자 지정"