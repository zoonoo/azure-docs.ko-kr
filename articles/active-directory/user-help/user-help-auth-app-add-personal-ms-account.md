---
title: Microsoft Authenticator 앱에 개인 Microsoft 계정 추가 - Azure AD
description: 2단계 인증을 사용하는 동안 ID를 확인하기 위해 Microsoft Authenticator 앱에 Outlook.com 또는 Xbox LIVE와 같은 개인 Microsoft 계정을 추가합니다.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 04/08/2020
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: ff83a9a4e4bfd4c27dd3f8d3f212e489c3772eb4
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83741620"
---
# <a name="add-personal-microsoft-accounts-to-the-microsoft-authenticator-app"></a>Microsoft Authenticator 앱에 개인 Microsoft 계정 추가

표준 2단계 인증 프로세스 및 암호 없는 휴대폰 로그인에 모두 사용하도록 Microsoft Authenticator 앱에 Outlook.com 또는 Xbox LIVE 계정과 같은 개인 Microsoft 계정을 추가합니다.

- **표준 2단계 인증 방법.** 로그인하는 데 사용하는 디바이스에 사용자 이름 및 암호를 입력한 다음, Microsoft Authenticator 앱에서 알림을 보낼지, 아니면 Microsoft Authenticator 앱의 **계정** 화면에서 연결된 확인 코드를 복사할지 여부를 선택합니다.

- **암호 없는 로그인 방법.** 개인 Microsoft 계정을 사용하여 로그인하는 디바이스에 사용자 이름을 입력한 다음, 모바일 디바이스에서 지문, 얼굴 또는 PIN을 사용하여 본인이라는 것을 확인합니다. 이 방법은 암호를 입력할 필요가 없습니다.

>[!Important]
>계정을 추가하려면 Microsoft Authenticator 앱을 다운로드하여 설치해야 합니다. [앱 다운로드 및 설치](user-help-auth-app-download-install.md) 문서의 단계를 아직 진행하지 않은 경우 지금 진행하세요.

먼저 2단계 인증을 설정한 다음, 앱에 계정을 추가하여 개인 Microsoft 계정을 추가할 수 있습니다. 계정에 대해 암호 없는 휴대폰 로그인만 사용하려면 2단계 인증을 켤 필요가 없지만 추가 계정 보안을 위해 2단계 인증을 설정하는 것이 좋습니다.

## <a name="turn-on-two-factor-verification"></a>2단계 인증 설정

1. 컴퓨터에서 [보안 기본 사항](https://account.microsoft.com/security) 페이지로 이동한 다음, 개인 Microsoft 계정을 사용하여 로그인합니다. alain@outlook.com)을 입력합니다.

2. **보안 기본 사항** 페이지 아래쪽에서 **추가 보안 옵션** 링크를 선택합니다.

    ![“추가 보안 옵션” 링크가 강조 표시된 보안 기본 사항 페이지](./media/user-help-auth-app-add-personal-ms-account/more-security-options-link.png)

3. **2단계 인증** 섹션으로 이동하고 기능을 **켜기**로 변경합니다. 기능을 더 이상 개인 계정으로 사용하지 않으려면 여기서 기능을 끌 수도 있습니다.

## <a name="add-your-microsoft-account-to-the-app"></a>앱에 Microsoft 계정 추가

1. 모바일 디바이스에서 Microsoft Authenticator 앱을 엽니다.

1. Android에서는 오른쪽 위에 있는 **사용자 지정 및 제어** 아이콘에서 **계정 추가**를 선택합니다.

    ![Android 계정 선택 페이지](media/user-help-auth-app-add-personal-ms-account/customize-and-control-icon.png)

    iOS의 경우 오른쪽 위에서 더하기 아이콘을 선택합니다.

    ![iOS 버전의 계정 선택 환경](media/user-help-auth-app-add-personal-ms-account/customize-and-control-icon-ios.png)

1. **계정 추가** 페이지에서 **개인 계정**을 선택합니다.

1. **Microsoft에 로그인**을 선택하여 계정을 추가합니다. 사용 가능한 경우 QR 코드를 사용할 수 있지만 사용자 이름 및 암호로 로그인하여 언제든지 계정을 추가할 수 있습니다.

    ![Microsoft 계정 중 하나를 선택하거나, 사용 가능한 경우 QR 코드를 스캔합니다.](media/user-help-auth-app-add-personal-ms-account/add-account-android.png)

1. 적절한 메일 주소(예: alain@outlook.com)를 사용하여 개인 계정에 로그인한 후 **다음**을 선택합니다.

    >[!Note]
    >개인 Microsoft 계정이 없는 경우 [계정을 만들 수 있습니다](https://account.microsoft.com/account/Account?refd=www.bing.com&ru=https%3A%2F%2Faccount.microsoft.com%2F%3Frefd%3Dwww.bing.com&destrt=home-index).

1. 암호를 입력한 다음 **로그인**을 선택합니다. 개인 계정이 Microsoft Authenticator 앱에 추가됩니다.

## <a name="next-steps"></a>다음 단계

- 앱에 계정을 추가한 후에는 디바이스에서 Authenticator 앱을 사용하여 로그인할 수 있습니다. 자세한 내용은 [앱을 사용하여 로그인](user-help-auth-app-sign-in.md)을 참조하세요.

- 개인 Microsoft 계정에 대한 확인 코드를 가져오는 데 문제가 있는 경우 **Microsoft 계정 보안 정보 및 확인 코드** 문서에서[ 확인 코드 문제 해결](https://support.microsoft.com/help/12428/microsoft-account-security-info-verification-codes) 섹션을 참조하세요.

- iOS를 실행하는 디바이스의 경우에는 계정 자격 증명 및 관련 앱 설정(예: 계정 순서)을 클라우드에 백업할 수도 있습니다. 자세한 내용은 [Microsoft Authenticator 앱을 통한 백업 및 복구](user-help-auth-app-backup-recovery.md)를 참조하세요.
