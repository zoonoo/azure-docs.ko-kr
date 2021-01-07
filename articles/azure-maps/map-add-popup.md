---
title: 맵의 점에 팝업 추가 | Microsoft Azure 맵
description: Azure Maps 팝업, 팝업 템플릿 및 팝업 이벤트에 대해 알아봅니다. 맵의 점에 팝업을 추가 하는 방법 및 팝업을 다시 사용 하 고 사용자 지정 하는 방법을 참조 하세요.
author: anastasia-ms
ms.author: v-stharr
ms.date: 02/27/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 903574a8c8696f10d9903a685bab8d12b3e6395f
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/28/2020
ms.locfileid: "92891039"
---
# <a name="add-a-popup-to-the-map"></a>맵에 팝업 추가

이 문서에서는 맵에 있는 지점에 팝업을 추가하는 방법을 보여 줍니다.

## <a name="understand-the-code"></a>코드 이해

다음 코드에서는 `name` `description` 기호 계층을 사용 하 여 및 속성이 있는 point 기능을 맵에 추가 합니다. [Popup 클래스](/javascript/api/azure-maps-control/atlas.popup) 의 인스턴스가 만들어졌지만 표시 되지 않습니다. 기호 계층에 마우스 이벤트를 추가 하 여 팝업을 열고 닫는 팝업을 트리거합니다. 표식 기호가 가리킴 이면 팝업의 `position` 속성이 표식의 위치로 업데이트 되 고 `content` 옵션은  `name` 가리키는 point 기능의 및 속성을 래핑하는 일부 HTML로 업데이트 됩니다 `description` . 그런 다음 함수를 사용 하 여 맵에 팝업을 표시 합니다 `open` .

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

다음은 위의 기능을 실행하는 전체 코드 샘플입니다.

<br/>

<iframe height='500' scrolling='no' title='Azure Maps를 사용하여 팝업 추가' src='//codepen.io/azuremaps/embed/MPRPvz/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)가 제공하는 <a href='https://codepen.io/azuremaps/pen/MPRPvz/'>Azure Maps를 사용하여 팝업 추가</a> 펜을 참조하세요.
</iframe>

## <a name="reusing-a-popup-with-multiple-points"></a>여러 점을 사용하여 팝업을 다시 사용

가장 좋은 방법은 팝업 하나를 만들고 다시 사용 하는 것입니다. 예를 들어 많은 수의 점이 있고 한 번에 하나의 popup만 표시 하려는 경우가 있습니다. Popup을 다시 사용 하면 응용 프로그램에서 만든 DOM 요소의 수가 크게 줄어 성능이 향상 될 수 있습니다. 다음 샘플에서는 3 포인트 기능을 만듭니다. 그 중 하나를 클릭하면 해당 점 기능에 대한 콘텐츠와 함께 팝업이 표시됩니다.

<br/>

<iframe height='500' scrolling='no' title='여러 점을 사용하여 팝업을 다시 사용' src='//codepen.io/azuremaps/embed/rQbjvK/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)의 펜 <a href='https://codepen.io/azuremaps/pen/rQbjvK/'>여러 점을 사용하여 팝업을 다시 사용</a>을 참조하세요.
</iframe>

## <a name="customizing-a-popup"></a>팝업 사용자 지정

기본적으로 popup에는 흰색 배경, 아래쪽에는 포인터 화살표, 오른쪽 위 모서리에 닫기 단추가 있습니다. 다음 샘플에서는 팝업의 옵션을 사용 하 여 배경색을 검은색으로 변경 합니다 `fillColor` . 옵션을 false로 설정 하 여 닫기 단추를 제거 합니다 `CloseButton` . 팝업의 HTML 콘텐츠는 팝업의 가장자리에서 10 픽셀의 안쪽 여백을 사용 합니다. 텍스트는 흰색 이므로 검은색 배경에 잘 표시 됩니다.  

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="사용자 지정 된 팝업" src="//codepen.io/azuremaps/embed/ymKgdg/?height=500&theme-id=0&default-tab=result" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
CodePen의 Azure Maps ()로 펜 <a href='https://codepen.io/azuremaps/pen/ymKgdg/'>사용자 지정 팝업</a> 을 참조 하세요 <a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-popup-templates-to-the-map"></a>맵에 팝업 템플릿 추가

팝업 템플릿을 사용 하면 팝업에 대 한 데이터 기반 레이아웃을 쉽게 만들 수 있습니다. 다음 섹션에서는 기능 속성을 사용 하 여 서식이 지정 된 콘텐츠를 생성 하는 다양 한 팝업 템플릿을 사용 하는 방법을 보여 줍니다.

> [!NOTE]
> 기본적으로 렌더링 된 모든 콘텐츠는 팝업 템플릿을 사용 하 여 iframe 내에서 보안 기능으로 샌드 박싱 됩니다. 그러나 다음과 같은 제한 사항이 있습니다.
>
> - 모든 스크립트, 폼, 포인터 잠금 및 상위 탐색 기능을 사용할 수 없습니다. 링크를 클릭 하면 새 탭에서 링크를 열 수 있습니다. 
> - Iframe에서 매개 변수를 지원 하지 않는 이전 브라우저 `srcdoc` 는 적은 양의 콘텐츠를 렌더링 하도록 제한 됩니다.
> 
> 팝업에 로드 되는 데이터를 신뢰 하 고 잠재적으로 popup에 로드 된 이러한 스크립트가 응용 프로그램에 액세스할 수 있도록 하려면 popup template 옵션을 false로 설정 하 여이 기능을 사용 하지 않도록 설정할 수 있습니다 `sandboxContent` . 

### <a name="string-template"></a>문자열 템플릿

문자열 템플릿은 자리 표시자를 기능 속성 값으로 바꿉니다. 기능의 속성에는 문자열 형식의 값을 할당할 필요가 없습니다. 예를 들어은 `value1` 정수를 보유 합니다. 이러한 값은의 콘텐츠 속성에 전달 됩니다 `popupTemplate` . 

`numberFormat`옵션은 표시할 숫자의 형식을 지정 합니다. 을 `numberFormat` 지정 하지 않으면 코드에서 popup 템플릿 날짜 형식을 사용 합니다. `numberFormat`옵션은 [toLocaleString](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Number/toLocaleString) 함수를 사용 하 여 숫자의 형식을 지정 합니다. 숫자의 형식을 지정 하려면 `numberFormat` [intl.numberformat](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/NumberFormat/format)의 함수에 옵션을 사용 하는 것이 좋습니다. 예를 들어 아래 코드 조각에서는를 사용 하 여 `maximumFractionDigits` 소수 자릿수를 2로 제한 합니다.

> [!Note]
> 문자열 템플릿에서 이미지를 렌더링할 수 있는 방법은 한 가지 뿐입니다. 먼저 문자열 템플릿에 이미지 태그가 있어야 합니다. 이미지 태그에 전달 되는 값은 이미지에 대 한 URL 이어야 합니다. 그런 다음에서 문자열 템플릿이 `isImage` true로 설정 되어야 합니다 `HyperLinkFormatOptions` . `isImage`옵션은 이미지에 대 한 하이퍼링크를 지정 하 고, 하이퍼링크가 이미지 태그에 로드 되도록 지정 합니다. 하이퍼링크를 클릭 하면 이미지가 열립니다.

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

### <a name="propertyinfo-template"></a>PropertyInfo 템플릿

PropertyInfo 템플릿은 기능의 사용 가능한 속성을 표시 합니다. `label`옵션은 사용자에 게 표시할 텍스트를 지정 합니다. `label`을 지정 하지 않으면 하이퍼링크가 표시 됩니다. 하이퍼링크가 이미지인 경우에는 "alt" 태그에 할당 된 값이 표시 됩니다. 는 `dateFormat` 날짜의 형식을 지정 하 고, 날짜 형식이 지정 되지 않은 경우 날짜는 문자열로 렌더링 됩니다. `hyperlinkFormat`옵션은 클릭 가능한 링크를 렌더링 합니다 `email` . 옵션을 사용 하 여 클릭 가능한 메일 주소를 렌더링할 수도 있습니다.

PropertyInfo 템플릿이 최종 사용자에 게 속성을 표시 하기 전에 속성이 해당 기능에 대해 실제로 정의 되었는지 여부를 재귀적으로 검사 합니다. 또한 스타일 및 제목 속성 표시도 무시 합니다. 예를 들어,,, 및는 표시 되지 않습니다 `color` `size` `anchor` `strokeOpacity` `visibility` . 따라서 백 엔드에서 속성 경로 확인이 완료 되 면 PropertyInfo 템플릿은 콘텐츠를 테이블 형식으로 표시 합니다.

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

또한 문자열 템플릿과 PropertyInfo 템플릿 조합을 사용 하 여 콘텐츠를 표시할 수 있습니다. 이 경우 문자열 템플릿은 흰색 배경에 자리 표시자 값을 렌더링 합니다.  그리고 PropertyInfo 템플릿은 테이블 내에서 전체 너비 이미지를 렌더링 합니다. 이 샘플의 속성은 이전 샘플에서 설명한 속성과 유사 합니다.

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

### <a name="points-without-a-defined-template"></a>정의 된 템플릿이 없는 요소

Popup 템플릿이 문자열 템플릿, PropertyInfo 템플릿 또는 둘의 조합으로 정의 되지 않은 경우 기본 설정을 사용 합니다. `title`및 `description` 가 유일 하 게 할당 된 속성인 경우 팝업 템플릿은 흰색 배경, 오른쪽 위 모서리에 닫기 단추를 표시 합니다. 그리고 중소 화면에서 아래쪽에 화살표가 표시 됩니다. 기본 설정은 및 이외의 모든 속성에 대 한 테이블 내에 표시 `title` `description` 됩니다. 기본 설정으로 대체 하는 경우에도 팝업 템플릿이 프로그래밍 방식으로 계속 조작 될 수 있습니다. 예를 들어 사용자는 하이퍼링크 검색을 해제할 수 있으며 기본 설정은 다른 속성에도 적용 됩니다.

CodePen 지도의 요소를 클릭 합니다. 지도에는 문자열 템플릿, PropertyInfo 템플릿 및 여러 콘텐츠 템플릿의 각 팝업 템플릿에 대 한 점이 있습니다. 또한 기본 설정을 사용 하 여 템플릿이 렌더링 되는 방식을 보여 주는 세 가지 점이 있습니다.

<br/>

<iframe height='500' scrolling='no' title='PopupTemplates' src='//codepen.io/azuremaps/embed/dyovrzL/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>CodePen에서 Azure Maps ()로 Pen <a href='https://codepen.io/azuremaps/pen/dyovrzL/'>PopupTemplates</a> ()를 참조 하세요 <a href='https://codepen.io/azuremaps'>@azuremaps</a> . <a href='https://codepen.io'>CodePen</a>
</iframe>

## <a name="reuse-popup-template"></a>팝업 템플릿 다시 사용

팝업을 다시 사용 하는 것과 유사 하 게 팝업 템플릿을 다시 사용할 수 있습니다. 이 방법은 여러 점에 대해 한 번에 하나의 popup 템플릿만 표시 하려는 경우에 유용 합니다. Popup 템플릿을 다시 사용 하면 응용 프로그램에서 만든 DOM 요소 수가 줄어들어 응용 프로그램 성능이 향상 됩니다. 다음 샘플에서는 세 개의 점에 동일한 popup 템플릿을 사용 합니다. 그 중 하나를 클릭하면 해당 점 기능에 대한 콘텐츠와 함께 팝업이 표시됩니다.

<br/>

<iframe height='500' scrolling='no' title='ReusePopupTemplate' src='//codepen.io/azuremaps/embed/WNvjxGw/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>CodePen의 Azure Maps ()를 통해 Pen <a href='https://codepen.io/azuremaps/pen/WNvjxGw/'>Reusepopuptemplate</a> 을 참조 하세요 <a href='https://codepen.io/azuremaps'>@azuremaps</a> . <a href='https://codepen.io'>CodePen</a>
</iframe>

## <a name="popup-events"></a>Popup 이벤트

팝업은 열고, 닫고, 끌 수 있습니다. Popup 클래스는 개발자가 이러한 이벤트에 대응할 수 있도록 하는 이벤트를 제공 합니다. 다음 샘플에서는 사용자가 팝업을 열거나 닫거나 끌 때 발생 하는 이벤트를 강조 표시 합니다. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Popup 이벤트" src="//codepen.io/azuremaps/embed/BXrpvB/?height=500&theme-id=0&default-tab=result" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
CodePen의 Azure Maps ()로 펜 <a href='https://codepen.io/azuremaps/pen/BXrpvB/'>팝업 이벤트</a> 를 참조 하세요 <a href='https://codepen.io/azuremaps'>@azuremaps</a> . <a href='https://codepen.io'>CodePen</a>
</iframe>

## <a name="next-steps"></a>다음 단계

이 문서에서 사용된 클래스 및 메서드에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [팝업](/javascript/api/azure-maps-control/atlas.popup)

> [!div class="nextstepaction"]
> [PopupOptions](/javascript/api/azure-maps-control/atlas.popupoptions)

> [!div class="nextstepaction"]
> [PopupTemplate](/javascript/api/azure-maps-control/atlas.popuptemplate)

전체 코드 샘플에 대한 다음 문서를 참조하세요.

> [!div class="nextstepaction"]
> [기호 계층 추가](./map-add-pin.md)

> [!div class="nextstepaction"]
> [HTML 표식 추가](./map-add-custom-html.md)

> [!div class="nextstepaction"]
> [선 계층 추가](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [다각형 계층 추가](map-add-shape.md)