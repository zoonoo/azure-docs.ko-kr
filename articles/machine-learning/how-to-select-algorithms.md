---
title: 기계 학습 알고리즘을 선택하는 방법
titleSuffix: Azure Machine Learning
description: 클러스터링, 분류 또는 회귀 실험에서 감독 및 감독되지 않은 학습을 위해 Azure 기계 학습 알고리즘을 선택하는 방법.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: FrancescaLazzeri
ms.author: lazzeri
ms.reviewer: cgronlun
ms.date: 03/05/2020
ms.openlocfilehash: e0482bac9569a834adf3e1cdef2b3f702980eac0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78328666"
---
# <a name="how-to-select-algorithms-for-azure-machine-learning"></a>Azure 기계 학습에 대 한 알고리즘을 선택 하는 방법

일반적인 질문은 "어떤 기계 학습 알고리즘을 사용해야 합니까?" 입니다. 선택한 알고리즘은 주로 데이터 과학 시나리오의 두 가지 측면에 따라 달라집니다.

 - **데이터로 무엇을 하고 싶으신가요?** 특히, 과거 데이터에서 학습하여 대답하고자 하는 비즈니스 질문은 무엇입니까?

 - **데이터 과학 시나리오의 요구 사항은 무엇입니까?** 특히 솔루션이 지원하는 정확도, 교육 시간, 선형성, 매개 변수 수 및 기능은 무엇입니까?

 ![알고리즘 선택에 대한 고려 사항: 무엇을 알고 싶습니까? 시나리오 요구 사항은 무엇입니까?](./media/how-to-select-algorithms/how-to-select-algorithms.png)

## <a name="business-scenarios-and-the-machine-learning-algorithm-cheat-sheet"></a>비즈니스 시나리오 및 기계 학습 알고리즘 치트 시트

[Azure 기계 학습 알고리즘 치트 시트는](https://docs.microsoft.com/azure/machine-learning/algorithm-cheat-sheet?WT.mc_id=docs-article-lazzeri) 첫 번째 고려 사항인 **데이터로 수행할 작업을**수행하는 데 도움이 됩니다. 기계 학습 알고리즘 치트 시트에서 수행하려는 작업을 찾은 다음 예측 분석 솔루션에 대한 [Azure 기계 학습 디자이너](https://docs.microsoft.com/azure/machine-learning/concept-designer?WT.mc_id=docs-article-lazzeri) 알고리즘을 찾습니다. 

머신 러닝 디자이너는 [다중 클래스 의사 결정 포리스트,](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/multiclass-decision-forest?WT.mc_id=docs-article-lazzeri) [권장 시스템,](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/evaluate-recommender?WT.mc_id=docs-article-lazzeri) [신경망 회귀,](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/neural-network-regression?WT.mc_id=docs-article-lazzeri) [다중 클래스 신경망](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/multiclass-neural-network?WT.mc_id=docs-article-lazzeri)및 [K-Means 클러스터링과](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/k-means-clustering?WT.mc_id=docs-article-lazzeri)같은 포괄적인 알고리즘 포트폴리오를 제공합니다. 각 알고리즘은 다른 유형의 기계 학습 문제를 해결하도록 설계되었습니다. 각 알고리즘의 작동 방식과 알고리즘을 최적화하기 위해 매개 변수를 조정하는 방법에 대한 설명서와 함께 전체 목록은 [Machine Learning 디자이너 알고리즘 및 모듈 참조를](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/module-reference?WT.mc_id=docs-article-lazzeri) 참조하십시오.

> [!NOTE]
> 기계 학습 알고리즘 치트 시트를 다운로드하려면 [Azure Machine 학습 알고리즘 치트 시트로](https://docs.microsoft.com/azure/machine-learning/algorithm-cheat-sheet?WT.mc_id=docs-article-lazzeri)이동하십시오.
> 
> 

Azure 기계 학습 알고리즘 치트 시트의 지침과 함께 솔루션에 대한 기계 학습 알고리즘을 선택할 때 다른 요구 사항을 염두에 두어야 합니다. 다음은 정확도, 학습 시간, 선형성, 매개변수 수 및 피처 수와 같이 고려해야 할 추가 요소입니다.

## <a name="additional-requirements-for-a-data-science-scenario"></a>데이터 과학 시나리오에 대한 추가 요구 사항

데이터로 수행할 작업을 알고 나면 솔루션에 대한 추가 요구 사항을 결정해야 합니다. 

다음 요구 사항에 대해 선택 및 장단점을 선택할 수 있습니다.

- 정확도
- 학습 시간
- 선형성
- 매개 변수 수
- 기능 수

## <a name="accuracy"></a>정확도

기계 학습의 정확도는 실제 결과의 비율로 모델의 효과를 총 사례에 측정합니다. 기계 학습 디자이너에서 [모델 평가 모듈은](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/evaluate-model?WT.mc_id=docs-article-lazzeri) 일련의 산업 표준 평가 메트릭을 계산합니다. 이 모듈을 사용하여 학습된 모델의 정확도를 측정할 수 있습니다.

가능한 가장 정확한 답변을 얻는 것이 항상 필요한 것은 아닙니다. 용도에 따라 근사치가 적절한 경우도 있습니다. 이 경우 보다 근사한 방법을 고수하여 처리 시간을 크게 줄일 수 있습니다. 대략적인 방법또한 자연적으로 과적합을 피하는 경향이 있습니다.

모델 평가 모듈을 사용하는 방법에는 세 가지가 있습니다.

- 모델을 평가하기 위해 교육 데이터에 대한 점수 생성
- 모델에서 점수를 생성하지만 해당 점수를 예약된 테스트 세트의 점수와 비교합니다.
- 동일한 데이터 집합을 사용하여 서로 다르지만 관련된 두 모델의 점수 비교

기계 학습 모델의 정확도를 평가하는 데 사용할 수 있는 메트릭 및 접근 방식의 전체 목록은 [모델 평가 모듈을](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/evaluate-model?WT.mc_id=docs-article-lazzeri)참조하십시오.

## <a name="training-time"></a>학습 시간

감독 학습에서 교육은 기록 데이터를 사용하여 오류를 최소화하는 기계 학습 모델을 구축하는 것을 의미합니다. 모델을 학습하는 데 필요한 시간 또는 분은 알고리즘에 따라 크게 다릅니다. 교육 시간은 종종 정확도와 밀접한 관련이 있습니다. 하나는 일반적으로 다른 하나를 동반합니다. 

또한 일부 알고리즘은 다른 항목보다 데이터 요소 수에 보다 민감합니다. 특히 데이터 집합이 큰 경우 시간 제한이 있기 때문에 특정 알고리즘을 선택할 수 있습니다.

기계 학습 디자이너에서 기계 학습 모델을 만들고 사용하는 것은 일반적으로 세 단계로 이루어집니다.

1.  특정 유형의 알고리즘을 선택한 다음 매개 변수 또는 하이퍼매개 변수를 정의하여 모델을 구성합니다. 

2.  레이블이 지정되고 알고리즘과 호환되는 데이터가 있는 데이터 집합을 제공합니다. 모델 [모듈을 학습에](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/train-model?WT.mc_id=docs-article-lazzeri)데이터와 모델을 모두 연결합니다.

3.  교육이 완료된 후 채점 모듈 중 하나와 함께 학습된 모델을 사용하여 새 데이터를 [예측합니다.](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/score-model?WT.mc_id=docs-article-lazzeri)

## <a name="linearity"></a>선형성

통계 및 기계 학습의 선형성은 데이터 집합에 변수와 상수 사이에 선형 관계가 있음을 의미합니다. 예를 들어 선형 분류 알고리즘은 클래스를 직선(또는 고차원 아날로그)으로 구분할 수 있다고 가정합니다.

많은 기계 학습 알고리즘에서 선형성을 활용합니다. Azure 기계 학습 디자이너에는 다음이 포함됩니다. 

- [다계층 로지스틱 회귀](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/multiclass-logistic-regression?WT.mc_id=docs-article-lazzeri)
- [2클래스 로지스틱 회귀](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/two-class-logistic-regression?WT.mc_id=docs-article-lazzeri)
- [벡터 기계 지원](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/two-class-support-vector-machine?WT.mc_id=docs-article-lazzeri)  

선형 회귀 알고리즘은 데이터가 직선을 따르는 경향이 있다고 가정합니다. 이 가정은 일부 문제에 대해 나쁘지 않지만 다른 경우에는 정확도가 떨어지지 않습니다. 단점에도 불구하고 선형 알고리즘은 첫 번째 전략으로 인기가 있습니다. 알고리즘 방식으로 간단하고 학습 시간이 빠른 경향이 있습니다.

![비선형 클래스 경계](./media/how-to-select-algorithms/nonlinear-class-boundary.png)

***비선형 클래스 경계***: *선형 분류 알고리즘에 의존하면 정확도가 낮아질 수 있습니다.*

![비선형 추세 반영 데이터](./media/how-to-select-algorithms/nonlinear-trend.png)

***비선형 추세의 데이터***: *선형 회귀 방법을 사용하면 필요 이상으로 훨씬 큰 오류가 생성됩니다.*

## <a name="number-of-parameters"></a>매개 변수 수

매개 변수는 데이터 과학자가 알고리즘을 설정할 때 전환하기 위한 노브입니다. 오류 허용 오차 또는 반복 횟수와 같이 알고리즘 동작에 영향을 주는 숫자 또는 알고리즘의 동작 방식 간의 옵션입니다. 알고리즘의 학습 시간과 정확성은 적절한 설정을 얻는 데 민감할 수 있습니다. 일반적으로 매개 변수가 많은 알고리즘은 좋은 조합을 찾기 위해 가장 많은 시행 착오를 필요로 합니다.

또는 기계 학습 디자이너에 [모델 하이퍼매개 변수 조정 모듈이](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/tune-model-hyperparameters?WT.mc_id=docs-article-lazzeri) 있습니다. 모듈은 서로 다른 설정 조합을 사용하여 여러 모델을 빌드하고 테스트합니다. 모든 모델에 대한 메트릭을 비교하여 설정 조합을 가져옵니다. 

매개 변수 공간을 확장했는지 확인하는 좋은 방법이지만 모델을 학습하는 데 필요한 시간은 매개 변수 수에 따라 기하급수적으로 증가합니다. 장점은 일반적으로 매개 변수를 많이 포함할수록 알고리즘의 유연성이 향상된다는 것입니다. 매개 변수 설정의 올바른 조합을 찾을 수 있다면 종종 매우 좋은 정확도를 얻을 수 있습니다.

## <a name="number-of-features"></a>기능 수

기계 학습에서 기능은 분석하려는 현상의 정량화 가능한 변수입니다. 특정 데이터 형식의 경우 데이터 요소에 비해 기능 수가 매우 클 수 있습니다. 보통 genetics 또는 텍스트 데이터가 그렇습니다. 

많은 수의 기능이 일부 학습 알고리즘을 수렁에 부을 수 있으므로 교육 시간이 매우 길어지다. [지원 벡터 컴퓨터는](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/two-class-support-vector-machine?WT.mc_id=docs-article-lazzeri) 특히 많은 기능이 있는 시나리오에 적합합니다. 이러한 이유로 정보 검색에서 텍스트 및 이미지 분류에 이르기까지 많은 응용 프로그램에서 사용되었습니다. 지원 벡터 기계는 분류 및 회귀 작업 모두에 사용할 수 있습니다.

기능 선택은 지정된 출력이 주어진 입력에 통계 테스트를 적용하는 프로세스를 말합니다. 목표는 출력을 더 예측하는 열을 결정하는 것입니다. 기계 학습 디자이너의 [필터 기반 기능 선택 모듈은](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/filter-based-feature-selection?WT.mc_id=docs-article-lazzeri) 선택할 수 있는 여러 기능 선택 알고리즘을 제공합니다. 이 모듈에는 Pearson 상관 관계 및 카제곱 값과 같은 상관 방지 방법이 포함되어 있습니다.

[순열 특징 중요도 모듈을](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/permutation-feature-importance?WT.mc_id=docs-article-lazzeri) 사용하여 데이터 집합에 대한 기능 중요도 점수 집합을 계산할 수도 있습니다. 그런 다음 이러한 점수를 활용하여 모델에서 사용할 최상의 기능을 결정할 수 있습니다.


## <a name="next-steps"></a>다음 단계

 - [Azure 기계 학습 디자이너에 대해 자세히 알아보기](https://docs.microsoft.com/azure/machine-learning/service/concept-designer?WT.mc_id=docs-article-lazzeri)
 - Azure 기계 학습 디자이너에서 사용할 수 있는 모든 기계 학습 알고리즘에 대한 설명은 [기계 학습 디자이너 알고리즘 및 모듈 참조를](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/module-reference?WT.mc_id=docs-article-lazzeri) 참조하십시오.
 - 딥 러닝, 머신 러닝 및 AI 간의 관계를 탐색하려면 [딥 러닝과 머신 러닝을 참조하세요.](https://docs.microsoft.com/azure/machine-learning/service/concept-deep-learning-vs-machine-learning?WT.mc_id=docs-article-lazzeri)
