---
title: Xamarin Android 고려 사항 (.NET 용 Microsoft 인증 라이브러리)
titleSuffix: Microsoft identity platform
description: MSAL.NET (Microsoft Authentication Library for .NET)에서 Xamarin Android를 사용 하는 경우의 특정 고려 사항에 대해 알아봅니다.
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/24/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8f5caad4b136c9ef2686cc4befc70e6720e27855
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72802740"
---
# <a name="xamarin-android-specific-considerations-with-msalnet"></a>MSAL.NET를 사용 하 여 Xamarin Android 관련 고려 사항
이 문서에서는 Microsoft Authentication Library for .NET (MSAL.NET)을 사용 하 여 Xamarin Android에서 시스템 브라우저를 사용 하는 경우의 구체적인 고려 사항을 설명 합니다.

MSAL.NET 2.4.0-preview부터 MSAL.NET는 Chrome 이외의 브라우저를 지원 하 고, 인증을 위해 Android 장치에 Chrome을 더 이상 설치 하지 않아도 됩니다.

다음과 같이 사용자 지정 탭을 지 원하는 브라우저를 사용 하는 것이 좋습니다.

| 사용자 지정 탭이 지원 되는 브라우저 | 패키지 이름 |
|------| ------- |
|Chrome | com. android chrome|
|Microsoft Edge | .com. emmx|
|Firefox | org. firefox|
|Ecosia | ecosia|
|키 위 | kiwibrowser|
|감 | com. 용감한 브라우저|

테스트에 따라 사용자 지정 탭이 지원 되는 브라우저 외에도, 사용자 지정 탭을 지원 하지 않는 몇 가지 브라우저가 인증에 대해 작동 합니다. Opera, Opera Mini, InBrowser 및 Maxthon. 자세한 내용은 [테스트 결과에 대 한 테이블](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Android-system-browser#devices-and-browsers-tested)을 참조 하세요.

## <a name="known-issues"></a>알려진 문제

- 사용자가 장치에서 브라우저를 사용 하도록 설정 하지 않은 경우 MSAL.NET는 `AndroidActivityNotFound` 예외를 throw 합니다. 
  - **완화**방법: 사용자에 게 장치에서 브라우저를 사용 하도록 설정 해야 한다는 것을 사용자에 게 알립니다.

- 인증에 실패 하는 경우 (예: DuckDuckGo를 사용 하 여 인증을 시작 합니다. MSAL.NET는 `AuthenticationCanceled MsalClientException`을 반환 합니다. 
  - **근본 문제**: 장치에서 사용자 지정 탭을 지 원하는 브라우저를 사용할 수 없습니다. 인증을 완료할 수 없는 대체 브라우저를 사용 하 여 인증을 시작 했습니다. 
  - **완화**방법: 사용자에 게 장치에 브라우저 (사용자 지정 탭 지원이 있는 경우)를 설치 해야 한다고 알립니다.

## <a name="devices-and-browsers-tested"></a>테스트 한 장치 및 브라우저
다음 표에서는 테스트 된 장치와 브라우저를 보여 줍니다.

| | 브라우저&ast;     |  결과  | 
| ------------- |:-------------:|:-----:|
| Huawei/One + | Chrome&ast; | 합격|
| Huawei/One + | 에 지&ast; | 합격|
| Huawei/One + | Firefox&ast; | 합격|
| Huawei/One + | 용감한&ast; | 합격|
| 1 + | Ecosia&ast; | 합격|
| 1 + | 키 위&ast; | 합격|
| Huawei/One + | Opera | 합격|
| Huawei | 운영 체제 | 합격|
| Huawei/One + | InBrowser | 합격|
| 1 + | Maxthon | 합격|
| Huawei/One + | DuckDuckGo | 사용자가 인증을 취소 함|
| Huawei/One + | UC 브라우저 | 사용자가 인증을 취소 함|
| 1 + | Dolphin | 사용자가 인증을 취소 함|
| 1 + | CM 브라우저 | 사용자가 인증을 취소 함|
| Huawei/One + | 설치 안 됨 | AndroidActivityNotFound 예|

사용자 지정 탭을 지 원하는 &ast;

## <a name="next-steps"></a>다음 단계
Xamarin Android에서 시스템 브라우저를 사용 하는 방법에 대 한 코드 조각과 추가 정보는이 [가이드](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/MSAL.NET-uses-web-browser#choosing-between-embedded-web-browser-or-system-browser-on-xamarinandroid)를 참조 하세요.  