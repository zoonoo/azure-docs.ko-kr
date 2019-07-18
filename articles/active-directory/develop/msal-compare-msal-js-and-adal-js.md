---
title: MSAL.js와 ADAL.js의 차이점 | Azure
description: MSAL.js(JavaScript용 Microsoft 인증 라이브러리)과 ADAL.js(JavaScript용 Azure AD 인증 라이브러리)의 차이점과 사용할 라이브러리를 선택하는 방법을 알아봅니다.
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/10/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 10b5169d3f06e265b3effa3ec18ad8e4f69959d3
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66121967"
---
# <a name="differences-between-msal-js-and-adal-js"></a>MSAL JS와 ADAL JS의 차이점

MSAL.js(JavaScript용 Microsoft 인증 라이브러리) 및 ADAL.js(JavaScript용 Azure AD 인증 라이브러리)는 모두 Azure AD 엔터티를 인증하고 Azure AD에서 토큰을 요청하는 데 사용됩니다. 지금까지 대부분의 개발자는 ADAL을 사용하는 토큰을 요청하여 Azure AD ID(회사 및 학교 계정)를 인증하는 데 개발자용 Azure AD(v1.0)를 사용해 왔습니다. 이제 MSAL.js를 사용하면 Microsoft ID 플랫폼(v2.0)을 통해 광범위한 Microsoft ID 세트(Azure AD ID, Microsoft 계정 및 Azure AD B2C를 통한 소셜/로컬 계정)를 인증할 수 있습니다.

이 문서에서는 MSAL.js(JavaScript용 Microsoft 인증 라이브러리)와 ADAL.js(JavaScript용 Azure AD 인증 라이브러리) 중에서 선택하는 방법을 설명하고, 두 라이브러리를 비교합니다.

## <a name="choosing-between-adaljs-and-msaljs"></a>ADAL.js와 MSAL.js 중 선택

대부분의 경우 Microsoft ID 플랫폼 및 최신 세대의 Microsoft 인증 라이브러리인 MSAL.js를 사용하려고 합니다. MSAL.js를 사용하면 Azure AD(회사 및 학교 계정), MSA(Microsoft (개인) 계정) 또는 Azure AD B2C를 사용하여 애플리케이션에 로그인하는 사용자에 대한 토큰을 획득할 수 있습니다.

v1.0 엔드포인트(및 ADAL.js)에 이미 익숙한 경우 [v2.0 엔드포인트와의 차이점은?](active-directory-v2-compare.md)을 참조하는 것이 좋습니다.

그러나 애플리케이션에서 이전 버전의 [ADFS(Active Directory Federation Services)](/windows-server/identity/active-directory-federation-services)를 사용하여 사용자를 로그인해야 하는 경우에도 여전히 ADAL.js를 사용해야 합니다.

## <a name="key-differences-in-authentication-with-msaljs"></a>MSAL.js를 사용한 인증의 주요 차이점

### <a name="core-api"></a>코어 API

* ADAL.js는 인증 기관 URL을 통한 권한 부여 서버 또는 ID 공급자에 대한 애플리케이션 연결 인스턴스의 표현으로 [AuthenticationContext](https://github.com/AzureAD/azure-activedirectory-library-for-js/wiki/Config-authentication-context#authenticationcontext)를 사용합니다. 반면 MSAL.js API는 웹 브라우저와 같은 사용자 에이전트에서 클라이언트 코드가 실행되는 공용 클라이언트 애플리케이션의 한 형태인 사용자 에이전트 클라이언트 애플리케이션을 중심으로 설계되었습니다. 권한 부여 서버를 사용하여 애플리케이션의 인증 컨텍스트 인스턴스를 나타내는 `UserAgentApplication` 클래스를 제공합니다. 자세한 내용은 [MSAL.js를 사용하여 초기화](msal-js-initializing-client-applications.md)를 참조하세요.

* ADAL.js에서 토큰을 획득하는 메서드는 `AuthenticationContext`에 설정된 단일 인증 기관과 연결됩니다. MSAL.js에서 토큰 획득 요청은 `UserAgentApplication`에 설정된 것과 다른 인증 기관 값을 사용할 수 있습니다. 이렇게 하면 MSAL.js에서 동일한 애플리케이션의 여러 테넌트와 사용자 계정에 대한 토큰을 개별적으로 획득하고 캐시할 수 있습니다.

* ADAL.js에서 사용자에게 메시지를 표시하지 않고 자동으로 토큰을 획득하고 갱신하는 메서드는 `acquireToken`으로 명명됩니다. MSAL.js에서는 이 기능을 더 자세히 설명하기 위해 이 메서드가 `acquireTokenSilent`로 명명됩니다.

### <a name="authority-value-common"></a>`common` 인증 기관 값

v1.0에서 `https://login.microsoftonline.com/common` 인증 기관을 사용하는 경우 사용자가 모든 조직에 대해 Azure AD 계정을 사용하여 로그인할 수 있습니다.

v2.0에서 `https://login.microsoftonline.com/common` 인증 기관을 사용하는 경우 사용자가 Azure AD 조직 계정 또는 MSA(Microsoft 개인 계정)를 사용하여 로그인할 수 있습니다. 로그인을 Azure AD 계정으로만 제한하려면(ADAL.js와 동일한 동작) `https://login.microsoftonline.com/organizations`를 사용해야 합니다. 자세한 내용은 [MSAL.js를 사용하여 초기화](msal-js-initializing-client-applications.md)의 `authority` 구성 옵션을 참조하세요.

### <a name="scopes-for-acquiring-tokens"></a>토큰 획득에 대한 범위
* 토큰을 획득하기 위한 인증 요청에서 리소스 매개 변수 대신 범위를 지정합니다.

    v2.0 프로토콜은 요청에서 리소스 대신 범위를 사용합니다. 즉, 애플리케이션에서 MS Graph와 같은 리소스에 대한 권한이 있는 토큰을 요청해야 하는 경우 라이브러리 메서드에 전달되는 값의 차이는 다음과 같습니다.

    v1.0: resource=https://graph.microsoft.com

    v2.0: scope = https://graph.microsoft.com/User.Read

    API의 URI를 appidURI/scope 형식으로 사용하여 리소스 API에 대한 범위를 요청할 수 있습니다. 예를 들어 https:\//mytenant.onmicrosoft.com/myapi/api.read입니다.

    `user.read` 범위 값은 MS Graph API에서만 https://graph.microsoft.com/User.Read에 매핑되며 서로 교환하여 사용할 수 있습니다.

    ```javascript
    var request = {
        scopes = ["User.Read"];
    };

    acquireTokenPopup(request);   
    ```

* 증분 동의에 대한 동적 범위

    v1.0을 사용하여 애플리케이션을 빌드하는 경우 로그인할 때 사용자가 동의하도록 애플리케이션에서 요구하는 권한의 전체 세트(정적 범위)를 등록해야 했습니다. v2.0에서는 범위 매개 변수를 사용하여 원하는 시간에 권한을 요청할 수 있습니다. 이를 동적 범위라고 합니다. 이를 통해 사용자는 범위에 대한 증분 동의를 제공할 수 있습니다. 따라서 처음에는 사용자가 애플리케이션에 로그인하도록 하고 어떤 종류의 액세스도 필요하지 않은 경우 그렇게 할 수 있습니다. 나중에 사용자의 일정을 읽을 수 있는 기능이 필요한 경우 acquireToken 메서드에서 일정 범위를 요청하고 사용자의 동의를 받을 수 있습니다. 예: 

    ```javascript
    var request = {
        scopes = ["https://graph.microsoft.com/User.Read", "https://graph.microsoft.com/Calendar.Read"];
    };

    acquireTokenPopup(request);   
    ```

* V1.0 API에 대한 범위

    MSAL.js를 사용하여 V1.0 API에 대한 토큰을 얻는 경우 API의 App ID URI에 `.default`를 범위로 적용하여 API에 등록된 모든 정적 범위를 요청할 수 있습니다. 예: 

    ```javascript
    var request = {
        scopes = [ appidURI + "/.default"];
    };

    acquireTokenPopup(request);
    ```

## <a name="next-steps"></a>다음 단계
자세한 내용은 [v1.0 및 v2.0 비교](active-directory-v2-compare.md)를 참조하세요.
