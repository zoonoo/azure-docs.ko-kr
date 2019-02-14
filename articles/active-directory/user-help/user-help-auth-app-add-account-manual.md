---
title: 수동으로 앱에 계정 추가 - Azure Active Directory | Microsoft Docs
description: 2단계 인증을 위해 Microsoft Authenticator 앱에 계정을 수동으로 추가하는 방법을 설명합니다.
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
ms.openlocfilehash: e0d46fc8a152c4426832669ce22d4aa78ac640e6
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56173945"
---
# <a name="manually-add-an-account-to-the-app"></a>수동으로 앱에 계정 추가
카메라가 QR 코드를 캡처할 수 없는 경우에는 2단계 인증을 위해 Microsoft Authenticator 앱에 계정 정보를 수동으로 추가할 수 있습니다. 회사 또는 학교 계정과 비 Microsoft 계정도 수동으로 추가할 수 있습니다.

계정에 제공된 코드는 대/소문자를 구분하지 않고 Microsoft Authenticator 앱에 추가할 때 공백이 없어야 합니다.

>[!Important]
>계정을 추가하려면 Microsoft Authenticator 앱을 다운로드하여 설치해야 합니다. [앱 다운로드 및 설치](user-help-auth-app-download-install.md) 문서의 단계를 아직 진행하지 않은 경우 지금 진행하세요.

## <a name="add-your-work-or-school-account"></a>회사 또는 학교 계정 추가

1. PC에서 **모바일 앱 구성** 페이지의 **코드** 및 **URL** 정보를 기록해 둡니다. 코드와 URL을 볼 수 있도록 이 페이지를 열어 둡니다.
    
    ![QR 코드를 제공하는 화면](./media/user-help-auth-app-add-account-manual/auth-app-barcode.png)

2. Microsoft Authenticator 앱을 열고 오른쪽 위에 있는 **사용자 지정 및 제어** 아이콘에서 **계정 추가**를 선택한 다음, **회사 또는 학교 계정**을 선택합니다.

3. **그렇지 않은 경우 수동으로 코드 입력**을 선택합니다.

    ![QR 코드를 스캔하는 화면](./media/user-help-auth-app-add-account-manual/auth-app-manual-code.png)
   
4. 1단계에서 **코드** 및 **URL**을 입력한 다음, **마침**을 선택합니다.

    ![코드와 URL을 입력하기 위한 화면](./media/user-help-auth-app-add-account-manual/auth-app-code-url.png)

    앱의 **계정** 화면에 사용자 계정 이름 및 6자리 확인 코드가 표시됩니다. 보안 수준을 높이기 위해 확인 코드는 30초마다 변경되므로 코드 하나를 여러 번 사용할 수 없습니다.

## <a name="add-your-google-account"></a>Google 계정 추가

1. PC에서, QR 코드가 있는 **인증자 설정** 페이지에서 **스캔할 수 없음**을 선택합니다.

    비밀 코드가 있는 **바코드를 스캔할 수 없음** 페이지가 표시됩니다. 비밀 코드를 볼 수 있도록 이 페이지를 열어 둡니다.

2. Microsoft Authenticator 앱을 열고, 오른쪽 위에 있는 **사용자 지정 및 제어** 아이콘에서 **계정 추가**를 선택하고, **기타 계정(Google, Facebook 등)** 을 선택한 다음, **그렇지 않은 경우 수동으로 코드 입력**을 선택합니다.

3. **계정 이름**을 입력하고(예: Google), 1단계의 **비밀 키**를 입력한 다음, **마침**을 선택합니다.

4. PC의 **Authenticator 설정** 페이지에서 앱에 제공된 Google 계정용 6자리 확인 코드를 입력한 다음, **확인**을 선택합니다.

    앱의 **계정** 화면에 사용자 계정 이름 및 6자리 확인 코드가 표시됩니다. 보안 수준을 높이기 위해 확인 코드는 30초마다 변경되므로 코드 하나를 여러 번 사용할 수 없습니다.

    >[!NOTE]
    >2단계 인증 및 Google 계정에 대한 자세한 내용은 [2단계 인증 설정](https://support.google.com/accounts/answer/185839) 및 [2단계 인증에 대해 자세히 알아보기](https://www.google.com/landing/2step/help.html)를 참조하세요.

## <a name="add-your-facebook-account"></a>Facebook 계정 추가

1. **타사 인증자를 통해 설정** 페이지에는 QR 코드 및 앱에 입력하도록 작성된 코드가 포함되어 있습니다. 코드를 볼 수 있도록 이 페이지를 열어 둡니다.

2. Microsoft Authenticator 앱을 열고, 오른쪽 위에 있는 **사용자 지정 및 제어** 아이콘에서 **계정 추가**를 선택하고, **기타 계정(Google, Facebook 등)** 을 선택한 다음, **그렇지 않은 경우 수동으로 코드 입력**을 선택합니다.

3. **계정 이름**을 입력하고(예: Facebook), 1단계의 **비밀 키**를 입력한 다음, **마침**을 선택합니다.

4. PC의 **2단계 인증자** 페이지에서 앱에 제공된 Facebook 계정용 6자리 확인 코드를 입력한 다음, **확인**을 선택합니다.

    앱의 **계정** 화면에 사용자 계정 이름 및 6자리 확인 코드가 표시됩니다. 보안 수준을 높이기 위해 확인 코드는 30초마다 변경되므로 코드 하나를 여러 번 사용할 수 없습니다.

    >[!NOTE]
    >2단계 인증 및 Facebook 계정에 대한 자세한 내용은 [2단계 인증의 정의와 작동 방식](https://www.facebook.com/help/148233965247823)을 참조하세요.

## <a name="add-your-amazon-account"></a>Amazon 계정 추가
2단계 인증을 설정한 다음, 앱에 계정을 추가하는 방식으로 Amazon 계정을 추가할 수 있습니다.

1. PC에서, QR 코드가 있는 **코드를 받을 방법 선택** 페이지에서 **바코드를 스캔할 수 없음**을 선택합니다.

    비밀 코드가 있는 **바코드를 스캔할 수 없음** 메시지가 표시됩니다. 비밀 코드를 볼 수 있도록 이 메시지를 열어 둡니다.

2. Microsoft Authenticator 앱을 열고, 오른쪽 위에 있는 **사용자 지정 및 제어** 아이콘에서 **계정 추가**를 선택하고, **기타 계정(Google, Facebook 등)** 을 선택한 다음, **그렇지 않은 경우 수동으로 코드 입력**을 선택합니다.

3. **계정 이름**을 입력하고(예: Amazon), 1단계의 **비밀 키**를 입력한 다음, **마침**을 선택합니다.

4. 문자 메시지 등의 백업 확인 방법을 추가하는 등 나머지 등록 프로세스를 완료하고, **코드 전송**을 선택합니다.

5. PC의 **백업 확인 방법 추가** 페이지에서, Amazon 계정용 백업 확인 방법을 통해 제공된 6자리 확인 코드를 입력한 다음, **코드 확인 후 계속하기**를 선택합니다.

6. [거의 완료됨] 페이지에서 PC를 신뢰하는 디바이스로 설정할지 여부를 결정한 다음, **완료. 2단계 인증 설정**을 선택합니다.

    **고급 보안 설정** 페이지가 나타나고, 업데이트된 2단계 인증 세부 정보가 표시됩니다.

    >[!NOTE]
    >2단계 인증 및 Amazon 계정에 대한 자세한 내용은 [2단계 인증 정보](https://www.amazon.com/gp/help/customer/display.html?nodeId=201596330) 및 [2단계 인증을 사용하여 로그인](https://www.amazon.com/gp/help/customer/display.html?nodeId=201962440)을 참조하세요.    

## <a name="next-steps"></a>다음 단계

- 앱에 계정을 추가한 후에는 디바이스에서 Microsoft Authenticator 앱을 사용하여 로그인할 수 있습니다. 자세한 내용은 [앱을 사용하여 로그인](user-help-auth-app-sign-in.md)을 참조하세요.

- iOS를 실행하는 디바이스의 경우에는 계정 자격 증명 및 관련 앱 설정(예: 계정 순서)을 클라우드에 백업할 수도 있습니다. 자세한 내용은 [Microsoft Authenticator 앱을 통한 백업 및 복구](user-help-auth-app-backup-recovery.md)를 참조하세요.
