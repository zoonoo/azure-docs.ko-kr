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
ms.reviewer: trbye
ms.date: 10/25/2019
ms.openlocfilehash: b68d2a72dc18f683f2203429908a536db1b5124a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063995"
---
# <a name="model-interpretability-in-azure-machine-learning"></a>Azure 기계 학습에서 해석 가능성 모델링
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

## <a name="overview-of-model-interpretability"></a>모델 해석 가능성 개요

해석성은 데이터 과학자와 비즈니스 의사 결정권자 모두에게 회사 정책, 산업 표준 및 정부 규정을 준수하는 데 매우 중요합니다.
+ 데이터 과학자는 경영진과 이해 관계자에게 모델을 설명할 수 있어야 하므로 연구 결과의 가치와 정확성을 이해할 수 있습니다. 
+ 비즈니스 의사 결정자는 최종 사용자가 신뢰를 얻고 유지할 수 있도록 투명성을 제공할 수 있는 능력에 대한 안심이 필요합니다.

기계 학습 모델을 설명하는 기능을 활성화하는 것은 모델 개발의 두 가지 주요 단계에서 중요합니다.
+ 기계 학습 모델 개발 주기의 교육 단계 동안. 모델 디자이너와 평가자는 모델의 해석 가능성 출력을 사용하여 가설을 확인하고 이해 관계자와의 신뢰를 구축할 수 있습니다. 또한 모델에 대한 인사이트를 사용하여 디버깅하고, 목표와 일치하는 모델 동작의 유효성을 검사하고, 편향또는 중요하지 않은 기능을 확인합니다.
+ 추론 단계에서 배포된 모델에 대한 투명성을 갖추면 경영진은 모델이 작동하는 방식과 실제 환경에서 의사 결정이 사람들을 치료하고 영향을 미치는 방식을 "배포할 때"를 이해할 수 있습니다. 

## <a name="interpretability-with-azure-machine-learning"></a>Azure 기계 학습을 사용 하 고 해석

이 문서에서는 모델 해석 개념이 SDK에서 구현되는 방법을 알아봅니다.

SDK의 클래스 와 메서드를 사용하여 다음을 얻을 수 있습니다.
+ 원시 피쳐와 엔지니어링된 피쳐 모두에 대한 기능 중요도 값
+ 교육 및 추론 중에 대규모로 실제 데이터 집합에 대한 해석 가능성.
+ 대화형 시각화를 통해 교육 시 데이터 패턴 및 설명 검색에 도움이 됩니다.


기계 학습에서 **기능은** 대상 데이터 포인트를 예측하는 데 사용되는 데이터 필드입니다. 예를 들어 신용 위험을 예측하기 위해 연령, 계정 크기 및 계정 연령에 대한 데이터 필드가 사용될 수 있습니다. 이 경우 연령, 계정 크기 및 계정 연령이 **기능입니다.** 기능 중요도는 각 데이터 필드가 모델의 예측에 미치는 영향을 알려줍니다. 예를 들어, 계정 크기와 나이가 예측 정확도에 크게 영향을 미치지 않는 동안 나이는 예측에 많이 사용될 수 있습니다. 이 프로세스를 통해 데이터 과학자는 결과 예측을 설명할 수 있으므로 이해 관계자가 모델에서 가장 중요한 데이터 포인트를 파악할 수 있습니다.

이러한 도구를 사용하여 모든 데이터에 **대해 전 세계적으로**기계 학습 모델을 설명하거나 사용하기 쉽고 확장 가능한 방식으로 최첨단 기술을 사용하여 **특정 데이터 포인트에서 로컬로** 기계 학습 모델을 설명할 수 있습니다.

해석 성 클래스는 여러 SDK 패키지를 통해 사용할 수 있습니다. [Azure 기계 학습에 대한 SDK 패키지를 설치하는](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)방법에 대해 알아봅니다.

* `azureml.interpret`, Microsoft에서 지원하는 기능을 포함하는 기본 패키지입니다.

* `azureml.contrib.interpret`.

* `azureml.train.automl.automlexplainer`자동화된 기계 학습 모델을 해석하기 위한 패키지입니다.

> [!IMPORTANT]
> 네임스페이스의 `contrib` 콘텐츠는 완전히 지원되지 않습니다. 실험 기능이 성숙해지면 점차 주 네임스페이스로 이동하게 됩니다.

## <a name="how-to-interpret-your-model"></a>모델을 해석하는 방법

해석 가능성 클래스 및 메서드를 적용하여 모델의 전역 동작 또는 특정 예측을 이해할 수 있습니다. 전자는 글로벌 설명이라고하며 후자는 로컬 설명이라고합니다.

메서드는 모델 불가지론자인지 또는 모델별인지에 따라 분류할 수도 있습니다. 일부 메서드는 특정 유형의 모델을 대상으로 합니다. 예를 들어 SHAP의 트리 설명자는 트리 기반 모델에만 적용됩니다. 일부 메서드는 모델을 모방 설명자 또는 SHAP의 커널 설명과 같은 블랙 박스로 처리합니다. 패키지는 `interpret` 데이터 집합, 모델 유형 및 사용 사례를 기반으로 이러한 다양한 접근 방식을 활용합니다.

출력은 지정된 모델이 예측을 만드는 방법에 대한 정보 집합입니다.
* 전역/로컬 상대 피쳐 중요도
* 글로벌/로컬 기능 및 예측 관계

### <a name="explainers"></a>설명자

이 패키지는 해석 가능한 모델을 교육하고 블랙박스 AI 시스템을 설명하는 데 도움이 되는 오픈 소스 파이썬 패키지인 [Interpret-Community에서](https://github.com/interpretml/interpret-community/)개발된 해석 기술을 사용합니다. [Interpret-Community는](https://github.com/interpretml/interpret-community/) 이 SDK의 지원되는 설명자의 호스트 역할을 하며 현재 다음과 같은 해석 기술을 지원합니다.

* **SHAP 트리 설명자**: 나무와 나무의 앙상블에 특정 다항식 시간 빠른 SHAP 값 추정 알고리즘에 초점을 맞추고 [SHAP의](https://github.com/slundberg/shap)나무 설명기.
* **SHAP 깊은 설명자**: [SHAP의](https://github.com/slundberg/shap)설명에 따라 , 깊은 설명자는 ["SHAP NIPS 논문에](https://papers.nips.cc/paper/7062-a-unified-approach-to-interpreting-model-predictions)설명 된 DeepLIFT와의 연결을 기반으로 딥 러닝 모델의 SHAP 값에 대한 고속 근사 알고리즘입니다. 텐서플로우 모델과 텐서플로우 백엔드를 사용하는 케라스 모델이 지원됩니다(PyTorch에 대한 예비 지원도 있음).
* **SHAP 선형 설명자**: [SHAP의](https://github.com/slundberg/shap)선형 설명자는 선형 모델에 대한 SHAP 값을 계산하며, 선택적으로 피처 간 상관 관계를 고려합니다.

* **SHAP 커널 설명자**: [SHAP의](https://github.com/slundberg/shap)커널 설명자는 모든 모델에 대해 SHAP 값을 추정하기 위해 특별히 가중치가 가중된 로컬 선형 회귀를 사용합니다.
* **모방 설명자**: 모방 설명자는 블랙 박스 모델을 모방하기 위해 [글로벌 대리 모델을](https://christophm.github.io/interpretable-ml-book/global.html) 교육의 아이디어를 기반으로합니다. 전역 서로게이트 모델은 블랙박스 모델의 예측을 가능한 한 정확하게 근사화하도록 훈련된 본질적으로 해석 가능한 모델입니다. 데이터 과학자는 서로게이트 모델을 해석하여 블랙박스 모델에 대한 결론을 도출할 수 있습니다. 라이트GBM(LGBM설명가능모델), 선형회귀(선형설명모델), [SGD설명가능한모델] 및 결정트리( 의사 결정트리 설명 모델).


* **순열 특징 중요도 설명자**: 순열 특징 중요도는 [Breiman의 임의 포리스트 용지에서](https://www.stat.berkeley.edu/~breiman/randomforest2001.pdf) 영감을 받은 분류 및 회귀 모델을 설명하는 데 사용되는 기술입니다(섹션 10 참조). 높은 수준에서 작동 방식은 전체 데이터 집합에 대해 한 번에 하나의 기능을 임의로 섞고 관심 있는 성능 메트릭이 얼마나 변경되는지 계산하는 것입니다. 변화가 클수록 해당 기능이 중요한 것입니다.

* **라임 설명자** (`contrib`) : [라임](https://github.com/marcotcr/lime)에 기초하여, 라임 설명자는 로컬 대리 모델을 만들기 위해 최첨단 로컬 해석 모델 불가지론 설명 (LIME) 알고리즘을 사용합니다. 글로벌 대리 모델과 달리 LIME은 개별 예측을 설명하기 위해 로컬 대리 모델을 교육하는 데 중점을 둡니다.
* **HAN 텍스트 설명자** ()`contrib`: HAN 텍스트 설명자는 주어진 블랙 박스 텍스트 모델에 대한 텍스트 데이터에서 모델 설명을 얻기 위해 계층적 주의 네트워크를 사용합니다. 주어진 블랙박스 모델의 예측 출력에 대해 HAN 대리 모델을 훈련시훈련합니다. 텍스트 모음에서 전 세계적으로 교육한 후 설명의 정확성을 향상시키기 위해 특정 문서에 대한 세부 조정 단계를 추가합니다. HAN은 문장과 단어주의를 위해 두 개의 주의 층이있는 양방향 RNN을 사용합니다. DNN이 블랙박스 모델에 대해 학습되고 특정 문서에서 미세 조정되면 사용자는 주의 계층에서 중요도라는 단어를 추출할 수 있습니다. HAN은 텍스트 데이터에 대한 LIME 또는 SHAP보다 더 정확하지만 교육 시간 측면에서도 비용이 많이 드는 것으로 나타났습니다. 사용자에게 GloVe 단어 포함을 사용하여 네트워크를 초기화하여 교육 시간을 줄일 수 있는 옵션이 개선되었습니다. 원격 Azure GPU VM에서 HAN을 실행하여 교육 시간을 크게 향상시킬 수 있습니다. HAN의 구현은 ['문서 분류를 위한 계층적 주의 네트워크(Yang et al., 2016)'에](https://www.researchgate.net/publication/305334401_Hierarchical_Attention_Networks_for_Document_Classification)설명되어 있습니다.


* **표 식 설명자**: `TabularExplainer` 직접 [SHAP](https://github.com/slundberg/shap) 설명자를 호출하기 위해 다음 논리를 사용합니다.

    1. 트리 기반 모델인 경우 SHAP `TreeExplainer`를 적용합니다.
    2. DNN 모델인 경우 SHAP를 `DeepExplainer`적용합니다.
    3. 선형 모델인 경우 SHAP `LinearExplainer`를 적용합니다.
    3. 블랙 박스 모델로 취급하고 SHAP를 적용`KernelExplainer`


`TabularExplainer`또한 직접 SHAP 설명자에 비해 상당한 기능 및 성능 향상을 이루었습니다.

* **초기화 데이터 집합의 요약입니다.** 설명 속도가 가장 중요한 경우 초기화 데이터 집합을 요약하고 전역 및 로컬 설명의 속도를 높이는 소수의 대표 샘플 집합을 생성합니다.
* **평가 데이터 집합을 샘플링합니다.** 사용자가 대규모 평가 샘플 집합을 전달하지만 실제로 모든 샘플을 평가할 필요가 없는 경우 샘플링 매개 변수를 true로 설정하여 전역 설명 속도를 높일 수 있습니다.

다음 다이어그램은 직접 및 메타 설명자의 현재 구조를 보여 주며 있습니다.

[![기계 학습 해석 아키텍처](./media/how-to-machine-learning-interpretability/interpretability-architecture.png)](./media/how-to-machine-learning-interpretability/interpretability-architecture.png#lightbox)


### <a name="models-supported"></a>지원되는 모델

Python `numpy.array` `pandas.DataFrame`, `iml.datatypes.DenseData`또는 `scipy.sparse.csr_matrix` 형식의 데이터 집합에 대해 학습된 모든 `explain` 모델은 SDK의 해석 가능성 패키지에서 지원됩니다.

설명 함수는 모델과 파이프라인을 모두 입력으로 허용합니다. 모델이 제공된 경우 모델은 예측 함수를 `predict` 구현하거나 `predict_proba` Scikit 규칙을 준수하는 것을 구현해야 합니다. 파이프라인(파이프라인 스크립트 이름)이 제공된 경우 설명 함수는 실행 중인 파이프라인 스크립트가 예측을 반환한다고 가정합니다. PyTorch, TensorFlow 및 Keras 딥 러닝 프레임워크를 통해 학습된 모델을 지원합니다.

### <a name="local-and-remote-compute-target"></a>로컬 및 원격 컴퓨팅 대상

이 `explain` 패키지는 로컬 및 원격 컴퓨팅 대상모두에서 작동하도록 설계되었습니다. 로컬로 실행되는 경우 SDK 함수는 Azure 서비스에 문의하지 않습니다. Azure 기계 학습 계산에서 원격으로 설명을 실행하고 설명 정보를 Azure 기계 학습 실행 기록 서비스에 기록할 수 있습니다. 이 정보가 기록되면 사용자 분석을 위해 Azure Machine Learning 작업 영역에서 설명의 보고서 및 시각화를 쉽게 사용할 수 있습니다.


## <a name="next-steps"></a>다음 단계

Azure Machine Learning 원격 계산 리소스에서 로컬 및 모두에서 학습하는 모델에 대한 해석 가능성을 설정하는 [방법을](how-to-machine-learning-interpretability-aml.md) 참조하세요. 추가 시나리오는 [샘플 전자 필기장을](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model) 참조하십시오.
