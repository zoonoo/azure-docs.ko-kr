---
title: 프레스 넬 대칭 효과
description: 프레스 넬 대칭 재질 효과에 대 한 기능 설명 페이지
author: jumeder
ms.author: jumeder
ms.date: 11/09/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: 0596d118d1f3c09bc295891f023fe9990f3f1e05
ms.sourcegitcommit: dc342bef86e822358efe2d363958f6075bcfc22a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94557921"
---
# <a name="fresnel-effect"></a>프레스 넬 대칭 효과

프레스 넬 대칭 효과 재질 기능은 실제적이 고 애드혹 효과입니다. 이 기능은 개체의 물리적인 관찰을 기반으로 하 여 이러한 각도에 반사를 추가 합니다. 프레스 넬 대칭 reflectance 자체는 이미 Azure 원격 렌더링에 사용 되는 [.pbr 재질 모델](../../overview/features/pbr-materials.md) 에 통합 되어 있습니다. 이와 대조적으로 프레스 넬 대칭 효과 재질 기능은 [조명이](../../overview/features/lights.md) 나 [하늘 환경](../../overview/features/sky.md)에 의존 하지 않는 추가 색 효과입니다.

프레스 넬 대칭 효과는 영향을 받는 개체에 가장자리 주위를 색으로 제공 합니다. 효과 사용자 지정 및 렌더링 결과 예제에 대 한 정보는 다음 섹션에서 찾을 수 있습니다.

## <a name="enabling-the-fresnel-effect"></a>프레스 넬 대칭 효과 사용

프레스 넬 대칭 효과 기능을 사용 하려면 해당 자료를 사용 하도록 설정 해야 합니다. FresnelEffect 비트를 [.pbr 재질](../../overview/features/pbr-materials.md) [에 설정](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.pbrmaterialfeatures) 하 여 사용 하도록 설정할 수 있습니다. [ColorMaterialFeatures](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering) 및 [색 재질](../../overview/features/color-materials.md)에도 동일한 패턴이 적용 됩니다. 사용 데모는 코드 샘플 섹션을 참조 하세요.

을 사용 하도록 설정 하면 프레스 넬 대칭 효과가 즉시 표시 됩니다. 기본적으로, 빛은 흰색 (1, 1, 1, 1) 이며 지 수는 1입니다. 아래 매개 변수 setter를 사용 하 여 이러한 설정을 사용자 지정할 수 있습니다.

## <a name="customizing-the-effect-appearance"></a>효과 모양 사용자 지정

현재 다음 속성을 사용 하 여 프레스 넬 대칭 효과를 재질 별로 사용자 지정할 수 있습니다.

| 재질 속성 | Type | 설명 |
|-------------------|------|-------------|
| FresnelEffectColor | Color4 | 최대 프레스 넬 대칭 빛에 추가 되는 색입니다. 알파 채널은 현재 무시 됩니다. |
| FresnelEffectExponent | float | 프레스 넬 대칭 빛의 확산입니다. 0.01 (모든 개체에 분산)에서 10 (가장 gracing 각도)까지 범위를 가집니다. |

실제로 다른 색 및 지 수 설정은 다음과 같이 표시 됩니다.

![프레스 넬 대칭 효과 예제](./media/fresnel-effect-examples.png)

각 색 행에 대해 프레스 넬 대칭 효과의 지수가 점차 1 ~ 10으로 증가 합니다. 이렇게 하면 프레스 넬 대칭 빛이 표시 된 개체의 가장자리로 점진적으로 끌어옵니다. 프레스 넬 대칭 효과는 다음 예제에서 볼 수 있듯이 투명도의 영향을 받지 않습니다.

![프레스 넬 대칭 효과 투명도 예](./media/fresnel-effect-transparent-examples.png)

표시 된 것 처럼 대각선의 개체는 완전히 투명 하지만 프레스 넬 대칭 빛은 그대로 유지 됩니다. 효과는 이러한 스크린샷에도 있는 것과 관련 하 여 물리적으로 프레스 넬 대칭 하는 것과 유사 합니다.

## <a name="code-samples"></a>코드 샘플

다음 코드 샘플에서는 프레스 넬 대칭 효과를 설정 하 고 사용자 지정 [하는 방법을](../../overview/features/pbr-materials.md) 보여 [줍니다.](../../overview/features/color-materials.md)

```cs
    void SetFresnelEffect(AzureSession session, Material material)
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
void SetFresnelEffect(ApiHandle<AzureSession> session, ApiHandle<Material> material)
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

* [C # PbrMaterialFeatures](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.pbrmaterialfeatures)
* [C + + PbrMaterialFeatures](https://docs.microsoft.com/cpp/api/remote-rendering/pbrmaterialfeatures)
* [C # ColorMaterialFeatures](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.colormaterialfeatures)
* [C + + ColorMaterialFeatures](https://docs.microsoft.com/cpp/api/remote-rendering/colormaterialfeatures)

## <a name="next-steps"></a>다음 단계

* [재질](../../concepts/materials.md)
* [.PBR 자료](../../overview/features/pbr-materials.md)
* [색 재질](../../overview/features/color-materials.md)