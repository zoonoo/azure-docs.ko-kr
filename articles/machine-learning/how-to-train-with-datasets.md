---
title: azureml 데이터 집합으로 학습
titleSuffix: Azure Machine Learning
description: 교육에서 데이터 집합을 사용하는 방법 알아보기
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 03/09/2020
ms.openlocfilehash: 401383f2d483836bf725051810d78167869f7b22
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283499"
---
# <a name="train-with-datasets-in-azure-machine-learning"></a>Azure 기계 학습에서 데이터 집합으로 학습
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

이 문서에서는 연결 문자열 이나 데이터 경로 에 대 한 걱정 없이 원격 실험 학습 실행에서 [Azure 기계 학습 데이터 집합을](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py) 사용 하는 두 가지 방법을 알아봅니다.

- 옵션 1: 데이터를 구조화한 경우 TabularDataset을 만들고 학습 스크립트에서 직접 사용합니다.

- 옵션 2: 구조화되지 않은 데이터가 있는 경우 FileDataset을 만들고 교육을 위해 파일을 원격 계산에 마운트하거나 다운로드합니다.

Azure 기계 학습 데이터 집합은 [ScriptRun,](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrun?view=azure-ml-py) [추정기,](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py) [하이퍼드라이브](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive?view=azure-ml-py) 및 Azure 기계 학습 파이프라인과 같은 Azure 기계 학습 교육 제품과 원활하게 [통합됩니다.](how-to-create-your-first-pipeline.md)

## <a name="prerequisites"></a>사전 요구 사항

데이터 집합을 만들고 학습하려면 다음이 필요합니다.

* Azure 구독 Azure 구독이 없는 경우 시작하기 전에 체험 계정을 만듭니다. 지금 [Azure Machine Learning 평가판 또는 유료 버전](https://aka.ms/AMLFree)을 사용해 보세요.

* [Azure 기계 학습 작업 영역](how-to-manage-workspace.md).

* Azureml 데이터 집합 패키지를 포함하는 [파이썬용 Azure 기계 학습 SDK가 설치되었습니다.](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)

> [!Note]
> 일부 데이터 집합 클래스에는 [azureml-dataprep](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) 패키지에 대한 종속성이 있습니다. 리눅스 사용자에 대 한, 이러한 클래스는 다음 배포판에서 지원 됩니다.: 레드 햇 엔터프라이즈 리눅스, 우분투, 페도라, 그리고 CentOS.

## <a name="option-1-use-datasets-directly-in-training-scripts"></a>옵션 1: 학습 스크립트에 직접 데이터 집합 사용

이 예제에서는 [TabularDataset을](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) 만들고 학습을 위해 `estimator` 개체에 대한 직접 입력으로 사용합니다. 

### <a name="create-a-tabulardataset"></a>테이블 형식데이터 집합 만들기

다음 코드는 웹 URL에서 등록되지 않은 TabularDataset을 만듭니다. 데이터스토어의 로컬 파일 이나 경로에서 데이터 집합을 만들 수도 있습니다. [데이터 집합을 만드는 방법에](https://aka.ms/azureml/howto/createdatasets)대해 자세히 알아봅니다.

```Python
from azureml.core.dataset import Dataset

web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path)
```

### <a name="access-the-input-dataset-in-your-training-script"></a>교육 스크립트의 입력 데이터 집합에 액세스

TabularDataset 개체는 익숙한 데이터 준비 및 학습 라이브러리로 작업할 수 있도록 데이터를 팬더에 로드하거나 DataFrame을 스파크하는 기능을 제공합니다. 이 기능을 활용하려면 TabularDataset을 학습 구성의 입력으로 전달한 다음 스크립트에서 검색할 수 있습니다.

이렇게 하려면 학습 스크립트의 개체를 [`Run`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py) 통해 입력 데이터 집합에 액세스하고 메서드를 [`to_pandas_dataframe()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset#to-pandas-dataframe-on-error--null---out-of-range-datetime--null--) 사용합니다. 

```Python
%%writefile $script_folder/train_titanic.py

from azureml.core import Dataset, Run

run = Run.get_context()
# get the input dataset by name
dataset = run.input_datasets['titanic']
# load the TabularDataset to pandas DataFrame
df = dataset.to_pandas_dataframe()
```

### <a name="configure-the-estimator"></a>추정기 구성

[추정개체는](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) 실험 실행을 제출하는 데 사용됩니다. Azure 기계 학습에는 일반적인 기계 학습 프레임워크와 일반 추정기에 대해 미리 구성된 추정기가 있습니다.

이 코드는 제네릭 `est`추정기 개체를 만듭니다.

* 스크립트에 대한 스크립트 디렉터리입니다. 이 디렉터리의 모든 파일은 실행을 위해 클러스터 노드로 업로드됩니다.
* 교육 스크립트, *train_titanic.py*.
* 학습을 위한 입력 `titanic`데이터 집합, . `as_named_input()`입력 데이터 집합을 학습 스크립트에 할당된 이름으로 참조할 수 있도록 해야 합니다. 
* 실험의 계산 대상입니다.
* 실험의 환경 정의입니다.

```Python
est = Estimator(source_directory=script_folder,
                entry_script='train_titanic.py',
                # pass dataset object as an input with name 'titanic'
                inputs=[titanic_ds.as_named_input('titanic')],
                compute_target=compute_target,
                environment_definition= conda_env)

# Submit the estimator as part of your experiment run
experiment_run = experiment.submit(est)
experiment_run.wait_for_completion(show_output=True)
```


## <a name="option-2--mount-files-to-a-remote-compute-target"></a>옵션 2: 파일을 원격 계산 대상에 마운트

학습 대상에서 데이터 파일을 사용할 수 있도록 하려면 [FileDataset을](https://docs.microsoft.com/python/api/azureml-core/azureml.data.file_dataset.filedataset?view=azure-ml-py) 사용하여 데이터 파일을 마운트하거나 다운로드합니다.

### <a name="mount-vs-download"></a>마운트 vs. 다운로드

모든 형식의 파일 탑재 또는 다운로드는 Azure Blob 저장소, Azure 파일, Azure Data Lake 저장소 Gen1, Azure Data Lake 저장소 Gen2, Azure SQL 데이터베이스 및 PostgreSQL용 Azure 데이터베이스에서 만든 데이터 집합에 대해 지원됩니다. 

데이터 집합을 마운트할 때 데이터 집합에서 참조하는 파일을 디렉터리(마운트 포인트)에 연결하고 계산 대상에서 사용할 수 있도록 합니다. Azure 기계 학습 컴퓨팅, 가상 머신 및 HDInsight를 비롯한 Linux 기반 컴퓨팅에 대해 마운팅이 지원됩니다. 데이터 집합을 다운로드하면 데이터 집합에서 참조하는 모든 파일이 계산 대상에 다운로드됩니다. 다운로드는 모든 계산 유형에 대해 지원됩니다. 

스크립트가 데이터 집합에서 참조하는 모든 파일을 처리하고 계산 디스크가 전체 데이터 집합에 맞을 수 있는 경우 저장소 서비스에서 스트리밍 데이터의 오버헤드를 피하기 위해 다운로드하는 것이 좋습니다. 데이터 크기가 계산 디스크 크기를 초과하면 다운로드할 수 없습니다. 이 시나리오에서는 처리 시 스크립트에서 사용하는 데이터 파일만 로드되므로 탑재하는 것이 좋습니다.

다음 코드는 `dataset``mounted_path`

```python
import tempfile
mounted_path = tempfile.mkdtemp()

# mount dataset onto the mounted_path of a Linux-based compute
mount_context = dataset.mount(mounted_path)

mount_context.start()

import os
print(os.listdir(mounted_path))
print (mounted_path)
```

### <a name="create-a-filedataset"></a>FileDataset 만들기

다음 예제는 웹 URL에서 등록되지 않은 FileDataset을 만듭니다. 다른 소스에서 [데이터 집합을 만드는 방법에](https://aka.ms/azureml/howto/createdatasets) 대해 자세히 알아보세요.

```Python
from azureml.core.dataset import Dataset

web_paths = [
            'http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz'
            ]
mnist_ds = Dataset.File.from_files(path = web_paths)
```

### <a name="configure-the-estimator"></a>추정기 구성

추정기의 매개 변수를 통해 데이터 집합을 `inputs` 전달하는 것 외에도 `script_params` 데이터 집합을 통과하고 인수를 통해 학습 스크립트의 데이터 경로(장착 지점)를 얻을 수도 있습니다. 이렇게 하면 azureml-sdk와 독립적으로 학습 스크립트를 유지할 수 있습니다. 즉, 모든 클라우드 플랫폼에서 로컬 디버깅 및 원격 교육에 동일한 교육 스크립트를 사용할 수 있습니다.

[SKLearn](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) 추정 개체는 scikit 학습 실험에 대한 실행을 제출하는 데 사용됩니다. [SKlearn 추정기와](how-to-train-scikit-learn.md)함께 교육에 대해 자세히 알아보십시오.

```Python
from azureml.train.sklearn import SKLearn

script_params = {
    # mount the dataset on the remote compute and pass the mounted path as an argument to the training script
    '--data-folder': mnist_ds.as_named_input('mnist').as_mount(),
    '--regularization': 0.5
}

est = SKLearn(source_directory=script_folder,
              script_params=script_params,
              compute_target=compute_target,
              environment_definition=env,
              entry_script='train_mnist.py')

# Run the experiment
run = experiment.submit(est)
run.wait_for_completion(show_output=True)
```

### <a name="retrieve-the-data-in-your-training-script"></a>교육 스크립트에서 데이터 검색

실행을 제출하면 데이터 집합에서 참조하는 `mnist` 데이터 파일이 계산 대상에 탑재됩니다. 다음 코드는 스크립트에서 데이터를 검색하는 방법을 보여 줍니다.

```Python
%%writefile $script_folder/train_mnist.py

import argparse
import os
import numpy as np
import glob

from utils import load_data

# retrieve the 2 arguments configured through script_params in estimator
parser = argparse.ArgumentParser()
parser.add_argument('--data-folder', type=str, dest='data_folder', help='data folder mounting point')
parser.add_argument('--regularization', type=float, dest='reg', default=0.01, help='regularization rate')
args = parser.parse_args()

data_folder = args.data_folder
print('Data folder:', data_folder)

# get the file paths on the compute
X_train_path = glob.glob(os.path.join(data_folder, '**/train-images-idx3-ubyte.gz'), recursive=True)[0]
X_test_path = glob.glob(os.path.join(data_folder, '**/t10k-images-idx3-ubyte.gz'), recursive=True)[0]
y_train_path = glob.glob(os.path.join(data_folder, '**/train-labels-idx1-ubyte.gz'), recursive=True)[0]
y_test = glob.glob(os.path.join(data_folder, '**/t10k-labels-idx1-ubyte.gz'), recursive=True)[0]

# load train and test set into numpy arrays
X_train = load_data(X_train_path, False) / 255.0
X_test = load_data(X_test_path, False) / 255.0
y_train = load_data(y_train_path, True).reshape(-1)
y_test = load_data(y_test, True).reshape(-1)
```

## <a name="notebook-examples"></a>Notebook 예제

[데이터 집합 전자 필기전자는](https://aka.ms/dataset-tutorial) 이 문서의 개념을 보여 주며 확장합니다.

## <a name="next-steps"></a>다음 단계

* 테이블 형식데이터 집합을 사용하여 [기계 학습 모델을 자동 학습](how-to-auto-train-remote.md)

* FileDataset을 사용하여 [이미지 분류 모델 학습](https://aka.ms/filedataset-samplenotebook)

* [파이프라인을 사용하여 데이터 집합으로 학습](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/pipeline-with-datasets/pipeline-for-image-classification.ipynb)
