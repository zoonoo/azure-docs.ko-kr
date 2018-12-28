---
title: 학습을 위해 데이터 저장소의 데이터 액세스
titleSuffix: Azure Machine Learning service
description: 데이터 저장소를 사용하여 Azure Machine Learning Service로 학습 중에 데이터 스토리지에 액세스하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: minxia
author: mx-iao
ms.reviewer: sgilley
ms.date: 09/24/2018
ms.custom: seodec18
ms.openlocfilehash: 235ad729be434c8329a74e71abfe713480756316
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2018
ms.locfileid: "53187146"
---
# <a name="access-data-during-training-from-your-datastores"></a>데이터 저장소에서 학습 중에 데이터 액세스
데이터 저장소를 사용하여 Azure Machine Learning 워크플로의 데이터에 액세스하고 상호 작용합니다.

Azure Machine Learning Services에서 데이터 저장소는 [Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-introduction)보다 추상적인 개념입니다. 데이터 저장소는 [Azure Blob](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) 컨테이너 또는 [Azure 파일 공유](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) 중 하나를 기본 저장소로 참조할 수 있습니다. 

## <a name="create-a-datastore"></a>데이터 저장소 만들기
데이터 저장소를 사용하려면 먼저 [작업 영역](concept-azure-machine-learning-architecture.md#workspace)이 필요합니다. [새 작업 영역을 만들](quickstart-create-workspace-with-python.md)거나 기존 작업 영역을 검색합니다.

```Python
import azureml.core
from azureml.core import Workspace

ws = Workspace.from_config()
```

### <a name="use-the-default-datastore"></a>기본 데이터 저장소 사용
스토리지 계정을 만들거나 구성할 필요가 없습니다.  각 작업 영역에는 즉시 사용을 시작할 수 있는 기본 데이터 저장소가 있습니다.

작업 영역의 기본 데이터 저장소를 가져오려면 다음 코드를 사용합니다.
```Python
ds = ws.get_default_datastore()
```

### <a name="register-a-datastore"></a>데이터 저장소 등록
기존 Azure Storage가 있는 경우에는 작업 영역의 데이터 저장소로 등록할 수 있습니다. 데이터 저장소로 Azure Blob 컨테이너 또는 Azure 파일 공유를 등록할 수 있습니다. 모든 등록 메서드는 `Datastore` 클래스에 포함되어 있으며 형식은 `register_azure_*`입니다.

#### <a name="azure-blob-container-datastore"></a>Azure Blob 컨테이너 데이터 저장소
Azure Blob 컨테이너 데이터 저장소를 등록하려면 다음 코드를 사용합니다.

```Python
ds = Datastore.register_azure_blob_container(workspace=ws, 
                                             datastore_name='your datastore name', 
                                             container_name='your azure blob container name',
                                             account_name='your storage account name', 
                                             account_key='your storage account key',
                                             create_if_not_exists=True)
```

#### <a name="azure-file-share-datastore"></a>Azure 파일 공유 데이터 저장소
Azure 파일 공유 데이터 저장소를 등록하려면 다음 코드를 사용합니다.

```Python
ds = Datastore.register_azure_file_share(workspace=ws, 
                                         datastore_name='your datastore name', 
                                         container_name='your file share name',
                                         account_name='your storage account name', 
                                         account_key='your storage account key',
                                         create_if_not_exists=True)
```

### <a name="get-an-existing-datastore"></a>기존 데이터 저장소 가져오기
등록된 데이터 저장소를 이름으로 쿼리하려면 다음 코드를 사용합니다.
```Python
ds = Datastore.get(ws, datastore_name='your datastore name')
```

작업 영역의 모든 데이터 저장소를 가져올 수도 있습니다.
```Python
datastores = ws.datastores()
for name, ds in datastores.items(),
    print(name, ds.datastore_type)
```

편의상 등록된 데이터 저장소 중 하나를 작업 영역의 기본 데이터 저장소로 설정합니다.
```Python
ws.set_default_datastore('your datastore name')
```

## <a name="upload-and-download-data"></a>데이터 업로드 및 다운로드
### <a name="upload"></a>업로드
Python SDK를 사용하여 데이터 저장소에 디렉터리 또는 개별 파일을 업로드합니다.

데이터 저장소 `ds`에 디렉터리를 업로드하려면 다음 명령을 사용합니다.
```Python
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

## <a name="access-datastores-for-training"></a>학습을 위해 데이터 저장소 액세스
Python SDK를 통해 원격 컴퓨팅 대상에서 교육을 실행하는 중에 학습 또는 유효성 검사 데이터를 확인하기 위해 데이터 저장소에 액세스할 수 있습니다. 

두 가지 방식을 통해 원격 컴퓨터에서 데이터 저장소를 사용할 수 있습니다.
* **탑재**  
`ds.as_mount()`: 이 탑재 모드를 지정하면 데이터 저장소가 원격 컴퓨터에 자동으로 탑재됩니다. 
* **다운로드/업로드**  
    * `ds.as_download(path_on_compute='your path on compute')`는 데이터 저장소의 데이터를 `path_on_compute`로 지정된 원격 계산의 위치에 다운로드합니다.
    * `ds.as_upload(path_on_compute='yourfilename'`는 데이터 저장소에 데이터를 업로드합니다.  학습 스크립트에서 원격 계산의 현재 작업 디렉터리에 `foo.pkl` 파일을 만든다고 가정합니다. 스크립트에서 파일을 만든 후 `ds.as_upload(path_on_compute='./foo.pkl')`를 사용하여 이 파일을 데이터 저장소에 업로드합니다. 파일이 데이터 저장소의 루트에 업로드됩니다.
    
데이터 저장소의 특정 폴더나 파일을 참조하려면 데이터 저장소의 **`path`** 함수를 사용합니다. 예를 들어 데이터 저장소에서 `./bar` 디렉터리의 콘텐츠를 컴퓨팅 대상에 다운로드하려면 `ds.path('./bar').as_download()`를 사용합니다.

모든 `ds` 또는 `ds.path` 개체는 `"$AZUREML_DATAREFERENCE_XXXX"` 형식의 환경 변수 이름으로 확인됩니다. 이 환경 변수의 값은 원격 컴퓨터의 탑재/다운로드 경로를 나타냅니다. 원격 컴퓨터의 데이터 저장소 경로는 스크립트의 실행 경로와 같을 수 없습니다.

학습 중에 데이터 저장소에 액세스하려면 `script_params`를 통해 학습 스크립트에 데이터 저장소를 명령줄 인수로 전달합니다.

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
`as_mount()`는 데이터 저장소의 기본 모드이므로 `ds`만 `'--data_dir'` 인수에 직접 전달해도 됩니다.

또는 데이터 저장소 목록을 Estimator 생성자 `inputs` 매개 변수에 전달하여 데이터 저장소를 탑재하거나 데이터 저장소에서/데이터 저장소로 데이터를 복사합니다.

```Python
est = Estimator(source_directory='your code directory',
                compute_target=compute_target,
                entry_script='train.py',
                inputs=[ds1.as_download(), ds2.path('./foo').as_download(), ds3.as_upload(path_on_compute='./bar.pkl')])
```
위의 코드는 다음 작업을 수행합니다.
* 학습 스크립트 `train.py`가 실행되기 전에 데이터 저장소 `ds1`의 모든 콘텐츠를 원격 컴퓨터에 다운로드합니다.
* `train.py`가 실행되기 전에 데이터 저장소 `ds2`의 `'./foo'` 폴더를 원격 컴퓨터에 다운로드합니다.
* 스크립트가 실행된 후 `'./bar.pkl'` 파일을 원격 컴퓨터에서 데이터 저장소 `d3`에 업로드합니다.

## <a name="next-steps"></a>다음 단계
* [모델 학습](how-to-train-ml-models.md)
