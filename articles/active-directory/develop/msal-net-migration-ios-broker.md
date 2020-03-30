---
title: 브로커를 사용하여 MSAL.NET으로 Xamarin 앱 마이그레이션
titleSuffix: Microsoft identity platform
description: Microsoft 인증기를 사용하는 Xamarin iOS 앱을 ADAL.NET MSAL.NET 마이그레이션하는 방법을 알아봅니다.
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/08/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: de259daa7fd27cc4f138c294a7f347502ca482a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77185821"
---
# <a name="migrate-ios-applications-that-use-microsoft-authenticator-from-adalnet-to-msalnet"></a>마이크로소프트 인증기를 사용하는 iOS 응용 프로그램을 ADAL.NET MSAL.NET 마이그레이션

.NET(ADAL.NET) 및 iOS 브로커에 대한 Azure Active Directory 인증 라이브러리를 사용했습니다. 이제 릴리스 4.3 이후부터 iOS의 브로커를 지원하는 .NET(MSAL.NET)에 대한 [Microsoft 인증 라이브러리로](msal-overview.md) 마이그레이션할 차례입니다. 

어디서 시작해야 합니까? 이 문서에서는 ADAL에서 MSAL로 Xamarin iOS 앱을 마이그레이션하는 데 도움이 됩니다.

## <a name="prerequisites"></a>사전 요구 사항
이 문서에서는 이미 iOS 브로커와 통합된 Xamarin iOS 앱이 있다고 가정합니다. 그렇지 않은 경우 MSAL.NET 직접 이동하여 브로커 구현을 시작합니다. 새 응용 프로그램을 사용하여 MSAL.NET iOS 브로커를 호출하는 방법에 대한 자세한 내용은 [이 설명서를](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Leveraging-the-broker-on-iOS#why-use-brokers-on-xamarinios-and-xamarinandroid-applications)참조하십시오.

## <a name="background"></a>배경

### <a name="what-are-brokers"></a>브로커란?

브로커는 안드로이드와 아이폰 OS에서 마이크로 소프트가 제공하는 응용 프로그램입니다. (iOS 및 Android에서 [Microsoft 인증자](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6) 앱, Android의 Intune 회사 포털 앱 참조). 

다음을 활성화합니다.

- Single Sign-On
- 일부 [조건부 액세스 정책에](../conditional-access/overview.md)필요한 장치 식별. 자세한 내용은 [장치 관리를](../conditional-access/concept-conditional-access-conditions.md#device-platforms)참조하십시오.
- 일부 엔터프라이즈 시나리오에서도 필요한 응용 프로그램 식별 확인입니다. 자세한 내용은 [MAM(모바일 응용 프로그램 관리)을](https://docs.microsoft.com/intune/mam-faq)참조하십시오.

## <a name="migrate-from-adal-to-msal"></a>ADAL에서 MSAL로 마이그레이션

### <a name="step-1-enable-the-broker"></a>1단계: 브로커 사용

<table>
<tr><td>현재 ADAL 코드:</td><td>MSAL 대응:</td></tr>
<tr><td>
ADAL.NET 브로커 지원은 인증컨텍스트별로 활성화되었습니다. 기본적으로 사용하지 않도록 설정되어 있습니다. 당신은 설정했다 

`useBroker`브로커를 `PlatformParameters` 호출하는 생성자에서 true로 플래그를 표시합니다.

```csharp
public PlatformParameters(
        UIViewController callerViewController, 
        bool useBroker)
```
또한 플랫폼별 코드에서 이 예제에서는 iOS용 페이지 렌더러에서`useBroker` 
true로 플래그:
```csharp
page.BrokerParameters = new PlatformParameters(
          this, 
          true, 
          PromptBehavior.SelectAccount);
```

그런 다음 acquire token 호출에 매개 변수를 포함합니다.
```csharp
 AuthenticationResult result =
                    await
                        AuthContext.AcquireTokenAsync(
                              Resource, 
                              ClientId, 
                              new Uri(RedirectURI), 
                              platformParameters)
                              .ConfigureAwait(false);
```

</td><td>
MSAL.NET 브로커 지원은 PublicClientApplication단위로 활성화됩니다. 기본적으로 사용하지 않도록 설정되어 있습니다. 활성화하려면 

`WithBroker()`브로커를 호출하기 위해 매개 변수 (기본적으로 true로 설정):

```csharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos)
                .Build();
```
획득 토큰 호출에서:
```csharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow(App.RootViewController)
             .ExecuteAsync();
```
</table>

### <a name="step-2-set-a-uiviewcontroller"></a>2단계: UIViewController() 설정
ADAL.NET UIViewController의 일부로 전달했습니다. `PlatformParameters` (1단계의 예제 참조). MSAL.NET 개발자에게 더 많은 유연성을 제공하기 위해 개체 창이 사용되지만 일반 iOS 사용에서는 필요하지 않습니다. 브로커를 사용하려면 브로커로부터 응답을 보내고 받을 수 있도록 개체 창을 설정합니다. 
<table>
<tr><td>현재 ADAL 코드:</td><td>MSAL 대응:</td></tr>
<tr><td>
UIViewController가 

`PlatformParameters`iOS 별 플랫폼에서

```csharp
page.BrokerParameters = new PlatformParameters(
          this, 
          true, 
          PromptBehavior.SelectAccount);
```
</td><td>
MSAL.NET iOS의 개체 창을 설정하는 두 가지 작업을 수행합니다.

1. 에서 `AppDelegate.cs`새 `App.RootViewController` `UIViewController()`로 설정합니다. 이 할당을 통해 브로커에 대한 호출이 있는 UIViewController가 있는지 확인합니다. 올바르게 설정되지 않으면 다음과 같은 오류가 발생할 수 있습니다.`"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`
1. AcquireTokenInteractive 호출에서 에서 `.WithParentActivityOrWindow(App.RootViewController)`을 사용하고 사용할 개체 창에 대한 참조를 전달합니다.

**예를 들어:**

`App.cs`의 경우:
```csharp
   public static object RootViewController { get; set; }
```
`AppDelegate.cs`의 경우:
```csharp
   LoadApplication(new App());
   App.RootViewController = new UIViewController();
```
획득 토큰 호출에서:
```csharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow(App.RootViewController)
             .ExecuteAsync();
```

</table>

### <a name="step-3-update-appdelegate-to-handle-the-callback"></a>3단계: 콜백을 처리하도록 AppDelegate 업데이트
ADAL과 MSAL은 브로커를 호출하고 브로커는 차례로 `OpenUrl` `AppDelegate` 클래스의 메서드를 통해 응용 프로그램에 다시 호출합니다. 자세한 내용은 [이 설명서를](msal-net-use-brokers-with-xamarin-apps.md#step-3-update-appdelegate-to-handle-the-callback)참조하십시오.

ADAL.NET MSAL.NET 사이에는 변화가 없습니다.

### <a name="step-4-register-a-url-scheme"></a>4단계: URL 구성표 등록
ADAL.NET 및 MSAL.NET URL을 사용하여 브로커를 호출하고 브로커 응답을 앱으로 반환합니다. 앱의 `Info.plist` 파일에 URL 구성표를 다음과 같이 등록합니다.

<table>
<tr><td>현재 ADAL 코드:</td><td>MSAL 대응:</td></tr>
<tr><td>
URL 구성표는 앱에 고유합니다.
</td><td>
이 

`CFBundleURLSchemes`이름은 다음을 포함해야 합니다. 

`msauth.`

접두사로, 그 다음에`CFBundleURLName`

예: `$"msauth.(BundleId")`

```csharp
 <key>CFBundleURLTypes</key>
    <array>
      <dict>
        <key>CFBundleTypeRole</key>
        <string>Editor</string>
        <key>CFBundleURLName</key>
        <string>com.yourcompany.xforms</string>
        <key>CFBundleURLSchemes</key>
        <array>
          <string>msauth.com.yourcompany.xforms</string>
        </array>
      </dict>
    </array>
```

> [!NOTE]
> 이 URL 체계는 브로커로부터 응답을 받을 때 앱을 고유하게 식별하는 데 사용되는 리디렉션 URI의 일부가 됩니다.

</table>

### <a name="step-5-add-the-broker-identifier-to-the-lsapplicationqueriesschemes-section"></a>5 단계: LSApplicationQuerySchemes 섹션에 브로커 식별자를 추가합니다.

ADAL.NET 브로커가 `-canOpenURL:` 장치에 설치되어 있는지 확인하는 데 사용할 MSAL.NET. 다음과 같이 info.plist 파일의 lsApplicationQuerySchemes 섹션에 iOS 브로커에 대한 올바른 식별자를 추가합니다.

<table>
<tr><td>현재 ADAL 코드:</td><td>MSAL 대응:</td></tr>
<tr><td>
사용 

`msauth`


```csharp
<key>LSApplicationQueriesSchemes</key>
<array>
     <string>msauth</string>
</array>
```
</td><td>
사용 

`msauthv2`


```csharp
<key>LSApplicationQueriesSchemes</key>
<array>
     <string>msauthv2</string>
     <string>msauthv3</string>
</array>
```
</table>

### <a name="step-6-register-your-redirect-uri-in-the-portal"></a>6단계: 포털에서 리디렉션 URI 등록

ADAL.NET MSAL.NET 둘 다 브로커를 대상으로 할 때 리디렉션 URI에 추가 요구 사항을 추가합니다. 포털에서 응용 프로그램에 리디렉션 URI를 등록합니다.
<table>
<tr><td>현재 ADAL 코드:</td><td>MSAL 대응:</td></tr>
<tr><td>

`"<app-scheme>://<your.bundle.id>"`

예제: 

`mytestiosapp://com.mycompany.myapp`
</td><td>

`$"msauth.{BundleId}://auth"`

예제:

`public static string redirectUriOnIos = "msauth.com.yourcompany.XForms://auth"; `

</table>

포털에서 리디렉션 URI를 등록하는 방법에 대한 자세한 내용은 [Xamarin.iOS 응용 프로그램의 브로커 활용을](msal-net-use-brokers-with-xamarin-apps.md#step-8-make-sure-the-redirect-uri-is-registered-with-your-app)참조하십시오.

## <a name="next-steps"></a>다음 단계

MSAL.NET 함께 [Xamarin iOS 관련 고려 사항에](msal-net-xamarin-ios-considerations.md)대해 알아봅니다. 
