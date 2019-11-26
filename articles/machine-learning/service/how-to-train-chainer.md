---
title: 심층 학습 체 이너 모델 학습
titleSuffix: Azure Machine Learning
description: Azure Machine Learning의 체 이너 평가기 클래스를 사용 하 여 엔터프라이즈 규모에서 PyTorch 학습 스크립트를 실행 하는 방법을 알아봅니다.  예제 스크립트는 numpy 위에서 실행 되는 체 이너 Python 라이브러리를 사용 하 여 심층 학습 신경망을 빌드하기 위해 필기 숫자 이미지를 분류 합니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.reviewer: sdgilley
ms.date: 08/02/2019
ms.openlocfilehash: f384a6a870d891bbaf6fa20a896b0251e62b8d4f
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74224974"
---
# <a name="train-and-register-chainer-models-at-scale-with-azure-machine-learning"></a>Azure Machine Learning를 사용 하 여 대규모로 체 이너 모델 학습 및 등록
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

이 문서에서는 Azure Machine Learning의 [체 이너 평가기](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) 클래스를 사용 하 여 엔터프라이즈 규모에서 [체 이너](https://chainer.org/) 교육 스크립트를 실행 하는 방법에 대해 알아봅니다. 이 문서의 예제 학습 스크립트는 인기 있는 [Mnist 데이터 집합](http://yann.lecun.com/exdb/mnist/) 을 사용 하 여 [numpy](https://www.numpy.org/)에서 실행 되는 체 이너 Python 라이브러리를 사용 하 여 빌드된 DNN (심층 신경망)를 사용 하 여 필기 된 숫자를 분류 합니다.

처음부터 심층 학습 체 이너 모델을 학습 하 고 있거나 기존 모델을 클라우드로 전환 하는 경우에는 Azure Machine Learning를 사용 하 여 탄력적 클라우드 계산 리소스를 사용 하 여 오픈 소스 학습 작업을 확장할 수 있습니다. Azure Machine Learning를 사용 하 여 프로덕션 등급 모델을 빌드, 배포, 버전 및 모니터링할 수 있습니다. 

[심층 학습 vs machine learning](concept-deep-learning-vs-machine-learning.md)에 대해 자세히 알아보세요.

Azure 구독이 없는 경우 시작하기 전에 체험 계정을 만듭니다. 지금 [Azure Machine Learning의 평가판 또는 유료 버전](https://aka.ms/AMLFree)을 사용해 보세요.

## <a name="prerequisites"></a>선행 조건

이러한 환경 중 하나에서이 코드를 실행 합니다.

- Azure Machine Learning 노트북 VM-다운로드 또는 설치 필요 없음

    - [자습서: 설치 환경 및 작업 영역](tutorial-1st-experiment-sdk-setup.md) 을 완료 하 여 SDK 및 샘플 리포지토리를 사용 하 여 미리 로드 한 전용 노트북 서버를 만듭니다.
    - 노트북 서버의 샘플 심층 학습 폴더에 있는 **사용 방법-azureml > ml 프레임 워크 > 체 이너 > 배포 > 학습-hyperparameter 변수-튜닝-배포-** 대/소문자를 구분 하는 폴더)에서 완성 된 노트북 및 파일을 찾습니다.  노트북에는 지능형 하이퍼 매개 변수 튜닝, 모델 배포 및 노트북 위젯을 다루는 확장 된 섹션이 포함 되어 있습니다.

- 사용자 고유의 Jupyter Notebook 서버

    - [AZURE MACHINE LEARNING SDK를 설치](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)합니다.
    - [작업 영역 구성 파일을 만듭니다](how-to-configure-environment.md#workspace).
    - Py 샘플 스크립트 chainer_mnist 파일을 다운로드 합니다 [.](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/chainer/deployment/train-hyperparameter-tune-deploy-with-chainer)
     - GitHub 샘플 페이지에서이 가이드의 전체 [Jupyter Notebook 버전](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/chainer/deployment/train-hyperparameter-tune-deploy-with-chainer/train-hyperparameter-tune-deploy-with-chainer.ipynb) 을 찾을 수도 있습니다. 노트북에는 지능형 하이퍼 매개 변수 튜닝, 모델 배포 및 노트북 위젯을 다루는 확장 된 섹션이 포함 되어 있습니다.

## <a name="set-up-the-experiment"></a>실험 설정

이 섹션에서는 필요한 python 패키지를 로드 하 고, 작업 영역을 초기화 하 고, 실험을 만들고, 학습 데이터 및 학습 스크립트를 업로드 하 여 학습 실험을 설정 합니다.

### <a name="import-packages"></a>패키지 가져오기

먼저 azureml Python 라이브러리를 가져오고 버전 번호를 표시 합니다.

```
# Check core SDK version number
import azureml.core

print("SDK version:", azureml.core.VERSION)
```

### <a name="initialize-a-workspace"></a>작업 영역 초기화

[Azure Machine Learning 작업 영역은](concept-workspace.md) 서비스에 대 한 최상위 리소스입니다. 사용자가 만드는 모든 아티팩트를 사용할 수 있는 중앙 집중식 환경을 제공 합니다. Python SDK에서 [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) 개체를 만들어 작업 영역 아티팩트에 액세스할 수 있습니다.

[필수 조건 섹션](#prerequisites)에서 만든 `config.json` 파일을 읽어 작업 영역 개체를 만듭니다.

```Python
ws = Workspace.from_config()
```

### <a name="create-a-project-directory"></a>프로젝트 디렉터리 만들기
원격 리소스에서 액세스 해야 하는 로컬 컴퓨터의 모든 필요한 코드를 포함 하는 디렉터리를 만듭니다. 여기에는 학습 스크립트와 학습 스크립트가 종속 된 모든 추가 파일이 포함 됩니다.

```
import os

project_folder = './chainer-mnist'
os.makedirs(project_folder, exist_ok=True)
```

### <a name="prepare-training-script"></a>학습 스크립트 준비

이 자습서에서는 학습 스크립트 **chainer_mnist py** 이미 제공 되어 있습니다. 실제로 사용자 지정 학습 스크립트를 그대로 사용 하 고 코드를 수정 하지 않고도 Azure ML을 사용 하 여 실행할 수 있습니다.

Azure ML의 추적 및 메트릭 기능을 사용 하려면 교육 스크립트 내에 적은 양의 Azure ML 코드를 추가 합니다.  **Py 학습 chainer_mnist** 스크립트는 스크립트 내에서 `Run` 개체를 사용 하 여 Azure ML 실행에 일부 메트릭을 기록 하는 방법을 보여 줍니다.

제공 된 학습 스크립트는 체 이너 `datasets.mnist.get_mnist` 함수의 예제 데이터를 사용 합니다.  사용자 고유의 데이터에 대해 데이터 [집합 및 스크립트 업로드](how-to-train-keras.md#data-upload) 와 같은 단계를 사용 하 여 학습 중에 데이터를 사용할 수 있도록 해야 할 수 있습니다.

**Py 학습 chainer_mnist** 스크립트를 프로젝트 디렉터리에 복사 합니다.

```
import shutil

shutil.copy('chainer_mnist.py', project_folder)
```

### <a name="create-a-deep-learning-experiment"></a>심층 학습 실험 만들기

실험 만들기 이 예에서는 "체 이너-mnist" 라는 실험을 만듭니다.

```
from azureml.core import Experiment

experiment_name = 'chainer-mnist'
experiment = Experiment(ws, name=experiment_name)
```


## <a name="create-or-get-a-compute-target"></a>계산 대상 만들기 또는 가져오기

모델 학습을 위한 [계산 대상이](concept-compute-target.md) 필요 합니다. 이 예제에서는 원격 학습 계산 리소스에 대해 AmlCompute (Azure ML 관리 계산)를 사용 합니다.

**AmlCompute 생성에는 약 5 분이 걸립니다**. 해당 이름을 가진 AmlCompute가 이미 작업 영역에 있는 경우이 코드는 생성 프로세스를 건너뜁니다.  

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

계산 대상에 대 한 자세한 내용은 [계산 대상 이란?](concept-compute-target.md) 문서를 참조 하세요.

## <a name="create-a-chainer-estimator"></a>체 이너 평가기 만들기

[체 이너 평가기](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) 는 계산 대상에서 체 이너 학습 작업을 시작 하는 간단한 방법을 제공 합니다.

체 이너 평가기는 모든 프레임 워크를 지 원하는 데 사용할 수 있는 제네릭 [`estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) 클래스를 통해 구현 됩니다. 제네릭 평가기을 사용한 학습 모델에 대 한 자세한 내용은 [평가기를 사용 하 여 Azure Machine Learning를](how-to-train-ml-models.md) 사용 하 여 모델 학습을 참조 하세요.

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

## <a name="submit-a-run"></a>실행 제출

[실행 개체](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) 는 작업이 실행 되는 동안 그리고 작업이 완료 된 후 실행 기록에 인터페이스를 제공 합니다.

```Python
run = exp.submit(est)
run.wait_for_completion(show_output=True)
```

실행이 실행 되 면 다음 단계를 거칩니다.

- **준비**: 체 이너 평가기에 따라 docker 이미지가 생성 됩니다. 이미지는 작업 영역 컨테이너 레지스트리로 업로드 되 고 나중에 실행할 수 있도록 캐시 됩니다. 로그는 실행 기록에도 스트리밍되 고 진행률을 모니터링 하기 위해 볼 수 있습니다.

- **크기 조정**: 클러스터는 현재 사용 가능한 것 보다 더 많은 노드를 실행 하는 Batch AI 클러스터가 필요한 경우 확장을 시도 합니다.

- **실행 중**: 스크립트 폴더의 모든 스크립트가 계산 대상으로 업로드 되 고, 데이터 저장소가 탑재 되거나 복사 되 고, entry_script 실행 됩니다. Stdout의 출력과./clogs 폴더는 실행 기록으로 스트리밍되 며 실행을 모니터링 하는 데 사용할 수 있습니다.

- **사후 처리**: 실행의./출력 폴더가 실행 기록에 복사 됩니다.

## <a name="save-and-register-the-model"></a>모델 저장 및 등록

모델을 학습 한 후에는 작업 영역에 저장 하 고 등록할 수 있습니다. 모델 등록을 사용 하면 모델 [관리 및 배포](concept-model-management-and-deployment.md)를 간소화 하기 위해 작업 영역에 모델을 저장 하 고 버전을 지정할 수 있습니다.


모델 학습을 완료 한 후 다음 코드를 사용 하 여 모델을 작업 영역에 등록 합니다.  

```Python
model = run.register_model(model_name='chainer-dnn-mnist', model_path='outputs/model.npz')
```

> [!TIP]
> 방금 등록 한 모델은 학습에 사용한 평가기에 관계 없이 Azure Machine Learning에서 등록 된 다른 모델과 정확히 동일한 방식으로 배포 됩니다. 배포 방법에는 모델 등록에 대 한 섹션이 포함 되어 있지만 등록 된 모델이 이미 있기 때문에 배포에 대 한 [계산 대상을 직접 만드는](how-to-deploy-and-where.md#choose-a-compute-target) 것으로 건너뛸 수 있습니다.

모델의 로컬 복사본을 다운로드할 수도 있습니다. 이는 로컬에서 추가 모델 유효성 검사 작업을 수행 하는 데 유용할 수 있습니다. 학습 스크립트에서 `chainer_mnist.py`보호기 개체는 모델을 로컬 폴더 (계산 대상의 로컬 폴더)에 유지 합니다. Run 개체를 사용 하 여 데이터 저장소에서 복사본을 다운로드할 수 있습니다.

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

이 문서에서는 Azure Machine Learning에 체 이너를 사용 하 여 심층 학습, 신경망을 학습 하 고 등록 했습니다. 모델을 배포 하는 방법에 대 한 자세한 내용은 [모델 배포](how-to-deploy-and-where.md) 문서를 참조 하세요.

* [하이퍼 매개 변수 조정](how-to-tune-hyperparameters.md)

* [학습 중에 실행 메트릭 추적](how-to-track-experiments.md)

* [Azure에서 분산 심층 학습 교육에 대 한 참조 아키텍처 보기](/azure/architecture/reference-architectures/ai/training-deep-learning)
