---
title: Xamarin Android 고려 사항 (Microsoft Authentication Library for.NET) | Azure
description: .NET (MSAL.NET)에 대 한 Microsoft 인증 라이브러리를 사용 하 여 Xamarin Android를 사용할 때 특정 고려 사항에 알아봅니다.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/24/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: cb0cfb06e95cadbb549f669e5d59bdb0d795c896
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65545873"
---
# <a name="xamarin-android-specific-considerations-with-msalnet"></a>MSAL.NET 사용 하 여 Xamarin Android 관련 고려 사항
이 문서에서는.NET (MSAL.NET)에 대 한 Microsoft 인증 라이브러리를 사용 하 여 Xamarin Android를 사용할 때 특정 고려 사항을 설명 합니다.

이 문서는 MSAL.NET 3.x용으로 작성되었습니다. MSAL.NET에 관심이 있다면 2.x를 참조 하세요 [MSAL.NET에서 Xamarin Android 고유 정보 2.x](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Xamarin-Android-specifics-2x)합니다.

## <a name="set-the-parent-activity"></a>부모 활동 설정

Xamarin.Android에서 토큰 상호 작용 발생 한 후에 다시 있도록 부모 활동을 설정 해야 합니다.

```csharp
var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .ExecuteAsync();
```

## <a name="ensuring-control-goes-back-to-msal-once-the-interactive-portion-of-the-authentication-flow-ends"></a>컨트롤을 보장 돌아갑니다 MSAL에 한 번 인증 흐름 끝의 대화형 부분
Android에서 재정의 해야 합니다 `OnActivityResult` 메서드는 `Activity` AuthenticationContinuationHelper MSAL 클래스의 SetAuthenticationContinuationEventArgs 메서드를 호출 합니다.

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
해당 줄에는 컨트롤 돌아갑니다 MSAL 인증 흐름의 대화형 부분 종료 되 면 확인 합니다.

## <a name="update-the-android-manifest"></a>Android 매니페스트 업데이트
`AndroidManifest.xml` 다음 값을 포함 해야 합니다.
```csharp
<activity android:name="microsoft.identity.client.BrowserTabActivity">
    <intent-filter>
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />
        <data android:scheme="msal{client_id}" android:host="auth" />
         </intent-filter>
</activity>
```

## <a name="use-the-embedded-web-view-optional"></a>(선택 사항) 포함 된 웹 보기를 사용 합니다.

기본적으로 MSAL.NET 웹 응용 프로그램을 사용 하 여 SSO 및 기타 앱을 가져올 수 있도록 하는 시스템 웹 브라우저를 사용 합니다. 일부 드문 경우에서 포함 된 웹 보기를 사용 한다고 지정 하는 것이 좋습니다. 자세한 내용은 [MSAL.NET 웹 브라우저를 사용 하 여](msal-net-web-browsers.md) 하 고 [Android 시스템 브라우저](msal-net-system-browser-android-considerations.md)합니다.

```csharp
bool useEmbeddedWebView = !app.IsSystemWebViewAvailable;

var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .WithEmbeddedWebView(useEmbeddedWebView)
 .ExecuteAsync();
```

## <a name="troubleshooting"></a>문제 해결
새 Xamarin.Forms 응용 프로그램을 만들고 MSAL.Net NuGet 패키지에 대 한 참조를 추가 하는 경우이 작동 합니다.
그러나 업그레이드 하려는 경우 MSAL.NET 기존 Xamarin.Forms 응용 프로그램 1.1.2를 미리 보거나 나중 빌드 문제가 발생할 수 있습니다.

이러한 문제를 해결 하려면 다음을 수행 해야 합니다.
- 기존 MSAL.NET NuGet 패키지 MSAL.NET preview 1.1.2로 업데이트 이상
- Xamarin.Forms 버전 2.5.0.122203 (없는 경우,이 버전으로 업데이트)에 자동으로 업데이트를 확인 합니다.
- Xamarin.Android.Support.v4 25.4.0.2 버전 (없는 경우,이 버전으로 업데이트)에 자동으로 업데이트를 확인 합니다.
- 버전 25.4.0.2 Xamarin.Android.Support 패키지를 대상으로 해야
- 정리/다시 작성
- 최대 병렬 프로젝트 설정 시도 1 Visual Studio에서 빌드 (옵션-프로젝트 > 솔루션 빌드-> 구문 및 실행에는 최대 병렬 프로젝트 빌드->)
- 또는 명령줄에서 작성 하는 경우에 사용 하는 경우 /m 명령에서 제거 하십시오.


### <a name="error-the-name-authenticationcontinuationhelper-does-not-exist-in-the-current-context"></a>오류: 이름 'AuthenticationContinuationHelper' 현재 컨텍스트에 없습니다.

Visual Studio Android.csproj* 파일을 올바르게 업데이트 되지 않은 것입니다. 경우에 따라 합니다 **<HintPath>** 파일 경로 대신 netstandard13 올바르게 포함 **monoandroid90**합니다.

```xml
<Reference Include="Microsoft.Identity.Client, Version=3.0.4.0, Culture=neutral, PublicKeyToken=0a613f4dd989e8ae,
           processorArchitecture=MSIL">
  <HintPath>..\..\packages\Microsoft.Identity.Client.3.0.4-preview\lib\monoandroid90\Microsoft.Identity.Client.dll</HintPath>
</Reference>
```

## <a name="next-steps"></a>다음 단계

자세한 내용 및 예제에 나와 합니다 [Android 특정 고려 사항](https://github.com/azure-samples/active-directory-xamarin-native-v2#android-specific-considerations) 단락에 다음 샘플의 readme.md 파일:

| 샘플 | 플랫폼 | 설명 |
| ------ | -------- | ----------- |
|[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin iOS, Android, UWP | MSAL을 사용 하 여 MSA 및 AADD v2.0 끝점을 통해 Azure AD를 인증 하 고 결과 토큰을 사용 하 여 Microsoft Graph를 액세스 하는 방법을 보여주는 간단한 Xamarin Forms 앱입니다. <br>![토폴로지](media/msal-net-xamarin-android-considerations/topology.png) |