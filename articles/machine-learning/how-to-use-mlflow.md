---
title: ML 실험을 위한 MLflow 추적
titleSuffix: Azure Machine Learning
description: Databricks 클러스터, 로컬 환경 또는 VM 환경에서 만든 ML 모델에서 메트릭 및 아티팩트를 로깅하도록 Azure Machine Learning을 사용하여 MLflow를 설정합니다.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: nibaccam
ms.topic: conceptual
ms.date: 02/03/2020
ms.custom: seodec18
ms.openlocfilehash: 95567a177635dc7d7ed03404487e62c76db8bdac
ms.sourcegitcommit: a9784a3fd208f19c8814fe22da9e70fcf1da9c93
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/22/2020
ms.locfileid: "83779114"
---
# <a name="track-models-metrics-with-mlflow-and-azure-machine-learning-preview"></a>MLflow 및 Azure Machine Learning을 사용하여 모델 메트릭 추적(미리 보기)

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

이 문서에서는 MLflow의 추적 URI 및 로깅 API(총칭하여 [MLflow 추적](https://mlflow.org/docs/latest/quickstart.html#using-the-tracking-api))를 사용하여 MLflow 실험 및 Azure Machine Learning에 연결하는 방법을 설명합니다. 이렇게 하면 [Azure Machine Learning 작업 영역](https://docs.microsoft.com/azure/machine-learning/concept-azure-machine-learning-architecture#workspaces)에서 실험 메트릭과 아티팩트를 추적하고 로깅할 수 있습니다. 실험을 위해 MLflow 추적을 이미 사용하고 있는 경우 작업 영역이 학습 메트릭 및 모델을 저장할 중앙 집중화된 안전하고 확장성 있는 위치를 제공합니다.

<!--
+ Deploy your MLflow experiments as an Azure Machine Learning web service. By deploying as a web service, you can apply the Azure Machine Learning monitoring and data drift detection functionalities to your production models. 
-->

[MLflow](https://www.mlflow.org)는 기계 학습 실험의 수명 주기를 관리하기 위한 오픈 소스 라이브러리입니다. MLFlow 추적은 실험 환경에 관계없이(컴퓨터에서 로컬로, 원격 컴퓨팅 대상, 가상 머신 또는 Azure Databricks 클러스터에서) 학습 실행 메트릭과 모델 아티팩트를 로깅하고 추적하는 MLflow의 구성 요소입니다. 

다음 다이어그램에서는 MLflow 추적을 사용하여 Azure Machine Learning 작업 영역에 실험의 실행 메트릭과 모델 아티팩트를 저장하는 방법을 보여 줍니다.

![Azure Machine Learning을 사용한 MLflow 다이어그램](./media/how-to-use-mlflow/mlflow-diagram-track.png)

> [!TIP]
> 이 문서의 정보는 주로 모델 학습 프로세스를 모니터링하려는 데이터 과학자와 개발자를 위한 것입니다. 할당량, 완료된 학습 실행 또는 완료된 모델 배포와 같이 Azure Machine Learning의 리소스 사용과 이벤트를 모니터링하는 데 관심이 있는 관리자는 [Azure Machine Learning 모니터링](monitor-azure-machine-learning.md)을 참조하세요.

## <a name="compare-mlflow-and-azure-machine-learning-clients"></a>MLflow 클라이언트와 Azure Machine Learning 클라이언트 비교

 아래 표에는 Azure Machine Learning을 사용할 수 있는 여러 클라이언트와 해당 기능이 요약되어 있습니다.

 MLflow 추적은 [Azure Machine Learning Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)를 통해 사용할 수 있는 메트릭 로깅 및 아티팩트 스토리지 기능을 제공합니다.


| | MLflow&nbsp;추적 <!--& Deployment--> | Azure Machine Learning Python SDK |  Azure Machine Learning CLI | Azure Machine Learning Studio|
|---|---|---|---|---|
| 작업 영역 관리 |   | ✓ | ✓ | ✓ |
| 데이터 저장소 사용  |   | ✓ | ✓ | |
| 메트릭 로깅      | ✓ | ✓ |   | |
| 아티팩트 업로드 | ✓ | ✓ |   | |
| 메트릭 보기     | ✓ | ✓ | ✓ | ✓ |
| 컴퓨팅 관리   |   | ✓ | ✓ | ✓ |

<!--| Deploy models    | ✓ | ✓ | ✓ | ✓ |
|Monitor model performance||✓|  |   |
| Detect data drift |   | ✓ |   | ✓ |
-->
## <a name="prerequisites"></a>사전 요구 사항

* [MLflow를 설치합니다.](https://mlflow.org/docs/latest/quickstart.html)
* [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)를 로컬 컴퓨터에 설치합니다. 이 SDK는 MLflow가 작업 영역에 액세스하기 위한 연결 기능을 제공합니다.
* [Azure Machine Learning 작업 영역을 만듭니다](how-to-manage-workspace.md).

## <a name="track-local-runs"></a>로컬 실행 추적

MLflow 추적을 Azure Machine Learning과 함께 사용하면 로컬 실행의 로깅된 메트릭 및 아티팩트를 Azure Machine Learning 작업 영역에 저장할 수 있습니다.

Jupyter Notebook 또는 코드 편집기에서 로컬로 실행되는 실험에서 MLflow 추적을 Azure Machine Learning과 함께 사용하려면 `azureml-mlflow` 패키지를 설치합니다.

```shell
pip install azureml-mlflow
```

>[!NOTE]
>서비스 개선을 위해 노력하는 과정에서 azureml.contrib 네임스페이스는 자주 변경됩니다. 따라서 이 네임 스페이스의 모든 것을 미리 보기로 간주하므로 Microsoft에서 완벽히 지원하지 않아도 됩니다.

`mlflow` 및 [`Workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py) 클래스를 가져와 MLflow의 추적 URI에 액세스하고 작업 영역을 구성합니다.

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

MLflow 추적을 Azure Machine Learning과 함께 사용하면 원격 실행의 로깅된 메트릭 및 아티팩트를 Azure Machine Learning 작업 영역에 저장할 수 있습니다.

원격 실행을 사용하면 GPU 사용 가상 머신 또는 Machine Learning 컴퓨팅 클러스터와 같은 보다 강력한 컴퓨팅을 통해 모델을 학습시킬 수 있습니다. 다양한 컴퓨팅 옵션에 대한 자세한 내용은 [모델 학습의 컴퓨팅 대상 설정](how-to-set-up-training-targets.md)을 참조하세요.

[`Environment`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) 클래스를 사용하여 컴퓨팅 및 학습 실행 환경을 구성합니다. 환경의 [`CondaDependencies`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py) 섹션에 `mlflow` 및 `azureml-mlflow` pip 패키지를 포함합니다. 그런 다음 컴퓨팅 대상으로 원격 컴퓨팅을 사용하여 [`ScriptRunConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig?view=azure-ml-py)을 생성합니다.

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

학습 스크립트에서 `mlflow`를 가져와 MLflow 로깅 API를 사용하고 실행 메트릭 로깅을 시작합니다.

```Python
import mlflow

with mlflow.start_run():
    mlflow.log_metric('example', 1.23)
```

이 컴퓨팅 및 학습 실행 구성에서 `Experiment.submit('train.py')` 메서드를 사용하여 실행을 제출합니다. 이 메서드는 자동으로 MLflow 추적 URI를 설정하고 MLflow에서 작업 영역으로 로깅을 보냅니다.

```Python
run = exp.submit(src)
```

## <a name="track-azure-databricks-runs"></a>Azure Databricks 실행 추적

MLflow 추적을 Azure Machine Learning과 함께 사용하면 Azure Databricks 실행의 로깅된 메트릭 및 아티팩트를 Azure Machine Learning 작업 영역에 저장할 수 있습니다.

Azure Databricks를 사용하여 MLflow 실험을 실행하려면 먼저 [Azure Databricks 작업 영역 및 클러스터](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal)를 만들어야 합니다. 클러스터에서 PyPi로부터 *azureml-mlflow* 라이브러리를 설치하여 클러스터가 필요한 함수 및 클래스에 대한 액세스 권한이 있는지 확인해야 합니다.

여기에서 실험 노트북을 가져오고, Azure Databricks 클러스터에 연결하고, 실험을 실행합니다. 

### <a name="install-libraries"></a>라이브러리 설치

클러스터에 라이브러리를 설치하려면 **라이브러리** 탭으로 이동하여 **새로 설치**를 클릭합니다.

 ![Azure Machine Learning을 사용한 MLflow 다이어그램](./media/how-to-use-mlflow/azure-databricks-cluster-libraries.png)

**패키지** 필드에 azureml-mlflow를 입력한 다음 설치를 클릭합니다. 필요에 따라 이 단계를 반복하여 실험을 위한 클러스터에 다른 추가 패키지를 설치합니다.

 ![Azure Machine Learning을 사용한 MLflow 다이어그램](./media/how-to-use-mlflow/install-libraries.png)

### <a name="set-up-your-notebook-and-workspace"></a>노트북 및 작업 영역 설정

클러스터가 설정되면 실험 노트북을 가져오고 연 다음 클러스터를 연결합니다.

다음 코드가 실험 노트북에 있어야 합니다. 이 코드는 작업 영역을 인스턴스화하기 위해 Azure 구독에 대한 세부 정보를 가져옵니다. 이 코드는 기존 리소스 그룹 및 Azure Machine Learning 작업 영역을 보유하고 있는 것으로 가정합니다. 그렇지 않으면 [만들 수 있습니다](how-to-manage-workspace.md). 

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

[Azure Portal](https://ms.portal.azure.com)에서 ADB(Azure Databricks) 작업 영역을 신규 또는 기존 Azure Machine Learning 작업 영역에 연결할 수 있습니다. 이렇게 하려면 ADB 작업 영역으로 이동하고 오른쪽 아래에 있는 **Azure Machine Learning 작업 영역 연결** 단추를 선택합니다. 작업 영역을 연결하면 Azure Machine Learning 작업 영역에서 실험 데이터를 추적할 수 있습니다. 

### <a name="link-mlflow-tracking-to-your-workspace"></a>MLflow 추적을 작업 영역에 연결

작업 영역을 인스턴스화한 후 MLflow 추적 URI를 설정합니다. 이렇게 하면 MLflow 추적을 Azure Machine Learning 작업 영역에 연결할 수 있습니다. 연결 후 모든 실험은 관리형 Azure Machine Learning 추적 서비스에 배치됩니다.

#### <a name="directly-set-mlflow-tracking-in-your-notebook"></a>노트북에서 MLflow 추적 직접 설정

```python
uri = ws.get_mlflow_tracking_uri()
mlflow.set_tracking_uri(uri)
```

학습 스크립트에서 mlflow를 가져와 MLflow 로깅 API를 사용하고 실행 메트릭 로깅을 시작합니다. 다음 예에서는 Epoch 손실 메트릭을 로깅합니다. 

```python
import mlflow 
mlflow.log_metric('epoch_loss', loss.item()) 
```

#### <a name="automate-setting-mlflow-tracking"></a>MLflow 추적 설정 자동화

클러스터의 모든 이후 실험 노트북 세션에서 추적 URI를 수동으로 설정하는 대신 [Azure Machine Learning Tracking Cluster Init 스크립트](https://github.com/Azure/MachineLearningNotebooks/blob/3ce779063b000e0670bdd1acc6bc3a4ee707ec13/how-to-use-azureml/azure-databricks/linking/README.md)를 사용하여 설정을 자동화합니다.

올바르게 구성되면 Azure Machine Learning REST API 및 모든 클라이언트에서 그리고 MLflow 사용자 인터페이스를 통해 또는 MLflow 클라이언트를 사용하여 Azure Databricks에서 MLflow 추적 데이터를 볼 수 있습니다.

## <a name="view-metrics-and-artifacts-in-your-workspace"></a>작업 영역에서 메트릭 및 아티팩트 보기

MLflow 로깅의 메트릭과 아티팩트는 작업 영역에 유지됩니다. 언제든지 이들을 보려면 작업 영역으로 이동하여 [Azure Machine Learning 스튜디오](https://ml.azure.com)의 작업 영역에서 이름으로 실험을 찾습니다.  또는 아래 코드를 실행합니다. 

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

### Deploy the model

Use the Azure Machine Learning SDK to deploy the model as a web service.

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

Then, register and deploy the model by using the Azure Machine Learning SDK [deploy](/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) method. 

```python
(webservice,model) = mlflow.azureml.deploy( model_uri='runs:/{}/{}'.format(run.id, model_path),
                      workspace=ws,
                      model_name='sklearn-model', 
                      service_name='diabetes-model-1', 
                      deployment_config=aci_config, 
                      tags=None, mlflow_home=None, synchronous=True)

webservice.wait_for_deployment(show_output=True)
```
#### Deploy to AKS

To deploy to AKS, first create an AKS cluster. Create an AKS cluster using the [ComputeTarget.create()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.computetarget?view=azure-ml-py#create-workspace--name--provisioning-configuration-) method. It may take 20-25 minutes to create a new cluster.

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

# Set the web service configuration (using default here with app insights)
aks_config = AksWebservice.deploy_configuration(enable_app_insights=True, compute_target_name='aks-mlflow')


Then, deploy the image by using the Azure Machine Learning SDK [deploy()](Then, register and deploy the model by using the Azure Machine Learning SDK [deploy](/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) method. 

```python
# Webservice creation using single command
from azureml.core.webservice import AksWebservice, Webservice
(webservice, model) = mlflow.azureml.deploy( model_uri='runs:/{}/{}'.format(run.id, model_path),
                      workspace=ws,
                      model_name='sklearn-model', 
                      service_name='my-aks', 
                      deployment_config=aks_config, 
                      tags=None, mlflow_home=None, synchronous=True)


webservice.wait_for_deployment()
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

[Azure ML을 사용한 MLflow 노트북](https://aka.ms/azureml-mlflow-examples)은 이 문서에 나와 있는 개념을 시연하고 확장합니다.

## <a name="next-steps"></a>다음 단계
* [모델 관리](concept-model-management-and-deployment.md).
* [데이터 드리프트](how-to-monitor-data-drift.md)를 위한 프로덕션 모델 모니터링.
