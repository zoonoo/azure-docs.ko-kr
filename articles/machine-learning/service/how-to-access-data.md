---
title: Azure storage 서비스의 데이터에 액세스
titleSuffix: Azure Machine Learning service
description: Azure Machine Learning 서비스를 사용 하 여 학습 하는 동안 데이터 저장소를 사용 하 여 Azure storage 서비스에 액세스 하는 방법 알아보기
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
ms.reviewer: nibaccam
ms.date: 08/2/2019
ms.custom: seodec18
ms.openlocfilehash: 545860a394c7eac953c1cbacc9dd05fc3737f6c1
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/08/2019
ms.locfileid: "68856164"
---
# <a name="access-data-in-azure-storage-services"></a>Azure storage 서비스의 데이터에 액세스

 이 문서에서는 Azure Machine Learning datastores를 통해 Azure storage 서비스에서 데이터에 쉽게 액세스 하는 방법을 알아봅니다. 데이터 저장소는 스크립트에 정보를 하드 코딩 하지 않고도 구독 ID 및 토큰 권한 부여와 같은 연결 정보를 저장 하 여 저장소에 액세스 하는 데 사용 됩니다.

이 방법에는 다음 작업의 예가 나와 있습니다.
* [데이터 저장소 등록](#access)
* [작업 영역에서 데이터 저장소 가져오기](#get)
* [데이터 저장소를 사용 하 여 데이터 업로드 및 다운로드](#up-and-down)
* [학습 중 데이터 액세스](#train)

## <a name="prerequisites"></a>필수 구성 요소

데이터 저장소를 사용하려면 먼저 [작업 영역](concept-workspace.md)이 필요합니다.

[새 작업 영역을 만들](how-to-manage-workspace.md)거나 기존 작업 영역을 검색합니다.

```Python
import azureml.core
from azureml.core import Workspace, Datastore

ws = Workspace.from_config()
```

<a name="access"></a>

## <a name="register-datastores"></a>데이터 저장소 등록

모든 register 메서드는 [`Datastore`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) 클래스에 있으며 register_azure_ * 형식을 갖습니다.

다음 예에서는 Azure Blob 컨테이너 또는 Azure 파일 공유를 데이터 저장소로 등록 하는 방법을 보여 줍니다.

+ **Azure Blob 컨테이너 데이터 저장소**의 경우 다음을 사용 합니다.[`register_azure_blob-container()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py)

  ```Python
  datastore = Datastore.register_azure_blob_container(workspace=ws, 
                                                      datastore_name='your datastore name', 
                                                      container_name='your azure blob container name',
                                                      account_name='your storage account name', 
                                                      account_key='your storage account key',
                                                      create_if_not_exists=True)
  ```

+ **Azure 파일 공유 데이터 저장소**의 경우를 [`register_azure_file_share()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-)사용 합니다. 예를 들어: 
  ```Python
  datastore = Datastore.register_azure_file_share(workspace=ws, 
                                                  datastore_name='your datastore name', 
                                                  file_share_name='your file share name',
                                                  account_name='your storage account name', 
                                                  account_key='your storage account key',
                                                  create_if_not_exists=True)
  ```

####  <a name="storage-guidance"></a>저장소 지침

Azure Blob 컨테이너를 권장 합니다. Blob에는 standard 및 premium storage를 모두 사용할 수 있습니다. 비용이 더 많이 들지만, 특히 큰 데이터 집합에 대해 학습 하는 경우 학습 실행 속도를 향상 시킬 수 있는 더 빠른 처리량 속도 때문에 premium storage를 제안 합니다. 저장소 계정 비용 정보는 [Azure 가격 계산기](https://azure.microsoft.com/pricing/calculator/?service=machine-learning-service) 를 참조 하세요.

<a name="get"></a>

## <a name="get-datastores-from-your-workspace"></a>작업 영역에서 데이터 저장소 가져오기

현재 작업 영역에 등록 된 특정 데이터 저장소를 가져오려면 데이터 저장소 [`get()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#get-workspace--datastore-name-) 클래스에서 정적 메서드를 사용 합니다.

```Python
#get named datastore from current workspace
datastore = Datastore.get(ws, datastore_name='your datastore name')
```
지정 된 작업 영역에 등록 된 데이터 저장소 목록을 가져오려면 작업 영역 개체에 대 한 `datastores` 속성을 사용 하면 됩니다.

```Python
#list all datastores registered in current workspace
datastores = ws.datastores
for name, datastore in datastores.items():
    print(name, datastore.datastore_type)
```

작업 영역을 만들 때 azure Blob 컨테이너와 azure 파일 공유는 각각 및 `workspaceblobstore` `workspacefilestore` 이라는 작업 영역에 등록 됩니다. 작업 영역에 연결 된 저장소 계정에 프로 비전 되는 파일 공유 및 Blob 컨테이너에 대 한 연결 정보를 저장 합니다. 는 기본 데이터 저장소로 설정 됩니다.`workspaceblobstore`

작업 영역의 기본 데이터 저장소를 가져오려면 다음 코드를 사용합니다.

```Python
datastore = ws.get_default_datastore()
```

현재 작업 영역에 대해 다른 기본 데이터 저장소를 정의 하려면 [`set_default_datastore()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#set-default-datastore-name-) 작업 영역 개체의 메서드를 사용 합니다.

```Python
#define default datastore for current workspace
ws.set_default_datastore('your datastore name')
```

<a name="up-and-down"></a>
## <a name="upload--download-data"></a>데이터 업로드 & 다운로드
다음 [`upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-) 예제 [`download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-) 에서 설명 하는 및 메서드는에만 적용 되며 [azureblobdatastore 저장소](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py) 및 [azureblobdatastore 저장소](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py) 클래스와 동일 하 게 작동 합니다.

### <a name="upload"></a>Upload

 Python SDK를 사용하여 데이터 저장소에 디렉터리 또는 개별 파일을 업로드합니다.

데이터 저장소 `datastore`에 디렉터리를 업로드하려면 다음 명령을 사용합니다.

```Python
import azureml.data
from azureml.data.azure_storage_datastore import AzureFileDatastore, AzureBlobDatastore

datastore.upload(src_dir='your source directory',
                 target_path='your target path',
                 overwrite=True,
                 show_progress=True)
```

매개 `target_path` 변수는 업로드할 파일 공유 (또는 blob 컨테이너)의 위치를 지정 합니다. 기본 위치는 `None`입니다(데이터가 루트에 업로드됨). `overwrite=True` 에서`target_path` 기존 데이터를 덮어씁니다.

또는 메서드를 `upload_files()` 통해 데이터 저장소에 개별 파일 목록을 업로드 합니다.

### <a name="download"></a>다운로드

업로드와 마찬가지로 데이터 저장소의 데이터를 로컬 파일 시스템에 다운로드합니다.

```Python
datastore.download(target_path='your target path',
                   prefix='your prefix',
                   show_progress=True)
```

`target_path` 매개 변수는 데이터를 다운로드할 로컬 디렉터리의 위치입니다. 다운로드할 파일 공유 또는 blob 컨테이너의 폴더 경로를 지정하려면 `prefix`에 해당 경로를 입력합니다. `prefix`가 `None`이면 파일 공유 또는 blob 컨테이너의 모든 콘텐츠가 다운로드됩니다.

<a name="train"></a>
## <a name="access-your-data-during-training"></a>학습 중 데이터 액세스

학습 중 데이터에 액세스 하려면 데이터 저장소를 통해 Azure storage 서비스에서 계산 대상으로 데이터를 다운로드 하거나 탑재할 수 있습니다.

다음 표에서는 실행 중에 데이터 저장소를 사용 하는 방법을 계산 대상에 알려 주는 방법을 보여 줍니다. 

Way|메서드|설명|
----|-----|--------
탑재| [`as_mount()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#as-mount--)| 를 사용 하 여 계산 대상에 데이터 저장소를 탑재 합니다.
다운로드|[`as_download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#as-download-path-on-compute-none-)|를 사용 하 여에 지정 `path_on_compute`된 위치에 데이터 저장소의 콘텐츠를 다운로드 합니다. <br> 이 다운로드는 실행 전에 발생 합니다.
Upload|[`as_upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#as-upload-path-on-compute-none-)| 를 사용 하 여에 지정 된 `path_on_compute` 위치에서 데이터 저장소에 파일을 업로드 합니다. <br> 이 업로드는 실행 후에 수행 됩니다.

데이터 저장소에서 특정 폴더 또는 파일을 참조 하 고 계산 대상에서 사용할 수 있도록 하려면 데이터 저장소 [`path()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#path-path-none--data-reference-name-none-) 메서드를 사용 합니다.

```Python
#to mount the full contents in your storage to the compute target
datastore.as_mount()

#to download the contents of the `./bar` directory in your storage to the compute target
datastore.path('./bar').as_download()
```
> [!NOTE]
> 모든 `datastore` 또는 `datastore.path` 개체는 형식의 `"$AZUREML_DATAREFERENCE_XXXX"`환경 변수 이름으로 확인 되며, 해당 값은 대상 계산의 탑재/다운로드 경로를 나타냅니다. 대상 계산의 데이터 저장소 경로가 학습 스크립트의 실행 경로와 다를 수 있습니다.

### <a name="examples"></a>예 

다음 코드 예제는 학습 중 데이터에 [`Estimator`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) 액세스 하기 위한 클래스와 관련 되어 있습니다. 

`script_params`entry_script에 대 한 매개 변수를 포함 하는 사전입니다. 이를 사용 하 여 데이터 저장소를 전달 하 고 계산 대상에서 데이터를 사용할 수 있는 방법을 설명할 수 있습니다. 종단 간 [자습서](tutorial-train-models-with-aml.md)에서 자세히 알아보세요.

```Python
from azureml.train.estimator import Estimator

script_params = {
    '--data_dir': datastore.path('/bar').as_mount()
}

est = Estimator(source_directory='your code directory',
                entry_script='train.py',
                script_params=script_params,
                compute_target=compute_target
                )
```

데이터 저장소 목록을 평가기 constructor `inputs` 매개 변수에 전달 하 여 데이터 저장소 간에 데이터를 탑재 하거나 복사할 수도 있습니다. 이 코드 예제는 다음과 같습니다.
* 학습 스크립트 `datastore1` `train.py` 를 실행 하기 전에 모든 콘텐츠를 계산 대상으로 다운로드 합니다.
* 가 실행 되기 `'./foo'` 전에 `datastore2` `train.py` 계산 대상에 폴더를 다운로드 합니다.
* 스크립트가 실행 된 `'./bar.pkl'` `datastore3` 후 계산 대상에서로 파일을 업로드 합니다.

```Python
est = Estimator(source_directory='your code directory',
                compute_target=compute_target,
                entry_script='train.py',
                inputs=[datastore1.as_download(), datastore2.path('./foo').as_download(), datastore3.as_upload(path_on_compute='./bar.pkl')])
```
### <a name="compute-and-datastore-matrix"></a>계산 및 데이터 저장소 행렬

Datastores는 현재 다음 행렬에 나열 된 저장소 서비스에 대 한 연결 정보를 저장 하는 기능을 지원 합니다. 이 행렬은 다양 한 계산 대상 및 데이터 저장소 시나리오에 사용할 수 있는 데이터 액세스 기능을 표시 합니다. [Azure Machine Learning에 대 한 계산 대상](how-to-set-up-training-targets.md#compute-targets-for-training)에 대해 자세히 알아보세요.

|컴퓨팅|[AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py)                                       |[AzureFileDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py)                                      |[AzureDataLakeDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_data_lake_datastore.azuredatalakedatastore?view=azure-ml-py) |[AzureDataLakeGen2Datastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_data_lake_datastore.azuredatalakegen2datastore?view=azure-ml-py) [AzurePostgreSqlDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_postgre_sql_datastore.azurepostgresqldatastore?view=azure-ml-py) [AzureSqlDatabaseDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_sql_database_datastore.azuresqldatabasedatastore?view=azure-ml-py) |
|--------------------------------|----------------------------------------------------------|----------------------------------------------------------|------------------------|----------------------------------------------------------------------------------------|
| 로컬|[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)|[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)|N/A         |해당 사항 없음                                                                         |
| Azure Machine Learning 컴퓨팅 |[as_mount()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--), [as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-), [ML&nbsp;pipelines](concept-ml-pipelines.md)|[as_mount()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--), [as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-), [ML&nbsp;pipelines](concept-ml-pipelines.md)|N/A         |해당 사항 없음                                                                         |
| 가상 머신               |[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                           | [as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            |해당 사항 없음         |해당 사항 없음                                                                         |
| HDInsight                      |[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            | [as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            |N/A         |해당 사항 없음                                                                         |
| 데이터 전송                  |[ML&nbsp;파이프라인](concept-ml-pipelines.md)                                               |해당 사항 없음                                           |[ML&nbsp;파이프라인](concept-ml-pipelines.md)            |[ML&nbsp;파이프라인](concept-ml-pipelines.md)                                                                            |
| Databricks                     |[ML&nbsp;파이프라인](concept-ml-pipelines.md)                                              |해당 사항 없음                                           |[ML&nbsp;파이프라인](concept-ml-pipelines.md)             |해당 사항 없음                                                                         |
| Azure Batch                    |[ML&nbsp;파이프라인](concept-ml-pipelines.md)                                               |해당 사항 없음                                           |해당 사항 없음         |해당 사항 없음                                                                         |
| Azure DataLake 분석       |해당 사항 없음                                           |해당 사항 없음                                           |[ML&nbsp;파이프라인](concept-ml-pipelines.md)             |해당 사항 없음                                                                         |

> [!NOTE]
> 매우 반복적이 고 큰 데이터 프로세스가 대신를 사용 하 여 `as_download()` 더 빠르게 실행 되는 시나리오가 있을 수 있습니다 .이는 experimentally의 `as_mount()`유효성을 검사할 수 있습니다.

## <a name="access-data-during-scoring"></a>점수 매기기 중 데이터 액세스

Azure Machine Learning 서비스는 모델을 사용 하 여 점수를 매기는 여러 가지 방법을 제공 합니다. 이러한 메서드 중 일부는 데이터 저장소에 대 한 액세스를 제공 하지 않습니다. 다음 표를 사용 하 여 점수 매기기 중에 데이터 저장소에 액세스할 수 있는 방법을 알아봅니다.

| 메서드 | 데이터 저장소 액세스 | 설명 |
| ----- | :-----: | ----- |
| [일괄 처리 예측](how-to-run-batch-predictions.md) | ✔ | 많은 양의 데이터를 비동기적으로 예측 합니다. |
| [웹 서비스](how-to-deploy-and-where.md) | &nbsp; | 모델을 웹 서비스로 배포 합니다. |
| [IoT Edge 모듈](how-to-deploy-and-where.md) | &nbsp; | IoT Edge 장치에 모델을 배포 합니다. |

SDK에서 데이터 저장소에 대 한 액세스를 제공 하지 않는 경우 관련 Azure SDK를 사용 하 여 데이터에 액세스 하는 사용자 지정 코드를 만들 수 있습니다. 예를 들어 [Python 용 AZURE STORAGE SDK](https://github.com/Azure/azure-storage-python) 를 사용 하 여 blob에 저장 된 데이터에 액세스 합니다.


## <a name="next-steps"></a>다음 단계

* [모델 학습](how-to-train-ml-models.md)

* [모델 배포](how-to-deploy-and-where.md)
