---
title: 권한 부여 에이전트 및 사용 하도록 설정 하는 방법 | Microsoft
description: MSAL (Microsoft 인증 라이브러리)에서 Android 앱을 사용 하 고 사용 하도록 설정 하는 방법에 대해 알아봅니다.
services: active-directory
author: tylermsft
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/05/2019
ms.author: twhitney
ms.reviewer: shoatman, brianmel, hahamil
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 44a0e93c87c50fbc63cddad349ec9b5fbf45d91f
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74843906"
---
# <a name="authorization-agents-android"></a>권한 부여 에이전트(Android)

이 문서에서는 MSAL (Microsoft 인증 라이브러리)에서 앱을 사용할 수 있도록 허용 하는 다양 한 권한 부여 에이전트와 앱을 사용 하도록 설정 하는 방법을 설명 합니다.

권한 부여 에이전트에 대 한 특정 전략 선택은 선택 사항이 며 앱에서 사용자 지정할 수 있는 추가 기능을 나타냅니다. 대부분의 앱은 MSAL 기본값을 사용 합니다. 다양 한 기본값을 보려면 [ANDROID msal 구성 파일 이해](msal-configuration.md) 를 참조 하세요.

MSAL은 `WebView`또는 시스템 브라우저를 사용 하 여 권한 부여를 지원 합니다.  다음 이미지는 `WebView`또는 CustomTabs이 있는 시스템 브라우저 또는 CustomTabs를 사용 하는 방법을 보여 줍니다.

![MSAL 로그인 예](./media/authorization-agents/sign-in-ui.jpg)

## <a name="single-sign-in-implications"></a>Single sign-on의 의미

기본적으로 MSAL과 통합 된 응용 프로그램은 시스템 브라우저의 사용자 지정 탭을 사용 하 여 권한을 부여 합니다. 웹 보기와 달리 사용자 지정 탭은 쿠키 jar을 기본 시스템 브라우저와 공유 하 여 웹 또는 사용자 지정 탭과 통합 된 다른 네이티브 앱을 사용 하 여 로그인을 줄여 줍니다.

응용 프로그램에서 Microsoft Authenticator 또는 회사 포털 지원을 앱에 통합 하지 않고 `WebView` 전략을 사용 하는 경우, 사용자는 장치 전체 또는 네이티브 앱과 웹 앱 간에 SSO (Single Sign-on) 환경을 제공 하지 않습니다.

응용 프로그램에서 Microsoft Authenticator 또는 회사 포털 지원으로 MSAL을 사용 하는 경우 사용자에 게 앱 중 하나를 사용 하 여 활성 로그인이 있는 경우 사용자가 응용 프로그램 간에 SSO 환경을 제공할 수 있습니다.

## <a name="webview"></a>WebView

앱 내 웹 보기를 사용 하려면 MSAL에 전달 되는 앱 구성 JSON에 다음 줄을 입력 합니다.

```json
"authorization_user_agent" : "WEBVIEW"
```

앱 내 `WebView`를 사용 하는 경우 사용자는 앱에 직접 로그인 합니다. 토큰은 앱의 샌드박스 내에 유지 되 고 앱의 쿠키 jar 외부에서 사용할 수 없습니다. 따라서 앱이 인증자 또는 회사 포털와 통합 되지 않은 경우 사용자는 응용 프로그램 간에 SSO 환경을 사용할 수 없습니다.

그러나 `WebView`는 로그인 UI의 모양과 느낌을 사용자 지정 하는 기능을 제공 합니다. 이 사용자 지정을 수행 하는 방법에 대 한 자세한 내용은 [Android 웹 보기](https://developer.android.com/reference/android/webkit/WebView) 를 참조 하세요.

## <a name="default-browser-plus-custom-tabs"></a>기본 브라우저 및 사용자 지정 탭

기본적으로 MSAL은 브라우저와 [사용자 지정 탭](https://developer.chrome.com/multidevice/android/customtabs) 전략을 사용 합니다. 사용자 지정 구성 파일에서 다음 JSON 구성을 사용 하 여 `DEFAULT`에 대 한 이후 릴리스가 변경 되는 것을 방지 하기 위해 명시적으로이 전략을 지정할 수 있습니다.

```json
"authorization_user_agent" : "BROWSER"
```

이 접근 방식을 사용 하 여 장치의 브라우저를 통해 SSO 환경을 제공 합니다. MSAL은 공유 쿠키 jar을 사용 합니다 .이를 통해 다른 네이티브 앱 또는 웹 앱은 MSAL으로 설정 된 영구 세션 쿠키를 사용 하 여 장치에서 SSO를 달성할 수 있습니다.

## <a name="browser-selection-heuristic"></a>브라우저 선택 추론

MSAL은 다양 한 Android 휴대폰 배열에서 사용할 정확한 브라우저 패키지를 지정 하는 것이 불가능 하기 때문에 MSAL은 가장 적합 한 장치 간 SSO를 제공 하려고 하는 브라우저 선택 추론을 구현 합니다.

MSAL은 장치에 설치 된 브라우저의 전체 목록을 검색 하 여 사용할 브라우저를 선택 합니다. 목록은 패키지 관리자에서 반환 된 순서 대로 표시 되며 사용자의 기본 설정을 간접적으로 반영 합니다. 예를 들어 기본 브라우저 (설정 된 경우)는 목록의 첫 번째 항목입니다. 목록의 _첫 번째_ 브라우저는 사용자 지정 탭을 지원 하는지 여부에 관계 없이 선택 됩니다. 브라우저에서 사용자 지정 탭을 지 원하는 경우 MSAL이 사용자 지정 탭을 시작 합니다. 사용자 지정 탭을 사용 하면 앱 내 `WebView`에 더 가깝게 표시 되 고 기본 UI 사용자 지정이 가능 합니다. 자세히 알아보려면 [Android의 사용자 지정 탭](https://developer.chrome.com/multidevice/android/customtabs) 을 참조 하세요.

장치에 브라우저 패키지가 없으면 MSAL은 앱 내 `WebView`을 사용 합니다.

브라우저 목록의 브라우저 순서는 운영 체제에 의해 결정 됩니다. 가장 선호 하는 순서 대로 정렬 됩니다. 장치 기본 설정이 변경 되지 않은 경우 SSO 환경을 보장 하기 위해 각 로그인에 대해 동일한 브라우저를 시작 해야 합니다.

> [!NOTE]
> 다른 브라우저가 기본값으로 설정 된 경우 MSAL은 더 이상 Chrome을 선호 하지 않습니다. 예를 들어 Chrome과 다른 브라우저가 미리 설치 된 장치에서 MSAL은 사용자가 기본값으로 설정 된 브라우저를 사용 합니다.

### <a name="tested-browsers"></a>테스트 된 브라우저

다음 브라우저는 구성 파일에 지정 된 `"redirect_uri"`에 올바르게 리디렉션해야 하는지 확인 하기 위해 테스트 되었습니다.

| | 기본 제공 브라우저 | Chrome | Opera  | Microsoft Edge | UC 브라우저 | Firefox |
| -- |:-------------:| -----:|-----:|-----:|-----:|-----:|
| Nexus 4 (API 17) | pass | pass |해당 없음 |해당 없음 |해당 없음 |해당 없음 |
| Samsung S7 (API 25) | 전달을 | pass | pass | pass | 실패 |pass |
| Huawei (API 26) |pass * * | pass | 실패 | pass | pass |pass |
| Vivo (API 26) |pass|pass|pass|pass|pass|실패|
| 픽셀 2 (API 26) |pass | pass | pass | pass | 실패 |pass |
| Oppo | pass | 해당 사항 없음 * * * |해당 없음  |해당 없음 |해당 없음 | 해당 없음|
| OnePlus (API 25) |pass | pass | pass | pass | 실패 |pass |
| Nexus (API 28) |pass | pass | pass | pass | 실패 |pass |
|MI | pass | pass | pass | pass | 실패 |pass |

\* Samsung의 기본 제공 브라우저는 Samsung Internet입니다.  
\* * Huawei의 기본 제공 브라우저는 Huawei Browser입니다.  
기본 브라우저는 Oppo 장치 설정 내에서 변경할 수 없습니다.
