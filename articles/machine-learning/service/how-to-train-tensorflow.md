---
title: TensorFlow & Keras를 사용 하 여 모델 학습
titleSuffix: Azure Machine Learning service
description: Keras와 TensorFlow 추정을 사용 하 여 단일 노드 및 분산된 Keras와 TensorFlow 모델 학습을 실행 하는 방법 알아보기
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: minxia
author: mx-iao
ms.reviewer: sgilley
ms.date: 05/06/2019
ms.custom: seodec18
ms.openlocfilehash: 82c9aa961221b582bb16438f30a0584232164393
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2019
ms.locfileid: "65915106"
---
# <a name="train-tensorflow-and-keras-models-with-azure-machine-learning-service"></a>Azure Machine Learning 서비스를 사용 하 여 Keras와 TensorFlow 모델 학습

Azure 계산에서 TensorFlow 교육 작업을 쉽게 실행할 수 있습니다 사용 하 여 합니다 [ `TensorFlow` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) Azure Machine Learning SDK에 스 티 메이 터 클래스입니다. `TensorFlow` 스 티 메이 터 심층 신경망 네트워크 DNN () 교육에 대 한 TensorFlow 기반 컨테이너에서 작업을 실행 하는 Azure Machine Learning 서비스를 전달 합니다.

`TensorFlow` 스 티 메이 터 학습 스크립트를 변경 하지 않고 다른 계산 대상에 대해 매개 변수가 있는 실행을 쉽게 구성할 수 있습니다 즉 실행에 비해 추상화 계층도 제공 합니다.

## <a name="get-started"></a>시작하기

하므로 `TensorFlow` 스 티 메이 터 클래스는 기본 비슷합니다 [ `Estimator` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py), 좋습니다, 첫 번째 읽기를 [기본 스 티 메이 터 방법 문서](how-to-train-ml-models.md) 가장 중요 한 개념을 이해 합니다.

Azure Machine Learning 서비스를 시작 하기 [빠른 시작을 완료](quickstart-run-cloud-notebook.md)합니다. 완료 되 면 해야는 [Azure Machine Learning 작업 영역](concept-workspace.md) 및 모든 우리의 [노트북 샘플](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml) Keras와 TensorFlow를 사용 하 여 학습 Dnn를 포함 하 여.

## <a name="single-node-training"></a>단일 노드 학습

TensorFlow 작업을 실행 하려면 인스턴스화한 후에 [ `TensorFlow` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) 개체 및 실험으로 제출 합니다.

다음 코드를 TensorFlow 스 티 메이 터 인스턴스화하고 시험 삼아 제출 합니다. 학습 스크립트 `train.py` 지정 된 스크립트 매개 변수를 사용 하 여 실행 됩니다. GPU 사용 하도록 작업을 실행할 [계산 대상](how-to-set-up-training-targets.md), 및 scikit-는 알아봅니다에 대 한 종속성으로 설치 `train.py`합니다.

```Python
from azureml.train.dnn import TensorFlow

# training script parameters passed as command-line arguments
script_params = {
    '--batch-size': 50,
    '--learning-rate': 0.01,
}

# TensorFlow constructor
tf_est = TensorFlow(source_directory='./my-tf-proj',
                    script_params=script_params,
                    compute_target=compute_target,
                    entry_script='train.py', # relative path to your TensorFlow job
                    conda_packages=['scikit-learn'],
                    use_gpu=True)

# submit the TensorFlow job
run = exp.submit(tf_est)
```

## <a name="distributed-training"></a>분산 학습

합니다 [ `TensorFlow` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) 스 티 메이 터 CPU 및 GPU 클러스터 간에 분산된 교육을도 지원 합니다. 분산된 TensorFlow 작업을 쉽게 실행할 수 있습니다 Azure Machine Learning 서비스는 및 및 관리 인프라를 오케스트레이션 합니다.

Azure Machine Learning 서비스에서 TensorFlow 분산된 교육의 두 메서드를 지원합니다.

* [MPI 기반](https://www.open-mpi.org/) 를 사용 하 여 학습을 분산 합니다 [Horovod](https://github.com/uber/horovod) 프레임 워크
* 네이티브 [분산 TensorFlow](https://www.tensorflow.org/deploy/distributed) 매개 변수 서버 메서드를 사용 하 여

### <a name="horovod"></a>Horovod

[Horovod](https://github.com/uber/horovod) 분산된 교육 Uber 개발한는 오픈 소스 프레임 워크입니다. 분산된 TensorFlow GPU 작업에 편리한 경로 제공합니다.

다음 샘플 Horovod를 사용 하 여 두 노드 간에 분산 하는 두 명의 작업자를 사용 하 여 분산된 교육 작업을 실행 합니다.

```Python
from azureml.train.dnn import TensorFlow

# Tensorflow constructor
tf_est = TensorFlow(source_directory='./my-tf-proj',
                    script_params={},
                    compute_target=compute_target,
                    entry_script='train.py', # relative path to your TensorFlow job
                    node_count=2,
                    process_count_per_node=1,
                    distributed_backend='mpi', # specifies Horovod backend
                    use_gpu=True)

# submit the TensorFlow job
run = exp.submit(tf_est)
```

학습 스크립트에서 가져올 수 있도록 Horovod 및 해당 종속성으로 설치 됩니다.

```Python
import tensorflow as tf
import horovod
```

### <a name="parameter-server"></a>매개 변수 서버

매개 변수 서버 모델을 사용하는 [기본 분산 TensorFlow](https://www.tensorflow.org/deploy/distributed)를 실행할 수도 있습니다. 이 방법에서는 매개 변수 서버 및 작업자 클러스터 전반에 걸쳐 학습을 실행합니다. 작업자는 학습 중에 그라데이션을 계산하고 매개 변수 서버는 그라데이션을 집계합니다.

다음 샘플 매개 변수 서버에서는 사용 하 여 두 노드 간에 분산 하는 4 명의 작업자를 사용 하 여 분산된 교육 작업을 실행 합니다.

```Python
from azureml.train.dnn import TensorFlow

# Tensorflow constructor
tf_est = TensorFlow(source_directory='./my-tf-proj',
                    script_params={},
                    compute_target=compute_target,
                    entry_script='train.py', # relative path to your TensorFlow job
                    node_count=2,
                    worker_count=2,
                    parameter_server_count=1,
                    distributed_backend='ps', # specifies parameter server backend
                    use_gpu=True)

# submit the TensorFlow job
run = exp.submit(tf_est)
```

#### <a name="note-on-tfconfig"></a>`TF_CONFIG` 관련 참고 사항

네트워크 주소 및 포트에 대 한 클러스터도 필요 합니다 [ `tf.train.ClusterSpec` ](https://www.tensorflow.org/api_docs/python/tf/train/ClusterSpec)이므로 설정 하는 Azure Machine Learning을 `TF_CONFIG` 환경 변수를 합니다.

`TF_CONFIG` 환경 변수는 JSON 문자열입니다. 매개 변수 서버의 변수 예제는 다음과 같습니다.

```
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

교육에 대 한 TensorFlow의 하위 수준 core Api에 대 한 구문 분석 합니다 `TF_CONFIG` 변수와 빌드는 `tf.train.ClusterSpec` 교육 코드에서. [이 예제](https://aka.ms/aml-notebook-tf-ps)에서는 다음과 같이 **학습 스크립트**에서 해당 작업을 수행합니다.

```Python
import os, json
import tensorflow as tf

tf_config = os.environ.get('TF_CONFIG')
if not tf_config or tf_config == "":
    raise ValueError("TF_CONFIG not found.")
tf_config_json = json.loads(tf_config)
cluster_spec = tf.train.ClusterSpec(cluster)

```

## <a name="keras-support"></a>Keras 지원

[Keras](https://keras.io/) 는 널리 사용 되는 높은 수준의 DNN Python API입니다 TensorFlow, CNTK, Theano 백 엔드를 지원 합니다. TensorFlow를 백 엔드로 사용 하는 경우 추가 Keras 간단 하 게 포함 하는 `pip_package` 생성자 매개 변수입니다.

다음 샘플 인스턴스화하는 [ `TensorFlow` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) 스 티 메이 터 시험 삼아 제출 합니다. Keras 학습 스크립트를 실행 하 여 평가기 `keras_train.py`합니다. 작업 gpu 가능에서 실행될지 [계산 대상](how-to-set-up-training-targets.md) Keras pip 통해 종속성으로 설치를 사용 하 여 합니다.

```Python
from azureml.train.dnn import TensorFlow

keras_est = TensorFlow(source_directory='./my-keras-proj',
                       script_params=script_params,
                       compute_target=compute_target,
                       entry_script='keras_train.py', # relative path to your TensorFlow job
                       pip_packages=['keras'], # add keras through pip
                       use_gpu=True)
```

## <a name="export-to-onnx"></a>ONNX 내보내려면

사용 하 여 최적화 된 추론을 가져오려고 합니다 [ONNX 런타임](concept-onnx.md), TensorFlow 모델 학습된을 ONNX 형식으로 변환할 수 있습니다. [예제](https://github.com/onnx/tensorflow-onnx/blob/master/examples/call_coverter_via_python.py)를 참조하세요.

## <a name="examples"></a>예

다양 한 프레임 워크를 사용 하 여 단일 노드 및 분산 TensorFlow 실행에 대 한 작업 코드 샘플을 찾을 수 있습니다 [GitHub 페이지](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning)합니다.

## <a name="next-steps"></a>다음 단계

* [학습 중에 실행 메트릭 추적](how-to-track-experiments.md)
* [하이퍼 매개 변수 조정](how-to-tune-hyperparameters.md)
* [학습된 모델 배포](how-to-deploy-and-where.md)
