---
title: PBR 재질
description: .PBR 재질 유형을 설명 합니다.
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: f2e63903546e173e17f2b457b78eb41bcdf65dbd
ms.sourcegitcommit: dc342bef86e822358efe2d363958f6075bcfc22a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94555569"
---
# <a name="pbr-materials"></a>PBR 재질

Azure 원격 렌더링에서 지원 되는 [재질 유형](../../concepts/materials.md) 중 하나는 *.pbr 자료* 입니다. 실제 조명을 받아야 하는 [메시](../../concepts/meshes.md) 에 사용 됩니다.

.PBR는 **P** hysically **B** 기반 **R** endering을 의미 하 고, 모든 조명 조건에서 실제 결과를 사용할 수 있도록 실제로 타당 방식으로 표면의 시각적 속성을 설명 합니다. 대부분의 최신 게임 엔진 및 콘텐츠 생성 도구는 실시간 렌더링을 위한 실제 시나리오의 가장 근접 한 것으로 간주 되기 때문에, 대부분의 경우에는 .PBR 자료를 지원 합니다.

![ARR에서 렌더링 하는 투구의 샘플 모델](media/helmet.png)

그러나 .PBR 자료는 범용 솔루션이 아닙니다. 보기 각도에 따라 색을 다르게 반영 하는 자료가 있습니다. 예를 들어 일부 패브릭 또는 자동차는를 그립니다. 이러한 종류의 자료는 표준 .PBR 모델에서 처리 되지 않으며 현재 Azure 원격 렌더링에서 지원 되지 않습니다. 여기에는 *씬 필름* (다중 계층 표면) 및 *선명한 옷걸이* (차량 페인트의 경우)와 같은 .pbr 확장이 포함 됩니다.

## <a name="common-material-properties"></a>공통 재질 속성

이러한 속성은 모든 자료에 공통적입니다.

* **Albedocolor:** 이 색을 *Albedomap* 또는 *:::no-loc text="vertex "::: colors* 와 같은 다른 색과 곱합니다. 재질에서 *투명도* 를 사용 하는 경우 알파 채널을 사용 하 여 불투명도를 조정 하 `1` 고 완전히 불투명 하며 `0` 의미를 완전히 투명 하 게 나타냅니다. 기본값은 흰색입니다.

  > [!NOTE]
  > 완벽 하 게 깨끗 한 투명 한 부분과 같이, .PBR 재질은 완전히 투명 한 경우에도 환경을 반영 합니다. Sun과 같은 밝은 스폿은 여전히 반사에서 볼 수 있습니다. 이는 [색 재질](color-materials.md)마다 다릅니다.

* **Albedomap:** 픽셀 별 albedo 값에 대 한 [2d 질감](../../concepts/textures.md) 입니다.

* **alphaClipEnabled** 및 **alphaClipThreshold:** *alphaClipEnabled* 가 true 이면 Albedo 알파 값이 *alphaClipThreshold* 보다 낮은 모든 픽셀은 그려지지 않습니다. 알파 클리핑을 투명도를 사용 하지 않고도 사용할 수 있으며 렌더링 속도가 훨씬 빠릅니다. 그러나 알파 클리핑 재질은 완전히 불투명 한 재질 보다 렌더링 하는 속도가 느립니다. 기본적으로 알파 클리핑을 사용 하지 않도록 설정 됩니다.

* **textureCoordinateScale** 및 **textureCoordinateOffset:** 눈금은 UV 질감 좌표에 곱하고이에 오프셋을 추가 합니다. 질감을 늘이거나 이동 하는 데 사용할 수 있습니다. 기본 소수 자릿수는 (1, 1)이 고 offset은 (0, 0)입니다.

* **useVertexColor:** 메시에 :::no-loc text="vertex"::: 색이 포함 되어 있고이 옵션을 사용 하는 경우 망상의 :::no-loc text="vertex"::: 색을 *albedocolor* 및 *albedocolor* 에 곱합니다. 기본적으로 *useVertexColor* 는 사용 되지 않습니다.

* **isDoubleSided:** 이중 sidedness가 true로 설정 된 경우 카메라가 뒷면 얼굴을 보는 경우에도이 재질의 삼각형이 렌더링 됩니다. 또한의 경우에는 뒷면의 경우에도 해당 재질 조명이 적절 하 게 계산 됩니다. 기본적으로이 옵션은 사용할 수 없습니다. [ :::no-loc text="Single-sided"::: 렌더링](single-sided-rendering.md)도 참조 하세요.

* **TransparencyWritesDepth:** TransparencyWritesDepth 플래그가 재질에 설정 되어 있고 자료가 투명 하면이 자료를 사용 하는 개체는 최종 깊이 버퍼에도 영향을 주지 않습니다. 다음 섹션에서는 .PBR 재질 플래그를 *투명* 하 게 표시 합니다. 이 기능을 사용 하는 경우 사용 사례에 완전히 투명 한 장면에 대 한 보다 타당 [늦은 단계 다시 프로젝션이](late-stage-reprojection.md) 필요한 경우에 권장 됩니다. 불투명 투명/투명 한 장면을 혼합 하는 경우이 설정으로 인해 집합 reprojection 동작이 나 reprojection 아티팩트가 발생할 수 있습니다. 이러한 이유로 일반 사용 사례에 대 한 기본 및 권장 설정은이 플래그를 사용 하지 않도록 설정 하는 것입니다. 작성 된 깊이 값은 카메라에 가장 가까운 개체의 픽셀 별 깊이 계층에서 가져옵니다.

* **FresnelEffect:** 이 재질 플래그를 사용 하면 해당 재질에서 가산 [프레스 넬 대칭 효과](../../overview/features/fresnel-effect.md) 를 사용할 수 있습니다. 효과의 모양은 다음에 설명 된 다른 프레스 넬 대칭 매개 변수에 의해 제어 됩니다. 

* **FresnelEffectColor:** 이 재질에 사용 되는 프레스 넬 대칭 색입니다. 이 자료에 프레스 넬 대칭 효과 비트가 설정 된 경우에만 중요 합니다 (위 참조). 이 속성은 프레스 넬 대칭 빛의 기본 색을 제어 합니다 (전체 설명에 대 한 [프레스 넬 대칭 효과](../../overview/features/fresnel-effect.md) 참조). 현재는 rgb 채널 값만 중요 하며 알파 값은 무시 됩니다.

* **FresnelEffectExponent:** 이 재질에 사용 되는 프레스 넬 대칭 지 수입니다. 이 자료에 프레스 넬 대칭 효과 비트가 설정 된 경우에만 중요 합니다 (위 참조). 이 속성은 프레스 넬 대칭 빛의 스프레드를 제어 합니다. 최소값 0.01은 전체 개체에 걸쳐 분산 됩니다. 최 댓 값 10.0은 제한를 가장 gracing 가장자리로 표시 합니다.

## <a name="pbr-material-properties"></a>.PBR 재질 속성

실제로 기반 렌더링의 핵심 개념은 *BaseColor* , *Metalness* 및 *황삭* 속성을 사용 하 여 광범위 한 실제 자료를 에뮬레이트하는 것입니다. 이 문서의 범위를 벗어나는 경우에 대 한 자세한 설명은이 문서의 범위를 벗어나는 것입니다. .PBR에 대 한 자세한 내용은 [다른 원본](http://www.pbr-book.org)을 참조 하세요. 다음 속성은 .PBR 자료에만 적용 됩니다.

* **baseColor:** .PBR 재질에서 *albedo 색* 을 *기본 색* 이라고 합니다. Azure 원격 렌더링에서 *albedo color* 속성은 이미 공통 재질 속성을 통해 제공 되므로 추가 기본 색 속성은 없습니다.

* **황삭** 및 **roughnessMap:** 황삭는 표면의 황삭 또는 부드러운 정도를 정의 합니다. 황삭 표면에서는 부드러운 표면 보다 더 많은 방향으로 조명을 분산 하 여 반사를 선명 하 게 만듭니다. 값 범위는에서 사이 `0.0` 입니다 `1.0` . 가 `roughness` 와 같으면 `0.0` 리플렉션이 선명 하 게 됩니다. 가 `roughness` 와 같으면 `0.5` 리플렉션이 흐릿하게 됩니다.

  황삭 값과 황삭 맵이 모두 제공 되는 경우 최종 값은 두 값의 곱이 됩니다.

* **metalness** 및 **metalnessMap:** 물리학에서이 속성은 표면이 conductive 인지 또는 dielectric 인지에 해당 합니다. Conductive 자료는 다른 반사 속성을 가지 며, albedo 색 없이 반사 되는 경향이 있습니다. 이 속성은 .PBR 재질에서 주변 환경을 반영 하는 화면 크기에 영향을 줍니다. 값의 범위는에서 사이 `0.0` `1.0` 입니다. Metalness가 인 경우 `0.0` albedo 색은 완전히 표시 되 고 재질은 플라스틱 또는 ceramics와 같이 보입니다. Metalness이 인 경우 `0.5` 에는 칠해진 금속 처럼 보입니다. Metalness가 인 경우 `1.0` 표면은 거의 완전히 해당 albedo 색을 상실 하 고 주변만을 반영 합니다. 예를 들어 `metalness` 가이 `1.0` 고 `roughness` 가 이면 `0.0` 표면은 실제 미러 처럼 보입니다.

  Metalness 값과 metalness map을 모두 제공 하면 최종 값이 두 값의 곱입니다.

  ![다른 metalness 및 황삭 값으로 렌더링 된 구](./media/metalness-roughness.png)

  위의 그림에서 오른쪽 아래에 있는 구는 실제 금속 재질 처럼 보이고 왼쪽 아래는 ceramic 또는 플라스틱 처럼 보입니다. 또한 albedo 색은 실제 속성에 따라 변경 됩니다. 황삭를 사용 하면 재질의 반사 선명도가 상실 됩니다.

* **Normalmap:** 세분화 된 세부 정보를 시뮬레이션 하기 위해 [일반적인 맵을](https://en.wikipedia.org/wiki/Normal_mapping) 제공할 수 있습니다.

* **occlusionMap** 및 **aoscale:** [앰비언트 폐색](https://en.wikipedia.org/wiki/Ambient_occlusion) 은 폐색 영역에 그림자를 추가 하 여 crevices를 사용 하는 개체를 보다 사실적으로 보입니다. 폐색 값의 범위 `0.0` `1.0` 는에서 사이 `0.0` 입니다. 여기서는 폐색 (어둡기를 의미)이 고 occlusions 없음을 `1.0` 의미 합니다. 2D 질감이 폐색 맵으로 제공 되는 경우 효과가 활성화 되 고 *Aoscale* 이 승수 역할을 합니다.

  ![앰비언트 폐색를 사용 하거나 사용 하지 않고 개체를 렌더링 합니다.](./media/boom-box-ao2.gif)

* **투명:** 이 경우에는 사용 하거나 사용 하지 않도록 설정 하는 것과 같은 여러 가지 투명성 설정이 있습니다. 불투명은 albedo 색의 알파 채널에 의해 정의 됩니다. 이 기능을 사용 하는 경우 반투명 화면을 그리기 위해 보다 복잡 한 렌더링 파이프라인이 호출 됩니다. Azure 원격 렌더링은 진정한 [주문 독립적 투명성](https://en.wikipedia.org/wiki/Order-independent_transparency) (oit)을 구현 합니다.

  투명 한 기 하 도형은 렌더링 하는 데 비용이 많이 듭니다. 트리의 리프와 같이 표면에 구멍이 필요한 경우에는 대신 알파 클리핑을 사용 하는 것이 좋습니다.

  ![위의 이미지에서는 0으로 렌더링 된 구를 렌더링 합니다 ](./media/transparency.png) . 위의 이미지에서는 오른쪽 구가 완전히 투명 하지만 리플렉션이 계속 표시 됩니다.

  > [!IMPORTANT]
  > 런타임에 불투명에서 투명으로 전환 되어야 하는 재질은 *TileBasedComposition* [렌더링 모드](../../concepts/rendering-modes.md)를 사용 해야 합니다. 이 제한은 시작할 때 투명 자료로 변환 되는 재질에는 적용 되지 않습니다.

## <a name="technical-details"></a>기술 세부 정보

Azure 원격 렌더링은 GGX .NDF, Schlick 프레스 넬 대칭 및 GGX Smith 상관 관계 표시 용어와 램버트 확산 용어를 사용 하 여 Cook-Torrance 마이크로 패싯 BRDF를 사용 합니다. 이 모델은 현재 업계 표준입니다. 자세한 내용은 [물리적 기반 렌더링-쿡 Torrance](http://www.codinglabs.net/article_physically_based_rendering_cook_torrance.aspx) 문서를 참조 하세요.

 Azure 원격 렌더링에 사용 되는 *Metalness-황삭* .pbr 모델에 대 한 대안은 *Glossiness* .pbr 모델입니다. 이 모델은 다양 한 자료를 나타낼 수 있습니다. 그러나 비용이 더 많이 들고, 일반적으로 실시간 사례에서 제대로 작동 하지 않습니다.
( *확산, 반사)* 값 쌍 *(BaseColor, Metalness)* 으로 변환할 수 없기 때문에 *Glossiness* 에서 *Metalness* 로 변환 하는 것이 항상 가능 하지는 않습니다. 모든 *(BaseColor, Metalness)* 쌍이 잘 정의 된 *(확산, 반사)* 쌍에 해당 하므로 다른 방향으로 변환 하는 것이 더 간단 하 고 정확 합니다.

## <a name="api-documentation"></a>API 설명서

* [C # PbrMaterial 클래스](/dotnet/api/microsoft.azure.remoterendering.pbrmaterial)
* [C # RemoteManager CreateMaterial ()](/dotnet/api/microsoft.azure.remoterendering.remotemanager.creatematerial)
* [C + + PbrMaterial 클래스](/cpp/api/remote-rendering/pbrmaterial)
* [C + + RemoteManager:: CreateMaterial ()](/cpp/api/remote-rendering/remotemanager#creatematerial)

## <a name="next-steps"></a>다음 단계

* [색 재질](color-materials.md)
* [질감](../../concepts/textures.md)
* [메시](../../concepts/meshes.md)