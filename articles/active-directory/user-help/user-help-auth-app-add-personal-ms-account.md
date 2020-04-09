---
title: 마이크로소프트 인증자 응용 프로그램에 개인 Microsoft 계정을 추가 - Azure AD
description: Outlook.com 또는 Xbox LIVE와 같은 개인 Microsoft 계정을 Microsoft 인증자 앱에 추가하여 2단계 확인을 사용하는 동안 사용자의 신원을 확인합니다.
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
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/09/2020
ms.locfileid: "80984761"
---
# <a name="add-personal-microsoft-accounts-to-the-microsoft-authenticator-app"></a>마이크로소프트 인증자 응용 프로그램에 개인 Microsoft 계정을 추가

표준 2단계 확인 프로세스와 암호없는 전화 로그인 모두에 대해 microsoft 인증자 앱에 Outlook.com 및 Xbox LIVE 계정과 같은 개인 Microsoft 계정을 추가합니다.

- **표준 2단계 인증 방법.** 로그인하는 장치에 사용자 이름과 암호를 입력한 다음 Microsoft 인증자 앱이 알림을 보낼지 또는 Microsoft 인증자 앱의 **계정** 화면에서 연결된 확인 코드를 복사할지 여부를 선택합니다.

- **암호 없는 로그인 방법.** 개인 Microsoft 계정을 사용하기 위해 로그인하는 장치에 사용자 이름을 입력한 다음 모바일 장치를 사용하여 지문, 얼굴 또는 PIN을 사용하고 있는지 확인합니다. 이 방법은 암호를 입력할 필요가 없습니다.

>[!Important]
>계정을 추가하려면 Microsoft Authenticator 앱을 다운로드하여 설치해야 합니다. [앱 다운로드 및 설치](user-help-auth-app-download-install.md) 문서의 단계를 아직 진행하지 않은 경우 지금 진행하세요.

먼저 2단계 인증을 설정한 다음, 앱에 계정을 추가하여 개인 Microsoft 계정을 추가할 수 있습니다. 계정에 암호가 없는 전화 로그인만 사용하려면 2단계 인증을 켤 필요가 없지만 추가 계정 보안을 위해 2단계 인증을 사용하는 것이 좋습니다.

## <a name="turn-on-two-factor-verification"></a>2단계 인증 설정

1. 컴퓨터에서 [보안 기본 사항](https://account.microsoft.com/security) 페이지로 이동한 다음, 개인 Microsoft 계정을 사용하여 로그인합니다. alain@outlook.com)을 입력합니다.

2. **보안 기본 사항** 페이지 아래쪽에서 **추가 보안 옵션** 링크를 선택합니다.

    ![“추가 보안 옵션” 링크가 강조 표시된 보안 기본 사항 페이지](./media/user-help-auth-app-add-personal-ms-account/more-security-options-link.png)

3. **2단계 인증** 섹션으로 이동하고 기능을 **켜기**로 변경합니다. 기능을 더 이상 개인 계정으로 사용하지 않으려면 여기서 기능을 끌 수도 있습니다.

## <a name="add-your-microsoft-account-to-the-app"></a>앱에 Microsoft 계정 추가

1. 모바일 디바이스에서 Microsoft Authenticator 앱을 엽니다.

1. Android에서 오른쪽 상단에 있는 **사용자 지정 및 제어** 아이콘에서 계정 **추가를** 선택합니다.

    ![안드로이드 계정 선택 페이지](media/user-help-auth-app-add-personal-ms-account/customize-and-control-icon.png)

    iOS에서 오른쪽 상단에 있는 더하기 아이콘을 선택합니다.

    ![계정 선택 환경의 iOS 버전](media/user-help-auth-app-add-personal-ms-account/customize-and-control-icon-ios.png)

1. **계정 추가** 페이지에서 **개인 계정**을 선택합니다.

1. 계정을 추가하려면 **Microsoft에서 로그인을** 선택합니다. QR 코드는 사용 가능한 경우 사용할 수 있지만 언제든지 사용자 이름과 암호로 로그인하여 계정을 추가할 수 있습니다.

    ![Microsoft 계정을 선택하거나 사용 가능한 경우 QR 코드를 스캔합니다.](media/user-help-auth-app-add-personal-ms-account/add-account-android.png)

1. 적절한 이메일 alain@outlook.com주소(예: )를 사용하여 개인 계정에 로그인한 다음 **다음을**선택합니다.

    >[!Note]
    >개인 Microsoft 계정이 없는 경우 [.](https://account.microsoft.com/account/Account?refd=www.bing.com&ru=https%3A%2F%2Faccount.microsoft.com%2F%3Frefd%3Dwww.bing.com&destrt=home-index)

1. 암호를 입력한 다음 **로그인을**선택합니다. 개인 계정이 Microsoft Authenticator 앱에 추가됩니다.

## <a name="next-steps"></a>다음 단계

- 앱에 계정을 추가한 후에는 디바이스에서 Authenticator 앱을 사용하여 로그인할 수 있습니다. 자세한 내용은 [앱을 사용하여 로그인](user-help-auth-app-sign-in.md)을 참조하세요.

- 개인 Microsoft 계정에 대한 확인 코드를 가져오는 데 문제가 있는 경우 Microsoft 계정 [보안 정보 & 확인 코드](https://support.microsoft.com/help/12428/microsoft-account-security-info-verification-codes) 문서의 문제 해결 확인 코드 **문제** 섹션을 참조하세요.

- iOS를 실행하는 디바이스의 경우에는 계정 자격 증명 및 관련 앱 설정(예: 계정 순서)을 클라우드에 백업할 수도 있습니다. 자세한 내용은 [Microsoft Authenticator 앱을 통한 백업 및 복구](user-help-auth-app-backup-recovery.md)를 참조하세요.
