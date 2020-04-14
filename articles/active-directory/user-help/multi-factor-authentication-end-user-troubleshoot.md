---
title: 계정 2단계 인증의 일반적인 문제 - Azure AD
description: 몇 가지 일반적인 이중 확인 문제와 직장 또는 학교 계정에 대한 솔루션입니다.
services: active-directory
author: curtand
manager: daveba
ms.assetid: 8f3aef42-7f66-4656-a7cd-d25a971cb9eb
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: conceptual
ms.date: 04/02/2020
ms.author: curtand
ms.reviewer: kexia
ms.openlocfilehash: 3df79fb1e103ddfaafeb348641e675cd250f5858
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81271695"
---
# <a name="common-problems-with-two-factor-verification-and-your-work-or-school-account"></a>2단계 인증 및 직장 또는 학교 계정의 일반적인 문제

Azure Active Directory(Azure AD) 조직이 2단계 인증을 켜면 사용자 이름, 암호 및 모바일 장치 또는 휴대폰을 조합해야 합니다. 암호보다 더 안전하며, 두 가지 형태의 인증, 즉 알고 있는 것과 함께 가지고 있는 것에 의존합니다. 이중 확인은 악의적인 해커가 암호를 가지고 있더라도 기기가 없는 경우가 기때문에 악의적인 해커가 사용자를 가장하는 것을 막을 수 있습니다.

<center>

![개념적 인증 방법 이미지](../authentication/media/concept-mfa-howitworks/methods.png)</center>

우리 중 누구보다 더 자주 발생하는 몇 가지 일반적인 이중 확인 문제가 있습니다. 가장 일반적인 문제와 몇 가지 가능한 수정 사항을 해결하기 위해 이 문서를 정리했습니다.

>[!Important]
>관리자인 경우 [Azure AD 설명서에서](https://docs.microsoft.com/azure/active-directory)Azure AD 환경을 설정하고 관리하는 방법에 대한 자세한 정보를 찾을 수 있습니다.
>
>이 콘텐츠는 조직에서 제공한 계정(예: alain@contoso.com)의 직장 또는 학교 계정과 함께 사용하기 위한 것입니다. 2단계 확인 및 개인 Microsoft 계정(예: 자신을 위해 설정한 danielle@outlook.com계정)에 문제가 있는 경우 Microsoft 계정에 대해 [2단계 확인 켜기 또는 끄기](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off)를 참조하십시오.

## <a name="i-dont-have-my-mobile-device-with-me"></a>모바일 장치가 없는 경우

그것은 일어난다. 모바일 장치를 집에 두고 이제 휴대폰을 사용하여 신원을 확인할 수 없습니다. 이전에 사무실 전화와 같은 계정에 로그인할 수 있는 다른 방법을 추가한 경우 지금 해당 방법을 사용할 수 있습니다. 추가 인증 방법을 추가하지 않은 경우 조직의 헬프 데스크에 문의하여 계정으로 다시 로그인할 수 있도록 도와야 합니다.

### <a name="to-sign-in-to-your-work-or-school-account-using-another-verification-method"></a>다른 인증 방법을 사용하여 직장 또는 학교 계정에 로그인하려면

1. 계정에 로그인하지만 **2단계 인증** 페이지에서 **다른 방법으로 로그인** 링크를 선택합니다.

    ![로그인 인증 방법 변경](./media/multi-factor-authentication-end-user-troubleshoot/two-factor-auth-signin-another-way.png)

    **다른 방법으로 로그인** 링크가 표시되지 않으면 다른 인증 방법을 설정하지 않은 것입니다. 계정에 로그인하는 데 도움이 필요하면 관리자에게 문의해야 합니다.

2. 대체 확인 방법을 선택하고 2단계 확인 프로세스를 계속합니다.

## <a name="i-lost-my-mobile-device-or-it-was-stolen"></a>모바일 장치를 분실했거나 도난당했습니다.

모바일 장치를 분실했거나 도난당한 경우 다른 방법을 사용하여 로그인하거나 조직의 헬프 데스크에 설정을 지우도록 요청할 수 있습니다. 휴대폰을 분실하거나 도난당한 경우 조직의 헬프 데스크에 알리면 계정에 적절한 업데이트를 할 수 있습니다. 설정이 지워지면 다음에 로그인할 때 [2단계 인증에 등록하라는](multi-factor-authentication-end-user-first-time.md) 메시지가 표시됩니다.

## <a name="im-not-getting-the-verification-code-sent-to-my-mobile-device"></a>모바일 기기로 인증 코드를 전송받지 못했습니다.

인증 코드를 받지 못하는 것은 일반적인 문제이며 일반적으로 모바일 장치 및 설정과 관련이 있습니다. 시도할 수 있는 몇 가지 사항:

사용 방법 | 안내 정보
--------- | ------------
모바일 장치 다시 시작 | 경우에 따라 기기를 새로 고치면 됩니다. 장치를 다시 시작하면 현재 실행 중인 백그라운드 프로세스 또는 서비스가 종료되며 장치의 핵심 구성 요소를 새로 고치는 것과 함께 문제가 발생할 수 있으며, 어떤 시점에서 충돌한 경우를 대비하여 다시 시작합니다.
보안 정보가 올바른지 확인 | 보안 확인 방법 정보, 특히 전화번호가 정확한지 확인합니다. 잘못된 전화 번호를 입력하면 모든 경고가 잘못된 번호로 이동합니다. 다행히도 해당 사용자는 경고로 아무 것도 할 수 없지만 계정에 로그인하는 데도 도움이 되지 않습니다. 정보가 정확한지 확인하려면 2단계 확인 방법 설정 관리 문서의 지침을 [참조하세요.](multi-factor-authentication-end-user-manage-settings.md)
알림이 켜져 있는지 확인 | 모바일 기기에 알림이 켜져 있고 전화 통화, 인증 앱 및 메시징 앱(문자 메시지용)을 사용하여 모바일 장치에 표시되는 경고 알림을 보낼 수 있는 알림 방법을 선택했는지 확인합니다.
장치 신호및 인터넷 연결이 있는지 확인 | 전화 통화 및 문자 메시지가 모바일 장치로 전달되는지 확인합니다. 친구가 전화를 걸어 두 가지 를 모두 받을 수 있도록 문자 메시지를 보내도록 합니다. 그렇지 않은 경우 먼저 모바일 장치가 켜져 있는지 확인합니다. 기기가 켜져 있지만 여전히 전화 또는 문자를 받지 못하는 경우 네트워크에 문제가 있을 수 있으므로 공급자와 문의해야 합니다. 종종 신호 관련 문제가 있는 경우 모바일 장치에서 [Microsoft 인증자 앱을](user-help-auth-app-download-install.md) 설치하고 사용하는 것이 좋습니다. 인증자 앱은 셀 신호 나 인터넷 연결없이 로그인을위한 임의의 보안 코드를 생성 할 수 있습니다.
끄기 방해 금지 | 모바일 기기의 **방해 금지** 기능을 켜지 않았는지 확인합니다. 이 기능이 켜져 있으면 모바일 장치에서 알림을 알릴 수 없습니다. 이 기능을 끄는 방법에 대한 지침은 모바일 장치 설명서를 참조하십시오.
전화 번호 차단 해제 | 미국에서 Microsoft의 음성 통화는 +1 (866) 539 4191, +1 (855) 330 8653 및 +1 (877) 668 6536입니다.
배터리 관련 설정 확인 | 이것은 표면에 조금 이상한 것 같다, 하지만 덜 사용 된 애플 리 케이 션 백그라운드에서 활성 남아 에서 중지 배터리 최적화를 설정 한 경우, 알림 시스템은 가장 가능성이 영향을. 이 문제를 해결하려면 인증 앱 및 메시징 앱의 배터리 최적화를 해제한 다음 계정에 다시 로그인해 봅니다.
타사 보안 앱 사용 안 함 | 알 수 없는 발신자를 최소화하기 위해 문자 메시지 또는 전화 통화를 보호하는 앱이 있는 경우 확인 코드가 수신되지 않을 수 있습니다. 휴대폰에서 타사 보안 앱을 사용하지 않도록 설정한 다음 다른 확인 코드 전송을 요청합니다.

## <a name="im-not-getting-prompted-for-my-second-verification-information"></a>두 번째 확인 정보가 표시되지 않습니다.

사용자 이름과 암호를 사용하여 직장 또는 학교 계정에 로그인했지만 추가 보안 확인 정보에 대한 메시지가 표시되지 않은 경우 기기를 아직 설정하지 않았을 수 있습니다. 특정 추가 보안 확인 방법으로 작동하도록 모바일 장치를 설정해야 합니다. 모바일 기기를 사용 중이고 확인 방법으로 사용할 수 있는지 확인 방법 관리 방법 설정 도움말을 [참조하세요.](multi-factor-authentication-end-user-manage-settings.md) 기기 또는 계정을 설정하지 않은 경우 [2단계 인증](multi-factor-authentication-end-user-first-time.md) 에 대한 내 계정 설정 문서의 단계를 수행하여 지금 수행할 수 있습니다.

## <a name="i-got-a-new-phone-number-and-i-want-to-add-it"></a>새 전화번호를 받았는데 추가하고 싶습니다.

새 전화번호를 받은 경우 확인 메시지가 올바른 위치로 이동하도록 보안 확인 방법 세부 정보를 업데이트해야 합니다. 확인 방법을 업데이트하려면 [2단계 확인 방법 설정 관리](multi-factor-authentication-end-user-manage-settings.md#add-or-change-your-phone-number) 문서의 **전화번호 추가 또는 변경** 단계를 따릅니다.

## <a name="i-got-a-new-mobile-device-and-i-want-to-add-it"></a>새 모바일 장치를 가지고 있고 추가하고 싶습니다.

새 모바일 기기를 얻었다면 2단계 인증을 사용하여 작동하도록 설정해야 합니다. 이것은 다단계 솔루션입니다.

1. [2단계 인증](multi-factor-authentication-end-user-first-time.md) 에 대한 내 계정 설정 문서의 단계에 따라 기기가 직장 또는 학교 계정과 함께 작동하도록 설정합니다.

1. **추가 보안 확인** 페이지에서 계정 및 기기 정보를 업데이트하고 이전 기기를 삭제하고 새 기기를 추가합니다. 자세한 내용은 2단계 확인 방법 설정 관리 문서를 [참조하세요.](multi-factor-authentication-end-user-manage-settings.md)

선택적 단계:

- 다운로드 및 Microsoft 인증앱 문서의 단계에 따라 모바일 장치에서 [Microsoft 인증자 앱을 다운로드,](user-help-auth-app-download-install.md) 설치 및 설정합니다.

- [2단계](multi-factor-authentication-end-user-manage-settings.md#turn-on-two-factor-verification-prompts-on-a-trusted-device) 확인 방법 설정 관리 문서의 신뢰할 수 있는 기기 섹션에서 **2단계 확인 설정** 설정 요청의 단계에 따라 신뢰할 수 있는 기기에 대한 2단계 인증을 켭니다.

## <a name="im-having-problems-signing-in-on-my-mobile-device-while-traveling"></a>여행 중 모바일 기기에 로그인하는 데 문제가 있습니다.

국제 적인 위치에 있는 동안 문자 메시지와 같은 모바일 장치 관련 확인 방법을 사용 하 여 더 어려울 수 있습니다. 모바일 기기로 인해 로밍 요금이 발생할 수도 있습니다. 이 경우 Wi-Fi 핫스팟에 연결하는 옵션과 함께 Microsoft 인증자 앱을 사용하는 것이 좋습니다. 모바일 장치에서 Microsoft 인증자 앱을 다운로드, 설치 및 설정하는 방법에 대한 자세한 내용은 [Microsoft 인증자 앱 문서 다운로드 및 설치를](user-help-auth-app-download-install.md) 참조하세요.

## <a name="i-cant-get-my-app-passwords-to-work"></a>앱 비밀번호를 사용할 수 없습니다.

앱 암호는 2단계 인증을 지원하지 않는 이전 데스크톱 응용 프로그램의 일반 암호를 대체합니다. 먼저 암호를 올바르게 입력했는지 확인합니다. 그래도 해결되지 않으면 2단계 인증 아티클에 대한 앱 암호 관리의 내 앱 포털 섹션을 **사용하여 앱 암호 만들기 및 삭제** 단계에 따라 [앱에 대한](multi-factor-authentication-end-user-app-passwords.md#create-and-delete-app-passwords-from-the-additional-security-verification-page) 새 앱 암호를 만들어 보세요.

## <a name="i-cant-turn-two-factor-verification-off"></a>2단계 인증을 해제할 수 없습니다.

직장 이나 학교 alain@contoso.com계정(예:)에서 이중 확인을 사용하는 경우 조직에서 이 추가 보안 기능을 사용해야 한다고 결정했을 가능성이 큽입니다. 조직에서 이 기능을 사용해야 한다고 결정했으므로 개별적으로 해제할 수 있는 방법이 없습니다. 그러나 개인 계정과 같은 alain@outlook.com2단계 인증을 사용하는 경우 기능을 켜고 끌 수 있습니다. 개인 계정에 대한 이중 확인을 제어하는 방법에 대한 지침은 [Microsoft 계정의 2단계 확인 켜기 또는 끄기를](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off)참조하십시오.

이중 확인을 해제할 수 없는 경우 조직 수준에서 적용된 보안 기본값 때문일 수도 있습니다. 보안 기본값에 대한 자세한 내용은 [보안 defulta란 무엇입니까?](../fundamentals/concept-fundamentals-security-defaults.md)

## <a name="i-didnt-find-an-answer-to-my-problem"></a>문제에 대한 답변을 찾을 수 없는 경우

이러한 단계를 시도했지만 여전히 문제가 있는 경우 조직의 헬프 데스크에 문의하여 도움을 받으십시오.

## <a name="related-articles"></a>관련 문서

- [2단계 인증 방법 설정 관리](multi-factor-authentication-end-user-manage-settings.md)

- [2단계 인증에 내 계정 설정](multi-factor-authentication-end-user-first-time.md)

- [Microsoft Authenticator 앱 FAQ](user-help-auth-app-faq.md)
