---
title: 메시
description: Azure 원격 렌더링 범위의 메시 정의
author: florianborn71
ms.author: flborn
ms.date: 02/05/2020
ms.topic: conceptual
ms.openlocfilehash: b800272a538243d39a89e4eed64bc5cbc2d53ad8
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681767"
---
# <a name="meshes"></a>메시

## <a name="mesh-resource"></a>메시 리소스

Meshes는 [모델 변환을](../how-tos/conversion/model-conversion.md)통해서만 만들 수 있는 변경할 수 없는 [공유 리소스입니다.](../concepts/lifetime.md) 메시에는 하나 또는 여러 개의 *하위 mesheshes가 포함됩니다.* 각 하위 메시는 기본적으로 렌더링해야 하는 [재질을](materials.md) 참조합니다. 메시를 3D 공간에 배치하려면 [엔터티에](entities.md) [메시 구성 요소를](#meshcomponent) 추가합니다.

### <a name="mesh-resource-properties"></a>메시 리소스 속성

클래스 `Mesh` 속성은 다음과 같습니다.

* **재료:** 재료의 배열. 각 재질은 다른 서브메시에 의해 사용됩니다. 배열의 여러 항목이 동일한 [재질을](materials.md)참조할 수 있습니다. 이 데이터는 런타임에 수정할 수 없습니다.

* **경계:** 메시 정점의 로컬 공간 축 정렬 경계 상자(AABB)입니다.

## <a name="meshcomponent"></a>메시 구성 요소

클래스는 `MeshComponent` 메시 리소스의 인스턴스를 배치하는 데 사용됩니다. 각 메시 구성 요소는 단일 메시를 참조합니다. 각 하위 메시를 렌더링하는 데 사용되는 재질을 재정의할 수 있습니다.

### <a name="meshcomponent-properties"></a>메시 구성요소 속성

* **메쉬:** 이 구성 요소에서 사용되는 메시 리소스입니다.

* **재료:** 메시 구성요소 자체에 지정된 재질의 배열입니다. 배열은 항상 메시 리소스의 *재질* 배열과 동일한 길이를 갖습니다. 메시 기본값에서 재정의되지 않는 재질은 이 배열에서 *null로* 설정됩니다.

* **사용된 재료:** 각 하위 메시에 대해 실제로 사용되는 재질의 배열입니다. null이 아닌 값에 대해 *Materials* 배열의 데이터와 동일합니다. 그렇지 않으면 메시 인스턴스의 *재질* 배열의 값이 포함됩니다.

## <a name="next-steps"></a>다음 단계

* [원자재](materials.md)
