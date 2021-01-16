---
title: 학습 실행 구성
titleSuffix: Azure Machine Learning
description: 다양 한 교육 환경 (계산 대상)에서 machine learning 모델을 학습 합니다. 학습 환경을 쉽게 전환할 수 있습니다.
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.date: 09/28/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, contperf-fy21q1
ms.openlocfilehash: 55e618a7e4e0d21f6d4afab270e257c26fa15634
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/16/2021
ms.locfileid: "98251117"
---
# <a name="configure-and-submit-training-runs"></a>학습 실행 구성 및 제출

이 문서에서는 모델을 학습 하는 Azure Machine Learning 실행을 구성 하 고 제출 하는 방법에 대해 알아봅니다. 코드 조각은 학습 스크립트 구성 및 제출의 주요 부분을 설명 합니다.  그런 다음 [예제 노트북](#notebooks) 중 하나를 사용 하 여 완전 한 종단 간 작업 예제를 찾습니다.

훈련 시 로컬 컴퓨터에서 시작 하 여 나중에 클라우드 기반 클러스터로 확장 하는 것이 일반적입니다. Azure Machine Learning를 사용 하면 학습 스크립트를 변경 하지 않고도 다양 한 계산 대상에서 스크립트를 실행할 수 있습니다.

**스크립트 실행 구성** 내에서 각 계산 대상에 대 한 환경을 정의 하기만 하면 됩니다.  그런 다음 다른 컴퓨팅 대상에서 학습 실험을 실행하려는 경우에 해당 컴퓨팅에 대한 실행 구성을 지정합니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독이 없는 경우 시작하기 전에 체험 계정을 만듭니다. 현재 [Azure Machine Learning의 무료 또는 유료 버전](https://aka.ms/AMLFree) 체험
* [Python 용 AZURE MACHINE LEARNING SDK](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py) (>= 1.13.0)
* [Azure Machine Learning 작업 영역](how-to-manage-workspace.md)`ws`
* 계산 대상인 `my_compute_target` 입니다.  [컴퓨팅 대상 만들기](how-to-create-attach-compute-studio.md) 

## <a name="whats-a-script-run-configuration"></a><a name="whats-a-run-configuration"></a>스크립트 실행 구성 이란?
[ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig?preserve-view=true&view=azure-ml-py) 은 실험의 일부로 학습 실행을 제출 하는 데 필요한 정보를 구성 하는 데 사용 됩니다.

ScriptRunConfig 개체를 사용 하 여 학습 실험을 제출 합니다.  이 개체는 다음을 포함합니다.

* **source_directory**: 학습 스크립트를 포함하는 원본 디렉터리
* **스크립트**: 실행할 학습 스크립트
* **compute_target**: 실행할 계산 대상
* **환경**: 스크립트를 실행할 때 사용할 환경
* 구성 가능한 몇 가지 옵션이 있습니다. 자세한 내용은 [참조 설명서](/python/api/azureml-core/azureml.core.scriptrunconfig?preserve-view=true&view=azure-ml-py) 를 참조 하세요.

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

>[!Note]
>Azure Databricks은 모델 학습을 위한 계산 대상으로 지원 되지 않습니다. Azure Databricks를 사용 하 여 데이터 준비 및 배포 작업을 수행할 수 있습니다. 

## <a name="create-an-environment"></a>환경 만들기
Azure Machine Learning [환경은](concept-environments.md) Machine Learning 교육이 발생 하는 환경을 캡슐화 하는 것입니다. 이는 교육 및 점수 매기기 스크립트와 관련 된 Python 패키지, Docker 이미지, 환경 변수 및 소프트웨어 설정을 지정 합니다. 또한 런타임 (Python, Spark 또는 Docker)을 지정 합니다.

사용자 고유의 환경을 정의 하거나 Azure ML 큐 레이트 환경을 사용할 수 있습니다. [큐 레이트 환경은](./how-to-use-environments.md#use-a-curated-environment) 기본적으로 작업 영역에서 사용할 수 있는 미리 정의 된 환경입니다. 이러한 환경은 캐시 된 Docker 이미지로 지원 되므로 실행 준비 비용이 줄어듭니다. 사용 가능한 큐 레이트 환경의 전체 목록은 [Azure Machine Learning 큐 레이트 환경](./resource-curated-environments.md) 을 참조 하세요.

원격 계산 대상의 경우 다음의 인기 있는 큐 레이트 환경 중 하나를 사용 하 여 시작할 수 있습니다.

```python
from azureml.core import Workspace, Environment

ws = Workspace.from_config()
myenv = Environment.get(workspace=ws, name="AzureML-Minimal")
```

환경에 대 한 자세한 내용 및 세부 정보는 [Azure Machine Learning에서 소프트웨어 환경 만들기 & 사용](how-to-use-environments.md)을 참조 하세요.
  
### <a name="local-compute-target"></a><a name="local"></a>로컬 계산 대상

계산 대상이 **로컬 컴퓨터** 이면 스크립트가 실행 되는 Python 환경에서 필요한 모든 패키지를 사용할 수 있는지 확인 해야 합니다.  `python.user_managed_dependencies`를 사용 하 여 현재 python 환경 또는 지정 된 경로에서 python을 사용 합니다.

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

# Set compute target
# Skip this if you are running on your local computer
script_run_config.run_config.target = my_compute_target
```

환경을 지정 하지 않으면 기본 환경이 생성 됩니다.

학습 스크립트에 전달할 명령줄 인수가 있는 경우 **`arguments`** ScriptRunConfig 생성자의 매개 변수를 통해 지정할 수 있습니다 (예: `arguments=['--arg1', arg1_val, '--arg2', arg2_val]` ).

실행에 허용 되는 기본 최대 시간을 재정의 하려는 경우 매개 변수를 통해이 작업을 수행할 수 있습니다 **`max_run_duration_seconds`** . 이 값 보다 오래 걸리면 시스템에서 자동으로 실행을 취소 하려고 합니다.

### <a name="specify-a-distributed-job-configuration"></a>분산 작업 구성 지정
분산 된 학습 작업을 실행 하려는 경우 매개 변수에 분산 된 작업별 구성을 제공 합니다 **`distributed_job_config`** . 지원 되는 구성 형식에는 [MpiConfiguration](/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?preserve-view=true&view=azure-ml-py), [TensorflowConfiguration](/python/api/azureml-core/azureml.core.runconfig.tensorflowconfiguration?preserve-view=true&view=azure-ml-py)및 [PyTorchConfiguration](/python/api/azureml-core/azureml.core.runconfig.pytorchconfiguration?preserve-view=true&view=azure-ml-py)가 있습니다. 

Distributed Horovod, TensorFlow 및 PyTorch 작업을 실행 하는 방법에 대 한 자세한 내용과 예는 다음을 참조 하세요.

* [TensorFlow 모델 학습](./how-to-train-tensorflow.md#distributed-training)
* [PyTorch 모델 학습](./how-to-train-pytorch.md#distributed-training)

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
> 두 개의 **특수 폴더** 인 *outputs* 및 *logs* 는 Azure Machine Learning에서 별도로 처리합니다. 학습하는 동안 루트 디렉터리(각각 `./outputs` 및 `./logs`) 기준의 *outputs* 및 *logs* 라는 폴더에 파일을 쓰면, 이러한 파일이 실행 기록에 자동으로 업로드되므로 실행이 완료되면 해당 파일에 액세스할 수 있습니다.
>
> 모델 파일, 검사점, 데이터 파일 또는 그려진 이미지와 같이 학습 중에 아티팩트를 만들려면 이러한 파일을 `./outputs` 폴더에 씁니다.
>
> 마찬가지로, 학습 실행의 로그는 `./logs` 폴더에 쓸 수 있습니다. Azure Machine Learning의 [TensorBoard 통합](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/tensorboard/export-run-history-to-tensorboard/export-run-history-to-tensorboard.ipynb)을 활용하려면 TensorBoard 로그를 이 폴더에 기록해야 합니다. 실행이 진행되는 동안 TensorBoard를 시작하고 이러한 로그를 스트림할 수 있습니다.  나중에 이전 실행의 로그를 복원할 수도 있습니다.
>
> 예를 들어 원격 학습을 실행한 후 *outputs* 폴더에 기록된 파일을 로컬 머신으로 다운로드하는 코드는 `run.download_file(name='outputs/my_output_file', output_file_path='my_destination_path')`입니다.

## <a name="git-tracking-and-integration"></a><a id="gitintegration"></a>Git 추적 및 통합

원본 디렉터리가 로컬 Git 리포지토리인 학습 실행을 시작하면 리포지토리에 대한 정보가 실행 기록에 저장됩니다. 자세한 내용은 [Azure Machine Learning에 대한 Git 통합](concept-train-model-git-integration.md)을 참조하세요.

## <a name="notebook-examples"></a><a name="notebooks"></a>Notebook 예제

다양 한 학습 시나리오에 대 한 실행 구성 예제는 이러한 노트북을 참조 하세요.
* [다양 한 계산 대상에 대 한 교육](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)
* [ML 프레임 워크를 사용한 교육](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks)
* [tutorials/img-classification-part1-training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/image-classification-mnist-data/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="troubleshooting"></a>문제 해결

* **실행 실패 `jwt.exceptions.DecodeError`**: 정확한 오류 메시지: `jwt.exceptions.DecodeError: It is required that you pass in a value for the "algorithms" argument when calling decode()` . 
    
    최신 버전의 azureml-코어로 업그레이드 하는 것이 `pip install -U azureml-core` 좋습니다.
    
    로컬 실행에 대해이 문제가 발생 하는 경우 실행을 시작 하는 환경에 설치 된 PyJWT의 버전을 확인 합니다. 지원 되는 버전의 PyJWT는 < 2.0.0입니다. 버전이 >= 2.0.0 인 경우 환경에서 PyJWT를 제거 합니다. PyJWT의 버전을 확인 하 고, 다음과 같이 올바른 버전을 제거 하 고 설치할 수 있습니다.
    1. 명령 셸을 시작 하 고 azureml-core가 설치 된 conda 환경을 활성화 합니다.
    2. 을 입력 `pip freeze` 하 고 검색 `PyJWT` 하는 경우 나열 된 버전 < 2.0.0 여야 합니다.
    3. 표시 된 버전이 지원 되는 버전이 아닌 경우 `pip uninstall PyJWT` 명령 셸에서 y를 입력 하 여 확인 합니다.
    4. `pip install 'PyJWT<2.0.0'`를 사용하여 설치
    
    사용자가 만든 환경을 실행 하 여 제출 하는 경우 해당 환경에서 최신 버전의 azureml-코어를 사용 하는 것이 좋습니다. 버전 >= 1.18.0 PyJWT < 2.0.0의 azureml가 이미 고정 되어 있습니다. 제출 하는 환경에서 azureml 1.18.0 < 버전을 사용 해야 하는 경우 pip 종속성에서 PyJWT < 2.0.0를 지정 해야 합니다.


 * **Moduleerrors (이름이 지정 된 모듈 없음)**: Azure ML에서 실험을 제출 하는 동안 moduleerrors를 실행 하는 경우 학습 스크립트는 패키지를 설치 하는 것으로 예상 하지만 추가 되지 않습니다. 패키지 이름을 제공 하 고 나면 Azure ML은 학습 실행에 사용 되는 환경에 패키지를 설치 합니다.

    추정를 사용 하 여 실험을 제출 하는 경우 `pip_packages` `conda_packages` 패키지를 설치 하려는 원본에 기반 하 여 평가기에서 또는 매개 변수를 통해 패키지 이름을 지정할 수 있습니다. 를 사용 하 여 모든 종속성이 포함 된 iisnode.yml 파일을 지정 `conda_dependencies_file` 하거나 매개 변수를 사용 하 여 txt 파일에 모든 pip 요구 사항을 나열할 수도 있습니다 `pip_requirements_file` . 평가기에서 사용 하는 기본 이미지를 재정의 하려는 고유한 Azure ML Environment 개체가 있는 경우 `environment` 평가기 생성자의 매개 변수를 통해 해당 환경을 지정할 수 있습니다.
    
    Azure ML에서 관리 하는 docker 이미지와 해당 콘텐츠는 [AzureML 컨테이너](https://github.com/Azure/AzureML-Containers)에서 볼 수 있습니다.
    프레임 워크 관련 종속성은 해당 프레임 워크 설명서에 나와 있습니다.
    *  [Chainer](/python/api/azureml-train-core/azureml.train.dnn.chainer?preserve-view=true&view=azure-ml-py#&preserve-view=trueremarks)
    * [PyTorch](/python/api/azureml-train-core/azureml.train.dnn.pytorch?preserve-view=true&view=azure-ml-py#&preserve-view=trueremarks)
    * [Tensorflow](/python/api/azureml-train-core/azureml.train.dnn.tensorflow?preserve-view=true&view=azure-ml-py#&preserve-view=trueremarks)
    *  [SKLearn](/python/api/azureml-train-core/azureml.train.sklearn.sklearn?preserve-view=true&view=azure-ml-py#&preserve-view=trueremarks)
    
    > [!Note]
    > 특정 패키지가 Azure 기계 학습에서 유지 관리 되는 이미지 및 환경에 추가할 수 있는 것으로 생각 되는 경우 [AzureML 컨테이너](https://github.com/Azure/AzureML-Containers)에서 GitHub 문제를 제기 하세요. 
 
* **Nameerror (이름이 정의 되지 않음), attributeerror (개체에 특성이 없음)**:이 예외는 학습 스크립트에서 제공 되어야 합니다. Azure Portal에서 로그 파일을 확인 하 여 지정 되지 않은 특정 이름 또는 특성 오류에 대 한 자세한 정보를 볼 수 있습니다. SDK에서를 사용 `run.get_details()` 하 여 오류 메시지를 확인할 수 있습니다. 또한 실행을 위해 생성 된 모든 로그 파일을 나열 합니다. 실행을 다시 전송 하기 전에 학습 스크립트를 확인 하 고 오류를 수정 하세요. 


* **실행 또는 실험 삭제**: 실험을 사용 하 [여 보관 하거나](/python/api/azureml-core/azureml.core.experiment%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truearchive--) "실험 보관" 단추를 통해 Azure Machine Learning studio 클라이언트의 실험 탭 보기에서 보관할 수 있습니다. 이 동작을 수행 하면 쿼리 및 뷰 목록에서 실험을 숨길 수 있지만 삭제 하지는 않습니다.

    개별 실험 또는 실행의 영구 삭제는 현재 지원되지 않습니다. 작업 영역 자산을 삭제 하는 방법에 대 한 자세한 내용은 [Machine Learning 서비스 작업 영역 데이터 내보내기 또는 삭제](how-to-export-delete-data.md)를 참조 하세요.

* **메트릭 문서가 너무 큼**: Azure Machine Learning 학습 실행에서 한 번에 기록할 수 있는 메트릭 개체 크기에 대 한 내부 제한이 있습니다. 목록 값 메트릭을 로깅할 때 "메트릭 문서가 너무 큼" 오류가 발생하면 목록을 더 작은 청크로 분할해보세요. 예를 들면 다음과 같습니다.

    ```python
    run.log_list("my metric name", my_metric[:N])
    run.log_list("my metric name", my_metric[N:])
    ```

    내부적으로 Azure ML은 동일한 메트릭 이름을 가진 블록을 연속된 목록에 연결합니다.

* **계산 대상을 시작 하** 는 데 시간이 오래 걸립니다. 계산 대상의 DOCKER 이미지가 ACR (Azure Container Registry)에서 로드 됩니다. 기본적으로 Azure Machine Learning는 *기본 서비스 계층을 사용* 하는 ACR을 만듭니다. 작업 영역에 대 한 ACR을 표준 또는 프리미엄 계층으로 변경 하면 이미지를 빌드하고 로드 하는 데 걸리는 시간을 줄일 수 있습니다. 자세한 내용은 [Azure Container Registry 서비스 계층](../container-registry/container-registry-skus.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

* [자습서: 모델 학습](tutorial-train-models-with-aml.md)은 모델 학습에 관리되는 컴퓨팅 대상을 사용합니다.
* [Scikit](how-to-train-scikit-learn.md), [TensorFlow](how-to-train-tensorflow.md)및 [PyTorch](how-to-train-pytorch.md)와 같은 특정 ML 프레임 워크를 사용 하 여 모델을 학습 하는 방법을 참조 하세요.
* [하이퍼 매개 변수를 효율적으로 튜닝](how-to-tune-hyperparameters.md)하여 보다 나은 모델을 빌드하는 방법을 알아봅니다.
* 모델을 학습했으면 [모델을 배포하는 방법 및 위치](how-to-deploy-and-where.md)를 알아봅니다.
* [ScriptRunConfig 클래스](/python/api/azureml-core/azureml.core.scriptrunconfig?preserve-view=true&view=azure-ml-py) SDK 참조를 봅니다.
* [Azure Virtual Networks에서 Azure Machine Learning 사용](./how-to-network-security-overview.md)
