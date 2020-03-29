---
title: 마이크로소프트 에지 (MSAL.js)& 인터넷 익스플로러에 문제 | Azure
titleSuffix: Microsoft identity platform
description: 인터넷 익스플로러 및 Microsoft Edge 브라우저에서 자바스크립트용 Microsoft 인증 라이브러리(MSAL.js)를 사용할 때 문제에 대해 알아봅니다.
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: troubleshooting
ms.workload: identity
ms.date: 05/16/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 5ae2dee68ec0da8e8a00d4f01583461462bc196c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76696098"
---
# <a name="known-issues-on-internet-explorer-and-microsoft-edge-browsers-msaljs"></a>인터넷 익스플로러와 마이크로 소프트 에지 브라우저에 알려진 문제 (MSAL.js)

## <a name="issues-due-to-security-zones"></a>보안 영역으로 인한 문제
우리는 IE와 마이크로 소프트 에지에서 인증 문제의 여러 보고서를했다 (마이크로 소프트 에지 브라우저 버전의 업데이트 이후 *40.15063.0.0).* 우리는 이들을 추적하고 있으며 Microsoft Edge 팀에 통보했습니다. Microsoft Edge는 해결 방법을 사용하지만 자주 발생하는 문제와 구현할 수 있는 가능한 해결 방법에 대한 설명은 다음과 같습니다.

### <a name="cause"></a>원인
이러한 문제의 대부분에 대 한 원인은 다음과 같습니다. 세션 저장소와 로컬 저장소는 Microsoft Edge 브라우저의 보안 영역으로 분할됩니다. 이 특정 버전의 Microsoft Edge에서는 응용 프로그램이 영역 간에 리디렉션되면 세션 저장소 및 로컬 저장소가 지워집니다. 특히 세션 저장소는 일반 브라우저 탐색에서 지워지고 세션과 로컬 저장소는 브라우저의 InPrivate 모드에서 지워집니다. MSAL.js는 세션 저장소에 특정 상태를 저장하고 인증 흐름 중에 이 상태를 확인하는 데 의존합니다. 세션 저장소를 선택 취소하면 이 상태가 손실되어 환경이 손상됩니다.

### <a name="issues"></a>문제

- **인증 하는 동안 무한 리디렉션 루프 및 페이지 다시 로드**합니다. 사용자가 Microsoft Edge에서 응용 프로그램에 로그인하면 AAD 로그인 페이지에서 다시 리디렉션되고 무한 리디렉션 루프에 갇혀 페이지 가 다시 로드됩니다. 일반적으로 세션 저장소에 `invalid_state` 오류가 발생합니다.

- **무한 은 토큰 루프 및 AADSTS50058 오류를 획득합니다.** Microsoft Edge에서 실행 중인 응용 프로그램이 리소스에 대한 토큰을 획득하려고 하면 네트워크 추적에서 AAD의 다음과 같은 오류와 함께 수집 토큰 호출의 무한 루프에 응용 프로그램이 갇아닐 수 있습니다.

    `Error :login_required; Error description:AADSTS50058: A silent sign-in request was sent but no user is signed in. The cookies used to represent the user's session were not sent in the request to Azure AD. This can happen if the user is using Internet Explorer or Edge, and the web app sending the silent sign-in request is in different IE security zone than the Azure AD endpoint (login.microsoftonline.com)`

- **팝업 창을 닫지 않거나 인증을 위해 팝업을 통해 로그인을 사용할 때 붙어**있습니다. Microsoft Edge 또는 IE(InPrivate)의 팝업 창을 통해 인증할 때 자격 증명을 입력하고 로그인한 후 보안 영역의 여러 도메인이 탐색에 관여하는 경우 MSAL.js가 핸들을 잃어도 팝업 창이 닫히지 않습니다. 팝업 창에 표시됩니다.  

### <a name="update-fix-available-in-msaljs-023"></a>업데이트: MSAL.js 0.2.3에서 사용할 수 있는 수정 사항
인증 리디렉션 루프 문제에 대한 수정 은 [MSAL.js 0.2.3에서](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases)릴리스되었습니다 . MSAL.js 구성의 플래그를 `storeAuthStateInCookie` 활성화하여 이 수정 프로그램을 활용합니다. 기본적으로 이 플래그는 false로 설정됩니다.

플래그를 `storeAuthStateInCookie` 사용하도록 설정하면 MSAL.js는 브라우저 쿠키를 사용하여 인증 흐름의 유효성 검사에 필요한 요청 상태를 저장합니다.

> [!NOTE]
> 이 수정 프로그램은 아직 msal 각도 및 msal 각도 래퍼에 사용할 수 없습니다. 이 수정 프로그램은 팝업 창에서 문제를 해결 하지 않습니다.

아래에서 해결 방법을 사용하십시오.

#### <a name="other-workarounds"></a>기타 해결 해결
이러한 해결 방법을 채택하기 전에 특정 버전의 Microsoft Edge 브라우저에서만 문제가 발생하는지 테스트하고 다른 브라우저에서 작동하는지 테스트해야 합니다.  
1. 이러한 문제를 해결하는 첫 번째 단계로 응용 프로그램 도메인 및 인증 흐름의 리디렉션과 관련된 다른 사이트가 브라우저의 보안 설정에서 신뢰할 수 있는 사이트로 추가되어 동일한 보안 영역에 속하도록 합니다.
이렇게 하려면 다음 단계를 따르십시오.
    - **인터넷 익스플로러를** 열고 오른쪽 상단 모서리에있는 **설정** (기어 아이콘)을 클릭
    - **인터넷 옵션** 선택
    - **보안** 탭 선택
    - 신뢰할 **수 있는 사이트** 옵션에서 **사이트** 단추를 클릭하고 열리는 대화 상자에 URL을 추가합니다.

2. 앞에서 설명한 것처럼 일반 탐색 중에 세션 저장소만 지워지므로 대신 로컬 저장소를 사용하도록 MSAL.js를 구성할 수 있습니다. MSAL을 `cacheLocation` 초기화하는 동안 구성 매개 변수로 설정할 수 있습니다.

세션 및 로컬 저장소가 모두 지워지므로 InPrivate 브라우징에 대한 문제는 해결되지 않습니다.

## <a name="issues-due-to-popup-blockers"></a>팝업 차단으로 인한 문제

예를 들어 다단계 인증 중에 두 번째 팝업이 발생하는 경우와 같은 IE 또는 Microsoft Edge에서 팝업이 차단되는 경우가 있습니다. 한 번 또는 항상 팝업을 허용 하는 브라우저에서 경고를 얻을 것 이다. 허용하도록 선택하면 브라우저가 팝업 창을 자동으로 열고 핸들을 `null` 반환합니다. 따라서 라이브러리에는 창에 대한 핸들이 없으며 팝업 창을 닫을 방법이 없습니다. 팝업 창을 자동으로 열지 않으므로 팝업을 허용하라는 메시지가 표시되면 Chrome에서도 동일한 문제가 발생하지 않습니다.

해결 **방법으로**개발자는 이 문제를 피하기 위해 앱을 사용하기 전에 IE 및 Microsoft Edge의 팝업을 허용해야 합니다.

## <a name="next-steps"></a>다음 단계
[인터넷 익스플로러에서 MSAL.js 사용에](msal-js-use-ie-browser.md)대해 자세히 알아보십시오.
