---
title: 인증자 앱을 사용하도록 보안 정보 설정 - Azure Active Directory | Microsoft Docs
description: Microsoft Authenticator 앱을 사용하여 본인 여부를 확인하도록 보안 정보를 설정합니다.
services: active-directory
author: eross-msft
manager: mtillman
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.component: user-help
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: lizross
ms.openlocfilehash: e7b07ba892f8f904b1b2127fa8e76649eb004388
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2018
ms.locfileid: "42143102"
---
# <a name="set-up-security-info-to-use-an-authenticator-app-preview"></a>인증자 앱을 사용하도록 보안 정보 설정(미리 보기)

[!INCLUDE[preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

보안 정보를 설정하면 회사 또는 학교 계정에 로그인한 다음, 등록 프로세스를 완료해야 합니다. 보안 정보를 설정하지 않은 경우 지금 수행할 것인지 묻는 메시지가 표시됩니다.

## <a name="set-up-the-microsoft-authenticator-app"></a>Microsoft Authenticator 앱 설정

조직의 설정에 따라 로그인할 때 Microsoft Authenticator 앱을 설정하라는 메시지가 표시될 수 있습니다. 그렇지 않은 경우 보안 정보에서 Microsoft Authenticator 앱 설정을 시작하려면 [보안 정보 관리](security-info-manage-settings.md)의 단계를 따릅니다.

Microsoft Authenticator 앱을 다운로드하고 자세히 알아보려면 [Microsoft Authenticator 앱 시작](microsoft-authenticator-app-how-to.md)을 참조하세요.

>[!Note]
>Microsoft Authenticator 앱을 사용하지 않으려는 경우 설정하는 동안 다른 앱을 선택할 수 있습니다. 이 문서에서는 Microsoft Authenticator 앱을 사용합니다. 인증자 앱 옵션이 보이지 않는 경우 사용자의 조직에서 인증에 인증 앱 사용을 허용하지 않았을 가능성이 있습니다. 이 경우 다른 방법을 선택하거나 관리자에게 문의하여 자세한 도움을 받아야 합니다.

### <a name="to-use-the-microsoft-authenticator-app"></a>Microsoft Authenticator 앱을 사용하려면

1. **Authenticator 앱** 옵션을 선택합니다.

    **앱 가져오기** 마법사가 나타납니다.

    ![앱 마법사 가져오기, 초기 화면](media/security-info/security-info-auth-app-wizard.png)

    Microsoft Authenticator 앱을 사용하지 않으려는 경우 **앱 가져오기** 화면에서 **다른 인증자 앱 사용** 링크를 클릭할 수 있습니다.

2. Microsoft Authenticator 앱을 설치한 후 **다음**을 선택합니다.

    메시지가 표시되면 알림을 허용하고, 새 계정을 추가한 다음, **회사 또는 학교 계정**을 선택합니다.

3. **다음**을 선택합니다.

    **QR 코드 스캔** 화면이 나타납니다.

    ![Authenticator 앱을 사용하여 QR 코드 스캔](media/security-info/security-info-scan-qr.png)

4. Microsoft Authenticator 앱을 열고 오른쪽 위에 있는 **사용자 지정 및 제어** 아이콘에서 **계정 추가**를 선택한 다음, **회사 또는 학교 계정**을 선택합니다. 

5. QR 코드 판독기 앱이 있는 경우 제공된 코드를 스캔합니다. 코드 판독기 앱이 없는 경우 **QR 코드 링크를 스캔할 수 없습니다**를 선택하고 Microsoft Authenticator 앱에 코드와 URL을 수동으로 입력할 수 있습니다.

6. Microsoft Authenticator 앱을 사용하여 앱을 활성화하도록 알림을 승인합니다.

    보안 정보는 2단계 인증 또는 셀프 서비스 암호 재설정을 사용하는 경우 본인 여부를 확인하기 위해 Microsoft Authenticator 앱을 사용하도록 업데이트됩니다.

    >[!Note]
    >조직에서 허용하는 경우 Microsoft Authenticator 앱 알림과 함께 확인 코드를 가져올 수도 있습니다. 코드를 기본 메서드로 만들려는 경우 [보안 정보 관리](security-info-setup-auth-app.md)의 지침을 따릅니다.

## <a name="additional-security-info-options"></a>추가 보안 정보 옵션

수행하려는 작업에 따라, 조직이 본인 여부를 확인하기 위해 사용자에게 연락하는 방식에 대한 추가 옵션이 제공됩니다. 옵션에는 다음이 포함됩니다.

- **모바일 장치 텍스트.** 모바일 장치 번호를 입력하면 2단계 인증 또는 암호 재설정에 사용할 코드가 포함된 문자를 받습니다. 문자 메시지(SMS)를 사용하여 본인 여부를 확인하는 방법에 대한 단계별 지침은 [문자 메시지(SMS)를 사용하도록 보안 정보 설정](security-info-setup-text-msg.md)을 참조하세요.

- **모바일 장치 또는 회사 전화 통화.** 모바일 장치 번호를 입력하면 2단계 인증 또는 암호 재설정을 위한 전화가 걸려옵니다. 전화 번호를 사용하여 본인 여부를 확인하는 방법에 대한 단계별 지침은 [전화 통화를 사용하도록 보안 정보 설정](security-info-setup-phone-number.md)을 참조하세요.

- **이메일 주소.** 회사 또는 학교 이메일 주소를 입력하여 암호 재설정을 위한 이메일을 받습니다. 이 옵션은 2단계 인증에 사용할 수 없습니다. 이메일을 설정하는 방법에 대한 단계별 지침은 [이메일을 사용하도록 보안 정보 설정](security-info-setup-email.md)을 참조하세요.

- **본인 확인 질문.** 조직의 관리자가 만든 몇 가지 보안 질문에 대답합니다. 이 옵션은 2단계 인증이 아닌 암호 재설정에만 사용할 수 있습니다. 보안 질문을 설정하는 방법에 대한 단계별 지침은 [보안 질문을 사용하도록 보안 정보 설정](security-info-setup-questions.md) 문서를 참조하세요.
    
    >[!Note]
    >이러한 옵션 중 일부가 누락된 경우 사용자의 조직에서 해당 방법을 허용하지 않았을 가능성이 있습니다. 이 경우 사용 가능한 방법을 선택하거나 관리자에게 문의하여 자세한 도움을 받아야 합니다.

## <a name="next-steps"></a>다음 단계

- 보안 정보를 업데이트해야 하는 경우 [보안 정보 관리](security-info-manage-settings.md) 문서의 지침에 따릅니다.

- 분실했거나 잊어버린 경우 [암호 재설정 포털](https://passwordreset.microsoftonline.com/)에서 암호를 다시 설정하거나 [회사 또는 학교 암호 재설정](user-help-reset-password.md) 문서의 단계를 수행합니다.

- [Microsoft 계정에 로그인할 수 없는 경우](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) 문서에서 로그인 문제에 대한 문제 해결 팁 및 도움말을 확인합니다.