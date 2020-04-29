---
title: 재질
description: 렌더링 재질 설명 및 재질 속성
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: conceptual
ms.openlocfilehash: 8551e17ddd71e76aca0c85b9768f564ae0e5f049
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80681845"
---
# <a name="materials"></a>재질

재질은 [메시](meshes.md) 렌더링 방법을 정의 하는 [공유 리소스](../concepts/lifetime.md) 입니다. 재질은 [개체를 투명](textures.md) 하 게 만들지 여부와 조명을 계산 하는 방법을 지정 하는 데 사용 됩니다.

재질은 [모델 변환](../how-tos/conversion/model-conversion.md) 중에 자동으로 생성 되며 런타임에 액세스할 수 있습니다. 코드에서 사용자 지정 자료를 만들어 기존 자료를 대체할 수도 있습니다. 이 시나리오는 여러 메시에서 동일한 자료를 공유 하려는 경우에 특히 적합 합니다. 재질을 참조 하는 모든 메시에서 재질 수정이 표시 되므로이 메서드를 사용 하 여 변경 내용을 쉽게 적용할 수 있습니다.

> [!NOTE]
> 선택 된 개체를 강조 표시 하는 것과 같은 일부 사용 사례는 자료를 수정 하 여 수행할 수 있지만 [HierarchicalStateOverrideComponent](../overview/features/override-hierarchical-state.md)를 통해 훨씬 더 쉽게 달성할 수 있습니다.

## <a name="material-types"></a>재질 유형

Azure 원격 렌더링에는 두 가지 고유한 재질 형식이 있습니다.

* [.Pbr 자료](../overview/features/pbr-materials.md) 는 가능한 한 물리적으로 올바르게 렌더링 되어야 하는 표면에 사용 됩니다. 실제 조명은 이러한 자료에 대해 .Pbr ( *물리적 기반 렌더링* )를 사용 하 여 계산 됩니다. 이 재질 유형을 최대한 활용 하려면 황삭 및 법선 맵과 같은 고품질의 입력 데이터를 제공 하는 것이 중요 합니다.

* [색 자료](../overview/features/color-materials.md) 는 추가 조명이 필요 하지 않은 경우에 사용 됩니다. 이러한 자료는 항상 완전 하 게 가득 차서 쉽게 설정할 수 있습니다. 색 재질은 조명이 없거나 [photogrammetry](https://en.wikipedia.org/wiki/Photogrammetry)을 통해 가져온 모델과 같이 고정 조명을 이미 통합 하는 데이터에 사용 됩니다.

## <a name="mesh-vs-meshcomponent-material-assignment"></a>메시와 MeshComponent 재질 할당 비교

[메시](meshes.md) 에는 하나 이상의 submeshes 있습니다. 각 하위 메시에 대해는 하나의 자료를 참조 합니다. 메시에서 직접 사용할 자료를 변경 하거나 [MeshComponent](meshes.md#meshcomponent)의 하위 메시에 대해에 사용할 자료를 재정의할 수 있습니다.

메시 리소스에서 재질을 직접 수정 하는 경우이 변경 내용은 해당 메시의 모든 인스턴스에 영향을 줍니다. 그러나 MeshComponent에 대 한 변경 내용은 하나의 메시 인스턴스에만 영향을 줍니다. 어떤 방법이 더 적합 한지는 원하는 동작에 따라 달라 지지만 MeshComponent을 수정 하는 것이 더 일반적인 방법입니다.

## <a name="material-classes"></a>재질 클래스

API에서 제공 하는 모든 자료는 기본 클래스 `Material`에서 파생 됩니다. 해당 형식은 직접 캐스팅 `Material.MaterialSubType` 하거나 직접 캐스팅 하 여 쿼리할 수 있습니다.

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

* [PBR 재질](../overview/features/pbr-materials.md)
* [색 재질](../overview/features/color-materials.md)
