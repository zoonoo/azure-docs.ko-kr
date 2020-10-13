---
title: 기록 없이 라이브 비디오 분석-Azure
description: 미디어 그래프를 사용 하 여에 지 또는 클라우드에 기록 하지 않고도 라이브 비디오 스트림에서 분석을 추출할 수 있습니다. 이 문서에서는 이러한 개념에 대해 설명 합니다.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 5dda18b68cb19d29623f2120fe07d7cc617f0c2f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90893031"
---
# <a name="analyzing-live-video-without-any-recording"></a>기록 없이 라이브 비디오 분석

## <a name="suggested-pre-reading"></a>추천 참고 자료 

* [미디어 그래프 개념](media-graph-concept.md)
* [이벤트 기반 비디오 녹화](event-based-video-recording-concept.md)

## <a name="overview"></a>개요  

미디어 그래프를 사용 하 여 비디오의 일부를 파일 또는 자산에 기록 하지 않고도 라이브 비디오를 분석할 수 있습니다. 아래에 표시 된 미디어 그래프는 자산 싱크 노드 또는 파일 싱크 노드가 없는 [이벤트 기반 비디오 기록](event-based-video-recording-concept.md)에 대 한 문서에 있는 것과 비슷합니다.

### <a name="motion-detection"></a>동작 검색

아래에 표시 된 미디어 그래프는 [RTSP 원본](media-graph-concept.md#rtsp-source) 노드, [동작 검색 프로세서](media-graph-concept.md#motion-detection-processor) 노드 및 [IoT Hub 메시지 싱크](media-graph-concept.md#iot-hub-message-sink) 노드로 구성 됩니다. 이러한 미디어 그래프의 그래프 토폴로지의 JSON 표현은 [여기](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/motion-detection/topology.json)에서 찾을 수 있습니다. 이 그래프를 사용 하면 들어오는 라이브 비디오 스트림에서 동작을 감지 하 고 IoT Hub 메시지 싱크 노드를 통해 동작 이벤트를 다른 앱 및 서비스에 릴레이할 수 있습니다. 외부 앱 또는 서비스는 경고를 트리거하거나 적절 한 사람에 게 알림을 보낼 수 있습니다.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/analyze-live-video/motion-detection.svg" alt-text="동작 감지를 기반으로 하는 Live Video Analytics":::

### <a name="analyzing-video-using-a-custom-vision-model"></a>사용자 지정 비전 모델을 사용 하 여 비디오 분석 

아래에 표시 된 미디어 그래프를 사용 하면 별도의 모듈에 패키지 된 사용자 지정 비전 모델을 사용 하 여 라이브 비디오 스트림을 분석할 수 있습니다. 이러한 미디어 그래프의 그래프 토폴로지의 JSON 표현은 [여기](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/httpExtension/topology.json)에서 찾을 수 있습니다. 유추 서비스로 실행 되는 IoT Edge 모듈로 모델 [래핑에서 몇 가지 예를 확인할](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis) 수 있습니다.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/analyze-live-video/motion-detected-frames.svg" alt-text="동작 감지를 기반으로 하는 Live Video Analytics":::

이 미디어 그래프에서 프레임 전송률 필터 프로세서 노드는 들어오는 라이브 비디오 스트림의 프레임 주기를 낮춰 [HTTP 확장 프로세서](media-graph-concept.md#http-extension-processor) 노드로 보냅니다 .이 노드는 이미지 프레임 (JPEG, BMP 또는 PNG 형식)을 REST를 통해 외부 유추 서비스로 보냅니다. 외부 유추 서비스의 결과는 HTTP 확장 노드에 의해 검색 되 고 IoT Hub 메시지 싱크 노드를 통해 IoT Edge 허브로 릴레이 됩니다. 이 유형의 미디어 그래프를 사용 하 여 교차로의 시계열 분포를 이해 하 고 소매점의 소비자 트래픽 패턴을 이해 하는 등의 다양 한 시나리오에 대 한 솔루션을 만들 수 있습니다.

이 예제의 향상 된 기능은 프레임 전송률 필터 프로세서 노드 앞에 동작 탐지기 프로세서를 사용 하는 것입니다. 이렇게 하면 비디오에 동작 동작이 있는 경우에만 사용 되므로 유추 서비스의 부하를 줄일 수 있습니다.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/analyze-live-video/custom-model.svg" alt-text="동작 감지를 기반으로 하는 Live Video Analytics":::

## <a name="next-steps"></a>다음 단계

[연속 비디오 녹화](continuous-video-recording-concept.md)
