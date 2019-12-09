---
title: Azure storage 서비스의 데이터에 액세스
titleSuffix: Azure Machine Learning
description: 을 사용 하 여 학습 하는 동안 데이터 저장소를 사용 하 여 Azure storage 서비스에 액세스 하는 방법을 알아봅니다 Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
ms.reviewer: nibaccam
ms.date: 11/04/2019
ms.custom: seodec18
ms.openlocfilehash: ee6ab1ada540f4f664e6782a1fffc63cc7df95e4
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/08/2019
ms.locfileid: "74928576"
---
# <a name="access-data-in-azure-storage-services"></a>Azure storage 서비스의 데이터에 액세스
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

이 문서에서는 Azure Machine Learning datastores를 통해 Azure storage 서비스에서 데이터에 쉽게 액세스 하는 방법을 알아봅니다. 데이터 저장소는 구독 ID 및 토큰 권한 부여와 같은 연결 정보를 저장 하는 데 사용 됩니다. 데이터 저장소를 사용 하면 스크립트에 연결 정보를 하드 코딩 하지 않고도 저장소에 액세스할 수 있습니다. 이러한 [Azure storage 솔루션](#matrix)에서 데이터 저장소를 만들 수 있습니다. 지원 되지 않는 저장소 솔루션의 경우 machine learning 실험 중에 데이터 송신 비용을 절약 하려면 지원 되는 Azure storage 솔루션으로 데이터를 이동 하는 것이 좋습니다. [데이터를 이동 하는 방법을 알아봅니다](#move). 

이 방법에는 다음 작업의 예가 나와 있습니다.
* 데이터 저장소 등록
* 작업 영역에서 데이터 저장소 가져오기
* 데이터 저장소를 사용 하 여 데이터 업로드 및 다운로드
* 학습 중 데이터 액세스
* Azure storage 서비스로 데이터 이동

## <a name="prerequisites"></a>전제 조건
.NET framework 4.5.2 이상이
- Azure 구독. Azure 구독이 없는 경우 시작하기 전에 체험 계정을 만듭니다. 지금 [Azure Machine Learning 서비스의 평가판 또는 유료 버전](https://aka.ms/AMLFree)을 사용해 보세요.

- Azure [Blob 컨테이너](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) 또는 [azure 파일 공유](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)를 사용 하는 azure storage 계정

- [Python 용 AZURE MACHINE LEARNING SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)또는 [Azure Machine Learning studio](https://ml.azure.com/)에 대 한 액세스입니다.

- Azure Machine Learning 작업 영역 
    - [Azure Machine Learning 작업 영역을 만들거나](how-to-manage-workspace.md) Python SDK를 사용 하 여 기존 작업 영역을 사용 합니다.

        ```Python
        import azureml.core
        from azureml.core import Workspace, Datastore
        
        ws = Workspace.from_config()
        ```

<a name="access"></a>

## <a name="create-and-register-datastores"></a>데이터 저장소 만들기 및 등록

Azure storage 솔루션을 데이터 저장소로 등록 하면 특정 작업 영역에서 해당 데이터 저장소를 자동으로 만듭니다. Python SDK 또는 Azure Machine Learning studio를 사용 하 여 데이터 저장소를 만들고 작업 영역에 등록할 수 있습니다.

### <a name="using-the-python-sdk"></a>Python SDK 사용

모든 register 메서드는 [`Datastore`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) 클래스에 있으며 register_azure_ * 형식입니다.

Register () 메서드를 채우는 데 필요한 정보는 [Azure Machine Learning studio](https://ml.azure.com) 및 다음 단계를 통해 찾을 수 있습니다.

1. 왼쪽 창에서 **저장소 계정** 을 선택 하 고 등록 하려는 저장소 계정을 선택 합니다. 
2. **개요** 페이지는 계정 이름과 컨테이너 또는 파일 공유 이름과 같은 정보를 제공 합니다. 
3. 계정 키 또는 SAS 토큰과 같은 인증 정보를 보려면 왼쪽의 **설정** 창에서 **계정 키** 로 이동 합니다. 

>중요 한 저장소 계정이 VNET에 있는 경우 Azure blob 데이터 저장소 만들기만 지원 됩니다. 매개 변수를 `True` `grant_workspace_access` 설정 하 여 작업 영역에 저장소 계정에 대 한 액세스 권한을 부여 합니다.

다음 예에서는 Azure Blob 컨테이너 또는 Azure 파일 공유를 데이터 저장소로 등록 하는 방법을 보여 줍니다.

+ **Azure Blob 컨테이너 데이터 저장소**의 경우 [`register_azure_blob-container()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-blob-container-workspace--datastore-name--container-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false--blob-cache-timeout-none--grant-workspace-access-false--subscription-id-none--resource-group-none-) 를 사용 합니다.

    다음 코드는 `ws`작업 영역에 데이터 저장소, `my_datastore`을 만들어 등록 합니다. 이 데이터 저장소는 제공 된 계정 키를 사용 하 여 Azure storage `my_storage_account` 계정에서 `my_blob_container`Azure blob 컨테이너에 액세스 합니다.

    ```Python
       datastore = Datastore.register_azure_blob_container(workspace=ws, 
                                                          datastore_name='my_datastore', 
                                                          container_name='my_blob_container',
                                                          account_name='my_storage_account', 
                                                          account_key='your storage account key',
                                                          create_if_not_exists=True)
    ```

+ **Azure 파일 공유 데이터 저장소**의 경우 [`register_azure_file_share()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-)를 사용 합니다. 

    다음 코드는 `ws`작업 영역에 데이터 저장소, `my_datastore`을 만들어 등록 합니다. 이 데이터 저장소는 제공 된 계정 키를 사용 하 여 Azure storage `my_storage_account` 계정에서 `my_file_share`Azure 파일 공유에 액세스 합니다.

    ```Python
       datastore = Datastore.register_azure_file_share(workspace=ws, 
                                                      datastore_name='my_datastore', 
                                                      file_share_name='my_file_share',
                                                      account_name='my_storage account', 
                                                      account_key='your storage account key',
                                                      create_if_not_exists=True)
    ```

####  <a name="storage-guidance"></a>스토리지 지침

Azure Blob 컨테이너를 권장 합니다. Blob에는 standard 및 premium storage를 모두 사용할 수 있습니다. 비용이 더 많이 들지만, 특히 큰 데이터 집합에 대해 학습 하는 경우 학습 실행 속도를 향상 시킬 수 있는 더 빠른 처리량 속도 때문에 premium storage를 제안 합니다. 저장소 계정 비용 정보는 [Azure 가격 계산기](https://azure.microsoft.com/pricing/calculator/?service=machine-learning-service) 를 참조 하세요.

### <a name="using-azure-machine-learning-studio"></a>Azure Machine Learning studio 사용 

Azure Machine Learning studio에서 몇 가지 단계를 수행 하 여 새 데이터 저장소를 만듭니다.

1. [Azure Machine Learning Studio](https://ml.azure.com/)에 로그인합니다.
1. **관리**아래의 왼쪽 창에서 **datastores** 를 선택 합니다.
1. **+ 새 데이터 저장소**를 선택 합니다.
1. 새 데이터 저장소 폼을 완성 합니다. 양식은 Azure storage 유형 및 인증 유형 선택에 따라 지능적으로 업데이트 됩니다.
  
양식을 채우는 데 필요한 정보는 [Azure Portal](https://portal.azure.com)를 통해 찾을 수 있습니다. 왼쪽 창에서 **저장소 계정** 을 선택 하 고 등록 하려는 저장소 계정을 선택 합니다. **개요** 페이지는 계정 이름과 컨테이너 또는 파일 공유 이름과 같은 정보를 제공 합니다. 계정 키 또는 SAS 토큰과 같은 인증 항목의 경우 왼쪽의 **설정** 창에서 **계정 키** 로 이동 합니다.

다음 예제에서는 Azure blob 데이터 저장소 생성에 대해 양식이 표시 되는 모양을 보여 줍니다. 
    
 ![새 데이터 저장소](media/how-to-access-data/new-datastore-form.png)


<a name="get"></a>

## <a name="get-datastores-from-your-workspace"></a>작업 영역에서 데이터 저장소 가져오기

현재 작업 영역에 등록 된 특정 데이터 저장소를 가져오려면 데이터 저장소 클래스에서 [`get()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#get-workspace--datastore-name-) 정적 메서드를 사용 합니다.

```Python
#get named datastore from current workspace
datastore = Datastore.get(ws, datastore_name='your datastore name')
```
지정 된 작업 영역에 등록 된 데이터 저장소 목록을 가져오려면 작업 영역 개체에 대 한 [`datastores`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace%28class%29?view=azure-ml-py#datastores) 속성을 사용 하면 됩니다.

```Python
#list all datastores registered in current workspace
datastores = ws.datastores
for name, datastore in datastores.items():
    print(name, datastore.datastore_type)
```

작업 영역을 만들 때 Azure Blob 컨테이너와 Azure 파일 공유는 각각 `workspaceblobstore` 및 `workspacefilestore` 이라는 작업 영역에 자동으로 등록 됩니다. 이러한 파일은 작업 영역에 연결 된 저장소 계정에 프로 비전 되는 Blob 컨테이너 및 파일 공유의 연결 정보를 저장 합니다. `workspaceblobstore`은 기본 데이터 저장소로 설정 됩니다.

작업 영역의 기본 데이터 저장소를 가져오려면 다음 코드를 사용합니다.

```Python
datastore = ws.get_default_datastore()
```

현재 작업 영역에 대해 다른 기본 데이터 저장소를 정의 하려면 작업 영역 개체에 대 한 [`set_default_datastore()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#set-default-datastore-name-) 메서드를 사용 합니다.

```Python
#define default datastore for current workspace
ws.set_default_datastore('your datastore name')
```

<a name="up-and-down"></a>
## <a name="upload--download-data"></a>데이터 업로드 & 다운로드
다음 예제에서 설명 하는 [`upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#upload-src-dir--target-path-none--overwrite-false--show-progress-true-) 및 [`download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-) 메서드는에만 적용 되며 [Azureblobdatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py) 저장소 및 [azureblobdatastore 저장소](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py) 클래스와 동일 하 게 작동 합니다.

### <a name="upload"></a>업로드

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

`target_path` 매개 변수는 업로드할 파일 공유 (또는 blob 컨테이너)의 위치를 지정 합니다. 기본 위치는 `None`입니다(데이터가 루트에 업로드됨). `overwrite=True` 그렇지 않으면 `target_path`에 있는 기존 데이터를 덮어씁니다.

또는 `upload_files()` 메서드를 통해 데이터 저장소에 개별 파일 목록을 업로드 합니다.

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

> [!IMPORTANT]
> [Azure Machine Learning 데이터 집합](how-to-create-register-datasets.md) 을 사용 하는 것은 학습에서 데이터에 액세스 하는 새로운 권장 방법입니다. 데이터 집합은 pandas 또는 spark 데이터 프레임에 테이블 형식 데이터를 로드 하는 함수와 Azure Blob, Azure File Azure Data Lake Gen 1, Azure Data Lake Gen 2, Azure SQL, Azure PostgreSQL에서 모든 형식의 파일을 다운로드 하거나 탑재 하는 기능을 제공 합니다. [데이터 집합을 사용 하 여 학습 하는 방법](how-to-train-with-datasets.md)에 대해 자세히 알아보세요.

다음 표에서는 실행 중에 데이터 저장소를 사용 하는 방법을 계산 대상에 알려 주는 방법을 보여 줍니다. 

Way|방법|설명|
----|-----|--------
탑재| [`as_mount()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#as-mount--)| 를 사용 하 여 계산 대상에 데이터 저장소를 탑재 합니다. 탑재 되 면 계산 대상에서 데이터 저장소의 모든 파일에 액세스할 수 있습니다.
다운로드|[`as_download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#as-download-path-on-compute-none-)|를 사용 하 여 `path_on_compute`에 지정 된 위치에 데이터 저장소의 콘텐츠를 다운로드 합니다. <br><br> 이 다운로드는 실행 전에 발생 합니다.
업로드|[`as_upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#as-upload-path-on-compute-none-)| `path_on_compute`에 지정 된 위치에서 데이터 저장소에 파일을 업로드 하는 데 사용 합니다. <br><br> 이 업로드는 실행 후에 수행 됩니다.

데이터 저장소에서 특정 폴더나 파일을 참조 하 고 계산 대상에서 사용할 수 있게 하려면 데이터 저장소 [`path()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#path-path-none--data-reference-name-none-) 메서드를 사용 합니다.

```Python
#to mount the full contents in your storage to the compute target
datastore.as_mount()

#to download the contents of the `./bar` directory in your storage to the compute target
datastore.path('./bar').as_download()
```
> [!NOTE]
> 지정 된 `datastore` 또는 `datastore.path` 개체는 `"$AZUREML_DATAREFERENCE_XXXX"`형식의 환경 변수 이름으로 확인 됩니다. 해당 값은 대상 계산의 탑재/다운로드 경로를 나타냅니다. 대상 계산의 데이터 저장소 경로가 학습 스크립트의 실행 경로와 다를 수 있습니다.

### <a name="examples"></a>예시 

다음 코드 예제는 학습 중 데이터에 액세스 하기 위한 [`Estimator`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) 클래스에만 적용 됩니다.

`script_params`는 entry_script에 대 한 매개 변수를 포함 하는 사전입니다. 이를 사용 하 여 데이터 저장소를 전달 하 고 계산 대상에서 데이터를 사용할 수 있는 방법을 설명 합니다. 종단 간 [자습서](tutorial-train-models-with-aml.md)에서 자세히 알아보세요.

```Python
from azureml.train.estimator import Estimator

# notice '/' is in front, this indicates the absolute path
script_params = {
    '--data_dir': datastore.path('/bar').as_mount()
}

est = Estimator(source_directory='your code directory',
                entry_script='train.py',
                script_params=script_params,
                compute_target=compute_target
                )
```

데이터 저장소 목록을 평가기 생성자 `inputs` 매개 변수에 전달 하 여 데이터 저장소 간에 데이터를 탑재 하거나 복사할 수도 있습니다. 이 코드 예제는 다음과 같습니다.
* 학습 스크립트 `train.py` 실행 되기 전에 `datastore1`의 모든 콘텐츠를 계산 대상으로 다운로드 합니다.
* `train.py` 실행 되기 전에 `datastore2`에서 계산 대상으로 `'./foo'` 폴더를 다운로드 합니다.
* 스크립트를 실행 한 후 계산 대상의 파일 `'./bar.pkl'`를 `datastore3`에 업로드 합니다.

```Python
est = Estimator(source_directory='your code directory',
                compute_target=compute_target,
                entry_script='train.py',
                inputs=[datastore1.as_download(), datastore2.path('./foo').as_download(), datastore3.as_upload(path_on_compute='./bar.pkl')])
```
<a name="matrix"></a>

### <a name="compute-and-datastore-matrix"></a>계산 및 데이터 저장소 행렬

Datastores는 현재 다음 행렬에 나열 된 저장소 서비스에 대 한 연결 정보를 저장 하는 기능을 지원 합니다. 이 행렬은 다양 한 계산 대상 및 데이터 저장소 시나리오에 사용할 수 있는 데이터 액세스 기능을 표시 합니다. [Azure Machine Learning에 대 한 계산 대상](how-to-set-up-training-targets.md#compute-targets-for-training)에 대해 자세히 알아보세요.

|컴퓨팅|[AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py)                                       |[AzureFileDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py)                                      |[AzureDataLakeDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_data_lake_datastore.azuredatalakedatastore?view=azure-ml-py) |[AzureDataLakeGen2Datastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_data_lake_datastore.azuredatalakegen2datastore?view=azure-ml-py) [AzurePostgreSqlDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_postgre_sql_datastore.azurepostgresqldatastore?view=azure-ml-py) [AzureSqlDatabaseDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_sql_database_datastore.azuresqldatabasedatastore?view=azure-ml-py) |
|--------------------------------|----------------------------------------------------------|----------------------------------------------------------|------------------------|----------------------------------------------------------------------------------------|
| 지방|[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)|[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)|N/A         |N/A                                                                         |
| Azure Machine Learning 컴퓨팅 |[as_mount()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--), [as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-), [ML&nbsp;pipelines](concept-ml-pipelines.md)|[as_mount()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--), [as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-), [ML&nbsp;pipelines](concept-ml-pipelines.md)|N/A         |N/A                                                                         |
| Virtual Machines               |[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                           | [as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            |N/A         |N/A                                                                         |
| HDInsight                      |[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            | [as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            |N/A         |N/A                                                                         |
| 데이터 전송                  |[ML&nbsp;파이프라인](concept-ml-pipelines.md)                                               |N/A                                           |[ML&nbsp;파이프라인](concept-ml-pipelines.md)            |[ML&nbsp;파이프라인](concept-ml-pipelines.md)                                                                            |
| Databricks                     |[ML&nbsp;파이프라인](concept-ml-pipelines.md)                                              |N/A                                           |[ML&nbsp;파이프라인](concept-ml-pipelines.md)             |N/A                                                                         |
| Azure Batch                    |[ML&nbsp;파이프라인](concept-ml-pipelines.md)                                               |N/A                                           |N/A         |N/A                                                                         |
| Azure DataLake 분석       |N/A                                           |N/A                                           |[ML&nbsp;파이프라인](concept-ml-pipelines.md)             |N/A                                                                         |

> [!NOTE]
> 매우 반복적인 큰 데이터 프로세스가 `as_mount()`대신 `as_download()`를 사용 하 여 더 빠르게 실행 되는 시나리오가 있을 수 있습니다. experimentally의 유효성을 검사할 수 있습니다.

### <a name="accessing-source-code-during-training"></a>학습 중 소스 코드 액세스

Azure blob storage는 Azure 파일 공유 보다 더 높은 처리량 속도를 가지 며 동시에 시작 된 많은 수의 작업으로 확장 됩니다. 따라서 소스 코드 파일을 전송 하는 데 blob storage를 사용 하도록 실행을 구성 하는 것이 좋습니다.

다음 코드 예제에서는 실행 구성에서 소스 코드 전송에 사용할 blob 데이터 저장소를 지정 합니다.

```python 
# workspaceblobstore is the default blob storage
run_config.source_directory_data_store = "workspaceblobstore" 
```

## <a name="access-data-during-scoring"></a>점수 매기기 중 데이터 액세스

Azure Machine Learning에서는 모델을 사용 하 여 점수를 매기는 여러 가지 방법을 제공 합니다. 이러한 메서드 중 일부는 데이터 저장소에 대 한 액세스를 제공 하지 않습니다. 다음 표를 사용 하 여 점수 매기기 중에 데이터 저장소에 액세스할 수 있는 방법을 알아봅니다.

| 방법 | 데이터 저장소 액세스 | 설명 |
| ----- | :-----: | ----- |
| [일괄 처리 예측](how-to-run-batch-predictions.md) | ✔ | 많은 양의 데이터를 비동기적으로 예측 합니다. |
| [웹 서비스](how-to-deploy-and-where.md) | &nbsp; | 모델을 웹 서비스로 배포 합니다. |
| [IoT Edge 모듈](how-to-deploy-and-where.md) | &nbsp; | IoT Edge 장치에 모델을 배포 합니다. |

SDK가 datastores에 대 한 액세스를 제공 하지 않는 경우 관련 Azure SDK를 사용 하 여 데이터에 액세스 하는 사용자 지정 코드를 만들 수 있습니다. 예를 들어 [Python 용 AZURE STORAGE SDK](https://github.com/Azure/azure-storage-python) 는 blob 또는 파일에 저장 된 데이터에 액세스 하는 데 사용할 수 있는 클라이언트 라이브러리입니다.

<a name="move"></a>
## <a name="move-data-to-supported-azure-storage-solutions"></a>지원 되는 Azure storage 솔루션으로 데이터 이동

Azure Machine Learning는 Azure Blob, Azure File, Azure Data Lake Gen 1 Azure Data Lake Gen 2, Azure SQL, Azure PostgreSQL의 데이터에 액세스할 수 있도록 지원 합니다. 지원 되지 않는 저장소의 경우 machine learning 실험 중에 데이터 송신 비용을 절약 하려면 Azure Data Factory을 사용 하 여 지원 되는 Azure storage 솔루션으로 데이터를 이동 하는 것이 좋습니다. Azure Data Factory는 Azure data services, 온-프레미스 데이터 원본, Amazon S3 및 Redshift, Google 이상 쿼리 등 80 개 이상의 미리 작성 된 커넥터를 사용 하 여 추가 비용 없이 효율적이 고 복원 력 있는 데이터 전송을 제공 합니다. [Azure Data Factory를 사용 하 여 데이터를 이동 하려면 단계별 가이드를 따르세요](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-copy-data-tool).

## <a name="next-steps"></a>다음 단계

* [모델을 학습합니다](how-to-train-ml-models.md).

* [모델을 배포](how-to-deploy-and-where.md)합니다.
