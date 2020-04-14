---
title: 기계 학습 모델 및 예측 설명
titleSuffix: Azure Machine Learning
description: 기계 학습 모델이 기능 의 중요성을 결정하고 Azure 기계 학습 SDK를 사용할 때 예측을 만드는 방법에 대한 설명을 얻는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.reviewer: Luis.Quintanilla
ms.date: 04/12/2020
ms.openlocfilehash: c1282ed16c9e3b92e7d5ec3f9969bee6fc3d917f
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81257205"
---
# <a name="explain-machine-learning-models-and-predictions"></a>기계 학습 모델 및 예측 설명

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

이 방법 가이드에서는 Azure 기계 학습 파이썬 SDK의 해석 가능성 패키지를 사용하여 다음 작업을 수행하는 방법을 배웁니다.


* 개인 컴퓨터에서 전체 모델 동작 또는 개별 예측을 로컬로 설명합니다.

* 엔지니어링 된 기능에 대한 해석 기술을 활성화합니다.

* Azure에서 전체 모델 및 개별 예측에 대한 동작을 설명합니다.

* 시각화 대시보드를 사용하여 모델 설명과 상호 작용할 수 있습니다.

* 추론 하는 동안 설명을 관찰 하기 위해 모델 함께 점수 설명기를 배포 합니다.



지원되는 해석 기술 및 기계 학습 모델에 대한 자세한 내용은 Azure 기계 학습 및 [샘플 노트북의](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model) [모델 해석 가능성을](how-to-machine-learning-interpretability.md) 참조하십시오.

## <a name="generate-feature-importance-value-on-your-personal-machine"></a>개인 기기에서 기능 중요도 가치 생성 
다음 예제에서는 Azure 서비스에 연결하지 않고 개인 컴퓨터에서 해석 가능성 패키지를 사용하는 방법을 보여 줍니다.

1. 설치 `azureml-interpret` `azureml-interpret-contrib` 및 패키지.
    ```bash
    pip install azureml-interpret
    pip install azureml-interpret-contrib
    ```

2. 로컬 Jupyter 노트북에서 샘플 모델을 학습합니다.

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

3. 설명자가 로컬로 호출합니다.
   * 설명자 개체를 초기화하려면 모델 및 일부 학습 데이터를 설명자의 생성자에게 전달합니다.
   * 설명 및 시각화를 보다 유익하게 만들려면 분류를 수행하는 경우 피처 이름 및 출력 클래스 이름을 전달하도록 선택할 수 있습니다.

   다음 코드 블록에서는 설명자 개체를 `TabularExplainer`을 (? `MimicExplainer`및 `PFIExplainer` 로컬)으로 인스턴스화하는 방법을 보여 준다.
   * `TabularExplainer`아래 세 SHAP 설명자 중`TreeExplainer` `DeepExplainer`하나를 `KernelExplainer`호출합니다.
   * `TabularExplainer`사용 사례에 가장 적합한 설명서를 자동으로 선택하지만 세 가지 기본 설명자 각각을 직접 호출할 수 있습니다.

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

### <a name="explain-the-entire-model-behavior-global-explanation"></a>전체 모델 동작 설명(전역 설명) 

집계(전역) 피쳐 중요도 값을 얻는 데 도움이 되는 다음 예제를 참조하십시오.

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

### <a name="explain-an-individual-prediction-local-explanation"></a>개별 예측 설명(로컬 설명)
개별 인스턴스 또는 인스턴스 그룹에 대한 설명을 호출하여 서로 다른 데이터 포인트의 개별 기능 중요도 값을 가져옵니다.
> [!NOTE]
> `PFIExplainer`로컬 설명을 지원하지 않습니다.

```python
# get explanation for the first data point in the test set
local_explanation = explainer.explain_local(x_test[0:5])

# sorted feature importance values and feature names
sorted_local_importance_names = local_explanation.get_ranked_local_names()
sorted_local_importance_values = local_explanation.get_ranked_local_values()
```

### <a name="raw-feature-transformations"></a>원시 기능 변환

엔지니어링된 기능이 아닌 변형되지 않은 원시 기능에 대한 설명을 얻을 수 있습니다. 이 옵션의 경우 기능 변환 파이프라인을 `train_explain.py`의 설명자에게 전달합니다. 그렇지 않으면 설명자는 엔지니어링된 기능에 대한 설명을 제공합니다.

지원되는 변환의 형식은 [sklearn-pandas에 설명된 것과 동일합니다.](https://github.com/scikit-learn-contrib/sklearn-pandas) 일반적으로 모든 변환은 단일 열에서 작동하는 한 지원되므로 일대다인 것이 분명합니다.

`sklearn.compose.ColumnTransformer` 또는 피팅 된 변압기 튜플 목록을 사용하여 원시 기능에 대한 설명을 가져옵니다. 다음 예제에서는 `sklearn.compose.ColumnTransformer`을 사용합니다.

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

피팅된 변압기 tuples 목록으로 예제를 실행하려는 경우 다음 코드를 사용합니다.

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

다음 예제에서는 클래스를 `ExplanationClient` 사용하여 원격 실행에 대한 모델 해석을 활성화하는 방법을 보여 주며 있습니다. 다음과 같은 경우를 제외하고는 개념적으로 로컬 프로세스와 유사합니다.

* 원격 `ExplanationClient` 실행에서 해석 가능성 컨텍스트를 업로드합니다.
* 나중에 로컬 환경에서 컨텍스트를 다운로드합니다.

1. 설치 `azureml-interpret` `azureml-interpret-contrib` 및 패키지.
    ```bash
    pip install azureml-interpret
    pip install azureml-interpret-contrib
    ```
1. 로컬 Jupyter 노트북에 학습 스크립트를 만듭니다. `train_explain.py`)을 입력합니다.

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

1. Azure 기계 학습 계산을 계산 대상으로 설정하고 교육 실행을 제출합니다. 지침에 [대한 모델 학습에 대한 계산 대상 설정을](how-to-set-up-training-targets.md#amlcompute) 참조하십시오. [예제 전자 필기장이](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model/azure-integration/remote-explanation) 도움이 될 수도 있습니다.

1. 해당 지역의 Jupyter 노트북에서 설명을 다운로드하십시오.

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


## <a name="visualizations"></a>시각화

로컬 Jupyter 노트북에서 설명을 다운로드한 후 시각화 대시보드를 사용하여 모델을 이해하고 해석할 수 있습니다.

### <a name="understand-entire-model-behavior-global-explanation"></a>전체 모델 동작 이해(전역 설명) 

다음 플롯은 예측 및 설명과 함께 학습된 모델의 전체 보기를 제공합니다.

|플롯|Description|
|----|-----------|
|데이터 탐색| 예측 값과 함께 데이터 집합의 개요를 표시합니다.|
|글로벌 중요성|집계는 모델의 전체 상위 K(구성 가능한 K)의 중요한 피처를 표시하기 위해 개별 데이터 포인트의 중요도 값을 특징으로 합니다. 기본 모델의 전반적인 동작을 이해하는 데 도움이 됩니다.|
|설명 탐색|기능이 모델의 예측 값 의 변화 또는 예측 값의 확률에 미치는 영향을 보여 줍니다. 기능 상호 작용의 영향을 표시합니다.|
|요약 중요도|모든 데이터 포인트에서 개별 피쳐 중요도 값을 사용하여 각 피쳐가 예측 값에 미치는 영향의 분포를 표시합니다. 이 다이어그램을 사용하여 기능 값이 예측 값에 영향을 미치는 방향을 조사합니다.
|

[![시각화 대시보드 글로벌](./media/how-to-machine-learning-interpretability-aml/global-charts.png)](./media/how-to-machine-learning-interpretability-aml/global-charts.png#lightbox)

### <a name="understand-individual-predictions-local-explanation"></a>개별 예측 이해(현지 설명) 

전체 플롯의 개별 데이터 점을 클릭하여 모든 데이터 포인트에 대한 개별 피쳐 중요도 플롯을 로드할 수 있습니다.

|플롯|Description|
|----|-----------|
|지역 적 중요성|개별 예측에 대한 상위 K(구성 가능한 K) 중요한 피처를 표시합니다. 특정 데이터 포인트에서 기본 모델의 로컬 동작을 보여 줍니다.|
|섭동 탐색(분석하는 경우 는 무엇인가)|선택한 데이터 포인트의 피처 값을 변경하고 예측 값에 대한 결과 변경 사항을 관찰할 수 있습니다.|
|개인 조건부 기대 (ICE)| 피처 값을 최소값에서 최대값으로 변경할 수 있습니다. 피쳐가 변경될 때 데이터 포인트의 예측이 어떻게 변경되는지 보여 줍니다.|

[![시각화 대시보드 로컬 기능 중요도](./media/how-to-machine-learning-interpretability-aml/local-charts.png)](./media/how-to-machine-learning-interpretability-aml/local-charts.png#lightbox)


[![시각화 대시보드 기능 섭동](./media/how-to-machine-learning-interpretability-aml/perturbation.gif)](./media/how-to-machine-learning-interpretability-aml/perturbation.gif#lightbox)


[![시각화 대시보드 ICE 플롯](./media/how-to-machine-learning-interpretability-aml/ice-plot.png)](./media/how-to-machine-learning-interpretability-aml/ice-plot.png#lightbox)

> [!NOTE]
> Jupyter 커널이 시작되기 전에 시각화 대시보드에 대한 위젯 확장을 사용하도록 설정해야 합니다.

* Jupyter Notebook

    ```shell
    jupyter nbextension install --py --sys-prefix azureml.contrib.interpret.visualize
    jupyter nbextension enable --py --sys-prefix azureml.contrib.interpret.visualize
    ```

* 주피터랩

    ```shell
    jupyter labextension install @jupyter-widgets/jupyterlab-manager
    jupyter labextension install microsoft-mli-widget
    ```

시각화 대시보드를 로드하려면 다음 코드를 사용합니다.

```python
from interpret_community.widget import ExplanationDashboard

ExplanationDashboard(global_explanation, model, x_test)
```

### <a name="visualization-in-azure-machine-learning-studio"></a>Azure 기계 학습 스튜디오의 시각화

원격 해석 [가능성](how-to-machine-learning-interpretability-aml.md#generate-feature-importance-values-via-remote-runs) 단계(생성된 설명을 Azure 기계 학습 실행 기록에 업로드)를 완료하면 [Azure 기계 학습 스튜디오에서](https://ml.azure.com)시각화 대시보드를 볼 수 있습니다. 이 대시보드는 위에서 설명한 시각화 대시보드의 간단한 버전입니다(설명 탐색 및 ICE 플롯은 실시간 계산을 수행할 수 있는 스튜디오에서 활성 계산이 없기 때문에 비활성화됨).

데이터 집합, 전역 및 로컬 설명을 사용할 수 있는 경우 데이터는 모든 탭을 채웁니다(교란 탐색 및 ICE 제외). 전역 설명만 사용할 수 있는 경우 요약 중요도 탭 및 모든 로컬 설명 탭이 비활성화됩니다.

다음 경로 중 하나를 따라 Azure 기계 학습 스튜디오에서 시각화 대시보드에 액세스합니다.

* **실험** 창(미리 보기)
  1. 왼쪽 창에서 **실험을** 선택하여 Azure 기계 학습에서 실행한 실험 목록을 확인합니다.
  1. 특정 실험을 선택하여 해당 실험의 모든 실행을 확인합니다.
  1. 실행을 선택한 다음 설명 시각화 대시보드에 **대한 설명** 탭을 선택합니다.

   [![시각화 대시보드 로컬 기능 중요도](./media/how-to-machine-learning-interpretability-aml/amlstudio-experiments.png)](./media/how-to-machine-learning-interpretability-aml/amlstudio-experiments.png#lightbox)

* **모델** 창
  1. [Azure 기계 학습을 사용하여 모델 배포의](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-and-where)단계에 따라 원래 모델을 등록한 경우 왼쪽 창에서 **모델을** 선택하여 볼 수 있습니다.
  1. 모델을 선택한 다음 **설명** 탭을 클릭하여 설명 시각화 대시보드를 봅니다.

## <a name="interpretability-at-inference-time"></a>추론 시간에 통역성

설명기를 원래 모델과 함께 배포하고 추론 시간에 사용하여 새 새 데이터 포인트에 대한 개별 기능 중요도 값(로컬 설명)을 제공할 수 있습니다. 또한 추론 시간에 해석 성능을 향상시키기 위해 경량 점수 설명자를 제공합니다. 경량 점수 설명기를 배포하는 프로세스는 모델을 배포하는 것과 유사하며 다음 단계를 포함합니다.

1. 설명 개체를 만듭니다. 예를 들어 다음을 `TabularExplainer`사용할 수 있습니다.

   ```python
    from interpret.ext.blackbox import TabularExplainer


   explainer = TabularExplainer(model, 
                                initialization_examples=x_train, 
                                features=dataset_feature_names, 
                                classes=dataset_classes, 
                                transformations=transformations)
   ```

1. 설명 개체를 사용하여 점수 매기기 설명기를 만듭니다.

   ```python
   from azureml.interpret.scoring.scoring_explainer import KernelScoringExplainer, save

   # create a lightweight explainer at scoring time
   scoring_explainer = KernelScoringExplainer(explainer)

   # pickle scoring explainer
   # pickle scoring explainer locally
   OUTPUT_DIR = 'my_directory'
   save(scoring_explainer, directory=OUTPUT_DIR, exist_ok=True)
   ```

1. 점수 매기기 설명 모델을 사용하는 이미지를 구성하고 등록합니다.

   ```python
   # register explainer model using the path from ScoringExplainer.save - could be done on remote compute
   # scoring_explainer.pkl is the filename on disk, while my_scoring_explainer.pkl will be the filename in cloud storage
   run.upload_file('my_scoring_explainer.pkl', os.path.join(OUTPUT_DIR, 'scoring_explainer.pkl'))
   
   scoring_explainer_model = run.register_model(model_name='my_scoring_explainer', 
                                                model_path='my_scoring_explainer.pkl')
   print(scoring_explainer_model.name, scoring_explainer_model.id, scoring_explainer_model.version, sep = '\t')
   ```

1. 선택적 단계로 클라우드에서 채점 설명자를 검색하고 설명을 테스트할 수 있습니다.

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

1. 다음 단계에 따라 계산 대상에 이미지를 배포합니다.

   1. 필요한 경우 [Azure Machine Learning을 사용하는 모델 배포의](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-and-where)단계에 따라 원래 예측 모델을 등록합니다.

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

         이 구성은 모델의 요구 사항에 따라 다릅니다. 다음 예제에서는 CPU 코어 1개와 메모리 1GB를 사용하는 구성을 정의합니다.

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

   1. g++가 설치된 사용자 지정 도커 파일을 만듭니다.

         ```python
         %%writefile dockerfile
         RUN apt-get update && apt-get install -y g++
         ```

   1. 생성된 이미지를 배포합니다.
   
         이 프로세스는 약 5분 정도 걸립니다.

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

## <a name="next-steps"></a>다음 단계

[모델 해석성에 대해 자세히 알아보기](how-to-machine-learning-interpretability.md)

[Azure 기계 학습 해석 가능성 샘플 노트북 확인](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model)

