---
title: 심층 학습 PyTorch 모델 학습
titleSuffix: Azure Machine Learning
description: Azure Machine Learning를 사용 하 여 엔터프라이즈 규모에서 PyTorch 교육 스크립트를 실행 하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: minxia
author: mx-iao
ms.reviewer: peterlu
ms.date: 09/28/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: bbc532acf704128e2311f440aabe8f707fc03aea
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93307232"
---
# <a name="train-pytorch-models-at-scale-with-azure-machine-learning"></a>Azure Machine Learning를 사용 하 여 대규모로 PyTorch 모델 학습

이 문서에서는 Azure Machine Learning를 사용 하 여 엔터프라이즈 규모에서 [PyTorch](https://pytorch.org/) 교육 스크립트를 실행 하는 방법에 대해 알아봅니다.

이 문서의 예제 스크립트는 PyTorch의 전송 학습 [자습서](https://pytorch.org/tutorials/beginner/transfer_learning_tutorial.html)에 따라 DNN (심층 학습 신경망)를 빌드하기 위해 닭 및 터키 이미지를 분류 하는 데 사용 됩니다. 

처음부터 심층 학습 PyTorch 모델을 학습 하 고 있거나 기존 모델을 클라우드로 가져오는 경우에는 Azure Machine Learning를 사용 하 여 탄력적 클라우드 계산 리소스를 사용 하 여 오픈 소스 학습 작업을 확장할 수 있습니다. Azure Machine Learning를 사용 하 여 프로덕션 등급 모델을 빌드, 배포, 버전 및 모니터링할 수 있습니다. 

## <a name="prerequisites"></a>필수 구성 요소

이러한 환경 중 하나에서이 코드를 실행 합니다.

- Azure Machine Learning 컴퓨팅 인스턴스 - 다운로드 또는 설치 필요 없음

    - 이 자습서를 시작하기 전에 [자습서: SDK 및 샘플 리포지토리로 미리 로드된 전용 Notebook 서버를 만들기 위한 환경 및 작업 영역](tutorial-1st-experiment-sdk-setup.md)을 설정합니다.
    - 노트북 서버의 샘플 심층 학습 폴더에서이 디렉터리로 이동 하 여 완료 되 고 확장 된 노트북을 찾습니다. **사용 방법-azureml > ml > pytorch > 학습-hyperparameter** 폴더입니다. 
 
 - 사용자 고유의 Jupyter Notebook 서버
    - [AZURE MACHINE LEARNING SDK](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py) (>= 1.15.0)를 설치 합니다.
    - [작업 영역 구성 파일을 만듭니다](how-to-configure-environment.md#workspace).
    - [샘플 스크립트 파일 다운로드](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/pytorch/train-hyperparameter-tune-deploy-with-pytorch)`pytorch_train.py`
     
    GitHub 샘플 페이지에서이 가이드의 전체 [Jupyter Notebook 버전](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/pytorch/train-hyperparameter-tune-deploy-with-pytorch/train-hyperparameter-tune-deploy-with-pytorch.ipynb) 을 찾을 수도 있습니다. 노트북에는 지능형 하이퍼 매개 변수 튜닝, 모델 배포 및 노트북 위젯을 다루는 확장 된 섹션이 포함 되어 있습니다.

## <a name="set-up-the-experiment"></a>실험 설정

이 섹션에서는 필요한 Python 패키지를 로드 하 고, 작업 영역을 초기화 하 고, 계산 대상을 만들고, 학습 환경을 정의 하 여 학습 실험을 설정 합니다.

### <a name="import-packages"></a>패키지 가져오기

먼저 필요한 Python 라이브러리를 가져옵니다.

```Python
import os
import shutil

from azureml.core.workspace import Workspace
from azureml.core import Experiment

from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException
```

### <a name="initialize-a-workspace"></a>작업 영역 초기화

[Azure Machine Learning 작업 영역은](concept-workspace.md) 서비스에 대 한 최상위 리소스입니다. 사용자가 만드는 모든 아티팩트를 사용할 수 있는 중앙 집중식 환경을 제공합니다. Python SDK에서 개체를 만들어 작업 영역 아티팩트에 액세스할 수 있습니다 [`workspace`](/python/api/azureml-core/azureml.core.workspace.workspace?preserve-view=true&view=azure-ml-py) .

`config.json` [전제 조건 섹션](#prerequisites)에서 만든 파일에서 작업 영역 개체를 만듭니다.

```Python
ws = Workspace.from_config()
```

### <a name="get-the-data"></a>데이터 가져오기

데이터 집합은 각 클래스에 대해 100 유효성 검사 이미지를 사용 하 여 chickens 키와 각각에 대 한 약 120 학습 이미지로 구성 됩니다. 데이터 집합은 학습 스크립트의 일부로 다운로드 하 고 압축을 풉니다 `pytorch_train.py` . 이미지는 [개방형 이미지 V5 데이터 집합](https://storage.googleapis.com/openimages/web/index.html)의 하위 집합입니다.

### <a name="prepare-training-script"></a>학습 스크립트 준비

이 자습서에서는 학습 스크립트 인 `pytorch_train.py` 가 이미 제공 되어 있습니다. 실제로는 사용자 지정 학습 스크립트를 그대로 사용 하 고 Azure Machine Learning를 사용 하 여 실행할 수 있습니다.

학습 스크립트에 대 한 폴더를 만듭니다.

```python
project_folder = './pytorch-birds'
os.makedirs(project_folder, exist_ok=True)
shutil.copy('pytorch_train.py', project_folder)
```

### <a name="create-a-compute-target"></a>컴퓨팅 대상 만들기

PyTorch 작업을 실행할 계산 대상을 만듭니다. 이 예제에서는 GPU 사용 Azure Machine Learning 계산 클러스터를 만듭니다.

```Python
cluster_name = "gpu-cluster"

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

[!INCLUDE [low-pri-note](../../includes/machine-learning-low-pri-vm.md)]

계산 대상에 대 한 자세한 내용은 [계산 대상 이란?](concept-compute-target.md) 문서를 참조 하세요.

### <a name="define-your-environment"></a>환경 정의

학습 스크립트의 종속성을 캡슐화 하는 Azure ML [환경을](concept-environments.md) 정의 하려면 사용자 지정 환경을 정의 하거나 azure ml 큐 레이트 환경을 사용할 수 있습니다.

#### <a name="use-a-curated-environment"></a>큐 레이트 환경 사용
사용자 고유의 환경을 정의 하지 않으려는 경우 Azure ML은 미리 빌드된 큐 레이트 환경을 제공 합니다. Azure ML에는 다양 한 버전의 PyTorch에 해당 하는 PyTorch에 대 한 몇 가지 CPU 및 GPU 큐 레이트 환경이 있습니다. 자세한 내용은 [여기](resource-curated-environments.md)를 참조 하세요.

큐 레이트 환경을 사용 하려는 경우 다음 명령을 대신 실행할 수 있습니다.

```python
curated_env_name = 'AzureML-PyTorch-1.6-GPU'
pytorch_env = Environment.get(workspace=ws, name=curated_env_name)
```

큐 레이트 환경에 포함 된 패키지를 보려면 conda 종속성을 디스크에 쓸 수 있습니다.
```python
pytorch_env.save_to_directory(path=curated_env_name)
```

큐 레이트 환경에 학습 스크립트에 필요한 모든 종속성이 포함 되어 있는지 확인 합니다. 그렇지 않은 경우 누락 된 종속성을 포함 하도록 환경을 수정 해야 합니다. 환경을 수정 하는 경우 ' AzureML ' 접두사가 큐 레이트 환경용으로 예약 되어 있으므로 새 이름을 지정 해야 합니다. Conda 종속성 YAML 파일을 수정한 경우 새 이름을 사용 하 여 새 환경을 만들 수 있습니다. 예를 들면 다음과 같습니다.
```python
pytorch_env = Environment.from_conda_specification(name='pytorch-1.6-gpu', file_path='./conda_dependencies.yml')
```

대신 큐 레이트 환경 개체를 직접 수정한 경우 새 이름으로 해당 환경을 복제할 수 있습니다.
```python
pytorch_env = pytorch_env.clone(new_name='pytorch-1.6-gpu')
```

#### <a name="create-a-custom-environment"></a>사용자 지정 환경 만들기

또한 학습 스크립트의 종속성을 캡슐화 하는 Azure ML 환경을 직접 만들 수 있습니다.

먼저, YAML 파일에서 conda 종속성을 정의 합니다. 이 예제에서 파일의 이름은 `conda_dependencies.yml` 입니다.

```yaml
channels:
- conda-forge
dependencies:
- python=3.6.2
- pip:
  - azureml-defaults
  - torch==1.6.0
  - torchvision==0.7.0
  - future==0.17.1
  - pillow
```

이 conda 환경 사양에서 Azure ML 환경을 만듭니다. 환경은 런타임에 Docker 컨테이너에 패키지 됩니다.

기본적으로 기본 이미지가 지정 되지 않은 경우 Azure ML은 CPU 이미지를 `azureml.core.environment.DEFAULT_CPU_IMAGE` 기본 이미지로 사용 합니다. 이 예제에서는 GPU 클러스터에서 학습을 실행 하므로 필요한 GPU 드라이버 및 종속성이 있는 GPU 기본 이미지를 지정 해야 합니다. Azure ML은 사용자가 사용할 수 있는 Microsoft Container Registry (MCR)에 게시 된 기본 이미지 집합을 유지 관리 합니다. 자세한 내용은 [azure/AzureML 컨테이너](https://github.com/Azure/AzureML-Containers) GitHub 리포지토리를 참조 하세요.

```python
from azureml.core import Environment

pytorch_env = Environment.from_conda_specification(name='pytorch-1.6-gpu', file_path='./conda_dependencies.yml')

# Specify a GPU base image
pytorch_env.docker.enabled = True
pytorch_env.docker.base_image = 'mcr.microsoft.com/azureml/openmpi3.1.2-cuda10.1-cudnn7-ubuntu18.04'
```

> [!TIP]
> 필요에 따라 사용자 지정 Docker 이미지 또는 Dockerfile에서 모든 종속성을 직접 캡처하고 해당 환경에서 환경을 만들 수 있습니다. 자세한 내용은 [사용자 지정 이미지를 사용 하 여 학습](how-to-train-with-custom-image.md)을 참조 하세요.

환경을 만들고 사용 하는 방법에 대 한 자세한 내용은 [Azure Machine Learning에서 소프트웨어 환경 만들기 및 사용](how-to-use-environments.md)을 참조 하세요.

## <a name="configure-and-submit-your-training-run"></a>학습 실행 구성 및 제출

### <a name="create-a-scriptrunconfig"></a>ScriptRunConfig 만들기

[ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig?preserve-view=true&view=azure-ml-py) 개체를 만들어 학습 스크립트, 사용할 환경 및 실행할 컴퓨팅 대상 등 학습 작업의 구성 세부 정보를 지정합니다. 매개 변수에 지정 된 경우 학습 스크립트에 대 한 모든 인수는 명령줄을 통해 전달 됩니다 `arguments` . 

```python
from azureml.core import ScriptRunConfig

src = ScriptRunConfig(source_directory=project_folder,
                      script='pytorch_train.py',
                      arguments=['--num_epochs', 30, '--output_dir', './outputs'],
                      compute_target=compute_target,
                      environment=pytorch_env)
```

> [!WARNING]
> Azure Machine Learning는 전체 원본 디렉터리를 복사 하 여 학습 스크립트를 실행 합니다. 업로드 하지 않으려는 중요 한 데이터가 있는 경우 [무시 파일](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots) 을 사용 하거나 원본 디렉터리에이 파일을 포함 하지 마세요. 대신, Azure ML [데이터 집합](how-to-train-with-datasets.md)을 사용 하 여 데이터에 액세스 합니다.

ScriptRunConfig를 사용 하 여 작업을 구성 하는 방법에 대 한 자세한 내용은 [학습 실행 구성 및 제출](how-to-set-up-training-targets.md)을 참조 하세요.

> [!WARNING]
> 이전에 PyTorch 평가기를 사용 하 여 PyTorch 교육 작업을 구성한 경우 Azure ML SDK의 이후 릴리스에서는 추정가 더 이상 사용 되지 않습니다. Azure ML SDK >= 1.15.0를 사용 하는 경우 ScriptRunConfig는 DL 프레임 워크를 사용 하 여 학습 작업을 구성 하는 데 권장 되는 방법입니다.

## <a name="submit-your-run"></a>실행 제출

[실행 개체](/python/api/azureml-core/azureml.core.run%28class%29?preserve-view=true&view=azure-ml-py) 는 작업이 실행 되는 동안 그리고 작업이 완료 된 후 실행 기록에 인터페이스를 제공 합니다.

```Python
run = Experiment(ws, name='pytorch-birds').submit(src)
run.wait_for_completion(show_output=True)
```

### <a name="what-happens-during-run-execution"></a>실행 실행 중 수행 되는 작업
실행이 실행 되 면 다음 단계를 거칩니다.

- **준비** : 정의 된 환경에 따라 docker 이미지가 생성 됩니다. 이미지는 작업 영역 컨테이너 레지스트리로 업로드 되 고 나중에 실행할 수 있도록 캐시 됩니다. 로그는 실행 기록에도 스트리밍되 고 진행률을 모니터링 하기 위해 볼 수 있습니다. 큐 레이트 환경을 대신 지정 하면 해당 큐 레이트 환경을 지 원하는 캐시 된 이미지가 사용 됩니다.

- **크기 조정** : 클러스터는 현재 사용 가능한 것 보다 더 많은 노드를 실행 하는 Batch AI 클러스터가 필요한 경우 확장을 시도 합니다.

- **실행 중** : 스크립트 폴더의 모든 스크립트가 계산 대상으로 업로드 되 고, 데이터 저장소가 탑재 되거나 복사 되 고, `script` 이 실행 됩니다. Stdout의 출력과 **./clogs** 폴더는 실행 기록으로 스트리밍되 며 실행을 모니터링 하는 데 사용할 수 있습니다.

- **사후 처리** : 실행의 **./출력** 폴더가 실행 기록에 복사 됩니다.

## <a name="register-or-download-a-model"></a>모델 등록 또는 다운로드

모델을 학습 한 후에는 작업 영역에 등록할 수 있습니다. 모델 등록을 사용하면 모델을 작업 영역에 저장하고 버전을 지정하여 [모델 관리 및 배포](concept-model-management-and-deployment.md)를 간소화할 수 있습니다.

```Python
model = run.register_model(model_name='pytorch-birds', model_path='outputs/model.pt')
```

> [!TIP]
> 배포 방법에는 모델 등록에 대 한 섹션이 포함 되어 있지만 등록 된 모델이 이미 있기 때문에 배포에 대 한 [계산 대상을 직접 만드는](how-to-deploy-and-where.md#choose-a-compute-target) 것으로 건너뛸 수 있습니다.

Run 개체를 사용 하 여 모델의 로컬 복사본을 다운로드할 수도 있습니다. 학습 스크립트에서 `pytorch_train.py` PyTorch save 개체는 모델을 로컬 폴더 (계산 대상의 로컬 폴더)에 유지 합니다. Run 개체를 사용 하 여 복사본을 다운로드할 수 있습니다.

```Python
# Create a model folder in the current directory
os.makedirs('./model', exist_ok=True)

# Download the model from run history
run.download_file(name='outputs/model.pt', output_file_path='./model/model.pt'), 
```

## <a name="distributed-training"></a>분산 학습

또한 Azure Machine Learning은 학습 워크 로드의 크기를 조정할 수 있도록 다중 노드 distributed PyTorch 작업을 지원 합니다. Distributed PyTorch 작업을 쉽게 실행할 수 있으며 Azure ML은 오케스트레이션을 관리 합니다.

Azure ML에서는 Horovod 및 PyTorch의 기본 제공 DistributedDataParallel 모듈을 모두 사용 하 여 분산 PyTorch 작업을 실행할 수 있습니다.

### <a name="horovod"></a>Horovod
[Horovod](https://github.com/uber/horovod) 는 uber에서 개발한 분산 교육을 위한 오픈 소스를 모두 줄인 프레임 워크입니다. 이는 학습을 위해 분산 PyTorch 코드를 작성 하는 쉬운 경로를 제공 합니다.

분산 교육을 위해 Horovod를 사용 하 여 교육 코드를 계측 해야 합니다. PyTorch와 함께 Horovod를 사용 하는 방법에 대 한 자세한 내용은 [Horovod 설명서](https://horovod.readthedocs.io/en/stable/pytorch.html)를 참조 하세요.

또한 교육 환경에 **horovod** 패키지가 포함 되어 있는지 확인 합니다. PyTorch 큐 레이트 환경을 사용 하는 경우 horovod는 이미 종속성 중 하나로 포함 되어 있습니다. 사용자 고유의 환경을 사용 하는 경우 horovod 종속성이 포함 되어 있는지 확인 합니다. 예를 들면 다음과 같습니다.

```yaml
channels:
- conda-forge
dependencies:
- python=3.6.2
- pip:
  - azureml-defaults
  - torch==1.6.0
  - torchvision==0.7.0
  - horovod==0.19.5
```

Azure ML에서 MPI/Horovod를 사용 하 여 분산 작업을 실행 하려면 [MpiConfiguration](/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?preserve-view=true&view=azure-ml-py) `distributed_job_config` ScriptRunConfig 생성자의 매개 변수에 MpiConfiguration를 지정 해야 합니다. 아래 코드에서는 노드당 하나의 프로세스를 실행 하는 2 노드 분산 작업을 구성 합니다. 노드당 여러 프로세스를 실행 하려는 경우 (즉, 클러스터 SKU에 여러 Gpu가 있는 경우) `process_count_per_node` MpiConfiguration에서 매개 변수를 추가로 지정 합니다 (기본값은 `1` ).

```python
from azureml.core import ScriptRunConfig
from azureml.core.runconfig import MpiConfiguration

src = ScriptRunConfig(source_directory=project_folder,
                      script='pytorch_horovod_mnist.py',
                      compute_target=compute_target,
                      environment=pytorch_env,
                      distributed_job_config=MpiConfiguration(node_count=2))
```

Azure ML에서 Horovod로 distributed PyTorch를 실행 하는 방법에 대 한 전체 자습서는 [Horovod를 사용 하는 Distributed PyTorch](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/pytorch/distributed-pytorch-with-horovod)를 참조 하세요.

### <a name="distributeddataparallel"></a>DistributedDataParallel
교육 코드에서 **torch** 패키지를 사용 하 여 작성 된 PyTorch의 기본 제공 [DistributedDataParallel](https://pytorch.org/tutorials/intermediate/ddp_tutorial.html) 모듈을 사용 하는 경우 Azure ML을 통해 분산 작업을 시작할 수도 있습니다.

DistributedDataParallel를 사용 하 여 distributed PyTorch 작업을 실행 하려면 [PyTorchConfiguration](/python/api/azureml-core/azureml.core.runconfig.pytorchconfiguration?preserve-view=true&view=azure-ml-py) `distributed_job_config` ScriptRunConfig 생성자의 매개 변수에 PyTorchConfiguration를 지정 합니다. Torch에 NCCL 백엔드를 사용 하려면 PyTorchConfiguration에를 지정 합니다 `communication_backend='Nccl'` . 다음 코드는 2 노드 분산 작업을 구성 합니다. NCCL 백 엔드는 PyTorch distributed GPU 학습에 권장 되는 백 엔드입니다.

PyTorchConfiguration를 통해 구성 된 distributed PyTorch 작업의 경우 Azure ML은 계산 대상의 노드에서 다음 환경 변수를 설정 합니다.

* `AZ_BATCHAI_PYTORCH_INIT_METHOD`: 프로세스 그룹의 공유 파일 시스템 초기화에 대 한 URL
* `AZ_BATCHAI_TASK_INDEX`: 작업자 프로세스의 전역 순위

ScriptRunConfig의 매개 변수를 통해 이러한 환경 변수를 학습 스크립트의 해당 인수에 지정할 수 있습니다 `arguments` .

```python
from azureml.core import ScriptRunConfig
from azureml.core.runconfig import PyTorchConfiguration

args = ['--dist-backend', 'nccl',
        '--dist-url', '$AZ_BATCHAI_PYTORCH_INIT_METHOD',
        '--rank', '$AZ_BATCHAI_TASK_INDEX',
        '--world-size', 2]

src = ScriptRunConfig(source_directory=project_folder,
                      script='pytorch_mnist.py',
                      arguments=args,
                      compute_target=compute_target,
                      environment=pytorch_env,
                      distributed_job_config=PyTorchConfiguration(communication_backend='Nccl', node_count=2))
```

분산 학습에 Gloo 백 엔드를 사용 하려는 경우 대신를 지정 `communication_backend='Gloo'` 합니다. 분산 CPU 학습에는 Gloo 백 엔드가 권장 됩니다.

Azure ML에서 distributed PyTorch을 실행 하는 방법에 대 한 전체 자습서는 [DistributedDataParallel로 Distributed PyTorch](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/pytorch/distributed-pytorch-with-nccl-gloo)를 참조 하세요.

## <a name="export-to-onnx"></a>ONNX로 내보내기

[Onnx 런타임으로](concept-onnx.md)유추를 최적화 하려면 학습 된 PyTorch 모델을 onnx 형식으로 변환 합니다. 유추 또는 모델 점수 매기기는 배포 된 모델이 예측에 사용 되는 단계 이며 가장 일반적으로 프로덕션 데이터에 사용 됩니다. 예제는 [자습서](https://github.com/onnx/tutorials/blob/master/tutorials/PytorchOnnxExport.ipynb) 를 참조 하십시오.

## <a name="next-steps"></a>다음 단계

이 문서에서는 PyTorch Azure Machine Learning에서를 사용 하 여 딥 러닝 신경망을 학습 하 고 등록 했습니다. 모델을 배포 하는 방법에 대 한 자세한 내용은 모델 배포 문서를 참조 하세요.

* [모델을 배포 하는 방법 및 위치](how-to-deploy-and-where.md)
* [학습 중에 실행 메트릭 추적](how-to-track-experiments.md)
* [하이퍼 매개 변수 조정](how-to-tune-hyperparameters.md)
* [학습된 모델 배포](how-to-deploy-and-where.md)
* [Azure의 분산 심층 학습 교육에 대 한 참조 아키텍처](/azure/architecture/reference-architectures/ai/training-deep-learning)