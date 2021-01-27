---
title: Machine learning 데이터 집합으로 학습
titleSuffix: Azure Machine Learning
description: Azure Machine Learning 데이터 집합을 사용 하 여 모델 학습을 위해 로컬 또는 원격 계산에서 데이터를 사용할 수 있도록 하는 방법에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 07/31/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, data4ml
ms.openlocfilehash: 688bec24cbcd88130470634abff0688ead8005ef
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98881689"
---
# <a name="train-models-with-azure-machine-learning-datasets"></a>Azure Machine Learning 데이터 집합을 사용 하 여 모델 학습 

이 문서에서는 [Azure Machine Learning 데이터 집합](/python/api/azureml-core/azureml.core.dataset%28class%29?preserve-view=true&view=azure-ml-py) 을 사용 하 여 기계 학습 모델을 학습 하는 방법에 대해 알아봅니다.  연결 문자열 또는 데이터 경로에 대 한 걱정 없이 로컬 또는 원격 계산 대상에서 데이터 집합을 사용할 수 있습니다. 

Azure Machine Learning 데이터 집합은 [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig?preserve-view=true&view=azure-ml-py), [hyperdrive](/python/api/azureml-train-core/azureml.train.hyperdrive?preserve-view=true&view=azure-ml-py) 및 [Azure Machine Learning 파이프라인과](./how-to-create-machine-learning-pipelines.md)같은 Azure Machine Learning 학습 기능과 원활한 통합을 제공 합니다.

데이터를 모델 학습에 사용할 수 있도록 준비 하지 않았지만 데이터 탐색을 위해 데이터를 전자 필기장에 로드 하려는 경우 데이터 [집합에서 데이터를 탐색](how-to-create-register-datasets.md#explore-data)하는 방법을 참조 하세요. 

## <a name="prerequisites"></a>선행 조건

데이터 집합을 만들고 학습 하려면 다음이 필요 합니다.

* Azure 구독 Azure 구독이 없는 경우 시작하기 전에 체험 계정을 만듭니다. 지금 [Azure Machine Learning 평가판 또는 유료 버전](https://aka.ms/AMLFree)을 사용해 보세요.

* [Azure Machine Learning 작업 영역](how-to-manage-workspace.md)입니다.

* 패키지를 포함 하는 [Python 용 AZURE MACHINE LEARNING SDK 설치](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py) (>= 1.13.0) `azureml-datasets` .

> [!Note]
> 일부 데이터 집합 클래스에는 [azureml-dataprep](/python/api/azureml-dataprep/?preserve-view=true&view=azure-ml-py) 패키지에 대 한 종속성이 있습니다. Linux 사용자의 경우 이러한 클래스는 Red Hat Enterprise Linux, Ubuntu, Fedora 및 CentOS 배포판 에서만 지원 됩니다.

## <a name="consume-datasets-in-machine-learning-training-scripts"></a>기계 학습 교육 스크립트에서 데이터 집합 사용

구조화 된 데이터를 데이터 집합으로 아직 등록 하지 않은 경우 TabularDataset를 만들어 로컬 또는 원격 실험을 위한 학습 스크립트에서 직접 사용 합니다.

이 예에서는 등록 되지 않은 [TabularDataset](/python/api/azureml-core/azureml.data.tabulardataset?preserve-view=true&view=azure-ml-py) 을 만들고 학습을 위해 ScriptRunConfig 개체에서 스크립트 인수로 지정 합니다. 작업 영역의 다른 실험에서이 TabularDataset를 다시 사용 하려면 [작업 영역에 데이터 집합을 등록 하는 방법](how-to-create-register-datasets.md#register-datasets)을 참조 하세요.

### <a name="create-a-tabulardataset"></a>TabularDataset 만들기

다음 코드는 웹 url에서 등록 되지 않은 TabularDataset을 만듭니다.  

```Python
from azureml.core.dataset import Dataset

web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path)
```

TabularDataset 개체는 TabularDataset의 데이터를 pandas 또는 Spark 데이터 프레임로 로드 하는 기능을 제공 하므로 노트북을 떠나지 않고도 익숙한 데이터 준비 및 학습 라이브러리를 사용할 수 있습니다.

### <a name="access-dataset-in-training-script"></a>학습 스크립트의 데이터 집합 액세스

다음 코드는 `--input-data` 학습 실행을 구성할 때 지정할 스크립트 인수를 구성 합니다 (다음 섹션 참조). 테이블 형식 데이터 집합이 인수 값으로 전달 되는 경우 Azure ML은 데이터 집합의 ID로이를 확인 하 고,이를 사용 하 여 학습 스크립트의 데이터 집합에 액세스할 수 있습니다 (스크립트에서 데이터 집합의 이름 또는 ID를 하드 코딩 하지 않음). 그런 다음 메서드를 사용 하 여 [`to_pandas_dataframe()`](/python/api/azureml-core/azureml.data.tabulardataset#to-pandas-dataframe-on-error--null---out-of-range-datetime--null--) 학습 전에 추가 데이터 탐색 및 준비를 위해 해당 데이터 집합을 pandas 데이터 프레임로 로드 합니다.

> [!Note]
> 원래 데이터 원본에 NaN, 빈 문자열 또는 빈 값이 포함 된 경우를 사용 하면 `to_pandas_dataframe()` 해당 값이 *Null* 값으로 대체 됩니다.

메모리 내 pandas 데이터 프레임에서 준비 된 데이터를 새 데이터 집합으로 로드 해야 하는 경우 데이터를 parquet와 같은 로컬 파일에 기록 하 고 해당 파일에서 새 데이터 집합을 만듭니다. [데이터 집합을 만드는 방법](how-to-create-register-datasets.md)에 대해 자세히 알아보세요.

```Python
%%writefile $script_folder/train_titanic.py

import argparse
from azureml.core import Dataset, Run

parser = argparse.ArgumentParser()
parser.add_argument("--input-data", type=str)
args = parser.parse_args()

run = Run.get_context()
ws = run.experiment.workspace

# get the input dataset by ID
dataset = Dataset.get_by_id(ws, id=args.input_data)

# load the TabularDataset to pandas DataFrame
df = dataset.to_pandas_dataframe()
```

### <a name="configure-the-training-run"></a>학습 실행 구성

[ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrun?preserve-view=true&view=azure-ml-py) 개체는 학습 실행을 구성 하 고 제출 하는 데 사용 됩니다.

이 코드는를 `src` 지정 하는 ScriptRunConfig 개체를 만듭니다.

* 스크립트에 대 한 스크립트 디렉터리입니다. 이 디렉터리의 모든 파일은 실행을 위해 클러스터 노드로 업로드됩니다.
* 학습 스크립트 *train_titanic. py*.
* 스크립트 인수로 사용할 학습의 입력 데이터 집합 `titanic_ds` 입니다. Azure ML은 스크립트에 전달 될 때 데이터 집합의 해당 ID로이를 확인 합니다.
* 실행에 대 한 계산 대상입니다.
* 실행에 대 한 환경입니다.

```python
from azureml.core import ScriptRunConfig

src = ScriptRunConfig(source_directory=script_folder,
                      script='train_titanic.py',
                      # pass dataset as an input with friendly name 'titanic'
                      arguments=['--input-data', titanic_ds.as_named_input('titanic')],
                      compute_target=compute_target,
                      environment=myenv)
                             
# Submit the run configuration for your training run
run = experiment.submit(src)
run.wait_for_completion(show_output=True)                             
```

## <a name="mount-files-to-remote-compute-targets"></a>원격 계산 대상에 파일 탑재

구조화 되지 않은 데이터가 있는 경우 [Filedataset](/python/api/azureml-core/azureml.data.filedataset?preserve-view=true&view=azure-ml-py) 을 만들고 데이터 파일을 탑재 하거나 다운로드 하 여 교육을 위해 원격 계산 대상에서 사용할 수 있도록 합니다. 원격 학습 실험을 위해 [탑재 및 다운로드](#mount-vs-download) 를 사용 하는 경우에 대해 알아봅니다. 

다음 예에서는 FileDataset을 만들고 학습 스크립트에 인수로 전달 하 여 계산 대상에 데이터 집합을 탑재 합니다. 

> [!Note]
> 사용자 지정 Docker 기본 이미지를 사용 하는 경우 `apt-get install -y fuse` 작업을 수행 하려면 데이터 집합 탑재에 대 한 종속성으로를 통해 퓨즈를 설치 해야 합니다. [사용자 지정 빌드 이미지를 빌드하](how-to-deploy-custom-docker-image.md#build-a-custom-base-image)는 방법에 대해 알아봅니다.

### <a name="create-a-filedataset"></a>FileDataset 만들기

다음 예제에서는 웹 url에서 등록 되지 않은 FileDataset을 만듭니다. 다른 원본에서 [데이터 집합을 만드는 방법](how-to-create-register-datasets.md) 에 대해 자세히 알아보세요.

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

### <a name="configure-the-training-run"></a>학습 실행 구성

`arguments`생성자의 매개 변수를 통해 탑재할 때 데이터 집합을 인수로 전달 하는 것이 좋습니다 `ScriptRunConfig` . 이렇게 하면 인수를 통해 학습 스크립트에서 데이터 경로 (탑재 지점)를 얻게 됩니다. 이러한 방식으로 모든 클라우드 플랫폼에서 로컬 디버깅 및 원격 교육에 대해 동일한 학습 스크립트를 사용할 수 있습니다.

다음 예에서는를 통해 FileDataset에 전달 되는 ScriptRunConfig를 만듭니다 `arguments` . 실행을 제출한 후에는 데이터 집합에서 참조 하는 데이터 파일이 `mnist_ds` 계산 대상으로 탑재 됩니다.

```python
from azureml.core import ScriptRunConfig

src = ScriptRunConfig(source_directory=script_folder,
                      script='train_mnist.py',
                      # the dataset will be mounted on the remote compute and the mounted path passed as an argument to the script
                      arguments=['--data-folder', mnist_ds.as_mount(), '--regularization', 0.5],
                      compute_target=compute_target,
                      environment=myenv)

# Submit the run configuration for your training run
run = experiment.submit(src)
run.wait_for_completion(show_output=True)
```

### <a name="retrieve-data-in-your-training-script"></a>학습 스크립트에서 데이터 검색

다음 코드에서는 스크립트에서 데이터를 검색 하는 방법을 보여 줍니다.

```Python
%%writefile $script_folder/train_mnist.py

import argparse
import os
import numpy as np
import glob

from utils import load_data

# retrieve the 2 arguments configured through `arguments` in the ScriptRunConfig
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

## <a name="mount-vs-download"></a>탑재 및 다운로드

모든 형식의 파일을 탑재 하거나 다운로드 하는 것은 Azure Blob storage, Azure Files, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure SQL Database 및 Azure Database for PostgreSQL에서 만든 데이터 집합에 대해 지원 됩니다. 

데이터 집합을 **탑재** 하는 경우 데이터 집합에서 참조 하는 파일을 디렉터리 (탑재 지점)에 연결 하 여 계산 대상에서 사용할 수 있도록 합니다. 탑재는 Azure Machine Learning 계산, 가상 컴퓨터 및 HDInsight를 포함 하 여 Linux 기반 계산에 대해 지원 됩니다. 

데이터 집합을 **다운로드** 하면 데이터 집합에서 참조 하는 모든 파일이 계산 대상으로 다운로드 됩니다. 다운로드는 모든 계산 형식에 대해 지원 됩니다. 

스크립트가 데이터 집합에서 참조 하는 모든 파일을 처리 하 고 계산 디스크가 전체 데이터 집합에 적합 한 경우 storage 서비스에서 데이터를 스트리밍하는 오버 헤드를 방지 하기 위해를 다운로드 하는 것이 좋습니다. 데이터 크기가 계산 디스크 크기를 초과 하는 경우 다운로드를 수행할 수 없습니다. 이 시나리오에서는 스크립트에서 사용 하는 데이터 파일만 처리할 때 로드 되므로 탑재 하는 것이 좋습니다.

다음 코드는 `dataset` 에서 임시 디렉터리에 탑재 합니다. `mounted_path`

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

## <a name="get-datasets-in-machine-learning-scripts"></a>기계 학습 스크립트에서 데이터 집합 가져오기

등록 된 데이터 집합은 Azure Machine Learning 계산 등의 계산 클러스터에서 로컬로 또는 원격으로 액세스할 수 있습니다. 실험에서 등록 된 데이터 집합에 액세스 하려면 다음 코드를 사용 하 여 작업 영역에 액세스 하 고 이전에 제출 된 실행에서 사용 된 데이터 집합을 가져옵니다. 기본적으로 [`get_by_name()`](/python/api/azureml-core/azureml.core.dataset.dataset?preserve-view=true&view=azure-ml-py#&preserve-view=trueget-by-name-workspace--name--version--latest--) 클래스의 메서드는 `Dataset` 작업 영역에 등록 된 데이터 집합의 최신 버전을 반환 합니다.

```Python
%%writefile $script_folder/train.py

from azureml.core import Dataset, Run

run = Run.get_context()
workspace = run.experiment.workspace

dataset_name = 'titanic_ds'

# Get a dataset by name
titanic_ds = Dataset.get_by_name(workspace=workspace, name=dataset_name)

# Load a TabularDataset into pandas DataFrame
df = titanic_ds.to_pandas_dataframe()
```

## <a name="access-source-code-during-training"></a>학습 중 소스 코드 액세스

Azure Blob Storage는 Azure 파일 공유보다 처리 속도가 빠르며 동시에 시작되는 여러 작업으로 스케일링됩니다. 이러한 이유로 소스 코드 파일을 전송할 때 Blob Storage를 사용하도록 실행을 구성하는 것이 좋습니다.

다음 코드 예제는 실행 구성에서 소스 코드 전송에 사용할 BLOB 데이터 저장소를 지정합니다.

```python 
# workspaceblobstore is the default blob storage
src.run_config.source_directory_data_store = "workspaceblobstore" 
```

## <a name="notebook-examples"></a>Notebook 예제

+ [데이터 집합 노트북](https://aka.ms/dataset-tutorial) 은이 문서의 개념을 시연 하 고 확장 합니다.
+ [ML 파이프라인에서 데이터 집합을 parametrize](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-showcasing-dataset-and-pipelineparameter.ipynb)하는 방법을 참조 하세요.

## <a name="troubleshooting"></a>문제 해결

* **데이터 집합 초기화 실패: 탑재 지점의 준비 대기 시간이 초과 되었습니다**. 
  * 아웃 바운드 [네트워크 보안 그룹](../virtual-network/network-security-groups-overview.md) 규칙이 없고를 사용 하는 경우 `azureml-sdk>=1.12.0` , 업데이트 `azureml-dataset-runtime` 및 해당 종속성이 특정 부 버전에 대 한 최신 버전으로 업데이트 되 고, 해당 종속성이 특정 부 버전에 대 한 최신 패치를 사용 하는 경우, 최신 패치를 수정 프로그램과 함께 사용할 수 있도록 환경을 다시 만드세요. 
  * 를 사용 하는 경우 `azureml-sdk<1.12.0` 최신 버전으로 업그레이드 합니다.
  * 아웃 바운드 NSG 규칙이 있는 경우 서비스 태그의 모든 트래픽을 허용 하는 아웃 바운드 규칙이 있는지 확인 합니다 `AzureResourceMonitor` .

### <a name="overloaded-azurefile-storage"></a>오버 로드 된 AzureFile 저장소

오류가 표시 `Unable to upload project files to working directory in AzureFile because the storage is overloaded` 되 면 다음 해결 방법을 적용 합니다.

데이터 전송과 같은 다른 작업에 대해 파일 공유를 사용 하는 경우 파일 공유를 사용 하 여 실행을 제출할 수 있도록 blob을 사용 하는 것이 좋습니다. 작업을 서로 다른 두 작업 영역 간에 분할할 수도 있습니다.

### <a name="passing-data-as-input"></a>데이터를 입력으로 전달

*  **TypeError: FileNotFound: 해당 파일 또는 디렉터리가 없습니다**.이 오류는 사용자가 제공 하는 파일 경로가 파일이 있는 위치가 아닌 경우에 발생 합니다. 계산 대상에서 데이터 집합을 탑재 한 위치와 파일을 참조 하는 방법이 일치 하는지 확인 해야 합니다. 결정적 상태를 보장 하려면 계산 대상에 데이터 집합을 탑재할 때 추상 경로를 사용 하는 것이 좋습니다. 예를 들어 다음 코드에서 계산 대상의 파일 시스템 루트 아래에 데이터 집합을 탑재 `/tmp` 합니다. 
    
    ```python
    # Note the leading / in '/tmp/dataset'
    script_params = {
        '--data-folder': dset.as_named_input('dogscats_train').as_mount('/tmp/dataset'),
    } 
    ```

    선행 슬래시 ('/')를 포함 하지 않는 경우 `/mnt/batch/.../tmp/dataset` 데이터 집합을 탑재할 위치를 나타내기 위해 계산 대상에서 작업 디렉터리 (예:)에 접두사를 추가 해야 합니다.


## <a name="next-steps"></a>다음 단계

* TabularDatasets를 사용 하 여 [기계 학습 모델을 자동으로 학습](how-to-auto-train-remote.md) 합니다.

* FileDatasets을 사용 하 여 [이미지 분류 모델을 학습](https://aka.ms/filedataset-samplenotebook) 합니다.

* [파이프라인을 사용 하 여 데이터 집합으로 학습](./how-to-create-machine-learning-pipelines.md)합니다.