---
title: 래스터 맵에서 사용자 지정 데이터 렌더링 | 마이크로소프트 Azure 지도
description: 이 문서에서는 Microsoft Azure Maps 정적 이미지 서비스를 사용하여 래스터 맵에서 사용자 지정 데이터를 렌더링하는 방법을 알아봅니다.
author: philmea
ms.author: philmea
ms.date: 01/23/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: b8d47b69b4aba14c86fb09176b662aee7d5482d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335511"
---
# <a name="render-custom-data-on-a-raster-map"></a>래스터 맵에서 사용자 지정 데이터 렌더링

이 문서에서는 이미지 컴포지션 기능을 사용하여 래스터 맵 위에 오버레이를 허용하는 [정적 이미지 서비스를](https://docs.microsoft.com/rest/api/maps/render/getmapimage)사용하는 방법을 설명합니다. 이미지 컴포지션에는 사용자 지정 푸시핀, 레이블 및 형상 오버레이와 같은 추가 데이터를 사용하여 래스터 타일을 다시 얻을 수 있는 기능이 포함됩니다.

사용자 지정 푸시핀, 레이블 및 형상 오버레이를 렌더링하려면 Postman 응용 프로그램을 사용할 수 있습니다. Azure Maps [데이터 서비스 API를](https://docs.microsoft.com/rest/api/maps/data) 사용하여 오버레이를 저장하고 렌더링할 수 있습니다.

> [!Tip]
> 정적 이미지 서비스를 사용하는 것보다 Azure Maps Web SDK를 사용하여 웹 페이지에 간단한 맵을 표시하는 것이 훨씬 더 비용 효율적인 경우가 많습니다. 웹 SDK는 맵 타일을 사용하며 사용자가 맵을 이동 및 확대/축소하지 않는 한 맵 로드당 트랜잭션의 일부만 생성하는 경우가 많습니다. Azure Maps 웹 SDK에는 이동 및 확대/축소를 사용하지 않도록 설정하는 옵션이 있습니다. 또한 Azure Maps 웹 SDK는 정적 맵 웹 서비스보다 더 풍부한 데이터 시각화 옵션을 제공합니다.  

## <a name="prerequisites"></a>사전 요구 사항

### <a name="create-an-azure-maps-account"></a>Azure Maps 계정 만들기

이 문서의 절차를 완료하려면 먼저 Azure Maps 계정을 만들고 맵 계정 키를 얻어야 합니다. [계정 만들기의](quick-demo-map-app.md#create-an-account-with-azure-maps) 지침에 따라 Azure Maps 계정 구독을 만들고 [기본 키를 받는](quick-demo-map-app.md#get-the-primary-key-for-your-account) 단계를 수행하여 계정의 기본 키를 가져옵니다. Azure Maps의 인증에 대한 자세한 내용은 [Azure Maps의 인증 관리](./how-to-manage-authentication.md)를 참조하세요.


## <a name="render-pushpins-with-labels-and-a-custom-image"></a>레이블 및 사용자 지정 이미지가 있는 푸시핀 렌더링

> [!Note]
> 이 섹션의 절차에는 가격 책정 계층 S0 또는 S1의 Azure Maps 계정이 필요합니다.

Azure Maps 계정 S0 계층은 `pins` 매개 변수의 단일 인스턴스만 지원합니다. 사용자 지정 이미지로 URL 요청에 지정된 푸시핀을 최대 5개까지 렌더링할 수 있습니다.

레이블과 사용자 지정 이미지가 있는 푸시핀을 렌더링하려면 다음 단계를 완료합니다.

1. 요청을 저장할 컬렉션을 만듭니다. 우체부 앱에서 새 를 **선택합니다.** 새 **만들기** 창에서 **컬렉션**을 선택합니다. 컬렉션의 이름을 지정하고 **만들기** 단추를 선택합니다. 

2. 요청을 만들려면 **새로 만들기를** 다시 선택합니다. 새 **만들기** 창에서 **요청을**선택합니다. 푸시핀에 대한 **요청 이름을** 입력합니다. 요청을 저장할 위치로 이전 단계에서 만든 컬렉션을 선택합니다. 그런 다음 **저장을**선택합니다.
    
    ![우체부에서 요청 만들기](./media/how-to-render-custom-data/postman-new.png)

3. 빌더 탭에서 GET HTTP 메서드를 선택하고 다음 URL을 입력하여 GET 요청을 만듭니다.

    ```HTTP
    https://atlas.microsoft.com/map/static/png?subscription-key={subscription-key}&api-version=1.0&layer=basic&style=main&zoom=12&center=-73.98,%2040.77&pins=custom%7Cla15+50%7Cls12%7Clc003b61%7C%7C%27CentralPark%27-73.9657974+40.781971%7C%7Chttps%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FAzureMapsCodeSamples%2Fmaster%2FAzureMapsCodeSamples%2FCommon%2Fimages%2Ficons%2Fylw-pushpin.png
    ```
    결과 이미지는 다음과 같습니다.

    ![레이블이 있는 사용자 지정 푸시핀](./media/how-to-render-custom-data/render-pins.png)


## <a name="get-data-from-azure-maps-data-storage"></a>Azure Maps 데이터 스토리지에서 데이터 가져오기

> [!Note]
> 이 섹션의 절차에는 가격 책정 계층 S1의 Azure Maps 계정이 필요합니다.

[데이터 업로드 API를](https://docs.microsoft.com/rest/api/maps/data/uploadpreview)사용하여 경로 및 핀 위치 정보를 얻을 수도 있습니다. 경로 및 핀 데이터를 업로드하려면 아래 단계를 수행합니다.

1. Postman 앱에서 이전 섹션에서 만든 컬렉션에서 새 탭을 엽니다. 빌더 탭에서 POST HTTP 메서드를 선택하고 다음 URL을 입력하여 POST 요청을 합니다.

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```

2. **Params** 탭에서 POST 요청 URL에 사용되는 다음 키/값 쌍을 입력합니다. `subscription-key` 값을 Azure Maps 구독 키로 바꿉습니다.
    
    ![우체부에서 키/값 파라마](./media/how-to-render-custom-data/postman-key-vals.png)

3. **Body** 탭에서 원시 입력 형식을 선택하고 드롭다운 목록에서 JSON을 입력 형식으로 선택합니다. 업로드할 데이터로 이 JSON을 제공합니다.
    
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

4. 응답 헤더 **보내기** 및 검토를 선택합니다. 요청이 성공하면 Location(위치) 헤더에 업로드 요청의 현재 상태를 확인하기 위한 상태 URI가 포함됩니다. 상태 URI는 다음 형식입니다.  

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0
   ```

5. 상태 URI를 복사하고 Azure Maps 계정 구독 키의 값으로 구독 키 매개 변수를 부호 지정합니다. 데이터를 업로드하는 데 사용한 것과 동일한 계정 구독 키를 사용합니다. 상태 URI 형식은 아래와 같아야 합니다.

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0&subscription-key={Subscription-key}
   ```

6. udId를 얻으려면 우체부 앱에서 새 탭을 엽니다. 빌더 탭에서 GET HTTP 메서드를 선택합니다. 데이터 업로드에 성공한 경우 응답 본문에 udId가 표시됩니다. udId를 복사합니다.

   ```JSON
   {
      "udid" : "{udId}"
   }
   ```

7. 데이터 `udId` 업로드 API에서 받은 값을 사용하여 맵에서 피처를 렌더링합니다. 이렇게 하려면 이전 섹션에서 만든 컬렉션에서 새 탭을 엽니다. 빌더 탭에서 GET HTTP 메서드를 선택하고 {구독 키} 및 {udId}를 값으로 바꾸고 이 URL을 입력하여 GET 요청을 합니다.

    ```HTTP
    https://atlas.microsoft.com/map/static/png?subscription-key={subscription-key}&api-version=1.0&layer=basic&style=main&zoom=12&center=-73.96682739257812%2C40.78119135317995&pins=default|la-35+50|ls12|lc003C62|co9B2F15||'Times Square'-73.98516297340393 40.758781646381024|'Central Park'-73.96682739257812 40.78119135317995&path=lc0000FF|fc0000FF|lw3|la0.80|fa0.30||udid-{udId}
    ```

    응답 이미지는 다음과 같습니다.

    ![Azure Maps 데이터 스토리지에서 데이터 가져오기](./media/how-to-render-custom-data/uploaded-path.png)

## <a name="render-a-polygon-with-color-and-opacity"></a>색상과 불투명도가 있는 다각형 렌더링

> [!Note]
> 이 섹션의 절차에는 가격 책정 계층 S1의 Azure Maps 계정이 필요합니다.


[경로 매개 변수](https://docs.microsoft.com/rest/api/maps/render/getmapimage#uri-parameters)와 스타일 한정자를 사용하여 다각형의 모양을 수정할 수 있습니다.

1. 우체부 앱에서 이전에 만든 컬렉션에서 새 탭을 엽니다. 빌더 탭에서 GET HTTP 메서드를 선택하고 다음 URL을 입력하여 색상및 불투명도가 있는 다각형을 렌더링하는 GET 요청을 구성합니다.
    
    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&sku=S1&zoom=14&height=500&Width=500&center=-74.040701, 40.698666&path=lc0000FF|fc0000FF|lw3|la0.80|fa0.50||-74.03995513916016 40.70090237454063|-74.04082417488098 40.70028420372218|-74.04113531112671 40.70049568385827|-74.04298067092896 40.69899904076542|-74.04271245002747 40.69879568992435|-74.04367804527283 40.6980961582905|-74.04364585876465 40.698055487620714|-74.04368877410889 40.698022951066996|-74.04168248176573 40.696444909137|-74.03901100158691 40.69837271818651|-74.03824925422668 40.69837271818651|-74.03809905052185 40.69903971085914|-74.03771281242369 40.699340668780984|-74.03940796852112 40.70058515602143|-74.03948307037354 40.70052821920425|-74.03995513916016 40.70090237454063
    &subscription-key={subscription-key}
    ```

    응답 이미지는 다음과 같습니다.

    ![불투명 한 다각형 렌더링](./media/how-to-render-custom-data/opaque-polygon.png)


## <a name="render-a-circle-and-pushpins-with-custom-labels"></a>사용자 지정 레이블로 원 및 푸시핀 렌더링

> [!Note]
> 이 섹션의 절차에는 가격 책정 계층 S1의 Azure Maps 계정이 필요합니다.


스타일 수정자를 추가하여 핀의 모양을 수정할 수 있습니다. 예를 들어 푸시핀과 해당 레이블을 더 크거나 `sc` 작게 만들려면 "배율 스타일" 수정자를 사용합니다. 이 수정자는 0보다 큰 값을 취합니다. 값 1이 표준 배율입니다. 값이 1보다 크면 핀이 확대되고, 값이 1보다 작으면 축소됩니다. 스타일 수정자에 대한 자세한 내용은 [정적 이미지 서비스 경로 매개 변수를](https://docs.microsoft.com/rest/api/maps/render/getmapimage#uri-parameters)참조하십시오.


다음 단계에 따라 사용자 지정 레이블이 있는 원 및 푸시핀을 렌더링합니다.

1. 우체부 앱에서 이전에 만든 컬렉션에서 새 탭을 엽니다. 빌더 탭에서 GET HTTP 메서드를 선택하고 이 URL을 입력하여 GET 요청을 합니다.

    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&zoom=14&height=700&Width=700&center=-122.13230609893799,47.64599069048016&path=lcFF0000|lw2|la0.60|ra1000||-122.13230609893799 47.64599069048016&pins=default|la15+50|al0.66|lc003C62|co002D62||'Microsoft Corporate Headquarters'-122.14131832122801  47.64690503939462|'Microsoft Visitor Center'-122.136828 47.642224|'Microsoft Conference Center'-122.12552547454833 47.642940335653996|'Microsoft The Commons'-122.13687658309935  47.64452336193245&subscription-key={subscription-key}
    ```

    응답 이미지는 다음과 같습니다.

    ![사용자 지정 푸시핀으로 원 렌더링](./media/how-to-render-custom-data/circle-custom-pins.png)

2. 마지막 단계에서 푸시핀의 색상을 변경하려면 "공동" 스타일 수정자를 변경합니다. `pins=default|la15+50|al0.66|lc003C62|co002D62|`현재 색상은 CSS에서 #002D62 지정됩니다. #41d42a 변경한다고 가정해 보겠습니다. 다음과 같이 "co" 지정자 다음의 새 색상 `pins=default|la15+50|al0.66|lc003C62|co41D42A|`값을 작성합니다. 새 GET 요청 만들기:

    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&zoom=14&height=700&Width=700&center=-122.13230609893799,47.64599069048016&path=lcFF0000|lw2|la0.60|ra1000||-122.13230609893799 47.64599069048016&pins=default|la15+50|al0.66|lc003C62|co41D42A||'Microsoft Corporate Headquarters'-122.14131832122801  47.64690503939462|'Microsoft Visitor Center'-122.136828 47.642224|'Microsoft Conference Center'-122.12552547454833 47.642940335653996|'Microsoft The Commons'-122.13687658309935  47.64452336193245&subscription-key={subscription-key}
    ```

    핀의 색상을 변경한 후의 응답 이미지는 다음과 같습니다.

    ![업데이트된 푸시핀으로 원 렌더링](./media/how-to-render-custom-data/circle-updated-pins.png)

마찬가지로 다른 스타일 수정자를 변경, 추가 및 제거할 수 있습니다.

## <a name="next-steps"></a>다음 단계


* [Azure Maps 지도 이미지 가져오기 API](https://docs.microsoft.com/rest/api/maps/render/getmapimage) 문서를 살펴봅니다.
* Azure Maps 데이터 서비스에 대한 자세한 내용은 [서비스 설명서를](https://docs.microsoft.com/rest/api/maps/data)참조하십시오.

