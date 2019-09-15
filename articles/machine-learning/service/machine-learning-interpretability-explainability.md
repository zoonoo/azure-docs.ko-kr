---
title: 모델 해석력
titleSuffix: Azure Machine Learning
description: Azure Machine Learning SDK를 사용 하 여 모델이 예측을 수행 하는 이유를 설명 하는 방법을 알아봅니다. 학습 및 유추 중에 모델에서 예측을 만드는 방법을 이해 하는 데 사용할 수 있습니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.reviewer: larryfr
ms.date: 06/21/2019
ms.openlocfilehash: 6b825e61542dabc92baf482ede6c93edc486e059
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/15/2019
ms.locfileid: "71002350"
---
# <a name="model-interpretability-with-azure-machine-learning"></a>Azure Machine Learning 모델 interpretability

이 문서에서는 Azure Machine Learning Python SDK의 다양 한 interpretability 패키지를 사용 하 여 모델에서 예측을 수행한 이유를 설명 하는 방법에 대해 알아봅니다.

SDK의 클래스 및 메서드를 사용 하 여 다음을 얻을 수 있습니다.
+ 원시 기능과 엔지니어링 된 기능 모두에 대 한 기능 중요도 값
+ 학습 및 유추 중에 실제 데이터 집합을 대규모로 Interpretability.
+ 학습 시간에 데이터 및 설명의 패턴을 검색 하는 데 도움이 되는 대화형 시각화

개발 주기의 학습 단계 중 모델 디자이너와 계산기는 모델의 interpretability 출력을 사용 하 여 관련자와의 가설 및 빌드 신뢰를 확인할 수 있습니다.  또한 디버깅을 위해 모델에 대 한 통찰력을 사용 하 고 모델 동작의 유효성을 검사 하 여 목표와 일치 하는지 확인 하 고 바이어스를 확인 합니다.

기계 학습에서 **기능은** 대상 데이터 요소를 예측 하는 데 사용 되는 데이터 필드입니다. 예를 들어 신용 위험을 예측 하려면 나이, 계정 크기 및 계정 사용 기간에 대 한 데이터 필드를 사용할 수 있습니다. 이 경우에는 나이, 계정 크기 및 계정 사용 기간이 **기능**입니다. 기능 중요도는 각 데이터 필드가 모델의 예측에 영향을 주는 방법을 알려 줍니다. 예를 들어, 계정 크기와 기간이 예측 정확도에 크게 영향을 주지 않는 동안에는 기간을 예측에 많이 사용할 수 있습니다. 이 프로세스를 통해 데이터 과학자는 결과 예측을 설명할 수 있으므로 이해 관계자가 모델에서 가장 중요 한 데이터 요소를 볼 수 있습니다.

이러한 도구를 사용 하 여 **모든 데이터에서 전 세계의**기계 학습 모델을 설명 하거나, 사용 하기 쉽고 확장 가능한 방식으로 최신 기술을 사용 하 여 **특정 데이터 요소에 로컬로 로컬로** 설명할 수 있습니다.

Interpretability 클래스는 여러 SDK 패키지를 통해 사용할 수 있습니다. [Azure Machine Learning에 대 한 SDK 패키지를 설치](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)하는 방법을 알아봅니다.

* [`azureml.explain.model`](https://docs.microsoft.com/python/api/azureml-explain-model/?view=azure-ml-py)Microsoft에서 지 원하는 기능을 포함 하는 기본 패키지입니다.

* `azureml.contrib.explain.model`, 미리 보기 및 실험적 기능을 사용해 볼 수 있습니다.

* `azureml.train.automl.automlexplainer`자동화 된 기계 학습 모델을 해석 하기 위한 패키지입니다.

> [!IMPORTANT]
> `contrib` 네임 스페이스의 콘텐츠는 완전히 지원 되지 않습니다. 실험적 기능이 완성 되 면 주 네임 스페이스로 점차적으로 이동 됩니다.

## <a name="how-to-interpret-your-model"></a>모델을 해석 하는 방법

Interpretability 클래스와 메서드를 적용 하 여 모델의 전역 동작 또는 특정 예측을 이해할 수 있습니다. 이전을 전역 설명 이라고 하며 후자를 로컬 설명 이라고 합니다.

메서드가 모델 독립적 인지 아니면 특정 모델 인지에 따라 범주화 될 수도 있습니다. 일부 메서드는 특정 유형의 모델을 대상으로 합니다. 예를 들어 SHAP의 tree 설명는 트리 기반 모델에만 적용 됩니다. 일부 메서드는 모델을 검정색 상자 (예: 설명 또는 SHAP의 커널 설명)로 처리 합니다. 패키지 `explain` 는 데이터 집합, 모델 유형 및 사용 사례에 따라 이러한 여러 방법을 활용 합니다.

출력은 다음과 같이 지정 된 모델이 예측을 수행 하는 방법에 대 한 정보 집합입니다.
* 글로벌/로컬 상대적 기능 중요도

* 전역/로컬 기능 및 예측 관계

### <a name="explainers"></a>Explainers

Explainers에는 다음과 같은 두 가지 집합이 있습니다. SDK의 Direct Explainers 및 Meta Explainers.

__직접 explainers__ 는 통합 라이브러리에서 제공 됩니다. SDK는 일반적인 API 및 출력 형식을 노출 하도록 모든 explainers를 래핑합니다. 이러한 explainers를 직접 사용 하는 것이 더 어려우면 일반적인 API 및 출력 형식을 사용 하는 대신 직접 호출할 수 있습니다. 다음은 SDK에서 사용할 수 있는 직접 explainers의 목록입니다.

* **Shap 트리 설명**: SHAP의 tree 설명는 트리의 트리와 관련 된 다항식 time fast SHAP 값 예측 알고리즘을 중심으로 합니다.
* **Shap 심층 설명**: SHAP의 설명에 따라 Deep 설명 "는 SHAP NIPS 용지에 설명 된 DeepLIFT와의 연결을 기반으로 하는 심층 학습 모델의 SHAP 값에 대 한 고속 근사값 알고리즘입니다. TensorFlow 백 엔드를 사용 하는 TensorFlow 모델 및 Keras 모델이 지원 됩니다 (PyTorch에 대 한 예비 지원도 있습니다.).
* **Shap 커널 설명**: SHAP의 커널 설명는 특별히 가중치가 적용 된 로컬 선형 회귀를 사용 하 여 모든 모델에 대 한 SHAP 값을 예측 합니다.
* **설명 모방**: 설명는 전역 서로게이트 모델의 아이디어를 기반으로 합니다. 전역 서로게이트 모델은 블랙 박스 모델의 예측을 최대한 정확 하 게 예측 하도록 학습 된 본질적으로 해석 되는 모델입니다. 데이터 과학자은 서로게이트 모델을 해석 하 여 블랙 박스 모델에 대 한 결론을 그릴 수 있습니다. 다음의 해석 가능 모델 중 하나를 서로게이트 모델로 사용할 수 있습니다. LightGBM (LinearExplainableModel), 선형 회귀 (LinearExplainableModel), 추계 explainable 모델 (SGDExplainableModel) 및 의사 결정 트리 (DecisionTreeExplainableModel)가 있습니다.


* **순열 기능 중요도 설명**: 순열 기능 중요도는 [Breiman의 임의 포리스트 용지](https://www.stat.berkeley.edu/%7Ebreiman/randomforest2001.pdf) (섹션 10 참조)에서 설명 하는 분류 및 회귀 모델을 설명 하는 데 사용 되는 기술입니다. 높은 수준에서 작동 방식은 전체 데이터 집합에 대해 한 번에 하나의 기능을 임의로 순서 섞기 하 고 관심 있는 성능 메트릭이 감소 하는 정도를 계산 하는 것입니다. 변경이 클수록 해당 기능이 더 중요 합니다.

* **라임 설명** (`contrib`): 라임를 기반으로 하는 라임 설명는 최신 로컬 해석 모델 (라임) 알고리즘을 사용 하 여 로컬 서로게이트 모델을 만듭니다. 전역 서로게이트 모델과 달리, 라임는 개별 예측을 설명 하는 로컬 서로게이트 모델 학습에 중점을 둔 것입니다.
* **한자 텍스트 설명** (`contrib`): 한자 텍스트 설명는 지정 된 블랙 박스 텍스트 모델에 대 한 텍스트 데이터에서 모델 설명을 가져오기 위해 계층적 주의 네트워크를 사용 합니다. 지정 된 교사 모델의 예측 된 출력에서 한자 서로게이트 모델을 학습 합니다. 텍스트 모음 전체적으로 학습 한 후에는 설명의 정확도를 향상 시키기 위해 특정 문서에 대 한 미세 조정 단계를 추가 했습니다. 한자는 문장 및 단어 주의를 위해 두 개의 주의 계층으로 양방향 RNN을 사용 합니다. DNN이 교사 모델에 대해 학습 되 고 특정 문서에 대해 미세 조정 되 면 주목 계층에서 단어 importances을 추출할 수 있습니다. 텍스트 데이터에 대 한 라임 또는 SHAP 보다 정확 하 게 정확 하 게 사용할 수 있지만 학습 시간 측면에서 비용이 더 많이 듭니다. 그러나 글러브 word 포함를 사용 하 여 네트워크를 초기화 하는 옵션을 사용자에 게 제공 하 여 학습 시간을 향상 시켰습니다. 하지만 여전히 느립니다. 원격 Azure GPU VM에서 한자를 실행 하 여 학습 시간을 크게 향상 시킬 수 있습니다. 한자 구현은 [' 문서 분류를 위한 계층적 주의 네트워크 (Yang et al., 2016) '](https://www.researchgate.net/publication/305334401_Hierarchical_Attention_Networks_for_Document_Classification)에 설명 되어 있습니다.


__Meta explainers__ 는 적절 한 직접 설명을 자동으로 선택 하 고 지정 된 모델 및 데이터 집합을 기반으로 최상의 설명 정보를 생성 합니다. Meta explainers는 통합 또는 개발한 모든 라이브러리 (SHAP, 라임, 모방 등)를 활용 합니다. SDK에서 사용할 수 있는 meta explainers는 다음과 같습니다.

* **테이블 형식 설명**: 테이블 형식 데이터 집합에 사용 됩니다.
* **텍스트 설명**: 텍스트 데이터 집합에 사용 됩니다.
* **이미지 설명**: 이미지 데이터 집합에 사용 됩니다.

Meta explainers는 직접 explainers를 메타 선택 하는 것 외에도 기본 라이브러리 위에 추가 기능을 개발 하 고 직접 explainers의 속도 및 확장성을 향상 시킵니다.

에서는 `TabularExplainer` 현재 직접 shap Explainers를 호출 하는 다음 논리를 채택 합니다.

1. 트리 기반 모델인 경우 shap `TreeExplainer`를 적용 합니다.
2. DNN 모델인 경우 shap `DeepExplainer`를 적용 합니다.
3. 블랙 박스 모델로 처리 하 고 SHAP 적용`KernelExplainer`

에 `TabularExplainer` 기본 제공 되는 인텔리전스는 더 많은 라이브러리가 SDK에 통합 되어 더 복잡 해지고 각 설명의 장단점에 대해 알아봅니다.

`TabularExplainer`는 또한 직접 Explainers에 대 한 중요 한 기능 및 성능 향상을 만들었습니다.

* **초기화 데이터 집합의 요약**입니다. 설명의 속도가 가장 중요 한 경우 초기화 데이터 집합을 요약 하 고 몇 가지 대표적인 샘플 집합을 생성 하 여 전역 및 로컬 설명의 속도를 높일 수 있습니다.
* **평가 데이터 집합을 샘플링**합니다. 사용자가 많은 수의 평가 샘플을 통과 하지만 실제로 평가할 필요가 없는 경우에는 샘플링 매개 변수를 true로 설정 하 여 글로벌 설명의 속도를 높일 수 있습니다.

다음 다이어그램에서는 직접 및 메타 explainers의 현재 구조를 보여 줍니다.

[![Machine Learning Interpretability 아키텍처](./media/machine-learning-interpretability-explainability/interpretability-architecture.png)](./media/machine-learning-interpretability-explainability/interpretability-architecture.png#lightbox)


### <a name="models-supported"></a>지원 되는 모델

Python `numpy.array`, `pandas.DataFrame` `explain` , 또는`iml.datatypes.DenseData`형식의 데이터집합에대해학습된모든모델은SDK의interpretability`scipy.sparse.csr_matrix` 패키지에서 지원 됩니다.

설명 함수는 모델과 파이프라인을 입력으로 받아들입니다. 모델을 제공 하는 경우 모델은 예측 함수 `predict` 를 구현 하거나 `predict_proba` Scikit 규칙을 준수 해야 합니다. 파이프라인 (파이프라인 스크립트의 이름)이 제공 된 경우 설명 함수는 실행 중인 파이프라인 스크립트가 예측을 반환 한다고 가정 합니다. PyTorch, TensorFlow 및 Keras 심층 학습 프레임 워크로 학습 된 모델을 지원 합니다.

### <a name="local-and-remote-compute-target"></a>로컬 및 원격 계산 대상

패키지 `explain` 는 로컬 및 원격 계산 대상 모두에서 작동 하도록 설계 되었습니다. 로컬로 실행 하는 경우 SDK 함수는 Azure 서비스에 연결 되지 않습니다. Azure Machine Learning 계산에 대 한 설명을 원격으로 실행 하 고 설명 정보를 Azure Machine Learning 실행 기록 서비스에 기록할 수 있습니다. 이 정보가 기록 되 면 설명의 보고서 및 시각화를 사용자 분석을 위해 Azure Machine Learning 작업 영역 포털에서 쉽게 사용할 수 있습니다.

## <a name="interpretability-in-training"></a>Interpretability 교육

### <a name="train-and-explain-locally"></a>로컬에서 학습 및 설명

1. 로컬 Jupyter 노트북에서 모델을 학습 합니다.

    ```python
    # load breast cancer dataset, a well-known small dataset that comes with scikit-learn
    from sklearn.datasets import load_breast_cancer
    from sklearn import svm
    from sklearn.model_selection import train_test_split
    breast_cancer_data = load_breast_cancer()
    classes = breast_cancer_data.target_names.tolist()
    
    # split data into train and test
    from sklearn.model_selection import train_test_split
    x_train, x_test, y_train, y_test = train_test_split(breast_cancer_data.data,            
                                                        breast_cancer_data.target,  
                                                        test_size=0.2,
                                                        random_state=0)
    clf = svm.SVC(gamma=0.001, C=100., probability=True)
    model = clf.fit(x_train, y_train)
    ```

2. 설명를 호출 합니다. 설명 개체를 초기화 하려면 모델 및 일부 학습 데이터를 설명의 생성자에 전달 해야 합니다. 설명 및 시각화에 대 한 자세한 정보를 제공 하는 데 사용 되는 기능 이름 및 출력 클래스 이름 (분류를 수행 하는 경우)을 선택적으로 전달할 수도 있습니다. [TabularExplainer](https://docs.microsoft.com/python/api/azureml-explain-model/azureml.explain.model.tabularexplainer?view=azure-ml-py), [MimicExplainer](https://docs.microsoft.com/python/api/azureml-explain-model/azureml.explain.model.mimic.mimicexplainer?view=azure-ml-py)및 [PFIExplainer](https://docs.microsoft.com/python/api/azureml-explain-model/azureml.explain.model.permutation.permutation_importance.pfiexplainer?view=azure-ml-py) 을 로컬로 사용 하 여 설명 개체를 인스턴스화하는 방법은 다음과 같습니다. `TabularExplainer`는 (`TreeExplainer`, `DeepExplainer`또는 `KernelExplainer`) 아래에 있는 세 개의 shap explainers 중 하나를 호출 하 고 사용 사례에 가장 적합 한 항목을 자동으로 선택 합니다. 그러나 세 개의 기본 explainers을 직접 호출할 수 있습니다.

    ```python
    from azureml.explain.model.tabular_explainer import TabularExplainer
    # "features" and "classes" fields are optional
    explainer = TabularExplainer(model, 
                                 x_train, 
                                 features=breast_cancer_data.feature_names, 
                                 classes=classes)
    ```

    로 구분하거나 여러

    ```python
    from azureml.explain.model.mimic.mimic_explainer import MimicExplainer
    
    # you can use one of the following four interpretable models as a global surrogate to the black box model
    from azureml.explain.model.mimic.models.lightgbm_model import LGBMExplainableModel
    from azureml.explain.model.mimic.models.linear_model import LinearExplainableModel
    from azureml.explain.model.mimic.models.linear_model import SGDExplainableModel
    from azureml.explain.model.mimic.models.tree_model import DecisionTreeExplainableModel

    # "features" and "classes" fields are optional
    # augment_data is optional and if true, oversamples the initialization examples to improve surrogate model accuracy to fit original model.  Useful for high-dimensional data where the number of rows is less than the number of columns. 
    # max_num_of_augmentations is optional and defines max number of times we can increase the input data size.
    # LGBMExplainableModel can be replaced with LinearExplainableModel, SGDExplainableModel, or DecisionTreeExplainableModel
    explainer = MimicExplainer(model, 
                               x_train, 
                               LGBMExplainableModel, 
                               augment_data=True, 
                               max_num_of_augmentations=10, 
                               features=breast_cancer_data.feature_names, 
                               classes=classes)
    ```
   로 구분하거나 여러

    ```python
    from azureml.explain.model.permutation.permutation_importance import PFIExplainer 
    
    # "features" and "classes" fields are optional
    explainer = PFIExplainer(model, 
                             features=breast_cancer_data.feature_names, 
                             classes=classes)
    ```

3. 전역 기능 중요도 값을 가져옵니다.

    ```python
    # you can use the training data or the test data here
    global_explanation = explainer.explain_global(x_train)
    
    # if you used the PFIExplainer in the previous step, use the next line of code instead
    # global_explanation = explainer.explain_global(x_train, true_labels=y_test)

    # sorted feature importance values and feature names
    sorted_global_importance_values = global_explanation.get_ranked_global_values()
    sorted_global_importance_names = global_explanation.get_ranked_global_names()
    dict(zip(sorted_global_importance_names, sorted_global_importance_values))

    # alternatively, you can print out a dictionary that holds the top K feature names and values
    global_explanation.get_feature_importance_dict()
    ```

4. 로컬 기능 중요도 값 가져오기: 다음 함수 호출을 사용 하 여 개별 인스턴스 또는 인스턴스 그룹을 설명 합니다. PFIExplainer는 로컬 설명을 지원 하지 않습니다.

    ```python
    # explain the first data point in the test set
    local_explanation = explainer.explain_local(x_test[0])

    # sorted feature importance values and feature names
    sorted_local_importance_names = local_explanation.get_ranked_local_names()
    sorted_local_importance_values = local_explanation.get_ranked_local_values()
    ```

    로 구분하거나 여러

    ```python
    # explain the first five data points in the test set
    local_explanation = explainer.explain_local(x_test[0:4])

    # sorted feature importance values and feature names
    sorted_local_importance_names = local_explanation.get_ranked_local_names()
    sorted_local_importance_values = local_explanation.get_ranked_local_values()
    ```

### <a name="train-and-explain-remotely"></a>원격 학습 및 설명

Azure Machine Learning에서 지 원하는 다양 한 계산 대상을 학습할 수 있지만이 섹션의 예제에서는 Azure Machine Learning 계산 대상을 사용 하 여이 작업을 수행 하는 방법을 보여 줍니다.

1. 로컬 Jupyter 노트북 (예: py)에서 학습 스크립트를 만듭니다.

    ```python
    from azureml.contrib.explain.model.explanation.explanation_client import ExplanationClient
    from azureml.core.run import Run

    run = Run.get_context()
    client = ExplanationClient.from_run(run)

    # write code to get and split your data into train and test sets here
    # write code to train your model here 

    # explain predictions on your local machine
    # "features" and "classes" fields are optional
    explainer = TabularExplainer(model, 
                                x_train, 
                                features=feature_names, 
                                classes=classes)

    # explain overall model predictions (global explanation)
    global_explanation = explainer.explain_global(x_test)
    
    # uploading global model explanation data for storage or visualization in webUX
    # the explanation can then be downloaded on any compute
    # multiple explanations can be uploaded
    client.upload_model_explanation(global_explanation, comment='global explanation: all features')
    # or you can only upload the explanation object with the top k feature info
    #client.upload_model_explanation(global_explanation, top_k=2, comment='global explanation: Only top 2 features')
    ```

2. [모델 학습을 위한 계산 대상 설정](how-to-set-up-training-targets.md#amlcompute) 의 지침에 따라 계산 대상으로 Azure Machine Learning 계산을 설정 하 고 학습 실행을 제출 하는 방법에 대해 알아봅니다.

3. 로컬 Jupyter 노트북에서 설명을 다운로드 합니다.

    ```python
    from azureml.contrib.explain.model.explanation.explanation_client import ExplanationClient
    
    client = ExplanationClient.from_run(run)
    
    # get model explanation data
    explanation = client.download_model_explanation()
    # or only get the top k (e.g., 4) most important features with their importance values
    explanation = client.download_model_explanation(top_k=4)
    
    global_importance_values = explanation.get_ranked_global_values()
    global_importance_names = explanation.get_ranked_global_names()
    print('global importance values: {}'.format(global_importance_values))
    print('global importance names: {}'.format(global_importance_names))
    ```

## <a name="visualizations"></a>시각화

시각화 대시보드를 사용 하 여 모델을 이해 하 고 해석 합니다.

### <a name="global-visualizations"></a>전역 시각화

다음 그림에서는 학습 된 모델을 예측 및 설명과 함께 전체적으로 보여 주는 방법을 제공 합니다.

|그림|Description|
|----|-----------|
|데이터 탐색| 예측 값과 함께 데이터 집합에 대 한 개요입니다.|
|글로벌 중요도|최상위 K (구성 가능 K) 중요 기능을 전역적으로 보여 줍니다. 이 차트는 기본 모델의 전역 동작을 이해 하는 데 유용 합니다.|
|설명 탐색|기능이 모델의 예측 값 (또는 예측 값의 확률)을 변경 하는 방법을 보여 줍니다. |
|요약| 모든 데이터 요소에서 서명 된 로컬 기능 중요도 값을 사용 하 여 각 기능이 예측 값에 미치는 영향의 분포를 표시 합니다.|

[![시각화 대시보드 전역](./media/machine-learning-interpretability-explainability/global-charts.png)](./media/machine-learning-interpretability-explainability/global-charts.png#lightbox)

### <a name="local-visualizations"></a>로컬 시각화

이전 그림에서 언제 든 지 개별 데이터 요소를 클릭 하 여 지정 된 데이터 요소에 대 한 로컬 기능 중요도 그림을 로드할 수 있습니다.

|그림|설명|
|----|-----------|
|로컬 중요도|최상위 K (구성 가능 K) 중요 기능을 전역적으로 보여 줍니다. 이 차트는 특정 데이터 요소에 대 한 기본 모델의 로컬 동작을 이해 하는 데 유용 합니다.|
|Perturbation 탐색|선택한 데이터 요소의 기능 값을 변경 하 고 이러한 변경 내용이 예측 값에 미치는 영향을 관찰할 수 있습니다.|
|개별 조건부 예상 (ICE)| 기능 값을 최 댓 값에서 최대값으로 변경 하 여 기능이 변경 될 때 데이터 요소의 예측이 어떻게 변경 되는지 확인할 수 있습니다.|

[![시각화 대시보드 로컬 기능 중요도](./media/machine-learning-interpretability-explainability/local-charts.png)](./media/machine-learning-interpretability-explainability/local-charts.png#lightbox)


[![시각화 대시보드 기능 Perturbation](./media/machine-learning-interpretability-explainability/perturbation.gif)](./media/machine-learning-interpretability-explainability/perturbation.gif#lightbox)


[![시각화 대시보드 ICE 플롯](./media/machine-learning-interpretability-explainability/ice-plot.png)](./media/machine-learning-interpretability-explainability/ice-plot.png#lightbox)

참고 Jupyter 커널을 시작 하기 전에 시각화 대시보드의 위젯 확장을 사용 하도록 설정 해야 합니다.

* Jupyter Notebook

    ```shell
    jupyter nbextension install --py --sys-prefix azureml.contrib.explain.model.visualize
    jupyter nbextension enable --py --sys-prefix azureml.contrib.explain.model.visualize
    ```



* Jupyter 랩

    ```shell
    jupyter labextension install @jupyter-widgets/jupyterlab-manager
    jupyter labextension install microsoft-mli-widget
    ```
시각화 대시보드를 로드 하려면 다음 코드를 사용 합니다.

```python
from azureml.contrib.explain.model.visualize import ExplanationDashboard

ExplanationDashboard(global_explanation, model, x_test)
```

## <a name="raw-feature-transformations"></a>원시 기능 변환

필요에 따라 기능 변환 파이프라인을 설명에 전달 하 여 변환 (엔지니어링 된 기능 대신)의 원시 기능을 기준으로 설명을 수신할 수 있습니다. 이를 건너뛰면 설명는 엔지니어링 된 기능 측면에서 설명을 제공 합니다.

지원 되는 변환의 형식은 [pandas](https://github.com/scikit-learn-contrib/sklearn-pandas)에서 설명한 것과 같습니다. 일반적으로 모든 변환은 단일 열에서 작동 하는 한, 따라서 명확 하 게 한 번만 지원 됩니다. 

`sklearn.compose.ColumnTransformer` 또는 피팅 변환기 튜플 목록을 사용 하 여 원시 기능을 설명할 수 있습니다. 아래 셀은를 `sklearn.compose.ColumnTransformer`사용 합니다. 

```python
from sklearn.compose import ColumnTransformer

numeric_transformer = Pipeline(steps=[
    ('imputer', SimpleImputer(strategy='median')),
    ('scaler', StandardScaler())])

categorical_transformer = Pipeline(steps=[
    ('imputer', SimpleImputer(strategy='constant', fill_value='missing')),
    ('onehot', OneHotEncoder(handle_unknown='ignore'))])

preprocessor = ColumnTransformer(
    transformers=[
        ('num', numeric_transformer, numeric_features),
        ('cat', categorical_transformer, categorical_features)])

# append classifier to preprocessing pipeline.
# now we have a full prediction pipeline.
clf = Pipeline(steps=[('preprocessor', preprocessor),
                      ('classifier', LogisticRegression(solver='lbfgs'))])


# append classifier to preprocessing pipeline.
# now we have a full prediction pipeline.
clf = Pipeline(steps=[('preprocessor', preprocessor),
                      ('classifier', LogisticRegression(solver='lbfgs'))])


# clf.steps[-1][1] returns the trained classification model
# pass transformation as an input to create the explanation object
# "features" and "classes" fields are optional
tabular_explainer = TabularExplainer(clf.steps[-1][1],
                                     initialization_examples=x_train,
                                     features=dataset_feature_names,
                                     classes=dataset_classes,
                                     transformations=preprocessor)
```

피팅 변환기 튜플 목록으로 예제를 실행 하려는 경우 다음 코드를 사용 합니다. 
```python
from sklearn.pipeline import Pipeline
from sklearn.impute import SimpleImputer
from sklearn.preprocessing import StandardScaler, OneHotEncoder
from sklearn.linear_model import LogisticRegression
from sklearn_pandas import DataFrameMapper

# assume that we have created two arrays, numerical and categorical, which holds the numerical and categorical feature names

numeric_transformations = [([f], Pipeline(steps=[('imputer', SimpleImputer(
    strategy='median')), ('scaler', StandardScaler())])) for f in numerical]

categorical_transformations = [([f], OneHotEncoder(
    handle_unknown='ignore', sparse=False)) for f in categorical]

transformations = numeric_transformations + categorical_transformations

# append model to preprocessing pipeline.
# now we have a full prediction pipeline.
clf = Pipeline(steps=[('preprocessor', DataFrameMapper(transformations)),
                      ('classifier', LogisticRegression(solver='lbfgs'))])

# clf.steps[-1][1] returns the trained classification model
# pass transformation as an input to create the explanation object
# "features" and "classes" fields are optional
tabular_explainer = TabularExplainer(clf.steps[-1][1],
                                     initialization_examples=x_train,
                                     features=dataset_feature_names,
                                     classes=dataset_classes,
                                     transformations=transformations)
```

## <a name="interpretability-at-inferencing-time"></a>추론 time에서 Interpretability

원본 모델과 함께 설명를 배포할 수 있으며, 점수 매기기 시간에 로컬 설명 정보를 제공 하는 데 사용할 수 있습니다. 또한 추론 시간에 더 explainers을 interpretability 하는 가벼운 점수 매기기 기능을 제공 합니다. 가벼운 점수 매기기 설명를 배포 하는 프로세스는 모델 배포와 유사 하며 다음 단계를 포함 합니다.




1. 설명 개체 (예: TabularExplainer 사용)를 만듭니다.

   ```python
   from azureml.contrib.explain.model.tabular_explainer import TabularExplainer

   explainer = TabularExplainer(model, 
                                initialization_examples=x_train, 
                                features=dataset_feature_names, 
                                classes=dataset_classes, 
                                transformations=transformations)
   ```

1. 설명 개체를 사용 하 여 점수 매기기 설명을 만듭니다.

   ```python
   from azureml.contrib.explain.model.scoring.scoring_explainer import KernelScoringExplainer, save

   # create a lightweight explainer at scoring time
   scoring_explainer = KernelScoringExplainer(explainer)

   # pickle scoring explainer
   # pickle scoring explainer locally
   OUTPUT_DIR = 'my_directory'
   save(scoring_explainer, directory=OUTPUT_DIR, exist_ok=True)
   ```

1. 점수 매기기 설명 모델을 사용 하는 이미지를 구성 하 고 등록 합니다.

   ```python
   # register explainer model using the path from ScoringExplainer.save - could be done on remote compute
   # scoring_explainer.pkl is the filename on disk, while my_scoring_explainer.pkl will be the filename in cloud storage
   run.upload_file('my_scoring_explainer.pkl', os.path.join(OUTPUT_DIR, 'scoring_explainer.pkl'))
   
   scoring_explainer_model = run.register_model(model_name='my_scoring_explainer', 
                                                model_path='my_scoring_explainer.pkl')
   print(scoring_explainer_model.name, scoring_explainer_model.id, scoring_explainer_model.version, sep = '\t')
   ```

1. 필드 클라우드에서 점수 매기기 설명을 검색 하 고 설명을 테스트 합니다.

   ```python
   from azureml.contrib.explain.model.scoring.scoring_explainer import load

   # retrieve the scoring explainer model from cloud"
   scoring_explainer_model = Model(ws, 'my_scoring_explainer')
   scoring_explainer_model_path = scoring_explainer_model.download(target_dir=os.getcwd(), exist_ok=True)

   # load scoring explainer from disk
   scoring_explainer = load(scoring_explainer_model_path)

   # test scoring explainer locally
   preds = scoring_explainer.explain(x_test)
   print(preds)
   ```

1. 계산 대상에 이미지를 배포 합니다.

   1. 점수 매기기 파일 만들기 (이 단계를 수행 하기 전에 [Azure Machine Learning으로 모델 배포](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-and-where) 의 단계에 따라 원래 예측 모델을 등록)

        ```python
        %%writefile score.py
        import json
        import numpy as np
        import pandas as pd
        import os
        import pickle
        from sklearn.externals import joblib
        from sklearn.linear_model import LogisticRegression
        from azureml.core.model import Model

        def init():

            global original_model
            global scoring_model

            # retrieve the path to the model file using the model name
            # assume original model is named original_prediction_model
            original_model_path = Model.get_model_path('original_prediction_model')
            scoring_explainer_path = Model.get_model_path('my_scoring_explainer')

            original_model = joblib.load(original_model_path)
            scoring_explainer = joblib.load(scoring_explainer_path)

        def run(raw_data):
            # get predictions and explanations for each data point
            data = pd.read_json(raw_data)
            # make prediction
            predictions = original_model.predict(data)
            # retrieve model explanations
            local_importance_values = scoring_explainer.explain(data)
            # you can return any data type as long as it is JSON-serializable
            return {'predictions': predictions.tolist(), 'local_importance_values': local_importance_values}
        ```

   1. 배포 구성을 정의 합니다 .이 구성은 모델 요구 사항에 따라 달라 집니다. 다음 예제에서는 하나의 CPU 코어 및 1gb 메모리를 사용 하는 구성을 정의 합니다.

        ```python
        from azureml.core.webservice import AciWebservice

        aciconfig = AciWebservice.deploy_configuration(cpu_cores=1,
                                                       memory_gb=1,
                                                       tags={"data": "NAME_OF_THE_DATASET",
                                                             "method" : "local_explanation"},
                                                       description='Get local explanations for NAME_OF_THE_PROBLEM')
        ```

   1. 환경 종속성이 있는 파일 만들기

        ```python
        from azureml.core.conda_dependencies import CondaDependencies

        # WARNING: to install this, g++ needs to be available on the Docker image and is not by default (look at the next cell)

        azureml_pip_packages = ['azureml-defaults', 'azureml-contrib-explain-model', 'azureml-core', 'azureml-telemetry', 'azureml-explain-model']
 

        # specify CondaDependencies obj
        myenv = CondaDependencies.create(conda_packages=['scikit-learn', 'pandas'],
                                         pip_packages=['sklearn-pandas'] + azureml_pip_packages,
                                         pin_sdk_version=False)


        with open("myenv.yml","w") as f:
            f.write(myenv.serialize_to_string())

        with open("myenv.yml","r") as f:
            print(f.read())
        ```

   1. G + +가 설치 된 사용자 지정 dockerfile 만들기

        ```python
        %%writefile dockerfile
        RUN apt-get update && apt-get install -y g++
        ```

   1. 생성 된 이미지 배포 (예상 시간: 5 분)

        ```python
        from azureml.core.webservice import Webservice
        from azureml.core.image import ContainerImage

        # use the custom scoring, docker, and conda files we created above
        image_config = ContainerImage.image_configuration(execution_script="score.py",
                                                        docker_file="dockerfile",
                                                        runtime="python",
                                                        conda_file="myenv.yml")

        # use configs and models generated above
        service = Webservice.deploy_from_model(workspace=ws,
                                            name='model-scoring-service',
                                            deployment_config=aciconfig,
                                            models=[scoring_explainer_model, original_model],
                                            image_config=image_config)

        service.wait_for_deployment(show_output=True)
        ```

1. 배포 테스트

    ```python
    import requests

    # create data to test service with
    examples = x_list[:4]
    input_data = examples.to_json()

    headers = {'Content-Type':'application/json'}

    # send request to service
    resp = requests.post(service.scoring_uri, input_data, headers=headers)

    print("POST to url", service.scoring_uri)
    # can covert back to Python objects from json string if desired
    print("prediction:", resp.text)
    ```

1. 정리: 배포된 웹 서비스를 삭제하려면 `service.delete()`를 사용합니다.




## <a name="next-steps"></a>다음 단계

위의 지침을 보여 주는 Jupyter 노트북의 컬렉션을 보려면 [Azure Machine Learning Interpretability 샘플 노트북](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model)을 참조 하세요.
