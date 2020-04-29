---
title: Microsoft Authenticator 앱에 개인 Microsoft 계정 추가-Azure AD
description: 2 단계 인증을 사용 하는 동안 id를 확인 하기 위해 Outlook.com 또는 Xbox LIVE와 같은 개인 Microsoft 계정을 Microsoft Authenticator 앱에 추가 합니다.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 04/08/2020
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: cef4f83881aed67c46477110de530bbf191ee39f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80984761"
---
# <a name="add-personal-microsoft-accounts-to-the-microsoft-authenticator-app"></a>Microsoft Authenticator 앱에 개인 Microsoft 계정 추가

표준 2 단계 인증 프로세스와 암호 없는 휴대폰 로그인 모두 Microsoft Authenticator 앱에 Outlook.com 및 Xbox LIVE 계정 등의 개인 Microsoft 계정을 추가 합니다.

- **표준 2단계 인증 방법.** 로그인 하는 장치에 사용자 이름과 암호를 입력 한 다음 Microsoft Authenticator 앱에서 알림을 보낼지 또는 Microsoft Authenticator 앱의 **계정** 화면에서 관련 인증 코드를 복사할지 여부를 선택 합니다.

- **암호 없는 로그인 방법.** 개인 Microsoft 계정를 사용 하 여 로그인 하는 장치에 사용자 이름을 입력 하 고 모바일 장치를 사용 하 여 지문, 얼굴 또는 PIN을 사용 하 고 있는지 확인 합니다. 이 방법은 암호를 입력할 필요가 없습니다.

>[!Important]
>계정을 추가하려면 Microsoft Authenticator 앱을 다운로드하여 설치해야 합니다. [앱 다운로드 및 설치](user-help-auth-app-download-install.md) 문서의 단계를 아직 진행하지 않은 경우 지금 진행하세요.

먼저 2단계 인증을 설정한 다음, 앱에 계정을 추가하여 개인 Microsoft 계정을 추가할 수 있습니다. 계정에 대해 암호 없는 휴대폰 로그인만 사용 하도록 2 단계 인증을 켤 필요는 없지만 추가 계정 보안을 위해 2 단계 인증을 설정 하는 것이 좋습니다.

## <a name="turn-on-two-factor-verification"></a>2단계 인증 설정

1. 컴퓨터에서 [보안 기본 사항](https://account.microsoft.com/security) 페이지로 이동한 다음, 개인 Microsoft 계정을 사용하여 로그인합니다. alain@outlook.com)을 입력합니다.

2. **보안 기본 사항** 페이지 아래쪽에서 **추가 보안 옵션** 링크를 선택합니다.

    ![“추가 보안 옵션” 링크가 강조 표시된 보안 기본 사항 페이지](./media/user-help-auth-app-add-personal-ms-account/more-security-options-link.png)

3. **2단계 인증** 섹션으로 이동하고 기능을 **켜기**로 변경합니다. 기능을 더 이상 개인 계정으로 사용하지 않으려면 여기서 기능을 끌 수도 있습니다.

## <a name="add-your-microsoft-account-to-the-app"></a>앱에 Microsoft 계정 추가

1. 모바일 디바이스에서 Microsoft Authenticator 앱을 엽니다.

1. Android의 오른쪽 위에 있는 **사용자 지정 및 제어** 아이콘에서 **계정 추가** 를 선택 합니다.

    ![Android 계정 선택 페이지](media/user-help-auth-app-add-personal-ms-account/customize-and-control-icon.png)

    IOS에서 오른쪽 위에 있는 더하기 아이콘을 선택 합니다.

    ![iOS 버전의 계정 선택 환경](media/user-help-auth-app-add-personal-ms-account/customize-and-control-icon-ios.png)

1. **계정 추가** 페이지에서 **개인 계정**을 선택합니다.

1. **Microsoft에 로그인** 를 선택 하 여 계정을 추가 합니다. 사용 가능한 경우 QR 코드를 사용할 수 있지만 사용자 이름 및 암호를 사용 하 여 로그인 하 여 언제 든 지 계정을 추가할 수 있습니다.

    ![Microsoft 계정 중 하나를 선택 하거나, 사용 가능한 경우 QR 코드를 스캔 합니다.](media/user-help-auth-app-add-personal-ms-account/add-account-android.png)

1. 적절 한 이메일 주소 (예: alain@outlook.com)를 사용 하 여 개인 계정에 로그인 하 고 **다음**을 선택 합니다.

    >[!Note]
    >개인 Microsoft 계정 없는 경우 [새로 만들 수](https://account.microsoft.com/account/Account?refd=www.bing.com&ru=https%3A%2F%2Faccount.microsoft.com%2F%3Frefd%3Dwww.bing.com&destrt=home-index)있습니다.

1. 암호를 입력 한 다음 **로그인**을 선택 합니다. 개인 계정이 Microsoft Authenticator 앱에 추가됩니다.

## <a name="next-steps"></a>다음 단계

- 앱에 계정을 추가한 후에는 디바이스에서 Authenticator 앱을 사용하여 로그인할 수 있습니다. 자세한 내용은 [앱을 사용하여 로그인](user-help-auth-app-sign-in.md)을 참조하세요.

- 개인 Microsoft 계정에 대 한 확인 코드를 가져오는 데 문제가 있는 경우 [Microsoft 계정 보안 정보 & 확인 코드](https://support.microsoft.com/help/12428/microsoft-account-security-info-verification-codes) 문서에서 **확인 코드 문제 해결** 섹션을 참조 하세요.

- iOS를 실행하는 디바이스의 경우에는 계정 자격 증명 및 관련 앱 설정(예: 계정 순서)을 클라우드에 백업할 수도 있습니다. 자세한 내용은 [Microsoft Authenticator 앱을 통한 백업 및 복구](user-help-auth-app-backup-recovery.md)를 참조하세요.
