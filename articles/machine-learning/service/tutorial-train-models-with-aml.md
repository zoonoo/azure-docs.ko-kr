---
title: '이미지 분류 자습서: 모델 학습'
titleSuffix: Azure Machine Learning service
description: Azure Machine Learning Service를 사용하는 Python Jupyter Notebook에서 scikit-Learn을 사용하여 이미지 분류 모델을 학습시키는 방법을 알아봅니다. 이 자습서는 2부로 구성된 시리즈 중 제1부입니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: sdgilley
ms.author: sgilley
ms.date: 05/08/2019
ms.custom: seodec18
ms.openlocfilehash: 097fb3422ce3868d9ef499ad6c92c8b7fa12e852
ms.sourcegitcommit: 4891f404c1816ebd247467a12d7789b9a38cee7e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2019
ms.locfileid: "65442053"
---
# <a name="tutorial-train-image-classification-models-with-mnist-data-and-scikit-learn-using-azure-machine-learning"></a>자습서: Azure Machine Learning에서 MNIST 데이터와 scikit-learn을 사용하여 이미지 분류 모델 학습

이 자습서에서는 원격 컴퓨팅 리소스에 대해 기계 학습 모델을 학습합니다. Python Jupyter 노트북에서 Azure Machine Learning 서비스(미리 보기)에 대한 학습 및 배포 워크플로를 사용합니다.  그런 다음, 이 노트를 템플릿으로 사용하여 자신의 데이터로 고유한 Machine Learning 모델을 학습할 수 있습니다. 이 자습서는 **2부로 구성된 자습서 시리즈 중 제1부**입니다.  

이 자습서에서는 Azure Machine Learning Service에서 [MNIST](http://yann.lecun.com/exdb/mnist/) 데이터 세트 및 [scikit-learn](https://scikit-learn.org)을 사용하여 간단한 로지스틱 회귀를 학습합니다. MNIST는 70,000개의 회색조 이미지로 구성된 인기 있는 데이터 세트입니다. 각 이미지는 0-9의 숫자를 나타내는 28x28 픽셀의 필기체 숫자입니다. 목표는 지정된 이미지가 나타내는 숫자를 식별하는 다중 클래스 분류자를 만드는 것입니다. 

다음 작업을 수행하는 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 개발 환경 설정
> * 데이터 액세스 및 검사
> * 원격 클러스터의 간단한 로지스틱 회귀 모델을 학습합니다.
> * 학습 결과 검토 및 최상의 모델 등록

모델을 선택하고 배포하는 방법은 이 [자습서의 제2부](tutorial-deploy-models-with-aml.md)에서 알아봅니다. 

Azure 구독이 없는 경우 시작하기 전에 체험 계정을 만듭니다. [Azure Machine Learning Service의 평가판 또는 유료 버전](https://aka.ms/AMLFree)을 지금 사용해 보세요.

>[!NOTE]
> 이 문서의 코드는 Azure Machine Learning SDK 버전 1.0.8에서 테스트되었습니다.

## <a name="prerequisites"></a>필수 조건

[개발 환경 설정](#start)으로 건너뛰어 Notebook 단계를 읽어보거나, 아래 지침에 따라 Notebook을 가져와서 Azure Notebooks 또는 사용자 고유의 Notebook 서버에서 실행합니다.  Notebook을 실행하려면 다음 항목이 필요합니다.

* 다음 요소가 설치된 Python 3.6 Notebook 서버:
    * Python용 Azure Machine Learning SDK
    * `matplotlib` 및 `scikit-learn`
* 자습서 Notebook 및 **utils.py** 파일
* 기계 학습 작업 영역
* Notebook과 동일한 디렉터리에 있는 작업 영역에 대한 구성 파일

아래 섹션 중 하나에서 이러한 필수 구성 요소를 모두 가져옵니다.
 
* [작업 영역에서 클라우드 Notebook 서버](#azure) 사용 
* [사용자 고유의 Notebook 서버](#server) 사용

### <a name="azure"></a>작업 영역에서 클라우드 Notebook 서버 사용

사용자 고유의 클라우드 기반 Notebook 서버를 쉽게 시작할 수 있습니다. 이 클라우드 리소스를 만들면 [Python용 Azure Machine Learning SDK](https://aka.ms/aml-sdk)가 이미 설치 및 구성되어 있습니다.

[!INCLUDE [aml-azure-notebooks](../../../includes/aml-azure-notebooks.md)]

* Notebook 웹 페이지를 시작한 후 **tutorials/img-classification-part1-training.ipynb** Notebook을 엽니다.


### <a name="server"></a>사용자 고유의 Jupyter Notebook 서버 사용

[!INCLUDE [aml-your-server](../../../includes/aml-your-server.md)]

 단계를 완료한 후 복제된 디렉터리에서 **tutorials/img-classification-part1-training.ipynb** Notebook을 실행합니다.

## <a name="start"></a>개발 환경 설정

개발 작업에 대한 모든 설정은 Python Notebook에서 수행할 수 있습니다. 설정에 포함되는 작업은 다음과 같습니다.

* Python 패키지 가져오기
* 로컬 컴퓨터에서 원격 리소스와 통신할 수 있도록 작업 영역에 연결
* 모든 실행을 추적하는 실험 만들기
* 학습에 사용할 원격 컴퓨팅 대상 만들기

### <a name="import-packages"></a>패키지 가져오기

이 세션에 필요한 Python 패키지를 가져옵니다. Azure Machine Learning SDK 버전도 표시합니다.

```python
%matplotlib inline
import numpy as np
import matplotlib.pyplot as plt

import azureml.core
from azureml.core import Workspace

# check core SDK version number
print("Azure ML SDK Version: ", azureml.core.VERSION)
```

### <a name="connect-to-a-workspace"></a>작업 영역에 연결

기존 작업 영역에서 작업 영역 개체를 만듭니다. `Workspace.from_config()`는 **config.json** 파일을 읽어 `ws`라는 개체에 세부 정보를 로드합니다.

```python
# load workspace configuration from the config.json file in the current folder.
ws = Workspace.from_config()
print(ws.name, ws.location, ws.resource_group, ws.location, sep = '\t')
```

### <a name="create-an-experiment"></a>실험 만들기

작업 영역에서 실행을 추적하는 실험을 만듭니다. 작업 영역에는 여러 개의 실험이 있을 수 있습니다. 

```python
experiment_name = 'sklearn-mnist'

from azureml.core import Experiment
exp = Experiment(workspace=ws, name=experiment_name)
```

### <a name="create-or-attach-an-existing-compute-resource"></a>기존 컴퓨팅 리소스 만들기 또는 연결

데이터 과학자는 관리형 서비스인 Azure Machine Learning 컴퓨팅을 사용하여 Azure 가상 머신 클러스터에서 기계 학습 모델을 학습할 수 있습니다. 예를 들어 GPU가 지원되는 VM이 있습니다. 이 자습서에서는 학습 환경으로 Azure Machine Learning 컴퓨팅을 만듭니다. 아래 코드는 작업 영역에 아직 컴퓨팅 클러스터가 없으면 새로 만듭니다.

 **컴퓨팅을 만드는 데 약 5분이 걸립니다.** 작업 영역에 이미 컴퓨팅이 있으면 이 코드는 기존 컴퓨팅을 사용하고 만들기 프로세스를 건너뜁니다.


```python
from azureml.core.compute import AmlCompute
from azureml.core.compute import ComputeTarget
import os

# choose a name for your cluster
compute_name = os.environ.get("AML_COMPUTE_CLUSTER_NAME", "cpucluster")
compute_min_nodes = os.environ.get("AML_COMPUTE_CLUSTER_MIN_NODES", 0)
compute_max_nodes = os.environ.get("AML_COMPUTE_CLUSTER_MAX_NODES", 4)

# This example uses CPU VM. For using GPU VM, set SKU to STANDARD_NC6
vm_size = os.environ.get("AML_COMPUTE_CLUSTER_SKU", "STANDARD_D2_V2")


if compute_name in ws.compute_targets:
    compute_target = ws.compute_targets[compute_name]
    if compute_target and type(compute_target) is AmlCompute:
        print('found compute target. just use it. ' + compute_name)
else:
    print('creating a new compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(vm_size = vm_size,
                                                                min_nodes = compute_min_nodes, 
                                                                max_nodes = compute_max_nodes)

    # create the cluster
    compute_target = ComputeTarget.create(ws, compute_name, provisioning_config)
    
    # can poll for a minimum number of nodes and for a specific timeout. 
    # if no min node count is provided it will use the scale settings for the cluster
    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
    
     # For a more detailed view of current AmlCompute status, use get_status()
    print(compute_target.get_status().serialize())
```

이제 클라우드에서 모델을 학습하는 데 필요한 패키지 및 계산 리소스가 준비되었습니다. 

## <a name="explore-data"></a>데이터 탐색

모델을 학습하기 전에 학습하는 데 사용할 데이터를 파악해야 합니다. 또한 데이터를 클라우드에 복사해야 합니다. 그러면 클라우드 학습 환경에서 액세스할 수 있습니다. 이 섹션에서는 다음 작업을 수행하는 방법에 대해 알아봅니다.

* MNIST 데이터 세트 다운로드
* 일부 샘플 이미지 표시
* 클라우드에 데이터 업로드

### <a name="download-the-mnist-dataset"></a>MNIST 데이터 세트 다운로드

MNIST 데이터 세트를 다운로드하고 파일을 `data` 디렉터리에 로컬로 저장합니다. 학습 및 테스트용 이미지와 레이블이 모두 다운로드됩니다.


```python
import urllib.request
import os

data_folder = os.path.join(os.getcwd(), 'data')
os.makedirs(data_folder, exist_ok = True)

urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz', filename=os.path.join(data_folder, 'train-images.gz'))
urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz', filename=os.path.join(data_folder, 'train-labels.gz'))
urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz', filename=os.path.join(data_folder, 'test-images.gz'))
urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz', filename=os.path.join(data_folder, 'test-labels.gz'))
```
```('./data/test-labels.gz', <http.client.HTTPMessage at 0x7f40864c77b8>)``` 같은 출력이 표시됩니다.

### <a name="display-some-sample-images"></a>일부 샘플 이미지 표시

압축된 파일을 `numpy` 배열로 로드합니다. 그런 다음, `matplotlib`를 사용하여 데이터 세트에 있는 30개의 무작위 이미지를 그리고, 위에 레이블을 표시합니다. 이 단계를 수행하려면 `util.py` 파일에 포함된 `load_data` 함수가 필요합니다. 이 파일은 샘플 폴더에 포함되어 있습니다. 이 Notebook과 동일한 폴더에 있는지 확인합니다. `load_data` 함수는 압축 파일을 numpy 배열로만 간단히 구문 분석합니다.



```python
# make sure utils.py is in the same directory as this code
from utils import load_data

# note we also shrink the intensity values (X) from 0-255 to 0-1. This helps the model converge faster.
X_train = load_data(os.path.join(data_folder, 'train-images.gz'), False) / 255.0
X_test = load_data(os.path.join(data_folder, 'test-images.gz'), False) / 255.0
y_train = load_data(os.path.join(data_folder, 'train-labels.gz'), True).reshape(-1)
y_test = load_data(os.path.join(data_folder, 'test-labels.gz'), True).reshape(-1)

# now let's show some randomly chosen images from the traininng set.
count = 0
sample_size = 30
plt.figure(figsize = (16, 6))
for i in np.random.permutation(X_train.shape[0])[:sample_size]:
    count = count + 1
    plt.subplot(1, sample_size, count)
    plt.axhline('')
    plt.axvline('')
    plt.text(x=10, y=-10, s=y_train[i], fontsize=18)
    plt.imshow(X_train[i].reshape(28, 28), cmap=plt.cm.Greys)
plt.show()
```

이미지의 무작위 샘플이 다음과 같이 표시됩니다.

![무작위 이미지 샘플](./media/tutorial-train-models-with-aml/digits.png)

이제 이러한 이미지의 모양과 예상되는 예측 결과를 이해할 수 있을 것입니다.

### <a name="upload-data-to-the-cloud"></a>클라우드에 데이터 업로드

이제 로컬 머신의 데이터를 Azure에 업로드하여 원격으로 데이터에 액세스할 수 있도록 합니다. 그러면 원격 학습에 액세스할 수 있습니다. 데이터 저장소는 데이터를 업로드하거나 다운로드할 수 있는 작업 영역과 연결된 편리한 구조입니다. 원격 컴퓨팅 대상에서도 이 데이터 저장소와 상호 작용할 수 있습니다. 이는 Azure Blob 스토리지 계정에서 지원됩니다.

MNIST 파일은 데이터 저장소의 루트에 있는 `mnist` 디렉터리로 업로드됩니다.

```python
ds = ws.get_default_datastore()
print(ds.datastore_type, ds.account_name, ds.container_name)

ds.upload(src_dir=data_folder, target_path='mnist', overwrite=True, show_progress=True)
```
이제 모델 학습을 시작하는 데 필요한 모든 준비가 갖추어졌습니다. 


## <a name="train-on-a-remote-cluster"></a>원격 클러스터에서 학습

이 태스크의 경우 이전에 설정한 원격 학습 클러스터로 작업을 제출하세요.  작업을 제출하는 방법은 아래와 같습니다.
* 디렉터리 만들기
* 학습 스크립트 만들기
* Estimator 개체 만들기
* 작업 제출 

### <a name="create-a-directory"></a>디렉터리 만들기

필요한 코드를 컴퓨터에서 원격 리소스로 전달하기 위한 디렉터리를 만듭니다.

```python
import os
script_folder  = os.path.join(os.getcwd(), "sklearn-mnist")
os.makedirs(script_folder, exist_ok=True)
```

### <a name="create-a-training-script"></a>학습 스크립트 만들기

클러스터에 작업을 제출하려면 먼저 학습 스크립트를 만듭니다. 다음 코드를 실행하여 방금 만든 디렉터리에 `train.py`라는 학습 스크립트를 만듭니다.

```python
%%writefile $script_folder/train.py

import argparse
import os
import numpy as np

from sklearn.linear_model import LogisticRegression
from sklearn.externals import joblib

from azureml.core import Run
from utils import load_data

# let user feed in 2 parameters, the location of the data files (from datastore), and the regularization rate of the logistic regression model
parser = argparse.ArgumentParser()
parser.add_argument('--data-folder', type=str, dest='data_folder', help='data folder mounting point')
parser.add_argument('--regularization', type=float, dest='reg', default=0.01, help='regularization rate')
args = parser.parse_args()

data_folder = args.data_folder
print('Data folder:', data_folder)

# load train and test set into numpy arrays
# note we scale the pixel intensity values to 0-1 (by dividing it with 255.0) so the model can converge faster.
X_train = load_data(os.path.join(data_folder, 'train-images.gz'), False) / 255.0
X_test = load_data(os.path.join(data_folder, 'test-images.gz'), False) / 255.0
y_train = load_data(os.path.join(data_folder, 'train-labels.gz'), True).reshape(-1)
y_test = load_data(os.path.join(data_folder, 'test-labels.gz'), True).reshape(-1)
print(X_train.shape, y_train.shape, X_test.shape, y_test.shape, sep = '\n')

# get hold of the current run
run = Run.get_context()

print('Train a logistic regression model with regularization rate of', args.reg)
clf = LogisticRegression(C=1.0/args.reg, random_state=42)
clf.fit(X_train, y_train)

print('Predict the test set')
y_hat = clf.predict(X_test)

# calculate accuracy on the prediction
acc = np.average(y_hat == y_test)
print('Accuracy is', acc)

run.log('regularization rate', np.float(args.reg))
run.log('accuracy', np.float(acc))

os.makedirs('outputs', exist_ok=True)
# note file saved in the outputs folder is automatically uploaded into experiment record
joblib.dump(value=clf, filename='outputs/sklearn_mnist_model.pkl')
```

이 스크립트가 데이터를 가져오고 모델을 저장하는 방법은 다음과 같습니다.

+ 학습 스크립트에서 인수를 읽어 데이터가 있는 디렉터리를 찾습니다. 나중에 작업을 제출할 때 이 인수에 대한 데이터 저장소를 가리킵니다(```parser.add_argument('--data-folder', type=str, dest='data_folder', help='data directory mounting point')```).

+ 학습 스크립트는 **outputs**라는 디렉터리에 모델을 저장합니다. 이 디렉터리에 작성된 모든 내용은 작업 영역으로 자동 업로드됩니다. 자습서의 뒷부분에서는 이 디렉터리의 모델에 액세스합니다. `joblib.dump(value=clf, filename='outputs/sklearn_mnist_model.pkl')`

+ 학습 스크립트가 데이터 세트를 올바르게 로드하려면 `utils.py` 파일이 필요합니다. 다음 코드는 `utils.py`를 `script_folder`에 복사합니다. 그러면 원격 리소스의 학습 스크립트와 함께 이 파일에 액세스할 수 있습니다.

  ```python
  import shutil
  shutil.copy('utils.py', script_folder)
  ```


### <a name="create-an-estimator"></a>추정기 만들기

추정기 개체는 실행을 제출하는 데 사용됩니다. 다음과 같은 항목을 정의하는 코드를 실행하여 추정기를 만듭니다.

* 추정기 개체의 이름(`est`)
* 스크립트를 포함하는 디렉터리. 이 디렉터리의 모든 파일은 실행을 위해 클러스터 노드로 업로드됩니다. 
* 계산 대상. 이 경우 만든 Azure Machine Learning 컴퓨팅 클러스터를 사용합니다.
* 학습 스크립트 이름(**train.py**)
* 학습 스크립트에서 필요한 매개 변수 
* 학습에 필요한 Python 패키지

이 자습서에서 이 대상은 AmlCompute입니다. 스크립트 폴더의 모든 파일은 실행을 위해 클러스터 노드에 업로드됩니다. **data_folder**에서 데이터 저장소(`ds.path('mnist').as_mount()`)를 사용하도록 설정되었습니다.

```python
from azureml.train.estimator import Estimator

script_params = {
    '--data-folder': ds.path('mnist').as_mount(),
    '--regularization': 0.8
}

est = Estimator(source_directory=script_folder,
                script_params=script_params,
                compute_target=compute_target,
                entry_script='train.py',
                conda_packages=['scikit-learn'])
```


### <a name="submit-the-job-to-the-cluster"></a>클러스터에 작업 제출

추정기 개체를 제출하여 실험을 실행합니다.

```python
run = exp.submit(config=est)
run
```

호출은 비동기이므로 작업이 시작되는 즉시 **준비 중** 또는 **실행 중** 상태가 반환됩니다.

## <a name="monitor-a-remote-run"></a>원격 실행 모니터링

전체적으로 첫 번째 실행은 **약 10분**이 걸립니다. 그러나 스크립트 종속성이 변경되지 않는 한 후속 실행에서도 동일한 이미지가 다시 사용됩니다. 따라서 컨테이너 시작 시간이 훨씬 더 빠릅니다.

기다리는 동안 수행되는 작업은 다음과 같습니다.

- **이미지 만들기**: 추정기에서 지정한 Python 환경과 일치하는 Docker 이미지가 만들어집니다. 이미지는 작업 영역에 업로드됩니다. 이미지를 만들고 업로드하는 데 **약 5분**이 걸립니다. 

  컨테이너가 후속 실행을 위해 캐시되므로 이 단계는 각 Python 환경에 대해 한 번만 수행됩니다. 이미지 생성 중에 로그가 실행 기록으로 스트리밍됩니다. 이러한 로그를 사용하여 이미지 만들기 진행 상황을 모니터링할 수 있습니다.

- **크기 조정**: 원격 클러스터에서 현재 사용 가능한 것보다 더 많은 노드를 실행해야 하는 경우 추가 노드가 자동으로 추가됩니다. 크기 조정에는 일반적으로 **약 5분**이 걸립니다.

- **Running**: 이 단계에서는 필요한 스크립트와 파일이 컴퓨팅 대상으로 보내집니다. 다음으로, 데이터 저장소가 탑재되거나 복사됩니다. 그런 다음, **entry_script**가 실행됩니다. 작업이 실행되는 동안 **stdout** 및 **./logs** 디렉터리가 실행 기록으로 스트리밍됩니다. 이러한 로그를 사용하여 실행 진행 상황을 모니터링할 수 있습니다.

- **후 처리 중**: 실행의 **./outputs** 디렉터리가 작업 영역의 실행 기록으로 복사되므로 이러한 결과에 액세스할 수 있습니다.


실행 중인 작업의 진행 상황은 여러 가지 방법으로 확인할 수 있습니다. 이 자습서에서는 Jupyter 위젯과 `wait_for_completion` 메서드를 사용합니다. 

### <a name="jupyter-widget"></a>Jupyter 위젯

Jupyter 위젯으로 실행의 진행 상태를 감시합니다. 실행 제출과 마찬가지로, 위젯은 비동기이며 작업이 완료될 때까지 10~15초마다 라이브 업데이트를 제공합니다.


```python
from azureml.widgets import RunDetails
RunDetails(run).show()
```

다음 스냅샷은 학습의 끝에 표시되는 위젯입니다.

![Notebook 위젯](./media/tutorial-train-models-with-aml/widget.png)

실행을 취소해야 하는 경우 [다음 지침](https://aka.ms/aml-docs-cancel-run)을 따릅니다.

### <a name="get-log-results-upon-completion"></a>완료 시 로그 결과 가져오기

모델 학습 및 모니터링은 백그라운드에서 발생합니다. 더 많은 코드를 실행하기 전에 모델 학습이 완료될 때까지 기다립니다. 모델 학습이 완료되는 시간을 표시하려면 `wait_for_completion`을 사용합니다. 


```python
run.wait_for_completion(show_output=False) # specify True for a verbose log
```

### <a name="display-run-results"></a>실행 결과 표시

이제 모델이 원격 클러스터에서 학습을 마쳤습니다. 다음과 같이 모델의 정확도를 확인합니다.

```python
print(run.get_metrics())
```
출력은 원격 모델 정확도가 0.9204라는 것을 보여줍니다.

`{'regularization rate': 0.8, 'accuracy': 0.9204}`

다음 자습서에서는 이 모델에 대해 더 자세히 살펴봅니다.

## <a name="register-model"></a>모델 등록

학습 스크립트의 마지막 단계에서는 작업이 실행되는 클러스터의 VM에 있는 `outputs`라는 디렉터리에 `outputs/sklearn_mnist_model.pkl` 파일을 작성했습니다. `outputs`는 이 디렉터리의 모든 콘텐츠가 작업 영역에 자동으로 업로드된다는 점에서 특수한 디렉터리입니다. 이 콘텐츠는 작업 영역의 실험 실행 레코드에 표시됩니다. 따라서 이제는 작업 영역에서도 모델 파일을 사용할 수 있습니다.

해당 실행과 연결된 파일을 볼 수 있습니다.

```python
print(run.get_file_names())
```

나중에 사용자 또는 다른 공동 작업자가 이 모델을 쿼리, 검사 및 배포할 수 있도록 해당 모델을 작업 영역에 등록합니다.

```python
# register model 
model = run.register_model(model_name='sklearn_mnist', model_path='outputs/sklearn_mnist_model.pkl')
print(model.name, model.id, model.version, sep = '\t')
```

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

Azure Machine Learning 컴퓨팅 클러스터만 삭제할 수도 있습니다. 그러나 자동 크기 조정이 설정되고 클러스터 최솟값이 0이 됩니다. 따라서 이 특정 리소스를 사용하지 않는 경우에는 추가 컴퓨팅 요금이 부과하지 않습니다.


```python
# optionally, delete the Azure Machine Learning Compute cluster
compute_target.delete()
```

## <a name="next-steps"></a>다음 단계

이 Azure Machine Learning Service 자습서에서는 Python을 사용하여 다음 작업을 수행했습니다.

> [!div class="checklist"]
> * 개발 환경 설정
> * 데이터 액세스 및 검사
> * 인기 있는 scikit-learn 기계 학습 라이브러리를 사용하여 원격 클러스터에서 여러 모델 학습
> * 학습 세부 정보 검토 및 최상의 모델 등록

이 등록된 모델을 자습서 시리즈의 제2부에 나와 있는 지침에 따라 배포할 준비가 되었습니다.

> [!div class="nextstepaction"]
> [자습서 2 - 모델 배포](tutorial-deploy-models-with-aml.md)
