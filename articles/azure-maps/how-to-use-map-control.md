---
title: 웹 맵 컨트롤 시작하기 | 마이크로소프트 Azure 지도
description: Microsoft Azure Maps 맵 컨트롤 클라이언트 측 JavaScript 라이브러리를 사용하여 웹 또는 모바일 응용 프로그램에 맵 및 포함된 Azure Maps 기능을 렌더링하는 방법을 알아봅니다.
author: philmea
ms.author: philmea
ms.date: 01/15/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 6becb504671c1fa380207fda9d7d553fca8ceddf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335243"
---
# <a name="use-the-azure-maps-map-control"></a>Azure 지도 맵 컨트롤 사용

맵 제어 클라이언트 측 JavaScript 라이브러리를 사용하면 웹 또는 모바일 응용 프로그램에 맵 및 포함된 Azure Maps 기능을 렌더링할 수 있습니다.

## <a name="create-a-new-map-in-a-web-page"></a>웹 페이지에 새 맵 만들기

맵 제어 클라이언트 측 JavaScript 라이브러리를 사용하여 웹 페이지에 맵을 포함할 수 있습니다.

1. 새 HTML 파일 만들기

2. Azure Maps 웹 SDK에서 로드합니다. 두 가지 옵션 중 하나를 선택할 수 있습니다.

    * HTML 파일의 `<head>` 요소에 JavaScript 및 스타일시트에 대한 참조를 추가하여 Azure Maps Web SDK의 전역으로 호스팅되는 CDN 버전을 사용합니다.

        ```HTML
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>
        ```

    * Azure 맵 제어 NPM 패키지를 사용하여 [Azure](https://www.npmjs.com/package/azure-maps-control) Maps Web SDK 소스 코드를 로컬로 로드하고 앱으로 호스트합니다. 이 패키지에는 TypeScript 정의도 포함됩니다.

        > **npm install azure-maps-control**

       그런 다음, Azure Maps 스타일시트에 대한 참조와 스크립트 소스 참조를 파일의 `<head>` 요소에 추가합니다.

        ```HTML
        <link rel="stylesheet" href="node_modules/azure-maps-control/dist/atlas.min.css" type="text/css"> 
        <script src="node_modules/azure-maps-control/dist/atlas.min.js"></script>
        ```

    > [!Note]
    > 타이프스크립트 정의는 다음 코드를 추가하여 응용 프로그램으로 가져올 수 있습니다.
    >
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

5. 맵 컨트롤을 초기화하려면 html 본문에 새 스크립트 태그를 정의합니다. `Map` 클래스의 `id` 인스턴스를 `<div>` 만들 `HTMLElement` 때 맵 `document.getElementById('myMap')`또는 a(예:)를 첫 번째 매개변수로 전달합니다. 자신의 Azure Maps 계정 키나 AAD(Azure Active Directory) 자격 증명으로 [인증 옵션](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.authenticationoptions)을 사용하여 맵을 인증합니다. 

   계정을 만들거나 키를 찾아야 하는 경우 [계정 만들기의 안내에](quick-demo-map-app.md#create-an-account-with-azure-maps) 따라 [기본 키를 가져옵니다.](quick-demo-map-app.md#get-the-primary-key-for-your-account) 

   **언어** 옵션은 맵 레이블 및 컨트롤에 사용할 언어를 지정합니다. 지원되는 언어에 대한 자세한 내용은 [지원되는 언어를](supported-languages.md)참조하십시오. 인증에 구독 키를 사용하는 경우 다음을 사용합니다.

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

   인증에 Azure Active Directory(AAD)를 사용하는 경우 다음을 사용합니다.

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

   Azure Active Directory(AAD)를 Azure 맵과 통합하는 방법을 보여 주면 [여기에서](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)찾을 수 있습니다. 
    
   자세한 내용은 Azure [Maps](azure-maps-authentication.md) 문서 및 Azure [Maps Azure AD 인증 샘플을](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)사용한 인증을 참조하세요.

6. 선택적으로 다음 메타 태그 요소를 페이지 머리글에 추가하는 것이 도움이 될 수 있습니다.

   ```HTML
    <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
    <meta http-equiv="x-ua-compatible" content="IE=Edge">

    <!-- Ensures the web page looks good on all screen sizes. -->
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
   ```

7. HTML 파일을 모두 합하면 다음 코드와 비슷해 보입니다.

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

8. 웹 브라우저에서 파일을 열고 렌더링된 맵을 봅니다. 아래 이미지와 같아야 합니다.

   ![렌더링된 결과를 보여주는 맵 이미지](./media/how-to-use-map-control/map-of-seattle.png)

## <a name="localizing-the-map"></a>맵 지역화

Azure Maps는 렌더링된 맵의 언어 및 지역 보기를 설정하는 두 가지 방법을 제공합니다. 첫 번째 옵션은 이 정보를 전역 `atlas` 네임스페이스에 추가하는 것이며, 이로 인해 앱의 모든 맵 컨트롤 인스턴스가 이러한 설정으로 기본값으로 설정됩니다. 다음은 언어를 프랑스어("fr-FR")로 설정하고 지역 보기를 "자동"으로 설정합니다.

```javascript
atlas.setLanguage('fr-FR');
atlas.setView('Auto');
```

두 번째 옵션은 다음과 같이 맵을 로드할 때 이 정보를 맵 옵션으로 전달하는 것입니다.

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
> Web SDK를 사용하면 언어 및 영역 설정이 다른 동일한 페이지에 여러 맵 인스턴스를 로드할 수 있습니다. 또한 맵의 `setStyle` 기능을 사용하여 맵이 로드된 후 이러한 설정을 업데이트할 수 있습니다. 

다음은 언어가 "fr-FR"로 설정되고 지역 보기가 "자동"으로 설정된 Azure Maps의 예입니다.

![프랑스어로 레이블을 보여주는 지도 이미지](./media/how-to-use-map-control/websdk-localization.png)

지원되는 언어 및 지역 보기의 전체 목록은 [여기](supported-languages.md)에 설명되어 있습니다.

## <a name="azure-government-cloud-support"></a>Azure 정부 클라우드 지원

Azure 지도 웹 SDK는 Azure 정부 클라우드를 지원합니다. Azure 지도 웹 SDK에 액세스하는 데 사용되는 모든 자바스크립트 및 CSS URL은 동일하게 유지됩니다. Azure Maps 플랫폼의 Azure 정부 클라우드 버전에 연결하려면 다음 작업을 수행해야 합니다.

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

맵을 만들고 상호 작용하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [맵 만들기](map-create.md)

맵 스타일을 지정하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [맵 스타일 선택](choose-map-style.md)

맵에 더 많은 데이터를 추가하려면 다음 을 수행합니다.

> [!div class="nextstepaction"]
> [맵 만들기](map-create.md)

> [!div class="nextstepaction"]
> [코드 샘플](https://docs.microsoft.com/samples/browse/?products=azure-maps)

Azure Active Directory(AAD)를 Azure 맵과 통합하는 방법을 보여 주면 다음을 참조하십시오.

> [!div class="nextstepaction"]
> [Azure AD 인증 샘플](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)
