---
title: MSAL을 사용 하 여 Android에서 앱 간 SSO를 사용 하도록 설정 하는 방법 Microsoft
titleSuffix: Microsoft identity platform
description: Android 용 MSAL (Microsoft 인증 라이브러리)을 사용 하 여 응용 프로그램에서 Single Sign-On를 사용 하도록 설정 하는 방법입니다.
services: active-directory
author: hamiltonha
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: android
ms.devlang: java
ms.topic: how-to
ms.date: 10/15/2020
ms.author: hahamil
ms.reviewer: marsma
ms.openlocfilehash: 7e53e21b6d929e2f0ba9a2e23e4e8e1b2278f828
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2020
ms.locfileid: "92209902"
---
# <a name="how-to-enable-cross-app-sso-on-android-using-msal"></a>방법: MSAL을 사용 하 여 Android에서 앱 간 SSO 사용

SSO (Single sign-on)를 사용 하면 사용자가 자격 증명을 한 번만 입력 하면 해당 자격 증명이 응용 프로그램 간에 자동으로 작동 합니다.

[Microsoft id 플랫폼](/azure/active-directory/develop/) 및 msal (Microsoft 인증 라이브러리)을 사용 하면 사용자 고유의 앱 모음에서 SSO를 사용 하도록 설정할 수 있습니다. Broker 기능 및 인증자 응용 프로그램을 사용 하 여 전체 장치에서 SSO를 확장할 수 있습니다.

이 방법에서는 응용 프로그램에서 사용자에 게 SSO를 제공 하는 데 사용 하는 Sdk를 구성 하는 방법을 알아봅니다.

## <a name="prerequisites"></a>필수 구성 요소

이 방법에서는 다음을 수행 하는 방법을 알고 있다고 가정 합니다.

- Azure Portal를 사용 하 여 앱을 프로 비전 합니다. 이 항목에 대 한 자세한 내용은 [Android 자습서](https://docs.microsoft.com/azure/active-directory/develop/tutorial-v2-android#create-a-project) 에서 앱을 만드는 방법에 대 한 지침을 참조 하세요.
- 응용 프로그램을 [Android 용 Microsoft 인증 라이브러리](https://github.com/AzureAD/microsoft-authentication-library-for-android)와 통합 합니다.

## <a name="methods-for-single-sign-on"></a>Single Sign-On에 대 한 메서드

Android 용 MSAL을 사용 하는 응용 프로그램은 다음 두 가지 방법으로 SSO를 달성할 수 있습니다.

* [Broker 응용 프로그램](#sso-through-brokered-authentication) 을 통해
* [시스템 브라우저](#sso-through-system-browser) 를 통해


   장치 차원의 SSO, 계정 관리 및 조건부 액세스와 같은 이점을 위해 broker 응용 프로그램을 사용 하는 것이 좋습니다. 그러나 사용자는 추가 응용 프로그램을 다운로드 해야 합니다.

## <a name="sso-through-brokered-authentication"></a>조정 된 인증을 통한 SSO

Microsoft 인증 브로커 중 하나를 사용 하 여 장치 차원의 SSO (Single Sign-On)에 참여 하 고 조직의 조건부 액세스 정책을 충족 하는 것이 좋습니다. Broker와 통합 하면 다음과 같은 이점이 있습니다.

- 장치 Single Sign-On
- 다음에 대 한 조건부 액세스:
  - Intune 앱 보호
  - 장치 등록 (Workplace Join)
  - Mobile Device Management
- 장치 수준 계정 관리
  -  Android AccountManager를 통해 & 계정 설정
  - "회사 계정"-사용자 지정 계정 유형

Android에서 Microsoft Authentication Broker는 [Microsoft Authenticator](https://play.google.com/store/apps/details?id=com.azure.authenticator) 및 [Intune 회사 포털](https://play.google.com/store/apps/details?id=com.microsoft.windowsintune.companyportal) 앱에 포함 된 구성 요소입니다.

다음 다이어그램은 앱, MSAL (Microsoft 인증 라이브러리) 및 Microsoft 인증 브로커 간의 관계를 보여 줍니다.

![응용 프로그램이 MSAL, broker 앱 및 Android 계정 관리자와 어떻게 관련 되는지를 보여 주는 다이어그램입니다.](./media/brokered-auth/brokered-deployment-diagram.png)

### <a name="installing-apps-that-host-a-broker"></a>Broker를 호스트 하는 앱 설치

Broker 호스팅 앱은 언제 든 지 앱 스토어 (일반적으로 Google Play 스토어)에서 장치 소유자에 의해 설치 될 수 있습니다. 그러나 일부 Api (리소스)는 장치를 사용 해야 하는 조건부 액세스 정책에 의해 보호 됩니다.

- 등록 됨 (작업 공간에 연결 됨) 및/또는
- 장치 관리에 등록 됨 또는
- Intune 앱 보호에 등록 됨

장치에 아직 broker 앱이 설치 되어 있지 않은 경우 MSAL은 앱에서 토큰을 대화형으로 가져오려고 할 때 즉시 설치 하도록 사용자에 게 지시 합니다. 그러면 앱이 사용자에 게 필요한 정책을 준수 하도록 장치를 설정 하는 단계를 안내 합니다.

### <a name="effects-of-installing-and-uninstalling-a-broker"></a>Broker 설치 및 제거의 효과

#### <a name="when-a-broker-is-installed"></a>Broker가 설치 된 경우

Broker가 장치에 설치 되 면 모든 후속 대화형 토큰 요청 (에 대 한 호출 `acquireToken()` )은 MSAL에 의해 로컬이 아닌 broker에서 처리 됩니다. 이전에 MSAL에서 사용할 수 있는 모든 SSO 상태를 broker에서 사용할 수 없습니다. 따라서 사용자가 다시 인증 하거나 장치에 알려진 기존 계정 목록에서 계정을 선택 해야 합니다.

Broker를 설치 하는 경우에는 사용자가 다시 로그인 할 필요가 없습니다. 사용자가를 확인 해야 하는 경우에만 `MsalUiRequiredException` 다음 요청이 broker로 이동 합니다. `MsalUiRequiredException` 는 여러 가지 이유로 throw 될 수 있으므로 대화형으로 해결 해야 합니다. 예를 들면 다음과 같습니다.

- 사용자가 계정과 연결 된 암호를 변경 했습니다.
- 사용자 계정이 더 이상 조건부 액세스 정책을 충족 하지 않습니다.
- 사용자가 앱의 계정에 연결 하는 데 대 한 동의를 취소 했습니다.

**여러 broker** -장치에 여러 broker가 설치 된 경우 먼저 설치 된 broker는 항상 활성 broker입니다. 단일 broker만 장치에서 활성 상태일 수 있습니다.

#### <a name="when-a-broker-is-uninstalled"></a>Broker가 제거 될 때

Broker 호스팅 앱이 하나만 설치 되어 있고 제거 된 경우에는 사용자가 다시 로그인 해야 합니다. 활성 broker를 제거 하면 장치에서 계정 및 연결 된 토큰이 제거 됩니다.

Intune 회사 포털 설치 되어 있고 활성 broker로 작동 하 고 Microsoft Authenticator도 설치 된 경우에는 Intune 회사 포털 (활성 broker)가 제거 되 면 사용자가 다시 로그인 해야 합니다. 다시 로그인 하면 Microsoft Authenticator 앱이 활성 broker가 됩니다.

### <a name="integrating-with-a-broker"></a>Broker와 통합

#### <a name="generate-a-redirect-uri-for-a-broker"></a>Broker에 대 한 리디렉션 URI 생성

Broker와 호환 되는 리디렉션 URI를 등록 해야 합니다. Broker의 리디렉션 URI에는 앱의 패키지 이름 및 앱 서명의 Base64 인코딩 표현이 포함 되어야 합니다.

리디렉션 URI의 형식은 다음과 같습니다. `msauth://<yourpackagename>/<base64urlencodedsignature>`

[Keytool](https://manpages.debian.org/buster/openjdk-11-jre-headless/keytool.1.en.html) 을 사용 하 여 앱의 서명 키를 사용 하 여 b a s e 64로 인코딩된 서명 해시를 생성 한 다음 Azure Portal를 사용 하 여 해당 해시를 사용 하 여 리디렉션 URI를 생성할 수 있습니다.

Linux 및 macOS:

```bash
keytool -exportcert -alias androiddebugkey -keystore ~/.android/debug.keystore | openssl sha1 -binary | openssl base64
```

Windows:

```powershell
keytool -exportcert -alias androiddebugkey -keystore %HOMEPATH%\.android\debug.keystore | openssl sha1 -binary | openssl base64
```

*Keytool*을 사용 하 여 서명 해시를 생성 한 후에는 Azure Portal를 사용 하 여 리디렉션 URI를 생성 합니다.

1. [Azure Portal](https://portal.azure.com) 에 로그인 하 고 **앱 등록**에서 Android 앱을 선택 합니다.
1. **인증**  >  **플랫폼 Android 추가를**선택  >  **Android**합니다.
1. 열리는 **Android 앱 구성** 창에서 앞서 생성 한 **서명 해시** 와 **패키지 이름을**입력 합니다.
1. **구성** 단추를 선택 합니다.

Azure Portal는 리디렉션 URI를 생성 하 고 **Android 구성** 창의 **리디렉션 uri** 필드에 표시 합니다.

앱에 서명 하는 방법에 대 한 자세한 내용은 Android Studio 사용자 가이드에서 [앱에 서명](https://developer.android.com/studio/publish/app-signing) 을 참조 하세요.

> [!IMPORTANT]
> 프로덕션 버전의 앱에 대 한 프로덕션 서명 키를 사용 합니다.

#### <a name="configure-msal-to-use-a-broker"></a>Broker를 사용 하도록 MSAL 구성

앱에서 broker를 사용 하려면 broker 리디렉션을 구성 했는지를 증명 해야 합니다. 예를 들어, MSAL 구성 파일에 다음 설정을 포함 하 여 broker 사용 리디렉션 URI를 모두 포함 하 고 등록 했음을 표시 합니다.

```json
"redirect_uri" : "<yourbrokerredirecturi>",
"broker_redirect_uri_registered": true
```

#### <a name="broker-related-exceptions"></a>Broker 관련 예외

MSAL은 다음 두 가지 방법으로 broker와 통신 합니다.

- Broker 바운드 서비스
- Android AccountManager

이 서비스를 호출 하려면 Android 권한이 필요 하지 않으므로 MSAL에서 먼저 broker 바인딩된 서비스를 사용 합니다. 바인딩된 서비스에 대 한 바인딩이 실패 하면 MSAL은 Android AccountManager API를 사용 합니다. MSAL은 앱에 이미 권한이 부여 된 경우에만 수행 `"READ_CONTACTS"` 합니다.

오류가 발생 하는 경우 `MsalClientException` `"BROKER_BIND_FAILURE"` 다음 두 가지 옵션을 사용할 수 있습니다.

- 사용자에 게 Microsoft Authenticator 앱 및 Intune 회사 포털에 대 한 전원 최적화를 사용 하지 않도록 설정 합니다.
- 사용자에 게 권한을 부여 하도록 요청 `"READ_CONTACTS"`

### <a name="verify-broker-integration"></a>Broker 통합 확인

Broker 통합이 작동 한다는 것을 즉시 알 수는 없지만 다음 단계를 사용 하 여 확인할 수 있습니다.

1. Android 장치에서 broker를 사용 하 여 요청을 완료 합니다.
1. Android 장치의 설정에서 인증 한 계정에 해당 하는 새로 만든 계정을 찾습니다. 계정은 *회사 계정*형식 이어야 합니다.

테스트를 반복 하려는 경우 설정에서 계정을 제거할 수 있습니다.

## <a name="sso-through-system-browser"></a>시스템 브라우저를 통한 SSO

Android 응용 프로그램에는 인증 사용자 환경에 대 한 웹 보기, 시스템 브라우저 또는 크롬 사용자 지정 탭을 사용할 수 있는 옵션이 있습니다. 응용 프로그램에서 조정 된 인증을 사용 하지 않는 경우 SSO를 얻기 위해 기본 웹 보기 대신 시스템 브라우저를 사용 해야 합니다.

### <a name="authorization-agents"></a>권한 부여 에이전트

권한 부여 에이전트에 대 한 특정 전략 선택은 선택 사항이 며 앱에서 사용자 지정할 수 있는 추가 기능을 나타냅니다. 대부분의 앱은 MSAL 기본값을 사용 합니다. 다양 한 기본값을 보려면 [ANDROID msal 구성 파일 이해](msal-configuration.md) 를 참조 하세요.

MSAL은 또는 시스템 브라우저를 사용 하 여 권한 부여 `WebView` 를 지원 합니다.  아래 이미지는, 또는 customtabs이 `WebView` 없는 시스템 브라우저 또는 CustomTabs를 사용 하는 방법을 보여 줍니다.

![MSAL 로그인 예](./media/authorization-agents/sign-in-ui.jpg)

### <a name="single-sign-on-implications"></a>Single sign-on의 영향

기본적으로 MSAL과 통합 된 응용 프로그램은 시스템 브라우저의 사용자 지정 탭을 사용 하 여 권한을 부여 합니다. 웹 보기와 달리 사용자 지정 탭은 쿠키 jar을 기본 시스템 브라우저와 공유 하 여 웹 또는 사용자 지정 탭과 통합 된 다른 네이티브 앱을 사용 하 여 로그인을 줄여 줍니다.

응용 프로그램이 `WebView` Microsoft Authenticator 또는 회사 포털 지원 기능을 앱에 통합 하지 않고 전략을 사용 하는 경우 사용자는 장치 전체 또는 네이티브 앱과 웹 앱 간에 Single Sign-On 환경을 제공 하지 않습니다.

응용 프로그램에서 Microsoft Authenticator 또는 Intune 회사 포털와 같이 broker와 함께 MSAL을 사용 하는 경우 응용 프로그램 중 하나에 활성 로그인이 있는 경우 사용자가 응용 프로그램 간에 SSO 환경을 사용할 수 있습니다.

### <a name="webview"></a>WebView

앱 내 웹 보기를 사용 하려면 MSAL에 전달 되는 앱 구성 JSON에 다음 줄을 입력 합니다.

```json
"authorization_user_agent" : "WEBVIEW"
```

앱 내를 사용 하는 경우 `WebView` 사용자는 앱에 직접 로그인 합니다. 토큰은 앱의 샌드박스 내에 유지 되 고 앱의 쿠키 jar 외부에서 사용할 수 없습니다. 따라서 앱이 인증자 또는 회사 포털와 통합 되지 않은 경우 사용자는 응용 프로그램 간에 SSO 환경을 사용할 수 없습니다.

그러나에서는 `WebView` 로그인 UI의 모양과 느낌을 사용자 지정할 수 있는 기능을 제공 합니다. 이 사용자 지정을 수행 하는 방법에 대 한 자세한 내용은 [Android 웹 보기](https://developer.android.com/reference/android/webkit/WebView) 를 참조 하세요.

### <a name="default-browser-plus-custom-tabs"></a>기본 브라우저 및 사용자 지정 탭

기본적으로 MSAL은 브라우저와 [사용자 지정 탭](https://developer.chrome.com/multidevice/android/customtabs) 전략을 사용 합니다. `DEFAULT`사용자 지정 구성 파일에서 다음 JSON 구성을 사용 하 여 이후 릴리스에서의 변경을 방지 하기 위해이 전략을 명시적으로 지정할 수 있습니다.

```json
"authorization_user_agent" : "BROWSER"
```

이 접근 방식을 사용 하 여 장치의 브라우저를 통해 SSO 환경을 제공 합니다. MSAL은 공유 쿠키 jar을 사용 합니다 .이를 통해 다른 네이티브 앱 또는 웹 앱은 MSAL으로 설정 된 영구 세션 쿠키를 사용 하 여 장치에서 SSO를 달성할 수 있습니다.

### <a name="browser-selection-heuristic"></a>브라우저 선택 추론

MSAL은 다양 한 Android 휴대폰 배열에서 사용할 정확한 브라우저 패키지를 지정 하는 것이 불가능 하기 때문에 MSAL은 가장 적합 한 장치 간 SSO를 제공 하려고 하는 브라우저 선택 추론을 구현 합니다.

MSAL은 주로 패키지 관리자에서 기본 브라우저를 검색 하 고 테스트 된 안전한 브라우저 목록에 있는지 확인 합니다. 그렇지 않은 경우에는 safe 목록에서 기본이 아닌 다른 브라우저를 시작 하는 대신 웹 보기를 사용 하 여 대체 합니다. 사용자 지정 탭을 지원 하는지 여부에 관계 없이 기본 브라우저가 선택 됩니다. 브라우저에서 사용자 지정 탭을 지 원하는 경우 MSAL이 사용자 지정 탭을 시작 합니다. 사용자 지정 탭은 앱 내에서 모양과 느낌을 가지 `WebView` 며 기본 UI 사용자 지정을 허용 합니다. 자세히 알아보려면 [Android의 사용자 지정 탭](https://developer.chrome.com/multidevice/android/customtabs) 을 참조 하세요.

장치에 브라우저 패키지가 없으면 MSAL은 앱 내를 사용 `WebView` 합니다. 장치 기본 설정이 변경 되지 않은 경우 SSO 환경을 보장 하기 위해 각 로그인에 대해 동일한 브라우저를 시작 해야 합니다.

#### <a name="tested-browsers"></a>테스트 된 브라우저

다음 브라우저가 `"redirect_uri"` 구성 파일의 지정 된에 올바르게 리디렉션되도록 테스트 되었습니다.

| 디바이스 | 기본 제공 브라우저 | Chrome | Opera  | Microsoft Edge | UC 브라우저 | Firefox |
| -- |:-------------:| -----:|-----:|-----:|-----:|-----:|
| Nexus 4 (API 17) | pass | pass |적용할 수 없음 |적용할 수 없음 |적용할 수 없음 |적용할 수 없음 |
| Samsung S7 (API 25) | 패스<sup>1</sup> | pass | pass | pass | fail |pass |
| Huawei (API 26) |패스<sup>2</sup> | pass | fail | pass | pass |pass |
| Vivo (API 26) |pass|pass|pass|pass|pass|fail|
| 픽셀 2 (API 26) |pass | pass | pass | pass | fail |pass |
| Oppo | pass | 해당 사항 없음<sup>3</sup>|적용할 수 없음  |적용할 수 없음 |적용할 수 없음 | 적용할 수 없음|
| OnePlus (API 25) |pass | pass | pass | pass | fail |pass |
| Nexus (API 28) |pass | pass | pass | pass | fail |pass |
|MI | pass | pass | pass | pass | fail |pass |

<sup>1</sup> Samsung의 기본 제공 브라우저는 Samsung Internet입니다.<br/>
<sup>2</sup> Huawei의 기본 제공 브라우저는 Huawei Browser입니다.<br/>
<sup>3</sup> 기본 브라우저는 Oppo 장치 설정 내에서 변경할 수 없습니다.

## <a name="next-steps"></a>다음 단계

[Android 장치에 대 한 공유 장치 모드](msal-android-shared-devices.md) 를 사용 하면 여러 직원이 쉽게 공유할 수 있도록 android 장치를 구성할 수 있습니다.
