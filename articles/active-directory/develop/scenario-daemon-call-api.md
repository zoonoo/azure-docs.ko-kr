---
title: 웹 Api를 호출 하는 디먼 앱 (웹 Api 호출)-Microsoft identity platform
description: Web api를 호출 하는 디먼 앱을 빌드하는 방법에 대해 알아봅니다.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/15/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9eacb574f20abeb63a9d0ab8caf534eb7abb9784
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71056357"
---
# <a name="daemon-app-that-calls-web-apis---call-a-web-api-from-the-app"></a>웹 Api를 호출 하는 디먼 앱-앱에서 web API 호출

디먼 앱은 .NET 데몬 응용 프로그램에서 web API를 호출 하거나 미리 승인 된 여러 web Api를 호출할 수 있습니다.

## <a name="calling-a-web-api-from-a-net-daemon-application"></a>.NET 데몬 응용 프로그램에서 web API 호출

토큰을 사용 하 여 API를 호출 하는 방법은 다음과 같습니다.

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

[!INCLUDE [Call web API in .NET](../../../includes/active-directory-develop-scenarios-call-apis-dotnet.md)]

# <a name="pythontabpython"></a>[Python](#tab/python)

```Python
endpoint = "url to the API" 
http_headers = {'Authorization': 'Bearer ' + result['access_token'],
                'Accept': 'application/json',
                'Content-Type': 'application/json'}
data = requests.get(endpoint, headers=http_headers, stream=False).json()
```

# <a name="javatabjava"></a>[Java](#tab/java)

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

---

## <a name="calling-several-apis"></a>여러 Api 호출

디먼 앱의 경우 호출 하는 웹 Api를 미리 승인 해야 합니다. 디먼 앱에는 증분 동의가 없습니다 (사용자 상호 작용이 없음). 테 넌 트 관리자는 응용 프로그램과 모든 API 사용 권한을 사전 동의 해야 합니다. 여러 Api를 호출 하려는 경우를 호출할 `AcquireTokenForClient`때마다 각 리소스에 대 한 토큰을 획득 해야 합니다. MSAL은 응용 프로그램 토큰 캐시를 사용 하 여 불필요 한 서비스 호출을 방지 합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [디먼 앱-프로덕션으로 이동](./scenario-daemon-production.md)