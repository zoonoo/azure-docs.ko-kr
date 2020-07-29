---
title: 메시
description: Azure 원격 렌더링 범위에서 메시 정의
author: florianborn71
ms.author: flborn
ms.date: 02/05/2020
ms.topic: conceptual
ms.openlocfilehash: b800272a538243d39a89e4eed64bc5cbc2d53ad8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "80681767"
---
# <a name="meshes"></a>메시

## <a name="mesh-resource"></a>메시 리소스

메시는 [모델 변환을](../how-tos/conversion/model-conversion.md)통해서만 만들 수 있는 변경할 수 없는 [공유 리소스](../concepts/lifetime.md)입니다. 메시는 하나 또는 여러 개의 *submeshes*를 포함 합니다. 각 하위 메시에 대해는 기본적으로 렌더링 되어야 하는 [자료](materials.md) 를 참조 합니다. 3D 공간에 망상을 놓으려면 [MeshComponent](#meshcomponent) 을 [엔터티에](entities.md)추가 합니다.

### <a name="mesh-resource-properties"></a>메시 리소스 속성

`Mesh`클래스 속성은 다음과 같습니다.

* **자료:** 재질의 배열입니다. 각 자료는 다른 하위 메시에 대해에서 사용 됩니다. 배열의 여러 항목은 동일한 [자료](materials.md)를 참조할 수 있습니다. 이 데이터는 런타임에 수정할 수 없습니다.

* **범위:** 메시 꼭 짓 점의 로컬 공간 축에 맞춰진 경계 상자 (AABB)입니다.

## <a name="meshcomponent"></a>MeshComponent

`MeshComponent`클래스는 메시 리소스의 인스턴스를 저장 하는 데 사용 됩니다. 각 MeshComponent는 단일 메시를 참조 합니다. 각 하위 메시에 대해를 렌더링 하는 데 사용 되는 자료를 재정의할 수 있습니다.

### <a name="meshcomponent-properties"></a>MeshComponent 속성

* **메시:** 이 구성 요소에서 사용 하는 메시 리소스입니다.

* **자료:** 메시 구성 요소 자체에 지정 된 재질의 배열입니다. 배열의 길이는 항상 메시 리소스의 *재질* 배열과 동일 합니다. 메시 기본값에서 재정의 되지 않아야 하는 자료는이 배열에서 *null* 로 설정 됩니다.

* **UsedMaterials:** 각 하위 메시에 대해에 실제로 사용 되는 자료의 배열입니다. 는 null이 아닌 값에 대 한 *재질* 배열의 데이터와 동일 합니다. 그렇지 않으면 메시 인스턴스의 *재질* 배열 값을 포함 합니다.

## <a name="next-steps"></a>다음 단계

* [재질](materials.md)
