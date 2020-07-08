---
title: 심층 학습 Keras 모델 학습
titleSuffix: Azure Machine Learning
description: Azure Machine Learning를 사용 하 여 TensorFlow에서 실행 되는 심층 신경망 분류 모델로 Keras를 학습 하 고 등록 하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: maxluk
author: maxluk
ms.reviewer: peterlu
ms.date: 08/01/2019
ms.custom: seodec18
ms.openlocfilehash: 14649d3e7bc12205283863f725a902a3cef20290
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84433870"
---
# <a name="train-and-register-a-keras-classification-model-with-azure-machine-learning"></a>Azure Machine Learning를 사용 하 여 Keras 분류 모델 학습 및 등록
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

이 문서에서는 Azure Machine Learning를 사용 하 여 TensorFlow를 기반으로 하는 Keras 분류 모델을 학습 하 고 등록 하는 방법을 보여 줍니다. 인기 있는 [Mnist 데이터 집합](http://yann.lecun.com/exdb/mnist/) 을 사용 하 여 [TensorFlow](https://www.tensorflow.org/overview)에서 실행 되는 [KERAS Python 라이브러리로](https://keras.io) 빌드된 DNN (심층 신경망)를 사용 하 여 필기 된 숫자를 분류 합니다.

Keras는 기타 인기 있는 DNN 프레임 워크를 실행 하 여 개발을 간소화할 수 있는 높은 수준의 신경망 API입니다. Azure Machine Learning를 사용 하면 탄력적 클라우드 계산 리소스를 사용 하 여 학습 작업을 신속 하 게 확장할 수 있습니다. 또한 학습 실행과 버전 모델을 추적 하 고 모델을 배포할 수 있습니다.

처음부터 Keras 모델을 개발 하 든, 기존 모델을 클라우드로 전환 하는 경우에는 프로덕션에 사용할 수 있는 모델을 빌드하는 데 도움이 Azure Machine Learning.

기계 학습 및 심층 학습 간의 차이점에 대 한 자세한 내용은 [개념 문서](concept-deep-learning-vs-machine-learning.md) 를 참조 하세요.

## <a name="prerequisites"></a>사전 요구 사항

이러한 환경 중 하나에서이 코드를 실행 합니다.

- Azure Machine Learning 컴퓨팅 인스턴스 - 다운로드 또는 설치 필요 없음

     - 이 자습서를 시작하기 전에 [자습서: SDK 및 샘플 리포지토리로 미리 로드된 전용 Notebook 서버를 만들기 위한 환경 및 작업 영역](tutorial-1st-experiment-sdk-setup.md)을 설정합니다.
    - 노트북 서버의 samples 폴더에서 다음 디렉터리로 이동 하 여 완료 및 확장 된 노트북을 찾습니다. **사용 방법-azureml > 교육-심층 학습 > 학습-hyperparameter 변수-튜닝-배포-keras** 폴더로 이동 합니다.

 - 사용자 고유의 Jupyter Notebook 서버

    - [AZURE MACHINE LEARNING SDK를 설치](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)합니다.
    - [작업 영역 구성 파일을 만듭니다](how-to-configure-environment.md#workspace).
    - [샘플 스크립트 파일 다운로드](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras) `mnist-keras.py` 하거나`utils.py`

    GitHub 샘플 페이지에서이 가이드의 전체 [Jupyter Notebook 버전](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras/train-hyperparameter-tune-deploy-with-keras.ipynb) 을 찾을 수도 있습니다. 노트북에는 지능형 하이퍼 매개 변수 튜닝, 모델 배포 및 노트북 위젯을 다루는 확장 된 섹션이 포함 되어 있습니다.

## <a name="set-up-the-experiment"></a>실험 설정

이 섹션에서는 필요한 python 패키지를 로드 하 고, 작업 영역을 초기화 하 고, 실험을 만들고, 학습 데이터 및 학습 스크립트를 업로드 하 여 학습 실험을 설정 합니다.

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

[Azure Machine Learning 작업 영역은](concept-workspace.md) 서비스에 대 한 최상위 리소스입니다. 사용자가 만드는 모든 아티팩트를 사용할 수 있는 중앙 집중식 환경을 제공합니다. Python SDK에서 개체를 만들어 작업 영역 아티팩트에 액세스할 수 있습니다 [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) .

`config.json` [전제 조건 섹션](#prerequisites)에서 만든 파일에서 작업 영역 개체를 만듭니다.

```Python
ws = Workspace.from_config()
```

### <a name="create-an-experiment"></a>실험 만들기

작업 영역에서 "keras-mnist" 라는 실험을 만듭니다.

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

메서드를 사용 `register()` 하 여 데이터 집합을 다른 사용자와 공유 하 고, 다양 한 실험에서 재사용 하 고, 학습 스크립트에서 이름으로 참조할 수 있도록 작업 영역에 데이터 집합을 등록할 수 있습니다.

```python
dataset = dataset.register(workspace=ws,
                           name='mnist dataset',
                           description='training and test dataset',
                           create_new_version=True)
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

## <a name="create-a-tensorflow-estimator-and-import-keras"></a>TensorFlow 평가기를 만들고 Keras를 가져옵니다.

[TensorFlow 평가기](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) 는 계산 대상에서 TensorFlow 학습 작업을 시작 하는 간단한 방법을 제공 합니다. Keras는 TensorFlow에서 실행 되므로 TensorFlow 평가기를 사용 하 고 인수를 사용 하 여 Keras 라이브러리를 가져올 수 있습니다 `pip_packages` .

먼저 클래스를 사용 하 여 작업 영역 데이터 저장소에서 데이터를 가져옵니다 `Dataset` .

```python
dataset = Dataset.get_by_name(ws, 'mnist dataset')

# list the files referenced by mnist dataset
dataset.to_path()
```

TensorFlow 평가기는 [`estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) 프레임 워크를 지 원하는 데 사용할 수 있는 제네릭 클래스를 통해 구현 됩니다. 또한 `script_params` DNN 하이퍼 매개 변수 설정을 포함 하는 사전을 만듭니다. 일반 예측 도구를 사용하는 학습 모델에 대한 자세한 내용은 [예측 도구를 사용하여 Azure Machine Learning에서 모델 학습](how-to-train-ml-models.md)을 참조하세요.

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

## <a name="submit-a-run"></a>실행 제출

[실행 개체](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) 는 작업이 실행 되는 동안 그리고 작업이 완료 된 후 실행 기록에 인터페이스를 제공 합니다.

```Python
run = exp.submit(est)
run.wait_for_completion(show_output=True)
```

실행이 실행 되 면 다음 단계를 거칩니다.

- **준비**: TensorFlow 평가기에 따라 docker 이미지가 생성 됩니다. 이미지는 작업 영역 컨테이너 레지스트리로 업로드 되 고 나중에 실행할 수 있도록 캐시 됩니다. 로그는 실행 기록에도 스트리밍되 고 진행률을 모니터링 하기 위해 볼 수 있습니다.

- **크기 조정**: 클러스터는 현재 사용 가능한 것 보다 더 많은 노드를 실행 하는 Batch AI 클러스터가 필요한 경우 확장을 시도 합니다.

- **실행 중**: 스크립트 폴더의 모든 스크립트가 계산 대상으로 업로드 되 고, 데이터 저장소가 탑재 되거나 복사 되 고, entry_script 실행 됩니다. Stdout의 출력과./clogs 폴더는 실행 기록으로 스트리밍되 며 실행을 모니터링 하는 데 사용할 수 있습니다.

- **사후 처리**: 실행의./출력 폴더가 실행 기록에 복사 됩니다.

## <a name="register-the-model"></a>모델 등록

DNN 모델을 학습 한 후에는 작업 영역에 등록할 수 있습니다. 모델 등록을 사용하면 모델을 작업 영역에 저장하고 버전을 지정하여 [모델 관리 및 배포](concept-model-management-and-deployment.md)를 간소화할 수 있습니다.

```Python
model = run.register_model(model_name='keras-dnn-mnist', model_path='outputs/model')
```

> [!TIP]
> 방금 등록 한 모델은 학습에 사용한 평가기에 관계 없이 Azure Machine Learning에서 등록 된 다른 모델과 정확히 동일한 방식으로 배포 됩니다. 배포 방법에는 모델 등록에 대 한 섹션이 포함 되어 있지만 등록 된 모델이 이미 있기 때문에 배포에 대 한 [계산 대상을 직접 만드는](how-to-deploy-and-where.md#choose-a-compute-target) 것으로 건너뛸 수 있습니다.

모델의 로컬 복사본을 다운로드할 수도 있습니다. 이는 로컬에서 추가 모델 유효성 검사 작업을 수행 하는 데 유용할 수 있습니다. 학습 스크립트에서 `mnist-keras.py` TensorFlow 보호기 개체는 모델을 로컬 폴더 (계산 대상의 로컬 폴더)에 유지 합니다. Run 개체를 사용 하 여 데이터 저장소에서 복사본을 다운로드할 수 있습니다.

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

> [!div class="nextstepaction"]
> [모델을 배포 하는 방법 및 위치](how-to-deploy-and-where.md)
* [학습 중에 실행 메트릭 추적](how-to-track-experiments.md)
* [하이퍼 매개 변수 조정](how-to-tune-hyperparameters.md)
* [학습된 모델 배포](how-to-deploy-and-where.md)
* [Azure의 분산 심층 학습 교육에 대 한 참조 아키텍처](/azure/architecture/reference-architectures/ai/training-deep-learning)
