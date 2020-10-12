---
title: 비디오 재생-Azure
description: 자리표시자
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 17b1f93c18dfb013916c4c0d3756fb97a73e2675
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87042969"
---
# <a name="video-playback"></a>비디오 재생 

## <a name="suggested-pre-reading"></a>추천 참고 자료 

* [Live Video Analytics on IoT Edge 개요](overview.md)
* [Live Video Analytics on IoT Edge 용어](terminology.md)
* [미디어 그래프 개념](media-graph-concept.md)

## <a name="overview"></a>개요  

[미디어 그래프](media-graph-concept.md) 를 사용 하 여 비디오를 Azure Media Services [자산](terminology.md#asset)에 기록할 수 있습니다. 이 문서에서는 Azure Media Services의 기존 스트리밍 기능을 사용 하 여 자산을 재생 하기 위해 수행 해야 하는 단계에 대해 알아볼 수 있습니다.

## <a name="streaming-endpoint"></a>스트리밍 엔드포인트 

Azure Media Services를 사용 하 여 HTTP 라이브 스트리밍 (HLS) 및 MPEG와 같은 업계 표준의 HTTP 기반 미디어 스트리밍 프로토콜을 사용 하 여 비디오 플레이어로 자산을 [스트리밍할](terminology.md#streaming) 수 있습니다. 기록 된 콘텐츠에서 미디어를 스트리밍 형식으로 변환 하는 것은 Azure Media Service 계정에서 프로 비전 해야 하는 리소스인 [스트리밍 끝점](../latest/streaming-endpoint-concept.md)에 의해 처리 됩니다.

## <a name="streaming-policy"></a>스트리밍 정책 

Azure Media Services [은 Media Services 동적 암호화를 사용 하 여 콘텐츠 보호](../latest/content-protection-overview.md) 문서에 설명 된 대로 비디오 스트림을 보호 하는 다양 한 방법을 제공 합니다. 높은 수준에서 콘텐츠 보호에 대 한 옵션은 다음과 같습니다.

* - **Clear 스트리밍** – 스트리밍 중에 암호화가 적용 되지 않습니다.
* **AES(Advanced Encryption Standard) (AES-128)을 사용** 하 고 인증 된 뷰어로만 비디오의 암호를 해독 하는 키를 제공 하는 메서드를 구현 합니다.
* **DRM (디지털 Rights Management) 시스템 사용** – 이러한 정책을 적용 하는 장치에 대 한 비디오의 사용, 수정 및 전달을 제어 합니다.

콘텐츠 보호를 위해 미디어 서비스 계정에서 [스트리밍 정책을](../latest/streaming-policy-concept.md) 정의 하 고 만들고 모든 자산을 스트리밍하는 데 사용할 수 있습니다 (모든 스트림이 동일한 보안 요구 사항을 전제로 한다고 가정). 미리 정의 된 정책 (예: Predefined_ClearStreamingOnly)을 사용할 수도 있습니다.

## <a name="streaming-locator"></a>스트리밍 로케이터  

스트리밍 끝점이 Media Service 계정에서 시작 되 고 스트리밍 정책이 정의 되 면 HLS 또는 대시 프로토콜을 통해 자산에서 기록 된 미디어를 스트리밍할 수 있습니다. 웹 플레이어와 모바일 앱에는 해당 HLS 또는 대시 스트림을 가리키는 URL이 필요 합니다. [스트리밍 로케이터](../latest/streaming-locators-concept.md)를 사용 하 여이 URL을 빌드할 수 있습니다. 이 문서에 설명 된 대로 [스트리밍 로케이터 및 빌드 Url 만들기](../latest/create-streaming-locator-build-url.md) 샘플에 표시 된 스트리밍 URL은 스트리밍 끝점, 스트리밍 정책 및 스트리밍 로케이터로 구성 됩니다.

## <a name="content-recorded-using-file-sink"></a>파일 싱크를 사용 하 여 기록 된 콘텐츠  

미디어 [그래프 파일 싱크에](media-graph-concept.md#file-sink)설명 된 대로 미디어 그래프를 사용 하 여 미디어 그래프에서 파일 싱크를 사용 하 여에 지 장치의 로컬 파일 시스템에 비디오를 기록할 수 있습니다. 파일 싱크에서 [MP4](https://developer.mozilla.org/docs/Web/Media/Formats/Containers#MP4) 파일을 생성 하 고 HTML5 [ &lt; video &gt; ](https://developer.mozilla.org/docs/Web/HTML/Element/video) 요소를 사용 하 여 이러한 콘텐츠를 재생할 수 있습니다. 

## <a name="next-steps"></a>다음 단계

[Azure IoT Edge](../../iot-edge/index.yml)
<!--
## Next steps

[Playback recording](playback-recording-how-to.md)
-->
