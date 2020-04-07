---
title: 라이트
description: 광원 설명 및 특성
author: florianborn71
ms.author: flborn
ms.date: 02/10/2020
ms.topic: article
ms.openlocfilehash: 0a4a226af1347b5302b0c3964889fc072f89e7f8
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680948"
---
# <a name="lights"></a>라이트

기본적으로 원격으로 렌더링된 오브젝트는 [하늘광을](sky.md)사용하여 점등됩니다. 대부분의 응용 분야에서는 이미 충분하지만 장면에 광원을 추가할 수 있습니다.

> [!IMPORTANT]
> [PBR 재료만](pbr-materials.md) 광원의 영향을 받습니다. [색상 재질은](color-materials.md) 항상 완전히 밝게 나타납니다.

> [!NOTE]
> 그림자 를 캐스팅하는 것은 현재 지원되지 않습니다. Azure 원격 렌더링은 필요한 경우 여러 GPU를 활용하여 엄청난 양의 형상을 렌더링하도록 최적화되어 있습니다. 이러한 시나리오에서는 섀도 캐스팅에 대한 기존 접근 방식이 잘 작동하지 않습니다.

## <a name="common-light-component-properties"></a>공통 광 부품 특성

모든 라이트 형식은 추상 `LightComponent` 기본 클래스에서 파생되며 다음 속성을 공유합니다.

* **색상:** [감마 공간에서](https://en.wikipedia.org/wiki/SRGB)빛의 색상. 알파는 무시됩니다.

* **강도:** 빛의 밝기입니다. 점 및 스폿 라이트의 경우 강도는 라이트가 얼마나 멀리 비추는지도 정의합니다.

## <a name="point-light"></a>포인트 라이트

Azure 원격 렌더링에서는 단일 지점에서 빛을 방출할 뿐만 아니라 작은 구 나 작은 튜브에서 빛을 방출하여 부드러운 광원을 시뮬레이션 할 `PointLightComponent` 수 있습니다.

### <a name="pointlightcomponent-properties"></a>포인트라이트컴포넌트 속성

* **반지름:** 기본 반지름은 0이며, 이 경우 라이트는 점 라이트역할을 합니다. 반지름이 0보다 크면 구형 광원으로 작동하여 반사 하이라이트의 모양을 변경합니다.

* **길이:** 둘 `Length` 다 `Radius` 0이 아닌 경우 라이트는 튜브 라이트역할을 합니다. 이것은 네온 튜브를 시뮬레이션하는 데 사용할 수 있습니다.

* **감쇠 컷오프:** (0,0)로 남겨두면 라이트의 감쇠는 에 따라 `Intensity`달라집니다. 그러나 라이트의 강도가 0으로 선형으로 조정되는 사용자 지정 최소/최대 거리를 제공할 수 있습니다. 이 기능은 특정 라이트의 영향 범위를 더 작게 적용하는 데 사용할 수 있습니다.

* **프로젝스큐브맵:** 유효한 [큐브맵으로](../../concepts/textures.md)설정하면 텍스처가 라이트의 주변 형상에 투영됩니다. 큐브맵의 색상은 라이트의 색상으로 변조됩니다.

## <a name="spot-light"></a>스포트 라이트

는 `SpotLightComponent` 라이트와 `PointLightComponent` 유사하지만 라이트는 원뿔 모양으로 제한됩니다. 원뿔의 방향은 *소유자 엔티티의 음수 z축으로*정의됩니다.

### <a name="spotlightcomponent-properties"></a>스포트라이트 컴포넌트 속성

* **반지름:** `PointLightComponent`와 동일 합니다.

* **스팟앵글데그:** 이 간격은 원뿔의 내부 및 외부 각도를 어느 정도 측정하여 정의합니다. 내부 각도 내의 모든 것이 전체 밝기로 비춰집니다. 폴오프는 음경과 같은 효과를 생성하는 외부 각도쪽으로 적용됩니다.

* **폴오프엑스포니트:** 내부와 외부 원뿔 각도 사이의 폴오프 전환이 얼마나 급격하게 되는지 정의합니다. 값이 높을수록 전환이 더 선명해집니다. 기본값이 1.0이면 선형 전환이 발생합니다.

* **감쇠 컷오프:** `PointLightComponent`와 동일 합니다.

* **투영된2d텍스처:** 유효한 [2D 텍스처로](../../concepts/textures.md)설정하면 이미지가 빛이 비추는 형상에 투영됩니다. 텍스처의 색상은 라이트의 색상으로 변조됩니다.

## <a name="directional-light"></a>방향 라이트

무한히 `DirectionalLightComponent` 멀리 떨어진 광원을 시뮬레이션합니다. 빛은 *소유자 엔티티의 음수 z축*방향으로 빛납니다. 엔터티의 위치는 무시됩니다.

추가 속성이 없습니다.

## <a name="performance-considerations"></a>성능 고려 사항

광원은 렌더링 성능에 큰 영향을 미칩니다. 응용 프로그램에서 필요한 경우에만 신중하게 사용하십시오. 정적 방향 구성 요소를 포함한 모든 정적 전역 조명 조건은 추가 렌더링 비용 없이 [사용자 지정 하늘 텍스처로](sky.md)수행할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [원자재](../../concepts/materials.md)
* [하늘](sky.md)
