---
title: MLflow 모델을 웹 서비스로 배포
titleSuffix: Azure Machine Learning
description: Azure Machine Learning를 사용 하 여 MLflow를 설정 하 여 ML 모델을 Azure 웹 서비스로 배포 합니다.
services: machine-learning
author: shivp950
ms.author: shipatel
ms.service: machine-learning
ms.subservice: core
ms.reviewer: nibaccam
ms.date: 12/23/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: c45b819f9fc02fae40c2bf7fc5c2247c8c0a6147
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102517483"
---
# <a name="deploy-mlflow-models-as-azure-web-services-preview"></a>MLflow 모델을 Azure 웹 서비스로 배포 (미리 보기)

이 문서에서는 [Mlflow](https://www.mlflow.org) 모델을 Azure 웹 서비스로 배포 하는 방법에 대해 설명 하 여 프로덕션 모델에 Azure Machine Learning의 모델 관리 및 데이터 드리프트 검색 기능을 활용 하 고 적용할 수 있습니다.

Azure Machine Learning는 다음에 대 한 배포 구성을 제공 합니다.
* 빠른 개발-테스트 배포에 적합 한 선택 인 ACI (Azure Container Instance).
* Azure Kubernetes Service (AKS)는 확장 가능한 프로덕션 배포에 권장 됩니다.
> [!TIP]
> 이 문서의 정보는 주로 Azure Machine Learning 웹 서비스 끝점에 MLflow 모델을 배포 하려는 데이터 과학자 및 개발자를 위한 것입니다. 할당량, 완료된 학습 실행 또는 완료된 모델 배포와 같이 Azure Machine Learning의 리소스 사용과 이벤트를 모니터링하는 데 관심이 있는 관리자는 [Azure Machine Learning 모니터링](monitor-azure-machine-learning.md)을 참조하세요.
## <a name="mlflow-with-azure-machine-learning-deployment"></a>Azure Machine Learning 배포를 사용 하는 MLflow

MLflow는 기계 학습 실험의 수명 주기를 관리하기 위한 오픈 소스 라이브러리입니다. Azure Machine Learning와 통합 되어 모델 교육 이상으로이 관리를 프로덕션 모델의 배포 단계로 확장할 수 있습니다.

다음 다이어그램은 MLflow 배포 API 및 Azure Machine Learning를 사용 하 여 PyTorch, Tensorflow, scikit 등의 인기 있는 프레임 워크를 사용 하 여 만든 모델을 Azure 웹 서비스로 배포 하 고 작업 영역에서 관리할 수 있음을 보여 줍니다. 

![ azure machine learning을 사용 하 여 mlflow 모델 배포](./media/how-to-deploy-mlflow-models/mlflow-diagram-deploy.png)


>[!NOTE]
> 오픈 소스 라이브러리로 MLflow는 자주 변경 됩니다. 따라서 Azure Machine Learning 및 MLflow 통합을 통해 제공 되는 기능은 미리 보기로 간주 해야 하며 Microsoft에서 완벽 하 게 지원 되지 않습니다.

## <a name="prerequisites"></a>필수 조건

* 기계 학습 모델입니다. 학습 된 모델이 없는 경우 [이 리포지토리의](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/using-mlflow) 계산 시나리오에 가장 잘 맞는 노트북 예제를 찾고 해당 지침을 따르세요. 
* [Azure Machine Learning 연결 하도록 MLflow 추적 URI를 설정](how-to-use-mlflow.md#track-local-runs)합니다.
* `azureml-mlflow` 패키지를 설치합니다. 
    * 이 패키지는 `azureml-core` 작업 영역에 액세스 하기 위해 MLflow에 대 한 연결을 제공 하는 [AZURE MACHINE LEARNING Python SDK](/python/api/overview/azure/ml/install)를 자동으로 가져옵니다.
* [작업 영역을 사용 하 여 MLflow 작업을 수행 하는 데 필요한 액세스 권한을](how-to-assign-roles.md#mlflow-operations)확인 하세요. 

## <a name="deploy-to-azure-container-instance-aci"></a>ACI (Azure Container Instance)에 배포

Azure Machine Learning 웹 서비스에 MLflow 모델을 배포 하려면 [Azure Machine Learning에 연결 하기 위해 Mlflow 추적 URI](how-to-use-mlflow.md)를 사용 하 여 모델을 설정 해야 합니다. 

[Deploy_configuration ()](/python/api/azureml-core/azureml.core.webservice.aciwebservice#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-) 메서드를 사용 하 여 배포 구성을 설정 합니다. 웹 서비스를 추적 하는 데 도움이 되는 태그와 설명을 추가할 수도 있습니다.

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

그런 다음 Azure Machine Learning에 대해 MLflow의 [deploy](https://www.mlflow.org/docs/latest/python_api/mlflow.azureml.html#mlflow.azureml.deploy) 메서드를 사용 하 여 한 번에 모델을 등록 하 고 배포 합니다. 

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

Azure Machine Learning 웹 서비스에 MLflow 모델을 배포 하려면 [Azure Machine Learning에 연결 하기 위해 Mlflow 추적 URI](how-to-use-mlflow.md)를 사용 하 여 모델을 설정 해야 합니다. 

AKS에 배포 하려면 먼저 AKS 클러스터를 만듭니다. [ComputeTarget ()](/python/api/azureml-core/azureml.core.computetarget#create-workspace--name--provisioning-configuration-) 메서드를 사용 하 여 AKS 클러스터를 만듭니다. 새 클러스터를 만드는 데 20-25 분이 걸릴 수 있습니다.

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
[Deploy_configuration ()](/python/api/azureml-core/azureml.core.webservice.aciwebservice#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-) 메서드를 사용 하 여 배포 구성을 설정 합니다. 웹 서비스를 추적 하는 데 도움이 되는 태그와 설명을 추가할 수도 있습니다.

```python
from azureml.core.webservice import Webservice, AksWebservice

# Set the web service configuration (using default here with app insights)
aks_config = AksWebservice.deploy_configuration(enable_app_insights=True, compute_target_name='aks-mlflow')

```

그런 다음 Azure Machine Learning에 대해 MLflow의 [deploy](https://www.mlflow.org/docs/latest/python_api/mlflow.azureml.html#mlflow.azureml.deploy) 메서드를 사용 하 여 한 번에 모델을 등록 하 고 배포 합니다. 

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

배포 된 웹 서비스를 사용 하지 않으려는 경우를 사용 하 여 `service.delete()` 노트북에서 삭제 합니다.  자세한 내용은 [WebService. delete ()](/python/api/azureml-core/azureml.core.webservice%28class%29#delete--)에 대 한 설명서를 참조 하십시오.

## <a name="example-notebooks"></a>노트북 예제

[Azure Machine Learning 노트북을 사용 하는 Mlflow](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/using-mlflow) 는이 문서에 나와 있는 개념을 시연 하 고 확장 합니다.

> [!NOTE]
> Mlflow를 사용 하는 커뮤니티 중심 예제 리포지토리는에서 찾을 수 있습니다 https://github.com/Azure/azureml-examples .

## <a name="next-steps"></a>다음 단계

* [모델 관리](concept-model-management-and-deployment.md).
* [데이터 드리프트](./how-to-enable-data-collection.md)를 위한 프로덕션 모델 모니터링.
* [MLflow를 사용 하 여 실행 Azure Databricks 추적](how-to-use-mlflow-azure-databricks.md)합니다.

