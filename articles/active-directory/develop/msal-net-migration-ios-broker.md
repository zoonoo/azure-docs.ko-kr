---
title: 브로커를 사용하여 MSAL.NET으로 Xamarin 앱 마이그레이션
titleSuffix: Microsoft identity platform
description: Microsoft Authenticator를 사용하는 Xamarin iOS 앱을 ADAL.NET에서 MSAL.NET으로 마이그레이션하는 방법을 알아봅니다.
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/08/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev, has-adal-ref
ms.openlocfilehash: e77a0c3ccfbd6c1b11948724083c896a5d192a65
ms.sourcegitcommit: 34aa13ead8299439af8b3fe4d1f0c89bde61a6db
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2021
ms.locfileid: "122568084"
---
# <a name="migrate-ios-applications-that-use-microsoft-authenticator-from-adalnet-to-msalnet"></a>Microsoft Authenticator를 사용하는 iOS 애플리케이션을 ADAL.NET에서 MSAL.NET으로 마이그레이션

.NET(ADAL.NET) 및 iOS Broker 용 Azure Active Directory 인증 라이브러리를 사용하고 있습니다. 이제 릴리스 4.3부터 iOS에서 Broker를 지원하는 .NET(MSAL.NET)용 [Microsoft Authentication Library](msal-overview.md)로 마이그레이션 할 시간입니다.

어디서 시작해야 합니까? 이 문서는 ADAL에서 MSAL으로 Xamarin iOS 앱을 마이그레이션하는 데 도움이 됩니다.

## <a name="prerequisites"></a>필수 구성 요소
이 문서에서는 iOS Broker와 통합된 Xamarin iOS 앱이 이미 있다고 가정합니다. 그렇지 않은 경우 MSAL.NET으로 직접 이동하여 해당 위치에서 Broker 구현을 시작합니다. 새 애플리케이션을 사용하여 MSAL.NET에서 iOS Broker를 호출하는 방법에 대한 자세한 내용은 [이 설명서](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Leveraging-the-broker-on-iOS#why-use-brokers-on-xamarinios-and-xamarinandroid-applications)를 참조하십시오.

## <a name="background"></a>배경

### <a name="what-are-brokers"></a>Broker는 무엇인가요?

Broker는 Microsoft가 Android 및 iOS에서 제공하는 애플리케이션입니다. (iOS 및 Android에서는 [Microsoft Authenticator](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6) 앱을, Android에서는 Intune 회사 포털 앱을 참조하세요.)

다음을 사용하도록 설정합니다:

- Single Sign-On
- 디바이스 ID는 일부 [조건부 액세스 정책](../conditional-access/overview.md)에 필요합니다. 자세한 내용은 [디바이스 관리](../conditional-access/concept-conditional-access-conditions.md#device-platforms)를 참조하세요.
- 애플리케이션 ID 확인은 일부 엔터프라이즈 시나리오에도 필요합니다. 자세한 내용은 [Intune MAM(모바일 애플리케이션 관리)](/intune/mam-faq)을 참조하세요.

## <a name="migrate-from-adal-to-msal"></a>ADAL에서 MSAL로 마이그레이션

### <a name="step-1-enable-the-broker"></a>1단계: Broker를 사용하도록 설정

<table>
<tr><td>현재 ADAL 코드:</td><td>MSAL 대응:</td></tr>
<tr><td>
ADAL.NET에서 Broker 지원은 인증별 컨텍스트를 기준으로 사용하도록 설정되었습니다. 기본적으로 사용하지 않도록 설정되어 있습니다. 다음을 설정해야 합니다

Broker를 호출하려면 `PlatformParameters` 생성자에서 `useBroker` TRUE로 플래그를 지정합니다:

```csharp
public PlatformParameters(
        UIViewController callerViewController,
        bool useBroker)
```
또한 플랫폼별 코드(이 예제에서는 iOS용 페이지 렌더러)에서 `useBroker`을 설정합니다.
TRUE로 플래그 지정:
```csharp
page.BrokerParameters = new PlatformParameters(
          this,
          true,
          PromptBehavior.SelectAccount);
```

그런 다음 토큰 가져오기 호출에 매개 변수를 포함합니다:
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
MSAL.NET에서 Broker 지원은 PublicClientApplication별 기준으로 사용하도록 설정됩니다. 기본적으로 사용하지 않도록 설정되어 있습니다. 사용하도록 설정하려면 다음을 사용합니다

Broker를 호출하기 위한 `WithBroker()` 매개 변수(기본적으로 TURE로 설정됨):

```csharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos)
                .Build();
```
토큰 가져오기 호출에서 다음을 수행합니다:
```csharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow(App.RootViewController)
             .ExecuteAsync();
```
</table>

### <a name="step-2-set-a-uiviewcontroller"></a>2단계: UIViewController() 설정
ADAL.NET에서 `PlatformParameters`의 일부로 UIViewController를 전달했습니다. (1 단계 예제를 참조합니다.) MSAL.NET에서는 개발자에게 더 많은 유연성을 제공하기 위해 개체 창이 사용되지만 일반적인 iOS 사용에는 필요하지 않습니다. Broker를 사용하려면 Broker에서 응답을 보내고 받기 위해 개체 창을 설정합니다.
<table>
<tr><td>현재 ADAL 코드:</td><td>MSAL 대응:</td></tr>
<tr><td>
UIViewController는 다음으로 전달됩니다.

iOS 특정 플랫폼의 `PlatformParameters`.

```csharp
page.BrokerParameters = new PlatformParameters(
          this,
          true,
          PromptBehavior.SelectAccount);
```
</td><td>
MSAL.NET에서 iOS용 개체 창을 설정하려면 두 가지 작업을 수행합니다:

1. `AppDelegate.cs`에서 `App.RootViewController`를 새 `UIViewController()`으로 설정합니다.
이 할당으로 Broker를 호출하는 UIViewController가 있는지 확인합니다. 올바르게 설정되지 않은 경우 다음 오류가 발생할 수 있습니다: `"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`
1. AcquireTokenInteractive 호출에서 `.WithParentActivityOrWindow(App.RootViewController)`을 사용하고, 사용할 개체 창에 대한 참조를 전달합니다.

**예를 들면 다음과 같습니다.**

`App.cs`의 경우
```csharp
   public static object RootViewController { get; set; }
```
`AppDelegate.cs`의 경우
```csharp
   LoadApplication(new App());
   App.RootViewController = new UIViewController();
```
토큰 가져오기 호출에서 다음을 수행합니다:
```csharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow(App.RootViewController)
             .ExecuteAsync();
```

</table>

### <a name="step-3-update-appdelegate-to-handle-the-callback"></a>3단계: 콜백을 처리하도록 AppDelegate 업데이트
ADAL과 MSAL은 모두 Broker를 호출하고 Broker는 순서대로 `AppDelegate` 클래스의 `OpenUrl` 메서드를 통해 애플리케이션을 다시 호출합니다. 자세한 내용은 [SDK 설명서](msal-net-use-brokers-with-xamarin-apps.md#step-3-update-appdelegate-to-handle-the-callback)를 참조하세요.

ADAL.NET과 MSAL.NET 사이에는 변경 내용이 없습니다.

### <a name="step-4-register-a-url-scheme"></a>4단계: URL 구성표 등록
ADAL.NET 및 MSAL.NET은 URL을 사용하여 Broker를 호출하고 Broker 응답을 앱으로 다시 반환합니다. 다음과 같이 앱의 `Info.plist` 파일에 URL 구성표를 등록합니다:

<table>
<tr><td>현재 ADAL 코드:</td><td>MSAL 대응:</td></tr>
<tr><td>
URL 구성표는 앱에 고유합니다.
</td><td>
이

`CFBundleURLSchemes` 이름은 다음을 포함해야 합니다

`msauth.`

접두사로, `CFBundleURLName` 뒤에 나와야 합니다

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
> 이 URL 구성표는 Broker의 응답을 받을 때 앱을 고유하게 식별하는데 사용되는 리디렉션 URI의 일부가 됩니다.

</table>

### <a name="step-5-add-the-broker-identifier-to-the-lsapplicationqueriesschemes-section"></a>5단계: LSApplicationQueriesSchemes 섹션에 Broker 식별자 추가

ADAL.NET과 MSAL.NET은 모두 `-canOpenURL:`을 사용하여 디바이스에 Broker가 설치되어 있는지 확인합니다. 다음과 같이 info.plist 파일의 LSApplicationQueriesSchemes 섹션에 iOS Broker에 대한 올바른 식별자를 추가합니다:

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

### <a name="step-6-register-your-redirect-uri-in-the-azure-portal"></a>6 단계: Azure Portal에서 리디렉션 URI 등록

ADAL.NET 및 MSAL.NET은 모두 Broker를 대상으로 하는 경우 리디렉션 URI에 대한 추가 요구 사항을 추가합니다. Azure Portal에서 리디렉션 URI를 애플리케이션에 등록합니다.
<table>
<tr><td>현재 ADAL 코드:</td><td>MSAL 대응:</td></tr>
<tr><td>

`"<app-scheme>://<your.bundle.id>"`

예:

`mytestiosapp://com.mycompany.myapp`
</td><td>

`$"msauth.{BundleId}://auth"`

예:

`public static string redirectUriOnIos = "msauth.com.yourcompany.XForms://auth"; `

</table>

Azure Portal에서 리디렉션 URI를 등록하는 방법에 대한 자세한 내용은 [7 단계: 앱 등록에 리디렉션 URI 추가](msal-net-use-brokers-with-xamarin-apps.md#step-7-add-a-redirect-uri-to-your-app-registration)를 참조하세요.

### <a name="step-7-set-the-entitlementsplist"></a>**7 단계: Entitlements.plist 설정**

*Entitlements.plist* 파일에서 키 집합 액세스를 사용하도록 설정합니다:

```xml
 <key>keychain-access-groups</key>
    <array>
      <string>$(AppIdentifierPrefix)com.microsoft.adalcache</string>
    </array>
```

키 집합 액세스를 사용하도록 설정하는 것에 대한 자세한 내용은 [키 집합 액세스 사용 설정](msal-net-xamarin-ios-considerations.md#enable-keychain-access)을 참조하세요.

## <a name="next-steps"></a>다음 단계

[MSAL.NET의 Xamarin iOS 관련 고려 사항](msal-net-xamarin-ios-considerations.md)에 대해 알아봅니다.