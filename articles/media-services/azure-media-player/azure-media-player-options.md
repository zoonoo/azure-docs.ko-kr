---
title: Azure Media Player 옵션
description: Azure Media Player embed 태그는 간단한 HTML5 비디오 태그이므로 대부분의 옵션에서는 표준 태그 특성을 사용하여 옵션을 설정할 수 있습니다.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/05/2021
ms.custom: devx-track-js
ms.openlocfilehash: 261d4710e1c88a89c6dcef06dad430cd996b2869
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106448782"
---
# <a name="options"></a>옵션 #

## <a name="setting-options"></a>설정 옵션 ##

Azure Media Player embed 태그는 간단한 HTML5 비디오 태그이므로 대부분의 옵션에서는 표준 태그 특성을 사용하여 옵션을 설정할 수 있습니다.

`<video controls autoplay ...>`

또는 data-setup 특성을 사용하여 [JSON](http://json.org/example.html) 형식으로 옵션을 제공할 수 있습니다. 이 방법을 사용하여 비디오 태그에 대해 표준이 아닌 옵션을 설정할 수 있습니다.

`<video data-setup='{ "controls": true, "autoplay": false }'...>`

마지막으로, data-setup 특성을 사용하여 플레이어 설정을 트리거하지 않는 경우 플레이어 옵션이 포함된 개체를 JavaScript 설정 함수의 두 번째 인수로 전달할 수 있습니다.

`amp("vid1", { "controls": true, "autoplay": false });`

> [!NOTE]
> 생성자의 옵션은 원본이 설정되기 전에 첫 번째 초기화에서만 설정됩니다.  동일한 초기화된 Azure Media Player 요소에 대한 옵션을 수정하려는 경우 원본을 변경하기 전에 옵션을 업데이트해야 합니다. `myPlayer.options({/*updated options*/});` 사용을 통해 JavaScript에서 옵션을 업데이트할 수 있습니다. 변경된 옵션만 영향을 받으므로 이전에 설정된 다른 모든 옵션은 그대로 유지됩니다.

## <a name="individual-options"></a>개별 옵션 ##

> [!NOTE]
>비디오 태그 특성은 true 또는 false(부울)만 가능합니다. 특성(등호 없음)을 포함하여 이를 설정하거나 제외하여 해제할 수 있습니다. 예를 들어 controls를 설정하는 경우: 올바르지 않음 `<video controls="true" ...>` 올바름 `<video controls ...>` 가장 많이 발생하는 문제는 이러한 값을 false로 설정하려 할 때 false를 값으로 사용하여(예: controls="false") 실제로 반대의 역할을 하여 값을 true로 설정하는 것입니다. 특성이 여전히 포함되어 있기 때문입니다.

### <a name="controls"></a>controls ###

controls 옵션은 플레이어에 사용자가 상호 작용할 수 있는 컨트롤이 있는지 여부를 설정합니다. 컨트롤을 사용하지 않고 비디오 재생을 시작하는 유일한 방법은 autoplay 특성을 사용하거나 API를 사용하는 것입니다.

`<video controls ...>` 또는 `{ "controls": true }`

### <a name="autoplay"></a>autoplay ###

autoplay가 true인 경우 페이지가 로드되는 즉시 비디오가 재생을 시작합니다(사용자의 상호 작용 없음).

> [!NOTE]
> 이 옵션은 Windows Phone, Apple iOS, Android 등의 모바일 디바이스에서 지원되지 않습니다. 모바일 디바이스는 소비자의 월간 데이터 요금제(요금이 높은 경우가 많음) 과다 사용을 방지하기 위해 자동 실행 기능을 차단합니다. 이 경우 비디오를 시작하려면 사용자 터치/클릭이 필요합니다.

`<video autoplay ...>` 또는 `{ "autoplay": true }`

### <a name="poster"></a>poster ###
poster 특성은 비디오 재생을 시작하기 전에 표시되는 이미지를 설정합니다. 주로 비디오의 프레임 또는 사용자 지정 제목 화면입니다. 사용자가 재생을 클릭하는 즉시 이 이미지는 사라집니다.

`<video poster="myPoster.jpg" ...>` 또는 `{ "poster": "myPoster.jpg" }`

### <a name="width"></a>width ###

width 특성은 비디오의 표시 너비를 설정합니다.

`<video width="640" ...>` 또는 `{ "width": 640 }`

### <a name="height"></a>키 ###

height 특성은 비디오의 표시 높이를 설정합니다.

`<video height="480" ...>` 또는 `{ "height": 480 }`

### <a name="plugins"></a>plugins ###

plugins JSON은 플러그 인에 있을 수 있는 옵션을 구성하도록 하는 AMP의 인스턴스와 로드할 플러그 인을 확인합니다.

   `<video... data-setup='{plugins: { "contentTitle": {"name": "Azure Medi Services Overview"}}}'...>`

플러그 인 개발 및 사용에 대한 자세한 내용은 [플러그 인 작성](azure-media-player-writing-plugins.md)을 참조하세요.

### <a name="other-options"></a>기타 옵션 ###

JSON을 가져오는 `data-setup` 매개 변수를 사용하여 `<video>` 태그에 기타 옵션을 설정할 수 있습니다.
`<video ... data-setup='{"nativeControlsForTouch": false}'>`

#### <a name="nativecontrolsfortouch"></a>nativeControlsForTouch ####

명시적으로 false로 설정됩니다. false로 설정하면 Azure Media Player 스킨이 여러 플랫폼에서 동일하게 렌더링됩니다.  또한 이름과 달리 터치는 계속 사용할 수 있습니다.

### <a name="fluid"></a>fluid ###

이 옵션을 true로 설정하면 비디오 요소가 부모 컨테이너의 전체 너비를 사용하고 높이는 표준 16:9 가로 세로 비율에 맞춰 조정됩니다.

`<video ... data-setup='{"fluid": true}'>`

`fluid` 옵션은 명시적 `width` 및 `height` 설정을 재정의합니다. 이 옵션은 Azure Media Player 버전 `2.0.0` 이상에서만 사용할 수 있습니다.

### <a name="playbackspeed"></a>playbackSpeed ###

`playbackSpeed` 옵션은 playbackSpeed 컨트롤 및 사용자에게 제공되는 재생 속도 설정 집합을 제어합니다. `playbackSpeed`은 개체를 받아들입니다. 컨트롤 막대에서 재생 속도 제어를 사용하려면 개체의 `enabled` 속성을 true로 설정해야 합니다. 태그에서 재생 속도를 사용하는 예시:

`<video ... data-setup='{"playbackSpeed": {"enabled": true}}'>`

`playbackSpeed` 설정의 다른 속성은 `PlaybackSpeedOptions` 개체에 의해 지정됩니다.

JavaScript에서 재생 속도 설정 옵션 설정의 예시:

```javascript
    var myPlayer = amp('vid1', {
        fluid: true,
        playbackSpeed: {
            enabled: true,
            initialSpeed: 1.0,
            speedLevels: [
                { name: "x4.0", value: 4.0 },
                { name: "x3.0", value: 3.0 },
                { name: "x2.0", value: 2.0 },
                { name: "x1.75", value: 1.75 },
                { name: "x1.5", value: 1.5 },
                { name: "x1.25", value: 1.25 },
                { name: "normal", value: 1.0 },
                { name: "x0.75", value: 0.75 },
                { name: "x0.5", value: 0.5 },
            ]
        }
    });
```

이 옵션은 Azure Media Player 버전 2.0.0 이상에서만 사용할 수 있습니다.

### <a name="staledatatimelimitinsec"></a>staleDataTimeLimitInSec ###

`staleDataTimeLimitInSec` 옵션은 mediaSource 버퍼에서 부실 데이터를 유지할 최대 시간(초)을 구성하는 데 사용할 수 있는 최적화입니다. 이 옵션은 기본적으로 사용하지 않도록 설정되어 있습니다.

### <a name="cea708captionssettings"></a>cea708CaptionsSettings ###

enabled를 true로 설정하면 라이브 스트림 및 라이브 보관에 라이브 CEA 캡션을 표시할 수 있습니다. label 특성이 필요하지 않으며, 포함되지 않은 경우 플레이어에서 기본 레이블로 대체합니다.

```javascript
     cea708CaptionsSettings: {
                enabled: true,
                srclang: 'en',
                label: 'Live CC'
            }
```

이 옵션은 Azure Media Player 버전 2.1.1 이상에서만 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계 ##

- [Azure Media Player 빠른 시작](azure-media-player-quickstart.md)
