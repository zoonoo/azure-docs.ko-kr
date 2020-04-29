---
title: '다중 클래스 승격 된 의사 결정 트리: 모듈 참조'
titleSuffix: Azure Machine Learning
description: Azure Machine Learning에서 다중 클래스 승격 된 의사 결정 트리 모듈을 사용 하 여 레이블이 지정 된 데이터를 사용 하는 분류자를 만드는 방법에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/19/2020
ms.openlocfilehash: cfe35f81526a729092edf522f693ccd18494d1ec
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82137827"
---
# <a name="multiclass-boosted-decision-tree"></a>다중 클래스 향상된 의사 결정 트리

이 문서에서는 Azure Machine Learning designer (미리 보기)의 모듈을 설명 합니다.

이 모듈을 사용 하 여 승격 된 의사 결정 트리 알고리즘을 기반으로 기계 학습 모델을 만들 수 있습니다.

승격 된 의사 결정 트리는 두 번째 트리가 첫 번째 트리의 오류를 수정 하 고, 세 번째 트리가 첫 번째 및 두 번째 트리의 오류를 수정 하는 등의 앙상블 학습 방법입니다. 예측은 트리의 앙상블을 기반으로 합니다.

## <a name="how-to-configure"></a>구성 방법 

이 모듈은 학습 되지 않은 분류 모델을 만듭니다. 분류는 감독 된 학습 방법 이므로 모든 행에 대 한 값이 있는 레이블 열을 포함 하는 *레이블이 지정 된 데이터 집합이* 필요 합니다.

[학습 모델](././train-model.md)을 사용 하 여 이러한 유형의 모델을 학습할 수 있습니다. 

1.  파이프라인에 **다중 클래스 승격 된 의사 결정 트리** 모듈을 추가 합니다.

1.  **강사 모드 만들기** 옵션을 설정 하 여 모델을 학습 하는 방법을 지정 합니다.

    + **단일 매개 변수**: 모델을 구성 하는 방법을 아는 경우 특정 값 집합을 인수로 제공할 수 있습니다.
    
    + **매개 변수 범위**: 가장 적합 한 매개 변수를 잘 모르겠으면 매개 변수 스윕을 실행 하려는 경우이 옵션을 선택 합니다. 반복할 값의 범위를 선택 하 고 [모델 조정 하이퍼 매개 변수 변수](tune-model-hyperparameters.md) 는 제공 된 모든 설정의 가능한 조합에 대해 반복 하 여 최적의 결과를 생성 하는 하이퍼 매개 변수를 결정 합니다.  

1. **트리 당 리프의 최대 수** 는 모든 트리에서 만들 수 있는 최대 터미널 노드 (리프) 수를 제한 합니다.
    
        By increasing this value, you potentially increase the size of the tree and achieve higher precision, at the risk of overfitting and longer training time.
  
1. **리프 노드당 최소 샘플 수** 는 트리에서 터미널 노드 (리프)를 만드는 데 필요한 사례 수를 나타냅니다.  

         By increasing this value, you increase the threshold for creating new rules. For example, with the default value of 1, even a single case can cause a new rule to be created. If you increase the value to 5, the training data would have to contain at least five cases that meet the same conditions.

1. 학습 **률** 은 학습 하는 동안 단계 크기를 정의 합니다. 0에서 1 사이의 숫자를 입력 하십시오.

         The learning rate determines how fast or slow the learner converges on an optimal solution. If the step size is too large, you might overshoot the optimal solution. If the step size is too small, training takes longer to converge on the best solution.

1. **생성 된 트리 수** 앙상블에서 만들 의사 결정 트리의 총 수를 나타냅니다. 추가 의사 결정 트리를 만들면 적용 범위가 확대될 수 있지만 학습 시간이 증가됩니다.

1. **난수 초기값** 은 임의의 초기값으로 사용할 음수가 아닌 정수를 선택적으로 설정 합니다. 초기값을 지정 하면 동일한 데이터 및 매개 변수가 있는 실행에 대해 재현 가능성 됩니다.  

         The random seed is set by default to 42. Successive runs using different random seeds can have different results.

1. 모델 학습:

    + 담당자 **모드 만들기** 를 **단일 매개 변수로**설정한 경우 태그가 지정 된 데이터 집합 및 [모델 학습](train-model.md) 모듈을 연결 합니다.  
  
    + **만든이 모드** 를 **매개 변수 범위**로 설정 하는 경우에는 태그가 지정 된 데이터 집합을 연결 하 고 [모델 모델링 hyperparameters](tune-model-hyperparameters.md)를 사용 하 여 모델을 학습 합니다.  
  
    > [!NOTE]
    > 
    > [모델 학습](train-model.md)에 매개 변수 범위를 전달 하는 경우 단일 매개 변수 목록의 기본값만 사용 합니다.  
    > 
    > 단일 매개 변수 값 집합을 [모델 하이퍼 매개 변수 조정](tune-model-hyperparameters.md) 모듈에 전달 하는 경우 각 매개 변수에 대 한 설정 범위가 필요한 경우 값을 무시 하 고 학습자에 대 한 기본값을 사용 합니다.  
    > 
    > **매개 변수 범위** 옵션을 선택 하 고 매개 변수에 대해 단일 값을 입력 하는 경우 다른 매개 변수가 값 범위에서 변경 되더라도 지정한 단일 값은 스윕 전체에서 사용 됩니다.

## <a name="next-steps"></a>다음 단계

Azure Machine Learning [사용할 수 있는 모듈 집합](module-reference.md) 을 참조 하세요. 
