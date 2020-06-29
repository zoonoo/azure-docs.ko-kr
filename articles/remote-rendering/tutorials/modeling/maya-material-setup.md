---
title: Maya에서 물리적 기반 렌더링 재질 설정
description: Maya에서 물리적 기반 렌더링 재질을 설정하고 FBX 형식으로 내보내는 방법을 설명합니다.
author: muxanickms
ms.author: misams
ms.date: 06/16/2020
ms.topic: tutorial
ms.openlocfilehash: 5579994b0746a2de4b0f2ca927027ac709940024
ms.sourcegitcommit: 9bfd94307c21d5a0c08fe675b566b1f67d0c642d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/17/2020
ms.locfileid: "84977411"
---
# <a name="tutorial-setting-up-physically-based-rendering-materials-in-maya"></a>자습서: Maya에서 물리적 기반 렌더링 재질 설정

## <a name="overview"></a>개요
이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
>
> * 장면에서 고급 조명 모델이 있는 재질을 개체에 할당합니다.
> * 개체 및 재질의 인스턴싱을 처리합니다.
> * 장면을 FBX 형식 및 선택할 수 있는 중요한 옵션으로 내보냅니다.

`Maya`에서 [PBR(물리적 기반 렌더링) 재질](../../overview/features/pbr-materials.md)을 만드는 것은 비교적 간단한 작업이며, `3DS Max`와 같은 다른 콘텐츠 만들기 앱에서 여러 가지 방법으로 PBR을 설정하는 것과 비슷합니다. 다음 자습서는 ARR 프로젝트의 기본 PBR 셰이더 설정 및 FBX 내보내기에 대한 지침입니다. 

이 자습서의 샘플 장면에는 다양한 재질(예: 목재, 금속, 도장 금속, 플라스틱 및 고무)이 할당된 여러 `Polygon Box` 개체가 포함되어 있습니다. 일반적으로 각 재질에는 다음 질감의 전부 또는 대부분이 포함되어 있습니다. 

* `Albedo` - 재질 색상표입니다(`Diffuse` 또는 `BaseColor`라고도 함).
* `Metalness` - 재질이 금속인지 여부 또는 금속 부분을 결정합니다. 
* `Roughness` - 표면의 거칠거나 매끄러운 정도를 결정하여 표면의 반사 및 하이라이트의 선명도 또는 흐릿함에 영향을 줍니다.
* `Normals` - 다각형을 더 추가하지 않고도 세부 정보(예: 금속 표면의 점식(pitting) 및 파임(dent) 또는 나무의 입자)를 표면에 추가합니다.
* `Ambient Occlusion` - 모델에 부드러운 음영 및 콘택트 섀도(contact shadow)를 추가하는 데 사용됩니다. 모델에서 전체 조명(흰색) 또는 전체 음영(검은색)을 받는 영역을 나타내는 회색조 맵입니다. 

## <a name="prerequisites"></a>필수 구성 요소
* `Autodesk Maya 2017` 이상

## <a name="setting-up-materials-in-the-scene"></a>장면에서 재질 설정
Maya에서 PBR 재질을 설정하는 프로세스는 다음과 같습니다.

먼저 샘플 장면에서 볼 수 있듯이 각각 서로 다른 유형의 재질을 나타내는 여러 Box 개체를 만들었습니다. 이러한 각 개체에는 아래 이미지와 같이 적절한 고유 이름이 지정되었습니다. 

> 미터 단위를 측정에 사용하고 위쪽 방향이 Y축인 ARR(Azure Remote Rendering)용 자산 만들기를 시작하기 전에 주목할 가치가 있습니다. 따라서 Maya의 장면 단위를 미터로 설정하는 것이 좋습니다. 또한 이는 FBX 내보내기 설정에서 단위를 미터로 설정하기 위해 내보내는 경우에도 좋습니다. 

> [!TIP]
이름을 사용하여 개체가 많은 장면을 더 쉽게 탐색할 수 있으므로 모델 자산의 이름은 일반적으로 관련 부분 또는 재질 유형을 사용하여 적절하게 지정하는 것이 좋습니다.

![개체 이름](media/object-names.jpg)

사용자의 요구에 따라 질감이 만들어지거나 획득되면 `Quixel Suite`, `Photoshop` 또는 `Substance Suite`와 같은 질감 앱에서 모델에 대한 고유한 질감을 만들거나 다른 원본의 일반 타일 질감을 사용할 수 있습니다. 다음과 같이 이러한 질감을 모델에 적용할 수 있습니다.

* 장면 뷰포트에서 모델/기하 도형을 선택하고 마우스 오른쪽 단추로 이를 클릭합니다. 표시되는 메뉴에서 `Assign New Material`을 클릭합니다.
* `Assign New Material` 옵션에서 `Maya`>`Stingray PBS`로 이동합니다. 이 작업을 수행하면 PBR 재질이 모델에 할당됩니다. 

`Maya 2020`에는 사용할 수 있는 여러 다른 PBR 셰이더(`Maya Standard Surface`, `Arnold Standard Surface` 및 `Stingray PBR`)가 있습니다. `Maya Standard Surface Shader`는 `FBX plugin 2020`을 통해 아직 내보낼 수 없지만, `Arnold Standard Surface Shader`는 FBX 파일로 내보낼 수 있습니다. 대부분의 다른 측면에서는 `Maya Standard Surface Shader`와 동일하며 `3D Studio Max`의 `Physical Material`과 비슷합니다.

**`The Stingray PBR Shader`** 는 다른 많은 애플리케이션과 호환되며, `ARR`의 요구 사항과 가장 일치하며 `Maya 2017`부터 지원됩니다. 또한 이 유형의 재질은 나중에 ARR에서 시각화하는 것과 비슷하게 뷰포트에서 시각화하는 것이 편리합니다.

!['Stingray' 재질](media/stingray-material.jpg)

재질이 자산에 할당되고 적절한 이름이 지정되면 이제 다양한 질감을 할당하는 작업을 진행할 수 있습니다. 다음 이미지에서는 각 질감 유형이 PBR 재질에 적합한 위치에 대해 자세히 설명합니다. `Stingray PBR` 재질을 사용하면 활성화할 수 있는 특성을 선택할 수 있으므로 질감 맵을 `plug in`하려면 먼저 관련 특성을 활성화해야 합니다. 

![재질 설정](media/material-setup.jpg)

> [!TIP]
재질의 사용 및/또는 유형을 고려하여 해당 재질의 이름을 적절하게 지정하는 것이 좋습니다. 고유한 부분에 사용되는 재질의 해당 부분에 대한 이름을 지정할 수 있지만, 더 광범위한 영역에서 사용할 수 있는 재질의 이름은 해당 속성 또는 유형에 따라 지정할 수 있습니다.

질감을 다음과 같이 할당합니다.

![질감 설정](media/texture-setup.jpg)

PBR 재질이 만들어지고 설정되면 장면에서 [개체 인스턴싱](../../how-tos/conversion/configure-model-conversion.md#instancing)을 수행하도록 고려하는 것이 좋습니다. 장면에서 비슷한 개체(예: 너트, 볼트, 나사 와셔), 기본적으로 동일한 개체를 인스턴싱하면 파일 크기 측면에서 상당한 비용을 절감할 수 있습니다. 마스터 개체의 인스턴스는 자체의 크기 조정, 회전 및 변환을 사용하고 있으므로 장면에서 필요한 대로 배치할 수 있습니다. Maya에서 인스턴싱 프로세스는 간단합니다.

* `Edit` 메뉴에서 `Duplicate Special`로 이동하여 `Options`를 엽니다. 
* `Duplicate Special` 옵션에서 `Geometry Type`을 `Copy`에서 `Instance`로 전환합니다. 
* `Duplicate Special`을 클릭합니다.

![인스턴싱](media/instancing.jpg)

이 작업을 수행하면 개체의 부모 및 해당 부모의 다른 인스턴스와 독립적으로 회전하거나 크기를 조정할 수 있는 해당 개체의 인스턴스가 만들어집니다. 
>그러나 구성 요소 모드에서 인스턴스에 대한 모든 변경 내용은 개체의 모든 인스턴스에 전송되므로, 인스턴싱된 개체 구성 요소(정점, 다각형 면 등)를 사용하는 경우 먼저 이러한 모든 인스턴스에 영향을 주도록 변경 내용을 적용해야 합니다.

샘플 장면에서 각 개별 상자 개체는 인스턴싱되었습니다. 이 작업은 장면을 FBX 형식으로 내보낼 때 관련이 있습니다.

![장면 개요](media/scene-overview.jpg)

>나중에 '복사본'을 인스턴싱된 개체로 바꾸는 것이 매우 어려우므로 장면에서의 인스턴싱에 대한 모범 사례는 인스턴싱을 진행하면서 개체를 만드는 것입니다. 

## <a name="fbx-export-process"></a>FBX 내보내기 프로세스

이제 장면 또는 장면 자산의 FBX 내보내기로 진행할 수 있습니다. 일반적으로 자산을 내보내는 경우 원하는 장면에서 해당 개체/자산만 내보내도록 선택하는 것이 좋습니다. 한 장면에 100개의 개체가 있지만 이 중 30개만 사용하려는 경우 전체 장면을 내보낼 필요가 없습니다. 따라서 전체 장면을 내보내지 않으려면 해당 개체를 선택합니다.

* 그런 다음, `File` > `Export Selection`으로 차례로 이동하고, 내보내기 대화 상자에서 아래쪽으로 이동하여 `Files of Type`을 `FBX Export`로 설정합니다. 이 창에는 FBX 내보내기 설정이 표시됩니다. FBX 내보내기에 대한 기본 설정은 아래 이미지에서 빨간색으로 강조 표시되어 있습니다.

![FBX 내보내기](media/FBX-exporting.jpg)

요구 사항에 따라(예: 자산을 클라이언트에 보내려고 하지만 많은 수의 질감 파일을 자산과 함께 보내지 않으려는 경우) 질감이 내보내는 FBX 파일 내에 포함되도록 선택할 수 있습니다. 이 옵션은 패키지할 파일이 하나뿐이지만 해당 FBX 자산의 크기를 크게 늘릴 수 있습니다. 아래와 같이 `Embed Media` 옵션을 전환하여 질감을 포함하는 옵션을 사용하도록 설정할 수 있습니다.

> [!TIP]
> 이 경우 파일의 이름이 이 조건을 반영하도록 지정되었습니다. 이는 자산을 추적할 수 있는 좋은 방법입니다. 

내보내기 구성 설정이 완료되면 오른쪽 아래의 '선택 대상 내보내기' 단추를 클릭합니다.

![미디어 포함](media/embedding-media.jpg)

## <a name="conclusion"></a>결론

일반적으로 이 유형의 재질은 실제 세계의 광물리학을 기반으로 하므로 더 사실적으로 표현됩니다. 이를 통해 장면이 실제 세계에 존재한다는 추가적인 몰입형 효과를 만듭니다.

## <a name="next-steps"></a>다음 단계

이제 장면에서 개체에 대한 고급 조명이 있는 재질을 설정하고 ARR에서 지원하는 FBX 형식으로 내보내는 데 가장 중요한 기능을 알고 있습니다. 다음 단계에서는 FBX 파일을 변환하고 ARR에서 시각화합니다.

> [!div class="nextstepaction"]
> [빠른 시작: 렌더링을 위해 모델 변환](../../quickstarts\convert-model.md)