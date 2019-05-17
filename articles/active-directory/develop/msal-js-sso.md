---
title: Single sign-on (Microsoft Authentication Library for JavaScript) | Azure
description: JavaScript (MSAL.js) 용 Microsoft Authentication Library를 사용 하 여 단일 로그인 환경을 구축 하는 방법을 알아봅니다.
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/24/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9f1f102307256852ac92616c7fb707e0e2739e5d
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/11/2019
ms.locfileid: "65544151"
---
# <a name="single-sign-on-with-msaljs"></a>MSAL.js를 사용 하 여에서 single sign-on

Single Sign-on (SSO) 사용자를 자격 증명을 입력 한 번에 로그인 하 여 다시 인증을 요구 하지 않고 여러 응용 프로그램에서 다시 사용할 수 있는 세션을 설정할 수 있습니다. 이 사용자에 게 원활한 환경을 제공 하 고 반복 되는 자격 증명 프롬프트를 줄일 수 있습니다.

Azure AD 사용자가 처음 인증할 때 세션 쿠키를 설정 하 여 응용 프로그램에 SSO 기능을 제공 합니다. MSAL.js 라이브러리에는 몇 가지 방법으로이 활용 하 여 응용 프로그램이 있습니다.

## <a name="sso-between-browser-tabs"></a>브라우저 탭 간에 SSO

응용 프로그램이 여러 탭에서 열기 고 한 탭에서 사용자에 처음 로그인 하면, 사용자는 또한 로그인 다른 탭에서 묻지 않고 됩니다. 브라우저에서 사용자에 대 한 ID 토큰을 캐시 하는 MSAL.js `localStorage` 열려 있는 다른 탭에서 응용 프로그램에 사용자를 로그인 됩니다.

MSAL.js 기본적으로 사용 하 여 `sessionStorage` 탭 간에 공유 될 세션을 허용 하지 않는입니다. 탭 간에 SSO를 얻으려면 설정 해야 합니다 `cacheLocation` MSAL.js를에서 `localStorage` 아래와 같이 합니다.

```javascript
const config = {
    auth: {
        clientId: “abcd-ef12-gh34-ikkl-ashdjhlhsdg”
    },
    cache: {
        cacheLocation: 'localStorage'
    }
}

const myMSALObj = new UserAgentApplication(config);
```

## <a name="sso-between-apps"></a>앱 간 SSO

사용자가 인증할 때 세션 쿠키는 브라우저에서 Azure AD 도메인에 설정 됩니다. MSAL.js 다른 응용 프로그램 간에 사용자에 대 한 SSO를 제공 하려면이 세션 쿠키에 의존 합니다. MSAL.js ID 토큰을 응용 프로그램 도메인당 브라우저 저장소에 사용자의 액세스 토큰에도 캐시합니다. 결과적으로, 다른 경우에 대 한 SSO 동작 달라 집니다.  

### <a name="applications-on-the-same-domain"></a>같은 도메인에 응용 프로그램

응용 프로그램은 동일한 도메인에서 호스트 되므로 사용자 앱에 한 번 로그인 할 수 하 고 프롬프트 없이 다른 앱에 인증을 받습니다. MSAL.js는 SSO를 제공 하는 도메인에서 사용자에 대 한 캐시 된 토큰을 활용 합니다.

### <a name="applications-on-different-domain"></a>다른 도메인에서 응용 프로그램

도메인 A에 캐시 된 토큰 MSAL.js 2. 도메인에서 액세스할 수 없는 응용 프로그램을 서로 다른 도메인에서 호스팅되는 경우

이 사용자가 로그인 도메인에 도메인 B의 응용 프로그램을 이동 하는 경우 이러한 리디렉션 또는 될 Azure AD 페이지를 사용 하 여 메시지가 표시 되 의미 합니다. Azure AD에 아직 사용자 세션 쿠키를 이후 사용자가 로그인 및 자격 증명을 다시 입력을 갖습니다. 사용자의 Azure AD 사용 하 여 세션에서 여러 사용자 계정이 있으면 로그인 하는 데 관련 계정을 선택 하 라는 메시지가 사용자 표시 됩니다.

### <a name="automatically-select-account-on-azure-ad"></a>자동으로 Azure AD의 계정 선택

경우에 따라 응용 프로그램 사용자의 인증 컨텍스트에 대 한 액세스 권한이 하 고 여러 계정에 로그인 하는 경우 Azure AD 계정 선택 프롬프트를 방지 하려고 합니다.  이렇게 하려면 몇 가지:

**세션 ID (SID)를 사용 하 여**

세션 ID가 있는 [선택적 클레임](active-directory-optional-claims.md) ID 토큰에서 구성할 수 있습니다. 이 클레임에는 응용 프로그램 사용자의 Azure AD 세션에 관계 없이 사용자의 계정 이름 또는 사용자 이름 확인을 허용 합니다. SID 요청 매개 변수를 전달할 수 있습니다는 `acquireTokenSilent` 호출 합니다. 이렇게 하면 Azure AD 계정 선택 하지 않으려면 있습니다. SID가 세션 쿠키에 바인딩되고 브라우저 컨텍스트 교차 하지 않습니다.

```javascript
var request = {
    scopes: ["user.read"],
    sid: sid
}

userAgentApplication.acquireTokenSilent(request).then(function(response) {
        const token = response.accessToken
    }
).catch(function (error) {  
        //handle error
});
```

> [!Note]
> 자동 인증 요청에만 사용할 수 있습니다 `acquireTokenSilent` MSAL.js에서 호출 합니다.
응용 프로그램 매니페스트에 선택적 클레임을 구성 하는 단계를 찾을 수 있습니다 [여기](active-directory-optional-claims.md)합니다.

**로그인 힌트를 사용 하 여**

구성 된 클레임 또는 대화형 인증 호출에서 계정 선택 프롬프트를 무시 해야 하는 SID가 없는 경우 가능 하도록 함으로써를 `login_hint` 요청 매개 변수 및 선택적으로 `domain_hint` 으로 `extraQueryParameters` 는 MSAL.js에서 대화형 메서드 (`loginPopup`, `loginRedirect`하십시오 `acquireTokenPopup` 및 `acquireTokenRedirect`). 예를 들면 다음과 같습니다.

```javascript
var request = {
    scopes: ["user.read"],
    loginHint: preferred_username,
    extraQueryParameters: {domain_hint: 'organizations'}
}

userAgentApplication.loginRedirect(request);
```

사용자에 대 한 ID 토큰에서 반환 된 클레임을 읽어서 login_hint 및 domain_hint에 대 한 값을 가져올 수 있습니다.

* **loginHint** 로 설정 해야 합니다 `preferred_username` ID 토큰의 클레임입니다.

* **domain_hint** /common을 사용 하는 경우 전달 될 경우에 필요 기관. 도메인 힌트는 ID(tid) 테 넌 트에 의해 결정 됩니다.  경우는 `tid` ID 토큰의 클레임은 `9188040d-6c67-4c5b-b112-36a304b66dad` 소비자는 것입니다. 그렇지 않을 경우 조직입니다.

읽기 [여기](v2-oauth2-implicit-grant-flow.md) 로그인 힌트 및 도메인 힌트 값에 대 한 자세한 내용은 합니다.

> [!Note]
> 동시에 SID 및 login_hint를 전달할 수 없습니다. 이렇게 하면 오류 응답입니다.

## <a name="sso-without-msaljs-login"></a>MSAL.js 로그인 없이 SSO

기본적으로 MSAL.js Api에 대 한 토큰을 가져오기 전에 사용자 컨텍스트를 설정 하는 login 메서드 호출 되도록 해야 합니다. 대화형 로그인 방법 되므로 사용자에 게 프롬프트를 표시 합니다.

경우가 응용 프로그램에 인증된 된 사용자의 컨텍스트에 대 한 액세스 또는 ID 토큰 인증을 통해 다른 응용 프로그램의 시작 및 로그인 하지 않고 첫 번째 MSAL.js를 통해 토큰을 획득 하는 SSO를 활용 하려면.

이 예제는 다음과 같습니다. 사용자가 추가 기능 또는 플러그 인으로 실행 하는 다른 JavaScript 응용 프로그램을 호스트 하는 부모 웹 응용 프로그램에 로그인 합니다.

이 시나리오에서 SSO 경험을 다음과 같이 수행할 수 있습니다.

전달 된 `sid` 사용 가능한 경우 (또는 `login_hint` 및 필요에 따라 `domain_hint`) 매개 변수는 MSAL.js 요청으로 `acquireTokenSilent` 다음과 같이 호출:

```javascript
var request = {
    scopes: ["user.read"],
    loginHint: preferred_username,
    extraQueryParameters: {domain_hint: 'organizations'}
}

userAgentApplication.acquireTokenSilent(request).then(function(response) {
        const token = response.accessToken
    }
).catch(function (error) {  
        //handle error
});
```

## <a name="sso-in-adaljs-to-msaljs-update"></a>MSAL.js 업데이트 ADAL.js에서 SSO

MSAL.js는 ADAL.js 사용 하 여 Azure AD 인증 시나리오에 대 한 기능 패리티를 제공합니다. 마이그레이션 ADAL.js에서 간편 하 게 수 MSAL.js 및 사용자가 다시 로그인 하려면 메시지를 표시 하지 않으려면 라이브러리 ADAL.js 캐시에서 사용자의 세션을 나타내는 ID 토큰을 읽고 원활 하 게 MSAL.js에서 사용자를 로그인 합니다.  

를 이용 하려면에서 single sign-on (SSO) 동작의 ADAL.js에서 업데이트할 때 라이브러리를 사용 해야 `localStorage` 토큰 캐시에 대 한 합니다. 설정 된 `cacheLocation` 에 `localStorage` 같이 초기화 시 MSAL.js와 ADAL.js 구성에서:


```javascript
// In ADAL.js
window.config = {
   clientId: 'g075edef-0efa-453b-997b-de1337c29185',
   cacheLocation: 'localStorage'
};

var authContext = new AuthenticationContext(config);


// In latest MSAL.js version
const config = {
    auth: {
        clientId: “abcd-ef12-gh34-ikkl-ashdjhlhsdg”
    },
    cache: {
        cacheLocation: 'localStorage'
    }
}

const myMSALObj = new UserAgentApplication(config);
```

이 구성 되 면 MSAL.js ADAL.js에서 인증된 된 사용자의 캐시 된 상태를 읽고 MSAL.js에서 SSO를 제공 하는 데는 수 있게 됩니다.

## <a name="next-steps"></a>다음 단계

에 대 한 자세한 정보는 [single sign-on 세션 토큰 수명](active-directory-configurable-token-lifetimes.md) Azure AD의 값입니다.
