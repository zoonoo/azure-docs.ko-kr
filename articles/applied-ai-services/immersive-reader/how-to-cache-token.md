---
title: 인증 토큰 캐시
titleSuffix: Azure Applied AI Services
description: 이 문서에서는 인증 토큰을 캐시하는 방법을 보여줍니다.
author: metanMSFT
manager: guillasi
ms.service: applied-ai-services
ms.subservice: immersive-reader
ms.topic: how-to
ms.date: 01/14/2020
ms.author: metang
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 504e87d8ea02c6bd291a2987f7ab1b32100c82ea
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122566544"
---
# <a name="how-to-cache-the-authentication-token"></a>인증 토큰을 캐시하는 방법

이 문서에서는 애플리케이션의 성능을 향상시키기 위해 인증 토큰을 캐시하는 방법을 보여줍니다.

## <a name="using-aspnet"></a>ASP.NET 사용

토큰을 획득하는 데 사용되는 **Microsoft.IdentityModel.Clients.ActiveDirectory** NuGet 패키지를 가져옵니다. 다음으로, [몰입형 리더 리소스를 만들](./how-to-create-immersive-reader.md) 때 가져온 인증 값을 통해 다음 코드를 사용하여 `AuthenticationResult`를 획득합니다.

```csharp
private async Task<AuthenticationResult> GetTokenAsync()
{
    AuthenticationContext authContext = new AuthenticationContext($"https://login.windows.net/{TENANT_ID}");
    ClientCredential clientCredential = new ClientCredential(CLIENT_ID, CLIENT_SECRET);
    AuthenticationResult authResult = await authContext.AcquireTokenAsync("https://cognitiveservices.azure.com/", clientCredential);
    return authResult;
}
```

`AuthenticationResult` 개체에는 SDK를 사용하여 몰입형 리더를 시작할 때 사용하는 실제 토큰인 `AccessToken` 속성이 있습니다. 토큰이 만료되는 시기를 나타내는 `ExpiresOn` 속성도 있습니다. 몰입형 리더를 시작하기 전에 토큰이 만료되었는지 여부를 확인하고 만료된 경우에만 새 토큰을 획득할 수 있습니다.

## <a name="using-nodejs"></a>Node.JS 사용

프로젝트에 [**요청**](https://www.npmjs.com/package/request) npm 패키지를 추가합니다. 다음 코드를 사용하여 [몰입형 리더 리소스를 만들](./how-to-create-immersive-reader.md) 때 가져온 인증 값을 통해 토큰을 획득합니다.

```javascript
router.get('/token', function(req, res) {
    request.post(
        {
            headers: { 'content-type': 'application/x-www-form-urlencoded' },
            url: `https://login.windows.net/${TENANT_ID}/oauth2/token`,
            form: {
                grant_type: 'client_credentials',
                client_id: CLIENT_ID,
                client_secret: CLIENT_SECRET,
                resource: 'https://cognitiveservices.azure.com/'
            }
        },
        function(err, resp, json) {
            const result = JSON.parse(json);
            return res.send({
                access_token: result.access_token,
                expires_on: result.expires_on
            });
        }
    );
});
```

`expires_on` 속성은 토큰이 만료되는 날짜와 시간으로, 1970년 1월 1일 UTC 이후의 시간(초)으로 표시됩니다. 이 값을 사용하여 새 토큰을 얻기 전에 토큰이 만료되었는지 여부를 확인합니다.

```javascript
async function getToken() {
    if (Date.now() / 1000 > CREDENTIALS.expires_on) {
        CREDENTIALS = await refreshCredentials();
    }
    return CREDENTIALS.access_token;
}
```

## <a name="next-steps"></a>다음 단계

* [몰입형 리더 SDK 참조](./reference.md) 살펴보기