---
title: Azure Maps 웹 맵 컨트롤의 스타일 변경
description: 지도의 스타일 및 옵션을 변경 하는 방법에 대해 알아봅니다. 사용자가 다른 스타일 사이를 전환할 수 있도록 Azure Maps에서 지도에 스타일 선택 컨트롤을 추가 하는 방법을 참조 하세요.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/27/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: devx-track-javascript
ms.openlocfilehash: 15224afac33a3e236c2d1ed9f60515bdc2f34c4f
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/10/2020
ms.locfileid: "88036796"
---
# <a name="change-the-style-of-the-map"></a>맵 스타일 변경

지도 컨트롤은 다양 한 지도 [스타일 옵션](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.styleoptions) 및 [기본 지도 스타일](supported-map-styles.md)을 지원 합니다. 지도 컨트롤이 초기화 될 때 모든 스타일을 설정할 수 있습니다. 또는 map 컨트롤의 함수를 사용 하 여 스타일을 설정할 수 있습니다 `setStyle` . 이 문서에서는 이러한 스타일 옵션을 사용 하 여 지도의 모양을 사용자 지정 하는 방법을 보여 줍니다. 또한 맵에서 스타일 선택 컨트롤을 구현 하는 방법을 배웁니다. 스타일 선택 컨트롤을 사용 하면 사용자가 다른 기본 스타일 사이를 전환할 수 있습니다.

## <a name="set-map-style-options"></a>지도 스타일 옵션 설정

웹 컨트롤 초기화 중 스타일 옵션을 설정할 수 있습니다. 또는 맵 컨트롤의 함수를 호출 하 여 스타일 옵션을 업데이트할 수 있습니다 `setStyle` . 사용 가능한 모든 스타일 옵션을 보려면 [스타일 옵션](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.styleoptions)을 참조 하세요.

```javascript
//Set the style options when creating the map.
var map = new atlas.Map('map', {
    renderWorldCopies: false,
    showBuildingModels: false,
    showLogo = true,
    showFeedbackLink = true,
    style='road'

    //Additional map options.
};

//Update the style options at anytime using `setStyle` function.
map.setStyle({
    renderWorldCopies: true,
    showBuildingModels: true,
    showLogo = false,
    showFeedbackLink = false
});
```

다음 도구는 다른 스타일 옵션을 통해 지도의 렌더링 방식을 변경 하는 방법을 보여 줍니다. 3D 건물을 보려면 주 도시 가까이를 확대 합니다.

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="지도 스타일 옵션" src="https://codepen.io/azuremaps/embed/eYNMjPb?height=700&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
CodePen의 Azure Maps ()로 펜 <a href='https://codepen.io/azuremaps/pen/eYNMjPb'>지도 스타일 옵션</a> 을 확인 하세요 <a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="set-a-base-map-style"></a>기본 지도 스타일 설정

웹 SDK에서 사용할 수 있는 [기본 지도 스타일](supported-map-styles.md) 중 하나로 맵 컨트롤을 초기화할 수도 있습니다. 그런 다음 함수를 사용 `setStyle` 하 여 기본 스타일을 다른 지도 스타일로 업데이트할 수 있습니다.

### <a name="set-a-base-map-style-on-initialization"></a>초기화 시 기본 지도 스타일 설정

지도 컨트롤의 기본 스타일을 초기화 하는 동안 설정할 수 있습니다. 다음 코드에서 `style` 지도 컨트롤의 옵션은 [ `grayscale_dark` 기본 지도 스타일](supported-map-styles.md#grayscale_dark)로 설정 됩니다.  

```javascript
var map = new atlas.Map('map', {
    style: 'grayscale_dark',

    //Additional map options
);
```

<br/>

<iframe height='500' scrolling='no' title='지도 로드 시 스타일 설정' src='//codepen.io/azuremaps/embed/WKOQRq/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)로 펜 <a href='https://codepen.io/azuremaps/pen/WKOQRq/'>지도 로드 시 스타일 설정</a>을 참조하세요.
</iframe>

### <a name="update-the-base-map-style"></a>기본 지도 스타일 업데이트

함수를 사용 하 여 기본 지도 스타일을 업데이트 하 `setStyle` 고 옵션을 `style` 다른 기본 지도 스타일로 변경 하거나 다른 스타일 옵션을 추가할 수 있습니다.

```javascript
map.setStyle({ style: 'satellite' });
```

다음 코드에서 map 인스턴스가 로드 된 후 `grayscale_dark` `satellite` [system.windows.forms.control.setstyle](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-maps-typescript-latest#setstyle-styleoptions-) 함수를 사용 하 여 지도 스타일이에서로 업데이트 됩니다.

<br/>

<iframe height='500' scrolling='no' title='스타일 업데이트' src='//codepen.io/azuremaps/embed/yqXYzY/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)로 펜 <a href='https://codepen.io/azuremaps/pen/yqXYzY/'>스타일 업데이트</a>를 참조하세요.
</iframe>

## <a name="add-the-style-picker-control"></a>스타일 선택 컨트롤 추가

스타일 선택 컨트롤은 최종 사용자가 기본 스타일을 전환 하는 데 사용할 수 있는 플라이 아웃 패널의 사용 하기 쉬운 단추를 제공 합니다.

스타일 선택기에는 두 가지 레이아웃 옵션인 `icon` 및가 `list` 있습니다. 또한 스타일 선택기를 사용 하 여 두 가지 스타일 선택 컨트롤 `style` 옵션인 및를 선택할 `light` 수 `dark` 있습니다. 이 예제에서 스타일 선택은 레이아웃을 사용 하 `icon` 고 기본 지도 스타일의 select 목록을 아이콘 형식으로 표시 합니다. 스타일 제어 선택기에는 스타일의 기본 집합이 포함 되어 있습니다 `["road", "grayscale_light", "grayscale_dark", "night", "road_shaded_relief"]` . 스타일 선택 컨트롤 옵션에 대 한 자세한 내용은 [스타일 컨트롤 옵션](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.stylecontroloptions)을 참조 하세요.

아래 이미지는 레이아웃에 표시 되는 스타일 선택기 컨트롤을 보여 줍니다 `icon` .

:::image type="content" source="./media/choose-map-style/style-picker-icon-layout.png" alt-text="스타일 선택 목록 레이아웃":::

아래 이미지는 레이아웃에 표시 되는 스타일 선택기 컨트롤을 보여 줍니다 `list` .

:::image type="content" source="./media/choose-map-style/style-picker-list-layout.png" alt-text="스타일 선택 목록 레이아웃":::

> [!IMPORTANT]
>기본적으로 스타일 선택 컨트롤은 Azure Maps의 S0 가격 책정 계층에서 사용할 수 있는 모든 스타일을 나열 합니다. 이 목록에 있는 스타일의 수를 줄이려면 목록에 표시할 스타일의 배열을 `mapStyle` 스타일 선택기의 옵션에 전달 합니다. S1을 사용 중이 고 사용 가능한 모든 스타일을 표시 하려는 경우 `mapStyles` 스타일 선택기의 옵션을로 설정 `"all"` 합니다.

다음 코드에서는 기본 스타일 목록을 재정의 하는 방법을 보여 줍니다 `mapStyles` . 이 예에서는 옵션을 설정 하 여 `mapStyles` 스타일 선택 컨트롤에서 표시 하려는 기본 스타일을 나열 합니다.

<br/>

<iframe height='500' scrolling='no' title='스타일 선택 추가' src='//codepen.io/azuremaps/embed/OwgyvG/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)로 펜 <a href='https://codepen.io/azuremaps/pen/OwgyvG/'>스타일 선택 추가</a>를 참조하세요.
</iframe>

## <a name="next-steps"></a>다음 단계

이 문서에서 사용된 클래스 및 메서드에 대해 자세히 알아려보면 다음 항목을 참조하세요.

> [!div class="nextstepaction"]
> [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [StyleOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.styleoptions)

> [!div class="nextstepaction"]
> [StyleControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.stylecontrol)

> [!div class="nextstepaction"]
> [StyleControlOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.stylecontroloptions)

지도에 컨트롤을 추가 합니다.

> [!div class="nextstepaction"]
> [맵 컨트롤 추가](map-add-controls.md)

> [!div class="nextstepaction"]
> [핀 추가](map-add-pin.md)
