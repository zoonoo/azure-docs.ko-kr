---
title: TensorBoard로 실험 시각화
titleSuffix: Azure Machine Learning
description: TensorBoard를 실행하여 실험 실행 기록을 시각화하고 하이퍼매개 변수 튜닝 및 재교육을 위한 잠재적 영역을 식별합니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: maxluk
ms.author: maxluk
ms.date: 02/27/2020
ms.openlocfilehash: b6b7e47acdbc5bd059e17e512731bd09c8580798
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78195382"
---
# <a name="visualize-experiment-runs-and-metrics-with-tensorboard-and-azure-machine-learning"></a>TensorBoard 및 Azure 기계 학습을 통해 실험 실행 및 메트릭 시각화
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

이 문서에서는 기본 Azure 기계 학습 SDK의 [ `tensorboard` 패키지를](https://docs.microsoft.com/python/api/azureml-tensorboard/?view=azure-ml-py) 사용하여 TensorBoard에서 실험 실행 및 메트릭을 보는 방법을 배웁니다. 실험 실행을 검사한 후에는 기계 학습 모델을 더 잘 조정하고 다시 학습할 수 있습니다.

[TensorBoard는](https://www.tensorflow.org/tensorboard/r1/overview) 실험 구조와 성능을 검사하고 이해하기 위한 웹 응용 프로그램 모음입니다.

Azure 기계 학습 실험을 통해 TensorBoard를 시작하는 방법은 실험 유형에 따라 다릅니다.
+ 실험에서 PyTorch, Chainer 및 TensorFlow 실험과 같이 텐서보드에서 사용할 수 있는 로그 파일을 기본적으로 출력하는 경우 실험의 실행 기록에서 [직접 텐서보드를 시작할](#direct) 수 있습니다. 

+ Scikit-learn 또는 Azure 기계 학습 실험과 같이 기본적으로 TensorBoard 소모품 파일을 출력하지 않는 실험의 경우 [이 `export_to_tensorboard()` 방법을](#export) 사용하여 실행 기록을 텐서보드 로그로 내보내고 거기에서 TensorBoard를 시작합니다. 

> [!TIP]
> 이 문서의 정보는 주로 모델 학습 프로세스를 모니터링하려는 데이터 과학자 및 개발자를 위한 것입니다. 할당량, 완료된 교육 실행 또는 완료된 모델 배포와 같은 Azure Machine 학습의 리소스 사용량 및 이벤트를 모니터링하는 데 관심이 있는 관리자는 [Azure Machine Learning 모니터링을](monitor-azure-machine-learning.md)참조하십시오.

## <a name="prerequisites"></a>사전 요구 사항

* TensorBoard를 시작하고 실험 실행 기록을 보려면 실험에서 메트릭 및 실적을 추적하기 위해 이전에 로깅을 사용하도록 설정해야 합니다.  

* 이 문서의 코드는 다음 환경 중 하나에서 실행할 수 있습니다. 

    * Azure 기계 학습 계산 인스턴스 - 다운로드 또는 설치 필요 없음

        * 자습서 [완료: 설정 환경 및 작업 영역을](tutorial-1st-experiment-sdk-setup.md) 설정하여 SDK 및 샘플 리포지토리로 미리 로드된 전용 노트북 서버를 만듭니다.

        * 노트북 서버의 샘플 폴더에서 다음 디렉터리로 이동하여 완료되고 확장된 두 개의 전자 필기장을 찾습니다.
            * **사용 방법 azureml > 교육 -딥 러닝 > 수출 실행 기록 - 텐서 보드 > 수출 실행 기록 - 텐서 보드로 수출 실행 기록 - 텐서 보드.ipynb**

            * **텐서보드> 트랙 및 모니터 실험> 사용 방법 azureml**

    * 나만의 Juptyer 노트북 서버
       * 추가 와 함께 Azure 기계 `tensorboard` 학습 [SDK를 설치](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)
        * [Azure 기계 학습 작업 영역을 만듭니다.](how-to-manage-workspace.md)  
        * [작업 영역 구성 파일 만들기](how-to-configure-environment.md#workspace).
  
<a name="direct"></a>

## <a name="option-1-directly-view-run-history-in-tensorboard"></a>옵션 1: 텐서보드에서 실행 기록을 직접 볼 수 있습니다.

이 옵션은 PyTorch, Chainer 및 TensorFlow 실험과 같이 텐서보드에서 소모가능한 로그 파일을 기본적으로 출력하는 실험에서 작동합니다. 실험의 경우 그렇지 않은 경우 대신 [메서드를 `export_to_tensorboard()` ](#export) 사용합니다.

다음 예제 코드는 원격 계산 대상인 Azure 기계 학습 계산에서 TensorFlow의 리포지토리에서 [MNIST 데모 실험을](https://raw.githubusercontent.com/tensorflow/tensorflow/r1.8/tensorflow/examples/tutorials/mnist/mnist_with_summaries.py) 사용합니다. 다음으로, 우리는 SDK의 사용자 정의 [TensorFlow 추정기와](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)함께 모델을 훈련하고이 텐서 플로우 실험, 즉, 기본적으로 텐서 보드 이벤트 파일을 출력 하는 실험에 대 한 텐서 보드를 시작 합니다.

### <a name="set-experiment-name-and-create-project-folder"></a>실험 이름 설정 및 프로젝트 폴더 만들기

여기서는 실험의 이름을 지정하고 해당 폴더를 만듭니다. 
 
```python
from os import path, makedirs
experiment_name = 'tensorboard-demo'

# experiment folder
exp_dir = './sample_projects/' + experiment_name

if not path.exists(exp_dir):
    makedirs(exp_dir)

```

### <a name="download-tensorflow-demo-experiment-code"></a>텐서플로우 데모 실험 코드 다운로드

텐서플로우의 리포지토리에는 광범위한 텐서보드 계측기가 있는 MNIST 데모가 있습니다. Azure 기계 학습에서 작동하도록 이 데모의 코드를 변경할 필요도 없으며 변경할 필요도 없습니다. 다음 코드에서는 MNIST 코드를 다운로드하여 새로 만든 실험 폴더에 저장합니다.

```python
import requests
import os

tf_code = requests.get("https://raw.githubusercontent.com/tensorflow/tensorflow/r1.8/tensorflow/examples/tutorials/mnist/mnist_with_summaries.py")
with open(os.path.join(exp_dir, "mnist_with_summaries.py"), "w") as file:
    file.write(tf_code.text)
```
MNIST 코드 파일 전체에서 mnist_with_summaries.py, 호출 `tf.summary.scalar()`하는 `tf.summary.histogram()`줄이 있음을 알 수 있습니다. `tf.summary.FileWriter()` 이러한 메서드는 실험의 주요 메트릭을 실행 기록으로 그룹화, 로그 및 태그를 연결합니다. 이는 `tf.summary.FileWriter()` 텐서보드가 시각화를 생성할 수 있도록 기록된 실험 메트릭의 데이터를 직렬화하기 때문에 특히 중요합니다.

 ### <a name="configure-experiment"></a>실험 구성

다음에서는 실험을 구성하고 로그 및 데이터에 대한 디렉터리를 설정합니다. 이러한 로그는 나중에 텐서보드가 액세스하는 아티팩트 서비스에 업로드됩니다.

>[!Note]
> 이 TensorFlow 예제에서는 로컬 컴퓨터에 TensorFlow를 설치해야 합니다. 또한, 텐서 보드 모듈 (즉, 텐서 플로우에 포함 된 모듈) 로컬 기계는 TensorBoard를 실행하는 것과 같이,이 노트북의 커널에 액세스 할 수 있어야합니다.

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
이 실험에 대한 AmlCompute 클러스터를 만들지만 모든 환경에서 실험을 만들 수 있으며 실험 실행 기록에 대해 TensorBoard를 시작할 수 있습니다. 

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

### <a name="submit-run-with-tensorflow-estimator"></a>텐서플로우 추정기를 사용하러실행 제출

TensorFlow 추정기는 계산 대상에서 TensorFlow 교육 작업을 시작하는 간단한 방법을 제공합니다. 모든 프레임워크를 지원하는 [`estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) 데 사용할 수 있는 제네릭 클래스를 통해 구현됩니다. 일반 추정기를 사용하는 학습 모델에 대한 자세한 내용은 [추정기를 사용하여 Azure 기계 학습을 사용하는 학습 모델을](how-to-train-ml-models.md) 참조하십시오.

```Python
from azureml.train.dnn import TensorFlow
script_params = {"--log_dir": "./logs"}

tf_estimator = TensorFlow(source_directory=exp_dir,
                          compute_target=compute_target,
                          entry_script='mnist_with_summaries.py',
                          script_params=script_params)

run = exp.submit(tf_estimator)
```

### <a name="launch-tensorboard"></a>텐서보드 출시

실행 중 또는 완료된 후 TensorBoard를 시작할 수 있습니다. 다음에서는 에 로드된 실험 실행 기록을 `tb`취한 다음 `start()` 메서드를 사용하여 `run`TensorBoard를 실행하는 TensorBoard 개체 인스턴스를 만듭니다. 
  
[TensorBoard 생성자는](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard?view=azure-ml-py) 실행 배열을 수행하므로 반드시 단일 요소 배열로 전달합니다.

```python
from azureml.tensorboard import Tensorboard

tb = Tensorboard([run])

# If successful, start() returns a string with the URI of the instance.
tb.start()

# After your job completes, be sure to stop() the streaming otherwise it will continue to run. 
tb.stop()
```

>[!Note]
 이 예제에서는 텐서플로우를 사용하지만, 텐서보드는 PyTorch 또는 Chainer 모델에서 쉽게 사용할 수 있습니다. 텐서플로우는 텐서보드를 실행하는 컴퓨터에서 사용할 수 있어야 하지만 PyTorch 또는 Chainer 계산을 수행하는 기계에는 필요하지 않습니다. 


<a name="export"></a>

## <a name="option-2-export-history-as-log-to-view-in-tensorboard"></a>옵션 2: 텐서보드에서 볼 로그로 내보내기 기록

다음 코드는 샘플 실험을 설정하고 Azure Machine Learning 실행 기록 API를 사용하여 로깅 프로세스를 시작하고 실험 실행 기록을 시각화를 위해 TensorBoard에서 소모가능한 로그로 내보시작합니다. 

### <a name="set-up-experiment"></a>실험 설정

다음 코드는 새 실험을 설정하고 실행 디렉터리 `root_run`이름을 지정합니다. 

```python
from azureml.core import Workspace, Experiment
import azuremml.core

# set experiment name and run name
ws = Workspace.from_config()
experiment_name = 'export-to-tensorboard'
exp = Experiment(ws, experiment_name)
root_run = exp.start_logging()
```

여기서는 scikit-learn과 함께 제공되는 내장된 작은 데이터 집합인 당뇨병 데이터 집합을 로드하고 테스트 및 교육 세트로 분할합니다.

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

이 코드의 경우 선형 회귀 모델 및 로그 키 메트릭, `alpha`알파 계수 및 `mse`평균 제곱 오차를 학습합니다.

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

### <a name="export-runs-to-tensorboard"></a>텐서보드로 내보내기 실행

SDK의 [export_to_tensorboard()](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.export?view=azure-ml-py) 메서드를 사용하여 Azure 기계 학습 실험의 실행 기록을 TensorBoard 로그로 내보내 텐서보드를 통해 볼 수 있습니다.  

다음 코드에서는 현재 작업 `logdir` 디렉터리에서 폴더를 만듭니다. 이 폴더는 실험 실행 기록 및 로그를 `root_run` 내보낸 다음 완료된 것으로 표시합니다. 

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
 실행 이름을 지정하여 특정 실행을 TensorBoard로 내보낼 수도 있습니다.`export_to_tensorboard(run_name, logdir)`

### <a name="start-and-stop-tensorboard"></a>텐서보드 시작 및 중지
이 실험에 대한 실행 기록을 내보내면 [start()](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard?view=azure-ml-py#start-start-browser-false-) 메서드를 사용하여 TensorBoard를 시작할 수 있습니다. 

```Python
from azureml.tensorboard import Tensorboard

# The TensorBoard constructor takes an array of runs, so be sure and pass it in as a single-element array here
tb = Tensorboard([], local_root=logdir, port=6006)

# If successful, start() returns a string with the URI of the instance.
tb.start()
```

작업이 완료되면 TensorBoard 개체의 [stop()](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard?view=azure-ml-py#stop--) 메서드를 호출해야 합니다. 그렇지 않으면 노트북 커널을 종료할 때까지 TensorBoard가 계속 실행됩니다. 

```python
tb.stop()
```

## <a name="next-steps"></a>다음 단계

이 방법에서 두 가지 실험을 만들고 실행 이력에 대해 TensorBoard를 실행하여 잠재적인 튜닝 및 재교육 영역을 식별하는 방법을 배웠습니다. 

* 모델에 만족하는 경우 [모델 배포 방법](how-to-deploy-and-where.md) 문서를 참조하십시오. 
* [하이퍼매개 변수 튜닝에](how-to-tune-hyperparameters.md)대해 자세히 알아봅니다. 
