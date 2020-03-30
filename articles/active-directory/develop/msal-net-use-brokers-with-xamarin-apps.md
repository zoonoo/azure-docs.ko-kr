---
title: 안드로이드에 & 자마린 아이폰 OS와 브로커를 사용 | Azure
titleSuffix: Microsoft identity platform
description: .NET(MSAL.NET)에 대해 Microsoft 인증자 및 Microsoft 인증 라이브러리를 사용할 수 있는 Xamarin iOS 응용 프로그램을 설정하는 방법에 대해 알아봅니다. 또한 .NET(ADAL.NET)에 대한 Azure AD 인증 라이브러리에서 .NET(MSAL.NET)에 대한 Microsoft 인증 라이브러리로 마이그레이션하는 방법도 알아봅니다.
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
ms.openlocfilehash: 1a57173311278c5e3e0304aeb12d4d6999379eb5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262790"
---
# <a name="use-microsoft-authenticator-or-intune-company-portal-on-xamarin-applications"></a>Xamarin 응용 프로그램에서 Microsoft 인증자 또는 인튠 회사 포털 사용

안드로이드와 iOS에서, 마이크로 소프트 인증기와 안드로이드 특정 마이크로 소프트 인튠 회사 포털과 같은 브로커는 사용할 수 있습니다 :

- **단일 사인온(SSO)**: 사용자는 각 응용 프로그램에 로그인할 필요가 없습니다.
- **장치 식별**: 브로커가 장치 인증서에 액세스합니다. 이 인증서는 업무 직장에 가입할 때 장치에서 만들어집니다.
- **응용 프로그램 식별 확인**: 응용 프로그램이 브로커를 호출하면 리디렉션 URL을 전달합니다. 브로커는 URL을 확인합니다.

이러한 기능 중 하나를 사용하려면 메서드를 `WithBroker()` `PublicClientApplicationBuilder.CreateApplication` 호출할 때 매개 변수를 사용합니다. 매개 `.WithBroker()` 변수는 기본적으로 true로 설정됩니다. 

또한 다음 섹션의 지침을 사용하여 [iOS](#brokered-authentication-for-ios) 응용 프로그램 또는 [Android](#brokered-authentication-for-android) 응용 프로그램에 대해 중개된 인증을 설정합니다.

## <a name="brokered-authentication-for-ios"></a>iOS용 중개 인증

다음 단계를 사용하여 Xamarin.iOS 앱이 Microsoft [인증자](https://itunes.apple.com/us/app/microsoft-authenticator/id983156458) 앱과 대화할 수 있도록 합니다.

### <a name="step-1-enable-broker-support"></a>1단계: 브로커 지원 활성화
`PublicClientApplication`의 개별 인스턴스에 대해 브로커 지원을 사용하도록 설정해야 합니다. 기본적으로 지원이 비활성화됩니다. 을 통해 `PublicClientApplication` 만들 때 `WithBroker()` 다음 예제와 같이 매개 변수를 사용합니다. `PublicClientApplicationBuilder` 매개 `WithBroker()` 변수는 기본적으로 true로 설정됩니다.

```csharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos) // $"msauth.{Bundle.Id}://auth" (see step 6 below)
                .Build();
```

### <a name="step-2-enable-keychain-access"></a>2단계: 키체인 액세스 활성화

키체인 액세스를 사용하려면 응용 프로그램에 대한 키체인 액세스 그룹이 있어야 합니다. API를 `WithIosKeychainSecurityGroup()` 사용하여 응용 프로그램을 만들 때 키체인 액세스 그룹을 설정할 수 있습니다.

```csharp
var builder = PublicClientApplicationBuilder
     .Create(ClientId)
      
     .WithIosKeychainSecurityGroup("com.microsoft.adalcache")
     .Build();
```

자세한 내용은 [키체인 액세스 활성화](msal-net-xamarin-ios-considerations.md#enable-keychain-access)를 참조하십시오.

### <a name="step-3-update-appdelegate-to-handle-the-callback"></a>3단계: 콜백을 처리하도록 AppDelegate 업데이트
.NET(MSAL.NET)에 대한 Microsoft 인증 라이브러리가 브로커를 호출하면 브로커는 `OpenUrl` `AppDelegate` 클래스의 메서드를 통해 응용 프로그램을 다시 호출합니다. MSAL은 브로커의 응답을 기다리기 때문에 응용 프로그램이 MSAL.NET 다시 호출하기 위해 협력해야 합니다. 이 협력을 사용하려면 `AppDelegate.cs` 파일을 업데이트하여 다음 방법을 재정의합니다.

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

이 메서드는 응용 프로그램을 시작할 때마다 호출됩니다. 브로커의 응답을 처리하고 MSAL.NET 시작한 인증 프로세스를 완료하는 기회로 사용됩니다.

### <a name="step-4-set-uiviewcontroller"></a>4단계: UIViewController() 설정
여전히 `AppDelegate.cs` 파일에 개체 창을 설정 해야 합니다. 일반적으로 Xamarin iOS의 경우 개체 창을 설정할 필요가 없습니다. 그러나 브로커로부터 응답을 보내고 받을 개체 창이 필요합니다. 

개체 창을 설정하려면 다음을 수행합니다. 
1. 파일에서 새 `UIViewController()`로 설정합니다. `App.RootViewController` `AppDelegate.cs` 이 할당은 브로커에 대한 호출에 를 포함하도록 합니다. `UIViewController` 이 설정이 잘못 할당된 경우 다음과 같은 오류가 발생할 수 있습니다.

      `"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`

1. 호출시 `AcquireTokenInteractive` 사용할 `.WithParentActivityOrWindow(App.RootViewController)` 개체 창에 대한 참조를 사용한 다음 전달합니다.

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

### <a name="step-5-register-a-url-scheme"></a>5단계: URL 구성표 등록
MSAL.NET URL을 사용하여 브로커를 호출한 다음 브로커 응답을 앱에 반환합니다. 왕복을 완료하려면 `Info.plist` 파일에 앱에 대한 URL 구성표를 등록합니다.

`CFBundleURLSchemes` 이름은 접두사로 포함되어야 `msauth.` 합니다. 의 접두사를 `CFBundleURLName`따르십시오. 

URL 구성표에서 `BundleId` 앱을 고유하게 `$"msauth.(BundleId)"`식별합니다. 따라서 `BundleId` 있는 `com.yourcompany.xforms`경우 URL 체계는 `msauth.com.yourcompany.xforms`.

> [!NOTE]
> 이 URL 체계는 브로커로부터 응답을 받을 때 앱을 고유하게 식별하는 리디렉션 URI의 일부가 됩니다.

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

### <a name="step-6-add-the-broker-identifier-to-the-lsapplicationqueriesschemes-section"></a>6 단계: LSApplicationQuerySchemes 섹션에 브로커 식별자를 추가합니다.

MSAL은 `–canOpenURL:` 브로커가 장치에 설치되어 있는지 여부를 확인하는 데 사용합니다. iOS 9에서 Apple은 응용 프로그램에서 쿼리할 수 있는 체계를 잠갔습니다. 

다음 `msauthv2` 예제와 `LSApplicationQueriesSchemes` 같이 `Info.plist` 파일 섹션에 추가합니다.

```XML
<key>LSApplicationQueriesSchemes</key>
    <array>
      <string>msauthv2</string>
      <string>msauthv3</string>
    </array>
```

### <a name="step-7-register-your-redirect-uri-in-the-application-portal"></a>7단계: 응용 프로그램 포털에서 리디렉션 URI 등록

브로커를 사용하는 경우 리디렉션 URI에는 추가 요구 사항이 있습니다. 리디렉션 URI에는 다음 형식이 _있어야 합니다._

```csharp
$"msauth.{BundleId}://auth"
```

예를 들면 다음과 같습니다.

```csharp
public static string redirectUriOnIos = "msauth.com.yourcompany.XForms://auth"; 
```

리디렉션 URI가 파일에 포함된 `CFBundleURLSchemes` 이름과 일치합니다. `Info.plist`

### <a name="step-8-make-sure-the-redirect-uri-is-registered-with-your-app"></a>8단계: 리디렉션 URI가 앱에 등록되어 있는지 확인

리디렉션 URI는 응용 프로그램에 대 한 유효한 리디렉션 URI로 [응용 프로그램 등록 포털에](https://portal.azure.com) 등록 해야 합니다. 

앱 등록 포털은 번들 ID에서 중개된 회신 URI를 계산하는 데 도움이 되는 새로운 환경을 제공합니다. 

리디렉션 URI를 계산하려면 다음을 수행합니다.

1. 앱 등록 포털에서 **인증을** > **선택하여 새 환경을 사용해 보십시오.**

   ![새로운 앱 등록 환경을 사용해 보십시오.](media/msal-net-use-brokers-with-xamarin-apps/60799285-2d031b00-a173-11e9-9d28-ac07a7ae894a.png)

1. **플랫폼 추가를 선택합니다.**

   ![플랫폼 추가](media/msal-net-use-brokers-with-xamarin-apps/60799366-4c01ad00-a173-11e9-934f-f02e26c9429e.png)

1. 플랫폼 목록이 지원되면 **iOS**를 선택합니다.

   ![iOS 구성](media/msal-net-use-brokers-with-xamarin-apps/60799411-60de4080-a173-11e9-9dcc-d39a45826d42.png)

1. 요청에 따라 번들 ID를 입력한 다음 **구성을**선택합니다.

   ![번들 ID 입력](media/msal-net-use-brokers-with-xamarin-apps/60799477-7eaba580-a173-11e9-9f8b-431f5b09344e.png)

단계를 완료하면 리디렉션 URI가 계산됩니다.

![리디렉션 URI 복사](media/msal-net-use-brokers-with-xamarin-apps/60799538-9e42ce00-a173-11e9-860a-015a1840fd19.png)

## <a name="brokered-authentication-for-android"></a>안드로이드에 대한 중개 인증

### <a name="step-1-enable-broker-support"></a>1단계: 브로커 지원 활성화

브로커 지원은 PublicClientApplication별로 활성화됩니다. 기본적으로 사용하지 않도록 설정되어 있습니다. 을 `WithBroker()` 통해를 만들 때 매개 변수(기본적으로 `PublicClientApplicationBuilder`true로 설정)를 `IPublicClientApplication` 사용합니다.

```CSharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithRedirectUri(redirectUriOnAndroid) //(see step 4 below)
                .Build();
```

### <a name="step-2-update-appdelegate-to-handle-the-callback"></a>2단계: 콜백을 처리하도록 AppDelegate 업데이트

MSAL.NET 브로커를 호출하면 브로커는 OnActivityResult() 메서드를 사용하여 응용 프로그램에 다시 호출합니다. MSAL은 브로커의 응답을 기다리므로 응용 프로그램에서 결과를 MSAL.NET 라우팅해야 합니다.
이는 아래와 같이 OnActivityResult() 메서드를 `SetAuthenticationContinuationEventArgs(int requestCode, Result resultCode, Intent data)` 재정의하여 결과를 라우팅하여 수행할 수 있습니다.

```CSharp
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
   base.OnActivityResult(requestCode, resultCode, data);
   AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(requestCode, resultCode, data);
}
```

이 메서드는 브로커 응용 프로그램이 시작될 때마다 호출되며 브로커의 응답을 처리하고 MSAL.NET 시작한 인증 프로세스를 완료하는 기회로 사용됩니다.

### <a name="step-3-set-an-activity"></a>3단계: 활동 설정

중개된 인증이 작동하려면 MSAL이 브로커로부터 응답을 보내고 받을 수 있도록 활동을 설정해야 합니다.

이렇게 하려면 활동(일반적으로 MainActivity)을 부모 `WithParentActivityOrWindow(object parent)` 개체로 제공해야 합니다. 

**예를 들어:**

토큰 획득 호출에서:

```CSharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow((Activity)context))
             .ExecuteAsync();
```

### <a name="step-4-register-your-redirecturi-in-the-application-portal"></a>4단계: 응용 프로그램 포털에서 리디렉션Uri 등록

MSAL은 URL을 사용하여 브로커를 호출한 다음 앱으로 돌아갑니다. 이 왕복 여행을 완료하려면 앱에 대한 URL 스키마를 등록해야 합니다. 이 리디렉션 URI는 응용 프로그램에 대 한 유효한 리디렉션 URI로 Azure AD 응용 프로그램 등록 포털에 등록 해야 합니다.


응용 프로그램에 필요한 리디렉션 URI는 APK에 서명하는 데 사용되는 인증서에 따라 다릅니다.

```
Example: msauth://com.microsoft.xforms.testApp/hgbUYHVBYUTvuvT&Y6tr554365466=
```

URI의 `hgbUYHVBYUTvuvT&Y6tr554365466=`마지막 부분은 APK가 서명된 서명이며 base64가 인코딩됩니다.
그러나 Visual Studio를 사용하는 응용 프로그램의 개발 단계에서 특정 인증서로 APK에 서명하지 않고 코드를 디버깅하는 경우 Visual Studio는 디버깅을 위해 APK에 APK에 고유한 서명을 제공합니다. 내장된 기계입니다. 따라서 다른 컴퓨터에서 앱을 빌드할 때마다 MSAL을 인증하려면 응용 프로그램의 코드에서 리디렉션 URI와 Azure 포털에서 응용 프로그램의 등록을 업데이트해야 합니다. 

디버깅하는 동안 제공된 리디렉션 URI가 올바르지 않는다는 MSAL 예외(또는 로그 메시지)가 발생할 수 있습니다. 이 예외는 또한 디버깅 중인 현재 컴퓨터와 함께 **사용해야 하는 리디렉션 URI를 제공합니다.** 이 리디렉션 URI를 사용하여 당분간 개발을 계속할 수 있습니다.

코드를 완료할 준비가 되면 코드와 Azure Portal에서 응용 프로그램 등록시 APK에 서명할 인증서의 서명을 사용하도록 리디렉션 URI를 업데이트해야 합니다.

실제로 이는 팀의 각 구성원에 대해 리디렉션 URI와 APK의 프로덕션 서명 버전에 대한 리디렉션 URI를 등록해야 한다는 것을 의미합니다.

MSAL이 수행하는 방식과 유사하게 이 서명을 직접 계산할 수도 있습니다. 

```CSharp
   private string GetRedirectUriForBroker()
   {
      string packageName = Application.Context.PackageName;
      string signatureDigest = this.GetCurrentSignatureForPackage(packageName);
      if (!string.IsNullOrEmpty(signatureDigest))
      {
            return string.Format(CultureInfo.InvariantCulture, "{0}://{1}/{2}", RedirectUriScheme,
               packageName.ToLowerInvariant(), signatureDigest);
      }

      return string.Empty;
   }

   private string GetCurrentSignatureForPackage(string packageName)
   {
            PackageInfo info = Application.Context.PackageManager.GetPackageInfo(packageName,
               PackageInfoFlags.Signatures);
            if (info != null && info.Signatures != null && info.Signatures.Count > 0)
            {
               // First available signature. Applications can be signed with multiple signatures.
               // The order of Signatures is not guaranteed.
               Signature signature = info.Signatures[0];
               MessageDigest md = MessageDigest.GetInstance("SHA");
               md.Update(signature.ToByteArray());
               return Convert.ToBase64String(md.Digest(), Base64FormattingOptions.None);
               // Server side needs to register all other tags. ADAL will
               // send one of them.
            }
   }
```

다음 명령과 함께 keytool을 사용하여 패키지의 서명을 획득할 수도 있습니다.

Windows의 경우: `keytool.exe -list -v -keystore "%LocalAppData%\Xamarin\Mono for Android\debug.keystore" -alias androiddebugkey -storepass android -keypass android`

Mac의 경우:`keytool -exportcert -alias androiddebugkey -keystore ~/.android/debug.keystore | openssl sha1 -binary | openssl base64`

## <a name="next-steps"></a>다음 단계

MSAL.NET [함께 유니버설 Windows 플랫폼을 사용하는 방법에 대한 고려 사항에](msal-net-uwp-considerations.md)대해 알아봅니다.
