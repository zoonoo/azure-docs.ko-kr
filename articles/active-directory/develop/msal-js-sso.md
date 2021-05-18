---
title: Single Sign-On(MSAL.js) | Microsoft
titleSuffix: Microsoft identity platform
description: JavaScript용 Microsoft Authentication Library(MSAL.js)를 사용하여 Single Sign-On 환경을 빌드하는 방법에 대해 알아봅니다.
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
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "84690781"
---
# <a name="single-sign-on-with-msaljs"></a>MSAL.js를 사용한 Single Sign-On

SSO(Single Sign-On)를 사용하면 로그인하기 위해 자격 증명을 한 번 입력하고, 다시 인증하지 않아도 여러 애플리케이션에서 다시 사용할 수 있는 세션을 설정할 수 있습니다. 이를 통해 사용자에게 원활한 환경을 제공하고 자격 증명에 대한 반복적인 메시지를 줄일 수 있습니다.

Azure AD는 사용자가 처음으로 인증할 때 세션 쿠키를 설정하여 애플리케이션에 SSO 기능을 제공합니다. MSAL.js 라이브러리를 사용하면 애플리케이션에서 몇 가지 방법으로 이를 활용할 수 있습니다.

## <a name="sso-between-browser-tabs"></a>브라우저 탭 간 SSO

애플리케이션이 여러 탭에 열려 있고 한 탭에서 사용자가 먼저 로그인하는 경우 메시지를 표시하지 않고 다른 탭에서도 로그인됩니다. MSAL.js는 브라우저 `localStorage`에서 사용자에 대한 ID 토큰을 캐시하고 열려 있는 다른 탭의 애플리케이션에 사용자를 로그인합니다.

기본적으로 MSAL.js는 탭 간에 세션을 공유할 수 없도록 하는 `sessionStorage`를 사용합니다. 탭 간에 SSO를 가져오려면 아래와 같이 MSAL.js의 `cacheLocation`을 `localStorage`로 설정해야 합니다.

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

사용자가 인증하면 세션 쿠키가 브라우저의 Azure AD 도메인에 설정됩니다. MSAL.js는 이 세션 쿠키를 사용하여 서로 다른 애플리케이션 간에 사용자에 대한 SSO를 제공합니다. 또한 MSAL.js는 애플리케이션 도메인당 브라우저 스토리지에서 사용자의 ID 토큰 및 액세스 토큰을 캐시합니다. 따라서 SSO 동작은 사례마다 다릅니다.  

### <a name="applications-on-the-same-domain"></a>동일한 도메인의 애플리케이션

애플리케이션이 동일한 도메인에서 호스트되는 경우 앱에 한 번 로그인한 다음 메시지가 표시되지 않고 다른 앱에 인증을 받을 수 있습니다. MSAL.js는 SSO를 제공하기 위해 도메인의 사용자에 대해 캐시된 토큰을 활용합니다.

### <a name="applications-on-different-domain"></a>다른 도메인의 애플리케이션

애플리케이션이 서로 다른 도메인에서 호스트되는 경우 도메인 B의 MSAL.js에서 도메인 A에 캐시된 토큰에 액세스할 수 없습니다.

즉, 도메인 A에 로그인한 사용자가 도메인 B의 애플리케이션으로 이동하면 해당 사용자는 Azure AD 페이지를 사용하여 리디렉션되거나 메시지가 표시됩니다. Azure AD에는 아직 사용자 세션 쿠키가 있으므로 사용자가 로그인하고 자격 증명을 다시 입력할 필요가 없습니다. Azure AD의 세션에 여러 사용자 계정이 있는 경우 로그인할 관련 계정을 선택하라는 메시지가 표시됩니다.

### <a name="automatically-select-account-on-azure-ad"></a>Azure AD에서 자동으로 계정 선택

경우에 따라 애플리케이션은 사용자의 인증 컨텍스트에 액세스하여 여러 계정이 로그인되어 있을 때 Azure AD 계정 선택 프롬프트를 피하려고 합니다.  이 작업은 다음과 같은 몇 가지 방법으로 수행할 수 있습니다.

**세션 ID 사용(SID)**

세션 ID는 ID 토큰에서 구성할 수 있는 [선택적 클레임](active-directory-optional-claims.md)입니다. 이 클레임을 통해 애플리케이션은 사용자의 계정 이름이나 사용자 이름과 관계없이 사용자의 Azure AD 세션을 식별할 수 있습니다. 요청 매개 변수의 SID를 `acquireTokenSilent` 호출에 전달할 수 있습니다. 이렇게 하면 Azure AD에서 계정 선택을 우회할 수 있습니다. SID는 세션 쿠키에 바인딩되고 브라우저 컨텍스트를 교차하지 않습니다.

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
> SID는 MSAL.js의 `acquireTokenSilent` 호출에 의해 수행된 자동 인증 요청에서만 사용할 수 있습니다.
애플리케이션 매니페스트에서 선택적 클레임을 구성하는 단계는 [여기](active-directory-optional-claims.md)에서 찾을 수 있습니다.

**로그인 힌트 사용**

구성된 SID 클레임이 없거나 대화형 인증 호출에서 계정 선택 프롬프트를 무시해야 하는 경우, 요청 매개 변수에서 `login_hint`를 제공하고 선택적으로 MSAL.js 대화형 메서드(`loginPopup`, `loginRedirect`, `acquireTokenPopup` 및 `acquireTokenRedirect`)에서 `extraQueryParameters`로 `domain_hint`를 제공하여 이를 수행할 수 있습니다. 예를 들면 다음과 같습니다.

```javascript
var request = {
    scopes: ["user.read"],
    loginHint: preferred_username,
    extraQueryParameters: {domain_hint: 'organizations'}
}

userAgentApplication.loginRedirect(request);
```

사용자에 대한 ID 토큰에 반환된 클레임을 읽어 login_hint 및 domain_hint에 대한 값을 가져올 수 있습니다.

* **loginHint** 는 ID 토큰의 `preferred_username` 클레임으로 설정해야 합니다.

* **domain_hint** 는 /common 인증 기관을 사용하는 경우에만 전달해야 합니다. 도메인 힌트는 tid(테넌트 ID)에 의해 결정됩니다.  ID 토큰의 `tid` 클레임이 `9188040d-6c67-4c5b-b112-36a304b66dad`이면 소비자입니다. 그렇지 않으면 조직입니다.

로그인 힌트 및 도메인 힌트의 값에 대한 자세한 내용은 [여기](v2-oauth2-implicit-grant-flow.md)를 참조하세요.

> [!Note]
> SID와 login_hint를 동시에 전달할 수 없습니다. 그러면 오류 응답이 발생합니다.

## <a name="sso-without-msaljs-login"></a>MSAL.js 로그인을 사용하지 않는 SSO

설계상 MSAL.js는 API에 대한 토큰을 가져오기 전에 사용자 컨텍스트를 설정하기 위해 로그인 메서드를 호출해야 합니다. 로그인 메서드는 대화형이므로 사용자에게 프롬프트가 표시됩니다.

다른 애플리케이션에서 시작된 인증을 통해 인증된 사용자의 컨텍스트나 ID 토큰에 액세스하고 SSO를 활용하여 MSAL.js를 통해 처음 로그인하지 않고 토큰을 획득하려는 경우가 있습니다.

예를 들어, 사용자가 상위 웹 애플리케이션에 서명되어 추가 기능 또는 플러그인으로 실행되는 다른 JavaScript 애플리케이션을 호스트합니다.

이 시나리오의 SSO 환경은 다음과 같이 달성할 수 있습니다.

다음과 같이 사용 가능한 경우 `sid`(또는 `login_hint` 및 선택적으로 `domain_hint`)를 요청 매개 변수로 MSAL.js `acquireTokenSilent` 호출에 전달합니다.

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

## <a name="sso-in-adaljs-to-msaljs-update"></a>ADAL.js에서 MSAL.js로 SSO 업데이트

MSAL.js는 Azure AD 인증 시나리오에 대한 ADAL.js의 기능 패리티를 제공합니다. ADAL.js에서 MSAL.js로 쉽게 마이그레이션하고 사용자가 다시 로그인하지 않도록 라이브러리는 ADAL.js 캐시에서 사용자 세션을 나타내는 ID 토큰을 읽고 MSAL.js의 사용자를 원활하게 로그인합니다.  

ADAL.js에서 업데이트할 때 SSO(Single Sign-On) 동작을 활용하려면 라이브러리에서 토큰을 캐시하는 데 `localStorage`를 사용하고 있는지 확인해야 합니다. 다음과 같이 초기화할 때 MSAL.js 및 ADAL.js 구성 모두에서 `cacheLocation`을 `localStorage`로 설정합니다.


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

구성된 후에는 MSAL.js가 ADAL.js에서 인증된 사용자의 캐시된 상태를 읽고 이를 사용하여 MSAL.js에서 SSO를 제공할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Azure AD의 [Single Sign-On 세션 및 토큰 수명](active-directory-configurable-token-lifetimes.md) 값에 대해 자세히 알아봅니다.
