---
title: 클라우드에서 Azure MFA 시작
description: 조건부 액세스를 사용하여 Microsoft Azure Multi-Factor Authentication 시작
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: michmcla
ms.openlocfilehash: d248f8bc5708dfe8554f513d4f96a6c1bee7605e
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39412415"
---
# <a name="deploy-cloud-based-azure-multi-factor-authentication"></a>클라우드 기반 Azure Multi-Factor Authentication 배포

Azure MFA(Azure Multi-Factor Authentication)를 시작하는 프로세스는 간단합니다.

시작하기 전에 다음 필수 조건을 갖추고 있는지 확인합니다.

* Azure AD 테넌트의 전역 관리자 계정. 이 단계를 완료하는 도움이 필요한 경우 [Azure AD 시작](../get-started-azure-ad.md) 문서를 참조하세요.
* 사용자에게 할당된 올바른 라이선스. 자세한 내용은 [Azure Multi-Factor Authentication을 가져오는 방법](concept-mfa-licensing.md)을 참조하세요.

## <a name="choose-how-to-enable"></a>사용하도록 설정하는 방법 선택

**조건부 액세스 정책에 따라 사용하도록 설정** - 이 문서에서는 이 방법을 설명합니다. 사용자에게 2단계 인증을 사용하도록 설정하는 가장 유연한 방법입니다. 조건부 액세스 정책을 사용하도록 설정하는 방법은 클라우드의 Azure MFA에서만 가능하며, Azure AD의 프리미엄 기능입니다.

Azure AD ID 보호에 따라 사용하도록 설정 - 이 방법은 Azure AD ID 보호 위험 정책을 사용하여 모든 클라우드 응용 프로그램에 대해 로그인 위험이 있을 때만 2단계 인증을 요구합니다. 이 방법에는 Azure Active Directory P2 라이선스가 필요합니다. 이 방법에 대한 자세한 내용은 [Azure Active Directory ID 보호](../active-directory-identityprotection.md#risky-sign-ins)에서 확인할 수 있습니다.

사용자 상태를 변경하여 사용하도록 설정 - 2단계 인증을 요구하는 전통적인 방법입니다. 이 방법은 클라우드의 Azure MFA와 Azure MFA Server 둘 다에서 작동합니다. 이 방법을 사용할 경우 사용자는 로그인할 **때마다** 2단계 인증을 수행해야 하며, 조건부 액세스 정책이 무시됩니다. 이 방법에 대한 자세한 내용은 [사용자에 대해 2단계 인증을 요구하는 방법](howto-mfa-userstates.md)에서 찾을 수 있습니다.

> [!Note]
> 라이선스 및 가격 책정에 대한 자세한 내용은 [Azure AD](https://azure.microsoft.com/pricing/details/active-directory/
) 및 [Multi-Factor Authentication](https://azure.microsoft.com/pricing/details/multi-factor-authentication/) 가격 책정 페이지에서 확인할 수 있습니다.

## <a name="choose-authentication-methods"></a>인증 방법 선택

조직의 요구 사항에 따라 사용자에게 하나 이상의 인증 방법을 사용하도록 설정합니다. 사용자에게 인증 방법을 사용하도록 설정하면 Microsoft Authenticator 앱이 최상의 사용자 환경을 제공합니다. 사용할 수 있는 방법 및 설정 방법에 대해 알아보려면 [인증 방법이란?](concept-authentication-methods.md) 문서를 참조하세요.

## <a name="get-users-to-enroll"></a>등록할 사용자 가져오기

조건부 액세스 정책을 사용하도록 설정하면 사용자는 다음에 정책으로 보호되는 앱을 사용할 때 강제로 등록해야 합니다. 모든 클라우드 앱의 모든 사용자에게 MFA를 요구하는 정책을 사용하도록 설정하면 사용자 및 기술 지원팀에게 골치 아픈 문제가 발생할 수 있습니다. [https://aka.ms/mfasetup](https://aka.ms/mfasetup)에서 등록 포털을 사용하기 전에 인증 방법을 등록하라고 사용자에게 요청하는 것이 좋습니다. 많은 조직에서는 포스터, 테이블 카드 및 이메일 메시지를 만들면 도입을 촉진하는 데 도움이 된다는 사실을 발견했습니다.

## <a name="enable-multi-factor-authentication-with-conditional-access"></a>조건부 액세스를 사용하여 Multi-Factor Authentication 사용

전역 관리자 계정을 사용하여 [Azure Portal](https://portal.azure.com)에 로그인합니다.

### <a name="choose-verification-options"></a>확인 옵션 선택

Azure Multi-Factor Authentication을 사용하도록 설정하기 전에 조직에서 어떤 확인 옵션을 허용할 것인지 결정해야 합니다. 이 연습에서는 대부분의 사람들이 사용할 수 있는 일반적인 옵션인 전화 통화 및 문자 메시지를 사용합니다. 인증 방법 및 용도에 대한 자세한 내용은 [인증 방법이란?](concept-authentication-methods.md) 문서에서 찾을 수 있습니다.

1. **Azure Active Directory**, **사용자**, **Multi-Factor Authentication로 이동**
   ![Azure Portal의 Azure AD 사용자 블레이드에서 Multi-Factor Authentication 포털에 액세스](media/howto-mfa-getstarted/users-mfa.png) 
2. 열리는 새 탭에서 **서비스 설정**으로 이동
3. **확인 옵션** 아래에서, 사용자에게 제공할 방법의 확인란 선택
   * 휴대폰에 전화 걸기
   * 휴대폰에 문자 메시지 전송

   ![Multi-Factor Authentication 서비스 설정 탭에서 인증 방법 구성](media/howto-mfa-getstarted/mfa-servicesettings-verificationoptions.png)

4. 설정 메뉴에서 **저장**
5. **서비스 설정** 탭 닫기

### <a name="create-conditional-access-policy"></a>조건부 액세스 정책 만들기

1. 전역 관리자 계정을 사용하여 [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **Azure Active Directory** , **조건부 액세스**로 이동합니다.
1. **새 정책**을 선택합니다.
1. 의미 있는 정책 이름을 입력합니다.
1. **사용자 및 그룹** 아래에서
   * **포함** 탭에서 **모든 사용자** 라디오 단추를 선택합니다.
   * 권장 사항: **제외** 탭에서 **사용자 및 그룹** 확인란을 선택하고, 사용자가 자신의 인증 방법에 액세스할 수 없는 경우 예외에 사용할 그룹을 선택합니다.
   * **완료**를 클릭합니다.
1. **클라우드 앱**에서 **모든 클라우드 앱** 라디오 단추를 선택합니다.
   * 선택 사항: **제외** 탭에서, 조직에서 MFA를 요구하지 않는 클라우드 앱을 선택합니다.
   * **완료**를 클릭합니다.
1. **조건** 섹션에서
   * 선택 사항: Azure Identity Protection을 사용하도록 설정한 경우 정책의 일부로 로그인 위험을 평가하도록 선택할 수 있습니다.
   * 선택 사항: 신뢰할 수 있는 위치를 구성했거나 위치 이름을 지정한 경우 정책에 해당 위치를 포함 또는 제거하도록 지정할 수 있습니다.
1. **권한 부여** 아래에서 **액세스 권한 부여** 라디오 단추를 선택합니다.
    * **Multi-Factor Authentication 필요** 확인란을 선택합니다.
    * **선택** 클릭
1. **세션** 섹션을 건너뜁니다.
1. **정책 사용** 토글을 **켜기**로 설정합니다.
1. **만들기**

![파일럿 그룹의 Azure Portal 사용자에게 MFA를 사용하도록 조건부 액세스 정책 만들기](media/howto-mfa-getstarted/conditionalaccess-newpolicy.png)

### <a name="test-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication 테스트

조건부 액세스 정책이 작동하는지 확인하려면 MFA를 요구하지 않아야 하는 리소스에 로그인 후 MFA를 요구하는 Azure Portal에 로그인하는 테스트를 수행하세요.

1. InPrivate 또는 incognito 모드에서 새 브라우저 창을 열고 [https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com)으로 이동합니다.
   * 이 문서의 필수 조건 섹션에서 만든 테스트 사용자로 로그인하여 MFA를 완료하라고 요청하지는 않는지 확인합니다.
   * 브라우저 창을 닫습니다.
2. InPrivate 또는 incognito 모드에서 새 브라우저 창을 열고 [https://portal.azure.com](https://portal.azure.com)으로 이동합니다.
   * 이 문서의 필수 조건 섹션에서 만든 테스트 사용자로 로그인하면 이제 Azure Multi-Factor Authentication을 등록하여 사용해야 합니다.
   * 브라우저 창을 닫습니다.

## <a name="next-steps"></a>다음 단계

축하합니다. 클라우드에서 Azure Multi-Factor Authentication을 설정했습니다.

신뢰할 수 있는 IP, 사용자 지정 음성 메시지 및 사기 행위 경고와 같은 추가 설정을 구성하려면 [Azure Multi-Factor Authentication 설정 구성](howto-mfa-mfasettings.md) 문서를 참조하세요.

Azure Multi-Factor Authentication에 대한 사용자 설정 관리에 대한 내용은 [클라우드에서 Azure Multi-Factor Authentication을 사용하여 사용자 설정 관리](howto-mfa-userdevicesettings.md) 문서에서 확인할 수 있습니다.
