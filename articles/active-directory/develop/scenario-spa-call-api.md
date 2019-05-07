---
title: 단일 페이지 응용 프로그램 (웹 API 호출)-Microsoft id 플랫폼
description: 단일 페이지 응용 프로그램 (웹 API 호출 하는 경우)을 빌드하는 방법을 알아봅니다
services: active-directory
documentationcenter: dev-center-name
author: CelesteDG
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/06/2019
ms.author: CelesteDG
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 01f141a5374c0e794b264f6e0135ca3e15ff8359
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074847"
---
# <a name="single-page-application---call-a-web-api"></a>단일 페이지 응용 프로그램-web API 호출

호출 하는 것이 좋습니다는 `acquireTokenSilent` 메서드를 획득 또는 web API를 호출 하기 전에 액세스 토큰을 갱신 합니다. 토큰을 만든 후에 보호 된 web API를 호출할 수 있습니다.

## <a name="call-a-web-api"></a>웹 API 호출

### <a name="javascript"></a>JavaScript

Microsoft Graph API와 같은 웹 API를 호출 하 HTTP 요청에 전달자도 획득 한 액세스 토큰을 사용 합니다. 예를 들면 다음과 같습니다.

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

에 설명 된 대로 합니다 [토큰 섹션을 획득](scenario-spa-acquire-token.md), MSAL Angular 래퍼를 자동으로 자동으로 액세스 토큰을 획득 하 고 Api에 HTTP 요청에 첨부할 HTTP 인터셉터를 활용 합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [프로덕션으로 이동](scenario-spa-production.md)
