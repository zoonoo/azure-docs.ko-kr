---
title: Azure Media Player API 메서드
description: Azure Media Player API를 사용 하면 브라우저에서 HTML5 비디오, Flash, Silverlight 또는 지원 되는 기타 재생 기술을 통해 비디오를 재생 하 고 있는지에 관계 없이 JavaScript를 통해 비디오와 상호 작용할 수 있습니다.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/20/2020
ms.openlocfilehash: d1ad0cbe30ce20e61a31f0534a47fe031f6f257b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "81727269"
---
# <a name="api"></a>API #

Azure Media Player API를 사용 하면 브라우저에서 HTML5 비디오, Flash, Silverlight 또는 지원 되는 기타 재생 기술을 통해 비디오를 재생 하 고 있는지에 관계 없이 JavaScript를 통해 비디오와 상호 작용할 수 있습니다.

## <a name="referencing-the-player"></a>플레이어 참조 ##

API 함수를 사용 하려면 player 개체에 액세스할 수 있어야 합니다. 다행히 쉽게 가져올 수 있습니다. 비디오 태그에 ID가 있는지 확인 하기만 하면 됩니다. 예제 embed 코드에는 ID가 `vid1` 있습니다. 한 페이지에 여러 비디오가 있는 경우 모든 비디오 태그에 고유한 ID가 있는지 확인 합니다.

`var myPlayer = amp('vid1');`

> [!NOTE]
> 플레이어를 아직 초기화 하지 않은 경우 데이터 설치 특성 또는 다른 방법을 통해 플레이어도 초기화 됩니다.

## <a name="wait-until-the-player-is-ready"></a>플레이어가 준비 될 때까지 대기 ##

비디오 및 API를 설정 하는 Azure Media Player 소요 되는 시간은 사용 되는 재생 기술에 따라 달라 집니다. HTML5는 Flash 또는 Silverlight 보다 로드 하는 것이 훨씬 빠른 경우가 많습니다. 이러한 이유로 플레이어의 API를 필요로 하는 모든 코드를 트리거하기 위해 플레이어의 ' ready ' 함수를 사용 해야 합니다.

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

이제 준비 된 플레이어에 액세스할 수 있으므로 비디오를 제어 하거나, 값을 가져오거나, 비디오 이벤트에 응답할 수 있습니다. Azure Media Player API 함수 이름은 [HTML5 미디어 API](http://www.whatwg.org/specs/web-apps/current-work/multipage/the-video-element.html)를 따릅니다. 주요 차이점은 getter/setter 함수가 비디오 속성에 사용 된다는 것입니다.

```javacript
    // setting a property on a bare HTML5 video element
    myVideoElement.currentTime = 120;

    // setting a property with Azure Media Player
    myPlayer.currentTime(120);
```

## <a name="registering-for-events"></a>이벤트 등록 ##
처음으로 플레이어를 초기화 한 후 이벤트를 직접 등록 하 여 모든 이벤트가 응용 프로그램에 적절 하 게 보고 되는지 확인 하 고 준비 이벤트 외부에서 수행 해야 합니다.

```javacript
    var myPlayer = amp("vid_1", myOptions, function(){
        //this is the ready function and will only execute after the player is loaded
    });
    myPlayer.addEventListener(amp.eventName.error, _ampEventHandler);
    //add other event listeners
```

## <a name="next-steps"></a>다음 단계 ##

<!---Some context for the following links goes here--->
- [Azure Media Player 빠른 시작](azure-media-player-quickstart.md)