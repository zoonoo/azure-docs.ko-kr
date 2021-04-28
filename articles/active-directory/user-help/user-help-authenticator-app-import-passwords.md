---
title: Microsoft Authenticator 앱에 암호 가져오기 - Azure AD
description: 주요 암호 관리자에서 Microsoft 인증 앱으로 암호를 가져오는 방법입니다.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 01/28/2021
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: ecc6580148dfba92077336a26ff9160fbe88eb2c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99806158"
---
# <a name="import-passwords-into-the-microsoft-authenticator-app"></a>Microsoft Authenticator 앱에 암호 가져오기

Microsoft Authenticator는 Google Chrome, Firefox, LastPass, Bitwarden 및 Roboform에서 암호 가져오기를 지원합니다. Microsoft에서 현재 기존 암호 관리자를 지원하지 않는 경우 [템플릿 CSV에 로그인 자격 증명을 수동으로 입력](https://go.microsoft.com/fwlink/?linkid=2134938)할 수 있습니다. Authenticator 앱에서 기존 암호를 가져와 관리하려면 기존 암호 관리자의 암호를 쉼표로 구분된 값(CSV) 형식으로 내보냅니다. 그런 다음, 내보낸 CSV를 Chrome 브라우저 확장의 Authenticator 또는 Authenticator 앱(Android 및 iOS)으로 직접 가져옵니다.

## <a name="import-from-google-chrome-or-android-smart-lock"></a>Google Chrome 또는 Android Smart Lock에서 가져오기

Google Chrome 또는 Android Smart Lock에서 스마트폰 또는 데스크톱 컴퓨터의 Authenticator로 암호를 가져올 수 있습니다. 다음을 할 수 있습니다.

- [Android 및 iOS의 Chrome에서 가져오기](#import-from-chrome-on-android-and-ios)
- [Chrome 데스크톱 브라우저에서 가져오기](#import-from-chrome-desktop-browser)

### <a name="import-from-chrome-on-android-and-ios"></a>Android 및 iOS의 Chrome에서 가져오기

Android 및 Apple 휴대폰의 Google Chrome 사용자는 몇 가지 간단한 단계를 통해 휴대폰에서 직접 암호를 가져올 수 있습니다.

1. 휴대폰에 Authenticator 앱을 설치하고 **암호** 탭을 엽니다.

1. 휴대폰에서 Google Chrome에 로그인합니다.

1. Android 휴대폰의 오른쪽 상단 또는 iOS 디바이스의 오른쪽 하단에 있는 ![Google Chrome 줄임표 메뉴](./media/user-help-authenticator-app-import-passwords/ellipsis-chrome.png)를 탭한 다음, **설정** 을 탭합니다.

   플랫폼 | 링크
   ---------- | --------
   Android | ![Google Chrome 설정 메뉴 위치](./media/user-help-authenticator-app-import-passwords/android-settings-menu.png)
   iOS | ![Google Chrome 설정 메뉴 아이콘](./media/user-help-authenticator-app-import-passwords/apple-settings-menu.png)

1. **설정** 에서 **암호** 를 엽니다.

   플랫폼 | 링크
   ---------- | --------
   Android | ![Android Chrome 암호 명령 위치](./media/user-help-authenticator-app-import-passwords/android-passwords-location.png)
   iOS | ![Apple Chrome 암호 명령 위치](./media/user-help-authenticator-app-import-passwords/apple-passwords-location.png)

1. Android 디바이스의 경우 Android 휴대폰의 오른쪽 상단(iOS 디바이스의 경우 오른쪽 하단)에 있는 ![Google Chrome 줄임표 메뉴](./media/user-help-authenticator-app-import-passwords/ellipsis-chrome.png)를 탭한 다음, **암호 내보내기** 를 탭합니다.

   플랫폼 | 링크
   ---------- | --------
   Android | ![Android Chrome 암호 내보내기 위치](./media/user-help-authenticator-app-import-passwords/android-export-passwords-location.png)
   iOS | ![Apple Chrome 암호 내보내기 위치](./media/user-help-authenticator-app-import-passwords/apple-export-passwords-location.png)

   PIN, 지문 또는 얼굴 인식을 제공해야 합니다. ID를 확인하고 **암호 내보내기** 를 다시 탭하여 내보내기를 시작합니다.

1. 암호를 내보내면 Chrome에서 가져올 앱을 선택하라는 메시지를 표시합니다. **Authenticator** 를 선택하여 암호 가져오기를 시작합니다. 완료되면 가져오기 상태에 대한 정보가 표시됩니다.

   플랫폼 | 링크
   ---------- | --------
   Android | ![Android Chrome 암호 가져오기 위치](./media/user-help-authenticator-app-import-passwords/android-chrome-import.png)
   iOS | ![Apple Chrome 암호 가져오기 위치](./media/user-help-authenticator-app-import-passwords/apple-chrome-import.png)

### <a name="import-from-chrome-desktop-browser"></a>Chrome 데스크톱 브라우저에서 가져오기

시작하기 전에 Chrome 브라우저에 [Microsoft 자동 채우기 확장](https://chrome.google.com/webstore/detail/microsoft-autofill/fiedbfgcleddlbcmgdigjgdfcggjcion)을 설치하고 로그인해야 합니다.

1. 원하는 브라우저에서 [Google 암호 관리자](https://passwords.google.com)를 엽니다. 아직 로그인하지 않은 경우 Google 계정에 로그인합니다.

1. 기어 아이콘 ![데스크톱 암호 관리자 기어 아이콘](./media/user-help-authenticator-app-import-passwords/desktop-password-manager-gear.png) 를 선택하여 암호 설정 페이지를 엽니다.

1. **내보내기** 를 선택하고 다음 페이지에서 **내보내기** 를 다시 선택하여 암호 내보내기를 시작합니다. ID를 확인하라는 메시지가 표시되면 Google 암호를 제공합니다. 완료되면 가져오기 상태에 대한 정보가 표시됩니다.

   ![데스크톱 Chrome 브라우저 암호 내보내기 명령 위치](./media/user-help-authenticator-app-import-passwords/desktop-chrome-export-passwords-location.png)

1. 자동 채우기 Chrome 확장을 열고 **설정** 을 선택합니다.

   ![데스크톱 Chrome 브라우저 자동 채우기 확장 설정 위치](./media/user-help-authenticator-app-import-passwords/desktop-chrome-autofill-settings.png)

1. **데이터 가져오기** 를 선택하여 대화 상자를 엽니다. 그런 다음, **파일 선택** 을 선택하여 CSV 파일을 찾아 가져옵니다.

   ![데스크톱 Chrome 브라우저 데이터 가져오기 CSV 위치](./media/user-help-authenticator-app-import-passwords/desktop-chrome-import-csv.png)

## <a name="import-from-firefox"></a>Firefox에서 가져오기

Firefox를 사용하면 데스크톱 브라우저에서만 암호를 내보낼 수 있으므로, Firefox에서 암호를 가져오기 전에 Firefox 데스크톱 브라우저에 액세스할 수 있는지 확인합니다.

1. 데스크톱에서 최신 버전의 Firefox에 로그인하고 화면 오른쪽 상단에서 ![Firefox "햄버거" 메뉴](./media/user-help-authenticator-app-import-passwords/desktop-firefox-ellipsis-icon.png) 메뉴를 선택합니다.

1. **로그인 및 암호** 를 선택합니다.

   ![데스크톱 Firefox 브라우저 로그인 및 암호 위치](./media/user-help-authenticator-app-import-passwords/desktop-firefox-passwords-location.png)

1. Firefox Lockwise 페이지에서 ![Firefox 줄임표 메뉴](./media/user-help-authenticator-app-import-passwords/desktop-firefox-ellipsis-icon.png) 메뉴를 선택하고 **로그인 내보내기** 를 선택한 다음, **내보내기** 를 선택하여 의도를 확인합니다. PIN, 디바이스 암호를 입력하거나 지문을 스캔하여 본인 확인을 해달라는 메시지가 표시됩니다. 성공적으로 확인되면 Firefox가 사용자의 암호를 선택된 위치에 CSV 형식으로 내보냅니다.

   ![데스크톱 Firefox 브라우저 암호 내보내기 위치](./media/user-help-authenticator-app-import-passwords/desktop-firefox-export-passwords-location.png)

1. 데스크톱 브라우저 또는 iOS나 Android 휴대폰에서 Authenticator로 암호를 가져올 수 있습니다. 휴대폰에서 Authenticator 앱으로 가져오려면 다음을 수행합니다.

      1. 권장되는 안전한 방법을 사용하여 Android 또는 iOS 휴대폰에서 내보낸 CSV 파일을 전송한 후 다운로드합니다. 그런 다음, CSV 파일을 Authenticator 앱과 공유하여 가져오기를 시작합니다.

         플랫폼 | 링크
         ---------- | --------
         Android | ![Android Chrome 암호 가져오기 위치](./media/user-help-authenticator-app-import-passwords/android-chrome-import.png)
         iOS | ![Apple Chrome 암호 가져오기 위치](./media/user-help-authenticator-app-import-passwords/apple-chrome-import.png)

      1. Authenticator로 암호를 성공적으로 가져온 후에는 데스크톱 또는 휴대폰에서 CSV 파일을 삭제합니다.

## <a name="import-from-lastpass"></a>LastPass에서 가져오기

LastPass는 데스크톱 브라우저에서만 암호 내보내기를 지원하므로 암호 가져오기를 시작하려면 데스크톱 브라우저에 액세스할 수 있어야 합니다.

1. [LastPass 웹 사이트](https://lastpass.com)에 로그인하고 **고급 옵션** 을 선택한 다음, **내보내기** 를 선택합니다.

   ![데스크톱 LastPass 암호 내보내기 위치](./media/user-help-authenticator-app-import-passwords/desktop-lastpass-export-passwords-location.png)

1. 메시지가 표시되면 마스터 암호를 제공하여 본인 확인을 합니다. 그러면 웹 페이지에서 내보낸 암호를 볼 수 있습니다.

1. 웹 페이지의 콘텐츠를 복사합니다.

1. 메모장(또는 원하는 텍스트 편집기)을 열고 복사한 콘텐츠를 붙여넣습니다.

1. **파일** &gt; **다른 이름으로 저장** 을 선택하여 이 메모장 파일을 저장합니다. 데스크톱의 안전한 위치에 ".csv"로 끝나는 이름(예: LastPass.csv)을 제공합니다.

   ![데스크톱 LastPass CSV 파일 저장](./media/user-help-authenticator-app-import-passwords/desktop-lastpass-save-import-file.png)

1. 데스크톱 브라우저 또는 iOS나 Android 휴대폰에서 Authenticator로 암호를 가져올 수 있습니다. 휴대폰에서 Authenticator 앱으로 가져오려면 다음을 수행합니다.

      1. 권장되는 안전한 방법을 사용하여 스마트폰에서 내보낸 CSV 파일을 전송한 후 다운로드합니다. 그런 다음, CSV 파일을 Authenticator 앱과 공유하여 가져오기를 시작합니다.

         플랫폼 | 링크
         ---------- | --------
         Android | ![Android LastPass 암호 가져오기 위치](./media/user-help-authenticator-app-import-passwords/android-chrome-import.png)
         iOS | ![Apple LastPass 암호 가져오기 위치](./media/user-help-authenticator-app-import-passwords/apple-chrome-import.png)

      1. Authenticator로 암호를 성공적으로 가져온 후에는 데스크톱 또는 휴대폰에서 CSV 파일을 삭제합니다.

## <a name="import-from-bitwarden"></a>Bitwarden에서 가져오기

Bitwarden은 데스크톱 브라우저에서만 암호 내보내기를 지원하므로 암호 가져오기를 시작하려면 데스크톱 브라우저에 액세스할 수 있어야 합니다.

1. https://vault.bitwarden.com/ 에 로그인하고 **도구** &gt; **자격 증명 모음 내보내기** 를 선택합니다. 파일 형식에 CSV를 선택하고 마스터 암호를 제공한 다음, **자격 증명 모음 내보내기** 를 선택하여 내보내기를 시작합니다.

   ![Bitwarden 자격 증명 모음 내보내기 위치](./media/user-help-authenticator-app-import-passwords/desktop-bitwarden-export-command-location.png)

1. 데스크톱 브라우저 또는 iOS나 Android 휴대폰에서 Authenticator로 암호를 가져올 수 있습니다. 휴대폰에서 Authenticator 앱으로 가져오려면 다음을 수행합니다.

      1. 권장되는 안전한 방법을 사용하여 스마트폰에서 내보낸 CSV 파일을 전송한 후 다운로드합니다. 그런 다음, CSV 파일을 Authenticator 앱과 공유하여 가져오기를 시작합니다.

         플랫폼 | 링크
         ---------- | --------
         Android | ![Android Bitwarden 암호 가져오기 위치](./media/user-help-authenticator-app-import-passwords/android-chrome-import.png)
         iOS | ![Apple Bitwarden 암호 가져오기 위치](./media/user-help-authenticator-app-import-passwords/apple-chrome-import.png)

      1. Authenticator로 암호를 성공적으로 가져온 후에는 데스크톱 또는 휴대폰에서 CSV 파일을 삭제합니다.

## <a name="import-from-roboform"></a>Roboform에서 가져오기

Roboform은 데스크톱 앱에서만 암호 내보내기를 허용하므로 가져오기를 시작하려면 데스크톱의 Roboform 앱에 액세스할 수 있어야 합니다.

1. 데스크톱 클라이언트에서 RoboForm을 시작하고 계정에 로그인합니다.

1. **Roboform** 메뉴에서 **옵션** 을 선택합니다.

   ![Desktop Roboform 옵션 메뉴](./media/user-help-authenticator-app-import-passwords/desktop-roboform-options.png)

1. **계정 및 데이터**&gt;**내보내기** 를 선택합니다.

   ![데스크톱 Roboform 내보내기 명령 위치](./media/user-help-authenticator-app-import-passwords/desktop-roboform-accounts-data.png)

1. 내보낸 파일을 저장할 안전한 위치를 선택합니다. **데이터** 형식에 **로그인** 을 선택하고 CSV 파일을 형식으로 선택한 다음, **내보내기** 를 선택합니다.

   ![데스크톱 Roboform 내보내기 대화 상자](./media/user-help-authenticator-app-import-passwords/desktop-roboform-export-dialog.png)

1. 의도를 확인하면 CSV 파일이 선택한 위치로 내보내집니다.

   ![데스크톱 Roboform 내보내기 확인 대화 상자](./media/user-help-authenticator-app-import-passwords/desktop-roboform-confirmation.png)

1. 데스크톱 브라우저 또는 iOS나 Android 휴대폰에서 Authenticator로 암호를 가져올 수 있습니다. 휴대폰에서 Authenticator 앱으로 가져오려면 다음을 수행합니다.

      1. 권장되는 안전한 방법을 사용하여 스마트폰에서 내보낸 CSV 파일을 전송한 후 다운로드합니다. 그런 다음, CSV 파일을 Authenticator 앱과 공유하여 가져오기를 시작합니다.

         플랫폼 | 링크
         ---------- | --------
         Android | ![Android Roboform 암호 가져오기 위치](./media/user-help-authenticator-app-import-passwords/android-chrome-import.png)
         iOS | ![Apple Roboform 암호 가져오기 위치](./media/user-help-authenticator-app-import-passwords/apple-chrome-import.png)

      1. Authenticator로 암호를 성공적으로 가져온 후에는 데스크톱 또는 휴대폰에서 CSV 파일을 삭제합니다.

## <a name="import-by-creating-a-csv"></a>CSV를 만들어 가져오기

사용 중인 암호 관리자에서 암호를 가져오는 단계가 이 문서에 나열되지 않은 경우, Authenticator로 암호를 가져오는 데 사용할 수 있는 CSV를 만들 수 있습니다. 서식을 쉽게 지정할 수 있도록 데스크톱에서 이러한 단계를 수행하는 것이 좋습니다.

1. 데스크톱에서 [가져오기 템플릿을 다운로드하고 엽니다](https://go.microsoft.com/fwlink/?linkid=2134938). Apple iPhone, Safari 및 Keychain 사용자는 이제 4단계로 건너뛸 수 있습니다.

1. 기존 암호 관리자에서 암호화되지 않은 CSV 파일로 암호를 내보냅니다.

1. 내보낸 CSV의 관련 열을 템플릿 CSV에 복사한 후 저장합니다.

1. 내보낸 CSV가 없는 경우 기존 암호 관리자의 각 로그인을 템플릿 CSV에 복사할 수 있습니다. 헤더 행을 제거하거나 변경하지 마세요. 완료되면 다음 단계를 시작하기 전에 데이터의 무결성을 확인합니다.

1. 데스크톱 브라우저 또는 iOS나 Android 휴대폰에서 Authenticator로 암호를 가져올 수 있습니다. 휴대폰에서 Authenticator 앱으로 가져오려면 다음을 수행합니다.

      1. 권장되는 안전한 방법을 사용하여 스마트폰에서 내보낸 CSV 파일을 전송한 후 다운로드합니다. 그런 다음, CSV 파일을 Authenticator 앱과 공유하여 가져오기를 시작합니다.

         플랫폼 | 링크
         ---------- | --------
         Android | ![Android CSV 암호 가져오기 위치](./media/user-help-authenticator-app-import-passwords/android-chrome-import.png)
         iOS | ![Apple CSV 암호 가져오기 위치](./media/user-help-authenticator-app-import-passwords/apple-chrome-import.png)

      1. Authenticator로 암호를 성공적으로 가져온 후에는 데스크톱 또는 휴대폰에서 CSV 파일을 삭제합니다.

## <a name="troubleshooting-steps"></a>문제 해결 단계

가져오기 실패의 가장 일반적인 원인은 CSV 파일의 잘못된 형식입니다. 다음 단계를 시도하여 문제를 해결할 수 있습니다.

- 현재 암호 관리자에서 암호 가져오기를 이미 지원하는지 확인하려면 이 문서를 확인하세요. 지원하는 경우 해당 공급자와 관련하여 명시된 단계를 수행하여 가져오기를 다시 시도할 수 있습니다.

- 현재 암호 관리자의 형식 가져오기가 지원되지 않는 경우 [CSV 파일을 수동으로 만들어](#import-by-creating-a-csv) 다시 시도할 수 있습니다.

- 다음 제안 사항을 통해 CSV 데이터의 무결성을 확인할 수 있습니다.

  - 첫 번째 행은 **url**, **username** 및 **password** 라는 세 개의 열이 있는 헤더를 포함해야 합니다.

  - 각 행은 **url** 및 **passwords** 열 아래에 값을 포함해야 합니다.

- [CSV 템플릿 파일](https://go.microsoft.com/fwlink/?linkid=2134938)에 콘텐츠를 붙여넣어 CSV를 다시 만들 수 있습니다.

- 어떤 방법도 효과가 없다면 Authenticator 앱 설정에서 **사용자 의견 보내기** 링크를 사용하여 문제를 보고하세요.
