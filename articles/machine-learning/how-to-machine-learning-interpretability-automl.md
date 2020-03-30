---
title: 자동화된 Machine Learning의 모델 해석력
titleSuffix: Azure Machine Learning
description: 자동화된 ML 모델이 기능 의 중요성을 결정하고 Azure 기계 학습 SDK를 사용할 때 예측을 만드는 방법에 대한 설명을 얻는 방법에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.reviewer: trbye
ms.date: 10/25/2019
ms.openlocfilehash: e6fa3b9705fcd718f802b73560e7703b008af2b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064004"
---
# <a name="model-interpretability-in-automated-machine-learning"></a>자동화된 Machine Learning의 모델 해석력

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

이 문서에서는 Azure 기계 학습에서 자동화된 기계 학습(ML)에 대한 해석 기능을 사용하도록 설정하는 방법을 알아봅니다. 자동화된 ML을 사용하면 엔지니어링된 기능의 중요성을 이해하는 데 도움이 됩니다. 

기본적으로 1.0.85 `model_explainability=True` 이후의 모든 SDK 버전입니다. SDK 버전 1.0.85 및 이전 버전에서는 `model_explainability=True` 모델 `AutoMLConfig` 해석가능성을 사용하려면 개체에 설정해야 합니다. 

이 문서에서는 다음 방법을 설명합니다.

- 최상의 모델 또는 모든 모델에 대한 교육 중에 해석성을 수행합니다.
- 시각화를 사용하면 데이터 및 설명의 패턴을 볼 수 있습니다.
- 추론 또는 채점 중에 해석성을 구현합니다.

## <a name="prerequisites"></a>사전 요구 사항

- 해석 기능. 필요한 `pip install azureml-interpret azureml-contrib-interpret` 패키지를 얻기 위해 실행합니다.
- 자동화된 ML 실험 구축에 대한 지식. Azure 기계 학습 SDK를 사용하는 방법에 대한 자세한 내용은 이 [회귀 모델 자습서를](tutorial-auto-train-models.md) 완료하거나 자동화된 ML 실험을 구성하는 방법을 [참조하세요.](how-to-configure-auto-train.md)

## <a name="interpretability-during-training-for-the-best-model"></a>최고의 모델을 위한 교육 중 해석성

에서 설명을 `best_run`검색합니다.

### <a name="download-engineered-feature-importance-from-artifact-store"></a>아티팩트 스토어에서 엔지니어링 기능 중요도 다운로드

의 아티팩트 저장소에서 엔지니어링된 기능 설명을 다운로드하는 데 사용할 `ExplanationClient` 수 있습니다. `best_run` 

```python
from azureml.explain.model._internal.explanation_client import ExplanationClient

client = ExplanationClient.from_run(best_run)
engineered_explanations = client.download_model_explanation(raw=False)
print(engineered_explanations.get_feature_importance_dict())
```

## <a name="interpretability-during-training-for-any-model"></a>모든 모델에 대한 교육 중 해석성 

모델 설명을 계산하고 시각화할 때 자동화된 ML 모델에 대한 기존 모델 설명에 국한되지 않습니다. 다른 테스트 데이터로 모델에 대한 설명을 얻을 수도 있습니다. 이 섹션의 단계는 테스트 데이터를 기반으로 엔지니어링된 기능 중요도를 계산하고 시각화하는 방법을 보여 줍니다.

### <a name="retrieve-any-other-automl-model-from-training"></a>교육에서 다른 AutoML 모델 검색

```python
automl_run, fitted_model = local_run.get_output(metric='accuracy')
```

### <a name="set-up-the-model-explanations"></a>모델 설명 설정

엔지니어링 `automl_setup_model_explanations` 된 설명을 얻을 하는 데 사용 합니다. 다음 `fitted_model` 항목을 생성할 수 있습니다.

- 학습된 또는 테스트 샘플의 주요 데이터
- 엔지니어링된 피쳐 이름 목록
- 분류 시나리오에서 레이블이 지정된 열에서 찾을 수 있는 클래스

는 `automl_explainer_setup_obj` 위의 목록에서 모든 구조를 포함 합니다.

```python
from azureml.train.automl.runtime.automl_explain_utilities import automl_setup_model_explanations

automl_explainer_setup_obj = automl_setup_model_explanations(fitted_model, X=X_train, 
                                                             X_test=X_test, y=y_train, 
                                                             task='classification')
```

### <a name="initialize-the-mimic-explainer-for-feature-importance"></a>기능 중요도에 대한 모방 설명자 초기화

AutoML 모델에 대한 설명을 생성하려면 `MimicWrapper` 클래스를 사용합니다. 다음과 같은 매개 변수를 통해 MimicWrapper를 초기화할 수 있습니다.

- 설명자 설정 개체
- 작업 공간
- `fitted_model` 자동화된 ML 모델의 대리 역할을 하는 LightGBM 모델

또한 MimicWrapper는 `automl_run` 엔지니어링된 설명이 업로드되는 개체를 취합니다.

```python
from azureml.explain.model.mimic.models.lightgbm_model import LGBMExplainableModel
from azureml.explain.model.mimic_wrapper import MimicWrapper

# Initialize the Mimic Explainer
explainer = MimicWrapper(ws, automl_explainer_setup_obj.automl_estimator, LGBMExplainableModel, 
                         init_dataset=automl_explainer_setup_obj.X_transform, run=automl_run,
                         features=automl_explainer_setup_obj.engineered_feature_names, 
                         feature_maps=[automl_explainer_setup_obj.feature_map],
                         classes=automl_explainer_setup_obj.classes)
```

### <a name="use-mimicexplainer-for-computing-and-visualizing-engineered-feature-importance"></a>설계기능 중요도 를 계산하고 시각화하기 위해 MimicExplainer 사용

변환된 테스트 `explain()` 샘플과 함께 MimicWrapper에서 메서드를 호출하여 생성된 엔지니어링 피쳐에 대한 기능 중요성을 얻을 수 있습니다. `ExplanationDashboard` 자동화된 ML 위화기를 통해 생성된 엔지니어링 피처의 기능 중요도 값의 대시보드 시각화를 볼 수도 있습니다.

```python
engineered_explanations = explainer.explain(['local', 'global'], eval_dataset=automl_explainer_setup_obj.X_test_transform)
print(engineered_explanations.get_feature_importance_dict())
```

### <a name="interpretability-during-inference"></a>추론 중 해석 가능성

이 섹션에서는 이전 섹션의 설명을 계산하는 데 사용된 설명과 함께 자동화된 ML 모델을 운영하는 방법을 알아봅니다.

### <a name="register-the-model-and-the-scoring-explainer"></a>모델 및 점수 매기기 설명자 등록

을 `TreeScoringExplainer` 사용하여 추론 시간에 엔지니어링된 피쳐 중요도 값을 계산하는 점수 계산기를 만듭니다. 이전에 계산된 점수 설명기를 `feature_map` 초기화합니다. 

점수 매기기 설명자를 저장한 다음 모델 관리 서비스에 모델 및 점수 설명자를 등록합니다. 다음 코드를 실행합니다.

```python
from azureml.interpret.scoring.scoring_explainer import TreeScoringExplainer, save

# Initialize the ScoringExplainer
scoring_explainer = TreeScoringExplainer(explainer.explainer, feature_maps=[automl_explainer_setup_obj.feature_map])

# Pickle scoring explainer locally
save(scoring_explainer, exist_ok=True)

# Register trained automl model present in the 'outputs' folder in the artifacts
original_model = automl_run.register_model(model_name='automl_model', 
                                           model_path='outputs/model.pkl')

# Register scoring explainer
automl_run.upload_file('scoring_explainer.pkl', 'scoring_explainer.pkl')
scoring_explainer_model = automl_run.register_model(model_name='scoring_explainer', model_path='scoring_explainer.pkl')
```

### <a name="create-the-conda-dependencies-for-setting-up-the-service"></a>서비스 설정에 대한 conda 종속성 만들기

다음으로 배포된 모델에 대해 컨테이너에 필요한 환경 종속성을 만듭니다. 버전 >= 1.0.45의 azureml 기본값은 웹 서비스로 모델을 호스트하는 데 필요한 기능을 포함하므로 pip 종속성으로 나열되어야 합니다.

```python
from azureml.core.conda_dependencies import CondaDependencies

azureml_pip_packages = [
    'azureml-interpret', 'azureml-train-automl', 'azureml-defaults'
]

myenv = CondaDependencies.create(conda_packages=['scikit-learn', 'pandas', 'numpy', 'py-xgboost<=0.80'],
                                 pip_packages=azureml_pip_packages,
                                 pin_sdk_version=True)

with open("myenv.yml","w") as f:
    f.write(myenv.serialize_to_string())

with open("myenv.yml","r") as f:
    print(f.read())

```

### <a name="deploy-the-service"></a>서비스 배포

이전 단계의 conda 파일 및 점수 매기기 파일을 사용하여 서비스를 배포합니다.

```python
from azureml.core.webservice import Webservice
from azureml.core.webservice import AciWebservice
from azureml.core.model import Model, InferenceConfig
from azureml.core.environment import Environment

aciconfig = AciWebservice.deploy_configuration(cpu_cores=1,
                                               memory_gb=1,
                                               tags={"data": "Bank Marketing",  
                                                     "method" : "local_explanation"},
                                               description='Get local explanations for Bank marketing test data')
myenv = Environment.from_conda_specification(name="myenv", file_path="myenv.yml")
inference_config = InferenceConfig(entry_script="score_local_explain.py", environment=myenv)

# Use configs and models generated above
service = Model.deploy(ws,
                       'model-scoring',
                       [scoring_explainer_model, original_model],
                       inference_config,
                       aciconfig)
service.wait_for_deployment(show_output=True)
```

### <a name="inference-with-test-data"></a>테스트 데이터로 추론

일부 테스트 데이터를 사용하여 자동화된 ML 모델에서 예측된 값을 확인합니다. 예측 값에 대한 엔지니어링 피쳐 중요도를 봅니다.

```python
if service.state == 'Healthy':
    # Serialize the first row of the test data into json
    X_test_json = X_test[:1].to_json(orient='records')
    print(X_test_json)
    # Call the service to get the predictions and the engineered explanations
    output = service.run(X_test_json)
    # Print the predicted value
    print(output['predictions'])
    # Print the engineered feature importances for the predicted value
    print(output['engineered_local_importance_values'])
```

### <a name="visualize-to-discover-patterns-in-data-and-explanations-at-training-time"></a>학습 시 데이터 패턴 및 설명 검색에 시각화

[Azure 기계 학습 스튜디오에서](https://ml.azure.com)작업 영역에서 기능 중요도 차트를 시각화할 수 있습니다. 자동화된 ML 실행이 완료되면 **모델 세부 정보 보기를** 선택하여 특정 실행을 봅니다. 설명 시각화 대시보드를 보려면 **설명 탭을** 선택합니다.

[![기계 학습 해석 아키텍처](./media/how-to-machine-learning-interpretability-automl/automl-explainability.png)](./media/how-to-machine-learning-interpretability-automl/automl-explainability.png#lightbox)

## <a name="next-steps"></a>다음 단계

자동화된 기계 학습 이외의 Azure 기계 학습 SDK 영역에서 모델 설명 및 기능 중요성을 사용하도록 설정하는 방법에 대한 자세한 내용은 [해석 가능성에 대한 개념 문서를](how-to-machine-learning-interpretability.md)참조하십시오.
