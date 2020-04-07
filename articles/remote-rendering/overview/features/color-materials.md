---
title: 색상 재료
description: 색상 재질 유형을 설명합니다.
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: 7cbcaefcc087c9f1c7c09668a27fbdef9a4802d3
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681078"
---
# <a name="color-materials"></a>색상 재료

*색상 재질은* Azure 원격 렌더링에서 지원되는 [재질 유형](../../concepts/materials.md) 중 하나입니다. 그들은 조명의 어떤 종류를수신하지 말아야 [하는 메시에](../../concepts/meshes.md) 사용되며, 오히려 항상 전체 밝기가 될 수 있습니다. 자동차 대시보드, 전구 와 같은 '빛나는' 재질또는 [사진 측정을](https://en.wikipedia.org/wiki/Photogrammetry)통해 얻은 모델과 같이 정적 조명을 이미 통합한 데이터의 경우일 수 있습니다.

색상 재질은 더 간단한 색상 모델 때문에 [PBR 재질보다](pbr-materials.md) 렌더링하는 것이 더 효율적입니다. 또한 다른 투명도 모드를 지원합니다.

## <a name="common-material-properties"></a>공통 재료 특성

이러한 특성은 모든 재질에 공통적입니다.

* **알베도컬러:** 이 색상은 *albedoMap* 또는 정점 색상과 같은 다른 색상과 *곱해지됩니다.* 재질에서 *투명도가* 활성화된 경우 알파 채널은 불투명도를 `1` 조정하는 데 `0` 사용되며, 이는 완전히 불투명하고 완전히 투명한 의미를 의미합니다. 기본값은 흰색입니다.

  > [!NOTE]
  > 색상 재질은 환경을 반영하지 않으므로 완전히 투명한 색상 재질이 보이지 않게 됩니다. 이것은 [PBR 재료에](pbr-materials.md)대해 다릅니다.

* **알베도맵:** 픽셀당 알베도 값에 대한 [2D 텍스처입니다.](../../concepts/textures.md)

* **alphaClipEnabled** 및 **alphaClipThreshold:** *alphaClipEnabledtrue인* 경우 알파클립임계값보다 알파클립값보다 *alphaClipThreshold* 낮은 모든 픽셀은 그려지지 않습니다. 알파 클리핑은 투명도를 설정하지 않고도 사용할 수 있으며 렌더링 속도가 훨씬 빠릅니다. 그러나 알파 클리핑된 재질은 완전히 불투명한 재질보다 렌더링 속도가 느립니다. 기본적으로 알파 클리핑은 사용할 수 없습니다.

* **텍스처코디네이터스케일** 및 **텍스처코디오프셋:** 배율이 UV 텍스처 좌표에 곱해지고 오프셋이 추가됩니다. 텍스처를 스트레칭하고 이동하는 데 사용할 수 있습니다. 기본 축척은 (1, 1) 및 간격띄우기 (0, 0)입니다.

* **사용버텍스색상:** 메시에 정점 색상이 포함되어 있고 이 옵션이 활성화된 경우 메시의 정점 색상이 *albedoColor* 및 *albedoMap에*곱해지됩니다. 기본적으로 정점 색상은 비활성화됩니다.

* **는양쪽:** 양면이 true로 설정된 경우 카메라가 뒷면을 보고 있더라도 이 재질의 삼각형이 렌더링됩니다. 기본적으로 이 옵션은 사용할 수 없습니다. [단면 렌더링도](single-sided-rendering.md)참조하십시오.

## <a name="color-material-properties"></a>색상 재료 특성

다음 속성은 색상 재질에 따라 다릅니다.

* **버텍스믹스:** 이 `0` 값은 `1` [메시의](../../concepts/meshes.md) 정점 색상이 최종 색상에 얼마나 강하게 기여하는지를 지정합니다. 기본값 1에서 정점 색상은 알베도 색상에 완전히 곱해지됩니다. 값이 0이면 정점 색상은 완전히 무시됩니다.

* **투명도모드:** [PBR 재료와는](pbr-materials.md)달리 색상 재료는 서로 다른 투명도 모드를 구별합니다.

  1. **불투명:** 기본 모드는 투명도를 비활성화합니다. 알파 클리핑은 여전히 가능하지만, 충분하다면 선호되어야 합니다.
  
  1. **알파블렌드:** 이 모드는 PBR 재질의 투명도와 유사합니다. 유리와 같은 시스루 재료에 사용해야 합니다.

  1. **첨가제:** 이 모드는 가장 간단하고 효율적인 투명도 모드입니다. 재질의 기여도가 렌더링된 이미지에 추가됩니다. 이 모드는 중요한 오브젝트를 강조 표시하는 데 사용되는 마커와 같이 빛나는(그러나 여전히 투명한) 오브젝트를 시뮬레이션하는 데 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [PBR 재료](pbr-materials.md)
* [질감](../../concepts/textures.md)
* [메시](../../concepts/meshes.md)
