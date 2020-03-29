---
title: 프로젝트 음향 베이킹 해상도
titlesuffix: Azure Cognitive Services
description: 이 개념적 개요는 음향을 굽는 동안 거친 해상도와 미세 해상도의 차이를 설명합니다.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 04/05/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: d8eb3b2cbaf7b4e842d8338eefde756f6d381111
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "68854358"
---
# <a name="project-acoustics-bake-resolution"></a>프로젝트 음향 베이킹 해상도
이 개념적 개요는 음향을 굽는 동안 거친 해상도와 미세 해상도의 차이를 설명합니다. 베이킹 워크플로의 프로브 단계에서 이 설정을 선택합니다.

## <a name="coarse-vs-fine-resolution"></a><a name="Coarse-vs-Fine-Resolution"></a>거친 대 미세 해상도

성긴 해상도 및 미세 해상도 설정 간의 유일한 차이점은 시뮬레이션이 수행되는 주파수입니다. 미세 해상도는 성긴 해상도의 2배에 해당하는 주파수를 사용합니다. 이는 음향 시뮬레이션에 여러 가지 영향을 미칩니다.

* 성긴 해상도 파장은 미세 해상도 파장의 2배이므로 복셀의 크기도 2배입니다.
* 시뮬레이션 시간은 복셀 크기와 직접적인 관계가 있으며, 성긴 해상도 베이킹은 미세 해상도 베이킹보다 약 16배 더 빠릅니다.
* 복셀 크기보다 작은 포털(예: 문 또는 창)은 시뮬레이션할 수 없습니다. 거친 설정으로 인해 이러한 작은 포털 중 일부를 시뮬레이션할 수 없습니다. 따라서 런타임에 사운드를 전달하지 않습니다. 복셀을 확인하여 이러한 문제가 나타나는지 확인할 수 있습니다.
* 시뮬레이션 주파수가 더 낮으면 모서리 및 가장자리 주변의 회절이 줄어듭니다.
* 음원은 "채워진" 복셀(예: 지오메트리가 포함된 복셀) 내부에 위치할 수 없습니다. 이로 인해 소리가 나지 않습니다. 음원을 배치하는 것이 더 어렵기 때문에 미세한 설정을 사용할 때보다 거친 더 큰 복셀 안에 있지 않습니다.
* 아래와 같이 더 큰 복셀은 정문을 더 많이 침범합니다. 첫 번째 이미지는 성긴 해상도를 사용하여 만들었지만, 두 번째 이미지는 미세 해상도를 사용하여 동일한 출입구를 만들었습니다. 빨간색으로 표시된 것처럼 미세 설정을 사용하는 경우 출입구로의 침입이 훨씬 적습니다. 파란색 선은 기하 도형으로 정의되는 출입구이지만, 빨간색 선은 복셀 크기로 정의되는 유효 음향 출입구입니다. 지정된 상황에서 이러한 침범이 어떻게 발생하느냐는 복셀이 정문 기하 도형에 얼마나 잘 맞게 정렬되는지에 달려 있습니다. 이러한 상태는 장면에 있는 개체의 크기 및 위치에 따라 결정됩니다.

![Unreal의 출입구를 채우는 거친 복셀 스크린샷](media/unreal-coarse-bake.png)

![Unreal 출입구의 세밀한 복셀 스크린샷](media/unreal-fine-bake.png)

## <a name="next-steps"></a>다음 단계

[언리얼](unreal-baking.md) 또는 [유니티](unity-baking.md) 플러그인을 사용하여 거칠고 미세한 해상도 설정을 직접 사용해 보십시오.
