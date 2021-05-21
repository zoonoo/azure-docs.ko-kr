---
title: Azure Media Services에서 Video.js 플레이어 사용
description: 이 문서에서는 Azure Media Services에서 HTML 비디오 개체와 JavaScript를 사용하는 방법을 설명합니다.
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
ms.openlocfilehash: 0ac00ecbbe5e0a72bccf6effe5e82fc7d973c91e
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106281406"
---
# <a name="how-to-use-the-videojs-player-with-azure-media-services"></a>Azure Media Services에서 Video.js 플레이어를 사용하는 방법

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

## <a name="overview"></a>개요

Video.js는 HTML5 환경을 위해 작성된 웹 비디오 플레이어입니다. 플러그 인 또는 플래시를 사용하지 않고 브라우저에서 적응형 미디어 형식(예: DASH 및 HLS)을 재생합니다. 대신, Video.js는 개방형 웹 표준 MediaSource 확장 및 암호화된 미디어 확장을 사용합니다. 또한 데스크톱 및 모바일 디바이스에서 비디오 재생을 지원합니다.

공식 설명서는 [https://docs.videojs.com/](https://docs.videojs.com/)에서 확인할 수 있습니다.

## <a name="sample-code"></a>예제 코드
이 문서에 대한 샘플 코드는 [Azure-Samples/media-services-3rdparty-player-samples](https://github.com/Azure-Samples/media-services-3rdparty-player-samples)에서 사용할 수 있습니다.

## <a name="implement-the-player"></a>플레이어 구현

1. 플레이어를 호스트할 `index.html` 파일을 만듭니다. 다음 코드 줄을 추가합니다(해당하는 경우 최신 버전을 바꿀 수 있음).

    ```html
    <html>
      <head>
        <link href="https://vjs.zencdn.net/7.8.2/video-js.css" rel="stylesheet" />
      </head>
      <body>
        <video id="video" class="video-js vjs-default-skin vjs-16-9" controls data-setup="{}">
        </video>

        <script src="https://vjs.zencdn.net/7.8.2/video.js"></script>
        <script src="https://cdn.jsdelivr.net/npm/videojs-contrib-eme@3.7.0/dist/videojs-contrib-eme.min.js"></script>
        <script type="module" src="index.js"></script>
      </body>
    <html>
    ```

2. 다음 코드를 사용하여 `index.js` 파일을 추가합니다.

    ```javascript
    var videoJS = videojs("video");
    videoJS.src({
      src: "manifestUrl",
      type: "protocolType",
    });
    ```

3. Azure Portal의 스트리밍 로케이터 페이지에서 찾을 수 있는 자산의 스트리밍 로케이터에서 `manifestUrl`을 HLS 또는 DASH URL로 바꿉니다.
    ![스트리밍 로케이터 URL](media/player-shaka-player-how-to/streaming-urls.png)

4. `protocolType`을 다음 옵션으로 바꿉니다.

    - HLS 프로토콜용 "application/x-mpegURL"
    - DASH 프로토콜용 "application/dash+xml"

### <a name="set-up-captions"></a>캡션 설정

`addRemoteTextTrack` 메서드를 실행하고 다음을 바꿉니다.

- `subtitleKind`(`"captions"`, `"subtitles"`, `"descriptions"` 또는 `"metadata"` 포함)  
- .vtt 파일 경로가 있는 `caption`(CORS 오류가 발생하지 않도록 vtt 파일이 동일한 호스트에 있어야 함)
- 언어에 대한 BCP 47 코드가 있는 `subtitleLang`(예: 영어의 경우 `"eng"` 또는 스페인어의 경우 `"es"`)
- 원하는 캡션 표시 이름이 있는 `subtitleLabel`

```javascript
videojs.players.video.addRemoteTextTrack({
  kind: subtitleKind,
  src: caption,
  srclang: subtitleLang,
  label: subtitleLabel
});
```

### <a name="set-up-token-authentication"></a>토큰 인증 설정

토큰은 요청 헤더의 인증 필드에 설정해야 합니다. CORS와 관련된 문제를 방지하기 위해 이 토큰은 URL에 `'keydeliver'`가 있는 요청에서만 설정해야 합니다. 다음 코드 줄이 작업을 수행해야 합니다.

```javascript
setupTokenForDecrypt (options) {
  if (options.uri.includes('keydeliver')) {
    options.headers = options.headers || {}
    options.headers.Authorization = 'Bearer=' + this.getInputToken()
  }

  return options
}
```

그런 다음, 위의 함수를 `videojs.Hls.xhr.beforeRequest` 이벤트에 연결해야 합니다.

```javascript
videojs.Hls.xhr.beforeRequest = setupTokenForDecrypt;
```

### <a name="set-up-aes-128-encryption"></a>AES-128 암호화 설정

Video.js는 추가 구성 없이 AES-128 암호화를 지원합니다. 

> [!NOTE] 
> 현재 재생할 수 없는 암호화 및 HLS/DASH CMAF 콘텐츠에 [문제](https://github.com/videojs/video.js/issues/6717)가 있습니다.

### <a name="set-up-drm-protection"></a>DRM 보호 설정

DRM 보호를 지원하려면 [videojs-contrib-eme](https://github.com/videojs/videojs-contrib-eme) 공식 확장을 추가해야 합니다. CDN 버전도 작동합니다.

1. 위에 자세히 설명된 `index.js` 파일에서 비디오 소스를 추가하기 *전* 에 `videoJS.eme();`를 추가하여 EME 확장을 초기화해야 합니다.

   ```javascript
    videoJS.eme();
   ```

2. 이제 다음과 같이 DRM 서비스의 URL과 해당 라이선스의 URL을 정의할 수 있습니다.

   ```javascript
   videoJS.src({
       keySystems: {
           "com.microsoft.playready": "YOUR PLAYREADY LICENSE URL",
           "com.widevine.alpha": "YOUR WIDEVINE LICENSE URL",
           "com.apple.fps.1_0": {
            certificateUri: "YOUR FAIRPLAY CERTIFICATE URL",
            licenseUri: "YOUR FAIRPLAY LICENSE URL"
           }
         }
       })

   ```

#### <a name="acquiring-the-license-url"></a>라이선스 URL 얻기

라이선스 URL을 얻기 위해 다음 작업을 수행할 수 있습니다.

- DRM 공급자 구성을 참조하세요.
- 또는 샘플을 사용하는 경우 VOD용 *setup-vod.ps1* PowerShell 스크립트 또는 라이브 스트림용 *start-live.ps1* 스크립트를 실행할 때 생성된 `output.json` 문서를 참조하세요. 이 파일 내에서 KID를 찾을 수도 있습니다.

#### <a name="using-tokenized-drm"></a>토큰화 DRM 사용

토큰화된 DRM 보호를 지원하려면 플레이어의 `src` 속성에 다음 줄을 추가해야 합니다.

```javascript
videoJS.src({
src: ...,
emeHeaders: {'Authorization': "Bearer=" + "YOUR TOKEN"},
keySystems: {...
```

## <a name="next-steps"></a>다음 단계

- [Azure Media Player 사용](../azure-media-player/azure-media-player-overview.md)  
- [빠른 시작: 콘텐츠 암호화](drm-encrypt-content-how-to.md)
