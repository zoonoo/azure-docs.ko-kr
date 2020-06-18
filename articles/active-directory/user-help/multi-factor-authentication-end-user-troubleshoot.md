---
title: 계정 2단계 인증의 일반적인 문제 - Azure AD
description: 몇 가지 일반적인 2단계 인증 문제들과 회사 또는 학교 계정에 대한 솔루션입니다.
services: active-directory
author: curtand
manager: daveba
ms.assetid: 8f3aef42-7f66-4656-a7cd-d25a971cb9eb
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 04/02/2020
ms.author: curtand
ms.reviewer: kexia
ms.openlocfilehash: 8d2e34bcfd180dfeb814dace2a496f3ac593c5bf
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83738611"
---
# <a name="common-problems-with-two-factor-verification-and-your-work-or-school-account"></a>2단계 인증 및 회사 또는 학교 계정에 대한 일반적인 문제

Azure AD(Azure Active Directory) 조직에서 2단계 인증을 설정하면 회사 또는 학교 계정으로 로그인하는 경우 사용자 이름, 암호 및 모바일 디바이스 또는 휴대폰의 조합이 필요합니다. 사용자가 알고 있는 것과 휴대하는 것, 두 가지 형태의 인증을 사용하기 때문에 단순 암호에 비해 더 안전합니다. 2단계 인증은 악의적인 해커가 사용자를 가장하지 못하게 차단하는 효과가 있습니다. 해커가 사용자의 암호를 알아내더라도 사용자의 디바이스까지 손에 넣을 가능성은 별로 높지 않기 때문입니다.

<center>

![개념적 인증 방법 이미지](../authentication/media/concept-mfa-howitworks/methods.png)</center>

몇 가지 일반적인 2단계 인증 문제들은 생각보다 더 자주 발생하는 것처럼 보이기도 합니다. 가장 일반적인 문제를 해결하고 몇 가지 가능한 픽스를 설명하기 위해 이 문서를 함께 제공합니다.

>[!Important]
>관리자의 경우, [Azure AD 문서](https://docs.microsoft.com/azure/active-directory)에서 Azure AD 환경을 설정하고 관리하는 방법에 대한 자세한 정보를 확인할 수 있습니다.
>
>이 콘텐츠는 조직에서 귀하에게 제공한 계정(예: alain@contoso.com)에 해당되는 회사 또는 학교 계정에만 사용할 수 있습니다. 2단계 인증과 직접 설정한 계정(예: danielle@outlook.com)에 해당되는 개인 Microsoft 계정에 문제가 있다면 [Microsoft 계정에 대한 2단계 인증 설정 또는 해제](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off)를 참조하세요.

## <a name="i-dont-have-my-mobile-device-with-me"></a>내 모바일 디바이스가 없습니다.

문제가 발생합니다. 회사에 모바일 디바이스를 놔두고 갔기 때문에 지금은 휴대폰을 사용하여 신원 확인을 할 수 없습니다. 이전에 사용자 계정에 로그인하는 다른 방법(예: 사무실 전화 등)을 추가한 경우에는 이 방법을 사용할 수 있습니다. 추가 인증 방법을 추가하지 않았다면 조직의 지원 센터에 문의하여 사용 중인 계정으로 다시 전환하는 데 필요한 도움을 받으세요.

### <a name="to-sign-in-to-your-work-or-school-account-using-another-verification-method"></a>다른 인증 방법을 사용하여 회사 또는 학교 계정에 로그인

1. 계정에 로그인하고 **2단계 인증** 페이지에서 **다른 방법으로 로그인** 링크를 선택합니다.

    ![로그인 인증 방법 변경](./media/multi-factor-authentication-end-user-troubleshoot/two-factor-auth-signin-another-way.png)

    **다른 방법으로 로그인** 링크가 표시되지 않으면 다른 인증 방법을 설정하지 않은 것입니다. 계정에 로그인하는 데 도움이 필요하면 관리자에게 문의해야 합니다.

2. 대체 인증 방법을 선택하고 2단계 인증 프로세스를 계속합니다.

## <a name="i-lost-my-mobile-device-or-it-was-stolen"></a>모바일 디바이스를 분실했거나 도난당한 경우

모바일 디바이스를 분실했거나 도난당한 경우, 다른 방법을 사용하여 로그인하거나 조직의 지원 센터에 문의하여 설정을 지울 수 있습니다. 휴대폰을 분실하거나 도난 당한 경우, 계정에 적절한 업데이트를 적용할 수 있도록 조직 지원 센터에 알릴 것을 적극 권장합니다. 설정이 지워진 후 다음 번에 로그인할 때 [2단계 인증 등록](multi-factor-authentication-end-user-first-time.md) 메시지가 표시됩니다.

## <a name="im-not-getting-the-verification-code-sent-to-my-mobile-device"></a>내 모바일 디바이스에 전송된 확인 코드가 수신되지 않습니다.

확인 코드가 수신되지 않는 것은 일반적인 문제에 속하며 대체로 모바일 디바이스 및 그 설정과 관련이 있습니다. 시도할 수 있는 몇 가지 작업은 다음과 같습니다.

사용 방법 | 지침 정보
--------- | ------------
모바일 디바이스 다시 시작 | 경우에 따라 디바이스에 새로 고침이 필요할 수도 있습니다. 디바이스를 다시 시작하면 현재 실행 중인 백그라운드 프로세스 또는 서비스가 종료되며, 디바이스의 핵심 구성 요소를 새로 고치거나 특정 시점에서 충돌이 발생한 경우 다시 시작하는 등 여러 문제가 발생할 수 있습니다.
보안 정보가 올바른지 확인합니다. | 보안 인증 방법 정보 특히 전화 번호가 정확한지 확인합니다. 잘못된 전화 번호를 입력한 경우, 모든 경고는 잘못 입력된 해당 전화 번호로 전달됩니다. 다행히도 해당 사용자는 경고를 사용하여 어떤 작업도 수행할 수 없으며 다만 이는 계정에 로그인하는 데에도 도움이 되지 않습니다. 정보가 올바른지 확인하려면 [2단계 인증 방법 설정 관리](multi-factor-authentication-end-user-manage-settings.md) 문서에서 해당 지침을 참조하세요.
알림이 설정되었는지 확인 | 모바일 디바이스에 알림이 설정되어 있는지 확인한 후 전화 통화, 인증 앱 및 메시징 앱(문자 메시지용)이 모바일 디바이스에 표시되는 경고 알림을 전송하도록 허용하는 알림 방법을 선택했는지 확인합니다.
디바이스 신호 및 인터넷 연결 여부 확인 | 전화 통화 및 문자 메시지가 모바일 디바이스에 수신되는지 확인합니다. 친구에게 전화 통화 및 문자 메시지 전송을 부탁해 두 가지 모두 수신되는지 확인합니다. 그렇지 않은 경우, 모바일 디바이스가 켜졌는지 먼저 확인합니다. 디바이스가 켜져 있지만 아직 통화 또는 텍스트를 받지 못하는 경우에는 네트워크에 문제가 있을 가능성이 무엇보다도 높기 때문에 공급자에게 문의해야 합니다. 신호와 관련된 문제들이 종종 발생할 경우, 모바일 디바이스에 [Microsoft Authenticator 앱](user-help-auth-app-download-install.md)을 설치한 후 사용하는 것이 좋습니다. 이 인증자 앱은 셀 신호 또는 인터넷 연결이 없어도 로그인용으로 임의 보안 코드를 생성할 수 있습니다.
방해 금지 해제 | 모바일 디바이스에서 **방해 금지** 기능을 사용하도록 설정하지 않았는지 확인합니다. 이 기능을 켜면 알림이 모바일 디바이스에서 경고하도록 허용되지 않습니다. 이 기능을 해제하는 방법에 관한 지침은 모바일 디바이스의 설명서를 참조하세요.
전화 번호 차단 해제 | 미국에서 Microsoft의 음성 통화는 +1(866) 539 4191, +1(855) 330 8653 및 +1(877) 668 6536에서 발신됩니다.
배터리 관련 설정 확인 | 이는 화면에서 약간 어색해 보이지만 비교적 덜 사용되는 앱이 백그라운드에서 계속 실행되는 것을 중지하도록 배터리 최적화를 설정했다면 그에 따른 영향은 알림 시스템이 받았을 가능성이 무엇보다도 높습니다. 이 문제를 해결하려면 인증 앱 및 메시징 앱에 대한 배터리 최적화를 해제한 후 사용 중인 계정에 다시 로그인해 보세요.
타사 보안 앱 사용 안 함 | 문자 메시지 또는 전화 통화를 보호하여 알 수 없는 호출자를 최소화하는 앱이 있는 경우, 확인 코드를 수신하지 못할 수도 있습니다. 휴대폰에서 타사 보안 앱을 사용하지 않도록 설정한 다음, 다른 확인 코드를 전송하도록 요청하세요.

## <a name="im-not-getting-prompted-for-my-second-verification-information"></a>두 번째 확인 정보를 입력하라는 메시지가 표시되지 않습니다.

사용자 이름 및 암호를 사용하여 회사 또는 학교 계정에 로그인했지만 추가 보안 인증 정보에 대한 메시지가 표시되지 않은 경우, 디바이스를 아직 설정하지 않은 것일 수도 있습니다. 모바일 디바이스는 특정 추가 보안 확인 방법으로 작동하도록 설정해야 합니다. 모바일 디바이스를 사용하도록 설정하고 이 디바이스를 인증 방법으로 사용할 수 있는지 확인하려면 [2단계 인증 방법 설정 관리](multi-factor-authentication-end-user-manage-settings.md) 문서를 참조하세요. 디바이스 또는 계정을 설정하지 않은 경우, [2단계 인증에 대한 계정 설정](multi-factor-authentication-end-user-first-time.md) 문서의 해당 단계를 수행하여 설정을 진행할 수 있습니다.

## <a name="i-got-a-new-phone-number-and-i-want-to-add-it"></a>새 전화 번호를 받았으며 이 번호를 추가하려고 할 경우

새 전화 번호를 받았다면 확인 프롬프트가 올바른 위치로 이동하도록 보안 확인 방법 세부 정보를 업데이트해야 합니다. 인증 방법을 업데이트하려면 [2단계 인증 방법 설정 관리](multi-factor-authentication-end-user-manage-settings.md#add-or-change-your-phone-number) 문서의 **전화 번호 추가 또는 변경** 섹션의 단계를 따라 진행하세요.

## <a name="i-got-a-new-mobile-device-and-i-want-to-add-it"></a>새 모바일 디바이스를 받았으며 이 디바이스를 추가하려는 경우

새 모바일 디바이스를 받았다면 2단계 인증을 사용하도록 디바이스를 설정해야 합니다. 이는 다단계 솔루션에 속합니다.

1. [2단계 인증에 대한 계정 설정](multi-factor-authentication-end-user-first-time.md) 문서의 해당 단계를 수행하여 회사 또는 학교 계정을 사용하도록 디바이스를 설정합니다.

1. **추가 보안 확인** 페이지에서 계정 및 디바이스 정보를 업데이트하고 이전 디바이스를 삭제하며 새 디바이스를 추가합니다. 자세한 내용은 [2단계 인증 방법 설정 관리](multi-factor-authentication-end-user-manage-settings.md) 문서를 참조하세요.

선택적 단계:

- [Microsoft Authenticator 앱 다운로드 및 설치](user-help-auth-app-download-install.md) 문서의 해당 단계에 따라 모바일 디바이스에서 Microsoft Authenticator 앱을 다운로드, 설치 및 설정하세요.

- [2단계 인증 방법 설정 관리](multi-factor-authentication-end-user-manage-settings.md#turn-on-two-factor-verification-prompts-on-a-trusted-device) 문서의 **신뢰할 수 있는 디바이스에서 2단계 인증 프롬프트 켜기** 섹션의 해당 단계에 따라 신뢰할 수 있는 디바이스에 대해 2단계 인증을 설정하세요.

## <a name="im-having-problems-signing-in-on-my-mobile-device-while-traveling"></a>여행 중 모바일 디바이스에 로그인하는 데 문제가 있는 경우

국제 행선지에 있는 동안 문자 메시지와 같은 모바일 디바이스 관련 확인 방법을 사용하는 것이 더 어려울 수 있습니다. 또한 모바일 디바이스에서 로밍 요금이 발생할 수도 있습니다. 이 경우, Wi-Fi 핫스팟에 연결하는 옵션과 함께 Microsoft Authenticator 앱을 사용하는 것이 좋습니다. 모바일 디바이스에서 Microsoft Authenticator 앱 다운로드, 설치 및 설정 방법에 관한 자세한 내용은 [Microsoft Authenticator 앱 다운로드 및 설치](user-help-auth-app-download-install.md) 문서를 참조하세요.

## <a name="i-cant-get-my-app-passwords-to-work"></a>사용할 앱 암호를 수신할 수 없는 경우

2단계 인증을 지원하지 않는 이전 데스크톱 애플리케이션의 경우, 앱 암호가 사용자의 일반 암호를 대체합니다. 먼저 암호를 올바르게 입력했는지 확인합니다. 그래도 문제가 해결되지 않을 경우, [2단계 인증을 위한 앱 암호 관리](multi-factor-authentication-end-user-app-passwords.md#create-and-delete-app-passwords-from-the-additional-security-verification-page) 문서의 **내 앱 포털을 사용하여 앱 암호 만들기 및 삭제** 섹션의 단계에 따라 앱의 새 앱 암호를 만들어 보세요.

## <a name="i-cant-turn-two-factor-verification-off"></a>2단계 인증을 해제할 수 없는 경우

회사 또는 학교 계정(예: alain@contoso.com)에서 2단계 인증을 사용하는 경우, 조직에서는 이 추가된 보안 기능을 사용해야 한다고 판단했을 가능성이 무엇보다도 높습니다. 조직에서 이 기능을 사용해야 한다고 결정했으므로 개별적으로 해제할 수 있는 방법이 없습니다. 그러나 alain@outlook.com과 같은 개인 계정으로 2단계 인증을 사용하는 경우, 해당 기능을 설정 및 해제할 수 있습니다. 개인 계정에서 2단계 인증 제어 방법에 관한 지침은 [Microsoft 계정에 대해 2단계 인증 설정 또는 해제](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off)를 참조하세요.

2단계 인증을 해제할 수 없는 경우, 조직 수준에서 적용된 보안 기본값 때문일 수도 있습니다. 보안 기본값에 관한 자세한 내용은 [보안 기본값이란?](../fundamentals/concept-fundamentals-security-defaults.md)을 참조하세요.

## <a name="i-didnt-find-an-answer-to-my-problem"></a>문제에 대한 답변을 찾을 수 없는 경우

이러한 단계를 시도했지만 여전히 문제가 계속된다면 조직의 지원 센터에 문의하여 도움을 받으세요.

## <a name="related-articles"></a>관련 문서

- [2단계 인증 방법 설정 관리](multi-factor-authentication-end-user-manage-settings.md)

- [2단계 인증에 내 계정 설정](multi-factor-authentication-end-user-first-time.md)

- [Microsoft Authenticator 앱 FAQ](user-help-auth-app-faq.md)
