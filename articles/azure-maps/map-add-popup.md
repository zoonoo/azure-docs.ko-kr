---
title: 지도의 한 점에 팝업 추가 | 마이크로소프트 Azure 지도
description: 이 문서에서는 Microsoft Azure Maps 웹 SDK를 사용하여 한 지점에 팝업을 추가하는 방법을 알아봅니다.
author: jinzh-azureiot
ms.author: jinzh
ms.date: 02/27/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: cf6424d2a6cbcfb7c5052201b5a9190c81fddaff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80055944"
---
# <a name="add-a-popup-to-the-map"></a>맵에 팝업 추가

이 문서에서는 맵에 있는 지점에 팝업을 추가하는 방법을 보여 줍니다.

## <a name="understand-the-code"></a>코드 이해

다음 코드는 기호 레이어를 `name` 사용하여 `description` 맵에 속성과 점 기능을 추가합니다. [Popup 클래스의](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup) 인스턴스가 만들어지지만 표시되지 는 않습니다. 기호 레이어에 마우스 이벤트가 추가되어 팝업을 열고 닫습니다. 마커 기호를 가리키면 `position` 팝업의 속성이 마커의 위치로 업데이트되고 가리키고 `content` 있는 점 피처의 `description` 속성을 `name` 래핑하는 일부 HTML로 옵션이 업데이트됩니다. 그런 다음 해당 기능을 사용하여 맵에 팝업이 `open` 표시됩니다.

```javascript
//Define an HTML template for a custom popup content laypout.
var popupTemplate = '<div class="customInfobox"><div class="name">{name}</div>{description}</div>';

//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

dataSource.add(new atlas.data.Feature(new atlas.data.Point([-122.1333, 47.63]), {
  name: 'Microsoft Building 41', 
  description: '15571 NE 31st St, Redmond, WA 98052'
}));

//Create a layer to render point data.
var symbolLayer = new atlas.layer.SymbolLayer(dataSource);

//Add the polygon and line the symbol layer to the map.
map.layers.add(symbolLayer);

//Create a popup but leave it closed so we can update it and display it later.
popup = new atlas.Popup({
  pixelOffset: [0, -18],
  closeButton: false
});

//Add a hover event to the symbol layer.
map.events.add('mouseover', symbolLayer, function (e) {
  //Make sure that the point exists.
  if (e.shapes && e.shapes.length > 0) {
    var content, coordinate;
    var properties = e.shapes[0].getProperties();
    content = popupTemplate.replace(/{name}/g, properties.name).replace(/{description}/g, properties.description);
    coordinate = e.shapes[0].getCoordinates();

    popup.setOptions({
      //Update the content of the popup.
      content: content,

      //Update the popup's position with the symbol's coordinate.
      position: coordinate

    });
    //Open the popup.
    popup.open(map);
  }
});

map.events.add('mouseleave', symbolLayer, function (){
  popup.close();
});
```

다음은 위의 기능의 전체 실행 코드 샘플입니다.

<br/>

<iframe height='500' scrolling='no' title='Azure Maps를 사용하여 팝업 추가' src='//codepen.io/azuremaps/embed/MPRPvz/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)가 제공하는 <a href='https://codepen.io/azuremaps/pen/MPRPvz/'>Azure Maps를 사용하여 팝업 추가</a> 펜을 참조하세요.
</iframe>

## <a name="reusing-a-popup-with-multiple-points"></a>여러 점을 사용하여 팝업을 다시 사용

가장 좋은 방법은 하나의 팝업을 만들고 다시 사용하는 경우가 있습니다. 예를 들어 많은 수의 포인트가 있고 한 번에 하나의 팝업만 표시하려고 할 수 있습니다. 팝업을 다시 사용하면 응용 프로그램에서 만든 DOM 요소 수가 크게 줄어들어 성능이 향상됩니다. 다음 샘플에서는 3점 피쳐를 만듭니다. 그 중 하나를 클릭하면 해당 점 기능에 대한 콘텐츠와 함께 팝업이 표시됩니다.

<br/>

<iframe height='500' scrolling='no' title='여러 점을 사용하여 팝업을 다시 사용' src='//codepen.io/azuremaps/embed/rQbjvK/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)의 펜 <a href='https://codepen.io/azuremaps/pen/rQbjvK/'>여러 점을 사용하여 팝업을 다시 사용</a>을 참조하세요.
</iframe>

## <a name="customizing-a-popup"></a>팝업 사용자 지정

기본적으로 팝업에는 흰색 배경, 아래쪽에 포인터 화살표, 오른쪽 상단 모서리에 닫기 버튼이 있습니다. 다음 샘플은 팝업 옵션을 사용하여 `fillColor` 배경색을 검은색으로 변경합니다. 옵션을 false로 설정하면 닫기 버튼이 `CloseButton` 제거됩니다. 팝업의 HTML 콘텐츠는 팝업 가장자리에서 10 픽셀의 패딩을 사용합니다. 텍스트가 흰색으로 표시되므로 검은색 배경에 잘 표시됩니다.  

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="사용자 정의 팝업" src="//codepen.io/azuremaps/embed/ymKgdg/?height=500&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen에서</a>Azure 지도 ()에<a href='https://codepen.io/azuremaps'>@azuremaps</a>의해 <a href='https://codepen.io/azuremaps/pen/ymKgdg/'>펜 사용자 지정 팝업을</a> 참조 하십시오.
</iframe>

## <a name="add-popup-templates-to-the-map"></a>맵에 팝업 템플릿 추가

팝업 템플릿을 사용하면 팝업에 대한 데이터 기반 레이아웃을 쉽게 만들 수 있습니다. 아래 섹션에서는 다양한 팝업 템플릿을 사용하여 피처 속성을 사용하여 형식이 지정된 콘텐츠를 생성하는 방법을 보여 줍니다.

> [!NOTE]
> 기본적으로 팝업 템플릿을 사용하여 렌더링된 모든 콘텐츠는 보안 기능으로 iframe 내부에 샌드박스됩니다. 그러나 다음과 같은 제한 사항이 있습니다.
>
> - 모든 스크립트, 양식, 포인터 잠금 및 상위 탐색 기능은 사용할 수 없습니다. 링크를 클릭하면 새 탭에서 열 수 있습니다. 
> - iframe에서 매개 변수를 `srcdoc` 지원하지 않는 이전 브라우저는 소량의 콘텐츠를 렌더링하는 것으로 제한됩니다.
> 
> 팝업에 로드되는 데이터를 신뢰하고 팝업에 로드된 이러한 스크립트가 응용 프로그램에 액세스할 수 있도록 하려면 팝업 템플릿 `sandboxContent` 옵션을 false로 설정하여 이 옵션을 비활성화할 수 있습니다. 

### <a name="string-template"></a>문자열 템플릿

String 템플릿은 자리 표시자를 피처 속성값으로 바꿉습니다. 기능의 속성에 문자열 형식의 값을 할당할 필요가 없습니다. 예를 들어 `value1` 정수보유. 그런 다음 이러한 값은 `popupTemplate`의 콘텐츠 속성에 전달됩니다. 

이 `numberFormat` 옵션은 표시할 숫자의 형식을 지정합니다. 를 `numberFormat` 지정하지 않으면 코드에서 팝업 템플릿 날짜 형식을 사용합니다. 이 `numberFormat` 옵션은 [Number.toLocaleString](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Number/toLocaleString) 함수를 사용하여 숫자의 서식을 지정합니다. 많은 수의 포맷을 `numberFormat` 지정하려면 [NumberFormat.format.format의](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/NumberFormat/format)함수와 함께 옵션을 사용하는 것이 좋습니다. 예를 들어 아래 코드 조각은 `maximumFractionDigits` 분수 숫자 수를 2개로 제한하는 데 사용합니다.

> [!Note]
> String 템플릿에서 이미지를 렌더링할 수 있는 방법은 하나뿐입니다. 먼저 String 템플릿에는 이미지 태그가 있어야 합니다. 이미지 태그에 전달되는 값은 이미지의 URL이어야 합니다. 그런 다음 문자열 템플릿은 `isImage` `HyperLinkFormatOptions`에서 true로 설정해야 합니다. 이 `isImage` 옵션은 하이퍼링크가 이미지용임을 지정하고 하이퍼링크가 이미지 태그에 로드되도록 지정합니다. 하이퍼링크를 클릭하면 이미지가 열립니다.

```javascript
var templateOptions = {
  content: 'This template uses a string template with placeholders.<br/><br/> - Value 1 = {value1}<br/> - Value 2 = {value2/subValue}<br/> - Array value [2] = {arrayValue/2}',
  numberFormat: {
    maximumFractionDigits: 2
  }
};

var feature = new atlas.data.Feature(new atlas.data.Point([0, 0]), {
    title: 'Template 1 - String template',
    value1: 1.2345678,
    value2: {
        subValue: 'Pizza'
    },
    arrayValue: [3, 4, 5, 6]
});

var popup = new atlas.Popup({
  content: atlas.PopupTemplate.applyTemplate(feature.properties, templateOptions),
  position: feature.geometry.coordinates
});
```

### <a name="propertyinfo-template"></a>속성정보 템플릿

PropertyInfo 템플릿에는 피쳐의 사용 가능한 속성이 표시됩니다. 이 `label` 옵션은 사용자에게 표시할 텍스트를 지정합니다. 지정하지 않으면 `label` 하이퍼링크가 표시됩니다. 또한 하이퍼링크가 이미지인 경우 "alt" 태그에 할당된 값이 표시됩니다. 는 `dateFormat` 날짜 형식을 지정하고 날짜 형식을 지정하지 않으면 날짜가 문자열로 렌더링됩니다. 이 `hyperlinkFormat` 옵션은 클릭 가능한 링크를 렌더링하며, 마찬가지로 이 `email` 옵션을 사용하여 클릭 가능한 이메일 주소를 렌더링할 수 있습니다.

PropertyInfo 템플릿이 최종 사용자에게 속성을 표시하기 전에 해당 기능에 대해 속성이 실제로 정의되어 있는지 재귀적으로 확인합니다. 또한 스타일 및 제목 속성 표시를 무시합니다. 예를 들어 , `color`에 `size` `anchor` `strokeOpacity` `visibility`대해 " 따라서 백 엔드에서 속성 경로 검사가 완료되면 PropertyInfo 템플릿은 테이블 형식으로 콘텐츠를 표시합니다.

```javascript
var templateOptions = {
  content: [
    {
        propertyPath: 'createDate',
        label: 'Created Date'
    },
    {
        propertyPath: 'dateNumber',
        label: 'Formatted date from number',
        dateFormat: {
          weekday: 'long',
          year: 'numeric',
          month: 'long',
          day: 'numeric',
          timeZone: 'UTC',
          timeZoneName: 'short'
        }
    },
    {
        propertyPath: 'url',
        label: 'Code samples',
        hideLabel: true,
        hyperlinkFormat: {
          lable: 'Go to code samples!',
          target: '_blank'
        }
    },
    {
        propertyPath: 'email',
        label: 'Email us',
        hideLabel: true,
        hyperlinkFormat: {
          target: '_blank',
          scheme: 'mailto:'
        }
    }
  ]
};

var feature = new atlas.data.Feature(new atlas.data.Point([0, 0]), {
    title: 'Template 2 - PropertyInfo',
    createDate: new Date(),
    dateNumber: 1569880860542,
    url: 'https://aka.ms/AzureMapsSamples',
    email: 'info@microsoft.com'
}),

var popup = new atlas.Popup({
  content: atlas.PopupTemplate.applyTemplate(feature.properties, templateOptions),
  position: feature.geometry.coordinates
});
```

### <a name="multiple-content-templates"></a>여러 콘텐츠 템플릿

기능은 문자열 템플릿과 PropertyInfo 템플릿의 조합을 사용하여 콘텐츠를 표시할 수도 있습니다. 이 경우 String 템플릿은 흰색 배경에 자리 표시자 값을 렌더링합니다.  또한 PropertyInfo 템플릿은 테이블 내부의 전체 너비 이미지를 렌더링합니다. 이 샘플의 속성은 이전 샘플에서 설명한 속성과 유사합니다.

```javascript
var templateOptions = {
  content: [
    'This template has two pieces of content; a string template with placeholders and a array of property info which renders a full width image.<br/><br/> - Value 1 = {value1}<br/> - Value 2 = {value2/subValue}<br/> - Array value [2] = {arrayValue/2}',
    [{
      propertyPath: 'imageLink',
      label: 'Image',
      hideImageLabel: true,
      hyperlinkFormat: {
        isImage: true
      }
    }]
  ],
  numberFormat: {
    maximumFractionDigits: 2
  }
};

var feature = new atlas.data.Feature(new atlas.data.Point([0, 0]), {
    title: 'Template 3 - Multiple content template',
    value1: 1.2345678,
    value2: {
    subValue: 'Pizza'
    },
    arrayValue: [3, 4, 5, 6],
    imageLink: 'https://azuremapscodesamples.azurewebsites.net/common/images/Pike_Market.jpg'
});

var popup = new atlas.Popup({
  content: atlas.PopupTemplate.applyTemplate(feature.properties, templateOptions),
  position: feature.geometry.coordinates
});
```

### <a name="points-without-a-defined-template"></a>정의된 템플릿이 없는 점

팝업 템플릿이 문자열 템플릿, PropertyInfo 템플릿 또는 둘 다의 조합으로 정의되지 않은 경우 기본 설정을 사용합니다. `title` 만 할당 `description` 된 속성 인 경우 팝업 템플릿은 흰색 배경, 오른쪽 상단 모서리에 닫기 버튼을 표시합니다. 그리고, 중소 화면에서, 그것은 하단에 화살표를 보여줍니다. 기본 설정은 `title` 및 `description`을 제외한 모든 속성에 대한 테이블 내부에 표시됩니다. 기본 설정으로 되돌아가는 경우에도 팝업 템플릿을 프로그래밍 방식으로 조작할 수 있습니다. 예를 들어 사용자는 하이퍼링크 검색을 해제할 수 있으며 기본 설정은 여전히 다른 속성에 적용됩니다.

CodePen에서 지도에서 포인트를 클릭합니다. 문자열 템플릿, PropertyInfo 템플릿 및 여러 콘텐츠 템플릿 : 다음 팝업 템플릿의 각각에 대한 지도에 포인트가있습니다. 기본 설정을 사용하여 템플릿이 렌더링되는 방식을 보여 줄 세 가지 포인트도 있습니다.

<br/>

<iframe height='500' scrolling='no' title='팝업 템플릿' src='//codepen.io/azuremaps/embed/dyovrzL/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen에서</a>Azure 지도 ()별<a href='https://codepen.io/azuremaps'>@azuremaps</a>펜 <a href='https://codepen.io/azuremaps/pen/dyovrzL/'>팝업 템플릿을</a> 참조하십시오.
</iframe>

## <a name="reuse-popup-template"></a>팝업 템플릿 재사용

팝업을 다시 사용하는 것과 마찬가지로 팝업 템플릿을 다시 사용할 수 있습니다. 이 방법은 여러 지점에 대해 한 번에 하나의 팝업 템플릿만 표시하려는 경우에 유용합니다. 팝업 템플릿을 다시 사용하면 응용 프로그램에서 만든 DOM 요소 수가 줄어들어 응용 프로그램 성능이 향상됩니다. 다음 샘플에서는 세 가지 점에 대해 동일한 팝업 템플릿을 사용합니다. 그 중 하나를 클릭하면 해당 점 기능에 대한 콘텐츠와 함께 팝업이 표시됩니다.

<br/>

<iframe height='500' scrolling='no' title='재사용팝업템플릿' src='//codepen.io/azuremaps/embed/WNvjxGw/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen에서</a>Azure 지도 ()별<a href='https://codepen.io/azuremaps'>@azuremaps</a>펜 <a href='https://codepen.io/azuremaps/pen/WNvjxGw/'>재사용PopupTemplate를</a> 참조하십시오.
</iframe>

## <a name="popup-events"></a>팝업 이벤트

팝업을 열고 닫고 끌 수 있습니다. 팝업 클래스는 개발자가 이러한 이벤트에 반응하는 데 도움이 되는 이벤트를 제공합니다. 다음 샘플은 사용자가 팝업을 열거나 닫거나 끌 때 이벤트가 발생하도록 강조 표시됩니다. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="팝업 이벤트" src="//codepen.io/azuremaps/embed/BXrpvB/?height=500&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen에서</a>Azure 지도 ()에<a href='https://codepen.io/azuremaps'>@azuremaps</a>의한 펜 <a href='https://codepen.io/azuremaps/pen/BXrpvB/'>팝업 이벤트를</a> 참조하십시오.
</iframe>

## <a name="next-steps"></a>다음 단계

이 문서에서 사용된 클래스 및 메서드에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [Popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup)

> [!div class="nextstepaction"]
> [PopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popupoptions)

> [!div class="nextstepaction"]
> [팝업 템플릿](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popuptemplate)

전체 코드 샘플에 대한 다음 문서를 참조하세요.

> [!div class="nextstepaction"]
> [기호 레이어 추가](./map-add-pin.md)

> [!div class="nextstepaction"]
> [HTML 표식 추가](./map-add-custom-html.md)

> [!div class="nextstepaction"]
> [선 계층 추가](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [다각형 계층 추가](map-add-shape.md)
