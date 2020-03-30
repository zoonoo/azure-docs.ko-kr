---
title: Azure 지도 서비스 모듈 사용 | 마이크로소프트 Azure 지도
description: 이 문서에서는 Azure 지도 서비스 모듈을 사용하여 Microsoft Azure Maps REST 서비스를 활용하는 방법을 알아봅니다.
author: rbrundritt
ms.author: richbrun
ms.date: 03/25/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.openlocfilehash: e985fdda4638529e8ade2c700456d595ff355e8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76988739"
---
# <a name="use-the-azure-maps-services-module"></a>Azure 지도 서비스 모듈 사용

Azure Maps 웹 SDK는 *서비스 모듈을*제공합니다. 이 모듈은 자바스크립트 또는 TypeScript를 사용하여 웹 또는 Node.js 응용 프로그램에서 Azure Maps REST 서비스를 쉽게 사용할 수 있는 도우미 라이브러리입니다.

## <a name="use-the-services-module-in-a-webpage"></a>웹 페이지에서 서비스 모듈 사용

1. 새 HTML 파일 만들기
1. Azure 지도 서비스 모듈을 로드합니다. 다음 두 가지 방법 중 하나로 로드할 수 있습니다.
    - Azure Maps 서비스 모듈의 전역 호스팅Azure 콘텐츠 배달 네트워크 버전을 사용합니다. 파일의 요소에 스크립트 `<head>` 참조를 추가합니다.

        ```html
        <script src="https://atlas.microsoft.com/sdk/javascript/service/2/atlas-service.min.js"></script>
        ```

    - 또는 Azure [Maps-rest](https://www.npmjs.com/package/azure-maps-rest) npm 패키지를 사용하여 Azure Maps Web SDK 소스 코드에 대한 서비스 모듈을 로컬로 로드한 다음 앱과 함께 호스트합니다. 이 패키지에는 TypeScript 정의도 포함됩니다. 이 명령 사용:
    
        > **npm install azure-maps-rest**
    
        그런 다음 파일의 요소에 `<head>` 스크립트 참조를 추가합니다.

         ```html
        <script src="node_modules/azure-maps-rest/dist/atlas-service.min.js"></script>
         ```

1. 인증 파이프라인을 만듭니다. 서비스 URL 클라이언트 끝점을 초기화하려면 먼저 파이프라인을 만들어야 합니다. 고유한 Azure Maps 계정 키 또는 Azure Active Directory(Azure AD) 자격 증명을 사용하여 Azure Maps 검색 서비스 클라이언트를 인증합니다. 이 예제에서는 검색 서비스 URL 클라이언트가 만들어집니다. 

    인증에 구독 키를 사용하는 경우:

    ```javascript
    // Get an Azure Maps key at https://azure.com/maps.
    var subscriptionKey = '<Your Azure Maps Key>';

    // Use SubscriptionKeyCredential with a subscription key.
    var subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(subscriptionKey);

    // Use subscriptionKeyCredential to create a pipeline.
    var pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential, {
      retryOptions: { maxTries: 4 } // Retry options
    });

    // Create an instance of the SearchURL client.
    var searchURL = new atlas.service.SearchURL(pipeline);
    ```

    인증에 Azure AD를 사용하는 경우:

    ```javascript
    // Enter your Azure AD client ID.
    var clientId = "<Your Azure Active Directory Client Id>";

    // Use TokenCredential with OAuth token (Azure AD or Anonymous).
    var aadToken = await getAadToken();
    var tokenCredential = new atlas.service.TokenCredential(clientId, aadToken);

    // Create a repeating time-out that will renew the Azure AD token.
    // This time-out must be cleared when the TokenCredential object is no longer needed.
    // If the time-out is not cleared, the memory used by the TokenCredential will never be reclaimed.
    var renewToken = async () => {
      try {
        console.log("Renewing token");
        var token = await getAadToken();
        tokenCredential.token = token;
        tokenRenewalTimer = setTimeout(renewToken, getExpiration(token));
      } catch (error) {
        console.log("Caught error when renewing token");
        clearTimeout(tokenRenewalTimer);
        throw error;
      }
    }
    tokenRenewalTimer = setTimeout(renewToken, getExpiration(aadToken));

    // Use tokenCredential to create a pipeline.
    var pipeline = atlas.service.MapsURL.newPipeline(tokenCredential, {
      retryOptions: { maxTries: 4 } // Retry options
    });

    // Create an instance of the SearchURL client.
    var searchURL = new atlas.service.SearchURL(pipeline);

    function getAadToken() {
      // Use the signed-in auth context to get a token.
      return new Promise((resolve, reject) => {
        // The resource should always be https://atlas.microsoft.com/.
        const resource = "https://atlas.microsoft.com/";
        authContext.acquireToken(resource, (error, token) => {
          if (error) {
            reject(error);
          } else {
            resolve(token);
          }
        });
      })
    }

    function getExpiration(jwtToken) {
      // Decode the JSON Web Token (JWT) to get the expiration time stamp.
      const json = atob(jwtToken.split(".")[1]);
      const decode = JSON.parse(json);

      // Return the milliseconds remaining until the token must be renewed.
      // Reduce the time until renewal by 5 minutes to avoid using an expired token.
      // The exp property is the time stamp of the expiration, in seconds.
      const renewSkew = 300000;
      return (1000 * decode.exp) - Date.now() - renewSkew;
    }
    ```

    자세한 내용은 [Azure 지도의 인증을](azure-maps-authentication.md)참조하십시오.

1. 다음 코드는 새로 만든 Azure Maps 검색 서비스 URL 클라이언트를 사용하여 "1 Microsoft Way, Redmond, WA"라는 주소를 지오코딩합니다. 코드는 함수를 `searchAddress` 사용하고 결과를 페이지 본문에 테이블로 표시합니다.

    ```javascript
    // Search for "1 microsoft way, redmond, wa".
    searchURL.searchAddress(atlas.service.Aborter.timeout(10000), '1 microsoft way, redmond, wa')
      .then(response => {
        var html = [];

        // Display the total results.
        html.push('Total results: ', response.summary.numResults, '<br/><br/>');

        // Create a table of the results.
        html.push('<table><tr><td></td><td>Result</td><td>Latitude</td><td>Longitude</td></tr>');

        for(var i=0;i<response.results.length;i++){
          html.push('<tr><td>', (i+1), '.</td><td>', 
            response.results[i].address.freeformAddress, 
            '</td><td>', 
            response.results[i].position.lat,
            '</td><td>', 
            response.results[i].position.lon,
            '</td></tr>');
        }

        html.push('</table>');

        // Add the resulting HTML to the body of the page.
        document.body.innerHTML = html.join('');
    });
    ```

    전체 실행 중인 코드 샘플은 다음과 같습니다.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="서비스 모듈 사용" src="//codepen.io/azuremaps/embed/zbXGMR/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen에서</a>Azure 지도 ()별<a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io/azuremaps/pen/zbXGMR/'>서비스 모듈사용</a> 펜 을 참조하십시오.
</iframe>

<br/>

## <a name="azure-government-cloud-support"></a>Azure 정부 클라우드 지원

Azure 지도 웹 SDK는 Azure 정부 클라우드를 지원합니다. Azure Maps 웹 SDK에 액세스하는 데 사용되는 모든 JavaScript 및 CSS URL은 동일하게 유지되지만 Azure Maps 플랫폼의 Azure 정부 클라우드 버전에 연결하려면 다음 작업을 수행해야 합니다.

대화형 맵 컨트롤을 사용하는 경우 클래스의 인스턴스를 만들기 `Map` 전에 다음 코드 줄을 추가합니다. 

```javascript
atlas.setDomain('atlas.azure.us');
```

맵 및 서비스를 인증할 때 Azure 정부 클라우드 플랫폼에서 Azure Maps 인증 세부 정보를 사용해야 합니다.

서비스 모듈을 사용하는 경우 API URL 끝점의 인스턴스를 만들 때 서비스에 대한 도메인을 설정해야 합니다. 예를 들어 다음 코드는 클래스의 `SearchURL` 인스턴스를 만들고 도메인을 Azure 정부 클라우드로 가리킵니다.

```javascript
var searchURL = new atlas.service.SearchURL(pipeline, 'atlas.azure.us');
```

Azure Maps REST 서비스에 직접 액세스하는 경우 `atlas.azure.us`URL 도메인을 로 변경합니다. 예를 들어 검색 API 서비스를 사용하는 경우 URL `https://atlas.microsoft.com/search/` `https://atlas.azure.us/search/`도메인을 에서 로 변경합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서 사용된 클래스 및 메서드에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [지도 수전](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.mapsurl?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [검색 URL](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchurl?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [Routeurl](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.routeurl?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [구독키 자격 증명](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.subscriptionkeycredential?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [토큰 자격 증명](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.tokencredential?view=azure-maps-typescript-latest)

서비스 모듈을 사용하는 자세한 코드 샘플은 다음 문서를 참조하십시오.

> [!div class="nextstepaction"]
> [지도에 검색 결과 표시](./map-search-location.md)

> [!div class="nextstepaction"]
> [좌표에서 정보 가져오기](./map-get-information-from-coordinate.md)

> [!div class="nextstepaction"]
> [A에서 B로의 방향 표시](./map-route.md)
