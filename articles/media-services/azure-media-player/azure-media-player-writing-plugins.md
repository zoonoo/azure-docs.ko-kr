---
title: Azure Media Player용 플러그 인 작성
description: JavaScript를 사용하여 Azure Media Player 플러그 인을 작성하는 방법 알아보기
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: how-to
ms.date: 04/05/2021
ms.custom: devx-track-js
ms.openlocfilehash: 7f14be29a33a885bfcef27fa9ef2775e72df16d9
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106449803"
---
# <a name="writing-plugins-for-azure-media-player"></a>Azure Media Player용 플러그 인 작성 #

플러그 인은 플레이어를 확장하거나 향상시키기 위해 작성된 JavaScript입니다. Azure Media Player의 모양, 기능 또는 다른 서비스와 인터페이스하도록 하는 플러그 인을 작성할 수 있습니다. 두 가지 간단한 단계로 이 작업을 수행할 수 있습니다.

## <a name="step-1"></a>1단계 ##

다음과 같이 함수에서 JavaScript를 작성합니다.

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

`<script>` 태그 내의 HTML 페이지 또는 외부 JavaScript 파일에 직접 코드를 작성할 수 있습니다. 후자를 수행하는 경우에는 AMP 스크립트 *뒤* 에 HTML 페이지의 `<head>`에 JavaScript 파일을 포함해야 합니다.

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
다음 두 가지 방법 중 하나로 JavaScript를 사용하여 플러그 인을 초기화합니다.

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

플러그 인 옵션은 필요하지 않습니다. 플러그 인을 사용하는 개발자가 소스 코드를 변경하지 않고도 해당 동작을 구성할 수 있습니다.

플러그 인 생성에 대한 영감과 추가 예제는 [갤러리](azure-media-player-plugin-gallery.md)를 살펴보세요.

>[!NOTE]
> 플러그 인 코드는 뷰어의 플레이어 환경의 수명 동안 DOM의 항목을 동적으로 변경하며 플레이어의 소스 코드를 영구적으로 변경하지 않습니다. 브라우저의 개발자 도구를 이해하는 데 유용합니다. 예를 들어 플레이어에서 요소의 모양을 변경하려는 경우 해당 클래스 이름으로 HTML 요소를 찾은 다음, 해당 요소에서 특성을 추가하거나 변경할 수 있습니다. [HTML 특성을 변경](http://www.w3schools.com/js/js_htmldom_html.asp)하는 데 유용한 리소스는 다음과 같습니다.

### <a name="integrated-plugins"></a>통합 플러그 인 ###

 현재 AMP로 베이킹한 두 개의 플러그 인([시간-팁](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/timetip/example.html) 및 [바로 가기 키](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/hotkeys/example.html))이 있습니다. 이러한 플러그 인은 원래 플레이어를 위한 모듈식 플러그 인으로 개발되었지만 현재 플레이어 소스 코드에 포함되어 있습니다.

### <a name="plugin-gallery"></a>플러그 인 갤러리 ###

[플러그 인 갤러리](https://aka.ms/ampplugins)에는 커뮤니티에서 시계열 마커, 확대/축소, 분석 등의 기능에 이미 제공한 여러 플러그 인이 있습니다. 이 페이지에서는 작동 중인 플러그 인을 보여주는 데모뿐만 아니라 플러그 인에 대한 액세스와 설정 방법에 대한 지침을 제공합니다. 갤러리에 포함시켜야 한다고 생각하는 쿨 플러그 인을 만드는 경우 확인할 수 있도록 자유롭게 제출할 수 있습니다.

## <a name="next-steps"></a>다음 단계 ##

- [Azure Media Player 빠른 시작](azure-media-player-quickstart.md)
