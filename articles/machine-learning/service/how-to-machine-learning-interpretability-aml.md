---
title: 로컬 및 원격 실행에 대 한 모델 interpretability
titleSuffix: Azure Machine Learning
description: Azure Machine Learning SDK를 사용 하 여 모델이 예측을 수행 하는 이유를 설명 하는 방법을 알아봅니다. 학습 및 유추 중에 모델에서 기능 중요도를 결정 하 고 예측을 수행 하는 방법을 이해 하는 데 사용할 수 있습니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.reviewer: trbye
ms.date: 10/25/2019
ms.openlocfilehash: a2b71a10606b7cd20f06b2497515b758426833a9
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73515305"
---
# <a name="model-interpretability-for-local-and-remote-runs"></a>로컬 및 원격 실행에 대 한 모델 interpretability

[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

이 문서에서는 Azure Machine Learning Python SDK의 interpretability 패키지를 사용 하 여 모델에서 예측을 수행한 이유를 설명 하는 방법에 대해 알아봅니다. 다음 작업에 대해 알아봅니다.

* 로컬 및 원격 계산 리소스에 대해 학습 된 기계 학습 모델 해석
* Azure 실행 기록에 대 한 로컬 및 글로벌 설명을 저장 합니다.
* [Azure Machine Learning studio](https://ml.azure.com) 에서 interpretability 시각화 보기
* 모델을 사용 하 여 점수 매기기 설명 배포

모델 interpretability 대해 자세히 알아보려면 [개념 문서](how-to-machine-learning-interpretability.md)를 참조 하세요.

## <a name="local-interpretability"></a>로컬 interpretability

다음 예제에서는 Azure 서비스에 연결 하지 않고 해석 패키지를 로컬로 사용 하는 방법을 보여 줍니다. `pip install azureml-interpret`를 실행 하 여 interpretability 패키지를 가져옵니다.

1. 로컬 Jupyter 노트북에서 샘플 모델을 학습 합니다.

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

2. 설명를 로컬로 호출: 설명 개체를 초기화 하려면 모델 및 일부 학습 데이터를 설명의 생성자에 전달 해야 합니다. 설명 및 시각화에 대 한 자세한 정보를 제공 하는 데 사용 되는 기능 이름 및 출력 클래스 이름 (분류를 수행 하는 경우)을 선택적으로 전달할 수도 있습니다. `TabularExplainer`, `MimicExplainer`및 `PFIExplainer`를 사용 하 여 로컬에서 설명 개체를 인스턴스화하는 방법은 다음과 같습니다. `TabularExplainer`는 아래에 있는 세 개의 SHAP explainers (`TreeExplainer`, `DeepExplainer`또는 `KernelExplainer`) 중 하나를 호출 하 고 사용 사례에 가장 적합 한 것을 자동으로 선택 합니다. 그러나 세 개의 기본 explainers을 직접 호출할 수 있습니다.

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

### <a name="overall-global-feature-importance-values"></a>전체 (전역) 기능 중요도 값

전역 기능 중요도 값을 가져옵니다.
    
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

### <a name="instance-level-local-feature-importance-values"></a>인스턴스 수준 (로컬) 기능 중요도 값

로컬 기능 중요도 값 가져오기: 다음 함수 호출을 사용 하 여 개별 인스턴스 또는 인스턴스 그룹을 설명 합니다. PFIExplainer는 로컬 설명을 지원 하지 않습니다.

```python
# explain the first data point in the test set
local_explanation = explainer.explain_local(x_test[0:5])

# sorted feature importance values and feature names
sorted_local_importance_names = local_explanation.get_ranked_local_names()
sorted_local_importance_values = local_explanation.get_ranked_local_values()
```

## <a name="interpretability-for-remote-runs"></a>원격 실행에 대 한 Interpretability

이 예제에서는 원격 실행을 위해 모델 interpretability를 사용 하도록 설정 하는 데 `ExplanationClient` 클래스를 사용 하는 방법을 보여 줍니다. 개념은 이전 섹션과 유사 하지만 원격 실행에서 `ExplanationClient`를 사용 하 여 interpretability 컨텍스트를 업로드 한 다음 나중에 로컬 환경에서 컨텍스트를 다운로드할 수 있습니다. `pip install azureml-contrib-interpret`를 사용 하 여 필요한 패키지를 가져옵니다.

1. 로컬 Jupyter 노트북 (예: py)에서 학습 스크립트를 만듭니다.

    ```python
    from azureml.contrib.interpret.explanation.explanation_client import ExplanationClient
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

1. [모델 학습을 위한 계산 목표 설정](how-to-set-up-training-targets.md#amlcompute) 지침에 따라 계산 대상으로 Azure Machine Learning 계산을 설정 하 고 학습 실행을 제출 하는 방법을 알아봅니다. [예제 노트북](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model/azure-integration/remote-explanation)도 볼 수 있습니다.

1. 로컬 Jupyter 노트북에서 설명을 다운로드 합니다.

    ```python
    from azureml.contrib.interpret.explanation.explanation_client import ExplanationClient
    
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

## <a name="raw-feature-transformations"></a>원시 기능 변환

필요에 따라 기능 변환 파이프라인을 설명 (train_explain)에 전달 하 여 변환 (엔지니어링 된 기능 대신)의 원시 기능을 기준으로 설명을 받을 수 있습니다. 이를 건너뛰면 설명는 엔지니어링 된 기능 측면에서 설명을 제공 합니다.

지원 되는 변환의 형식은 [pandas](https://github.com/scikit-learn-contrib/sklearn-pandas)에서 설명한 것과 같습니다. 일반적으로 모든 변환은 단일 열에서 작동 하는 한, 따라서 명확 하 게 한 번만 지원 됩니다. 

`sklearn.compose.ColumnTransformer` 또는 피팅 변환기 튜플 목록을 사용 하 여 원시 기능을 설명 합니다. 아래 코드는 `sklearn.compose.ColumnTransformer`를 사용 합니다. 


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

## <a name="visualizations"></a>시각화

로컬 Jupyter 노트북에서 설명을 다운로드 한 후 시각화 대시보드를 사용 하 여 모델을 이해 하 고 해석할 수 있습니다.

### <a name="global-visualizations"></a>전역 시각화

다음 그림에서는 학습 된 모델을 예측 및 설명과 함께 전체적으로 보여 주는 방법을 제공 합니다.

|그리면|설명|
|----|-----------|
|데이터 탐색| 예측 값과 함께 데이터 집합에 대 한 개요입니다.|
|글로벌 중요도|최상위 K (구성 가능 K) 중요 기능을 전역적으로 보여 줍니다. 이 차트는 기본 모델의 전역 동작을 이해 하는 데 유용 합니다.|
|설명 탐색|기능이 모델의 예측 값 (또는 예측 값의 확률)을 변경 하는 방법을 보여 줍니다. 또한 두 기능이 예측에 영향을 주는 것과 상호 작용 하는 방법을 보여 줍니다.|
|요약 중요도| 모든 데이터 요소에서 서명 된 로컬 기능 중요도 값을 사용 하 여 각 기능이 예측 값에 미치는 영향의 분포를 표시 합니다.|

[![시각화 대시보드 전역](./media/machine-learning-interpretability-explainability/global-charts.png)](./media/machine-learning-interpretability-explainability/global-charts.png#lightbox)

### <a name="local-visualizations"></a>로컬 시각화

이전 그림에서 언제 든 지 개별 데이터 요소를 클릭 하 여 지정 된 데이터 요소에 대 한 로컬 기능 중요도 그림을 로드 합니다.

|그리면|설명|
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
    jupyter nbextension install --py --sys-prefix azureml.contrib.interpret.visualize
    jupyter nbextension enable --py --sys-prefix azureml.contrib.interpret.visualize
    ```



* Jupyter 랩

    ```shell
    jupyter labextension install @jupyter-widgets/jupyterlab-manager
    jupyter labextension install microsoft-mli-widget
    ```

시각화 대시보드를 로드 하려면 다음 코드를 사용 합니다.

```python
from azureml.contrib.interpret.visualize import ExplanationDashboard

ExplanationDashboard(global_explanation, model, x_test)
```

### <a name="visualization-in-azure-machine-learning-studio"></a>Azure Machine Learning studio의 시각화

[Remote interpretability](how-to-machine-learning-interpretability-aml.md#interpretability-for-remote-runs) 섹션의 단계를 완료 하 여 [Azure Machine Learning studio](https://ml.azure.com)에서 시각화 대시보드를 확인할 수 있습니다. Azure Machine Learning studio에 표시 된 대시보드는 위에 설명 된 시각화 대시보드의 더 간단한 버전이 며 다음 두 개의 탭만 지원 합니다.

|그리면|설명|
|----|-----------|
|글로벌 중요도|최상위 K (구성 가능 K) 중요 기능을 전역적으로 보여 줍니다. 이 차트는 기본 모델의 전역 동작을 이해 하는 데 유용 합니다.|
|요약 중요도| 모든 데이터 요소에서 서명 된 로컬 기능 중요도 값을 사용 하 여 각 기능이 예측 값에 미치는 영향의 분포를 표시 합니다.|

전역 설명과 로컬 설명을 모두 사용할 수 있는 경우 두 탭이 모두 데이터로 채워집니다. 글로벌 설명만 사용할 수 있는 경우 두 번째 탭은 사용 하지 않도록 설정 됩니다.

Azure Machine Learning studio에서 시각화 대시보드에 액세스 하려면 다음 경로 중 하나를 사용할 수 있습니다.

1. 실험 탭 (미리 보기): "실험" 탭을 클릭 하면 Azure Machine Learning 서비스에서 실행 한 실험 목록이 표시 됩니다. 이 목록에서 선택한 실험 이름 아래의 모든 실행이 포함 된 페이지로 리디렉션되는 특정 실험을 선택할 수 있습니다. 각 실행 및 해당 "설명" 탭을 클릭 하면 설명 시각화 대시보드가 표시 됩니다.


[![시각화 대시보드 로컬 기능 중요도](./media/machine-learning-interpretability-explainability/amlstudio-experiments.png)](./media/machine-learning-interpretability-explainability/amlstudio-experiments.png#lightbox)


2. 모델 탭: [Azure Machine Learning로 모델 배포](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-and-where)의 단계를 사용 하 여 원래 모델을 등록 한 경우 "모델" 탭의 목록에 모델이 표시 됩니다. 각 모델 및 해당 "설명" 탭을 클릭 하면 설명 시각화 대시보드가 표시 됩니다.

## <a name="interpretability-at-inference-time"></a>유추 시 Interpretability

설명는 원본 모델과 함께 배포할 수 있으며, 유추 시 로컬 설명 정보를 제공 하는 데 사용할 수 있습니다. 또한 유추 시 interpretability의 성능을 향상 시키기 위해 가벼운 점수 매기기 explainers 제공 합니다. 가벼운 점수 매기기 설명를 배포 하는 프로세스는 모델 배포와 유사 하며 다음 단계를 포함 합니다.


1. 설명 개체 (예: TabularExplainer 사용)를 만듭니다.

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
   from azureml.contrib.interpret.scoring.scoring_explainer import KernelScoringExplainer, save

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
   from azureml.contrib.interpret.scoring.scoring_explainer import load

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

        azureml_pip_packages = ['azureml-defaults', 'azureml-contrib-interpret', 'azureml-core', 'azureml-telemetry', 'azureml-interpret']
 

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

1. 정리: 배포 된 웹 서비스를 삭제 하려면 `service.delete()`을 사용 합니다.

## <a name="next-steps"></a>다음 단계

모델 interpretability에 대해 자세히 알아보려면 [개념 문서](how-to-machine-learning-interpretability.md)를 참조 하세요.
