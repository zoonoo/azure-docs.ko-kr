---
title: TensorBoard로 실험 시각화
titleSuffix: Azure Machine Learning service
description: 실험 실행 기록 시각화에 TensorBoard를 시작 하 고 하이퍼 매개 변수 조정 및 재 학습에 대 한 잠재적인 영역을 식별 합니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: article
author: maxluk
ms.author: maxluk
ms.date: 06/28/2019
ms.openlocfilehash: fde2b6d1d298e89227951c376d584452fbff2679
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67707058"
---
# <a name="visualize-experiment-runs-and-metrics-with-tensorboard-and-azure-machine-learning"></a>실험 실행 및 TensorBoard 및 Azure Machine Learning을 사용 하 여 메트릭 시각화

이 문서에서는 TensorBoard를 사용 하 여 실험 실행 및 메트릭 보기에 방법을 알아봅니다 [는 `tensorboard` 패키지](https://docs.microsoft.com/python/api/azureml-tensorboard/?view=azure-ml-py) 주요 Azure Machine Learning에서 SDK 서비스입니다. 실험 실행을 검사 한 후 더 잘 조정 하 고 machine learning 모델 재 학습 합니다.

[TensorBoard](https://www.tensorflow.org/tensorboard/r1/overview) 는 검사 및 성능을 확인 하 고 실험 구조를 이해 하기 위한 웹 응용 프로그램의 모음입니다.

Azure Machine Learning 실험을 사용 하 여 TensorBoard를 시작 하는 방법을 실험의 유형에 따라 달라 집니다.
+ 실험은 PyTorch, Chainer 및 TensorFlow 실험 같은 TensorBoard를 사용할 수 있는 로그 파일을 고유 하 게 출력 경우 있습니다 [TensorBoard를 직접 시작](#direct) 실험의 실행 기록 합니다. 

+ TensorBoard 읽은 파일을 같은 Scikit-학습 또는 Azure Machine Learning 실험과 같은 출력 고유 하 게 하지 하는 실험에 대 한 사용 [는 `export_to_tensorboard()` 메서드](#export) TensorBoard 로그로 실행된 기록을 내보내고 시작 여기에서 TensorBoard 합니다. 

## <a name="prerequisites"></a>전제 조건

* TensorBoard 및 보기 실행 내역을 실험을 시작 하려면 실험을 이전에 메트릭 및 성능 추적에 로깅을 사용 하도록 설정 해야 합니다.  

* 이 방법의 코드는 다음 환경 중 하나에서 실행할 수 있습니다. 

    * Azure Machine Learning Notebook VM-다운로드 나 설치 필요 없이

        * 완료 합니다 [클라우드 기반 notebook 퀵 스타트](quickstart-run-cloud-notebook.md#create-notebook) SDK 및 샘플 리포지토리를 사용 하 여 미리 로드 전용된 노트북 서버를 만들려면.

        * Notebook 서버에 샘플 폴더에서 찾고 두 완료이 디렉터리로 이동 하 여 notebook을 확장 합니다. **방법으로-사용-azureml > 학습 된 심층 학습**합니다.
        * export-run-history-to-run-history.ipynb
        * tensorboard.ipynb

    * 사용자 고유의 Juptyer notebook 서버
      * 사용 된 [작업 영역 항목을 만드는](setup-create-workspace.md) 를
          * [Azure Machine Learning SDK를 설치](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) 사용 하 여는 `tensorboard` 추가
          * 작업 영역을 만들고 해당 구성 파일 (config.json)
  
<a name="direct"></a>
## <a name="option-1-directly-view-run-history-in-tensorboard"></a>옵션 1: 직접 실행 기록 보기 TensorBoard에서

이 옵션은 출력 TensorBoard PyTorch, Chainer 등에서 사용할 수 있는 파일을 기록 하는 고유 하 게 하 고 TensorFlow 실험 실험에 대 한 작동 합니다. 없는 경우 실험의 대/소문자를 사용 하 여 [는 `export_to_tensorboard()` 메서드](#export) 대신 합니다.

다음 예제 코드를 사용 하는 [MNIST 데모 실험](https://raw.githubusercontent.com/tensorflow/tensorflow/r1.8/tensorflow/examples/tutorials/mnist/mnist_with_summaries.py) Azure Machine Learning Compute, 원격 계산 대상에서 TensorFlow의 리포지토리에서 합니다. SDK의 사용자 지정을 사용 하 여 모델을 학습할 어 [TensorFlow 스 티 메이 터](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py), TensorBoard TensorBoard 이벤트 파일을 출력 하는 고유 하 게 하는 실험,이 TensorFlow 실험에 대 한 시작 합니다.

### <a name="set-experiment-name-and-create-project-folder"></a>프로젝트 폴더를 만들고 실험 이름 설정

실험 이름을 여기 해당 폴더를 만듭니다. 
 
```python
from os import path, makedirs
experiment_name = 'tensorboard-demo'

# experiment folder
exp_dir = './sample_projects/' + experiment_name

if not path.exists(exp_dir):
    makedirs(exp_dir)

```

### <a name="download-tensorflow-demo-experiment-code"></a>TensorFlow 데모 실험 코드 다운로드

TensorFlow의 리포지토리에 광범위 한 TensorBoard 계측을 사용 하 여 MNIST 데모를 합니다. 가지고 있지 않은 것도 Azure Machine Learning 서비스와 작동 하도록 하기 위해이 데모 코드를 변경 해야 할. 다음 코드에서 MNIST 코드를 다운로드 하는 새로 만든된 실험 폴더에 저장 합니다.

```python
import requests
import os

tf_code = requests.get("https://raw.githubusercontent.com/tensorflow/tensorflow/r1.8/tensorflow/examples/tutorials/mnist/mnist_with_summaries.py")
with open(os.path.join(exp_dir, "mnist_with_summaries.py"), "w") as file:
    file.write(tf_code.text)
```
Mnist_with_summaries.py를 가지는 MNIST 코드 파일 전체에서 해당 호출을 줄 `tf.summary.scalar()`하십시오 `tf.summary.histogram()`, `tf.summary.FileWriter()` 등입니다. 이러한 메서드는 그룹, 로그 및 실험의 주요 메트릭이 실행된 기록에 태그를 지정 합니다. `tf.summary.FileWriter()` 허용 TensorBoard에서 해당 시각화를 생성 하는 메트릭 기록된 실험에서에서 데이터를 serialize 할 때 특히 유용 합니다.

 ### <a name="configure-experiment"></a>실험 구성

다음에서 실험을 구성 하 고 로그 및 데이터에 대 한 디렉터리를 설정 합니다. 이러한 로그는 TensorBoard 나중에 액세스 하는 아티팩트 서비스에 업로드 됩니다.

>[!Note]
> TensorFlow 예를 들어 로컬 컴퓨터에서 TensorFlow를 설치 해야 합니다. 또한 로컬 컴퓨터 TensorBoard 실행 되는 것은 TensorBoard 모듈 (즉, TensorFlow를 사용 하 여 포함 된 하나)이이 노트북의이 커널 액세스할 수 있어야 합니다.

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

### <a name="create-a-cluster-for-your-experiment"></a>실험에 대 한 클러스터 만들기
실험 환경에서 만들 수 있으며 여전히 실험 실행 기록이 대해 TensorBoard를 시작할 수 있지만이 실험 AmlCompute 클러스터를 만듭니다. 

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

### <a name="submit-run-with-tensorflow-estimator"></a>TensorFlow 평가기를 사용 하 여 실행을 제출

TensorFlow 스 티 메이 터 계산 대상에서 TensorFlow 교육 작업을 시작 하는 간단한 방법을 제공 합니다. 제네릭을 통해 구현 됩니다 [ `estimator` ](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) 클래스에서 임의 프레임 워크를 지 원하는 데 사용할 수 있습니다. 제네릭 평가기를 사용 하 여 모델을 학습 하는 방법에 대 한 자세한 내용은 참조 하세요. [평가기를 사용 하 여 Azure Machine Learning을 사용 하 여 모델을 학습 합니다.](how-to-train-ml-models.md)

```Python
from azureml.train.dnn import TensorFlow
script_params = {"--log_dir": "./logs"}

tf_estimator = TensorFlow(source_directory=exp_dir,
                          compute_target=compute_target,
                          entry_script='mnist_with_summaries.py',
                          script_params=script_params)

run = exp.submit(tf_estimator)
```

### <a name="launch-tensorboard"></a>TensorBoard를 시작 합니다.

실행 중 또는 완료 된 후에 TensorBoard를 시작할 수 있습니다. 다음 TensorBoard 개체 인스턴스를 만듭니다 `tb`는 실험 실행 기록 로드, 합니다 `run`을 사용 하 여 TensorBoard를 시작 합니다 `start()` 메서드. 
  
합니다 [TensorBoard 생성자](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard?view=azure-ml-py) 배열을 사용 실행 되므로 해야 및 단일 요소 배열로 전달 합니다.

```python
from azureml.tensorboard import Tensorboard

tb = Tensorboard([run])

# If successful, start() returns a string with the URI of the instance.
tb.start()

# After your job completes, be sure to stop() the streaming otherwise it will continue to run. 
tb.stop()
```

<a name="export"></a>

## <a name="option-2-export-history-as-log-to-view-in-tensorboard"></a>옵션 2: TensorBoard에서 보려는 로그 내보내기 기록

다음 코드 샘플 실험을 설정 하 고, 실행 기록 Api, Azure Machine Learning을 사용 하는 로깅 프로세스를 시작, 실행 기록에 사용할 수 있는 로그 tensorboard 시각화에 대 한 실험을 내보냅니다. 

### <a name="set-up-experiment"></a>실험 설정

다음 코드는 새 실험을 설정 하 고 실행된 디렉터리 이름을 `root_run`입니다. 

```python
from azureml.core import Workspace, Experiment
import azuremml.core

# set experiment name and run name
ws = Workspace.from_config()
experiment_name = 'export-to-tensorboard'
exp = Experiment(ws, experiment_name)
root_run = exp.start_logging()
```

여기에서는 로드 된 diabetes 데이터 집합-scikit 함께 제공 되는 기본 제공 작은 데이터 집합-배우고, 테스트 및 학습 집합으로 분할 합니다.

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

### <a name="run-experiment-and-log-metrics"></a>실험을 실행 하 고 로그 메트릭

이 코드에 대 한 선형 회귀 모델을 학습 하 고 알파 계수 주요 메트릭을 기록할 `alpha` 제곱 평균 오차 및 `mse`, 실행 기록 합니다.

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

### <a name="export-runs-to-tensorboard"></a>TensorBoard 실행 내보내기

SDK를 사용 하 여 [export_to_tensorboard()](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.export?view=azure-ml-py) 메서드를 해당 TensorBoard를 통해 볼 수 있도록 TensorBoard 로그에는 Azure machine learning 실험의 실행된 기록을 내보낼 수 있습니다.  

다음 코드에서는 폴더를 만듭니다 `logdir` 는 현재 작업 디렉터리에 있습니다. 이 폴더는 실행 기록 실험 내보냅니다은 및에서 로그 `root_run` 하 고 완료로 실행 되는 표시 합니다. 

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
 실행의 이름을 지정 하 여 특정 실행 TensorBoard를 내보낼 수도 있습니다.  `export_to_tensorboard(run_name, logdir)`

TensorBoard를 사용 하 여 start 및 stop이이 실험의 실행된 기록을 우리의 내보내집니다 TensorBoard 한 번에 실행할 수 있습니다 합니다 [start ()](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard?view=azure-ml-py#start-start-browser-false-) 메서드. 

```Python
from azureml.tensorboard import Tensorboard

# The TensorBoard constructor takes an array of runs, so be sure and pass it in as a single-element array here
tb = Tensorboard([], local_root=logdir, port=6006)

# If successful, start() returns a string with the URI of the instance.
tb.start()
```

완료 되 면 호출 해야 합니다 [stop ()](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard?view=azure-ml-py#stop--) TensorBoard 개체의 메서드. 이 고, 그렇지 TensorBoard notebook 커널 종료 될 때까지 실행을 계속 합니다. 

```python
tb.stop()
```

## <a name="next-steps"></a>다음 단계

이 방법, 두 실험 만들고 잠재적으로 조정 하 고 재 학습 영역을 식별 하는 실행된 기록에 대해 TensorBoard를 시작 하는 방법을 배웠습니다. 

* 만족 스 러 우면 모델을 사용 하 여,로 이동 하세요 [모델을 배포 하는 방법을](how-to-deploy-and-where.md) 문서. 
* 에 대해 자세히 알아보세요 [하이퍼 매개 변수 튜닝](how-to-tune-hyperparameters.md)합니다. 
