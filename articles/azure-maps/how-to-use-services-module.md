---
title: 서비스 모듈-Azure Maps를 사용 하 여 | Microsoft Docs
description: Azure Maps 서비스 모듈을 사용 하는 방법을 알아봅니다.
author: rbrundritt
ms.author: richbrun
ms.date: 03/25/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.openlocfilehash: 3aa39568904cb0acc9b5b76ff7e07729bf99d6b1
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2019
ms.locfileid: "59278601"
---
# <a name="using-the-azure-maps-services-module"></a>Azure Maps 서비스 모듈을 사용 하 여

Azure Maps 웹 SDK 서비스 모듈을 손쉽게 웹 또는 JavaScript 또는 TypeScript를 사용 하 여 Node.js 응용 프로그램에서 Azure Maps REST 서비스를 사용 하는 도우미 라이브러리를 제공 합니다.

## <a name="using-the-services-module-in-a-web-page"></a>웹 페이지에서 서비스 모듈을 사용 하 여

1. 새 HTML 파일 만들기
2. Azure Maps 서비스 모듈을 로드 합니다. 두 가지 옵션 중 하나를 사용하여 수행할 수 있습니다.

    a. Azure Maps 서비스 모듈의 전역적으로 호스팅되는 CDN 버전에 대 한 스크립트 참조를 추가 하 여 사용 된 <head> 파일의 요소:
    
    ```html
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas-service.min.js"></script>
    ```
    
    b. 또는 사용 하 여 로컬로 Azure 매핑합니다 웹 SDK 소스 코드를 로드 합니다 [azure-맵-rest](https://www.npmjs.com/package/azure-maps-rest) NPM 패키지 및 응용 프로그램을 사용 하 여 호스트 합니다. 이 패키지에는 TypeScript 정의도 포함됩니다.
    
    > npm 설치 azure-맵-rest
    
    다음에 대 한 스크립트 참조를 추가 합니다 `<head>` 파일의 요소:
    
    ```html
    <script src="node_modules/azure-maps-rest/dist/js/atlas-service.min.js"></script>
    ```

3. 서비스 URL 클라이언트 끝점을 초기화 하려면 먼저 인증 파이프라인을 만들어야 합니다. 검색 서비스 클라이언트를 인증할 사용자 고유의 Azure Maps 계정 키 또는 Azure Active Directory (AAD) 자격 증명을 사용 합니다. 이 예제에서는 검색 서비스 URL 클라이언트가 만들어집니다. 인증에 대 한 구독 키를 사용 합니다.

    ```javascript
    //Get an Azure Maps key at https://azure.com/maps
    var subscriptionKey = '<Your Azure Maps Key>';
    
    //Use SubscriptionKeyCredential with a subscription key.
    var subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(subscriptionKey);
    
    //Use subscriptionKeyCredential to create a pipeline.
    var pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential, {
      retryOptions: { maxTries: 4 } // Retry options
    });
    
    //Create an instance of the SearchURL client.
    var searchURL = new atlas.service.SearchURL(pipeline);
    ```
    
    인증에 AAD(Azure Active Directory)를 사용하는 경우입니다.

    ```javascript
    // Enter your Azure Actiuve Directory client ID.
    var clientId = "<Your Azure Active Directory Client Id>";
    
    // Use TokenCredential with OAuth token (AAD or Anonymous).
    var aadToken = await getAadToken();
    var tokenCredential = new atlas.service.TokenCredential(clientId, aadToken);
    
    // Create a repeating timeout that will renew the AAD token.
    // This timeout must be cleared once the TokenCredential object is no longer needed.
    // If the timeout is not cleared the memory used by the TokenCredential will never be reclaimed.
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
    
    // Use tokenCredential to create a pipeline
    var pipeline = atlas.service.MapsURL.newPipeline(tokenCredential, {
        retryOptions: { maxTries: 4 } // Retry options
    });
    
    //Create an instance of the SearchURL client.
    var searchURL = new atlas.service.SearchURL(pipeline);

    function getAadToken() {
        //Use the logged in auth context to get a token.
        return new Promise((resolve, reject) => {
            //The resource should always be https://atlas.microsoft.com/.
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
        //Decode the JWT token to get the expiration timestamp.
        const json = atob(jwtToken.split(".")[1]);
        const decode = JSON.parse(json);

        //Return the milliseconds until the token needs renewed.
        //Reduce the time until renew by 5 minutes to avoid using an expired token.
        //The exp property is the timestamp of the expiration in seconds.
        const renewSkew = 300000;
        return (1000 * decode.exp) - Date.now() - renewSkew;
    }
    ```

    자세한 내용은 [Azure Maps를 사용 하 여 인증](azure-maps-authentication.md)합니다.

4. 다음 코드에서는 새로 만든된 검색 서비스 URL 클라이언트 주소는 geocode "1 Microsoft 방식으로, Redmond, WA"를 사용 하는 `searchAddress` 작동 하 고 페이지의 본문에 테이블로 결과 표시 합니다. 

    ```javascript
    //Search for "1 microsoft way, redmond, wa".
    searchURL.searchAddress(atlas.service.Aborter.timeout(10000), '1 microsoft way, redmond, wa').then(response => {
      var html = [];
      
      //Display the total results.
      html.push('Total results: ', response.summary.numResults, '<br/><br/>');
     
      //Create a table of the results.
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
      
      //Add the result HTML to the body of the page.
      document.body.innerHTML = html.join('');
    });
    ```

    정상적으로 실행 하는 코드 샘플은 다음과 같습니다.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="서비스 모듈을 사용 하 여" src="//codepen.io/azuremaps/embed/zbXGMR/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
펜을을 참조 하세요 <a href='https://codepen.io/azuremaps/pen/zbXGMR/'>Services 모듈을 사용 하 여</a> 에서 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)에서 <a href='https://codepen.io'>CodePen</a>합니다.
</iframe>

## <a name="next-steps"></a>다음 단계

이 문서에서 사용된 클래스 및 메서드에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [MapsURL](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.mapsurl?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [SearchURL](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchurl?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [RouteURL](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.routeurl?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [SubscriptionKeyCredential](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.subscriptionkeycredential?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [TokenCredential](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.tokencredential?view=azure-iot-typescript-latest)

Services 모듈을 사용 하는 자세한 코드 예제는 다음 문서를 참조 하세요.

> [!div class="nextstepaction"]
> [지도에 검색 결과 표시](./map-search-location.md)

> [!div class="nextstepaction"]
> [좌표에서 정보 가져오기](./map-get-information-from-coordinate.md)

> [!div class="nextstepaction"]
> [A에서 B로의 방향 표시](./map-route.md)