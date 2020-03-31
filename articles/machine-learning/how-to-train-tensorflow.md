---
title: 텐서플로우 모델 교육 및 배포
titleSuffix: Azure Machine Learning
description: Azure 기계 학습을 사용하여 TensorFlow 학습 스크립트를 대규모로 실행하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.date: 08/20/2019
ms.custom: seodec18
ms.openlocfilehash: 2bbd81f3858aa78b9e0e2d610c0fdb0a67816c8e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78228310"
---
# <a name="build-a-tensorflow-deep-learning-model-at-scale-with-azure-machine-learning"></a>Azure 기계 학습을 통해 대규모로 TensorFlow 딥 러닝 모델 구축
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

이 문서에서는 Azure 기계 학습의 [TensorFlow 추정클래스를](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) 사용하여 [TensorFlow](https://www.tensorflow.org/overview) 교육 스크립트를 대규모로 실행하는 방법을 보여 줍니다. 이 예제에서는 DNN(심층 신경망)을 사용하여 손으로 쓴 숫자를 분류하기 위해 TensorFlow 모델을 훈련하고 등록합니다.

처음부터 TensorFlow 모델을 개발하든 [기존 모델을](how-to-deploy-existing-model.md) 클라우드로 가져오든 Azure Machine Learning을 사용하여 오픈 소스 교육 작업을 확장하여 프로덕션 급 모델을 빌드, 배포, 버전 및 모니터링할 수 있습니다.

[딥 러닝과 머신 러닝에](concept-deep-learning-vs-machine-learning.md)대해 자세히 알아보십시오.

## <a name="prerequisites"></a>사전 요구 사항

다음 환경 중 하나에서 이 코드를 실행합니다.

 - Azure 기계 학습 계산 인스턴스 - 다운로드 또는 설치 필요 없음

     - 자습서 [완료: 설정 환경 및 작업 영역을](tutorial-1st-experiment-sdk-setup.md) 설정하여 SDK 및 샘플 리포지토리로 미리 로드된 전용 노트북 서버를 만듭니다.
    - 노트북 서버의 샘플 딥 러닝 폴더에서 이 디렉토리로 이동하여 완료되고 확장된 노트북을 찾습니다: **텐서플로우 > 배포 > 텐서플로우 > 구축-하이퍼매개 변수 조정-텐서플로우** 폴더를 > 사용 방법 azureml > ml 프레임워크입니다. 
 
 - 사용자 고유의 Jupyter Notebook 서버

    - [Azure 기계 학습 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)를 설치합니다.
    - [작업 영역 구성 파일 만들기](how-to-configure-environment.md#workspace).
    - [샘플 스크립트 파일을](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/tensorflow/deployment/train-hyperparameter-tune-deploy-with-tensorflow) `mnist-tf.py` 다운로드하고`utils.py`
     
    GitHub 샘플 페이지에서 이 가이드의 완성된 [Jupyter 노트북 버전을](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/tensorflow/deployment/train-hyperparameter-tune-deploy-with-tensorflow/train-hyperparameter-tune-deploy-with-tensorflow.ipynb) 찾을 수도 있습니다. 노트북에는 지능형 하이퍼매개 변수 튜닝, 모델 배포 및 노트북 위젯을 포함하는 확장된 섹션이 포함되어 있습니다.

## <a name="set-up-the-experiment"></a>실험 설정

이 섹션에서는 필요한 Python 패키지를 로드하고, 작업 영역을 초기화하고, 실험을 만들고, 학습 데이터 및 학습 스크립트를 업로드하여 학습 실험을 설정합니다.

### <a name="import-packages"></a>패키지 가져오기

먼저 필요한 파이썬 라이브러리를 가져옵니다.

```Python
import os
import urllib
import shutil
import azureml

from azureml.core import Experiment
from azureml.core import Workspace, Run

from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException
from azureml.train.dnn import TensorFlow
```

### <a name="initialize-a-workspace"></a>작업 영역 초기화

[Azure 기계 학습 작업 영역은](concept-workspace.md) 서비스의 최상위 리소스입니다. 그것은 당신이 만드는 모든 아티팩트와 함께 작업 할 수있는 중앙 집중식 장소를 제공합니다. Python SDK에서 개체를 만들어 작업 영역 아티팩트에 [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) 액세스할 수 있습니다.

[필수 구성 조건 섹션에서](#prerequisites)만든 `config.json` 파일에서 작업 영역 개체를 만듭니다.

```Python
ws = Workspace.from_config()
```

### <a name="create-a-deep-learning-experiment"></a>딥 러닝 실험 만들기

학습 스크립트를 보관할 실험 및 폴더를 만듭니다. 이 예제에서는 "tf-mnist"라는 실험을 만듭니다.

```Python
script_folder = './tf-mnist'
os.makedirs(script_folder, exist_ok=True)

exp = Experiment(workspace=ws, name='tf-mnist')
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

이 `register()` 방법을 사용하여 데이터 집합을 작업 영역에 등록하여 다른 사용자와 공유하고, 다양한 실험에서 재사용하고, 학습 스크립트의 이름으로 참조할 수 있습니다.

```python
dataset = dataset.register(workspace=ws,
                           name='mnist dataset',
                           description='training and test dataset',
                           create_new_version=True)

# list the files referenced by dataset
dataset.to_path()
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

## <a name="create-a-tensorflow-estimator"></a>텐서플로우 추정기 작성

[TensorFlow 추정기는](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) 계산 대상에서 TensorFlow 교육 작업을 시작하는 간단한 방법을 제공합니다.

TensorFlow 추정기는 모든 프레임 [`estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) 워크를 지원 하기 위해 사용할 수 있는 제네릭 클래스를 통해 구현 됩니다. 일반 추정기를 사용하는 학습 모델에 대한 자세한 내용은 [추정기를 사용하여 Azure 기계 학습을 사용하는 학습 모델을](how-to-train-ml-models.md) 참조하십시오.

교육 스크립트에서 추가 pip 또는 conda 패키지를 실행해야 하는 경우 `pip_packages` 해당 `conda_packages` 이름과 인수를 통해 이름을 전달하여 결과 Docker 이미지에 패키지를 설치할 수 있습니다.

```python
script_params = {
    '--data-folder': dataset.as_named_input('mnist').as_mount(),
    '--batch-size': 50,
    '--first-layer-neurons': 300,
    '--second-layer-neurons': 100,
    '--learning-rate': 0.01
}

est = TensorFlow(source_directory=script_folder,
                 entry_script='tf_mnist.py',
                 script_params=script_params,
                 compute_target=compute_target,
                 use_gpu=True,
                 pip_packages=['azureml-dataprep[pandas,fuse]'])
```

> [!TIP]
> **텐서플로우 2.0에** 대한 지원이 텐서플로우 추정기 클래스에 추가되었습니다. 자세한 내용은 [블로그 게시물](https://azure.microsoft.com/blog/tensorflow-2-0-on-azure-fine-tuning-bert-for-question-tagging/)을 참조하세요.

Python 환경 사용자 지정에 대한 자세한 내용은 [교육 및 배포를 위한 환경 만들기 및 관리를](how-to-use-environments.md)참조하십시오. 

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

## <a name="register-or-download-a-model"></a>모델 등록 또는 다운로드

모델을 학습한 후에는 작업 영역에 등록할 수 있습니다. 모델 등록을 사용하면 모델 관리 및 배포를 간소화하기 위해 작업 영역에 모델을 [저장하고](concept-model-management-and-deployment.md)버전이 정번할 수 있습니다. 매개 변수를 `model_framework`지정하면 `model_framework_version`및 `resource_configuration`, 코드 없음 모델 배포를 사용할 수 있게 됩니다. 이렇게 하면 등록된 모델에서 웹 서비스로 모델을 직접 배포할 수 있으며 개체는 `ResourceConfiguration` 웹 서비스에 대한 계산 리소스를 정의합니다.

```Python
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

model = run.register_model(model_name='tf-dnn-mnist', 
                           model_path='outputs/model',
                           model_framework=Model.Framework.TENSORFLOW,
                           model_framework_version='1.13.0',
                           resource_configuration=ResourceConfiguration(cpu=1, memory_in_gb=0.5))
```

Run 개체를 사용하여 모델의 로컬 복사본을 다운로드할 수도 있습니다. 학습 스크립트에서 `mnist-tf.py`TensorFlow 보호기 개체는 모델을 로컬 폴더(계산 대상에 로컬)로 유지합니다. 실행 개체를 사용하여 복사본을 다운로드할 수 있습니다.

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

또한 [`TensorFlow`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) 이 추정기는 CPU 및 GPU 클러스터 전반에 걸쳐 분산 교육을 지원합니다. 분산 된 TensorFlow 작업을 쉽게 실행할 수 있으며 Azure 기계 학습은 오케스트레이션을 관리합니다.

Azure Machine Learning은 TensorFlow에서 두 가지 분산 학습 방법을 지원합니다.

- [호로로드](https://github.com/uber/horovod) 프레임워크를 사용한 [MPI 기반](https://www.open-mpi.org/) 분산 교육
- 매개 변수 서버 메서드를 사용하여 네이티브 [분산 텐서플로우](https://www.tensorflow.org/deploy/distributed)

### <a name="horovod"></a>Horovod

[Horovod는](https://github.com/uber/horovod) Uber가 개발한 분산 교육을 위한 오픈 소스 프레임워크입니다. 분산 GPU TensorFlow 작업에 대한 쉬운 경로를 제공합니다.

Horovod를 사용하려면 [`MpiConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py) TensorFlow `distributed_training` 생성자에서 매개 변수에 대한 개체를 지정합니다. 이 매개 변수는 교육 스크립트에서 사용할 수 있도록 Horovod 라이브러리가 설치되도록 합니다.

```Python
from azureml.core.runconfig import MpiConfiguration
from azureml.train.dnn import TensorFlow

# Tensorflow constructor
estimator= TensorFlow(source_directory=project_folder,
                      compute_target=compute_target,
                      script_params=script_params,
                      entry_script='script.py',
                      node_count=2,
                      process_count_per_node=1,
                      distributed_training=MpiConfiguration(),
                      framework_version='1.13',
                      use_gpu=True,
                      pip_packages=['azureml-dataprep[pandas,fuse]'])
```

### <a name="parameter-server"></a>매개 변수 서버

매개 변수 서버 모델을 사용하는 [기본 분산 TensorFlow](https://www.tensorflow.org/deploy/distributed)를 실행할 수도 있습니다. 이 방법에서는 매개 변수 서버 및 작업자 클러스터 전반에 걸쳐 학습을 실행합니다. 작업자는 학습 중에 그라데이션을 계산하고 매개 변수 서버는 그라데이션을 집계합니다.

매개 변수 서버 메서드를 [`TensorflowConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.tensorflowconfiguration?view=azure-ml-py) 사용 하려면 `distributed_training` TensorFlow 생성자에서 매개 변수에 대 한 개체를 지정 합니다.

```Python
from azureml.train.dnn import TensorFlow

distributed_training = TensorflowConfiguration()
distributed_training.worker_count = 2

# Tensorflow constructor
tf_est= TensorFlow(source_directory=project_folder,
                      compute_target=compute_target,
                      script_params=script_params,
                      entry_script='script.py',
                      node_count=2,
                      process_count_per_node=1,
                      distributed_training=distributed_training,
                      use_gpu=True,
                      pip_packages=['azureml-dataprep[pandas,fuse]'])

# submit the TensorFlow job
run = exp.submit(tf_est)
```

#### <a name="define-cluster-specifications-in-tf_config"></a>'TF_CONFIG'에서 클러스터 사양 정의

또한 에 대한 클러스터의 네트워크 주소와 [`tf.train.ClusterSpec`](https://www.tensorflow.org/api_docs/python/tf/train/ClusterSpec)포트가 필요하므로 Azure `TF_CONFIG` Machine Learning은 환경 변수를 설정합니다.

`TF_CONFIG` 환경 변수는 JSON 문자열입니다. 매개 변수 서버의 변수 예제는 다음과 같습니다.

```JSON
TF_CONFIG='{
    "cluster": {
        "ps": ["host0:2222", "host1:2222"],
        "worker": ["host2:2222", "host3:2222", "host4:2222"],
    },
    "task": {"type": "ps", "index": 0},
    "environment": "cloud"
}'
```

TensorFlow의 높은 수준의 [`tf.estimator`](https://www.tensorflow.org/api_docs/python/tf/estimator) API의 경우 TensorFlow는 변수를 `TF_CONFIG` 구문 분석하고 클러스터 사양을 작성합니다.

교육을 위한 TensorFlow의 하위 수준 코어 API의 경우 `TF_CONFIG` 변수를 `tf.train.ClusterSpec` 구문 분석하고 교육 코드에서 빌드합니다.

```Python
import os, json
import tensorflow as tf

tf_config = os.environ.get('TF_CONFIG')
if not tf_config or tf_config == "":
    raise ValueError("TF_CONFIG not found.")
tf_config_json = json.loads(tf_config)
cluster_spec = tf.train.ClusterSpec(cluster)

```

## <a name="deploy-a-tensorflow-model"></a>텐서플로우 모델 배포

방금 등록한 모델은 교육에 사용한 추정기에 관계없이 Azure Machine Learning의 다른 등록된 모델과 동일한 방식으로 배포할 수 있습니다. 배포 방법은 모델 등록에 대한 섹션을 포함하지만 이미 등록된 모델이 있으므로 배포에 대한 [계산 대상을 만드는](how-to-deploy-and-where.md#choose-a-compute-target) 데 직접 건너뛸 수 있습니다.

## <a name="preview-no-code-model-deployment"></a>(미리 보기) 코드 없음 모델 배포

기존 배포 경로 대신 Tensorflow에 대해 코드 없음 배포 기능(미리 보기)을 사용할 수도 있습니다. 위에 표시된 대로 `model_framework`의 를 의 `model_framework_version`. `resource_configuration` 및 매개 변수에 `deploy()` 등록하면 정적 함수를 사용하여 모델을 배포할 수 있습니다.

```python
service = Model.deploy(ws, "tensorflow-web-service", [model])
```

Azure 기계 학습의 배포에 대한 자세한 [방법을](how-to-deploy-and-where.md) 자세히 설명합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 TensorFlow 모델을 학습및 등록하고 배포 옵션에 대해 배웠습니다. Azure 기계 학습에 대해 자세히 알아보려면 다음 다른 문서를 참조하세요.

* [학습 중에 실행 메트릭 추적](how-to-track-experiments.md)
* [하이퍼 매개 변수 조정](how-to-tune-hyperparameters.md)
* [Azure의 분산 딥 러닝 교육을 위한 참조 아키텍처](/azure/architecture/reference-architectures/ai/training-deep-learning)
