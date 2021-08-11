---
title: Xamarin Android 시스템 브라우저 고려 사항(MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: .NET용 Microsoft 인증 라이브러리(MSAL.NET)에서 Xamarin Android의 시스템 브라우저를 사용하기 위한 고려 사항에 관해 알아봅니다.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 4230a194fb18587a209c100a39b0924e6170502d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98063470"
---
#  <a name="xamarin-android-system-browser-considerations-for-using-msalnet"></a>MSAL.NET 사용을 위한 Xamarin Android 시스템 브라우저 고려 사항

이 문서에서는 .NET용 Microsoft 인증 라이브러리(MSAL.NET)에서 Xamarin Android의 시스템 브라우저를 사용하는 경우 고려해야 할 사항을 설명합니다.

MSAL.NET 2.4.0 미리 보기부터, MSAL.NET은 Chrome 이외의 브라우저를 지원합니다. 더 이상 인증을 위해 Android 디바이스에 Chrome을 설치할 필요가 없습니다.

사용자 지정 탭을 지원하는 브라우저를 사용하는 것이 좋습니다. 해당 브라우저의 몇 가지 예는 다음과 같습니다.

| 사용자 지정 탭 지원이 있는 브라우저 | 패키지 이름 |
|------| ------- |
|Chrome | com.android.chrome|
|Microsoft Edge | com.microsoft.emmx|
|Firefox | org.mozilla.firefox|
|Ecosia | com.ecosia.android|
|Kiwi | com.kiwibrowser.browser|
|Brave | com.brave.browser|

사용자 지정 탭 지원을 제공하는 브라우저를 식별하는 것 외에도 테스트에 따르면 사용자 지정 탭을 지원하지 않는 몇 가지 브라우저가 인증에서 작동합니다. 해당 브라우저에는 Opera, Opera Mini, InBrowser, Maxthon 등이 포함됩니다. 

## <a name="tested-devices-and-browsers"></a>테스트된 디바이스 및 브라우저
다음 표에는 인증 호환성에 대해 테스트된 디바이스와 브라우저가 나와 있습니다.

| 디바이스 | 브라우저     |  결과  | 
| ------------- |:-------------:|:-----:|
| Huawei/One+ | Chrome\* | 합격|
| Huawei/One+ | Edge\* | 합격|
| Huawei/One+ | Firefox\* | 합격|
| Huawei/One+ | Brave\* | 합격|
| One+ | Ecosia\* | 합격|
| One+ | Kiwi\* | 합격|
| Huawei/One+ | Opera | 합격|
| Huawei | OperaMini | 합격|
| Huawei/One+ | InBrowser | 합격|
| One+ | Maxthon | 합격|
| Huawei/One+ | DuckDuckGo | 사용자가 인증을 취소함|
| Huawei/One+ | UC Browser | 사용자가 인증을 취소함|
| One+ | Dolphin | 사용자가 인증을 취소함|
| One+ | CM Browser | 사용자가 인증을 취소함|
| Huawei/One+ | 설치되지 않음 | AndroidActivityNotFound 예외|

\* 사용자 지정 탭 지원

## <a name="known-issues"></a>알려진 문제

사용자가 디바이스에서 브라우저를 사용하도록 설정하지 않은 경우 MSAL.NET은 `AndroidActivityNotFound` 예외를 throw합니다.  
  - **완화**: 디바이스에서 브라우저를 사용하도록 설정하도록 사용자에게 요청합니다. 사용자 지정 탭을 지원하는 브라우저를 권장합니다.

인증이 실패하는 경우(예: 인증이 DuckDuckGo를 사용하여 시작되는 경우) MSAL.NET은 `AuthenticationCanceled MsalClientException`을 반환합니다. 
  - **근본 문제**: 사용자 지정 탭을 지원하는 브라우저가 디바이스에서 사용하도록 설정되지 않았습니다. 인증을 완료할 수 없는 브라우저를 사용하여 인증이 시작되었습니다. 
  - **완화**: 디바이스에서 브라우저를 사용하도록 설정하도록 사용자에게 요청합니다. 사용자 지정 탭을 지원하는 브라우저를 권장합니다.

## <a name="next-steps"></a>다음 단계
자세한 내용과 코드 예제는 [포함된 웹 브라우저와 Xamarin Android의 시스템 브라우저 중에 선택](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/MSAL.NET-uses-web-browser#choosing-between-embedded-web-browser-or-system-browser-on-xamarinandroid) 및 [포함 및 시스템 웹 UI](msal-net-web-browsers.md#embedded-vs-system-web-ui)를 참조하세요.  