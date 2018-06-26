---
title: Azure Maps로 방향 표시 | Microsoft Docs
description: Javascript 맵에서 두 위치 간의 방향을 표시하는 방법
author: jingjing-z
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: codepen
ms.openlocfilehash: 5e9ab73ddc16517e17894cddd9bc102f3941f00c
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/25/2018
ms.locfileid: "35778803"
---
# <a name="show-directions-from-a-to-b"></a>A에서 B로의 방향 표시 

이 문서에서는 경로 요청을 만들고 맵에 경로를 표시하는 방법을 설명합니다. 

## <a name="understand-the-code"></a>코드 이해

<iframe height='500' scrolling='no' title='맵에 A에서 B로의 방향 표시' src='//codepen.io/azuremaps/embed/zRyNmP/?height=469&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)에 의해 펜 <a href='https://codepen.io/azuremaps/pen/zRyNmP/'>A에서 B로의 방향 표시</a>를 참조하세요.
</iframe>

위의 코드에서 첫 번째 코드 블록은 맵 개체를 생성합니다. 지침은 [맵 만들기](./map-create.md)를 참조하세요.

두 번째 코드 블록은 경로의 시작점과 끝점을 나타내기 위해 맵에 핀을 만들고 추가합니다. 지침은 [맵에 핀 추가](map-add-pin.md)를 참조하세요.

세 번째 코드 블록은 map 클래스의 [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#setcamerabounds) 함수를 사용하여 경로의 시작점과 끝점을 기반으로 맵의 경계 상자를 설정합니다.

네 번째 코드 블록은 [XMLHttpRequest](https://xhr.spec.whatwg.org/)를 [Azure Maps 경로 API](https://docs.microsoft.com/rest/api/maps/route/getroutedirections)로 보냅니다.

코드의 마지막 블록은 수신 응답을 구문 분석합니다. 성공적인 응답을 위해 각 중간 지점에 대한 위도 및 경도 정보를 수집합니다. 각 중간 지점을 후속 중간 지점에 연결하여 선 배열을 만듭니다. 모든 선을 맵에 추가하여 경로를 렌더링합니다. 지침은 [맵에 선 추가](./map-add-shape.md#addALine)를 참조하세요.

## <a name="next-steps"></a>다음 단계

이 문서에서 사용된 클래스 및 메서드에 대해 자세히 알아봅니다. 

* [Map](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest)
    * [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#setcamerabounds)
    * [addLinestrings](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addlinestrings)
    * [addPins](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addpins)

맵에 추가할 더 많은 코드 예제를 보려면 다음 문서를 참조하세요. 
* [맵에 트래픽 표시](./map-show-traffic.md)
* [맵 - 마우스 이벤트 상호 작용](./map-events.md)
