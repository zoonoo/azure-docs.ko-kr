---
title: 알려진 문제 브라우저 (Microsoft Authentication Library for JavaScript) | Azure
description: 에 대해 알아봅니다 (MSAL.js) JavaScript 용 Microsoft Authentication Library를 사용 하는 경우에 문제를 알고 있는 Internet Explorer 및 Microsoft Edge 브라우저를 사용 하 여.
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/16/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: c57ed956ec50c8bac26720a27894c07353928336
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65873902"
---
# <a name="known-issues-on-internet-explorer-and-microsoft-edge-browsers-with-msaljs"></a>MSAL.js 사용 하 여 Internet Explorer와 Microsoft Edge 브라우저에서 알려진된 문제

## <a name="issues-due-to-security-zones"></a>보안 영역으로 인 한 문제
IE와 Microsoft Edge에서 인증을 사용 하 여 문제의 여러 보고서 했습니다 (업데이트 이후 합니다 *40.15063.0.0 하려면 Microsoft Edge 브라우저 버전*). 이러한 추적 하 고 Microsoft Edge 팀 알림을 했습니다. Microsoft Edge 해상도에 작동 하는 동안 자주 발생 하는 문제 및 가능한 해결 방법을 구현할 수 있는 설명은 다음과 같습니다.

### <a name="cause"></a>원인
대부분 이러한 문제의 원인은 아래와 같습니다. 세션 저장소와 로컬 저장소는 Microsoft Edge 브라우저에서 보안 영역으로 분할 됩니다. 이 특정 버전의 Microsoft Edge, 영역, 응용 프로그램을 리디렉션할 세션 저장소와 로컬 저장소 취소 됩니다. 특히, 세션 저장소는 일반 브라우저 탐색에서 선택을 취소 하 고 브라우저의 InPrivate 모드에서 세션 및 로컬 저장소를 모두 지워집니다. MSAL.js 세션 저장소의 특정 상태를 저장 하며 인증 흐름 중이 상태를 확인 합니다. 세션 저장소의 선택을 취소 하는 경우이 상태 손실 되 고 따라서 손상 된 환경에서 발생 합니다.

### <a name="issues"></a>문제

- **인증 하는 동안 무한 리디렉션 루프 및 페이지를 다시 로드**합니다. 사용자가 Microsoft Edge에서 응용 프로그램에 로그인 하는 경우 AAD 로그인 페이지에서 다시 리디렉션됩니다 하며 반복된 페이지를 다시 로드의 결과 무한 리디렉션 루프에서 중단 됩니다. 일반적으로 포함 되어이 `invalid_state` 세션 저장소에 오류가 있습니다.

- **무한 루프 토큰 및 AADSTS50058 오류 획득**합니다. Microsoft Edge에서 실행 중인 응용 프로그램에 리소스에 대 한 토큰을 획득 하려고 응용 프로그램 acquire 토큰 호출이 다음 오류와 함께 AAD에서 네트워크 추적에서의 무한 루프에 빠질 수 있습니다.

    `Error :login_required; Error description:AADSTS50058: A silent sign-in request was sent but no user is signed in. The cookies used to represent the user's session were not sent in the request to Azure AD. This can happen if the user is using Internet Explorer or Edge, and the web app sending the silent sign-in request is in different IE security zone than the Azure AD endpoint (login.microsoftonline.com)`

- **팝업 창이 닫히지 않습니다 또는 팝업을 통해 로그인을 사용 하 여 인증 하는 경우 걸려**합니다. MSAL.js에 대 한 핸들을 손실 때문에 팝업 창이 닫히지 않은 자격 증명을 입력 하 고 탐색에서 보안 영역에 걸쳐 여러 도메인이 관련 된 경우 로그인 팝업 창에서 Microsoft Edge 또는 IE(InPrivate)를 통해 인증 하는 경우 팝업 창입니다.  

    Microsoft Edge 문제 추적기에서 이러한 문제에 대 한 링크는 다음과 같습니다.  
    - [Bug 13861050](https://developer.microsoft.com/en-us/microsoft-edge/platform/issues/13861050/)
    - [Bug 13861663](https://developer.microsoft.com/en-us/microsoft-edge/platform/issues/13861663/)

### <a name="update-fix-available-in-msaljs-023"></a>업데이트: MSAL.js 0.2.3에서에서 사용할 수 있는 수정
인증 리디렉션 루프 문제에 출시 된에 대 한 픽스 [MSAL.js 0.2.3](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases)합니다. 플래그를 사용 하도록 설정 `storeAuthStateInCookie` 이 수정 프로그램을 활용 하려면 MSAL.js 구성에서 합니다. 기본적으로이 플래그는 false로 설정 됩니다.

경우는 `storeAuthStateInCookie` 플래그를 설정, MSAL.js 브라우저 쿠키를 사용 하 여 인증 흐름의 유효성 검사에 필요한 요청 상태를 저장 합니다.

> [!NOTE]
> 이 문제가 해결 되지 않습니다 msal angular 및 msal angularjs 래퍼에 사용할 수 있습니다. 이 문제가 해결 팝업 windows 사용 하 여 문제를 다루지 않습니다.

아래 해결 방법을 사용 합니다.

#### <a name="other-workarounds"></a>다른 해결 방법
에 문제가 발생 한 특정 버전의 Microsoft Edge 브라우저 및 다른 브라우저에서 작동에만 이러한 해결 방법을 채택 하기 전에 테스트 해야 합니다.  
1. 이러한 문제를 해결 하려면 첫 번째 단계로, 확인 하는 응용 프로그램 도메인에서 인증 흐름의 리디렉션에 관련 된 다른 사이트는 동일한 보안 영역에 속해 있도록 브라우저의 보안 설정에 신뢰할 수 있는 사이트로 추가 됩니다.
이렇게 하려면 다음 단계를 따르십시오.
    - 열기 **Internet Explorer** 를 클릭 합니다 **설정** (기어 아이콘) 오른쪽 위 모서리에 있는
    - 선택 **인터넷 옵션**
    - 선택 된 **보안** 탭
    - 아래는 **신뢰할 수 있는 사이트** 옵션을 클릭 합니다 **사이트** 단추 및 열리는 대화 상자에서 Url을 추가 합니다.

2. 세션에만 이후 일반 탐색 하는 동안 저장소 지워집니다 전에 언급 했 듯이 MSAL.js 로컬 저장소를 대신 사용 하도록 구성할 수 있습니다. 으로 설정할 수 있습니다는 `cacheLocation` MSAL을 초기화 하는 동안 구성 매개 변수입니다.

Note이 세션 및 로컬 저장소를 지운 후에 InPrivate 브라우징에 대 한 문제가 해결 되지 않습니다.

## <a name="issues-due-to-popup-blockers"></a>팝업 차단 기능으로 인 한 문제

IE 또는 예를 들어 두 번째 팝업 multi-factor authentication 인증 중에 발생 하는 경우 Microsoft Edge에서 팝업 차단 하는 데는 경우가 있습니다. 한 번만 나타나거나 항상 팝업을 허용 하려면 브라우저에서 경고가 표시 됩니다. 허용 하려는 경우 브라우저 팝업 창이 자동으로 열리고 반환을 `null` 를 처리 합니다. 결과적으로, 라이브러리의 창 핸들을 없고 팝업 창을 닫으려면 방법이 있습니다. 팝업 창이 자동으로 열리지 않으면 때문에 팝업을 허용 하 라는 메시지를 표시 하는 경우 Chrome에서 동일한 문제가 발생 하지 않습니다.

로 **해결 방법**, 개발자가 앱을 사용 하 여이 문제를 방지 하려면 시작 하기 전에 IE와 Microsoft Edge에서 팝업을 허용 해야 합니다.

## <a name="next-steps"></a>다음 단계
에 대해 자세히 알아보세요 [Internet Explorer에서 사용 하 여 MSAL.js](msal-js-use-ie-browser.md)합니다.
