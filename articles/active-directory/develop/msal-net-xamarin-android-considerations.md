---
title: 자마린 안드로이드 고려 사항 (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: .NET(MSAL.NET)에 대한 Microsoft 인증 라이브러리에서 Xamarin Android를 사용하는 방법에 대해 알아봅니다.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/24/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 81b55253d757f641979c6f72001803d7d38d9af3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77132507"
---
# <a name="considerations-for-using-xamarin-android-with-msalnet"></a>MSAL.NET 함께 자마린 안드로이드를 사용하기위한 고려 사항
이 문서에서는 .NET(MSAL.NET)에 대한 Microsoft 인증 라이브러리에서 Xamarin Android를 사용할 때 고려해야 할 사항과 대해 설명합니다.

## <a name="set-the-parent-activity"></a>상위 활동 설정

Xamarin Android에서 상호 작용 후 토큰이 반환되도록 부모 활동을 설정합니다. 코드 예제는 다음과 같습니다.

```csharp
var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .ExecuteAsync();
```

MSAL 4.2 이상에서는 이 기능을 `PublicClientApplication`의 수준에서 설정할 수도 있습니다. 이렇게 하려면 콜백을 사용합니다.

```csharp
// Requires MSAL.NET 4.2 or later
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => parentActivity)
  .Build();
```

[CurrentActivityPlugin을](https://github.com/jamesmontemagno/CurrentActivityPlugin)사용하는 경우 `PublicClientApplication` 빌더 코드는 다음 예제와 같습니다.

```csharp
// Requires MSAL.NET 4.2 or later
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => CrossCurrentActivity.Current)
  .Build();
```

## <a name="ensure-that-control-returns-to-msal"></a>제어가 MSAL로 반환되도록 합니다. 
인증 흐름의 대화형 부분이 끝나면 해당 컨트롤이 MSAL로 돌아가야 합니다. Android에서 `OnActivityResult` `Activity`의 메서드를 재정의합니다. 그런 다음 `SetAuthenticationContinuationEventArgs` MSAL `AuthenticationContinuationHelper` 클래스의 메서드를 호출합니다. 

예를 들면 다음과 같습니다.

```csharp
protected override void OnActivityResult(int requestCode, 
                                         Result resultCode, Intent data)
{
 base.OnActivityResult(requestCode, resultCode, data);
 AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(requestCode,
                                                                         resultCode,
                                                                         data);
}

```

이 줄은 인증 흐름의 대화형 부분 끝에 컨트롤이 MSAL로 반환되도록 합니다.

## <a name="update-the-android-manifest"></a>안드로이드 매니페스트 업데이트
*AndroidManifest.xml* 파일에는 다음 값이 포함되어야 합니다.

<!--Intent filter to capture System Browser or Authenticator calling back to our app after sign-in-->
```
  <activity
        android:name="com.microsoft.identity.client.BrowserTabActivity">
     <intent-filter>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.DEFAULT" />
            <category android:name="android.intent.category.BROWSABLE" />
            <data android:scheme="msauth"
                android:host="Enter_the_Package_Name"
                android:path="/Enter_the_Signature_Hash" />
     </intent-filter>
 </activity>
```

Azure 포털에 등록한 패키지 이름을 `android:host=` 값으로 대체합니다. Azure 포털에 등록한 키 해시를 값으로 대체합니다. `android:path=` 서명 해시는 URL인코딩을 *해서는* 안 됩니다. 서명 해시의 시작`/`부분에 선행 슬래시() 가 나타나는지 확인합니다.

또는 *AndroidManifest.xml을*수동으로 편집하는 대신 [코드에서 활동을 만듭니다.](https://docs.microsoft.com/xamarin/android/platform/android-manifest#the-basics) 코드에서 활동을 만들려면 먼저 `Activity` 특성과 특성이 포함된 `IntentFilter` 클래스를 만듭니다. 

다음은 XML 파일의 값을 나타내는 클래스의 예입니다.

```csharp
  [Activity]
  [IntentFilter(new[] { Intent.ActionView },
        Categories = new[] { Intent.CategoryBrowsable, Intent.CategoryDefault },
        DataHost = "auth",
        DataScheme = "msal{client_id}")]
  public class MsalActivity : BrowserTabActivity
  {
  }
```

### <a name="xamarinforms-43x-manifest"></a>자마린.양식 4.3.X 매니페스트

Xamarin.Forms 4.3.x는 `package` *AndroidManifest.xml에서*속성을 `com.companyname.{appName}` 설정하는 코드를 생성합니다. 을 로 `DataScheme` `msal{client_id}`사용하는 경우 `MainActivity.cs` 네임스페이스 값과 일치하도록 값을 변경할 수 있습니다.

## <a name="use-the-embedded-web-view-optional"></a>포함된 웹 보기 사용(선택 사항)

기본적으로 MSAL.NET 시스템 웹 브라우저를 사용합니다. 이 브라우저를 사용하면 웹 응용 프로그램 및 기타 앱을 사용하여 단일 사인온(SSO)을 얻을 수 있습니다. 드문 경우지만 시스템에서 포함된 웹 보기를 사용하도록 할 수 있습니다. 

이 코드 예제에서는 포함된 웹 보기를 설정하는 방법을 보여 주며 다음과 같은 방법을 보여 주며 있습니다.

```csharp
bool useEmbeddedWebView = !app.IsSystemWebViewAvailable;

var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .WithEmbeddedWebView(useEmbeddedWebView)
 .ExecuteAsync();
```

자세한 내용은 MSAL.NET 및 [Xamarin Android 시스템 브라우저 고려 사항에](msal-net-system-browser-android-considerations.md)대한 웹 브라우저 [사용을](msal-net-web-browsers.md) 참조하십시오.


## <a name="troubleshoot"></a>문제 해결
새 Xamarin.Forms 응용 프로그램을 만들고 nuGet 패키지에 대한 참조를 추가할 수 MSAL.NET.
그러나 기존 Xamarin.Forms 응용 프로그램을 미리 보기 1.1.2 이상으로 업그레이드하면 빌드 문제가 있을 수 MSAL.NET.

빌드 문제를 해결하려면 다음을 수행하십시오.

- 기존 MSAL.NET NuGet 패키지를 업데이트하여 미리 MSAL.NET 1.1.2 이상입니다.
- Xamarin.Forms버전 2.5.0.122203으로 자동으로 업데이트되어 있는지 확인합니다. 필요한 경우 Xamarin.Forms를 이 버전으로 업데이트합니다.
- Xamarin.Android.Support.v4버전 25.4.0.2로 자동으로 업데이트되어 있는지 확인합니다. 필요한 경우 버전 25.4.0.2로 업데이트합니다.
- 모든 Xamarin.Android.Support 패키지 대상 버전 25.4.0.2를 확인합니다.
- 응용 프로그램을 정리하거나 다시 빌드합니다.
- Visual Studio에서 병렬 프로젝트 빌드의 최대 수를 1로 설정해 보십시오. 이렇게 하려면 **옵션** > **프로젝트 및 솔루션** > **빌드 및 실행** > **병렬 프로젝트 빌드의 최대 수를 선택합니다.**
- 명령줄에서 빌드하는 경우 명령에서 `/m`이 요소를 제거해 보십시오.

### <a name="error-the-name-authenticationcontinuationhelper-doesnt-exist-in-the-current-context"></a>오류: 이름 인증Continuation도움말 현재 컨텍스트에 없는

오류가 현재 컨텍스트에 존재하지 않는 것을 `AuthenticationContinuationHelper` 나타내는 경우 Visual Studio에서 Android.csproj* 파일을 잘못 업데이트했을 수 있습니다. 경우에 따라 * \<HintPath>* 파일 경로에 *monoandroid90*대신 *netstandard13이* 잘못 포함되어 있습니다.

이 예제에는 올바른 파일 경로가 포함되어 있습니다.

```xml
<Reference Include="Microsoft.Identity.Client, Version=3.0.4.0, Culture=neutral, PublicKeyToken=0a613f4dd989e8ae,
           processorArchitecture=MSIL">
  <HintPath>..\..\packages\Microsoft.Identity.Client.3.0.4-preview\lib\monoandroid90\Microsoft.Identity.Client.dll</HintPath>
</Reference>
```

## <a name="next-steps"></a>다음 단계

자세한 내용은 [Microsoft ID 플랫폼을 사용하는 Xamarin 모바일 응용 프로그램의](https://github.com/azure-samples/active-directory-xamarin-native-v2#android-specific-considerations)샘플을 참조하십시오. 다음 표에서는 README 파일의 관련 정보를 요약합니다.

| 예제 | 플랫폼 | 설명 |
| ------ | -------- | ----------- |
|[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | 자마린.iOS, 안드로이드, UWP | MSAL을 사용하여 Azure AD 2.0 끝점을 통해 Microsoft 개인 계정 및 Azure AD를 인증하는 방법을 보여 주는 간단한 Xamarin.Forms 앱입니다. 응용 프로그램은 또한 마이크로 소프트 그래프에 액세스하는 방법을 보여주고 결과 토큰을 보여줍니다. <br>![토폴로지](media/msal-net-xamarin-android-considerations/topology.png) |
