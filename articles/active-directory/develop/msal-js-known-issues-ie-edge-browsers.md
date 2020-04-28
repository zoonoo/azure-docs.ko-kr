---
title: Internet Explorer & Microsoft Edge (MSAL .js)에서 발생 하는 문제 | Microsoft
titleSuffix: Microsoft identity platform
description: Internet Explorer 및 Microsoft Edge 브라우저에서 JavaScript 용 Microsoft 인증 라이브러리 (MSAL)를 사용 하는 경우의 문제에 대해 알아봅니다.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "76696098"
---
# <a name="known-issues-on-internet-explorer-and-microsoft-edge-browsers-msaljs"></a>Internet Explorer 및 Microsoft Edge 브라우저의 알려진 문제 (MSAL .js)

## <a name="issues-due-to-security-zones"></a>보안 영역으로 인 한 문제
*Microsoft edge 브라우저 버전을 40.15063.0.0로*업데이트 한 후 IE 및 microsoft edge에서 인증 관련 문제에 대 한 여러 보고서가 있습니다. Microsoft Edge 팀에 알려 주는 Microsoft Edge 팀을 추적 하 고 있습니다. Microsoft Edge는 해결 방법으로 작동 하지만, 여기에는 자주 발생 하는 문제 및 구현할 수 있는 가능한 해결 방법에 대 한 설명이 나와 있습니다.

### <a name="cause"></a>원인
이러한 문제의 대부분은 다음과 같습니다. 세션 저장소와 로컬 저장소는 Microsoft Edge 브라우저의 보안 영역에 의해 분할 됩니다. 이 특정 버전의 Microsoft Edge에서 응용 프로그램이 영역 간에 리디렉션되는 경우 세션 저장소와 로컬 저장소가 지워집니다. 특히 세션 저장소는 일반 브라우저 탐색에서 제거 되며 세션 및 로컬 저장소는 모두 브라우저의 InPrivate 모드에서 지워집니다. MSAL는 세션 저장소에 특정 상태를 저장 하며 인증 흐름 중에이 상태를 확인 합니다. 세션 저장소를 지우면이 상태가 손실 되므로 환경이 손상 됩니다.

### <a name="issues"></a>문제

- **인증 하는 동안 무한 리디렉션 루프 및 페이지 다시 로드** 사용자가 Microsoft Edge에서 응용 프로그램에 로그인 하면 해당 사용자는 AAD 로그인 페이지에서 다시 리디렉션되고 무한 리디렉션 루프에서 중단 되어 페이지 다시 로드 반복이 발생 합니다. 일반적으로 세션 저장소에 `invalid_state` 오류가 수반 됩니다.

- **토큰 루프 및 AADSTS50058 오류를 무한 획득**합니다. Microsoft Edge에서 실행 되는 응용 프로그램에서 리소스에 대 한 토큰을 얻으려고 시도 하는 경우, 네트워크 추적에서 AAD의 다음 오류와 함께 토큰 획득 호출의 무한 루프에서 응용 프로그램이 중단 될 수 있습니다.

    `Error :login_required; Error description:AADSTS50058: A silent sign-in request was sent but no user is signed in. The cookies used to represent the user's session were not sent in the request to Azure AD. This can happen if the user is using Internet Explorer or Edge, and the web app sending the silent sign-in request is in different IE security zone than the Azure AD endpoint (login.microsoftonline.com)`

- **로그인을 사용 하 여 인증할 때 팝업 창이 닫히지 않거나 중단 됩니다**. 자격 증명을 입력 하 고 로그인 한 후에 Microsoft Edge 또는 IE (InPrivate)의 팝업 창을 통해 인증 하는 경우, 보안 영역에 있는 여러 도메인이 탐색에 포함 되는 경우 MSAL가 팝업 창에 대 한 핸들이 손실 되므로 팝업 창이 닫히지 않습니다.  

### <a name="update-fix-available-in-msaljs-023"></a>업데이트: MSAL .js 0.2.3에서 사용할 수 있는 수정
인증 리디렉션 루프 문제에 대 한 픽스는 [Msal .js 0.2.3](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases)에서 릴리스 되었습니다. 이 픽스를 `storeAuthStateInCookie` 활용 하려면 msal .js 구성에서 플래그를 사용 하도록 설정 합니다. 기본적으로이 플래그는 false로 설정 됩니다.

`storeAuthStateInCookie` 플래그를 사용 하는 경우 msal는 인증 흐름의 유효성 검사에 필요한 요청 상태를 저장 하기 위해 브라우저 쿠키를 사용 합니다.

> [!NOTE]
> 이 픽스는 msal 각도 및 msal angularjs 래퍼에는 아직 사용할 수 없습니다. 이 수정은 팝업 창에서 발생 하는 문제를 해결 하지 않습니다.

아래에서 해결 방법을 사용 합니다.

#### <a name="other-workarounds"></a>기타 해결 방법
문제가 특정 버전의 Microsoft Edge 브라우저 에서만 발생 하는지 테스트 하 고 이러한 해결 방법을 채택 하기 전에 다른 브라우저에서 작동 하는지 테스트 해야 합니다.  
1. 이러한 문제를 해결 하기 위한 첫 번째 단계로, 응용 프로그램 도메인, 및 인증 흐름의 리디렉션을 포함 하는 다른 모든 사이트를 브라우저의 보안 설정에서 신뢰할 수 있는 사이트로 추가 하 여 동일한 보안 영역에 속하도록 해야 합니다.
이렇게 하려면 다음 단계를 따르십시오.
    - **Internet Explorer** 를 열고 오른쪽 위 모서리에서 **설정** (기어 아이콘)을 클릭 합니다.
    - **인터넷 옵션** 선택
    - **보안** 탭을 선택 합니다.
    - **신뢰할 수 있는 사이트** 옵션에서 **사이트** 단추를 클릭 하 고 열리는 대화 상자에 url을 추가 합니다.

2. 앞서 언급 했 듯이 정기적으로 탐색 하는 동안 세션 저장소만 지우기 때문에 로컬 저장소를 대신 사용 하도록 MSAL를 구성할 수 있습니다. MSAL을 초기화 하는 `cacheLocation` 동안 config 매개 변수로 설정할 수 있습니다.

세션 및 로컬 저장소가 모두 지워지므로 InPrivate 브라우징에 대 한 문제가 해결 되지 않습니다.

## <a name="issues-due-to-popup-blockers"></a>팝업 차단으로 인 한 문제

예를 들어, IE 또는 Microsoft Edge에서 팝업이 차단 되는 경우가 있습니다. 예를 들어 multi-factor authentication에서 두 번째 팝업이 발생 하는 경우가 여기에 해당 합니다. 브라우저에서 팝업을 한 번 또는 항상 허용 하는 경고를 받게 됩니다. 허용 하도록 선택 하면 브라우저에서 팝업 창을 자동으로 열고 해당 핸들을 `null` 반환 합니다. 결과적으로 라이브러리에는 창에 대 한 핸들이 없으며 팝업 창을 닫을 수 있는 방법이 없습니다. 팝업 창이 자동으로 열리지 않으므로 팝업을 허용 하 라는 메시지가 표시 되 면 Chrome에서 동일한 문제가 발생 하지 않습니다.

이 문제를 방지 하려면 개발자가 앱 사용을 시작 하기 전에 IE 및 Microsoft Edge에서 **팝업을 허용**해야 합니다.

## <a name="next-steps"></a>다음 단계
[Internet Explorer에서 MSAL를 사용 하](msal-js-use-ie-browser.md)는 방법에 대해 자세히 알아보세요.
