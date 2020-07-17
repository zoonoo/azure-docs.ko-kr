---
title: 비디오 녹화-Azure
description: CCTV 카메라에 대 한 비디오 관리 시스템의 컨텍스트에서 비디오 기록은 카메라에서 비디오를 캡처하고 모바일 및 브라우저 앱을 통해 볼 수 있도록 기록 하는 프로세스를 나타냅니다. 비디오 기록은 연속 비디오 녹화 및 이벤트 기반 비디오 기록으로 분류 될 수 있습니다.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: c03472f577e67d5b2705a2ebe3f53e85e0849819
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84260196"
---
# <a name="video-recording"></a>비디오 녹화

CCTV 카메라에 대 한 비디오 관리 시스템의 컨텍스트에서 비디오 기록은 카메라에서 비디오를 캡처하고 모바일 및 브라우저 앱을 통해 볼 수 있도록 기록 하는 프로세스를 나타냅니다. 비디오 기록은 연속 비디오 녹화 및 이벤트 기반 비디오 기록으로 분류 될 수 있습니다. 

## <a name="continuous-video-recording"></a>연속 비디오 녹화  

CVR (연속 비디오 녹화)는 비디오 원본에서 캡처된 모든 비디오를 지속적으로 기록 하는 프로세스를 말합니다. CVR는 나중에 분석 및/또는 감사를 위해 모든 비디오를 사용할 수 있습니다 ( [기록 정책](#recording-policy)에 의해 결정 됨).

## <a name="event-based-video-recording"></a>이벤트 기반 비디오 녹화  

EVR(이벤트 기반 비디오 녹화)은 이벤트에 의해 트리거되는 비디오 녹화 프로세스를 말합니다. 문제의 이벤트는 비디오 신호 자체의 처리 나 독립적인 원본 (예: 도어 센서)에서 발생 했을 수 있습니다. EVR (이벤트 기반 비디오 기록)은 저장소를 절약할 수 있지만 이벤트를 생성 하 고 비디오 녹화를 트리거하는 추가 구성 요소 (미리 정의 된 정책에 따라)가 필요 합니다. 즉, EVR을 사용 하려면 비디오 기록을 트리거해야 하는 이벤트와 관련 된 추가 의사 결정을 수행 하 고 비디오 기록과 관련 된 정책 (기록 정책이 라고도 함)을 결정 해야 합니다. 정책의 예는 이벤트 시간 이전에 30 초에서 시작 하 여 2 분간 비디오를 기록 하는 것과 같을 수 있습니다. 문제의 이벤트는 카메라 자체에서 비디오를 처리 하기 때문에 발생할 수 있습니다. 예를 들어 카메라의 뷰포트에 미리 구성 된 영역 내에서 동작이 감지 되 면 몇 가지 카메라에서 동작 검색 신호 이벤트 생성을 지원 합니다. 카메라에서 비디오를 캡처하는 다른 장치에서 비디오를 처리 하 고 간단한 이미지 처리 기술 또는 정교한 기계 학습 모델을 사용 하 여 분석 하는 방법으로 이벤트를 생성할 수도 있습니다. 

## <a name="choosing-recording-modes"></a>기록 모드 선택  

CVR 또는 EVR 중 무엇을 사용할지는 비즈니스 목표에 따라 달라 집니다. IoT Edge의 Live Video Analytics는 CVR 및 EVR 모두에 대 한 플랫폼 기능을 제공 합니다. 이에 대 한 자세한 내용은 [Cvr](continuous-video-recording-concept.md) 및 [evr](event-based-video-recording-concept.md) 시나리오 문서에서 확인할 수 있습니다.

## <a name="recording-policy"></a>기록 정책  

기록 정책은 기록 시작/중지 시간 (EVR의 경우), 기록 기간 및 기록 삭제를 지시 하는 정책을 나타냅니다. 기록 정책을 사용 하면 비즈니스 요구 사항을 충족 하는 저장소 비용의 균형을 맞출 수 있습니다. 기록 정책은 CVR와 EVR 간에 차이가 있습니다. CVR의 경우 기록 정책은 비디오를 저장할 기간 (예: 지난 7 일)을 정의 합니다. EVR의 경우 기록 정책은 녹화를 시작 하 고 종료 하는 시간을 비디오 기간과 함께 정의 합니다. 이에 대 한 자세한 내용은 [Cvr](continuous-video-recording-concept.md) 및 [evr](event-based-video-recording-concept.md) 시나리오 문서에서 확인할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [동작을 감지하고 비디오 클립을 Azure Media Services에 기록](detect-motion-record-video-clips-media-services-quickstart.md)
* [동작 감지, 비디오 클립을에 지 장치에 기록](detect-motion-record-video-clips-edge-devices-quickstart.md)

