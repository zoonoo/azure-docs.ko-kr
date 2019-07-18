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
ms.openlocfilehash: c57feb33967732481d78e0ddaba5e90f4f82f327
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65544435"
---
# <a name="xamarin-android-specific-considerations-with-msalnet"></a>MSAL.NET 사용 하 여 Xamarin Android 관련 고려 사항
이 문서에서는.NET (MSAL.NET)에 대 한 Microsoft 인증 라이브러리를 사용 하 여 Xamarin Android에서 시스템 브라우저를 사용할 때 특정 고려 사항 설명 합니다.

MSAL.NET 2.4.0-preview부터 MSAL.NET Chrome 이외의 브라우저를 지원 하며 더 이상 Chrome 인증을 위해 Android 장치에 설치할 수 없습니다.

이러한 사용자 지정 탭을 지 원하는 브라우저를 사용 하는 것이 좋습니다.

| 사용자 지정 탭 지원을 통해 브라우저 | 패키지 이름 |
|------| ------- |
|Chrome | com.android.chrome|
|Microsoft Edge | com.microsoft.emmx|
|Firefox | org.mozilla.firefox|
|Ecosia | com.ecosia.android|
|키 위 | com.kiwibrowser.browser|
|용감한 | com.brave.browser|

이 테스트를 기반으로 사용자 지정 탭 지원을 통해 브라우저 외에도 사용자 지정 탭을 지원 하지 않는 몇 가지 브라우저 에서도 작동 인증: Opera, Opera Mini InBrowser, 및 Maxthon 합니다. 자세한 내용은 [테스트 결과 대 한 테이블](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Android-system-browser#devices-and-browsers-tested)합니다.

## <a name="known-issues"></a>알려진 문제

- 사용자가 장치에서 사용할 수 없는 브라우저, MSAL.NET 시킵니다는 `AndroidActivityNotFound` 예외입니다. 
  - **해결 방법**: 장치에서 브라우저 (가급적 하나는 사용자 지정 탭 지원과) 사용 하도록 해야 한다는 사용자에 게 알립니다.

- (예: 인증에 실패 하는 경우 인증 DuckDuckGo를 사용 하 여 시작), MSAL.NET 돌아갑니다는 `AuthenticationCanceled MsalClientException`합니다. 
  - **문제를 루트**: 지원 사용자 지정 탭을 사용 하 여 브라우저 장치에서 사용할 수 없습니다. 인증 대체 인증을 완료할 수 없습니다는 브라우저를 시작 합니다. 
  - **해결 방법**: (가급적 하나는 사용자 지정 탭 지원과) 브라우저를 해당 장치에 설치 해야 사용자에 게 알립니다.

## <a name="devices-and-browsers-tested"></a>장치 및 브라우저 테스트
다음 표에서 장치 및 테스트 된 브라우저를 나열 합니다.

| | 브라우저&ast;     |  결과  | 
| ------------- |:-------------:|:-----:|
| Huawei/One+ | Chrome&ast; | 합격|
| Huawei/One+ | Edge&ast; | 합격|
| Huawei/One+ | Firefox&ast; | 합격|
| Huawei/One+ | 용감한&ast; | 합격|
| 한 개 이상 | Ecosia&ast; | 합격|
| 한 개 이상 | 키 위&ast; | 합격|
| Huawei/One+ | Opera | 합격|
| Huawei | OperaMini | 합격|
| Huawei/One+ | InBrowser | 합격|
| 한 개 이상 | Maxthon | 합격|
| Huawei/One+ | DuckDuckGo | 취소 하는 사용자 인증|
| Huawei/One+ | UC Browser | 취소 하는 사용자 인증|
| 한 개 이상 | 돌고래 | 취소 하는 사용자 인증|
| 한 개 이상 | CM 브라우저 | 취소 하는 사용자 인증|
| Huawei/One+ | 설치 되지 않음 | AndroidActivityNotFound 예|

&ast; 지원 사용자 지정 탭

## <a name="next-steps"></a>다음 단계
코드 조각 및 시스템 브라우저를 사용 하 여 Xamarin Android를 사용 하 여 추가 정보 이해 되십니까 [가이드](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/MSAL.NET-uses-web-browser#choosing-between-embedded-web-browser-or-system-browser-on-xamarinandroid)합니다.  