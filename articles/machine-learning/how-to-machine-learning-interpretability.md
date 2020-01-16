---
title: Azure Machine Learning 모델 interpretability
titleSuffix: Azure Machine Learning
description: Azure Machine Learning SDK를 사용 하 여 모델이 예측을 수행 하는 이유를 설명 하는 방법을 알아봅니다. 학습 및 유추 중에 모델에서 예측을 만드는 방법을 이해 하는 데 사용할 수 있습니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.reviewer: trbye
ms.date: 10/25/2019
ms.openlocfilehash: 339ab811969a3de6ce87d529e1bf77f325be4071
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/15/2020
ms.locfileid: "75968498"
---
# <a name="model-interpretability-in-azure-machine-learning"></a>Azure Machine Learning 모델 interpretability
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

## <a name="overview-of-model-interpretability"></a>모델 interpretability 개요

Interpretability는 회사 정책, 산업 표준 및 정부 규정 준수를 보장 하기 위해 데이터 과학자 및 비즈니스 의사 결정권자와 동일 하 게 중요 합니다.
+ 데이터 과학자는 임원 및 관련자에 게 모델을 설명 하는 기능이 필요 하므로 결과의 가치와 정확도를 이해할 수 있습니다. 
+ 비즈니스 의사 결정권자는 최종 사용자에 게 투명성을 제공 하 여 신뢰를 확보 하 고 유지 관리 하는 기능에 대 한 안심할 필요가 있습니다.

모델 개발의 두 가지 주요 단계에서는 machine learning 모델을 설명 하는 기능을 사용 하는 것이 중요 합니다.
+ Machine learning 모델 개발 주기의 학습 단계를 진행 합니다. 모델 디자이너 및 계산기는 모델의 interpretability 출력을 사용 하 여 관련자와의 가설 및 빌드 트러스트를 확인할 수 있습니다. 또한 디버깅을 위해 모델에 대 한 통찰력을 사용 하 고, 모델 동작의 유효성을 검사 하 여 목표와 일치 하는지 확인 하 고, 바이어스 또는 무효 기능을 확인 합니다.
+ 추론 단계를 수행 하는 동안 배포 된 모델에 대 한 투명도를 사용 하면 경영진은 모델의 작동 방식 및 의사 결정을 어떻게 처리 하 고 사용자에 게 실제적인 영향을 줄 수 있습니다. 

## <a name="interpretability-with-azure-machine-learning"></a>Azure Machine Learning Interpretability

이 문서에서는 SDK에서 모델 interpretability 개념을 구현 하는 방법에 대해 알아봅니다.

SDK의 클래스 및 메서드를 사용 하 여 다음을 얻을 수 있습니다.
+ 원시 기능과 엔지니어링 된 기능 모두에 대 한 기능 중요도 값
+ 학습 및 유추 중에 실제 데이터 집합을 대규모로 Interpretability.
+ 학습 시간에 데이터 및 설명의 패턴을 검색 하는 데 도움이 되는 대화형 시각화


기계 학습에서 **기능은** 대상 데이터 요소를 예측 하는 데 사용 되는 데이터 필드입니다. 예를 들어 신용 위험을 예측 하려면 나이, 계정 크기 및 계정 사용 기간에 대 한 데이터 필드를 사용할 수 있습니다. 이 경우에는 나이, 계정 크기 및 계정 사용 기간이 **기능**입니다. 기능 중요도는 각 데이터 필드가 모델의 예측에 영향을 주는 방법을 알려 줍니다. 예를 들어, 계정 크기와 기간이 예측 정확도에 크게 영향을 주지 않는 동안에는 기간을 예측에 많이 사용할 수 있습니다. 이 프로세스를 통해 데이터 과학자는 결과 예측을 설명할 수 있으므로 이해 관계자가 모델에서 가장 중요 한 데이터 요소를 볼 수 있습니다.

이러한 도구를 사용 하 여 **모든 데이터에서 전 세계의**기계 학습 모델을 설명 하거나, 사용 하기 쉽고 확장 가능한 방식으로 최신 기술을 사용 하 여 **특정 데이터 요소에 로컬로 로컬로** 설명할 수 있습니다.

Interpretability 클래스는 여러 SDK 패키지를 통해 사용할 수 있습니다. [Azure Machine Learning에 대 한 SDK 패키지를 설치](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)하는 방법을 알아봅니다.

* Microsoft에서 지 원하는 기능을 포함 하는 기본 패키지를 `azureml.interpret`합니다.

* 시험해 볼 수 있는 `azureml.contrib.interpret`, 미리 보기 및 실험적 기능.

* 자동화 된 기계 학습 모델을 해석 하는 `azureml.train.automl.automlexplainer` 패키지입니다.

> [!IMPORTANT]
> `contrib` 네임 스페이스의 콘텐츠는 완전히 지원 되지 않습니다. 실험적 기능이 완성 되 면 주 네임 스페이스로 점차적으로 이동 됩니다.

## <a name="how-to-interpret-your-model"></a>모델을 해석 하는 방법

Interpretability 클래스와 메서드를 적용 하 여 모델의 전역 동작 또는 특정 예측을 이해할 수 있습니다. 이전을 전역 설명 이라고 하며 후자를 로컬 설명 이라고 합니다.

메서드가 모델 독립적 인지 아니면 특정 모델 인지에 따라 범주화 될 수도 있습니다. 일부 메서드는 특정 유형의 모델을 대상으로 합니다. 예를 들어 SHAP의 tree 설명는 트리 기반 모델에만 적용 됩니다. 일부 메서드는 모델을 검정색 상자 (예: 설명 또는 SHAP의 커널 설명)로 처리 합니다. `interpret` 패키지는 데이터 집합, 모델 유형 및 사용 사례에 따라 이러한 여러 방법을 활용 합니다.

출력은 다음과 같이 지정 된 모델이 예측을 수행 하는 방법에 대 한 정보 집합입니다.
* 글로벌/로컬 상대적 기능 중요도
* 전역/로컬 기능 및 예측 관계

### <a name="explainers"></a>Explainers

이 패키지는 해석 된 모델을 학습 하 고 블랙 박스 AI 시스템을 설명 하는 데 도움이 되는 오픈 소스 python 패키지인 [해석 커뮤니티](https://github.com/interpretml/interpret-community/)에서 개발한 interpretability 기술을 사용 합니다. [해석-커뮤니티](https://github.com/interpretml/interpret-community/) 는이 SDK의 지원 되는 explainers 호스트 역할을 하며 현재 다음 interpretability 기술을 지원 합니다.

* **Shap 트리 설명**: [shap](https://github.com/slundberg/shap)의 tree 설명는 트리의 트리와 관련 된 다항식 time fast shap 값 예측 알고리즘을 중심으로 합니다.
* **Shap 심층 설명**: [shap](https://github.com/slundberg/shap)의 설명에 따라 심화 설명 "는 [shap Nips 용지](https://papers.nips.cc/paper/7062-a-unified-approach-to-interpreting-model-predictions)에 설명 된 deeplift와의 연결을 기반으로 하는 심층 학습 모델의 shap 값에 대 한 고속 근사값 알고리즘입니다. TensorFlow 백 엔드를 사용 하는 TensorFlow 모델 및 Keras 모델이 지원 됩니다 (PyTorch에 대 한 예비 지원도 있습니다.).
* **Shap 선형 설명**: [shap](https://github.com/slundberg/shap)의 선형 설명는 선형 모델에 대해 shap 값을 계산 하 고, 선택적으로 기능 간 상관 관계를 고려 합니다.

* **Shap Kernel 설명**: [shap](https://github.com/slundberg/shap)의 커널 설명는 특수 한가 중 로컬 선형 회귀를 사용 하 여 모든 모델에 대 한 shap 값을 예측 합니다.
* **설명 모방**: 모방 설명은 블랙 박스 모델을 모방 하기 위해 [전역 서로게이트 모델](https://christophm.github.io/interpretable-ml-book/global.html) 을 학습 하는 아이디어를 기반으로 합니다. 전역 서로게이트 모델은 블랙 박스 모델의 예측을 최대한 정확 하 게 예측 하도록 학습 된 본질적으로 해석 되는 모델입니다. 데이터 과학자은 서로게이트 모델을 해석 하 여 블랙 박스 모델에 대 한 결론을 그릴 수 있습니다. 다음의 해석 가능 모델 중 하나를 서로게이트 모델 (LightGBM (LGBMExplainableModel), 선형 회귀 (LinearExplainableModel), 추계) 및 의사 결정 트리 ()로 사용할 수 있습니다. DecisionTreeExplainableModel).


* **순열 기능 중요도 설명**: 순열 기능 중요도는 [Breiman의 임의 포리스트 문서](https://www.stat.berkeley.edu/~breiman/randomforest2001.pdf) 에 따라 분류 되는 분류 및 회귀 모델을 설명 하는 데 사용 되는 기술입니다 (섹션 10 참조). 높은 수준에서 작동 방식은 전체 데이터 집합에 대해 한 번에 하나의 기능을 임의로 순서 섞기 하 고 관심 있는 성능 메트릭이 변경 되는 정도를 계산 하는 것입니다. 변화가 클수록 해당 기능이 중요한 것입니다.

* **설명** (`contrib`): [라임를 기반으로 하](https://github.com/marcotcr/lime)는 라임 설명는 최신 로컬 해석 모델 (라임) 알고리즘을 사용 하 여 로컬 서로게이트 모델을 만듭니다. 전역 서로게이트 모델과 달리, 라임는 개별 예측을 설명 하는 로컬 서로게이트 모델 학습에 중점을 둔 것입니다.
* **한자 텍스트 설명** (`contrib`): 한자 텍스트 설명는 지정 된 블랙 박스 텍스트 모델에 대 한 텍스트 데이터에서 모델 설명을 가져오기 위해 계층적 주의 네트워크를 사용 합니다. 지정 된 블랙 박스 모델의 예측 된 출력에 대 한 한자 서로게이트 모델을 학습 합니다. 텍스트 모음 전체적으로 학습 한 후에는 설명의 정확도를 향상 시키기 위해 특정 문서에 대 한 미세 조정 단계를 추가 합니다. 한자는 문장 및 단어 주의를 위해 두 개의 주의 계층으로 양방향 RNN을 사용 합니다. DNN가 블랙 박스 모델에 대해 학습 되 고 특정 문서에 대해 미세 조정 되 면 사용자는 주의 계층에서 단어 importances을 추출할 수 있습니다. 한자는 텍스트 데이터에 대 한 라임 또는 SHAP 보다 더 정확 하 게 표시 되지만 학습 시간 측면 에서도 비용이 더 많이 듭니다. 사용자에 게 글러브 word 포함를 사용 하 여 네트워크를 초기화 하는 옵션을 제공 하 여 학습 시간을 줄일 수 있도록 기능이 향상 되었습니다. 원격 Azure GPU VM에서 한자를 실행 하 여 학습 시간을 크게 향상 시킬 수 있습니다. 한자 구현은 [' 문서 분류를 위한 계층적 주의 네트워크 (Yang et al., 2016) '](https://www.researchgate.net/publication/305334401_Hierarchical_Attention_Networks_for_Document_Classification)에 설명 되어 있습니다.


* **테이블 형식 설명**: `TabularExplainer`는 다음 논리를 활용 하 여 직접 [shap](https://github.com/slundberg/shap) Explainers를 호출 합니다.

    1. 트리 기반 모델인 경우 SHAP `TreeExplainer`, 기타를 적용 합니다.
    2. DNN model 인 경우 SHAP `DeepExplainer`, 기타를 적용 합니다.
    3. 선형 모델인 경우 SHAP `LinearExplainer`, 기타를 적용 합니다.
    3. 블랙 박스 모델로 처리 하 고 SHAP를 적용 `KernelExplainer`


또한 `TabularExplainer`는 직접 SHAP Explainers에서 중요 한 기능 및 성능 향상을 만들었습니다.

* **초기화 데이터 집합의 요약**입니다. 설명의 속도가 가장 중요 한 경우 초기화 데이터 집합을 요약 하 고 몇 가지 대표적인 샘플 집합을 생성 하 여 전역 및 로컬 설명의 속도를 높일 수 있습니다.
* **평가 데이터 집합을 샘플링**합니다. 사용자가 많은 수의 평가 샘플을 통과 하지만 실제로 평가할 필요가 없는 경우에는 샘플링 매개 변수를 true로 설정 하 여 글로벌 설명의 속도를 높일 수 있습니다.

다음 다이어그램에서는 직접 및 메타 explainers의 현재 구조를 보여 줍니다.

[![Machine Learning Interpretability 아키텍처](./media/how-to-machine-learning-interpretability/interpretability-architecture.png)](./media/how-to-machine-learning-interpretability/interpretability-architecture.png#lightbox)


### <a name="models-supported"></a>지원 되는 모델

Python `numpy.array`, `pandas.DataFrame`, `iml.datatypes.DenseData`또는 `scipy.sparse.csr_matrix` 형식의 데이터 집합에 대해 학습 된 모든 모델은 SDK의 interpretability `explain` 패키지에서 지원 됩니다.

설명 함수는 모델과 파이프라인을 입력으로 받아들입니다. 모델을 제공 하는 경우 모델은 Scikit 규칙을 준수 하는 `predict_proba` `predict` 예측 함수를 구현 해야 합니다. 파이프라인 (파이프라인 스크립트의 이름)이 제공 된 경우 설명 함수는 실행 중인 파이프라인 스크립트가 예측을 반환 한다고 가정 합니다. PyTorch, TensorFlow 및 Keras 심층 학습 프레임 워크로 학습 된 모델을 지원 합니다.

### <a name="local-and-remote-compute-target"></a>로컬 및 원격 계산 대상

`explain` 패키지는 로컬 및 원격 계산 대상 모두에서 작동 하도록 설계 되었습니다. 로컬로 실행 하는 경우 SDK 함수는 Azure 서비스에 연결 되지 않습니다. Azure Machine Learning 계산에 대 한 설명을 원격으로 실행 하 고 설명 정보를 Azure Machine Learning 실행 기록 서비스에 기록할 수 있습니다. 이 정보가 기록 되 면 설명의 보고서 및 시각화를 사용자 분석을 위해 Azure Machine Learning 작업 영역에서 쉽게 사용할 수 있습니다.


## <a name="next-steps"></a>다음 단계

로컬 및 Azure Machine Learning 원격 계산 리소스 모두에 대 한 모델 학습에 대해 interpretability를 사용 하도록 설정 [하는 방법을](how-to-machine-learning-interpretability-aml.md) 참조 하세요. 추가 시나리오는 [샘플 노트북](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model) 을 참조 하세요.
