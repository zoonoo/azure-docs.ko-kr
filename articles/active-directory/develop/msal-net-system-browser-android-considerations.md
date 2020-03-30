---
title: 자마린 안 드 로이드 시스템 브라우저 고려 사항 (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: .NET(MSAL.NET)에 대한 Microsoft 인증 라이브러리와 함께 Xamarin Android에서 시스템 브라우저를 사용하는 방법에 대해 알아봅니다.
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
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: ad26a4d619a7984f08a8decc87f9339adae47cdd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77132603"
---
#  <a name="xamarin-android-system-browser-considerations-for-using-msalnet"></a>MSAL.NET 사용에 대한 자마린 안드로이드 시스템 브라우저 고려 사항

이 문서에서는 .NET(MSAL.NET)에 대한 Microsoft 인증 라이브러리와 함께 Xamarin Android에서 시스템 브라우저를 사용할 때 고려해야 할 사항과 대해 설명합니다.

MSAL.NET 2.4.0 미리 보기부터 chrome 이외의 브라우저를 지원하는 MSAL.NET. 인증을 위해 더 이상 Android 기기에 Chrome을 설치할 필요 없습니다.

사용자 지정 탭을 지원하는 브라우저를 사용하는 것이 좋습니다. 다음은 이러한 브라우저의 몇 가지 예입니다.

| 사용자 지정 탭이 지원되는 브라우저 | 패키지 이름 |
|------| ------- |
|Chrome | com.android.chrome|
|Microsoft Edge | com.마이크로소프트.emmx|
|Firefox | org.mozilla.firefox|
|에코시아 (있는) | com.ecosia.android|
|뉴질랜드 | com.kiwibrowser.browser.browser|
|용감한 | com.brave.브라우저|

사용자 지정 탭 지원을 제공하는 브라우저를 식별하는 것 외에도 사용자 지정 탭을 지원하지 않는 일부 브라우저도 인증을 위해 작동한다는 테스트가 표시됩니다. 이러한 브라우저는 오페라를 포함, 오페라 미니, InBrowser, 맥슨. 

## <a name="tested-devices-and-browsers"></a>테스트된 장치 및 브라우저
다음 표에는 인증 호환성을 테스트한 장치 및 브라우저가 나열되어 있습니다.

| 디바이스 | 브라우저     |  결과  | 
| ------------- |:-------------:|:-----:|
| 화웨이 / 하나 + | Chrome\* | 합격|
| 화웨이 / 하나 + | Edge\* | 합격|
| 화웨이 / 하나 + | Firefox\* | 합격|
| 화웨이 / 하나 + | 용감한\* | 합격|
| 1+ | 에코시아 (있는)\* | 합격|
| 1+ | 뉴질랜드\* | 합격|
| 화웨이 / 하나 + | Opera | 합격|
| Huawei | 오페라 미니 | 합격|
| 화웨이 / 하나 + | 인브라우저 | 합격|
| 1+ | Maxthon | 합격|
| 화웨이 / 하나 + | 덕덕고 | 사용자가 인증을 취소했습니다.|
| 화웨이 / 하나 + | UC 브라우저 | 사용자가 인증을 취소했습니다.|
| 1+ | 돌고래 | 사용자가 인증을 취소했습니다.|
| 1+ | CM 브라우저 | 사용자가 인증을 취소했습니다.|
| 화웨이 / 하나 + | 설치되지 않았습니다. | 안드로이드 활동발견 예외|

\*사용자 지정 탭 지원

## <a name="known-issues"></a>알려진 문제

사용자가 장치에서 브라우저를 사용하도록 설정한 경우 MSAL.NET `AndroidActivityNotFound` 예외를 throw합니다.  
  - **완화:** 사용자에게 장치에서 브라우저를 사용하도록 요청합니다. 사용자 지정 탭을 지원하는 브라우저를 추천합니다.

인증에 실패하면(예: DuckDuckGo로 인증이 시작되는 `AuthenticationCanceled MsalClientException`경우) MSAL.NET 반환됩니다. 
  - **루트 문제**: 사용자 지정 탭을 지원하는 브라우저가 장치에서 활성화되지 않았습니다. 인증을 완료할 수 없는 브라우저에서 인증이 시작되었습니다. 
  - **완화:** 사용자에게 장치에서 브라우저를 사용하도록 요청합니다. 사용자 지정 탭을 지원하는 브라우저를 추천합니다.

## <a name="next-steps"></a>다음 단계
자세한 정보 및 코드 예제는 Xamarin Android 및 [임베디드](msal-net-web-browsers.md#embedded-vs-system-web-ui)대 시스템 웹 [UI의 임베디드 웹 브라우저와 시스템 브라우저 사이에서 선택을](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/MSAL.NET-uses-web-browser#choosing-between-embedded-web-browser-or-system-browser-on-xamarinandroid) 참조하십시오.  