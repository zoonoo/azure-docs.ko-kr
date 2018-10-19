---
title: Azure Maps로 팝업 추가 | Microsoft Docs
description: Javascript 맵에 팝업을 추가하는 방법
author: jingjing-z
ms.author: jinzh
ms.date: 09/17/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 76a7e230491d5e524a1d73437a56d12594cfebe2
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/18/2018
ms.locfileid: "46127440"
---
# <a name="add-a-popup-to-the-map"></a>맵에 팝업 추가

이 문서에서는 맵에 팝업을 추가하는 방법을 보여 줍니다.  

## <a name="understand-the-code"></a>코드 이해

<a id="addAPopup"></a>

<iframe height='500' scrolling='no' title='맵에 팝업 추가' src='//codepen.io/azuremaps/embed/zRyKxj/?height=545&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)로 펜 <a href='https://codepen.io/azuremaps/pen/zRyKxj/'>맵에 팝업 추가</a>를 참조하세요.
</iframe>

위의 코드에서 첫 번째 코드 블록은 맵 개체를 만듭니다. 지침은 [지도 만들기](./map-create.md)를 참조하세요.

두 번째 코드 블록은 핀을 만들어 맵에 추가합니다. 지침은 [맵에 핀 추가](./map-add-pin.md)를 참조하세요.

세 번째 코드 블록은 팝업에 표시할 콘텐츠를 만듭니다. 팝업 콘텐츠는 HTML 요소입니다.

네 번째 코드 블록은 `new atlas.Popup()`을 통해 [팝업 개체](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)를 만듭니다. content 및 position과 같은 팝업 속성은 [PopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/models.popupoptions?view=azure-iot-typescript-latest)의 일부입니다. PopupOptions는 popup 생성자나 popup 클래스의 [setPopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setpopupoptions) 함수를 통해 정의할 수 있습니다.

마지막 코드 블록은 map 클래스의 [addEventListener](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addeventlistener) 함수를 사용하여 핀에서 mouseover 이벤트를 수신하고 popup 클래스의 [open](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open) 함수를 사용하여 이벤트가 발생하면 팝업을 엽니다.

## <a name="next-steps"></a>다음 단계

이 문서에서 사용된 클래스 및 메서드에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [팝업](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)

전체 코드 샘플에 대한 다음 문서를 참조하세요.

> [!div class="nextstepaction"]
> [도형 추가](./map-add-shape.md)

> [!div class="nextstepaction"]
> [사용자 지정 HTML 추가](./map-add-custom-html.md)
