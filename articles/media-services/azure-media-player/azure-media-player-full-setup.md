---
title: Azure 미디어 플레이어 전체 설정
description: Azure 미디어 플레이어를 설정하는 방법에 대해 알아봅니다.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: how-to
ms.date: 04/20/2020
ms.openlocfilehash: d4c2dc58ca341db7ba17dbaf6a5ce7c009983379
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81727243"
---
# <a name="azure-media-player-full-setup"></a>Azure 미디어 플레이어 전체 설정 #

Azure Media Player는 설정하기 쉽습니다. Azure Media Services 계정에서 미디어 콘텐츠를 기본으로 재생하는 데 몇 분밖에 걸리지 않습니다. [샘플은](https://github.com/Azure-Samples/azure-media-player-samples) 릴리스의 샘플 디렉토리에도 제공됩니다.


## <a name="step-1-include-the-javascript-and-css-files-in-the-head-of-your-page"></a>1단계: 페이지 머리에 자바스크립트 및 CSS 파일 포함 ##

Azure Media Player를 사용하면 CDN 호스팅 버전에서 스크립트에 액세스할 수 있습니다. 자바 스크립트를 대신 끝 본문 태그 `<body>` 앞에 `<head>`두는 것이 좋습니다.하지만 Azure Media Player에는 비디오 태그를 유효한 요소로 간주하기 위해 이전 IE 버전의 머리에 있어야하는 'HTML5 Shiv'가 포함되어 있습니다.

> [!NOTE]
> [이미 모더니즈르와](http://modernizr.com/) 같은 HTML5 shiv를 사용하고 있다면 Azure 미디어 플레이어 자바 스크립트를 어디서나 포함 할 수 있습니다. 그러나 모더니즈르의 버전이 비디오에 대한 shiv를 포함했는지 확인하십시오.

### <a name="cdn-version"></a>CDN 버전 ###
    <link href="//amp.azure.net/libs/amp/latest/skins/amp-default/azuremediaplayer.min.css" rel="stylesheet">
    <script src= "//amp.azure.net/libs/amp/latest/azuremediaplayer.min.js"></script>

> [!IMPORTANT]
> 필요에 따라 변경될 `latest` 수 있으므로 프로덕션 환경에서 버전을 **사용해서는** 안 됩니다. Azure `latest` 미디어 플레이어 버전으로 바꿉습니다. 예를 들어 `latest`를 `2.1.1`로 바꿉니다. Azure 미디어 플레이어 버전은 [여기에서](azure-media-player-changelog.md)쿼리할 수 있습니다.

> [!NOTE]
> `1.2.0` 릴리스 이후 더 이상 대체 기술자에 대한 위치를 포함할 필요가 없습니다(azuremediaplayer.min.js 파일의 상대 경로에서 자동으로 위치를 선택합니다). 위의 스크립트 `<head>` 다음에 다음 스크립트를 추가하여 대체 기술의 위치를 수정할 수 있습니다.

> [!NOTE]
> 플래시 및 Silverlight 플러그인의 특성으로 인해 swf 및 xap 파일은 중요한 정보 나 데이터없이 도메인에서 호스팅되어야합니다 .

```javascript
    <script>
      amp.options.flashSS.swf = "//amp.azure.net/libs/amp/latest/techs/StrobeMediaPlayback.2.0.swf"
      amp.options.silverlightSS.xap = "//amp.azure.net/libs/amp/latest/techs/SmoothStreamingPlayer.xap"
    </script>
```

## <a name="step-2-add-an-html5-video-tag-to-your-page"></a>2단계: 페이지에 HTML5 동영상 태그 추가 ##

Azure 미디어 플레이어를 사용하면 HTML5 비디오 태그를 사용하여 비디오를 임베드할 수 있습니다. 그런 다음 Azure Media Player는 태그를 읽고 HTML5 비디오를 지원하는 브라우저뿐만 아니라 모든 브라우저에서 작동하게 합니다. 기본 태그 외에도 Azure 미디어 플레이어에게는 몇 가지 추가 조각이 필요합니다.

1. Azure `<data-setup>` Media `<video>` Player의 특성은 페이지가 준비되면 자동으로 비디오를 설정하고 특성에서 JSON 형식으로 읽도록 지시합니다.
1. `id` 속성: 동일한 페이지의 모든 동영상에 대해 사용하고 고유해야 합니다.
1. 특성에는 `class` 다음 두 클래스가 포함되어 있습니다.
    - `azuremediaplayer`Azure 미디어 플레이어 UI 기능에 필요한 스타일을 적용합니다.
    - `amp-default-skin`기본 스킨을 HTML5 컨트롤에 적용합니다.
1. 두 `<source>` 가지 필수 특성이 포함됩니다.
    - `src`속성은 Azure 미디어 서비스에서 **.ism / 매니페스트* 파일이 추가 될 수 있습니다, Azure 미디어 플레이어는 자동으로 플레이어에 DASH, SMOOTH, HLS에 대한 URL을 추가
    - `type`속성은 스트림의 필수 MIME 유형입니다. *".ism/manifest"와* 연관된 MIME 유형은 *"응용 프로그램/vnd.ms-sstr+xml"입니다.*
1. 암호화 유형(AES 또는 PlayReady, 와이드바인 또는 페어플레이) 및 토큰을 포함하되 이에 국한되지 않는 Azure Media Services의 스트림에 대한 고유한 배달 정책이 있는 경우 Azure Media Player의 *선택적* `<data-setup>` 특성은 `<source>` Azure Media Player에 알려줍니다.

HTML5 비디오와 똑같이 속성, 설정, 소스 및 트랙을 포함/제외합니다.

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin" autoplay controls width="640" height="400" poster="poster.jpg" data-setup='{"techOrder": ["azureHtml5JS", "flashSS", "html5FairPlayHLS","silverlightSS", "html5"], "nativeControlsForTouch": false}'>
        <source src="http://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest" type="application/vnd.ms-sstr+xml" />
        <p class="amp-no-js">
            To view this video please enable JavaScript, and consider upgrading to a web browser that supports HTML5 video
        </p>
    </video>
```

기본적으로 큰 재생 버튼은 왼쪽 상단 모서리에 있으므로 포스터의 흥미로운 부분을 가릴 수 없습니다. 큰 재생 단추의 가운데를 지정하려면 요소에 추가버튼을 `amp-big-play-centered` `class` 추가할 `<video>` 수 있습니다.

### <a name="alternative-setup-for-dynamically-loaded-html"></a>동적으로 로드된 HTML에 대한 대체 설정 ###

웹 페이지 또는 응용 프로그램이 비디오 태그를 동적으로 로드하는 경우(ajax, appendChild 등) 페이지가 로드될 때 존재하지 않을 수 있으므로 데이터 설정 특성에 의존하지 않고 플레이어를 수동으로 설정하는 것이 좋습니다. 이렇게 하려면 먼저 태그에서 데이터 설정 특성을 제거하여 플레이어가 초기화될 때 혼동을 가지지 않도록 합니다. 다음으로 Azure 미디어 플레이어 자바스크립트가 로드된 후 비디오 태그가 DOM에 로드된 후 몇 시간 후에 다음 자바스크립트를 실행합니다.

```javascript
    var myPlayer = amp('vid1', { /* Options */
            techOrder: ["azureHtml5JS", "flashSS", "html5FairPlayHLS","silverlightSS", "html5"],
            "nativeControlsForTouch": false,
            autoplay: false,
            controls: true,
            width: "640",
            height: "400",
            poster: ""
        }, function() {
              console.log('Good to go!');
               // add an event listener
              this.addEventListener('ended', function() {
                console.log('Finished!');
            }
          }
    );
    myPlayer.src([{
        src: "http://samplescdn.origin.mediaservices.windows.net/e0e820ec-f6a2-4ea2-afe3-1eed4e06ab2c/AzureMediaServices_Overview.ism/manifest",
        type: "application/vnd.ms-sstr+xml"
    }]);
```

함수의 `amp` 첫 번째 인수는 비디오 태그의 ID입니다. 직접 교체하십시오.

두 번째 인수는 옵션 개체입니다. 데이터 설정 특성을 사용하여 할 수 있는 것과 같은 추가 옵션을 설정할 수 있습니다.

세 번째 인수는 '준비' 콜백입니다. Azure 미디어 플레이어가 초기화되면 이 함수를 호출합니다. 준비 콜백에서 'this' 개체는 플레이어 인스턴스를 참조합니다.

요소 ID를 사용하는 대신 요소 자체에 대한 참조를 전달할 수도 있습니다.

```javascript

    amp(document.getElementById('example_video_1'), {/*Options*/}, function() {
        //This is functionally the same as the previous example.
    });
    myPlayer.src([{ src: "//example/path/to/myVideo.ism/manifest", type: "application/vnd.ms-sstr+xml"]);
```

## <a name="next-steps"></a>다음 단계 ##

- [Azure 미디어 플레이어 빠른 시작](azure-media-player-quickstart.md)