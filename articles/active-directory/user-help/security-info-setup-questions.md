---
title: 보안 질문을 사용하도록 보안 정보 설정 - Azure Active Directory | Microsoft Docs
description: 미리 정의된 보안 질문을 사용하여 본인 여부를 확인하도록 보안 정보를 설정합니다.
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: lizross
ms.collection: M365-identity-device-management
ms.openlocfilehash: ab3817411c1285f2ca7c8aa294f90314e3545504
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56203491"
---
# <a name="set-up-security-info-to-use-pre-defined-security-questions-preview"></a>미리 정의된 보안 질문을 사용하도록 보안 정보 설정(미리 보기)

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

보안 정보를 설정하면 회사 또는 학교 계정에 로그인한 다음, 등록 프로세스를 완료해야 합니다. 보안 정보를 설정하지 않은 경우 지금 수행할 것인지 묻는 메시지가 표시됩니다.

## <a name="set-up-security-questions"></a>보안 질문 설정

조직의 설정에 따라, 로그인 시 보안 정보에 보안 질문을 추가할지 묻는 메시지가 표시될 수 있습니다. 그렇지 않은 경우, 보안 정보에서 보안 질문을 설정하려면 [보안 정보 관리](security-info-manage-settings.md)의 단계를 따릅니다.

보안 질문을 사용하는 경우 다른 방법과 함께 사용하는 것이 좋습니다. 어떤 사람들이 다른 사용자의 질문에 대한 답을 알고 있을 수도 있으므로 보안 질문은 다른 방법보다 덜 안전할 수 있습니다.

>[!Note]
>보안 질문은 디렉터리에서 사용자 개체에 대해 비공개적으로 안전하게 저장되며 등록하는 동안 사용자만이 답변할 수 있습니다. 관리자는 사용자의 질문 또는 대답을 읽거나 수정할 수 없습니다.<br>보안 질문 옵션이 보이지 않는 경우 사용자의 조직에서 인증에 보안 질문 사용을 허용하지 않았을 가능성이 있습니다. 이 경우 다른 방법을 선택하거나 관리자에게 문의하여 자세한 도움을 받아야 합니다.

### <a name="to-choose-and-answer-your-security-questions"></a>보안 질문을 선택하고 대답하려면

1. **보안 질문**을 선택한 후 대답하려는 보안 질문을 선택합니다. 

    선택해야 하는 보안 질문 수는 관리자가 결정합니다.

    ![보안 정보 페이지, 보안 질문 선택](media/security-info/security-info-keep-secure-setup-pick-questions.png)

2. 선택한 질문에 대한 답변을 제공하고 **완료**를 선택합니다.

## <a name="additional-security-info-options"></a>추가 보안 정보 옵션

사용자가 시도하려는 방식에 따라 조직이 본인 여부를 확인하기 위해 사용자에게 연락하는 방법에 대한 옵션이 있습니다. 옵션에는 다음이 포함됩니다.

- **Authenticator 앱.** Authenticator 앱을 다운로드한 후 사용하여 2단계 인증 또는 암호 재설정을 위한 승인 알림 또는 임의로 생성된 승인 코드를 가져올 수 있습니다. Microsoft Authenticator 앱을 설치 및 사용하는 방법에 대한 단계별 지침은 [Authenticator 앱을 사용하도록 보안 정보 설정](security-info-setup-auth-app.md)을 참조하세요.

- **모바일 디바이스 텍스트.** 모바일 디바이스 번호를 입력하면 2단계 인증 또는 암호 재설정에 사용할 코드가 포함된 문자를 받습니다. 문자 메시지(SMS)를 사용하여 본인 여부를 확인하는 방법에 대한 단계별 지침은 [문자 메시지(SMS)를 사용하도록 보안 정보 설정](security-info-setup-text-msg.md)을 참조하세요.

- **모바일 디바이스 또는 회사 전화 통화.** 모바일 디바이스 번호를 입력하면 2단계 인증 또는 암호 재설정을 위한 전화가 걸려옵니다. 전화 번호를 사용하여 본인 여부를 확인하는 방법에 대한 단계별 지침은 [전화 통화를 사용하도록 보안 정보 설정](security-info-setup-phone-number.md)을 참조하세요.

- **이메일 주소.** 회사 또는 학교 이메일 주소를 입력하여 암호 재설정을 위한 이메일을 받습니다. 이 옵션은 2단계 인증에 사용할 수 없습니다. 이메일을 설정하는 방법에 대한 단계별 지침은 [이메일을 사용하도록 보안 정보 설정](security-info-setup-email.md)을 참조하세요.
   
    >[!Note]
    >이러한 옵션 중 일부가 누락된 경우 사용자의 조직에서 해당 방법을 허용하지 않았을 가능성이 있습니다. 이 경우 사용 가능한 방법을 선택하거나 관리자에게 문의하여 자세한 도움을 받아야 합니다.

## <a name="next-steps"></a>다음 단계

- 보안 정보를 업데이트해야 하는 경우 [보안 정보 관리](security-info-manage-settings.md) 문서의 지침에 따릅니다.

- 분실했거나 잊어버린 경우 [암호 재설정 포털](https://passwordreset.microsoftonline.com/)에서 암호를 다시 설정하거나 [회사 또는 학교 암호 재설정](user-help-reset-password.md) 문서의 단계를 수행합니다.

- [Microsoft 계정에 로그인할 수 없는 경우](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) 문서에서 로그인 문제에 대한 문제 해결 팁 및 도움말을 확인합니다.
