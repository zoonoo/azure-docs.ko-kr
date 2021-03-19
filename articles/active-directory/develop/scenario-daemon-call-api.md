---
title: 디먼 앱에서 web API 호출 | Microsoft
titleSuffix: Microsoft identity platform
description: 웹 API를 호출 하는 디먼 앱을 빌드하는 방법에 대해 알아봅니다.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: f4b223c9195168b445b7eb81c2709a61807fddac
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104578399"
---
# <a name="daemon-app-that-calls-web-apis---call-a-web-api-from-the-app"></a>웹 Api를 호출 하는 디먼 앱-앱에서 web API 호출

.NET 데몬 앱은 웹 API를 호출할 수 있습니다. .NET 데몬 앱은 미리 승인 된 여러 웹 Api를 호출할 수도 있습니다.

## <a name="calling-a-web-api-from-a-daemon-application"></a>디먼 응용 프로그램에서 web API 호출

토큰을 사용 하 여 API를 호출 하는 방법은 다음과 같습니다.

# <a name="net"></a>[.NET](#tab/dotnet)

[!INCLUDE [Call web API in .NET](../../../includes/active-directory-develop-scenarios-call-apis-dotnet.md)]

# <a name="java"></a>[Java](#tab/java)

```Java
HttpURLConnection conn = (HttpURLConnection) url.openConnection();

// Set the appropriate header fields in the request header.
conn.setRequestProperty("Authorization", "Bearer " + accessToken);
conn.setRequestProperty("Accept", "application/json");

String response = HttpClientHelper.getResponseStringFromConn(conn);

int responseCode = conn.getResponseCode();
if(responseCode != HttpURLConnection.HTTP_OK) {
    throw new IOException(response);
}

JSONObject responseObject = HttpClientHelper.processResponse(responseCode, response);
```

# <a name="nodejs"></a>[Node.JS](#tab/nodejs)

[Axios](https://www.npmjs.com/package/axios)와 같은 HTTP 클라이언트를 사용 하 여 *권한 부여 전달자* 로 액세스 토큰을 사용 하 여 API 끝점 URI를 호출 합니다.

```JavaScript
const axios = require('axios');

async function callApi(endpoint, accessToken) {

    const options = {
        headers: {
            Authorization: `Bearer ${accessToken}`
        }
    };

    console.log('request made to web API at: ' + new Date().toString());

    try {
        const response = await axios.default.get(endpoint, options);
        return response.data;
    } catch (error) {
        console.log(error)
        return error;
    }
};
```

# <a name="python"></a>[Python](#tab/python)

```Python
endpoint = "url to the API"
http_headers = {'Authorization': 'Bearer ' + result['access_token'],
                'Accept': 'application/json',
                'Content-Type': 'application/json'}
data = requests.get(endpoint, headers=http_headers, stream=False).json()
```

---

## <a name="calling-several-apis"></a>여러 Api 호출

디먼 앱의 경우 호출 하는 웹 Api를 미리 승인 해야 합니다. 디먼 앱에는 증분 동의가 없습니다. 사용자 상호 작용이 없습니다. 테 넌 트 관리자는 응용 프로그램 및 모든 API 권한에 대해 사전에 동의를 제공 해야 합니다. 여러 Api를 호출 하려는 경우를 호출할 때마다 각 리소스에 대 한 토큰을 획득 `AcquireTokenForClient` 합니다. MSAL은 응용 프로그램 토큰 캐시를 사용 하 여 불필요 한 서비스 호출을 방지 합니다.

## <a name="next-steps"></a>다음 단계

# <a name="net"></a>[.NET](#tab/dotnet)

이 시나리오에서 다음 문서로 이동 하 여 [프로덕션으로 이동](./scenario-daemon-production.md?tabs=dotnet)합니다.

# <a name="java"></a>[Java](#tab/java)

이 시나리오에서 다음 문서로 이동 하 여 [프로덕션으로 이동](./scenario-daemon-production.md?tabs=java)합니다.

# <a name="nodejs"></a>[Node.JS](#tab/nodejs)

이 시나리오에서 다음 문서로 이동 하 여 [프로덕션으로 이동](./scenario-daemon-production.md?tabs=nodejs)합니다.

# <a name="python"></a>[Python](#tab/python)

이 시나리오에서 다음 문서로 이동 하 여 [프로덕션으로 이동](./scenario-daemon-production.md?tabs=python)합니다.

---
