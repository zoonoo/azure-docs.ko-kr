---
title: 개체 및 리소스 수명
description: 다양한 형식의 수명 관리에 대해 설명합니다.
author: jakrams
ms.author: jakras
ms.date: 02/06/2020
ms.topic: conceptual
ms.openlocfilehash: 0945b35f7aff8e93a1a3ba23b89db288db3d8efa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "99593894"
---
# <a name="object-and-resource-lifetime"></a>개체 및 리소스 수명

Azure Remote Rendering은 두 가지 형식, 즉 **개체** 와 **리소스** 를 구분합니다.

## <a name="object-lifetime"></a>개체 수명

*개체* 는 사용자의 재량에 따라 생성, 수정 및 제거할 수 있는 것으로 간주됩니다. 개체는 자유롭게 복제할 수 있으며 각 인스턴스는 시간이 지남에 따라 변형될 수 있습니다. 따라서 [엔터티](entities.md) 및 [구성 요소](components.md)는 개체입니다.

개체의 수명은 전적으로 사용자 제어에 달려있습니다. 그러나 클라이언트 쪽 표현의 수명과는 관련이 없습니다. `Entity` 및 `Component`와 같은 클래스에는 원격 렌더링 호스트에서 개체의 할당을 취소하기 위해 호출해야 하는 `Destroy` 함수가 있습니다. 또한 `Entity.Destroy()`는 해당 계층의 엔터티, 자식 및 모든 구성 요소를 제거합니다.

## <a name="resource-lifetime"></a>리소스 수명

*리소스* 의 수명은 완전히 원격 렌더링 호스트에 의해 관리됩니다. 리소스는 내부적으로 참조 횟수가 계산됩니다. 더 이상 참조되지 않는 경우 리소스의 할당이 취소됩니다.

대부분의 리소스는 일반적으로 파일에서 로드하여 간접적으로만 만들 수 있습니다. 동일한 파일이 여러 번 로드되면 Azure Remote Rendering은 동일한 참조를 반환하고 데이터를 다시 로드하지 않습니다.

많은 리소스는 변경이 불가능합니다(예: [메시](meshes.md) 및 [질감](textures.md)). 그러나 일부 리소스는 변경 가능합니다(예: [재질](materials.md)). 리소스는 자주 공유되므로 리소스를 수정하면 여러 개체에 영향을 줄 수 있습니다. 예를 들어 재질의 색을 변경하면 메시를 사용하는 모든 개체의 색이 변경되어 해당 재질을 참조합니다.

### <a name="built-in-resources"></a>기본 제공 리소스

Azure Remote Rendering에는 `RenderingSession.Connection.LoadXYZAsync()`를 호출할 때 `builtin://`으로 각 식별자를 추가하여 로드할 수 있는 몇 가지 기본 제공 리소스가 포함되어 있습니다. 사용할 수 있는 기본 제공 리소스는 각 기능에 대한 설명서에 나와 있습니다. 예를 들어, [하늘 챕터](../overview/features/sky.md)에는 기본 제공되는 하늘 질감이 나열됩니다.

## <a name="general-lifetime"></a>일반 수명

모든 개체 및 리소스의 수명은 연결에 바인딩됩니다. 연결 해제 시 모든 항목은 무시됩니다. 동일한 세션에 다시 연결하는 경우 장면 그래프가 비어 있고 모든 리소스가 제거됩니다.

실제로 세션에 동일한 리소스를 로드하는 경우 연결을 끊은 후에는 일반적으로 처음보다 빠릅니다. 대부분의 리소스를 Azure Storage에서 처음 다운로드해야 하기 때문입니다. 이 경우 두 번째는 필요하지 않으므로 상당한 시간을 절약할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [엔터티](entities.md)
* [Components](components.md)
* [모델](models.md)
