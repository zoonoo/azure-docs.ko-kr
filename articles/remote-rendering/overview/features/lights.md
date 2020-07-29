---
title: 장면 조명
description: 광원 설명 및 속성
author: florianborn71
ms.author: flborn
ms.date: 02/10/2020
ms.topic: article
ms.openlocfilehash: e33e012480c876dc5befbb93404bdb131ea9329a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84022149"
---
# <a name="scene-lighting"></a>장면 조명

기본적으로 원격으로 렌더링 된 개체는 [하늘 조명을](sky.md)사용 하 여 켜 집니다. 대부분의 응용 프로그램의 경우이는 이미 충분 하지만 장면에 더 많은 광원을 추가할 수 있습니다.

> [!IMPORTANT]
> [.Pbr 자료](pbr-materials.md) 만 광원의 영향을 받습니다. [색 재질](color-materials.md) 은 항상 완전히 밝은 색으로 표시 됩니다.

> [!NOTE]
> 현재 그림자는 지원 되지 않습니다. Azure 원격 렌더링은 필요한 경우 여러 Gpu를 활용 하 여 엄청난 양의 기 하 도형을 렌더링 하도록 최적화 되었습니다. 섀도 캐스팅에 대 한 일반적인 접근 방식은 이러한 시나리오에서 제대로 작동 하지 않습니다.

## <a name="common-light-component-properties"></a>일반 광원 구성 요소 속성

모든 광원 형식은 추상 기본 클래스에서 파생 되 `LightComponent` 고 다음 속성을 공유 합니다.

* **색:** [감마 공간의](https://en.wikipedia.org/wiki/SRGB)광원 색입니다. 알파는 무시 됩니다.

* **강도:** 조명의 밝기입니다. 점 및 스폿 광원의 경우 강도는 빛이 얼마나 떨어져 있는지를 정의 합니다.

## <a name="point-light"></a>점 광원

Azure 원격 렌더링에서는 `PointLightComponent` 단일 지점에서의 조명을 내보낼 수 있을 뿐만 아니라 작은 구 또는 작은 튜브에서 광원을 내보내 부드러운 광원을 시뮬레이션할 수 있습니다.

### <a name="pointlightcomponent-properties"></a>PointLightComponent 속성

* **Radius:** 기본 반지름은 0 이며,이 경우 광원이 점 빛의 역할을 합니다. 반지름이 0 보다 크면 반사 하이라이트의 모양을 변경 하는 구면 광원으로 작동 합니다.

* **길이:** `Length`및가 모두 `Radius` 0이 아니면 빛은 튜브 광원의 역할을 합니다. Neon 튜브를 시뮬레이션 하는 데 사용할 수 있습니다.

* **AttenuationCutoff:** 왼쪽에서 (0, 0) 조명의 감쇠는 해당에 따라 다릅니다 `Intensity` . 그러나 사용자 지정 최소/최대 거리를 제공할 수 있습니다 .이 거리는 조명의 농도가 0으로 선형적으로 조정 됩니다. 이 기능을 사용 하 여 특정 조명의 영향을 더 작은 범위에 적용할 수 있습니다.

* **ProjectedCubemap:** 유효한 [큐브 맵](../../concepts/textures.md)설정 된 경우 질감이 광원의 주변 기 하 도형에 투영 됩니다. 큐브 맵 색은 밝은 색으로 변조 됩니다.

## <a name="spot-light"></a>스폿 조명

는와 `SpotLightComponent` 유사 `PointLightComponent` 하지만 조명이 원뿔의 모양으로 제한 됩니다. 원뿔의 방향은 *소유자 엔터티의 음수 z 축*에 의해 정의 됩니다.

### <a name="spotlightcomponent-properties"></a>SpotLightComponent 속성

* **Radius:** 의 경우와 동일 `PointLightComponent` 합니다.

* **SpotAngleDeg:** 이 간격은 각도로 측정 된 원뿔의 내부 및 외부 각도를 정의 합니다. 내부 각도 내의 모든 항목에는 전체 밝기가 있습니다. Penumbra 같은 효과를 생성 하는 외부 각도에 대칭가 적용 됩니다.

* 고 **지 수:** 내부 및 외부 원뿔 각도 사이에서 대칭 전환이 얼마나 선명 하 게 전환 되는 정도를 정의 합니다. 값이 높을수록 더 선명한 전환이 발생 합니다. 기본값 1.0은 선형 전환을 발생 합니다.

* **AttenuationCutoff:** 의 경우와 동일 `PointLightComponent` 합니다.

* **Projected2dTexture:** 유효한 [2d 질감](../../concepts/textures.md)으로 설정 된 경우 이미지가 빛이 비치는 기 하 도형에 투영 됩니다. 질감의 색이 밝은 색으로 변조 됩니다.

## <a name="directional-light"></a>방향성 광원

는 `DirectionalLightComponent` 무한히 떨어져 있는 광원을 시뮬레이션 합니다. 빛은 *소유자 엔터티의 음의 z 축*방향에 해당 합니다. 엔터티의 위치는 무시 됩니다.

추가 속성이 없습니다.

## <a name="performance-considerations"></a>성능 고려 사항

광원은 렌더링 성능에 상당한 영향을 미칩니다. 응용 프로그램에 필요한 경우에만 신중 하 게 사용 합니다. 정적 방향성 구성 요소를 비롯 한 모든 정적 글로벌 조명 조건은 추가 렌더링 비용 없이 [사용자 지정 하늘 질감](sky.md)을 사용 하 여 달성할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [재질](../../concepts/materials.md)
* [Sky](sky.md)
