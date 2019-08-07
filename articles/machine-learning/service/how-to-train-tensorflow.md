---
title: TensorFlow 모델 학습 및 등록
titleSuffix: Azure Machine Learning service
description: 이 문서에서는 Azure Machine Learning 서비스를 사용 하 여 TensorFlow 모델을 학습 하 고 등록 하는 방법을 보여 줍니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.date: 06/10/2019
ms.custom: seodec18
ms.openlocfilehash: 1f6aaa4f1b8f58f7cd6c1f02f424614d33863fc5
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/06/2019
ms.locfileid: "68815881"
---
# <a name="train-and-register-tensorflow-models-at-scale-with-azure-machine-learning-service"></a>Azure Machine Learning 서비스를 사용 하 여 대규모로 TensorFlow 모델 학습 및 등록

이 문서에서는 Azure Machine Learning 서비스를 사용 하 여 TensorFlow 모델을 학습 하 고 등록 하는 방법을 보여 줍니다. 인기 있는 [Mnist 데이터 집합](http://yann.lecun.com/exdb/mnist/) 을 사용 하 여 [TensorFlow Python 라이브러리](https://www.tensorflow.org/overview)를 사용 하 여 빌드된 심층 신경망을 사용 하 여 필기 한 숫자를 분류 합니다.

TensorFlow는 DNN (심층 신경망)를 만드는 데 일반적으로 사용 되는 오픈 소스 계산 프레임 워크입니다. Azure Machine Learning 서비스를 사용 하면 탄력적 클라우드 계산 리소스를 사용 하 여 오픈 소스 학습 작업을 신속 하 게 확장할 수 있습니다. 또한 학습 실행과 버전 모델을 추적 하 고 모델을 배포할 수 있습니다.

TensorFlow 모델을 처음부터 개발 하 든, [기존 모델](how-to-deploy-existing-model.md) 을 클라우드로 전환 하 든 Azure Machine Learning 서비스를 통해 프로덕션이 준비 된 모델을 빌드할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

이러한 환경 중 하나에서이 코드를 실행 합니다.

 - Azure Machine Learning 노트북 VM-다운로드 또는 설치 필요 없음

     - 이 자습서를 시작하기 전에 [자습서: SDK 및 샘플 리포지토리](tutorial-1st-experiment-sdk-setup.md) 를 사용 하 여 미리 로드 된 전용 노트북 서버를 만들기 위한 환경 및 작업 영역을 설정 합니다.
    - 노트북 서버의 samples 폴더에서 다음 디렉터리로 이동 하 여 완료 되 고 확장 된 노트북을 찾습니다. **사용 방법-azureml > 교육-심층 학습 > 학습-hyperparameter 변수-tensorflow** 폴더. 
 
 - 사용자 고유의 Jupyter Notebook 서버

     - [Python 용 Azure Machine Learning SDK 설치](setup-create-workspace.md#sdk)
    - [작업 영역 구성 파일 만들기](setup-create-workspace.md#write-a-configuration-file)
    - [샘플 스크립트 파일 다운로드](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow) `mnist-tf.py` 및`utils.py`
     
    GitHub 샘플 페이지에서이 가이드의 전체 [Jupyter Notebook 버전](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow/train-hyperparameter-tune-deploy-with-tensorflow.ipynb) 을 찾을 수도 있습니다. 노트북에는 지능형 하이퍼 매개 변수 튜닝, 모델 배포 및 노트북 위젯을 다루는 확장 된 섹션이 포함 되어 있습니다.

## <a name="set-up-the-experiment"></a>실험 설정

이 섹션에서는 필요한 Python 패키지를 로드 하 고, 작업 영역을 초기화 하 고, 실험을 만들고, 학습 데이터 및 학습 스크립트를 업로드 하 여 학습 실험을 설정 합니다.

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

[Azure Machine Learning 서비스 작업 영역은](concept-workspace.md) 서비스에 대 한 최상위 리소스입니다. 사용자가 만드는 모든 아티팩트를 사용할 수 있는 중앙 집중식 환경을 제공 합니다. Python SDK에서 개체를 [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) 만들어 작업 영역 아티팩트에 액세스할 수 있습니다.

`config.json` [전제 조건 섹션](#prerequisites)에서 만든 파일에서 작업 영역 개체를 만듭니다.

```Python
ws = Workspace.from_config()
```

### <a name="create-an-experiment"></a>실험 만들기

학습 스크립트를 보관할 실험 및 폴더를 만듭니다. 이 예제에서는 "tf-mnist" 라는 실험을 만듭니다.

```Python
script_folder = './tf-mnist'
os.makedirs(script_folder, exist_ok=True)

exp = Experiment(workspace=ws, name='tf-mnist')
```

### <a name="upload-dataset-and-scripts"></a>데이터 집합 및 스크립트 업로드

데이터 [저장소](how-to-access-data.md) 는 데이터를 계산 대상으로 탑재 하거나 복사 하 여 데이터를 저장 하 고 액세스할 수 있는 장소입니다. 각 작업 영역은 기본 데이터 저장소를 제공 합니다. 데이터 및 학습 스크립트를 데이터 저장소에 업로드 하 여 학습 중에 쉽게 액세스할 수 있도록 합니다.

1. MNIST 데이터 집합을 로컬로 다운로드 합니다.

    ```Python
    os.makedirs('./data/mnist', exist_ok=True)

    urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz', filename = './data/mnist/train-images.gz')
    urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz', filename = './data/mnist/train-labels.gz')
    urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz', filename = './data/mnist/test-images.gz')
    urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz', filename = './data/mnist/test-labels.gz')
    ```

1. MNIST 데이터 집합을 기본 데이터 저장소에 업로드 합니다.

    ```Python
    ds = ws.get_default_datastore()
    ds.upload(src_dir='./data/mnist', target_path='mnist', overwrite=True, show_progress=True)
    ```

1. TensorFlow 교육 스크립트 `tf_mnist.py`, 및 도우미 파일 ( `utils.py`)을 업로드 합니다.

    ```Python
    shutil.copy('./tf_mnist.py', script_folder)
    shutil.copy('./utils.py', script_folder)
    ```

## <a name="create-a-compute-target"></a>컴퓨팅 대상 만들기

TensorFlow 작업을 실행할 계산 대상을 만듭니다. 이 예제에서는 GPU 사용 Azure Machine Learning 계산 클러스터를 만듭니다.

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

계산 대상에 대 한 자세한 내용은 [계산 대상 이란?](concept-compute-target.md) 문서를 참조 하세요.

## <a name="create-a-tensorflow-estimator"></a>TensorFlow 평가기 만들기

[TensorFlow 평가기](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) 는 계산 대상에서 TensorFlow 학습 작업을 시작 하는 간단한 방법을 제공 합니다.

TensorFlow 평가기는 프레임 워크를 지 원하는 [`estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) 데 사용할 수 있는 제네릭 클래스를 통해 구현 됩니다. 제네릭 평가기을 사용한 학습 모델에 대 한 자세한 내용은 [평가기를 사용 하 여 Azure Machine Learning를](how-to-train-ml-models.md) 사용 하 여 모델 학습을 참조 하세요.

학습 스크립트에 추가 pip 또는 conda 패키지를 실행 해야 하는 경우 및 `pip_packages` `conda_packages` 인수를 통해 해당 이름을 전달 하 여 결과 Docker 이미지에 패키지를 설치할 수 있습니다.

```Python
script_params = {
    '--data-folder': ws.get_default_datastore().as_mount(),
    '--batch-size': 50,
    '--first-layer-neurons': 300,
    '--second-layer-neurons': 100,
    '--learning-rate': 0.01
}

est = TensorFlow(source_directory=script_folder,
                 entry_script='tf_mnist.py',
                 script_params=script_params,
                 compute_target=compute_target,
                 use_gpu=True)
```

## <a name="submit-a-run"></a>실행 제출

[실행 개체](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) 는 작업이 실행 되는 동안 그리고 작업이 완료 된 후 실행 기록에 인터페이스를 제공 합니다.

```Python
run = exp.submit(est)
run.wait_for_completion(show_output=True)
```

실행이 실행 되 면 다음 단계를 거칩니다.

- **준비 중**: Docker 이미지는 TensorFlow 평가기에 따라 만들어집니다. 이미지는 작업 영역 컨테이너 레지스트리로 업로드 되 고 나중에 실행할 수 있도록 캐시 됩니다. 로그는 실행 기록에도 스트리밍되 고 진행률을 모니터링 하기 위해 볼 수 있습니다.

- **크기 조정**: Batch AI 클러스터가 현재 사용할 수 있는 것 보다 더 많은 노드를 실행 하는 데 필요한 경우 클러스터를 확장 하려고 시도 합니다.

- **Running**: 스크립트 폴더의 모든 스크립트는 계산 대상으로 업로드 되 고 데이터 저장소는 탑재 되거나 복사 되며 entry_script가 실행 됩니다. Stdout의 출력과./clogs 폴더는 실행 기록으로 스트리밍되 며 실행을 모니터링 하는 데 사용할 수 있습니다.

- **후 처리 중**: 실행의./출력 폴더가 실행 기록에 복사 됩니다.

## <a name="register-or-download-a-model"></a>모델 등록 또는 다운로드

모델을 학습 한 후에는 작업 영역에 등록할 수 있습니다. 모델 등록을 사용 하면 모델 [관리 및 배포](concept-model-management-and-deployment.md)를 간소화 하기 위해 작업 영역에 모델을 저장 하 고 버전을 지정할 수 있습니다.

```Python
model = run.register_model(model_name='tf-dnn-mnist', model_path='outputs/model')
```

Run 개체를 사용 하 여 모델의 로컬 복사본을 다운로드할 수도 있습니다. 학습 스크립트 `mnist-tf.py`에서 TensorFlow 보호기 개체는 로컬 폴더 (계산 대상의 로컬)에 모델을 유지 합니다. Run 개체를 사용 하 여 복사본을 다운로드할 수 있습니다.

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

또한 [`TensorFlow`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) 평가기는 CPU 및 GPU 클러스터에서 분산 된 학습을 지원 합니다. Distributed TensorFlow 작업을 쉽게 실행할 수 있고 Azure Machine Learning 서비스에서 오케스트레이션을 관리할 수 있습니다.

Azure Machine Learning 서비스는 TensorFlow에서 두 가지 분산 된 교육 방법을 지원 합니다.

- [Horovod](https://github.com/uber/horovod) framework를 사용 하는 [MPI 기반](https://www.open-mpi.org/) 분산 교육
- Parameter server 메서드를 사용 하는 네이티브 [Distributed TensorFlow](https://www.tensorflow.org/deploy/distributed)

### <a name="horovod"></a>Horovod

[Horovod](https://github.com/uber/horovod) 는 uber에서 개발한 분산 교육을 위한 오픈 소스 프레임 워크입니다. Distributed GPU TensorFlow 작업에 대 한 쉬운 경로를 제공 합니다.

Horovod를 사용 하려면 TensorFlow 생성자 [`MpiConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py) 에서 `distributed_training` 매개 변수에 대 한 개체를 지정 합니다. 이 매개 변수는 학습 스크립트에서 사용할 Horovod 라이브러리가 설치 되어 있는지 확인 합니다.

```Python
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
                      use_gpu=True)
```

### <a name="parameter-server"></a>매개 변수 서버

매개 변수 서버 모델을 사용하는 [기본 분산 TensorFlow](https://www.tensorflow.org/deploy/distributed)를 실행할 수도 있습니다. 이 방법에서는 매개 변수 서버 및 작업자 클러스터 전반에 걸쳐 학습을 실행합니다. 작업자는 학습 중에 그라데이션을 계산하고 매개 변수 서버는 그라데이션을 집계합니다.

매개 변수 서버 메서드를 사용 하려면 TensorFlow 생성자 [`TensorflowConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.tensorflowconfiguration?view=azure-ml-py) 에서 `distributed_training` 매개 변수에 대 한 개체를 지정 합니다.

```Python
from azureml.train.dnn import TensorFlow

distributed_training = TensorflowConfiguration()
distributed_training.worker_count = 2

# Tensorflow constructor
estimator= TensorFlow(source_directory=project_folder,
                      compute_target=compute_target,
                      script_params=script_params,
                      entry_script='script.py',
                      node_count=2,
                      process_count_per_node=1,
                      distributed_training=distributed_training,
                      use_gpu=True)

# submit the TensorFlow job
run = exp.submit(tf_est)
```

#### <a name="define-cluster-specifications-in-tf_config"></a>' TF_CONFIG '에서 클러스터 사양을 정의 합니다.

또한에 대 한 [`tf.train.ClusterSpec`](https://www.tensorflow.org/api_docs/python/tf/train/ClusterSpec)클러스터의 네트워크 주소와 포트가 필요 하므로 Azure Machine Learning `TF_CONFIG` 환경 변수를 설정 합니다.

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

TensorFlow의 상위 수준 [`tf.estimator`](https://www.tensorflow.org/api_docs/python/tf/estimator) API의 경우 TensorFlow은 `TF_CONFIG` 변수를 구문 분석 하 고 클러스터 사양을 작성 합니다.

TensorFlow의 낮은 수준의 핵심 api를 학습 하려면 변수를 `TF_CONFIG` 구문 분석 하 고 교육 코드에서를 `tf.train.ClusterSpec` 작성 합니다.

```Python
import os, json
import tensorflow as tf

tf_config = os.environ.get('TF_CONFIG')
if not tf_config or tf_config == "":
    raise ValueError("TF_CONFIG not found.")
tf_config_json = json.loads(tf_config)
cluster_spec = tf.train.ClusterSpec(cluster)

```

## <a name="next-steps"></a>다음 단계

이 문서에서는 TensorFlow 모델을 학습 하 고 등록 했습니다. GPU 사용 클러스터에 모델을 배포 하는 방법에 대 한 자세한 내용은 GPU 모델 배포 문서를 참조 하세요.

[Gpu를 사용 하 여 추론를 배포 하는 방법](how-to-deploy-inferencing-gpus.md)[Tensorboard를 사용 하 여 모니터링 하는 방법](how-to-monitor-tensorboard.md) 

