---
title: Azure Media Services에서 권장되는 인코더 알아보기 | Microsoft Docs
description: Media Services에서 권장되는 인코더 알아보기
services: media-services
keywords: 인코딩, 인코더, 미디어
author: dbgeorge
manager: johndeu
ms.author: johndeu
ms.date: 03/20/2019
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 373ce1d10af87603b1bdd6339c94891187c35d8c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60332655"
---
# <a name="recommended-on-premises-encoders"></a>권장 온-프레미스 인코더
Azure Media Services를 통해 라이브 스트리밍을 사용하는 경우 채널에서 입력 스트림을 수신하는 방법을 지정할 수 있습니다. 라이브 인코딩 채널을 사용 하 여 온-프레미스 인코더를 사용 하려는 경우 인코더는 고품질 단일 비트 전송률 스트림을 출력으로 푸시 해야 합니다. 통과 채널을 사용 하 여 온-프레미스 인코더를 사용 하려는 경우 인코더는 다중 비트 전송률 스트림을 원하는 모든 출력 품질이 포함 된 출력으로 푸시 해야 합니다. 자세한 내용은 [인코더를 프레미스에 사용한 라이브 스트리밍](media-services-live-streaming-with-onprem-encoders.md)합니다.

Azure Media Services는 RTMP이 출력으로 포함된 다음 라이브 인코더 중 하나를 사용하도록 권장합니다.
- Adobe Flash Media Live Encoder 3.2
- Haivision Makito X HEVC
- Haivision KB
- Telestream Wirecast 8.1+
- Telestream Wirecast S
- Teradek Slice 756
- TriCaster 8000
- Tricaster Mini HD-4
- OBS Studio
- VMIX
- xStream
- Switcher Studio(iOS)

Azure Media Services는 다중 비트 전송률 조각화된 MP4(부드러운 스트리밍)를 출력으로 보유한 다음 라이브 인코더 중 하나를 사용하도록 권장합니다.
- Media Excel Hero Live 및 Hero 4K(UHD/HEVC)
- Ateme TITAN Live
- Cisco Digital Media Encoder 2200
- Elemental Live
- Envivio 4Caster C4 Gen III
- Imagine Communications Selenio MCP3

> [!NOTE]
> 라이브 인코더는 통과 채널에 단일 비트 전송률 스트림을 전송할 수 있지만 이 구성에서는 클라이언트에 대한 적응 비트 전송률 스트리밍이 허용되지 않으므로 이 구성은 권장되지 않습니다.

## <a name="how-to-become-an-on-premises-encoder-partner"></a>온-프레미스 인코더 파트너가 하는 방법
온-프레미스 인코더 파트너가 Azure Media Services로 Media Services 인코더 기업 고객에 게 추천 하 여 제품을 승격 합니다. 온-프레미스 인코더 파트너가 되려면를 Media Services를 사용 하 여 온-프레미스 인코더의 호환성을 확인 해야 합니다. 이렇게 하려면 다음 확인을 완료하세요.

채널 확인 통과
1. Azure Media Services 계정 만들기 또는 방문
2. **통과** 채널 만들기 및 시작
3. 다중 비트 전송률 라이브 스트림을 푸시하도록 인코더를 구성합니다.
4. 게시된 라이브 이벤트 만들기
5. 약 10분 동안 라이브 인코더 실행
6. 라이브 이벤트 중지
7. 스트리밍 엔드포인트를 만들어 시작하고, [Azure Media Player](https://ampdemo.azureedge.net/azuremediaplayer.html) 같은 플레이어를 사용하여 재생 시 모든 품질 수준에서 결함이 표시되지 않도록 보관된 자산을 감시(하거나 6단계 전에 라이브 세션 동안 미리 보기 URL을 통해 감시하고 유효성 검사를 실시)합니다.
8. 자산 ID, 라이브 보관의 게시된 스트리밍 URL, 라이브 인코더에서 사용되는 설정 및 버전을 기록합니다.
9. 각 샘플을 만든 후 채널 상태를 다시 설정합니다.
10. 인코더에서 지원되는 모든 구성에 대해 3-9단계 반복(광고 신호/자막/다른 인코딩 속도를 포함하거나 포함하지 않음)

라이브 인코딩 채널 확인
1. Azure Media Services 계정 만들기 또는 방문
2. **라이브 인코딩** 채널 만들기 및 시작
3. 단일 비트 전송률 라이브 스트림을 푸시하도록 인코더를 구성합니다.
4. 게시된 라이브 이벤트 만들기
5. 약 10분 동안 라이브 인코더 실행
6. 라이브 이벤트 중지
7. 스트리밍 엔드포인트를 만들어 시작하고, [Azure Media Player](https://ampdemo.azureedge.net/azuremediaplayer.html) 같은 플레이어를 사용하여 재생 시 모든 품질 수준에서 결함이 표시되지 않도록 보관된 자산을 감시(하거나 6단계 전에 라이브 세션 동안 미리 보기 URL을 통해 감시하고 유효성 검사를 실시)합니다.
8. 자산 ID, 라이브 보관의 게시된 스트리밍 URL, 라이브 인코더에서 사용되는 설정 및 버전을 기록합니다.
9. 각 샘플을 만든 후 채널 상태를 다시 설정합니다.
10. 인코더에서 지원되는 모든 구성에 대해 3-9단계 반복(광고 신호/자막/다양한 인코딩 속도를 포함하거나 포함하지 않음)

수명 확인
1. Azure Media Services 계정 만들기 또는 방문
2. **통과** 채널 만들기 및 시작
3. 다중 비트 전송률 라이브 스트림을 푸시하도록 인코더를 구성합니다.
4. 게시된 라이브 이벤트 만들기
5. 한 주 이상 라이브 인코더 실행
6. [Azure Media Player](https://ampdemo.azureedge.net/azuremediaplayer.html) 같은 플레이어를 사용하여 재생 시 결함이 표시되지 않도록 때때로 라이브 스트리밍(또는 보관된 자산)을 감시
7. 라이브 이벤트 중지
8. 자산 ID, 라이브 보관의 게시된 스트리밍 URL, 라이브 인코더에서 사용되는 설정 및 버전을 기록합니다.

마지막으로 amsstreaming@microsoft.com에 메일을 보내 기록된 설정과 라이브 보관 매개 변수를 Media Services에 보냅니다. 수신 시 Media Services는 라이브 인코더에서 샘플에 대한 확인 테스트를 수행합니다. 이 프로세스와 관련된 질문이 있는 경우 Media Services에 문의할 수 있습니다.
