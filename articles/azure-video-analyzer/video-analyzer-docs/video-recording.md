---
title: Azure Video Analyzer로 재생하기 위해 비디오 녹화
description: 이 문서에서는 Azure Video Analyzer로 재생하기 위해 비디오를 녹화하는 방법을 설명합니다.
ms.service: azure-video-analyzer
ms.topic: conceptual
ms.date: 06/01/2021
ms.openlocfilehash: 4c5d0121df90791d8eb77782fb6d949548a0185e
ms.sourcegitcommit: 3941df51ce4fca760797fa4e09216fcfb5d2d8f0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2021
ms.locfileid: "114604359"
---
# <a name="record-video-for-playback"></a>재생을 위해 비디오 녹화

CCTV 카메라용 비디오 관리 시스템 컨텍스트에서 비디오 녹화는 카메라에서 영상을 캡처해 모바일과 브라우저 앱을 통해 나중에 볼 수 있도록 기록하는 과정을 말합니다. 비디오 녹화는 연속 비디오 녹화 및 이벤트 기반 비디오 녹화로 분류될 수 있습니다.

## <a name="continuous-video-recording"></a>연속 비디오 녹화

CVR(연속 비디오 녹화)은 비디오 소스에서 캡처되는 모든 비디오를 지속적으로 녹화하는 프로세스를 말합니다. CVR은 원하는 비디오 기간을 나중에 분석 및/또는 감사할 수 있도록 합니다( **[녹화 정책](#recording-policy)** 에 따라 기록됨).


## <a name="event-based-video-recording"></a>이벤트 기반 비디오 녹화

EVR(이벤트 기반 비디오 녹화)은 이벤트에 의해 트리거되는 비디오 녹화 프로세스를 말합니다. 문제의 이벤트는 비디오 신호 자체 처리(예: 동작 감지)로 인해 발생하거나 독립적인 소스(예: 도어 센서)에서 발생할 수 있습니다. EVR은 스토리지 절감 효과를 가져올 수 있지만, 이벤트를 생성하고 비디오 녹화를 트리거하는 추가 구성 요소가 필요합니다. 즉, EVR을 사용하려면 비디오 녹화를 트리거해야 하는 이벤트 및 비디오 녹화 기간과 관련된 추가 의사 결정을 수행해야 합니다. 이벤트 시간 30초 전부터 2분간 비디오를 녹화하는 경우를 예로 들 수 있습니다.

## <a name="choosing-recording-modes"></a>녹화 모드 선택

CVR 또는 EVR 중 무엇을 사용할지는 비즈니스 목표에 따라 달라집니다. AVA(Azure Video Analyzer)는 CVR 및 EVR 모두에 대한 플랫폼 기능을 제공합니다. **[CVR](continuous-video-recording.md)** 및 **[EVR](event-based-video-recording-concept.md)** 시나리오 문서에서 자세히 알아볼 수 있습니다.

## <a name="recording-policy"></a>녹화 정책

녹화 정책은 유지되는 비디오 녹화의 길이 또는 기간을 지정하는 정책을 나타냅니다. 녹화 정책을 사용하면 스토리지 비용과 비즈니스 요구 사항 간의 균형을 유지할 수 있습니다. CVR의 경우 녹화 정책은 비디오를 저장해야 하는 기간(예: 최근 7일)을 정의합니다. **[CVR](continuous-video-recording.md)** 시나리오 문서에서 자세히 알아볼 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [EVR 시나리오에 대해 알아보기](event-based-video-recording-concept.md)
- [CVR 시나리오에 대해 알아보기](continuous-video-recording.md)
