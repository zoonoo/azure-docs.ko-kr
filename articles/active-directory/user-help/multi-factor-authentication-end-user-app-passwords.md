---
title: 앱 암호를 관리 하는 방법-Azure Active Directory | Microsoft Docs
description: 2 단계 인증과 관련 하 여 앱 암호 및 사용 되는 항목에 대해 알아봅니다.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: richagi
ms.assetid: 345b757b-5a2b-48eb-953f-d363313be9e5
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: conceptual
ms.date: 08/13/2019
ms.author: curtand
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: 5f81181ac3107307a352cdbcd0b5cc4a555deacb
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79253222"
---
# <a name="manage-app-passwords-for-two-step-verification"></a>2단계 인증을 위한 앱 암호 관리

>[!Important]
>관리자가 사용자의 앱 암호 사용을 허용하지 않을 수 있습니다. **앱 암호**가 옵션으로 표시되지 않으면 조직에서 사용할 수 없는 것입니다.

앱 암호를 사용하는 경우 다음 사항을 고려해야 합니다.

- 앱 암호는 자동으로 생성 되며, 앱 당 한 번 만들어 입력 해야 합니다.

- 사용자당 40개의 암호로 제한되어 있습니다. 해당 제한을 초과한 후 암호를 만들려고 하면 새 암호를 만들기 전에 기존 암호를 삭제할 것인지를 묻는 메시지가 표시됩니다.

    >[!Note]
    >Outlook을 포함한 Office 2013 클라이언트는 새로운 인증 프로토콜을 지원하며 2단계 인증과 함께 사용할 수 있습니다. 이 지원으로 2단계 인증을 켜고 나면 Office 2013 클라이언트에는 더 이상 앱 암호가 필요하지 않습니다. 자세한 내용은 [Office 2013 및 Office 2016 클라이언트 앱에 대한 최신 인증 작동 방식](https://support.office.com/article/how-modern-authentication-works-for-office-2013-and-office-2016-client-apps-e4c45989-4b1a-462e-a81b-2a13191cf517) 문서를 참조하세요.

## <a name="create-new-app-passwords"></a>새 앱 암호 만들기

처음 2 단계 인증 등록 프로세스 중에는 단일 앱 암호를 제공 합니다. 둘 이상의 작업이 필요한 경우 직접 만들어야 합니다. 조직에서 2 단계 인증을 설정 하는 방법에 따라 여러 영역에서 앱 암호를 만들 수 있습니다. 회사 또는 학교 계정으로 2 단계 인증을 사용 하도록 등록 하는 방법에 대 한 자세한 내용은 [2 단계 인증에 대 한 개요 및 회사 또는 학교 계정](multi-factor-authentication-end-user-first-time.md) 및 관련 문서를 참조 하세요.

### <a name="where-to-create-and-delete-your-app-passwords"></a>앱 암호를 만들고 삭제하는 위치

2 단계 인증을 사용 하는 방법에 따라 앱 암호를 만들고 삭제할 수 있습니다.

- **조직에서 2 단계 인증 및 추가 보안 확인 페이지를 사용 합니다.** 조직에서 2 단계 인증을 사용 하 여 회사 또는 학교 계정 (예: alain@contoso.com)을 사용 하는 경우 [추가 보안 인증 페이지](https://account.activedirectory.windowsazure.com/Proofup.aspx)에서 앱 암호를 관리할 수 있습니다. 자세한 지침은이 문서에서 [추가 보안 인증을 사용 하 여 앱 암호 만들기 및 삭제 페이지](#create-and-delete-app-passwords-from-the-additional-security-verification-page) 를 참조 하세요.

- **조직에서 2 단계 인증 및 Office 365 포털을 사용 합니다.** 조직에서 회사 또는 학교 계정 (예: alain@contoso.com), 2 단계 확인 및 Office 365 앱을 사용 하는 경우 [office 365 포털 페이지](https://www.office.com)에서 앱 암호를 관리할 수 있습니다. 자세한 지침은이 문서에서 [Office 365 포털을 사용 하 여 앱 암호 만들기 및 삭제](#create-and-delete-app-passwords-using-the-office-365-portal) 를 참조 하세요.

- **개인 Microsoft 계정를 사용 하 여 2 단계 인증을 사용 하 고 있습니다.** 2 단계 인증에 개인 Microsoft 계정 (예: alain@outlook.com)를 사용 하는 경우 [보안 기본 페이지](https://account.microsoft.com/security/)에서 앱 암호를 관리할 수 있습니다. 자세한 지침은 [2 단계 인증을 지원 하지 않는 앱에서 앱 암호 사용](https://support.microsoft.com/help/12409/microsoft-account-app-passwords-and-two-step-verification)을 참조 하세요.

## <a name="create-and-delete-app-passwords-from-the-additional-security-verification-page"></a>추가 보안 인증 페이지에서 앱 암호 만들기 및 삭제

회사 또는 학교 계정에 대 한 **추가 보안 인증** 페이지에서 앱 암호를 만들고 삭제할 수 있습니다.

1. [추가 보안 인증 페이지](https://account.activedirectory.windowsazure.com/Proofup.aspx)에 로그인 한 후 **앱 암호**를 선택 합니다.

    ![앱 암호 탭이 강조 표시 된 앱 암호 페이지](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page.png)

2. **만들기**를 선택 하 고 앱 암호가 필요한 앱의 이름을 입력 한 후 **다음**을 선택 합니다.

    ![암호를 요구 하는 앱의 이름을 사용 하 여 앱 암호 만들기 페이지](media/multi-factor-authentication-end-user-app-passwords/mfa-create-app-password-page.png)

3. **앱 암호** 페이지에서 암호를 복사한 다음 **닫기**를 선택 합니다.

    ![지정 된 앱에 대 한 암호를 포함 하는 앱 암호 페이지](media/multi-factor-authentication-end-user-app-passwords/mfa-your-app-password-page.png)

4. **앱 암호** 페이지에서 앱이 나열 되는지 확인 합니다.

     ![목록에 새 앱이 표시 된 앱 암호 페이지](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page-with-new-password.png)  

5. 앱 암호를 만든 앱 (예: Outlook 2010)을 열고 요청 시 앱 암호를 붙여넣습니다. 앱 당 한 번만 수행 하면 됩니다.

### <a name="to-delete-an-app-password-using-the-app-passwords-page"></a>앱 암호 페이지를 사용 하 여 앱 암호를 삭제 하려면

1. **앱 암호** 페이지에서 삭제 하려는 앱 암호 옆에 있는 **삭제** 를 선택 합니다.

   ![앱 암호 삭제](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page-delete.png)

2. **예**를 선택하여 암호를 삭제할 것인지 확인한 다음, **닫기**를 선택합니다.

    앱 암호가 성공적으로 삭제되었습니다.

## <a name="create-and-delete-app-passwords-using-the-office-365-portal"></a>Office 365 포털을 사용하여 앱 암호 만들기 및 삭제

회사 또는 학교 계정 및 Office 365 앱에서 2단계 인증을 사용하는 경우 Office 365 포털을 사용하여 앱 암호를 만들고 삭제할 수 있습니다.

### <a name="to-create-app-passwords-using-the-office-365-portal"></a>Office 365 포털을 사용하여 앱 암호를 삭제하려면

1. Office 365에 로그인 한 후 [내 계정 페이지로](https://portal.office.com)이동 하 고 **보안 & 개인 정보**를 선택한 후에 **추가 보안 확인**을 확장 합니다.

    ![확장된 추가 보안 확인 영역을 보여 주는 Office 포털](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-o365-my-account-page.png)

2. 앱 암호 **만들기 및 관리** 라는 텍스트를 선택 하 여 **앱 암호** 페이지를 엽니다.

    ![앱 암호 탭이 강조 표시 된 앱 암호 페이지](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page.png)

3. **만들기**를 선택 하 고 앱 암호가 필요한 앱의 이름을 입력 한 후 **다음**을 선택 합니다.

    ![암호를 요구 하는 앱의 이름을 사용 하 여 앱 암호 만들기 페이지](media/multi-factor-authentication-end-user-app-passwords/mfa-create-app-password-page.png)

4. **앱 암호** 페이지에서 암호를 복사한 다음 **닫기**를 선택 합니다.

    ![지정 된 앱에 대 한 암호를 포함 하는 앱 암호 페이지](media/multi-factor-authentication-end-user-app-passwords/mfa-your-app-password-page.png)

5. **앱 암호** 페이지에서 앱이 나열 되는지 확인 합니다.

     ![목록에 새 앱이 표시 된 앱 암호 페이지](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page-with-new-password.png)  

6. 앱 암호를 만든 앱 (예: Outlook 2010)을 열고 요청 시 앱 암호를 붙여넣습니다. 앱 당 한 번만 수행 하면 됩니다.

### <a name="to-delete-app-passwords-using-the-app-passwords-page"></a>앱 암호 페이지를 사용 하 여 앱 암호를 삭제 하려면

1. **앱 암호** 페이지에서 삭제 하려는 앱 암호 옆에 있는 **삭제** 를 선택 합니다.

   ![앱 암호 삭제](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page-delete.png)

2. 확인 상자에서 **예** 를 선택 하 고 **닫기**를 선택 합니다.

    앱 암호가 성공적으로 삭제되었습니다.

## <a name="if-your-app-passwords-arent-working-properly"></a>앱 암호가 제대로 작동하지 않는 경우

암호를 올바르게 입력했는지 확인합니다. 암호를 올바르게 입력했다면 로그인을 다시 시도하고 새 앱 암호를 만들 수 있습니다. 이러한 방법으로 문제를 해결할 수 없는 경우 기존 앱 암호를 삭제 하 여 새 앱 암호를 만들 수 있도록 조직의 지원 센터에 문의 하세요.

## <a name="next-steps"></a>다음 단계

- [2단계 인증 설정 관리](multi-factor-authentication-end-user-manage-settings.md)

- [Microsoft Authenticator 앱](user-help-auth-app-download-install.md)을 사용하여 텍스트 또는 전화를 받는 대신 앱 알림으로 로그인을 확인합니다.
