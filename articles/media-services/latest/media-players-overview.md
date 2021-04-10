---
title: Media Services용 미디어 플레이어 개요
description: Azure Media Services에서 사용할 수 있는 미디어 플레이어는 무엇인가요? 지금까지 Azure Media Player, Shaka 및 Video.js.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 3/08/2021
ms.openlocfilehash: 47ef56a770ca9965cef3e50630be4b69342a038d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105559862"
---
# <a name="media-players-for-media-services"></a>Media Services용 미디어 플레이어

Media Services에서 여러 미디어 플레이어를 사용할 수 있습니다.

## <a name="azure-media-player"></a>Azure Media Player

Azure Media Player는 다양한 브라우저와 디바이스를 위한 비디오 플레이어입니다. Azure Media Player는 풍부해진 적응 스트리밍 환경을 제공할 수 있는 HTML5, MSE(미디어 원본 확장) 및 EME(암호화된 미디어 확장)와 같은 업계 표준을 사용합니다. 이러한 표준을 디바이스 또는 브라우저에서 사용할 수 없는 경우, Azure Media Player는 Flash 및 Silverlight를 대체 기술로 사용합니다. 사용된 재생 기술이 무엇이든 개발자는 API에 액세스할 수 있는 통합된 JavaScript 인터페이스가 있어야 합니다. Azure Media Services에서 제공하는 콘텐츠는 추가 작업 없이 광범위한 디바이스 및 브라우저에서 재생할 수 있습니다.

[Azure Media Player 설명서](../azure-media-player/azure-media-player-overview.md)를 참조하세요.

## <a name="shaka"></a>Shaka

Shaka Player는 적응형 미디어를 위한 오픈 소스 JavaScript 라이브러리입니다. 플러그 인 또는 플래시를 사용하지 않고 브라우저에서 적응형 미디어 형식(예: DASH 및 HLS)을 재생합니다. 대신, Shaka Player는 개방형 웹 표준 미디어 원본 확장 및 암호화된 미디어 확장을 사용합니다.

[Azure Media Services에서 Shaka 플레이어를 사용하는 방법](how-to-shaka-player.md)을 참조하세요.

## <a name="videojs"></a>Video.js

Video.js는 브라우저에서 적응형 미디어 형식(예: DASH 및 HLS)을 재생하는 비디오 플레이어입니다. Video.js는 개방형 웹 표준 MediaSource 확장 및 암호화된 미디어 확장을 사용합니다. 데스크톱 및 모바일 디바이스에서 비디오 재생을 지원합니다. 공식 설명서는 https://docs.videojs.com/ 에서 확인할 수 있습니다.

[Azure Media Services에서 Video.js 플레이어를 사용하는 방법](how-to-video-js-player.md)을 참조하세요.


## <a name="next-steps"></a>다음 단계 ##

- [Azure Media Player 빠른 시작](../azure-media-player/azure-media-player-quickstart.md)