---
title: Microsoft Azure Maps 웹 맵 컨트롤 시작
description: Azure Maps에서 맵 컨트롤 클라이언트 쪽 JavaScript 라이브러리를 사용 하 여 웹 및 모바일 응용 프로그램에 맵을 추가 하는 방법에 대해 알아봅니다. Maps를 지역화 하는 방법을 참조 하세요.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/20/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: devx-track-js
ms.openlocfilehash: db7006c01f59e8a11872d99b3a16f24f65ef7588
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94413380"
---
# <a name="use-the-azure-maps-map-control"></a>Azure Maps 맵 컨트롤 사용

맵 컨트롤 클라이언트 쪽 JavaScript 라이브러리를 사용 하면 맵 및 포함 된 Azure Maps 기능을 웹 또는 모바일 응용 프로그램에 렌더링할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

웹 페이지에서 맵 컨트롤를 사용 하려면 다음 필수 구성 요소 중 하나가 있어야 합니다.

* [Azure Maps 계정을](quick-demo-map-app.md#create-an-azure-maps-account) 만들고 기본 키 또는 구독 키 라고도 하는 [기본 구독 키를 가져옵니다](quick-demo-map-app.md#get-the-primary-key-for-your-account).

* [인증 옵션](/javascript/api/azure-maps-control/atlas.authenticationoptions)을 사용 하 여 AAD (Azure Active Directory) 자격 증명을 가져옵니다.

## <a name="create-a-new-map-in-a-web-page"></a>웹 페이지에 새 맵 만들기

맵 컨트롤 클라이언트 쪽 JavaScript 라이브러리를 사용 하 여 웹 페이지에 맵을 포함할 수 있습니다.

1. 새 HTML 파일 만들기

2. Azure Maps 웹 SDK에서 로드합니다. 다음 두 가지 옵션 중 하나를 선택하세요.

    * HTML 파일의 요소에서 JavaScript 및 스타일 시트에 대 한 참조를 추가 하 여 Azure Maps 웹 SDK의 전역적으로 호스팅된 CDN 버전을 사용 합니다 `<head>` .

      ```html
      <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
      <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>
      ```

    * [NPM 패키지를 사용](https://www.npmjs.com/package/azure-maps-control) 하 여 AZURE MAPS 웹 SDK 소스 코드를 로컬로 로드 하 고 앱과 함께 호스트 합니다. 이 패키지에는 TypeScript 정의도 포함됩니다.

      > **npm install azure-maps-control**

    그런 다음 Azure Maps 스타일 시트에 대 한 참조를 `<head>` 파일의 요소에 추가 합니다.

    ```html
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
     ```

    > [!NOTE]
    > 다음 코드를 추가 하 여 Typescript 정의를 응용 프로그램으로 가져올 수 있습니다.
    >
    > ```javascript
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

4. 페이지 본문에 `<div>` 요소를 추가하고 **myMap** 이라는 `id`를 부여합니다.

   ```HTML
    <body onload="InitMap()">
        <div id="myMap"></div>
    </body>
   ```

5. 이제 지도 컨트롤을 초기화 합니다. 컨트롤을 인증 하려면 Azure Maps 구독 키를 소유 하거나 [인증 옵션과](/javascript/api/azure-maps-control/atlas.authenticationoptions)함께 AAD (Azure Active Directory) 자격 증명을 사용 해야 합니다.

    인증을 위해 구독 키를 사용 하는 경우 요소 내에 다음 스크립트 요소를 복사 하 여 `<head>` 첫 번째 요소 아래에 붙여 넣습니다 `<script>` . `<Your Azure Maps Key>`을 Azure Maps 기본 구독 키로 바꿉니다.

     ```HTML
    <script type="text/javascript">
        function InitMap()
        {
            var map = new atlas.Map('myMap', {
                center: [-122.33, 47.6],
                zoom: 12,
                language: 'en-US',
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });
       }
    </script>
    ```

    인증을 위해 AAD (Azure Active Directory)를 사용 하는 경우 요소 내에 다음 스크립트 요소를 복사 하 여 `<head>` 첫 번째 요소 아래에 붙여 넣습니다 `<script>` .

      ```HTML
    <script type="text/javascript">
        function InitMap()
        {
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
        }
    </script>
   ```

    Azure Maps 인증에 대 한 자세한 내용은 [Azure Maps 인증](azure-maps-authentication.md) 문서를 참조 하세요. 또한 Azure Maps와 AAD (Azure Active Directory)를 통합 하는 방법을 보여 주는 샘플 목록은 [여기](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)에서 찾을 수 있습니다.

    >[!TIP]
    >이 예제에서는 지도의를 전달 했습니다 `id` `<div>` . 이 작업을 수행 하는 또 다른 방법은를 `HTMLElement` 첫 번째 매개 변수로 전달 하 여 개체를 전달 하는 것입니다 `document.getElementById('myMap')` .

6. 필요에 따라 `meta` 페이지의 요소에 다음 요소를 추가 하는 것이 유용할 수 있습니다 `head` .

   ```HTML
    <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
    <meta http-equiv="x-ua-compatible" content="IE=Edge">

    <!-- Ensures the web page looks good on all screen sizes. -->
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
   ```

7. 모든 항목을 함께 저장 하면 HTML 파일은 다음과 같이 표시 됩니다.

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


        <script type="text/javascript">
            //Create an instance of the map control and set some options.
            function InitMap()
            {
                var map = new atlas.Map('myMap', {
                    center: [-122.33, 47.6],
                    zoom: 12,
                    language: 'en-US',
                    authOptions: {
                        authType: 'subscriptionKey',
                        subscriptionKey: '<Your Azure Maps Key>'
                    }
                });
            }
        </script>

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
    <body onload="InitMap()">
        <div id="myMap"></div>
    </body>
    </html>
    ```

8. 웹 브라우저에서 파일을 열고 렌더링된 맵을 봅니다. 아래 이미지와 같습니다.

   ![렌더링 된 결과를 표시 하는 지도 이미지](./media/how-to-use-map-control/map-of-seattle.png)

## <a name="localizing-the-map"></a>맵 지역화

Azure Maps는 렌더링 된 맵의 언어 및 지역 뷰를 설정 하는 두 가지 다른 방법을 제공 합니다. 첫 번째 옵션은이 정보를 전역 네임 스페이스에 추가 하는 것입니다 `atlas` . 그러면 앱의 모든 맵 컨트롤 인스턴스가 이러한 설정을 기본값으로 설정 합니다. 다음은 언어를 프랑스어 ("fr-fr")로 설정 하 고 국가별 보기를 "Auto"로 설정 합니다.

```javascript
atlas.setLanguage('fr-FR');
atlas.setView('Auto');
```

두 번째 옵션은 다음과 같이 지도를 로드할 때이 정보를 맵 옵션에 전달 하는 것입니다.

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

> [!NOTE]
> 서로 다른 언어 및 지역 설정을 사용 하 여 동일한 페이지에 여러 개의 맵 인스턴스를 로드할 수 있습니다. 또한 지도의 함수를 사용 하 여 맵이 로드 된 후 이러한 설정을 업데이트할 수 있습니다 `setStyle` .

언어가 "fr-fr"로 설정 되 고 지역 보기가 "Auto"로 설정 된 Azure Maps의 예는 다음과 같습니다.

![프랑스어로 레이블을 표시 하는 지도 이미지](./media/how-to-use-map-control/websdk-localization.png)

지원되는 언어 및 지역 보기의 전체 목록은 [여기](supported-languages.md)에 설명되어 있습니다.

## <a name="azure-government-cloud-support"></a>Azure Government 클라우드 지원

Azure Maps 웹 SDK는 Azure Government 클라우드를 지원 합니다. Azure Maps 웹 SDK에 액세스 하는 데 사용 되는 모든 JavaScript 및 CSS Url은 동일 하 게 유지 됩니다. Azure Maps 플랫폼의 Azure Government 클라우드 버전에 연결 하려면 다음 작업을 수행 해야 합니다.

대화형 맵 컨트롤을 사용 하는 경우 클래스의 인스턴스를 만들기 전에 다음 코드 줄을 추가 `Map` 합니다.

```javascript
atlas.setDomain('atlas.azure.us');
```

지도와 서비스를 인증 하는 경우 Azure Government 클라우드 플랫폼에서 Azure Maps 인증 세부 정보를 사용 해야 합니다.

서비스 모듈을 사용 하는 경우 API URL 끝점의 인스턴스를 만들 때 서비스에 대 한 도메인을 설정 해야 합니다. 예를 들어 다음 코드는 클래스의 인스턴스를 만들고 `SearchURL` 도메인을 Azure Government 클라우드로 가리킵니다.

```javascript
var searchURL = new atlas.service.SearchURL(pipeline, 'atlas.azure.us');
```

Azure Maps REST 서비스에 직접 액세스 하는 경우 URL 도메인을로 변경 `atlas.azure.us` 합니다. 예를 들어 검색 API 서비스를 사용 하는 경우 URL 도메인을에서 `https://atlas.microsoft.com/search/` 로 변경 `https://atlas.azure.us/search/` 합니다.

## <a name="javascript-frameworks"></a>JavaScript 프레임 워크

JavaScript 프레임워크를 사용하여 개발하는 경우 다음 오픈 소스 프로젝트 중 하나가 유용할 수 있습니다.

- [ng-azure-maps](https://github.com/arnaudleclerc/ng-azure-maps) - Azure 맵 주변의 Angular 10 래퍼.
- [AzureMapsControl.Components](https://github.com/arnaudleclerc/AzureMapsControl.Components) - Azure Maps Blazor 구성 요소.
- [Azure Maps React 구성 요소](https://github.com/WiredSolutions/react-azure-maps) - Azure Maps 컨트롤의 반응 래퍼.
- [Vue Azure Maps](https://github.com/rickyruiz/vue-azure-maps) - Vue 애플리케이션용 Azure Maps 구성 요소.

## <a name="next-steps"></a>다음 단계

맵을 만들고 상호 작용하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [맵 만들기](map-create.md)

맵 스타일을 지정하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [지도 스타일 선택](choose-map-style.md)

지도에 데이터를 더 추가 하려면:

> [!div class="nextstepaction"]
> [맵 만들기](map-create.md)

> [!div class="nextstepaction"]
> [코드 샘플](/samples/browse/?products=azure-maps)

Azure Maps와 AAD (Azure Active Directory)를 통합 하는 방법을 보여 주는 샘플 목록은 다음을 참조 하세요.

> [!div class="nextstepaction"]
> [Azure AD 인증 샘플](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)