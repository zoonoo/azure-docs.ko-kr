---
title: 권한 부여 에이전트 및 이를 활성화하는 방법 | Azure
description: MICROSOFT 인증 라이브러리(MSAL)를 사용하면 Android 앱에서 사용할 수 있는 다양한 권한 부여 에이전트와 이를 활성화하는 방법에 대해 알아봅니다.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/05/2019
ms.author: marsma
ms.reviewer: shoatman, brianmel, hahamil
ms.custom: aaddev
ms.openlocfilehash: 4f1b3fc5b60069cfa47d437e4341ded141204418
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77085322"
---
# <a name="authorization-agents-android"></a>권한 부여 에이전트(Android)

이 문서에서는 MSAL(Microsoft 인증 라이브러리)에서 앱에서 사용할 수 있는 다양한 권한 부여 에이전트와 이를 사용하도록 설정하는 방법에 대해 설명합니다.

권한 부여 에이전트에 대한 특정 전략을 선택하는 것은 선택 사항이며 앱이 사용자 지정할 수 있는 추가 기능을 나타냅니다. 대부분의 앱은 MSAL 기본값을 [사용합니다(Android MSAL 구성 파일 이해를](msal-configuration.md) 참조하여 다양한 기본값을 확인하세요).

MSAL은 `WebView`을 사용하거나 시스템 브라우저를 사용하여 권한 부여를 지원합니다.  아래 이미지는 CustomTabs 또는 `WebView`CustomTabs가 있거나 사용자 지정 탭이 없는 시스템 브라우저를 사용하여 어떻게 보이는지 보여줍니다.

![MSAL 로그인 예제](./media/authorization-agents/sign-in-ui.jpg)

## <a name="single-sign-in-implications"></a>단일 로그인 의미

기본적으로 MSAL과 통합된 응용 프로그램은 시스템 브라우저의 사용자 지정 탭을 사용하여 권한을 부여합니다. WebViews와 달리 사용자 지정 탭은 기본 시스템 브라우저와 쿠키 항아리를 공유하므로 사용자 지정 탭과 통합된 웹 또는 기타 네이티브 앱의 로그인 횟수를 줄일 수 있습니다.

응용 프로그램이 Microsoft `WebView` 인증자 또는 회사 포털 지원을 앱에 통합하지 않고 전략을 사용하는 경우 사용자는 장치 전체 또는 기본 앱과 웹 앱 간에 단일 사인온(SSO) 환경을 갖지 않습니다.

응용 프로그램이 Microsoft 인증자 또는 회사 포털 지원과 함께 MSAL을 사용하는 경우 사용자가 앱 중 하나를 사용하여 활성 로그인을 하는 경우 응용 프로그램 전체에서 SSO 환경을 사용할 수 있습니다.

## <a name="webview"></a>웹 보기

인앱 WebView를 사용하려면 MSAL에 전달되는 앱 구성 JSON에 다음 줄을 넣습니다.

```json
"authorization_user_agent" : "WEBVIEW"
```

인앱 `WebView`에서 를 사용하는 경우 사용자가 앱에 직접 로그인합니다. 토큰은 앱의 샌드박스 안에 보관되며 앱의 쿠키 항아리 외부에서 사용할 수 없습니다. 따라서 앱이 인증자 또는 회사 포털과 통합되지 않는 한 사용자는 응용 프로그램 전체에서 SSO 환경을 사용할 수 없습니다.

그러나 `WebView` 로그인 UI에 대 한 모양과 느낌을 사용자 지정 하는 기능을 제공 합니다. 이 사용자 지정을 수행하는 방법에 대한 자세한 내용은 [Android WebViews를](https://developer.android.com/reference/android/webkit/WebView) 참조하십시오.

## <a name="default-browser-plus-custom-tabs"></a>기본 브라우저 및 사용자 지정 탭

기본적으로 MSAL은 브라우저와 [사용자 지정 탭](https://developer.chrome.com/multidevice/android/customtabs) 전략을 사용합니다. 사용자 지정 구성 파일에서 다음 JSON `DEFAULT` 구성을 사용하여 향후 릴리스에서 변경되지 않도록 이 전략을 명시적으로 나타낼 수 있습니다.

```json
"authorization_user_agent" : "BROWSER"
```

이 방법을 사용하여 장치의 브라우저를 통해 SSO 환경을 제공합니다. MSAL은 공유 쿠키 jar을 사용하여 MSAL에서 설정한 지속 세션 쿠키를 사용하여 다른 네이티브 앱 또는 웹 앱이 장치에서 SSO를 달성할 수 있도록 합니다.

## <a name="browser-selection-heuristic"></a>브라우저 선택 추론

MSAL은 광범위한 Android 휴대폰에서 사용할 정확한 브라우저 패키지를 지정하는 것이 불가능하기 때문에 MSAL은 최상의 교차 장치 SSO를 제공하려고 하는 브라우저 선택 추론을 구현합니다.

MSAL은 장치에 설치된 브라우저의 전체 목록을 검색하여 사용할 브라우저를 선택합니다. 목록은 사용자의 기본 설정을 간접적으로 반영하는 패키지 관리자가 반환한 순서대로 표시됩니다. 예를 들어 기본 브라우저(설정된 경우)가 목록의 첫 번째 항목입니다. 목록의 _첫 번째_ 브라우저는 사용자 지정 탭을 지원하는지 여부에 관계없이 선택됩니다. 브라우저가 사용자 지정 탭을 지원하는 경우 MSAL은 사용자 지정 탭을 시작합니다. 사용자 `WebView` 지정 탭은 인앱에 더 가깝게 보이고 느끼고 기본 UI 사용자 지정을 허용합니다. 자세한 내용은 [안드로이드에서 사용자 정의 탭을](https://developer.chrome.com/multidevice/android/customtabs) 참조하십시오.

장치에 브라우저 패키지가 없는 경우 MSAL은 인앱 `WebView`에서 를 사용합니다.

브라우저 목록의 브라우저 순서는 운영 체제에 의해 결정됩니다. 가장 선호되는 것부터 최소한으로 순서대로 정렬됩니다. 장치 기본 설정이 변경되지 않으면 SSO 환경을 보장하기 위해 로그인할 때마다 동일한 브라우저를 시작해야 합니다.

> [!NOTE]
> 다른 브라우저가 기본값으로 설정된 경우 MSAL은 더 이상 항상 크롬을 선호하지 않습니다. 예를 들어 Chrome과 다른 브라우저가 모두 사전 설치된 기기에서 MSAL은 사용자가 설정한 브라우저를 기본값으로 사용합니다.

### <a name="tested-browsers"></a>테스트된 브라우저

다음 브라우저를 테스트하여 구성 파일에 `"redirect_uri"` 지정된 브라우저로 올바르게 리디렉션되는지 확인합니다.

| | 내장 브라우저 | Chrome | Opera  | Microsoft Edge | UC 브라우저 | Firefox |
| -- |:-------------:| -----:|-----:|-----:|-----:|-----:|
| 넥서스 4 (API 17) | pass | pass |적용할 수 없음 |적용할 수 없음 |적용할 수 없음 |적용할 수 없음 |
| 삼성 S7 (API 25) | 패스* | pass | pass | pass | fail |pass |
| 화웨이 (API 26) |패스 ** | pass | fail | pass | pass |pass |
| 비보 (API 26) |pass|pass|pass|pass|pass|fail|
| 픽셀 2(API 26) |pass | pass | pass | pass | fail |pass |
| Oppo | pass | 해당되지 않음*** |적용할 수 없음  |적용할 수 없음 |적용할 수 없음 | 적용할 수 없음|
| 원플러스 (API 25) |pass | pass | pass | pass | fail |pass |
| 넥서스 (API 28) |pass | pass | pass | pass | fail |pass |
|MI | pass | pass | pass | pass | fail |pass |

* 삼성의 내장 브라우저는 삼성 인터넷입니다.  
** 화웨이의 내장 브라우저는 화웨이 브라우저입니다.  
기본 브라우저는 Oppo 장치 설정 내에서 변경할 수 없습니다.
