---
title: 단일 사인온(MSAL.js) | Azure
titleSuffix: Microsoft identity platform
description: 자바스크립트용 Microsoft 인증 라이브러리(MSAL.js)를 사용하여 단일 사인온 환경을 빌드하는 방법에 대해 알아봅니다.
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/24/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 8080d4cf4c3f0091f7837b3fccead5474c42db55
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262855"
---
# <a name="single-sign-on-with-msaljs"></a>MSAL.js를 사용한 Single Sign-On

단일 사인온(SSO)을 사용하면 사용자가 자격 증명을 한 번 입력하여 로그인하고 다시 인증할 필요 없이 여러 응용 프로그램에서 다시 사용할 수 있는 세션을 설정할 수 있습니다. 이렇게 하면 사용자에게 원활한 환경을 제공하고 자격 증명에 대한 반복된 프롬프트가 줄어듭니다.

Azure AD는 사용자가 처음 인증할 때 세션 쿠키를 설정하여 응용 프로그램에 SSO 기능을 제공합니다. MSAL.js 라이브러리를 사용하면 응용 프로그램에서 몇 가지 방법으로 이를 활용할 수 있습니다.

## <a name="sso-between-browser-tabs"></a>브라우저 탭 사이의 SSO

응용 프로그램이 여러 탭에서 열리고 한 탭에서 사용자를 처음 로그인하면 메시지가 표시되지 않고 다른 탭에서도 로그인됩니다. MSAL.js는 브라우저에서 `localStorage` 사용자의 ID 토큰을 캐시하고 다른 열려 있는 탭에서 응용 프로그램에 사용자를 로그인합니다.

기본적으로 MSAL.js는 `sessionStorage` 탭 간에 세션을 공유할 수 없는 것을 사용합니다. 탭 사이에 SSO를 얻으려면 아래와 `cacheLocation` 같이 MSAL.js를 `localStorage` 설정하십시오.

```javascript
const config = {
    auth: {
        clientId: "abcd-ef12-gh34-ikkl-ashdjhlhsdg"
    },
    cache: {
        cacheLocation: 'localStorage'
    }
}

const myMSALObj = new UserAgentApplication(config);
```

## <a name="sso-between-apps"></a>앱 간 SSO

사용자가 인증하면 브라우저의 Azure AD 도메인에 세션 쿠키가 설정됩니다. MSAL.js는 이 세션 쿠키를 사용하여 서로 다른 응용 프로그램 간에 사용자에게 SSO를 제공합니다. 또한 MSAL.js는 응용 프로그램 도메인별 브라우저 저장소에서 사용자의 ID 토큰 및 액세스 토큰을 캐시합니다. 따라서 SSO 동작은 다음과 같은 경우에 따라 다릅니다.  

### <a name="applications-on-the-same-domain"></a>동일한 도메인의 응용 프로그램

응용 프로그램이 동일한 도메인에서 호스팅되는 경우 사용자는 앱에 한 번 로그인한 다음 프롬프트 없이 다른 앱에 인증받을 수 있습니다. MSAL.js는 도메인의 사용자가 SSO를 제공하기 위해 캐시된 토큰을 활용합니다.

### <a name="applications-on-different-domain"></a>다른 도메인의 응용 프로그램

응용 프로그램이 다른 도메인에서 호스팅되는 경우 도메인 A에 캐시된 토큰은 도메인 B의 MSAL.js에서 액세스할 수 없습니다.

즉, 도메인 A에서 로그인한 사용자가 도메인 B의 응용 프로그램으로 이동하면 Azure AD 페이지가 리디렉션되거나 메시지가 표시됩니다. Azure AD에는 여전히 사용자 세션 쿠키가 있으므로 사용자에 로그인하고 자격 증명을 다시 입력할 필요가 없습니다. Azure AD를 사용하여 세션에 여러 사용자 계정이 있는 경우 로그인할 관련 계정을 선택하라는 메시지가 표시됩니다.

### <a name="automatically-select-account-on-azure-ad"></a>Azure AD에서 계정 자동으로 선택

경우에 따라 응용 프로그램은 사용자의 인증 컨텍스트에 액세스할 수 있으며 여러 계정이 로그인될 때 Azure AD 계정 선택 프롬프트를 피하려고 합니다.  이 작업은 다음과 같은 몇 가지 방법으로 수행할 수 있습니다.

**세션 ID(SID) 사용**

세션 ID는 ID 토큰에서 구성할 수 있는 [선택적 클레임입니다.](active-directory-optional-claims.md) 이 클레임은 응용 프로그램이 사용자의 계정 이름 또는 사용자 이름과 관계없이 사용자의 Azure AD 세션을 식별할 수 있도록 합니다. 요청 매개 변수에서 SID를 호출에 `acquireTokenSilent` 전달할 수 있습니다. 이렇게 하면 Azure AD가 계정 선택을 우회할 수 있습니다. SID는 세션 쿠키에 바인딩되며 브라우저 컨텍스트를 교차하지 않습니다.

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
> SID는 MSAL.js에서 호출하여 `acquireTokenSilent` 이루어진 자동 인증 요청에서만 사용할 수 있습니다.
응용 프로그램 매니페스트에서 선택적 클레임을 구성하는 단계를 찾을 수 [있습니다.](active-directory-optional-claims.md)

**로그인 힌트 사용**

SID 클레임이 구성되지 않았거나 대화형 인증 호출에서 계정 선택 프롬프트를 우회해야 하는 `login_hint` 경우 요청 매개 변수를 `domain_hint` `extraQueryParameters` 제공하고 선택적으로 MSAL.js`loginPopup` `loginRedirect`대화형 `acquireTokenPopup` `acquireTokenRedirect`메서드(, 및 )에서와 같이 를 제공하여 이를 수행할 수 있습니다. 예를 들어:

```javascript
var request = {
    scopes: ["user.read"],
    loginHint: preferred_username,
    extraQueryParameters: {domain_hint: 'organizations'}
}

userAgentApplication.loginRedirect(request);
```

사용자의 ID 토큰에 반환된 클레임을 읽고 login_hint 및 domain_hint 값을 얻을 수 있습니다.

* **loginHintID** 토큰의 `preferred_username` 클레임으로 설정되어야 합니다.

* **domain_hint** /common 권한을 사용할 때만 전달되어야 합니다. 도메인 힌트는 테넌트 ID(tid)에 의해 결정됩니다.  ID `tid` 토큰의 `9188040d-6c67-4c5b-b112-36a304b66dad` 클레임이 소비자인 경우. 그렇지 않으면 조직입니다.

로그인 힌트 및 도메인 힌트의 값에 대한 자세한 내용은 [여기를](v2-oauth2-implicit-grant-flow.md) 참조하십시오.

> [!Note]
> SID와 login_hint 동시에 전달할 수 없습니다. 이렇게 하면 오류 응답이 발생합니다.

## <a name="sso-without-msaljs-login"></a>MSAL.js 로그인이 없는 SSO

일반적으로 MSAL.js는 API에 대한 토큰을 얻기 전에 사용자 컨텍스트를 설정하기 위해 로그인 메서드를 호출해야 합니다. 로그인 메서드는 대화형이므로 사용자에게 프롬프트가 표시됩니다.

응용 프로그램이 다른 응용 프로그램에서 시작된 인증을 통해 인증된 사용자의 컨텍스트 또는 ID 토큰에 액세스할 수 있고 MsAL.js를 통해 먼저 로그인하지 않고 SSO를 활용하여 토큰을 획득하려는 경우가 있습니다.

예를 들어 사용자가 추가 기능 또는 플러그인으로 실행되는 다른 JavaScript 응용 프로그램을 호스팅하는 부모 웹 응용 프로그램에 로그인됩니다.

이 시나리오의 SSO 환경은 다음과 같이 수행할 수 있습니다.

요청 `sid` 매개 변수로 `login_hint` 사용 `domain_hint`가능한 경우(또는 선택적으로) MSAL.js `acquireTokenSilent` 호출에 다음과 같이 전달합니다.

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

## <a name="sso-in-adaljs-to-msaljs-update"></a>MsAL.js 업데이트에 ADAL.js의 SSO

MSAL.js는 Azure AD 인증 시나리오에 대해 ADAL.js와 기능 패리티를 제공합니다. ADAL.js에서 MSAL.js로의 마이그레이션을 쉽게 하고 사용자가 다시 로그인하라는 메시지를 표시하지 않도록 하기 위해 라이브러리는 ADAL.js 캐시에서 사용자의 세션을 나타내는 ID 토큰을 읽고 MSAL.js의 사용자에 원활하게 로그인합니다.  

ADAL.js에서 업데이트할 때 단일 사인온(SSO) 동작을 활용하려면 라이브러리가 토큰 캐싱에 사용하고 `localStorage` 있는지 확인해야 합니다. `cacheLocation` 초기화시 MSAL.js 및 ADAL.js 구성 모두에서 다음을 설정합니다. `localStorage`


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
        clientId: "abcd-ef12-gh34-ikkl-ashdjhlhsdg"
    },
    cache: {
        cacheLocation: 'localStorage'
    }
}

const myMSALObj = new UserAgentApplication(config);
```

이렇게 구성되면 MSAL.js는 ADAL.js에서 인증된 사용자의 캐시된 상태를 읽고 이를 사용하여 MSAL.js에서 SSO를 제공할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Azure AD의 [단일 사인온 세션 및 토큰 수명](active-directory-configurable-token-lifetimes.md) 값에 대해 자세히 알아봅니다.
