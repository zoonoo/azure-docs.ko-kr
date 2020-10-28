---
title: Azure Maps Creator 실내 지도를 위한 동적 스타일 구현
description: Creator 실내 지도를 위한 동적 스타일 구현 방법 알아보기
author: anastasia-ms
ms.author: v-stharr
ms.date: 05/18/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: b9ce0d0770c7e6c4579469cc16d8c76c309a33d1
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/28/2020
ms.locfileid: "92895378"
---
# <a name="implement-dynamic-styling-for-creator-indoor-maps"></a>Creator 실내 지도를 위한 동적 스타일 구현

Azure Maps Creator [기능 상태 서비스](/rest/api/maps/featurestate)를 사용하여 실내 지도 데이터 기능의 동적 속성에 따라 스타일을 적용할 수 있습니다.  예를 들어 특정 색을 사용하여 재실 상태를 나타내도록 시설 회의실을 렌더링할 수 있습니다. 이 문서에서는 [기능 상태 서비스](/rest/api/maps/featurestate) 및 [실내 웹 모듈](how-to-use-indoor-module.md)을 사용하여 실내 지도 기능을 동적으로 렌더링하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>사전 요구 사항

1. [Azure Maps 계정 만들기](quick-demo-map-app.md#create-an-azure-maps-account)
2. 기본 키 또는 구독 키라고도 하는 [기본 구독 키를 가져옵니다](quick-demo-map-app.md#get-the-primary-key-for-your-account).
3. [Creator 리소스 만들기](how-to-manage-creator.md)
4. [샘플 그리기 패키지](https://github.com/Azure-Samples/am-creator-indoor-data-examples)를 다운로드합니다.
5. `tilesetId` 및 `statesetId`를 얻기 위해 [실내 지도를 만듭니다](tutorial-creator-indoor-maps.md).
6. [실내 지도 모듈 사용 방법](how-to-use-indoor-module.md)의 단계를 수행하여 웹 애플리케이션을 빌드합니다.

이 자습서에서는 [Postman](https://www.postman.com/) 애플리케이션을 사용하지만 다른 API 개발 환경을 선택할 수도 있습니다.

## <a name="implement-dynamic-styling"></a>동적 스타일 구현

필수 구성 요소를 완료한 후에는 구독 키, `tilesetId`및 `statesetId`를 사용하여 간단한 웹 애플리케이션을 구성해야 합니다.

### <a name="select-features"></a>기능 선택

동적 스타일을 구현하려면 회의실 같은 기능을 기능 `id`로 참조해야 합니다. `id` 기능을 사용하여 해당 기능의 동적 속성 또는 *상태* 를 업데이트합니다. 데이터 세트에 정의된 기능을 보려면 다음 방법 중 하나를 사용할 수 있습니다.

* WFS API(웹 기능 서비스) 데이터 세트는 WFS API를 사용하여 쿼리할 수 있습니다. WFS는 Open Geospatial Consortium API 기능을 따릅니다. WFS API는 데이터 세트 내 기능을 쿼리하는 데 유용합니다. 예를 들어 WFS를 사용하여 지정된 시설 및 층 수준의 모든 중간 규모 회의실을 찾을 수 있습니다.

* 사용자가 웹 애플리케이션을 사용하여 지도의 기능을 선택할 수 있는 사용자 지정 코드를 구현합니다. 이 문서에서는 이 옵션을 사용합니다.  

다음 스크립트는 마우스 클릭 이벤트를 구현합니다. 이 코드는 클릭한 지점에 따라 기능 `id`를 검색합니다. 애플리케이션에서 실내 관리자 코드 블록 아래에 코드를 삽입할 수 있습니다. 애플리케이션을 실행하고 콘솔을 확인하여 클릭한 지점의 기능 `id`를 가져옵니다.

```javascript
/* Upon a mouse click, log the feature properties to the browser's console. */
map.events.add("click", function(e){

    var features = map.layers.getRenderedShapes(e.position, "indoor");

    var result = features.reduce(function (ids, feature) {
        if (feature.layer.id == "indoor_unit_office") {
            console.log(feature);
        }
    }, []);
});
```

[실내 지도 만들기](tutorial-creator-indoor-maps.md) 자습서에서는 `occupancy`에 대한 상태 업데이트를 수락하도록 기능 상태 세트를 구성했습니다.

다음 섹션에서는 `UNIT26` 사무실의 재실 *상태* 를 `true`로 설정합니다. `UNIT27` 사무실은 `false`로 설정됩니다.

### <a name="set-occupancy-status"></a>재실 상태 설정

 이제 두 사무실 `UNIT26` 및 `UNIT27`의 상태를 업데이트합니다.

1. Postman 애플리케이션에서 **새로 만들기** 를 선택합니다. **새로 만들기** 창에서 **요청** 을 선택합니다. **요청 이름** 입력하고 컬렉션을 선택합니다. 페이지 맨 아래에 있는 **저장**

2. [기능 업데이트 상태 API](/rest/api/maps/featurestate/updatestatespreview)를 사용하여 상태를 업데이트합니다. 상태 세트 ID 및 두 단위 중 하나에 대한 `UNIT26`를 전달합니다. Azure Maps 구독 키를 추가합니다. 상태를 업데이트하는 **POST** 요청의 URL은 다음과 같습니다.

    ```http
    https://atlas.microsoft.com/featureState/state?api-version=1.0&statesetID={statesetId}&featureID=UNIT26&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

3. **POST** 요청의 **헤더** 에서 `Content-Type`을 `application/json`으로 설정합니다. **POST** 요청의 **본문** 에서 기능 업데이트를 사용하여 다음 JSON을 작성합니다. 게시된 타임스탬프가 동일한 기능 `ID`에 대한 이전 기능 상태 업데이트 요청에 사용된 타임스탬프보다 이후일 경우에만 업데이트가 저장됩니다. "occupied" `keyName`을 전달하여 값을 업데이트합니다.

    ```json
    {
        "states": [
            {
                "keyName": "occupied",
                "value": true,
                "eventTimestamp": "2019-11-14T17:10:20"
            }
        ]
    }
    ```

4. 다음 JSON에서 `UNIT27`을 사용하여 2단계와 3단계를 다시 수행합니다.

    ``` json
    {
        "states": [
            {
                "keyName": "occupied",
                "value": false,
                "eventTimestamp": "2019-11-14T17:10:20"
            }
        ]
    }
    ```

### <a name="visualize-dynamic-styles-on-a-map"></a>지도에서 동적 스타일 시각화

이전에 브라우저에서 연 웹 애플리케이션이 이제 지도 기능의 업데이트된 상태를 반영합니다. `UNIT27`(151)은 녹색으로 표시되고 `UNIT26`(157)은 빨간색으로 표시됩니다.

![녹색으로 표시된 빈 공간과 빨간색으로 표시된 사용 중인 공간](./media/indoor-map-dynamic-styling/room-state.png)

## <a name="next-steps"></a>다음 단계

자세한 정보는 다음을 참조하세요.

> [!div class="nextstepaction"]
> [실내 지도 제작용 Creator](creator-indoor-maps.md)

이 문서에서 언급한 API에 대한 참조를 확인하세요.

> [!div class="nextstepaction"]
> [데이터 업로드](creator-indoor-maps.md#upload-a-drawing-package)

> [!div class="nextstepaction"]
> [데이터 변환](creator-indoor-maps.md#convert-a-drawing-package)

> [!div class="nextstepaction"]
> [데이터 세트](creator-indoor-maps.md#datasets)

> [!div class="nextstepaction"]
> [타일 세트](creator-indoor-maps.md#tilesets)

> [!div class="nextstepaction"]
> [기능 상태 세트](creator-indoor-maps.md#feature-statesets)

> [!div class="nextstepaction"]
> [WFS 서비스](creator-indoor-maps.md#web-feature-service-api)