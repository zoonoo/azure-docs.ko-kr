---
title: '변환 열 선택: 모듈 참조'
titleSuffix: Azure Machine Learning
description: Azure 기계 학습에서 열 변환 선택 모듈을 사용하여 지정된 데이터 집합과 동일한 열 하위 집합을 선택하는 변환을 만드는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: a5264c14294f84858cd489f5892b8cdd19e117d0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79455913"
---
# <a name="select-columns-transform"></a>열 변환 선택

이 문서에서는 Azure 기계 학습 디자이너(미리 보기)에서 열 변환 선택 모듈을 사용하는 방법을 설명합니다. 열 변환 선택 모듈의 목적은 예측 가능하고 일관된 열 집합이 다운스트림 기계 학습 작업에 사용되도록 하는 것입니다.

이 모듈은 특정 열이 필요한 점수 매기기와 같은 작업에 유용합니다. 사용 가능한 열이 변경되어 파이프라인이 중단되어 결과가 변경될 수 있습니다.

변형 선택을 사용하여 열 세트를 만들고 저장합니다. 그런 다음 [변환 적용](apply-transformation.md) 모듈을 사용하여 이러한 선택을 새 데이터에 적용합니다.

## <a name="how-to-use-select-columns-transform"></a>선택 열 변환 사용 방법

이 시나리오에서는 피처 선택을 사용하여 모델을 학습하는 데 사용할 동적 열 집합을 생성한다고 가정합니다. 점수 매기기 프로세스에 대해 열 선택이 동일하게 유지되도록 하려면 열 변환 선택 모듈을 사용하여 열 선택을 캡처하고 파이프라인의 다른 곳에 적용합니다.

1. 디자이너의 파이프라인에 입력 데이터 집합을 추가합니다.

2. 필터 기반 [피쳐 선택](filter-based-feature-selection.md)의 인스턴스를 추가합니다.

3. 모듈을 연결하고 기능 선택 모듈을 구성하여 입력 데이터 집합에서 최상의 기능을 자동으로 찾습니다.

4. [학습 모델의](train-model.md) 인스턴스를 추가하고 [필터 기반 피쳐 선택의](filter-based-feature-selection.md) 출력을 학습의 입력으로 사용합니다.

    > [!IMPORTANT]
    > 피쳐 중요도는 열의 값을 기반으로 하므로 [모델에](train-model.md)입력할 수 있는 열을 미리 알 수 없습니다.  
5. 열 변환 선택 모듈의 인스턴스를 연결합니다. 

    이 단계에서는 다른 데이터 집합에 저장하거나 적용할 수 있는 변환으로 열 선택을 생성합니다. 이 단계를 통해 피쳐 선택에서 식별된 열이 다른 모듈에서 재사용할 수 있도록 저장됩니다.

6. 점수 모델 모듈을 [추가합니다.](score-model.md) 

   *입력 데이터 집합을 연결하지 마십시오.* 대신 변환 [적용](apply-transformation.md) 모듈을 추가하고 피쳐 선택 변환의 출력을 연결합니다.

   > [!IMPORTANT]
   > 점수 매기기 데이터 집합에 [필터 기반 기능 선택을](filter-based-feature-selection.md) 적용하고 동일한 결과를 얻을 수 없습니다. 피쳐 선택은 값을 기반으로 하므로 다른 열 집합을 선택할 수 있으므로 점수 매기기 작업이 실패할 수 있습니다.
7. 파이프라인을 제출합니다.

열 선택을 저장한 다음 적용하는 이 프로세스는 학습 및 채점에 동일한 데이터 스키마를 사용할 수 있도록 합니다.


## <a name="next-steps"></a>다음 단계

Azure 기계 학습에 사용할 수 있는 [모듈 집합을](module-reference.md) 참조하십시오. 
