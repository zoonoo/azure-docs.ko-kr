---
title: Media Services에서 권장하는 라이브 스트리밍 온-프레미스 인코더에 대한 자세한 정보 - Azure | Microsoft Docs
description: Media Services에서 권장하는 라이브 스트리밍 온-프레미스 인코더에 대한 자세한 정보
services: media-services
keywords: 인코딩, 인코더, 미디어
author: johndeu
manager: johndeu
ms.author: johndeu
ms.date: 12/14/2018
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 4d25e4cd94fec35f31594544b619aa054a35d58d
ms.sourcegitcommit: 70471c4febc7835e643207420e515b6436235d29
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/15/2019
ms.locfileid: "54302343"
---
# <a name="recommended-live-streaming-encoders"></a>권장 라이브 스트리밍 인코더

Media Services에서 [LiveEvent](https://docs.microsoft.com/rest/api/media/liveevents)(채널)는 라이브 스트리밍 콘텐츠를 처리하기 위한 파이프라인을 나타냅니다. LiveEvent는 다음 두 가지 방법 중 하나로 라이브 입력 스트림을 받습니다.

* 온-프레미스 라이브 인코더가 다중 비트 전송률 RTMP 또는 부드러운 스트리밍(조각난 MP4) 스트림을 Media Services에서 라이브 인코딩을 수행하도록 설정되지 않은 LiveEvent로 보냅니다. 수집된 스트림은 어떠한 추가적인 처리 없이 LiveEvent를 통과합니다. 이 방법을 **통과**라고 합니다. 라이브 인코더는 통과 채널에 단일 비트 전송률 스트림을 전송할 수 있지만 이 구성에서는 클라이언트에 대한 적응 비트 전송률 스트리밍이 허용되지 않으므로 이 구성은 권장되지 않습니다.

  > [!NOTE]
  > 통과 방법은 라이브 스트리밍을 수행하는 가장 경제적인 방법입니다.

* 온-프레미스 라이브 인코더는 단일 비트 전송률 스트림을 RTMP 또는 부드러운 스트리밍(조각난 MP4) 형식의 하나로 Media Services를 통해 라이브 인코딩을 수행할 수 있는 LiveEvent에 전송합니다. 그러면 LiveEvent는 들어오는 단일 비트 전송률 스트림을 다중 비트 전송률(적응) 비디오 스트림으로 라이브 인코딩합니다.

Media Services를 사용하는 라이브 인코딩에 대한 자세한 내용은 [Media Services v3을 사용하는 라이브 스트리밍](live-streaming-overview.md)을 참조하세요.

## <a name="live-encoders-that-output-rtmp"></a>RTMP를 출력하는 라이브 인코더

Media Services는 RTMP이 출력으로 포함된 다음 라이브 인코더 중 하나를 사용하도록 권장합니다. 지원되는 URL 체계는 `rtmp://` 또는 `rtmps://`입니다.

> [!NOTE]
 > RTMP를 통해 스트리밍할 때 방화벽 및/또는 프록시 설정을 검사하여 아웃바운드 TCP 포트 1935 및 1936이 열려 있는지 확인합니다.<br/>
 RTMPS를 통해 스트리밍할 때 방화벽 및/또는 프록시 설정을 검사하여 아웃바운드 TCP 포트 2935 및 2936이 열려 있는지 확인합니다.

- Adobe Flash Media Live Encoder 3.2
- Haivision KB
- Haivision Makito X HEVC
- OBS Studio
- Switcher Studio(iOS)
- Telestream Wirecast 8.1+
- Telestream Wirecast S
- Teradek Slice 756
- TriCaster 8000
- Tricaster Mini HD-4
- VMIX
- xStream

## <a name="live-encoders-that-output-fragmented-mp4"></a>조각난 MP4를 출력하는 라이브 인코더

Media Services는 다중 비트 전송률 부드러운 스트리밍(조각난 MP4)을 출력으로 제공하는 다음 라이브 인코더 중 하나를 사용하도록 권장합니다. 지원되는 URL 체계는 `http://` 또는 `https://`입니다.

- Ateme TITAN Live
- Cisco Digital Media Encoder 2200
- Elemental Live
- Envivio 4Caster C4 Gen III
- Imagine Communications Selenio MCP3
- Media Excel Hero Live 및 Hero 4K(UHD/HEVC)

## <a name="how-to-become-an-on-premises-encoder-partner"></a>온-프레미스 인코더 파트너가 되는 방법

Media Services는 Azure Media Services 온-프레미스 인코더 파트너로서 엔터프라이즈 고객에게 인코더를 추천하여 제품을 홍보합니다. 온-프레미스 인코더 파트너가 되려면 온-프레미스 인코더가 Media Services와 호환되는지 확인해야 합니다. 이렇게 하려면 다음 확인을 완료하세요.

### <a name="pass-through-liveevent-verification"></a>통과 LiveEvent 확인

1. Azure Media Services 계정을 만들거나 방문합니다.
2. **통과** LiveEvent를 만들고 시작합니다.
3. 다중 비트 전송률 라이브 스트림을 푸시하도록 인코더를 구성합니다.
4. 게시된 라이브 이벤트를 만듭니다.
5. 약 10분 동안 라이브 인코더를 실행합니다.
6. 라이브 이벤트를 중지합니다.
7. 스트리밍 엔드포인트를 만들어 시작하고, [Azure Media Player](https://ampdemo.azureedge.net/azuremediaplayer.html) 같은 플레이어를 사용하여 재생 시 모든 품질 수준에서 결함이 표시되지 않도록 보관된 자산을 감시(하거나 6단계 전에 라이브 세션 동안 미리 보기 URL을 통해 감시하고 유효성 검사를 실시)합니다.
8. 자산 ID, 라이브 보관의 게시된 스트리밍 URL, 라이브 인코더에서 사용되는 설정 및 버전을 기록합니다.
9. 각 샘플을 만든 후 LiveEvent 상태를 다시 설정합니다.
10. 인코더에서 지원되는 모든 구성에 대해 3~9단계를 반복합니다(광고 신호/자막/다른 인코딩 속도를 포함하거나 포함하지 않음).

### <a name="live-encoding-liveevent-verification"></a>라이브 인코딩 LiveEvent 확인

1. Azure Media Services 계정을 만들거나 방문합니다.
2. **라이브 인코딩** LiveEvent를 만들고 시작합니다.
3. 단일 비트 전송률 라이브 스트림을 푸시하도록 인코더를 구성합니다.
4. 게시된 라이브 이벤트를 만듭니다.
5. 약 10분 동안 라이브 인코더를 실행합니다.
6. 라이브 이벤트를 중지합니다.
7. 스트리밍 엔드포인트를 만들어 시작하고, [Azure Media Player](https://ampdemo.azureedge.net/azuremediaplayer.html) 같은 플레이어를 사용하여 재생 시 모든 품질 수준에서 결함이 표시되지 않도록 보관된 자산을 감시(하거나 6단계 전에 라이브 세션 동안 미리 보기 URL을 통해 감시하고 유효성 검사를 실시)합니다.
8. 자산 ID, 라이브 보관의 게시된 스트리밍 URL, 라이브 인코더에서 사용되는 설정 및 버전을 기록합니다.
9. 각 샘플을 만든 후 LiveEvent 상태를 다시 설정합니다.
10. 인코더에서 지원되는 모든 구성에 대해 3~9단계를 반복합니다(광고 신호/자막/다양한 인코딩 속도를 포함하거나 포함하지 않음).

### <a name="longevity-verification"></a>수명 확인

1. Azure Media Services 계정을 만들거나 방문합니다.
2. **통과** 채널을 만들고 시작합니다.
3. 다중 비트 전송률 라이브 스트림을 푸시하도록 인코더를 구성합니다.
4. 게시된 라이브 이벤트를 만듭니다.
5. 한 주 이상 라이브 인코더를 실행합니다.
6. [Azure Media Player](https://ampdemo.azureedge.net/azuremediaplayer.html) 같은 플레이어를 사용하여 재생 시 결함이 표시되지 않도록 때때로 라이브 스트리밍(또는 보관된 자산)을 감시합니다.
7. 라이브 이벤트를 중지합니다.
8. 자산 ID, 라이브 보관의 게시된 스트리밍 URL, 라이브 인코더에서 사용되는 설정 및 버전을 기록합니다.

마지막으로, 메일을 통해 기록된 설정 및 라이브 보관 매개 변수를 amsstreaming@microsoft.com의 Azure Media Services에 모든 자체 확인 검사가 통과되었다는 알림으로 보냅니다. 또한 후속 작업을 위해 연락처 정보를 기재해 주세요. 이 프로세스와 관련된 질문이 있는 경우 Azure Media Services 팀에 문의할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[Media Services v3을 통한 라이브 스트리밍](live-streaming-overview.md)
