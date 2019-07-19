---
title: 학습을 위해 데이터 저장소/Blob의 데이터에 액세스
titleSuffix: Azure Machine Learning service
description: 데이터 저장소를 사용하여 Azure Machine Learning Service로 학습 중에 Blob 데이터 스토리지에 액세스하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: minxia
author: mx-iao
ms.reviewer: sgilley
ms.date: 05/24/2019
ms.custom: seodec18
ms.openlocfilehash: 97a4bc20394553b97211763cedaa76c3711306f2
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68319320"
---
# <a name="access-data-from-your-datastores"></a>데이터 저장소에서 데이터 액세스

 Azure Machine Learning 서비스에서 데이터 저장소는 소스 코드를 변경할 필요 없이 저장소에 액세스 하는 계산 위치 독립적 메커니즘입니다. 경로를 매개 변수로 사용 하는 교육 코드를 작성 하 든, Azure Machine Learning 워크플로를 사용 하 여 데이터 저장소 위치에 액세스할 수 있는지 확인 하 고 계산 컨텍스트에서 사용할 수 있게 합니다.

이 방법에는 다음 작업의 예가 나와 있습니다.
* [데이터 저장소 선택](#access)
* [데이터 가져오기](#get)
* [데이터를 데이터 저장소에 업로드 및 다운로드](#up-and-down)
* [학습 중 데이터 저장소 액세스](#train)

## <a name="prerequisites"></a>필수 구성 요소

데이터 저장소를 사용하려면 먼저 [작업 영역](concept-workspace.md)이 필요합니다.

[새 작업 영역을 만들](setup-create-workspace.md#sdk)거나 기존 작업 영역을 검색합니다.

```Python
import azureml.core
from azureml.core import Workspace, Datastore

ws = Workspace.from_config()
```

<a name="access"></a>

## <a name="choose-a-datastore"></a>데이터 저장소 선택

기본 데이터 저장소를 사용 하거나 사용자 고유의 데이터 저장소를 가져올 수 있습니다.

### <a name="use-the-default-datastore-in-your-workspace"></a>작업 영역에서 기본 데이터 저장소 사용

 각 작업 영역에는 바로 사용할 수 있는 등록 된 기본 데이터 저장소가 있습니다.

작업 영역의 기본 데이터 저장소를 가져오려면 다음 코드를 사용합니다.

```Python
ds = ws.get_default_datastore()
```

### <a name="register-your-own-datastore-with-the-workspace"></a>작업 영역을 사용 하 여 고유한 데이터 저장소 등록

기존 Azure Storage가 있는 경우에는 작업 영역의 데이터 저장소로 등록할 수 있습니다. 

<a name="store"></a>

####  <a name="storage-guidance"></a>저장소 지침

Blob storage 및 blob datastores를 권장 합니다. Blob에는 standard 및 premium storage를 모두 사용할 수 있습니다. 비용이 더 많이 들지만, 특히 큰 데이터 집합에 대해 학습 하는 경우 학습 실행 속도를 향상 시킬 수 있는 더 빠른 처리량 속도 때문에 premium storage를 제안 합니다. 저장소 계정 비용 정보는 [Azure 가격 계산기](https://azure.microsoft.com/pricing/calculator/?service=machine-learning-service) 를 참조 하세요.

>[!NOTE]
> Azure Machine Learning 서비스는 특정 시나리오에 유용할 수 있는 다른 유형의 데이터 저장소를 지원 합니다. 예를 들어 데이터베이스에 저장 된 데이터를 사용 하 여 학습 해야 하는 경우 AzureSQLDatabaseDatastore 또는 AzurePostgreSqlDatastore를 사용할 수 있습니다. 사용 가능한 데이터 저장소 유형은 [이 표](#matrix) 를 참조 하세요.

#### <a name="register-your-datastore"></a>데이터 저장소 등록
모든 register 메서드는 [`Datastore`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) 클래스에 있으며 register_azure_ * 형식을 갖습니다.

다음 예에서는 Azure Blob 컨테이너 또는 Azure 파일 공유를 데이터 저장소로 등록 하는 방법을 보여 줍니다.

+ **Azure Blob 컨테이너 데이터 저장소**의 경우 다음을 사용 합니다.[`register_azure_blob-container()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py)

  ```Python
  ds = Datastore.register_azure_blob_container(workspace=ws, 
                                               datastore_name='your datastore name', 
                                               container_name='your azure blob container name',
                                               account_name='your storage account name', 
                                               account_key='your storage account key',
                                               create_if_not_exists=True)
  ```

+ **Azure 파일 공유 데이터 저장소**의 경우를 [`register_azure_file_share()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-)사용 합니다. 예: 
  ```Python
  ds = Datastore.register_azure_file_share(workspace=ws, 
                                           datastore_name='your datastore name', 
                                           file_share_name='your file share name',
                                           account_name='your storage account name', 
                                           account_key='your storage account key',
                                           create_if_not_exists=True)
  ```

<a name="get"></a>

## <a name="find--define-datastores"></a>데이터 저장소 찾기 & 정의

현재 작업 영역에 등록 된 지정 된 데이터 저장소를 가져오려면 [`get()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#get-workspace--datastore-name-) 다음을 사용 합니다.

```Python
#get named datastore from current workspace
ds = Datastore.get(ws, datastore_name='your datastore name')
```

지정 된 작업 영역에 있는 모든 데이터 저장소 목록을 가져오려면 다음 코드를 사용 합니다.

```Python
#list all datastores registered in current workspace
datastores = ws.datastores
for name, ds in datastores.items():
    print(name, ds.datastore_type)
```

현재 작업 영역에 대 한 다른 기본 데이터 저장소를 정의 [`set_default_datastore()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#set-default-datastore-name-)하려면 다음을 사용 합니다.

```Python
#define default datastore for current workspace
ws.set_default_datastore('your datastore name')
```

<a name="up-and-down"></a>
## <a name="upload--download-data"></a>데이터 업로드 & 다운로드
다음 [`upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-) 예제 [`download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-) 에서 설명 하는 및 메서드는에만 적용 되며 [azureblobdatastore 저장소](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py) 및 [azureblobdatastore 저장소](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py) 클래스와 동일 하 게 작동 합니다.

### <a name="upload"></a>Upload

 Python SDK를 사용하여 데이터 저장소에 디렉터리 또는 개별 파일을 업로드합니다.

데이터 저장소 `ds`에 디렉터리를 업로드하려면 다음 명령을 사용합니다.

```Python
import azureml.data
from azureml.data.azure_storage_datastore import AzureFileDatastore, AzureBlobDatastore

ds.upload(src_dir='your source directory',
          target_path='your target path',
          overwrite=True,
          show_progress=True)
```

`target_path`는 업로드할 파일 공유 또는 blob 컨테이너의 위치를 지정합니다. 기본 위치는 `None`입니다(데이터가 루트에 업로드됨). `overwrite=True`를 사용하는 경우 `target_path`에서 기존 데이터를 덮어씁니다.

또는 데이터 저장소의 `upload_files()` 메서드를 통해 개별 파일 목록을 데이터 저장소에 업로드합니다.

### <a name="download"></a>다운로드
업로드와 마찬가지로 데이터 저장소의 데이터를 로컬 파일 시스템에 다운로드합니다.

```Python
ds.download(target_path='your target path',
            prefix='your prefix',
            show_progress=True)
```

`target_path`는 데이터를 다운로드할 로컬 디렉터리의 위치입니다. 다운로드할 파일 공유 또는 blob 컨테이너의 폴더 경로를 지정하려면 `prefix`에 해당 경로를 입력합니다. `prefix`가 `None`이면 파일 공유 또는 blob 컨테이너의 모든 콘텐츠가 다운로드됩니다.

<a name="train"></a>
## <a name="access-datastores-during-training"></a>학습 중에 데이터 저장소 액세스

학습 계산 대상에서 데이터 저장소를 사용할 수 있도록 설정 하면 학습 스크립트에서 해당 경로를 매개 변수로 전달 하기만 하면 학습 또는 유효성 검사 데이터와 같은 학습 실행 중에 액세스할 수 있습니다.

다음 표에서는 계산 대상이 [`DataReference`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py) 실행 중에 데이터 저장소를 사용 하는 방법을 설명 하는 메서드를 보여 줍니다.

Way|메서드|설명|
----|-----|--------
탑재| [`as_mount()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--)| 를 사용 하 여 계산 대상에 데이터 저장소를 탑재 합니다.
다운로드|[`as_download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-)|를 사용 하 여에 지정 `path_on_compute`된 위치에 데이터 저장소의 콘텐츠를 다운로드 합니다. <br> 학습 실행 컨텍스트의 경우이 다운로드가 실행 되기 전에 발생 합니다.
Upload|[`as_upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)| 를 사용 하 여에 지정 된 `path_on_compute` 위치에서 데이터 저장소에 파일을 업로드 합니다. <br> 학습 실행 컨텍스트의 경우이 업로드는 실행 후에 수행 됩니다.

 ```Python
import azureml.data
from azureml.data.data_reference import DataReference

ds.as_mount()
ds.as_download(path_on_compute='your path on compute')
ds.as_upload(path_on_compute='yourfilename')
```  

데이터 저장소의 특정 폴더 또는 파일을 참조 하 여 계산 대상에서 사용할 수 있도록 하려면 데이터 저장소의 [`path()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#path-path-none--data-reference-name-none-) 함수를 사용 합니다.

```Python
#download the contents of the `./bar` directory in ds to the compute target
ds.path('./bar').as_download()
```

> [!NOTE]
> 모든 `ds` `"$AZUREML_DATAREFERENCE_XXXX"` 또는 `ds.path` 개체는 대상 계산의 탑재/다운로드 경로를 나타내는 값을 가진 형식의 환경 변수 이름으로 확인 됩니다. 대상 계산의 데이터 저장소 경로가 학습 스크립트의 실행 경로와 다를 수 있습니다.

<a name="matrix"></a>
### <a name="training-compute-and-datastore-matrix"></a>계산 및 데이터 저장소 매트릭스 학습

다음 행렬은 다양 한 학습 계산 대상 및 데이터 저장소 시나리오에 사용할 수 있는 데이터 액세스 기능을 표시 합니다. [Azure Machine Learning의 학습 계산 대상](how-to-set-up-training-targets.md#compute-targets-for-training)에 대해 자세히 알아보세요.

|컴퓨팅|[AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py)                                       |[AzureFileDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py)                                      |[AzureDataLakeDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_data_lake_datastore.azuredatalakedatastore?view=azure-ml-py) |[AzureDataLakeGen2Datastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_data_lake_datastore.azuredatalakegen2datastore?view=azure-ml-py) [AzurePostgreSqlDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_postgre_sql_datastore.azurepostgresqldatastore?view=azure-ml-py) [AzureSqlDatabaseDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_sql_database_datastore.azuresqldatabasedatastore?view=azure-ml-py) |
|--------------------------------|----------------------------------------------------------|----------------------------------------------------------|------------------------|----------------------------------------------------------------------------------------|
| 로컬|[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)|[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)|해당 사항 없음         |해당 사항 없음                                                                         |
| Azure Machine Learning 컴퓨팅 |[as_mount()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--), [as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-), [ML&nbsp;pipelines](concept-ml-pipelines.md)|[as_mount()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--), [as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-), [ML&nbsp;pipelines](concept-ml-pipelines.md)|해당 사항 없음         |해당 사항 없음                                                                         |
| 가상 머신               |[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                           | [as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            |N/A         |N/A                                                                         |
| HDInsight                      |[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            | [as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            |N/A         |해당 사항 없음                                                                         |
| 데이터 전송                  |[ML&nbsp;파이프라인](concept-ml-pipelines.md)                                               |해당 사항 없음                                           |[ML&nbsp;파이프라인](concept-ml-pipelines.md)            |[ML&nbsp;파이프라인](concept-ml-pipelines.md)                                                                            |
| Databricks                     |[ML&nbsp;파이프라인](concept-ml-pipelines.md)                                              |해당 사항 없음                                           |[ML&nbsp;파이프라인](concept-ml-pipelines.md)             |N/A                                                                         |
| Azure Batch                    |[ML&nbsp;파이프라인](concept-ml-pipelines.md)                                               |해당 사항 없음                                           |해당 사항 없음         |N/A                                                                         |
| Azure DataLake 분석       |N/A                                           |N/A                                           |[ML&nbsp;파이프라인](concept-ml-pipelines.md)             |N/A                                                                         |

> [!NOTE]
> 매우 반복적이 고 큰 데이터 프로세스가 대신를 사용 하 여 `as_download()` 더 빠르게 실행 되는 시나리오가 있을 수 있습니다 .이는 experimentally의 `as_mount()`유효성을 검사할 수 있습니다.

### <a name="examples"></a>예 

다음 코드 예제는 학습 중 데이터 저장소 [`Estimator`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) 에 액세스 하기 위한 클래스와 관련 되어 있습니다.

이 코드는 지정 된 학습 계산 대상의에 `train.py` `script_params`정의 된 매개 변수를 사용 하 여 표시 된 원본 디렉터리에서 학습 스크립트를 사용 하 여 평가기를 만듭니다.

```Python
from azureml.train.estimator import Estimator

script_params = {
    '--data_dir': ds.as_mount()
}

est = Estimator(source_directory='your code directory',
                entry_script='train.py',
                script_params=script_params,
                compute_target=compute_target
                )
```

데이터 저장소 목록을 평가기 생성자 `inputs` 매개 변수에 전달 하 여 데이터 저장소에서/로 복사 하거나 데이터 저장소에서 복사할 수도 있습니다. 이 코드 예제는 다음과 같습니다.
* 학습 스크립트 `ds1` `train.py` 를 실행 하기 전에 데이터 저장소의 모든 콘텐츠를 계산 대상으로 다운로드 합니다.
* 가 실행 되기 `'./foo'` 전에 `train.py` 데이터 `ds2` 저장소의 폴더를 계산 대상으로 다운로드 합니다.
* 스크립트가 실행 된 `'./bar.pkl'` 후 계산 대상에서 데이터 저장소 `ds3` 까지 파일을 업로드 합니다.

```Python
est = Estimator(source_directory='your code directory',
                compute_target=compute_target,
                entry_script='train.py',
                inputs=[ds1.as_download(), ds2.path('./foo').as_download(), ds3.as_upload(path_on_compute='./bar.pkl')])
```

## <a name="access-datastores-during-for-scoring"></a>점수 매기기를 위해에서 데이터 저장소 액세스

Azure Machine Learning 서비스는 모델을 사용 하 여 점수를 매기는 여러 가지 방법을 제공 합니다. 이러한 메서드 중 일부는 데이터 저장소에 대 한 액세스를 제공 하지 않습니다. 다음 표를 사용 하 여 점수 매기기 중에 데이터 저장소에 액세스할 수 있는 방법을 알아봅니다.

| 메서드 | 데이터 저장소 액세스 | Description |
| ----- | :-----: | ----- |
| [일괄 처리 예측](how-to-run-batch-predictions.md) | ✔ | 많은 양의 데이터를 비동기적으로 예측 합니다. |
| [웹 서비스](how-to-deploy-and-where.md) | &nbsp; | 모델을 웹 서비스로 배포 합니다. |
| [IoT Edge 모듈](how-to-deploy-and-where.md) | &nbsp; | IoT Edge 장치에 모델을 배포 합니다. |

SDK에서 데이터 저장소에 대 한 액세스를 제공 하지 않는 경우 관련 Azure SDK를 사용 하 여 데이터에 액세스 하는 사용자 지정 코드를 만들 수 있습니다. 예를 들어 [Python 용 AZURE STORAGE SDK](https://github.com/Azure/azure-storage-python) 를 사용 하 여 blob에 저장 된 데이터에 액세스 합니다.

## <a name="next-steps"></a>다음 단계

* [모델 학습](how-to-train-ml-models.md)

* [모델 배포](how-to-deploy-and-where.md)
