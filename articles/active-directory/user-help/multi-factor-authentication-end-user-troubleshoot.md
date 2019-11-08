---
title: 일반적인 2 단계 인증 문제 해결-Azure AD
description: 몇 가지 일반적인 2 단계 인증 문제에 대 한 가능한 해결 방법에 대해 알아봅니다.
services: active-directory
author: eross-msft
manager: daveba
ms.assetid: 8f3aef42-7f66-4656-a7cd-d25a971cb9eb
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: conceptual
ms.date: 10/28/2019
ms.author: lizross
ms.reviewer: kexia
ms.collection: M365-identity-device-management
ms.openlocfilehash: f8d226d61f4b67541a6991fef3a435b4cfff500f
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73805294"
---
# <a name="troubleshoot-common-two-factor-verification-problems"></a>일반적인 2 단계 인증 문제 해결

조직에서 2 단계 인증을 켜면 회사 또는 학교 계정 로그인에 사용자 이름, 암호 및 모바일 장치 또는 휴대폰의 조합이 필요 합니다. 암호 자체보다 더 안전하므로 조직에서 이 추가 확인을 설정했습니다. 이에 따라 두 가지 인증 형식, 즉 사용자가 알고 있는 인증 형식과 사용자 전용의 인증 형식을 사용합니다. 2단계 인증은 악의적인 해커가 사용자를 가장하지 못하게 차단하는 효과가 있습니다. 해커가 사용자의 암호를 알아내더라도 사용자의 디바이스까지 손에 넣을 가능성은 별로 높지 않기 때문입니다.

어떤 경우에는 보다 자주 발생 하는 것 처럼 보이는 몇 가지 일반적인 2 단계 확인 문제가 있습니다. 이 문서를 통합 하 여 가장 일반적인 문제 및 가능한 해결 방법에 대해 설명 했습니다.

>[!Important]
>이 콘텐츠는 사용자를 위한 것입니다. 관리자의 경우 [Azure Active Directory 문서](https://docs.microsoft.com/azure/active-directory)에서 Azure AD(Azure Active Directory) 환경을 설정하고 관리하는 방법에 대한 자세한 정보를 찾을 수 있습니다.
>
>이 콘텐츠는 회사 또는 학교 계정, 조직에서 귀하에게 제공한 계정(예: alain@contoso.com)에만 사용할 수 있습니다. 2단계 인증과 개인 Microsoft 계정, 직접 설정한 계정(예: danielle@outlook.com)에 문제가 있으면 [Microsoft 계정에 대한 2단계 인증 설정 또는 해제](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off)를 참조하세요.

## <a name="i-forgot-my-mobile-device-at-home"></a>집에서 모바일 장치를 잊은 경우

오류가 발생 합니다. 회사에서 모바일 장치를 떠난 후 지금은 휴대폰을 사용 하 여 사용자가 누구 인지 확인할 수 없습니다. 이전에 사무실 전화와 같이 계정에 로그인 하는 다른 방법을 추가한 경우에는이 방법을 사용할 수 있습니다. 추가 인증 방법을 추가 하지 않은 경우 지원 센터에 문의 하 여 사용자의 계정으로 돌아갈 수 있도록 해야 합니다.

### <a name="to-sign-in-to-your-work-or-school-account-using-another-verification-method"></a>다른 인증 방법을 사용 하 여 회사 또는 학교 계정에 로그인 하려면

1. 계정에 일반적으로 로그인 하 고 **2 단계 인증** 페이지에서 **다른 방식으로 로그인** 링크를 선택 합니다.

    ![로그인 인증 방법 변경](./media/multi-factor-authentication-end-user-troubleshoot/two-factor-auth-signin-another-way.png)

    >[!Note]
    >**다른 방법으로 로그인** 링크가 표시되지 않으면 다른 인증 방법을 설정하지 않은 것입니다. 계정에 로그인하는 데 도움이 필요하면 관리자에게 문의해야 합니다.

2. 대체 확인 방법을 선택 하 고 2 단계 인증 프로세스를 계속 합니다.

## <a name="i-lost-my-mobile-device-or-it-was-stolen"></a>모바일 장치를 분실 했거나 도난당 한 경우

모바일 장치를 분실 했거나 도난당 한 경우 다른 방법을 사용 하 여 로그인 하거나 지원 센터에 문의 하 여 설정을 지울 수 있습니다. 휴대폰을 분실 하거나 도난당 한 경우 지원 센터에 알리는 것이 좋습니다. 따라서 계정에 적절 한 업데이트를 적용할 수 있습니다. 설정이 취소 된 후 다음에 로그인 할 때 [2 단계 인증을 등록](multi-factor-authentication-end-user-first-time.md) 하 라는 메시지가 표시 됩니다.

## <a name="im-not-getting-the-verification-code-sent-to-my-mobile-device"></a>내 모바일 장치에 전송 된 확인 코드를 얻지 않습니다.

확인 코드를 가져오지 않는 것은 일반적인 문제 이며 일반적으로 모바일 장치 및 해당 설정과 관련 되어 있습니다. 시도할 수 있는 몇 가지 작업은 다음과 같습니다.

- **모바일 장치를 다시 시작 합니다.** 경우에 따라 장치에 새로 고침이 필요할 수도 있습니다. 장치를 다시 시작 하면 현재 실행 중인 모든 백그라운드 프로세스나 서비스가 종료 되 고 장치의 핵심 구성 요소를 새로 고치는 것과 함께 문제를 발생 시킬 수 있습니다.

- **보안 정보가 올바른지 확인 합니다.** 보안 확인 방법 정보가 정확한 지, 특히 전화 번호 인지 확인 합니다. 잘못 된 전화 번호를 입력 하는 경우 모든 경고는 해당 하는 잘못 된 번호로 이동 합니다. 다행히 해당 사용자는 경고를 사용 하 여 어떤 작업을 수행할 수 없지만 계정에 로그인 하는 데도 도움이 되지 않습니다. 정보가 올바른지 확인 하려면 [2 단계 인증 방법 설정 관리](multi-factor-authentication-end-user-manage-settings.md) 문서의 지침을 참조 하세요.

- **알림이 설정 되었는지 확인 합니다.** 모바일 장치에 알림이 설정 되어 있고 전화 통화, 인증 앱 및 메시징 앱 (문자 메시지)이 모바일 장치에 표시 되는 경고 알림을 보내도록 허용 하는 알림 방법을 선택 했는지 확인 합니다.

- **장치 신호 및 인터넷 연결이 있는지 확인 합니다.** 휴대폰 통화 및 문자 메시지가 모바일 장치에 도달 하는지 확인 합니다. 친구에 게 전화를 걸어 문자 메시지를 보내 두 메시지를 모두 수신 하는지 확인 합니다. 그렇지 않은 경우 먼저 모바일 장치를 켜 졌는 지 확인 합니다. 장치가 켜져 있지만 아직 통화 또는 텍스트를 받지 못하는 경우에는 네트워크에 문제가 있을 수 있으므로 공급자에 게 문의 해야 합니다. 신호 관련 문제가 자주 발생 하는 경우 모바일 장치에 [Microsoft Authenticator 앱](user-help-auth-app-download-install.md) 을 설치 하 고 사용 하는 것이 좋습니다. 인증자 앱은 셀 신호 또는 인터넷 연결을 요구 하지 않고 로그인에 대 한 임의의 보안 코드를 생성할 수 있습니다.

- **방해 금지를 해제 합니다.** 모바일 장치에 대 한 **방해** 금지 기능을 설정 하지 않았는지 확인 합니다. 이 기능을 켜면 알림이 모바일 장치에서 경고 하도록 허용 되지 않습니다. 이 기능을 해제 하는 방법에 대 한 지침은 모바일 장치의 설명서를 참조 하세요.

- **전화 번호 차단 해제** 미국에서 Microsoft의 음성 통화는 + 1 (866) 539 4191, + 1 (855) 330 8653 및 + 1 (877) 668 6536 번호에서 제공 됩니다.

- **배터리 관련 설정을 확인 하세요.** 이는 화면에서 약간 이상한 것 처럼 보이지만, 백그라운드에서 사용 되는 사용 되지 않는 응용 프로그램을 중지 하도록 배터리 최적화를 설정한 경우 알림 시스템에 영향을 줄 가능성이 높습니다. 이 문제를 해결 하려면 인증 앱 및 메시징 앱에 대 한 배터리 최적화를 해제 한 다음 계정에 다시 로그인 해 보세요.

## <a name="im-not-getting-prompted-for-my-second-verification-information"></a>두 번째 확인 정보를 입력 하 라는 메시지가 표시 되지 않습니다.

사용자 이름 및 암호를 사용 하 여 회사 또는 학교 계정에 로그인 했지만 추가 보안 인증 정보에 대 한 메시지가 표시 되지 않은 경우 장치를 아직 설정 하지 않은 것일 수 있습니다. 모바일 장치는 특별히 추가 보안 확인 방법으로 작동 하도록 설정 해야 합니다. 모바일 장치를 켜고 확인 방법으로 사용할 수 있는지 확인 하려면 [2 단계 인증 방법 설정 관리](multi-factor-authentication-end-user-manage-settings.md) 문서를 참조 하세요. 장치 또는 계정을 설정 하지 않은 경우 [2 단계 인증을 위한 내 계정 설정](multi-factor-authentication-end-user-first-time.md) 문서의 단계를 수행 하 여이 작업을 수행할 수 있습니다.

## <a name="i-got-a-new-phone-number-how-do-i-change-it-for-two-factor-verification"></a>새 전화 번호가 있고, 2 단계 인증을 위해 변경 하려면 어떻게 해야 하나요?

새 전화 번호를 받은 경우 확인 메시지가 올바른 위치로 이동 하도록 보안 확인 방법 세부 정보를 업데이트 해야 합니다. 확인 방법을 업데이트 하려면 [2 단계 인증 방법 설정 관리](multi-factor-authentication-end-user-manage-settings.md#add-or-change-your-phone-number) 문서의 단계에 따라 **전화 번호 추가 또는 변경** 섹션을 참조 하세요.

## <a name="i-got-a-new-mobile-device-how-do-i-add-it"></a>새 모바일 장치를 추가 하려면 어떻게 해야 하나요?

새 모바일 장치를 사용 하는 경우 2 단계 인증을 사용 하도록 설정 해야 합니다. 이는 여러 단계 솔루션입니다.

1. [2 단계 인증을 위한 내 계정 설정](multi-factor-authentication-end-user-first-time.md) 문서에 있는 단계에 따라 회사 또는 학교 계정으로 작업 하도록 장치를 설정 합니다.

2. **추가 보안 인증** 페이지에서 이전 장치를 삭제 하 고 새 장치를 추가 하 여 계정 및 장치 정보를 업데이트 합니다. 자세한 내용은 [2 단계 인증 방법 관리 설정](multi-factor-authentication-end-user-manage-settings.md) 문서를 참조 하세요.

3. 선택 사항입니다. [Microsoft Authenticator 앱 다운로드 및 설치](user-help-auth-app-download-install.md) 문서의 단계를 수행 하 여 모바일 장치에 Microsoft Authenticator 앱을 다운로드 하 고 설치 하 고 설정 합니다.

4. 선택 사항입니다. [2 단계 인증 방법 설정 관리](multi-factor-authentication-end-user-manage-settings.md#turn-on-two-factor-verification-prompts-on-a-trusted-device) 문서의 **신뢰할 수 있는 장치에 대 한 2 단계 확인 프롬프트 켜기** 섹션의 단계에 따라 신뢰할 수 있는 장치에 대 한 2 단계 인증을 설정 합니다.

## <a name="im-having-problems-signing-in-on-my-mobile-device-while-traveling"></a>여행 하는 동안 모바일 장치에 로그인 하는 데 문제가 있음

국제 위치에 있는 동안 텍스트 메시징과 같은 모바일 장치 관련 확인 방법을 사용 하는 것이 더 어려울 수 있습니다. 또한 모바일 장치에서 로밍 요금을 발생 시킬 수 있습니다. 이 경우 Wi-fi 핫스팟에 연결 하는 옵션과 함께 Microsoft Authenticator 앱을 사용 하는 것이 좋습니다. 모바일 장치에서 Microsoft Authenticator 앱을 다운로드 하 고 설치 하 고 설정 하는 방법에 대 한 자세한 내용은 [Microsoft Authenticator 앱 다운로드 및 설치](user-help-auth-app-download-install.md) 문서를 참조 하세요.

## <a name="i-cant-get-my-app-passwords-to-work"></a>앱 암호를 사용할 수 없습니다.

앱 암호는 2 단계 인증을 지원 하지 않는 이전 데스크톱 응용 프로그램에 대 한 일반 암호를 대체 합니다. 먼저 암호를 올바르게 입력했는지 확인합니다. 이 문제가 해결 되지 않으면 [2 단계 인증에 대 한 앱 암호 관리](multi-factor-authentication-end-user-app-passwords.md#create-and-delete-app-passwords-from-the-additional-security-verification-page) 문서의 단계에 따라 앱 암호 **만들기 및 삭제** 의 단계에 따라 앱에 대 한 새 앱 암호를 만들어 보세요.

## <a name="why-cant-i-turn-two-factor-verification-off"></a>2 단계 인증을 해제할 수 없는 이유는 무엇입니까?

회사 또는 학교 계정 (예: alain@contoso.com)에서 2 단계 인증을 사용 하는 경우이 추가 된 보안 기능을 사용 해야 하는 경우가 많습니다. 조직에서 이 기능을 사용해야 한다고 결정했으므로 개별적으로 해제할 수 있는 방법이 없습니다. 그러나 alain@outlook.com와 같은 개인 계정으로 2 단계 인증을 사용 하는 경우 기능을 켜고 끌 수 있습니다. 개인 Microsoft 계정에 대 한 2 단계 인증을 제어 하는 방법에 대 한 지침은 [Microsoft 계정에 대해 2 단계 인증 설정 또는 해제](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off)를 참조 하세요.

## <a name="i-didnt-find-an-answer-to-my-problem"></a>문제에 대한 답변을 찾을 수 없는 경우

이러한 단계를 시도해도 여전히 문제가 발생 하는 경우 지원 센터에 문의 하십시오.

## <a name="related-articles"></a>관련된 문서

- [2단계 인증 방법 설정 관리](multi-factor-authentication-end-user-manage-settings.md)

- [2 단계 인증을 위한 계정 설정](multi-factor-authentication-end-user-first-time.md)

- [Microsoft Authenticator 앱 FAQ](user-help-auth-app-faq.md)
