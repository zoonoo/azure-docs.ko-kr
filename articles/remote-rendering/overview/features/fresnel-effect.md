---
title: Fresnel 효과
description: 프레넬 재질 효과에 대한 기능 설명 페이지
author: jumeder
ms.author: jumeder
ms.date: 11/09/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: f63cd3b50642c3cf531387b4446992b6f15116f2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "99594115"
---
# <a name="fresnel-effect"></a>Fresnel 효과

프레넬 효과 재질 기능은 실제 정확하지 않은 임시 효과입니다. 이 기능은 이러한 각도에서 더 반사되는 개체의 실제 관찰을 기반으로 합니다. 프레넬 반사 자체는 Azure Remote Rendering에서 사용되는 [PBR 재질 모델](../../overview/features/pbr-materials.md)에 이미 통합되어 있습니다. 이와 대조적으로 프레넬 효과 재질 기능은 [광원](../../overview/features/lights.md)이나 [하늘 환경](../../overview/features/sky.md)에 의존하지 않는 가색 효과입니다.

프레넬 효과는 영향을 받는 개체의 가장자리에서 색상이 지정된 빛을 냅니다. 효과 사용자 지정 및 렌더링 결과 예에 대한 정보는 다음 섹션에서 찾을 수 있습니다.

## <a name="enabling-the-fresnel-effect"></a>프레넬 효과 사용

프레넬 효과 기능을 사용하려면 해당 재질을 사용하도록 설정해야 합니다. FresnelEffect 비트를 [PBR 재질](../../overview/features/pbr-materials.md)에서 [PbrMaterialFeatures](/dotnet/api/microsoft.azure.remoterendering.pbrmaterialfeatures)의 FresnelEffect 비트를 설정하면 될 수 있습니다. [ColorMaterialFeatures](/dotnet/api/microsoft.azure.remoterendering) 및 [색 재질](../../overview/features/color-materials.md)에도 동일한 패턴이 적용됩니다. 사용 데모는 코드 샘플 섹션을 참조하세요.

프레넬 효과는 사용하도록 설정하는 즉시 표시됩니다. 기본적으로 빛은 흰색(1, 1, 1, 1)이고 지수는 1입니다. 아래 매개 변수 setter를 사용하여 이러한 설정을 사용자 지정할 수 있습니다.

## <a name="customizing-the-effect-appearance"></a>효과 모양 사용자 지정

현재 다음 속성을 사용하여 프레넬 효과를 재질마다 사용자 지정할 수 있습니다.

| 재질 속성 | Type | 설명 |
|-------------------|------|-------------|
| FresnelEffectColor | Color4 | 최대한 많은 프레넬 빛이 추가된 색입니다. 알파 채널은 현재 무시됩니다. |
| FresnelEffectExponent | float | 프레넬 빛의 확산. 0\.01(모든 개체에 분산)에서 10(가장 화려한 각도만)까지의 범위를 갖습니다. |

실제로 다른 색 및 지수 설정은 다음과 같이 표시됩니다.

![프레넬 효과 예](./media/fresnel-effect-examples.png)

각 색상 행에 대해 프레넬 효과의 지수가 1에서 10으로 점차 증가합니다. 이렇게 하면 프레넬 빛을 표시된 개체의 가장자리로 점진적으로 끌어옵니다. 프레넬 효과는 다음 예에서 볼 수 있듯이 투명도의 영향을 받지 않습니다.

![프레넬 효과 투명도 예](./media/fresnel-effect-transparent-examples.png)

표시된 것처럼 대각선의 개체는 완전히 투명하지만 프레넬 빛은 그대로 유지됩니다. 효과는 이와 관련하여 이러한 스크린샷에도 있는 물리적 기반 프레넬과 유사합니다.

## <a name="code-samples"></a>코드 샘플

다음 코드 샘플은 [PBR 재질](../../overview/features/pbr-materials.md) 및 [색 재질](../../overview/features/color-materials.md)에 대해 프레넬 효과를 설정하고 사용자 지정하는 방법을 보여 줍니다.

```cs
    void SetFresnelEffect(RenderingSession session, Material material)
    {
        if (material.MaterialSubType == MaterialType.Pbr)
        {
            var pbrMaterial = material as PbrMaterial;
            pbrMaterial.PbrFlags |= PbrMaterialFeatures.FresnelEffect;
            pbrMaterial.FresnelEffectColor = new Color4(1.0f, 0.5f, 0.1f, 1.0f);
            pbrMaterial.FresnelEffectExponent = 3.141592f;
        }
        else if (material.MaterialSubType == MaterialType.Color)
        {
            var colorMaterial = material as ColorMaterial;
            colorMaterial.ColorFlags |= ColorMaterialFeatures.FresnelEffect;
            colorMaterial.FresnelEffectColor = new Color4(0.25f, 1.0f, 0.25f, 1.0f);
            colorMaterial.FresnelEffectExponent = 7.654321f;
        }
    }
```

```cpp
void SetFresnelEffect(ApiHandle<RenderingSession> session, ApiHandle<Material> material)
{
    if (material->GetMaterialSubType() == MaterialType::Pbr)
    {
        auto pbrMaterial = material.as<PbrMaterial>();
        auto featureFlags = PbrMaterialFeatures((int32_t)pbrMaterial->GetPbrFlags() | (int32_t)PbrMaterialFeatures::FresnelEffect);
        pbrMaterial->SetPbrFlags(featureFlags);
        pbrMaterial->SetFresnelEffectColor(Color4{ 1.f, 0.5f, 0.1f, 1.f });
        pbrMaterial->SetFresnelEffectExponent(3.141592f);

    }
    else if (material->GetMaterialSubType() == MaterialType::Color)
    {
        auto colorMaterial = material.as<ColorMaterial>();
        auto featureFlags = ColorMaterialFeatures((int32_t)colorMaterial->GetColorFlags() | (int32_t)ColorMaterialFeatures::FresnelEffect);
        colorMaterial->SetColorFlags(featureFlags);
        colorMaterial->SetFresnelEffectColor(Color4{ 0.25f, 1.f, 0.25f, 1.f });
        colorMaterial->SetFresnelEffectExponent(7.654321f);
    }
}
```

## <a name="api-documentation"></a>API 설명서

* [C# PbrMaterialFeatures](/dotnet/api/microsoft.azure.remoterendering.pbrmaterialfeatures)
* [C++ PbrMaterialFeatures](/cpp/api/remote-rendering/pbrmaterialfeatures)
* [C# ColorMaterialFeatures](/dotnet/api/microsoft.azure.remoterendering.colormaterialfeatures)
* [C++ ColorMaterialFeatures](/cpp/api/remote-rendering/colormaterialfeatures)

## <a name="next-steps"></a>다음 단계

* [재질](../../concepts/materials.md)
* [PBR 재질](../../overview/features/pbr-materials.md)
* [색 재질](../../overview/features/color-materials.md)