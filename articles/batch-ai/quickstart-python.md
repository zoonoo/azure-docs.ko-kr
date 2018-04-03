---
title: Azure 빠른 시작 - Batch AI로 CNTK 교육 - Python | Microsoft Docs
description: Python SDK를 사용하여 Batch AI로 CNTK 교육 작업을 실행하는 방법을 신속하게 알아봅니다
services: batch-ai
documentationcenter: na
author: lliimsft
manager: Vaman.Bedekar
editor: tysonn
ms.assetid: ''
ms.custom: ''
ms.service: batch-ai
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: Python
ms.topic: quickstart
ms.date: 10/06/2017
ms.author: lili
ms.openlocfilehash: f535c9adf4926f29ae9cade6382debedab73937d
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="run-a-cntk-training-job-using-the-azure-python-sdk"></a>Azure Python SDK를 사용하여 CNTK 교육 작업 실행

이 빠른 시작에서는 Azure Python SDK를 사용하여 Batch AI 서비스를 통해 Microsoft CNTK(Cognitive Toolkit) 교육 작업을 실행하는 방법을 자세히 알아봅니다. 

이 예제에서는 손으로 그린 이미지의 MNIST 데이터베이스를 사용하여 단일 노드 GPU 클러스터에서 나선형 신경망을 교육합니다. 

## <a name="prerequisites"></a>필수 조건

* Azure 구독 - Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다. 

* Azure Python SDK - [설치 지침](/python/azure/python-sdk-azure-install) 참조

* Azure 저장소 계정 - [Azure 저장소 계정을 만드는 방법](../storage/common/storage-create-storage-account.md) 참조

* Azure Active Directory 서비스 사용자 자격 증명 - [CLI를 사용하여 서비스 사용자를 만드는 방법](../azure-resource-manager/resource-group-authenticate-service-principal-cli.md) 참조

* Azure Cloud Shell 또는 Azure CLI를 사용하여 Batch AI 리소스 공급자를 구독에 한 번 등록해야 합니다. 공급자 등록에 최대 15분이 걸릴 수 있습니다.

  ```azurecli
  az provider register -n Microsoft.BatchAI
  az provider register -n Microsoft.Batch
  ```


## <a name="configure-credentials"></a>자격 증명 구성
Python 스크립트에서 다음 매개 변수를 만들고, 사용자 고유의 값으로 바꿉니다.

```Python
# credentials used for authentication
client_id = 'my_aad_client_id'
secret = 'my_aad_secret_key'
token_uri = 'my_aad_token_uri'
subscription_id = 'my_subscription_id'

# credentials used for storage
storage_account_name = 'my_storage_account_name'
storage_account_key = 'my_storage_account_key'

# specify the credentials used to remote login your GPU node
admin_user_name = 'my_admin_user_name'
admin_user_password = 'my_admin_user_password'
```

모든 자격 증명을 이 예제에 표시되지 않는 별도의 구성 파일에 저장하는 것이 가장 좋습니다. 구성 파일을 구현하려면 [레시피](https://github.com/Azure/BatchAI/tree/master/recipes)를 참조하세요. 

## <a name="authenticate-with-batch-ai"></a>Batch AI 인증

Azure Batch AI에 액세스하려면 Azure Active Directory를 사용하여 인증해야 합니다. 아래는 서비스 사용자 자격 증명 및 구독 ID를 사용하여 서비스를 인증(`BatchAIManagementClient` 개체 만들기)하는 코드입니다.

```Python
from azure.common.credentials import ServicePrincipalCredentials
import azure.mgmt.batchai as batchai
import azure.mgmt.batchai.models as models

creds = ServicePrincipalCredentials(
        client_id=client_id, secret=secret, token_uri=token_uri)

batchai_client = batchai.BatchAIManagementClient(credentials=creds,
                                         subscription_id=subscription_id
)
```

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

Batch AI 및 작업은 Azure 리소스이므로 Azure 리소스 그룹에 배치해야 합니다. 다음 코드 조각은 리소스 그룹을 만듭니다.

```Python
from azure.mgmt.resource import ResourceManagementClient

resource_group_name = 'myresourcegroup'

resource_management_client = ResourceManagementClient(
        credentials=creds, subscription_id=subscription_id)

resource = resource_management_client.resource_groups.create_or_update(
        resource_group_name, {'location': 'eastus'})
```


## <a name="prepare-azure-file-share"></a>Azure 파일 공유 준비
이해를 돕기 위해 이 빠른 시작에서는 Azure 파일 공유를 사용하여 학습 작업에 대한 교육 데이터 및 스크립트를 호스트합니다. 

1. *batchaiquickstart*라는 파일 공유를 만듭니다.

  ```Python
  from azure.storage.file import FileService 
 
  azure_file_share_name = 'batchaiquickstart' 
 
  service = FileService(storage_account_name, storage_account_key) 
 
  service.create_share(azure_file_share_name, fail_on_exist=False)
  ``` 
 
2. *mnistcntksample*이라는 공유에 디렉터리를 만듭니다. 

  ```Python
  mnist_dataset_directory = 'mnistcntksample' 
 
  service.create_directory(azure_file_share_name, mnist_dataset_directory, fail_on_exist=False) 
  ```
3. [샘플 패키지](https://batchaisamples.blob.core.windows.net/samples/BatchAIQuickStart.zip?st=2017-09-29T18%3A29%3A00Z&se=2099-12-31T08%3A00%3A00Z&sp=rl&sv=2016-05-31&sr=b&sig=hrAZfbZC%2BQ%2FKccFQZ7OC4b%2FXSzCF5Myi4Cj%2BW3sVZDo%3D)를 다운로드하여 압축을 풉니다. Python 스크립트를 실행할 디렉터리에 콘텐츠를 업로드합니다.

  ```Python
  for f in ['Train-28x28_cntk_text.txt', 'Test-28x28_cntk_text.txt', 'ConvNet_MNIST.py']:     
     service.create_file_from_path(
             azure_file_share_name, mnist_dataset_directory, f, f) 
  ```

## <a name="create-gpu-cluster"></a>GPU 클러스터 만들기

Batch AI 클러스터를 만듭니다. 이 예제에서는 클러스터가 단일 STANDARD_NC6 VM 노드로 구성됩니다. 이 VM의 크기는 NVIDIA K80 GPU 하나입니다. *azurefileshare* 폴더에 파일 공유를 탑재합니다. GPU 계산 노드에서 이 폴더의 전체 경로는 $AZ_BATCHAI_MOUNT_ROOT/azurefileshare입니다.

```Python
cluster_name = 'mycluster'

relative_mount_point = 'azurefileshare' 
 
parameters = models.ClusterCreateParameters(
    # Location where the cluster will physically be deployed
    location='eastus', 
 
    # VM size. Use NC or NV series for GPU
    vm_size='STANDARD_NC6', 
 
    # Configure the ssh users
    user_account_settings=models.UserAccountSettings(
         admin_user_name=admin_user_name,
         admin_user_password=admin_user_password), 
 
    # Number of VMs in the cluster
    scale_settings=models.ScaleSettings(
         manual=models.ManualScaleSettings(target_node_count=1)
     ), 
 
    # Configure each node in the cluster
    node_setup=models.NodeSetup( 
 
        # Mount shared volumes to the host
         mount_volumes=models.MountVolumes(
             azure_file_shares=[
                 models.AzureFileShareReference(
                     account_name=storage_account_name,
                     credentials=models.AzureStorageCredentialsInfo(
         account_key=storage_account_key),
         azure_file_url='https://{0}.file.core.windows.net/{1}'.format(
               storage_account_name, azure_file_share_name),
                  relative_mount_path = relative_mount_point)],
         ), 
    ), 
) 
batchai_client.clusters.create(resource_group_name, cluster_name, parameters).result() 
```

## <a name="get-cluster-status"></a>클러스터 상태 가져오기

다음 명령을 사용하여 클러스터 상태를 모니터링합니다. 

```Python
cluster = batchai_client.clusters.get(resource_group_name, cluster_name)
print('Cluster state: {0} Target: {1}; Allocated: {2}; Idle: {3}; '
      'Unusable: {4}; Running: {5}; Preparing: {6}; leaving: {7}'.format(
    cluster.allocation_state,
    cluster.scale_settings.manual.target_node_count,
    cluster.current_node_count,
    cluster.node_state_counts.idle_node_count,
    cluster.node_state_counts.unusable_node_count,
    cluster.node_state_counts.running_node_count,
    cluster.node_state_counts.preparing_node_count,
    cluster.node_state_counts.leaving_node_count)) 
```

이전 코드는 다음과 같은 기본 클러스터 할당 정보를 인쇄합니다.

```Shell
Cluster state: AllocationState.steady Target: 1; Allocated: 1; Idle: 0; Unusable: 0; Running: 0; Preparing: 1; Leaving 0
 
```  

노드가 할당되고 준비가 완료되면 클러스터를 사용할 수 있습니다(`nodeStateCounts` 특성 참조). 잘못된 부분이 있으면 `errors` 특성에 오류 설명이 포함됩니다.

## <a name="create-training-job"></a>교육 작업 만들기

클러스터가 준비된 후에는 학습 작업을 구성하고 제출합니다. 

```Python
job_name = 'myjob' 
 
parameters = models.job_create_parameters.JobCreateParameters( 
 
     # Location where the job will run
     # Ideally this should be co-located with the cluster.
     location='eastus', 
 
     # The cluster this job will run on
     cluster=models.ResourceId(cluster.id), 
 
     # The number of VMs in the cluster to use
     node_count=1, 
 
     # Override the path where the std out and std err files will be written to.
     # In this case we will write these out to an Azure Files share
     std_out_err_path_prefix='$AZ_BATCHAI_MOUNT_ROOT/{0}'.format(relative_mount_point), 
 
     input_directories=[models.InputDirectory(
         id='SAMPLE',
         path='$AZ_BATCHAI_MOUNT_ROOT/{0}/{1}'.format(relative_mount_point, mnist_dataset_directory))], 
 
     # Specify directories where files will get written to 
     output_directories=[models.OutputDirectory(
        id='MODEL',
        path_prefix='$AZ_BATCHAI_MOUNT_ROOT/{0}'.format(relative_mount_point),
        path_suffix="Models")], 
 
     # Container configuration
     container_settings=models.ContainerSettings(
        models.ImageSourceRegistry(image='microsoft/cntk:2.1-gpu-python3.5-cuda8.0-cudnn6.0')), 
 
     # Toolkit specific settings
     cntk_settings = models.CNTKsettings(
        python_script_file_path='$AZ_BATCHAI_INPUT_SAMPLE/ConvNet_MNIST.py',
        command_line_args='$AZ_BATCHAI_INPUT_SAMPLE $AZ_BATCHAI_OUTPUT_MODEL')
 ) 
 
# Create the job 
batchai_client.jobs.create(resource_group_name, job_name, parameters).result() 
```

## <a name="monitor-job"></a>작업 모니터링
다음 명령을 사용하여 작업 상태를 검사할 수 있습니다. 
 
```Python
job = batchai_client.jobs.get(resource_group_name, job_name) 
 
print('Job state: {0} '.format(job.execution_state.name))
```

출력은 `Job state: running`과 비슷합니다.

`executionState`에는 작업의 현재 실행 상태가 포함되어 있습니다.
* `queued`: 클러스터 노드를 사용할 수 있을 때까지 작업이 대기 중
* `running`: 작업이 실행 중
* `succeeded`(또는 `failed`): 작업이 완료되었으며 `executionInfo`에 결과에 대한 세부 정보가 포함되어 있음
 
## <a name="list-stdout-and-stderr-output"></a>stdout 및 stderr 출력 나열
stdout 및 stderr 로그 파일의 링크를 나열하려면 다음 명령을 사용합니다.

```Python
files = batchai_client.jobs.list_output_files(resource_group_name, job_name, models.JobsListOutputFilesOptions("stdouterr")) 
 
for file in list(files):
     print('file: {0}, download url: {1}'.format(file.name, file.download_url)) 
```
## <a name="delete-resources"></a>리소스 삭제

다음 명령을 사용하여 작업을 삭제합니다.
```Python
batchai_client.jobs.delete(resource_group_name, job_name) 
```

다음 명령을 사용하여 클러스터를 삭제합니다.
```Python
batchai_client.clusters.delete(resource_group_name, cluster_name)
```
## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Azure Python SDK를 사용하여 Batch AI 클러스터에서 CNTK 교육 작업을 실행하는 방법을 배웠습니다. 여러 도구에서 Batch AI를 사용하는 자세한 방법은 [교육 레시피](https://github.com/Azure/BatchAI)를 참조하세요.
