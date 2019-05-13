---
title: 클라이언트 응용 프로그램 (JavaScript 용 Microsoft Authentication Library)를 초기화 합니다. | Azure
description: JavaScript (MSAL.js) 용 Microsoft Authentication Library를 사용 하 여 클라이언트 응용 프로그램을 초기화 하는 방법을 알아봅니다.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: celested
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/12/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7990566ca9cd93e79b8356cfd15fda03a7469695
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65138312"
---
# <a name="initialize-client-applications-using-msaljs"></a>MSAL.js를 사용 하 여 클라이언트 응용 프로그램 초기화
이 문서에서는 JavaScript (MSAL.js)에 대 한 사용자 에이전트 응용 프로그램의 인스턴스를 사용 하 여 초기화 하는 동안 Microsoft 인증 라이브러리를 설명합니다. 사용자 에이전트 응용 프로그램은 웹 브라우저와 같은 사용자 에이전트에서 실행 되는 클라이언트 코드는 공용 클라이언트 응용 프로그램의 폼입니다. 이러한 클라이언트는 브라우저 컨텍스트가 공개적으로 액세스할 수 있으므로 암호를 저장 하지 마십시오. 클라이언트 응용 프로그램 유형 및 응용 프로그램 구성 옵션에 대 한 자세한 내용은 참조는 [개요](msal-client-applications.md)합니다.

## <a name="prerequisites"></a>필수 조건
응용 프로그램을 초기화 하기 전에 먼저 [Azure portal을 사용 하 여 등록](scenario-spa-app-registration.md) Microsoft id 플랫폼을 사용 하 여 앱을 통합 될 수 있도록 합니다. 등록이 완료 되 면 다음 정보를 (Azure portal에서 찾을 수 있습니다) 해야 합니다.

- 클라이언트 ID (응용 프로그램에 대 한 GUID를 나타내는 문자열)
- Id 공급자 URL (명명 된 인스턴스) 및 응용 프로그램에 대 한 로그인 대상 그룹입니다. 이 두 매개 변수를 기관으로 통칭 합니다.
- 조직 (또한 명명 된 단일 테 넌 트 응용 프로그램)에 대해 전적으로 기간 업무 응용 프로그램을 작성 하는 경우 테 넌 트 ID입니다.
- 웹 앱에 대 한 보안 토큰을 사용 하 여 응용 프로그램에 id 공급자가 반환 하는 위치 redirectUri도 설정 해야 합니다.

## <a name="initializing-applications"></a>응용 프로그램 초기화

MSAL.js 일반 JavaScript/Typescript 응용 프로그램에서 다음과 같이 사용할 수 있습니다. MSAL 인증 컨텍스트를 인스턴스화하고 초기화 `UserAgentApplication` 구성 개체를 사용 하 여 합니다. MSAL.js 초기화에 필요한 최소 구성에는 응용 프로그램 등록 포털에서 가져와야 하는 응용 프로그램의 clientID입니다.

인증 방법을 사용 하 여 흐름을 리디렉션에 대 한 (`loginRedirect` 하 고 `acquireTokenRedirect`), 성공 또는 오류를 통해 콜백을 명시적으로 등록 해야 `handleRedirectCallback()` 메서드. 팝업 환경과 메서드 마찬가지로 리디렉션 흐름 프라미스를 반환 하지 않는 있으므로이 필요 합니다.

```javascript
// Configuration object constructed
const config = {
    auth: {
        clientId: “abcd-ef12-gh34-ikkl-ashdjhlhsdg”
    }
}

// create UserAgentApplication instance
const myMSALObj = new UserAgentApplication(config);

function authCallback(error, response) {
    //handle redirect response
}

// (optional when using redirect methods) register redirect call back for Success or Error
myMSALObj.handleRedirectCallback(authCallback);
```

MSAL.js 단일 인스턴스를 구성 하도록 설계 되었습니다는 `UserAgentApplication` 단일 인증 컨텍스트를 나타내는입니다. 브라우저에서 충돌 하는 캐시 항목 및 동작을 하면 여러 인스턴스 권장 되지 않습니다.

## <a name="configuration-options"></a>구성 옵션

MSAL.js 구성이 아래에 표시 된 개체의 인스턴스를 만들고 사용할 수 있는 구성 가능한 옵션의 그룹화를 제공 `UserAgentApplication`합니다.

```javascript
type storage = "localStorage" | "sessionStorage";

// Protocol Support
export type AuthOptions = {
    clientId: string;
    authority?: string;
    validateAuthority?: boolean;
    redirectUri?: string | (() => string);
    postLogoutRedirectUri?: string | (() => string);
    navigateToLoginRequestUrl?: boolean;
};

// Cache Support
export type CacheOptions = {
    cacheLocation?: CacheLocation;
    storeAuthStateInCookie?: boolean;
};

// Library support
export type SystemOptions = {
    logger?: Logger;
    loadFrameTimeout?: number;
    tokenRenewalOffsetSeconds?: number;
};

// Developer App Environment Support
export type FrameworkOptions = {
    isAngular?: boolean;
    unprotectedResources?: Array<string>;
    protectedResourceMap?: Map<string, Array<string>>;
};

// Configuration Object
export type Configuration = {
    auth: AuthOptions,
    cache?: CacheOptions,
    system?: SystemOptions,
    framework?: FrameworkOptions
};
```

다음은 구성 개체에서 현재 지원 되는 구성 가능한 옵션의 전체 집합입니다.

- **clientID**: 필수 사항입니다. 응용 프로그램의 clientID, 가져와야이 응용 프로그램 등록 포털에서 합니다.

- **authority**: 선택 사항입니다. MSAL에서 토큰을 요청할 수 있는 디렉터리를 나타내는 URL입니다. 기본값은 `https://login.microsoftonline.com/common`입니다.
    * Azure AD에서는 https://의&lt;인스턴스&gt;/&lt;audience&gt;여기서 &lt;인스턴스&gt; identity 공급자 도메인 (예를 들어 `https://login.microsoftonline.com`) 및 &lt;청중&gt; 로그인 대상 식별자입니다. 다음 값을 수 있습니다.
        * `https://login.microsoftonline.com/<tenant>`-테 넌 트가 테 넌 트, 예: contoso.onmicrosoft.com 또는 GUID를 나타내는 연결 된 도메인 인지는 `TenantID` 특정 조직의 사용자를 로그인에 사용 되는 디렉터리의 속성입니다.
        * `https://login.microsoftonline.com/common`-회사 및 학교 계정 또는 개인 Microsoft 계정을 사용 하 여 사용자를 로그인 하는 데 사용 합니다.
        * `https://login.microsoftonline.com/organizations/`-회사 및 학교 계정 사용 하 여 사용자를 로그인 하는 데 사용 합니다.
        * `https://login.microsoftonline.com/consumers/` -(라이브)만 개인 Microsoft 계정 사용 하 여 사용자를 로그인 하는 데 사용 합니다.
    * Azure AD B2C에서는 폼의 `https://<instance>/tfp/<tenant>/<policyName>/`테 넌 트 Azure AD B2C 테 넌 트의 이름은 여기서 인스턴스는 Azure AD B2C 도메인, policyName B2C 정책 적용의 이름입니다.


- **validateAuthority**: 선택 사항입니다.  토큰의 발급자의 유효성을 검사 합니다. 기본값은 `true`입니다. B2C 응용 프로그램에 대 한 기관 값 이라고 하며 정책에 따라 다를 수 있으므로 기관 유효성 검사 작동 하지 않으며로 설정 해야 `false`합니다.

- **redirectUri**: 선택 사항입니다.  앱이 인증 응답을 보내고 받을 수 있는 앱의 리디렉션 URI입니다. URL로 인코딩되어야 한다는 점을 제외하고 포털에서 등록한 리디렉션 URI 중 하나와 정확히 일치해야 합니다. 기본값은 `window.location.href`입니다.

- **postLogoutRedirectUri**: 선택 사항입니다.  사용자를 리디렉션합니다 `postLogoutRedirectUri` 후 로그 아웃 합니다. 기본값은 `redirectUri`입니다.

- **navigateToLoginRequestUrl**: 선택 사항입니다. 로그인 한 후 시작 페이지 기본 탐색을 해제할 수 있습니다. 기본값은 True입니다. 이 리디렉션 흐름에 대해서만 사용 됩니다.

- **cacheLocation**: 선택 사항입니다.  브라우저 저장소를 설정 `localStorage` 또는 `sessionStorage`합니다. 기본값은 `sessionStorage`입니다.

- **storeAuthStateInCookie**: 선택 사항입니다.  이 플래그에 대 한 수정으로 MSAL.js v0.2.2에 도입 된 합니다 [인증 루프 문제](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#1-issues-due-to-security-zones) Microsoft Internet Explorer와 Microsoft Edge에서. 플래그를 사용 하도록 설정 `storeAuthStateInCookie` 활용이 true로 설정 하면 해결 하려면. 이 설정 되 면 MSAL.js 브라우저 쿠키에서 인증 흐름의 유효성을 검사 하는 데 필요한 인증 요청 상태를 저장 합니다. 기본적으로이 플래그 설정 `false`합니다.

- **logger**: 선택 사항입니다.  사용 하 고 사용자 지정 방식으로 로그를 게시 하는 개발자가 제공할 수 있는 콜백 인스턴스를 사용 하 여로 거 개체입니다. 전달로 거 개체에 대 한 세부 정보를 참조 하세요 [msal.js를 사용 하 여 로깅](msal-logging.md)합니다.

- **loadFrameTimeout**: 선택 사항입니다.  Azure AD에서 토큰 갱신 응답 해야 간주 되기까지의 비활성 시간 (밀리초)의 제한 시간이 초과 되었습니다. 기본값은 6 초입니다.

- **tokenRenewalOffsetSeconds**: 선택 사항입니다. 오프셋 만료 될 때까지 토큰을 갱신 하는 데 필요한 기간을 설정 하는 시간 (밀리초)의 수입니다. 기본값은 300 밀리초입니다.

이러한 MSAL Angular 래퍼 라이브러리에서 전달 되 게 적용 됩니다.
- **unprotectedResources**: 선택 사항입니다.  보호 되지 않은 리소스는 Uri의 배열입니다. MSAL이이 URI는 나가는 요청 토큰을 연결 됩니다. 기본값은 `null`입니다.

- **protectedResourceMap**: 선택 사항입니다.  이 리소스 MSAL에 의해 자동으로 웹 API 호출에서 액세스 토큰을 연결 하는 데 사용 되는 범위를 매핑하는 것입니다. 리소스에 대 한 단일 액세스 토큰을 가져옵니다. 사용자 특정 리소스 경로 다음과 같이 매핑할 수 있도록: {"https://graph.microsoft.com/v1.0/me", ["user.read"]}, 또는 리소스의 앱 URL: {"https://graph.microsoft.com/", ["user.read", "mail.send"]}. CORS 호출에 대 한 필수입니다. 기본값은 `null`입니다.
