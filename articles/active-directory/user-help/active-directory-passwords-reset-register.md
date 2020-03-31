---
title: 인증 정보를 등록하여 사용자 고유의 암호를 재설정합니다 - Azure AD
description: Azure AD 셀프 서비스 암호 재설정에 대한 확인 방법 정보를 등록하여 관리자의 도움 없이 자신의 암호를 재설정할 수 있습니다.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: sahenry
ms.assetid: ''
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 01/15/2020
ms.author: curtand
ms.openlocfilehash: 397e1fd7695fd7e74e1f22959d0f9f24af7d1ea3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77062645"
---
# <a name="register-your-verification-method-info-to-reset-your-own-password"></a>확인 방법 정보를 등록하여 자신의 비밀번호를 재설정하십시오.

회사 또는 학교 암호가 생각 나지 않거나, 조직으로부터 암호를 받은 적이 없거나, 계정이 잠긴 경우 보안 정보 및 모바일 디바이스를 사용하여 회사 또는 학교 암호를 재설정할 수 있습니다.

관리자는 정보를 등록하고 자신의 암호를 재설정할 수 있도록 이 기능을 켜야 합니다. **비밀번호 잊어버린 옵션(IdFor I)이** 표시되지 않으면 관리자가 조직의 기능을 켜지 않았음을 의미합니다. 이 기능이 필요하다고 생각하시는 경우 지원 센터에 도움을 요청하세요.

>[!Important]
>이 문서는 셀프 서비스 암호 재설정에 등록을 사용하려는 사용자를 위한 것입니다. 즉, 관리자의 도움 없이도 자신의 직장 또는 학교 비밀번호를 alain@contoso.com재설정할 수 있습니다. 직원 또는 다른 사용자에 대해 셀프 서비스 암호 재설정을 설정하는 방법에 대한 정보를 찾는 관리자인 경우 [Azure AD 셀프 서비스 암호 재설정 및 기타 문서를](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-deployment)참조하십시오.

## <a name="set-up-your-password-reset-verification-method"></a>비밀번호 재설정 확인 방법 설정

1. 장치에서 웹 브라우저를 열고 보안 [정보 페이지로](https://account.activedirectory.windowsazure.com/PasswordReset/Register.aspx?regref=ssprsetup)이동합니다.

2. 관리자가 조직을 설정한 방법에 따라 다음 옵션 중 하나 이상을 보안 확인 방법으로 설정할 수 있습니다. 여러 옵션을 사용할 수 있는 경우 방법 중 하나를 사용할 수 없는 경우 를 대비하여 둘 이상의 옵션을 보안 확인 방법으로 사용하는 것이 좋습니다.

    - **인증 응용 프로그램.** Microsoft 인증자 앱 또는 기타 인증자 앱을 보안 확인 방법으로 사용하도록 선택합니다. 앱 설정에 대한 자세한 내용은 [Microsoft 인증자 앱을 확인 방법으로 설정합니다.](security-info-setup-auth-app.md)

    - **문자 메시지.** 모바일 장치에 자신에게 문자 메시지를 보낼 수 있습니다. 문자 메시지 설정에 대한 자세한 내용은 [문자 메시지 설정을 확인 방법으로 설정합니다.](security-info-setup-text-msg.md)

    - **전화.** 등록된 전화번호로 전화를 받도록 선택합니다. 전화 통화 설정에 대한 자세한 내용은 [전화 번호 설정을 확인 방법으로 설정합니다.](security-info-setup-phone-number.md)

    - **보안 키.** Microsoft 호환 보안 키를 사용하도록 선택합니다. 자세한 내용은 [보안 키 를 확인 방법으로 설정 을](security-info-setup-security-key.md)참조하십시오.

    - **이메일 주소입니다.** 잊어버리거나 누락된 암호를 요구하지 않고 사용할 수 있는 대체 이메일 주소를 사용하도록 선택합니다. 이는 보안 확인 방법이 아니라 암호 재설정에만 작동합니다. 이메일 주소 설정에 대한 자세한 내용은 [이메일 주소 설정을 확인 방법으로 설정합니다.](security-info-setup-email.md)

    - **보안 질문.** 관리자가 설정한 미리 정의된 보안 질문을 설정하고 답변하도록 선택합니다. 이는 보안 확인 방법이 아니라 암호 재설정에만 작동합니다. 보안 질문에 대한 자세한 내용은 [보안 질문을 확인 방법으로 설정 참조하세요.](security-info-setup-questions.md)

3. 메서드를 선택하고 설정한 후 **완료를** 선택하여 프로세스를 완료합니다.

    > [!Note]
    > 전화 번호 또는 이메일 주소에 추가된 정보는 조직의 글로벌 디렉터리와 공유되지 않습니다. 이 정보를 볼 수 있는 사람은 사용자및 관리자뿐입니다. 보안 질문에 대한 대답만 볼 수 있습니다.

## <a name="common-problems-and-their-solutions"></a>일반적인 문제 및 해결 방법

 다음은 일반적인 오류 사례 및 해결 방법입니다.

| 오류 메시지 |  가능한 해결 방법 |
| --- | --- | --- |
| 관리자에게 문의하세요.<br>Microsoft에서 사용자 계정 암호가 관리되지 않는 것이 감지되었습니다. 결과적으로 자동으로 암호를 재설정할 수 없습니다.<br>IT 직원에게 추가 지원을 요청하세요.| 사용자 ID를 입력한 후 이 오류 메시지가 표시되면 조직에서 내부적으로 암호를 관리하고 계정 링크에 액세스할 수 **없습니다에서** 암호를 재설정하지 않으려는 의미입니다. 이 상황에서 암호를 재설정하려면 조직의 헬프 데스크 또는 관리자에게 도움을 요청해야 합니다. |
| 암호 재설정을 위해 계정을 사용할 수 없습니다.<br>죄송하지만 IT 직원이 계정을 이 서비스와 함께 사용하도록 설정하지 않습니다.<br>원하는 경우 사용자의 조직에서 관리자에게 연락하여 사용자용 암호를 재설정할 수 있습니다. | 사용자 ID를 입력한 후 이 오류 메시지가 발생하면 조직에서 암호 재설정 기능을 켜지 않았거나 사용할 수 없다는 의미입니다. 이 상황에서 암호를 재설정하려면 **관리자 연락처** 링크를 선택해야 합니다. 링크를 클릭하면 조직의 헬프 데스크 또는 관리자에게 암호를 재설정할 수 있다는 전자 메일이 전송됩니다. |
| 계정을 확인하지 못했습니다.<br>원하는 경우 사용자의 조직에서 관리자에게 연락하여 사용자용 암호를 재설정할 수 있습니다. | 사용자 ID를 입력한 후 이 오류 메시지가 발생하면 조직에서 암호 재설정을 설정했으며 사용할 수 있지만 서비스에 등록하지 않았음을 의미합니다. 이 경우 조직의 헬프 데스크 또는 관리자에게 문의하여 암호를 재설정해야 합니다. 기기에 돌아온 후 암호 재설정에 등록하는 방법에 대한 자세한 내용은 이 문서의 위의 프로세스를 참조하십시오. |

## <a name="next-steps"></a>다음 단계

- [셀프 서비스 암호 재설정을 사용하여 암호 변경](active-directory-passwords-update-your-own-password.md)

- [보안 정보 페이지](https://mysignins.microsoft.com/security-info)

- [암호 재설정 포털](https://passwordreset.microsoftonline.com/)

- [Microsoft 계정에 로그인할 수 없는 경우](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)
