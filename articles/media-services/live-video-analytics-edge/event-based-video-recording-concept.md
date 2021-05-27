---
title: 이벤트 기반 비디오 녹화 Azure Live Video Analytics
description: 'EVR(이벤트 기반 비디오 녹화)은 Live Video Analytics를 사용하여 이벤트에 의해 트리거된 비디오를 녹화하는 프로세스를 의미합니다. 문제의 이벤트는 비디오 신호 자체 처리(예: 동작 시 감지)로 인해 발생하거나 독립적인 소스(예: 도어 열기)에서 발생할 수 있습니다.  이벤트 기반 비디오 녹화와 관련된 몇 가지 사용 사례가 이 문서에 설명되어 있습니다.'
ms.topic: conceptual
ms.date: 05/27/2020
ms.openlocfilehash: 7970bc6e29a6fb9f914541a52188bf0b53b83b86
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110371490"
---
# <a name="event-based-video-recording-with-live-video-analytics"></a>Live Video Analytics를 사용한 이벤트 기반 비디오 녹화

[!INCLUDE [redirect to Azure Video Analyzer](./includes/redirect-video-analyzer.md)]

## <a name="suggested-pre-reading"></a>추천 참고 자료  

* [연속 비디오 녹화](continuous-video-recording-concept.md)
* [녹화된 콘텐츠 재생](video-playback-concept.md)
* [미디어 그래프 개념](media-graph-concept.md)

## <a name="overview"></a>개요 

EVR(이벤트 기반 비디오 녹화)은 이벤트에 의해 트리거되는 비디오 녹화 프로세스를 말합니다. 문제의 이벤트는 비디오 신호 자체 처리(예: 동작 시 감지)로 인해 발생하거나 독립적인 소스(예: 도어 열기)에서 발생할 수 있습니다. 

아래 사용 사례에 간략하게 설명된 대로 [RTSP 원본](media-graph-concept.md#rtsp-source) 노드, [자산 싱크](media-graph-concept.md#asset-sink) 노드 및 기타 노드로 구성된 미디어 그래프를 사용하여 CCTV 카메라의 비디오(이벤트에 의해 트리거됨)를 Media Services 자산에 기록할 수 있습니다. 이벤트가 발생할 때마다 새 자산을 생성하도록 [자산 싱크](media-graph-concept.md#asset-sink) 노드를 구성하여 각 이벤트에 해당하는 비디오가 자체 자산에 포함되도록 할 수 있습니다. 하나의 자산을 사용하여 모든 이벤트에 대한 비디오를 보존하도록 할 수도 있습니다. 

자산 싱크 노드에 대한 대안으로, [파일 싱크](media-graph-concept.md#file-sink) 노드를 사용하여 에지 디바이스의 로컬 파일 시스템의 지정된 위치로 짧은 비디오 조각(관심 있는 이벤트와 관련됨)을 캡처할 수 있습니다. 

이벤트 기반 비디오 녹화와 관련된 몇 가지 사용 사례가 이 문서에 설명되어 있습니다.

### <a name="video-recording-based-on-motion-detection"></a>동작 감지를 기반으로 하는 비디오 녹화  

이 사용 사례에서는 비디오에서 동작이 감지된 경우에만 비디오 클립을 기록하고 해당 비디오 클립이 생성될 때 경고를 표시할 수 있습니다. 이는 중요한 인프라의 보호와 관련된 상업적 보안 시나리오와 관련이 있을 수 있습니다. 예기치 않은 동작이 감지될 때 경고를 생성하고 비디오를 녹화하면 경고가 생성되는 경우에만 작업이 필요하므로 인적 운영자의 효율성을 높일 수 있습니다.

아래 다이어그램은 이 사용 사례를 처리하는 미디어 그래프의 그래픽 표현을 보여 줍니다. 미디어 그래프의그래프 토폴로지의 JSON 표현은 [여기](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/evr-motion-assets/topology.json)에서 확인할 수 있습니다.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/event-based-video-recording/motion-detection.svg" alt-text="동작 감지를 기반으로 하는 비디오 녹화":::

다이어그램에서 RTSP 원본 노드는 카메라에서 라이브 비디오 피드를 캡처하여 [동작 감지 프로세서](media-graph-concept.md#motion-detection-processor) 노드에 전달합니다. 라이브 비디오에서 동작을 감지할 때 동작 감지 프로세서 노드는 [신호 게이트 프로세서](media-graph-concept.md#signal-gate-processor) 노드뿐만 아니라 IoT Hub 메시지 싱크 노드로도 이동하는 이벤트를 생성합니다. IoT Hub 메시지 싱크 노드는 이벤트를 IoT Edge Hub로 보내고 여기에서 다른 대상으로 라우팅하여 경고를 트리거할 수 있습니다. 

동작 감지기 노드의 이벤트는 신호 게이트 프로세서 노드를 트리거하고 이 노드는 라이브 비디오 피드가 RTSP 원본 노드 흐름에서 자산 싱크 노드로 이동할 수 있도록 합니다. 이후에 이러한 동작 감지 이벤트가 없으면 구성된 시간 후에 게이트가 닫힙니다. 이에 따라 녹화된 비디오의 기간이 결정됩니다.

### <a name="video-recording-based-on-events-from-other-sources"></a>다른 원본의 이벤트를 기반으로 하는 비디오 녹화  

이 사용 사례에서는 다른 IoT 센서의 신호를 사용하여 비디오 녹화를 트리거할 수 있습니다. 아래 다이어그램은 이 사용 사례를 처리하는 미디어 그래프의 그래픽 표현을 보여 줍니다. 미디어 그래프의그래프 토폴로지의 JSON 표현은 [여기](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/evr-hubMessage-files/topology.json)에서 확인할 수 있습니다.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/event-based-video-recording/other-sources.svg" alt-text="다른 원본의 이벤트를 기반으로 비디오 녹화":::

다이어그램에서 외부 센서는 IoT Edge Hub로 이벤트를 보냅니다. 그런 다음 이벤트는 [IoT Hub 메시지 원본](media-graph-concept.md#iot-hub-message-source) 노드를 통해 신호 게이트 프로세서 노드로 라우팅됩니다. 신호 게이트 프로세서 노드의 동작은 이전 사용 사례와 동일합니다 .이는 외부 이벤트에 의해 라이브 비디오 피드가 트리거될 때 이 피드가 RTSP 원본 노드에서 파일 싱크 노드(또는 자산 싱크 노드)로 이동하도록 합니다. 

파일 싱크 노드를 사용하는 경우 비디오는 에지 디바이스의 로컬 파일 시스템에 기록됩니다. 또는 자산 싱크 노드를 사용하는 경우 비디오는 자산에 기록됩니다.

### <a name="video-recording-based-on-an-external-inferencing-module"></a>외부 추론 모듈을 기반으로 하는 비디오 녹화 

이 사용 사례에서는 외부 논리 시스템의 신호를 기반으로 비디오 클립을 기록할 수 있습니다. 이러한 사용 사례의 예로는 고속도로 트래픽의 비디오 피드에서 트럭이 감지된 경우에만 비디오 클립을 기록하는 경우가 있을 수 있습니다. 아래 다이어그램은 이 사용 사례를 처리하는 미디어 그래프의 그래픽 표현을 보여 줍니다. 미디어 그래프의그래프 토폴로지의 JSON 표현은 [여기](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/evr-hubMessage-assets/topology.json)에서 확인할 수 있습니다.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/event-based-video-recording/external-inferencing-module.svg" alt-text="외부 추론 모듈을 기반으로 하는 비디오 녹화":::

다이어그램에서 RTSP 원본 노드는 카메라에서 라이브 비디오 피드를 캡처하여 두 개의 분기로 전달합니다. 하나는 [신호 게이트 프로세서](media-graph-concept.md#signal-gate-processor) 노드를 포함하고 다른 하나는 [HTTP 확장](media-graph-concept.md) 노드를 사용하여 외부 논리 모듈로 데이터를 보냅니다. HTTP 확장 노드를 사용하면 미디어 그래프가 REST를 통해 외부 유추 서비스로 이미지 프레임(JPEG, BMP 또는 PNG 형식)을 보낼 수 있습니다. 이 신호 경로는 일반적으로 낮은 프레임 속도(5fps 미만)만 지원할 수 있습니다. HTTP 확장 프로세서 노드를 사용하여 외부 추론 모듈로 이동하는 비디오의 프레임 속도로 낮출 수 있습니다.

외부 유추 서비스의 결과는 HTTP 확장 노드에서 검색되며, IoT Hub 메시지 싱크 노드를 통해 IoT Edge Hub로 릴레이되어 여기에서 외부 논리 모듈에 의해 추가로 처리될 수 있습니다. 예를 들어 유추 서비스에서 차량 감지를 수행할 수 있는 경우 논리 모듈이 버스 또는 트럭과 같은 특정 자동차를 찾을 수 있습니다. 논리 모듈이 관심 개체를 감지하면 IoT Edge Hub를 통해 그래프의 IoT Hub 메시지 원본 노드로 이벤트를 전송하여 신호 게이트 프로세서 노드를 트리거할 수 있습니다. 신호 게이트의 출력은 파일 싱크 노드 또는 자산 싱크 노드로 이동하는 것으로 표시됩니다. 파일 싱크 노드로 이동하는 경우 비디오는 에지 디바이스의 로컬 파일 시스템에 기록됩니다. 자산 싱크 노드로 이동하는 경우에는 비디오가 자산에 기록됩니다.

이 예제의 향상된 기능은 HTTP 확장 프로세서 노드에 앞서 동작 탐지기 프로세서를 사용하는 것입니다. 이렇게 하면 야간과 같이 고속도로에 장시간 차량이 없을 때 유추 서비스의 부하를 줄일 수 있습니다. 

## <a name="next-steps"></a>다음 단계

[자습서: 이벤트 기반 비디오 녹화](event-based-video-recording-tutorial.md)
