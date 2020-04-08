---
title: Azure 지도 웹 SDK의 이미지 템플릿 | 마이크로소프트 Azure 지도
description: 이 문서에서는 Microsoft Azure Maps Web SDK에서 HTML 마커 및 다양한 계층이 있는 이미지 템플릿을 사용하는 방법을 알아봅니다.
author: rbrundritt
ms.author: richbrun
ms.date: 8/6/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.custom: codepen
ms.openlocfilehash: ee8e8ee4ca64de0390b6fa34e36fb4d06348a8ac
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804812"
---
# <a name="how-to-use-image-templates"></a>이미지 템플릿을 사용하는 방법

이미지는 Azure Maps 웹 SDK 내의 HTML 마커 및 다양한 레이어와 함께 사용할 수 있습니다.

 - 심볼 레이어는 이미지 아이콘으로 맵의 포인트를 렌더링할 수 있습니다. 기호는 선 경로를 따라 렌더링할 수도 있습니다.
 - 다각형 레이어는 채우기 패턴 이미지로 렌더링할 수 있습니다. 
 - HTML 마커는 이미지 및 기타 HTML 요소를 사용하여 점을 렌더링할 수 있습니다.

레이어의 성능을 높이기 위해 렌더링하기 전에 이미지를 맵 이미지 스프라이트 리소스에 로드합니다. [SymbolLayer의 IconOptions은](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions)기본적으로 소수의 색상으로 마커 이미지 몇 개를 맵 이미지 스프라이트에 미리 로드합니다. 이러한 마커 이미지 및 더 많은 SVG 템플릿으로 사용할 수 있습니다. 사용자 지정 축척으로 이미지를 만들거나 고객 기본 및 보조 색상으로 사용할 수 있습니다. 총 42 개의 이미지 템플릿이 제공됩니다 : 27 기호 아이콘과 15 개의 다각형 채우기 패턴.

이미지 템플릿은 기능을 사용하여 맵 이미지 스프라이트 `map.imageSprite.createFromTemplate` 리소스에 추가할 수 있습니다. 이 함수를 사용하면 최대 5개의 매개 변수를 전달할 수 있습니다.

```javascript
createFromTemplate(id: string, templateName: string, color?: string, secondaryColor?: string, scale?: number): Promise<void>
```

는 `id` 만드는 고유 식별자입니다. 이 `id` 맵 이미지 스프라이트에 추가될 때 이미지에 할당됩니다. 레이어에서 이 식별자를 사용하여 렌더링할 이미지 리소스를 지정합니다. 사용할 `templateName` 이미지 템플릿을 지정합니다. 이 `color` 옵션은 이미지의 기본 색상을 `secondaryColor` 설정하고 옵션은 이미지의 보조 색상을 설정합니다. 이 `scale` 옵션은 이미지 스프라이트에 적용하기 전에 이미지 템플릿의 배율을 조정합니다. 이미지가 이미지 스프라이트에 적용되면 PNG로 변환됩니다. 선명한 렌더링을 보장하려면 레이어에서 크기를 조정하는 것보다 스프라이트에 추가하기 전에 이미지 템플릿을 확장하는 것이 좋습니다.

이 함수는 비동기적으로 이미지를 이미지 스프라이트에 로드합니다. 따라서 이 함수가 완료될 때까지 기다릴 수 있는 Promise를 반환합니다.

다음 코드는 기본 제공 템플릿 중 하나에서 이미지를 만들고 기호 레이어와 함께 사용하는 방법을 보여 주었습니다.

```javascript
map.imageSprite.createFromTemplate('myTemplatedIcon', 'marker-flat', 'teal', '#fff').then(function () {

    //Add a symbol layer that uses the custom created icon.
    map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
        iconOptions: {
            image: 'myTemplatedIcon'
        }
    }));
});
```

## <a name="use-an-image-template-with-a-symbol-layer"></a>심볼 레이어가 있는 이미지 템플릿 사용

이미지 템플릿이 맵 이미지 스프라이트에 로드되면 `image` `iconOptions`의 옵션에서 이미지 리소스 ID를 참조하여 기호 레이어의 심볼로 렌더링할 수 있습니다.

다음 샘플에서는 청록색 기본 `marker-flat` 색상과 흰색 보조 색상이 있는 이미지 템플릿을 사용하여 기호 레이어를 렌더링합니다. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="기본 제공 아이콘 템플릿이 있는 기호 레이어" src="//codepen.io/azuremaps/embed/VoQMPp/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen에서</a>Azure Maps ()에<a href='https://codepen.io/azuremaps'>@azuremaps</a>의해 <a href='https://codepen.io/azuremaps/pen/VoQMPp/'>기본 제공 아이콘 템플릿이 있는</a> 펜 기호 레이어를 참조하십시오.
</iframe>

## <a name="use-an-image-template-along-a-lines-path"></a>선 경로를 따라 이미지 템플릿 사용

이미지 템플릿이 맵 이미지 스프라이트에 로드되면 데이터 원본에 LineString을 추가하고 `lineSpacing`옵션이 있는 기호 레이어를 사용하고 다음 `image` 옵션에서 이미지 리소스의 ID를 참조하여 줄의 경로를 `iconOptions`따라 렌더링할 수 있습니다. 

다음 샘플에서는 맵에서 분홍색 선을 렌더링하고 다저 `car` 블루 기본 색상과 흰색 보조 색상이 있는 이미지 템플릿을 사용하여 기호 레이어를 사용합니다. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="기본 제공 아이콘 템플릿이 있는 선 레이어" src="//codepen.io/azuremaps/embed/KOQvJe/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen에서</a>Azure Maps ()에<a href='https://codepen.io/azuremaps'>@azuremaps</a>의해 <a href='https://codepen.io/azuremaps/pen/KOQvJe/'>기본 제공 아이콘 템플릿이 있는</a> 펜 선 레이어를 참조하십시오.
</iframe>

> [!TIP]
> 이미지 템플릿이 가리키는 경우 `rotation` 기호 레이어의 아이콘 옵션을 선과 동일한 방향으로 가리키도록 하려면 90으로 설정합니다.

## <a name="use-an-image-template-with-a-polygon-layer"></a>다각형 레이어가 있는 이미지 템플릿 사용

이미지 템플릿이 맵 이미지 스프라이트에 로드되면 레이어 `fillPattern` 옵션에서 이미지 리소스 ID를 참조하여 다각형 레이어의 채우기 패턴으로 렌더링할 수 있습니다.

다음 샘플에서는 빨간색 기본 색상과 `dot` 투명한 보조 색상이 있는 이미지 템플릿을 사용하여 다각형 레이어를 렌더링합니다.  

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="내장 된 아이콘 템플릿으로 다각형을 채우기" src="//codepen.io/azuremaps/embed/WVMEmz/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen에서</a>Azure Maps ()에<a href='https://codepen.io/azuremaps'>@azuremaps</a>의해 기본 제공 아이콘 템플릿이 있는 펜 <a href='https://codepen.io/azuremaps/pen/WVMEmz/'>채우기 다각형을</a> 참조하십시오.
</iframe>

> [!TIP]
> 채우기 패턴의 보조 색상을 설정하면 기본 맵이 여전히 기본 패턴을 제공하는 것을 더 쉽게 볼 수 있습니다. 

## <a name="use-an-image-template-with-an-html-marker"></a>HTML 마커가 있는 이미지 템플릿 사용

이미지 템플릿은 함수를 `altas.getImageTemplate` 사용하여 검색하여 HTML 마커의 콘텐츠로 사용할 수 있습니다. 템플릿을 `htmlContent` 마커 옵션으로 전달한 다음 `color`에서 를 `secondaryColor`사용하여 사용자 `text` 지정할 수 있습니다.

다음 샘플에서는 `marker-arrow` 빨간색 기본 색상, 분홍색 보조 색상 및 텍스트 값이 "00"인 템플릿을 사용합니다.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="기본 제공 아이콘 템플릿이 있는 HTML 마커" src="//codepen.io/azuremaps/embed/EqQvzq/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen에서</a>Azure Maps ()에<a href='https://codepen.io/azuremaps'>@azuremaps</a>의해 <a href='https://codepen.io/azuremaps/pen/EqQvzq/'>기본 제공 아이콘 템플릿이 있는</a> 펜 HTML 마커를 참조하십시오.
</iframe>

## <a name="create-custom-reusable-templates"></a>사용자 지정 재사용 가능한 템플릿 만들기

응용 프로그램에서 다른 아이콘과 동일한 아이콘을 사용하거나 이미지 템플릿을 추가하는 모듈을 만드는 경우 Azure Maps 웹 SDK에서 이러한 아이콘을 쉽게 추가하고 검색할 수 있습니다. 네임스페이스에서 다음과 같은 `atlas` 정적 함수를 사용합니다.

| 이름 | 반환 형식 | Description | 
|-|-|-|
| `addImageTemplate(templateName: string, template: string, override: boolean)` | | 아틀라스 네임스페이스에 사용자 지정 SVG 이미지 템플릿을 추가합니다. |
|  `getImageTemplate(templateName: string, scale?: number)`| 문자열 | 이름으로 SVG 템플릿을 검색합니다. |
| `getAllImageTemplateNames()` | string[] |  이름으로 SVG 템플릿을 검색합니다. |

SVG 이미지 템플릿은 다음 자리 표시자 값을 지원합니다.

| 자리 표시자 | Description |
|-|-|
| `{color}` | 기본 색상입니다. | 
| `{secondaryColor}` | 보조 색상입니다. | 
| `{scale}` | SVG 이미지는 맵 이미지 스프라이트에 추가되면 png 이미지로 변환됩니다. 이 자리 표시자는 명확하게 렌더링되도록 변환되기 전에 템플릿의 크기를 조정하는 데 사용할 수 있습니다. | 
| `{text}` | HTML 마커와 함께 사용할 때 텍스트를 렌더링할 위치입니다. |

다음 예제에서는 SVG 템플릿을 가져 와서 Azure Maps 웹 SDK에 재사용 가능한 아이콘 템플릿으로 추가하는 방법을 보여 주며 있습니다. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="아틀라스 네임스페이스에 사용자 지정 아이콘 템플릿 추가" src="//codepen.io/azuremaps/embed/NQyvEX/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen에서</a>Azure Maps ()별로<a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io/azuremaps/pen/NQyvEX/'>아틀라스 네임스페이스에 사용자 지정 아이콘 템플릿 추가</a> 를 참조하십시오.
</iframe>

## <a name="list-of-image-templates"></a>이미지 템플릿 목록

이 표에는 Azure Maps 웹 SDK 내에서 현재 사용할 수 있는 모든 이미지 템플릿이 나열되어 있습니다. 템플릿 이름은 각 이미지 위에 있습니다. 기본적으로 기본 색상은 파란색이고 보조 색상은 흰색입니다. 흰색 배경에서 보조 색상을 더 쉽게 볼 수 있도록 하려면 다음 이미지에 보조 색상이 검은색으로 설정되어 있습니다.

**기호 아이콘 템플릿**

|||||
|:-:|:-:|:-:|:-:|
| marker | 마커 두께 | 마커 원 | 마커 플랫 |
|![마커 아이콘](./media/image-templates/marker.png)|![마커 두께 아이콘](./media/image-templates/marker-thick.png)|![마커 원 아이콘](./media/image-templates/marker-circle.png)|![마커 플랫 아이콘](./media/image-templates/marker-flat.png)|
||||
| 마커 사각형 | 마커 사각형 클러스터 | 마커 화살표 | 마커 볼 핀 | 
|![마커 정사각형 아이콘](./media/image-templates/marker-square.png)|![마커 사각형 클러스터 아이콘](./media/image-templates/marker-square-cluster.png)|![마커 화살표 아이콘](./media/image-templates/marker-arrow.png)|![마커 볼 핀 아이콘](./media/image-templates/marker-ball-pin.png)|
||||
| 마커 사각형 둥근 | 마커 사각형 반올림 클러스터 | 플래그 | 플래그 삼각형 |
| ![마커 사각형 둥근 아이콘](./media/image-templates/marker-square-rounded.png) | ![마커 사각형 둥근 클러스터 아이콘](./media/image-templates/marker-square-rounded-cluster.png) | ![플래그 아이콘](./media/image-templates/flag.png) | ![플래그-삼각형 아이콘](./media/image-templates/flag-triangle.png) |
||||
| 삼각형 | 삼각형 두께 | 삼각형 화살표 업 | 삼각형 화살표 왼쪽 |
| ![삼각형 아이콘](./media/image-templates/triangle.png) | ![삼각형 두께 아이콘](./media/image-templates/triangle-thick.png) | ![삼각형 화살표 업 아이콘](./media/image-templates/triangle-arrow-up.png) | ![삼각형 화살표 왼쪽 아이콘](./media/image-templates/triangle-arrow-left.png) |
||||
| 육각 | 육각형 두께 | 육각형 라운드 | 육각형 원형 두께 |
| ![육각형 아이콘](./media/image-templates/hexagon.png) | ![육각형 두꺼운 아이콘](./media/image-templates/hexagon-thick.png) | ![육각형 원형 아이콘](./media/image-templates/hexagon-rounded.png) | ![육각형 원형 두꺼운 아이콘](./media/image-templates/hexagon-rounded-thick.png) |
||||
| 고정 | 핀 라운드 | 둥근 사각형 | 둥근 사각형 두께 |
| ![고정 아이콘](./media/image-templates/pin.png) | ![핀 라운드 아이콘](./media/image-templates/pin-round.png) | ![둥근 사각형 아이콘](./media/image-templates/rounded-square.png) | ![둥근 사각형 두께 아이콘](./media/image-templates/rounded-square-thick.png) |
||||
| 화살표 업 | 화살표 업 얇은 | 차량 ||
| ![화살표 업 아이콘](./media/image-templates/arrow-up.png) | ![화살표 업 얇은 아이콘](./media/image-templates/arrow-up-thin.png) | ![자동차 아이콘](./media/image-templates/car.png) | |

**다각형 채우기 패턴 템플릿**

|||||
|:-:|:-:|:-:|:-:|
| 검사기 | 검사기 회전 | 원 | 원 간격 |
| ![검사기 아이콘](./media/image-templates/checker.png) | ![검사기 회전 아이콘](./media/image-templates/checker-rotated.png) | ![원 아이콘](./media/image-templates/circles.png) | ![원 간격 아이콘](./media/image-templates/circles-spaced.png) |
|||||
| 대각선 라인 업 | 대각선 아래로 | 대각선 스트라이프 업 | 대각선 줄무늬 다운 |
| ![대각선 선 업 아이콘](./media/image-templates/diagonal-lines-up.png) | ![대각선 아래로 아이콘](./media/image-templates/diagonal-lines-down.png) | ![대각선 줄무늬 업 아이콘](./media/image-templates/diagonal-stripes-up.png) | ![대각선 줄무늬 아래로 아이콘](./media/image-templates/diagonal-stripes-down.png) |
|||||
| 그리드 라인 | 회전 그리드 선 | 회전 그리드 스트라이프 | x-채우기 |
| ![그리드 선 아이콘](./media/image-templates/grid-lines.png) | ![회전그리드선 아이콘](./media/image-templates/rotated-grid-lines.png) | ![회전 그리드 스트라이프 아이콘](./media/image-templates/rotated-grid-stripes.png) | ![x-채우기 아이콘](./media/image-templates/x-fill.png) |
|||||
| 지그재그 | 지그재그 수직 | 점 |  |
| ![지그재그 아이콘](./media/image-templates/zig-zag.png) | ![지그재그 수직 아이콘](./media/image-templates/zig-zag-vertical.png) | ![점 아이콘](./media/image-templates/dots.png) | |

## <a name="try-it-now-tool"></a>지금 시도 도구

다음 도구를 사용하면 다양한 기본 제공 이미지 템플릿을 다양한 방법으로 렌더링하고 기본 및 보조 색상 및 배율을 사용자 지정할 수 있습니다.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="아이콘 템플릿 옵션" src="//codepen.io/azuremaps/embed/NQyaaO/?height=500&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen에서</a>Azure 지도 ()<a href='https://codepen.io/azuremaps'>@azuremaps</a>별 펜 <a href='https://codepen.io/azuremaps/pen/NQyaaO/'>아이콘 템플릿 옵션을</a> 참조하십시오.
</iframe>

## <a name="next-steps"></a>다음 단계

이 문서에서 사용된 클래스 및 메서드에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [이미지스프라이트매니저](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.imagespritemanager)

> [!div class="nextstepaction"]
> [아틀라스 네임스페이스](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas?view=azure-maps-typescript-latest#functions
)

이미지 템플릿을 사용할 수 있는 자세한 코드 샘플은 다음 문서를 참조하십시오.

> [!div class="nextstepaction"]
> [기호 레이어 추가](map-add-pin.md)

> [!div class="nextstepaction"]
> [선 계층 추가](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [다각형 계층 추가](map-add-shape.md)

> [!div class="nextstepaction"]
> [HTML 표식 추가](map-add-bubble-layer.md)
