---
title: Chainer 모델 학습 및 등록
titleSuffix: Azure Machine Learning service
description: 이 문서에서는 학습 Azure Machine Learning 서비스를 사용 하 여 Chainer 모델을 등록 하는 방법을 보여 줍니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.reviewer: sdgilley
ms.date: 06/15/2019
ms.openlocfilehash: 8ecefccbdf5f02652e935858b6ae8fb4cdfde640
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67840031"
---
# <a name="train-and-register-chainer-models-at-scale-with-azure-machine-learning-service"></a>학습 및 Azure Machine Learning 서비스를 사용 하 여 대규모로 Chainer 모델 등록

이 문서에서는 학습 Azure Machine Learning 서비스를 사용 하 여 Chainer 모델을 등록 하는 방법을 보여 줍니다. 사용 하 여 널리 사용 되 [MNIST 데이터 집합](http://yann.lecun.com/exdb/mnist/) 필기 숫자를 사용 하 여 빌드된 심층 신경망 네트워크 DNN ()를 사용 하 여 분류 하는 [Chainer Python 라이브러리](https://Chainer.org) 기반으로 실행 [numpy](https://www.numpy.org/)합니다.

Chainer는 고급 신경망 네트워크 API 개발 간소화 하기 위해 다른 인기 있는 DNN 프레임 워크의 상단에서 실행할 수 있습니다. Azure Machine Learning 서비스를 사용 하 여 탄력적인 클라우드 계산 리소스를 사용 하 여 학습 작업 규모를 신속 하 게 확장할 수 있습니다. 교육 실행, 버전 모델을 통해 추적할 수 있습니다 모델 등을 배포 합니다.

부터 Chainer 모델을 개발 하 든 기존 모델을 클라우드로 가져오는 Azure Machine Learning 서비스 도움이 될 수 있습니다 프로덕션이 준비 된 모델을 작성 합니다.

Azure 구독이 없는 경우 시작하기 전에 체험 계정을 만듭니다. [Azure Machine Learning Service의 평가판 또는 유료 버전](https://aka.ms/AMLFree)을 지금 사용해 보세요.

## <a name="prerequisites"></a>필수 구성 요소

이러한 환경 중 하나에서이 코드를 실행 합니다.

- Azure Machine Learning Notebook VM-다운로드 나 설치 필요 없이

    - 완료 합니다 [클라우드 기반 notebook 퀵 스타트](quickstart-run-cloud-notebook.md) SDK 및 샘플 리포지토리를 사용 하 여 미리 로드 전용된 노트북 서버를 만들려면.
    - Notebook 서버에 샘플 폴더에서 찾고 완료 된 notebook을 파일에는 **how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-chainer** 폴더입니다.  Notebook 지능형 하이퍼 매개 변수 튜닝, 모델 배포 및 notebook 위젯 확장된 섹션을 포함 합니다.

- 사용자 고유의 Jupyter Notebook 서버

    - [Azure Python SDK 학습 컴퓨터 설치](setup-create-workspace.md#sdk)
    - [작업 영역 구성 파일 만들기](setup-create-workspace.md#write-a-configuration-file)
    - 예제 스크립트 파일을 다운로드 [chainer_mnist.py](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-chainer/chainer_mnist.py)
     - 완료 된 찾을 수도 있습니다 [Jupyter Notebook 버전](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-chainer/train-hyperparameter-tune-deploy-with-chainer.ipynb) GitHub 샘플 페이지에서이 가이드의 합니다. Notebook 지능형 하이퍼 매개 변수 튜닝, 모델 배포 및 notebook 위젯 확장된 섹션을 포함 합니다.

## <a name="set-up-the-experiment"></a>실험을 설정

이 섹션에서는 필요한 python 패키지를 로드, 초기화 작업 영역, 실험, 만들기 및 학습 데이터와 학습 스크립트를 업로드 하 여 학습 실험을 설정 합니다.

### <a name="import-packages"></a>패키지 가져오기

먼저, azureml.core Python 라이브러리 ad 표시 버전 번호를 가져와야 합니다.

```
# Check core SDK version number
import azureml.core

print("SDK version:", azureml.core.VERSION)
```

### <a name="initialize-a-workspace"></a>작업 영역 초기화

[Azure Machine Learning 서비스 작업 영역](concept-workspace.md) 서비스에 대 한 최상위 리소스입니다. 만든 모든 아티팩트를 작업할 수 있는 중앙된 위치를 사용 하 여 제공 합니다. Python SDK에서 작업 영역 아티팩트를 만들어 액세스할 수 있습니다는 [ `workspace` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) 개체입니다.

작업 영역 개체를 만들어야 합니다 `config.json` 에서 만든 파일을 [전제 조건 섹션](#prerequisites)합니다.

```Python
ws = Workspace.from_config()
```

### <a name="create-a-project-directory"></a>프로젝트 디렉터리를 만듭니다
원격 리소스에 대 한 액세스를 해야 로컬 컴퓨터에서 필요한 모든 코드에 있는 디렉터리를 만듭니다. 여기에 학습 스크립트 및 추가 파일 학습 스크립트에 따라 달라 집니다.

```
import os

project_folder = './chainer-mnist'
os.makedirs(project_folder, exist_ok=True)
```

### <a name="prepare-training-script"></a>학습 스크립트를 준비 합니다.

이 자습서에서는 학습 스크립트 **chainer_mnist.py** 이미 제공 됩니다. 실제로 코드를 수정 하지 않고도 Azure ML을 사용 하 여 실행 하는 대로 모든 사용자 지정 학습 스크립트를 수행할 수 있게 해야 합니다.

Azure ML의 추적 및 메트릭 기능을 사용 하려면 약간의 Azure ML 학습 스크립트 내에서 코드를 추가 해야 합니다.  학습 스크립트 **chainer_mnist.py** 실행 하 여 Azure ML에 몇 가지 메트릭을 기록 하는 방법을 보여 줍니다. 이렇게 하려면 Azure ML 액세스 `Run` 스크립트 내에서 개체입니다.

학습 스크립트를 복사 **chainer_mnist.py** 프로젝트 디렉터리에 있습니다.

```
import shutil

shutil.copy('chainer_mnist.py', project_folder)
```

### <a name="create-an-experiment"></a>실험 만들기

실험 및 학습 스크립트를 보관할 폴더를 만듭니다. 이 예제에서는 "chainer mnist"를 호출 하는 실험을 만듭니다.

```
from azureml.core import Experiment

experiment_name = 'chainer-mnist'
experiment = Experiment(ws, name=experiment_name)
```


## <a name="create-or-get-a-compute-target"></a>만들기 또는 가져오기 계산 대상

해야 합니다는 [계산 대상](concept-compute-target.md) 모델을 학습 합니다. 이 자습서에서는 원격 학습 계산 리소스에 대 한 관리 되는 Azure ML 계산 (AmlCompute)를 사용 합니다.

**생성의 AmlCompute 약 5 분이 걸립니다**합니다. 해당 이름의 AmlCompute 작업 영역에 이미 있으면이 코드는 생성 프로세스를 건너뜁니다.  

```Python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# choose a name for your cluster
cluster_name = "gpu-cluster"

try:
    compute_target = ComputeTarget(workspace=ws, name=cluster_name)
    print('Found existing compute target.')
except ComputeTargetException:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_NC6', 
                                                           max_nodes=4)

    # create the cluster
    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

    compute_target.wait_for_completion(show_output=True)

# use get_status() to get a detailed status for the current cluster. 
print(compute_target.get_status().serialize())
```

계산 대상에 대 한 자세한 내용은 참조는 [계산 대상을 란](concept-compute-target.md) 문서.

## <a name="create-a-chainer-estimator"></a>Chainer 예측 만들기

합니다 [Chainer 스 티 메이 터](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) 계산 대상에서 Chainer 교육 작업을 시작 하는 간단한 방법을 제공 합니다.

제네릭 통해 구현 됩니다. Chainer 평가기 [ `estimator` ](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) 모든 프레임 워크를 지원 하기 위해 사용할 수 있는 클래스입니다. 제네릭 평가기를 사용 하 여 모델을 학습 하는 방법에 대 한 자세한 내용은 참조 하세요. [평가기를 사용 하 여 Azure Machine Learning을 사용 하 여 모델을 학습 합니다.](how-to-train-ml-models.md)

```Python
from azureml.train.dnn import Chainer

script_params = {
    '--epochs': 10,
    '--batchsize': 128,
    '--output_dir': './outputs'
}

estimator = Chainer(source_directory=project_folder, 
                    script_params=script_params,
                    compute_target=compute_target,
                    pip_packages=['numpy', 'pytest'],
                    entry_script='chainer_mnist.py',
                    use_gpu=True)
```

## <a name="submit-a-run"></a>실행을 제출 합니다.

합니다 [개체 실행](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) 작업이 실행 되 고 완료 된 후 실행된 기록에 인터페이스를 제공 합니다.

```Python
run = exp.submit(est)
run.wait_for_completion(show_output=True)
```

실행을 실행 하는 다음 단계를 통해 이동 합니다.

- **준비**: Chainer 스 티 메이 터에 따라 docker 이미지를 생성 됩니다. 이미지 작업 공간의 container registry에 업로드 이며 이후 실행에 대 한 캐시 됩니다. 로그는 실행된 기록에도 스트리밍되고 진행 상황을 모니터링 볼 수 있습니다.

- **크기 조정**: 클러스터는 Batch AI 클러스터 노드를 현재 사용할 수 있는 것 보다는 실행을 실행 해야 하는 경우 확장 하려고 합니다.

- **Running**: 스크립트 폴더에 있는 모든 스크립트가 계산 대상에 업로드 됩니다, 데이터 저장소는 탑재 하거나 복사할 수 고를 entry_script 실행 됩니다. Stdout에서 출력 및. / logs 폴더 실행된 기록에 스트리밍됩니다 및 실행을 모니터링 할 수 있습니다.

- **후 처리 중**: 합니다. / 출력 폴더 실행의 실행된 기록에 복사 됩니다.

## <a name="save-and-register-the-model"></a>저장 하 고 모델 등록

모델을 학습 한 후에 저장 하 고 작업 영역에 등록 수 있습니다. 모델 등록 하면 저장소와 버전 간소화 하기 위해 작업 영역에서 모델 [모델 관리 및 배포](concept-model-management-and-deployment.md)합니다.

학습 스크립트에 다음 코드를 추가 **chainer_mnist.py**, 모델을 저장 합니다. 

``` Python
    serializers.save_npz(os.path.join(args.output_dir, 'model.npz'), model)
```

다음 코드를 사용 하 여 모델 작업 영역을 등록 합니다.

```Python
model = run.register_model(model_name='chainer-dnn-mnist', model_path='outputs/model.npz')
```



## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure Machine Learning 서비스에서 Chainer 모델을 학습합니다. 

* 모델을 배포 하는 방법에 알아보려면에 계속 우리의 [배포 모델](how-to-deploy-and-where.md) 문서.

* [하이퍼 매개 변수 조정](how-to-tune-hyperparameters.md)

* [학습 중에 실행 메트릭 추적](how-to-track-experiments.md)
