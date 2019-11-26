---
title: Getting started with web map control in Azure Maps | Microsoft Docs
description: Learn how to use the Azure Maps map control client-side Javascript library.
author: walsehgal
ms.author: v-musehg
ms.date: 10/08/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: ff183261f67ff76f56fc034d8102e3aa3a4838a8
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/25/2019
ms.locfileid: "74480532"
---
# <a name="use-the-azure-maps-map-control"></a>Use the Azure Maps map control

맵 컨트롤 클라이언트 쪽 Javascript 라이브러리를 사용하면 맵 및 포함된 Azure Maps 기능을 웹 또는 모바일 애플리케이션에 렌더링할 수 있습니다.

## <a name="create-a-new-map-in-a-web-page"></a>웹 페이지에 새 맵 만들기

맵 컨트롤 클라이언트 쪽 Javascript 라이브러리를 사용하여 웹 페이지에 지도를 포함할 수 있습니다.

1. 새 HTML 파일 만들기

2. Azure Maps 웹 SDK에서 로드합니다. 두 가지 옵션 중 하나를 사용하여 수행할 수 있습니다.

    a. 파일의 `<head>` 요소에 있는 스타일시트 및 스크립트 참조에 URL 엔드포인트를 추가하여 Azure Maps 웹 SDK의 글로벌 호스팅 CDN 버전을 사용합니다.

    ```HTML
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>
    ```

    b. 아니면 [azure-maps-control](https://www.npmjs.com/package/azure-maps-control) NPM 패키지를 사용하여 로컬에서 Azure Maps 웹 SDK 소스 코드를 로드하여 앱으로 호스트합니다. 이 패키지에는 TypeScript 정의도 포함됩니다.

    > npm install azure-maps-control

    그런 다음, Azure Maps 스타일시트에 대한 참조와 스크립트 소스 참조를 파일의 `<head>` 요소에 추가합니다.

    ```HTML
    <link rel="stylesheet" href="node_modules/azure-maps-control/dist/atlas.min.css" type="text/css"> 
    <script src="node_modules/azure-maps-control/dist/atlas.min.js"></script>
    ```

    >[!Note]
    > Typescript definitions can be imported into your application by adding:
    > ```Javascript
    > import * as atlas from 'azure-maps-control';
    > ```

3. 맵이 페이지 본문 전체를 채우도록 렌더링하려면 `<head>` 요소에 다음 `<style>` 요소를 추가합니다.

    ```HTML
    <style>
        html, body {
            margin: 0;
        }

        #myMap {
            height: 100vh;
            width: 100vw;
        }
    </style>
    ```

4. 페이지 본문에 `<div>` 요소를 추가하고 **myMap**이라는 `id`를 부여합니다.

    ```HTML
    <body>
        <div id="myMap"></div>
    </body>
    ```

5. 맵 컨트롤을 초기화하려면 HTML 본문에 새 섹션을 정의하고 스크립트를 만듭니다. Pass in the `id` of the map `<div>` or an `HTMLElement` (for example, `document.getElementById('myMap')`) as the first parameter when creating an instance of the `Map` class. 자신의 Azure Maps 계정 키나 AAD(Azure Active Directory) 자격 증명으로 [인증 옵션](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.authenticationoptions)을 사용하여 맵을 인증합니다. 계정을 만들거나 키를 찾아야 하는 경우 [Azure Maps 계정과 키를 관리하는 방법](how-to-manage-account-keys.md)을 참조하세요. **언어** 옵션은 맵 레이블 및 컨트롤에 사용할 언어를 지정합니다. 지원되는 언어에 대한 자세한 내용은 [지원되는 언어](supported-languages.md)를 참조하세요. 인증에 구독 키를 사용하는 경우입니다.

    ```HTML
    <script type="text/javascript">
        var map = new atlas.Map('myMap', {
            center: [-122.33, 47.6],
            zoom: 12,
            language: 'en-US',
            authOptions: {
                authType: 'subscriptionKey',
                subscriptionKey: '<Your Azure Maps Key>'
            }
        });
    </script>
    ```

    인증에 AAD(Azure Active Directory)를 사용하는 경우입니다.

    ```HTML
    <script type="text/javascript">
        var map = new atlas.Map('myMap', {
            center: [-122.33, 47.6],
            zoom: 12,
            language: 'en-US',
            authOptions: {
                authType: 'aad',
                clientId: '<Your AAD Client Id>',
                aadAppId: '<Your AAD App Id>',
                aadTenant: '<Your AAD Tenant Id>'
            }
        });
    </script>
    ```

    A list of samples showing how to integrate Azure Active Directory (AAD) with Azure Maps can be found [here](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples). 
    
    For more information, see the [Authentication with Azure Maps](azure-maps-authentication.md) document and also the [Azure Maps Azure AD authentication samples](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples).

6. 선택적으로 다음 메타 태그 요소를 페이지 머리글에 추가하는 것이 도움이 될 수 있습니다.

    ```HTML
    <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
    <meta http-equiv="x-ua-compatible" content="IE=Edge">

    <!-- Ensures the web page looks good on all screen sizes. -->
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
    ```

7. Putting it all together your HTML file should look something like the following code:

    ```HTML
    <!DOCTYPE html>
    <html>
    <head>
        <title></title>

        <meta charset="utf-8">

        <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
        <meta http-equiv="x-ua-compatible" content="IE=Edge">

        <!-- Ensures the web page looks good on all screen sizes. -->
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

        <style>
            html, body {
                margin: 0;
            }

            #myMap {
                height: 100vh;
                width: 100vw;
            }
        </style>
    </head>
    <body>
        <div id="myMap"></div>

        <script type="text/javascript">
            //Create an instance of the map control and set some options.
            var map = new atlas.Map('myMap', {
                center: [-122.33, 47.6],
                zoom: 12,
                language: 'en-US',
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });
        </script>
    </body>
    </html>
    ```

8. 웹 브라우저에서 파일을 열고 렌더링된 맵을 봅니다. It should look like the following code:

    <iframe height="700" style="width: 100%;" scrolling="no" title="맵 컨트롤을 사용하는 방법" src="//codepen.io/azuremaps/embed/yZpEYL/?height=557&theme-id=0&default-tab=html,result" frameborder="no" allowtransparency="true" allowfullscreen="true">See the Pen <a href='https://codepen.io/azuremaps/pen/yZpEYL/'>How to use the map control</a> by Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>) on <a href='https://codepen.io'>CodePen</a>.
    </iframe>

## <a name="localizing-the-map"></a>Localizing the map

Azure Maps provides two different ways of setting the language and regional view of the map. The first option is to add this information to the global `atlas` namespace, which will result in all map control instances in your app defaulting to these settings. The following sets the language to French ("fr-FR") and the regional view to "Auto":

```javascript
atlas.setLanguage('fr-FR');
atlas.setView('Auto');
```

The second option is to pass this information into the map options when loading the map like:

```javascript
map = new atlas.Map('myMap', {
    language: 'fr-FR',
    view: 'Auto',

    authOptions: {
        authType: 'aad',
        clientId: '<Your AAD Client Id>',
        aadAppId: '<Your AAD App Id>',
        aadTenant: '<Your AAD Tenant Id>'
    }
});
```

> [!Note]
> With the Web SDK it is possible to load multiple map instances on the same page with different language and region settings. Additionally, these settings can be update after the map has loaded by using the `setStyle` function of the map. 

Here is an example of Azure Maps with the language set to "fr-FR" and the regional view set to "Auto".

![Map image showing labels in French](./media/how-to-use-map-control/websdk-localization.png)

A complete list of supported languages and regional views is documented [here](supported-languages.md).

## <a name="next-steps"></a>다음 단계

맵을 만들고 상호 작용하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [맵 만들기](map-create.md)

맵 스타일을 지정하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [맵 스타일 선택](choose-map-style.md)

To add more data to your map:

> [!div class="nextstepaction"]
> [맵 만들기](map-create.md)

> [!div class="nextstepaction"]
> [코드 샘플](https://docs.microsoft.com/samples/browse/?products=azure-maps)

For a list of samples showing how to integrate Azure Active Directory (AAD) with Azure Maps, see:

> [!div class="nextstepaction"]
> [Azure AD authentication samples](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)