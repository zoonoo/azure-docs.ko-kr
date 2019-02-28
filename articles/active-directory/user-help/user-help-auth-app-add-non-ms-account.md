---
title: Microsoft Authenticator 앱에 비 Microsoft 계정 추가 - Azure Active Directory | Microsoft Docs
description: 2단계 인증용으로 Microsoft Authenticator 앱에 Google, Facebook, GitHub 등의 비 Microsoft 계정을 추가하는 방법을 설명합니다.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 01/24/2019
ms.author: lizross
ms.reviewer: olhaun
ms.collection: M365-identity-device-management
ms.openlocfilehash: e6f94ba30c06fc6975ab212c895cecefe5d383fa
ms.sourcegitcommit: 75fef8147209a1dcdc7573c4a6a90f0151a12e17
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/20/2019
ms.locfileid: "56453650"
---
# <a name="add-your-non-microsoft-accounts"></a>비 Microsoft 계정 추가
2단계 인증용으로 Microsoft Authenticator 앱에 Google, Facebook, GitHub 등의 비 Microsoft 계정을 추가합니다. Microsoft Authenticator 앱은 2단계 인증을 사용하는 모든 앱 및 TOTP(시간 제약이 있는 일회성 암호) 표준을 지원하는 모든 계정과 호환됩니다.

>[!Important]
>계정을 추가하려면 Microsoft Authenticator 앱을 다운로드하여 설치해야 합니다. [앱 다운로드 및 설치](user-help-auth-app-download-install.md) 문서의 단계를 아직 진행하지 않은 경우 지금 진행하세요.

## <a name="add-personal-accounts"></a>개인 계정 추가
일반적으로 모든 개인 계정에 대해 다음 단계를 수행해야 합니다.

1. 계정에 로그인한 다음, 사용 중인 디바이스나 컴퓨터를 통해 2단계 인증을 설정합니다.

2. Microsoft Authenticator 앱에 계정을 추가합니다. 이 프로세스의 일부로 QR 코드를 스캔하라는 메시지가 표시될 수 있습니다.

이 문서에서는 Facebook, Google, GitHub 및 Amazon 계정용 프로세스를 설명하지만 Instagram, Netflix, Adobe 등의 기타 앱에서도 동일한 프로세스를 수행하면 됩니다.

## <a name="add-your-google-account"></a>Google 계정 추가
2단계 인증을 설정한 다음, 앱에 계정을 추가하는 방식으로 Google 계정을 추가합니다.

### <a name="turn-on-two-factor-verification"></a>2단계 인증 설정

1. 컴퓨터에서 https://myaccount.google.com/signinoptions/two-step-verification/enroll-welcome으로 이동하여 **시작하기**를 선택한 다음, ID를 확인합니다.

2. 페이지에 표시되는 단계에 따라 개인 Google 계정에 2단계 인증을 설정합니다.

### <a name="add-your-google-account-to-the-app"></a>앱에 Google 계정 추가

1. 컴퓨터에 표시된 Google 페이지에서 **두 번째 대체 단계 설정** 섹션으로 이동하여 **Authenticator 앱** 섹션에서 **설정**을 선택합니다.

2. **Authenticator 앱에서 코드 받기** 페이지에서 사용 중인 휴대폰 유형에 따라 **Android** 또는 **iPhone**을 선택하고, **다음**을 선택합니다.

    Microsoft Authenticator 앱과 계정을 자동으로 연결하는 데 사용할 수 있는 QR 코드가 전송됩니다. 이 창을 닫지 마세요.

3. Microsoft Authenticator 앱을 열고 오른쪽 위에 있는 **사용자 지정 및 제어** 아이콘에서 **계정 추가**를 선택한 다음, **기타 계정(Google, Facebook 등)** 을 선택합니다.

4. 디바이스 카메라를 사용하여 컴퓨터의 **Authenticator 설정** 페이지에서 QR 코드를 스캔합니다.

    >[!Note]
    >카메라가 올바르게 작동하지 않으면 QR 코드 및 URL을 수동으로 입력할 수 있습니다.

5. 디바이스의 Microsoft Authenticator 앱 **계정** 페이지를 검토하여 계정 정보가 올바르며 6자리 확인 코드가 연결되어 있는지 확인합니다.

    보안 수준을 높이기 위해 확인 코드는 30초마다 변경되므로 코드 하나를 여러 번 사용할 수 없습니다.

6. 컴퓨터의 **Authenticator 설정** 페이지에서 **다음**을 선택하고, 앱에 제공된 Google 계정용 6자리 확인 코드를 입력한 다음, **확인**을 선택합니다.

7. 계정이 확인되면 **완료**를 선택하여 **Authenticator 설정** 페이지를 닫을 수 있습니다.

    >[!NOTE]
    >2단계 인증 및 Google 계정에 대한 자세한 내용은 [2단계 인증 설정](https://support.google.com/accounts/answer/185839) 및 [2단계 인증에 대해 자세히 알아보기](https://www.google.com/landing/2step/help.html)를 참조하세요.

## <a name="add-your-facebook-account"></a>Facebook 계정 추가
2단계 인증을 설정한 다음, 앱에 계정을 추가하는 방식으로 Facebook 계정을 추가합니다.

### <a name="turn-on-two-factor-verification"></a>2단계 인증 설정

1. 컴퓨터에서 Facebook을 열고 오른쪽 위의 드롭다운 메뉴를 선택한 다음, **설정** > **보안 및 로그인**으로 이동합니다.

    **보안 및 로그인** 페이지가 표시됩니다.

2. **2단계 인증** 섹션의 아래쪽에 있는 **2단계 인증 사용**으로 이동한 다음, **수정**을 선택합니다.

    **2단계 인증** 페이지가 표시됩니다.

3. **시작하기**를 선택합니다.

### <a name="add-your-facebook-account-to-the-app"></a>앱에 Facebook 계정 추가

1. 컴퓨터의 Facebook 페이지에서 **백업 추가** 섹션으로 이동한 다음, **Authentication 앱** 영역에서 **설정**을 선택합니다.

    Microsoft Authenticator 앱과 계정을 자동으로 연결하는 데 사용할 수 있는 QR 코드가 전송됩니다. 이 창을 닫지 마세요.

2. Microsoft Authenticator 앱을 열고 오른쪽 위에 있는 **사용자 지정 및 제어** 아이콘에서 **계정 추가**를 선택한 다음, **기타 계정(Google, Facebook 등)** 을 선택합니다.

3. 디바이스 카메라를 사용하여 컴퓨터의 **2단계 인증** 페이지에서 QR 코드를 스캔합니다.

    >[!Note]
    >카메라가 올바르게 작동하지 않으면 QR 코드 및 URL을 수동으로 입력할 수 있습니다.

4. 디바이스의 Microsoft Authenticator 앱 **계정** 페이지를 검토하여 계정 정보가 올바르며 6자리 확인 코드가 연결되어 있는지 확인합니다.

    보안 수준을 높이기 위해 확인 코드는 30초마다 변경되므로 코드 하나를 여러 번 사용할 수 없습니다.

5. 컴퓨터의 **2단계 인증** 페이지에서 **다음**을 선택하고, 앱에 제공된 Facebook 계정용 6자리 확인 코드를 입력합니다.

    계정이 확인됩니다. 그러면 앱을 사용하여 계정을 확인할 수 있습니다.

    >[!NOTE]
    >2단계 인증 및 Facebook 계정에 대한 자세한 내용은 [2단계 인증의 정의와 작동 방식](https://www.facebook.com/help/148233965247823)을 참조하세요.

## <a name="add-your-github-account"></a>GitHub 계정 추가
2단계 인증을 설정한 다음, 앱에 계정을 추가하는 방식으로 GitHub 계정을 추가합니다.

### <a name="turn-on-two-factor-verification"></a>2단계 인증 설정

1. 컴퓨터에서 GitHub를 열고 오른쪽 위에서 사용자 이미지를 선택한 다음, **설정**을 선택합니다.

    **2단계 인증** 페이지가 표시됩니다.

2. **개인 설정** 사이드바에서 **보안**을 선택하고, **2단계 인증** 영역에서 **2단계 인증 사용**을 선택합니다.

### <a name="add-your-github-account-to-the-app"></a>앱에 GitHub 계정 추가

1. 컴퓨터의 **2단계 인증** 페이지에서 **앱을 사용하여 설정**을 선택합니다.

2. 액세스 권한이 없어져도 계정에 다시 액세스할 수 있도록 복구 코드를 저장하고, **다음**을 선택합니다. 

    코드는 디바이스에 다운로드하거나, 하드 카피를 인쇄하거나, 암호 관리자 도구에 복사하는 방식으로 저장할 수 있습니다.

3. **2단계 인증** 페이지에서 **앱을 사용하여 설정**을 선택합니다.

    페이지가 변경되어 QR 코드가 표시됩니다. 이 페이지를 닫지 마세요.

4. Microsoft Authenticator 앱을 열고, 오른쪽 위에 있는 **사용자 지정 및 제어** 아이콘에서 **계정 추가**를 선택하고, **기타 계정(Google, Facebook 등)** 을 선택한 다음, 페이지 위쪽의 텍스트에서 **이 텍스트 코드 입력**을 선택합니다.

    Microsoft Authenticator 앱은 코드를 스캔할 수 없기 때문에 수동으로 코드를 입력해야 합니다.

5. **계정 이름**을 입력하고(예: GitHub), 4단계의 **비밀 키**를 입력한 다음, **마침**을 선택합니다.

4. 컴퓨터의 **2단계 인증** 페이지에서, 앱에 제공된 GitHub 계정용 6자리 확인 코드를 입력한 다음, **사용**을 선택합니다.

    앱의 **계정** 페이지에 사용자 계정 이름 및 6자리 확인 코드가 표시됩니다. 보안 수준을 높이기 위해 확인 코드는 30초마다 변경되므로 코드 하나를 여러 번 사용할 수 없습니다.

    >[!NOTE]
    >2단계 인증 및 GitHub 계정에 대한 자세한 내용은 [2단계 인증 정보](https://help.github.com/articles/about-two-factor-authentication/)를 참조하세요.

## <a name="add-your-amazon-account"></a>Amazon 계정 추가
2단계 인증을 설정한 다음, 앱에 계정을 추가하는 방식으로 Amazon 계정을 추가합니다.

### <a name="turn-on-two-factor-verification"></a>2단계 인증 설정

1. 컴퓨터에서 Amazon을 열고 **계정 및 목록** 드롭다운 메뉴를 선택한 다음, **계정**을 선택합니다.

2. **로그인 및 보안**을 선택하고, Amazon 계정에 로그인한 다음, **고급 보안 설정** 영역에서 **편집**을 선택합니다.

    **고급 보안 설정** 페이지가 표시됩니다.

3. **시작**을 선택합니다.

4. **코드를 받을 방법 선택** 페이지에서 **Authenticator 앱**을 선택합니다.

    페이지가 변경되어 QR 코드가 표시됩니다. 이 페이지를 닫지 마세요.

5. Microsoft Authenticator 앱을 열고 오른쪽 위에 있는 **사용자 지정 및 제어** 아이콘에서 **계정 추가**를 선택한 다음, **기타 계정(Google, Facebook 등)** 을 선택합니다.

6. 디바이스 카메라를 사용하여 컴퓨터의 **코드를 받을 방법 선택** 페이지에서 QR 코드를 스캔합니다.

    >[!Note]
    >카메라가 올바르게 작동하지 않으면 QR 코드 및 URL을 수동으로 입력할 수 있습니다.

5. 디바이스의 Microsoft Authenticator 앱 **계정** 페이지를 검토하여 계정 정보가 올바르며 6자리 확인 코드가 연결되어 있는지 확인합니다.

    보안 수준을 높이기 위해 확인 코드는 30초마다 변경되므로 코드 하나를 여러 번 사용할 수 없습니다.

6. 컴퓨터의 **코드를 받을 방법 선택** 페이지에서 앱에 제공된 Amazon 계정용 6자리 확인 코드를 입력한 다음, **코드 확인 후 계속하기**를 선택합니다.

7. 문자 메시지 등의 백업 확인 방법을 추가하는 등 나머지 등록 프로세스를 완료하고, **코드 전송**을 선택합니다.

8. 컴퓨터의 **백업 확인 방법 추가** 페이지에서 백업 확인 방법을 통해 제공된 Amazon 계정용 6자리 확인 코드를 입력한 다음, **코드 확인 후 계속하기**를 선택합니다.

9. [거의 완료됨] 페이지에서 컴퓨터를 신뢰할 수 있는 디바이스로 설정할지 여부를 결정한 다음, **완료. 2단계 인증 설정**을 선택합니다.

    **고급 보안 설정** 페이지가 나타나고, 업데이트된 2단계 인증 세부 정보가 표시됩니다.

    >[!NOTE]
    >2단계 인증 및 Amazon 계정에 대한 자세한 내용은 [2단계 인증 정보](https://www.amazon.com/gp/help/customer/display.html?nodeId=201596330) 및 [2단계 인증을 사용하여 로그인](https://www.amazon.com/gp/help/customer/display.html?nodeId=201962440)을 참조하세요.


## <a name="next-steps"></a>다음 단계

- 앱에 계정을 추가한 후에는 디바이스에서 Authenticator 앱을 사용하여 로그인할 수 있습니다. 자세한 내용은 [앱을 사용하여 로그인](user-help-auth-app-sign-in.md)을 참조하세요.

- iOS를 실행하는 디바이스의 경우에는 계정 자격 증명 및 관련 앱 설정(예: 계정 순서)을 클라우드에 백업할 수도 있습니다. 자세한 내용은 [Microsoft Authenticator 앱을 통한 백업 및 복구](user-help-auth-app-backup-recovery.md)를 참조하세요.
