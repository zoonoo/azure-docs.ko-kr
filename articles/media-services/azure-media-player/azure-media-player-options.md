---
title: Azure 미디어 플레이어 옵션
description: Azure Media Player 포함 코드는 단순히 HTML5 비디오 태그이므로 많은 옵션에서 표준 태그 특성을 사용하여 옵션을 설정할 수 있습니다.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/20/2020
ms.openlocfilehash: e26215115b4c4484e5e05a2fd94a4d2c6680a4d0
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81727165"
---
# <a name="options"></a>옵션 #

## <a name="setting-options"></a>설정 옵션 ##

Azure Media Player 포함 코드는 단순히 HTML5 비디오 태그이므로 많은 옵션에서 표준 태그 특성을 사용하여 옵션을 설정할 수 있습니다.

`<video controls autoplay ...>`

또는 데이터 설정 특성을 사용하여 [JSON](http://json.org/example.html) 형식으로 옵션을 제공할 수 있습니다. 또한 비디오 태그에 표준이 아닌 옵션을 설정하는 방법도 마찬가지입니다.

`<video data-setup='{ "controls": true, "autoplay": false }'...>`

마지막으로 데이터 설정 특성을 사용하여 플레이어 설정을 트리거하지 않는 경우 JavaScript 설치 기능의 두 번째 인수로 플레이어 옵션을 사용하여 개체를 전달할 수 있습니다.

`amp("vid1", { "controls": true, "autoplay": false });`

> [!NOTE]
> 생성자의 옵션은 소스가 설정되기 전에 첫 번째 초기화에만 설정됩니다.  동일한 초기화 된 Azure Media Player 요소에서 옵션을 수정하려면 소스를 변경하기 전에 옵션을 업데이트해야합니다. 을 사용하여 `myPlayer.options({/*updated options*/});`JavaScript의 옵션을 업데이트할 수 있습니다. 변경된 옵션만 영향을 주며 이전에 설정한 다른 모든 옵션은 유지됩니다.

## <a name="individual-options"></a>개별 옵션 ##

> [!NOTE]
>동영상 태그 속성은 참 또는 거짓(부울)일 뿐이며, 속성(등가 기호 없음)을 포함하면 켜거나 제외하여 해제할 수 있습니다. 예를 들어 컨트롤을 켜려면: `<video controls ...>` RIGHT `<video controls="true" ...>` 사람들이 실행하는 가장 큰 문제는 실제로 반대를 수행하고 특성이 여전히 포함되어 있기 때문에 true로 값을 설정하는 값 (예 : controls="false")으로 false를 사용하여 이러한 값을 false로 설정하려고합니다.

### <a name="controls"></a>controls ###

컨트롤 옵션은 플레이어가 사용자가 상호 작용할 수 있는 컨트롤을 가지고 있는지 여부를 설정합니다. 컨트롤이 없으면 비디오 재생을 시작하는 유일한 방법은 자동 재생 특성또는 API를 통해서입니다.

`<video controls ...>` 또는 `{ "controls": true }`

### <a name="autoplay"></a>autoplay ###

자동 재생이 true이면 페이지가 로드되는 즉시 비디오 재생이 시작됩니다(사용자와의 상호 작용 없이).

> [!NOTE]
> 이 옵션은 Windows Phone, Apple iOS 및 Android와 같은 모바일 장치에서는 지원되지 않습니다. 모바일 장치는 자동 재생 기능을 차단하여 소비자의 월별 데이터 요금제(종종 비용이 많이 드는)의 과다 사용을 방지합니다. 이 경우 비디오를 시작하려면 사용자 터치/클릭이 필요합니다.

`<video autoplay ...>`또는`{ "autoplay": true }`

### <a name="poster"></a>포스터 ###
포스터 속성은 비디오 재생을 시작하기 전에 표시되는 이미지를 설정합니다. 이것은 종종 비디오 또는 사용자 지정 제목 화면의 프레임입니다. 사용자가 재생을 클릭하면 이미지가 사라집니다.

`<video poster="myPoster.jpg" ...>` 또는 `{ "poster": "myPoster.jpg" }`

### <a name="width"></a>width ###

너비 특성은 비디오의 표시 너비를 설정합니다.

`<video width="640" ...>` 또는 `{ "width": 640 }`

### <a name="height"></a>height ###

높이 특성은 비디오의 표시 높이를 설정합니다.

`<video height="480" ...>` 또는 `{ "height": 480 }`

### <a name="plugins"></a>plugins ###

플러그인 JSON은 플러그인이 있을 수 있는 모든 옵션을 구성할 수 있는 AMP의 인스턴스와 함께 로드 되는 플러그인을 결정 합니다.

   `<video... data-setup='{plugins: { "contentTitle": {"name": "Azure Medi Services Overview"}}}'...>`

플러그인 개발 및 사용에 대한 자세한 내용은 [플러그인 작성을](azure-media-player-writing-plugins.md) 참조하십시오.

### <a name="other-options"></a>기타 옵션 ###

JSON을 사용하는 매개 `<video>` 변수를 `data-setup` 사용하여 태그에 다른 옵션을 설정할 수 있습니다.
`<video ... data-setup='{"nativeControlsForTouch": false}'>`

#### <a name="nativecontrolsfortouch"></a>네이티브컨트롤포터치 ####

이것은 명시적으로 false로 설정됩니다. false로 설정하면 Azure Media Player 스킨을 플랫폼 간에 동일하게 렌더링할 수 있습니다.  또한 이름과는 달리 터치는 계속 활성화됩니다.

### <a name="fluid"></a>유체 ###

이 옵션을 실제 비디오 요소로 설정하면 부모 컨테이너의 전체 너비가 유지되고 표준 16:9 종횡비의 비디오에 맞게 높이가 조정됩니다.

`<video ... data-setup='{"fluid": true}'>`

`fluid`옵션은 명시적 `width` `height` 및 설정을 재정의합니다. 이 옵션은 Azure Media Player `2.0.0` 버전 이상에서만 사용할 수 있습니다.

### <a name="playbackspeed"></a>재생 속도 ###

`playbackSpeed`옵션은 재생속도 제어 및 사용자가 사용할 수 있는 재생 속도 설정 세트를 제어합니다. `playbackSpeed`개체를 차지합니다. 컨트롤 막대에서 재생 속도 제어를 사용하려면 `enabled` 개체의 속성을 true로 설정해야 합니다. 태그에서 재생 속도를 활성화하는 예:

`<video ... data-setup='{"playbackSpeed": {"enabled": true}}'>`


`playbackSpeed` 설정의 다른 속성은 [재생속도옵션](https://docs.microsoft.com/javascript/api/azuremediaplayer/amp.player.playbackspeedoptions) 개체에 의해 제공됩니다.

자바 스크립트에서 재생 속도 옵션을 설정의 예 :

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

### <a name="staledatatimelimitinsec"></a>부실데이터타임제한인섹 ###

이 `staleDataTimeLimitInSec` 옵션은 mediaSource 버퍼에 보관할 오래된 데이터의 몇 초 분량을 구성할 수 있는 최적화입니다. 이 옵션은 기본적으로 사용하지 않도록 설정되어 있습니다.

### <a name="cea708captionssettings"></a>cea708캡션설정 ###

true로 설정하면 라이브 스트림 및 라이브 아카이브에 라이브 CEA 캡션을 표시할 수 있습니다. 플레이어가 기본 레이블로 대체되지 않으면 레이블 특성이 필요하지 않습니다.

```javascript
     cea708CaptionsSettings: {
                enabled: true,
                srclang: 'en',
                label: 'Live CC'
            }
```

이 옵션은 Azure Media Player 버전 2.1.1 이상에서만 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계 ##

- [Azure 미디어 플레이어 빠른 시작](azure-media-player-quickstart.md)