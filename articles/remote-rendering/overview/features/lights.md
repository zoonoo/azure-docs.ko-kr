---
title: 장면 조명
description: 광원 설명 및 속성
author: florianborn71
ms.author: flborn
ms.date: 02/10/2020
ms.topic: article
ms.openlocfilehash: 49027899d66a2192cc311fb4dba66e441155b527
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "92206852"
---
# <a name="scene-lighting"></a>장면 조명

기본적으로 원격 렌더링된 개체는 [하늘 광원](sky.md)을 사용하여 켜집니다. 대부분의 애플리케이션의 경우 이미 충분하지만 장면에 더 많은 광원을 추가할 수 있습니다.

> [!IMPORTANT]
> [PBR 재질](pbr-materials.md)만 광원의 영향을 받습니다. [색 재질](color-materials.md)은 항상 완전히 밝은 색으로 표시됩니다.

> [!NOTE]
> 캐스팅 그림자는 현재 지원되지 않습니다. Azure Remote Rendering은 필요한 경우 여러 GPU를 활용하여 엄청난 양의 기하 도형을 렌더링하도록 최적화되어 있습니다. 그림자 캐스팅에 대한 일반적인 접근 방식은 이러한 시나리오에서 제대로 작동하지 않습니다.

## <a name="common-light-component-properties"></a>일반 광원 구성 요소 속성

모든 광원 형식은 추상 기본 클래스 `LightComponent`에서 파생되고 다음 속성을 공유합니다.

* **색:** [감마 공간](https://en.wikipedia.org/wiki/SRGB)의 광원 색입니다. 알파는 무시됩니다.

* **강도:** 광원의 밝기입니다. 점 광원 및 집중 광원의 경우 강도는 빛이 얼마나 멀리 비추는지도 정의합니다.

## <a name="point-light"></a>점 광원

Azure 원격 렌더링에서 `PointLightComponent`는 단일 지점뿐만 아니라 작은 구 또는 작은 튜브에서도 빛을 발해 부드러운 광원을 시뮬레이션할 수 있습니다.

### <a name="pointlightcomponent-properties"></a>PointLightComponent 속성

* **Radius:** 기본 반지름은 0이며 이 경우 광원은 점 광원으로 작동합니다. 반지름이 0보다 크면 구면 광원으로 작동하여 반사 하이라이트의 모양이 변경됩니다.

* **Length:** `Length` 및 `Radius` 모두 0이 아닌 경우 광원은 튜브 광원으로 작동합니다. 이 속성은 네온 튜브를 시뮬레이션하는 데 사용할 수 있습니다.

* **AttenuationCutoff:** 광원의 감쇠를 (0,0)으로 두는 경우 오직 `Intensity`에 따라 달라집니다. 그러나 광원의 강도가 0까지 선형적으로 줄어드는 사용자 지정 최소/최대 거리를 제공할 수 있습니다. 이 기능을 사용하여 특정 광원의 영향을 더 작은 범위에 적용할 수 있습니다.

* **ProjectedCubemap:** 유효한 [큐브 맵](../../concepts/textures.md)으로 설정된 경우 광원의 주변 기하 도형으로 질감이 투영됩니다. 큐브 맵의 색은 광원의 색으로 조절됩니다.

## <a name="spot-light"></a>집중 광원

`SpotLightComponent`는 `PointLightComponent`와 유사하지만 광원이 원뿔 모양으로 제한됩니다. 원뿔의 방향은 *소유자 엔터티의 음수 z축* 에 의해 정의됩니다.

### <a name="spotlightcomponent-properties"></a>SpotLightComponent 속성

* **Radius:** `PointLightComponent`의 경우와 같습니다.

* **SpotAngleDeg:** 이 간격은 각도로 측정된 원뿔의 내부 및 외부 각도를 정의합니다. 내부 각도 내의 모든 항목은 최대 밝기로 조명됩니다. 외부 각도에는 대칭이 적용되어 반음영과 같은 효과를 생성합니다.

* **FalloffExponent:** 내부 및 외부 원뿔 각도 간의 대칭 전환에 대한 선명도를 정의합니다. 값이 높을수록 더 선명한 전환이 발생합니다. 기본값 1.0은 선형 전환을 발생합니다.

* **AttenuationCutoff:** `PointLightComponent`의 경우와 같습니다.

* **Projected2dTexture:** 유효한 [2D 질감](../../concepts/textures.md)으로 설정된 경우 이미지는 광원이 비추는 기하 도형으로 투영됩니다. 질감의 색이 광원의 색으로 조절됩니다.

## <a name="directional-light"></a>방향성 광원

`DirectionalLightComponent`는 무한히 떨어져 있는 광원을 시뮬레이션합니다. 광원이 *소유자 엔터티의 음의 z축* 방향으로 비춥니다. 엔터티의 위치는 무시됩니다.

추가 속성은 없습니다.

## <a name="performance-considerations"></a>성능 고려 사항

광원은 렌더링 성능에 상당한 영향을 줄 수 있습니다. 애플리케이션에 필요한 경우에만 신중하게 사용합니다. 추가 렌더링 비용 없이 [사용자 지정 하늘 질감](sky.md)으로 정적 방향성 구성 요소를 비롯한 모든 정적 전체 조명 조건을 얻을 수 있습니다.

## <a name="api-documentation"></a>API 설명서

* [C# LightComponentBase 클래스](/dotnet/api/microsoft.azure.remoterendering.lightcomponentbase)
* [C# PointLightComponent 클래스](/dotnet/api/microsoft.azure.remoterendering.pointlightcomponent)
* [C# SpotLightComponent 클래스](/dotnet/api/microsoft.azure.remoterendering.spotlightcomponent)
* [C# DirectionalLightComponent 클래스](/dotnet/api/microsoft.azure.remoterendering.directionallightcomponent)
* [C++ LightComponentBase 클래스](/cpp/api/remote-rendering/lightcomponentbase)
* [C++ PointLightComponent 클래스](/cpp/api/remote-rendering/pointlightcomponent)
* [C++ SpotLightComponent 클래스](/cpp/api/remote-rendering/spotlightcomponent)
* [C++ DirectionalLightComponent 클래스](/cpp/api/remote-rendering/directionallightcomponent)

## <a name="next-steps"></a>다음 단계

* [재질](../../concepts/materials.md)
* [Sky](sky.md)