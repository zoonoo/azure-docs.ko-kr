---
title: 저장소 계정 액세스 키를 변경 합니다.
titleSuffix: Azure Machine Learning service
description: 작업 영역에서 사용 하는 Azure Storage 계정에 대 한 액세스 키를 변경 하는 방법에 알아봅니다. Azure Machine Learning 서비스는 Azure Storage 계정을 사용 하 여 데이터 및 모델 저장 합니다. 저장소 계정의 액세스 키를 다시 생성 하면 새 키를 사용 하려면 Azure Machine Learning 서비스를 업데이트 해야 합니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 05/17/2019
ms.openlocfilehash: 488a032e177897caf2897ba6335f4e7f64dc0e4d
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67543836"
---
# <a name="regenerate-storage-account-access-keys"></a>저장소 계정 액세스 키 다시 생성

Azure Machine Learning 서비스에서 사용 하는 Azure Storage 계정에 대 한 액세스 키를 변경 하는 방법에 알아봅니다. Azure Machine Learning 데이터 또는 학습 된 모델을 저장할 저장소 계정을 사용할 수 있습니다.

보안상의 이유로 Azure Storage 계정에 대 한 액세스 키를 변경 해야 합니다. 액세스 키를 다시 생성 하면 새 키를 사용 하려면 Azure Machine Learning 업데이트 되어야 합니다. Azure Machine Learning 데이터 저장소와 두 모델 저장소에 대 한 저장소 계정을 사용할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

* Azure Machine Learning 서비스 작업 영역. 자세한 내용은 참조는 [작업 영역 만들기](setup-create-workspace.md) 문서.

* 합니다 [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)합니다.

* 합니다 [Azure Machine Learning CLI 확장](reference-azure-machine-learning-cli.md)합니다.

<a id="whattoupdate"></a> 

## <a name="what-needs-to-be-updated"></a>업데이트 하는 데 필요한

저장소 계정 (로그, 모델, 스냅숏 등 저장) Azure Machine Learning 서비스 작업 영역에서 데이터 저장소로 사용할 수 있습니다. 작업 영역을 업데이트 하는 프로세스는 사용 하는 단일 Azure CLI 명령 및 저장소 키를 업데이트 한 후 실행할 수 있습니다. 데이터 저장소 업데이트 프로세스가 더 복잡 하며 어떤 데이터 저장소는 저장소 계정을 현재 사용 중인 검색 하 고 다시 등록 합니다.

> [!IMPORTANT]
> Azure CLI 및 Python을 사용 하 여 동시에 데이터 저장소를 사용 하 여 작업 영역을 업데이트 합니다. 하나만 또는 다른 업데이트 충분 하지 않습니다 하 고 둘 다가 업데이트 될 때까지 오류가 발생할 수 있습니다.

사용자 데이터 저장소에서 사용 되는 저장소 계정을 검색 하려면 다음 코드를 사용 합니다.

```python
import azureml.core
from azureml.core import Workspace, Datastore

ws = Workspace.from_config()

default_ds = ws.get_default_datastore()
print("Default datstore: " + default_ds.name + ", storage account name: " + default_ds.account_name + ", container name: " + ds.container_name)

datastores = ws.datastores
for name, ds in datastores.items():
    if ds.datastore_type == "AzureBlob" or ds.datastore_type == "AzureFile":
        print("datastore name: " + name + ", storage account name: " + ds.account_name + ", container name: " + ds.container_name)
```

이 코드는 Azure Storage를 사용 하는 모든 등록 된 데이터 저장소를 찾습니다 하 고 다음 정보를 나열 합니다.

* 데이터 저장소 이름: 저장소 계정에서 등록 된 데이터 저장소의 이름입니다.
* Storage 계정 이름: Azure Storage 계정의 이름입니다.
* 컨테이너: 이 등록에서 사용 되는 저장소 계정에 대 한 컨테이너입니다.

항목에 대 한 액세스 키 다시 생성 하려는 저장소 계정에 있는 경우 데이터 저장소 이름, 저장소 계정 이름 및 컨테이너 이름을 저장 합니다.

## <a name="update-the-access-key"></a>액세스 키 업데이트

새 키를 사용 하려면 Azure Machine Learning 서비스를 업데이트 하려면 다음 단계를 사용 합니다.

> [!IMPORTANT]
> 둘 다 CLI 및 Python을 사용 하 여 데이터 저장소를 사용 하 여 작업 영역을 업데이트 하는 모든 단계를 수행 합니다. 둘 다가 업데이트 될 때까지 하나만 또는 다른 업데이트 오류가 발생할 수 있습니다.

1. 키 다시 생성 합니다. 액세스 키 다시 생성에 대 한 내용은 참조는 [저장소 계정 관리](/azure/storage/common/storage-account-manage#access-keys) 문서. 새 키를 저장 합니다.

1. 새 키를 사용 하도록 작업 영역을 업데이트 하려면 다음 단계를 사용 합니다.

    1. 다음 Azure CLI 명령을 사용 하 여 작업 영역을 포함 하는 Azure 구독에 로그인 합니다.

        ```azurecli-interactive
        az login
        ```

    1. Azure Machine Learning 확장을 설치 하려면 다음 명령을 사용 합니다.

        ```azurecli-interactive
        az extension add -n azure-cli-ml 
        ```

    1. 새 키를 사용 하도록 작업 영역을 업데이트 하려면 다음 명령을 사용 합니다. 바꿉니다 `myworkspace` 에 Azure Machine Learning 작업 영역 이름으로 바꾸고를 사용 하 여 `myresourcegroup` 작업 영역을 포함 하는 Azure 리소스 그룹의 이름입니다.

        ```azurecli-interactive
        az ml workspace sync-keys -w myworkspace -g myresourcegroup
        ```

        이 명령은 작업 영역에서 사용 하는 Azure storage 계정에 대 한 새 키를 자동으로 동기화 합니다.

1. 저장소 계정을 사용 하는 데이터 저장소를 다시 등록 하려면의 값을 사용 합니다 [업데이트 하는 데 필요한](#whattoupdate) 섹션 및 다음 코드를 사용 하 여 1 단계에서 키:

    ```python
    ds = Datastore.register_azure_blob_container(workspace=ws, 
                                              datastore_name='your datastore name', 
                                              container_name='your container name',
                                              account_name='your storage account name', 
                                              account_key='new storage account key',
                                              overwrite=True)
    ```

    이후 `overwrite=True` 를 지정한 경우이 코드는 기존 등록 덮어씁니다 하 고 새 키를 사용 하도록 업데이트 합니다.

## <a name="next-steps"></a>다음 단계

데이터 저장소를 등록에 대 한 자세한 내용은 참조는 [ `Datastore` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) 클래스 참조 합니다.
