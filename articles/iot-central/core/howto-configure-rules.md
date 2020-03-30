---
title: Azure IoT Central에서 규칙 및 작업 구성 | Microsoft Docs
description: 이 방법 문서에서는 Azure IoT Central 응용 프로그램에서 원격 분석 기반 규칙 및 작업을 구성하는 방법을 작성자로 보여 줍니다.
author: vavilla
ms.author: vavilla
ms.date: 11/27/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 509f9557a8128df12353ad02a7c7db02b7b42631
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80158471"
---
# <a name="configure-rules"></a>규칙 구성



*이 문서는 운영자, 빌더 및 관리자에게 적용됩니다.*

IoT Central의 규칙은 연결된 장치에서 적극적으로 모니터링되는 이벤트를 트리거하는 사용자 지정 가능한 응답 도구역할을 합니다. 다음 섹션에서는 규칙을 평가하는 방법을 설명합니다.

## <a name="select-target-devices"></a>대상 장치 선택

대상 장치 섹션을 사용하여 이 규칙이 적용되는 장치 종류를 선택합니다. 필터를 사용하면 포함해야 할 장치를 더 구체화할 수 있습니다. 필터는 장치 템플릿의 속성을 사용하여 장치 집합을 필터링합니다. 필터 자체는 작업을 트리거하지 않습니다. 다음 스크린샷에서 대상되는 장치는 장치 템플릿 유형 **냉장고입니다.** 필터는 규칙에 **제조 된 주** **속성이 워싱턴과** 동일한 **Washington**냉장고만 포함해야한다고 명시합니다.

![조건](media/howto-configure-rules/filters.png)

## <a name="use-multiple-conditions"></a>여러 조건 사용

조건은 규칙이 트리거하는 규칙입니다. 현재 규칙에 여러 조건을 추가하면 논리적으로 함께 사용할 수 있습니다. 즉, 규칙이 true로 평가하려면 모든 조건을 충족해야 합니다.  

다음 스크린샷에서는 온도가 70F보다 크고&deg; 습도가 10미만일 때 조건을 확인합니다. 이 두 문이 모두 true이면 규칙은 true로 평가하고 작업을 트리거합니다.

![조건](media/howto-configure-rules/conditions.png)

## <a name="use-aggregate-windowing"></a>집계 창 사용

규칙은 집계 시간 창을 텀블링 창으로 평가합니다. 아래 스크린샷에서 시간 창은 5분입니다. 이 규칙은 5분마다 데이터의 마지막 5분에 대해 평가합니다. 데이터는 해당 창에서 한 번만 평가됩니다.

![연속 창](media/howto-configure-rules/tumbling-window.png)

## <a name="use-rules-with-iot-edge-modules"></a>IoT 에지 모듈에서 규칙 사용

IoT Edge 모듈에 적용되는 규칙에는 제한이 적용됩니다. 다른 모듈의 원격 분석 규칙은 유효한 규칙으로 평가되지 않습니다. 다음을 예로 들어 보겠습니다. 규칙의 첫 번째 조건은 모듈 A의 온도 원격 분석입니다. 규칙의 두 번째 조건은 모듈 B의 습도 원격 분석입니다. 두 조건은 서로 다른 모듈에서 온 상태이므로 잘못된 조건 집합입니다. 규칙이 유효하지 않으며 규칙을 저장하려고 할 때 오류가 발생합니다.

## <a name="next-steps"></a>다음 단계

Azure IoT Central 응용 프로그램에서 규칙을 구성하는 방법을 배웠으니 다음을 수행할 수 있습니다.

> [!div class="nextstepaction"]
> [즉석에서 데이터 분석](howto-create-analytics.md)
