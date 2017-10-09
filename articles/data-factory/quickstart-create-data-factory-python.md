---
title: "Python을 사용하여 Azure Data Factory 만들기 | Microsoft Docs"
description: "Azure Data Factory를 만들어서 Azure Blob Storage의 한 위치에서 동일한 Blob Storage의 다른 위치로 데이터를 복사합니다."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: 
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 09/19/2017
ms.author: jingwang
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 9c6f7c8e052aed2ab5aba2ee54065bf40e81a022
ms.contentlocale: ko-kr
ms.lasthandoff: 09/25/2017

---

# <a name="create-a-data-factory-and-pipeline-using-python"></a>Python을 사용하여 데이터 팩터리 및 파이프라인 만들기
Azure Data Factory는 데이터 이동 및 데이터 변환을 오케스트레이션하고 자동화하기 위해 클라우드에서 데이터 기반 워크플로를 만들 수 있는 클라우드 기반 데이터 통합 서비스입니다. Azure Data Factory를 사용하여 서로 다른 데이터 저장소에서 데이터를 수집할 수 있는 데이터 기반 워크플로(파이프라인이라고 함)를 만들고 일정을 조정하며, Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics 및 Azure Machine Learning과 같은 계산 서비스를 사용하여 데이터를 처리/변환하고, 사용할 BI(비즈니스 인텔리전스) 응용 프로그램의 Azure SQL Data Warehouse와 같은 데이터 저장소에 출력 데이터를 게시할 수 있습니다. 

이 빠른 시작에서는 Python을 사용하여 Azure Data Factory를 만드는 방법을 설명합니다. 이 데이터 팩터리의 파이프라인은 Azure Blob Storage의 한 폴더에서 다른 폴더로 데이터를 복사합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>필수 조건

* **Azure Storage 계정**. Blob Storage를 **원본** 및 **싱크** 데이터 저장소 모두로 사용합니다. Azure Storage 계정이 없는 경우 새로 만드는 단계는 [저장소 계정 만들기](../storage/common/storage-create-storage-account.md#create-a-storage-account) 문서를 참조하세요. 
* [이러한 지침](../azure-resource-manager/resource-group-create-service-principal-portal.md#create-an-azure-active-directory-application)에 따라 **Azure Active Directory에 응용 프로그램을 만듭니다**. 나중에 나오는 단계에서 사용하는 다음 값을 기록해 둡니다. **응용 프로그램 ID**, **인증 키** 및 **테넌트 ID**. 동일한 문서의 지침에 따라 응용 프로그램을 “**참가자**” 역할에 할당합니다. 

### <a name="create-and-upload-an-input-file"></a>입력 파일 만들기 및 업로드

1. 메모장을 시작합니다. 다음 텍스트를 복사하고 **input.txt** 파일로 디스크에 저장합니다.
    
    ```
    John|Doe
    Jane|Doe
    ```
2.  [Azure Storage 탐색기](http://storageexplorer.com/)와 같은 도구를 사용하여 **adfv2tutorial** 컨테이너를 만들고 input.txt 파일을 컨테이너에 업로드합니다. 

## <a name="install-the-python-package"></a>Python 패키지 설치
1. 관리자 권한으로 터미널 또는 명령 프롬프트를 엽니다.  
2. 데이터 팩터리를 위한 Python 패키지를 설치하려면 다음 명령을 실행합니다.

    ```
    pip install azure-mgmt-datafactory
    ```

    [데이터 팩터리를 위한 Python SDK](https://github.com/Azure/azure-sdk-for-python)는 Python 2.7, 3.3, 3.4, 3.5 및 3.6을 지원합니다.
## <a name="create-a-data-factory-client"></a>데이터 팩터리 클라이언트 만들기

1. **datafactory.py**라는 파일을 만듭니다. 다음 문을 추가하여 네임스페이스에 대한 참조를 추가합니다.
    
    ```python
    from azure.common.credentials import ServicePrincipalCredentials
    from msrestazure.azure_cloud import Cloud, CloudEndpoints, CloudSuffixes
    from azure.mgmt.resource import ResourceManagementClient
    from azure.mgmt.datafactory import DataFactoryManagementClient
    from azure.mgmt.datafactory.models import *
    import json
    import time    
    ```
2. 정보를 출력하는 다음 함수를 추가합니다. 

    ```python
    def print_item(group):
      """Print an Azure object instance."""
      print("\tName: {}".format(group.name))
      print("\tId: {}".format(group.id))
      if hasattr(group, 'location'):
          print("\tLocation: {}".format(group.location))
      if hasattr(group, 'tags'):
          print("\tTags: {}".format(group.tags))
      if hasattr(group, 'properties'):
          print_properties(group.properties)
    
    def print_properties(props):
      """Print a ResourceGroup properties instance."""
      if props and hasattr(props, 'provisioning_state') and props.provisioning_state:
          print("\tProperties:")
          print("\t\tProvisioning State: {}".format(props.provisioning_state))
      print("\n\n")    
    ```
3. DataFactoryManagementClient 클래스의 인스턴스를 만드는 **Main** 메서드에 다음 코드를 추가합니다. 이 개체를 사용하여 데이터 팩터리, 연결된 서비스, 데이터 집합 및 파이프라인을 만듭니다. 또한 이 개체를 사용하여 파이프라인 실행 세부 정보를 모니터링합니다.

    ```python   
    subscription_id = '<your subscription ID where the factory resides>'
    credentials = ServicePrincipalCredentials(
            client_id='<yourClientId>',
            secret='<YourPassword>',
            tenant='<YourTenandId>'
    )
    resource_client = ResourceManagementClient(credentials, subscription_id)
    adf_client = DataFactoryManagementClient(credentials, subscription_id)
    
    rg_params = {'location':'eastus'}
    df_params = {'location':'eastus'}
    rg_name = '<Your Resource Group Name>'
    df_name = '<Your Data Factory Name>'
    ```

## <a name="create-a-data-factory"></a>데이터 팩터리를 만듭니다.

**데이터 팩터리**를 만드는 **Main** 메서드에 다음 코드를 추가합니다. 

```python
#Create Resource Group
resource_client.resource_groups.create_or_update(rg_name, rg_params)

#Create Data Factory
df_resource = Factory(location='eastus')
df = adf_client.factories.create_or_update(rg_name, df_name, df_resource)
print_item(df)
while df.provisioning_state != 'Succeeded':
    df = adf_client.factories.get(rg_name, df_name)
    time.sleep(1)
print_item(adf_client.factories.get(rg_name, df_name))
```

## <a name="create-a-linked-service"></a>연결된 서비스 만들기

**Azure Storage 연결된 서비스**를 만드는 **Main** 메서드에 다음 코드를 추가합니다.

데이터 팩터리에서 연결된 서비스를 만들어 데이터 저장소를 연결하고 계산 서비스를 데이터 팩터리에 연결합니다. 이 빠른 시작에서는 복사 원본 및 싱크 저장소 모두에 대해 샘플의 “AzureStorageLinkedService”라는 하나의 Azure Storage 연결된 서비스를 만들기만 하면 됩니다.

```python
#Create Storage Linked Service
ls_name = 'storageLinkedService'

#Replace Storage String with your credentials
storage_string = SecureString('DefaultEndpointsProtocol=https;AccountName=<replace>;AccountKey=<replace>')

ls_azure_storage = AzureStorageLinkedService(connection_string=storage_string)
ls = adf_client.linked_services.create_or_update(rg_name, df_name, ls_name, ls_azure_storage)
print_item(ls)
```
## <a name="create-datasets"></a>데이터 집합 만들기
이 섹션에서는 원본과 싱크에 각각 하나씩, 두 개의 데이터 집합을 만듭니다.

### <a name="create-a-dataset-for-source-azure-blob"></a>원본 Azure Blob에 대한 데이터 집합 만들기
Azure Blob 데이터 집합을 만드는 Main 메서드에 다음 코드를 추가합니다. Azure Blob 데이터 집합의 속성에 대한 자세한 내용은 [Azure Blob 커넥터](connector-azure-blob-storage.md#dataset-properties) 문서를 참조하세요. 

Azure Blob에 원본 데이터를 나타내는 데이터 집합을 정의합니다. 이 Blob 데이터 집합은 이전 단계에서 만든 Azure Storage 연결된 서비스를 참조합니다.

```python
#Create Dataset Input
ds_name = 'ds_in'
ds_ls = LinkedServiceReference(ls_name)
blob_path= 'adfv2tutorial/'
blob_filename = 'input.txt'
ds_azure_blob= AzureBlobDataset(ds_ls, folder_path=blob_path, file_name = blob_filename)
ds = adf_client.datasets.create_or_update(rg_name, df_name, ds_name, ds_azure_blob)
print_item(ds)
```

### <a name="create-a-dataset-for-sink-azure-blob"></a>싱크 Azure Blob에 대한 데이터 집합 만들기
Azure Blob 데이터 집합을 만드는 Main 메서드에 다음 코드를 추가합니다. Azure Blob 데이터 집합의 속성에 대한 자세한 내용은 [Azure Blob 커넥터](connector-azure-blob-storage.md#dataset-properties) 문서를 참조하세요. 

Azure Blob에 원본 데이터를 나타내는 데이터 집합을 정의합니다. 이 Blob 데이터 집합은 이전 단계에서 만든 Azure Storage 연결된 서비스를 참조합니다.

```python
#Create Dataset Output
dsOut_name = 'ds_out'
output_blobpath = 'output/'
dsOut_azure_blob = AzureBlobDataset(ds_ls, folder_path=output_blobpath)
dsOut = adf_client.datasets.create_or_update(rg_name, df_name, dsOut_name, dsOut_azure_blob)
print_item(dsOut)
```

## <a name="create-a-pipeline"></a>파이프라인을 만듭니다.

**복사 활동으로 파이프라인**을 만드는 **Main** 메서드에 다음 코드를 추가합니다.

```python
#Create 1st activity: Copy Activity
act_name =  'copyBlobtoBlob'
blob_source = BlobSource()
blob_sink = BlobSink()
dsin_ref = DatasetReference(ds_name)
dsOut_ref = DatasetReference(dsOut_name)
copy_activity = CopyActivity(act_name,inputs=[dsin_ref], outputs=[dsOut_ref], source=blob_source, sink=blob_sink)

#Create Pipeline
p_name =  'copyPipeline'
params_for_pipeline = {}
p_obj = PipelineResource(activities=[copy_activity], parameters=params_for_pipeline)
p = adf_client.pipelines.create_or_update(rg_name, df_name, p_name, p_obj)
print_item(p)
```


## <a name="create-a-pipeline-run"></a>파이프라인 실행 만들기

**파이프라인 실행을 트리거하는** **Main** 메서드에 다음 코드를 추가합니다.

```python
#Create Pipeline Run
print(adf_client.pipelines.create_run(rg_name, df_name, p_name,
    {
    }
))
```

## <a name="run-the-code"></a>코드 실행
응용 프로그램을 빌드하고 시작한 다음, 파이프라인 실행을 확인합니다.

콘솔에서는 데이터 팩터리, 연결된 서비스, 데이터 집합, 파이프라인 및 파이프라인 실행 만들기에 대한 진행률을 출력합니다. 데이터 읽기/쓰기 크기와 함께 복사 활동 실행 세부 정보가 표시될 때까지 기다립니다. 그런 다음 [Azure Storage 탐색기](https://azure.microsoft.com/features/storage-explorer/)와 같은 도구를 사용하여 Blob이 변수에 지정한 대로 “inputBlobPath”에서 “outputBlobPath”로 복사되었는지 검사합니다.


## <a name="clean-up-resources"></a>리소스 정리
데이터 팩터리를 프로그래밍 방식으로 삭제하려면 프로그램에 다음 코드 줄을 추가합니다. 

```csharp
adf_client.data_factories.delete(rg_name, df_name)
```

## <a name="next-steps"></a>다음 단계
이 샘플의 파이프라인은 Azure Blob Storage의 한 위치에서 다른 위치로 데이터를 복사했습니다. [자습서](tutorial-copy-data-dot-net.md)를 통해 더 많은 시나리오에서의 데이터 팩터리 사용에 관해 알아보세요. 
