---
title: Google Maps에서 웹 앱 마이그레이션 | Microsoft Docs
description: Google Maps에서 Microsoft Azure Maps로 웹 앱을 마이그레이션하는 방법에 대 한 자습서입니다.
author: rbrundritt
ms.author: richbrun
ms.date: 12/17/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: a414d7b15f81ab81783b66f8297a207afe569365
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/31/2019
ms.locfileid: "75562173"
---
# <a name="migrate-a-web-app-from-google-maps"></a>Google Maps에서 웹 앱 마이그레이션

Google Maps를 사용 하는 대부분의 웹 앱은 Google Maps V3 JavaScript SDK를 사용 합니다. Azure Maps 웹 SDK는로 마이그레이션하는 데 적합 한 Azure 기반 SDK입니다. Azure Maps 웹 SDK를 사용하면 웹 또는 모바일 애플리케이션에 표시할 자체 콘텐츠와 이미지를 통해 대화형 지도를 사용자 지정할 수 있습니다. 이 컨트롤을 통해 WebGL을 사용하여 성능이 높은 대형 데이터 집합을 렌더링할 수 있습니다. JavaScript 또는 TypeScript를 사용 하 여이 SDK로 개발 합니다.

기존 웹 응용 프로그램을 마이그레이션하는 경우 Cesium, Leaflet 및 OpenLayers 같은 오픈 소스 맵 컨트롤 라이브러리를 사용 하 고 있는지 확인 합니다. Azure Maps 웹 SDK를 사용 하지 않으려는 경우, 응용 프로그램을 마이그레이션하기 위한 또 다른 옵션은 오픈 소스 맵 컨트롤을 계속 사용 하 고[Azure Maps 타일 서비스 (](https://docs.microsoft.com/rest/api/maps/render/getmaptile) [위성 타일 \| 위성 타일](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytile))에 연결 하는 것입니다. 다음은 일반적으로 사용 되는 일부 오픈 소스 맵 컨트롤 라이브러리에서 Azure Maps를 사용 하는 방법에 대 한 자세한 내용입니다.

- Cesium-웹에 대 한 3D 맵 컨트롤입니다. [코드 샘플](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Raster%20Tiles%20in%20Cesium%20JS) \| [설명서](https://cesiumjs.org/)
- Leaflet – 웹의 경량 2D 지도 컨트롤입니다. [코드 샘플](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Azure%20Maps%20Raster%20Tiles%20in%20Leaflet%20JS) \| [설명서](https://leafletjs.com/)
- OpenLayers-프로젝션을 지 원하는 웹에 대 한 2D 지도 컨트롤입니다. [코드 샘플](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Raster%20Tiles%20in%20OpenLayers) \| [설명서](https://openlayers.org/)

## <a name="key-features-support"></a>주요 기능 지원

다음 표에서는 Google Maps V3 JavaScript SDK의 주요 API 기능 및 Azure Maps 웹 SDK의 비슷한 API에 대 한 지원을 보여 줍니다.

| Google Maps 기능     | 웹 SDK 지원 Azure Maps |
|-------------------------|:--------------------------:|
| Markers                 | ✓                          |
| 표식 클러스터링       | ✓                          |
| 다중선 & 다각형    | ✓                          |
| 데이터 계층             | ✓                          |
| 접지 오버레이         | ✓                          |
| 열 지도               | ✓                          |
| 타일 계층             | ✓                          |
| KML 계층               | ✓                          |
| 그리기 도구           | ✓                          |
| Geocoder 서비스        | ✓                          |
| 방향 서비스      | ✓                          |
| 거리 매트릭스 서비스 | ✓                          |
| 권한 상승 서비스       | 계획                    |

## <a name="notable-differences-in-the-web-sdks"></a>웹 Sdk의 주목할 만한 차이점

다음은 Google Maps와 Azure Maps 웹 Sdk에서 인식할 수 있는 몇 가지 주요 차이점입니다.

- Azure Maps 웹 SDK에 액세스 하기 위해 호스트 된 끝점을 제공 하는 것 외에도, 원하는 경우 웹 SDK를 앱에 포함 하는 데에도 NPM 패키지를 사용할 수 있습니다. 자세한 내용은이 [설명서](how-to-use-map-control.md) 를 참조 하세요. 이 패키지에는 TypeScript 정의도 포함됩니다.
- Azure Maps에서 Map 클래스의 인스턴스를 만든 후 맵과 상호 작용 하기 전에 코드에서 Maps `ready` 또는 `load` 이벤트가 발생 될 때까지 기다려야 합니다. 이렇게 하면 모든 맵 리소스를 로드 하 고 액세스할 준비가 됩니다.
- 두 플랫폼 모두 기본 맵에 비슷한 바둑판식 배열 시스템을 사용 하지만 Google Maps의 타일은 차원에서 256 픽셀이 고 Azure Maps의 타일은 512 픽셀입니다. 따라서 Google Maps Azure Maps에서 동일한 지도 보기를 가져오려면 Google Maps에서 사용 되는 확대/축소 수준을 Azure Maps에서 1로 빼서 야 합니다.
- Google Maps의 좌표는 "경도, 위도" Azure Maps 사용 하는 동안 "위도, 경도" 라고 합니다. 이는 대부분의 GIS 플랫폼 뒤에 나오는 표준 `[x, y]` 맞춥니다.
- Azure Maps 웹 SDK의 도형은 GeoJSON 스키마를 기반으로 합니다. 도우미 클래스는 [ *atlas 데이터* 네임 스페이스](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data?view=azure-iot-typescript-latest)를 통해 노출 됩니다. Atlas도 있습니다 [ *.* ](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape)GeoJSON 개체를 래핑하고 데이터 바인딩 가능한 방식으로 쉽게 업데이트 하 고 유지 관리할 수 있도록 하는 데 사용할 수 있는 Shape 클래스입니다.
- Azure Maps 좌표는 `[longitude, latitude]` 또는 새 atlas. Position (경도, 위도) 형식으로 단순 숫자 배열로 지정할 수 있는 Position 개체로 정의 됩니다.
    > [!TIP]
    > Position 클래스에는 "위도, 경도" 형식으로 된 좌표를 가져오기 위한 정적 도우미 메서드가 있습니다. [FromLatLng](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.position?view=azure-iot-typescript-latest) 메서드는 Google Maps 코드의 `new google.maps.LatLng` 메서드를 대체 하는 경우가 종종 있습니다.
- 지도에 추가 되는 각 모양에 대 한 스타일 정보를 지정 하는 대신 Azure Maps는 데이터에서 스타일을 구분 합니다. 데이터는 데이터 원본에 저장 되 고 코드에서 데이터를 렌더링 하는 데 사용 하는 Azure Maps 렌더링 계층에 연결 됩니다. 이 방법은 향상 된 성능 혜택을 제공 합니다. 또한 대부분의 계층에서는 비즈니스 논리를 계층 스타일 옵션에 추가할 수 있는 데이터 기반 스타일을 지원 합니다 .이 스타일 옵션을 사용 하면 도형에 정의 된 속성을 기반으로 개별 셰이프가 계층 내에서 렌더링 되는 방식이 변경 됩니다.

## <a name="web-sdk-side-by-side-examples"></a>웹 SDK side-by-side 예제

다음은 Google Maps V3 JavaScript SDK에서 Azure Maps 웹 SDK로 웹 응용 프로그램을 마이그레이션하는 데 도움이 되는 일반적인 사용 사례를 다루는 각 플랫폼에 대 한 코드 샘플 모음입니다. 웹 응용 프로그램과 관련 된 코드 샘플은 JavaScript로 제공 됩니다. 그러나 Azure Maps는 [NPM 모듈](how-to-use-map-control.md)을 통해 TypeScript 정의를 추가 옵션으로 제공할 수도 있습니다.

### <a name="load-a-map"></a>지도 로드

두 SDK의 맵 로드는 동일한 단계를 따릅니다.

- 맵 SDK에 대 한 참조를 추가 합니다.
- 맵의 자리 표시자 역할을 하는 페이지 본문에 `div` 태그를 추가 합니다.
- 페이지가 로드 될 때 호출 되는 JavaScript 함수를 만듭니다.
- 각 map 클래스의 인스턴스를 만듭니다.

**몇 가지 주요 차이점**

- Google maps를 사용 하려면 API의 스크립트 참조에 계정 키를 지정 해야 합니다. Azure Maps에 대 한 인증 자격 증명은 map 클래스의 옵션으로 지정 됩니다. 구독 키 또는 Azure Active Directory 정보 일 수 있습니다.
- Google Maps는 초기화 함수를 호출 하 여 맵을 로드 하는 데 사용 되는 API의 스크립트 참조에서 콜백 함수를 사용 합니다. Azure Maps 사용 하 여 페이지의 onload 이벤트를 사용 해야 합니다.
- 맵이 렌더링 될 `div` 요소를 참조 하는 경우 Google Maps에는 `HTMLElement` 개체가 필요 하지만 Azure Maps의 `Map` 클래스에는 `id` 값만 필요 합니다.
- Azure Maps 좌표는 `[longitude, latitude]`형식으로 단순 숫자 배열로 지정할 수 있는 Position 개체로 정의 됩니다.
- Azure Maps의 확대/축소 수준은 두 플랫폼 간의 바둑판식 배열 시스템 크기 차이로 인해 Google Maps 예제 보다 한 수준 낮습니다.
- 기본적으로 Azure Maps는 확대/축소 단추 및 지도 스타일 단추와 같은 탐색 컨트롤을 지도 캔버스에 추가 하지 않습니다. 그러나 지도 스타일 선택기, 확대/축소 단추, 나침반 또는 회전 컨트롤 및 피치 컨트롤을 추가 하는 컨트롤이 있습니다.
- Azure Maps에서 이벤트 처리기를 추가 하 여 map 인스턴스의 `ready` 이벤트를 모니터링할 수 있습니다. 이는 맵이 WebGL 컨텍스트 및 필요한 모든 리소스의 로드를 완료 했을 때 발생 합니다. 이 이벤트 처리기에서 모든 사후 로드 코드를 추가할 수 있습니다.

아래 예제에서는 뉴욕 (경도:-73.985, 위도: 40.747)에서 뉴욕을 중심으로 하는 기본 지도를 로드 하는 방법을 보여 주고 Google Maps에서 확대/축소 수준 12에 있습니다.

**이전: Google Maps**

다음 코드는 위치를 중심으로 하는 Google 지도를 표시 하는 방법의 예입니다.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map;

        function initMap() {
            map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(40.747, -73.985),
                zoom: 12
            });
        }
    </script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

브라우저에서이 코드를 실행 하면 다음 이미지와 같은 지도가 표시 됩니다.

<center>

![간단한 Google Maps](media/migrate-google-maps-web-app/simple-google-map.png)</center>

**이후: Azure Maps**

다음 코드에서는 지도 스타일 컨트롤 및 확대/축소 단추와 함께 Azure Maps에서 동일한 뷰로 지도를 로드 하는 방법을 보여 줍니다.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map;

        function initMap() {
            map = new atlas.Map('myMap', {
                center: [-73.985, 40.747],  //Format coordinates as longitude, latitude.
                zoom: 11,   //Subtract the zoom level by one.

                //Specify authentication information when loading the map.
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {
                //Add zoom controls to bottom right of map.
                map.controls.add(new atlas.control.ZoomControl(), {
                    position: 'bottom-right'
                });

                //Add map style control in top left corner of map.
                map.controls.add(new atlas.control.StyleControl(), {
                    position: 'top-left'
                });
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

브라우저에서이 코드를 실행 하면 다음 이미지와 같은 지도가 표시 됩니다.

<center>

![단순 Azure Maps](media/migrate-google-maps-web-app/simple-azure-maps.png)</center>

웹 앱에서 Azure Maps 맵 컨트롤을 설정 하 고 사용 하는 방법에 대 한 자세한 설명서는 [여기](how-to-use-map-control.md)를 참조 하세요.

> [!NOTE]
> Google Maps와 달리 Azure Maps은 지도를 로드할 때 초기 중심 및 확대/축소 수준을 지정할 필요가 없습니다. 지도를 로드할 때이 정보를 제공 하지 않으면 지도에서 사용자가 속한 도시를 확인 하 고 지도를 가운데에 정렬 하 여 확대/축소 합니다.

**추가 리소스:**

- Azure Maps는 [여기](map-add-controls.md)에 설명 된 대로 지도 보기를 회전 하 고 피칭 하는 탐색 컨트롤도 제공 합니다.

### <a name="localizing-the-map"></a>맵 지역화

대상 그룹이 여러 국가에 걸쳐 분산 되어 있거나 다른 언어를 말하는 경우 지역화가 중요 합니다.

**이전: Google Maps**

Google 지도를 지역화 하려면 언어 및 지역 매개 변수가에 추가 됩니다.

```html
<script type="text/javascript" src=" https://maps.googleapis.com/maps/api/js?callback=initMap&key=[api_key]& language=[language_code]&region=[region_code]" async defer></script>
```

언어가 "fr-fr"로 설정 된 Google Maps의 예는 다음과 같습니다.

<center>

![Google Maps 지역화](media/migrate-google-maps-web-app/google-maps-localization.png)</center>

**이후: Azure Maps**

Azure Maps은 지도의 언어 및 지역 보기를 설정 하는 두 가지 다른 방법을 제공 합니다. 첫 번째 옵션은이 정보를 전역 *atlas* 네임 스페이스에 추가 하 여 앱의 모든 맵 컨트롤 인스턴스가 이러한 설정을 기본값으로 설정 하는 것입니다. 다음은 언어를 프랑스어 ("fr-fr")로 설정 하 고 국가별 보기를 "auto"로 설정 합니다.

```javascript
atlas.setLanguage('fr-FR');
atlas.setView('auto');
```

두 번째 옵션은 다음과 같이 맵을 로드할 때 맵 옵션에이 정보를 전달 하는 것입니다.

```javascript
map = new atlas.Map('myMap', {
    language: 'fr-FR',
    view: 'auto',

    authOptions: {
        authType: 'subscriptionKey',
        subscriptionKey: '<Your Azure Maps Key>'
    }
});
```

> [!NOTE]
> Azure Maps를 사용 하면 다른 언어 및 지역 설정을 사용 하 여 동일한 페이지에 여러 개의 맵 인스턴스를 로드할 수 있습니다. 또한 로드 된 후에 맵에서 이러한 설정을 업데이트할 수도 있습니다. Azure Maps 지원 되는 언어에 대 한 자세한 목록은 [여기](supported-languages.md)에서 찾을 수 있습니다.

언어가 "fr"로 설정 되 고 사용자 지역이 "fr-fr"로 설정 된 Azure Maps의 예는 다음과 같습니다.

<center>

![Azure Maps 지역화](media/migrate-google-maps-web-app/azure-maps-localization.png)</center>

### <a name="setting-the-map-view"></a>지도 보기 설정

Azure와 Google Maps의 동적 맵은 JavaScript에서 적절 한 함수를 호출 하 여 프로그래밍 방식으로 새 지리적 위치로 이동할 수 있습니다. 아래 예제에서는 지도를 위성 항공 이미지를 표시 하는 방법을 보여 줍니다. 지도를 좌표 (경도:-111.0225, 위도: 35.0272)를 사용 하 여 위치 위에 배치 하 고 Google Maps에서 확대/축소 수준을 15로 변경 합니다.

> [!NOTE]
> Google Maps는 Azure Maps 차원에서 256 픽셀인 타일을 사용 하 고는 더 큰 512 픽셀 타일을 사용 합니다. 그러면 Azure Maps에서 Google Maps와 동일한 맵 영역을 로드 하는 데 필요한 네트워크 요청 수가 줄어듭니다. 그러나 지도 컨트롤에서 타일 피라미드가 작동 하는 방식으로 인해 Azure Maps의 큰 타일이 Google Maps에서 지도와 동일한 볼 수 있는 영역을 사용 하는 것을 의미 합니다. Azure Maps 사용 하는 경우 Google Maps에서 사용 되는 확대/축소 수준을 1에서 뺍니다.

**이전: Google Maps**

Google 지도 지도 컨트롤은 지도의 중심 및 확대/축소 수준을 지정할 수 있는 `setOptions` 메서드를 사용 하 여 프로그래밍 방식으로 이동할 수 있습니다.

```javascript
map.setOptions({
    mapTypeId: google.maps.MapTypeId.SATELLITE,
    center: new google.maps.LatLng(35.0272, -111.0225),
    zoom: 15
});
```

<center>

![Google Maps 집합 보기](media/migrate-google-maps-web-app/google-maps-set-view.png)</center>

**이후: Azure Maps**

Azure Maps 맵의 `setCamera` 메서드를 사용 하 여 맵 위치를 프로그래밍 방식으로 변경할 수 있으며, `setStyle` 메서드를 사용 하 여 맵 스타일 변경 내용을 변경할 수 있습니다. Azure Maps 좌표는 "경도, 위도" 형식으로 되어 있고 확대/축소 수준 값은 1로 뺍니다.

```javascript
map.setCamera({
    center: [-111.0225, 35.0272],
    zoom: 14
});

map.setStyle({
    style: 'satellite'
});
```

<center>

![Azure Maps 집합 뷰](media/migrate-google-maps-web-app/azure-maps-set-view.jpeg)</center>

**추가 리소스:**

- [지도 스타일 선택](choose-map-style.md)
- [지원 되는 지도 스타일](supported-map-styles.md)

### <a name="adding-a-marker"></a>표식 추가

Azure Maps 지도에서 요소 데이터를 렌더링할 수 있는 여러 가지 방법이 있습니다.

- **HTML 표식** – 기존 DOM 요소를 사용 하 여 요소를 렌더링 합니다. HTML 마커는 끌기를 지원 합니다.
- **기호 계층** – WebGL 컨텍스트 내에서 아이콘 및/또는 텍스트를 사용 하 여 요소를 렌더링 합니다.
- **거품형 계층** – 지도에서 점이 원으로 렌더링 됩니다. 원의 반지름은 데이터의 속성에 따라 크기를 조정할 수 있습니다.

기호 및 거품형 레이어는 모두 WebGL 컨텍스트 내에서 렌더링 되며 지도에서 매우 큰 점의 집합을 렌더링할 수 있습니다. 이러한 계층을 통해 데이터 원본에 데이터를 저장 해야 합니다. `ready` 이벤트가 발생 한 후 데이터 원본 및 렌더링 계층을 맵에 추가 해야 합니다. HTML 마커는 페이지 내에서 DOM 요소로 렌더링 되며 데이터 원본을 사용 하지 않습니다. 페이지의 DOM 요소가 많을 수록 페이지의 속도가 느려집니다. 지도에 몇 개 이상의 요소를 렌더링 하는 경우에는 렌더링 계층 중 하나를 대신 사용 하는 것이 좋습니다.

다음 예제에서는 숫자 10이 레이블로 중첩 된 (경도:-0.2, 위도: 51.5)에서 지도에 표식을 추가 합니다.

**이전: Google Maps**

Google Maps를 사용 하면 `google.maps.Marker` 클래스를 사용 하 여 지도에 표식을 추가 하 고 옵션 중 하나로 맵을 지정 합니다.

```javascript
//Create a marker and add it to the map.
var marker = new google.maps.Marker({
    position: new google.maps.LatLng(51.5, -0.2),
    label: '10',
    map: map
});
```

<center>

Google Maps 표식 ![](media/migrate-google-maps-web-app/google-maps-marker.png)</center>

**이후: HTML 표식을 사용 하 Azure Maps**

Azure Maps에서는 HTML 표식을 사용 하 여 지도의 점을 표시할 수 있으며, 지도에 적은 수의 지점만 표시 하기만 하면 되는 앱에만 권장 됩니다. HTML 마커를 사용 하려면 `atlas.HtmlMarker` 클래스의 인스턴스를 만들고, 텍스트 및 위치 옵션을 설정 하 고, `map.markers.add` 메서드를 사용 하 여 지도에 표식을 추가 하면 됩니다.

```javascript
//Create a HTML marker and add it to the map.
map.markers.add(new atlas.HtmlMarker({
    text: '10',
    position: [-0.2, 51.5]
}));
```

<center>

HTML 마커를 Azure Maps ![](media/migrate-google-maps-web-app/azure-maps-html-marker.png)</center>

**이후: 기호 레이어를 사용 하 여 Azure Maps**

기호 계층을 사용 하는 경우 데이터 원본 및 계층에 연결 된 데이터 원본에 데이터를 추가 해야 합니다. 또한 `ready` 이벤트가 발생 한 후에 데이터 원본 및 계층을 맵에 추가 해야 합니다. 기호 위에 고유한 텍스트 값을 렌더링 하려면 텍스트 정보를 데이터 요소의 속성으로 저장 하 고 해당 속성을 계층의 `textField` 옵션에서 참조 해야 합니다. 이 작업은 HTML 표식을 사용 하는 것 보다 좀 더 많은 작업을 수행 하지만 많은 성능상의 이점을 제공 합니다.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map, datasource;

        function initMap() {
            map = new atlas.Map('myMap', {
                center: [-0.2, 51.5],
                zoom: 9,
                
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create a data source and add it to the map.
                datasource = new atlas.source.DataSource();
                map.sources.add(datasource);

                //Create a point feature, add a property to store a label for it, and add it to the data source.
                datasource.add(new atlas.data.Feature(new atlas.data.Point([-0.2, 51.5]), {
                    label: '10'
                }));

                //Add a layer for rendering point data as symbols.
                map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
                    textOptions: {
                        //Use the label property to populate the text for the symbols.
                        textField: ['get', 'label'],
                        color: 'white',
                        offset: [0, -1]
                    }
                }));
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Azure Maps 기호 계층](media/migrate-google-maps-web-app/azure-maps-symbol-layer.png)</center>

**추가 리소스:**

- [데이터 원본 만들기](create-data-source-web-sdk.md)
- [기호 계층 추가](map-add-pin.md)
- [거품형 계층 추가](map-add-bubble-layer.md)
- [클러스터 지점 데이터](clustering-point-data-web-sdk.md)
- [HTML 표식 추가](map-add-custom-html.md)
- [데이터 기반 스타일 식 사용](data-driven-style-expressions-web-sdk.md)
- [기호 계층 아이콘 옵션](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions?view=azure-iot-typescript-latest)
- [기호 계층 텍스트 옵션](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions?view=azure-iot-typescript-latest)
- [HTML 표식 클래스](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)
- [HTML 표식 옵션](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkeroptions?view=azure-iot-typescript-latest)

### <a name="adding-a-custom-marker"></a>사용자 지정 표식 추가

사용자 지정 이미지를 사용 하 여 지도의 요소를 나타낼 수 있습니다. 아래 예제에서 사용 되는 다음 이미지는 사용자 지정 이미지를 사용 하 여 지도의 점을 (위도: 51.5, 경도:-0.2) 표시 하 고 압정 아이콘의 점이 지도의 올바른 위치에 맞게 조정 되도록 표식의 위치를 오프셋 합니다.

<center>

![노란색 압정 이미지](media/migrate-google-maps-web-app/ylw_pushpin.png)<br/>
ylw\_압정</center>

**이전: Google Maps**

Google Maps에서 이미지에 대 한 `url`를 포함 하는 `Icon` 개체를 지정 하 여 사용자 지정 표식을 만들고 압정 이미지의 점을 맵의 좌표와 맞춥니다 `anchor` 점입니다. Google 지도에서 이미지의 왼쪽 위 모퉁이를 기준으로 하는 앵커 값입니다.

```javascript
var marker = new google.maps.Marker({
    position: new google.maps.LatLng(51.5, -0.2),
    icon: {
        url: 'https://azuremapscodesamples.azurewebsites.net/Common/images/icons/ylw-pushpin.png',
        anchor: new google.maps.Point(5, 30)
    },
    map: map
});
```

<center>

![Google Maps 사용자 지정 표식](media/migrate-google-maps-web-app/google-maps-custom-marker.png)</center>

**이후: HTML 표식을 사용 하 Azure Maps**

Html `string` 또는 `HTMLElement` Azure Maps에서 HTML 표식을 사용자 지정 하려면 표식의 `htmlContent` 옵션에 전달할 수 있습니다. Azure Maps에서 `anchor` 옵션은 9 개의 정의 된 참조 지점 중 하나를 사용 하 여 위치 좌표를 기준으로 표식의 상대 위치를 지정 하는 데 사용 됩니다. "가운데", "위쪽", "아래쪽", "왼쪽", "오른쪽", "왼쪽 위", "오른쪽 위", "왼쪽 끝", "오른쪽 아래" 콘텐츠는 기본적으로 html 콘텐츠의 하단 가운데에 고정 됩니다. Google Maps에서 코드를 보다 쉽게 마이그레이션하려면 `anchor`를 "왼쪽 위"로 설정 하 고 Google Maps에서 사용 되는 것과 동일한 오프셋을 사용 하 여 `pixelOffset` 옵션을 사용 합니다. Azure Maps의 오프셋은 Google Maps의 반대 방향으로 이동 하므로 1을 빼서 곱합니다.

> [!TIP]
> Microsoft Edge에서 원치 않는 아이콘을 표시 하는 기본 끌기 동작을 사용 하지 않도록 설정 하려면 `pointer-events:none`을 html 콘텐츠에 대 한 스타일로 추가 합니다.

```javascript
map.markers.add(new atlas.HtmlMarker({
    htmlContent: '<img src="https://azuremapscodesamples.azurewebsites.net/Common/images/icons/ylw-pushpin.png" style="pointer-events: none;" />',
    anchor: 'top-left',
    pixelOffset: [-5, -30],
    position: [-0.2, 51.5]
}));
```

<center>

![Azure Maps 사용자 지정 HTML 표식](media/migrate-google-maps-web-app/azure-maps-custom-html-marker.png)</center>

**이후: 기호 레이어를 사용 하 여 Azure Maps**

Azure Maps 기호 계층은 사용자 지정 이미지도 지원 하지만 먼저 이미지를 지도 리소스에 로드 하 고 고유한 ID를 할당 해야 합니다. 그러면 기호 계층에서이 ID를 참조할 수 있습니다. 아이콘 `offset` 옵션을 사용 하 여 이미지의 올바른 점에 맞게 기호를 오프셋할 수 있습니다. Azure Maps에서 `anchor` 옵션은 9 개의 정의 된 참조 지점 중 하나를 사용 하 여 위치 좌표를 기준으로 기호의 상대 위치를 지정 하는 데 사용 됩니다. "가운데", "위쪽", "아래쪽", "왼쪽", "오른쪽", "왼쪽 위", "오른쪽 위", "왼쪽 끝", "오른쪽 아래" 콘텐츠는 기본적으로 html 콘텐츠의 하단 가운데에 고정 됩니다. Google Maps에서 코드를 보다 쉽게 마이그레이션하려면 `anchor`를 "왼쪽 위"로 설정 하 고 Google Maps에서 사용 되는 것과 동일한 오프셋을 사용 하 여 `offset` 옵션을 사용 합니다. Azure Maps의 오프셋은 Google Maps의 반대 방향으로 이동 하므로 1을 빼서 곱합니다.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map, datasource;

        function initMap() {
            map = new atlas.Map('myMap', {
                center: [-0.2, 51.5],
                zoom: 9,
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Load the custom image icon into the map resources.
                map.imageSprite.add('my-yellow-pin', 'https://azuremapscodesamples.azurewebsites.net/Common/images/icons/ylw-pushpin.png').then(function () {

                    //Create a data source and add it to the map.
                    datasource = new atlas.source.DataSource();
                    map.sources.add(datasource);

                    //Create a point and add it to the data source.
                    datasource.add(new atlas.data.Point([-0.2, 51.5]));

                    //Add a layer for rendering point data as symbols.
                    map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
                        iconOptions: {
                            //Set the image option to the id of the custom icon that was loaded into the map resources.
                            image: 'my-yellow-pin',
                            anchor: 'top-left',
                            offset: [-5, -30]
                        }
                    }));
                });
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

Azure Maps 사용자 지정 아이콘 기호 계층을 ![](media/migrate-google-maps-web-app/azure-maps-custom-icon-symbol-layer.png)</center>

> [!TIP]
> 점의 고급 사용자 지정 렌더링을 만들려면 여러 렌더링 계층을 함께 사용 합니다. 예를 들어 각 색의 여러 이미지를 생성 하는 대신 각 색에 대 한 여러 개의 이미지를 생성 하는 대신 각 색의 여러 이미지를 생성 하는 것이 아니라 거품형 계층을 기반으로 하는 기호 계층을 압정 하 여 동일한 데이터 원본을 참조 하도록 하려는 경우를 예로 들 수 있습니다. 이렇게 하면를 만드는 것 보다 훨씬 효율적이 고 지도에서 다양 한 이미지를 유지 관리할 수 있습니다.

**추가 리소스:**

- [데이터 원본 만들기](create-data-source-web-sdk.md)
- [기호 계층 추가](map-add-pin.md)
- [HTML 표식 추가](map-add-custom-html.md)
- [데이터 기반 스타일 식 사용](data-driven-style-expressions-web-sdk.md)
- [기호 계층 아이콘 옵션](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions?view=azure-iot-typescript-latest)
- [기호 계층 텍스트 옵션](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions?view=azure-iot-typescript-latest)
- [HTML 표식 클래스](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)
- [HTML 표식 옵션](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkeroptions?view=azure-iot-typescript-latest)

### <a name="adding-a-polyline"></a>다중선 추가

다중선은 지도의 선 또는 경로를 나타내는 데 사용 됩니다. 다음 예에서는 지도에서 파선 다중선을 만드는 방법을 보여 줍니다.

**이전: Google Maps**

Google Maps에서 폴리라인 클래스는 일련의 옵션을 사용 합니다. 좌표 배열은 다중선의 `path` 옵션으로 전달 됩니다.

```javascript
//Get the center of the map.
var center = map.getCenter();

//Define a symbol using SVG path notation, with an opacity of 1.
var lineSymbol = {
    path: 'M 0,-1 0,1',
    strokeOpacity: 1,
    scale: 4
};

//Create the polyline.
var line = new google.maps.Polyline({
    path: [
        center,
        new google.maps.LatLng(center.lat() - 0.5, center.lng() - 1),
        new google.maps.LatLng(center.lat() - 0.5, center.lng() + 1)
    ],
    strokeColor: 'red',
    strokeOpacity: 0,
    strokeWeight: 4,
    icons: [{
        icon: lineSymbol,
        offset: '0',
        repeat: '20px'
    }]
});

//Add the polyline to the map.
line.setMap(map);
```

<center>

![Google Maps 폴리라인](media/migrate-google-maps-web-app/google-maps-polyline.png)</center>

**이후: Azure Maps**

Azure Maps에서는 다중선을 LineString 또는 MultiLineString 개체 라고 합니다. 이러한 개체를 데이터 원본에 추가 하 고 선 계층을 사용 하 여 렌더링할 수 있습니다.

```javascript
//Get the center of the map.
var center = map.getCamera().center;

//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Create a line and add it to the data source.
datasource.add(new atlas.data.LineString([
    center,
    [center[0] - 1, center[1] - 0.5],
    [center[0] + 1, center[1] - 0.5]
]));

//Add a layer for rendering line data.
map.layers.add(new atlas.layer.LineLayer(datasource, null, {
    strokeColor: 'red',
    strokeWidth: 4,
    strokeDashArray: [3, 3]
}));
```

<center>

![Azure Maps 폴리라인](media/migrate-google-maps-web-app/azure-maps-polyline.png)</center>

**추가 리소스:**

- [맵에 선 추가](map-add-line-layer.md)
- [선 계층 옵션](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)
- [데이터 기반 스타일 식 사용](data-driven-style-expressions-web-sdk.md)

### <a name="adding-a-polygon"></a>다각형 추가

다각형은 지도의 영역을 나타내는 데 사용 됩니다. Azure Maps 및 Google Maps는 다각형에 대해 매우 유사한 지원을 제공 합니다. 다음 예에서는 지도의 가운데 좌표를 기준으로 삼각형을 형성 하는 다각형을 만드는 방법을 보여 줍니다.

**이전: Google Maps**

Google Maps에서 Polygon 클래스는 일련의 옵션을 사용 합니다. 좌표의 배열이 다각형의 `paths` 옵션으로 전달 됩니다.

```javascript
//Get the center of the map.
var center = map.getCenter();

//Create a polygon.
var polygon = new google.maps.Polygon({
    paths: [
        center,
        new google.maps.LatLng(center.lat() - 0.5, center.lng() - 1),
        new google.maps.LatLng(center.lat() - 0.5, center.lng() + 1),
        center
    ],
    strokeColor: 'red',
    strokeWeight: 2,
    fillColor: 'rgba(0, 255, 0, 0.5)'
});

//Add the polygon to the map
polygon.setMap(map);
```

<center>

Google 지도 polygon을 ![](media/migrate-google-maps-web-app/google-maps-polygon.png)</center>

**이후: Azure Maps**

Azure Maps에서 Polygon 및 MultiPolygon 개체는 데이터 원본에 추가 하 고 레이어를 사용 하 여 지도에서 렌더링할 수 있습니다. 다각형의 면적은 다각형 계층에서 렌더링 될 수 있습니다. 다각형의 윤곽선은 선 계층을 사용 하 여 렌더링할 수 있습니다.

```javascript
//Get the center of the map.
var center = map.getCamera().center;

//Create a data source and add it to the map.
datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Create a polygon and add it to the data source.
datasource.add(new atlas.data.Polygon([
    center,
    [center[0] - 1, center[1] - 0.5],
    [center[0] + 1, center[1] - 0.5],
    center
]));

//Add a polygon layer for rendering the polygon area.
map.layers.add(new atlas.layer.PolygonLayer(datasource, null, {
    fillColor: 'rgba(0, 255, 0, 0.5)'
}));

//Add a line layer for rendering the polygon outline.
map.layers.add(new atlas.layer.LineLayer(datasource, null, {
    strokeColor: 'red',
    strokeWidth: 2
}));
```

<center>

![Azure Maps polygon](media/migrate-google-maps-web-app/azure-maps-polygon.png)</center>

**추가 리소스:**

- [지도에 다각형 추가](map-add-shape.md)
- [지도에 원 추가](map-add-shape.md#add-a-circle-to-the-map)
- [다각형 계층 옵션](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest)
- [선 계층 옵션](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)
- [데이터 기반 스타일 식 사용](data-driven-style-expressions-web-sdk.md)

### <a name="display-an-info-window"></a>정보 창 표시

엔터티에 대 한 추가 정보는 Google Maps의 `google.maps.InfoWindow` 클래스로 지도에 표시 될 수 있습니다. Azure Maps에서 `atlas.Popup` 클래스를 사용 하 여이를 달성할 수 있습니다. 다음 예에서는 맵에 표식을 추가 하 고 클릭 하면 정보 창/팝업을 표시 합니다.

**이전: Google Maps**

Google Maps를 사용 하면 `google.maps.InfoWindow` 생성자를 사용 하 여 정보 창을 만들 수 있습니다.

```javascript
//Add a marker in which to display an infowindow for.
var marker = new google.maps.Marker({
    position: new google.maps.LatLng(47.6, -122.33),
    map: map
});

//Create an infowindow.
var infowindow = new google.maps.InfoWindow({
    content: '<div style="padding:5px"><b>Hello World!</b></div>'
});

//Add a click event to the marker to open the infowindow.
marker.addListener('click', function () {
    infowindow.open(map, marker);
});
```

<center>

Google Maps 팝업을 ![](media/migrate-google-maps-web-app/google-maps-popup.png)</center>

**이후: Azure Maps**

Azure Maps 팝업을 사용 하 여 위치에 대 한 추가 정보를 표시할 수 있습니다. HTML `string` 또는 `HTMLElement` 개체를 팝업의 `content` 옵션에 전달할 수 있습니다. 팝업은 원하는 경우 모든 모양과 독립적으로 표시 될 수 있으므로 `position` 값을 지정 해야 합니다. Popup을 표시 하려면 `open` 메서드를 호출 하 고 팝업이 표시 될 `map`를 전달 합니다.

```javascript
//Add a marker to the map in which to display a popup for.
var marker = new atlas.HtmlMarker({
    position: [-122.33, 47.6]
});

//Add the marker to the map.
map.markers.add(marker);

//Create a popup.
var popup = new atlas.Popup({
    content: '<div style="padding:5px"><b>Hello World!</b></div>',
    position: [-122.33, 47.6],
    pixelOffset: [0, -35]
});

//Add a click event to the marker to open the popup.
map.events.add('click', marker, function () {
    //Open the popup
    popup.open(map);
});
```

<center>

![Azure Maps popup](media/migrate-google-maps-web-app/azure-maps-popup.png)</center>

> [!NOTE]
> 기호, 거품형, 선 또는 다각형 계층을 사용 하 여 동일한 작업을 수행 하려면 마커 대신 지도 이벤트 코드로 계층을 전달 하면 됩니다.

**추가 리소스:**

- [팝업 추가](map-add-popup.md)
- [미디어 콘텐츠가 포함 된 팝업](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Popup%20with%20Media%20Content)
- [모양의 팝업](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Popups%20on%20Shapes)
- [여러 핀으로 Popup 다시 사용](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Reusing%20Popup%20with%20Multiple%20Pins)
- [Popup 클래스](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)
- [Popup 옵션](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popupoptions?view=azure-iot-typescript-latest)

### <a name="import-a-geojson-file"></a>GeoJSON 파일 가져오기

Google Maps는 `google.maps.Data` 클래스를 통해 GeoJSON 데이터의 로드 및 동적 스타일을 지원 합니다. 이 클래스의 기능은 Azure Maps의 데이터 기반 스타일을 사용 하 여 훨씬 더 잘 정렬 됩니다. 한 가지 중요 한 차이점은 Google Maps에서 콜백 함수를 지정 하 고 UI 스레드에서 개별적으로 처리 한 각 기능의 스타일을 지정 하기 위한 비즈니스 논리를 지정 한다는 것입니다. Azure Maps에서 계층은 데이터 기반 식을 스타일 지정 옵션으로 지정 하는 기능을 지원 합니다. 이러한 식은 렌더링 시 별도의 스레드에서 처리 되 고 렌더링 성능을 향상 시키고 더 큰 데이터 집합을 더 빠르게 렌더링할 수 있습니다.

다음 예에서는 USGS에서 지난 7 일 동안 모든 지진의 GeoJSON 피드를 로드 하 고 지도에서 크기가 조정 된 원으로 렌더링 합니다. 각 원의 색과 배율은 데이터 집합에 있는 각 기능의 `"mag"` 속성에 저장 되는 각 지진의 크기를 기준으로 합니다. 크기가 5 보다 크거나 같은 경우에는 원이 빨간색으로 나타납니다. 3 보다 크거나 같고 5 보다 작은 경우에는 원이 주황색으로 나타납니다. 3 보다 작은 경우 원이 녹색으로 나타납니다. 각 원의 반지름은 크기의 지 수에 0.1을 곱한 값입니다.

**이전: Google Maps**

Google Maps에서 `map.data.loadGeoJson` 메서드를 통해 GeoJSON 피드에서 로드 된 각 기능에 비즈니스 논리를 적용 하는 데 사용 되는 `map.data.setStyle` 메서드에서 단일 콜백 함수를 지정할 수 있습니다.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson';

        function initMap() {
            map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(20, -160),
                zoom: 2
            });

            //Define a callback to style each feature.
            map.data.setStyle(function (feature) {

                //Extract the 'mag' property from the feature.
                var mag = parseFloat(feature.getProperty('mag'));

                //Set the color value to 'green' by default.
                var color = 'green';

                //If the mag value is greater than 5, set the color to 'red'.
                if (mag >= 5) {
                    color = 'red';
                }
                //If the mag value is greater than 3, set the color to 'orange'.
                else if (mag >= 3) {
                    color = 'orange';
                }

                return /** @type {google.maps.Data.StyleOptions} */({
                    icon: {
                        path: google.maps.SymbolPath.CIRCLE,

                        //Scale the radius based on an exponential of the 'mag' value.
                        scale: Math.exp(mag) * 0.1,
                        fillColor: color,
                        fillOpacity: 0.75,
                        strokeWeight: 2,
                        strokeColor: 'white'
                    }
                });
            });

            //Load the data feed.
            map.data.loadGeoJson(earthquakeFeed);
        }
    </script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Google Maps GeoJSON](media/migrate-google-maps-web-app/google-maps-geojson.png)</center>

**이후: Azure Maps**

GeoJSON는 Azure Maps의 기본 데이터 형식이 며 `datasource.importFromUrl` 메서드를 사용 하 여 데이터 원본으로 쉽게 가져올 수 있습니다. 거품형 계층은 데이터 원본의 기능 속성을 기반으로 크기가 조정 된 원을 렌더링 하기 위한 기능을 제공 합니다. 콜백 함수를 포함 하는 대신 비즈니스 논리가 식으로 변환 되 고 스타일 옵션으로 전달 됩니다. 식은 다른 스레드로 전달 되 고 기능 데이터에 대해 평가 될 수 있도록 비즈니스 논리가 작동 하는 방식을 정의 합니다. 서로 다른 비즈니스 논리를 사용 하 여 여러 데이터 원본 및 계층을 Azure Maps에 추가 하 여 여러 데이터 집합을 여러 가지 방식으로 지도에서 렌더링할 수 있습니다.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson';

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                center: [-160, 20],
                zoom: 1,

                //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create a data source and add it to the map.
                datasource = new atlas.source.DataSource();
                map.sources.add(datasource);

                //Load the earthquake data.
                datasource.importDataFromUrl(earthquakeFeed);

                //Create a layer to render the data points as scaled circles.
                map.layers.add(new atlas.layer.BubbleLayer(datasource, null, {
                    //Make the circles semi-transparent.
                    opacity: 0.75,

                    color: [
                        'case',

                        //If the mag value is greater than 5, return 'red'.
                        ['>=', ['get', 'mag'], 5],
                        'red',

                        //If the mag value is greater than 3, return 'orange'.
                        ['>=', ['get', 'mag'], 3],
                        'orange',

                        //Return 'green' as a fallback.
                        'green'
                    ],

                    //Scale the radius based on an exponential of the 'mag' value.
                    radius: ['*', ['^', ['e'], ['get', 'mag']], 0.1]
                }));
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Azure Maps GeoJSON](media/migrate-google-maps-web-app/azure-maps-geojson.png)</center>

**추가 리소스:**

- [기호 계층 추가](map-add-pin.md)
- [거품형 계층 추가](map-add-bubble-layer.md)
- [클러스터 지점 데이터](clustering-point-data-web-sdk.md)
- [데이터 기반 스타일 식 사용](data-driven-style-expressions-web-sdk.md)

### <a name="marker-clustering"></a>표식 클러스터링

지도에서 많은 데이터 요소를 시각화할 때 점수가 서로 겹치면 지도는 복잡 하 게 보이고 보기 및 사용 하기 어려워집니다. Point 데이터의 클러스터링을 사용 하 여 이러한 사용자 환경을 개선 하 고 성능을 향상 시킬 수 있습니다. 클러스터링 지점 데이터는 서로 가까이 있는 점 데이터를 결합 하 여 단일 클러스터형 데이터 요소로 맵에 표시 하는 프로세스입니다. 사용자가 지도를 확대 하면 클러스터는 개별 데이터 요소를 분리 합니다.

다음 예에서는 지난 주에 있는 지진 데이터의 GeoJSON 피드를 로드 하 여 지도에 추가 합니다. 클러스터는 포함 된 요소 수에 따라 크기가 조정 되 고 색 원으로 렌더링 됩니다.

> [!NOTE]
> 표식 클러스터링에는 여러 가지 알고리즘이 사용 됩니다. Google 및 Azure Maps는 약간 다른 알고리즘을 사용 합니다. 따라서 때때로 클러스터의 지점 배포는 달라질 수 있습니다.

**이전: Google Maps**

Google Maps 마커는 MarkerClusterer 라이브러리에서 로드 하 여 클러스터링 할 수 있습니다. 클러스터 아이콘은 이름이 1에서 5 사이이 고 동일한 디렉터리에 호스트 되는 이미지인 것으로 제한 됩니다.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson';

        function initMap() {
            map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(20, -160),
                zoom: 2
            });

            //Download the GeoJSON data.
            fetch(earthquakeFeed)
                .then(function (response) {
                    return response.json();
                }).then(function (data) {
                    //Loop through the GeoJSON data and create a marker for each data point.
                    var markers = [];

                    for (var i = 0; i < data.features.length; i++) {

                        markers.push(new google.maps.Marker({
                            position: new google.maps.LatLng(data.features[i].geometry.coordinates[1], data.features[i].geometry.coordinates[0])
                        }));
                    }

                    //Create a marker clusterer instance and tell it where to find the cluster icons.
                    var markerCluster = new MarkerClusterer(map, markers,
                        { imagePath: 'https://developers.google.com/maps/documentation/javascript/examples/markerclusterer/m' });
                });
        }
    </script>

    <!-- Load the marker cluster library. -->
    <script src="https://developers.google.com/maps/documentation/javascript/examples/markerclusterer/markerclusterer.js"></script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

Google Maps 클러스터링을 ![](media/migrate-google-maps-web-app/google-maps-clustering.png)</center>

**이후: Azure Maps**

Azure Maps 데이터 소스에서 데이터를 추가 하 고 관리 합니다. 레이어는 데이터 원본에 연결 하 여 데이터를 렌더링 합니다. Azure Maps의 `DataSource` 클래스는 몇 가지 클러스터링 옵션을 제공 합니다.

- `cluster` – 데이터 원본에 클러스터 지점 데이터를 알려 줍니다.
- `clusterRadius`-클러스터링 하는 데 적용할 픽셀의 반경 (픽셀)입니다.
- `clusterMaxZoom`-클러스터링이 발생 하는 최대 확대/축소 수준입니다. 이를 확대 하는 경우 모든 점이 기호로 렌더링 됩니다.
- `clusterProperties`-각 클러스터 내의 모든 지점에 대해 식을 사용 하 여 계산 되 고 각 클러스터 지점의 속성에 추가 되는 사용자 지정 속성을 정의 합니다.

클러스터링을 사용 하도록 설정 하면 데이터 원본에서 렌더링을 위해 클러스터형 및 비클러스터형 데이터 요소를 계층으로 보냅니다. 데이터 원본은 수백 개의 데이터 요소를 클러스터링 할 수 있습니다. 클러스터 된 데이터 요소에는 다음과 같은 속성이 있습니다.

| 속성 이름             | 유형    | Description   |
|---------------------------|---------|---------------|
| `cluster`                 | boolean | 기능이 클러스터를 나타내는지 여부를 나타냅니다. |
| `cluster_id`              | 문자열  | DataSource `getClusterExpansionZoom`, `getClusterChildren`및 `getClusterLeaves` 메서드와 함께 사용할 수 있는 클러스터의 고유 ID입니다. |
| `point_count`             | number  | 클러스터에 포함 된 지점의 수입니다.  |
| `point_count_abbreviated` | 문자열  | Long 인 경우 `point_count` 값을 줄여서 표시 하는 문자열입니다. (예를 들어 4000은 4K가 됨)  |

`DataSource` 클래스에는 `cluster_id`를 사용 하 여 클러스터에 대 한 추가 정보에 액세스 하는 다음과 같은 도우미 함수가 있습니다.

| 방법 | 반환 형식 | Description |
|--------|-------------|-------------|
| `getClusterChildren(clusterId: number)` | &lt;배열&lt;기능&lt;기 하 도형,&gt; \| 셰이프&gt; | 다음 확대/축소 수준에서 지정 된 클러스터의 자식을 검색 합니다. 이러한 자식은 도형과 하위 클러스터의 조합일 수 있습니다. 하위 클러스터는 ClusteredProperties와 일치 하는 속성이 있는 기능입니다. |
| `getClusterExpansionZoom(clusterId: number)` | 약속&lt;번호&gt; | 클러스터가 확장 또는 분리를 시작 하는 확대/축소 수준을 계산 합니다. |
| `getClusterLeaves(clusterId: number, limit: number, offset: number)` | &lt;배열&lt;기능&lt;기 하 도형,&gt; \| 셰이프&gt; | 클러스터에 있는 모든 요소를 검색 합니다. `limit`를 설정 하 여 점의 하위 집합을 반환 하 고 `offset`를 사용 하 여 요소를 통해 페이지를 이동 합니다. |

맵에서 클러스터 된 데이터를 렌더링할 때 두 개 이상의 계층을 사용 하는 것이 가장 쉬운 경우가 많습니다. 다음 예에서는 클러스터의 크기를 기반으로 크기가 조정 된 색이 지정 된 원을 그리는 데 사용 되는 거품형 계층, 클러스터 크기를 텍스트로 렌더링 하기 위한 기호 계층, 비클러스터형 요소를 렌더링 하기 위한 두 번째 기호 계층 등 세 가지 계층을 사용 합니다. [클러스터 지점 데이터](clustering-point-data-web-sdk.md) 설명서에 강조 표시 된 Azure Maps에서 클러스터 된 데이터를 렌더링 하는 다른 여러 가지 방법이 있습니다.

GeoJSON 데이터는 `DataSource` 클래스의 `importDataFromUrl` 함수를 사용 하 여 Azure Maps에서 직접 가져올 수 있습니다.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map, datasource;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson';

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                center: [-160, 20],
                zoom: 1,

                //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create a data source and add it to the map.
                datasource = new atlas.source.DataSource(null, {
                    //Tell the data source to cluster point data.
                    cluster: true
                });
                map.sources.add(datasource);

                //Create layers for rendering clusters, their counts and unclustered points and add the layers to the map.
                map.layers.add([
                    //Create a bubble layer for rendering clustered data points.
                    new atlas.layer.BubbleLayer(datasource, null, {
                        //Scale the size of the clustered bubble based on the number of points inthe cluster.
                        radius: [
                            'step',
                            ['get', 'point_count'],
                            20,         //Default of 20 pixel radius.
                            100, 30,    //If point_count >= 100, radius is 30 pixels.
                            750, 40     //If point_count >= 750, radius is 40 pixels.
                        ],

                        //Change the color of the cluster based on the value on the point_cluster property of the cluster.
                        color: [
                            'step',
                            ['get', 'point_count'],
                            'lime',            //Default to lime green. 
                            100, 'yellow',     //If the point_count >= 100, color is yellow.
                            750, 'red'         //If the point_count >= 750, color is red.
                        ],
                        strokeWidth: 0,
                        filter: ['has', 'point_count'] //Only rendered data points which have a point_count property, which clusters do.
                    }),

                    //Create a symbol layer to render the count of locations in a cluster.
                    new atlas.layer.SymbolLayer(datasource, null, {
                        iconOptions: {
                            image: 'none' //Hide the icon image.
                        },
                        textOptions: {
                            textField: ['get', 'point_count_abbreviated'],
                            offset: [0, 0.4]
                        }
                    }),

                    //Create a layer to render the individual locations.
                    new atlas.layer.SymbolLayer(datasource, null, {
                        filter: ['!', ['has', 'point_count']] //Filter out clustered points from this layer.
                    })
                ]);

                //Retrieve a GeoJSON data set and add it to the data source. 
                datasource.importDataFromUrl(earthquakeFeed);
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

클러스터링 Azure Maps ![](media/migrate-google-maps-web-app/azure-maps-clustering.png)</center>

**추가 리소스:**

- [기호 계층 추가](map-add-pin.md)
- [거품형 계층 추가](map-add-bubble-layer.md)
- [클러스터 지점 데이터](clustering-point-data-web-sdk.md)
- [데이터 기반 스타일 식 사용](data-driven-style-expressions-web-sdk.md)

### <a name="add-a-heat-map"></a>열 지도 추가

열 지도(또는 요소 밀도 맵)는 여러 색을 사용하여 데이터 밀도를 나타내기 위해 사용하는 일종의 데이터 시각화입니다. 맵에 데이터 “핫 스폿”을 표시하기 위해 사용되는 경우가 많으며 큰 요소 데이터 세트를 렌더링하는 좋은 방법입니다.

다음 예에서는 USGS에서 지난 달에 모든 지진의 GeoJSON 피드를 로드 하 고 `"mag"` 속성이 가중치로 사용 되는가 중 열 맵으로 렌더링 합니다.

**이전: Google Maps**

Google Maps에서 열 지도를 만들려면 API 스크립트 URL에 `&libraries=visualization`를 추가 하 여 "시각화" 라이브러리를 로드 해야 합니다. Google Maps의 열 지도 계층은 GeoJSON 데이터를 직접 지원 하지 않으며 대신 데이터를 먼저 다운로드 하 여 가중치가 적용 된 데이터 요소 배열로 변환 해야 합니다.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_month.geojson';

        function initMap() {

            var map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(20, -160),
                zoom: 2,
                mapTypeId: 'satellite'
            });

            //Download the GeoJSON data.
            fetch(url).then(function (response) {
                return response.json();
            }).then(function (res) {
                var points = getDataPoints(res);

                var heatmap = new google.maps.visualization.HeatmapLayer({
                    data: points
                });
                heatmap.setMap(map);
            });
        }

        function getDataPoints(geojson, weightProp) {
            var points = [];

            for (var i = 0; i < geojson.features.length; i++) {
                var f = geojson.features[i];

                if (f.geometry.type === 'Point') {
                    points.push({
                        location: new google.maps.LatLng(f.geometry.coordinates[1], f.geometry.coordinates[0]),
                        weight: f.properties[weightProp]
                    });
                }
            }

            return points;
        } 
    </script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]&libraries=visualization" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Google 지도 열 지도](media/migrate-google-maps-web-app/google-maps-heatmap.png)</center>

**이후: Azure Maps**

Azure Maps에서 GeoJSON 데이터를 데이터 원본에 로드 하 고 데이터 원본을 열 지도 계층에 연결 합니다. 가중치에 사용할 속성은 식을 사용 하 여 `weight` 옵션으로 전달할 수 있습니다. GeoJSON 데이터는 `DataSource` 클래스의 `importDataFromUrl` 함수를 사용 하 여 Azure Maps에서 직접 가져올 수 있습니다.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_month.geojson';

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                center: [-160, 20],
                zoom: 1,
                style: 'satellite',

                //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create a data source and add it to the map.
                datasource = new atlas.source.DataSource();
                map.sources.add(datasource);

                //Load the earthquake data.
                datasource.importDataFromUrl(earthquakeFeed);

                //Create a layer to render the data points as a heat map.
                map.layers.add(new atlas.layer.HeatMapLayer(datasource, null, {
                    weight: ['get', 'mag'],
                    intensity: 0.005,
                    opacity: 0.65,
                    radius: 10
                }));
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

열 지도를 Azure Maps ![](media/migrate-google-maps-web-app/azure-maps-heatmap.png)</center>

**추가 리소스:**

- [열 지도 계층 추가](map-add-heat-map-layer.md)
- [열 지도 계층 클래스](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.heatmaplayer?view=azure-iot-typescript-latest)
- [열 지도 계층 옵션](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest)
- [데이터 기반 스타일 식 사용](data-driven-style-expressions-web-sdk.md)

### <a name="overlay-a-tile-layer"></a>타일 계층 오버레이

Google Maps에서 이미지 오버레이가 라고도 하는 타일 계층은 지도 바둑판식 배열 시스템과 맞춘 작은 바둑판식 이미지에 분할 된 작은 이미지를 오버레이 하는 데 사용할 수 있습니다. 이는 큰 이미지나 매우 큰 데이터 집합을 겹치게 하는 일반적인 방법입니다.

다음 예에서는 아이오 환경적 Mesonet 아이오 State 대학의 날씨 레이더 타일 계층을 오버레이 합니다.

**이전: Google Maps**

Google Maps에서 타일 계층은 `google.maps.ImageMapType` 클래스를 사용 하 여 만들 수 있습니다.

```javascript
map.overlayMapTypes.insertAt(0, new google.maps.ImageMapType({
    getTileUrl: function (coord, zoom) {
        return "https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/" + zoom + "/" + coord.x + "/" + coord.y;
    },
    tileSize: new google.maps.Size(256, 256),
    opacity: 0.8
}));
```

<center>

Google Maps 타일 계층을 ![](media/migrate-google-maps-web-app/google-maps-tile-layer.png)</center>

**이후: Azure Maps**

Azure Maps에서 타일 계층을 다른 레이어와 거의 같은 방식으로 지도에 추가할 수 있습니다. X, y, zoom 자리 표시자를 포함 하는 형식이 지정 된 URL입니다. `{x}`, `{y}`, `{z}`는 각각 타일에 액세스할 위치를 계층에 알리는 데 사용 됩니다. Azure Maps 타일 계층은 `{quadkey}`, `{bbox-epsg-3857}` 및 `{subdomain}` 자리 표시자도 지원 합니다.

> [!TIP]
> Azure Maps 레이어는 기본 지도 계층을 포함 하 여 다른 계층 아래에서 쉽게 렌더링할 수 있습니다. 쉽게 읽을 수 있도록 지도 레이블 아래에 타일 계층을 렌더링 하는 것이 좋습니다. `map.layers.add` 메서드는 아래에 새 계층을 삽입할 계층의 id 인 두 번째 매개 변수를 사용 합니다. 지도 레이블 아래에 타일 계층을 삽입 하려면 다음 코드를 사용할 수 있습니다. `map.layers.add(myTileLayer, "labels");`

```javascript
//Create a tile layer and add it to the map below the label layer.
map.layers.add(new atlas.layer.TileLayer({
    tileUrl: 'https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png',
    opacity: 0.8,
    tileSize: 256
}), 'labels');
```

<center>

![Azure Maps 타일 계층](media/migrate-google-maps-web-app/azure-maps-tile-layer.png)</center>

> [!TIP]
> 타일 요청은 지도의 `transformRequest` 옵션을 사용 하 여 캡처할 수 있습니다. 이렇게 하면 필요한 경우 요청에 헤더를 추가 하거나 수정할 수 있습니다.

**추가 리소스:**

- [타일 계층 추가](map-add-tile-layer.md)
- [타일 계층 클래스](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest)
- [타일 계층 옵션](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.tilelayeroptions?view=azure-iot-typescript-latest)

### <a name="show-traffic"></a>교통량 표시

트래픽 데이터는 Azure와 Google maps 모두에 중첩 될 수 있습니다.

**이전: Google Maps**

Google Maps에서는 트래픽 계층을 사용 하 여 지도에 트래픽 데이터를 겹쳐서 표시할 수 있습니다.

```javascript
var trafficLayer = new google.maps.TrafficLayer();
trafficLayer.setMap(map);
```

<center>

![Google Maps 트래픽](media/migrate-google-maps-web-app/google-maps-traffic.png)</center>

**이후: Azure Maps**

Azure Maps는 트래픽 표시를 위한 여러 가지 옵션을 제공 합니다. 여행 여행 및 사고와 같은 트래픽 인시던트를 지도에 아이콘으로 표시할 수 있습니다. 트래픽 흐름, 색으로 구분 된도로는 지도에 중첩 될 수 있으며, 일반적인 예상 지연 시간 또는 절대 지연 시간을 기준으로 게시 된 속도 제한을 기준으로 색을 변경할 수 있습니다. Azure Maps의 인시던트 데이터는 1 분 마다 업데이트 되 고 데이터 흐름은 2 분 마다 업데이트 됩니다.

```javascript
map.setTraffic({
    incidents: true,
    flow: 'relative'
});
```

<center>

트래픽 Azure Maps ![](media/migrate-google-maps-web-app/azure-maps-traffic.png)</center>

Azure Maps에서 트래픽 아이콘 중 하나를 클릭 하면 팝업에 추가 정보가 표시 됩니다.

<center>

트래픽 인시던트를 Azure Maps ![](media/migrate-google-maps-web-app/azure-maps-traffic-incident.png)</center>

**추가 리소스:**

- [맵에 트래픽 표시](map-show-traffic.md)
- [트래픽 오버레이 옵션](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Traffic%20Overlay%20Options)

### <a name="add-a-ground-overlay"></a>그라운드 오버레이 추가

Azure와 Google maps는 모두 지도에서 오버레이 된 georeferenced 이미지를 지원 하므로 지도를 이동 하 고 확대/축소 하는 동안 이동 하 고 크기를 조정할 수 있습니다. Google Maps에서는이를 Azure Maps 하는 반면에는 이미지 계층 이라고 합니다. 이러한 기능은 바닥 계획을 구축 하는 데 유용 하며, 이전 지도 또는 드 론의 이미지를 오버레이 합니다.

**이전: Google Maps**

Google Maps에서 그라운드 오버레이를 만들 때 오버레이 할 이미지의 URL과 지도에서 이미지를 바인딩할 경계 상자를 지정 해야 합니다. 이 예제에서는 map에서 1922의 [뉴어크 New Jersey](https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg) 맵 이미지를 오버레이 합니다.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map, historicalOverlay;

        function initMap() {
            map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(40.740, -74.18),
                zoom: 12
            });

            var imageBounds = {
                north: 40.773941,
                south: 40.712216,
                east: -74.12544,
                west: -74.22655
            };

            historicalOverlay = new google.maps.GroundOverlay(
                'https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg',
                imageBounds);
            historicalOverlay.setMap(map);
        }
    </script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]" async defer></script>
</head>
<body>
    <div id="myMap" style="position:relative;width:600px;height:400px;"></div>
</body>
</html>
```

브라우저에서이 코드를 실행 하면 다음 이미지와 같은 지도가 표시 됩니다.

<center>

![Google Maps 이미지 오버레이](media/migrate-google-maps-web-app/google-maps-image-overlay.png)</center>

**이후: Azure Maps**

Azure Maps에서 `atlas.layer.ImageLayer` 클래스를 사용 하 여 georeferenced 이미지를 중첩 시킬 수 있습니다. 이 클래스에는 이미지에 대 한 URL과 이미지의 네 모퉁이에 대 한 좌표 집합이 필요 합니다. 동일한 도메인에 이미지를 호스팅하거나 CORs를 사용 하도록 설정 해야 합니다.

> [!TIP]
> 이미지의 각 모퉁이의 좌표가 아닌 북부, 남부, 동부, 서 부 및 회전 정보만 있는 경우 정적 [`atlas.layer.ImageLayer.getCoordinatesFromEdges`](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest#getcoordinatesfromedges-number--number--number--number--number-) 메서드를 사용할 수 있습니다.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map;

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                center: [-74.18, 40.740],
                zoom: 12,

                //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create an image layer and add it to the map.
                map.layers.add(new atlas.layer.ImageLayer({
                    url: 'newark_nj_1922.jpg',
                    coordinates: [
                        [-74.22655, 40.773941], //Top Left Corner
                        [-74.12544, 40.773941], //Top Right Corner
                        [-74.12544, 40.712216], //Bottom Right Corner
                        [-74.22655, 40.712216]  //Bottom Left Corner
                    ]
                }));
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

이미지 오버레이를 Azure Maps ![](media/migrate-google-maps-web-app/azure-maps-image-overlay.png)</center>

**추가 리소스:**

- [이미지 오버레이](map-add-image-layer.md)
- [이미지 계층 클래스](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest)

## <a name="additional-code-samples"></a>추가 코드 샘플

Google Maps 마이그레이션과 관련 된 몇 가지 추가 코드 샘플은 다음과 같습니다.

- [그리기 도구](map-add-drawing-toolbar.md)
- [지도를 두 손가락으로 이동 하도록 제한](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Limit%20Map%20to%20Two%20Finger%20Panning)
- [스크롤 휠 확대/축소 제한](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Limit%20Scroll%20Wheel%20Zoom)
- [전체 화면 컨트롤 만들기](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Create%20a%20Fullscreen%20Control)

**서비스:**

- [Azure Maps services 모듈 사용](how-to-use-services-module.md)
- [관심 지점 검색](map-search-location.md)
- [좌표에서 정보 가져오기 (역방향 geocode)](map-get-information-from-coordinate.md)
- [A에서 B로의 방향 표시](map-route.md)
- [JQuery UI를 사용 하 여 Autosuggest 검색](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Search%20Autosuggest%20and%20JQuery%20UI)

## <a name="google-maps-v3-to-azure-maps-web-sdk-class-mapping"></a>Google Maps V3 Azure Maps 웹 SDK 클래스 매핑

다음 부록에서는 Google Maps v 3에서 가장 일반적으로 사용 되는 클래스와 해당 Azure Maps 웹 SDK에 대 한 상호 참조 매핑을 제공 합니다.

### <a name="core-classes"></a>핵심 클래스

| Google 지도   | Azure Maps  |
|---------------|-------------|
| `google.maps.Map` | [아틀라스. 매핑할](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)  |
| `google.maps.InfoWindow` | [아틀라스. 팝업](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)  |
| `google.maps.InfoWindowOptions` | [아틀라스. PopupOptions](https://docs.microsoft.com/) |
| `google.maps.LatLng`  | [atlas. 위치](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.position?view=azure-iot-typescript-latest)  |
| `google.maps.LatLngBounds` | [BoundingBox](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.boundingbox?view=azure-iot-typescript-latest) |
| `google.maps.MapOptions`  | [아틀라스. CameraOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.cameraoptions?view=azure-iot-typescript-latest)<br/>[아틀라스. CameraBoundsOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.cameraboundsoptions?view=azure-iot-typescript-latest)<br/>[아틀라스. ServiceOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.serviceoptions?view=azure-iot-typescript-latest)<br/>[아틀라스. StyleOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.styleoptions?view=azure-iot-typescript-latest)<br/>[아틀라스. UserInteractionOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.userinteractionoptions?view=azure-iot-typescript-latest) |
| `google.maps.Point`  | [아틀라스. 픽셀](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.pixel?view=azure-iot-typescript-latest)   |

## <a name="overlay-classes"></a>오버레이 클래스

| Google 지도  | Azure Maps  |
|--------------|-------------|
| `google.maps.Marker` | [아틀라스. HtmlMarker](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)<br/>[atlas. Point](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest)  |
| `google.maps.MarkerOptions`  | [아틀라스. HtmlMarkerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkeroptions?view=azure-iot-typescript-latest)<br/>[아틀라스 계층.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest)<br/>[아틀라스. SymbolLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest)<br/>[아틀라스. IconOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions?view=azure-iot-typescript-latest)<br/>[아틀라스. TextOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions?view=azure-iot-typescript-latest)<br/>[atlas. BubbleLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest)<br/>[아틀라스. BubbleLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.bubblelayeroptions?view=azure-iot-typescript-latest) |
| `google.maps.Polygon`  | [atlas. 데이터 다각형](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest)               |
| `google.maps.PolygonOptions` |[atlas. PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest)<br/> [아틀라스. PolygonLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest)<br/> [atlas. LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest)<br/> [아틀라스. LineLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)|
| `google.maps.Polyline` | [LineString](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.linestring?view=azure-iot-typescript-latest)         |
| `google.maps.PolylineOptions` | [atlas. LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-maps-typescript-latest)<br/>[아틀라스. LineLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-maps-typescript-latest) |
| `google.maps.Circle`  | [지도에 원 추가](map-add-shape.md#add-a-circle-to-the-map) 를 참조 하세요.                                     |
| `google.maps.ImageMapType`  | [아틀라스. TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest)         |
| `google.maps.ImageMapTypeOptions` | [아틀라스. TileLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.tilelayeroptions?view=azure-iot-typescript-latest) |
| `google.maps.GroundOverlay`  | [atlas. ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest)<br/>[아틀라스. ImageLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.imagelayeroptions?view=azure-iot-typescript-latest) |

## <a name="service-classes"></a>서비스 클래스

Azure Maps 웹 SDK에는 개별적으로 로드할 수 있는 [서비스 모듈이](how-to-use-services-module.md) 포함 되어 있습니다. 이 모듈은 웹 API를 사용 하 여 Azure Maps REST 서비스를 래핑하고 JavaScript, TypeScript 및 node.js 응용 프로그램에서 사용할 수 있습니다.

| Google 지도 | Azure Maps  |
|-------------|-------------|
| `google.maps.Geocoder` | [atlas. SearchUrl](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchurl?view=azure-iot-typescript-latest)  |
| `google.maps.GeocoderRequest`  | [아틀라스. SearchAddressOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchaddressoptions?view=azure-iot-typescript-latest)<br/>[아틀라스. SearchAddressRevrseOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchaddressreverseoptions?view=azure-iot-typescript-latest)<br/>[아틀라스. SearchAddressReverseCrossStreetOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchaddressreversecrossstreetoptions?view=azure-iot-typescript-latest)<br/>[아틀라스. SearchAddressStructuredOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchaddressstructuredoptions?view=azure-iot-typescript-latest)<br/>[아틀라스. SearchAlongRouteOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchalongrouteoptions?view=azure-iot-typescript-latest)<br/>[아틀라스. SearchFuzzyOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchfuzzyoptions?view=azure-iot-typescript-latest)<br/>[아틀라스. SearchInsideGeometryOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchinsidegeometryoptions?view=azure-iot-typescript-latest)<br/>[아틀라스. SearchNearbyOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchnearbyoptions?view=azure-iot-typescript-latest)<br/>[아틀라스. SearchPOIOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchpoioptions?view=azure-iot-typescript-latest)<br/>[아틀라스. SearchPOICategoryOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchpoicategoryoptions?view=azure-iot-typescript-latest) |
| `google.maps.DirectionsService`  | [RouteUrl](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.routeurl?view=azure-iot-typescript-latest)  |
| `google.maps.DirectionsRequest`  | [아틀라스. CalculateRouteDirectionsOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.calculateroutedirectionsoptions?view=azure-iot-typescript-latest) |
| `google.maps.places.PlacesService` | [atlas. SearchUrl](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchurl?view=azure-iot-typescript-latest)  |

## <a name="libraries"></a>라이브러리

라이브러리는 맵에 추가 기능을 추가 합니다. 이러한 중 상당수는 Azure Maps의 핵심 SDK에 있습니다. 다음은 이러한 Google Maps 라이브러리 대신 사용할 수 있는 몇 가지 동일한 클래스입니다.

| Google 지도           | Azure Maps   |
|-----------------------|--------------|
| 라이브러리 그리기       | [그리기 도구 모듈](set-drawing-options.md) |
| 기 하 도형 라이브러리      | [아틀라스. 수학](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.math?view=azure-iot-typescript-latest)   |
| 시각화 라이브러리 | [열 지도 계층](map-add-heat-map-layer.md) |

## <a name="next-steps"></a>다음 단계

Azure Maps 웹 SDK에 대해 자세히 알아보세요.

> [!div class="nextstepaction"]
> [맵 컨트롤을 사용하는 방법](how-to-use-map-control.md)

> [!div class="nextstepaction"]
> [서비스 모듈을 사용 하는 방법](how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [그리기 도구 모듈을 사용 하는 방법](set-drawing-options.md)

> [!div class="nextstepaction"]
> [코드 샘플](https://docs.microsoft.com/samples/browse/?products=azure-maps)

