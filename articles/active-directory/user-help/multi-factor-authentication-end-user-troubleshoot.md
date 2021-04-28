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
ms.date: 09/01/2020
ms.author: curtand
ms.reviewer: kexia
ms.custom: contperf-fy21q1
ms.openlocfilehash: 0cb5e3463eda5d77bbbdefe223954105fd125c71
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98033985"
---
# <a name="common-problems-with-two-factor-verification-and-your-work-or-school-account"></a>2단계 인증 및 회사 또는 학교 계정에 대한 일반적인 문제

몇 가지 일반적인 2단계 인증 문제들은 생각보다 더 자주 발생하는 것처럼 보이기도 합니다. 가장 일반적인 문제에 대한 해결책을 설명하기 위해 이 문서를 함께 제공합니다.

Azure AD(Azure Active Directory) 조직은 사용자 계정에 2단계 인증을 설정할 수 있습니다. 2단계 인증이 설정되어 있으면 다음 데이터를 조합하여 계정에 로그인해야 합니다.

- 사용자 이름
- 사용자 암호
- 모바일 디바이스 또는 전화

2단계 인증은 사용자가 _알고 있는 것_ 과 _휴대하는 것_ 을 사용하기 때문에 단순 암호에 비해 더 안전합니다. 사용자의 실물 전화기를 가지고 있는 해커는 없습니다.

>[!Important]
>관리자의 경우, [Azure AD 문서](../index.yml)에서 Azure AD 환경을 설정하고 관리하는 방법에 대한 자세한 정보를 확인할 수 있습니다.

이 콘텐츠는 조직에서 귀하에게 제공한 계정(예: dritan@contoso.com)에 해당되는 회사 또는 학교 계정과 관련이 있습니다. 직접 설정한 계정(예: danielle@outlook.com)인 개인 Microsoft 계정에 대한 2단계 인증에 문제가 있다면 [Microsoft 계정에 대한 2단계 인증 설정 또는 해제](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off)를 참조하세요.

## <a name="i-dont-have-my-mobile-device-with-me"></a>내 모바일 디바이스가 없습니다.

문제가 발생합니다. 회사에 모바일 디바이스를 놔두고 갔기 때문에 지금은 휴대폰을 사용하여 신원 확인을 할 수 없습니다. 이전에 사무실 전화 등을 통해 계정에 로그인하는 대체 방법을 추가했을 수도 있습니다. 그렇다면 이 대체 방법을 지금 사용할 수 있습니다. 대체 인증 방법을 추가하지 않은 경우 조직의 지원 센터에 문의하여 도움을 받을 수 있습니다.

### <a name="to-sign-in-to-your-work-or-school-account-using-another-verification-method"></a>다른 인증 방법을 사용하여 회사 또는 학교 계정에 로그인

1. 계정에 로그인하고 **2단계 인증** 페이지에서 **다른 방법으로 로그인** 링크를 선택합니다.

    ![로그인 인증 방법 변경](./media/multi-factor-authentication-end-user-troubleshoot/two-factor-auth-signin-another-way.png)

    **다른 방법으로 로그인** 링크가 표시되지 않으면 다른 인증 방법을 설정하지 않은 것입니다. 계정에 로그인하는 데 도움이 필요하면 관리자에게 문의해야 합니다.

2. 대체 인증 방법을 선택하고 2단계 인증 프로세스를 계속합니다.

## <a name="i-cant-turn-two-factor-verification-off"></a>2단계 인증을 해제할 수 없는 경우

- alain@outlook.com과 같은 Microsoft 서비스의 개인 계정으로 2단계 인증을 사용하는 경우, [해당 기능을 설정 및 해제](https://account.live.com/proofs/Manage)할 수 있습니다.

- 회사 또는 학교 계정에서 2단계 인증을 사용하는 경우, 조직에서는 이 추가된 보안 기능을 사용해야 한다고 판단했을 가능성이 무엇보다도 높습니다. 개별적으로 해제할 수 있는 방법은 없습니다.

2단계 인증을 해제할 수 없는 경우, 조직 수준에서 적용된 보안 기본값 때문일 수도 있습니다. 보안 기본값에 관한 자세한 내용은 [보안 기본값이란?](../fundamentals/concept-fundamentals-security-defaults.md)을 참조하세요.

## <a name="my-device-was-lost-or-stolen"></a>디바이스를 분실하거나 도난당함

모바일 디바이스를 분실하거나 도난당한 경우 다음 작업 중 하나를 수행할 수 있습니다.

- 다른 방법을 사용하여 로그인합니다.
- 조직의 지원 센터에 문의하여 설정을 지웁니다.

휴대폰을 분실하거나 도난당한 경우, 조직 지원 센터에 알릴 것을 적극 권장합니다. 그러면 지원 센터에서 계정에 적절한 업데이트를 적용할 수 있습니다. 설정이 지워진 후 다음 번에 로그인할 때 [2단계 인증 등록](multi-factor-authentication-end-user-first-time.md) 메시지가 표시됩니다.

## <a name="im-not-receiving-the-verification-code-sent-to-my-mobile-device"></a>내 모바일 디바이스에 전송된 확인 코드가 수신되지 않음

확인 코드가 수신되지 않는 것은 일반적인 문제입니다. 이 문제는 일반적으로 모바일 디바이스 및 해당 설정과 관련되어 있습니다. 다음은 시도해 볼 수 있는 몇 가지 조치입니다.

사용 방법 | 지침 정보
--------- | ------------
Microsoft Authenticator 앱 또는 확인 코드 사용 | 로그인하는 동안 "확인 전화에 대한 제한에 도달했습니다" 또는 "문자 확인 코드에 대한 제한에 도달했습니다"라는 오류 메시지가 표시됩니다. <br/><br/>Microsoft는 동일한 사용자가 짧은 시간 동안 수행하는 반복적인 인증 시도를 제한할 수 있습니다. Microsoft Authenticator 또는 확인 코드에는 이러한 제한이 적용되지 않습니다. 이러한 제한에 도달하면 Authenticator 앱, 확인 코드를 사용하거나 몇 분 후에 다시 로그인해 보면 됩니다. <br/><br/> 로그인하는 동안 "죄송합니다. 계정을 확인하는 데 문제가 있습니다"라는 오류 메시지가 표시됩니다. <br/><br/> Microsoft는 음성 또는 SMS 인증 시도 실패 횟수가 너무 많을 경우 동일한 사용자, 전화 번호 또는 조직에서 수행하는 음성 또는 SMS 인증 시도를 제한하거나 차단할 수 있습니다. 이 오류가 발생하는 경우 Authenticator 앱 또는 확인 코드와 같은 다른 방법을 시도하거나 관리자에게 문의하여 지원을 받을 수 있습니다.
모바일 디바이스 다시 시작 | 경우에 따라 디바이스에 새로 고침이 필요할 수도 있습니다. 디바이스를 다시 시작하면 모든 백그라운드 프로세스 및 서비스가 종료됩니다. 다시 시작하면 디바이스의 핵심 구성 요소도 종료됩니다. 디바이스를 다시 시작하면 모든 서비스 또는 구성 요소가 새로 고쳐집니다.
보안 정보가 올바른지 확인합니다. | 보안 인증 방법 정보 특히 전화 번호가 정확한지 확인합니다. 잘못된 전화 번호를 입력한 경우, 모든 경고는 잘못 입력된 해당 전화 번호로 전달됩니다. 다행히도 해당 사용자는 경고를 사용하여 어떤 작업도 수행할 수 없으며 다만 이는 계정에 로그인하는 데에도 도움이 되지 않습니다. 정보가 올바른지 확인하려면 [2단계 인증 방법 설정 관리](multi-factor-authentication-end-user-manage-settings.md) 문서에서 해당 지침을 참조하세요.
알림이 설정되었는지 확인 | 모바일 디바이스의 알림이 켜져 있는지 확인합니다. 다음 알림 모드가 허용되는지 확인합니다. <br/><br/> &bull; 전화 통화 <br/> &bull; 인증 앱 <br/> &bull; 문자 메시지 앱 <br/><br/> 이러한 모드에서 디바이스에 _표시_ 되는 경고를 만드는지 확인합니다.
디바이스 신호 및 인터넷 연결 여부 확인 | 전화 통화 및 문자 메시지가 모바일 디바이스에 수신되는지 확인합니다. 친구에게 전화 통화 및 문자 메시지 전송을 부탁해 두 가지 모두 수신되는지 확인합니다. 전화나 문자가 수신되지 않으면 먼저 모바일 디바이스가 켜져 있는지 확인합니다. 디바이스가 켜져 있는데도 전화나 문자가 수신되지 않으면 네트워크에 문제가 있는 것일 수 있습니다. 공급자에게 문의해야 합니다. 신호와 관련된 문제들이 종종 발생할 경우, 모바일 디바이스에 [Microsoft Authenticator 앱](user-help-auth-app-download-install.md)을 설치한 후 사용하는 것이 좋습니다. 이 인증자 앱은 셀 신호 또는 인터넷 연결이 없어도 로그인용으로 임의 보안 코드를 생성할 수 있습니다.
방해 금지 해제 | 모바일 디바이스에서 **방해 금지** 기능을 사용하도록 설정하지 않았는지 확인합니다. 이 기능을 켜면 알림이 모바일 디바이스에서 경고하도록 허용되지 않습니다. 이 기능을 해제하는 방법에 관한 지침은 모바일 디바이스의 설명서를 참조하세요.
전화 번호 차단 해제 | 미국에서 Microsoft의 음성 통화는 +1(866) 539 4191, +1(855) 330 8653 및 +1(877) 668 6536에서 발신됩니다.
배터리 관련 설정 확인 | 자주 사용되지 않는 앱이 백그라운드에 활성 상태로 남아 있지 않게 중지하도록 배터리 최적화를 설정하면 알림 시스템에 영향을 줄 수 있습니다. 인증 앱과 메시징 앱 모두에서 배터리 최적화를 해제해 보세요. 그런 다음, 계정에 다시 로그인하세요.
타사 보안 앱 사용 안 함 | 일부 전화 보안 앱은 알 수 없는 호출자의 성가신 문자 메시지 및 전화 통화를 차단합니다. 보안 앱으로 인해 휴대폰에 확인 코드가 수신되지 않을 수 있습니다. 휴대폰에서 타사 보안 앱을 사용하지 않도록 설정한 다음, 다른 확인 코드를 전송하도록 요청하세요.

## <a name="im-not-being-prompted-for-my-second-verification-information"></a>두 번째 확인 정보를 입력하라는 메시지가 표시되지 않음

사용자 이름 및 암호를 사용하여 회사 또는 학교 계정에 로그인합니다. 그러면 추가 보안 확인 정보를 묻는 메시지가 표시됩니다. 메시지가 표시되지 않으면 디바이스를 아직 설정하지 않은 것일 수 있습니다. 모바일 디바이스는 특정 추가 보안 확인 방법으로 작동하도록 설정해야 합니다.

디바이스를 아직 설정하지 않은 것일 수 있습니다. 모바일 디바이스에 특정 추가 보안 확인 방법을 사용하도록 설정해야 합니다. 모바일 디바이스에 인증 방법을 사용하도록 설정하는 단계는 [2단계 인증 방법 설정 관리](multi-factor-authentication-end-user-manage-settings.md)를 참조하세요. 디바이스 또는 계정을 아직 설정하지 않은 경우, [2단계 인증에 대한 계정 설정](multi-factor-authentication-end-user-first-time.md) 문서의 단계를 따라 설정을 진행할 수 있습니다.

## <a name="i-have-a-new-phone-number-and-i-want-to-add-it"></a>새 전화 번호를 받았으며 이 번호를 추가하려고 함

새 전화 번호를 받은 경우 보안 확인 방법 세부 정보를 업데이트해야 합니다. 그러면 확인 메시지가 올바른 위치로 이동할 수 있습니다. 인증 방법을 업데이트하려면 [2단계 인증 방법 설정 관리](multi-factor-authentication-end-user-manage-settings.md#add-or-change-your-phone-number) 문서의 **전화 번호 추가 또는 변경** 섹션의 단계를 따라 진행하세요.

## <a name="i-have-a-new-mobile-device-and-i-want-to-add-it"></a>새 모바일 디바이스가 있으며 이 디바이스를 추가하려는 경우

새 모바일 디바이스가 있다면 2단계 인증을 사용하도록 디바이스를 설정해야 합니다. 이는 다단계 솔루션에 속합니다.

1. [2단계 인증에 대한 계정 설정](multi-factor-authentication-end-user-first-time.md) 문서의 해당 단계를 수행하여 계정을 사용하도록 디바이스를 설정합니다.

1. **추가 보안 확인** 페이지에서 계정 및 디바이스 정보를 업데이트합니다. 이전 디바이스를 삭제하고 새 디바이스를 추가하여 업데이트를 수행합니다. 자세한 내용은 [2단계 인증 방법 설정 관리](multi-factor-authentication-end-user-manage-settings.md) 문서를 참조하세요.

선택적 단계:

- [Microsoft Authenticator 앱 다운로드 및 설치](user-help-auth-app-download-install.md) 문서의 해당 단계에 따라 모바일 디바이스에 Microsoft Authenticator 앱을 설치합니다.

- [2단계 인증 방법 설정 관리](multi-factor-authentication-end-user-manage-settings.md#turn-on-two-factor-verification-prompts-on-a-trusted-device) 문서의 **신뢰할 수 있는 디바이스에서 2단계 인증 프롬프트 켜기** 섹션의 해당 단계에 따라 신뢰할 수 있는 디바이스에 대해 2단계 인증을 설정하세요.

## <a name="im-having-problems-signing-in-on-my-mobile-device-while-traveling"></a>여행 중 모바일 디바이스에 로그인하는 데 문제가 있는 경우

국제 행선지에 있는 동안 문자 메시지와 같은 모바일 디바이스 관련 확인 방법을 사용하는 것이 더 어려울 수 있습니다. 또한 모바일 디바이스에서 로밍 요금이 발생할 수도 있습니다. 이 경우, Wi-Fi 핫스팟에 연결하는 옵션과 함께 Microsoft Authenticator 앱을 사용하는 것이 좋습니다. 모바일 디바이스에서 Microsoft Authenticator 앱을 설정하는 방법에 관한 자세한 내용은 [Microsoft Authenticator 앱 다운로드 및 설치](user-help-auth-app-download-install.md) 문서를 참조하세요.

## <a name="i-cant-get-my-app-passwords-to-work"></a>사용할 앱 암호를 수신할 수 없는 경우

2단계 인증을 지원하지 않는 이전 데스크톱 애플리케이션의 경우, 앱 암호가 사용자의 일반 암호를 대체합니다. 먼저 암호를 올바르게 입력했는지 확인합니다. 그래도 문제가 해결되지 않을 경우 앱의 새 앱 암호를 만들어 보세요. [2단계 인증을 위한 앱 암호 관리](multi-factor-authentication-end-user-app-passwords.md#create-and-delete-app-passwords-from-the-additional-security-verification-page) 문서의 **내 앱 포털을 사용하여 앱 암호 만들기 및 삭제** 섹션의 단계를 따르면 됩니다.

## <a name="i-cant-turn-off-two-factor-verification"></a>2단계 인증을 해제할 수 없음

회사 또는 학교 계정(예: alain@contoso.com)에서 2단계 인증을 사용하는 경우, 조직에서는 이 추가된 보안 기능을 사용해야 한다고 판단했을 가능성이 무엇보다도 높습니다. 조직에서 이 기능을 사용해야 한다고 결정했으므로 개별적으로 해제할 수 있는 방법이 없습니다. 그러나 alain@outlook.com과 같은 개인 계정으로 2단계 인증을 사용하는 경우, 해당 기능을 설정 및 해제할 수 있습니다. 개인 계정에서 2단계 인증 제어 방법에 관한 지침은 [Microsoft 계정에 대해 2단계 인증 설정 또는 해제](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off)를 참조하세요.

2단계 인증을 해제할 수 없는 경우, 조직 수준에서 적용된 보안 기본값 때문일 수도 있습니다. 보안 기본값에 관한 자세한 내용은 [보안 기본값이란?](../fundamentals/concept-fundamentals-security-defaults.md)을 참조하세요.

## <a name="i-didnt-find-an-answer-to-my-problem"></a>문제에 대한 답변을 찾을 수 없는 경우

이러한 단계를 시도했지만 여전히 문제가 계속된다면 조직의 지원 센터에 문의하여 도움을 받으세요.

## <a name="related-articles"></a>관련 문서

- [2단계 인증 방법 설정 관리](multi-factor-authentication-end-user-manage-settings.md)

- [2단계 인증에 내 계정 설정](multi-factor-authentication-end-user-first-time.md)

- [Microsoft Authenticator 앱 FAQ](user-help-auth-app-faq.md)
