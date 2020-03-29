---
title: 데몬 앱에서 웹 API 호출 - Microsoft ID 플랫폼 | Azure
description: 웹 API를 호출하는 데몬 앱을 빌드하는 방법 알아보기
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
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 338b638d6b33bcbbb5cf377643a96c71b0d314bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76775185"
---
# <a name="daemon-app-that-calls-web-apis---call-a-web-api-from-the-app"></a>웹 API를 호출하는 데몬 앱 - 앱에서 웹 API호출

.NET 데몬 앱은 웹 API를 호출할 수 있습니다. .NET 데몬 앱은 사전 승인된 여러 웹 API를 호출할 수도 있습니다.

## <a name="calling-a-web-api-from-a-daemon-application"></a>데몬 응용 프로그램에서 웹 API 호출

토큰을 사용하여 API를 호출하는 방법은 다음과 같습니다.

# <a name="net"></a>[.NET](#tab/dotnet)

[!INCLUDE [Call web API in .NET](../../../includes/active-directory-develop-scenarios-call-apis-dotnet.md)]

# <a name="python"></a>[Python](#tab/python)

```Python
endpoint = "url to the API"
http_headers = {'Authorization': 'Bearer ' + result['access_token'],
                'Accept': 'application/json',
                'Content-Type': 'application/json'}
data = requests.get(endpoint, headers=http_headers, stream=False).json()
```

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

---

## <a name="calling-several-apis"></a>여러 API 호출

데몬 앱의 경우 호출하는 웹 API를 사전 승인을 받아야 합니다. 데몬 앱에는 증분 동의가 없습니다. (사용자 상호 작용이 없습니다.) 테넌트 관리자는 응용 프로그램 및 모든 API 권한에 대해 사전에 동의를 제공해야 합니다. 여러 API를 호출하려면 호출할 때마다 각 리소스에 대한 토큰을 `AcquireTokenForClient`획득해야 합니다. MSAL은 불필요한 서비스 호출을 방지하기 위해 응용 프로그램 토큰 캐시를 사용합니다.

## <a name="next-steps"></a>다음 단계

# <a name="net"></a>[.NET](#tab/dotnet)

> [!div class="nextstepaction"]
> [데몬 앱 - 생산으로 이동](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-production?tabs=dotnet)

# <a name="python"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [데몬 앱 - 생산으로 이동](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-production?tabs=python)

# <a name="java"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [데몬 앱 - 생산으로 이동](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-production?tabs=java)

---
