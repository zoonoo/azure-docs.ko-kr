---
title: 웹 앱에서 web api 호출-Microsoft identity platform | Microsoft
description: 웹 Api를 호출 하는 웹 앱을 빌드하는 방법 알아보기 (보호 된 웹 API 호출)
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
ms.openlocfilehash: 84df33137566445015848655cfecb87ba67ef123
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82181684"
---
# <a name="a-web-app-that-calls-web-apis-call-a-web-api"></a>웹 api를 호출 하는 웹 앱: web API 호출

이제 토큰이 있으므로 보호 된 web API를 호출할 수 있습니다.

## <a name="call-a-protected-web-api"></a>보호 된 웹 API 호출

보호 된 웹 API를 호출 하는 것은 선택한 언어 및 프레임 워크에 따라 다릅니다.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

의 동작에 대 한 단순화 된 `HomeController`코드는 다음과 같습니다. 이 코드는 Microsoft Graph를 호출 하는 토큰을 가져옵니다. REST API Microsoft Graph를 호출 하는 방법을 보여 주는 코드가 추가 되었습니다. Microsoft Graph API에 대 한 URL은 appsettings 파일에 제공 되며 라는 `webOptions`변수에서 읽습니다.

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    ...
  },
  ...
  "GraphApiUrl": "https://graph.microsoft.com"
}
```

```csharp
public async Task<IActionResult> Profile()
{
 // Acquire the access token.
 string[] scopes = new string[]{"user.read"};
 string accessToken = await tokenAcquisition.GetAccessTokenForUserAsync(scopes);

 // Use the access token to call a protected web API.
 HttpClient client = new HttpClient();
 client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);
 
  var response = await httpClient.GetAsync($"{webOptions.GraphApiUrl}/beta/me");

  if (response.StatusCode == HttpStatusCode.OK)
  {
   var content = await response.Content.ReadAsStringAsync();

   dynamic me = JsonConvert.DeserializeObject(content);
   return me;
  }

  ViewData["Me"] = me;
  return View();
}
```

> [!NOTE]
> 동일한 원칙을 사용 하 여 web API를 호출할 수 있습니다.
>
> 대부분의 Azure web Api는 API 호출을 간소화 하는 SDK를 제공 합니다. Microsoft Graph도 마찬가지입니다. 다음 문서에서는 API 사용을 보여 주는 자습서를 찾을 수 있는 위치에 대해 알아봅니다.

# <a name="java"></a>[Java](#tab/java)

```Java
private String getUserInfoFromGraph(String accessToken) throws Exception {
    // Microsoft Graph user endpoint
    URL url = new URL("https://graph.microsoft.com/v1.0/me");

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
    return responseObject.toString();
}

```

# <a name="python"></a>[Python](#tab/python)

```Python
@app.route("/graphcall")
def graphcall():
    token = _get_token_from_cache(app_config.SCOPE)
    if not token:
        return redirect(url_for("login"))
    graph_data = requests.get(  # Use token to call downstream service.
        app_config.ENDPOINT,
        headers={'Authorization': 'Bearer ' + token['access_token']},
        ).json()
    return render_template('display.html', result=graph_data)
```

---

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [프로덕션 환경으로 이동](scenario-web-app-call-api-production.md)
