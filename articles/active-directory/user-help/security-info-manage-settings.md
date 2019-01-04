---
title: 보안 정보 관리 - Azure Active Directory | Microsoft Docs
description: 2단계 인증 설정 작업을 비롯하여 보안 정보를 관리하는 방법을 알아봅니다.
services: active-directory
author: eross-msft
manager: mtillman
ms.reviewer: sahenry
ms.service: active-directory
ms.component: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: lizross
ms.openlocfilehash: 4cd88490118343a9a00dbd919d7820f0334df92e
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/07/2018
ms.locfileid: "53016149"
---
# <a name="manage-your-security-info-preview"></a>보안 정보 관리(미리 보기)

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

보안 정보를 사용하여 회사 또는 학교 계정에 로그인하거나 암호를 재설정할 수 있습니다.

로그인할 때 조직의 설정에 따라 **X일 동안 다시 묻지 않음** 확인란이 표시될 수 있습니다. 이 확인란을 사용하면 다시 확인을 요구하지 않고 관리자가 허용한 일 수 동안 디바이스에 로그인한 상태를 유지할 수 있습니다.

## <a name="change-your-info"></a>정보 변경
관리자 및 조직이 허용하는 사항을 기반으로 보안 정보를 업데이트 또는 추가하거나 기본값을 변경할 수 있습니다.

### <a name="to-change-your-info"></a>정보를 변경하려면

1. 회사 또는 학교 계정에 로그인합니다.

2. myapps.microsoft.com으로 이동하고 페이지 오른쪽 위 모서리에서 이름을 선택한 다음, **프로필**을 선택합니다.

3. **계정 관리** 영역에서 **보안 정보 편집**을 선택합니다.

    ![보안 정보 편집 링크가 강조 표시된 프로필 화면](media/security-info/security-info-profile.png)

4. 기본 메서드를 사용하여 액세스를 승인하고 현재 보안 정보 세부 사항을 확인합니다. 관리자가 조직에 대해 이 환경을 설정한 경우에 가능합니다.

5. **Keep your account secure**(계정 보안 유지) 페이지에서 다음 작업을 수행할 수 있습니다.

    - **보안 정보 추가**를 선택하여 메서드를 더 추가합니다.

    - **기본값 변경**을 선택하여 기본 메서드를 변경합니다.

    - 기본 메서드 옆에 있는 **연필** 아이콘을 선택하여 정보를 업데이트합니다.

    ![편집 가능한 기존 정보가 있는 보안 정보 화면](media/security-info/security-info-edit.png)

6. 변경한 후 페이지를 나가면 변경 내용이 저장됩니다.

이 옵션이 표시되지 않거나 myapps.microsoft.com 페이지에 액세스할 수 없는 경우, 조직에서 사용자 지정 옵션이나 사용자 지정 페이지를 사용할 가능성이 있습니다. 도움이 필요하면 관리자에게 문의해야 합니다.

## <a name="manage-your-security-info-for-a-lost-or-potentially-compromised-device"></a>분실되거나 손상되었을 수 있는 디바이스에 대한 보안 정보 관리

디바이스를 분실하거나 디바이스가 손상되면, 신뢰할 수 있는 이전 디바이스에 대한 확인 프로세스를 모두 다시 실행해야 합니다.

### <a name="to-manage-your-security-info-for-lost-or-potentially-compromised-devices"></a>분실되거나 손상되었을 수 있는 디바이스에 대한 보안 정보를 관리하려면

1. 회사 또는 학교 계정에 로그인합니다.

2. myapps.microsoft.com으로 이동하고 페이지 오른쪽 위 모서리에서 이름을 선택한 다음, **프로필**을 선택합니다.

3. **계정 관리** 영역에서 **Forget MFA on remembered devices**(기억된 디바이스에서 MFA 제거)를 선택합니다.
    
    이 옵션을 선택하면 로그인한 후 MFA(Multi-Factor Authentication) 프로세스를 다시 거쳐야 합니다.

    ![Forget(제거) 링크가 강조 표시된 프로필 화면](media/security-info/security-info-forget.png)

## <a name="common-problems-and-solutions-with-your-security-info"></a>보안 정보의 일반적인 문제 및 솔루션

이 문서는 2단계 인증 관련 문제를 비롯한 보안 정보와 관련된 문제를 해결하는 데 도움이 됩니다.

|문제|해결 방법|
|-------|--------|
|전화기가 없는 경우|휴대폰을 항상 가지고 다니지 않을 수도 있지만 회사 또는 학교 계정에 여전히 로그인이 필요합니다. 이 문제를 해결하려면 휴대폰이 필요 없는 다른 인증 메서드(예: 회사 전화 번호)을 사용하여 로그인할 수 있습니다. 보안 정보에 메서드를 더 추가하려면 [정보 변경](#change-your-info) 섹션의 단계를 수행합니다.|
|전화를 분실했거나 도난당한 경우|불행히도 휴대폰을 분실하거나 도난 당하는 일이 발생할 수 있습니다. 이런 경우 IT 담당자가 앱 암호를 재설정하고 신뢰할 수 있는 디바이스 목록에서 기억된 모든 디바이스를 지울 수 있도록 조직에 알리는 것이 좋습니다. [분실되거나 손상되었을 수 있는 디바이스에 대한 보안 정보 관리](#manage-your-security-info-for-a-lost-or-potentially-compromised-device) 섹션의 단계를 수행하여 신뢰할 수 있는 디바이스를 잊도록 할 수도 있습니다.|
|새 전화 번호를 받은 경우|이 문제를 해결하는 방법은 두 가지입니다. 이메일과 같이 휴대폰 번호가 필요하지 않은 다른 인증 메서드를 사용하여 로그인하거나 이것이 불가능하면 조직의 IT 담당자에게 문의하여 설정을 지우도록 요청할 수 있습니다. 보안 정보에 메서드를 더 추가하려면 [정보 변경](#change-your-info) 섹션의 단계를 수행합니다.|
|기본 메서드가 틀린 경우|보안 옵션에서 기본 메서드를 업데이트할 수 있습니다. 구체적인 정보를 보려면 [정보 변경](#change-your-info) 섹션으로 이동하세요.|
|모바일 디바이스에서 문자나 전화를 받지 못하는 경우|이전에 모바일 디바이스에서 문자나 전화를 성공적으로 수신한 경우에는, 계정이 아니라 전화 공급자 관련 문제일 가능성이 높습니다. 휴대폰 신호가 양호한지 문자 메시지와 전화를 받을 수 있는지 확인하십시오. 테스트 삼아 친구에게 전화를 걸거나 문자를 보내달라고 요청할 수 있습니다.<br><br>문자 및 전화 메시지를 성공적으로 수신할 수 있지만 알림은 여전히 받지 못하는 경우에는 다른 메서드를 사용해 볼 수 있습니다. [정보 변경](#change-your-info) 섹션의 단계를 수행하여 보안 정보에 메서드를 더 추가할 수 있습니다. 추가할 다른 메서드가 없으면 회사 지원팀에 문의하여 설정을 지우도록 요청하고 다음에 로그인할 때 메서드를 설정할 수 있습니다.<br><br>휴대폰 신호 수신 불량으로 인해 문제가 자주 발생하는 경우 모바일 디바이스에서 Microsoft Authenticator 앱을 사용하는 것이 좋습니다. 앱은 로그인하는 데 사용할 수 있는 임의의 보안 코드를 생성할 수 있고 이러한 코드는 셀 신호 또는 인터넷 연결이 필요하지 않습니다. Microsoft Authenticator 앱에 대한 자세한 내용은 [Microsoft Authenticator 앱 시작](https://docs.microsoft.com/azure/multi-factor-authentication/end-user/microsoft-authenticator-app-how-to) 문서를 참조하세요.|
|이 테이블의 옵션으로 문제가 해결되지 않는 경우|이러한 문제 해결 단계를 시도했는데도 문제가 계속되는 경우 귀사의 지원팀에 문의하면 도움을 받을 수 있습니다.|

## <a name="next-steps"></a>다음 단계

- [보안 정보(미리 보기) 개요](user-help-security-info-overview.md)에서 보안 정보에 대해 자세히 알아봅니다.

- [2단계 인증 개요](user-help-two-step-verification-overview.md) 문서에서 2단계 인증에 대해 알아봅니다. 

- 보안 정보 영역에서 디바이스를 설정하는 방법에 대해 알아 보려면 다음 방법 문서 중 하나를 참조하세요.

    - [인증 앱을 사용하도록 보안 정보 설정](security-info-setup-auth-app.md)

    - [전화 통화를 사용하도록 보안 정보 설정](security-info-setup-phone-number.md)

    - [문자 메시지를 사용하도록 보안 정보 설정](security-info-setup-text-msg.md)

    - [이메일을 사용하도록 보안 정보 설정](security-info-setup-email.md)

    - [본인 확인 질문을 사용하도록 보안 정보 설정](security-info-setup-questions.md)

- 분실했거나 잊어버린 경우 [암호 재설정 포털](https://passwordreset.microsoftonline.com/)에서 암호를 다시 설정하거나 [회사 또는 학교 암호 재설정](user-help-reset-password.md) 문서의 단계를 수행합니다.

- [Microsoft 계정에 로그인할 수 없는 경우](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) 문서에서 로그인 문제에 대한 문제 해결 팁 및 도움말을 확인합니다.