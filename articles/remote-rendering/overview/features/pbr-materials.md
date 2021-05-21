---
title: PBR 재질
description: PBR 재질 유형을 설명합니다.
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: e9908c106e57801cb1b7def8b3353a983cc97de0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "99591942"
---
# <a name="pbr-materials"></a>PBR 재질

*PBR 재질* 은 Azure Remote Rendering에서 지원되는 [재질 유형](../../concepts/materials.md) 중 하나입니다. 실제 조명을 받아야 하는 [메시](../../concepts/meshes.md)에 사용됩니다.

PBR은 물리 기반 렌더링(**P** hysically **B** ased **R** endering)의 약어이며, 모든 광원 조건에서 실제와 같은 결과를 얻을 수 있도록 해당 재질이 물리적으로 타당한 방식으로 표면의 시각적 속성을 기술한다는 것을 의미합니다. 대부분의 최신 게임 엔진 및 콘텐츠 생성 도구에서는 실시간 렌더링을 위한 실제 세계의 시나리오와 가장 근접한 것으로 간주되기 때문에 PBR 재질이 지원됩니다.

![ARR로 렌더링된 헬멧 glTF 샘플 모델](media/helmet.png)

하지만 PBR 재질은 범용 솔루션이 아닙니다. 시야 각도에 따라 색상이 다르게 반영되는 재질이 있습니다. 예를 들어 일부 패브릭 또는 자동차 페인트가 여기에 포함됩니다. 이러한 종류의 재질은 표준 PBR 모델로 처리되지 않으며, 현재까지 Azure Remote Rendering에서 지원되지 않습니다. 여기에는 *Thin-Film*(다중 레이어 표면) 및 *Clear-Coat*(자동차 페인트)와 같은 PBR 확장이 포함됩니다.

## <a name="common-material-properties"></a>공통 재질 속성

다음 속성은 모든 재질에 공통됩니다.

* **albedoColor:** 이 색은 *albedoMap* 또는 *:::no-loc text="vertex "::: 색* 과 같은 다른 색에 곱해집니다. 재질에서 *투명도* 를 사용하는 경우 알파 채널을 사용하여 불투명도가 조정됩니다. `1`은 완전히 불투명한 것을 의미하고 `0`은 완전히 투명한 것을 의미합니다. 기본값은 흰색입니다.

  > [!NOTE]
  > 완벽하게 깨끗한 유리 조각과 같이 PBR 재질이 완전히 투명한 경우에도 환경이 반영됩니다. 태양과 같은 밝은 지점이 반사되어 표시될 수 있습니다. 이것은 [색상 재질](color-materials.md)과 다릅니다.

* **albedoMap:** 픽셀별 albedo 값의 [2D 텍스처](../../concepts/textures.md)입니다.

* **alphaClipEnabled** 및 **alphaClipThreshold:** *alphaClipEnabled* 가 true이면 albedo 알파 값이 *alphaClipThreshold* 보다 낮은 모든 픽셀은 그려지지 않습니다. 알파 클리핑은 투명도를 사용하도록 설정하지 않아도 사용할 수 있으며 렌더링 속도가 훨씬 빠릅니다. 그러나 알파 클리핑된 재질은 완전히 불투명한 재질보다 렌더링 속도가 느립니다. 기본적으로 알파 클리핑은 사용되지 않습니다.

* **textureCoordinateScale** 및 **textureCoordinateOffset:** 눈금은 UV 텍스처 좌표에 곱해지고 여기에 오프셋이 추가됩니다. 텍스처를 늘이거나 이동하는 데 사용할 수 있습니다. 기본 눈금은 (1, 1)이고 오프셋은 (0, 0)입니다.

* **useVertexColor:** 메시에 :::no-loc text="vertex"::: 색이 포함되고 이 옵션이 설정된 경우 메시의 :::no-loc text="vertex"::: 색이 *albedoColor* 및 *albedoMap* 에 곱해집니다. 기본적으로 *useVertexColor* 는 사용되지 않습니다.

* **isDoubleSided:** 이중 sidedness가 true로 설정된 경우 카메라가 후면을 바라보는 경우에도 이 재질을 갖는 삼각형이 렌더링됩니다. 또한 PBR 재질의 경우 광원이 후면에 대해 적절하게 계산됩니다. 이 옵션은 기본적으로 사용하지 않도록 설정됩니다. [:::no-loc text="Single-sided"::: 렌더링](single-sided-rendering.md)도 참조하세요.

* **TransparencyWritesDepth:** 재질에 TransparencyWritesDepth 플래그가 설정되어 있고 재질이 투명하면 이 재질을 사용하는 개체는 최종 깊이 버퍼에도 기여합니다. 다음 섹션에서 *투명한* PBR 재질 플래그를 참조하세요. 사용 사례에서 완전히 투명한 장면에 보다 타당한 [후기 단계 재투영](late-stage-reprojection.md)이 필요한 경우 이 기능을 사용하는 것이 좋습니다. 불투명/투명 혼합 장면의 경우에는 이 설정으로 인해 타당하지 않은 재투영 동작 또는 재투영 아티팩트가 발생할 수 있습니다. 그러므로 일반 사용 사례에서 기본 및 권장 설정은 이 플래그를 사용하지 않도록 설정하는 것입니다. 작성된 깊이 값은 카메라에 가장 가까운 개체의 픽셀별 깊이 계층에서 가져옵니다.

* **FresnelEffect:** 이 재질 플래그를 사용하면 각 재질에서 가산적 [프레스넬 효과](../../overview/features/fresnel-effect.md)를 사용할 수 있습니다. 이 효과의 모양은 다음에 설명된 다른 프레스넬 매개 변수에 의해 제어됩니다. 

* **FresnelEffectColor:** 이 재질에 사용되는 프레스넬 색입니다. 이 재질에 프레스넬 효과 비트가 설정된 경우에만 중요합니다(위 참조). 이 속성은 프레스넬 빛의 기본 색을 제어합니다(전체 설명은 [프레스넬 효과](../../overview/features/fresnel-effect.md) 참조). 현재는 rgb 채널 값만 중요하며 알파 값은 무시됩니다.

* **FresnelEffectExponent:** 이 재질에 사용되는 프레스넬 지수입니다. 이 재질에 프레스넬 효과 비트가 설정된 경우에만 중요합니다(위 참조). 이 속성은 프레스넬 빛의 분산을 제어합니다. 최솟값 0.01에서는 빛이 전체 개체로 분산됩니다. 최댓값 10.0에서는 빛이 눈에 보이는 가장 빛나는 가장자리로만 제한됩니다.

## <a name="pbr-material-properties"></a>PBR 재질 속성

물리 기반 렌더링의 핵심 개념은 실제 세계의 다양한 재질을 에뮬레이트하기 위해 *BaseColor*, *Metalness* 및 *Roughness* 속성을 사용하는 것입니다. PBR에 대한 자세한 설명은 이 문서의 범위를 벗어납니다. PBR에 대한 자세한 내용은 [다른 원본](http://www.pbr-book.org)을 참조하세요. 다음 속성은 PBR 재질과 관련이 있습니다.

* **baseColor:** PBR 재질에서 *albedo 색* 은 *기본 색* 으로 참조됩니다. Azure Remote Rendering에서 *albedo 색* 속성은 공통 재질 속성으로 표현되므로, 추가적인 기본 색 속성이 없습니다.

* **roughness** 및 **roughnessMap:** 거칠기는 표면이 거칠거나 부드러운 정도를 정의합니다. 거친 표면에서는 부드러운 표면보다 광원이 더 많은 방향으로 산란되며, 반사가 선명하지 않고 흐리게 나타납니다. 값 범위는 `0.0`~`1.0`입니다. `roughness`가 `0.0`이면 반사가 선명합니다. `roughness`가 `0.5`이면 반사가 흐려집니다.

  거칠기 값 및 거칠기 맵이 모두 제공된 경우 최종 값은 두 값의 곱으로 나타납니다.

* **metalness** 및 **metalnessMap:** 물리학에서 이 속성은 해당 표면이 전도체 또는 절연체인지 여부를 나타냅니다. 전도체 재질은 다른 반사 속성을 가지며, albedo 색 없이 반사되는 경향이 있습니다. PBR 재질에서 이 속성은 표면이 주위 환경을 반사하는 정도에 영향을 줍니다. 값 범위는 `0.0`~`1.0`입니다. 금속성이 `0.0`이면 albedo 색이 완전히 표시되고, 재질이 플라스틱 또는 세라믹처럼 보입니다. 금속성이 `0.5`이면 페인트를 칠한 금속처럼 보입니다. 금속성이 `1.0`이면 표면이 albedo 색을 거의 완전히 잃고, 주변만 반사됩니다. 예를 들어 `metalness`가 `1.0`이고 `roughness`가 `0.0`이면 표면이 실제 세계의 거울처럼 보입니다.

  금속성 값과 금속성 맵이 모두 제공된 경우 최종 값은 두 값의 곱으로 나타납니다.

  ![다른 금속성 및 거칠기 값으로 렌더링된 구](./media/metalness-roughness.png)

  위 그림에서 오른쪽 아래 모서리에 있는 구는 실제 금속 재질처럼 보이고, 왼쪽 아래는 세라믹 또는 플라스틱처럼 보입니다. albedo 색도 물리적 속성에 따라 변경됩니다. 거칠기가 증가하면 재질의 반사 선명도가 손실됩니다.

* **normalMap:** 세부 정보를 세밀하게 시뮬레이트하기 위해서는 [보통 맵](https://en.wikipedia.org/wiki/Normal_mapping)을 제공할 수 있습니다.

* **occlusionMap** 및 **aoScale:** [앰비언트 폐색](https://en.wikipedia.org/wiki/Ambient_occlusion)은 폐쇄된 영역에 그림자를 추가하여 균열이 있는 개체가 더 사실적으로 보이도록 만듭니다. 폐색 값 범위는 `0.0`~`1.0`입니다. 여기서 `0.0`은 어두운 부분(폐쇄됨)을 의미하고 `1.0`은 폐쇄된 부분이 없음을 의미합니다. 2D 질감이 폐색 맵으로 제공되는 경우 이 효과가 사용되고 *aoScale* 이 승수로 사용됩니다.

  ![앰비언트 폐색을 사용하거나 사용하지 않고 렌더링된 개체](./media/boom-box-ao2.gif)

* **투명:** PBR 재질의 경우 투명도 설정이 하나만 있고, 이 설정이 사용되거나 사용되지 않습니다. 불투명도는 albedo 색의 알파 채널에 따라 정의됩니다. 사용하도록 설정된 경우 반투명 표면을 그리기 위해 보다 복잡한 렌더링 파이프라인이 호출됩니다. Azure Remote Rendering은 진정한 [OIT(Order Independent Transparency)](https://en.wikipedia.org/wiki/Order-independent_transparency)를 구현합니다.

  투명한 기하 도형은 렌더링 비용이 많이 듭니다. 나뭇잎과 같이 표면에 구멍만 필요하면 대신 알파 클리핑을 사용하는 것이 더 좋습니다.

  ![0~100 투명도로 렌더링된 구](./media/transparency.png) 위 이미지에서 가장 오른쪽 구는 완전히 투명하지만 여전히 반사가 표시됩니다.

  > [!IMPORTANT]
  > 런타임에 불투명에서 투명으로 전환되는 재질의 경우, 렌더러가 *TileBasedComposition* [렌더링 모드](../../concepts/rendering-modes.md)를 사용해야 합니다. 시작 시 투명한 재질로 변환되는 재질에는 이러한 제한이 적용되지 않습니다.

## <a name="technical-details"></a>기술 세부 정보

Azure Remote Rendering에는 GGX NDF를 사용하는 Cook-Torrance 마이크로 패싯 BRDF, Schlick Fresnel 및 Lambert 디퓨징 텀을 사용하는 GGX Smith 상관 조정된 가시도 텀이 사용됩니다. 이 모델은 사실상 현재 업계 표준입니다. 자세한 내용은 [물리적 기반 렌더링 - Cook Torrance](http://www.codinglabs.net/article_physically_based_rendering_cook_torrance.aspx)를 참조하세요

 Azure Remote Rendering에 사용되는 *Metalness-Roughness* PBR 모델의 대안은 *Specular-Glossiness* PBR 모델입니다. 이 모델은 다양한 범위의 재질을 나타낼 수 있습니다. 하지만 비용이 더 높고, 일반적으로 실시간 사례에 적합하지 않습니다.
*(BaseColor, Metalness)* 로 변환될 수 없는 *(Diffuse, Specular)* 값이 있기 때문에 항상 *Specular-Glossiness* 에서 *Metalness-Roughness* 로 변환할 수 있는 것은 아닙니다. 모든 *(BaseColor, Metalness)* 쌍이 잘 정의된 *(Diffuse, Specular)* 쌍에 해당하기 때문에 다른 방향으로의 변환이 더 간단하고 더 정확합니다.

## <a name="api-documentation"></a>API 설명서

* [C# PbrMaterial 클래스](/dotnet/api/microsoft.azure.remoterendering.pbrmaterial)
* [C# RenderingConnection.CreateMaterial()](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.creatematerial)
* [C++ PbrMaterial 클래스](/cpp/api/remote-rendering/pbrmaterial)
* [C++ RenderingConnection::CreateMaterial()](/cpp/api/remote-rendering/renderingconnection#creatematerial)

## <a name="next-steps"></a>다음 단계

* [색 재질](color-materials.md)
* [질감](../../concepts/textures.md)
* [메시](../../concepts/meshes.md)