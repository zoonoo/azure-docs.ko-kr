---
title: 이벤트 기반 비디오 기록-Azure
description: 'EVR(이벤트 기반 비디오 녹화)은 이벤트에 의해 트리거되는 비디오 녹화 프로세스를 말합니다. 문제의 이벤트는 비디오 신호 자체의 처리 (예: 동작에 대 한 검색) 또는 독립적인 원본 (예: 도어 열기)으로 인해 발생할 수 있습니다.  이벤트 기반 비디오 기록과 관련 된 몇 가지 사용 사례는이 문서에 설명 되어 있습니다.'
ms.topic: conceptual
ms.date: 05/27/2020
ms.openlocfilehash: f3efd2b9be41928ab4721d6db4aa84c0f1f57e2f
ms.sourcegitcommit: d0541eccc35549db6381fa762cd17bc8e72b3423
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89568506"
---
# <a name="event-based-video-recording"></a>이벤트 기반 비디오 녹화  
 
## <a name="suggested-pre-reading"></a>추천 참고 자료  

* [연속 비디오 녹화](continuous-video-recording-concept.md)
* [기록 된 콘텐츠 재생](video-playback-concept.md)
* [미디어 그래프 개념](media-graph-concept.md)

## <a name="overview"></a>개요 

EVR(이벤트 기반 비디오 녹화)은 이벤트에 의해 트리거되는 비디오 녹화 프로세스를 말합니다. 문제의 이벤트는 비디오 신호 자체의 처리 (예: 동작에 대 한 검색) 또는 독립적인 원본 (예: 도어 열기)으로 인해 발생할 수 있습니다. 

아래 사용 사례에 설명 된 대로 [RTSP 원본](media-graph-concept.md#rtsp-source) 노드, [자산 싱크](media-graph-concept.md#asset-sink) 노드 및 기타 노드로 구성 된 미디어 그래프를 사용 하 여 cctv 카메라의 비디오 (이벤트에 의해 트리거됨)를 Media Services 자산에 기록할 수 있습니다. 이벤트가 발생할 때마다 [자산 싱크](media-graph-concept.md#asset-sink) 노드를 구성 하 여 각 이벤트에 해당 하는 비디오가 자체 자산에 있도록 할 수 있습니다. 한 자산을 사용 하 여 모든 이벤트에 대 한 비디오를 보관할 수도 있습니다. 

Asset sink 노드에 대 한 대 안으로, [파일 싱크](media-graph-concept.md#file-sink) 노드를 사용 하 여에 지 장치에서 로컬 파일 시스템의 지정 된 위치에 대 한 짧은 비디오 조각 (관심 있는 이벤트와 관련 된)을 캡처할 수 있습니다. 

이벤트 기반 비디오 기록과 관련 된 몇 가지 사용 사례는이 문서에 설명 되어 있습니다.

### <a name="video-recording-based-on-motion-detection"></a>동작 검색을 기준으로 비디오 녹화  

이 사용 사례에서는 비디오에서 동작이 검색 된 경우에만 비디오 클립을 기록 하 고 이러한 비디오 클립이 생성 될 때 경고를 표시할 수 있습니다. 이는 중요 한 인프라의 보호와 관련 된 상업적 보안 시나리오와 관련이 있을 수 있습니다. 예기치 않은 동작이 검색 될 때 경고를 생성 하 고 비디오를 기록 하면 경고가 생성 되는 경우에만 작업이 필요 하므로 인적 연산자의 효율성을 향상 시킬 수 있습니다.

아래 다이어그램은이 사용 사례를 해결 하는 미디어 그래프를 그래픽으로 나타낸 것입니다. 이러한 미디어 그래프의 그래프 토폴로지의 JSON 표현은 [여기](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/evr-motion-assets/topology.json)에서 찾을 수 있습니다.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/event-based-video-recording/motion-detection.svg" alt-text="동작 검색을 기준으로 비디오 녹화":::

다이어그램에서 RTSP 원본 노드는 카메라에서 라이브 비디오 피드를 캡처하여 [동작 감지 프로세서](media-graph-concept.md#motion-detection-processor) 노드에 전달 합니다. 라이브 비디오에서 동작을 감지할 때 동작 감지 프로세서 노드는 [신호 게이트 프로세서](media-graph-concept.md#signal-gate-processor) 노드 뿐만 아니라 IoT Hub 메시지 싱크 노드로 이동 하는 이벤트를 생성 합니다. 후자 노드는 이벤트를 다른 대상으로 라우팅할 수 있는 위치에서 IoT Edge 허브로 전송 하 여 경고를 트리거합니다. 

동작 탐지기 노드의 이벤트는 신호 게이트 프로세서 노드를 트리거하고 RTSP 원본 노드의 라이브 비디오 피드가 자산 싱크 노드로 이동할 수 있도록 합니다. 이러한 동작 검색 이벤트가 없으면 구성 된 시간 후에 게이트가 닫힙니다. 녹화 된 비디오의 기간을 결정 합니다.

### <a name="video-recording-based-on-events-from-other-sources"></a>다른 원본의 이벤트를 기반으로 하는 비디오 녹음  

이 사용 사례에서 다른 IoT 센서의 신호를 사용 하 여 비디오 기록을 트리거할 수 있습니다. 아래 다이어그램은이 사용 사례를 해결 하는 미디어 그래프를 그래픽으로 나타낸 것입니다. 이러한 미디어 그래프의 그래프 토폴로지의 JSON 표현은 [여기](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/evr-hubMessage-files/topology.json)에서 찾을 수 있습니다.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/event-based-video-recording/other-sources.svg" alt-text="다른 원본의 이벤트를 기반으로 하는 비디오 녹음":::

다이어그램에서 외부 센서는 IoT Edge 허브로 이벤트를 보냅니다. 그런 다음 이벤트는 [IoT Hub 메시지 원본](media-graph-concept.md#iot-hub-message-source) 노드를 통해 신호 게이트 프로세서 노드로 라우팅됩니다. 신호 게이트 프로세서 노드의 동작은 이전 사용 사례와 동일 합니다 .이는 외부 이벤트에 의해 트리거될 때 라이브 비디오 피드가 RTSP 원본 노드에서 파일 싱크 노드 (또는 자산 싱크 노드)로 이동 하는 것을 허용 합니다. 

파일 싱크 노드를 사용 하는 경우 비디오는에 지 장치의 로컬 파일 시스템에 기록 됩니다. 또는 자산 싱크 노드를 사용 하는 경우 비디오는 자산에 기록 됩니다.

### <a name="video-recording-based-on-an-external-inferencing-module"></a>외부 추론 모듈을 기반으로 하는 비디오 녹화 

이 사용 사례에서 외부 논리 시스템의 신호에 따라 비디오 클립을 기록할 수 있습니다. 이러한 사용 사례의 예는 고속도로의 트래픽 비디오 피드에서 트럭이 검색 된 경우에만 비디오 클립을 기록 하는 것일 수 있습니다. 아래 다이어그램은이 사용 사례를 해결 하는 미디어 그래프를 그래픽으로 나타낸 것입니다. 이러한 미디어 그래프의 그래프 토폴로지의 JSON 표현은 [여기](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/evr-hubMessage-assets/topology.json)에서 찾을 수 있습니다.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/event-based-video-recording/external-inferencing-module.svg" alt-text="외부 추론 모듈을 기반으로 하는 비디오 녹화":::

다이어그램에서 RTSP 원본 노드는 카메라에서 라이브 비디오 피드를 캡처하여 두 개의 분기로 전달 합니다. 하나는 [신호 게이트 프로세서](media-graph-concept.md#signal-gate-processor) 노드를 포함 하 고 다른 하나는 [HTTP 확장](media-graph-concept.md) 노드를 사용 하 여 외부 논리 모듈로 데이터를 전송 합니다. HTTP 확장 노드를 사용 하면 미디어 그래프가 휴지 상태의 외부 유추 서비스로 이미지 프레임 (JPEG, BMP 또는 PNG 형식)을 보낼 수 있습니다. 이 신호 경로는 일반적으로 낮은 프레임 속도 (<5fps)만 지원할 수 있습니다. [프레임 전송률 필터 프로세서](media-graph-concept.md#frame-rate-filter-processor) 노드를 사용 하 여 HTTP 확장 노드로 이동 하는 비디오의 프레임 속도로 낮출 수 있습니다.

외부 유추 서비스의 결과는 HTTP 확장 노드에서 검색 되며, 외부 논리 모듈에서 추가 처리할 수 있는 IoT Hub 메시지 싱크 노드를 통해 IoT Edge 허브로 릴레이 됩니다. 예를 들어 유추 서비스에서 차량 검색을 수행할 수 있는 경우 논리 모듈이 버스 또는 트럭과 같은 특정 자동차를 찾을 수 있습니다. 논리 모듈이 원하는 개체를 검색 하면 IoT Edge 허브를 통해 그래프의 IoT Hub 메시지 원본 노드로 이벤트를 전송 하 여 신호 게이트 프로세서 노드를 트리거할 수 있습니다. 신호 게이트의 출력은 파일 싱크 노드 또는 자산 싱크 노드로 이동 하도록 표시 됩니다. 이전 경우 비디오는에 지 장치의 로컬 파일 시스템에 기록 됩니다. 후자의 경우 비디오는 자산에 기록 됩니다.

이 예제의 향상 된 기능은 프레임 전송률 필터 프로세서 노드 앞에 동작 탐지기 프로세서를 사용 하는 것입니다. 이렇게 하면 고속도로에 대 한 차량 없는 시간이 길어질 때 야간 같은 유추 서비스의 부하를 줄일 수 있습니다. 

## <a name="next-steps"></a>다음 단계

[자습서: 이벤트 기반 비디오 기록](event-based-video-recording-tutorial.md)
