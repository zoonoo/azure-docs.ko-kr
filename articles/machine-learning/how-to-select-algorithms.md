---
title: 기계 학습 알고리즘을 선택 하는 방법
titleSuffix: Azure Machine Learning
description: 클러스터링, 분류 또는 회귀 실험에서 감독 및 자율 학습을 위한 Azure Machine Learning 알고리즘을 선택 하는 방법입니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: FrancescaLazzeri
ms.author: lazzeri
ms.reviewer: cgronlun
ms.date: 05/07/2020
ms.openlocfilehash: 98f7edac5bbec7a88999c728b2e4db8be7a3d2b5
ms.sourcegitcommit: b396c674aa8f66597fa2dd6d6ed200dd7f409915
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2020
ms.locfileid: "82891357"
---
# <a name="how-to-select-algorithms-for-azure-machine-learning"></a>Azure Machine Learning에 대 한 알고리즘을 선택 하는 방법

일반적인 질문은 "어떤 기계 학습 알고리즘을 사용 해야 하나요?"입니다. 선택 하는 알고리즘은 주로 데이터 과학 시나리오의 두 가지 서로 다른 측면에 따라 다릅니다.

 - **데이터를 사용 하 여 할 일은 무엇 인가요?** 특히 과거 데이터를 학습 하 여 답변할 비즈니스 질문은 무엇 인가요?

 - **데이터 과학 시나리오의 요구 사항은 무엇 인가요?** 특히 정확도, 학습 시간, 선형, 매개 변수 수 및 솔루션에서 지 원하는 기능 수는 무엇 인가요?

 ![알고리즘 선택 시 고려 사항: 알아야 할 사항 시나리오 요구 사항은 무엇 인가요?](./media/how-to-select-algorithms/how-to-select-algorithms.png)

## <a name="business-scenarios-and-the-machine-learning-algorithm-cheat-sheet"></a>비즈니스 시나리오 및 Machine Learning 알고리즘 참고 자료 시트

[Azure Machine Learning Algorithm 참고 자료 시트](https://docs.microsoft.com/azure/machine-learning/algorithm-cheat-sheet?WT.mc_id=docs-article-lazzeri) 를 사용 하면 **데이터를 사용 하 여 수행할 작업을**첫 번째로 고려 하 여 수행할 수 있습니다. Machine Learning Algorithm 참고 자료 시트에서 수행 하려는 작업을 찾은 다음 예측 분석 솔루션에 대 한 [Azure Machine Learning 디자이너](https://docs.microsoft.com/azure/machine-learning/concept-designer?WT.mc_id=docs-article-lazzeri) 알고리즘을 찾습니다. 

Machine Learning designer는 [다중 클래스 의사 결정 포리스트](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/multiclass-decision-forest?WT.mc_id=docs-article-lazzeri), [권장 사항 시스템](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/evaluate-recommender?WT.mc_id=docs-article-lazzeri), [신경망 회귀](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/neural-network-regression?WT.mc_id=docs-article-lazzeri), [다중 클래스 신경망](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/multiclass-neural-network?WT.mc_id=docs-article-lazzeri)및 [K = 클러스터링 이라는](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/k-means-clustering?WT.mc_id=docs-article-lazzeri)포괄적인 알고리즘 포트폴리오를 제공 합니다. 각 알고리즘은 다른 유형의 기계 학습 문제를 해결 하도록 설계 되었습니다. 각 알고리즘의 작동 방식 및 알고리즘을 최적화 하는 매개 변수를 조정 하는 방법에 대 한 설명서와 함께 전체 목록은 [Machine Learning 디자이너 알고리즘 및 모듈 참조](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/module-reference?WT.mc_id=docs-article-lazzeri) 를 참조 하세요.

> [!NOTE]
> Machine learning 알고리즘 참고 자료 시트를 다운로드 하려면 [Azure machine learning 알고리즘 참고 자료 시트로](https://docs.microsoft.com/azure/machine-learning/algorithm-cheat-sheet?WT.mc_id=docs-article-lazzeri)이동 합니다.
> 
> 

Azure Machine Learning Algorithm 참고 자료 시트의 지침과 함께 솔루션에 대 한 기계 학습 알고리즘을 선택할 때 다른 요구 사항을 염두에 두어야 합니다. 다음은 정확도, 학습 시간, 선형, 매개 변수 개수 및 기능 수와 같은 고려해 야 할 추가 요소입니다.

## <a name="comparison-of-machine-learning-algorithms"></a>기계 학습 알고리즘 비교

일부 학습 알고리즘에서는 데이터 구조 또는 원하는 결과에 대해 특수한 가정을 합니다. 사용자 요구에 적합한 것을 찾을 수 있는 경우 보다 유용한 결과, 보다 정확한 예측 또는 단축된 교육 시간을 제공할 수 있습니다.

다음 표에는 분류, 회귀 및 클러스터링 제품군에서 알고리즘의 가장 중요 한 특성이 요약 되어 있습니다.

| **알고리즘** | **높아집니다** | **학습 시간** | **선형성** | **매개 변수** | **참고 사항** |
| --- |:---:|:---:|:---:|:---:| --- |
| **분류 패밀리** | | | | | |
| [2 클래스 로지스틱 회귀](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/two-class-logistic-regression?WT.mc_id=docs-article-lazzeri) |좋음  |Fast |예 |4 | |
| [2 클래스 의사 결정 포리스트](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/two-class-decision-forest?WT.mc_id=docs-article-lazzeri) |최고 |보통 |아니요 |5 |더 느린 점수 매기기 시간을 표시 합니다. 누적 트리 예측의 디 어 지 잠금으로 인 한 더 느린 점수 매기기 시간 때문에 일대다 다중 클래스를 사용 하지 않는 것이 좋습니다. |
| [2 클래스 승격 된 의사 결정 트리](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/two-class-boosted-decision-tree?WT.mc_id=docs-article-lazzeri) |최고 |보통 |아니요 |6 |큰 메모리 공간 |
| [2 클래스 신경망](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/two-class-neural-network?WT.mc_id=docs-article-lazzeri) |좋음 |보통 |아니요 |8 | |
| [2 클래스 평균 퍼셉트론](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/two-class-averaged-perceptron?WT.mc_id=docs-article-lazzeri) |좋음 |보통 |예 |4 | |
| [2 클래스 지원 벡터 컴퓨터](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/two-class-support-vector-machine?WT.mc_id=docs-article-lazzeri) |좋음 |Fast |예 |5 |큰 기능 집합의 적합 |
| [다중 클래스 로지스틱 회귀](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/multiclass-logistic-regression?WT.mc_id=docs-article-lazzeri) |좋음 |Fast |예 |4 | |
| [다중 클래스 의사 결정 포리스트](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/multiclass-decision-forest?WT.mc_id=docs-article-lazzeri) |최고 |보통 |아니요 |5 |더 느린 점수 매기기 시간 표시 |
| [다중 클래스 승격 된 의사 결정 트리](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/multiclass-boosted-decision-tree?WT.mc_id=docs-article-lazzeri) |최고 |보통 |아니요 |6 | 적용 범위가 적은 몇 가지 위험으로 정확도를 향상 시키는 경향이 있습니다. |
| [다중 클래스 신경망](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/multiclass-neural-network?WT.mc_id=docs-article-lazzeri) |좋음 |보통 |아니요 |8 | |
| [One-vs-all 다중 클래스](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/one-vs-all-multiclass?WT.mc_id=docs-article-lazzeri) | - | - | - | - |선택된 2클래스 메서드의 속성을 참조하세요. |
| **회귀 제품군** | | | | | |
| [선형 회귀](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/linear-regression?WT.mc_id=docs-article-lazzeri) |좋음 |Fast |예 |4 | |
| [의사 결정 포리스트 회귀](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/decision-forest-regression?WT.mc_id=docs-article-lazzeri)|최고 |보통 |아니요 |5 | |
| [승격 된 의사 결정 트리 회귀](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/boosted-decision-tree-regression?WT.mc_id=docs-article-lazzeri) |최고 |보통 |아니요 |6 |큰 메모리 공간 |
| [신경망 회귀](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/neural-network-regression?WT.mc_id=docs-article-lazzeri) |좋음 |보통 |아니요 |8 | |
| **클러스터링 제품군** | | | | | |
| [K-클러스터링을 의미 합니다.](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/k-means-clustering?WT.mc_id=docs-article-lazzeri) |최고 |보통 |예 |8 |클러스터링 알고리즘 |

## <a name="requirements-for-a-data-science-scenario"></a>데이터 과학 시나리오에 대 한 요구 사항

데이터를 사용 하 여 수행할 작업을 확인 한 후에는 솔루션에 대 한 추가 요구 사항을 확인 해야 합니다. 

다음 요구 사항에 대 한 선택 및 장단점을 결정 합니다.

- 정확도
- 학습 시간
- 선형성
- 매개 변수 수
- 기능 수

## <a name="accuracy"></a>정확도

기계 학습의 정확도는 전체 사례에 대 한 실제 결과의 비율로 모델의 효율성을 측정 합니다. Machine Learning 디자이너에서 [모델 평가 모듈](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/evaluate-model?WT.mc_id=docs-article-lazzeri) 은 산업 표준 평가 메트릭 집합을 계산 합니다. 이 모듈을 사용 하 여 학습 된 모델의 정확도를 측정할 수 있습니다.

최대한 정확한 답을 얻는 것은 항상 필요한 것은 아닙니다. 용도에 따라 근사치가 적절한 경우도 있습니다. 이 경우 더 근접 한 방법을 사용 하 여 처리 시간을 크게 줄일 수 있습니다. 또한 대략적인 방법은 자연스럽 게 과잉 맞춤을 방지 하는 경향이 있습니다.

모델 평가 모듈을 사용 하는 방법에는 세 가지가 있습니다.

- 모델을 평가 하기 위해 학습 데이터에 대 한 점수 생성
- 모델에 점수를 생성 하지만 이러한 점수를 예약 된 테스트 집합의 점수와 비교 합니다.
- 동일한 데이터 집합을 사용 하 여 서로 다른 두 개의 관련 모델에 대 한 점수를 비교 합니다.

기계 학습 모델의 정확도를 평가 하는 데 사용할 수 있는 메트릭 및 방법의 전체 목록은 [모델 평가 모듈](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/evaluate-model?WT.mc_id=docs-article-lazzeri)을 참조 하세요.

## <a name="training-time"></a>학습 시간

감독 학습에서 교육은 기록 데이터를 사용 하 여 오류를 최소화 하는 기계 학습 모델을 작성 하는 것을 의미 합니다. 모델을 학습하는 데 필요한 시간 또는 분은 알고리즘에 따라 크게 다릅니다. 학습 시간은 종종 정확도와 밀접 하 게 연관 되어 있습니다. 하나는 일반적으로 다른 항목과 함께 제공 됩니다. 

또한 일부 알고리즘은 다른 항목보다 데이터 요소 수에 보다 민감합니다. 특히 데이터 집합이 큰 경우 시간 제한이 있기 때문에 특정 알고리즘을 선택할 수 있습니다.

Machine Learning 디자이너에서 기계 학습 모델을 만들고 사용 하는 과정은 일반적으로 다음 세 단계로 진행 됩니다.

1.  특정 알고리즘 유형을 선택한 다음 매개 변수 또는 하이퍼 매개 변수를 정의 하 여 모델을 구성 합니다. 

2.  레이블이 지정 되 고 알고리즘과 호환 되는 데이터를 포함 하는 데이터 집합을 제공 합니다. 데이터와 모델을 모두 연결 하 여 [모델 학습 모듈을 학습](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/train-model?WT.mc_id=docs-article-lazzeri)합니다.

3.  학습을 완료 한 후 [점수 매기기 모듈](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/score-model?WT.mc_id=docs-article-lazzeri) 중 하나를 사용 하 여 학습 된 모델을 사용 하 여 새 데이터에 대 한 예측을 만듭니다.

## <a name="linearity"></a>선형성

통계 및 기계 학습의 선형은 데이터 집합의 변수와 상수 간에 선형 관계가 있음을 의미 합니다. 예를 들어 선형 분류 알고리즘은 클래스를 직선 (또는 더 높은 차원의 아날로그)으로 구분할 수 있다고 가정 합니다.

많은 기계 학습 알고리즘에서 선형성을 활용합니다. Azure Machine Learning 디자이너에는 다음이 포함 됩니다. 

- [다중 클래스 로지스틱 회귀](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/multiclass-logistic-regression?WT.mc_id=docs-article-lazzeri)
- [2 클래스 로지스틱 회귀](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/two-class-logistic-regression?WT.mc_id=docs-article-lazzeri)
- [지원 벡터 컴퓨터](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/two-class-support-vector-machine?WT.mc_id=docs-article-lazzeri)  

선형 회귀 알고리즘은 데이터가 직선을 따르는 경향이 있다고 가정합니다. 이러한 가정은 일부 문제에 대해서는 나쁘지 않지만 다른 경우에는 정확도가 떨어집니다. 단점에도 불구 하 고 선형 알고리즘은 첫 번째 전략으로 널리 사용 됩니다. 알고리즘 방식으로 간단하고 학습 시간이 빠른 경향이 있습니다.

![비선형 클래스 경계](./media/how-to-select-algorithms/nonlinear-class-boundary.png)

***비선형 클래스 경계***: *선형 분류 알고리즘을 이용 하면 정확도가 낮아집니다.*

![비선형 추세 반영 데이터](./media/how-to-select-algorithms/nonlinear-trend.png)

***비선형 추세를 포함***하는 데이터: *선형 회귀 방법을 사용 하면 필요한 것 보다 훨씬 큰 오류가 생성 됩니다.*

## <a name="number-of-parameters"></a>매개 변수 수

매개 변수는 데이터 과학자가 알고리즘을 설정할 때 전환하기 위한 노브입니다. 이는 오류 허용 여부, 반복 횟수 등 알고리즘의 동작에 영향을 주는 숫자 이거나 알고리즘이 동작 하는 방법의 변형 사이에 있는 옵션입니다. 알고리즘의 학습 시간 및 정확도는 적절 한 설정에만 영향을 받는 경우가 있습니다. 일반적으로 많은 수의 매개 변수를 포함 하는 알고리즘에서는 가장 많은 평가판 및 오류가 발생 하므로 좋은 조합을 찾을 수 있습니다.

또는 Machine Learning 디자이너에 [모델 하이퍼 매개 변수 조정 모듈이](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/tune-model-hyperparameters?WT.mc_id=docs-article-lazzeri) 있습니다 .이 모듈의 목표는 기계 학습 모델에 대 한 최적의 하이퍼 매개 변수를 결정 하는 것입니다. 모듈은 다양 한 설정 조합을 사용 하 여 여러 모델을 작성 하 고 테스트 합니다. 모든 모델에 대 한 메트릭을 비교 하 여 설정의 조합을 가져옵니다. 

이 방법은 매개 변수 공간을 확장 하는 좋은 방법 이지만 모델을 학습 하는 데 필요한 시간은 매개 변수 수를 사용 하 여 급격 하 게 늘어납니다. 장점은 일반적으로 매개 변수를 많이 포함할수록 알고리즘의 유연성이 향상된다는 것입니다. 매개 변수 설정의 올바른 조합을 찾을 수 있다면 매우 좋은 정확도를 얻을 수 있는 경우가 많습니다.

## <a name="number-of-features"></a>기능 수

기계 학습에서 기능은 분석 하려는 현상을 측정 하는 변수입니다. 특정 데이터 형식의 경우 데이터 요소에 비해 기능 수가 매우 클 수 있습니다. 보통 genetics 또는 텍스트 데이터가 그렇습니다. 

많은 기능을 통해 학습 시간을 unfeasibly 빠뜨릴 수 있습니다. [지원 벡터 컴퓨터](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/two-class-support-vector-machine?WT.mc_id=docs-article-lazzeri) 는 기능이 많은 시나리오에 특히 적합 합니다. 이러한 이유로 대부분의 응용 프로그램에서는 텍스트 및 이미지 분류에 대 한 정보 검색에서 사용 되었습니다. 지원 벡터 컴퓨터는 분류 및 회귀 작업 모두에 사용할 수 있습니다.

지정 된 출력의 경우 기능 선택은 입력에 통계 테스트를 적용 하는 프로세스를 의미 합니다. 목표는 출력의 예측 가능성이 더 높은 열을 결정 하는 것입니다. Machine Learning 디자이너의 [필터 기반 기능 선택 모듈](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/filter-based-feature-selection?WT.mc_id=docs-article-lazzeri) 은 선택할 수 있는 여러 기능 선택 알고리즘을 제공 합니다. 모듈에는 피어슨 상관 관계 및 카이 제곱 값과 같은 상관 관계 메서드가 포함 되어 있습니다.

또한 [순열 기능 중요도 모듈](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/permutation-feature-importance?WT.mc_id=docs-article-lazzeri) 을 사용 하 여 데이터 집합에 대 한 기능 중요도 점수 집합을 계산할 수 있습니다. 그런 다음 이러한 점수를 활용 하 여 모델에서 사용 하기에 가장 적합 한 기능을 결정 하는 데 도움을 줍니다.

## <a name="next-steps"></a>다음 단계

 - [Azure Machine Learning 디자이너에 대 한 자세한 정보](https://docs.microsoft.com/azure/machine-learning/service/concept-designer?WT.mc_id=docs-article-lazzeri)
 - Azure Machine Learning 디자이너에서 사용할 수 있는 모든 기계 학습 알고리즘에 대 한 설명은 [디자이너 알고리즘 및 모듈 참조 Machine Learning](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/module-reference?WT.mc_id=docs-article-lazzeri) 를 참조 하세요.
 - 심층 학습, 기계 학습 및 AI 간의 관계를 탐색 하려면 [심층 학습 및 Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/concept-deep-learning-vs-machine-learning?WT.mc_id=docs-article-lazzeri) 을 참조 하세요.
