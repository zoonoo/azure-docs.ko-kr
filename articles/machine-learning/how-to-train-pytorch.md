---
title: 딥 러닝 파이토치 모델 교육
titleSuffix: Azure Machine Learning
description: Azure 기계 학습의 PyTorch 추정기 클래스를 사용하여 엔터프라이즈 규모에서 PyTorch 교육 스크립트를 실행하는 방법을 알아봅니다.  이 예제 스크립트는 닭과 칠면조 이미지를 분류하여 PyTorch의 전송 학습 자습서를 기반으로 딥 러닝 신경망을 구축합니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: peterlu
author: peterclu
ms.reviewer: peterlu
ms.date: 08/01/2019
ms.custom: seodec18
ms.openlocfilehash: 136ee197271fc659497c169e27a6399c3940c19e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75834856"
---
# <a name="train-pytorch-deep-learning-models-at-scale-with-azure-machine-learning"></a>Azure 기계 학습을 통해 Pytorch 딥 러닝 모델 규모 교육
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

이 문서에서는 Azure 기계 학습의 [PyTorch 추정기](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py) 클래스를 사용하여 엔터프라이즈 규모에서 [PyTorch](https://pytorch.org/) 학습 스크립트를 실행하는 방법을 알아봅니다.  

이 문서의 예제 스크립트는 닭과 칠면조 이미지를 분류하여 PyTorch의 전송 [학습 자습서를](https://pytorch.org/tutorials/beginner/transfer_learning_tutorial.html)기반으로 딥 러닝 신경망을 구축하는 데 사용됩니다. 

처음부터 딥 러닝 PyTorch 모델을 교육하든 기존 모델을 클라우드로 가져오든 Azure Machine Learning을 사용하여 탄력적 클라우드 컴퓨팅 리소스를 사용하여 오픈 소스 학습 작업을 확장할 수 있습니다. Azure 기계 학습을 통해 프로덕션 급 모델을 빌드, 배포, 버전 및 모니터링할 수 있습니다. 

[딥 러닝과 머신 러닝에](concept-deep-learning-vs-machine-learning.md)대해 자세히 알아보십시오.

## <a name="prerequisites"></a>사전 요구 사항

다음 환경 중 하나에서 이 코드를 실행합니다.

- Azure 기계 학습 계산 인스턴스 - 다운로드 또는 설치 필요 없음

    - 자습서 [완료: 설정 환경 및 작업 영역을](tutorial-1st-experiment-sdk-setup.md) 설정하여 SDK 및 샘플 리포지토리로 미리 로드된 전용 노트북 서버를 만듭니다.
    - 노트북 서버의 샘플 딥 러닝 폴더에서 이 디렉토리를 탐색하여 완료되고 확장된 전자 필기장을 찾습니다 **> >.** 
 
 - 사용자 고유의 Jupyter Notebook 서버

    - [Azure 기계 학습 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)를 설치합니다.
    - [작업 영역 구성 파일 만들기](how-to-configure-environment.md#workspace).
    - [샘플 스크립트 파일 다운로드](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/pytorch/deployment/train-hyperparameter-tune-deploy-with-pytorch)`pytorch_train.py`
     
    GitHub 샘플 페이지에서 이 가이드의 완성된 [Jupyter 노트북 버전을](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/pytorch/deployment/train-hyperparameter-tune-deploy-with-pytorch/train-hyperparameter-tune-deploy-with-pytorch.ipynb) 찾을 수도 있습니다. 노트북에는 지능형 하이퍼매개 변수 튜닝, 모델 배포 및 노트북 위젯을 포함하는 확장된 섹션이 포함되어 있습니다.

## <a name="set-up-the-experiment"></a>실험 설정

이 섹션에서는 필요한 파이썬 패키지를 로드하고, 작업 영역을 초기화하고, 실험을 만들고, 학습 데이터 및 학습 스크립트를 업로드하여 학습 실험을 설정합니다.

### <a name="import-packages"></a>패키지 가져오기

먼저 필요한 파이썬 라이브러리를 가져옵니다.

```Python
import os
import shutil

from azureml.core.workspace import Workspace
from azureml.core import Experiment

from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException
from azureml.train.dnn import PyTorch
```

### <a name="initialize-a-workspace"></a>작업 영역 초기화

[Azure 기계 학습 작업 영역은](concept-workspace.md) 서비스의 최상위 리소스입니다. 그것은 당신이 만드는 모든 아티팩트와 함께 작업 할 수있는 중앙 집중식 장소를 제공합니다. Python SDK에서 개체를 만들어 작업 영역 아티팩트에 [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) 액세스할 수 있습니다.

[필수 구성 조건 섹션에서](#prerequisites)만든 `config.json` 파일에서 작업 영역 개체를 만듭니다.

```Python
ws = Workspace.from_config()
```

### <a name="create-a-deep-learning-experiment"></a>딥 러닝 실험 만들기

학습 스크립트를 보관할 실험 및 폴더를 만듭니다. 이 예제에서는 "불꽃-새"라는 실험을 만듭니다.

```Python
project_folder = './pytorch-birds'
os.makedirs(project_folder, exist_ok=True)

experiment_name = 'pytorch-birds'
experiment = Experiment(ws, name=experiment_name)
```

### <a name="get-the-data"></a>데이터 가져오기

데이터 집합은 칠면조와 닭에 대해 각각 약 120개의 교육 이미지로 구성되며 각 클래스에 대해 100개의 유효성 검사 이미지가 있습니다. 학습 스크립트의 `pytorch_train.py`일부로 데이터 집합을 다운로드하고 추출합니다. 이미지는 [이미지 열기 v5 데이터 집합의](https://storage.googleapis.com/openimages/web/index.html)하위 집합입니다.

### <a name="prepare-training-scripts"></a>교육 스크립트 준비

이 자습서에서는 교육 스크립트가 `pytorch_train.py`이미 제공됩니다. 실제로 는 사용자 지정 학습 스크립트를 있는 것처럼 가져 와서 Azure 기계 학습을 통해 실행할 수 있습니다.

Pytorch 교육 스크립트를 `pytorch_train.py`업로드합니다.

```Python
shutil.copy('pytorch_train.py', project_folder)
```

그러나 Azure 기계 학습 추적 및 메트릭 기능을 사용하려면 학습 스크립트 에 소량 코드를 추가해야 합니다. 메트릭 추적의 예는 에서 `pytorch_train.py`찾을 수 있습니다.

## <a name="create-a-compute-target"></a>컴퓨팅 대상 만들기

PyTorch 작업을 실행할 계산 대상을 만듭니다. 이 예제에서는 GPU 지원 Azure 기계 학습 계산 클러스터를 만듭니다.

```Python
cluster_name = "gpucluster"

try:
    compute_target = ComputeTarget(workspace=ws, name=cluster_name)
    print('Found existing compute target')
except ComputeTargetException:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_NC6', 
                                                           max_nodes=4)

    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
```

계산 대상에 대한 자세한 내용은 [계산 대상](concept-compute-target.md) 문서를 참조하세요.

## <a name="create-a-pytorch-estimator"></a>파이토치 추정기 만들기

[PyTorch 추정기는](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py) 계산 대상에서 PyTorch 교육 작업을 시작하는 간단한 방법을 제공합니다.

PyTorch 추정기는 모든 프레임 [`estimator`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) 워크를 지원하는 데 사용할 수있는 제네릭 클래스를 통해 구현됩니다. 일반 추정기를 사용하는 학습 모델에 대한 자세한 내용은 [추정기를 사용하여 Azure 기계 학습을 사용하는 학습 모델을](how-to-train-ml-models.md) 참조하십시오.

교육 스크립트에서 추가 pip 또는 conda 패키지를 실행해야 하는 경우 `pip_packages` 해당 `conda_packages` 도커 이미지에 해당 이름을 전달하여 패키지를 설치할 수 있습니다.

```Python
script_params = {
    '--num_epochs': 30,
    '--output_dir': './outputs'
}

estimator = PyTorch(source_directory=project_folder, 
                    script_params=script_params,
                    compute_target=compute_target,
                    entry_script='pytorch_train.py',
                    use_gpu=True,
                    pip_packages=['pillow==5.4.1'])
```

Python 환경 사용자 지정에 대한 자세한 내용은 [교육 및 배포를 위한 환경 만들기 및 관리를](how-to-use-environments.md)참조하십시오.

## <a name="submit-a-run"></a>달리기 제출

[Run 개체는](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) 작업이 실행되는 동안 및 작업이 완료된 후 실행 기록에 대한 인터페이스를 제공합니다.

```Python
run = experiment.submit(estimator)
run.wait_for_completion(show_output=True)
```

실행이 실행되면 다음 단계를 거칩니다.

- **준비**: PyTorch 추정기에 따라 도커 이미지가 만들어집니다. 이미지가 작업 영역의 컨테이너 레지스트리에 업로드되고 나중에 실행될 수 있도록 캐시됩니다. 로그는 실행 기록으로 스트리밍되며 진행 상황을 모니터링하기 위해 볼 수 있습니다.

- **크기 조정**: Batch AI 클러스터에 현재 사용 가능한 것보다 실행을 실행하는 데 더 많은 노드가 필요한 경우 클러스터가 확장하려고 시도합니다.

- **실행**중 : 스크립트 폴더의 모든 스크립트가 계산 대상에 업로드되고 데이터 저장소가 탑재되거나 복사되고 entry_script 실행됩니다. stdout 및 ./logs 폴더의 출력은 실행 기록으로 스트리밍되며 실행을 모니터링하는 데 사용할 수 있습니다.

- **사후 처리**: 실행의 ./outputs 폴더가 실행 기록에 복사됩니다.

## <a name="register-or-download-a-model"></a>모델 등록 또는 다운로드

모델을 학습한 후에는 작업 영역에 등록할 수 있습니다. 모델 등록을 사용하면 모델 관리 및 배포를 간소화하기 위해 작업 영역에 모델을 [저장하고](concept-model-management-and-deployment.md)버전이 정번할 수 있습니다.

```Python
model = run.register_model(model_name='pt-dnn', model_path='outputs/')
```

> [!TIP]
> 방금 등록한 모델은 교육에 사용한 추정기에 관계없이 Azure Machine Learning의 다른 등록된 모델과 동일한 방식으로 배포됩니다. 배포 방법은 모델 등록에 대한 섹션을 포함하지만 이미 등록된 모델이 있으므로 배포에 대한 [계산 대상을 만드는](how-to-deploy-and-where.md#choose-a-compute-target) 데 직접 건너뛸 수 있습니다.

Run 개체를 사용하여 모델의 로컬 복사본을 다운로드할 수도 있습니다. 학습 스크립트에서 `pytorch_train.py`PyTorch 저장 개체는 모델을 로컬 폴더(계산 대상에 로컬)로 유지합니다. 실행 개체를 사용하여 복사본을 다운로드할 수 있습니다.

```Python
# Create a model folder in the current directory
os.makedirs('./model', exist_ok=True)

for f in run.get_file_names():
    if f.startswith('outputs/model'):
        output_file_path = os.path.join('./model', f.split('/')[-1])
        print('Downloading from {} to {} ...'.format(f, output_file_path))
        run.download_file(name=f, output_file_path=output_file_path)
```

## <a name="distributed-training"></a>분산 학습

또한 [`PyTorch`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py) 이 추정기는 CPU 및 GPU 클러스터 전반에 걸쳐 분산 교육을 지원합니다. 분산 PyTorch 작업을 쉽게 실행할 수 있으며 Azure 기계 학습은 오케스트레이션을 관리합니다.

### <a name="horovod"></a>Horovod
[Horovod는](https://github.com/uber/horovod) 오픈 소스, 모든 동네 짱에 의해 개발 된 분산 교육에 대 한 프레임 워크를 줄일. 그것은 분산 GPU PyTorch 작업에 쉬운 경로를 제공합니다.

Horovod를 사용하려면 [`MpiConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py) PyTorch `distributed_training` 생성자에서 매개 변수에 대한 개체를 지정합니다. 이 매개 변수는 교육 스크립트에서 사용할 수 있도록 Horovod 라이브러리가 설치되도록 합니다.


```Python
from azureml.train.dnn import PyTorch

estimator= PyTorch(source_directory=project_folder,
                      compute_target=compute_target,
                      script_params=script_params,
                      entry_script='script.py',
                      node_count=2,
                      process_count_per_node=1,
                      distributed_training=MpiConfiguration(),
                      framework_version='1.13',
                      use_gpu=True)
```
Horovod 및 해당 종속성이 설치되므로 다음과 같이 교육 스크립트에서 `train.py` 가져올 수 있습니다.

```Python
import torch
import horovod
```
## <a name="export-to-onnx"></a>ONNX로의 내보내기

[ONNX 런타임으로](concept-onnx.md)추론을 최적화하려면 숙련된 PyTorch 모델을 ONNX 형식으로 변환합니다. 추론 또는 모델 점수 매기기는 배포된 모델이 예측에 가장 일반적으로 사용되는 단계입니다. 예제에 대 한 [자습서를](https://github.com/onnx/tutorials/blob/master/tutorials/PytorchOnnxExport.ipynb) 참조 하십시오.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure 기계 학습에서 PyTorch를 사용하여 딥 러닝, 신경망을 학습하고 등록했습니다. 모델을 배포하는 방법을 알아보려면 모델 배포 문서를 계속 계속 설명합니다.

> [!div class="nextstepaction"]
> [모델을 배포하는 방법 및 위치](how-to-deploy-and-where.md)
* [학습 중에 실행 메트릭 추적](how-to-track-experiments.md)
* [하이퍼 매개 변수 조정](how-to-tune-hyperparameters.md)
* [학습된 모델 배포](how-to-deploy-and-where.md)
* [Azure의 분산 딥 러닝 교육을 위한 참조 아키텍처](/azure/architecture/reference-architectures/ai/training-deep-learning)

