---
title: Media Services에서 권장하는 라이브 스트리밍 인코더 - Azure | Microsoft Docs
description: Media Services에서 권장하는 라이브 스트리밍 온-프레미스 인코더에 대한 자세한 정보
services: media-services
keywords: 인코딩, 인코더, 미디어
author: johndeu
manager: johndeu
ms.author: johndeu
ms.date: 02/10/2020
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 5e16f1fb948ddb435c5002c16125b36fa61d50a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336240"
---
# <a name="tested-on-premises-live-streaming-encoders"></a>테스트된 온-프레미스 라이브 스트리밍 인코더

Azure Media Services에서 [라이브 이벤트](https://docs.microsoft.com/rest/api/media/liveevents)(채널)는 라이브 스트리밍 콘텐츠를 처리하기 위한 파이프라인을 나타냅니다. 라이브 이벤트는 다음 두 가지 방법 중 하나로 라이브 입력 스트림을 받습니다.

* 온-프레미스 라이브 인코더가 다중 비트 전송률 RTMP 또는 부드러운 스트리밍(조각난 MP4) 스트림을 Media Services에서 라이브 인코딩을 수행하도록 설정되지 않은 라이브 이벤트로 보냅니다. 수집된 스트림은 어떠한 추가적인 처리 없이 라이브 이벤트를 통과합니다. 이 방법을 **통과**라고 합니다. 라이브 인코더는 단일 비트레이트 스트림 대신 다중 비트레이트 스트림을 통과 라이브 이벤트로 전송하여 클라이언트에 적응형 비트 전송률 스트리밍을 허용하는 것이 좋습니다. 

    통과 라이브 이벤트에 다중 비트 레이트 스트림을 사용하는 경우 재생 측에서 예기치 않은 동작을 피하기 위해 서로 다른 비트 레이트에 있는 비디오 GOP 크기와 비디오 조각을 동기화해야 합니다.

  > [!TIP]
  > 통과 방법은 라이브 스트리밍을 수행하는 가장 경제적인 방법입니다.
 
* 온-프레미스 라이브 인코더는 RTMP 또는 스무딩 스트리밍(조각화된 MP4) 중 하나로 미디어 서비스를 사용하여 라이브 인코딩을 수행할 수 있는 단일 비트레이트 스트림을 라이브 이벤트로 보냅니다. 그러면 라이브 이벤트에서 들어오는 단일 비트 전송률 스트림을 다중 비트 전송률(적응) 비디오 스트림으로 라이브 인코딩합니다.

이 문서에서는 테스트된 온-프레미스 라이브 스트리밍 인코더에 대해 설명합니다. 온-프레미스 라이브 인코더를 확인하는 방법에 대한 지침은 [온-프레미스 인코더 확인을 참조하세요.](become-on-premises-encoder-partner.md)

Media Services를 사용하는 라이브 인코딩에 대한 자세한 내용은 [Media Services v3을 사용하는 라이브 스트리밍](live-streaming-overview.md)을 참조하세요.

## <a name="encoder-requirements"></a>인코더 요구 사항

인코더는 HTTPS 또는 RTMPS 프로토콜을 사용할 때 TLS 1.2를 지원해야 합니다.

## <a name="live-encoders-that-output-rtmp"></a>RTMP를 출력하는 라이브 인코더

Media Services는 RTMP이 출력으로 포함된 다음 라이브 인코더 중 하나를 사용하도록 권장합니다. 지원되는 URL 체계는 `rtmp://` 또는 `rtmps://`입니다.

RTMP를 통해 스트리밍할 때 방화벽 및/또는 프록시 설정을 검사하여 아웃바운드 TCP 포트 1935 및 1936이 열려 있는지 확인합니다.<br/><br/>
RTMPS를 통해 스트리밍할 때 방화벽 및/또는 프록시 설정을 검사하여 아웃바운드 TCP 포트 2935 및 2936이 열려 있는지 확인합니다.

> [!NOTE]
> 인코더는 RTMPS 프로토콜을 사용할 때 TLS 1.2를 지원해야 합니다.

- Adobe Flash Media Live Encoder 3.2
- [캄브리아 라이브 4.3](https://www.capellasystems.net/products/cambria-live/)
- 정령 라이브 (버전 2.14.15 이상)
- Haivision KB
- Haivision Makito X HEVC
- OBS Studio
- Switcher Studio(iOS)
- 텔레스트림 와이어캐스트(TLS 1.2 요구 사항으로 인해 버전 13.0.2 이상)
- 텔레스트림 와이어캐스트 S(RTMP만 지원)
- Teradek Slice 756
- TriCaster 8000
- Tricaster Mini HD-4
- VMIX
- xStream
- [Ffmpeg](https://www.ffmpeg.org)
- [고프로 (미국)](https://gopro.com/help/articles/block/getting-started-with-live-streaming) 영웅 7과 영웅 8
- [Restream.io](https://restream.io/)

## <a name="live-encoders-that-output-fragmented-mp4"></a>조각난 MP4를 출력하는 라이브 인코더

Media Services는 다중 비트 전송률 부드러운 스트리밍(조각난 MP4)을 출력으로 제공하는 다음 라이브 인코더 중 하나를 사용하도록 권장합니다. 지원되는 URL 체계는 `http://` 또는 `https://`입니다.

> [!NOTE]
> 인코더는 HTTPS 프로토콜을 사용할 때 TLS 1.2를 지원해야 합니다.

- Ateme TITAN Live
- Cisco Digital Media Encoder 2200
- 정령 라이브 (버전 2.14.15 이상 으로 인해 TLS 1.2 요구 사항)
- Envivio 4Caster C4 Gen III 
- Imagine Communications Selenio MCP3
- Media Excel Hero Live 및 Hero 4K(UHD/HEVC)
- [Ffmpeg](https://www.ffmpeg.org)

> [!TIP]
>  여러 언어로 라이브 이벤트를 스트리밍하는 경우(예: 영어 오디오 트랙 1개 및 스페인어 오디오 트랙 1개) 라이브 피드를 통과 라이브 이벤트로 전송하도록 구성된 Media Excel 라이브 인코더를 사용하여 이 작업을 수행할 수 있습니다.

## <a name="configuring-on-premises-live-encoder-settings"></a>온-프레미스 라이브 인코더 설정 구성

라이브 이벤트 유형에 유효한 설정에 대한 자세한 내용은 [라이브 이벤트 유형 비교](live-event-types-comparison.md)를 참조하세요.

### <a name="playback-requirements"></a>재생 요구 사항

콘텐츠를 재생하려면 오디오 및 비디오 스트림이 둘 다 있어야 합니다. 비디오 전용 스트림의 재생은 지원되지 않습니다.

### <a name="configuration-tips"></a>구성 팁

- 가능하면 하드웨어에 내장된 인터넷 연결을 사용합니다.
- 대역폭 요구 사항을 결정할 때는 스트리밍 비트 전송률의 2배를 지정합니다. 필수는 아니지만, 이 간단한 규칙은 네트워크 정체의 영향을 완화하는 데 도움이 됩니다.
- 소프트웨어 기반 인코더를 사용하는 경우 불필요한 프로그램을 모두 닫습니다.
- 푸시를 시작한 후에 인코더 구성을 변경하면 이벤트에 부정적인 영향을 줍니다. 구성 변경으로 인해 이벤트가 불안정해질 수 있습니다. 
- 이벤트를 설정할 수 있는 충분한 시간을 확보합니다. 대규모 이벤트의 경우 이벤트보다 1시간 전에 설정을 시작하는 것이 좋습니다.
- H.264 비디오 및 AAC 오디오 코덱 출력을 사용합니다.
- 비디오 품질 간에 키 프레임 또는 GOP 시간정렬이 있는지 확인합니다.
- 각 비디오 품질에 대해 고유한 스트림 이름이 있는지 확인합니다.
- 최적의 적응 형 비트 레이트 성능을 위해 권장되는 엄격한 CBR 인코딩을 사용합니다.

> [!IMPORTANT]
> 클라우드에 조각을 업로드하는 CPU및 IO 작업을 포함으로 기계 (CPU / 메모리 / 등)의 물리적 상태를보십시오. 인코더의 설정을 변경하는 경우 변경 사항이 적용될 채널/라이브 이벤트를 특정 재설정해야 합니다.

## <a name="see-also"></a>참조

[Media Services v3을 통한 라이브 스트리밍](live-streaming-overview.md)

## <a name="next-steps"></a>다음 단계

[인코더 를 확인하는 방법](become-on-premises-encoder-partner.md)
