---
title: 딥 러닝 Keras 모델 학습
titleSuffix: Azure Machine Learning
description: Azure Machine Learning을 사용하여 TensorFlow에서 실행되는 Keras 심층 신경망 분류 모델을 학습시키고 등록하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: minxia
author: mx-iao
ms.reviewer: peterlu
ms.date: 09/28/2020
ms.topic: how-to
ms.openlocfilehash: 5a641f349b070f3b4cb285b3d0007baa54219683
ms.sourcegitcommit: 5ce88326f2b02fda54dad05df94cf0b440da284b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107884429"
---
# <a name="train-keras-models-at-scale-with-azure-machine-learning"></a>Azure Machine Learning을 사용하여 대규모 Keras 모델 학습

이 문서에서는 Azure Machine Learning을 사용하여 Keras 학습 스크립트를 실행하는 방법을 알아봅니다.

이 문서의 예제 코드는 Azure Machine Learning에서 TensorFlow 백 엔드를 사용하여 빌드된 Keras 분류 모델을 학습시키고 등록하는 방법을 보여줍니다. 널리 사용되는 [MNIST 데이터 세트](http://yann.lecun.com/exdb/mnist/)와 함께 [TensorFlow](https://www.tensorflow.org/overview)에서 실행되는 [Keras Python 라이브러리](https://keras.io)를 사용하여 빌드된 DNN(심층 신경망)으로 필기 숫자를 분류합니다.

Keras는 다른 널리 사용되는 DNN 프레임워크에서 실행할 수 있어 개발을 단순화해주는 상위 수준의 신경망 API입니다. Azure Machine Learning에서 탄력적 클라우드 컴퓨팅 리소스를 사용하여 학습 작업을 신속하게 스케일 아웃할 수 있습니다. 또한 학습 실행, 버전 모델, 모델 배포 등을 추적할 수 있습니다.

Azure Machine Learning에서는 Keras 모델을 처음부터 개발하든 또는 기존 모델을 클라우드로 가져오든 관계 없이 프로덕션 환경에 준비된 모델을 빌드할 수 있습니다.

> [!NOTE]
> 독립 실행형 Keras 패키지가 아닌 TensorFlow에 기본 제공되는 Keras API **tf.keras** 를 사용하는 경우 [TensorFlow 모델 학습](how-to-train-tensorflow.md)을 대신 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

다음 환경 중 하나에서 이 코드를 실행합니다.

- Azure Machine Learning 컴퓨팅 인스턴스 - 다운로드 또는 설치 필요 없음

     - 이 자습서를 시작하기 전에 [자습서: SDK 및 샘플 리포지토리로 미리 로드된 전용 Notebook 서버를 만들기 위한 환경 및 작업 영역](tutorial-1st-experiment-sdk-setup.md)을 설정합니다.
    - Notebook 서버의 샘플 폴더에서 **how-to-use-azureml > ml-frameworks > keras > train-hyperparameter-tune-deploy-with-keras** 폴더로 이동하여 완성된 확장 Notebook을 찾습니다.

 - 사용자 고유의 Jupyter Notebook 서버

    - [Azure Machine Learning SDK(1.15.0 이상)를 설치](/python/api/overview/azure/ml/install)합니다.
    - [작업 영역 구성 파일을 만듭니다](how-to-configure-environment.md#workspace).
    - [샘플 스크립트 파일(`keras_mnist.py` 및 `utils.py`)을 다운로드](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/keras/train-hyperparameter-tune-deploy-with-keras)합니다.

    GitHub 샘플 페이지에서 이 가이드의 완료된 [Jupyter Notebook 버전](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/keras/train-hyperparameter-tune-deploy-with-keras/train-hyperparameter-tune-deploy-with-keras.ipynb)을 찾을 수도 있습니다. Notebook에는 지능형 하이퍼 매개 변수 튜닝, 모델 배포 및 Notebook 위젯을 다루는 확장 섹션이 포함되어 있습니다.

## <a name="set-up-the-experiment"></a>실험 설정

이 섹션에서는 필요한 Python 패키지를 로드하고, 작업 영역을 초기화하고, 입력 학습 데이터에 대한 FileDataset를 만들고, 컴퓨팅 대상을 만들고, 학습 환경을 정의하여 학습 실험을 설정합니다.

### <a name="import-packages"></a>패키지 가져오기

먼저 필요한 Python 라이브러리를 가져옵니다.

```Python
import os
import azureml
from azureml.core import Experiment
from azureml.core import Environment
from azureml.core import Workspace, Run
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException
```

### <a name="initialize-a-workspace"></a>작업 영역 초기화

[Azure Machine Learning 작업 영역](concept-workspace.md)은 서비스의 최상위 리소스입니다. 사용자가 만드는 모든 아티팩트를 사용할 수 있는 중앙 집중식 환경을 제공합니다. Python SDK에서 [`workspace`](/python/api/azureml-core/azureml.core.workspace.workspace) 개체를 만들어 작업 영역 아티팩트에 액세스할 수 있습니다.

[사전 요구 사항 섹션](#prerequisites)에서 만든 `config.json` 파일에서 작업 영역 개체를 만듭니다.

```Python
ws = Workspace.from_config()
```

### <a name="create-a-file-dataset"></a>파일 데이터 세트 만들기

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

`register()` 메서드를 사용하여 작업 영역에 데이터 세트를 등록할 수 있습니다. 이렇게 하면 데이터 세트를 다른 사용자와 공유하고, 다양한 실험에서 재사용하고, 학습 스크립트에서 이름으로 참조할 수 있습니다.

```python
dataset = dataset.register(workspace=ws,
                           name='mnist-dataset',
                           description='training and test dataset',
                           create_new_version=True)
```

### <a name="create-a-compute-target"></a>컴퓨팅 대상 만들기

학습 작업을 실행할 컴퓨팅 대상을 만듭니다. 이 예제에서는 GPU 지원 Azure Machine Learning 컴퓨팅 클러스터를 만듭니다.

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

컴퓨팅 대상에 대한 자세한 내용은 [컴퓨팅 대상이란?](concept-compute-target.md) 문서를 참조하세요.

### <a name="define-your-environment"></a>환경 정의

학습 스크립트의 종속성을 캡슐화하는 Azure ML [환경](concept-environments.md)을 정의합니다.

먼저 YAML 파일에 conda 종속성을 정의합니다. 다음 예제에서 파일 이름은 `conda_dependencies.yml`입니다.

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

이 conda 환경 사양에서 Azure ML 환경을 만듭니다. 환경은 런타임에 Docker 컨테이너로 패키지화됩니다.

기본 이미지가 지정되지 않은 경우에는 Azure ML은 기본적으로 CPU 이미지 `azureml.core.environment.DEFAULT_CPU_IMAGE`를 기본 이미지로 사용합니다. 다음 예제에서는 GPU 클러스터에서 학습을 실행하므로 필요한 GPU 드라이버 및 종속성이 있는 GPU 기본 이미지를 지정해야 합니다. Azure ML은 사용할 수 있는 MCR(Microsoft Container Registry)에 게시된 기본 이미지 세트를 유지 관리합니다. 자세한 내용은 [Azure/AzureML-Containers](https://github.com/Azure/AzureML-Containers) GitHub 리포지토리를 참조하세요.

```python
keras_env = Environment.from_conda_specification(name='keras-env', file_path='conda_dependencies.yml')

# Specify a GPU base image
keras_env.docker.enabled = True
keras_env.docker.base_image = 'mcr.microsoft.com/azureml/openmpi3.1.2-cuda10.0-cudnn7-ubuntu18.04'
```

환경을 만들고 사용하는 방법에 대한 자세한 내용은 [Azure Machine Learning에서 소프트웨어 환경 만들기 및 사용](how-to-use-environments.md)을 참조하세요.

## <a name="configure-and-submit-your-training-run"></a>학습 실행 구성 및 제출

### <a name="create-a-scriptrunconfig"></a>ScriptRunConfig 만들기
먼저 `Dataset` 클래스를 사용하여 작업 영역 데이터 저장소에서 데이터를 가져옵니다.

```python
dataset = Dataset.get_by_name(ws, 'mnist-dataset')

# list the files referenced by mnist-dataset
dataset.to_path()
```

ScriptRunConfig 개체를 만들어 학습 스크립트, 사용할 환경 및 실행할 컴퓨팅 대상 등 학습 작업의 구성 세부 정보를 지정합니다.

`arguments` 매개 변수에 지정되면 학습 스크립트에 대한 모든 인수가 명령줄을 통해 전달됩니다. FileDataset의 DatasetConsumptionConfig는 학습 스크립트에 대한 `--data-folder` 인수로 전달됩니다. Azure ML은 이 DatasetConsumptionConfig를 지원 데이터 저장소의 탑재 지점으로 확인하며, 그런 다음, 학습 스크립트에서 이를 액세스할 수 있습니다.

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

ScriptRunConfig를 사용하여 작업을 구성하는 방법에 대한 자세한 내용은 [학습 실행 구성 및 제출](how-to-set-up-training-targets.md)을 참조하세요.

> [!WARNING]
> 이전에 TensorFlow 예측 도구를 사용하여 Keras 학습 작업을 구성한 경우, 이 예측 도구는 1.19.0 SDK 릴리스부터 더 이상 사용되지 않습니다. Azure ML SDK 1.15.0 이상을 사용하는 경우 ScriptRunConfig는 딥 러닝 프레임워크를 사용하는 작업을 포함하여 학습 작업을 구성하는 데 권장되는 방법입니다. 마이그레이션에 대한 일반적인 질문은 [예측 도구에서 ScriptRunConfig로 마이그레이션 가이드](how-to-migrate-from-estimators-to-scriptrunconfig.md)를 참조하세요.

### <a name="submit-your-run"></a>실행 제출

[Run 개체](/python/api/azureml-core/azureml.core.run%28class%29)는 작업이 실행되는 동안 그리고 작업이 완료된 후 실행 기록에 대한 인터페이스를 제공합니다.

```Python
run = Experiment(workspace=ws, name='Tutorial-Keras-Minst').submit(src)
run.wait_for_completion(show_output=True)
```

### <a name="what-happens-during-run-execution"></a>Run을 실행하는 중에 수행되는 작업
Run이 실행되면 다음 단계를 거칩니다.

- **준비**: 정의된 환경에 따라 Docker 이미지가 생성됩니다. 이미지는 작업 영역의 컨테이너 레지스트리에 업로드되고 나중에 실행될 수 있도록 캐시됩니다. 또한 로그는 실행 기록으로 스트리밍되며 진행 상황을 모니터링할 수 있도록 표시됩니다. 큐레이팅된 환경이 대신 지정되면 해당 큐레이팅된 환경을 지원하는 캐시된 이미지가 사용됩니다.

- **스케일링**: Batch AI 클러스터에서 Run을 실행하는 데 현재 사용할 수 있는 노드보다 더 많은 노드가 필요한 경우 클러스터에서 스케일 업을 시도합니다.

- **실행**: 스크립트 폴더의 모든 스크립트가 컴퓨팅 대상으로 업로드되고, 데이터 저장소가 탑재되거나 복사되며, `script`가 실행됩니다. stdout 및 **./logs** 폴더의 출력은 실행 기록으로 스트리밍되며 Run을 모니터링하는 데 사용될 수 있습니다.

- **후 처리**: Run의 **./outputs** 폴더가 실행 기록에 복사됩니다.

## <a name="register-the-model"></a>모델 등록

모델이 학습되었으면 해당 모델을 작업 영역에 등록할 수 있습니다. 모델 등록을 사용하면 모델을 작업 영역에 저장하고 버전을 지정하여 [모델 관리 및 배포](concept-model-management-and-deployment.md)를 간소화할 수 있습니다.

```Python
model = run.register_model(model_name='keras-mnist', model_path='outputs/model')
```

> [!TIP]
> 배포 방법에는 모델 등록에 대한 섹션이 포함되어 있지만 이미 등록된 모델이 있으므로 배포에 대한 [컴퓨팅 대상 만들기](how-to-deploy-and-where.md#choose-a-compute-target)로 직접 건너뛸 수 있습니다.

모델의 로컬 사본을 다운로드할 수도 있습니다. 이는 로컬에서 추가 모델 유효성 검사 작업을 수행하는 데 유용할 수 있습니다. 학습 스크립트 `keras_mnist.py`에서 TensorFlow 저장기 개체는 모델을 로컬 폴더(컴퓨팅 대상의 로컬 폴더)에 유지합니다. Run 개체를 사용하여 실행 기록에서 복사본을 다운로드할 수 있습니다.

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

이 문서에서는 Azure Machine Learning에서 Keras 모델을 학습시키고 등록했습니다. 모델을 배포하는 방법을 알아보려면 모델 배포 문서로 계속 진행하세요.

* [모델을 배포하는 방법 및 위치](how-to-deploy-and-where.md)
* [학습 중에 실행 메트릭 추적](how-to-log-view-metrics.md)
* [하이퍼 매개 변수 조정](how-to-tune-hyperparameters.md)
* [학습된 모델 배포](how-to-deploy-and-where.md)
* [Azure에서 분산 딥 러닝 학습의 참조 아키텍처](/azure/architecture/reference-architectures/ai/training-deep-learning)
