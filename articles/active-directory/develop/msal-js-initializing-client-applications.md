---
title: MSAL.js 클라이언트 앱 초기화 | Azure
titleSuffix: Microsoft identity platform
description: 자바스크립트용 Microsoft 인증 라이브러리(MSAL.js)를 사용하여 클라이언트 응용 프로그램을 초기화하는 방법에 대해 알아봅니다.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/12/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: fbd700c787a844fa7538ed198f76ed5c06af2c28
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/10/2020
ms.locfileid: "81010157"
---
# <a name="initialize-client-applications-using-msaljs"></a>MSAL.js를 사용하여 클라이언트 응용 프로그램 초기화
이 문서에서는 사용자 에이전트 응용 프로그램의 인스턴스를 사용 하 여 자바 스크립트 (MSAL.js)에 대 한 Microsoft 인증 라이브러리를 초기화 설명 합니다. 사용자 에이전트 응용 프로그램은 웹 브라우저와 같은 사용자 에이전트에서 클라이언트 코드가 실행되는 공용 클라이언트 응용 프로그램의 한 형태입니다. 이러한 클라이언트는 브라우저 컨텍스트에 공개적으로 액세스할 수 있으므로 비밀을 저장하지 않습니다. 클라이언트 응용 프로그램 유형 및 응용 프로그램 구성 옵션에 대해 자세히 알아보려면 [개요를](msal-client-applications.md)참조하십시오.

## <a name="prerequisites"></a>사전 요구 사항
응용 프로그램을 초기화하기 전에 먼저 앱을 Microsoft ID 플랫폼과 통합할 수 있도록 [Azure 포털에 등록해야](scenario-spa-app-registration.md) 합니다. 등록 후 Azure 포털에서 찾을 수 있는 다음 정보가 필요할 수 있습니다.

- 클라이언트 ID(응용 프로그램에 대한 GUID를 나타내는 문자열)
- 인스턴스라는 ID 공급자 URL과 응용 프로그램의 로그인 대상입니다. 이러한 두 매개 변수를 통칭하여 기관이라고 합니다.
- 조직전용(단일 테넌트 응용 프로그램이라고도 함)에 대해서만 업무 용 응용 프로그램을 작성하는 경우 테넌트 ID입니다.
- 웹 앱의 경우 ID 공급자가 보안 토큰을 사용하여 응용 프로그램으로 반환되는 리디렉션Uri도 설정해야 합니다.

## <a name="initializing-applications"></a>응용 프로그램 초기화

일반 자바 스크립트 / 타이스크립트 응용 프로그램에서 다음과 같이 MSAL.js를 사용할 수 있습니다. 구성 개체를 인스턴스화하여 MSAL `UserAgentApplication` 인증 컨텍스트를 초기화합니다. MSAL.js를 초기화하는 데 필요한 최소 구성은 응용 프로그램 등록 포털에서 얻어야 하는 응용 프로그램의 clientID입니다.

리디렉션 흐름 (및)이`loginRedirect` `acquireTokenRedirect`있는 인증 방법의 경우 MSAL.js 1.2.x 이전에서 메서드를 통해 `handleRedirectCallback()` 성공 또는 오류에 대한 콜백을 명시적으로 등록해야 합니다. 이 리디렉션 흐름 은 팝업 환경의 방법으로 약속을 반환 하지 않습니다 때문에 필요 합니다. 이는 MSAL.js 버전 1.3.0에서 선택 사항이 되었습니다.

```javascript
// Configuration object constructed
const config = {
    auth: {
        clientId: "abcd-ef12-gh34-ikkl-ashdjhlhsdg"
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

MSAL.js는 단일 인증 컨텍스트를 나타내는 `UserAgentApplication` 단일 인스턴스 및 구성을 갖도록 설계되었습니다. 여러 인스턴스는 브라우저에서 캐시 항목및 동작이 충돌하기 때문에 권장되지 않습니다.

## <a name="configuration-options"></a>구성 옵션

MSAL.js에는 의 인스턴스를 만드는 데 사용할 수 있는 구성 가능한 옵션의 그룹화를 제공하는 구성 개체가 아래에 `UserAgentApplication`표시됩니다.

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
    navigateFrameWait?: number;
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

다음은 구성 객체에서 현재 지원되는 구성 가능한 옵션의 총 집합입니다.

- **clientID**: 필수입니다. 응용 프로그램의 clientID는 응용 프로그램 등록 포털에서 이 것을 얻어야 합니다.

- **권한**: 선택 사항입니다. MSAL에서 토큰을 요청할 수 있는 디렉터리를 나타내는 URL입니다. 기본값은 `https://login.microsoftonline.com/common`입니다.
    * Azure AD에서 인스턴스가 ID&lt;공급자&gt;/&lt;&gt;도메인(예: &lt;&gt; `https://login.microsoftonline.com`ID 공급자 도메인)이고 &lt;잠재고객이&gt; 로그인 대상을 나타내는 식별자인 경우 인스턴스 대상을 https:// 형식입니다. 다음 값일 수 있습니다.
        * `https://login.microsoftonline.com/<tenant>`- 테넌트는 contoso.onmicrosoft.com 같은 테넌트에 연결된 도메인또는 `TenantID` 특정 조직의 사용자에 로그인하는 데만 사용되는 디렉터리 속성을 나타내는 GUID입니다.
        * `https://login.microsoftonline.com/common`- 직장 및 학교 계정 또는 Microsoft 개인 계정으로 사용자를 로그인하는 데 사용됩니다.
        * `https://login.microsoftonline.com/organizations/`- 직장 및 학교 계정으로 사용자를 로그인하는 데 사용됩니다.
        * `https://login.microsoftonline.com/consumers/`- 개인 마이크로 소프트 계정 (라이브)로 사용자에 로그인하는 데 사용됩니다.
    * Azure AD B2C에서 `https://<instance>/tfp/<tenant>/<policyName>/`인스턴스가 Azure AD B2C 도메인즉{your-tenant-name}.b2clogin.com인 경우, 테넌트는 Azure AD B2C 테넌트 즉 {귀하의 테넌트 이름}.onmicrosoft.com의 이름이며, 정책이름은 적용할 B2C 정책의 이름입니다.


- **유효성 검사 기관**: 선택 사항입니다.  토큰 발급자의 유효성을 검사합니다. 기본값은 `true`입니다. B2C 응용 프로그램의 경우 기관 값이 알려져 있고 정책마다 다를 수 있기 때문에 권한 유효성 `false`검사가 작동하지 않으며 로 설정해야 합니다.

- **리디렉션Uri**: 선택 사항입니다.  앱이 인증 응답을 보내고 받을 수 있는 앱의 리디렉션 URI입니다. 포털에 등록한 리디렉션 URI 중 하나와 정확히 일치해야 합니다. 기본값은 `window.location.href`입니다.

- **postLogoutRedirectUri**: 선택 사항입니다.  로그아웃 후로 `postLogoutRedirectUri` 사용자를 리디렉션합니다. 기본값은 `redirectUri`.

- **탐색ToLoginRequestUrl**: 선택 사항입니다. 로그인 후 페이지를 시작하려면 기본 탐색을 해제할 수 있습니다. 기본값은 true입니다. 리디렉션 흐름에만 사용됩니다.

- **캐시위치**: 선택 사항입니다.  브라우저 저장소를 `localStorage` 중 `sessionStorage`하나 또는 로 설정합니다. 기본값은 `sessionStorage`입니다.

- **storeAuthStateInCookie**: 선택 사항입니다.  이 플래그는 MSAL.js v0.2.2에서 마이크로소프트 인터넷 익스플로러및 마이크로소프트 에지의 [인증 루프 문제에](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#1-issues-due-to-security-zones) 대한 수정사항으로 도입되었다. 이 수정 `storeAuthStateInCookie` 프로그램을 활용 하려면 플래그를 true로 설정 합니다. 이 옵션을 사용하면 MSAL.js는 브라우저 쿠키에서 인증 흐름의 유효성 검사에 필요한 인증 요청 상태를 저장합니다. 기본적으로 이 플래그는 `false`로 설정됩니다.

- **로거**: 선택 사항입니다.  개발자가 사용자 지정 방식으로 로그를 사용하고 게시하기 위해 제공할 수 있는 콜백 인스턴스가 있는 Logger 개체입니다. 로거 객체 전달에 대한 자세한 내용은 [msal.js로 로깅을](msal-logging.md)참조하십시오.

- **로드프레임 타임아웃**: 선택 사항입니다.  Azure AD의 토큰 갱신 응답 이전의 비활성 시간(밀리초)은 시간 초과로 간주되어야 합니다. 기본값은 6초입니다.

- **토큰 갱신오프셋초**: 선택 사항입니다. 만료되기 전에 토큰을 갱신하는 데 필요한 오프셋 창을 설정하는 밀리초입니다. 기본값은 300밀리초입니다.

- **탐색프레임대기**: 선택 사항입니다. 숨겨진 iframes가 대상으로 이동하기 전에 대기 시간을 설정하는 밀리초 수입니다. 기본값은 500밀리초입니다.

MSAL 각도 래퍼 라이브러리에서 전달할 때만 적용할 수 있습니다.
- **보호되지 않은 리소스**: 선택 사항입니다.  보호되지 않은 리소스인 URI 배열입니다. MSAL은 이러한 URI가 있는 발고 요청에 토큰을 연결하지 않습니다. 기본값은 `null`입니다.

- **보호리소스맵**: 선택 사항입니다.  이는 웹 API 호출에서 액세스 토큰을 자동으로 연결하기 위해 MSAL에서 사용하는 범위에 리소스를 매핑하는 것입니다. 리소스에 대한 단일 액세스 토큰이 가져옵니다. 따라서 {"https://graph.microsoft.com/v1.0/me", [user.read"}} 또는 리소스의 앱 URL을 다음과https://graph.microsoft.com/같이 매핑할 수 있습니다. 이는 CORS 호출에 필요합니다. 기본값은 `null`입니다.
