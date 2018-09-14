---
title: Azure Maps를 사용하여 좌표에 대한 정보 표시 | Microsoft Docs
description: 사용자가 좌표를 선택할 경우 맵에서 주소에 대한 정보를 표시하는 방법
author: jingjing-z
ms.author: jinzh
ms.date: 08/31/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 331e687c40f21b0bf6074239969848c632682773
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/04/2018
ms.locfileid: "43667484"
---
# <a name="get-information-from-a-coordinate"></a>좌표에서 정보 가져오기

이 문서에서는 역 주소 검색을 만들고 마우스 클릭 시 팝업에 클릭한 위치의 주소를 표시하는 방법을 보여 줍니다.

## <a name="understand-the-code"></a>코드 이해

<iframe height='500' scrolling='no' title='좌표에서 정보 가져오기(서비스 모듈)' src='//codepen.io/azuremaps/embed/ejEYMZ/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)로 펜 <a href='https://codepen.io/azuremaps/pen/ejEYMZ/'>좌표에서 정보 가져오기(서비스 모듈)</a>를 참조하세요.
</iframe>

위의 코드에서 첫 번째 코드 블록은 지도 개체를 만듭니다. 지침은 [지도 만들기](./map-create.md)를 참조하세요.

두 번째 코드 블록의 줄은 서비스 클라이언트를 인스턴스화합니다.

세 번째 코드 블록은 포인터에 마우스 커서의 스타일을 업데이트합니다.

네 번째 코드 블록은 팝업을 만듭니다. [맵에서 팝업 추가](./map-add-popup.md)에서 지침을 확인할 수 있습니다.

마지막 코드 블록은 마우스 클릭에 대한 이벤트 수신기를 추가합니다. 마우스 클릭 시 클릭한 지점의 좌표를 사용하여 검색 쿼리를 만듭니다. 그런 후 지도의 [getSearchAddressReverse](https://docs.microsoft.com/javascript/api/azure-maps-rest/services.search?view=azure-iot-typescript-latest#getsearchaddressreverse) 엔드포인트를 사용하여 좌표의 주소를 쿼리합니다.

성공적인 응답의 경우 클릭한 위치에 대한 주소를 수집하고 팝업 클래스의 [setPopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setpopupoptions) 함수를 통해 팝업 콘텐츠 및 위치를 정의합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서 사용된 클래스 및 메서드에 대해 자세히 알아봅니다. 
* [역 주소 검색](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)
* [맵](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)
    * [addEventListener](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addeventlistener)
* [팝업](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)
    * [setPopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setpopupoptions)
    * [열기](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open)
    * [닫기](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#close)

맵에 추가할 더 많은 코드 예제를 보려면 다음 문서를 참조하세요.
* [A에서 B로의 방향 표시](./map-route.md)
* [트래픽 표시](./map-show-traffic.md)
