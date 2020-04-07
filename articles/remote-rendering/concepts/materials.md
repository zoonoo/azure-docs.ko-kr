---
title: 원자재
description: 재료 설명 및 재질 특성 렌더링
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: conceptual
ms.openlocfilehash: 8551e17ddd71e76aca0c85b9768f564ae0e5f049
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681845"
---
# <a name="materials"></a>원자재

재질은 메시 렌더링 방법을 정의하는 [공유](../concepts/lifetime.md) [리소스입니다.](meshes.md) 재질은 적용할 [텍스처,](textures.md) 오브젝트를 투명하게 만들지 여부 및 조명계산 방법을 지정하는 데 사용됩니다.

재질은 모델 [변환](../how-tos/conversion/model-conversion.md) 중에 자동으로 생성되며 런타임에 액세스할 수 있습니다. 코드에서 사용자 지정 재질을 만들고 기존 재질을 대체할 수도 있습니다. 이 시나리오는 여러 메시간에 동일한 재질을 공유하려는 경우에 특히 의미가 있습니다. 재질을 참조하는 모든 메시에 재질 수정이 표시되므로 이 방법을 사용하여 변경 사항을 쉽게 적용할 수 있습니다.

> [!NOTE]
> 선택한 오브젝트를 강조 표시하는 것과 같은 일부 사용 사례는 재질을 수정하여 수행할 수 있지만 [계층적StateOverride 구성 요소를](../overview/features/override-hierarchical-state.md)통해 훨씬 쉽게 달성할 수 있습니다.

## <a name="material-types"></a>재질 유형

Azure 원격 렌더링에는 두 가지 재질 유형이 있습니다.

* [PBR 재질은](../overview/features/pbr-materials.md) 가능한 한 물리적으로 올바르게 렌더링되어야 하는 표면에 사용됩니다. 물리적 기반 렌더링(PBR)을 사용하여 이러한 재질에 대해 사실적인 조명이 계산됩니다. *physically based rendering* 이 재질 유형을 최대한 사용하려면 거칠기 및 노멀 맵과 같은 고품질 입력 데이터를 제공하는 것이 중요합니다.

* [색상 재료는](../overview/features/color-materials.md) 추가 조명이 필요하지 않은 경우에 사용됩니다. 이러한 재료는 항상 전체 밝고 설정하기 쉽습니다. 색상 재질은 조명이 전혀 없거나 [사진 측정을](https://en.wikipedia.org/wiki/Photogrammetry)통해 얻은 모델과 같은 정적 조명을 이미 통합한 데이터에 사용됩니다.

## <a name="mesh-vs-meshcomponent-material-assignment"></a>메시 대 메시 구성요소 재질 할당

[메시에는](meshes.md) 하나 이상의 하위 mesheshes가 있습니다. 각 하위 메시는 하나의 재질을 참조합니다. 메시에서 직접 사용할 재질을 변경하거나 [메시 구성 요소의](meshes.md#meshcomponent)하위 메시에 사용할 재질을 재정의할 수 있습니다.

메시 리소스에서 직접 재질을 수정하면 이 변경 사항은 해당 메시의 모든 인스턴스에 영향을 줍니다. 그러나 메시 구성 요소에서 변경하면 하나의 메시 인스턴스에만 영향을 줍니다. 더 적합한 방법은 원하는 동작에 따라 다르지만 MeshComponent를 수정하는 것이 더 일반적인 방법입니다.

## <a name="material-classes"></a>재질 클래스

API에서 제공하는 모든 자료는 기본 `Material`클래스에서 파생됩니다. 해당 형식은 직접 `Material.MaterialSubType` 캐스팅하거나 캐스팅하여 쿼리할 수 있습니다.

``` cs
void SetMaterialColorToGreen(Material material)
{
    if (material.MaterialSubType == MaterialType.Color)
    {
        ColorMaterial colorMaterial = material as ColorMaterial;
        colorMaterial.AlbedoColor = new Color4(0, 1, 0, 1);
        return;
    }

    PbrMaterial pbrMat = material as PbrMaterial;
    if( pbrMat!= null )
    {
        PbrMaterial pbrMaterial = material.PbrMaterial.Value;
        pbrMaterial.AlbedoColor = new Color4(0, 1, 0, 1);
        return;
    }
}
```

## <a name="next-steps"></a>다음 단계

* [PBR 재료](../overview/features/pbr-materials.md)
* [색상 재료](../overview/features/color-materials.md)
