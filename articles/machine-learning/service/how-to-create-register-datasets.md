---
title: Azureml 데이터 집합을 사용 하 여 데이터에 액세스 하는 데이터 집합 만들기
titleSuffix: Azure Machine Learning
description: 다양 한 원본에서 데이터 집합을 만들고 작업 영역에 데이터 집합을 등록 하는 방법에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 08/22/2019
ms.openlocfilehash: 6c3a8d62bd6b3650f834540bd7bb13027792b091
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71076978"
---
# <a name="create-and-access-datasets-preview-in-azure-machine-learning"></a>Azure Machine Learning에서 데이터 집합 만들기 및 액세스 (미리 보기)

이 문서에서는 Azure Machine Learning 데이터 집합을 만드는 방법 (미리 보기)과 로컬 또는 원격 실험에서 데이터에 액세스 하는 방법에 대해 알아봅니다.

Azure Machine Learning 데이터 집합을 사용 하 여 다음을 수행할 수 있습니다. 

* 데이터 집합에서 참조 하 **는 저장소에 데이터의 단일 복사본을 유지** 합니다. 

* 연결 문자열 또는 데이터 경로에 대해 걱정 하지 않고 **모델 학습 중에 데이터에 쉽게 액세스** 합니다.

* 다른 사용자와 **공동으로 데이터 & 공유** 합니다.

## <a name="prerequisites"></a>전제 조건

데이터 집합을 만들고 작업 하려면 다음이 필요 합니다.

* Azure 구독. Azure 구독이 없는 경우 시작하기 전에 체험 계정을 만듭니다. 현재 [Azure Machine Learning의 무료 또는 유료 버전](https://aka.ms/AMLFree) 을 사용해 보세요.

* [Azure Machine Learning 작업 영역](how-to-manage-workspace.md)

* Azureml 데이터 집합 패키지가 포함 된 [Python 용 AZURE MACHINE LEARNING SDK가 설치 되어](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)있습니다.

> [!Note]
> 일부 데이터 집합 클래스 (미리 보기)에는 [azureml-dataprep](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) 패키지에 대 한 종속성이 있습니다. Linux 사용자의 경우 이러한 클래스는 다음 배포판 에서만 지원 됩니다.  Red Hat Enterprise Linux, Ubuntu, Fedora 및 CentOS입니다.

## <a name="dataset-types"></a>데이터 집합 형식

데이터 집합은 사용자가 학습에서 사용 하는 방법에 따라 두 가지 유형으로 분류 됩니다. 

* [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) 는 제공 된 파일 또는 파일 목록을 구문 분석 하 여 테이블 형식으로 데이터를 나타냅니다. 이를 통해 pandas 데이터 프레임로 데이터를 구체화할 수 있습니다. Csv, tsv, parquet 파일, SQL 쿼리 결과 등에서 개체를만들수있습니다.`TabularDataset` 전체 목록은 [설명서](https://aka.ms/tabulardataset-api-reference)를 참조 하세요.

* [Filedataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.file_dataset.filedataset?view=azure-ml-py) 은 데이터 저장소 또는 public url의 단일 또는 여러 파일을 참조 합니다. 그러면 계산에 파일을 다운로드 하거나 탑재 하는 기능이 제공 됩니다. 이 파일은 심층 학습을 비롯 한 다양 한 기계 학습 시나리오를 가능 하 게 하는 모든 형식일 수 있습니다.

예정 된 API 변경에 대 한 자세한 내용은 [여기](https://aka.ms/tabular-dataset)를 참조 하세요.

## <a name="create-datasets"></a>데이터 세트 만들기 

데이터 집합을 만들면 데이터 원본 위치에 대 한 참조와 해당 메타 데이터의 복사본을 만듭니다. 데이터는 기존 위치에 그대로 남아 있으므로 추가 저장소 비용이 발생 하지 않습니다.

Azure Machine Learning에서 데이터에 액세스할 수 있도록 하려면 [Azure 데이터 저장소](how-to-access-data.md) 또는 공용 웹 url의 경로에서 데이터 집합을 만들어야 합니다.

[Azure 데이터 저장소](how-to-access-data.md)에서 데이터 집합을 만들려면 다음을 수행 합니다.

* 등록 된 Azure `contributor` 데이터 `owner` 저장소에 대 한 액세스 권한이 있는지 확인 합니다.

* 데이터 저장소에서 경로를 참조 하 여 데이터 집합을 만듭니다.

```Python
from azureml.core.workspace import Workspace
from azureml.core.datastore import Datastore
from azureml.core.dataset import Dataset

datastore_name = 'your datastore name'

# get existing workspace
workspace = Workspace.from_config()

# retrieve an existing datastore in the workspace by name
datastore = Datastore.get(workspace, datastore_name)
```

### <a name="create-tabulardatasets"></a>TabularDatasets 만들기

TabularDatasets는 SDK를 통해 또는 작업 영역 방문 페이지 (미리 보기)를 사용 하 여 만들 수 있습니다. 타임 스탬프는 데이터의 열에서 지정 하거나 데이터를에 저장 하 여 시계열 특성을 사용할 수 있습니다 .이 특성을 사용 하면 시간에 따라 쉽고 효율적으로 필터링 할 수 있습니다. 

#### <a name="using-the-sdk"></a>SDK 사용 

클래스의 [`from_delimited_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-delimited-files-path--validate-true--include-path-false--infer-column-types-true--set-column-types-none--separator------header--promoteheadersbehavior-all-files-have-same-headers--3---partition-format-none-) `TabularDatasetFactory` 메서드를 사용 하 여 csv 또는 tsv 형식의 파일을 읽고 등록 되지 않은 TabularDataset를 만듭니다. 여러 파일에서 읽는 경우 결과는 하나의 테이블 형식 표현으로 집계 됩니다.

```Python
# create a TabularDataset from multiple paths in datastore
datastore_paths = [
                  (datastore, 'weather/2018/11.csv'),
                  (datastore, 'weather/2018/12.csv'),
                  (datastore, 'weather/2019/*.csv')
                 ]
weather_ds = Dataset.Tabular.from_delimited_files(path=datastore_paths)

# create a TabularDataset from a delimited file behind a public web url
web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path)

# preview the first 3 rows of titanic_ds
titanic_ds.take(3).to_pandas_dataframe()
```

| |PassengerId|Survived|Pclass|이름|성|나이|SibSp|Parch|적합|택시|Cabin|Embarked
-|-----------|--------|------|----|---|---|-----|-----|------|----|-----|--------|
0|1|0|3|Braund, Mr. Owen Harris|male|22.0|1|0|A/5 21171|7.2500||S
1|2|1|1|Cumings, Mrs Bradley (Florence Briggs Th ...|female|38.0|1|0|PC 17599|71.2833|C85|C
2|3|1|3|Heikkinen, 누락. Laina|female|26.0|0|0|STON/O2. 3101282|7.9250||S

클래스의 [`with_timestamp_columns()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#with-timestamp-columns-fine-grain-timestamp--coarse-grain-timestamp-none--validate-false-) `TabularDataset` 메서드를 사용 하 여 시간에 따라 쉽고 효율적인 필터링을 사용할 수 있습니다. 더 많은 예제 및 세부 정보는 [여기](http://aka.ms/azureml-tsd-notebook)에서 확인할 수 있습니다. 

```Python
# create a TabularDataset with timeseries trait
datastore_paths = [(datastore, 'weather/*/*/*/data.parquet')]

# get a coarse timestamp column from the path pattern
dataset = Dataset.Tabular.from_parquet_files(path=datastore_path, partition_format='weather/{coarse_time:yyy/MM/dd}/data.parquet')

# set coarse timestamp to the virtual column created, and fine grain timestamp from a column in the data
dataset = dataset.with_timestamp_columns(fine_grain_timestamp='datetime', coarse_grain_timestamp='coarse_time')

# filter with timeseries trait specific methods 
data_slice = dataset.time_before(datetime(2019, 1, 1))
data_slice = dataset.time_after(datetime(2019, 1, 1))
data_slice = dataset.time_between(datetime(2019, 1, 1), datetime(2019, 2, 1)) 
data_slice = dataset.time_recent(timedelta(weeks=1, days=1))                  
```

#### <a name="using-the-workspace-landing-page"></a>작업 영역 방문 페이지 사용 

[작업 영역 방문 페이지](https://ml.azure.com) 에 로그인 하 여 웹 환경을 통해 데이터 집합을 만듭니다. 현재 작업 영역 방문 페이지는 TabularDatasets 만들기만 지원 합니다.

다음 애니메이션은 작업 영역 방문 페이지에서 데이터 집합을 만드는 방법을 보여 줍니다. 

먼저 왼쪽 창의 **자산** 섹션에서 **데이터 집합** 을 선택 합니다. 그런 다음, **데이터 집합 만들기** 를 선택 하 여 데이터 집합의 원본을 선택 합니다. 이 파일은 로컬 파일, 데이터 저장소 또는 공용 웹 url 중 하나일 수 있습니다. **설정 및 미리 보기** 및 **스키마** 양식은 파일 형식에 따라 지능적으로 채워집니다. **다음** 을 선택 하 여 해당 항목을 검토 하거나 만들기 전에 데이터 집합을 추가로 구성 합니다. **완료** 를 선택 하 여 데이터 집합 만들기를 완료 합니다. 

![UI를 사용 하 여 데이터 집합 만들기](media/how-to-create-register-datasets/create-dataset-ui.gif)

### <a name="create-filedatasets"></a>FileDatasets 만들기

클래스의 [`from_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py#from-files-path--validate-true-) `FileDatasetFactory` 메서드를 사용 하 여 모든 형식의 파일을 로드 하 고 등록 되지 않은 filedataset을 만듭니다.

```Python
# create a FileDataset from multiple paths in datastore
datastore_paths = [
                  (datastore, 'animals/dog/1.jpg'),
                  (datastore, 'animals/dog/2.jpg'),
                  (datastore, 'animals/dog/*.jpg')
                 ]
animal_ds = Dataset.File.from_files(path=datastore_paths)

# create a FileDataset from image and label files behind public web urls
web_paths = [
            'https://azureopendatastorage.blob.core.windows.net/mnist/train-images-idx3-ubyte.gz',
            'https://azureopendatastorage.blob.core.windows.net/mnist/train-labels-idx1-ubyte.gz'
           ]          
mnist_ds = Dataset.File.from_files(path=web_paths)
```

## <a name="register-datasets"></a>데이터 집합 등록

만들기 프로세스를 완료 하려면 작업 영역에 데이터 집합을 등록 합니다.

메서드를 [`register()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#register-workspace--name--description-none--tags-none--visible-true--exist-ok-false--update-if-exist-false-) 사용 하 여 다른 사용자와 공유 하 고 다양 한 실험에서 다시 사용할 수 있도록 작업 영역에 데이터 집합을 등록 합니다.

```Python
titanic_ds = titanic_ds.register(workspace = workspace,
                                 name = 'titanic_ds',
                                 description = 'titanic training data')
```

>[!Note]
> 작업 영역 방문 페이지를 통해 만든 데이터 집합은 작업 영역에 자동으로 등록 됩니다. 

## <a name="version-datasets"></a>버전 데이터 집합

새 버전을 만들어 동일한 이름으로 새 데이터 집합을 등록할 수 있습니다. 데이터 집합 버전은 실험 또는 향후 복제를 위해 데이터 집합의 특정 버전을 적용할 수 있도록 데이터의 상태에 책갈피를 지정 하는 방법입니다. 버전 관리를 고려 하는 일반적인 시나리오: 
* 새 데이터를 다시 학습에 사용할 수 있는 경우.
* 다른 데이터 준비 또는 기능 엔지니어링 방법을 적용 하는 경우

```Python
# create a TabularDataset from Titanic training data
web_paths = [
            'https://dprepdata.blob.core.windows.net/demo/Titanic.csv',
            'https://dprepdata.blob.core.windows.net/demo/Titanic2.csv'
           ]          
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_paths)

# create a new version of titanic_ds
titanic_ds = titanic_ds.register(workspace = workspace,
                                 name = 'titanic_ds',
                                 description = 'new titanic training data',
                                 create_new_version = True)
```


## <a name="access-your-data-during-training"></a>학습 중 데이터 액세스

등록 된 데이터 집합은 Azure Machine Learning 계산과 같은 계산 클러스터에서 로컬로 또는 원격으로 액세스할 수 있습니다. 실험에서 등록 된 데이터 집합에 액세스 하려면 다음 코드를 사용 하 여 작업 영역과 등록 된 데이터 집합을 이름별로 가져옵니다. 기본적 [`get_by_name()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-by-name-workspace--name--version--latest--) 으로 `Dataset` 클래스의 메서드는 작업 영역에 등록 된 데이터 집합의 최신 버전을 반환 합니다.

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

## <a name="next-steps"></a>다음 단계

* 자동화 된 machine learning을 사용 하 여 [TabularDatasets으로 학습](https://aka.ms/automl-dataset)합니다.
* 데이터 집합에 대 한 학습의 추가 예제는 [샘플 노트북](https://aka.ms/dataset-tutorial)을 참조 하세요.
