---
title: Azure Maps 권한 상승 서비스를 사용 하 여 데이터 권한 상승 요청 (미리 보기)
description: Azure Maps 권한 상승 서비스 (미리 보기)를 사용 하 여 권한 상승 데이터를 요청 하는 방법을 알아봅니다.
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: a303f5e6177d0dc4205eaec8c3b1911e8e004fe3
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/20/2021
ms.locfileid: "98602427"
---
# <a name="request-elevation-data-using-the-azure-maps-elevation-service-preview"></a>Azure Maps 권한 상승 서비스를 사용 하 여 데이터 권한 상승 요청 (미리 보기)

> [!IMPORTANT]
> Azure Maps 권한 상승 서비스는 현재 공개 미리 보기로 제공 됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

Azure Maps [권한 상승 서비스](https://docs.microsoft.com/rest/api/maps/elevation) 는 지구 표면의 모든 위치에서 권한 상승 데이터를 쿼리 하는 api를 제공 합니다. 정의 된 경계 상자 내에서 또는 특정 좌표에서 경로를 따라 샘플링 된 권한 상승 데이터를 요청할 수 있습니다. 또한 [Render V2-맵 가져오기 타일 API](https://docs.microsoft.com/rest/api/maps/renderv2) 를 사용 하 여 타일 형식의 권한 상승을 검색할 수 있습니다. 타일은 GeoTIFF 래스터 형식으로 전달 됩니다. 이 문서에서는 Azure Maps 권한 상승 서비스 및 맵 가져오기 타일 API를 사용 하 여 권한 상승 데이터를 요청 하는 방법을 보여 줍니다. GeoJSON 및 GeoTiff 형식 모두에서 권한 상승 데이터를 요청할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

1. [S1 가격 책정 계층에서 Azure Maps 계정 만들기](quick-demo-map-app.md#create-an-azure-maps-account)
2. 기본 키 또는 구독 키라고도 하는 [기본 구독 키를 가져옵니다](quick-demo-map-app.md#get-the-primary-key-for-your-account).

Azure Maps의 인증에 대 한 자세한 내용은 [Azure Maps 인증을 관리](how-to-manage-authentication.md)하십시오.

이 아티클에서는 [Postman](https://www.postman.com/) 응용 프로그램을 사용 하지만 다른 API 개발 환경을 선택할 수도 있습니다.

## <a name="request-elevation-data-in-raster-tiled-format"></a>래스터 타일 형식의 권한 상승 데이터 요청

래스터 타일 형식의 권한 상승 데이터를 요청 하려면 [Render V2-맵 가져오기 타일 API](https://docs.microsoft.com/rest/api/maps/renderv2)를 사용 합니다. 타일을 찾을 수 있는 경우 API는 타일을 GeoTIFF로 반환 합니다. 그렇지 않으면 API는 0을 반환 합니다. 모든 래스터 DEM 타일은 geoid (바다 수준) 지구 모드를 사용 합니다. 이 예제에서는 Mt의 상승 데이터를 요청 합니다. Everest.

>[!TIP]
>세계 지도의 특정 영역에서 타일을 검색 하려면 적절 한 확대/축소 수준에서 올바른 타일을 찾아야 합니다. 또한 WorldDEM는 전체 전역 landmass를 다루지만 대양를 다루지 않습니다.  자세한 내용은 [확대/축소 수준 및 타일 그리드](zoom-levels-and-tile-grid.md)를 참조하세요.

1. Postman 앱을 엽니다. Postman 앱의 위쪽 근처에서 **새로 만들기** 를 선택합니다. **새로 만들기** 창에서 **컬렉션** 을 선택합니다.  컬렉션 이름을 지정하고, **만들기** 단추를 선택합니다.

2. 요청을 만들려면 **새로 만들기** 를 다시 선택합니다. **새로 만들기** 창에서 **요청** 을 선택합니다. 요청에 대한 **요청 이름** 을 입력합니다. 이전 단계에서 만든 컬렉션을 선택한 다음, **저장** 을 선택합니다.

3. 작성기 탭에서 **GET** HTTP 메서드를 선택 하 고 다음 URL을 입력 하 여 래스터 타일을 요청 합니다. 이 요청 및 이 문서에 언급된 기타 요청에 대한 `{Azure-Maps-Primary-Subscription-key}`를 기본 구독 키로 바꿉니다.

    ```http
    https://atlas.microsoft.com/map/tile?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=2.0&tilesetId=microsoft.dem&zoom=13&x=6074&y=3432
    ```

4. **보내기** 단추를 클릭합니다. GeoTIFF 형식의 권한 상승 데이터를 포함 하는 래스터 타일을 수신 해야 합니다. 래스터 타일 원시 데이터 내의 각 픽셀은 유형입니다 `float` . 각 픽셀의 값은 상승 높이를 미터 단위로 나타냅니다.

## <a name="request-elevation-data-in-geojson-format"></a>GeoJSON 형식의 권한 상승 데이터 요청

권한 상승 서비스 (미리 보기) Api를 사용 하 여 GeoJSON 형식의 권한 상승 데이터를 요청 합니다. 이 섹션에서는 다음과 같은 세 가지 Api를 각각 보여 줍니다.

* [요소에 대 한 데이터 가져오기](/rest/api/maps/elevation/getdataforpoints)
* [점에 대 한 게시 데이터](/rest/api/maps/elevation/postdataforpoints)
* [다중선 데이터 가져오기](https://docs.microsoft.com/rest/api/maps/elevation/getdataforpolyline)
* [다중선의 게시 데이터](https://docs.microsoft.com/rest/api/maps/elevation/postdataforpolyline)
* [경계 상자에 대 한 데이터 가져오기](https://docs.microsoft.com/rest/api/maps/elevation/getdataforboundingbox)

>[!IMPORTANT]
> 데이터를 반환할 수 없는 경우 모든 Api는를 반환 `0` 합니다.

### <a name="request-elevation-data-for-points"></a>요소에 대 한 권한 상승 데이터 요청

이 예제에서는 [요소에 대 한 데이터 가져오기 API](/rest/api/maps/elevation/getdataforpoints) 를 사용 하 여 Mt에서 데이터 상승 데이터를 요청 합니다. Everest 및 Chamlang 산. 그런 다음, [요소에 대 한 게시 데이터 API](/rest/api/maps/elevation/postdataforpoints) 를 사용 하 여 동일한 두 개의 요소를 사용 하 여 권한 상승 데이터를 요청 합니다. URL의 위도 및 경도는 WGS84 (세계 측 지 System)의 10 진수 수준에 있어야 합니다.

 >[!IMPORTANT]
 >URL 문자 길이 제한이 2048 이므로 URL GET 요청에서 100 이상의 좌표를 파이프라인으로 구분 된 문자열로 전달할 수 없습니다. 100 개 이상의 좌표를 파이프라인으로 구분 된 문자열로 전달 하려는 경우에는 점에 대 한 게시 데이터를 사용 합니다.

1. 요청을 만들려면 **새로 만들기** 를 다시 선택합니다. **새로 만들기** 창에서 **요청** 을 선택합니다. 요청에 대한 **요청 이름** 을 입력합니다. 이전 단계에서 만든 컬렉션을 선택한 다음, **저장** 을 선택합니다.

2. 작성기 탭에서 **GET** HTTP 메서드를 선택 하 고 다음 URL을 입력 합니다. 이 요청 및 이 문서에 언급된 기타 요청에 대한 `{Azure-Maps-Primary-Subscription-key}`를 기본 구독 키로 바꿉니다.

    ```http
    https://atlas.microsoft.com/elevation/point/json?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0&points=-73.998672,40.714728|150.644,-34.397
    ```

3. **보내기** 단추를 클릭합니다.  다음 JSON 응답을 받게 됩니다.

    ```json
    {
    "data": [
        {
            "coordinate": {
                "latitude": 40.714728,
                "longitude": -73.998672
            },
            "elevationInMeter": 12.142355447638208
        },
        {
            "coordinate": {
                "latitude": -34.397,
                "longitude": 150.644
            },
            "elevationInMeter": 384.47041445517846
        }
    ]
    }
    ```

4. 이제는 요소에 대 한 [게시 데이터 API](/rest/api/maps/elevation/postdataforpoints) 를 호출 하 여 동일한 두 점에 대 한 권한 상승 데이터를 가져옵니다. 작성기 탭에서 **POST** HTTP 메서드를 선택 하 고 다음 URL을 입력 합니다. 이 요청 및 이 문서에 언급된 기타 요청에 대한 `{Azure-Maps-Primary-Subscription-key}`를 기본 구독 키로 바꿉니다.

    ```http
    https://atlas.microsoft.com/elevation/point/json?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0
    ```

5. **POST** 요청의 **헤더** 에서 `Content-Type`을 `application/json`으로 설정합니다. **본문** 에서 아래 좌표 지점 정보를 제공 합니다. 완료되면 **보내기** 를 클릭합니다.

     ```json
    [
        {
            "lon": -73.998672,
            "lat": 40.714728
        },
        {
            "lon": 150.644,
            "lat": -34.397
        }
    ]
    ```

### <a name="request-elevation-data-samples-along-a-polyline"></a>다중선을 따라 권한 상승 데이터 샘플 요청

이 예제에서는 [다중선의 데이터 가져오기](https://docs.microsoft.com/rest/api/maps/elevation/getdataforpolyline) 를 사용 하 여 Mt의 좌표 사이에 직선을 따라 데이터 상승에 대 한 5 개의 동일한 간격의 샘플을 요청 합니다. Everest 및 Chamlang 산. 두 좌표는 모두 Long/Lat 형식으로 정의 되어야 합니다. 매개 변수의 값을 지정 하지 않으면 `samples` 샘플 수는 기본적으로 10이 됩니다. 최대 샘플 수는 2000입니다.

그런 다음, 폴리라인 데이터 가져오기를 사용 하 여 경로를 따라 데이터 상승에 대 한 세 개의 동일한 간격으로 샘플을 요청 합니다. 3 개의 긴/Lat 좌표 쌍을 전달 하 여 샘플에 대 한 정확한 위치를 정의 합니다.

마지막으로, [폴리라인 API에 대 한 Post 데이터](https://docs.microsoft.com/rest/api/maps/elevation/postdataforpolyline) 를 사용 하 여 동일한 간격으로 동일한 3 개의 샘플에서 권한 상승 데이터를 요청 합니다.

URL의 위도 및 경도는 WGS84 (세계 측 지 System)의 10 진수 수준에 있어야 합니다.

 >[!IMPORTANT]
 >URL 문자 길이 제한이 2048 이므로 URL GET 요청에서 100 이상의 좌표를 파이프라인으로 구분 된 문자열로 전달할 수 없습니다. 100 개 이상의 좌표를 파이프라인으로 구분 된 문자열로 전달 하려는 경우에는 점에 대 한 게시 데이터를 사용 합니다.

1. **새로 만들기** 를 선택합니다. **새로 만들기** 창에서 **요청** 을 선택합니다. **요청 이름** 입력하고 컬렉션을 선택합니다. **저장** 을 클릭합니다.

2. 작성기 탭에서 **GET** HTTP 메서드를 선택 하 고 다음 URL을 입력 합니다. 이 요청 및 이 문서에 언급된 기타 요청에 대한 `{Azure-Maps-Primary-Subscription-key}`를 기본 구독 키로 바꿉니다.

   ```http
    https://atlas.microsoft.com/elevation/line/json?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}&lines=-73.998672,40.714728|150.644,-34.397&samples=5
    ```

3. **보내기** 단추를 클릭합니다.  다음 JSON 응답을 받게 됩니다.

    ```JSON
    {
        "data": [
            {
                "coordinate": {
                    "latitude": 40.714728,
                    "longitude": -73.998672
                },
                "elevationInMeter": 12.14236
            },
            {
                "coordinate": {
                    "latitude": 21.936796000000001,
                    "longitude": -17.838003999999998
                },
                "elevationInMeter": 0.0
            },
            {
                "coordinate": {
                    "latitude": 3.1588640000000012,
                    "longitude": 38.322664000000003
                },
                "elevationInMeter": 598.66943
            },
            {
                "coordinate": {
                    "latitude": -15.619067999999999,
                    "longitude": 94.483332000000019
                },
                "elevationInMeter": 0.0
            },
            {
                "coordinate": {
                    "latitude": -34.397,
                    "longitude": 150.644
                },
                "elevationInMeter": 384.47041
            }
        ]
    }
    ```

4. 이제 Mount Everest, Chamlang 및 Jannu 산의 좌표 간 경로를 따라 상승 하는 세 가지 샘플 데이터를 요청 합니다. **Params** 섹션에서 쿼리 키 값에 대 한 다음 좌표 배열을 복사 `lines` 합니다.

    ```html
        86.9797222, 27.775|86.9252778, 27.9880556 | 88.0444444, 27.6822222
    ```

5. `samples`쿼리 키 값을로 변경 `3` 합니다.  아래 이미지는 새 값을 보여 줍니다.

     :::image type="content" source="./media/how-to-request-elevation-data/get-elevation-samples.png" alt-text="3 가지 권한 상승 데이터 샘플을 검색 합니다.":::

6. 파란색 **보내기** 단추를 클릭 합니다. 다음 JSON 응답을 받게 됩니다.

    ```json
    {
        "data": [
            {
                "coordinate": {
                    "latitude": 27.775,
                    "longitude": 86.9797222
                },
                "elevationInMeter": 7116.0348851572589
            },
            {
                "coordinate": {
                    "latitude": 27.737403546316028,
                    "longitude": 87.411180791156454
                },
                "elevationInMeter": 1798.6945512521534
            },
            {
                "coordinate": {
                    "latitude": 27.682222199999998,
                    "longitude": 88.0444444
                },
                "elevationInMeter": 7016.9372013588072
            }
        ]
    }
    ```

7. 이제 [폴리라인 API에 대 한 Post 데이터](https://docs.microsoft.com/rest/api/maps/elevation/postdataforpolyline) 를 호출 하 여 동일한 3 개 점에 대 한 권한 상승 데이터를 가져옵니다. 작성기 탭에서 **POST** HTTP 메서드를 선택 하 고 다음 URL을 입력 합니다. 이 요청 및 이 문서에 언급된 기타 요청에 대한 `{Azure-Maps-Primary-Subscription-key}`를 기본 구독 키로 바꿉니다.

    ```http
    https://atlas.microsoft.com/elevation/line/json?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}&samples=5
    ```

8. **POST** 요청의 **헤더** 에서 `Content-Type`을 `application/json`으로 설정합니다. **본문** 에서 아래 좌표 지점 정보를 제공 합니다. 완료되면 **보내기** 를 클릭합니다.

     ```json
    [
        {
            "lon": 86.9797222,
            "lat": 27.775
        },
        {
            "lon": 86.9252778,
            "lat": 27.9880556
        },
        {
            "lon": 88.0444444,
            "lat": 27.6822222
        }
    ]
    ```

### <a name="request-elevation-data-by-bounding-box"></a>경계 상자를 기준으로 데이터 권한 상승 요청

이제 [경계에 대 한 데이터 가져오기 상자](https://docs.microsoft.com/rest/api/maps/elevation/getdataforboundingbox) 를 사용 하 여 Mt 근처의 상승 데이터를 요청 합니다. Rainier, WA. 권한 상승 데이터는 경계 상자 내에서 간격이 동일한 위치에 반환 됩니다. (2) lat/긴 좌표 집합으로 정의 된 경계 영역 (남부 위도, 서 부 경도 | 북부 위도, 동쪽 경도)은 행과 열로 구분 됩니다. 두 개의 행과 두 개의 열 (2)에 대 한 경계 상자 계정의 가장자리입니다. 행 및 열 교차로 만든 그리드 꼭 짓 점에 대 한 상승이 반환 됩니다. 최대 2000 상승은 단일 요청으로 반환 될 수 있습니다.

이 예에서는 rows = 3 및 columns = 6을 지정 합니다. 18 개의 승격 값이 응답에 반환 됩니다. 다음 다이어그램에서 상승 값은 남서쪽 모퉁이부터 순서 대로 정렬 된 다음 서쪽에서 북부 및 남부에서 북쪽으로 진행 됩니다.  상승 지점은 반환 된 순서 대로 번호가 매겨집니다.

:::image type="content" source="./media/how-to-request-elevation-data/bounding-box.png" border="false" alt-text="경계 상자는 NE 및 SE 모퉁이를 조정 합니다.":::

1. **새로 만들기** 를 선택합니다. **새로 만들기** 창에서 **요청** 을 선택합니다. **요청 이름** 입력하고 컬렉션을 선택합니다. **저장** 을 클릭합니다.

2. 작성기 탭에서 **GET** HTTP 메서드를 선택 하 고 다음 URL을 입력 합니다. 이 요청 및 이 문서에 언급된 기타 요청에 대한 `{Azure-Maps-Primary-Subscription-key}`를 기본 구독 키로 바꿉니다.

    ```http
    https://atlas.microsoft.com/elevation/lattice/json?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0&bounds=-121.66853362143818, 46.84646479863713,-121.65853362143818, 46.85646479863713&rows=2&columns=3
    ```

3. 파란색 **보내기** 단추를 클릭 합니다. 표의 각 꼭 짓 점 마다 하나씩 18 개의 승격 데이터 샘플이 응답으로 반환 됩니다.

    ```json
    {
    "data": [
        {
            "coordinate": {
                "latitude": 46.846464798637129,
                "longitude": -121.66853362143819
            },
            "elevationInMeter": 2298.6581875651746
        },
        {
            "coordinate": {
                "latitude": 46.846464798637129,
                "longitude": -121.66653362143819
            },
            "elevationInMeter": 2306.3980756609963
        },
        {
            "coordinate": {
                "latitude": 46.846464798637129,
                "longitude": -121.66453362143818
            },
            "elevationInMeter": 2279.3385479564113
        },
        {
            "coordinate": {
                "latitude": 46.846464798637129,
                "longitude": -121.66253362143819
            },
            "elevationInMeter": 2233.1549264690366
        },
        {
            "coordinate": {
                "latitude": 46.846464798637129,
                "longitude": -121.66053362143818
            },
            "elevationInMeter": 2196.4485923541492
        },
        {
            "coordinate": {
                "latitude": 46.846464798637129,
                "longitude": -121.65853362143818
            },
            "elevationInMeter": 2133.1756767157253
        },
        {
            "coordinate": {
                "latitude": 46.849798131970459,
                "longitude": -121.66853362143819
            },
            "elevationInMeter": 2345.3227848228803
        },
        {
            "coordinate": {
                "latitude": 46.849798131970459,
                "longitude": -121.66653362143819
            },
            "elevationInMeter": 2292.2449195443587
        },
        {
            "coordinate": {
                "latitude": 46.849798131970459,
                "longitude": -121.66453362143818
            },
            "elevationInMeter": 2270.5867788258074
        },
        {
            "coordinate": {
                "latitude": 46.849798131970459,
                "longitude": -121.66253362143819
            },
            "elevationInMeter": 2296.8311427390604
        },
        {
            "coordinate": {
                "latitude": 46.849798131970459,
                "longitude": -121.66053362143818
            },
            "elevationInMeter": 2266.0729430891065
        },
        {
            "coordinate": {
                "latitude": 46.849798131970459,
                "longitude": -121.65853362143818
            },
            "elevationInMeter": 2242.216346631234
        },
        {
            "coordinate": {
                "latitude": 46.8531314653038,
                "longitude": -121.66853362143819
            },
            "elevationInMeter": 2378.460838833359
        },
        {
            "coordinate": {
                "latitude": 46.8531314653038,
                "longitude": -121.66653362143819
            },
            "elevationInMeter": 2327.6761137260387
        },
        {
            "coordinate": {
                "latitude": 46.8531314653038,
                "longitude": -121.66453362143818
            },
            "elevationInMeter": 2208.3782743402949
        },
        {
            "coordinate": {
                "latitude": 46.8531314653038,
                "longitude": -121.66253362143819
            },
            "elevationInMeter": 2106.9526472760981
        },
        {
            "coordinate": {
                "latitude": 46.8531314653038,
                "longitude": -121.66053362143818
            },
            "elevationInMeter": 2054.3270174034078
        },
        {
            "coordinate": {
                "latitude": 46.8531314653038,
                "longitude": -121.65853362143818
            },
            "elevationInMeter": 2030.6438331110671
        },
        {
            "coordinate": {
                "latitude": 46.856464798637127,
                "longitude": -121.66853362143819
            },
            "elevationInMeter": 2318.753153399402
        },
        {
            "coordinate": {
                "latitude": 46.856464798637127,
                "longitude": -121.66653362143819
            },
            "elevationInMeter": 2253.88875188271
        },
        {
            "coordinate": {
                "latitude": 46.856464798637127,
                "longitude": -121.66453362143818
            },
            "elevationInMeter": 2136.6145845357587
        },
        {
            "coordinate": {
                "latitude": 46.856464798637127,
                "longitude": -121.66253362143819
            },
            "elevationInMeter": 2073.6734467948486
        },
        {
            "coordinate": {
                "latitude": 46.856464798637127,
                "longitude": -121.66053362143818
            },
            "elevationInMeter": 2042.994055784251
        },
        {
            "coordinate": {
                "latitude": 46.856464798637127,
                "longitude": -121.65853362143818
            },
            "elevationInMeter": 1988.3631481900356
        }
    ]
    }
    ```

## <a name="samples-use-elevation-service-preview-apis-in-azure-maps-control"></a>샘플: Azure Maps 컨트롤에서 권한 상승 서비스 (미리 보기) Api 사용

### <a name="get-elevation-data-by-coordinate-position"></a>좌표 위치로 상승 데이터 가져오기

다음 샘플 웹 페이지에서는 지도 컨트롤을 사용 하 여 좌표 점에서 상승 데이터를 표시 하는 방법을 보여 줍니다. 사용자가 표식을 끌면 지도에 팝업의 상승 데이터가 표시 됩니다.

<br/>

<iframe height="500" style="width:100%;" scrolling="no" title="위치에서 권한 상승 가져오기" src="https://codepen.io/azuremaps/embed/c840b510e113ba7cb32809591d5f96a2?height=500&theme-id=default&default-tab=js,result&editable=true" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
CodePen의 Azure Maps ()로 펜 <a href='https://codepen.io/azuremaps/pen/c840b510e113ba7cb32809591d5f96a2'>가져오기 권한 상승</a> 을 참조 하세요 <a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io'></a>.
</iframe>

### <a name="get-elevation-data-by-bounding-box"></a>경계 상자를 기준으로 데이터 상승 가져오기

다음 샘플 웹 페이지에서는 지도 컨트롤을 사용 하 여 경계 상자 내에 포함 된 권한 상승 데이터를 표시 하는 방법을 보여 줍니다. 사용자는 왼쪽 위 모퉁이에 있는 아이콘을 클릭 하 여 경계 상자를 정의 `square` 하 고 지도의 아무 곳에 나 사각형을 그립니다. 그러면 지도 컨트롤은 오른쪽 위 모퉁이에 있는 키에 지정 된 색에 따라 권한 상승 데이터를 렌더링 합니다.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="경계 상자 별 권한 상승" src="https://codepen.io/azuremaps/embed/619c888c70089c3350a3e95d499f3e48?height=500&theme-id=default&default-tab=js,result" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
CodePen에서 ()를 Azure Maps 하 여 <a href='https://codepen.io/azuremaps/pen/619c888c70089c3350a3e95d499f3e48'>경계 상자</a> 에의 한 펜 권한 상승을 참조 하세요 <a href='https://codepen.io/azuremaps'>@azuremaps</a> . <a href='https://codepen.io'></a>
</iframe>

### <a name="get-elevation-data-by-polyline-path"></a>다중선 경로를 기준으로 데이터 상승 가져오기

다음 샘플 웹 페이지에서는 지도 컨트롤을 사용 하 여 경로를 따라 상승 데이터를 표시 하는 방법을 보여 줍니다. `Polyline`왼쪽 위 모퉁이에 있는 아이콘을 클릭 하 고 지도에 다중선을 그리면 사용자가 경로를 정의 합니다. 그러면 지도 컨트롤은 오른쪽 위 모퉁이에 있는 키에 지정 된 색으로 상승 데이터를 렌더링 합니다.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="상승 경로 그라데이션" src="https://codepen.io/azuremaps/embed/7bee08e5cb13d05cb0a11636b60f14ca?height=500&theme-id=default&default-tab=js,result&editable=true" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
CodePen의 Azure Maps ()로 펜 <a href='https://codepen.io/azuremaps/pen/7bee08e5cb13d05cb0a11636b60f14ca'>상승 경로 그라데이션을</a> 확인 하세요 <a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io'></a>.
</iframe>


## <a name="next-steps"></a>다음 단계

Azure Maps 권한 상승 (미리 보기) Api를 추가로 탐색 하려면 다음을 참조 하세요.

> [!div class="nextstepaction"]
> [권한 상승 (미리 보기)-Lat 긴 좌표에 대 한 데이터 가져오기](/rest/api/maps/elevation/getdataforpoints)

> [!div class="nextstepaction"]
> [권한 상승 (미리 보기)-경계 상자에 대 한 데이터 가져오기](https://docs.microsoft.com/rest/api/maps/elevation/getdataforboundingbox)

> [!div class="nextstepaction"]
> [권한 상승 (미리 보기)-폴리라인 데이터 가져오기](https://docs.microsoft.com/rest/api/maps/elevation/getdataforpolyline)

> [!div class="nextstepaction"]
> [렌더링 V2 – 맵 타일 가져오기](https://docs.microsoft.com/rest/api/maps/renderv2)

Azure Maps REST API의 전체 목록은 다음을 참조하세요.

> [!div class="nextstepaction"]
> [Azure Maps REST API](https://docs.microsoft.com/rest/api/maps/)
