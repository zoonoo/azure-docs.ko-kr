---
title: 학습 및 TensorFlow를 실행 하는 Keras 모델 등록
titleSuffix: Azure Machine Learning service
description: 이 아티클에서 학습 및 TensorFlow를 실행 하는 Keras 모델을 등록 하는 Azure Machine Learning 서비스를 사용 하 여 합니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: minxia
author: mx-iao
ms.date: 06/07/2019
ms.custom: seodec18
ms.openlocfilehash: bd2552cdfde19995413f4665f04c41c295304d50
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67082600"
---
# <a name="train-and-register-keras-models-at-scale-with-azure-machine-learning-service"></a>학습 및 Azure Machine Learning 서비스를 사용 하 여 대규모로 Keras 모델 등록

이 아티클에서 학습 및 TensorFlow를 기반으로 하는 Keras 모델을 등록 하는 Azure Machine Learning 서비스를 사용 하 여 합니다. 사용 하 여 널리 사용 되 [MNIST 데이터 집합](http://yann.lecun.com/exdb/mnist/) 필기 숫자를 사용 하 여 빌드된 심층 신경망 네트워크 DNN ()를 사용 하 여 분류 하는 [Keras Python 라이브러리](https://keras.io) 기반으로 실행 [TensorFlow](https://www.tensorflow.org/overview) .

Keras는 고급 신경망 네트워크 API 개발 간소화 하기 위해 다른 인기 있는 DNN 프레임 워크의 위쪽을 실행할 수 있습니다. Azure Machine Learning 서비스를 사용 하 여 탄력적인 클라우드 계산 리소스를 사용 하 여 학습 작업 규모를 신속 하 게 확장할 수 있습니다. 교육 실행, 버전 모델을 통해 추적할 수 있습니다 모델 등을 배포 합니다.

부터 Keras 모델을 개발 하 든 기존 모델을 클라우드로 가져오는 Azure Machine Learning 서비스 도움이 될 수 있습니다 프로덕션이 준비 된 모델을 작성 합니다.

## <a name="prerequisites"></a>필수 조건

- Azure 구독. [Azure Machine Learning Service의 평가판 또는 유료 버전](https://aka.ms/AMLFree)을 지금 사용해 보세요.
- [Azure Python SDK 학습 컴퓨터 설치](setup-create-workspace.md#sdk)
- [작업 영역 구성 파일 만들기](setup-create-workspace.md#write-a-configuration-file)
- [샘플 스크립트 파일을 다운로드](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras) `mnist-keras.py` 및 `utils.py`

완료 된 찾을 수도 있습니다 [Jupyter Notebook 버전](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras/train-hyperparameter-tune-deploy-with-keras.ipynb) GitHub 샘플 페이지에서이 가이드의 합니다. Notebook 지능형 하이퍼 매개 변수 튜닝, 모델 배포 및 notebook 위젯 확장된 섹션을 포함 합니다.

## <a name="set-up-the-experiment"></a>실험을 설정

이 섹션에서는 필요한 python 패키지를 로드, 초기화 작업 영역, 실험, 만들기 및 학습 데이터와 학습 스크립트를 업로드 하 여 학습 실험을 설정 합니다.

### <a name="import-packages"></a>패키지 가져오기

먼저, 필요한 Python 라이브러리를 가져와야 합니다.

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

작업 영역 개체를 만들어야 합니다 `config.json` 에서 만든 파일을 [전제 조건 섹션](#prerequisites)합니다.

```Python
ws = Workspace.from_config()
```

### <a name="create-an-experiment"></a>실험 만들기

실험 및 학습 스크립트를 보관할 폴더를 만듭니다. 이 예제에서는 "keras mnist"를 호출 하는 실험을 만듭니다.

```Python
script_folder = './keras-mnist'
os.makedirs(script_folder, exist_ok=True)

exp = Experiment(workspace=ws, name='keras-mnist')
```

### <a name="upload-dataset-and-scripts"></a>데이터 집합 및 스크립트 업로드

합니다 [데이터 저장소](how-to-access-data.md) 데이터 저장 고 탑재 하거나 계산 대상에 데이터를 복사 하 여 액세스할 수 있는 위치입니다. 각 작업 영역의 기본 데이터 저장소를 제공합니다. 학습 하는 동안 쉽게 액세스할 수 있도록 데이터 저장소에 데이터 및 학습 스크립트를 업로드 합니다.

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

1. Keras 학습 스크립트를 업로드 `keras_mnist.py`, 및 도우미 파일 `utils.py`합니다.

    ```Python
    shutil.copy('./keras_mnist.py', script_folder)
    shutil.copy('./utils.py', script_folder)
    ```

## <a name="get-the-default-compute-target"></a>기본 계산 대상을 가져오기

각 작업 영역에 두는, 기본 계산 대상: gpu 기반 계산 대상 및 cpu 기반 계산 대상입니다. 기본 계산 대상을 사용 될 때까지 할당 되지 않습니다 즉 0으로 설정 하는 자동 크기 조정 합니다. 이 예제에서는 wIn, 기본 GPU 계산 대상을 사용 합니다.

```Python
compute_target = ws.get_default_compute_target(type="GPU")
```

계산 대상에 대 한 자세한 내용은 참조는 [계산 대상을 란](concept-compute-target.md) 문서.

## <a name="create-a-tensorflow-estimator-and-import-keras"></a>TensorFlow 평가기를 만들고 Keras 가져오기

합니다 [TensorFlow 스 티 메이 터](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) 계산 대상에서 TensorFlow 교육 작업을 시작 하는 간단한 방법을 제공 합니다. Keras는 TensorFlow 기반으로 실행, 되므로를 TensorFlow 평가기를 사용 하 여 한 사용 하 여 Keras 라이브러리를 가져올 수 있습니다는 `pip_packages` 인수입니다.

제네릭 통해 구현 됩니다. TensorFlow 평가기 [ `estimator` ](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) 모든 프레임 워크를 지원 하기 위해 사용할 수 있는 클래스입니다. 제네릭 평가기를 사용 하 여 모델을 학습 하는 방법에 대 한 자세한 내용은 참조 하세요. [평가기를 사용 하 여 Azure Machine Learning을 사용 하 여 모델을 학습 합니다.](how-to-train-ml-models.md)

```Python
script_params = {
    '--data-folder': ds.path('mnist').as_mount(),
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

## <a name="submit-a-run"></a>실행을 제출 합니다.

합니다 [개체 실행](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) 작업이 실행 되 고 완료 된 후 실행된 기록에 인터페이스를 제공 합니다.

```Python
run = exp.submit(est)
run.wait_for_completion(show_output=True)
```

실행을 실행 하는 다음 단계를 통해 이동 합니다.

- **준비**: Docker 이미지를 TensorFlow 스 티 메이 터에 따라 생성 됩니다. 이미지 작업 공간의 container registry에 업로드 이며 이후 실행에 대 한 캐시 됩니다. 로그는 실행된 기록에도 스트리밍되고 진행 상황을 모니터링 볼 수 있습니다.

- **크기 조정**: 클러스터는 Batch AI 클러스터 노드를 현재 사용할 수 있는 것 보다는 실행을 실행 해야 하는 경우 확장 하려고 합니다.

- **Running**: 스크립트 폴더에 있는 모든 스크립트가 계산 대상에 업로드 됩니다, 데이터 저장소는 탑재 하거나 복사할 수 고를 entry_script 실행 됩니다. Stdout에서 출력 및. / logs 폴더 실행된 기록에 스트리밍됩니다 및 실행을 모니터링 할 수 있습니다.

- **후 처리 중**: 합니다. / 출력 폴더 실행의 실행된 기록에 복사 됩니다.

## <a name="register-the-model"></a>모델 등록

모델을 학습 한 후 작업 영역에 등록할 수 있습니다. 모델 등록 하면 저장소와 버전 간소화 하기 위해 작업 영역에서 모델 [모델 관리 및 배포](concept-model-management-and-deployment.md)합니다.

```Python
model = run.register_model(model_name='keras-dnn-mnist', model_path='outputs/model')
```

또한 모델의 로컬 복사본을 다운로드할 수 있습니다. 이 로컬로 추가 모델 유효성 검사 작업을 수행 하는 것에 대 한 유용할 수 있습니다. 학습 스크립트에서 `mnist-keras.py`, TensorFlow 보호기를 계산 대상 (로컬)를 로컬 폴더에 모델을 유지 합니다. 데이터 저장소에서 복사본을 다운로드 하려면 Run 개체를 사용할 수 있습니다.

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

이 문서에서는 학습 하 고 Azure Machine Learning 서비스에서 Keras 모델을 등록 합니다. 모델을 배포 하는 방법에 알아보려면 모델 배포 문서를 진행 합니다.

> [!div class="nextstepaction"]
> [방법 및 모델을 배포할 수 있는 위치](how-to-deploy-and-where.md)
