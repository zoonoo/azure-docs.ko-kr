---
title: Live Video Analytics를 사용한 비디오 재생 - Azure
description: 이 문서에서는 Azure Media Services의 기존 스트리밍 기능을 사용하여 자산을 재생하기 위해 수행해야 하는 단계에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 9669194f3dff8f64d54cb15c8cb6fee0d7a2667b
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110368390"
---
# <a name="video-playback-with-live-video-analytics"></a>Live Video Analytics를 사용한 비디오 재생

[!INCLUDE [redirect to Azure Video Analyzer](./includes/redirect-video-analyzer.md)]

## <a name="suggested-pre-reading"></a>추천 참고 자료 

* [Live Video Analytics on IoT Edge 개요](overview.md)
* [Live Video Analytics on IoT Edge 용어](terminology.md)
* [미디어 그래프 개념](media-graph-concept.md)

## <a name="overview"></a>개요  

[미디어 그래프](media-graph-concept.md)를 사용하여 비디오를 Azure Media Services [자산](terminology.md#asset)에 기록할 수 있습니다. 이 문서에서는 Azure Media Services의 기존 스트리밍 기능을 사용하여 자산을 재생하기 위해 수행해야 하는 단계에 대해 알아봅니다.

## <a name="streaming-endpoint"></a>스트리밍 엔드포인트 

Azure Media Services를 사용하여 HLS(HTTP 라이브 스트리밍) 및 MPEG-DASH와 같은 업계 표준의 HTTP 기반 미디어 스트리밍 프로토콜을 사용하여 자산을 비디오 플레이어로 [스트리밍](terminology.md#streaming)할 수 있습니다. 기록된 콘텐츠에서 미디어를 스트리밍 형식으로 미디어를 변환하는 것은 Azure Media Service 계정에서 프로비전해야 하는 리소스인 [스트리밍 엔드포인트](../latest/stream-streaming-endpoint-concept.md)에 의해 처리됩니다.

## <a name="streaming-policy"></a>스트리밍 정책 

Azure Media Services는 [Media Services 동적 암호화를 사용하여 콘텐츠 보호](../latest/drm-content-protection-concept.md) 문서에 설명된 대로 비디오 스트림을 보호하는 다양한 방법을 제공합니다. 높은 수준에서 콘텐츠 보호에 대한 옵션은 다음과 같습니다.

* **명확한 스트리밍** – 스트리밍 중에 암호화가 적용되지 않습니다.
* **Advanced Encryption Standard(AES-128) 사용** – 인증된 뷰어에게만 비디오의 암호를 해독하는 키를 제공하는 메서드를 구현합니다.
* **DRM(Digital Rights Management) 시스템 사용** – 이러한 정책을 적용하는 디바이스에 대한 비디오의 사용, 수정 및 전달을 제어합니다.

콘텐츠 보호를 위해 Media Service 계정에서 [스트리밍 정책](../latest/stream-streaming-policy-concept.md)을 정의 및 만들고 모든 자산을 스트리밍하는 데 사용할 수 있습니다(모든 스트림에 보안 요구 사항이 동일하다고 가정). 미리 정의된 정책(예: Predefined_ClearStreamingOnly)을 사용할 수도 있습니다.

## <a name="streaming-locator"></a>스트리밍 로케이터  

Media Service 계정에서 스트리밍 엔드포인트를 시작하고 스트리밍 정책을 정의한 후에는 HLS 또는 DASH 프로토콜을 통해 자산에서 기록된 미디어를 스트리밍할 수 있습니다. 웹 플레이어와 모바일 앱에는 해당 HLS 또는 DASH 스트림을 가리키는 URL이 필요합니다. [스트리밍 로케이터](../latest/stream-streaming-locators-concept.md)를 사용하여 이 URL을 빌드할 수 있습니다. 해당 문서에 설명된 대로 [스트리밍 로케이터 만들기 및 URL 빌드](../latest/create-streaming-locator-build-url.md) 샘플에 표시된 스트리밍 URL은 스트리밍 엔드포인트, 스트리밍 정책 및 스트리밍 로케이터로 구성됩니다.

## <a name="content-recorded-using-file-sink"></a>파일 싱크를 사용하여 기록된 콘텐츠  

[미디어 그래프 파일 싱크](media-graph-concept.md#file-sink)에 설명된 대로 미디어 그래프를 통해 미디어 그래프의 파일 싱크를 사용하여 에지 디바이스의 로컬 파일 시스템에 비디오를 녹화할 수 있습니다. 파일 싱크에서 [MP4](https://developer.mozilla.org/docs/Web/Media/Formats/Containers#MP4) 파일을 생성하고 HTML5 [&lt;비디오&gt;](https://developer.mozilla.org/docs/Web/HTML/Element/video) 요소를 사용하여 이러한 콘텐츠를 재생할 수 있습니다. 

## <a name="next-steps"></a>다음 단계

[Azure IoT Edge](../../iot-edge/index.yml)
<!--
## Next steps

[Playback recording](playback-recording-how-to.md)
-->
