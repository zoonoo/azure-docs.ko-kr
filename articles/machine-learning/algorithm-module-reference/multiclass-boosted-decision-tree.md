---
title: '다중 클래스 부스트 의사 결정 트리: 모듈 참조'
titleSuffix: Azure Machine Learning
description: Azure 기계 학습에서 다중 클래스 강화 의사 결정 트리 모듈을 사용하여 레이블이 지정된 데이터를 사용하여 분류기를 만드는 방법에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/19/2020
ms.openlocfilehash: 7d51e3007b7773e28d846f8d30178426f5668cfb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77920062"
---
# <a name="multiclass-boosted-decision-tree"></a>다중 클래스 향상된 의사 결정 트리

이 문서에서는 Azure 기계 학습 디자이너(미리 보기)의 모듈에 대해 설명합니다.

이 모듈을 사용하여 향상된 의사 결정 트리 알고리즘을 기반으로 하는 기계 학습 모델을 만듭니다.

증폭된 결정 트리는 두 번째 트리가 첫 번째 트리의 오류를 수정하고 세 번째 트리가 첫 번째 트리와 두 번째 트리의 오류를 수정하는 앙상블 학습 방법입니다. 예측은 함께 나무의 앙상블을 기반으로합니다.

## <a name="how-to-configure"></a>구성 방법 

이 모듈은 학습되지 않은 분류 모델을 만듭니다. 분류는 감독되는 학습 방법이기 때문에 모든 행에 대한 값이 있는 레이블 열이 포함된 *레이블이 지정된 데이터 집합이* 필요합니다.

[기차](././train-model.md)모델을 사용하여 이러한 유형의 모델을 학습할 수 있습니다. 

1.  파이프라인에 **다중 클래스 부스트 의사 결정 트리** 모듈을 추가합니다.

1.  **트레이너 만들기 모드** 만들기 옵션을 설정하여 모델을 학습할 방법을 지정합니다.

    + **단일 매개 변수**: 모델을 구성하는 방법을 알고 있는 경우 특정 값 집합을 인수로 제공할 수 있습니다.
    
    + **매개 변수 범위**: 최상의 매개 변수를 잘 모르고 매개 변수 스윕을 실행하려는 경우 이 옵션을 선택합니다. 반복할 값 범위를 선택하고 [모델 하이퍼매개 변수 조정은](tune-model-hyperparameters.md) 제공한 설정의 가능한 모든 조합을 반복하여 최적의 결과를 생성하는 하이퍼매개 변수를 결정합니다.  

1. **트리당 최대 잎 수는** 모든 트리에서 만들 수 있는 터미널 노드(잎)의 최대 수를 제한합니다.
    
        By increasing this value, you potentially increase the size of the tree and achieve higher precision, at the risk of overfitting and longer training time.
  
1. **리프 노드당 최소 샘플 수는** 트리에서 터미널 노드(리프)를 만드는 데 필요한 사례 수를 나타냅니다.  

         By increasing this value, you increase the threshold for creating new rules. For example, with the default value of 1, even a single case can cause a new rule to be created. If you increase the value to 5, the training data would have to contain at least five cases that meet the same conditions.

1. **학습 속도는** 학습 하는 동안 단계 크기를 정의 합니다. 0에서 1 사이의 숫자를 입력합니다.

         The learning rate determines how fast or slow the learner converges on an optimal solution. If the step size is too large, you might overshoot the optimal solution. If the step size is too small, training takes longer to converge on the best solution.

1. **구성된 트리 수는** 앙상블에서 작성할 의사 결정 트리의 총 수를 나타냅니다. 추가 의사 결정 트리를 만들면 적용 범위가 확대될 수 있지만 학습 시간이 증가됩니다.

1. **임의 수 시드는** 선택적으로 비음수 정수를 설정하여 임의 시드 값으로 사용합니다. 시드를 지정하면 동일한 데이터와 매개 변수가 있는 런에서 재현성이 보장됩니다.  

         The random seed is set by default to 42. Successive runs using different random seeds can have different results.

> [!Note]
> **트레이너 만들기 모드를** 단일 매개 **변수로**설정하면 태그가 지정된 데이터 집합과 [모델 학습](./train-model.md) 모듈을 연결합니다.

## <a name="next-steps"></a>다음 단계

Azure 기계 학습에 사용할 수 있는 [모듈 집합을](module-reference.md) 참조하십시오. 
