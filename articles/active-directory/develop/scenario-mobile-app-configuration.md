---
title: 웹 API를 호출하는 모바일 앱 구성 | Azure
titleSuffix: Microsoft identity platform
description: 웹 API(앱의 코드 구성)를 호출하는 모바일 앱을 빌드하는 방법 알아보기
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
ms.openlocfilehash: fc25f13d0b0b8a264dcd47a5fdebb0533e93fb55
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77132497"
---
# <a name="configure-a-mobile-app-that-calls-web-apis"></a>웹 API를 호출하는 모바일 앱 구성

응용 프로그램을 만든 후에는 앱 등록 매개 변수를 사용하여 코드를 구성하는 방법을 배웁니다. 모바일 응용 프로그램은 생성 프레임워크에 맞추는 데 관련된 몇 가지 복잡성을 제시합니다.

## <a name="find-msal-support-for-mobile-apps"></a>모바일 앱에 대한 MSAL 지원 찾기

다음 MSAL(Microsoft 인증 라이브러리) 형식은 모바일 앱을 지원합니다.

MSAL | 설명
------------ | ----------
![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | 휴대용 응용 프로그램을 개발하는 데 사용됩니다. MSAL.NET 모바일 응용 프로그램을 구축하기위한 다음 플랫폼을 지원합니다 : 유니버설 윈도우 플랫폼 (UWP), Xamarin.iOS, 그리고 Xamarin.Android.
![MSAL.iOS](media/sample-v2-code/logo_iOS.png) <br/> MSAL.iOS | Objective-C 또는 Swift를 사용하여 네이티브 iOS 응용 프로그램을 개발하는 데 사용됩니다.
![MSAL.Android](media/sample-v2-code/logo_android.png) <br/> MSAL.Android | 안드로이드에 대한 자바에서 네이티브 안드로이드 응용 프로그램을 개발하는 데 사용됩니다.

## <a name="instantiate-the-application"></a>응용 프로그램 인스턴스화

### <a name="android"></a>Android

모바일 응용 프로그램은 `PublicClientApplication` 클래스를 사용합니다. 인스턴스화하는 방법은 다음과 같습니다.

```Java
PublicClientApplication sampleApp = new PublicClientApplication(
                    this.getApplicationContext(),
                    R.raw.auth_config);
```

### <a name="ios"></a>iOS

iOS의 모바일 응용 프로그램은 클래스를 `MSALPublicClientApplication` 인스턴스화해야 합니다. 클래스를 인스턴스화하려면 다음 코드를 사용합니다. 

```objc
NSError *msalError = nil;
     
MSALPublicClientApplicationConfig *config = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"];    
MSALPublicClientApplication *application = [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&msalError];
```

```swift
let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>")
if let application = try? MSALPublicClientApplication(configuration: config){ /* Use application */}
```

[추가 MSALPublicClientApplicationConfig 속성은](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALPublicClientApplicationConfig.html#/Configuration%20options) 기본 권한을 재정의하거나, 리디렉션 URI를 지정하거나, MSAL 토큰 캐싱의 동작을 변경할 수 있습니다. 

### <a name="xamarin-or-uwp"></a>자마린 또는 UWP

이 섹션에서는 Xamarin.iOS, Xamarin.Android 및 UWP 앱에 대한 응용 프로그램을 인스턴스화하는 방법에 대해 설명합니다.

#### <a name="instantiate-the-application"></a>응용 프로그램 인스턴스화

Xamarin 또는 UWP에서 응용 프로그램을 인스턴스화하는 가장 간단한 방법은 다음 코드를 사용하는 것입니다. 이 `ClientId` 코드에서 등록된 앱의 GUID입니다.

```csharp
var app = PublicClientApplicationBuilder.Create(clientId)
                                        .Build();
```

추가 `With<Parameter>` 메서드는 UI 부모를 설정하고, 기본 권한을 재정의하고, 원격 분석을 위한 클라이언트 이름과 버전을 지정하고, 리디렉션 URI를 지정하고, 사용할 HTTP 팩터리를 지정합니다. 예를 들어 HTTP 팩터리는 프록시를 처리하고 원격 분석 및 로깅을 지정하는 데 사용될 수 있습니다. 

다음 섹션에서는 응용 프로그램 인스턴스화에 대한 자세한 정보를 제공합니다.

##### <a name="specify-the-parent-ui-window-or-activity"></a>상위 UI, 창 또는 활동 지정

Android에서는 대화형 인증을 수행하기 전에 상위 활동을 전달해야 합니다. iOS에서 브로커를 사용할 때는 에 전달해야 `ViewController`합니다. UWP에서도 마찬가지로 부모 창을 전달할 수 있습니다. 토큰을 획득하면 전달합니다. 그러나 앱을 만들 때 콜백을 반환하는 `UIParent`대리자로 지정할 수도 있습니다.

```csharp
IPublicClientApplication application = PublicClientApplicationBuilder.Create(clientId)
  .ParentActivityOrWindowFunc(() => parentUi)
  .Build();
```

Android에서는 을 사용하는 [`CurrentActivityPlugin`](https://github.com/jamesmontemagno/CurrentActivityPlugin)것이 좋습니다. 결과 `PublicClientApplication` 빌더 코드는 다음과 같습니다.

```csharp
// Requires MSAL.NET 4.2 or above
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => CrossCurrentActivity.Current)
  .Build();
```

##### <a name="find-more-app-building-parameters"></a>앱 구축 매개 변수 찾기

에서 사용할 수 `PublicClientApplicationBuilder`있는 모든 메서드 목록은 메서드 [목록을](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationbuilder#methods)참조하십시오.

에 노출되는 `PublicClientApplicationOptions`모든 옵션에 대한 설명은 [참조 설명서를](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationoptions)참조하십시오.

## <a name="tasks-for-xamarin-ios"></a>자마린 iOS용 작업

Xamarin iOS에서 MSAL.NET 사용하는 경우 다음 작업을 수행합니다.

* [에서 함수를 `OpenUrl` 재정의하고 구현합니다.`AppDelegate`](msal-net-xamarin-ios-considerations.md#implement-openurl)
* [키체인 그룹 사용](msal-net-xamarin-ios-considerations.md#enable-keychain-access)
* [토큰 캐시 공유 사용](msal-net-xamarin-ios-considerations.md#enable-token-cache-sharing-across-ios-applications)
* [키체인 액세스 활성화](msal-net-xamarin-ios-considerations.md#enable-keychain-access)

자세한 내용은 [Xamarin iOS 고려 사항을](msal-net-xamarin-ios-considerations.md)참조하십시오.

## <a name="tasks-for-msal-for-ios-and-macos"></a>iOS 및 macOS용 MSAL 작업

이러한 작업은 iOS 및 macOS용 MSAL을 사용할 때 필요합니다.

* [`openURL` 콜백 구현](#brokered-authentication-for-msal-for-ios-and-macos)
* [키체인 액세스 그룹 사용](howto-v2-keychain-objc.md)
* [브라우저 및 WebViews 사용자 지정](customize-webviews.md)

## <a name="tasks-for-xamarinandroid"></a>자마린.안드로이드에 대한 작업

Xamarin.Android를 사용하는 경우 다음 작업을 수행합니다.

- [인증 흐름의 대화형 부분이 끝난 후 제어가 MSAL로 돌아가도록 보장](msal-net-xamarin-android-considerations.md#ensure-that-control-returns-to-msal)
- [안드로이드 매니페스트 업데이트](msal-net-xamarin-android-considerations.md#update-the-android-manifest)
- [포함된 웹 보기 사용(선택 사항)](msal-net-xamarin-android-considerations.md#use-the-embedded-web-view-optional)
- [필요에 따라 문제 해결](msal-net-xamarin-android-considerations.md#troubleshoot)

자세한 내용은 [Xamarin.Android 고려 사항을](msal-net-xamarin-android-considerations.md)참조하십시오.

안드로이드의 브라우저에 대한 고려 사항은 [MSAL.NET Xamarin.Android 관련 고려 사항을](msal-net-system-browser-android-considerations.md)참조하십시오.

#### <a name="tasks-for-uwp"></a>UWP에 대한 작업

UWP에서 회사 네트워크를 사용할 수 있습니다. 다음 섹션에서는 회사 시나리오에서 완료해야 하는 작업을 설명합니다.

자세한 내용은 [MSAL.NET UWP 관련 고려 사항을](msal-net-uwp-considerations.md)참조하십시오.

## <a name="configure-the-application-to-use-the-broker"></a>브로커를 사용하도록 응용 프로그램 구성

Android 및 iOS에서는 브로커가 다음을 사용하도록 설정합니다.

- **단일 사인온(SSO)**: Azure Active Directory(Azure AD)에 등록된 장치에 SSO를 사용할 수 있습니다. SSO를 사용하는 경우 사용자는 각 응용 프로그램에 로그인할 필요가 없습니다.
- **장치 식별**: 이 설정을 사용하면 Azure AD 장치와 관련된 조건부 액세스 정책을 사용할 수 있습니다. 인증 프로세스는 장치가 업무 직장에 조인될 때 생성된 장치 인증서를 사용합니다.
- **응용 프로그램 식별 확인**: 응용 프로그램이 브로커를 호출하면 리디렉션 URL을 전달합니다. 그런 다음 브로커가 이를 확인합니다.

### <a name="enable-the-broker-on-xamarin"></a>자마린에서 브로커 활성화

Xamarin에서 브로커를 사용하려면 `WithBroker()` 메서드를 호출할 `PublicClientApplicationBuilder.CreateApplication` 때 매개 변수를 사용합니다. 기본적으로 true로 `.WithBroker()` 설정됩니다. 

Xamarin.iOS에 대한 중개 인증을 사용하려면 이 문서의 [Xamarin.iOS 섹션의](#enable-brokered-authentication-for-xamarin-ios) 단계를 따르십시오.

### <a name="enable-the-broker-for-msal-for-android"></a>안드로이드에 대한 MSAL에 대한 브로커를 사용

Android에서 브로커를 사용하도록 설정하는 방법에 대한 자세한 내용은 [Android에서 중개 인증을](brokered-auth.md)참조하십시오. 

### <a name="enable-the-broker-for-msal-for-ios-and-macos"></a>iOS 및 macOS용 MSAL용 브로커 사용

중개된 인증은 iOS 및 macOS용 MSAL의 Azure AD 시나리오에 대해 기본적으로 활성화됩니다. 

다음 섹션에서는 Xamarin.iOS용 MSAL 또는 iOS 및 macOS용 MSAL에 대한 중개 인증 지원을 위한 응용 프로그램을 구성하는 지침을 제공합니다. 두 가지 지침 집합에서 일부 단계가 다릅니다.

### <a name="enable-brokered-authentication-for-xamarin-ios"></a>Xamarin iOS용 중개 인증 사용

Xamarin.iOS 앱이 [Microsoft 인증자](https://itunes.apple.com/us/app/microsoft-authenticator/id983156458) 앱과 대화할 수 있도록 하려면 이 섹션의 단계를 따릅니다.

#### <a name="step-1-enable-broker-support"></a>1단계: 브로커 지원 활성화

브로커 지원은 기본적으로 비활성화됩니다. 개별 `PublicClientApplication` 클래스에 대해 사용하도록 설정합니다. 을 `WithBroker()` 통해 `PublicClientApplicationBuilder`클래스를 `PublicClientApplication` 만들 때 매개 변수를 사용합니다. 매개 `WithBroker()` 변수는 기본적으로 true로 설정됩니다.

```csharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos) // $"msauth.{Bundle.Id}://auth" (see step 6 below)
                .Build();
```

#### <a name="step-2-update-appdelegate-to-handle-the-callback"></a>2단계: 콜백을 처리하도록 AppDelegate 업데이트

MSAL.NET 브로커를 호출하면 브로커는 응용 프로그램에 다시 호출합니다. 메서드를 사용 하 `AppDelegate.OpenUrl` 여 다시 호출 합니다. MSAL은 브로커의 응답을 기다리기 때문에 응용 프로그램이 MSAL.NET 다시 호출하기 위해 협력해야 합니다. 다음 코드에서 와 같이 `AppDelegate.cs` 메서드를 재정의하도록 파일을 업데이트하여 이 동작을 설정합니다.

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

이 메서드는 응용 프로그램을 시작할 때마다 호출됩니다. 브로커의 응답을 처리하고 MSAL.NET 시작된 인증 프로세스를 완료할 수 있는 기회입니다.

#### <a name="step-3-set-a-uiviewcontroller"></a>3단계: UIViewController() 설정

Xamarin iOS의 경우 일반적으로 개체 창을 설정할 필요가 없습니다. 그러나이 경우 브로커로부터 응답을 보내고받을 수 있도록 설정해야합니다. 개체 창을 설정하려면 `AppDelegate.cs`에서 를 `ViewController`설정합니다.

개체 창을 설정하려면 다음 단계를 따르십시오.

1. 에서 `AppDelegate.cs`새 `UIViewController()` `App.RootViewController` 로 설정합니다. 이 설정을 사용하면 브로커에 대한 `UIViewController`호출에 이가 포함됩니다. 올바르게 설정되지 않으면 다음과 같은 오류가 발생할 수 있습니다.

    `"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker."`

1. `AcquireTokenInteractive` 통화에서 을 사용합니다. `.WithParentActivityOrWindow(App.RootViewController)` 사용할 개체 창에 대한 참조를 전달합니다. 예를 들면 다음과 같습니다.

    `App.cs`의 경우:
    ```csharp
       public static object RootViewController { get; set; }
    ```
    `AppDelegate.cs`의 경우:
    ```csharp
       LoadApplication(new App());
       App.RootViewController = new UIViewController();
    ```
    `AcquireToken` 통화에서:
    ```csharp
    result = await app.AcquireTokenInteractive(scopes)
                 .WithParentActivityOrWindow(App.RootViewController)
                 .ExecuteAsync();
    ```
    
#### <a name="step-4-register-a-url-scheme"></a>4단계: URL 구성표 등록

MSAL.NET URL을 사용하여 브로커를 호출한 다음 브로커 응답을 앱으로 다시 반환합니다. 왕복을 완료하려면 파일에 앱의 URL 체계를 `Info.plist` 등록합니다. 

앱의 URL 구성표를 등록하려면 다음 단계를 따르세요.

1. 의 `CFBundleURLSchemes` 접두사 `msauth`. 
1. 끝에 `CFBundleURLName` 추가합니다. 이 패턴을 따르십시오. 

   `$"msauth.(BundleId)"`

   여기에서 `BundleId` 장치를 고유하게 식별합니다. 예를 `BundleId` `yourcompany.xforms`들어, 있는 경우 , `msauth.com.yourcompany.xforms`URL 구성표는 입니다.
    
   > [!NOTE]
   > 이 URL 체계는 브로커의 응답을 받을 때 앱을 고유하게 식별하는 리디렉션 URI의 일부가 됩니다.
    
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
    
#### <a name="step-5-add-to-the-lsapplicationqueriesschemes-section"></a>5 단계: LS응용 프로그램 쿼리스키스 섹션에 추가

MSAL은 `–canOpenURL:` 브로커가 장치에 설치되어 있는지 확인하는 데 사용합니다. iOS 9에서 Apple은 응용 프로그램에서 쿼리할 수 있는 체계를 잠갔습니다.

다음 `msauthv2` 코드 `LSApplicationQueriesSchemes` 예제와 `Info.plist` 같이 파일 섹션에 추가합니다.

```XML 
<key>LSApplicationQueriesSchemes</key>
    <array>
      <string>msauthv2</string>
    </array>
```

### <a name="brokered-authentication-for-msal-for-ios-and-macos"></a>iOS 및 macOS용 MSAL용 인증 중개

Azure AD 시나리오에 대해 중개된 인증은 기본적으로 활성화됩니다.

#### <a name="step-1-update-appdelegate-to-handle-the-callback"></a>1단계: 콜백을 처리하도록 AppDelegate 업데이트

iOS 및 macOS용 MSAL이 브로커를 호출하면 브로커는 메서드를 `openURL` 사용하여 응용 프로그램에 다시 호출합니다. MSAL은 브로커의 응답을 기다리기 때문에 응용 프로그램이 MSAL을 다시 호출하기 위해 협력해야 합니다. 다음 코드 예제에서 와 `AppDelegate.m` 같이 메서드를 재정의하도록 파일을 업데이트하여 이 기능을 설정합니다.

```objc
- (BOOL)application:(UIApplication *)app
            openURL:(NSURL *)url
            options:(NSDictionary<UIApplicationOpenURLOptionsKey,id> *)options
{
    return [MSALPublicClientApplication handleMSALResponse:url 
                                         sourceApplication:options[UIApplicationOpenURLOptionsSourceApplicationKey]];
}
```

```swift
    func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
        
        guard let sourceApplication = options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String else {
            return false
        }
        
        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: sourceApplication)
    }
```

> [!NOTE]
> iOS `UISceneDelegate` 13 이상에서 채택한 경우 MSAL 콜백을 `scene:openURLContexts:` `UISceneDelegate` 대신에 배치합니다. MSAL은 `handleMSALResponse:sourceApplication:` 각 URL에 대해 한 번만 호출해야 합니다.
>
> 자세한 내용은 Apple [설명서를](https://developer.apple.com/documentation/uikit/uiscenedelegate/3238059-scene?language=objc)참조하십시오.

#### <a name="step-2-register-a-url-scheme"></a>2단계: URL 구성표 등록

iOS 및 macOS용 MSAL은 URL을 사용하여 브로커를 호출한 다음 브로커 응답을 앱에 반환합니다. 왕복을 완료하려면 `Info.plist` 파일에 앱에 대한 URL 구성표를 등록합니다.

앱에 대한 스키마를 등록하려면 다음 을 수행합니다. 

1. 사용자 지정 URL 구성표를 `msauth`으로 접두사합니다. 

1. 구성표 끝에 번들 식별자를 추가합니다. 이 패턴을 따르십시오. 

   `$"msauth.(BundleId)"`

   여기에서 `BundleId` 장치를 고유하게 식별합니다. 예를 `BundleId` `yourcompany.xforms`들어, 있는 경우 , `msauth.com.yourcompany.xforms`URL 구성표는 입니다.
  
   > [!NOTE]
   > 이 URL 체계는 브로커의 응답을 받을 때 앱을 고유하게 식별하는 리디렉션 URI의 일부가 됩니다. 형식의 `msauth.(BundleId)://auth` 리디렉션 URI가 [Azure 포털의](https://portal.azure.com)응용 프로그램에 등록되어 있는지 확인합니다.
  
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

#### <a name="step-3-add-lsapplicationqueriesschemes"></a>3 단계: LS응용 프로그램 쿼리 체계 추가

Microsoft `LSApplicationQueriesSchemes` 인증자 앱이 설치된 경우 호출을 허용하도록 추가합니다.

> [!NOTE]
> `msauthv3` 이 구성표는 Xcode 11 이상을 사용하여 앱을 컴파일할 때 필요합니다. 

추가하는 `LSApplicationQueriesSchemes`방법의 예는 다음과 같습니다.

```XML 
<key>LSApplicationQueriesSchemes</key>
<array>
  <string>msauthv2</string>
  <string>msauthv3</string>
</array>
```

### <a name="brokered-authentication-for-xamarinandroid"></a>Xamarin.Android에 대한 중개 인증

MSAL.NET 안드로이드에 대한 브로커를 지원하지 않습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [토큰 획득](scenario-mobile-acquire-token.md)
