---
title: Azure Media Player 전체 설정
description: Azure Media Player를 설정하는 방법을 알아봅니다.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: how-to
ms.date: 04/05/2021
ms.custom: devx-track-js
ms.openlocfilehash: d7c103a7eba2e5da7ac040b50fcc0009a0b7237b
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106449871"
---
# <a name="azure-media-player-full-setup"></a>Azure Media Player 전체 설정 #

Azure Media Player는 설정하기 쉽습니다. Azure Media Services 계정에서 바로 미디어 콘텐츠의 기본 재생을 가져오는 데 몇 분밖에 걸리지 않습니다. [샘플](https://github.com/Azure-Samples/azure-media-player-samples)은 릴리스의 samples 디렉터리에도 제공됩니다.

## <a name="step-1-include-the-javascript-and-css-files-in-the-head-of-your-page"></a>1단계: 페이지의 헤드에 JavaScript 및 CSS 파일 포함 ##

Azure Media Player를 사용하여 CDN 호스트된 버전에서 스크립트에 액세스할 수 있습니다. 일반적으로는 `<head>` 대신 끝 본문 태그 `<body>` 앞에 JavaScript를 배치하는 것이 좋지만, Azure Media Player에는 이전 IE 버전이 비디오 태그를 유효한 요소로 준수하기 위해 헤드에 있어야 하는 ‘HTML5 Shiv’가 포함됩니다.

> [!NOTE]
> [Modernizr](https://modernizr.com/)과 같은 HTML5 Shiv를 이미 사용하고 있는 경우 어디서든 Azure Media Player JavaScript를 포함할 수 있습니다. 그러나 Modernizr의 버전에 비디오용 shiv가 포함되어 있는지 확인합니다.

### <a name="cdn-version"></a>CDN 버전 ###

```html
    <link href="//amp.azure.net/libs/amp/latest/skins/amp-default/azuremediaplayer.min.css" rel="stylesheet">
    <script src= "//amp.azure.net/libs/amp/latest/azuremediaplayer.min.js"></script>
```

> [!IMPORTANT]
> 주문형으로 변경될 수 있으므로 프로덕션에 `latest` 버전을 사용하지 **않아야** 합니다. `latest`를 Azure Media Player 버전으로 바꿉니다. 예를 들어 `latest`를 `2.1.1`로 바꿉니다. [여기에서](https://amp.azure.net/libs/amp/latest/docs/changelog.html) Azure Media Player 버전을 쿼리할 수 있습니다.

> [!NOTE]
> `1.2.0` 릴리스부터, 더 이상 대체 기술 위치를 포함할 필요가 없습니다(azuremediaplayer.min.js 파일의 상대 경로에서 위치가 자동으로 선택됨). 위 스크립트 이후 `<head>`에서 다음 스크립트를 추가하여 대체 기술의 위치를 수정할 수 있습니다.

> [!NOTE]
> Flash 및 Silverlight 플러그 인의 특성으로 인해 swf 및 xap 파일은 중요한 정보 또는 데이터 없이 도메인에서 호스트되어야 합니다. 이는 Azure CDN 호스트된 버전을 통해 자동으로 처리됩니다.

```javascript
    <script>
      amp.options.flashSS.swf = "//amp.azure.net/libs/amp/latest/techs/StrobeMediaPlayback.2.0.swf"
      amp.options.silverlightSS.xap = "//amp.azure.net/libs/amp/latest/techs/SmoothStreamingPlayer.xap"
    </script>
```

## <a name="step-2-add-an-html5-video-tag-to-your-page"></a>2단계: 페이지에 HTML5 비디오 태그 추가 ##

Azure Media Player에서는 HTML5 비디오 태그를 사용하여 비디오를 포함할 수 있습니다. 그러면 Azure Media Player는 태그를 읽고 HTML5 비디오를 지원하는 브라우저뿐 아니라 모든 브라우저에서 태그가 작동하도록 합니다. 기본 태그 외에도 Azure Media Player에는 몇 가지 추가 부분이 필요합니다.

1. `<video>`의 `<data-setup>` 특성은 페이지가 준비될 때 자동으로 비디오를 설정하고 특성에서 JSON 형식으로 읽도록 Azure Media Player에 지시합니다.
1. `id` 특성: 동일한 페이지의 모든 비디오에 대해 사용되고 고유해야 합니다.
1. `class` 특성에는 다음 두 가지 클래스가 포함됩니다.
    - `azuremediaplayer`는 Azure Media Player UI 기능에 필요한 스타일을 적용합니다.
    - `amp-default-skin`은 HTML5 컨트롤에 기본 스킨을 적용합니다.
1. `<source>`에는 두 개의 필수 특성이 포함됩니다.
    - `src` 특성에는 추가된 Azure Media Services의 * *.ism/manifest* 파일이 포함될 수 있으며, Azure Media Player는 자동으로 DASH, SMOOTH, HLS의 URL을 플레이어에 추가합니다.
    - `type` 특성은 스트림의 필수 MIME 형식입니다. *“.ism/manifest”* 와 연결된 MIME 형식은 *“application/vnd.ms-sstr+xml”* 입니다.
1. `<source>`의 ‘선택적’ `<data-setup>` 특성은 암호화 유형(AES 또는 PlayReady, Widevine 또는 FairPlay)과 토큰을 포함하되 이에 국한되지 않는 Azure Media Services의 스트림에 대한 고유한 전송 정책이 있는 경우 Azure Media Player에 알립니다.

HTML5 비디오의 경우와 똑같이 특성, 설정, 원본, 트랙을 포함/제외합니다.

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin" autoplay controls width="640" height="400" poster="poster.jpg" data-setup='{"techOrder": ["azureHtml5JS", "flashSS", "html5FairPlayHLS","silverlightSS", "html5"], "nativeControlsForTouch": false}'>
        <source src="http://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest" type="application/vnd.ms-sstr+xml" />
        <p class="amp-no-js">
            To view this video please enable JavaScript, and consider upgrading to a web browser that supports HTML5 video
        </p>
    </video>
```

기본적으로 큰 재생 단추는 왼쪽 위에 있으므로 포스터의 흥미로운 부분을 숨기지 않습니다. 큰 재생 단추를 가운데 맞춤으로 정렬하려는 경우 `<video>` 요소에 `amp-big-play-centered` `class`를 더 추가할 수 있습니다.

### <a name="alternative-setup-for-dynamically-loaded-html"></a>동적으로 로드된 HTML에 대한 대체 설정 ###

웹 페이지 또는 애플리케이션이 비디오 태그를 동적으로 로드하는 경우(ajax, appendChild 등) 페이지가 로드될 때 비디오 태그가 존재하지 않을 수 있도록 data-setup 특성을 사용하는 대신 플레이어를 수동으로 설정하려고 합니다. 이 작업을 위해 먼저 태그에서 data-setup 특성을 제거하면 플레이어가 초기화될 때 혼동되지 않습니다. 다음으로, Azure Media Player JavaScript가 로드되고 비디오 태그가 DOM에 로드된 후 다음 JavaScript를 실행합니다.

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
            });
          }
    );
    myPlayer.src([{
        src: "http://samplescdn.origin.mediaservices.windows.net/e0e820ec-f6a2-4ea2-afe3-1eed4e06ab2c/AzureMediaServices_Overview.ism/manifest",
        type: "application/vnd.ms-sstr+xml"
    }]);
```

`amp` 함수의 첫 번째 인수는 비디오 태그의 ID입니다. 이를 고유한 값으로 바꿉니다.

두 번째 인수는 options 개체입니다. 이를 사용하여 data-setup 특성으로 할 수 있는 것처럼 추가 옵션을 설정할 수 있습니다.

세 번째 인수는 ‘ready’ 콜백입니다. Azure Media Player가 초기화되면 이 함수를 호출합니다. ready 콜백에서 ‘this’ 개체는 플레이어 인스턴스를 참조합니다.

요소 ID를 사용하는 대신 요소 자체에 대한 참조를 전달할 수도 있습니다.

```javascript

    amp(document.getElementById('example_video_1'), {/*Options*/}, function() {
        //This is functionally the same as the previous example.
    });
    myPlayer.src([{ src: "//example/path/to/myVideo.ism/manifest", type: "application/vnd.ms-sstr+xml"]);
```

## <a name="next-steps"></a>다음 단계 ##

- [Azure Media Player 빠른 시작](azure-media-player-quickstart.md)
