---
title: 저장소 계정 액세스 키 변경
titleSuffix: Azure Machine Learning
description: 작업 영역에서 사용하는 Azure Storage 계정의 액세스 키를 변경하는 방법에 대해 알아봅니다. Azure 기계 학습은 Azure Storage 계정을 사용하여 데이터와 모델을 저장합니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 03/06/2020
ms.openlocfilehash: f1541c177cea2d223a5e7df576d95fab7eafb310
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80296926"
---
# <a name="regenerate-storage-account-access-keys"></a>저장소 계정 액세스 키 재생성
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Azure 기계 학습에서 사용하는 Azure Storage 계정의 액세스 키를 변경하는 방법에 대해 알아봅니다. Azure Machine Learning은 저장소 계정을 사용하여 데이터 또는 학습된 모델을 저장할 수 있습니다.

보안을 위해 Azure Storage 계정의 액세스 키를 변경해야 할 수 있습니다. 액세스 키를 다시 생성할 때 새 키를 사용하려면 Azure 기계 학습을 업데이트해야 합니다. Azure 기계 학습은 모델 저장소와 데이터스토어 모두에 저장소 계정을 사용하고 있을 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure Machine Learning 작업 영역 자세한 내용은 작업 [영역 만들기](how-to-manage-workspace.md) 문서를 참조하십시오.

* [Azure 기계 학습 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

* [Azure 기계 학습 CLI 확장.](reference-azure-machine-learning-cli.md)

> [!NOTE]
> 이 문서의 코드 조각은 Python SDK의 버전 1.0.83으로 테스트되었습니다.

<a id="whattoupdate"></a> 

## <a name="what-needs-to-be-updated"></a>업데이트해야 할 사항

저장소 계정은 Azure Machine Learning 작업 영역(로그, 모델, 스냅숏 저장 등)과 데이터스토어로 사용할 수 있습니다. 작업 영역을 업데이트하는 프로세스는 단일 Azure CLI 명령이며 저장소 키를 업데이트한 후 실행될 수 있습니다. 데이터 스토어를 업데이트하는 프로세스가 더 많이 필요하며 현재 저장소 계정을 사용하고 있는 데이터 스토어를 검색한 다음 다시 등록해야 합니다.

> [!IMPORTANT]
> Azure CLI를 사용하여 작업 영역과 Python을 사용하여 데이터 스토어를 동시에 업데이트합니다. 둘 중 하나만 업데이트하는 것만으로는 충분하지 않으며 둘 다 업데이트될 때까지 오류가 발생할 수 있습니다.

데이터 스토어에서 사용하는 저장소 계정을 검색하려면 다음 코드를 사용합니다.

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

이 코드는 Azure Storage를 사용하는 등록된 모든 데이터스토어를 찾고 다음 정보를 나열합니다.

* 데이터스토어 이름: 저장소 계정이 등록된 데이터스토어의 이름입니다.
* 저장소 계정 이름: Azure Storage 계정의 이름입니다.
* 컨테이너: 이 등록에서 사용되는 저장소 계정의 컨테이너입니다.

또한 각 데이터스토어 유형을 다시 등록하는 여러 가지 방법이 있으므로 데이터스토어가 Azure Blob 또는 Azure File 공유용인지 여부를 나타냅니다.

액세스 키를 다시 생성하려는 저장소 계정에 대한 항목이 있는 경우 데이터스토어 이름, 저장소 계정 이름 및 컨테이너 이름을 저장합니다.

## <a name="update-the-access-key"></a>액세스 키 업데이트

새 키를 사용하도록 Azure 기계 학습을 업데이트하려면 다음 단계를 사용합니다.

> [!IMPORTANT]
> CLI를 사용하여 작업 영역과 Python을 사용하여 데이터 스토어를 모두 업데이트하여 모든 단계를 수행합니다. 둘 다 업데이트될 때까지 둘 중 하나만 업데이트하면 오류가 발생할 수 있습니다.

1. 키를 다시 생성합니다. 액세스 키 를 다시 생성하는 방법에 대한 자세한 내용은 [저장소 계정 액세스 키 관리를](../storage/common/storage-account-keys-manage.md)참조하십시오. 새 키를 저장합니다.

1. 새 키를 사용하도록 작업 영역을 업데이트하려면 다음 단계를 사용합니다.

    1. 다음 Azure CLI 명령을 사용하여 작업 영역을 포함하는 Azure 구독에 로그인하려면 다음을 수행합니다.

        ```azurecli-interactive
        az login
        ```

        [!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)]

    1. 새 키를 사용하도록 작업 영역을 업데이트하려면 다음 명령을 사용합니다. Azure `myworkspace` 기계 학습 작업 영역 이름으로 `myresourcegroup` 바꾸고 작업 영역이 포함된 Azure 리소스 그룹의 이름으로 바꿉니다.

        ```azurecli-interactive
        az ml workspace sync-keys -w myworkspace -g myresourcegroup
        ```

        [!INCLUDE [install extension](../../includes/machine-learning-service-install-extension.md)]

        이 명령은 작업 영역에서 사용되는 Azure 저장소 계정에 대한 새 키를 자동으로 동기화합니다.

1. 저장소 계정을 사용하는 Datastore를 다시 등록하려면 [업데이트해야 하는 내용](#whattoupdate) 섹션의 값과 다음 코드를 사용하여 1단계의 키를 사용합니다.

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

    `overwrite=True` 지정되므로 이 코드는 기존 등록을 덮어쓰고 새 키를 사용하도록 업데이트합니다.

## <a name="next-steps"></a>다음 단계

데이터 스토어 등록에 대한 자세한 [`Datastore`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) 내용은 클래스 참조를 참조하십시오.
