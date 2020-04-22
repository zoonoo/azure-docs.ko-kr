---
title: Azure 미디어 플레이어에 대 한 플러그인 쓰기
description: 자바 스크립트와 Azure 미디어 플레이어와 플러그인을 작성하는 방법에 대해 알아보기
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: how-to
ms.date: 04/20/2020
ms.openlocfilehash: b7aac80b19a7c30d994f3c14e19047583d5334ac
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81727113"
---
# <a name="writing-plugins-for-azure-media-player"></a>Azure 미디어 플레이어에 대 한 플러그인 쓰기 #

플러그인은 플레이어를 확장하거나 향상시키기 위해 작성된 자바스크립트입니다. Azure Media Player의 모양, 기능 또는 다른 서비스와 인터페이스를 변경하는 플러그인을 작성할 수 있습니다. 다음 두 단계로 이 작업을 수행할 수 있습니다.

## <a name="step-1"></a>1단계 ##

다음과 같은 함수에 자바 스크립트를 작성하십시오.

```javascript

    (function () {
        amp.plugin('yourPluginName', function (options) {
        var myPlayer = this;
           myPlayer.addEventListener(amp.eventName.ready, function () {
        console.log("player is ready!");
            });
        });
    }).call(this);
```

태그 내의 `<script>` HTML 페이지 나 외부 자바 스크립트 파일에 직접 코드를 작성할 수 있습니다. 후자를 수행하는 경우 AMP 스크립트 `<head>` *다음의* HTML 페이지에 자바 스크립트 파일을 포함해야 합니다.

예:

```javascript
    <!--*****START OF Azure Media Player Scripts*****-->
    <script src="//amp.azure.net/libs/amp/latest/azuremediaplayer.min.js"></script>
    <link href="//amp.azure.net/libs/amp/latest/skins/amp-default/azuremediaplayer.min.css" rel="stylesheet">
    <!--*****END OF Azure Media Player Scripts*****-->
    <!--Add Plugins-->
    <script src="yourPluginName.js"></script>
```

## <a name="step-2"></a>2단계 ##
두 가지 방법 중 하나로 자바 스크립트와 플러그인을 초기화 :

방법 1:

```javascript
    var myOptions = {
        autoplay: true,
        controls: true,
        width: "640",
        height: "400",
        poster: "",
        plugins: {
            yourPluginName: {
                [your plugin options]: [example options]
           }
        }
    };     
    var myPlayer = amp([videotag id], myOptions);
```

방법 2

```javascript
    var video = amp([videotag id]);
    video.yourPluginName({[your plugins option]: [example option]});
```

플러그인 옵션은 필요하지 않습니다, 그들을 포함하여 단지 소스 코드를 변경하지 않고 그 동작을 구성하기 위해 플러그인을 사용하는 개발자를 허용.

플러그인을 만드는 방법에 대한 영감과 더 많은 예제를 보려면 [갤러리를](azure-media-player-plugin-gallery.md) 살펴보십시오.

>[!NOTE]
> 플러그인 코드는 시청자의 플레이어 경험 의 수명 동안 DOM의 항목을 동적으로 변경, 그것은 결코 플레이어의 소스 코드를 영구적으로 변경하지 않습니다. 브라우저의 개발자 도구에 대한 이해가 유용한 곳입니다. 예를 들어 플레이어에서 요소의 모양을 변경하려는 경우 해당 클래스 이름으로 HTML 요소를 찾은 다음 거기에서 속성을 추가하거나 변경할 수 있습니다. HTML [특성 변경에](http://www.w3schools.com/js/js_htmldom_html.asp) 대한 훌륭한 리소스는 다음과 같습니다.

### <a name="integrated-plugins"></a>통합 플러그인 ###

 현재 AMP에 구운 두 개의 플러그인이 있습니다: [시간 팁과](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/timetip/example.html) [단축키.](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/hotkeys/example.html) 이 플러그인은 원래 플레이어를위한 모듈 형 플러그인으로 개발되었지만 지금은 플레이어 소스 코드에 포함되어 있습니다.

### <a name="plugin-gallery"></a>플러그인 갤러리 ###

[플러그인 갤러리에는](http//:aka.ms/ampplugins) 커뮤니티가 이미 타임 라인 마커, 줌, 분석 등의 기능에 기여한 여러 플러그인이 있습니다. 이 페이지는 플러그인에 대한 액세스 및 플러그인을 설정하는 방법에 대한 지침뿐만 아니라 플러그인이 작동하는 것을 보여주는 데모를 제공합니다. 당신은 당신이 우리의 갤러리에 포함되어야한다고 생각하는 멋진 플러그인을 만들 경우, 우리가 그것을 체크 아웃 할 수 있도록 그것을 제출 주시기 바랍니다.

## <a name="next-steps"></a>다음 단계 ##

- [Azure 미디어 플레이어 빠른 시작](azure-media-player-quickstart.md)