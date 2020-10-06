---
title: TensorFlow 모델 학습 및 배포
titleSuffix: Azure Machine Learning
description: Azure Machine Learning를 사용 하 여 대규모로 TensorFlow 교육 스크립트를 실행 하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: minxia
author: mx-iao
ms.date: 09/28/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 21a0672db5a7038fbcdeb01e4cf07bcd760cf7ef
ms.sourcegitcommit: a07a01afc9bffa0582519b57aa4967d27adcf91a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2020
ms.locfileid: "91742998"
---
# <a name="train-tensorflow-models-at-scale-with-azure-machine-learning"></a>Azure Machine Learning를 사용 하 여 대규모로 TensorFlow 모델 학습

이 문서에서는 Azure Machine Learning를 사용 하 여 대규모로 [TensorFlow](https://www.tensorflow.org/overview) 교육 스크립트를 실행 하는 방법에 대해 알아봅니다.

이 예제에서는 TensorFlow 모델을 학습 하 고 등록 하 여 DNN (심층 신경망)를 사용 하 여 필기 된 숫자를 분류 합니다.

TensorFlow 모델을 처음부터 개발 하 든, [기존 모델](how-to-deploy-existing-model.md) 을 클라우드로 가져오는 경우에는 Azure Machine Learning를 사용 하 여 오픈 소스 학습 작업을 확장 하 여 프로덕션 등급 모델을 빌드, 배포, 버전 및 모니터링할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

이러한 환경 중 하나에서이 코드를 실행 합니다.

 - Azure Machine Learning 컴퓨팅 인스턴스 - 다운로드 또는 설치 필요 없음

     - 이 자습서를 시작하기 전에 [자습서: SDK 및 샘플 리포지토리로 미리 로드된 전용 Notebook 서버를 만들기 위한 환경 및 작업 영역](tutorial-1st-experiment-sdk-setup.md)을 설정합니다.
    - 노트북 서버의 샘플 심층 학습 폴더에서이 디렉터리로 이동 하 여 완료 되 고 확장 된 노트북을 찾습니다. **사용 방법-azureml > ml > tensorflow > 학습-hyperparameter** 폴더입니다. 
 
 - 사용자 고유의 Jupyter Notebook 서버

    - [AZURE MACHINE LEARNING SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true) (>= 1.15.0)를 설치 합니다.
    - [작업 영역 구성 파일을 만듭니다](how-to-configure-environment.md#workspace).
    - [샘플 스크립트 파일 다운로드](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/tensorflow/train-hyperparameter-tune-deploy-with-tensorflow) `tf_mnist.py` 하거나 `utils.py`
     
    GitHub 샘플 페이지에서이 가이드의 전체 [Jupyter Notebook 버전](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/tensorflow/train-hyperparameter-tune-deploy-with-tensorflow/train-hyperparameter-tune-deploy-with-tensorflow.ipynb) 을 찾을 수도 있습니다. 노트북에는 지능형 하이퍼 매개 변수 튜닝, 모델 배포 및 노트북 위젯을 다루는 확장 된 섹션이 포함 되어 있습니다.

## <a name="set-up-the-experiment"></a>실험 설정

이 섹션에서는 필요한 Python 패키지를 로드 하 고, 작업 영역을 초기화 하 고, 계산 대상을 만들고, 학습 환경을 정의 하 여 학습 실험을 설정 합니다.

### <a name="import-packages"></a>패키지 가져오기

먼저 필요한 Python 라이브러리를 가져옵니다.

```Python
import os
import urllib
import shutil
import azureml

from azureml.core import Experiment
from azureml.core import Workspace, Run

from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException
```

### <a name="initialize-a-workspace"></a>작업 영역 초기화

[Azure Machine Learning 작업 영역은](concept-workspace.md) 서비스에 대 한 최상위 리소스입니다. 사용자가 만드는 모든 아티팩트를 사용할 수 있는 중앙 집중식 환경을 제공합니다. Python SDK에서 개체를 만들어 작업 영역 아티팩트에 액세스할 수 있습니다 [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py&preserve-view=true) .

`config.json` [전제 조건 섹션](#prerequisites)에서 만든 파일에서 작업 영역 개체를 만듭니다.

```Python
ws = Workspace.from_config()
```

### <a name="create-a-file-dataset"></a>파일 데이터 집합 만들기

`FileDataset` 개체는 작업 영역 데이터 저장소 또는 공용 URL에서 하나 이상의 파일을 참조합니다. 파일은 모든 형식일 수 있으며, 클래스는 컴퓨팅에 파일을 다운로드하거나 탑재하는 기능을 제공합니다. `FileDataset`를 생성하여 데이터 원본 위치에 대한 참조를 만듭니다. 데이터 세트에 변환을 적용한 경우 데이터 세트에도 저장됩니다. 데이터는 기존 위치에 그대로 남아 있으므로 추가 스토리지 비용이 발생하지 않습니다. 자세한 내용은 `Dataset` 패키지에 대한 [방법](https://docs.microsoft.com/azure/machine-learning/how-to-create-register-datasets) 가이드를 참조하세요.

```python
from azureml.core.dataset import Dataset

web_paths = [
            'http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz'
            ]
dataset = Dataset.File.from_files(path=web_paths)
```

메서드를 사용 `register()` 하 여 데이터 집합을 다른 사용자와 공유 하 고, 다양 한 실험에서 재사용 하 고, 학습 스크립트에서 이름으로 참조할 수 있도록 작업 영역에 데이터 집합을 등록할 수 있습니다.

```python
dataset = dataset.register(workspace=ws,
                           name='mnist-dataset',
                           description='training and test dataset',
                           create_new_version=True)

# list the files referenced by dataset
dataset.to_path()
```

### <a name="create-a-compute-target"></a>컴퓨팅 대상 만들기

TensorFlow 작업을 실행할 계산 대상을 만듭니다. 이 예제에서는 GPU 사용 Azure Machine Learning 계산 클러스터를 만듭니다.

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
사용자 고유의 환경을 정의 하지 않으려는 경우 Azure ML은 미리 빌드된 큐 레이트 환경을 제공 합니다. Azure ML에는 다양 한 버전의 TensorFlow에 해당 하는 TensorFlow에 대 한 몇 가지 CPU 및 GPU 큐 레이트 환경이 있습니다. 자세한 내용은 [여기](resource-curated-environments.md)를 참조 하세요.

큐 레이트 환경을 사용 하려는 경우 다음 명령을 대신 실행할 수 있습니다.

```python
curated_env_name = 'AzureML-TensorFlow-2.2-GPU'
tf_env = Environment.get(workspace=ws, name=curated_env_name)
```

큐 레이트 환경에 포함 된 패키지를 보려면 conda 종속성을 디스크에 쓸 수 있습니다.
```python
tf_env.save_to_directory(path=curated_env_name)
```

큐 레이트 환경에 학습 스크립트에 필요한 모든 종속성이 포함 되어 있는지 확인 합니다. 그렇지 않은 경우 누락 된 종속성을 포함 하도록 환경을 수정 해야 합니다. 환경을 수정 하는 경우 ' AzureML ' 접두사가 큐 레이트 환경용으로 예약 되어 있으므로 새 이름을 지정 해야 합니다. Conda 종속성 YAML 파일을 수정한 경우 새 이름을 사용 하 여 새 환경을 만들 수 있습니다. 예를 들면 다음과 같습니다.
```python
tf_env = Environment.from_conda_specification(name='tensorflow-2.2-gpu', file_path='./conda_dependencies.yml')
```

대신 큐 레이트 환경 개체를 직접 수정한 경우 새 이름으로 해당 환경을 복제할 수 있습니다.
```python
tf_env = tf_env.clone(new_name='tensorflow-2.2-gpu')
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
  - tensorflow-gpu==2.2.0
```

이 conda 환경 사양에서 Azure ML 환경을 만듭니다. 환경은 런타임에 Docker 컨테이너에 패키지 됩니다.

기본적으로 기본 이미지가 지정 되지 않은 경우 Azure ML은 CPU 이미지를 `azureml.core.environment.DEFAULT_CPU_IMAGE` 기본 이미지로 사용 합니다. 이 예제에서는 GPU 클러스터에서 학습을 실행 하므로 필요한 GPU 드라이버 및 종속성이 있는 GPU 기본 이미지를 지정 해야 합니다. Azure ML은 사용자가 사용할 수 있는 Microsoft Container Registry (MCR)에 게시 된 기본 이미지 집합을 유지 관리 합니다. 자세한 내용은 [azure/AzureML 컨테이너](https://github.com/Azure/AzureML-Containers) GitHub 리포지토리를 참조 하세요.

```python
from azureml.core import Environment

tf_env = Environment.from_conda_specification(name='tensorflow-2.2-gpu', file_path='./conda_dependencies.yml')

# Specify a GPU base image
tf_env.docker.enabled = True
tf_env.docker.base_image = 'mcr.microsoft.com/azureml/openmpi3.1.2-cuda10.1-cudnn7-ubuntu18.04'
```

> [!TIP]
> 필요에 따라 사용자 지정 Docker 이미지 또는 Dockerfile에서 모든 종속성을 직접 캡처하고 해당 환경에서 환경을 만들 수 있습니다. 자세한 내용은 [사용자 지정 이미지를 사용 하 여 학습](how-to-train-with-custom-image.md)을 참조 하세요.

환경을 만들고 사용 하는 방법에 대 한 자세한 내용은 [Azure Machine Learning에서 소프트웨어 환경 만들기 및 사용](how-to-use-environments.md)을 참조 하세요.

## <a name="configure-and-submit-your-training-run"></a>학습 실행 구성 및 제출

### <a name="create-a-scriptrunconfig"></a>ScriptRunConfig 만들기

[ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py&preserve-view=true) 개체를 만들어 학습 스크립트, 사용할 환경 및 실행할 계산 대상 등 학습 작업의 구성 세부 정보를 지정 합니다. 매개 변수에 지정 된 경우 학습 스크립트에 대 한 모든 인수는 명령줄을 통해 전달 됩니다 `arguments` .

```python
from azureml.core import ScriptRunConfig

args = ['--data-folder', dataset.as_mount(),
        '--batch-size', 64,
        '--first-layer-neurons', 256,
        '--second-layer-neurons', 128,
        '--learning-rate', 0.01]

src = ScriptRunConfig(source_directory=script_folder,
                      script='tf_mnist.py',
                      arguments=args,
                      compute_target=compute_target,
                      environment=tf_env)
```

> [!WARNING]
> Azure Machine Learning는 전체 원본 디렉터리를 복사 하 여 학습 스크립트를 실행 합니다. 업로드 하지 않으려는 중요 한 데이터가 있는 경우 [무시 파일](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots) 을 사용 하거나 원본 디렉터리에이 파일을 포함 하지 마세요. 대신, Azure ML [데이터 집합](how-to-train-with-datasets.md)을 사용 하 여 데이터에 액세스 합니다.

ScriptRunConfig를 사용 하 여 작업을 구성 하는 방법에 대 한 자세한 내용은 [학습 실행 구성 및 제출](how-to-set-up-training-targets.md)을 참조 하세요.

> [!WARNING]
> 이전에 TensorFlow 평가기를 사용 하 여 TensorFlow 교육 작업을 구성한 경우 Azure ML SDK의 이후 릴리스에서는 추정가 더 이상 사용 되지 않습니다. Azure ML SDK >= 1.15.0를 사용 하는 경우 ScriptRunConfig는 DL 프레임 워크를 사용 하 여 학습 작업을 구성 하는 데 권장 되는 방법입니다.

### <a name="submit-a-run"></a>실행 제출

[실행 개체](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py&preserve-view=true) 는 작업이 실행 되는 동안 그리고 작업이 완료 된 후 실행 기록에 인터페이스를 제공 합니다.

```Python
run = Experiment(workspace=ws, name='tf-mnist').submit(src)
run.wait_for_completion(show_output=True)
```
### <a name="what-happens-during-run-execution"></a>실행 실행 중 수행 되는 작업
실행이 실행 되 면 다음 단계를 거칩니다.

- **준비**: 정의 된 환경에 따라 docker 이미지가 생성 됩니다. 이미지는 작업 영역 컨테이너 레지스트리로 업로드 되 고 나중에 실행할 수 있도록 캐시 됩니다. 로그는 실행 기록에도 스트리밍되 고 진행률을 모니터링 하기 위해 볼 수 있습니다. 큐 레이트 환경을 대신 지정 하면 해당 큐 레이트 환경을 지 원하는 캐시 된 이미지가 사용 됩니다.

- **크기 조정**: 클러스터는 현재 사용 가능한 것 보다 더 많은 노드를 실행 하는 Batch AI 클러스터가 필요한 경우 확장을 시도 합니다.

- **실행 중**: 스크립트 폴더의 모든 스크립트가 계산 대상으로 업로드 되 고, 데이터 저장소가 탑재 되거나 복사 되 고, `script` 이 실행 됩니다. Stdout의 출력과 **./clogs** 폴더는 실행 기록으로 스트리밍되 며 실행을 모니터링 하는 데 사용할 수 있습니다.

- **사후 처리**: 실행의 **./출력** 폴더가 실행 기록에 복사 됩니다.

## <a name="register-or-download-a-model"></a>모델 등록 또는 다운로드

모델을 학습 한 후에는 작업 영역에 등록할 수 있습니다. 모델 등록을 사용하면 모델을 작업 영역에 저장하고 버전을 지정하여 [모델 관리 및 배포](concept-model-management-and-deployment.md)를 간소화할 수 있습니다. 선택 사항: 매개 변수, 및를 지정 하 여 `model_framework` `model_framework_version` `resource_configuration` 코드 없는 모델 배포를 사용할 수 있습니다. 이렇게 하면 등록 된 모델에서 모델을 웹 서비스로 직접 배포할 수 있으며, `ResourceConfiguration` 개체는 웹 서비스에 대 한 계산 리소스를 정의 합니다.

```Python
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

model = run.register_model(model_name='tf-mnist', 
                           model_path='outputs/model',
                           model_framework=Model.Framework.TENSORFLOW,
                           model_framework_version='2.0',
                           resource_configuration=ResourceConfiguration(cpu=1, memory_in_gb=0.5))
```

Run 개체를 사용 하 여 모델의 로컬 복사본을 다운로드할 수도 있습니다. 학습 스크립트에서 `tf_mnist.py` TensorFlow 보호기 개체는 로컬 폴더 (계산 대상의 로컬)에 모델을 유지 합니다. Run 개체를 사용 하 여 복사본을 다운로드할 수 있습니다.

```Python
# Create a model folder in the current directory
os.makedirs('./model', exist_ok=True)
run.download_files(prefix='outputs/model', output_directory='./model', append_prefix=False)
```

## <a name="distributed-training"></a>분산 학습

또한 Azure Machine Learning은 학습 워크 로드의 크기를 조정할 수 있도록 다중 노드 distributed TensorFlow 작업을 지원 합니다. Distributed TensorFlow 작업을 쉽게 실행할 수 있으며 Azure ML은 오케스트레이션을 관리 합니다.

Azure ML에서는 Horovod 및 TensorFlow의 기본 제공 distributed 교육 API를 사용 하 여 분산 TensorFlow 작업을 실행할 수 있습니다.

### <a name="horovod"></a>Horovod
[Horovod](https://github.com/uber/horovod) 는 uber에서 개발한 분산 교육을 위한 오픈 소스를 모두 줄인 프레임 워크입니다. 이는 학습을 위해 분산 TensorFlow 코드를 작성 하는 쉬운 경로를 제공 합니다.

분산 교육을 위해 Horovod를 사용 하 여 교육 코드를 계측 해야 합니다. TensorFlow와 함께 Horovod를 사용 하는 방법에 대 한 자세한 내용은 Horovod 설명서를 참조 하세요.

TensorFlow와 함께 Horovod를 사용 하는 방법에 대 한 자세한 내용은 Horovod 설명서를 참조 하세요.

* [TensorFlow를 사용 하는 Horovod](https://github.com/horovod/horovod/blob/master/docs/tensorflow.rst)
* [TensorFlow의 Keras API를 사용 하는 Horovod](https://github.com/horovod/horovod/blob/master/docs/keras.rst)

또한 교육 환경에 **horovod** 패키지가 포함 되어 있는지 확인 합니다. TensorFlow 큐 레이트 환경을 사용 하는 경우 horovod는 이미 종속성 중 하나로 포함 되어 있습니다. 사용자 고유의 환경을 사용 하는 경우 horovod 종속성이 포함 되어 있는지 확인 합니다. 예를 들면 다음과 같습니다.

```yaml
channels:
- conda-forge
dependencies:
- python=3.6.2
- pip:
  - azureml-defaults
  - tensorflow-gpu==2.2.0
  - horovod==0.19.5
```

Azure ML에서 MPI/Horovod를 사용 하 여 분산 작업을 실행 하려면 [MpiConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py&preserve-view=true) `distributed_job_config` ScriptRunConfig 생성자의 매개 변수에 MpiConfiguration를 지정 해야 합니다. 아래 코드에서는 노드당 하나의 프로세스를 실행 하는 2 노드 분산 작업을 구성 합니다. 노드당 여러 프로세스를 실행 하려는 경우 (즉, 클러스터 SKU에 여러 Gpu가 있는 경우) `process_count_per_node` MpiConfiguration에서 매개 변수를 추가로 지정 합니다 (기본값은 `1` ).

```python
from azureml.core import ScriptRunConfig
from azureml.core.runconfig import MpiConfiguration

src = ScriptRunConfig(source_directory=project_folder,
                      script='tf_horovod_word2vec.py',
                      arguments=['--input_data', dataset.as_mount()],
                      compute_target=compute_target,
                      environment=tf_env,
                      distributed_job_config=MpiConfiguration(node_count=2))
```

Azure ML에서 Horovod로 distributed TensorFlow를 실행 하는 방법에 대 한 전체 자습서는 [Horovod를 사용 하는 Distributed TensorFlow](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/tensorflow/distributed-tensorflow-with-horovod)를 참조 하세요.

### <a name="tfdistribute"></a>tf. 배포

교육 코드에서 [네이티브 Distributed TensorFlow](https://www.tensorflow.org/guide/distributed_training) (예: TensorFlow 2.X의 API)를 사용 하는 경우 `tf.distribute.Strategy` Azure ML을 통해 분산 작업을 시작할 수도 있습니다. 

이렇게 하려면 [TensorflowConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.tensorflowconfiguration?view=azure-ml-py&preserve-view=true) `distributed_job_config` ScriptRunConfig 생성자의 매개 변수에 TensorflowConfiguration를 지정 합니다. 를 사용 하는 경우 `tf.distribute.experimental.MultiWorkerMirroredStrategy` `worker_count` 학습 작업에 대 한 노드 수에 해당 하는 TensorflowConfiguration에서을 지정 합니다.

```python
import os
from azureml.core import ScriptRunConfig
from azureml.core.runconfig import TensorflowConfiguration

distr_config = TensorflowConfiguration(worker_count=2, parameter_server_count=0)

model_path = os.path.join("./outputs", "keras-model")

src = ScriptRunConfig(source_directory=source_dir,
                      script='train.py',
                      arguments=["--epochs", 30, "--model-dir", model_path],
                      compute_target=compute_target,
                      environment=tf_env,
                      distributed_job_config=distr_config)
```

TensorFlow에서는 `TF_CONFIG` 여러 컴퓨터에 대 한 학습을 위해 환경 변수가 필요 합니다. Azure ML은 `TF_CONFIG` 학습 스크립트를 실행 하기 전에 각 작업자에 대해 변수를 적절 하 게 구성 하 고 설정 합니다. `TF_CONFIG`를 통해 필요한 경우 학습 스크립트에서에 액세스할 수 있습니다 `os.environ['TF_CONFIG']` .

`TF_CONFIG`최고 작업자 노드에 set의 예제 구조:
```JSON
TF_CONFIG='{
    "cluster": {
        "worker": ["host0:2222", "host1:2222"]
    },
    "task": {"type": "worker", "index": 0},
    "environment": "cloud"
}'
```

학습 스크립트에서 분산 학습에 대 한 매개 변수 서버 전략을 사용 하는 경우 (예: 레거시 TensorFlow 1.x의 경우) 작업에서 사용할 매개 변수 서버 수 (예:)도 지정 해야 합니다 `distr_config = TensorflowConfiguration(worker_count=2, parameter_server_count=1)` .

## <a name="deploy-a-tensorflow-model"></a>TensorFlow 모델 배포

배포 방법에는 모델 등록에 대 한 섹션이 포함 되어 있지만 등록 된 모델이 이미 있기 때문에 배포에 대 한 [계산 대상을 직접 만드는](how-to-deploy-and-where.md#choose-a-compute-target) 것으로 건너뛸 수 있습니다.

### <a name="preview-no-code-model-deployment"></a>모드 코드 없는 모델 배포

기존 배포 경로 대신 TensorFlow에 대 한 비 코드 배포 기능 (미리 보기)을 사용할 수도 있습니다. 위에 표시 된 대로, 및 매개 변수를 사용 하 여 모델을 등록 하면 `model_framework` `model_framework_version` `resource_configuration` 단순히 정적 함수를 사용 하 여 모델을 배포할 수 있습니다 `deploy()` .

```python
service = Model.deploy(ws, "tensorflow-web-service", [model])
```

Azure Machine Learning의 배포에 [대 한 자세한 내용을](how-to-deploy-and-where.md) 자세히 설명 합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 TensorFlow 모델을 학습 하 고 등록 했으며 배포 옵션에 대해 알아보았습니다. Azure Machine Learning에 대해 자세히 알아보려면 다음 문서를 참조 하세요.

* [학습 중에 실행 메트릭 추적](how-to-track-experiments.md)
* [하이퍼 매개 변수 조정](how-to-tune-hyperparameters.md)
* [Azure의 분산 심층 학습 교육에 대 한 참조 아키텍처](/azure/architecture/reference-architectures/ai/training-deep-learning)
