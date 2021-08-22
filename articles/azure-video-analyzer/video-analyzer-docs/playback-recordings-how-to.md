---
title: 비디오 녹화 재생 - Azure Video Analyzer
description: 연속 비디오 녹화를 위해 Azure Video Analyzer를 사용하여 몇 주 또는 몇 달 동안 클라우드에 비디오를 녹화할 수 있습니다. 이벤트 기반 녹화를 통해 관심 있는 클립으로 녹화를 제한할 수도 있습니다. 이 문서에서는 이러한 녹화를 재생하는 방법을 설명합니다.
ms.service: azure-video-analyzer
ms.topic: how-to
ms.date: 06/01/2021
ms.openlocfilehash: 415edea10eaf635b88786d33bbd439bc386a57fc
ms.sourcegitcommit: 3941df51ce4fca760797fa4e09216fcfb5d2d8f0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2021
ms.locfileid: "114602183"
---
# <a name="playback-of-video-recordings"></a>비디오 녹화 재생 

## <a name="pre-read"></a>미리 읽기  

* [연속 비디오 녹화](continuous-video-recording.md)
* [이벤트 기반 비디오 녹화](event-based-video-recording-concept.md)

## <a name="background"></a>배경  

[CVR(연속 비디오 녹화)](continuous-video-recording.md)을 위해 Azure Video Analyzer를 사용하여 몇 주 또는 몇 달 동안 클라우드에 비디오를 녹화할 수 있습니다. 또한 [EVR(이벤트 기반 비디오 녹화)](event-based-video-recording-concept.md)을 통해 관심 있는 클립으로 기록을 제한할 수 있습니다. 두 경우 모두 [파이프라인](pipeline.md)이 AI를 사용하여 유추 결과를 생성하는 경우 [이러한 결과를 비디오와 함께 기록](record-stream-inference-data-with-video.md)해야 합니다. 

Video Analyzer의 기능을 평가하는 경우 [CVR에 대한 자습서](use-continuous-video-recording.md) 또는 [유추 메타데이터가 있는 비디오 재생에 대한 자습서](record-stream-inference-data-with-video.md)를 참조하여 Azure Portal에서 녹화를 재생해야 합니다.

Video Analyzer API를 사용하여 애플리케이션 또는 서비스를 구축하는 경우 [액세스 정책](access-policies.md) 및 [Video Analyzer 플레이어 위젯](player-widget.md)에 대한 문서와 함께 다음을 검토하여 녹화를 재생하는 방법을 이해해야 합니다.

<!-- TODO - add a section here about 1P/3P SaaS and how to use widgets to allow end users to view videos without talking to ARM APIs -->

## <a name="determining-that-a-video-recording-is-ready-for-playback"></a>비디오 녹화를 재생할 준비가 되었는지 확인

Video Analyzer 계정은 Azure Storage 계정에 연결되어 있으며, 클라우드에 비디오를 녹화하는 경우 콘텐츠가 [비디오 리소스](terminology.md#video)에 기록됩니다. 녹화가 완료된 후 또는 녹화가 진행되는 동안 [해당 콘텐츠를 스트리밍](terminology.md#streaming)할 수 있습니다. 이는 비디오 리소스에 대해 `true`로 설정되는 `canStream` [플래그](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/videoanalyzer/resource-manager/Microsoft.Media/preview/2021-05-01-preview/Videos.json)를 통해 표시됩니다. 

## <a name="video-analyzer-player-widget"></a>Video Analyzer 플레이어 위젯
Video Analyzer는 HLS 또는 MPEG-DASH 프로토콜을 통해 재생 디바이스(클라이언트)로 스트림을 전달하는 데 필요한 기능을 제공합니다. Video Analyzer 플레이어 위젯을 사용하여 스트리밍 URL 및 재생 권한 부여 토큰을 가져온 다음 클라이언트 앱에서 이를 사용하여 비디오 및 유추 메타데이터를 재생합니다.

Video Analyzer 플레이어 위젯을 설치하여 비디오 녹화를 재생할 수 있습니다. 위젯은 `npm` 또는 `yarn`을 사용하여 설치할 수 있으며 이를 통해 클라이언트 쪽 애플리케이션에 위젯을 포함할 수 있습니다. 다음 명령 중 하나를 실행하여 자체 애플리케이션에 위젯을 포함합니다.

NPM:
```
npm install –-save @azure/video-analyzer-widgets
```
YARN:
```
yarn add @azure/video-analyzer-widgets 
```
또는 다음 예를 사용하여 사전 빌드 위치를 참조하는 스크립트 요소에 type="module"을 추가하여 기존 사전 빌드 스크립트를 포함할 수 있습니다.

```
<script async type="module" src="https://unpkg.com/@azure/video-analyzer-widgets"></script> 
``` 

## <a name="recording-and-playback-latencies"></a>녹화 및 재생 대기 시간

Video Analyzer를 사용하여 비디오 리소스에 녹화할 때 모듈이 클라우드에 기록되기 전에 최소 비디오 길이(초)를 집계하도록 지시하는 [`segmentLength` 속성](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/videoanalyzer/data-plane/VideoAnalyzer.Edge/preview/1.0.0/AzureVideoAnalyzer.json)을 지정합니다. 예를 들어 `segmentLength`가 300으로 설정된 경우 모듈은 5분 분량의 비디오를 누적하여 5분 '청크'를 하나 업로드한 후 다음 5분 동안 누적 모드로 전환한 후 다시 업로드합니다. `segmentLength`를 높이면 Azure Storage 트랜잭션 비용을 낮추는 이점이 있습니다. 읽기 및 쓰기가 `segmentLength`(초)마다 한 번만 발생하기 때문입니다.

결과적으로 Video Analyzer 계정으로부터의 비디오 스트리밍은 최소한 그 시간만큼 지연됩니다. 

재생 대기 시간(카메라 앞에서 이벤트가 발생한 시간과 재생 디바이스에서 해당 이벤트를 볼 수 있는 시간 사이의 지연)을 결정하는 또 다른 요소는 [GOP](https://en.wikipedia.org/wiki/Group_of_pictures)(group-of-picture) 기간입니다. [3개의 간단한 기술을 사용하여 라이브 스트림 지연 단축](https://medium.com/vrt-digital-studio/reducing-the-delay-of-live-streams-by-using-3-simple-techniques-e8e028b0a641)에서 설명하듯이 GOP 기간이 길수록 대기 시간이 길어집니다. 일반적으로 감시 및 보안 시나리오에서 사용되는 IP 카메라는 30초 이상의 GOP를 사용하도록 구성됩니다. 이는 전체 대기 시간에 큰 영향을 미칩니다.

## <a name="next-steps"></a>다음 단계

[연속 비디오 녹화 자습서](use-continuous-video-recording.md)
