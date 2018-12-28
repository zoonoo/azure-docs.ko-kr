---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: 488fbb2acbf43ac092a7834fc25f433ef09d2b00
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/06/2018
ms.locfileid: "52973241"
---
### <a name="server-auth"></a>방법: 공급자를 사용하여 인증(서버 흐름)
Mobile Apps가 앱에서 인증 프로세스를 관리하게 하려면 앱을 ID 공급자에 등록해야 합니다. 그런 다음, Azure App Service에서 공급자로부터 제공된 응용 프로그램 ID 및 암호를 구성해야 합니다.
자세한 내용은 [앱에 인증 추가](../articles/app-service-mobile/app-service-mobile-cordova-get-started-users.md)자습서를 참조하세요.

ID 공급자를 등록하고 나면 공급자의 이름을 사용하여 `.login()` 메서드를 호출합니다. 예를 들어, Facebook으로 로그인하려면 다음 코드를 사용합니다.

```
client.login("facebook").done(function (results) {
     alert("You are now signed in as: " + results.userId);
}, function (err) {
     alert("Error: " + err);
});
```

공급자에 대해 유효한 값은 'aad', 'facebook', 'google', 'microsoftaccount' 및 'twitter'입니다.

> [!NOTE]
> Google 인증은 서버 흐름을 통해 현재 작동하지 않습니다.  Google을 인증하려면 [클라이언트 흐름 메서드](#client-auth)를 사용해야 합니다.

이 경우 Azure App Service는 OAuth 2.0 인증 흐름을 관리합니다.  선택한 공급자의 로그인 페이지가 표시되고 ID 공급자로 성공적으로 로그인한 후에는 App Service 인증 토큰이 생성됩니다. login 함수를 완료하면 사용자 ID와 App Service 인증 토큰을 각각 userId 및 authenticationToken 필드에 표시하는 JSON 개체가 반환됩니다. 이 토큰은 캐시했다가 만료될 때까지 다시 사용할 수 있습니다.

### <a name="client-auth"></a>방법: 공급자를 사용하여 인증(클라이언트 흐름)

앱이 독립적으로 ID 공급자에 연결한 후 반환된 토큰을 인증을 위해 App Service에 제공할 수도 있습니다. 이 클라이언트 흐름을 사용하면 단일 로그인 환경을 사용자에게 제공하거나 ID 공급자로부터 더 많은 사용자 데이터를 검색할 수 있습니다.

#### <a name="social-authentication-basic-example"></a>소셜 인증 기본 예제

이 예제에서는 인증을 위해 Facebook 클라이언트 SDK를 사용합니다.

```
client.login(
     "facebook",
     {"access_token": token})
.done(function (results) {
     alert("You are now signed in as: " + results.userId);
}, function (err) {
     alert("Error: " + err);
});

```
이 예제에서는 각 공급자 SDK에서 제공된 토큰이 token 변수에 저장되어 있다고 가정합니다.

### <a name="auth-getinfo"></a>방법: 인증된 사용자에 대한 정보 얻기

모든 AJAX 라이브러리와 함께 HTTP 호출 사용 시 인증 정보를 `/.auth/me` 엔드포인트에서 검색할 수 있습니다.  `X-ZUMO-AUTH` 헤더를 인증 토큰으로 설정했는지 확인합니다.  인증 토큰은 `client.currentUser.mobileServiceAuthenticationToken`에 저장되어 있습니다.  예를 들어, fetch API를 사용하려면 다음과 같이 합니다.

```
var url = client.applicationUrl + '/.auth/me';
var headers = new Headers();
headers.append('X-ZUMO-AUTH', client.currentUser.mobileServiceAuthenticationToken);
fetch(url, { headers: headers })
    .then(function (data) {
        return data.json()
    }).then(function (user) {
        // The user object contains the claims for the authenticated user
    });
```

fetch는 [npm 패키지](https://www.npmjs.com/package/whatwg-fetch)로 제공되거나 [CDNJS](https://cdnjs.com/libraries/fetch)에서 브라우저 다운로드할 수 있습니다. 또한 jQuery 또는 AJAX API를 사용하여 정보를 가져올 수도 있습니다.  데이터는 JSON 개체로 수신됩니다.
