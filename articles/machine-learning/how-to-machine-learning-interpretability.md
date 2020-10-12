---
title: Azure Machine Learning 모델 interpretability (미리 보기)
titleSuffix: Azure Machine Learning
description: Azure Machine Learning SDK를 사용 하 여 모델이 예측을 수행 하는 이유를 설명 하는 방법을 알아봅니다. 학습 및 유추 중에 모델에서 예측을 만드는 방법을 이해 하는 데 사용할 수 있습니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: mithigpe
author: minthigpen
ms.reviewer: Luis.Quintanilla
ms.date: 07/09/2020
ms.openlocfilehash: d063af3ba3b9261100af5e48a2c507a80ac76d98
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91322362"
---
# <a name="model-interpretability-in-azure-machine-learning-preview"></a>Azure Machine Learning 모델 interpretability (미리 보기)


## <a name="overview-of-model-interpretability"></a>모델 interpretability 개요

Interpretability는 회사 정책, 산업 표준 및 정부 규정 준수를 보장 하기 위해 데이터 과학자, 감사자 및 비즈니스 의사 결정자에 게 매우 중요 합니다.

+ 데이터 과학자에는 임원 및 관련자에 게 모델을 설명 하는 기능이 필요 하므로 결과의 가치와 정확도를 이해할 수 있습니다. 또한 모델을 디버그 하 고이를 개선 하는 방법에 대해 합리적인 결정을 내리는 데 interpretability 필요 합니다. 

+ 법적 감사자는 규정 준수와 관련 하 여 모델의 유효성을 검사 하 고 모델의 결정이 어떻게 영향을 주는지 모니터링 하는 도구를 필요로 합니다. 

+ 비즈니스 의사 결정권자는 최종 사용자에 게 투명도를 제공 하는 기능을 제공 하 여 안심 하 고 있어야 합니다. 이렇게 하면 트러스트를 획득 하 고 유지 관리할 수 있습니다.


모델 개발의 두 가지 주요 단계에서는 machine learning 모델을 설명 하는 기능을 사용 하는 것이 중요 합니다.
+ 학습 단계 중에 모델 디자이너와 계산기는 모델의 interpretability 출력을 사용 하 여 관련자와의 가설 및 빌드 트러스트를 확인할 수 있습니다. 또한 디버깅을 위해 모델에 대 한 통찰력을 사용 하 고, 모델 동작의 유효성을 검사 하 여 목표와 일치 하는지 확인 하 고, 모델의 공평 하거나 중요 하지 않은 기능을 확인 합니다

+ 추론 단계를 수행 하는 동안 배포 된 모델에 대 한 투명도를 사용 하면 경영진은 모델의 작동 방식 및 의사 결정을 어떻게 처리 하 고 사용자에 게 실제적인 영향을 줄 수 있습니다. 

## <a name="interpretability-with-azure-machine-learning"></a>Azure Machine Learning Interpretability

Interpretability 클래스는 다음 SDK 패키지를 통해 제공 됩니다. ( [Azure Machine Learning에 대 한 sdk 패키지를 설치](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true)하는 방법 알아보기)

* `azureml.interpret`에는 Microsoft에서 지 원하는 기능이 포함 되어 있습니다.

`pip install azureml-interpret`일반적인 용도에 사용 합니다.

## <a name="how-to-interpret-your-model"></a>모델을 해석 하는 방법

SDK에서 클래스 및 메서드를 사용 하 여 다음을 수행할 수 있습니다.
+ 전체 모델 및/또는 개별 datapoints에 대 한 기능 중요도 값을 생성 하 여 모델 예측을 설명 합니다. 
+ 학습 및 유추 중에 규모에 따라 실제 데이터 집합에서 모델 interpretability를 실현 합니다.
+ 대화형 시각화 대시보드를 사용 하 여 학습 시간에 데이터 및 설명의 패턴 검색


기계 학습에서 **기능은** 대상 데이터 요소를 예측 하는 데 사용 되는 데이터 필드입니다. 예를 들어 신용 위험을 예측 하려면 나이, 계정 크기 및 계정 사용 기간에 대 한 데이터 필드를 사용할 수 있습니다. 이 경우에는 나이, 계정 크기 및 계정 사용 기간이 **기능**입니다. 기능 중요도는 각 데이터 필드가 모델의 예측에 영향을 주는 방법을 알려 줍니다. 예를 들어 age는 예측에서 많이 사용 될 수 있으며, 계정 크기와 age는 예측 값에 크게 영향을 주지 않습니다. 이 프로세스를 통해 데이터 과학자는 결과 예측을 설명할 수 있으므로 이해 관계자가 모델에서 가장 중요 한 기능을 파악할 수 있습니다.

지원 되는 interpretability 기술, 지원 되는 기계 학습 모델 및 지원 되는 실행 환경에 대해 알아봅니다.


## <a name="supported-interpretability-techniques"></a>지원 되는 interpretability 기술

 `azureml-interpret` 에서는 해석 된 모델을 학습 하 고 블랙 박스 AI 시스템을 설명 하는 데 도움이 되는 오픈 소스 python 패키지인 [해석 커뮤니티](https://github.com/interpretml/interpret-community/)에서 개발한 interpretability 기술을 사용 합니다. [해석-커뮤니티](https://github.com/interpretml/interpret-community/) 는이 SDK의 지원 되는 explainers 호스트 역할을 하며 현재 다음 interpretability 기술을 지원 합니다.

|Interpretability 기술|설명|Type|
|--|--|--------------------|
|SHAP 트리 설명| [Shap](https://github.com/slundberg/shap)의 tree 설명는 트리의 트리와 관련 된 다항식 TIME FAST shap 값 예측 알고리즘을 **중심으로 합니다.**|모델 관련|
|SHAP 심층 설명| SHAP의 설명에 따라 Deep 설명 "는 [SHAP NIPS 용지](https://papers.nips.cc/paper/7062-a-unified-approach-to-interpreting-model-predictions)에 설명 된 DeepLIFT와의 연결을 기반으로 하는 심층 학습 모델의 shap 값에 대 한 고속 근사값 알고리즘입니다. TensorFlow 백 엔드를 사용 하는 **TensorFlow** 모델 및 **keras** 모델이 지원 됩니다 (PyTorch에 대 한 예비 지원도 있습니다.).|모델 관련|
|SHAP 선형 설명| SHAP의 선형 설명는 **선형 모델**에 대해 shap 값을 계산 하 고, 선택적으로 기능 간 상관 관계를 고려 합니다.|모델 관련|
|SHAP 커널 설명| SHAP의 커널 설명는 특별히 가중치가 적용 된 로컬 선형 회귀를 사용 하 여 **모든 모델**에 대 한 shap 값을 예측 합니다.|모델 독립적|
|설명 모방 (전역 서로게이트)| 설명 모방은 블랙 박스 모델을 모방 하기 위해 [전역 서로게이트 모델](https://christophm.github.io/interpretable-ml-book/global.html) 을 학습 하는 아이디어를 기반으로 합니다. 전역 서로게이트 모델은 **모든 블랙 박스 모델** 의 예측을 최대한 정확 하 게 예측 하도록 학습 된 본질적으로 해석 되는 모델입니다. 데이터 과학자은 서로게이트 모델을 해석 하 여 블랙 박스 모델에 대 한 결론을 그릴 수 있습니다. 다음 해석 가능 모델 중 하나를 서로게이트 모델 (LightGBM (LGBMExplainableModel), 선형 회귀 (LinearExplainableModel), 추계 explainable) 및 의사 결정 트리 (SGDExplainableModel)로 사용할 수 있습니다.|모델 독립적|
|순열 기능 중요도 설명 (PFI)| 순열 기능 중요도는 [Breiman의 임의 포리스트 용지](https://www.stat.berkeley.edu/~breiman/randomforest2001.pdf) (섹션 10 참조)에서 설명 하는 분류 및 회귀 모델을 설명 하는 데 사용 되는 기술입니다. 높은 수준에서 작동 방식은 전체 데이터 집합에 대해 한 번에 하나의 기능을 임의로 순서 섞기 하 고 관심 있는 성능 메트릭이 변경 되는 정도를 계산 하는 것입니다. 변화가 클수록 해당 기능이 중요한 것입니다. PFI는 **기본 모델** 의 전체 동작을 설명할 수 있지만 개별 예측에 대해서는 설명 하지 않습니다. |모델 독립적|




위에서 설명한 interpretability 기술 외에도 이라는 다른 SHAP 기반 설명을 지원 `TabularExplainer` 합니다. 모델에 따라은 `TabularExplainer` 지원 되는 SHAP explainers 중 하나를 사용 합니다.

* 모든 트리 기반 모델에 대 한 TreeExplainer
* DNN 모델에 대 한 DeepExplainer
* 선형 모델에 대 한 LinearExplainer
* 다른 모든 모델에 대 한 KernelExplainer

`TabularExplainer` 는 또한 직접 SHAP Explainers에서 중요 한 기능 및 성능 향상을 만들었습니다.

* **초기화 데이터 집합의 요약**입니다. 설명의 속도가 가장 중요 한 경우 초기화 데이터 집합을 요약 하 고 몇 가지 대표적인 샘플 집합을 생성 하 여 전체 및 개별 기능 중요도 값의 생성 속도를 향상 시킵니다.
* **평가 데이터 집합을 샘플링**합니다. 사용자가 많은 수의 평가 샘플을 통과 하지만 실제로 평가할 필요가 없는 경우에는 샘플링 매개 변수를 true로 설정 하 여 전체 모델 설명의 계산 속도를 높일 수 있습니다.

다음 다이어그램은 지원 되는 explainers의 현재 구조를 보여 줍니다.

[![Machine Learning Interpretability 아키텍처](./media/how-to-machine-learning-interpretability/interpretability-architecture.png)](./media/how-to-machine-learning-interpretability/interpretability-architecture.png#lightbox)


## <a name="supported-machine-learning-models"></a>지원 되는 기계 학습 모델

`azureml.interpret`SDK 패키지는 다음 데이터 집합 형식으로 학습 된 모델을 지원 합니다.
- `numpy.array`
- `pandas.DataFrame`
- `iml.datatypes.DenseData`
- `scipy.sparse.csr_matrix`

설명 함수는 모델과 파이프라인을 입력으로 받아들입니다. 모델을 제공 하는 경우 모델은 예측 함수를 구현 `predict` 하거나 `predict_proba` Scikit 규칙을 준수 해야 합니다. 모델에서이 기능을 지원 하지 않는 경우 Scikit와 동일한 결과를 생성 하는 함수로 모델을 래핑하고 `predict` `predict_proba` 선택한 설명 해당 래퍼 함수를 사용할 수 있습니다. 파이프라인이 제공 되는 경우 설명 함수는 실행 중인 파이프라인 스크립트가 예측을 반환 한다고 가정 합니다. 이 래핑 기법을 사용 하 여는 `azureml.interpret` PyTorch, TensorFlow 및 Keras (최고급 학습 프레임 워크 및 클래식 기계 학습 모델)를 통해 학습 된 모델을 지원할 수 있습니다.

## <a name="local-and-remote-compute-target"></a>로컬 및 원격 계산 대상

`azureml.interpret`패키지는 로컬 및 원격 계산 대상 모두에서 작동 하도록 설계 되었습니다. 로컬로 실행 하는 경우 SDK 함수는 Azure 서비스에 연결 되지 않습니다. 

Azure Machine Learning 계산에 대 한 설명을 원격으로 실행 하 고 설명 정보를 Azure Machine Learning 실행 기록 서비스에 기록할 수 있습니다. 이 정보가 기록 되 면 설명의 보고서 및 시각화를 사용자 분석을 위해 Azure Machine Learning studio에서 쉽게 사용할 수 있습니다.


## <a name="next-steps"></a>다음 단계

- 로컬 및 Azure Machine Learning 원격 계산 리소스 모두에 대 한 모델 학습에 대해 interpretability를 사용 하도록 설정 [하는 방법을](how-to-machine-learning-interpretability-aml.md) 참조 하세요. 
- 추가 시나리오는 [샘플 노트북](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model) 을 참조 하세요. 
- Interpretability for text 시나리오에 관심이 있는 경우 NLP에 대 한 interpretability 기술에 대 한 소개- [텍스트 해석](https://github.com/interpretml/interpret-text) [-커뮤니티에서 해석](https://github.com/interpretml/interpret-community/)하는 관련 오픈 소스 리포지토리를 참조 하세요. `azureml.interpret` 패키지는 현재 이러한 기술을 지원 하지 않지만 [텍스트 분류의 예제 전자 필기장](https://github.com/interpretml/interpret-text/blob/master/notebooks/text_classification/text_classification_classical_text_explainer.ipynb)을 사용 하 여 시작할 수 있습니다.
