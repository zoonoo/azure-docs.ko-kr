---
title: 웹 Api를 호출 하는 모바일 앱 구성-Microsoft identity platform | Microsoft
description: 웹 Api를 호출 하는 모바일 앱을 빌드하는 방법에 대해 알아봅니다 (앱 코드 구성).
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/23/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3d7148b104c723d124a954cf858ca77ff6552f94
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75423803"
---
# <a name="mobile-app-that-calls-web-apis---code-configuration"></a>웹 Api를 호출 하는 모바일 앱-코드 구성

응용 프로그램을 만들었으면 앱 등록 매개 변수를 사용 하 여 코드를 구성 하는 방법을 배웁니다. 또한 모바일 응용 프로그램에는 이러한 앱을 빌드하는 데 사용 되는 프레임 워크에 맞추기 위해 수행 해야 하는 몇 가지 복잡 한 세부 사항이 있습니다.

## <a name="msal-libraries-supporting-mobile-apps"></a>모바일 앱을 지 원하는 MSAL 라이브러리

모바일 앱을 지 원하는 Microsoft 라이브러리는 다음과 같습니다.

  MSAL 라이브러리 | Description
  ------------ | ----------
  ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | 이식 가능한 응용 프로그램을 개발 합니다. 모바일 응용 프로그램을 빌드하는 데 지원 되는 플랫폼은 UWP, Xamarin.ios 및 MSAL.NET입니다.
  ![MSAL.iOS](media/sample-v2-code/logo_iOS.png) <br/> MSAL.iOS | 목적-C 또는 Swift를 사용 하 여 네이티브 iOS 응용 프로그램을 개발 하려면
  ![MSAL.Android](media/sample-v2-code/logo_android.png) <br/> MSAL.Android | Android 용 Java에서 네이티브 Android 응용 프로그램을 개발 하려면

## <a name="instantiating-the-application"></a>응용 프로그램 인스턴스화

### <a name="android"></a>Android

모바일 응용 프로그램은 `PublicClientApplication` 클래스를 사용 합니다. 다음은이를 인스턴스화하는 방법입니다.

```Java
PublicClientApplication sampleApp = new PublicClientApplication(
                    this.getApplicationContext(),
                    R.raw.auth_config);
```

### <a name="ios"></a>iOS

IOS의 모바일 응용 프로그램은 `MSALPublicClientApplication` 클래스를 인스턴스화해야 합니다.

Objective-C:

```objc
NSError *msalError = nil;
     
MSALPublicClientApplicationConfig *config = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"];    
MSALPublicClientApplication *application = [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&msalError];
```

Swift:
```swift
let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>")
if let application = try? MSALPublicClientApplication(configuration: config){ /* Use application */}
```

기본 기관을 재정의 하거나, 리디렉션 URI를 지정 하거나, MSAL 토큰 캐싱 동작을 변경할 수 있는 [추가 MSALPublicClientApplicationConfig 속성이](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALPublicClientApplicationConfig.html#/Configuration%20options) 있습니다. 

### <a name="xamarin-or-uwp"></a>Xamarin 또는 UWP

다음 단락에서는 Xamarin.ios, Xamarin Android 및 UWP 앱 용 응용 프로그램을 인스턴스화하는 방법을 설명 합니다.

#### <a name="instantiating-the-application"></a>응용 프로그램 인스턴스화

Xamarin 또는 UWP에서 응용 프로그램을 인스턴스화하는 가장 간단한 방법은 다음과 같습니다. 여기서 `ClientId`는 등록 된 앱의 Guid입니다.

```csharp
var app = PublicClientApplicationBuilder.Create(clientId)
                                        .Build();
```

UI 부모를 설정 하 고, 기본 인증 기관을 재정의 하 고, 원격 분석을 위해 클라이언트 이름과 버전을 지정 하 고, 리디렉션 URI를 지정 하 고, 사용할 Http 팩터리를 지정 하 여 프록시를 처리 하 고 원격 분석 및 로깅을 지정 하는*매개 변수* 메서드가 추가로 있습니다. 다음 단락의 항목입니다.

##### <a name="specifying-the-parent-uiwindowactivity"></a>부모 UI/창/작업 지정

Android에서는 대화형 인증을 수행 하기 전에 부모 활동을 전달 해야 합니다. IOS에서 broker를 사용 하는 경우에는 ViewController에 전달 해야 합니다. UWP에서와 동일한 방식으로 부모 창에 전달 하는 것이 좋습니다. 이는 토큰을 가져올 때 가능 하지만, 응용 프로그램 생성 시 UIParent를 반환 하는 대리자를 지정 하는 것도 가능 합니다.

```csharp
IPublicClientApplication application = PublicClientApplicationBuilder.Create(clientId)
  .ParentActivityOrWindowFunc(() => parentUi)
  .Build();
```

Android에서는 [여기](https://github.com/jamesmontemagno/CurrentActivityPlugin)`CurrentActivityPlugin`를 사용 하는 것이 좋습니다.  그러면 `PublicClientApplication` builder 코드가 다음과 같이 표시 됩니다.

```csharp
// Requires MSAL.NET 4.2 or above
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => CrossCurrentActivity.Current)
  .Build();
```

##### <a name="more-app-building-parameters"></a>추가 앱 빌드 매개 변수

- `PublicClientApplicationBuilder`에서 사용할 수 있는 모든 한정자 목록은 참조 설명서 [Publicclientapplicationbuilder](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationbuilder#methods) 를 참조 하세요.
- `PublicClientApplicationOptions`에서 제공 하는 모든 옵션에 대 한 설명은 참조 설명서의 [Publicclientapplicationoptions](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationoptions)를 참조 하세요.

## <a name="xamarin-ios-specific-considerations"></a>Xamarin iOS 관련 고려 사항

Xamarin iOS에서는 MSAL.NET을 사용할 때 고려해 야 할 몇 가지 고려 사항이 있습니다.

1. [`AppDelegate`에서 `OpenUrl` 함수를 재정의 하 고 구현 합니다.](msal-net-xamarin-ios-considerations.md#implement-openurl)
1. [키 집합 그룹 사용](msal-net-xamarin-ios-considerations.md#enable-keychain-access)
1. [토큰 캐시 공유 사용](msal-net-xamarin-ios-considerations.md#enable-token-cache-sharing-across-ios-applications)
1. [키 집합 액세스 사용](msal-net-xamarin-ios-considerations.md#enable-keychain-access)

세부 정보는 [Xamarin iOS 고려 사항](msal-net-xamarin-ios-considerations.md) 에 제공 됩니다.

## <a name="msal-for-ios-and-macos-specific-considerations"></a>IOS 및 macOS 관련 고려 사항에 대 한 MSAL

IOS 및 macOS에 MSAL을 사용 하는 경우에도 비슷한 고려 사항이 적용 됩니다.

1. [`openURL` 콜백 구현](#brokered-authentication-for-msal-for-ios-and-macos)
2. [키 집합 액세스 그룹 사용](howto-v2-keychain-objc.md)
3. [브라우저 및 웹 보기 사용자 지정](customize-webviews.md)

## <a name="xamarin-android-specific-considerations"></a>Xamarin Android 관련 고려 사항

Xamarin Android 세부 사항은 다음과 같습니다.

- [인증 흐름의 대화형 부분이 종료 되 면 컨트롤이 MSAL으로 다시 이동 하도록 보장](msal-net-xamarin-android-considerations.md#ensuring-control-goes-back-to-msal-once-the-interactive-portion-of-the-authentication-flow-ends)
- [Android 매니페스트 업데이트](msal-net-xamarin-android-considerations.md#update-the-android-manifest)
- [포함 된 웹 보기 사용 (선택 사항)](msal-net-xamarin-android-considerations.md#use-the-embedded-web-view-optional)
- [문제 해결](msal-net-xamarin-android-considerations.md#troubleshooting)

세부 정보는 [Xamarin Android 고려 사항](msal-net-xamarin-android-considerations.md) 에서 제공 됩니다.

마지막으로, Android에서 브라우저에 대해 알아야 할 몇 가지 specificities 있습니다. [MSAL.NET를 사용 하 여 Xamarin Android 관련 고려 사항](msal-net-system-browser-android-considerations.md) 에 설명 되어 있습니다.

#### <a name="uwp-specific-considerations"></a>UWP 관련 고려 사항

UWP에서는 회사 네트워크를 사용할 수 있습니다. UWP에서 MSAL 라이브러리를 사용 하는 방법에 대 한 자세한 내용은 [MSAL.NET의 유니버설 Windows 플랫폼 관련 고려 사항](msal-net-uwp-considerations.md)을 참조 하세요.

## <a name="configuring-the-application-to-use-the-broker"></a>Broker를 사용 하도록 응용 프로그램 구성

### <a name="why-use-brokers-in-ios-and-android-applications"></a>IOS 및 Android 응용 프로그램에서 broker를 사용 하는 이유

Android 및 iOS에서 broker를 사용 하도록 설정 합니다.

- 장치가 AAD에 등록 된 경우 SSO (Single Sign On) 사용자는 각 응용 프로그램에 로그인 할 필요가 없습니다.
- 장치 id입니다. 작업 공간에 연결 된 장치에서 만든 장치 인증서에 액세스 하 여 Azure AD 장치 관련 조건부 액세스 정책을 사용 하도록 설정 합니다.
- 응용 프로그램 id 확인. 응용 프로그램은 broker를 호출할 때 리디렉션 url을 전달 하 고 broker는이를 확인 합니다.

### <a name="enable-the-broker-on-xamarin"></a>Xamarin에서 broker 사용

이러한 기능 중 하나를 사용 하도록 설정 하려면 `PublicClientApplicationBuilder.CreateApplication` 메서드를 호출할 때 `WithBroker()` 매개 변수를 사용 합니다. `.WithBroker()`는 기본적으로 true로 설정 됩니다. [Xamarin.ios](#brokered-authentication-for-xamarinios)에 대 한 다음 단계를 수행 합니다.

### <a name="enable-the-broker-for-msal-for-android"></a>Android에 대 한 MSAL에 broker를 사용 하도록 설정

Android에서 broker를 사용 하는 방법에 대 한 자세한 내용은 [android에서](brokered-auth.md) 조정 된 인증을 참조 하세요. 

### <a name="enable-the-broker-for-msal-for-ios-and-macos"></a>IOS 및 macOS 용 MSAL에 대해 broker를 사용 하도록 설정

조정 된 인증은 iOS 및 macOS 용 MSAL의 AAD 시나리오에 대해 기본적으로 사용 하도록 설정 됩니다. [IOS 및 macOS 용 Msal](#brokered-authentication-for-msal-for-ios-and-macos)에 대 한 조정 된 인증 지원을 위해 응용 프로그램을 구성 하려면 다음 단계를 수행 합니다. 일부 단계는 iOS [용 Msal](#brokered-authentication-for-xamarinios) 과 [Ios 및 Macos 용 msal](#brokered-authentication-for-msal-for-ios-and-macos)간에 차이가 있습니다.

### <a name="brokered-authentication-for-xamarinios"></a>Xamarin.ios에 대 한 조정 된 인증

다음 단계를 수행 하 여 Xamarin.ios 앱이 [Microsoft Authenticator](https://itunes.apple.com/us/app/microsoft-authenticator/id983156458) 앱과 통신할 수 있도록 설정 합니다.

#### <a name="step-1-enable-broker-support"></a>1 단계: broker 지원 사용

Broker 지원은`PublicClientApplication` 별로 사용 하도록 설정 됩니다. 기본적으로 사용하지 않도록 설정되어 있습니다. `PublicClientApplicationBuilder`를 통해 `PublicClientApplication`를 만들 때 `WithBroker()` 매개 변수를 사용 해야 합니다 (기본적으로 true로 설정).

```csharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos) // $"msauth.{Bundle.Id}://auth" (see step 6 below)
                .Build();
```

#### <a name="step-2-update-appdelegate-to-handle-the-callback"></a>2 단계: 콜백을 처리 하도록 AppDelegate 업데이트

MSAL.NET가 broker를 호출 하면 broker는 `AppDelegate.OpenUrl` 메서드를 통해 응용 프로그램을 다시 호출 합니다. MSAL은 broker의 응답을 기다리기 때문에 MSAL.NET를 호출 하려면 응용 프로그램을 공동으로 전환 해야 합니다. 이렇게 하려면 `AppDelegate.cs` 파일을 업데이트 하 여 아래 메서드를 재정의 합니다.

```csharp
public override bool OpenUrl(UIApplication app, NSUrl url,
                             string sourceApplication,
                             NSObject annotation)
{
 if (AuthenticationContinuationHelper.IsBrokerResponse(sourceApplication))
 {
  AuthenticationContinuationHelper.SetBrokerContinuationEventArgs(url);
  return true;
 }
 else if (!AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(url))
 {
  return false;
 }
 return true;
}
```

이 메서드는 응용 프로그램이 시작 될 때마다 호출 되며, broker에서 응답을 처리 하 고 MSAL.NET에서 시작한 인증 프로세스를 완료할 수 있는 기회로 사용 됩니다.

#### <a name="step-3-set-a-uiviewcontroller"></a>3 단계: UIViewController () 설정

Xamarin iOS를 사용 하면 일반적으로 개체 창을 설정할 필요가 없지만,이 경우에는 broker에서 응답을 보내고 받을 수 있습니다. `AppDelegate.cs`에도 ViewController를 설정 합니다.

개체 창을 설정 하려면 다음을 수행 합니다.

1) `AppDelegate.cs`에서 `App.RootViewController`를 새 `UIViewController()`설정 합니다. 이렇게 하면 broker를 호출 하는 `UIViewController` 있는지 확인 됩니다. 올바르게 설정 되지 않은 경우 다음과 같은 오류가 발생할 수 있습니다. `"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`
2) AcquireTokenInteractive 호출에서 `.WithParentActivityOrWindow(App.RootViewController)` 사용 하 여 사용할 개체 창에 대 한 참조를 전달 합니다.

**예:**

`App.cs`의 경우:
```csharp
   public static object RootViewController { get; set; }
```
`AppDelegate.cs`의 경우:
```csharp
   LoadApplication(new App());
   App.RootViewController = new UIViewController();
```
토큰 획득 호출에서 다음을 수행 합니다.
```csharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow(App.RootViewController)
             .ExecuteAsync();
```

#### <a name="step-4-register-a-url-scheme"></a>4 단계: URL 구성표 등록

MSAL.NET는 Url을 사용 하 여 broker를 호출한 다음 broker 응답을 앱으로 다시 반환 합니다. 왕복을 완료 하려면 `Info.plist` 파일에 앱에 대 한 URL 체계를 등록 해야 합니다.

`msauth`를 사용 하 여 `CFBundleURLSchemes`에 접두사를 붙입니다. 그런 다음 끝에 `CFBundleURLName`를 추가 합니다.

`$"msauth.(BundleId)"`

**예:** 
`msauth.com.yourcompany.xforms`

> [!NOTE]
> 이 URL 구성표는 broker에서 응답을 받을 때 앱을 고유 하 게 식별 하는 데 사용 되는 RedirectUri의 일부가 됩니다.

```XML
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

#### <a name="step-5-lsapplicationqueriesschemes"></a>5 단계: LSApplicationQueriesSchemes

MSAL은 `–canOpenURL:`를 사용 하 여 broker가 장치에 설치 되어 있는지 확인 합니다. IOS 9에서 Apple은 응용 프로그램에서 쿼리할 수 있는 스키마를 잠 궜 습니다.

`Info.plist` 파일의 `LSApplicationQueriesSchemes` 섹션에 **`msauthv2`** 를 **추가** 합니다.

```XML 
<key>LSApplicationQueriesSchemes</key>
    <array>
      <string>msauthv2</string>
    </array>
```

### <a name="brokered-authentication-for-msal-for-ios-and-macos"></a>IOS 및 macOS 용 MSAL에 대 한 조정 된 인증

조정 된 인증은 AAD 시나리오에 대해 기본적으로 사용 하도록 설정 됩니다.

#### <a name="step-1-update-appdelegate-to-handle-the-callback"></a>1 단계: 콜백을 처리 하도록 AppDelegate 업데이트

IOS 및 macOS 용 MSAL이 broker를 호출할 때 broker는 다시 `openURL` 메서드를 통해 응용 프로그램을 다시 호출 합니다. MSAL은 broker의 응답을 기다리기 때문에 MSAL을 호출 하려면 응용 프로그램이 협력 해야 합니다. 이렇게 하려면 `AppDelegate.m` 파일을 업데이트 하 여 아래 메서드를 재정의 합니다.

Objective-C:

```objc
- (BOOL)application:(UIApplication *)app
            openURL:(NSURL *)url
            options:(NSDictionary<UIApplicationOpenURLOptionsKey,id> *)options
{
    return [MSALPublicClientApplication handleMSALResponse:url 
                                         sourceApplication:options[UIApplicationOpenURLOptionsSourceApplicationKey]];
}
```

Swift:

```swift
    func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
        
        guard let sourceApplication = options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String else {
            return false
        }
        
        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: sourceApplication)
    }
```

IOS 13 이상에서 UISceneDelegate을 채택 하는 경우 MSAL 콜백은 대신 UISceneDelegate의 `scene:openURLContexts:`에 배치 해야 합니다 ( [Apple 설명서](https://developer.apple.com/documentation/uikit/uiscenedelegate/3238059-scene?language=objc)참조). MSAL `handleMSALResponse:sourceApplication:`는 각 URL에 대해 한 번만 호출 해야 합니다.

#### <a name="step-2-register-a-url-scheme"></a>2 단계: URL 구성표 등록

IOS 및 macOS 용 MSAL은 Url을 사용 하 여 broker를 호출한 다음 broker 응답을 앱으로 다시 반환 합니다. 왕복을 완료 하려면 `Info.plist` 파일에 앱에 대 한 URL 체계를 등록 해야 합니다.

`msauth`를 사용 하 여 사용자 지정 URL 체계에 접두사를 지정 합니다. 그런 다음 **번들 식별자** 를 끝에 추가 합니다.

`msauth.(BundleId)`

**예:** 
`msauth.com.yourcompany.xforms`

> [!NOTE]
> 이 URL 구성표는 broker에서 응답을 받을 때 앱을 고유 하 게 식별 하는 데 사용 되는 RedirectUri의 일부가 됩니다. [Azure Portal](https://portal.azure.com)에서 응용 프로그램에 대 한 `msauth.(BundleId)://auth` 형식의 redirecturi가 등록 되어 있는지 확인 합니다.

```XML
<key>CFBundleURLTypes</key>
<array>
    <dict>
        <key>CFBundleURLSchemes</key>
        <array>
            <string>msauth.[BUNDLE_ID]</string>
        </array>
    </dict>
</array>
```

#### <a name="step-3-lsapplicationqueriesschemes"></a>3 단계: LSApplicationQueriesSchemes

설치 된 경우 Microsoft Authenticator를 호출할 수 있도록 **`LSApplicationQueriesSchemes`추가** 합니다.
Xcode 11 이상으로 앱을 컴파일하는 경우에는 "msauthv3" 체계가 필요 합니다. 

```XML 
<key>LSApplicationQueriesSchemes</key>
<array>
  <string>msauthv2</string>
  <string>msauthv3</string>
</array>
```

### <a name="brokered-authentication-for-xamarinandroid"></a>Xamarin Android에 대 한 조정 된 인증

MSAL.NET는 Android 용 broker를 아직 지원 하지 않습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [토큰 획득](scenario-mobile-acquire-token.md)
