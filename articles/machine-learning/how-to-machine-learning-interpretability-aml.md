---
title: Python에서 ML 모델 설명 & 해석 (미리 보기)
titleSuffix: Azure Machine Learning
description: 기계 학습 모델이 기능 중요도를 결정 하 고 Azure Machine Learning SDK를 사용 하는 경우 예측을 수행 하는 방법에 대 한 설명을 가져오는 방법에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: mithigpe
author: minthigpen
ms.reviewer: Luis.Quintanilla
ms.date: 07/09/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, responsible-ml
ms.openlocfilehash: fda1bc2ef0a112a8a32ba7c4caebf29028c8cdd7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98222754"
---
# <a name="use-the-interpretability-package-to-explain-ml-models--predictions-in-python-preview"></a>Interpretability 패키지를 사용 하 여 Python의 ML 모델 & 예측 (미리 보기)을 설명 합니다.



이 방법 가이드에서는 Azure Machine Learning Python SDK의 interpretability 패키지를 사용 하 여 다음 작업을 수행 하는 방법을 알아봅니다.


* 로컬에서 개인 컴퓨터의 전체 모델 동작 또는 개별 예측을 설명 합니다.

* Interpretability 기술을 사용 하 여 엔지니어링 된 기능을 지원 합니다.

* Azure에서 전체 모델 및 개별 예측의 동작을 설명 합니다.

* 시각화 대시보드를 사용 하 여 모델 설명과 상호 작용할 수 있습니다.

* 모델에 설명 점수 매기기를 배포 하 여 추론 중에 설명을 관찰 합니다.



지원 되는 interpretability 기술 및 기계 학습 모델에 대 한 자세한 내용은 [모델 interpretability의 Azure Machine Learning](how-to-machine-learning-interpretability.md) 및 [샘플 노트북](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model)을 참조 하세요.

## <a name="generate-feature-importance-value-on-your-personal-machine"></a>개인용 컴퓨터에서 기능 중요도 값 생성 
다음 예제에서는 Azure 서비스에 연결 하지 않고 개인 컴퓨터에서 interpretability 패키지를 사용 하는 방법을 보여 줍니다.

1. `azureml-interpret` 패키지를 설치합니다.
    ```bash
    pip install azureml-interpret
    ```

2. 로컬 Jupyter Notebook에서 샘플 모델을 학습 합니다.

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

3. 설명를 로컬로 호출 합니다.
   * 설명 개체를 초기화 하려면 모델 및 일부 학습 데이터를 설명의 생성자에 전달 합니다.
   * 설명 및 시각화에 대 한 자세한 정보를 제공 하기 위해 분류를 수행 하는 경우 기능 이름 및 출력 클래스 이름을 전달 하도록 선택할 수 있습니다.

   다음 코드 블록은, 및을 로컬로 사용 하 여 설명 개체를 인스턴스화하는 방법을 보여 줍니다 `TabularExplainer` `MimicExplainer` `PFIExplainer` .
   * `TabularExplainer` 는 (, 또는) 아래에 있는 세 개의 SHAP explainers 중 하나를 호출 `TreeExplainer` `DeepExplainer` `KernelExplainer` 합니다.
   * `TabularExplainer` 는 사용 사례에 가장 적합 한 항목을 자동으로 선택 하지만 세 가지 기본 explainers를 직접 호출할 수 있습니다.

    ```python
    from interpret.ext.blackbox import TabularExplainer

    # "features" and "classes" fields are optional
    explainer = TabularExplainer(model, 
                                 x_train, 
                                 features=breast_cancer_data.feature_names, 
                                 classes=classes)
    ```

    또는

    ```python

    from interpret.ext.blackbox import MimicExplainer
    
    # you can use one of the following four interpretable models as a global surrogate to the black box model
    
    from interpret.ext.glassbox import LGBMExplainableModel
    from interpret.ext.glassbox import LinearExplainableModel
    from interpret.ext.glassbox import SGDExplainableModel
    from interpret.ext.glassbox import DecisionTreeExplainableModel

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

    또는

    ```python
    from interpret.ext.blackbox import PFIExplainer

    # "features" and "classes" fields are optional
    explainer = PFIExplainer(model,
                             features=breast_cancer_data.feature_names, 
                             classes=classes)
    ```

### <a name="explain-the-entire-model-behavior-global-explanation"></a>전체 모델 동작 설명 (전역 설명) 

집계 (전역) 기능 중요도 값을 가져오는 데 도움이 되는 다음 예를 참조 하세요.

```python

# you can use the training data or the test data here, but test data would allow you to use Explanation Exploration
global_explanation = explainer.explain_global(x_test)

# if you used the PFIExplainer in the previous step, use the next line of code instead
# global_explanation = explainer.explain_global(x_train, true_labels=y_train)

# sorted feature importance values and feature names
sorted_global_importance_values = global_explanation.get_ranked_global_values()
sorted_global_importance_names = global_explanation.get_ranked_global_names()
dict(zip(sorted_global_importance_names, sorted_global_importance_values))

# alternatively, you can print out a dictionary that holds the top K feature names and values
global_explanation.get_feature_importance_dict()
```

### <a name="explain-an-individual-prediction-local-explanation"></a>개별 예측 설명 (로컬 설명)
개별 인스턴스 또는 인스턴스 그룹에 대 한 설명을 호출 하 여 서로 다른 datapoints의 개별 기능 중요도 값을 가져옵니다.
> [!NOTE]
> `PFIExplainer` 는 로컬 설명을 지원 하지 않습니다.

```python
# get explanation for the first data point in the test set
local_explanation = explainer.explain_local(x_test[0:5])

# sorted feature importance values and feature names
sorted_local_importance_names = local_explanation.get_ranked_local_names()
sorted_local_importance_values = local_explanation.get_ranked_local_values()
```

### <a name="raw-feature-transformations"></a>원시 기능 변환

엔지니어링 된 기능이 아니라 원시 레이크의 기능을 사용 하 여 설명을 볼 수 있습니다. 이 옵션의 경우 기능 변환 파이프라인을 설명에 전달 `train_explain.py` 합니다. 그렇지 않으면 설명는 엔지니어링 된 기능 측면에서 설명을 제공 합니다.

지원 되는 변환 형식은 [pandas](https://github.com/scikit-learn-contrib/sklearn-pandas)에서 설명한 것과 같습니다. 일반적으로 모든 변환은 단일 열에 대해 작동 하는 한, 즉 일대다를 명확 하 게 수행 하는 동안 지원 됩니다.

또는에 적합 한 `sklearn.compose.ColumnTransformer` 변환기 튜플 목록과 함께 또는를 사용 하 여 원시 기능에 대 한 설명을 가져옵니다. 다음 예에서는를 사용 `sklearn.compose.ColumnTransformer` 합니다.

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

## <a name="generate-feature-importance-values-via-remote-runs"></a>원격 실행을 통해 기능 중요도 값 생성

다음 예제에서는 클래스를 사용 하 여 `ExplanationClient` 원격 실행에 대해 모델 interpretability를 사용 하도록 설정 하는 방법을 보여 줍니다. 다음을 제외 하 고는 로컬 프로세스와 개념적으로 유사 합니다.

* `ExplanationClient`원격 실행에서를 사용 하 여 interpretability 컨텍스트를 업로드 합니다.
* 로컬 환경에서 나중에 컨텍스트를 다운로드 합니다.

1. `azureml-interpret` 패키지를 설치합니다.
    ```bash
    pip install azureml-interpret
    ```
1. 로컬 Jupyter Notebook에 학습 스크립트를 만듭니다. 예: `train_explain.py`

    ```python
    from azureml.interpret import ExplanationClient
    from azureml.core.run import Run
    from interpret.ext.blackbox import TabularExplainer

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

1. 계산 대상으로 Azure Machine Learning 계산을 설정 하 고 학습 실행을 제출 합니다. 지침은 [계산 클러스터 Azure Machine Learning 만들기 및 관리](how-to-create-attach-compute-cluster.md) 를 참조 하세요. 또한 [예제 노트북이](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model/azure-integration/remote-explanation) 도움이 될 것입니다.

1. 로컬 Jupyter Notebook에서 설명을 다운로드 합니다.

    ```python
    from azureml.interpret import ExplanationClient
    
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

로컬 Jupyter Notebook에서 설명을 다운로드 한 후 시각화 대시보드를 사용 하 여 모델을 이해 하 고 해석할 수 있습니다. Jupyter Notebook에서 시각화 대시보드 위젯을 로드 하려면 다음 코드를 사용 합니다.

```python
from interpret_community.widget import ExplanationDashboard

ExplanationDashboard(global_explanation, model, datasetX=x_test)
```

시각화는 엔지니어링 된 기능과 원시 기능 모두에 대 한 설명을 지원 합니다. 원시 설명은 원래 데이터 집합의 기능을 기반으로 하며, 엔지니어링 된 설명은 기능 엔지니어링이 적용 된 데이터 집합의 기능을 기반으로 합니다.

원래 데이터 집합에 대해 모델을 해석 하려고 할 때 각 기능 중요도가 원래 데이터 집합의 열에 해당 하므로 원시 설명을 사용 하는 것이 좋습니다. 엔지니어링 된 설명이 유용할 수 있는 한 가지 시나리오는 범주별 기능에서 개별 범주의 영향을 검사할 때입니다. 하나 핫 인코딩이 범주 기능에 적용 되는 경우 엔지니어링 된 결과에는 한 번에 하나씩 실행 되는 기능별로 다른 중요도 값이 포함 됩니다. 모델에 가장 많은 정보를 제공 하는 데이터 집합의 일부를 축소 하는 경우에 유용할 수 있습니다.

> [!NOTE]
> 엔지니어링 된 및 원시 설명은 순차적으로 계산 됩니다. 먼저 모델 및 기능화 파이프라인을 기반으로 엔지니어링 된 설명이 생성 됩니다. 그런 다음 동일한 원시 기능에서 제공 된 엔지니어링 된 기능의 중요도를 집계 하 여 해당 엔지니어링 설명에 따라 원시 설명이 생성 됩니다.

### <a name="create-edit-and-view-dataset-cohorts"></a>데이터 집합 만들기, 편집 및 보기 코 호트

위쪽 리본에는 모델 및 데이터에 대 한 전반적인 통계가 표시 됩니다. 데이터 집합 코 호트 또는 하위 그룹으로 데이터를 조각화 및 분석 하 여 이러한 정의 된 하위 그룹에서 모델의 성능과 설명을 조사 하거나 비교할 수 있습니다. 이러한 하위 그룹에 대 한 데이터 집합 통계와 설명을 비교 하면 한 그룹에서 다른 그룹으로 발생 가능한 오류가 발생 하는 이유를 파악할 수 있습니다.

[![데이터 집합 생성, 편집 및 보기 코 호트](./media/how-to-machine-learning-interpretability-aml/dataset-cohorts.gif)](./media/how-to-machine-learning-interpretability-aml/dataset-cohorts.gif#lightbox)

### <a name="understand-entire-model-behavior-global-explanation"></a>전체 모델 동작 이해 (전역 설명) 

설명 대시보드의 처음 세 탭은 예측 및 설명과 함께 학습 된 모델에 대 한 전체 분석을 제공 합니다.

#### <a name="model-performance"></a>모델 성능
예측 값의 분포와 모델 성능 메트릭의 값을 탐색 하 여 모델의 성능을 평가 합니다. 데이터 집합의 다양 한 코 호트 또는 하위 그룹에서의 성능에 대 한 비교 분석을 살펴보면 모델을 자세히 조사할 수 있습니다. Y 값 및 x 값을 따라 필터를 선택 하 여 여러 차원에서 자릅니다. 정확도, 전체 자릿수, 회수, 거짓 긍정 요금 (FPR) 및 가양성 (FNR)과 같은 메트릭을 표시 합니다.

[![설명 시각화의 모델 성능 탭](./media/how-to-machine-learning-interpretability-aml/model-performance.gif)](./media/how-to-machine-learning-interpretability-aml/model-performance.gif#lightbox)

#### <a name="dataset-explorer"></a>데이터 집합 탐색기
X, Y 및 색 축을 따라 다른 필터를 선택 하 여 데이터 집합 통계를 탐색 하 여 다양 한 차원에 따라 데이터를 조각화 합니다. 예측 결과, 데이터 집합 기능 및 오류 그룹과 같은 필터를 사용 하 여 데이터 집합 통계를 분석 하기 위해 위의 코 호트 데이터 집합을 만듭니다. 그래프의 오른쪽 위 모퉁이에 있는 기어 아이콘을 사용 하 여 그래프 유형을 변경 합니다.

[![설명 시각화의 데이터 집합 탐색기 탭](./media/how-to-machine-learning-interpretability-aml/dataset-explorer.gif)](./media/how-to-machine-learning-interpretability-aml/dataset-explorer.gif#lightbox)

#### <a name="aggregate-feature-importance"></a>집계 기능 중요도
전반적인 모델 예측에 영향을 주는 중요 한 기능 (전역 설명이 라고도 함)에 대해 살펴봅니다. 슬라이더를 사용 하 여 내림차순 기능 중요도 값을 표시 합니다. 최대 3 개의 코 호트를 선택 하 여 기능 중요도 값을 나란히 표시 합니다. 그래프에서 기능 막대를 클릭 하 여 아래 종속성 플롯에서 선택한 기능의 값이 모델 예측에 미치는 영향을 확인 합니다.

[![설명 시각화의 집계 기능 중요도 탭](./media/how-to-machine-learning-interpretability-aml/aggregate-feature-importance.gif)](./media/how-to-machine-learning-interpretability-aml/aggregate-feature-importance.gif#lightbox)

### <a name="understand-individual-predictions-local-explanation"></a>개별 예측 이해 (로컬 설명) 

설명 탭의 네 번째 탭에서는 개별 요소 및 개별 기능 importances를 자세히 살펴볼 수 있습니다. 주 산 점도의 개별 데이터 요소를 클릭 하거나 오른쪽의 패널 마법사에서 특정 데이터 요소를 선택 하 여 모든 데이터 요소에 대 한 개별 기능 중요도 그림을 로드할 수 있습니다.

|그림|설명|
|----|-----------|
|개별 기능 중요도|개별 예측의 최상위 중요 기능을 보여 줍니다. 특정 데이터 요소에 대 한 기본 모델의 로컬 동작을 보여 줍니다.|
|What-If 분석|선택한 실제 데이터 요소의 기능 값에 대 한 변경 내용을 허용 하 고 새 기능 값을 사용 하 여 가상 데이터 포인트를 생성 하 여 예측 값에 대 한 변경 결과를 관찰 합니다.|
|개별 조건부 예상 (ICE)|최소값에서 최대값으로 기능 값을 변경할 수 있습니다. 기능이 변경 될 때 데이터 요소의 예측이 어떻게 변경 되는지를 보여 줍니다.|

[![설명 대시보드의 개별 기능 중요도 및 가상-구성 탭](./media/how-to-machine-learning-interpretability-aml/individual-tab.gif)](./media/how-to-machine-learning-interpretability-aml/individual-tab.gif#lightbox)

> [!NOTE]
> 이러한 설명은 많은 근사치을 기반으로 하며 예측의 "원인"은 아닙니다. 현실 유추의 엄격한 수학적 견고성을 사용 하지 않으면 사용자에 게 What-If 도구의 기능 perturbations을 기반으로 실질적인 결정을 내리는 것을 권장 하지 않습니다. 이 도구는 주로 모델과 디버깅을 이해 하는 데 사용할 수 있습니다.

### <a name="visualization-in-azure-machine-learning-studio"></a>Azure Machine Learning studio의 시각화

Azure Machine Learning 실행 기록에 생성 된 설명 업로드) [원격 interpretability](how-to-machine-learning-interpretability-aml.md#generate-feature-importance-values-via-remote-runs) 단계를 완료 한 경우 [Azure Machine Learning studio](https://ml.azure.com)에서 시각화 대시보드를 볼 수 있습니다. 이 대시보드는 위에서 설명한 시각화 대시보드의 더 간단한 버전입니다. Azure Machine Learning studio에는 실시간 계산을 수행할 수 있는 활성 계산이 없으므로 What-If 포인트 생성 및 ICE 플롯은 사용 하지 않도록 설정 됩니다.

데이터 집합, 전역 및 로컬 설명을 사용할 수 있는 경우 데이터는 모든 탭을 채웁니다. 글로벌 설명만 사용할 수 있는 경우에는 개별 기능 중요도 탭을 사용할 수 없습니다.

다음 경로 중 하나를 따라 Azure Machine Learning studio에서 시각화 대시보드에 액세스 합니다.

* **실험** 창 (미리 보기)
  1. 왼쪽 창에서 **실험** 을 선택 하 여 Azure Machine Learning에서 실행 한 실험의 목록을 확인 합니다.
  1. 특정 실험을 선택 하 여 해당 실험의 모든 실행을 볼 수 있습니다.
  1. 실행을 선택한 다음 설명 시각화 대시보드에 **설명 탭을 선택 합니다.**

   [![실험의 AzureML 스튜디오에서 집계 기능 중요도가 있는 시각화 대시보드](./media/how-to-machine-learning-interpretability-aml/model-explanation-dashboard-aml-studio.png)](./media/how-to-machine-learning-interpretability-aml/model-explanation-dashboard-aml-studio.png#lightbox)

* **모델** 창
  1. [Azure Machine Learning를 사용 하 여 모델 배포](./how-to-deploy-and-where.md)의 단계를 수행 하 여 원래 모델을 등록 한 경우 왼쪽 창에서 **모델** 을 선택 하 여 볼 수 있습니다.
  1. 모델을 선택한 다음 **설명 탭을 클릭 하 여 설명** 시각화 대시보드를 확인 합니다.

## <a name="interpretability-at-inference-time"></a>유추 시 Interpretability

원본 모델과 함께 설명를 배포 하 고 유추 시이를 사용 하 여 새 요소에 대해 개별 기능 중요도 값 (로컬 설명)을 제공할 수 있습니다. 또한 현재 Azure Machine Learning SDK 에서만 지원 되는 유추 시 interpretability 성능을 향상 시키기 위해 가벼운 점수 매기기 explainers 제공 합니다. 가벼운 점수 매기기 설명를 배포 하는 프로세스는 모델 배포와 유사 하며 다음 단계를 포함 합니다.

1. 설명 개체를 만듭니다. 예를 들어 다음을 사용할 수 있습니다 `TabularExplainer` .

   ```python
    from interpret.ext.blackbox import TabularExplainer


   explainer = TabularExplainer(model, 
                                initialization_examples=x_train, 
                                features=dataset_feature_names, 
                                classes=dataset_classes, 
                                transformations=transformations)
   ```

1. 설명 개체를 사용 하 여 점수 매기기 설명을 만듭니다.

   ```python
   from azureml.interpret.scoring.scoring_explainer import KernelScoringExplainer, save

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

1. 선택적 단계로 클라우드에서 설명 점수 매기기를 검색 하 고 설명을 테스트할 수 있습니다.

   ```python
   from azureml.interpret.scoring.scoring_explainer import load

   # retrieve the scoring explainer model from cloud"
   scoring_explainer_model = Model(ws, 'my_scoring_explainer')
   scoring_explainer_model_path = scoring_explainer_model.download(target_dir=os.getcwd(), exist_ok=True)

   # load scoring explainer from disk
   scoring_explainer = load(scoring_explainer_model_path)

   # test scoring explainer locally
   preds = scoring_explainer.explain(x_test)
   print(preds)
   ```

1. 다음 단계를 수행 하 여 계산 대상에 이미지를 배포 합니다.

   1. 필요한 경우 [Azure Machine Learning를 사용 하 여 모델 배포](./how-to-deploy-and-where.md)의 단계를 수행 하 여 원래 예측 모델을 등록 합니다.

   1. 점수 매기기 파일을 만듭니다.

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
   1. 배포 구성을 정의합니다.

         이 구성은 모델 요구 사항에 따라 달라 집니다. 다음 예제에서는 하나의 CPU 코어 및 1GB의 메모리를 사용 하는 구성을 정의 합니다.

         ```python
         from azureml.core.webservice import AciWebservice

          aciconfig = AciWebservice.deploy_configuration(cpu_cores=1,
                                                    memory_gb=1,
                                                    tags={"data": "NAME_OF_THE_DATASET",
                                                          "method" : "local_explanation"},
                                                    description='Get local explanations for NAME_OF_THE_PROBLEM')
         ```

   1. 환경 종속성이 있는 파일을 만듭니다.

         ```python
         from azureml.core.conda_dependencies import CondaDependencies

         # WARNING: to install this, g++ needs to be available on the Docker image and is not by default (look at the next cell)

         azureml_pip_packages = ['azureml-defaults', 'azureml-core', 'azureml-telemetry', 'azureml-interpret']
 

         # specify CondaDependencies obj
         myenv = CondaDependencies.create(conda_packages=['scikit-learn', 'pandas'],
                                          pip_packages=['sklearn-pandas'] + azureml_pip_packages,
                                          pin_sdk_version=False)


         with open("myenv.yml","w") as f:
            f.write(myenv.serialize_to_string())

         with open("myenv.yml","r") as f:
            print(f.read())
         ```

   1. G + +가 설치 된 사용자 지정 dockerfile을 만듭니다.

         ```python
         %%writefile dockerfile
         RUN apt-get update && apt-get install -y g++
         ```

   1. 만든 이미지를 배포 합니다.
   
         이 프로세스는 5 분 정도 걸립니다.

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

1. 배포를 테스트합니다.

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

1. 정리합니다.

   배포된 웹 서비스를 삭제하려면 `service.delete()`를 사용합니다.

## <a name="troubleshooting"></a>문제 해결

* **스파스 데이터는 지원 되지 않습니다**. 모델 설명 대시보드는 많은 기능을 사용 하 여 상당히 중단/속도를 저하 하므로 현재 스파스 데이터 형식을 지원 하지 않습니다. 또한 일반적인 메모리 문제는 대량 데이터 집합 및 많은 기능에 발생 합니다. 

* **모델 설명에서 지원 되지 않는 예측 모델**: Interpretability, 최상의 모델 설명은 TCNForecaster, AutoArima, Prophet, ExponentialSmoothing, Average, Naive, 계절 평균 및 계절 Naive 알고리즘을 권장 하는 자동 ml 예측 실험에 사용할 수 없습니다. AutoML 예측에는 설명을 지 원하는 회귀 모델이 있습니다. 그러나 설명 대시보드에서는 데이터 파이프라인의 복잡성 때문에 "개별 기능 중요도" 탭이 예측에 대해 지원 되지 않습니다.

* **데이터 인덱스에 대 한 로컬 설명**: 대시보드가 데이터를 임의로 downsamples datapoints이 데이터 집합이 5000 보다 큰 경우 설명 대시보드가 원래 유효성 검사 데이터 집합의 행 식별자에 대 한 로컬 중요도 값 관련을 지원 하지 않습니다. 그러나 대시보드는 개별 기능 중요도 탭에서 대시보드로 전달 되는 각 데이터 집합에 대 한 원시 데이터 집합 기능 값을 보여 줍니다. 사용자는 원시 데이터 집합 기능 값과 일치 하 여 로컬 importances를 원래 데이터 집합에 다시 매핑할 수 있습니다. 유효성 검사 데이터 집합 크기가 5000 샘플 보다 작은 경우 `index` AzureML 스튜디오의 기능은 유효성 검사 데이터 집합의 인덱스에 해당 합니다.

* **스튜디오에서 지원 되지 않는 것이 무엇 인지** 확인 합니다. perturbed 기능의 예측 및 확률을 다시 계산 하기 위해 업로드 된 설명에 활성 계산이 필요 하므로 설명 탭의 Azure Machine Learning studio에서 What-If 및 개별 조건부 예상 (ice) 플롯은 지원 되지 않습니다. 현재는 SDK를 사용 하 여 위젯에 실행 될 때 Jupyter 노트북에서 지원 됩니다.


## <a name="next-steps"></a>다음 단계

[모델 interpretability에 대 한 자세한 정보](how-to-machine-learning-interpretability.md)

[Azure Machine Learning Interpretability 샘플 노트북 확인](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model)
