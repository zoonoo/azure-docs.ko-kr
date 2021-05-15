---
title: MLflow 모델을 웹 서비스로 배포
titleSuffix: Azure Machine Learning
description: Azure Machine Learning으로 MLflow를 설정하여 ML 모델을 Azure 웹 서비스로 배포합니다.
services: machine-learning
author: shivp950
ms.author: shipatel
ms.service: machine-learning
ms.subservice: core
ms.reviewer: nibaccam
ms.date: 12/23/2020
ms.topic: how-to
ms.custom: devx-track-python
ms.openlocfilehash: a8f19a9c731b24b5c839a29035aab118b91cf891
ms.sourcegitcommit: 5ce88326f2b02fda54dad05df94cf0b440da284b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107885383"
---
# <a name="deploy-mlflow-models-as-azure-web-services-preview"></a>MLflow 모델을 Azure 웹 서비스로 배포(미리 보기)

이 문서에서는 [Mlflow](https://www.mlflow.org) 모델을 Azure 웹 서비스로 배포하는 방법에 대해 설명하여, 프로덕션 모델에 Azure Machine Learning의 모델 관리 및 데이터 드리프트 검색 기능을 활용하고 적용할 수 있습니다.

Azure Machine Learning은 다음에 대한 배포 구성을 제공합니다.
* 빠른 개발-테스트 배포에 적합한 ACI (Azure Container Instance).
* AKS (Azure Kubernetes Service)는 확장 가능한 프로덕션 배포에 권장됩니다.
> [!TIP]
> 이 문서의 정보는 주로 Azure Machine Learning 웹 서비스 엔드포인트에 MLflow 모델을 배포하려는 데이터 과학자 및 개발자를 위한 것입니다. 할당량, 완료된 학습 실행 또는 완료된 모델 배포와 같이 Azure Machine Learning의 리소스 사용과 이벤트를 모니터링하는 데 관심이 있는 관리자는 [Azure Machine Learning 모니터링](monitor-azure-machine-learning.md)을 참조하세요.
## <a name="mlflow-with-azure-machine-learning-deployment"></a>Azure Machine Learning 배포를 사용한 MLflow

MLflow는 기계 학습 실험의 수명 주기를 관리하기 위한 오픈 소스 라이브러리입니다. Azure Machine Learning과 통합되어 모델 교육 이상으로 이 관리를 프로덕션 모델의 배포 단계로 확장할 수 있습니다.

다음 다이어그램은 MLflow 배포 API 및 Azure Machine Learning을 사용하여 PyTorch, Tensorflow, scikit-learn 등의 인기 있는 프레임워크를 사용하여 만든 모델을 Azure 웹 서비스로 배포하고 작업 영역에서 이를 관리하는 것을 보여 줍니다. 

![ Azure Machine Learning을 사용하여 MLflow 모델 배포](./media/how-to-deploy-mlflow-models/mlflow-diagram-deploy.png)


>[!NOTE]
> 오픈 소스 라이브러리로서 MLflow는 자주 변경됩니다. 따라서 Azure Machine Learning 및 MLflow 통합을 통해 제공되는 기능은 미리 보기로 간주해야 하며, Microsoft에서 완벽하게 지원하지 않습니다.

## <a name="prerequisites"></a>사전 요구 사항

* Machine Learning 모델 학습된 모델이 없는 경우, [이 리포지토리](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/using-mlflow)의 컴퓨팅 시나리오에 가장 잘 맞는 Notebook 예제를 찾아서 해당 지침을 따르세요. 
* [MLflow 추적 URI를 설정하여 Azure Machine Learning을 연결합니다](how-to-use-mlflow.md#track-local-runs).
* `azureml-mlflow` 패키지를 설치합니다. 
    * 이 패키지는 작업 영역에 액세스하기 위해 MLflow에 대한 연결을 제공하는 [Azure Machine Learning Python SDK](/python/api/overview/azure/ml/install)의 `azureml-core`를 자동으로 가져옵니다.
* [작업 영역을 사용하여 MLflow 작업을 수행하는 데 필요한 액세스 권한](how-to-assign-roles.md#mlflow-operations)을 확인하세요. 

## <a name="deploy-to-azure-container-instance-aci"></a>ACI (Azure Container Instances)에 배포

Azure Machine Learning 웹 서비스에 MLflow 모델을 배포하려면, [Azure Machine Learning에 연결하기 위한 Mlflow 추적 URI](how-to-use-mlflow.md)를 사용하여 모델을 설정해야 합니다. 

[Deploy_configuration()](/python/api/azureml-core/azureml.core.webservice.aciwebservice#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-) 메서드를 사용하여 배포 구성을 설정합니다. 웹 서비스를 추적하는 데 도움이 되는 태그와 설명을 추가할 수도 있습니다.

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

그런 다음 Azure Machine Learning에 대해 MLflow의 [deploy](https://www.mlflow.org/docs/latest/python_api/mlflow.azureml.html#mlflow.azureml.deploy) 메서드를 사용하여 한 번에 모델을 등록하고 배포합니다. 

```python
(webservice,model) = mlflow.azureml.deploy( model_uri='runs:/{}/{}'.format(run.id, model_path),
                      workspace=ws,
                      model_name='sklearn-model', 
                      service_name='diabetes-model-1', 
                      deployment_config=aci_config, 
                      tags=None, mlflow_home=None, synchronous=True)

webservice.wait_for_deployment(show_output=True)
```

## <a name="deploy-to-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Service)에 배포

Azure Machine Learning 웹 서비스에 MLflow 모델을 배포하려면, [Azure Machine Learning에 연결하기 위한 Mlflow 추적 URI](how-to-use-mlflow.md)를 사용하여 모델을 설정해야 합니다. 

AKS에 배포하려면 먼저 AKS 클러스터를 만듭니다. [ComputeTarget.create()](/python/api/azureml-core/azureml.core.computetarget#create-workspace--name--provisioning-configuration-) 메서드를 사용하여 AKS 클러스터를 만듭니다. 새 클러스터를 만드는 데 20~25분이 걸릴 수 있습니다.

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
[Deploy_configuration()](/python/api/azureml-core/azureml.core.webservice.aciwebservice#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-) 메서드를 사용하여 배포 구성을 설정합니다. 웹 서비스를 추적하는 데 도움이 되는 태그와 설명을 추가할 수도 있습니다.

```python
from azureml.core.webservice import Webservice, AksWebservice

# Set the web service configuration (using default here with app insights)
aks_config = AksWebservice.deploy_configuration(enable_app_insights=True, compute_target_name='aks-mlflow')

```

그런 다음 Azure Machine Learning에 대해 MLflow의 [deploy](https://www.mlflow.org/docs/latest/python_api/mlflow.azureml.html#mlflow.azureml.deploy) 메서드를 사용하여 한 번에 모델을 등록하고 배포합니다. 

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

배포된 웹 서비스를 사용할 계획이 없는 경우, `service.delete()`을 사용하여 Notebook에서 삭제합니다.  자세한 내용은 [WebService.delete()](/python/api/azureml-core/azureml.core.webservice%28class%29#delete--)에 대한 설명서를 참조하세요.

## <a name="example-notebooks"></a>노트북 예제

[Azure Machine Learning Notebook을 사용한 MLflow](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/using-mlflow)는 이 문서에 나와 있는 개념에 따라 시연하고 확장합니다.

> [!NOTE]
> MLflow를 사용하는 커뮤니티 기반 예제 리포지토리는 https://github.com/Azure/azureml-examples 에서 찾을 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [모델 관리](concept-model-management-and-deployment.md).
* [데이터 드리프트](./how-to-enable-data-collection.md)를 위한 프로덕션 모델 모니터링.
* [MLflow를 사용하여 Azure Databricks 실행 추적](how-to-use-mlflow-azure-databricks.md)

