---
title: Azure 지도로 맵 만들기 | 마이크로소프트 Azure 지도
description: 이 문서에서는 Microsoft Azure Maps 웹 SDK를 사용하여 웹 페이지에서 맵을 렌더링하는 방법을 알아봅니다.
author: jinzh-azureiot
ms.author: jinzh
ms.date: 07/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: c85d6078fce7fc8e5a5b5d8485517a8b262044a9
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/07/2020
ms.locfileid: "80802334"
---
# <a name="create-a-map"></a>맵 만들기

이 문서에서는 맵을 만들고 맵을 애니메이션하는 방법을 보여줍니다.  

## <a name="loading-a-map"></a>맵 로드

맵을 로드하려면 [맵 클래스의](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map)새 인스턴스를 만듭니다. 맵을 초기화할 때 DIV 요소 ID를 전달하여 맵을 렌더링하고 맵을 로드할 때 사용할 옵션 집합을 전달합니다. `atlas` 네임스페이스에 기본 인증 정보가 지정되지 않은 경우 맵을 로드할 때 맵 옵션에 이 정보를 지정해야 합니다. 맵은 성능을 위해 비동기적으로 여러 리소스를 로드합니다. 따라서 맵 인스턴스를 만든 후 `ready` 맵에 또는 `load` 이벤트를 연결한 다음 맵과 상호 작용하는 추가 코드를 이벤트 처리기에 추가합니다. 맵에 `ready` 프로그래밍 방식으로 상호 작용할 수 있는 충분한 리소스가 로드되는 즉시 이벤트가 발생합니다. 초기 `load` 맵 뷰가 완전히 로드된 후 이벤트가 발생합니다. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="기본 맵 로드" src="//codepen.io/azuremaps/embed/rXdBXx/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen에서</a>Azure 지도 ()별<a href='https://codepen.io/azuremaps'>@azuremaps</a>펜 <a href='https://codepen.io/azuremaps/pen/rXdBXx/'>기본 맵 로드를</a> 참조하십시오.
</iframe>

> [!TIP]
> 동일한 페이지에 여러 맵을 로드할 수 있습니다. 동일한 페이지의 여러 맵은 동일하거나 다른 인증 및 언어 설정을 사용할 수 있습니다.

## <a name="show-a-single-copy-of-the-world"></a>세계의 단일 사본 표시

맵이 와이드 스크린에서 축소되면 여러 개의 월드 복사본이 가로로 표시됩니다. 이 옵션은 일부 시나리오에서는 좋지만 다른 응용 프로그램의 경우 세계의 단일 복사본을 보는 것이 좋습니다. 이 동작은 맵 `renderWorldCopies` 옵션을 로 `false`설정하여 구현됩니다.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="렌더월드복사 = 거짓" src="//codepen.io/azuremaps/embed/eqMYpZ/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen에서</a>펜 <a href='https://codepen.io/azuremaps/pen/eqMYpZ/'>렌더월드복사 =</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure 지도 ()에 의해 false를 참조하십시오.
</iframe>


## <a name="map-options"></a>지도 옵션

맵을 만들 때 맵이 아래에 나열된 대로 작동하는 방식을 사용자 지정하기 위해 전달할 수 있는 여러 가지 옵션 유형이 있습니다.

- [카메라옵션](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.cameraoptions) 및 [카메라바운드옵션은](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.cameraboundsoptions) 맵이 표시할 영역을 지정하는 데 사용됩니다.
- [ServiceOptions는](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.serviceoptions) 맵에 전원을 공급하는 서비스와 맵이 상호 작용하는 방법을 지정하는 데 사용됩니다.
- [StyleOptions는](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.styleoptions) 맵의 스타일을 지정하고 렌더링하도록 지정하는 데 사용됩니다.
- [UserInteractionOptions는](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.userinteractionoptions) 사용자가 맵과 상호 작용할 때 맵에 도달하는 방법을 지정하는 데 사용됩니다. 

이러한 옵션은 `setCamera`" `setServiceOptions` `setStyle`및 `setUserInteraction` 함수를 사용하여 맵을 로드한 후에도 업데이트할 수 있습니다. 

## <a name="controlling-the-map-camera"></a>맵 카메라 제어

맵의 카메라를 사용하여 맵의 표시된 영역을 설정하는 방법에는 두 가지가 있습니다. 맵을 로드할 때 카메라 옵션을 설정할 수 있습니다. 또는 맵이 `setCamera` 로드된 후 언제든지 옵션을 호출하여 맵 보기를 프로그래밍 방식으로 업데이트할 수 있습니다.  

<a id="setCameraOptions"></a>

### <a name="set-the-camera"></a>카메라 설정

맵 카메라는 맵 캔버스의 뷰포트에 표시되는 내용을 제어합니다. 카메라 옵션은 초기화되거나 맵 기능으로 전달될 때 `setCamera` 맵 옵션으로 전달될 수 있습니다.

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

다음 코드에서는 [맵 오브젝트가](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map) 만들어지고 가운데 및 확대/축소 옵션이 설정됩니다. 중심 및 확대/축소 수준과 같은 맵 속성은 [카메라 옵션의](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.cameraoptions)일부입니다.

<br/>

<iframe height='500' scrolling='no' title='CameraOptions를 통해 맵 만들기' src='//codepen.io/azuremaps/embed/qxKBMN/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>의 Pen <a href='https://codepen.io/azuremaps/pen/qxKBMN/'>Azure Location Based Services에서 제공되는 `CameraOptions` </a>를 통해 맵 만들기(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)를 참조하세요.
</iframe>

<a id="setCameraBoundsOptions"></a>

### <a name="set-the-camera-bounds"></a>카메라 경계 설정

경계 상자를 사용하여 맵 카메라를 업데이트할 수 있습니다. 경계 상자가 점 데이터에서 계산된 경우 아이콘 크기를 고려하여 카메라 옵션에서 픽셀 패딩 값을 지정하는 것이 유용한 경우가 많습니다. 이렇게 하면 포인트가 맵 뷰포트의 가장자리에서 떨어지지 않도록 할 수 있습니다.

```javascript
map.setCamera({
    bounds: [-122.4, 47.6, -122.3, 47.7],
    padding: 10
});
```

다음 코드에서는 [맵 개체를](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map) 통해 `new atlas.Map()`생성됩니다. `CameraBoundsOptions`와 같은 맵 속성은 맵 클래스의 [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map) 함수를 통해 정의될 수 있습니다. 경계 및 안쪽 여백 속성은 `setCamera`를 사용하여 설정됩니다.

<br/>

<iframe height='500' scrolling='no' title='CameraBoundsOptions를 통해 맵 만들기' src='//codepen.io/azuremaps/embed/ZrRbPg/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>의 Pen <a href='https://codepen.io/azuremaps/pen/ZrRbPg/'>Azure Maps에서 제공되는 `CameraBoundsOptions` </a>를 통해 맵 만들기(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)를 참조하세요.
</iframe>

### <a name="animate-map-view"></a>맵 보기 애니메이션

맵의 카메라 옵션을 설정할 때 [애니메이션 옵션을](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.animationoptions) 설정할 수도 있습니다. 이러한 옵션은 카메라를 이동하는 데 걸리는 애니메이션 유형과 기간을 지정합니다.

```javascript
map.setCamera({
    center: [-122.33, 47.6],
    zoom: 12,
    duration: 1000,
    type: 'fly'  
});
```

다음 코드에서 첫 번째 코드 블록은 맵을 만들고 입력 및 확대/축소 맵 스타일을 설정합니다. 두 번째 코드 블록에서는 애니메이션 단추에 대해 클릭 이벤트 처리기가 만들어집니다. 이 단추를 클릭하면 `setCamera` CameraOptions 및 애니메이션 [옵션에](/javascript/api/azure-maps-control/atlas.cameraoptions) 대한 임의의 값으로 함수가 [호출됩니다.](/javascript/api/azure-maps-control/atlas.animationoptions)

<br/>

<iframe height='500' scrolling='no' title='맵 보기 애니메이션' src='//codepen.io/azuremaps/embed/WayvbO/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>의 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)에서 제공되는 Pen <a href='https://codepen.io/azuremaps/pen/WayvbO/'>맵 보기 애니메이션</a>을 참조하세요.
</iframe>

## <a name="try-out-the-code"></a>코드 사용해 보기

코드 샘플을 살펴봅니다. **JS 탭** 내에서 자바스크립트 코드를 편집하고 **결과 탭에서**맵 뷰 변경 사항을 확인할 수 있습니다. 오른쪽 상단 **모서리에서 CodePen에서 편집을**클릭하고 CodePen에서 코드를 수정할 수도 있습니다.

<a id="relatedReference"></a>

## <a name="next-steps"></a>다음 단계

이 문서에서 사용된 클래스 및 메서드에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [지도](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [카메라 옵션](/javascript/api/azure-maps-control/atlas.cameraoptions)

> [!div class="nextstepaction"]
> [애니메이션 옵션](/javascript/api/azure-maps-control/atlas.animationoptions)

앱에 기능을 추가하는 코드 예제를 참조하세요.

> [!div class="nextstepaction"]
> [맵 스타일 변경](choose-map-style.md)

> [!div class="nextstepaction"]
> [맵에 컨트롤 추가](map-add-controls.md)

> [!div class="nextstepaction"]
> [코드 샘플](https://docs.microsoft.com/samples/browse/?products=azure-maps)
