---
title: Azure Maps로 맵 만들기 | Microsoft Docs
description: Javascript 맵을 만드는 방법
author: jingjing-z
ms.author: jinzh
ms.date: 09/14/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 9759c4149c6b026837e550dcf3ab0a0156bbb736
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/17/2018
ms.locfileid: "45730012"
---
# <a name="create-a-map"></a>맵 만들기

이 문서에서는 맵을 만드는 방법을 보여 줍니다.  

## <a name="understand-the-code"></a>코드 이해

두 가지 방법으로 맵을 만들 수 있습니다. 중심점과 확대/축소 수준을 지정하여 맵의 카메라를 설정할 수도 있고, 남서쪽 경계 지점과 북동쪽 경계 지점을 지정하여 맵의 카메라 경계를 설정할 수도 있습니다.

<a id="setCameraOptions"></a>

### <a name="set-the-camera"></a>카메라 설정

<iframe height='310' scrolling='no' title='CameraOptions를 통해 맵 만들기' src='//codepen.io/azuremaps/embed/qxKBMN/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>의 Pen <a href='https://codepen.io/azuremaps/pen/qxKBMN/'>Azure Location Based Services에서 제공되는 `CameraOptions` </a>를 통해 맵 만들기(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)를 참조하세요.
</iframe>

위의 코드에서 [맵 개체](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)는 `new atlas.Map()`을 통해 생성됩니다. 중심 및 확대/축소 수준과 같은 맵 속성은 [CameraOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/models.cameraoptions?view=azure-iot-typescript-latest)의 일부입니다. `CameraOptions`는 맵 생성자에서 정의하거나 맵 클래스의 [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera) 함수를 통해 정의할 수 있습니다.

<a id="setCameraBoundsOptions"></a>

### <a name="set-the-camera-bounds"></a>카메라 경계 설정

<iframe height='310' scrolling='no' title='CameraBoundsOptions를 통해 맵 만들기' src='//codepen.io/azuremaps/embed/ZrRbPg/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>의 Pen <a href='https://codepen.io/azuremaps/pen/ZrRbPg/'>Azure Maps에서 제공되는 `CameraBoundsOptions`</a>를 통해 맵 만들기(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)를 참조하세요.
</iframe>

위의 코드에서 [맵 개체](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)는 `new atlas.Map()`을 통해 생성됩니다. 경계 상자와 같은 맵 속성은 [CameraBoundsOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/models.cameraboundsoptions?view=azure-iot-typescript-latest)의 일부입니다. `CameraBoundsOptions`는 맵 클래스의 [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamerabounds) 함수를 통해 정의할 수 있습니다.

## <a name="try-out-the-code"></a>코드 사용해 보기

위의 샘플 코드를 살펴보세요. 왼쪽에 있는 **JS 탭**에서 JavaScript 코드를 편집하고 오른쪽에 있는 **결과 탭**에서 맵 보기 변경 내용을 확인할 수 있습니다. 또한 **CodePen에서 편집** 단추를 클릭하고 CodePen에서 코드를 편집할 수 있습니다.

<a id="relatedReference"></a>

## <a name="next-steps"></a>다음 단계

이 문서에서 사용된 클래스 및 메서드에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

앱에 기능을 추가하는 코드 예제를 참조하세요.

> [!div class="nextstepaction"]
> [지도 스타일 선택](choose-map-style.md)

> [!div class="nextstepaction"]
> [맵 컨트롤 추가](map-add-controls.md)
