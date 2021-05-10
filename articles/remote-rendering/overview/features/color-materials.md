---
title: 색 재질
description: 색 재질 유형을 설명합니다.
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: fb26a669229ac140aba262032f8ce84ef9f37727
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "99593386"
---
# <a name="color-materials"></a>색 재질

색 재질은 Azure Remote Rendering에서 지원되는 [재질 유형](../../concepts/materials.md) 중 하나입니다. 어떤 종류의 조명도 받지 않아야 하고 항상 전체 밝기여야 하는 [메시](../../concepts/meshes.md)에 사용됩니다. 자동차 대시보드, 전구 등의 '반짝이는' 재질 또는 [사진 측량](https://en.wikipedia.org/wiki/Photogrammetry)을 통해 얻은 모델과 같은 고정 조명을 이미 포함하는 데이터가 여기에 해당될 수 있습니다.

색 재질은 더 간단한 음영 모델 때문에 [PBR 재질](pbr-materials.md)보다 효율적으로 렌더링할 수 있습니다. 또한 다양한 투명도 모드도 지원합니다.

## <a name="common-material-properties"></a>공통 재질 속성

다음 속성은 모든 재질에 공통됩니다.

* **albedoColor:** 이 색은 *albedoMap* 또는 *:::no-loc text="vertex":::* 색과 같은 다른 색에 곱해집니다. 재질에서 투명도를 사용하는 경우 알파 채널을 사용하여 불투명도가 조정됩니다. `1`은 완전히 불투명한 것을 의미하고 `0`은 완전히 투명한 것을 의미합니다. 기본값은 흰색입니다.

  > [!NOTE]
  > 색 재질은 환경을 반사하지 않으므로 완전히 투명한 색 재질은 보이지 않게 됩니다. 이는 [PBR 재질](pbr-materials.md)에서 다릅니다.

* **albedoMap:** 픽셀별 albedo 값의 [2D 텍스처](../../concepts/textures.md)입니다.

* **alphaClipEnabled** 및 **alphaClipThreshold:** *alphaClipEnabled* 가 true이면 albedo 알파 값이 *alphaClipThreshold* 보다 낮은 모든 픽셀은 그려지지 않습니다. 알파 클리핑은 투명도를 사용하도록 설정하지 않아도 사용할 수 있으며 렌더링 속도가 훨씬 빠릅니다. 그러나 알파 클리핑된 재질은 완전히 불투명한 재질보다 렌더링 속도가 느립니다. 기본적으로 알파 클리핑은 사용되지 않습니다.

* **textureCoordinateScale** 및 **textureCoordinateOffset:** 눈금은 UV 텍스처 좌표에 곱해지고 여기에 오프셋이 추가됩니다. 텍스처를 늘이거나 이동하는 데 사용할 수 있습니다. 기본 눈금은 (1, 1)이고 오프셋은 (0, 0)입니다.

* **useVertexColor:** 메시에 :::no-loc text="vertex"::: 색이 포함되고 이 옵션이 설정된 경우 메시의 :::no-loc text="vertex"::: 색이 *albedoColor* 및 *albedoMap* 에 곱해집니다. 기본적으로 *useVertexColor* 는 사용되지 않습니다.

* **isDoubleSided:** 이중 sidedness가 true로 설정된 경우 카메라가 후면을 바라보는 경우에도 이 재질을 갖는 삼각형이 렌더링됩니다. 기본적으로 이 옵션은 사용되지 않습니다. [:::no-loc text="Single-sided"::: 렌더링](single-sided-rendering.md)도 참조하세요.

* **TransparencyWritesDepth:** 재질에 TransparencyWritesDepth 플래그가 설정되어 있고 재질이 투명하면 이 재질을 사용하는 개체는 최종 깊이 버퍼에도 기여합니다. 다음 섹션에서 색 재질 속성 *transparencyMode* 를 참조하세요. 사용 사례에서 완전히 투명한 장면에 보다 타당한 [후기 단계 재투영](late-stage-reprojection.md)이 필요한 경우 이 기능을 사용하는 것이 좋습니다. 불투명/투명 혼합 장면의 경우에는 이 설정으로 인해 타당하지 않은 재투영 동작 또는 재투영 아티팩트가 발생할 수 있습니다. 그러므로 일반 사용 사례에서 기본 및 권장 설정은 이 플래그를 사용하지 않도록 설정하는 것입니다. 작성된 깊이 값은 카메라에 가장 가까운 개체의 픽셀별 깊이 계층에서 가져옵니다.

* **FresnelEffect:** 이 재질 플래그를 사용하면 각 재질에서 가산적 [프레스넬 효과](../../overview/features/fresnel-effect.md)를 사용할 수 있습니다. 이 효과의 모양은 다음에 설명된 다른 프레스넬 매개 변수에 의해 제어됩니다. 

* **FresnelEffectColor:** 이 재질에 사용되는 프레스넬 색입니다. 이 재질에 프레스넬 효과 비트가 설정된 경우에만 중요합니다(위 참조). 이 속성은 프레스넬 빛의 기본 색을 제어합니다(전체 설명은 [프레스넬 효과](../../overview/features/fresnel-effect.md) 참조). 현재는 rgb 채널 값만 중요하며 알파 값은 무시됩니다.

* **FresnelEffectExponent:** 이 재질에 사용되는 프레스넬 지수입니다. 이 재질에 프레스넬 효과 비트가 설정된 경우에만 중요합니다(위 참조). 이 속성은 프레스넬 빛의 분산을 제어합니다. 최소값 0.01에서는 빛이 전체 개체로 분산됩니다. 최대값 10.0에서는 빛이 눈에 보이는 가장 빛나는 가장자리로만 제한됩니다.

## <a name="color-material-properties"></a>색 재질 속성

다음 속성은 색 재질에만 적용됩니다.

* **vertexMix:** 이 값(`0`~`1`)은 [메시](../../concepts/meshes.md)의 :::no-loc text="vertex"::: 색이 최종 색에 기여하는 강도를 지정합니다. 기본값 1에서 :::no-loc text="vertex"::: 색은 albedo 색에 완전히 곱해집니다. 값 0에서는 :::no-loc text="vertex"::: 색이 완전히 무시됩니다.

* **transparencyMode:** [PBR 재질](pbr-materials.md)과 반대로 색 재질은 여러 투명도 모드에서 구분됩니다.

  1. **Opaque:** 기본 모드에서는 투명도를 사용하지 않습니다. 그러나 알파 클리핑은 여전히 가능하며, 충분한 경우 우선되어야 합니다.
  
  1. **AlphaBlended:** 이 모드는 PBR 재질의 투명도 모드와 비슷합니다. 유리와 같이 비치는 재질에 사용해야 합니다.

  1. **Additive:** 이 모드는 가장 간단하고 가장 효율적인 투명도 모드입니다. 재질의 기여가 렌더링된 이미지에 가산됩니다. 이 모드는 중요한 개체를 강조 표시하는 데 사용되는 표식과 같이 빛나는(하지만 여전히 투명한) 개체를 시뮬레이트하는 데 사용할 수 있습니다.

## <a name="api-documentation"></a>API 설명서

* [C# ColorMaterial 클래스](/dotnet/api/microsoft.azure.remoterendering.colormaterial)
* [C# RenderingConnection.CreateMaterial()](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.creatematerial)
* [C++ ColorMaterial 클래스](/cpp/api/remote-rendering/colormaterial)
* [C++ RenderingConnection::CreateMaterial()](/cpp/api/remote-rendering/renderingconnection#creatematerial)

## <a name="next-steps"></a>다음 단계

* [PBR 재질](pbr-materials.md)
* [질감](../../concepts/textures.md)
* [메시](../../concepts/meshes.md)