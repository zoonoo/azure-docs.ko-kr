---
title: 자동화 된 ML에서 모델 interpretability
titleSuffix: Azure Machine Learning
description: 자동화 된 ML 모델에서 Azure Machine Learning SDK를 사용 하 여 예측을 수행 하는 이유를 설명 하는 방법을 알아봅니다. 학습 및 유추 중에 모델에서 기능 중요도를 결정 하 고 예측을 수행 하는 방법을 이해 하는 데 사용할 수 있습니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.reviewer: trbye
ms.date: 10/25/2019
ms.openlocfilehash: 2c9df55eb319dd45281eca4684c79d83dc6ef933
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73515331"
---
# <a name="model-interpretability-for-automated-ml-models"></a>자동화 된 ML 모델에 대 한 모델 interpretability

[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

이 방법에서는 Azure Machine Learning 서비스를 사용 하 여 자동화 된 기계 학습에서 interpretability 기능을 사용 하도록 설정 하는 방법에 대해 알아봅니다. 자동화 된 ML을 사용 하면 원시 기능과 엔지니어링 된 기능 중요도를 모두 이해할 수 있습니다. 모델 interpretability를 사용 하려면 `AutoMLConfig` 개체에 `model_explainability=True`를 설정 합니다.  

이 문서에서는 다음 작업에 대해 알아봅니다.

* 모범 모델 또는 모델에 대 한 학습 중에 Interpretability
* 데이터 및 설명의 패턴을 검색 하는 데 도움이 되는 시각화 사용
* 유추 또는 점수 매기기 중 Interpretability

## <a name="prerequisites"></a>필수 조건

* `pip install azureml-interpret azureml-contrib-interpret`를 실행 하 여 interpretability 기능에 필요한 패키지를 가져옵니다.
* 이 문서에서는 자동화 된 ML 실험을 빌드하는 데 대 한 지식이 있다고 가정 합니다. SDK에서 자동화 된 ML을 사용 하는 방법에 대해 알아보려면 [자습서](tutorial-auto-train-models.md) 또는 [방법](how-to-configure-auto-train.md) 을 참조 하세요.
 
## <a name="interpretability-during-training-for-the-best-model"></a>최상의 모델을 위한 학습 중에 Interpretability 

`best_run`에서 설명 하는 설명을 검색 합니다. 여기에는 엔지니어링 된 기능과 원시 기능에 대 한 설명이 포함 되어 있습니다. 

### <a name="download-engineered-feature-importance-from-artifact-store"></a>아티팩트 저장소에서 엔지니어링 된 기능 중요도 다운로드

`ExplanationClient`를 사용 하 여 best_run의 아티팩트 저장소에서 엔지니어링 된 기능 설명을 다운로드할 수 있습니다. 원시 기능 집합에 대 한 설명을 보려면 `raw=True`합니다. 

```python
from azureml.contrib.interpret.explanation.explanation_client import ExplanationClient

client = ExplanationClient.from_run(best_run)
engineered_explanations = client.download_model_explanation(raw=False)
print(engineered_explanations.get_feature_importance_dict())
```

## <a name="interpretability-during-training-for-any-model"></a>모델에 대해 학습 하는 동안 Interpretability 

이 섹션에서는 모델 설명을 계산 하 고 설명을 시각화 하는 방법에 대해 알아봅니다. 자동화 된 ML 모델에 대 한 기존 모델 설명을 검색 하는 것 외에도 다른 테스트 데이터를 사용 하 여 모델을 설명할 수 있습니다. 다음 단계를 통해 테스트 데이터를 기반으로 엔지니어링 된 기능 중요도와 원시 기능 중요도를 계산 하 고 시각화할 수 있습니다.

### <a name="retrieve-any-other-automl-model-from-training"></a>학습에서 다른 AutoML 모델 검색

```python
automl_run, fitted_model = local_run.get_output(metric='r2_score')
```

### <a name="setup-the-model-explanations"></a>모델 설명 설정

Fitted_model는 automl_setup_model_explanations를 사용 하 여 엔지니어링 된 기능 및 원시 기능 설명을 가져오는 데 사용 되는 다음 항목을 생성할 수 있습니다.

* 샘플 학습/테스트 샘플에서 데이터 기능화
* 엔지니어링 및 원시 기능 이름 목록 수집
* 분류 시나리오에서 레이블이 지정 된 열의 클래스 찾기

Automl_explainer_setup_obj에는 위의 목록에 있는 모든 구조가 포함 됩니다.

```python
from azureml.train.automl.automl_explain_utilities import AutoMLExplainerSetupClass, automl_setup_model_explanations

automl_explainer_setup_obj = automl_setup_model_explanations(fitted_model, X=X_train, 
                                                             X_test=X_test, y=y_train, 
                                                             task='classification')
```
### <a name="initialize-the-mimic-explainer-for-feature-importance"></a>기능 중요도에 대 한 모방 설명 초기화

AutoML 모델을 설명 하는 `MimicWrapper` 클래스를 사용 합니다. MimicWrapper는 설명 설치 개체의 매개 변수, 작업 영역 및 자동화 된 ML 모델을 설명 하는 서로게이트 모델 역할을 하는 LightGBM 모델 (여기서는 fitted_model here)으로 초기화할 수 있습니다. 또한 MimicWrapper는 원시 및 엔지니어링 된 설명이 업로드 되는 automl_run 개체를 사용 합니다.

```python
from azureml.interpret.mimic.models.lightgbm_model import LGBMExplainableModel
from azureml.interpret.mimic_wrapper import MimicWrapper

explainer = MimicWrapper(ws, automl_explainer_setup_obj.automl_estimator, LGBMExplainableModel, 
                         init_dataset=automl_explainer_setup_obj.X_transform, run=automl_run,
                         features=automl_explainer_setup_obj.engineered_feature_names, 
                         feature_maps=[automl_explainer_setup_obj.feature_map],
                         classes=automl_explainer_setup_obj.classes)
```

### <a name="use-mimicexplainer-for-computing-and-visualizing-engineered-feature-importance"></a>엔지니어링 된 기능 중요도를 계산 하 고 시각화 하는 데 MimicExplainer 사용

MimicWrapper의 설명 () 메서드를 변환 된 테스트 샘플과 함께 호출 하 여 생성 된 엔지니어링 기능에 대 한 기능 중요도를 가져올 수 있습니다. 또한 ExplanationDashboard를 사용 하 여 자동화 된 ML featurizers를 통해 생성 된 엔지니어링 기능의 기능 중요도 값의 대시 보드 시각화를 볼 수 있습니다.

```python
from azureml.contrib.interpret.visualize import ExplanationDashboard
engineered_explanations = explainer.explain(['local', 'global'],              
                                            eval_dataset=automl_explainer_setup_obj.X_test_transform)

print(engineered_explanations.get_feature_importance_dict())
ExplanationDashboard(engineered_explanations, automl_explainer_setup_obj.automl_estimator, automl_explainer_setup_obj.X_test_transform)
```
### <a name="use-mimic-explainer-for-computing-and-visualizing-raw-feature-importance"></a>원시 기능 중요도를 계산 하 고 시각화 하는 데 모방 설명 사용

MimicWrapper의 설명 () 메서드는 변환 된 테스트 샘플을 사용 하 여 다시 호출 하 고, 원시 기능의 기능 중요도를 얻기 위해 `get_raw`를 True로 설정할 수 있습니다. ExplanationDashboard를 사용 하 여 원시 기능의 기능 중요도 값의 대시보드 시각화를 볼 수도 있습니다.

```python
from azureml.contrib.interpret.visualize import ExplanationDashboard

raw_explanations = explainer.explain(['local', 'global'], get_raw=True, 
                                     raw_feature_names=automl_explainer_setup_obj.raw_feature_names,
                                     eval_dataset=automl_explainer_setup_obj.X_test_transform)

print(raw_explanations.get_feature_importance_dict())
ExplanationDashboard(raw_explanations, automl_explainer_setup_obj.automl_pipeline, automl_explainer_setup_obj.X_test_raw)
```

### <a name="interpretability-during-inference"></a>유추 중 Interpretability

이 섹션에서는 이전 섹션에서 설명을 계산 하는 데 사용 된 설명을 사용 하 여 자동화 된 ML 모델을 운영 하는 방법에 대해 알아봅니다.

### <a name="register-the-model-and-the-scoring-explainer"></a>모델 및 점수 매기기 설명을 등록 합니다.

`TreeScoringExplainer`를 사용 하 여 유추 시 원시 및 엔지니어링 된 기능 중요도 값을 계산 하는 데 사용 되는 점수 매기기 설명을 만듭니다. 이전에 계산 된 feature_map을 사용 하 여 점수 매기기 설명를 초기화 합니다. Feature_map은 점수 매기기 설명에서 원시 기능 중요도를 반환 하는 데 사용 됩니다.

아래 코드에서 점수 매기기 설명을 저장 하 고 모델 및 점수 매기기 설명를 모델 관리 서비스에 등록 합니다.

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

### <a name="create-the-conda-dependencies-for-setting-up-the-service"></a>서비스를 설정 하기 위한 conda 종속성 만들기

그런 다음 배포 된 모델에 대 한 컨테이너에 필요한 환경 종속성을 만듭니다.

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

이전 단계의 conda 파일 및 점수 매기기 파일을 사용 하 여 서비스를 배포 합니다.

```python
from azureml.core.webservice import Webservice
from azureml.core.model import InferenceConfig
from azureml.core.webservice import AciWebservice
from azureml.core.model import Model

aciconfig = AciWebservice.deploy_configuration(cpu_cores=1, 
                                               memory_gb=1, 
                                               tags={"data": "Bank Marketing",  
                                                     "method" : "local_explanation"}, 
                                               description='Get local explanations for Bank marketing test data')

inference_config = InferenceConfig(runtime= "python", 
                                   entry_script="score_local_explain.py",
                                   conda_file="myenv.yml")

# Use configs and models generated above
service = Model.deploy(ws, 'model-scoring', [scoring_explainer_model, original_model], inference_config, aciconfig)
service.wait_for_deployment(show_output=True)
```

### <a name="inference-using-test-data"></a>테스트 데이터를 사용 하는 유추

일부 테스트 데이터를 사용 하는 유추를 통해 자동화 된 ML 모델의 예측 값을 확인 하 고 예측 값에 대 한 엔지니어링 된 기능 중요도와 예측 값에 대 한 원시 기능 중요도를 확인할 수 있습니다.

```python
if service.state == 'Healthy':
    # Serialize the first row of the test data into json
    X_test_json = X_test[:1].to_json(orient='records')
    print(X_test_json)
    # Call the service to get the predictions and the engineered and raw explanations
    output = service.run(X_test_json)
    # Print the predicted value
    print(output['predictions'])
    # Print the engineered feature importances for the predicted value
    print(output['engineered_local_importance_values'])
    # Print the raw feature importances for the predicted value
    print(output['raw_local_importance_values'])
```

### <a name="visualizations-to-aid-you-in-the-discovery-of-patterns-in-data-and-explanations-at-training-time"></a>학습 시간에 데이터 및 설명의 패턴을 검색 하는 데 도움이 되는 시각화

[Azure Machine Learning studio](https://ml.azure.com)에서 작업 영역에 있는 기능 중요도 차트를 시각화할 수도 있습니다. 자동화 된 ML 실행이 완료 되 면 "모델 세부 정보 보기"를 클릭 해야 합니다. 그러면 특정 실행으로 이동 됩니다. 여기에서 "설명" 탭을 클릭 하 여 설명 시각화 대시보드를 볼 수 있습니다. 

[![Machine Learning Interpretability 아키텍처](./media/machine-learning-interpretability-explainability/automl-explainability.png)](./media/machine-learning-interpretability-explainability/automl-explainability.png#lightbox)

## <a name="next-steps"></a>다음 단계

자동화 된 machine learning 외부에서 SDK의 다른 영역에 모델 설명과 기능 중요도를 사용 하도록 설정 하는 방법에 대 한 자세한 내용은 interpretability의 [개념](how-to-machine-learning-interpretability.md) 문서를 참조 하세요.