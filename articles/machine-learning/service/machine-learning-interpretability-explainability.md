---
title: 모델 해석력
titleSuffix: Azure Machine Learning service
description: 모델에서 Azure Machine Learning SDK를 사용 하 여 예측을 수행 하는 이유를 설명 하는 방법에 알아봅니다. 모델에서 예측을 수행 하는 방법을 이해 하려면 교육 및 유추 하는 동안 사용할 수 있습니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.reviewer: larryfr
ms.date: 05/30/2019
ms.openlocfilehash: 4f1dd0bfc0637c6681b513860a63df2b8c35fe5e
ms.sourcegitcommit: 087ee51483b7180f9e897431e83f37b08ec890ae
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/31/2019
ms.locfileid: "66430864"
---
# <a name="model-interpretability-with-azure-machine-learning-service"></a>Azure Machine Learning 서비스를 사용 하 여 모델 interpretability

모델 예측을 수행 하는 이유를 설명 하는 방법을 알아봅니다이 문서에서는 Azure Machine Learning Python SDK의 다양 한 interpretability 패키지를 사용 하 여 수행한 것입니다.

클래스 및 메서드를 사용 하 여 SDK에서, 다음을 얻을 수 있습니다.
+ 원시 및 엔지니어링 된 기능에 대 한 중요도 값 기능
+ 교육 및 유추 하는 동안 규모가 든 실제 데이터 집합에서 interpretability 합니다.
+ 학습 시 검색 데이터 및 설명의 패턴을 지원 하기 위해 대화형 시각화

개발 주기의 교육 단계 중 모델 디자이너 및 계산기 가설을 확인 하 고 관련자와 신뢰를 구축 하는 모델의 interpretability 출력을 사용할 수 있습니다.  또한 사용 하 여 모델에 대 한 정보를 디버깅, 일치 하는 목표를 모델의 동작의 유효성을 검사 및 편차에 대 한 확인 합니다.

Machine learning에서 **기능** 데이터 필드를 대상 데이터 요소를 예측 하는 데 사용 됩니다. 예를 들어 신용 위험 예측, 나이, 계정 크기 및 계정 사용 기간에 대 한 데이터 필드를 사용할 수 있습니다. 이 경우에 나이, 계정 크기 및 계정 시대는 **기능**합니다. 기능 중요도 각 데이터 필드를 모델의 예측에 영향을 알려 줍니다. 예를 들어 age 과도 하 게 사용할 수 예측에 계정 크기 및 기간에 영향을 주지 예측 정확도 크게 동안. 이 프로세스는 결과 예측을 설명 하기 위해 데이터 과학자를 통해 관련자에 게가 모델에서 가장 중요 한 데이터 요소가 무엇에 대 한 가시성을 갖도록 합니다.

이러한 도구를 사용 하 여, 기계 학습 모델을 설명할 수 있습니다 **모든 데이터에 대해 전 세계**, 또는 **특정 데이터 요소에 로컬로** 최신의 기술을 사용 하기 쉬운 하 고 확장 가능한 방식으로 사용 하 합니다.

Interpretability 클래스는 여러 SDK 패키지를 통해 가능 합니다. 설명 하는 방법 [Azure Machine Learning에 대 한 SDK 패키지를 설치](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)합니다.

* [`azureml.explain.model`](https://docs.microsoft.com/python/api/azureml-explain-model/?view=azure-ml-py)를 Microsoft에서 지 원하는 기능을 포함 하는 기본 패키지입니다.

* `azureml.contrib.explain.model`미리 보기 및 시도할 수 있는 실험적 기능입니다.

* `azureml.train.automl.automlexplainer` 자동화 된 기계 학습 모델을 해석 하는 것에 대 한 패키지입니다.

> [!IMPORTANT]
> 콘텐츠를 `contrib` 네임 스페이스 완전히 지원 되지 않습니다. 실험적 기능 성숙 해지면 점진적으로 기본 네임 스페이스에 이동지 것입니다.

## <a name="how-to-interpret-your-model"></a>모델을 해석 하는 방법

Interpretability 클래스 및 모델의 전역 동작이 나 특정 예측을 이해 하는 메서드를 적용할 수 있습니다. 전자는 전역 설명 호출 되 고 후자는 로컬 설명 호출 됩니다.

메서드를 메서드 모델을 알 수 없는 또는 특정 모델 인지에 따라도 분류할 수 있습니다. 일부 메서드는 특정 유형의 모델 대상입니다. 예를 들어 SHAP의 트리 설명만 트리 기반 모델에 적용 됩니다. 일부 메서드는 모방 설명 등 SHAP의 커널 설명 검은색 상자로 모델을 처리합니다. `explain` 패키지 데이터 집합, 모델 형식 및 사용 사례에 따라 이러한 다른 접근 방식을 활용 합니다.

출력은에 지정 된 모델을 사용 하 여 방법 예측에 따라 같은 정보 집합입니다.
* 글로벌/로컬 상대적 기능 중요도

* 글로벌/로컬 기능과 예측 관계

### <a name="explainers"></a>Explainers

Explainers의 두 가지 있습니다. SDK에서 직접 Explainers 및 메타 Explainers 합니다.

__직접 explainers__ 통합된 라이브러리에서 제공 됩니다. SDK는 공통 API 및 출력 형식을 노출 되도록 모든 explainers를 래핑합니다. 더 편리 하 게 직접 이러한 explainers 인 경우 출력 형식을 확인 하 고 공용 API를 사용 하는 대신 직접 호출할 수 있습니다. 다음은 SDK에서 사용할 수 있는 직접 explainers의 목록입니다.

* **설명 트리**: SHAP의 트리 설명 다항 시간 빠른 SHAP 값 예측 알고리즘 트리 및 트리 앙상블에 중점을 둡니다.
* **전체 설명**: "DeepLIFT SHAP NIPS 문서에 설명 된 연결에서 작성 하는 심층 학습 모델에서 SHAP 값에 대 한 고속 근사값 알고리즘은 SHAP, 심층 설명에서에서 설명에 따라 TensorFlow 모델과 Keras는 TensorFlow 백 엔드를 사용 하 여 지원 됩니다 (이기도 PyTorch에 대 한 예비 지원) ".
* **커널 설명**: SHAP의 커널 설명 모든 모델에 대 한 SHAP 값을 예측 하려면 특별히 가중치가 적용 된 로컬 선형 회귀를 사용 합니다.
* **설명 모방**: 모방 설명 전역 서로게이트 모델 개념을 기반으로 합니다. 전역 서로게이트 모델은 본질적으로 해석할 수 있는 모델을 최대한 정확 하 게 검정 상자 모델의 예측을 예상 하도록 학습 됩니다. 데이터 과학자는 블랙 박스 모델에 대 한 결론을 그릴 서로게이트 모델을 해석할 수 있습니다.
* **라임 설명** (`contrib`): 라임에 따라 라임 설명 로컬 서로게이트 모델을 만들의 최신 로컬 해석할 수 있는 모델을 알 수 없는 설명 (라임) 알고리즘을 사용 합니다. 전역 서로게이트 모델과 달리 라임 개별 예측을 설명 하기 위해 로컬 서로게이트 모델 학습에 중점을 둡니다.
* **HAN 텍스트 설명** (`contrib`): HAN 텍스트 설명 지정된 블랙 박스 텍스트 모델에 대 한 텍스트 데이터에서 모델 설명을 가져오는 Hierarchical Attention Network를 사용 합니다. 모델을 학습 합니다 HAN 서로게이트 지정된 교사 모델의 예측 된 출력에 있습니다. 텍스트 모음에서 전역적으로 학습, 이후에 설명의 정확도 개선 하기 위해 특정 문서에 대 한 미세 조정 단계를 추가 했습니다. HAN 문장 및 단어 주의 양방향 같은 메시지가 두 주의 레이어를 사용 하 여 사용합니다. DNN 교사 모델에서 학습 된 특정 문서 미세 조정 하는 고 주의 계층에서 word importances 추출 수 것입니다. HAN 시간도 학습 텍스트 데이터에 대 한 라임 또는 SHAP 보다 더 정확 하지만 더 비용이 많이 드는 용어를 발견 했습니다. 그러나 학습 시간이 향상 된 기능을 이지만 여전히 느린 사용자 글러브 단어 포함을 사용 하 여 네트워크를 초기화할 수 있는 옵션을 제공 하 여 했습니다. HAN 원격 Azure GPU VM에서 실행 하 여 학습 시간을 크게 향상 될 수 있습니다. HAN 구현의 ' 문서 분류 (Yang et al, 2016)에 대 한 계층적 주의 네트워크 '에서 설명한 ([https://www.cs.cmu.edu/~diyiy/docs/naacl16.pdf](https://www.cs.cmu.edu/~diyiy/docs/naacl16.pdf)).

__Meta explainers__ 자동으로 적합 한를 직접 설명을 선택 하 고 지정 된 모델 및 데이터 집합에 따라 최상의 설명 정보를 생성 합니다. Meta explainers을 활용 하 여 모든 라이브러리 (SHAP 라임, 모방, 등)를 개발 중이거나 통합 했습니다. 다음은 SDK에서 사용할 수 있는 메타 explainers입니다.

* **테이블 형식 설명**: 테이블 형식 데이터 집합을 사용 합니다.
* **텍스트 설명**: 텍스트 데이터 집합을 사용 합니다.

또한에 메타 선택의 직접 explainers, 메타 explainers 기본 라이브러리를 기반으로 추가 기능을 개발 및 직접 explainers 통해 속도 및 확장성을 개선 합니다.

현재 `TabularExplainer` 직접 Explainers를 호출 하는 다음 논리를 사용 합니다.

1. 트리 기반 모델을 이면 적용 `TreeExplainer`, 그렇지 않으면
2. DNN 모델 인 경우 적용 `DeepExplainer`, 그렇지 않으면
3. 블랙 박스 모델으로 처리 하 고 적용 `KernelExplainer`

에 빌드된 인텔리전스 `TabularExplainer` 더 많은 라이브러리는 SDK를 통합 하 고 각 설명의 장단점에 대해 살펴보고 보다 정교한 됩니다.

`TabularExplainer` 직접 Explainers 통해 중요 한 기능 및 성능 향상 있게 되었습니다.

* **초기화 데이터 집합의 요약**합니다. 여기서 설명 속도가 가장 중요 한 경우에 초기화 데이터 집합을 요약 하 고 전역 및 로컬 설명 속도 작은 대표 샘플 집합을 생성 합니다.
* **평가 데이터 집합을 샘플링**합니다. 사용자 평가 샘플의 큰 집합을 전달 했지만 모든 평가할 실제로 필요 하지 않습니다, 경우 샘플링 매개 변수는 전역 설명은 속도를 true로 설정할 수 있습니다.

다음 다이어그램은 메타 explainers 직접의 두 집합 간의 관계를 보여 줍니다.

[![Machine Learning Interpretability 아키텍처](./media/machine-learning-interpretability-explainability/interpretability-architecture.png)](./media/machine-learning-interpretability-explainability/interpretability-architecture.png#lightbox)

### <a name="models-supported"></a>지원 되는 모델

Python의 데이터 집합에서 학습 된 모델 `numpy.array`, `pandas.DataFrame`, `iml.datatypes.DenseData`, 또는 `scipy.sparse.csr_matrix` 형식으로 interpretability 지 `explain` SDK의 패키지입니다.

입력으로 모델 및 파이프라인을 허용 하는 함수를 설명 합니다. 모델을 제공 하는 경우 모델 예측 함수를 구현 해야 합니다 `predict` 또는 `predict_proba` Scikit 규칙을 준수 합니다. 파이프라인 (파이프라인 스크립트의 이름)를 제공 하는 경우 예측을 반환 하는 실행 중인 파이프라인 스크립트 설명 함수 가정 합니다.

### <a name="local-and-remote-compute-target"></a>로컬 및 원격 계산 대상

`explain` 패키지는 모두 로컬 및 원격 계산 대상으로 작동 하도록 설계 되었습니다. 를 로컬로 실행 하는 경우 SDK 함수는 모든 Azure 서비스 연락 하지 않습니다. Azure Machine Learning Compute에서 설명을 원격으로 실행할 수 있으며 설명 정보를 Azure Machine Learning 실행 기록 서비스에 로그인 할 수 있습니다. 이 정보가 기록 됩니다 보고서 및 설명의 시각화를 쉽게 사용자 분석을 위해 Azure Machine Learning 작업 영역 포털에서 사용할 수 있습니다.

## <a name="interpretability-in-training"></a>Interpretability 교육

### <a name="train-and-explain-locally"></a>학습 하 고 로컬로 설명

1. 로컬 Jupyter notebook에서 모델을 학습 합니다.

    ```python
    # load breast cancer dataset, a well-known small dataset that comes with scikit-learn
    from sklearn.datasets import load_breast_cancer
    from sklearn import svm
    from sklearn.model_selection import train_test_split
    breast_cancer_data = load_breast_cancer()
    classes = breast_cancer_data.target_names.tolist()
    # Split data into train and test
    from sklearn.model_selection import train_test_split
    x_train, x_test, y_train, y_test = train_test_split(breast_cancer_data.data, breast_cancer_data.target, test_size=0.2, random_state=0)
    clf = svm.SVC(gamma=0.001, C=100., probability=True)
    model = clf.fit(x_train, y_train)
    ```

2. 설명을 호출 합니다. 설명 개체를 초기화 하려면 모델 및 일부 학습 데이터 설명의 생성자에 전달 해야 합니다. 또한 필요에 따라 기능 이름 및 출력 클래스 이름 (분류를 수행) 하는 경우 자세한 설명 및 시각화를 쉽게에 사용 되는 전달할 수 있습니다. 사용 하는 설명 개체를 인스턴스화하는 방법은 다음과 같습니다 [TabularExplainer](https://docs.microsoft.com/python/api/azureml-explain-model/azureml.explain.model.tabularexplainer?view=azure-ml-py) 하 고 [MimicExplainer](https://docs.microsoft.com/python/api/azureml-explain-model/azureml.explain.model.mimic.mimicexplainer?view=azure-ml-py) 로컬로 합니다. `TabularExplainer` 아래 세 가지 explainers 중 하나를 호출 하는 (`TreeExplainer`, `DeepExplainer`, 또는 `KernelExplainer`)은 자동으로 가장 적절 한 사용 사례에 대 한 선택 하 고 있습니다. 그러나 각각의 세 가지 기본 explainers의 직접 호출 합니다.

    ```python
    from azureml.explain.model.tabular_explainer import TabularExplainer
    # "features" and "classes" fields are optional
    explainer = TabularExplainer(model, x_train, features=breast_cancer_data.feature_names, classes=classes)
    ```

    또는

    ```python
    from azureml.explain.model.mimic.mimic_explainer import MimicExplainer
    from azureml.explain.model.mimic.models.lightgbm_model import LGBMExplainableModel

    # "features" and "classes" fields are optional
    explainer = MimicExplainer(model, x_train, LGBMExplainableModel, features=breast_cancer_data.feature_names, classes=classes)
    ```

3. 전역 기능 중요도 값을 가져옵니다.

    ```python
    # You can use the training data or the test data here
    global_explanation = explainer.explain_global(x_train)
    # Sorted feature importance values and feature names
    sorted_global_importance_values = global_explanation.get_ranked_global_values()
    sorted_global_importance_names = global_explanation.get_ranked_global_names()
    dict(zip(sorted_global_importance_names, sorted_global_importance_values))
    ```

4. 로컬 기능 중요도 값: 개별 인스턴스 또는 인스턴스 그룹을 설명 하기 위해 다음 함수 호출을 사용 합니다.

    ```python
    # explain the first data point in the test set
    local_explanation = explainer.explain_local(x_test[0])

    # sorted feature importance values and feature names
    sorted_local_importance_names = local_explanation.get_ranked_local_names()
    sorted_local_importance_values = local_explanation.get_ranked_local_values()
    ```

    또는

    ```python
    # explain the first five data points in the test set
    local_explanation = explainer.explain_local(x_test[0:4])

    # sorted feature importance values and feature names
    sorted_local_importance_names = local_explanation.get_ranked_local_names()
    sorted_local_importance_values = local_explanation.get_ranked_local_values()
    ```

### <a name="train-and-explain-remotely"></a>학습 하 고 원격으로 설명

Azure Machine Learning 서비스에서 지 원하는 다양 한 계산 대상에서 학습할 수, 하는 동안이 섹션의 예제는 Azure Machine Learning Compute 대상을 사용 하 여이 수행 하는 방법을 보여 줍니다.

1. 로컬 Jupyter notebook (예를 들어 run_explainer.py)에서 학습 스크립트를 만듭니다.

    ```python
    run = Run.get_context()
    client = ExplanationClient.from_run(run)

    # Train your model here

    # explain predictions on your local machine
    # "features" and "classes" fields are optional
    explainer = TabularExplainer(model, x_train, features=breast_cancer_data.feature_names, classes=classes)
    # explain overall model predictions (global explanation)
    global_explanation = explainer.explain_global(x_test)
    # explain local data points (individual instances)
    local_explanation = explainer.explain_local(x_test[0])
    # upload global and local explanation objects to Run History
    client.upload_model_explanation(run, local_explanation, top_k=2, comment='local explanation: top 2 features')
    # Uploading global model explanation data for storage or visualization in webUX
    # The explanation can then be downloaded on any compute
    # Multiple explanations can be uploaded
    client.upload_model_explanation(global_explanation, comment='global explanation: all features')
    # Or you can only upload the explanation object with the top k feature info
    #client.upload_model_explanation(global_explanation, top_k=2, comment='global explanation: Only top 2 features')
    ```

2. 지침에 따라 [모델 학습을 위한 계산 대상 설정](how-to-set-up-training-targets.md#amlcompute) 하는 Azure Machine Learning Compute 계산 대상으로 설정 하 고 학습 실행을 제출 하는 방법을 알아봅니다.

3. 로컬 Jupyter notebook에 설명이 다운로드 합니다.

    ```python
    from azureml.contrib.explain.model.explanation.explanation_client import ExplanationClient
    # Get model explanation data
    client = ExplanationClient.from_run(run)
    explanation = client.download_model_explanation()
    local_importance_values = explanation.local_importance_values
    expected_values = explanation.expected_values
    # Or you can use the saved run.id to retrive the feature importance values
    client = ExplanationClient.from_run_id(ws, experiment_name, run.id)
    explanation = client.download_model_explanation()
    local_importance_values = explanation.local_importance_values
    expected_values = explanation.expected_values
    # Get the top k (e.g., 4) most important features with their importance values
    explanation = client.download_model_explanation(top_k=4)
    global_importance_values = explanation.get_ranked_global_values()
    global_importance_names = explanation.get_ranked_global_names()
    print('global importance values: {}'.format(global_importance_values))
    print('global importance names: {}'.format(global_importance_names))
    ```

## <a name="visualizations"></a>시각화

시각화 대시보드를 사용 하 여 이해 하 고 모델을 해석 합니다.

### <a name="global-visualizations"></a>전역 시각화

다음 그림에는 예측 및 설명을 함께 학습된 된 모델의 전역 뷰를 제공합니다.

|그림|설명|
|----|-----------|
|데이터 탐색| 예측 값과 함께 데이터 집합을 간략히 설명 합니다.|
|전역 중요도|Top K (구성 가능한 K) 중요 한 기능을 전체적으로 보여 줍니다. 이 차트는 기본 모델의 전역 동작을 이해 하는 데 유용 합니다.|
|설명은 탐색|기능은 모델의 예측 값 (또는 예측 값의 확률)을 변경 하는 일을 담당 하는 방법을 보여 줍니다. |
|요약| 모든 데이터 요소는 서명 된 로컬 기능 중요도 값을 사용 하 여 예측 값에 각 기능에 미치는 영향의 분포를 표시 합니다.|

[![전역 시각화 대시보드](./media/machine-learning-interpretability-explainability/global-charts.png)](./media/machine-learning-interpretability-explainability/global-charts.png#lightbox)

### <a name="local-visualizations"></a>로컬 시각화

언제 든 지 지정 된 데이터 요소에 대 한 로컬 기능 중요도 플롯을 로드 하려면 이전 그림의 개별 데이터 요소를 클릭할 수 있습니다.

|그림|설명|
|----|-----------|
|로컬 중요도|Top K (구성 가능한 K) 중요 한 기능을 전체적으로 보여 줍니다. 이 차트는 특정 데이터 요소에 대해 기본 모델의 로컬 동작을 이해 하는 데 유용 합니다.|

[![시각화 대시보드 로컬](./media/machine-learning-interpretability-explainability/local-charts.png)](./media/machine-learning-interpretability-explainability/local-charts.png#lightbox)

시각화 대시보드를 로드 하려면 다음 코드를 사용 합니다.

```python
from azureml.contrib.explain.model.visualize import ExplanationDashboard

ExplanationDashboard(global_explanation, model, x_test)
```

## <a name="raw-feature-transformations"></a>원시 기능 변환

필요에 따라 기능 변환 파이프라인은 변환 (기능 보다는 엔지니어링 된) 하기 전에 원시 기능 측면에서 설명을 받기 위해 설명에 전달할 수 있습니다. 이 작업을 건너뛸 경우를 설명 엔지니어링 된 기능 측면에서 설명을 제공 합니다.

지원 되는 변환의 형식은 동일한 1에 설명 된 대로 [sklearn pandas](https://github.com/scikit-learn-contrib/sklearn-pandas)합니다. 일반적으로 단일 열에서 작동 하며 따라서는 명확 하 게 다 있다면 모든 변환이 지원 됩니다.

```python
from sklearn.pipeline import Pipeline
from sklearn.impute import SimpleImputer
from sklearn.preprocessing import StandardScaler, OneHotEncoder
from sklearn.linear_model import LogisticRegression
from sklearn_pandas import DataFrameMapper

# Assume that we have created two arrays, numerical and categorical, which holds the numerical and categorical feature names

numeric_transformations = [([f], Pipeline(steps=[('imputer', SimpleImputer(strategy='median')), ('scaler', StandardScaler())])) for f in numerical]

categorical_transformations = [([f], OneHotEncoder(handle_unknown='ignore', sparse=False)) for f in categorical]

transformations = numeric_transformations + categorical_transformations

# Append model to preprocessing pipeline.
# Now we have a full prediction pipeline.
clf = Pipeline(steps=[('preprocessor', DataFrameMapper(transformations)),
                    ('classifier', LogisticRegression(solver='lbfgs'))])

# clf.steps[-1][1] returns the trained classification model
# Pass transformation as an input to create the explanation object
# "features" and "classes" fields are optional
tabular_explainer = TabularExplainer(clf.steps[-1][1], initialization_examples=x_train, features=dataset_feature_names, classes=dataset_classes, transformations=transformations)
```

## <a name="interpretability-in-inference"></a>유추에서 interpretability

설명 원래 모델과 함께 배포할 수 있으며 로컬 설명 정보를 제공 하는 시간을 점수 매기기에 사용할 수 있습니다. 점수 매기기 설명을 배포 하는 프로세스 모델을 배포 하는 것과 비슷합니다 및 다음 단계가 포함 됩니다.

1. 설명 개체를 만듭니다.

   ```python
   from azureml.contrib.explain.model.tabular_explainer import TabularExplainer

   explainer = TabularExplainer(model, x_test)
   ```

1. 설명 개체를 사용 하는 점수 매기기 설명을 만듭니다.

   ```python
   scoring_explainer = explainer.create_scoring_explainer(x_test)

   # Pickle scoring explainer
   scoring_explainer_path = scoring_explainer.save('scoring_explainer_deploy')
   ```

1. 구성 하 고 점수 매기기 설명 모델을 사용 하는 이미지를 등록 합니다.

   ```python
   # Register explainer model using the path from ScoringExplainer.save - could be done on remote compute
   run.upload_file('breast_cancer_scoring_explainer.pkl', scoring_explainer_path)
   model = run.register_model(model_name='breast_cancer_scoring_explainer', model_path='breast_cancer_scoring_explainer.pkl')
   print(model.name, model.id, model.version, sep = '\t')
   ```

1. [선택 사항] 점수 매기기 설명 클라우드에서 검색 하 고 설명을 테스트합니다

   ```python
   from azureml.contrib.explain.model.scoring.scoring_explainer import ScoringExplainer

   # Retrieve the scoring explainer model from cloud"
   scoring_explainer_model = Model(ws, 'breast_cancer_scoring_explainer')
   scoring_explainer_model_path = scoring_explainer_model.download(target_dir=os.getcwd(), exist_ok=True)

   # Load scoring explainer from disk
   scoring_explainer = ScoringExplainer.load(scoring_explainer_model_path)

   # Test scoring explainer locally
   preds = scoring_explainer.explain(x_test)
   print(preds)
   ```

1. 계산 대상에 이미지를 배포 합니다.

   1. 점수 매기기 파일을 만듭니다 (이 단계 전에 단계를 따릅니다 [Azure Machine Learning 서비스를 사용 하 여 모델 배포](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-and-where) 원래 예측 모델을 등록 하려면)

        ```python
        %%writefile score.py
        import json
        import numpy as np
        import os
        import pickle
        from sklearn.externals import joblib
        from sklearn.linear_model import LogisticRegression
        from azureml.core.model import Model

        def init():

            global original_model
            global scoring_model

            # Retrieve the path to the model file using the model name
            # Assume original model is named original_prediction_model
            original_model_path = Model.get_model_path('original_prediction_model')
            scoring_explainer_path = Model.get_model_path('breast_cancer_scoring_explainer')

            original_model = joblib.load(original_model_path)
            scoring_explainer = joblib.load(scoring_explainer_path)

        def run(raw_data):
            # Get predictions and explanations for each data point
            data = np.array(json.loads(raw_data)['data'])
            # Make prediction
            predictions = original_model.predict(data)
            # Retrieve model explanations
            local_importance_values = scoring_explainer.explain(data)
            # You can return any data type as long as it is JSON-serializable
            return {'predictions': predictions.tolist(), 'local_importance_values': local_importance_values}
        ```

   1. (이 구성 모델의 요구 사항에 따라 다릅니다 배포 구성 정의. 다음 예제에서는 하나의 CPU 코어 및 1GB의 메모리를 사용 하는 구성을 정의)

        ```python
        from azureml.core.webservice import AciWebservice

        aciconfig = AciWebservice.deploy_configuration(cpu_cores=1,
                                                       memory_gb=1,
                                                       tags={"data": "breastcancer",
                                                             "method" : "local_explanation"},
                                                       description='Get local explanations for breast cancer data')
        ```

   1. 환경 종속성을 사용 하 여 파일 만들기

        ```python
        from azureml.core.conda_dependencies import CondaDependencies

        # WARNING: to install this, g++ needs to be available on the Docker image and is not by default (look at the next cell)


        myenv = CondaDependencies.create(pip_packages=["azureml-defaults", "azureml-explain-model", "azureml-contrib-explain-model"],
                                        conda_packages=["scikit-learn"])

        with open("myenv.yml","w") as f:
            f.write(myenv.serialize_to_string())

        with open("myenv.yml","r") as f:
            print(f.read())
        ```

   1. 사용자 지정 dockerfile에 g + + 설치를 사용 하 여 만들기

        ```python
        %%writefile dockerfile
        RUN apt-get update && apt-get install -y g++
        ```

   1. 만든된 이미지를 배포 (예상 시간: 5 분)

        ```python
        from azureml.core.webservice import Webservice
        from azureml.core.image import ContainerImage

        # Use the custom scoring, docker, and conda files we created above
        image_config = ContainerImage.image_configuration(execution_script="score.py",
                                                        docker_file="dockerfile",
                                                        runtime="python",
                                                        conda_file="myenv.yml")

        # Use configs and models generated above
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

    # Create data to test service with
    x_list = x_test.tolist()
    examples = x_list[:4]
    input_data = "{\"data\": " + str(examples) + "}"

    headers = {'Content-Type':'application/json'}

    # send request to service
    resp = requests.post(service.scoring_uri, input_data, headers=headers)

    print("POST to url", service.scoring_uri)
    # can covert back to Python objects from json string if desired
    print("prediction:", resp.text)
    ```

1. 정리 합니다. 배포된 웹 서비스를 삭제하려면 `service.delete()`를 사용합니다.

## <a name="interpretability-in-automated-ml"></a>자동화 된 기계 학습에서 interpretability

자동화 된 machine learning 자동 학습 모델의 기능 중요도 해석 하는 것에 대 한 패키지를 포함 합니다. 또한 분류 시나리오를 수행할 수 클래스 수준 기능 중요도 검색할 수 있습니다. 자동화 된 machine learning 내에서이 동작을 사용 하도록 설정 하는 방법은 두 가지가 있습니다.

* 앙상블 학습 된 모델의 기능 중요도 사용 하도록 설정 하려면 사용 합니다 [ `explain_model()` ](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlexplainer?view=azure-ml-py) 함수입니다.

    ```python
    from azureml.train.automl.automlexplainer import explain_model

    shap_values, expected_values, overall_summary, overall_imp, \
        per_class_summary, per_class_imp = explain_model(fitted_model, X_train, X_test)
    ```

* 학습 하기 전에 각 개별 실행의 기능 중요도 사용 하도록 설정 하려면 설정를 `model_explainability` 매개 변수를 `True` 에 `AutoMLConfig` 개체, 유효성 검사 데이터를 제공 합니다. 그런 다음 사용 합니다 [ `retrieve_model_explanation()` ](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlexplainer?view=azure-ml-py) 함수입니다.

    ```python
    from azureml.train.automl.automlexplainer import retrieve_model_explanation

    shap_values, expected_values, overall_summary, overall_imp, per_class_summary, \
        per_class_imp = retrieve_model_explanation(best_run)
    ```

자세한 내용은 참조는 [방법](how-to-configure-auto-train.md#explain-the-model-interpretability) 자동화 된 machine learning에서 interpretability 기능을 사용 하도록 설정 합니다.

## <a name="next-steps"></a>다음 단계

위의 지침을 보여주는 Jupyter notebook의 컬렉션을 참조 합니다 [Azure Machine Learning Interpretability 샘플 노트북이](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model)합니다.
