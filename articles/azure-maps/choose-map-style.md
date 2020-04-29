---
title: Azure Maps에서 지도의 스타일 변경 Microsoft Azure 맵
description: 이 문서에서는 Microsoft Azure Maps 웹 SDK에서 제공 되는 스타일 관련 기능에 대해 알아봅니다.
author: philmea
ms.author: philmea
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: b1c5d9b5cd2b6b9bfecf8a0af79699061003eec1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80335679"
---
# <a name="change-the-style-of-the-map"></a>맵 스타일 변경

맵은 map `setStyle` 함수를 사용 하 여 맵을 초기화할 때 설정할 수 있는 여러 가지 [스타일 옵션](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.styleoptions) 을 지원 합니다. 이 문서에서는 이러한 스타일 옵션을 사용 하 여 지도 모양을 사용자 지정 하는 방법을 보여 줍니다. 지도를 로드할 때 스타일을 설정 하는 방법과 스타일 선택 컨트롤을 사용 하 여 새 지도 스타일을 설정 하는 방법을 알아봅니다.

## <a name="set-the-style-options"></a>스타일 옵션 설정 

스타일 옵션은 나중에 맵 `setStyle` 함수를 사용 하 여 초기화 되거나 업데이트 될 때 맵에 전달 될 수 있습니다.

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

다음 도구는 다른 스타일 옵션을 통해 지도의 렌더링 방식을 변경 하는 방법을 보여 줍니다. 3D 건물을 보려면 주 도시 가까이를 확대 합니다. 

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="지도 스타일 옵션" src="https://codepen.io/azuremaps/embed/eYNMjPb?height=700&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a>의 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)로 펜 <a href='https://codepen.io/azuremaps/pen/eYNMjPb'>지도 스타일 옵션</a> 을 확인 하세요.
</iframe>

## <a name="choose-a-base-map-style"></a>기본 지도 스타일 선택

가장 일반적인 지도 스타일 옵션 중 하나는 스타일이 지정 된 기본 지도의 스타일을 변경 하는 데 사용 됩니다. [Azure Maps에서 지원](supported-map-styles.md) 되는 다양 한 지도 스타일은 웹 SDK에서 사용할 수 있습니다. 

### <a name="set-base-map-style-on-map-load"></a>지도 부하의 기본 지도 스타일 설정


옵션을 `style` 설정 하 여 지도를 초기화할 때 지도 스타일을 지정할 수 있습니다. 다음 코드에서 map의 `style` 옵션은 초기화 시로 `grayscale_dark` 설정 됩니다.

```javascript
var map = new atlas.Map('map', {
    style: 'grayscale_dark',

    //Additiona map options
);
```

<br/>

<iframe height='500' scrolling='no' title='지도 로드 시 스타일 설정' src='//codepen.io/azuremaps/embed/WKOQRq/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)로 펜 <a href='https://codepen.io/azuremaps/pen/WKOQRq/'>지도 로드 시 스타일 설정</a>을 참조하세요.
</iframe>

### <a name="update-the-base-map-style"></a>기본 지도 스타일 업데이트

 지도 스타일은 함수를 `setStyle` 사용 하 여 업데이트 하 고 `style` 옵션을 원하는 지도 스타일로 설정할 수 있습니다.

```javascript
map.setStyle({ style: 'satellite' });
```

다음 코드에서 map 인스턴스가 로드 된 후 `road` [system.windows.forms.control.setstyle](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-maps-typescript-latest#setstyle-styleoptions-) 함수를 사용 하 여 지도 스타일이에서로 `satellite` 업데이트 됩니다.

<br/>

<iframe height='500' scrolling='no' title='스타일 업데이트' src='//codepen.io/azuremaps/embed/yqXYzY/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)로 펜 <a href='https://codepen.io/azuremaps/pen/yqXYzY/'>스타일 업데이트</a>를 참조하세요.
</iframe>

### <a name="add-the-style-picker"></a>스타일 선택 추가

스타일 선택 컨트롤은 최종 사용자가 지도 스타일을 변경 하는 데 사용할 수 있는 플라이 아웃 패널의 사용 하기 쉬운 단추를 제공 합니다. 스타일 선택기에는 두 가지 레이아웃 옵션이 있습니다. 기본적으로 스타일 선택기는 레이아웃을 `icons` 사용 하 고 모든 지도 스타일을 아이콘의 가로 행으로 표시 합니다. 

<center>

![스타일 선택 아이콘 레이아웃](media/choose-map-style/style-picker-icon-layout.png)</center>

두 번째 레이아웃 옵션이 호출 `list` 되 고 스크롤 가능한 지도 스타일 목록을 표시 합니다.  

<center>

![스타일 선택 목록 레이아웃](media/choose-map-style/style-picker-list-layout.png)</center>


다음 코드에서는 스타일 선택 컨트롤의 인스턴스를 만들고 지도의 오른쪽 위 모퉁이에 추가 하는 방법을 보여 줍니다. 스타일 선택은 어두운 스타일로 설정 되 고 목록 계층을 사용 하 여 선택한 몇 가지 지도 스타일을 표시 합니다.

```javascript
map.controls.add(new atlas.control.StyleControl({
    mapStyles: ['road', 'night', 'grayscale_dark', 'grayscale_light'],
    layout: 'list',
    style: 'dark'
}), {
    position: 'top-right'
}); 
```

다음 코드는 기본 설정이 포함 된 스타일 선택 컨트롤을 맵에 추가 하 여 사용자가 다양 한 지도 스타일 간을 쉽게 전환할 수 있도록 합니다. 오른쪽 위 모서리 근처에 있는 지도 스타일 컨트롤을 사용 하 여 지도 스타일을 설정/해제 합니다.

<br/>

<iframe height='500' scrolling='no' title='스타일 선택 추가' src='//codepen.io/azuremaps/embed/OwgyvG/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)로 펜 <a href='https://codepen.io/azuremaps/pen/OwgyvG/'>스타일 선택 추가</a>를 참조하세요.
</iframe>

> [!TIP]
> 기본적으로 Azure Maps의 S0 가격 책정 계층을 사용 하는 경우 스타일 선택 컨트롤은 사용 가능한 모든 스타일을 나열 합니다. 이 목록에 있는 스타일의 수를 줄이려면 목록에 표시할 스타일의 배열을 스타일 선택기의 `mapStyle` 옵션에 전달 합니다. S1을 사용 중이 고 사용 가능한 모든 스타일을 표시 하려는 경우 스타일 선택기 `mapStyles` 의 옵션을로 `"all"`설정 합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서 사용된 클래스 및 메서드에 대해 자세히 알아려보면 다음 항목을 참조하세요.

> [!div class="nextstepaction"]
> [매핑할](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map)

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
