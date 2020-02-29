---
title: TensorBoard로 실험 시각화
titleSuffix: Azure Machine Learning
description: TensorBoard를 시작 하 여 실험 실행 기록을 시각화 하 고 하이퍼 매개 변수 조정 및 재 학습을 위한 잠재적 영역을 식별 합니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: maxluk
ms.author: maxluk
ms.date: 02/27/2020
ms.openlocfilehash: b6b7e47acdbc5bd059e17e512731bd09c8580798
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/29/2020
ms.locfileid: "78195382"
---
# <a name="visualize-experiment-runs-and-metrics-with-tensorboard-and-azure-machine-learning"></a>TensorBoard 및 Azure Machine Learning를 사용 하 여 실험 실행 및 메트릭 시각화
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

이 문서에서는 기본 Azure Machine Learning SDK의 [`tensorboard` 패키지를](https://docs.microsoft.com/python/api/azureml-tensorboard/?view=azure-ml-py) 사용 하 여 TensorBoard에서 실험 실행과 메트릭을 보는 방법에 대해 알아봅니다. 실험 실행을 검사 한 후에는 machine learning 모델을 더 잘 조정 하 고 다시 학습 수 있습니다.

[TensorBoard](https://www.tensorflow.org/tensorboard/r1/overview) 은 실험 구조와 성능을 검사 하 고 이해 하기 위한 웹 응용 프로그램 모음입니다.

Azure Machine Learning 실험을 통해 TensorBoard를 시작 하는 방법은 실험 유형에 따라 달라 집니다.
+ 실험에서 TensorBoard에서 사용할 수 있는 로그 파일 (예: PyTorch, 체 이너 및 TensorFlow 실험)을 기본적으로 출력 하는 경우 실험의 실행 기록에서 [직접 TensorBoard를 시작할](#direct) 수 있습니다. 

+ TensorBoard 사용 파일을 기본적으로 출력 하지 않는 실험 (예: Scikit-학습 또는 Azure Machine Learning 실험)의 경우 [`export_to_tensorboard()` 메서드](#export) 를 사용 하 여 실행 기록을 TensorBoard logs로 내보내고 여기에서 TensorBoard를 실행 합니다. 

> [!TIP]
> 이 문서의 정보는 주로 모델 학습 프로세스를 모니터링 하려는 데이터 과학자 및 개발자를 위한 것입니다. 할당량, 완료 된 학습 실행 또는 완료 된 모델 배포와 같이 Azure Machine learning의 리소스 사용 및 이벤트를 모니터링 하는 데 관심이 있는 관리자는 [모니터링 Azure Machine Learning](monitor-azure-machine-learning.md)을 참조 하세요.

## <a name="prerequisites"></a>필수 조건

* TensorBoard을 시작 하 고 실험 실행 기록을 보려면 실험에서 이전에 로깅을 사용 하도록 설정 하 여 해당 메트릭과 성능을 추적 해야 합니다.  

* 이 문서의 코드는 다음 환경 중 하나에서 실행할 수 있습니다. 

    * Azure Machine Learning 계산 인스턴스-다운로드 또는 설치 필요 없음

        * [자습서: 설치 환경 및 작업 영역](tutorial-1st-experiment-sdk-setup.md) 을 완료 하 여 SDK 및 샘플 리포지토리를 사용 하 여 미리 로드 한 전용 노트북 서버를 만듭니다.

        * 노트북 서버의 samples 폴더에서 다음 디렉터리로 이동 하 여 두 개의 완료 및 확장 된 노트북을 찾습니다.
            * **사용 방법-azureml > 교육-심층 학습 > 내보내기-실행-기록-tensorboard > 내보내기-실행-기록-tensorboard.**

            * **사용 방법-azureml > 트랙-및 모니터-실험 > tensorboard**

    * 사용자 고유의 Juptyer 노트북 서버
       * `tensorboard` 추가 된 [AZURE MACHINE LEARNING SDK를 설치](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) 합니다.
        * [Azure Machine Learning 작업 영역을 만듭니다](how-to-manage-workspace.md).  
        * [작업 영역 구성 파일을 만듭니다](how-to-configure-environment.md#workspace).
  
<a name="direct"></a>

## <a name="option-1-directly-view-run-history-in-tensorboard"></a>옵션 1: TensorBoard에서 실행 기록 직접 보기

이 옵션은 PyTorch, 체 이너 및 TensorFlow 실험 같이 TensorBoard에서 사용할 수 있는 로그 파일을 고유 하 게 출력 하는 실험에 사용할 수 있습니다. 실험의 사례가 아니면 [`export_to_tensorboard()` 메서드를](#export) 대신 사용 합니다.

다음 예제 코드에서는 TensorFlow의 리포지토리에서의 리포지토리에서 [Mnist demo 실험](https://raw.githubusercontent.com/tensorflow/tensorflow/r1.8/tensorflow/examples/tutorials/mnist/mnist_with_summaries.py) 을 사용 하 여 계산을 Azure Machine Learning 합니다. 그런 다음 SDK의 사용자 지정 [TensorFlow 평가기](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)를 사용 하 여 모델을 학습 한 다음이 TensorFlow 실험, 즉 기본적으로 TensorBoard 이벤트 파일을 출력 하는 실험에 대해 TensorBoard를 시작 합니다.

### <a name="set-experiment-name-and-create-project-folder"></a>실험 이름 설정 및 프로젝트 폴더 만들기

여기서는 실험의 이름을로 하 고 해당 폴더를 만듭니다. 
 
```python
from os import path, makedirs
experiment_name = 'tensorboard-demo'

# experiment folder
exp_dir = './sample_projects/' + experiment_name

if not path.exists(exp_dir):
    makedirs(exp_dir)

```

### <a name="download-tensorflow-demo-experiment-code"></a>TensorFlow demo 실험 코드 다운로드

TensorFlow의 리포지토리는 광범위 한 TensorBoard 계측을 포함 하는 MNIST 데모를 포함 합니다. Azure Machine Learning 사용 하기 위해이 데모 코드를 변경할 필요는 없습니다. 다음 코드에서는 MNIST 코드를 다운로드 하 고 새로 만든 실험 폴더에 저장 합니다.

```python
import requests
import os

tf_code = requests.get("https://raw.githubusercontent.com/tensorflow/tensorflow/r1.8/tensorflow/examples/tutorials/mnist/mnist_with_summaries.py")
with open(os.path.join(exp_dir, "mnist_with_summaries.py"), "w") as file:
    file.write(tf_code.text)
```
MNIST 코드 파일 mnist_with_summaries py에서 `tf.summary.scalar()`, `tf.summary.histogram()`, `tf.summary.FileWriter()` 등을 호출 하는 줄이 있는지 확인 합니다. 이러한 메서드는 실험의 주요 메트릭을 실행 기록으로 그룹화, 기록 및 태그 합니다. `tf.summary.FileWriter()`는 로깅된 실험 메트릭의 데이터를 직렬화 하므로 TensorBoard에서 시각화를 생성할 수 있도록 하는 것이 특히 중요 합니다.

 ### <a name="configure-experiment"></a>실험 구성

다음에서는 실험을 구성 하 고 로그 및 데이터에 대 한 디렉터리를 설정 합니다. 이러한 로그는 나중에 TensorBoard 액세스 하는 아티팩트 서비스에 업로드 됩니다.

>[!Note]
> 이 TensorFlow 예제에서는 로컬 컴퓨터에 TensorFlow를 설치 해야 합니다. 또한 TensorBoard 모듈 (즉, TensorFlow에 포함 된 모듈)은 로컬 컴퓨터가 TensorBoard를 실행 하는 것 처럼이 노트북의 커널에 액세스할 수 있어야 합니다.

```Python
import azureml.core
from azureml.core import Workspace
from azureml.core import Experiment

ws = Workspace.from_config()

# create directories for experiment logs and dataset
logs_dir = os.path.join(os.curdir, "logs")
data_dir = os.path.abspath(os.path.join(os.curdir, "mnist_data"))

if not path.exists(data_dir):
    makedirs(data_dir)

os.environ["TEST_TMPDIR"] = data_dir

# Writing logs to ./logs results in their being uploaded to Artifact Service,
# and thus, made accessible to our TensorBoard instance.
arguments_list = ["--log_dir", logs_dir]

# Create an experiment
exp = Experiment(ws, experiment_name)
```

### <a name="create-a-cluster-for-your-experiment"></a>실험을 위한 클러스터 만들기
이 실험을 위해 AmlCompute 클러스터를 만들기는 하지만 모든 환경에서 실험을 만들 수 있으며 실험 실행 기록에 대해 TensorBoard를 시작할 수 있습니다. 

```Python
from azureml.core.compute import ComputeTarget, AmlCompute

cluster_name = "cpucluster"

cts = ws.compute_targets
found = False
if cluster_name in cts and cts[cluster_name].type == 'AmlCompute':
   found = True
   print('Found existing compute target.')
   compute_target = cts[cluster_name]
if not found:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2', 
                                                           max_nodes=4)

    # create the cluster
    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

compute_target.wait_for_completion(show_output=True, min_node_count=None)

# use get_status() to get a detailed status for the current cluster. 
# print(compute_target.get_status().serialize())
```

### <a name="submit-run-with-tensorflow-estimator"></a>TensorFlow 평가기를 사용 하 여 실행 제출

TensorFlow 평가기는 계산 대상에서 TensorFlow 학습 작업을 시작 하는 간단한 방법을 제공 합니다. 이 클래스는 프레임 워크를 지 원하는 데 사용할 수 있는 제네릭 [`estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) 클래스를 통해 구현 됩니다. 제네릭 평가기을 사용한 학습 모델에 대 한 자세한 내용은 [평가기를 사용 하 여 Azure Machine Learning를](how-to-train-ml-models.md) 사용 하 여 모델 학습을 참조 하세요.

```Python
from azureml.train.dnn import TensorFlow
script_params = {"--log_dir": "./logs"}

tf_estimator = TensorFlow(source_directory=exp_dir,
                          compute_target=compute_target,
                          entry_script='mnist_with_summaries.py',
                          script_params=script_params)

run = exp.submit(tf_estimator)
```

### <a name="launch-tensorboard"></a>TensorBoard 시작

실행 중 또는 완료 된 후 TensorBoard를 시작할 수 있습니다. 다음에는 `run`에 로드 된 실험 실행 기록을 사용 하는 `tb`TensorBoard 개체 인스턴스를 만들고 `start()` 메서드를 사용 하 여 TensorBoard를 시작 합니다. 
  
[TensorBoard 생성자](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard?view=azure-ml-py) 는 실행 배열을 사용 하므로이를 단일 요소 배열로 전달 해야 합니다.

```python
from azureml.tensorboard import Tensorboard

tb = Tensorboard([run])

# If successful, start() returns a string with the URI of the instance.
tb.start()

# After your job completes, be sure to stop() the streaming otherwise it will continue to run. 
tb.stop()
```

>[!Note]
 이 예제에서는 TensorFlow을 사용 했지만 PyTorch 또는 체 이너 모델에서 TensorBoard를 쉽게 사용할 수 있습니다. TensorFlow는 TensorBoard를 실행 하는 컴퓨터에서 사용할 수 있어야 하지만 PyTorch 또는 체 이너 계산을 수행 하는 컴퓨터에서는 필요 하지 않습니다. 


<a name="export"></a>

## <a name="option-2-export-history-as-log-to-view-in-tensorboard"></a>옵션 2: TensorBoard에서 보려는 기록을 로그로 내보내기

다음 코드는 샘플 실험을 설정 하 고, Azure Machine Learning 실행 기록 Api를 사용 하 여 로깅 프로세스를 시작 하 고, 실험 실행 기록을 시각화를 위해 TensorBoard에서 사용 가능한 로그로 내보냅니다. 

### <a name="set-up-experiment"></a>실험 설정

다음 코드는 새 실험을 설정 하 고 실행 디렉터리의 이름을 `root_run`합니다. 

```python
from azureml.core import Workspace, Experiment
import azuremml.core

# set experiment name and run name
ws = Workspace.from_config()
experiment_name = 'export-to-tensorboard'
exp = Experiment(ws, experiment_name)
root_run = exp.start_logging()
```

여기서는 scikit와 함께 제공 되는 기본 제공 작은 데이터 집합 인 당뇨병 데이터 집합을 로드 하 고 테스트 및 학습 집합으로 분할 합니다.

```Python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
X, y = load_diabetes(return_X_y=True)
columns = ['age', 'gender', 'bmi', 'bp', 's1', 's2', 's3', 's4', 's5', 's6']
x_train, x_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=0)
data = {
    "train":{"x":x_train, "y":y_train},        
    "test":{"x":x_test, "y":y_test}
}
```

### <a name="run-experiment-and-log-metrics"></a>실험 및 로그 메트릭 실행

이 코드의 경우 실행 기록에서 선형 회귀 모델 및 로그 키 메트릭, 알파 계수, `alpha`및 평균 제곱 오차 (`mse`)를 학습 합니다.

```Python
from tqdm import tqdm
alphas = [.1, .2, .3, .4, .5, .6 , .7]
# try a bunch of alpha values in a Linear Regression (aka Ridge regression) mode
for alpha in tqdm(alphas):
  # create child runs and fit lines for the resulting models
  with root_run.child_run("alpha" + str(alpha)) as run
 
   reg = Ridge(alpha=alpha)
   reg.fit(data["train"]["x"], data["train"]["y"])    
 
   preds = reg.predict(data["test"]["x"])
   mse = mean_squared_error(preds, data["test"]["y"])
   # End train and eval

# log alpha, mean_squared_error and feature names in run history
   root_run.log("alpha", alpha)
   root_run.log("mse", mse)
```

### <a name="export-runs-to-tensorboard"></a>TensorBoard로 실행 내보내기

SDK의 [export_to_tensorboard ()](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.export?view=azure-ml-py) 메서드를 사용 하 여 Azure machine learning 실험의 실행 기록을 tensorboard를 통해 볼 수 있도록 tensorboard 로그로 내보낼 수 있습니다.  

다음 코드에서는 현재 작업 디렉터리에 `logdir` 폴더를 만듭니다. 이 폴더에는 `root_run`에서 실험 실행 기록 및 로그를 내보낸 후 해당 실행을 완료 된 것으로 표시 합니다. 

```Python
from azureml.tensorboard.export import export_to_tensorboard
import os

logdir = 'exportedTBlogs'
log_path = os.path.join(os.getcwd(), logdir)
try:
    os.stat(log_path)
except os.error:
    os.mkdir(log_path)
print(logdir)

# export run history for the project
export_to_tensorboard(root_run, logdir)

root_run.complete()
```

>[!Note]
 실행 `export_to_tensorboard(run_name, logdir)` 이름을 지정 하 여 특정 실행을 TensorBoard로 내보낼 수도 있습니다.

### <a name="start-and-stop-tensorboard"></a>TensorBoard 시작 및 중지
이 실험의 실행 기록을 내보내면 [start ()](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard?view=azure-ml-py#start-start-browser-false-) 메서드를 사용 하 여 TensorBoard를 시작할 수 있습니다. 

```Python
from azureml.tensorboard import Tensorboard

# The TensorBoard constructor takes an array of runs, so be sure and pass it in as a single-element array here
tb = Tensorboard([], local_root=logdir, port=6006)

# If successful, start() returns a string with the URI of the instance.
tb.start()
```

완료 되 면 TensorBoard 개체의 [stop ()](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard?view=azure-ml-py#stop--) 메서드를 호출 해야 합니다. 그렇지 않으면 노트북 커널을 종료할 때까지 TensorBoard가 계속 실행 됩니다. 

```python
tb.stop()
```

## <a name="next-steps"></a>다음 단계

이 방법에서는 두 개의 실험을 만들었으며 실행 기록에 대해 TensorBoard를 시작 하 여 잠재적인 튜닝 및 재 학습 영역을 파악 하는 방법을 배웠습니다. 

* 모델에 만족 하는 경우 [모델을 배포 하는 방법](how-to-deploy-and-where.md) 문서를 참조 하세요. 
* 하이퍼 [매개 변수 튜닝](how-to-tune-hyperparameters.md)에 대해 자세히 알아보세요. 
