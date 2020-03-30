---
title: 딥 러닝 체인러 모델 교육
titleSuffix: Azure Machine Learning
description: Azure 기계 학습 체인어 추정 클래스를 사용하여 엔터프라이즈 규모에서 PyTorch 교육 스크립트를 실행하는 방법을 알아봅니다.  예제 스크립트는 손으로 쓴 숫자 이미지를 분류하여 숫자 위에서 실행되는 Chainer Python 라이브러리를 사용하여 딥 러닝 신경망을 구축합니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.reviewer: sdgilley
ms.date: 08/02/2019
ms.openlocfilehash: e2840a6295140e0dc22a032fa844c0488403c5a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75536617"
---
# <a name="train-and-register-chainer-models-at-scale-with-azure-machine-learning"></a>Azure 기계 학습을 통해 규모에 맞게 Chainer 모델 교육 및 등록
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

이 문서에서는 Azure 기계 학습 [Chainer 추정 클래스를](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) 사용하여 엔터프라이즈 규모에서 [Chainer](https://chainer.org/) 교육 스크립트를 실행하는 방법을 알아봅니다. 이 문서의 예제 학습 스크립트는 인기 있는 [MNIST 데이터 집합을](http://yann.lecun.com/exdb/mnist/) 사용하여 [numpy](https://www.numpy.org/)위에 실행되는 Chainer Python 라이브러리를 사용하여 빌드된 DNN(심층 신경망)을 사용하여 손으로 쓴 숫자를 분류합니다.

처음부터 딥 러닝 Chainer 모델을 교육하든 기존 모델을 클라우드로 가져오든 Azure Machine Learning을 사용하여 탄력적 클라우드 컴퓨팅 리소스를 사용하여 오픈 소스 교육 작업을 확장할 수 있습니다. Azure 기계 학습을 통해 프로덕션 급 모델을 빌드, 배포, 버전 및 모니터링할 수 있습니다. 

[딥 러닝과 머신 러닝에](concept-deep-learning-vs-machine-learning.md)대해 자세히 알아보십시오.

Azure 구독이 없는 경우 시작하기 전에 체험 계정을 만듭니다. 지금 [Azure Machine Learning 평가판 또는 유료 버전](https://aka.ms/AMLFree)을 사용해 보세요.

## <a name="prerequisites"></a>사전 요구 사항

다음 환경 중 하나에서 이 코드를 실행합니다.

- Azure 기계 학습 계산 인스턴스 - 다운로드 또는 설치 필요 없음

    - 자습서 [완료: 설정 환경 및 작업 영역을](tutorial-1st-experiment-sdk-setup.md) 설정하여 SDK 및 샘플 리포지토리로 미리 로드된 전용 노트북 서버를 만듭니다.
    - 노트북 서버의 샘플 딥 러닝 폴더에서 사용 방법 azureml > ml 프레임워크에서 완료된 전자 필기장 과 파일을 찾을 **> 체인> 배포 > 기차 하이퍼매개 변수 조정-체인 폴더** 를 배포합니다.  노트북에는 지능형 하이퍼매개 변수 튜닝, 모델 배포 및 노트북 위젯을 포함하는 확장된 섹션이 포함되어 있습니다.

- 사용자 고유의 Jupyter Notebook 서버

    - [Azure 기계 학습 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)를 설치합니다.
    - [작업 영역 구성 파일 만들기](how-to-configure-environment.md#workspace).
    - 샘플 스크립트 파일 [chainer_mnist.py를](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/chainer/deployment/train-hyperparameter-tune-deploy-with-chainer)다운로드합니다.
     - GitHub 샘플 페이지에서 이 가이드의 완성된 [Jupyter 노트북 버전을](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/chainer/deployment/train-hyperparameter-tune-deploy-with-chainer/train-hyperparameter-tune-deploy-with-chainer.ipynb) 찾을 수도 있습니다. 노트북에는 지능형 하이퍼매개 변수 튜닝, 모델 배포 및 노트북 위젯을 포함하는 확장된 섹션이 포함되어 있습니다.

## <a name="set-up-the-experiment"></a>실험 설정

이 섹션에서는 필요한 파이썬 패키지를 로드하고, 작업 영역을 초기화하고, 실험을 만들고, 학습 데이터 및 학습 스크립트를 업로드하여 학습 실험을 설정합니다.

### <a name="import-packages"></a>패키지 가져오기

먼저 azureml.core 파이썬 라이브러리를 가져오고 버전 번호를 표시합니다.

```
# Check core SDK version number
import azureml.core

print("SDK version:", azureml.core.VERSION)
```

### <a name="initialize-a-workspace"></a>작업 영역 초기화

[Azure 기계 학습 작업 영역은](concept-workspace.md) 서비스의 최상위 리소스입니다. 그것은 당신이 만드는 모든 아티팩트와 함께 작업 할 수있는 중앙 집중식 장소를 제공합니다. Python SDK에서 개체를 만들어 작업 영역 아티팩트에 [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) 액세스할 수 있습니다.

[필수 구성 조건 섹션에서](#prerequisites)만든 `config.json` 파일을 읽고 작업 영역 개체를 만듭니다.

```Python
ws = Workspace.from_config()
```

### <a name="create-a-project-directory"></a>프로젝트 디렉터리 만들기
원격 리소스에 액세스해야 하는 로컬 컴퓨터에서 필요한 모든 코드를 포함하는 디렉터리를 만듭니다. 여기에는 학습 스크립트와 교육 스크립트가 종속된 추가 파일이 포함됩니다.

```
import os

project_folder = './chainer-mnist'
os.makedirs(project_folder, exist_ok=True)
```

### <a name="prepare-training-script"></a>교육 스크립트 준비

이 자습서에서는 교육 스크립트 **chainer_mnist.py가** 이미 제공됩니다. 실제로 코드를 수정하지 않고도 사용자 지정 학습 스크립트를 있는 것처럼 사용하여 Azure ML에서 실행할 수 있어야 합니다.

Azure ML 추적 및 메트릭 기능을 사용하려면 학습 스크립트 에 소량의 Azure ML 코드를 추가합니다.  **chainer_mnist.py** 학습 스크립트는 스크립트 내의 `Run` 개체를 사용하여 Azure ML 실행에 일부 메트릭을 기록하는 방법을 보여 줍니다.

제공된 학습 스크립트는 chainer `datasets.mnist.get_mnist` 함수의 예제 데이터를 사용합니다.  사용자 고유의 데이터의 경우 학습 중에 데이터를 사용할 수 있도록 [데이터 집합 및 스크립트 업로드와](how-to-train-keras.md#data-upload) 같은 단계를 사용해야 할 수 있습니다.

학습 스크립트 **chainer_mnist.py를** 프로젝트 디렉터리에 복사합니다.

```
import shutil

shutil.copy('chainer_mnist.py', project_folder)
```

### <a name="create-a-deep-learning-experiment"></a>딥 러닝 실험 만들기

실험 만들기 이 예제에서는 "chainer-mnist"라는 실험을 만듭니다.

```
from azureml.core import Experiment

experiment_name = 'chainer-mnist'
experiment = Experiment(ws, name=experiment_name)
```


## <a name="create-or-get-a-compute-target"></a>계산 대상 만들기 또는 받기

모델을 학습하기 위한 [계산 대상이](concept-compute-target.md) 필요합니다. 이 예제에서는 원격 학습 계산 리소스에 Azure ML 관리 되는 계산 (AmlCompute)를 사용 합니다.

**AmlCompute를 만드는 데 약 5 분이 걸립니다.** 해당 이름의 AmlCompute가 작업 영역에 이미 있는 경우 이 코드는 생성 프로세스를 건너뜁니다.  

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

계산 대상에 대한 자세한 내용은 [계산 대상](concept-compute-target.md) 문서를 참조하세요.

## <a name="create-a-chainer-estimator"></a>체인어 추정기 만들기

[Chainer 추정기는](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) 계산 대상에서 Chainer 교육 작업을 시작하는 간단한 방법을 제공합니다.

Chainer 추정기는 모든 프레임워크를 지원하는 데 사용할 수 있는 제네릭 [`estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) 클래스를 통해 구현됩니다. 일반 추정기를 사용하는 학습 모델에 대한 자세한 내용은 [추정기를 사용하여 Azure 기계 학습을 사용하는 학습 모델을](how-to-train-ml-models.md) 참조하십시오.

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

## <a name="submit-a-run"></a>달리기 제출

[Run 개체는](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) 작업이 실행되는 동안 및 작업이 완료된 후 실행 기록에 대한 인터페이스를 제공합니다.

```Python
run = exp.submit(est)
run.wait_for_completion(show_output=True)
```

실행이 실행되면 다음 단계를 거칩니다.

- **준비**: Docker 이미지는 Chainer 추정기에 따라 만들어집니다. 이미지가 작업 영역의 컨테이너 레지스트리에 업로드되고 나중에 실행될 수 있도록 캐시됩니다. 로그는 실행 기록으로 스트리밍되며 진행 상황을 모니터링하기 위해 볼 수 있습니다.

- **크기 조정**: Batch AI 클러스터에 현재 사용 가능한 것보다 실행을 실행하는 데 더 많은 노드가 필요한 경우 클러스터가 확장하려고 시도합니다.

- **실행**중 : 스크립트 폴더의 모든 스크립트가 계산 대상에 업로드되고 데이터 저장소가 탑재되거나 복사되고 entry_script 실행됩니다. stdout 및 ./logs 폴더의 출력은 실행 기록으로 스트리밍되며 실행을 모니터링하는 데 사용할 수 있습니다.

- **사후 처리**: 실행의 ./outputs 폴더가 실행 기록에 복사됩니다.

## <a name="save-and-register-the-model"></a>모델 저장 및 등록

모델을 학습한 후에는 모델을 저장하고 작업 영역에 등록할 수 있습니다. 모델 등록을 사용하면 모델 관리 및 배포를 간소화하기 위해 작업 영역에 모델을 [저장하고](concept-model-management-and-deployment.md)버전이 정번할 수 있습니다.


모델 교육이 완료되면 다음 코드로 모델을 작업 영역에 등록합니다.  

```Python
model = run.register_model(model_name='chainer-dnn-mnist', model_path='outputs/model.npz')
```

> [!TIP]
> 방금 등록한 모델은 교육에 사용한 추정기에 관계없이 Azure Machine Learning의 다른 등록된 모델과 동일한 방식으로 배포됩니다. 배포 방법은 모델 등록에 대한 섹션을 포함하지만 이미 등록된 모델이 있으므로 배포에 대한 [계산 대상을 만드는](how-to-deploy-and-where.md#choose-a-compute-target) 데 직접 건너뛸 수 있습니다.

모델의 로컬 복사본을 다운로드할 수도 있습니다. 이 기능은 추가 모델 유효성 검사 작업을 로컬에서 수행하는 데 유용할 수 있습니다. 학습 스크립트에서 `chainer_mnist.py`saver 개체는 모델을 로컬 폴더(계산 대상에 로컬)로 유지합니다. 실행 개체를 사용하여 데이터스토어에서 복사본을 다운로드할 수 있습니다.

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

이 문서에서는 Azure 기계 학습에서 Chainer를 사용하여 딥 러닝, 신경망을 학습하고 등록했습니다. 모델을 배포하는 방법을 알아보려면 모델 [배포](how-to-deploy-and-where.md) 문서를 계속 계속 설명합니다.

* [하이퍼 매개 변수 조정](how-to-tune-hyperparameters.md)

* [학습 중에 실행 메트릭 추적](how-to-track-experiments.md)

* [Azure에서 분산 된 딥 러닝 교육에 대 한 참조 아키텍처 보기](/azure/architecture/reference-architectures/ai/training-deep-learning)
