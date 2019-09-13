---
title: Azure Maps에서 래스터 맵에 사용자 지정 데이터를 렌더링 하는 방법 | Microsoft Docs
description: Azure Maps에서 래스터 맵에 사용자 지정 데이터를 렌더링 합니다.
author: walsehgal
ms.author: v-musehg
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 6619fd842f225a6d362a4b308dde6e35b43677c9
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/11/2019
ms.locfileid: "70915750"
---
# <a name="render-custom-data-on-a-raster-map"></a>래스터 맵에서 사용자 지정 데이터 렌더링

이 문서에서는 이미지 컴퍼지션 기능과 함께 [정적 이미지 서비스](https://docs.microsoft.com/rest/api/maps/render/getmapimage) 를 사용 하 여 래스터 맵 위에 오버레이를 허용 하는 방법을 설명 합니다. 이미지 컴포지션에는 사용자 지정 압정, 레이블 및 기 하 오버레이와 같은 추가 데이터를 사용 하 여 래스터 타일을 다시 가져오는 기능이 포함 되어 있습니다.

사용자 지정 압정, 레이블 및 기 하 도형 오버레이를 렌더링 하기 위해 Postman 응용 프로그램을 사용할 수 있습니다. Azure Maps [데이터 서비스 api](https://docs.microsoft.com/rest/api/maps/data) 를 사용 하 여 오버레이를 저장 하 고 렌더링할 수 있습니다.


## <a name="prerequisites"></a>전제 조건

### <a name="create-an-azure-maps-account"></a>Azure Maps 계정 만들기

이 문서의 절차를 완료 하려면 먼저 [계정 관리](https://docs.microsoft.com/azure/azure-maps/how-to-manage-account-keys#create-a-new-account) 의 지침에 따라 Azure Maps 계정을 만들고 [기본 키 가져오기](./tutorial-search-location.md#getkey) 의 단계를 수행 하 여 계정에 대 한 기본 구독 키를 검색 해야 합니다.


## <a name="render-pushpins-with-labels-and-a-custom-image"></a>레이블 및 사용자 지정 이미지를 사용 하 여 압정 렌더링

> [!Note]
> 이 섹션의 절차에는 가격 책정 계층 S0 또는 S1의 Azure Maps 계정이 필요 합니다.

Azure Maps 계정 S0 계층은 `pins` 매개 변수의 단일 인스턴스만 지원 합니다. URL 요청에 지정 된 사용자 지정 이미지를 사용 하 여 최대 5 개의 압정을 렌더링할 수 있습니다.

레이블 및 사용자 지정 이미지를 사용 하 여 압정를 렌더링 하려면 다음 단계를 완료 합니다.

1. 요청을 저장할 컬렉션을 만듭니다. Postman 앱에서 **새로 만들기**를 선택 합니다. **새로 만들기** 창에서 **컬렉션**을 선택 합니다. 컬렉션 이름을로 하 고 **만들기** 단추를 선택 합니다. 

2. 요청을 만들려면 **새로 만들기** 를 다시 선택 합니다. **새로 만들기** 창에서 **요청**을 선택 합니다. 압정에 대 한 **요청 이름을** 입력 하 고, 이전 단계에서 만든 컬렉션을 요청을 저장할 위치로 선택 하 고, **저장**을 선택 합니다.
    
    ![Postman에서 요청 만들기](./media/how-to-render-custom-data/postman-new.png)

3. 작성기 탭에서 GET HTTP 메서드를 선택 하 고 다음 URL을 입력 하 여 GET 요청을 만듭니다.

    ```HTTP
    https://atlas.microsoft.com/map/static/png?subscription-key={subscription-key}&api-version=1.0&layer=basic&style=main&zoom=12&center=-73.98,%2040.77&pins=custom%7Cla15+50%7Cls12%7Clc003b61%7C%7C%27CentralPark%27-73.9657974+40.781971%7C%7Chttp%3A%2F%2Fazuremapscodesamples.azurewebsites.net%2FCommon%2Fimages%2Fpushpins%2Fylw-pushpin.png
    ```
    결과 이미지는 다음과 같습니다.

    ![레이블이 있는 사용자 지정 압정](./media/how-to-render-custom-data/render-pins.png)


## <a name="get-data-from-azure-maps-data-storage"></a>Azure Maps 데이터 스토리지에서 데이터 가져오기

> [!Note]
> 이 섹션의 절차에는 가격 책정 계층 s 1의 Azure Maps 계정이 필요 합니다.

[데이터 업로드 API](https://docs.microsoft.com/rest/api/maps/data/uploadpreview)를 사용 하 여 경로 및 pin 위치 정보를 가져올 수도 있습니다. 경로 및 핀 데이터를 업로드하려면 아래 단계를 수행합니다.

1. Postman 앱에서 이전 섹션에서 만든 컬렉션의 새 탭을 엽니다. 작성기 탭에서 POST HTTP 메서드를 선택 하 고 다음 URL을 입력 하 여 POST 요청을 수행 합니다.

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```

2. **매개 변수** 탭에서 POST 요청 URL에 사용 되는 다음 키/값 쌍을 입력 합니다. 값을 `subscription-key` Azure Maps 구독 키로 바꿉니다.
    
    ![Postman의 키/값 매개 변수](./media/how-to-render-custom-data/postman-key-vals.png)

3. **본문** 탭에서 원시 입력 형식을 선택 하 고 드롭다운 목록에서 JSON을 입력 형식으로 선택 합니다. 업로드할 데이터로이 JSON 제공:
    
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

4. **보내기** 를 선택 하 고 응답 헤더를 검토 합니다. 요청이 성공적으로 완료 되 면 Location 헤더는 상태 URI를 포함 하 여 업로드 요청의 현재 상태를 확인 합니다. 상태 URI는 다음과 같은 형식입니다.  

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0
   ```

5. 상태 URI를 복사 하 고 해당 값을 데이터를 업로드 하는 데 사용한 Azure Maps 계정 구독 키로 사용 하 여 구독 키 매개 변수를 추가 합니다. 상태 URI 형식은 아래와 같습니다.

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0&subscription-key={Subscription-key}
   ```

6. 을 (를) 가져오려면 Postman 앱에서 새 탭을 열고 작성기 탭에서 GET HTTP 메서드를 선택 하 고 상태 URI에서 GET 요청을 수행 합니다. 데이터 업로드가 성공하면 응답 본문에 udId가 있습니다. UdId를 복사 합니다.

   ```JSON
   {
      "udid" : "{udId}"
   }
   ```

7. 데이터 업로드 API에서 받은 값을사용하여맵의기능을렌더링합니다.`udId` 이렇게 하려면 이전 섹션에서 만든 컬렉션에서 새 탭을 엽니다. 작성기 탭에서 GET HTTP 메서드를 선택 하 고 다음 URL을 입력 하 여 GET 요청을 수행 합니다.

    ```HTTP
    https://atlas.microsoft.com/map/static/png?subscription-key={subscription-key}&api-version=1.0&layer=basic&style=main&zoom=12&center=-73.96682739257812%2C40.78119135317995&pins=default|la-35+50|ls12|lc003C62|co9B2F15||'Times Square'-73.98516297340393 40.758781646381024|'Central Park'-73.96682739257812 40.78119135317995&path=lc0000FF|fc0000FF|lw3|la0.80|fa0.30||udid-{udId}
    ```

    응답 이미지는 다음과 같습니다.

    ![Azure Maps 데이터 스토리지에서 데이터 가져오기](./media/how-to-render-custom-data/uploaded-path.png)

## <a name="render-a-polygon-with-color-and-opacity"></a>색 및 불투명도를 사용 하 여 다각형 렌더링

> [!Note]
> 이 섹션의 절차에는 가격 책정 계층 s 1의 Azure Maps 계정이 필요 합니다.


[경로 매개 변수](https://docs.microsoft.com/rest/api/maps/render/getmapimage#uri-parameters)와 스타일 한정자를 사용하여 다각형의 모양을 수정할 수 있습니다.

1. Postman 앱에서 이전에 만든 컬렉션의 새 탭을 엽니다. 작성기 탭에서 GET HTTP 메서드를 선택 하 고 다음 URL을 입력 하 여 색 및 불투명도를 사용 하 여 다각형을 렌더링 하도록 GET 요청을 구성 합니다.
    
    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&sku=S1&zoom=14&height=500&Width=500&center=-74.040701, 40.698666&path=lc0000FF|fc0000FF|lw3|la0.80|fa0.50||-74.03995513916016 40.70090237454063|-74.04082417488098 40.70028420372218|-74.04113531112671 40.70049568385827|-74.04298067092896 40.69899904076542|-74.04271245002747 40.69879568992435|-74.04367804527283 40.6980961582905|-74.04364585876465 40.698055487620714|-74.04368877410889 40.698022951066996|-74.04168248176573 40.696444909137|-74.03901100158691 40.69837271818651|-74.03824925422668 40.69837271818651|-74.03809905052185 40.69903971085914|-74.03771281242369 40.699340668780984|-74.03940796852112 40.70058515602143|-74.03948307037354 40.70052821920425|-74.03995513916016 40.70090237454063
    &subscription-key={subscription-key}
    ```

    응답 이미지는 다음과 같습니다.

    ![불투명 다각형 렌더링](./media/how-to-render-custom-data/opaque-polygon.png)


## <a name="render-a-circle-and-pushpins-with-custom-labels"></a>사용자 지정 레이블을 사용 하 여 원 및 압정 렌더링

> [!Note]
> 이 섹션의 절차에는 가격 책정 계층 s 1의 Azure Maps 계정이 필요 합니다.


크기 조정 스타일 한정자를 `sc` 사용 하 여 압정 및 해당 레이블을 더 크거나 작게 만들 수 있습니다. 이 한정자는 0 보다 큰 값을 사용 합니다. 값 1이 표준 배율입니다. 값이 1보다 크면 핀이 확대되고, 값이 1보다 작으면 축소됩니다. 스타일 한정자에 대 한 자세한 내용은 [정적 이미지 서비스 경로 매개 변수](https://docs.microsoft.com/rest/api/maps/render/getmapimage#uri-parameters)를 참조 하세요.


사용자 지정 레이블을 사용 하 여 원과 압정를 렌더링 하려면 다음 단계를 따르세요.

1. Postman 앱에서 이전에 만든 컬렉션의 새 탭을 엽니다. 작성기 탭에서 GET HTTP 메서드를 선택 하 고 다음 URL을 입력 하 여 GET 요청을 수행 합니다.

    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&zoom=14&height=700&Width=700&center=-122.13230609893799,47.64599069048016&path=lcFF0000|lw2|la0.60|ra1000||-122.13230609893799 47.64599069048016&pins=default|la15+50|al0.66|lc003C62|co002D62||'Microsoft Corporate Headquarters'-122.14131832122801  47.64690503939462|'Microsoft Visitor Center'-122.136828 47.642224|'Microsoft Conference Center'-122.12552547454833 47.642940335653996|'Microsoft The Commons'-122.13687658309935  47.64452336193245&subscription-key={subscription-key}
    ```

    응답 이미지는 다음과 같습니다.

    ![사용자 지정 압정를 사용 하 여 원 렌더링](./media/how-to-render-custom-data/circle-custom-pins.png)

## <a name="next-steps"></a>다음 단계


* [Azure Maps 지도 이미지 가져오기 API](https://docs.microsoft.com/rest/api/maps/render/getmapimage) 문서를 살펴봅니다.
* Azure Maps Data Service에 대 한 자세한 내용은 [서비스 설명서](https://docs.microsoft.com/rest/api/maps/data)를 참조 하세요.

