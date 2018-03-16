---
title: "Azure Location Based Services를 사용해 검색 | Microsoft Docs"
description: "Azure Location Based Services를 사용하여 주변 관심 지점 검색"
services: location-based-services
keywords: 
author: kgremban
ms.author: kgremban
ms.date: 11/28/2017
ms.topic: tutorial
ms.service: location-based-services
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: f03cdd5491868b78de0514bb66184235dd7df5c2
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/08/2018
---
# <a name="search-nearby-points-of-interest-using-azure-location-based-services"></a>Azure Location Based Services를 사용하여 주변 관심 지점 검색

이 자습서에서는 Azure Location Based Services를 사용해 계정을 설정한 다음 제공된 API를 사용하여 관심 지점을 검색하는 방법을 보여 줍니다. 이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Azure Location Based Services를 사용해 계정 만들기
> * Azure Location Based Services 계정에 대한 기본 키 확인
> * 맵 컨트롤 API를 사용하여 새 웹 페이지 만들기
> * Search Service를 사용하여 주변 관심 지점 찾기

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/)을 만듭니다.

## <a name="log-in-to-the-azure-portal"></a>Azure Portal에 로그인
[Azure 포털](https://portal.azure.com) 에 로그인합니다.

<a id="createaccount"></a>

## <a name="create-an-account-with-azure-location-based-services"></a>Azure Location Based Services를 사용해 계정 만들기

다음 단계를 수행하여 새 Location Based Services 계정을 만듭니다.

1. [Azure Portal](https://portal.azure.com)의 왼쪽 위 모서리에서 **리소스 만들기**를 클릭합니다.
2. *Marketplace 검색* 상자에 **Location Based Services**를 입력합니다.
3. *결과*에서 **Location Based Services(미리 보기)**를 클릭합니다. 맵 아래에 나타나는 **만들기** 단추를 클릭합니다. 
4. **Location Based Services 계정** 페이지에서 다음 값을 입력합니다.
    - 새 계정의 *이름*. 
    - 이 계정에 사용하려는 *구독*.
    - 이 계정에 대한 *리소스 그룹* 이름. *새로 만들기* 또는 *기존* 리소스 그룹 사용을 선택할 수도 있습니다.
    - *리소스 그룹 위치*를 선택합니다.
    - *미리 보기 약관*을 읽고 약관에 동의하면 확인란을 선택합니다. 
    - 마지막으로 **만들기** 단추를 클릭합니다.
   
    ![포털에서 Location Based Services 계정 만들기](./media/tutorial-search-location/create-lbs-account.png)


<a id="getkey"></a>

## <a name="get-the-primary-key-for-your-account"></a>사용자 계정에 대한 기본 키 가져오기

Location Based Services 계정이 성공적으로 만들어지면 단계에 따라 맵 검색 API에 연결합니다.

1. 포털에서 Location Based Services 계정을 엽니다.
2. 계정 **SETTINGS**으로 이동한 다음 **Keys**를 선택합니다.
3. **기본 키**를 클립보드로 복사합니다. 로컬로 저장하여 다음 단계에서 사용합니다. 

    ![포털에서 기본 키 가져오기](./media/tutorial-search-location/lbs-get-key.png)


<a id="createmap"></a>

## <a name="create-new-web-page-using-azure-map-control-api"></a>Azure 맵 컨트롤 API를 사용하여 새 웹 페이지 만들기
Azure 맵 컨트롤 API는 Azure Location Based Services를 웹 응용 프로그램에 쉽게 통합할 수 있도록 하는 편리한 클라이언트 라이브러리입니다. 기본 REST 서비스 호출의 복잡성을 숨기고 스타일 지정 및 사용자 지정 가능한 구성 요소를 사용하여 생산성을 향상합니다. 다음 단계에서는 Location Based Services의 맵 컨트롤 API가 포함된 정적 HTML 페이지를 만드는 방법을 보여줍니다. 

1. 로컬 컴퓨터에서 새 파일을 만들고 이름을 **MapSearch.html**로 지정합니다. 
2. 다음 HTML 구성 요소를 파일에 추가합니다.

    ```HTML
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="utf-8" />
        <meta name="viewport" content="width=device-width, user-scalable=no" />
        <title>Map Search</title>

        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/css/atlas.min.css?api-version=1.0" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/js/atlas.min.js?api-version=1.0"></script>

        <style>
            html,
            body {
                width: 100%;
                height: 100%;
                padding: 0;
                margin: 0;
            }

            #map {
                width: 100%;
                height: 100%;
            }
        </style>
    </head>

    <body>
        <div id="map"></div>
        <script>
            // Embed Map Control JavaScript code here
        </script>
    </body>

    </html>
    ``` 
    HTML 헤더는 Azure 맵 컨트롤 라이브러리에서 호스팅하는 CSS 및 JavaScript 리소스 파일을 포함합니다. *스크립트* 세그먼트가 HTML 파일의 *body*에 추가됨을 참고합니다. 이 세그먼트에는 Azure Location Based Services API에 액세스할 수 있도록 인라인 JavaScript 코드가 포함됩니다.
 
3.  다음 JavaScript 코드를 HTML 파일의 *스크립트* 블록에 추가합니다. 스크립트의 Location Based Services 계정에서 기본 키를 사용합니다. 

    ```JavaScript
    // Instantiate map to the div with id "map"
    var LBSAccountKey = "<_your account key_>";
    var map = new atlas.Map("map", {
        "subscription-key": LBSAccountKey
    });
    ```
    이 세그먼트는 Azure Location Based Services 계정 키에 대한 맵 컨트롤 API를 시작합니다. **Atlas**는 Azure 맵 컨트롤 API 및 관련된 시각적 구성 요소를 포함하는 네임스페이스입니다. **아틀라스 맵**은 시각적 및 대화형 웹 맵에 대한 컨트롤을 제공합니다. 브라우저에서 HTML 페이지를 열어 맵이 어떻게 보이는지 확인할 수 있습니다. 

4. 검색 핀 계층을 맵 컨트롤에 추가하려면 다음 JavaScript 코드를 *스크립트* 블록에 추가합니다.

    ```JavaScript
    // Initialize the pin layer for search results to the map
    var searchLayerName = "search-results";
    map.addPins([], {
        name: searchLayerName,
        cluster: false,
        icon: "pin-round-darkblue"
    });
    ```

5. 파일을 컴퓨터에 저장합니다. 


<a id="usesearch"></a>

## <a name="use-search-service-to-find-nearby-point-of-interest"></a>주변 관심 지점을 찾기 위해 Search Service 사용

이 섹션에서는 Azure Location Based Services의 Search Service API를 사용하여 맵에서 관심 지점을 찾는 방법을 보여줍니다. 주소, 관심 영역 및 기타 지역 정보를 검색하도록 개발자용으로 설계된 RESTful API입니다. Search Service는 지정된 주소에 위도와 경도 정보를 할당합니다. 

1. 이전 섹션에서 만든 **MapSearch.html** 파일을 열고 다음 JavaScript 코드를 *스크립트* 블록에 추가하여 Search Service를 설명합니다. 
    ```JavaScript
    // Perform a request to the search service and create a pin on the map for each result
    var xhttp = new XMLHttpRequest();
    xhttp.onreadystatechange = function () {
        var searchPins = [];

        if (this.readyState === 4 && this.status === 200) {
            var response = JSON.parse(this.responseText);

            var poiResults = response.results.filter((result) => { return result.type === "POI" }) || [];

            searchPins = poiResults.map((poiResult) => {
                var poiPosition = [poiResult.position.lon, poiResult.position.lat];
                return new atlas.data.Feature(new atlas.data.Point(poiPosition), {
                    name: poiResult.poi.name,
                    address: poiResult.address.freeformAddress,
                    position: poiResult.position.lat + ", " + poiResult.position.lon
                });
            });

            map.addPins(searchPins, {
                name: searchLayerName
            });

            var lons = searchPins.map((pin) => { return pin.geometry.coordinates[0] });
            var lats = searchPins.map((pin) => { return pin.geometry.coordinates[1] });

            var swLon = Math.min.apply(null, lons);
            var swLat = Math.min.apply(null, lats);
            var neLon = Math.max.apply(null, lons);
            var neLat = Math.max.apply(null, lats);

            map.setCameraBounds({
                bounds: [swLon, swLat, neLon, neLat],
                padding: 50
            });
        }
    };
    ```
    이 코드 조각은 [XMLHttpRequest](https://xhr.spec.whatwg.org/)를 생성하고, 들어오는 응답을 구문 분석하는 이벤트 처리기를 추가합니다. 성공적인 응답을 위해 반환된 각 위치에 대한 주소, 이름, 위도 및 경도 정보를 `searchPins` 변수에 수집합니다. 마지막으로 이 위치 지점의 컬렉션을 `map` 컨트롤에 핀으로 추가합니다. 

2. 다음 코드를 *스크립트* 블록에 추가하여 XMLHttpRequest를 Azure Location Based Services의 Search Service로 보냅니다.

    ```JavaScript
    var url = "https://atlas.microsoft.com/search/fuzzy/json?";
    url += "&api-version=1.0";
    url += "&query=gasoline%20station";
    url += "&subscription-key=" + LBSAccountKey;
    url += "&lat=47.6292";
    url += "&lon=-122.2337";
    url += "&radius=100000";

    xhttp.open("GET", url, true);
    xhttp.send();
    ``` 
    이 코드 조각은 **Fuzzy Search**이라는 Search Service의 기본 검색 API를 사용합니다. 입력 유사 항목 대부분을 처리하여 주소 또는 *POI* 토큰의 어떠한 결합도 처리합니다. 위도 및 경도로 주어진 주소에 대해, 지정된 반경 내의 모든 **주유소**를 검색합니다. 앞에서 예제 파일에 제공된 사용자 계정의 기본 키를 사용하여 위치 기반 서비스로 호출합니다. 찾은 위치에 대해 위도/경도 쌍으로 결과를 반환합니다. 브라우저에서 HTML 페이지를 열어 검색 핀을 확인할 수도 있습니다. 

3. 다음 코드 줄을 *스크립트* 블록에 추가하여 Search Service에서 반환한 관심 지점에 대한 팝업을 만듭니다.

    ```JavaScript
    // Add a popup to the map which will display some basic information about a search result on hover over a pin
    var popup = new atlas.Popup();
    map.addEventListener("mouseover", searchLayerName, (e) => {
        var popupContentElement = document.createElement("div");
        popupContentElement.style.padding = "5px";

        var popupNameElement = document.createElement("div");
        popupNameElement.innerText = e.features[0].properties.name;
        popupContentElement.appendChild(popupNameElement);

        var popupAddressElement = document.createElement("div");
        popupAddressElement.innerText = e.features[0].properties.address;
        popupContentElement.appendChild(popupAddressElement);

        var popupPositionElement = document.createElement("div");
        popupPositionElement.innerText = e.features[0].properties.position;
        popupContentElement.appendChild(popupPositionElement);

        popup.setPopupOptions({
            position: e.features[0].geometry.coordinates,
            content: popupContentElement
        });

        popup.open(map);
    });
    ```
    API **atlas Popup**은 맵의 필수 지점에 고정된 정보 창을 제공합니다. 이 코드 조각은 팝업에 대한 콘텐츠 및 위치를 설정할 뿐만 아니라 _마우스_가 팝업을 롤오버하도록 기다려 이벤트 수신기를 `map` 컨트롤에 추가합니다. 

4. 파일을 저장한 다음 선택한 웹 브라우저에서 **MapSearch.html** 파일을 열고 결과를 확인합니다. 이 때 브라우저의 맵은 표시된 아무 검색 핀 위로 마우스를 가져갈 때 다음과 비슷한 정보 팝업 창을 보여줍니다 

    ![Azure 맵 컨트롤 및 Search Service](./media/tutorial-search-location/lbs-map-search.png)


## <a name="next-steps"></a>다음 단계
이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * Azure Location Based Services를 사용해 계정 만들기
> * 사용자 계정에 대한 기본 키 가져오기
> * 맵 컨트롤 API를 사용하여 새 웹 페이지 만들기
> * 주변 관심 지점을 찾기 위해 Search Service 사용

관심 지점으로 라우팅하기 위해 Azure Location Based Services를 사용하는 방법에 대해 알아보려면 자습서 [Azure Location Based Services를 사용해 관심 지점까지 라우팅](./tutorial-route-location.md)으로 진행합니다. 
