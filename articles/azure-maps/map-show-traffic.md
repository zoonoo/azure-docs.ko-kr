---
title: Azure Maps를 사용하여 트래픽 표시 | Microsoft Docs
description: Javascript 맵에서 트래픽 데이터를 표시하는 방법
author: jingjing-z
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 6ff7a0270509c244fc97bd04d8ba648fd262dc58
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34600122"
---
# <a name="show-traffic-on-the-map"></a>맵에 트래픽 표시

이 문서에서는 맵에 트래픽 및 인시던트 정보를 표시하는 방법을 보여 줍니다. 

## <a name="understand-the-code"></a>코드 이해

<iframe height='456' scrolling='no' title='맵에 트래픽 표시' src='//codepen.io/azuremaps/embed/WMLRPw/?height=456&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)로 펜 <a href='https://codepen.io/azuremaps/pen/WMLRPw/'>맵에 트래픽 표시</a>를 참조하세요.
</iframe>

위의 코드에서 첫 번째 코드 블록은 맵 개체를 만듭니다. [맵 만들기](map-create.md)에서 지침을 참조할 수 있습니다.

두 번째 코드 블록은 맵 클래스의 [setTraffic](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#settraffic) 기능을 사용하여 맵에 트래픽 흐름 및 인시던트를 렌더링합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서 사용된 클래스 및 메서드에 대해 자세히 알아봅니다. 
* [맵](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest)
    * [setTraffic](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#settraffic)

맵에 추가할 더 많은 코드 예제를 보려면 다음 문서를 참조하세요. 
* [맵 - 마우스 이벤트 상호 작용](./map-events.md)
* [액세스할 수 있는 맵 작성](./map-accessibility.md)

[코드 샘플 페이지](http://aka.ms/AzureMapsSamples)에서 다른 매핑 시나리오를 확인해 보세요.
