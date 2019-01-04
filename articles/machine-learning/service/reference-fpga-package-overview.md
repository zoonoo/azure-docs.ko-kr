---
title: 하드웨어 가속을 위한 FPGA 패키지
titleSuffix: Azure Machine Learning service
description: 이 문서는 이제 사용되지 않습니다.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: reference
ms.reviewer: jmartens
ms.author: tedway
author: tedway
ms.date: 05/07/2018
ms.custom: seodec18
ROBOTS: NOINDEX
ms.openlocfilehash: 44c0818d7ddb418e34e6e124ddc0b27460bb24a0
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/11/2018
ms.locfileid: "53262081"
---
# <a name="azure-machine-learning-hardware-acceleration-package"></a>Azure Machine Learning 하드웨어 가속 패키지

>[!Note]
>**이 문서는 이제 사용되지 않습니다.** 이 FPGA 패키지는 사용되지 않습니다. 이 기능에 대한 지원이 Azure ML SDK에 추가되었습니다. 이 패키지에 대한 지원은 점진적으로 종료됩니다. [지원 관련 일정을 확인해 보세요](overview-what-happened-to-workbench.md#timeline). 업데이트된 [FPGA 지원](concept-accelerate-with-fpgas.md)에 대해 알아보세요.

Azure Machine Learning 하드웨어 가속 패키지는 Azure Machine Learning용 Python pip 설치 가능 확장이며, 데이터 과학자와 AI 개발자는 다음 작업을 신속하게 수행할 수 있습니다.

+ ResNet 50의 양자화된 버전으로 이미지 기능화

+ 이러한 기능을 기반으로 분류자 교육

+ 대기 시간이 엄청나게 짧은 추론을 위해 Azure에서 [FPGA(Field-programmable Gate Array)](concept-accelerate-with-fpgas.md)에 모델 배포

## <a name="prerequisites"></a>필수 조건

>[!Note]
>Azure 구독이 없는 경우 시작하기 전에 체험 계정을 만듭니다. 지금 바로 [Azure Machine Learning 서비스의 평가판 또는 유료 버전](http://aka.ms/AMLFree)을 사용해 보세요.

1. Azure Machine Learning 모델 관리 계정. 계정 만들기에 대한 자세한 내용은 [Azure Machine Learning 빠른 시작 및 Workbench 설치](../desktop-workbench/quickstart-installation.md) 문서를 참조하세요. 

1. 패키지를 설치해야 합니다. 

 
## <a name="how-to-install-the-package"></a>패키지를 설치하는 방법

1. 최신 버전의 [Git](https://git-scm.com/downloads)를 다운로드하여 설치합니다.

2. [Anaconda(Python 3.6)](https://conda.io/miniconda.html)를 설치합니다.

   미리 구성된 Anaconda 환경을 다운로드하려면 Git 프롬프트에서 아래 명령을 사용합니다.

    ```
    git clone https://aka.ms/aml-real-time-ai
    ```
1. 환경을 만들려면 **Anaconda 프롬프트**를 열고 아래 명령을 사용합니다.

    ```
    conda env create -f aml-real-time-ai/environment.yml
    ```

1. 환경을 활성화하려면 아래 명령을 사용합니다.

    ```
    conda activate amlrealtimeai
    ```

## <a name="sample-code"></a>샘플 코드

이 샘플 코드는 SDK를 사용하여 모델을 FPGA에 배포하는 과정을 안내합니다.

1. 패키지 가져오기:
   ```python
   import amlrealtimeai
   from amlrealtimeai import resnet50
   ```

1. 이미지 사전 처리:
   ```python 
   from amlrealtimeai.resnet50.model import LocalQuantizedResNet50
   model_path = os.path.expanduser('~/models')
   model = LocalQuantizedResNet50(model_path)
   print(model.version)
   ```

1. 이미지 기능화:
   ```python 
   from amlrealtimeai.resnet50.model import LocalQuantizedResNet50
   model_path = os.path.expanduser('~/models')
   model = LocalQuantizedResNet50(model_path)
   print(model.version)
   ```

1. 분류자 만들기:
   ```python
   model.import_graph_def(include_featurizer=False)
   print(model.classifier_input)
   print(model.classifier_output)
   ```

1. 서비스 정의 만들기:
   ```python
   from amlrealtimeai.pipeline import ServiceDefinition, TensorflowStage, BrainWaveStage
   save_path = os.path.expanduser('~/models/save')
   service_def_path = os.path.join(save_path, 'service_def.zip')

   service_def = ServiceDefinition()
   service_def.pipeline.append(TensorflowStage(tf.Session(), in_images, image_tensors))
   service_def.pipeline.append(BrainWaveStage(model))
   service_def.pipeline.append(TensorflowStage(tf.Session(), model.classifier_input, model.classifier_output))
   service_def.save(service_def_path)
   print(service_def_path)
   ```
 
1. FPGA에서 실행할 모델 준비:
   ```python
   from amlrealtimeai import DeploymentClient

   subscription_id = "<Your Azure Subscription ID>"
   resource_group = "<Your Azure Resource Group Name>"
   model_management_account = "<Your AzureML Model Management Account Name>"

   model_name = "resnet50-model"
   service_name = "quickstart-service"

   deployment_client = DeploymentClient(subscription_id, resource_group, model_management_account)
   ```

1. FPGA에서 실행할 모델 배포:
   ```python
   service = deployment_client.get_service_by_name(service_name)
   model_id = deployment_client.register_model(model_name, service_def_path)

   if(service is None):
      service = deployment_client.create_service(service_name, model_id)    
   else:
      service = deployment_client.update_service(service.id, model_id)
   ```

1. 클라이언트 만들기:
    ```python
   from amlrealtimeai import PredictionClient
   client = PredictionClient(service.ipAddress, service.port)  
   ```

1. API 호출:
   ```python
   image_file = R'C:\path_to_file\image.jpg'
   results = client.score_image(image_file)
   ```

## <a name="reporting-issues"></a>문제 보고

[포럼](https://aka.ms/aml-forum-service)을 사용하여 패키지와 관련된 모든 문제를 보고하세요.

## <a name="next-steps"></a>다음 단계

[모델을 FPGA에 웹 서비스로 배포](how-to-deploy-fpga-web-service.md)
