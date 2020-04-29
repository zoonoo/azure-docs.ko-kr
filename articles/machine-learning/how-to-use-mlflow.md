---
title: ML 실험을 위한 MLflow 추적
titleSuffix: Azure Machine Learning
description: Azure Machine Learning를 사용 하 여 MLflow를 설정 하 여 Databricks 클러스터, 로컬 환경 또는 VM 환경에서 만든 ML 모델에서 메트릭 및 아티팩트를 로깅합니다.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: nibaccam
ms.topic: conceptual
ms.date: 02/03/2020
ms.custom: seodec18
ms.openlocfilehash: dce7db9fc508c70d79be62a7e97b3bf52a316b22
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "76983701"
---
# <a name="track-models-metrics-with-mlflow-and-azure-machine-learning-preview"></a>MLflow 및 Azure Machine Learning를 사용 하 여 모델 메트릭 추적 (미리 보기)

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

이 문서에서는 mlflow의 추적 URI 및 로깅 API를 사용 하 [여 mlflow](https://mlflow.org/docs/latest/quickstart.html#using-the-tracking-api)실험 및 Azure Machine Learning 연결 하는 방법을 보여 줍니다. 이렇게 하면 [Azure Machine Learning 작업 영역](https://docs.microsoft.com/azure/machine-learning/concept-azure-machine-learning-architecture#workspaces)에서 실험 메트릭과 아티팩트를 추적 하 고 기록할 수 있습니다. 실험을 위해 MLflow 추적을 이미 사용 하 고 있는 경우 작업 영역에서 교육 메트릭과 모델을 저장할 중앙 집중화 된 안전 하 고 확장 가능한 위치를 제공 합니다.

<!--
+ Deploy your MLflow experiments as an Azure Machine Learning web service. By deploying as a web service, you can apply the Azure Machine Learning monitoring and data drift detection functionalities to your production models. 
-->

[Mlflow](https://www.mlflow.org) 는 machine learning 실험의 수명 주기를 관리 하기 위한 오픈 소스 라이브러리입니다. MLFlow 추적은 실험 환경에 상관 없이, 원격 계산 대상, 가상 머신 또는 Azure Databricks 클러스터에서 컴퓨터의 로컬로 로컬로 학습 실행 메트릭과 모델 아티팩트를 기록 하 고 추적 하는 MLflow의 구성 요소입니다. 

다음 다이어그램에서는 MLflow 추적을 사용 하 여 Azure Machine Learning 작업 영역에 실험의 실행 메트릭과 모델 아티팩트를 저장 하는 방법을 보여 줍니다.

![azure machine learning 다이어그램을 사용한 mlflow](./media/how-to-use-mlflow/mlflow-diagram-track.png)

> [!TIP]
> 이 문서의 정보는 주로 모델 학습 프로세스를 모니터링 하려는 데이터 과학자 및 개발자를 위한 것입니다. 할당량, 완료 된 학습 실행 또는 완료 된 모델 배포와 같은 Azure Machine Learning에서 리소스 사용 및 이벤트를 모니터링 하는 데 관심이 있는 관리자는 [모니터링 Azure Machine Learning](monitor-azure-machine-learning.md)을 참조 하세요.

## <a name="compare-mlflow-and-azure-machine-learning-clients"></a>MLflow 및 Azure Machine Learning 클라이언트 비교

 아래 표에는 Azure Machine Learning 사용할 수 있는 여러 클라이언트와 해당 기능 기능이 요약 되어 있습니다.

 MLflow 추적은 [Azure Machine Learning PYTHON SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)를 통해 사용 가능한 경우에만 사용할 수 있는 메트릭 로깅 및 아티팩트 저장소 기능을 제공 합니다.


| | MLflow&nbsp;추적 <!--& Deployment--> | Azure Machine Learning Python SDK |  Azure Machine Learning CLI | Azure Machine Learning Studio|
|---|---|---|---|---|
| 작업 영역 관리 |   | ✓ | ✓ | ✓ |
| 데이터 저장소 사용  |   | ✓ | ✓ | |
| 로그 메트릭      | ✓ | ✓ |   | |
| 아티팩트 업로드 | ✓ | ✓ |   | |
| 메트릭 보기     | ✓ | ✓ | ✓ | ✓ |
| 컴퓨팅 관리   |   | ✓ | ✓ | ✓ |

<!--| Deploy models    | ✓ | ✓ | ✓ | ✓ |
|Monitor model performance||✓|  |   |
| Detect data drift |   | ✓ |   | ✓ |
-->
## <a name="prerequisites"></a>전제 조건

* [MLflow를 설치 합니다.](https://mlflow.org/docs/latest/quickstart.html)
* 로컬 컴퓨터에 [AZURE MACHINE LEARNING sdk를 설치](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) 합니다. Sdk는 mlflow에 대 한 연결을 제공 하 여 작업 영역에 액세스 합니다.
* [Azure Machine Learning 작업 영역를 만듭니다](how-to-manage-workspace.md).

## <a name="track-local-runs"></a>로컬 실행 추적

MLflow 추적 Azure Machine Learning를 사용 하면 로컬 실행의 기록 된 메트릭과 아티팩트를 Azure Machine Learning 작업 영역에 저장할 수 있습니다.

Jupyter Notebook 또는 `azureml-mlflow` 코드 편집기에서 로컬로 실행 되는 실험에서 Azure Machine Learning를 사용 하 여 Mlflow 추적을 사용 하려면 패키지를 설치 합니다.

```shell
pip install azureml-mlflow
```

>[!NOTE]
>서비스를 개선 하기 위해 azureml 네임 스페이스는 자주 변경 됩니다. 따라서 이 네임 스페이스의 모든 것을 미리 보기로 간주하므로 Microsoft에서 완벽히 지원하지 않아도 됩니다.

`mlflow` 및 [`Workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py) 클래스를 가져와 MLFLOW의 추적 URI에 액세스 하 고 작업 영역을 구성 합니다.

다음 코드에서 메서드는 `get_mlflow_tracking_uri()` 작업 영역 `ws`에 고유한 추적 uri 주소를 할당 하 고 `set_tracking_uri()` 이 주소에 대 한 mlflow 추적 uri를 가리킵니다.

```Python
import mlflow
from azureml.core import Workspace

ws = Workspace.from_config()

mlflow.set_tracking_uri(ws.get_mlflow_tracking_uri())
```

>[!NOTE]
>추적 URI는 최대 1 시간 이하로 유효 합니다. 유휴 시간 이후 스크립트를 다시 시작 하는 경우 get_mlflow_tracking_uri API를 사용 하 여 새 URI를 가져옵니다.

MLflow 실험 이름을로 `set_experiment()` 설정 하 고를 사용 하 여 `start_run()`학습 실행을 시작 합니다. 그런 다음 `log_metric()` 를 사용 하 여 MLFLOW 로깅 API를 활성화 하 고 학습 실행 메트릭의 로깅을 시작 합니다.

```Python
experiment_name = 'experiment_with_mlflow'
mlflow.set_experiment(experiment_name)

with mlflow.start_run():
    mlflow.log_metric('alpha', 0.03)
```

## <a name="track-remote-runs"></a>원격 실행 추적

MLflow 추적 Azure Machine Learning를 사용 하면 원격 실행의 기록 된 메트릭과 아티팩트를 Azure Machine Learning 작업 영역에 저장할 수 있습니다.

원격 실행을 사용 하면 GPU를 사용 하는 가상 컴퓨터 또는 Machine Learning 컴퓨팅 클러스터와 같은 보다 강력한 계산을 통해 모델을 학습할 수 있습니다. 다양 한 계산 옵션에 대 한 자세한 내용은 [모델 학습을 위한 계산 대상 설정](how-to-set-up-training-targets.md) 을 참조 하세요.

[`Environment`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) 클래스를 사용 하 여 계산 및 학습 실행 환경을 구성 합니다. 환경 `mlflow` [`CondaDependencies`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py) 섹션 `azureml-mlflow` 에 및 pip 패키지를 포함 합니다. 그런 다음 [`ScriptRunConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig?view=azure-ml-py) 원격 계산을 계산 대상으로 사용 하 여를 생성 합니다.

```Python
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core import ScriptRunConfig

exp = Experiment(workspace = 'my_workspace',
                 name='my_experiment')

mlflow_env = Environment(name='mlflow-env')

cd = CondaDependencies.create(pip_packages=['mlflow', 'azureml-mlflow'])

mlflow_env.python.conda_dependencies = cd

src = ScriptRunConfig(source_directory='./my_script_location', script='my_training_script.py')

src.run_config.target = 'my-remote-compute-compute'
src.run_config.environment = mlflow_env
```

학습 스크립트에서 MLflow 로깅 `mlflow` api를 사용 하도록 가져오고 실행 메트릭의 로깅을 시작 합니다.

```Python
import mlflow

with mlflow.start_run():
    mlflow.log_metric('example', 1.23)
```

이 계산 및 학습 실행 구성을 사용 하 여 `Experiment.submit('train.py')` 메서드를 사용 하 여 실행을 제출 합니다. 이 메서드는 MLflow 추적 URI를 자동으로 설정 하 고 MLflow에서 작업 영역으로 로깅을 보냅니다.

```Python
run = exp.submit(src)
```

## <a name="track-azure-databricks-runs"></a>Azure Databricks 실행 추적

MLflow 추적 Azure Machine Learning를 사용 하면 Azure Databricks 실행의 기록 된 메트릭과 아티팩트를 Azure Machine Learning 작업 영역에 저장할 수 있습니다.

Azure Databricks를 사용 하 여 Mlflow 실험을 실행 하려면 먼저 [Azure Databricks 작업 영역 및 클러스터](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal)를 만들어야 합니다. 클러스터에서 필요한 함수 및 클래스에 대 한 액세스 권한이 있는지 확인 하기 위해 PyPi에서 *azureml* 를 설치 해야 합니다.

여기에서 실험 노트북을 가져오고, Azure Databricks 클러스터에 연결 하 고, 실험을 실행 합니다. 

### <a name="install-libraries"></a>라이브러리 설치

클러스터에 라이브러리를 설치 하려면 **라이브러리** 탭으로 이동 하 여 **새로 설치** 를 클릭 합니다.

 ![azure machine learning 다이어그램을 사용한 mlflow](./media/how-to-use-mlflow/azure-databricks-cluster-libraries.png)

**패키지** 필드에 azureml-mlflow를 입력 한 다음 설치를 클릭 합니다. 필요에 따라이 단계를 반복 하 여 실험을 위해 클러스터에 다른 추가 패키지를 설치 합니다.

 ![azure machine learning 다이어그램을 사용한 mlflow](./media/how-to-use-mlflow/install-libraries.png)

### <a name="set-up-your-notebook-and-workspace"></a>노트북 및 작업 영역 설정

클러스터가 설정 되 면 실험 노트북을 가져오고 열고 클러스터를 연결 합니다.

다음 코드는 실험 노트북에 있어야 합니다. 이 코드는 작업 영역을 인스턴스화하기 위해 Azure 구독에 대 한 세부 정보를 가져옵니다. 이 코드는 기존 리소스 그룹 및 Azure Machine Learning 작업 영역을가지고 있는 것으로 가정 합니다. 그렇지 않은 경우에는 [만들](how-to-manage-workspace.md)수 있습니다. 

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

#### <a name="connect-your-azure-databricks-and-azure-machine-learning-workspaces"></a>Azure Databricks 및 Azure Machine Learning 작업 영역 연결

[Azure Portal](https://ms.portal.azure.com)에서 Azure Databricks (adb) 작업 영역을 신규 또는 기존 Azure Machine Learning 작업 영역에 연결할 수 있습니다. 이렇게 하려면 ADB 작업 영역으로 이동 하 고 오른쪽 아래에 있는 **Azure Machine Learning 작업 영역 연결** 단추를 선택 합니다. 작업 영역을 연결 하면 Azure Machine Learning 작업 영역에서 실험 데이터를 추적할 수 있습니다. 

### <a name="link-mlflow-tracking-to-your-workspace"></a>작업 영역에 MLflow 추적 링크

작업 영역을 인스턴스화한 후 MLflow 추적 URI를 설정 합니다. 이렇게 하면 MLflow 추적을 Azure Machine Learning 작업 영역에 연결할 수 있습니다. 연결 후 모든 실험은 관리 되는 Azure Machine Learning 추적 서비스에 배치 됩니다.

#### <a name="directly-set-mlflow-tracking-in-your-notebook"></a>노트북에서 MLflow 추적 직접 설정

```python
uri = ws.get_mlflow_tracking_uri()
mlflow.set_tracking_uri(uri)
```

학습 스크립트에서 mlflow를 가져와서 MLflow 로깅 Api를 사용 하 고 실행 메트릭 로깅을 시작 합니다. 다음 예에서는 epoch 손실 메트릭을 기록 합니다. 

```python
import mlflow 
mlflow.log_metric('epoch_loss', loss.item()) 
```

#### <a name="automate-setting-mlflow-tracking"></a>MLflow 추적 설정 자동화

클러스터의 모든 이후 실험 노트북 세션에서 추적 URI를 수동으로 설정 하는 대신이 [Azure Machine Learning 추적 클러스터 초기화 스크립트](https://github.com/Azure/MachineLearningNotebooks/blob/3ce779063b000e0670bdd1acc6bc3a4ee707ec13/how-to-use-azureml/azure-databricks/linking/README.md)를 사용 하 여 자동으로 수행 합니다.

올바르게 구성 되 면 mlflow 사용자 인터페이스를 통해 또는 mlflow 클라이언트를 사용 하 여 Azure Machine Learning REST API 및 모든 클라이언트에서 MLflow 추적 데이터를 볼 수 있고 Azure Databricks에서 볼 수 있습니다.

## <a name="view-metrics-and-artifacts-in-your-workspace"></a>작업 영역에서 메트릭 및 아티팩트 보기

MLflow 로깅의 메트릭과 아티팩트가 작업 영역에 유지 됩니다. 언제 든 지 작업 영역으로 이동 하 고 [Azure Machine Learning studio](https://ml.azure.com)의 작업 영역에서 이름별로 실험을 찾을 수 있습니다.  또는 아래 코드를 실행 합니다. 

```python
run.get_metrics()
ws.get_details()
```

<!-- ## Deploy MLflow models as a web service

Deploying your MLflow experiments as an Azure Machine Learning web service allows you to leverage the Azure Machine Learning model management and data drift detection capabilities and apply them to your production models.

The following diagram demonstrates that with the MLflow deploy API you can deploy your existing MLflow models as an Azure Machine Learning web service, despite their frameworks--PyTorch, Tensorflow, scikit-learn, ONNX, etc., and manage your production models in your workspace.

![mlflow with azure machine learning diagram](./media/how-to-use-mlflow/mlflow-diagram-deploy.png)

### Log your model

Before you can deploy, be sure that your model is saved so you can reference it and its path location for deployment. In your training script, there should be code similar to the following [mlflow.sklearn.log_model()](https://www.mlflow.org/docs/latest/python_api/mlflow.sklearn.html) method, that saves your model to the specified outputs directory. 

```python
# change sklearn to pytorch, tensorflow, etc. based on your experiment's framework 
import mlflow.sklearn

# Save the model to the outputs directory for capture
mlflow.sklearn.log_model(regression_model, model_save_path)
```
>[!NOTE]
> Include the `conda_env` parameter to pass a dictionary representation of the dependencies and environment this model should be run in.

### Retrieve model from previous run

To retrieve the run, you need the run ID and the path in run history of where the model was saved. 

```python
# gets the list of runs for your experiment as an array
experiment_name = 'experiment-with-mlflow'
exp = ws.experiments[experiment_name]
runs = list(exp.get_runs())

# get the run ID and the path in run history
runid = runs[0].id
model_save_path = 'model'
```

### Create Docker image

The `mlflow.azureml.build_image()` function builds a Docker image from the saved model in a framework-aware manner. It automatically creates the framework-specific inferencing wrapper code and specifies package dependencies for you. Specify the model path, your workspace, run ID and other parameters.

The following code builds a docker image using *runs:/<run.id>/model* as the model_uri path for a Scikit-learn experiment.

```python
import mlflow.azureml

azure_image, azure_model = mlflow.azureml.build_image(model_uri='runs:/{}/{}'.format(runid, model_save_path),
                                                      workspace=ws,
                                                      model_name='sklearn-model',
                                                      image_name='sklearn-image',
                                                      synchronous=True)
```
The creation of the Docker image can take several minutes. 

### Deploy the Docker image 

After the image is created, use the Azure Machine Learning SDK to deploy the image as a web service.

First, specify the deployment configuration. Azure Container Instance (ACI) is a suitable choice for a quick dev-test deployment, while Azure Kubernetes Service (AKS) is suitable for scalable production deployments.

#### Deploy to ACI

Set up your deployment configuration with the [deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-) method. You can also add tags and descriptions to help keep track of your web service.

```python
from azureml.core.webservice import AciWebservice, Webservice

# Configure 
aci_config = AciWebservice.deploy_configuration(cpu_cores=1, 
                                                memory_gb=1, 
                                                tags={'method' : 'sklearn'}, 
                                                description='Diabetes model',
                                                location='eastus2')
```

Then, deploy the image by using the Azure Machine Learning SDK [deploy_from_image()](/python/api/azureml-core/azureml.core.webservice.webservice(class)?view=azure-ml-py#deploy-from-image-workspace--name--image--deployment-config-none--deployment-target-none--overwrite-false-) method. 

```python
webservice = Webservice.deploy_from_image( image=azure_image, 
                                           workspace=ws, 
                                           name='diabetes-model-1', 
                                           deployment_config=aci_config)

webservice.wait_for_deployment(show_output=True)
```
#### Deploy to AKS

To deploy to AKS, first create an AKS cluster and bring over the Docker image you want to deploy. For this example, bring over the previously created image from the ACI deployment.

To get the image from the previous ACI deployment use the [Image](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.image.image?view=azure-ml-py) class. 

```python
from azureml.core.image import Image

# Get the image by name, you can change this based on the image you want to deploy
myimage = Image(workspace=ws, name='sklearn-image') 
```

Create an AKS cluster using the [ComputeTarget.create()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.computetarget?view=azure-ml-py#create-workspace--name--provisioning-configuration-) method. It may take 20-25 minutes to create a new cluster.

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
Set up your deployment configuration with the [deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-) method. You can also add tags and descriptions to help keep track of your web service.

```python
from azureml.core.webservice import Webservice, AksWebservice
from azureml.core.image import ContainerImage

# Set the web service configuration (using default here with app insights)
aks_config = AksWebservice.deploy_configuration(enable_app_insights=True)

# Unique service name
service_name ='aks-service'
```

Then, deploy the image by using the Azure Machine Learning SDK [deploy_from_image()](/python/api/azureml-core/azureml.core.webservice.webservice(class)?view=azure-ml-py#deploy-from-image-workspace--name--image--deployment-config-none--deployment-target-none--overwrite-false-) method. 

```python
# Webservice creation using single command
aks_service = Webservice.deploy_from_image( workspace=ws, 
                                            name=service_name,
                                            deployment_config = aks_config
                                            image = myimage,
                                            deployment_target = aks_target)

aks_service.wait_for_deployment(show_output=True)
```

The service deployment can take several minutes.

## Clean up resources

If you don't plan to use the logged metrics and artifacts in your workspace, the ability to delete them individually is currently unavailable. Instead, delete the resource group that contains the storage account and workspace, so you don't incur any charges:

1. In the Azure portal, select **Resource groups** on the far left.

   ![Delete in the Azure portal](./media/how-to-use-mlflow/delete-resources.png)

1. From the list, select the resource group you created.

1. Select **Delete resource group**.

1. Enter the resource group name. Then select **Delete**.

 -->

 ## <a name="example-notebooks"></a>노트북 예제

[AZURE ML 노트북을 사용 하는 Mlflow](https://aka.ms/azureml-mlflow-examples) 는이 문서에 제시 된 개념을 시연 하 고 확장 합니다.

## <a name="next-steps"></a>다음 단계
* [모델을 관리](concept-model-management-and-deployment.md)합니다.
* [데이터 드리프트](how-to-monitor-data-drift.md)를 위해 프로덕션 모델을 모니터링 합니다.
