---
title: Azure IoT Central에서 규칙 및 작업 구성 | Microsoft Docs
description: 이 방법 문서에서는 작성기로 Azure IoT Central 응용 프로그램에서 원격 분석 기반 규칙 및 작업을 구성 하는 방법을 보여 줍니다.
author: vavilla
ms.author: vavilla
ms.date: 11/27/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: ca308522d0c6b9c3c5b39d73a2f4a278a5b20db8
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77026992"
---
# <a name="configure-rules"></a>규칙 구성



*이 문서는 운영자, 빌더 및 관리자에게 적용됩니다.*

IoT Central 규칙은 연결 된 장치에서 적극적으로 모니터링 되는 이벤트를 트리거하는 사용자 지정 가능한 응답 도구로 사용 됩니다. 다음 섹션에서는 규칙을 평가 하는 방법을 설명 합니다.

## <a name="select-target-devices"></a>대상 장치 선택

대상 장치 섹션을 사용 하 여이 규칙을 적용할 장치 종류를 선택 합니다. 필터를 사용 하 여 포함 해야 하는 장치를 세부적으로 조정할 수 있습니다. 필터는 장치 템플릿의 속성을 사용 하 여 장치 집합을 필터링 합니다. 필터 자체가 작업을 트리거하지 않습니다. 다음 스크린샷에서 대상으로 하는 장치는 장치 템플릿 유형 **냉동**입니다. 필터는 **제조 된 상태** 속성이 **워싱턴**과 같은 **Refrigerators** 포함 하는 규칙을 포함 합니다.

![조건](media/howto-configure-rules/filters.png)

## <a name="use-multiple-conditions"></a>여러 조건 사용

조건은 규칙이 트리거되는 조건입니다. 현재, 규칙에 여러 조건을 추가 하는 경우에는 논리적이 고 함께 사용할 수 있습니다. 즉, 규칙이 true로 평가 되려면 모든 조건을 충족 해야 합니다.  

다음 스크린샷에서 조건은 온도가 70&deg; F 보다 크고 습도가 10 보다 작은 경우를 확인 합니다. 두 문이 모두 true 이면 규칙이 true로 평가 되 고 작업을 트리거합니다.

![조건](media/howto-configure-rules/conditions.png)

## <a name="use-aggregate-windowing"></a>집계 창 사용

규칙은 집계 시간 창을 연속 windows로 평가 합니다. 아래 스크린샷에서 시간 창은 5 분입니다. 5 분 마다 규칙은 지난 5 분 동안의 데이터를 평가 합니다. 데이터는 해당 데이터가 해당 하는 창에서 한 번만 평가 됩니다.

![연속 창](media/howto-configure-rules/tumbling-window.png)

## <a name="use-rules-with-iot-edge-modules"></a>IoT Edge 모듈에서 규칙 사용

IoT Edge 모듈에 적용 되는 규칙에 제한이 적용 됩니다. 다른 모듈의 원격 분석에 대 한 규칙은 유효한 규칙으로 평가 되지 않습니다. 다음을 예로 들어 보겠습니다. 규칙의 첫 번째 조건은 모듈 A의 온도 원격 분석에 있습니다. 규칙의 두 번째 조건은 모듈 B의 습도 원격 분석에 있습니다. 두 조건은 서로 다른 모듈에 있으므로 잘못 된 조건 집합입니다. 규칙이 유효 하지 않으며 규칙을 저장 하는 동안 오류가 발생 합니다.

## <a name="next-steps"></a>다음 단계

Azure IoT Central 응용 프로그램에서 규칙을 구성 하는 방법을 배웠으므로 이제 다음을 수행할 수 있습니다.

> [!div class="nextstepaction"]
> [즉석에서 데이터 분석](howto-create-analytics.md)
