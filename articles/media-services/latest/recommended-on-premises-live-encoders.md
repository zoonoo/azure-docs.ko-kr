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
ms.openlocfilehash: 60f9209129c75e329b283045d19b4b5140b40ec2
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/04/2020
ms.locfileid: "78268187"
---
# <a name="recommended-on-premises-live-streaming-encoders"></a>권장 되는 온-프레미스 라이브 스트리밍 인코더

Azure Media Services에서 [라이브 이벤트](https://docs.microsoft.com/rest/api/media/liveevents)(채널)는 라이브 스트리밍 콘텐츠를 처리하기 위한 파이프라인을 나타냅니다. 라이브 이벤트는 다음 두 가지 방법 중 하나로 라이브 입력 스트림을 받습니다.

* 온-프레미스 라이브 인코더가 다중 비트 전송률 RTMP 또는 부드러운 스트리밍(조각난 MP4) 스트림을 Media Services에서 라이브 인코딩을 수행하도록 설정되지 않은 라이브 이벤트로 보냅니다. 수집된 스트림은 어떠한 추가적인 처리 없이 라이브 이벤트를 통과합니다. 이 방법을 **통과**라고 합니다. 라이브 인코더는 클라이언트에 적응 비트 전송률 스트리밍을 허용 하기 위해 단일 비트 전송률 스트림 대신 다중 비트 전송률 스트림을 통과 라이브 이벤트로 보내는 것이 좋습니다. 

    통과 라이브 이벤트에 다중 비트 전송률 스트림을 사용 하는 경우 재생 측에서 예기치 않은 동작이 발생 하지 않도록 비디오 GOP 크기와 다양 한 비트 전송률의 비디오 조각을 동기화 해야 합니다.

  > [!NOTE]
  > 통과 방법은 라이브 스트리밍을 수행하는 가장 경제적인 방법입니다.
 
* 온-프레미스 라이브 인코더는 RTMP 또는 부드러운 스트리밍 (조각화 된 MP4) 형식 중 하나로 Media Services를 사용 하 여 라이브 인코딩을 수행 하도록 설정 된 라이브 이벤트에 단일 비트 전송률 스트림을 보냅니다. 그러면 라이브 이벤트에서 들어오는 단일 비트 전송률 스트림을 다중 비트 전송률(적응) 비디오 스트림으로 라이브 인코딩합니다.

Media Services를 사용하는 라이브 인코딩에 대한 자세한 내용은 [Media Services v3을 사용하는 라이브 스트리밍](live-streaming-overview.md)을 참조하세요.

## <a name="encoder-requirements"></a>인코더 요구 사항

인코더는 HTTPS 또는 RTMPS 프로토콜을 사용 하는 경우 TLS 1.2를 지원 해야 합니다.

## <a name="live-encoders-that-output-rtmp"></a>RTMP를 출력하는 라이브 인코더

Media Services는 RTMP이 출력으로 포함된 다음 라이브 인코더 중 하나를 사용하도록 권장합니다. 지원되는 URL 체계는 `rtmp://` 또는 `rtmps://`입니다.

RTMP를 통해 스트리밍할 때 방화벽 및/또는 프록시 설정을 검사하여 아웃바운드 TCP 포트 1935 및 1936이 열려 있는지 확인합니다.<br/><br/>
RTMPS를 통해 스트리밍할 때 방화벽 및/또는 프록시 설정을 검사하여 아웃바운드 TCP 포트 2935 및 2936이 열려 있는지 확인합니다.

> [!NOTE]
> RTMPS 프로토콜을 사용 하는 경우 인코더는 TLS 1.2를 지원 해야 합니다.

- Adobe Flash Media Live Encoder 3.2
- [Cambria Live 4.3](https://www.capellasystems.net/products/cambria-live/)
- 정령 Live (버전 2.14.15 이상)
- Haivision KB
- Haivision Makito X HEVC
- OBS Studio
- Switcher Studio(iOS)
- Telestream Wirecast (TLS 1.2 요구 사항으로 인해 버전 13.0.2 이상)
- Telestream Wirecast S (RTMP만 지원 됨)
- Teradek Slice 756
- TriCaster 8000
- Tricaster Mini HD-4
- VMIX
- xStream
- [Ffmpeg](https://www.ffmpeg.org)
- [GoPro](https://gopro.com/help/articles/block/getting-started-with-live-streaming) 영웅 7 및 영웅 8
- [Restream.io](https://restream.io/)

## <a name="live-encoders-that-output-fragmented-mp4"></a>조각난 MP4를 출력하는 라이브 인코더

Media Services는 다중 비트 전송률 부드러운 스트리밍(조각난 MP4)을 출력으로 제공하는 다음 라이브 인코더 중 하나를 사용하도록 권장합니다. 지원되는 URL 체계는 `http://` 또는 `https://`입니다.

> [!NOTE]
> 인코더는 HTTPS 프로토콜을 사용 하는 경우 TLS 1.2를 지원 해야 합니다.

- Ateme TITAN Live
- Cisco Digital Media Encoder 2200
- 정령 Live (TLS 1.2 요구 사항으로 인해 버전 2.14.15 이상)
- Envivio 4Caster C4 Gen III 
- Imagine Communications Selenio MCP3
- Media Excel Hero Live 및 Hero 4K(UHD/HEVC)
- [Ffmpeg](https://www.ffmpeg.org)

> [!TIP]
>  라이브 이벤트를 여러 언어로 스트리밍하는 경우 (예: 하나의 영어 오디오 트랙 및 하나의 스페인어 오디오 트랙) 라이브 피드를 통과 라이브 이벤트로 보내도록 구성 된 미디어 Excel 라이브 인코더를 사용 하 여이를 수행할 수 있습니다.

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

## <a name="see-also"></a>참고 항목

[온-프레미스 인코더 파트너가 됩니다.](become-on-premises-encoder-partner.md)

## <a name="next-steps"></a>다음 단계

[Media Services v3을 통한 라이브 스트리밍](live-streaming-overview.md)
