---
title: Azure 저장소 서비스에 연결
titleSuffix: Azure Machine Learning
description: Azure 기계 학습을 사용하여 교육하는 동안 데이터 스토어를 사용하여 Azure 저장소 서비스에 안전하게 연결하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
ms.reviewer: nibaccam
ms.date: 03/24/2020
ms.custom: seodec18
ms.openlocfilehash: c5972b602d92b2e08fd70850dd1af5c1236e2b1d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80234466"
---
# <a name="connect-to-azure-storage-services"></a>Azure 저장소 서비스에 연결
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

이 문서에서는 Azure 기계 학습 데이터 스토어를 통해 Azure 저장소 서비스에 연결하는 방법을 알아봅니다. Datastores는 작업 영역과 연결된 [Key Vault에](https://azure.microsoft.com/services/key-vault/) 구독 ID 및 토큰 권한 부여와 같은 연결 정보를 저장하므로 스크립트에서 하드 코딩하지 않고도 저장소에 안전하게 액세스할 수 있습니다.

이러한 Azure 저장소 [솔루션에서](#matrix)데이터 저장소를 만들 수 있습니다. 지원되지 않는 저장소 솔루션과 기계 학습 실험 중에 데이터 송신 비용을 절약하려면 데이터를 지원되는 Azure 저장소 솔루션으로 [이동하는](#move) 것이 좋습니다. 

## <a name="prerequisites"></a>사전 요구 사항

필요한 사항:
- Azure 구독 Azure 구독이 없는 경우 시작하기 전에 체험 계정을 만듭니다. Azure [기계 학습의 무료 또는 유료 버전을](https://aka.ms/AMLFree)사용해 보십시오.

- [Azure Blob 컨테이너](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) 또는 Azure [파일 공유가](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)있는 Azure 저장소 계정입니다.

- [파이썬용 Azure 기계 학습 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)또는 [Azure 기계 학습 스튜디오에](https://ml.azure.com/)대한 액세스.

- Azure Machine Learning 작업 영역
  
  [Azure 기계 학습 작업 영역을 만들거나](how-to-manage-workspace.md) Python SDK를 통해 기존 작업 영역을 사용합니다. 및 `Workspace` `Datastore` 클래스를 가져오고 함수를 `config.json` `from_config()`사용하여 파일에서 구독 정보를 로드합니다. 이렇게 하면 기본적으로 현재 디렉터리에서 JSON 파일을 찾지만 을 사용하여 `from_config(path="your/file/path")`파일을 가리키는 경로 매개 변수를 지정할 수도 있습니다.

   ```Python
   import azureml.core
   from azureml.core import Workspace, Datastore
        
   ws = Workspace.from_config()
   ```
<a name="matrix"></a>

## <a name="supported-data-storage-service-types"></a>지원되는 데이터 저장소 서비스 유형

데이터 스토어는 현재 다음 행렬에 나열된 저장소 서비스에 연결 정보를 저장하는 것을 지원합니다. 현재 Azure 데이터 웨어하우스는 지원되지 않습니다. 

| 스토리지&nbsp;유형 | 인증&nbsp;유형 | [Azure&nbsp;&nbsp;기계 학습 스튜디오](https://ml.azure.com/) | [Azure&nbsp;&nbsp;기계&nbsp; 학습 파이썬 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) |  [Azure&nbsp;&nbsp;기계 학습 CLI](reference-azure-machine-learning-cli.md) | [Azure&nbsp;&nbsp;기계&nbsp; 학습 나머지 API](https://docs.microsoft.com/rest/api/azureml/)
---|---|---|---|---|---
[Azure&nbsp;Blob&nbsp;저장소](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview)| 계정 키 <br> SAS 토큰 | ✓ | ✓ | ✓ |✓
[Azure&nbsp;&nbsp;파일 공유](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)| 계정 키 <br> SAS 토큰 | ✓ | ✓ | ✓ |✓
[Azure&nbsp;데이터&nbsp;레이크&nbsp;스토리지 1세대](https://docs.microsoft.com/azure/data-lake-store/)| 서비스 주체| ✓ | ✓ | ✓ |✓
[Azure&nbsp;데이터&nbsp;레이크&nbsp;스토리지 2세대](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction)| 서비스 주체| ✓ | ✓ | ✓ |✓
[Azure&nbsp;&nbsp;SQL 데이터베이스](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview)| SQL 인증 <br>서비스 주체| ✓ | ✓ | ✓ |✓
[저어 포스트그레SQL&nbsp;](https://docs.microsoft.com/azure/postgresql/overview) | SQL 인증| ✓ | ✓ | ✓ |✓
[&nbsp;MySQL용&nbsp;Azure 데이터베이스&nbsp;](https://docs.microsoft.com/azure/mysql/overview) | SQL 인증|  | ✓* | ✓* |✓*
[데이터 브릭스&nbsp;&nbsp;파일 시스템](https://docs.microsoft.com/azure/databricks/data/databricks-file-system)| 인증 없음 | | ✓** | ✓ ** |✓** 

*MySQL은 파이프라인 [데이터 전송 단계에](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep?view=azure-ml-py)대해서만 지원됩니다. <br>
** 데이터 브릭은 파이프 라인 [데이터 브릭스스스스에 대해서만 지원됩니다.](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricks_step.databricksstep?view=azure-ml-py)

### <a name="storage-guidance"></a>스토리지 지침

[Azure Blob 컨테이너에](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction)대한 데이터 스토어를 만드는 것이 좋습니다. Blob에는 표준 스토리지와 프리미엄 스토리지를 모두 사용할 수 있습니다. 프리미엄 스토리지는 비용이 더 많이 들지만 처리 속도가 빨라지면 특히 대규모 데이터 집합을 상대로 학습하는 경우 교육 실행 속도가 향상될 수 있습니다. 저장소 계정 비용에 대한 자세한 내용은 [Azure 가격 계산기를](https://azure.microsoft.com/pricing/calculator/?service=machine-learning-service)참조하십시오.

[Azure Data Lake Storage Gen2는](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction?toc=/azure/storage/blobs/toc.json) Azure Blob 스토리지 위에 구축되었으며 엔터프라이즈 빅 데이터 분석을 위해 설계되었습니다. Data Lake Storage Gen2의 기본적인 부분은 [계층 구조 네임스페이스](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-namespace)를 Blob 스토리지에 추가하는 것입니다. 계층 구조 네임스페이스는 효율적인 데이터 액세스를 위해 개체/파일을 디렉터리 계층 구조로 구성합니다.

작업 영역을 만들면 Azure Blob 컨테이너와 Azure 파일 공유가 작업 영역에 자동으로 등록됩니다. 각각 명명되고 `workspaceblobstore` `workspacefilestore`, `workspaceblobstore`는 작업 영역 아티팩트와 기계 학습 실험 로그를 저장하는 데 사용됩니다. `workspacefilestore`은 [계산 인스턴스를](https://docs.microsoft.com/azure/machine-learning/concept-compute-instance#accessing-files)통해 승인된 전자 필기장 및 R 스크립트를 저장하는 데 사용됩니다. 컨테이너가 `workspaceblobstore` 기본 데이터 저장소로 설정됩니다.

> [!IMPORTANT]
> Azure 기계 학습 디자이너(미리 보기)는 디자이너 홈페이지에서 샘플을 열 때 자동으로 **azureml_globaldatasets** 라는 데이터스토어를 만듭니다. 이 데이터 스토어에는 샘플 데이터 집합만 포함됩니다. 기밀 데이터 액세스를 위해 이 데이터 스토어를 사용하지 **마십시오!**
> ![디자이너 샘플 데이터 집합을 위한 자동 생성된 데이터스토어](media/how-to-access-data/datastore-designer-sample.png)

<a name="access"></a>

## <a name="create-and-register-datastores"></a>데이터 스토어 생성 및 등록

Azure 저장소 솔루션을 데이터 스토어로 등록하면 해당 데이터 스토어를 자동으로 만들고 특정 작업 영역에 등록합니다. Python SDK 또는 Azure 기계 학습 스튜디오를 사용하여 데이터 저장소를 만들고 작업 영역에 등록할 수 있습니다.

>[!IMPORTANT]
> 초기 데이터스토어 생성 및 등록 프로세스의 일부로 Azure Machine Learning은 기본 저장소 서비스가 존재하고 사용자가 제공한 보안 주체(사용자 이름, 서비스 주체 또는 SAS 토큰)가 해당 저장소에 액세스할 수 있는지 확인합니다. 그러나 Azure Data Lake Storage Gen 1 및 2 데이터 스토어의 경우 [`from_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py) [`from_delimited_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-parquet-files-path--validate-true--include-path-false--set-column-types-none--partition-format-none-) 이 유효성 검사는 나중에 데이터 액세스 메서드와 유사하거나 호출될 때 발생합니다. 
<br><br>
데이터스토어를 만든 후에는 데이터스토어 개체가 검색될 때마다가 **아니라** 기본 저장소 컨테이너에 액세스해야 하는 메서드에 대해서만 이 유효성 검사가 수행됩니다. 예를 들어 데이터스토어에서 파일을 다운로드하려는 경우 유효성 검사가 수행됩니다. 그러나 기본 데이터 스토어를 변경하려는 경우 유효성 검사가 발생하지 않습니다.

### <a name="python-sdk"></a>Python SDK

모든 레지스터 메서드는 [`Datastore`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) 클래스에 있으며 `register_azure_*`양식이 있습니다.

Azure `register()` [포털](https://portal.azure.com)에서 메서드를 채우는 데 필요한 정보를 찾을 수 있습니다.
왼쪽 창에서 **저장소 계정을** 선택하고 등록할 저장소 계정을 선택합니다. **개요** 페이지에서는 계정 이름, 컨테이너 및 파일 공유 이름과 같은 정보를 제공합니다. 

* 계정 키 또는 SAS 토큰과 같은 인증 항목의 경우 **설정** 창에서 **계정 키로** 이동합니다. 

* 테넌트 ID 및 클라이언트 ID와 같은 서비스 주체 항목의 경우 **앱 등록으로** 이동하여 사용할 앱을 선택합니다. 해당 **개요** 페이지에는 이러한 항목이 포함됩니다.

> [!IMPORTANT]
> 저장소 계정이 가상 네트워크에 있는 경우 **SDK를 통해** Blob, 파일 공유, ADLS Gen 1 및 ADLS Gen 2 데이터스토어만 생성이 지원됩니다. 저장소 계정에 작업 영역 액세스 권한을 부여하려면 `grant_workspace_access` `True`매개 변수를 로 설정합니다.

다음 예제에서는 Azure Blob 컨테이너, Azure 파일 공유 및 Azure Data Lake Storage Generation 2를 데이터 스토어로 등록하는 방법을 보여 주며 있습니다. 다른 저장소 서비스의 경우 [해당 `register_azure_*` 방법에 대한 참조 문서를](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore.datastore?view=azure-ml-py#methods)참조하십시오.

#### <a name="blob-container"></a>Blob 컨테이너

Azure Blob 컨테이너를 데이터스토어로 등록하려면 을 사용합니다. [`register_azure_blob-container()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-blob-container-workspace--datastore-name--container-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false--blob-cache-timeout-none--grant-workspace-access-false--subscription-id-none--resource-group-none-)

다음 코드는 데이터스토어를 `blob_datastore_name` `ws` 만들고 작업 영역에 등록합니다. 이 데이터 스토어는 `my-container-name` 제공된 계정 `my-account-name` 키를 사용하여 저장소 계정의 Blob 컨테이너에 액세스합니다.

```Python
blob_datastore_name='azblobsdk' # Name of the datastore to workspace
container_name=os.getenv("BLOB_CONTAINER", "<my-container-name>") # Name of Azure blob container
account_name=os.getenv("BLOB_ACCOUNTNAME", "<my-account-name>") # Storage account name
account_key=os.getenv("BLOB_ACCOUNT_KEY", "<my-account-key>") # Storage account key

blob_datastore = Datastore.register_azure_blob_container(workspace=ws, 
                                                         datastore_name=blob_datastore_name, 
                                                         container_name=container_name, 
                                                         account_name=account_name,
                                                         account_key=account_key)
```

#### <a name="file-share"></a>파일 공유

Azure 파일 공유를 데이터 스토어로 [`register_azure_file_share()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-)등록하려면 을 사용합니다. 

다음 코드는 데이터스토어를 `file_datastore_name` `ws` 만들고 작업 영역에 등록합니다. 이 데이터 스토어는 `my-fileshare-name` 제공된 계정 `my-account-name` 키를 사용하여 저장소 계정의 파일 공유에 액세스합니다.

```Python
file_datastore_name='azfilesharesdk' # Name of the datastore to workspace
file_share_name=os.getenv("FILE_SHARE_CONTAINER", "<my-fileshare-name>") # Name of Azure file share container
account_name=os.getenv("FILE_SHARE_ACCOUNTNAME", "<my-account-name>") # Storage account name
account_key=os.getenv("FILE_SHARE_ACCOUNT_KEY", "<my-account-key>") # Storage account key

file_datastore = Datastore.register_azure_file_share(workspace=ws,
                                                     datastore_name=file_datastore_name, 
                                                     file_share_name=file_share_name, 
                                                     account_name=account_name,
                                                     account_key=account_key)
```

#### <a name="azure-data-lake-storage-generation-2"></a>Azure 데이터 레이크 스토리지 생성 2

Azure Data Lake Storage Generation 2(ADLS Gen 2) 데이터 스토어의 경우 [register_azure_data_lake_gen2()를](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore.datastore?view=azure-ml-py#register-azure-data-lake-gen2-workspace--datastore-name--filesystem--account-name--tenant-id--client-id--client-secret--resource-url-none--authority-url-none--protocol-none--endpoint-none--overwrite-false-) 사용하여 Azure DataLake Gen 2 저장소에 연결된 자격 증명 데이터 스토어를 [서비스 주 체로 등록합니다.](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) 서비스 주체를 활용하려면 [응용 프로그램을 등록하고](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals) 서비스 주체에게 올바른 데이터 액세스 권한을 부여해야 합니다. [ADLS Gen 2에 대한 액세스 제어 설정에](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)대해 자세히 알아보십시오. 

다음 코드는 데이터스토어를 `adlsgen2_datastore_name` `ws` 만들고 작업 영역에 등록합니다. 이 데이터 스토어는 제공된 `test` 서비스 `account_name` 주체 자격 증명을 사용하여 저장소 계정의 파일 시스템에 액세스합니다.

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

Azure 기계 학습 스튜디오에서 몇 단계로 새 데이터 스토어를 만듭니다.

> [!IMPORTANT]
> 저장소 계정이 가상 네트워크에 있는 경우 [SDK를 통한](#python-sdk) 데이터스토어 생성만 지원됩니다. 

1. [Azure Machine Learning Studio](https://ml.azure.com/)에 로그인합니다.
1. 왼쪽 창에서 데이터 **저장소를** **선택합니다.**
1. **+ 새 데이터 스토어를**선택합니다.
1. 새 데이터 스토어에 대한 양식을 작성합니다. 양식은 Azure 저장소 유형 및 인증 유형에 대한 선택에 따라 지능적으로 업데이트됩니다.
  
[Azure 포털](https://portal.azure.com)에서 양식을 채우는 데 필요한 정보를 찾을 수 있습니다. 왼쪽 창에서 **저장소 계정을** 선택하고 등록할 저장소 계정을 선택합니다. **개요** 페이지에서는 계정 이름, 컨테이너 및 파일 공유 이름과 같은 정보를 제공합니다. 

* 계정 키 또는 SAS 토큰과 같은 인증 항목의 경우 **설정** 창에서 **계정 키로** 이동합니다. 

* 테넌트 ID 및 클라이언트 ID와 같은 서비스 주체 항목의 경우 **앱 등록으로** 이동하여 사용할 앱을 선택합니다. 해당 **개요** 페이지에는 이러한 항목이 포함됩니다. 

다음 예제에서는 Azure Blob 데이터 스토어를 만들 때 양식의 모양을 보여 줍니다. 
    
![새 데이터 스토어에 대한 양식](media/how-to-access-data/new-datastore-form.png)


<a name="get"></a>

## <a name="get-datastores-from-your-workspace"></a>작업 공간에서 데이터 스토어 받기

현재 작업 영역에 등록된 특정 데이터 스토어를 [`get()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#get-workspace--datastore-name-) 얻으려면 클래스에서 `Datastore` 정적 메서드를 사용합니다.

```Python
# Get a named datastore from the current workspace
datastore = Datastore.get(ws, datastore_name='your datastore name')
```
지정된 작업 영역에 등록된 데이터 스토어 목록을 얻으려면 작업 [`datastores`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace%28class%29?view=azure-ml-py#datastores) 영역 개체에서 속성을 사용할 수 있습니다.

```Python
# List all datastores registered in the current workspace
datastores = ws.datastores
for name, datastore in datastores.items():
    print(name, datastore.datastore_type)
```

작업 영역의 기본 데이터 스토어를 얻으려면 다음 줄을 사용합니다.

```Python
datastore = ws.get_default_datastore()
```

<a name="up-and-down"></a>
## <a name="upload-and-download-data"></a>데이터 업로드 및 다운로드

다음 [`upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#upload-src-dir--target-path-none--overwrite-false--show-progress-true-) [`download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-) 예제에서 설명하는 메서드와 메서드는 [AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py) 및 [AzureFileDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py) 클래스에 대해 동일하게 작동하며 동일하게 작동합니다.

### <a name="upload"></a>업로드

Python SDK를 사용하여 디렉토리 또는 개별 파일을 데이터스토어에 업로드합니다.

```Python
datastore.upload(src_dir='your source directory',
                 target_path='your target path',
                 overwrite=True,
                 show_progress=True)
```

매개 `target_path` 변수는 업로드할 파일 공유(또는 Blob 컨테이너)의 위치를 지정합니다. 기본적으로 는 `None`의 값이므로 데이터가 루트에 업로드됩니다. 의 `overwrite=True`기존 데이터가 `target_path` 덮어씁을 작성하는 경우.

메서드를 통해 개별 파일 목록을 데이터스토어에 `upload_files()` 업로드할 수도 있습니다.

### <a name="download"></a>다운로드

데이터스토어에서 로컬 파일 시스템으로 데이터 다운로드:

```Python
datastore.download(target_path='your target path',
                   prefix='your prefix',
                   show_progress=True)
```

매개 `target_path` 변수는 데이터를 다운로드할 로컬 디렉터리 위치입니다. 다운로드할 파일 공유 또는 blob 컨테이너의 폴더 경로를 지정하려면 `prefix`에 해당 경로를 입력합니다. 이 `prefix` `None`경우 파일 공유(또는 Blob 컨테이너)의 모든 내용이 다운로드됩니다.

<a name="train"></a>

## <a name="access-your-data-during-training"></a>교육 중 데이터 액세스

데이터 스토어의 데이터와 상호 작용하거나 교육과 같은 기계 학습 작업을 위한 소모품 개체에 데이터를 패키징하려면 [Azure Machine Learning 데이터 집합을 만듭니다.](how-to-create-register-datasets.md) 데이터 집합은 표 형식 데이터를 팬더 또는 스파크 DataFrame에 로드하는 기능을 제공합니다. 데이터 집합은 또한 Azure Blob 저장소, Azure 파일, Azure Data Lake 저장소 Gen1, Azure Data Lake 저장소 Gen2, Azure SQL 데이터베이스 및 PostgreSQL용 Azure 데이터베이스에서 모든 형식의 파일을 다운로드하거나 마운트하는 기능을 제공합니다. [데이터 집합으로 학습하는 방법에 대해 자세히 알아봅니다.](how-to-train-with-datasets.md)

### <a name="accessing-source-code-during-training"></a>교육 중 소스 코드 액세스

Azure Blob 저장소는 Azure 파일 공유보다 처리 속도가 더 높으며 병렬로 시작된 많은 작업으로 확장됩니다. 따라서 소스 코드 파일을 전송하기 위해 Blob 저장소를 사용하도록 실행을 구성하는 것이 좋습니다.

다음 코드 예제에서는 소스 코드 전송에 사용할 Blob 데이터 스토어를 실행 구성에서 지정합니다.

```python 
# workspaceblobstore is the default blob storage
run_config.source_directory_data_store = "workspaceblobstore" 
```

## <a name="access-data-during-scoring"></a>채점 하는 동안 데이터에 액세스

Azure 기계 학습은 점수를 매기기 위해 모델을 사용하는 여러 가지 방법을 제공합니다. 이러한 방법 중 일부는 데이터 스토어에 대한 액세스를 제공하지 않습니다. 다음 표를 사용하여 채점 하는 동안 데이터 스토어에 액세스할 수 있는 메서드를 이해합니다.

| 방법 | 데이터스토어 액세스 | 설명 |
| ----- | :-----: | ----- |
| [일괄 처리 예측](how-to-use-parallel-run-step.md) | ✔ | 많은 양의 데이터에 대해 비동기적으로 예측합니다. |
| [웹 서비스](how-to-deploy-and-where.md) | &nbsp; | 모델을 웹 서비스로 배포합니다. |
| [Azure IoT 에지 모듈](how-to-deploy-and-where.md) | &nbsp; | IoT Edge 장치에 모델을 배포합니다. |

SDK가 데이터 스토어에 대한 액세스를 제공하지 않는 경우 관련 Azure SDK를 사용하여 데이터에 액세스하여 사용자 지정 코드를 만들 수 있습니다. 예를 들어 [파이썬용 Azure 저장소 SDK는](https://github.com/Azure/azure-storage-python) Blob 또는 파일에 저장된 데이터에 액세스하는 데 사용할 수 있는 클라이언트 라이브러리입니다.

<a name="move"></a>

## <a name="move-data-to-supported-azure-storage-solutions"></a>지원되는 Azure 저장소 솔루션으로 데이터 이동

Azure 기계 학습은 Azure Blob 저장소, Azure 파일, Azure 데이터 레이크 저장소 Gen1, Azure 데이터 레이크 저장소 Gen2, Azure SQL 데이터베이스 및 PostgreSQL용 Azure 데이터베이스의 데이터에 액세스하는 것을 지원합니다. 지원되지 않는 저장소를 사용하는 경우 [Azure Data Factory 및 다음 단계를](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-copy-data-tool)사용하여 데이터를 지원되는 Azure 저장소 솔루션으로 이동하는 것이 좋습니다. 데이터를 지원되는 저장소로 이동하면 기계 학습 실험 중에 데이터 송신 비용을 절감할 수 있습니다. 

Azure Data Factory는 추가 비용 없이 80개 이상의 사전 빌드된 커넥터를 통해 효율적이고 복원력이 뛰어난 데이터 전송을 제공합니다. 이러한 커넥터에는 Azure 데이터 서비스, 온-프레미스 데이터 원본, Amazon S3 및 Redshift 및 Google BigQuery가 포함됩니다.

## <a name="next-steps"></a>다음 단계

* [Azure 기계 학습 데이터 집합 만들기](how-to-create-register-datasets.md)
* [모델 학습](how-to-train-ml-models.md)
* [모델 배포](how-to-deploy-and-where.md)
