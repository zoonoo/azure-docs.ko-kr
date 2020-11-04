---
title: 심층 학습 Keras 모델 학습
titleSuffix: Azure Machine Learning
description: Azure Machine Learning를 사용 하 여 TensorFlow에서 실행 되는 심층 신경망 분류 모델로 Keras를 학습 하 고 등록 하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: minxia
author: mx-iao
ms.reviewer: peterlu
ms.date: 09/28/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: c6b65c97fd87d4e3ed84c837d2702395091097fa
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93308060"
---
# <a name="train-keras-models-at-scale-with-azure-machine-learning"></a>Azure Machine Learning를 통해 대규모로 Keras 모델 학습

이 문서에서는 Azure Machine Learning를 사용 하 여 Keras 학습 스크립트를 실행 하는 방법에 대해 알아봅니다.

이 문서의 예제 코드는 Azure Machine Learning를 사용 하 여 TensorFlow 백 엔드를 사용 하 여 빌드된 Keras 분류 모델을 학습 하 고 등록 하는 방법을 보여 줍니다. 인기 있는 [Mnist 데이터 집합](http://yann.lecun.com/exdb/mnist/) 을 사용 하 여 [TensorFlow](https://www.tensorflow.org/overview)에서 실행 되는 [KERAS Python 라이브러리로](https://keras.io) 빌드된 DNN (심층 신경망)를 사용 하 여 필기 된 숫자를 분류 합니다.

Keras는 기타 인기 있는 DNN 프레임 워크를 실행 하 여 개발을 간소화할 수 있는 높은 수준의 신경망 API입니다. Azure Machine Learning를 사용 하면 탄력적 클라우드 계산 리소스를 사용 하 여 학습 작업을 신속 하 게 확장할 수 있습니다. 또한 학습 실행과 버전 모델을 추적 하 고 모델을 배포할 수 있습니다.

처음부터 Keras 모델을 개발 하 든, 기존 모델을 클라우드로 전환 하는 경우에는 프로덕션에 사용할 수 있는 모델을 빌드하는 데 도움이 Azure Machine Learning.

> [!NOTE]
> [TensorFlow 모델을 학습](how-to-train-tensorflow.md)하는 대신 TensorFlow에 기본 제공 되는 KERAS API **tf** 를 사용 하는 경우를 참조 하세요.

## <a name="prerequisites"></a>필수 구성 요소

이러한 환경 중 하나에서이 코드를 실행 합니다.

- Azure Machine Learning 컴퓨팅 인스턴스 - 다운로드 또는 설치 필요 없음

     - 이 자습서를 시작하기 전에 [자습서: SDK 및 샘플 리포지토리로 미리 로드된 전용 Notebook 서버를 만들기 위한 환경 및 작업 영역](tutorial-1st-experiment-sdk-setup.md)을 설정합니다.
    - 노트북 서버의 samples 폴더에서 다음 디렉터리로 이동 하 여 완료 되 고 확장 된 노트북을 찾습니다. **사용 방법-azureml > ml 프레임 워크 > keras > 학습-하이퍼 매개 변수-튜닝-배포-keras** 폴더로 이동 합니다.

 - 사용자 고유의 Jupyter Notebook 서버

    - [AZURE MACHINE LEARNING SDK](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py) (>= 1.15.0)를 설치 합니다.
    - [작업 영역 구성 파일을 만듭니다](how-to-configure-environment.md#workspace).
    - [샘플 스크립트 파일 다운로드](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/keras/train-hyperparameter-tune-deploy-with-keras) `keras_mnist.py` 하거나 `utils.py`

    GitHub 샘플 페이지에서이 가이드의 전체 [Jupyter Notebook 버전](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/keras/train-hyperparameter-tune-deploy-with-keras/train-hyperparameter-tune-deploy-with-keras.ipynb) 을 찾을 수도 있습니다. 노트북에는 지능형 하이퍼 매개 변수 튜닝, 모델 배포 및 노트북 위젯을 다루는 확장 된 섹션이 포함 되어 있습니다.

## <a name="set-up-the-experiment"></a>실험 설정

이 섹션에서는 필요한 Python 패키지를 로드 하 고, 작업 영역을 초기화 하 고, 입력 학습 데이터의 FileDataset을 만들고, 계산 대상을 만들고, 학습 환경을 정의 하는 방법으로 학습 실험을 설정 합니다.

### <a name="import-packages"></a>패키지 가져오기

먼저 필요한 Python 라이브러리를 가져옵니다.

```Python
import os
import azureml
from azureml.core import Experiment
from azureml.core import Workspace, Run
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException
```

### <a name="initialize-a-workspace"></a>작업 영역 초기화

[Azure Machine Learning 작업 영역은](concept-workspace.md) 서비스에 대 한 최상위 리소스입니다. 사용자가 만드는 모든 아티팩트를 사용할 수 있는 중앙 집중식 환경을 제공합니다. Python SDK에서 개체를 만들어 작업 영역 아티팩트에 액세스할 수 있습니다 [`workspace`](/python/api/azureml-core/azureml.core.workspace.workspace?preserve-view=true&view=azure-ml-py) .

`config.json` [전제 조건 섹션](#prerequisites)에서 만든 파일에서 작업 영역 개체를 만듭니다.

```Python
ws = Workspace.from_config()
```

### <a name="create-a-file-dataset"></a>파일 데이터 집합 만들기

`FileDataset` 개체는 작업 영역 데이터 저장소 또는 공용 URL에서 하나 이상의 파일을 참조합니다. 파일은 모든 형식일 수 있으며, 클래스는 컴퓨팅에 파일을 다운로드하거나 탑재하는 기능을 제공합니다. `FileDataset`를 생성하여 데이터 원본 위치에 대한 참조를 만듭니다. 데이터 세트에 변환을 적용한 경우 데이터 세트에도 저장됩니다. 데이터는 기존 위치에 그대로 남아 있으므로 추가 스토리지 비용이 발생하지 않습니다. 자세한 내용은 `Dataset` 패키지에 대한 [방법](./how-to-create-register-datasets.md) 가이드를 참조하세요.

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

메서드를 사용 하 여 `register()` 데이터 집합을 다른 사용자와 공유 하 고, 다양 한 실험에서 재사용 하 고, 학습 스크립트에서 이름으로 참조할 수 있도록 작업 영역에 데이터 집합을 등록할 수 있습니다.

```python
dataset = dataset.register(workspace=ws,
                           name='mnist-dataset',
                           description='training and test dataset',
                           create_new_version=True)
```

### <a name="create-a-compute-target"></a>컴퓨팅 대상 만들기

학습 작업을 실행할 계산 대상을 만듭니다. 이 예제에서는 GPU 사용 Azure Machine Learning 계산 클러스터를 만듭니다.

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

학습 스크립트의 종속성을 캡슐화 하는 Azure ML [환경을](concept-environments.md) 정의 합니다.

먼저, YAML 파일에서 conda 종속성을 정의 합니다. 이 예제에서 파일의 이름은 `conda_dependencies.yml` 입니다.

```yaml
channels:
- conda-forge
dependencies:
- python=3.6.2
- pip:
  - azureml-defaults
  - tensorflow-gpu==2.0.0
  - keras<=2.3.1
  - matplotlib
```

이 conda 환경 사양에서 Azure ML 환경을 만듭니다. 환경은 런타임에 Docker 컨테이너에 패키지 됩니다.

기본적으로 기본 이미지가 지정 되지 않은 경우 Azure ML은 CPU 이미지를 `azureml.core.environment.DEFAULT_CPU_IMAGE` 기본 이미지로 사용 합니다. 이 예제에서는 GPU 클러스터에서 학습을 실행 하므로 필요한 GPU 드라이버 및 종속성이 있는 GPU 기본 이미지를 지정 해야 합니다. Azure ML은 사용자가 사용할 수 있는 Microsoft Container Registry (MCR)에 게시 된 기본 이미지 집합을 유지 관리 합니다. 자세한 내용은 [azure/AzureML 컨테이너](https://github.com/Azure/AzureML-Containers) GitHub 리포지토리를 참조 하세요.

```python
from azureml.core import Environment

keras_env = Environment.from_conda_specification(name='keras-env', file_path='conda_dependencies.yml')

# Specify a GPU base image
keras_env.docker.enabled = True
keras_env.docker.base_image = 'mcr.microsoft.com/azureml/openmpi3.1.2-cuda10.0-cudnn7-ubuntu18.04'
```

환경을 만들고 사용 하는 방법에 대 한 자세한 내용은 [Azure Machine Learning에서 소프트웨어 환경 만들기 및 사용](how-to-use-environments.md)을 참조 하세요.

## <a name="configure-and-submit-your-training-run"></a>학습 실행 구성 및 제출

### <a name="create-a-scriptrunconfig"></a>ScriptRunConfig 만들기
먼저 클래스를 사용 하 여 작업 영역 데이터 저장소에서 데이터를 가져옵니다 `Dataset` .

```python
dataset = Dataset.get_by_name(ws, 'mnist-dataset')

# list the files referenced by mnist-dataset
dataset.to_path()
```

ScriptRunConfig 개체를 만들어 학습 스크립트, 사용할 환경 및 실행할 컴퓨팅 대상 등 학습 작업의 구성 세부 정보를 지정합니다.

매개 변수에 지정 된 경우 학습 스크립트에 대 한 모든 인수는 명령줄을 통해 전달 됩니다 `arguments` . FileDataset의 DatasetConsumptionConfig는 인수에 대해 학습 스크립트에 인수로 전달 됩니다 `--data-folder` . Azure ML은이 DatasetConsumptionConfig를 지원 데이터 저장소의 탑재 지점으로 확인 하 고이를 학습 스크립트에서 액세스할 수 있습니다.

```python
from azureml.core import ScriptRunConfig

args = ['--data-folder', dataset.as_mount(),
        '--batch-size', 50,
        '--first-layer-neurons', 300,
        '--second-layer-neurons', 100,
        '--learning-rate', 0.001]

src = ScriptRunConfig(source_directory=script_folder,
                      script='keras_mnist.py',
                      arguments=args,
                      compute_target=compute_target,
                      environment=keras_env)
```

ScriptRunConfig를 사용 하 여 작업을 구성 하는 방법에 대 한 자세한 내용은 [학습 실행 구성 및 제출](how-to-set-up-training-targets.md)을 참조 하세요.

> [!WARNING]
> 이전에 TensorFlow 평가기를 사용 하 여 Keras 학습 작업을 구성한 경우 Azure ML SDK의 이후 릴리스에서는 추정이 더 이상 사용 되지 않습니다. Azure ML SDK >= 1.15.0를 사용 하는 경우 ScriptRunConfig는 DL 프레임 워크를 사용 하 여 학습 작업을 구성 하는 데 권장 되는 방법입니다.

### <a name="submit-your-run"></a>실행 제출

[실행 개체](/python/api/azureml-core/azureml.core.run%28class%29?preserve-view=true&view=azure-ml-py) 는 작업이 실행 되는 동안 그리고 작업이 완료 된 후 실행 기록에 인터페이스를 제공 합니다.

```Python
run = Experiment(workspace=ws, name='keras-mnist').submit(src)
run.wait_for_completion(show_output=True)
```

### <a name="what-happens-during-run-execution"></a>실행 실행 중 수행 되는 작업
실행이 실행 되 면 다음 단계를 거칩니다.

- **준비** : 정의 된 환경에 따라 docker 이미지가 생성 됩니다. 이미지는 작업 영역 컨테이너 레지스트리로 업로드 되 고 나중에 실행할 수 있도록 캐시 됩니다. 로그는 실행 기록에도 스트리밍되 고 진행률을 모니터링 하기 위해 볼 수 있습니다. 큐 레이트 환경을 대신 지정 하면 해당 큐 레이트 환경을 지 원하는 캐시 된 이미지가 사용 됩니다.

- **크기 조정** : 클러스터는 현재 사용 가능한 것 보다 더 많은 노드를 실행 하는 Batch AI 클러스터가 필요한 경우 확장을 시도 합니다.

- **실행 중** : 스크립트 폴더의 모든 스크립트가 계산 대상으로 업로드 되 고, 데이터 저장소가 탑재 되거나 복사 되 고, `script` 이 실행 됩니다. Stdout의 출력과 **./clogs** 폴더는 실행 기록으로 스트리밍되 며 실행을 모니터링 하는 데 사용할 수 있습니다.

- **사후 처리** : 실행의 **./출력** 폴더가 실행 기록에 복사 됩니다.

## <a name="register-the-model"></a>모델 등록

모델을 학습 한 후에는 작업 영역에 등록할 수 있습니다. 모델 등록을 사용하면 모델을 작업 영역에 저장하고 버전을 지정하여 [모델 관리 및 배포](concept-model-management-and-deployment.md)를 간소화할 수 있습니다.

```Python
model = run.register_model(model_name='keras-mnist', model_path='outputs/model')
```

> [!TIP]
> 배포 방법에는 모델 등록에 대 한 섹션이 포함 되어 있지만 등록 된 모델이 이미 있기 때문에 배포에 대 한 [계산 대상을 직접 만드는](how-to-deploy-and-where.md#choose-a-compute-target) 것으로 건너뛸 수 있습니다.

모델의 로컬 복사본을 다운로드할 수도 있습니다. 이는 로컬에서 추가 모델 유효성 검사 작업을 수행 하는 데 유용할 수 있습니다. 학습 스크립트에서 `keras_mnist.py` TensorFlow 보호기 개체는 모델을 로컬 폴더 (계산 대상의 로컬 폴더)에 유지 합니다. 실행 개체를 사용 하 여 실행 기록에서 복사본을 다운로드할 수 있습니다.

```Python
# Create a model folder in the current directory
os.makedirs('./model', exist_ok=True)

for f in run.get_file_names():
    if f.startswith('outputs/model'):
        output_file_path = os.path.join('./model', f.split('/')[-1])
        print('Downloading from {} to {} ...'.format(f, output_file_path))
        run.download_file(name=f, output_file_path=output_file_path)
```

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure Machine Learning에서 Keras 모델을 학습 하 고 등록 했습니다. 모델을 배포 하는 방법에 대 한 자세한 내용은 모델 배포 문서를 참조 하세요.

* [모델을 배포 하는 방법 및 위치](how-to-deploy-and-where.md)
* [학습 중에 실행 메트릭 추적](how-to-track-experiments.md)
* [하이퍼 매개 변수 조정](how-to-tune-hyperparameters.md)
* [학습된 모델 배포](how-to-deploy-and-where.md)
* [Azure의 분산 심층 학습 교육에 대 한 참조 아키텍처](/azure/architecture/reference-architectures/ai/training-deep-learning)