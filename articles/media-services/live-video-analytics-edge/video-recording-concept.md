---
title: Live Video Analytics를 사용한 비디오 녹화 - Azure
description: CCTV 카메라용 비디오 관리 시스템 컨텍스트에서 비디오 녹화는 카메라에서 영상을 캡처해 모바일과 브라우저 앱을 통해 볼 수 있도록 기록하는 과정을 말합니다. 비디오 녹화는 연속 비디오 녹화 및 이벤트 기반 비디오 녹화로 분류될 수 있습니다. 이 문서에서는 비디오 녹화에 대해 설명합니다.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: fea0d2daaa4724cf4d354c8ab16b6b6ba3e76bf5
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110368373"
---
# <a name="video-recording-with-live-video-analytics"></a>Live Video Analytics를 사용한 비디오 녹화

[!INCLUDE [redirect to Azure Video Analyzer](./includes/redirect-video-analyzer.md)]

CCTV 카메라용 비디오 관리 시스템 컨텍스트에서 비디오 녹화는 카메라에서 영상을 캡처해 모바일과 브라우저 앱을 통해 볼 수 있도록 기록하는 과정을 말합니다. 비디오 녹화는 연속 비디오 녹화 및 이벤트 기반 비디오 녹화로 분류될 수 있습니다. 

## <a name="continuous-video-recording"></a>연속 비디오 녹화  

CVR(연속 비디오 녹화)은 비디오 소스에서 캡처되는 모든 비디오를 지속적으로 녹화하는 프로세스를 말합니다. CVR은 모든 비디오를 나중에 분석 및/또는 감사할 수 있도록 합니다([녹화 정책](#recording-policy)에 따라 기록됨).

## <a name="event-based-video-recording"></a>이벤트 기반 비디오 녹화  

EVR(이벤트 기반 비디오 녹화)은 이벤트에 의해 트리거되는 비디오 녹화 프로세스를 말합니다. 문제의 이벤트는 비디오 신호 자체 처리로 인해 발생하거나 독립적인 소스(예: 도어 센서)에서 발생할 수 있습니다. EVR(이벤트 기반 비디오 녹화)은 스토리지 절감 효과를 가져올 수 있지만, 미리 정의된 정책에 따라 이벤트를 생성하고 비디오 녹화를 트리거하는 추가 구성 요소가 필요합니다. 즉, EVR을 사용하려면 비디오 녹화를 트리거해야 하는 이벤트와 관련된 추가 의사 결정을 수행하고 비디오 녹화와 관련된 정책(녹화 정책)을 결정해야 합니다. 정책의 예로는 이벤트 시간 30초 전부터 2분간 비디오를 녹화하는 것을 들 수 있습니다. 문제의 이벤트는 카메라 자체에서 비디오를 처리하기 때문에 발생할 수 있습니다. 예를 들어, 여러 카메라는 카메라 뷰포트에서 사전 구성된 관심 영역 내에서 동작이 감지될 때 동작 감지 신호 이벤트 생성을 지원합니다. 카메라에서 비디오를 캡처한 뒤 간단한 비디오 처리 기법이나 정교한 기계 학습 모델을 활용해 분석하는 다른 기기에서 비디오를 처리하여 이벤트를 생성할 수도 있습니다. 

## <a name="choosing-recording-modes"></a>녹화 모드 선택  

CVR 또는 EVR 중 무엇을 사용할지는 비즈니스 목표에 따라 달라집니다. IoT Edge의 Live Video Analytics는 CVR 및 EVR 모두에 대한 플랫폼 기능을 제공합니다. [CVR](continuous-video-recording-concept.md) 및 [EVR](event-based-video-recording-concept.md) 시나리오 문서에서 자세히 알아볼 수 있습니다.

## <a name="recording-policy"></a>녹화 정책  

녹화 정책은 녹화 시작/중지 시간(EVR의 경우), 녹화 기간 및 녹화 삭제를 지시하는 정책을 나타냅니다. 녹화 정책을 사용하면 스토리지 비용과 비즈니스 요구 사항 간의 균형을 유지할 수 있습니다. 녹화 정책은 CVR 및 EVR 간에 차이가 있습니다. CVR의 경우 녹화 정책은 비디오를 저장해야 하는 기간(예: 최근 7일)을 정의합니다. EVR의 경우 녹화 정책은 녹화를 시작하고 종료하는 시간을 비디오 기간과 함께 정의합니다. [CVR](continuous-video-recording-concept.md) 및 [EVR](event-based-video-recording-concept.md) 시나리오 문서에서 자세히 알아볼 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [동작을 감지하고 비디오 클립을 Azure Media Services에 기록](detect-motion-record-video-clips-media-services-quickstart.md)
* [동작을 감지하고 비디오 클립을 에지 디바이스에 기록](detect-motion-record-video-clips-edge-devices-quickstart.md)

