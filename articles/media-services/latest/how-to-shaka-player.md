---
title: Azure Media Services에서 Shaka 플레이어를 사용하는 방법
description: 이 문서에서는 Azure Media Services에서 Shaka 플레이어를 사용하는 방법에 관해 설명합니다.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-js
ms.openlocfilehash: bd180586b863e14953160689ddcc7946c2247e15
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106067846"
---
# <a name="how-to-use-the-shaka-player-with-azure-media-services"></a>Azure Media Services에서 Shaka 플레이어를 사용하는 방법

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

## <a name="overview"></a>개요

Shaka Player는 적응형 미디어를 위한 오픈 소스 JavaScript 라이브러리입니다. 플러그 인 또는 플래시를 사용하지 않고 브라우저에서 적응형 미디어 형식(예: DASH 및 HLS)을 재생합니다. 대신, Shaka Player는 개방형 웹 표준 미디어 원본 확장 및 암호화된 미디어 확장을 사용합니다.

[Mux.js](https://github.com/videojs/mux.js/)를 사용하지 않으면 Shaka 플레이어가 HLS CMAF 형식을 지원하지만 HLS TS는 지원하지 않으므로 사용하는 것이 좋습니다.

공식 설명서는 [Shaka 플레이어 설명서](https://shaka-player-demo.appspot.com/docs/api/tutorial-welcome.html)에서 확인할 수 있습니다.

## <a name="sample-code"></a>샘플 코드

이 문서에 대한 샘플 코드는 [Azure-Samples/media-services-3rdparty-player-samples](https://github.com/Azure-Samples/media-services-3rdparty-player-samples)에서 사용할 수 있습니다.

## <a name="implementing-the-player"></a>플레이어 구현

플레이어의 고유한 인스턴스를 구현해야 하는 경우 다음 지침을 따르세요.

1. 플레이어를 호스트할 `index.html` 파일을 만듭니다. 다음 코드 줄을 추가합니다(해당하는 경우 최신 버전을 바꿀 수 있음).

    ```html
    <html>
      <head>
        <script src="//cdn.jsdelivr.net/npm/shaka-player@3.0.1/dist/shaka-player.compiled.js"></script>
        <script src="//cdn.jsdelivr.net/npm/mux.js@5.6.3/dist/mux.js"></script>
        <script type="module" src="index.js"></script>
      </head>
      <body>
        <video id="video" controls></video>
      </body>
    </html>
    ```

1. 다음 코드를 사용하여 JavaScript 파일을 추가합니다.

    ```javascript
    // myScript.js
    shaka.polyfill.installAll();

    var video = document.getElementById('video');
    var player = new shaka.Player(video);
    window.player = player;

    var manifestUrl = 'https://amsplayeraccount-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/sample-vod.ism/manifest(format=m3u8-aapl)';
    player.load(manifestUrl);
    ```

1. Azure Portal의 스트리밍 로케이터 페이지에서 찾을 수 있는 자산의 스트리밍 로케이터에서 `manifestUrl`을 HLS 또는 DASH URL로 바꿉니다.
    ![스트리밍 로케이터 URL](media/how-to-shaka-player/streaming-urls.png)

1. 서버를 실행하면(예: `npm http-server`) 플레이어가 작동 중이어야 합니다.

## <a name="set-up-captions"></a>자막 설정

### <a name="set-up-vod-captions"></a>VOD 자막 설정

다음 코드 줄을 실행하고 `captionUrl`을 .vtt 디렉터리(CORS 오류를 방지하기 위해 vtt 파일이 동일한 호스트에 있어야 함)로, `lang`을 언어의 두 문자 코드로, `type`을 `caption` 또는 `subtitle`중 하나로 바꿉니다.

```javascript
player.configure('streaming.alwaysStreamText', true)
player.load(manifestUrl).then(function(){
        player.addTextTrack(captionUrl, lang, type, 'text/vtt');
        var tracks = player.getTextTracks();
        player.selectTextTrack(tracks[0]);
});
```

### <a name="set-up-live-stream-captions"></a>라이브 스트림 자막 설정

라이브 스트림에서 자막 사용은 다음 코드 줄을 추가하여 구성됩니다.

```javascript
player.setTextTrackVisibility(true)
```

## <a name="set-up-token-authentication"></a>토큰 인증 설정

다음 코드 줄을 실행하여 `token`을 토큰 문자열로 바꿉니다.

```javascript
player.getNetworkingEngine().registerRequestFilter(function (type, request) {
  if (type === shaka.net.NetworkingEngine.RequestType.LICENSE) {
    request.headers['Authorization'] = 'Bearer ' + token;
  }
});
```

## <a name="set-up-aes-128-encryption"></a>AES-128 암호화 설정

Shaka Player는 현재 AES-128 암호화를 지원하지 않습니다.

이 기능의 상태를 따르는 GitHub [이슈](https://github.com/google/shaka-player/issues/850)에 대한 링크입니다.

## <a name="set-up-drm-protection"></a>DRM 보호 설정

Shaka Player는 사용할 보안 URL이 필요한 EME(암호화된 미디어 확장)를 사용합니다. 따라서 보호된 DRM 콘텐츠를 테스트하려면 HTTPS를 사용해야 합니다. 사이트에서 HTTPS를 사용하는 경우 매니페스트와 모든 세그먼트도 HTTPS를 사용해야 합니다. 이는 혼합된 콘텐츠 요구 사항 때문입니다.

해당 라이선스 서버의 URL에 대한 Shaka 관리 기본 설정 순서:

1. 디버깅에 사용되는 ClearKey 구성은 다른 모든 구성을 재정의해야 합니다. 애플리케이션은 여전히 ClearKey 라이선스 서버를 지정할 수 있습니다.
2. 애플리케이션이 구성된 서버가 있는 경우 매니페스트의 모든 서버를 재정의해야 합니다.
3. 매니페스트 제공 라이선스 서버는 다른 서버가 지정되지 않은 경우에만 사용됩니다.

Widevine 또는 PlayReady의 라이선스 서버 URL을 지정하려면 다음 코드를 사용할 수 있습니다.

```javascript
player.configure({
  drm: {
    servers: {
      "com.widevine.alpha": "YOUR WIDEVINE LICENSE URL",
      "com.microsoft.playready": "YOUR PLAYREADY LICENSE URL"
    }
  }
});

```

모든 FairPlay 콘텐츠에는 서버 인증서를 설정해야 합니다. 서버 인증서는 Player 구성에 설정됩니다.

```javascript
const req = await fetch("YOUR FAIRPLAY CERTIFICATE URL");
const cert = await req.arrayBuffer();
player.configure('drm.advanced.com\\.apple\\.fps\\.1_0.serverCertificate', new Uint8Array(cert));
```

자세한 내용은 [Shaka 플레이어 DRM 보호 설명서](https://shaka-player-demo.appspot.com/docs/api/tutorial-drm-config.html)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* [Azure Media Player 사용](../azure-media-player/azure-media-player-overview.md)
* [빠른 시작: 콘텐츠 암호화](drm-encrypt-content-how-to.md)
