---
title: 웹 API를 호출 하는 단일 페이지 앱 빌드-Microsoft identity platform | Microsoft
description: 웹 API를 호출 하는 단일 페이지 응용 프로그램을 빌드하는 방법을 알아봅니다.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/06/2019
ms.author: ryanwi
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1171d8c3bc28c7b325cc8daf6cc072965363339c
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74965334"
---
# <a name="single-page-application-call-a-web-api"></a>단일 페이지 응용 프로그램: web API 호출

웹 API를 호출 하기 전에 `acquireTokenSilent` 메서드를 호출 하 여 액세스 토큰을 얻거나 갱신 하는 것이 좋습니다. 토큰이 있으면 보호 된 웹 API를 호출할 수 있습니다.

## <a name="call-a-web-api"></a>Webes API-hívás

### <a name="javascript"></a>JavaScript

Microsoft Graph API와 같은 웹 API를 호출 하는 HTTP 요청에서 전달자로 획득 된 액세스 토큰을 사용 합니다. Példa:

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

### <a name="angular"></a>Angular

MSAL 각도 래퍼는 HTTP 인터셉터를 활용 하 여 자동으로 액세스 토큰을 획득 하 고 Api에 대 한 HTTP 요청에 연결 합니다. 자세한 내용은 [API를 호출 하기 위한 토큰 가져오기](scenario-spa-acquire-token.md)를 참조 하세요.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [프로덕션으로 이동](scenario-spa-production.md)
