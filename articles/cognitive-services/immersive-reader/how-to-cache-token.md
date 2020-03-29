---
title: 인증 토큰 캐시
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 인증 토큰을 캐시하는 방법을 보여 줄 것입니다.
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: metan
ms.openlocfilehash: e652aa29b1c1935fcc4887dbe13ef9b683a8bd05
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "75946293"
---
# <a name="how-to-cache-the-authentication-token"></a>인증 토큰을 캐시하는 방법

이 문서에서는 응용 프로그램의 성능을 향상시키기 위해 인증 토큰을 캐시하는 방법을 보여 줍니다.

## <a name="using-aspnet"></a>ASP.NET 사용

토큰을 획득하는 데 사용되는 **Microsoft.IdentityModel.Client.ActiveDirectory** NuGet 패키지를 가져옵니다. 그런 다음 다음 코드를 사용하여 `AuthenticationResult` [몰입형 Reader 리소스를 만들](./how-to-create-immersive-reader.md)때 얻은 인증 값을 사용하여 을 얻습니다.

```csharp
private async Task<AuthenticationResult> GetTokenAsync()
{
    AuthenticationContext authContext = new AuthenticationContext($"https://login.windows.net/{TENANT_ID}");
    ClientCredential clientCredential = new ClientCredential(CLIENT_ID, CLIENT_SECRET);
    AuthenticationResult authResult = await authContext.AcquireTokenAsync("https://cognitiveservices.azure.com/", clientCredential);
    return authResult;
}
```

`AuthenticationResult` 개체에는 SDK를 `AccessToken` 사용하여 몰입형 판독기를 시작할 때 사용할 실제 토큰인 속성이 있습니다. 또한 토큰이 `ExpiresOn` 만료되는 시기를 나타내는 속성도 있습니다. 몰입형 Reader를 시작하기 전에 토큰이 만료되었는지 확인하고 만료된 경우에만 새 토큰을 획득할 수 있습니다.

## <a name="using-nodejs"></a>노드.JS 사용

[**요청에**](https://www.npmjs.com/package/request) npm 패키지를 프로젝트에 추가합니다. [몰입형 Reader 리소스를 만들](./how-to-create-immersive-reader.md)때 얻은 인증 값을 사용하여 다음 코드를 사용하여 토큰을 획득합니다.

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

속성은 `expires_on` 토큰이 만료되는 날짜와 시간으로, 1970년 1월 1일 UTC 이후의 초 수로 표시됩니다. 이 값을 사용하여 토큰을 새로 획득하기 전에 토큰이 만료되었는지 확인합니다.

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