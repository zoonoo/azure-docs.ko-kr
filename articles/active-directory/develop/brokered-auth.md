---
title: 안드로이드에서 중개 인증 | Azure
titlesuffix: Microsoft identity platform
description: Microsoft ID 플랫폼에서 Android에 대한 중개 인증 & 권한 의 개요
services: active-directory
author: shoatman
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/14/2019
ms.author: shoatman
ms.custom: aaddev
ms.reviewer: shoatman, hahamil, brianmel
ms.openlocfilehash: a734589178438fd65d9a2d156fd91fc82807f578
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76697900"
---
# <a name="brokered-authentication-in-android"></a>안드로이드에서 중개 인증

Microsoft의 인증 브로커 중 하나를 사용하여 장치 전체 의 SSO(단일 사인온)에 참여하고 조직 조건부 액세스 정책을 충족해야 합니다. 브로커와 통합하면 다음과 같은 이점이 있습니다.

- 장치 단일 사인온
- 조건부 액세스:
  - Intune 앱 보호
  - 장치 등록(직장 가입)
  - 모바일 디바이스 관리
- 장치 전체 계정 관리
  -  계정 설정을 & 안드로이드 계정 관리자를 통해
  - "직장 계정" - 사용자 지정 계정 유형

안드로이드에서, 마이크로 소프트 인증 브로커는 마이크로 [소프트 인증자 응용 프로그램](https://play.google.com/store/apps/details?id=com.azure.authenticator) 및 [Intune 회사 포털에](https://play.google.com/store/apps/details?id=com.microsoft.windowsintune.companyportal) 포함 된 구성 요소입니다

> [!TIP]
> 브로커를 호스팅하는 응용 프로그램은 한 번에 브로커로 활성화됩니다. 브로커로서 활성화된 응용 프로그램은 장치의 설치 순서에 따라 결정됩니다. 설치할 첫 번째 또는 장치의 마지막 존재가 활성 브로커가 됩니다.

다음 다이어그램은 앱, MSAL(Microsoft 인증 라이브러리) 및 Microsoft 인증 브로커 간의 관계를 보여 줍니다.

![브로커 배포 다이어그램](./media/brokered-auth/brokered-deployment-diagram.png)

## <a name="installing-apps-that-host-a-broker"></a>브로커를 호스팅하는 앱 설치

브로커 호스팅 앱은 언제든지 앱 스토어(일반적으로 Google Play 스토어)의 기기 소유자가 설치할 수 있습니다. 그러나 일부 API(리소스)는 장치가 다음과 같은 조건부 액세스 정책에 의해 보호됩니다.

- 등록(직장 가입) 및/또는
- 장치 관리에 등록하거나
- 인튠 앱 보호에 등록

장치에 브로커 앱이 아직 설치되어 있지 않은 경우 MSAL은 앱이 대화식으로 토큰을 얻으려고 시도하는 즉시 설치하도록 사용자에게 지시합니다. 그런 다음 앱이 필요한 정책을 준수하도록 하는 단계를 통해 사용자를 이끌어야 합니다.

## <a name="effects-of-installing-and-uninstalling-a-broker"></a>브로커 설치 및 제거효과

### <a name="when-a-broker-is-installed"></a>브로커가 설치된 경우

브로커가 장치에 설치되면 모든 후속 대화형 토큰 `acquireToken()`요청(호출)은 MSAL이 로컬로 처리하지 않고 브로커에 의해 처리됩니다. MSAL에서 이전에 사용할 수 있는 모든 SSO 상태는 브로커가 사용할 수 없습니다. 따라서 사용자는 다시 인증하거나 장치에 알려진 기존 계정 목록에서 계정을 선택해야 합니다.

브로커를 설치해도 사용자가 다시 로그인할 필요가 없습니다. 사용자가 `MsalUiRequiredException` 유언지를 해결해야 하는 경우에만 다음 요청이 브로커로 이동합니다. `MsalUiRequiredException`여러 가지 이유로 throw되며 대화식으로 해결해야 합니다. 다음은 몇 가지 일반적인 이유입니다.

- 사용자가 자신의 계정과 연결된 암호를 변경했습니다.
- 사용자의 계정이 더 이상 조건부 액세스 정책을 충족하지 않습니다.
- 사용자는 앱이 자신의 계정과 연결될 수 있도록 동의를 취소했습니다.

### <a name="when-a-broker-is-uninstalled"></a>브로커가 제거된 경우

브로커 호스팅 앱이 하나만 설치되어 있고 제거된 경우 사용자는 다시 로그인해야 합니다. 활성 브로커를 제거하면 장치에서 계정 및 관련 토큰이 제거됩니다.

Intune 회사 포털이 설치되어 있고 활성 브로커로 작동하고 Microsoft 인증자도 설치된 경우 Intune 회사 포털(활성 브로커)이 제거된 경우 사용자는 다시 로그인해야 합니다. 다시 로그인하면 Microsoft 인증자 앱이 활성 브로커가 됩니다.

## <a name="integrating-with-a-broker"></a>브로커와의 통합

### <a name="generating-a-redirect-uri-for-a-broker"></a>브로커에 대한 리디렉션 URI 생성

브로커와 호환되는 리디렉션 URI를 등록해야 합니다. 브로커에 대한 리디렉션 URI에는 앱의 패키지 이름과 앱 서명의 base64 인코딩된 표현이 포함되어야 합니다.

리디렉션 URI의 형식은 다음과 입니다.`msauth://<yourpackagename>/<base64urlencodedsignature>`

앱의 서명 키를 사용하여 Base64 URL 인코딩 서명을 생성합니다. 디버그 서명 키를 사용하는 몇 가지 예제 명령은 다음과 같습니다.

#### <a name="macos"></a>macOS

```bash
keytool -exportcert -alias androiddebugkey -keystore ~/.android/debug.keystore | openssl sha1 -binary | openssl base64
```

#### <a name="windows"></a>Windows

```powershell
keytool -exportcert -alias androiddebugkey -keystore %HOMEPATH%\.android\debug.keystore | openssl sha1 -binary | openssl base64
```

[앱 서명에](https://developer.android.com/studio/publish/app-signing) 대한 자세한 내용은 앱 서명을 참조하십시오.

> [!IMPORTANT]
> 앱의 프로덕션 버전에 프로덕션 서명 키를 사용합니다.

### <a name="configure-msal-to-use-a-broker"></a>브로커를 사용하도록 MSAL 구성

앱에서 브로커를 사용하려면 브로커 리디렉션을 구성했다는 것을 증명해야 합니다. 예를 들어 브로커가 사용 가능한 리디렉션 URI를 모두 포함하고 MSAL 구성 파일에 다음을 포함하여 등록했음을 나타냅니다.

```javascript
"redirect_uri" : "<yourbrokerredirecturi>",
"broker_redirect_uri_registered": true
```

> [!TIP]
> 새 Azure 포털 앱 등록 UI를 사용하면 브로커 리디렉션 URI를 생성하는 데 도움이 됩니다. 이전 환경을 사용하여 앱을 등록했거나 Microsoft 앱 등록 포털을 사용하여 앱을 등록한 경우 리디렉션 URI를 생성하고 포털에서 리디렉션 URI 목록을 수동으로 업데이트해야 할 수 있습니다.

### <a name="broker-related-exceptions"></a>브로커 관련 예외

MSAL은 다음 두 가지 방법으로 브로커와 통신합니다.

- 브로커 바운드 서비스
- 안드로이드 계정 관리자

MSAL은 먼저 이 서비스를 호출해도 Android 권한이 필요하지 않으므로 브로커 바인딩 서비스를 사용합니다. 바인딩된 서비스에 바인딩되지 않으면 MSAL은 Android AccountManager API를 사용합니다. MSAL은 앱에 이미 `"READ_CONTACTS"` 사용 권한이 부여된 경우에만 이 작업을 수행합니다.

오류 코드가 `"BROKER_BIND_FAILURE"` `MsalClientException` 있는 경우 다음 두 가지 옵션이 있습니다.

- 사용자에게 Microsoft 인증자 앱 및 Intune 회사 포털에 대한 전원 최적화를 사용하지 않도록 설정하도록 요청합니다.
- 사용자에게 권한을 부여하도록 `"READ_CONTACTS"` 요청합니다.
