---
title: Azure Static Web Apps의 사용자 정보 액세스
description: 권한 부여 공급자가 반환하는 사용자 데이터를 읽는 방법을 알아봅니다.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: cshoe
ms.custom: devx-track-js
ms.openlocfilehash: c185075feed71556f8a20ab0e40ffa700f3fdbac
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91333616"
---
# <a name="accessing-user-information-in-azure-static-web-apps-preview"></a>Azure Static Web Apps 미리 보기의 사용자 정보 액세스

Azure 정적 Web Apps는 [직접 액세스 엔드포인트](#direct-access-endpoint) 및 [API 함수](#api-functions)를 통해 인증 관련 사용자 정보를 제공합니다.

많은 사용자 인터페이스가 사용자 인증 데이터에 크게 의존합니다. 직접 액세스 엔드포인트는 사용자 지정 함수를 구현하지 않고도 사용자 정보를 노출하는 유틸리티 API입니다. 편의성 외에도 직접 액세스 엔드포인트는 서버리스 아키텍처와 관련된 콜드 시작 지연의 영향을 받지 않습니다.

## <a name="client-principal-data"></a>클라이언트 보안 주체 데이터

클라이언트 보안 주체 데이터 개체는 사용자가 식별할 수 있는 정보를 앱에 노출합니다. 클라이언트 보안 주체 개체에는 다음과 같은 속성이 있습니다.

| 속성  | Description |
|-----------|---------|
| `identityProvider` | [ID 공급자](authentication-authorization.md)의 이름입니다. |
| `userId` | 사용자에 대한 Azure Static Web Apps 관련 고유 식별자입니다. <ul><li>이 값은 앱별로 고유합니다. 예를 들어 동일한 사용자가 다른 Static Web Apps 리소스에서 다른 `userId` 값을 반환합니다.<li>이 값은 사용자의 수명 동안 지속됩니다. 동일한 사용자를 삭제하고 앱에 다시 추가하면 새 `userId`가 생성됩니다.</ul>|
| `userDetails` | 사용자의 사용자 이름 또는 이메일 주소입니다. 일부 공급자는 [사용자의 이메일 주소](authentication-authorization.md)를 반환하는 반면 다른 공급자는 [사용자 핸들](authentication-authorization.md)을 보냅니다. |
| `userRoles`     | [사용자의 할당된 역할](authentication-authorization.md)의 배열입니다. |

다음 예제는 샘플 클라이언트 보안 주체 개체입니다.

```json
{
  "identityProvider": "facebook",
  "userId": "d75b260a64504067bfc5b2905e3b8182",
  "userDetails": "user@example.com",
  "userRoles": [ "anonymous", "authenticated" ]
}
```

## <a name="direct-access-endpoint"></a>직접 액세스 엔드포인트

`GET` 요청을 `/.auth/me` 경로로 보내고 클라이언트 보안 주체 데이터에 직접 액세스할 수 있습니다. 보기 상태가 권한 부여 데이터를 사용하는 경우 최상의 성능을 위해 이 방법을 사용합니다.

로그인한 사용자의 경우 응답에는 클라이언트 보안 주체 JSON 개체가 포함되어 있습니다. 인증되지 않은 사용자의 요청은 `null`을 반환합니다.

[fetch](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch)<sup>1</sup> API를 통해 다음 구문을 사용하여 클라이언트 보안 주체 데이터에 액세스할 수 있습니다.

```javascript
async function getUserInfo() {
  const response = await fetch("/.auth/me");
  const payload = await response.json();
  const { clientPrincipal } = payload;
  return clientPrincipal;
}

console.log(getUserInfo());
```

## <a name="api-functions"></a>API 함수

Azure Functions 백 엔드를 통해 정적 Web Apps에서 사용할 수 있는 API 함수는 클라이언트 응용 프로그램과 동일한 사용자 정보에 액세스할 수 있습니다. API는 사용자가 식별할 수 있는 정보를 수신 하지만 사용자가 인증 되는 경우 또는 사용자가 필요한 역할과 일치 하는지 확인 하지 않습니다. 액세스 제어 규칙은 파일에 정의 되어 있습니다 [`routes.json`](routes.md) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

클라이언트 보안 주체 데이터는 `x-ms-client-principal` 요청 헤더의 API 함수에 전달됩니다. 클라이언트 보안 주체 데이터는 직렬화된 JSON 개체를 포함하는 [Base64](https://www.wikipedia.org/wiki/Base64) 인코딩 문자열로 전송됩니다.

다음 예제 함수는 사용자 정보를 읽고 반환하는 방법을 보여줍니다.

```javascript
module.exports = async function (context, req) {
  const header = req.headers["x-ms-client-principal"];
  const encoded = Buffer.from(header, "base64");
  const decoded = encoded.toString("ascii");

  context.res = {
    body: {
      clientPrincipal: JSON.parse(decoded)
    }
  };
};
```

위의 함수 이름이 `user`라고 가정할 경우 [fetch](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch)<sup>1</sup> 브라우저 API를 통해 다음 구문을 사용하여 API의 응답에 액세스할 수 있습니다.

```javascript
async function getUser() {
  const response = await fetch("/api/user");
  const payload = await response.json();
  const { clientPrincipal } = payload;
  return clientPrincipal;
}

console.log(await getUser());
```

# <a name="c"></a>[C#](#tab/csharp)

C # 함수에서 사용자 정보는 `x-ms-client-principal` `ClaimsPrincipal` 개체 또는 사용자 지정 형식으로 deserialize 할 수 있는 헤더에서 사용할 수 있습니다. 다음 코드에서는 헤더를 중간 형식에 압축을 풀고 인스턴스로 전환 하는 방법을 보여 줍니다 `ClientPrincipal` `ClaimsPrincipal` .

```csharp
  public static class StaticWebAppsAuth
  {
    private class ClientPrincipal
    {
        public string IdentityProvider { get; set; }
        public string UserId { get; set; }
        public string UserDetails { get; set; }
        public IEnumerable<string> UserRoles { get; set; }
    }

    public static ClaimsPrincipal Parse(HttpRequest req)
    {
        var header = req.Headers["x-ms-client-principal"];
        var data = header.Value[0];
        var decoded = System.Convert.FromBase64String(data);
        var json = System.Text.ASCIIEncoding.ASCII.GetString(decoded);
        var principal = JsonSerializer.Deserialize<ClientPrincipal>(json, new JsonSerializerOptions { PropertyNameCaseInsensitive = true });
  
        principal.UserRoles = principal.UserRoles.Except(new string[] { "anonymous" }, StringComparer.CurrentCultureIgnoreCase);
  
        if (!principal.UserRoles.Any())
        {
            return new ClaimsPrincipal();
        }
  
        var identity = new ClaimsIdentity(principal.IdentityProvider);
        identity.AddClaim(new Claim(ClaimTypes.NameIdentifier, principal.UserId));
        identity.AddClaim(new Claim(ClaimTypes.Name, principal.UserDetails));
        identity.AddClaims(principal.UserRoles.Select(r => new Claim(ClaimTypes.Role, r)));
        return new ClaimsPrincipal(identity);
    }
  }
```

---

<sup>1</sup> Internet Explorer에서는 [fetch](https://caniuse.com/#feat=fetch) API 및 [await](https://caniuse.com/#feat=mdn-javascript_operators_await) 연산자가 지원되지 않습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [앱 설정 구성](application-settings.md)
