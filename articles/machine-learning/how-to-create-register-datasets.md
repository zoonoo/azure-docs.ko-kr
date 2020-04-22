---
title: Azure 기계 학습 데이터 집합을 만들어 데이터에 액세스
titleSuffix: Azure Machine Learning
description: Azure 기계 학습 데이터 집합을 만들어 기계 학습 실험 실행을 위한 데이터에 액세스하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 02/10/2020
ms.openlocfilehash: cc7a8df80e719173c7818055ab8771ddd7f73691
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81682781"
---
# <a name="create-azure-machine-learning-datasets"></a>Azure 기계 학습 데이터 집합 만들기

[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

이 문서에서는 로컬 또는 원격 실험에 대 한 데이터에 액세스 하는 Azure 기계 학습 데이터 집합을 만드는 방법을 알아봅니다.

Azure 기계 학습 데이터 집합을 사용하면 다음을 수행할 수 있습니다.

* 데이터 집합에서 참조하는 단일 데이터 복사본을 저장소에 보관합니다.

* 연결 문자열이나 데이터 경로에 대한 걱정 없이 모델 학습 중에 데이터에 원활하게 액세스할 수 있습니다.

* 데이터를 공유하고 다른 사용자와 공동 작업할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항
' 데이터 집합을 만들고 작업하려면 다음이 필요합니다.

* Azure 구독 구독이 없으면 시작하기 전에 계정을 만드세요. Azure [기계 학습의 무료 또는 유료 버전을](https://aka.ms/AMLFree)사용해 보십시오.

* [Azure 기계 학습 작업 영역](how-to-manage-workspace.md).

* Azureml 데이터 집합 패키지를 포함하는 [파이썬용 Azure 기계 학습 SDK가 설치되었습니다.](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)

> [!NOTE]
> 일부 데이터 집합 클래스에는 [azureml-dataprep](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) 패키지에 대한 종속성이 있습니다. 리눅스 사용자에 대 한, 이러한 클래스는 다음 배포판에서 지원 됩니다.: 레드 햇 엔터프라이즈 리눅스, 우분투, 페도라, 그리고 CentOS.

## <a name="compute-size-guidance"></a>크기 계산 지침

데이터 집합을 만들 때 컴퓨팅 처리 능력과 메모리에 있는 데이터의 크기를 검토합니다. 저장소에 있는 데이터의 크기는 데이터 프레임의 데이터 크기와 다 다. 예를 들어 CSV 파일의 데이터는 데이터 프레임에서 최대 10배까지 확장할 수 있으므로 1GB CSV 파일은 데이터 프레임에서 10GB가 될 수 있습니다. 

주요 요소는 데이터 집합이 메모리 내인지, 즉 데이터 프레임과 같은 크기의 데이터 집합입니다. 계산 크기와 처리 능력은 RAM 크기의 2배가 포함되어 있는 것이 좋습니다. 따라서 데이터 프레임이 10GB인 경우 20GB 이상의 RAM이 있는 계산 대상을 사용하여 데이터 프레임이 메모리에 편안하게 맞고 처리될 수 있도록 해야 합니다. 데이터가 압축되면 더 확장 될 수 있습니다. 압축 마루 형식으로 저장된 20 GB의 비교적 희소 한 데이터는 메모리에서 ~ 800 GB로 확장 할 수 있습니다. Parquet 파일은 열 형식의 데이터를 저장하기 때문에 열의 절반만 필요한 경우 메모리에 ~ 400 GB만로드하면됩니다.
 
팬더를 사용 하는 경우, 그 모든 사용 하기 때문에 1 vCPU 를 가질 이유가 있다. 단일 Azure 기계 학습 계산 인스턴스/노드에서 Modin 및 Dask/Ray를 통해 여러 vCPU에 쉽게 병렬화하고, 필요한 `import pandas as pd` 경우 `import modin.pandas as pd`을 로 변경하기만 하면 큰 클러스터로 확장할 수 있습니다. 
 
데이터에 대해 충분히 큰 가상을 얻을 수 없는 경우 Spark 또는 Dask와 같은 프레임워크를 사용하여 데이터 '메모리 부족'에 대한 처리를 수행합니다( 즉, 데이터 프레임이 파티션별로 RAM 파티션에 로드되고 처리됨)과 최종 결과가 최종 결과에 수집됩니다. 너무 느린 경우 Spark 또는 Dask를 사용하면 대화식으로 계속 사용할 수 있는 클러스터로 확장할 수 있습니다. 

## <a name="dataset-types"></a>데이터 세트 형식

사용자가 교육에서 데이터 집합을 사용하는 방법에 따라 두 가지 데이터 집합 유형이 있습니다.

* [TabularDataset는](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) 제공된 파일 또는 파일 목록을 구문 분석하여 테이블 형식의 데이터를 나타냅니다. 이를 통해 팬더 또는 스파크 데이터프레임으로 데이터를 구체화할 수 있습니다. .csv, `TabularDataset` .tsv, .parquet, .jsonl 파일 및 SQL 쿼리 결과에서 개체를 만들 수 있습니다. 전체 목록은 [TabularDatasetFactory 클래스를](https://aka.ms/tabulardataset-api-reference)참조하십시오.

* [FileDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.file_dataset.filedataset?view=azure-ml-py) 클래스는 데이터스토어 또는 공용 URL의 단일 또는 다중 파일을 참조합니다. 이 방법으로 파일을 다운로드하거나 FileDataset 개체로 계산에 탑재할 수 있습니다. 파일은 모든 형식일 수 있으므로 딥 러닝을 포함한 광범위한 기계 학습 시나리오를 사용할 수 있습니다.

예정된 API 변경 사항에 대한 자세한 내용은 [데이터 집합 API 변경 공지를](https://aka.ms/tabular-dataset)참조하십시오.

## <a name="create-datasets"></a>데이터 세트 만들기

데이터 집합을 만들면 메타데이터의 복사본과 함께 데이터 원본 위치에 대한 참조를 만듭니다. 데이터가 기존 위치에 남아 있으므로 추가 저장소 비용이 발생하지 않습니다. 파이썬 SDK를 `TabularDataset` `FileDataset` 사용하거나 에서 https://ml.azure.com데이터 세트를 모두 만들 수 있습니다.

Azure Machine Learning에서 데이터에 액세스할 수 있도록 Azure [데이터 스토어](how-to-access-data.md) 또는 공용 웹 URL의 경로에서 데이터 집합을 만들어야 합니다. 

### <a name="use-the-sdk"></a>SDK 사용

Python SDK를 사용하여 [Azure 데이터 스토어에서](how-to-access-data.md) 데이터 집합을 만들려면 다음을 수행합니다.

1. 등록된 Azure `contributor` 데이터 `owner` 스토어에 대한 액세스 권한이 있는지 확인합니다.

2. 데이터 스토어에서 경로를 참조하여 데이터 집합을 만듭니다.
> [!Note]
> 여러 데이터 스토어의 여러 경로에서 데이터 집합을 만들 수 있습니다. 데이터 집합을 만들 수 있는 파일 수 또는 데이터 크기에는 하드 제한이 없습니다. 그러나 각 데이터 경로에 대해 파일 또는 폴더를 가리키는지 여부를 확인하기 위해 저장소 서비스로 몇 가지 요청이 전송됩니다. 이 오버헤드로 인해 성능이 저하되거나 실패할 수 있습니다. 내부에 1000개의 파일이 있는 하나의 폴더를 참조하는 데이터 집합은 하나의 데이터 경로를 참조하는 것으로 간주됩니다. 최적의 성능을 위해 데이터스토어에서 100개 미만의 경로를 참조하는 데이터 집합을 만드는 것이 좋습니다.

#### <a name="create-a-tabulardataset"></a>테이블 형식데이터 집합 만들기

클래스의 [`from_delimited_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-delimited-files-path--validate-true--include-path-false--infer-column-types-true--set-column-types-none--separator------header-true--partition-format-none--support-multi-line-false-) 메서드를 `TabularDatasetFactory` 사용하여 .csv 또는 .tsv 형식으로 파일을 읽고 등록되지 않은 TabularDataset을 만듭니다. 여러 파일에서 읽는 경우 결과가 하나의 테이블 형식 표현으로 집계됩니다. 

```Python
from azureml.core import Workspace, Datastore, Dataset

datastore_name = 'your datastore name'

# get existing workspace
workspace = Workspace.from_config()
    
# retrieve an existing datastore in the workspace by name
datastore = Datastore.get(workspace, datastore_name)

# create a TabularDataset from 3 paths in datastore
datastore_paths = [(datastore, 'weather/2018/11.csv'),
                   (datastore, 'weather/2018/12.csv'),
                   (datastore, 'weather/2019/*.csv')]
weather_ds = Dataset.Tabular.from_delimited_files(path=datastore_paths)
```

기본적으로 TabularDataset을 만들 때 열 데이터 형식이 자동으로 유추됩니다. 추론된 형식이 예상과 일치하지 않는 경우 다음 코드를 사용하여 열 형식을 지정할 수 있습니다. 매개 `infer_column_type` 변수는 제한된 파일에서 만든 데이터 집합에만 적용됩니다. [지원되는 데이터 형식에 대해 자세히 알아볼](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.datatype?view=azure-ml-py)수도 있습니다.

> [!IMPORTANT] 
> 저장소가 가상 네트워크 또는 방화벽 뒤에 있는 경우 SDK를 통한 데이터 집합 생성만 지원됩니다. 데이터 집합을 만들려면 매개 변수와 `validate=False` `infer_column_types=False` 메서드를 `from_delimited_files()` 포함해야 합니다. 이렇게 하면 초기 유효성 검사가 무시되고 이러한 보안 파일에서 데이터 집합을 만들 수 있습니다. 

```Python
from azureml.core import Dataset
from azureml.data.dataset_factory import DataType

# create a TabularDataset from a delimited file behind a public web url and convert column "Survived" to boolean
web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path, set_column_types={'Survived': DataType.to_bool()})

# preview the first 3 rows of titanic_ds
titanic_ds.take(3).to_pandas_dataframe()
```

| |승객 ID|Survived|Pclass|이름|섹스|Age|시브 스프 (것)와 같은|양피지|티켓|요금|오두막|착수
-|-----------|--------|------|----|---|---|-----|-----|------|----|-----|--------|
0|1|False|3|브라운드, 오웬 해리스 씨|male|22.0|1|0|A/5 21171|7.2500||S
1|2|True|1|커밍스, 존 브래들리 부인 (피렌체 브릭스|female|38.0|1|0|PC 17599|71.2833|C85|C
2|3|True|3|헤이키넨, 미스. 라나 (주)|female|26.0|0|0|STON/O2. 3101282|7.9250||S


메모리 팬더 데이터 프레임에서 데이터 집합을 만들려면 csv와 같은 로컬 파일에 데이터를 작성하고 해당 파일에서 데이터 집합을 만듭니다. 다음 코드는 이 워크플로를 보여 줍니다.

```python
local_path = 'data/prepared.csv'
dataframe.to_csv(local_path)
upload the local file to a datastore on the cloud
# azureml-core of version 1.0.72 or higher is required
# azureml-dataprep[pandas] of version 1.1.34 or higher is required
from azureml.core import Workspace, Dataset

subscription_id = 'xxxxxxxxxxxxxxxxxxxxx'
resource_group = 'xxxxxx'
workspace_name = 'xxxxxxxxxxxxxxxx'

workspace = Workspace(subscription_id, resource_group, workspace_name)

# get the datastore to upload prepared data
datastore = workspace.get_default_datastore()

# upload the local file from src_dir to the target_path in datastore
datastore.upload(src_dir='data', target_path='data')
# create a dataset referencing the cloud location
dataset = Dataset.Tabular.from_delimited_files(datastore.path('data/prepared.csv'))
```

클래스의 [`from_sql_query()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-sql-query-query--validate-true--set-column-types-none--query-timeout-30-) 메서드를 `TabularDatasetFactory` 사용하여 Azure SQL Database에서 읽을 수 있습니다.

```Python

from azureml.core import Dataset, Datastore

# create tabular dataset from a SQL database in datastore
sql_datastore = Datastore.get(workspace, 'mssql')
sql_ds = Dataset.Tabular.from_sql_query((sql_datastore, 'SELECT * FROM my_table'))
```

TabularDataset에서 데이터 또는 경로 패턴 데이터가 저장되는 모든 위치에서 타임스탬프를 지정하여 시간계 계열 특성을 활성화할 수 있습니다. 이 사양은 시간에 따라 쉽고 효율적인 필터링을 허용합니다.

클래스의 [`with_timestamp_columns()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#with-timestamp-columns-timestamp-none--partition-timestamp-none--validate-false----kwargs-) 메서드를`TabularDataset` 사용하여 타임스탬프 열을 지정하고 시간별로 필터링을 사용하도록 설정합니다. 자세한 내용은 [NOAA 날씨 데이터가 있는 표 열열 관련 API 데모를](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/timeseries-datasets/tabular-timeseries-dataset-filtering.ipynb)참조하십시오.

```Python
# create a TabularDataset with time series trait
datastore_paths = [(datastore, 'weather/*/*/*/data.parquet')]

# get a coarse timestamp column from the path pattern
dataset = Dataset.Tabular.from_parquet_files(path=datastore_path, partition_format='weather/{coarse_time:yyy/MM/dd}/data.parquet')

# set coarse timestamp to the virtual column created, and fine grain timestamp from a column in the data
dataset = dataset.with_timestamp_columns(fine_grain_timestamp='datetime', coarse_grain_timestamp='coarse_time')

# filter with time-series-trait-specific methods
data_slice = dataset.time_before(datetime(2019, 1, 1))
data_slice = dataset.time_after(datetime(2019, 1, 1))
data_slice = dataset.time_between(datetime(2019, 1, 1), datetime(2019, 2, 1))
data_slice = dataset.time_recent(timedelta(weeks=1, days=1))
```

#### <a name="create-a-filedataset"></a>FileDataset 만들기

클래스의 [`from_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py#from-files-path--validate-true-) 메서드를 `FileDatasetFactory` 사용하여 모든 형식으로 파일을 로드하고 등록되지 않은 FileDataset을 만듭니다. 저장소가 가상 네트워크 또는 방화벽 뒤에 있는 `validate =False` 경우 `from_files()` 메서드에서 매개 변수를 설정합니다. 이렇게 하면 초기 유효성 검사 단계를 무시하고 이러한 보안 파일에서 데이터 집합을 만들 수 있습니다.

```Python
# create a FileDataset pointing to files in 'animals' folder and its subfolders recursively
datastore_paths = [(datastore, 'animals')]
animal_ds = Dataset.File.from_files(path=datastore_paths)

# create a FileDataset from image and label files behind public web urls
web_paths = ['https://azureopendatastorage.blob.core.windows.net/mnist/train-images-idx3-ubyte.gz',
             'https://azureopendatastorage.blob.core.windows.net/mnist/train-labels-idx1-ubyte.gz']
mnist_ds = Dataset.File.from_files(path=web_paths)
```

#### <a name="on-the-web"></a>웹에서 
다음 단계 및 애니메이션에서는 Azure 기계 학습 스튜디오에서 데이터 https://ml.azure.com집합을 만드는 방법을 보여 주며,

![UI를 사용하여 데이터 집합 만들기](./media/how-to-create-register-datasets/create-dataset-ui.gif)

스튜디오에서 데이터 집합을 만들려면 다음을 수행합니다.
1. 에서 https://ml.azure.com로그인합니다.
1. 왼쪽 창의 **자산** 섹션에서 **데이터 집합을** 선택합니다. 
1. **데이터 집합 만들기를** 선택하여 데이터 집합의 원본을 선택합니다. 이 소스는 로컬 파일, 데이터스토어 또는 공용 URL일 수 있습니다.
1. 데이터 집합 유형에 대한 **테이블 형식** 또는 **파일을** 선택합니다.
1. **데이터스토어 및 파일 선택** 양식을 열려면 **다음을** 선택합니다. 이 양식에서는 생성 후 데이터 집합을 유지할 위치를 선택하고 데이터 집합에 사용할 데이터 파일을 선택합니다. 
1. 설정 및 미리 **보기 및** **스키마** 양식을 채우려면 **다음을** 선택합니다. 파일 형식에 따라 지능적으로 채워지며 이러한 양식을 만들기 전에 데이터 집합을 추가로 구성할 수 있습니다. 
1. **다음을** 선택하여 세부 정보 확인 양식을 **검토합니다.** 선택 항목을 확인하고 데이터 집합에 대한 선택적 데이터 프로필을 만듭니다. [데이터 프로파일링](how-to-use-automated-ml-for-ml-models.md#profile)에 대한 자세한 정보 
1. 데이터 집합 생성을 완료하려면 **만들기를** 선택합니다.

## <a name="register-datasets"></a>데이터 집합 등록

생성 프로세스를 완료하려면 데이터 집합을 작업 영역에 등록합니다. 이 [`register()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.abstract_dataset.abstractdataset?view=azure-ml-py#register-workspace--name--description-none--tags-none--create-new-version-false-) 방법을 사용하여 작업 영역에 데이터 집합을 등록하여 다른 사용자와 공유하고 다양한 실험에서 다시 사용할 수 있습니다.

```Python
titanic_ds = titanic_ds.register(workspace=workspace,
                                 name='titanic_ds',
                                 description='titanic training data')
```

> [!Note]
> Azure 기계 학습 스튜디오를 통해 생성된 데이터 집합은 작업 영역에 자동으로 등록됩니다.

## <a name="create-datasets-with-azure-open-datasets"></a>Azure 개방형 데이터 집합을 사용하여 데이터 집합 만들기

[Azure Open Datasets](https://azure.microsoft.com/services/open-datasets/)는 기계 학습 솔루션에 시나리오별 기능을 추가하여 보다 정확한 모델을 만들 수 있는 큐레이팅된 공개 데이터 세트입니다. 데이터 세트에는 기계 학습 모델을 학습시키고 예측 솔루션을 보강할 수 있는 날씨, 인구, 휴일, 공공 안전 및 위치에 대한 공개 도메인 데이터가 포함되어 있습니다. 개방형 데이터 집합은 Microsoft Azure의 클라우드에 있으며 SDK 및 작업 영역 UI에 모두 포함됩니다.

### <a name="use-the-sdk"></a>SDK 사용

SDK에서 Azure Open 데이터 집합을 사용하여 데이터 집합을 만들려면 `pip install azureml-opendatasets`을 사용하여 패키지를 설치했는지 확인합니다. 각 개별 데이터 집합은 SDK의 자체 클래스로 표시되며 특정 클래스는 `TabularDataset` `FileDataset`에서 로 사용할 수 있습니다. 전체 클래스 목록은 [참조 설명서를](https://docs.microsoft.com/python/api/azureml-opendatasets/azureml.opendatasets?view=azure-ml-py) 참조하십시오.

파일을 직접 조작 및/또는 다운로드할 수 있는 특정 클래스를 `TabularDataset` 또는 `FileDataset`로 검색할 수 있습니다. 다른 클래스는 중 하나 `get_tabular_dataset()` 또는 `get_file_dataset()` 함수를 사용하여 데이터 집합을 얻을 수 있습니다. **only** 다음 코드 샘플에서는 이러한 유형의 클래스에 대한 몇 가지 예를 보여 주며 있습니다.

```python
from azureml.opendatasets import MNIST

# MNIST class can return either TabularDataset or FileDataset
tabular_dataset = MNIST.get_tabular_dataset()
file_dataset = MNIST.get_file_dataset()

from azureml.opendatasets import Diabetes

# Diabetes class can return ONLY TabularDataset and must be called from the static function
diabetes_tabular = Diabetes.get_tabular_dataset()
```

Open Dataset에서 만든 데이터 집합을 등록하면 데이터가 즉시 다운로드되지 않지만 중앙 저장소 위치에서 요청된 경우 나중에 데이터에 액세스합니다(예: 교육 중).

### <a name="use-the-ui"></a>UI 사용

UI를 통해 열린 데이터 집합 클래스에서 데이터 집합을 만들 수도 있습니다. 작업 영역에서 **자산**에서 **데이터 집합** 탭을 선택합니다. 데이터 **집합** 드롭다운 만들기 메뉴에서 **데이터 집합 열기에서**를 선택합니다.

![UI가 있는 데이터 집합 열기](./media/how-to-create-register-datasets/open-datasets-1.png)

타일을 선택하여 데이터 집합을 선택합니다. (검색 모음을 사용하여 필터링할 수 있는 옵션이 있습니다.) **다음을**선택합니다.

![데이터 집합 선택](./media/how-to-create-register-datasets/open-datasets-2.png)

데이터 집합을 등록할 이름을 선택하고 사용 가능한 필터를 사용하여 선택적으로 데이터를 필터링합니다. 이 경우 공휴일 데이터 집합의 경우 기간을 1년으로 필터링하고 국가 코드를 미국으로만 필터링합니다. **만들기**를 선택합니다.

![데이터 집합 매개 변수 설정 및 데이터 집합 만들기](./media/how-to-create-register-datasets/open-datasets-3.png)

이제 데이터 집합을 **데이터 집합**아래의 작업 영역에서 사용할 수 있습니다. 만든 다른 데이터 집합과 동일한 방식으로 사용할 수 있습니다.

## <a name="version-datasets"></a>버전 데이터 집합

새 버전을 만들어 동일한 이름으로 새 데이터 집합을 등록할 수 있습니다. 데이터 집합 버전은 데이터 상태를 북마크하여 실험 또는 향후 재생을 위해 데이터 집합의 특정 버전을 적용할 수 있는 방법입니다. [데이터 집합 버전에](how-to-version-track-datasets.md)대해 자세히 알아봅니다.
```Python
# create a TabularDataset from Titanic training data
web_paths = ['https://dprepdata.blob.core.windows.net/demo/Titanic.csv',
             'https://dprepdata.blob.core.windows.net/demo/Titanic2.csv']
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_paths)

# create a new version of titanic_ds
titanic_ds = titanic_ds.register(workspace = workspace,
                                 name = 'titanic_ds',
                                 description = 'new titanic training data',
                                 create_new_version = True)
```

## <a name="access-datasets-in-your-script"></a>스크립트의 데이터 집합에 액세스

등록된 데이터 집합은 Azure Machine Learning 계산과 같은 계산 클러스터에서 로컬 및 원격으로 액세스할 수 있습니다. 실험 에서 등록된 데이터 집합에 액세스하려면 다음 코드를 사용하여 작업 영역 및 등록된 데이터 집합에 이름으로 액세스합니다. 기본적으로 클래스의 [`get_by_name()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-by-name-workspace--name--version--latest--) `Dataset` 메서드는 작업 영역에 등록된 데이터 집합의 최신 버전을 반환합니다.

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

* [데이터 집합으로 학습하는 방법에](how-to-train-with-datasets.md)대해 알아봅니다.
* 자동화된 기계 학습을 사용하여 [TabularDataset으로 학습합니다.](https://aka.ms/automl-dataset)
* 자세한 데이터 집합 학습 예제는 [샘플 전자 필기장을](https://aka.ms/dataset-tutorial)참조하십시오.
