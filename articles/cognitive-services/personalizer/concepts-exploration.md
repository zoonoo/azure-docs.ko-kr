---
title: 탐색 - Personalizer
titleSuffix: Azure Cognitive Services
description: 탐색을 통해 Personalizer는 사용자 동작이 변하더라도 계속 좋은 결과를 제공할 수 있습니다. 탐색 설정의 선택은 모델을 개선하기 위한 사용자 상호 작용 중 탐색하는 비율에 관한 비즈니스 결정입니다.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 10/23/2019
ms.openlocfilehash: 00b9cc881d9414e957d7503bb6156095bb821c06
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/24/2020
ms.locfileid: "87128151"
---
# <a name="exploration-and-exploitation"></a>탐색 및 이용

탐색을 통해 Personalizer는 사용자 동작이 변하더라도 계속 좋은 결과를 제공할 수 있습니다.

Personalizer는 순위 호출을 받으면 다음 기능을 수행하는 RewardActionID를 반환합니다.
* 악용을 사용하여 현재 기계 학습 모델을 기반으로 가장 유력한 사용자 동작과 일치시킵니다.
* 순위에서 확률이 가장 높은 작업과 일치하지 않는 탐색을 사용합니다.

Personalizer는 현재 *입실론 그리디*라는 알고리즘을 사용하여 탐색합니다. 

## <a name="choosing-an-exploration-setting"></a>탐색 설정 선택

Personalizer에 대 한 Azure Portal **구성** 페이지에서 탐색에 사용할 트래픽의 비율을 구성 합니다. 이 설정은 탐색을 수행하는 순위 호출의 백분율을 결정합니다. 

Personalizer는 각 순위 호출에 대해 이 확률을 사용하여 탐색할지 또는 이용할지를 결정합니다. 이는 특정 사용자 ID에 대해 처리를 잠그는 일부 A/B 프레임워크의 동작과 다릅니다.

## <a name="best-practices-for-choosing-an-exploration-setting"></a>탐색 설정 선택 모범 사례

탐색 설정의 선택은 모델을 개선하기 위한 사용자 상호 작용 중 탐색하는 비율에 관한 비즈니스 결정입니다. 

0으로 설정하면 Personalizer의 많은 이점이 무효화됩니다. 0으로 설정한 경우 Personalizer는 더 나은 사용자 상호 작용을 검색하기 위해 사용자 상호 작용을 사용하지 않습니다. 이 때문에 모델 정체 및 드리프트가 발생하고 궁극적으로 성능이 저하됩니다.

너무 큰 설정을 지정하면 사용자 동작 학습의 이점이 무효화됩니다. 100%로 설정하면 상수 불규칙화를 암시하며 사용자로부터 학습한 동작이 결과에 영향을 주지 않습니다.

Personalizer가 탐색하는지 또는 이용하는지를 알고 있는지 여부에 따라 애플리케이션 동작을 변경하지 않아야 합니다. 이렇게 하면 학습 편차가 발생하여 결국 잠재적 성능이 감소할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[보충 학습](concepts-reinforcement-learning.md) 