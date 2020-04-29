---
title: 개체 및 리소스 수명
description: 다양 한 형식의 수명 관리에 대해 설명 합니다.
author: jakrams
ms.author: jakras
ms.date: 02/06/2020
ms.topic: conceptual
ms.openlocfilehash: d031ff4a6ee86da2843f0f18ac428c50f7cfc121
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80681871"
---
# <a name="object-and-resource-lifetime"></a>개체 및 리소스 수명

Azure 원격 렌더링은 두 가지 유형, 즉 **개체** 와 **리소스**를 구분 합니다.

## <a name="object-lifetime"></a>개체 수명

*개체* 는 사용자가 신중 하 게 생성, 수정 및 제거할 수 있는 것으로 간주 됩니다. 개체는 자유롭게 복제할 수 있으며 각 인스턴스는 시간이 지남에 따라 변경할 수 있습니다. 따라서 [엔터티](entities.md) 및 [구성 요소](components.md) 는 개체입니다.

개체의 수명은 전적으로 사용자 제어에 있습니다. 그러나 클라이언트 쪽 표현의 수명과는 관련이 없습니다. 및 `Entity` `Component` 와 같은 클래스에 `Destroy` 는 원격 렌더링 호스트에서 개체의 할당을 취소 하기 위해 호출 해야 하는 함수가 있습니다. 또한 `Entity.Destroy()` 은 해당 계층의 엔터티, 자식 및 모든 구성 요소를 제거 합니다.

## <a name="resource-lifetime"></a>리소스 수명

*리소스* 는 원격 렌더링 호스트에서 수명이 완전히 관리 되는 항목입니다. 리소스는 내부적으로 참조 횟수가 계산 됩니다. 둘 중 어떤 것도 더 이상 참조 하지 않는 경우 할당이 취소 됩니다.

대부분의 리소스는 일반적으로 파일에서 로드 하 여 간접적 으로만 만들 수 있습니다. 동일한 파일이 여러 번 로드 되 면 Azure 원격 렌더링은 동일한 참조를 반환 하 고 데이터를 다시 로드 하지 않습니다.

인스턴스 [메시](meshes.md) 및 [질감](textures.md)에 대해 많은 리소스를 변경할 수 없습니다. 그러나 일부 리소스는 변경 가능 합니다 (예: [자료](materials.md)). 리소스는 자주 공유 되므로 리소스를 수정 하면 여러 개체에 영향을 줄 수 있습니다. 예를 들어 재질의 색을 변경 하면 메시를 사용 하는 모든 개체의 색이 변경 되어 해당 자료를 참조 합니다.

### <a name="built-in-resources"></a>기본 제공 리소스

Azure 원격 렌더링에는를 `builtin://` `AzureSession.Actions.LoadXYZAsync()`호출 하는 동안를 사용 하 여 로드할 수 있는 몇 가지 기본 제공 리소스가 포함 되어 있습니다. 사용할 수 있는 기본 제공 리소스는 각각의 각 기능에 대 한 설명서에 나와 있습니다. 예를 들어, [공중 챕터](../overview/features/sky.md) 에는 기본 제공 되는 하늘 질감이 나열 됩니다.

## <a name="general-lifetime"></a>일반 수명

모든 개체 및 리소스의 수명은 연결에 바인딩됩니다. 연결 끊기 시 모든 항목은 무시 됩니다. 동일한 세션에 다시 연결 하는 경우 장면 그래프가 비어 있고 모든 리소스가 제거 됩니다.

실제로 세션에 동일한 리소스를 로드 하는 경우 연결을 끊은 후에는 일반적으로 처음 보다 빠릅니다. 이는 항상 Azure Storage에서 대부분의 리소스를 다운로드 해야 하는 경우입니다 .이 경우 두 번째는 필요 하지 않으므로 상당한 시간을 절약할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [Entities](entities.md)
* [요소도](components.md)
* [모델](models.md)
