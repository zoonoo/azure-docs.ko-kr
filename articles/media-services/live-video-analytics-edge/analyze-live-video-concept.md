---
title: 기록 없이 라이브 비디오 분석 - Azure
description: 미디어 그래프를 사용하여 에지 또는 클라우드에 기록하지 않고도 라이브 비디오 스트림에서 분석을 추출할 수 있습니다. 이 문서에서는 이러한 개념에 대해 설명합니다.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 25a7cadc47603b726542fa391d441e1fbca78908
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97398977"
---
# <a name="analyzing-live-video-without-any-recording"></a>기록 없이 라이브 비디오 분석

## <a name="suggested-pre-reading"></a>추천 참고 자료 

* [미디어 그래프 개념](media-graph-concept.md)
* [이벤트 기반 비디오 녹화](event-based-video-recording-concept.md)

## <a name="overview"></a>개요  

미디어 그래프를 사용하여 비디오의 일부를 파일 또는 자산에 기록하지 않고도 라이브 비디오를 분석할 수 있습니다. 아래에 표시된 미디어 그래프는 이 문서에서 다루는 [이벤트 기반 비디오 녹화](event-based-video-recording-concept.md)에 대한 그래프와 유사하지만 자산 싱크 노드 또는 파일 싱크 노드는 포함하지 않습니다.

### <a name="motion-detection"></a>동작 감지

아래에 표시된 미디어 그래프는 [RTSP 원본](media-graph-concept.md#rtsp-source) 노드, [동작 감지 프로세서](media-graph-concept.md#motion-detection-processor) 노드 및 [IoT Hub 메시지 싱크](media-graph-concept.md#iot-hub-message-sink) 노드로 구성됩니다. 미디어 그래프의그래프 토폴로지의 JSON 표현은 [여기](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/motion-detection/topology.json)에서 확인할 수 있습니다. 이 그래프에서는 들어오는 라이브 비디오 스트림에서 동작을 감지하고 IoT Hub 메시지 싱크 노드를 통해 동작 이벤트를 다른 앱 및 서비스로 릴레이할 수 있습니다. 외부 앱 또는 서비스는 경고를 트리거하거나 적절한 사람에게 알림을 보낼 수 있습니다.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/analyze-live-video/motion-detection.svg" alt-text="동작 감지를 기반으로 하는 Live Video Analytics":::

### <a name="analyzing-video-using-a-custom-vision-model"></a>사용자 지정 비전 모델을 사용하여 비디오 분석 

아래에 표시된 미디어 그래프에서는 별도의 모듈에 패키지된 사용자 지정 비전 모델을 사용하여 라이브 비디오 스트림을 분석할 수 있습니다. 미디어 그래프의그래프 토폴로지의 JSON 표현은 [여기](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/httpExtension/topology.json)에서 확인할 수 있습니다. [여기](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis)에서 유추 서비스로 실행되는 IoT Edge 모듈에 모델을 래핑하는 몇 가지 예제를 확인할 수 있습니다.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/analyze-live-video/motion-detected-frames.svg" alt-text="외부 유추 모듈을 기준으로 하는 Live Video Analytics":::

이 미디어 그래프에서는 RTSP 원본의 비디오 입력이 [HTTP 확장 프로세서](media-graph-concept.md#http-extension-processor) 노드로 전송됩니다. 그러면 노드는 REST를 통해 외부 유추 서비스로 이미지 프레임(JPEG, BMP 또는 PNG 형식)을 보냅니다. 외부 유추 서비스의 결과는 HTTP 확장 노드에서 검색된 후 IoT Hub 메시지 싱크 노드를 통해 IoT Edge 허브로 릴레이됩니다. 이 유형의 미디어 그래프를 사용하여 교차로를 통과하는 차량의 시계열 분포를 이해하고, 소매점의 소비자 트래픽 패턴을 이해하는 등의 다양한 시나리오에 대한 솔루션을 만들 수 있습니다.
>[!TIP]
> 다운스트림으로 보내기 전에 `samplingOptions` 필드를 사용하여 HTTP 확장 프로세서 노드 내에서 프레임 속도를 관리할 수 있습니다.

이 예제의 향상된 기능은 HTTP 확장 프로세서 노드에 앞서 동작 탐지기 프로세서를 사용하는 것입니다. 이를 통해 비디오에 동작 활동이 있는 경우에만 유추 서비스가 사용되므로 유추 서비스에 대한 부하를 줄일 수 있습니다.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/analyze-live-video/custom-model.svg" alt-text="외부 유추 모듈을 통한 동작 감지 프레임을 기준으로 하는 Live Video Analytics":::

## <a name="next-steps"></a>다음 단계

[연속 비디오 녹화](continuous-video-recording-concept.md)
