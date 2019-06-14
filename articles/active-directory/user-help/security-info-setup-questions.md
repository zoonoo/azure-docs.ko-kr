---
title: 보안 질문을 사용하도록 보안 정보(미리 보기) 설정 - Azure Active Directory | Microsoft Docs
description: 미리 정의된 보안 질문을 사용하여 본인 여부를 확인하도록 보안 정보를 설정하는 방법입니다.
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
ms.openlocfilehash: 3e5d1546c658631911f25c43e94275f00c7a5140
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60474644"
---
# <a name="set-up-security-info-preview-to-use-security-questions"></a>본인 확인 질문을 사용하도록 보안 정보(미리 보기) 설정
다음 단계에 따라 암호 재설정 방법을 추가할 수 있습니다. 이 정보를 처음 설정한 후 **보안 정보** 페이지로 돌아가서 보안 정보를 추가, 업데이트 또는 삭제할 수 있습니다.

암호 재설정 방법을 설정한 후 [인증자 앱](security-info-setup-auth-app.md), [문자 메시지](security-info-setup-text-msg.md) 또는 [전화 통화](security-info-setup-phone-number.md)를 사용하여 2단계 확인 방법도 설정해야 합니다.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

## <a name="set-up-your-security-questions-from-the-security-info-page"></a>보안 정보 페이지에서 본인 확인 질문 설정
조직의 설정에 따라 몇 개의 본인 확인 질문을 보안 정보 방법 중 하나로 선택하고 대답할 수 있습니다. 선택하고 대답해야 하는 본인 확인 질문 수는 관리자가 설정합니다.

보안 질문을 사용하는 경우 다른 방법과 함께 사용하는 것이 좋습니다. 다른 사람이 본인 확인 질문에 대한 답변을 알고 있을 수도 있으므로 본인 확인 질문은 다른 방법보다 덜 안전할 수 있습니다.

> [!Note]
> 보안 질문은 디렉터리에서 사용자 개체에 대해 비공개적으로 안전하게 저장되며 등록하는 동안 사용자만이 답변할 수 있습니다. 관리자는 사용자의 질문 또는 대답을 읽거나 수정할 수 없습니다.
> 
> 보안 질문 옵션이 보이지 않는 경우 사용자의 조직에서 인증에 보안 질문 사용을 허용하지 않았을 가능성이 있습니다. 이 경우 다른 방법을 선택하거나 관리자에게 문의하여 자세한 도움을 받아야 합니다.
> 
> 관리자 계정 암호 재설정 방법으로 보안 질문을 사용 하는 허용 되지 않습니다. 관리자 수준 계정으로 로그인 하는 경우 이러한 옵션이 표시 되지 않습니다.

### <a name="to-set-up-your-security-questions"></a>본인 확인 질문을 설정하려면 다음을 수행합니다.

1. 회사 또는 학교 계정에 로그인한 다음, https://myprofile.microsoft.com/ 페이지로 이동합니다.

    ![보안 정보 링크가 강조 표시된 내 프로필 페이지](media/security-info/securityinfo-myprofile-page.png)

2. 왼쪽 탐색 창 또는 **보안 정보** 블록의 링크에서 **보안 정보**를 선택한 다음, **보안 정보** 페이지에서 **메서드 추가**를 선택합니다.

    ![방법 추가 옵션이 강조 표시된 보안 정보 페이지](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. **방법 추가** 페이지의 드롭다운 목록에서 **본인 확인 질문**을 선택한 다음, **추가**를 선택합니다.

    ![본인 확인 질문이 선택된 방법 추가 상자](media/security-info/securityinfo-myprofile-addquestions.png)

4. **본인 확인 질문** 페이지에서 본인 확인 질문을 선택하고 대답한 다음, **저장**을 선택합니다.

    ![전화 번호를 추가하고 전화 통화 선택](media/security-info/securityinfo-myprofile-securityquestions.png)

    보안 정보가 업데이트되며, 암호 재설정을 사용할 때 본인 확인 질문을 사용하여 본인 여부를 확인할 수 있습니다.

## <a name="delete-security-questions-from-your-security-info-methods"></a>보안 정보 방법에서 본인 확인 질문 삭제
더 이상 본인 확인 질문을 보안 정보 방법으로 사용하지 않으려는 경우 **보안 정보** 페이지에서 제거할 수 있습니다.

>[!Important]
>실수로 본인 확인 질문을 삭제하면 실행 취소할 수 없습니다. 이 문서의 [본인 확인 질문 설정](#set-up-your-security-questions-from-the-security-info-page) 섹션에 있는 단계에 따라 방법을 다시 추가해야 합니다.

### <a name="to-delete-your-security-questions"></a>본인 확인 질문을 삭제하려면 다음을 수행합니다.

1. **보안 정보** 페이지에서 **본인 확인 질문** 옵션 옆에 있는 **삭제** 링크를 선택합니다.

    ![보안 정보에서 전화 방법을 삭제하기 위한 링크](media/security-info/securityinfo-myprofile-questionsdelete.png)

2. 확인 상자에서 **예**를 선택하여 **본인 확인 질문**을 삭제합니다. 본인 확인 질문이 삭제되면 방법이 보안 정보에서 제거되고 **보안 정보** 페이지에서 사라집니다.

## <a name="additional-security-info-methods"></a>추가 보안 정보 방법
수행하려는 작업에 따라, 조직이 본인 여부를 확인하기 위해 사용자에게 연락하는 방식에 대한 추가 옵션이 제공됩니다. 옵션에는 다음이 포함됩니다.

- **Authenticator 앱.** Authenticator 앱을 다운로드한 후 사용하여 2단계 인증 또는 암호 재설정을 위한 승인 알림 또는 임의로 생성된 승인 코드를 가져올 수 있습니다. Microsoft Authenticator 앱을 설치 및 사용하는 방법에 대한 단계별 지침은 [Authenticator 앱을 사용하도록 보안 정보 설정](security-info-setup-auth-app.md)을 참조하세요.

- **모바일 디바이스 텍스트.** 모바일 디바이스 번호를 입력하면 2단계 인증 또는 암호 재설정에 사용할 코드가 포함된 문자를 받습니다. 문자 메시지(SMS)를 사용하여 본인 여부를 확인하는 방법에 대한 단계별 지침은 [문자 메시지(SMS)를 사용하도록 보안 정보 설정](security-info-setup-text-msg.md)을 참조하세요.

- **모바일 디바이스 또는 회사 전화 통화.** 모바일 디바이스 번호를 입력하면 2단계 인증 또는 암호 재설정을 위한 전화가 걸려옵니다. 전화 번호를 사용하여 본인 여부를 확인하는 방법에 대한 단계별 지침은 [전화 통화를 사용하도록 보안 정보 설정](security-info-setup-phone-number.md)을 참조하세요.

- **이메일 주소.** 회사 또는 학교 이메일 주소를 입력하여 암호 재설정을 위한 이메일을 받습니다. 이 옵션은 2단계 인증에 사용할 수 없습니다. 이메일을 설정하는 방법에 대한 단계별 지침은 [이메일을 사용하도록 보안 정보 설정](security-info-setup-email.md)을 참조하세요.
   
    >[!Note]
    >이러한 옵션 중 일부가 누락된 경우 사용자의 조직에서 해당 방법을 허용하지 않았을 가능성이 있습니다. 이 경우 사용 가능한 방법을 선택하거나 관리자에게 문의하여 자세한 도움을 받아야 합니다.

## <a name="next-steps"></a>다음 단계

- 분실했거나 잊어버린 경우 [암호 재설정 포털](https://passwordreset.microsoftonline.com/)에서 암호를 다시 설정하거나 [회사 또는 학교 암호 재설정](user-help-reset-password.md) 문서의 단계를 수행합니다.

- [Microsoft 계정에 로그인할 수 없는 경우](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) 문서에서 로그인 문제에 대한 문제 해결 팁 및 도움말을 확인합니다.
