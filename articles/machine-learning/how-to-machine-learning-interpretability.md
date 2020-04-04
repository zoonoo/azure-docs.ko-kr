---
title: Azure 기계 학습에서 해석 가능성 모델링
titleSuffix: Azure Machine Learning
description: 모델이 Azure 기계 학습 SDK를 사용하여 예측을 만드는 이유를 설명하는 방법을 알아봅니다. 모델이 예측을 만드는 방법을 이해하기 위해 교육 및 추론 중에 사용할 수 있습니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.reviewer: Luis.Quintanilla
ms.date: 04/02/2020
ms.openlocfilehash: fcb837af85a54102e8c9eafc33249af9dba6b5ce
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631411"
---
# <a name="model-interpretability-in-azure-machine-learning"></a>Azure 기계 학습에서 해석 가능성 모델링
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

## <a name="overview-of-model-interpretability"></a>모델 해석 가능성 개요

해석성은 데이터 과학자, 감사자 및 비즈니스 의사 결정권자가 회사 정책, 산업 표준 및 정부 규정을 준수하는 데 매우 중요합니다.

+ 데이터 과학자는 경영진과 이해 관계자에게 모델을 설명할 수 있어야 하므로 연구 결과의 가치와 정확성을 이해할 수 있습니다. 또한 모델을 디버깅하고 모델을 개선하는 방법에 대한 정보에 입각한 결정을 내리기 위해 해석이 필요합니다. 

+ 법률 감사원은 규정 준수와 관련하여 모델의 유효성을 검사하고 모델의 결정이 인간에게 미치는 영향을 모니터링하는 도구가 필요합니다. 

+ 비즈니스 의사 결정자는 최종 사용자에게 투명성을 제공할 수 있는 기능을 갖추어 안심할 수 있어야 합니다. 이를 통해 신뢰를 얻고 유지할 수 있습니다.


기계 학습 모델을 설명하는 기능을 활성화하는 것은 모델 개발의 두 가지 주요 단계에서 중요합니다.
+ 교육 단계에서 모델 디자이너와 평가자는 모델의 해석 가능성 출력을 사용하여 가설을 확인하고 이해 관계자와의 신뢰를 구축할 수 있습니다. 또한 모델에 대한 인사이트를 사용하여 디버깅하고, 목표와 일치하는 모델 동작의 유효성을 검사하고, 모델 부당성 또는 중요하지 않은 기능을 확인합니다.

+ 추론 단계에서 배포된 모델에 대한 투명성을 갖추면 경영진은 모델이 작동하는 방식과 실제 환경에서 의사 결정이 사람들을 치료하고 영향을 미치는 방식을 "배포할 때"를 이해할 수 있습니다. 

## <a name="interpretability-with-azure-machine-learning"></a>Azure 기계 학습을 사용 하 고 해석

해석 성 클래스는 여러 SDK 패키지를 통해 사용할 수 있습니다: [(Azure 기계 학습에 대 한 SDK 패키지를 설치](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)하는 방법 알아보기)

* `azureml.interpret`, Microsoft에서 지원하는 기능을 포함하는 기본 패키지입니다.

* `azureml.contrib.interpret`.

* `azureml.train.automl.automlexplainer`자동화된 기계 학습 모델을 해석하기 위한 패키지입니다.

AutoML을 `pip install azureml-interpret` `pip install azureml-interpret-contrib` `pip install azureml-interpret-contrib` 사용하여 해석 가능 패키지를 가져옵니다.


> [!IMPORTANT]
> 네임스페이스의 `contrib` 콘텐츠는 완전히 지원되지 않습니다. 실험 기능이 성숙해지면 점차 주 네임스페이스로 이동하게 됩니다.
.



## <a name="how-to-interpret-your-model"></a>모델을 해석하는 방법

SDK의 클래스 및 메서드를 사용하여 다음을 수행할 수 있습니다.
+ 전체 모델 및/또는 개별 데이터 포인트에 대한 피쳐 중요도 값을 생성하여 모델 예측을 설명합니다. 
+ 교육 및 추론 중에 대규모로 실제 데이터 집합에서 모델 해석성을 달성합니다.
+ 대화형 시각화 대시보드를 사용하여 교육 시 데이터 패턴 및 설명 검색


기계 학습에서 **기능은** 대상 데이터 포인트를 예측하는 데 사용되는 데이터 필드입니다. 예를 들어 신용 위험을 예측하기 위해 연령, 계정 크기 및 계정 연령에 대한 데이터 필드가 사용될 수 있습니다. 이 경우 연령, 계정 크기 및 계정 연령이 **기능입니다.** 기능 중요도는 각 데이터 필드가 모델의 예측에 미치는 영향을 알려줍니다. 예를 들어, 계정 크기와 연령이 예측 값에 크게 영향을 미치지 않는 동안 연령은 예측에 많이 사용될 수 있습니다. 이 프로세스를 통해 데이터 과학자는 결과 예측을 설명할 수 있으므로 이해 관계자가 모델에서 가장 중요한 기능을 파악할 수 있습니다.

여기에서 지원되는 해석 기술, 지원되는 기계 학습 모델 및 지원되는 실행 환경에 대해 알아보십시오.


## <a name="supported-interpretability-techniques"></a>지원되는 해석 기술

 `azureml-interpret`해석 가능한 모델을 교육하고 블랙박스 AI 시스템을 설명하는 데 도움이 되는 오픈 소스 파이썬 패키지인 [Interpret-Community에서](https://github.com/interpretml/interpret-community/)개발된 해석 기술을 사용합니다. [Interpret-Community는](https://github.com/interpretml/interpret-community/) 이 SDK의 지원되는 설명자의 호스트 역할을 하며 현재 다음과 같은 해석 기술을 지원합니다.

|해석 기술|설명|Type|
|--|--|--------------------|
|1. SHAP 트리 설명자| [다항식](https://github.com/slundberg/shap)시간에 초점을 맞춘 SHAP의 트리 설명기는 **나무와 나무의 앙상블에**특정 한 형급식 빠른 SHAP 값 추정 알고리즘에 초점을 맞추고 있습니다.|모델별|
|2. SHAP 깊은 설명자| [SHAP](https://github.com/slundberg/shap), Deep Explainer의 설명에 기초하여 ["SHAP NIPS 백서에](https://papers.nips.cc/paper/7062-a-unified-approach-to-interpreting-model-predictions)설명된 DeepLIFT와의 연결을 기반으로 하는 딥 러닝 모델의 SHAP 값에 대한 고속 근사치 알고리즘입니다. **텐서플로우** 모델과 텐서플로우 백엔드를 사용하는 **케라스** 모델이 지원됩니다(PyTorch에 대한 예비 지원도 있음).|모델별|
|3. SHAP 선형 설명자| [SHAP의](https://github.com/slundberg/shap)선형 설명자는 **선형 모델에**대한 SHAP 값을 계산하며, 선택적으로 피처 간 상관 관계를 고려합니다.|모델별|
|4. SHAP 커널 해설자| [SHAP의](https://github.com/slundberg/shap)커널 설명자는 특별히 가중치가 가중된 로컬 선형 회귀를 사용하여 **모든 모델에**대한 SHAP 값을 추정합니다.|모델 독립적|
|5. 모방 설명자 (글로벌 대리)| Mimic 설명자는 블랙박스 모델을 모방하기 위해 [글로벌 대리 모델을](https://christophm.github.io/interpretable-ml-book/global.html) 교육하는 아이디어를 기반으로 합니다. 전역 서로게이트 모델은 모든 **블랙박스 모델의** 예측을 가능한 한 정확하게 근사화하도록 훈련된 본질적으로 해석 가능한 모델입니다. 데이터 과학자는 서로게이트 모델을 해석하여 블랙박스 모델에 대한 결론을 도출할 수 있습니다. 라이트GBM(LGBM설명 가능 모델), 선형 회귀(선형 설명 가능한 모델), 스토크 그라데이션 하강 설명 모델(SGD설명 가능한 모델) 및 의사 결정 트리(DecisionTree설명 가능한 모델)의 대리 모델로 다음 해석 가능한 모델 중 하나를 사용할 수 있습니다.|모델 독립적|
|6. 순열 기능 중요도 설명자(PFI)| 순열 특징 중요도는 [Breiman의 랜덤 포리스트 용지에서](https://www.stat.berkeley.edu/~breiman/randomforest2001.pdf) 영감을 받은 분류 및 회귀 모델을 설명하는 데 사용되는 기술입니다(섹션 10 참조). 높은 수준에서 작동 방식은 전체 데이터 집합에 대해 한 번에 하나의 기능을 임의로 섞고 관심 있는 성능 메트릭이 얼마나 변경되는지 계산하는 것입니다. 변화가 클수록 해당 기능이 중요한 것입니다. PFI는 **기본 모델의** 전반적인 동작을 설명할 수 있지만 개별 예측을 설명하지는 않습니다. |모델 독립적|




위에서 설명한 해석 기술 외에도 다른 [SHAP 기반 설명자인](https://github.com/slundberg/shap)을 `TabularExplainer`지원합니다. 모델에 따라 지원되는 SHAP 설명자 중 하나를 `TabularExplainer` 사용합니다.

* 모든 트리 기반 모델에 대한 트리 설명자
* DNN 모델에 대한 심층 설명자
* 선형 모델에 대한 선형 설명자
* 다른 모든 모델에 대한 커널 설명자

`TabularExplainer`또한 직접 SHAP 설명자에 비해 상당한 기능 및 성능 향상을 이루었습니다.

* **초기화 데이터 집합의 요약입니다.** 설명 속도가 가장 중요한 경우 초기화 데이터 집합을 요약하고 전체 및 개별 기능 중요도 값의 생성 속도를 높이는 소수의 대표 샘플 집합을 생성합니다.
* **평가 데이터 집합을 샘플링합니다.** 사용자가 대규모 평가 샘플 집합을 통과하지만 실제로 모든 샘플을 평가할 필요가 없는 경우 샘플링 매개 변수를 true로 설정하여 전체 모델 설명 계산 속도를 높일 수 있습니다.

다음 다이어그램은 지원되는 설명자의 현재 구조를 보여 주며 있습니다.

[![기계 학습 해석 아키텍처](./media/how-to-machine-learning-interpretability/interpretability-architecture.png)](./media/how-to-machine-learning-interpretability/interpretability-architecture.png#lightbox)


## <a name="supported-machine-learning-models"></a>지원되는 기계 학습 모델

SDK 패키지는 `azureml.interpret` 다음과 같은 데이터 집합 형식으로 학습된 모델을 지원합니다.
- `numpy.array`
- `pandas.DataFrame`
- `iml.datatypes.DenseData`
- `scipy.sparse.csr_matrix`

설명 함수는 모델과 파이프라인을 모두 입력으로 허용합니다. 모델이 제공된 경우 모델은 예측 함수를 `predict` 구현하거나 `predict_proba` Scikit 규칙을 준수하는 것을 구현해야 합니다. 모델이 이를 지원하지 않는 경우 `predict` Scikit또는 `predict_proba` Scikit에서 와 동일한 결과를 생성하는 함수로 모델을 래핑하고 선택한 설명자와 함께 해당 래퍼 함수를 사용할 수 있습니다. 파이프라인이 제공되면 설명 함수는 실행 중인 파이프라인 스크립트가 예측을 반환한다고 가정합니다. 이 래핑 `azureml.interpret` 기술을 사용하여 PyTorch, TensorFlow 및 Keras 딥 러닝 프레임워크와 클래식 기계 학습 모델을 통해 학습된 모델을 지원할 수 있습니다.

## <a name="local-and-remote-compute-target"></a>로컬 및 원격 컴퓨팅 대상

이 `azureml.interpret` 패키지는 로컬 및 원격 컴퓨팅 대상모두에서 작동하도록 설계되었습니다. 로컬로 실행되는 경우 SDK 함수는 Azure 서비스에 문의하지 않습니다. 

Azure 기계 학습 계산에서 설명을 원격으로 실행하고 설명 정보를 Azure 기계 학습 실행 기록 서비스에 기록할 수 있습니다. 이 정보가 기록되면 사용자 분석을 위해 Azure Machine Learning 스튜디오에서 설명의 보고서 및 시각화를 쉽게 사용할 수 있습니다.


## <a name="next-steps"></a>다음 단계

Azure Machine Learning 원격 계산 리소스에서 로컬 및 모두에서 학습하는 모델에 대한 해석 가능성을 설정하는 [방법을](how-to-machine-learning-interpretability-aml.md) 참조하세요. 추가 시나리오는 [샘플 전자 필기장을](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model) 참조하십시오.
