---
title: Azure Maps 웹 SDK의 이미지 템플릿 | Microsoft Azure 맵
description: Azure Maps 웹 SDK를 사용 하 여 지도에 이미지 아이콘 및 패턴 채워진 다각형을 추가 하는 방법에 대해 알아봅니다. 사용 가능한 이미지 및 채우기 패턴 템플릿을 봅니다.
author: rbrundritt
ms.author: richbrun
ms.date: 8/6/2019
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 5f455a1132a0f63e1ba3eb5d2a57a1f9bfa9a867
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/28/2020
ms.locfileid: "92895684"
---
# <a name="how-to-use-image-templates"></a>이미지 템플릿을 사용하는 방법

Azure Maps 웹 SDK 내에서 HTML 표식 및 다양 한 계층에 이미지를 사용할 수 있습니다.

 - 기호 레이어는 이미지 아이콘이 있는 지도에서 요소를 렌더링할 수 있습니다. 줄 경로를 따라 기호를 렌더링할 수도 있습니다.
 - 다각형 계층은 채우기 패턴 이미지를 사용 하 여 렌더링할 수 있습니다. 
 - HTML 표식은 이미지 및 기타 HTML 요소를 사용 하 여 요소를 렌더링할 수 있습니다.

레이어를 사용 하 여 성능을 보장 하기 위해 렌더링 하기 전에 이미지를 지도 이미지 스프라이트 리소스에 로드 합니다. 기호 계층의 [Iconoptions](/javascript/api/azure-maps-control/atlas.iconoptions)는 기본적으로 몇 가지 색의 표식 이미지를 지도 이미지 스프라이트에 미리 로드 합니다. 이러한 표식 이미지는 SVG 템플릿으로 사용할 수 있습니다. 사용자 지정 배율을 사용 하 여 이미지를 만들거나 고객 기본 및 보조 색으로 사용 될 수 있습니다. 총 42 개의 이미지 템플릿이 제공 됩니다: 27 기호 아이콘 및 15 개의 다각형 채우기 패턴.

이미지 템플릿은 함수를 사용 하 여 지도 이미지 스프라이트 리소스에 추가할 수 있습니다 `map.imageSprite.createFromTemplate` . 이 함수를 사용 하면 최대 5 개의 매개 변수를 전달할 수 있습니다.

```javascript
createFromTemplate(id: string, templateName: string, color?: string, secondaryColor?: string, scale?: number): Promise<void>
```

는 `id` 사용자가 만드는 고유 식별자입니다. 는 `id` 맵 이미지 스프라이트에 추가 될 때 이미지에 할당 됩니다. 레이어에이 식별자를 사용 하 여 렌더링할 이미지 리소스를 지정 합니다. 는 `templateName` 사용할 이미지 템플릿을 지정 합니다. `color`옵션은 이미지의 기본 색을 설정 하 고 `secondaryColor` 옵션은 이미지의 보조 색을 설정 합니다. 옵션은 이미지 `scale` 스프라이트에 이미지를 적용 하기 전에 이미지 템플릿을 확장 합니다. 이미지가 이미지 스프라이트에 적용 되 면 PNG로 변환 됩니다. 선명 하 게 렌더링 하려면 레이어에 크기를 조정 하는 것 보다 이미지를 스프라이트에 추가 하기 전에 이미지 템플릿을 확장 하는 것이 좋습니다.

이 함수는 이미지 스프라이트에 이미지를 비동기적으로 로드 합니다. 따라서이 함수는이 함수가 완료 될 때까지 기다릴 수 있는 약속을 반환 합니다.

다음 코드에서는 기본 제공 템플릿 중 하나에서 이미지를 만들어 기호 계층에서 사용 하는 방법을 보여 줍니다.

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

## <a name="use-an-image-template-with-a-symbol-layer"></a>기호 계층과 함께 이미지 템플릿 사용

이미지 템플릿이 맵 이미지 스프라이트에 로드 되 면의 옵션에서 이미지 리소스 ID를 참조 하 여 기호 계층에서 기호로 렌더링 될 수 있습니다 `image` `iconOptions` .

다음 샘플에서는 `marker-flat` 청록 기본 색과 흰색 보조 색을 사용 하 여 이미지 템플릿을 사용 하 여 기호 계층을 렌더링 합니다. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="기본 제공 아이콘 템플릿이 있는 기호 계층" src="//codepen.io/azuremaps/embed/VoQMPp/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
CodePen의 Azure Maps ()를 사용 하 여 <a href='https://codepen.io/azuremaps/pen/VoQMPp/'>기본 제공 아이콘 템플릿이 있는 펜 기호 계층</a> 을 참조 하세요 <a href='https://codepen.io/azuremaps'>@azuremaps</a> . <a href='https://codepen.io'>CodePen</a>
</iframe>

## <a name="use-an-image-template-along-a-lines-path"></a>선 경로를 따라 이미지 템플릿 사용

이미지 템플릿이 맵 이미지 스프라이트에 로드 되 면 데이터 원본에 LineString를 추가 하 고 옵션을 사용 하 여 기호 계층을 사용 하 `lineSpacing` 고 해당 옵션에서 이미지 리소스의 ID를 참조 하 여 선 경로를 따라 렌더링할 수 있습니다 `image` `iconOptions` . 

다음 샘플에서는 지도의 분홍색 선을 렌더링 하 고 `car` 진한 하늘색 blue 기본 색과 흰색 보조 색을 사용 하는 이미지 템플릿을 사용 하 여 기호 계층을 사용 합니다. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="기본 제공 아이콘 템플릿이 있는 선 계층" src="//codepen.io/azuremaps/embed/KOQvJe/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
CodePen에서 Azure Maps ()를 사용 하 여 <a href='https://codepen.io/azuremaps/pen/KOQvJe/'>기본 제공 아이콘 템플릿이 있는 펜 선 계층</a> 을 참조 하세요 <a href='https://codepen.io/azuremaps'>@azuremaps</a> . <a href='https://codepen.io'>CodePen</a>
</iframe>

> [!TIP]
> 이미지 템플릿이 가리키는 경우 `rotation` 기호와 동일한 방향을 가리키도록 기호 계층의 아이콘 옵션을 90로 설정 합니다.

## <a name="use-an-image-template-with-a-polygon-layer"></a>다각형 계층에 이미지 템플릿 사용

이미지 템플릿이 지도 이미지 스프라이트에 로드 되 면 계층의 옵션에서 이미지 리소스 ID를 참조 하 여 다각형 계층에서 채우기 패턴으로 렌더링할 수 있습니다 `fillPattern` .

다음 샘플에서는 `dot` 빨강 기본 색과 투명 한 보조 색을 사용 하 여 이미지 템플릿을 사용 하 여 다각형 계층을 렌더링 합니다.  

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="기본 제공 아이콘 템플릿으로 다각형 채우기" src="//codepen.io/azuremaps/embed/WVMEmz/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
CodePen의 Azure Maps ()를 사용 하 여 <a href='https://codepen.io/azuremaps/pen/WVMEmz/'>기본 제공 아이콘 템플릿이 있는 펜 채우기 다각형</a> 을 참조 하세요 <a href='https://codepen.io/azuremaps'>@azuremaps</a> . <a href='https://codepen.io'>CodePen</a>
</iframe>

> [!TIP]
> 채우기 패턴의 보조 색을 설정 하면 기본 맵이 여전히 주 패턴을 제공 하는 것을 쉽게 확인할 수 있습니다. 

## <a name="use-an-image-template-with-an-html-marker"></a>HTML 표식과 함께 이미지 템플릿 사용

이미지 템플릿은 함수를 사용 하 여 검색 하 `altas.getImageTemplate` 고 HTML 표식의 콘텐츠로 사용할 수 있습니다. 템플릿은 표식의 옵션으로 전달 된 `htmlContent` 다음 `color` , 및 옵션을 사용 하 여 사용자 지정할 수 있습니다 `secondaryColor` `text` .

다음 샘플에서는 `marker-arrow` 빨강 기본 색, 분홍색 보조 색 및 텍스트 값 "00"을 사용 하 여 템플릿을 사용 합니다.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="기본 제공 아이콘 템플릿이 있는 HTML 표식" src="//codepen.io/azuremaps/embed/EqQvzq/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
CodePen의 Azure Maps ()를 사용 하 여 <a href='https://codepen.io/azuremaps/pen/EqQvzq/'>기본 제공 아이콘 템플릿이 있는 Pen HTML 마커</a> 를 확인 <a href='https://codepen.io/azuremaps'>@azuremaps</a> 합니다. <a href='https://codepen.io'>CodePen</a>
</iframe>


> [!TIP]
> 이미지 템플릿도 지도 외부에서 사용할 수 있습니다. GetImageTemplate 작동는 자리 표시 자가 있는 SVG 문자열을 반환 합니다. `{color}`, `{secondaryColor}`, `{scale}`, `{text}`. 올바른 SVG 문자열을 만들려면 이러한 자리 표시자 값을 바꿉니다. 그런 다음 HTML DOM에 직접 SVG 문자열을 추가 하거나 데이터 URI로 변환 하 여 이미지 태그에 삽입할 수 있습니다. 예를 들면 다음과 같습니다.
> ```JavaScript
> //Retrieve an SVG template and replace the placeholder values.
> var svg = atlas.getImageTemplate('marker').replace(/{color}/, 'red').replace(/{secondaryColor}/, 'white').replace(/{text}/, '').replace(/{scale}/, 1);
>
> //Convert to data URI for use in image tags.
> var dataUri = 'data:image/svg+xml;base64,' + btoa(svg);
> ```

## <a name="create-custom-reusable-templates"></a>재사용 가능한 사용자 지정 템플릿 만들기

응용 프로그램에서 아이콘이 다른 아이콘을 사용 하거나 추가 이미지 템플릿을 추가 하는 모듈을 만드는 경우 Azure Maps 웹 SDK에서 이러한 아이콘을 쉽게 추가 하 고 검색할 수 있습니다. 네임 스페이스에서 다음 정적 함수를 사용 합니다 `atlas` .

| Name | 반환 형식 | 설명 | 
|-|-|-|
| `addImageTemplate(templateName: string, template: string, override: boolean)` | | Atlas 네임 스페이스에 사용자 지정 SVG 이미지 템플릿을 추가 합니다. |
|  `getImageTemplate(templateName: string, scale?: number)`| 문자열 | 이름으로 SVG 템플릿을 검색 합니다. |
| `getAllImageTemplateNames()` | string[] |  이름으로 SVG 템플릿을 검색 합니다. |

SVG 이미지 템플릿은 다음 자리 표시자 값을 지원 합니다.

| 자리 표시자 | Description |
|-|-|
| `{color}` | 기본 색입니다. | 
| `{secondaryColor}` | 보조 색입니다. | 
| `{scale}` | 지도 이미지 스프라이트에 추가 되 면 SVG 이미지가 png 이미지로 변환 됩니다. 이 자리 표시자는 명확 하 게 렌더링 하기 위해 템플릿이 변환 되기 전에 크기를 조정 하는 데 사용할 수 있습니다. | 
| `{text}` | HTML 표식과 함께 사용 될 경우 텍스트를 렌더링할 위치입니다. |

다음 예제에서는 SVG 템플릿을 사용 하 여 Azure Maps web SDK에 다시 사용 가능한 아이콘 템플릿으로 추가 하는 방법을 보여 줍니다. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Atlas 네임 스페이스에 사용자 지정 아이콘 템플릿 추가" src="//codepen.io/azuremaps/embed/NQyvEX/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
CodePen에서 Azure Maps ()를 통해 <a href='https://codepen.io/azuremaps/pen/NQyvEX/'>atlas 네임 스페이스에 펜 사용자 지정 아이콘 템플릿 추가를</a> 참조 하세요 <a href='https://codepen.io/azuremaps'>@azuremaps</a> . <a href='https://codepen.io'>CodePen</a>
</iframe>

## <a name="list-of-image-templates"></a>이미지 템플릿 목록

이 표에는 Azure Maps web SDK 내에서 현재 사용할 수 있는 모든 이미지 템플릿이 나열 되어 있습니다. 템플릿 이름이 각 이미지 위에 있습니다. 기본적으로 기본 색은 파란색이 고 보조 색은 흰색입니다. 흰색 배경에 보조 색을 더 쉽게 볼 수 있도록 다음 이미지는 보조 색을 검정으로 설정 합니다.

**기호 아이콘 템플릿**

:::row:::
   :::column span="":::
      marker
   :::column-end:::
   :::column span="":::
      표식-굵고
   :::column-end:::
   :::column span="":::
      표식-원
   :::column-end:::
   :::column span="":::
      표식-플랫
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![표식 아이콘](./media/image-templates/marker.png)
   :::column-end:::
   :::column span="":::
      ![표식-굵은 아이콘](./media/image-templates/marker-thick.png)
   :::column-end:::
   :::column span="":::
      ![표식-원 아이콘](./media/image-templates/marker-circle.png)
   :::column-end:::
   :::column span="":::
      ![표식-플랫 아이콘](./media/image-templates/marker-flat.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      표식-사각형
   :::column-end:::
   :::column span="":::
      표식-제곱-클러스터
   :::column-end:::
   :::column span="":::
      표식-화살표
   :::column-end:::
   :::column span="":::
      표식-공 핀
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![표식-사각형 아이콘](./media/image-templates/marker-square.png)
   :::column-end:::
   :::column span="":::
      ![표식-사각형-클러스터 아이콘](./media/image-templates/marker-square-cluster.png)
   :::column-end:::
   :::column span="":::
      ![표식-화살표 아이콘](./media/image-templates/marker-arrow.png)
   :::column-end:::
   :::column span="":::
      ![표식-공 핀 아이콘](./media/image-templates/marker-ball-pin.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      표식-둥근 사각형
   :::column-end:::
   :::column span="":::
      표식-둥근 사각형-클러스터
   :::column-end:::
   :::column span="":::
      flag
   :::column-end:::
   :::column span="":::
      플래그-삼각형
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![표식-둥근 사각형 아이콘](./media/image-templates/marker-square-rounded.png)
   :::column-end:::
   :::column span="":::
      ![표식-둥근 모양-클러스터 아이콘](./media/image-templates/marker-square-rounded-cluster.png)
   :::column-end:::
   :::column span="":::
      ![플래그 아이콘](./media/image-templates/flag.png)
   :::column-end:::
   :::column span="":::
      ![플래그-삼각형 아이콘](./media/image-templates/flag-triangle.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      삼각형
   :::column-end:::
   :::column span="":::
      삼각형-굵은
   :::column-end:::
   :::column span="":::
      삼각형-위쪽 화살표
   :::column-end:::
   :::column span="":::
      삼각형-왼쪽 화살표
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![삼각형 아이콘](./media/image-templates/triangle.png)
   :::column-end:::
   :::column span="":::
      ![삼각형-굵은 아이콘](./media/image-templates/triangle-thick.png)
   :::column-end:::
   :::column span="":::
      ![삼각형-위쪽 화살표 아이콘](./media/image-templates/triangle-arrow-up.png)
   :::column-end:::
   :::column span="":::
      ![삼각형-왼쪽 아이콘](./media/image-templates/triangle-arrow-left.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      육각형
   :::column-end:::
   :::column span="":::
      육각형-굵고
   :::column-end:::
   :::column span="":::
      육각형-둥근 모양
   :::column-end:::
   :::column span="":::
      육각형-굵고
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![육각형 아이콘](./media/image-templates/hexagon.png)
   :::column-end:::
   :::column span="":::
      ![육각형-굵은 아이콘](./media/image-templates/hexagon-thick.png)
   :::column-end:::
   :::column span="":::
      ![육각형 모양 아이콘](./media/image-templates/hexagon-rounded.png)
   :::column-end:::
   :::column span="":::
      ![육각형-굵은 아이콘](./media/image-templates/hexagon-rounded-thick.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      고정
   :::column-end:::
   :::column span="":::
      핀 반올림
   :::column-end:::
   :::column span="":::
      둥근 사각형
   :::column-end:::
   :::column span="":::
      둥근 사각형-굵고
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![고정 아이콘](./media/image-templates/pin.png)
   :::column-end:::
   :::column span="":::
      ![핀 둥근 모양 아이콘](./media/image-templates/pin-round.png)
   :::column-end:::
   :::column span="":::
      ![둥근 사각형 아이콘](./media/image-templates/rounded-square.png)
   :::column-end:::
   :::column span="":::
      ![둥근 사각형-굵은 아이콘](./media/image-templates/rounded-square-thick.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      위쪽 화살표
   :::column-end:::
   :::column span="":::
      화살표-위쪽-씬
   :::column-end:::
   :::column span="":::
      car
   :::column-end:::
   :::column span="":::
      &nbsp;
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![화살표 아이콘](./media/image-templates/arrow-up.png)
   :::column-end:::
   :::column span="":::
      ![화살표-위쪽 화살표 아이콘](./media/image-templates/arrow-up-thin.png)
   :::column-end:::
   :::column span="":::
      ![자동차 아이콘](./media/image-templates/car.png)
   :::column-end:::
   :::column span="":::
      &nbsp;
   :::column-end:::
:::row-end:::


**다각형 채우기 패턴 템플릿**

:::row:::
   :::column span="":::
      맞춤법
   :::column-end:::
   :::column span="":::
      검사기 회전
   :::column-end:::
   :::column span="":::
      원
   :::column-end:::
   :::column span="":::
      원-간격
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![검사기 아이콘](./media/image-templates/checker.png)
   :::column-end:::
   :::column span="":::
      ![검사기 회전 아이콘](./media/image-templates/checker-rotated.png)
   :::column-end:::
   :::column span="":::
      ![원 아이콘](./media/image-templates/circles.png)
   :::column-end:::
   :::column span="":::
      ![원-간격 아이콘](./media/image-templates/circles-spaced.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      대각선-위쪽
   :::column-end:::
   :::column span="":::
      대각선 줄 다운
   :::column-end:::
   :::column span="":::
      대각선-줄무늬-위쪽
   :::column-end:::
   :::column span="":::
      대각선-아래쪽 줄무늬
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![대각선 줄 위로 아이콘](./media/image-templates/diagonal-lines-up.png)
   :::column-end:::
   :::column span="":::
      ![대각선 줄 다운 아이콘](./media/image-templates/diagonal-lines-down.png)
   :::column-end:::
   :::column span="":::
      ![대각선-줄무늬-위쪽 아이콘](./media/image-templates/diagonal-stripes-up.png)
   :::column-end:::
   :::column span="":::
      ![대각선-아래쪽 줄무늬 아이콘](./media/image-templates/diagonal-stripes-down.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      그리드-줄
   :::column-end:::
   :::column span="":::
      회전 된 모눈선
   :::column-end:::
   :::column span="":::
      회전-표-줄무늬
   :::column-end:::
   :::column span="":::
      x-채우기
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![그리드-선 아이콘](./media/image-templates/grid-lines.png)
   :::column-end:::
   :::column span="":::
      ![회전-눈금선 아이콘](./media/image-templates/rotated-grid-lines.png)
   :::column-end:::
   :::column span="":::
      ![회전-표-줄무늬 아이콘](./media/image-templates/rotated-grid-stripes.png)
   :::column-end:::
   :::column span="":::
      ![x-채우기 아이콘](./media/image-templates/x-fill.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      지그재그
   :::column-end:::
   :::column span="":::
      지그재그-세로
   :::column-end:::
   :::column span="":::
      점에
   :::column-end:::
   :::column span="":::
      &nbsp;
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![지그재그 아이콘](./media/image-templates/zig-zag.png)
   :::column-end:::
   :::column span="":::
      ![지그재그-세로 아이콘](./media/image-templates/zig-zag-vertical.png)
   :::column-end:::
   :::column span="":::
      ![점 아이콘](./media/image-templates/dots.png)
   :::column-end:::
   :::column span="":::
      &nbsp;
   :::column-end:::
:::row-end:::
<br>

**미리 로드 한 이미지 아이콘**

맵은 `marker` , 및 템플릿을 사용 하 여 아이콘 집합을 지도 이미지 스프라이트에 미리 로드 합니다 `pin` `pin-round` . 이러한 아이콘 이름 및 해당 색 값은 아래 표에 나와 있습니다.

| 아이콘 이름 | 색 | secondaryColor |
|-----------|-------|----------------|
| `marker-black` | `#231f20` | `#ffffff` |
| `marker-blue` | `#1a73aa` | `#ffffff` |
| `marker-darkblue` | `#003963` | `#ffffff` |
| `marker-red` | `#ef4c4c` | `#ffffff` |
| `marker-yellow` | `#f2c851` | `#ffffff` |
| `pin-blue` | `#2072b8` | `#ffffff` |
| `pin-darkblue` | `#003963` | `#ffffff` |
| `pin-red` | `#ef4c4c` | `#ffffff` |
| `pin-round-blue` | `#2072b8` | `#ffffff` |
| `pin-round-darkblue` | `#003963` | `#ffffff` |
| `pin-round-red` | `#ef4c4c` | `#ffffff` |


## <a name="try-it-now-tool"></a>지금 체험 도구

다음 도구를 사용 하 여 다양 한 방법으로 다양 한 기본 제공 이미지 템플릿을 렌더링 하 고 기본 및 보조 색상 및 크기를 사용자 지정할 수 있습니다.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="아이콘 템플릿 옵션" src="//codepen.io/azuremaps/embed/NQyaaO/?height=500&theme-id=0&default-tab=result" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
CodePen의 Azure Maps ()에서 펜 <a href='https://codepen.io/azuremaps/pen/NQyaaO/'>아이콘 템플릿 옵션</a> 을 참조 하세요 <a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>다음 단계

이 문서에서 사용된 클래스 및 메서드에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [ImageSpriteManager](/javascript/api/azure-maps-control/atlas.imagespritemanager)

> [!div class="nextstepaction"]
> [atlas 네임 스페이스](/javascript/api/azure-maps-control/atlas#functions
)

이미지 템플릿을 사용할 수 있는 더 많은 코드 샘플은 다음 문서를 참조 하세요.

> [!div class="nextstepaction"]
> [기호 계층 추가](map-add-pin.md)

> [!div class="nextstepaction"]
> [선 계층 추가](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [다각형 계층 추가](map-add-shape.md)

> [!div class="nextstepaction"]
> [HTML 표식 추가](map-add-bubble-layer.md)