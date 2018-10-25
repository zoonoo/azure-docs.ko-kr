---
title: Azure Machine Learning 서비스에서 모델을 Kubernetes에 배포 | Microsoft Docs
description: Azure Machine Learning 서비스에서 Azure Kubernetes Service로 모델을 배포하는 방법을 알아봅니다. 모델은 웹 서비스로 배포됩니다. Azure Kubernetes Service는 대규모 프로덕션 워크로드에 적합합니다.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: raymondl
author: raymondlaghaeian
manager: cgronlun
ms.reviewer: larryfr
ms.date: 09/24/2018
ms.openlocfilehash: 3ab32388e0a35f4abf3866aa0a84ee0628b0570c
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2018
ms.locfileid: "49318200"
---
# <a name="how-to-deploy-models-from-azure-machine-learning-service-to-azure-kubernetes-service"></a>Azure Machine Learning 서비스에서 Azure Kubernetes Service로 모델을 배포하는 방법

확장성이 뛰어난 프로덕션 시나리오의 경우 AKS(Azure Kubernetes Service)에 모델을 배포할 수 있습니다. Azure Machine Learning은 기존 AKS 클러스터 또는 배포 도중에 생성된 새 클러스터를 사용할 수 있습니다. 모델은 웹 서비스로 ASK에 배포됩니다.

AKS로의 배포를 통해 자동 크기 조정, 로깅, 모델 데이터 컬렉션 및 웹 서비스에 대한 빠른 응답 시간을 제공합니다.

## <a name="prerequisites"></a>필수 조건

- Azure 구독. 구독이 없으면 시작하기 전에 [계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만드세요.

- Azure Machine Learning 서비스 작업 영역, 스크립트가 포함된 로컬 디렉터리 및 Python용 Azure Machine Learning SDK가 설치되어 있어야 합니다. [개발 환경 구성 방법](how-to-configure-environment.md) 문서를 사용하여 이러한 필수 구성 요소를 충족하는 방법을 알아보세요.

- 학습된 Machine Learning 모델. 이러한 모델이 없으면 [이미지 분류 모델 학습](tutorial-train-models-with-aml.md) 자습서를 참조하세요.

## <a name="initialize-the-workspace"></a>작업 영역 초기화

작업 영역을 초기화하려면 작업 영역 정보를 포함하는 `config.json` 파일을 로드합니다.

```python
from azureml.coreazureml import Workspace

# Load existing workspace from the config file info.
ws = Workspace.from_config()
```

## <a name="register-the-model"></a>모델 등록

기존 모델을 등록하려면 모델 경로, 설명 및 태그를 지정합니다.

```python
from azureml.core.model import Model

model = Model.register(model_path = "model.pkl", # this points to a local file
                        model_name = "best_model", # this is the name the model is registered as
                        tags = {"data": "diabetes", "type": "regression"},
                        description = "Ridge regression model to predict diabetes",
                        workspace = ws)
```

## <a name="create-a-docker-image"></a>Docker 이미지 만들기

Azure Kubernetes Service는 Docker 이미지를 사용합니다. 이미지를 만들려면 다음 단계를 사용합니다.

1. 이미지를 구성하려면 점수 매기기 스크립트 및 환경 파일을 만들어야 합니다. 스크립트 및 환경 파일을 만드는 예제는 이미지 분류 예제의 다음 섹션을 참조하세요.

    * [점수 매기기 스크립트(score.py) 만들기](tutorial-deploy-models-with-aml.md#create-scoring-script)

    * [환경 파일(myenv.yml) 만들기](tutorial-deploy-models-with-aml.md#create-environment-file) 

   다음 예제에서는 이러한 파일을 사용하여 이미지를 구성합니다.

    ```python
    from azureml.core.image import ContainerImage

    # Image configuration
    image_config = ContainerImage.image_configuration(execution_script = "score.py",
                                                        runtime = "python",
                                                        conda_file = "myenv.yml",
                                                        description = "Image with ridge regression model",
                                                        tags = {"data": "diabetes", "type": "regression"}
                                                        )
    ```

1. 이미지를 만들려면 모델 및 이미지 구성을 사용합니다. 이 작업을 완료하는 데 5분 정도 걸릴 수 있습니다.

    ```python
    image = ContainerImage.create(name = "myimage1",
                                    # this is the model object
                                    models = [model],
                                    image_config = image_config,
                                    workspace = ws)

    # Wait for the create process to complete
    image.wait_for_creation(show_output = True)
    ```

## <a name="create-the-aks-cluster"></a>AKS 클러스터 만들기

다음 코드 조각은 AKS 클러스터를 만드는 방법을 설명합니다. 이 프로세스를 완료하는 데는 약 20분이 걸립니다.

> [!IMPORTANT]
> AKS 클러스터 만들기는 작업 영역에 대한 하나의 프로세스입니다. 만든 후에는 여러 배포에 대해 이 클러스터를 재사용할 수 있습니다. 이를 포함한 클러스터 또는 리소스 그룹을 삭제하는 경우 다음에 배포해야 할 때 새 클러스터를 만들어야 합니다.


```python
from azureml.core.compute import AksCompute, ComputeTarget

# Use the default configuration (you can also provide parameters to customize this)
prov_config = AksCompute.provisioning_configuration()

aks_name = 'aml-aks-1' 
# Create the cluster
aks_target = ComputeTarget.create(workspace = ws, 
                                    name = aks_name, 
                                    provisioning_configuration = prov_config)

# Wait for the create process to complete
aks_target.wait_for_completion(show_output = True)
print(aks_target.provisioning_state)
print(aks_target.provisioning_errors)
```

### <a name="attach-existing-aks-cluster-optional"></a>기존 AKS 클러스터 연결(선택 사항)

기존 AKS 클러스터가 Azure 구독에 있는 경우 이미지를 배포하는 데 사용할 수 있습니다. 다음 코드 조각은 작업 영역에 클러스터를 연결하는 방법을 설명합니다. 

> [!IMPORTANT]
> AKS 1.11.2 버전만 지원됩니다.

```python
# Get the resource id from https://porta..azure.com -> Find your resource group -> click on the Kubernetes service -> Properties
resource_id = '/subscriptions/<your subscription id>/resourcegroups/<your resource group>/providers/Microsoft.ContainerService/managedClusters/<your aks service name>'

# Set to the name of the cluster
cluster_name='my-existing-aks' 

# Attatch the cluster to your workgroup
aks_target = AksCompute.attach(workspace=ws, name=cluster_name, resource_id=resource_id)

# Wait for the operation to complete
aks_target.wait_for_completion(True)
```

## <a name="deploy-your-web-service"></a>웹 서비스 배포

다음 코드 조각은 AKS 클러스터에 이미지를 배포하는 방법을 설명합니다.

```python
from azureml.core.webservice import Webservice, AksWebservice

# Set configuration and service name
aks_config = AksWebservice.deploy_configuration()
aks_service_name ='aks-service-1'
# Deploy from image
aks_service = Webservice.deploy_from_image(workspace = ws, 
                                            name = aks_service_name,
                                            image = image,
                                            deployment_config = aks_config,
                                            deployment_target = aks_target)
# Wait for the deployment to complete
aks_service.wait_for_deployment(show_output = True)
print(aks_service.state)
```

> [!TIP]
> 배포하는 동안 오류가 있으면 `aks_service.get_logs()`를 사용하여 AKS 서비스 로그를 확인합니다. 로깅된 정보에 오류 원인이 나타날 수 있습니다.

## <a name="test-the-web-service"></a>웹 서비스 테스트

`aks_service.run()`을 사용하여 웹 서비스를 테스트합니다. 다음 코드 조각은 데이터를 서비스에 전달하여 예측을 표시하는 방법을 설명합니다.

```python
import json

test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10], 
    [10,9,8,7,6,5,4,3,2,1]
]})
test_sample = bytes(test_sample,encoding = 'utf8')

prediction = aks_service.run(input_data = test_sample)
print(prediction)
```

## <a name="cleanup"></a>정리

서비스, 이미지 및 모델을 삭제하려면 다음 코드 조각을 사용합니다.

```python
aks_service.delete()
image.delete()
model.delete()
```
