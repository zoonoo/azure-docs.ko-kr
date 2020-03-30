---
title: 지도 이벤트 처리 | 마이크로소프트 Azure 지도
description: 이 문서에서는 Microsoft Azure Maps 웹 SDK를 사용하여 맵 이벤트가 있는 대화형 웹 SDK 맵을 만드는 방법을 알아봅니다.
author: jinzh-azureiot
ms.author: jinzh
ms.date: 09/10/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: b97371d1b63ad4abfe1635e426df1449ab5f3f14
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79534901"
---
# <a name="interact-with-the-map"></a>맵과 상호 작용

이 문서에서는 맵 [이벤트 클래스를](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?#events)사용하는 방법을 보여 주며 있습니다. 속성은 맵과 맵의 여러 레이어에서 이벤트를 강조 표시합니다. HTML 마커와 상호 작용할 때 이벤트를 강조 표시할 수도 있습니다.

## <a name="interact-with-the-map"></a>맵과 상호 작용

아래 맵을 사용하여 플레이하고 오른쪽에 강조 표시된 해당 마우스 이벤트를 참조하십시오. **JS 탭**을 클릭하면 JavaScript 코드를 보고 편집할 수 있습니다. **CodePen에서 편집을** 클릭하여 CodePen에서 코드를 수정할 수도 있습니다.

<br/>

<iframe height='600' scrolling='no' title='맵 – 마우스 이벤트와 상호 작용' src='//codepen.io/azuremaps/embed/bLZEWd/?height=600&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)에 의해 펜 <a href='https://codepen.io/azuremaps/pen/bLZEWd/'>맵과 상호 작용 - 마우스 이벤트</a>를 참조하세요.
</iframe>

## <a name="interact-with-map-layers"></a>맵 계층과 상호 작용

다음 코드는 심볼 레이어와 상호 작용할 때 발생한 이벤트를 강조 표시됩니다. 기호, 거품, 선 및 다각형 레이어는 모두 동일한 이벤트 집합을 지원합니다. 히트 맵및 타일 레이어는 이러한 이벤트를 지원하지 않습니다.

<br/>

<iframe height='600' scrolling='no' title='맵 - 계층 이벤트와 상호 작용' src='//codepen.io/azuremaps/embed/bQRRPE/?height=600&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)에 의해 펜 <a href='https://codepen.io/azuremaps/pen/bQRRPE/'>맵 - 계층 이벤트와 상호 작용</a>을 참조하세요.
</iframe>

## <a name="interact-with-html-marker"></a>HTML 표식과 상호 작용

다음 코드는 HTML 마커에 자바 스크립트 맵 이벤트를 추가합니다. 또한 HTML 표식과 상호 작용할 때 발생한 이벤트의 이름을 강조 표시합니다.

<br/>

<iframe height='500' scrolling='no' title='맵 - HTML 표식 이벤트와 상호 작용' src='//codepen.io/azuremaps/embed/VVzKJY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)에 의해 펜 <a href='https://codepen.io/azuremaps/pen/VVzKJY/'>맵 - HTML 표식 이벤트와 상호 작용</a>을 참조하세요.
</iframe>

다음 표에는 지원되는 모든 맵 클래스 이벤트가 나열됩니다.

| 행사               | 설명 |
|---------------------|-------------|
| `boxzoomend`        | "상자 확대/축소" 상호 작용이 끝나면 발생합니다.|
| `boxzoomstart`      | "상자 확대/축소" 상호 작용이 시작될 때 발생합니다.|
| `click`             | 포인팅 장치를 누르고 맵의 동일한 지점에서 해제할 때 발생합니다.|
| `close`             | 팝업이 수동으로 또는 프로그래밍적으로 닫히면 발생합니다.|
| `contextmenu`       | 마우스오른쪽 버튼을 클릭하면 발생합니다.|
| `data`              | 맵 데이터가 로드되거나 변경될 때 발생합니다. |
| `dataadded`         | `DataSource`에 셰이프가 추가될 때 발생합니다.|
| `dataremoved`       | 에서 셰이프를 제거할 `DataSource`때 발생합니다.|
| `datasourceupdated` | 개체가 `DataSource` 업데이트될 때 발생합니다.|
| `dblclick`          | 지도의 동일한 지점에서 포인팅 장치를 두 번 클릭하면 발생합니다.|
| `drag`              | 맵, 팝업 또는 HTML 마커에서 "이동하려면 드래그" 상호 작용 중에 반복적으로 발생합니다.|
| `dragend`           | 맵, 팝업 또는 HTML 마커에서 "이동하려면 드래그" 상호 작용이 끝날 때 발생합니다.|
| `dragstart`         | 맵, 팝업 또는 HTML 마커에서 "이동하려면 드래그" 상호 작용이 시작될 때 발생합니다.|
| `error`             | 오류가 발생할 때 발생합니다.|
| `idle`              | <p>맵이 "유휴" 상태가 되기 전에 렌더링된 마지막 프레임 이후에 발생합니다.<ul><li>카메라 전환이 진행 중입니다.</li><li>현재 요청된 모든 타일이 로드되었습니다.</li><li>모든 페이드/전환 애니메이션이 완료되었습니다.</li></ul></p>|
| `keydown`           | 키를 누르면 발사됩니다.|
| `keypress`          | 입력 가능한 문자(ANSI 키)를 생성하는 키를 누르면 발생합니다.|
| `keyup`             | 키가 해제되면 발생합니다.|
| `layeradded`        | 레이어가 맵에 추가될 때 발생합니다.|
| `layerremoved`      | 맵에서 레이어가 제거될 때 발생합니다.|
| `load`              | 필요한 모든 리소스가 다운로드되고 맵의 첫 번째 시각적으로 완전한 렌더링이 발생한 직후에 발생합니다.|
| `mousedown`         | 포인팅 장치가 맵 내에서 누르거나 요소 위에 있을 때 발생합니다.|
| `mouseenter`        | 포인팅 장치가 맵 또는 요소 위로 처음 이동될 때 발생합니다. |
| `mouseleave`        | 포인팅 장치가 맵 또는 요소 밖으로 이동될 때 발생합니다. |
| `mousemove`         | 맵 또는 요소 내에서 포인팅 장치가 이동될 때 발생합니다.|
| `mouseout`          | 포인트 장치가 맵의 캔버스를 떠날 때 발생합니다.|
| `mouseover`         | 포인팅 장치가 맵 또는 요소 위로 이동될 때 발생합니다.|
| `mouseup`           | 포인팅 장치가 맵 내에서 해제되거나 요소 위에 있을 때 발생합니다.|
| `move`              | 사용자 상호 작용 또는 메서드의 결과로 한 뷰에서 다른 뷰로 애니메이션된 전환 중에 반복적으로 발생합니다.|
| `moveend`           | 맵이 사용자 상호 작용 또는 메서드의 결과로 한 뷰에서 다른 보기로의 전환을 완료한 직후에 발생합니다.|
| `movestart`         | 맵이 사용자 상호 작용 또는 메서드의 결과로 한 뷰에서 다른 보기로 의 전환을 시작하기 직전에 발생합니다.|
| `open`              | 팝업이 수동으로 또는 프로그래밍하여 열릴 때 발생합니다.|
| `pitch`             | 사용자 상호 작용 이나 방법의 결과로 맵의 피치 (기울기)가 변경 될 때마다 발생 합니다.|
| `pitchend`          | 맵의 피치(틸트)가 사용자 상호 작용 또는 방법의 결과로 변경된 직후에 발생합니다.|
| `pitchstart`        | 맵의 피치(틸트)가 사용자 상호 작용 또는 방법의 결과로 변경될 때마다 발생합니다.|
| `ready`             | 맵이 프로그래밍 방식으로 상호 작용할 준비가 되기 전에 필요한 최소 맵 리소스가 로드될 때 발생합니다.|
| `render`            | <p>다음 의 결과로 맵이 화면에 그려질 때마다 발생합니다.<ul><li>맵의 위치, 확대/축소, 피치 또는 베어링변경</li><li>맵 스타일변경.</li><li>원본에 대한 `DataSource` 변경.</li><li>벡터 타일, GeoJSON 파일, 글리프 또는 스프라이트의 로드입니다.</li></ul></p>|
| `resize`            | 맵의 크기가 조정된 직후 에 발사됩니다.|
| `rotate`            | "회전하기 위해 드래그" 상호 작용 중에 반복적으로 발생합니다.|
| `rotateend`         | "드래그하여 회전" 상호 작용이 끝날 때 발생합니다.|
| `rotatestart`       | "회전하는 드래그" 상호 작용이 시작될 때 발생합니다.|
| `shapechanged`      | 셰이프 개체 속성이 변경될 때 발생합니다.|
| `sourcedata`        | 맵의 소스 중 하나가 소스에 속한 타일이 로드되거나 변경되는 경우를 포함하여 로드되거나 변경될 때 발생합니다. |
| `sourceadded`       | 맵에 `DataSource` 추가되거나 `VectorTileSource` 추가될 때 발사됩니다.|
| `sourceremoved`     | 맵에서 `DataSource` 또는 `VectorTileSource` 제거될 때 발사됩니다.|
| `styledata`         | 맵의 스타일이 로드되거나 변경될 때 발생합니다.|
| `styleimagemissing` | 레이어가 존재하지 않는 이미지 스프라이트에서 이미지를 로드하려고 할 때 발생합니다. |
| `tokenacquired`     | AAD 액세스 토큰을 얻을 때 발생합니다.|
| `touchcancel`       | 맵 내에서 터치 취소 이벤트가 발생하면 발생합니다.|
| `touchend`          | 맵 내에서 터치 엔드 이벤트가 발생하면 발생합니다.|
| `touchmove`         | 맵 내에서 터치이동 이벤트가 발생하면 발생합니다.|
| `touchstart`        | 맵 내에서 터치 시작 이벤트가 발생하면 발생합니다.|
| `wheel`             | 맵 내에서 마우스 휠 이벤트가 발생하면 발생합니다.|
| `zoom`              | 사용자 상호 작용 또는 메서드의 결과로 한 확대/축소 수준에서 다른 확대/축소 수준으로 애니메이션된 전환 중에 반복적으로 발생합니다.|
| `zoomend`           | 맵이 사용자 상호 작용 또는 메서드의 결과로 한 확대/축소 수준에서 다른 확대/축소 수준으로 전환을 완료한 직후에 발생합니다.|
| `zoomstart`         | 맵이 사용자 상호 작용 또는 메서드의 결과로 한 확대/축소 수준에서 다른 확대/축소 수준으로 전환되기 직전에 발생합니다.|


## <a name="next-steps"></a>다음 단계

전체 코드 예제는 다음 문서를 참조하세요.

> [!div class="nextstepaction"]
> [Azure 지도 서비스 모듈 사용](./how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [코드 샘플](https://docs.microsoft.com/samples/browse/?products=azure-maps)
