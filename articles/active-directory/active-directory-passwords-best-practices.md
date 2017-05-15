---
title: "롤아웃: Azure AD SSPR | Microsoft 문서"
description: "Azure AD 셀프 서비스 암호 재설정을 성공적으로 롤아웃하기 위한 팁"
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.assetid: f8cd7e68-2c8e-4f30-b326-b22b16de9787
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/26/2017
ms.author: joflore
ms.translationtype: Human Translation
ms.sourcegitcommit: be3ac7755934bca00190db6e21b6527c91a77ec2
ms.openlocfilehash: 328537b2bfbe15a23a9199bbb3c07851eaa85390
ms.contentlocale: ko-kr
ms.lasthandoff: 05/03/2017


---
# <a name="roll-out-password-reset-for-users"></a>사용자 암호 재설정 롤아웃

대부분의 고객은 SSPR 기능의 원활한 롤아웃을 위해 수반되는 단계를 따릅니다.

1. [디렉터리에서 암호 재설정 사용](active-directory-passwords-getting-started.md)
2. [비밀번호 쓰기 저장을 위한 온-프레미스 AD 권한 구성](active-directory-passwords-how-it-works.md#active-directory-permissions)
3. [필요한 라이선스 할당 및 확인](active-directory-passwords-licensing.md)
4. 점진적으로 롤아웃하려는 경우 암호 재설정을 사용자 그룹으로 제한하여 시간에 따라 기능을 천천히 롤아웃할 수 있습니다. 이렇게 하려면**셀프 서비스 암호 재설정이 사용하도록 설정됨**을 **모든 사람**에서 **그룹**으로 전환하고 암호 재설정을 사용할 보안 그룹을 선택합니다. 이 그룹의 모든 구성원에게 라이선스가 할당되어 있어야 하며 [그룹 기반 라이선스](active-directory-passwords-licensing.md#enable-group-or-user-based-licensing)를 사용하는 훌륭한 방법입니다.
5. 정책에 따라 최소 [인증 데이터](active-directory-passwords-data.md) 집합을 채웁니다.
6. 사용자에게 등록 방법 및 재설정 방법을 보여 주는 지침을 제공하여 SSPR 사용 방법을 알려 줍니다.
    > [!NOTE]
    > 관리자가 아닌 사용자로 SSPR을 테스트합니다. Microsoft가 Azure 관리자 유형 계정에 강력한 인증 요구 사항을 적용하기 때문입니다. 관리자 암호 정책에 대한 자세한 내용은 [심층 분석 문서](active-directory-passwords-how-it-works.md)를 참조하세요.

7. 언제든지 등록을 적용할 수 있으며, 일정 기간이 지나면 인증 정보를 다시 확인하도록 사용자에게 요구할 수 있습니다. 사용자가 의무적으로 등록하지 않아도 되게 하려면 [최종 사용자에게 등록을 요구하지 않고 암호 재설정을 배포](active-directory-passwords-data.md)하면 됩니다.
8. 시간이 지나면 [Azure AD에서 제공하는 보고서](active-directory-passwords-reporting.md)를 검토하여 사용자의 등록과 사용을 검토합니다.

## <a name="email-based-rollout"></a>전자 메일 기반 롤아웃

사용자가 SSPR을 사용하게 만드는 가장 쉬운 방법은 간단한 지침이 포함된 전자 메일 캠페인이라는 사실을 많은 고객이 알고 있습니다. Microsoft는 고객의 롤아웃에 도움이 되도록 템플릿으로 사용할 수 있는 세 가지 간단한 전자 메일을 만들었습니다.

* **출시 예정** 전자 메일 템플릿 - 롤아웃 몇 주 또는 며칠 전에 사용자에게 어떤 작업을 수행해야 한다는 사실을 알리기 위해 사용됩니다.
* **현재 사용 가능한** 전자 메일 템플릿 - 사용자가 필요할 때 SSPR을 사용할 수 있도록 출시 당일에 사용자가 인증 데이터를 등록하고 확인하도록 유도하기 위해 사용됩니다.
* **등록 미리 알림** 전자 메일 템플릿 - 배포 며칠 후 또는 몇 주 후에 사용자에게 인증 데이터를 등록하고 확인하라고 알리기 위해 사용됩니다.

## <a name="creating-your-own-password-portal"></a>고유의 암호 포털 만들기

대부분의 대기업 고객은 웹 페이지를 호스팅하고 https://passwords.contoso.com 같은 루트 DNS 항목을 만드는 방법을 선택합니다. 고객은 이 페이지를 Azure AD 암호 재설정, 암호 재설정 등록, 암호 변경 포털 및 기타 조직 관련 정보의 링크로 채웁니다. 이러한 방식으로 보내는 전자 메일 통신 또는 전단지에서, 사용자가 서비스를 사용해야 할 때 이동할 수 있는 유명하고 기억하기 쉬운 URL을 포함할 수 있습니다.

* 암호 재설정 포털 - https://passwordreset.microsoftonline.com/
* 암호 재설정 등록 포털 - http://aka.ms/ssprsetup
* 암호 변경 포털 - https://account.activedirectory.windowsazure.com/ChangePassword.aspx

## <a name="using-enforced-registration"></a>강제 등록 사용

사용자가 암호 재설정을 등록하게 하려면 사용자가 Azure AD를 사용하여 로그인할 때 강제로 등록하게 만들 수 있습니다. **등록** 탭의 **로그인할 때 사용자가 등록해야 함** 옵션을 사용하여 디렉터리의 **암호 재설정** 블레이드에서 이 옵션을 사용할 수 있습니다.

관리자는 **사용자가 인증 정보를 다시 확인해야 할 때까지 남은 일 수**를 0-730일 사이로 설정하여 일정 기간 후 사용자가 다시 등록하게 만들 수 있습니다.

이 옵션을 설정한 다음 사용자가 로그인하면 사용자의 요청에 따라 인증 정보를 확인해야 한다는 내용의 메시지가 표시됩니다.

## <a name="populate-authentication-data"></a>인증 데이터 채우기

[사용자의 인증 데이터를 채우면](active-directory-passwords-data.md) 사용자가 암호 재설정을 등록하지 않아도 SSPR을 사용할 수 있습니다. 관리자가 정의한 암호 재설정 정책을 충족하는 인증 데이터가 정의되어 있는 한, 사용자는 암호를 재설정할 수 있습니다.

## <a name="disabling-self-service-password-reset"></a>셀프 서비스 암호 재설정 해제

셀프 서비스 암호 재설정을 해제하는 방법은 아주 간단합니다. Azure AD 테넌트를 열고, **암호 재설정**, **속성**으로 이동하여 **셀프 서비스 암호 재설정이 사용하도록 설정됨** 아래에서 **아무도 없음**을 선택하면 됩니다.

## <a name="next-steps"></a>다음 단계

다음 링크는 Azure AD를 사용한 암호 재설정에 대한 추가 정보를 제공합니다.

* [**빠른 시작**](active-directory-passwords-getting-started.md) - Azure AD 셀프 서비스 암호 관리를 사용하여 운영 시작 
* [**라이선스**](active-directory-passwords-licensing.md) - Azure AD 라이선스 구성
* [**데이터**](active-directory-passwords-data.md) - 암호 관리에 필요한 데이터 및 사용 방식을 이해
* [**사용자 지정**](active-directory-passwords-customize.md) - 회사 SSPR 경험의 모양과 느낌을 사용자 지정.
* [**정책**](active-directory-passwords-policy.md) - Azure AD 암호 정책을 이해하고 설정
* [**비밀번호 쓰기 저장**](active-directory-passwords-writeback.md) - 비밀번호 쓰기 저장이 온-프레미스 디렉터리와 함께 작동 하는 원리
* [**보고**](active-directory-passwords-reporting.md) - 사용자가 SSPR 기능에 액세스하는 조건, 시간 및 위치 탐색
* [**기술 심층 분석**](active-directory-passwords-how-it-works.md) - 작동 방식을 이해하기 위해 심층 분석
* [**질문과 대답**](active-directory-passwords-faq.md) - 어떤 방식으로? 그 이유는 무엇을? 어디서? 누가? 언제? - 많은 분들이 항상 묻는 질문에 대한 답변입니다.
* [**문제 해결**](active-directory-passwords-troubleshoot.md) - SSPR의 일반적인 문제 해결 방법 알아보기
