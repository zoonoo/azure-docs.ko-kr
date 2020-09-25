---
title: 학습 실행 구성
titleSuffix: Azure Machine Learning
description: 다양 한 교육 환경 (계산 대상)에서 machine learning 모델을 학습 합니다. 학습 환경을 쉽게 전환할 수 있습니다. 로컬로 학습을 시작합니다. 규모 확장이 필요한 경우 클라우드 기반 컴퓨팅 대상으로 전환합니다.
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.date: 09/25/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, contperfq1
ms.openlocfilehash: f93b6ab43e1dbf9230c92d22f8fb22ca48eb720e
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91275764"
---
# <a name="configure-and-submit-training-runs"></a>학습 실행 구성 및 제출

이 문서에서는 모델을 학습 하는 Azure Machine Learning 실행을 구성 하 고 제출 하는 방법에 대해 알아봅니다.

훈련 시 로컬 컴퓨터에서 시작 하 여 나중에 클라우드 기반 클러스터로 확장 하는 것이 일반적입니다. Azure Machine Learning를 사용 하면 학습 스크립트를 변경 하지 않고도 다양 한 계산 대상에서 스크립트를 실행할 수 있습니다.

**스크립트 실행 구성**내에서 각 계산 대상에 대 한 환경을 정의 하기만 하면 됩니다.  그런 다음 다른 컴퓨팅 대상에서 학습 실험을 실행하려는 경우에 해당 컴퓨팅에 대한 실행 구성을 지정합니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독이 없는 경우 시작하기 전에 체험 계정을 만듭니다. 현재 [Azure Machine Learning의 무료 또는 유료 버전](https://aka.ms/AMLFree) 체험
* [Python 용 AZURE MACHINE LEARNING SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true) (>= 1.13.0)
* [Azure Machine Learning 작업 영역](how-to-manage-workspace.md)`ws`
* 계산 대상인 `my_compute_target` 입니다.  다음을 사용 하 여 계산 대상을 만듭니다.
  * [Python SDK](how-to-create-attach-compute-sdk.md) 
  * [Azure Machine Learning Studio](how-to-create-attach-compute-studio.md)

## <a name="whats-a-script-run-configuration"></a><a name="whats-a-run-configuration"></a>스크립트 실행 구성 이란?
[ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py&preserve-view=true) 은 실험의 일부로 학습 실행을 제출 하는 데 필요한 정보를 구성 하는 데 사용 됩니다.

ScriptRunConfig 개체를 사용 하 여 학습 실험을 제출 합니다.  이 개체는 다음을 포함합니다.

* **source_directory**: 학습 스크립트를 포함하는 원본 디렉터리
* **스크립트**: 실행할 학습 스크립트
* **compute_target**: 실행할 계산 대상
* **환경**: 스크립트를 실행할 때 사용할 환경
* 구성 가능한 몇 가지 옵션이 있습니다. 자세한 내용은 [참조 설명서](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py&preserve-view=true) 를 참조 하세요.

## <a name="train-your-model"></a><a id="submit"></a>모델 학습

학습 실행을 제출하는 코드 패턴은 모든 유형의 컴퓨팅 대상에서 동일합니다.

1. 실행할 실험 만들기
1. 스크립트가 실행 되는 환경 만들기
1. 계산 대상 및 환경을 지정 하는 ScriptRunConfig를 만듭니다.
1. 실행 제출
1. 실행이 완료 될 때까지 대기

또는

* [하이퍼 매개 변수 튜닝](how-to-tune-hyperparameters.md)에 대한 HyperDrive 실행을 제출합니다.
* [VS Code 확장](tutorial-train-deploy-image-classification-model-vscode.md#train-the-model)을 통해 실험을 제출합니다.

## <a name="create-an-experiment"></a>실험 만들기

작업 영역에서 실험을 만듭니다.

```python
from azureml.core import Experiment

experiment_name = 'my_experiment'
experiment = Experiment(workspace=ws, name=experiment_name)
```

## <a name="select-a-compute-target"></a>계산 대상 선택

학습 스크립트를 실행할 계산 대상을 선택 합니다. ScriptRunConfig에 계산 대상이 지정 되어 있지 않거나 이면 `compute_target='local'` AZURE ML이 스크립트를 로컬로 실행 합니다. 

이 문서의 예제 코드에서는 `my_compute_target` "전제 조건" 섹션에서 계산 대상을 이미 만들었다고 가정 합니다.

## <a name="create-an-environment"></a>환경 만들기
Azure Machine Learning [환경은](concept-environments.md) Machine Learning 교육이 발생 하는 환경을 캡슐화 하는 것입니다. 이는 교육 및 점수 매기기 스크립트와 관련 된 Python 패키지, Docker 이미지, 환경 변수 및 소프트웨어 설정을 지정 합니다. 또한 런타임 (Python, Spark 또는 Docker)을 지정 합니다.

사용자 고유의 환경을 정의 하거나 Azure ML 큐 레이트 환경을 사용할 수 있습니다. [큐 레이트 환경은](https://docs.microsoft.com/azure/machine-learning/how-to-use-environments#use-a-curated-environment) 기본적으로 작업 영역에서 사용할 수 있는 미리 정의 된 환경입니다. 이러한 환경은 캐시 된 Docker 이미지로 지원 되므로 실행 준비 비용이 줄어듭니다. 사용 가능한 큐 레이트 환경의 전체 목록은 [Azure Machine Learning 큐 레이트 환경](https://docs.microsoft.com/azure/machine-learning/resource-curated-environments) 을 참조 하세요.

원격 계산 대상의 경우 다음의 인기 있는 큐 레이트 환경 중 하나를 사용 하 여 시작할 수 있습니다.

```python
from azureml.core import Workspace, Environment

ws = Workspace.from_config()
myenv = Environment.get(workspace=ws, name="AzureML-Minimal")
```

환경에 대 한 자세한 내용 및 세부 정보는 [Azure Machine Learning에서 소프트웨어 환경 만들기 & 사용](how-to-use-environments.md)을 참조 하세요.
  
### <a name="local-compute-target"></a>로컬 컴퓨팅 대상

계산 대상이 **로컬 컴퓨터**이면 스크립트가 실행 되는 Python 환경에서 필요한 모든 패키지를 사용할 수 있는지 확인 해야 합니다.  `python.user_managed_dependencies`를 사용 하 여 현재 python 환경 또는 지정 된 경로에서 python을 사용 합니다.

```python
from azureml.core import Environment

myenv = Environment("user-managed-env")
myenv.python.user_managed_dependencies = True

# You can choose a specific Python environment by pointing to a Python path 
# myenv.python.interpreter_path = '/home/johndoe/miniconda3/envs/myenv/bin/python'
```

## <a name="create-the-script-run-configuration"></a>스크립트 실행 구성 만들기

이제 계산 대상 ( `my_compute_target` ) 및 환경 ()이 있으므로 `myenv` , `train.py` 디렉터리에 있는 학습 스크립트 ()를 실행 하는 스크립트 실행 구성을 만듭니다 `project_folder` .

```python
from azureml.core import ScriptRunConfig

src = ScriptRunConfig(source_directory=project_folder,
                      script='train.py',
                      compute_target=my_compute_target,
                      environment=myenv)
```

환경을 지정 하지 않으면 기본 환경이 생성 됩니다.

학습 스크립트에 전달할 명령줄 인수가 있는 경우 **`arguments`** ScriptRunConfig 생성자의 매개 변수를 통해 지정할 수 있습니다 (예: `arguments=['--arg1', arg1_val, '--arg2', arg2_val]` ).

실행에 허용 되는 기본 최대 시간을 재정의 하려는 경우 매개 변수를 통해이 작업을 수행할 수 있습니다 **`max_run_duration_seconds`** . 이 값 보다 오래 걸리면 시스템에서 자동으로 실행을 취소 하려고 합니다.

### <a name="specify-a-distributed-job-configuration"></a>분산 작업 구성 지정
분산 된 학습 작업을 실행 하려는 경우 매개 변수에 분산 된 작업별 구성을 제공 합니다 **`distributed_job_config`** . 지원 되는 구성 형식에는 [MpiConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py&preserve-view=true), [TensorflowConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.tensorflowconfiguration?view=azure-ml-py&preserve-view=true)가 포함 됩니다. 

Distributed Horovod, TensorFlow 및 PyTorch 작업을 실행 하는 방법에 대 한 자세한 내용과 예는 다음을 참조 하세요.

* [TensorFlow 모델 학습](https://docs.microsoft.com/azure/machine-learning/how-to-train-tensorflow#distributed-training)
* [PyTorch 모델 학습](https://docs.microsoft.com/azure/machine-learning/how-to-train-pytorch#distributed-training)

## <a name="submit-the-experiment"></a>실험 제출

```python
run = experiment.submit(config=src)
run.wait_for_completion(show_output=True)
```

> [!IMPORTANT]
> 학습 실행을 제출하면 학습 스크립트가 포함된 디렉터리의 스냅샷이 생성되어 컴퓨팅 대상으로 전송됩니다. 또한 작업 영역에 실험의 일부로 저장됩니다. 파일을 변경하고 실행을 다시 제출하면 변경된 파일만 업로드됩니다.
>
> [!INCLUDE [amlinclude-info](../../includes/machine-learning-amlignore-gitignore.md)]
> 
> 스냅숏에 대 한 자세한 내용은 [스냅숏](concept-azure-machine-learning-architecture.md#snapshots)을 참조 하십시오.

> [!IMPORTANT]
> 두 개의 **특수 폴더**인 *outputs* 및 *logs*는 Azure Machine Learning에서 별도로 처리합니다. 학습하는 동안 루트 디렉터리(각각 `./outputs` 및 `./logs`) 기준의 *outputs* 및 *logs*라는 폴더에 파일을 쓰면, 이러한 파일이 실행 기록에 자동으로 업로드되므로 실행이 완료되면 해당 파일에 액세스할 수 있습니다.
>
> 모델 파일, 검사점, 데이터 파일 또는 그려진 이미지와 같이 학습 중에 아티팩트를 만들려면 이러한 파일을 `./outputs` 폴더에 씁니다.
>
> 마찬가지로, 학습 실행의 로그는 `./logs` 폴더에 쓸 수 있습니다. Azure Machine Learning의 [TensorBoard 통합](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/export-run-history-to-tensorboard/export-run-history-to-tensorboard.ipynb)을 활용하려면 TensorBoard 로그를 이 폴더에 기록해야 합니다. 실행이 진행되는 동안 TensorBoard를 시작하고 이러한 로그를 스트림할 수 있습니다.  나중에 이전 실행의 로그를 복원할 수도 있습니다.
>
> 예를 들어 원격 학습을 실행한 후 *outputs* 폴더에 기록된 파일을 로컬 머신으로 다운로드하는 코드는 `run.download_file(name='outputs/my_output_file', output_file_path='my_destination_path')`입니다.

## <a name="git-tracking-and-integration"></a><a id="gitintegration"></a>Git 추적 및 통합

원본 디렉터리가 로컬 Git 리포지토리인 학습 실행을 시작하면 리포지토리에 대한 정보가 실행 기록에 저장됩니다. 자세한 내용은 [Azure Machine Learning에 대한 Git 통합](concept-train-model-git-integration.md)을 참조하세요.

## <a name="notebook-examples"></a>Notebook 예제

다양 한 학습 시나리오에 대 한 실행 구성 예제는 이러한 노트북을 참조 하세요.
* [how-to-use-azureml/training](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)
* [how-to-use-azureml/ml-frameworks](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks)
* [tutorials/img-classification-part1-training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/image-classification-mnist-data/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>다음 단계

* [자습서: 모델 학습](tutorial-train-models-with-aml.md)은 모델 학습에 관리되는 컴퓨팅 대상을 사용합니다.
* [Scikit](how-to-train-scikit-learn.md), [TensorFlow](how-to-train-tensorflow.md)및 [PyTorch](how-to-train-pytorch.md)와 같은 특정 ML 프레임 워크를 사용 하 여 모델을 학습 하는 방법을 참조 하세요.
* [하이퍼 매개 변수를 효율적으로 튜닝](how-to-tune-hyperparameters.md)하여 보다 나은 모델을 빌드하는 방법을 알아봅니다.
* 모델을 학습했으면 [모델을 배포하는 방법 및 위치](how-to-deploy-and-where.md)를 알아봅니다.
* [ScriptRunConfig 클래스](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py&preserve-view=true) SDK 참조를 봅니다.
* [Azure Virtual Networks에서 Azure Machine Learning 사용](how-to-enable-virtual-network.md)
