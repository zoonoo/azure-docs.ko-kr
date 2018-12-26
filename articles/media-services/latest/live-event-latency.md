---
title: Azure Media Services의 LiveEvent 대기 시간 | Microsoft Docs
description: 이 토픽에서는 LiveEvent 대기 시간에 대해 간략히 설명하고 짧은 대기 시간을 설정하는 방법을 보여줍니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 11/16/2018
ms.author: juliako
ms.openlocfilehash: a74f2e53127b506f42ff49018c3df2985396646d
ms.sourcegitcommit: eba6841a8b8c3cb78c94afe703d4f83bf0dcab13
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/29/2018
ms.locfileid: "52619822"
---
# <a name="liveevent-latency-in-media-services"></a>Media Services의 LiveEvent 대기 시간

이 문서에서는 **LiveEvent**에서 짧은 대기 시간을 설정하는 방법을 보여줍니다. 또한 다양한 플레이어에서 짧은 대기 시간 설정을 사용하는 경우 표시되는 일반적인 결과에 대해 설명합니다. 그 결과는 CDN 및 네트워크 대기 시간에 따라 다릅니다. 

새로운 **LowLatency** 기능을 사용하려면 **LiveEvent**에서 **StreamOptionsFlag**를 **LowLatency**로 설정할 수 있습니다. 스트림이 실행되면 [Azure Media Player](http://ampdemo.azureedge.net/)(AMP) 데모 페이지를 사용할 수 있으며, 재생 옵션을 설정하여 "짧은 대기 시간 추론 프로필"을 사용할 수 있습니다.

다음 .NET 예제에서는 **LiveEvent**에서 **LowLatency**를 설정하는 방법을 보여줍니다.

```csharp
LiveEvent liveEvent = new LiveEvent(
            location: mediaService.Location, 
            description: "Sample LiveEvent for testing",
            vanityUrl: false,
            encoding: new LiveEventEncoding(
                        // Set this to Basic to enable a transcoding LiveEvent, and None to enable a pass-through LiveEvent
                        encodingType:LiveEventEncodingType.None, 
                        presetName:null
                    ),
            input: new LiveEventInput(LiveEventInputProtocol.RTMP,liveEventInputAccess), 
            preview: liveEventPreview,
            streamOptions: new List<StreamOptionsFlag?>()
            {
                // Set this to Default or Low Latency
                // To use low latency optimally, you should tune your encoder settings down to 1 second GOP size instead of 2 seconds.
                StreamOptionsFlag.LowLatency
            }
        );
```                

전체 예제 [MediaV3LiveApp](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs#L126)을 참조하세요.

## <a name="pass-through-liveevents-latency"></a>통과 LiveEvent 대기 시간

다음 표에서는 Media Services에서 (LowLatency 플래그를 사용하는 경우) 대기 시간에 대한 일반적인 결과를 보여주며 플레이어가 재생을 요청하는 경우 기여도 피드가 서비스에 도달하는 시간으로부터 측정됩니다.

||2초 GOP 짧은 대기 시간 사용|1초 GOP 짧은 대기 시간 사용|
|---|---|---|
|AMP의 DASH|10초|8초|
|기본 iOS 플레이어의 HLS|14초|10초|

> [!NOTE]
> 로컬 네트워크 조건에 따라 또는 CDN 캐싱 계층을 도입하여 엔드투엔드 대기 시간이 달라질 수 있습니다. 정확한 구성을 테스트해야 합니다.

## <a name="next-steps"></a>다음 단계

- [라이브 스트리밍 개요](live-streaming-overview.md)
- [라이브 스트리밍 자습서](stream-live-tutorial-with-api.md)

