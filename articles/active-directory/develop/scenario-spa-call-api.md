---
title: 웹 API를 호출하는 단일 페이지 앱 빌드 - Microsoft ID 플랫폼 | Azure
description: 웹 API를 호출하는 단일 페이지 응용 프로그램을 빌드하는 방법 알아보기
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/06/2019
ms.author: ryanwi
ms.custom: aaddev
ms.openlocfilehash: 5b70b109f43e80fc3ec68f52aef2dba6823033bb
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80882134"
---
# <a name="single-page-application-call-a-web-api"></a>단일 페이지 응용 프로그램: 웹 API 호출

웹 API를 `acquireTokenSilent` 호출하기 전에 액세스 토큰을 획득하거나 갱신하는 메서드를 호출하는 것이 좋습니다. 토큰이 있으면 보호된 웹 API를 호출할 수 있습니다.

## <a name="call-a-web-api"></a>웹 API 호출

# <a name="javascript"></a>[JavaScript](#tab/javascript)

획득한 액세스 토큰을 HTTP 요청에서 보유자로 사용하여 Microsoft 그래프 API와 같은 웹 API를 호출합니다. 예를 들어:

```javascript
    var headers = new Headers();
    var bearer = "Bearer " + access_token;
    headers.append("Authorization", bearer);
    var options = {
         method: "GET",
         headers: headers
    };
    var graphEndpoint = "https://graph.microsoft.com/v1.0/me";

    fetch(graphEndpoint, options)
        .then(function (response) {
             //do something with response
        }
```

# <a name="angular"></a>[Angular](#tab/angular)

MSAL 각도 래퍼는 HTTP 인터셉터를 사용하여 자동으로 액세스 토큰을 자동으로 획득하고 이를 API에 HTTP 요청에 연결합니다. 자세한 내용은 [API를 호출할 토큰 획득을](scenario-spa-acquire-token.md)참조하십시오.

---

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [프로덕션 환경으로 이동](scenario-spa-production.md)
