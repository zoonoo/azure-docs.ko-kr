---
title: Microsoft Authenticator 앱에 암호 가져오기-Azure AD
description: 자주 사용 하는 암호 관리자에서 Microsoft 인증 앱으로 암호를 가져오는 방법입니다.
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
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/07/2021
ms.locfileid: "99806158"
---
# <a name="import-passwords-into-the-microsoft-authenticator-app"></a>Microsoft Authenticator 앱에 암호 가져오기

Microsoft Authenticator Google Chrome, Firefox, LastPass, Bitwarden 및 Roboform에서 암호 가져오기를 지원 합니다. Microsoft에서 현재 기존 암호 관리자를 지원 하지 않는 경우 [템플릿 CSV에 로그인 자격 증명을 수동으로 입력할](https://go.microsoft.com/fwlink/?linkid=2134938)수 있습니다. 기존 암호를 가져와 Authenticator 앱에서 관리 하려면 기존 암호 관리자의 암호를 쉼표로 구분 된 값 (CSV) 형식으로 내보냅니다. 그런 다음, 내보낸 CSV를 Chrome 브라우저 확장의 인증자로 또는 인증자 앱 (Android 및 iOS)에 직접 가져옵니다.

## <a name="import-from-google-chrome-or-android-smart-lock"></a>Google Chrome 또는 Android에서 가져오기 Smart Lock

Google Chrome 또는 Android Smart Lock에서 스마트폰 또는 데스크톱 컴퓨터의 인증자로 암호를 가져올 수 있습니다. 다음과 같습니다.

- [Android 및 iOS의 Chrome에서 가져오기](#import-from-chrome-on-android-and-ios)
- [Chrome 데스크톱 브라우저에서 가져오기](#import-from-chrome-desktop-browser)

### <a name="import-from-chrome-on-android-and-ios"></a>Android 및 iOS의 Chrome에서 가져오기

Android 및 Apple 휴대폰의 Google Chrome 사용자는 몇 가지 간단한 단계를 통해 휴대폰에서 직접 암호를 가져올 수 있습니다.

1. 휴대폰에 Authenticator 앱을 설치 하 고 **암호** 탭을 엽니다.

1. 휴대폰에서 Google Chrome에 로그인 합니다.

1. ![ ](./media/user-help-authenticator-app-import-passwords/ellipsis-chrome.png) Android 휴대폰의 오른쪽 위 또는 iOS 장치에 대 한 오른쪽 아래에 있는 Google Chrome 줄임표 메뉴를 탭 한 다음 설정을 탭 합니다 **.**

   플랫폼 | 링크
   ---------- | --------
   Android | ![Google Chrome 설정 메뉴 위치](./media/user-help-authenticator-app-import-passwords/android-settings-menu.png)
   iOS | ![Google Chrome 설정 메뉴 아이콘](./media/user-help-authenticator-app-import-passwords/apple-settings-menu.png)

1. **설정** 에서 **암호** 를 엽니다.

   플랫폼 | 링크
   ---------- | --------
   Android | ![Andoid Chrome 암호 명령 위치](./media/user-help-authenticator-app-import-passwords/android-passwords-location.png)
   iOS | ![Apple Chrome 암호 명령 위치](./media/user-help-authenticator-app-import-passwords/apple-passwords-location.png)

1. Android 장치에서 ![ ](./media/user-help-authenticator-app-import-passwords/ellipsis-chrome.png) android 휴대폰의 오른쪽 위 또는 iOS 장치에 대 한 오른쪽 아래에 있는 Google Chrome 줄임표 메뉴를 탭 한 다음 **암호 내보내기** 를 누릅니다.

   플랫폼 | 링크
   ---------- | --------
   Android | ![Android Chrome 암호 내보내기 위치](./media/user-help-authenticator-app-import-passwords/android-export-passwords-location.png)
   iOS | ![Apple Chrome 내보내기 암호 위치](./media/user-help-authenticator-app-import-passwords/apple-export-passwords-location.png)

   PIN, 지문 또는 얼굴 인식을 제공 해야 합니다. Id를 확인 하 고 **암호 내보내기를** 다시 탭 하 여 내보내기를 시작 합니다.

1. 암호를 내보낸 후 Chrome은 가져올 앱을 선택 하 라는 메시지를 표시 합니다. 암호 가져오기를 시작 하려면 **Authenticator** 를 선택 합니다. 완료 되 면 가져오기 상태에 대 한 정보가 표시 됩니다.

   플랫폼 | 링크
   ---------- | --------
   Android | ![Android Chrome 암호 가져오기 위치](./media/user-help-authenticator-app-import-passwords/android-chrome-import.png)
   iOS | ![Apple Chrome 암호 가져오기 위치](./media/user-help-authenticator-app-import-passwords/apple-chrome-import.png)

### <a name="import-from-chrome-desktop-browser"></a>Chrome 데스크톱 브라우저에서 가져오기

시작 하기 전에 Chrome 브라우저에서 [Microsoft 자동 채우기 확장](https://chrome.google.com/webstore/detail/microsoft-autofill/fiedbfgcleddlbcmgdigjgdfcggjcion) 을 설치 하 고 로그인 해야 합니다.

1. 모든 브라우저에서 [Google 암호 관리자](https://passwords.google.com) 를 엽니다. 아직 로그인 하지 않은 경우 Google 계정에 로그인 합니다.

1. 기어 아이콘을 선택 합니다. ![데스크톱 암호 관리자 기어 아이콘](./media/user-help-authenticator-app-import-passwords/desktop-password-manager-gear.png) 를 열려면 암호 설정 페이지를 엽니다.

1. **내보내기** 를 선택 하 고 다음 페이지에서 **내보내기** 를 다시 선택 하 여 암호 내보내기를 시작 합니다. Id를 확인 하 라는 메시지가 표시 되 면 Google 암호를 제공 합니다. 완료 되 면 가져오기 상태에 대 한 정보가 표시 됩니다.

   ![데스크톱 Chrome 브라우저 암호 내보내기 명령 위치](./media/user-help-authenticator-app-import-passwords/desktop-chrome-export-passwords-location.png)

1. 자동 채우기 크롬 확장을 열고 **설정** 을 선택 합니다.

   ![바탕 화면 Chrome 브라우저 자동 채우기 확장 프로그램 설정 위치](./media/user-help-authenticator-app-import-passwords/desktop-chrome-autofill-settings.png)

1. **데이터 가져오기** 를 선택 하 여 대화 상자를 엽니다. 그런 다음 **파일 선택** 을 선택 하 여 CSV 파일을 찾아서 가져옵니다.

   ![데스크톱 Chrome 브라우저 데이터 CSV 위치 가져오기](./media/user-help-authenticator-app-import-passwords/desktop-chrome-import-csv.png)

## <a name="import-from-firefox"></a>Firefox에서 가져오기

Firefox를 사용 하면 데스크톱 브라우저 에서만 암호를 내보낼 수 있으므로 firefox에서 암호를 가져오기 전에 Firefox 데스크톱 브라우저에 액세스할 수 있는지 확인 합니다.

1. 바탕 화면에서 최신 버전의 Firefox에 로그인 하 고 다음을 선택 합니다. ![Firefox "햄버거" 메뉴](./media/user-help-authenticator-app-import-passwords/desktop-firefox-ellipsis-icon.png) 화면 오른쪽 위의 메뉴입니다.

1. **로그인 및 암호를** 선택 합니다.

   ![데스크톱 Firefox 브라우저 로그인 및 암호 위치](./media/user-help-authenticator-app-import-passwords/desktop-firefox-passwords-location.png)

1. Firefox Lockwise 페이지에서 ![ firefox 줄임표 메뉴 ](./media/user-help-authenticator-app-import-passwords/desktop-firefox-ellipsis-icon.png) 메뉴를 선택 하 고 **로그인 내보내기** 를 선택한 다음 **내보내기** 를 선택 하 여 의도를 확인 합니다. PIN, 장치 암호를 입력 하거나 지문을 검색 하 여 자신을 식별 하 라는 메시지가 표시 됩니다. 성공적으로 식별 되 면 Firefox는 사용자의 암호를 CSV 형식으로 선택한 위치로 내보냅니다.

   ![데스크톱 Firefox 브라우저 암호 위치 내보내기](./media/user-help-authenticator-app-import-passwords/desktop-firefox-export-passwords-location.png)

1. 데스크톱 브라우저 또는 iOS 또는 Android 휴대폰에서 인증자에 게 암호를 가져올 수 있습니다. 휴대폰에서 Authenticator 앱으로 가져오려면 다음을 수행 합니다.

      1. 선호 하는 안전한 방법으로 Android 또는 iOS 휴대폰에서 내보낸 CSV 파일을 전송 하 고 다운로드 합니다. 그런 다음 CSV 파일을 Authenticator 앱과 공유 하 여 가져오기를 시작 합니다.

         플랫폼 | 링크
         ---------- | --------
         Android | ![Android Chrome 암호 가져오기 위치](./media/user-help-authenticator-app-import-passwords/android-chrome-import.png)
         iOS | ![Apple Chrome 암호 가져오기 위치](./media/user-help-authenticator-app-import-passwords/apple-chrome-import.png)

      1. 인증자에 대 한 암호를 성공적으로 가져온 후에는 데스크톱 또는 휴대폰에서 CSV 파일을 삭제 합니다.

## <a name="import-from-lastpass"></a>LastPass에서 가져오기

LastPass는 데스크톱 브라우저 에서만 암호 내보내기를 지원 하므로 암호 가져오기를 시작 하기 전에 데스크톱 브라우저에 액세스할 수 있어야 합니다.

1. [LastPass 웹 사이트](https://lastpass.com) 에 로그인 하 고 **고급 옵션** 을 선택한 다음 **내보내기** 를 선택 합니다.

   ![데스크톱 LastPass 내보내기 암호 위치](./media/user-help-authenticator-app-import-passwords/desktop-lastpass-export-passwords-location.png)

1. 마스터 암호를 제공 하 여 메시지가 표시 되 면 사용자를 식별 합니다. 그 후에는 웹 페이지에서 내보낸 암호를 볼 수 있습니다.

1. 웹 페이지의 내용을 복사 합니다.

1. 메모장 (또는 원하는 텍스트 편집기)을 열고 복사한 내용을 붙여넣습니다.

1. **파일** 다른 이름으로 저장을 선택 하 여이 메모장 파일 &gt; **을** 저장 합니다. 바탕 화면에서 안전한 위치에 ".csv" (예: LastPass.csv)로 끝나는 이름을 제공 합니다.

   ![데스크톱 LastPass CSV 파일 저장](./media/user-help-authenticator-app-import-passwords/desktop-lastpass-save-import-file.png)

1. 데스크톱 브라우저 또는 iOS 또는 Android 휴대폰에서 인증자에 게 암호를 가져올 수 있습니다. 휴대폰에서 Authenticator 앱으로 가져오려면 다음을 수행 합니다.

      1. 선호 하는 안전한 방법으로 스마트폰에서 내보낸 CSV 파일을 전송 하 고 다운로드 합니다. 그런 다음 CSV 파일을 Authenticator 앱과 공유 하 여 가져오기를 시작 합니다.

         플랫폼 | 링크
         ---------- | --------
         Android | ![Android LastPass 가져오기 암호 위치](./media/user-help-authenticator-app-import-passwords/android-chrome-import.png)
         iOS | ![Apple LastPass 가져오기 암호 위치](./media/user-help-authenticator-app-import-passwords/apple-chrome-import.png)

      1. 인증자에 대 한 암호를 성공적으로 가져온 후에는 데스크톱 또는 휴대폰에서 CSV 파일을 삭제 합니다.

## <a name="import-from-bitwarden"></a>Bitwarden에서 가져오기

Bitwarden는 데스크톱 브라우저 에서만 암호 내보내기를 지원 하므로 암호 가져오기를 시작 하기 전에 데스크톱 브라우저에 액세스할 수 있어야 합니다.

1. 에 로그인 https://vault.bitwarden.com/ 하 고 **도구** &gt; **모음 내보내기** 를 선택 합니다. CSV로 파일 형식을 선택 하 고 마스터 암호를 제공한 다음 **내보내기 자격 증명 모음 내보내기** 를 선택 하 여 내보내기를 시작 합니다.

   ![Bitwarden Export 자격 증명 모음 위치](./media/user-help-authenticator-app-import-passwords/desktop-bitwarden-export-command-location.png)

1. 데스크톱 브라우저 또는 iOS 또는 Android 휴대폰에서 인증자에 게 암호를 가져올 수 있습니다. 휴대폰에서 Authenticator 앱으로 가져오려면 다음을 수행 합니다.

      1. 선호 하는 안전한 방법으로 스마트폰에서 내보낸 CSV 파일을 전송 하 고 다운로드 합니다. 그런 다음 CSV 파일을 Authenticator 앱과 공유 하 여 가져오기를 시작 합니다.

         플랫폼 | 링크
         ---------- | --------
         Android | ![Android Bitwarden 암호 가져오기 위치](./media/user-help-authenticator-app-import-passwords/android-chrome-import.png)
         iOS | ![Apple Bitwarden 가져오기 암호 위치](./media/user-help-authenticator-app-import-passwords/apple-chrome-import.png)

      1. 인증자에 대 한 암호를 성공적으로 가져온 후에는 데스크톱 또는 휴대폰에서 CSV 파일을 삭제 합니다.

## <a name="import-from-roboform"></a>Roboform에서 가져오기

Roboform는 데스크톱 앱 에서만 암호를 내보낼 수 있도록 하므로 가져오기를 시작 하기 전에 데스크톱의 Roboform 앱에 액세스할 수 있는지 확인 합니다.

1. 데스크톱 클라이언트에서 RoboForm를 시작 하 고 계정에 로그인 합니다.

1. **Roboform** 메뉴에서 **옵션** 을 선택 합니다.

   ![Desktop Roboform 옵션 메뉴](./media/user-help-authenticator-app-import-passwords/desktop-roboform-options.png)

1. **계정 & 데이터** &gt; **내보내기** 를 선택 합니다.

   ![데스크톱 Roboform 내보내기 명령 위치](./media/user-help-authenticator-app-import-passwords/desktop-roboform-accounts-data.png)

1. 내보낸 파일을 저장할 안전한 위치를 선택 합니다. **데이터** 형식으로 **로그인** 을 선택 하 고 CSV 파일을 형식으로 선택한 다음 **내보내기** 를 선택 합니다.

   ![Desktop Roboform 내보내기 대화 상자](./media/user-help-authenticator-app-import-passwords/desktop-roboform-export-dialog.png)

1. 사용자의 의도를 확인 하 고 CSV 파일을 선택한 위치로 내보냅니다.

   ![바탕 화면 Roboform 내보내기 확인 대화 상자](./media/user-help-authenticator-app-import-passwords/desktop-roboform-confirmation.png)

1. 데스크톱 브라우저 또는 iOS 또는 Android 휴대폰에서 인증자에 게 암호를 가져올 수 있습니다. 휴대폰에서 Authenticator 앱으로 가져오려면 다음을 수행 합니다.

      1. 선호 하는 안전한 방법으로 스마트폰에서 내보낸 CSV 파일을 전송 하 고 다운로드 합니다. 그런 다음 CSV 파일을 Authenticator 앱과 공유 하 여 가져오기를 시작 합니다.

         플랫폼 | 링크
         ---------- | --------
         Android | ![Android Roboform 암호 가져오기 위치](./media/user-help-authenticator-app-import-passwords/android-chrome-import.png)
         iOS | ![Apple Roboform 가져오기 암호 위치](./media/user-help-authenticator-app-import-passwords/apple-chrome-import.png)

      1. 인증자에 대 한 암호를 성공적으로 가져온 후에는 데스크톱 또는 휴대폰에서 CSV 파일을 삭제 합니다.

## <a name="import-by-creating-a-csv"></a>CSV를 만들어 가져오기

암호 관리자에서 암호를 가져오는 단계가이 문서에 나열 되지 않은 경우 암호를 인증자로 가져오는 데 사용할 수 있는 CSV를 만들 수 있습니다. 쉽게 서식 지정을 위해 데스크톱에서 이러한 단계를 수행 하는 것이 좋습니다.

1. 바탕 화면에서 [가져오기 템플릿을 다운로드 하 고 엽니다](https://go.microsoft.com/fwlink/?linkid=2134938). Apple iPhone, Safari 및 키 집합 사용자 인 경우 이제 4 단계로 건너뛸 수 있습니다.

1. 암호화 되지 않은 CSV 파일의 기존 암호 관리자에서 암호를 내보냅니다.

1. 내보낸 CSV의 관련 열을 템플릿 CSV로 복사한 후 저장 합니다.

1. 내보낸 CSV가 없는 경우 기존 암호 관리자의 각 로그인을 템플릿 CSV로 복사할 수 있습니다. 머리글 행을 제거 하거나 변경 하지 마십시오. 완료 되 면 다음 단계를 시작 하기 전에 데이터의 무결성을 확인 합니다.

1. 데스크톱 브라우저 또는 iOS 또는 Android 휴대폰에서 인증자에 게 암호를 가져올 수 있습니다. 휴대폰에서 Authenticator 앱으로 가져오려면 다음을 수행 합니다.

      1. 선호 하는 안전한 방법으로 스마트폰에서 내보낸 CSV 파일을 전송 하 고 다운로드 합니다. 그런 다음 CSV 파일을 Authenticator 앱과 공유 하 여 가져오기를 시작 합니다.

         플랫폼 | 링크
         ---------- | --------
         Android | ![Android CSV 암호 가져오기 위치](./media/user-help-authenticator-app-import-passwords/android-chrome-import.png)
         iOS | ![Apple CSV 가져오기 암호 위치](./media/user-help-authenticator-app-import-passwords/apple-chrome-import.png)

      1. 인증자에 대 한 암호를 성공적으로 가져온 후에는 데스크톱 또는 휴대폰에서 CSV 파일을 삭제 합니다.

## <a name="troubleshooting-steps"></a>문제 해결 단계

실패 한 가져오기의 가장 일반적인 원인은 CSV 파일에서 잘못 된 형식입니다. 다음 단계를 수행 하 여 문제를 해결할 수 있습니다.

- 현재 암호 관리자에서 암호 가져오기를 이미 지원 하는지 확인 하려면이 문서를 확인 하세요. 이렇게 하는 경우 해당 공급자에 대해 언급 된 단계를 수행 하 여 가져오기를 다시 시도할 수 있습니다.

- 현재 암호 관리자의 형식 가져오기를 지원 하지 않는 경우 [CSV 파일을 수동으로 만들어](#import-by-creating-a-csv)다시 시도할 수 있습니다.

- 다음 제안 사항을 사용 하 여 CSV 데이터의 무결성을 확인할 수 있습니다.

  - 첫 번째 행은 **url**, **사용자 이름** 및 **암호** 라는 세 개의 열이 포함 된 헤더를 포함 해야 합니다.

  - 각 행은 **url** 및 **암호** 열 아래에 값을 포함 해야 합니다.

- Csv [템플릿 파일](https://go.microsoft.com/fwlink/?linkid=2134938)에 콘텐츠를 붙여넣어 csv를 다시 만들 수 있습니다.

- 다른 작업을 수행 하지 않는 경우 인증자 앱 설정에서 **사용자 의견 보내기** 링크를 사용 하 여 문제를 보고 하세요.
