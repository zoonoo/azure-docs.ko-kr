---
title: TensorFlow 모델 학습 및 등록
titleSuffix: Azure Machine Learning service
description: 이 문서에서는 학습 Azure Machine Learning 서비스를 사용 하 여 TensorFlow 모델을 등록 하는 방법을 보여 줍니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: minxia
author: mx-iao
ms.date: 05/28/2019
ms.custom: seodec18
ms.openlocfilehash: 4a6f9734a7b2b59035efcbb0f4e2d75f47e053be
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66515605"
---
# <a name="train-and-register-tensorflow-models-at-scale-with-azure-machine-learning-service"></a>학습 및 Azure Machine Learning 서비스를 사용 하 여 대규모로 TensorFlow 모델을 등록 합니다.

이 문서에서는 학습 Azure Machine Learning 서비스를 사용 하 여 TensorFlow 모델을 등록 하는 방법을 보여 줍니다. 사용 하면 인기 있는 [MNIST 데이터 집합](http://yann.lecun.com/exdb/mnist/) 심층 신경망을 사용 하 여 빌드를 사용 하 여 필기 숫자를 분류 하는 [TensorFlow Python 라이브러리](https://www.tensorflow.org/overview)합니다.

Azure Machine Learning 서비스를 사용 하 여 탄력적인 클라우드 계산 리소스를 사용 하 여 오픈 소스 학습 작업을 신속 하 게 확장할 수 있습니다. 또한 수 교육 실행, 버전 모델을 통해 추적, 모델 등을 배포 합니다.

부터 TensorFlow 모델을 개발 하 든 기존 모델을 클라우드로 가져오는 Azure Machine Learning 서비스를 사용 하 여 프로덕션이 준비 된 모델을 빌드할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

- 설치 합니다 [Azure Machine Learning Python for SDK](setup-create-workspace.md#sdk)합니다. 선택 사항: 만들기는 `config.json` 구성 파일입니다.
- 다운로드 합니다 [샘플 스크립트 파일](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow) `mnist-tf.py` 및 `utils.py`

완료 된 찾을 수도 있습니다 [Jupyter Notebook 버전](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow/train-hyperparameter-tune-deploy-with-tensorflow.ipynb) Github 샘플 페이지에서이 가이드의 합니다. Notebook에 지능형 하이퍼 매개 변수 튜닝을 다루는 확장된 섹션 및 모델 배포에 포함 됩니다.

## <a name="set-up-the-experiment"></a>실험을 설정

이 섹션에서는 필요한 python 패키지를 로드, 작업 영역 초기화, 실험을 만들고, 학습 데이터 및 Python SDK를 사용 하 여 학습 스크립트를 업로드 하 여 학습 실험을 설정 합니다.

### <a name="import-packages"></a>패키지 가져오기

먼저 필요한 Python 라이브러리를 가져와서 해야 합니다.

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

[Azure Machine Learning 서비스 작업 영역](concept-workspace.md) 서비스에 대 한 최상위 리소스입니다. 만든 모든 아티팩트를 작업할 수 있는 중앙된 위치를 사용 하 여 제공 합니다. Python SDK에서 작업 영역 아티팩트를 만들어 액세스할 수 있습니다는 [ `workspace` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) 개체입니다.

선택적 단계를 완료 한 경우는 [전제 조건 섹션](#prerequisites)를 사용할 수 있습니다 `Workspace.from_config()` 신속 하 게 개체를 만들려면 작업 영역에서 구성 파일에 저장 된 자세한 정보.

```Python
ws = Workspace.from_config()
```

또한 작업 영역을 명시적으로 만들 수 있습니다.

```Python
ws = Workspace.create(name='<workspace-name>',
                      subscription_id='<azure-subscription-id>',
                      resource_group='<choose-a-resource-group>',
                      create_resource_group=True,
                      location='<select-location>' # For example: 'eastus2'
                      )
```

### <a name="create-an-experiment"></a>실험 만들기

실험 및 학습 스크립트를 보관할 폴더를 만듭니다. 이 예제에서는 "tf-mnist"를 호출 하는 실험을 만듭니다.

```Python
script_folder = './tf-mnist'
os.makedirs(script_folder, exist_ok=True)

exp = Experiment(workspace=ws, name='tf-mnist')
```

### <a name="upload-dataset-and-scripts"></a>데이터 집합 및 스크립트 업로드

합니다 [데이터 저장소](how-to-access-data.md) 데이터 저장 고 탑재 하거나 계산 대상에 데이터를 복사 하 여 액세스할 수 있는 위치입니다. 각 작업 영역의 기본 데이터 저장소를 제공합니다. 학습 하는 동안 쉽게 액세스할 수 있도록에서는 데이터 및 학습 스크립트 업로드 됩니다.

1. MNIST 데이터 집합을 로컬로 다운로드 합니다.

    ```Python
    os.makedirs('./data/mnist', exist_ok=True)

    urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz', filename = './data/mnist/train-images.gz')
    urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz', filename = './data/mnist/train-labels.gz')
    urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz', filename = './data/mnist/test-images.gz')
    urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz', filename = './data/mnist/test-labels.gz')
    ```

1. 기본 데이터 저장소에는 MNIST 데이터 집합을 업로드 합니다.

    ```Python
    ds = ws.get_default_datastore()
    ds.upload(src_dir='./data/mnist', target_path='mnist', overwrite=True, show_progress=True)
    ```

1. TensorFlow 학습 스크립트를 업로드 `tf_mnist.py`, 및 도우미 파일 `utils.py`합니다.

    ```Python
    shutil.copy('./tf_mnist.py', script_folder)
    shutil.copy('./utils.py', script_folder)
    ```

## <a name="create-a-compute-target"></a>계산 대상 만들기

실행 하 여 TensorFlow 작업에 대 한 계산 대상을 만듭니다. 이 예제에서는 Azure Machine Learning GPU 가능 계산 클러스터를 만듭니다. 제공 되는 교육의 목록에 대 한 계산 대상 내용은 [이 문서](how-to-set-up-training-targets.md#compute-targets-for-training)

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

## <a name="create-a-tensorflow-estimator"></a>TensorFlow 예측 만들기

합니다 [TensorFlow 스 티 메이 터](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) 계산 대상에서 TensorFlow 교육 작업을 시작 하는 간단한 방법을 제공 합니다. TensorFlow 설치 된 docker 이미지를 만듭니다.

제네릭 통해 구현 됩니다. TensorFlow 평가기 [ `estimator` ](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) 모든 프레임 워크를 지원 하기 위해 사용할 수 있는 클래스입니다. 제네릭 평가기를 사용 하 여 모델을 학습 하는 방법에 대 한 자세한 내용은 참조 하세요. [평가기를 사용 하 여 Azure Machine Learning을 사용 하 여 모델을 학습 합니다.](how-to-train-ml-models.md)

통해 해당 이름을 전달 하 여 결과 docker 이미지에 설치 된 패키지 있습니다 학습 스크립트 추가 pip 또는 conda 패키지 실행에 필요한 경우는 `pip_packages` 고 `conda_packages` 인수입니다.

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

## <a name="submit-a-run"></a>실행을 제출 합니다.

합니다 [개체 실행](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) 작업이 실행 되 고 완료 된 후 실행된 기록에 인터페이스를 제공 합니다.

```Python
run = exp.submit(est)
run.wait_for_completion(show_output=True)
```

실행을 실행 하는 다음 단계를 거치게 됩니다.

- **준비**: Docker 이미지를 TensorFlow 스 티 메이 터에 따라 생성 됩니다. 이미지 작업 공간의 container registry에 업로드 이며 이후 실행에 대 한 캐시 됩니다. 로그는 실행된 기록에도 스트리밍되고 진행 상황을 모니터링 볼 수 있습니다.

- **크기 조정**: 클러스터는 Batch AI 클러스터 노드를 현재 사용할 수 있는 것 보다는 실행을 실행 해야 하는 경우 확장 하려고 합니다.

- **Running**: 스크립트 폴더에 있는 모든 스크립트가 계산 대상에 업로드 됩니다, 데이터 저장소는 탑재 하거나 복사할 수 고를 entry_script 실행 됩니다. Stdout에서 출력 및. / logs 폴더 실행된 기록에 스트리밍됩니다 및 실행을 모니터링 할 수 있습니다.

- **후 처리 중**: 합니다. / 출력 폴더 실행의 실행된 기록에 복사 됩니다.

## <a name="register-or-download-a-model"></a>등록 또는 모델을 다운로드 합니다.

모델을 학습 한 후 작업 영역에 등록할 수 있습니다. 모델 등록 하면 저장소와 버전 간소화 하기 위해 작업 영역에서 모델 [모델 관리 및 배포](concept-model-management-and-deployment.md)합니다.

```Python
model = run.register_model(model_name='tf-dnn-mnist', model_path='outputs/model')
```

또한 실행 개체를 사용 하 여 모델의 로컬 복사본을 다운로드할 수 있습니다. 학습 스크립트에서 `mnist-tf.py`, TensorFlow 보호기를 계산 대상 (로컬)를 로컬 폴더에 모델을 유지 합니다. 실행 개체 복사본을 다운로드 하려면 사용할 수 있습니다.

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

합니다 [ `TensorFlow` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) 스 티 메이 터 CPU 및 GPU 클러스터 간에 분산된 교육을도 지원 합니다. 분산된 TensorFlow 작업을 쉽게 실행할 수 있습니다 하 고 Azure Machine Learning 서비스를 오케스트레이션 관리 됩니다.

Azure Machine Learning 서비스에서 TensorFlow 분산된 교육의 두 메서드를 지원합니다.

- [MPI 기반](https://www.open-mpi.org/) 를 사용 하 여 학습을 분산 합니다 [Horovod](https://github.com/uber/horovod) 프레임 워크
- 네이티브 [분산 TensorFlow](https://www.tensorflow.org/deploy/distributed) 매개 변수 서버 메서드를 사용 하 여

### <a name="horovod"></a>Horovod

[Horovod](https://github.com/uber/horovod) 분산된 교육 Uber 개발한는 오픈 소스 프레임 워크입니다. 분산된 TensorFlow GPU 작업에 편리한 경로 제공합니다.

Horovod를 사용 하려면 지정 `mpi` 에 대 한는 `distributed_training` TensorFlow 스 티 메이 터 생성자에 매개 변수입니다. Horovod 학습 스크립트에서 사용 하 여에 대 한 설치 됩니다.

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

매개 변수 서버 메서드를 사용 하려면 지정 `ps` 에 대 한는 `distributed_training` TensorFlow 스 티 메이 터 생성자에 매개 변수입니다.

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
                      distributed_backend=distributed_training,
                      use_gpu=True)

# submit the TensorFlow job
run = exp.submit(tf_est)
```

#### <a name="define-cluster-specifications-in-tfconfig"></a>클러스터 사양에 대 한 'TF_CONFIG'를 정의 합니다.

네트워크 주소 및 포트에 대 한 클러스터도 필요 합니다 [ `tf.train.ClusterSpec` ](https://www.tensorflow.org/api_docs/python/tf/train/ClusterSpec)이므로 설정 하는 Azure Machine Learning을 `TF_CONFIG` 환경 변수를 합니다.

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

TensorFlow의 높은 수준의 [ `tf.estimator` ](https://www.tensorflow.org/api_docs/python/tf/estimator) API, TensorFlow는이 구문 분석할 `TF_CONFIG` 변수와 빌드 클러스터 수에 대 한 사양입니다.

교육에 대 한 TensorFlow의 하위 수준 core Api에 대 한 구문 분석 합니다 `TF_CONFIG` 변수와 빌드는 `tf.train.ClusterSpec` 교육 코드에서.

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

이 문서에서는 학습 하 고 Azure Machine Learning 서비스에서 TensorFlow 모델을 등록 합니다. 모델을 배포 하는 방법에 알아보려면 모델 배포 문서를 진행 합니다.

> [!div class="nextstepaction"]
> [방법 및 모델을 배포할 수 있는 위치](how-to-deploy-and-where.md)
