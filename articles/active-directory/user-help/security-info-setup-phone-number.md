---
title: 전화 통화를 사용하도록 보안 정보(미리 보기) 설정 - Azure Active Directory | Microsoft Docs
description: 전화 통화를 사용하여 본인 여부를 확인하도록 보안 정보를 설정하는 방법입니다.
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 02/13/2019
ms.author: lizross
ms.collection: M365-identity-device-management
ms.openlocfilehash: cf07541c23ba2295a367ac8ba6a514b7dee6ee9a
ms.sourcegitcommit: 75fef8147209a1dcdc7573c4a6a90f0151a12e17
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/20/2019
ms.locfileid: "56453837"
---
# <a name="set-up-security-info-preview-to-use-phone-calls"></a>전화 통화를 사용하도록 보안 정보(미리 보기) 설정
이 단계를 수행하여 2단계 확인 및 암호 재설정 방법을 추가할 수 있습니다. 이 정보를 처음 설정한 후 **보안 정보** 페이지로 돌아가서 보안 정보를 추가, 업데이트 또는 삭제할 수 있습니다.

회사 또는 학교 계정에 로그인한 직후에 이 정보를 설정하라는 메시지가 표시되는 경우 [로그인 페이지 프롬프트에서 보안 정보 설정](security-info-setup-signin.md) 문서의 자세한 단계를 참조하세요.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

>[!Note]
>보안 정보는 내선 번호 사용을 지원하지 않습니다. 올바른 형식인 +1 4255551234X12345를 추가하더라도, 통화가 되기 전에 내선 번호가 제거됩니다.

>전화 옵션이 보이지 않는 경우 전화 번호를 확인에 사용하는 것을 조직에서 허용하지 않기 때문일 수 있습니다. 이 경우 다른 방법을 선택하거나 관리자에게 문의하여 추가 도움을 받아야 합니다.

## <a name="set-up-phone-calls-from-the-security-info-page"></a>보안 정보 페이지에서 전화 통화 설정
조직의 설정에 따라 전화 통화를 보안 정보 방법 중 하나로 사용할 수 있습니다.

>[!Note]
>전화 통화 대신 문자 메시지를 수신하려는 경우 [문자 메시지를 사용하도록 보안 정보 설정](security-info-setup-text-msg.md) 문서의 단계를 따르세요.

### <a name="to-set-up-phone-calls"></a>전화 통화를 설정하려면 다음을 수행합니다.

1. 회사 또는 학교 계정에 로그인한 다음, https://myprofile.microsoft.com/ 페이지로 이동합니다.

    ![보안 정보 링크가 강조 표시된 내 프로필 페이지](media/security-info/securityinfo-myprofile.png)

2. 왼쪽 탐색 창 또는 **보안 정보** 블록의 링크에서 **보안 정보**를 선택한 다음, **보안 정보** 페이지에서 **방법 추가**를 선택합니다.

    ![방법 추가 옵션이 강조 표시된 보안 정보 페이지](media/security-info/securityinfo-myprofile-addmethod.png)

3. **방법 추가** 페이지의 드롭다운 목록에서 **전화**를 선택한 다음, **추가**를 선택합니다.

    ![전화가 선택된 방법 추가 상자](media/security-info/securityinfo-myprofile-addphonetext.png)

4. **전화** 페이지에서 모바일 디바이스의 전화 번호를 입력하고 **내게 전화 걸기**를 선택한 후, **다음**을 선택합니다.

    ![전화 번호를 추가하고 전화 통화 선택](media/security-info/securityinfo-myprofile-phonecall-addnumber.png)

5. 입력한 전화 번호로 수신되는 확인 전화 통화에 응답하고 지침을 따릅니다.

    페이지가 변경되어 성공을 표시합니다.

    ![성공 알림, 전화 번호 연결, 전화 통화 수신 선택 및 계정](media/security-info/securityinfo-myprofile-phonetext-success.png)

    보안 정보가 업데이트되며, 2단계 확인 또는 암호 재설정을 사용할 때 전화 통화를 사용하여 본인 여부를 확인할 수 있습니다. 전화 통화를 기본 방법으로 설정하려는 경우 이 문서의 [기본 보안 정보 방법 변경](#change-your-default-security-info-method) 섹션을 참조하세요.

## <a name="delete-phone-calls-from-your-security-info-methods"></a>보안 정보 방법에서 전화 통화 삭제
더 이상 전화 통화를 보안 정보 방법으로 사용하지 않으려는 경우 **보안 정보** 페이지에서 제거할 수 있습니다.

>[!Important]
>실수로 전화 통화를 삭제하면 실행 취소할 수 없습니다. 이 문서의 [전화 통화 설정](#set-up-phone-calls-from-the-security-info-page) 섹션에 있는 단계에 따라 방법을 다시 추가해야 합니다.

### <a name="to-delete-phone-calls"></a>전화 통화를 삭제하려면 다음을 수행합니다.

1. **보안 정보** 페이지에서 **전화** 옵션 옆에 있는 **삭제** 링크를 선택합니다.

    ![보안 정보에서 전화 방법을 삭제하기 위한 링크](media/security-info/securityinfo-myprofile-phonetext-delete.png)

2. 확인 상자에서 **예**를 선택하여 **전화** 번호를 삭제합니다. 전화 번호가 삭제되면 보안 정보에서 제거되고 **보안 정보** 페이지에서 사라집니다. **전화**가 기본 방법인 경우 기본값이 사용 가능한 다른 방법으로 바뀝니다.
    
## <a name="change-your-default-security-info-method"></a>기본 보안 정보 방법 변경
2단계 확인을 사용하거나 암호 재설정 요청을 위해 회사 또는 학교 계정에 로그인할 때 사용되는 기본 방법으로 전화 통화를 설정하려는 경우 **보안 정보** 페이지에서 설정할 수 있습니다.

### <a name="to-change-your-default-security-info-method"></a>기본 보안 정보 방법을 변경하려면 다음을 수행합니다.

1. **보안 정보** 페이지에서 **기본 로그인 방법** 정보 옆에 있는 **변경** 링크를 선택합니다.

    ![기본 로그인 방법의 변경 링크](media/security-info/securityinfo-myprofile-phonetext-defaultchange.png)

2. 사용 가능한 방법의 드롭다운 목록에서 **전화 통화(*_your_phone_number_*)** 를 선택한 다음, **확인**을 선택합니다.

    ![기본 로그인 방법 선택](media/security-info/securityinfo-myprofile-phonecall-changeddefault.png)

    로그인에 사용되는 기본 방법이 **전화 통화(*_your_phone_number_*)** 로 바뀝니다.

## <a name="additional-security-info-methods"></a>추가 보안 정보 방법
수행하려는 작업에 따라, 조직이 본인 여부를 확인하기 위해 사용자에게 연락하는 방식에 대한 추가 옵션이 제공됩니다. 옵션에는 다음이 포함됩니다.

- **Authenticator 앱.** Authenticator 앱을 다운로드한 후 사용하여 2단계 인증 또는 암호 재설정을 위한 승인 알림 또는 임의로 생성된 승인 코드를 가져올 수 있습니다. Microsoft Authenticator 앱을 설치 및 사용하는 방법에 대한 단계별 지침은 [Authenticator 앱을 사용하도록 보안 정보 설정](security-info-setup-auth-app.md)을 참조하세요.

- **모바일 디바이스 텍스트.** 모바일 디바이스 번호를 입력하면 2단계 인증 또는 암호 재설정에 사용할 코드가 포함된 문자를 받습니다. 문자 메시지(SMS)를 사용하여 본인 여부를 확인하는 방법에 대한 단계별 지침은 [문자 메시지(SMS)를 사용하도록 보안 정보 설정](security-info-setup-text-msg.md)을 참조하세요.

- **이메일 주소.** 회사 또는 학교 이메일 주소를 입력하여 암호 재설정을 위한 이메일을 받습니다. 이 옵션은 2단계 인증에 사용할 수 없습니다. 이메일을 설정하는 방법에 대한 단계별 지침은 [이메일을 사용하도록 보안 정보 설정](security-info-setup-email.md)을 참조하세요.

- **본인 확인 질문.** 조직의 관리자가 만든 몇 가지 보안 질문에 대답합니다. 이 옵션은 2단계 인증이 아닌 암호 재설정에만 사용할 수 있습니다. 보안 질문을 설정하는 방법에 대한 단계별 지침은 [보안 질문을 사용하도록 보안 정보 설정](security-info-setup-questions.md) 문서를 참조하세요.
    
    >[!Note]
    >이러한 옵션 중 일부가 누락된 경우 사용자의 조직에서 해당 방법을 허용하지 않았을 가능성이 있습니다. 이 경우 사용 가능한 방법을 선택하거나 관리자에게 문의하여 자세한 도움을 받아야 합니다.

## <a name="next-steps"></a>다음 단계

- 분실했거나 잊어버린 경우 [암호 재설정 포털](https://passwordreset.microsoftonline.com/)에서 암호를 다시 설정하거나 [회사 또는 학교 암호 재설정](user-help-reset-password.md) 문서의 단계를 수행합니다.

- [Microsoft 계정에 로그인할 수 없는 경우](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) 문서에서 로그인 문제에 대한 문제 해결 팁 및 도움말을 확인합니다.