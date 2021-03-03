---
title: Azure에서 저장소 서비스에 대 한 id 기반 데이터 액세스
titleSuffix: Azure Machine Learning
description: Id 기반 데이터 액세스를 사용 하 여 Azure에서 저장소 서비스에 연결 하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
ms.reviewer: nibaccam
ms.date: 02/22/2021
ms.custom: how-to, contperf-fy21q1, devx-track-python, data4ml
ms.openlocfilehash: dbfb4ea729b8360c7065d75cb3efbaf42b82c0da
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101663142"
---
# <a name="connect-to-storage-with-identity-based-data-access-preview"></a>Id 기반 데이터 액세스를 사용 하 여 저장소에 연결 (미리 보기)

>[!IMPORTANT]
> 이 문서에 제공 된 기능은 미리 보기 상태 이며 언제 든 지 변경 될 수 있는 [실험적](/python/api/overview/azure/ml/?preserve-view=true&view=azure-ml-py#stable-vs-experimental) 미리 보기 기능으로 간주 되어야 합니다.

이 문서에서는 id 기반 데이터 액세스를 사용 하 여 Azure에서 저장소 서비스에 연결 하 고 [PYTHON SDK Azure Machine Learning](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py)를 통해 데이터 저장소를 Azure Machine Learning 하는 방법을 알아봅니다.  

일반적으로 데이터 저장소는 자격 증명 기반 데이터 액세스를 사용 하 여 저장소 서비스에 액세스할 수 있는 권한이 있는지 확인 합니다. 작업 영역과 연결 된 [Key Vault](https://azure.microsoft.com/services/key-vault/) 에서 구독 ID 및 토큰 권한 부여와 같은 연결 정보를 유지 합니다. Id 기반 데이터 액세스를 사용 하는 데이터 저장소를 만들 때 Azure 로그인 ([Azure Active Directory 토큰](../active-directory/fundamentals/active-directory-whatis.md))은 저장소 서비스에 액세스할 수 있는 권한이 있는지 확인 하는 데 사용 됩니다. 이 시나리오에서는 인증 자격 증명이 저장 되지 않으며 저장소 계정 정보만 데이터 저장소에 저장 됩니다. 

액세스 키 또는 서비스 사용자와 같은 자격 증명 기반 인증을 사용 하는 데이터 저장소를 만들려면 [Azure에서 저장소 서비스에 연결](how-to-access-data.md)을 참조 하세요.

## <a name="identity-based-data-access-in-azure-machine-learning"></a>Azure Machine Learning의 id 기반 데이터 액세스

Azure Machine Learning에서 id 기반 데이터 액세스를 적용 하는 두 가지 영역이 있습니다. 특히 기밀 데이터를 사용할 때 보다 세분화 된 데이터 액세스 관리가 필요한 경우 

1. 저장소 서비스에 액세스 합니다.
1. 개인 데이터를 사용 하 여 기계 학습 모델 학습

### <a name="accessing-storage-services"></a>Storage 서비스 액세스

Azure Machine Learning 데이터 저장소 또는 [Azure Machine Learning 데이터 집합](how-to-create-register-datasets.md)을 사용 하 여 id 기반 데이터 액세스를 통해 저장소 서비스에 연결할 수 있습니다. 

일반적으로 인증 자격 증명은 저장소 서비스에 액세스할 수 있는 권한이 있는지 확인 하는 데 사용 되는 데이터 저장소에 유지 됩니다. 이러한 자격 증명을 datastores에 등록 하면 작업 영역 *판독기* 역할을 가진 사용자는 이러한 자격 증명을 검색할 수 있습니다 .이 경우 일부 조직의 보안 문제가 발생할 수 있습니다. [작업 영역 *판독기* 역할에 대해 자세히 알아보세요](how-to-assign-roles.md#default-roles). 

Id 기반 데이터 액세스를 사용 하는 경우 데이터 저장소에 자격 증명을 유지 하는 대신 데이터 액세스 인증에 대 한 Azure Active Directory 토큰을 입력 하 라는 메시지가 표시 Azure Machine Learning. 저장소 수준에서 데이터 액세스를 관리 하 고 자격 증명을 기밀로 유지할 수 있습니다. 

같은 동작이 적용 됩니다.

* [저장소 url에서 직접 데이터 집합을 만듭니다](#use-data-in-storage). 
* 로컬 컴퓨터 또는 [계산 인스턴스의](concept-compute-instance.md)Jupyter 노트북을 통해 대화형으로 데이터를 사용 합니다.

> [!NOTE]
> 자격 증명 기반 인증을 사용 하 여 저장 된 자격 증명에는 구독 ID, SAS (공유 액세스 서명) 토큰, 저장소 액세스 키 및 서비스 주체 정보 (예: 클라이언트 ID 및 테 넌 트 ID)가 포함 됩니다.

### <a name="model-training-on-private-data"></a>개인 데이터에 대 한 모델 학습

특정 기계 학습 시나리오는 개인 데이터를 포함 하는 학습 모델을 포함 합니다. 이러한 경우 데이터 과학자는 기밀 입력 데이터를 노출 하지 않고 학습 워크플로를 실행 해야 합니다. 이 시나리오에서는 데이터 액세스 인증에 학습 계산의 관리 id가 사용 됩니다. 이러한 방식으로 저장소 관리자는 교육 계산에서 개별 데이터 과학자 아니라 교육 작업을 실행 하는 데 사용 하는 관리 id에 **저장소 Blob 데이터 판독기** 액세스 권한을 부여할 수 있습니다. [계산에서 관리 id를 설정](how-to-create-attach-compute-cluster.md#managed-identity)하는 방법에 대해 알아봅니다.


## <a name="prerequisites"></a>필수 구성 요소

- Azure 구독 Azure 구독이 없는 경우 시작하기 전에 체험 계정을 만듭니다. [Azure Machine Learning 평가판 또는 유료 버전](https://aka.ms/AMLFree)을 사용해 보세요.

- 지원 되는 저장소 형식을 사용 하는 Azure 저장소 계정. 미리 보기에서 지원 되는 저장소 유형은 다음과 같습니다. 
    - [Azure Blob Storage](../storage/blobs/storage-blobs-overview.md)
    - [Azure Data Lake Gen 1](../data-lake-store/index.yml)
    - [Azure Data Lake Gen 2](../storage/blobs/data-lake-storage-introduction.md)
    - [Azure SQL 데이터베이스](../azure-sql/database/sql-database-paas-overview.md)

- [Python 용 AZURE MACHINE LEARNING SDK](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py)입니다.

- Azure Machine Learning 작업 영역
  
  [Azure Machine Learning 작업 영역을 만들거나](how-to-manage-workspace.md) [Python SDK를 통해 기존](how-to-manage-workspace.md#connect-to-a-workspace)작업 영역을 사용 합니다. 

## <a name="storage-access-permissions"></a>저장소 액세스 권한

Azure에서 저장소 서비스에 안전 하 게 연결 하려면 해당 하는 데이터 저장소에 액세스할 수 있는 권한이 Azure Machine Learning 필요 합니다.

Id 기반 데이터 액세스는 다음 저장소 서비스에 대 한 연결만 지원 합니다.

* Azure Blob Storage
* Azure Data Lake 1 세대
* Azure Data Lake 2 세대
* Azure SQL 데이터베이스

이러한 저장소 서비스에 액세스 하려면 최소 **저장소 Blob 데이터 판독기** 액세스 권한이 있어야 합니다. [저장소 Blob 데이터 판독기](../role-based-access-control/built-in-roles.md#storage-blob-data-reader)에 대해 자세히 알아보세요. 저장소 계정 소유자만 [Azure Portal를 통해 액세스 수준을 변경할](../storage/common/storage-auth-aad-rbac-portal.md)수 있습니다.

원격 계산 대상에서 모델을 학습 하는 경우 저장소 서비스의 **저장소 Blob 데이터 판독기** 역할 이상으로 계산 id를 부여 해야 합니다. [계산에서 관리 id를 설정](how-to-create-attach-compute-cluster.md#managed-identity)하는 방법을 알아봅니다.

## <a name="work-with-virtual-networks"></a>가상 네트워크 작업

기본적으로 Azure Machine Learning는 방화벽 뒤에 있거나 가상 네트워크 내에 있는 저장소 계정과 통신할 수 없습니다.

특정 가상 네트워크 내 에서만 액세스를 허용 하도록 저장소 계정을 구성할 수 있습니다 .이 경우 데이터가 네트워크 외부에서 유출 되지 않도록 하기 위해 추가 구성이 필요 합니다. 이 동작은 자격 증명 기반 데이터 액세스에 대해 동일 [하며, 필요한 구성과 가상 네트워크 시나리오에 적용 하는 방법을](how-to-access-data.md#virtual-network)참조 하세요. 

## <a name="create-and-register-datastores"></a>데이터 저장소 만들기 및 등록

저장소 서비스를 Azure에 데이터 저장소로 등록 하는 경우 해당 데이터 저장소를 자동으로 만들어 특정 작업 영역에 등록 합니다. 다음 섹션을 검토 하세요. [저장소 액세스 권한](#storage-access-permissions) 필요한 사용 권한 유형에 대 한 지침을 확인 하 고 가상 [네트워크를 사용](#work-with-virtual-networks) 하 여 데이터 저장소에 연결 하는 방법에 대 한 자세한 내용을 확인 합니다.

다음 코드에서는 `sas_token` ,, `account_key` `subscription_id` 또는 서비스 사용자와 같은 인증 매개 변수가 없음을 확인 합니다 `client_id` . 이 생략은 Azure Machine Learning가 인증을 위해에 id 기반 데이터 액세스를 사용 하는 것을 나타냅니다. 일반적으로 데이터 저장소 생성은 노트북에서 대화형으로 발생 하거나 스튜디오를 통해 수행 되므로 Azure Active Directory 토큰이 데이터 액세스 인증에 사용 됩니다.

> [!NOTE]
> 데이터 저장소 이름은 소문자, 숫자 및 밑줄로만 구성 되어야 합니다. 

### <a name="azure-blob-container"></a>Azure Blob 컨테이너

Azure BLOB 컨테이너를 데이터 저장소로 등록하려면 [`register_azure_blob_container()`](/python/api/azureml-core/azureml.core.datastore%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=trueregister-azure-blob-container-workspace--datastore-name--container-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false--blob-cache-timeout-none--grant-workspace-access-false--subscription-id-none--resource-group-none-)를 사용합니다.

다음 코드에서는 데이터 저장소를 만들어 `credentialless_blob` 작업 영역에 등록 하 `ws` 고 변수에 할당 `blob_datastore` 합니다. 이 데이터 저장소는 `my_container_name` 저장소 계정의 blob 컨테이너에 액세스 `my-account-name` 합니다.

```Python
# create blob datastore without credentials
blob_datastore = Datastore.register_azure_blob_container(workspace=ws,
                                                      datastore_name='credentialless_blob',
                                                      container_name='my_container_name',
                                                      account_name='my_account_name')
```

### <a name="azure-data-lake-storage-generation-1"></a>Azure Data Lake Storage 1 세대

Azure Data Lake Storage 1 세대 (ADLS Gen 1) 데이터 저장소의 경우 [register_azure_data_lake ()](/python/api/azureml-core/azureml.core.datastore.datastore?preserve-view=true&view=azure-ml-py#&preserve-view=trueregister-azure-data-lake-workspace--datastore-name--store-name--tenant-id-none--client-id-none--client-secret-none--resource-url-none--authority-url-none--subscription-id-none--resource-group-none--overwrite-false--grant-workspace-access-false-) 를 사용 하 여 Azure DataLake Generation 1 저장소에 연결 하는 데이터 저장소를 등록 합니다.

다음 코드에서는 데이터 저장소를 만들어 `credentialless_adls1` 작업 영역에 등록 하 `workspace` 고 변수에 할당 `adls_dstore` 합니다. 이 데이터 저장소는 `adls_storage` Azure Data Lake Store 저장소 계정에 액세스 합니다.

```Python
# create adls gen1 without credentials
adls_dstore = Datastore.register_azure_data_lake(workspace = workspace,
                                                 datastore_name='credentialless_adls1',
                                                 store_name='adls_storage')

```

### <a name="azure-data-lake-storage-generation-2"></a>Azure Data Lake Storage Generation 2

2 세대 (ADLS Gen 2) 데이터 저장소 Azure Data Lake Storage의 경우 [register_azure_data_lake_gen2 ()](/python/api/azureml-core/azureml.core.datastore.datastore?preserve-view=true&view=azure-ml-py#&preserve-view=trueregister-azure-data-lake-gen2-workspace--datastore-name--filesystem--account-name--tenant-id--client-id--client-secret--resource-url-none--authority-url-none--protocol-none--endpoint-none--overwrite-false-) 를 사용 하 여 Azure DataLake Gen 2 저장소에 연결 되는 데이터 저장소를 등록 합니다.

다음 코드에서는 데이터 저장소를 만들어 `credentialless_adls2` 작업 영역에 등록 하 `ws` 고 변수에 할당 `adls2_dstore` 합니다. 이 데이터 저장소는 저장소 계정의 파일 시스템에 액세스 `tabular` `myadls2` 합니다.  

```python
# createn adls2 datastore without credentials
adls2_dstore = Datastore.register_azure_data_lake_gen2(workspace=ws, 
                                                       datastore_name='credentialless_adls2', 
                                                       filesystem='tabular', 
                                                       account_name='myadls2')
```

## <a name="use-data-in-storage"></a>저장소에서 데이터 사용

[Azure Machine Learning 데이터 집합](how-to-create-register-datasets.md) 은 Azure Machine Learning를 사용 하 여 저장소에서 데이터와 상호 작용 하는 데 권장 되는 방법입니다. 

데이터 집합은 학습 같은 기계 학습 작업에 대 한 데이터를 지연 계산 된 소비재 개체로 패키지 합니다. 또한 데이터 집합을 사용 하 여 azure storage 서비스 (예: Azure Blob storage 및 Azure 데이터 레이크)에서 계산 대상으로 모든 형식의 파일을 [다운로드 하거나 탑재할](how-to-train-with-datasets.md#mount-vs-download) 수 있습니다.


**Id 기반 데이터 액세스를 사용 하 여 데이터 집합을 만들려면** 다음과 같은 옵션을 사용할 수 있습니다. 이러한 유형의 데이터 집합 만들기에서는 데이터 액세스 인증을 위해 Azure Active Directory 토큰을 사용 합니다. 

*  Id 기반 데이터 액세스도 사용 하는 데이터 저장소의 참조 경로입니다. 
<br>다음 예에서는 이전에 `blob_datastore` id 기반 데이터 액세스를 사용 하 여를 만들었습니다.   

    ```python
    blob_dataset = Dataset.Tabular.from_delimited_files(blob_datastore,'test.csv') 
    ```

* 데이터 저장소 만들기를 건너뛰고 저장소 url에서 직접 데이터 집합을 만듭니다. 현재이 기능은 Azure Blob 및 Azure Data Lake Storage 세대 1 및 2만 지원 합니다.

    ```python
    blob_dset = Dataset.File.from_files('https://myblob.blob.core.windows.net/may/keras-mnist-fashion/')
    ```

**그러나 id 기반 데이터 액세스를 사용 하 여 만든 데이터 집합을 사용 하는 학습 작업을 제출** 하는 경우에는 Azure Active Directory 토큰이 아닌 데이터 액세스 인증에 대해 학습 계산의 관리 id가 사용 됩니다. 이 시나리오에서는 저장소 서비스에서 **저장소 Blob 데이터 판독기** 역할을 사용 하 여 계산의 관리 id를 부여 해야 합니다. [계산에서 관리 id를 설정](how-to-create-attach-compute-cluster.md#managed-identity)하는 방법을 알아봅니다. 

## <a name="next-steps"></a>다음 단계

* [Azure machine learning 데이터 집합을 만듭니다](how-to-create-register-datasets.md).
* [데이터 집합을 사용 하 여 학습](how-to-train-with-datasets.md)합니다.
* [키 기반 데이터 액세스를 사용 하 여 데이터 저장소를 만듭니다](how-to-access-data.md).
