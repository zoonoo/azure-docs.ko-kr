---
title: ML 실험을 위한 MLflow 추적
titleSuffix: Azure Machine Learning
description: Azure 기계 학습을 사용하여 MLflow를 설정하여 Databricks 클러스터, 로컬 환경 또는 VM 환경에서 만든 ML 모델의 메트릭 및 아티팩트를 기록합니다.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76983701"
---
# <a name="track-models-metrics-with-mlflow-and-azure-machine-learning-preview"></a>MLflow 및 Azure 기계 학습을 통해 모델 메트릭 추적(미리 보기)

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

이 문서에서는 MLflow 의 추적 URI 및 로깅 API(총칭하여 [MLflow 추적)를](https://mlflow.org/docs/latest/quickstart.html#using-the-tracking-api)사용하여 MLflow 실험 및 Azure 기계 학습을 연결하는 방법을 보여 줍니다. 이렇게 하면 [Azure Machine Learning 작업 영역에서](https://docs.microsoft.com/azure/machine-learning/concept-azure-machine-learning-architecture#workspaces)실험 메트릭 및 아티팩트를 추적하고 기록할 수 있습니다. 실험에 MLflow Tracking을 이미 사용하는 경우 작업 영역은 교육 메트릭 및 모델을 저장할 중앙 집중식, 보안 및 확장 가능한 위치를 제공합니다.

<!--
+ Deploy your MLflow experiments as an Azure Machine Learning web service. By deploying as a web service, you can apply the Azure Machine Learning monitoring and data drift detection functionalities to your production models. 
-->

[MLflow는](https://www.mlflow.org) 기계 학습 실험의 수명 주기를 관리하기 위한 오픈 소스 라이브러리입니다. MLFlow 추적은 컴퓨터, 원격 계산 대상, 가상 컴퓨터 또는 Azure Databricks 클러스터에서 실험 환경과 상관없이 학습 실행 메트릭 및 모델 아티팩트를 기록하고 추적하는 MLflow의 구성 요소입니다. 

다음 다이어그램은 MLflow 추적을 통해 실험의 실행 메트릭을 추적하고 Azure 기계 학습 작업 영역에서 모델 아티팩트를 저장한다는 것을 보여 줍니다.

![azure 기계 학습 다이어그램이 있는 mlflow](./media/how-to-use-mlflow/mlflow-diagram-track.png)

> [!TIP]
> 이 문서의 정보는 주로 모델 학습 프로세스를 모니터링하려는 데이터 과학자 및 개발자를 위한 것입니다. 할당량, 완료된 교육 실행 또는 완료된 모델 배포와 같은 Azure 기계 학습의 리소스 사용량 및 이벤트를 모니터링하는 데 관심이 있는 관리자는 [Azure 기계 학습 모니터링을](monitor-azure-machine-learning.md)참조하십시오.

## <a name="compare-mlflow-and-azure-machine-learning-clients"></a>MLflow 및 Azure 기계 학습 클라이언트 비교

 아래 표에는 Azure 기계 학습을 사용할 수 있는 다양한 클라이언트와 해당 기능 기능이 요약되어 있습니다.

 MLflow 추적은 [Azure 기계 학습 파이썬 SDK를](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)통해서만 사용할 수 있는 메트릭 로깅 및 아티팩트 저장소 기능을 제공합니다.


| | MLflow&nbsp;추적 <!--& Deployment--> | Azure 기계 학습 파이썬 SDK |  Azure Machine Learning CLI | Azure Machine Learning Studio|
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
## <a name="prerequisites"></a>사전 요구 사항

* [MLflow를 설치합니다.](https://mlflow.org/docs/latest/quickstart.html)
* 로컬 컴퓨터에 [Azure 기계 학습 SDK를](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) 설치SDK는 MLflow가 작업 영역에 액세스하는 연결을 제공합니다.
* [Azure 기계 학습 작업 영역을 만듭니다.](how-to-manage-workspace.md)

## <a name="track-local-runs"></a>로컬 실행 추적

Azure 기계 학습을 사용하여 MLflow 추적을 사용하면 로컬 실행에서 기록된 메트릭 및 아티팩트를 Azure 기계 학습 작업 영역에 저장할 수 있습니다.

Jupyter 노트북 또는 코드 편집기에서 로컬로 실행되는 실험에서 Azure 기계 학습을 사용한 MLflow 추적을 사용하도록 `azureml-mlflow` 패키지를 설치합니다.

```shell
pip install azureml-mlflow
```

>[!NOTE]
>azureml.contrib 네임스페이스는 서비스를 개선하기 위해 노력함에 따라 자주 변경됩니다. 따라서 이 네임 스페이스의 모든 것을 미리 보기로 간주하므로 Microsoft에서 완벽히 지원하지 않아도 됩니다.

`mlflow` 및 [`Workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py) 클래스를 가져와 MLflow의 추적 URI에 액세스하고 작업 영역을 구성합니다.

다음 코드에서 `get_mlflow_tracking_uri()` 메서드는 고유한 추적 URI 주소를 작업 영역에 `ws`할당하고 `set_tracking_uri()` MLflow 추적 URI를 해당 주소로 가리킵니다.

```Python
import mlflow
from azureml.core import Workspace

ws = Workspace.from_config()

mlflow.set_tracking_uri(ws.get_mlflow_tracking_uri())
```

>[!NOTE]
>추적 URI는 최대 1시간 이하까지 유효합니다. 유휴 시간 이후에 스크립트를 다시 시작하면 get_mlflow_tracking_uri API를 사용하여 새 URI를 가져옵니다.

MLflow 실험 이름을 `set_experiment()` 설정하고 을 통해 `start_run()`교육 실행을 시작합니다. 그런 `log_metric()` 다음 MLflow 로깅 API를 활성화하고 학습 실행 메트릭로 깅을 시작합니다.

```Python
experiment_name = 'experiment_with_mlflow'
mlflow.set_experiment(experiment_name)

with mlflow.start_run():
    mlflow.log_metric('alpha', 0.03)
```

## <a name="track-remote-runs"></a>원격 실행 추적

Azure 기계 학습을 사용하여 MLflow 추적을 사용하면 원격 실행에서 기록된 메트릭 및 아티팩트를 Azure 기계 학습 작업 영역에 저장할 수 있습니다.

원격 실행을 사용하면 GPU 지원 가상 머신 또는 기계 학습 컴퓨팅 클러스터와 같은 보다 강력한 컴퓨팅에서 모델을 학습할 수 있습니다. 다양한 계산 옵션에 대해 알아보려면 [모델 학습에 대한 계산 대상 설정을](how-to-set-up-training-targets.md) 참조하십시오.

[`Environment`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) 클래스와 함께 계산 및 학습 실행 환경을 구성합니다. 환경 `mlflow` `azureml-mlflow` 섹션에 패키지를 포함하고 [`CondaDependencies`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py) pip합니다. 그런 [`ScriptRunConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig?view=azure-ml-py) 다음 원격 계산을 계산 대상으로 구성합니다.

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

학습 스크립트에서 MLflow 로깅 API를 사용하도록 가져오고 `mlflow` 실행 메트릭 로깅을 시작합니다.

```Python
import mlflow

with mlflow.start_run():
    mlflow.log_metric('example', 1.23)
```

이 계산 및 학습 실행 구성을 사용하여 메서드를 `Experiment.submit('train.py')` 사용하여 실행을 제출합니다. 이 메서드는 MLflow 추적 URI를 자동으로 설정 하 고 MLflow에서 작업 영역으로 로깅을 지시 합니다.

```Python
run = exp.submit(src)
```

## <a name="track-azure-databricks-runs"></a>Azure 데이터 브릭 실행 추적

Azure 기계 학습을 사용하여 MLflow 추적을 사용하면 Azure Databricks에서 실행되는 Azure 기계 학습 작업 영역에서 기록된 메트릭 및 아티팩트를 저장할 수 있습니다.

Azure Databricks를 사용하여 Mlflow 실험을 실행하려면 먼저 [Azure Databricks 작업 영역 및 클러스터를](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal)만들어야 합니다. 클러스터에서 PyPi에서 *azureml-mlflow* 라이브러리를 설치하여 클러스터가 필요한 기능 및 클래스에 액세스할 수 있도록 해야 합니다.

여기에서 실험 전자 필기장을 가져오고 Azure Databricks 클러스터에 연결하고 실험을 실행합니다. 

### <a name="install-libraries"></a>라이브러리 설치

클러스터에 라이브러리를 설치하려면 **라이브러리** 탭으로 이동하여 **새 새 설치를** 클릭합니다.

 ![azure 기계 학습 다이어그램이 있는 mlflow](./media/how-to-use-mlflow/azure-databricks-cluster-libraries.png)

**패키지** 필드에서 azureml-mlflow를 입력한 다음 설치를 클릭합니다. 필요에 따라 이 단계를 반복하여 실험을 위해 클러스터에 다른 추가 패키지를 설치합니다.

 ![azure 기계 학습 다이어그램이 있는 mlflow](./media/how-to-use-mlflow/install-libraries.png)

### <a name="set-up-your-notebook-and-workspace"></a>노트북 및 작업 공간 설정

클러스터가 설정되면 실험 전자 필기장을 가져오고 열고 클러스터를 연결합니다.

다음 코드는 실험 전자 필기장에 있어야 합니다. 이 코드는 작업 영역을 인스턴스화하기 위해 Azure 구독의 세부 정보를 가져옵니다. 이 코드는 기존 리소스 그룹 및 Azure 기계 학습 작업 영역이 있다고 가정하며, 그렇지 않으면 [을 만들](how-to-manage-workspace.md)수 있습니다. 

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

#### <a name="connect-your-azure-databricks-and-azure-machine-learning-workspaces"></a>Azure 데이터 브릭 및 Azure 기계 학습 작업 영역 연결

Azure [포털에서](https://ms.portal.azure.com)Azure Databricks(ADB) 작업 영역을 새 또는 기존 Azure 기계 학습 작업 영역에 연결할 수 있습니다. 이렇게 하려면 ADB 작업 영역으로 이동하여 오른쪽 하단의 **Azure 기계 학습 작업 영역 링크** 단추를 선택합니다. 작업 영역을 연결하면 Azure 기계 학습 작업 영역에서 실험 데이터를 추적할 수 있습니다. 

### <a name="link-mlflow-tracking-to-your-workspace"></a>작업 영역에 MLflow 추적 연결

작업 영역을 인스턴스화한 후 MLflow 추적 URI를 설정합니다. 이렇게 하면 MLflow 추적을 Azure 기계 학습 작업 영역에 연결합니다. 연결 한 후 모든 실험 관리 되는 Azure 기계 학습 추적 서비스에 착륙 합니다.

#### <a name="directly-set-mlflow-tracking-in-your-notebook"></a>노트북에서 MLflow 추적을 직접 설정

```python
uri = ws.get_mlflow_tracking_uri()
mlflow.set_tracking_uri(uri)
```

학습 스크립트에서 mlflow를 가져와 MLflow 로깅 API를 사용하고 실행 메트릭 로깅을 시작합니다. 다음 예제에서는 epoch 손실 메트릭을 기록합니다. 

```python
import mlflow 
mlflow.log_metric('epoch_loss', loss.item()) 
```

#### <a name="automate-setting-mlflow-tracking"></a>MLflow 추적 설정 자동화

클러스터의 모든 후속 실험 전자 필기장 세션에서 추적 URI를 수동으로 설정하는 대신 이 [Azure 기계 학습 추적 클러스터 Init 스크립트를](https://github.com/Azure/MachineLearningNotebooks/blob/3ce779063b000e0670bdd1acc6bc3a4ee707ec13/how-to-use-azureml/azure-databricks/linking/README.md)사용하여 자동으로 설정합니다.

올바르게 구성되면 Azure 기계 학습 REST API 및 모든 클라이언트및 MLflow 사용자 인터페이스를 통해 또는 MLflow 클라이언트를 사용하여 Azure Databricks에서 MLflow 추적 데이터를 볼 수 있습니다.

## <a name="view-metrics-and-artifacts-in-your-workspace"></a>작업 영역에서 메트릭 및 아티팩트 보기

MLflow 로깅의 메트릭 및 아티팩트는 작업 영역에 보관됩니다. 언제든지 볼 수 있도록 작업 영역으로 이동하여 Azure Machine Learning [studio의](https://ml.azure.com)작업 영역에서 사용자 이름으로 실험을 찾습니다.  또는 아래 코드를 실행합니다. 

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

[Azure ML 노트북을 갖춘 MLflow는](https://aka.ms/azureml-mlflow-examples) 이 문서에서 제시한 개념을 보여 주며 확장합니다.

## <a name="next-steps"></a>다음 단계
* [모델을 관리합니다.](concept-model-management-and-deployment.md)
* [데이터 드리프트에](how-to-monitor-data-drift.md)대한 생산 모델을 모니터링합니다.
