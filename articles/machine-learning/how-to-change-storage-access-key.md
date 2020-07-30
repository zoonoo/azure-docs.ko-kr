---
title: 저장소 계정 액세스 키 변경
titleSuffix: Azure Machine Learning
description: 작업 영역에서 사용 하는 Azure Storage 계정의 액세스 키를 변경 하는 방법에 대해 알아봅니다. Azure Machine Learning는 Azure Storage 계정을 사용 하 여 데이터 및 모델을 저장 합니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 06/19/2020
ms.openlocfilehash: c6b8f9d7f99d49f65a45d0e09623ef2f0e1217f2
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/29/2020
ms.locfileid: "87387067"
---
# <a name="regenerate-storage-account-access-keys"></a>저장소 계정 액세스 키 다시 생성
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Azure Machine Learning에서 사용 하는 Azure Storage 계정의 액세스 키를 변경 하는 방법에 대해 알아봅니다. 저장소 계정을 사용 하 여 데이터 나 학습 된 모델을 저장할 수 Azure Machine Learning.

보안을 위해 Azure Storage 계정에 대 한 액세스 키를 변경 해야 할 수 있습니다. 액세스 키를 다시 생성 하는 경우 새 키를 사용 하도록 Azure Machine Learning 업데이트 해야 합니다. Azure Machine Learning은 모델 저장소 및 데이터 저장소에 대 한 저장소 계정을 사용할 수 있습니다.

> [!IMPORTANT]
> 데이터 저장소를 사용 하는 자격 증명 registred 작업 영역과 연결 된 Azure Key Vault에 저장 됩니다. Key Vault에 대해 [일시 삭제](https://docs.microsoft.com/azure/key-vault/general/soft-delete-overview) 를 사용 하도록 설정한 경우 자격 증명 업데이트에 대 한이 문서를 참조 하세요. 데이터 저장소의 등록을 취소 하 고 같은 이름으로 다시 등록 하면 실패 합니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure Machine Learning 작업 영역 자세한 내용은 [작업 영역 만들기](how-to-manage-workspace.md) 문서를 참조 하세요.

* [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)

* [AZURE MACHINE LEARNING CLI 확장](reference-azure-machine-learning-cli.md)입니다.

> [!NOTE]
> 이 문서의 코드 조각은 Python SDK의 버전 1.0.83 테스트 되었습니다.

<a id="whattoupdate"></a> 

## <a name="what-needs-to-be-updated"></a>업데이트 해야 하는 항목

저장소 계정은 Azure Machine Learning 작업 영역에서 사용할 수 있습니다. 즉, 로그, 모델, 스냅숏 등을 저장 하 고 데이터 저장소로 사용할 수 있습니다. 작업 영역을 업데이트 하는 프로세스는 단일 Azure CLI 명령이 며 저장소 키를 업데이트 한 후에 실행할 수 있습니다. 데이터 저장소를 업데이트 하는 프로세스는 더 복잡 하며, 현재 저장소 계정을 사용 하는 데이터 저장소를 검색 한 다음 다시 등록 해야 합니다.

> [!IMPORTANT]
> Azure CLI를 사용 하 여 작업 영역을 업데이트 하 고 Python을 사용 하 여 데이터 저장소를 동시에 업데이트 합니다. 하나만 업데이트 해도 충분 하지 않으며 둘 다 업데이트 될 때까지 오류가 발생할 수 있습니다.

Datastores에서 사용 하는 저장소 계정을 검색 하려면 다음 코드를 사용 합니다.

```python
import azureml.core
from azureml.core import Workspace, Datastore

ws = Workspace.from_config()

default_ds = ws.get_default_datastore()
print("Default datstore: " + default_ds.name + ", storage account name: " +
      default_ds.account_name + ", container name: " + default_ds.container_name)

datastores = ws.datastores
for name, ds in datastores.items():
    if ds.datastore_type == "AzureBlob":
        print("Blob store - datastore name: " + name + ", storage account name: " +
              ds.account_name + ", container name: " + ds.container_name)
    if ds.datastore_type == "AzureFile":
        print("File share - datastore name: " + name + ", storage account name: " +
              ds.account_name + ", container name: " + ds.container_name)
```

이 코드는 Azure Storage를 사용 하는 등록 된 모든 데이터 저장소를 검색 하 고 다음 정보를 나열 합니다.

* 데이터 저장소 이름: 저장소 계정이 등록 된 데이터 저장소의 이름입니다.
* Storage 계정 이름: Azure Storage 계정의 이름입니다.
* 컨테이너:이 등록에서 사용 하는 저장소 계정의 컨테이너입니다.

또한 각 유형의 데이터 저장소를 다시 등록 하는 다양 한 방법이 있으므로 데이터 저장소가 Azure Blob 또는 Azure 파일 공유에 대 한 것인지 여부를 나타냅니다.

에 대 한 액세스 키를 다시 생성 하려는 저장소 계정에 대 한 항목이 있는 경우 데이터 저장소 이름, 저장소 계정 이름 및 컨테이너 이름을 저장 합니다.

## <a name="update-the-access-key"></a>액세스 키 업데이트

새 키를 사용 하도록 Azure Machine Learning를 업데이트 하려면 다음 단계를 사용 합니다.

> [!IMPORTANT]
> 모든 단계를 수행 하 여 CLI를 사용 하 여 작업 영역을 업데이트 하 고 Python을 사용 하 여 데이터 저장소를 업데이트 합니다. 하나만 업데이트 하면 둘 다 업데이트 될 때까지 오류가 발생할 수 있습니다.

1. 키를 다시 생성 합니다. 액세스 키를 다시 생성 하는 방법에 대 한 자세한 내용은 [저장소 계정 액세스 키 관리](../storage/common/storage-account-keys-manage.md)를 참조 하세요. 새 키를 저장 합니다.

1. Azure Machine Learning 작업 영역은 자동으로 새 키를 동기화 하 고 한 시간 후에 사용을 시작 합니다. 작업 영역을 즉시 새 키로 동기화 하려면 다음 단계를 사용 합니다.

    1. 다음 Azure CLI 명령을 사용 하 여 작업 영역을 포함 하는 Azure 구독에 로그인 합니다.

        ```azurecli-interactive
        az login
        ```

        [!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)]

    1. 새 키를 사용 하도록 작업 영역을 업데이트 하려면 다음 명령을 사용 합니다. 을 `myworkspace` Azure Machine Learning 작업 영역 이름으로 바꾸고,을 `myresourcegroup` 작업 영역을 포함 하는 Azure 리소스 그룹의 이름으로 바꿉니다.

        ```azurecli-interactive
        az ml workspace sync-keys -w myworkspace -g myresourcegroup
        ```

        [!INCLUDE [install extension](../../includes/machine-learning-service-install-extension.md)]

        이 명령은 작업 영역에서 사용 하는 Azure storage 계정에 대 한 새 키를 자동으로 동기화 합니다.

1. SDK 또는 [Azure Machine Learning studio](https://ml.azure.com)를 통해 저장소 계정을 사용 하는 데이터 저장소를 다시 등록할 수 있습니다.
    1. **Python SDK를 통해 데이터 저장소를 다시 등록 하려면**다음 코드를 사용 하 여 [업데이트](#whattoupdate) 해야 할 항목 섹션과 1 단계에서 제공 하는 키의 값을 사용 합니다. 
    
        `overwrite=True`가 지정 되었으므로이 코드는 기존 등록을 덮어쓰고 새 키를 사용 하도록 업데이트 합니다.
    
        ```python
        # Re-register the blob container
        ds_blob = Datastore.register_azure_blob_container(workspace=ws,
                                                  datastore_name='your datastore name',
                                                  container_name='your container name',
                                                  account_name='your storage account name',
                                                  account_key='new storage account key',
                                                  overwrite=True)
        # Re-register file shares
        ds_file = Datastore.register_azure_file_share(workspace=ws,
                                              datastore_name='your datastore name',
                                              file_share_name='your container name',
                                              account_name='your storage account name',
                                              account_key='new storage account key',
                                              overwrite=True)
        
        ```
    
    1. **스튜디오를 통해 데이터 저장소를 다시 등록 하려면**스튜디오 왼쪽 창에서 **데이터 저장소** 를 선택 합니다. 
        1. 업데이트 하려는 데이터 저장소를 선택 합니다.
        1. 왼쪽 위에서 **자격 증명 업데이트** 단추를 선택 합니다. 
        1. 1 단계의 새 선택 키를 사용 하 여 양식을 채운 다음 **저장**을 클릭 합니다.
        
            **기본 데이터 저장소**에 대 한 자격 증명을 업데이트 하는 경우이 단계를 완료 하 고 2b 단계를 반복 하 여 새 키를 작업 영역의 기본 데이터 저장소로 다시 동기화 합니다. 

## <a name="next-steps"></a>다음 단계

Datastores를 등록 하는 방법에 대 한 자세한 내용은 [`Datastore`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) 클래스 참조를 참조 하세요.
