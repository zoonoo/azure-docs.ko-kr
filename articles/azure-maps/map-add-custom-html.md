---
title: Azure Maps에서 사용자 지정 html 추가 | Microsoft Docs
description: Javascript 지도에 사용자 지정 html을 추가하는 방법
author: jingjing-z
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 3ee1f394536061cecaa5d775650149b30098560e
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34600224"
---
# <a name="add-custom-html-to-the-map"></a>지도에 사용자 지정 HTML 추가

이 문서는 지도에 이미지 파일과 같은 사용자 지정 HTML을 추가하는 방법을 보여줍니다. 

## <a name="understand-the-code"></a>코드 이해

<iframe height='466' scrolling='no' title='지도에 사용자 지정 html 추가 - png' src='//codepen.io/azuremaps/embed/MVoeVw/?height=466&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)에 의해 펜 <a href='https://codepen.io/azuremaps/pen/MVoeVw/'>지도에 사용자 지정 html 추가 - png</a>를 참조하세요.
</iframe>

위의 코드에서 첫 번째 코드 블록은 지도 개체를 만듭니다. 지침은 [지도 만들기](./map-create.md)를 참조하세요.

두 번째 코드 블록은 이미지에서 HTML 요소를 만듭니다.

마지막 코드 블록은 map 클래스의 [addHtml](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addhtml) 함수를 사용하여 지도의 지정된 위치에 이미지를 추가합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서 사용된 클래스 및 메서드에 대해 자세히 알아봅니다. 
* [Map](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest)
    * [addHtml](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addhtml)
    
맵에 추가할 더 많은 코드 예제를 보려면 다음 문서를 참조하세요. 
* [검색 결과 표시](./map-search-location.md)
* [좌표에서 정보 가져오기](./map-get-information-from-coordinate.md)

