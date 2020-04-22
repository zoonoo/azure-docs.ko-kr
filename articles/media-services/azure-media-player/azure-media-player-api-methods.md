---
title: Azure 미디어 플레이어 API 메서드
description: Azure Media Player API를 사용하면 브라우저가 HTML5 비디오, 플래시, Silverlight 또는 기타 지원되는 재생 기술을 통해 비디오를 재생하는지 여부에 관계없이 JavaScript를 통해 비디오와 상호 작용할 수 있습니다.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/20/2020
ms.openlocfilehash: d1ad0cbe30ce20e61a31f0534a47fe031f6f257b
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81727269"
---
# <a name="api"></a>API #

Azure Media Player API를 사용하면 브라우저가 HTML5 비디오, 플래시, Silverlight 또는 기타 지원되는 재생 기술을 통해 비디오를 재생하는지 여부에 관계없이 JavaScript를 통해 비디오와 상호 작용할 수 있습니다.

## <a name="referencing-the-player"></a>플레이어 참조 ##

API 함수를 사용하려면 플레이어 개체에 액세스해야 합니다. 다행히 쉽게 얻을 수 있습니다. 동영상 태그에 ID가 있는지 확인하기만 하면 됩니다. 예제 포함 코드에는 의 `vid1`ID가 있습니다. 한 페이지에 동영상이 여러 개 있는 경우 모든 동영상 태그에 고유 ID가 있는지 확인합니다.

`var myPlayer = amp('vid1');`

> [!NOTE]
> 플레이어가 데이터 설정 속성 이나 다른 방법을 통해 아직 초기화 되지 않은 경우, 이것은 또한 플레이어를 초기화 됩니다.

## <a name="wait-until-the-player-is-ready"></a>플레이어가 준비될 때까지 기다립니다. ##

Azure Media Player에서 비디오 및 API를 설정하는 데 걸리는 시간은 사용 중인 재생 기술에 따라 다릅니다. HTML5는 플래시 나 실버 라이트보다로드속도가 훨씬 빠릅니다. 이러한 이유로 플레이어의 '준비' 함수를 사용하여 플레이어의 API가 필요한 코드를 트리거해야 합니다.

```javacript
    amp("vid_1").ready(function(){
      var myPlayer = this;

      // EXAMPLE: Start playing the video.
      myPlayer.play();
    });
```

또는

```javacript
    var myPlayer = amp("vid_1", myOptions, function(){
        //this is the ready function and will only execute after the player is loaded
    });
```

## <a name="api-methods"></a>API 메서드 ##

이제 준비된 플레이어에 액세스할 수 있으므로 비디오를 제어하거나 값을 얻거나 비디오 이벤트에 응답할 수 있습니다. Azure 미디어 플레이어 API 함수 이름은 [HTML5 미디어 API를](http://www.whatwg.org/specs/web-apps/current-work/multipage/the-video-element.html)따르려고 시도합니다. 주요 차이점은 getter/setter 함수가 비디오 속성에 사용된다는 것입니다.

```javacript
    // setting a property on a bare HTML5 video element
    myVideoElement.currentTime = 120;

    // setting a property with Azure Media Player
    myPlayer.currentTime(120);
```

## <a name="registering-for-events"></a>이벤트 등록 ##
모든 이벤트가 응용 프로그램에 적절하게 보고되도록 플레이어를 처음으로 초기화한 후 이벤트를 직접 등록해야 하며, 준비된 이벤트 외부에서 수행해야 합니다.

```javacript
    var myPlayer = amp("vid_1", myOptions, function(){
        //this is the ready function and will only execute after the player is loaded
    });
    myPlayer.addEventListener(amp.eventName.error, _ampEventHandler);
    //add other event listeners
```

## <a name="next-steps"></a>다음 단계 ##

<!---Some context for the following links goes here--->
- [Azure 미디어 플레이어 빠른 시작](azure-media-player-quickstart.md)