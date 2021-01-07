---
title: Azure Media Player 옵션
description: Azure Media Player embed 코드는 단순히 HTML5 비디오 태그 이므로 대부분의 옵션에서는 표준 태그 특성을 사용 하 여 옵션을 설정할 수 있습니다.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/20/2020
ms.custom: devx-track-js
ms.openlocfilehash: 67f1f2fe3f6ac1061f1edcd22532bffaf0a1e815
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92366873"
---
# <a name="options"></a>옵션 #

## <a name="setting-options"></a>설정 옵션 ##

Azure Media Player embed 코드는 단순히 HTML5 비디오 태그 이므로 대부분의 옵션에서는 표준 태그 특성을 사용 하 여 옵션을 설정할 수 있습니다.

`<video controls autoplay ...>`

또는 데이터 설치 특성을 사용 하 여 [JSON](http://json.org/example.html) 형식으로 옵션을 제공할 수 있습니다. 이는 비디오 태그에 대해 표준이 아닌 옵션을 설정 하는 방법 이기도 합니다.

`<video data-setup='{ "controls": true, "autoplay": false }'...>`

마지막으로, 데이터 설치 특성을 사용 하 여 플레이어 설정을 트리거하지 않는 경우 플레이어 옵션을 사용 하 여 개체를 JavaScript 설치 함수의 두 번째 인수로 전달할 수 있습니다.

`amp("vid1", { "controls": true, "autoplay": false });`

> [!NOTE]
> 생성자의 옵션은 소스가 설정 되기 전에 첫 번째 초기화 에서만 설정 됩니다.  초기화 된 동일한 Azure Media Player 요소에 대 한 옵션을 수정 하려는 경우 소스를 변경 하기 전에 옵션을 업데이트 해야 합니다. 을 사용 하 여 JavaScript의 옵션을 업데이트할 수 있습니다 `myPlayer.options({/*updated options*/});` . 변경 된 옵션만 영향을 받으므로 이전에 설정 된 다른 모든 옵션은 유지 됩니다.

## <a name="individual-options"></a>개별 옵션 ##

> [!NOTE]
>비디오 태그 특성은 true 또는 false (부울)만 가능 합니다. 특성을 설정 하거나 해제 하려면 해당 특성 (등호 없음)만 포함 하면 됩니다. 예를 들어 컨트롤을 설정 하려면: 사용자가 실행 하는 `<video controls="true" ...>` `<video controls ...>` 가장 큰 문제는 값 (예: controls = "false")으로 false를 사용 하 여 이러한 값을 false로 설정 하는 것입니다.

### <a name="controls"></a>controls ###

Controls 옵션은 플레이어에 게 사용자가 조작할 수 있는 컨트롤이 있는지 여부를 설정 합니다. 컨트롤을 사용 하지 않고 비디오 재생을 시작 하는 유일한 방법은 자동 실행 특성을 사용 하거나 API를 사용 하는 것입니다.

`<video controls ...>` 또는 `{ "controls": true }`

### <a name="autoplay"></a>autoplay ###

자동 실행이 true 이면 페이지가 로드 되는 즉시 비디오가 재생 되기 시작 합니다 (사용자의 상호 작용 없음).

> [!NOTE]
> 이 옵션은 Windows Phone, Apple iOS, Android 등의 모바일 장치에서 지원 되지 않습니다. 모바일 장치는 소비자의 월간 데이터 요금제 사용을 방지 하기 위해 자동 실행 기능을 차단 합니다 (종종 비용이 많이 듭니다.). 이 경우 비디오를 시작 하려면 사용자 터치/클릭이 필요 합니다.

`<video autoplay ...>`디스크나 `{ "autoplay": true }`

### <a name="poster"></a>장 ###
포스터 특성은 비디오 재생을 시작 하기 전에 표시 되는 이미지를 설정 합니다. 이는 종종 비디오 또는 사용자 지정 제목 화면의 프레임입니다. 사용자가 play를 클릭 하는 즉시 이미지는 사라집니다.

`<video poster="myPoster.jpg" ...>` 또는 `{ "poster": "myPoster.jpg" }`

### <a name="width"></a>width ###

Width 특성은 비디오의 표시 너비를 설정 합니다.

`<video width="640" ...>` 또는 `{ "width": 640 }`

### <a name="height"></a>키 ###

Height 특성은 비디오의 표시 높이를 설정 합니다.

`<video height="480" ...>` 또는 `{ "height": 480 }`

### <a name="plugins"></a>plugins ###

플러그 인 JSON은 해당 AMP의 인스턴스를 사용 하 여 로드 된 플러그 인을 확인 하 여 플러그 인에 포함 될 수 있는 옵션을 구성할 수 있습니다.

   `<video... data-setup='{plugins: { "contentTitle": {"name": "Azure Medi Services Overview"}}}'...>`

플러그 인 개발 및 사용에 대 한 자세한 내용은 [플러그 인 작성](azure-media-player-writing-plugins.md) 을 참조 하세요.

### <a name="other-options"></a>기타 옵션 ###

`<video>`JSON을 사용 하는 매개 변수를 사용 하 여 태그에 대해 다른 옵션을 설정할 수 있습니다 `data-setup` .
`<video ... data-setup='{"nativeControlsForTouch": false}'>`

#### <a name="nativecontrolsfortouch"></a>nativeControlsForTouch ####

이는 명시적으로 false로 설정 됩니다. 을 false로 설정 하면 Azure Media Player 스킨이 여러 플랫폼에서 동일 하 게 렌더링 될 수 있습니다.  또한 이름과 달리 터치는 계속 사용할 수 있습니다.

### <a name="fluid"></a>유체 ###

이 옵션을 true video 요소로 설정 하면 부모 컨테이너의 전체 너비가 사용 되며 높이가 표준 16:9 가로 세로 비율을 사용 하는 비디오에 맞게 조정 됩니다.

`<video ... data-setup='{"fluid": true}'>`

`fluid` 옵션은 명시적 `width` 및 `height` 설정 보다 우선 합니다. 이 옵션은 Azure Media Player 버전 이상 에서만 사용할 수 있습니다 `2.0.0` .

### <a name="playbackspeed"></a>playbackSpeed ###

`playbackSpeed` 옵션은 playbackSpeed 컨트롤 및 사용자가 사용할 수 있는 재생 속도 설정 집합을 제어 합니다. `playbackSpeed` 개체를 사용 합니다. 컨트롤 막대에서 재생 속도 제어를 사용 하도록 설정 하려면 `enabled` 개체의 속성을 true로 설정 해야 합니다. 태그에서 재생 속도를 사용 하는 예제:

`<video ... data-setup='{"playbackSpeed": {"enabled": true}}'>`


이 설정의 다른 속성 `playbackSpeed` 은 [PlaybackSpeedOptions](/javascript/api/azuremediaplayer/playbackspeedoptions) 개체에 의해 지정 됩니다.

JavaScript에서 재생 속도 옵션을 설정 하는 예제:

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

이 옵션은 Azure Media Player 버전 2.0.0 이상 에서만 사용할 수 있습니다.

### <a name="staledatatimelimitinsec"></a>staleDataTimeLimitInSec ###

`staleDataTimeLimitInSec`옵션은 mediaSource 버퍼에 유지할 부실 데이터의 최대 시간 (초)을 구성 하는 데 사용할 수 있는 최적화입니다. 이 옵션은 기본적으로 사용하지 않도록 설정되어 있습니다.

### <a name="cea708captionssettings"></a>cea708CaptionsSettings ###

Enabled를 true로 설정 하면 라이브 스트림 및 라이브 보관 파일에 라이브 CEA 캡션을 표시할 수 있습니다. 레이블 특성은 필요 하지 않습니다. 포함 되지 않은 경우 플레이어는 기본 레이블로 대체 됩니다.

```javascript
     cea708CaptionsSettings: {
                enabled: true,
                srclang: 'en',
                label: 'Live CC'
            }
```

이 옵션은 Azure Media Player 버전 2.1.1 이상 에서만 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계 ##

- [Azure Media Player 빠른 시작](azure-media-player-quickstart.md)
