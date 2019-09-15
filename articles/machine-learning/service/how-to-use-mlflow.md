---
title: MLflow 사용
titleSuffix: Azure Machine Learning
description: Azure Machine Learning를 사용 하 여 MLflow를 설정 하 여 메트릭을 & 아티팩트를 기록 하 고 Databricks, 로컬 환경 또는 VM 환경에서 모델을 배포 합니다.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: nibaccam
ms.topic: conceptual
ms.date: 08/07/2019
ms.custom: seodec18
ms.openlocfilehash: b1b2255b4e0f5aa34e3c7159b00156aee5224928
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/15/2019
ms.locfileid: "70999280"
---
# <a name="track-metrics-and-deploy-models-with-mlflow-and-azure-machine-learning-preview"></a>MLflow 및 Azure Machine Learning를 사용 하 여 메트릭 추적 및 모델 배포 (미리 보기)

이 문서에서는 Azure Machine Learning를 사용 하 여 [mlflow 추적](https://mlflow.org/docs/latest/quickstart.html#using-the-tracking-api)URI 및 로깅 API를 사용 하도록 설정 하는 방법을 보여 줍니다. 이렇게 하면 다음 작업을 수행할 수 있습니다.

+ [Azure Machine Learning 작업 영역](https://docs.microsoft.com/azure/machine-learning/service/concept-azure-machine-learning-architecture#workspaces)에서 실험 메트릭과 아티팩트를 추적 하 고 기록 합니다. 실험에 MLflow 추적을 이미 사용 하 고 있는 경우 작업 영역에서 교육 메트릭과 모델을 저장할 수 있는 중앙 집중화 된 안전 하 고 확장 가능한 위치를 제공 합니다.

+ MLflow 실험을 Azure Machine Learning 웹 서비스로 배포 합니다. 웹 서비스로 배포 하 여 Azure Machine Learning 모니터링 및 데이터 드리프트 검색 기능을 프로덕션 모델에 적용할 수 있습니다. 

[Mlflow](https://www.mlflow.org) 는 machine learning 실험의 수명 주기를 관리 하기 위한 오픈 소스 라이브러리입니다. MLFlow 추적은 실험 환경에 상관 없이 (가상 머신, 원격 계산 클러스터에서 로컬로, Azure Databricks에도 로컬에서) 학습 실행 메트릭과 모델 아티팩트를 기록 하 고 추적 하는 MLflow의 구성 요소입니다.

다음 다이어그램은 MLflow 추적을 사용 하는 경우 가상 컴퓨터의 원격 계산 대상에 있든, 컴퓨터에서 로컬로 또는 Azure Databricks 클러스터에서 로컬로 실행 되 고, 실행 메트릭과 저장소 모델 아티팩트를 추적 하는 모든 실험을 수행할 수 있음을 보여 줍니다. Azure Machine Learning 작업 영역에 있습니다.

![azure machine learning 다이어그램을 사용한 mlflow](media/how-to-use-mlflow/mlflow-diagram-track.png)

## <a name="compare-mlflow-and-azure-machine-learning-clients"></a>MLflow 및 Azure Machine Learning 클라이언트 비교

 아래 표에는 Azure Machine Learning 사용할 수 있는 여러 클라이언트와 해당 기능 기능이 요약 되어 있습니다.

 MLflow 추적은 [Azure Machine Learning PYTHON SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)를 통해 사용 가능한 경우에만 사용할 수 있는 메트릭 로깅 및 아티팩트 저장소 기능을 제공 합니다.


| | MLflow 추적 & 배포 | Azure Machine Learning Python SDK |  Azure Machine Learning CLI | Azure Portal 또는 작업 영역 방문 페이지 (미리 보기)|
|---|---|---|---|---|
| 작업 영역 관리 |   | ✓ | ✓ | ✓ |
| 데이터 저장소 사용  |   | ✓ | ✓ | |
| 로그 메트릭      | ✓ | ✓ |   | |
| 아티팩트 업로드 | ✓ | ✓ |   | |
| 메트릭 보기     | ✓ | ✓ | ✓ | ✓ |
| 컴퓨팅 관리   |   | ✓ | ✓ | ✓ |
| 모델 배포    | ✓ | ✓ | ✓ | ✓ |
|모델 성능 모니터링||✓|  |   |
| 데이터 드리프트 검색 |   | ✓ |   | ✓ |

## <a name="prerequisites"></a>필수 구성 요소

* [MLflow를 설치 합니다.](https://mlflow.org/docs/latest/quickstart.html)
* 로컬 컴퓨터에 [AZURE MACHINE LEARNING sdk를 설치](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) 합니다. Sdk는 mlflow에 대 한 연결을 제공 하 여 작업 영역에 액세스 합니다.
* [Azure Machine Learning 작업 영역를 만듭니다](how-to-manage-workspace.md).

## <a name="track-local-runs"></a>로컬 실행 추적

MLflow 추적 Azure Machine Learning를 사용 하면 로컬 실행의 기록 된 메트릭과 아티팩트를 Azure Machine Learning 작업 영역에 저장할 수 있습니다.

Jupyter Notebook 또는 `azureml-contrib-run` 코드 편집기에서 로컬로 실행 되는 실험에서 Azure Machine Learning를 사용 하 여 mlflow 추적을 사용 하려면 패키지를 설치 합니다.

```shell
pip install azureml-contrib-run
```

>[!NOTE]
>서비스를 개선 하기 위해 azureml 네임 스페이스는 자주 변경 됩니다. 따라서 이 네임 스페이스의 모든 것을 미리 보기로 간주하므로 Microsoft에서 완벽히 지원하지 않아도 됩니다.

`mlflow` [및`Workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py) 클래스를 가져와 mlflow의 추적 URI에 액세스 하 고 작업 영역을 구성 합니다.

다음 코드에서 메서드는 `get_mlflow_tracking_uri()` `ws`작업 영역에 고유한 추적 uri 주소를 할당 하 고 `set_tracking_uri()` 이 주소에 대 한 mlflow 추적 uri를 가리킵니다.

```Python
import mlflow
from azureml.core import Workspace

ws = Workspace.from_config()

mlflow.set_tracking_uri(ws.get_mlflow_tracking_uri())
```

>[!NOTE]
>추적 URI는 최대 1 시간 이하로 유효 합니다. 유휴 시간 이후 스크립트를 다시 시작 하는 경우 get_mlflow_tracking_uri API를 사용 하 여 새 URI를 가져옵니다.

Mlflow 실험 이름을로 `set_experiment()` 설정 하 고를 사용 하 여 `start_run()`학습 실행을 시작 합니다. 그런 다음 `log_metric()` 를 사용 하 여 mlflow 로깅 API를 활성화 하 고 학습 실행 메트릭의 로깅을 시작 합니다.

```Python
experiment_name = 'experiment_with_mlflow'
mlflow.set_experiment(experiment_name)

with mlflow.start_run():
    mlflow.log_metric('alpha', 0.03)
```

## <a name="track-remote-runs"></a>원격 실행 추적

MLflow 추적 Azure Machine Learning를 사용 하면 원격 실행의 기록 된 메트릭과 아티팩트를 Azure Machine Learning 작업 영역에 저장할 수 있습니다.

원격 실행을 사용 하면 GPU를 사용 하는 가상 컴퓨터 또는 Machine Learning 컴퓨팅 클러스터와 같은 보다 강력한 계산을 통해 모델을 학습할 수 있습니다. 다양 한 계산 옵션에 대 한 자세한 내용은 [모델 학습을 위한 계산 대상 설정](how-to-set-up-training-targets.md) 을 참조 하세요.

[`Environment`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) 클래스를 사용 하 여 계산 및 학습 실행 환경을 구성 합니다. 환경 `mlflow` [섹션`CondaDependencies`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py) 에 및 pip 패키지를 `azure-contrib-run` 포함 합니다. 그런 다음 [`ScriptRunConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig?view=azure-ml-py) 원격 계산을 계산 대상으로 사용 하 여를 생성 합니다.

```Python
from azureml.core import Environment
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core import ScriptRunConfig

exp = Experiment(workspace = 'my_workspace',
                 name='my_experiment')

mlflow_env = Environment(name='mlflow-env')

cd = CondaDependencies.create(pip_packages=['mlflow', 'azureml-contrib-run'])

mlflow_env.python.conda_dependencies = cd

src = ScriptRunConfig(source_directory='./my_script_location', script='my_training_script.py')

src.run_config.target = 'my-remote-compute-compute'
src.run_config.environment = mlflow_env
```

학습 스크립트에서 mlflow 로깅 `mlflow` api를 사용 하도록 가져오고 실행 메트릭의 로깅을 시작 합니다.

```Python
import mlflow

with mlflow.start_run():
    mlflow.log_metric('example', 1.23)
```

이 계산 및 학습 실행 구성을 사용 하 여 `Experiment.submit('train.py')` 메서드를 사용 하 여 실행을 제출 합니다. 그러면 MLflow 추적 URI가 자동으로 설정 되 고 로깅이 MLflow에서 작업 영역으로 전송 됩니다.

```Python
run = exp.submit(src)
```

## <a name="track-azure-databricks-runs"></a>Azure Databricks 실행 추적

MLflow 추적 Azure Machine Learning를 사용 하면 Azure Machine Learning 작업 영역에서 Databricks 실행의 기록 된 메트릭과 아티팩트를 저장할 수 있습니다.

Azure Databricks를 사용 하 여 Mlflow 실험을 실행 하려면 먼저 [Azure Databricks 작업 영역 및 클러스터](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal) 를 만들어야 합니다.

클러스터에서 필요한 함수 및 클래스에 대 한 액세스 권한이 있는지 확인 하기 위해 PyPi에서 *azureml* 를 설치 해야 합니다.

### <a name="install-libraries"></a>라이브러리 설치

클러스터에 라이브러리를 설치 하려면 **라이브러리** 탭으로 이동 하 여 **새로 설치** 를 클릭 합니다.

 ![azure machine learning 다이어그램을 사용한 mlflow](media/how-to-use-mlflow/azure-databricks-cluster-libraries.png)

**패키지** 필드에 azureml-mlflow를 입력 한 다음 설치를 클릭 합니다. 필요에 따라이 단계를 반복 하 여 실험을 위해 클러스터에 다른 추가 패키지를 설치 합니다.

 ![azure machine learning 다이어그램을 사용한 mlflow](media/how-to-use-mlflow/install-libraries.png)

### <a name="set-up-your-notebook-and-workspace"></a>노트북 및 작업 영역 설정

클러스터가 설정 되 면 실험 노트북을 가져오고 열고 클러스터를 연결 합니다.

다음 코드는 실험 노트북에 있어야 합니다. 작업 영역을 인스턴스화하기 위해 Azure 구독에 대 한 세부 정보를 가져옵니다. 여기에는 기존 리소스 그룹 및 Azure Machine Learning 작업 영역이 있는 것으로 가정 합니다. 그렇지 않은 경우에는 [만들](how-to-manage-workspace.md)수 있습니다. 

```python
import mlflow
import mlflow.azureml
import azureml.mlflow
import azureml.core

from azureml.core import Workspace
from azureml.mlflow import get_portal_url

subscription_id = 'subscription_id'

# Azure Machine Learning resource group NOT the managed resource group
resource_group = 'resource_group_name' 

#Azure Machine Learning workspace name, NOT Azure Databricks workspace
workspace_name = 'workspace_name'  

# Instantiate Azure Machine Learning workspace
ws = Workspace.get(name=workspace_name,
                   subscription_id=subscription_id,
                   resource_group=resource_group)

```
### <a name="link-mlflow-tracking-to-your-workspace"></a>작업 영역에 MLflow 추적 링크
작업 영역을 인스턴스화한 후 MLflow 추적 URI를 설정 합니다. 이렇게 하면 MLflow 추적을 Azure Machine Learning 작업 영역에 연결할 수 있습니다. 그러면 모든 실험은 관리 되는 Azure Machine Learning 추적 서비스에 배치 됩니다.

```python
uri = ws.get_mlflow_tracking_uri()
mlflow.set_tracking_uri(uri)
```

학습 스크립트에서 mlflow를 가져와서 MLflow 로깅 Api를 사용 하 고 실행 메트릭 로깅을 시작 합니다. 다음 예에서는 epoch 손실 메트릭을 기록 합니다. 

```python
import mlflow 
mlflow.log_metric('epoch_loss', loss.item()) 
```

## <a name="view-metrics-and-artifacts-in-your-workspace"></a>작업 영역에서 메트릭 및 아티팩트 보기

MLflow 로깅의 메트릭과 아티팩트가 작업 영역에 유지 됩니다. 언제 든 지 작업 영역으로 이동 하 여 [Azure Portal](https://portal.azure.com) 또는 [작업 영역 방문 페이지 (미리 보기)](https://ml.azure.com)에서 이름별로 실험을 찾습니다.  또는 아래 코드를 실행 합니다. 

```python
run.get_metrics()
ws.get_details()
```

## <a name="deploy-mlflow-models-as-a-web-service"></a>MLflow 모델을 웹 서비스로 배포

MLflow 실험을 Azure Machine Learning 웹 서비스로 배포 하면 Azure Machine Learning 모델 관리 및 데이터 드리프트 검색 기능을 활용 하 여 프로덕션 모델에 적용할 수 있습니다.

다음 다이어그램은 MLflow 배포 API를 사용 하 여 PyTorch, Tensorflow, scikit, ONNX 등의 프레임 워크에도 불구 하 고 기존 MLflow 모델을 Azure Machine Learning 웹 서비스로 배포할 수 있음을 보여 줍니다. 작업 영역.

![azure machine learning 다이어그램을 사용한 mlflow](media/how-to-use-mlflow/mlflow-diagram-deploy.png)

### <a name="log-your-model"></a>모델 기록

를 배포 하려면 먼저 모델을 저장 하 여 배포할 수 있도록 모델을 저장 해야 합니다. 학습 스크립트에는 지정 된 출력 디렉터리에 모델을 저장 하는 다음과 같은 코드가 있어야 합니다. [mlflow.sklearn.log_model()](https://www.mlflow.org/docs/latest/python_api/mlflow.sklearn.html) 

```python
# change sklearn to pytorch, tensorflow, etc. based on your experiment's framework 
import mlflow.sklearn

# Save the model to the outputs directory for capture
mlflow.sklearn.log_model(regression_model, model_save_path)
```
>[!NOTE]
> `conda_env` 매개 변수를 포함 하 여이 모델이 실행 되어야 하는 종속성 및 환경의 사전 표현을 전달 합니다.

### <a name="retrieve-model-from-previous-run"></a>이전 실행에서 모델 검색

원하는 실행을 검색 하려면 모델을 저장 한 실행 기록의 실행 ID와 경로가 필요 합니다. 

```python
# gets the list of runs for your experiment as an array
experiment_name = 'experiment-with-mlflow'
exp = ws.experiments[experiment_name]
runs = list(exp.get_runs())

# get the run ID and the path in run history
runid = runs[0].id
model_save_path = 'model'
```

### <a name="create-docker-image"></a>Docker 이미지 만들기

함수 `mlflow.azureml.build_image()` 는 저장 된 모델에서 프레임 워크 인식 방식으로 Docker 이미지를 빌드합니다. 자동으로 프레임 워크 별 추론 래퍼 코드를 만들고 패키지 종속성을 지정 합니다. 모델 경로, 작업 영역, 실행 ID 및 기타 매개 변수를 지정 합니다.

다음 코드는 Scikit를 사용 하 < 여 model_uri *>/tmodel* 을 사용 하 여 docker 이미지를 작성 합니다.

```python
import mlflow.azureml

azure_image, azure_model = mlflow.azureml.build_image(model_uri='runs:/{}/{}'.format(runid, model_save_path),
                                                      workspace=ws,
                                                      model_name='sklearn-model',
                                                      image_name='sklearn-image',
                                                      synchronous=True)
```
Docker 이미지를 만들려면 몇 분 정도 걸릴 수 있습니다. 

### <a name="deploy-the-docker-image"></a>Docker 이미지 배포 

이미지를 만든 후 Azure Machine Learning SDK를 사용 하 여 이미지를 웹 서비스로 배포 합니다.

먼저 배포 구성을 지정 합니다. ACI (azure Container Instance)는 빠른 개발-테스트 배포에 적합 한 선택이 고, AKS (Azure Kubernetes Service)는 확장 가능한 프로덕션 배포에 적합 합니다.

#### <a name="deploy-to-aci"></a>ACI에 배포

[Deploy_configuration ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-) 메서드를 사용 하 여 배포 구성을 설정 합니다. 웹 서비스를 추적 하는 데 도움이 되는 태그와 설명을 추가할 수도 있습니다.

```python
from azureml.core.webservice import AciWebservice, Webservice

# Configure 
aci_config = AciWebservice.deploy_configuration(cpu_cores=1, 
                                                memory_gb=1, 
                                                tags={'method' : 'sklearn'}, 
                                                description='Diabetes model',
                                                location='eastus2')
```

그런 다음 Azure Machine Learning SDK의 [deploy_from_image ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice(class)?view=azure-ml-py#deploy-from-image-workspace--name--image--deployment-config-none--deployment-target-none-) 메서드를 사용 하 여 이미지를 배포 합니다. 

```python
webservice = Webservice.deploy_from_image( image=azure_image, 
                                           workspace=ws, 
                                           name='diabetes-model-1', 
                                           deployment_config=aci_config)

webservice.wait_for_deployment(show_output=True)
```
#### <a name="deploy-to-aks"></a>AKS에 배포

AKS에 배포 하려면 AKS 클러스터를 만들고 배포 하려는 Docker 이미지를 가져와야 합니다. 이 예제에서는 ACI 배포에서 이전에 만든 이미지를 가져옵니다.

이전 ACI 배포에서 이미지를 가져오려면 [image](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.image.image?view=azure-ml-py) 클래스를 사용 합니다. 

```python
from azureml.core.image import Image

# Get the image by name, you can change this based on the image you want to deploy
myimage = Image(workspace=ws, name='sklearn-image') 
```

AKS 계산 만들기 새 클러스터를 만드는 데 20-25 분이 걸릴 수 있습니다.

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
[Deploy_configuration ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-) 메서드를 사용 하 여 배포 구성을 설정 합니다. 웹 서비스를 추적 하는 데 도움이 되는 태그와 설명을 추가할 수도 있습니다.

```python
from azureml.core.webservice import Webservice, AksWebservice
from azureml.core.image import ContainerImage

# Set the web service configuration (using default here with app insights)
aks_config = AksWebservice.deploy_configuration(enable_app_insights=True)

# Unique service name
service_name ='aks-service'
```

그런 다음 Azure Machine Learning SDK의 [deploy_from_image ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice(class)?view=azure-ml-py#deploy-from-image-workspace--name--image--deployment-config-none--deployment-target-none-) 메서드를 사용 하 여 이미지를 배포 합니다. 

```python
# Webservice creation using single command
aks_service = Webservice.deploy_from_image( workspace=ws, 
                                            name=service_name,
                                            deployment_config = aks_config
                                            image = myimage,
                                            deployment_target = aks_target)

aks_service.wait_for_deployment(show_output=True)
```

서비스 배포에는 몇 분 정도 걸릴 수 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

작업 영역에서 기록 된 메트릭과 아티팩트를 사용 하지 않으려는 경우에는 개별적으로 삭제할 수 있는 기능을 현재 사용할 수 없습니다. 대신, 저장소 계정 및 작업 영역을 포함 하는 리소스 그룹을 삭제 하 여 요금이 발생 하지 않도록 합니다.

1. Azure Portal의 맨 왼쪽에서 **리소스 그룹**을 선택합니다.

   ![Azure Portal에서 삭제](media/how-to-use-mlflow/delete-resources.png)

1. 목록에서 만든 리소스 그룹을 선택합니다.

1. **리소스 그룹 삭제**를 선택합니다.

1. 리소스 그룹 이름을 입력합니다. 그런 다음, **삭제**를 선택합니다.


## <a name="example-notebooks"></a>노트북 예제

[AZURE ML 노트북을 사용 하는 Mlflow](https://aka.ms/azureml-mlflow-examples) 는이 문서에 제시 된 개념을 시연 하 고 확장 합니다.

## <a name="next-steps"></a>다음 단계
* [모델을 관리](concept-model-management-and-deployment.md)합니다.
* [데이터 드리프트](how-to-monitor-data-drift.md)를 위해 프로덕션 모델을 모니터링 합니다.
