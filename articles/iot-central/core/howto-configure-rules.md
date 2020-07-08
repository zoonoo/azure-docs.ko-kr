---
title: Azure IoT Central에서 규칙 및 작업 구성 | Microsoft Docs
description: 이 방법 문서에서는 Azure IoT Central 애플리케이션에서 원격 분석 기반 규칙 및 작업을 구성하는 방법을 보여 줍니다.
author: vavilla
ms.author: vavilla
ms.date: 11/27/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: c4d0639831d2f6f60a719637c5158fba5caf6f43
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83659359"
---
# <a name="configure-rules"></a>규칙 구성

*이 문서는 운영자, 빌더 및 관리자에게 적용됩니다.*

IoT Central 규칙은 연결된 디바이스에서 능동적으로 모니터링되는 이벤트를 트리거하는 사용자 지정 가능한 응답 도구로 기능합니다. 이어지는 섹션에서는 규칙이 평가되는 방법을 설명합니다.

## <a name="select-target-devices"></a>대상 디바이스 선택

대상 디바이스 섹션을 사용하여 이 규칙을 적용할 디바이스 유형을 선택합니다. 필터를 사용하여 포함해야 하는 디바이스를 세부적으로 조정할 수 있습니다. 필터는 디바이스 템플릿의 속성을 사용하여 디바이스 세트를 필터링합니다. 필터 자체적으로는 작업을 트리거하지 않습니다. 다음 스크린샷에서 대상으로 지정되는 디바이스는 템플릿 유형 **Refrigerator**(냉장고)입니다. 이 필터는 **Manufactured State**(제조됨 상태) 속성이 **Washington**(워싱턴)과 일치하는 경우에만 규칙에 **Refrigerators**(냉장고)를 포함해야 한다고 명시하고 있습니다.

![조건](media/howto-configure-rules/filters.png)

## <a name="use-multiple-conditions"></a>여러 조건 사용

조건은 규칙이 트리거되는 상황을 가리킵니다. 현재 규칙에 여러 조건을 추가하면 여러 규칙에 논리 AND가 적용됩니다. 즉, 이 규칙이 참으로 평가되려면 모든 조건이 충족되어야 합니다.  

다음 스크린샷에서 조건은 온도가 70&deg;F보다 크고 습도가 10보다 작은지를 확인합니다. 두 문이 모두 참이면 규칙이 참으로 평가되고 작업이 트리거됩니다.

![조건](media/howto-configure-rules/conditions.png)

### <a name="use-a-cloud-property-in-a-value-field"></a>값 필드에 클라우드 속성 사용

디바이스 템플릿에서 조건의 **값** 필드에 있는 클라우드 속성을 참조할 수 있습니다. 클라우드 속성 및 원격 분석 값은 형식이 동일해야 합니다. 예를 들어 **온도**가 double형이라면 **값** 드롭다운에 double형의 클라우드 속성만 옵션으로 표시됩니다.

이벤트 유형 원격 분석 값을 선택할 경우 **값** 드롭다운에는 **Any**(모두) 옵션이 포함됩니다. **Any**(모두) 옵션은 애플리케이션이 해당 형식의 이벤트를 받을 때 (페이로드와 관계없이) 규칙이 실행됨을 의미합니다.

## <a name="use-aggregate-windowing"></a>집계 창 사용

규칙은 집계 시간 창을 연속 창으로 평가합니다. 아래 스크린샷에서 시간 창은 5분입니다. 규칙은 5분에 한 번씩 지난 5분 동안의 데이터를 평가합니다. 데이터는 해당 데이터가 해당 창에 있을 때만 평가됩니다.

![연속 창](media/howto-configure-rules/tumbling-window.png)

## <a name="use-rules-with-iot-edge-modules"></a>IoT Edge 모듈에서 규칙 사용

IoT Edge 모듈에 적용되는 규칙에는 제한이 적용됩니다. 다른 모듈의 원격 분석에 적용되는 규칙은 유효한 규칙으로 평가되지 않습니다. 다음 예제를 살펴보세요. 규칙의 첫 번째 조건은 모듈 A의 온도 원격 분석에 적용됩니다. 규칙의 두 번째 조건은 모듈 B의 습도 원격 분석에 적용됩니다. 두 조건은 서로 다른 모듈에 적용되므로 이는 잘못된 조건 세트입니다. 규칙이 유효하지 않으며 규칙을 저장하려고 시도하면 오류가 발생합니다.

## <a name="next-steps"></a>다음 단계

Azure IoT Central 애플리케이션에서 규칙을 구성하는 방법을 알아보았으니 이제 Power Automate 또는 Azure Logic Apps를 사용하여 [고급 규칙을 구성](howto-configure-rules-advanced.md)하는 방법을 알아볼 수 있습니다.
