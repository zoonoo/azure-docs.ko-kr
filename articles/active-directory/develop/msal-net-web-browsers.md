---
title: 웹 브라우저 사용(MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: .NET(MSAL.NET)에 대한 Microsoft 인증 라이브러리에서 Xamarin Android를 사용할 때의 특정 고려 사항에 대해 알아봅니다.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/16/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: ed1f47ae99f6346a932d0fe94be7586dc25a672f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262738"
---
# <a name="using-web-browsers-msalnet"></a>웹 브라우저 사용(MSAL.NET)

대화형 인증을 위해서는 웹 브라우저가 필요합니다. 기본적으로 MSAL.NET Xamarin.iOS 및 Xamarin.Android에서 [시스템 웹 브라우저를](#system-web-browser-on-xamarinios-xamarinandroid) 지원합니다. 그러나 [Xamarin.iOS](#choosing-between-embedded-web-browser-or-system-browser-on-xamarinios) 및 [Xamarin.Android](#detecting-the-presence-of-custom-tabs-on-xamarinandroid) 앱에서 요구 사항(UX, 단일 사인온(SSO), 보안에 따라 [임베디드 웹 브라우저를 활성화할 수도](#enable-embedded-webviews-on-ios-and-android) 있습니다. 또한 Chrome 또는 Android에서 Chrome 사용자 지정 탭을 지원하는 브라우저를 기반으로 사용할 웹 브라우저를 [동적으로 선택할](#detecting-the-presence-of-custom-tabs-on-xamarinandroid) 수도 있습니다. MSAL.NET .NET Core 데스크톱 응용 프로그램의 시스템 브라우저만 지원합니다.

## <a name="web-browsers-in-msalnet"></a>MSAL.NET 웹 브라우저

### <a name="interaction-happens-in-a-web-browser"></a>상호 작용은 웹 브라우저에서 발생합니다.

대화식으로 토큰을 획득할 때 대화 상자의 내용은 라이브러리가 아니라 STS(보안 토큰 서비스)에서 제공하는 다는 것을 이해하는 것이 중요합니다. 인증 끝점은 웹 브라우저 또는 웹 컨트롤에서 렌더링되는 상호 작용을 제어하는 일부 HTML 및 JavaScript를 다시 보냅니다. STS가 HTML 상호 작용을 처리하도록 허용하면 다음과 같은 많은 이점이 있습니다.

- 암호(입력된 암호)는 응용 프로그램이나 인증 라이브러리에 저장되지 않습니다.
- 다른 ID 공급자(예: MSAL가 있는 개인 계정 또는 Azure AD B2C가 있는 소셜 계정으로 로그인)를 사용하여 리디렉션할 수 있습니다.
- 예를 들어 사용자가 인증 단계에서 MFA(다중 요소 인증)를 수행하도록(Windows Hello 핀입력 또는 휴대폰또는 휴대폰의 인증 앱에서 호출됨) STS가 조건부 액세스를 제어할 수 있습니다. 필요한 다단계 인증이 아직 설정되지 않은 경우 사용자는 동일한 대화 상자에서 적시에 설정할 수 있습니다.  사용자는 휴대폰 번호를 입력하고 인증 응용 프로그램을 설치하고 QR 태그를 스캔하여 계정을 추가하도록 안내됩니다. 이 서버 기반 상호 작용은 훌륭한 경험입니다!
- 암호가 만료되면 사용자가 동일한 대화 상자에서 암호를 변경할 수 있습니다(이전 암호 및 새 암호에 대한 추가 필드 제공).
- Azure AD 테넌트 관리자/응용 프로그램 소유자가 제어하는 테넌트 또는 응용 프로그램(이미지)의 브랜딩을 활성화합니다.
- 사용자가 인증 직후 응용 프로그램이 자신의 이름으로 리소스/범위에 액세스할 수 있도록 동의하는 데 동의할 수 있습니다.

### <a name="embedded-vs-system-web-ui"></a>임베디드 대 시스템 웹 UI

MSAL.NET 다중 프레임워크 라이브러리이며 UI 컨트롤에서 브라우저를 호스트하는 프레임워크별 코드가 있습니다(예: .Net Classic에서는 WinForms를 사용하며 Xamarin에서는 네이티브 모바일 컨트롤 등을 사용합니다). 이 컨트롤을 `embedded` 웹 UI라고 합니다. 또는, MSAL.NET 또한 시스템 OS 브라우저를 시작할 수 있습니다.

일반적으로 플랫폼 기본값을 사용하는 것이 좋습니다. 시스템 브라우저는 이전에 로그인 한 사용자를 기억하는 것이 좋습니다. 이 동작을 변경해야 하는 경우`WithUseEmbeddedWebView(bool)`

### <a name="at-a-glance"></a>개요

| 프레임워크        | 포함된 | 시스템 | 기본값 |
| ------------- |-------------| -----| ----- |
| .NET 클래식     | yes | 예^ | 포함된 |
| .NET Core     | 예 | 예^ | 시스템 |
| .NET Standard | 예 | 예^ | 시스템 |
| UWP | yes | 예 | 포함된 |
| Xamarin.Android | yes | yes  | 시스템 |
| Xamarin.iOS | yes | yes  | 시스템 |
| Xamarin.Mac| yes | 예 | 포함된 |

^ 필요http://localhost" " URI 리디렉션

## <a name="system-web-browser-on-xamarinios-xamarinandroid"></a>Xamarin.iOS, 자마린.안드로이드에 시스템 웹 브라우저

기본적으로 MSAL.NET Xamarin.iOS, Xamarin.Android 및 .NET Core에서 시스템 웹 브라우저를 지원합니다. .NET Core가 아닌 UI를 제공하는 모든 플랫폼에 대해 웹 브라우저 컨트롤을 포함하는 라이브러리에서 대화 상자를 제공합니다. MSAL.NET UWP 플랫폼에 대해 .NET 데스크톱 및 WAB에 임베디드 웹 보기를 사용합니다. 그러나, 그것은 기본적으로 자마린 iOS와 자마린 안 드 로이드 응용 프로그램에 대 한 **시스템 웹 브라우저를** 활용. iOS에서는 운영 체제 버전(iOS12, iOS11 및 이전 버전)에 따라 사용할 웹 보기를 선택합니다.

시스템 브라우저를 사용하면 브로커 (회사 포털 / 인증자)를 필요로하지 않고 다른 응용 프로그램 및 웹 응용 프로그램과 SSO 상태를 공유하는 상당한 장점이 있습니다. 시스템 브라우저는 기본적으로 Xamarin iOS 및 Xamarin Android 플랫폼용 MSAL.NET 사용되었기 때문에 이러한 플랫폼에서 시스템 웹 브라우저가 전체 화면을 차지하고 사용자 환경이 더 좋습니다. 시스템 웹 보기는 대화 상자와 구별할 수 없습니다. 그러나 iOS에서는 브라우저가 응용 프로그램을 다시 호출하는 데 동의해야 할 수 있으며 이는 성가신 일 수 있습니다.

## <a name="system-browser-experience-on-net-core"></a>.NET 코어의 시스템 브라우저 환경

.NET Core에서 MSAL.NET 별도의 프로세스로 시스템 브라우저를 시작합니다. MSAL.NET 이 브라우저를 제어할 수 없지만 사용자가 인증을 완료하면 웹 페이지가 MSAL.NET Uri를 가로챌 수 있는 방식으로 리디렉션됩니다.

이 브라우저를 사용하도록 .NET Classic용으로 작성된 앱을 지정하여 구성할 수도 있습니다.

```csharp
await pca.AcquireTokenInteractive(s_scopes)
         .WithUseEmbeddedWebView(false)
```

MSAL.NET 사용자가 브라우저를 탐색하거나 단순히 닫을지 감지할 수 없습니다. 이 기술을 사용하는 앱은 시간 시간을 정의하는 `CancellationToken`것이 좋습니다(via). 사용자가 암호를 변경하거나 다중 요소 인증을 수행하라는 메시지가 표시되는 경우를 고려하려면 최소 몇 분의 시간 초과를 권장합니다.

### <a name="how-to-use-the-default-os-browser"></a>기본 OS 브라우저를 사용하는 방법

MSAL.NET 사용자가 인증을 `http://localhost:port` 완료할 때 AAD가 보내는 코드를 수신 대기하고 가로챌 필요가 있습니다(자세한 내용은 [권한 부여 코드](v2-oauth2-auth-code-flow.md) 참조).

시스템 브라우저를 사용하려면 다음을 수행합니다.

1. 앱 등록 중에 `http://localhost` 리디렉션 uri로 구성(현재 B2C에서 지원되지 않음).
2. PublicClientApplication을 생성할 때 다음 리디렉션 uri를 지정합니다.

```csharp
IPublicClientApplication pca = PublicClientApplicationBuilder
                            .Create("<CLIENT_ID>")
                             // or use a known port if you wish "http://localhost:1234"
                            .WithRedirectUri("http://localhost")  
                            .Build();
```

> [!Note]
> 구성하는 `http://localhost`경우 내부적으로 MSAL.NET 임의의 열린 포트를 찾아 사용합니다.

### <a name="linux-and-mac"></a>리눅스와 맥

리눅스에서, MSAL.NET xdg 오픈 도구를 사용 하 여 기본 OS 브라우저를 엽니다. 문제를 해결하려면 터미널에서 도구를 실행합니다.`xdg-open "https://www.bing.com"`  
Mac에서 브라우저가 호출하여 열립니다.`open <url>`

### <a name="customizing-the-experience"></a>환경 사용자 지정

> [!NOTE]
> 사용자 지정은 MSAL.NET 4.1.0 이상에서 사용할 수 있습니다.

MSAL.NET 토큰을 받을 때 또는 오류가 있는 경우 HTTP 메시지로 응답할 수 있습니다. HTML 메시지를 표시하거나 원하는 URL로 리디렉션할 수 있습니다.

```csharp
var options = new SystemWebViewOptions() 
{
    HtmlMessageError = "<p> An error occured: {0}. Details {1}</p>",
    BrowserRedirectSuccess = new Uri("https://www.microsoft.com");
}

await pca.AcquireTokenInteractive(s_scopes)
         .WithUseEmbeddedWebView(false)
         .WithSystemWebViewOptions(options)
         .ExecuteAsync();
```

### <a name="opening-a-specific-browser-experimental"></a>특정 브라우저 열기(실험)

MSAL.NET 브라우저를 여는 방법을 사용자 지정할 수 있습니다. 예를 들어 기본값인 브라우저를 사용하는 대신 특정 브라우저를 강제로 열 수 있습니다.

```csharp
var options = new SystemWebViewOptions() 
{
    OpenBrowserAsync = SystemWebViewOptions.OpenWithEdgeBrowserAsync
}
```

### <a name="uwp-doesnt-use-the-system-webview"></a>UWP는 시스템 웹뷰를 사용하지 않습니다.

그러나 데스크톱 응용 프로그램의 경우 시스템 웹뷰를 시작하면 사용자가 브라우저를 볼 때 다른 탭이 이미 열려 있을 수 있으므로 사용자 환경이 하위로 이동하게 됩니다. 인증이 발생하면 사용자는 이 창을 닫도록 요청하는 페이지를 얻습니다. 사용자가 주의를 기울이지 않으면 전체 프로세스를 닫을 수 있습니다(인증과 관련이 없는 다른 탭 포함). 데스크톱에서 시스템 브라우저를 활용하려면 로컬 포트를 열고 수신 대기해야 하므로 응용 프로그램에 대한 고급 권한이 필요할 수 있습니다. 개발자, 사용자 또는 관리자는 이 요구 사항에 대해 꺼릴 수 있습니다.

## <a name="enable-embedded-webviews-on-ios-and-android"></a>iOS 및 Android에서 임베디드 웹뷰 활성화

또한 Xamarin.iOS 및 Xamarin.Android 앱에서 임베디드 웹뷰를 활성화할 수도 있습니다. MSAL.NET 2.0.0 미리 보기로 시작하여 MSAL.NET **포함된** 웹뷰 옵션을 사용하여도 지원합니다. ADAL.NET 경우 임베디드 웹뷰가 지원되는 유일한 옵션입니다.

Xamarin을 대상으로 MSAL.NET 사용하는 개발자는 포함된 웹뷰 또는 시스템 브라우저를 사용하도록 선택할 수 있습니다. 대상하려는 사용자 환경 및 보안 문제에 따라 선택할 수 있습니다.

현재 MSAL.NET 아직 안드로이드 및 iOS 브로커를 지원하지 않습니다. 따라서 단일 사인온(SSO)을 제공해야 하는 경우 시스템 브라우저가 여전히 더 나은 옵션일 수 있습니다. 포함된 웹 브라우저를 통해 브로커를 지원하는 MSAL.NET 백로그에 있습니다.

### <a name="differences-between-embedded-webview-and-system-browser"></a>임베디드 웹뷰와 시스템 브라우저의 차이점
MSAL.NET 임베디드 웹뷰와 시스템 브라우저 사이에는 몇 가지 시각적 차이가 있습니다.

**임베디드 웹뷰를 사용하여 MSAL.NET 대화형 로그인:**

![포함된](media/msal-net-web-browsers/embedded-webview.png)

**시스템 브라우저를 사용하여 MSAL.NET 대화형 로그인:**

![시스템 브라우저](media/msal-net-web-browsers/system-browser.png)

### <a name="developer-options"></a>개발자 옵션

MSAL.NET 사용하는 개발자는 STS에서 대화형 대화 상자를 표시하기 위한 몇 가지 옵션이 있습니다.

- **시스템 브라우저.** 시스템 브라우저는 라이브러리에서 기본적으로 설정됩니다. Android를 사용하는 경우 인증에 지원되는 브라우저에 대한 특정 정보를 시스템 [브라우저를](msal-net-system-browser-android-considerations.md) 읽습니다. Android에서 시스템 브라우저를 사용하는 경우 기기에 Chrome 맞춤 탭을 지원하는 브라우저가 있는 것이 좋습니다.  그렇지 않으면 인증이 실패할 수 있습니다.
- **임베디드 웹뷰.** MSAL.NET 포함된 웹뷰만 사용하려면 `AcquireTokenInteractively` 매개 변수 `WithUseEmbeddedWebView()` 빌더에 메서드가 포함되어 있습니다.

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

#### <a name="choosing-between-embedded-web-browser-or-system-browser-on-xamarinios"></a>Xamarin.iOS에서 임베디드 웹 브라우저 또는 시스템 브라우저 중 선택

iOS 앱에서 `AppDelegate.cs` `ParentWindow` `null`을 초기화할 수 있습니다. iOS에서는 사용되지 않습니다.

```csharp
App.ParentWindow = null; // no UI parent on iOS
```

#### <a name="choosing-between-embedded-web-browser-or-system-browser-on-xamarinandroid"></a>Xamarin.Android에서 임베디드 웹 브라우저 또는 시스템 브라우저 중 선택

Android 앱에서 `MainActivity.cs` 상위 활동을 설정하여 인증 결과가 다시 시작되도록 할 수 있습니다.

```csharp
 App.ParentWindow = this;
```

그런 다음 `MainPage.xaml.cs`다음 :

```csharp
authResult = await App.PCA.AcquireTokenInteractive(App.Scopes)
                      .WithParentActivityOrWindow(App.ParentWindow)
                      .WithUseEmbeddedWebView(true)
                      .ExecuteAsync();
```

#### <a name="detecting-the-presence-of-custom-tabs-on-xamarinandroid"></a>Xamarin.Android에서 사용자 지정 탭의 존재 감지

시스템 웹 브라우저를 사용하여 브라우저에서 실행 중인 앱으로 SSO를 사용하도록 설정하지만 사용자 지정 탭 이 지원되는 브라우저가 없는 Android 장치의 사용자 `IsSystemWebViewAvailable()` 환경이 걱정되는 경우 에서 메서드를 호출하여 결정할 수 있습니다. `IPublicClientApplication` 이 메서드는 PackageManager 사용자 지정 탭을 검색 하 고 `true` `false` 장치에서 검색 되지 않은 경우 반환 합니다.

이 메서드에서 반환되는 값과 요구 사항에 따라 다음을 결정할 수 있습니다.

- 사용자에게 사용자 지정 오류 메시지를 반환할 수 있습니다. 예: "인증을 계속하려면 Chrome을 설치하십시오" -OR-
- 포함된 웹뷰 옵션으로 돌아가서 UI를 임베디드 웹뷰로 시작할 수 있습니다.

아래 코드는 포함된 웹뷰 옵션을 보여 주며, 다음을 보여 주며,

```csharp
bool useSystemBrowser = app.IsSystemWebviewAvailable();

authResult = await App.PCA.AcquireTokenInteractive(App.Scopes)
                      .WithParentActivityOrWindow(App.ParentWindow)
                      .WithUseEmbeddedWebView(!useSystemBrowser)
                      .ExecuteAsync();
```

#### <a name="net-core-doesnt-support-interactive-authentication-with-an-embedded-browser"></a>.NET Core는 임베디드 브라우저에서 대화형 인증을 지원하지 않습니다.

.NET Core의 경우 대화식으로 토큰을 획득하는 것은 포함된 웹 보기가 아닌 시스템 웹 브라우저를 통해서만 사용할 수 있습니다. 실제로 .NET Core는 아직 UI를 제공하지 않습니다.
시스템 웹 브라우저로 브라우징 환경을 사용자 지정하려는 경우 [IWithCustomUI](scenario-desktop-acquire-token.md#withcustomwebui) 인터페이스를 구현하고 자체 브라우저를 제공할 수도 있습니다.
