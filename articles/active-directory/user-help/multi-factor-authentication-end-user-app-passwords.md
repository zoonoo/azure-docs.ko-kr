---
title: Azure Active Directory에서 앱 암호를 관리하는 방법 | Microsoft Docs
description: 이 페이지는 사용자가 앱 암호란 무엇이며 2단계 인증과 관련해서 암호가 어떤 용도로 사용되는지를 이해하는 데 도움이 됩니다.
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: richagi
ms.assetid: 345b757b-5a2b-48eb-953f-d363313be9e5
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: lizross
ms.collection: M365-identity-device-management
ms.openlocfilehash: fdcd078714d8311cf59471492187314183de28b2
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56187307"
---
# <a name="manage-app-passwords-for-two-step-verification"></a>2단계 인증을 위한 앱 암호 관리

Outlook 2010과 같은 특정 비브라우저 앱은 2단계 인증을 지원하지 않습니다. 따라서 2단계 인증을 사용하는 경우 앱이 작동하지 않습니다. 이 문제를 해결하려면 사용자의 일반 암호와는 별도로 자동으로 생성된 암호를 만들어 각 비 브라우저 앱과 함께 사용할 수 있습니다.

앱 암호를 사용하는 경우 다음 사항을 고려해야 합니다.

- 앱 암호는 자동으로 생성되며 앱당 한 번만 입력됩니다.

- 사용자당 40개의 암호로 제한되어 있습니다. 해당 제한을 초과한 후 암호를 만들려고 하면 새 암호를 만들기 전에 기존 암호를 삭제할 것인지를 묻는 메시지가 표시됩니다.

- 앱마다가 아닌 디바이스마다 하나의 앱 암호를 사용합니다. 예를 들어 랩톱의 모든 앱에 대해 단일 암호를 만든 다음, 데스크톱의 모든 앱에 대해 다른 단일 암호를 만듭니다.

    >[!Note]
    >Outlook을 포함한 Office 2013 클라이언트는 새로운 인증 프로토콜을 지원하며 2단계 인증과 함께 사용할 수 있습니다. 이 지원으로 2단계 인증을 켜고 나면 Office 2013 클라이언트에는 더 이상 앱 암호가 필요하지 않습니다. 자세한 내용은 [Office 2013 및 Office 2016 클라이언트 앱에 대한 최신 인증 작동 방식](https://support.office.com/article/how-modern-authentication-works-for-office-2013-and-office-2016-client-apps-e4c45989-4b1a-462e-a81b-2a13191cf517) 문서를 참조하세요.

## <a name="where-to-create-and-delete-your-app-passwords"></a>앱 암호를 만들고 삭제하는 위치

초기 2단계 인증을 등록하는 동안 앱 암호가 지정됩니다. 둘 이상의 암호가 필요한 경우 2단계 인증을 사용하는 방법에 따라 추가적인 암호를 만들 수 있습니다.

- **회사 또는 학교 계정, MyApps 포털에서 2단계 인증을 사용합니다.** 이 문서의 [MyApps 포털을 사용하여 앱 암호 만들기 및 삭제](#create-and-delete-app-passwords-using-the-myapps-portal) 섹션에 나온 지침을 사용하여 앱 암호를 만들고 삭제합니다. MyApps 포털 및 사용 방법에 대한 자세한 내용은 [Azure Active Directory의 MyApps 포털이란?](active-directory-saas-access-panel-introduction.md)을 참조하세요.

- **회사 또는 학교 계정, Office 365 포털에서 2단계 인증을 사용합니다.** 이 문서의 [Office 365 포털을 사용하여 앱 암호 만들기 및 삭제](#create-and-delete-app-passwords-using-the-office-365-portal) 섹션에 나온 지침을 사용하여 앱 암호를 만들고 삭제합니다.

- **개인 Microsoft 계정에 2단계 인증을 사용합니다.** 개인 Microsoft 계정을 통해 [보안 기본 사항](https://account.microsoft.com/account/) 페이지를 사용하여 앱 암호를 만들고 삭제합니다. 자세한 내용은 [앱 암호 및 2단계 인증](https://support.microsoft.com/help/12409/microsoft-account-app-passwords-two-step-verification) 문서를 참조하세요.

## <a name="create-and-delete-app-passwords-using-the-myapps-portal"></a>MyApps 포털을 사용하여 앱 암호 만들기 및 삭제
MyApps 포털을 통해 앱 암호를 만들고 삭제할 수 있습니다.

### <a name="to-create-an-app-password-using-the-myapps-portal"></a>MyApps 포털을 사용하여 앱 암호를 만들려면

1. [https://myapps.microsoft.com](https://myapps.microsoft.com)에 로그인합니다.

2. 오른쪽 위에 있는 이름을 클릭하고 **프로필**을 선택합니다.

3. **추가 보안 인증**을 선택합니다.

   ![추가 보안 인증 선택 - 스크린샷](./media/multi-factor-authentication-end-user-app-passwords/myapps1.png)

4. **앱 암호**를 선택합니다.

   ![앱 암호 선택 - 스크린샷](./media/multi-factor-authentication-end-user-app-passwords/apppass2.png)

5. **만들기**를 클릭합니다.

6. 앱 암호에 사용할 이름을 입력한 다음, **다음**을 선택합니다.

7. 앱 암호를 클립보드에 복사하고 앱에 붙여 넣습니다.
   
    ![앱 암호 만들기](./media/multi-factor-authentication-end-user-app-passwords/create2.png)

### <a name="to-delete-an-app-password-using-the-myapps-portal"></a>MyApps 포털을 사용하여 앱 암호를 삭제하려면

1. 프로필로 이동한 다음, **추가 보안 확인**을 선택합니다.

2. **앱 암호**를 선택한 다음, 삭제할 앱 암호 옆에 있는 **삭제**를 선택합니다.

   ![앱 암호 삭제](./media/multi-factor-authentication-end-user-app-passwords/delete1.png)

3. **예**를 선택하여 암호를 삭제할 것인지 확인한 다음, **닫기**를 선택합니다.

## <a name="create-and-delete-app-passwords-using-the-office-365-portal"></a>Office 365 포털을 사용하여 앱 암호 만들기 및 삭제

회사 또는 학교 계정 및 Office 365 앱에서 2단계 인증을 사용하는 경우 Office 365 포털을 사용하여 앱 암호를 만들고 삭제할 수 있습니다. 한 번에 최대 40개의 앱 암호를 만들 수 있습니다. 해당 제한을 초과한 후 다른 앱 암호가 필요한 경우 기존 앱 암호 중 하나를 삭제해야 합니다.

### <a name="to-create-app-passwords-using-the-office-365-portal"></a>Office 365 포털을 사용하여 앱 암호를 삭제하려면

1. 회사 또는 학교 계정에 로그인합니다.

2. https://portal.office.com으로 이동하고 **Office 365 포털** 페이지의 오른쪽 위에 있는 **설정** 아이콘을 선택한 다음, **추가 보안 확인**을 확장합니다.

    ![확장된 추가 보안 확인 영역을 보여 주는 Office 포털](media/security-info/security-info-o365password.png)

3. **앱 암호 만들기 및 관리**라는 텍스트를 선택하여 **앱 암호** 페이지를 엽니다.

4. **만들기**를 선택하고 앱 암호가 필요한 앱에 사용할 친숙한 이름을 입력한 다음, **다음**을 선택합니다.

5. **클립보드로 암호 복사**를 선택한 다음, **닫기**를 선택합니다.

6. 복사한 앱 암호를 사용하여 비브라우저 앱에 로그인합니다. 이 암호를 한 번 입력해야 하고 다음에 사용할 수 있도록 기억해야 합니다.

### <a name="to-delete-app-passwords-using-the-office-365-portal"></a>Office 365 포털을 사용하여 앱 암호를 삭제하려면

1. 회사 또는 학교 계정에 로그인합니다.

2. https://portal.office.com으로 이동하고 **Office 365 포털** 페이지의 오른쪽 위에 있는 **설정** 아이콘을 선택한 다음, **추가 보안 확인**을 선택합니다.

3. **앱 암호 만들기 및 관리**라는 텍스트를 선택하여 **앱 암호** 페이지를 엽니다.

4. 삭제할 앱 암호에서 **삭제**를 선택하고 확인 상자에서 **예**를 선택한 다음, **닫기**를 선택합니다.

    앱 암호가 성공적으로 삭제되었습니다.

5. 새 앱 암호를 만들려면 앱 암호를 만들기 위한 단계를 수행합니다.

## <a name="if-your-app-passwords-arent-working-properly"></a>앱 암호가 제대로 작동하지 않는 경우

암호를 올바르게 입력했는지 확인합니다. 암호를 올바르게 입력했다면 로그인을 다시 시도하고 새 앱 암호를 만들 수 있습니다. 그러한 옵션들로 문제가 해결되지 않는다면 기존 앱 암호를 삭제할 수 있도록 회사 지원팀에 문의합니다. 그러면 새로운 앱 암호를 만들 수 있습니다. 

## <a name="next-steps"></a>다음 단계

- [2단계 인증 설정 관리](multi-factor-authentication-end-user-manage-settings.md)

- [Microsoft Authenticator 앱](user-help-auth-app-download-install.md)을 사용하여 텍스트 또는 전화를 받는 대신 앱 알림으로 로그인을 확인합니다.
