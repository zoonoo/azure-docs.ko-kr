---
title: 딥 러닝 케라스 모델 교육
titleSuffix: Azure Machine Learning
description: Azure 기계 학습을 사용하여 TensorFlow에서 실행되는 Keras 심층 신경망 분류 모델을 학습하고 등록하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.reviewer: peterlu
ms.date: 08/01/2019
ms.custom: seodec18
ms.openlocfilehash: ba7976d602412037578d0a324916718b2d515aac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79269966"
---
# <a name="train-and-register-a-keras-classification-model-with-azure-machine-learning"></a>Azure 기계 학습을 통해 Keras 분류 모델 교육 및 등록
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

이 문서에서는 Azure 기계 학습을 사용하여 TensorFlow에 빌드된 Keras 분류 모델을 학습하고 등록하는 방법을 보여 주었습니다. 인기 있는 [MNIST 데이터 집합을](http://yann.lecun.com/exdb/mnist/) 사용하여 [TensorFlow](https://www.tensorflow.org/overview)위에서 실행되는 [Keras Python 라이브러리를](https://keras.io) 사용하여 빌드된 DNN(심층 신경망)을 사용하여 필기 숫자를 분류합니다.

Keras는 개발을 단순화하기 위해 인기 있는 다른 DNN 프레임워크의 최상위를 실행할 수 있는 고급 신경망 API입니다. Azure 기계 학습을 사용하면 탄력적 클라우드 계산 리소스를 사용하여 교육 작업을 빠르게 확장할 수 있습니다. 또한 교육 실행, 버전 모델, 배포 모델 등을 추적할 수 있습니다.

처음부터 Keras 모델을 개발하든 기존 모델을 클라우드로 가져오든 Azure Machine Learning을 통해 프로덕션 지원 모델을 빌드하는 데 도움이 됩니다.

기계 학습과 딥 러닝의 차이점에 대한 자세한 내용은 [개념 문서를](concept-deep-learning-vs-machine-learning.md) 참조하십시오.

## <a name="prerequisites"></a>사전 요구 사항

다음 환경 중 하나에서 이 코드를 실행합니다.

- Azure 기계 학습 계산 인스턴스 - 다운로드 또는 설치 필요 없음

     - 자습서 [완료: 설정 환경 및 작업 영역을](tutorial-1st-experiment-sdk-setup.md) 설정하여 SDK 및 샘플 리포지토리로 미리 로드된 전용 노트북 서버를 만듭니다.
    - 노트북 서버의 샘플 폴더에서 이 디렉토리를 탐색하여 완료되고 확장된 전자 필기장을 찾을 수 있습니다 **> >.**

 - 사용자 고유의 Jupyter Notebook 서버

    - [Azure 기계 학습 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)를 설치합니다.
    - [작업 영역 구성 파일 만들기](how-to-configure-environment.md#workspace).
    - [샘플 스크립트 파일을](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras) `mnist-keras.py` 다운로드하고`utils.py`

    GitHub 샘플 페이지에서 이 가이드의 완성된 [Jupyter 노트북 버전을](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras/train-hyperparameter-tune-deploy-with-keras.ipynb) 찾을 수도 있습니다. 노트북에는 지능형 하이퍼매개 변수 튜닝, 모델 배포 및 노트북 위젯을 포함하는 확장된 섹션이 포함되어 있습니다.

## <a name="set-up-the-experiment"></a>실험 설정

이 섹션에서는 필요한 파이썬 패키지를 로드하고, 작업 영역을 초기화하고, 실험을 만들고, 학습 데이터 및 학습 스크립트를 업로드하여 학습 실험을 설정합니다.

### <a name="import-packages"></a>패키지 가져오기

먼저 필요한 파이썬 라이브러리를 가져옵니다.

```Python
import os
import azureml
from azureml.core import Experiment
from azureml.core import Workspace, Run
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException
```

### <a name="initialize-a-workspace"></a>작업 영역 초기화

[Azure 기계 학습 작업 영역은](concept-workspace.md) 서비스의 최상위 리소스입니다. 그것은 당신이 만드는 모든 아티팩트와 함께 작업 할 수있는 중앙 집중식 장소를 제공합니다. Python SDK에서 개체를 만들어 작업 영역 아티팩트에 [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) 액세스할 수 있습니다.

[필수 구성 조건 섹션에서](#prerequisites)만든 `config.json` 파일에서 작업 영역 개체를 만듭니다.

```Python
ws = Workspace.from_config()
```

### <a name="create-an-experiment"></a>실험 만들기

작업 영역에서 "keras-mnist"라는 실험을 만듭니다.

```Python
exp = Experiment(workspace=ws, name='keras-mnist')
```

<a name="data-upload"></a>
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

이 `register()` 방법을 사용하여 데이터 집합을 작업 영역에 등록하여 다른 사용자와 공유하고, 다양한 실험에서 재사용하고, 학습 스크립트의 이름으로 참조할 수 있습니다.

```python
dataset = dataset.register(workspace=ws,
                           name='mnist dataset',
                           description='training and test dataset',
                           create_new_version=True)
```

## <a name="create-a-compute-target"></a>컴퓨팅 대상 만들기

실행할 TensorFlow 작업에 대한 계산 대상을 만듭니다. 이 예제에서는 GPU 지원 Azure 기계 학습 계산 클러스터를 만듭니다.

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

## <a name="create-a-tensorflow-estimator-and-import-keras"></a>텐서플로우 추정기 작성 및 케라스 가져오기

[TensorFlow 추정기는](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) 계산 대상에서 TensorFlow 교육 작업을 시작하는 간단한 방법을 제공합니다. Keras는 텐서플로우 위에서 실행되므로 텐서플로우 추정기를 사용하고 `pip_packages` 인수를 사용하여 Keras 라이브러리를 가져올 수 있습니다.

먼저 `Dataset` 클래스를 사용하여 작업 영역 데이터 스토어에서 데이터를 가져옵니다.

```python
dataset = Dataset.get_by_name(ws, 'mnist dataset')

# list the files referenced by mnist dataset
dataset.to_path()
```

TensorFlow 추정기는 모든 프레임 [`estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) 워크를 지원 하기 위해 사용할 수 있는 제네릭 클래스를 통해 구현 됩니다. 또한 DNN `script_params` 하이퍼 매개 변수 설정을 포함 하는 사전을 만듭니다. 일반 추정기를 사용하는 학습 모델에 대한 자세한 내용은 [추정기를 사용하여 Azure 기계 학습을 사용하는 학습 모델을](how-to-train-ml-models.md) 참조하십시오.

```python
from azureml.train.dnn import TensorFlow

script_params = {
    '--data-folder': dataset.as_named_input('mnist').as_mount(),
    '--batch-size': 50,
    '--first-layer-neurons': 300,
    '--second-layer-neurons': 100,
    '--learning-rate': 0.001
}

est = TensorFlow(source_directory=script_folder,
                 entry_script='keras_mnist.py',
                 script_params=script_params,
                 compute_target=compute_target,
                 pip_packages=['keras', 'matplotlib'],
                 use_gpu=True)
```

## <a name="submit-a-run"></a>달리기 제출

[Run 개체는](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) 작업이 실행되는 동안 및 작업이 완료된 후 실행 기록에 대한 인터페이스를 제공합니다.

```Python
run = exp.submit(est)
run.wait_for_completion(show_output=True)
```

실행이 실행되면 다음 단계를 거칩니다.

- **준비**: 텐서플로우 추정기에 따라 도커 이미지가 만들어집니다. 이미지가 작업 영역의 컨테이너 레지스트리에 업로드되고 나중에 실행될 수 있도록 캐시됩니다. 로그는 실행 기록으로 스트리밍되며 진행 상황을 모니터링하기 위해 볼 수 있습니다.

- **크기 조정**: Batch AI 클러스터에 현재 사용 가능한 것보다 실행을 실행하는 데 더 많은 노드가 필요한 경우 클러스터가 확장하려고 시도합니다.

- **실행**중 : 스크립트 폴더의 모든 스크립트가 계산 대상에 업로드되고 데이터 저장소가 탑재되거나 복사되고 entry_script 실행됩니다. stdout 및 ./logs 폴더의 출력은 실행 기록으로 스트리밍되며 실행을 모니터링하는 데 사용할 수 있습니다.

- **사후 처리**: 실행의 ./outputs 폴더가 실행 기록에 복사됩니다.

## <a name="register-the-model"></a>모델 등록

DNN 모델을 학습한 후에는 작업 영역에 등록할 수 있습니다. 모델 등록을 사용하면 모델 관리 및 배포를 간소화하기 위해 작업 영역에 모델을 [저장하고](concept-model-management-and-deployment.md)버전이 정번할 수 있습니다.

```Python
model = run.register_model(model_name='keras-dnn-mnist', model_path='outputs/model')
```

> [!TIP]
> 방금 등록한 모델은 교육에 사용한 추정기에 관계없이 Azure Machine Learning의 다른 등록된 모델과 동일한 방식으로 배포됩니다. 배포 방법은 모델 등록에 대한 섹션을 포함하지만 이미 등록된 모델이 있으므로 배포에 대한 [계산 대상을 만드는](how-to-deploy-and-where.md#choose-a-compute-target) 데 직접 건너뛸 수 있습니다.

모델의 로컬 복사본을 다운로드할 수도 있습니다. 이 기능은 추가 모델 유효성 검사 작업을 로컬에서 수행하는 데 유용할 수 있습니다. 학습 스크립트에서 `mnist-keras.py`TensorFlow 보호기 개체는 모델을 로컬 폴더(계산 대상에 로컬)로 유지합니다. 실행 개체를 사용하여 데이터스토어에서 복사본을 다운로드할 수 있습니다.

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

이 문서에서는 Azure 기계 학습에서 Keras 모델을 학습하고 등록했습니다. 모델을 배포하는 방법을 알아보려면 모델 배포 문서를 계속 계속 설명합니다.

> [!div class="nextstepaction"]
> [모델을 배포하는 방법 및 위치](how-to-deploy-and-where.md)
* [학습 중에 실행 메트릭 추적](how-to-track-experiments.md)
* [하이퍼 매개 변수 조정](how-to-tune-hyperparameters.md)
* [학습된 모델 배포](how-to-deploy-and-where.md)
* [Azure의 분산 딥 러닝 교육을 위한 참조 아키텍처](/azure/architecture/reference-architectures/ai/training-deep-learning)
