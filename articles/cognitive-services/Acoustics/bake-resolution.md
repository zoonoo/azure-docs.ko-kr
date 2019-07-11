---
title: 프로젝트 소음 적용 확인
titlesuffix: Azure Cognitive Services
description: 이 개념 개요 소음 이루어졌습니다 하는 동안 대략적인 및 세밀 하 게 해결 간의 차이점을 설명 합니다.
services: cognitive-services
author: KyleStorck
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 04/05/2019
ms.author: KyleStorck
ms.openlocfilehash: 8684cf16e41c763db01c064242cc5466b1c83f01
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67621405"
---
# <a name="project-acoustics-bake-resolution"></a>프로젝트 소음 적용 확인
이 개념 개요 소음 이루어졌습니다 하는 동안 대략적인 및 세밀 하 게 해결 간의 차이점을 설명 합니다. 제과점 워크플로 프로브 단계에서이 설정을 선택 합니다.

## <a name="Coarse-vs-Fine-Resolution"></a>성긴 해상도 및 미세 해상도

성긴 해상도 및 미세 해상도 설정 간의 유일한 차이점은 시뮬레이션이 수행되는 주파수입니다. 미세 해상도는 성긴 해상도의 2배에 해당하는 주파수를 사용합니다. 이 여러 가지 의미가 내포 음향 시뮬레이션에:

* 성긴 해상도 파장은 미세 해상도 파장의 2배이므로 복셀의 크기도 2배입니다.
* 시뮬레이션 시간은 복셀 크기와 직접적인 관계가 있으며, 성긴 해상도 베이킹은 미세 해상도 베이킹보다 약 16배 더 빠릅니다.
* 포털 (예: 문 또는 windows) voxel 크기 보다 작으면를 시뮬레이션할 수 없습니다. 정교 하지 않은 설정 하지 않으면 시뮬레이션할 작은 이러한 포털 중 발생할 수 있습니다. 따라서 이러한 없습니다 음향을 런타임에 전달 합니다. 복셀을 확인하여 이러한 문제가 나타나는지 확인할 수 있습니다.
* 시뮬레이션 주파수가 더 낮으면 모서리 및 가장자리 주변의 회절이 줄어듭니다.
* 사운드 원본 "채워진된" voxels (즉, 기 하 도형에 있는 voxels) 내에서 찾을 수 없습니다. 이 인해 소리 없음. 더 세밀 하 게 설정을 사용 하는 경우 보다 정교 하지 않은의 더 큰 voxels 내 가장 되지 않으므로 사운드 소스를 배치 하는 것이 어렵습니다.
* 아래와 같이 더 큰 복셀은 정문을 더 많이 침범합니다. 첫 번째 이미지는 성긴 해상도를 사용하여 만들었지만, 두 번째 이미지는 미세 해상도를 사용하여 동일한 출입구를 만들었습니다. 빨간색으로 표시된 것처럼 미세 설정을 사용하는 경우 출입구로의 침입이 훨씬 적습니다. 파란색 선은 기하 도형으로 정의되는 출입구이지만, 빨간색 선은 복셀 크기로 정의되는 유효 음향 출입구입니다. 지정된 상황에서 이러한 침범이 어떻게 발생하느냐는 복셀이 정문 기하 도형에 얼마나 잘 맞게 정렬되는지에 달려 있습니다. 이러한 상태는 장면에 있는 개체의 크기 및 위치에 따라 결정됩니다.

![Unreal의 출입구를 채우는 거친 복셀 스크린샷](media/unreal-coarse-bake.png)

![Unreal 출입구의 세밀한 복셀 스크린샷](media/unreal-fine-bake.png)

## <a name="next-steps"></a>다음 단계

정교 하지 않은 및 세밀 하 게 확인 설정을 사용 하 여 직접 사용해 우리의 [Unreal](unreal-baking.md) 하거나 [Unity](unity-baking.md) 플러그 인입니다.