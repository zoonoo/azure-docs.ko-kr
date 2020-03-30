---
title: Azure 지도에서 맵 스타일 변경 | 마이크로소프트 Azure 지도
description: 이 문서에서는 Microsoft Azure Maps 웹 SDK에서 사용할 수 있는 스타일 관련 기능에 대해 알아봅니다.
author: philmea
ms.author: philmea
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: b1c5d9b5cd2b6b9bfecf8a0af79699061003eec1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335679"
---
# <a name="change-the-style-of-the-map"></a>맵 스타일 변경

맵은 맵이 [style options](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.styleoptions) 초기화되거나 나중에 맵 `setStyle` 함수를 사용하여 설정할 수 있는 여러 가지 스타일 옵션을 지원합니다. 이 문서에서는 이러한 스타일 옵션을 사용하여 맵 모양을 사용자 지정하는 방법을 보여 주며 있습니다. 맵을 로드할 때 스타일을 설정하고 스타일 선택기 컨트롤을 사용하여 새 맵 스타일을 설정하는 방법을 배웁니다.

## <a name="set-the-style-options"></a>스타일 옵션 설정 

스타일 옵션은 맵 함수를 사용하여 나중에 초기화되거나 업데이트될 때 맵에 `setStyle` 전달될 수 있습니다.

```javascript
//Set the style options when creating the map.
var map = new atlas.Map('map', {
    renderWorldCopies: false,
    showBuildingModels: true

    //Additional map options.
};

//Update the style options at anytime using setStyle function.
map.setStyle({
    renderWorldCopies: true,
    showBuildingModels: false
});
```

다음 도구는 다양한 스타일 옵션이 맵 렌더링 방법을 변경하는 방법을 보여 주어집니다. 3D 건물을 보려면 주요 도시에 가깝게 확대합니다. 

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="맵 스타일 옵션" src="https://codepen.io/azuremaps/embed/eYNMjPb?height=700&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen에서</a>Azure <a href='https://codepen.io/azuremaps/pen/eYNMjPb'>지도</a> ()<a href='https://codepen.io/azuremaps'>@azuremaps</a>별 펜 맵 스타일 옵션을 참조하십시오.
</iframe>

## <a name="choose-a-base-map-style"></a>기본 맵 스타일 선택

가장 일반적인 맵 스타일 옵션 중 하나는 스타일이 있는 기본 맵의 스타일을 변경하는 데 사용됩니다. [Azure 지도에서 지원되는](supported-map-styles.md) 많은 맵 스타일은 웹 SDK에서 사용할 수 있습니다. 

### <a name="set-base-map-style-on-map-load"></a>맵 하중에 기본 맵 스타일 설정


옵션을 설정하여 맵을 초기화할 때 `style` 맵 스타일을 지정할 수 있습니다. 다음 코드에서는 맵 `style` 옵션이 초기화시로 `grayscale_dark` 설정됩니다.

```javascript
var map = new atlas.Map('map', {
    style: 'grayscale_dark',

    //Additiona map options
);
```

<br/>

<iframe height='500' scrolling='no' title='지도 로드 시 스타일 설정' src='//codepen.io/azuremaps/embed/WKOQRq/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)로 펜 <a href='https://codepen.io/azuremaps/pen/WKOQRq/'>지도 로드 시 스타일 설정</a>을 참조하세요.
</iframe>

### <a name="update-the-base-map-style"></a>기본 맵 스타일 업데이트

 맵 스타일은 함수를 `setStyle` 사용하여 옵션을 원하는 `style` 맵 스타일로 설정할 수 있습니다.

```javascript
map.setStyle({ style: 'satellite' });
```

다음 코드에서는 맵 인스턴스가 로드된 후 [setStyle](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-maps-typescript-latest#setstyle-styleoptions-) `road` 함수를 `satellite` 사용하여 맵 스타일이 업데이트됩니다.

<br/>

<iframe height='500' scrolling='no' title='스타일 업데이트' src='//codepen.io/azuremaps/embed/yqXYzY/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)로 펜 <a href='https://codepen.io/azuremaps/pen/yqXYzY/'>스타일 업데이트</a>를 참조하세요.
</iframe>

### <a name="add-the-style-picker"></a>스타일 선택 추가

스타일 선택기 컨트롤은 최종 사용자가 맵 스타일을 변경하는 데 사용할 수있는 플라이 아웃 패널로 사용하기 쉬운 버튼을 제공합니다. 스타일 선택기에는 두 가지 레이아웃 옵션이 있습니다. 기본적으로 스타일 선택기는 `icons` 레이아웃을 사용하고 모든 맵 스타일을 아이콘의 가로 행으로 표시합니다. 

<center>

![스타일 선택기 아이콘 레이아웃](media/choose-map-style/style-picker-icon-layout.png)</center>

두 번째 레이아웃 `list` 옵션이 호출되고 스크롤 가능한 맵 스타일 목록을 표시합니다.  

<center>

![스타일 선택기 목록 레이아웃](media/choose-map-style/style-picker-list-layout.png)</center>


다음 코드는 스타일 선택기 컨트롤의 인스턴스를 만들고 맵의 오른쪽 상단 모서리에 추가하는 방법을 보여 주며 있습니다. 스타일 선택기는 어두운 스타일을 가지도록 설정되어 있으며 목록 레이어를 사용하여 선택한 몇 가지 맵 스타일을 표시합니다.

```javascript
map.controls.add(new atlas.control.StyleControl({
    mapStyles: ['road', 'night', 'grayscale_dark', 'grayscale_light'],
    layout: 'list',
    style: 'dark'
}), {
    position: 'top-right'
}); 
```

다음 코드는 기본 설정을 사용하여 스타일 선택기 컨트롤을 맵에 추가하므로 사용자는 다른 맵 스타일 간에 쉽게 전환할 수 있습니다. 오른쪽 상단 모서리 근처의 맵 스타일 컨트롤을 사용하여 맵 스타일을 전환합니다.

<br/>

<iframe height='500' scrolling='no' title='스타일 선택 추가' src='//codepen.io/azuremaps/embed/OwgyvG/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)로 펜 <a href='https://codepen.io/azuremaps/pen/OwgyvG/'>스타일 선택 추가</a>를 참조하세요.
</iframe>

> [!TIP]
> 기본적으로 Azure Maps의 S0 가격 책정 계층을 사용하는 경우 스타일 선택기 컨트롤에는 사용 가능한 모든 스타일이 나열됩니다. 이 목록의 스타일 수를 줄이려면 목록에 표시할 스타일 배열을 스타일 선택기 `mapStyle` 옵션으로 전달합니다. S1을 사용하고 사용 가능한 모든 스타일을 표시하려면 `mapStyles` 스타일 선택기 옵션을 `"all"`로 설정합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서 사용된 클래스 및 메서드에 대해 자세히 알아려보면 다음 항목을 참조하세요.

> [!div class="nextstepaction"]
> [지도](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [스타일 옵션](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.styleoptions)

> [!div class="nextstepaction"]
> [StyleControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.stylecontrol)

> [!div class="nextstepaction"]
> [스타일 컨트롤 옵션](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.stylecontroloptions)

맵에 컨트롤 추가:

> [!div class="nextstepaction"]
> [맵 컨트롤 추가](map-add-controls.md)

> [!div class="nextstepaction"]
> [핀 추가](map-add-pin.md)
