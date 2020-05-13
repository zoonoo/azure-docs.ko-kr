---
title: Azure Maps를 사용 하 여 맵 만들기 Microsoft Azure 맵
description: 이 문서에서는 Microsoft Azure Maps 웹 SDK를 사용 하 여 웹 페이지에서 지도를 렌더링 하는 방법을 알아봅니다.
author: Philmea
ms.author: philmea
ms.date: 07/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 97eb1ebb61e5ff78ed918fded8107f5775b533c2
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83124026"
---
# <a name="create-a-map"></a>맵 만들기

이 문서에서는 맵을 만들고 맵을 애니메이션하는 방법을 보여줍니다.  

## <a name="loading-a-map"></a>지도 로드

지도를 로드 하려면 [map 클래스](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map)의 새 인스턴스를 만듭니다. 맵을 초기화할 때 DIV 요소 ID를 전달 하 여 맵을 렌더링 하 고 지도를 로드할 때 사용할 옵션 집합을 전달 합니다. 네임 스페이스에 기본 인증 정보가 지정 되지 않은 경우 `atlas` 지도를 로드할 때 맵 옵션에서이 정보를 지정 해야 합니다. 맵은 성능을 위해 여러 리소스를 비동기적으로 로드 합니다. 따라서 맵 인스턴스를 만든 후에 `ready` 또는 이벤트를 맵에 연결한 `load` 다음 맵과 상호 작용 하는 추가 코드를 이벤트 처리기에 추가 합니다. `ready`이 이벤트는 map에서 프로그래밍 방식으로 상호 작용할 수 있는 충분 한 리소스를 로드 하는 즉시 발생 합니다. `load`이 이벤트는 초기 지도 보기의 로드가 완전히 완료 된 후에 발생 합니다. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="기본 지도 부하" src="//codepen.io/azuremaps/embed/rXdBXx/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
CodePen의 Azure Maps ()에서 펜 <a href='https://codepen.io/azuremaps/pen/rXdBXx/'>기본 지도 로드</a> 를 참조 하세요 <a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> 여러 맵을 동일한 페이지에 로드할 수 있습니다. 동일한 페이지의 여러 지도에서 동일 하거나 다른 인증 및 언어 설정을 사용할 수 있습니다.

## <a name="show-a-single-copy-of-the-world"></a>전 세계의 단일 복사본 표시

넓은 화면에서 지도를 축소 하면 전 세계의 여러 복사본이 가로로 표시 됩니다. 이 옵션은 일부 시나리오에는 유용 하지만 다른 응용 프로그램의 경우에는 전 세계의 단일 복사본을 확인 하는 것이 좋습니다. 이 동작은 maps `renderWorldCopies` 옵션을로 설정 하 여 구현 `false` 합니다.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="renderWorldCopies = false" src="//codepen.io/azuremaps/embed/eqMYpZ/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
CodePen에서 Azure Maps ()로 Pen <a href='https://codepen.io/azuremaps/pen/eqMYpZ/'>renderWorldCopies = false</a> 를 참조 하세요 <a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="map-options"></a>맵 옵션

지도를 만들 때에는 아래 나열 된 것 처럼 map의 기능을 사용자 지정 하기 위해 전달할 수 있는 여러 가지 유형의 옵션이 있습니다.

- [CameraOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.cameraoptions) 및 [CameraBoundOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.cameraboundsoptions) 를 사용 하 여 지도에서 표시 해야 하는 영역을 지정 합니다.
- [Serviceoptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.serviceoptions) 는 map이 지도를 구동 하는 서비스와 상호 작용 하는 방법을 지정 하는 데 사용 됩니다.
- [StyleOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.styleoptions) 를 사용 하 여 지도를 지정 하 고 렌더링 해야 합니다.
- [UserInteractionOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.userinteractionoptions) 는 사용자가 지도와 상호 작용할 때 map에 도달 하는 방법을 지정 하는 데 사용 됩니다. 

`setCamera`,, `setServiceOptions` `setStyle` 및 함수를 사용 하 여 맵을 로드 한 후에도 이러한 옵션을 업데이트할 수 있습니다 `setUserInteraction` . 

## <a name="controlling-the-map-camera"></a>지도 카메라 제어

지도의 카메라를 사용 하 여 지도의 표시 된 영역을 설정 하는 방법에는 두 가지가 있습니다. 지도를 로드할 때 카메라 옵션을 설정할 수 있습니다. 또는 맵이 `setCamera` 로드 된 후 언제 든 지 옵션을 호출 하 여 맵 뷰를 프로그래밍 방식으로 업데이트할 수 있습니다.  

<a id="setCameraOptions"></a>

### <a name="set-the-camera"></a>카메라 설정

지도 카메라는 지도 캔버스의 뷰포트에 표시 되는 항목을 제어 합니다. 카메라 옵션은 초기화 되거나 맵 함수에 전달 될 때 맵 옵션으로 전달 될 수 있습니다 `setCamera` .

```javascript
//Set the camera options when creating the map.
var map = new atlas.Map('map', {
    center: [-122.33, 47.6],
    zoom: 12

    //Additional map options.
};

//Update the map camera at anytime using setCamera function.
map.setCamera({
    center: [-110, 45],
    zoom: 5 
});
```

다음 코드에서는 [Map 개체가](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map) 만들어지고 중심 및 확대/축소 옵션이 설정 됩니다. 가운데 및 확대/축소 수준과 같은 지도 속성은 [CameraOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.cameraoptions)의 일부입니다.

<br/>

<iframe height='500' scrolling='no' title='CameraOptions를 통해 맵 만들기' src='//codepen.io/azuremaps/embed/qxKBMN/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>의 Pen <a href='https://codepen.io/azuremaps/pen/qxKBMN/'>Azure Location Based Services에서 제공되는 `CameraOptions` </a>를 통해 맵 만들기(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)를 참조하세요.
</iframe>

<a id="setCameraBoundsOptions"></a>

### <a name="set-the-camera-bounds"></a>카메라 경계 설정

경계 상자는 지도 카메라를 업데이트 하는 데 사용할 수 있습니다. 경계 상자가 점 데이터에서 계산 된 경우 카메라 옵션에서 픽셀 패딩 값을 지정 하 여 아이콘 크기를 고려 하는 것이 유용한 경우가 많습니다. 그러면 요소가 지도 뷰포트의 가장자리를 벗어날 수 있습니다.

```javascript
map.setCamera({
    bounds: [-122.4, 47.6, -122.3, 47.7],
    padding: 10
});
```

다음 코드에서 [Map 개체](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map) 는을 통해 생성 됩니다 `new atlas.Map()` . `CameraBoundsOptions`와 같은 맵 속성은 맵 클래스의 [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map) 함수를 통해 정의될 수 있습니다. 경계 및 안쪽 여백 속성은 `setCamera`를 사용하여 설정됩니다.

<br/>

<iframe height='500' scrolling='no' title='CameraBoundsOptions를 통해 맵 만들기' src='//codepen.io/azuremaps/embed/ZrRbPg/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>의 Pen <a href='https://codepen.io/azuremaps/pen/ZrRbPg/'>Azure Maps에서 제공되는 `CameraBoundsOptions` </a>를 통해 맵 만들기(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)를 참조하세요.
</iframe>

### <a name="animate-map-view"></a>맵 보기 애니메이션

지도의 카메라 옵션을 설정할 때 [애니메이션 옵션도](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.animationoptions) 설정할 수 있습니다. 이러한 옵션은 카메라를 이동 하기 위해 수행 해야 하는 애니메이션의 유형 및 기간을 지정 합니다.

```javascript
map.setCamera({
    center: [-122.33, 47.6],
    zoom: 12,
    duration: 1000,
    type: 'fly'  
});
```

다음 코드에서 첫 번째 코드 블록은 지도를 만들고 입력 및 확대/축소 맵 스타일을 설정 합니다. 두 번째 코드 블록에서 애니메이션 효과에 대 한 click 이벤트 처리기가 만들어집니다. 이 단추를 클릭 하면 `setCamera` [CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions) 및 [애니메이션 옵션](/javascript/api/azure-maps-control/atlas.animationoptions)에 대 한 임의 값을 사용 하 여 함수가 호출 됩니다.

<br/>

<iframe height='500' scrolling='no' title='맵 보기 애니메이션' src='//codepen.io/azuremaps/embed/WayvbO/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>의 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)에서 제공되는 Pen <a href='https://codepen.io/azuremaps/pen/WayvbO/'>맵 보기 애니메이션</a>을 참조하세요.
</iframe>

## <a name="try-out-the-code"></a>코드 사용해 보기

코드 샘플을 확인 합니다. **JS 탭** 내에서 JavaScript 코드를 편집 하 고 **결과 탭**에서 맵 보기 변경 내용을 볼 수 있습니다. 오른쪽 위 모서리에서 **CodePen에서 편집**을 클릭 하 고 CodePen에서 코드를 수정할 수도 있습니다.

<a id="relatedReference"></a>

## <a name="next-steps"></a>다음 단계

이 문서에서 사용된 클래스 및 메서드에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [맵](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions)

> [!div class="nextstepaction"]
> [애니메이션 옵션](/javascript/api/azure-maps-control/atlas.animationoptions)

앱에 기능을 추가하는 코드 예제를 참조하세요.

> [!div class="nextstepaction"]
> [맵 스타일 변경](choose-map-style.md)

> [!div class="nextstepaction"]
> [맵에 컨트롤 추가](map-add-controls.md)

> [!div class="nextstepaction"]
> [코드 샘플](https://docs.microsoft.com/samples/browse/?products=azure-maps)
