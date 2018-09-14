---
title: Azure Maps에서 지도 컨트롤 추가 | Microsoft Docs
description: Azure Maps에서 지도에 확대/축소 컨트롤, 피치 컨트롤, 회전 컨트롤 및 스타일 선택기를 추가하는 방법입니다.
author: walsehgal
ms.author: v-musehg
ms.date: 08/29/2018
ms.topic: how-to-guides
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 435c6545b69b4457c3e1035fb8125399d4c9c23a
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/04/2018
ms.locfileid: "43666134"
---
# <a name="add-map-controls-to-azure-maps"></a>Azure Maps에 지도 컨트롤 추가

이 문서에서는 지도에 지도 컨트롤을 추가하는 방법을 보여 줍니다. 또한 모든 컨트롤 및 [스타일 선택기](https://docs.microsoft.com/azure/azure-maps/choose-map-style#adding-the-style-picker)를 사용하여 지도를 만드는 방법도 알아봅니다.

## <a name="add-zoom-control"></a>확대/축소 컨트롤 추가

<iframe height='500' scrolling='no' title='확대/축소 컨트롤 추가' src='//codepen.io/azuremaps/embed/WKOQyN/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)로 펜 <a href='https://codepen.io/azuremaps/pen/WKOQyN/'>확대/축소 컨트롤 추가</a>를 참조하세요.
</iframe>

위 코드의 첫 번째 코드 블록은 지도 개체를 만듭니다. 지도 만드는 방법에 대한 지침은 [지도 만들기](./map-create.md)를 참조하세요.

두 번째 코드 블록은 atlas [ZoomControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.zoomcontrol?view=azure-iot-typescript-latest)을 사용하여 를 확대/축소 컨트롤 개체를 만듭니다.

확대/축소 컨트롤은 지도를 확대/축소하는 기능을 추가합니다. 세 번째 코드 블록은 지도의 [addControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addcontrol) 메서드를 사용하여 지도에 확대/축소 컨트롤을 추가합니다.

## <a name="add-pitch-control"></a>피치 컨트롤 추가

<iframe height='500' scrolling='no' title='피치 컨트롤 추가' src='//codepen.io/azuremaps/embed/xJrwaP/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)로 펜 <a href='https://codepen.io/azuremaps/pen/xJrwaP/'>피치 컨트롤 추가</a>를 참조하세요.
</iframe>

위의 첫 번째 코드 블록은 스타일을 회색조로 설정하여 지도 개체를 만듭니다. 지도 만드는 방법에 대한 지침은 [지도 만들기](./map-create.md)를 참조하세요.

두 번째 코드 블록은 atlas [PitchControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.pitchcontrol?view=azure-iot-typescript-latest)을 사용하여 를 피치 컨트롤 개체를 만듭니다.

피치 컨트롤은 지도의 피치를 변경하는 기능을 추가합니다. 세 번째 코드 블록은 지도의 [addControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addcontrol) 메서드를 사용하여 지도에 피치 컨트롤을 추가합니다.

## <a name="add-compass-control"></a>나침반 컨트롤 추가

<iframe height='500' scrolling='no' title='회전 컨트롤 추가' src='//codepen.io/azuremaps/embed/GBEoRb/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)로 펜 <a href='https://codepen.io/azuremaps/pen/GBEoRb/'>회전 컨트롤 추가</a>를 참조하세요.
</iframe>

위 코드의 첫 번째 코드 블록은 지도 개체를 만듭니다. 지도 만드는 방법에 대한 지침은 [지도 만들기](./map-create.md)를 참조하세요.

두 번째 코드 블록은 atlas [나침반 컨트롤](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.compasscontrol?view=azure-iot-typescript-latest#compasscontrol)을 사용하여 나침반 컨트롤 개체를 만듭니다. 또한 지도의 [addControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addcontrol) 메서드를 사용하여 지도에 나침반 컨트롤을 추가합니다.

## <a name="a-map-with-all-controls"></a>모든 컨트롤이 있는 지도

<iframe height='500' scrolling='no' title='모든 컨트롤이 있는 지도' src='//codepen.io/azuremaps/embed/qyjbOM/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)로 펜 <a href='https://codepen.io/azuremaps/pen/qyjbOM/'>모든 컨트롤이 있는 지도</a>를 참조하세요.
</iframe>

위 코드의 첫 번째 코드 블록은 지도 개체를 만듭니다. 지도 만드는 방법에 대한 지침은 [지도 만들기](./map-create.md)를 참조하세요.

두 번째 코드 블록은 atlas [CompassControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.compasscontrol?view=azure-iot-typescript-latest#compasscontrol)을 사용하여 나침반 컨트롤 개체를 만들고 지도의 [addControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addcontrol) 메서드를 사용하여 지도에 해당 개체를 추가합니다.

세 번째 코드 블록은 atlas [ZoomControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.zoomcontrol?view=azure-iot-typescript-latest)을 사용하여 확대/축소 컨트롤 개체를 만들고 지도의 [addControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addcontrol) 메서드를 사용하여 지도에 해당 개체를 추가합니다.

네 번째 코드 블록은 atlas [PitchControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.pitchcontrol?view=azure-iot-typescript-latest)을 사용하여 피치 컨트롤 개체를 만들고 지도의 [addControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addcontrol) 메서드를 사용하여 지도에 해당 개체를 추가합니다.

마지막 코드 블록은 atlas [StyleControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.stylecontrol?view=azure-iot-typescript-latest#stylecontrol)을 사용하여 스타일 선택기 개체를 만들고 지도의 [addControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addcontrol) 메서드를 사용하여 지도에 해당 개체를 추가합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서 사용된 클래스 및 메서드에 대해 자세히 알아봅니다. 
* [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)
    * [addControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addcontrol)

* [Atlas](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas?view=azure-iot-typescript-latest)
    * [CompassControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.compasscontrol?view=azure-iot-typescript-latest#compasscontrol)
    * [ZoomControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.zoomcontrol?view=azure-iot-typescript-latest)
    * [PitchControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.pitchcontrol?view=azure-iot-typescript-latest)
    * [StyleControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.stylecontrol?view=azure-iot-typescript-latest#stylecontrol)
    
맵에 추가할 더 많은 코드 예제를 보려면 다음 문서를 참조하세요. 
* [핀 추가](./map-add-pin.md)
* [팝업 추가](./map-add-popup.md)