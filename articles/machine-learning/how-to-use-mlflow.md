---
title: ML 실험을 위한 MLflow 추적
titleSuffix: Azure Machine Learning
description: Azure Machine Learning를 사용 하 여 MLflow를 설정 하 여 ML 모델에서 메트릭과 아티팩트를 기록 하 고 ML 모델을 웹 서비스로 배포 합니다.
services: machine-learning
author: shivp950
ms.author: shipatel
ms.service: machine-learning
ms.subservice: core
ms.reviewer: nibaccam
ms.date: 09/08/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 3fb177afa804788632f22d24bbd376d64cbe1c9f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91250694"
---
# <a name="track-experiment-runs-and-deploy-ml-models-with-mlflow-and-azure-machine-learning-preview"></a>MLflow 및 Azure Machine Learning를 사용 하 여 실험 실행 추적 및 ML 모델 배포 (미리 보기)

이 문서에서는 mlflow의 추적 URI 및 로깅 API를 사용 하 [여 mlflow](https://mlflow.org/docs/latest/quickstart.html#using-the-tracking-api)실험의 백 엔드로 Azure Machine Learning 연결 하는 방법에 대해 알아봅니다. 

지원 되는 기능은 다음과 같습니다. 

+ [Azure Machine Learning 작업 영역](https://docs.microsoft.com/azure/machine-learning/concept-azure-machine-learning-architecture#workspaces)에서 실험 메트릭과 아티팩트를 추적 하 고 기록 합니다. 실험을 위해 MLflow 추적을 이미 사용하고 있는 경우 작업 영역이 학습 메트릭 및 모델을 저장할 중앙 집중화된 안전하고 확장성 있는 위치를 제공합니다.

+ Azure Machine Learning 백엔드 지원 (미리 보기)을 사용 하 여 MLflow 프로젝트와 함께 학습 작업을 제출 합니다. Azure Machine Learning 추적을 사용 하 여 로컬로 작업을 제출 하거나 실행을 [Azure Machine Learning 계산](https://docs.microsoft.com/azure/machine-learning/how-to-create-attach-compute-sdk#amlcompute)을 통해 클라우드로 마이그레이션할 수 있습니다.

+ MLflow 및 Azure Machine Learning 모델 레지스트리에서 모델을 추적 하 고 관리 합니다.

+ MLflow 실험을 Azure Machine Learning 웹 서비스로 배포 합니다. 웹 서비스로 배포 하 여 Azure Machine Learning 모니터링 및 데이터 드리프트 검색 기능을 프로덕션 모델에 적용할 수 있습니다. 

[MLflow](https://www.mlflow.org)는 기계 학습 실험의 수명 주기를 관리하기 위한 오픈 소스 라이브러리입니다. MLFlow 추적은 실험 환경에 상관 없이, 원격 계산 대상, 가상 머신 또는 [Azure Databricks 클러스터](how-to-use-mlflow-azure-databricks.md)에서 컴퓨터의 로컬로 로컬로 학습 실행 메트릭과 모델 아티팩트를 기록 하 고 추적 하는 mlflow의 구성 요소입니다. 

>[!NOTE]
> 오픈 소스 라이브러리로 MLflow는 자주 변경 됩니다. 따라서 Azure Machine Learning 및 MLflow 통합을 통해 제공 되는 기능은 미리 보기로 간주 해야 하며 Microsoft에서 완벽 하 게 지원 되지 않습니다.

다음 다이어그램에서는 MLflow 추적을 사용하여 Azure Machine Learning 작업 영역에 실험의 실행 메트릭과 모델 아티팩트를 저장하는 방법을 보여 줍니다.

![Azure Machine Learning을 사용한 MLflow 다이어그램](./media/how-to-use-mlflow/mlflow-diagram-track.png)

> [!TIP]
> 이 문서의 정보는 주로 모델 학습 프로세스를 모니터링하려는 데이터 과학자와 개발자를 위한 것입니다. 할당량, 완료된 학습 실행 또는 완료된 모델 배포와 같이 Azure Machine Learning의 리소스 사용과 이벤트를 모니터링하는 데 관심이 있는 관리자는 [Azure Machine Learning 모니터링](monitor-azure-machine-learning.md)을 참조하세요.

## <a name="compare-mlflow-and-azure-machine-learning-clients"></a>MLflow 클라이언트와 Azure Machine Learning 클라이언트 비교

 다음 표에서 Azure Machine Learning 및 해당 기능 기능을 사용할 수 있는 여러 클라이언트를 요약 합니다.

 MLflow 추적은 [Azure Machine Learning Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true)를 통해 사용할 수 있는 메트릭 로깅 및 아티팩트 스토리지 기능을 제공합니다.

| 기능 | MLflow 추적 & 배포 | Azure Machine Learning Python SDK |  Azure Machine Learning CLI | Azure Machine Learning Studio|
|---|---|---|---|---|
| 작업 영역 관리 |   | ✓ | ✓ | ✓ |
| 데이터 저장소 사용  |   | ✓ | ✓ | |
| 메트릭 로깅      | ✓ | ✓ |   | |
| 아티팩트 업로드 | ✓ | ✓ |   | |
| 메트릭 보기     | ✓ | ✓ | ✓ | ✓ |
| 컴퓨팅 관리   |   | ✓ | ✓ | ✓ |
| 모델 배포    | ✓ | ✓ | ✓ | ✓ |
|모델 성능 모니터링||✓|  |   |
| 데이터 드리프트 검색 |   | ✓ |   | ✓ |

## <a name="prerequisites"></a>사전 요구 사항

* `azureml-mlflow` 패키지를 설치합니다. 
    * 이 패키지는 `azureml-core` 작업 영역에 액세스 하기 위해 MLflow에 대 한 연결을 제공 하는 [AZURE MACHINE LEARNING Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true)를 자동으로 가져옵니다.
* [Azure Machine Learning 작업 영역을 만듭니다](how-to-manage-workspace.md).

## <a name="track-local-runs"></a>로컬 실행 추적

MLflow 추적을 Azure Machine Learning과 함께 사용하면 로컬 실행의 로깅된 메트릭 및 아티팩트를 Azure Machine Learning 작업 영역에 저장할 수 있습니다.

`mlflow` 및 [`Workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace%28class%29?view=azure-ml-py&preserve-view=true) 클래스를 가져와 MLflow의 추적 URI에 액세스하고 작업 영역을 구성합니다.

다음 코드에서 `get_mlflow_tracking_uri()` 메서드는 작업 영역 `ws`에 고유한 추적 URI 주소를 할당하고, `set_tracking_uri()`는 이 주소로 MLflow 추적 URI를 가리킵니다.

```Python
import mlflow
from azureml.core import Workspace

ws = Workspace.from_config()

mlflow.set_tracking_uri(ws.get_mlflow_tracking_uri())
```

>[!NOTE]
>추적 URI는 최대 1시간까지 유효합니다. 유휴 시간 이후 스크립트를 다시 시작하는 경우 get_mlflow_tracking_uri API를 사용하여 새 URI를 가져옵니다.

`set_experiment()`를 사용하여 MLflow 실험 이름을 설정하고 `start_run()`을 사용하여 학습 실행을 시작합니다. 그런 다음 `log_metric()`을 사용하여 MLflow 로깅 API를 활성화하고 학습 실행 메트릭 로깅을 시작합니다.

```Python
experiment_name = 'experiment_with_mlflow'
mlflow.set_experiment(experiment_name)

with mlflow.start_run():
    mlflow.log_metric('alpha', 0.03)
```

## <a name="track-remote-runs"></a>원격 실행 추적

원격 실행을 사용하면 GPU 사용 가상 머신 또는 Machine Learning 컴퓨팅 클러스터와 같은 보다 강력한 컴퓨팅을 통해 모델을 학습시킬 수 있습니다. 다양 한 계산 옵션에 대 한 자세한 내용은 [모델 학습에 계산 대상 사용](how-to-set-up-training-targets.md) 을 참조 하세요.

MLflow 추적을 Azure Machine Learning과 함께 사용하면 원격 실행의 로깅된 메트릭 및 아티팩트를 Azure Machine Learning 작업 영역에 저장할 수 있습니다. MLflow 추적 코드를 사용 하는 모든 실행은 작업 영역에 자동으로 기록 됩니다. 

다음 예제 conda 환경에서는 `mlflow` 및를 `azureml-mlflow` pip 패키지로 포함 합니다. 


```yaml
name: sklearn-example
dependencies:
  - python=3.6.2
  - scikit-learn
  - matplotlib
  - numpy
  - pip:
    - azureml-mlflow
    - numpy
```

스크립트에서 클래스를 사용 하 여 계산 및 학습 실행 환경을 구성 [`Environment`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py&preserve-view=true) 합니다. 그런 다음  [`ScriptRunConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig?view=azure-ml-py&preserve-view=true) 계산 대상으로 원격 계산을 사용 하 여를 생성 합니다.

```Python
import mlflow

with mlflow.start_run():
    mlflow.log_metric('example', 1.23)
```

이 컴퓨팅 및 학습 실행 구성에서 `Experiment.submit()` 메서드를 사용하여 실행을 제출합니다. 이 메서드는 자동으로 MLflow 추적 URI를 설정하고 MLflow에서 작업 영역으로 로깅을 보냅니다.

```Python
run = exp.submit(src)
```

## <a name="train-with-mlflow-projects"></a>MLflow 프로젝트를 사용 하 여 학습

[Mlflow 프로젝트](https://mlflow.org/docs/latest/projects.html) 를 사용 하면 코드를 구성 하 고 설명 하 여 다른 데이터 과학자 (또는 자동화 된 도구)에서 실행할 수 있습니다. Azure Machine Learning를 사용 하는 MLflow 프로젝트를 사용 하면 작업 영역에서 학습 실행을 추적 하 고 관리할 수 있습니다. 

이 예에서는 Azure Machine Learning 추적을 사용 하 여 MLflow 프로젝트를 로컬로 제출 하는 방법을 보여 줍니다.

`azureml-mlflow`실험에서 Azure Machine Learning 하 여 MLflow 추적을 로컬로 사용 하려면 패키지를 설치 합니다. 실험은 Jupyter 노트북 또는 코드 편집기를 통해 실행할 수 있습니다.

```shell
pip install azureml-mlflow
```

`mlflow` 및 [`Workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace%28class%29?view=azure-ml-py&preserve-view=true) 클래스를 가져와 MLflow의 추적 URI에 액세스하고 작업 영역을 구성합니다.

```Python
import mlflow
from azureml.core import Workspace

ws = Workspace.from_config()

mlflow.set_tracking_uri(ws.get_mlflow_tracking_uri())
```

`set_experiment()`를 사용하여 MLflow 실험 이름을 설정하고 `start_run()`을 사용하여 학습 실행을 시작합니다. 그런 다음를 사용 `log_metric()` 하 여 MLflow 로깅 API를 활성화 하 고 학습 실행 메트릭의 로깅을 시작 합니다.

```Python
experiment_name = 'experiment-with-mlflow-projects'
mlflow.set_experiment(experiment_name)
```

백 엔드 구성 개체를 만들어 사용할 관리 되는 환경 유형과 계산 대상과 같은 통합에 필요한 정보를 저장 합니다.

```python
backend_config = {"USE_CONDA": False}
```
`azureml-mlflow`작업 영역에서 메트릭 및 키 아티팩트를 추적 하기 위해 패키지를 환경 구성 파일에 pip 종속성으로 추가 합니다. 

``` shell
name: mlflow-example
channels:
  - defaults
  - anaconda
  - conda-forge
dependencies:
  - python=3.6
  - scikit-learn=0.19.1
  - pip
  - pip:
    - mlflow
    - azureml-mlflow
```
로컬 실행을 제출 하 고 매개 변수를 설정 했는지 확인 `backend = "azureml" ` 합니다. 이 설정을 사용 하 여 로컬로 실행을 제출 하 고 작업 영역에서 자동 출력 추적, 로그 파일, 스냅숏 및 인쇄 된 오류에 대 한 추가 지원을 받을 수 있습니다. 

[Azure Machine Learning studio](overview-what-is-machine-learning-studio.md)에서 실행 및 메트릭을 확인 합니다. 


```python
local_env_run = mlflow.projects.run(uri=".", 
                                    parameters={"alpha":0.3},
                                    backend = "azureml",
                                    use_conda=False,
                                    backend_config = backend_config, 
                                    )

```

## <a name="view-metrics-and-artifacts-in-your-workspace"></a>작업 영역에서 메트릭 및 아티팩트 보기

MLflow 로깅의 메트릭과 아티팩트는 작업 영역에 유지됩니다. 언제든지 이들을 보려면 작업 영역으로 이동하여 [Azure Machine Learning 스튜디오](https://ml.azure.com)의 작업 영역에서 이름으로 실험을 찾습니다.  또는 아래 코드를 실행합니다. 

```python
run.get_metrics()
ws.get_details()
```

## <a name="manage-models"></a>모델 관리 

MLflow 모델 레지스트리를 지 원하는 [Azure Machine Learning model registry](concept-model-management-and-deployment.md#register-package-and-deploy-models-from-anywhere) 를 사용 하 여 모델을 등록 하 고 추적 합니다. Azure Machine Learning 모델은 MLflow 모델 스키마를 사용 하 여 다양 한 워크플로를 통해 이러한 모델을 쉽게 내보내고 가져올 수 있습니다. 실행 id와 같은 MLflow 관련 메타 데이터에는 추적 가능성을 위한 등록 된 모델만 태그가 지정 되어 있습니다. 사용자는 MLflow 실행에서 생성 된 학습 실행, 등록 및 배포 모델을 제출할 수 있습니다. 

프로덕션 준비 모델을 한 번에 배포 하 고 등록 하려면 [MLflow 모델 배포 및 등록](#deploy-and-register-mlflow-models)을 참조 하세요.

실행에서 모델을 등록 하 고 보려면 다음 단계를 사용 합니다.

1. 실행이 완료 되 면 메서드를 호출 합니다 `register_model()` .

    ```python
    # the model folder produced from the run is registered. This includes the MLmodel file, model.pkl and the conda.yaml.
    run.register_model(model_name = 'my-model', model_path = 'model')
    ```

1. [Azure Machine Learning studio](overview-what-is-machine-learning-studio.md)를 사용 하 여 작업 영역에서 등록 된 모델을 봅니다.

    다음 예제에서 등록 된 모델에는 `my-model` MLflow 추적 메타 데이터 태그가 지정 되어 있습니다. 

    ![등록-mlflow-모델](./media/how-to-use-mlflow/registered-mlflow-model.png)

1. **아티팩트** 탭을 선택 하 여 mlflow 모델 스키마 (Conda, mlflow, model.pkl)와 일치 하는 모든 모델 파일을 볼 수 있습니다.

    ![모델-스키마](./media/how-to-use-mlflow/mlflow-model-schema.png)

1. MLmodel을 선택 하 여 실행에 의해 생성 된 MLmodel 파일을 확인 합니다.

    ![MLmodel-스키마](./media/how-to-use-mlflow/mlmodel-view.png)



## <a name="deploy-and-register-mlflow-models"></a>MLflow 모델 배포 및 등록 

MLflow 실험을 Azure Machine Learning 웹 서비스로 배포 하면 Azure Machine Learning 모델 관리 및 데이터 드리프트 검색 기능을 활용 하 여 프로덕션 모델에 적용할 수 있습니다.

이렇게 하려면 다음을 수행 해야 합니다.

1. 장치를 등록합니다.
1. 시나리오에 사용할 배포 구성을 결정 합니다.

    1. [ACI (Azure Container Instance)](#deploy-to-aci) 는 빠른 개발-테스트 배포에 적합 한 선택입니다.
    1. [AKS (Azure Kubernetes Service)](#deploy-to-aks) 는 확장 가능한 프로덕션 배포에 적합 합니다.

다음 다이어그램은 MLflow 배포 API를 사용 하 여 PyTorch, Tensorflow, scikit, ONNX 등의 프레임 워크에도 불구 하 고 기존 MLflow 모델을 Azure Machine Learning 웹 서비스로 배포할 수 있음을 보여 주며, 작업 영역에서 프로덕션 모델을 관리 합니다.

![ azure machine learning을 사용 하 여 mlflow 모델 배포](./media/how-to-use-mlflow/mlflow-diagram-deploy.png)


### <a name="deploy-to-aci"></a>ACI에 배포

[Deploy_configuration ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py&preserve-view=true#&preserve-view=truedeploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-) 메서드를 사용 하 여 배포 구성을 설정 합니다. 웹 서비스를 추적 하는 데 도움이 되는 태그와 설명을 추가할 수도 있습니다.

```python
from azureml.core.webservice import AciWebservice, Webservice

# Set the model path to the model folder created by your run
model_path = "model"

# Configure 
aci_config = AciWebservice.deploy_configuration(cpu_cores=1, 
                                                memory_gb=1, 
                                                tags={'method' : 'sklearn'}, 
                                                description='Diabetes model',
                                                location='eastus2')
```

그런 다음 Azure Machine Learning SDK [deploy](/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py&preserve-view=true#&preserve-view=truedeploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) 메서드를 사용 하 여 모델을 한 번에 등록 하 고 배포 합니다. 

```python
(webservice,model) = mlflow.azureml.deploy( model_uri='runs:/{}/{}'.format(run.id, model_path),
                      workspace=ws,
                      model_name='sklearn-model', 
                      service_name='diabetes-model-1', 
                      deployment_config=aci_config, 
                      tags=None, mlflow_home=None, synchronous=True)

webservice.wait_for_deployment(show_output=True)
```

### <a name="deploy-to-aks"></a>AKS에 배포

AKS에 배포 하려면 먼저 AKS 클러스터를 만듭니다. [ComputeTarget ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.computetarget?view=azure-ml-py&preserve-view=true#&preserve-view=truecreate-workspace--name--provisioning-configuration-) 메서드를 사용 하 여 AKS 클러스터를 만듭니다. 새 클러스터를 만드는 데 20-25 분이 걸릴 수 있습니다.

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Use the default configuration (can also provide parameters to customize)
prov_config = AksCompute.provisioning_configuration()

aks_name = 'aks-mlflow'

# Create the cluster
aks_target = ComputeTarget.create(workspace=ws, 
                                  name=aks_name, 
                                  provisioning_configuration=prov_config)

aks_target.wait_for_completion(show_output = True)

print(aks_target.provisioning_state)
print(aks_target.provisioning_errors)
```
[Deploy_configuration ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py&preserve-view=true#&preserve-view=truedeploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-) 메서드를 사용 하 여 배포 구성을 설정 합니다. 웹 서비스를 추적 하는 데 도움이 되는 태그와 설명을 추가할 수도 있습니다.

```python
from azureml.core.webservice import Webservice, AksWebservice

# Set the web service configuration (using default here with app insights)
aks_config = AksWebservice.deploy_configuration(enable_app_insights=True, compute_target_name='aks-mlflow')

```

그런 다음 Azure Machine Learning SDK [deploy ()]를 사용 하 여 모델을 한 번에 등록 하 고 배포한 후 Azure Machine Learning SDK [deploy](/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py&preserve-view=true#&preserve-view=truedeploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) 메서드를 사용 하 여 모델을 등록 하 고 배포 합니다. 

```python

# Webservice creation using single command
from azureml.core.webservice import AksWebservice, Webservice

# set the model path 
model_path = "model"

(webservice, model) = mlflow.azureml.deploy( model_uri='runs:/{}/{}'.format(run.id, model_path),
                      workspace=ws,
                      model_name='sklearn-model', 
                      service_name='my-aks', 
                      deployment_config=aks_config, 
                      tags=None, mlflow_home=None, synchronous=True)


webservice.wait_for_deployment()
```

서비스 배포에는 몇 분 정도 걸릴 수 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

작업 영역에서 기록 된 메트릭과 아티팩트를 사용 하지 않으려는 경우에는 개별적으로 삭제할 수 있는 기능을 현재 사용할 수 없습니다. 대신, 저장소 계정 및 작업 영역을 포함 하는 리소스 그룹을 삭제 하 여 요금이 발생 하지 않도록 합니다.

1. Azure Portal의 맨 왼쪽에서 **리소스 그룹**을 선택합니다.

   ![Azure Portal에서 삭제](./media/how-to-use-mlflow/delete-resources.png)

1. 목록에서 만든 리소스 그룹을 선택합니다.

1. **리소스 그룹 삭제**를 선택합니다.

1. 리소스 그룹 이름을 입력합니다. 그런 다음, **삭제**를 선택합니다.

## <a name="example-notebooks"></a>노트북 예제

[Azure ML을 사용한 MLflow 노트북](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/track-and-monitor-experiments/using-mlflow)은 이 문서에 나와 있는 개념을 시연하고 확장합니다.

## <a name="next-steps"></a>다음 단계

* [모델 관리](concept-model-management-and-deployment.md).
* [데이터 드리프트](how-to-monitor-data-drift.md)를 위한 프로덕션 모델 모니터링.
* [MLflow를 사용 하 여 실행 Azure Databricks 추적](how-to-use-mlflow-azure-databricks.md)합니다. 
