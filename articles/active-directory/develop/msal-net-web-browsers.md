---
title: .NET 용 Microsoft Authentication Library의 브라우저 웹 | Azure
description: .NET (MSAL.NET)에 대 한 Microsoft 인증 라이브러리를 사용 하 여 Xamarin Android를 사용할 때 특정 고려 사항에 알아봅니다.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: celested
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/06/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: d4b4c4cd4dbab10a9d4796a8393cc7f479b90cc4
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2019
ms.locfileid: "65406779"
---
# <a name="using-web-browsers-in-msalnet"></a>웹 브라우저를 사용 하 여 MSAL.NET에서
웹 브라우저는 대화형 인증을 위해 필요 합니다. MSAL.NET 기본적으로 지원 합니다 [시스템 웹 브라우저](#system-web-browser-on-xamarinios-and-xamarinandroid) Xamarin.iOS에서 및 [Xamarin.Android](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/system-browser)합니다. 하지만 [포함 된 웹 브라우저를 사용할 수도 있습니다](#enable-embedded-webviews) (UX에서 single sign-on (SSO)를 보안에 대 한 필요)의 요구 사항에 따라 [Xamarin.iOS](#choosing-between-embedded-web-browser-or-system-browser-on-xamarinios) 하 고 [Xamarin.Android](#choosing-between-embedded-web-browser-or-system-browser-on-xamarinandroid) 앱입니다. 및 수도 있습니다 [동적으로 선택](#detecting-the-presence-of-custom-tabs-on-xamarinandroid) Chrome 또는 Android에서 Chrome 사용자 지정 탭을 지 원하는 브라우저의 존재를 기반으로 웹 브라우저를 사용 합니다.

## <a name="web-browsers-in-msalnet"></a>MSAL.NET에서 웹 브라우저

대화형으로 토큰을 획득 하는 경우 대화 상자의 콘텐츠를 제공 하지 않으면 라이브러리에서 하지만 STS (보안 토큰 서비스)에서 이해 하는 것이 반드시 합니다. HTML 및 JavaScript 웹 브라우저 또는 웹 컨트롤에서 렌더링 되는 상호 작용을 제어 하는 몇 가지 인증 끝점에서 다시 보냅니다. HTML 상호 작용을 처리 하도록 STS를 허용 하는 것에 많은 이점이 있습니다.

- 암호 (입력 하나) 하는 경우 응용 프로그램 및 인증 라이브러리에서 저장 되지 됩니다.
- 다른 id 공급자 (예를 들어 로그인에는 회사 또는 학교 계정 또는 Azure AD B2C를 사용 하 여 소셜 계정 또는 MSAL을 사용 하 여 개인 계정)으로 리디렉션을 사용 하도록 설정 합니다.
- STS를 (Windows Hello pin을 입력 또는 자신의 전화 또는 휴대폰에 인증 앱에서 호출) 인증 단계 중 사용자 수행 다중 요소 인증 (MFA) 하 여 예를 들어, 조건부 액세스를 제어할 수 있습니다. 여기서는 필요한 다단계 인증 설정 되지 않은 것 아직 경우 사용자 수 설정 동일한 대화 상자에서 just-in-time에서 합니다.  사용자는 휴대폰 번호를 입력 하 고 인증 응용 프로그램을 설치 하 고 해당 계정을 추가 하려면 QR 태그를 검색 하는 따릅니다. 이 서버 상호 작용 기반 환경을 개선 되었습니다.
- 암호 (이전 암호와 새 암호에 대 한 추가 필드를 제공)이 만료 된 경우이 동일한 대화 상자에서 해당 암호를 변경할 수가 있습니다.
- Azure AD 테 넌 트 관리자에 의해 제어 되는 테 넌 트 또는 응용 프로그램 (이미지)의 브랜딩 수 있도록 / 응용 프로그램 소유자입니다.
- 응용 프로그램 리소스에 액세스할 수 있도록 동의 하면/인증 직후 이름에 범위를 지정 합니다.

## <a name="system-web-browser-on-xamarinios-and-xamarinandroid"></a>Xamarin.iOS 및 Xamarin.Android에서 시스템 웹 브라우저

기본적으로 MSAL.NET는 Xamarin.iOS 및 Xamarin.Android에서 시스템 웹 브라우저를 지원합니다. UI (즉,.NET Core 아님)를 제공 하는 모든 플랫폼에 대해 대화 상자는 웹 브라우저 컨트롤을 포함 하는 라이브러리에서 제공 됩니다. 또한 MSAL.NET의 UWP 플랫폼에 대 한 포함 된 웹 보기를 WAB.NET 데스크톱을 사용합니다. 그러나 기본적으로 활용 하는 **시스템 웹 브라우저** Xamarin iOS 및 Xamarin Android 응용 프로그램에 대 한 합니다. IOS에서도 운영 체제의 버전에 따라 사용 하도록 웹 뷰를 선택 (iOS12, iOS11, 및 이전 버전).

Broker를 사용 하지 않고도 다른 응용 프로그램 및 웹 응용 프로그램을 사용 하 여 SSO 상태를 공유의 중요 한 이점이 시스템 브라우저를 사용 하 여 (회사 포털 / Authenticator). 시스템 브라우저 사용한, Xamarin iOS 및 Xamarin Android 플랫폼에 대 한 MSAL.NET에서 기본적으로 하기 때문에 이러한 플랫폼에서는 시스템 웹 브라우저를 전체 화면을 차지 하며 사용자 환경을 더 합니다. 시스템 웹 보기 대화 상자를 구분 하지 않습니다. Ios, 그러나 사용자 할 번거로울 수 있는 응용 프로그램을 다시 호출 브라우저에 대 한 동의 제공 합니다.

### <a name="uwp-does-not-use-the-system-webview"></a>UWP은 System Webview를 사용 하지 않습니다.

데스크톱 응용 프로그램에 대 한 System Webview를 시작 합니다. 그런데이 방법을 거쳤으며 평균 이하의 사용자 경험을 사용자에 표시 되는 브라우저를 연 다른 탭이 이미 있습니다. 및 사용자 인증에 문제가 발생 한 경우이 창을 닫을지 묻는 페이지를 가져옵니다. 사용자 주의 하지 않습니다, 경우 (인증에 관련 되지 않는 다른 탭 포함)는 전체 프로세스를 닫을 수 있습니다. 바탕 화면에서 시스템 브라우저를 활용 하 여도 해야를 수신 하 고 로컬 포트 열기 응용 프로그램에 대 한 고급 권한이 필요할 수 있습니다. 이 요구 사항에 대 한 것을 꺼릴 수 개발자, 사용자 또는 관리자가 있습니다.

## <a name="enable-embedded-webviews"></a>포함 된 웹 보기를 사용 하도록 설정 
Xamarin.iOS 및 Xamarin.Android 앱에 포함 된 웹 보기를 사용할 수도 있습니다. MSAL.NET 2.0.0-preview부터 MSAL.NET 사용도 지원 합니다 **embedded** webview 옵션입니다. ADAL.NET, 포함 된 웹 보기는 옵션만 지원 합니다.

Xamarin을 대상으로 하는 MSAL.NET을 사용 하 여 개발자가 포함 된 웹 보기 또는 시스템 브라우저를 사용 하도록 선택할 수 있습니다. 사용자가 선택한 대상으로 지정할 사용자 환경 및 보안 문제에 따라입니다.

현재, MSAL.NET Android 및 iOS 브로커를 아직 지원 하지 않습니다. 따라서에서 single sign-on (SSO)을 제공 해야 할 경우 시스템 브라우저 중일 수 있습니다 더 나은 옵션입니다. MSAL.NET 백로그에는 포함 된 웹 브라우저를 사용 하 여 브로커를 지원 합니다.

### <a name="differences-between-embedded-webview-and-system-browser"></a>포함 된 웹 보기 및 시스템 브라우저 간의 차이점 
몇 가지 visual 차이점이 embedded webview와 시스템 브라우저 간에 MSAL.NET에서.

**대화형 로그인 포함 된 웹 보기를 사용 하 여 MSAL.NET을 사용 하 여:**

![포함 된](media/msal-net-web-browsers/embedded-webview.png)

**대화형 로그인 시스템 브라우저를 사용 하 여 MSAL.NET을 사용 하 여:**

![시스템 브라우저](media/msal-net-web-browsers/system-browser.png)

### <a name="developer-options"></a>개발자 옵션

MSAL.NET을 사용 하 여 개발자는 몇 가지 옵션이 있습니다 STS에서 대화형 대화 상자를 표시 합니다.

- **시스템 브라우저입니다.** 시스템 브라우저는 라이브러리에 기본적으로 설정 됩니다. Android를 사용 하는 경우 읽을 [시스템 브라우저](msal-net-system-browser-android-considerations.md) 인증에 대 한 지원 되는 브라우저에 대 한 특정 정보에 대 한 합니다. Android에서 시스템 브라우저를 사용 하는 경우에 장치에 사용자 지정 탭 Chrome을 지 원하는 브라우저 하는 것이 좋습니다.  그렇지 않으면 인증이 실패할 수 있습니다.
- **포함 된 웹 보기입니다.** Webview MSAL.NET을 포함 하는 전용을 사용 하는 `AcquireTokenInteractively` 매개 변수 작성기 포함을 `WithUseEmbeddedWebView()` 메서드.

    iOS

    ```csharp
    AuthenticationResult authResult;
    authResult = app.AcquireTokenInteractively(scopes)
                    .WithUseEmbeddedWebView(useEmbeddedWebview)
                    .ExecuteAsync();
    ```

    Android:

    ```csharp
    authResult = app.AcquireTokenInteractively(scopes)
                .WithParentActivityOrWindow(activity)
                .WithUseEmbeddedWebView(useEmbeddedWebview)
                .ExecuteAsync();
    ```

#### <a name="choosing-between-embedded-web-browser-or-system-browser-on-xamarinios"></a>포함 된 웹 브라우저 또는 Xamarin.iOS에서 시스템 브라우저 간의 선택

IOS 앱에서의 `AppDelegate.cs` 초기화할 수 있습니다 합니다 `ParentWindow` 를 `null`입니다. IOS에서 사용 되지 않습니다.

```csharp
App.ParentWindow = null; // no UI parent on iOS
```

#### <a name="choosing-between-embedded-web-browser-or-system-browser-on-xamarinandroid"></a>포함 된 웹 브라우저 또는 Xamarin.Android에서 시스템 브라우저 간의 선택

Android 앱에서의 `MainActivity.cs` 을 인증 결과를 다시 가져오도록 부모 활동을 설정할 수 있습니다.

```csharp
 App.ParentWindow = this;
```

그런 다음는 `MainPage.xaml.cs`:

```csharp
authResult = await App.PCA.AcquireTokenInteractive(App.Scopes)
                      .WithParentActivityOrWindow(App.ParentWindow)
                      .WithUseEmbeddedWebView(true)
                      .ExecuteAsync();
```

#### <a name="detecting-the-presence-of-custom-tabs-on-xamarinandroid"></a>Xamarin.Android의 사용자 지정 탭의 존재를 검색합니다.

호출 하 여 결정 하는 옵션이 있는 시스템 웹 브라우저를 사용 하 여 브라우저에서 실행 되는 앱을 사용 하 여 SSO를 사용 하도록 설정 하려면 지원 사용자 지정 탭을 사용 하 여 브라우저를가지고 있지 않은 경우 Android 장치에 대 한 사용자 환경에 걱정 하는 경우는 `IsSystemWebViewAvailable()` 에서 메서드 < c 2 > `IPublicClientApplication` 합니다. 이 메서드는 반환 `true` PackageManager를 사용자 지정 탭을 검색 하는 경우 및 `false` 장치에서 검색 되지 않은 경우.

이 메서드를 요구 하 여 반환 된 값에 따라 의사 결정을 수행할 수 있습니다.

- 사용자에 게 사용자 지정 오류 메시지를 반환할 수 있습니다. 예를 들면 다음과 같습니다. "인증을 사용 하 여 계속 하려면 Chrome을 설치 하세요"-OR-
- 포함 된 웹 보기 옵션을 대체 하 고는 포함 된 웹 보기 UI를 시작할 수 있습니다.

아래 코드에 포함 된 웹 보기 옵션을 보여 줍니다.

```csharp
bool useSystemBrowser = app.IsSystemWebviewAvailable();

authResult = await App.PCA.AcquireTokenInteractive(App.Scopes)
                      .WithParentActivityOrWindow(App.ParentWindow)
                      .WithUseEmbeddedWebView(!useSystemBrowser)
                      .ExecuteAsync();
```

## <a name="net-core-does-not-support-interactive-authentication-out-of-the-box"></a>.NET core는 기본적으로 대화형 인증을 지원 하지 않습니다.

.NET Core에 대 한 토큰의 획득 대화형으로 사용할 수 없는 경우 실제로.NET Core는 UI을 아직 제공 하지 않습니다. .NET Core 응용 프로그램에 대화형 로그인을 제공 하려는 경우 하도록 허용할 수 있습니다는 코드와 URL을 대화형으로 로그인으로 사용자에 게 표시 합니다 (참조 [장치 코드 흐름](msal-authentication-flows.md#device-code)).

또는 구현할 수는 [IWithCustomUI](scenario-desktop-acquire-token.md#withcustomwebui) 인터페이스 및 고유한 브라우저를 제공 합니다.