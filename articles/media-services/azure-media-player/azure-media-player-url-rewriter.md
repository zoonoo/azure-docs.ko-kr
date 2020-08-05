---
title: Azure Media Player URL 재작성기
description: Azure Media Player는 Azure Media Services에서 지정된 URL을 다시 작성하여 SMOOTH, DASH, HLS v3 및 HLS v4에 대한 스트림을 제공합니다.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/20/2020
ms.custom: devx-track-javascript
ms.openlocfilehash: c8497d50f86155ef7df0de995864e74753542750
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/29/2020
ms.locfileid: "87422626"
---
# <a name="url-rewriter"></a>URL 재작성기 #

기본적으로 Azure Media Player는 Azure Media Services에서 지정된 URL을 다시 작성하여 SMOOTH, DASH, HLS v3 및 HLS v4에 대한 스트림을 제공합니다. 예를 들어 원본이 다음과 같이 지정된 경우 Azure Media Player는 위의 모든 프로토콜을 재생하려고 시도하는지 확인합니다.

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin">
        <source src="//example/path/to/myVideo.ism/manifest" type="application/vnd.ms-sstr+xml" />
    </video>
```

그러나 URL 재작성기를 사용하지 않으려면 매개 변수에 `disableUrlRewriter` 속성을 추가하여 이 작업을 수행할 수 있습니다. 즉, 원본에 전달된 모든 정보는 수정 없이 플레이어에게 직접 전달됩니다.  다음은 DASH 및 하나의 SMOOTH 스트리밍에서 두 개의 원본을 플레이어에 추가하는 예제입니다.

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin">
        <source src="//example/path/to/myVideo.ism/manifest(format=mpd-time-csf)" type="application/dash+xml" data-setup='{"disableUrlRewriter": true}'/>
        <source src="//example/path/to/myVideo.ism/manifest" type="application/vnd.ms-sstr+xml" data-setup='{"disableUrlRewriter": true}'/>
    </video>
```

또는

```javascript
    myPlayer.src([
        { src: "//example/path/to/myVideo.ism/manifest(format=mpd-time-csf)", type: "application/dash+xml", disableUrlRewriter: true },
        { src: "//example/path/to/myVideo.ism/manifest", type: "application/vnd.ms-sstr+xml", disableUrlRewriter: true }
    ]);
```

또한 원하는 경우 Azure Media Player에서 `streamingFormats` 매개 변수를 사용하여 다시 작성할 특정 스트리밍 형식을 지정할 수 있습니다. 옵션에는 `DASH`, `SMOOTH`, `HLSv3`, `HLSv4`, `HLS`가 포함됩니다. HLS와 HLSv3 & v4 간의 차이점은 HLS 형식이 FairPlay 콘텐츠의 재생을 지원한다는 것입니다. v3 및 v4는 FairPlay를 지원하지 않습니다. 이는 사용 가능한 특정 프로토콜에 대한 전달 정책이 없는 경우에 유용합니다.  자산에서 DASH 프로토콜을 사용하도록 설정하지 않은 경우의 예는 다음과 같습니다.

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin">
        <source src="//example/path/to/myVideo.ism/manifest" type="application/vnd.ms-sstr+xml" data-setup='{"streamingFormats": ["SMOOTH", "HLS","HLS-V3", "HLS-V4"] }'/>
    </video>
```

또는

```javascript
    myPlayer.src([
        { src: "//example/path/to/myVideo.ism/manifest", type: "application/vnd.ms-sstr+xml", streamingFormats: ["SMOOTH", "HLS","HLS-V3", "HLS-V4"]},
    ]);
```

위의 두 가지는 특정 자산에 따라 여러 상황에서 서로 조합하여 사용할 수 있습니다.

> [!NOTE]
> Widevine 보호 정보는 DASH 프로토콜에서만 유지됩니다.

## <a name="next-steps"></a>다음 단계 ##

- [Azure Media Player 빠른 시작](azure-media-player-quickstart.md)