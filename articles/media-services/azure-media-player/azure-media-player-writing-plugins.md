---
title: Azure Media Player에 대 한 플러그 인 작성
description: JavaScript를 사용 하 여 Azure Media Player 플러그 인을 작성 하는 방법 알아보기
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: how-to
ms.date: 04/20/2020
ms.openlocfilehash: b7aac80b19a7c30d994f3c14e19047583d5334ac
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81727113"
---
# <a name="writing-plugins-for-azure-media-player"></a>Azure Media Player에 대 한 플러그 인 작성 #

플러그 인은 플레이어를 확장 하거나 향상 시키기 위해 JavaScript로 작성 되었습니다. Azure Media Player의 모양, 해당 기능 또는 다른 서비스와의 인터페이스를 변경 하는 플러그 인을 작성할 수 있습니다. 이 작업은 다음과 같은 두 가지 간단한 단계로 수행할 수 있습니다.

## <a name="step-1"></a>1단계 ##

다음과 같이 함수에서 JavaScript를 작성 합니다.

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

태그 또는 외부 JavaScript 파일 내 `<script>` 에서 HTML 페이지에 직접 코드를 작성할 수 있습니다. 후자를 수행 하는 경우에는 AMP 스크립트 `<head>` *뒤* 에 HTML 페이지의에 JavaScript 파일을 포함 해야 합니다.

예제:

```javascript
    <!--*****START OF Azure Media Player Scripts*****-->
    <script src="//amp.azure.net/libs/amp/latest/azuremediaplayer.min.js"></script>
    <link href="//amp.azure.net/libs/amp/latest/skins/amp-default/azuremediaplayer.min.css" rel="stylesheet">
    <!--*****END OF Azure Media Player Scripts*****-->
    <!--Add Plugins-->
    <script src="yourPluginName.js"></script>
```

## <a name="step-2"></a>2단계 ##
다음 두 가지 방법 중 하나로 JavaScript를 사용 하 여 플러그 인을 초기화 합니다.

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

플러그 인 옵션은 필요 하지 않습니다 .이 옵션을 포함 하면 플러그 인을 사용 하는 개발자가 소스 코드를 변경할 필요 없이 해당 동작을 구성할 수 있습니다.

플러그 인을 만드는 방법에 대 한 자세한 내용을 보려면 [갤러리](azure-media-player-plugin-gallery.md) 를 살펴보세요.

>[!NOTE]
> 플러그 인 코드는 뷰어 플레이어 환경의 수명 동안 DOM의 항목을 동적으로 변경 합니다. 플레이어의 소스 코드를 영구적으로 변경 하지는 않습니다. 브라우저의 개발자 도구를 이해 하는 데 유용 합니다. 예를 들어 플레이어에서 요소의 모양을 변경 하려는 경우 해당 클래스 이름으로 HTML 요소를 찾은 다음 해당 요소에서 특성을 추가 하거나 변경할 수 있습니다. [HTML 특성을 변경](http://www.w3schools.com/js/js_htmldom_html.asp) 하는 데 유용한 리소스는 다음과 같습니다.

### <a name="integrated-plugins"></a>통합 플러그 인 ###

 현재 구운에는 두 개의 플러그 인, 즉 [시간-팁](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/timetip/example.html) 및 [바로 가기 키](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/hotkeys/example.html)가 있습니다. 이러한 플러그 인은 원래 플레이어를 위한 모듈식 플러그인으로 개발 되었지만 이제 플레이어 소스 코드에 포함 되어 있습니다.

### <a name="plugin-gallery"></a>플러그 인 갤러리 ###

[플러그 인 갤러리](http//:aka.ms/ampplugins) 에는 커뮤니티에서 시계열 마커, 확대/축소, 분석 등의 기능에 대해 이미 제공한 여러 플러그 인이 있습니다. 이 페이지에서는 플러그 인에 대 한 액세스 뿐만 아니라 작동 중인 플러그 인을 보여 주는 데모를 설정 하는 방법에 대 한 지침을 제공 합니다. 갤러리에 포함 해야 하는 쿨 플러그 인을 만드는 경우 확인할 수 있도록 자유롭게 제출할 수 있습니다.

## <a name="next-steps"></a>다음 단계 ##

- [Azure Media Player 빠른 시작](azure-media-player-quickstart.md)