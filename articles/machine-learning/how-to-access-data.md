---
title: Azure storage 서비스에 연결
titleSuffix: Azure Machine Learning
description: 을 사용 하 여 학습 하는 동안 데이터 저장소를 사용 하 여 Azure storage 서비스에 안전 하 게 연결 하는 방법을 알아봅니다 Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
ms.reviewer: nibaccam
ms.date: 03/24/2020
ms.custom: seodec18
ms.openlocfilehash: f5a7605a1fa68c3a600c77ded762722990d7a514
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82231206"
---
# <a name="connect-to-azure-storage-services"></a>Azure storage 서비스에 연결
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

이 문서에서는 Azure Machine Learning datastores를 통해 Azure storage 서비스에 연결 하는 방법을 알아봅니다. Datastores는 작업 영역과 연결 된 [Key Vault](https://azure.microsoft.com/services/key-vault/) 에서 구독 ID 및 토큰 권한 부여와 같은 연결 정보를 저장 하므로 스크립트에 하드 코딩 하지 않고도 저장소에 안전 하 게 액세스할 수 있습니다. 데이터 저장소가 Azure Machine Learning의 전체 데이터 액세스 워크플로에 적합 한 위치를 이해 하려면 안전 하 [게 데이터 액세스](concept-data.md#data-workflow) 문서를 참조 하세요.

[이러한 Azure storage 솔루션](#matrix)에서 데이터 저장소를 만들 수 있습니다. 지원 되지 않는 저장소 솔루션의 경우 machine learning 실험 중에 데이터 송신 비용을 절약 하려면 지원 되는 Azure storage 솔루션으로 [데이터를 이동](#move) 하는 것이 좋습니다. 

## <a name="prerequisites"></a>사전 요구 사항

필요한 사항:
- Azure 구독 Azure 구독이 없는 경우 시작하기 전에 체험 계정을 만듭니다. [Azure Machine Learning의 무료 또는 유료 버전](https://aka.ms/AMLFree)을 사용해 보세요.

- Azure [blob 컨테이너](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) 또는 [azure 파일 공유](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)를 사용 하는 azure storage 계정

- [Python 용 AZURE MACHINE LEARNING SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)또는 [Azure Machine Learning studio](https://ml.azure.com/)에 대 한 액세스입니다.

- Azure Machine Learning 작업 영역
  
  [Azure Machine Learning 작업 영역을 만들거나](how-to-manage-workspace.md) Python SDK를 통해 기존 작업 영역을 사용 합니다. `Workspace` 및 `Datastore` 클래스를 가져오고 함수 `config.json` `from_config()`를 사용 하 여 파일에서 구독 정보를 로드 합니다. 이는 기본적으로 현재 디렉터리에서 JSON 파일을 검색 하지만를 사용 하 여 `from_config(path="your/file/path")`파일을 가리키도록 경로 매개 변수를 지정할 수도 있습니다.

   ```Python
   import azureml.core
   from azureml.core import Workspace, Datastore
        
   ws = Workspace.from_config()
   ```
<a name="matrix"></a>

## <a name="supported-data-storage-service-types"></a>지원 되는 데이터 저장소 서비스 유형

Datastores는 현재 다음 행렬에 나열 된 저장소 서비스에 대 한 연결 정보를 저장 하는 기능을 지원 합니다.

| 저장소&nbsp;유형 | 인증&nbsp;유형 | [Azure&nbsp;Machine&nbsp;Learning studio](https://ml.azure.com/) | [Azure&nbsp;Machine&nbsp;Learning&nbsp; Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) |  [Azure&nbsp;MACHINE&nbsp;Learning CLI](reference-azure-machine-learning-cli.md) | [Azure&nbsp;Machine&nbsp;Learning&nbsp; Rest API](https://docs.microsoft.com/rest/api/azureml/)
---|---|---|---|---|---
[Azure&nbsp;Blob&nbsp;저장소](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview)| 계정 키 <br> SAS 토큰 | ✓ | ✓ | ✓ |✓
[Azure&nbsp;파일&nbsp;공유](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)| 계정 키 <br> SAS 토큰 | ✓ | ✓ | ✓ |✓
[Azure&nbsp;Data Lake&nbsp;Storage Gen&nbsp;1](https://docs.microsoft.com/azure/data-lake-store/)| 서비스 주체| ✓ | ✓ | ✓ |✓
[Azure&nbsp;Data Lake&nbsp;Storage Gen&nbsp;2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction)| 서비스 주체| ✓ | ✓ | ✓ |✓
[Azure&nbsp;SQL&nbsp;데이터베이스](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview)| SQL 인증 <br>서비스 주체| ✓ | ✓ | ✓ |✓
[Azure&nbsp;PostgreSQL](https://docs.microsoft.com/azure/postgresql/overview) | SQL 인증| ✓ | ✓ | ✓ |✓
[MySQL&nbsp;용&nbsp;&nbsp;Azure 데이터베이스](https://docs.microsoft.com/azure/mysql/overview) | SQL 인증|  | ✓* | ✓* |✓*
[Databricks&nbsp;파일&nbsp;시스템](https://docs.microsoft.com/azure/databricks/data/databricks-file-system)| 인증 없음 | | ✓** | ✓ ** |✓** 

* MySQL은 파이프라인 [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep?view=azure-ml-py)지원 됩니다. <br>
* * Databricks은 파이프라인 [DatabricksStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricks_step.databricksstep?view=azure-ml-py) 지원 됩니다.

### <a name="storage-guidance"></a>스토리지 지침

[Azure Blob 컨테이너](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction)에 대 한 데이터 저장소를 만드는 것이 좋습니다. Blob에는 standard 및 premium storage를 모두 사용할 수 있습니다. Premium storage는 비용이 더 많이 들지만 더 빠른 처리량 속도는 특히 큰 데이터 집합에 대해 학습 하는 경우 학습 실행 속도를 향상 시킬 수 있습니다. 저장소 계정 비용에 대 한 자세한 내용은 [Azure 가격 계산기](https://azure.microsoft.com/pricing/calculator/?service=machine-learning-service)를 참조 하세요.

[Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction?toc=/azure/storage/blobs/toc.json) 는 Azure Blob Storage를 기반으로 구축 되 고 엔터프라이즈 빅 데이터 분석을 위해 설계 되었습니다. Data Lake Storage Gen2의 기본적인 부분은 [계층 구조 네임스페이스](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-namespace)를 Blob 스토리지에 추가하는 것입니다. 계층 구조 네임스페이스는 효율적인 데이터 액세스를 위해 개체/파일을 디렉터리 계층 구조로 구성합니다.

작업 영역을 만들면 Azure blob 컨테이너 및 Azure 파일 공유가 작업 영역에 자동으로 등록 됩니다. 각각 및 `workspacefilestore`라는 `workspaceblobstore` 이름이 지정 됩니다. `workspaceblobstore`는 작업 영역 아티팩트와 machine learning 실험 로그를 저장 하는 데 사용 됩니다. `workspacefilestore`는 [계산 인스턴스](https://docs.microsoft.com/azure/machine-learning/concept-compute-instance#accessing-files)를 통해 권한이 부여 된 노트북 및 R 스크립트를 저장 하는 데 사용 됩니다. 컨테이너 `workspaceblobstore` 는 기본 데이터 저장소로 설정 됩니다.

> [!IMPORTANT]
> Azure Machine Learning designer (미리 보기)는 디자이너 홈페이지에서 샘플을 열 때 **azureml_globaldatasets** 라는 데이터 저장소를 자동으로 만듭니다. 이 데이터 저장소는 샘플 데이터 집합만 포함 합니다. 기밀 데이터 액세스에는이 데이터 저장소를 사용 **하지 마세요** .
> ![디자이너 샘플 데이터 집합의 자동 생성 데이터 저장소](media/how-to-access-data/datastore-designer-sample.png)

<a name="access"></a>

## <a name="create-and-register-datastores"></a>데이터 저장소 만들기 및 등록

Azure storage 솔루션을 데이터 저장소로 등록 하는 경우 해당 데이터 저장소를 자동으로 만들어 특정 작업 영역에 등록 합니다. [PYTHON SDK](#python-sdk) 또는 [Azure Machine Learning studio](#azure-machine-learning-studio)를 사용 하 여 데이터 저장소를 만들고 작업 영역에 등록할 수 있습니다.

>[!IMPORTANT]
> 초기 데이터 저장소 만들기 및 등록 프로세스의 일부로 기본 저장소 서비스가 존재 하 고 사용자가 제공한 보안 주체 (사용자 이름, 서비스 주체 또는 SAS 토큰)가 해당 저장소에 액세스할 수 있는지 확인 Azure Machine Learning 합니다. 그러나 Gen 1 및 2 데이터 저장소 Azure Data Lake Storage의 경우 또는 [`from_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py) [`from_delimited_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-parquet-files-path--validate-true--include-path-false--set-column-types-none--partition-format-none-) 와 같은 데이터 액세스 메서드가 호출 될 때이 유효성 검사는 나중에 수행 됩니다. 
<br><br>
데이터 저장소를 만든 후에는 데이터 저장소 개체가 검색 **될 때마다 기본** 저장소 컨테이너에 액세스 해야 하는 메서드에 대해서만이 유효성 검사가 수행 됩니다. 예를 들어 데이터 저장소에서 파일을 다운로드 하려는 경우 유효성 검사가 수행 됩니다. 그러나 기본 데이터 저장소를 변경 하려는 경우에는 유효성 검사가 수행 되지 않습니다.

### <a name="python-sdk"></a>Python SDK

모든 register 메서드는 [`Datastore`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) 클래스에 있으며 형식은 `register_azure_*`입니다.
> [!IMPORTANT]
> 저장소 계정이 가상 네트워크에 있는 경우 **SDK를 통한** 데이터 저장소 만들기만 지원 됩니다.

`register_azure_*()` [Azure Portal](https://portal.azure.com)에서 메서드를 채우는 데 필요한 정보를 찾을 수 있습니다.

* 인증을 위해 계정 키 또는 SAS 토큰을 사용 하려는 경우 왼쪽 창에서 **저장소 계정** 을 선택 하 고 등록 하려는 저장소 계정을 선택 합니다. 
  * **개요** 페이지에서 계정 이름, 컨테이너 및 파일 공유 이름과 같은 정보를 제공 합니다. 
      1. 계정 키의 경우 **설정** 창에서 **액세스 키** 로 이동 합니다. 
      1. SAS 토큰의 경우 **설정** 창에서 **공유 액세스 서명** 으로 이동 합니다.

* 인증을 위해 서비스 주체를 사용 하려는 경우 **앱 등록** 으로 이동 하 여 사용 하려는 앱을 선택 합니다. 
    * 해당 **개요** 페이지에는 테 넌 트 id 및 클라이언트 ID와 같은 필수 정보가 포함 됩니다.

다음 예제에서는 Azure blob 컨테이너, Azure 파일 공유 및 Azure Data Lake Storage 2 세대를 데이터 저장소로 등록 하는 방법을 보여 줍니다. 이러한 예제에 제공 된 매개 변수는 데이터 저장소를 만들고 등록 하는 **데 필요한 매개 변수** 입니다. 

다른 저장소 서비스에 대 한 데이터 저장소를 만들고 이러한 메서드에 대 한 선택적 매개 변수를 보려면 [해당 `register_azure_*` 메서드에 대 한 참조 설명서](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore.datastore?view=azure-ml-py#methods)를 참조 하세요.

#### <a name="blob-container"></a>Blob 컨테이너

Azure blob 컨테이너를 데이터 저장소로 등록 하려면를 사용 [`register_azure_blob-container()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-blob-container-workspace--datastore-name--container-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false--blob-cache-timeout-none--grant-workspace-access-false--subscription-id-none--resource-group-none-)합니다.

다음 코드는 `blob_datastore_name` `ws` 작업 영역에 데이터 저장소를 만들고 등록 합니다. 이 데이터 저장소는 `my-container-name` 제공 된 계정 액세스 `my-account-name` 키를 사용 하 여 저장소 계정의 blob 컨테이너에 액세스 합니다.

```Python
blob_datastore_name='azblobsdk' # Name of the datastore to workspace
container_name=os.getenv("BLOB_CONTAINER", "<my-container-name>") # Name of Azure blob container
account_name=os.getenv("BLOB_ACCOUNTNAME", "<my-account-name>") # Storage account name
account_key=os.getenv("BLOB_ACCOUNT_KEY", "<my-account-key>") # Storage account access key

blob_datastore = Datastore.register_azure_blob_container(workspace=ws, 
                                                         datastore_name=blob_datastore_name, 
                                                         container_name=container_name, 
                                                         account_name=account_name,
                                                         account_key=account_key)
```
Blob 컨테이너가 가상 네트워크에 있는 경우 `skip_validation=True` [`register_azure_blob-container()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-blob-container-workspace--datastore-name--container-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false--blob-cache-timeout-none--grant-workspace-access-false--subscription-id-none--resource-group-none-) 메서드에 매개 변수를 포함 합니다. 

#### <a name="file-share"></a>파일 공유

Azure 파일 공유를 데이터 저장소로 등록 하려면를 사용 [`register_azure_file_share()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-)합니다. 

다음 코드는 `file_datastore_name` `ws` 작업 영역에 데이터 저장소를 만들고 등록 합니다. 이 데이터 저장소는 `my-fileshare-name` 제공 된 계정 액세스 `my-account-name` 키를 사용 하 여 저장소 계정의 파일 공유에 액세스 합니다.

```Python
file_datastore_name='azfilesharesdk' # Name of the datastore to workspace
file_share_name=os.getenv("FILE_SHARE_CONTAINER", "<my-fileshare-name>") # Name of Azure file share container
account_name=os.getenv("FILE_SHARE_ACCOUNTNAME", "<my-account-name>") # Storage account name
account_key=os.getenv("FILE_SHARE_ACCOUNT_KEY", "<my-account-key>") # Storage account access key

file_datastore = Datastore.register_azure_file_share(workspace=ws,
                                                     datastore_name=file_datastore_name, 
                                                     file_share_name=file_share_name, 
                                                     account_name=account_name,
                                                     account_key=account_key)
```
파일 공유가 가상 네트워크에 있는 경우 `skip_validation=True` [`register_azure_file_share()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-) 메서드에 매개 변수를 포함 합니다. 

#### <a name="azure-data-lake-storage-generation-2"></a>Azure Data Lake Storage 2 세대

ADLS Gen 2 (Gen 2) 데이터 저장소 Azure Data Lake Storage의 경우 [register_azure_data_lake_gen2 ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore.datastore?view=azure-ml-py#register-azure-data-lake-gen2-workspace--datastore-name--filesystem--account-name--tenant-id--client-id--client-secret--resource-url-none--authority-url-none--protocol-none--endpoint-none--overwrite-false-) 를 사용 하 여 [서비스 사용자 권한](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)으로 Azure DataLake Gen 2 저장소에 연결 된 자격 증명 데이터 저장소를 등록 합니다. 서비스 주체를 활용 하려면 [응용 프로그램을 등록](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals) 하 고 *저장소 Blob 데이터 소유자* 액세스 권한이 있는 서비스 주체를 부여 해야 합니다. [ADLS Gen 2에 대 한 액세스 제어 설정](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)에 대 한 자세한 정보. 

서비스 주체를 활용 하기 위해 [응용 프로그램을 등록](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals) 하 고 적절 한 데이터 액세스 권한이 있는 서비스 주체를 부여 해야 합니다. [ADLS Gen 2에 대 한 액세스 제어 설정](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)에 대 한 자세한 정보. 

다음 코드는 `adlsgen2_datastore_name` `ws` 작업 영역에 데이터 저장소를 만들고 등록 합니다. 이 데이터 저장소는 제공 된 `test` 서비스 주체 `account_name` 자격 증명을 사용 하 여 저장소 계정의 파일 시스템에 액세스 합니다.

```python 
adlsgen2_datastore_name = 'adlsgen2datastore'

subscription_id=os.getenv("ADL_SUBSCRIPTION", "<my_subscription_id>") # subscription id of ADLS account
resource_group=os.getenv("ADL_RESOURCE_GROUP", "<my_resource_group>") # resource group of ADLS account

account_name=os.getenv("ADLSGEN2_ACCOUNTNAME", "<my_account_name>") # ADLS Gen2 account name
tenant_id=os.getenv("ADLSGEN2_TENANT", "<my_tenant_id>") # tenant id of service principal
client_id=os.getenv("ADLSGEN2_CLIENTID", "<my_client_id>") # client id of service principal
client_secret=os.getenv("ADLSGEN2_CLIENT_SECRET", "<my_client_secret>") # the secret of service principal

adlsgen2_datastore = Datastore.register_azure_data_lake_gen2(workspace=ws,
                                                             datastore_name=adlsgen2_datastore_name,
                                                             account_name=account_name, # ADLS Gen2 account name
                                                             filesystem='test', # ADLS Gen2 filesystem
                                                             tenant_id=tenant_id, # tenant id of service principal
                                                             client_id=client_id, # client id of service principal
                                                             client_secret=client_secret) # the secret of service principal
```

### <a name="azure-machine-learning-studio"></a>Azure Machine Learning Studio 

Azure Machine Learning studio에서 몇 가지 단계를 수행 하 여 새 데이터 저장소를 만듭니다.

> [!IMPORTANT]
> 저장소 계정이 가상 네트워크에 있는 경우 [SDK를 통한](#python-sdk) 데이터 저장소 만들기만 지원 됩니다. 

1. [Azure Machine Learning Studio](https://ml.azure.com/)에 로그인합니다.
1. **관리**아래의 왼쪽 창에서 **datastores** 를 선택 합니다.
1. **+ 새 데이터 저장소**를 선택합니다.
1. 새 데이터 저장소에 대 한 양식을 작성 합니다. 양식은 Azure storage 유형 및 인증 유형에 대 한 선택 항목에 따라 지능적으로 업데이트 됩니다.
  
[Azure Portal](https://portal.azure.com)에서 폼을 채우는 데 필요한 정보를 찾을 수 있습니다. 왼쪽 창에서 **저장소 계정** 을 선택 하 고 등록 하려는 저장소 계정을 선택 합니다. **개요** 페이지에서 계정 이름, 컨테이너 및 파일 공유 이름과 같은 정보를 제공 합니다. 

* 계정 키 또는 SAS 토큰과 같은 인증 항목의 경우 **설정** 창에서 **액세스 키** 로 이동 합니다. 

* 테 넌 트 ID 및 클라이언트 ID와 같은 서비스 사용자 항목의 경우 **앱 등록** 으로 이동 하 여 사용할 앱을 선택 합니다. 해당 **개요** 페이지에는 다음 항목이 포함 됩니다. 

다음 예제에서는 Azure blob 데이터 저장소를 만들 때 양식이 표시 되는 모양을 보여 줍니다. 
    
![새 데이터 저장소의 폼](media/how-to-access-data/new-datastore-form.png)


<a name="get"></a>

## <a name="get-datastores-from-your-workspace"></a>작업 영역에서 데이터 저장소 가져오기

현재 작업 영역에 등록 된 특정 데이터 저장소를 가져오려면 [`get()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#get-workspace--datastore-name-) `Datastore` 클래스에서 정적 메서드를 사용 합니다.

```Python
# Get a named datastore from the current workspace
datastore = Datastore.get(ws, datastore_name='your datastore name')
```
지정 된 작업 영역에 등록 된 데이터 저장소 목록을 가져오려면 작업 영역 개체에 대 한 [`datastores`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace%28class%29?view=azure-ml-py#datastores) 속성을 사용 하면 됩니다.

```Python
# List all datastores registered in the current workspace
datastores = ws.datastores
for name, datastore in datastores.items():
    print(name, datastore.datastore_type)
```

작업 영역의 기본 데이터 저장소를 가져오려면 다음 줄을 사용 합니다.

```Python
datastore = ws.get_default_datastore()
```
다음 코드를 사용 하 여 기본 데이터 저장소를 변경할 수도 있습니다. 이 기능은 SDK를 통해서만 지원 됩니다. 

```Python
 ws.set_default_datastore(new_default_datastore)
```
<a name="up-and-down"></a>
## <a name="upload-and-download-data"></a>데이터 업로드 및 다운로드

다음 [`upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#upload-src-dir--target-path-none--overwrite-false--show-progress-true-) 예제 [`download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-) 에서 설명 하는 및 메서드는 [Azureblobdatastore 저장소](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py) 및 [azureblobdatastore 저장소](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py) 클래스에 대해 동일 하 게 작동 합니다.

> [!NOTE]
> AzureDataLakeGen2 데이터 저장소에 업로드가 지원 되지 않습니다.

### <a name="upload"></a>업로드

Python SDK를 사용 하 여 데이터 저장소에 디렉터리 또는 개별 파일을 업로드 합니다.

```Python
datastore.upload(src_dir='your source directory',
                 target_path='your target path',
                 overwrite=True,
                 show_progress=True)
```

매개 `target_path` 변수는 업로드할 파일 공유 (또는 blob 컨테이너)의 위치를 지정 합니다. 기본적으로로 `None`설정 되므로 데이터가 루트에 업로드 됩니다. 이면 `overwrite=True`의 기존 데이터 `target_path` 를 덮어씁니다.

메서드를 `upload_files()` 통해 데이터 저장소에 개별 파일 목록을 업로드할 수도 있습니다.

### <a name="download"></a>다운로드

데이터 저장소에서 로컬 파일 시스템으로 데이터를 다운로드 합니다.

```Python
datastore.download(target_path='your target path',
                   prefix='your prefix',
                   show_progress=True)
```

`target_path` 매개 변수는 데이터를 다운로드할 로컬 디렉터리의 위치입니다. 다운로드할 파일 공유 또는 blob 컨테이너의 폴더 경로를 지정하려면 `prefix`에 해당 경로를 입력합니다. `prefix` 가 `None`이면 파일 공유 (또는 blob 컨테이너)의 모든 내용이 다운로드 됩니다.

<a name="train"></a>

## <a name="access-your-data-during-training"></a>학습 중 데이터 액세스

데이터 저장소의 데이터를 조작 하거나 학습 등의 기계 학습 작업을 위한 사용 가능한 개체에 데이터를 패키지 하려면 [Azure Machine Learning 데이터 집합을 만듭니다](how-to-create-register-datasets.md). 데이터 집합은 pandas 또는 Spark 데이터 프레임에 테이블 형식 데이터를 로드 하는 함수를 제공 합니다. 또한 데이터 집합은 Azure Blob storage, Azure Files, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure SQL Database 및 Azure Database for PostgreSQL에서 모든 형식의 파일을 다운로드 하거나 탑재 하는 기능을 제공 합니다. [데이터 집합을 사용 하 여 학습 하는 방법에 대해 자세히 알아보세요](how-to-train-with-datasets.md).

### <a name="accessing-source-code-during-training"></a>학습 중 소스 코드 액세스

Azure Blob storage는 Azure 파일 공유 보다 더 높은 처리량 속도를 가지 며 동시에 시작 된 많은 수의 작업으로 확장 됩니다. 따라서 소스 코드 파일을 전송 하는 데 Blob storage를 사용 하도록 실행을 구성 하는 것이 좋습니다.

다음 코드 예제에서는 실행 구성에서 소스 코드 전송에 사용할 blob 데이터 저장소를 지정 합니다.

```python 
# workspaceblobstore is the default blob storage
run_config.source_directory_data_store = "workspaceblobstore" 
```

## <a name="access-data-during-scoring"></a>점수 매기기 중 데이터 액세스

Azure Machine Learning에서는 모델을 사용 하 여 점수를 매기는 여러 가지 방법을 제공 합니다. 이러한 메서드 중 일부는 datastores에 대 한 액세스를 제공 하지 않습니다. 다음 표를 사용 하 여 점수 매기기 중에 데이터 저장소에 액세스할 수 있는 방법을 알아봅니다.

| 방법 | 데이터 저장소 액세스 | 설명 |
| ----- | :-----: | ----- |
| [일괄 처리 예측](how-to-use-parallel-run-step.md) | ✔ | 많은 양의 데이터에 대해 비동기적으로 예측합니다. |
| [웹 서비스](how-to-deploy-and-where.md) | &nbsp; | 모델을 웹 서비스로 배포 합니다. |
| [Azure IoT Edge 모듈](how-to-deploy-and-where.md) | &nbsp; | IoT Edge 장치에 모델을 배포 합니다. |

SDK가 datastores에 대 한 액세스를 제공 하지 않는 경우에는 관련 Azure SDK를 사용 하 여 데이터에 액세스 하 여 사용자 지정 코드를 만들 수 있습니다. 예를 들어 [Python 용 AZURE STORAGE SDK](https://github.com/Azure/azure-storage-python) 는 blob 또는 파일에 저장 된 데이터에 액세스 하는 데 사용할 수 있는 클라이언트 라이브러리입니다.

<a name="move"></a>

## <a name="move-data-to-supported-azure-storage-solutions"></a>지원 되는 Azure storage 솔루션으로 데이터 이동

Azure Machine Learning는 Azure Blob storage, Azure Files, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure SQL Database 및 Azure Database for PostgreSQL의 데이터에 액세스 하는 것을 지원 합니다. 지원 되지 않는 저장소를 사용 하는 경우 [Azure Data Factory 및 이러한 단계](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-copy-data-tool)를 사용 하 여 지원 되는 Azure storage 솔루션으로 데이터를 이동 하는 것이 좋습니다. 지원 되는 저장소로 데이터를 이동 하면 기계 학습 실험 중에 데이터 송신 비용을 절감할 수 있습니다. 

Azure Data Factory는 80 개 이상의 미리 작성 된 커넥터를 추가 비용 없이 효율적이 고 복원 력 있는 데이터 전송을 제공 합니다. 이러한 커넥터에는 Azure 데이터 서비스, 온-프레미스 데이터 원본, Amazon S3 및 Redshift 및 Google 이상 쿼리가 포함 됩니다.

## <a name="next-steps"></a>다음 단계

* [Azure machine learning 데이터 집합 만들기](how-to-create-register-datasets.md)
* [모델 학습](how-to-train-ml-models.md)
* [모델 배포](how-to-deploy-and-where.md)
