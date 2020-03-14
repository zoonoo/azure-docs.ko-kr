---
title: Android & Xamarin iOS에서 broker 사용 | Microsoft
titleSuffix: Microsoft identity platform
description: Microsoft Authenticator 및 .NET 용 Microsoft Authentication Library (MSAL.NET)를 사용할 수 있는 Xamarin iOS 응용 프로그램을 설정 하는 방법에 대해 알아봅니다. 또한 .NET 용 Azure AD 인증 Library (ADAL.NET)에서 MSAL.NET (Microsoft Authentication Library for .NET)로 마이그레이션하는 방법에 대해 알아봅니다.
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
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79262790"
---
# <a name="use-microsoft-authenticator-or-intune-company-portal-on-xamarin-applications"></a>Xamarin 응용 프로그램에서 Microsoft Authenticator 또는 Intune 회사 포털 사용

Android 및 iOS에서 Microsoft Authenticator와 같은 broker와 Android 관련 Microsoft Intune 회사 포털 사용 하도록 설정 합니다.

- **Sso (Single sign-on)** : 사용자가 각 응용 프로그램에 로그인 할 필요가 없습니다.
- **장치 식별**: broker는 장치 인증서에 액세스 합니다. 이 인증서는 작업 공간에 조인 될 때 장치에 생성 됩니다.
- **응용 프로그램 id 확인**: 응용 프로그램에서 broker를 호출할 때 해당 리디렉션 URL을 전달 합니다. Broker에서 URL을 확인 합니다.

이러한 기능 중 하나를 사용 하도록 설정 하려면 `PublicClientApplicationBuilder.CreateApplication` 메서드를 호출할 때 `WithBroker()` 매개 변수를 사용 합니다. `.WithBroker()` 매개 변수는 기본적으로 true로 설정 됩니다. 

또한 다음 섹션의 지침을 사용 하 여 [iOS](#brokered-authentication-for-ios) 응용 프로그램 또는 [Android](#brokered-authentication-for-android) 응용 프로그램에 대 한 조정 된 인증을 설정 합니다.

## <a name="brokered-authentication-for-ios"></a>IOS에 대 한 조정 된 인증

다음 단계를 사용 하 여 Xamarin.ios 앱이 [Microsoft Authenticator](https://itunes.apple.com/us/app/microsoft-authenticator/id983156458) 앱과 통신할 수 있도록 설정 합니다.

### <a name="step-1-enable-broker-support"></a>1 단계: broker 지원 사용
`PublicClientApplication`의 개별 인스턴스에 대해 broker 지원을 사용 하도록 설정 해야 합니다. 지원 기능은 기본적으로 사용 하지 않도록 설정 되어 있습니다. `PublicClientApplicationBuilder`를 통해 `PublicClientApplication`을 만드는 경우 다음 예제와 같이 `WithBroker()` 매개 변수를 사용 합니다. `WithBroker()` 매개 변수는 기본적으로 true로 설정 됩니다.

```csharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos) // $"msauth.{Bundle.Id}://auth" (see step 6 below)
                .Build();
```

### <a name="step-2-enable-keychain-access"></a>2 단계: 키 집합 액세스 사용

키 집합 액세스를 사용 하도록 설정 하려면 응용 프로그램에 대 한 키 집합 액세스 그룹이 있어야 합니다. 응용 프로그램을 만들 때 `WithIosKeychainSecurityGroup()` API를 사용 하 여 키 집합 액세스 그룹을 설정할 수 있습니다.

```csharp
var builder = PublicClientApplicationBuilder
     .Create(ClientId)
      
     .WithIosKeychainSecurityGroup("com.microsoft.adalcache")
     .Build();
```

자세한 내용은 키 [집합 액세스 사용](msal-net-xamarin-ios-considerations.md#enable-keychain-access)을 참조 하세요.

### <a name="step-3-update-appdelegate-to-handle-the-callback"></a>3 단계: 콜백을 처리 하도록 AppDelegate 업데이트
Microsoft Authentication Library for .NET (MSAL.NET)이 broker를 호출할 때 broker는 `AppDelegate` 클래스의 `OpenUrl` 메서드를 통해 응용 프로그램을 다시 호출 합니다. MSAL이 broker의 응답을 대기 하므로 응용 프로그램은 MSAL.NET를 다시 호출 해야 합니다. 이 협력을 사용 하도록 설정 하려면 `AppDelegate.cs` 파일을 업데이트 하 여 다음 메서드를 재정의 합니다.

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

이 메서드는 응용 프로그램이 시작 될 때마다 호출 됩니다. Broker에서 응답을 처리 하 고 MSAL.NET 시작 된 인증 프로세스를 완료 하는 기회를 사용 합니다.

### <a name="step-4-set-uiviewcontroller"></a>4 단계: UIViewController () 설정
`AppDelegate.cs` 파일에서 개체 창을 설정 해야 합니다. 일반적으로 Xamarin iOS의 경우 개체 창을 설정 하지 않아도 됩니다. 그러나 broker에서 응답을 보내고 받으려면 개체 창이 필요 합니다. 

개체 창을 설정 하려면 다음을 수행 합니다. 
1. `AppDelegate.cs` 파일에서 `App.RootViewController`를 새 `UIViewController()`설정 합니다. 이렇게 할당 하면 broker에 대 한 호출에 `UIViewController`포함 됩니다. 이 설정이 잘못 할당 되 면 다음과 같은 오류가 발생할 수 있습니다.

      `"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`

1. `AcquireTokenInteractive` 호출에서 `.WithParentActivityOrWindow(App.RootViewController)`를 사용 하 여 사용할 개체 창에 대 한 참조를 전달 합니다.

    `App.cs`의 경우:

    ```csharp
       public static object RootViewController { get; set; }
    ```

    `AppDelegate.cs`의 경우:

    ```csharp
       LoadApplication(new App());
       App.RootViewController = new UIViewController();
    ```

    `AcquireToken`에서 다음을 호출 합니다.

    ```csharp
    result = await app.AcquireTokenInteractive(scopes)
                 .WithParentActivityOrWindow(App.RootViewController)
                 .ExecuteAsync();
    ```

### <a name="step-5-register-a-url-scheme"></a>5 단계: URL 구성표 등록
MSAL.NET는 Url을 사용 하 여 broker를 호출한 다음 broker 응답을 앱에 반환 합니다. 왕복을 완료 하려면 `Info.plist` 파일에 앱에 대 한 URL 체계를 등록 합니다.

`CFBundleURLSchemes` 이름은 접두사로 `msauth.`를 포함 해야 합니다. `CFBundleURLName`접두사를 따릅니다. 

URL 체계에서 `BundleId`는 앱 `$"msauth.(BundleId)"`고유 하 게 식별 합니다. 따라서 `BundleId` `com.yourcompany.xforms`경우 URL 체계가 `msauth.com.yourcompany.xforms`됩니다.

> [!NOTE]
> 이 URL 구성표는 broker에서 응답을 받을 때 앱을 고유 하 게 식별 하는 리디렉션 URI의 일부가 됩니다.

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

### <a name="step-6-add-the-broker-identifier-to-the-lsapplicationqueriesschemes-section"></a>6 단계: LSApplicationQueriesSchemes 섹션에 broker 식별자 추가

MSAL은 `–canOpenURL:`를 사용 하 여 broker가 장치에 설치 되어 있는지 여부를 확인 합니다. IOS 9에서 Apple은 응용 프로그램에서 쿼리할 수 있는 스키마를 잠갔습니다. 

다음 예제와 같이 `Info.plist` 파일의 `LSApplicationQueriesSchemes` 섹션에 `msauthv2`를 추가 합니다.

```XML
<key>LSApplicationQueriesSchemes</key>
    <array>
      <string>msauthv2</string>
      <string>msauthv3</string>
    </array>
```

### <a name="step-7-register-your-redirect-uri-in-the-application-portal"></a>7 단계: 응용 프로그램 포털에서 리디렉션 URI 등록

Broker를 사용 하는 경우 리디렉션 URI에는 추가 요구 사항이 있습니다. 리디렉션 URI의 형식은 다음과 _같아야 합니다_ .

```csharp
$"msauth.{BundleId}://auth"
```

예를 들면 다음과 같습니다.

```csharp
public static string redirectUriOnIos = "msauth.com.yourcompany.XForms://auth"; 
```

리디렉션 URI가 `Info.plist` 파일에 포함 된 `CFBundleURLSchemes` 이름과 일치 하는지 확인 합니다.

### <a name="step-8-make-sure-the-redirect-uri-is-registered-with-your-app"></a>8 단계: 리디렉션 URI가 앱에 등록 되었는지 확인

응용 프로그램에 대 한 유효한 리디렉션 URI로 리디렉션 URI를 [앱 등록 포털](https://portal.azure.com) 에 등록 해야 합니다. 

앱 등록 포털은 번들 ID에서 조정 된 회신 URI를 계산 하는 데 도움이 되는 새로운 환경을 제공 합니다. 

리디렉션 URI를 계산 하려면:

1. 앱 등록 포털에서 **인증** > 선택 하 **여 새로운 환경을 사용해 보세요**.

   ![새 앱 등록 환경 사용해 보기](media/msal-net-use-brokers-with-xamarin-apps/60799285-2d031b00-a173-11e9-9d28-ac07a7ae894a.png)

1. **플랫폼 추가를**선택 합니다.

   ![플랫폼 추가](media/msal-net-use-brokers-with-xamarin-apps/60799366-4c01ad00-a173-11e9-934f-f02e26c9429e.png)

1. 플랫폼 목록이 지원 되는 경우 **iOS**를 선택 합니다.

   ![IOS 구성](media/msal-net-use-brokers-with-xamarin-apps/60799411-60de4080-a173-11e9-9dcc-d39a45826d42.png)

1. 요청에 따라 번들 ID를 입력 하 고 **구성**을 선택 합니다.

   ![번들 ID 입력](media/msal-net-use-brokers-with-xamarin-apps/60799477-7eaba580-a173-11e9-9f8b-431f5b09344e.png)

단계를 완료 한 후에는 리디렉션 URI가 계산 됩니다.

![리디렉션 URI 복사](media/msal-net-use-brokers-with-xamarin-apps/60799538-9e42ce00-a173-11e9-860a-015a1840fd19.png)

## <a name="brokered-authentication-for-android"></a>Android에 대 한 조정 된 인증

### <a name="step-1-enable-broker-support"></a>1 단계: broker 지원 사용

Broker 지원은 PublicClientApplication 기준으로 설정 됩니다. 기본적으로 사용하지 않도록 설정되어 있습니다. `PublicClientApplicationBuilder`를 통해 `IPublicClientApplication`를 만들 때 `WithBroker()` 매개 변수를 사용 합니다 (기본적으로 true로 설정).

```CSharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithRedirectUri(redirectUriOnAndroid) //(see step 4 below)
                .Build();
```

### <a name="step-2-update-appdelegate-to-handle-the-callback"></a>2 단계: 콜백을 처리 하도록 AppDelegate 업데이트

MSAL.NET가 broker를 호출할 때 broker는 OnActivityResult () 메서드를 사용 하 여 응용 프로그램을 다시 호출 합니다. MSAL은 broker의 응답을 기다리기 때문에 응용 프로그램에서 결과를 MSAL.NET로 라우팅해야 합니다.
이렇게 하려면 다음과 같이 OnActivityResult () 메서드를 재정의 하 여 결과를 `SetAuthenticationContinuationEventArgs(int requestCode, Result resultCode, Intent data)`로 라우팅합니다.

```CSharp
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
   base.OnActivityResult(requestCode, resultCode, data);
   AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(requestCode, resultCode, data);
}
```

이 메서드는 broker 응용 프로그램이 시작 될 때마다 호출 되며, broker에서 응답을 처리 하 고 MSAL.NET에서 시작한 인증 프로세스를 완료할 수 있는 기회로 사용 됩니다.

### <a name="step-3-set-an-activity"></a>3 단계: 활동 설정

조정 된 인증이 작동 하려면 MSAL이 broker에서 응답을 보내고 받을 수 있도록 작업을 설정 해야 합니다.

이렇게 하려면 작업 (일반적으로 MainActivity)을 부모 개체와 `WithParentActivityOrWindow(object parent)`에 제공 해야 합니다. 

**예:**

토큰 획득 호출에서 다음을 수행 합니다.

```CSharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow((Activity)context))
             .ExecuteAsync();
```

### <a name="step-4-register-your-redirecturi-in-the-application-portal"></a>4 단계: 응용 프로그램 포털에 RedirectUri 등록

MSAL은 Url을 사용 하 여 broker를 호출한 다음 앱으로 돌아갑니다. 이 왕복을 완료 하려면 앱에 대 한 URL 체계를 등록 해야 합니다. 이 리디렉션 URI는 응용 프로그램에 대 한 유효한 리디렉션 URI로 Azure AD 앱 등록 포털에 등록 되어야 합니다.


응용 프로그램에 필요한 리디렉션 URI는 APK에 서명 하는 데 사용 되는 인증서에 따라 달라 집니다.

```
Example: msauth://com.microsoft.xforms.testApp/hgbUYHVBYUTvuvT&Y6tr554365466=
```

URI의 마지막 부분인 `hgbUYHVBYUTvuvT&Y6tr554365466=`는 APK가 서명 된 서명 (base64 인코딩)입니다.
그러나 Visual Studio를 사용 하는 응용 프로그램의 개발 단계 중에 특정 인증서를 사용 하 여 apk에 서명 하지 않고 코드를 디버깅 하는 경우 Visual Studio는 디버깅을 위해 apk에 서명 하 여 APK에 고유한 서명을 제공 합니다. 컴퓨터를 빌드할 수 있습니다. 따라서 다른 컴퓨터에서 앱을 빌드할 때마다 MSAL으로 인증 하기 위해 응용 프로그램 코드에서 리디렉션 URI를 업데이트 하 고 Azure Portal의 응용 프로그램 등록을 업데이트 해야 합니다. 

디버깅 하는 동안 제공 된 리디렉션 URI가 잘못 되었음을 나타내는 MSAL 예외 (또는 로그 메시지)가 발생할 수 있습니다. **또한이 예외** 는 디버깅 중인 현재 컴퓨터에서 사용 해야 하는 리디렉션 URI를 제공 합니다. 이 리디렉션 URI를 사용 하 여 시간에 대 한 개발을 계속할 수 있습니다.

코드를 종료할 준비가 되 면 코드의 리디렉션 URI와 Azure Portal의 응용 프로그램 등록을 업데이트 하 여 APK에 서명 하는 데 사용할 인증서의 서명을 사용 해야 합니다.

실제로이는 팀의 각 멤버에 대 한 리디렉션 URI와 프로덕션 서명 된 APK 버전의 리디렉션 URI를 등록 해야 함을 의미 합니다.

MSAL이이를 수행 하는 방법과 비슷하게이 서명을 직접 계산할 수도 있습니다. 

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

다음 명령을 사용 하 여 keytool을 사용 하 여 패키지에 대 한 서명을 가져오는 옵션도 있습니다.

Windows의 경우: `keytool.exe -list -v -keystore "%LocalAppData%\Xamarin\Mono for Android\debug.keystore" -alias androiddebugkey -storepass android -keypass android`

Mac: `keytool -exportcert -alias androiddebugkey -keystore ~/.android/debug.keystore | openssl sha1 -binary | openssl base64`

## <a name="next-steps"></a>다음 단계

[MSAL.NET와 함께 유니버설 Windows 플랫폼를 사용 하기 위한 고려 사항](msal-net-uwp-considerations.md)에 대해 알아봅니다.
