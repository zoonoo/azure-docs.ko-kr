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
ms.date: 02/25/2019
ms.custom: seodec18
ms.openlocfilehash: af36f38bf206da588d327dc319d2418460f79b13
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/18/2019
ms.locfileid: "58165031"
---
# <a name="access-data-from-your-datastores"></a>사용자 데이터 저장소에서 데이터에 액세스

데이터 저장소를 사용 하면 상호 작용 하는 계산 클러스터 또는 가상 컴퓨터에서 코드를 로컬로 실행 하 든 데이터에 액세스할 수 있습니다. 이 문서에서는 사용자 데이터 저장소를 확인 하는 Azure Machine Learning 워크플로 액세스할 수 있는 알아봅니다 하 고 계산 컨텍스트를 제공 합니다.

이 방법에서는 다음 작업에 대 한 예제를 보여 줍니다.
* [데이터 저장소 선택](#access)
* [데이터 가져오기](#get)
* [업로드 하 고 데이터 저장소에 데이터를 다운로드 합니다.](#up-and-down)
* [학습 중 데이터 저장소 액세스](#train)

## <a name="prerequisites"></a>필수 조건

필요한 데이터 저장소를 사용 하는 [작업 영역](concept-azure-machine-learning-architecture.md#workspace) 첫 번째입니다. 

[새 작업 영역을 만들](quickstart-create-workspace-with-python.md)거나 기존 작업 영역을 검색합니다.

```Python
import azureml.core
from azureml.core import Workspace, Datastore

ws = Workspace.from_config()
```

또는 [Python 빠른 시작이 수행](quickstart-create-workspace-with-python.md) 에 SDK를 사용 하 여 작업 영역 만들기를 시작 합니다.

<a name="access"></a>

## <a name="choose-a-datastore"></a>데이터 저장소 선택

기본 데이터 저장소를 사용 하거나 직접 준비 수 있습니다.

### <a name="use-the-default-datastore-in-your-workspace"></a>작업 영역에서 기본 데이터 저장소를 사용 합니다.

만들고 각 작업 영역의 기본 데이터 저장소에 있으므로 저장소 계정을 구성 하거나 필요가 없습니다. 데이터 저장소 바로으로 작업 영역에 이미 등록 되어 사용할 수 있습니다. 

작업 영역의 기본 데이터 저장소를 가져오려면 다음 코드를 사용합니다.
```Python
ds = ws.get_default_datastore()
```

### <a name="register-your-own-datastore-with-the-workspace"></a>작업 영역을 사용 하 여 사용자 고유의 데이터 저장소를 등록 합니다.
기존 Azure Storage가 있는 경우에는 작업 영역의 데이터 저장소로 등록할 수 있습니다.   모든 등록 방법에는 [ `Datastore` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) 클래스 및 폼 register_azure_ * 있어야 합니다. 

다음 예제에서는 표시를 데이터 저장소로 Azure Blob 컨테이너 또는 Azure 파일 공유를 등록할 수 있습니다.

+ 에 **Azure Blob 컨테이너 데이터 저장소**를 사용 하 여 [`register_azure_blob-container()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py)

  ```Python
  ds = Datastore.register_azure_blob_container(workspace=ws, 
                                               datastore_name='your datastore name', 
                                               container_name='your azure blob container name',
                                               account_name='your storage account name', 
                                               account_key='your storage account key',
                                               create_if_not_exists=True)
  ```

+ 에 **Azure 파일 공유 데이터 저장소**를 사용 하 여 [ `register_azure_file_share()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-)합니다. 예:  
  ```Python
  ds = Datastore.register_azure_file_share(workspace=ws, 
                                           datastore_name='your datastore name', 
                                           container_name='your file share name',
                                           account_name='your storage account name', 
                                           account_key='your storage account key',
                                           create_if_not_exists=True)
  ```

<a name="get"></a>

## <a name="find--define-datastores"></a>찾기 및 데이터 저장소를 정의 합니다.

현재 작업 영역에 등록 하는 지정 된 데이터 저장소를 가져오려면 [ `get()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#get-workspace--datastore-name-) :

```Python
#get named datastore from current workspace
ds = Datastore.get(ws, datastore_name='your datastore name')
```

지정된 된 작업 영역에서 모든 데이터 저장소의 목록을 가져오려면,이 코드를 사용 합니다.

```Python
#list all datastores registered in current workspace
datastores = ws.datastores
for name, ds in datastores.items():
    print(name, ds.datastore_type)
```

현재 작업 영역에 대 한 다른 기본 데이터 저장소를 정의 하기 위해 사용 하 여 [ `set_default_datastore()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#set-default-datastore-name-):

```Python
#define default datastore for current workspace
ws.set_default_datastore('your datastore name')
```

<a name="up-and-down"></a>
## <a name="upload--download-data"></a>업로드 및 다운로드 데이터
[ `upload()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-) 하 고 [ `download()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-) 다음 예제에 설명 된 방법와 관련이 있으며 동일 하 게 작동 합니다 [AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py) 및 [AzureFileDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py) 클래스입니다.

### <a name="upload"></a>업로드

 Python SDK를 사용하여 데이터 저장소에 디렉터리 또는 개별 파일을 업로드합니다.

데이터 저장소 `ds`에 디렉터리를 업로드하려면 다음 명령을 사용합니다.

```Python
import azureml.data
from azureml.data import AzureFileDatastore, AzureBlobDatastore

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
## <a name="access-datastores-during-training"></a>학습 중 데이터 저장소 액세스

원격 컴퓨터에서 데이터 저장소를 사용할 수 있도록 면 단순히 학습 스크립트의 매개 변수에 경로를 전달 하 여 교육 실행 (예: 학습 또는 유효성 검사 데이터) 하는 동안 액세스할 수 있습니다.

다음 표에서 일반적인 [ `DataReference` ](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py) 원격 계산에서 데이터 저장소를 사용할 수 있도록 하는 메서드에 해당 합니다.

##

방법|방법|설명
----|-----|--------
탑재| [`as_mount()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--)| 원격 컴퓨터에서 데이터 저장소를 탑재 하려면 사용 합니다. 데이터 저장소에 대 한 기본 모드입니다.
다운로드|[`as_download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-)|지정 된 위치에서 데이터를 다운로드 하는 데 `path_on_compute` 원격 계산을 데이터 저장소에 있습니다.
업로드|[`as_upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)| 지정 된 위치에서 데이터 저장소의 루트에 데이터 업로드를 사용 하 여 `path_on_compute`입니다.

```Python
import azureml.data
from azureml.data import DataReference

ds.as_mount()
ds.as_download(path_on_compute='your path on compute')
ds.as_upload(path_on_compute='yourfilename')
```  

데이터 저장소의 특정 폴더나 파일을 참조하려면 데이터 저장소의 [`path()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#path-path-none--data-reference-name-none-) 함수를 사용합니다.

```Python
#download the contents of the `./bar` directory from the datastore to the remote compute
ds.path('./bar').as_download()
```



> [!NOTE]
> 모든 `ds` 또는 `ds.path` 개체는 `"$AZUREML_DATAREFERENCE_XXXX"` 형식의 환경 변수 이름으로 확인됩니다. 이 환경 변수의 값은 원격 컴퓨터의 탑재/다운로드 경로를 나타냅니다. 원격 계산의 데이터 저장소 경로 학습 스크립트의 실행 경로와 동일한 수 없습니다.

### <a name="examples"></a>예 

다음 설명에 대 한 예는 [ `Estimator` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) 학습 중에 데이터 저장소에 액세스 하기 위한 클래스입니다.


```Python
from azureml.train.estimator import Estimator

script_params = {
    '--data_dir': ds.as_mount()
}

est = Estimator(source_directory='your code directory',
                script_params=script_params,
                compute_target=compute_target,
                entry_script='train.py')
```

이후 `as_mount()` 는 기본 모드는 데이터 저장소에 대 한 직접을 전달할 수 있습니다 `ds` 에 `'--data_dir'` 인수.

데이터 저장소 목록에 스 티 메이 터 생성자에 전달 하거나 `inputs` 매개 변수를 탑재 또는 사용자 데이터 저장소에서 복사 합니다. 이 코드 예제:
* 데이터 저장소에 모든 콘텐츠를 다운로드 `ds1` 학습 스크립트 하기 전에 원격 컴퓨터에 `train.py` 실행
* 폴더를 다운로드 `'./foo'` 데이터 저장소에 `ds2` 하기 전에 원격 컴퓨터에 `train.py` 실행
* 파일을 업로드 `'./bar.pkl'` 데이터 저장소를 최대 원격 계산에서 `ds3` 스크립트를 실행 한 후

```Python
est = Estimator(source_directory='your code directory',
                compute_target=compute_target,
                entry_script='train.py',
                inputs=[ds1.as_download(), ds2.path('./foo').as_download(), ds3.as_upload(path_on_compute='./bar.pkl')])
```


## <a name="next-steps"></a>다음 단계

* [모델 학습](how-to-train-ml-models.md)

* [모델 배포](how-to-deploy-and-where.md)
