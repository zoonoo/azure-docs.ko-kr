---
title: Azure Maps에서 래스터 지도에 사용자 지정 데이터를 렌더링하는 방법 | Microsoft Docs
description: Azure Maps에서 래스터 지도에 사용자 지정 데이터를 렌더링합니다.
author: walsehgal
ms.author: v-musehg
ms.date: 02/12/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 9b3c0f7b1ff56cb269f6852be8fd2affeca8b8f1
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2019
ms.locfileid: "56119159"
---
# <a name="render-custom-data-on-raster-map"></a>래스터 지도에 사용자 지정 데이터 렌더링

이 문서에서는 래스터 지도에서 오버레이를 허용하는 이미지 컴퍼지션 기능과 함께 [정적 이미지 서비스](https://docs.microsoft.com/rest/api/maps/render/getmapimage)를 사용하는 방법을 보여 줍니다. 이미지 컴퍼지션에는 사용자 지정 압핀, 레이블, 기하 도형 오버레이 등의 추가 데이터가 포함된 래스터 타일을 가져올 수 있는 기능이 포함됩니다. 사용자 지정 압핀, 레이블 및 기하 도형을 렌더링하려면 postman 애플리케이션을 사용합니다. Postman 앱을 열고 새로 만들기 | 새로 만들기를 클릭합니다. 항목을 저장할 컬렉션 또는 폴더를 선택하고 이름을 지정한 다음, 저장을 클릭합니다.

Azure Maps [데이터 서비스 API](https://docs.microsoft.com/rest/api/maps/data)를 사용하여 오버레이를 저장하고 렌더링합니다. 


## <a name="prerequisites"></a>필수 조건

### <a name="create-an-azure-maps-account"></a>Azure Maps 계정 만들기 

이 가이드의 단계를 수행하려면 먼저 [계정 및 키 관리](how-to-manage-account-keys.md)를 참조하여 S1 가격 책정 계층으로 계정 구독을 만들고 관리해야 합니다.

## <a name="render-pushpins-with-labels-and-custom-image"></a>레이블 및 사용자 지정 이미지를 사용하여 압핀 렌더링

> [!Note]
> 이 예제를 수행하려면 가격 책정 계층 S0 또는 S1의 Azure Maps 계정이 필요합니다. 

Azure Maps 계정 S0 SKU는 사용자가 URL 요청에 지정된 압핀을 최대 5개까지 사용자 지정 이미지로 렌더링할 수 있게 해주는 `pins` 매개 변수의 단일 인스턴스만 지원합니다.

레이블 및 사용자 지정 이미지를 사용하여 압핀을 렌더링하려면 아래 단계를 수행합니다.

1. Postman 앱을 열고 새로 만들기 | 새로 만들기를 클릭한 다음, 요청을 선택합니다. 압핀 렌더링에 대한 요청 이름을 입력하고 요청을 저장할 컬렉션 또는 폴더를 선택한 다음, 저장을 클릭합니다.
    
    ![Postman을 사용하여 지오펜스 업로드](./media/tutorial-geofence/postman-new.png)

2. 작성기 탭에서 GET HTTP 메서드를 선택하고, 다음 URL을 입력하여 GET 요청을 합니다.

    ```HTTP
    https://atlas.microsoft.com/map/static/png?subscription-key={subscription-key}&api-version=1.0&layer=basic&style=main&zoom=12&center=-73.98,%2040.77&pins=custom%7Cla15+50%7Cls12%7Clc003b61%7C%7C%27CentralPark%27-73.9657974+40.781971%7C%7Chttp%3A%2F%2Fazuremapscodesamples.azurewebsites.net%2FCommon%2Fimages%2Fpushpins%2Fylw-pushpin.png
    ```
    반환되는 응답 이미지는 다음과 같습니다.

    ![레이블을 사용하여 사용자 지정 압핀 렌더링](./media/how-to-render-custom-data/render-pins.png)


## <a name="get-data-from-azure-maps-data-storage"></a>Azure Maps 데이터 스토리지에서 데이터 가져오기

> [!Note]
> 이 예제를 수행하려면 가격 책정 계층 S1의 Azure Maps 계정이 필요합니다.

경로 및 핀 위치 정보는 [데이터 업로드 API](https://docs.microsoft.com/rest/api/maps/mapdata/upload)를 통해서도 확인할 수 있습니다. 경로 및 핀 데이터를 업로드하려면 아래 단계를 수행합니다.

1. Postman 앱에서, 위에서 만든 컬렉션에 새 탭을 엽니다. 작성기 탭에서 POST HTTP 메서드를 선택하고, 다음 URL을 입력하여 POST 요청을 합니다.

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```

2. 매개 변수를 클릭하고 POST 요청 URL에 사용할 다음 키/값 쌍을 입력합니다. subscription-key 값을 Azure Maps 구독 키로 바꿉니다.
    
    ![키 값 매개 변수 Postman](./media/how-to-render-custom-data/postman-key-vals.png)

3. **본문**을 클릭하고 원시 입력 형식을 선택한 다음, 드롭다운 목록에서 JSON을 입력 형식으로 선택합니다. 다음 JSON을 업로드할 데이터로 제공합니다.
    
    ```JSON
    {
      "type": "FeatureCollection",
      "features": [
        {
          "type": "Feature",
          "properties": {},
          "geometry": {
            "type": "Polygon",
            "coordinates": [
              [
                [
                  -73.98235,
                  40.76799
                ],
                [
                  -73.95785,
                  40.80044
                ],
                [
                  -73.94928,
                  40.7968
                ],
                [
                  -73.97317,
                  40.76437
                ],
                [
                  -73.98235,
                  40.76799
                ]
              ]
            ]
          }
        },
        {
          "type": "Feature",
          "properties": {},
          "geometry": {
            "type": "LineString",
            "coordinates": [
              [
                -73.97624731063843,
                40.76560773817073
              ],
              [
                -73.97914409637451,
                40.766826609362575
              ],
              [
                -73.98513078689575,
                40.7585866048861
              ]
            ]
          }
        }
      ]
    }
    ```

4. 보내기를 클릭하고 응답 헤더를 검토합니다. 위치 헤더에는 나중에 사용할 수 있도록 데이터를 다운로드하거나 액세스하기 위한 URI가 포함되어 있습니다. 업로드된 데이터에 대한 고유한 `udId`도 포함됩니다.   

  ```HTTP
  https://atlas.microsoft.com/mapData/{udId}/status?api-version=1.0&subscription-key={Subscription-key}
  ```



5. 데이터 업로드 API에서 받은 `udid` 값을 사용하여 지도에 기능을 렌더링합니다. 이렇게 하려면 위에서 만든 컬렉션에 새 탭을 엽니다. 작성기 탭에서 GET HTTP 메서드를 선택하고, 다음 URL을 입력하여 GET 요청을 합니다.

    ```HTTP
    https://atlas.microsoft.com/map/static/png?subscription-key={subscription-key}&api-version=1.0&layer=basic&style=main&zoom=12&center=-73.96682739257812%2C40.78119135317995&pins=default|la-35+50|ls12|lc003C62|co9B2F15||'Times Square'-73.98516297340393 40.758781646381024|'Central Park'-73.96682739257812 40.78119135317995&path=lc0000FF|fc0000FF|lw3|la0.80|fa0.30||udid-{udId}
    ```

6. 응답 이미지는 다음과 같아야 합니다.

    ![업로드된 데이터 렌더링](./media/how-to-render-custom-data/uploaded-path.png)

## <a name="render-polygon-with-color-and-opacity"></a>색과 불투명도를 사용하여 다각형 렌더링

> [!Note]
> 이 예제를 수행하려면 가격 책정 계층 S1의 Azure Maps 계정이 필요합니다.

[경로 매개 변수](https://docs.microsoft.com/rest-staging/api/maps/render/getmapimage#uri-parameters)와 스타일 한정자를 사용하여 다각형의 모양을 수정할 수 있습니다.

1. Postman 앱에서, 위에서 만든 컬렉션에 새 탭을 엽니다. 작성기 탭에서 GET HTTP 메서드를 선택하고, 다음 URL을 입력하여 색과 불투명도로 다각형을 렌더링하도록 GET 요청을 합니다.
    
    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&sku=S1&zoom=14&height=500&Width=500&center=-74.040701, 40.698666&path=lc0000FF|fc0000FF|lw3|la0.80|fa0.50||-74.03995513916016 40.70090237454063|-74.04082417488098 40.70028420372218|-74.04113531112671 40.70049568385827|-74.04298067092896 40.69899904076542|-74.04271245002747 40.69879568992435|-74.04367804527283 40.6980961582905|-74.04364585876465 40.698055487620714|-74.04368877410889 40.698022951066996|-74.04168248176573 40.696444909137|-74.03901100158691 40.69837271818651|-74.03824925422668 40.69837271818651|-74.03809905052185 40.69903971085914|-74.03771281242369 40.699340668780984|-74.03940796852112 40.70058515602143|-74.03948307037354 40.70052821920425|-74.03995513916016 40.70090237454063
    &subscription-key={subscription--key}
    ```

응답 이미지는 다음과 같아야 합니다.

![불투명 다각형 렌더링](./media/how-to-render-custom-data/opaque-polygon.png)


## <a name="render-polygon-with-circle-and-push-pins-with-custom-labels"></a>원을 사용하여 다각형 렌더링 및 사용자 지정 레이블을 사용하여 압핀 렌더링

> [!Note]
> 이 예제를 수행하려면 가격 책정 계층 S1의 Azure Maps 계정이 필요합니다.

‘sc’ 배율 스타일 한정자를 사용하여 압핀과 해당 레이블을 확대하거나 축소할 수 있습니다. 0보다 큰 값입니다. 값 1이 표준 배율입니다. 값이 1보다 크면 핀이 확대되고, 값이 1보다 작으면 축소됩니다. 스타일 한정자에 대한 자세한 내용은 [정적 이미지 서비스 경로 매개 변수](https://docs.microsoft.com/rest/api/maps/render/getmapimage#uri-parameters)를 참조하세요.

원을 사용하여 다각형을 렌더링하고 사용자 지정 레이블을 사용하여 압핀을 렌더링하려면 아래 단계를 수행합니다.

1. Postman 앱에서, 위에서 만든 컬렉션에 새 탭을 엽니다. 작성기 탭에서 GET HTTP 메서드를 선택하고, 다음 URL을 입력하여 GET 요청을 합니다.

    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&zoom=14&height=700&Width=700&center=-122.13230609893799,47.64599069048016&path=lcFF0000|lw2|la0.60|ra1000||-122.13230609893799 47.64599069048016&pins=default|la15+50|al0.66|lc003C62|co002D62||'Microsoft Corporate Headquarters'-122.14131832122801  47.64690503939462|'Microsoft Visitor Center'-122.136828 47.642224|'Microsoft Conference Center'-122.12552547454833 47.642940335653996|'Microsoft The Commons'-122.13687658309935  47.64452336193245&subscription-key={subscription-key}
    ```

응답 이미지는 다음과 같아야 합니다.

![사용자 지정 핀을 사용하여 원 렌더링](./media/how-to-render-custom-data/circle-custom-pins.png)

## <a name="next-steps"></a>다음 단계

* [Azure Maps 지도 이미지 가져오기 API](https://docs.microsoft.com/rest/api/maps/search) 문서를 살펴봅니다.
* Azure Maps 데이터 서비스 기능에 대한 자세한 내용은 [서비스 문서](https://docs.microsoft.com/rest/api/maps/data)를 참조하세요.